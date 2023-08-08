# Android Echarts字体问题

```js
import RNEChartsPro from 'react-native-echarts-pro';

const fileName = Platform.select({
  ios: `DINCondensed-Bold.otf`,
  android: `file:///android_asset/fonts/DINCondensed-Bold.otf`,
});

// echart字体设置
export const extension = `
    setTimeout(()=>{
      var styles = document.createElement('style');
      styles.innerHTML = '@font-face{font-family:"DINCondensed-Bold";src:url("${fileName}");}';
      document.getElementsByTagName('head')[0].appendChild(styles);
    },100)
`;

<RNEChartsPro
	// extension={[extension]}
	option={option(t)}
	height={arcHeight}
	webViewSettings={{
		useWebView2: true,
		incognito: true,
		scrollEnabled: false,
		injectedJavaScriptBeforeContentLoaded: extension,
	}}
/>;
```

