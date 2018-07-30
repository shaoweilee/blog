# webpack

其实，官方指南是最好的教程了https://webpack.docschina.org/guides/

本文意在用最简单的语言，来简化操作的步骤。

## 一、安装

在安装之前，确保已经安装node.js；

下面开始：

```bash
npm install --save-dev webpack
```

如果使用4+版本，还需安装命令行工具：

```bash
npm install --save-dev webpack-cli
```

参数--save-dev（简写是-D）的意思是安装在本地的、开发时所需的依赖。这也是官方指南建议的，比如使用npm脚本时，会在本地的node_modules中寻找webpack。

与之对应的是--save，用于将生产所需的依赖安装到本地。



## 二、基本配置文件

通常，我们在src目录下保存开发环境的代码，将编译完成后的代码和文件放在dist目录下。

接下来，新建这两个目录，然后在dist目录下新建bundle.js文件与index.html文件。

然后就可以创建webpack.config.js文件了：

```JavaScript
//一个最简单的webpack.config.js文件
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```

```JavaScript
//甚至不需要path模块的webpack.config.js
module.exports = {
    entry: './src/index.js',
    output: {
        filename: 'bundle.js',
        path: __dirname + '/dist',//输出目录必须是绝对目录。
    },
};
```

接下来，我们可以用命令行进行手动编译：

```bash
webpack --config webpack.config.js
```

注意，使用--config参数只是表明我们可以使用不同的配置文件，webpack默认使用的就是webpack.config.js文件。

### 好麻烦

webpack只是最简单的命令之一，如果我们必须每次都带--config参数，在命令行敲这么多字母就会显得很麻烦，幸运的是我们可以为命令指定别名，方便调用：

```JavaScript
//在package.json中的"script"添加：
"scripts": {
  "build": "webpack --config webpack.config.js",
},
```

接下来就可以在命令行中使用了：

```bash
npm run build
```

### 结束了？

其实到这里，webpack打包管理依赖的作用就已经凸显出来了——配置文件只是指定了入口出口，无论我们怎么在src目录下折腾依赖代码，无论使用commonjs还是ES6原生模块（当然不推荐不同模块加载方式混用），都能得到webpack完美的支持。然而它的功能远没有结束。

## 三、资源管理

webpack不只是能管理js依赖和打包js，它还能管理css、图片等资源。

### 加载CSS

为了使CSS也能用模块方式**加载到页面**，我们可以使用style-loader和css-loader：

```bash
npm install -D style-loader css-loader
```

style-loader用于把样式用\<style>标签添加到页面，css-loader用于解释import/require等引入语句。

接下来，在src目录下增加index.css文件，随便写样式；

修改webpack.config.js，增加以下代码：

```JavaScript
module: {
    rules:[
        {
            test: /\.css$/,
            use: ['style-loader', 'css-loader'],
        }
    ],
}
```

然后，在src的index.js下引入css：

```JavaScript
import './index.css';
```

构建，成功！

但是，如果写的css中包含图片，这时候是会报错的。

### 加载图片

安装所需loader：

```bash
npm install -D file-loader
```

修改webpack.config.js：

```javascript
module: {
    rules: [
        {
            test: /\.(png|svg|jpe?g|gif)$/,
            use: ['file-loader'],
        }
    ]
}
```

接下来，就可以使用了，准备好任意图片，在index.js文件里使用：

```JavaScript
import img from './anyPic.jpg';
let pic = new Image();
pic.src = img;
document.body.appendChild(pic);
```

在css文件中使用，写法没啥区别：

```css
body{
    background: url('./anyPic.jpg');
}
```

构建，成功后，在浏览器中查看用到的图片，会发现图片名称已经改成了一串哈希值+扩展名的形式。如果想用原来的文件名，这样就可以了：

```JavaScript
module: {
    rules: [
        {
            test: /\.(png|svg|jpe?g|gif)$/,
            use: [
                {
                	loader: 'file-loader',
                    options: {
                        name: '[name].[ext]'
                        //设置单独存放目录的方式：
                        //name: [path][name].[ext]
                        //name: images/[name].[ext]
                        //outputPath: 'images'
                    }
                }
            ],
        }
    ]
}
```

但是图片大小并没有被优化，要优化还需要另一个loader：

```bash
npm install -D image-webpack-loader
```

这个loader必须紧跟file-loader使用，再修改webpack.config.js：

