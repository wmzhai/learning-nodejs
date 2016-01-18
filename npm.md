# NPM

学习NPM最主要的资料是参考在[官方文档](https://docs.npmjs.com)

## 目录问题

* local install: 放在当前项目的`./node_modules`目录下
* global install(-g): 放在`/usr/local`这样的node安装目录下
* **local**:  当需要通过`require()`使用时，采用local安装
* **global**: 当需要通过命令行运行时，采用global安装
* 如果两者都需要，可以都安装，或者采用npm link
