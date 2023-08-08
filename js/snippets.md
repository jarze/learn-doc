# 代码片段

## html 文本去样式带换行文本内容

```js
const contentElement = () => {
	if (!document) return { contentImg: [], textArray: [] };
	const element = document.createElement('div');
	// 块元素后面增加换行，以便innerText可以获取换行文本
	element.innerHTML = self.content.replace(
		/<\/(p|div|blockquote|ul|ol|li|table|h\d)*>/g,
		'</$1>\n'
	);
	return {
		// 富文本中图片
		contentImg: element.querySelectorAll('img') || [],
		// 文本内容
		textArray: element.innerText.split('\n'),
	};
};

// 提取内容文本 截取第一行文本
const contentText = () => {
	return self.contentElement.textArray.find(t => !!t);
};
```

## 随机颜色

```js
const getRandomColor = () =>
	`#${Math.floor(Math.random() * 0xffffff).toString(16)}`;
```

## 随机数

```js
const getRandomNumber = (min, max) =>
	Math.floor(Math.random() * (max - min + 1)) + min;
```

## 颜色格式转换

```js
const hexToRgb = hex =>
	hex
		.replace(
			/^#?([a-f\d])([a-f\d])([a-f\d])$/i,
			(_, r, g, b) => `#${r}${r}${g}${g}${b}${b}`
		)
		.substring(1)
		.match(/.{2}/g)
		.map(x => parseInt(x, 16));
```

## uuid

```js
const uuid = a =>
	a
		? (a ^ ((Math.random() * 16) >> (a / 4))).toString(16)
		: ([1e7] + -1e3 + -4e3 + -8e3 + -1e11).replace(/[018]/g, uuid);
```

## 获取 cookie

```js
const getCookie = () =>
	document.cookie
		.split(';')
		.map(item => item.split('='))
		.reduce((acc, [k, v]) => (acc[k.trim().replace('"', '')] = v) && acc, {});
```