```JavaScript
module: {
    rules: [
        {
            test: /\.(png|svg|jpe?g|gif)$/,
            use: [
                {
                	loader: 'file-loader',
                    options: {
                        name: '[name].[ext]'
                    }
                },
                'image-webpack-loader'
            ],
        }
    ]
}
```

## 四、管理输出

假如我们的项目有多个入口呢，像这样：

```JavaScript
module.exports = {
    entry: {
        index: __dirname + '/src/index.js',
        test: __dirname + '/src/test.js'
    }
    output: {
    	path: __dirname + 'dist',
    	filename: [name].bundle.js//会按照入口名字输出。
    }
}
```

但是相应的，我们也得把index.html中引入的js文件名指定为新输出的，下面介绍自动更新的方法。

### 使用HtmlWebpackPlugin

安装：

```bash
npm install -D html-webpack-plugin
```

使用：

```JavaScript
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
    plugins: [
        new HtmlWebpackPlugin({
            title: '口袋豆瓣'
        }),
    ],
}
```

默认生成的文件名是index.html，自动引入了生成的js文件。请注意，如果用了这个插件，在不指定模板的情况下，就算没有index.html文件，也是可以的，它会自己创建index.html文件。

### 使用CleanWebpackPlugin清理dist文件夹

安装：

```bash
npm install -D clean-webpack-plugin
```

使用：

```javascript
const CleanWebpackPlugin = require('clean-webpack-plugin');
module.exports = {
    plugins: [
        new CleanWebpackPlugin(['dist'])
    ]
}
```

## 五、方便开发

### source map

通过source map来增强调试，对于开发环境，推荐使用eval-source-map；对于生产环境，省略source map（即省略devtool选项）是不错的选择，设为source-map将使得source map作为一个单独的文件生成。

### 自动编译

webpack提供三种方式，使得模块发生后可以自动编译：

1. 观察模式
2. webpack-dev-server
3. webpack-dev-middleware

绝大多数情况下，使用webpack-dev-server就可以了。

#### 观察模式

在命令行使用就行了：

```bash
webpack --watch
```

当然自定义npm scripts也可以。

现在保存模块就会使得webpack自动编译了，但是不能使浏览器自动刷新。

#### webpack-dev-server（推荐）

安装：

```bash
npm install -D webpack-dev-server
```

webpack-dev-server会开启一个服务器，能够实现重新加载，要使用的话，得在webpack.config.js中告诉webpackc查找文件的位置：

```JavaScript
module.exports = {
    devServer: {
        contentBase: __dirname + '/dist'//推荐使用绝对路径
    }
}
```

接下来在命令行使用就行了：

```bash
webpack-dev-server --open
```

加上--open参数，会自动打开浏览器。这时候浏览器地址已经不是file://而是http://了。

当然，更推荐使用npm scripts：

```json
"scripts": {
    "start": "webpack-dev-server --open"
}
```

注意，开启服务器后，服务器会寻找名为index.html的文件，如果没有，页面就会有“cannot get /”的错误，或者显示contentBase指定的目录，要解决这一点，要么把输出文件命名为index.html，要么为devServer指定入口文件：

```JavaScript
module.exports = {
    devServer: {
        contentBase: __dirname + '/dist',//推荐使用绝对路径
        index: 'test.html'
    }
}
```

#### webpack-dev-middleware

这个实际上是用node框架express手写服务器，先不记录。



## 六、模块热替换（HMR）

模块热替换的必要性：

1. 能保留在页面重载时丢失的状态；
2. 只更新变更的内容；
3. 调整样式更加快速；

在模块中，HMR是可选功能，只会影响包含HMR代码的模块。在开发过程中，可以用HMR替代实时重载。webpack-dev-server支持hot模式，在完全重载整个页面之前，hot模式会尝试使用HMR来更新。

### 启用HMR

修改webpack.config.js配置，以使用webpack内置的HMR插件：

```JavaScript
const webpack = require('webpack');
module.exports = {
    devServer: {
        hot: true//添加hot参数
    },
    plugins: [//添加HMR插件
        new webpack.NamedModulesPlugin(),
        new webpack.HotModuleReplacementPlugin(),
    ]
}
```

但这时HMR还不能工作，需要在JS模块中执行webpack提供的API才可以。如果是React体系，可以用react-hot-loader https://github.com/gaearon/react-hot-loader

