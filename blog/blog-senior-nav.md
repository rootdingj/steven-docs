

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

## 进阶配置

### Gulp压缩

- 全局安装gulp

  ```
  npm install --global gulp-cli
  npm install gulp -g
  gulp -v
  ```

- 在Hexo根目录下，安装gulp压缩插件

  ```
  // 压缩html
  npm install gulp-htmlclean --save-dev
  npm install --save gulp-html-minifier-terser
  
  // 压缩css
  npm install gulp-clean-css --save-dev
  
  // 压缩js
  npm install --save-dev gulp-uglify
  npm install --save-dev gulp-babel @babel/core @babel/preset-env
  
  // 压缩图片
  npm install --save-dev gulp-imagemin
  ```

- 在 Hexo 站点下新建`gulpfile.js`文件，文件内容如下：

  ```
  var gulp = require('gulp');
  var minifycss = require('gulp-minify-css');
  var uglify = require('gulp-uglify');
  var htmlmin = require('gulp-htmlmin');
  var htmlclean = require('gulp-htmlclean');
  // 压缩css
  gulp.task('minify-css', function() {
      return gulp.src('./public/**/*.css')
          .pipe(minifycss({
              compatibility: 'ie8'
          }))
          .pipe(gulp.dest('./public'));
  });
  // 压缩js
  gulp.task('minify-js', function() {
      return gulp.src('./public/js/**/*.js')
          .pipe(uglify())
          .pipe(gulp.dest('./public'));
  });
  
  // 压缩 public 目录 html文件
  gulp.task('minify-html', function() {
      return gulp.src('./public/**/*.html')
        .pipe(htmlclean())
        .pipe(htmlmin({
             removeComments: true,
             minifyJS: true,
             minifyCSS: true,
             minifyURLs: true,
        }))
        .pipe(gulp.dest('./public'))
    });
  
  // 默认任务
  gulp.task('default', gulp.parallel(
      'minify-css','minify-js','minify-html'
  ));
  ```

- 静态资源压缩，只需要每次在执行 `generate` 命令后执行 `gulp` 就可以实现对静态资源的压缩

  ```
  hexo g
  gulp
  ```

### 添加来必力评论系统

_config.butterfly.yml 配置文件中，修改 comments 的 use 配置项为 Livere，然后在 livere 配置项中，添加你在来必力网站注册的ID即可

```
comments:
  # Up to two comments system, the first will be shown as default
  # Choose: Disqus/Disqusjs/Livere/Gitalk/Valine/Waline/Utterances/Facebook Comments/Twikoo/Giscus
  use: 
    Livere   ## 使用来必力
    # Valine,Disqus
  text: true # Display the comment name next to the button
  # lazyload: The comment system will be load when comment element enters the browser's viewport.
  # If you set it to true, the comment count will be invalid
  lazyload: false
  count: false # Display comment count in post's top_img
  card_post_count: false # Display comment count in Home Page
  
  
# livere (來必力)
# https://www.livere.com/
livere:
  uid: XXXX== ## uid:后面有个空格
```

### 插入代码自定义样式

_config.butterfly.yml 配置文件中，找到 inject 配置项，配置想要的样式文件，相关的样式可以去github上找，或者按照自己想要的去配置。

```
# Inject
# Insert the code to head (before '</head>' tag) and the bottom (before '</body>' tag)
# 插入代码到头部 </head> 之前 和 底部 </body> 之前
inject:
  head:
    - <link rel="stylesheet" href="/css/bg.css">
    - <link rel="stylesheet" href="/css/style.css">
  bottom:
    # - <script src="xxxx"></script>
```

### 备案信息实现

_config.butterfly.yml 配置文件中，找到 custom_text 配置项，类似配置如下：

```
<div id="footer-wrap">
	<div class="copyright">
		©2019 - 2022 By steven ting
	</div>
	<div class="footer_custom_text">
		<span style="color:#999" onclick="window.open('https://beian.miit.gov.cn/#/Integrated/index/')">
			<a class="footer-a">
				渝 ICP 备 19004608 号
			</a>
		</span>
		<br>
		<span style="color:#999" onclick="window.open('http://www.beian.gov.cn/portal/registerSystemInfo?recordcode=50023502000331')">
			<a class="footer-a">
				<img class="upy entered loaded" align="absmiddle" src="https://cdn.jsdelivr.net/gh/constown/HexoStaticFile/img/20200831161110.png"
				data-lazy-src="https://cdn.jsdelivr.net/gh/constown/HexoStaticFile/img/20200831161110.png"
				data-ll-status="loaded">
				渝公网安备 50023502000331 号
			</a>
		</span>
	</div>
</div>
```



### 博客SEO优化引擎收录



### 双线部署与CDN加速





# 参考

- [文档| Hexo](https://hexo.io/zh-cn/docs/)