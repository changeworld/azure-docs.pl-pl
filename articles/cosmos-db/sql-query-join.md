---
title: Kwerendy SQL JOIN dla usługi Azure Cosmos DB
description: Dowiedz się, jak dołączyć wiele tabel w usłudze Azure Cosmos DB do wykonywania zapytań o dane
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 38e80f1597a08b8db7cbfa852d1bcf38ac768b1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871146"
---
# <a name="joins-in-azure-cosmos-db"></a>Dołączanie do usługi Azure Cosmos DB

W relacyjnej bazie danych sprzężenia między tabelami są logicznym następstwem projektowania znormalizowanych schematów. Natomiast sql API używa zdenormalizowanego modelu danych elementów wolnych od schematu, który jest logicznym odpowiednikiem *samosprzężenia*.

Sprzężenia wewnętrzne powodują kompletny produkt krzyżowy zestawów uczestniczących w sprzężeniu. Wynikiem sprzężenia N-way jest zestaw krotek N-element, gdzie każda wartość w krocie jest skojarzony z aliasem zestawu uczestniczącego w sprzężeniu i można uzyskać do niego dostęp, odwołując się do tego aliasu w innych klauzulach.

## <a name="syntax"></a>Składnia

Język obsługuje składnię `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Ta kwerenda zwraca zestaw krotek z `N` wartościami. Każda krotka ma wartości utworzone w procesie iterowania wszystkich aliasów kontenera w odpowiednich zestawach. 

Przyjrzyjmy się następującej klauzuli FROM:`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Niech każde `input_alias1, input_alias2, …, input_aliasN`źródło zdefiniuje . Ta klauzula FROM zwraca zestaw N-krotek (krotka z wartościami N). Każda krotka ma wartości utworzone w procesie iterowania wszystkich aliasów kontenera w odpowiednich zestawach.  
  
**Przykład 1** - 2 źródła  
  
- Pozwól, `<from_source1>` aby był on objęty zakresem kontenera i reprezentuj zestaw {A, B, C}.  
  
- Niech `<from_source2>` będzie dokument o zakresie odwołując się input_alias1 i reprezentują zestawy:  
  
    {1, 2} dla`input_alias1 = A,`  
  
    {3}For`input_alias1 = B,`  
  
    {4, 5} dla`input_alias1 = C,`  
  
- Klauzula `<from_source1> JOIN <from_source2>` FROM spowoduje następujące krotki:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Przykład 2** - 3 źródła  
  
- Pozwól, `<from_source1>` aby był on objęty zakresem kontenera i reprezentuj zestaw {A, B, C}.  
  
- Niech `<from_source2>` będzie odwołujące `input_alias1` się do dokumentu i reprezentują zestawy:  
  
    {1, 2} dla`input_alias1 = A,`  
  
    {3}For`input_alias1 = B,`  
  
    {4, 5} dla`input_alias1 = C,`  
  
- Niech `<from_source3>` będzie odwołujące `input_alias2` się do dokumentu i reprezentują zestawy:  
  
    {100, 200} dla`input_alias2 = 1,`  
  
    {300}For`input_alias2 = 3,`  
  
- Klauzula `<from_source1> JOIN <from_source2> JOIN <from_source3>` FROM spowoduje następujące krotki:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Brak krotek dla innych `input_alias1`wartości `input_alias2`, dla `<from_source3>` których nie zwrócił żadnych wartości.  
  
**Przykład 3** - 3 źródła  
  
- Pozwól <from_source1> być o zakresie kontenera i reprezentować zestaw {A, B, C}.  
  
- Pozwól, `<from_source1>` aby był on objęty zakresem kontenera i reprezentuj zestaw {A, B, C}.  
  
- Pozwól <from_source2> być input_alias1 o zakresie dokumentu i reprezentować zestawy:  
  
    {1, 2} dla`input_alias1 = A,`  
  
    {3}For`input_alias1 = B,`  
  
    {4, 5} dla`input_alias1 = C,`  
  
