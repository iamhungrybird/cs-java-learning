# 一 JDK动态代理

在了解JDK动态代理前，有需要可以了解下代理模式。

参考：https://blog.csdn.net/yhl_jxy/article/details/52679882；

天天的都听到人们说JDK动态代理，听上去感觉好屌的样子，为什么要叫JDK动态代理？

是因为代理对象是由JDK动态生成的，而不像静态代理方式写死代理对象和被代理类，不灵活。

JDK动态代理基于拦截器和反射来实现。

JDK代理是不需要第三方库支持的，只需要JDK环境就可以进行代理，使用条件：

1）必须实现InvocationHandler接口；

2）使用Proxy.newProxyInstance产生代理对象；

3）被代理的对象必须要实现接口；

# 二 JDK动态代理实例

使用JDK动态代理的五大步骤：

1）通过实现InvocationHandler接口来自定义自己的InvocationHandler；

2）通过Proxy.getProxyClass获得动态代理类；

3）通过反射机制获得代理类的构造方法，方法签名为getConstructor(InvocationHandler.class)；

4）通过构造函数获得代理对象并将自定义的InvocationHandler实例对象传为参数传入；

5）通过代理对象调用目标方法；

IHello接口



```java
package com.jpeony.spring.proxy.jdk;

public interface IHello {
    void sayHello();
}
HelloImpl接口实现

package com.jpeony.spring.proxy.jdk;

public class HelloImpl implements IHello {
    @Override
    public void sayHello() {
        System.out.println("Hello world!");
    }
}
MyInvocationHandler(实现InvocationHandler接口)

package com.jpeony.spring.proxy.jdk;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class MyInvocationHandler implements InvocationHandler {
/** 目标对象 */
private Object target;
 
public MyInvocationHandler(Object target){
    this.target = target;
}
 
@Override
public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    System.out.println("------插入前置通知代码-------------");
    // 执行相应的目标方法
    Object rs = method.invoke(target,args);
    System.out.println("------插入后置处理代码-------------");
    return rs;
}
}

MyProxyTest(Client)

package com.jpeony.spring.proxy.jdk;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Proxy;

/**

 * 使用JDK动态代理的五大步骤:
 * 1.通过实现InvocationHandler接口来自定义自己的InvocationHandler;
 * 2.通过Proxy.getProxyClass获得动态代理类
 * 3.通过反射机制获得代理类的构造方法，方法签名为getConstructor(InvocationHandler.class)
 * 4.通过构造函数获得代理对象并将自定义的InvocationHandler实例对象传为参数传入
 * 5.通过代理对象调用目标方法
   */
   public class MyProxyTest {
public static void main(String[] args)
        throws NoSuchMethodException, IllegalAccessException, InstantiationException, InvocationTargetException {
    // =========================第一种==========================
    // 1、生成$Proxy0的class文件
    System.getProperties().put("sun.misc.ProxyGenerator.saveGeneratedFiles", "true");
    // 2、获取动态代理类
    Class proxyClazz = Proxy.getProxyClass(IHello.class.getClassLoader(),IHello.class);
    // 3、获得代理类的构造函数，并传入参数类型InvocationHandler.class
    Constructor constructor = proxyClazz.getConstructor(InvocationHandler.class);
    // 4、通过构造函数来创建动态代理对象，将自定义的InvocationHandler实例传入
    IHello iHello1 = (IHello) constructor.newInstance(new MyInvocationHandler(new HelloImpl()));
    // 5、通过代理对象调用目标方法
    iHello1.sayHello();
// ==========================第二种=============================
/**
 * Proxy类中还有个将2~4步骤封装好的简便方法来创建动态代理对象，
 *其方法签名为：newProxyInstance(ClassLoader loader,Class<?>[] instance, InvocationHandler h)
 */
IHello  iHello2 = (IHello) Proxy.newProxyInstance(IHello.class.getClassLoader(), // 加载接口的类加载器
        new Class[]{IHello.class}, // 一组接口
        new MyInvocationHandler(new HelloImpl())); // 自定义的InvocationHandler
iHello2.sayHello();
}
}
```
# 三 深入源码分析