对于css，前文配置的css-loader和style-loader自带HMR接口，无需配置。

## 七、tree shaking

这部分是清理模块中没有用到的接口，即被export而没被import的。

## 八、生产环境构建

### 配置

最好的做法是将开发环境和生产环境的webpack配置写入两个不同的文件，然后保留一个通用配置文件，再合并，使用这个开发依赖：

```bash
npm install -D webpack-merge
```

把webpack.config.js分离为三个文件：

1. webpack.common.js

   ```javascript
   const CleanWebpackPlugin = require('clean-webpack-plugin');
   const HtmlWebpackPlugin = require('html-webpack-plugin');
   module.exports = {
       entry: {
           app: './src/index.js'
       },
       output: {
           filename: '[name].bundle.js',
           path: __dirname + '/dist'
       },
       module: {
           rules: [
               {
                   test: /\.css$/,
                   use: ['style-loader', 'css-loader']
               }
           ]  
       },
       plugins: [
           new CleanWebpackPlugin(['dist']),
           new HtmlWebpackPlugin({
               title: '口袋豆瓣'
           })
       ],
   }
   ```

   

2. webpack.development.js

   ```javascript
   const merge = require('webpack-merge');
   const common = require('./webpack.common.js');
   const webpack = require('webpack');
   module.exports = merge(common, {
       devtool: 'eval-source-map',
       mode: 'development',
       devServer: {
           contentBase: './dist',
           hot: true
       },
       plugins: [
           new webpack.NamedModulesPlugin(),
           new webpack.HotModuleReplacementPlugin(),
       ]
   });
   ```

   

3. webpack.production.js

   ```javascript
   const merge = require('webpack-merge');
   const common = require('./webpack.common.js');
   module.exports = merge(common, {
       devtool: 'source-map',
       mode: 'production'
   })
   ```

然后，有必要修改下npm scripts，方便编译：

```JavaScript
"scripts": {
    "start": "webpack-dev-server --open --config webpack.development.js",
    "build": "webpack --config webpack.production.js"
}
```

### 指定环境  

操作这一步的原因是，许多库都和process.env.NODE_ENV这个变量关联，来指定库中该引用哪些内容。但是，实际上，process.env.NODE_ENV这个变量是不存在的，所以需要一个webpack的内置插件来为**所有依赖**指定：

```JavaScript
const webpack = require("webpack");
module.exports = {
    plugins: [
        new webpack.DefinePlugin({
            'process.env.NODE_ENV': JSON.stringify('production'),
        }),
    ]
}
```

### 分离CSS

使用`ExtractTextPlugin`插件，注意应该在开发环境中避免使用，因为这是没有意义哒。

它会将所有的入口 chunk(entry chunks)中引用的 `*.css`，移动到独立分离的 CSS 文件。因此，你的样式将不再内嵌到 JS bundle 中，而是会放到一个单独的 CSS 文件（即 `styles.css`）当中。 如果css文件大，这样是有益的，因为js和css是一起加载的嘛。

安装（注意版本！用webpack4+必须这样安装）：

```bash
npm install -D extract-text-webpack-plugin@next
```

更新webpack.production.js：

```JavaScript
const ExtractTextPlugin = require("extract-text-webpack-plugin");

module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ExtractTextPlugin.extract({
          fallback: "style-loader",//当css没有被导出为一个单独chunk时使用的loader，其实是降级处理。
          use: "css-loader"
        })
      }
    ]
  },
  plugins: [
      new ExtractTextPlugin({
          filename: 'style.[chunkhash].css'
      }),
  ]
}
```

### 多个实例

如果有多于一个 `ExtractTextPlugin` 实例的情形，请使用此方法每个实例上的 `extract` 方法。

```JavaScript
const ExtractTextPlugin = require('extract-text-webpack-plugin');

// 创建多个实例
const extractCSS = new ExtractTextPlugin('stylesheets/[name]-one.css');
const extractLESS = new ExtractTextPlugin('stylesheets/[name]-two.css');

module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: extractCSS.extract([ 'css-loader', 'postcss-loader' ])
      },
      {
        test: /\.less$/i,
        use: extractLESS.extract([ 'css-loader', 'less-loader' ])
      },
    ]
  },
  plugins: [
    extractCSS,
    extractLESS
  ]
};
```

## 九、代码分离

webpack其实提供了两种JS代码分离的方式：

