# maven 命令


## 问题收集
### 导入第三方jar到远程仓库
- 无POM问题

```text
org.apache.maven.lifecycle.MissingProjectException: The goal you specified requires a project to execute but there is no POM in this directory (/Users/admin/.m2/repository/xx/xx/xx/1.0). Please verify you invoked Maven from the correct directory.
```

- 解决命令，使用双引号

```bash
mvn deploy:deploy-file -X "-DgroupId=xx.xx" \
"-DartifactId=xx" \
"-Dversion=1.0.0" \
"-Dpackaging=jar" \
"-Dfile=jar_name.jar"  \
"-Durl=配置文件中distributionManagement>repository>url地址" \
"-DrepositoryId=配置文件中distributionManagement>repository>id" \
--settings /Library/apache-maven-3.8.6/settings.xml(多个配置文件可以指定)
```