以Proxy.newProxyInstance()方法为切入点来剖析代理类的生成及代理方法的调用。

```java
    
@CallerSensitive
    public static Object newProxyInstance(ClassLoader loader,
                                          Class<?>[] interfaces,
                                          InvocationHandler h)
        throws IllegalArgumentException
    {
	// 如果h为空直接抛出空指针异常，之后所有的单纯的判断null并抛异常，都是此方法
        Objects.requireNonNull(h);
	// 拷贝类实现的所有接口
        final Class<?>[] intfs = interfaces.clone();
	// 获取当前系统安全接口
        final SecurityManager sm = System.getSecurityManager();
        if (sm != null) {
	    // Reflection.getCallerClass返回调用该方法的方法的调用类;loader：接口的类加载器
	    // 进行包访问权限、类加载器权限等检查
            checkProxyAccess(Reflection.getCallerClass(), loader, intfs);
        }
/*
     * Look up or generate the designated proxy class.
 * 译: 查找或生成指定的代理类
     */
    Class<?> cl = getProxyClass0(loader, intfs);
 
    /*
     * Invoke its constructor with the designated invocation handler.
 * 译: 用指定的调用处理程序调用它的构造函数。
     */
    try {
        if (sm != null) {
            checkNewProxyPermission(Reflection.getCallerClass(), cl);
        }
   /*
    * 获取代理类的构造函数对象。
    * constructorParams是类常量，作为代理类构造函数的参数类型，常量定义如下:
    * private static final Class<?>[] constructorParams = { InvocationHandler.class };
    */
        final Constructor<?> cons = cl.getConstructor(constructorParams);
        final InvocationHandler ih = h;
        if (!Modifier.isPublic(cl.getModifiers())) {
            AccessController.doPrivileged(new PrivilegedAction<Void>() {
                public Void run() {
                    cons.setAccessible(true);
                    return null;
                }
            });
        }
    // 根据代理类的构造函数对象来创建需要返回的代理类对象
        return cons.newInstance(new Object[]{h});
    } catch (IllegalAccessException|InstantiationException e) {
        throw new InternalError(e.toString(), e);
    } catch (InvocationTargetException e) {
        Throwable t = e.getCause();
        if (t instanceof RuntimeException) {
            throw (RuntimeException) t;
        } else {
            throw new InternalError(t.toString(), t);
        }
    } catch (NoSuchMethodException e) {
        throw new InternalError(e.toString(), e);
    }
}
```

newProxyInstance()方法帮我们执行了生成代理类----获取构造器----生成代理对象这三步；

生成代理类: Class<?> cl = getProxyClass0(loader, intfs);

获取构造器: final Constructor<?> cons = cl.getConstructor(constructorParams);

生成代理对象: cons.newInstance(new Object[]{h});

Proxy.getProxyClass0()如何生成代理类？



```java
    private static Class<?> getProxyClass0(ClassLoader loader,
                                           Class<?>... interfaces) {
	// 接口数不得超过65535个，这么大，足够使用的了
        if (interfaces.length > 65535) {
            throw new IllegalArgumentException("interface limit exceeded");
        }
    // If the proxy class defined by the given loader implementing
    // the given interfaces exists, this will simply return the cached copy;
    // otherwise, it will create the proxy class via the ProxyClassFactory
// 译: 如果缓存中有代理类了直接返回，否则将由代理类工厂ProxyClassFactory创建代理类
    return proxyClassCache.get(loader, interfaces);
}
```
如果缓存中没有代理类，Proxy中的ProxyClassFactory如何创建代理类？从get()方法追踪进去看看。



