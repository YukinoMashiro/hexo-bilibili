---
title: Java语法
author: Yukino
avatar: https://cdn.jsdelivr.net/gh/Yukino831143/CDN/img/yukino/avatar/a26.ico #头像地址
authorLink: /# #头像跳转链接
authorAbout: NULL
authorDesc: NULL
categories: 技术 #public/catagories文章目录
date: 2021-1-17 20:00:00
comments: true #是否开启评论
tags: #文章标签
  - 日积跬步
keywords: Docker #这个暂时没找到用户
description: 项目中遇到的一些语法或表达 #首页文章简介
photos: https://cdn.jsdelivr.net/gh/Yukino831143/CDN/img/yukino/article_cover/ #首页的文章的封面图
banner: https://cdn.jsdelivr.net/gh/Yukino831143/CDN/img/yukino/banner/1.jpg #文章详情页的banner
---
# 1. stream() 使用
1. stream().map

```Java
List<Integer> list = Arrays.asList(1,2,3,4);
List<Integer> list2 = list.stream().map(l1 -> l1 * 2).collect(Collectors.toList());
```

```java
List<String> list3 = Arrays.asList("a","b","cc","dd");
List<String> list4 = list3.stream().map(String::toUpperCase).collect(Collectors.toList());
```

```java
List<String> list3 = Arrays.asList("a","b","cc","dd");
//映射规则可以是一个函数
List<String> list4 = list3.stream().map(l1 -> {return l1.toUpperCase();}).collect(Collectors.toList());
```

stream().map 表示将数组、列表等的元素按照一定规则进行映射

`collect(Collectors.toList());`表示将映射结果转换成列表

`collect(Collectors.toSet());`表述转换成集合，可以达到去重目的



2. stream().filter

```java
List<Integer> list = Arrays.asList(1,2,3,4);
List<Integer> list1 = list.stream().filter(l1 -> l1.intValue() > 3).collect(Collectors.toList());
```

stream().filter 表示将数组、列表等的元素按照一定规则进行筛选,保留的是满足上面条件的

3. Collectors.toMap

实现list 转 map

```java
@Data
@AllArgsConstructor
class User {
    private Integer id;
    private String name;
}
```

```java
List<User> list5 = Arrays.asList(
  new User(1, "yukino"),
  new User(2, "mashiro"),
  new User(3, "yui")
);
Map<Integer, String> collect = list5.stream().collect(Collectors.toMap(User::getId, User::getName));
```



4. Collectors.joining

实现字符串拼接

```java
String[] name = {"yukino*", "mash?iro", "yu+i"};
Stream<String> stringStream = Stream.of(name);

String collect = stringStream
  .map(t -> t.replace("*", "").replace("+", "").replace("?", ""))
  .collect(Collectors.joining(",", "[", "]"));
System.out.println(collect);
```

`[yukino,mashiro,yui]`



5. stream().flatMap

**作用**

列表中的元素含有列表成员，取出元素，再将元素成员列表的元素取出来，进行合并

```java
@Data
@AllArgsConstructor
class TagDTO {
    private String categoryName;
    private List<String> tags;
}

public static void main(String[] args) {
  TagDTO tagDTO1 = new TagDTO("开发语言", Arrays.asList("Java", "Python", "C"));
  TagDTO tagDTO2 = new TagDTO("开发工具", Arrays.asList("IDEA", "Tomcat"));
  List<TagDTO> tagDTOS = Arrays.asList(tagDTO1, tagDTO2);
  List<String> collect = tagDTOS.stream().flatMap(tag -> tag.getTags().stream()).collect(Collectors.toList());
  System.out.println(collect);
}
```

`[Java, Python, C, IDEA, Tomcat]`

# 2. Map foreach的使用

```java
Map<String, String> infoMap = new HashMap<>();
        infoMap.put("name","yukino");
        infoMap.put("site","www.yukino.tech");
        infoMap.put("email", "xxxx");
        infoMap.forEach((key,value) -> {
            System.out.println(key + ":" + value);
        });
```



# 3. PriorityQueue

PriorityQueue，即优先队列，由小顶堆组成

```java
@Data
@AllArgsConstructor
class HotTagDTO implements Comparable {
    private String name;
    private Integer priority;
    
  	//此方法是元素比较大小的依据
    @Override
    public int compareTo(Object o) {
        return this.getPriority() - ((HotTagDTO) o).getPriority();
    }
}
```

```java
    public static void main(String[] args) {
        PriorityQueue<HotTagDTO> priorityQueue = new PriorityQueue<>(10);
        priorityQueue.add(new HotTagDTO("yukino", 1));
        priorityQueue.add(new HotTagDTO("mashiro",2));
        System.out.println(priorityQueue.peek().getName());
    }
```

1. `add(E e)`和`offer(E e)`的语义相同，都是向优先队列中插入元素，只是`Queue`接口规定二者对插入失败时的处理不同，前者在插入失败时抛出异常，后则则会返回`false`。
2. `element()`和`peek()`的语义完全相同，都是获取但不删除队首元素，也就是队列中权值最小的那个元素，二者唯一的区别是当方法失败时前者抛出异常，后者返回`null`。根据小顶堆的性质，堆顶那个元素就是全局最小的那个
3. `remove()`和`poll()`方法的语义也完全相同，都是获取并删除队首元素，区别是当方法失败时前者抛出异常，后者返回`null`。
4. `remove(Object o)`方法用于删除队列中跟`o`相等的某一个元素（如果有多个相等，只删除一个）