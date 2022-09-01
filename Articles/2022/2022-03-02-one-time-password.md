| Property     | Content                                                      |
| ------------ | ------------------------------------------------------------ |
| Id           | 0ca252d4-b423-43cb-b5dc-c4959b908be1                         |
| Name         | 2022-03-02-one-time-password.md                              |
| ArticleLink  | https://blog.spiritling.cn/articles/0ca252d4-b423-43cb-b5dc-c4959b908be1 |
| Github Issue |                                                              |
| Gitee Issue  |                                                              |

# 二次验证中的一次性密码

**基于时间的一次性密码算法**（英语：Time-based One-Time Password，简称：**TOTP**）是一种根据**预共享**的密钥与当前时间计算一次性密码的算法。它已被互联网工程任务组接纳为 [RFC 6238 - TOTP: Time-Based One-Time Password Algorithm (ietf.org)](https://datatracker.ietf.org/doc/html/rfc6238) 标准，成为主动开放认证（OATH）的基石，并被用于众多多重要素验证系统当中。

## one time password

一次性密码 (OTP) 是自动生成的数字或字母数字字符串，用于对单个事务或登录会话的用户进行身份验证。

实现是将一个随机值和一个时间戳合并后计算出来的散列值，而其值可以作为一次性密码。但是时间戳变化太快了，防止来不及输入或者网络延迟导致的问题。一般将时间戳从0到目前的值以30秒或者60秒分割，获取到当前时间所处的区间值，使用区间值去进行散列计算，形成的密码将最长具有30秒时间，完全有足够时间进行输入。

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Security.Cryptography;
using System.Text;
namespace SpiritLingSystem.Library.Tools.Authentication
{
    public static class OtpAuth
    {
        const int IntervalLength = 30;
        const int PinLength = 6;
        static readonly int PinModulo = (int) Math.Pow(10, PinLength);
        static readonly DateTime UnixEpoch = new DateTime(1970, 1, 1, 0, 0, 0, DateTimeKind.Utc);
        /// <summary>
        ///   Number of intervals that have elapsed.
        /// </summary>
        static long CurrentInterval
        {
            get
            {
                var ElapsedSeconds = (long) Math.Floor((DateTime.UtcNow - UnixEpoch).TotalSeconds);
                return ElapsedSeconds / IntervalLength;
            }
        }
        
        /// <summary>
        /// Generates a opt auth url
        /// </summary>
        /// <param name="identifier">用户标识一般邮箱手机号</param>
        /// <param name="key">唯一的key</param>
        /// <param name="issuer">表示站点等</param>
        /// <returns>otpauth://totp/{issuer}:{identifier}?secret=secret({key})&issuer={issuer}</returns>
        public static string GenerateOptAuth(string identifier, byte[] key, string issuer = "SpiritLing")
        {
            var KeyString = Encoder.Base32Encode(key);
            // Google: otpauth://totp/GitHub:xxxx?secret=isqkvxnmnlb3ce7v&issuer=GitHub2
            // Google 验证器 显示为 GitHub2(GitHub:xxxx)
            // Microsoft: otpauth://totp/GitHub:xxxx?secret=isqkvxnmnlb3ce7v&issuer=GitHub2
            // Microsoft显示为 GitHub(xxxx)
            // 不包含issuer 和前面一致
            var ProvisionUrl = $"otpauth://totp/{issuer}:{identifier}?secret={KeyString}&issuer={issuer}";
            // 通过生成google接口生产二维码
            //var ChartUrl = string.Format("https://chart.apis.google.com/chart?cht=qr&chs={0}x{1}&chl={2}", width, height, ProvisionUrl);
            //using (var Client = new WebClient())
            //{
            //    return Client.DownloadData(ChartUrl);
            //}
            return ProvisionUrl;
        }
        /// <summary>
        /// Generates a opt auth url
        /// </summary>
        /// <param name="identifier">用户标识一般邮箱手机号</param>
        /// <param name="key">唯一的key</param>
        /// <param name="issuer">表示站点等</param>
        /// <returns>otpauth://totp/{issuer}:{identifier}?secret=secret({key})&issuer={issuer}</returns>
        public static string GenerateOptAuth(string identifier, string key, string issuer = "SpiritLing")
        {
            return GenerateOptAuth(identifier, Encoding.UTF8.GetBytes(key), issuer);
        }
        /// <summary>
        ///   Generates a pin for the given key.
        /// </summary>
        public static string GeneratePin(byte[] key)
        {
            return GeneratePin(key, CurrentInterval);
        }
        public static string GeneratePin(string key)
        {
            return GeneratePin(Encoding.UTF8.GetBytes(key));
        }
        /// <summary>
        ///   Generates a pin by hashing a key and counter.
        /// </summary>
        static string GeneratePin(byte[] key, long counter)
        {
            const int SizeOfInt32 = 4;
            var CounterBytes = BitConverter.GetBytes(counter);
            if (BitConverter.IsLittleEndian)
            {
                //spec requires bytes in big-endian order
                Array.Reverse(CounterBytes);
            }
            var Hash = new HMACSHA1(key).ComputeHash(CounterBytes);
            var Offset = Hash[Hash.Length - 1] & 0xF;
            var SelectedBytes = new byte[SizeOfInt32];
            Buffer.BlockCopy(Hash, Offset, SelectedBytes, 0, SizeOfInt32);
            if (BitConverter.IsLittleEndian)
            {
                //spec interprets bytes in big-endian order
                Array.Reverse(SelectedBytes);
            }
            var SelectedInteger = BitConverter.ToInt32(SelectedBytes, 0);
            //remove the most significant bit for interoperability per spec
            var TruncatedHash = SelectedInteger & 0x7FFFFFFF;
            //generate number of digits for given pin length
            var Pin = TruncatedHash % PinModulo;
            return Pin.ToString(CultureInfo.InvariantCulture).PadLeft(PinLength, '0');
        }
        #region Nested type: Encoder
        static class Encoder
        {
            /// <summary>
            ///   Url Encoding (with upper-case hexadecimal per OATH specification)
            /// </summary>
            public static string UrlEncode(string value)
            {
                const string UrlEncodeAlphabet = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-_.~";
                var Builder = new StringBuilder();
                for (var i = 0; i < value.Length; i++)
                {
                    var Symbol = value[i];
                    if (UrlEncodeAlphabet.IndexOf(Symbol) != -1)
                    {
                        Builder.Append(Symbol);
                    }
                    else
                    {
                        Builder.Append('%');
                        Builder.Append(((int) Symbol).ToString("X2"));
                    }
                }
                return Builder.ToString();
            }
            /// <summary>
            ///   Base-32 Encoding
            /// </summary>
            public static string Base32Encode(byte[] data)
            {
                const int InByteSize = 8;
                const int OutByteSize = 5;
                const string Base32Alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ234567";
                int i = 0, index = 0;
                var Builder = new StringBuilder((data.Length + 7) * InByteSize / OutByteSize);
                while (i < data.Length)
                {
                    int CurrentByte = data[i];
                    int Digit;
                    //Is the current digit going to span a byte boundary?
                    if (index > (InByteSize - OutByteSize))
                    {
                        int NextByte;
                        if ((i + 1) < data.Length)
                        {
                            NextByte = data[i + 1];
                        }
                        else
                        {
                            NextByte = 0;
                        }
                        Digit = CurrentByte & (0xFF >> index);
                        index = (index + OutByteSize) % InByteSize;
                        Digit <<= index;
                        Digit |= NextByte >> (InByteSize - index);
                        i++;
                    }
                    else
                    {
                        Digit = (CurrentByte >> (InByteSize - (index + OutByteSize))) & 0x1F;
                        index = (index + OutByteSize) % InByteSize;
                        if (index == 0)
                        {
                            i++;
                        }
                    }
                    Builder.Append(Base32Alphabet[Digit]);
                }
                return Builder.ToString();
            }
        }
        #endregion
    }
}
```



