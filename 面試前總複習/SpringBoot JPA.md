#面試參考網址

>  [心得] 2022後端面試心得 https://www.ptt.cc/bbs/Soft_Job/M.1671730127.A.173.html 

# JPA Repository 使用須知

---

```java
    稍微講解下
    public interface CartRepository extends CrudRepository<CartBean, Integer> {
        List<CartBean> findByuserID(int userID);
        List<CartBean> findAllByuserID(int userID); 跟上面依樣功能~
    }
```

- **CrudRepository** **<**``實體類`` , ``UUID、Long、Integer``**>**

- **常見資料**
  
  | 數據類型       | 範圍            | bit |
  | ---------- | ------------- | --- |
  | Integer    | 2147483647    | 32  |
  | Long       | 約 $ 10^{18} $ | 64  |
  | BigInteger | 約 $ 10^{18} $ | 64  |
  | UUID       | 幾乎不可能重複       | 128 |

- **UUID**     
  
    ``128bit`` 通常使用32個16進制表示``550e8400``-``e29b``-``41d4``-``a716``-``446655440000``

- **BigInteger**
    ```MySQL``` 可以設定 ``Sign`` 900兆 ``unsign`` 1900多兆 
  
  <br>
  > **使用要注意**
  ```
  SQL    unsign > 900兆  轉過來JAVA  BigInteger
  SQL    sign   < 900兆              Long  
  ```
  <br>
  <br>

#Eager v.s. Lazy
---

這邊取得college的時候`eager`會一口氣一起取得`List< Student > students`

```java
    @Entity
    public class College {
        @Id
        @GeneratedValue(strategy=GenerationType.AUTO)
        private int collegeId;
        private String collegeName;
        @OneToMany(targetEntity=Student.class, mappedBy="college", fetch=FetchType.EAGER)
        private List<Student> students;
    }
```

如果`lazy`就是之後如果 `College物件.students` 才會查詢!

#GPT 回答關於序列化
---

如果有某個物件不希望被序列化，可以使用 `transient` 關鍵字標記類別中的字段，或使用 `static` 關鍵字標記字段，或使用 `transient` 和 `static` 兩個關鍵字一同標記。

當一個欄位被宣告為 `transient` 時，它將不會參與 `Java` 的預設序列化過程。這意味著，即使該物件被標記為 `Serializable`，標記為 `transient` 的欄位將不會被序列化。另一方面，`static` 關鍵字標記的欄位也不參與序列化，因為它們與類別的狀態相關，而不是與實例的狀態相關。

舉個例子：

```java
import java.io.Serializable;

public class MyClass implements Serializable {
    private transient int sensitiveData; //這個欄位不會被序列化

    private static int someStaticValue; // 這個欄位不會被序列化

    // 其他fields 和 方法
}
```

這樣做是為了避免某些字段被序列化，特別是一些敏感資訊或與特定環境相關的字段。
<br><br>

# @Entity

---

<br>

關於``Date``這個 ``物件``
會需要用到剛剛序列化
下面介紹關於  可能如何使用

> **使用JPA提供的可以效率up一些，無須如下轉Json + implements Serializable**

```java
@JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd", timezone="GMT+8")
Date birthday;
String extra;

@JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd HH:mm:ss SSS", timezone="GMT+8")
Timestamp registerTime;
```

> **直接使用就可以**

```java
@Column(name = "created_time", nullable = false, updatable = false)
private LocalDateTime createdTime = LocalDateTime.now();

@Column(name = "upgraded_time", nullable = false)
private LocalDateTime upgradedTime = LocalDateTime.now();
```

`JPA` 的實體類別（具有 `@Entity` 註解的類別）**不需要強制**要求實作 `Serializable` 介面。 `JPA` 實體類別是由 `JPA` 容器管理的，它們被認為是持久化對象，並不需要手動進行序列化和反序列化。通常，`JPA` 提供的持久化機制已經能夠管理物件的序列化和持久化，因此在實體類別上手動新增 `Serializable` 是可選的，而非必須。

