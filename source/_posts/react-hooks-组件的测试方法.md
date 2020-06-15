---
title: react hooks 组件的测试方法
date: 2020-06-15 17:03:34
tags: 
	- hooks
	- 测试方法
---

> 自从react-hooks出来以后，重构后的代码基本都适用hook了，和同事聊了几句，都说条理清楚了，写起来思路清楚一些，nice！但是我的instance上没有组件方法了，测试怎么办呢？

<!--more-->

很简单，简单来说有如下两种情况：

### 事件有ui变化

那就用find方法去找事件前的ui，写一条断言；触发事件之后，找到现在的ui，再写一条断言去匹配即可。

### 事件无ui变化

这种情况下多半是发出了api请求，那我们就mock api即可。

假如组件调用了“api.auth.token()”方法，那mock就如下写：

```javascript
import * as api from '@/services/api';

jest.mock("@/services/api", () => {
    return {
        auth: {
            token: jest.fn(() => {
                return Promise.resolve({ data: { info: {} }, status: 'success' });
            })
        }
    };
});
```



