---
title: Funkcje zdefiniowane przez użytkownika (UDF) w usłudze Azure Cosmos DB
description: Dowiedz się więcej o funkcjach zdefiniowanych przez użytkownika w usłudze Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.openlocfilehash: 455f44fb365152b75a3811563b646c6243f686db
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011127"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Funkcje zdefiniowane przez użytkownika (UDF) w usłudze Azure Cosmos DB

Interfejs API SQL zapewnia obsługę funkcji zdefiniowanych przez użytkownika (UDF). Za pomocą skalarnych plików UDF można przekazać w zeru lub wielu argumentach i zwrócić wynik pojedynczego argumentu. Interfejs API sprawdza każdy argument jako legalne wartości JSON.  

## <a name="udf-use-cases"></a>Przypadki użycia UDF

Interfejs API rozszerza składnię SQL do obsługi niestandardowej logiki aplikacji przy użyciu plików UDF. Można zarejestrować pliki UDF za pomocą interfejsu API SQL i odwoływać się do nich w kwerendach SQL. W przeciwieństwie do procedur składowanych i wyzwalaczy, pliki UDF są tylko do odczytu.

Za pomocą UDFs, można rozszerzyć język zapytań usługi Azure Cosmos DB. UDFs to świetny sposób na wyrażenie złożonej logiki biznesowej w projekcji kwerendy.

Zaleca się jednak unikanie plików UDF, gdy:

- Równoważna [funkcja systemu](sql-query-system-functions.md) już istnieje w usłudze Azure Cosmos DB. Funkcje systemowe zawsze będą używać mniejszej liczby ru niż równoważne UDF.
- UDF jest jedynym filtrem `WHERE` w klauzuli kwerendy. UDF nie wykorzystują indeksu, więc ocena UDF będzie wymagać ładowania dokumentów. Połączenie dodatkowych predykatów filtru, które używają indeksu, `WHERE` w połączeniu z UDF, w klauzuli zmniejszy liczbę dokumentów przetwarzanych przez UDF.

Jeśli należy używać tego samego UDF wiele razy w kwerendzie, należy odwołać się do UDF w [podkwerendy,](sql-query-subquery.md#evaluate-once-and-reference-many-times)co pozwala na użycie wyrażenia JOIN do oceny UDF raz, ale odwołać się do niego wiele razy.

## <a name="examples"></a>Przykłady

Poniższy przykład rejestruje UDF w kontenerze towarów w bazie danych usługi Cosmos. W przykładzie utworzy się `REGEX_MATCH`UDF, którego nazwa jest . Akceptuje dwie wartości ciągu JSON `pattern`i , i sprawdza, `input` czy pierwszy pasuje do `string.match()` wzorca określonego w drugim przy użyciu funkcji JavaScript.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Teraz użyj tego UDF w projekcji kwerendy. Należy zakwalifikować UDFs z prefiksem `udf.` z uwzględnieniem wielkości liter podczas wywoływania ich z wewnątrz kwerend.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Wyniki są następujące:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

Można użyć UDF kwalifikowany `udf.` z prefiksem wewnątrz filtru, jak w poniższym przykładzie:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Wyniki są następujące:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

W istocie pliki UDF są prawidłowymi wyrażeniami skalarnymi, których można używać zarówno w rzutach, jak i filtrach.

Aby rozwinąć moc plików UDF, przyjrzyj się innemu przykładowi z logiką warunkową:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

Poniższy przykład wykonuje UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Wyniki są następujące:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Jeśli właściwości, o których mowa w parametrach UDF, nie są dostępne w wartości JSON, parametr jest uważany za niezdefiniowany, a wywołanie UDF jest pomijane. Podobnie jeśli wynik UDF jest niezdefiniowany, nie jest uwzględniony w wyniku.

Jak pokazują powyższe przykłady, pliki UDF integrują moc języka JavaScript z interfejsem API SQL. Pliki UDF zapewniają bogaty programowalny interfejs do wykonywania złożonych proceduralnych, warunkowych logiki za pomocą wbudowanych funkcji środowiska wykonawczego JavaScript. Interfejs API SQL udostępnia argumenty do plików UDF dla każdego elementu źródłowego na bieżącym etapie przetwarzania klauzuli WHERE lub SELECT. Wynik jest bezproblemowo włączone w potoku wykonywania ogólnej. Podsumowując, pliki UDF są doskonałymi narzędziami do wykonywania złożonych logiki biznesowej w ramach zapytań.

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Funkcje systemowe](sql-query-system-functions.md)
- [Agregacje](sql-query-aggregates.md)