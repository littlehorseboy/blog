---
title: C#，又來找你玩了！ - MSTest 測試專案初體驗
date: 2020-03-01 14:04:46
categories:
- w3HexSchool
- C#
tags:
- w3HexSchool
- C#
- Visual Studio
- Unit Test
cover: https://upload.wikimedia.org/wikipedia/commons/thumb/0/0d/C_Sharp_wordmark.svg/225px-C_Sharp_wordmark.svg.png
---

Hi，大家好，我是小馬彬，單元測試能夠使你對你寫的程式碼更有信心，像遇到需求變更，或是維護解 bug 時，已經撰寫好的單元測試就能夠在你更動程式碼時，幫助了解有沒有改壞東西，不過撰寫『好的』或是『完備的』單元測試，絕對是需要一番勤學苦練的。

其實筆者目前算是 C# 的生手，好幾年前的菜鳥時期碰過，但只知其一，不知其二，OOP 及設計模式都還沒熟識，所以這篇文會紀錄筆者用一個非常基礎的 class，然後創建一個 MSTest 測試專案來測試該 class，以及一些 IDE 使用方式，對 class 玩轉自如的重構這邊應該短期內看不到 XD，後幾篇期望能將 `Microsoft.VisualStudio.TestTools.UnitTesting` 做更深入的研究

## 新建主控台應用程式

選擇主控台應用程式後按下一步

![](https://i.imgur.com/o1owz4i.png)

輸入專案名稱選擇好存放位置後按建立

![](https://i.imgur.com/b8n9cdd.png)

得到 Hello World!

![](https://i.imgur.com/dcybXVX.png)

建立 Stack.cs

![](https://i.imgur.com/CrWI8Kj.png)

程式碼取自官方文檔中的 [C# 6.0 草稿規格](https://docs.microsoft.com/zh-tw/dotnet/csharp/language-reference/language-specification/introduction#program-structure) 內容

```csharp=
/* Stack.cs */
using System;

namespace ConsoleAppClassTests_practice
{
    public class Stack
    {
        Entry top;

        public void Push(object data)
        {
            top = new Entry(top, data);
        }

        public object Pop()
        {
            if (top == null) throw new InvalidOperationException();
            object result = top.data;
            top = top.next;
            return result;
        }

        class Entry
        {
            public Entry next;
            public object data;

            public Entry(Entry next, object data)
            {
                this.next = next;
                this.data = data;
            }
        }
    }
}

```

主程式 Program.cs 操作這個堆疊 class 來輸出看看

```csharp=
/* Program.cs */
using System;

namespace ConsoleAppClassTests
{
    class Program
    {
        static void Main(string[] args)
        {
            Stack s = new Stack();

            s.Push("one");
            s.Push("two");
            s.Push("three");

            Console.WriteLine("three", s.Pop());
            Console.WriteLine("two", s.Pop());
            Console.WriteLine("one", s.Pop());
        }
    }
}
```

執行並觀察輸出，Push one two three，Pop three two one，如果在堆疊內還沒 Push 進任何內容，就先寫 Pop 的話會被拋出例外

![](https://i.imgur.com/nmwOKLN.png)

## 建立測試專案

接下來就是要寫測試的程式碼了，建立測試專案的起手式，有兩種

### 第一種，手動建立測試專案

對解決方案點右鍵來新增專案

![](https://i.imgur.com/8b49ShA.png)

搜尋 MSTest，選擇 MSTest 測試專案，並輸入專案名稱後按下建立

![](https://i.imgur.com/eXXYYE1.png)

建立後會在右側方案總管對測試專案的相依性點右鍵，將要測試專案加入參考，因為這兩個專案的 RootNamespace 是不一樣的，沒有加入參考會存取不到

![](https://i.imgur.com/qS7zAnY.gif)

### 第二種，用 IDE 對要測試的目標 class 點右鍵

先將剛剛建立的專案刪除，要記得的就是，從 IDE 上刪除專案，並不會刪到真正的檔案，而是刪除該專案與這個解決方案 .sln 的相依性，還需要額外到資料夾進行刪除，至少在筆者的 Windows 10 Visual Studio 2019 是這樣，不同作業系統或版本可能會有差異

只剩原本的主控台應用程式專案

![](https://i.imgur.com/iQn6Yxy.png)

選擇 Stack.cs，直接 class 名稱點右鍵，選單選擇建立單元測試

![](https://i.imgur.com/nzGMJWu.gif)

這個功能就能直接幫你蓋一個 MSTest 的專案及測試檔，名稱預設會依照你現在指定要測試的專案檔案來產生，還有相依性都幫你準備好，懶人（如我）必用

![](https://i.imgur.com/aAIMFuC.png)

按下確定後就會幫你建好新專案，以及對應原本 class 的 TestMethod

![](https://i.imgur.com/57XbwwW.png)


## 來直接進行測試吧！

選擇最上排選單的『測試』，然後執行所有測試，這時會開啟 Test Explorer 的小視窗

![](https://i.imgur.com/LmnsqQl.png)

![](https://i.imgur.com/nYPl7f5.png)

測試失敗了！當然，剛剛建立完測試檔案後什麼都沒有改動，TestMethod 中直接寫著讓測試 fail 的 `Assert.Fail()`

## 想想我要測試什麼樣的情境

現在的 class 是一個 Stack 堆疊的功能，那麼...

1. 首先當然是測堆疊 Push 後有資料時進行 Pop 能否正常後進先出的取得資料

```csharp=
[TestMethod()]
public void StackPushPopIntTest()
{
    // arrange
    Stack s = new Stack();

    // act
    s.Push(1);
    s.Push(10);
    s.Push("100");

    // assert
    Assert.AreEqual("100", s.Pop());
    Assert.AreEqual(10, s.Pop());
    Assert.AreEqual(1, s.Pop());
}
```

2. 測堆疊在沒有任何資料時進行 Pop() 會拋出例外

```csharp=
[ExpectedException(typeof(InvalidOperationException))]
[TestMethod()]
public void StackPopInvalidOperationException()
{
    // arrange
    Stack s = new Stack();

    // act
    s.Pop();
}
```

測試拋出例外要用 decorator 的 [ExpectedException()]

## 執行測試

用剛剛開啟的 Test Explorer，直接點他左上角的執行所有測試（兩個三角形的按紐）

![](https://i.imgur.com/ybRNXLP.png)

就能看到測試完全成功了，都是綠色勾勾，代表堆疊功能正常，堆疊沒資料時進行 Pop 會拋出例外

## 總結

以上就是依靠 Visual Studio 提供的功能來建立一個最基礎的測試專案，只有一個 class 及測試的 class，還不知道複雜的專案會怎麼去玩轉這些 XD，不過就還是一樣繼續刻意練習吧，C# 的基礎應該要先盡力提升起來
