# NPM

学习NPM最主要的资料是参考在[官方文档](https://docs.npmjs.com)

## 目录

* local install: 放在当前项目的`./node_modules`目录下
* global install(-g): 放在`/usr/local`这样的node安装目录下
* **local**:  当需要通过`require()`使用时，采用local安装
* **global**: 当需要通过命令行运行时，采用global安装
* 如果两者都需要，可以都安装，或者采用npm link


## package.json

**name**

* 必填项
* 小写字符串
* 短小精悍
* 不要重名


**version**

* 必填项
* 采用semver形式


**scripts**

## CLI Commands

**npm run-script**

简写是`npm run`, 这个指令运行package`scripts`对象里面的任意指令，如果不指定具体的指令，它会列出所有的选项。

## npm link

开发npm模块时，会存在很多本地需要调试，但是暂时没有publish的模块。 我们需要做几件事情：

1. 通过module名称来require模块，比如
```
var myModule = require('my-module');
```
2. 在项目里面像他们被发布了一样去声明依赖关系
3. 我们希望可以修改代码，并立刻看到结果（不需要重复安装）

有两种方法是不太可取的：
1. 使用相对路径来引用本地module，如下
```
var myModule = require('../../local-folder/my-module')
```
这种方式的缺点在于，模块发布以后需要修改代码
2. 使用local的install
```
npm install ../../local-folder/my-module
```
这种方式的缺点在于，每次修改模块后都需要重新安装

更好的方法是采用npm link， linking的过程分为2步：

* 在一个module的根路径执行`npm link`,从而声明这个module为global的module
* 需要引用这个module时，在目标目录里面执行`npm link <module-name>`

## [npm-link-shared](https://github.com/OrKoN/npm-link-shared)

这种形式在没有递归引用的时候没有问题，不过当本地一个local module引用另外一个local module的时候，就会因为找不到依赖module而出现问题。为了解决这个问题，需要连接依赖module到父module里，然后再在app里面安装父module。比如下面这个模块结构：

```
modules/moduleA
modules/moduleB -> depends on moduleA
app/
```

可能会按照如下指令进行操作

```
cd modules/moduleA
npm link
cd modules/moduleB
npm link moduleA
npm link
cd app/
npm link moduleB
```

如果出现了很多module之间的内部依赖，这个过程会变得比较复杂，我们可以使用`npm-link-shared`来将这个过程自动化。 它使用一个cli指令，将一个目录下所有的module安装到目标app里。这种情况下有2个假定条件：

* 源目录下保存了所有需要安装在目标目录的module
* module所在目录的名字和package里面的module name严格一致。
