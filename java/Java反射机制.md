# Java反射机制

## 一、Java反射机制概述

**Java放射机制**是指在==运行状态==中，对于任意一个类，都能知道这个类的所有属性和方法；对于任意一个对象，都能调用它的任意一个方法和属性；这种动态获取信息及动态调用方法的功能成为**Java的反射机制**。

## 二、反射的作用

1. 利用Java机制，在**Java程序**中可以动态的去调用一些protected甚至是private的方法或类，这样就可以在很大程度上满足一些特殊需求。
2. 在**Android SDK**的源码中，很多类或方法中经常加上了“@hide”注释标记，它的作用是使这个方法或类在生成SDK时不可见，所以程序可能无法编译通过，而且在最终发布的时候，就可能存在一些问题。对于这种问题，第一种方法就是自己去掉源码中的“@hide”标记，然后在重新编译生成一个SDK。另一种方法就是使用Java反射机制，利用反射机制可以访问存在访问权限的方法或修改其访问域。
3. 可能有人会有疑问，明明直接new对象就好了，为什么非要用反射呢？代码量不是反而增加了？其实反射的初衷不是方便你去创建一个对象，而是让你在写代码的时候可以更加灵活，降低耦合，提高代码的自适应能力。

**怎么样降低耦合度，提高代码的自适应能力？**

通过接口实现,**但是接口如果需要用到new关键字,这时候耦合问题又会出现**

举个例子:

```
/**
/**
 * @ClassName: TestReflection 
 * @Model : (所属模块名称)
 * @Description: 使用反射降低耦合度（通过接口来实现）
 * @author Administrator 
 * @date 2017年6月2日 下午5:09:38 
 */
public class TestReflection {

    /**
    /**
     * main (这里用一句话描述这个方法的作用) 
     * @param args
     * void 
     * @ModifiedPerson Administrator
     * @date 2017年6月2日 下午5:09:38 
     */
    public static void main(String[] args) {

        //普通写法，使用New 关键字
        ITest iTest = createITest("ITestImpl1");
        iTest.testReflect();
        ITest iTest2 = createITest("ITestImpl2");
        iTest2.testReflect();
        
    }

    /**
    * createITest 普通写法，使用New关键字，但是假设有1000个不同ITest需要创建,那你打算写1000个 if语句来返回不同的ITest对象?
    * @param name
    * @return
    * ITest 
    * @ModifiedPerson Administrator
    * @date 2017年6月2日 下午5:32:21
     */
    public static ITest createITest(String name){
        
        if (name.equals("ITestImpl1")) {
            return new ITestImpl1();
        } else if(name.equals("ITestImpl2")){
            return new ITestImpl2();
        }
    
        return null;
    }

}


interface ITest{
    public void testReflect();
}

class ITestImpl1 implements ITest{

    /* (non-Javadoc)
    * <p>Title: test</p> 
    * <p>Description: </p>  
    * @see ITest#test()
    */
    @Override
    public void testReflect() {

         System.out.println("I am ITestImpl1 !");
    }
}

class ITestImpl2 implements ITest{

    /* (non-Javadoc)
    * <p>Title: testReflect</p> 
    * <p>Description: </p>  
    * @see ITest#testReflect()
    */
    @Override
    public void testReflect() {

        System.out.println("I am ITestImpl2 !");
    }
    
}


复制代码
```

假设有1000个不同ITest需要创建,那你打算写1000个 if语句来返回不同的ITest对象?

如果使用反射机制呢?