#Lambda 小知識
---

**通常不予修改lambda外的變數，`Lambda` is a `funcation`，不希望使用者能改外部 !**

> **Tip : 可以透過下面方式 修改Scope外的variable**

```java
    public class LambdaExample {
    public static void main(String[] args) {
        int[] num = {10};

        // 透過陣列 包裹物件    或者 
        // ArrayList 之類 Collection包裹就可偷渡 
        Runnable r = () -> {
            num[0] = 20; 
        };

        r.run();


        System.out.println(num[0]); // 输出 20
    }
}
```

# Lombok

---

##範例參考

> C:\Users\qw284\IdeaProjects\springboot\src\main\java\com\oni\training\springboot\LombokExample

範例可以了解下面使用方法

- @AllArgsConstructor
- @RequiredArgsConstructor
- @NoArgsConstructor
- @Data

# Jackson轉譯時注意

---

##範例參考

> C:\Users\qw284\IdeaProjects\springboot\src\main\java\com\oni\training\springboot\JacksonExample
> (com.oni.training.springboot.JacksonExample)
> https://github.com/odenmeow/SpringBoot4Learn/blob/b9a0818a98c0b3b06088992497617eb134f91541/src/main/java/com/oni/training/springboot/JacksonExample
> 上傳之後要 更新 <使用11月之後的版本才可能有內容>
> 優先參考貓咪2

<br>

##使用 TypeReference:

- Jackson 庫中用於在`運行時保留泛型類型資訊`的**抽象類**。

- 可以清楚告知不止 `TOrder_detail` ，該物件還是包在`List`內。
  
  ```java
    new TypeReference<List<TOrder_detail>>(){} 
  ```
  
  - { } 使用了`匿名函數` 實現抽象類別  

```java
    @Override
    public List<TOrder_detail> convertToEntityAttribute(String dbData) {
    List<TOrder_detail> tOrder_detail = null;
    try {
        tOrder_detail = mapper.readValue(dbData, new TypeReference<List<TOrder_detail>>() {});
    } catch (JsonProcessingException e) {
        // Handle the exception according to your requirements
        e.printStackTrace();
    }
    return tOrder_detail;
}
```

> **以下是上述做法產生的欄位內容**

```java
[
    {
        "item_id": 1,
        "item_name": "SwiftX SFX14-41G-R7QJ 金 14吋",
        "item_price": 23900,
        "item_amount": "3"
    },
    {
        "item_id": 4,
        "item_name": "Swift X SFX14-51G-70P8 綠 16吋",
        "item_price": 39900,
        "item_amount": "2"
    }
]
```

> **以下是之前的版本兩層轉換**

```java
{
    "orderDetails": [
        {
            "item_id": 3,
            "item_name": "Swift5 SF514-54T-79C0 白 14吋",
            "item_price": 42900,
            "item_amount": "1"
        },
        {
            "item_id": 4,
            "item_name": "Swift X SFX14-51G-70P8 綠 16吋",
            "item_price": 39900,
            "item_amount": "2"
        }
    ]
}
```

<br><br>

#N+1 query 效能問題
---

 **N+1 query是指在處理關聯式數據庫時常見的效能問題之一。**

- 例如，假設你有一個存儲訂單的資料庫，並且每個訂單都有關聯的客戶。當你要查詢所有訂單時，如果你使用了N+1查詢的方式，這樣的情況會是：首先執行1個查詢來獲取N個訂單，接著針對每個訂單，再執行N個單獨的查詢來獲取與每個訂單相關聯的客戶資訊。

這樣的查詢模式會導致額外的資料庫請求次數增加，可能會對數據庫性能造成負面影響，尤其是在處理大量資料時。通常，這些N+1查詢的問題可以通過使用JOIN（連接）或者批量載入（例如使用ORM框架中的eager loading或者指定使用批量載入）來改進。

