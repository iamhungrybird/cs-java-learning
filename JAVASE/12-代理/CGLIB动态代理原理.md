## 一 CGLIB介绍

CGLIB(Code Generation Library)是一个开源项目！是一个强大的，高性能，高质量的Code生成类库，

它可以在运行期扩展Java类与实现Java接口。Hibernate用它来实现PO(Persistent Object 持久化对象)字节码的动态生成。

CGLIB是一个强大的高性能的代码生成包。它广泛的被许多AOP的框架使用，例如Spring AOP为他们提供

方法的interception（拦截）。CGLIB包的底层是通过使用一个小而快的字节码处理框架ASM，来转换字节码并生成新的类。

除了CGLIB包，脚本语言例如Groovy和BeanShell，也是使用ASM来生成java的字节码。当然不鼓励直接使用ASM，

因为它要求你必须对JVM内部结构包括class文件的格式和指令集都很熟悉。

## 二 CGLIB动态代理实例

实现一个业务类，注意，这个业务类并没有实现任何接口：



```java
package com.jpeony.spring.proxy.cglib;

public class HelloService {
public HelloService() {
    System.out.println("HelloService构造");
}
 
/**
 * 该方法不能被子类覆盖,Cglib是无法代理final修饰的方法的
 */
final public String sayOthers(String name) {
    System.out.println("HelloService:sayOthers>>"+name);
    return null;
}
 
public void sayHello() {
    System.out.println("HelloService:sayHello");
}
}
```

自定义MethodInterceptor

```java
package com.jpeony.spring.proxy.cglib;

import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

/**

 * 自定义MethodInterceptor
   */
   public class MyMethodInterceptor implements MethodInterceptor{

   /**

    * sub：cglib生成的代理对象
    * method：被代理对象方法
    * objects：方法入参
    * methodProxy: 代理方法
      */
      @Override
      public Object intercept(Object sub, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
      System.out.println("======插入前置通知======");
      Object object = methodProxy.invokeSuper(sub, objects);
      System.out.println("======插入后者通知======");
      return object;
      }
      }
```


生成CGLIB代理对象调用目标方法：

```java
package com.jpeony.spring.proxy.cglib;

import net.sf.cglib.core.DebuggingClassWriter;
import net.sf.cglib.proxy.Enhancer;

public class Client {
    public static void main(String[] args) {
        // 代理类class文件存入本地磁盘方便我们反编译查看源码
        System.setProperty(DebuggingClassWriter.DEBUG_LOCATION_PROPERTY, "D:\\code");
        // 通过CGLIB动态代理获取代理对象的过程
        Enhancer enhancer = new Enhancer();
        // 设置enhancer对象的父类
        enhancer.setSuperclass(HelloService.class);
        // 设置enhancer的回调对象
        enhancer.setCallback(new MyMethodInterceptor());
        // 创建代理对象
        HelloService proxy= (HelloService)enhancer.create();
        // 通过代理对象调用目标方法
        proxy.sayHello();
    }
}
```



## 三 CGLIB动态代理源码分析

实现CGLIB动态代理必须实现MethodInterceptor(方法拦截器)接口，源码如下:

```java
/*

 * Copyright 2002,2003 The Apache Software Foundation
   *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
   *
 * http://www.apache.org/licenses/LICENSE-2.0
    *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
   */
   package net.sf.cglib.proxy;

/**

 * General-purpose {@link Enhancer} callback which provides for "around advice".
 * @author Juozas Baliuka <a href="mailto:baliuka@mwm.lt">baliuka@mwm.lt</a>
 * @version $Id: MethodInterceptor.java,v 1.8 2004/06/24 21:15:20 herbyderby Exp $
   */
   public interface MethodInterceptor
   extends Callback
   {
   /**
    * All generated proxied methods call this method instead of the original method.
    * The original method may either be invoked by normal reflection using the Method object,
    * or by using the MethodProxy (faster).
    * @param obj "this", the enhanced object
    * @param method intercepted Method
    * @param args argument array; primitive types are wrapped
    * @param proxy used to invoke super (non-intercepted method); may be called
    * as many times as needed
    * @throws Throwable any exception may be thrown; if so, super method will not be invoked
    * @return any value compatible with the signature of the proxied method. Method returning void will ignore this value.
    * @see MethodProxy
      */    
      public Object intercept(Object obj, java.lang.reflect.Method method, Object[] args,
                             MethodProxy proxy) throws Throwable;

}
```


