### http://wizardwu.tk/2022/10/22/Spring%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86.html

说到增强，要么是在编译期生成的class文件对目标进行增强，要么就是运行期间使用代理字节码文件对其增强；

### 静态代理

#### Aspectj增强 — 编译期

1.引入maven的插件

该插件会导致Lombok插件失效，比如通过@Data注解生成get set方法调用的地方会报红。

```
  <build>
        <plugins>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>aspectj-maven-plugin</artifactId>
                <version>1.14.0</version>
                <configuration>
                    <complianceLevel>1.8</complianceLevel>
                    <source>1.8</source>
                    <target>1.8</target>
                    <showWeaveInfo>true</showWeaveInfo>
                    <Xlint>ignore</Xlint>
                    <encoding>UTF-8</encoding>
                    <skip>true</skip>
                </configuration>
                <executions>
                    <execution>
                        <configuration>
                            <skip>false</skip>
                        </configuration>
                        <goals>
                            <goal>compile</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```

2.编写测试代码

```
//1.声明切面
@Aspect
public class MyAspect {
    @Before("execution (* com.dev.wizard.proxy.aspectj.TestService.testService())")  //要求是方法的全路径
    public void before(){
        System.out.println("before......");
    }
}
//2.声明被切入的方法，静态方法也是可以增强的
public class TestService {
    public void testService(){
        System.out.println("testService");
    }
}
//3.测试主方法
public class TestMain {
    public static void main(String[] args) {
        TestService testService = new TestService();
        testService.testService();
    }
}
//4.输出结果
before......
testService
```

上述主要是对MyAspect/TestService类在编译期间修改成了生成的class文件，class文件如下：

```java
@Aspect
public class MyAspect {
    public MyAspect() {
    }

    @Before("execution (* com.dev.wizard.proxy.aspectj.TestService.testService())")
    public void before() {
        System.out.println("before......");
    }

    public static MyAspect aspectOf() {
        if (ajc$perSingletonInstance == null) {
            throw new NoAspectBoundException("com.dev.wizard.proxy.aspectj.MyAspect", ajc$initFailureCause);
        } else {
            return ajc$perSingletonInstance;
        }
    }

    public static boolean hasAspect() {
        return ajc$perSingletonInstance != null;
    }

    static {
        try {
            ajc$postClinit();
        } catch (Throwable var1) {
            ajc$initFailureCause = var1;
        }

    }
}


public class TestService {
    public TestService() {
    }

    public void testService() {
        MyAspect.aspectOf().before();
        System.out.println("testService");
    }
}
```

注意：

由于IDEA使用Javac编译，会导致插件没有生效，你可以通过设置去让插件生效，也可以使用maven的complie方式。 ![image-20220814214841612](http://wizardwu.tk/assets/picture/2022-10/image-20220814214841612.png)

#### 2.Aspectj增强 — 类加载期间(LTW)

基于上述的代码不变，去除pom文件的maven插件，然后再classpath路径下的META-INF文件夹下建立aop.xml文件，该文件用于在程序启动时读取对那些路径下的文件做切面。

```
<?xml version="1.0" encoding="UTF-8"?>
<aspectj>
    <aspects>
        <!-- 1.切面类 -->
        <aspect name="com.dev.wizard.springboot.proxy.agent.MyAspect" />
    </aspects>
    <weaver options="-Xset:weaveJavaxPackages=true">
        <!-- 2.指定需要进行织入操作的目标类范围 -->
        <include within="com.dev.wizard.springboot.proxy.agent.*" />
    </weaver>
</aspectj>
```

这里是我的文件结构： ![image-20220814215536390](http://wizardwu.tk/assets/picture/2022-10/image-20220814215536390.png)

在项目启动时需要配置参数：`-javaagent:F:/Repository/maven_respository/org/aspectj/aspectjweaver/1.9.7/aspectjweaver-1.9.7.jar`，这里是我配置的绝对路径，大家也可以把这个jar包拷贝到项目中配置一个相对路径。例如在项目路径下创建一个common的文件夹(该文件夹和src齐平)，然后拷贝jar包到该文件夹下面，这时你可以配置成`-javaagent:./common/aspectjweaver-1.9.7.jar`

该方法不同于代理模式生成的代理类调用

```
@Aspect
public class MyAspect {

    @Before("execution (* com.dev.wizard.proxy.agent.TestService.*()  )")
    public void before(){
        System.out.println("before......");
    }
}

@Service
public class TestService {

    public void first(){
        System.out.println("first");
        this.second();

    }

    public void second(){
        System.out.println("second");
    }
}
//使用LTW 可以对this这种调用也会执行切面的逻辑，此处输出如下：
before......
first
before......
second
```

如果上述使用的是代理类的方式就不会触发second的切面逻辑，因为代理类必须要用代理类调用需要切面的方法，这样才可以对目标进行增强。

通过arthas工具看看类加载对这个目标类做了什么？

windows使用cmd命令打开终端(powershell不支持下载命令)：`curl -O https://arthas.aliyun.com/arthas-boot.jar`

```
       @Service
       public class TestService {
           public void first() {
/*10*/         MyAspect.aspectOf().before();
               System.out.println("first");
/*11*/         this.second();
           }

           public void second() {
/*16*/         MyAspect.aspectOf().before();
               System.out.println("second");
           }
       }
从反编译后的代码可以，两个方法内部都会执行before的方法。
```

上述的两种切面，是从执行的代码层面来进行改变进而实现了静态的AOP。
