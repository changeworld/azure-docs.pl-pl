---
title: Zapytania SQL w usłudze Azure Cosmos DB
description: Dowiedz się więcej o składni języka SQL, pojęciach związanych z bazami danych oraz zapytaniach języka SQL dla usługi Azure Cosmos DB. Użyj programu SQL jako języka zapytań usługi Azure Cosmos DB w formacie JSON.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mjbrown
ms.openlocfilehash: 04a88558e3aea33c6d99bd0e4f1354c4316f5529
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579225"
---
# <a name="sql-query-examples-for-azure-cosmos-db"></a>Przykłady zapytania SQL dla usługi Azure Cosmos DB

Konta usługi Azure Cosmos DB — interfejs API SQL obsługuje elementy tworzenie zapytań przy użyciu języka SQL (Structured Query) jako język zapytań JSON. Cele projektu języka zapytań usługi Azure Cosmos DB są:

* Obsługuje język SQL, jednym z najbardziej znanych i popularnych języków zapytanie, zamiast inventing nowego języka zapytań. SQL zapewnia model programowania formalne zaawansowane zapytania przez w formacie JSON.  

* Użyj modelu programowania języka JavaScript jako podstawa dla języka zapytań. System typów języka JavaScript, Obliczanie wyrażenia i wywołania funkcji są korzenie interfejsu API SQL. Te katalogi główne Obejmij naturalnych model programowania funkcji takich jak relacyjne projekcji, nawigacja hierarchiczna w formacie JSON samosprzężenia, zapytań przestrzennych i wywołania funkcji zdefiniowanych przez użytkownika (UDF), napisanych w całości w języku JavaScript.

W tym artykule przedstawiono kilka przykładowych zapytań SQL na proste w formacie JSON. Aby dowiedzieć się więcej na temat składni języka SQL usługi Azure Cosmos DB, zobacz [dokumentacja składni SQL](sql-api-query-reference.md).

## <a id="GettingStarted"></a>Wprowadzenie do zapytań SQL

Na koncie usługi SQL API Cosmos DB, tworzenia kontenera o nazwie `Families`. Utwórz dwa proste elementy JSON w kontenerze i uruchomić kilka prostych zapytań względem nich.

### <a name="create-json-items"></a>Tworzenie elementów JSON

Poniższy kod tworzy dwa proste w formacie JSON dotyczących rodziny. Proste elementów JSON dla rodziny Andersen i Wakefield obejmują elementy nadrzędne, elementy podrzędne i ich zwierząt domowych, adresu i informacji o rejestracji. Pierwszy element zawiera ciągi, liczby, wartości logicznych, tablic i zagnieżdżonych właściwości.


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Drugi element używa `givenName` i `familyName` zamiast `firstName` i `lastName`.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female", 
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Zapytania w formacie JSON

Wypróbuj kilka zapytań dotyczących danych JSON, aby poznać niektóre z kluczowych aspektów języka zapytania SQL usługi Azure Cosmos DB.

