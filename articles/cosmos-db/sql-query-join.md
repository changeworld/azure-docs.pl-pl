---
title: Dołącz do programu SQL zapytań dla usługi Azure Cosmos DB
description: Dowiedz się więcej o składni SQL, Dołącz do usługi Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 408ee11b318143b3128833a741e04dd68f3816ed
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342543"
---
# <a name="joins-in-azure-cosmos-db"></a>Sprzężenia w usłudze Azure Cosmos DB

W relacyjnej bazie danych sprzężenia między tabelami są logiczne następstwem do projektowania znormalizowaną schematów. Z kolei interfejsu API SQL korzysta z modelu dane denormalizowane elementów bez schematu, który jest logicznym odpowiednikiem *samosprzężenie*.

Sprzężenia wewnętrzne spowodować pełny iloczyn wektorowy zestawy uczestniczących w sprzężenia. Wynikiem sprzężenia sposób N jest zestaw spójnych kolekcji N-elementowej, gdzie każda wartość w spójnej kolekcji jest skojarzony z aliasem, ustaw udział w sprzężeniu i jest dostępny, odwołując się do tego aliasu w innych klauzul.

## <a name="syntax"></a>Składnia

Język obsługuje składnię `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Ta kwerenda zwraca zestaw spójnych kolekcji zawierający `N` wartości. Każda krotka ma wartości utworzone w procesie iterowania wszystkich aliasów kontenera w odpowiednich zestawach. 

Spójrzmy na następujący klauzuli FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Pozwól każdego źródła, zdefiniuj `input_alias1, input_alias2, …, input_aliasN`. Ta klauzula FROM zwraca zestaw (krotki wartości N) N-krotek. Każda krotka ma wartości utworzone w procesie iterowania wszystkich aliasów kontenera w odpowiednich zestawach.  
  
**Przykład 1** -2 źródeł  
  
- Pozwól `<from_source1>` kontenerów należące i reprezentują zestawu {A, B, C}.  
  
- Pozwól `<from_source2>` się dokument o zakresie odwołujące się do input_alias1 i reprezentują zestawów:  
  
    {1, 2} dla `input_alias1 = A,`  
  
    {3} Aby uzyskać `input_alias1 = B,`  
  
    {4, 5} dla `input_alias1 = C,`  
  
- Klauzula FROM `<from_source1> JOIN <from_source2>` spowoduje następujące kolekcje:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Przykład 2** -3 źródła  
  
- Pozwól `<from_source1>` kontenerów należące i reprezentują zestawu {A, B, C}.  
  
- Pozwól `<from_source2>` być zakresem dokument odwołuje się do `input_alias1` i reprezentują zestawów:  
  
    {1, 2} dla `input_alias1 = A,`  
  
    {3} Aby uzyskać `input_alias1 = B,`  
  
    {4, 5} dla `input_alias1 = C,`  
  
- Pozwól `<from_source3>` być zakresem dokument odwołuje się do `input_alias2` i reprezentują zestawów:  
  
    {100, 200} dla `input_alias2 = 1,`  
  
    {300} Aby uzyskać `input_alias2 = 3,`  
  
- Klauzula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` spowoduje następujące kolekcje:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Brak spójnych kolekcji dla innych wartości parametru `input_alias1`, `input_alias2`, dla którego `<from_source3>` nie zwrócił żadnych wartości.  
  
**Przykład 3** -3 źródła  
  
- Pozwól < from_source1 > być należące do kontenera i reprezentują zestawu {A, B, C}.  
  
- Pozwól `<from_source1>` kontenerów należące i reprezentują zestawu {A, B, C}.  
  
- Pozwól < from_source2 > być zakresu w dokumencie input_alias1 odwołujący się i reprezentują zestawów:  
  
    {1, 2} dla `input_alias1 = A,`  
  
    {3} Aby uzyskać `input_alias1 = B,`  
  
    {4, 5} dla `input_alias1 = C,`  
  
- Pozwól `<from_source3>` należeć do zakresu `input_alias1` i reprezentują zestawów:  
  
    {100, 200} dla `input_alias2 = A,`  
  
    {300} Aby uzyskać `input_alias2 = C,`  
  
- Klauzula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` spowoduje następujące kolekcje:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), C, 4, 300, (C, 5, 300)  
  
  > [!NOTE]
  > Pozwoliło to odnotować iloczyn między `<from_source2>` i `<from_source3>` ponieważ zarówno dostosowanych do tej samej `<from_source1>`.  Pozwoliło to odnotować 4 (2 x 2) krotki o wartości A, 0 krotek mających wartość B (1 x 0) i 2 (2 x 1) krotek mających wartość C.  
  
## <a name="examples"></a>Przykłady

W poniższych przykładach pokazano, jak działa klauzula JOIN. W poniższym przykładzie wynik jest pusta, ponieważ iloczyn wektorowy każdy element ze źródła i pusty zestaw jest pusty:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Wynik jest:

```json
    [{
    }]
```

W poniższym przykładzie sprzężenia jest produktem między pomiędzy dwoma obiektami JSON, głównego elementu `id` i `children` subroot. Fakt, `children` jest tablica nie jest skuteczna sprzężenia, ponieważ dotyczy on jednym elementem głównym, który jest `children` tablicy. Wynik zawiera tylko dwa wyniki, ponieważ dokładnie jedną daje w wyniku iloczyn wektorowy każdego elementu z tablicy.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Wyniki są:

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

Wyniki są:

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

Źródło FROM klauzuli JOIN jest iteratorem. Dlatego jest przepływu w poprzednim przykładzie:  

1. Rozwiń każdy element podrzędny `c` w tablicy.
2. Zastosuj iloczyn z certyfikatem głównym elementu `f` z każdego elementu podrzędnego `c` , pierwszym krokiem spłaszczone.
3. Na koniec projektu głównego obiektu `f` `id` właściwość samodzielnie.

Pierwszy element `AndersenFamily`, zawiera tylko jeden `children` elementu, więc zestaw wyników zawiera pojedynczy obiekt. Drugi element `WakefieldFamily`, zawiera dwa `children`, więc iloczyn tworzy dwa obiekty, po jednym dla każdego `children` elementu. Główne pola w obu elementach są takie same, tak jak można oczekiwać w iloczynie wektorowym.

Rzeczywiste narzędzie klauzuli JOIN jest krotek formularza z iloczyn kształtu, który jest trudny do projektu. W poniższym przykładzie filtrów na kombinację spójną kolekcją, która pozwala użytkownikowi na wybranie warunek jest spełniony przez kolekcje ogólne.

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

Wyniki są:

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

Następujące rozszerzenie poprzedni przykład wykonuje sprzężenie double. Iloczyn można wyświetlić jako pseudo-następujący kod:

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

`AndersenFamily` ma jeden element podrzędny, który ma jedno zwierzę, więc iloczyn daje jeden wiersz (1\*1\*1) z tej rodziny. `WakefieldFamily` ma dwa elementy podrzędne, tylko jeden z nich ma zwierząt domowych, ale ten podrzędny ma dwa zwierzęta. Iloczyn dla tej rodziny daje 1\*1\*2 = 2 wiersze.

W następnym przykładzie istnieje dodatkowy filtr na `pet`, który nie obejmuje wszystkich krotek, gdzie imię zwierzęcia jest `Shadow`. Możesz tworzyć kolekcje z tablic, filtr na jeden z elementów krotki i dowolną kombinację elementów projektu.

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

Wyniki są:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Zapytania podrzędne](sql-query-subquery.md)