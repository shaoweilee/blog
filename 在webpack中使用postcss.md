# 在webpack中使用postcss

首先是安装：

```bash
npm i -D postcss-loader
```

官方建议：建议css-loader和postcss-loader一同使用。用在css-loader和style-loader之后，但是在预处理器之前（这里指书写顺序，执行顺序是反着的）。如果只用postcss-loader，请不要使用@import。

安装好之后，在webpack.config.js同级目录下新建一个postcss.config.js:

```JavaScript
module.exports = {
  parser: 'sugarss',
  plugins: {
    'postcss-import': {},
    'postcss-preset-env': {},
    'cssnano': {}
  }
}
```

webpack.config.js如下：

```JavaScript
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'style-loader',
          { loader: 'css-loader', options: { importLoaders: 1 } },
          'postcss-loader'
        ]
      }
    ]
  }
}
```

## Options

| Name                                                         | Type               | Default     | Description                                  |
| ------------------------------------------------------------ | ------------------ | ----------- | -------------------------------------------- |
| [`exec`](https://github.com/postcss/postcss-loader#exec)     | `{Boolean}`        | `undefined` | Enable PostCSS Parser support in `CSS-in-JS` |
| [`parser`](https://github.com/postcss/postcss-loader#syntaxes) | `{String|Object}`  | `undefined` | Set PostCSS Parser                           |
| [`syntax`](https://github.com/postcss/postcss-loader#syntaxes) | `{String|Object}`  | `undefined` | Set PostCSS Syntax                           |
| [`stringifier`](https://github.com/postcss/postcss-loader#syntaxes) | `{String|Object}`  | `undefined` | Set PostCSS Stringifier                      |
| [`config`](https://github.com/postcss/postcss-loader#config) | `{Object}`         | `undefined` | Set `postcss.config.js` config path && `ctx` |
| [`plugins`](https://github.com/postcss/postcss-loader#plugins) | `{Array|Function}` | `[]`        | Set PostCSS Plugins                          |
| [`sourceMap`](https://github.com/postcss/postcss-loader#sourcemap) | `{String|Boolean}` | `false`     | Enable Source Maps                           |



