```java
public V get(K key, P parameter) {// key：类加载器；parameter：接口数组
        // 检查指定类型的对象引用不为空null。当参数为null时，抛出空指针异常。
        Objects.requireNonNull(parameter);
		// 清除已经被GC回收的弱引用
        expungeStaleEntries();
		// 将ClassLoader包装成CacheKey, 作为一级缓存的key
        Object cacheKey = CacheKey.valueOf(key, refQueue);    
// lazily install the 2nd level valuesMap for the particular cacheKey
	// 获取得到二级缓存
    ConcurrentMap<Object, Supplier<V>> valuesMap = map.get(cacheKey);
	// 没有获取到对应的值
    if (valuesMap == null) {
        ConcurrentMap<Object, Supplier<V>> oldValuesMap
            = map.putIfAbsent(cacheKey,
                              valuesMap = new ConcurrentHashMap<>());
        if (oldValuesMap != null) {
            valuesMap = oldValuesMap;
        }
    }
 
    // create subKey and retrieve the possible Supplier<V> stored by that
    // subKey from valuesMap
	// 根据代理类实现的接口数组来生成二级缓存key
    Object subKey = Objects.requireNonNull(subKeyFactory.apply(key, parameter));
	// 通过subKey获取二级缓存值
    Supplier<V> supplier = valuesMap.get(subKey);
    Factory factory = null;
	// 这个循环提供了轮询机制, 如果条件为假就继续重试直到条件为真为止
    while (true) {
        if (supplier != null) {
            // supplier might be a Factory or a CacheValue<V> instance
			// 在这里supplier可能是一个Factory也可能会是一个CacheValue
			// 在这里不作判断, 而是在Supplier实现类的get方法里面进行验证
            V value = supplier.get();
            if (value != null) {
                return value;
            }
        }
        // else no supplier in cache
        // or a supplier that returned null (could be a cleared CacheValue
        // or a Factory that wasn't successful in installing the CacheValue)
 
        // lazily construct a Factory
        if (factory == null) {
		    // 新建一个Factory实例作为subKey对应的值
            factory = new Factory(key, parameter, subKey, valuesMap);
        }
 
        if (supplier == null) {
		    // 到这里表明subKey没有对应的值, 就将factory作为subKey的值放入
            supplier = valuesMap.putIfAbsent(subKey, factory);
            if (supplier == null) {
                // successfully installed Factory
				// 到这里表明成功将factory放入缓存
                supplier = factory;
            }
			// 否则, 可能期间有其他线程修改了值, 那么就不再继续给subKey赋值, 而是取出来直接用
            // else retry with winning supplier
        } else {
		    // 期间可能其他线程修改了值, 那么就将原先的值替换
            if (valuesMap.replace(subKey, supplier, factory)) {
                // successfully replaced
                // cleared CacheEntry / unsuccessful Factory
                // with our Factory
				// 成功将factory替换成新的值
                supplier = factory;
            } else {
                // retry with current supplier
				// 替换失败, 继续使用原先的值
                supplier = valuesMap.get(subKey);
            }
        }
    }
}
```
get方法中Object subKey = Objects.requireNonNull(subKeyFactory.apply(key, parameter));

subKeyFactory调用apply，具体实现在ProxyClassFactory中完成。

ProxyClassFactory.apply()实现代理类创建。



