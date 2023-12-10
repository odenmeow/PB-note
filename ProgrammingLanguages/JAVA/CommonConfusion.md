# 1.ArrayInitialization

## Which two array initialization statements are valid?

```java
A.int array[3] = new int[] {1, 2, 3};

B.int array[] = new int[] {1,2,3};

C.int array[] = new int[3];

array = {1, 2, 3};

D.int array[] = new int[3] {1, 2, 3};

E.int array[] = new int[3];

array[0] = 1;

array[1] = 2;

array[2] = 3;
```

- B、E

## explanation :

# 2.TwoDimArrayForEach

package at.com.home.common_confuse.OCP;

## Given the code fragment:

```java
String shirts[][] = new String[2][2];

shirts[0][0] = “red”;

shirts[0][1] = “blue”;

shirts[1][0] = “small”;

shirts[1][1] = “medium”;
```

## Which code fragment prints red:blue:small:medium:?

```java
A)

for (String[] c : shirts) {

for (String s : c) {

System.out.print(s + “:”);

}

}

B)

for (int index = 1; index < 2; index++) {

for (int idx = 1; idx < 2; idx++) {

System.out.print(shirts[index][idx] + “:”);

}

}

C)

for (int index = 0; index < 2; ++index) {

for (int idx = 0; idx < index; ++idx) {

System.out.print(shirts[index][idx] + “:”);

}

}

D)

for (int index = 0; index <= 2;) {

for (int idx = 0; idx <= 2;) {

System.out.print(shirts[index][idx] + “:”);

}

index++;

}
```

- A

## explanation :

### 最高維度開始拜訪，[0] [0,1] 這樣拜訪。

- Look into it  shirt[2][2] ;
  
  > [0]  [0,1] 遍歷 先拜訪 最高維度的 0 號開始。
  
  > [1]  [0,1]
  
  ##### 下面提供三維範例
  
  ```java
  //[顏色] [大小] [長短]  去儲存 2*2*2 共八種可能性 。
  String [][][]clothes=new String[2][2][2] ;
  clothes[0][0][0] = "red small short";
  clothes[0][0][1] = "red small long";
  clothes[0][1][0] = "red medium short";
  clothes[0][1][1] = "red medium long";
  clothes[1][0][0] = "blue small short";
  clothes[1][0][1] = "blue small long";
  clothes[1][1][0] = "blue medium short";
  clothes[1][1][1] = "blue medium long";
  ```

# 3. ArrayDeclaration&宣告但不使用。

### Given code

```java
public class Test {
public static void main(String[] args) {
String[][] chs = new String[5][2];
chs[0] = new String[2];
chs[1] = new String[5];
int i = 97;
for (int a = 0; a < chs.length; a++) {
for (int b = 0; b < chs.length; b++) {
chs[a][b] = "" + i;
i++;
}
}
for (String[] ca : chs) {
for (String c : ca) {
System.out.print(c + " ");
}
System.out.println();
}
}
}
```

## What is the result?

```java
A. NullPointerException is thrown at runtime.//呼叫的變數沒有值
B.
97 98
99 100 null null null
C.
97 98
99 100 101 102 103
D.
Compilation fails.
E.
An ArrayIndexOutOfBoundsException is thrown at runtime.
```

- E

## explanation : 宣告但沒使用、給定

- 
