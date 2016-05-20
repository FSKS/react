# CodePush 命令行

CodePush是一个云服务，它能让Cordova和React Native的开发者将手机应用的更新直接部署到用户的设备上。
它担任类似中间仓库的角色，开发者可以把更新（JS，HTML，CSS和图片）发布到这个仓库上，然后那些Apps就能查询到更新了（那些集成了CodePush SDKs的[Cordova](http://github.com/Microsoft/cordova-plugin-code-push)和[React Native](http://github.com/Microsoft/react-native-code-push) 应用）。

这就让你可以与你的用户群有一个更确定且直接的交互模式，当你解决Bug或添加小功能时，不用重新构建二进制文件然后在各自的AppStore里重新发布。
（原文是：This allows you to have a more deterministic and direct engagement model with your user base, when addressing bugs and/or adding small features that don't require you to re-build a binary and re-distribute it through the respective app stores.）

![CodePush CLI](https://cloud.githubusercontent.com/assets/116461/14505396/c97bdc78-016d-11e6-89da-3f3557f8b33d.png)

* [安装](#安装)
* [快速开始](#快速开始)
* [创建账号](#创建账号)
* [身份认证](#身份认证)
* [应用管理](#应用管理)
* [应用合作](#应用合作)
* [部署管理](#部署管理)
* [发布更新](#发布更新)
    * [发布更新 (General)](#发布更新-general)
    * [发布更新 (React Native)](#发布更新-react-native)
    * [发布更新 (Cordova)](#发布更新-cordova)
* [补丁更新](#补丁更新)
* [促进更新](#促进更新)
* [回滚更新](#回滚更新)
* [查看发布历史](#查看发布历史)
* [清除发布历史](#清除发布历史)

## 安装

* 安装 [Node.js](https://nodejs.org/)
* 安装 CodePush CLI: `npm install -g code-push-cli`

## 快速开始

1. 使用CodePush CLI创建一个[CodePush 账号](#创建账号)。
2. 注册你的CodePush[应用](#应用管理), 并[分享](#应用合作)给你团队的其它开发者 。
3. 使用[Cordova插件](http://github.com/Microsoft/cordova-plugin-code-push) 或 [React Native插件](http://github.com/Microsoft/react-native-code-push))配置好CodePush。（原文：CodePush-ify your app and point it at the deployment you wish to use ([Cordova](http://github.com/Microsoft/cordova-plugin-code-push) and [React Native](http://github.com/Microsoft/react-native-code-push))）
4. [发布](#发布更新) 更新。
5. 活的长而成功！[详细资料](https://en.wikipedia.org/wiki/Vulcan_salute)（原文：Live long and prosper! ([details](https://en.wikipedia.org/wiki/Vulcan_salute))）

## 创建账号

在你发布应用更新之前，你需要创建一个CodePush帐号。一旦你安装了Cli你就可以简单的使用如下命令来注册：running the following command once you've installed the CLI:

```
code-push register
```

这将会启动浏览器，要求验证你的Github或微软帐号。一旦验证成功，它将创建一个CodePush帐号跟你的Github或MSA相连，而且生成一个Access Key，你可以拷贝/粘贴到CLI以便登录。

*注意：注册成功后，你就已经自动登录了。所以除非你明确登出了，否则你不需要在此机器上再次登录。*

如果你有一个已存在的帐号，你还可以把你的帐号跟另一个帐号关联起来，通过运行：

```
code-push link
```

注意：为了实现这个，在供应商使用的Email地址必须与你存在的帐号匹配。
（原文：Note that in order to do this, the email address used on the provider must match the one on your existing account.）

## 身份认证

在CodePush CLI里大多数命令需要身份认证，所以在你开始管理你的帐号之前，你需要使用GitHub或者微软帐号注册和登录。你可以通过执行如下命令做到这些：

```
code-push login
```

这将会启动浏览器，要求验证你的Github或微软帐号。这将生成一个Access Key，然后你可以拷贝/粘贴到CLI（它会提示你这样做）。这时你就认证成功了，并且可以关掉你的浏览器了。


如果在任何时候你想确认你是否已经登录了，你可以运行如下命令来显示与你当前认证会话相关的e-mail帐号，而且这个身份提供者是连接到（如：GitHub）的。

```shell
code-push whoami
```

当你从CLI登录后，你的Access Key就一直保存在你本地磁盘上，所以你不必每次使用帐号是都需要登录。为了结束或删除AccessKey，你可以简单的运行如下命令：

```
code-push logout
```
如果你在一台机器上忘记登录（比如：你朋友的电脑上），你可以使用如下命令列出和删除任何“有效”的Access Keys。Access Keys列表将显示创建Key的机器名和发生登录的时间。这让你可以简单的认出那些你不想要保存的Keys。

```
code-push access-key ls
code-push access-key rm <accessKey>
```

如果你需要额外的Keys,被用来验证CodePush服务而不需要给你的GitHub和/或访问微软凭证,您可以运行下面的命令来创建一个持久的Access Key(连同一个描述):
（原文是：If you need additional keys that can be used to authenticate against the CodePush service without needing to give access to your GitHub and/or Microsoft credentials, you can run the following command to create a persistent one (along with a description of what it is for):）

```
code-push access-key add "VSTS Integration"
```
在创建新的密钥之后,您可以在`login`命令后使用`--accessKey`标志并指定其值，它允许您执行“无头”身份验证,而不是启动一个浏览器。
（原文：After creating the new key, you can specify its value using the `--accessKey` flag of the `login` command, which allows you to perform "headless" authentication, as opposed to launching a browser.）

```
code-push login --accessKey <accessKey>
```
当使用这种方式登录时，密钥(Access Key)在注销时不会自动失效，它可以一直被使用，除非它从CodePush服务端明确被移除掉。
然而，仍然建议一旦你完成了会话就注销掉，以便从本地磁盘移除掉你的授权。（原文：However, it is still recommended to log out once your session is complete, in order to remove your credentials from disk.）

## 应用管理

在你发布更新前，你需要用如下命令在CodePush服务上注册一个App：

```
code-push app add <appName>
```

如果你的App既有IOS又有Android，我们推荐你创建单独的App。一个平台一个。这样你可以单独的管理和发布更新，从长远来看这会让事情更简单。大部分人的命名约定会在App名加后缀`-IOS`和`-Android`。例如：

```
code-push app add MyApp-Android
code-push app add MyApp-iOS
```

所有新的Apps自动会出现有个部署环境（`Staging`和`Production`），为了你可以开始发布更新到不同的渠道而不需要做任何其它的事（参考下面的部署指南）。你创建一个App之后，CLI将显示`Staging`和`Production`环境的开发密钥，你就可以使用不同的SDKs(详细请看[Cordova](http://github.com/Microsoft/cordova-plugin-code-push) 和 [React Native](http://github.com/Microsoft/react-native-code-push))来配置你的手机端App了。

如果你不喜欢你之前取的名字，你还可以随时重命名它，使用如下命令：

```
code-push app rename <appName> <newAppName>
```

应用的名字从管理方面看只是为了能辨识，因此，必要时可以随时重命名它。它其实不会影响正在运行的应用程序，因为更新的查询都是通过部署密钥的。

如果你不想要一个App，你可以从服务端上移除它，命令如下：

```
code-push app rm <appName>
```

做这个移除请务必小心，因为任何配置了它的App都将停止收到更新了。

最后，如果你想列出你在CodePush服务上注册的所有Apps，你可以运行如下命令：

```
code-push app ls
```

## 应用合作

如果你讲和其它开发者在一起合作同一个CodePush应用，你可以把他们添加为合作者，使用如下命令：

```shell
code-push collaborator add <appName> <collaboratorEmail>
```

*注意： 这个期待开发者已经用e-mail[注册](#创建账号)了CodePush，所以在打算分享应用之前确保他们已经准备好了一切。*

一旦添加了，所有的合作者将立即拥有了最新分享App的如下权限：
1. 查看App，它的合作者，[部署管理](#部署管理)和[查看发布历史](#查看发布历史)。
1. [发布](#发布更新)更新到任何应用的部署环境。
1. [促进](#促进更新)更新在任何应用部署环境之间。
1. [回滚](#回滚更新)任何应用部署。
1. [打补丁](#补丁更新)在任何应用部署里。

相反的，这就意味着一个合作者不能做任何如下的事情：

1. 重命名或删除应用。
1. 转让应用的所有权。
1. 创建，重命名或删除新的部署环境。
1. 清除一个部署历史。
1. 添加或删除合作者。

*注意：一个合作的开发者可以移除他/她自己。*

随着时间的推移，如果有人不再和你一起合作，那你可以解除合作者关系，使用如下命令：

```shell
code-push collaborator rm <appName> <collaboratorEmail>
```

如果你想列出应用的所有合作者，你可以简单的运行如下命令：

```shell
code-push collaborator ls <appName>
```

最后，如果在某刻你（作为App的拥有者）将不再开发App了，你想转让给其他开发者（或客户），你可以运行如下命令：

```shell
code-push app transfer <appName> <newOwnerEmail>
```

*注意：就像`code-push collaborator add`命令一样，这期望新的拥有者已经用指定的e-mail注册了CodePush。*

一经确认，该指定的开发者成为App的拥有者，而且立即接收到该角色的相关权限。除了拥有权转移外，其它的任何都没有被修改（比如：部署环境，发布历史，合作者）。这意味着你还仍然是该App的一个合作者，所以如果你想移除你自己，那你可以在成功转让拥有全后简单的运行`code-push collaborator rm`命令。 

## 部署管理

从CodePush的角度来看，一个应用把一个或更多的东西简单命名分组称为“部署(环境)”。


While the app represents a conceptual "namespace" or "scope" for a platform-specific version of an app (e.g. the iOS port of Foo app), its deployments represent the actual target for releasing updates (for developers) and synchronizing updates (for end-users). Deployments allow you to have multiple "environments" for each app in-flight at any given time, and help model the reality that apps typically move from a dev's personal environment to a testing/QA/staging environment, before finally making their way into production.

*注意: 正如你将在下面看到的`release`（发布），`promote`（提升），`rollback`（回滚）命令需要应用名字和部署名字，因为这两个组成一个独特的发布标识（例如：我想发布更新到我的IOS应用给beta环境的测试者们）。*

当一个用CodePush服务注册的应用，它默认包含两个部署环境：`Staging`和`Production`。这让你可以理解发布更新到一个内部的环境，你可以在推送到终端用户之前彻底的测试每个更新。这个工作流是至关重要的，以确保你的版本准备好给大众，而且这是一个在Web上实践很久的惯例。

如果你的App有`Staging`和`Production`环境其实已经满足了你的需求，然后你不需要做任何事情。不过，如果你需要alpha，dev等部署环境，那你可以简单的使用如下命令创建：

```
code-push deployment add <appName> <deploymentName>
```

就像Apps一样，你也可以删除或重命名部署环境，分别使用如下命令：

```
code-push deployment rm <appName> <deploymentName>
code-push deployment rename <appName> <deploymentName> <newDeploymentName>
```

你可以在任何时候查看特定应用包含的部署环境列表，你可以简单的运行下面的命令：

```
code-push deployment ls <appName> [--displayKeys|-k]
```

这将不仅显示部署环境列表，而且还有元数据（例如：强制性属性，描述）和最新版本的安装指标：

![Deployment lis](https://cloud.githubusercontent.com/assets/116461/12526883/7730991c-c127-11e5-9196-98e9ceec758f.png)

*注意: 因为他们很少用和需要屏幕，部署密钥默认是不显示的。如果你需要查看它们，只要在`deployment ls`命令后面加上`-k`标识即可。*

安装指标有如下意义：

* **Active（激活）** - 成功安装的数量目前运行这个版本。这个数字将会随着用户更新到或离开这个版本分别增加或减少。

* **Total** - 该版本更新收到的所有成功安装的总数。这个数字只会随新用户/设备安装它而增加，所以它是__激活__的超集。

* **Pending** - 更新被下载了但还没安装的数量。This would only apply to updates that aren't installed immediately, and helps provide the broader picture of release adoption for apps that rely on app resume and restart to apply an update.

* **Rollbacks** - 该版本被自动回滚的次数。理想情况下这个数应该为0，而且在这种情况下这个量是不会显示的。然而，如果你发布了一个包含严重问题(Crash)的更新，CodePush插件将在安装时回滚到上一个版本，同时把问题反馈到服务端。这可以让终端用户依旧能用，不被损坏的版本阻塞住，而且能够在CLI里看到这些，你可以鉴定错误的版本并且能在服务器上做出[回滚](#回滚更新)的响应。

* **Rollout** - 显示有资格接收更新的百分比。这个属性只会被显示在那些`激活的`的首次展示的版本，所以，首次展示百分比是小于100%。此外， 因为一个部署任何时候只能有一个激活的首次展示，这个标签只会被显示在最新的一次部署里。

* **Disabled** - 标示是否该版本被标记成失效的，因此用户是否可下载。这个属性只有在版本真实失效时才显示。

当度量(metrics)单元格统计为`No installs recorded`（无安装记录），那是表示这个版本在服务器上没有任何活动记录。这可能要么是因为被插件阻止了，或者用户还没有跟CodePush服务器同步。一旦发生了安装，你将在CLI里看到该版本的度量。

## 发布更新

一旦你的App被配置了从CodePush服务器查询版本更新，你就可以向它开始发布。为了简易性和灵活性，CodePush CLI包含三种不同的发布命令：

1. [通用](#发布更新-general) - 使用外部的工具或构建脚本（如：Gulp任务，`react-native bundle`命令）像CodePush服务器发布一个更新。这对装配进目前的工作流而言提供最灵活的方式，因为它严格按CodePush特性的步骤处理，而把App特性的编译过程留给你。

2. [React Native](#发布更新-react-native) - 跟通用发布命令一样执行相同的功能，但是还会为你生成的应用更新内容(JS包和资源)，而不需要你运行`react-native bundle`，然后执行`code-push release`。

3. [Cordova](#releasing-updates-cordova) - 跟通用发布命令一样执行相同的功能，但也会为你处理准备应用更新的任务，而不需要你运行`cordova prepare`，然后执行`code-push release`。

你应该使用哪个命令主要是一种需求或偏好的事。然而，我们通常推荐使用相关的特定平台的命令开始(因为它大大简化了体验)，然后当有更大控制必要时用通用的`release`命令。

### 发布更新 (General)

```
code-push release <appName> <updateContents> <targetBinaryVersion>
[--deploymentName <deploymentName>]
[--description <description>]
[--disabled <disabled>]
[--mandatory]
[--rollout <rolloutPercentage>]
```

#### App name (应用名)参数

指定将发布更新的CodePush 应用名。这个与最初你调用`code-push app add`（如："MyApp-Android"）的名字保持一致。如果你想查一下，可以运行`code-push app ls`命令看看应用的列表。

#### Update contents (更新内容)参数

指定应用更新的代码和资源位置。你可以提供要么一个单独文件（如：React Native的JS bundle文件），或者一个文件夹路径（如：Cordova应用的`/platforms/ios/www`文件夹）。注意你不需要为了部署更新而对文件或文件夹进行Zip压缩，因为CLI会帮你自动ZIP压缩。

重要的是你指定的路径是跟特定平台相关的，准备/打包你的应用。下面表格概括了在发布前你应该运行哪个命令，以及你以后可以参考的`updateContents` 参数路径:

| 平台                         | 准备命令（Prepare command ） | 包的路径 (相对项目的根目录) |
|-------------------------------------|--------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| Cordova (Android)             | `cordova prepare android`                    | `./platforms/android/assets/www` 目录                                                                 |
| Cordova (iOS)                    | `cordova prepare ios`                                  | `./platforms/ios/www ` 目录                                                                      |
| React Native wo/assets (Android) | `react-native bundle --platform android --entry-file <entryFile> --bundle-output <bundleOutput> --dev false`                                               |  `--bundle-output` 参数的值                                                                      |
| React Native w/assets (Android)  | `react-native bundle --platform android --entry-file <entryFile> --bundle-output <releaseFolder>/<bundleOutput> --assets-dest <releaseFolder> --dev false` |  `--assets-dest` 参数的值，应该是一个包含资源和JS bundle的新创建的目录。|
| React Native wo/assets (iOS)    | `react-native bundle --platform ios --entry-file <entryFile> --bundle-output <bundleOutput> --dev false`   | `--bundle-output` 参数的值  |
| React Native w/assets (iOS)      | `react-native bundle --platform ios --entry-file <entryFile> --bundle-output <releaseFolder>/<bundleOutput> --assets-dest <releaseFolder> --dev false` | `--assets-dest` 参数的值，应该是一个包含资源和JS bundle的新创建的目录。|

#### Target binary version (目标二进制版本)参数

这是你想发布更新的特定仓库/二进制版本，这样只有那个版本上的用户才会接收到更新，而那些运行较老/新版本用户则不会。这样很有用，原因如下：

1. 如果有用户运行一个很老的版本，有可能在CodePush的更新里有个破坏性的更新，这跟他们现在运行的版本不兼容。

2. 如果用户正在运行一个新的二进制版本，那么假定，他们正在运行并更新CodePush 更新（可能不兼容）。

如果你想更新应用商店里二进制文件的多个版本，我们允许你指定参数像这样[语义版本范围表达式](https://github.com/npm/node-semver#advanced-range-syntax)。这样， 任何在版本号范围内（如：`semver.satisfies(version, range)` returns `true`）的客户端设备都能获得更新。

如下是有效的版本号范围表达式的例子：

| 范围表达式 | 谁获得更新                                                                    |
|------------------|----------------------------------------------------------------------------------------|
| `1.2.3`          | 只有`1.2.3`版本  |
| `*`                 | 所有版本                    |
| `1.2.x`          | 主版本为1，小版本为2的任何版本  |
| `1.2.3 - 1.2.7`  | 在 `1.2.3` (包含) 和 `1.2.7` (包含) 之间的版本 |
| `>=1.2.3 <1.2.7` | 在 `1.2.3` (包含) 和 `1.2.7` (不包含)之间的版本 |
| `~1.2.3`         | 相当于`>=1.2.3 <1.3.0`                                                         |
| `^1.2.3`         | 相当于`>=1.2.3 <2.0.0`                                                         |

*注意：如果语义表达式以特殊字符开始如`>`,`^`或***，如果你没有用引号括起来的话命令可能执行不对，因为shell在CLI里不支持右边的值。所以，当调用`release`命令时最好能把你的`targetBinaryVersion`参数用双引号括起来，如：`code-push release MyApp updateContents ">1.2.3"`。*

如下表格分别概括了每个应用类型的CodePush更新的语义版本范围的版本值：
| 平台               | 应用商店版本来源 |
|------------------------|------------------------------------------------------------------------------|
| Cordova                | 在`config.xml`文件里的`<widget version>` 属性   |
| React Native (Android) | 在`build.gradle`文件里 `android.defaultConfig.versionName` 属性 |
| React Native (iOS)     | 在`Info.plist`文件里的`CFBundleShortVersionString` 键          |
| React Native (Windows) | 在`Package.appxmanifest`文件的 `<Identity Version>` 键     |

*注意：如果在元数据文件里的应用版本号漏掉补丁版本值，如`2.0`，它将被当成补丁版本值为`0`，如：`2.0 当成 2.0.0`.*

#### Deployment name (部署环境名)参数

这是你想发布更新到的那个指定部署环境名。默认为`Staging`(临时环境)，但是当你准备部署到`Production`(生产环境)或一个你自定义的部署环境时，你只要指明设置这个参数即可。

*注意：这个参数可以用"--deploymentName" 或 "-d"来设置。*

#### Description (描述)参数

给部署提供一个可选的"更新日志"。当被检测到有更新时这个值就会完整的传到客户端，所以你的应用可以选择显示给终端用户（如：通过一个`哪些新东西？`的对话框）。这个字符串可以接受控制字符如`\n` 和 `\t`，以便你可以包含空白格式在你的描述里来提高可读性。

*注意：这个参数可以用"--description" 或 "-desc"来设置。*

#### Mandatory (强制性)参数

这个标识该更新是否是强制性的（如：包含一个严重的安全修复）。这个属性简单的传到客户端，然后客户端决定是否要强制更新。

*注意: 这个参数是简单的一个"标记"，所以，没有该标记表示版本更新可选，如果有标记则表示版本是强制更新的。你可以给它赋值（如：`--mandatory true`)，但其实简单的`--mandatory`就已能标识强制更新了。*

强制属性是唯一的，因为服务端必要时将动态修改它，为了确保你对终端用户的版本更新语义上的维护。例如：设想你的应用有如下3个更新：

| 版本 | 强制？ |
|---------|------------|
| v1      | No         |
| v2      | Yes        |
| v3      | No         |

如果用户当前是`v1`版本，然后从服务端查询更新，将以`v3`（因为这是最新的）响应，但是它将动态将这个版本转变成强制的，因为中间有一个强制更新的版本。这个行为很重要因为`v3`的代码是在`v2`上增加的，所以任何没有获取`v2`版本的都会不管`v2`的强制，而继续让`v3`变成强制更新版本。

If an end-user is currently running `v2`, and they query the server for an update, it will respond with `v3`, but leave the release as optional. This is because they already received the mandatory update, and therefore, there isn't a need to modify the policy of `v3`. This behavior is why we say that the server will "dynamically convert" the mandatory flag, because as far as the release goes, its mandatory attribute will always be stored using the value you specified when releasing it. It is only changed on-the-fly as necessary when responding to an update check from an end-user.

If you never release an update that is marked as mandatory, then the above behavior doesn't apply to you, since the server will never change an optional release to mandatory unless there were intermingled mandatory updates as illustrated above. Additionally, if a release is marked as mandatory, it will never be converted to optional, since that wouldn't make any sense. The server will only change an optional release to mandatory in order to respect the semantics described above.

*NOTE: This 参数 can be set using either `--mandatory` or `-m`*

#### Rollout 参数

**IMPORTANT: In order for this 参数 to actually take affect, your end users need to be running version `1.6.0-beta+` (for Cordova) or `1.9.0-beta+` (for React Native) of the CodePush plugin. If you release an update that specifies a rollout property, no end user running an older version of the Cordova or React Native plugins will be eligible for the update. Therefore, until you have adopted the neccessary version of the CodePush SDK, we would advise not setting a rollout value on your releases, since no one would end up receiving it.**

This specifies the percentage of users (as an integer between `1` and `100`) that should be eligible to receive this update. It can be helpful if you want to "flight" new releases with a portion of your audience (e.g. 25%), and get feedback and/or watch for exceptions/crashes, before making it broadly available for everyone. If this 参数 isn't set, it is set to `100%`, and therefore, you only need to set it if you want to actually limit how many users will receive it.

 When leveraging the rollout capability, there are a few additional considerations to keep in mind:

1. You cannot release a new update to a deployment whose latest release is an "active" rollout (i.e. its rollout property is non-null). The rollout needs to be "completed" (i.e. setting the `rollout` property to `100`) before you can release further updates to the deployment.

2. If you rollback a deployment whose latest release is an "active" rollout, the rollout value will be cleared, effectively "deactivating" the rollout behavior

3. Unlike the `mandatory` and `description` fields, when you promote a release from one deployment to another, it will not propagate the `rollout` property, and therefore, if you want the new release (in the target deployment) to have a rollout value, you need to explicitly set it when you call the `promote` command.

*NOTE: This 参数 can be set using either `--rollout` or `-r`*

#### Disabled 参数

This specifies whether an update should be downloadable by end users or not. If left unspecified, the update will not be disabled (i.e. users will download it the moment your app calls `sync`). This 参数 can be valuable if you want to release an update that isn't immediately available, until you expicitly [patch it](#patching-releases) when you want end users to be able to download it (e.g. an announcement blog post went live).

*NOTE: This 参数 can be set using either "--disabled" or "-x"*

### 发布更新 (React Native)

```shell
code-push release-react <appName> <platform>
[--bundleName <bundleName>]
[--deploymentName <deploymentName>]
[--description <description>]
[--development <development>]
[--disabled <disabled>]
[--entryFile <entryFile>]
[--mandatory]
[--sourcemapOutput <sourcemapOutput>]
[--targetBinaryVersion <targetBinaryVersion>]
[--rollout <rolloutPercentage>]
```

The `release-react` command is a React Native-specific version of the "vanilla" [`release`](#releasing-app-updates) command, which supports all of the same 参数s (e.g. `--mandatory`, `--description`), yet simplifies the process of releasing updates by performing the following additional behavior:

1. Running the `react-native bundle` command in order to generate the [update contents](#update-contents-参数) (JS bundle and assets) that will be released to the CodePush server. It uses sensible defaults as much as possible (e.g. creating a non-dev build, assuming an iOS entry file is named `index.ios.js`), but also exposes the relevant `react-native bundle` 参数s to enable flexibility (e.g. `--sourcemapOutput`).

2. Inferring the [`targetBinaryVersion`](#target-binary-version-参数) of this release by using the version name that is specified in your project's `Info.plist` (for iOS) and `build.gradle` (for Android) files.

To illustrate the difference that the `release-react` command can make, the following is an example of how you might generate and release an update for a React Native app using the "vanilla" `release` command:

```shell
mkdir ./CodePush

react-native bundle --platform ios \
--entry-file index.ios.js \
--bundle-output ./CodePush/main.jsbundle \
--assets-dest ./CodePush \
--dev false

code-push release MyApp ./CodePush 1.0.0
```

Achieving the equivalent behavior with the `release-react` command would simply require the following command, which is generally less error-prone:

```shell
code-push release-react MyApp ios
```

*NOTE: We believe that the `release-react` command should be valuable for most React Native developers, so if you're finding that it isn't flexible enough or missing a key feature, please don't hesistate to [let us know](mailto:codepushfeed@microsoft.com), so that we can improve it.*

#### App name 参数

This is the same 参数 as the one described in the [above section](#app-name-参数).

#### Platform 参数

This specifies which platform the current update is targeting, and can be either `android`, `ios` or `windows` (case-insensitive).

#### Deployment name 参数

This is the same 参数 as the one described in the [above section](#deployment-name-参数).

#### Description 参数

This is the same 参数 as the one described in the [above section](#description-参数).

#### Mandatory 参数

This is the same 参数 as the one described in the [above section](#mandatory-参数).

#### Rollout 参数

This is the same 参数 as the one described in the [above section](#rollout-参数). If left unspecified, the release will be made available to all users.

#### Target binary version 参数

This is the same 参数 as the one described in the [above section](#target-binary-version-参数). If left unspecified, this defaults to targeting the exact version specified in the app's `Info.plist` (for iOS) and `build.gradle` (for Android) files.

#### Disabled 参数

This is the same 参数 as the one described in the [above section](#disabled-参数).

#### Development 参数

This specifies whether to generate a unminified, development JS bundle. If left unspecified, this defaults to `false` where warnings are disabled and the bundle is minified.

*NOTE: This 参数 can be set using either --develoment or --dev*

#### Entry file 参数

This specifies the relative path to the app's root/entry JavaScript file. If left unspecified, this defaults to `index.ios.js` (for iOS),  `index.android.js` (for Android) or `index.windows.bundle` (for Windows) if that file exists, or `index.js` otherwise.

*NOTE: This 参数 can be set using either --entryFile or -e*

#### Bundle name 参数

This specifies the file name that should be used for the generated JS bundle. If left unspecified, the standard bundle name will be used for the specified platform: `main.jsbundle` (iOS), `index.android.bundle` (Android) and `index.windows.bundle` (Windows).

*NOTE: This 参数 can be set using either --bundleName or -b*

#### Sourcemap output 参数

This specifies the relative path to where the generated JS bundle's sourcemap file should be written. If left unspecified, sourcemaps will not be generated.

*NOTE: This 参数 can be set using either --sourcemapOutput or -s*

### 发布更新 (Cordova)

```shell
code-push release-cordova <appName> <platform>
[--deploymentName <deploymentName>]
[--description <description>]
[--mandatory]
[--targetBinaryVersion <targetBinaryVersion>]
[--rollout <rolloutPercentage>]
[--build]
```

The `release-cordova` command is a Cordova-specific version of the "vanilla" [`release`](#releasing-app-updates) command, which supports all of the same 参数s (e.g. `--mandatory`, `--description`), yet simplifies the process of releasing updates by performing the following additional behavior:

1. Running the `cordova prepare` command in order to generate the [update contents](#update-contents-参数) (`www` folder) that will be released to the CodePush server.

2. Inferring the [`targetBinaryVersion`](#target-binary-version-参数) of this release by using the version name that is specified in your project's `config.xml` file.

To illustrate the difference that the `release-cordova` command can make, the following is an example of how you might generate and release an update for a Cordova app using the "vanilla" `release` command:

```shell
cordova prepare ios
code-push release MyApp ./platforms/ios/www 1.0.0
```

Achieving the equivalent behavior with the `release-cordova` command would simply require the following command, which is generally less error-prone:

```shell
code-push release-cordova MyApp ios
```

*NOTE: We believe that the `release-cordova` command should be valuable for most Cordova developers, so if you're finding that it isn't flexible enough or missing a key feature, please don't hesistate to [let us know](mailto:codepushfeed@microsoft.com), so that we can improve it.*

#### App name 参数

This is the same 参数 as the one described in the [above section](#app-name-参数).

#### Platform 参数

This specifies which platform the current update is targeting, and can be either `ios` or `android` (case-insensitive).

#### Deployment name 参数

This is the same 参数 as the one described in the [above section](#deployment-name-参数).

#### Description 参数

This is the same 参数 as the one described in the [above section](#description-参数).

#### Mandatory 参数

This is the same 参数 as the one described in the [above section](#mandatory-参数).

#### Rollout 参数

This is the same 参数 as the one described in the [above section](#rollout-参数). If left unspecified, the release will be made available to all users.

#### Target binary version 参数

This is the same 参数 as the one described in the [above section](#target-binary-version-参数). If left unspecified, the command defaults to targeting only the specified version in the project's metadata (`Info.plist` if this update is for iOS clients, and `build.gradle` for Android clients).

#### Disabled 参数

This is the same 参数 as the one described in the [above section](#disabled-参数).

#### Build 参数

Specifies whether you want to run `cordova build` instead of `cordova prepare` (which is the default behavior), when generating your updated web assets. This is valuable if your project includes before and/or after build hooks (e.g. to transpile TypeScript), and therefore, having CodePush simply run `cordova prepare` isn't sufficient to create and release an update. If left unspecified, this defaults to `false`.

*NOTE: This 参数 can be set using either --build or -b*

## 补丁更新

After releasing an update, there may be scenarios where you need to modify one or more of the attributes associated with it (e.g. you forgot to mark a critical bug fix as mandatory, you want to increase the rollout percentage of an update). You can easily do this by running the following command:

```shell
code-push patch <appName> <deploymentName>
[--label <releaseLabel>]
[--mandatory <isMandatory>]
[--description <description>]
[--rollout <rolloutPercentage>]
[--targetBinaryVersion <targetBinaryVersion>]
```

Aside from the `appName` and `deploymentName`, all 参数s are optional, and therefore, you can use this command to update just a single attribute or all of them at once. Calling the `patch` command without specifying any attribute flag will result in a no-op.

```shell
# Mark the latest production release as mandatory
code-push patch MyApp Production -m

# Increase the rollout for v23 to 50%
code-push patch MyApp Production -l v23 -rollout 50%
```

### Label 参数

Indicates which release (e.g. `v23`) you want to update within the specified deployment. If ommitted, the requested changes will be applied to the latest release in the specified deployment. In order to look up the label for the release you want to update, you can run the `code-push deployment history` command and refer to the `Label` column.

*NOTE: This 参数 can be set using either `--label` or `-l`*

### Mandatory 参数

This is the same 参数 as the one described in the [above section](#mandatory-参数), and simply allows you to update whether the release should be considered mandatory or not. Note that `--mandatory` and `--mandatory true` are equivalent, but the absence of this flag is not equivalent to `--mandatory false`. Therefore, if the 参数 is ommitted, no change will be made to the value of the target release's mandatory property. You need to set this to `--mandatory false` to explicitly make a release optional.

### Description 参数

This is the same 参数 as the one described in the [above section](#description-参数), and simply allows you to update the description associated with the release (e.g. you made a typo when releasing, or you forgot to add a description at all). If this 参数 is ommitted, no change will be made to the value of the target release's description property.

### Disabled 参数

This is the same 参数 as the one described in the [above section](#disabled-参数), and simply allows you to update whether the release should be disabled or not. Note that `--disabled` and `--disabled true` are equivalent, but the absence of this flag is not equivalent to `--disabled false`. Therefore, if the paremeter is ommitted, no change will be made to the value of the target release's disabled property. You need to set this to `--disabled false` to explicity make a release acquirable if it was previously disabled.

### Rollout 参数

This is the same 参数 as the one described in the [above section](#rollout-参数), and simply allows you to increase the rollout percentage of the target release. This 参数 can only be set to an integer whose value is greater than the current rollout value. Additionally, if you want to "complete" the rollout, and therefore, make the release available to everyone, you can simply set this 参数 to `--rollout 100`. If this 参数 is ommitted, no change will be made to the value of the target release's rollout 参数.

Additionally, as mentioned above, when you release an update without a rollout value, it is treated equivalently to setting the rollout to `100`. Therefore, if you released an update without a rollout, you cannot change the rollout property of it via the `patch` command since that would be considered lowering the rollout percentage.

### Target binary version 参数

This is the same 参数 as the one described in the [above section](#target-binary-version-参数), and simply allows you to update the semver range that indicates which binary version(s) a release is compatible with. This can be useful if you made a mistake when originally releasing an update (e.g. you specified `1.0.0` but meant `1.1.0`) or you want to increase or decrease the version range that a release supports (e.g. you discovered that a release doesn't work with `1.1.2` after all). If this paremeter is ommitted, no change will be made to the value of the target release's version property.

```shell
# Add a "max binary version" to an existing release
# by scoping its eligibility to users running >= 1.0.5
code-push patch MyApp Staging -t "1.0.0 - 1.0.5"
```

## 促进更新

Once you've tested an update against a specific deployment (e.g. `Staging`), and you want to promote it "downstream" (e.g. dev->staging, staging->production), you can simply use the following command to copy the release from one deployment to another:

```
code-push promote <appName> <sourceDeploymentName> <destDeploymentName>
[--description <description>]
[--disabled <disabled>]
[--mandatory]
[--rollout <rolloutPercentage>]
[--targetBinaryVersion <targetBinaryVersion]
```

The `promote` command will create a new release for the destination deployment, which includes the **exact code and metadata** (description, mandatory and target binary version) from the latest release of the source deployment. While you could use the `release` command to "manually" migrate an update from one environment to another, the `promote` command has the following benefits:

1. It's quicker, since you don't need to reassemble the release assets you want to publish or remember the description/app store version that are associated with the source deployment's release.

2. It's less error-prone, since the promote operation ensures that the exact thing that you already tested in the source deployment (e.g. `Staging`) will become active in the destination deployment (e.g. `Production`).

We recommend that all users take advantage of the automatically created `Staging` and `Production` environments, and do all releases directly to `Staging`, and then perform a `promote` from `Staging` to `Production` after performing the appropriate testing.

### Description 参数

This is the same 参数 as the one described in the [above section](#description-参数), and simply allows you to override the description that will be used for the promoted release. If unspecified, the new release will inherit the description from the release being promoted.

#### Disabled 参数

This is the same 参数 as the one described in the [above section](#disabled-参数), and simply allows you to override the value of the disabled flag that will be used for the promoted release. If unspecified, the new release will inherit the disabled property from the release being promoted.

### Mandatory 参数

This is the same 参数 as the one described in the [above section](#mandatory-参数), and simply allows you to override the mandatory flag that will be used for the promoted release. If unspecified, the new release will inherit the mandatory property from the release being promoted.

### Rollout 参数

This is the same 参数 as the one described in the [above section](#rollout-参数), and allows you to specify whether the newly created release should only be made available to a portion of your users. Unlike the other release metadata 参数s (e.g. `description`), the `rollout` of a release is not carried over/inherited as part of a promote, and therefore, you need to explicitly set this if you don't want the newly created release to be available to all of your users.

### Target binary version 参数

This is the same 参数 as the one described in the [above section](#target-binary-version-参数), and simply allows you to override the target binary version that will be used for the promoted release. If unspecified, the new release will inherit the target binary version property from the release being promoted.

```shell
# Promote the release to production and make it 
# available to all versions using that deployment
code-push promote MyApp Staging Production -t "*"
```

## 回滚更新

A deployment's release history is immutable, so you cannot delete or remove an update once it has been released. However, if you release an update that is broken or contains unintended features, it is easy to roll it back using the `rollback` command:

```
code-push rollback <appName> <deploymentName>
code-push rollback MyApp Production
```

This has the effect of creating a new release for the deployment that includes the **exact same code and metadata** as the version prior to the latest one. For example, imagine that you released the following updates to your app:

| Release | Description       | Mandatory |
|---------|-------------------|-----------|
| v1      | Initial release!  | Yes       |
| v2      | Added new feature | No        |
| v3      | Bug fixes         | Yes       |

If you ran the `rollback` command on that deployment, a new release (`v4`) would be created that included the contents of the `v2` release.

| Release                     | Description       | Mandatory |
|-----------------------------|-------------------|-----------|
| v1                          | Initial release!  | Yes       |
| v2                          | Added new feature | No        |
| v3                          | Bug fixes         | Yes       |
| v4 (Rollback from v3 to v2) | Added new feature | No        |

End-users that had already acquired `v3` would now be "moved back" to `v2` when the app performs an update check. Additionally, any users that were still running `v2`, and therefore, had never acquired `v3`, wouldn't receive an update since they are already running the latest release (this is why our update check uses the package hash in addition to the release label).

If you would like to rollback a deployment to a release other than the previous (e.g. `v3` -> `v2`), you can specify the optional `--targetRelease` 参数:

```
code-push rollback MyApp Production --targetRelease v34
```

*NOTE: The release produced by a rollback will be annotated in the output of the `deployment history` command to help identify them more easily.*

## Viewing Release History

You can view a history of the 50 most recent releases for a specific app deployment using the following command:

```
code-push deployment history <appName> <deploymentName>
```

The history will display all attributes about each release (e.g. label, mandatory) as well as indicate if any releases were made due to a promotion or a rollback operation.

![Deployment History](https://cloud.githubusercontent.com/assets/696206/11605068/14e440d0-9aab-11e5-8837-69ab09bfb66c.PNG)

Additionally, the history displays the install metrics for each release. You can view the details about how to interpret the metric data in the documentation for the `deployment ls` command above.

By default, the history doesn't display the author of each release, but if you are collaborating on an app with other developers, and want to view who released each update, you can pass the additional `--displayAuthor` (or `-a`) flag to the history command.

*NOTE: The history command can also be run using the "h" alias*

## Clearing Release History

You can clear the release history associated with a deployment using the following command:

```
code-push deployment clear <appName> <deploymentName>
```

After running this command, client devices configured to receive updates using its associated deployment key will no longer receive the updates that have been cleared. This command is irreversible, and therefore should not be used in a production deployment.
