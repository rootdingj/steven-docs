

# Hexo+Github 搭建个人博客

Markdown 博客将图片和博文放在 GitHub 的同一个仓库中，然后采用相对路径访问图片，会碰到一些问题：

- 提交到仓库中的图片会占用一定的空间，日积月累仓库变得臃肿起来了，能不能把图片分离开？
- 想要一篇博文同时发在 CSDN、博客园、简书、掘金等平台，但上传图片太麻烦了，怎样才能图片一次上传，处处引用？

**图床**刚好解决上述问题，下面是我的方案以及理由。

## 准备工作

### 工具推荐

- 写博客的 Markdown 编辑器：Typora  

- 代码编辑器：VSCode，Webstrom

### 环境搭建

- 版本控制工具：Git

- js 运行环境：Node.js

- 包管理工具：npm、cnpm、yarn

### 配置 Github 仓库

- 新建仓库
- 配置 SSH key

## 使用 Hexo 搭建博客

### 安装 Hexo

- **创建**名为 blog 的**文件夹**，用 VSCode 中打开 blog 文件夹，并打开 VSCode 终端
- **全局安装 Hexo**，VSCode 终端中输入 `npm install -g hexo` 

- **初始化博客**，在终端中输入 `hexo init`
- **安装依赖**，若没有生成 node_modules 文件夹，则需要输入 `npm install` 安装依赖
- **本地预览博客**，输入 `hexo s`
- 浏览器输入 http://localhost:4000/ ，查看效果。

### 发布博客到 Github

- 打开 blog 根目录下的 _config.yml 文件，配置 deploy，例如：

  ```yml
  deploy:
    type: git
    repo: https://github.com/rootdingj/rootdingj.github.io  ## https://github.com/你的 GitHub 名字/仓库名
    branch: master  ## 主干分支名
  ```

- 安装 git 插件

  ```git
  npm install hexo-deployer-git --save
  ```

- 执行 3 个命令

  ```git
  hexo clean
  hexo g
  hexo d
  ```

- 查看效果，浏览器中输入：https://rootdingj.github.io/。

## Hexo 文档摘要

