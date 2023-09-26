
# Nextjs 13

[TOC]

## 将服务器组件嵌套在客户端组件内
不支持的模式：将服务器组件导入客户端组件
推荐模式：将服务器组件作为 Props 传递给客户端组件

```js
// This pattern works:
// You can pass a Server Component as a child or prop of a
// Client Component.
import ExampleClientComponent from './example-client-component';
import ExampleServerComponent from './example-server-component';

// Pages in Next.js are Server Components by default
export default function Page() {
	return (
		<ExampleClientComponent>
			<ExampleServerComponent />
		</ExampleClientComponent>
	);
}
```

将属性从服务器传递到客户端组件（序列化）
从服务器传递到客户端组件的道具需要可序列化。这意味着函数、日期等值不能直接传递给客户端组件。相反，您应该将它们转换为可序列化的值（通常是字符串）。

在服务器组件内获取的数据不需要序列化，因为它们不会传递给客户端组件。

## “仅服务器”包 `server-only`

```js
import 'server-only';

export async function getData() {
	const res = await fetch('https://external-service.com/data', {
		headers: {
			authorization: process.env.API_KEY,
		},
	});

	return res.json();
}
```

相应的包 client-only 可用于标记包含仅限客户端代码的模块，例如访问对象的代码 window。

next 变量
NEXT_PUBLIC 用于公开的环境变量
私有变量不会在客户端中公开，只能在服务器端访问及使用

## `Route`

App Router app 目录中的文件。
Pages Router pages 目录中的文件。

值得注意的是：App Router 的优先级高于 Pages Router。跨目录的路由不应解析为相同的 URL 路径，并且会导致构建时错误以防止冲突。

![Title](https://nextjs.org/_next/image?url=/docs/dark/next-router-directories.png&w=1920&q=75&dpl=dpl_2R3kjiCD5HGdpH3yxuPw8Jc9nZ2F)

### `App Router`

![Title](https://nextjs.org/_next/image?url=/docs/dark/project-organization-colocation.png&w=1920&q=75&dpl=dpl_2R3kjiCD5HGdpH3yxuPw8Jc9nZ2F)

默认情况下，里面的组件 app 是 React Server Components。这是一种性能优化，可以让您轻松采用它们，并且您还可以使用 Client Components。您可以通过将其设置为 false 来禁用此行为。

```js
// next.config.js
module.exports = {
	reactStrictMode: true,
	experimental: {
		reactServerComponents: true,
	},
};
```

---

![Title](https://nextjs.org/_next/image?url=/docs/dark/file-conventions-component-hierarchy.png&w=1920&q=75&dpl=dpl_2R3kjiCD5HGdpH3yxuPw8Jc9nZ2F)

![Title](https://nextjs.org/_next/image?url=/docs/dark/nested-file-conventions-component-hierarchy.png&w=1920&q=75&dpl=dpl_2R3kjiCD5HGdpH3yxuPw8Jc9nZ2F)

## 第三方组件

```js
'use client';

import { Carousel } from 'acme-carousel';

export default Carousel;
```

## Using context in Client Components

```js
'use client';

import { createContext } from 'react';

export const ThemeContext = createContext({});

export default function ThemeProvider({ children }) {
	return <ThemeContext.Provider value='dark'>{children}</ThemeContext.Provider>;
}
```

## Sharing data between Server Components

在服务器组件之间共享获取请求

fetch 获取数据时，您可能希望在 apage 或 layout 及其某些子组件之间共享 a 的结果。这是组件之间不必要的耦合，并且可能导致 props 组件之间来回传递。

相反，我们建议将数据获取与使用数据的组件放在一起。fetch 请求会在服务器组件中自动进行重复数据删除，因此每个路由段都可以准确请求其所需的数据，而不必担心重复请求。Next.js 将从缓存中读取相同的值 fetch。

---

## `Concurrent React`

```js
一个关键属性是渲染是可中断的;
```
