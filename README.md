# XJar [![](https://jitpack.io/v/core-lib/xjar.svg)](https://jitpack.io/#core-lib/xjar)
GitHub: https://github.com/core-lib/xjar
### Spring Boot JAR 安全加密运行工具，同时支持的原生JAR。
### 基于对JAR包内资源的加密以及拓展ClassLoader来构建的一套程序加密启动，动态解密运行的方案，避免源码泄露或反编译。

## 功能特性
* 无需侵入代码，只需要把编译好的JAR包通过工具加密即可。
* 完全内存解密，杜绝源码以及字节码泄露或反编译。
* 支持所有JDK内置加解密算法。
* 可选择需要加解密的字节码或其他资源文件，避免计算资源浪费。

## 环境依赖
JDK 1.7 +

## 使用步骤

```xml
<project>
    <!-- 设置 jitpack.io 仓库 -->
    <repositories>
        <repository>
            <id>jitpack.io</id>
            <url>https://www.jitpack.io</url>
        </repository>
    </repositories>
    <!-- 添加 XJar 依赖 -->
    <dependencies>
        <dependency>
            <groupId>com.github.core-lib</groupId>
            <artifactId>xjar</artifactId>
            <version>LATEST_VERSION</version>
        </dependency>
    </dependencies>
</project>
```

```java
// Spring-Boot Jar包加密
public static void main(String[] args) {
    String password = "io.xjar";
    File plaintext = new File("/path/to/read/plaintext.jar");
    File encrypted = new File("/path/to/save/encrypted.jar");
    XBoot.encrypt(plaintext, encrypted, password);
}
```

```java
// Spring-Boot Jar包解密
public static void main(String[] args) {
    String password = "io.xjar";
    File encrypted = new File("/path/to/read/encrypted.jar");
    File decrypted = new File("/path/to/save/decrypted.jar");
    XBoot.decrypt(encrypted, decrypted, password);
}
```

```java
// Jar包加密
public static void main(String[] args) {
    String password = "io.xjar";
    File plaintext = new File("/path/to/read/plaintext.jar");
    File encrypted = new File("/path/to/save/encrypted.jar");
    XJar.encrypt(plaintext, encrypted, password);
}
```

```java
// Jar包解密
public static void main(String[] args) {
    String password = "io.xjar";
    File encrypted = new File("/path/to/read/encrypted.jar");
    File decrypted = new File("/path/to/save/decrypted.jar");
    XJar.decrypt(encrypted, decrypted, password);
}
```

```text
// 命令行运行JAR 然后在提示输入密码的时候输入密码后按回车即可正常启动
java -jar /path/to/encrypted.jar
//也可以通过传参的方式直接启动
java -jar /path/to/encrypted.jar --xjar.password=PASSWORD
```

## 参数说明
* --xjar.algorithm  加解密算法名称，缺省为AES，支持JDK所有内置算法，如AES / DES ...
* --xjar.keysize    密钥长度，缺省为128，根据不同的算法选取不同的密钥长度。
* --xjar.ivsize     向量长度，缺省为128，根据不同的算法选取不同的向量长度。
* --xjar.password   密码

## 进阶用法
```java
// 只加密自身项目及相关模块的源码不加密第三方依赖，可以通过XEntryFilter来定制需要加密的JAR包内资源
public static void main(String[] args) {
    String password = "io.xjar";
    File plaintext = new File("/path/to/read/plaintext.jar");
    File encrypted = new File("/path/to/save/encrypted.jar");
    XBoot.encrypt(plaintext, encrypted, password, (entry) -> {
        String name = entry.getName();
        return name.startsWith("BOOT-INF/classes/") || name.startsWith("BOOT-INF/lib/io-xjar-");
    });
}
```

## 注意事项
```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <!-- 请将executable参数删除或设置为false，目前还不能支持对该模式Jar的加密！后面将会支持该方式的打包。
    <configuration>
        <executable>true</executable>
    </configuration>
    -->
</plugin>
```

