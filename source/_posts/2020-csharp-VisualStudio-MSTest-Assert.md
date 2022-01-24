---
title: C#，又來找你玩了！ - MSTest 的 Assert 提供了哪些 method？
date: 2020-03-06 23:47:56
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

Hi，大家好，我是小馬彬，今天要將 `Microsoft.VisualStudio.TestTools.UnitTesting` 的 Assert 所提供的 method 逐個來玩玩，首先要開啟測試專案，直接建一個 TestClass 來實驗，如果不知道如何建立 MSTest 測試專案，可以到我的上一篇文章看看 [C#，又來找你玩了！ - MSTest 測試專案初體驗](https://littlehorseboy.github.io/2020/03/01/2020-csharp-VisualStudio-MSTest/)，那麼就直接開始逐個了解

## Assert.AreEqual

```csharp=
/**
 * 測試指定的值是否相等
 * 說明上還寫著不同型態的數值會視為不相等
 * 不過倒還是有可以 pass 的情況，是不是搞錯了什麼 XD
 */
[TestMethod()]
public void AreEqual()
{
    Assert.AreEqual(1, 1);
    Assert.AreEqual(true, true);
    Assert.AreEqual("string", "string");
    Assert.AreEqual(1.0, 1.0f); // 此為特殊的相等情況，但如果是用 (1.1, 1.1f) 就會 fail
    Assert.AreEqual(1, 1.0m);
}
```

## Assert.AreNotEqual

```csharp=
/**
 * 測試指定的值是否『不』相等
 */
[TestMethod()]
public void AreNotEqual()
{
    Assert.AreNotEqual(1, 2);
    Assert.AreNotEqual("one", "two");
}
```

## Assert.AreNotSame

```csharp=
/**
 * 測試指定的物件是否參照『不同』物件
 */
[TestMethod()]
public void AreNotSame()
{
    Assert.AreNotSame(new[] { 1, 2 }, new[] { 1, 2 });
}
```

## Assert.AreSame

```csharp=
/**
 * 測試指定的物件是否參照『相同』物件
 */
[TestMethod()]
public void AreSame()
{
    int[] array = new[] { 1, 2 };
    Assert.AreSame(array, array);
}
```

**Assert.Equals** 沒搞懂這個是怎麼回事 XD

## Assert.Fail

```csharp=
/**
 * 擲回 AssertFailedException
 */
[TestMethod()]
public void Fail()
{
    try
    {
        Assert.Fail();
    }
    catch (Exception)
    {
        Assert.AreEqual(1, 1);
    }
}
```

## Assert.Inconclusive

```csharp=
/**
 * 擲回 AssertInconclusiveException
 * 一個沒有定論，介於有斷言成功與沒有斷言成功之間，沒啦，他其實是『跳過』
 */
[TestMethod()]
public void Inconclusive()
{
    Assert.Inconclusive();
}
```

## Assert.IsFalse

```csharp=
/**
 * 測試指定的條件是否為 false
 */
[TestMethod()]
public void IsFalse()
{
    Assert.IsFalse(1 == 2);
    Assert.IsFalse(false);
}
```

## Assert.IsInstanceOfType

```csharp=
/**
 * 測試指定的物件是否為預期類型的執行個體
 */
[TestMethod()]
public void IsInstanceOfType()
{
    Assert.IsInstanceOfType(1, typeof(int));
}
```

## Assert.IsNotInstanceOfType

```csharp=
/**
 * 測試指定的物件是否不是錯誤類型的執行個體
 */
[TestMethod()]
public void IsNotInstanceOfType()
{
    Assert.IsNotInstanceOfType(1, typeof(string));
}
```

## Assert.IsNotNull

```csharp=
/**
 * 測試指定的物件是否為非 null
 */
[TestMethod()]
public void IsNotNull()
{
    Assert.IsNotNull(1 == 1);
    Assert.IsNotNull(false);
}
```

## Assert.IsNull

```csharp=
/**
 * 測試指定的物件是否為 null
 */
[TestMethod()]
public void IsNull()
{
    Assert.IsNull(null);
}
```

## Assert.IsTrue

```csharp=
/**
 * 測試指定的條件是否為 true
 */
[TestMethod()]
public void IsTrue()
{
    Assert.IsTrue(1 == 1);
    Assert.IsTrue(true);
}
```

**Assert.ReferenceEquals** 沒搞懂這個是怎麼回事 XD

**Assert.ReplaceNullChars** 沒搞懂這個是怎麼回事 XD

## Assert.ThrowsException

```csharp=
/**
 * 測試委派 action 所指定的程式碼會擲回 T 類型的確切指定例外狀況
 */
[TestMethod()]
public void ThrowsException()
{
    Assert.ThrowsException<ArgumentNullException>(() => new Regex(null));
}
```

## 總結

將 Assert 給大致上玩過了一遍，除了有幾個完全沒搞懂，爬文是說好像完全不能用 = =，不能用還會放在裡面也蠻奇怪的 XD，其實 MSTest 除了 Assert 的核心 class 可用以外，還有 `StringAssert` 以及 `CollectionAssert`，不過筆者就不通通擠在這篇裡面介紹了，等之後有機會將 class 運用得更嫻熟的時候再來玩玩吧！