这个接口只有一个intercept()方法，这个方法有4个参数：

1）obj表示增强的对象，即实现这个接口类的一个对象；

2）method表示要被拦截的方法；

3）args表示要被拦截方法的参数；

4）proxy表示要触发父类的方法对象；

在上面的Client代码中，通过Enhancer.create()方法创建代理对象，create()方法的源码：

```java
/**
     * Generate a new class if necessary and uses the specified
     * callbacks (if any) to create a new object instance.
     * Uses the no-arg constructor of the superclass.
     * @return a new instance
     */
    public Object create() {
        classOnly = false;
        argumentTypes = null;
        return createHelper();
    }
```


该方法含义就是如果有必要就创建一个新类，并且用指定的回调对象创建一个新的对象实例，

使用的父类的参数的构造方法来实例化父类的部分。核心内容在createHelper()中，源码如下:

```java
private Object createHelper() {
        preValidate();
        Object key = KEY_FACTORY.newInstance((superclass != null) ? superclass.getName() : null,
                ReflectUtils.getNames(interfaces),
                filter == ALL_ZERO ? null : new WeakCacheKey<CallbackFilter>(filter),
                callbackTypes,
                useFactory,
                interceptDuringConstruction,
                serialVersionUID);
        this.currentKey = key;
        Object result = super.create(key);
        return result;
    }
```


preValidate()方法校验callbackTypes、filter是否为空，以及为空时的处理。

通过newInstance()方法创建EnhancerKey对象，作为Enhancer父类AbstractClassGenerator.create()方法

创建代理对象的参数。

```java
protected Object create(Object key) {
        try {
            ClassLoader loader = getClassLoader();
            Map<ClassLoader, ClassLoaderData> cache = CACHE;
            ClassLoaderData data = cache.get(loader);
            if (data == null) {
                synchronized (AbstractClassGenerator.class) {
                    cache = CACHE;
                    data = cache.get(loader);
                    if (data == null) {
                        Map<ClassLoader, ClassLoaderData> newCache = new WeakHashMap<ClassLoader, ClassLoaderData>(cache);
                        data = new ClassLoaderData(loader);
                        newCache.put(loader, data);
                        CACHE = newCache;
                    }
                }
            }
            this.key = key;
            Object obj = data.get(this, getUseCache());
            if (obj instanceof Class) {
                return firstInstance((Class) obj);
            }
            return nextInstance(obj);
        } catch (RuntimeException e) {
            throw e;
        } catch (Error e) {
            throw e;
        } catch (Exception e) {
            throw new CodeGenerationException(e);
        }
    }
```


真正创建代理对象方法在nextInstance()方法中，该方法为抽象类AbstractClassGenerator的一个方法，签名如下：

abstract protected Object nextInstance(Object instance) throws Exception;

在子类Enhancer中实现，实现源码如下：



```java
    protected Object nextInstance(Object instance) {
        EnhancerFactoryData data = (EnhancerFactoryData) instance;
    if (classOnly) {
        return data.generatedClass;
    }
 
    Class[] argumentTypes = this.argumentTypes;
    Object[] arguments = this.arguments;
    if (argumentTypes == null) {
        argumentTypes = Constants.EMPTY_CLASS_ARRAY;
        arguments = null;
    }
    return data.newInstance(argumentTypes, arguments, callbacks);
}
```
看看data.newInstance(argumentTypes, arguments, callbacks)方法，

第一个参数为代理对象的构成器类型，第二个为代理对象构造方法参数，第三个为对应回调对象。

最后根据这些参数，通过反射生成代理对象，源码如下：