- 多entry+optimization.splitChunks
- 动态导入

### 多entry+optimization.splitChunks

多配置几个entry之后，在webpack.common.js修改：

```JavaScript
const CleanWebpackPlugin = require('clean-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
    entry: {
        index: './src/index.js',
        main: './src/main.js'
    },
    output: {
        filename: '[name].bundle.js',
        path: __dirname + '/dist'
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            }
        ]  
    },
    plugins: [
        new CleanWebpackPlugin(['dist']),
        new HtmlWebpackPlugin({
            title: '口袋豆瓣'
        }),
    ],
    optimization: {
        splitChunks: {
            cacheGroups: {
                commons: {
                    name: "commons",
                    chunks: "initial",
                    minChunks: 2
                }
            }
        }
    }
}
```

这样，被公共引用的模块会在各个入口文件中删除，然后被放到一个名为common.js的文件中。

另外，optimization.splitChunks提供了两个配置：

```JavaScript
//创建一个common的chunk，包含在每个入口文件中共享的代码。
splitChunks: {
    cacheGroups: {
        commons: {
            name: "commons",
            chunks: "initial",
            minChunks: 2
        }
    }
}
```



```JavaScript
//创建一个vendors的chunk，包含整个应用中来自node_modules中的代码。用库的时候推荐。
splitChunks: {
    cacheGroups: {
        commons: {
            test: /[\\/]node_modules[\\/]/,
            name: "vendors",
            chunks: "all"
        }
    }
}
```

### 动态导入

使用import()提案。官方推荐的。调用 `import()` 的地方，被作为分离的模块起点，意思是，被请求的模块和它引用的所有子模块，会分离到一个单独的 chunk 中。

`import` 规范不允许控制模块的名称或其他属性，因为 "chunks" 只是 webpack 中的一个概念。幸运的是，webpack 中可以通过注释接收一些特殊的参数，而无须破坏规定：

```JavaScript
import(
  /* webpackChunkName: "my-chunk-name" */   //被引入的模块构建完成后，被分离出来的chunk名
  /* webpackMode: "lazy" */
  'module'//被引入的模块
);
```

`webpackChunkName`：新 chunk 的名称。从 webpack 2.6.0 开始，`[index]` and `[request]` 占位符，分别支持赋予一个递增的数字和实际解析的文件名。

`webpackMode`：从 webpack 2.6.0 开始，可以指定以不同的模式解析动态导入。支持以下选项：

- `"lazy"`（默认）：为每个 `import()` 导入的模块，生成一个可延迟加载(lazy-loadable) chunk。
- `"lazy-once"`：生成一个可以满足所有 `import()` 调用的单个可延迟加载(lazy-loadable) chunk。此 chunk 将在第一次 `import()` 调用时获取，随后的 `import()` 调用将使用相同的网络响应。注意，这种模式仅在部分动态语句中有意义，例如 `import(`./locales/${language}.json`)`，其中可能含有多个被请求的模块路径。
- `"eager"`：不会生成额外的 chunk，所有模块都被当前 chunk 引入，并且没有额外的网络请求。仍然会返回 `Promise`，但是是 resolved 状态。和静态导入相对比，在调用 import（）完成之前，该模块不会被执行。
- `"weak"`：尝试加载模块，如果该模块函数已经以其他方式加载（即，另一个 chunk 导入过此模块，或包含模块的脚本被加载）。仍然会返回 `Promise`，但是只有在客户端上已经有该 chunk 时才成功解析。如果该模块不可用，`Promise` 将会是 rejected 状态，并且网络请求永远不会执行。当需要的 chunks 始终在（嵌入在页面中的）初始请求中手动提供，而不是在应用程序导航在最初没有提供的模块导入的情况触发，这对于通用渲染（SSR）是非常有用的。

>  请注意，这两个选项可以组合起来使用，如 `/* webpackMode: "lazy-once", webpackChunkName: "all-i18n-data" */`，这会按没有花括号的 JSON5 对象去解析。 

