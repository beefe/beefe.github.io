## 这是什么？

这是 BeeFE 团队公共博客的源码，将文章提交到这里后就会在 <http://beefe.github.io> 上展现。


## 环境搭建

这个系统是基于 [jekyll](http://jekyllrb.com/) 搭建的，为了方便本地编辑和看效果，需要将本项目 clone 至本地环境，并在本机安装 jekyll 环境。

### Mac/Linux 下

请使用如下命令（其中 gem 是 [Ruby](https://www.ruby-lang.org/)  的包管理工具）安装 jekyll（如果遇到权限问题请在前面加 sudo）：

    gem install jekyll jekyll-paginate redcarpet

如果在 Mac 下安装遇到编译报错，可以试试用 [Brew](http://brew.sh/) 安装新版 ruby

    brew install ruby

如果 gem 安装不上，请试试国内镜像

    gem sources --remove https://rubygems.org/
    gem sources -a https://ruby.taobao.org/


### 本地预览

完成 jekyll 的安装后，从公共主页获取源代码

```bash
$ git clone https://github.com/beefe/beefe.github.io.git
```

进入该目录运行如下命令，就能在 localhost:4000 中预览了：

```bash
   $ jekyll serve
```

## 如何编辑？

### 新建草稿

新文章编写时请先浏览 `_drafts` 目录，这里存放的是草稿，它不会在首页显示，请参考里面的 `2014-05-06-empty.md` 文件，新建文件名要遵循这样的格式，以日期开头，后面接着是文章的对外 url 子路径，中间以 `-` 分隔，后续标题有多个单词时也以 `-` 作为分隔符，建议只用英文单词或拼音，目前不确定中文是否可行。

需要注意的是草稿不会出现在首页列表中，如果想本地预览草稿效果，可以加 `--drafts` 参数，如下所示：

    jekyll serve --watch --drafts

### 个人信息

每篇文章都会附上个人相关信息，所以请先编辑 `_data\authors.yml` 文件，按照其中的格式新增一项，需要注意以下几点：

* 这是 YAML 格式，每行的开头是两个空格，而不是 TAB
* `author` 字段需要和你所写文章开头的 `author` 属性保持一致，这样才能正确展现
* `url` 字段可以连接到个人主页或微博等
* `intro` 是个人简介，会在每篇文章中展现，带标点不要超过十个字

### 图片存放地

请将图片放在 `img` 目录里，每篇文章新建一个目录（目录名称请与文章名称保持同步），在文章中的引用方式为：

    ![](/img/<文章名>/xx.png)

### 发布

如果觉得文章可以对外展示了，不过还得先找个 280x150 的图片作为首页封面，放到 `/img/<文章名>/cover.jpg` 下，然后将文章移到 `_posts` 目录下，提交后就可以了。

### 小技巧

* jekyll 最终生成的文件会放在 `_site` 目录下，可以通过浏览这个目录来确认效果
* img 目录的主要用途是放图片，但也可以放其它文件静态，如 zip 等
* 不常见的语法高亮缩写可以[参考这里](http://tinker.kotaweaver.com/blog/?p=152)

## 写什么？

虽然对外会觉得这是团队 Blog，但其实准确来说这里是每个团队成员的个人分享，每篇文章都只代表个人观点，所以如果有什么值得分享的话题，请不要有太多顾虑，想写什么就写什么，借助这个平台来提升自己的影响力吧。

具体内容形式将包括但不限于：

* 技术介绍、经验总结
* BeeFE 新开源项目及升级版本介绍
* 优秀文章的翻译
* 优秀资源（书籍、文章、教程、开源项目）等的推荐

另外，如果你对目前界面的哪些细节不满意，也欢迎直接修改相关源码。

### 对于写作风格的约定

请参考 [Markdown 编写规范](https://github.com/fex-team/styleguide/blob/master/markdown.md)，另外在根目录下个脚本 format.js，可以通过它来自动加空格。


## 其它问题

* 为什么某篇文章没显示出来？
    * 你确定放到 `_posts` 下了是吧？
    * 有可能是用了 `{% xxx %}`，因为页面会当成 Liquid 模板进行解析，所以请使用 `{% raw  %}{% xxx %}{% endraw %}` 来包含起来
    * 那你肯定没在本地预览过，估计是有报错

* 文章发布前需要找谁审核么？
    * 不需要，因为每篇文章都是以个人名义发表的

* 我不是 beeFE 团队成员，可以在这里发表文章么？
    * 可以啊，请提 pull request