```java
/**
         * Creates proxy instance for given argument types, and assigns the callbacks.
         * Ideally, for each proxy class, just one set of argument types should be used,
         * otherwise it would have to spend time on constructor lookup.
         * Technically, it is a re-implementation of {@link Enhancer#createUsingReflection(Class)},
         * with "cache {@link #setThreadCallbacks} and {@link #primaryConstructor}"
         *
         * @see #createUsingReflection(Class)
         * @param argumentTypes constructor argument types
         * @param arguments constructor arguments
         * @param callbacks callbacks to set for the new instance
         * @return newly created proxy
         */
        public Object newInstance(Class[] argumentTypes, Object[] arguments, Callback[] callbacks) {
            setThreadCallbacks(callbacks);
            try {
                // Explicit reference equality is added here just in case Arrays.equals does not have one
                if (primaryConstructorArgTypes == argumentTypes ||
                        Arrays.equals(primaryConstructorArgTypes, argumentTypes)) {
                    // If we have relevant Constructor instance at hand, just call it
                    // This skips "get constructors" machinery
                    return ReflectUtils.newInstance(primaryConstructor, arguments);
                }
                // Take a slow path if observing unexpected argument types
                return ReflectUtils.newInstance(generatedClass, argumentTypes, arguments);
            } finally {
                // clear thread callbacks to allow them to be gc'd
                setThreadCallbacks(null);
            }
            }
```

最后生成代理对象：



将其反编译后代码如下：

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

package com.jpeony.spring.proxy.cglib;

import java.lang.reflect.Method;
import net.sf.cglib.core.ReflectUtils;
import net.sf.cglib.core.Signature;
import net.sf.cglib.proxy.Callback;
import net.sf.cglib.proxy.Factory;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