> **解決方向**

```
    1.使用JOIN操作將相關的數據一次性地擷取出來。
    2.使用ORM框架的eager loading或者指定使用批量載入（例如Hibernate中的FetchType.EAGER）。
    3.考慮使用快取（caching）來減少重複請求。
    4.調整程式碼和查詢以盡可能減少不必要的數據讀取。
```

# SQL Injection & 如何避免

---

- 使用參數化查詢（Prepared Statements）：使用參數化的查詢，也就是使用預處理語句，這能夠防止用戶的輸入直接被解釋為SQL代碼。

```sql
    （使用預處理語句）：
    PreparedStatement pstmt = connection.prepareStatement("SELECT * FROM users WHERE username = ? AND password = ?");
    pstmt.setString(1, username);
    pstmt.setString(2, password);
    ResultSet rs = pstmt.executeQuery();
```

- `限制用戶輸入`：在接受用戶輸入時，限制輸入的類型和範圍，並進行驗證。這可以防止一些潛在的攻擊。

- `避免拼接SQL語句`：盡量避免使用字符串拼接的方式構造SQL語句，因為這樣容易受到SQL注入攻擊。

- `權限管理`：為了最小化潛在的損害，使用合適的數據庫權限，限制應用程式對數據庫的訪問範圍。

- `輸入過濾和轉義`（Input Filtering and Escaping）：對輸入進行適當的過濾和轉義處理，特殊字符（例如引號、分號等）應該進行轉義處理，以防止被誤解為SQL命令的一部分。

- `使用ORM框架`：使用ORM（Object-Relational Mapping）框架可以幫助隱藏大部分SQL語句的細節，並提供一個更安全的接口與數據庫交互。

綜合利用這些措施可以大幅降低應用程式遭受SQL注入攻擊的風險。

<br><br>

#Index 資料庫
---

## 來源網址

https://medium.com/@jinghua.shih/rails-%E7%B6%B2%E7%AB%99%E6%95%88%E8%83%BD%E5%84%AA%E5%8C%96-%E4%BA%8C-%E8%B3%87%E6%96%99%E5%BA%AB%E7%B4%A2%E5%BC%95-database-index-bd89fa3757a

> 大部分的 DBSM 都會預設把主鍵（Primary Key / ID）加上索引

實際情況下，A’ 這個擁有 A 的 ID 資料的儲存結構通常會以 `Hash` 或是 `B-tree` 實作。

- `Hash` 在搜尋不能重複的資料時，效率會比較好。
  因此適合用在主索引鍵（Primary Index）和唯一索引（Unique Index）；
  `B-tree` 適合用在可以允許重複資料的一般索引（Non-Unique Index）。

> 非主要欄位 Non-Primary Column

看使用者最常使用什麼欄位來獲取資料

- 我們可以自己定義主鍵以外的索引 : `first_name`。
- 或是使用複合式索引（composite indexes）:  `first_name + last_name。`

## 索引結構（Index Architecture）

### 叢集 Clustered

- 叢集索引（Clustered Index）直接修改原本的資料表順序。
  將資料按照要索引的欄位排，就是一個王牌的意思，如此可以讓資料搜尋非常有效率。
  尤其是`依照順序`（accessed sequentially）或是一個`範圍連續資料`的時候。
  想當然爾，一張資料表`只會有一個叢集索引`，通常就是主鍵欄位（primary key column）。
  
  <br>
  >  **因為叢集索引會調整資料順序，所以它最大的特色就是實體的資料（physical data rows）**
  **跟索引表（index block/index table）中的順序會是一樣的**。

### 非叢集 Non-Clustered

- 非叢集索引（Non-Clustered Index）是指不管資料在原本資料表中的排序，而在索引表中自己依照索引值建立排列。
  <br>