```
/**
/**
 * @ClassName: TestReflection 
 * @Model : (所属模块名称)
 * @Description: 使用反射降低耦合度（通过接口来实现）
 * @author Administrator 
 * @date 2017年6月2日 下午5:09:38 
 */
public class TestReflection {

    /**
    /**
     * main (这里用一句话描述这个方法的作用) 
     * @param args
     * void 
     * @ModifiedPerson Administrator
     * @date 2017年6月2日 下午5:09:38 
     */
    public static void main(String[] args) {
        //普通写法，使用New 关键字
        ITest iTest = createITest("ITestImpl1");
        iTest.testReflect();
        ITest iTest2 = createITest("ITestImpl2");
        iTest2.testReflect();
        
        //使用反射机制
        ITest iTest3 = createITest2("ITestImpl1");
        iTest3.testReflect();
        ITest iTest4 = createITest2("ITestImpl2");
        iTest4.testReflect();
        
    }

    /**
    * createITest 普通写法，使用New关键字，但是假设有1000个不同ITest需要创建,那你打算写1000个 if语句来返回不同的ITest对象?
    * @param name
    * @return
    * ITest 
    * @ModifiedPerson Administrator
    * @date 2017年6月2日 下午5:32:21
     */
    public static ITest createITest(String name){
        
        if (name.equals("ITestImpl1")) {
            return new ITestImpl1();
        } else if(name.equals("ITestImpl2")){
            return new ITestImpl2();
        }
    
        return null;
    }
    
    /**
    * createITest2 使用反射机制:当有1000个不同ITest需要创建时，不用针对每个创建ITest对象
    * @param name
    * @return
    * ITest 
    * @ModifiedPerson Administrator
    * @date 2017年6月2日 下午5:34:55
     */
    public static ITest createITest2(String name){
        try {
            Class<?> class1 = Class.forName(name);
            ITest iTest = (ITest) class1.newInstance();
            return iTest;
        } catch (ClassNotFoundException e) {

            e.printStackTrace();
        } catch (InstantiationException e) {

            e.printStackTrace();
        } catch (IllegalAccessException e) {

            e.printStackTrace();
        }
        
        return null;
        
    }

}


interface ITest{
    public void testReflect();
}

class ITestImpl1 implements ITest{

    /* (non-Javadoc)
    * <p>Title: test</p> 
    * <p>Description: </p>  
    * @see ITest#test()
    */
    @Override
    public void testReflect() {

         System.out.println("I am ITestImpl1 !");
    }
}

class ITestImpl2 implements ITest{

    /* (non-Javadoc)
    * <p>Title: testReflect</p> 
    * <p>Description: </p>  
    * @see ITest#testReflect()
    */
    @Override
    public void testReflect() {

        System.out.println("I am ITestImpl2 !");
    }
    
}


复制代码
```

利用反射机制进行解耦的原理：就是利用反射机制"动态"的创建对象:向createITest()方法传入Hero类的包名.类名 通过加载指定的类,然后再实例化对象.

## 三、理解Class类和类类型

要想理解反射，首先要理解Class类，因为Class类是反射实现的基础。

### 3.1 类是对象吗

在面向对象的世界里，万物皆对象，对象是一个类的实例，所以**类是java.lang.Class类的实例对象**，而Class是所有类的类（This is a class named Class）,Class类是类类型，即类的类型。

### 3.2 Class的对象的获取

对于普通的对象，我们都是直接通过new来创建一个对象。

Student sdutent = new Student（）；

但是Class的对象是类，不能通过new来创建。Class的源码描述如下：

```
/*
     * Private constructor. Only the Java Virtual Machine creates Class objects.
     * This constructor is not used and prevents the default constructor being
     * generated.
     */
    private Class(ClassLoader loader) {
        // Initialize final field for classLoader.  The initialization value of non-null
        // prevents future JIT optimizations from assuming this final field is null.
        classLoader = loader;
    }
复制代码
```

构造器是私有的，只有Java虚拟机（JVM）可以创建Class的对象，不能像普通类一样new一个Class对象。只能是通过已有的类来得到一个Class对象（三种方法）：

###### 第一种方法

```
Class<?> cls = Class.forName("com.example.student");//forName（包名.类名）
Student s1 = (Student)cls.newInstance();
复制代码
```

1. 通过JVM查找并加载指定的类
2. 调用newInstance()方法让加载完的类在内存中创建对应的实例，并把实例赋值给s1

###### 第二种方法

```
Student s = new Student;    
Class<?> cls = s.getClass;
Student s2 = (Student)cls.newInstance();
复制代码
```

1. 在内存中新建一个Student的实例，对象s对这个内存地址进行引用
2. 对象s调用getClass()方法返回对象s所对应的Class对象
3. 调用newInstance()方法让Class对象在内存中创建对象的实例，并让s2引用实例的内存地址

###### 第三种方法

```
Class<?> cls = Student.Class();
Student s3 = (Student)cls.newInstance();
复制代码
```

1. 获取指定类型的Class对象，这里是Student
2. 调用newInstance()方法让Class对象在内存中创建对应实例，并让s3引用实例的内存地址

###### 注意:

