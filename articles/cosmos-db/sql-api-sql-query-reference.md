---
title: 'Usługi Azure Cosmos DB: Dokumentacja zapytań składni SQL | Dokumentacja firmy Microsoft'
description: Dokumentacja dotycząca języka zapytań SQL usługi Azure Cosmos DB.
services: cosmos-db
author: LalithaMV
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: reference
ms.date: 08/19/2018
ms.author: laviswa
ms.openlocfilehash: 33614628926e53354db14886530d7ca44da61f0a
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "42057392"
---
# <a name="azure-cosmos-db-sql-syntax-reference"></a>Dokumentacja składni w usłudze Azure Cosmos DB SQL

Środowisko usługi Azure Cosmos DB obsługuje wykonywanie zapytań względem dokumentów za pomocą dobrze znanych SQL (Structured Query Language), takich jak gramatyka za pośrednictwem hierarchiczne dokumentów JSON bez konieczności jawnego schematu lub tworzenia indeksów pomocniczych. Ten artykuł zawiera dokumentacja odwołania/składni język zapytań SQL, które są zgodne z kontami interfejsu API SQL. Przewodnik SQL zapytań z przykładowych danych w temacie [wysyłania zapytań dotyczących danych usługi Azure Cosmos DB](sql-api-sql-query.md).  
  