```java
private static final class ProxyClassFactory
        implements BiFunction<ClassLoader, Class<?>[], Class<?>>
    {
        // prefix for all proxy class names
	// 统一代理类的前缀名都以$Proxy
        private static final String proxyClassNamePrefix = "$Proxy";    
// next number to use for generation of unique proxy class names
// 使用唯一的编号给作为代理类名的一部分，如$Proxy0,$Proxy1等
    private static final AtomicLong nextUniqueNumber = new AtomicLong();
 
    @Override
    public Class<?> apply(ClassLoader loader, Class<?>[] interfaces) {
 
        Map<Class<?>, Boolean> interfaceSet = new IdentityHashMap<>(interfaces.length);
        for (Class<?> intf : interfaces) {
            /*
             * Verify that the class loader resolves the name of this
             * interface to the same Class object.
	 * 验证指定的类加载器(loader)加载接口所得到的Class对象(interfaceClass)是否与intf对象相同
             */
            Class<?> interfaceClass = null;
            try {
                interfaceClass = Class.forName(intf.getName(), false, loader);
            } catch (ClassNotFoundException e) {
            }
            if (interfaceClass != intf) {
                throw new IllegalArgumentException(
                    intf + " is not visible from class loader");
            }
            /*
             * Verify that the Class object actually represents an
             * interface.
	 * 验证该Class对象是不是接口
             */
            if (!interfaceClass.isInterface()) {
                throw new IllegalArgumentException(
                    interfaceClass.getName() + " is not an interface");
            }
            /*
             * Verify that this interface is not a duplicate.
	 * 验证该接口是否重复
             */
            if (interfaceSet.put(interfaceClass, Boolean.TRUE) != null) {
                throw new IllegalArgumentException(
                    "repeated interface: " + interfaceClass.getName());
            }
        }
    // 声明代理类所在包
        String proxyPkg = null;     // package to define proxy class in
        int accessFlags = Modifier.PUBLIC | Modifier.FINAL;
 
        /*
         * Record the package of a non-public proxy interface so that the
         * proxy class will be defined in the same package.  Verify that
         * all non-public proxy interfaces are in the same package.
     * 验证所有非公共的接口在同一个包内；公共的就无需处理
         */
        for (Class<?> intf : interfaces) {
            int flags = intf.getModifiers();
            if (!Modifier.isPublic(flags)) {
                accessFlags = Modifier.FINAL;
                String name = intf.getName();
                int n = name.lastIndexOf('.');
				// 截取完整包名
                String pkg = ((n == -1) ? "" : name.substring(0, n + 1));
                if (proxyPkg == null) {
                    proxyPkg = pkg;
                } else if (!pkg.equals(proxyPkg)) {
                    throw new IllegalArgumentException(
                        "non-public interfaces from different packages");
                }
            }
        }
 
        if (proxyPkg == null) {
            // if no non-public proxy interfaces, use com.sun.proxy package
	/*如果都是public接口，那么生成的代理类就在com.sun.proxy包下如果报java.io.FileNotFoundException: com\sun\proxy\$Proxy0.class 
	(系统找不到指定的路径。)的错误，就先在你项目中创建com.sun.proxy路径*/
            proxyPkg = ReflectUtil.PROXY_PACKAGE + ".";
        }
 
        /*
         * Choose a name for the proxy class to generate.
     * nextUniqueNumber 是一个原子类，确保多线程安全，防止类名重复，类似于：$Proxy0，$Proxy1......
         */
        long num = nextUniqueNumber.getAndIncrement();
    // 代理类的完全限定名，如com.sun.proxy.$Proxy0.calss
        String proxyName = proxyPkg + proxyClassNamePrefix + num;
 
        /*
         * Generate the specified proxy class.
     * 生成类字节码的方法（重点）
         */
        byte[] proxyClassFile = ProxyGenerator.generateProxyClass(
            proxyName, interfaces, accessFlags);
        try {
            return defineClass0(loader, proxyName,
                                proxyClassFile, 0, proxyClassFile.length);
        } catch (ClassFormatError e) {
            /*
             * A ClassFormatError here means that (barring bugs in the
             * proxy class generation code) there was some other
             * invalid aspect of the arguments supplied to the proxy
             * class creation (such as virtual machine limitations
             * exceeded).
             */
            throw new IllegalArgumentException(e.toString());
        }
    }
}
```
代理类创建真正在ProxyGenerator.generateProxyClass（）方法中，方法签名如下:

```java
byte[] proxyClassFile = ProxyGenerator.generateProxyClass(proxyName, interfaces, accessFlags);

public static byte[] generateProxyClass(final String name, Class<?>[] interfaces, int accessFlags) {
        ProxyGenerator gen = new ProxyGenerator(name, interfaces, accessFlags);
        // 真正生成字节码的方法
        final byte[] classFile = gen.generateClassFile();
        // 如果saveGeneratedFiles为true 则生成字节码文件，所以在开始我们要设置这个参数
        // 当然，也可以通过返回的bytes自己输出
        if (saveGeneratedFiles) {
            java.security.AccessController.doPrivileged( new java.security.PrivilegedAction<Void>() {
                        public Void run() {
                            try {
                                int i = name.lastIndexOf('.');
                                Path path;
                                if (i > 0) {
                                    Path dir = Paths.get(name.substring(0, i).replace('.', File.separatorChar));
                                    Files.createDirectories(dir);
                                    path = dir.resolve(name.substring(i+1, name.length()) + ".class");
                                } else {
                                    path = Paths.get(name + ".class");
                                }
                                Files.write(path, classFile);
                                return null;
                            } catch (IOException e) {
                                throw new InternalError( "I/O exception saving generated file: " + e);
                            }
                        }
                    });
        }
        return classFile;
    }
```

