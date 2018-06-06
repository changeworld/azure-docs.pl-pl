---
title: Zapytania SQL dla bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o składni SQL, pojęcia bazy danych i zapytania SQL dla bazy danych Azure rozwiązania Cosmos. SQL może być używany jako język kwerendy JSON w usłudze Azure DB rozwiązania Cosmos.
keywords: Składnia SQL, zapytanie sql, zapytania sql, język zapytań json, koncepcje bazy danych i zapytania sql, funkcje agregujące
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
ms.openlocfilehash: f0fd1b57be07eda13655b5a6c0dcb5b412e8a248
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798325"
---
# <a name="sql-queries-for-azure-cosmos-db"></a>Zapytania SQL dla bazy danych Azure rozwiązania Cosmos

Bazy danych programu Microsoft Azure rozwiązania Cosmos obsługuje tworzenie zapytań dla dokumentów przy użyciu programu SQL (Structured Query Language) jako język kwerendy JSON na kontach interfejsu API SQL. Azure DB rozwiązania Cosmos jest rzeczywiście bez schematu. Ze względu na jego zobowiązania do modelu danych JSON bezpośrednio wewnątrz aparatu bazy danych zapewnia automatycznego indeksowania dokumentów JSON bez konieczności jawnego schematu lub tworzenia indeksów pomocniczych.

Podczas projektowania rozwiązania Cosmos DB język zapytań, było dwóch celów pamiętać:

* Zamiast inventing nowy język zapytań JSON, możemy obsługuje język SQL. SQL jest jednym z znanych i najbardziej popularnych języków zapytania. Rozwiązania cosmos bazy danych SQL zapewnia model programowania posiadanie zaawansowane zapytania przez dokumentów JSON.
* Jako dokument bazy danych JSON może zostać uruchomiona JavaScript bezpośrednio w aparacie bazy danych trzeba użyć modelu programowania języka JavaScript jako podstawa dla naszych języka zapytań. Interfejsu API SQL jest umieszczone w systemie typów języka JavaScript, wyrażenia i wywołania funkcji. To w Włącz zapewnia fizyczną model programowania dla projekcje relacyjnych, hierarchicznych nawigacji między dokumentów JSON, self sprzężenia zapytania przestrzenne i wywołania funkcji zdefiniowanej przez użytkownika (UDF) zapisywane w całości w języku JavaScript, między innymi funkcjami. 

Mamy nadzieję, że te możliwości są kluczem do zmniejszenia tarcia pomiędzy aplikacją i bazy danych i decydują o produktywność deweloperów.

