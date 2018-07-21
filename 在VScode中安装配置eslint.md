# 在VScode中安装与配置eslint

## 安装

首先在VSCode中安装ESLint扩展；

然后用npm全局安装ESLint，本地安装开发依赖也行；

```bash
$ npm install -g eslint
```





## 配置

安装完成后，使用`eslint --init`命令在某个目录下生成一个配置文件`.eslintrc.js`；

如果需要支持jsx，还需要安装`eslint-plugin-react`插件。

然后进入VSCode设置ESLint扩展，需要在右侧用户配置新增一下配置文件的路径：

```json
"eslint.options": {
    "configFile": "path/to/.eslintrc.js"
},
```

OK！