代理类生成的最终方法是ProxyGenerator.generateClassFile()

```java
    private byte[] generateClassFile() {
        /* ============================================================
         * Step 1: Assemble ProxyMethod objects for all methods to generate proxy dispatching code for.
         * 步骤1：为所有方法生成代理调度代码，将代理方法对象集合起来。
         */
        //增加 hashcode、equals、toString方法
        addProxyMethod(hashCodeMethod, Object.class);
        addProxyMethod(equalsMethod, Object.class);
        addProxyMethod(toStringMethod, Object.class);
        // 获得所有接口中的所有方法，并将方法添加到代理方法中
        for (Class<?> intf : interfaces) {
            for (Method m : intf.getMethods()) {
                addProxyMethod(m, intf);
            }
        }
/*
     * 验证方法签名相同的一组方法，返回值类型是否相同；意思就是重写方法要方法签名和返回值一样
     */
    for (List<ProxyMethod> sigmethods : proxyMethods.values()) {
        checkReturnTypes(sigmethods);
    }
 
    /* ============================================================
     * Step 2: Assemble FieldInfo and MethodInfo structs for all of fields and methods in the class we are generating.
     * 为类中的方法生成字段信息和方法信息
     */
    try {
        // 生成代理类的构造函数
        methods.add(generateConstructor());
        for (List<ProxyMethod> sigmethods : proxyMethods.values()) {
            for (ProxyMethod pm : sigmethods) {
                // add static field for method's Method object
                fields.add(new FieldInfo(pm.methodFieldName,
                        "Ljava/lang/reflect/Method;",
                        ACC_PRIVATE | ACC_STATIC));
                // generate code for proxy method and add it
				// 生成代理类的代理方法
                methods.add(pm.generateMethod());
            }
        }
        // 为代理类生成静态代码块，对一些字段进行初始化
        methods.add(generateStaticInitializer());
    } catch (IOException e) {
        throw new InternalError("unexpected I/O Exception", e);
    }
 
    if (methods.size() > 65535) {
        throw new IllegalArgumentException("method limit exceeded");
    }
    if (fields.size() > 65535) {
        throw new IllegalArgumentException("field limit exceeded");
    }
 
    /* ============================================================
     * Step 3: Write the final class file.
     * 步骤3：编写最终类文件
     */
    /*
     * Make sure that constant pool indexes are reserved for the following items before starting to write the final class file.
     * 在开始编写最终类文件之前，确保为下面的项目保留常量池索引。
     */
    cp.getClass(dotToSlash(className));
    cp.getClass(superclassName);
    for (Class<?> intf: interfaces) {
        cp.getClass(dotToSlash(intf.getName()));
    }
 
    /*
     * Disallow new constant pool additions beyond this point, since we are about to write the final constant pool table.
     * 设置只读，在这之前不允许在常量池中增加信息，因为要写常量池表
     */
    cp.setReadOnly();
 
    ByteArrayOutputStream bout = new ByteArrayOutputStream();
    DataOutputStream dout = new DataOutputStream(bout);
 
    try {
        // u4 magic;
        dout.writeInt(0xCAFEBABE);
        // u2 次要版本;
        dout.writeShort(CLASSFILE_MINOR_VERSION);
        // u2 主版本
        dout.writeShort(CLASSFILE_MAJOR_VERSION);
 
        cp.write(dout);             // (write constant pool)
 
        // u2 访问标识;
        dout.writeShort(accessFlags);
        // u2 本类名;
        dout.writeShort(cp.getClass(dotToSlash(className)));
        // u2 父类名;
        dout.writeShort(cp.getClass(superclassName));
        // u2 接口;
        dout.writeShort(interfaces.length);
        // u2 interfaces[interfaces_count];
        for (Class<?> intf : interfaces) {
            dout.writeShort(cp.getClass(
                    dotToSlash(intf.getName())));
        }
        // u2 字段;
        dout.writeShort(fields.size());
        // field_info fields[fields_count];
        for (FieldInfo f : fields) {
            f.write(dout);
        }
        // u2 方法;
        dout.writeShort(methods.size());
        // method_info methods[methods_count];
        for (MethodInfo m : methods) {
            m.write(dout);
        }
        // u2 类文件属性：对于代理类来说没有类文件属性;
        dout.writeShort(0); // (no ClassFile attributes for proxy classes)
 
    } catch (IOException e) {
        throw new InternalError("unexpected I/O Exception", e);
    }
 
    return bout.toByteArray();
}
```
通过addProxyMethod（）添加hashcode、equals、toString方法。

