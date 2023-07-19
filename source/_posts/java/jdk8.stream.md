---
title: 关于Jdk1.8中Stream的一些API的解释
excerpt: 关于Jdk1.8中Stream的一些API的解释
date: 2023/07/08 15:46:25
comments: true
updated:
categories: 
  - Java
tags: 
  - Java
---
## 如何创建一个Steam流

实现了`Collection`接口的类，都可以使用stream流

```java
public interface Collection<E> extends Iterable<E> {
    default Stream<E> stream() {
        return StreamSupport.stream(spliterator(), false);
    }

    /**
     * Returns a possibly parallel {@code Stream} with this collection as its
     * source.  It is allowable for this method to return a sequential stream.
     *
     * <p>This method should be overridden when the {@link #spliterator()}
     * method cannot return a spliterator that is {@code IMMUTABLE},
     * {@code CONCURRENT}, or <em>late-binding</em>. (See {@link #spliterator()}
     * for details.)
     *
     * @implSpec
     * The default implementation creates a parallel {@code Stream} from the
     * collection's {@code Spliterator}.
     *
     * @return a possibly parallel {@code Stream} over the elements in this
     * collection
     * @since 1.8
     */
    default Stream<E> parallelStream() {
        return StreamSupport.stream(spliterator(), true);
    }
}
```

也可以采用工具类Arrays将字符串、数组转换为List，从而使用`Collection`中的stream

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
List<Integer> evenNumbers = numbers.stream()
                                   .filter(n -> n % 2 == 0)
                                   .collect(Collectors.toList());
```

`Stream.of`：可以使用该方法创建一个包含指定元素的流。

`Arrays.stream`：可以使用该方法将数组转换为流。

`IntStream.range`和`IntStream.rangeClosed`：可以使用这两个方法创建指定范围的整数流。

`Stream.generate`和`Stream.iterate`：可以使用这两个方法创建无限流。

## API介绍

1. 归约（Reduce）

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
int sum = numbers.stream()
                 .reduce(0, Integer::sum);
```

这个示例使用reduce操作将集合中的元素求和。


2. 扁平化（FlatMap）

```java
List<List<Integer>> numberLists = Arrays.asList(
    Arrays.asList(1, 2, 3),
    Arrays.asList(4, 5, 6),
    Arrays.asList(7, 8, 9)
);
List<Integer> flattenedList = numberLists.stream()
                                         .flatMap(Collection::stream)
                                         .collect(Collectors.toList());
```

flatMap操作用于将嵌套的集合扁平化为一个单一的集合。

3. 并行处理

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
int sum = numbers.parallelStream()
                 .reduce(0, Integer::sum);
```

通过使用parallelStream方法，我们可以将Stream的处理并行化，从而提高处理大量数据的效率。
也可以使用`stream().parallel()`。

使用`sequential()`可切换为串行流

4. 分组（groupingBy）

将流中的元素按照指定的条件进行分组，返回一个Map，其中键是分组的条件，值是对应的元素列表。

5. 分区（partitioningBy）

将流中的元素按照指定的条件进行分区，返回一个Map，其中键是分区的条件（true或false），值是对应的元素列表。

6. 提前终止操作

findFirst：返回流中的第一个元素。

findAny：返回流中的任意一个元素。

anyMatch：判断流中是否存在满足指定条件的元素。

allMatch：判断流中的所有元素是否都满足指定条件。

noneMatch：判断流中是否没有任何元素满足指定条件。
