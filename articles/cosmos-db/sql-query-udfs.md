---
title: Funkcje zdefiniowane przez użytkownika (UDF) w usłudze Azure Cosmos DB
description: Zapoznaj się z funkcjami zdefiniowanymi przez użytkownika w usłudze Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: e168e450230720f4ad78516e6edcdc3aa08ba3e1
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342894"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Funkcje zdefiniowane przez użytkownika (UDF) w usłudze Azure Cosmos DB

Interfejs API SQL zapewnia obsługę funkcji zdefiniowanych przez użytkownika (UDF). Funkcje skalarne zdefiniowane przez użytkownika możesz przekazać argumenty, zero lub wiele i zwrócenia wyniku pojedynczy argument. Interfejs API sprawdza każdy argument jest prawną wartości JSON.  

Interfejs API rozszerza składni SQL do obsługi niestandardowej logiki aplikacji za pomocą funkcji zdefiniowanych przez użytkownika. Możesz zarejestrować funkcje zdefiniowane przez użytkownika za pomocą interfejsu API SQL i odwoływać się do nich zapytań SQL. Tak naprawdę funkcje zdefiniowane przez użytkownika są w sposób zaawansowany projektowane na potrzeby wywoływania z zapytań. Jako następstwem funkcje zdefiniowane przez użytkownika nie mają dostępu do obiektu context, podobnie jak inne typy języka JavaScript, takie jak procedur składowanych i wyzwalaczy. Zapytania są przeznaczone tylko do odczytu i można uruchomić na repliki podstawowej lub dodatkowej. Funkcje zdefiniowane przez użytkownika, w przeciwieństwie do innych typów języka JavaScript są zaprojektowane do uruchamiania w replikach pomocniczych.

Poniższy przykład rejestruje funkcji zdefiniowanej przez użytkownika w ramach kontenera elementu w bazie danych Cosmos DB. W przykładzie jest tworzony funkcji zdefiniowanej przez użytkownika o nazwie `REGEX_MATCH`. Akceptuje dwóch wartości ciągu JSON `input` i `pattern`, i sprawdza, czy pierwszego dopasowania wzorca określonego w drugi przy użyciu języka JavaScript `string.match()` funkcji.

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

Teraz można użyć tej funkcji zdefiniowanej przez użytkownika w projekcji zapytań. Kwalifikujesz funkcje zdefiniowane przez użytkownika z uwzględnieniem wielkości liter prefiksem `udf.` podczas wywoływania ich z w ramach zapytania.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Wyniki są:

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

Można użyć funkcji zdefiniowanej przez użytkownika kwalifikowany za pomocą `udf.` prefiks wewnątrz filtru, tak jak w poniższym przykładzie:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Wyniki są:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

W zasadzie funkcje zdefiniowane przez użytkownika są prawidłowe wyrażenia skalarne, które można użyć zarówno projekcje i filtry.

Aby rozszerzyć możliwości funkcje zdefiniowane przez użytkownika, Przyjrzyj się kolejny przykład za pomocą logikę warunkową:

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

Poniższy przykład wykonuje funkcji zdefiniowanej przez użytkownika:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Wyniki są:

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

Jeśli właściwości określone przez UDF parametry nie są dostępne w wartości JSON, parametr jest traktowany jako niezdefiniowane i wywołania funkcji zdefiniowanej przez użytkownika jest pomijany. Podobnie jeśli wynikiem funkcji zdefiniowanej przez użytkownika jest niezdefiniowana, go nie znajduje się w wyniku.

Jak w poprzednich przykładach, funkcje zdefiniowane przez użytkownika integracji możliwości języka JavaScript przy użyciu interfejsu API SQL. Funkcje zdefiniowane przez użytkownika zapewnia bogaty interfejs programowalny celu złożonej logiki przedstawiające diagramy proceduralne i warunkowego za pomocą wbudowanych funkcji środowiska uruchomieniowego JavaScript. Interfejs API SQL udostępnia argumenty do funkcji zdefiniowanych przez użytkownika dla każdego elementu źródłowego w bieżącym gdzie lub klauzuli SELECT etap przetwarzania. Wynik jest włączona bezproblemowo ogólną potoku wykonywania. Podsumowując funkcje zdefiniowane przez użytkownika są doskonałe narzędzia pozwalające czy złożoną logikę biznesową jako część zapytania.

## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Funkcje systemowe](sql-query-system-functions.md)
- [agregacje](sql-query-aggregates.md)