- cls.newInstance()方法返回的是一个泛型T,我们要强转成Student类
- cls.newInstance()默认返回的是Student类的无参数构造对象
- 被反射机制加载的类必须有无参数构造方法,否者运行会抛出异常

通过类类型创建类和通过new创建类的不同之处是：类类型创建的是动态加载类。

## 四、动态加载类

程序执行分为编译器和运行期，编译时刻加载一个类就称为静态加载类，运行时刻加载类称为动态加载类，下面通过一个实例来讲解：

现在抛开IDE工具，用记事本手写类，这是为了方便我们利用cmd命令行手动编译和运行一个类，从而更好理解动态加载类和静态加载类的区别。

首先写First.java

```
class First
{
    public static void main(String[] args)
    {
        if ("Word".equals(args[0]))
        {   
            // 静态加载类，在编译时加载
            Word w = new Word();
            w.start();
        }
        if ("Excel".equals(args[0]))
        {
            Excel e = new Excel();
            e.start();
        }
    }
}
复制代码
```

然后进入cmd编译First.java

由于我们new的两个类Word和Excel没有编译，所以报错了，这就是静态加载类的缺点，即必须在编译时期就加载所有可能用到的类，而我们希望实现的是运行时用到哪个类就加载哪个类，下面通过动态加载类来加以改进。

改进以后的类：FirstBetter.java

```
class FirstBetter
{   
    public static void main(String[] args)
    {
            try
        {
            // 动态加载类，在运行时加载
            Class c = Class.forName(args[0]);
            // 通过类类型，创建该类对象
            FirstAble oa = (FirstAble)c.newInstance();
            oa.start();
        }
        catch (Exception e)
        {
            e.printStackTrace();
        }
    }   
}
复制代码
```

这里动态加载了名为args[0]的类，而args[0]是在运行期输入给main方法的第一个参数，如果你输入Word那么就会加载Word.java，这时候就需要在与FirstBetter.java相同路径下面创建Word.java；同理，如果你输入Excel就需要加载Excel.java了。 其中FirstAble是一个接口，上面动态加载的类如Word、Excel就是实现了FirstAble，体现了多态的思想，这种动态加载和多态的思想可以使具体功能和代码解耦，也就是随时想添加某个功能（如Word和Excel都不要了，我要PPT）都能动态添加，而不改动原来的代码。

其中FirstAble接口如下：

```
interface FirstAble
{
    public void start();
}
复制代码
```

Word类：

```
class Word implements FirstAble
{
    public void start()
    {
        System.out.println("word...starts...");
    }
}
复制代码
```

按顺序编译、运行上面的类。

## 五、获取类的信息

一个类中通常包含属性和方法，通过反射获取类的构造方法、成员方法、成员变量、修饰（方法和变量的）等。

### 5.1 获取类的构造函数

构造函数中都包括什么：构造函数参数

类的成构造函数是一个对象，它是java.lang.reflect.Constructor的一个对象，所以我们通过java.lang.reflect.Constructor里面封装的方法来获取这些信息。

###### 1、单独获取某个构造函数

通过Class类的以下方法实现：

- public Constructor getDeclaredConstructor(Class<?>... parameterTypes) // 根据构造函数的参数，返回一个具体的构造函数（不分public和非public属性）
- public Constructor getConstructor(Class<?>... parameterTypes) // 根据构造函数的参数，返回一个具体的具有public属性的构造函数
- 参数parameterTypes为构造函数参数类的类类型列表。

例如类A有如下一个构造函数：

```
public A(String a, int b) {
    // code body
}
复制代码
```

那么就可以通过：

```
Constructor constructor = a.getDeclaredConstructor(String.class, int.class);
复制代码
```

来获取这个构造函数。

###### 2、获取所有的构造函数

通过Class类的以下方法实现：

- Constructor getDeclaredConstructors()    返回该类中所有的构造函数数组（不分public和非public属性）
- Constructor getConstructors()     返回所有具有public属性的构造函数数组

可以通过以下步骤实现：

1、已知一个对象，获取其类的类类型

```
Class c = obj.getClass();
复制代码
```

2、获取该类的所有构造函数，放在一个数组中

```
Constructor[] constructors = c.getDeclaredConstructors();
复制代码
```

3、遍历构造函数数组，获得某个构造函数constructor

```
for (Constructor constructor : constructors)
复制代码
```

4、得到构造函数参数类型的类类型数组

