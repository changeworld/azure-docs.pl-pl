---
title: 'Azure DB rozwiązania Cosmos: Odwołania do zapytań składni SQL | Dokumentacja firmy Microsoft'
description: Dokumentacja referencyjna dla języka zapytań usługi Azure rozwiązania Cosmos bazy danych SQL.
services: cosmos-db
author: LalithaMV
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: reference
ms.date: 10/18/2017
ms.author: laviswa
ms.openlocfilehash: 13337e7979a378382df5e62661b04bac8dffa689
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798835"
---
# <a name="azure-cosmos-db-sql-syntax-reference"></a>Odwołania do składni rozwiązania Cosmos bazy danych SQL Azure

Azure obsługuje rozwiązania Cosmos DB badania dokumentów za pomocą znanych SQL (Structured Query Language), takich jak gramatyki za pośrednictwem hierarchiczna dokumentów JSON bez konieczności jawnego schematu lub tworzenia indeksów pomocniczych. Ten temat zawiera dokumentację referencyjną dla język zapytań SQL, które są zgodne z interfejsu API SQL kont.

Aby uzyskać wskazówki język zapytań SQL, zobacz [kwerendy SQL dla bazy danych Azure rozwiązania Cosmos](sql-api-sql-query.md).  
  
Również zachęcamy do odwiedzenia [Plac zabaw dla zapytań](http://www.documentdb.com/sql/demo) gdzie spróbuj bazy danych rozwiązania Cosmos Azure i wykonywania kwerend SQL do naszego zestawu danych.  
  
## <a name="select-query"></a>Zapytanie SELECT  
Pobiera dokumentów JSON z bazy danych. Obsługuje oceny wyrażenia, projekcji, filtrowania i tworzy sprzężenie.  Konwencje opisujących instrukcji "SELECT" wyszczególniono w sekcji konwencje składni.  
  
**Składnia**  
  
```
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ]  
```  
  
 **Uwagi**  
  
 Zobacz następujące sekcje, aby uzyskać szczegółowe informacje na każdej klauzuli:  
  
-   [Klauzula SELECT](#bk_select_query)  
  
-   [Klauzula FROM](#bk_from_clause)  
  
-   [Klauzula WHERE](#bk_where_clause)  
  
-   [Klauzula ORDER BY](#bk_orderby_clause)  
  
Muszą być uporządkowane klauzul w instrukcji SELECT, jak pokazano powyżej. Dowolny opcjonalna klauzula można pominąć. Jednak w przypadku używania klauzule opcjonalne muszą występować w odpowiedniej kolejności.  
  
**Logiczna kolejność przetwarzania instrukcji SELECT**  
  
Kolejność przetwarzania klauzule jest:  

1.  [Klauzula FROM](#bk_from_clause)  
2.  [Klauzula WHERE](#bk_where_clause)  
3.  [Klauzula ORDER BY](#bk_orderby_clause)  
4.  [Klauzula SELECT](#bk_select_query)  

Należy pamiętać, że jest inna niż kolejność, w jakiej występują w składni. Kolejność jest taka, że wszystkie nowe symbole wynikające z klauzulą przetworzonych są widoczne i mogą być używane w klauzulach później przetwarzane. Na przykład w przypadku gdy aliasów zadeklarować w klauzuli FROM są dostępne i klauzule SELECT.  

**Odstęp i komentarze**  

Wszystkie białe znaki, które nie są częścią ciągu w cudzysłowie lub identyfikator w cudzysłowach nie są częścią gramatyki języka i są ignorowane podczas analizy.  

Język zapytań obsługuje komentarzy styl T-SQL, takich jak  

-   Instrukcja SQL `-- comment text [newline]`  

Podczas znaków odstępu i komentarze nie ma żadnego znaczenia w gramatyce, ich może służyć do rozdzielania tokenów. Na przykład: `-1e5` jest chwilę jednego tokenu, liczba`: – 1 e5` minus token następuje numer 1 i identyfikatora e5.  

##  <a name="bk_select_query"></a> Klauzula SELECT  
Muszą być uporządkowane klauzul w instrukcji SELECT, jak pokazano powyżej. Dowolny opcjonalna klauzula można pominąć. Jednak w przypadku używania klauzule opcjonalne muszą występować w odpowiedniej kolejności.  

**Składnia**  
```  
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **Argumenty**  
  
 `<select_specification>`  
  
 Właściwości lub wartości, należy wybrać zestaw wyników.  
  
 `'*'`  
  
Określa, czy ma być pobrana wartość bez wprowadzania żadnych zmian. W szczególności, jeśli wartość przetworzonych jest obiektem, będzie można pobrać wszystkich właściwości.  
  
 `<object_property_list>`  
  
Określa listę właściwości, które mają zostać pobrane. Wartości zwracane będzie obiektu z określonej właściwości.  
  
`VALUE`  
  
Określa, czy ma być pobrana wartość JSON zamiast cały obiekt JSON. To, w przeciwieństwie do `<property_list>` nie jest zawijany przewidywane wartości w obiekcie.  
  
`<scalar_expression>`  
  
Wyrażenie reprezentujący wartość ma zostać obliczony. Zobacz [wyrażenia skalarne](#bk_scalar_expressions) sekcji, aby uzyskać szczegółowe informacje.  
  
**Uwagi**  
  
`SELECT *` Składnia jest prawidłowa, jeśli klauzula FROM została zadeklarowana dokładnie jeden alias. `SELECT *` udostępnia projekcji tożsamości, które mogą być przydatne, jeśli nie projekcji nie jest konieczne. Wybierz * jest prawidłowa, jeśli klauzula FROM określono i wprowadzono tylko jednego źródła danych wejściowych.  
  
Należy pamiętać, że `SELECT <select_list>` i `SELECT *` są "sugar składni" i może być również wyrażona za pomocą prostego instrukcji "SELECT", jak pokazano poniżej.  
  
1.  `SELECT * FROM ... AS from_alias ...`  
  
     odpowiada to:  
  
     `SELECT from_alias FROM ... AS from_alias ...`  
  
2.  `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
     odpowiada to:  
  
     `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**Zobacz też**  
  
[Wyrażenia skalarne](#bk_scalar_expressions)  
[Klauzula SELECT](#bk_select_query)  
  
##  <a name="bk_from_clause"></a> Klauzula FROM  
Określa źródło lub dołączonym do źródła. Klauzula FROM jest opcjonalna. Jeśli nie jest określony, inne klauzule nadal będą wykonywane tak, jakby klauzuli FROM podane pojedynczego dokumentu.  
  
**Składnia**  
  
```  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <collection_expression> [[AS] input_alias]  
        | input_alias IN <collection_expression>  
  
<collection_expression> ::=   
        ROOT   
     | collection_name  
     | input_alias  
     | <collection_expression> '.' property_name  
     | <collection_expression> '[' "property_name" | array_index ']'  
```  
  
**Argumenty**  
  
`<from_source>`  
  
Określa źródło danych, z lub bez aliasu. Jeśli nie określono aliasu, będzie można wywnioskować na podstawie `<collection_expression>` za pomocą następujących reguł:  
  
-   Jeśli wyrażenie jest nazwa_kolekcji, nazwa_kolekcji będzie używany jako alias.  
  
-   Jeśli wyrażenie jest `<collection_expression>`, property_name, a następnie property_name zostanie użyta jako alias. Jeśli wyrażenie jest nazwa_kolekcji, nazwa_kolekcji będzie używany jako alias.  
  
AS `input_alias`  
  
Określa, że `input_alias` to zbiór wartości zwracanych przez podstawowej wyrażeniu kolekcji.  
 
`input_alias` W  
  
Określa, że `input_alias` powinno reprezentować zbiór wartości uzyskane przez Iterowanie po każdej tablica zwrócona przez podstawowej wyrażeniu kolekcji wszystkie elementy tablicy. Każda wartość zwracana przez podstawowej wyrażeniu kolekcji, która nie jest tablicą jest ignorowana.  
  
`<collection_expression>`  
  
Określa wyrażenie kolekcji ma być używany do pobierania dokumentów.  
  
`ROOT`  
  
Określa, że w tym dokumencie powinny zostać pobrane od domyślnej kolekcji aktualnie połączonych.  
  
`collection_name`  
  
Określa, że w tym dokumencie powinny zostać pobrane z określonej kolekcji. Nazwa kolekcji musi odpowiadać nazwie aktualnie połączony z kolekcji.  
  
`input_alias`  
  
Określa, że tego dokumentu powinny zostać pobrane ze źródła, zdefiniowany przez podany alias.  
  
`<collection_expression> '.' property_`  
  
Określa, w tym dokumencie powinny zostać pobrane po zalogowaniu się do `property_name` właściwości lub indeks_tablicy elementu tablicy dla wszystkich dokumentów pobierane przez określone wyrażenie kolekcji.  
  
`<collection_expression> '[' "property_name" | array_index ']'`  
  
Określa, w tym dokumencie powinny zostać pobrane po zalogowaniu się do `property_name` właściwości lub indeks_tablicy elementu tablicy dla wszystkich dokumentów pobierane przez określone wyrażenie kolekcji.  
  
**Uwagi**  
  
Wszystkie aliasy dostarczona lub wywnioskować w `<from_source>(`s) musi być unikatowa. Składnia `<collection_expression>.`property_name jest taka sama jak `<collection_expression>' ['"property_name"']'`. Jednak ostatnie składni umożliwia znaków innych niż identyfikator zawiera nazwę właściwości.  
  
**Brak elementów tablicy nie ma właściwości niezdefiniowanych wartości obsługi**  
  
Wyrażeniu kolekcji uzyskuje dostęp do właściwości lub elementów tablicy i że nie ma wartości, tej wartości będą ignorowane i nie można przetworzyć więcej.  
  
**Kolekcja wyrażeń kontekstu zakresu**  
  
Wyrażeniu kolekcji może być zakresem kolekcji lub zakres dokumentu:  
  
-   Wyrażenie jest kolekcja zakresu, jeśli źródle wyrażeniu kolekcji jest albo GŁÓWNYM lub `collection_name`. Takie wyrażenie reprezentuje zestaw dokumentów pobrać bezpośrednio z kolekcji i nie jest zależna od przetwarzania inne wyrażenia kolekcji.  
  
-   Wyrażenie jest dokumentu zakresu, jeśli jest źródle wyrażeniu kolekcji `input_alias` wprowadzone wcześniej w zapytaniu. Takie wyrażenie reprezentuje zestaw dokumentów uzyskany przez obliczenie wyrażenia kolekcji w zakresie każdego dokumentu należących do zestawu skojarzone z tą kolekcją aliasu.  Zestaw wynikowy będzie Unii zestawów uzyskany przez obliczenie wyrażenia kolekcji dla wszystkich dokumentów w podstawowym zestawie.  
  
**Tworzy sprzężenie**  
  
W bieżącej wersji bazy danych rozwiązania Cosmos Azure obsługuje sprzężeń wewnętrznych. Nadchodzącego są sprzężenia dodatkowe funkcje.

Sprzężenia wewnętrzne spowodować pełny iloczyn wektorowy zestawów uczestniczących sprzężenia. Wynik sprzężenia sposób N jest zestaw spójnych kolekcji N element, gdzie każda wartość w spójnej kolekcji jest skojarzony z aliasem ustawić uczestniczących w sprzężeniu i jest możliwy za pomocą odwołań do tego aliasu w innych klauzul.  
  
Ocena sprzężenia zależy od zakresu kontekstu uczestniczących zestawów:  
  
-  Sprzężenia między kolekcji zestawu A i zakres kolekcji ustawić B, wynikiem iloczyn wektorowy wszystkie elementy w zestawach A i B.
  
-   Powoduje sprzężenie zestaw A i zestaw zakres dokumentu B, w związku z wszystkich zestawów uzyskany przez obliczenie zakres dokumentu zestawu B dla każdego dokumentu z ustawić A.  
  
 W bieżącej wersji maksymalnie jedno wyrażenie zakres kolekcji jest obsługiwany przez procesor zapytań.  
  
**Przykłady sprzężeń:**  
  
Przyjrzyjmy się następujące klauzuli FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Let każdego źródła Zdefiniuj `input_alias1, input_alias2, …, input_aliasN`. Tej klauzuli FROM zwraca zestaw spójnych kolekcji na N (krotka z wartościami N). Każda krotka zawiera wartości utworzonego przez Iterowanie wszystkie aliasy kolekcji po ich odpowiednich zestawów.  
  
*Dołącz do SPOŁECZNOŚCI przykład 1 z 2 źródeł:*  
  
- Let `<from_source1>` się o zakresie kolekcji i reprezentują zestaw {A, B, C}.  
  
- Let `<from_source2>` można dokumentu o zakresie odwołujące się do input_alias1 i reprezentują zestawów:  
  
    {1, 2} dla `input_alias1 = A,`  
  
    {3} dla `input_alias1 = B,`  
  
    {4, 5} dla `input_alias1 = C,`  
  
- Klauzula FROM `<from_source1> JOIN <from_source2>` spowoduje następujące spójnych kolekcji:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
*Dołącz do SPOŁECZNOŚCI przykład 2 z 3 źródeł:*  
  
- Let `<from_source1>` się o zakresie kolekcji i reprezentują zestaw {A, B, C}.  
  
- Let `<from_source2>` się zakres dokument odwołuje się do `input_alias1` i reprezentują zestawów:  
  
    {1, 2} dla `input_alias1 = A,`  
  
    {3} dla `input_alias1 = B,`  
  
    {4, 5} dla `input_alias1 = C,`  
  
- Let `<from_source3>` się zakres dokument odwołuje się do `input_alias2` i reprezentują zestawów:  
  
    {100, 200} dla `input_alias2 = 1,`  
  
    {300} dla `input_alias2 = 3,`  
  
- Klauzula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` spowoduje następujące spójnych kolekcji:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
> [!NOTE]
> Brak spójnych kolekcji dla innych wartości `input_alias1`, `input_alias2`, dla którego `<from_source3>` nie zwrócił żadnych wartości.  
  
*Dołącz do SPOŁECZNOŚCI przykład 3 z 3 źródeł:*  
  
- Let < from_source1 > można zakres kolekcji i reprezentują zestaw {A, B, C}.  
  
- Let `<from_source1>` się o zakresie kolekcji i reprezentują zestaw {A, B, C}.  
  
- Pozwól < from_source2 > można input_alias1 odwołującego się o zakresie dokumentu i reprezentują zestawów:  
  
    {1, 2} dla `input_alias1 = A,`  
  
    {3} dla `input_alias1 = B,`  
  
    {4, 5} dla `input_alias1 = C,`  
  
- Let `<from_source3>` należeć do zakresu `input_alias1` i reprezentują zestawów:  
  
    {100, 200} dla `input_alias2 = A,`  
  
    {300} dla `input_alias2 = C,`  
  
- Klauzula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` spowoduje następujące spójnych kolekcji:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), C, 4, 300, (C, 5, 300)  
  
> [!NOTE]
> Spowodowało to iloczyn wektorowy między `<from_source2>` i `<from_source3>` ponieważ oba ograniczone do tej samej `<from_source1>`.  Spowodowało to 4 (2 x 2) spójnych kolekcji o wartości A, 0 spójnych kolekcji o wartości B (1 x 0) i 2 (2 x 1) spójnych kolekcji o wartości C.  
  
**Zobacz też**  
  
 [Klauzula SELECT](#bk_select_query)  
  
##  <a name="bk_where_clause"></a> Klauzula WHERE  
 Określa warunek wyszukiwania dla dokumentów zwróconych przez kwerendę.  
  
 **Składnia**  
  
```  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **Argumenty**  
  
-   `<filter_condition>`  
  
     Określa warunek do spełnienia dokumentów, które mają zostać zwrócone.  
  
-   `<scalar_expression>`  
  
     Wyrażenie reprezentujący wartość ma zostać obliczony. Zobacz [wyrażenia skalarne](#bk_scalar_expressions) sekcji, aby uzyskać szczegółowe informacje.  
  
 **Uwagi**  
  
 Aby dokumentu będą zwracane wyrażenie określone jako filtr warunek musi zwrócić wartość true. Tylko wartość logiczną PRAWDA będzie spełniają warunek, wszelkie inne wartości: niezdefiniowana, null, wartość false, numer, tablicy lub obiekt nie spełniają warunek.  
  
##  <a name="bk_orderby_clause"></a> Klauzula ORDER BY  
 Określa kolejność sortowania wyników zwróconych przez kwerendę.  
  
 **Składnia**  
  
```  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **Argumenty**  
  
-   `<sort_specification>`  
  
     Określa właściwość lub wyrażenie do sortowania zestawu wyników zapytania. Sortuj kolumny można określić jako alias nazwy lub kolumny.  
  
     Można określić wiele kolumn sortowania. Nazwy kolumn muszą być unikatowe. Kolejność sortowania kolumn w klauzuli ORDER BY definiuje organizacji zestawu wyników posortowane. Oznacza to zestaw wyników jest sortowana według pierwszą właściwością, a następnie tej listy uporządkowanej są sortowane według właściwości drugiej i tak dalej.  
  
     Nazwy kolumn w klauzuli ORDER BY musi odpowiadać z kolumną na liście wyboru lub kolumny w tabeli określonej w klauzuli FROM bez żadnych niejednoznaczności.  
  
-   `<sort_expression>`  
  
     Określa jedną właściwość lub wyrażenie do sortowania zestawu wyników zapytania.  
  
-   `<scalar_expression>`  
  
     Zobacz [wyrażenia skalarne](#bk_scalar_expressions) sekcji, aby uzyskać szczegółowe informacje.  
  
-   `ASC | DESC`  
  
     Określa, że wartości w określonej kolumnie mają być sortowane w kolejności rosnącej lub malejącej. ASC sortuje od najmniejszej wartości do największej wartości. DESC sortuje z najwyższą wartością najniższą wartość. ASC jest domyślny porządek sortowania. Wartości null są traktowane jak najmniejsze możliwe wartości.  
  
 **Uwagi**  
  
 Natomiast gramatyki zapytań obsługuje wiele kolejność według właściwości, czasu wykonywania zapytania bazy danych Azure rozwiązania Cosmos obsługuje sortowanie, tylko jednej właściwości oraz tylko z nazwy właściwości, tj., nie dla właściwości obliczanej. Sortowanie wymaga również, że zasady indeksowania zawiera indeks zakresu dla właściwości i określonego typu z precyzja maksymalna. Zajrzyj do dokumentacji zasad indeksowania, aby uzyskać więcej informacji.  
  
##  <a name="bk_scalar_expressions"></a> Wyrażenia skalarne  
 Wyrażenie skalarne jest kombinacją symboli i operatory, które może przyjąć uzyskania pojedynczej wartości. Proste wyrażenia mogą być stałe, odwołań do właściwości, odwołania do elementu tablicy, odwołania do aliasu lub wywołania funkcji. Proste wyrażenia można łączyć przy użyciu operatorów wyrażenia.  
  
 Aby uzyskać szczegółowe informacje, na których wyrażenie skalarne może mieć wartości, zobacz [stałe](#bk_constants) sekcji.  
  
 **Składnia**  
  
```  
<scalar_expression> ::=  
       <constant>   
     | input_alias   
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>   
     | <create_array_expression>  
     | (<scalar_expression>)   
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```  
  
 **Argumenty**  
  
-   `<constant>`  
  
     Reprezentuje wartość stałą. Zobacz [stałe](#bk_constants) sekcji, aby uzyskać szczegółowe informacje.  
  
-   `input_alias`  
  
     Reprezentuje wartość zdefiniowana przez `input_alias` wprowadzone w systemie `FROM` klauzuli.  
    Ta wartość jest gwarancji nie **Niezdefiniowany** —**Niezdefiniowany** wartości w danych wejściowych są pomijane.  
  
-   `<scalar_expression>.property_name`  
  
     Reprezentuje wartość właściwości obiektu. Jeśli właściwość nie istnieje lub odwołuje się do właściwości na wartość, która nie jest obiektem, a następnie wyrażenie ma **Niezdefiniowany** wartość.  
  
-   `<scalar_expression>'['"property_name"|array_index']'`  
  
     Reprezentuje wartość właściwości o nazwie `property_name` lub element tablicy o indeksie `array_index` obiektu/tablicy. Jeśli indeks tablicy właściwości/nie istnieje lub indeks tablicy właściwości/odwołuje się do wartości, która nie jest Tablica obiektów /, następnie wynikiem wyrażenia jest niezdefiniowana wartość.  
  
-   `unary_operator <scalar_expression>`  
  
     Reprezentuje operatora, który jest stosowany do pojedynczej wartości. Zobacz [operatory](#bk_operators) sekcji, aby uzyskać szczegółowe informacje.  
  
-   `<scalar_expression> binary_operator <scalar_expression>`  
  
     Reprezentuje operatora, który jest stosowany do dwóch wartości. Zobacz [operatory](#bk_operators) sekcji, aby uzyskać szczegółowe informacje.  
  
-   `<scalar_function_expression>`  
  
     Reprezentuje wartość zdefiniowana przez wynik wywołania funkcji.  
  
-   `udf_scalar_function`  
  
     Nazwa użytkownika zdefiniowana skalarną.  
  
-   `builtin_scalar_function`  
  
     Nazwa wbudowanej funkcji skalarnych.  
  
-   `<create_object_expression>`  
  
     Reprezentuje wartość uzyskany przez utworzenie nowego obiektu o określonej właściwości i ich wartości.  
  
-   `<create_array_expression>`  
  
     Reprezentuje wartość uzyskany przez utworzenie nowej tablicy z określonymi wartościami jako elementy  
  
-   `parameter_name`  
  
     Reprezentuje wartość określona nazwa parametru. Nazwy parametrów muszą mieć pojedynczy @ jako pierwszy znak.  
  
 **Uwagi**  
  
 Podczas wywoływania wbudowanych lub użytkownika definiowania funkcji skalarnych muszą być zdefiniowane wszystkie argumenty. Jeśli dowolny argument nie jest zdefiniowane, nie zostanie wywołana funkcja i wynik będzie niezdefiniowany.  
  
 Podczas tworzenia obiektu, dowolnej właściwości, która jest przypisana niezdefiniowana wartość zostanie pominięty i nie jest uwzględniony w utworzony obiekt.  
  
 Podczas tworzenia tablicy wartości elementu, który jest przypisany **Niezdefiniowany** wartość zostanie pominięty i nie jest uwzględniony w utworzony obiekt. Spowoduje to dalej zdefiniowanego elementu do jego miejsce w taki sposób, że utworzony tablicy nie zostaną pominięte indeksów.  
  
##  <a name="bk_operators"></a> Operatory  
 W tej sekcji opisano operatory obsługiwane. Każdy operator można przypisać do dokładnie jednej kategorii.  
  
 Zobacz **kategorii Operator** tabelę poniżej, aby uzyskać szczegółowe informacje dotyczące obsługi **Niezdefiniowany** wartości typu wymagania dotyczące obsługi z niezgodne typy wartości i wartości wejściowe.  
  
 **Operator kategorie:**  
  
|**Kategoria**|**Szczegóły**|  
|-|-|  
|**Operacje arytmetyczne**|Operator oczekuje input(s) się numery. Dane wyjściowe również jest liczbą. Jeśli dowolny z jej danych wejściowych jest **Niezdefiniowany** lub jest typu innego niż numer następnie wynik **Niezdefiniowany**.|  
|**Alternatywy bitowej**|Operator oczekuje input(s) być 32-bitowej liczby całkowitej ze znakiem numery. Dane wyjściowe jest również 32-bitowa liczba całkowita.<br /><br /> Zostanie zaokrąglony żadnej wartości niebędące liczbami całkowitymi. Wartość dodatnią zostaną zaokrąglone w dół, ujemne wartości zaokrąglona w górę.<br /><br /> Każdą wartość, która znajduje się poza zakresem 32-bitową liczbę całkowitą zostaną przekonwertowane, wykonując ostatnich 32 bity jego dwa w notacji uzupełnienia.<br /><br /> Jeśli dowolny z jej danych wejściowych jest **Niezdefiniowany** lub innych typów niż numer, a następnie wynik jest **Niezdefiniowany**.<br /><br /> **Uwaga:** powyżej zachowanie jest zgodne z zachowanie bitowy operator w JavaScript.|  
|**logical**|Operator oczekuje input(s) jako Boolean(s). Dane wyjściowe również jest wartością logiczną.<br />Jeśli dowolny z jej danych wejściowych jest **Niezdefiniowany** lub typu, innego niż Boolean, wynikiem będzie **Niezdefiniowany**.|  
|**Porównanie**|Operator oczekuje input(s) być tego samego typu i nie jest niezdefiniowany. Dane wyjściowe jest wartością logiczną.<br /><br /> Jeśli dowolny z jej danych wejściowych jest **Niezdefiniowany** lub dane wejściowe mają różne typy, a następnie w wyniku **Niezdefiniowany**.<br /><br /> Zobacz **porządkowanie wartości do porównania** tabeli dla wartości kolejności szczegóły.|  
|**Ciąg**|Operator oczekuje input(s) jako ciągi. Dane wyjściowe również jest ciągiem.<br />Jeśli dowolny z jej danych wejściowych jest **Niezdefiniowany** lub typu, innego niż ciąg wynik jest **Niezdefiniowany**.|  
  
 **Operatory jednoargumentowe:**  
  
|**Nazwa**|**Operator**|**Szczegóły**|  
|-|-|-|  
|**Operacje arytmetyczne**|+<br /><br /> -|Zwraca wartość liczbową.<br /><br /> Bitową negację. Zwraca zanegowane wartość liczbową.|  
|**Alternatywy bitowej**|~|Uzupełnienie tych. Zwraca uzupełnienie wartość liczbową.|  
|**Logiczne**|**NIE**|Negacja. Zwraca zanegowane wartość logiczna.|  
  
 **Operatory binarne:**  
  
|**Nazwa**|**Operator**|**Szczegóły**|  
|-|-|-|  
|**Operacje arytmetyczne**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|Dodatek.<br /><br /> Odejmowanie.<br /><br /> Mnożenia.<br /><br /> Podział.<br /><br /> Modulacji.|  
|**Alternatywy bitowej**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|Wartość logiczną lub.<br /><br /> Bitowe koniunkcji binarnej.<br /><br /> Iloczynu bitowego XOR.<br /><br /> Przesunięcia w lewo.<br /><br /> Przesunięcia w prawo.<br /><br /> Przesunięcia w prawo wypełnienia zero.|  
|**logical**|**I**<br /><br /> **LUB**|Połączenie logiczne. Zwraca **true** Jeśli oba argumenty mają **true**, zwraca **false** inaczej.<br /><br /> Połączenie logiczne. Zwraca **true** Jeśli oba argumenty mają **true**, zwraca **false** inaczej.|  
|**Porównanie**|**=**<br /><br /> **!=, <>**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|Równa się. Zwraca **true** argumenty są takie same, funkcja zwraca **false** inaczej.<br /><br /> Nie równa się. Zwraca **true** argumenty nie są równe, zwraca **false** inaczej.<br /><br /> Większa. Zwraca **true** Jeśli pierwszy argument jest większy niż drugi, zwróć **false** inaczej.<br /><br /> Większe lub równe. Zwraca **true** Jeśli pierwszy argument jest większa niż lub równa jeden, **false** inaczej.<br /><br /> Poniżej. Zwraca **true** Jeśli pierwszy argument jest mniejsza niż drugi jeden zwracany **false** inaczej.<br /><br /> Mniejsze niż lub równe. Zwraca **true** Jeśli pierwszy argument jest mniejsza niż drugi, **false** inaczej.<br /><br /> Połączenie. Zwraca drugi argument, jeśli pierwszy argument **Niezdefiniowany** wartość.|  
|**Ciąg**|**&#124;&#124;**|Łączenie. Zwraca złączeniem oba argumenty.|  
  
 **Operatory trzyargumentowe:**  
  
|Operator trójargumentowy|?|Zwraca drugi argument, jeśli pierwszy argument ma wartość **true**; w przeciwnym razie zwracany trzeci argument.|  
|-|-|-|  
  
 **Kolejność wartości do porównania**  
  
|**Typ**|**Kolejność wartości**|  
|-|-|  
|**Niezdefiniowana**|Nie można porównywać.|  
|**Wartość null**|Pojedyncza wartość: **wartości null**|  
|**Numer**|Liczba rzeczywista fizycznych.<br /><br /> Wartości nieskończoności ujemnej jest mniejszy niż inne wartość liczbową.<br /><br /> Dodatnia wartość Infinity jest większy niż inne wartość liczbową. **NaN** wartość nie jest on porównywalny. Porównanie z **NaN** spowoduje **Niezdefiniowany** wartość.|  
|**Ciąg**|Kolejność lexicographical.|  
|**Tablica**|Nie określania kolejności, ale słuszne.|  
|**Obiekt**|Nie określania kolejności, ale słuszne.|  
  
 **Uwagi**  
  
 Typy wartości w usłudze Azure DB rozwiązania Cosmos, często nie są znane, do momentu rzeczywistości pobrania z bazy danych. Aby zapewnić obsługę wydajność wykonywania zapytań, większość operatorów mają wymagania dotyczące typu strict. Operatory samodzielnie nie wykonuj również niejawną konwersję.  
  
 Oznacza to, że zapytanie, takich jak: Wybierz * z katalogu głównego r WHERE r.Age = 21 zwróci tylko te dokumenty z właściwością wieku równa liczbie 21. Dokumenty z właściwością wieku ciąg "21" lub ciąg "0021", nie będzie odpowiadała jako wyrażenie "21" = 21 ocenia do niezdefiniowanego. Umożliwia to lepsze wykorzystanie indeksów, ponieważ wyszukiwanie określonej wartości (np. numer 21) jest szybsze niż wyszukiwanie nieokreśloną liczbę potencjalnych (tj. liczba 21 lub ciągi "21", "021", "21.0"...). To różni się od sposobu JavaScript ocenia operatory na wartości różnych typów.  
  
 **Porównanie i równości obiektów i tablic**  
  
 Porównywanie tablicy lub obiekt wartości przy użyciu operatorów zakresu (>, > =, <, < =) spowoduje niezdefiniowana, ponieważ nie ma nie kolejności zdefiniowane dla obiektu ani tablicy wartości. Jednak przy użyciu operatorów równości i nierówności (=,! =, <>) jest obsługiwana i będzie można porównać wartości strukturę.  
  
 Tablice są takie same, jeśli obie tablice mają tę samą liczbę elementów, a elementy w pozycji dopasowania również są takie same. Jeśli porównanie wyników elementów w jakiejkolwiek parze niezdefiniowana wynik porównania tablicy jest niezdefiniowany.  
  
 Obiekty są takie same, jeśli oba obiekty mają takie same właściwości zdefiniowane, a także są takie same wartości dopasowania właściwości. Jeśli porównanie wyników wartości właściwości w jakiejkolwiek parze niezdefiniowana wynik porównania obiektu jest niezdefiniowany.  
  
##  <a name="bk_constants"></a> Stałe  
 Stała, znana także jako literału lub wartość skalarną, to symbol, który reprezentuje wartość określonych danych. Format stałą zależy od typu danych wartości, która reprezentuje.  
  
 **Obsługiwane typy skalarne danych:**  
  
|**Typ**|**Kolejność wartości**|  
|-|-|  
|**Niezdefiniowana**|Pojedyncza wartość: **niezdefiniowane**|  
|**Wartość null**|Pojedyncza wartość: **wartości null**|  
|**Boolean**|Wartości: **false**, **true**.|  
|**Numer**|Liczba podwójnej precyzji liczb zmiennoprzecinkowych, IEEE-754 standardowa.|  
|**Ciąg**|Sekwencja zero lub więcej znaków Unicode. Ciągi, musi być ujęty w pojedynczym lub podwójnym cudzysłowie.|  
|**Tablica**|Sekwencja zero lub więcej elementów. Każdy element może być wartością dowolnego typu danych skalarnych, z wyjątkiem Undefined.|  
|**Obiekt**|Nieuporządkowaną zestaw par nazwa/wartość zero lub więcej. Nazwa jest ciągiem Unicode, wartość może być dowolnego typu danych skalarnych, z wyjątkiem **niezdefiniowane**.|  
  
 **Składnia**  
  
```  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
 **Argumenty**  
  
1.  `<undefined_constant>; undefined`  
  
     Reprezentuje niezdefiniowana wartość typu Undefined.  
  
2.  `<null_constant>; null`  
  
     Reprezentuje **null** wartości typu **Null**.  
  
3.  `<boolean_constant>`  
  
     Reprezentuje stała typu Boolean.  
  
4.  `false`  
  
     Reprezentuje **false** wartość typu Boolean.  
  
5.  `true`  
  
     Reprezentuje **true** wartość typu Boolean.  
  
6.  `<number_constant>`  
  
     Reprezentuje stałą.  
  
7.  `decimal_literal`  
  
     Literałów dziesiętnych są liczbami reprezentowanej przy użyciu notacji dziesiętnej lub wykładniczej.  
  
8.  `hexadecimal_literal`  
  
     Literały szesnastkowe są przedstawiane za pomocą prefiks '0 x' następuje co najmniej jedną cyfrę szesnastkową liczby.  
  
9. `<string_constant>`  
  
     Reprezentuje stałą typu String.  
  
10. `string _literal`  
  
     Literały ciągu są reprezentowane przez sekwencję zero lub więcej znaków Unicode lub sekwencji unikowych ciągów Unicode. Literały ciągu są ujęte w apostrofy (apostrof: ") lub podwójne cudzysłowy (cudzysłów:").  
  
 Dopuszczalne są następujące sekwencje unikowe:  
  
|**Sekwencja specjalna**|**Opis**|**Znak Unicode**|  
|-|-|-|  
|\\'|apostrof (')|U+0027|  
|\\"|znak cudzysłowu (")|U+0022|  
|\\\|Odwrotna kreska ułamkowa (\\)|U+005C|  
|\\/|kreska ułamkowa (/)|U+002F|  
|\b|BACKSPACE|U+0008|  
|\f|Wysuw strony|U+000C|  
|\n|wysuwu wiersza|U+000A|  
|\r|Powrót karetki|U+000D|  
|\t|tabulator|U+0009|  
|\uXXXX|Znak Unicode, zdefiniowane przez 4 cyfr szesnastkowych.|U+XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a> Wskazówki dotyczące wydajności kwerendy  
 Aby zapytanie w celu wykonania wydajnie dla dużych kolekcji powinien on używać filtry, które mogą być przekazywane za pośrednictwem jednego lub kilku indeksów.  
  
 Indeks wyszukiwania będzie mogła zostać usunięta następujące filtry:  
  
-   Za pomocą operatora równości (=) wyrażenie ścieżki dokumentu i stałą.  
  
-   Operatory zakresu (<, \<=, >, > =) z wyrażenie ścieżki dokumentu i number — stałe.  
  
-   Wyrażenie ścieżki dokumentu oznacza dowolne Wyrażenie identyfikujące stałej ścieżki w dokumentach z kolekcji bazy danych.  
  
 **Wyrażenie ścieżki dokumentu**  
  
 Wyrażenia ścieżki dokumentu są wyrażenia który ścieżki właściwości lub tablicy oceniających indeksatora za pośrednictwem dokumentu pochodzące z bazy danych kolekcji dokumentów. Tę ścieżkę można zidentyfikować lokalizację wartości filtru bezpośrednio z poziomu dokumentów w kolekcji bazy danych.  
  
 Wyrażenie wziąć pod uwagę wyrażenie ścieżki dokumentu powinno:  
  
1.  Bezpośrednie odwołanie główny kolekcji.  
  
2.  Indeksator odwołanie do właściwości lub stała tablicy niektóre wyrażenia ścieżki dokumentu  
  
3.  Odwołanie alias, który reprezentuje wyrażenie ścieżki niektórych dokumentów.  
  
     **Konwencje składni**  
  
     W poniższej tabeli opisano konwencje używana do opisu składni w następujące odwołanie SQL.  
  
    |**Konwencja**|**Używany do**|  
    |-|-|    
    |WIELKIE LITERY|Słowa kluczowe bez uwzględniania wielkości liter.|  
    |małe litery|Słowa kluczowe z uwzględnieniem wielkości liter.|  
    |\<nonterminal >|Nonterminal, definiowane osobno.|  
    |\<nonterminal >:: =|Definicja składni nonterminal.|  
    |other_terminal|Terminali (token) opisano szczegółowo w wyrazy.|  
    |Identyfikator|Identyfikator. Umożliwia następujące znaki: a-z A-Z 0-9 _First znak nie może być cyfrą.|  
    |"string"|Ciąg w cudzysłowie. Umożliwia dowolny prawidłowy ciąg. Zobacz opis literał.|  
    |'symbol'|Literał symbol, który jest częścią składni.|  
    |&#124;(pionowa kreska)|Alternatywy dla elementy składni. Można użyć tylko jednej z określonych elementów.|  
    |/(brackets)]|Nawiasy kwadratowe powinno być jeden lub więcej elementów opcjonalnych.|  
    |[,.. .n]|Wskazuje, że poprzedni element może być powtarzane n liczbę razy. Wystąpienia są oddzielone przecinkami.|  
    |[.. .n]|Wskazuje, że poprzedni element może być powtarzane n liczbę razy. Wystąpienia są oddzielone puste wartości.|  
  
##  <a name="bk_built_in_functions"></a> Funkcje wbudowane  
 Azure DB rozwiązania Cosmos udostępnia wiele wbudowanych funkcji SQL. Poniżej wymieniono kategorie funkcji wbudowanych.  
  
|Funkcja|Opis|  
|--------------|-----------------|  
|[Funkcje matematyczne](#bk_mathematical_functions)|Funkcje matematyczne wykonywanie obliczeń, zwykle oparte na wartości wejściowych, które są przekazywane jako argumenty i zwracać wartość liczbową.|  
|[Typ funkcji sprawdzania](#bk_type_checking_functions)|Funkcje sprawdzania typu umożliwiają sprawdzanie typu wyrażenia w obrębie zapytania SQL.|  
|[Funkcje ciągów](#bk_string_functions)|Funkcje ciągów wykonania operacji w ciągu wartości wejściowej i zwraca ciąg, wartość liczbowa lub wartość logiczna.|  
|[Funkcje tablicy](#bk_array_functions)|Funkcje tablicy wykonywać operacji na tablicy wartości wejściowej i przywracać liczbowych, wartość logiczną lub tablicy.|  
|[Funkcje przestrzenne](#bk_spatial_functions)|Funkcje przestrzenne wykonania operacji na wartości wejściowej obiektu przestrzennego i zwracać wartość liczbowa lub wartość logiczna.|  
  
###  <a name="bk_mathematical_functions"></a> Funkcje matematyczne  
 Następujące funkcje każdego wykonywanie obliczeń, zwykle oparte na wartości wejściowych, które są przekazywane jako argumenty i zwracać wartość liczbową.  
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[LIMITU](#bk_ceiling)|  
|[COS](#bk_cos)|[KOT](#bk_cot)|[STOPNI](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[DZIENNIK](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[ZASILANIA](#bk_power)|  
|[RADIANS](#bk_radians)|[ROUND](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[KWADRATOWE](#bk_square)|[ZALOGUJ SIĘ](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC —](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 Zwraca wartość bezwzględną (dodatnia) z określonego wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 W poniższym przykładzie przedstawiono wyniki za pomocą funkcji ABS na trzy różne liczby.  
  
```  
SELECT ABS(-1), ABS(0), ABS(1)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 1, $2: 0, $3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Zwraca kąt w radianach, którego cosinus jest określonego wyrażenia liczbowego; Skrót cosinus.  
  
 **Składnia**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca ACOS-1.  
  
```  
SELECT ACOS(-1)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Zwraca kąt w radianach, którego sinusem jest dana określonego wyrażenia liczbowego. Jest to również sinus.  
  
 **Składnia**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca ASIN-1.  
  
```  
SELECT ASIN(-1)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Zwraca kąt w radianach, którego tangens jest określonego wyrażenia liczbowego. Jest to również tangens.  
  
 **Składnia**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca ATAN określonej wartości.  
  
```  
SELECT ATAN(-45.01)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Zwraca wartość główną arcus tangens y / x, wyrażone w radianach.  
  
 **Składnia**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 W poniższym przykładzie oblicza ATN2 dla określonego x i y składników.  
  
```  
SELECT ATN2(35.175643, 129.44)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> LIMITU  
 Zwraca najmniejszą wartość całkowita większa lub równa określonej wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 W poniższym przykładzie przedstawiono alfanumeryczne dodatnią, ujemna i wartości zerowe za pomocą funkcji limitu.  
  
```  
SELECT CEILING(123.45), CEILING(-123.45), CEILING(0.0)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 124, $2: -123, $3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 Zwraca cosinus trygonometryczne o określony kąt w radianach, określone wyrażenie.  
  
 **Składnia**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 W poniższym przykładzie oblicza COS podanemu kątowi.  
  
```  
SELECT COS(14.78)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> KOT  
 Zwraca trygonometryczne cotangens kąta określonego w radianach, określonego wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 W poniższym przykładzie oblicza KOT podanemu kątowi.  
  
```  
SELECT COT(124.1332)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> STOPNI  
 Zwraca odpowiadający mu kąt w stopniach dla kąta określonego w radianach.  
  
 **Składnia**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca liczbę stopni w kąta PI/2 radianów.  
  
```  
SELECT DEGREES(PI()/2)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 Zwraca największą liczbę całkowitą mniejszą niż określona wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 W poniższym przykładzie przedstawiono alfanumeryczne dodatnią, ujemna i wartości zerowe za pomocą funkcji FLOOR.  
  
```  
SELECT FLOOR(123.45), FLOOR(-123.45), FLOOR(0.0)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 123, $2: -124, $3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Zwraca wartość wykładniczej określonego wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Uwagi**  
  
 Stała **e** (2.718281...), jest podstawą podstawę logarytmu naturalnego.  
  
 Wykładnik liczby jest stałą **e** podniesionej do potęgi liczby. Na przykład EXP(1.0) = e ^ 1.0 = 2.71828182845905 i EXP(10) = e ^ 10 = 22026.4657948067.  
  
 Wykładniczy z logarytm naturalny liczby. jest to liczba sam: EXP (dziennik (n)) = n. I logarytm naturalny wykładnicza liczby. jest to liczba sam: dziennika (EXP (n)) = n.  
  
 **Przykłady**  
  
 Poniższy przykład deklaruje zmienną i zwraca wartość wykładniczej określonej zmiennej (10).  
  
```  
SELECT EXP(10)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 22026.465794806718}]  
```  
  
 Poniższy przykład zwraca wartość wykładniczej natural logarithm 20 i wykładniczej 20 logarytmu naturalnego. Ponieważ te funkcje są funkcje odwrócone siebie, wartość zwracana z zaokrąglania ruchomy punkt matematyczne w obu przypadkach wynosi 20.  
  
```  
SELECT EXP(LOG(20)), LOG(EXP(20))  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 19.999999999999996, $2: 20}]  
```  
  
####  <a name="bk_log"></a> DZIENNIK  
 Zwraca logarytm naturalny z określonego wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
-   `base`  
  
     Opcjonalny argument numeryczny, która ustawia podstawa logarytmu.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Uwagi**  
  
 Domyślnie LOG() Zwraca logarytm naturalny. Podstawa logarytmu można zmienić na inną wartość, przy użyciu opcjonalny parametr podstawowego.  
  
 Logarytm naturalny to logarytm o podstawie **e**, gdzie **e** jest równa około 2.718281828 nieracjonalnej stałą.  
  
 Logarytm naturalny wykładnicza liczby. jest to liczba sam: dziennika (EXP (n)) = n. I wykładniczej z logarytm naturalny liczby. jest to liczba sam: EXP (dziennik (n)) = n.  
  
 **Przykłady**  
  
 Poniższy przykład deklaruje zmienną i zwraca wartość logarytmu określonej zmiennej (10).  
  
```  
SELECT LOG(10)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 2.3025850929940459}]  
```  
  
 W poniższym przykładzie oblicza dziennika dla wykładnik liczby.  
  
```  
SELECT EXP(LOG(10))  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 Zwraca logarytm base 10 określonego wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Uwagi**  
  
 Funkcje LOG10 i zasilania są odwrotnie ze sobą powiązane. Na przykład 10 ^ LOG10(n) = n.  
  
 **Przykłady**  
  
 Poniższy przykład deklaruje zmienną i zwraca wartość LOG10 określonej zmiennej (100).  
  
```  
SELECT LOG10(100)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 Zwraca stałą wartość liczby PI.  
  
 **Składnia**  
  
```  
PI ()  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca wartość liczby PI.  
  
```  
SELECT PI()  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> ZASILANIA  
 Zwraca wartość określonego wyrażenia do określonej potęgi.  
  
 **Składnia**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
-   `y`  
  
     Jest to prawo, do którego zostanie wywołane `numeric_expression`.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 W poniższym przykładzie pokazano podniesienia liczby do potęgi 3 (modułu numer).  
  
```  
SELECT POWER(2, 3), POWER(2.5, 3)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 8, $2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> WARTOŚĆ W RADIANACH  
 Zwraca wartość w radianach, po wprowadzeniu w stopniach, wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 W poniższym przykładzie przyjmuje jako dane wejściowe kilka kąty i zwraca odpowiednimi wartościami radianach.  
  
```  
SELECT RADIANS(-45.01), RADIANS(-181.01), RADIANS(0), RADIANS(0.1472738), RADIANS(197.1099392)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{  
       "$1": -0.7855726963226477,  
       "$2": -3.1592204790349356,  
       "$3": 0,  
       "$4": 0.0025704127119236249,  
       "$5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 Zwraca wartość liczbową zaokrąglona do najbliższej wartości całkowitej.  
  
 **Składnia**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład zaokrągla liczby dodatnie i ujemne następujące do najbliższej liczby całkowitej.  
  
```  
SELECT ROUND(2.4), ROUND(2.6), ROUND(2.5), ROUND(-2.4), ROUND(-2.6)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 2, $2: 3, $3: 3, $4: -2, $5: -3}]  
```  
  
####  <a name="bk_sign"></a> ZALOGUJ SIĘ  
 Zwraca plus (+ 1), 0 (zero) lub znakiem minus (-1) z określonego wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca wartości znak liczby z -2, 2.  
  
```  
SELECT SIGN(-2), SIGN(-1), SIGN(0), SIGN(1), SIGN(2)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: -1, $2: -1, $3: 0, $4: 1, $5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 Zwraca sinus określonego kąta trygonometryczne w radianach, określone wyrażenie.  
  
 **Składnia**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 W poniższym przykładzie oblicza SINUS określonego kąta.  
  
```  
SELECT SIN(45.175643)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 Zwraca pierwiastek kwadratowy z określoną wartość liczbową.  
  
 **Składnia**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca pierwiastków kwadratowych liczb od 1 do 3.  
  
```  
SELECT SQRT(1), SQRT(2.0), SQRT(3)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 1, $2: 1.4142135623730952, $3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> KWADRATOWE  
 Zwraca kwadrat określoną wartość liczbową.  
  
 **Składnia**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca kwadratów liczb od 1 do 3.  
  
```  
SELECT SQUARE(1), SQUARE(2.0), SQUARE(3)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 1, $2: 4, $3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 Zwraca tangens kąta określonego w radianach, określone wyrażenie.  
  
 **Składnia**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 W poniższym przykładzie oblicza tangens () PI / 2.  
  
```  
SELECT TAN(PI()/2);  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC —  
 Zwraca wartość liczbową obcięte do najbliższej wartości całkowitej.  
  
 **Składnia**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład obcina następujące liczby dodatnie i ujemne do najbliższej wartości całkowitej.  
  
```  
SELECT TRUNC(2.4), TRUNC(2.6), TRUNC(2.5), TRUNC(-2.4), TRUNC(-2.6)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 2, $2: 2, $3: 2, $4: -2, $5: -2}]  
```  
  
###  <a name="bk_type_checking_functions"></a> Typ funkcji sprawdzania  
 Następujące funkcje obsługi typu sprawdzanie względem wartości wejściowe, a każdy zwracać wartość logiczną.  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest typem tablicy.  
  
 **Składnia**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Jest dowolne prawidłowe wyrażenie.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza obiektów JSON Boolean, numer, ciąg, wartość null, obiekt, tablicy i niezdefiniowane typów przy użyciu funkcji is_array —.  
  
```  
SELECT   
 IS_ARRAY(true),   
 IS_ARRAY(1),  
 IS_ARRAY("value"),  
 IS_ARRAY(null),  
 IS_ARRAY({prop: "value"}),   
 IS_ARRAY([1, 2, 3]),  
 IS_ARRAY({prop: "value"}.prop2)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: false, $6: true}]  
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenie jest wartością logiczną.  
  
 **Składnia**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Jest dowolne prawidłowe wyrażenie.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza obiektów JSON Boolean, numer, ciąg, wartość null, obiekt, tablicy i niezdefiniowane typów przy użyciu funkcji IS_BOOL.  
  
```  
SELECT   
    IS_BOOL(true),   
    IS_BOOL(1),  
    IS_BOOL("value"),   
    IS_BOOL(null),  
    IS_BOOL({prop: "value"}),   
    IS_BOOL([1, 2, 3]),  
    IS_BOOL({prop: "value"}.prop2)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: true, $2: false, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 Zwraca wartość Boolean wskazującą, czy właściwość zostanie przypisana wartość.  
  
 **Składnia**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Jest dowolne prawidłowe wyrażenie.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza obecność właściwości w ramach określonego dokumentu JSON. Pierwszy zwraca wartość true, ponieważ "a" jest obecny, ale druga zwraca wartość false, ponieważ nie ma "b".  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a), IS_DEFINED({ "a" : 5 }.b)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{  
       "$1": true,    
       "$2": false   
   }]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest wartość null.  
  
 **Składnia**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Jest dowolne prawidłowe wyrażenie.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza obiektów JSON Boolean, numer, ciąg, wartość null, obiekt, tablicy i niezdefiniowane typów przy użyciu funkcji IS_NULL.  
  
```  
SELECT   
    IS_NULL(true),   
    IS_NULL(1),  
    IS_NULL("value"),   
    IS_NULL(null),  
    IS_NULL({prop: "value"}),   
    IS_NULL([1, 2, 3]),  
    IS_NULL({prop: "value"}.prop2)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: false, $2: false, $3: false, $4: true, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest liczbą.  
  
 **Składnia**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Jest dowolne prawidłowe wyrażenie.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza obiektów JSON Boolean, numer, ciąg, wartość null, obiekt, tablicy i niezdefiniowane typów przy użyciu funkcji IS_NULL.  
  
```  
SELECT   
    IS_NUMBER(true),   
    IS_NUMBER(1),  
    IS_NUMBER("value"),   
    IS_NUMBER(null),  
    IS_NUMBER({prop: "value"}),   
    IS_NUMBER([1, 2, 3]),  
    IS_NUMBER({prop: "value"}.prop2)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: false, $2: true, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest obiektem JSON.  
  
 **Składnia**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Jest dowolne prawidłowe wyrażenie.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza obiektów JSON Boolean, numer, ciąg, wartość null, obiekt, tablicy i niezdefiniowane typów przy użyciu funkcji is_object —.  
  
```  
SELECT   
    IS_OBJECT(true),   
    IS_OBJECT(1),  
    IS_OBJECT("value"),   
    IS_OBJECT(null),  
    IS_OBJECT({prop: "value"}),   
    IS_OBJECT([1, 2, 3]),  
    IS_OBJECT({prop: "value"}.prop2)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: true, $6: false}]  
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest właściwością pierwotną (string, Boolean, liczbowa lub wartość null).  
  
 **Składnia**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Jest dowolne prawidłowe wyrażenie.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza obiektów JSON Boolean, numer, ciąg, wartość null, obiekt, tablicy i niezdefiniowane typów przy użyciu funkcji IS_PRIMITIVE.  
  
```  
SELECT   
           IS_PRIMITIVE(true),   
           IS_PRIMITIVE(1),  
           IS_PRIMITIVE("value"),   
           IS_PRIMITIVE(null),  
           IS_PRIMITIVE({prop: "value"}),   
           IS_PRIMITIVE([1, 2, 3]),  
           IS_PRIMITIVE({prop: "value"}.prop2)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": true, "$2": true, "$3": true, "$4": true, "$5": false, "$6": false, "$7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest ciągiem.  
  
 **Składnia**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Jest dowolne prawidłowe wyrażenie.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza obiektów JSON Boolean, numer, ciąg, wartość null, obiekt, tablicy i niezdefiniowane typów przy użyciu funkcji IS_STRING.  
  
```  
SELECT   
       IS_STRING(true),   
       IS_STRING(1),  
       IS_STRING("value"),   
       IS_STRING(null),  
       IS_STRING({prop: "value"}),   
       IS_STRING([1, 2, 3]),  
       IS_STRING({prop: "value"}.prop2)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: false, $2: false, $3: true, $4: false, $5: false, $6: false}]  
```  
  
###  <a name="bk_string_functions"></a> Funkcje ciągów  
 Następujące funkcje skalarne wykonania operacji w ciągu wartości wejściowej i zwraca ciąg, wartość liczbowa lub wartość logiczna.  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[ZAWIERA](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[DŁUGOŚĆ](#bk_length)|  
|[NIŻSZE](#bk_lower)|[PRZYTP](#bk_ltrim)|[ZAMIEŃ](#bk_replace)|  
|[REPLIKUJ](#bk_replicate)|[REVERSE](#bk_reverse)|[PRAWO](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[SUBSTRING](#bk_substring)|  
|[GÓRNY](#bk_upper)|||  
  
####  <a name="bk_concat"></a> CONCAT  
 Zwraca ciąg, który jest wynikiem łączenie dwóch lub więcej wartości ciągu.  
  
 **Składnia**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolne wyrażenie prawidłowy ciąg.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca połączony ciąg z określonymi wartościami.  
  
```  
SELECT CONCAT("abc", "def")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> ZAWIERA  
 Zwraca wartość Boolean wskazującą, czy pierwszy wyrażenia ciągu zawiera drugi.  
  
 **Składnia**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolne wyrażenie prawidłowy ciąg.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład umożliwia sprawdzenie, czy "abc" zawiera "ab" a "d".  
  
```  
SELECT CONTAINS("abc", "ab"), CONTAINS("abc", "d")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": true, "$2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Zwraca wartość Boolean wskazującą, czy pierwszy wyrażenia ciągu kończy się na sekundę.  
  
 **Składnia**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolne wyrażenie prawidłowy ciąg.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca "abc" kończy się wyrazem "b" i "bc".  
  
```  
SELECT ENDSWITH("abc", "b"), ENDSWITH("abc", "bc")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 Zwraca pozycję początkową pierwszego wystąpienia drugi ciąg wyrażenia w pierwszym wyrażeniu określony ciąg lub wartość -1, jeśli nie zostanie znaleziony ciąg.  
  
 **Składnia**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolne wyrażenie prawidłowy ciąg.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca indeks różnych podciągów wewnątrz "abc".  
  
```  
SELECT INDEX_OF("abc", "ab"), INDEX_OF("abc", "b"), INDEX_OF("abc", "c")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": 0, "$2": 1, "$3": -1}]  
```  
  
####  <a name="bk_left"></a> LEFT  
 Zwraca lewej części ciągu z określoną liczbę znaków.  
  
 **Składnia**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolne wyrażenie prawidłowy ciąg.  
  
-   `num_expr`  
  
     Jest dowolne prawidłowe wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca lewej części "abc" dla różnych wartości długości.  
  
```  
SELECT LEFT("abc", 1), LEFT("abc", 2)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "a", "$2": "ab"}]  
```  
  
####  <a name="bk_length"></a> DŁUGOŚĆ  
 Zwraca liczbę znaków z określonego wyrażenia ciągu.  
  
 **Składnia**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolne wyrażenie prawidłowy ciąg.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca długość ciągu.  
  
```  
SELECT LENGTH("abc")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_lower"></a> NIŻSZE  
 Zwraca wyrażenie ciągu po konwersji danych wielką literę na małe litery.  
  
 **Składnia**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolne wyrażenie prawidłowy ciąg.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład przedstawia użycie NIŻSZYCH w zapytaniu.  
  
```  
SELECT LOWER("Abc")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> PRZYTP  
 Zwraca wyrażenie ciągu, po usuwa spacje wiodące.  
  
 **Składnia**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolne wyrażenie prawidłowy ciąg.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład przedstawia użycie LTRIM w zapytaniu.  
  
```  
SELECT LTRIM("  abc"), LTRIM("abc"), LTRIM("abc   ")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "abc", "$2": "abc", "$3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> ZAMIEŃ  
 Zamienia wszystkie wystąpienia określonej wartości ciągu na inną wartość ciągu.  
  
 **Składnia**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolne wyrażenie prawidłowy ciąg.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład przedstawia użycie ZAMIEŃ w zapytaniu.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLIKOWANIE  
 Wartość ciągu jest powtarzany określoną liczbę razy.  
  
 **Składnia**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolne wyrażenie prawidłowy ciąg.  
  
-   `num_expr`  
  
     Jest dowolne prawidłowe wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład przedstawia użycie REPLIKACJA w zapytaniu.  
  
```  
SELECT REPLICATE("a", 3)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 Zwraca odwrotnej kolejności wartość ciągu.  
  
 **Składnia**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolne wyrażenie prawidłowy ciąg.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład przedstawia użycie wstecznego w zapytaniu.  
  
```  
SELECT REVERSE("Abc")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "cbA"}]  
```  
  
####  <a name="bk_right"></a> PRAWO  
 Zwraca prawa część ciągu z określoną liczbę znaków.  
  
 **Składnia**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolne wyrażenie prawidłowy ciąg.  
  
-   `num_expr`  
  
     Jest dowolne prawidłowe wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca prawa część "abc" dla różnych wartości długości.  
  
```  
SELECT RIGHT("abc", 1), RIGHT("abc", 2)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "c", "$2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> PRZYTK  
 Zwraca wyrażenie ciągu, po usuwa spacje końcowe.  
  
 **Składnia**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolne wyrażenie prawidłowy ciąg.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład przedstawia użycie RTRIM w zapytaniu.  
  
```  
SELECT RTRIM("  abc"), RTRIM("abc"), RTRIM("abc   ")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "   abc", "$2": "abc", "$3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Zwraca wartość Boolean wskazującą, czy pierwszy wyrażenia ciągu rozpoczyna się od drugiego.  
  
 **Składnia**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolne wyrażenie prawidłowy ciąg.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza, czy ciąg "abc" rozpoczyna się od "b" i "a".  
  
```  
SELECT STARTSWITH("abc", "b"), STARTSWITH("abc", "a")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_substring"></a> SUBSTRING  
 Zwraca część wyrażenia ciągu, zaczynając od pozycji określony znak liczony od zera i kontynuuje określonej długości lub końca ciągu.  
  
 **Składnia**  
  
```  
SUBSTRING(<str_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolne wyrażenie prawidłowy ciąg.  
  
-   `num_expr`  
  
     Jest dowolne prawidłowe wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca podciąg "abc", począwszy od 1 i o długości 1 znak.  
  
```  
SELECT SUBSTRING("abc", 1, 1)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "b"}]  
```  
  
####  <a name="bk_upper"></a> GÓRNY  
 Zwraca wyrażenie ciągu po konwersji danych małą literę na wielkie litery.  
  
 **Składnia**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolne wyrażenie prawidłowy ciąg.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład przedstawia sposób użycia wielkich w zapytaniu  
  
```  
SELECT UPPER("Abc")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "ABC"}]  
```  
  
###  <a name="bk_array_functions"></a> Funkcje tablicy  
 Następujące funkcje skalarne wykonania operacji na tablicy wartości wejściowej i powrotu liczbowego, wartość logiczną lub tablicy  
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 Zwraca tablicę, która jest wynikiem łączenie dwóch lub więcej wartości tablicy.  
  
 **Składnia**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **Argumenty**  
  
-   `arr_expr`  
  
     Jest dowolne wyrażenie prawidłowy tablicy.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie tablicy.  
  
 **Przykłady**  
  
 Poniższy przykład sposobu łączenia dwóch tablic.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"])  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
Zwraca wartość Boolean wskazującą, czy tablica zawiera określoną wartość. Można określić, czy dopasowanie jest pełny lub jego część. 

 **Składnia**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argumenty**  
  
-   `arr_expr`  
  
     Jest dowolne wyrażenie prawidłowy tablicy.  
  
-   `expr`  
  
     Jest dowolne prawidłowe wyrażenie.  

-   `bool_expr`  
  
     Jest wyrażenia logicznego.       
  
 **Zwracane typy**  
  
 Zwraca wartość logiczną.  
  
 **Przykłady**  
  
 Poniższy przykład jak sprawdzić członkostwa w tablicy przy użyciu ARRAY_CONTAINS.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples"),  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": true, "$2": false}]  
```  

 Poniższy przykład sprawdzania częściowe dopasowanie JSON w tablicy przy użyciu ARRAY_CONTAINS.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true), 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}),
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{
  "$1": true,
  "$2": false,
  "$3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 Zwraca liczbę elementów wyrażenia określonej tablicy.  
  
 **Składnia**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argumenty**  
  
-   `arr_expr`  
  
     Jest dowolne wyrażenie prawidłowy tablicy.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład sposobu uzyskania długość tablicy przy użyciu ARRAY_LENGTH.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"])  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 Zwraca część wyrażenie tablicy.
  
 **Składnia**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumenty**  
  
-   `arr_expr`  
  
     Jest dowolne wyrażenie prawidłowy tablicy.  
  
-   `num_expr`  
  
     Jest dowolne prawidłowe wyrażenie liczbowe.  
  
 **Zwracane typy**  
  
 Zwraca wartość logiczną.  
  
 **Przykłady**  
  
 Poniższy przykład sposobu uzyskania część tablicy przy użyciu ARRAY_SLICE.  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1),  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{  
           "$1": ["strawberries", "bananas"],   
           "$2": ["strawberries"]  
       }]  
```  
  
###  <a name="bk_spatial_functions"></a> Funkcje przestrzenne  
 Następujące funkcje skalarne wykonania operacji na wartości wejściowej obiektu przestrzennego i zwracać wartość liczbowa lub wartość logiczna.  
  
||||  
|-|-|-|  
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|  
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)|||  
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 Zwraca odległość między dwoma wyrażeniami GeoJSON punktu wielokąta i LineString.  
  
 **Składnia**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumenty**  
  
-   `spatial_expr`  
  
     Jest dowolne prawidłowe wyrażenie obiektu GeoJSON punktu wielokąta i LineString.  
  
 **Zwracane typy**  
  
 Zwraca wartość wyrażenia liczbowego zawierający odległość. To wymaganie jest wyrażone w liczników dla systemu odniesienia domyślne.  
  
 **Przykłady**  
  
 Poniższy przykład przedstawia sposób zwrócenia wszystkich dokumentów rodziny, znajdujące się w ramach 30 km do określonej lokalizacji za pomocą wbudowanych funkcji ST_DISTANCE. .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 Zwraca wartość wskazującą, czy obiektu GeoJSON (punkt, wielokąta lub LineString) określony w pierwszym argumencie jest w GeoJSON (punkt, wielokąta lub LineString) drugiego argumentu wyrażenia logicznego.  
  
 **Składnia**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumenty**  
  
-   `spatial_expr`  
  
     Jest dowolne prawidłowe wyrażenie obiektu GeoJSON punktu wielokąta i LineString.  
 
-   `spatial_expr`  
  
     Jest dowolne prawidłowe wyrażenie obiektu GeoJSON punktu wielokąta i LineString.  
  
 **Zwracane typy**  
  
 Zwraca wartość logiczną.  
  
 **Przykłady**  
  
 Poniższy przykład pokazuje, jak można znaleźć wszystkie dokumenty rodziny za pomocą ST_WITHIN wielokąta.  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 Zwraca wartość wskazującą, czy obiektu GeoJSON (punkt, wielokąta lub LineString) określony w pierwszym argumencie przecina GeoJSON (punkt, wielokąta lub LineString) drugiego argumentu wyrażenia logicznego.  
  
 **Składnia**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumenty**  
  
-   `spatial_expr`  
  
     Jest dowolne prawidłowe wyrażenie obiektu GeoJSON punktu wielokąta i LineString.  
 
-   `spatial_expr`  
  
     Jest dowolne prawidłowe wyrażenie obiektu GeoJSON punktu wielokąta i LineString.  
  
 **Zwracane typy**  
  
 Zwraca wartość logiczną.  
  
 **Przykłady**  
  
 Poniższy przykład pokazuje, jak można odnaleźć wszystkie obszary, które przecina z danym wielokąta.  
  
```  
SELECT a.id   
FROM Areas a   
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 Zwraca wartość logiczną wskazującą, czy określone wyrażenie GeoJSON punktu wielokąta i LineString jest nieprawidłowy.  
  
 **Składnia**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumenty**  
  
-   `spatial_expr`  
  
     Jest dowolne prawidłowe wyrażenie GeoJSON punktu wielokąta i LineString.  
  
 **Zwracane typy**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład przedstawia sposób sprawdzania, czy punkt jest prawidłowy, przy użyciu ST_VALID.  
  
 Na przykład ten punkt ma wartość szerokości geograficznej, który nie jest prawidłowy zakres wartości [-90, 90], więc zapytanie zwraca wartość false.  
  
 Wielokąty Specyfikacja GeoJSON wymaga ostatnią parę współrzędnych podane powinna być taka sama jak pierwsza strona, aby utworzyć zamknięty kształt. Punktów w wielokąta muszą być określone w kolejności przeciwnie do ruchu wskazówek zegara. Wielokąt określony w kolejności zegara reprezentuje odwrotność regionu w niej.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{ "$1": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Zwraca wartość wartość JSON, zawierające wartość typu Boolean, jeśli określone wyrażenie GeoJSON punktu wielokąta i LineString jest prawidłowy, a nieprawidłowy, ponadto Przyczyna jako wartość typu ciąg.  
  
 **Składnia**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumenty**  
  
-   `spatial_expr`  
  
     Jest dowolne GeoJSON prawidłowe wyrażenie punktu lub wielokąta.  
  
 **Zwracane typy**  
  
 Zwraca wartość wartość JSON, zawierające wartość typu Boolean, jeśli określony punkt lub wielokąta wyrażenie GeoJSON jest prawidłowe, a nieprawidłowy, ponadto Przyczyna jako wartość typu ciąg.  
  
 **Przykłady**  
  
 Poniższy przykład sposobu sprawdzania poprawności (ze szczegółami) przy użyciu ST_ISVALIDDETAILED.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
})  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{  
  "$1": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
  
## <a name="next-steps"></a>Kolejne kroki  
 [Składnia SQL i kwerendy SQL dla bazy danych Azure rozwiązania Cosmos](sql-api-sql-query.md)   
 [Dokumentację platformy Azure DB rozwiązania Cosmos](https://docs.microsoft.com/azure/cosmos-db/)  
  
  
