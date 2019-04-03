---
title: Zapytania SQL w usłudze Azure Cosmos DB
description: Dowiedz się więcej o składni języka SQL, pojęciach związanych z bazami danych oraz zapytaniach języka SQL dla usługi Azure Cosmos DB. SQL może służyć jako język zapytań JSON w usłudze Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: mjbrown
ms.openlocfilehash: f2ad46e7738582f82edcef6b54ac8234901c887d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885336"
---
# <a name="sql-query-examples-for-azure-cosmos-db"></a>Przykłady zapytania SQL dla usługi Azure Cosmos DB

Usługa Azure Cosmos DB obsługuje wykonywanie zapytań o elementy, używając języka SQL (Structured Query Language) jako języka zapytań JSON na kontach interfejsu API SQL. Podczas projektowania języka zapytań dla usługi Azure Cosmos DB należy wziąć pod uwagę dwa cele:

* Zamiast tworzyć nowy język zapytań, umożliwiliśmy usłudze Azure Cosmos DB obsługę języka SQL, czyli jednego z najbardziej znanych i popularnych języków zapytań. Język SQL w usłudze Azure Cosmos DB oferuje formalny model programowania na potrzeby zapytań zaawansowanych w postaci elementów w formacie JSON.  

* Usługa Azure Cosmos DB używa modelu programowania języka JavaScript jako podstawy języka zapytań. Interfejs API SQL jest umieszczany w systemie typów, procesie obliczania wartości wyrażeń i wywołaniach funkcji języka JavaScript. W rezultacie powoduje to udostępnianie naturalnego modelu programowania na potrzeby m.in. projekcji relacyjnych, hierarchicznej nawigacji między elementami w formacie JSON, samosprzężeń, zapytań przestrzennych oraz wywoływania funkcji zdefiniowanych przez użytkownika napisanych w całości w języku JavaScript.

W tym artykule za pomocą prostych elementów JSON przedstawiono kilka przykładów zapytań SQL. Aby uzyskać informacje dotyczące składni języka SQL usługi Azure Cosmos DB, zobacz artykuł [SQL syntax reference (Dokumentacja dotycząca składni języka SQL)](sql-api-query-reference.md).

## <a id="GettingStarted"></a>Wprowadzenie do poleceń języka SQL

Utworzymy dwa proste elementy w formacie JSON i zapytania względem tych danych. Rozważ użycie dwóch elementów JSON dotyczących rodzin, wstaw te elementy elementów JSON do kontenera, a następnie wykonaj zapytanie o dane. Mamy tutaj prosty element JSON dla rodzin Andersen i Wakefield: rodzice, dzieci (i ich zwierzęta), adres oraz informacje o rejestracji. Element zawiera ciągi, liczby, wartości logiczne, tablic i właściwości zagnieżdżone.

**Item1 —**

```JSON
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
  "address": { "state": "WA", "county": "King", "city": "seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Oto drugi element z jedną małą różnicą — elementy `givenName` i `familyName` są używane zamiast elementów `firstName` i `lastName`.

**Element 2**

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
        "gender": "female", "grade": 1,
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

Teraz wypróbujmy kilka zapytań względem tych danych, aby poznać niektóre z kluczowych aspektów języka zapytań SQL usługi Azure Cosmos DB.

**Zapytanie 1**: na przykład następujące zapytanie zwraca elementy, w których pole identyfikatora ma wartość `AndersenFamily`. Ponieważ jest to element `SELECT *`, dane wyjściowe zapytania to kompletny element JSON. Aby dowiedzieć się więcej na temat składni, zobacz sekcję [SELECT statement (Instrukcja SELECT)](sql-api-query-reference.md#select-query):

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Wyniki**

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
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

**Zapytanie 2:** teraz rozważmy sytuację, w której musimy ponownie sformatować dane wyjściowe JSON w innym kształcie. To zapytanie przeprowadza projekcję nowego obiektu JSON z dwoma wybranymi polami, Name (Imię i nazwisko) i City (Miasto), gdy miasto w adresie ma taką samą nazwę jak stan. W tym przypadku pasujący element to „NY, NY”.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

**Wyniki**

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

**Zapytanie 3**: to zapytanie zwraca imiona wszystkich dzieci w rodzinie o identyfikatorze zgodnym z `WakefieldFamily`, uporządkowane według miasta zamieszkania.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

**Wyniki**

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

Poniżej przedstawiono kilka aspektów języka zapytań usługi Cosmos DB przy użyciu przykładów użytych do tej pory w tym samouczku:  

* Ponieważ interfejs API SQL działa na wartościach w formacie JSON, obsługuje jednostki w kształcie drzewa, a nie wiersze i kolumny. Dlatego język umożliwia odwoływanie się do węzłów drzewa na dowolnej głębokości, takich jak `Node1.Node2.Node3…..Nodem`, podobnie jak w przypadku relacyjnej bazy danych SQL odwołującej się do dwuczęściowego odwołania `<table>.<column>`.

* Język Structured Query Language współpracuje z danymi bez schematu. Dlatego system typów musi zostać powiązany dynamicznie. To samo wyrażenie może spowodować uzyskiwanie różnych typów w różnych elementach. Wynik zapytania jest prawidłową wartością JSON, ale nie ma gwarancji, że będzie to stały schemat.  

* Usługa Azure Cosmos DB obsługuje tylko ścisłe elementy JSON. Oznacza to, że system typów i wyrażenia są ograniczone do obsługi tylko typów JSON. Zapoznaj się ze [specyfikacją notacji JSON](https://www.json.org/), aby uzyskać więcej informacji.  

* Kontener usługi Cosmos DB to kolekcja elementów JSON bez schematu. Relacje w jednostkach danych wewnątrz elementów kontenera i między nimi są przechwytywane niejawnie przez zawieranie, a nie przez relacje klucza podstawowego i klucza obcego. Jest to ważny aspekt warty wspomnienia ze względu na sprzężenia wewnątrz elementu omówione w dalszej części tego artykułu.

## <a id="SelectClause"></a>Klauzula Select

Każde zapytanie składa się z klauzuli SELECT i opcjonalnych klauzul FROM i WHERE zgodnie ze standardami ANSI-SQL. Zwykle dla każdego zapytania źródło w klauzuli FROM jest wyliczane. Następnie filtr w klauzuli WHERE jest stosowany do źródła w celu pobrania podzestawu elementów JSON. Na koniec klauzula SELECT jest używana do przeprowadzania projekcji żądanych wartości JSON na liście wyboru. Aby dowiedzieć się więcej na temat składni, zobacz sekcję [SELECT syntax (Składnia klauzuli SELECT)](sql-api-query-reference.md#bk_select_query).

Poniższy przykład przedstawia typowe zapytanie SELECT.

**Zapytanie**

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Wyniki**

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "seattle"
      }
    }]
```

### <a name="nested-properties"></a>Właściwości zagnieżdżone

W poniższym przykładzie przeprowadzamy projekcję dwóch właściwości zagnieżdżonych `f.address.state` i `f.address.city`.

**Zapytanie**

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Wyniki**

```json
    [{
      "state": "WA",
      "city": "seattle"
    }]
```

Projekcja obsługuje również wyrażenia JSON, jak pokazano w poniższym przykładzie:

**Zapytanie**

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Wyniki**

```json
    [{
      "$1": {
        "state": "WA",
        "city": "seattle",
        "name": "AndersenFamily"
      }
    }]
```

Przyjrzyjmy się roli elementu `$1`. Klauzula `SELECT` musi tworzyć obiekt JSON, a ponieważ nie podano kluczy, używamy nazw niejawnych zmiennych argumentów, które zaczynają się od `$1`. Na przykład to zapytanie zwraca dwie niejawne zmienne argumentów z etykietami `$1` i `$2`.

**Zapytanie**

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Wyniki**

```json
    [{
      "$1": {
        "state": "WA",
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="FromClause"></a>Klauzula FROM

Klauzula FROM <specyfikacja_początkowa> jest opcjonalna, chyba że później w zapytaniu źródło jest filtrowane lub poddawane projekcji. Aby dowiedzieć się więcej na temat składni, zobacz sekcję [FROM syntax (Składnia klauzuli FROM)](sql-api-query-reference.md#bk_from_clause). Zapytanie, takie jak `SELECT * FROM Families`, wskazuje, że cały kontener Families (Rodziny) jest źródłem, w którym zostaną przeprowadzone wyliczenia. Specjalny identyfikator ROOT może być używany zamiast nazwy kontenera do reprezentowania tego kontenera.
Poniższa lista zawiera reguły, które są wymuszane w poszczególnych zapytaniach:

* Kontener może mieć alias, taki jak `SELECT f.id FROM Families AS f` lub po prostu `SELECT f.id FROM Families f`. W tym miejscu `f` jest odpowiednikiem `Families`. `AS` jest opcjonalnym słowem kluczowym na alias identyfikator.  

* Po dodaniu aliasu nie można powiązać oryginalnego źródła. Na przykład `SELECT Families.id FROM Families f` ma nieprawidłową składnię, ponieważ już nie można rozpoznać identyfikatora „Families” (Rodziny).  

* Wszystkie właściwości, które będą przywoływane, muszą być w pełni kwalifikowane. W przypadku braku zgodności ścisłego schematu jest to wymuszane w celu uniknięcia niejednoznacznych powiązań. Dlatego element `SELECT id FROM Families f` ma nieprawidłową składnię, ponieważ właściwość `id` nie została powiązana.

### <a name="get-subitems-using-from-clause"></a>Pobieranie elementów podrzędnych przy użyciu klauzuli FROM

Źródło można również zredukować do mniejszego podzestawu. Na przykład w celu wyliczania wartości tylko poddrzewa w każdym elemencie podrzędnym katalog główny może stać się źródłem, jak pokazano w poniższym przykładzie:

**Zapytanie**

```sql
    SELECT *
    FROM Families.children