```
Class[] paramTypes = constructor.getParameterTypes();
复制代码
```

5、遍历参数类的类类型数组，得到某个参数的类类型class1

```
for (Class class1 : paramTypes)
复制代码
```

6、得到该参数的类型名

```
String paramName = class1.getName();
复制代码
```

例子：

```
private static void forName2GetConstructor() {
        try {
            //第一种方法：forName()
            Class<?> class1 = Class.forName("com.zyt.reflect.StudentInfo");
            
            Constructor<?>[] constructors = class1.getDeclaredConstructors();
            for (Constructor<?> constructor : constructors) {
                System.out.print("构造方法："+constructor.getName()+" 参数类型：");
                Class<?>[] parameterTypes = constructor.getParameterTypes();
                for (Class<?> class2 : parameterTypes) {
                    System.out.print(class2.getName()+" , ");
                }
                System.out.print("\n");
            }
            
            //调用构造方法
            Constructor<?> constructor = class1.getDeclaredConstructor(String.class, String.class, int.class);
            StudentInfo instance = (StudentInfo) constructor.newInstance("张三", "男", 18);
            System.out.println("调用构造方法 == "+instance.getName()+"，"+instance.getScore());
            
            
        } catch (Exception e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
复制代码
```

### 5.2 获取类的成员方法

成员方法中都包括什么：返回值类型+方法名+参数类型

在Java中，类的成员方法也是一个对象，它是java.lang.reflect.Method的一个对象，所以我们通过java.lang.reflect.Method里面封装的方法来获取这些信息.

###### 1、单独获取某一个方法

- Method getMethod(String name, Class[] params)    根据方法名和参数，返回一个具体的具有public属性的方法
- Method getDeclaredMethod(String name, Class[] params)    根据方法名和参数，返回一个具体的方法（不分public和非public属性）
- 两个参数分别是方法名和方法参数类的类类型列表。

例如类A有如下一个方法：

```
public void print(String a, int b) {
    // code body
}
复制代码
```

现在知道A有一个对象a，那么就可以通过：

```
Class c = a.getClass();
Method method = c.getDeclaredMethod("print", String.class, int.class);
复制代码
```

来获取这个方法。

###### 如何调用获取到的方法

那得到方法以后如何调用这个方法呢，通过Method类的以下方法实现：

```
public Object invoke(Object obj, Object… args)
复制代码
```

两个参数分别是这个方法所属的对象和这个方法需要的参数，还是用上面的例子来说明，通过：

```
method.invoke(a, "hello", 10);
复制代码
```

和通过普通调用：

```
a.print("hello", 10);
复制代码
```

效果完全一样，这就是方法的反射，invoke()方法可以反过来将其对象作为参数来调用方法，完全跟正常情况反了过来。

###### 2、获取类中所有成员方法的信息

- Method[] getMethods()    返回所有具有public属性的方法数组
- Method[] getDeclaredMethods()    返回该类中的所有的方法数组（不分public和非public属性）

###### 注意：

getMethods()：用于获取类的所有的public修饰域的成员方法，包括从父类继承的public方法和实现接口的public方法；

getDeclaredMethods()：用于获取在当前类中定义的所有的成员方法和实现的接口方法，不包括从父类继承的方法。

大家可以查考一下开发文档的解释：

```
getMethods() - Returns an array containing Method objects for all public methods for the class C represented by this Class. 

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; Methods may be declared in C, the interfaces it implements or in the superclasses of C. 

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; The elements in the returned array are in no particular order. 

getDeclaredMethods() - Returns a Method object which represents the method matching the specified name and parameter types 

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;  that is declared by the class represented by this Class.
复制代码
```

因此在示例代码的方法get_Reflection_Method(...)中，ReflectionTest类继承了Object类，实现了actionPerformed方法，并定义如下成员方法： 

