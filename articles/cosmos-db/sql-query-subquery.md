---
title: Podksemie SQL dla usługi Azure Cosmos DB
description: Dowiedz się więcej o podksyłaniach SQL i ich typowych przypadkach użycia oraz różnych typach podkemietów w usłudze Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 42d9e8b190747a3ffaf0e46ea1eddda33d09bb24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870568"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Przykłady podzapytania SQL dla usługi Azure Cosmos DB

Podkwerenda jest kwerendą zagnieżdżoną w innej kwerendzie. Podkwerenda jest również nazywana kwerendą wewnętrzną lub wewnętrzną. Instrukcja zawierająca podkwerendę jest zwykle nazywana kwerendą zewnętrzną.

W tym artykule opisano podkemiecje SQL i ich typowe przypadki użycia w usłudze Azure Cosmos DB. Wszystkie przykładowe zapytania w tym doc można uruchomić względem zestawu danych żywieniowych, który jest wstępnie załadowany na [platformie Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo).

## <a name="types-of-subqueries"></a>Rodzaje podkemietów

Istnieją dwa główne typy podkemietów:

* **Skorelowane**: Podkwi, która odwołuje się do wartości z kwerendy zewnętrznej. Podkwerenda jest oceniana raz dla każdego wiersza, który przetwarza kwerendy zewnętrznej.
* **Nieskorelowane:** Podkwerenda, która jest niezależna od kwerendy zewnętrznej. Można go uruchomić samodzielnie bez polegania na kwerendzie zewnętrznej.

> [!NOTE]
> Usługa Azure Cosmos DB obsługuje tylko skorelowane podquerie.

Podksyfikacje mogą być dalej klasyfikowane na podstawie liczby wierszy i kolumn, które zwracają. Istnieją trzy typy:
* **Tabela**: Zwraca wiele wierszy i wiele kolumn.
* **Wiele wartości**: Zwraca wiele wierszy i pojedynczą kolumnę.
* **Skalar**: Zwraca pojedynczy wiersz i pojedynczą kolumnę.

Kwerendy SQL w usłudze Azure Cosmos DB zawsze zwracają pojedynczą kolumnę (wartość prostą lub złożony dokument). W związku z tym tylko wielowartowiowe i skalarne podquerie mają zastosowanie w usłudze Azure Cosmos DB. Podkwerendy wielowartościowej można używać tylko w klauzuli FROM jako wyrażenia relacyjnego. Podkwerendy skalarnej można użyć jako wyrażenia skalarnego w klauzuli SELECT lub WHERE lub jako wyrażenia relacyjnego w klauzuli FROM.

## <a name="multi-value-subqueries"></a>Podksyle wielowartowiowe

Podksy podkwity o wielu wartościach zwracają zestaw dokumentów i są zawsze używane w klauzuli FROM. Są one używane do:

* Optymalizowanie wyrażeń JOIN. 
* Ocena kosztownych wyrażeń raz i odwoływanie się wiele razy.

## <a name="optimize-join-expressions"></a>Optymalzowanie wyrażeń JOIN

Wielowartościowe podkemiecje mogą optymalizować wyrażenia JOIN, wypychając predykaty po każdym wyrażeniu select-many, a nie po wszystkich sprzężeniach krzyżowych w klauzuli WHERE.

Rozpatrzmy następujące zapytanie:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

W przypadku tej kwerendy indeks będzie zgodny z każdym dokumentem, który ma znacznik o nazwie "formuła dla niemowląt". Jest to składnik odżywczy o wartości od 0 do 10 i element służący o kwocie większej niż 1. Wyrażenie JOIN w tym miejscu wykona cross-product wszystkich elementów tagów, składników odżywczych i tablic porcji dla każdego dokumentu dopasowania przed zastosowaniem dowolnego filtru. 

Klauzula WHERE zastosuje predykat filtru dla każdej <c, t, n, s> krotki. Na przykład jeśli pasujący dokument miał 10 elementów w każdej z trzech tablic, zostanie on rozszerzony do 1 x 10 x 10 x 10 (czyli 1000) krotek. Za pomocą podkemiewów w tym miejscu może pomóc w odfiltrowaniu połączonych elementów tablicy przed przystąpieniem do następnego wyrażenia.

Ta kwerenda jest odpowiednikiem poprzedniej, ale używa podksyłek:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Załóżmy, że tylko jeden element w tablicy tagów pasuje do filtru i istnieje pięć elementów dla modułów składników odżywczych i tablic porcji. Wyrażenia JOIN zostaną następnie rozwinięte do 1 x 1 x 5 x 5 = 25 elementów, w przeciwieństwie do 1000 elementów w pierwszym zapytaniu.

## <a name="evaluate-once-and-reference-many-times"></a>Oceniaj raz i odwołujej się wiele razy

Podkemiety mogą pomóc w optymalizacji zapytań za pomocą kosztownych wyrażeń, takich jak funkcje zdefiniowane przez użytkownika (UDF), złożone ciągi ciągów lub wyrażenia arytmetyczne. Można użyć podkwerendy wraz z wyrażeniem JOIN do oceny wyrażenia raz, ale odwołuje się do niego wiele razy.

