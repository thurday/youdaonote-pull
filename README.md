# youdaonote-pull

现在有道云笔记不能导出笔记，如果想迁移笔记很麻烦。

此 Python 脚本的主要实现以下功能：

1. 可将所有笔记（文件）按原格式下载到本地
2. 由于「笔记」类型文件下载后默认为 Xml 格式，不是正常笔记内容，**默认将其转换为 Markdown 格式**
3. 由于有道云笔记图床图片不能在有道云笔记外显示，**默认将其下载到本地，或指定上传到 [SM.MS](https://sm.ms)**

<!--效果：-->

<!--![](https://deppwang.oss-cn-beijing.aliyuncs.com/blog/2020-06-09-130325.jpg)-->

## 使用提示

1. 脚本单纯本地运行，不用担心你的账号密码泄露。但注意，如果你自己修改脚本，注意不要将 config.json 文件 push 到 GitHub
4. 如果你不是开发者，可能对下面的命令行操作有所陌生，建议按步骤慢慢操作一遍。后续我会更加完善此文档，并根据需求看是否应该提供网页下载
6. 请确认代码是否为最新，有问题请提交 issue

<!--.note 格式笔记下载后为 Xml 格式，**默认将 .note 格式笔记转换为 Markdown 格式**，表格等未转换，需要手动复制-->

<!--有道云笔记图床图片在有道云笔记外不显示，**默认下载到本地，使用本地图片链接，可设置上传到免费的 [SM.MS](https://sm.ms) 上**-->

## 使用步骤

<!--针对普通用户-->

### 一、导出前的准备工作

#### 1、安装  [Git](https://git-scm.com/downloads)、clone 项目

- 可根据 [廖雪峰 Git 教程](https://www.liaoxuefeng.com/wiki/896043488029600/896067074338496) 安装 Git，测试是否安装成功

```sh
git --version
```

- 打开命令行软件，如 Terminal (macOS)、PowerShell (Windows)，clone 项目，里面包含脚本

```shell
pwd
git clone https://github.com/DeppWang/youdaonote-pull.git
cd youdaonote-pull
```

#### 2、安装 Python3、安装 requests 依赖模块（包）

- 可根据 [廖雪峰 Python 教程](https://www.liaoxuefeng.com/wiki/1016959663602400/1016959856222624) 安装 Python3，测试是否安装成功

```shell
python3 --version  # macOS/Linux
python --version  # Windows
```

- 安装 requests 依赖包，脚本依赖 requests

```shell
# macOS/Linux
sudo easy_install pip3 # 安装 Python3 Package Installer
pip3 install requests
```

```shell
# Windows
pip install requests

# 有问题可参考 https://www.liaoxuefeng.com/wiki/1016959663602400/1017493741106496
```

#### 3、设置脚本参数配置文件

config.json

```json
{
    "username": "",
    "password": "",
    "local_dir": "",
    "ydnote_dir": "",
    "smms_secret_token": ""
}
```

* username：**必填**，你的有道云笔记用户名
* password：**必填**，你的有道云笔记密码
* local_dir：选填，本地存放导出文件的文件夹，不填则默认为当前文件夹
* ydnote_dir：选填，有道云笔记指定导出文件夹名，不填则导出所有文件
* smms_secret_token：选填， [SM.MS](https://sm.ms) 的 Secret Token（注册后 -> Dashboard -> API Token），用于上传笔记中有道云图床图片到 SM.MS 图床，不填则只下载到本地（youdaonote-images 文件夹），Markdown 使用本地链接
* 建议使用 [Sublime](https://www.sublimetext.com/3) 编辑 config.json

示例：

- macOS

```json
{
    "username": "deppwang@163.com",
    "password": "12345678",
    "local_dir": "/Users/yanjie/Dropbox/youdaonote/test",
    "ydnote_dir": "",
    "smms_secret_token": "SGSLk9yWdTe4RenXYqEPWkqVrx0Yexample"
}
```

- Windows

```json
{
    "username": "deppwang@163.com",
    "password": "12345678",
    "local_dir": "D:/Dropbox/youdaonote/test",
    "ydnote_dir": "",
    "smms_secret_token": "SGSLk9yWdTe4RenXYqEPWkqVrx0Yexample"
}
```

###  二、运行导出脚本

```shell
python3 pull.py # macOS/Linux
python pull.py  # Windows
```

效果：

![](https://deppwang.oss-cn-beijing.aliyuncs.com/blog/2020-06-09-130325.jpg)

### 三、多次导出

多次导出时，同样使用以下命令：

```shell
python3 pull.py # macOS/Linux
python pull.py # Windows
```

根据有道云笔记的最后修改时间是否大于本地文件最后修改时间来判断是否更新。再次导出时，只会导出有道云笔记上次导出后新增、修改的笔记，不会覆盖本地已经修改的文件。**但有道云笔记和本地不要同时修改同一个文件，这样会导致本地修改丢失**！

导出是根据最后修改时间来判断，所以被更新文件和新导出一样，会重复下载图片

## 后续开发计划

- [x] 将 .note 文件转换为 MarkDown 文件
- [x] 解决有道云图床图片不能显示问题，实现方式为默认下载到本地，使用本地图片链接，也可上传到 SM.MS 图床
- [x] 首次导出使用账号密码登录，再次导出时使用 Cookie 登录（Cookie 保存在 cookies.json 中），避免频繁操作时因为需要输入验证码导致登录不上的情况
- [ ] 优化如果同一目录存在同名的 .md 和 .note 文件，.md 文件将被覆盖的情况
- [ ] 并发执行以加快速度
- [ ] 针对非开发者用户，提供网页输入账号密码直接下载所有笔记压缩包的方式

## 原理

- 正常用户操作时，浏览器（前端）调用服务器（后端）接口，接口返回文件内容由前端渲染显示。
- [找到有道云笔记的接口](https://depp.wang/2020/06/11/how-to-find-the-api-of-a-website-eg-note-youdao-com)，模拟操作接口，将前端显示改为存放到本地。
- Xml 转换为 Markdown：使用了 [xml.etree.ElementTreeI](http://docs.python.org/3.7/library/xml.etree.elementtree.html)

## 感谢（参考）

- [YoudaoNoteExport](https://github.com/wesley2012/YoudaoNoteExport)

## 出发点

原来一直是有道云笔记的忠实用户，后面接触到了所见即所得的 [Typora](https://typora.io/)，有点用不惯有道云笔记了，想着有什么法子能电脑本地文件和有道云笔记同步，这样电脑使用 Typora，手机使用有道云笔记。发现有道云笔记有 [Open API](http://note.youdao.com/open/developguide.html) ，打算利用提供的 API，写两个脚本，一个 pull 所有文件到本地，一个 push 本地文件到云笔记。但 API 太难用了，N 多年没更新了，问客服也没更新的意思，开发到最后发现竟然没有 Markdown 文件的接口，醉了。遂放弃。

现在我使用 Typora + [Dropbox](https://www.dropbox.com/) + [MWeb](https://www.mweb.im/) 实现同步笔记和手机查看编辑的功能，很香。

最近给朋友推荐此方式，但发现有道云笔记最新的 Mac 客户端和网页端去除了导出所有笔记的功能！这是什么逻辑，怕用户跑了么。所以在原来 pull 脚本的基础上修改得到此脚本。