![image](https://user-gold-cdn.xitu.io/2018/8/23/16565e76f3d05bef?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

 通过这两个语句执行后的结果不同：



  a、Method[] methods = temp.getDeclaredMethods()执行后结果如下：

   

![image](https://user-gold-cdn.xitu.io/2018/8/23/16565e76f395fe00?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



  b、Method[] methods = temp.getMethods()执行后，结果如下：

  

![image](https://user-gold-cdn.xitu.io/2018/8/23/16565e76f3e9ef96?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



Method类：

```
public invoke( obj,                       ... args)
复制代码
```

对带有指定参数的指定对象调用由此 Method对象表示的底层方法。个别参数被自动解包，以便与基本形参相匹配，基本参数和引用参数都随需服从方法调用转换。

- 如果底层方法是静态的，那么可以忽略指定的 obj参数。该参数可以为 null。
- 如果底层方法所需的形参数为 0，则所提供的 args数组长度可以为 0 或 null。
- 如果底层方法是实例方法，则使用动态方法查找来调用它，这一点记录在 Java Language Specification, Second Edition 的第 15.12.4.4 节中；在发生基于目标对象的运行时类型的重写时更应该这样做。
- 如果底层方法是静态的，并且尚未初始化声明此方法的类，则会将其初始化。
- 如果方法正常完成，则将该方法返回的值返回给调用者；如果该值为基本类型，则首先适当地将其包装在对象中。但是，如果该值的类型为一组基本类型，则数组元素不被包装在对象中；换句话说，将返回基本类型的数组。如果底层方法返回类型为 void，则该调用返回 null。

参数：

obj- 从中调用底层方法的对象

args- 用于方法调用的参数

返回：

使用参数 args在 obj上指派该对象所表示方法的结果   如果想要获得类中所有而非单独某个成员方法的信息，可以通过以下几步来实现：

1、已知一个对象，获取其类的类类型

```
Class c = obj.getClass();
复制代码
```

2、获取该类的所有方法，放在一个数组中

```
Method[] methods = c.getDeclaredMethods();
复制代码
```

3、遍历方法数组，获得某个方法method

```
for (Method method : methods)
复制代码
```

4、得到方法返回值类型的类类型

```
Class returnType = method.getReturnType();
复制代码
```

5、得到方法返回值类型的名称

```
String returnTypeName = returnType.getName();
复制代码
```

6、得到方法的名称

```
String methodName = method.getName();
复制代码
```

7、得到所有参数类型的类类型数组

```
Class[] paramTypes = method.getParameterTypes();
复制代码
```

8、遍历参数类的类类型数组，得到某个参数的类类型class1

```
for (Class class1 : paramTypes)
复制代码
```

9、得到该参数的类型名

```
String paramName = class1.getName();
复制代码
```

例子：

```
private static void getClass2GetMethod(){
        StudentInfo studentInfo = new StudentInfo();
        Class<? extends StudentInfo> class1 = studentInfo.getClass();
        
        Method[] methods = class1.getDeclaredMethods();
        for (Method method : methods) {
            System.out.print("方法名 ： "+method.getName());
            Class<?>[] parameterTypes = method.getParameterTypes();
            for (Class<?> class2 : parameterTypes) {
                System.out.print(" 参数类型： "+class2.getName());
            }
            Class<?> returnType = method.getReturnType();
            System.out.println(" 返回值类型： "+returnType.getName());
        }
        
        try {
            //1、调用非静态方法
            Method method1 = class1.getDeclaredMethod("setName", String.class);
            Method method2 = class1.getDeclaredMethod("getName");
            method1.invoke(studentInfo, "李四");
            String name = (String) method2.invoke(studentInfo);
            System.out.println("调用非静态方法 getName()==name："+name);
            
            //2、调用静态方法：将invoke的第一个参数设置为null
            System.out.println("调用静态方法 test()：");
            Method method3 = class1.getDeclaredMethod("test");
            method3.invoke(null);
            Method method4 = class1.getDeclaredMethod("test", String.class);
            method4.invoke(null, "123456");
            
            //3、调用私有方法：方法调用之前，需要将方法setAccessible
            Method method5 = class1.getDeclaredMethod("getAge");
            method5.setAccessible(true);
            System.out.println("调用私有方法 getAge()=="+method5.invoke(studentInfo));
            
            //4、调用包含对象参数的方法(多个参数)：
            Method method6 = class1.getDeclaredMethod("showMsg", String.class,int.class,MsgClass.class);
            //Method method6 = class1.getDeclaredMethod("showMsg", new Class[]{String.class,int.class,MsgClass.class});
            //method6.invoke(studentInfo, new Object[]{"王小五",28,new MsgClass()});
            method6.invoke(studentInfo,"赵小六",28,new MsgClass());
            
        } catch (Exception e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
复制代码
```

### 5.3 获取类的成员变量

成员变量中都包括什么：成员变量类型+成员变量名

类的成员变量也是一个对象，它是java.lang.reflect.Field的一个对象，所以我们通过java.lang.reflect.Field里面封装的方法来获取这些信息。

###### 1、单独获取某个成员变量

- Field getField(String name)    根据变量名，返回一个具体的具有public属性的成员变量
- Field getDeclaredField(String name)    根据变量名，返回一个成员变量（不分public和非public属性）
- 参数是成员变量的名字。

例如一个类A有如下成员变量：

```
private int n;
复制代码
```

如果A有一个对象a，那么就可以这样得到其成员变量：

```
Class c = a.getClass();
Field field = c.getDeclaredField("n");
复制代码
```

###### 2、获取所有的成员变量

- Field[] getFields()    返回具有public属性的成员变量的数组
- Field[] getDelcaredField()    返回所有成员变量组成的数组（不分public和非public属性）

同样，如果想要获取所有成员变量的信息，可以通过以下几步：

1、已知一个对象，获取其类的类类型

```
Class c = obj.getClass();
复制代码
```

2、获取该类的所有成员变量，放在一个数组中

```
Field[] fields = c.getDeclaredFields();
复制代码
```

3、遍历变量数组，获得某个成员变量field

```
for (Field field : fields)
复制代码
```

4、得到成员变量类型的类类型

```
Class fieldType = field.getType();
复制代码
```

5、得到成员变量的类型名

```
String typeName = fieldType.getName();
复制代码
```

6、得到成员变量的名称

```
String fieldName = field.getName();
复制代码
```

例子：

```
private static void class2GetField() {
        Class<?> class1 =  StudentInfo.class;
        Field[] fields = class1.getDeclaredFields();
        for (Field field : fields) {
            System.out.print("变量名 ： "+field.getName());
            Class<?> type = field.getType();
            System.out.println(" 类型 ： "+type.getName());
        }
        
        try {
            //访问非私有变量
            StudentInfo studentInfo = new StudentInfo();
            Field field = class1.getDeclaredField("name");
            System.out.println(" 变量name== "+field.get(studentInfo));
            Field field2 = class1.getDeclaredField("school");
            System.out.println(" 静态变量school== "+field2.get(studentInfo));
            
            //访问私有变量
            Field field3 = class1.getDeclaredField("age");
            field3.setAccessible(true);
            System.out.println(" 私有变量age== "+field3.get(studentInfo));
            field3.set(studentInfo, 18);
            System.out.println(" 私有变量age== "+field3.get(studentInfo));

        } catch (Exception e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }

    }
复制代码
```

### 5.4 获取类、方法、属性的修饰域

类Class、Method、Constructor、Field都有一个public方法int getModifiers()。该方法返回一个int类型的数，表示被修饰对象（ Class、 Method、 Constructor、 Field ）的修饰类型的组合值。

  在开发文档中，可以查阅到，Modifier类中定义了若干特定的修饰域，每个修饰域都是一个固定的int数值，列表如下： 

![image](https://user-gold-cdn.xitu.io/2018/8/23/16565e76f456f048?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



  该类不仅提供了若干用于判断是否拥有某中修饰域的方法boolean isXXXXX(int modifiers)，还提供一个String toString(int modifier)方法，用于将一个表示修饰域组合值的int数转换成描述修饰域的字符串。



![image](https://user-gold-cdn.xitu.io/2018/8/23/16565e76f443929a?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



### 5.5 通过反射获取私有成员变量和私有方法

Person类

```
public class Person {
private String name = "zhangsan";
private String age;
 
public String getName() {
    return name;
}
 
public void setName(String name) {
    this.name = name;
}
}  

    //main函数
 
    Person person = new Person();
    //打印没有改变属性之前的name值
    System.out.println("before：" + getPrivateValue(person, "name"));
    person.setName("lisi");
    //打印修改之后的name值
    System.out.println("after：" + getPrivateValue(person, "name"));
 
 
 
/**
 * 通过反射获取私有的成员变量
 *
 * @param person
 * @return
 */
private Object getPrivateValue(Person person, String fieldName) {
 
    try {
        Field field = person.getClass().getDeclaredField(fieldName);
        // 参数值为true，打开禁用访问控制检查
        //setAccessible(true) 并不是将方法的访问权限改成了public，而是取消java的权限控制检查。
        //所以即使是public方法，其accessible 属相默认也是false
        field.setAccessible(true);
        return field.get(person);
    } catch (Exception e) {
        e.printStackTrace();
    }
    return null;
}  
复制代码
```

运行结果： 

![image](https://user-gold-cdn.xitu.io/2018/8/23/16565e7803548277?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



获取私有方法的方式类似获取私有成员变量的方式 Filed类，Method类等详细查看开发者文档： [developer.android.com/intl/zh-cn/…](https://link.juejin.im?target=http%3A%2F%2Fdeveloper.android.com%2Fintl%2Fzh-cn%2Freference%2Fjava%2Flang%2Freflect%2FField.html)

## 六、关于反射的一些高级话题

如果说前面那些属于Java反射的基本知识，那么在文章的最后，我们来探讨一下反射的一些高级话题。另外，本文对基础知识的讲解仅属于抓主干，具体的一些旁支可以自己参看文档。需要提一下的是，Java反射中对数组做过单独的优化处理，具体可查看java.lang.reflect.Array类；还有关于泛型的支持，可查看java.lang.reflect.ParameterizedType及相关资料。

暂时想到的高级话题有三个，由于对Java反射理解的也不算深入，所以仅仅从思路上进行探讨，具体实现上，大家可以参考其他相关资料，做更深入研究。

### Android编译期问题

Android的安全权限问题我把它简单的划分成三个层次，最不严格的一层就是仅仅骗过编译器的“@hide”标记。对于一款开源的操作系统而言，这个标记本身并不具备安全上的限制。不过，从上次Google过来的负责Android工程师的说法来看，这个标记的作用更多的是方便硬件厂商做闭源的二次开发。这样解释倒也说得过去。

不过这并不影响我们使用反射机制以绕过原生Android的第一层安全措施。如果你熟悉源码的话，会发现这可以应用到很多地方。并且最关键的是你并不需要放在源码中编译，而是像普通应用程序的开发过程一样。

具体使用范围我不能一一列举了，例如自定义窗口、安装程序等等。简单的说，在Android上使用反射技术，你才会对Android系统有更深的理解和更高的控制权。

### 软件的解耦合

我们在架构代码的时候，经常提到解耦合、弱耦合。其实，解耦和不仅仅只能在代码上做文章。我们可以考虑这样一种情况：软件的功能需求不可能一开始就完全确定，有一些功能在软件开发的后期甚至是软件已经发布出去之后才想到要加入或者去掉。 按我们惯有的思维，这种情况就得改动源码，重新编译。如果软件已经发布出去，那么就得让客户重新安装一次软件。反思一下，我们是否认为软件和程序是同一回事呢？事实上，如果你能将软件和程序分开来理解，那么你会发现，为了应对以上的情况，我们还有其他的解决办法。

我国有一个很重要但是很麻烦的制度，那就是户籍制度。它的本意是为了更好的管理人口事宜。每当一个孩子出生，我们就需要在户籍管理的地方去给他办理户籍入户；而每当一个人去世，我们也需要在相应的地方销去他的户籍。既然我们可以视类为生命，那么我们能否通过学习这样的户籍管理制度来动态地管理类呢？

事实上这样的管理是可行的，而且Java虚拟机本身正是基于这样的机制来运行程序的。因此我们是否可以这样来架构软件框架。首先，我们的软件有一个配置文件，配置文件其实是一个文本，里面详细描述了，我们的软件核心部分运行起来后还需要从什么路径加载些什么类需要何时调用什么方法等。这样当我们需要加或减某些功能时，我们只需要简单地修改配置文本文件，然后删除或者添加相应的.class文件就可以了。

如果你足够敏感，你或许会发现，这种方式形成的配置文件几乎可以相当于一门脚本语言了。而且这个脚本的解释器也是我们自己写的，另外关键是它是开发的，你可以为它动态地加入一些新的类以增加它的功能。

不要以为这仅仅是一个设想，虽然要开发成一门完备的脚本语言确实比较麻烦。但是在一些网络端的大型项目中，通过配置文件 + ClassLoader + 反射机制结合形成的这种软件解耦和方式已经用得比较普遍了。 所以，在此我不是在提出一种设想，而是在介绍业界处理此类问题的一种解决方案。

### 反射安全

文章读到这里，我想你应该由衷地感叹，Java反射机制实在是太强大了。但是，如果你有一些安全意识的话，就会发现Java这个机制强大得似乎有些过头了。前面我们提到，Java反射甚至可以访问private方法和属性。为了让大家对Java反射有更全面的了解，树立正确的人生观价值观，本小节将对Java的安全问题做一个概要性的介绍。

相对于C++来说，Java算是比较安全的语言了。这与它们的运行机制有密切的关系，C++运行于本地，也就是说几乎所有程序的权限理论上都是相同的。而Java由于是运行于虚拟机中，而不直接与外部联系，所以实际上Java的运行环境是一个“沙盒”环境。 Java的安全机制其实是比较复杂的，至少对于我来说是如此。作为Java的安全模型，它包括了：字节码验证器、类加载器、安全管理器、访问控制器等一系列的组件。之前文中提到过，我把Android安全权限划分为三个等级：第一级是针对编译期的“@hide”标记；第二级是针对访问权限的private等修饰；第三级则是以安全管理器为托管的Permission机制。

Java反射确实可以访问private的方法和属性，这是绕过第二级安全机制的方法（之一）。它其实是Java本身为了某种目的而留下的类似于“后门”的东西，或者说是为了方便调试？不管如何，它的原理其实是关闭访问安全检查。

如果你具有独立钻研的精神的话，你会发现之前我们提到的Field、Method和Constructor类，它们都有一个共同的父类AccessibleObject 。AccessibleObject 有一个公共方法：void setAccessible(boolean flag)。正是这个方法，让我们可以改变动态的打开或者关闭访问安全检查，从而访问到原本是private的方法或域。另外，访问安全检查是一件比较耗时的操作，关闭它反射的性能也会有较大提升。

不要认为我们绕过了前两级安全机制就沾沾自喜了，因为这两级安全并不是真正为了安全而设置的。它们的作用更多的是为了更好的完善规则。而第三级安全才是真正为了防止恶意攻击而出现的。在这一级的防护下，你甚至可能都无法完成反射（ReflectPermission），其他的一切自然无从说起。

## 七、通过反射了解集合泛型的本质

首先下结论：

Java中集合的泛型，是防止错误输入的，只在编译阶段有效，绕过编译到了运行期就无效了。 下面通过一个实例来验证：

```
package com.Soul.reflect;

import java.lang.reflect.Method;
import java.util.ArrayList;
import java.util.List;

/**
 * 集合泛型的本质
 * @description
 * @author Soul
 * @date 2016年4月2日上午2:54:11
 */
public class Generic {
    public static void main(String[] args) {
        List list1 = new ArrayList(); // 没有泛型 
        List<String> list2 = new ArrayList<String>(); // 有泛型


        /*
         * 1.首先观察正常添加元素方式，在编译器检查泛型，
         * 这个时候如果list2添加int类型会报错
         */
        list2.add("hello");
//      list2.add(20); // 报错！list2有泛型限制，只能添加String，添加int报错
        System.out.println("list2的长度是：" + list2.size()); // 此时list2长度为1


        /*
         * 2.然后通过反射添加元素方式，在运行期动态加载类，首先得到list1和list2
         * 的类类型相同，然后再通过方法反射绕过编译器来调用add方法，看能否插入int
         * 型的元素
         */
        Class c1 = list1.getClass();
        Class c2 = list2.getClass();
        System.out.println(c1 == c2); // 结果：true，说明类类型完全相同

        // 验证：我们可以通过方法的反射来给list2添加元素，这样可以绕过编译检查
        try {
            Method m = c2.getMethod("add", Object.class); // 通过方法反射得到add方法
            m.invoke(list2, 20); // 给list2添加一个int型的，上面显示在编译器是会报错的
            System.out.println("list2的长度是：" + list2.size()); // 结果：2，说明list2长度增加了，并没有泛型检查
        } catch (Exception e) {
            e.printStackTrace();
        }

        /*
         * 综上可以看出，在编译器的时候，泛型会限制集合内元素类型保持一致，但是编译器结束进入
         * 运行期以后，泛型就不再起作用了，即使是不同类型的元素也可以插入集合。
         */
    }
}
复制代码
```

输出结果

list2的长度是：1 true list2的长度是：2

作者：YunSoul

链接：https://juejin.im/post/5b7e708b51882542b83d70c9

来源：掘金

著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。