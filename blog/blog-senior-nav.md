

# Hexo+Github 搭建个人博客

Markdown 博客将图片和博文放在 GitHub 的同一个仓库中，然后采用相对路径访问图片，会碰到一些问题：

- 提交到仓库中的图片会占用一定的空间，日积月累仓库变得臃肿起来了，能不能把图片分离开？
- 想要一篇博文同时发在 CSDN、博客园、简书、掘金等平台，但上传图片太麻烦了，怎样才能图片一次上传，处处引用？

**图床**刚好解决上述问题，下面是我的方案以及理由。

## 主题的安装与配置

我使用的主题 [hexo-theme-butterfly](https://github.com/jerryc127/hexo-theme-butterfly)，下文以 butterfly 主题为例进行简述。butterfly 的详细介绍可查阅 [butterfly 官方文档](https://butterfly.js.org/ )。

*注：主题相关的命令都是在你的 Hexo 根目录下执行，例如：我的根目录为 `E:\blogwork\blog`>* 

### 主题安装

参考：[Butterfly 安裝文檔(一) 快速開始](https://butterfly.js.org/posts/21cfbf15/ )

- 在你的 Hexo 根目录下，克隆 butterfly 主题，注意存放路径

  ```git
  git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly
  ```

- 应用主题

  修改 Hexo 根目录下的 _config.yml，把主题改为 butterfly

  ```yml
  theme: butterfly
  ```

- 安装渲染器插件

  如果你沒有 pug 以及 stylus 的渲染器，请安裝

  ```git
  npm install hexo-renderer-pug hexo-renderer-stylus --save
  ```

- 执行命令，本地预览：http://localhost:4000/

  ```git
  hexo clean
  hexo g
  hexo s
  ```

### 主题配置

参考：[Butterfly 安裝文檔(二) 主題頁面](https://butterfly.js.org/posts/dc584b87/)

- 配置标签页

- 配置分类页

- 配置友情链接

- 配置404页面

  - 在 butterfly 主题中找到他的 `_config.yml` 文件，拷贝到 Hexo 根目录下，并重命名为 `_config.butterfly.yml`

  - 这时 Hexo 根目录下有两个 .yml 文件（`\_config.yml、_config.butterfly.yml`），Hexo 会先执行  `_config.yml` ,然后再执行  `_config.butterfly.yml`

  - `_config.butterfly.yml` 中找到  error_404 配置项，启用改配置即可

    ```yml
    error_404:
      enable: ture   ## false 改为 ture
      subtitle: 'Page Not Found'
      background: https://i.loli.net/2020/05/19/aKOcLiyPl2JQdFD.png
    ```

# 参考

- [文档| Hexo](https://hexo.io/zh-cn/docs/)