Następująca kwerenda uruchamia `GetMaxNutritionValue` UDF dwa razy:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Oto równoważne zapytanie, które uruchamia UDF tylko raz:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Należy pamiętać o zachowaniu międzyunikalo-produktowym wyrażeń JOIN. Jeśli wyrażenie UDF można ocenić jako niezdefiniowane, należy upewnić się, że wyrażenie JOIN zawsze tworzy pojedynczy wiersz, zwracając obiekt z podzapytania, a nie bezpośrednio.
>

Oto podobny przykład, który zwraca obiekt, a nie wartość:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

Podejście nie ogranicza się do plików UDF. Dotyczy to każdego potencjalnie kosztownego wyrażenia. Na przykład, można przyjąć takie samo podejście `avg`z funkcją matematyczną:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Naśladuj sprzężenie z zewnętrznymi danymi referencyjnymi

Często konieczne może być odwoływanie się do danych statycznych, które rzadko się zmieniają, takich jak jednostki miary lub kody krajów. Lepiej nie powielać takich danych dla każdego dokumentu. Unikanie tego powielania pozwoli zaoszczędzić na pamięci masowej i poprawić wydajność zapisu, utrzymując mniejszy rozmiar dokumentu. Za pomocą podzapytania można naśladować semantykę sprzężenia wewnętrznego z kolekcją danych referencyjnych.

Rozważmy na przykład ten zestaw danych referencyjnych:

| **Jednostki** | **Nazwa**            | **Mnożnik** | **Jednostka bazowa** |
| -------- | ------------------- | -------------- | ------------- |
| Ng       | Nanogram            | 1.00E-09       | Gram          |
| Μg       | Mikrogramów           | 1.00E-06       | Gram          |
| Mg       | Miligram           | 1.00E-03       | Gram          |
| g        | Gram                | 1.00E+00       | Gram          |
| Kg       | Kilogram            | 1.00E+03       | Gram          |
| Mg       | Megagram            | 1.00E+06       | Gram          |
| Gg       | Gigagram            | 1.00E+09       | Gram          |
| Nj       | Nanodżuł           | 1.00E-09       | Joule         |
| μJ       | Mikrodżul          | 1.00E-06       | Joule         |
| Mj       | Millijoule ( Millijoule )          | 1.00E-03       | Joule         |
| J        | Joule               | 1.00E+00       | Joule         |
| Kj       | Kilodżul           | 1.00E+03       | Joule         |
| Mj       | Megadżuł           | 1.00E+06       | Joule         |
| Gj       | Gigajoule ( Gigajoule )           | 1.00E+09       | Joule         |
| cal (cal)      | Kalorii             | 1.00E+00       | Kalorii       |
| Kcal     | Kalorii             | 1.00E+03       | Kalorii       |
| J.m       | Jednostki międzynarodowe |                |               |


Następująca kwerenda naśladuje łączenie z tymi danymi, dzięki czemu można dodać nazwę jednostki do danych wyjściowych:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Podkemiety skalarne

Scalar podkwerendy wyrażenie jest podkwerendy, która ocenia do pojedynczej wartości. Wartość wyrażenia podkwerendy skalarnej jest wartością projekcji (klauzula SELECT) podkwerendy.  W wielu miejscach, w których jest prawidłowe wyrażenie skalarne, można użyć wyrażenia podkwerendy skalarnej. Na przykład można użyć podkwerendy skalarnej w dowolnym wyrażeniu w klauzulach SELECT i WHERE.

Korzystanie ze skalarnej podkwerendy nie zawsze pomaga jednak zoptymalizować. Na przykład przekazywanie podkwerendy skalarnej jako argumentu do funkcji systemowych lub zdefiniowanych przez użytkownika nie zapewnia żadnych korzyści w jednostce zasobów (RU) zużycie lub opóźnienie.

Podkemiety skalarne można dalej klasyfikować jako:
* Podksibli skalarne proste
* Agregowanie podkemiewów skalarnych

## <a name="simple-expression-scalar-subqueries"></a>Podksibli skalarne proste

Podkwerenda skalarna simple-expression jest skorelowanym podkwerendą, która ma klauzulę SELECT, która nie zawiera żadnych wyrażeń agregujących. Te podkwery nie zapewniają żadnych korzyści optymalizacji, ponieważ kompilator konwertuje je na jedno większe proste wyrażenie. Nie ma skorelowanego kontekstu między zapytaniami wewnętrznymi i zewnętrznymi.

Oto kilka przykładów:

**Przykład 1**

```sql
SELECT 1 AS a, 2 AS b
```

Tę kwerendę można przepisać, używając podkwerendy skalarnej prostego wyrażenia, aby:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Oba zapytania dają to dane wyjściowe:

```json
[
  { "a": 1, "b": 2 }
]
```

**Przykład 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

Tę kwerendę można przepisać, używając podkwerendy skalarnej prostego wyrażenia, aby:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Dane wyjściowe kwerendy:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**Przykład 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