> `import()` 调用会在内部用到 [promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。如果在旧有版本浏览器中使用 `import()`，记得使用 一个 polyfill 库（例如 [es6-promise](https://github.com/stefanpenner/es6-promise) 或 [promise-polyfill](https://github.com/taylorhakes/promise-polyfill)），来 shim `Promise`。 



## 十、缓存

以前我们用'[name].bundle.js'作为构建后的文件名，现在，官方指南建议使用'[name].[chunkhash].js'：

注意，[hash]和[chunkhash]都应该避免在开发时使用，因为这样是没有意义的，甚至在开发环境下会报错：

```bash
ERROR in chunk index [entry]
[name].[chunkhash].js
Cannot use [chunkhash] or [contenthash] for chunk in '[name].[chunkhash].js' (use [hash] instead)
```



```JavaScript
module.exports = {
    output: {
        filename: [name].[chunkhash].js,
        path: __dirname + '/dist'
    }
}
```

将第三方库（如React）提取到单独的vender chunk中是推荐的做法，因为它们不像其他本地文件那样会被频繁修改。官方指南推荐的是在entry中配置vender，再利用CommonsChunkPlugin分离vender实现，但是后者已经在webpack4中被移除了，所以先贴出新的entry：

```javascript
module.exports = {
    entry: {
        index: './src/index.js',
        vender: [
            'react'
        ]
    }
}
```

以后再写分离vender。

但是，增减模块仍然会引起vender chunk的hash变化，官方指南推荐了webpack内置插件[`HashedModuleIdsPlugin`](https://webpack.docschina.org/plugins/hashed-module-ids-plugin)来解决这个问题。



Chunkhash,hash,contenthash;

vender分离

babel





2018年7月27日，更新一下自用webpack配置：

```javascript
//webpack.common.js
const CleanWebpackPlugin = require('clean-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
  entry: {
    index: './src/index.js',
  },
  module: {
    rules: [
      {
        test: /\.jsx$/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        },
      }
    ]
  },
  plugins: [
    new CleanWebpackPlugin(['dist']),
    new HtmlWebpackPlugin({
      title: '口袋豆瓣'
    }),
  ],
  optimization: {
    splitChunks: {
      cacheGroups: {
        commons: {
          name: "commons",
          chunks: "initial",
          minChunks: 2
        }
      }
    }
  }
};
```

```javascript
//webpack.development.js
const merge = require('webpack-merge');
const common = require('./webpack.common.js');
const webpack = require('webpack');

module.exports = merge(common, {
  devtool: 'eval-source-map',
  mode: 'development',
  output: {
    filename: '[name].bundle.js',
    chunkFilename: '[name].bundle.js',
    path: __dirname + '/dist'
  },
  devServer: {
    contentBase: './dist',
    hot: true
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]  
  },
  plugins: [
    new webpack.NamedModulesPlugin(),
    new webpack.HotModuleReplacementPlugin(),
  ]
});
```

```javascript
//webpack.production.js
const webpack = require('webpack');
const merge = require('webpack-merge');
const common = require('./webpack.common.js');
const ExtractTextPlugin = require('extract-text-webpack-plugin');

module.exports = merge(common, {
  devtool: 'source-map',
  mode: 'production',
  output: {
    filename: '[name].[chunkhash].js',
    chunkFilename: '[name].bundle.js',
    path: __dirname + '/dist'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ExtractTextPlugin.extract({
          fallback: ['style-loader'],
          use: ['css-loader']
        })
      }
    ]
  },
  plugins: [
    new webpack.DefinePlugin({
      'process.env.NODE_ENV': JSON.stringify('production')
    }),
    new ExtractTextPlugin({
      filename: 'style.[chunkhash].css'
    })
  ],
});
```

```javascript
//package.json
{
  "name": "douban",
  "version": "1.0.0",
  "description": "P7 口袋豆瓣",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack-dev-server --open --config webpack.development.js",
    "build": "webpack --config webpack.production.js"
  },
  "repository": {
    "type": "git",
    "url": "http://git.imweb.io/qq610382941/douban.git"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "clean-webpack-plugin": "^0.1.19",
    "css-loader": "^0.28.11",
    "extract-text-webpack-plugin": "^4.0.0-beta.0",
    "file-loader": "^1.1.11",
    "html-webpack-plugin": "^3.2.0",
    "image-webpack-loader": "^4.3.1",
    "style-loader": "^0.21.0",
    "webpack": "^4.12.1",
    "webpack-cli": "^3.0.8",
    "webpack-dev-server": "^3.1.4",
    "webpack-merge": "^4.1.3"
  }
}

```

## 十一、使用sass-loader

安装：

```bash
npm install sass-loader node-sass webpack --save-dev
```

快速使用：

