# apt-maven-plugin

This plugin deploys .deb artifacts into apt repository. It searches for attached artifacts which end with `.deb` extension. `.deb` files could be generated by [jdeb](https://github.com/tcurdt/jdeb). 

## 

## Usage

To publish .deb artifacts a distribution section must be configured:

```
<distributionManagement>
    <repository>
      <id>apt-repository</id>
      <name>Maven artifact repository to act as apt-repository</name>
      <url>https://example.com/release</url>
    </repository>
</distributionManagement>
```

And configure `apt-maven-plugin`:

```
<plugins>
...
  <plugin>
    <groupId>com.aerse.maven</groupId>
    <artifactId>apt-maven-plugin</artifactId>
    <version>1.7</version>
    <executions>
      <execution>
        <id>deploy</id>
        <goals>
          <goal>deploy</goal>
        </goals>
      </execution>
    </executions>
    <configuration>
      <component>main</component> <!-- Required. Example: main restricted universe. -->
      <codename>repo</codename> <!-- Required. Example: mycompany, repo -->
      <skip>false</skip> <!-- Not required. By default: false -->
      <sign>true</sign> <!-- Not required. By default: false. GPG-sign of "Release" file. Configuration is same as in maven-gpg-plugin -->
    </configuration>
  </plugin>
...
</plugins>
```

* component - type of repository. Ubuntu proposes the following naming for them:
  * main- Officially supported software. Recommended.
  * restricted - Supported software that is not available under a completely free license.
  * universe - Community maintained software, i.e. not officially supported software.
  * multiverse - Software that is not free.
* codename - codename of release. 


## Hints

It is possible to specify `.deb` file using command line. Configuration will be taken from `pom.xml`:

```
mvn apt:deploy -Dmaven.apt.file=target/file.deb
```

If package was signed once (using "sign=true" option), then un-sign (using "sign=false") will cause GPG checksum failure during ```apt-get update```.  Maven Wagon doesn't support "delete" operation, so I cannot remove "Release.gpg" file from apt repository. Please remove "Release.gpg" manually if you have to stop signing.

Works perfectly with [aws-maven](https://github.com/spring-projects/aws-maven "aws-maven"). Just configure distribution section to use `s3://bucketname`.

If you want to publish only .deb artifacts, then turn standard deploy plugin off:

```
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-deploy-plugin</artifactId>
    <version>2.7</version>
    <configuration>
      <skip>true</true>
    </configuration>
  </plugin>
```


