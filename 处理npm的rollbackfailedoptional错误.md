# 处理npm的rollbackfailedoptional错误

1.修改~/.npmrc文件(没有就自行新建一个)，写入

```bash
registry = https://registry.npm.taobao.org
```

或者命令行写入：

```bash
npm config set registry https://registry.npm.taobao.org
```

2.同样的方法，将disturl这个配置同样指向: [https://npm.taobao.org/dist](https://link.jianshu.com?t=https://npm.taobao.org/dist)

```bash
npm config set disturl https://npm.taobao.org/dist
```

再用`npm install`安装就好使了！