```java
private void addProxyMethod(Method var1, Class var2) {
        String var3 = var1.getName();  //方法名
        Class[] var4 = var1.getParameterTypes();   //方法参数类型数组
        Class var5 = var1.getReturnType();    //返回值类型
        Class[] var6 = var1.getExceptionTypes();   //异常类型
        String var7 = var3 + getParameterDescriptors(var4);   //方法签名
        Object var8 = (List)this.proxyMethods.get(var7);   //根据方法签名却获得proxyMethods的Value
        if(var8 != null) {    //处理多个代理接口中重复的方法的情况
            Iterator var9 = ((List)var8).iterator();
            while(var9.hasNext()) {
                ProxyGenerator.ProxyMethod var10 = (ProxyGenerator.ProxyMethod)var9.next();
                if(var5 == var10.returnType) {
                    /*归约异常类型以至于让重写的方法抛出合适的异常类型，我认为这里可能是多个接口中有相同的方法，而这些相同的方法抛出的异常类                      型又不同，所以对这些相同方法抛出的异常进行了归约*/
                    ArrayList var11 = new ArrayList();
                    collectCompatibleTypes(var6, var10.exceptionTypes, var11);
                    collectCompatibleTypes(var10.exceptionTypes, var6, var11);
                    var10.exceptionTypes = new Class[var11.size()];
                    //将ArrayList转换为Class对象数组
                    var10.exceptionTypes = (Class[])var11.toArray(var10.exceptionTypes);
                    return;
                }
            }
        } else {
            var8 = new ArrayList(3);
            this.proxyMethods.put(var7, var8);
        }    
        ((List)var8).add(new ProxyGenerator.ProxyMethod(var3, var4, var5, var6, var2, null));
       /*如果var8为空，就创建一个数组，并以方法签名为key,proxymethod对象数组为value添加到proxyMethods*/
    }
```


生成的代理对象$Proxy0.class字节码反编译：

```java
package com.sun.proxy;

import com.jpeony.spring.proxy.jdk.IHello;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.lang.reflect.UndeclaredThrowableException;

public final class $Proxy0 extends Proxy
  implements IHello // 继承了Proxy类和实现IHello接口
{
  // 变量，都是private static Method  XXX
  private static Method m1;
  private static Method m3;
  private static Method m2;
  private static Method m0;

  // 代理类的构造函数，其参数正是是InvocationHandler实例，Proxy.newInstance方法就是通过通过这个构造函数来创建代理实例的
  public $Proxy0(InvocationHandler paramInvocationHandler)
    throws 
  {
    super(paramInvocationHandler);
  }

  // 以下Object中的三个方法
  public final boolean equals(Object paramObject)
    throws 
  {
    try
    {
      return ((Boolean)this.h.invoke(this, m1, new Object[] { paramObject })).booleanValue();
    }
    catch (RuntimeException localRuntimeException)
    {
      throw localRuntimeException;
    }
    catch (Throwable localThrowable)
    {
      throw new UndeclaredThrowableException(localThrowable);
    }
  }

  // 接口代理方法
  public final void sayHello()
    throws 
  {
    try
    {
      this.h.invoke(this, m3, null);
      return;
    }
    catch (RuntimeException localRuntimeException)
    {
      throw localRuntimeException;
    }
    catch (Throwable localThrowable)
    {
      throw new UndeclaredThrowableException(localThrowable);
    }
  }

  public final String toString()
    throws 
  {
    try
    {
      return ((String)this.h.invoke(this, m2, null));
    }
    catch (RuntimeException localRuntimeException)
    {
      throw localRuntimeException;
    }
    catch (Throwable localThrowable)
    {
      throw new UndeclaredThrowableException(localThrowable);
    }
  }

  public final int hashCode()
    throws 
  {
    try
    {
      return ((Integer)this.h.invoke(this, m0, null)).intValue();
    }
    catch (RuntimeException localRuntimeException)
    {
      throw localRuntimeException;
    }
    catch (Throwable localThrowable)
    {
      throw new UndeclaredThrowableException(localThrowable);
    }
  }

  // 静态代码块对变量进行一些初始化工作
  static
  {
    try
    {
	  // 这里每个方法对象 和类的实际方法绑定
      m1 = Class.forName("java.lang.Object").getMethod("equals", new Class[] { Class.forName("java.lang.Object") });
      m3 = Class.forName("com.jpeony.spring.proxy.jdk.IHello").getMethod("sayHello", new Class[0]);
      m2 = Class.forName("java.lang.Object").getMethod("toString", new Class[0]);
      m0 = Class.forName("java.lang.Object").getMethod("hashCode", new Class[0]);
      return;
    }
    catch (NoSuchMethodException localNoSuchMethodException)
    {
      throw new NoSuchMethodError(localNoSuchMethodException.getMessage());
    }
    catch (ClassNotFoundException localClassNotFoundException)
    {
      throw new NoClassDefFoundError(localClassNotFoundException.getMessage());
    }
  }
}
```