```

**Wyniki**

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

Podczas gdy w powyższym przykładzie jako źródła użyto tablicy, także obiekt może zostać użyty jako źródło, co pokazano w poniższym przykładzie: Dowolna prawidłowa wartość JSON (która nie jest niezdefiniowana) znajdująca się w źródle jest rozważana do włączenia w wyniku zapytania. Jeśli niektóre rodziny nie mają wartości `address.state`, są wykluczane z wyniku zapytania.

**Zapytanie**

```sql
    SELECT *
    FROM Families.address.state
```

**Wyniki**

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="WhereClause"></a>Klauzula WHERE

Klauzula WHERE (**`WHERE <filter_condition>`**) jest opcjonalna. Określa ona warunki, które muszą spełnić elementy JSON dostarczone przez źródło, aby zostały uwzględnione w wyniku. Każdy element JSON musi oceniać określone warunki jako „true”, aby zostały uznane za wynik. Klauzula WHERE jest używana przez warstwę indeksu w celu określenia bezwzględnie najmniejszego podzestawu elementów źródła, który może być częścią wyniku. Aby dowiedzieć się więcej na temat składni, zobacz sekcję [WHERE syntax (Składnia klauzuli WHERE)](sql-api-query-reference.md#bk_where_clause).

Następujące zapytanie żąda elementów, które zawierają właściwość name o wartości `AndersenFamily`. Każdy inny element, który nie ma właściwości name lub w którym właściwość ma wartość inną niż `AndersenFamily`, jest wykluczany.

**Zapytanie**

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Wyniki**

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "seattle"
      }
    }]
```

W poprzednim przykładzie pokazano proste zapytanie dotyczące równości. Interfejs API SQL obsługuje również różne wyrażenia skalarne. Najczęściej używane są wyrażenia binarne i jednoargumentowe. Odwołania do właściwości z obiektu JSON źródła są również prawidłowymi wyrażeniami.

Następujące operatory binarne są obecnie obsługiwane i mogą być używane w zapytaniach, jak pokazano w poniższych przykładach:  

|**Typ operatora**  | **Wartości** |
|---------|---------|
|Arytmetyczny | +,-,*,/,% |
|Bitowy    | \|, &, ^, <<, >>, >>> (wypełnienie zerami, przesunięcie w prawo) |
|Logiczny    | AND, OR, NOT      |
|Porównanie | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\| (łączenie) |

Przyjrzyjmy się kilku zapytaniom korzystającym z operatorów binarnych.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5
```

Operatory jednoargumentowe +, -, ~ i NOT są obecnie obsługiwane i mogą być używane w zapytaniach, jak pokazano w poniższych przykładach:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Oprócz operatorów binarnych i jednoargumentowych są również dozwolone odwołania do właściwości. Na przykład wyrażenie `SELECT * FROM Families f WHERE f.isRegistered` zwraca element JSON zawierający właściwość `isRegistered`, gdzie wartość właściwości jest równa wartości `true` w formacie JSON. Wszystkie inne wartości (false, null, niezdefiniowane, `<number>`, `<string>`, `<object>`, `<array>` itp.) prowadzą do wykluczenia elementu źródłowego z wyniku. 

### <a name="equality-and-comparison-operators"></a>Operatory równości i porównania

W poniższej tabeli przedstawiono wynik porównań równości w interfejsie API SQL między dwoma dowolnymi typami JSON.

| **OP** | **Niezdefiniowane** | **Null** | **Wartość logiczna** | **Liczba** | **String** | **Obiekt** | **Tablica** |
|---|---|---|---|---|---|---|---|
| **Niezdefiniowane** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Null** | Niezdefiniowane | **OK** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Wartość logiczna** | Niezdefiniowane | Niezdefiniowane | **OK** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Liczba** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **OK** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **String** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **OK** | Niezdefiniowane | Niezdefiniowane |
| **Obiekt** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **OK** | Niezdefiniowane |
| **Tablica** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **OK** |

W przypadku innych operatorów porównania, takich jak >, >=, !=, < i <=, mają zastosowanie następujące reguły:

* Porównanie różnych typów generuje wynik Niezdefiniowane.  
* Porównanie dwóch obiektów lub dwóch tablic generuje wynik Niezdefiniowane.

Jeśli wynik wyrażenia skalarnego w filtrze to Niezdefiniowane, odpowiadający element nie zostanie uwzględniony w wyniku, ponieważ wynik Niezdefiniowane logicznie nie równa się wartości „true”.

## <a name="between-keyword"></a>Słowo kluczowe BETWEEN
Słowo kluczowe BETWEEN umożliwia również wyrażanie zapytań względem zakresów wartości, tak jak w języku ANSI SQL. Słowo BETWEEN może być używane w odniesieniu do ciągów lub liczb.

Na przykład to zapytanie zwraca wszystkie elementy rodziny, w których jest właściwość „grade” pierwszego dziecka należy do przedziału od 1 do 5 (włącznie z obydwoma wartościami).

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

W odróżnieniu od języka ANSI-SQL można również użyć klauzuli BETWEEN w klauzuli FROM jak w poniższym przykładzie.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Główna różnica między klauzulą BETWEEN w interfejsie API SQL i języku ANSI SQL polega na tym, że można wyrazić zapytania względem zakresu — na przykład w niektórych elementach właściwość „grade” może być liczbą (5), a w innych ciągiem („grade4”). W takich przypadkach, tak jak w języku JavaScript, porównanie dwóch różnych typów generuje wynik „undefined” i element jest pomijany.

> [!NOTE]
> Aby skrócić czas wykonywania zapytania, pamiętaj o utworzeniu zasad indeksowania używających typu indeksu zakresu względem dowolnych liczbowych właściwości/ścieżek, które są filtrowane w klauzuli BETWEEN.

### <a name="logical-and-or-and-not-operators"></a>Operatory logiczne (AND, OR i NOT)
Operatory logiczne działają na wartościach logicznych. Tabele prawdziwych danych logicznych dla tych operatorów zostały przedstawione poniżej.

**OR — operator**

| LUB | True | False | Niezdefiniowane |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Niezdefiniowane |
| Niezdefiniowane |True |Niezdefiniowane |Niezdefiniowane |

**AND — operator**

| AND | True | False | Niezdefiniowane |
| --- | --- | --- | --- |
| True |True |False |Niezdefiniowane |
| False |False |False |False |
| Niezdefiniowane |Niezdefiniowane |False |Niezdefiniowane |

**NOT — operator**

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Niezdefiniowane |Niezdefiniowane |

## <a name="in-keyword"></a>Słowo kluczowe IN

Słowo kluczowe IN może być używane do sprawdzania, czy określona wartość pasuje do dowolnej wartości na liście. Na przykład to zapytanie zwraca wszystkie elementy rodziny z identyfikatorem „WakefieldFamily” lub „AndersenFamily”.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

W tym przykładzie są zwracane wszystkie elementy, których stan jest dowolną z wybranych wartości.

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="ternary--and-coalesce--operators"></a>Operatory Ternary (?) i Coalesce (??)

Operatorów Ternary i Coalesce można używać do kompilowania wyrażeń warunkowych, podobnie jak w popularnych językach programowania, takich jak C# i JavaScript. Operator Ternary (?) może być przydatny podczas tworzenia nowej właściwości kodu JSON na bieżąco. Możesz teraz pisać zapytania w celu klasyfikowania poziomów klas w postaci czytelnej dla człowieka, takich jak początkujący/średniozaawansowany/zaawansowaney, jak pokazano poniżej.

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Można także zagnieżdżać wywołania w operatorze, podobnie jak w poniższym zapytaniu.

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel
    FROM Families.children[0] c
```

Tak jak w przypadku innych operatorów zapytań, jeśli w dowolnym elemencie brakuje właściwości przywoływanych w wyrażeniu warunkowym lub jeśli porównywane typy są różne, ten element jest wykluczany w wynikach zapytania.

Operator Coalesce (??) może służyć do efektywnego sprawdzania obecności właściwości w elemencie. Ten operator jest przydatny w przypadku wykonywania zapytań względem lub danych z częściową strukturą lub mieszanych typów danych. Na przykład to zapytanie zwraca element „lastName”, jeśli jest obecny, lub element „Name”, jeśli nie jest obecny.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="EscapingReservedKeywords"></a>Metoda dostępu do właściwości cytowanej
Można także uzyskiwać dostęp właściwości za pomocą metody dostępu do właściwości cytowanej `[]`. Na przykład elementy `SELECT c.grade` i `SELECT c["grade"]` są równoważne. Ta składnia jest przydatna, gdy potrzebujesz przeprowadzić ucieczkę dla właściwości zawierającej spacje lub znaki specjalne albo właściwości, która ma taką samą nazwę jak słowo kluczowe SQL lub słowo zastrzeżone.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="aliasing"></a>Tworzenie aliasów

