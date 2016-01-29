---
layout: post
title:  "7行Javascript代码的异步函数库!"
date:   2015-07-13 16:58:14
categories: java maven
---

最近连续碰到几个java打包可执行jar文件的问题， 抽了一点时间research一下。由于maven社区太强大了， 还是没有搞懂 。


#### 打包成一个jar。（没有依赖第三方的类库)


{%highlight java %}
      <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-jar-plugin</artifactId>
          <configuration>
            <excludes>
              <exclude>log4j.properties</exclude>
            </excludes>
            <archive>
              <manifest>
                  <mainClass>zhaoyou.com.App</mainClass>
              </manifest>
            </archive>
          </configuration>
      </plugin>
{%endhighlight%}


#### 打包成一个jar。（包含第三方的类库合并成一个jar）


{%highlight java %}
      <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <archive>
            <manifest>
              <mainClass>zhaoyou.com.App</mainClass>
            </manifest>
          </archive>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
{% endhighlight%}


> 未完待续
