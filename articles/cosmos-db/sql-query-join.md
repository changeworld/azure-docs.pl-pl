---
title: Zapytania SPRZĘŻENIa SQL dla Azure Cosmos DB
description: Dowiedz się więcej na temat dołączania do składni SQL Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: d78904fde53da0e800a69d2148a9c4e3acf57307
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494404"
---
# <a name="joins-in-azure-cosmos-db"></a>Sprzężenia w Azure Cosmos DB

W relacyjnej bazie danych sprzężenia między tabelami jest logicznym współrzutem do projektowania znormalizowanych schematów. W przeciwieństwie do interfejsu API SQL jest używany nieznormalizowany model danych elementów bez schematu, który jest logicznym odpowiednikiem *samosprzężenia*.

Sprzężenia wewnętrzne powodują pełny iloczyn skrzyżowania zestawów uczestniczących w sprzężeniu. Wynik sprzężenia N-kierunkowego jest zestawem N-elementowych krotek, gdzie każda wartość w spójnej kolekcji jest skojarzona z zestawem aliasów uczestniczącym w sprzężeniu i można uzyskać do niego dostęp, odwołując się do tego aliasu w innych klauzulach.

## <a name="syntax"></a>Składnia

Język obsługuje składnię `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. To zapytanie zwraca zestaw krotek z wartościami `N`. Każda krotka ma wartości utworzone w procesie iterowania wszystkich aliasów kontenera w odpowiednich zestawach. 

Przyjrzyjmy się następującej klauzuli FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Zezwól każdemu źródłu na definiowanie `input_alias1, input_alias2, …, input_aliasN`. Ta klauzula FROM zwraca zestaw N-krotek (krotka z N wartościami). Każda krotka ma wartości utworzone w procesie iterowania wszystkich aliasów kontenera w odpowiednich zestawach.  
  
**Przykładowe źródła 1** -2  
  
- Niech `<from_source1>` być zakresem kontenera i reprezentuje zestaw {A, B, C}.  
  
- Niech `<from_source2>` być odwołujące się do zakresu dokumentu input_alias1 i reprezentuje zestawy:  
  
    {1, 2} dla `input_alias1 = A,`  
  
    {3} `input_alias1 = B,`  
  
    {4, 5} dla `input_alias1 = C,`  
  
- Klauzula FROM `<from_source1> JOIN <from_source2>` spowoduje powstanie następujących krotek:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Przykładowe źródła 2** -3  
  
- Niech `<from_source1>` być zakresem kontenera i reprezentuje zestaw {A, B, C}.  
  
- Pozwól `<from_source2>` być odwołujący się do zakresu dokumentu `input_alias1` i reprezentować zestawy:  
  
    {1, 2} dla `input_alias1 = A,`  
  
    {3} `input_alias1 = B,`  
  
    {4, 5} dla `input_alias1 = C,`  
  
- Pozwól `<from_source3>` być odwołujący się do zakresu dokumentu `input_alias2` i reprezentować zestawy:  
  
    {100, 200} dla `input_alias2 = 1,`  
  
    {300} `input_alias2 = 3,`  
  
- Klauzula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` spowoduje powstanie następujących krotek:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Brak krotek dla innych wartości `input_alias1`, `input_alias2`, dla których `<from_source3>` nie zwróciła żadnych wartości.  
  
**Przykładowe źródła 3** -3  
  
- Niech < from_source1 > być w zakresie kontenera i reprezentuje zestaw {A, B, C}.  
  
- Niech `<from_source1>` być zakresem kontenera i reprezentuje zestaw {A, B, C}.  
  
- Niech < from_source2 > być odwołujące się do zakresu dokumentu input_alias1 i reprezentuje zestawy:  
  
    {1, 2} dla `input_alias1 = A,`  
  
    {3} `input_alias1 = B,`  
  
    {4, 5} dla `input_alias1 = C,`  
  
- Zezwól na `<from_source3>` zakresem `input_alias1` i reprezentowania zestawów:  
  
    {100, 200} dla `input_alias2 = A,`  
  
    {300} `input_alias2 = C,`  
  
- Klauzula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` spowoduje powstanie następujących krotek:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
  > [!NOTE]
  > Spowodowało to przekroczenie iloczynu między `<from_source2>` i `<from_source3>`, ponieważ oba zakresy należą do tego samego `<from_source1>`.  Wynika to z 4 (2x2) krotek z wartością A, 0 krotek, które mają wartość B (1x0) i 2 (2x1) krotek mające wartość C.  
  
## <a name="examples"></a>Przykłady

W poniższych przykładach pokazano, jak działa klauzula JOIN. Przed uruchomieniem tych przykładów Przekaż przykładowe [dane rodziny](sql-query-getting-started.md#upload-sample-data). W poniższym przykładzie wynik jest pusty, ponieważ iloczyn między elementami źródłowymi i pustym jest pusty:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Wynik:

```json
    [{
    }]
```

W poniższym przykładzie sprzężenie jest iloczynem krzyżowym między dwoma obiektami JSON, głównym elementem `id` i `children` podgłównym. Fakt, że `children` jest tablicą, nie obowiązuje w sprzężeniu, ponieważ zajmuje się pojedynczym elementem głównym, który jest tablicą `children`. Wynik zawiera tylko dwa wyniki, ponieważ iloczyn poprzeczny każdego elementu z tablicą daje tylko jeden element.

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

Źródłowa klauzula JOIN jest iteratorem. Dlatego przepływ w poprzednim przykładzie jest:  

1. Rozwiń każdy element podrzędny `c` w tablicy.
2. Zastosuj iloczyn krzyżowy z elementem głównym elementu `f` z każdym elementem podrzędnym, `c` ten, który został spłaszczony w pierwszym kroku.
3. Na koniec Zaprojektuj tylko obiekt główny `f` właściwości `id`.

Pierwszy element, `AndersenFamily`, zawiera tylko jeden element `children`, więc zestaw wyników zawiera tylko jeden obiekt. Drugi element, `WakefieldFamily`, zawiera dwa `children`, więc iloczyn krzyżowy wytwarza dwa obiekty, jeden dla każdego `children` elementu. Główne pola w obu elementach są takie same, tak jak można oczekiwać w iloczynie wektorowym.

Prawdziwe narzędzie klauzuli JOIN polega na utworzeniu krotek od iloczynu krzyżowego w kształcie, który w przeciwnym razie trudno jest projektować. Poniższy przykład filtruje kombinację spójnej kolekcji, która umożliwia użytkownikowi wybranie warunku, który jest ogólny przez krotki.

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

Poniższe rozszerzenie powyższego przykładu wykonuje podwójne sprzężenie. Można wyświetlić iloczyn krzyżowy jako następujący pseudo kod:

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

`AndersenFamily` ma jednego elementu podrzędnego, który ma jedną PET, więc iloczyn krzyżowy daje jeden wiersz (1\*1\*1) z tej rodziny. `WakefieldFamily` ma dwa elementy podrzędne, tylko jeden z nich ma zwierzęta domowe, ale ten element podrzędny ma dwa zwierzęta domowe. Iloczyn krzyżowy dla tej rodziny daje 1\*1\*2 = 2 wierszy.

W następnym przykładzie istnieje dodatkowy filtr dla `pet`, który wyklucza wszystkie krotki, w których nazwa PET nie jest `Shadow`. Można tworzyć krotki z tablic, filtrować według dowolnego elementu krotki i projektować dowolną kombinację elementów.

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
- [Podzapytania](sql-query-subquery.md)