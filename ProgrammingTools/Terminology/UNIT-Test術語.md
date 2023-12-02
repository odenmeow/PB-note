# 術語

> [Unit Test 中的替身：搞不清楚的Dummy 、Stub、Spy、Mock、Fake | by 被蛇咬到的魯卡 | Starbugs Weekly 星巴哥技術專欄 | Medium](https://medium.com/starbugs/unit-test-%E4%B8%AD%E7%9A%84%E6%9B%BF%E8%BA%AB-%E6%90%9E%E4%B8%8D%E6%B8%85%E6%A5%9A%E7%9A%84dummy-stub-spy-mock-fake-94be192d5c46) 

> [Fake 與 Stub & Mock - HackMD](https://hackmd.io/@AlienHackMd/HycK5pEpo#%E8%99%9B%E8%A8%AD%E5%B8%B8%E5%BC%8F-Stub) 比較清楚  蠻有用 ! 上面可Pass

# SUT

- System Under Test 待測系統

# Dummy Object

- ( 直譯 ) 冒牌貨、人體模型、擬真貨    [ /ˈdʌm.i/ ] 

- 純粹的填充物 讓程式能運作

# Test Stub

- 原本只香菸抽剩的，或者動詞踢到拇指

- 這邊指 替身物件 可以避免依賴的資料庫找不到資料的情況發生
  
  例如 
  
  ```java
  import static org.junit.Assert.assertEquals;
  import static org.mockito.Mockito.*;
  
  // 待测试的接口
  interface IWeather {
      boolean isSunny();
  }
  
  interface ICost {
      int getCost();
  }
  
  public class CampingTest {
  
      @Test
      public void testGetBookingTentType_NotSunnyDay() {
          // 创建 Mock 对象
          IWeather stubWeather = mock(IWeather.class);
          when(stubWeather.isSunny()).thenReturn(false); // 设置返回值为 false
  
          // 创建 Stub 对象
          ICost stubCost = new ICost() {
              @Override
              public int getCost() {
                  return 1000; // 固定返回值为 1000
              }
          };
  
          // 待测试的对象
          Camping sut = new Camping();
  
          // 调用待测试方法并验证结果
          assertEquals(Camping.Type.INNER_TENT, sut.getBookingTentType(stubCost, stubWeather));
      }
  }
  ```

# Test Spy

- 🔥<font style="color:lightgreen">還沒用過 </font>🔥

- 用來驗證 SUT  與對其他 DOC 物件造成的效果。
  
  [ DOC（Depended-On Component，被依赖组件）]

- 當我們想想要驗證朋友物件的聊天次數是不是如我們所預期的增長時，我們就可以派出間諜朋友，來驗證「 SUT 阿牛是否有跟朋友聊三次天」之類的期待。

- 可以觀察到不只被調用、而是調用幾次、使用參數等。

# Mock Object

- Mock 是一個能夠判斷 SUT 是不是有正確使用 DOC 的替身。Mock 跟 Spy 的最大差別是，Mock 用來驗證 SUT 的行為，而 Spy 用來驗證 SUT 對 DOC 狀態的改變。
  
  `GPT的示例` 

- ```java
  import static org.mockito.Mockito.*;
  
  // 假设有一个名为 SomeClass 的类需要测试，并依赖于 AnotherClass
  public class SomeClassTest {
      @Test
      public void testSomeMethod() {
          // 创建 Mock 对象
          AnotherClass anotherMock = mock(AnotherClass.class);
  
          // 设置 Mock 对象的行为期望
          when(anotherMock.someMethod()).thenReturn("Mocked result");
  
          // 在测试中使用 Mock 对象
          SomeClass someClass = new SomeClass(anotherMock);
          String result = someClass.someMethodUnderTest();
  
          // 验证 SUT 是否正确调用了 Mock 对象的方法
          verify(anotherMock).someMethod();
          // 验证 SUT 返回了预期的结果
          assertEquals("Mocked result", result);
      }
  }
  ```

- 透過 `Mokito` 給予替身 觸發方法後 🔥<font style="color:lightgreen">可如預期般地回傳值 </font> 🔥

# Fake Object

- Fake Object 假物件是一個簡化的 DOC (依賴元件)，例如：一台真實的飛機有很多零件，但是我們其實只需要他有外殼，並且可以飛，可以降落 … 等等的行為。所以做一個簡單版的假物件。假物件不需要考慮跟 SUT 目標對象的間接互動(Indirect input , indirect output)。