Następujące zapytanie zwraca elementy gdzie `id` pola dopasowania `AndersenFamily`. Ponieważ jest ono `SELECT *` zapytanie, wyniki kwerendy jest kompletny element JSON. Aby uzyskać więcej informacji na temat składni wybierz zobacz [instrukcji SELECT](sql-api-query-reference.md#select-query). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki zapytania są: 

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

Następujące zapytanie formatuje dane wyjściowe JSON do innego kształtu. Zapytanie projektów nowych JSON `Family` obiektu z dwóch wybranych pól, `Name` i `City`, gdy adres, Miasto jest taka sama jak stan. "NY, NY" pasuje do tej sprawy.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Wyniki zapytania są:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

Następujące zapytanie zwraca imiona wszystkich dzieci w rodzinie którego `id` odpowiada `WakefieldFamily`, uporządkowanych według klasy.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.grade ASC
```

Wyniki są:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

W poprzednich przykładach pokazano kilka aspektów języka zapytań usługi Cosmos DB:  

* Ponieważ interfejsu API SQL działa na wartości JSON, zajmuje się on w kształcie drzewo jednostek zamiast wierszy i kolumn. Możesz zapoznać się z węzłami drzewa, na dowolnym poziomie dowolnego, takie jak `Node1.Node2.Node3…..Nodem`, podobnie jak odwołanie legalną dwuczęściową `<table>.<column>` ANSI SQL.

* Ponieważ język zapytań działa z danymi ze schematów, system typu musi być powiązany dynamicznie. To samo wyrażenie może spowodować uzyskiwanie różnych typów w różnych elementach. Wynik zapytania jest prawidłową wartością JSON, ale nie jest gwarantowana stałego schematu.  

* Usługa Azure Cosmos DB obsługuje tylko ścisłe elementy JSON. System typów i wyrażenia są ograniczone do czynienia tylko z typami JSON. Aby uzyskać więcej informacji, zobacz [specyfikacji formatu JSON](https://www.json.org/).  

* Kontener usługi Cosmos DB to kolekcja elementów JSON bez schematu. Relacje wewnątrz i pomiędzy elementami kontenera są przechwytywane niejawnie przez zawierania, nie za pomocą klucza podstawowego i relacje klucza obcego. Ta funkcja jest ważna dla sprzężeń wewnątrz elementu omówione w dalszej części tego artykułu.

## <a id="SelectClause"></a>Klauzula SELECT

Każdego zapytania, który składa się z klauzuli SELECT i opcjonalnie FROM a klauzulach WHERE w poszczególnych standardy ANSI SQL. Zazwyczaj są wyliczane źródła w klauzuli FROM a klauzuli WHERE zastosowanie filtru źródła do pobrania podzbioru w formacie JSON. Klauzula SELECT następnie projektów wymagane wartości JSON na liście wyboru. Aby uzyskać więcej informacji na temat składni, zobacz [instrukcji SELECT](sql-api-query-reference.md#select-query).

Wybierz następujące zapytanie zwraca przykład `address` z `Families` którego `id` odpowiada `AndersenFamily`:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a id="EscapingReservedKeywords"></a>Metoda dostępu do właściwości cytowanej
Możesz uzyskać dostęp właściwości za pomocą operatora [] właściwości w cudzysłowie. Na przykład elementy `SELECT c.grade` i `SELECT c["grade"]` są równoważne. Ta składnia jest przydatne w jako znak ucieczki dla właściwości, która zawiera spacje, znaki specjalne lub ma taką samą nazwę jak słowo kluczowe SQL lub słowo zastrzeżone.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="nested-properties"></a>Właściwości zagnieżdżone

Poniższy przykład projekty dwie właściwości zagnieżdżone, `f.address.state` i `f.address.city`.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="json-expressions"></a>Wyrażenia JSON

Projekcja obsługuje również wyrażeń JSON, jak pokazano w poniższym przykładzie:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

W powyższym przykładzie klauzuli SELECT musi utworzyć obiekt JSON, a ponieważ przykład zawiera bez klucza, klauzuli używa nazwy zmiennej niejawnego argumentu `$1`. Następujące zapytanie zwraca dwie zmienne niejawnego argumentu: `$1` i `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="ValueKeyword"></a>Słowo kluczowe VALUE

VALUE — słowo kluczowe udostępnia sposób zwrócenia wartości JSON samodzielnie. Na przykład, zapytanie poniżej zwraca wyrażenie skalarne `"Hello World"` zamiast `{$1: "Hello World"}`:

```sql
    SELECT VALUE "Hello World"
```

Następujące zapytanie zwraca wartości JSON bez `address` etykiety:

```sql
    SELECT VALUE f.address
    FROM Families f
```

Wyniki są:

```json
    [
      {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan",
        "city": "NY"
      }
    ]
```

Poniższy przykład przedstawia sposób zwracania wartości pierwotnych JSON (poziomu liścia drzewa JSON):


```sql
    SELECT VALUE f.address.state
    FROM Families f
```

Wyniki są:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="aliasing"></a>Tworzenie aliasów

Można jawnie alias wartości w zapytaniach. Jeśli zapytanie ma dwie właściwości o takiej samej nazwie, należy użyć aliasów, można zmienić nazwy jedną lub obie właściwości, dzięki czemu są one rozróżniane w przewidywany wynik.

AS — słowo kluczowe używane do aliasów jest opcjonalne, jak pokazano w poniższym przykładzie, podczas projekcji drugiej wartości jako `NameInfo`:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="FromClause"></a>Klauzula FROM

ŹRÓDŁOWA (`FROM <from_specification>`) klauzula jest opcjonalne, jeśli źródło jest filtrowana lub przewidywany później w zapytaniu. Aby uzyskać więcej informacji na temat składni, zobacz [SKŁADNIĘ](sql-api-query-reference.md#bk_from_clause). Zapytanie, takich jak `SELECT * FROM Families` wylicza w całym `Families` kontenera. Umożliwia także specjalny identyfikator GŁÓWNYM w kontenerze, zamiast nazwy kontenera.

Klauzula FROM wymusza następujące zasady na zapytanie:

* Kontener może mieć alias, taki jak `SELECT f.id FROM Families AS f` lub po prostu `SELECT f.id FROM Families f`. W tym miejscu `f` jest aliasem dla `Families`. PONIEWAŻ jest opcjonalnym słowem kluczowym na alias identyfikator.  

* Jeden raz, ponieważ oryginalna nazwa źródła nie może być powiązany. Na przykład `SELECT Families.id FROM Families f` ma nieprawidłową składnię ponieważ identyfikator `Families` jest aliasem i nie można rozpoznać dłużej.  

* Wszystkie właściwości odwołania musi być w pełni kwalifikowana uniknąć niejednoznaczne powiązań w przypadku braku zgodności ścisłego schematu. Na przykład `SELECT id FROM Families f` ma nieprawidłową składnię ponieważ właściwość `id` nie jest powiązany.

### <a name="get-subitems-by-using-the-from-clause"></a>Pobierz elementy podrzędne przy użyciu klauzuli FROM

Klauzula FROM może zmniejszyć źródła utworzenie mniejszego podzestawu. Aby wyliczyć tylko poddrzewo w każdym elemencie, subroot może stać się źródła, jak pokazano w poniższym przykładzie:

```sql
    SELECT *
    FROM Families.children
```

Wyniki są:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Poprzednie Zapytanie używane tablicę jako źródła, ale obiekt jest również używany jako źródło. Zapytanie uwzględnia wszystkie wartości JSON prawidłowe, definicja źródła do włączenia w wyniku. Poniższy przykład, można wyłączyć `Families` nie mają `address.state` wartość.

```sql
    SELECT *
    FROM Families.address.state
```

Wyniki są:

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="WhereClause"></a>Klauzula WHERE

Opcjonalna klauzula WHERE (`WHERE <filter_condition>`) określa warunki, w formacie JSON źródła musi spełniać dla zapytania je uwzględnić w wynikach. Element JSON musi być określone warunki, aby `true` wziąć pod uwagę na wynik. Warstwa indeksu używa klauzuli WHERE, aby określić najmniejszy podzbiór elementów źródła, które mogą być częścią wynik. Aby uzyskać więcej informacji na temat składni, zobacz [składni gdzie](sql-api-query-reference.md#bk_where_clause).

Poniższe zapytanie elementów żądań, które zawierają `id` właściwości, których wartość jest `AndersenFamily`. Wyklucza dowolny element, który nie ma `id` właściwości lub którego wartość nie jest zgodna `AndersenFamily`.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Wyrażenia skalarne w klauzuli WHERE

W poprzednim przykładzie pokazano proste zapytanie dotyczące równości. Interfejs API SQL obsługuje również różne [wyrażenia skalarne](#scalar-expressions). Najczęściej używane są wyrażenia binarne i jednoargumentowe. Odwołania do właściwości z obiektu JSON źródła są również prawidłowymi wyrażeniami.

Można użyć następujących obsługiwanych operatory binarne:  

|**Typ operatora**  | **Wartości** |
|---------|---------|
|Arytmetyczny | +,-,*,/,% |
|Bitowy    | \|, &, ^, <<, >>, >>> (wypełnienie zerami, przesunięcie w prawo) |
|Logiczny    | AND, OR, NOT      |
|Porównanie | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\| (łączenie) |

Operatory dwuargumentowe należy użyć następujących zapytań:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Można również użyć operatorów jednoargumentowych +,-, ~, a nie w zapytaniach, jak pokazano w poniższych przykładach:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Umożliwia także odwołania do właściwości w zapytaniach. Na przykład `SELECT * FROM Families f WHERE f.isRegistered` zwraca element JSON zawierający właściwości `isRegistered` o wartości równej `true`. Wszelkie inne wartości, takich jak `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`, lub `<array>`, wyklucza element z wynikiem. 

### <a name="equality-and-comparison-operators"></a>Operatory równości i porównania

W poniższej tabeli przedstawiono wynik porównań równości w interfejsie API SQL między dwoma dowolnymi typami JSON.

| **Operator** | **Niezdefiniowane** | **Null** | **Wartość logiczna** | **Liczba** | **Ciąg** | **Obiekt** | **Tablica** |
|---|---|---|---|---|---|---|---|
| **Niezdefiniowane** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Null** | Niezdefiniowane | **OK** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Wartość logiczna** | Niezdefiniowane | Niezdefiniowane | **OK** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Liczba** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **OK** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Ciąg** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **OK** | Niezdefiniowane | Niezdefiniowane |
| **Obiekt** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **OK** | Niezdefiniowane |
| **Tablica** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **OK** |

Dla operatorów porównania, takie jak `>`, `>=`, `!=`, `<`, i `<=`, porównanie różnych typów lub między dwoma obiektami lub tablic generuje `Undefined`.  

Jeśli jest wynikiem wyrażenia skalarne `Undefined`, element nie jest uwzględniony w wyniku, ponieważ `Undefined` nie równa się `true`.

### <a name="logical-and-or-and-not-operators"></a>Operatory logiczne (AND, OR i NOT)

Operatory logiczne działają na wartościach logicznych. W poniższych tabelach przedstawiono tabel prawdziwych danych logicznych dla tych operatorów:

**Operator OR**

| LUB | True | False | Niezdefiniowane |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Niezdefiniowane |
| Niezdefiniowane |True |Niezdefiniowane |Niezdefiniowane |

**Operator AND**

| AND | True | False | Niezdefiniowane |
| --- | --- | --- | --- |
| True |True |False |Niezdefiniowane |
| False |False |False |False |
| Niezdefiniowane |Niezdefiniowane |False |Niezdefiniowane |

**Operator NOT**

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Niezdefiniowane |Niezdefiniowane |

## <a name="between-keyword"></a>Słowo kluczowe BETWEEN

Jak ANSI SQL można użyć BETWEEN — słowo kluczowe wyrażenia zapytań dotyczących zakresów ciąg lub wartości liczbowych. Na przykład następujące zapytanie zwraca wszystkie elementy, w których klasa pierwszy element podrzędny jest 1-5, włącznie.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

W odróżnieniu od w ANSI SQL umożliwia także klauzuli BETWEEN w klauzuli FROM, jak w poniższym przykładzie.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

W interfejsie API SQL, w przeciwieństwie do ANSI SQL można wyrazić zakres zapytań dotyczących właściwości mieszane typy. Na przykład `grade` mogą być podobne do numeru `5` niektóre elementy i parametry, takie jak `grade4` w innych. W takich sytuacjach jak JavaScript, wynikiem porównania między dwoma różnymi typami `Undefined`, dzięki czemu element jest pomijany.

> [!TIP]
> Celu skrócenia czasu wykonywania zapytania należy utworzyć zasady indeksowania, korzystającej z typu indeks zakresu względem dowolnej właściwości liczbowych lub ścieżki, które filtruje klauzuli BETWEEN.

## <a name="in-keyword"></a>Słowo kluczowe IN

Użyj słowa kluczowego w celu sprawdzenia, czy określona wartość pasuje do dowolnej wartości na liście. Na przykład, następujące zapytanie zwraca wszystkie rodziny elementy gdzie `id` jest `WakefieldFamily` lub `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Poniższy przykład zwraca wszystkie elementy, których stan to dowolnego z określonymi wartościami:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="-operator"></a>* — operator

Operator specjalny * projektów całego elementu, ponieważ jest. Jeśli go używasz, musi być to jedyne pole w projekcji. Zapytanie, takich jak `SELECT * FROM Families f` jest prawidłowy, ale `SELECT VALUE * FROM Families f` i `SELECT *, f.id FROM Families f` są nieprawidłowe. [Najpierw kwerendy w tym artykule](#query-the-json-items) używane * — operator. 

## <a name="-and--operators"></a>? i? Operatory

Możesz użyć Ternary (?) i operatory (?), aby zbudować wyrażenia warunkowego, tak jak w językach programowania, jak Coalesce C# i JavaScript. 

Można użyć? Operator do tworzenia nowych właściwości kodu JSON na bieżąco. Na przykład, następujące zapytanie klasyfikuje poziomy klasy korporacyjnej do `elementary` lub `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Można także zagnieżdżać wywołania? operator, tak jak w następującej kwerendy: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Podobnie jak w przypadku innych operatorów zapytania? operator nie obejmuje elementy, czy brakuje odwołania właściwości różnią się typami, którą jest porównywany.

Użyj? operator, który ma być efektywnie Sprawdź właściwości w elemencie podczas wykonywania zapytań dotyczących danych z częściową strukturą lub typu mieszanego. Na przykład, następujące zapytanie zwraca `lastName` , jeśli jest dostępna lub `surname` Jeśli `lastName` nie będą dostępne.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="TopKeyword"></a>TOP operator

GÓRNY — słowo kluczowe zwraca pierwszy `N` liczba wyników zapytania w kolejności niezdefiniowane. Najlepszym rozwiązaniem, jak za pomocą GÓRNEJ klauzuli ORDER BY ograniczyć wyniki do pierwszego `N` liczba uporządkowane wartości. Połączenie tych dwóch klauzul jest jedynym sposobem, aby przewidywalnie wskazują wiersze, które wpływa na GÓRNYM. 

Korzystać z GÓRNEGO, z wartością stałą, jak w poniższym przykładzie, lub z wartością zmiennej za pomocą sparametryzowanych zapytań. Aby uzyskać więcej informacji, zobacz [sparametryzowanych zapytań](#parameterized-queries) sekcji.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Wyniki są:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a id="OrderByClause"></a>Klauzula ORDER BY

Jak ANSI SQL można dołączyć opcjonalny klauzuli ORDER BY w zapytaniach. Opcjonalny argument ASC lub DESC Określa, czy można pobrać wyniki w kolejności rosnącej lub malejącej. ASC jest ustawieniem domyślnym.

Na przykład w tym miejscu jest zapytanie, które pobiera rodzin rosnąco nazwę miejscowości rezydentnego:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Wyniki są:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Następujące zapytanie pobiera rodziny `id`s w kolejności ich Data utworzenia elementu. Element `creationDate` jest liczbą oznaczającą *czasie uniksowym*, lub czas, który upłynął od 1 stycznia 1970 r. w ciągu kilku sekund.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Wyniki są:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```
## <a name="scalar-expressions"></a>Wyrażenia skalarne

Klauzula SELECT obsługuje wyrażenia skalarne, takie jak wyrażenia arytmetyczne, stałe i wyrażeń logicznych. Wyrażenie skalarne, które korzysta z następującego zapytania:


```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Wyniki są:

```json
    [{
      "$1": 1.33333
    }]
```

W następującym zapytaniu wynik wyrażenia skalarne jest wartością logiczną:


```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Wyniki są:

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="object-and-array-creation"></a>Tworzenie obiektu i tablicy

Kluczową funkcją interfejsu API SQL jest tworzenie tablicy i obiektów. Poprzedni przykład utworzył nowy obiekt JSON, `AreFromSameCityState`. Można również utworzyć tablic, jak pokazano w poniższym przykładzie:


```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Wyniki są:

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```


## <a id="Iteration"></a>Iteracja

Interfejs API SQL zapewnia obsługę Iterowanie tablice notacji JSON z nową konstrukcję dodane za pośrednictwem słowa kluczowego w w źródle FROM. W poniższym przykładzie:

```sql
    SELECT *
    FROM Families.children
```

Wyniki są:

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Następne zapytanie wykonuje iterację przez `children` w `Families` kontenera. Tablica dane wyjściowe różni się od poprzedniego zapytania. Ten przykład dzieli `children`i spłaszcza wyniki do jednej tablicy:  

```sql
    SELECT *
    FROM c IN Families.children
```

Wyniki są:

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

Można filtrować dalsze na każdy pojedynczy wpis tablicy, jak pokazano w poniższym przykładzie:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Wyniki są:

```json
    [{
      "givenName": "Lisa"
    }]
```

Możesz także agregować za pośrednictwem wyniku iterację tablicy. Na przykład następujące zapytanie zlicza liczbę elementów podrzędnych wśród wszystkich rodzin:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Wyniki są:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a id="Joins"></a>Sprzężenia

W relacyjnej bazie danych sprzężenia między tabelami są logiczne następstwem do projektowania znormalizowaną schematów. Z kolei interfejsu API SQL korzysta z modelu dane denormalizowane elementów bez schematu, który jest logicznym odpowiednikiem *samosprzężenie*.

Język obsługuje składnię `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Ta kwerenda zwraca zestaw spójnych kolekcji zawierający `N` wartości. Każda krotka ma wartości utworzone w procesie iterowania wszystkich aliasów kontenera w odpowiednich zestawach. Innymi słowy to zapytanie wykonuje pełny iloczyn wektorowy zestawów uczestniczących w sprzężeniu.

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

## <a id="UserDefinedFunctions"></a>Funkcje zdefiniowane przez użytkownika (UDF)

Interfejs API SQL zapewnia obsługę funkcji zdefiniowanych przez użytkownika (UDF). Funkcje skalarne zdefiniowane przez użytkownika możesz przekazać argumenty, zero lub wiele i zwrócenia wyniku pojedynczy argument. Interfejs API sprawdza każdy argument jest prawną wartości JSON.  

Interfejs API rozszerza składni SQL do obsługi niestandardowej logiki aplikacji za pomocą funkcji zdefiniowanych przez użytkownika. Możesz zarejestrować funkcje zdefiniowane przez użytkownika za pomocą interfejsu API SQL i odwoływać się do nich zapytań SQL. Tak naprawdę funkcje zdefiniowane przez użytkownika są w sposób zaawansowany projektowane na potrzeby wywoływania z zapytań. Jako następstwem funkcje zdefiniowane przez użytkownika nie mają dostępu do obiektu context, podobnie jak inne typy języka JavaScript, takie jak procedur składowanych i wyzwalaczy. Zapytania są przeznaczone tylko do odczytu i można uruchomić na repliki podstawowej lub dodatkowej. Funkcje zdefiniowane przez użytkownika, w przeciwieństwie do innych typów języka JavaScript są zaprojektowane do uruchamiania w replikach pomocniczych.

Poniższy przykład rejestruje funkcji zdefiniowanej przez użytkownika w ramach kontenera elementu w bazie danych Cosmos DB. W przykładzie jest tworzony funkcji zdefiniowanej przez użytkownika o nazwie `REGEX_MATCH`. Akceptuje dwóch wartości ciągu JSON `input` i `pattern`, i sprawdza, czy pierwszego dopasowania wzorca określonego w drugi przy użyciu języka JavaScript `string.match()` funkcji.

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

## <a id="Aggregates"></a>Funkcje agregujące

Funkcje agregujące wykonywanie obliczeń na zestaw wartości w klauzuli SELECT i zwracać pojedynczą wartość. Na przykład, następujące zapytanie zwraca liczbę elementów w obrębie `Families` kontenera:

```sql
    SELECT COUNT(1)
    FROM Families f
```

Wyniki są:

```json
    [{
        "$1": 2
    }]
```

Może również zwracać wartość skalarna agregacji za pomocą słowa kluczowego wartość. Na przykład poniższe zapytanie zwraca liczbę wartości jako pojedynczą liczbę:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Wyniki są:

```json
    [ 2 ]
```

Można także połączyć agregacji za pomocą filtrów. Na przykład, następujące zapytanie zwraca liczbę elementów, stan adresu `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Wyniki są:

```json
    [ 1 ]
```

Interfejs API SQL obsługuje następujące funkcje agregujące. Suma i średnia działają na wartości liczbowe i COUNT, MIN i MAX pracować na liczby, ciągi, wartości logicznych i wartości null.

| Funkcja | Opis |
|-------|-------------|
| COUNT | Zwraca liczbę elementów w wyrażeniu. |
| SUM   | Zwraca sumę wszystkich wartości w wyrażeniu. |
| MIN   | Zwraca minimalną wartość w wyrażeniu. |
| MAX   | Zwraca maksymalną wartość w wyrażeniu. |
| AVG   | Zwraca średnią wartości w wyrażeniu. |

Możesz także agregować wynikami iterację tablicy. Aby uzyskać więcej informacji, zobacz [iteracji](#Iteration) sekcji.

> [!NOTE]
> W Eksploratorze danych w witrynie Azure portal zapytania agregacji mogą zostać zagregowane wyniki częściowe za pośrednictwem strony tylko jedno zapytanie. Zestaw SDK tworzy pojedynczej wartości zbiorczej na wszystkich stronach. Do wykonywania zapytań agregacji, przy użyciu kodu, potrzebny jest zestaw SDK platformy .NET 1.12.0, zestaw SDK platformy .NET Core 1.1.0 lub zestawu Java SDK 1.9.5 lub nowszej.
>

## <a id="BuiltinFunctions"></a>Funkcje wbudowane

Usługa cosmos DB obsługuje także szereg wbudowanych funkcji dla typowych operacji, których można używać wewnątrz kwerend, takich jak funkcje zdefiniowane przez użytkownika (UDF).

| Grupa funkcji | Operacje |
|---------|----------|
| Funkcje matematyczne | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN, TAN |
| Sprawdzanie typu funkcji | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Funkcje ciągów | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING, UPPER |
| Funkcje tablicy | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH, ARRAY_SLICE |
| Funkcje przestrzenne | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Jeśli aktualnie używasz funkcji zdefiniowanej przez użytkownika (UDF) dla której wbudowanej funkcji jest teraz dostępna, odpowiednie wbudowanej funkcji będzie szybsze uruchamianie i bardziej wydajne.

Główna różnica między ANSI SQL i funkcje usługi Cosmos DB jest, funkcje usługi Cosmos DB są zaprojektowane do dobrze pracować ze schematów i mieszanego schematu danych. Na przykład, jeśli właściwość jest nieobecna lub ma wartość nieliczbowych, takich jak `unknown`, element jest pomijany, zamiast zwracać błąd.

### <a name="mathematical-functions"></a>Funkcje matematyczne

Każda z funkcji matematycznych wykonuje obliczenie na podstawie wartości wejściowych, które są podawane jako argumenty, i zwraca wartość liczbową. Poniżej znajduje się tabela zawierająca obsługiwane wbudowane funkcje matematyczne.

| Sposób użycia | Opis |
|----------|--------|
| ABS (num_expr) | Zwraca wartość bezwzględną (dodatnią) podanego wyrażenia liczbowego. |
| CEILING (wyrażenie_liczbowe) | Zwraca najmniejszą wartość całkowitą równą określonemu wyrażeniu liczbowemu lub większą. |
| FLOOR (wyrażenie_liczbowe) | Zwraca największą wartość całkowitą równą określonemu wyrażeniu liczbowemu lub mniejszą. |
| EXP (wyrażenie_liczbowe) | Zwraca wykładnik określonego wyrażenia liczbowego. |
| Dziennik (num_expr, podstawowy) | Zwraca logarytm naturalny określone wyrażenie liczbowe lub logarytm przy użyciu określonej podstawie. |
| LOG10 (wyrażenie_liczbowe) | Zwraca wartość logarytmiczną przy podstawie 10 określonego wyrażenia liczbowego. |
| ROUND (wyrażenie_liczbowe) | Zwraca wartość liczbową zaokrągloną do najbliższej wartości całkowitej. |
| TRUNC (wyrażenie_liczbowe) | Zwraca wartość liczbową przyciętą do najbliższej wartości całkowitej. |
| SQRT (wyrażenie_liczbowe) | Zwraca pierwiastek kwadratowy określonego wyrażenia liczbowego. |
| SQUARE (wyrażenie_liczbowe) | Zwraca kwadrat określonego wyrażenia liczbowego. |
| ZASILANIA (num_expr, num_expr) | Zwraca wartość określonego wyrażenia liczbowego podniesioną do podanej potęgi. |
| SIGN (wyrażenie_liczbowe) | Zwraca wartość znaku (-1, 0, 1) określonego wyrażenia liczbowego. |
| ACOS (wyrażenie_liczbowe) | Zwraca kąt w radianach, którego cosinus jest określonym wyrażeniem liczbowym. Ta funkcja jest również nazywana arcus cosinus. |
| ASIN (wyrażenie_liczbowe) | Zwraca kąt w radianach, którego sinus jest określonym wyrażeniem liczbowym. Ta funkcja jest również nazywana arcus sinus. |
| ATAN (wyrażenie_liczbowe) | Zwraca kąt w radianach, którego tangens jest określonym wyrażeniem liczbowym. Ta funkcja jest również nazywany tangens. |
| ATN2 (wyrażenie_liczbowe) | Zwraca kąt w radianach, między dodatnią osią x i promieniem z początku do punktu (y, x), gdzie x i y to wartości dwóch określonych wyrażeń zmiennoprzecinkowych. |
| COS (wyrażenie_liczbowe) | Zwraca trygonometryczny cosinus określonego kąta w radianach w określonym wyrażeniu. |
| COT (wyrażenie_liczbowe) | Zwraca trygonometryczny cotangens określonego kąta w radianach w określonym wyrażeniu liczbowym. |
| DEGREES (wyrażenie_liczbowe) | Zwraca kąt w stopniach odpowiadający kątowi określonemu w radianach. |
| PI () | Zwraca stałą wartość liczby PI. |
| RADIANS (wyrażenie_liczbowe) | Zwraca wartość w radianach po wprowadzeniu wyrażenia liczbowego w stopniach. |
| SIN (wyrażenie_liczbowe) | Zwraca trygonometryczny sinus określonego kąta w radianach w określonym wyrażeniu. |
| TAN (wyrażenie_liczbowe) | Zwraca tangens wyrażenia wejściowego w określonym wyrażeniu. |

Można uruchomić zapytania, podobnie jak w poniższym przykładzie:

```sql
    SELECT VALUE ABS(-4)
```

Wynik jest:

```json
    [4]
```

### <a name="type-checking-functions"></a>Sprawdzanie typu funkcji

Funkcje sprawdzania typu pozwalają sprawdzić typ wyrażenia w ramach zapytania SQL. Kontrola typów w funkcji służy do określania typów właściwości elementów na bieżąco, kiedy są one zmienna lub nieznany. W tym miejscu znajduje się tabela obsługiwanych wbudowanych funkcji sprawdzania typów:

| **Użycie** | **Opis** |
|-----------|------------|
| [IS_ARRAY (wyrażenie)](sql-api-query-reference.md#bk_is_array) | Zwraca wartość logiczną wskazującą, czy typ wartości jest tablicą. |
| [IS_BOOL (wyrażenie)](sql-api-query-reference.md#bk_is_bool) | Zwraca wartość logiczną wskazującą, czy typ wartości jest wartością logiczną. |
| [IS_NULL (wyrażenie)](sql-api-query-reference.md#bk_is_null) | Zwraca wartość logiczną wskazującą, czy typ wartości jest wartością null. |
| [IS_NUMBER (wyrażenie)](sql-api-query-reference.md#bk_is_number) | Zwraca wartość logiczną wskazującą, czy typ wartości jest liczbą. |
| [IS_OBJECT (wyrażenie)](sql-api-query-reference.md#bk_is_object) | Zwraca wartość logiczną wskazującą, czy typ wartości jest obiektem JSON. |
| [IS_STRING (wyrażenie)](sql-api-query-reference.md#bk_is_string) | Zwraca wartość logiczną wskazującą, czy typ wartości jest ciągiem. |
| [IS_DEFINED (wyrażenie)](sql-api-query-reference.md#bk_is_defined) | Zwraca wartość logiczną wskazującą, do właściwości przypisano wartość. |
| [IS_PRIMITIVE (wyrażenie)](sql-api-query-reference.md#bk_is_primitive) | Zwraca wartość Boolean wskazującą, czy typ wartości ciągu, liczba, wartość logiczna lub o wartości null. |

Korzystanie z tych funkcji, można uruchomić zapytania, podobnie jak w poniższym przykładzie:

```sql
    SELECT VALUE IS_NUMBER(-4)
```

Wynik jest:

```json
    [true]
```

### <a name="string-functions"></a>Funkcje ciągów

Następujące funkcje skalarne wykonania operacji na wartość ciągu wejściowego i zwraca ciąg, wartość liczbowa lub Boolean. Poniżej znajduje się tabela wbudowanych funkcji ciągów:

| Sposób użycia | Opis |
| --- | --- |
| [LENGTH (wyrażenie_ciągu)](sql-api-query-reference.md#bk_length) | Zwraca liczbę znaków z określonego wyrażenia ciągu. |
| [CONCAT (wyrażenie_ciągu, wyrażenie_ciągu [, wyrażenie_ciągu])](sql-api-query-reference.md#bk_concat) | Zwraca ciąg, który jest wynikiem połączenia co najmniej dwóch wartości ciągu. |
| [SUBSTRING (wyrażenie_ciągu, wyrażenie_liczbowe, wyrażenie_liczbowe)](sql-api-query-reference.md#bk_substring) | Zwraca część wyrażenia ciągu. |
| [STARTSWITH (wyrażenie_ciągu, wyrażenie_ciągu)](sql-api-query-reference.md#bk_startswith) | Zwraca wartość logiczną wskazującą, czy pierwszy ciąg wyrażenia rozpoczyna się od drugiego. |
| [ENDSWITH (wyrażenie_ciągu, wyrażenie_ciągu)](sql-api-query-reference.md#bk_endswith) | Zwraca wartość logiczną wskazującą, czy pierwszy ciąg wyrażenia kończy się na drugi. |
| [CONTAINS (wyrażenie_ciągu, wyrażenie_ciągu)](sql-api-query-reference.md#bk_contains) | Zwraca wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu zawiera drugie. |
| [INDEX_OF (wyrażenie_ciągu, wyrażenie_ciągu)](sql-api-query-reference.md#bk_index_of) | Zwraca pozycję początkową pierwsze wystąpienie ciągu drugiego ciągu wyrażenia w ramach pierwszego określonego wyrażenia ciągu lub wartość -1, jeśli nie odnaleziono ciągu. |
| [LEFT (wyrażenie_ciągu, wyrażenie_liczbowe)](sql-api-query-reference.md#bk_left) | Zwraca lewą część ciągu z określoną liczbą znaków. |
| [RIGHT (wyrażenie_ciągu, wyrażenie_liczbowe)](sql-api-query-reference.md#bk_right) | Zwraca prawą część ciągu z określoną liczbą znaków. |
| [LTRIM (wyrażenie_ciągu)](sql-api-query-reference.md#bk_ltrim) | Zwraca wyrażenie ciągu po usunięciu spacji wiodących. |
| [RTRIM (wyrażenie_ciągu)](sql-api-query-reference.md#bk_rtrim) | Zwraca wyrażenie ciągu po przycięciu wszystkich spacji końcowych. |
| [LOWER (wyrażenie_ciągu)](sql-api-query-reference.md#bk_lower) | Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych wielkich liter na małe litery. |
| [UPPER (wyrażenie_ciągu)](sql-api-query-reference.md#bk_upper) | Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych małych liter na wielkie litery. |
| [REPLACE (wyrażenie_ciągu,wyrażenie_ciągu, wyrażenie_ciągu)](sql-api-query-reference.md#bk_replace) | Zamienia wszystkie wystąpienia określonej wartości ciągu na inną wartość ciągu. |
| [REPLICATE (wyrażenie_ciągu, wyrażenie_liczbowe)](sql-api-query-reference.md#bk_replicate) | Powtarza wartość ciągu określoną liczbę razy. |
| [REVERSE (wyrażenie_ciągu)](sql-api-query-reference.md#bk_reverse) | Zwraca wartość ciągu w odwrotnej kolejności. |

Korzystanie z tych funkcji, można uruchomić zapytania, podobnie do poniższego, która zwraca rodziny `id` pisane wielkimi literami:

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

Wyniki są:

```json
    [
        "WAKEFIELDFAMILY",
        "ANDERSENFAMILY"
    ]
```

Lub łączenia ciągów, np. w tym przykładzie:

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

Wyniki są:

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "Seattle,WA"
    }]
```

Można również użyć funkcji ciągów w klauzuli WHERE, aby filtrować wyniki, takie jak w poniższym przykładzie:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

Wyniki są:

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>Funkcje tablicy

Następujące funkcje skalarne wykonania operacji na wartości wejściowej tablicy i zwraca liczbową, logiczną lub wartość tablicy. Poniżej znajduje się tabela wbudowanych funkcji tablicy:

| Sposób użycia | Opis |
| --- | --- |
| [ARRAY_LENGTH (wyrażenie_tablicy)](sql-api-query-reference.md#bk_array_length) |Zwraca liczbę elementów określonego wyrażenia tablicy. |
| [ARRAY_CONCAT (wyrażenie_tablicy, wyrażenie_tablicy [, wyrażenie_tablicy])](sql-api-query-reference.md#bk_array_concat) |Zwraca tablicę, która jest wynikiem połączenia co najmniej dwóch wartości tablicy. |
| [ARRAY_CONTAINS (wyrażenie_tablicy, wyrażenie [, wyrażenie_warunkowe])](sql-api-query-reference.md#bk_array_contains) |Zwraca wartość logiczną wskazującą, czy tablica zawiera określoną wartość. Można określić, czy dopasowanie jest pełne czy lub częściowe. |
| [ARRAY_SLICE (wyrażenie_ciągu, wyrażenie_liczbowe [, wyrażenie_liczbowe])](sql-api-query-reference.md#bk_array_slice) |Zwraca część wyrażenia tablicy. |

Funkcje tablicy należy używać do manipulowania tablicami w formacie JSON. Na przykład, w tym miejscu jest zapytanie, które zwraca wszystkie elementy `id`s w przypadku, gdy jeden z `parents` jest `Robin Wakefield`: 

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

Wynik jest:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Można określić częściowy fragment na potrzeby pasujących elementów w tablicy. Następujące zapytanie znajdzie wszystkie elementy `id`s, który ma `parents` z `givenName` z `Robin`:

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

Wynik jest:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Oto inny przykład, który używa ARRAY_LENGTH w celu uzyskania liczby `children` na rodzinę:

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

Wyniki są:

```json
    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]
```

### <a name="spatial-functions"></a>Funkcje przestrzenne

Usługa cosmos DB obsługuje następujące funkcje wbudowane Otwórz dane geograficzne Consortium (OGC) dla danych geoprzestrzennych zapytań: 

| Sposób użycia | Opis |
| --- | --- |
| ST_DISTANCE (wyrażenie_punktu, wyrażenie_punktu) | Zwraca odległość między dwoma GeoJSON `Point`, `Polygon`, lub `LineString` wyrażenia. |
| T_WITHIN (wyrażenie_punktu, wyrażenie_wielokąta) | Zwraca wyrażenie logiczne wskazującą czy pierwszy obiekt GeoJSON (`Point`, `Polygon`, lub `LineString`) znajduje się w drugim obiektu GeoJSON (`Point`, `Polygon`, lub `LineString`). |
| ST_INTERSECTS (wyrażenie_przestrzenne, wyrażenie_przestrzenne) | Zwraca wartość wskazującą, czy dwa określone obiekty GeoJSON wyrażenie logiczne (`Point`, `Polygon`, lub `LineString`) intersect. |
| ST_ISVALID | Zwraca wartość Boolean wskazującą czy określonego GeoJSON `Point`, `Polygon`, lub `LineString` wyrażenie jest prawidłowe. |
| ST_ISVALIDDETAILED | Zwraca wartość JSON, zawierająca wartość logiczną, jeśli określony GeoJSON `Point`, `Polygon`, lub `LineString` wyrażenie jest prawidłowe i jeśli jest to nieprawidłowa Przyczyna jako wartość ciągu. |

Funkcje przestrzenne służy do wykonywania zapytań dotyczących odległości dla danych przestrzennych. Na przykład w tym miejscu jest zapytanie, które zwraca wszystkie rodziny elementy, które są w ciągu 30 km określonej lokalizacji za pomocą wbudowanych funkcji ST_DISTANCE:

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

Wynik jest:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Aby uzyskać więcej informacji na temat obsługi danych geoprzestrzennych, zobacz [Working with geospatial data in Azure Cosmos DB (Praca z danymi geoprzestrzennymi w usłudze Azure Cosmos DB)](geospatial.md). 

## <a name="parameterized-queries"></a>Zapytania sparametryzowane

Usługa cosmos DB obsługuje zapytania z parametrami wyrażone w znanej @ notacji. SQL — sparametryzowane zapewnia niezawodne obsługi i anulowania zapewnianego element danych wejściowych użytkownika i uniemożliwia przypadkowe ujawnienie danych przez wstrzyknięcie kodu SQL.

Na przykład można napisać zapytanie pobierające `lastName` i `address.state` jako parametry i wykonaj go dla różnych wartości `lastName` i `address.state` na podstawie danych wejściowych użytkownika.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Następnie możesz wysłać żądanie do usługi Cosmos DB jako sparametryzowanych zapytań JSON, jak pokazano poniżej:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

W poniższym przykładzie ustawiono argument TOP za pomocą sparametryzowanych zapytań: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Wartości parametru może być dowolnym prawidłowym kodem JSON: ciągi, liczby, wartości logicznych ma wartość null, nawet wtedy, tablic lub są zagnieżdżone JSON. Ponieważ usługi Cosmos DB korzysta ze schematów, parametry nie są weryfikowany pod kątem dowolnego typu.

## <a id="JavaScriptIntegration"></a>Integracja z językiem JavaScript

Usługa Azure Cosmos DB zapewnia model programowania do wykonywania logiki aplikacji JavaScript bezpośrednio w kontenerach, przy użyciu procedur składowanych i wyzwalaczy. Ten model obsługuje:

* Wykonywanie operacji CRUD transakcji o wysokiej wydajności i zapytania dotyczące elementów w kontenerze, na podstawie głęboka Integracja środowiska uruchomieniowego JavaScript w ramach aparatu bazy danych.
* Fizyczne modelowanie przepływu sterowania, zmienna zakresu, przypisania i integracji programów obsługi wyjątków w nim elementów podstawowych z transakcjami bazy danych. 

Aby uzyskać więcej informacji na temat integracji usługi Azure Cosmos DB JavaScript, zobacz [interfejsu API języka JavaScript po stronie serwera](#JavaScriptServerSideApi) sekcji.

### <a name="operator-evaluation"></a>Ocena operatora

Usługa cosmos DB, bycia bazy danych JSON, rysuje równoleżników przy użyciu operatorów w języku JavaScript i semantyka oceny. Usługa cosmos DB próbuje zachować semantykę JavaScript pod względem obsługi formatu JSON, ale ocena operacji odbiega w pewnych okolicznościach.

W interfejsie API SQL w przeciwieństwie do tradycyjnych SQL typów wartości często nie są znane aż interfejs API pobiera wartości z bazy danych. Aby efektywnie wykonywać zapytania, większość operatorów mają ścisłe wymagania dotyczące typu.

W przeciwieństwie do języka JavaScript i interfejsu API SQL nie wykonuje konwersje niejawne. Na przykład zapytanie takie jak `SELECT * FROM Person p WHERE p.Age = 21` dopasowuje elementy, które zawierają `Age` właściwości, których wartość jest `21`. Nie odpowiada żadnej innych elementów, których `Age` właściwość odpowiada prawdopodobnie nieskończone odmiany takich jak `twenty-one`, `021`, lub `21.0`. To zachowanie różni się od języka JavaScript, gdzie wartości ciągu są niejawnie rzutowany na liczby, na podstawie operatora, na przykład: `==`. To zachowanie interfejsu API SQL ma kluczowe znaczenie dla efektywnego indeksu dopasowania.

## <a id="ExecutingSqlQueries"></a>Wykonywanie zapytania SQL

Dowolnego języka realizującego żądania HTTP/HTTPS można wywołać interfejsu API REST usługi DB Cosmos. Usługa cosmos DB oferuje biblioteki programistyczne dla języków programowania .NET, Node.js, JavaScript i Python. Interfejs API REST i biblioteki obsługują wykonywanie zapytań za pomocą języka SQL i zestawu .NET SDK obsługuje również [zapytań LINQ](#Linq).

Poniższe przykłady pokazują, jak utworzyć zapytanie i przesłać je do konta bazy danych Cosmos DB.

### <a id="RestAPI"></a>Interfejs API REST

Usługa Cosmos DB oferuje otwarty model programowania RESTful używający protokołu HTTP. Model zasobów zawiera zestaw zasobów w ramach konta bazy danych, która aprowizuje subskrypcji platformy Azure. Konto bazy danych składa się z szeregu *baz danych*, z których każdy może zawierać więcej niż jednego *kontenery*, który z kolei zawiera *elementów*, funkcje zdefiniowane przez użytkownika i innych typów zasobów. Każdy zasób usługi Cosmos DB to adresy logicznych i stabilnych identyfikatora URI. Zestaw zasobów jest nazywany *kanału informacyjnego*. 

Model interakcji podstawowa przy użyciu tych zasobów jest za pomocą polecenia HTTP `GET`, `PUT`, `POST`, i `DELETE`, przy użyciu ich standardowego interpretacji. Użyj `POST` do utworzenia nowego zasobu, wykonaj procedurę składowaną lub wystawiania zapytanie usługi Cosmos DB. Zapytania są zawsze operacjami tylko do odczytu bez efektów ubocznych.

W poniższych przykładach pokazano `POST` dla elementów przykładowe zapytanie do interfejsu API SQL. Zapytanie zawiera proste filtr w formacie JSON `name` właściwości. `x-ms-documentdb-isquery` I Content-Type: `application/query+json` nagłówki Określa, czy operacja się zapytania. Zastąp `mysqlapicosmosdb.documents.azure.com:443` za pomocą identyfikatora URI dla konta usługi Cosmos DB.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Wyniki są:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

Dalej, bardziej skomplikowanych zwraca wiele wyników z sprzężenia:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": [] 
    }
```

Wyniki są: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Jeśli wyniki zapytania nie mieści się w jednej strony, interfejs API REST zwraca token kontynuacji za pośrednictwem `x-ms-continuation-token` nagłówka odpowiedzi. Klienci mogą stronicowanie wyników, łącznie z nagłówkiem w kolejnych wyników. Można także kontrolować liczbę wyników na stronę za pośrednictwem `x-ms-max-item-count` numer nagłówka. 

Jeśli zapytanie zawiera funkcję agregacji, takich jak liczba, na stronie zapytanie może zwracać częściowo zagregowaną wartość w tylko jednej stronie wyników. Klienci, należy wykonać agregacji drugiego poziomu, za pośrednictwem tych wyników, aby wygenerować wyniki końcowe. Na przykład Suma za pośrednictwem liczby zwracanych w poszczególnych stron, aby zwrócić łączna liczba.

Aby zarządzać zasadami spójności danych dla zapytania, należy użyć `x-ms-consistency-level` nagłówka, tak jak wszystkie żądania interfejsu API REST. Spójność sesji wymaga również wyświetlania najnowsze `x-ms-session-token` nagłówek cookie żądania zapytania. Zasady indeksowania kontenera uwzględnionego w zapytaniu mogą również wpływać na spójność wyników zapytania. Za pomocą domyślne ustawienia zasad dla kontenerów indeksowania, indeks jest zawsze aktualny z zawartością elementu, a wyniki zapytania pasuje spójności dla danych. Aby uzyskać więcej informacji, zobacz [poziomów spójności w usłudze Azure Cosmos DB][consistency-levels].

Jeśli skonfigurowane zasady indeksowania w kontenerze nie obsługuje określonego zapytania, serwer usługi Azure Cosmos DB zwraca 400 "złe żądanie". Ten komunikat o błędzie zwraca zapytania ze ścieżkami jawnie wykluczone z indeksowania. Można określić `x-ms-documentdb-query-enable-scan` nagłówka, aby zezwolić na zapytanie, aby przeprowadzić skanowanie, gdy indeks jest niedostępny.

Możesz uzyskać szczegółowe metryki na wykonanie zapytania, ustawiając `x-ms-documentdb-populatequerymetrics` nagłówka do `true`. Aby uzyskać więcej informacji, zobacz [SQL query metrics for Azure Cosmos DB (Metryki zapytań języka SQL dla usługi Azure Cosmos DB)](sql-api-query-metrics.md).

### <a id="DotNetSdk"></a>C#(ZESTAW SDK PLATFORMY .NET)

Zestaw .NET SDK obsługuje wykonywanie zapytań LINQ i SQL. Poniższy przykład pokazuje, jak wykonać poprzednie zapytanie filtru przy użyciu platformy .NET:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Poniższy przykład porównanie dwóch właściwości pod kątem równości w ramach każdego elementu i wykorzystuje anonimowy projekcji.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

W kolejnym przykładzie pokazano sprzężenia, wyrażone za pomocą LINQ `SelectMany`.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

Klient modelu .NET automatycznie wykonuje iterację przez wszystkie strony wyników zapytania w `foreach` blokuje, jak pokazano w powyższym przykładzie. Opcje zapytania wprowadzona w [interfejsu API REST](#RestAPI) sekcji są również dostępne w zestawie SDK platformy .NET przy użyciu `FeedOptions` i `FeedResponse` klas w `CreateDocumentQuery` metody. Liczbę stron, które można kontrolować za pomocą `MaxItemCount` ustawienie.

Można także jawnie kontrolować stronicowania, tworząc `IDocumentQueryable` przy użyciu `IQueryable` obiektu, a następnie, czytając `ResponseContinuationToken` wartości i przekazywania ich z powrotem jako `RequestContinuationToken` w `FeedOptions`. Możesz ustawić `EnableScanInQuery` Aby włączyć skanowanie, gdy zapytanie nie jest obsługiwany przez skonfigurowane zasady indeksowania. W przypadku partycjonowanego kontenerów, można użyć `PartitionKey` uruchamiać zapytanie jednej partycji, mimo że usługę Azure Cosmos DB można automatycznie prowadzenie to tekst zapytania. Możesz użyć `EnableCrossPartitionQuery` uruchamiać zapytania wiele partycji.

Aby uzyskać więcej przykładów .NET za pomocą zapytań, zobacz [przykładów usługi Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet) w usłudze GitHub.

### <a id="JavaScriptServerSideApi"></a>Interfejs API języka JavaScript po stronie serwera

Usługa cosmos DB zapewnia model programowania do wykonywania logiki aplikacji JavaScript na podstawie bezpośrednio w kontenerach, przy użyciu procedur składowanych i wyzwalaczy. Logika JavaScript zarejestrowany na poziomie kontenera następnie mogą wyzwalać operacje bazy danych w elementach danego kontenera w transakcje ACID otoczenia.

Poniższy przykład pokazuje, jak używać `queryDocuments` na serwerze JavaScript API tworzenie kwerend z wewnątrz procedur składowanych i wyzwalaczy:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="Linq"></a>Interfejs API LINQ to SQL

LINQ to model programowania .NET i wyraża obliczenie jako kwerendy dla strumieni obiektu. Usługa Cosmos DB udostępnia w interfejsie bibliotekę po stronie klienta za pomocą modelu LINQ, ułatwiając konwersję między obiektami JSON i .NET oraz mapowanie z podzestawu zapytań LINQ do zapytań usługi Cosmos DB.

Na poniższym diagramie przedstawiono architekturę obsługi zapytań LINQ, za pomocą usługi Cosmos DB. Za pomocą klienta usługi Cosmos DB, możesz utworzyć `IQueryable` obiekt, który bezpośrednio odpytuje dostawcę zapytanie usługi Cosmos DB i wykonuje translację zapytanie LINQ na zapytanie usługi Cosmos DB. Zapytanie jest następnie przekazać do serwera usługi Cosmos DB, który pobiera zestaw wyników w formacie JSON. Deserializator JSON konwertuje wyniki do strumienia obiekty .NET po stronie klienta.

![Architektura pomocniczych zapytań LINQ używających interfejsu API SQL — składnia języka SQL, język zapytań JSON, pojęcia dotyczące baz danych oraz zapytania SQL][1]

### <a name="net-and-json-mapping"></a>Mapowanie platformy .NET i notacji JSON

Mapowanie między obiektami platformy .NET i w formacie JSON jest naturalnym. Każde pole składowej danych mapuje obiekt JSON, w którym mapuje nazwę pola *klucz* częścią obiektu i rekursywnie wartość mapuje *wartość* wchodzi w skład obiektu. Poniższy kod mapy `Family` klasy do elementu JSON, a następnie tworzy `Family` obiektu:

```csharp
    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };
```

Poprzedni przykład tworzy następującego elementu JSON:

```json
    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                "givenName": "Jesse",
                "gender": "female",
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller",
              "givenName": "Lisa",
              "gender": "female",
              "grade": 8
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };
```

### <a name="linq-to-sql-translation"></a>Tłumaczenie składni LINQ na język SQL

Dostawca zapytania usługi Cosmos DB wykonuje najlepsze możliwe mapowanie z wyniku zapytania LINQ do zapytania SQL usługi Cosmos DB. Poniższy opis założono podstawowe znajomość LINQ.

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

### <a id="SupportedLinqOperators"></a>Obsługiwane operatory zapytań LINQ

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

### <a name="sql-query-operators"></a>Operatory zapytań języka SQL

Poniższe przykłady ilustrują, jak tłumaczenie niektóre standardowe operatory zapytań LINQ do zapytań usługi Cosmos DB.

#### <a name="select-operator"></a>Wybierz operator

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

#### <a name="selectmany-operator"></a>Operator SelectMany

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

#### <a name="where-operator"></a>Operator Where

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

### <a name="composite-sql-queries"></a>Złożone zapytania języka SQL

Można utworzyć, poprzedni operatorów w celu utworzenia bardziej zaawansowane kwerendy. Ponieważ usługi Cosmos DB obsługuje zagnieżdżone kontenery, można połączyć lub zagnieździć kompozycji.

#### <a name="concatenation"></a>Łączenie

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

#### <a name="nesting"></a>Zagnieżdżanie

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

## <a id="References"></a>Dokumentacja

- [Azure Cosmos DB SQL specification (Specyfikacja języka SQL w usłudze Azure Cosmos DB)](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [Specyfikacja języka JavaScript](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 
- Graefe, Goetz. [Zapytanie technik oceny wykonania w przypadku dużych baz danych](https://dl.acm.org/citation.cfm?id=152611). *ACM obliczeń ankiet* 25, nie. 2 (1993).
- Graefe, G. "Framework kaskady optymalizacji zapytań." *Eng. danych IEEE Bull.* 18, nie. 3 (1995).
- Lu, Ooi, Tan. "Przetwarzania zapytań, w systemach równoległe relacyjnej bazy danych." *Naciśnij klawisz społeczeństwa komputera IEEE* (1994).
- Olston Christopher, Benjamin Reed, Utkarsh Srivastava, Kumara Ravi i Andrew Tomkins. "Język Pig Latin: Język nie tak obcych do przetwarzania danych." *SIGMOD* (2008).

## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do usługi Azure Cosmos DB][introduction]
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Poziomy spójności w usłudze Azure Cosmos DB][consistency-levels]

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