- 與叢集索引的差別是，叢集索引的排列順序就是實際上資料的排列順序，而非叢集索引的排列順序不會/無法影響到實際資料的排列順序。
  <br>
- 因此，一個資料表中`可以包含很多非叢集索引`。

> **通常會使用非叢集索引的會是在 JOIN, WHERE 或是 ORDER BY 
> 使用的非主鍵欄位（non-primary key columns）。**

### 使用索引的限制

- 索引之所以可以增加資料庫的效率，是因為它額外創造了一張表來儲存索引的資料。就是拿空間換取時間，這也是為什麼我們不能為每個欄位都加上索引的原因。
  
  <br>

- 再來，我們對資料庫做的任何更動都會連動到索引表（Index Table），如果這是一張更動很頻繁的表，那就會製造額外的負擔。

>    **加了索引，也不是萬能的，我們來看兩個狀況**

```sql
SELECT first_name FROM people WHERE last_name = ‘Shih’;
```

這個例子要尋找在people 中所有 last_name 是 ‘Shih’ 的 first_name資料，如果 last_name 欄位沒有被索引，那麼就會是一個 full table scan。如果有索引，那資料庫系統就會使用 B-tree 結構來尋找，大大增加搜尋效率，很好！

```sql
SELECT full_name FROM people WHERE full_name LIKE ‘%Shih’;
```

現在假設我們是要從 people中找到 full_name 的結尾是 ‘Shih’ 的full_name 資料， 這時候，就算full_name 有索引，還是會引發 full table scan，因為索引的預設搜尋方向是從左到右，當搜尋的字串開頭是% 外卡（`wildcard`），資料庫系統就沒有辦法使用 B-tree 了。

要解決這個困境可以增加一個 reverse(full_name) 的索引，然後改變 SQL 查詢：

```sql
SELECT full_name FROM people WHERE reverse(full_name) LIKE reverse(‘Shih’);
```

當然，我們就要自己衡量這樣值不值得了。

> **常會加上索引的欄位 :**

- 主鍵 Primary Key（通常是預設）
- 外部鍵 Foreign Key
- 常被放在查詢子句中（ORDER, WHERE, GROUP）的欄位

# SQL 印出 Procedure 內容

- <span style="color: #FFFF22;">查出來的表格有 `` + `` 選項可以選擇顯示全部 ! </span>`phpMyAdmin`
- 請先創造一個Procedure 名稱不一定要跟我一樣 !

> 使用下面可取得 `GetOrderCount` 的原始創建 `content`

```sql
SELECT ROUTINE_DEFINITION
FROM information_schema.ROUTINES
WHERE ROUTINE_NAME = 'GetOrderCount' AND ROUTINE_TYPE = 'PROCEDURE';
```

> 下面則是尋找有哪些預儲程式

```sql
SHOW PROCEDURE status;
```

> 可以尋找原始創建訊息(`Table`也適用)

```sql
SHOW CREATE PROCEDURE GETORDERCOUNT
```

# @ManyToMany

- 學生可以選多課

- 課可以被多個學生選
  
  ```java
  @Entity
  public class Student {
    // other fields
  
    @ManyToMany
    @JoinTable(name = "student_course",
            joinColumns = @JoinColumn(name = "student_id"),
            inverseJoinColumns = @JoinColumn(name = "course_id"))
    private Set<Course> courses;
  }
  ```

@Entity
public class Course {
    // other fields

    @ManyToMany(mappedBy = "courses")
    private Set<Student> students;

}

```
# 時間
---

- `LocalDateTime` 使用的時間不帶時區，所以就算獲得時間也不知道是哪個地區的時間。
- `ZonedDateTime` 加註時區資訊，能確定幾乎正確的(人類觀點)時間資訊。
- https://carricanbuild.medium.com/java-time-use-a1713b30163

```java
ZonedDateTime currentDateTime = ZonedDateTime.now(ZoneId.of("Asia/Taipei"));
```
