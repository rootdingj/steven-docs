

# Hexo+Github 搭建个人博客

Markdown 博客将图片和博文放在 GitHub 的同一个仓库中，然后采用相对路径访问图片，会碰到一些问题：

- 提交到仓库中的图片会占用一定的空间，日积月累仓库变得臃肿起来了，能不能把图片分离开？
- 想要一篇博文同时发在 CSDN、博客园、简书、掘金等平台，但上传图片太麻烦了，怎样才能图片一次上传，处处引用？

**图床**刚好解决上述问题，下面是我的方案以及理由。

## 准备工作

### 工具推荐

Markdown 编辑器  

- Typora，顺便说一下 Typora 现在的版本已经收费了，想白嫖可以降到 Typora 0.11.18  版本，传送门：https://www.aliyundrive.com/s/WDA5XYcbJhh    

代码编辑器

- VSCode，Webstrom

### 环境搭建

版本控制工具

- Git

js 运行环境

- nodejs

包管理工具

- npm
- cnpm
- yarn



