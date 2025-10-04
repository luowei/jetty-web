# jetty-web

一个整合 Spring MVC、JSP 与 JSTL 的嵌入式 Jetty 8 Web 应用项目

## 项目简介

这是一个轻量级的 Java Web 应用示例项目，展示了如何使用嵌入式 Jetty 服务器结合 Spring MVC 框架构建 Web 应用。该项目可以直接作为可执行的 JAR 文件运行，无需外部 Web 容器，非常适合快速开发和部署。

## 技术栈

- **Java**: JDK 1.6+
- **Web 服务器**: Jetty 8.1.8.v20121106（嵌入式）
- **Web 框架**: Spring MVC 3.1.2.RELEASE
- **视图技术**: JSP + JSTL 1.2
- **构建工具**: Maven 3.x
- **依赖注入**: Spring Context
- **国际化**: Spring i18n

## 项目结构

```
jetty-web/
├── pom.xml                                          # Maven 项目配置文件
├── README.md                                        # 项目说明文档
└── src/
    ├── main/
    │   └── java/
    │       ├── com/rootls/
    │       │   ├── Main.java                        # 应用程序入口
    │       │   ├── WebServer.java                   # Jetty 服务器配置类
    │       │   └── web/
    │       │       └── Home.java                    # Spring MVC 控制器
    │       └── META-INF/webapp/
    │           └── WEB-INF/
    │               ├── web.xml                      # Web 应用配置文件
    │               ├── application-context.xml      # Spring 应用上下文配置
    │               ├── web-context.xml              # Spring Web MVC 配置
    │               ├── i18n/
    │               │   └── messages.properties      # 国际化资源文件
    │               └── views/
    │                   └── index.jsp                # 首页视图
    └── test/
        └── java/
            └── com/rootls/
                └── IDE.java                         # IDE 运行检测类
```

## 主要功能

### 1. 嵌入式 Jetty 服务器
- 无需外部 Tomcat 或其他 Web 容器
- 可配置端口（默认 8000）和绑定接口
- 支持线程池配置（最小 10 线程，最大 100 线程）
- 内置 NCSA 格式的访问日志记录

### 2. Spring MVC 集成
- 基于注解的控制器（`@Controller`）
- 自动组件扫描（`com.rootls.web` 包）
- JSP 视图解析器配置
- 静态资源映射（图片、CSS、JavaScript）

### 3. 智能运行模式检测
- **IDE 模式**: 直接从源码目录加载 Web 资源
- **生产模式**: 从打包的 shaded JAR 中加载资源
- 自动检测运行环境并调整资源加载路径

### 4. 国际化支持
- 使用 Spring 的 i18n 消息机制
- 可重载的资源文件（缓存时间 30 秒）
- UTF-8 编码支持

## 使用方法

### 前置要求
- JDK 1.6 或更高版本
- Maven 3.x

### 编译项目
```bash
cd /Users/luowei/projects/My_Java/repos/jetty-web
mvn clean package
```

### 运行方式

#### 方式一：在 IDE 中运行
直接运行 `com.rootls.Main` 类的 main 方法

#### 方式二：使用 Maven 运行
```bash
mvn exec:java -Dexec.mainClass="com.rootls.Main"
```

#### 方式三：运行打包的 JAR 文件
```bash
java -jar target/webapp-1.0-SNAPSHOT.jar
```

### 访问应用
启动成功后，在浏览器中访问：
```
http://localhost:8000/
```

将看到首页显示 "Hi" 消息（来自 messages.properties 配置）。

## 依赖说明

### 核心依赖

#### Spring 框架
- `spring-context (3.1.2.RELEASE)`: Spring 核心容器和依赖注入
- `spring-webmvc (3.1.2.RELEASE)`: Spring MVC Web 框架

#### Jetty 服务器
- `jetty-server (8.1.8)`: Jetty 核心服务器
- `jetty-servlet (8.1.8)`: Servlet 支持
- `jetty-webapp (8.1.8)`: Web 应用支持
- `jetty-servlets (8.1.8)`: 额外的 Servlet 功能

#### JSP 和 JSTL
- `jetty-jsp (8.1.4)`: JSP 页面编译和执行支持
- `jstl (1.2)`: JSP 标准标签库

### Maven 插件

- `maven-compiler-plugin`: Java 编译（目标版本 1.6）
- `maven-shade-plugin`: 打包为包含所有依赖的可执行 JAR
- `build-helper-maven-plugin`: 添加额外的资源目录
- `maven-source-plugin`: 生成源码 JAR

## 配置说明

### 服务器配置
在 `WebServer.java` 中可以修改以下配置：
- **端口号**: 默认 8000（在 Main.java 中设置）
- **线程池**: 最小 10 线程，最大 100 线程
- **访问日志路径**: `./var/logs/access/yyyy_mm_dd.request.log`
- **日志保留天数**: 90 天

### Web 应用配置
- **上下文路径**: `/`（根路径）
- **视图文件位置**: `/WEB-INF/views/`
- **视图文件后缀**: `.jsp`
- **静态资源映射**:
  - `/i/**` → 图片资源
  - `/c/**` → CSS 样式
  - `/s/**` → JavaScript 脚本

## 开发说明

### 添加新的控制器
1. 在 `com.rootls.web` 包下创建新的类
2. 使用 `@Controller` 注解标注
3. 使用 `@RequestMapping` 定义路由映射

示例：
```java
@Controller
public class MyController {
    @RequestMapping("/hello")
    public ModelAndView hello() {
        return new ModelAndView("hello");
    }
}
```

### 添加新的视图
在 `src/main/java/META-INF/webapp/WEB-INF/views/` 目录下创建 JSP 文件。

### 修改国际化消息
编辑 `src/main/java/META-INF/webapp/WEB-INF/i18n/messages.properties` 文件。

## 注意事项

1. **资源文件位置**: 如果运行时找不到配置文件与资源文件，需要将 `META-INF` 文件夹拷贝到 ClassPath 下（即生成的 `classes` 或 `target/classes` 目录下）

2. **日志目录**: 确保应用有权限在 `./var/logs/access/` 目录创建日志文件

3. **端口占用**: 如果 8000 端口已被占用，可以在 `Main.java` 中修改端口号

4. **打包注意**: 使用 maven-shade-plugin 打包时会自动合并 Spring 的配置文件（spring.handlers 和 spring.schemas）

## 参考资源

**视频讲解**

[集成Spring-MVC内嵌Jetty的web应用视频](http://www.tudou.com/programs/view/IJaTf3bJrVc/)

## 作者信息

- **作者**: Steve Liles
- **GroupId**: com.sjl
- **ArtifactId**: webapp
- **版本**: 1.0-SNAPSHOT

## 许可证

请参考项目源代码中的许可证信息。
