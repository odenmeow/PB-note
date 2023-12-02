# References

---

    at.com.home.datastruct.TreeSet  提供三個範例
    /Onini_0316/src/at/com/home/datastruct/TreeSet/TreeSet_comparator.java

---

# Collection 家族

**<** 點標籤 快速前往 **>**

#### [``Set``](#Collection-Set)

#### [``Map``](#Collection-Map)

#### [``List``](#Collection-List)

---

<a id="Collection-Set"></a>

## Set

     1.如果要更改元素 先移出 改好再加入  **Set不能直接改物件否則導致不確定行為**
    2.重複元素不會加入
    3.常見實現 :
    
        HashSet 、 LinkedHashSet 、 TreeSet 

### 【 HashSet 】

``無順序性``                     
``HashTable 實現`` 

當 CRUD 操作時， 透過``hashCode()`` 快速定位 如果重複 再去看``equals()`` 所以兩個都要定義

```java
    @Override
    public boolean equals(Object o) {
        if (this == o) return true; // 如果是同一对象，直接返回 true
        if (o == null || getClass() != o.getClass()) return false; // 如果对象为 null 或不是同一类型，返回 false
        MyClass myClass = (MyClass) o; // 将对象强制类型转换为 MyClass 类型
        return value == myClass.value; // 比较对象的内容
    }

    @Override
    public int hashCode() {
        return Objects.hash(value); // 重写 hashCode 方法，通常是根据对象内容生成哈希码
    }
```

### 【 TreeSet 】

1. ``有順序性``

2. ``一定要實現comparable`` 或 ``給予comparator``

3. **依**  ``<提供的比較器>`` **而非** ``<插入順序>`` 
- ```/Onini_0316/src/at/com/home/datastruct/TreeSet   三個範例有說明```

### 【 LinkedHashSet】

1. ``有順序性``
2. ``依照插入順入``

### 【Set 使用範例】

```java
        Set<String> set1 = new HashSet<>();
        Set<String> set2 = new HashSet<>();
        Set<String> intersection = new HashSet<>(set1);
        intersection.retainAll(set2);  // 交集
        Set<String> union = new HashSet<>(set1);
        union.addAll(set2);  // 聯集
        Set<String> difference = new HashSet<>(set1);
        difference.removeAll(set2);  // 差集        
```

---

<a id="Collection-Map"></a>

## Map

```
1. key : value 的方式  Map<String,String> map=new HashMap<>();
2. 記得提供 hashCode跟 equals 避免衝突 不知道要不要過濾該物件  (如果採用預設 可能導致 內容相同但是重複加入 collection之中)    
3. 用來作為判斷依據 的 hashCode跟 equals 使用的屬性 不可以被更動 (需要immutable)  否則會可能導致 未來找不到

4. TreeMap LinkedHashMap EnumMap          前三者 常 用

5. ConcurrentHashMap  WeakHashMap IdentityHashMap(比較引用reference)   三者 暫時比較沒可能使用到 
```

<a id="Collection-List"></a>        

## List

---

### 網址參考:

> Java双端队列Deque使用详解 https://blog.csdn.net/devnn/article/details/82716447 
>  Stack 跟 Queue  https://justnote.coderbridge.io/2022/03/06/stack-and-queue/

### 範例參考:

> ../../Onini_0316/src/at/com/home/leetcode/_20_Easy_ValidParentheses.java

```java
    List<Cat> list=new ArrayList<>(); 
    Stack 跟 Queue 
    Queue<Cat> queue=new LinkedList<>();
    vs
    Deque<Cat> deque=new LinkedList<>();    //高插入寫入
    Deque<Cat> stack=new ArrayDeque<>();    //高訪問使用
```

- `Queue` : `FIFO`  兩個端點(front /rear)，新增會從佇列尾端放入，刪除則從最前端的舊資料先刪。

- `Stack` : `FILO`  `過時技術` ( base on Vector Not List  ) 但List也能做出類似功能 ! 
    上面兩者參考:
  Onini_0316/src/at/com/home/leetcode/_20_Easy_ValidParentheses.java

- `Deque`: `比起Stack建議用Deque替代` 、`開頭非Concurrent幾乎都非Threadsafe`

| Deque | 第一個元素`head`   | -             | 最後的元素`tail`  | -            |
|:-----:|:-------------:|:-------------:|:------------:|:------------:|
| 操作    | 拋出異常          | 特殊值           | 拋出異常         | 特殊值          |
| 插入    | addFirst(e)   | offerFirst(e) | addLast(e)   | offerLast(e) |
| 刪除    | removeFirst() | pollFirst()   | removeLast() | pollLast()   |
| 偷看    | getFirst()    | peekFirst()   | getLast()    | peekLast()   |

| Queue方法   | 等效Deque方法     | vs  | Stack方法 | 等效Deque方法     |
|:---------:|:-------------:|:---:|:-------:|:-------------:|
| add(e)    | addLast(e)    |     | push(e) | addFirst(e)   |
| offer(e)  | offerLast(e)  |     | pop()   | removeFirst() |
| remove()  | removeFirst() |     | peek()  | peekFirst()   |
| poll()    | pollFirst()   |     |         |               |
| element() | getFirst()    |     |         |               |
| peek()    | peekFirst()   |     |         |               |

- `Vector` : 🔥<span style="color: #FFFF22;">threadSafe </span> 🔥但更推薦用 `ConcurrentLinkedQueue`， Vector 增長默認 now*2

- `LinkedList` :  non thread-safe

- `ArrayList`  :     non thread-safe  ， 增長默認+0.5*now

- 大多數 `List` 都非 thread-safe  

- `開發者自己處理安全問題`

- `ArrayBlockingQueue<E>`、還有許多其他衍生很方便的類別
