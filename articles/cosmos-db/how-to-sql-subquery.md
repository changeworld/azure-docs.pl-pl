---
title: Podzapytania SQL dla usługi Azure Cosmos DB
description: Dowiedz się więcej o podzapytań SQL i ich typowe przypadki użycia w usłudze Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: tisande
ms.openlocfilehash: 3ba547aea9034777fe76f3c911efd2648f6184fa
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/25/2019
ms.locfileid: "64514802"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Przykłady podzapytania SQL dla usługi Azure Cosmos DB

Podzapytania jest zapytanie zagnieżdżone w obrębie innego zapytania. Zapytanie wewnętrzne lub wewnętrzne wybierz podzapytania jest również nazywany i instrukcję, zawierającą podzapytania jest zwykle nazywany zapytanie zewnętrzne.

Istnieją dwa rodzaje podzapytań:

* Skorelowane - skorelowane podzapytanie jest podzapytania, który odwołuje się do wartości z zapytania zewnętrznego. Podzapytanie jest oceniany jeden raz dla każdego wiersza, który jest przetwarzany przez zapytanie zewnętrzne.

* Skorelowane non - bez skorelowane podzapytanie jest podzapytania, który jest niezależny od zapytanie zewnętrzne i mogą być wykonywane na jego własnej bez polegania na zapytanie zewnętrzne.

> [!NOTE]
> Usługa Azure Cosmos DB obsługuje tylko skorelowany podzapytania.

## <a name="types-of-subqueries"></a>Typy podzapytań

Zapytania podrzędne mogą być klasyfikowane w dalszej, na podstawie liczby wierszy i kolumn, które zwracają. Istnieją trzy typy:
1.  **Tabela**: Zwraca wiele wierszy i wielu kolumn
2.  **Wielowartościowy**: Zwraca wiele wierszy i jedną kolumnę
3.  **Skalarną**: Zwraca jednowierszową i jedną kolumnę

> [!NOTE]
> Usługa Azure Cosmos DB obsługuje wielowartościowymi i operatora skalarnego podzapytań

Azure zapytań SQL usługi Cosmos DB zawsze zwraca jednokolumnową (prostą wartością lub złożonych dokumentów). W związku z tym tylko wielowartościowymi i operatora skalarnego podzapytania z powyższych mają zastosowanie w usłudze Azure Cosmos DB. Podzapytania wielowartościowych należy używać tylko w klauzuli FROM jako wyrażenie relacyjne, chociaż skalarne podzapytania mogą być używane jako wyrażenie skalarne, które w polu Wybierz lub klauzuli WHERE lub wyrażenie relacyjne w klauzuli FROM.


## <a name="multi-value-subqueries"></a>Podzapytania wielowartościowymi

Podzapytania wielowartościowych zwrócić zestaw dokumentów i są zawsze używane w klauzuli FROM. Służą one do:

* Optymalizacja sprzężenia wyrażeń 
* Ocenianie wyrażenia kosztowne raz i odwołanie do wiele razy

### <a name="optimize-join-expressions"></a>Optymalizacja sprzężenia wyrażeń

Podzapytania wielowartościowych można zoptymalizować wyrażeniach sprzężenia, wypychając predykatów po każdym wyrażeniu Wybierz wiele, a nie po wszystkich sprzężenia krzyżowe w klauzuli WHERE.

Należy wziąć pod uwagę następujące zapytanie:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Dla tego zapytania indeksu będzie odpowiadał dowolny dokument, który ma tag o nazwie "niemowląt", element odżywczych wartość z zakresu od 0 i 10 i element obsługująca o kwotę większą niż 1. Jednak wyrażeniu JOIN w tym miejscu będzie wykonywać iloczyn wektorowy wszystkich elementów, tagów, składników i porcji tablic dla każdego pasującego dokumentu przed zastosowaniem dowolnego filtru. Klauzula WHERE następnie zastosuje filtr krotki predykatu w każdym < c, t, n, s >. Na przykład jeśli pasujących dokumentów 10 elementów we wszystkich trzech tablic, jego rozwinie 1 x 10 x 10 x 10 (czyli 1000) krotek. W tym miejscu przy użyciu podzapytań, może pomóc w filtrowanie elementów tablicy połączonych przed sprzężeniem ich ze następnego wyrażenia.