- Niech `<from_source3>` być `input_alias1` zakres i reprezentują zestawy:  
  
    {100, 200} dla`input_alias2 = A,`  
  
    {300}For`input_alias2 = C,`  
  
- Klauzula `<from_source1> JOIN <from_source2> JOIN <from_source3>` FROM spowoduje następujące krotki:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
  > [!NOTE]
  > Spowodowało to, że `<from_source2>` `<from_source3>` produkt krzyżowy między i `<from_source1>`ponieważ oba są objęte zakresem do tego samego .  Spowodowało to 4 (2x2) krotek o wartości A, 0 krotek o wartości B (1x0) i 2 (2x1) krotek o wartości C.  
  
## <a name="examples"></a>Przykłady

W poniższych przykładach pokazano, jak działa klauzula JOIN. Przed uruchomieniem tych przykładów należy przesłać przykładowe [dane rodzinne](sql-query-getting-started.md#upload-sample-data). W poniższym przykładzie wynik jest pusty, ponieważ produkt krzyżowy każdego towaru ze źródła i pusty zestaw jest pusty:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Rezultatem jest:

```json
    [{
    }]
```

W poniższym przykładzie sprzężenia jest produktem krzyżowym `id` między `children` dwoma obiektami JSON, katalogiem głównym towaru i podrozkiem. Fakt, `children` że jest tablica nie jest skuteczne w sprzężeniu, `children` ponieważ zajmuje się jednym katalogiem głównym, który jest tablicą. Wynik zawiera tylko dwa wyniki, ponieważ produkt krzyżowy każdego elementu z tablicą daje dokładnie tylko jeden element.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Wyniki są następujące:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

Poniższy przykład przedstawia bardziej konwencjonalne sprzężenie:

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Wyniki są następujące:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

Źródło from klauzuli JOIN jest iteratorem. Tak więc przepływ w poprzednim przykładzie jest:  

1. Rozwiń każdy `c` element podrzędny w tablicy.
2. Zastosuj produkt krzyżowy z katalogiem głównym `f` `c` elementu z każdym elementem podrzędnym spłaszczonym pierwszym krokiem.
3. Na koniec project właściwości `f` `id` obiektu głównego samodzielnie.

Pierwszy element `AndersenFamily`, zawiera `children` tylko jeden element, więc zestaw wyników zawiera tylko jeden obiekt. Drugi element, `WakefieldFamily`, `children`zawiera dwa , więc produkt krzyżowy `children` tworzy dwa obiekty, po jednym dla każdego elementu. Główne pola w obu elementach są takie same, tak jak można oczekiwać w iloczynie wektorowym.

Prawdziwe użyteczność JOIN klauzuli jest do tworzenia krotek z produktu krzyżowego w kształcie, który w przeciwnym razie trudne do projektu. Poniższy przykład filtruje kombinację krotki, która pozwala użytkownikowi wybrać warunek spełniony przez krotek ogólnej.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

Wyniki są następujące:

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

Następujące rozszerzenie poprzedniego przykładu wykonuje double join. Produkt krzyżowy można wyświetlić jako następujący pseudokod:

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily`ma jedno dziecko, które ma jedno zwierzę, więc produkt\*krzyżowy daje jeden rząd (1 1\*1) z tej rodziny. `WakefieldFamily`ma dwoje dzieci, z których tylko jedno ma zwierzęta domowe, ale to dziecko ma dwa zwierzęta domowe. Produkt krzyżowy dla tej rodziny\*daje\*1 1 2 = 2 rzędy.

W następnym przykładzie znajduje się `pet`dodatkowy filtr na , który wyklucza wszystkie krotki, gdzie nazwa zwierzęcia nie `Shadow`jest . Można tworzyć krotek z tablic, filtrować na dowolnych elementów krotki i projekt dowolnej kombinacji elementów.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

Wyniki są następujące:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie](sql-query-getting-started.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Podkwerend](sql-query-subquery.md)