Odwiedź stronę [Plac zabaw dla zapytań](http://www.documentdb.com/sql/demo) gdzie możesz wypróbować usługę Azure Cosmos DB i uruchamiać zapytania SQL względem naszego zestawu danych.  
  
## <a name="select-query"></a>Wybierz zapytanie  
Każdego zapytania, który składa się z klauzuli SELECT i opcjonalnie FROM a klauzulach WHERE w standardy ANSI SQL. Zwykle dla każdego zapytania do źródła w klauzuli FROM są wyliczane. Następnie w klauzuli WHERE filtrowaniu na "source" do pobrania podzbioru dokumentów JSON. Na koniec klauzuli SELECT jest używany do projektu wymagane wartości JSON na liście wyboru. W sekcji Konwencji składni wyszczególniono w Konwencji używanych do opisywania instrukcji "SELECT". Aby uzyskać przykłady, zobacz [przykładowe zapytanie SELECT](sql-api-sql-query.md#SelectClause)
  
**Składnia**  
  
```sql
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ]  
```  
  
 **Uwagi**  
  
 Zobacz następujące sekcje, aby uzyskać szczegółowe informacje o każdej klauzuli:  
  
-   [Klauzula SELECT](#bk_select_query)    
-   [FROM — klauzula](#bk_from_clause)    
-   [Klauzula WHERE](#bk_where_clause)    
-   [Klauzula ORDER BY](#bk_orderby_clause)  
  
Muszą być uporządkowane klauzule w instrukcji SELECT, jak pokazano powyżej. Jeden opcjonalna klauzula można pominąć. Jednak gdy klauzule opcjonalne są używane, musi występować w odpowiedniej kolejności.  
  
### <a name="logical-processing-order-of-the-select-statement"></a>Logiczna kolejność przetwarzania instrukcji SELECT  
  
Kolejność, w jakiej są przetwarzane klauzule jest:  

1.  [FROM — klauzula](#bk_from_clause)  
2.  [Klauzula WHERE](#bk_where_clause)  
3.  [Klauzula ORDER BY](#bk_orderby_clause)  
4.  [Klauzula SELECT](#bk_select_query)  

Należy pamiętać, że to różni się od kolejności, w jakiej występują w składni. Kolejność jest tak, aby wszystkie nowe symbole wynikające z klauzulą przetwarzania są widoczne i mogą być używane w klauzulach późniejszego przetworzenia. Na przykład zadeklarowanych w klauzuli FROM aliasy są dostępne w przypadku gdy i klauzule SELECT.  

### <a name="whitespace-characters-and-comments"></a>Białych znaków i komentarze  

Wszystkie białe znaki, które nie są częścią ciągów w cudzysłowach lub identyfikator w cudzysłowach nie są częścią gramatyki języka i są ignorowane podczas analizowania.  

Język zapytań obsługuje komentarze style języka T-SQL, takich jak  

-   Instrukcja SQL `-- comment text [newline]`  

Gdy białych znaków i komentarze nie ma żadnego znaczenia w gramatyce, muszą one używane do oddzielania tokenów. Na przykład: `-1e5` chwilę jednego tokenu, liczba jest`: – 1 e5` minus token następuje numer 1 i identyfikatora e5.  

##  <a name="bk_select_query"></a> Klauzula SELECT  
Muszą być uporządkowane klauzule w instrukcji SELECT, jak pokazano powyżej. Jeden opcjonalna klauzula można pominąć. Jednak gdy klauzule opcjonalne są używane, musi występować w odpowiedniej kolejności. Aby uzyskać przykłady, zobacz [przykładowe zapytanie SELECT](sql-api-sql-query.md#SelectClause)

**Składnia**  

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **Argumenty**  
  
- `<select_specification>`  

  Właściwości lub wartość należy wybrać zestaw wyników.  
  
- `'*'`  

  Określa, że wartość należy pobrać bez wprowadzania żadnych zmian. W szczególności, jeśli przetworzonych wartość jest obiektem, wszystkie właściwości będą pobierane.  
  
- `<object_property_list>`  
  
  Określa listę właściwości, które mają zostać pobrane. Każdy zwrócona wartość będzie obiektu przy użyciu właściwości określonych.  
  
- `VALUE`  

  Określa, że wartość JSON mają zostać pobrane zamiast kompletnego obiektu JSON. To, w przeciwieństwie do `<property_list>` nie jest zawijany przewidywane wartości w obiekcie.  
  
- `<scalar_expression>`  

  Wyrażenie reprezentujące wartość ma zostać obliczony. Zobacz [wyrażenia skalarne](#bk_scalar_expressions) sekcji, aby uzyskać szczegółowe informacje.  
  
**Uwagi**  
  
`SELECT *` Składnia jest prawidłowa, jeśli klauzula FROM zadeklarował dokładnie jeden alias. `SELECT *` udostępnia projekcji tożsamości, które mogą być przydatne, jeśli brak projekcji nie jest konieczne. Wybierz * jest prawidłowa, jeśli klauzula FROM określono tylko i wyłącznie jedno źródło danych wejściowych.  
  
Należy pamiętać, że `SELECT <select_list>` i `SELECT *` są "sugar składni" i można również wyrazić za pomocą prostych instrukcji "SELECT", jak pokazano poniżej.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   jest równoważne:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   jest równoważne:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**Zobacz też**  
  
[Wyrażenia skalarne](#bk_scalar_expressions)  
[Klauzula SELECT](#bk_select_query)  
  
##  <a name="bk_from_clause"></a> FROM — klauzula  
Określa źródło lub połączone źródła. Klauzula FROM jest opcjonalny, jeśli źródło jest filtrowana lub przewidywany później w zapytaniu. Ta klauzula ma na celu określania źródła danych, na którym zapytanie musi działać. Często jest źródłem w całej kolekcji, ale zamiast tego można określić podzbiór kolekcji. Jeśli ta klauzula nie jest określony, inne klauzule nadal będą wykonywane tak, jakby klauzuli FROM podane pojedynczego dokumentu. Aby uzyskać przykłady, zobacz [z przykładów — klauzula](sql-api-sql-query.md#FromClause)
  
**Składnia**  
  
```sql  
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
  
- `<from_source>`  
  
  Określa źródło danych, z lub bez aliasu. Jeśli nie określono aliasu, zostanie wywnioskowany z `<collection_expression>` za pomocą następujących reguł:  
  
  -  Jeśli wyrażenie ma nazwa_kolekcji, nazwa_kolekcji będzie używany jako alias.  
  
  -  Jeśli wyrażenie ma `<collection_expression>`, property_name, a następnie property_name zostanie użyta jako alias. Jeśli wyrażenie ma nazwa_kolekcji, nazwa_kolekcji będzie używany jako alias.  
  
- AS `input_alias`  
  
  Określa, że `input_alias` to zbiór wartości zwracanych przez wyrażenie kolekcji źródłowej.  
 
- `input_alias` INDIE  
  
  Określa, że `input_alias` powinny reprezentować zbiór wartości uzyskane przez Iterowanie wszystkie elementy tablicy tablic zwracanym przez wyrażenie kolekcji źródłowej. Dowolna wartość zwracanych przez wyrażenie kolekcji źródłowej, który nie jest tablicą jest ignorowany.  
  
- `<collection_expression>`  
  
  Określa wyrażenie kolekcji ma być używany do pobierania dokumentów.  
  
- `ROOT`  
  
  Określa, że w tym dokumencie mają zostać pobrane z domyślnej kolekcji aktualnie połączonych.  
  
- `collection_name`  
  
  Określa, że w tym dokumencie mają zostać pobrane z określonej kolekcji. Nazwa kolekcji musi odpowiadać Nazwa kolekcji połączone obecnie z usługą.  
  
- `input_alias`  
  
  Określa, że w tym dokumencie mają zostać pobrane ze źródła, zdefiniowane przez podany alias.  
  
- `<collection_expression> '.' property_`  
  
  Określa dokumentu powinny zostać pobrane, uzyskując dostęp do `property_name` właściwości lub indeks_tablicy elementu tablicy, dla wszystkich dokumentów, pobierane przez określone wyrażenie kolekcji.  
  
- `<collection_expression> '[' "property_name" | array_index ']'`  
  
  Określa dokumentu powinny zostać pobrane, uzyskując dostęp do `property_name` właściwości lub indeks_tablicy elementu tablicy, dla wszystkich dokumentów, pobierane przez określone wyrażenie kolekcji.  
  
**Uwagi**  
  
Wszystkie aliasy podany lub wnioskowany w `<from_source>(`s) musi być unikatowa. Składnia `<collection_expression>.`property_name jest taka sama jak `<collection_expression>' ['"property_name"']'`. Jednak te ostatnie składni można użyć, jeśli nazwa właściwości zawiera znaki inne niż identyfikator.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Obsługa brakujących elementów tablicy i wartości niezdefiniowanego nie ma właściwości
  
Wyrażenie kolekcji uzyskuje dostęp do właściwości lub elementów tablicy i wartości nie istnieje, ta wartość będą ignorowane i nie przetwarzane dalej.  
  
### <a name="collection-expression-context-scoping"></a>Wyznaczanie zakresu kontekście wyrażenia kolekcji  
  
Wyrażenie kolekcji mogą być zakresem kolekcji lub dokumentu o określonym zakresie:  
  
-   Wyrażenie jest kolekcja należące do zakresu, jeśli bazowe źródło wyrażenia kolekcji jest albo głównego lub `collection_name`. Takie wyrażenie reprezentuje zestaw dokumenty pobierane z kolekcji bezpośrednio, a nie jest zależna od przetwarzania innych wyrażeń w kolekcji.  
  
-   Wyrażenie jest dokument należące do zakresu, jeśli bazowe źródło wyrażeniu kolekcji jest `input_alias` wprowadzone wcześniej w zapytaniu. Takie wyrażenie reprezentuje zestaw dokumentów uzyskanych przez obliczenie wyrażenia kolekcji w zakresie każdego dokumentu należących do zestawu, skojarzone z tą kolekcją aliasem.  Wynikowy zestaw będzie Unii zestawów uzyskany w wyniku obliczenia wyrażenia kolekcji dla wszystkich dokumentów w podstawowym zestawie.  
  
### <a name="joins"></a>Sprzężenia 
  
W bieżącej wersji usługi Azure Cosmos DB obsługuje sprzężeń wewnętrznych. Zapowiadane są sprzężenia dodatkowe możliwości. 

Sprzężenia wewnętrzne spowodować pełny iloczyn wektorowy zestawy uczestniczących w sprzężenia. Wynikiem sprzężenia sposób N jest zestaw spójnych kolekcji N-elementowej, gdzie każda wartość w spójnej kolekcji jest skojarzony z aliasem, ustaw udział w sprzężeniu i jest dostępny, odwołując się do tego aliasu w innych klauzul. Aby uzyskać przykłady, zobacz [przykłady — słowo kluczowe sprzężenia](sql-api-sql-query.md#Joins)
  
Ocena sprzężenia zależy od zakresu kontekstu uczestniczących w programie zestawów:  
  
-  Sprzężenia między kolekcji zestawu A i należące do kolekcji zestawu B, wyniki w produkcie między wszystkie elementy w zestawach, A i B.
  
-   Sprzężenie zestaw A i zakresu w dokumencie zestawu B, wynikiem sumę wszystkich zestawów uzyskane poprzez ocenę zakresu w dokumencie zestawu B dla każdego dokumentu z zestawu A.  
  
 W bieżącej wersji więcej niż jedno wyrażenie należące do kolekcji jest obsługiwana przez procesor zapytań.  
  
### <a name="examples-of-joins"></a>Przykłady połączeń  
  
Spójrzmy na następujący klauzuli FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Pozwól każdego źródła, zdefiniuj `input_alias1, input_alias2, …, input_aliasN`. Ta klauzula FROM zwraca zestaw (krotki wartości N) N-krotek. Każda krotka zawiera wartości utworzone przez wszystkie aliasy kolekcji Iterowanie po ich odpowiednich zestawów.  
  
**Przykład 1** -2 źródeł  
  
- Pozwól `<from_source1>` kolekcji należące i reprezentują zestawu {A, B, C}.  
  
- Pozwól `<from_source2>` się dokument o zakresie odwołujące się do input_alias1 i reprezentują zestawów:  
  
    {1, 2} dla `input_alias1 = A,`  
  
    {3} Aby uzyskać `input_alias1 = B,`  
  
    {4, 5} dla `input_alias1 = C,`  
  
- Klauzula FROM `<from_source1> JOIN <from_source2>` spowoduje następujące kolekcje:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Przykład 2** -3 źródła  
  
- Pozwól `<from_source1>` kolekcji należące i reprezentują zestawu {A, B, C}.  
  
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
  
- Pozwól < from_source1 > być zakresem kolekcji i reprezentują zestawu {A, B, C}.  
  
- Pozwól `<from_source1>` kolekcji należące i reprezentują zestawu {A, B, C}.  
  
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
  
**Zobacz też**  
  
 [Klauzula SELECT](#bk_select_query)  
  
##  <a name="bk_where_clause"></a> Klauzula WHERE  
 Określa warunek wyszukiwania określający dokumenty zwrócone przez zapytanie. Aby uzyskać przykłady, zobacz [przykłady klauzuli WHERE](sql-api-sql-query.md#WhereClause)
  
 **Składnia**  
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **Argumenty**  
  
-   `<filter_condition>`  
  
     Określa warunek do spełnienia dokumentów, które mają zostać zwrócone.  
  
-   `<scalar_expression>`  
  
     Wyrażenie reprezentujące wartość ma zostać obliczony. Zobacz [wyrażenia skalarne](#bk_scalar_expressions) sekcji, aby uzyskać szczegółowe informacje.  
  
 **Uwagi**  
  
 W kolejności dokumentu, które mają zostać zwrócone wyrażenie określone jako filtr warunek musi zwrócić wartość true. Tylko wartość logiczną PRAWDA będzie spełniać warunek jakakolwiek inna wartość: Niezdefiniowany, null, wartość false, liczby, tablicy lub obiektu nie spełnia warunku.  
  
##  <a name="bk_orderby_clause"></a> Klauzula ORDER BY  
 Określa kolejność sortowania wyników zwróconych przez zapytanie. Aby uzyskać przykłady, zobacz [ORDER BY przykłady klauzuli](sql-api-sql-query.md#OrderByClause)
  
 **Składnia**  
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **Argumenty**  
  
-   `<sort_specification>`  
  
     Określa właściwość lub wyrażenie do sortowania zestawu wyników zapytania. Kolumna sortowania może być określona jako alias nazwy lub kolumny.  
  
     Można określić wiele kolumn sortowania. Nazwy kolumn muszą być unikatowe. Kolejność sortowania kolumn w klauzuli ORDER BY określa organizacji zestawu posortowanego wyników. Oznacza to zestaw wyników jest posortowana według pierwszą właściwością, a następnie tego uporządkowana lista jest posortowana według właściwości drugiej i tak dalej.  
  
     Nazwy kolumn w klauzuli ORDER BY muszą odpowiadać kolumnę na liście wyboru lub kolumny zdefiniowane w tabeli określony w klauzuli FROM bez żadnych niejednoznaczności.  
  
-   `<sort_expression>`  
  
     Określa jedną właściwość lub wyrażenie do sortowania zestawu wyników zapytania.  
  
-   `<scalar_expression>`  
  
     Zobacz [wyrażenia skalarne](#bk_scalar_expressions) sekcji, aby uzyskać szczegółowe informacje.  
  
-   `ASC | DESC`  
  
     Określa, że wartości w określonej kolumnie powinny być sortowane w kolejności rosnącej lub malejącej. ASC sortuje od najniższej do najwyższej wartości. DESC sortuje od najwyższej do najniższej wartości. ASC jest domyślny porządek sortowania. Wartości null są traktowane jako najniższe możliwe wartości.  
  
 **Uwagi**  
  
 Natomiast gramatyki zapytań obsługuje wiele kolejność według właściwości, w czasie wykonywania zapytań usługi Azure Cosmos DB obsługuje sortowanie wyłącznie w odniesieniu do jednej właściwości i wyłącznie w odniesieniu do nazw właściwości, czyli nie względem obliczone właściwości. Sortowanie wymaga również, że zasady indeksowania zawiera indeks zakresu dla właściwości i określonego typu, z maksymalną dokładnością. Zajrzyj do dokumentacji zasad indeksowania, aby uzyskać więcej informacji.  
  
##  <a name="bk_scalar_expressions"></a> Wyrażenia skalarne  
 Wyrażenie skalarne, które jest kombinacją symboli i operatorów, które mogą być obliczane w celu uzyskania pojedynczej wartości. Proste wyrażenia może być stałe, odwołania do właściwości, odwołania do elementu tablicy, odwołania do aliasu lub wywołania funkcji. Proste wyrażenia można łączyć w złożonych wyrażeń przy użyciu operatorów. Aby uzyskać przykłady, zobacz [przykłady wyrażenia skalarne](sql-api-sql-query.md#scalar-expressions)
  
 Szczegółowe informacje na temat wartości, które wyrażenie skalarne, które mogą mieć, [stałe](#bk_constants) sekcji.  
  
 **Składnia**  
  
```sql  
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
  
     Reprezentuje wartość zdefiniowana przez `input_alias` wprowadzona w `FROM` klauzuli.  
    Ta wartość może nie być **niezdefiniowane** —**niezdefiniowane** wartości w danych wejściowych są pomijane.  
  
-   `<scalar_expression>.property_name`  
  
     Reprezentuje wartość właściwości obiektu. Jeśli właściwość nie istnieje lub odwołuje się do właściwości na wartość, która nie jest obiektem, a następnie wyrażenie ma **niezdefiniowane** wartość.  
  
-   `<scalar_expression>'['"property_name"|array_index']'`  
  
     Reprezentuje wartość właściwości o nazwie `property_name` lub elementu tablicy za pomocą indeksu `array_index` obiektu/tablicy. Jeśli indeks tablicy właściwości/nie istnieje lub indeks tablicy właściwości/odwołuje się do wartości, która nie jest/Tablica obiektów, wyrażenie przyjmuje wartość niezdefiniowana.  
  
-   `unary_operator <scalar_expression>`  
  
     Reprezentuje operatora, który jest stosowany do pojedynczej wartości. Zobacz [operatory](#bk_operators) sekcji, aby uzyskać szczegółowe informacje.  
  
-   `<scalar_expression> binary_operator <scalar_expression>`  
  
     Reprezentuje operatora, który jest stosowany do dwóch wartości. Zobacz [operatory](#bk_operators) sekcji, aby uzyskać szczegółowe informacje.  
  
-   `<scalar_function_expression>`  
  
     Reprezentuje wartość zdefiniowana przez wynik wywołania funkcji.  
  
-   `udf_scalar_function`  
  
     Nazwa użytkownika zdefiniowana funkcja skalarna.  
  
-   `builtin_scalar_function`  
  
     Nazwa wbudowanej funkcji skalarnej.  
  
-   `<create_object_expression>`  
  
     Reprezentuje wartość uzyskaną przez utworzenie nowego obiektu przy użyciu określonych właściwości i ich wartości.  
  
-   `<create_array_expression>`  
  
     Reprezentuje wartość uzyskać, tworząc nową tablicę z określonymi wartościami jako elementy  
  
-   `parameter_name`  
  
     Reprezentuje wartość określona nazwa parametru. Nazwy parametrów muszą mieć pojedynczy \@ jako pierwszego znaku.  
  
 **Uwagi**  
  
 Podczas wywoływania wbudowanych lub użytkownika definiowania funkcji skalarnej musi być zdefiniowany w argumentach. Jeśli którykolwiek z argumentów jest niezdefiniowana, nie zostanie wywołana funkcja, a wynik jest niezdefiniowany.  
  
 Podczas tworzenia obiektu, dowolnej właściwości, która jest przypisana wartość niezdefiniowana zostanie pominięty i nie są objęte utworzony obiekt.  
  
 Podczas tworzenia tablicy, każda wartość elementu, który jest przypisany **niezdefiniowane** wartość zostanie pominięty i nie są objęte utworzony obiekt. To spowoduje, że następny element zdefiniowany do jego miejsce w taki sposób, że utworzona tablica nie zostaną pominięte indeksów.  
  
##  <a name="bk_operators"></a> Operatory  
 W tej sekcji opisano obsługiwane operatory. Każdy operator można przypisać do dokładnie jednej kategorii.  
  
 Zobacz **kategorie Operator** poniższej tabeli, aby uzyskać szczegółowe informacje dotyczące obsługi **niezdefiniowane** wartości, wymagania dotyczące typu dla wartości wejściowych i obsługa wartościami niezgodne typy.  
  
 **Operator kategorie:**  
  
|**Kategoria**|**Szczegóły**|  
|-|-|  
|**Operacje arytmetyczne**|Operator oczekuje input(s) się numery. Dane wyjściowe jest również liczbą. Jeśli jakiekolwiek dane wejściowe są **niezdefiniowane** lub jest typu innego niż liczba następnie wynik **niezdefiniowane**.|  
|**bitowe**|Operator oczekuje input(s) jako 32-bitowej liczby całkowitej ze znakiem numery. Dane wyjściowe są również 32-bitowa liczba całkowita.<br /><br /> Dowolna wartość nie jest liczbą całkowitą, zostanie zaokrąglony. Dodatnia wartość zostanie zaokrąglona w dół, ujemne wartości zaokrąglane w górę.<br /><br /> Każdą wartość, która znajduje się poza zakresem 32-bitową liczbę całkowitą zostaną przekonwertowane, wykonując ostatnich 32 bity jego dwójki notacji uzupełnienia.<br /><br /> Jeśli jakiekolwiek dane wejściowe są **niezdefiniowane** lub innych typów niż numer, a następnie w wyniku **niezdefiniowane**.<br /><br /> **Uwaga:** powyżej zachowanie jest zgodne z zachowaniem bitowy operator w JavaScript.|  
|**logical**|Operator oczekuje input(s) jako Boolean(s). Dane wyjściowe również jest wartością logiczną.<br />Jeśli jakiekolwiek dane wejściowe są **niezdefiniowane** lub innych typów niż wartość logiczną, a następnie wynik będzie **niezdefiniowane**.|  
|**Porównanie**|Operator oczekuje input(s) mają tego samego typu i nie jest niezdefiniowana. Dane wyjściowe jest wartością logiczną.<br /><br /> Jeśli jakiekolwiek dane wejściowe są **niezdefiniowane** lub danych wejściowych mają różne typy, a następnie w wyniku **niezdefiniowane**.<br /><br /> Zobacz **porządkowanie wartości do porównania** tabeli wartości kolejności szczegółowe informacje.|  
|**ciąg**|Operator oczekuje input(s) jako ciągi. Dane wyjściowe jest ciąg.<br />Jeśli jakiekolwiek dane wejściowe są **niezdefiniowane** lub typem innym niż ciąg, a następnie wynik jest **niezdefiniowane**.|  
  
 **Operatory jednoargumentowe:**  
  
|**Nazwa**|**Operator**|**Szczegóły**|  
|-|-|-|  
|**Operacje arytmetyczne**|+<br /><br /> -|Zwraca wartość liczbową.<br /><br /> Negacja bitowa. Zwraca wartość ujemna wartość liczbową.|  
|**bitowe**|~|Uzupełnienie tych. Zwraca uzupełnienie wartość liczbową.|  
|**Logiczne**|**NIE**|Negacja. Zwraca wartość ujemna wartość logiczną.|  
  
 **Operatory binarne:**  
  
|**Nazwa**|**Operator**|**Szczegóły**|  
|-|-|-|  
|**Operacje arytmetyczne**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|Dodatek.<br /><br /> Odejmowanie.<br /><br /> Mnożenia.<br /><br /> Dzielenie.<br /><br /> Modulacja.|  
|**bitowe**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|Bitowe OR.<br /><br /> Operatora bitowego AND.<br /><br /> Bitowe XOR.<br /><br /> Przesunięcie w lewo.<br /><br /> Przesunięcia w prawo.<br /><br /> Wypełnienie zero przesunięcia bitowego w prawo.|  
|**logical**|**I**<br /><br /> **OR**|Połączenie logiczne. Zwraca **true** Jeśli oba argumenty **true**, zwraca **false** inaczej.<br /><br /> Połączenie logiczne. Zwraca **true** Jeśli oba argumenty **true**, zwraca **false** inaczej.|  
|**Porównanie**|**=**<br /><br /> **!=, <>**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|Equals. Zwraca **true** argumenty są równe, funkcja zwraca **false** inaczej.<br /><br /> Nie ma wartości. Zwraca **true** argumenty nie są równe, zwraca **false** inaczej.<br /><br /> Większe niż. Zwraca **true** Jeśli pierwszy argument jest większy niż drugi, zwracają **false** inaczej.<br /><br /> Większa niż lub równe. Zwraca **true** Jeśli pierwszy argument jest większy lub równy do drugiej, zwracają **false** inaczej.<br /><br /> Mniejsze niż. Zwraca **true** Jeśli pierwszy argument jest mniejszy niż drugi jeden zwracany **false** inaczej.<br /><br /> Mniejsze niż lub równe. Zwraca **true** Jeśli pierwszy argument jest mniejszy niż drugi, zwracają **false** inaczej.<br /><br /> COALESCE. Zwraca wartość drugiego argumentu, jeśli pierwszy argument jest **niezdefiniowane** wartość.|  
|**Ciąg**|**&#124;&#124;**|Łączenie. Zwraca połączenie obu argumentów.|  
  
 **Operatory trzyargumentowe:**  
  
|Operator trójargumentowy|?|Zwraca wartość drugiego argumentu, jeśli pierwszy argument daje w wyniku **true**; w przeciwnym razie zwraca wartość trzeciego argumentu.|  
|-|-|-|  
  
 **Kolejność wartości do porównania**  
  
|**Typ**|**Kolejność wartości**|  
|-|-|  
|**Niezdefiniowane**|Nie jest porównywalny.|  
|**Wartość null**|Pojedyncza wartość: **o wartości null**|  
|**Numer**|Liczba rzeczywista fizycznych.<br /><br /> Wartość ujemna nieskończoność jest mniejszy niż inne wartość liczbową.<br /><br /> Wartości nieskończoności dodatniej jest większy niż inne wartość liczbową. **NaN** wartość nie jest porównywalny. Porównanie z **NaN** spowoduje **niezdefiniowane** wartość.|  
|**Ciąg**|Kolejności lexicographical.|  
|**Tablica**|Kolejność nie, ale sprawiedliwe.|  
|**obiekt**|Kolejność nie, ale sprawiedliwe.|  
  
 **Uwagi**  
  
 W usłudze Azure Cosmos DB typy wartości, często nie są znane, dopóki nie są faktycznie są pobierane z bazy danych. Aby można było obsługiwać zapewnienia ich wydajnego wykonywania zapytań, większość operatorów mają wymagania dotyczące typu strict. Również operatory samodzielnie, nie należy wykonywać konwersje niejawne.  
  
 Oznacza to, że zapytania, takich jak: Wybierz * z katalogu głównego r r.Age gdzie = 21 zwróci tylko dokumentów za pomocą właściwości wiek równą liczbie 21. Dokumenty z właściwością wiek równa "21" lub ciągu "0021", nie będą zgodne jako wyrażenie "21" = 21 ocenia do niezdefiniowanego. Umożliwia to lepsze wykorzystanie indeksów, ponieważ wyszukiwania określonej wartości (czyli numer 21) jest szybsze niż wyszukiwanie nieokreśloną liczbę potencjalnych dopasowania (tj. liczba 21 lub ciągi "21", "021", "21.0"...). To różni się od sposobu JavaScript ocenia operatory na wartości różnych typów.  
  
 **Tablice i obiekty równości i porównania**  
  
 Porównanie wartości tablicy lub obiektu przy użyciu operatorów zakresu (>, > =, <, < =) spowoduje niezdefiniowane, ponieważ nie ma zdefiniowaną dla obiektu lub tablicy wartości nie kolejność. Jednak przy użyciu operatorów równości i nierówności (=,! = <>) jest obsługiwana i będzie można porównać wartości strukturalnie.  
  
 Tablice są równe, jeśli obie tablice mają tę samą liczbę elementów, a elementy w pasujących pozycji również są takie same. Jeśli porównanie jakaś para elementów powoduje niezdefiniowane wynik porównania tablicy jest niezdefiniowane.  
  
 Obiekty są równe, jeśli oba obiekty mają te same właściwości zdefiniowane i wartości właściwości dopasowywania również są równe. Jeśli porównanie jakaś para wartości właściwości powoduje niezdefiniowane wynik porównania obiektu jest niezdefiniowane.  
  
##  <a name="bk_constants"></a> Stałe  
 Stała, znana także jako literał lub wartość skalarną, jest symbol, który reprezentuje wartość określonych danych. Format stałej zależy od typu danych wartość, którą reprezentuje.  
  
 **Obsługiwane skalarnych typów danych:**  
  
|**Typ**|**Kolejność wartości**|  
|-|-|  
|**Niezdefiniowane**|Pojedyncza wartość: **niezdefiniowane**|  
|**Wartość null**|Pojedyncza wartość: **o wartości null**|  
|**Boolean**|Wartości: **false**, **true**.|  
|**Numer**|Podwójnej precyzji liczba zmiennoprzecinkowa, IEEE 754 standardowych.|  
|**Ciąg**|Sekwencja zero lub więcej znaków Unicode. Parametry muszą być ujęte w pojedyncze lub podwójne cudzysłowy.|  
|**Tablica**|Sekwencja zero lub więcej elementów. Każdy element może być wartością dowolnego typu danych skalarnych, z wyjątkiem niezdefiniowane.|  
|**obiekt**|Nieuporządkowana zestaw par nazwa/wartość zero lub więcej. Nazwa jest ciągiem Unicode, wartość może być dowolnego typu danych skalarnych, z wyjątkiem **niezdefiniowane**.|  
  
 **Składnia**  
  
```sql  
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
  
* `<undefined_constant>; undefined`  
  
  Wartość reprezentuje Niezdefiniowany typ niezdefiniowane.  
  
* `<null_constant>; null`  
  
  Reprezentuje **null** wartości typu **Null**.  
  
* `<boolean_constant>`  
  
  Reprezentuje stałą typu Boolean.  
  
* `false`  
  
  Reprezentuje **false** wartość typu Boolean.  
  
* `true`  
  
  Reprezentuje **true** wartość typu Boolean.  
  
* `<number_constant>`  
  
  Reprezentuje stałą.  
  
* `decimal_literal`  
  
  Literały dziesiętna są cyfry, reprezentowane za pomocą notacji dziesiętnej lub notacji wykładniczej.  
  
* `hexadecimal_literal`  
  
  Literały szesnastkowe są cyfry, reprezentowane za pomocą prefiks "0 x" następuje co najmniej jedna cyfra szesnastkowa.  
  
* `<string_constant>`  
  
  Reprezentuje stałą typu String.  
  
* `string _literal`  
  
  Literały ciągów są reprezentowane przez Sekwencja zero lub więcej znaków Unicode lub sekwencji unikowych ciągów znaków Unicode. Literały ciągu są ujmowane w cudzysłów pojedynczy (apostrof: ") lub podwójny cudzysłów (cudzysłów:").  
  
 Poniższe sekwencje ucieczki są dozwolone:  
  
|**Sekwencja unikowa**|**Opis**|**Znak Unicode**|  
|-|-|-|  
|\\'|apostrof (')|U+0027|  
|\\"|znak cudzysłowu (")|U+0022|  
|\\\|kreska ułamkowa odwrotnej (\\)|U+005C|  
|\\/|kreska ułamkowa (/)|U+002F|  
|\b|BACKSPACE|U+0008|  
|\f|Wysuw strony|U+000C|  
|\n|Wysuw wiersza|U+000A|  
|\r|Powrót karetki|U+000D|  
|\t|tabulator|U+0009|  
|\uXXXX|Znak Unicode, zdefiniowane przez 4 cyfr szesnastkowych.|U+XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a> Wytyczne dotyczące wydajności zapytań  
 Aby zapytanie w celu wykonania wydajne dla dużych kolekcji powinna korzystać filtry, które mogą być udostępniane za pośrednictwem jednego lub kilku indeksów.  
  
 Poniższe filtry zostaną uwzględnione podczas wyszukiwania indeksu:  
  
-   Za pomocą operatora równości (=) wyrażenie ścieżki dokumentu i stałą.  
  
-   Operatory zakresu (<, \<=, >, > =) przy użyciu wyrażenie ścieżki dokumentu a number — stałe.  
  
-   Wyrażenie ścieżki dokumentu oznacza dowolne Wyrażenie identyfikujące stałe ścieżki w dokumentach z kolekcji bazy danych.  
  
 **Wyrażenie ścieżki dokumentu**  
  
 Wyrażenia ścieżka dokumentu są wyrażeniami, ścieżkę właściwości lub tablicy oceniających indeksatora za pośrednictwem dokumentu pochodzące z bazy danych kolekcji dokumentów. Ta ścieżka może służyć do identyfikowania lokalizacji wartości filtru bezpośrednio z poziomu dokumenty w kolekcji bazy danych.  
  
 Wyrażenie wziąć pod uwagę wyrażenie ścieżki dokumentu należy:  
  
1.  Odwołanie do kolekcji głównym bezpośrednio.  
  
2.  Odwołanie do właściwości lub stała indeksatora tablicy niektóre wyrażenia ścieżka dokumentu  
  
3.  Odwoływać się do aliasu, który reprezentuje niektóre wyrażenie ścieżki dokumentu.  
  
     **Konwencje składni**  
  
     W poniższej tabeli opisano konwencje używany do opisu składni w następujące odwołanie SQL.  
  
    |**Konwencja**|**Używane dla**|  
    |-|-|    
    |WIELKIE LITERY|Słowa kluczowe bez uwzględniania wielkości liter.|  
    |Małe litery|Słowa kluczowe uwzględniana wielkość liter.|  
    |\<nieterminalnych >|Nieterminalnych, określane oddzielnie.|  
    |\<nieterminalnych >:: =|Definicja składni nieterminalnych.|  
    |other_terminal|Terminalu (token) opisano szczegółowo w słów.|  
    |Identyfikator|Identyfikator. Zezwala na następujące znaki: a-z A-Z 0-9 _First znaków nie może być cyfrę.|  
    |"string"|Ciąg w cudzysłowach. Umożliwia dowolny prawidłowy ciąg. Zobacz opis string_literal.|  
    |'symbol'|Literał symbol, który jest częścią składni.|  
    |&#124;(kreska pionowa)|Alternatywy dla elementy składni. Można użyć tylko jeden z określonych elementów.|  
    |/(brackets)]|Nawiasy kwadratowe powinno być co najmniej jeden element opcjonalny.|  
    |[,.. .n]|Wskazuje, że poprzedni element może być powtarzane n liczbę razy. Wystąpienia są oddzielone przecinkami.|  
    |[.. .n]|Wskazuje, że poprzedni element może być powtarzane n liczbę razy. Wystąpienia są rozdzielane odstępami.|  
  
##  <a name="bk_built_in_functions"></a> Funkcje wbudowane  
 Usługa Azure Cosmos DB udostępnia wiele wbudowanych funkcji SQL. Poniżej przedstawiono kategorie funkcji wbudowanych.  
  
|Funkcja|Opis|  
|--------------|-----------------|  
|[Funkcje matematyczne](#bk_mathematical_functions)|Funkcje matematyczne wykonywanie obliczeń, zazwyczaj na podstawie wartości wejściowych, które są przekazywane jako argumenty i zwraca wartość liczbową.|  
|[Typ funkcji sprawdzania](#bk_type_checking_functions)|Funkcje sprawdzania typu pozwalają sprawdzić typ wyrażenia w ramach zapytania SQL.|  
|[Funkcje ciągów](#bk_string_functions)|Funkcje ciągów wykonania operacji na wartość ciągu wejściowego i zwraca ciąg, wartość liczbowa lub Boolean.|  
|[Funkcje tablicy](#bk_array_functions)|Funkcje tablicy wykonywania operacji na tablicy wartości wejściowej i zwrócenie liczbowych, wartość logiczną lub tablicy.|  
|[Funkcje przestrzenne](#bk_spatial_functions)|Funkcje przestrzenne wykonania operacji na wartości wejściowej obiektu przestrzennego i zwracają wartość liczbowa lub Boolean.|  
  
###  <a name="bk_mathematical_functions"></a> Funkcje matematyczne  
 Następujące funkcje każdego wykonywanie obliczeń, zazwyczaj na podstawie wartości wejściowych, które są przekazywane jako argumenty i zwraca wartość liczbową.  
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[LIMIT](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[STOPNI](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[DZIENNIK](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[ZASILANIA](#bk_power)|  
|[RADIANS](#bk_radians)|[ROUND](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[KWADRAT](#bk_square)|[LOGOWANIE](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC —](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 Zwraca wartość bezwzględną (pozytywna) wartość podanego wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład pokazuje wyniki za pomocą funkcji ABS na trzech różnych liczb.  
  
```  
SELECT ABS(-1), ABS(0), ABS(1)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 1, $2: 0, $3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Zwraca kąt w radianach, którego cosinus jest określone wyrażenie liczbowe; Skrót cosinus.  
  
 **Składnia**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
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
 Zwraca kąt w radianach, którego sinus jest określone wyrażenie liczbowe. Jest to również nazywane arcus sinus.  
  
 **Składnia**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
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
 Zwraca kąt w radianach, którego tangens jest określone wyrażenie liczbowe. Jest to również nazywane tangens.  
  
 **Składnia**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca ATAN od określonej wartości.  
  
```  
SELECT ATAN(-45.01)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Zwraca wartość główną wartości arcus tangens y / x, wyrażony w radianach.  
  
 **Składnia**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 W poniższym przykładzie obliczany ATN2 dla określonego x i y składników.  
  
```  
SELECT ATN2(35.175643, 129.44)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> LIMIT  
 Zwraca najmniejszą wartość liczby całkowitej większa lub równa określonej wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład pokazuje, liczbowych dodatnia, ujemna i wartości zerowych za pomocą funkcji CEILING.  
  
```  
SELECT CEILING(123.45), CEILING(-123.45), CEILING(0.0)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 124, $2: -123, $3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 Zwraca trygonometrycznych cosinus kąta określonego w radianach, określone wyrażenie.  
  
 **Składnia**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 W poniższym przykładzie obliczany COS podanemu kątowi.  
  
```  
SELECT COS(14.78)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 Zwraca trygonometrycznych cotangens kąta określonego w radianach, z określonego wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 W poniższym przykładzie obliczany COT podanemu kątowi.  
  
```  
SELECT COT(124.1332)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> STOPNI  
 Zwraca odpowiedni kąt w stopniach na potrzeby kąta określonego w radianach.  
  
 **Składnia**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca liczbę stopni w pod kątem PI/2 radianów.  
  
```  
SELECT DEGREES(PI()/2)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 Zwraca największą liczbę całkowitą, mniejsze niż lub równe określonego wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład pokazuje, liczbowych dodatnia, ujemna i wartości zerowe za pomocą funkcji FLOOR.  
  
```  
SELECT FLOOR(123.45), FLOOR(-123.45), FLOOR(0.0)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 123, $2: -124, $3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Zwraca wartość określonego wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Uwagi**  
  
 Stała **e** (2.718281...), jest podstawą logarytmy naturalne.  
  
 Wykładnik potęgi liczby jest stałą **e** podniesioną do potęgi liczby. Na przykład EXP(1.0) = e ^ 1.0 = 2.71828182845905 i EXP(10) = e ^ 10 = 22026.4657948067.  
  
 Jest to liczba wykładniczą logarytm naturalny liczby sam: EXP (dziennik (n)) = n. A logarytm naturalny wykładniczą liczby jest liczbą sam: LOG (EXP (n)) = n.  
  
 **Przykłady**  
  
 Poniższy przykład deklaruje zmienną i zwraca wartość określonej zmiennej (10).  
  
```  
SELECT EXP(10)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 22026.465794806718}]  
```  
  
 Poniższy przykład zwraca wartość natural logarithm 20 i wykładniczą 20 logarytm naturalny. Ponieważ te funkcje są funkcje odwrotne od siebie, wartość zwracana z zaokrąglaniem do zmiennoprzecinkowych matematyczne punktu w obu przypadkach wynosi 20.  
  
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
  
     Opcjonalny argument liczbowy, który ustawia podstawa logarytmu.  
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Uwagi**  
  
 Domyślnie LOG() Zwraca logarytm naturalny. Podstawa logarytmu można zmienić z inną wartością za pomocą opcjonalnego parametru podstawowej.  
  
 Logarytm naturalny to logarytm o podstawie **e**, gdzie **e** jest stałą nieracjonalnej w przybliżeniu równa 2.718281828.  
  
 Logarytm naturalny wykładniczą liczby jest to liczba sam: LOG (EXP (n)) = n. I wykładniczą logarytm naturalny liczby jest to liczba sam: EXP (dziennik (n)) = n.  
  
 **Przykłady**  
  
 Poniższy przykład deklaruje zmienną i zwraca wartość logarytmu określonej zmiennej (10).  
  
```  
SELECT LOG(10)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 2.3025850929940459}]  
```  
  
 W poniższym przykładzie obliczany dziennika wykładnika potęgi liczby.  
  
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
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Uwagi**  
  
 LOG10 i możliwości funkcji odwrotnie są powiązane ze sobą. Na przykład 10 ^ LOG10(n) = n.  
  
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
 Zwraca wartość stała PI.  
  
 **Składnia**  
  
```  
PI ()  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
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
 Zwraca wartość z określonego wyrażenia do określonej potęgi.  
  
 **Składnia**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
-   `y`  
  
     Jest to prawo do którego zostanie wywołane `numeric_expression`.  
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 W poniższym przykładzie pokazano, zwiększenie liczby do potęgi 3 (moduł liczba).  
  
```  
SELECT POWER(2, 3), POWER(2.5, 3)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 8, $2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> WARTOŚĆ W RADIANACH  
 Zwraca wartość w radianach, po wprowadzeniu wyrażenia liczbowego w stopniach.  
  
 **Składnia**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład przyjmuje kilka kąty jako dane wejściowe i zwraca ich odpowiednie wartości w radianach.  
  
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
 Zwraca wartość liczbową zaokrąglone do najbliższej wartości całkowitej.  
  
 **Składnia**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
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
  
####  <a name="bk_sign"></a> LOGOWANIE  
 Zwraca wynik dodatni (+ 1), wartość zero (0) lub minus (-1) z określonego wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca wartości znak cyfry od -2 z 2.  
  
```  
SELECT SIGN(-2), SIGN(-1), SIGN(0), SIGN(1), SIGN(2)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: -1, $2: -1, $3: 0, $4: 1, $5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 Zwraca trygonometrycznych sinus określonego kąta w radianach, określone wyrażenie.  
  
 **Składnia**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
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
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca pierwiastki kwadratowe liczb od 1 do 3.  
  
```  
SELECT SQRT(1), SQRT(2.0), SQRT(3)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 1, $2: 1.4142135623730952, $3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> KWADRAT  
 Zwraca kwadrat określoną wartość liczbową.  
  
 **Składnia**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
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
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 W poniższym przykładzie oblicza tangens liczby PI () / 2.  
  
```  
SELECT TAN(PI()/2);  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC —  
 Zwraca wartość liczbową obcięty do najbliższej wartości całkowitej.  
  
 **Składnia**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     To wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład obcina następujących numerów dodatnie i ujemne do najbliższej wartości całkowitej.  
  
```  
SELECT TRUNC(2.4), TRUNC(2.6), TRUNC(2.5), TRUNC(-2.4), TRUNC(-2.6)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{$1: 2, $2: 2, $3: 2, $4: -2, $5: -2}]  
```  
  
###  <a name="bk_type_checking_functions"></a> Typ funkcji sprawdzania  
 Następujące funkcje obsługuje typ sprawdzania względem wartości wejściowych, a każda zwraca wartość logiczną.  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 Zwraca wartość logiczną wskazującą, czy Tablica typu z określonego wyrażenia.  
  
 **Składnia**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Jest dowolnym prawidłowym wyrażeniem.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza obiektów JSON logiczna, liczba, ciąg, wartość null, obiekt, tablica i niezdefiniowane typów, przy użyciu funkcji IS_ARRAY.  
  
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
 Zwraca wartość logiczną wskazującą, jeśli typ określonego wyrażenie jest wartością logiczną.  
  
 **Składnia**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Jest dowolnym prawidłowym wyrażeniem.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza obiektów JSON logiczna, liczba, ciąg, wartość null, obiekt, tablica i niezdefiniowane typów, przy użyciu funkcji IS_BOOL.  
  
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
 Zwraca wartość Boolean wskazującą, jeśli właściwość zostanie przypisana wartość.  
  
 **Składnia**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Jest dowolnym prawidłowym wyrażeniem.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza obecność właściwość w ramach określonego dokumentu JSON. Pierwsza zwraca wartość true, ponieważ "a" jest obecny, ale druga zwraca wartość false, ponieważ nie istnieje "b".  
  
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
 Zwraca wartość logiczną wskazującą, jeśli typ określonego wyrażenie ma wartość null.  
  
 **Składnia**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Jest dowolnym prawidłowym wyrażeniem.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza obiektów JSON logiczna, liczba, ciąg, wartość null, obiekt, tablica i niezdefiniowane typów, przy użyciu funkcji IS_NULL.  
  
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
 Zwraca wartość logiczną wskazującą, jeżeli typ podanego wyrażenia jest liczbą.  
  
 **Składnia**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Jest dowolnym prawidłowym wyrażeniem.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza obiektów JSON logiczna, liczba, ciąg, wartość null, obiekt, tablica i niezdefiniowane typów, przy użyciu funkcji IS_NULL.  
  
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
 Zwraca wartość logiczną wskazującą, czy obiekt JSON typu z określonego wyrażenia.  
  
 **Składnia**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Jest dowolnym prawidłowym wyrażeniem.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza obiektów JSON logiczna, liczba, ciąg, wartość null, obiekt, tablica i niezdefiniowane typów, przy użyciu funkcji is_object —.  
  
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
 Zwraca wartość logiczną wskazującą, czy podstawowy typu z określonego wyrażenia (string, Boolean, liczbowych lub ma wartość null).  
  
 **Składnia**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Jest dowolnym prawidłowym wyrażeniem.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza obiektów JSON logiczna, liczba, ciąg, wartość null, obiekt, tablica i niezdefiniowane typów, przy użyciu funkcji IS_PRIMITIVE.  
  
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
 Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia ciągu.  
  
 **Składnia**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Jest dowolnym prawidłowym wyrażeniem.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza obiektów JSON logiczna, liczba, ciąg, wartość null, obiekt, tablica i niezdefiniowane typów, przy użyciu funkcji IS_STRING.  
  
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
 Następujące funkcje skalarne wykonania operacji na wartość ciągu wejściowego i zwraca ciąg, wartość liczbowa lub Boolean.  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[ZAWIERA](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[DŁUGOŚĆ](#bk_length)|  
|[NIŻSZY](#bk_lower)|[PRZYTP](#bk_ltrim)|[ZASTĄP](#bk_replace)|  
|[REPLIKUJ](#bk_replicate)|[REVERSE](#bk_reverse)|[PO PRAWEJ STRONIE](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[PODCIĄG](#bk_substring)|  
|[ToString](#bk_tostring)|[TRIM](#bk_trim)|[GÓRNY](#bk_upper)||| 
  
####  <a name="bk_concat"></a> CONCAT  
 Zwraca ciąg, który jest wynikiem złączenie co najmniej dwóch wartości ciągu.  
  
 **Składnia**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
 **Typy zwracane**  
  
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
 Zwraca wartość logiczną wskazującą, czy pierwszy ciąg wyrażenie zawiera drugą.  
  
 **Składnia**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza, czy "abc" zawiera "ab" i zawiera "d".  
  
```  
SELECT CONTAINS("abc", "ab"), CONTAINS("abc", "d")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": true, "$2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Zwraca wartość logiczną wskazującą, czy pierwszy ciąg wyrażenia kończy się na drugi.  
  
 **Składnia**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca "abc" kończy się ciągiem "b" i "bc".  
  
```  
SELECT ENDSWITH("abc", "b"), ENDSWITH("abc", "bc")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 Zwraca pozycję początkową pierwsze wystąpienie ciągu drugiego ciągu wyrażenia w ramach pierwszego określonego wyrażenia ciągu lub wartość -1, jeśli nie zostanie znaleziony ciąg.  
  
 **Składnia**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
 **Typy zwracane**  
  
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
 Zwraca część po lewej stronie ciąg zawierający określoną liczbę znaków.  
  
 **Składnia**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
-   `num_expr`  
  
     Jest dowolne prawidłowe wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
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
  
     Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
 **Typy zwracane**  
  
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
  
####  <a name="bk_lower"></a> NIŻSZY  
 Zwraca wyrażenie ciągu po przekonwertowaniu danych wielkiej litery na małe litery.  
  
 **Składnia**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład pokazuje, jak używać NIŻSZYCH w zapytaniu.  
  
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
  
     Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład pokazuje, jak używać LTRIM w zapytaniu.  
  
```  
SELECT LTRIM("  abc"), LTRIM("abc"), LTRIM("abc   ")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "abc", "$2": "abc", "$3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> ZASTĄP  
 Zamienia wszystkie wystąpienia określonej wartości ciągu na inną wartość ciągu.  
  
 **Składnia**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład przedstawia sposób użycia ZAMIEŃ w zapytaniu.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLIKACJA  
 Wartość ciągu jest powtarzany określoną liczbę razy.  
  
 **Składnia**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
-   `num_expr`  
  
     Jest dowolne prawidłowe wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład przedstawia sposób użycia replikacji w zapytaniu.  
  
```  
SELECT REPLICATE("a", 3)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 Zwraca wartość ciągu w kolejności odwrotnej.  
  
 **Składnia**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład przedstawia sposób użycia w zapytaniu w odwrotnej kolejności.  
  
```  
SELECT REVERSE("Abc")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "cbA"}]  
```  
  
####  <a name="bk_right"></a> PO PRAWEJ STRONIE  
 Zwraca ciąg zawierający określoną liczbę znaków jego prawej części.  
  
 **Składnia**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
-   `num_expr`  
  
     Jest dowolne prawidłowe wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca jego prawej części "abc" dla różnych wartości długości.  
  
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
  
     Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład pokazuje, jak używać RTRIM w zapytaniu.  
  
```  
SELECT RTRIM("  abc"), RTRIM("abc"), RTRIM("abc   ")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "   abc", "$2": "abc", "$3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Zwraca wartość logiczną wskazującą, czy pierwszy ciąg wyrażenia rozpoczyna się od drugiego.  
  
 **Składnia**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład sprawdza, jeśli ciąg "abc" rozpoczyna się znakiem "b" i "a".  
  
```  
SELECT STARTSWITH("abc", "b"), STARTSWITH("abc", "a")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_substring"></a> PODCIĄG  
 Zwraca część wyrażenia ciągu, zaczynając od pozycji liczony od zera określony znak i kontynuuje do określonej długości lub do końca ciągu.  
  
 **Składnia**  
  
```  
SUBSTRING(<str_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
-   `num_expr`  
  
     Jest dowolne prawidłowe wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład zwraca podciąg "abc", począwszy od stawki 1 i długości 1 znak.  
  
```  
SELECT SUBSTRING("abc", 1, 1)  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 Zwraca ciąg reprezentujący wyrażenie skalarne. 
  
 **Składnia**  
  
```  
ToString(<expr>)
```  
  
 **Argumenty**  
  
-   `expr`  
  
     Jest dowolne prawidłowe wyrażenie skalarne.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład pokazuje, jak ToString zachowuje się na różnych urządzeniach.   
  
```  
SELECT ToString(1.0000), ToString("Hello World"), ToString(NaN), ToString(Infinity),
ToString(IS_STRING(ToString(undefined))), IS_STRING(ToString(0.1234), ToString(false), ToString(undefined))
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "1", "$2": "Hello World", "$3": "NaN", "$4": "Infinity", "$5": "false", "$6": true, "$7": "false"}]  
```  
 Biorąc pod uwagę następujące dane wejściowe:
```  
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 Poniższy przykład pokazuje, jak można używać z innymi funkcjami ciąg znaków, takich jak CONCAT ToString.   
 
```  
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1":"4lb" },
 {"$1":"32kg"},
 {"$1":"400g" },
 {"$1":"8999mg" }]

```  
Biorąc pod uwagę następujące dane wejściowe.
```
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
 Poniższy przykład pokazuje, jak ToString można używać z innymi funkcjami ciągu, np. ZAMIANY.   
```
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
 W tym miejscu znajduje się zestaw wyników.  
 ```
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
 ``` 
 
####  <a name="bk_trim"></a> TRIM  
 Zwraca wyrażenie ciągu, po usuwa wiodące i końcowe spacje.  
  
 **Składnia**  
  
```  
TRIM(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład pokazuje, jak używać TRIM w zapytaniu.  
  
```  
SELECT TRIM("   abc"), TRIM("   abc   "), TRIM("abc   "), TRIM("abc")   
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "abc", "$2": "abc", "$3": "abc", "$4": "abc"}]  
``` 
####  <a name="bk_upper"></a> GÓRNY  
 Zwraca wyrażenie ciągu po przekonwertowaniu danych małej litery na wielkie litery.  
  
 **Składnia**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład przedstawia sposób użycia w zapytaniu w prawym GÓRNYM  
  
```  
SELECT UPPER("Abc")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": "ABC"}]  
```  
  
###  <a name="bk_array_functions"></a> Funkcje tablicy  
 Następujące funkcje skalarne wykonania operacji na wartości wejściowej tablicy i zwrócenia numeryczne, wartości tablicy lub atrybut typu wartość logiczna  
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 Zwraca tablicę, która jest wynikiem łączenia dwóch lub więcej wartości tablicy.  
  
 **Składnia**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **Argumenty**  
  
-   `arr_expr`  
  
     Jest dowolnym wyrażeniem prawidłową tablicą.  
  
 **Typy zwracane**  
  
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
Zwraca wartość Boolean wskazującą, czy tablica zawiera określoną wartość. Można określić, czy dopasowanie pełnej lub częściowej. 

 **Składnia**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argumenty**  
  
-   `arr_expr`  
  
     Jest dowolnym wyrażeniem prawidłową tablicą.  
  
-   `expr`  
  
     Jest dowolnym prawidłowym wyrażeniem.  

-   `bool_expr`  
  
     Jest dowolne wyrażenie logiczne.       
  
 **Typy zwracane**  
  
 Zwraca wartość typu Boolean.  
  
 **Przykłady**  
  
 Poniższy przykład jak sprawdzić, czy członkostwo w tablicy przy użyciu ARRAY_CONTAINS.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples"),  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes")  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1": true, "$2": false}]  
```  

 Poniższy przykład jak sprawdzić, czy częściowym JSON w tablicy przy użyciu ARRAY_CONTAINS.  
  
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
  
     Jest dowolnym wyrażeniem prawidłową tablicą.  
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego.  
  
 **Przykłady**  
  
 Poniższy przykład sposobu uzyskania długości tablicy przy użyciu ARRAY_LENGTH.  
  
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
  
     Jest dowolnym wyrażeniem prawidłową tablicą.  
  
-   `num_expr`  
  
     Jest dowolne prawidłowe wyrażenie liczbowe.  
  
 **Typy zwracane**  
  
 Zwraca wartość typu Boolean.  
  
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
 Następujące funkcje skalarne wykonania operacji na wartości wejściowej obiektu przestrzennego i zwracają wartość liczbowa lub Boolean.  
  
||||  
|-|-|-|  
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|  
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)|||  
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 Zwraca odległość między dwoma wyrażeniami GeoJSON punkt, wielokąta lub LineString.  
  
 **Składnia**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumenty**  
  
-   `spatial_expr`  
  
     Jest dowolne prawidłowe wyrażenie obiektu GeoJSON punkt, wielokąta lub LineString.  
  
 **Typy zwracane**  
  
 Zwraca wartość wyrażenia liczbowego zawierający odległość. To wymaganie jest wyrażone w metrach dla systemu odniesienia domyślne.  
  
 **Przykłady**  
  
 Poniższy przykład pokazuje, jak zwrócić wszystkie dokumenty rodziny, które znajdują się w 30 km określonej lokalizacji za pomocą wbudowanych funkcji ST_DISTANCE. .  
  
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
 Zwraca wartość wskazującą, czy obiekt GeoJSON (punkt, wielokąta lub LineString) określona w pierwszym argumencie znajduje się w GeoJSON (punkt wielokąta i LineString) w drugim argumencie wyrażenia logicznego.  
  
 **Składnia**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumenty**  
  
-   `spatial_expr`  
  
     Jest dowolne prawidłowe wyrażenie obiektu GeoJSON punkt, wielokąta lub LineString.  
 
-   `spatial_expr`  
  
     Jest dowolne prawidłowe wyrażenie obiektu GeoJSON punkt, wielokąta lub LineString.  
  
 **Typy zwracane**  
  
 Zwraca wartość typu Boolean.  
  
 **Przykłady**  
  
 Poniższy przykład pokazuje, jak znaleźć wszystkie dokumenty rodziny Wielokąt przy użyciu ST_WITHIN.  
  
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
 Zwraca wartość wskazującą, czy obiekt GeoJSON (punkt, wielokąta lub LineString) określona w pierwszym argumencie przecina GeoJSON (punkt wielokąta i LineString) w drugim argumencie wyrażenia logicznego.  
  
 **Składnia**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumenty**  
  
-   `spatial_expr`  
  
     Jest dowolne prawidłowe wyrażenie obiektu GeoJSON punkt, wielokąta lub LineString.  
 
-   `spatial_expr`  
  
     Jest dowolne prawidłowe wyrażenie obiektu GeoJSON punkt, wielokąta lub LineString.  
  
 **Typy zwracane**  
  
 Zwraca wartość typu Boolean.  
  
 **Przykłady**  
  
 Poniższy przykład pokazuje, jak odnaleźć wszystkie obszary, które przecinają z danym wielokąta.  
  
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
 Zwraca wartość logiczną wskazującą, czy określone wyrażenie GeoJSON punkt, wielokąta lub LineString jest nieprawidłowy.  
  
 **Składnia**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumenty**  
  
-   `spatial_expr`  
  
     Jest dowolne prawidłowe wyrażenie GeoJSON punkt, wielokąta lub LineString.  
  
 **Typy zwracane**  
  
 Zwraca wyrażenie logiczne.  
  
 **Przykłady**  
  
 Poniższy przykład pokazuje, jak sprawdzić, czy punkt jest prawidłowy, za pomocą ST_VALID.  
  
 Na przykład ten punkt ma wartość szerokości geograficznej, która nie jest prawidłowy zakres wartości [-90, 90], dlatego zwraca wartość false zapytania.  
  
 W przypadku wielokątów specyfikacją GeoJSON wymaga ostatnią parę współrzędnych podane powinna być taka sama jak pierwsza, aby utworzyć kształt zamknięty. Punkty, w ramach Wielokąt musi być określona w kolejności przeciwnie do ruchu wskazówek zegara. Wielokąt podane w kolejności do ruchu wskazówek zegara reprezentuje odwrotność region znajdujący się w nim.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{ "$1": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Zwraca wartość JSON, zawierająca wartość logiczną wartość, jeśli określone wyrażenie GeoJSON punkt, wielokąta lub LineString jest prawidłowy, a nieprawidłowy, dodatkowo Przyczyna jako wartość ciągu.  
  
 **Składnia**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumenty**  
  
-   `spatial_expr`  
  
     Jest dowolne GeoJSON prawidłowe wyrażenie punktów i wielokątów.  
  
 **Typy zwracane**  
  
 Zwraca wartość JSON, zawierająca wartość logiczną wartość, jeśli określone wyrażenie punktu lub Wielokąt GeoJSON jest prawidłowy, a nieprawidłowy, dodatkowo Przyczyna jako wartość ciągu.  
  
 **Przykłady**  
  
 Poniższy przykład sposobu sprawdzania poprawności (ze szczegółowymi informacjami) przy użyciu ST_ISVALIDDETAILED.  
  
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
 [Składnia SQL i zapytania SQL usługi Azure Cosmos DB](sql-api-sql-query.md)   
 [Dokumentacja usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)  
  
  