Tę kwerendę można przepisać, używając podkwerendy skalarnej prostego wyrażenia, aby:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Dane wyjściowe kwerendy:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Agregowanie podkemiewów skalarnych

Zagregowane podkwarcie skalarne to podkwerenda, które ma funkcję agregacji w projekcji lub filtrze, która jest obliczana na pojedynczą wartość.

**Przykład 1:**

Oto podkwerendy z jednym wyrażeniem funkcji agregacji w jego projekcji:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Dane wyjściowe kwerendy:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**Przykład 2**

Oto podkwerenda z wieloma wyrażeniami funkcji agregujących:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Dane wyjściowe kwerendy:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**Przykład 3**

Oto zapytanie z podkwerendą agregacji zarówno w projekcji, jak i w filtrze:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Dane wyjściowe kwerendy:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Bardziej optymalnym sposobem na zapisanie tej kwerendy jest dołączenie do podzapytania i odwoływanie się do aliasu podzapytania w klauzulach SELECT i WHERE. Ta kwerenda jest bardziej wydajne, ponieważ należy wykonać podkwerendy tylko w instrukcji join, a nie w rzutowaniu i filtrze.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>Wyrażenie EXISTS

Usługa Azure Cosmos DB obsługuje wyrażenia EXISTS. Jest to zagregowane podkwarcie skalarne wbudowane w interfejs API SQL usługi Azure Cosmos DB. EXISTS jest wyrażeniem logicznym, które przyjmuje wyrażenie podzapytania i zwraca wartość true, jeśli podkwerenda zwraca wszystkie wiersze. W przeciwnym razie zwraca false.

Ponieważ interfejs API SQL usługi Azure Cosmos DB nie rozróżnia wyrażeń logicznych i innych wyrażeń skalarnych, można użyć EXISTS w klauzulach SELECT i WHERE. Jest to w przeciwieństwie do T-SQL, gdzie wyrażenie logiczne (na przykład EXISTS, BETWEEN i IN) jest ograniczone do filtru.

Jeśli podkwerenda EXISTS zwraca pojedynczą wartość, która jest niezdefiniowana, EXISTS oceni wartość false. Na przykład należy wziąć pod uwagę następujące zapytanie, które ocenia false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Jeśli słowo kluczowe WARTOŚĆ w poprzednim podkwerendy zostanie pominięte, kwerenda oceni wartość true:
```sql
SELECT EXISTS (SELECT undefined) 
```

Podkwerenda ujednej listę wartości na wybranej liście w obiekcie. Jeśli wybrana lista nie zawiera żadnych wartości, podkwerenda zwróci pojedynczą wartość '{}. Ta wartość jest zdefiniowana, więc EXISTS ocenia true.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Przykład: Przepisywanie ARRAY_CONTAINS i DOŁĄCZANIE ZGODNIE Z ISTNIEJĄCYMI

Typowym przypadkiem użycia ARRAY_CONTAINS jest filtrowanie dokumentu przez istnienie elementu w tablicy. W takim przypadku sprawdzamy, czy tablica tagów zawiera element o nazwie "pomarańczowy".

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Można przepisać tę samą kwerendę, aby użyć EXISTS:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Ponadto ARRAY_CONTAINS można sprawdzić tylko, jeśli wartość jest równa dowolnego elementu w tablicy. Jeśli potrzebujesz bardziej złożonych filtrów we właściwościach tablicy, użyj funkcji JOIN.

Należy wziąć pod uwagę następującą kwerendę, która filtruje na podstawie jednostek i `nutritionValue` właściwości w tablicy: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Dla każdego z dokumentów w kolekcji, cross-product jest wykonywany z jego elementów tablicy. Ta operacja JOIN umożliwia filtrowanie właściwości w tablicy. Jednak zużycie ru tej kwerendy będzie znaczące. Na przykład jeśli 1000 dokumentów miał 100 elementów w każdej tablicy, zostanie on rozszerzony do 1000 x 100 (czyli 100 000) krotek.

Korzystanie exists może pomóc uniknąć tego drogiego cross-produktu:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

W takim przypadku filtrowanie elementów tablicy w podkwerendy EXISTS. Jeśli element tablicy pasuje do filtru, a następnie projekt i EXISTS ocenia true.

Można również alias EXISTS i odwoływać się do niego w projekcji:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Dane wyjściowe kwerendy:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>Wyrażenie TABLICA

Za pomocą wyrażenia ARRAY można rzutować wyniki kwerendy jako tablicę. Tego wyrażenia można używać tylko w klauzuli SELECT kwerendy.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Dane wyjściowe kwerendy:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Podobnie jak w przypadku innych podkemietów, możliwe są filtry z wyrażeniem ARRAY.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Dane wyjściowe kwerendy:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

Wyrażenia tablicy mogą również pochodzić po klauzuli FROM w podkemietach.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Dane wyjściowe kwerendy:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Następne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelowanie danych dokumentów](modeling-data.md)