To zapytanie jest równoważne z poprzednim, ale korzysta z podzapytań:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Zakładając, że tylko jeden element w tablicy tagów jest zgodny z filtrem i pięć elementów dla składników i porcji tablic, wyrażeniach sprzężenia rozszerzy do 1 x 1 x 5 x 5 = 25 elementy w przeciwieństwie do 1000 elementów w pierwszym zapytaniu.

### <a name="evaluate-once-and-reference-many-times"></a>Oceń raz i odwołania wiele razy

Podzapytania może pomóc w optymalizacji zapytań przy użyciu wyrażenia kosztowne, takich jak funkcje zdefiniowane przez użytkownika (UDF) lub złożony ciąg lub wyrażeniach arytmetycznych. Podzapytania wraz z wyrażeniu JOIN służy do obliczenia wyrażenia raz, ale odwoływać się do niego wiele razy.

Następujące zapytanie wykonuje UDF GetMaxNutritionValue dwa razy:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Oto zapytanie równoważne, która jest wykonywana tylko funkcji zdefiniowanej przez użytkownika po:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Jeśli wyrażenia funkcji zdefiniowanej przez użytkownika może być Niezdefiniowany, biorąc pod uwagę zachowanie obejmujących wiele produktów w wyrażeniach sprzężenia, należy upewnić się, że zawsze wyrażeniu JOIN tworzy jeden wiersz, zwracając obiekt z podzapytanie, a nie wartość bezpośrednio.
>

Oto przykład podobne, która zwraca obiekt, a nie wartość:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

To podejście nie jest ograniczona do funkcji zdefiniowanych przez użytkownika, ale raczej do dowolnego wyrażenia potencjalnie kosztowne. Na przykład firma Microsoft może potrwać tej samej metody, za pomocą avg funkcji matematycznych:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

### <a name="mimic-join-with-external-reference-data"></a>Naśladowanie łączenia z danymi referencyjnymi zewnętrznych

Firma Microsoft często muszą odwoływać się do danych statycznych, które zmieniają się rzadko, takich jak jednostki pomiaru albo numerów kierunkowych krajów. W przypadku takich danych zaleca się nie można zduplikować go dla każdego dokumentu. Unikanie ta duplikacja zapisywania w magazynie i zwiększyć wydajność, przechowując mniejszy rozmiar dokumentu. Podzapytania może służyć w tym miejscu do naśladowania semantyki sprzężenia wewnętrznego z kolekcją danych referencyjnych.
Na przykład należy wziąć pod uwagę ten zestaw danych referencyjnych.

| **Unit** | **Nazwa**            | **Mnożnik** | **Jednostka podstawowa** |
| -------- | ------------------- | -------------- | ------------- |
| NG       | Nanogram            | 1.00E-09       | Gram          |
| g       | Microgram           | 1.00E-06       | Gram          |
| mg       | Milligram           | 1.00E-03       | Gram          |
| b        | Gram                | 1.00E + 00       | Gram          |
| kg       | Kilogram            | 1.00E + 03       | Gram          |
| Mg       | Megagram            | 1.00E + 06       | Gram          |
| Gg       | Gigagram            | 1.00E + 09       | Gram          |
| nJ       | Nanojoule           | 1.00E-09       | Joule —         |
| µJ       | Microjoule          | 1.00E-06       | Joule —         |
| mJ       | Millijoule          | 1.00E-03       | Joule —         |
| J        | Joule —               | 1.00E + 00       | Joule —         |
| kJ       | Kilojoule           | 1.00E + 03       | Joule —         |
| MJ       | Megajoule           | 1.00E + 06       | Joule —         |
| GJ       | Gigajoule           | 1.00E + 09       | Joule —         |
| Licencja CAL      | Calorie             | 1.00E + 00       | Kaloria       |
| kcal     | Calorie             | 1.00E + 03       | Kaloria       |
| IU       | Jednostki międzynarodowe |                |               |


