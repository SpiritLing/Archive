

| Property     | Content                                                      |
| ------------ | ------------------------------------------------------------ |
| Id           | ac5cfbd1-c858-4496-be3b-f554b6ae4a17                         |
| Name         | 2022-07-08_react18x-react-router-v6-laz                      |
| ArticleLink  | https://blog.spiritling.cn/articles/ac5cfbd1-c858-4496-be3b-f554b6ae4a17 |
| Github Issue |                                                              |
| Gitee Issue  |                                                              |

# React v18.x 在 react-router v6 使用 lazy 动态加载组件实现

对于直接使用 `React.lazy` 来说，基本上是没有问题的，但是当在 ts 下，将导入的组件放到 router 的 elment 属性下会报错，一般为类型不匹配

而且对于 lazy 来说，是react提供的一个功能，并且需要配置 `fallback` 来确保当组件找不到或者正在获取时的替换组件。

那么我们可以创建一个 `lazy-import-component.tsx` 来共用

```tsx
import GlobalLoading from '@main/components/global-loading/global-loading';
import React, { LazyExoticComponent } from 'react';

export const LazyImportComponent = (props: {
  lazyChildren: LazyExoticComponent<() => JSX.Element>;
}) => {
  return (
    <React.Suspense fallback={<GlobalLoading />}>
      <props.lazyChildren />
    </React.Suspense>
  );
};
```

其中 `GlobalLoading` 为你自己写的loading组件，并且此处可以替换成任意组件

然后再 router 中使用即可解决 lazy 导入的组件不能在 router 的 elment中使用

```tsx
import { Navigate,RouteObject } from 'react-router-dom';
import React from 'react';
import { LazyImportComponent } from '@main/components/lazy-import-component';
const DashboardLazy = React.lazy(() => import('./dashboard'));
export const DashboardRouter: RouteObject = {
  path: '/dashboard',
  element: <LazyImportComponent lazyChildren={DashboardLazy} />
};
```

即可实现动态加载组件