当你成功发布博客到 Github 后，似乎觉得已经可以掌握一切了；然而这只是开始，想要玩转 Hexo 博客可能需要阅读 [文档| Hexo](https://hexo.io/zh-cn/docs/) ，但文档太长了，让人昏昏欲睡，我摘录了一些必要的内容如下文。

### Hexo 常用命令

关于 Hexo 命令只用记住如下几个，需要的时候可使用 `hexo help [command]` 查看命令的详细信息，或阅读 [指令 | Hexo](https://hexo.io/zh-cn/docs/commands)。

| 命令                            | 描述                                                         |
| :------------------------------ | ------------------------------------------------------------ |
| `npm install -g hexo`           | 全局安装 Hexo                                                |
| `npm update -g hexo`            | 升级 Hexo                                                    |
| `hexo init [folder]`            | 初始化博客，`folder` 可选参数，用以指定初始化目录的路径，若无指定则默认为当前目录 |
| `hexo clean`                    | 清理缓存文件                                                 |
| `hexo server（简写：hexo s）`   | 启动本地服务器，本地预览博客                                 |
| `hexo generate（简写：hexo g）` | 生成静态文件(public文件夹内容生成)                           |
| `hexo deploy（简写：hexo d）`   | 部署网站                                                     |
| `npm install hexo-xxxx --save`  | 安装插件                                                     |
| `npm uninstall hexo-xxx --save` | 卸载插件                                                     |

### Hexo 目录结构

如果想修改 Hexo 的布局、显示样式，或是为其增添 RSS、评论等功能，首先应该了解 Hexo 每个目录的作用。下面列了一下我的 Hexo 目录结构：

```
.
├─.deploy_git
├─node_modules
├─public
├─scaffolds
├─source
│ ├─about
│ ├...
│ ├─_drafts
│ └─_posts
└─themes
    ├─landscape
    └─...
├── _config.yml
├── package.json
```

- .deploy_git

​	部署到github后自动创建。

​	该目录中的内容 = github站点中对于repository中的内容 = 最近一次上传到站点的public目录中的内容

​	因此，该目录的结构和 `public` 目录基本一致（不一致的情况是由于重新生成，但是没有发布站点造成，此时 `public` 内容新于 `.deploy_git` 内容）。

- node_modules

​	存放安装的 Hexo 扩展、依赖项。

- public

  执行 `hexo g` 命令，Hexo 程序会解析 `source` 、当前使用的 theme，生成的静态网页内容目录就是 `piblic` 。执行 `hexo d` 则将该目录内容复制到 `.deploy_git` 目录。

- scaffolds

​	模版文件夹。当新建文章时，Hexo 会根据 scaffold 来建立文件。

- source

  存放用户资源的地方。

  - _posts 文件夹

    存放博客文章的地方，其中的 markdown 文件、html 文件、org 文件等会被解析并放到 public 文件夹，并发布到站点。

  - 其他开头命名为 _ (下划线)的文件 / 文件夹

    将会被忽略。因此可在source目录下创建 `_drafts` 目录用于存放未完成的草稿，其内容不会发布到网站。

  - 其他开头非_的文件 / 文件夹

    会被拷贝到public目录并上传到站点。

- themes

​		主题文件夹。默认安装 `landscape` 主题，你可以安装新主题到 `themes` 目录，也可以自己新建主题。

### _config.yml 配置

各配置项介绍如下：

- **Site**，网站的个性化描述，大家需要根据自己的实际情况认真填写

  ```
  title: 网站标题
  subtitle: 网站副标题
  description: 网站描述
  keywords:网站关键字
  author: 网站作者
  language: 网站使用的语言，默认是 en ，中文网站填 zh-CN
  timezone: 网站使用的时区，默认为 计算机的预设置，可以不填
  ```

- **URL**，关于博客文章 URL 的设置，一般不用进行更改

  ```
  url: 网站的网址
  permalink: 文章的链接格式 ，默认为 :year/:month/:day/:title/
  ```

- **Directory**，关于文件夹的设置，也是一般不用进行更改

  ```
  source_dir: 资源文件夹 ，存放用户的资源文件，默认为 source
  public_dir: 公用文件夹 ，存放生成的静态文件，默认为 public
  tag_dir: 标签目录 ，默认为 tags
  archive_dir: 档案目录 ，默认为 archives
  category_dir: 分类目录 ，默认为 categories
  code_dir: 代码目录 ，默认为 downloads/code
  i18n_dir: :i18n 目录 ，默认为 :lang
  skip_render:储存站长验证文件，跳过指定文件的渲染
  ```

- **Writing**，比较常用的写作设置，可以根据自己的写作习惯随时进行调整

  ```
  new_post_name: 文章的文件名格式，默认为 :title.md
  default_layout: 预设的布局模板，默认为 post
  titlecase: 标题是否使用首字母大写 ，默认为 false
  external_link:链接是否在新标签页中打开，默认为 true
  filename_case: 将文件名转换为 1 小写 或 2 大写，默认为 0
  render_drafts: 是否显示渲染草稿，默认为 false
  post_asset_folder: 是否启用 Asset 文件夹，默认为 false
  relative_link: 是否建立相对于根文件夹的链接，默认为 false
  future: 是否显示未来文章，默认为 true
  highlight:代码块设置
    enable: 是否使用代码高亮 ，默认为 true
    line_number: 是否显示行号 ，默认为 true
    auto_detect: 是否自动检测语言 ，默认为 false
    tab_replace: tab 替代设置，默认为''
  ```

- **Home page setting**，首页设置，可以自己决定每页显示的文章数量和显示文章的顺序

  ```
  index_generator: 主页设置
    path: 首页的根目录
    per_page: 每页显示文章的数量，默认为 10
    order_by: 显示文章的顺序，默认为 -date
  ```

- **Pagination**，分页设置，可以自己决定单个页面上显示的文章数量和分页目录

  ```
  default_category: 预设分类，默认为 uncategorized
  category_map:分类别名
  tag_map:标签别名
  ```

- **Category & Tag**，分类和标签的配置

  ```
  default_category: 预设分类，默认为 uncategorized
  category_map:分类别名
  tag_map:标签别名
  ```

- **Extensions**，设置主题类型和插件

  ```
  theme: 主题
  ```

- **deploy**，网站部署配置

  ```
  deploy:
    type: 网站部署类型，默认为 git
    repo: 网站部署地址
    branch: 网站部署分支
  ```





# 参考

- [文档| Hexo](https://hexo.io/zh-cn/docs/)