Teraz rozszerzymy powyższy przykład przy użyciu jawnych aliasów wartości. AS to słowo kluczowe używane na potrzeby tworzenia aliasów. Jest ono opcjonalne, jak pokazano podczas projekcji drugiej wartości jako `NameInfo`.

Jeśli zapytanie ma dwie właściwości o takiej samej nazwie, do zmiany nazwy jednej lub obu właściwości należy użyć aliasów, dzięki czemu nazwy będą rozróżniane w projektowanym wyniku.

**Zapytanie**

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Wyniki**

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="scalar-expressions"></a>Wyrażenia skalarne

Oprócz odwołań do właściwości klauzula SELECT obsługuje wyrażenia skalarne, takie jak stałe, wyrażenia arytmetyczne, wyrażenia logiczne itd. Na przykład poniżej przedstawiono proste zapytanie „Hello World”.

**Zapytanie**

```sql
    SELECT "Hello World"
```

**Wyniki**

```json
    [{
      "$1": "Hello World"
    }]
```

Oto bardziej złożony przykład, który używa wyrażenia skalarnego.

**Zapytanie**

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

**Wyniki**

```json
    [{
      "$1": 1.33333
    }]
```

W poniższym przykładzie wynik wyrażenia skalarnego jest wartością logiczną.

**Zapytanie**

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

**Wyniki**

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

Inną ważną funkcją interfejsu API SQL jest tworzenie tablic/obiektów. W poprzednim przykładzie utworzono nowy obiekt JSON. W podobny sposób można tworzyć tablice, jak pokazano w poniższych przykładach:

**Zapytanie**

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

**Wyniki**

```json
    [
      {
        "CityState": [
          "seattle",
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

## <a id="ValueKeyword"></a>Słowo kluczowe VALUE

Słowo kluczowe **VALUE** oferuje sposób zwracania wartości JSON. Na przykład zapytanie pokazane poniżej zwraca wartość skalarną `"Hello World"`, a nie wartość `{$1: "Hello World"}`.

**Zapytanie**

```sql
    SELECT VALUE "Hello World"
```

**Wyniki**

```json
    [
      "Hello World"
    ]
```

Poniższe zapytanie zwraca wartość JSON bez etykiety `"address"` w wynikach.

**Zapytanie**

```sql
    SELECT VALUE f.address
    FROM Families f
```

**Wyniki**

```json
    [
      {
        "state": "WA",
        "county": "King",
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan",
        "city": "NY"
      }
    ]
```

Poniższy przykład jest rozszerzany w celu pokazania sposobu zwracania wartości pierwotnych JSON (poziom liści drzewa JSON).

**Zapytanie**

```sql
    SELECT VALUE f.address.state
    FROM Families f
```

**Wyniki**

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="-operator"></a>Operator *
Operator specjalny (*) jest obsługiwany w przypadku projekcji elementu w aktualnym stanie. Jeśli go używasz, musi być to jedyne pole w projekcji. Zapytanie `SELECT * FROM Families f` jest prawidłowe, ale zapytania takie jak `SELECT VALUE * FROM Families f` i `SELECT *, f.id FROM Families f` są nieprawidłowe.

**Zapytanie**

```sql
    SELECT * 
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

**Wyniki**

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
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a id="TopKeyword"></a>Operator TOP

Słowo kluczowe TOP może być używane do ograniczania liczby wartości z zapytania. Jeśli operator TOP jest używany w połączeniu z klauzulą ORDER BY, zestaw wyników jest ograniczony do pierwszej liczby N uporządkowanych wartości. W przeciwnym razie zwracanych jest pierwszych N wyników w niezdefiniowanej kolejności. Najlepszym rozwiązaniem w instrukcji SELECT jest używanie klauzuli ORDER BY z klauzuli TOP w każdej sytuacji. Łączenie tych dwóch klauzul jest jedynym sposobem przewidywalnego wskazywania wierszy, na które wpływa operator TOP. 

**Zapytanie**

```sql
    SELECT TOP 1 *
    FROM Families f
```

**Wyniki**

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
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

Operator TOP może być używany z wartością stałą (jak pokazano powyżej) lub z wartością zmiennej za pomocą zapytań sparametryzowanych. Aby uzyskać więcej informacji, zobacz poniższe zapytania sparametryzowane.

## <a id="Aggregates"></a>Funkcje agregujące

Można również wykonać agregacje w klauzuli `SELECT`. Funkcje agregujące wykonują obliczenie na zestawie wartości i zwracają pojedynczą wartość. Na przykład poniższe zapytanie zwraca liczbę elementów rodziny w kontenerze.

**Zapytanie**

```sql
    SELECT COUNT(1)
    FROM Families f
```

**Wyniki**

```json
    [{
        "$1": 2
    }]
```

Wartość skalarną agregacji można również zwrócić za pomocą słowa kluczowego `VALUE`. Na przykład poniższe zapytanie zwraca liczbę wartości jako pojedynczą liczbę:

**Zapytanie**

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

**Wyniki**

```json
    [ 2 ]
```

Agregacje można również wykonywać w połączeniu z filtrami. Na przykład poniższe zapytanie zwraca liczbę elementów z adresem w stanie Waszyngton.

**Zapytanie**

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

**Wyniki**

```json
    [ 1 ]
```

W poniższej tabeli przedstawiono listę funkcji agregujących obsługiwanych w interfejsie API SQL. `SUM` i `AVG` są realizowane poprzez wartości liczbowych, natomiast `COUNT`, `MIN`, i `MAX` mogą być wykonywane za pośrednictwem liczby, ciągi, wartości logicznych i wartości null.

| Sposób użycia | Opis |
|-------|-------------|
| COUNT | Zwraca liczbę elementów w wyrażeniu. |
| SUM   | Zwraca sumę wszystkich wartości w wyrażeniu. |
| MIN   | Zwraca minimalną wartość w wyrażeniu. |
| MAX   | Zwraca maksymalną wartość w wyrażeniu. |
| AVG   | Zwraca średnią wartości w wyrażeniu. |