Zalecamy rozpoczęcie pracy od obejrzenia poniższego klipu wideo, gdzie Azure Menedżera programów DB rozwiązania Cosmos Andrew Liu pokazuje możliwości podczas badania DB rozwiązania Cosmos Azure i prezentuje online [Plac zabaw dla zapytań](http://www.documentdb.com/sql/demo), gdzie można wypróbować Azure Rozwiązania cosmos bazy danych oraz wykonywania kwerendy SQL naszym zestawie danych, jak pokazano w wideo.

> [!VIDEO https://www.youtube.com/embed/1LqUQRpHfFI]
>
>

Bardziej zaawansowane techniki podczas badania ilustrują ta flaga monitująca wideo:

> [!VIDEO https://www.youtube.com/embed/kASU9NOIR90]
>
>

Następnie wróć do tego artykułu, gdzie Możemy zaczynać się samouczek zapytania SQL, który przeprowadzi Cię przez niektóre prostych dokumentów JSON i polecenia SQL.

## <a id="GettingStarted"></a>Wprowadzenie do poleceń SQL w bazie danych rozwiązania Cosmos
Aby wyświetlić rozwiązania Cosmos bazy danych SQL w miejscu pracy, umożliwia rozpoczyna się od kilku prostych dokumentów JSON i zapoznaj się z kilku prostych kwerend na nim. Należy wziąć pod uwagę następujące dwa dokumenty JSON o dwie grupy. DB rozwiązania Cosmos nie musimy jawnie Utwórz schematy ani indeksów pomocniczych. Musimy po prostu wstawiania dokumentów JSON do kolekcji DB rozwiązania Cosmos, a następnie zapytania. W tym miejscu mamy proste JSON dokumentów dla rodziny Andersen, nadrzędne elementy podrzędne (i ich zwierząt domowych), adresu i informacji o rejestracji. Dokument ma ciągów, liczb, wartości logiczne, tablic i właściwości zagnieżdżonych. 

**Dokument**  

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

W tym miejscu jest drugi dokument o jedną niewielka różnica — `givenName` i `familyName` są używane zamiast `firstName` i `lastName`.

**Dokument**  

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

Teraz spróbujmy kilka zapytań dotyczących tych danych, aby poznać niektóre z kluczowych aspektów język zapytań SQL Azure rozwiązania Cosmos DB. Na przykład następujące zapytanie zwraca dokumentów, jeśli w polu identyfikatora odpowiada `AndersenFamily`. Ponieważ jest ono `SELECT *`, wyniki kwerendy jest cały dokument JSON:

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


Teraz Rozważmy przypadek, w których należy sformatować dane wyjściowe JSON w różnych kształtu. To zapytanie projekcję obiekt JSON z dwóch wybranych pól, nazwa i Miasto, gdy ten adres miasto ma taką samą nazwę jak stan. W takim przypadku "NY, NY" jest zgodny.

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


Dalej zapytania zwraca wszystkie nazwy podanej dzieci w rodzinie odpowiada o identyfikatorze `WakefieldFamily` uporządkowanych według Miasto zamieszkania.

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


Chcemy zwrócić uwagę na kilka aspektów warte wymienienia DB rozwiązania Cosmos język zapytania za pomocą którego możemy wykonanej do tej pory przedstawiono przykłady:  

* Ponieważ interfejsu API SQL działa na wartości JSON, dotyczy drzewa w kształcie jednostek zamiast wierszy i kolumn. W związku z tym języka umożliwia tak samo, jak można znaleźć węzły drzewa w dowolnej dowolnego głębokości `Node1.Node2.Node3…..Nodem`, podobny do relacyjnej bazy danych SQL odwołujących się do odwołania dwie części `<table>.<column>`.   
* Język structured query language współpracuje z danych bez schematu. W związku z tym system typów należy powiązać dynamicznie. To samo wyrażenie można użyć instrukcji yield różnych typów w różnych dokumentach. Wynik kwerendy jest prawidłową wartością JSON, ale nie musi być typu stałego schematu.  
* Rozwiązania cosmos bazy danych obsługuje tylko ścisłe dokumentów JSON. Oznacza to, że system typów i wyrażenia są ograniczone do radzenia tylko z typami JSON. Zapoznaj się [specyfikacji JSON](http://www.json.org/) więcej szczegółów.  
* Kolekcja DB rozwiązania Cosmos jest kontenerem dokumentów JSON bez schematu. Relacji w danych jednostki w obrębie dokumentów w kolekcji oraz niejawnie są przechwytywane przez zamknięcia, a nie przez klucz podstawowy i relacje klucza obcego. To jest istotnym elementem warto wskazujące świetle sprzężenia wewnątrz dokumentu omówiony w dalszej części tego artykułu.

## <a id="Indexing"></a> Indeksowanie rozwiązania cosmos bazy danych
Przed uzyskujemy do składni SQL, warto eksploracji indeksowania projektu w usłudze Azure DB rozwiązania Cosmos. 

Indeksy bazy danych służy do obsługi zapytań w różnych formularzach i kształtów zużycie zasobów minimalną (na przykład procesora CPU i operacjami wejścia/wyjścia) zapewniają dobrą wydajność i małe opóźnienia. Często wybór indeks prawo do wykonywania zapytań w bazie danych wymaga dużo planowania i eksperymenty. Takie podejście stanowi wyzwanie dla baz danych bez schematu, gdzie dane nie są zgodne z ograniczeniami schematu i rozwoju szybko. 

W związku z tym podczas projektowania możemy podsystemu indeksowania rozwiązania Cosmos DB, możemy ustawić następujące cele:

* Indeksować dokumenty bez konieczności schematu: indeksowania podsystemu nie wymagają żadnych informacji o schemacie ani nie przyjmuje żadnych założeń dotyczących schematu dokumentów. 
* Obsługę wydajne, zaawansowanych zapytań hierarchicznej i relacyjne: indeks obsługuje język kwerendy DB rozwiązania Cosmos wydajnie, tym obsługę projekcje hierarchiczne i relacyjny.
* Obsługa zapytań spójne in face of trwały wolumin zapisów: zapisu wysokiej przepływności obciążeń z zapytaniami spójne, indeks jest zaktualizowane przyrostowo, wydajne i online w wypadku trwały wolumin zapisów. Aktualizacja spójne indeksu jest niezwykle ważny do obsługi zapytań na poziomie spójności konfigurowane usługi dokumentu przez użytkownika.
* Obsługa wielu dzierżawców: podana oparte na rezerwacjach modelu dla zarządu zasobów między dzierżawcami, indeksu aktualizacji są wykonywane w ramach budżetu zasobów systemowych (CPU, pamięci i liczby operacji wejścia/wyjścia na sekundę) przydzielone dla replik. 
* Wydajność magazynu: opłacalności, narzut na przechowywanie na dysku, indeksu jest ograniczone i przewidywalne. Jest to ważne, ponieważ DB rozwiązania Cosmos umożliwia deweloperowi kompromisy opartych na kosztach między narzut indeksu w odniesieniu do wydajności zapytania.  

Zapoznaj się [przykłady Azure DB rozwiązania Cosmos](https://github.com/Azure/azure-documentdb-net) w witrynie MSDN dla przykładów przedstawiająca sposób skonfigurować zasady indeksowania dla kolekcji. Teraz Załóż do szczegółów składni Azure rozwiązania Cosmos bazy danych SQL.

## <a id="Basics"></a>Podstawowe informacje o kwerendzie Azure rozwiązania Cosmos bazy danych SQL
Co kwerenda składa się z klauzuli SELECT i opcjonalnie FROM i klauzulach WHERE w standardach ANSI SQL. Zazwyczaj dla każdego zapytania wyliczyć jest źródła w klauzuli FROM. Następnie w źródle pobrać podzbiór dokumentów JSON jest stosowany filtr w klauzuli WHERE. Na koniec klauzuli SELECT jest używana do projektu wymagane wartości JSON na liście wyboru.

    SELECT <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a id="FromClause"></a>Klauzula FROM
`FROM <from_specification>` Klauzuli jest opcjonalny, jeśli źródło jest filtrowana lub przewidywane później w zapytaniu. Celem tej klauzuli jest aby określić źródło danych, na którym musi działać zapytania. Często całą kolekcję jest źródłem, ale zamiast tego można określić podzestaw kolekcji. 

Zapytanie, takich jak `SELECT * FROM Families` oznacza całą kolekcję rodzin źródła służącym do wyliczenia. Identyfikator specjalne głównego może służyć do reprezentowania kolekcji zamiast nazwę kolekcji. Poniższa lista zawiera reguły, które są wymuszane na zapytanie:

* Kolekcji można używać z aliasem, takich jak `SELECT f.id FROM Families AS f` lub po prostu `SELECT f.id FROM Families f`. W tym miejscu `f` jest odpowiednikiem `Families`. `AS` jest to identyfikator optional-słowo kluczowe do aliasu.
* Raz, ponieważ oryginalne źródło nie może być powiązany. Na przykład `SELECT Families.id FROM Families f` ma nieprawidłową składnię, ponieważ już nie można rozpoznać identyfikatora "Rodzin".
* Wszystkie właściwości, które muszą odwoływać się musi być w pełni kwalifikowana. W przypadku braku schematu ścisłego przestrzegania ta wartość jest wymuszana w celu uniknięcia powiązań niejednoznaczne. W związku z tym `SELECT id FROM Families f` ma nieprawidłową właściwość `id` nie jest powiązany.

### <a name="subdocuments"></a>Dokumenty podrzędne
Źródła można zmniejszyć w taki sposób, aby mniejszego podzestawu. Na przykład do wyliczania tylko poddrzewo do każdego dokumentu, subroot może następnie stać się źródłowego, jak pokazano w poniższym przykładzie:

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

Powyższy przykład tablicy są używane jako źródło, obiektu również mogą być użyte jako źródło, w którym jest, co przedstawiono w poniższym przykładzie: wszelkie prawidłową wartość JSON (nie zdefiniowano), który znajduje się w źródle jest uwzględnione w wyniku zapytania. Jeśli nie masz niektórych rodzin `address.state` wartość, są wyłączone w wyniku zapytania.

**Zapytanie**

    SELECT * 
    FROM Families.address.state

**Results**

    [
      "WA", 
      "NY"
    ]


## <a id="WhereClause"></a>Klauzula WHERE
Klauzula WHERE (**`WHERE <filter_condition>`**) jest opcjonalna. Określa się, że warunki, dokumentów JSON zapewnianej przez źródło musi spełniać, aby zostać zawarte w ramach wyniku. Każdy dokument JSON musi być określone warunki na "true", aby zostać uwzględnione w wyniku. Klauzula WHERE jest używana przez warstwę indeksu aby można było określić bezwzględny najmniejszą podzbiór dokumentów źródłowych, które mogą być częścią wynik. 

Następujące zapytanie żądań dokumentów, które zawierają właściwość name o wartości `AndersenFamily`. Każdy dokument, który nie ma właściwości name, lub gdy wartość jest niezgodna `AndersenFamily` jest wyłączone. 

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


W poprzednim przykładzie pokazano zapytania prostego równości. Interfejsu API SQL obsługuje również wiele wyrażeń skalarnych. Wyrażenia binarne i jednoargumentowy są najczęściej używane. Odwołań do właściwości z obiektu JSON źródła są również prawidłowe wyrażenia. 

Następujące operatory binarne są obecnie obsługiwane i mogą być używane w zapytaniach, jak pokazano w poniższych przykładach:  

<table>
<tr>
<td>Operacje arytmetyczne</td>    
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>Alternatywy bitowej</td>    
<td>|, &, ^, <<>>,, >>> (zero wypełnienia przesunięcia w prawo)</td>
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


Operatory jednoargumentowe +,-, ~ nie są również obsługiwane i może być używany w zapytaniach, jak pokazano w poniższym przykładzie:

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



Oprócz operatorów binarnych i jednoargumentowe dozwolone są też odwołań do właściwości. Na przykład `SELECT * FROM Families f WHERE f.isRegistered` zwraca dokument JSON zawierający właściwości `isRegistered` gdy wartość właściwości jest równa JSON `true` wartość. Inne wartości (wartość false, wartość null, niezdefiniowane, `<number>`, `<string>`, `<object>`, `<array>`, itd.) prowadzi do dokumentu źródłowego są wykluczone z wyników. 

### <a name="equality-and-comparison-operators"></a>Operatory równości i porównania
W poniższej tabeli przedstawiono wynik porównania równości w interfejsie API SQL między żadnych dwa typy JSON.

<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>OP</strong>
         </td>
         <td valign="top">
            <strong>Niezdefiniowana</strong>
         </td>
         <td valign="top">
            <strong>Wartość null</strong>
         </td>
         <td valign="top">
            <strong>Wartość logiczna</strong>
         </td>
         <td valign="top">
            <strong>Numer</strong>
         </td>
         <td valign="top">
            <strong>Ciąg</strong>
         </td>
         <td valign="top">
            <strong>Obiekt</strong>
         </td>
         <td valign="top">
            <strong>Tablica</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Niezdefiniowana<strong>
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
            <strong>Wartość logiczna<strong>
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
            <strong>Ciąg<strong>
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
            <strong>Obiekt<strong>
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

Dla innych operatory porównania, takich jak >, > =,! =, < i < = następujące zasady stosowane:   

* Porównanie różnych typów powoduje Undefined.
* Porównanie między dwoma obiektami lub dwóch stałych powoduje Undefined.   

Jeśli wynikiem wyrażenia skalarne w filtrze jest niezdefiniowana, odpowiedni dokument nie będzie uwzględniony w wyniku, ponieważ Undefined logicznego nie są równoważne "true".

### <a name="between-keyword"></a>MIĘDZY — słowo kluczowe
Umożliwia także BETWEEN — słowo kluczowe do wyrażenia zapytania względem zakresów wartości podobnie jak w języku SQL ANSI. MIĘDZY może służyć przed ciągów lub cyfry.

Na przykład ta kwerenda zwraca wszystkie dokumenty rodziny, w których klasa pierwszym elementem podrzędnym jest między 1-5, (zarówno z wartościami granicznymi). 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

W odróżnieniu od w ANSI SQL umożliwia także klauzuli BETWEEN w klauzuli FROM, podobnie jak w poniższym przykładzie.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Dla krótszego czasu wykonywania zapytania Pamiętaj, aby utworzyć zasady indeksowania, które korzysta z typu indeksu zakresu względem dowolnego liczbowego właściwości/ścieżek, które są filtrowane w klauzuli BETWEEN. 

Główną różnicą między przy użyciu BETWEEN interfejsu API SQL i ANSI SQL jest czy można wyrazić zakresu zapytań dotyczących właściwości mieszane typy — na przykład może być "klasy" być liczbą (5) w niektórych dokumentów i ciągi w innych ("grade4"). W takich przypadkach takich jak w języku JavaScript, porównanie dwóch różnych typów wyników w "undefined" i dokument zostanie pominięte.

### <a name="logical-and-or-and-not-operators"></a>Logiczne (AND, OR i NOT) operatory
Operatory logiczne działają na wartości logiczne. W poniższych tabelach przedstawiono tabel logicznych prawdy dla tych operatorów.

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

### <a name="in-keyword"></a>IN — słowo kluczowe
IN — słowo kluczowe może służyć do sprawdzenia, czy określona wartość odpowiada wartości na liście. Na przykład ta kwerenda zwraca wszystkie dokumenty rodziny których identyfikator jest "WakefieldFamily" lub "AndersenFamily". 

    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

W tym przykładzie zwraca wszystkie dokumenty, których stan jest dowolną z określonymi wartościami.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### <a name="ternary--and-coalesce--operators"></a>Operatory łączonej (?) i trójargumentowy (?)
Operatory Trzyargumentowe i Coalesce może służyć do tworzenia wyrażenia warunkowego, podobnie jak popularnych języków programowania, takich jak C# i JavaScript. 

Operator trójargumentowy (?) może być bardzo przydatne podczas tworzenia nowych właściwości JSON na bieżąco. Na przykład możesz teraz napisać zapytania klasyfikowanie poziomy klasy w człowieka czytelnej postaci jak początkujących/pośredni/zaawansowane jak pokazano poniżej.

     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

Również można zagnieździć wywołań operator podobnie jak w poniższym zapytaniu.

    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Jako z innymi operatorami zapytania, jeśli brakuje właściwości do którego istnieje odwołanie w wyrażeniu warunkowym, dokumentów lub typy są porównywane są różne, następnie te dokumenty są wyłączone w wynikach zapytania.

Operator łączonej (?) umożliwia wydajne sprawdzić obecność właściwości () zdefiniowano) w dokumencie. Jest to przydatne podczas wykonywania zapytania względem częściową strukturą lub mieszane typów danych. Na przykład to zapytanie zwraca "nazwisko", jeśli istnieje, lub "nazwisko" Jeśli nie jest obecny.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### <a id="EscapingReservedKeywords"></a>Metody dostępu właściwości cudzysłowie
Można także przejść do właściwości za pomocą operatora ujętego w cudzysłów właściwości `[]`. Na przykład `SELECT c.grade` i `SELECT c["grade"]` są równoważne. Ta składnia jest przydatne, gdy trzeba wprowadzić właściwości, która zawiera spacje i znaki specjalne lub stanie się o tej samej nazwie jako słowa kluczowego SQL lub zastrzeżony wyraz.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a id="SelectClause"></a>Klauzula SELECT
Klauzula SELECT (**`SELECT <select_list>`**) jest obowiązkowy i określa, jakie są one pobierane zapytanie, tak jak w języku SQL ANSI. Podzbiór jest zostały przefiltrowane na podstawie dokumentów źródłowych są przekazywane na projekcji fazy, gdy określone wartości JSON są pobierane i jest tworzony nowy obiekt JSON dla każdego dane wejściowe przekazane do jej. 

Poniższy przykład przedstawia typowy zapytania SELECT. 

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


### <a name="nested-properties"></a>Właściwości zagnieżdżonych
W poniższym przykładzie mamy projekcji dwie właściwości zagnieżdżonych `f.address.state` i `f.address.city`.

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


Przyjrzyjmy się rola `$1` tutaj. `SELECT` Klauzuli potrzebuje do utworzenia obiektu JSON i ponieważ podany żaden klucz, używamy nazwy zmiennych argumentów niejawnego począwszy `$1`. Na przykład ta kwerenda zwraca dwóch zmiennych argumentów niejawnego etykietą `$1` i `$2`.

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


### <a name="aliasing"></a>Aliasów
Teraz załóżmy rozszerzenia tego przykładu powyżej z jawnym aliasów wartości. Jest to słowo kluczowe użyte dla aliasów. Jest opcjonalne, jak pokazano podczas projekcji drugiej wartości jako `NameInfo`. 

W przypadku, gdy zapytanie zawiera dwie właściwości o tej samej nazwie, należy użyć aliasów można zmienić nazwy jedną lub obie właściwości, dzięki czemu są one rozróżniane w wyniku planowane.

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


### <a name="scalar-expressions"></a>Wyrażenia skalarne
Oprócz odwołań do właściwości w klauzuli SELECT obsługuje również skalarne wyrażenia stałe, wyrażenia arytmetyczne, wyrażenie logiczne itd. Na przykład w tym miejscu jest prostego zapytania "Hello World".

**Zapytanie**

    SELECT "Hello World"

**Results**

    [{
      "$1": "Hello World"
    }]


Oto przykład bardziej złożone wyrażenie skalarne.

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


### <a name="object-and-array-creation"></a>Tworzenie obiektu i tablicy
Inna funkcja klucza interfejsu API SQL jest tworzenie tablicy i obiektów. W poprzednim przykładzie należy pamiętać, że utworzono nowy obiekt JSON. Analogicznie jeden również utworzyć tablic jak pokazano w poniższych przykładach:

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

### <a id="ValueKeyword"></a>VALUE — słowo kluczowe
**Wartość** — słowo kluczowe udostępnia sposób zwrócenia wartość JSON. Na przykład poniższe zapytanie zwraca skalarnych `"Hello World"` zamiast `{$1: "Hello World"}`.

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

Poniższy przykład rozszerza to demonstrujące sposób zwrócić wartości pierwotnych JSON (poziomu liścia drzewa JSON). 

**Zapytanie**

    SELECT VALUE f.address.state
    FROM Families f    

**Results**

    [
      "WA",
      "NY"
    ]


### <a name="-operator"></a>* — Operator
Operator specjalne (*) jest obsługiwane do projektu dokumentu — jest. Gdy jest używany, musi być tylko pola planowane. Podczas zapytania, takie jak `SELECT * FROM Families f` jest prawidłowy, `SELECT VALUE * FROM Families f ` i `SELECT *, f.id FROM Families f ` nie są prawidłowe.

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

### <a id="TopKeyword"></a>TOP Operator
Słowo kluczowe TOP pozwala ograniczyć liczbę wartości z zapytania. W GÓRNEJ jest używany w połączeniu z klauzulą ORDER BY, zestaw wyników jest ograniczone do pierwsza liczba N uporządkowanej wartości. w przeciwnym razie zwraca pierwsze N liczba wyników w kolejności niezdefiniowany. Najlepszym rozwiązaniem w instrukcji SELECT, zawsze za pomocą klauzuli ORDER BY w klauzuli TOP. To jest jedynym sposobem, aby jednoznacznie wskazują wiersze, które wpływają na GÓRZE. 

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

Z wartością stałą (jak pokazano powyżej) lub wartość zmiennej użycie zapytań sparametryzowanych można TOP. Aby uzyskać więcej informacji zapoznaj się z zapytań sparametryzowanych poniżej.

### <a id="Aggregates"></a>Funkcje agregujące
Można również wykonywać agregacji w `SELECT` klauzuli. Funkcje agregujące wykonywanie obliczeń na zestaw wartości i zwraca pojedynczą wartość. Na przykład następujące zapytanie zwraca liczbę rodziny dokumentów w kolekcji.

**Zapytanie**

    SELECT COUNT(1) 
    FROM Families f 

**Results**

    [{
        "$1": 2
    }]

Można także wrócić przy użyciu wartości skalarnych agregacji `VALUE` — słowo kluczowe. Na przykład następujące zapytanie zwraca liczbę wartości jako jeden numer:

**Zapytanie**

    SELECT VALUE COUNT(1) 
    FROM Families f 

**Results**

    [ 2 ]

Można również wykonywać wartości zagregowanych w połączeniu z filtrami. Na przykład następujące zapytanie zwraca liczbę dokumentów przy użyciu adresu w stanie Waszyngton.

**Zapytanie**

    SELECT VALUE COUNT(1) 
    FROM Families f
    WHERE f.address.state = "WA" 

**Results**

    [ 1 ]

W poniższej tabeli przedstawiono listę obsługiwanych funkcji agregujących w interfejsie API SQL. `SUM` i `AVG` są wykonywane za pośrednictwem wartości liczbowe, podczas gdy `COUNT`, `MIN`, i `MAX` mogą być wykonywane za pośrednictwem liczb i ciągów, wartości logiczne oraz wartości null. 

| Sposób użycia | Opis |
|-------|-------------|
| LICZBA | Zwraca liczbę elementów w wyrażeniu. |
| SUMA   | Zwraca sumę wszystkich wartości w wyrażeniu. |
| MIN.   | Zwraca minimalną wartość wyrażenia. |
| MAKS.   | Zwraca maksymalną wartość wyrażenia. |
| ŚREDNIA   | Zwraca średnią z wartości wyrażenia. |

Można również przeprowadzić agregacje w wynikach iteracji tablicy. Aby uzyskać więcej informacji, zobacz [iteracji tablicy w zapytaniach](#Iteration).

> [!NOTE]
> Podczas używania Eksploratora danych portalu Azure, należy pamiętać, zapytania agregacji może zwrócić wyników zagregowany częściowo za pośrednictwem strony zapytania. Zestawy SDK tworzy pojedynczej wartości zbiorczej na wszystkich stronach. 
> 
> W celu wykonywania zapytań agregacji przy użyciu kodu, potrzebny jest zestaw .NET SDK 1.12.0, zestawu SDK programu .NET Core 1.1.0 lub zestawu Java SDK 1.9.5 lub nowszej.    
>

## <a id="OrderByClause"></a>Klauzula ORDER BY
Podobnie jak w ANSI SQL, można dołączyć opcjonalny klauzuli Order By podczas wykonywania zapytania. Klauzuli mogą zawierać opcjonalny argument ASC/DESC, aby określić kolejność, w którym można pobrać wyników.

Na przykład w tym miejscu jest kwerendę, która pobiera rodzin w kolejności nazwę miejscowości rezydentna.

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

I w tym czasie kwerendę, która pobiera rodzin w kolejności Data utworzenia, który jest przechowywany jako liczba reprezentująca epoka, tj, czas, który upłynął od 1 stycznia 1970 w sekundach.

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

## <a id="Advanced"></a>Pojęcia zaawansowane bazy danych i zapytania SQL

### <a id="Iteration"></a>Iteracji
Dodano nową konstrukcję za pośrednictwem **IN** — słowo kluczowe w interfejsie API SQL w celu zapewnienia obsługi Iterowanie przez tablice notacji JSON. Źródło FROM zapewnia obsługę iteracji. Zacznijmy od poniższym przykładzie:

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

Teraz Przyjrzyjmy się inne zapytanie, który wykonuje iterację na elementy podrzędne w kolekcji. Należy zwrócić uwagę różnica w tablicy danych wyjściowych. W tym przykładzie dzieli `children` i spłaszcza wyniki do jednej macierzy.  

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

To dodatkowe można filtrować według każdego wpisu poszczególnych tablicy, jak pokazano w poniższym przykładzie:

**Zapytanie**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Results**  

    [{
      "givenName": "Lisa"
    }]

Można również wykonywać agregacji na wyniku iteracji tablicy. Na przykład poniższe zapytanie zlicza liczbę elementów podrzędnych wśród wszystkich rodzin.

**Zapytanie**

    SELECT COUNT(child) 
    FROM child IN Families.children

**Results**  

    [
      { 
        "$1": 3
      }
    ]

### <a id="Joins"></a>Tworzy sprzężenie
W relacyjnej bazie danych ważne jest konieczność dołączanie w tabelach. Jest logiczną następstwem projektowania znormalizowane schematów. Sprzeczności z tego interfejsu API SQL podchodzi do modelu danych nieznormalizowany dokumentów bez schematu. Jest to odpowiednik logiczny a "samosprzężenie".

Składnia, obsługujący język jest sprzężenia sprzężenia < from_source2 > < from_source1 >... Przyłącz < from_sourceN >. Ogólne, to zwraca zbiór **N**- krotek (krotki o **N** wartości). Każda krotka zawiera wartości utworzonego przez Iterowanie wszystkie aliasy kolekcji po ich odpowiednich zestawów. Innymi słowy jest to pełny iloczyn wektorowy zestawów uczestniczących sprzężenia.

W poniższych przykładach pokazano, jak działa klauzuli JOIN. W poniższym przykładzie wyniku jest pusta, ponieważ iloczyn wektorowy każdego dokumentu ze źródła, a pusty zestaw jest pusty.

**Zapytanie**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Results**  

    [{
    }]


W poniższym przykładzie sprzężenie jest między głównego dokumentu i `children` subroot. Jest to produkt między między dwoma obiektami JSON. Fakt, że elementy podrzędne jest tablicy nie jest w SPRZĘŻENIU skuteczne, ponieważ firma Microsoft ma do czynienia z jednym elementem głównym, który jest tablica elementów podrzędnych. Dlatego wynik zawiera tylko dwa wyników, ponieważ dokładnie tylko jeden dokument Zwraca iloczyn wektorowy każdy dokument z tablicą.

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


W poniższym przykładzie przedstawiono bardziej z konwencjonalnej sprzężenia:

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



Jest to przede wszystkim należy pamiętać, że `from_source` z **JOIN** klauzula jest iteratora. Dlatego przepływ w tym przypadku jest następujący:  

* Rozwiń węzeł każdego elementu podrzędnego **c** w tablicy.
* Zastosuj iloczyn wektorowy z elementem głównym dokumentu **f** z każdym elementem podrzędnym **c** który został spłaszczone w pierwszym kroku.
* Na koniec projektu obiekt główny **f** name — właściwość samodzielnie. 

Pierwszy dokument (`AndersenFamily`) zawiera tylko jeden element podrzędny, w związku z czym zestaw wyników zawiera tylko jeden obiekt odpowiadający w tym dokumencie. Drugi dokument (`WakefieldFamily`) zawiera dwa elementy podrzędne. Tak iloczyn wektorowy tworzy oddzielny obiekt dla każdego elementu podrzędnego, powodując dwa obiekty, po jednej dla każdego elementu podrzędnego odpowiadający tego dokumentu. Pola głównego oba te dokumenty są takie same, zgodnie z oczekiwaniami w iloczyn wektorowy.

Narzędzie rzeczywistych sprzężenia jest krotek formularza z iloczyn wektorowy kształtu, który jest trudne do projektu. Co więcej, jak przedstawiono w poniższym przykładzie można odfiltrować kombinację krotka, że umożliwia wybranym przez użytkownika warunek spełniać krotki ogólne.

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



W tym przykładzie stanowi naturalne rozszerzenie powyższego przykładu i wykonuje sprzężenie dwa razy. Tak można wyświetlić iloczyn wektorowy jako pseudo-następujący kod:

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

`AndersenFamily` ma jeden element podrzędny, który ma jedno zwierzę. Dlatego iloczyn wektorowy zwraca jeden wiersz (1\*1\*1) z tej rodziny. WakefieldFamily, ale zawiera dwa elementy podrzędne, ale tylko jeden element podrzędny "Jesse" ma zwierząt domowych. Jesse jednak ma dwa zwierząt domowych. Dlatego iloczyn wektorowy daje 1\*1\*2 = 2 wierszy z tej rodziny.

W następnym przykładzie na jest dodatkowy filtr `pet`. Obejmuje to wszystkich krotek, gdzie nazwa pet nie jest "Cienia". Zwróć uwagę, że jesteśmy w stanie Tworzenie spójnych kolekcji z tablic, filtr na jeden z elementów spójnej kolekcji, i dowolną kombinację elementów projektu. 

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


## <a id="JavaScriptIntegration"></a>Integracja języka JavaScript
Azure DB rozwiązania Cosmos zapewnia model programowania do wykonywania logiki aplikacji JavaScript na podstawie bezpośrednio w kolekcjach pod względem procedur składowanych i wyzwalaczy. Dzięki temu zarówno dla:

* Możliwości transakcyjnych operacji CRUD wysokiej wydajności i zapytania względem dokumentów w kolekcji na podstawie głęboka Integracja środowiska wykonawczego języka JavaScript bezpośrednio wewnątrz aparatu bazy danych. 
* Fizyczne modelowania przepływu sterowania, zmiennej zakresu i przypisania i integracja z transakcji bazy danych w nim elementów podstawowych obsługi wyjątków. Aby uzyskać więcej informacji o obsłudze bazy danych Azure rozwiązania Cosmos integracji JavaScript zapoznaj się dokumentacją programowania po stronie serwera JavaScript.

### <a id="UserDefinedFunctions"></a>Funkcje zdefiniowane przez użytkownika (UDF)
Wraz z typów zdefiniowanych w tym artykule interfejsu API SQL zapewnia obsługę dla użytkownika określone funkcje (UDF). W szczególności skalarne funkcji UDF są obsługiwane, gdy deweloperzy można przekazywać do zera lub wiele argumentów i zwracanie wyniku pojedynczy argument ponownie. Każdy z tych argumentów jest sprawdzany pod kątem trwa wartości JSON.  

Składnia SQL jest rozszerzony do obsługi niestandardowej logiki aplikacji przy użyciu tych funkcji zdefiniowanych przez użytkownika. Funkcje UDF może być zarejestrowane przy użyciu interfejsu API SQL i odwoływać jako część zapytania SQL. W rzeczywistości funkcje UDF exquisitely są przeznaczone do wywołania przez zapytania. Jako następstwem ta opcja funkcje UDF nie mają dostępu do obiektu kontekstu, które mają inne typy JavaScript (procedury składowane i wyzwalaczy). Od czasu wykonania zapytania jako tylko do odczytu, można uruchomić na podstawowym lub w replikach pomocniczych. W związku z tym funkcje UDF są przeznaczone do uruchamiania w replikach pomocniczych, w odróżnieniu od innych typów języka JavaScript.

Poniżej przedstawiono przykładowy sposób funkcji zdefiniowanej przez użytkownika może być zarejestrowany w rozwiązania Cosmos DB bazy danych, a w szczególności w kolekcji dokumentów.

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

W poprzednim przykładzie jest tworzony UDF, którego nazwa jest `REGEX_MATCH`. Akceptuje dwóch wartości ciągu JSON `input` i `pattern` i sprawdza, czy pierwszy dopasowania wzorca określony w ciągu sekundy przy użyciu funkcji string.match() języka JavaScript.

Firma Microsoft mogą teraz używać tej funkcji w zapytaniu w projekcji. Funkcje UDF musi być kwalifikowana z uwzględnieniem wielkości liter prefiks "udf." Po wywołaniu z wewnątrz zapytania. 

> [!NOTE]
> Przed 2015-3-17 DB rozwiązania Cosmos obsługiwane wywołania funkcji zdefiniowanej przez użytkownika bez "udf." prefix like SELECT REGEX_MATCH(). Ten wzorzec wywołującego jest przestarzała.  
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

Funkcję zdefiniowaną przez użytkownika również może być używany w filtrze, jak pokazano w poniższym przykładzie również kwalifikowany za pomocą "udf." Prefiks:

**Zapytanie**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Results**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


W zasadzie funkcje UDF są prawidłowe wyrażenia skalarne i mogą być używane w zarówno projekcje i filtry. 

Aby rozszerzyć możliwości funkcji UDF, Przyjrzyjmy się innym przykładem w logice warunkowego:

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


Poniżej znajduje się przykład sprawdzający funkcję zdefiniowaną przez użytkownika.

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


Zgodnie z poprzednim przykłady pokazują, funkcje UDF zintegrować możliwości języka JavaScript przy użyciu interfejsu API SQL zapewnia bogaty interfejs programowalny celu złożonej logiki procedurach, warunkowego za pomocą wbudowanych możliwości środowiska wykonawczego języka JavaScript.

Interfejsu API SQL umożliwia argumentów do funkcji UDF dla każdego dokumentu w źródle na etapie bieżący (w klauzuli WHERE lub w klauzuli SELECT) przetwarzania funkcję zdefiniowaną przez użytkownika. Wynik jest włączona w ogólnej potoku wykonywania bezproblemowo. Jeśli właściwości określonym przez funkcję zdefiniowaną przez użytkownika nie są dostępne w formacie JSON wartości parametrów, parametr jest uznawany za niezdefiniowane i dlatego wywołania funkcji zdefiniowanej przez użytkownika jest całkowicie pominięty. Podobnie jeśli zdefiniowano wynik UDF go nie jest uwzględniony w wyniku. 

Podsumowując funkcje UDF są doskonałe narzędzia celu złożonej logiki biznesowej w ramach zapytania.

### <a name="operator-evaluation"></a>Ocena — operator
Rozwiązania cosmos bazy danych, na mocy jest bazą danych JSON, rysuje równoleżników JavaScript — operatory i jego semantyki oceny. Gdy DB rozwiązania Cosmos próbuje zachować semantyki JavaScript pod względem obsługi JSON, oceny operacji odbiega w niektórych przypadkach.

W interfejsie API SQL w przeciwieństwie do tradycyjnych SQL typy wartości, często nie są znane aż do wartości są pobierane z bazy danych. Aby wydajnie wykonywanie zapytań, większość operatorów mają wymagania dotyczące typu strict. 

Interfejsu API SQL nie działa niejawne konwersje, w przeciwieństwie do języka JavaScript. Na przykład, takich jak kwerendy `SELECT * FROM Person p WHERE p.Age = 21` odpowiada dokumentów, które zawierają właściwość wieku, którego wartość to 21. Innych dokumentów, których właściwość wieku odpowiada ciągu "21" lub innych zmian prawdopodobnie nieskończone, takich jak "021", "21.0", "0021", "00021", nie będzie można dopasować itp. Pozwala to z kolei JavaScript, w których wartości ciągu są niejawnie rzutowana na numery (na podstawie operatora, np: ==). Ten wybór jest kluczowe znaczenie dla efektywnego indeksu pasujące w interfejsie API SQL. 

## <a name="parameterized-sql-queries"></a>Sparametryzowane zapytania SQL
Rozwiązania cosmos bazy danych obsługuje zapytania z parametrami wyrażone ze znanym @ notacji. Sparametryzowane SQL zapewnia niezawodne obsługi i anulowanie z danych wprowadzonych przez użytkownika, uniemożliwia przypadkowe ujawnienie danych za pomocą iniekcji kodu SQL. 

Można na przykład napisz zapytanie, które przyjmuje jako parametry nazwisko i adres Stan i wykonaj go dla różnych wartości nazwisko i adres Stan oparte na danych wejściowych użytkownika.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

To żądanie następnie można wysłać do rozwiązania Cosmos bazy danych jako sparametryzowanych zapytań JSON, jak pokazano poniżej.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

Argument do góry można skonfigurować użycie zapytań sparametryzowanych, jak pokazano poniżej.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

Wartości parametru może być dowolnym poprawne dane JSON (ciągów, liczb, wartości logiczne, null, nawet tablice lub zagnieżdżone JSON). Również DB rozwiązania Cosmos jest bez schematu, parametry nie są weryfikowane względem dowolnego typu.

## <a id="BuiltinFunctions"></a>Funkcje wbudowane
Rozwiązania cosmos bazy danych obsługuje również kilka wbudowanych funkcji typowych operacji, które mogą być używane wewnątrz zapytań, takich jak funkcje zdefiniowane przez użytkownika (UDF).

| Grupy — funkcja          | Operacje                                                                                                                                          |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| Funkcje matematyczne  | ABS, limitu, EXP, FLOOR, dziennika, LOG10, zasilania, ROUND, logowania, SQRT, KWADRATOWYCH, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, KOT, stopni PI, wartość w RADIANACH, SIN i TAN |
| Typ funkcji sprawdzania | Is_array —, IS_BOOL IS_NULL, IS_NUMBER, is_object —, IS_STRING, IS_DEFINED i IS_PRIMITIVE                                                           |
| Funkcje ciągów        | CONCAT, zawiera ENDSWITH, INDEX_OF, po lewej, długość, małe, LTRIM, ZAMIEŃ, REPLIKACJA, ODWROTNIE, prawo, RTRIM, STARTSWITH, PODCIĄG i górna       |
| Funkcje tablicy         | ARRAY_CONCAT, ARRAY_CONTAINS ARRAY_LENGTH i ARRAY_SLICE                                                                                         |
| Funkcje przestrzenne       | ST_DISTANCE, ST_WITHIN ST_INTERSECTS, ST_ISVALID i ST_ISVALIDDETAILED                                                                           | 

Jeśli obecnie używasz — funkcja zdefiniowana przez użytkownika (UDF) dla której wbudowana funkcja jest teraz dostępna, należy użyć odpowiedniego wbudowana funkcja jak ma to być szybsze uruchamianie i bardziej efektywnie. 

### <a name="mathematical-functions"></a>Funkcje matematyczne
Funkcje matematyczne wykonywanie obliczeń, na podstawie wartości wejściowych, które są przekazywane jako argumenty i zwracać wartość liczbową. W tym miejscu znajduje się tabela obsługiwanych wbudowanych funkcji matematycznych.


| Sposób użycia | Opis |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [[ABS (num_expr)](#bk_abs) | Zwraca wartość bezwzględną (dodatnia) z określonego wyrażenia liczbowego. |
| [CEILING (num_expr)](#bk_ceiling) | Zwraca najmniejszą wartość całkowita większa lub równa określonej wyrażenia liczbowego. |
| [FLOOR (num_expr)](#bk_floor) | Zwraca największą liczbę całkowitą mniejszą niż określona wyrażenia liczbowego. |
| [EXP (num_expr)](#bk_exp) | Zwraca wykładnik określonego wyrażenia liczbowego. |
| [Dziennik (num_expr [, base])](#bk_log) | Zwraca logarytm naturalny z określonego wyrażenia liczbowego lub przy użyciu określonej podstawy logarytmu |
| [LOG10 (num_expr)](#bk_log10) | Zwraca wartość logarytmicznej base 10 określonego wyrażenia liczbowego. |
| [ZAOKRĄGLIJ (num_expr)](#bk_round) | Zwraca wartość liczbową zaokrąglona do najbliższej wartości całkowitej. |
| [TRUNC (num_expr)](#bk_trunc) | Zwraca wartość liczbową obcięte do najbliższej wartości całkowitej. |
| [SQRT (num_expr)](#bk_sqrt) | Zwraca pierwiastek kwadratowy z określonego wyrażenia liczbowego. |
| [(Num_expr) KWADRATU](#bk_square) | Zwraca kwadrat określonego wyrażenia liczbowego. |
| [ZASILANIA (num_expr, num_expr)](#bk_power) | Zwraca wartość określona zasilania z określonego wyrażenia liczbowego. |
| [ZNAK (num_expr)](#bk_sign) | Zwraca wartość logowania (-1, 0, 1) z określonego wyrażenia liczbowego. |
| [ACOS (num_expr)](#bk_acos) | Zwraca kąt w radianach, którego cosinus jest określonego wyrażenia liczbowego; Skrót cosinus. |
| [ASIN (num_expr)](#bk_asin) | Zwraca kąt w radianach, którego sinusem jest dana określonego wyrażenia liczbowego. Jest to również sinus. |
| [ATAN (num_expr)](#bk_atan) | Zwraca kąt w radianach, którego tangens jest określonego wyrażenia liczbowego. Jest to również tangens. |
| [ATN2 (num_expr)](#bk_atn2) | Zwraca kąt w radianach, między dodatnią osi x i promień od źródła do punktu (y, x), gdzie x i y wartości dwóch wyrażeń określonego typu float. |
| [COS (num_expr)](#bk_cos) | Zwraca cosinus trygonometryczne o określony kąt w radianach, określone wyrażenie. |
| [KOT (num_expr)](#bk_cot) | Zwraca trygonometryczne cotangens kąta określonego w radianach, określonego wyrażenia liczbowego. |
| [STOPNI (num_expr)](#bk_degrees) | Zwraca odpowiadający mu kąt w stopniach dla kąta określonego w radianach. |
| [PI ()](#bk_pi) | Zwraca stałą wartość liczby PI. |
| [Wartość w RADIANACH (num_expr)](#bk_radians) | Zwraca wartość w radianach, po wprowadzeniu w stopniach, wyrażenia liczbowego. |
| [SIN (num_expr)](#bk_sin) | Zwraca sinus określonego kąta trygonometryczne w radianach, określone wyrażenie. |
| [TAN (num_expr)](#bk_tan) | Zwraca tangens wyrażenie wejściowe określonego wyrażenia. |

Na przykład można teraz uruchomić zapytania podobne do poniższych:

**Zapytanie**

    SELECT VALUE ABS(-4)

**Results**

    [4]

Główna różnica pomiędzy rozwiązania Cosmos DB funkcji w porównaniu do ANSI SQL jest, że są one przeznaczone do działają prawidłowo w przypadku danych bez schematu i mieszanych schematu. Na przykład jeśli masz dokument, w którym brakuje właściwości Size lub ma wartości nieliczbowe, takich jak "Nieznany", a następnie dokumentu jest pominięty, zamiast zwróciła błąd.

### <a name="type-checking-functions"></a>Typ funkcji sprawdzania
Funkcje sprawdzania typu umożliwiają sprawdzanie typu wyrażenia w obrębie zapytania SQL. Sprawdzanie, czy funkcje typu może służyć do określania typu właściwości w dokumentach na bieżąco, kiedy jest zmienna lub nieznany. W tym miejscu znajduje się tabela obsługiwanym typem wbudowane funkcje kontroli.

<table>
<tr>
  <td><strong>Użycie</strong></td>
  <td><strong>Opis</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">Is_array — (wyrażenie)</a></td>
  <td>Zwraca wartość Boolean wskazującą, czy typ wartości jest tablicą.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (wyrażenie)</a></td>
  <td>Zwraca wartość Boolean wskazującą, czy typ wartości jest wartością logiczną.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (wyrażenie)</a></td>
  <td>Zwraca wartość Boolean wskazującą, czy typ wartości ma wartość null.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (wyrażenie)</a></td>
  <td>Zwraca wartość Boolean wskazującą, czy typ wartości jest liczbą.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">Is_object — (wyrażenie)</a></td>
  <td>Zwraca wartość Boolean wskazującą, czy typ wartości jest obiektem JSON.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (wyrażenie)</a></td>
  <td>Zwraca wartość Boolean wskazującą, czy typ wartości jest ciągiem.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (wyrażenie)</a></td>
  <td>Zwraca wartość Boolean wskazującą, czy właściwość zostanie przypisana wartość.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (wyrażenie)</a></td>
  <td>Zwraca wartość Boolean wskazującą, czy typ wartości jest ciąg, liczbę, Boolean lub wartość null.</td>
</tr>

</table>

Przy użyciu tych funkcji, można teraz uruchomić zapytania podobne do poniższych:

**Zapytanie**

    SELECT VALUE IS_NUMBER(-4)

**Results**

    [true]

### <a name="string-functions"></a>Funkcje ciągów
Następujące funkcje skalarne wykonania operacji w ciągu wartości wejściowej i zwraca ciąg, wartość liczbowa lub wartość logiczna. W tym miejscu jest tabela funkcji wbudowanych ciągu:

| Sposób użycia | Opis |
| --- | --- |
| [DŁUGOŚĆ (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length) |Zwraca liczbę znaków z określonego wyrażenia ciągu |
| [CONCAT (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat) |Zwraca ciąg, który jest wynikiem łączenie dwóch lub więcej wartości ciągu. |
| [SUBSTRING (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring) |Zwraca część wyrażenia ciągu. |
| [STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith) |Zwraca wartość Boolean wskazującą, czy pierwszy wyrażenia ciągu rozpoczyna się od drugiego |
| [ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith) |Zwraca wartość Boolean wskazującą, czy pierwszy wyrażenia ciągu kończy się na sekundę |
| [ZAWIERA (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains) |Zwraca wartość Boolean wskazującą, czy pierwszy wyrażenia ciągu zawiera drugi. |
| [INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of) |Zwraca pozycję początkową pierwszego wystąpienia drugi ciąg wyrażenia w pierwszym wyrażeniu określony ciąg lub wartość -1, jeśli nie zostanie znaleziony ciąg. |
| [LEFT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left) |Zwraca lewej części ciągu z określoną liczbę znaków. |
| [RIGHT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right) |Zwraca prawa część ciągu z określoną liczbę znaków. |
| [PRZYTP (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim) |Zwraca wyrażenie ciągu, po usuwa spacje wiodące. |
| [PRZYTK (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim) |Zwraca wyrażenie ciągu po obcinanie wszystkie spacje końcowe. |
| [MAŁE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower) |Zwraca wyrażenie ciągu po konwersji danych wielką literę na małe litery. |
| [Górna (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper) |Zwraca wyrażenie ciągu po konwersji danych małą literę na wielkie litery. |
| [Zastąp (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace) |Zamienia wszystkie wystąpienia określonej wartości ciągu na inną wartość ciągu. |
| [REPLIKACJA (str_expr, num_expr)](https://docs.microsoft.com/azure/cosmos-db/sql-api-sql-query-reference#bk_replicate) |Wartość ciągu jest powtarzany określoną liczbę razy. |
| [REVERSE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse) |Zwraca odwrotnej kolejności wartość ciągu. |

Przy użyciu tych funkcji, można teraz uruchomić zapytania podobne do następujących. Na przykład można zwrócić nazwę rodziny wielkie litery w następujący sposób:

**Zapytanie**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Results**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

Lub ciągów podobnie jak w tym przykładzie:

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


Funkcje ciągów można również w klauzuli WHERE, które mają być filtrowane wyniki, podobnie jak w poniższym przykładzie:

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
Następujące funkcje skalarne wykonania operacji na tablicy wartości wejściowej i powrotu liczbowego, wartość logiczną lub tablicy. W tym miejscu jest tabela funkcji wbudowanej tablicy:

| Sposób użycia | Opis |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length) |Zwraca liczbę elementów wyrażenia określonej tablicy. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat) |Zwraca tablicę, która jest wynikiem łączenie dwóch lub więcej wartości tablicy. |
| [ARRAY_CONTAINS (arr_expr, wyrażenie [, bool_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains) |Zwraca wartość Boolean wskazującą, czy tablica zawiera określoną wartość. Można określić, czy dopasowanie jest pełny lub jego część. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice) |Zwraca część wyrażenie tablicy. |

Funkcje tablicy może służyć do manipulowania tablicami w formacie JSON. Na przykład w tym miejscu jest kwerendę, która zwraca wszystkie dokumenty, gdy jeden z elementów nadrzędnych jest "Działania okrężnego Wakefield". 

**Zapytanie**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Results**

    [{
      "id": "WakefieldFamily"
    }]

Można określić fragmentu częściowe dopasowanie elementów w tablicy. Następujące zapytanie znajdzie wszystkich elementów nadrzędnych z `givenName` z `Robin`.

**Zapytanie**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)

**Results**

    [{
      "id": "WakefieldFamily"
    }]


Oto przykład innego, który używa ARRAY_LENGTH, aby uzyskać liczbę elementów podrzędnych na rodzinę.

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
Rozwiązania cosmos bazy danych obsługuje następujące funkcje wbudowane Otwórz geograficzne konsorcjum (OGC) na potrzeby zapytań o dane geograficzne. 

<table>
<tr>
  <td><strong>Użycie</strong></td>
  <td><strong>Opis</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Zwraca odległość między dwoma wyrażeniami GeoJSON punktu wielokąta i LineString.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Zwraca wyrażenie logiczne, wskazującą, czy pierwszy obiekt GeoJSON (punkt, wielokąta lub LineString) znajduje się w drugim obiektu GeoJSON (punkt, wielokąta lub LineString).</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Zwraca wartość wskazującą, czy dwa określonych obiektów GeoJSON (punkt, wielokąta lub LineString) intersect wyrażenie logiczne.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Zwraca wartość logiczną wskazującą, czy określone wyrażenie GeoJSON punktu wielokąta i LineString jest nieprawidłowy.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Zwraca wartość wartość JSON, zawierające wartość typu Boolean, jeśli określone wyrażenie GeoJSON punktu wielokąta i LineString jest prawidłowy, a nieprawidłowy, ponadto Przyczyna jako wartość typu ciąg.</td>
</tr>
</table>

Funkcje przestrzenne może służyć do wykonywania zapytań zbliżeniowe względem danych przestrzennych. Na przykład w tym miejscu jest kwerendę, która zwraca wszystkie dokumenty rodziny, które są w ciągu 30 km do określonej lokalizacji za pomocą wbudowanych funkcji ST_DISTANCE. 

**Zapytanie**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Results**

    [{
      "id": "WakefieldFamily"
    }]

Aby uzyskać więcej informacji dotyczących obsługi dane geograficzne do rozwiązania Cosmos bazy danych, zobacz [Praca z dane geograficzne w usłudze Azure DB rozwiązania Cosmos](geospatial.md). Który koduje funkcje przestrzenne i składni SQL DB rozwiązania Cosmos. Teraz Spójrzmy na jak wykonywanie kwerend działa i jak współdziała z użyciem składni LINQ możemy w tym samouczku wykonanej do tej pory.

## <a id="Linq"></a>LINQ do SQL interfejsu API
LINQ jest model programowania .NET określającym obliczeń jako kwerendy dla strumieni obiektów. Rozwiązania cosmos DB udostępnia bibliotekę klienta do interfejsu za pomocą LINQ ułatwiając konwersji między obiektami JSON i .NET i mapowanie podzbiór zapytań LINQ do zapytania DB rozwiązania Cosmos. 

Na rysunku poniżej przedstawiono architekturę obsługi zapytań LINQ przy użyciu rozwiązania Cosmos bazy danych.  Za pomocą klienta rozwiązania Cosmos bazy danych, deweloperzy mogą tworzyć **IQueryable** obiekt, który wysyła zapytanie bezpośrednio DB rozwiązania Cosmos dostawcy zapytania, który następnie tłumaczy zapytania LINQ na zapytanie DB rozwiązania Cosmos. Zapytania są następnie przekazywane do serwera bazy danych rozwiązania Cosmos można pobrać zestawu wyników w formacie JSON. Zwrócone wyniki są deserializacji w strumieniu obiekty .NET po stronie klienta.

![Architektura obsługi zapytań LINQ przy użyciu interfejsu API SQL — składni SQL, język zapytań JSON pojęcia bazy danych i zapytania SQL][1]

### <a name="net-and-json-mapping"></a>Mapowanie JSON i .NET
Mapowanie między obiektami .NET i dokumentów JSON jest naturalna — każdego pola elementu członkowskiego danych jest zamapowany na obiekt JSON, gdzie nazwa pola jest mapowany na "klucz" część obiektu, a część "value" rekursywnie mapowane na wartości część obiektu. Rozważmy następujący przykład: rodziny obiektu utworzonego jest mapowany na dokument JSON, jak pokazano poniżej. I na odwrót dokumentu JSON jest zamapowana do obiektu .NET.

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
Dostawca zapytania DB rozwiązania Cosmos wykonuje najlepsze mapowania nakładu pracy w wyniku zapytania LINQ do zapytania rozwiązania Cosmos bazy danych SQL. W opisie następujące przyjęto założenie, że czytelnik ma podstawowe znajomości LINQ.

Najpierw w systemie typ obsługujemy JSON pierwotne typy — typy liczbowe, boolean, typ string i wartości null. Obsługiwane są tylko te typy JSON. Obsługiwane są następujące wyrażeń skalarnych.

* Wartości stałe — należą do stałej wartości typów pierwotnych danych w chwili oceny zapytania.
* Wyrażenia indeksu tablicy/właściwości — tych wyrażeń odwoływać się do właściwości obiektu lub elementu tablicy.
  
     rodziny. Identyfikator;    Family.Children[0].familyName;    Family.Children[0].Grade;    Family.Children[n].Grade; n jest zmienną int
* Wyrażenia arytmetyczne - obejmują one wspólnych wyrażeniach arytmetycznych na wartościach wartości liczbowych i logicznych. Pełną listę można znaleźć w specyfikacji SQL.
  
     2 * family.children[0].grade;    x + y;
* Wyrażenia porównanie ciągu - należą do porównywania wartości ciągu na niektóre wartości stałej ciągu.  
  
     mother.familyName == "Smith";    child.givenName == s; s jest zmienną ciągu
* / Tablicę obiektów wyrażeniem tworzenia - tych wyrażeń zwracanego obiektu złożonego wartość lub anonimowy typ lub tablicę takie obiekty. Te wartości mogą być zagnieżdżone.
  
     nadrzędną {familyName = "Smith" givenName = "Jan"}; New {najpierw = 1, drugi = 2;} Typ anonimowy z dwóch pól              
     Nowy int [] {3, child.grade, 5};

### <a id="SupportedLinqOperators"></a>Lista obsługiwanych operatorów LINQ
Poniżej przedstawiono listę obsługiwanych operatorów LINQ w dostawcy LINQ dołączone do zestawu .NET SDK SQL.

* **Wybierz**: projekcje przełożyć na SQL SELECT, łącznie z konstrukcji obiektów
* **Gdzie**: filtry przełożyć na SQL WHERE i translacja między obsługuje & &, || i! Operatory SQL
* **Operacja SelectMany**: umożliwia rozwinięcia tablic do klauzuli SQL JOIN. Może służyć do łańcucha/zagnieżdżania wyrażenia, aby filtrować elementy tablicy
* **OrderBy i OrderByDescending**: Umożliwia to ORDER BY rosnąco/malejąco
* **Liczba**, **suma**, **Min**, **Max**, i **średni** operatory do agregacji i ich odpowiedniki async **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, i **AverageAsync**.
* **Wykonanie funkcji CompareTo**: Umożliwia to przeprowadzenie porównania z zakresu. Często używane dla ciągów, ponieważ nie są one porównywalne w .NET
* **Podejmij**: tłumaczy do góry SQL ograniczania wyników kwerendy
* **Funkcje matematyczne**: obsługuje translację. Asin Abs, Acos, przez sieć, Atan Ceiling Cos, Exp, Floor, dziennika, Log10, Pow, Round, logowania, Sin, Sqrt, Tan, Truncate na równoważne funkcje wbudowane SQL.
* **Ciąg funkcji**: obsługuje translację. EndsWith Concat, zawiera, w sieci, IndexOf, Count, ToLower, TrimStart, Zamień, wstecznego, TrimEnd, StartsWith, SubString, ToUpper na równoważne funkcje wbudowane SQL.
* **Tablica funkcji**: obsługuje translację. Concat, zawierający i liczby na równoważne funkcje wbudowane SQL w sieci.
* **Funkcje rozszerzeń dane geograficzne**: obsługuje translację szkieletu metody odległości w IsValid i IsValidDetailed na równoważne funkcje wbudowane SQL.
* **Zdefiniowane przez użytkownika funkcji rozszerzenia funkcji**: obsługuje tłumaczenia z metody zastępczej UserDefinedFunctionProvider.Invoke odpowiedniej funkcji zdefiniowanej przez użytkownika.
* **Różne**: obsługuje tłumaczenia łączonej i operatory warunkowe. Może dokonywać translacji zawiera ciąg zawiera, ARRAY_CONTAINS lub IN SQL, w zależności od kontekstu.

### <a name="sql-query-operators"></a>Operatory kwerend SQL
Oto przykłady ilustrujące sposobu niektóre standardowe operatory zapytań LINQ przekształcania do zapytania DB rozwiązania Cosmos.

#### <a name="select-operator"></a>SELECT — Operator
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



#### <a name="selectmany-operator"></a>Operacja SelectMany — operator
Składnia jest `input.SelectMany(x => f(x))`, gdzie `f` jest wyrażenie skalarne, który zwraca typ kolekcji.

**Wyrażenie lambda LINQ**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>Gdy operator
Składnia jest `input.Where(x => f(x))`, gdzie `f` jest wyrażenie skalarne, która zwraca wartość logiczną.

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


### <a name="composite-sql-queries"></a>Złożonego zapytania SQL
Operatory powyżej może być do utworzenia bardziej zaawansowanych zapytań. Ponieważ DB rozwiązania Cosmos obsługuje zagnieżdżonych kolekcje, kompozycji można być połączonych lub zagnieżdżone.

#### <a name="concatenation"></a>Łączenie
Składnia jest `input(.|.SelectMany())(.Select()|.Where())*`. Połączonych zapytanie można uruchomić z opcjonalną `SelectMany` query następuje wielu `Select` lub `Where` operatorów.

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



#### <a name="nesting"></a>Zagnieżdżania
Składnia jest `input.SelectMany(x=>x.Q())` w przypadku pytań `Select`, `SelectMany`, lub `Where` operatora.

W zapytaniu zagnieżdżonym wewnętrzny zapytania są stosowane do każdego elementu w kolekcji zewnętrzne. Jedna funkcja ważne jest, że wewnętrzny zapytania mogą odwoływać się do pól elementów w kolekcji zewnętrzne, takie jak samosprzężenia.

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


## <a id="ExecutingSqlQueries"></a>Wykonywanie kwerend SQL
Rozwiązania cosmos DB udostępnia zasoby za pośrednictwem interfejsu API REST, który można wywołać za pomocą dowolnego języka realizującego żądania HTTP i HTTPS. Ponadto DB rozwiązania Cosmos oferuje biblioteki programistyczne dla kilku popularnych języków, takich jak .NET, Node.js, JavaScript i Python. Interfejs API REST i biblioteki różnych obsługują zapytań za pomocą programu SQL. Zestaw .NET SDK obsługuje LINQ zapytań oprócz SQL.

Poniższe przykłady przedstawiają sposób tworzyć kwerendy i przesłać je do konta bazy danych DB rozwiązania Cosmos.

### <a id="RestAPI"></a>INTERFEJS API REST
Rozwiązania cosmos DB oferuje Otwórz model programowania RESTful za pośrednictwem protokołu HTTP. Konta bazy danych można alokować przy użyciu subskrypcji platformy Azure. Model zasobów bazy danych rozwiązania Cosmos zawiera zestaw zasobów w ramach konta bazy danych, z których każdy jest mogą być adresowane za pomocą logicznych i stabilny identyfikator URI. Zestaw zasobów jest określana jako źródła danych w tym dokumencie. Konto bazy danych zawiera zestaw baz danych, każda z nich zawiera wiele kolekcji, a każdy z których w Włącz zawierają dokumentów, funkcje UDF i innych typów zasobów.

Model podstawowe interakcji przy użyciu tych zasobów jest za pomocą polecenia HTTP GET, PUT, POST i DELETE z ich interpretacji standardowa. Zlecenie POST jest używany w celu utworzenia nowego zasobu, wykonywania procedury składowanej lub zapytania DB rozwiązania Cosmos. Zapytania są zawsze operacji tylko do odczytu z żadnych efektów ubocznych.

W poniższych przykładach pokazano POST dla zapytania interfejsu API SQL wykonane w stosunku do kolekcji zawierającej dwa dokumenty przykładowe się, że firma Microsoft zostało sprawdzone wykonanej do tej pory. Kwerenda ma filtr prosty na właściwość name JSON. Zwróć uwagę na użycie `x-ms-documentdb-isquery` i Content-Type: `application/query+json` nagłówków do określenia, czy operacja się zapytania.

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


W drugim przykładzie bardziej złożoną kwerendę, która zwraca wiele wyników z sprzężenie.

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


Jeśli wyników zapytania nie mieści się w obrębie jednej strony wyników, a następnie interfejsu API REST zwraca token kontynuacji za pośrednictwem `x-ms-continuation-token` nagłówka odpowiedzi. Klienci mogą z podziałem na strony wyników przez dołączenie nagłówka do kolejnych wyników. Liczba wyników na stronie również mogą być kontrolowane za pośrednictwem `x-ms-max-item-count` numer nagłówka. Jeśli określone zapytanie ma funkcję agregacji, takie jak `COUNT`, następnie strony zapytania może zwracać wartości zagregowane częściowo za pośrednictwem strony wyników. Klienci muszą wykonywać agregacji drugiego poziomu za pośrednictwem tych wynikach i tworzyć wyników końcowych, na przykład, Suma za pośrednictwem liczby zwracane w poszczególnych stron, aby zwrócić łączna liczba.

Aby zarządzać zasadami spójności danych dla zapytań, należy użyć `x-ms-consistency-level` nagłówka, takie jak wszystkie żądania interfejsu API REST. Spójność sesji jest wymagane również echo najnowszej `x-ms-session-token` nagłówka pliku Cookie w żądaniu zapytania. Zasady indeksowania, którego dotyczy kwerenda kolekcji może mieć wpływ również spójności wyników zapytania. Z domyślne ustawienia zasad indeksowania, kolekcje indeks jest zawsze z zawartości dokumentu i wyniki zapytania odpowiada spójności wybrany dla danych. Jeśli zasady indeksowania jest rozluźnić, aby opóźnieniem, zapytania mogą zwracać starych wyników. Aby uzyskać więcej informacji, zobacz [poziomy spójności bazy danych rozwiązania Cosmos Azure][consistency-levels].

Jeśli skonfigurowane zasady indeksowania w kolekcji nie obsługuje określonego zapytania, serwer bazy danych Azure rozwiązania Cosmos zwraca 400 "złe żądanie". Ten błąd jest zwracany dla zakresu zapytania względem ścieżki skonfigurowane dla wyszukiwań wyznaczania wartości skrótu (równości) i ścieżek jawnie wykluczona z indeksowania. `x-ms-documentdb-query-enable-scan` Można określić nagłówka Umożliwia zapytanie, aby wykonać skanowanie, gdy indeks nie jest dostępna.

Szczegółowe metryki na wykonanie kwerendy można uzyskać przez ustawienie `x-ms-documentdb-populatequerymetrics` nagłówka do `True`. Aby uzyskać więcej informacji, zobacz [metryki kwerendy SQL dla bazy danych Azure rozwiązania Cosmos](sql-api-sql-query-metrics.md).

### <a id="DotNetSdk"></a>C# (.NET) SDK
Zestaw .NET SDK obsługuje zarówno LINQ, jak i SQL zapytań. Poniższy przykład pokazuje, jak wykonać kwerendy filtr prosty wprowadzone wcześniej w tym dokumencie.

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


W tym przykładzie porównanie dwóch właściwości równości w ramach każdego dokumentu i używa projekcje anonimowy. 

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


Następna próbka zawiera sprzężenia, wyrazić za pomocą operacja SelectMany LINQ.

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



Klient .NET automatycznie wykonuje iterację na stronach wyników zapytania w blokach foreach zgodnie z powyższym. Opcje zapytania, wprowadzone w sekcji interfejsu API REST są także dostępne przy użyciu zestawu .NET SDK `FeedOptions` i `FeedResponse` klasy w metodzie CreateDocumentQuery. Liczba stron można kontrolować przy użyciu `MaxItemCount` ustawienie. 

Można również jawnie kontrolować stronicowania, tworząc `IDocumentQueryable` przy użyciu `IQueryable` obiektu, a następnie odczytując` ResponseContinuationToken` wartości i przekazywanie ich ponownie jako `RequestContinuationToken` w `FeedOptions`. `EnableScanInQuery` można ustawić tak, aby włączyć skanowanie, gdy zapytanie nie może być obsługiwana przez skonfigurowane zasady indeksowania. Dla kolekcji partycjonowanych, można użyć `PartitionKey` w celu uruchomienia zapytania pojedynczej partycji (chociaż DB rozwiązania Cosmos można automatycznie wyodrębniania to tekst zapytania), i `EnableCrossPartitionQuery` do uruchamiania zapytań, które może trzeba będzie być uruchamiane na wiele partycji. 

Zapoznaj się [przykłady Azure rozwiązania Cosmos DB .NET](https://github.com/Azure/azure-documentdb-net) dla większej liczby próbek zawierający zapytania. 

### <a id="JavaScriptServerSideApi"></a>Interfejs API programu JavaScript po stronie serwera
Rozwiązania cosmos DB zapewnia model programowania do wykonywania logiki aplikacji JavaScript oparty bezpośrednio na kolekcji przy użyciu procedur składowanych i wyzwalaczy. Logiki JavaScript zarejestrowany na poziomie kolekcji następnie mogą wyzwalać operacje bazy danych operacji na dokumenty z danej kolekcji. Operacje te są ujęte w transakcji ACID otoczenia.

Poniższy przykład przedstawia użycie queryDocuments na serwerze JavaScript API tworzenie kwerend z wewnątrz procedury składowane i wyzwalaczy.

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
1. [Wprowadzenie do platformy Azure rozwiązania Cosmos bazy danych][introduction]
2. [Specyfikacja rozwiązania Cosmos bazy danych SQL Azure](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3. [Przykładów dla platformy Azure .NET DB rozwiązania Cosmos](https://github.com/Azure/azure-documentdb-net)
4. [Poziomy spójności bazy danych Azure rozwiązania Cosmos][consistency-levels]
5. ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6. JSON [http://json.org/](http://json.org/)
7. Specyfikacja języka JavaScript [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8. LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9. Techniki oceny zapytania dla dużych baz danych [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Przetwarzania zapytań w systemach równoległych relacyjnych baz danych, naciśnij społeczeństwa IEEE komputera, 1994 r.
11. Tan lu, Ooi, Wyślij zapytanie do przetwarzania w systemach równoległych relacyjnej bazy danych, naciśnij klawisz społeczeństwa IEEE komputera, 1994.
12. Christopher Olston, Reed Benjaminowi Utkarsh Srivastava, Kumar Ravi, Andrew Tomkins: Pig Latin: nie tak obcego języka do przetwarzania danych, SIGMOD 2008.
13. G. Graefe. Ramach kaskady na optymalizację zapytania. Eng. IEEE danych Bull., 18(3): 1995.

[1]: ./media/sql-api-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
