# antd-theme-webpack-plugin

This webpack plugin is to  generate color specific less/css and inject into your `index.html` file so 
that you can change Ant Design specific color theme in browser.

Live Theme Demo: https://antd-live-theme.firebaseapp.com/

In order to integrate with your webpack configurations, install the package and add following code in your webpack config file.

## Install
  - npm install -D antd-theme-webpack-plugin

```js
const AntDesignThemePlugin = require('antd-theme-webpack-plugin');

const options = {
  antDir: path.join(__dirname, './node_modules/antd'),
  stylesDir: path.join(__dirname, './src/styles'),
  varFile: path.join(__dirname, './src/styles/variables.less'),
  mainLessFile: path.join(__dirname, './src/styles/index.less'),
  themeVariables: ['@primary-color'],
  indexFileName: 'index.html',
  generateOnce: false
}

const themePlugin = new AntDesignThemePlugin(options);
// in config object
plugins: [
    themePlugin
  ]
```
Add this plugin in `plugins` array.

- Here are some default paths.
- Default html file name
  - index.html
- Ant Design deafult path
  - /node_modules/antd
- Default styles directory
  - /src/styles
- Default variables file path
  - /src/styles/variables.less (import `antd/lib/style/themes/default.less` file at top (important))
- Default styles main file path
  - /src/styles/index.less
- Default Theme variables (that can be updated in browser)
  - ['@primary-color']
- generateOne: false
  - if you don't want to generate color.less on each chnage in code to make build process fast in development mode, assign it `true` value. But if you have new changes in your styles, you need to re-run your build process `npm start`.

So if your directory structure is different or file names are different then provide correct paths as options 
while initailizing plugin

If you directory structure is same as below then you don't need to override path
```
/
  /src
    /styles
      /index.less
      /variables.less
      /otherlessfiles.less
  node_modules
  public/index.html (index.html file at any path, only name matters in this case)
```

If you `index.html` file is not being generated by build process then add following code in your `index.html` or whatever is the name of html main file and add `indexFileName: false` in options/config. This way you can better place your below script in your html file according to your needs.

```
<link rel="stylesheet/less" type="text/css" href="/color.less" />
<script>
  window.less = {
    async: false,
    env: 'production'
  };
</script>
<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/less.js/2.7.2/less.min.js"></script>
```
Don't forget to add import Ant design default theme file i.e. antd/lib/style/themes/default.less in variables.less file.

# Enable Javascript for less-loader

You need to enable javascript for less-loader.

```
{
  enableJavascript: true
}

```

For those who are using `react-app-rewire-less` with `react-app-rewired`, enable javascript like this

```
module.exports = function override(config, env) {
  config = injectBabelPlugin(['import', { libraryName: 'antd', style: true }], config);
  config = rewireLess.withLoaderOptions({
    modifyVars: {
      '@primary-color': '#002251'
    },
    enableJavascript: true
  })(config, env);
  config.plugins.push(new AntDesignThemePlugin(options));
  return config;
};
```

# Changes

## v1.1.8
- Added support for webpack specific less imports so you can now import like this `@import "~antd/lib/style/themes/default.less";` instead of `@import "../../node_modules/antd/lib/style/themes/default.less";`
- Added cache mechanism to avoid generation of color.less incase of same css/less source.