## 插件集成
[XJar-Maven-Plugin](https://github.com/core-lib/xjar-maven-plugin)
GitHub: https://github.com/core-lib/xjar-maven-plugin
```xml
<project>
    <!-- 设置 jitpack.io 插件仓库 -->
    <pluginRepositories>
        <pluginRepository>
            <id>jitpack.io</id>
            <url>https://jitpack.io</url>
        </pluginRepository>
    </pluginRepositories>
    <!-- 添加 XJar Maven 插件 -->
    <build>
        <plugins>
            <plugin>
                <groupId>com.github.core-lib</groupId>
                <artifactId>xjar-maven-plugin</artifactId>
                <version>LATEST_VERSION</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>spring-boot</goal>
                        </goals>
                        <phase>package</phase>
                        <configuration>
                            <password>io.xjar</password>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```
#### 也可以通过Maven命令执行
```text
mvn xjar:spring-boot -Dxjar.password=io.xjar
mvn xjar:jar -Dxjar.password=io.xjar -Dxjar.targetDir=/directory/to/save/target.xjar
```

## 命令目标
| 目标名称 | 目标说明 |
| :------- | :------ |
| spring-boot | Spring-Boot项目或模块使用 |
| jar | 普通jar项目或模块使用 |

## 参数说明
| 参数名称 | 命令参数名称 | 参数说明 | 参数类型 | 缺省值 | 示例值 |
| :------ | :----------- | :------ | :------ | :----- | :----- |
| password | -Dxjar.password | 密码字符串 | String | 必须 | 任意字符串，io.xjar |
| algorithm | -Dxjar.algorithm | 加密算法名称 | String | AES | JDK内置加密算法，如：AES / DES |
| keySize | -Dxjar.keySize | 密钥长度 | int | 128 | 根据加密算法而定，56，128，256 |
| ivSize | -Dxjar.ivSize | 密钥向量长度 | int | 128 | 根据加密算法而定，128 |
| sourceDir | -Dxjar.sourceDir | 源jar所在目录 | File | ${project.build.directory} | 文件目录 |
| sourceJar | -Dxjar.sourceJar | 源jar名称 | String | ${project.build.finalName}.jar | 文件名称 |
| targetDir | -Dxjar.targetDir | 目标jar存放目录 | File | ${project.build.directory} | 文件目录 |
| targetJar | -Dxjar.targetJar | 目标jar名称 | String | ${project.build.finalName}.xjar | 文件名称 |
| includes | -Dxjar.includes | 需要加密的包内资源 | String[] | 无 | BOOT-INF/classes/** , BOOT-INF/lib/xjar-*.jar , 支持Ant表达式 |
| excludes | -Dxjar.excludes | 无需加密的包内资源 | String[] | 无 | BOOT-INF/classes/** , BOOT-INF/lib/xjar-*.jar , 支持Ant表达式 |

#### 注意：
当 includes 和 excludes 同时使用是，excludes 将会失效！更多文档请点击：[XJar-Maven-Plugin](https://github.com/core-lib/xjar-maven-plugin)

## 版本记录
* v1.0.9
    1. 修复对Spring-Boot 版本依赖的bug
* v1.0.8
    1. 支持以Maven插件方式集成
* v1.0.7
    1. 将sprint-boot-loader依赖设为provide
    2. 将XEntryFilter#filter(E entry); 变更为XEntryFilter#filtrate(E entry);
    3. 将Encryptor/Decryptor的构造函数中接收多个过滤器参数变成接收一个，外部提供XEntryFilters工具类来实现多过滤器混合成一个，避免框架自身的逻辑限制了使用者的过滤逻辑实现。
* v1.0.6
    1. 采用[LoadKit](https://github.com/core-lib/loadkit)作为资源加载工具
* v1.0.5
    1. 支持并行类加载，需要JDK1.7+的支持，可提升多线程环境类加载的效率
    2. Spring-Boot JAR 包加解密增加一个安全过滤器，避免无关资源被加密造成无法运行
    3. XBoot / XJar 工具类中增加多个按文件路径加解密的方法，提升使用便捷性
* v1.0.4 小优化
* v1.0.3 增加Spring-Boot的FatJar加解密时的缺省过滤器，避免由于没有提供过滤器时加密后的JAR包不能正常运行。
* v1.0.2 修复中文及空格路径的问题
* v1.0.1 升级detector框架
* v1.0.0 第一个正式版发布

## 协议声明
[Apache-2.0](http://www.apache.org/licenses/LICENSE-2.0)

## 联系作者
QQ 646742615 不会钓鱼的兔子
