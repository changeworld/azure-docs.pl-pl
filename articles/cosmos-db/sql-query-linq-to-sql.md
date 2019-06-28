---
title: LINQ do SQL tłumaczenia w usłudze Azure Cosmos DB
description: Mapowanie zapytań LINQ do zapytań SQL usługi Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 057614da8fd29e1208c2788049c5d6d1a985eed5
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342699"
---
# <a name="linq-to-sql-translation"></a>Tłumaczenie składni LINQ na język SQL

Dostawca usługi Azure Cosmos DB zapytania wykonuje najlepsze mapowania nakład pracy w wyniku zapytania LINQ do zapytań SQL usługi Cosmos DB. Poniższy opis założono podstawowe znajomość LINQ.

System typu dostawcy zapytań obsługuje tylko typy pierwotne JSON: liczbowe, atrybut typu wartość logiczna, ciąg, a wartość null.

Dostawcy zapytań obsługuje następujące wyrażenia skalarne:

- Stałe wartości, w tym stałe wartości pierwotnych typów danych w czasie oceny kwerendy.
  
- Wyrażenia indeksu tablicy/właściwości, które odwołują się do właściwości elementu tablicy lub obiektu. Na przykład:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Wyrażenia arytmetyczne, w tym wspólnych wyrażeniach arytmetycznych na wartościach liczbowych i logicznych. Aby uzyskać pełną listę, zobacz [specyfikacji SQL usługi Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Wyrażenia porównania ciągu, obejmujących porównanie wartości ciągu na wartość stałą ciągu.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Tablica/obiektów tworzenia wyrażeń, które zwracają obiekt typu złożonego wartości lub typu anonimowego lub tablicę obiektów. Można zagnieżdżać te wartości.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a id="SupportedLinqOperators"></a>Obsługiwane operatory zapytań LINQ

Dostawca LINQ, dołączone do zestawu .NET SDK SQL obsługuje następujące operatory:

- **Select**: Aby wybrać SQL, w tym konstrukcji obiektu wykonuje translację elementu projekcji.
- **Where**: Filtry przełożyć na WHERE języka SQL i obsługują tłumaczenie między `&&`, `||`, i `!` operatorów SQL
- **SelectMany**: umożliwia rozwijanie tablic do klauzuli JOIN języka SQL. Użyj, aby utworzyć łańcuch lub zagnieżdżanie wyrażeń do filtrowania elementów tablicy.
- **OrderBy** i **OrderByDescending**: Wykonuje translację elementu do zamówienia, za pomocą usługi ASC lub DESC.
- Operatory **Count**, **Sum**, **Min**, **Max** i **Average** na potrzeby agregacji oraz ich asynchroniczne odpowiedniki **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** i **AverageAsync**.
- **CompareTo**: operator tłumaczony na porównania zakresu. Często używane dla ciągów, ponieważ nie jest porównywalny na platformie .NET.
- **Take**: Przekłada się na GÓRZE SQL do ograniczania wyników z zapytania.
- **Funkcje matematyczne**: Tłumaczenie z .NET obsługuje `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan`, i `Truncate` na równoważne funkcje wbudowane SQL.
- **Ciąg funkcje**: Tłumaczenie z .NET obsługuje `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`, i `TrimStart` na równoważne funkcje wbudowane SQL.
- **Funkcje tablicy**: Tłumaczenie z .NET obsługuje `Concat`, `Contains`, i `Count` na równoważne funkcje wbudowane SQL.
- **Funkcje Geoprzestrzenne rozszerzeń**: Obsługuje tłumaczenie z metodami klasy zastępczej `Distance`, `IsValid`, `IsValidDetailed`, i `Within` na równoważne funkcje wbudowane SQL.
- **Funkcja zdefiniowana przez użytkownika funkcji rozszerzenia**: Obsługuje tłumaczenia z metody klasy zastępczej `UserDefinedFunctionProvider.Invoke` do odpowiednich funkcji zdefiniowanej przez użytkownika.
- **Różne**: Obsługuje tłumaczenie `Coalesce` i operatorów warunkowych. Może dokonywać translacji `Contains` zawiera ciąg, ARRAY_CONTAINS lub IN SQL, w zależności od kontekstu.

## <a name="examples"></a>Przykłady

Poniższe przykłady ilustrują, jak tłumaczenie niektóre standardowe operatory zapytań LINQ do zapytań usługi Cosmos DB.

### <a name="select-operator"></a>Wybierz operator

Składnia to `input.Select(x => f(x))`, gdzie `f` jest wyrażeniem skalarnym.

**Wybierz operator, przykład 1:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Wybierz operator, przykład 2:** 

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Wybierz operator, przykład 3:**

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

**Gdy operator, na przykład 1:**

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
  
**Gdy operator, na przykład 2:**

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

Można utworzyć, poprzedni operatorów w celu utworzenia bardziej zaawansowane kwerendy. Ponieważ usługi Cosmos DB obsługuje zagnieżdżone kontenery, można połączyć lub zagnieździć kompozycji.

### <a name="concatenation"></a>Łączenie

Składnia to `input(.|.SelectMany())(.Select()|.Where())*`. Zapytania połączonych można uruchomić z opcjonalnymi `SelectMany` zapytania, a następnie za pomocą wielu `Select` lub `Where` operatorów.

**Łączenie, przykład 1:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Łączenie, przykład 2:**

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

**Łączenie, przykład 3:**

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

**Łączenie, przykład 4:**

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

Składnia jest `input.SelectMany(x=>x.Q())` gdzie `Q` jest `Select`, `SelectMany`, lub `Where` operatora.

Zapytanie zagnieżdżone dotyczy zapytanie wewnętrzne każdy element obiektu kontenera. Jedną funkcję ważne jest, czy zapytanie wewnętrzne mogą odwoływać się do pól elementów w kontenerze zewnętrzne, takie jak samosprzężenie.

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


## <a name="next-steps"></a>Kolejne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Modelowanie danych dokumentów](modeling-data.md)
