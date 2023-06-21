# GraalVM Native - Gradle

### Hello from NATIVE Vert.x!


* ./gradlew clean nativeCompile
* ./build/native/nativeCompile/graalvm_gradle
<br>Hello world!
* task "jar" will also create a fat jar
<br>java -jar build/libs/graalvm_gradle-1.0-SNAPSHOT.jar
<br>java -cp build/libs/graalvm_gradle-1.0-SNAPSHOT.jar io.vertx.core.Launcher run org.example.MainVerticle

Added bucketloads of, and ...dependencies

From https://www.graalvm.org/22.2/reference-manual/native-image/guides/build-with-reflection/
* mkdir -p META-INF/native-image
* $JAVA_HOME/bin/java -cp build/libs/graalvm_gradle-1.0-SNAPSHOT.jar -agentlib:native-image-agent=config-output-dir=META-INF/native-image io.vertx.core.Launcher run org.example.MainVerticle


* Caused by: org.graalvm.compiler.java.BytecodeParser$BytecodeParserError: com.or[jni-config.json](META-INF%2Fnative-image%2Fjni-config.json)acle.graal.pointsto.constraints.UnsupportedFeatureException: 
  <br>No instances of io.netty.util.internal.logging.Log4J2Logger are allowed in the image heap as this class should be initialized at image runtime. 
  <br>To see how this object got instantiated use 
  - --trace-oge bject-instantiation=io.netty.util.internal.logging.Log4J2Logger.
  
Fixed by pushing ALL of netty into runtime initialization 


```
graalvmNative {
    binaries {
        main {
            buildArgs.add('--initialize-at-run-time=io.netty')
        }
    }
}
```
* Disabled log4j
  -  //implementation group: 'org.apache.logging.log4j', name: 'log4j-core', version: '2.20.0'
* Ran profiling to generate reflect-config.json (in META-INF subtree)




### There be monsters below - you don't need to go any farther:

* Caused by: org.graalvm.compiler.java.BytecodeParser$BytecodeParserError: com.oracle.graal.pointsto.constraints.UnsupportedFeatureException: 
  <br>No instances of io.netty.util.internal.logging.Log4J2Logger are allowed in the image heap as this class should be initialized at image runtime. 
  <br>To see how this object got instantiated use 
  - --trace-object-instantiation=io.netty.util.internal.logging.Log4J2Logger.
* https://stackoverflow.com/questions/63328298/how-do-you-debug-a-no-instances-of-are-allowed-in-the-image-heap-when-buil
* --initialize-at-build-time=io.netty.util.internal.logging.Log4J2Logger
* --trace-object-instantiation=io.netty.util.internal.logging.Log4J2Logger.
  - Object has been initialized by the io.netty.channel.nio.AbstractNioChannel class initializer
  - Object has been initialized by the io.netty.channel.socket.nio.SelectorProviderUtil
  - Object has been initialized by the io.netty.util.internal.PlatformDependent
  - Object has been initialized by the io.netty.util.internal.PlatformDependent0
  - Object has been initialized by the io.netty.util.internal.SystemPropertyUtil
  - Object has been initialized by the io.netty.util.concurrent.FastThreadLocalThread
  - Object has been initialized by the io.netty.channel.ChannelHandlerMask
* https://medium.com/graalvm/instant-netty-startup-using-graalvm-native-image-generation-ed6f14ff7692
  - ss
* https://stackoverflow.com/questions/60654455/how-to-fix-try-avoiding-to-initialize-the-class-that-caused-initialization-wit
* ERROR StatusLogger Unable to load services for service class org.apache.logging.log4j.spi.Provider
<br>  java.lang.InternalError: com.oracle.svm.core.jdk.UnsupportedFeatureError: Defining hidden classes at runtime is not supported.
