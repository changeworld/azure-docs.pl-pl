---
title: Zapytania SQL pod kątem usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o składni języka SQL, pojęć dotyczących baz danych i zapytań SQL usługi Azure Cosmos DB. SQL może być używany jako język zapytań JSON w usłudze Azure Cosmos DB.
keywords: Składnia SQL, zapytanie sql, zapytania sql, język zapytań json, pojęć dotyczących baz danych i zapytania sql, funkcje agregujące
services: cosmos-db
author: LalithaMV
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: laviswa
ms.openlocfilehash: f6829d497c85ef1b4e74e26befe42d5d6fa87e36
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205973"
---
# <a name="sql-queries-for-azure-cosmos-db"></a>Zapytania SQL pod kątem usługi Azure Cosmos DB

Microsoft Azure Cosmos DB obsługuje tworzenie zapytań dla dokumentów przy użyciu języka SQL (Structured Query Language) jako język zapytań JSON na kontach interfejsu API SQL. Usługa Azure Cosmos DB jest naprawdę bez schematu. Ze względu na swoje zaangażowanie do modelu danych JSON bezpośrednio wewnątrz aparatu bazy danych zapewnia automatyczne indeksowanie dokumentów JSON, bez konieczności jawnego schematu lub tworzenia indeksów pomocniczych.

Podczas projektowania język zapytań usługi Cosmos DB, mieliśmy dwóch celów pamiętać:

* Zamiast inventing nowego języka zapytań dla formatu JSON, Chcieliśmy obsługuje język SQL. SQL jest jednym z najbardziej znanych i popularnych języków zapytania. SQL usługi cosmos DB zapewnia model programowania formalne zaawansowane zapytania przez dokumentów JSON.
* Jako dokument bazy danych JSON stanie wykonywanie kodu JavaScript bezpośrednio w aparacie bazy danych Chcieliśmy używają modelu programowania języka JavaScript jako podstawa dla naszych język zapytań. Interfejs API SQL zostaje umieszczone w systemie typów języka JavaScript, Obliczanie wyrażenia i wywołania funkcji. To umożliwiłoby przewiduje naturalnych modelu programowania projekcje relacyjnych, hierarchicznych nawigacji między dokumentów JSON, samodzielnie sprzężeń, zapytań przestrzennych i wywołania funkcji zdefiniowanych przez użytkownika (UDF), napisanych w całości w języku JavaScript, m.in. 

Firma Microsoft uważa, że te funkcje są kluczem do redukując opór między aplikacją i bazy danych mają kluczowe znaczenie podczas pracy deweloperskiej.

