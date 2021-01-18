
# 在线交流平台项目开发总结

历时不到一个月的时间，基于大一暑假的动漫交流平台已经改装完毕。
:::default
这里是[传送门](http://bbs.ambitiouscc.com)

## 开发流程

* 前端开发：

1. 设计整个网站框架，改变拼接页面的布局和决定哪些页面需要修改。

2. 进一步完善各个功能，修改部分样式，大幅度调整网站的背景和色调。

3. 不断调整布局，优化pc/ph/pad端浏览体验，虽然效果并没有那么理想。

4. 配合后端增加若干功能

* 后端开发：

1. 配合以前的sql语句和BEAN对象建立新的数据库，优化了很多属性
但是有个问题暂未解决：如何同步各个数据表之间的公用的字段比如用户头像用户昵称等
暂定解决思路：取消共用属性，不断调用查询语句(可能增加服务器负荷)

2. 主要优化和增加了若干用户管理模块的功能，具体的功能见后方

3. 数据库连接改为jdbc template，很抱歉springmvc 还只会用并不怎么会配置，所以没有用上。

* 设备准备

1. 购买了云服务器+域名+DNS加速服务+云数据库

2. 服务器部署了可视化桌面方便进行本地调试作业

3. 服务器部署了vncserver，连接2中的可视化桌面

4. 服务器使用七牛云JAVA SDK，连接图床

* 软件准备

* xshell/xftp/vnc/idea

## 主要功能

* 用户模块
+++primary 用户注册
- [ ] 后端输入校验
- [x] 前端JS校验输入
- [x] 检查用户是否注册
- [x] 邮箱激活
+++

+++info 用户登陆
- [x] 用户名校验是否存在
- [x] 账号是否激活，如果未激活则会提醒重新注册并注销
- [x] 校验用户密码，实现前端后端两次md5加密
- [x] 存储用户安全信息，不涉及密码等隐私信息至session
+++

+++info 忘记密码
- [x] 忘记密码发送邮件，邮件跳转密码更改页面，且此页面只能通过邮箱访问
+++

+++info 个人基本资料修改
- [ ] 详细介绍出现bug，会自动存储信息到phone列，有时间请尽快排查问题:construction:
- [x] 头像上传至七牛云，减少云服务器的内存
- [x] 个人资料的修改如昵称、地址、性别、生日等
+++

+++info 个人资料
- [ ] 可以给其他人留言
- [x] 可浏览其他人资料：头像、签名等
- [x] 自己资料的浏览
+++

+++info 管理个人文章
- [ ] 重新编辑文章内容
- [x] 删除文章
- [x] 点击跳转到达具体文章内容
+++

+++warning 修改密码
- [x] 旧密码检验
- [x] 邮箱确认修改密码操作，**只有通过邮箱验证才能成功修改密码**
+++

+++danger 注销用户
- [x] 邮箱验证
- [x] 点击操作以后跳转到~诡异的~欢迎界面
+++

* 文章模块

+++primary 写文
- [ ] 实现插入图片的修改功能
- [x] 整合了wang编辑器，暂未更新
- [x] 更加优雅的布局
- [x] 文章标签
- [x] 文章摘要
+++

* 社区模块

+++primary 浏览所有文章
- [ ] 展示点赞数和收藏数
- [x] 展示封面
- [x] 展示作者和发表日期
- [x] 分页展示
- [x] 文章块调整
+++

+++info 文章内容
- [ ] 头像
- [x] 文章内容基本样式
- [x] 文章作者信息：昵称和发表日期
- [x] 点赞互动
- [x] 收藏互动
+++

+++info 评论模块
- [ ] 互动消息提醒
- [x] 主楼评论
- [x] 二级评论
- [x] 评论点赞
- [x] 评论删除
+++

* 其他功能

+++info 搜索
- [ ] 用户搜索
- [ ] 搜索到的内容标记
- [x] 文章标题和摘要以及关键字
- [x] 支持全局搜索内容
+++

* 未完成的功能

+++danger 管理员模块
- [ ] 管理员管理界面
- [ ] 站内数据统计，可以整合google analytics
- [ ] 文章内容和评论的审核，可以集成插件
+++

+++danger 论坛模块
- [ ] 发表问题
- [ ] 查看问题下所有回复
- [ ] 问题收藏
- [ ] 回复内容点赞删除
+++

+++danger 资源管理
- [ ] 上传学习资料
- [ ] 资料评分系统
- [ ] 资料下载
- [ ] 资料预览与基本信息展示
- [ ] 资料评论和资料收藏
- [ ] 个人的资料下载记录
+++

+++danger 推荐系统
- [ ] 根据点赞量和收藏量推送主页
- [ ] 根据文章内容定向推送给用户
+++

+++danger 多媒体系统
- [ ] 用户可以上传在线观看的学习视频
- [ ] 用户可以上传音乐视频
- [ ] 用户可以定制自己的页面
- [ ] 切换昼/夜模式
+++

## 部署

实现了tomcat的部署，不能支持高并发，希望先继续学习nginx知识后整合其：
* 反向代理
* 负载均衡

## 踩坑记录
1. 页面无法访问
配置tomcat 的server.xml如下
> 8080-> 80 ; 8443-> 43
> 修改defaultHost和Host为自己的域名并增加Context内容指向项目位置，path="" docBase="项目地址" 
> 记得关闭所有占用端口，如apache2服务

+++warning 具体的server.xml配置
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Server port="8005" shutdown="SHUTDOWN">
  <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
  <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
  <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
  <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />
  <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" />
  <GlobalNamingResources>
    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml" />
  </GlobalNamingResources>
  <Service name="Catalina">
    <Connector port="80" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="443" />
    <Engine name="Catalina" defaultHost="bbs.ambitiouscc.com">
      <Realm className="org.apache.catalina.realm.LockOutRealm">
        <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
               resourceName="UserDatabase"/>
      </Realm>

      <Host name="bbs.ambitiouscc.com"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
	<Context path="" docBase="webapp" debug="0" reloadable="true" />
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
      </Host>
    </Engine>
  </Service>
</Server>

```
+++

:::info 
DEBUG 小贴士： 出现后端500错误，一定要查查看`logs/catalina.out`文件中的输出信息，建议各个语句分模块书写内容，方便排除。同时一定要看最下面的**caused by**的报错信息，这里才是最主要的问题所在！！
:::

2. 无法获取资源：~删除html文件中meta自动升级https的部分~，检查后端内容：
* 是否配置了固定的访问端口
* filter进行字符过滤时是否统一拼接`text/html`而不是根据对应类型拼接内容

3. 数据库连接：不要出现多个获取数据的方法，否则连接数据库会出错：`Could not initialize class web.utils.JDBCUtils`

4. jdbc template进行select时需要`try{}catch{}`语句块，否则会报错

5. 存入session的数据应该是不能修改的，比如用户id可以存入session，而用户的个人资料都不要存入session，否则修改资料无法立即生效。

6. 检查所有的数据库字段，部分数值类型的是不能赋值null的，后端在查到以后转换会出错

7. linux系统的存储路径建议都用`/`写

8. 七牛云的连接Auth验证出错->无法读取配置文件(windows 可以)

应该使用相应的inputstream文件流读取，不能直接通过prop.load(file)读取，详细的配置如下：

+++info 读取文件配置工具类（部分）
```java
private QiNiuConfig(){
        Properties prop = new Properties();
        String File_PATH = this.getClass().getClassLoader().getResource(FILENAME).getPath();
        File file = new File(File_PATH);
        try {
            FileInputStream in = new FileInputStream(file);
            prop.load(in);
            accessKey = prop.getProperty("qiniu.access-key");
            secretKey = prop.getProperty("qiniu.secret-key");
            bucket = prop.getProperty("qiniu.bucket");
            domainOfBucket = prop.getProperty("qiniu.domain-of-bucket");
            expireInSeconds = Long.parseLong(prop.getProperty("qiniu.expire-in-seconds"));
            String zoneName = prop.getProperty("qiniu.zone");
            if(zoneName.equals("zone0")){
                zone = Zone.zone0();
            }else if(zoneName.equals("zone1")){
                zone = Zone.zone1();
            }else if(zoneName.equals("zone2")){
                zone = Zone.zone2();
            }else if(zoneName.equals("zoneNa0")){
                zone = Zone.zoneNa0();
            }else if(zoneName.equals("zoneAs0")){
                zone = Zone.zoneAs0();
            }else{
                throw new Exception("Zone对象配置错误！");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```
+++

## 本站采用的数据表结构

* 具体的表结构如下图所示：

[![rRzoqO.jpg](https://s3.ax1x.com/2020/12/25/rRzoqO.jpg)](https://imgchr.com/i/rRzoqO)

:::info
还有很多问题需要修改，比如尝试加入triggle实现数据同步的问题，不过mysql似乎不支持
:::

+++info 数据表结构
```sql
/*
 Navicat Premium Data Transfer

 Source Server         : cloud
 Source Server Type    : MySQL
 Source Server Version : 50718
 Source Host           : cdb-9uvgorig.bj.tencentcdb.com:10186
 Source Schema         : forumproject

 Target Server Type    : MySQL
 Target Server Version : 50718
 File Encoding         : 65001

 Date: 25/12/2020 14:00:05
*/

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for art_comment_likes
-- ----------------------------
DROP TABLE IF EXISTS `art_comment_likes`;
CREATE TABLE `art_comment_likes`  (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `article_id` bigint(20) NULL DEFAULT NULL,
  `user_id` bigint(20) NOT NULL,
  `like_time` datetime(0) NOT NULL,
  `comment_time` datetime(0) NOT NULL,
  `comment_main_time` datetime(0) NOT NULL,
  `comment_id` bigint(20) NOT NULL,
  PRIMARY KEY (`id`) USING BTREE,
  INDEX `fk_art_comment_likes2`(`user_id`) USING BTREE,
  INDEX `fk_art_comment_likes1`(`comment_id`) USING BTREE,
  CONSTRAINT `fk_art_comment_likes1` FOREIGN KEY (`comment_id`) REFERENCES `comment` (`id`) ON DELETE CASCADE ON UPDATE RESTRICT,
  CONSTRAINT `fk_art_comment_likes2` FOREIGN KEY (`user_id`) REFERENCES `user` (`user_id`) ON DELETE CASCADE ON UPDATE RESTRICT
) ENGINE = InnoDB AUTO_INCREMENT = 1 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Table structure for art_comment_main_likes
-- ----------------------------
DROP TABLE IF EXISTS `art_comment_main_likes`;
CREATE TABLE `art_comment_main_likes`  (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `article_id` bigint(20) NULL DEFAULT NULL,
  `user_id` bigint(20) NOT NULL,
  `like_time` datetime(0) NOT NULL,
  `comment_main_id` bigint(20) NOT NULL,
  PRIMARY KEY (`id`) USING BTREE,
  INDEX `fk_art_comment_main_likes1`(`comment_main_id`) USING BTREE,
  INDEX `fk_art_comment_main_likes2`(`user_id`) USING BTREE,
  CONSTRAINT `fk_art_comment_main_likes1` FOREIGN KEY (`comment_main_id`) REFERENCES `comment_main` (`id`) ON DELETE CASCADE ON UPDATE RESTRICT,
  CONSTRAINT `fk_art_comment_main_likes2` FOREIGN KEY (`user_id`) REFERENCES `user` (`user_id`) ON DELETE CASCADE ON UPDATE RESTRICT
) ENGINE = InnoDB AUTO_INCREMENT = 48 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Table structure for art_keeps
-- ----------------------------
DROP TABLE IF EXISTS `art_keeps`;
CREATE TABLE `art_keeps`  (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `article_id` bigint(20) NOT NULL,
  `user_id` bigint(20) NOT NULL,
  `keep_time` datetime(0) NOT NULL,
  PRIMARY KEY (`id`) USING BTREE,
  INDEX `fk_art_keeps1`(`article_id`) USING BTREE,
  INDEX `fk_art_keeps2`(`user_id`) USING BTREE,
  CONSTRAINT `fk_art_keeps1` FOREIGN KEY (`article_id`) REFERENCES `article` (`article_id`) ON DELETE CASCADE ON UPDATE RESTRICT,
  CONSTRAINT `fk_art_keeps2` FOREIGN KEY (`user_id`) REFERENCES `user` (`user_id`) ON DELETE CASCADE ON UPDATE RESTRICT
) ENGINE = InnoDB AUTO_INCREMENT = 7 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Table structure for art_likes
-- ----------------------------
DROP TABLE IF EXISTS `art_likes`;
CREATE TABLE `art_likes`  (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `article_id` bigint(20) NOT NULL,
  `user_id` bigint(20) NOT NULL,
  `like_time` datetime(0) NOT NULL,
  PRIMARY KEY (`id`) USING BTREE,
  INDEX `fk_art_likes1`(`article_id`) USING BTREE,
  INDEX `fk_art_likes2`(`user_id`) USING BTREE,
  CONSTRAINT `fk_art_likes1` FOREIGN KEY (`article_id`) REFERENCES `article` (`article_id`) ON DELETE CASCADE ON UPDATE RESTRICT,
  CONSTRAINT `fk_art_likes2` FOREIGN KEY (`user_id`) REFERENCES `user` (`user_id`) ON DELETE CASCADE ON UPDATE RESTRICT
) ENGINE = InnoDB AUTO_INCREMENT = 20 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Table structure for article
-- ----------------------------
DROP TABLE IF EXISTS `article`;
CREATE TABLE `article`  (
  `article_id` bigint(20) NOT NULL AUTO_INCREMENT,
  `user_id` bigint(20) NOT NULL,
  `content_id` bigint(20) NOT NULL,
  `title` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `des` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `create_time` datetime(0) NULL DEFAULT NULL,
  `like` int(20) NULL DEFAULT NULL,
  `keep` int(20) NULL DEFAULT NULL,
  `article_img` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `article_author` varchar(20) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `tid` tinyint(4) NULL DEFAULT NULL,
  `pid` tinyint(4) NULL DEFAULT NULL,
  PRIMARY KEY (`article_id`) USING BTREE,
  INDEX `fk_user`(`user_id`) USING BTREE,
  INDEX `fk_article_content`(`content_id`) USING BTREE,
  CONSTRAINT `fk_article_content` FOREIGN KEY (`content_id`) REFERENCES `content` (`content_id`) ON DELETE CASCADE ON UPDATE CASCADE,
  CONSTRAINT `fk_user` FOREIGN KEY (`user_id`) REFERENCES `user` (`user_id`) ON DELETE CASCADE ON UPDATE CASCADE
) ENGINE = InnoDB AUTO_INCREMENT = 19 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Table structure for comment
-- ----------------------------
DROP TABLE IF EXISTS `comment`;
CREATE TABLE `comment`  (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `comment` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `article_id` bigint(20) NOT NULL,
  `comment_time` datetime(0) NOT NULL,
  `from_user` bigint(20) NOT NULL,
  `to_user` bigint(20) NULL DEFAULT NULL,
  `user_ico` varchar(200) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `from_nickname` varchar(20) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `to_nickname` varchar(20) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `like` bigint(20) NULL DEFAULT 0,
  `comment_main_id` bigint(20) NOT NULL,
  PRIMARY KEY (`id`) USING BTREE,
  INDEX `fk_art_comment`(`article_id`) USING BTREE,
  INDEX `fk_art_comment_user_ico`(`user_ico`) USING BTREE,
  CONSTRAINT `fk_art_comment` FOREIGN KEY (`article_id`) REFERENCES `article` (`article_id`) ON DELETE CASCADE ON UPDATE RESTRICT
) ENGINE = InnoDB AUTO_INCREMENT = 14 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Table structure for comment_main
-- ----------------------------
DROP TABLE IF EXISTS `comment_main`;
CREATE TABLE `comment_main`  (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `article_id` bigint(20) NOT NULL,
  `user_id` bigint(20) NOT NULL,
  `comment` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `comment_main_time` datetime(0) NOT NULL,
  `user_ico` varchar(200) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `nickname` varchar(20) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `like` bigint(20) NULL DEFAULT 0,
  PRIMARY KEY (`id`) USING BTREE,
  INDEX `fk_art_comment_main`(`article_id`) USING BTREE,
  INDEX `fk_art_user_ico`(`user_ico`) USING BTREE,
  CONSTRAINT `fk_art_comment_main` FOREIGN KEY (`article_id`) REFERENCES `article` (`article_id`) ON DELETE CASCADE ON UPDATE RESTRICT
) ENGINE = InnoDB AUTO_INCREMENT = 19 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Table structure for content
-- ----------------------------
DROP TABLE IF EXISTS `content`;
CREATE TABLE `content`  (
  `content_id` bigint(20) NOT NULL AUTO_INCREMENT,
  `content` longtext CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `user_id` bigint(20) NOT NULL,
  `content_time` datetime(0) NOT NULL,
  `content_key` varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  PRIMARY KEY (`content_id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 33 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Table structure for good
-- ----------------------------
DROP TABLE IF EXISTS `good`;
CREATE TABLE `good`  (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `cn` varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `en` varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `create_time` datetime(0) NOT NULL,
  `author` varchar(10) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 1 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Table structure for img
-- ----------------------------
DROP TABLE IF EXISTS `img`;
CREATE TABLE `img`  (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `article_id` bigint(20) NOT NULL,
  `bigPic` varchar(200) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `smallPic` varchar(200) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT '',
  PRIMARY KEY (`id`) USING BTREE,
  INDEX `fk_img`(`article_id`) USING BTREE,
  CONSTRAINT `fk_img` FOREIGN KEY (`article_id`) REFERENCES `article` (`article_id`) ON DELETE CASCADE ON UPDATE CASCADE
) ENGINE = InnoDB AUTO_INCREMENT = 1 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Table structure for user
-- ----------------------------
DROP TABLE IF EXISTS `user`;
CREATE TABLE `user`  (
  `user_id` bigint(20) NOT NULL AUTO_INCREMENT,
  `username` varchar(20) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `nickname` varchar(20) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `password` varchar(32) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `birthday` datetime(0) NULL DEFAULT NULL,
  `age` tinyint(3) UNSIGNED NULL DEFAULT 0,
  `phone` varchar(16) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `address` varchar(128) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `email` varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `sex` enum('男','女','保密') CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL DEFAULT '保密',
  `create_user_time` datetime(0) NULL DEFAULT NULL,
  `status` enum('Y','N','F') CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL DEFAULT 'N',
  `code` varchar(50) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `user_img` varchar(200) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT 'http://img.ambitiouscc.com/user/admin.jpg',
  `user_ico` varchar(200) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT 'http://img.ambitiouscc.com/user/admin.jpg',
  `user_des` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `user_comments` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `last_login` datetime(0) NULL DEFAULT NULL,
  PRIMARY KEY (`user_id`) USING BTREE,
  INDEX `user_ico`(`user_ico`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 34 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

SET FOREIGN_KEY_CHECKS = 1;
```
+++

## 总结

亲手实现了一个项目从零到发布可以访问，这样的心情还是很激动的，比如我现在就在百忙之中抽出了几个小时写完了这篇总结。

### 以后开发需要注意的问题

1. 不要指望最后还有多少时间，时间永远都是不够的，项目越早开始越好！复习越早开始越好

2. 项目的说明文档应该和项目同步进行制作，不要等到项目做完才做（因为大部分时间项目是做不完的）

3. 在debug的时候不要灰心，只有一个方法：

:::primary
**:rocket:测试发现问题** - :love_letter:查看logs日志文件 - 找到最准确的出错位置（通过输出的代码行）- :pushpin:调整代码（设置各个执行代码的输出语句判断） - :rainbow:重新发布 - **:rocket:测试比较问题**
:::

4. 及时的给队友分配任务，不布置任务他们是不会主动的，布置任务并设置例会时间。

### 后语

1. 可能不会有机会这样认真的制作网站，希望能够记住所有的经验和教训

2. 希望能够给开发设置阶段，没上一个台阶，记录当前的技术手段和所处的位置

3. **不断记录自己解决的问题，有的问题解决了还会继续困扰你！**

:::success
:heart::heart::heart:项目地址如下标签
:::

{% links %}
- site: 计算机与信息工程学院编程爱好者交流系统
  owner: 站长大大
  url: https://bbs.ambitiouscc.com/
  desc: 项目地址
  image: https://s3.ax1x.com/2020/12/23/rcua6J.jpg
  color: "#e9546b"
{% endlinks %}
