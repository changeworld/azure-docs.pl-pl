---
title: Tłumaczenie LINQ na SQL w usłudze Azure Cosmos DB
description: Dowiedz się, operatory LINQ obsługiwane i jak zapytania LINQ są mapowane do zapytań SQL w usłudze Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d43f95b91df7d0c9c442339de51936200f4688e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441251"
---
# <a name="linq-to-sql-translation"></a>Tłumaczenie składni LINQ na język SQL

Dostawca zapytań usługi Azure Cosmos DB wykonuje najlepsze mapowanie wysiłku z zapytania LINQ do kwerendy SQL usługi Cosmos DB. Poniższy opis zakłada podstawową znajomość LINQ.

System typu dostawcy kwerend obsługuje tylko typy pierwotne JSON: numeryczne, logiczne, ciąg i null.

Dostawca kwerend obsługuje następujące wyrażenia skalarne:

- Wartości stałe, w tym wartości stałe typów danych pierwotnych w czasie oceny kwerendy.
  
- Wyrażenia indeksu właściwości/tablicy, które odwołują się do właściwości obiektu lub elementu tablicy. Przykład:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Wyrażenia arytmetyczne, w tym typowe wyrażenia arytmetyczne na wartościach liczbowych i logicznych. Aby uzyskać pełną listę, zobacz [specyfikację SQL usługi Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Wyrażenia porównania ciągów, które obejmują porównywanie wartości ciągu z jakąś stałą wartością ciągu.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Wyrażenia tworzenia obiektu/tablicy, które zwracają obiekt o typie wartości złożonej lub typie anonimowym lub tablicę takich obiektów. Można zagnieżdżać te wartości.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>Obsługiwane operatory LINQ

Dostawca LINQ dołączony do narzędzia SQL .NET SDK obsługuje następujące operatory:

- **Wybierz**: Projekcje przekładają się na SQL SELECT, w tym na konstrukcję obiektów.
- **Gdzie**: Filtry przekładają się `&&`na `||`SQL `!` WHERE i obsługują tłumaczenie między operatorami SQL
- **SelectMany**: umożliwia rozwijanie tablic do klauzuli JOIN języka SQL. Służy do kreślenia lub zagnieżdżania wyrażeń do filtrowania elementów tablicy.
- **OrderBy** i **OrderByDescending**: Przetłumacz na zamówienie według asc lub DESC.
- Operatory **Count**, **Sum**, **Min**, **Max** i **Average** na potrzeby agregacji oraz ich asynchroniczne odpowiedniki **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** i **AverageAsync**.
- **CompareTo**: operator tłumaczony na porównania zakresu. Powszechnie używane dla ciągów, ponieważ nie są one porównywalne w .NET.
- **Pomiń** i **Weź**: Tłumaczy na PRZESUNIĘCIE SQL i LIMIT w celu ograniczenia wyników z kwerendy i wykonywania podziałów na strony.
- **Funkcje matematyczne**: `Abs`Obsługuje `Acos` `Asin`tłumaczenie `Atan` `Ceiling`z `Cos`.NET , `Pow` `Round`, `Sign` `Sin`, `Sqrt` `Tan`, `Truncate` `Exp`, , `Floor`, `Log`, `Log10`, , , , , , i do równoważnych wbudowanych funkcji SQL.
- **Funkcje ciągu**: Obsługuje `Concat` `Contains`tłumaczenie `Count` `EndsWith`z`IndexOf` `Replace`.NET , `ToLower` `ToUpper`, `TrimEnd`, `TrimStart` , , , `Reverse`, `StartsWith`, `SubString`, , , , i równoważnych funkcji wbudowanych SQL.
- **Funkcje tablicy:** Obsługuje `Concat` `Contains`tłumaczenie `Count` z platformy .NET i równoważne wbudowane funkcje SQL.
- **Funkcje rozszerzenia geoprzestrzennego**: `Distance` `IsValid`Obsługuje `IsValidDetailed`tłumaczenie `Within` z metod skrótowych , , i do równoważnych funkcji wbudowanych SQL.
- **Funkcja rozszerzenia zdefiniowana przez użytkownika**: `UserDefinedFunctionProvider.Invoke` Obsługuje translację z metody skrótowej do odpowiedniej funkcji zdefiniowanej przez użytkownika.
- **Różne**: Obsługuje tłumaczenia `Coalesce` i operatorów warunkowych. Można `Contains` przetłumaczyć na ciąg zawiera, ARRAY_CONTAINS lub SQL IN, w zależności od kontekstu.

## <a name="examples"></a>Przykłady

Poniższe przykłady ilustrują, jak niektóre standardowe operatory zapytań LINQ tłumaczą zapytania usługi Cosmos DB.

### <a name="select-operator"></a>Wybierz operatora

Składnia to `input.Select(x => f(x))`, gdzie `f` jest wyrażeniem skalarnym.

**Wybierz operatora, przykład 1:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Wybierz operatora, przykład 2:** 

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Wybierz operatora, przykład 3:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>Operator SelectMany

Składnia to `input.SelectMany(x => f(x))`, gdzie `f` jest wyrażeniem skalarnym, które zwraca typ kontenera.

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Operator Where

Składnia to `input.Where(x => f(x))`, gdzie `f` jest wyrażenie skalarnym, które zwraca wartość logiczną.

**Jeżeli operator, przykład 1:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Jeżeli operator, przykład 2:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>Złożone zapytania języka SQL

Można skomponować poprzednich operatorów do tworzenia bardziej zaawansowanych zapytań. Ponieważ usługa Cosmos DB obsługuje kontenery zagnieżdżone, można łączyć lub zagnieżdżać kompozycję.

### <a name="concatenation"></a>Łączenie

Składnia to `input(.|.SelectMany())(.Select()|.Where())*`. Kwerenda łączona może zaczynać się od kwerendy opcjonalnej, `SelectMany` po której następuje wiele `Select` operatorów. `Where`

**Konkadyjacja, przykład 1:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Konkadyjacja, przykład 2:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Konkadyjacja, przykład 3:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Konkadyjacja, przykład 4:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>Zagnieżdżanie

Składnia `input.SelectMany(x=>x.Q())` jest `Q` `Select`tam, gdzie `SelectMany`jest `Where` , lub operator.

Kwerenda zagnieżdżona stosuje kwerendę wewnętrzną do każdego elementu kontenera zewnętrznego. Jedną z ważnych funkcji jest to, że zapytanie wewnętrzne może odwoływać się do pól elementów w kontenerze zewnętrznym, takich jak samosiew.

**Zagnieżdżanie, przykład 1:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Zagnieżdżanie, przykład 2:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Zagnieżdżanie, przykład 3:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```


## <a name="next-steps"></a>Następne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelowanie danych dokumentów](modeling-data.md)
