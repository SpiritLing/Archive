---
title: æ åéå
---

> ð å·¥æ¬²åå¶äºï¼å¿åå©å¶å¨ã

# æ åéå - Stack And Queue

## æ 

å¦ä½çè§£æ å¢ï¼

**åè¿èååºï¼åè¿èååºï¼è¿å°±æ¯å¸åç "æ " ç»æ**ã

![æ åéå-æ ç»æ](https://archive.static.spiritling.net/images/04_StackAndQueue-StackStructure.png)

ä»æ çæä½ç¹æ§ä¸æ¥çï¼**æ æ¯ä¸ç§âæä½åéâççº¿æ§è¡¨ï¼**åªåè®¸å¨ä¸ç«¯æå¥åå é¤æ°æ®ã

_12312312_

äºå®ä¸ï¼ä»åè½ä¸æ¥è¯´ï¼æ°ç»æèé¾è¡¨ç¡®å®é½å¯ä»¥æ¿ä»£æ ï¼ä½ä½ è¦ç¥éï¼ç¹å®çæ°æ®ç»ææ¯å¯¹ç¹å®åºæ¯çæ½è±¡ï¼èä¸ï¼æ°ç»æé¾è¡¨æ´æ¼äºå¤ªå¤çæä½æ¥å£ï¼æä½ä¸çç¡®çµæ´»è¿ç¨ï¼ä½åæ¶ä½¿ç¨æ¶å°±åçä¸å¯æ§ï¼èªç¶ä¹å°±æ´å®¹æåºéã

**å½æä¸ªæ°æ®éååªæ¶åå¨ä¸ç«¯æå¥åå é¤æ°æ®ï¼å¹¶ä¸æ»¡è¶³åè¿ååºãåè¿ååºçç¹æ§ï¼å°±åºè¯¥é¦éâæ âè¿ç§æ°æ®ç»æ**ã

### å®ç°æ 

æ¢ç¶æ°ç»æèé¾è¡¨å¯ä»¥æ¿ä»£æ ï¼é£ä¹åæ ·çï¼ä½¿ç¨æ°ç»åé¾è¡¨å¯ä»¥å®ç°æ ãç¨æ°ç»å®ç°çæ å«åé¡ºåºæ ï¼ç¨é¾è¡¨å®ç°çæ å«åé¾å¼æ ã

```java
// åºäºæ°ç»å®ç°çé¡ºåºæ 
public class ArrayStack {
  private String[] items;  // æ°ç»
  private int count;       // æ ä¸­åç´ ä¸ªæ°
  private int n;           //æ çå¤§å°

  // åå§åæ°ç»ï¼ç³è¯·ä¸ä¸ªå¤§å°ä¸ºnçæ°ç»ç©ºé´
  public ArrayStack(int n) {
    this.items = new String[n];
    this.n = n;
    this.count = 0;
  }

  // å¥æ æä½
  public boolean push(String item) {
    // æ°ç»ç©ºé´ä¸å¤äºï¼ç´æ¥è¿åfalseï¼å¥æ å¤±è´¥ã
    if (count == n) return false;
    // å°itemæ¾å°ä¸æ ä¸ºcountçä½ç½®ï¼å¹¶ä¸countå ä¸
    items[count] = item;
    ++count;
    return true;
  }

  // åºæ æä½
  public String pop() {
    // æ ä¸ºç©ºï¼åç´æ¥è¿ånull
    if (count == 0) return null;
    // è¿åä¸æ ä¸ºcount-1çæ°ç»åç´ ï¼å¹¶ä¸æ ä¸­åç´ ä¸ªæ°countåä¸
    String tmp = items[count-1];
    --count;
    return tmp;
  }
}
```

_ä¸é¢ä»£ç æ¯æ·è´ç½ä¸çï¼ä½¿ç¨ java çæ°ç»å®ç°æ ã_

äºè§£äºå®ä¹ååºæ¬æä½ï¼é£å®çæä½çæ¶é´ãç©ºé´å¤æåº¦æ¯å¤å°å¢ï¼

ä¸ç®¡æ¯é¡ºåºæ è¿æ¯é¾å¼æ ï¼å­å¨æ°æ®åªéè¦ä¸ä¸ªå¤§å°ä¸º n çæ°ç»å°±å¤äºãå¨å¥æ ååºæ è¿ç¨ä¸­ï¼åªéè¦ä¸ä¸¤ä¸ªä¸´æ¶åéå­å¨ç©ºé´ï¼æä»¥ç©ºé´å¤æåº¦æ¯ O(1)ã

æ³¨æï¼è¿éå­å¨æ°æ®éè¦ä¸ä¸ªå¤§å°ä¸º n çæ°ç»ï¼å¹¶ä¸æ¯è¯´ç©ºé´å¤æåº¦å°±æ¯ O(n)ãå ä¸ºï¼è¿ n ä¸ªç©ºé´æ¯å¿é¡»çï¼æ æ³çæãæä»¥æä»¬è¯´ç©ºé´å¤æåº¦çæ¶åï¼æ¯æ**é¤äºåæ¬çæ°æ®å­å¨ç©ºé´å¤ï¼ç®æ³è¿è¡è¿éè¦é¢å¤çå­å¨ç©ºé´**ã

ä¸ç®¡æ¯é¡ºåºæ è¿æ¯é¾å¼æ ï¼å¥æ ãåºæ åªæ¶åæ é¡¶ä¸ªå«æ°æ®çæä½ï¼æä»¥æ¶é´å¤æåº¦é½æ¯ O(1)ã

### æ çåºç¨ - ååè¿ç®

ååè¿ç®æ¯ç¼è¯å¨å©ç¨æ æ¥å®ç°çè¡¨è¾¾å¼æ±å¼ã

å¯¹äºååè¿ç®æ¥è¯´ï¼æä»¬å¤§èå¯ä»¥å¾å¿«çè®¡ç®åºæ¥ï¼ä½æ¯å¯¹äºè®¡ç®æºèè¨ï¼çè§£ååè¿ç®æ¬æ¥å°±é¾ã

é£ä¹ç¼è¯å¨å¦ä½éè¿æ æ¥è§£å³ååè¿ç®ï¼é¦åæä»¬éè¦ä¸¤ä¸ªæ ï¼ä¸ä¸ªä¿å­æä½æ°ï¼ä¸ä¸ªä¿å­è¿ç®ç¬¦ãä»å·¦åå³éåè¡¨è¾¾å¼ï¼å½éå°æ°å­ï¼æä»¬å°±ç´æ¥åå¥æä½æ°æ ï¼å½éå°è¿ç®ç¬¦ï¼å°±ä¸è¿ç®ç¬¦æ çæ é¡¶åç´ è¿è¡æ¯è¾ã

å¦ææ¯è¿ç®ç¬¦æ é¡¶åç´ çä¼åçº§é«ï¼å°±å°å½åè¿ç®ç¬¦åå¥æ ï¼å¦ææ¯è¿ç®ç¬¦æ é¡¶åç´ çä¼åçº§ä½æèç¸åï¼ä»è¿ç®ç¬¦æ ä¸­åæ é¡¶è¿ç®ç¬¦ï¼ä»æä½æ°æ çæ é¡¶å 2 ä¸ªæä½æ°ï¼ç¶åè¿è¡è®¡ç®ï¼åæè®¡ç®å®çç»æåå¥æä½æ°æ ï¼ç»§ç»­æ¯è¾ã

![æ åéå-ååè¿ç®](https://archive.static.spiritling.net/images/04_StackAndQueue-Arithmetic.png)

## éå

æ æ¯åè¿ååºï¼é£ææ²¡æåè¿ååºçå¢ï¼å½ç¶æï¼å°±æ¯éåã

**åè¿ååºï¼è¿å°±æ¯å¸åçâéåâ**

æ åªæ¯æä¸¤ä¸ªåºæ¬æä½ï¼**å¥æ  push()ååºæ  pop()**ãéåè·æ éå¸¸ç¸ä¼¼ï¼æ¯æçæä½ä¹å¾æéï¼æåºæ¬çæä½ä¹æ¯ä¸¤ä¸ªï¼**å¥é enqueue()**ï¼æ¾ä¸ä¸ªæ°æ®å°éåå°¾é¨ï¼**åºé dequeue()**ï¼ä»éåå¤´é¨åä¸ä¸ªåç´ ã

![æ åéå-éå](https://archive.static.spiritling.net/images/04_StackAndQueue-Queue.png)

æä»¥ï¼éåè·æ ä¸æ ·ï¼ä¹æ¯ä¸ç§**æä½åéççº¿æ§è¡¨æ°æ®ç»æ**ã

éåçæ¦å¿µå¾å¥½çè§£ï¼åºæ¬æä½ä¹å¾å®¹æææ¡ãä½ä¸ºä¸ç§éå¸¸åºç¡çæ°æ®ç»æï¼éåçåºç¨ä¹éå¸¸å¹¿æ³ï¼ç¹å«æ¯ä¸äºå·ææäºé¢å¤ç¹æ§çéåï¼æ¯å¦å¾ªç¯éåãé»å¡éåãå¹¶åéåãå®ä»¬å¨å¾å¤ååºå±ç³»ç»ãæ¡æ¶ãä¸­é´ä»¶çå¼åä¸­ï¼èµ·çå³é®æ§çä½ç¨ãæ¯å¦é«æ§è½éå DisruptorãLinux ç¯å½¢ç¼å­ï¼é½ç¨å°äºå¾ªç¯å¹¶åéåï¼Java concurrent å¹¶ååå©ç¨ ArrayBlockingQueue æ¥å®ç°å¬å¹³éç­ã

### å®ç°éå

è·æ ä¸æ ·ï¼éåå¯ä»¥ç¨æ°ç»æ¥å®ç°ï¼ä¹å¯ä»¥ç¨é¾è¡¨æ¥å®ç°ãç¨æ°ç»å®ç°çæ å«ä½é¡ºåºæ ï¼ç¨é¾è¡¨å®ç°çæ å«ä½é¾å¼æ ãåæ ·ï¼ç¨æ°ç»å®ç°çéåå«ä½**é¡ºåºéå**ï¼ç¨é¾è¡¨å®ç°çéåå«ä½**é¾å¼éå**ã

```java
// ç¨æ°ç»å®ç°çéå
public class ArrayQueue {
  // æ°ç»ï¼itemsï¼æ°ç»å¤§å°ï¼n
  private String[] items;
  private int n = 0;
  // headè¡¨ç¤ºéå¤´ä¸æ ï¼tailè¡¨ç¤ºéå°¾ä¸æ 
  private int head = 0;
  private int tail = 0;

  // ç³è¯·ä¸ä¸ªå¤§å°ä¸ºcapacityçæ°ç»
  public ArrayQueue(int capacity) {
    items = new String[capacity];
    n = capacity;
  }

  // å¥é
  public boolean enqueue(String item) {
    // å¦ætail == n è¡¨ç¤ºéåå·²ç»æ»¡äº
    if (tail == n) return false;
    items[tail] = item;
    ++tail;
    return true;
  }

  // åºé
  public String dequeue() {
    // å¦æhead == tail è¡¨ç¤ºéåä¸ºç©º
    if (head == tail) return null;
    // ä¸ºäºè®©å¶ä»è¯­è¨çåå­¦ççæ´å æç¡®ï¼æ--æä½æ¾å°åç¬ä¸è¡æ¥åäº
    String ret = items[head];
    ++head;
    return ret;
  }
}
```

å¯¹äºæ æ¥è¯´ï¼æä»¬åªéè¦ä¸ä¸ª**æ é¡¶æé**å°±å¯ä»¥äºãä½æ¯éåéè¦ä¸¤ä¸ªæéï¼ä¸ä¸ªæ¯ head æéï¼æåéå¤´ï¼ä¸ä¸ªæ¯ tail æéï¼æåéå°¾ã

å½ aãbãcãd ä¾æ¬¡å¥éä¹åï¼éåä¸­ç head æéæåä¸æ ä¸º 0 çä½ç½®ï¼tail æéæåä¸æ ä¸º 4 çä½ç½®ã

å½æä»¬è°ç¨ä¸¤æ¬¡åºéæä½ä¹åï¼éåä¸­ head æéæåä¸æ ä¸º 2 çä½ç½®ï¼tail æéä»ç¶æåä¸æ ä¸º 4 çä½ç½®ã

![æ åéå-éåè¿è¡](https://archive.static.spiritling.net/images/04_StackAndQueue-QueueRunning.png)

éçä¸åå°è¿è¡å¥éãåºéæä½ï¼head å tail é½ä¼æç»­å¾åç§»å¨ãå½ tail ç§»å¨å°æå³è¾¹ï¼å³ä½¿æ°ç»ä¸­è¿æç©ºé²ç©ºé´ï¼ä¹æ æ³ç»§ç»­å¾éåä¸­æ·»å æ°æ®äºã

å¨æ°ç»é£ä¸èï¼æä»¬ä¹éå°è¿ç±»ä¼¼çé®é¢ï¼å°±æ¯æ°ç»çå é¤æä½ä¼å¯¼è´æ°ç»ä¸­çæ°æ®ä¸è¿ç»­ãä½ è¿è®°å¾æä»¬å½æ¶æ¯æä¹è§£å³çåï¼å¯¹ï¼ç¨æ°æ®æ¬ç§»!

æ¯æ¬¡è¿è¡åºéæä½é½ç¸å½äºå é¤æ°ç»ä¸æ ä¸º 0 çæ°æ®ï¼è¦æ¬ç§»æ´ä¸ªéåä¸­çæ°æ®ï¼è¿æ ·åºéæä½çæ¶é´å¤æåº¦å°±ä¼ä»åæ¥ç O(1) åä¸º O(n)ãå®éä¸ï¼æä»¬å¨åºéæ¶å¯ä»¥ä¸ç¨æ¬ç§»æ°æ®ãå¦ææ²¡æç©ºé²ç©ºé´äºï¼æä»¬åªéè¦å¨å¥éæ¶ï¼åéä¸­è§¦åä¸æ¬¡æ°æ®çæ¬ç§»æä½ã

æä»¥æ¹é ä¸ä¸é¢çä»£ç åï¼

```java
   // å¥éæä½ï¼å°itemæ¾å¥éå°¾
  public boolean enqueue(String item) {
    // tail == nè¡¨ç¤ºéåæ«å°¾æ²¡æç©ºé´äº
    if (tail == n) {
      // tail ==n && head==0ï¼è¡¨ç¤ºæ´ä¸ªéåé½å æ»¡äº
      if (head == 0) return false;
      // æ°æ®æ¬ç§»
      for (int i = head; i < tail; ++i) {
        items[i-head] = items[i];
      }
      // æ¬ç§»å®ä¹åéæ°æ´æ°headåtail
      tail -= head;
      head = 0;
    }

    items[tail] = item;
    ++tail;
    return true;
  }
```

ä»ä»£ç ä¸­æä»¬çå°ï¼å½éåç tail æéç§»å¨å°æ°ç»çæå³è¾¹åï¼å¦æææ°çæ°æ®å¥éï¼æä»¬å¯ä»¥å° head å° tail ä¹é´çæ°æ®ï¼æ´ä½æ¬ç§»å°æ°ç»ä¸­ 0 å° tail-head çä½ç½®ã

![æ åéå-éåæ°æ®æ¬ç§»](https://archive.static.spiritling.net/images/04_StackAndQueue-QueueDataMovement.png)

åºéæä½çæ¶é´å¤æåº¦ä»ç¶æ¯ O(1)ï¼ä½æ¯å¥éæä½æ¶éè¦ä½¿ç¨åé¢æå°çæ¶é´å¤æåº¦ä¸­çæè¿åæï¼æ¶é´å¤æåº¦ä»æ¯ O(1) ã

### å¾ªç¯éå

åæç¨æ°ç»æ¥å®ç°éåçæ¶åï¼å¨ tail==n æ¶ï¼ä¼ææ°æ®æ¬ç§»æä½ï¼è¿æ ·å¥éæä½æ§è½å°±ä¼åå°å½±åãé£ææ²¡æåæ³è½å¤é¿åæ°æ®æ¬ç§»å¢ï¼æä»¬æ¥ççå¾ªç¯éåçè§£å³æè·¯ã

å¾ªç¯éåï¼é¡¾åæä¹ï¼å®é¿å¾åä¸ä¸ªç¯ãåæ¬æ°ç»æ¯æå¤´æå°¾çï¼æ¯ä¸æ¡ç´çº¿ãç°å¨æä»¬æé¦å°¾ç¸è¿ï¼æ³æäºä¸ä¸ªç¯ãå¦ä¸å¾æç¤º

![æ åéå-å¾ªç¯éå](https://archive.static.spiritling.net/images/04_StackAndQueue-CircularQueue.png)

### éååºç¨

éåè¿ç§æ°æ®ç»æå¾åºç¡ï¼å¹³æ¶çä¸å¡å¼åä¸å¤§å¯è½ä»é¶å®ç°ä¸ä¸ªéåï¼çè³é½ä¸ä¼ç´æ¥ç¨å°ãèä¸äºå·æç¹æ®ç¹æ§çéååºç¨å´æ¯è¾å¹¿æ³ï¼æ¯å¦é»å¡éååå¹¶åéåã

**é»å¡éå**å¶å®å°±æ¯å¨éååºç¡ä¸å¢å äºé»å¡æä½ãç®åæ¥è¯´ï¼å°±æ¯å¨éåä¸ºç©ºçæ¶åï¼ä»éå¤´åæ°æ®ä¼è¢«é»å¡ãå ä¸ºæ­¤æ¶è¿æ²¡ææ°æ®å¯åï¼ç´å°éåä¸­æäºæ°æ®æè½è¿åï¼å¦æéåå·²ç»æ»¡äºï¼é£ä¹æå¥æ°æ®çæä½å°±ä¼è¢«é»å¡ï¼ç´å°éåä¸­æç©ºé²ä½ç½®ååæå¥æ°æ®ï¼ç¶ååè¿åã

çº¿ç¨å®å¨çéåæä»¬å«ä½**å¹¶åéå**ãæç®åç´æ¥çå®ç°æ¹å¼æ¯ç´æ¥å¨ enqueue()ãdequeue() æ¹æ³ä¸å éï¼ä½æ¯éç²åº¦å¤§å¹¶ååº¦ä¼æ¯è¾ä½ï¼åä¸æ¶å»ä»åè®¸ä¸ä¸ªå­æèåæä½ãå®éä¸ï¼åºäºæ°ç»çå¾ªç¯éåï¼å©ç¨ CAS åå­æä½ï¼å¯ä»¥å®ç°éå¸¸é«æçå¹¶åéåãè¿ä¹æ¯å¾ªç¯éåæ¯é¾å¼éååºç¨æ´å å¹¿æ³çåå ã