Agregacje można również przeprowadzać w przypadku wyników iteracji tablicy. Aby uzyskać więcej informacji, zobacz [Array Iteration in Queries (Iteracja tablicy w zapytaniach)](#Iteration).

> [!NOTE]
> Jeśli używasz usługi Data Explorer w witrynie Azure Portal, pamiętaj, że zapytania agregacji mogą zwracać częściowo agregowane wyniki za pośrednictwem strony zapytania. Zestawy SDK tworzą pojedynczą wartość zbiorczą na wszystkich stronach.
>
> Do wykonywania zapytań agregacji przy użyciu kodu potrzebny jest zestaw .NET SDK 1.12.0, zestaw .NET Core SDK 1.1.0 lub zestaw Java SDK Java 1.9.5 lub nowszy.
>

## <a id="OrderByClause"></a>Klauzula ORDER BY

Tak jak w języku ANSI-SQL, można uwzględnić opcjonalną klauzulę Order By podczas wykonywania zapytania. Klauzula może zawierać opcjonalny argument ASC/DESC, który określa kolejność pobierania wyników.

Na przykład oto zapytanie, które pobiera rodziny w kolejności według nazwy miasta zamieszkania.

**Zapytanie**

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

**Wyniki**

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

A oto zapytanie, które pobiera rodziny w kolejności według daty utworzenia, która jest przechowywana jako liczba reprezentująca epokę, czyli czas, który upłynął od 1 stycznia 1970 r., w sekundach.

**Zapytanie**

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

**Wyniki**

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

## <a id="Advanced"></a>Zaawansowane pojęcia dotyczące bazy danych i zapytania SQL

### <a id="Iteration"></a>Iteracja

Dodano nową konstrukcję za pośrednictwem słowa kluczowego **IN** w interfejsie API SQL w celu zapewnienia obsługi iterowania za pośrednictwem tablic JSON. Źródło FROM zapewnia obsługę iteracji. Zacznijmy od następującego przykładu:

**Zapytanie**

```sql
    SELECT *
    FROM Families.children
```

**Wyniki**

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

Teraz przyjrzyjmy się innemu zapytaniu, które wykonuje iterację dla dzieci w kontenerze. Zwróć uwagę na różnicę w tablicy wyjściowej. Ten przykład dzieli `children` i spłaszcza wyniki w jednej tablicy.  

**Zapytanie**

```sql
    SELECT *
    FROM c IN Families.children
```

**Wyniki**

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

Tego elementu można używać do odfiltrowania każdego pojedynczego wpisu tablicy, jak pokazano w poniższym przykładzie:

**Zapytanie**

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

**Wyniki**

```json
    [{
      "givenName": "Lisa"
    }]
```

Można również wykonać agregację za pośrednictwem wyniku iteracji tablicy. Na przykład poniższe zapytanie zlicza liczbę dzieci we wszystkich rodzinach.

**Zapytanie**

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

**Wyniki**

```json
    [
      {
        "$1": 3
      }
    ]
```

### <a id="Joins"></a>Sprzężenia

W relacyjnej bazie danych ważne jest używanie sprzężeń między tabelami. Jest to logiczne następstwo projektowania schematów znormalizowanych. Z kolei interfejs API SQL obsługuje nieznormalizowany model danych elementów bez schematu, który jest logicznym odpowiednikiem „samosprzężenia”.

Składnia obsługiwana przez język to `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Mówiąc ogólnie, to zapytanie zwraca zestaw **N**-krotności (krotka z następującą liczbą wartości: **N**). Każda krotka ma wartości utworzone w procesie iterowania wszystkich aliasów kontenera w odpowiednich zestawach. Innymi słowy to zapytanie wykonuje pełny iloczyn wektorowy zestawów uczestniczących w sprzężeniu.

W poniższych przykładach pokazano, jak działa klauzula JOIN. W poniższym przykładzie wynik jest pusty, ponieważ iloczyn wektorowy każdego elementu ze źródła i zestawu pustego jest pusty.

**Zapytanie**

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

**Wyniki**

```json
    [{
    }]
```

W poniższym przykładzie sprzężenie występuje między katalogiem głównym elementu i podrzędnym katalogiem głównym `children`. Jest to iloczyn wektorowy między dwoma obiektami JSON. Fakt, że dzieci znajdują się w tablicy, nie obowiązuje w klauzuli JOIN, ponieważ mamy do czynienia z jednym elementem głównym, który jest tablicą dzieci. Dlatego wynik zawiera tylko dwa wyniki, ponieważ iloczyn wektorowy każdego elementu z tablicą tworzy dokładnie jeden element.

**Zapytanie**

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

**Wyniki**

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

**Zapytanie**

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

**Wyniki**

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

Należy przede wszystkim zauważyć, że element `from_source` klauzuli **JOIN** jest iteratorem. Dlatego przepływ w tym przypadku jest następujący:  

* Rozwiń każdy element podrzędny **c** w tablicy.
* Zastosuj iloczyn wektorowy z elementem głównym elementu **f** z każdego elementu dziecka **c**, który został spłaszczony w pierwszym kroku.
* Na koniec przeprowadź projekcję tylko właściwości nazwy obiektu głównego **f**.

Pierwszy element (`AndersenFamily`) zawiera tylko jeden element „dziecko”, dlatego zestaw wyników zawiera tylko jeden obiekt odpowiadający temu elementowi. Drugi element (`WakefieldFamily`) zawiera dwoje dzieci. Dlatego iloczyn wektorowy tworzy oddzielny obiekt dla każdego dziecka, powodując utworzenie dwóch obiektów, po jednym dla każdego dziecka odpowiadającego temu elementowi. Główne pola w obu elementach są takie same, tak jak można oczekiwać w iloczynie wektorowym.

Rzeczywiste przeznaczenie klauzuli JOIN to formowanie krotek z iloczynu wektorowego w kształcie, którego obsługa w projekcji w innej sytuacji będzie trudna. Co więcej, jak widać w poniższym przykładzie, można filtrować według kombinacji krotki, co umożliwia użytkownikowi wybranie warunku ogólnie spełnianego przez krotki.

**Zapytanie**

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

**Wyniki**

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

Ten przykład stanowi naturalne rozszerzenie powyższego przykładu i wykonuje sprzężenie podwójne. Dlatego iloczyn wektorowy może być wyświetlany jako następujący pseudokod:

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

`AndersenFamily` ma jeden element podrzędny, który ma jedno zwierzę. Dlatego iloczyn wektorowy daje jeden wiersz (1\*1\*1) dla tej rodziny. Rodzina WakefieldFamily ma dwoje dzieci, ale tylko jedno dziecko — „Jesse” — ma zwierzęta. Jesse ma dwa zwierzęta domowe. Dlatego iloczyn wektorowy daje 1\*1\*2 = 2 wiersze dla tej rodziny.

W następnym przykładzie jest używany dodatkowy filtr według elementu `pet`, który wyklucza wszystkie krotki z imieniem zwierzęcia innym niż „Shadow”. Zauważ, że jesteśmy w stanie tworzyć krotki z tablic, filtrować według dowolnego elementu i przeprowadzać projekcję dowolnej kombinacji elementów.

**Zapytanie**

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

**Wyniki**

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a id="JavaScriptIntegration"></a>Integracja z językiem JavaScript

Usługa Azure Cosmos DB oferuje model programowania służący do wykonywania logiki aplikacji opartej na języku JavaScript bezpośrednio w kontenerach w obrębie procedur składowanych i wyzwalaczy. Ta metoda obsługuje również:

* Możliwość wykonywania transakcyjnych zapytań i operacji CRUD o wysokiej wydajności względem elementów w kontenerze dzięki głębokiej integracji środowiska uruchomieniowego języka JavaScript bezpośrednio wewnątrz aparatu bazy danych.
* Naturalne modelowanie przepływu sterowania, określanie zakresu zmiennych oraz przypisania i integrację elementów pierwotnych obsługi wyjątków przy użyciu transakcji bazy danych. Aby uzyskać więcej informacji na temat obsługi usługi Azure Cosmos DB na potrzeby integracji z językiem JavaScript, zapoznaj się z dokumentacją dotyczącą możliwości programowania w języku JavaScript po stronie serwera.

### <a id="UserDefinedFunctions"></a>Funkcje zdefiniowane przez użytkownika

Oprócz typów już zdefiniowanych w tym artykule interfejs API SQL obsługuje także funkcje zdefiniowane przez użytkownika. W szczególności skalarne funkcje zdefiniowane przez użytkownika są obsługiwane w sytuacjach, gdy deweloperzy mogą przekazać zero lub wiele argumentów i uzyskać w wyniku pojedynczy argument. Każdy z tych argumentów jest sprawdzany pod kątem tego, czy jest dozwoloną wartością JSON.  

Składnia SQL jest rozszerzana tak, aby obsługiwać niestandardową logikę aplikacji za pomocą funkcji zdefiniowanych przez użytkownika. Funkcje zdefiniowane przez użytkownika można rejestrować za pomocą interfejsu SQL API i odwoływać się do nich jako do elementu zapytania SQL. Tak naprawdę funkcje zdefiniowane przez użytkownika są w sposób zaawansowany projektowane na potrzeby wywoływania z zapytań. W następstwie tego wyboru funkcje zdefiniowane przez użytkownika nie mają dostępu do obiektu kontekstu, tak jak inne typy języka JavaScript (procedury składowane i wyzwalacze). Ponieważ zapytania są wykonywane jako tylko do odczytu, można uruchomić je w replikach podstawowych lub pomocniczych. Dlatego funkcje zdefiniowane przez użytkownika są przeznaczone do uruchamiania w replikach pomocniczych, w przeciwieństwie do innych typów języka JavaScript.

Poniżej przedstawiono przykładowy sposób, w jaki funkcję zdefiniowaną przez użytkownika można zarejestrować w bazie danych usługi Cosmos DB, w szczególności w ramach kontenera elementów.

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

Poprzedni przykład tworzy funkcję zdefiniowaną przez użytkownika o nazwie `REGEX_MATCH`. Akceptuje ona dwie wartości ciągu JSON, `input` i `pattern`, i sprawdza, czy pierwsza pasuje do wzorca określonego w drugiej przy użyciu funkcji string.match() języka JavaScript.

Teraz możemy użyć tej funkcji zdefiniowanej przez użytkownika w zapytaniu w projekcji. Funkcje zdefiniowane przez użytkownika muszą być kwalifikowane za pomocą prefiksu „udf.”uwzględniającego wielkość liter, gdy są wywoływane w ramach zapytań.

> [!NOTE]
> Przed 17.03.2015 r. usługa Cosmos DB obsługiwała wywołania funkcji zdefiniowanych przez użytkownika bez prefiksu „.udf”, np. SELECT REGEX_MATCH(). Ten wzorzec wywołujący jest teraz przestarzały.  
>

**Zapytanie**

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

**Wyniki**

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

Funkcji zdefiniowanej przez użytkownika można również używać wewnątrz filtru, jak pokazano w poniższym przykładzie, jako kwalifikowanej za pomocą prefiksu „udf.” :

**Zapytanie**

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

**Wyniki**

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

W zasadzie funkcje zdefiniowane przez użytkownika to prawidłowe wyrażenia skalarne, których można używane w projekcjach i filtrów.

Aby rozszerzyć możliwości funkcji UDF, przyjrzyjmy się innemu przykładowi z logiką warunkową:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'seattle':
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

Poniżej znajduje się przykład, który używa funkcji zdefiniowanej przez użytkownika.

**Zapytanie**

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

**Wyniki**

```json
     [
      {
        "city": "seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Jak pokazują poprzednie przykłady, funkcje zdefiniowane przez użytkownika integrują możliwości języka JavaScript z interfejsem API SQL w celu zapewnienia bogatego programowalnego interfejsu, który umożliwia wykonywania złożonej proceduralnej, warunkowej logiki za pomocą wbudowanych możliwości środowiska uruchomieniowego języka JavaScript.

Interfejs API SQL udostępnia argumenty funkcjom zdefiniowanym przez użytkownika dla każdego elementu w źródle na bieżącym etapie (klauzula WHERE lub klauzula SELECT) przetwarzania funkcji zdefiniowanej przez użytkownika. Wynik jest bezproblemowo uwzględniany w ogólnym potoku wykonywania. Jeśli właściwości przywoływane przez parametry funkcji zdefiniowanej przez użytkownika są niedostępne w wartości JSON, parametr jest traktowany jako niezdefiniowany i dlatego wywołanie funkcji zdefiniowanej przez użytkownika jest w całości pomijane. Podobnie jeśli wynik funkcji zdefiniowanej przez użytkownika jest niezdefiniowany, nie jest ona uwzględniana w wyniku.

Podsumowując, funkcje zdefiniowane przez użytkownika to doskonałe narzędzia pozwalające na użycie złożonej logiki biznesowej jako części zapytania.

### <a name="operator-evaluation"></a>Ocena operatora

Dzięki temu, że baza danych Cosmos DB jest bazą danych JSON, wykazuje podobieństwa do operatorów języka JavaScript i jego semantyki oceny. Usługa Cosmos DB próbuje zachować semantykę języka JavaScript w zakresie obsługi formatu JSON, a ocena operacji odbiega od niej w pewnych okolicznościach.

W interfejsie API SQL — w odróżnieniu od tradycyjnego języka SQL — typy wartości często nie są znane do momentu pobrania wartości z bazy danych. Aby efektywnie wykonywać zapytania, większość operatorów mają ścisłe wymagania dotyczące typu.

W przeciwieństwie do języka JavaScript interfejs API SQL nie wykonuje konwersji niejawnych. Na przykład zapytanie, takie jak `SELECT * FROM Person p WHERE p.Age = 21`, dopasowuje elementy, które zawierają właściwość Age (Wiek) o wartości 21. Żaden inny element, którego właściwość Age odpowiada ciągowi „21” lub jego prawdopodobnie nieskończonej liczbie odmian, takich jak „021”, „21,0”, „0021”, „00021” itp., nie zostanie dopasowany. Jest to przeciwieństwo języka JavaScript, w którym wartości ciągu są niejawnie rzutowane na liczby (na podstawie operatora, np: ==). Ten wybór ma kluczowe znaczenie dla efektywnego dopasowywania indeksu w interfejsie API SQL.

## <a name="parameterized-sql-queries"></a>Sparametryzowane zapytania SQL

Usługa Cosmos DB obsługuje zapytania z parametrami wyrażonymi za pomocą znanej notacji \@. Sparametryzowany język SQL zapewnia niezawodną obsługę i przeprowadzanie ucieczki dla danych wejściowych użytkownika, co zapobiega przypadkowemu ujawnieniu danych przez wstrzyknięcie kodu SQL.

Można na przykład napisać zapytanie, które przyjmuje nazwisko i stan w adresie jako parametry, a następnie uruchamiać je dla różnych wartości nazwisk i stanów w adresie na podstawie danych wejściowych użytkownika.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

To żądanie następnie można wysłać do usługi Cosmos DB jako sparametryzowane zapytanie JSON, jak pokazano poniżej.

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

Argument klauzuli TOP można ustawić za pomocą sparametryzowanych zapytań, takich jak pokazane poniżej.

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Wartości parametru mogą być dowolnym prawidłowym kodem JSON (takim jak ciągi, liczby, wartości logiczne, wartość null, a nawet tablice lub zagnieżdżony kod JSON). Ponadto ponieważ usługa Cosmos DB nie używa schematu, parametry nie są weryfikowane względem żadnego typu.

## <a id="BuiltinFunctions"></a>Funkcje wbudowane

Usługa Cosmos DB obsługuje także szereg wbudowanych funkcji używanych w typowych operacjach. Można używać ich wewnątrz zapytań tak jak funkcji zdefiniowanych przez użytkownika.

| Grupa funkcji | Operacje |
|---------|----------|
| Funkcje matematyczne | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN, TAN |
| Funkcje kontroli typów | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Funkcje ciągów | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING, UPPER |
| Funkcje tablicy | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH, ARRAY_SLICE |
| Funkcje przestrzenne | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Jeśli aktualnie używasz funkcji zdefiniowanej przez użytkownika, dla której jest dostępna funkcja wbudowana, należy skorzystać z odpowiedniej funkcji wbudowanej, ponieważ jej uruchamianie powinno być szybsze i wydajniejsze.

### <a name="mathematical-functions"></a>Funkcje matematyczne

Każda z funkcji matematycznych wykonuje obliczenie na podstawie wartości wejściowych, które są podawane jako argumenty, i zwraca wartość liczbową. Poniżej znajduje się tabela zawierająca obsługiwane wbudowane funkcje matematyczne.

| Sposób użycia | Opis |
|----------|--------|
| [ABS (wyrażenie_liczbowe) | Zwraca wartość bezwzględną (dodatnią) podanego wyrażenia liczbowego. |
| CEILING (wyrażenie_liczbowe) | Zwraca najmniejszą wartość całkowitą równą określonemu wyrażeniu liczbowemu lub większą. |
| FLOOR (wyrażenie_liczbowe) | Zwraca największą wartość całkowitą równą określonemu wyrażeniu liczbowemu lub mniejszą. |
| EXP (wyrażenie_liczbowe) | Zwraca wykładnik określonego wyrażenia liczbowego. |
| Dziennik (num_expr, podstawowy) | Zwraca logarytm naturalny określonego wyrażenia liczbowego lub logarytm o określonej podstawie. |
| LOG10 (wyrażenie_liczbowe) | Zwraca wartość logarytmiczną przy podstawie 10 określonego wyrażenia liczbowego. |
| ROUND (wyrażenie_liczbowe) | Zwraca wartość liczbową zaokrągloną do najbliższej wartości całkowitej. |
| TRUNC (wyrażenie_liczbowe) | Zwraca wartość liczbową przyciętą do najbliższej wartości całkowitej. |
| SQRT (wyrażenie_liczbowe) | Zwraca pierwiastek kwadratowy określonego wyrażenia liczbowego. |
| SQUARE (wyrażenie_liczbowe) | Zwraca kwadrat określonego wyrażenia liczbowego. |
| ZASILANIA (num_expr, num_expr) | Zwraca wartość określonego wyrażenia liczbowego podniesioną do podanej potęgi. |
| SIGN (wyrażenie_liczbowe) | Zwraca wartość znaku (-1, 0, 1) określonego wyrażenia liczbowego. |
| ACOS (wyrażenie_liczbowe) | Zwraca kąt w radianach, którego cosinus jest określonym wyrażeniem liczbowym. Ta funkcja jest również nazywana arcus cosinus. |
| ASIN (wyrażenie_liczbowe) | Zwraca kąt w radianach, którego sinus jest określonym wyrażeniem liczbowym. Ta funkcja jest również nazywana arcus sinus. |
| ATAN (wyrażenie_liczbowe) | Zwraca kąt w radianach, którego tangens jest określonym wyrażeniem liczbowym. Ta funkcja jest również nazywana arcus tangens. |
| ATN2 (wyrażenie_liczbowe) | Zwraca kąt w radianach, między dodatnią osią x i promieniem z początku do punktu (y, x), gdzie x i y to wartości dwóch określonych wyrażeń zmiennoprzecinkowych. |
| COS (wyrażenie_liczbowe) | Zwraca trygonometryczny cosinus określonego kąta w radianach w określonym wyrażeniu. |
| COT (wyrażenie_liczbowe) | Zwraca trygonometryczny cotangens określonego kąta w radianach w określonym wyrażeniu liczbowym. |
| DEGREES (wyrażenie_liczbowe) | Zwraca kąt w stopniach odpowiadający kątowi określonemu w radianach. |
| PI () | Zwraca stałą wartość liczby PI. |
| RADIANS (wyrażenie_liczbowe) | Zwraca wartość w radianach po wprowadzeniu wyrażenia liczbowego w stopniach. |
| SIN (wyrażenie_liczbowe) | Zwraca trygonometryczny sinus określonego kąta w radianach w określonym wyrażeniu. |
| TAN (wyrażenie_liczbowe) | Zwraca tangens wyrażenia wejściowego w określonym wyrażeniu. |

Teraz można na przykład uruchomić zapytania, jak pokazano w poniższym przykładzie:

**Zapytanie**

```sql
    SELECT VALUE ABS(-4)
```

**Wyniki**

```json
    [4]
```

Główna różnica między funkcjami usługi Cosmos DB w porównaniu do języka ANSI SQL polega na tym, że zostały one zaprojektowane z myślą o współdziałaniu z danymi bez schematu i ze schematem mieszanym. Jeśli na przykład masz element bez właściwości Size (Rozmiar) lub z nienumeryczną wartością, taką jak „unknown” (nieznana), ten element zostanie pominięty, zamiast zwracać błąd.

### <a name="type-checking-functions"></a>Funkcje kontroli typów

Funkcje kontroli typów pozwalają sprawdzić typ wyrażenia w zapytaniach języka SQL. Funkcji kontroli typów można używać do określania typu właściwości elementów na bieżąco, gdy jest to zmienna lub właściwość nieznana. Poniżej znajduje się tabela zawierająca obsługiwane wbudowane funkcje kontroli typów.

| **Sposób użycia** | **Opis** |
|-----------|------------|
| [IS_ARRAY (wyrażenie)](sql-api-query-reference.md#bk_is_array) | Zwraca wartość logiczną wskazującą, czy typ wartości jest tablicą. |
| [IS_BOOL (wyrażenie)](sql-api-query-reference.md#bk_is_bool) | Zwraca wartość logiczną wskazującą, czy typ wartości jest wartością logiczną. |
| [IS_NULL (expr)](sql-api-query-reference.md#bk_is_null) | Zwraca wartość logiczną wskazującą, czy typ wartości jest wartością null. |
| [IS_NUMBER (wyrażenie)](sql-api-query-reference.md#bk_is_number) | Zwraca wartość logiczną wskazującą, czy typ wartości jest liczbą. |
| [Is_object — (wyrażenie)](sql-api-query-reference.md#bk_is_object) | Zwraca wartość logiczną wskazującą, czy typ wartości jest obiektem JSON. |
| [IS_STRING (wyrażenie)](sql-api-query-reference.md#bk_is_string) | Zwraca wartość logiczną wskazującą, czy typ wartości jest ciągiem. |
| [IS_DEFINED (wyrażenie)](sql-api-query-reference.md#bk_is_defined) | Zwraca wartość logiczną wskazującą, do właściwości przypisano wartość. |
| [IS_PRIMITIVE (wyrażenie)](sql-api-query-reference.md#bk_is_primitive) | Zwraca wartość logiczną wskazującą, czy typ wartości jest ciągiem, liczbą, wartością logiczną lub wartością null. |

Używając tych funkcji, można teraz uruchomić zapytania, jak pokazano w poniższym przykładzie:

**Zapytanie**

```sql
    SELECT VALUE IS_NUMBER(-4)
```

**Wyniki**

```json
    [true]
```

### <a name="string-functions"></a>Funkcje ciągów

Następujące funkcje skalarne wykonują operację na wartości wejściowej ciągu i zwracają ciąg, wartość liczbową lub wartość logiczną. Poniżej znajduje się tabela wbudowanych funkcji ciągów:

| Sposób użycia | Opis |
| --- | --- |
| [DŁUGOŚĆ (str_expr)](sql-api-query-reference.md#bk_length) | Zwraca liczbę znaków z określonego wyrażenia ciągu. |
| [CONCAT (str_expr str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | Zwraca ciąg, który jest wynikiem połączenia co najmniej dwóch wartości ciągu. |
| [SUBSTRING (str_expr, num_expr, num_expr)](sql-api-query-reference.md#bk_substring) | Zwraca część wyrażenia ciągu. |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | Zwraca wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu rozpoczyna się od drugiego. |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | Zwraca wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu kończy się drugim. |
| [ZAWIERA (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | Zwraca wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu zawiera drugie. |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | Zwraca pozycję początkową pierwszego wystąpienia drugiego ciągu wyrażenia w ramach pierwszego określonego wyrażenia ciągu lub wartość -1, jeśli ciąg nie zostanie znaleziony. |
| [Po lewej stronie (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | Zwraca lewą część ciągu z określoną liczbą znaków. |
| [Po prawej stronie (str_expr, num_expr)](sql-api-query-reference.md#bk_right) | Zwraca prawą część ciągu z określoną liczbą znaków. |
| [PRZYTP (str_expr)](sql-api-query-reference.md#bk_ltrim) | Zwraca wyrażenie ciągu po usunięciu spacji wiodących. |
| [PRZYTK (str_expr)](sql-api-query-reference.md#bk_rtrim) | Zwraca wyrażenie ciągu po przycięciu wszystkich spacji końcowych. |
| [MAŁE (str_expr)](sql-api-query-reference.md#bk_lower) | Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych wielkich liter na małe litery. |
| [GÓRNY (str_expr)](sql-api-query-reference.md#bk_upper) | Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych małych liter na wielkie litery. |
| [Zastąp (str_expr str_expr, str_expr)](sql-api-query-reference.md#bk_replace) | Zamienia wszystkie wystąpienia określonej wartości ciągu na inną wartość ciągu. |
| [REPLIKACJA (str_expr, num_expr)](https://docs.microsoft.com/azure/cosmos-db/sql-api-sql-query-reference#bk_replicate) | Powtarza wartość ciągu określoną liczbę razy. |
| [REVERSE (str_expr)](sql-api-query-reference.md#bk_reverse) | Zwraca wartość ciągu w odwrotnej kolejności. |

Używając tych funkcji, można teraz uruchomić zapytania, takie jak poniżej. Można na przykład zwrócić nazwę rodziny zapisaną wielkimi literami w następujący sposób:

**Zapytanie**

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

**Wyniki**

```json
    [
        "WAKEFIELDFAMILY",
        "ANDERSENFAMILY"
    ]
```

Lub połączyć ciąg jak w tym przykładzie:

**Zapytanie**

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

**Wyniki**

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]
```

Funkcje ciągów można być również używane w klauzuli WHERE do filtrowania wyników, podobnie jak w poniższym przykładzie:

**Zapytanie**

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

**Wyniki**

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>Funkcje tablicy

Następujące funkcje skalarne wykonują operację na wartości wejściowej tablicy i zwracają, wartość liczbową, wartość logiczną lub wartość tablicy. Poniżej znajduje się tabela wbudowanych funkcji tablicy:

| Sposób użycia | Opis |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |Zwraca liczbę elementów określonego wyrażenia tablicy. |
| [ARRAY_CONCAT (arr_expr arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |Zwraca tablicę, która jest wynikiem połączenia co najmniej dwóch wartości tablicy. |
| [ARRAY_CONTAINS (arr_expr, wyrażenie [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |Zwraca wartość logiczną wskazującą, czy tablica zawiera określoną wartość. Można określić, czy dopasowanie jest pełne czy lub częściowe. |
| [ARRAY_SLICE (arr_expr num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |Zwraca część wyrażenia tablicy. |

Funkcje tablicy może służyć do manipulowania tablicami w formacie kodzie JSON. Na przykład poniżej znajduje się zapytanie, które zwraca wszystkie elementy, w których jeden z rodziców to „Robin Wakefield”. 

**Zapytanie**

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

**Wyniki**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Można określić częściowy fragment na potrzeby pasujących elementów w tablicy. Następujące zapytanie znajdzie wszystkich rodziców, dla których element `givenName` ma wartość `Robin`.

**Zapytanie**

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

**Wyniki**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Oto inny przykład, który używa funkcji ARRAY_LENGTH w celu uzyskania liczby dzieci w każdej rodzinie.

**Zapytanie**

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

**Wyniki**

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

Usługa Cosmos DB obsługuje następujące wbudowane funkcje OGC (Open Geospatial Consortium) w celu wykonywania zapytań dotyczących danych geoprzestrzennych. 

| Sposób użycia | Opis |
| --- | --- |
| ST_DISTANCE (wyrażenie_punktu, wyrażenie_punktu) | Zwraca odległość między dwoma wyrażeniami GeoJSON typu Point, Polygon lub LineString. |
| T_WITHIN (wyrażenie_punktu, wyrażenie_wielokąta) | Zwraca wyrażenie warunkowe wskazujące, czy pierwszy obiekt GeoJSON (Point, Polygon lub LineString) znajduje się w drugim obiekcie GeoJSON (Point, Polygon lub LineString). |
| ST_INTERSECTS (wyrażenie_przestrzenne, wyrażenie_przestrzenne) | Zwraca wyrażenie warunkowe wskazujące, czy dwa określone obiekty GeoJSON (Point, Polygon lub LineString) przecinają się. |
| ST_ISVALID | Zwraca wartość logiczną wskazującą, czy określone wyrażenie GeoJSON typu Point, Polygon lub LineString jest nieprawidłowe. |
| ST_ISVALIDDETAILED | Zwraca wartość JSON zawierającą wartość logiczną, jeśli określone wyrażenie GeoJSON typu Point, Polygon lub LineString jest prawidłowe, a jeśli jest nieprawidłowe, dodatkowo zwraca przyczynę jako wartość ciągu. |

Funkcje przestrzenne mogą być używane do wykonywania zapytań dotyczących odległości względem danych przestrzennych. Na przykład poniższe zapytanie zwraca wszystkie elementy rodziny, które znajdują się maksymalnie 30 km od określonej lokalizacji przy użyciu wbudowanej funkcji ST_DISTANCE.

**Zapytanie**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

**Wyniki**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Aby uzyskać więcej informacji na temat obsługi danych geoprzestrzennych, zobacz [Working with geospatial data in Azure Cosmos DB (Praca z danymi geoprzestrzennymi w usłudze Azure Cosmos DB)](geospatial.md). To już wszystkie informacje na temat funkcji przestrzennych i składni języka SQL dla usługi Cosmos DB. Teraz zobaczmy, jak działa wykonywanie zapytań w modelu LINQ i jak współpracuje on z omówioną do tej pory składnią.

## <a id="Linq"></a>Interfejs API LINQ to SQL

LINQ to model programowania platformy .NET, który wyraża obliczenie jako zapytania w strumieniu obiektów. Usługa Cosmos DB udostępnia w interfejsie bibliotekę po stronie klienta za pomocą modelu LINQ, ułatwiając konwersję między obiektami JSON i .NET oraz mapowanie z podzestawu zapytań LINQ do zapytań usługi Cosmos DB.

Na poniższej ilustracji przedstawiono architekturę pomocniczych zapytań LINQ, które używają usługi Cosmos DB.  Za pomocą klienta usługi Cosmos DB deweloperzy mogą tworzyć obiekt **IQueryable**, który bezpośrednio wykonuje zapytanie do dostawcy zapytań usługi Cosmos DB. Następnie tłumaczy on zapytanie LINQ na zapytanie usługi Cosmos DB. Zapytanie jest następnie przekazywane do serwera usługi Cosmos DB w celu pobrania zestawu wyników w formacie JSON. Zwrócone wyniki są deserializowane do postaci strumienia obiektów platformy .NET po stronie klienta.

![Architektura pomocniczych zapytań LINQ używających interfejsu API SQL — składnia języka SQL, język zapytań JSON, pojęcia dotyczące baz danych oraz zapytania SQL][1]

### <a name="net-and-json-mapping"></a>Mapowanie platformy .NET i notacji JSON

Mapowanie między obiektami platformy .NET i elementami w formacie JSON jest procesem naturalnym — każde pole elementu członkowskiego danych jest mapowane na obiekt JSON, tak aby nazwa pola była mapowana na część „key” obiektu, a część „value” była rekursywnie mapowana na część „wartość” obiektu. Rozważmy następujący przykład: utworzony obiekt Family (Rodzina) jest mapowany na element JSON, jak pokazano poniżej. I odwrotnie: element JSON jest mapowany z powrotem na obiekt .NET.

**C# Class**

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

**JSON**

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

Dostawca zapytania usługi Cosmos DB wykonuje najlepsze możliwe mapowanie z wyniku zapytania LINQ do zapytania SQL usługi Cosmos DB. W poniższym opisie przyjęto założenie, że czytelnik zna podstawy modelu LINQ.

Po pierwsze w przypadku systemu typów obsługujemy wszystkich pierwotne typy JSON — typy liczbowe, wartości logiczne, ciągi i wartości null. Obsługiwane są tylko te typy JSON. Obsługiwane są poniższe wyrażenia skalarne.

* Wartości stałe — obejmują stałe wartości pierwotnych typów danych w czasie oceny zapytania.
* Wyrażenia indeksu tablicy/właściwości — te wyrażenia odwołują się do właściwości elementu tablicy lub obiektu.
  
     family.Id;    family.children[0].familyName;    family.children[0].grade;    family.children[n].grade; //n jest zmienną całkowitą
* Wyrażenia arytmetyczne —obejmują one typowe wyrażenia arytmetyczne używające wartości liczbowych i logicznych. Kompletną listę można znaleźć w specyfikacji języka SQL.
  
     2 * family.children[0].grade;    x + y;
* Wyrażenie porównania ciągu — obejmują one porównanie wartości ciągu ze stałą wartością ciągu.  
  
     mother.familyName == "Smith";    child.givenName == s; //s jest zmienną ciągu
* Wyrażenie tworzenia obiektu/tablicy — te wyrażenia zwracają obiekt typu wartości złożonej lub typu anonimowego albo tablicę takich obiektów. Te wartości mogą być zagnieżdżone.
  
     new Parent { familyName = "Smith", givenName = "Joe" }; new { first = 1, second = 2 }; //typ anonimowy z dwoma polami              
     new int[] { 3, child.grade, 5 };

### <a id="SupportedLinqOperators"></a>Lista obsługiwanych operatorów LINQ

Poniżej przedstawiono listę obsługiwanych operatorów LINQ w dostawcy LINQ dołączonym do zestawu .NET SDK języka SQL.

* **Select**: projekcje są tłumaczone na instrukcję SELECT języka SQL, łącznie z konstrukcją obiektu
* **Where**: filtry są tłumaczone na instrukcję WHERE języka SQL i obsługują tłumaczenie między && , || i ! na operatory języka SQL
* **SelectMany**: umożliwia rozwijanie tablic do klauzuli JOIN języka SQL. Ten operator może być używany w wyrażeniach łańcucha/zagnieżdżania do filtrowania elementów tablicy
* **OrderBy i OrderByDescending**: operator tłumaczony na instrukcję ORDER BY rosnąco/malejąco
* Operatory **Count**, **Sum**, **Min**, **Max** i **Average** na potrzeby agregacji oraz ich asynchroniczne odpowiedniki **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** i **AverageAsync**.
* **CompareTo**: operator tłumaczony na porównania zakresu. Często używany w przypadku ciągów, ponieważ nie są one porównywalne na platformie .NET
* **Take**: operator tłumaczony na klauzulę TOP języka SQL w celu ograniczania wyników z zapytania
* **Funkcje matematyczne**: obsługuje tłumaczenie następujących funkcji platformy .NET: Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate na odpowiednie wbudowane funkcje języka SQL.
* **Funkcje ciągu**: obsługują tłumaczenie następujących funkcji platformy .NET: Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper na odpowiednie wbudowane funkcje języka SQL.
* **Funkcje tablicy**: obsługują tłumaczenie następujących funkcji platformy .NET: Concat, Contains i Count na odpowiednie wbudowane funkcje języka SQL.
* **Funkcje rozszerzenia geoprzestrzennego**: obsługują tłumaczenie następujących metod klas zastępczych: Distance, Within, IsValid i IsValidDetailed na odpowiednie wbudowane funkcje języka SQL.
* **Funkcja rozszerzenia funkcji zdefiniowanych przez użytkownika**: obsługuje tłumaczenie z metody klasy zastępczej UserDefinedFunctionProvider.Invoke na odpowiednią funkcję zdefiniowaną przez użytkownika.
* **Różne**: obsługują tłumaczenie operatora coalesce i operatorów warunkowych. W zależności od kontekstu mogą tłumaczyć funkcję Contains na String CONTAINS, ARRAY_CONTAINS lub IN języka SQL.

### <a name="sql-query-operators"></a>Operatory zapytań języka SQL

Poniżej przedstawiono przykłady, które ilustrują sposób tłumaczenia niektórych standardowych operatorów zapytań LINQ na zapytania usługi Cosmos DB.

#### <a name="select-operator"></a>Operator Select

Składnia to `input.Select(x => f(x))`, gdzie `f` jest wyrażeniem skalarnym.

**Wyrażenie lambda LINQ**

```csharp
    input.Select(family => family.parents[0].familyName);
```

**SQL** 

```sql
    SELECT VALUE f.parents[0].familyName
    FROM Families f
```

**Wyrażenie lambda LINQ**

```csharp
    input.Select(family => family.children[0].grade + c); // c is an int variable
```

**SQL**

```sql
    SELECT VALUE f.children[0].grade + c
    FROM Families f
```

**Wyrażenie lambda LINQ**

```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
```

**SQL** 

```sql
    SELECT VALUE {"name":f.children[0].familyName,
                  "grade": f.children[0].grade + 3 }
    FROM Families f
```


#### <a name="selectmany-operator"></a>Operator SelectMany

Składnia to `input.SelectMany(x => f(x))`, gdzie `f` jest wyrażeniem skalarnym, które zwraca typ kontenera.

**Wyrażenie lambda LINQ**

```csharp
    input.SelectMany(family => family.children);
```

**SQL**

```sql
    SELECT VALUE child
    FROM child IN Families.children
```

#### <a name="where-operator"></a>Operator Where

Składnia to `input.Where(x => f(x))`, gdzie `f` jest wyrażenie skalarnym, które zwraca wartość logiczną.

**Wyrażenie lambda LINQ**

```csharp
    input.Where(family=> family.parents[0].familyName == "Smith");
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
```

**Wyrażenie lambda LINQ**

```csharp
    input.Where(
        family => family.parents[0].familyName == "Smith" &&
        family.children[0].grade < 3);
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3
```

### <a name="composite-sql-queries"></a>Złożone zapytania języka SQL

Powyższe operatory mogą być złożone w celu tworzenia bardziej zaawansowanych zapytań. Ponieważ usługa Cosmos DB obsługuje kontenery zagnieżdżone, kompozycja może być połączona lub zagnieżdżona.

#### <a name="concatenation"></a>Łączenie

Składnia to `input(.|.SelectMany())(.Select()|.Where())*`. Zapytanie połączone można uruchomić przy użyciu opcjonalnego zapytania `SelectMany`, po którym nastąpi wiele operatorów `Select` lub `Where`.

**Wyrażenie lambda LINQ**

```csharp
    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
```

**Wyrażenie lambda LINQ**

```csharp
    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);
```

**SQL**

```sql
    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3
```

**Wyrażenie lambda LINQ**

```csharp
    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)
```

**Wyrażenie lambda LINQ**

```csharp
    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");
```

**SQL**

```sql
    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"
```

#### <a name="nesting"></a>Zagnieżdżanie

Składnia to `input.SelectMany(x=>x.Q())`, gdzie Q jest operatorem `Select`, `SelectMany` lub `Where`.

W zapytaniu zagnieżdżonym zapytanie wewnętrzne jest stosowane do każdego elementu kontenera zewnętrznego. Jedna z ważnych funkcji to fakt, że zapytanie wewnętrzne może odwoływać się do pól elementów w kontenerze zewnętrznym, takich jak samosprzężenia.

**Wyrażenie lambda LINQ**

```csharp
    input.SelectMany(family=>
        family.parents.Select(p => p.familyName));
```

**SQL**

```sql
    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents
```

**Wyrażenie lambda LINQ**

```csharp
    input.SelectMany(family =>
        family.children.Where(child => child.familyName == "Jeff"));
```

**SQL**

```sql
    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"
```

**Wyrażenie lambda LINQ**

```csharp
    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));
```

**SQL**

```sql
    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName
```

## <a id="ExecutingSqlQueries"></a>Wykonywanie zapytań SQL

Usługa Cosmos DB udostępnia zasoby za pośrednictwem interfejsu API REST, który można wywoływać za pomocą dowolnego języka realizującego żądania HTTP i HTTPS. Ponadto usługa Cosmos DB oferuje biblioteki programistyczne dla kilku popularnych języków, takich jak .NET, Node.js, JavaScript i Python. Interfejs API REST i różne biblioteki obsługują wykonywanie zapytań za pośrednictwem języka SQL. Zestaw .NET SDK obsługuje wykonywanie zapytań LINQ oprócz zapytań SQL.

Poniższe przykłady pokazują, jak utworzyć zapytanie i przesłać je do konta bazy danych Cosmos DB.

### <a id="RestAPI"></a>Interfejs API REST

Usługa Cosmos DB oferuje otwarty model programowania RESTful używający protokołu HTTP. Konta bazy danych mogą być aprowizowane przy użyciu subskrypcji platformy Azure. Model zasobów usługi Cosmos DB składa się z zestawu zasobów w ramach konta bazy danych. Każdy z tych zasobów można zaadresować przy użyciu logicznego i stabilnego identyfikatora URI. W tym elemencie zestaw zasobów jest określany jako źródło. Konto bazy danych składa się z zestawu baz danych, z których każda zawiera wiele kontenerów z elementami, funkcjami zdefiniowanymi przez użytkownika oraz innymi typami zasobów.

Podstawowy model interakcji z tymi zasobami używa czasowników HTTP GET, PUT, POST i DELETE oraz ich standardowej interpretacji. Czasownik POST jest używany do tworzenia nowego zasobu, wykonywania procedury składowanej lub wystawiania zapytania usługi Cosmos DB. Zapytania są zawsze operacjami tylko do odczytu bez efektów ubocznych.

W poniższych przykładach pokazano operator POST dla zapytania interfejsu API SQL wykonanego względem kontenera zawierającego dwa elementy przykładowe przedstawione do tej pory. Zapytanie ma prosty filtr właściwości nazwy JSON. Zwróć uwagę na użycie nagłówków `x-ms-documentdb-isquery` i Content-Type: `application/query+json`, aby wskazać, czy operacja jest zapytaniem.

**Żądanie**
```
    POST https://<REST URI>/docs HTTP/1.1
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

**Wyniki**

```
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    <indented for readability, results highlighted>

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
                "city":"seattle"
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

Drugi przykład przedstawia bardziej złożone zapytanie, które zwraca wiele wyników sprzężenia.

**Żądanie**
```
    POST https://<REST URI>/docs HTTP/1.1
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

**Wyniki**

```
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    <indented for readability, results highlighted>

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

Jeśli wyniki zapytania nie mieszczą się na jednej stronie wyników, interfejs API REST zwraca token kontynuacji za pośrednictwem nagłówka odpowiedzi `x-ms-continuation-token`. Klienci mogą dzielić wyniki na strony, uwzględniając nagłówek w kolejnych wynikach. Można także kontrolować liczbę wyników na stronie za pośrednictwem nagłówka liczby `x-ms-max-item-count`. Jeśli określone zapytanie ma funkcję agregacji, taką jak `COUNT`, na stronie zapytania może zostać zwrócona częściowo zagregowana wartość za pośrednictwem strony wyników. Klienci muszą wykonać agregację drugiego poziomu za pośrednictwem tych wyników w celu wygenerowania wyników końcowych, na przykład sumy liczebności zwróconych na poszczególnych stronach w celu zwrócenia liczebności łącznej.

Aby zarządzać zasadami spójności danych dla zapytań, należy użyć nagłówka `x-ms-consistency-level`, tak jak we wszystkich żądaniach interfejsu API REST. W celu zapewnienia spójności sesji należy również wykonać echo najnowszego nagłówka pliku cookie `x-ms-session-token` w żądaniu zapytania. Zasady indeksowania kontenera uwzględnionego w zapytaniu mogą również wpływać na spójność wyników zapytania. Dzięki domyślnym ustawieniom zasad indeksowania indeks związany z kontenerami jest zawsze aktualny, a zawartość elementów i wyniki zapytania są zgodne z poziomem spójności wybranym dla danych. Aby uzyskać więcej informacji, zobacz [Poziomy spójności w usłudze Azure Cosmos DB][consistency-levels].

Jeśli zasady indeksowania skonfigurowane w kontenerze nie mogą obsługiwać określonego zapytania, serwer usługi Azure Cosmos DB zwraca błąd 400 „Nieprawidłowe żądanie”. Ten komunikat o błędzie jest zwracany dla zapytań ze ścieżkami jawnie wykluczone z indeksowania. Można określić nagłówek `x-ms-documentdb-query-enable-scan`, aby zezwolić zapytaniu na przeprowadzenie skanowania, gdy indeks nie będzie dostępny.

Aby uzyskać szczegółowe metryki dotyczące wykonywania zapytań, można ustawić nagłówek `x-ms-documentdb-populatequerymetrics` na `True`. Aby uzyskać więcej informacji, zobacz [SQL query metrics for Azure Cosmos DB (Metryki zapytań języka SQL dla usługi Azure Cosmos DB)](sql-api-query-metrics.md).

### <a id="DotNetSdk"></a>Zestaw C# (.NET) SDK

Zestaw .NET SDK obsługuje wykonywanie zapytań LINQ i SQL. Poniższy przykład pokazuje, jak wykonać zapytanie filtru wprowadzone wcześniej w tym elemencie.
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

W tym przykładzie porównujemy dwie właściwości pod kątem równości w poszczególnych elementach i używamy projekcji anonimowych.

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

Następny przykład pokazuje sprzężenia wyrażone za pośrednictwem funkcji SelectMany składni LINQ.

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

    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

Klient platformy .NET automatycznie wykonuje iterację przez wszystkie strony wyników zapytania w blokach instrukcji foreach, jak pokazano powyżej. Opcje zapytania wprowadzone w sekcji interfejsu API REST są również dostępne w zestawie .NET SDK za pośrednictwem klas `FeedOptions` i `FeedResponse` w metodzie CreateDocumentQuery. Liczbą stron można sterować za pomocą ustawienia `MaxItemCount`.

Można także jawnie kontrolować stronicowania, tworząc `IDocumentQueryable` przy użyciu `IQueryable` obiektu, a następnie, czytając `ResponseContinuationToken` wartości i przekazywania ich z powrotem jako `RequestContinuationToken` w `FeedOptions`. `EnableScanInQuery` można ustawić tak, aby włączyć skanowanie, gdy zapytanie nie może być obsługiwana przez skonfigurowane zasady indeksowania. W przypadku kontenerów partycjonowanych można używać elementu `PartitionKey`, aby uruchamiać zapytania względem jednej partycji (chociaż usługa Azure Cosmos DB może wyodrębnić go z tekstu zapytania), a elementu `EnableCrossPartitionQuery`, aby uruchamiać zapytania, które mogą wymagać uruchamiania względem wielu partycji.

Zapoznaj się z [przykładami dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet), aby uzyskać więcej informacji na temat przykładów zawierających zapytania.

### <a id="JavaScriptServerSideApi"></a>Interfejs API języka JavaScript po stronie serwera

Usługa Cosmos DB oferuje model programowania służący do wykonywania logiki aplikacji opartej na języku JavaScript bezpośrednio w kontenerach przy użyciu procedur składowanych i wyzwalaczy. Logika języka JavaScript zarejestrowana na poziomie kontenera może następnie wyzwalać operacje bazy danych w ramach operacji na elementach danego kontenera. Te operacje są opakowane w otaczające transakcje ACID.

Poniższy przykład pokazuje, jak używać elementu queryDocuments w interfejsie API języka JavaScript na serwerze do wykonywania zapytań z wewnątrz procedur składowanych i wyzwalaczy.

```javascript
    function businessLogic(name, author) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="References"></a>Dokumentacja

1. [Wprowadzenie do usługi Azure Cosmos DB][introduction]
2. [Usługa Azure Cosmos DB SQL specyfikacji](https://go.microsoft.com/fwlink/p/?LinkID=510612)
3. [Przykłady usługi Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
4. [Poziomy spójności w usłudze Azure Cosmos DB][consistency-levels]
5. ANSI SQL 2011 [https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6. Notacja JSON [https://json.org/](https://json.org/)
7. Specyfikacja języka Javascript [https://www.ecma-international.org/publications/standards/Ecma-262.htm](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8. Składnia LINQ [https://msdn.microsoft.com/library/bb308959.aspx](https://msdn.microsoft.com/library/bb308959.aspx) 
9. Techniki oceny zapytań w przypadku dużych baz danych [https://dl.acm.org/citation.cfm?id=152611](https://dl.acm.org/citation.cfm?id=152611)
10. Query Processing in Parallel Relational Database Systems (Przetwarzanie zapytań w równoległych systemach relacyjnych baz danych), IEEE Computer Society Press, 1994
11. Lu, Ooi, Tan, Query Processing in Parallel Relational Database Systems (Przetwarzanie zapytań w równoległych systemach relacyjnych baz danych), IEEE Computer Society Press, 1994.
12. Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: Pig Latin: A Not-So-Foreign Language for Data Processing (Pig Latin: niezbyt obcy język do przetwarzania danych), SIGMOD 2008.
13. G. Graefe. The Cascades framework for query optimization (Platforma kaskad na potrzeby optymalizacji zapytań). IEEE Data Eng. Bull., 18(3): 1995.

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
