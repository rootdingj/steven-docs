

# Gitee+PicGo+Typora 让你的写作更丝滑

Markdown 博客将图片和博文放在 GitHub 的同一个仓库中，然后采用相对路径访问图片，会碰到一些问题：

- 提交到仓库中的图片会占用一定的空间，日积月累仓库变得臃肿起来了，能不能把图片分离开？
- 想要一篇博文同时发在 CSDN、博客园、简书、掘金等平台，但上传图片太麻烦了，怎样才能图片一次上传，处处引用？

**图床**刚好解决上述问题，下面是我的方案以及理由。

## Gitee 图床

在网上有许多搭建图床的方案，如：腾讯云 COS、Github 图床（Gitee 图床思路相同） 七牛云图床、阿里云 OSS 等。

看了网上的一些帖子后，我从访问速度、稳定性、费用、可操作性等方面考虑，最后决定选用 Gitee 图床：

- 首先，我写博客为了量化学习或者说为了更好的学习，应该选一个永久性免费、速度还行、比较稳定的；
- 其次，像七牛云，是需要绑定自定义域名的，域名还是需要备案的（否则境内 ip 无法访问），操作门槛较高；
- 最后，我选择 Gitee（码云，国内最大的代码托管的工具）而不是 Github 的原因有两个：就国内访问稳定性而言 Gitee 更好；不想把GitHub 的 Contribution 表格给画花了。

### 创建图床仓库

没有 Gitee 账号的，可以先注册账号，[Gitee 官网](https://gitee.com/)。

- 创建仓库作为图床，用于存储图片

  ![image-20220219234150584](https://gitee.com/rootdingj/pichost/raw/master/blog/2022/02/image-20220219234150584.png)

  *创建仓库后，记得将仓库设置为开源（所有人可见），点击仓库名，然后点击【管理】即可编辑。*

- 【管理】即可

### 获取私人令牌

- 私人令牌是 PicGo 访问 Gitee 必要的 token，点击头像 >> 【设置】>> 【生成新令牌】。

​	![image-20220219235547512](https://gitee.com/rootdingj/pichost/raw/master/blog/2022/02/image-20220219235039183.png)

- 可以根据自己的需要勾选权限，这里我仅勾选了 `projects`。

![image-20220220000111479](https://gitee.com/rootdingj/pichost/raw/master/blog/2022/02/image-20220219235802678.png)

点击【提交】>> 输入密码验证，即可得到你的私人令牌了，令牌只会展示一次，先复制保存起来（忘记了也没关系，可以删了重新生成）。

## 安装 PicGo

由于 PicGo 中下载 Gitee 插件需要基于 nodejs 环境，所以先确定电脑是否有一个较高版本的 nodejs 环境（如下图），没有可去 [Nodejs 中文官网](https://nodejs.org/zh-cn/) 下载。

![image-20220220001511387](https://gitee.com/rootdingj/pichost/raw/master/blog/2022/02/image-20220220001437547.png)

- 官网下载地址：https://molunerfinn.com/PicGo/
- GitHub下载地址：https://github.com/Molunerfinn/PicGo/releases

一路安装 PicGo 后，客户端界面如下：

![image-20220220002443399](https://gitee.com/rootdingj/pichost/raw/master/blog/2022/02/image-20220220002443399.png)

### 安装 PicGo 插件

PicGo 目前的默认图床中没有 Gitee，需要手动搜索并安装插件。

![image-20220220002305742](https://gitee.com/rootdingj/pichost/raw/master/blog/2022/02/image-20220220002305742.png)

- 我安装的是gitee 2.0.5，后面两个也可以，区别在于图床参数的设置；
- 如果无法安装插件，提示nodejs 相关错误，请检查 是否安装了 nodejs 或者安装的版本是否太低了。

### PicGo 设置

参数及说明：

```txt
设置日志文件：勾选日志等级 Error、Warn，若上传图片失败可以通过分析日志排查
设置Server：默认就好和 Typora 对应的，127.0.0.1：36677
上传前重命名：开
时间戳重命名：开
上传后自动复制URL：开
选择显示的图床：Gitee图床
```

### Gitee 图床设置

根据如下说明填写相关参数，然后点击【默认图床】>>点击【确认】，提示成功了即可

![image-20220220004811009](https://gitee.com/rootdingj/pichost/raw/master/blog/2022/02/image-20220220004653211.png)

- 我的仓库URL为：https://gitee.com/rootdingj/pichost，可根据上图对号入座
- path : 图片上传路径，默认为空（根目录），也可根据博文类型和时间分类存放
- token：就是上文提到的 Gitee 私人令牌

设置完成后，点击【上传区】拖拽图片进行上传验证。

*注意：点击【相册】可以同步删除 Gitee 图床中的图片，图片太多的情况下，PicGo 删错，可以将图床仓库 clone 到本地来管理删除。*

## Typora 配置

用 Typora 写博客真香，没下载的可以前往 [Typora 官网](https://www.typora.net/) 下载，不过现在这玩意收费了；想白嫖的话可以降到 Typora 0.11.18  版本，传送门：https://www.aliyundrive.com/s/WDA5XYcbJhh 

- 文件>>偏好设置，进入具体如下配置

![image-20220220011111395](https://gitee.com/rootdingj/pichost/raw/master/blog/2022/02/image-20220220010815249.png)

验证成功后，对着正在写的博客——《Gitee+PicGo+Typora 让你的写作更丝滑》点击鼠标右键>>上传图片，把博客中所有的图片上传到 Gitee 图床，真的丝滑。