Następujące zapytanie naśladuje łączącymi się z tych danych, tak aby dodamy nazwę jednostki w typie danych wyjściowych:

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

## <a name="scalar-subqueries"></a>Skalarnych podzapytań

Wyrażenie skalarne podzapytania znajduje się podzapytanie, którego wynikiem jest wartość typu single. Wartość wyrażenia skalarne podzapytania jest wartością rzutu (klauzuli SELECT) podzapytanie.  Wyrażenia skalarne podzapytania można używać w wielu miejscach wyrażenie skalarne, które jest prawidłowy. Na przykład skalarne podzapytania może służyć w dowolnych wyrażeń w obu wybierz i klauzulach WHERE.
Jednak przy użyciu skalarne podzapytanie nie zawsze pomaga w optymalizacji. Na przykład przekazanie skalarne podzapytanie jako argumentu do systemu lub funkcje zdefiniowane przez użytkownika zapewnia żadnych korzyści w zużycia jednostek RU lub opóźnienia.

Podzapytań skalarnych mogą być dodatkowo klasyfikowane jako:
* Proste wyrażenie skalarne podzapytań
* Łączny skalarnych podzapytań

### <a name="simple-expression-scalar-subqueries"></a>Proste wyrażenie skalarne podzapytań

Proste wyrażenie skalarne podzapytania jest skorelowane podzapytanie, mającej klauzuli SELECT, która nie zawiera żadnych wyrażeniami dotyczącymi agregacji. Te podzapytań zapewniają korzyści nie optymalizacji, ponieważ kompilator konwertuje je do jednego większego wyrażenia proste. Nie jest skorelowany kontekst między wewnętrznymi i zewnętrznymi zapytania.

Poniżej przedstawiono kilka przykładów:

**Przykład 1**

```sql
SELECT 1 AS a, 2 AS b
```

To zapytanie można dopasować, za pomocą prostego wyrażenia skalarne podzapytania do:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Oba zapytania do przedstawienia tych danych wyjściowych:

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

To zapytanie można dopasować, za pomocą prostego wyrażenia skalarne podzapytania do:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Dane wyjściowe zapytań:

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

To zapytanie można dopasować, za pomocą prostego wyrażenia skalarne podzapytania do:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Dane wyjściowe zapytań:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

## <a name="aggregate-scalar-subqueries"></a>Łączny skalarnych podzapytań

Łączny podzapytania skalarne znajduje się podzapytanie zawierającej funkcji agregującej w projekcji lub filtr, którego wynikiem jest wartość typu single.

**Przykład 1:**

W tym miejscu znajduje się podzapytanie za pomocą wyrażenia pojedynczej funkcji agregującej w swojej projekcji:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Dane wyjściowe zapytań:

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

Podzapytania z wielu funkcji agregującej wyrażeń:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Dane wyjściowe zapytań:

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

Zapytanie o agregacji podzapytania, zarówno w przypadku projekcji, jak i filtr:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Dane wyjściowe zapytań:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Więcej optymalny sposób, aby zapisać to zapytanie jest odbędzie się podzapytanie i odwoływać się do podzapytania aliasu w obu wybierz i klauzulach WHERE. To zapytanie jest bardziej wydajne, ponieważ konieczne będzie wykonanie podzapytania tylko wewnątrz instrukcji join i nie ma w projekcji i filtru.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

### <a name="exists-expression"></a>Wyrażenie EXISTS

