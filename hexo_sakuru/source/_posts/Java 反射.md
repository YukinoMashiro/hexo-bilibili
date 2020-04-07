# 1.反射的概述
![image](http://note.youdao.com/yws/res/1099/62A6DC512D7B40FA99475D39AB7B8840)
![image](http://note.youdao.com/yws/res/1097/44B7B2835AB140F08FDB64650AB8F444)
![image](http://note.youdao.com/yws/res/1101/0ABCF6AF7F954F07B6543003B86C06E3)
![image](http://note.youdao.com/yws/res/1105/821881325DAB446CB5B94ACA1208A332)
# 2.反射

![image](http://note.youdao.com/yws/res/1122/5A8AEAA5B7EC4916B300D1DEE7AE478D)



```Java
//Person.Java
package com.imooc.reflect.test;

public class Person {
	public String name;
	private String sex;
	
	public Person() {
		super();
	}
	public Person(String name, String sex) {
		super();
		this.name = name;
		this.sex = sex;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getSex() {
		return sex;
	}
	public void setSex(String sex) {
		this.sex = sex;
	}
	public void eat() {
		System.out.println("吃....");
	}
	private void run() {
		System.out.println("跑....");
	}
	
	private String sayHello(String name) {
		return "hello " + name;
	}
	@Override
	public String toString() {
		return "Person [name=" + name + ", sex=" + sex + "]";
	}
	
	
	
}

```
## 2.1 Class类
![image](http://note.youdao.com/yws/res/1108/4FABF444E06748E1B070304399CC78BF)

```Java
//ClassTest.java
package com.imooc.reflect.test;

import org.junit.Test;

public class ClassTest {
	@Test
	/**
	 * 获得Class对象
	 * 1.通过类名.class
	 * 2.对象.getClass();
	 * 3.Class.forName();
	 */
	public void demo1() throws ClassNotFoundException {
		//1.通过类名.class的方式
		Class clazz1 = Person.class;
		//2.通过对象.getClass()的方式
		Person person = new Person();
		Class clazz2 = person.getClass();
		//3.Class类forName();获得（一般使用这种）
		Class clazz3 = Class.forName("com.imooc.reflect.test.Person");
	}
}

```
# 2.2 Constructor类
![image](http://note.youdao.com/yws/res/1110/452D7B6D3BD54B10BB2ABC64D70EC45C)
```Java
//ConstructorTest.java
package com.imooc.reflect.test;

import java.lang.reflect.Constructor;

import org.junit.jupiter.api.Test;

public class ConstructorTest {
	@Test
	/**
	 * 获得无参数的构造方法
	 */
	public void demo1() throws Exception {
		//获得类的字节码文件对应的对象
		Class class1 = Class.forName("com.imooc.reflect.test.Person");
		//获取构造函数
		Constructor c = class1.getConstructor();
		//new一个对象，相当于 Person person = new Person();
		Person person = (Person)c.newInstance();
		person.eat();
	}
	
	@Test
	/**
	 * 获得有参数的构造方法
	 */
	public void demo2() throws Exception {
		Class class1 = Class.forName("com.imooc.reflect.test.Person");
		//获取带参构造函数
		Constructor c = class1.getConstructor(String.class,String.class);
		Person person = (Person)c.newInstance("张三","男");
		System.out.println(person);
	}
}

```
# 2.3 FiledTest类
![image](http://note.youdao.com/yws/res/1115/8051C20E3CC04E298FEC377CA897EB8A)

```Java
//FiledTest.java
package com.imooc.reflect.test;

import java.lang.reflect.Field;

import org.junit.jupiter.api.Test;

public class FiledTest {
	
	@Test
	//测试公有属性
	public void demo1() throws Exception {
		//获得Class
		Class class1 = Class.forName("com.imooc.reflect.test.Person");
		//获得属性,这里name 是公有的
		Field field = class1.getField("name");
		//操作属性 p.name = "";
		Person p = (Person)class1.newInstance();
		field.set(p, "李四");
		//获得属性值
		Object obj = field.get(p);
		System.out.println(obj);
	}
	@Test
	//测试私有的属性
	public void demo2() throws Exception {
		//获得Class
		Class class1 = Class.forName("com.imooc.reflect.test.Person");
		//获得私有属性
		Field field = class1.getDeclaredField("sex");
		//操作属性
		Person p = (Person)class1.newInstance();
		//私有属性，需要设置一个可访问的权限
		field.setAccessible(true);
		field.set(p, "男");
		//获取值
		Object obj = field.get(p);
		System.out.println(obj);
		System.out.println(p);
	}
	
	
}

```


## 2.4 Method类
![image](http://note.youdao.com/yws/res/1137/0F042BE796BA49AEB204108A1916F81C)
```Java
//MethodTest.java
package com.imooc.reflect.test;

import java.lang.reflect.Method;

import org.junit.jupiter.api.Test;

public class MethodTest {
	@Test
	//测试公有方法
	public void demo1() throws Exception{
		Class class1 = Class.forName("com.imooc.reflect.test.Person");
		//实例化
		Person person = (Person) class1.newInstance();
		//获得公有方法
		Method method = class1.getMethod("eat");
		//执行该方法,person.eat
		method.invoke(person);
	}
	
	@Test
	//测试私有方法
	public void demo2() throws Exception {
		Class class1 = Class.forName("com.imooc.reflect.test.Person");
		//实例化
		Person person = (Person) class1.newInstance();
		//获得方法
		Method method = class1.getDeclaredMethod("run");
		//设置私有的属性的访问权限
		method.setAccessible(true);
		//执行该方法
		//method.invoke(person);
		method.invoke(person,null);
	}
	
	@Test
	//测试私有方法带参数,也可以是公有方法带参数
	public void demo3() throws Exception {
		Class class1 = Class.forName("com.imooc.reflect.test.Person");
		//实例化
		Person person = (Person) class1.newInstance();
		//获得该方法
		Method method = class1.getDeclaredMethod("sayHello", String.class);
		//设置访问权限
		method.setAccessible(true);
		//执行
		Object obj = method.invoke(person, "Tom");
		System.out.println(obj);
		
	}
}

```