Zalecamy rozpoczęcie od obejrzenia poniższego klipu wideo, gdzie Azure Menedżer programu usługi Cosmos DB Andrew Liu pokazuje możliwości zapytań usługi Azure Cosmos DB i pokazuje online [Plac zabaw dla zapytań](http://www.documentdb.com/sql/demo), gdzie możesz wypróbować platformy Azure Usługa cosmos DB i wykonywania zapytania SQL w odniesieniu do naszego zestawu danych, jak pokazano w trakcie filmu wideo.

> [!VIDEO https://www.youtube.com/embed/1LqUQRpHfFI]
>
>

Bardziej zaawansowane techniki zapytań zostały przedstawione w tym monitującą film wideo:

> [!VIDEO https://www.youtube.com/embed/kASU9NOIR90]
>
>

Następnie wróć do tego artykułu, gdy Rozpoczniemy pracę z samouczkiem zapytania SQL, który przeprowadzi Cię przez pewne proste dokumentów JSON i polecenia SQL.

## <a id="GettingStarted"></a>Wprowadzenie do poleceń SQL w usłudze Cosmos DB
Aby wyświetlić SQL usługi Cosmos DB w miejscu pracy, możemy zaczynają się od kilku prostych dokumentów JSON i poznać dokładnie proces pewnych prostych zapytań przed nim. Należy wziąć pod uwagę te dwa dokumenty JSON o dwie grupy. Za pomocą usługi Cosmos DB nie musimy jawnie utworzyć żadnych schematów ani indeksów pomocniczych. Po prostu należy wstawić dokumenty JSON w kolekcji usługi Cosmos DB i następnie wykonywać zapytania. Tu mamy JSON prostych dokumentów dla rodziny Andersen, nadrzędne, podrzędne (i ich zwierzęta), adres i informacje o rejestracji. Dokument ma ciągi, liczby, wartości logicznych, tablic i zagnieżdżonych właściwości. 

**Document**  

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

W tym miejscu jest drugi dokument z jedną różnicą subtelne — `givenName` i `familyName` są używane zamiast `firstName` i `lastName`.

**Document**  

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

Teraz Wypróbujmy kilka zapytań dotyczących tych danych, aby poznać niektóre z kluczowych aspektów języka zapytania SQL usługi Azure Cosmos DB. Na przykład, następujące zapytanie zwraca dokumenty, których pole id `AndersenFamily`. Ponieważ jest ono `SELECT *`, dane wyjściowe zapytania są kompletnym dokumentem JSON:

**Zapytanie**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

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


Teraz należy rozważyć przypadek, gdy będziemy musieli ponownie sformatować dane wyjściowe JSON w innym kształcie. To zapytanie projektów obiekt JSON z dwoma wybrane pola Nazwisko i Miasto, gdy adres miasto ma taką samą nazwę jak stan. W tym przypadku pasuje do "NY, NY".

**Zapytanie**    

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**Results**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


Następne zapytanie zwraca imiona wszystkich dzieci w rodzinie o identyfikatorze zgodnym `WakefieldFamily` uporządkowane według miejscowości zamieszkania.

**Zapytanie**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**Results**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


Chcielibyśmy zwrócić uwagę na kilka warte zauważenia aspektów języka zapytań usługi Cosmos DB przy użyciu przykładów, które widzieliśmy w do tej pory:  

* Ponieważ interfejs API SQL działa na wartości JSON, dotyczy drzewa ukształtowane jednostki zamiast wierszy i kolumn. W związku z tym, języka pozwala odwoływać się do węzłów drzewa na dowolnym poziomie dowolnego, takie jak `Node1.Node2.Node3…..Nodem`, podobnie jak relacyjna baza danych SQL odwołujące się do dwóch części odwołanie `<table>.<column>`.   
* Język structured query language współpracuje z danych bez schematu. W związku z tym system typu musi być dynamicznie powiązane. To samo wyrażenie może przynieść różnych typów w różnych dokumentach. Wynik zapytania jest prawidłową wartością JSON, ale nie musi być stały schemat.  
* Usługa cosmos DB obsługuje tylko ścisłe dokumentów JSON. Oznacza to, że system typów i wyrażenia są ograniczone do czynienia tylko z typami JSON. Zapoznaj się [specyfikacji formatu JSON](http://www.json.org/) Aby uzyskać więcej informacji.  
* Kolekcja usługi Cosmos DB jest kontenerem dokumentów JSON bez schematu. Relacje w encji danych wewnątrz i pomiędzy dokumenty w kolekcji są przechwytywane niejawnie przez zawierania a nie przez klucz podstawowy i relacje klucza obcego. Jest to ważnym aspektem wspomnieć w świetle sprzężeń wewnątrz dokumentu omówione w dalszej części tego artykułu.

## <a id="Indexing"></a> Indeksowania usługi cosmos DB
Przed przejściem do składni SQL, jest wart poznania indeksowania projektu w usłudze Azure Cosmos DB. 

Celem indeksy bazy danych jest do obsługi zapytań w ich różnych formularzy i kształty o użyciu zasobów minimalny (na przykład procesora CPU lub we/wy) przy jednoczesnym zapewnieniu dobrej przepływności i małego opóźnienia. Często wybranego indeksu prawo do wykonywania zapytań bazy danych wymaga dużej ilości planowania i eksperymentowania. To podejście stanowi wyzwanie dla baz danych bez schematu, gdy dane nie są zgodne z ścisłego schematu i szybkiego rozwoju. 

W związku z tym Projektując podsystemu indeksowania usługi Cosmos DB, możemy ustawić następujące cele:

* Indeksować dokumenty bez schematu: indeksowania podsystem nie wymagają żadnych informacji o schemacie ani nie należy czynić żadnych założeń o schemacie dokumentów. 
* Obsługa zapytań efektywne, bogate hierarchicznych i relacyjne: indeks obsługuje język zapytań usługi Cosmos DB, takie jak obsługa projekcje relacyjnych i hierarchicznych.
* Obsługa zapytań spójne in face of dużych ilości zapisów: w przypadku obciążeń zapisu o wysokiej przepływności za pomocą zapytań spójne zaktualizowaniu indeksu przyrostowo, sprawnie i online w przypadku dużych ilości zapisy. Aktualizacja spójne indeksu jest do obsługi zapytań na poziomie spójności, w którym użytkownik skonfigurowany usługa dokumentów.
* Obsługa wielu dzierżawców: podana model oparty na rezerwacji dla nadzór nad zasobami dla dzierżaw, aktualizacje indeksu będą wprowadzane w ramach budżetu zasobów systemowych (CPU, pamięci i operacji wejścia/wyjścia na sekundę) przydzielone dla replik. 
* Wydajność magazynu: efektywność kosztową narzut na przechowywanie na dysku, indeksu jest ograniczony i przewidywalne. Jest to istotne, ponieważ usługi Cosmos DB umożliwia deweloperom kompromisy opartych na kosztach między narzut indeksu w odniesieniu do wydajności zapytań.  

Zapoznaj się [przykładów usługi Azure Cosmos DB](https://github.com/Azure/azure-documentdb-net) w witrynie MSDN dla przykładów, pokazujący sposób konfigurowania zasad indeksowania w kolekcji. Możemy teraz przejść do szczegółów składni SQL usługi Azure Cosmos DB.

## <a id="Basics"></a>Podstawowe informacje dotyczące zapytań SQL usługi Azure Cosmos DB
Każdego zapytania, który składa się z klauzuli SELECT i opcjonalnie FROM a klauzulach WHERE w standardy ANSI SQL. Zwykle dla każdego zapytania do źródła w klauzuli FROM są wyliczane. Następnie w klauzuli WHERE filtrowaniu na "source" do pobrania podzbioru dokumentów JSON. Na koniec klauzuli SELECT jest używany do projektu wymagane wartości JSON na liście wyboru.

    SELECT <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a id="FromClause"></a>FROM — klauzula
`FROM <from_specification>` Klauzula jest opcjonalny w przypadku, jeśli źródło jest filtrowana lub przewidywany później w zapytaniu. Ta klauzula ma na celu określania źródła danych, na którym zapytanie musi działać. Często jest źródłem w całej kolekcji, ale zamiast tego można określić podzbiór kolekcji. 

Zapytanie, takich jak `SELECT * FROM Families` wskazuje, że całą kolekcję rodzin źródła, względem którego ma zostać wyliczenia. Specjalny identyfikator główny może służyć do reprezentowania kolekcji zamiast nazwę kolekcji. Poniższa lista zawiera reguły, które są wymuszane na zapytanie:

* Kolekcja może być aliasem, taką jak `SELECT f.id FROM Families AS f` lub po prostu `SELECT f.id FROM Families f`. W tym miejscu `f` jest odpowiednikiem `Families`. `AS` jest opcjonalnym słowem kluczowym na alias identyfikator.
* Jeden raz, ponieważ nie można powiązać oryginalnego źródła. Na przykład `SELECT Families.id FROM Families f` ma nieprawidłową składnię, ponieważ już nie można rozpoznać identyfikatora "Rodzin".
* Wszystkie właściwości, które muszą być przywoływane musi być w pełni kwalifikowana. W przypadku braku zgodności ścisłego schematu ta wartość jest wymuszana w celu uniknięcia niejednoznaczne powiązań. W związku z tym `SELECT id FROM Families f` ma nieprawidłową składnię, ponieważ właściwość `id` nie jest powiązany.

### <a name="subdocuments"></a>Dokumenty podrzędne
Źródła można zmniejszyć w taki sposób, aby mniejszego podzestawu. Na przykład do wyliczania tylko poddrzewo poszczególnych dokumentów, subroot można następnie stają się źródła, jak pokazano w poniższym przykładzie:

**Zapytanie**

    SELECT * 
    FROM Families.children

**Results**  

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

Powyższy przykład tablicy są używane jako źródło, obiekt może być również używana jako źródło, w którym jest przedstawionego w poniższym przykładzie: wszystkie prawidłową wartość JSON (nie jest niezdefiniowany) znajdujący się w źródle uznaje się do włączenia w wyniku zapytania. Jeśli nie masz niektórych rodzin `address.state` wartości, są one wyłączone w wyniku zapytania.

**Zapytanie**

    SELECT * 
    FROM Families.address.state

**Results**

    [
      "WA", 
      "NY"
    ]


## <a id="WhereClause"></a>Klauzula WHERE
Klauzula WHERE (**`WHERE <filter_condition>`**) jest opcjonalny. Określa, że warunki, dokumenty JSON dostarczony przez źródło musi spełniać, aby być dołączone do wyniku. Dowolny dokument JSON musi być określone warunki "true", aby zostały uznane za na wynik. Klauzula WHERE jest używana przez warstwę indeksu w celu określenia najmniejszy bezwzględne podzbiór dokumentów źródła, które mogą być częścią wynik. 

Następujące zapytanie żąda dokumentów, które zawierają właściwość name, którego wartością jest `AndersenFamily`. Dowolny dokument, który nie ma właściwości name, lub gdy nie jest zgodna wartość `AndersenFamily` jest wykluczona. 

**Zapytanie**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


W poprzednim przykładzie pokazano równości proste zapytanie. Interfejs API SQL obsługuje również wiele wyrażeń skalarnych. Najczęściej używane są dane binarne i jednoargumentowe wyrażeń. Odwołania do właściwości z obiektu JSON źródła są również prawidłowych wyrażeń. 

Następujące operatory dwuargumentowe są obecnie obsługiwane i mogą być używane w kwerendach, jak pokazano w poniższych przykładach:  

<table>
<tr>
<td>Operacje arytmetyczne</td>    
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>bitowe</td>    
<td>|, &, ^, <<>>,, >>> (zero wypełnienia przesunięcia bitowego w prawo)</td>
</tr>
<tr>
<td>Logiczne</td>
<td>AND, OR, NOT</td>
</tr>
<tr>
<td>porównanie</td>    
<td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td>
</tr>
<tr>
<td>Ciąg</td>    
<td>|| (Połącz)</td>
</tr>
</table>  


Spójrzmy na kilka zapytań przy użyciu operatorów binarnych.

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


Operatory jednoargumentowe +,-, ~ nie są również obsługiwane i może być używany wewnątrz zapytania, jak pokazano w poniższym przykładzie:

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



Oprócz operatorów binarnych i Jednoelementowy są również dozwolone odwołania do właściwości. Na przykład `SELECT * FROM Families f WHERE f.isRegistered` zwraca dokument JSON zawierający właściwości `isRegistered` gdzie wartość właściwości jest równa JSON `true` wartość. Inne wartości (wartość false, wartość null, niezdefiniowane, `<number>`, `<string>`, `<object>`, `<array>`, itp.) prowadzi do dokumentu źródłowego, są wykluczone z wyników. 

### <a name="equality-and-comparison-operators"></a>Operatory równości i porównania
W poniższej tabeli przedstawiono wynik porównania równości w interfejsie API SQL między wszystkie dwa typy JSON.

<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>OP</strong>
         </td>
         <td valign="top">
            <strong>Niezdefiniowane</strong>
         </td>
         <td valign="top">
            <strong>Wartość null</strong>
         </td>
         <td valign="top">
            <strong>Atrybut typu wartość logiczna</strong>
         </td>
         <td valign="top">
            <strong>Numer</strong>
         </td>
         <td valign="top">
            <strong>ciąg</strong>
         </td>
         <td valign="top">
            <strong>obiekt</strong>
         </td>
         <td valign="top">
            <strong>Tablica</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Niezdefiniowane<strong>
         </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Wartość null<strong>
         </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Atrybut typu wartość logiczna<strong>
         </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Numer<strong>
         </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>ciąg<strong>
         </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>obiekt<strong>
         </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Nie zdefiniowano </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Tablica<strong>
         </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
Nie zdefiniowano </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
      </tr>
   </tbody>
</table>

Dla innych operatorów porównania, takie jak >, > =,! =, < a < = następujące reguły zastosowania:   

* Porównanie różnych typów powoduje niezdefiniowane.
* Porównanie dwóch obiektów lub dwie tablice powoduje niezdefiniowane.   

Jeśli wynik wyrażenie skalarne, które w filtrze jest niezdefiniowana, odpowiedni dokument nie będzie uwzględniony w wyniku, ponieważ niezdefiniowane logicznie nie równa się "true".

## <a name="between-keyword"></a>MIĘDZY — słowo kluczowe
Słowo kluczowe BETWEEN umożliwia również zapytań dotyczących zakresów wartości, takich jak ANSI SQL express. MIĘDZY może służyć do atakowania ciągów lub liczby.

Na przykład ta kwerenda zwraca wszystkie rodziny dokumentów, w których jest pierwszym elementem podrzędnym klasy korporacyjnej, od 1 do 5 (oba włącznie). 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

W odróżnieniu od w ANSI SQL umożliwia także klauzuli BETWEEN w klauzuli FROM, podobnie jak w poniższym przykładzie.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Celu skrócenia czasu wykonywania zapytania Pamiętaj, aby utworzyć zasady indeksowania, korzystającej z typu indeks zakresu względem dowolnego liczbowych właściwości/ścieżek, które są filtrowane w klauzuli BETWEEN. 

Główna różnica między BETWEEN w interfejsie SQL API i ANSI SQL jest czy można wyrazić zakres zapytań dotyczących właściwości mieszane typy — na przykład może być "grade" numer (5) w niektórych dokumentów i ciągi w innych ("grade4"). W takich przypadkach takich jak w języku JavaScript, porównanie dwóch różnych typów skutkuje "undefined" i dokument zostanie pominięte.

### <a name="logical-and-or-and-not-operators"></a>Logicznych (AND, OR i NOT) operatory
Operatory logiczne działają na wartości logiczne. Tabel prawdziwych danych logicznych dla tych operatorów są wyświetlane w poniższych tabelach.

| LUB | True | False | Nie zdefiniowano |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Nie zdefiniowano |
| Nie zdefiniowano |True |Nie zdefiniowano |Nie zdefiniowano |

| I | True | False | Nie zdefiniowano |
| --- | --- | --- | --- |
| True |True |False |Nie zdefiniowano |
| False |False |False |False |
| Nie zdefiniowano |Nie zdefiniowano |False |Nie zdefiniowano |

| NIE |  |
| --- | --- |
| True |False |
| False |True |
| Nie zdefiniowano |Nie zdefiniowano |

## <a name="in-keyword"></a>IN — słowo kluczowe
IN — słowo kluczowe może służyć do sprawdzania, czy określona wartość pasuje do dowolnej wartości na liście. Na przykład ta kwerenda zwraca wszystkie rodziny dokumentów identyfikator przypadku "WakefieldFamily" lub "AndersenFamily". 

    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

W tym przykładzie zwraca wszystkie dokumenty, gdy stan to dowolnego z określonymi wartościami.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

## <a name="ternary--and-coalesce--operators"></a>Ternary (?) i operatory Coalesce (?)
Operatory Ternary i Coalesce może służyć do tworzenia wyrażeń warunkowych, podobnie jak popularnych języków programowania, takich jak C# i JavaScript. 

Operator Ternary (?) może być bardzo przydatne podczas tworzenia nowej właściwości kodu JSON na bieżąco. Na przykład możesz teraz napisać zapytania do klasyfikowania poziomy klasy w postaci czytelnej dla człowieka, takich jak Początkujący/pośredni/zaawansowane jak pokazano poniżej.

     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

Można także zagnieżdżać wywołania do operatora podobnie jak w poniższym zapytaniu.

    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Jako przy użyciu innych operatorów zapytań, czy brakuje odwołania właściwości w wyrażeniu warunkowym, w każdym dokumencie, czy typy są porównywane są różne, następnie dokumentów, które są wykluczone w wynikach zapytania.

Operator Coalesce (?) może służyć do wydajnego (zwany również sprawdzić obecność właściwości zdefiniowano) w dokumencie. Jest to przydatne podczas wykonywania zapytań względem lub częściową strukturą lub mieszane typy danych. Na przykład ta kwerenda zwraca "lastName", jeśli jest obecna, lub "nazwisko" Jeśli nie jest obecny.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

## <a id="EscapingReservedKeywords"></a>Metoda dostępu do właściwości w cudzysłowie
Można także przejść do właściwości, za pomocą operatora cudzysłowie właściwość `[]`. Na przykład `SELECT c.grade` i `SELECT c["grade"]` są równoważne. Ta składnia jest przydatne, gdy potrzebujesz jako znak ucieczki dla właściwości, która zawiera spacje, znaki specjalne lub stanie się udostępnić taką samą nazwę jak słowo kluczowe SQL lub słowo zastrzeżone.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a id="SelectClause"></a>Klauzula SELECT
SELECT — klauzula (**`SELECT <select_list>`**) jest obowiązkowy i określa, jakie są one pobierane zapytania, podobnie jak w języku SQL ANSI. Podzbiór jest zostały przefiltrowane na podstawie dokumentów źródłowych są przekazywane na projekcji fazy, gdzie pobierane są określone wartości JSON, a nowy obiekt JSON jest konstruowany, dla każdego dane wejściowe przekazane do jej. 

Poniższy przykład przedstawia typowe zapytania SELECT. 

**Zapytanie**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### <a name="nested-properties"></a>Zagnieżdżonych właściwości
W poniższym przykładzie firma Microsoft jest przekazywana dwie właściwości zagnieżdżone `f.address.state` i `f.address.city`.

**Zapytanie**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


Projekcja obsługuje również wyrażenia JSON, jak pokazano w poniższym przykładzie:

**Zapytanie**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


Przyjrzyjmy się rola `$1` tutaj. `SELECT` Klauzuli musi utworzyć obiekt JSON, a ponieważ klucz nie zostanie podany, używamy nazwy zmiennej niejawnego argumentu, począwszy od `$1`. Na przykład ta kwerenda zwraca dwóch zmiennych argumentów niejawnego etykietą `$1` i `$2`.

**Zapytanie**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


## <a name="aliasing"></a>Tworzenie aliasów
Teraz możemy rozszerzać powyższego przykładu przy użyciu aliasów jawne wartości. Jest to — słowo kluczowe, umożliwiający tworzenie aliasów. Jest to opcjonalne, jak pokazano podczas projekcji drugiej wartości jako `NameInfo`. 

W przypadku, gdy zapytanie ma dwie właściwości o takiej samej nazwie, aby zmienić nazwę jedną lub obie właściwości, dzięki czemu są one rozróżniane w przewidywany wynik należy użyć aliasów.

**Zapytanie**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


## <a name="scalar-expressions"></a>Wyrażenia skalarne
Oprócz odwołania do właściwości klauzula SELECT obsługuje wyrażenia skalarne, takie jak stałe, wyrażenia arytmetyczne, wyrażenia logiczne itd. Na przykład poniżej przedstawiono proste zapytanie "Hello World".

**Zapytanie**

    SELECT "Hello World"

**Results**

    [{
      "$1": "Hello World"
    }]


Oto przykład bardziej złożony, który używa wyrażenia skalarne.

**Zapytanie**

    SELECT ((2 + 11 % 7)-2)/3    

**Results**

    [{
      "$1": 1.33333
    }]


W poniższym przykładzie wynik wyrażenia skalarne jest wartością logiczną.

**Zapytanie**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f    

**Results**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


## <a name="object-and-array-creation"></a>Tworzenie obiektu i tablicy
Inną niezwykle ważną cechą interfejsu API SQL jest tworzenie tablicy i obiektów. W poprzednim przykładzie należy pamiętać, że firma Microsoft utworzył nowy obiekt JSON. Analogicznie jeden można także skonstruować tablic jak pokazano w poniższych przykładach:

**Zapytanie**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f    

**Results**  

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

## <a id="ValueKeyword"></a>VALUE — słowo kluczowe
**Wartość** — słowo kluczowe udostępnia sposób zwrócenia wartości JSON. Na przykład, zapytanie poniżej zwraca skalarnych `"Hello World"` zamiast `{$1: "Hello World"}`.

**Zapytanie**

    SELECT VALUE "Hello World"

**Results**

    [
      "Hello World"
    ]


Następujące zapytanie zwraca wartość JSON bez `"address"` etykiety w wynikach.

**Zapytanie**

    SELECT VALUE f.address
    FROM Families f    

**Results**  

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

Poniższy przykład rozszerza ten element, aby pokazać sposób zwracania wartości pierwotnych JSON (poziomu liścia drzewa JSON). 

**Zapytanie**

    SELECT VALUE f.address.state
    FROM Families f    

**Results**

    [
      "WA",
      "NY"
    ]


## <a name="-operator"></a>* — Operator
Operator specjalny (*) jest obsługiwany w do projektu dokumentu-to. W przypadku użycia, musi być jedynym polem przewidywany. Podczas gdy zapytanie takie jak `SELECT * FROM Families f` jest prawidłowy, `SELECT VALUE * FROM Families f ` i `SELECT *, f.id FROM Families f ` są nieprawidłowe.

**Zapytanie**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

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

## <a id="TopKeyword"></a>TOP Operator
GÓRNY — słowo kluczowe może służyć do Ogranicz liczbę wartości z zapytania. Jeśli TOP jest używany w połączeniu z klauzulą ORDER BY, zestaw wyników jest ograniczona do pierwsza liczba N uporządkowane wartości. w przeciwnym razie zwraca pierwsze N liczba wyników, w kolejności niezdefiniowane. Najlepszym rozwiązaniem w instrukcji SELECT, zawsze za pomocą klauzuli ORDER BY w klauzuli TOP. Jest to jedyny sposób przewidywalny wskazują wiersze, które wpływają na GÓRZE. 

**Zapytanie**

    SELECT TOP 1 * 
    FROM Families f 

**Results**

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

TOP może służyć z wartością stałą (jak pokazano powyżej) lub z wartością zmiennej za pomocą sparametryzowanych zapytań. Aby uzyskać więcej informacji zobacz poniższe sparametryzowanych zapytań.

## <a id="Aggregates"></a>Funkcje agregujące
Można również wykonać agregacji w `SELECT` klauzuli. Funkcje agregujące wykonywanie obliczeń na zestaw wartości i zwraca wartość typu single. Na przykład następujące zapytanie zwraca liczbę rodziny dokumentów w kolekcji.

**Zapytanie**

    SELECT COUNT(1) 
    FROM Families f 

**Results**

    [{
        "$1": 2
    }]

Możesz też zwrócić wartość skalarna agregacji za pomocą `VALUE` — słowo kluczowe. Na przykład następujące zapytanie zwraca liczbę wartości jako pojedyncza liczba:

**Zapytanie**

    SELECT VALUE COUNT(1) 
    FROM Families f 

**Results**

    [ 2 ]

Można również wykonać agregacje w połączeniu z filtrami. Na przykład następujące zapytanie zwraca liczbę dokumentów przy użyciu adresu w stanie Waszyngton.

**Zapytanie**

    SELECT VALUE COUNT(1) 
    FROM Families f
    WHERE f.address.state = "WA" 

**Results**

    [ 1 ]

W poniższej tabeli przedstawiono listę obsługiwanych funkcji agregujących w interfejsie API SQL. `SUM` i `AVG` są realizowane poprzez wartości liczbowych, natomiast `COUNT`, `MIN`, i `MAX` mogą być wykonywane za pośrednictwem liczby, ciągi, wartości logicznych i wartości null. 

| Sposób użycia | Opis |
|-------|-------------|
| LICZBA | Zwraca liczbę elementów w wyrażeniu. |
| SUMA   | Zwraca sumę wszystkich wartości w wyrażeniu. |
| MIN.   | Zwraca minimalną wartość wyrażenia. |
| MAKS.   | Zwraca maksymalną wartość wyrażenia. |
| ŚREDNIA   | Zwraca średnią z wartości w wyrażeniu. |

Agregacje, można również przeprowadzić za pośrednictwem wyników iterację tablicy. Aby uzyskać więcej informacji, zobacz [iteracji tablicy w zapytaniach](#Iteration).

> [!NOTE]
> Korzystając z Eksploratora danych w witrynie Azure portal, należy pamiętać, zapytania agregacji może zwracać częściowo zagregowanych wyników za pośrednictwem strony kwerendy. Zestawy SDK tworzy pojedynczej wartości zbiorczej na wszystkich stronach. 
> 
> W celu wykonywania zapytań agregacji, przy użyciu kodu, potrzebny jest zestaw SDK platformy .NET 1.12.0, zestaw SDK platformy .NET Core 1.1.0 lub zestawu SDK Java 1.9.5 lub nowszej.    
>

## <a id="OrderByClause"></a>Klauzula ORDER BY
Tak jak w ANSI SQL, można dołączyć opcjonalny klauzuli Order By podczas wykonywania zapytania. Klauzula mogą zawierać opcjonalny argument ASC/DESC określić kolejność, w której można pobrać wyniki.

Na przykład w tym miejscu jest zapytanie, które pobiera rodziny, w kolejności według nazwy miasta rezydentnego.

**Zapytanie**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city

**Results**

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

A Oto czasu kwerendy, która pobiera rodziny, w kolejności według daty utworzenia, która jest przechowywana jako liczba reprezentująca epoka, tj, czas, który upłynął od 1 stycznia 1970 r. w sekundach.

**Zapytanie**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC

**Results**

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

## <a id="Advanced"></a>Pojęcia zaawansowane bazy danych i zapytań SQL

### <a id="Iteration"></a>Iteracja
Dodano nową konstrukcję za pośrednictwem **w** — słowo kluczowe w interfejsie API SQL w celu zapewnienia obsługi Iterowanie przez tablice JSON. Źródło FROM zapewnia obsługę dla iteracji. Zacznijmy od następująco:

**Zapytanie**

    SELECT * 
    FROM Families.children

**Results**  

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

Teraz Przyjrzyjmy się inne zapytanie, który wykonuje iterację przez elementy podrzędne w kolekcji. Należy zauważyć różnicę w tablicy danych wyjściowych. Ten przykład dzieli `children` i spłaszcza wyniki w jedną.  

**Zapytanie**

    SELECT * 
    FROM c IN Families.children

**Results**  

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

To dalsze można odfiltrować każdy pojedynczy wpis tablicy, jak pokazano w poniższym przykładzie:

**Zapytanie**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Results**  

    [{
      "givenName": "Lisa"
    }]

Można również wykonać agregacji za pośrednictwem wyniku tablicy iteracji. Na przykład następujące zapytanie zlicza liczbę elementów podrzędnych wśród wszystkich rodzin.

**Zapytanie**

    SELECT COUNT(child) 
    FROM child IN Families.children

**Results**  

    [
      { 
        "$1": 3
      }
    ]

### <a id="Joins"></a>Sprzężenia
W relacyjnej bazie danych ważne jest konieczność join między tabelami. Jest logiczną następstwem do projektowania znormalizowaną schematów. Sprzecznie tego interfejsu API SQL dotyczy modelu dane denormalizowane dokumentów bez schematu. Jest logicznym odpowiednikiem metod na "samosprzężenie".

Składnia, która obsługuje język jest sprzężenia JOIN < from_source2 > < from_source1 >... Dołącz do < from_sourceN >. Ogólne, to zwraca zestaw elementów **N**- krotności (krotki o **N** wartości). Każda krotka zawiera wartości utworzone przez wszystkie aliasy kolekcji Iterowanie po ich odpowiednich zestawów. Innymi słowy jest to pełny iloczyn wektorowy zestawy uczestniczących w sprzężeniu.

W poniższych przykładach pokazano, jak działa klauzuli JOIN. W poniższym przykładzie wynik jest pusty, ponieważ iloczyn wektorowy poszczególnych dokumentów ze źródła i pusty zestaw jest pusty.

**Zapytanie**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Results**  

    [{
    }]


W poniższym przykładzie sprzężenia jest między katalog główny dokumentów i `children` subroot. Jest to produkt między pomiędzy dwoma obiektami JSON. Fakt, że elementy podrzędne są tablicy nie jest w SPRZĘŻENIU efektywne, ponieważ mamy do czynienia z jednym elementem głównym, który jest tablica elementów podrzędnych. Dlatego wynik zawiera tylko dwa wyniki, ponieważ iloczyn wektorowy każdy dokument z tablicą daje dokładnie tylko jeden dokument.

**Zapytanie**

    SELECT f.id
    FROM Families f
    JOIN f.children

**Results**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


Poniższy przykład przedstawia bardziej konwencjonalne join:

**Zapytanie**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**Results**

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



Jest to przede wszystkim należy zauważyć, że `from_source` z **Dołącz** klauzula jest iteratorem. Dlatego przepływu w tym przypadku jest następująca:  

* Rozwiń każdy element podrzędny **c** w tablicy.
* Zastosuj iloczyn z elementem głównym dokumentu **f** z każdego elementu podrzędnego **c** , został spłaszczone w pierwszym kroku.
* Na koniec projektu głównego obiektu **f** name — właściwość samodzielnie. 

Pierwszy dokument (`AndersenFamily`) zawiera tylko jeden element podrzędny, dlatego zestaw wyników zawiera tylko jeden obiekt odpowiadający w tym dokumencie. Drugi dokument (`WakefieldFamily`) zawiera dwa elementy podrzędne. Dlatego iloczyn tworzy oddzielny obiekt dla każdego elementu podrzędnego, powodując dwóch obiektów, po jednym dla każdego elementu podrzędnego odpowiadający tego dokumentu. Pola głównego oba te dokumenty są takie same, tak samo, jak można oczekiwać w produkcie między.

Narzędzie rzeczywistych sprzężenia jest spójnych kolekcji formularza z obejmujących wiele produktów w kształcie, w przeciwnym razie jest trudne do projektu. Co więcej, jak widać w poniższym przykładzie można filtrować na kombinację krotki, że umożliwia użytkownik wybrał warunek określony przez kolekcje ogólne.

**Zapytanie**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets

**Results**

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



W tym przykładzie stanowi naturalne rozszerzenie powyższego przykładu i wykonuje sprzężenie double. Tak iloczyn mogą być wyświetlane jako pseudo-następujący kod:

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

`AndersenFamily` ma jeden element podrzędny, który ma jedno zwierzę. Dlatego iloczyn daje jeden wiersz (1\*1\*1) z tej rodziny. WakefieldFamily jednak ma dwa elementy podrzędne, ale tylko jeden element podrzędny "Jesse" ma zwierząt domowych. Jesse ma dwa zwierzęta, mimo że. Dlatego iloczyn daje 1\*1\*2 = 2 wiersze z tej rodziny.

W następnym przykładzie istnieje dodatkowy filtr na `pet`. Nie obejmuje to wszystkie krotek, gdzie imię zwierzęcia nie jest "W tle". Należy zauważyć, że jesteśmy w stanie tworzyć spójne kolekcje z tablic, filtr na jeden z elementów krotki, i dowolną kombinację elementów projektu. 

**Zapytanie**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**Results**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## <a id="JavaScriptIntegration"></a>Integracja z językiem JavaScript
Usługa Azure Cosmos DB zapewnia model programowania do wykonywania logiki aplikacji JavaScript na podstawie bezpośrednio w kolekcjach pod względem procedur składowanych i wyzwalaczy. Dzięki temu zarówno dla:

* Możliwości transakcyjne operacje CRUD o wysokiej wydajności i zapytania względem dokumentów w kolekcji na podstawie głęboka Integracja środowiska uruchomieniowego JavaScript bezpośrednio wewnątrz aparatu bazy danych. 
* Fizyczne modelowanie przepływu sterowania, zmienną zakresu, przypisania i integracji z transakcjami bazy danych w nim elementów podstawowych obsługi wyjątków. Aby uzyskać więcej informacji na temat obsługi usługi Azure Cosmos DB Integracja z językiem JavaScript można znaleźć w dokumentacji programowania po stronie serwera języka JavaScript.

### <a id="UserDefinedFunctions"></a>Funkcje zdefiniowane przez użytkownika (UDF)
Wraz z typów już zdefiniowanych w tym artykule interfejs API SQL zapewnia obsługę dla zdefiniowane funkcje użytkownika (UDF). W szczególności funkcje skalarne zdefiniowane przez użytkownika są obsługiwane, gdzie deweloperzy mogą przekazać argumenty, zero lub wiele i zwraca wynik jeden argument, który jest ponownie. Każdy z tych argumentów są sprawdzane pod kątem trwa prawne wartości JSON.  

Składania SQL jest rozszerzony do obsługi niestandardowej logiki aplikacji za pomocą tych funkcji, zdefiniowane przez użytkownika. Funkcje zdefiniowane przez użytkownika można zarejestrować za pomocą interfejsu SQL API i odwoływać jako elementu zapytania SQL. W rzeczywistości funkcje zdefiniowane przez użytkownika exquisitely są przeznaczone do wywołania przez zapytania. Jako następstwem ten wybór funkcje zdefiniowane przez użytkownika nie mają dostępu do obiektu kontekstu, które mają inne typy JavaScript (procedury składowane i wyzwalacze). Ponieważ zapytania są wykonywane jako tylko do odczytu, można uruchomić na maszynie podstawowej lub w replikach pomocniczych. W związku z tym funkcje zdefiniowane przez użytkownika są przeznaczone do uruchamiania w replikach pomocniczych, w przeciwieństwie do innych typów języka JavaScript.

Poniżej przedstawiono przykładowy sposób funkcji zdefiniowanej przez użytkownika mogą być rejestrowane w bazie danych usługi Cosmos DB, w szczególności w ramach kolekcji dokumentów.

       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  

Poprzedni przykład tworzy funkcji zdefiniowanej przez użytkownika o nazwie `REGEX_MATCH`. Akceptuje dwóch wartości ciągu JSON `input` i `pattern` i sprawdza, czy pierwszego dopasowania wzorca określonego w drugi przy użyciu funkcji string.match() języka JavaScript.

Teraz możemy użyć tej funkcji zdefiniowanej przez użytkownika w zapytaniu w projekcji. Funkcje zdefiniowane przez użytkownika musi być kwalifikowana za pomocą liter prefiksu "udf". gdy jest wywoływana z w ramach zapytania. 

> [!NOTE]
> Przed 3/17/2015 r. usługi Cosmos DB obsługuje wywołania funkcji zdefiniowanej przez użytkownika bez "udf". prefix like SELECT REGEX_MATCH(). Ten wzorzec wywołującego jest przestarzała.  
> 
> 

**Zapytanie**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**Results**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

Funkcji zdefiniowanej przez użytkownika również można używać wewnątrz filtru, jak pokazano w poniższym przykładzie, również są kwalifikowany za pomocą "udf". Prefiks:

**Zapytanie**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Results**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


W zasadzie funkcje zdefiniowane przez użytkownika są prawidłowe wyrażenia skalarne i mogą być używane w projekcje i filtry. 

Aby rozszerzyć możliwości funkcji UDF, Przyjrzyjmy się za pomocą logikę warunkową inny przykład:

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
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);


Poniżej znajduje się przykład, która wykonuje operacje w funkcji zdefiniowanej przez użytkownika.

**Zapytanie**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f    

**Results**

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


Zgodnie z poprzednim przykłady pokazują, funkcje zdefiniowane przez użytkownika integracji możliwości języka JavaScript przy użyciu interfejsu API SQL zapewnia bogaty interfejs programowalny celu złożonej logiki przedstawiające diagramy proceduralne i warunkowego za pomocą wbudowanych możliwości środowiska wykonawczego języka JavaScript.

Interfejs API SQL zapewnia argumenty do funkcji zdefiniowanych przez użytkownika dla każdego dokumentu w źródle na etapie bieżący (w klauzuli WHERE lub w klauzuli SELECT) przetwarzania funkcji zdefiniowanej przez użytkownika. Wynik jest włączona w ogólnej potoku wykonywania bezproblemowo. Jeśli właściwości określone przez UDF parametry nie są dostępne w wartości JSON, parametr jest traktowany jako niezdefiniowane i dlatego całkowicie pominięto wywołania funkcji zdefiniowanej przez użytkownika. Podobnie jeśli wynikiem funkcji zdefiniowanej przez użytkownika jest niezdefiniowana, go nie znajduje się w wyniku. 

Podsumowując funkcje zdefiniowane przez użytkownika są doskonałe narzędzia pozwalające czy złożoną logikę biznesową jako część zapytania.

### <a name="operator-evaluation"></a>Ocena — operator
Usługa cosmos DB, na mocy bycia bazy danych JSON, rysuje równoleżników przy użyciu operatorów w języku JavaScript i jego semantyki oceny. Gdy usługi Cosmos DB próbuje zachować semantykę JavaScript pod względem obsługi formatu JSON, ocena operacji odbiega w pewnych okolicznościach.

W interfejsie API SQL w odróżnieniu od tradycyjnych SQL typy wartości, często nie są znane dopóki wartość są pobierane z bazy danych. W celu wydajnego wykonywania zapytań, większość operatorów mają wymagania dotyczące typu strict. 

Interfejs API SQL nie wykonuje konwersje niejawne, w przeciwieństwie do języka JavaScript. Na przykład zapytanie takie jak `SELECT * FROM Person p WHERE p.Age = 21` pasuje do dokumentów, które zawierają właściwość wiek, którego wartość to 21. Innych dokumentów, których właściwość wiek odpowiada prawdopodobnie nieskończone odmiany ciągów "21" lub inne, takie jak "021", "21.0", "0021", "00021", nie będzie można dopasować itp. Pozwala to w przeciwieństwie JavaScript, w których wartości ciągu są niejawnie rzutować na liczby (na podstawie operatora, np: ==). Ten wybór jest kluczowe znaczenie dla efektywnego indeksu dopasowywania w interfejsie API SQL. 

## <a name="parameterized-sql-queries"></a>Sparametryzowane zapytania SQL
Usługa cosmos DB obsługuje zapytania z parametrami wyrażone za pomocą znanej \@ notacji. SQL — sparametryzowane zapewnia niezawodne obsługiwanie i anulowania zapewnianego element z danych wprowadzonych przez użytkownika, co uniemożliwia przypadkowe ujawnienie danych przez wstrzyknięcie kodu SQL. 

Można na przykład, napisz zapytanie, które przyjmuje jako parametry nazwisko i adres Stan, a następnie uruchomić go dla różnych wartości nazwisko i adres Stan na podstawie danych wejściowych użytkownika.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

To żądanie następnie można wysłać do usługi Cosmos DB jako sparametryzowanych zapytań JSON, jak pokazano poniżej.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

Argument do góry można ustawić za pomocą sparametryzowanych zapytań, takich jak pokazano poniżej.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

Wartości parametru może być dowolnym prawidłowym kodem JSON (ciągi, liczby, wartości logicznych ma wartość null, nawet wtedy, tablic lub są zagnieżdżone JSON). Ponadto ponieważ usługi Cosmos DB bez schematu, parametry nie są weryfikowane względem dowolnego typu.

## <a id="BuiltinFunctions"></a>Funkcje wbudowane
Usługa cosmos DB obsługuje także szereg wbudowanych funkcji dla typowych operacji, które można używać wewnątrz kwerend, takich jak funkcje zdefiniowane przez użytkownika (UDF).

| Grupy — funkcja          | Operacje                                                                                                                                          |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| Funkcje matematyczne  | ABS, CEILING, EXP, FLOOR, dziennika, LOG10, zasilania, ROUND, logowania, SQRT, KWADRATOWY, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, wartość w RADIANACH, SIN i TAN |
| Typ funkcji sprawdzania | IS_ARRAY, IS_BOOL, IS_NULL i IS_NUMBER, a także is_object —, IS_STRING, IS_DEFINED i IS_PRIMITIVE                                                           |
| Funkcje ciągów        | CONCAT, zawiera, ENDSWITH, INDEX_OF, po lewej stronie, długość, małe, LTRIM, Zastąp, REPLIKACJA, wstecznego, po prawej stronie, RTRIM, STARTSWITH, PODCIĄG i prawym GÓRNYM       |
| Funkcje tablicy         | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH i ARRAY_SLICE                                                                                         |
| Funkcje przestrzenne       | ST_DISTANCE ST_WITHIN, ST_INTERSECTS, ST_ISVALID i ST_ISVALIDDETAILED                                                                           | 

Jeśli aktualnie używasz funkcji zdefiniowanej przez użytkownika (UDF) dla której wbudowanej funkcji jest teraz dostępna, należy użyć odpowiedniej funkcji wbudowanej jak ma to być szybsze uruchamianie i zwiększyć jej wydajność. 

### <a name="mathematical-functions"></a>Funkcje matematyczne
Funkcje matematyczne wykonywanie obliczeń, na podstawie wartości wejściowych, które są przekazywane jako argumenty i zwraca wartość liczbową. W tym miejscu znajduje się tabela obsługiwanych wbudowanych funkcji matematycznych.


| Sposób użycia | Opis |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [[ABS (num_expr)](#bk_abs) | Zwraca wartość bezwzględną (pozytywna) wartość podanego wyrażenia liczbowego. |
| [LIMIT (num_expr)](#bk_ceiling) | Zwraca najmniejszą wartość liczby całkowitej większa lub równa określonej wyrażenia liczbowego. |
| [FLOOR (num_expr)](#bk_floor) | Zwraca największą liczbę całkowitą, mniejsze niż lub równe określonego wyrażenia liczbowego. |
| [EXP (num_expr)](#bk_exp) | Zwraca wykładnik określonego wyrażenia liczbowego. |
| [Dziennik (num_expr [, base])](#bk_log) | Zwraca logarytm naturalny z określonego wyrażenia liczbowego lub logarytm przy użyciu określonej podstawie |
| [LOG10 (num_expr)](#bk_log10) | Zwraca wartości logarytmu base 10, z określonego wyrażenia liczbowego. |
| [ZAOKRĄGLIJ (num_expr)](#bk_round) | Zwraca wartość liczbową zaokrąglone do najbliższej wartości całkowitej. |
| [TRUNC (num_expr)](#bk_trunc) | Zwraca wartość liczbową obcięty do najbliższej wartości całkowitej. |
| [SQRT (num_expr)](#bk_sqrt) | Zwraca pierwiastek kwadratowy z określonego wyrażenia liczbowego. |
| [KWADRAT (num_expr)](#bk_square) | Zwraca kwadrat określonego wyrażenia liczbowego. |
| [ZASILANIA (num_expr, num_expr)](#bk_power) | Zwraca wartość określona możliwości określonego wyrażenia liczbowego. |
| [ZNAK (num_expr)](#bk_sign) | Zwraca wartość logowania (-1, 0, 1) określonego wyrażenia liczbowego. |
| [ACOS (num_expr)](#bk_acos) | Zwraca kąt w radianach, którego cosinus jest określone wyrażenie liczbowe; Skrót cosinus. |
| [ASIN (num_expr)](#bk_asin) | Zwraca kąt w radianach, którego sinus jest określone wyrażenie liczbowe. Jest to również nazywane arcus sinus. |
| [ATAN (num_expr)](#bk_atan) | Zwraca kąt w radianach, którego tangens jest określone wyrażenie liczbowe. Jest to również nazywane tangens. |
| [ATN2 (num_expr)](#bk_atn2) | Zwraca kąt w radianach, między dodatnią osi x i ray ze źródła do miejsca (y, x), gdzie x i y są wartości dwóch wyrażeń określonego float. |
| [COS (num_expr)](#bk_cos) | Zwraca trygonometrycznych cosinus kąta określonego w radianach, określone wyrażenie. |
| [COT (num_expr)](#bk_cot) | Zwraca trygonometrycznych cotangens kąta określonego w radianach, z określonego wyrażenia liczbowego. |
| [STOPNIE (num_expr)](#bk_degrees) | Zwraca odpowiedni kąt w stopniach na potrzeby kąta określonego w radianach. |
| [PI ()](#bk_pi) | Zwraca wartość stała PI. |
| [Wartość w RADIANACH (num_expr)](#bk_radians) | Zwraca wartość w radianach, po wprowadzeniu wyrażenia liczbowego w stopniach. |
| [SIN (num_expr)](#bk_sin) | Zwraca trygonometrycznych sinus określonego kąta w radianach, określone wyrażenie. |
| [TAN (num_expr)](#bk_tan) | Zwraca tangens wyrażenia wejściowego określonego wyrażenia. |

Na przykład można teraz uruchomić zapytania podobne do następującego:

**Zapytanie**

    SELECT VALUE ABS(-4)

**Results**

    [4]

Główna różnica między funkcjami usługi Cosmos DB w porównaniu do ANSI SQL jest, że są one przeznaczone do działają prawidłowo w przypadku danych bez schematu i mieszanych schematu. Na przykład w przypadku dokumentów, w którym właściwość rozmiaru jest nieobecna lub ma wartość nieliczbowych, takich jak "Nieznany", a następnie dokument zostanie pominięty, zamiast zwracać błąd.

### <a name="type-checking-functions"></a>Typ funkcji sprawdzania
Funkcje sprawdzania typu pozwalają sprawdzić typ wyrażenia w ramach zapytania SQL. Sprawdzanie, czy funkcje typu może służyć do określenia typu właściwości w dokumentach na bieżąco, gdy jest to zmienna lub nieznany. W tym miejscu znajduje się tabela obsługiwanym typem wbudowane funkcje sprawdzania.

<table>
<tr>
  <td><strong>Sposób użycia</strong></td>
  <td><strong>Opis</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (wyrażenie)</a></td>
  <td>Zwraca wartość Boolean wskazującą, jeśli typ wartości jest tablicą.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (wyrażenie)</a></td>
  <td>Zwraca wartość Boolean wskazującą, jeśli typ wartości jest wartością logiczną.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (wyrażenie)</a></td>
  <td>Zwraca wartość Boolean wskazującą, jeśli typ wartości ma wartość null.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (wyrażenie)</a></td>
  <td>Zwraca wartość Boolean wskazującą, jeśli typ wartości jest liczbą.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">Is_object — (wyrażenie)</a></td>
  <td>Zwraca wartość Boolean wskazującą, czy typ wartości obiektu JSON.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (wyrażenie)</a></td>
  <td>Zwraca wartość Boolean wskazującą, czy typ wartości ciągu.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (wyrażenie)</a></td>
  <td>Zwraca wartość Boolean wskazującą, jeśli właściwość zostanie przypisana wartość.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (wyrażenie)</a></td>
  <td>Zwraca wartość Boolean wskazującą, czy typ wartości ciągu, liczba, wartość logiczna lub o wartości null.</td>
</tr>

</table>

Korzystanie z tych funkcji, możesz teraz uruchamiać zapytania podobne do następujących:

**Zapytanie**

    SELECT VALUE IS_NUMBER(-4)

**Results**

    [true]

### <a name="string-functions"></a>Funkcje ciągów
Następujące funkcje skalarne wykonania operacji na wartość ciągu wejściowego i zwraca ciąg, wartość liczbowa lub Boolean. W tym miejscu znajduje się tabela funkcje wbudowane ciągów:

| Sposób użycia | Opis |
| --- | --- |
| [DŁUGOŚĆ (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length) |Zwraca liczbę znaków z określonego wyrażenia ciągu |
| [CONCAT (str_expr str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat) |Zwraca ciąg, który jest wynikiem złączenie co najmniej dwóch wartości ciągu. |
| [SUBSTRING (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring) |Zwraca część wyrażenia ciągu. |
| [STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith) |Zwraca wartość logiczną wskazującą, czy pierwszy ciąg wyrażenia rozpoczyna się od drugiego |
| [ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith) |Zwraca wartość logiczną wskazującą, czy pierwszy ciąg wyrażenia kończy się na sekundę |
| [ZAWIERA (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains) |Zwraca wartość logiczną wskazującą, czy pierwszy ciąg wyrażenie zawiera drugą. |
| [INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of) |Zwraca pozycję początkową pierwsze wystąpienie ciągu drugiego ciągu wyrażenia w ramach pierwszego określonego wyrażenia ciągu lub wartość -1, jeśli nie zostanie znaleziony ciąg. |
| [Po lewej stronie (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left) |Zwraca część po lewej stronie ciąg zawierający określoną liczbę znaków. |
| [Po prawej stronie (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right) |Zwraca ciąg zawierający określoną liczbę znaków jego prawej części. |
| [PRZYTP (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim) |Zwraca wyrażenie ciągu, po usuwa spacje wiodące. |
| [PRZYTK (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim) |Zwraca wyrażenie ciągu po obcinanie wszystkie spacje końcowe. |
| [MAŁE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower) |Zwraca wyrażenie ciągu po przekonwertowaniu danych wielkiej litery na małe litery. |
| [GÓRNY (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper) |Zwraca wyrażenie ciągu po przekonwertowaniu danych małej litery na wielkie litery. |
| [Zastąp (str_expr str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace) |Zamienia wszystkie wystąpienia określonej wartości ciągu na inną wartość ciągu. |
| [REPLIKACJA (str_expr, num_expr)](https://docs.microsoft.com/azure/cosmos-db/sql-api-sql-query-reference#bk_replicate) |Wartość ciągu jest powtarzany określoną liczbę razy. |
| [REVERSE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse) |Zwraca wartość ciągu w kolejności odwrotnej. |

Korzystanie z tych funkcji, możesz teraz uruchamiać zapytania podobne do następujących. Na przykład można powrócić nazwę rodziny pisane wielkimi literami w następujący sposób:

**Zapytanie**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Results**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

Lub łączenia ciągów, np. w tym przykładzie:

**Zapytanie**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Results**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Funkcje ciągów można również w klauzuli WHERE, aby filtrować wyniki, podobnie jak w poniższym przykładzie:

**Zapytanie**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Results**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### <a name="array-functions"></a>Funkcje tablicy
Następujące funkcje skalarne wykonania operacji na tablicy wartości wejściowej i zwrócenia liczbowych, wartość logiczna lub tablicy. W tym miejscu znajduje się tabela funkcje wbudowane tablicy:

| Sposób użycia | Opis |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length) |Zwraca liczbę elementów wyrażenia określonej tablicy. |
| [ARRAY_CONCAT (arr_expr arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat) |Zwraca tablicę, która jest wynikiem łączenia dwóch lub więcej wartości tablicy. |
| [ARRAY_CONTAINS (arr_expr, wyrażenie [, bool_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains) |Zwraca wartość Boolean wskazującą, czy tablica zawiera określoną wartość. Można określić, czy dopasowanie pełnej lub częściowej. |
| [ARRAY_SLICE (arr_expr num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice) |Zwraca część wyrażenie tablicy. |

Funkcje tablicy może służyć do manipulowania tablicami w formacie JSON. Na przykład w tym miejscu jest zapytanie, które zwraca wszystkie dokumenty, gdy jeden z elementów nadrzędnych jest "Okrężne Wakefield". 

**Zapytanie**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Results**

    [{
      "id": "WakefieldFamily"
    }]

Można określić częściowego fragmentu pasujące elementy w tablicy. Następujące zapytanie znajdzie wszystkich elementów nadrzędnych za pomocą `givenName` z `Robin`.

**Zapytanie**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)

**Results**

    [{
      "id": "WakefieldFamily"
    }]


Oto inny przykład, który używa ARRAY_LENGTH, aby uzyskać liczbę elementów podrzędnych na rodzinę.

**Zapytanie**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Results**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### <a name="spatial-functions"></a>Funkcje przestrzenne
Usługa cosmos DB obsługuje następujące funkcje wbudowane Otwórz dane geograficzne Consortium (OGC) do wykonywania zapytań na danych geoprzestrzennych. 

<table>
<tr>
  <td><strong>Sposób użycia</strong></td>
  <td><strong>Opis</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Zwraca odległość między dwoma wyrażeniami GeoJSON punkt, wielokąta lub LineString.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Zwraca wartość wskazującą, czy pierwszy obiekt GeoJSON (punkt wielokąta i LineString) znajduje się w drugi obiekt GeoJSON (punkt wielokąta i LineString) wyrażenie logiczne.</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Zwraca wartość wskazującą, czy dwa określone obiekty GeoJSON (punkt wielokąta i LineString) intersect wyrażenia logicznego.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Zwraca wartość logiczną wskazującą, czy określone wyrażenie GeoJSON punkt, wielokąta lub LineString jest nieprawidłowy.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Zwraca wartość JSON, zawierająca wartość logiczną wartość, jeśli określone wyrażenie GeoJSON punkt, wielokąta lub LineString jest prawidłowy, a nieprawidłowy, dodatkowo Przyczyna jako wartość ciągu.</td>
</tr>
</table>

Funkcje przestrzenne może służyć do wykonywania zapytań dotyczących odległości dla danych przestrzennych. Na przykład w tym miejscu jest zapytanie, które zwraca wszystkie rodziny dokumenty, które znajdują się w 30 km określonej lokalizacji za pomocą wbudowanych funkcji ST_DISTANCE. 

**Zapytanie**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Results**

    [{
      "id": "WakefieldFamily"
    }]

Aby uzyskać więcej informacji na temat obsługi dane geograficzne w usłudze Cosmos DB, zobacz [pracę z danymi dane geograficzne w usłudze Azure Cosmos DB](geospatial.md). To wszystko na funkcje przestrzenne i składnia SQL usługi Cosmos DB. Teraz Przyjrzyjmy się w sposób wysyłania zapytań do działania i sposób jej interakcji z użyciem składni LINQ widzieliśmy do tej pory.

## <a id="Linq"></a>LINQ do interfejsu API SQL
LINQ to model programowania .NET i wyraża obliczenie jako kwerendy dla strumieni obiektów. Usługa cosmos DB udostępnia bibliotekę klienta interfejsu za pomocą LINQ przez ułatwienie konwersji między obiektami JSON i platformy .NET i mapowanie podzbiór zapytań LINQ do zapytań usługi Cosmos DB. 

Na rysunku poniżej przedstawiono architekturę obsługi zapytań LINQ, za pomocą usługi Cosmos DB.  Za pomocą klienta usługi Cosmos DB, deweloperzy mogą tworzyć **IQueryable** obiekt, który wysyła zapytanie bezpośrednio dostawcy zapytanie usługi Cosmos DB, który przetwarza zapytania LINQ do kwerendy usługi Cosmos DB. Zapytania są następnie przekazywane do serwera usługi Cosmos DB do pobierania zestawu wyników w formacie JSON. Zwrócone wyniki są przeprowadzona deserializacja strumienia obiektów .NET po stronie klienta.

![Architektura obsługi zapytań LINQ za pomocą interfejsu API SQL - składni języka SQL, język zapytań JSON, pojęć dotyczących baz danych i zapytań SQL][1]

### <a name="net-and-json-mapping"></a>Mapowanie JSON i platformy .NET
Mapowanie między obiektami platformy .NET i dokumentów JSON jest naturalnym — każde pole składowej danych jest zamapowany na obiekt JSON, gdzie nazwa pola jest mapowany do "key" część obiektu, a część "value" to rekursywnie mapowane na wartość część obiektu. Rozważmy następujący przykład: rodziny obiekt utworzony jest mapowany na dokumencie JSON, jak pokazano poniżej. I odwrotnie, dokument JSON jest mapowane z powrotem na obiekt .NET.

**Klasa C#**

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


**JSON**  

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



### <a name="linq-to-sql-translation"></a>LINQ do SQL tłumaczenia
Dostawca zapytanie usługi Cosmos DB wykonuje najlepsze mapowania nakład pracy w wyniku zapytania LINQ do zapytań SQL usługi Cosmos DB. W poniższym opisie przyjęto założenie, że czytelnik ma podstawowe znajomość LINQ.

Najpierw dla typu systemu, firma Microsoft wspiera wszystkich JSON pierwotnych typów — typy liczbowe, atrybut typu wartość logiczna, ciąg i wartości null. Obsługiwane są tylko następujące typy JSON. Poniższe wyrażenia skalarne są obsługiwane.

* Wartości stałe — obejmują one stałe wartości pierwotnych typów danych w czasie, gdy zapytanie jest.
* Wyrażenia indeksu tablicy/właściwości tych wyrażeń odwołać się do właściwości elementu tablicy lub obiektu.
  
     Rodzina. ID;    Family.Children[0].familyName;    Family.Children[0].Grade;    Family.Children[n].Grade; n jest zmienną int
* Wyrażenia arytmetyczne - należą do nich wspólnych wyrażeniach arytmetycznych na wartościach liczbowych i logicznych. Pełną listę można znaleźć w specyfikacji SQL.
  
     2 * family.children[0].grade;    x + y;
* Wyrażenie porównania ciągu - należą do porównywania wartości ciągu na wartość stałą ciągu.  
  
     mother.familyName == "Smith";    child.givenName == s; s jest zmienną ciągu
* / Tablicę obiektów wyrażenie tworzenia — te wyrażenia return tablicę obiektów lub obiektu typu złożonego wartości lub typu anonimowego. Te wartości mogą być zagnieżdżone.
  
     Nowy element nadrzędny {familyName = "Smith", givenName = "Jan"}; nowe {najpierw = 1, drugi = 2;} Typ anonimowy z elementem dwa pola              
     New int [] {3, child.grade, 5};

### <a id="SupportedLinqOperators"></a>Lista obsługiwanych operatorów LINQ
Poniżej przedstawiono listę obsługiwanych operatorów LINQ w dostawcy LINQ, dołączone do zestawu .NET SDK SQL.

* **Wybierz**: projekcje przełożyć na SQL SELECT, łącznie z konstrukcji obiektu
* **Gdzie**: filtry przełożyć na WHERE języka SQL i obsługują tłumaczenie między & &, || i! te operatory SQL
* **SelectMany**: umożliwia odwijanie tablic do klauzuli SQL JOIN. Może służyć do łańcucha/zagnieżdżanie wyrażeń do filtrowania elementów tablicy
* **OrderBy i OrderByDescending**: przekłada się na ORDER BY rosnąco/malejąco
* **Liczba**, **suma**, **Min**, **Max**, i **średni** operatorów agregacji i ich odpowiedniki async **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, i **AverageAsync**.
* **Element CompareTo**: przekłada się na zakres porównania. Często używane do ciągów, ponieważ nie jest porównywalny na platformie .NET
* **Wykonaj**: przekłada się na GÓRNYM SQL do ograniczania wyników z zapytania
* **Funkcje matematyczne**: obsługuje translację. NET firmy Abs, funkcje Acos, Asin, Atan, Ceiling Cos, Exp, Floor, Log, Log10, Pow, Round, logowania, Sin, Sqrt, Tan, Truncate na równoważne funkcje wbudowane SQL.
* **Ciąg funkcje**: obsługuje translację. NET firmy Concat, Contains, EndsWith, IndexOf, liczba, ToLower, trimstart —, Zastąp, wstecznego, trimend —, StartsWith, podciąg, ToUpper na równoważne funkcje wbudowane SQL.
* **Funkcje tablicy**: obsługuje translację. Concat, zawiera i liczby na równoważne funkcje wbudowane SQL w sieci.
* **Funkcje rozszerzenia Geoprzestrzenne**: obsługuje translację metod klasy zastępczej odległość, w ramach IsValid i IsValidDetailed na równoważne funkcje wbudowane SQL.
* **Zdefiniowane przez użytkownika funkcji rozszerzenia funkcji**: obsługuje tłumaczenia z metody klasy zastępczej UserDefinedFunctionProvider.Invoke do odpowiednich funkcji zdefiniowanej przez użytkownika.
* **Różne**: obsługuje tłumaczenia coalesce i operatorów warunkowych. Można tłumaczyć zawiera ciąg zawiera, ARRAY_CONTAINS lub IN SQL, w zależności od kontekstu.

### <a name="sql-query-operators"></a>Operatory zapytań SQL
Poniżej przedstawiono kilka przykładów, które ilustrują sposób przekształcania są niektóre standardowe operatory zapytań LINQ do zapytań usługi Cosmos DB.

#### <a name="select-operator"></a>Wybierz Operator
Składnia jest `input.Select(x => f(x))`, gdzie `f` jest wyrażenie skalarne.

**Wyrażenie lambda LINQ**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**Wyrażenie lambda LINQ**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**Wyrażenie lambda LINQ**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### <a name="selectmany-operator"></a>SelectMany operator
Składnia jest `input.SelectMany(x => f(x))`, gdzie `f` to wyrażenie skalarne, które zwraca typ kolekcji.

**Wyrażenie lambda LINQ**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>Gdy operator
Składnia jest `input.Where(x => f(x))`, gdzie `f` jest wyrażenie skalarne, które zwraca wartość typu Boolean.

**Wyrażenie lambda LINQ**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**Wyrażenie lambda LINQ**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### <a name="composite-sql-queries"></a>Złożone zapytania SQL
Operatory powyżej może być złożone w celu utworzenia bardziej zaawansowane kwerendy. Ponieważ usługi Cosmos DB obsługuje kolekcje zagnieżdżone, kompozycji może być połączonych lub zagnieżdżonych.

#### <a name="concatenation"></a>Łączenie
Składnia jest `input(.|.SelectMany())(.Select()|.Where())*`. Zapytania połączonych można uruchomić z opcjonalnymi `SelectMany` zapytania, a następnie za pomocą wielu `Select` lub `Where` operatorów.

**Wyrażenie lambda LINQ**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**Wyrażenie lambda LINQ**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**Wyrażenie lambda LINQ**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**Wyrażenie lambda LINQ**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### <a name="nesting"></a>Zagnieżdżanie
Składnia jest `input.SelectMany(x=>x.Q())` gdzie funkcji pytania i odpowiedzi jest `Select`, `SelectMany`, lub `Where` operatora.

W zapytaniem zagnieżdżonym zapytanie wewnętrzne są stosowane do każdego elementu kolekcji zewnętrznego. Jeden z ważną funkcją jest, że zapytanie wewnętrzne mogą odwoływać się do pól elementów w kolekcji zewnętrzne, takie jak samosprzężenia.

**Wyrażenie lambda LINQ**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**Wyrażenie lambda LINQ**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**Wyrażenie lambda LINQ**

    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## <a id="ExecutingSqlQueries"></a>Wykonywanie zapytania SQL
Usługa cosmos DB udostępnia zasoby za pośrednictwem interfejsu API REST, który można wywoływać za pomocą dowolnego języka realizującego żądania HTTP/HTTPS. Ponadto usługi Cosmos DB oferuje biblioteki programistyczne dla kilku popularnych języków, takich jak .NET, Node.js, JavaScript i Python. Interfejs API REST i różnych bibliotek obsługują wykonywanie zapytań za pomocą języka SQL. Zestaw .NET SDK obsługuje LINQ podczas badania oprócz SQL.

Poniższe przykłady pokazują, jak tworzyć zapytania i przesłać go do konta bazy danych Cosmos DB.

### <a id="RestAPI"></a>INTERFEJS API REST
Usługa cosmos DB oferuje otwarte model programowania RESTful przy użyciu protokołu HTTP. Konta bazy danych mogą być udostępniane przy użyciu subskrypcji platformy Azure. Model zasobów usługi Cosmos DB zawiera zestaw zasobów w ramach konta bazy danych, z których każdy jest adresy logicznych i stabilnych identyfikatora URI. Zestaw zasobów, jest określany jako źródła danych w tym dokumencie. Konto bazy danych zawiera zestaw baz danych, każdy z nich zawierający wiele kolekcji, a każdy z których umożliwiłoby zawiera dokumenty, funkcje zdefiniowane przez użytkownika i innych typów zasobów.

Model interakcji podstawowa przy użyciu tych zasobów jest za pomocą polecenia HTTP GET, PUT, POST i DELETE przy użyciu ich standardowego interpretacji. Czasownik WPIS jest używany do tworzenia nowego zasobu, wykonywanie procedury przechowywanej lub zapytania usługi Cosmos DB. Zapytania są zawsze operacji tylko do odczytu przy użyciu efektów ubocznych.

W poniższych przykładach pokazano WPIS dla interfejsu API SQL zapytanie wykonywane względem kolekcji, zawierający dwie przykładowe dokumenty gdy poznaliśmy już do tej pory. Zapytanie ma filtr prosty na nazwy właściwości JSON. Zwróć uwagę na użycie `x-ms-documentdb-isquery` i Content-Type: `application/query+json` nagłówki, aby wskazać, czy operacja się zapytania.

**Żądanie**

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


**Results**

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


Drugi przykład przedstawia bardziej złożonego zapytania, które zwraca wiele wyników sprzężenia.

**Żądanie**

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


**Results**

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


Jeśli wyniki zapytania nie mieści się na jednej stronie wyników, a następnie interfejsu API REST zwraca token kontynuacji za pośrednictwem `x-ms-continuation-token` nagłówka odpowiedzi. Klienci mogą stronicowanie wyników, łącznie z nagłówkiem w kolejnych wyników. Można także kontrolować liczbę wyników na stronę za pośrednictwem `x-ms-max-item-count` numer nagłówka. Jeśli określona kwerenda ma funkcję agregacji, takich jak `COUNT`, a następnie na stronie zapytania mogą zwracać częściowo zagregowaną wartość za pośrednictwem strony wyników. Klienci, należy wykonać agregacji drugiego poziomu, za pośrednictwem tych wyników do wygenerowania wyników końcowych, na przykład, Suma za pośrednictwem liczby zwracanych w poszczególnych stron, aby zwrócić łączna liczba.

Aby zarządzać zasadami spójności danych dla zapytania, należy użyć `x-ms-consistency-level` nagłówek, np. wszystkie żądania interfejsu API REST. W celu zapewnienia spójności sesji jest wymagane również echo najnowsze `x-ms-session-token` nagłówek Cookie żądania zapytania. Zasady indeksowania kolekcji kwerendy także mogą mieć wpływ na spójność wyników zapytania. Za pomocą domyślne ustawienia zasad indeksowania, zbierania danych indeks jest zawsze aktualny wraz z zawartością dokumentu i wyników zapytania pasuje spójności dla danych. Jeśli na leniwy przestała obowiązywać zasady indeksowania zapytania mogą zwracać wyniki starych. Aby uzyskać więcej informacji, zobacz [poziomów spójności systemu Azure Cosmos DB][consistency-levels].

Jeśli skonfigurowane zasady indeksowania w kolekcji nie obsługuje określonego zapytania, serwer usługi Azure Cosmos DB zwraca 400 "złe żądanie". Ta wartość jest zwracana dla zapytania zakresowe względem ścieżki skonfigurowane dla wyszukiwań wyznaczania wartości skrótu (równości), a dla ścieżek, które jawnie wykluczone z indeksowania. `x-ms-documentdb-query-enable-scan` Nagłówka można określić, aby zezwolić na zapytanie, aby przeprowadzić skanowanie, gdy indeks nie jest dostępna.

Możesz uzyskać szczegółowe metryki na wykonanie zapytania, ustawiając `x-ms-documentdb-populatequerymetrics` nagłówka do `True`. Aby uzyskać więcej informacji, zobacz [metryki zapytania SQL usługi Azure Cosmos DB](sql-api-sql-query-metrics.md).

### <a id="DotNetSdk"></a>C# (.NET) SDK
Zestaw .NET SDK obsługuje zarówno LINQ, jak i SQL zapytań. Poniższy przykład pokazuje, jak wykonać zapytanie filtru proste wprowadzono we wcześniejszej części tego dokumentu.

    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Ten przykład porównanie dwóch właściwości pod kątem równości w ramach każdego dokumentu i wykorzystuje anonimowy projekcji. 

    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Następny przykład pokazuje sprzężenia, wyrażonych za pośrednictwem LINQ SelectMany.

    foreach (var pet in client.CreateDocumentQuery(collectionLink,
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
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



Klient modelu .NET automatycznie wykonuje iterację przez wszystkie strony wyników zapytania w blokach instrukcji foreach, jak pokazano powyżej. Opcje zapytania zostanie wprowadzony w sekcji interfejsu API REST są również dostępne w przy użyciu zestawu .NET SDK `FeedOptions` i `FeedResponse` klas w metodzie CreateDocumentQuery. Liczbę stron, które mogą być kontrolowane za pomocą `MaxItemCount` ustawienie. 

Można także jawnie kontrolować stronicowania, tworząc `IDocumentQueryable` przy użyciu `IQueryable` obiektu, a następnie, czytając` ResponseContinuationToken` wartości i przekazywania ich z powrotem jako `RequestContinuationToken` w `FeedOptions`. `EnableScanInQuery` można ustawić tak, aby włączyć skanowanie, gdy zapytanie nie może być obsługiwana przez skonfigurowane zasady indeksowania. W przypadku kolekcji podzielonych na partycje, można użyć `PartitionKey` do uruchomienia zapytania względem pojedynczej partycji (Chociaż usługi Cosmos DB można automatycznie prowadzenie to tekst zapytania), a `EnableCrossPartitionQuery` do uruchamiania zapytań, które mogą wymagać można uruchamiać wiele partycji. 

Zapoznaj się [przykładów usługi Azure Cosmos DB .NET](https://github.com/Azure/azure-documentdb-net) więcej przykładów, zawierający zapytania. 

### <a id="JavaScriptServerSideApi"></a>Interfejs API języka JavaScript po stronie serwera
Usługa cosmos DB zapewnia model programowania do wykonywania logiki aplikacji JavaScript na podstawie bezpośrednio w kolekcji przy użyciu procedur składowanych i wyzwalaczy. Logika JavaScript zarejestrowany na poziomie kolekcji następnie mogą wyzwalać operacje bazy danych na operacje na dokumentach w danej kolekcji. Te operacje są opakowane w transakcje ACID otoczenia.

Poniższy przykład pokazuje, jak queryDocuments w serwerze JavaScript API służą do zapytania z wewnątrz procedur składowanych i wyzwalaczy.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a id="References"></a>Odwołania
1. [Wprowadzenie do usługi Azure Cosmos DB][introduction]
2. [Usługa Azure Cosmos DB SQL specyfikacji](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3. [Przykłady usługi Azure Cosmos DB .NET](https://github.com/Azure/azure-documentdb-net)
4. [Poziomy spójności usługi Azure Cosmos DB][consistency-levels]
5. ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6. JSON [http://json.org/](http://json.org/)
7. Specyfikacja języka JavaScript [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8. LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9. Technik oceny wykonania zapytania w przypadku dużych baz danych [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Przetwarzania zapytań w systemy równoległe relacyjnych baz danych, komputer IEEE Press społeczeństwa 1994 r.
11. Tan — lu, Ooi, przetwarzania w systemach równoległe relacyjnej bazy danych, komputer IEEE Press społeczeństwa 1994 r. zapytań.
12. Christopher Olston Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: Pig Latin: nie aby obcym języku do przetwarzania danych, SIGMOD 2008.
13. G. Graefe. Kaskady umożliwiająca optymalizacji zapytań. Eng. danych IEEE Bull., 18(3): 1995.

[1]: ./media/sql-api-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