当代理对象生成后，最后由InvocationHandler的invoke()方法调用目标方法:

在动态代理中InvocationHandler是核心，每个代理实例都具有一个关联的调用处理程序(InvocationHandler)。

对代理实例调用方法时，将对方法调用进行编码并将其指派到它的调用处理程序(InvocationHandler)的invoke()方法。

所以对代理方法的调用都是通InvocationHadler的invoke来实现中，而invoke方法根据传入的代理对象，

方法和参数来决定调用代理的哪个方法。

方法签名如下:

invoke(Object Proxy，Method method，Object[] args)

从反编译源码分析调用invoke()过程:

从反编译后的源码看$Proxy0类继承了Proxy类，同时实现了IHello接口，即代理类接口，

所以才能强制将代理对象转换为IHello接口，然后调用$Proxy0中的sayHello()方法。

$Proxy0中sayHello()源码:

```java
public final void sayHello()
    throws 
  {
    try
    {
      this.h.invoke(this, m3, null);
      return;
    }
    catch (RuntimeException localRuntimeException)
    {
      throw localRuntimeException;
    }
    catch (Throwable localThrowable)
    {
      throw new UndeclaredThrowableException(localThrowable);
    }
  }

this.h.invoke(this, m3, null); 
```

this就是$Proxy0对象；

m3就是m3 = Class.forName("com.jpeony.spring.proxy.jdk.IHello").getMethod("sayHello", new Class[0]);

即是通过全路径名，反射获取的目标对象中的真实方法加参数。

h就是Proxy类中的变量protected InvocationHandler h;

所以成功的调到了InvocationHandler中的invoke()方法，但是invoke()方法在我们自定义的MyInvocationHandler

中实现，MyInvocationHandler中的invoke()方法:

```java
@Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("------插入前置通知代码-------------");
        // 执行相应的目标方法
        Object rs = method.invoke(target,args);
        System.out.println("------插入后置处理代码-------------");
        return rs;
    }
```


所以，绕了半天，终于调用到了MyInvocationHandler中的invoke()方法，从上面的this.h.invoke(this, m3, null); 

可以看出，MyInvocationHandler中invoke第一个参数为$Proxy0（代理对象），第二个参数为目标类的真实方法，

第三个参数为目标方法参数，因为sayHello()没有参数，所以是null。

到这里，我们真正的实现了通过代理调用目标对象的完全分析，至于InvocationHandler中的invoke()方法就是

最后执行了目标方法。到此完成了代理对象生成，目标方法调用。

所以，我们可以看到在打印目标方法调用输出结果前后所插入的前置和后置代码处理。
————————————————
版权声明：本文为CSDN博主「街灯下的小草」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/yhl_jxy/article/details/80586785