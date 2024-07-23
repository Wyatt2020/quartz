---
created: 2023-09-03 16:07
modified: 2023-09-03 16:49
tags:
  - 前端
imageNameKey: install-nodejs
---

> [!link] Reference
> [Node.js 安装及环境配置之 Windows 篇](https://www.cnblogs.com/liuqiyun/p/8133904.html)
> 
> [安装 npm 及 cnpm(Windows)](https://www.cnblogs.com/liaojie970/p/9296177.html)
> 
> [在 Ubuntu 24.04 LTS 上安装 Node.js 和 NPM](https://blog.csdn.net/ecscoupon/article/details/139921777)
> 
> [国内 npm 源镜像](https://blog.csdn.net/qq_43940789/article/details/131449710)
> 
> [下载地址](https://nodejs.org/zh-cn/download/package-manager)

### Windows 安装

1. 解压 zip

2. 将当前目录配置到环境变量
   ![[install-nodejs-1.png]]
   ![[install-nodejs-2.png]]

3. 查看版本：`node -v`
   ![[install-nodejs-3.png]]

4. 添加两个目录

   - `node_global`
   - `node_cache`

   ```shell
   npm config set prefix "D:\development\nodejs\node_global"
   npm config set cache "D:\development\nodejs\node_cache"
   ```

5. 环境变量

   1. 用户变量的 `Path` 添加 `node_global` 文件夹
      ![[install-nodejs-4.png]]

   2. 将 `node_global\node_modules` 文件夹新增到系统变量 `NODE_PATH`

		![[install-nodejs-5.png]]

   3. 在系统变量下的 `Path` 新建添加 `%NODE_PATH%` 和 node 全局文件夹 `node_global`

		![[install-nodejs-6.png]]

6. 修改镜像源，解决下载模块慢问题

   ```shell
   // 设置淘宝镜像源
   npm config set registry https://registry.npm.taobao.org

   // 查看使用的镜像源
   npm config get registry

   // 全局安装 cnpm
   npm install -g cnpm --registry=https://registry.npm.taobao.org
   ```

   ![[install-nodejs-7.png]]

### yarn

Yarn 是 facebook 发布的一款取代 npm 的包管理工具。

- Yarn 缓存了每个下载过的包，所以再次使用时无需重复下载。同时利用并行下载以最大化资源利用率，因此安装速度更快。
- 在执行代码之前，Yarn 会通过算法校验每个安装包的完整性。
- 使用详细、简洁的锁文件格式和明确的安装算法，Yarn 能够保证在不同系统上无差异的工作。

> [!link] Reference
> [yarn 的安装和使用](https://blog.csdn.net/LIZHUOLONG1/article/details/125534086)

**安装**

```shell
npm install -g yarn
```

**卸载**

```shell
npm uninstall -g yarn
```

**配置环境变量**

将 `bin/` 配置到 `Path`

![[install-nodejs-8.png]]

**常用命令**

```shell
yarn -v  // 查看 yarn 版本
yarn config list  // 查看 yarn 配置
yarn config get registry   // 查看当前 yarn 源

// 修改 yarn 源（此处为淘宝的源）
yarn config set registry https://registry.npm.taobao.org

// yarn 安装依赖
yarn add 包名          // 局部安装
yarn global add 包名   // 全局安装

// yarn 卸载依赖
yarn remove 包名         // 局部卸载
yarn global remove 包名  // 全局卸载（如果安装时安到了全局，那么卸载就要对应卸载全局的）

// yarn 查看全局安装过的包
yarn global list
```

```shell
npm install -g yarn  // 安装 yarn
yarn --version       // 安装成功后，查看版本号
md yarn   // 创建文件夹 yarn
cd yarn   // 进入 yarn 文件夹

初始化项目
yarn init // 同 npm init，执行输入信息后，会生成 package. json 文件

yarn 的配置项：
yarn config list // 显示所有配置项
yarn config get <key> //显示某配置项
yarn config delete <key> //删除某配置项
yarn config set <key> <value> [-g|--global] //设置配置项

安装包：
yarn install         //安装 package. json 里所有包，并将包及它的所有依赖项保存进 yarn. lock
yarn install --flat  //安装一个包的单一版本
yarn install --force         //强制重新下载所有包
yarn install --production    //只安装 dependencies 里的包
yarn install --no-lockfile   //不读取或生成 yarn. lock
yarn install --pure-lockfile //不生成 yarn. lock
添加包（会更新 package. json 和 yarn. lock）：

yarn add [package] // 在当前的项目中添加一个依赖包，会自动更新到 package. json 和 yarn. lock 文件中
yarn add [package]@[version] // 安装指定版本，这里指的是主要版本，如果需要精确到小版本，使用-E 参数
yarn add [package]@[tag] // 安装某个 tag（比如 beta, next 或者 latest）

//不指定依赖类型默认安装到 dependencies 里，你也可以指定依赖类型：
yarn add --dev/-D // 加到 devDependencies
yarn add --peer/-P // 加到 peerDependencies
yarn add --optional/-O // 加到 optionalDependencies

//默认安装包的主要版本里的最新版本，下面两个命令可以指定版本：
yarn add --exact/-E // 安装包的精确版本。例如 yarn add foo@1.2.3 会接受 1.9.1 版，但是 yarn add foo@1.2.3 --exact 只会接受 1.2.3版
yarn add --tilde/-T // 安装包的次要版本里的最新版。例如 yarn add foo@1.2.3 --tilde 会接受 1.2.9，但不接受 1.3.0

yarn publish // 发布包
yarn remove <packageName>  // 移除一个包，会自动更新 package. json 和 yarn. lock
yarn upgrade // 更新一个依赖: 用于更新包到基于规范范围的最新版本
yarn run   // 运行脚本: 用来执行在 package. json 中 scripts 属性下定义的脚本
yarn info <packageName> 可以用来查看某个模块的最新版本信息

缓存
yarn cache
yarn cache list # 列出已缓存的每个包
yarn cache dir # 返回全局缓存位置
yarn cache clean # 清除缓存
```

### 踩坑记录
- [解决 vite 不是内部或外部命令，也不是可运行的程序或批处理文件](https://blog.csdn.net/weixin_46713508/article/details/128211201)
- [npm install certificate has expired 证书过期的解决办法](https://www.cnblogs.com/gaoyanbing/p/18006744)