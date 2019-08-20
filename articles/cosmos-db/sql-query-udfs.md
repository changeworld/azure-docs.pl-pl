---
title: Funkcje zdefiniowane przez użytkownika (UDF) w Azure Cosmos DB
description: Dowiedz się więcej o funkcjach zdefiniowanych przez użytkownika w programie Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614336"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Funkcje zdefiniowane przez użytkownika (UDF) w Azure Cosmos DB

Interfejs API SQL zapewnia obsługę funkcji zdefiniowanych przez użytkownika (UDF). Za pomocą skalarnej UDF można przekazać zero lub wiele argumentów i zwrócić wynik pojedynczego argumentu. Interfejs API sprawdza każdy argument pod kątem dozwolonych wartości JSON.  

Interfejs API rozszerza składnię SQL w celu obsługi niestandardowej logiki aplikacji przy użyciu UDF. Możesz zarejestrować UDF z interfejsem API SQL i odwoływać się do nich w zapytaniach SQL. Tak naprawdę funkcje zdefiniowane przez użytkownika są w sposób zaawansowany projektowane na potrzeby wywoływania z zapytań. Jako współrzuty UDF nie mają dostępu do obiektu kontekstu, takiego jak inne typy JavaScript, takie jak procedury składowane i wyzwalacze. Zapytania są tylko do odczytu i mogą być uruchamiane w replikach podstawowych lub pomocniczych. UDF, w przeciwieństwie do innych typów języka JavaScript, są przeznaczone do uruchamiania w replikach pomocniczych.

Poniższy przykład rejestruje UDF w kontenerze elementu w bazie danych Cosmos. W przykładzie tworzony jest `REGEX_MATCH`format UDF o nazwie. Akceptuje dwie wartości `input` ciągu JSON i `pattern`i sprawdza, czy pierwsze pasuje do wzorca określonego w drugim przy użyciu `string.match()` funkcji JavaScript.

## <a name="examples"></a>Przykłady

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

Teraz Użyj tego formatu UDF w projekcji zapytania. Należy zakwalifikować UDF z prefiksem `udf.` z rozróżnianiem wielkości liter podczas wywoływania ich z poziomu zapytań.

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

Można użyć formatu UDF kwalifikowana z `udf.` prefiksem wewnątrz filtra, tak jak w poniższym przykładzie:

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

W zasadzie UDF są prawidłowymi wyrażeniami skalarnymi, których można używać zarówno w projekcjach, jak i w filtrach.

Aby rozwijać możliwości UDF, należy zapoznać się z innym przykładem logiki warunkowej:

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

Poniższy przykład wykonuje funkcję UDF:

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

Jeśli właściwości, do których odwołują się parametry UDF, nie są dostępne w wartości JSON, parametr jest traktowany jako undefined, a wywołanie UDF jest pomijane. Podobnie, jeśli wynik UDF jest niezdefiniowany, nie jest zawarty w wyniku.

Jak przedstawiono w powyższych przykładach, UDF integruje możliwości języka JavaScript z interfejsem API SQL. UDF zapewniają bogaty programowalny interfejs do wykonywania złożonej procedury, logiki warunkowej z pomocą wbudowanych funkcji środowiska uruchomieniowego języka JavaScript. Interfejs API SQL udostępnia argumenty UDF dla każdego elementu źródłowego w bieżącym etapie przetwarzania lub ZAZNACZania klauzuli. Wynik jest bezproblemowo zawarty w ogólnym potoku wykonywania. Podsumowując, UDF to doskonałe narzędzia do wykonywania złożonej logiki biznesowej w ramach zapytań.

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do Azure Cosmos DB](introduction.md)
- [Funkcje systemowe](sql-query-system-functions.md)
- [Agreguje](sql-query-aggregates.md)