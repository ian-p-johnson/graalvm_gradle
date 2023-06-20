# GraalVM Native - Gradle

* ./gradlew clean nativeCompile
* ./build/native/nativeCompile/graalvm_gradle
<br>Hello world!

Added bucketloads of, and ...dependencies 

* Caused by: org.graalvm.compiler.java.BytecodeParser$BytecodeParserError: com.oracle.graal.pointsto.constraints.UnsupportedFeatureException: 
  <br>No instances of io.netty.util.internal.logging.Log4J2Logger are allowed in the image heap as this class should be initialized at image runtime. 
  <br>To see how this object got instantiated use 
  - --trace-object-instantiation=io.netty.util.internal.logging.Log4J2Logger.
  
Fixed by pushing ALL of netty into runtime initialization 

Not tested - but it does build an image at least

```
graalvmNative {
    binaries {
        main {
            buildArgs.add('--initialize-at-run-time=io.netty')
        }
    }
}
```

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
