maven的配置信息都在`sttings.xml`文件中
### 路径
- 本地的路径：`apache-maven-3.8.6/conf/`目录下
- idea的路径：`IntelliJ IDEA 2021.1/plugins/maven/lib/maven3/conf`目录下

### 配置本地maven仓库路径
在maven下载的文件会保存在我们的本地maven仓库，默认地址：`${user.home}/.m2/repository`
```xml
<!-- 举例子配置在E:/maven-repo -->
<settings>
	<localRepository>E:/maven-repo</localRepository>
</settings>
```

### 配置阿里云镜像仓库
maven的中心仓库地址是外网下载东西很慢，所以配置国内阿里云的镜像仓库地址
在`<settings></settings>`标签中添加以下内容
```xml
<mirrors>
	<mirror>
	  <id>nexus-aliyun</id>
	  <mirrorOf>central</mirrorOf>
	  <name>Nexus aliyun</name>
	  <url>http://maven.aliyun.com/nexus/content/groups/public</url>
	</mirror>
</mirrors>
```

### 配置JDK版本
maven中默认的jdk版本是1.4，根据自己的情况去修改jdk版本
```xml
<profile>    
      <id>jdk-1.8</id>    
      <activation>    
        <activeByDefault>true</activeByDefault>    
        <jdk>1.8</jdk>    
      </activation>    
      <properties>    
        <maven.compiler.source>1.8</maven.compiler.source>    
        <maven.compiler.target>1.8</maven.compiler.target>    
<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>   
      </properties>    
</profile>
```
