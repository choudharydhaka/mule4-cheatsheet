```xml
<mirror>
  <id>nexus</id>
  <mirrorOf>*</mirrorOf>
  <url>http://internal/nexus/content/repositories/thirdparty</url>
</mirror>

 <mirror>
  <id>google</id>
  <mirrorOf>google</mirrorOf>
  <url>http://google-maven-repository.googlecode.com/svn/repository</url>
</mirror>  
 ```

 ## then add internal & external repo

 ```xml

 <profile>
     <id>nexus</id>
  <repositories>

    <repository>
      <id>central</id>
      <name>central</name>
      <url>http://internal/nexus/content/repositories/thirdparty</url>
    </repository>


    <repository>
      <id>google</id>
      <name>google</name>
      <url>http://google-maven-repository.googlecode.com/svn/repository</url>
    </repository>

  </repositories>
</profile>

```