---
title: Funkcje zdefiniowane przez użytkownika (UDF) w usłudze Azure Cosmos DB
description: Dowiedz się więcej o funkcjach zdefiniowanych przez użytkownika w usłudze Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69614336"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Funkcje zdefiniowane przez użytkownika (UDF) w usłudze Azure Cosmos DB

Interfejs API SQL zapewnia obsługę funkcji zdefiniowanych przez użytkownika (UDF). Za pomocą skalarnych plików UDF można przekazać w zeru lub wielu argumentach i zwrócić wynik pojedynczego argumentu. Interfejs API sprawdza każdy argument jako legalne wartości JSON.  

Interfejs API rozszerza składnię SQL do obsługi niestandardowej logiki aplikacji przy użyciu plików UDF. Można zarejestrować pliki UDF za pomocą interfejsu API SQL i odwoływać się do nich w kwerendach SQL. Tak naprawdę funkcje zdefiniowane przez użytkownika są w sposób zaawansowany projektowane na potrzeby wywoływania z zapytań. W konsekwencji pliki UDF nie mają dostępu do obiektu kontekstowego, podobnie jak inne typy JavaScript, takie jak procedury składowane i wyzwalacze. Kwerendy są tylko do odczytu i można uruchomić na replikach podstawowych lub pomocniczych. Pliki UDF, w przeciwieństwie do innych typów JavaScript, są przeznaczone do uruchamiania w replikach pomocniczych.

Poniższy przykład rejestruje UDF w kontenerze towarów w bazie danych usługi Cosmos. W przykładzie utworzy się `REGEX_MATCH`UDF, którego nazwa jest . Akceptuje dwie wartości ciągu JSON `pattern`i , i sprawdza, `input` czy pierwszy pasuje do `string.match()` wzorca określonego w drugim przy użyciu funkcji JavaScript.

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