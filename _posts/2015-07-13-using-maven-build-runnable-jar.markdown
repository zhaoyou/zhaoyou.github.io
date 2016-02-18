---
layout: post
title:  "使用maven构造可执行jar项目!"
date:   2015-07-13 16:58:14
categories: java maven
---

最近连续碰到几个java打包可执行jar文件的问题， 抽了一点时间research一下。

__目标__

- 使用SpringMVC和内嵌Jetty


#### pom.xml 文件的build关键部分。


{%highlight xml %}

<build>
        <resources>
            <resource>
                <targetPath>webapp</targetPath>
                <directory>src/main/webapp</directory>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <targetPath>${project.build.directory}</targetPath>
            </resource>
            <resource>
                <directory>src/main/script</directory>
                <targetPath>${project.build.directory}</targetPath>
            </resource>
        </resources>

        <finalName>ticketSystem</finalName>

        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.1</version>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                        <configuration>
                            <createDependencyReducedPom>false</createDependencyReducedPom>
                            <transformers>
                                <transformer
                                        implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <manifestEntries>
                                        <Main-Class>com.sby.EmbeddedJetty</Main-Class>
                                        <Class-Path>.</Class-Path>
                                    </manifestEntries>
                                </transformer>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer">
                                    <resource>META-INF/spring.handlers</resource>
                                </transformer>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer">
                                    <resource>META-INF/spring.schemas</resource>
                                </transformer>

                                <transformer implementation="org.apache.maven.plugins.shade.resource.DontIncludeResourceTransformer">
                                    <resource>log4j.xml</resource>
                                </transformer>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.DontIncludeResourceTransformer">
                                    <resource>config.properties</resource>
                                </transformer>
                            </transformers>
                            <filters>
                                <filter>
                                    <artifact>*:*</artifact>
                                    <excludes>
                                        <exclude>META-INF/*.SF</exclude>
                                        <exclude>META-INF/*.DSA</exclude>
                                        <exclude>META-INF/*.RSA</exclude>
                                    </excludes>
                                </filter>
                            </filters>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.6</source>
                    <target>1.6</target>
                </configuration>
                <version>3.2</version>
            </plugin>
            <plugin>
                <artifactId>maven-surefire-plugin</artifactId>
                <configuration>
                    <includes>
                        <include>**/*Tests.java</include>
                    </includes>
                </configuration>
                <version>2.18.1</version>
            </plugin>
            <plugin>
                <groupId>org.mortbay.jetty</groupId>
                <artifactId>jetty-maven-plugin</artifactId>
                <version>8.1.3.v20120416</version>
                <configuration>
                    <webApp>
                        <contextPath>/</contextPath>
                    </webApp>
                    <connectors>
                        <connector implementation="org.eclipse.jetty.server.nio.SelectChannelConnector">
                            <port>8086</port>
                        </connector>
                    </connectors>
                    <scanIntervalSeconds>5</scanIntervalSeconds>
                </configuration>
            </plugin>
        </plugins>
    </build>
{%endhighlight%}


#### 集成springMVC与jetty的关键Java代码部分


{%highlight java %}

private static final Logger logger = LoggerFactory.getLogger(EmbeddedJetty.class);
    private static final int DEFAULT_PORT = 8080;
    private static final String CONTEXT_PATH = "/";
    private static final String MAPPING_URL = "/";
    private static final String DEFAULT_PROFILE = "dev";

    public static void main(String[] args) throws Exception {
        new EmbeddedJetty().startJetty(getPortFromArgs(args));
    }

    private static int getPortFromArgs(String[] args) {
        if (args.length > 0) {
            try {
                return Integer.valueOf(args[0]);
            } catch (NumberFormatException ignore) {
            }
        }
        logger.debug("No server port configured, falling back to {}", DEFAULT_PORT);
        return DEFAULT_PORT;
    }

    private void startJetty(int port) throws Exception {
        logger.debug("Starting server at port {}", port);
        Server server = new Server(port);
        server.setHandler(getServletContextHandler(getContext()));
        server.start();
        logger.info("Server started at port {}", port);
        server.join();
    }

    private static ServletContextHandler getServletContextHandler(WebApplicationContext context) throws IOException {
        //ServletContextHandler contextHandler = new ServletContextHandler();
        WebAppContext contextHandler = new WebAppContext();
        //contextHandler.setErrorHandler(null);
        contextHandler.setContextPath(CONTEXT_PATH);
        contextHandler.addServlet(new ServletHolder(new DispatcherServlet(context)), MAPPING_URL);
        contextHandler.addEventListener(new ContextLoaderListener(context));
        contextHandler.setResourceBase(new ClassPathResource("webapp").getURI().toString());
        return contextHandler;
    }

    private static WebApplicationContext getContext() {
        XmlWebApplicationContext ctx = new XmlWebApplicationContext();
        ctx.setConfigLocation("classpath:webapp/WEB-INF/mvc-dispatcher-servlet.xml");
        ctx.getEnvironment().setDefaultProfiles(DEFAULT_PROFILE);
        return ctx;
    }

{% endhighlight%}


> 未完待续