public class HelloService$$EnhancerByCGLIB$$be45efdd extends HelloService implements Factory {
    private boolean CGLIB$BOUND;
    public static Object CGLIB$FACTORY_DATA;
    private static final ThreadLocal CGLIB$THREAD_CALLBACKS;
    private static final Callback[] CGLIB$STATIC_CALLBACKS;
    private MethodInterceptor CGLIB$CALLBACK_0;
    private static Object CGLIB$CALLBACK_FILTER;
    private static final Method CGLIB$sayHello$0$Method;
    private static final MethodProxy CGLIB$sayHello$0$Proxy;
    private static final Object[] CGLIB$emptyArgs;
    private static final Method CGLIB$equals$1$Method;
    private static final MethodProxy CGLIB$equals$1$Proxy;
    private static final Method CGLIB$toString$2$Method;
    private static final MethodProxy CGLIB$toString$2$Proxy;
    private static final Method CGLIB$hashCode$3$Method;
    private static final MethodProxy CGLIB$hashCode$3$Proxy;
    private static final Method CGLIB$clone$4$Method;
    private static final MethodProxy CGLIB$clone$4$Proxy;

static void CGLIB$STATICHOOK1() {
    CGLIB$THREAD_CALLBACKS = new ThreadLocal();
    CGLIB$emptyArgs = new Object[0];
    Class var0 = Class.forName("com.jpeony.spring.proxy.cglib.HelloService$$EnhancerByCGLIB$$be45efdd");
    Class var1;
    Method[] var10000 = ReflectUtils.findMethods(new String[]{"equals", "(Ljava/lang/Object;)Z", "toString", "()Ljava/lang/String;", "hashCode", "()I", "clone", "()Ljava/lang/Object;"}, (var1 = Class.forName("java.lang.Object")).getDeclaredMethods());
    CGLIB$equals$1$Method = var10000[0];
    CGLIB$equals$1$Proxy = MethodProxy.create(var1, var0, "(Ljava/lang/Object;)Z", "equals", "CGLIB$equals$1");
    CGLIB$toString$2$Method = var10000[1];
    CGLIB$toString$2$Proxy = MethodProxy.create(var1, var0, "()Ljava/lang/String;", "toString", "CGLIB$toString$2");
    CGLIB$hashCode$3$Method = var10000[2];
    CGLIB$hashCode$3$Proxy = MethodProxy.create(var1, var0, "()I", "hashCode", "CGLIB$hashCode$3");
    CGLIB$clone$4$Method = var10000[3];
    CGLIB$clone$4$Proxy = MethodProxy.create(var1, var0, "()Ljava/lang/Object;", "clone", "CGLIB$clone$4");
    CGLIB$sayHello$0$Method = ReflectUtils.findMethods(new String[]{"sayHello", "()V"}, (var1 = Class.forName("com.jpeony.spring.proxy.cglib.HelloService")).getDeclaredMethods())[0];
    CGLIB$sayHello$0$Proxy = MethodProxy.create(var1, var0, "()V", "sayHello", "CGLIB$sayHello$0");
}
 
final void CGLIB$sayHello$0() {
    super.sayHello();
}
 
public final void sayHello() {
    MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
    if (var10000 == null) {
        CGLIB$BIND_CALLBACKS(this);
        var10000 = this.CGLIB$CALLBACK_0;
    }
 
    if (var10000 != null) {
        var10000.intercept(this, CGLIB$sayHello$0$Method, CGLIB$emptyArgs, CGLIB$sayHello$0$Proxy);
    } else {
        super.sayHello();
    }
}
 
final boolean CGLIB$equals$1(Object var1) {
    return super.equals(var1);
}
 
public final boolean equals(Object var1) {
    MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
    if (var10000 == null) {
        CGLIB$BIND_CALLBACKS(this);
        var10000 = this.CGLIB$CALLBACK_0;
    }
 
    if (var10000 != null) {
        Object var2 = var10000.intercept(this, CGLIB$equals$1$Method, new Object[]{var1}, CGLIB$equals$1$Proxy);
        return var2 == null ? false : (Boolean)var2;
    } else {
        return super.equals(var1);
    }
}
 
final String CGLIB$toString$2() {
    return super.toString();
}
 
public final String toString() {
    MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
    if (var10000 == null) {
        CGLIB$BIND_CALLBACKS(this);
        var10000 = this.CGLIB$CALLBACK_0;
    }
 
    return var10000 != null ? (String)var10000.intercept(this, CGLIB$toString$2$Method, CGLIB$emptyArgs, CGLIB$toString$2$Proxy) : super.toString();
}
 
final int CGLIB$hashCode$3() {
    return super.hashCode();
}
 
public final int hashCode() {
    MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
    if (var10000 == null) {
        CGLIB$BIND_CALLBACKS(this);
        var10000 = this.CGLIB$CALLBACK_0;
    }
 
    if (var10000 != null) {
        Object var1 = var10000.intercept(this, CGLIB$hashCode$3$Method, CGLIB$emptyArgs, CGLIB$hashCode$3$Proxy);
        return var1 == null ? 0 : ((Number)var1).intValue();
    } else {
        return super.hashCode();
    }
}
 
final Object CGLIB$clone$4() throws CloneNotSupportedException {
    return super.clone();
}
 
protected final Object clone() throws CloneNotSupportedException {
    MethodInterceptor var10000 = this.CGLIB$CALLBACK_0;
    if (var10000 == null) {
        CGLIB$BIND_CALLBACKS(this);
        var10000 = this.CGLIB$CALLBACK_0;
    }
 
    return var10000 != null ? var10000.intercept(this, CGLIB$clone$4$Method, CGLIB$emptyArgs, CGLIB$clone$4$Proxy) : super.clone();
}
 
public static MethodProxy CGLIB$findMethodProxy(Signature var0) {
    String var10000 = var0.toString();
    switch(var10000.hashCode()) {
    case -508378822:
        if (var10000.equals("clone()Ljava/lang/Object;")) {
            return CGLIB$clone$4$Proxy;
        }
        break;
    case 1535311470:
        if (var10000.equals("sayHello()V")) {
            return CGLIB$sayHello$0$Proxy;
        }
        break;
    case 1826985398:
        if (var10000.equals("equals(Ljava/lang/Object;)Z")) {
            return CGLIB$equals$1$Proxy;
        }
        break;
    case 1913648695:
        if (var10000.equals("toString()Ljava/lang/String;")) {
            return CGLIB$toString$2$Proxy;
        }
        break;
    case 1984935277:
        if (var10000.equals("hashCode()I")) {
            return CGLIB$hashCode$3$Proxy;
        }
    }
 
    return null;
}
 
public HelloService$$EnhancerByCGLIB$$be45efdd() {
    CGLIB$BIND_CALLBACKS(this);
}
 
public static void CGLIB$SET_THREAD_CALLBACKS(Callback[] var0) {
    CGLIB$THREAD_CALLBACKS.set(var0);
}
 
public static void CGLIB$SET_STATIC_CALLBACKS(Callback[] var0) {
    CGLIB$STATIC_CALLBACKS = var0;
}
 
private static final void CGLIB$BIND_CALLBACKS(Object var0) {
    HelloService$$EnhancerByCGLIB$$be45efdd var1 = (HelloService$$EnhancerByCGLIB$$be45efdd)var0;
    if (!var1.CGLIB$BOUND) {
        var1.CGLIB$BOUND = true;
        Object var10000 = CGLIB$THREAD_CALLBACKS.get();
        if (var10000 == null) {
            var10000 = CGLIB$STATIC_CALLBACKS;
            if (var10000 == null) {
                return;
            }
        }
 
        var1.CGLIB$CALLBACK_0 = (MethodInterceptor)((Callback[])var10000)[0];
    }
 
}
 
public Object newInstance(Callback[] var1) {
    CGLIB$SET_THREAD_CALLBACKS(var1);
    HelloService$$EnhancerByCGLIB$$be45efdd var10000 = new HelloService$$EnhancerByCGLIB$$be45efdd();
    CGLIB$SET_THREAD_CALLBACKS((Callback[])null);
    return var10000;
}
 
public Object newInstance(Callback var1) {
    CGLIB$SET_THREAD_CALLBACKS(new Callback[]{var1});
    HelloService$$EnhancerByCGLIB$$be45efdd var10000 = new HelloService$$EnhancerByCGLIB$$be45efdd();
    CGLIB$SET_THREAD_CALLBACKS((Callback[])null);
    return var10000;
}
 
public Object newInstance(Class[] var1, Object[] var2, Callback[] var3) {
    CGLIB$SET_THREAD_CALLBACKS(var3);
    HelloService$$EnhancerByCGLIB$$be45efdd var10000 = new HelloService$$EnhancerByCGLIB$$be45efdd;
    switch(var1.length) {
    case 0:
        var10000.<init>();
        CGLIB$SET_THREAD_CALLBACKS((Callback[])null);
        return var10000;
    default:
        throw new IllegalArgumentException("Constructor not found");
    }
}
 
public Callback getCallback(int var1) {
    CGLIB$BIND_CALLBACKS(this);
    MethodInterceptor var10000;
    switch(var1) {
    case 0:
        var10000 = this.CGLIB$CALLBACK_0;
        break;
    default:
        var10000 = null;
    }
 
    return var10000;
}
 
public void setCallback(int var1, Callback var2) {
    switch(var1) {
    case 0:
        this.CGLIB$CALLBACK_0 = (MethodInterceptor)var2;
    default:
    }
}
 
public Callback[] getCallbacks() {
    CGLIB$BIND_CALLBACKS(this);
    return new Callback[]{this.CGLIB$CALLBACK_0};
}
 
public void setCallbacks(Callback[] var1) {
    this.CGLIB$CALLBACK_0 = (MethodInterceptor)var1[0];
}
 
static {
    CGLIB$STATICHOOK1();
}
```
}
重点关注代理对象的sayHello方法：



从代理对象反编译源码可以知道，代理对象继承于HelloService，拦截器调用intercept()方法，

intercept()方法由自定义MyMethodInterceptor实现，所以，最后调用MyMethodInterceptor中

的intercept()方法，从而完成了由代理对象访问到目标对象的动态代理实现。
————————————————
版权声明：本文为CSDN博主「街灯下的小草」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/yhl_jxy/article/details/80633194