Usługa Azure Cosmos DB obsługuje wyrażenia EXISTS. Jest to łączny podzapytania skalarne, wbudowane interfejsu API SQL usługi Azure Cosmos DB. EXISTS jest wyrażenie logiczne, które przyjmuje wyrażenia podzapytania i zwraca wartość true, jeśli podzapytanie zwraca wszystkie wiersze; w przeciwnym razie zwraca wartość false.
Ponieważ interfejsu API SQL usługi Azure Cosmos DB nie odróżniają między wyrażenia logiczne i inne wyrażenia skalarne, EXISTS mogą być używane w obu wybierz i klauzulach WHERE. Jest to w przeciwieństwie do języka T-SQL, gdzie wyrażenie logiczne (np. EXISTS, między oraz w) jest ograniczona do bieżącego filtra.

Jeśli podzapytanie EXISTS zwraca jedną wartość, która jest niezdefiniowana, EXISTS będą oceniać na wartość false. Na przykład należy wziąć pod uwagę następujące zapytanie, które zwróci wartość false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Jednak w przypadku pominięcia VALUE — słowo kluczowe w podzapytaniu powyżej zapytania będą oceniać na wartość true:
```sql
SELECT EXISTS (SELECT undefined) 
```

Podzapytanie będzie zawierać listę wartości na liście wyboru w obiekcie. Jeśli wybrana lista nie zawiera danych, zwraca pojedynczą wartość podzapytanie '{}"który jest zdefiniowany i ISTNIEJE w związku z tym wynikiem jest wartość true.

### <a name="example-rewriting-arraycontains-and-join-as-exists"></a>Przykład: ARRAY_CONTAINS ponownego zapisywania adresów i sprzężenia jako EXISTS

Typowy przypadek użycia programu ARRAY_CONTAINS jest filtrującą dane dokumentu z istniejących elementów w tablicy. W tym przypadku odbywa się sprawdzenie, czy tablica tagów zawiera element o nazwie pomarańczowy.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Tego samego zapytania można dopasować do użycia EXISTS:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Ponadto ARRAY_CONTAINS może tylko sprawdzić, czy wartość jest równa dowolnego elementu w tablicy. Jeśli potrzebne są bardziej złożonych filtrów właściwości tablicy, sprzężenia jest wymagana.

Należy wziąć pod uwagę następujące zapytanie, że filtry oparte na jednostkach i nutritionValue właściwości w tablicy: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Dla każdej wersji dokumenty w kolekcji obejmujących wiele produktów odbywa się ze swoimi elementami tablicy. Ta operacja JOIN pozwala filtrować dane według właściwości w tablicy. Jednak zużycia jednostek RU to zapytanie będzie znaczące. Na przykład jeśli 1000 dokumentów 100 elementów w każdej macierzy, jego rozszerzy do 1000 x 100 (tj. 100 000) krotek.

Za pomocą `EXISTS` może pomóc uniknąć tego kosztowne obejmujących wiele produktów:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

W tym przypadku możemy filtrować elementy tablicy w podzapytaniu EXISTS. Jeśli do elementu tablicy jest zgodny z filtrem, a następnie mamy do projektu i `EXISTS` zwraca wartość true.

Firma Microsoft może również alias EXISTS i odwoływać się do niego w projekcji:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Dane wyjściowe zapytań:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

### <a name="array-expression"></a>Wyrażenie tablicy

`ARRAY` Wyrażenie może służyć do projektu z wynikami zapytania jako tablica. To wyrażenie należy używać tylko w klauzuli SELECT w zapytaniu.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Dane wyjściowe zapytań:

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

Podobnie jak w przypadku innych podzapytań filtry ze `ARRAY` wyrażenia są możliwe.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Dane wyjściowe zapytań:

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

Wyrażenia tablicy może również pochodzić po klauzuli FROM w podzapytania.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Dane wyjściowe zapytań:

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

## <a name="next-steps"></a>Kolejne kroki

- [Przykłady zapytań SQL](how-to-sql-query.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Modelowanie danych dokumentów](modeling-data.md)
