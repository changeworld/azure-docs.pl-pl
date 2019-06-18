---
title: Wydajność Phoenix w usłudze Azure HDInsight
description: Najlepsze rozwiązania w celu zoptymalizowania wydajności Phoenix.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 4fc4d1843ddb8d007ca062d928ebbddf90909583
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64690046"
---
# <a name="apache-phoenix-performance-best-practices"></a>Najlepsze rozwiązania w zakresie wydajności dla rozwiązania Apache Phoenix

Najważniejszym aspektem [Apache Phoenix](https://phoenix.apache.org/) wydajność jest zoptymalizować bazowego [bazy danych Apache HBase](https://hbase.apache.org/). Phoenix tworzy na jego podstawie bazy danych HBase, który konwertuje zapytań SQL na operacje bazy danych HBase, takie jak skanowanie modelu opartego na danych relacyjnych. Projektowanie schematu tabeli, wybór i kolejność pól na klucz podstawowy i korzystanie z wszystkich indeksów wpłynąć na wydajność Phoenix.

## <a name="table-schema-design"></a>Projekt schematu tabeli

Po utworzeniu tabeli Phoenix tej tabeli są przechowywane w tabeli HBase. Tabela bazy danych HBase zawiera grupy kolumn (rodziny kolumn), które są używane razem. Wiersz w tabeli Phoenix jest wiersz w tabeli HBase, w którym każdy wiersz składa się z komórek numerów wersji skojarzony z co najmniej jedną kolumnę. Logicznie pojedynczy wiersz bazy danych HBase jest kolekcją par klucz wartość, mających taką samą wartość rowkey. Oznacza to każdej pary klucz wartość ma atrybut rowkey, a wartość tego atrybutu rowkey jest taka sama, dla konkretnego wiersza.

Projekt schematu tabeli Phoenix obejmuje podstawowy klucz projektu, projekt rodziny kolumn, projektowania poszczególnych kolumn i sposób partycjonowania danych.

### <a name="primary-key-design"></a>Projekt klucza podstawowego

Klucz podstawowy, zdefiniowanego w tabeli Phoenix Określa, jak dane są przechowywane w rowkey tabeli podstawowej bazy danych HBase. W bazie danych HBase to jest jedynym sposobem na dostęp do określonego wiersza, rowkey. Ponadto — dane przechowywane w tabeli bazy danych HBase jest sortowana przez rowkey. Phoenix tworzy wartość rowkey przez złączenie wartości każdej z kolumn w wierszu, w kolejności, w którym są zdefiniowane w kluczu podstawowym.

Na przykład tabela kontaktów ma imię, ostatnia nazwa, numer telefonu i adres, w tej samej rodziny kolumn. Można zdefiniować klucz podstawowy, w oparciu o rosnący numer kolejny:

|rowkey|       address|   phone| firstName| lastName|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    Jan|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvina|Raji|

Jednak po wykonaniu zapytania według nazwiska często tego klucza podstawowego może nie działać poprawnie, ponieważ każde zapytanie wymaga pełne skanowanie tabeli można odczytać wartości każdego lastName. Zamiast tego można zdefiniować klucz podstawowy na lastName, imię i numer ubezpieczenia społecznego kolumn. Jest to ostatnia kolumna do odróżniania dwóch mieszkańców na ten sam adres o takiej samej nazwie, takie jak ojcem i syn.

|rowkey|       address|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    Jan|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvina|Raji| 222 |

Przy użyciu nowego klucza podstawowego wiersza będzie klucze generowane przez Phoenix:

|rowkey|       address|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    Jan|Dole| 111 |
|  Raji Calvina-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvina|Raji| 222 |

W pierwszym wierszu powyżej jest reprezentowany dane rowkey, jak pokazano:

|rowkey|       key|   value| 
|------|--------------------|---|
|  Dole-John-111|address |1111 San Gabriel Dr.|  
|  Dole-John-111|phone |1-425-000-0002|  
|  Dole-John-111|firstName |Jan|  
|  Dole-John-111|lastName |Dole|  
|  Dole-John-111|socialSecurityNum |111| 

Ta rowkey teraz przechowuje kopię danych. Należy wziąć pod uwagę rozmiar i liczba kolumn, które uwzględniasz w klucz podstawowy, ponieważ ta wartość jest dołączone do każdej komórki w tabeli podstawowej bazy danych HBase.

Ponadto jeśli klucz podstawowy zawiera wartości, które przynoszą coraz większy monotonicznie, należy utworzyć tabelę z *soli zasobników* można uniknąć tworzenia punktów aktywnych zapisu — zobacz [partycjonowanie danych](#partition-data).

### <a name="column-family-design"></a>Projekt rodziny kolumn

Jeśli niektóre kolumny są dostępne częściej niż inne, należy utworzyć wiele rodzin kolumn do rozdzielania często używanych kolumn z rzadko używanych kolumn.

Ponadto jeśli niektóre kolumny mają tendencję do być używane razem, należy umieścić te kolumny w tej samej rodziny kolumn.

### <a name="column-design"></a>Kolumny projektu

* Zachowaj kolumny VARCHAR poniżej około 1 MB z powodu koszty operacji We/Wy duże kolumny. Podczas przetwarzania zapytań, bazy danych HBase materializuje komórek w całości przed wysłaniem ich przez klienta, a klient otrzymuje je w całości przed przekazaniem ich do kodu aplikacji.
* Store wartości kolumny za pomocą compact formatu protobuf, Avro, msgpack lub formatu BSON. JSON nie jest zalecane, ponieważ ma on większy.
* Należy wziąć pod uwagę skompresowanie danych przed utworzeniem magazynu można wyciąć opóźnień i kosztów operacji We/Wy.

### <a name="partition-data"></a>Partycjonowanie danych

Phoenix umożliwia sterowanie przez liczbę regionów dystrybuowane dane, które może znacznie zwiększyć wydajność odczytu i zapisu. Podczas tworzenia tabeli Phoenix, możesz soli lub wstępnie podziału danych.

Aby soli tabeli podczas tworzenia, określ liczbę przedziałów ziarna:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

Ta solenie dzieli tabeli wzdłuż wartości kluczy podstawowych, wybierając wartość automatycznie. 

Do kontrolowania, gdzie występują podziały tabeli, możesz wstępnie podzielić tabelę, podając wartości zakresu, w których występuje, podziału. Na przykład aby utworzyć tabelę Podziel wzdłuż trzech regionach:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Projekt indeksu

Indeks Phoenix jest tabeli HBase, który przechowuje kopię niektórych lub wszystkich danych z indeksowanej tabeli. Indeks zwiększa wydajność dla określonych typów zapytań.

Gdy ma wiele indeksów zdefiniowane, a następnie wysłać zapytanie do tabeli, Phoenix automatycznie wybiera najlepsze indeksu dla zapytania. Podstawowy indeks jest tworzony automatycznie w oparciu o kluczy podstawowych, którą wybierzesz.

Przewidywane zapytań można również utworzyć indeksy pomocnicze, określając ich kolumny.

Podczas projektowania indeksów:

* Tylko utworzyć indeksy, które są potrzebne.
* Ogranicz liczbę indeksów tabel często aktualizowane. Aktualizacje do tabeli przekłada się na operacje zapisu do głównej tabeli i tabele indeksów.

## <a name="create-secondary-indexes"></a>Tworzenie indeksów pomocniczych

Indeksy pomocnicze może zwiększyć wydajność odczytu, włączając, co mogłoby być pełne skanowanie tabeli do wyszukiwania punktów, kosztem miejsca do magazynowania i szybkość zapisu. Indeksów pomocniczych można dodać lub usunąć po utworzeniu tabeli i nie wymaga zmian w istniejących zapytaniach — zapytania po prostu działają szybciej. W zależności od potrzeb należy wziąć pod uwagę tworzenie indeksów objęte usługą i/lub indeksy funkcjonalności.

### <a name="use-covered-indexes"></a>Użyj ustawy indeksów

Pokryte indeksy są indeksy, które zawierają dane z wiersza, oprócz wartości, które są indeksowane. Po znalezieniu wpis żądanego indeksu, nie ma potrzeby dostępu do tabeli podstawowej.

Na przykład w tym przykładzie należy skontaktować się z tabeli, można utworzyć pomocniczego indeksu tylko kolumny socialSecurityNum. Ten indeks pomocniczy może przyspieszyć działanie zapytań, które filtrować według wartości socialSecurityNum, ale pobieranie innych wartości pól będzie wymagać innego odczytu względem tabeli głównej.

|rowkey|       address|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    Jan|Dole| 111 |
|  Raji Calvina-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvina|Raji| 222 |

Jednak zazwyczaj chcesz wyszukać imię i nazwisko, biorąc pod uwagę socialSecurityNum, można utworzyć ustawy indeks, który zawiera rzeczywiste dane w tabeli indeksów imię i nazwisko:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

Ten indeks objęte usługą umożliwia następujące zapytanie, aby pobrać wszystkie dane tylko przez odczyt z tabeli zawierającej indeks pomocniczy:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>Użyj funkcjonalności indeksów

Indeksy funkcjonalne pozwalają by utworzyć indeks na dowolne wyrażenie, które mają być używane w zapytaniach. Po ma funkcjonalności indeksu w miejscu, a zapytanie używa to wyrażenie, indeks może służyć do pobierania wyników, a nie w tabeli danych.

Na przykład można utworzyć indeksu w celu pozwalają wykonać wyszukiwanie bez uwzględniania wielkości liter na połączone imię i nazwisko osoby:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Projekt kwerendy

Główne zagadnienia dotyczące projektowania zapytań są następujące:

* Omówienie planu zapytania, a następnie sprawdź jej oczekiwane zachowanie.
* Dołącz do wydajnie.

### <a name="understand-the-query-plan"></a>Omówienie planu zapytania

W [SQLLine](http://sqlline.sourceforge.net/), użyj wyjaśnienia, a następnie przez zapytanie SQL, aby wyświetlić planu operacje wykonujące Phoenix. Sprawdź, czy plan:

* Używa klucz podstawowy, gdy jest to konieczne.
* Używa odpowiednich indeksów pomocniczych, a nie w tabeli danych.
* Używa zakresu skanowania lub POMIŃ skanowania, jeśli to możliwe, a nie skanowanie tabeli.

#### <a name="plan-examples"></a>Przykłady planu

Na przykład załóżmy, że masz tabelę o nazwie lotów przechowuje informacje o lotach opóźnienia.

Aby wybrać wszystkie loty z airlineid z `19805`, gdzie airlineid to pole, które nie ma w kluczu podstawowym lub w dowolnym indeksie:

    select * from "FLIGHTS" where airlineid = '19805';

Uruchom polecenie wyjaśnienia w następujący sposób:

    explain select * from "FLIGHTS" where airlineid = '19805';

W planie zapytania wygląda następująco:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

Należy pamiętać frazy PEŁNEGO skanowania przez lotów, w ramach tego planu. To wyrażenie wskazuje, że wykonanie obsługuje skanowania tabeli za pośrednictwem wszystkich wierszy w tabeli, a nie przy użyciu bardziej wydajne opcji skanowania zakresu lub POMIŃ skanowania.

Teraz załóżmy, które chcesz zbadać lotów 2 stycznia 2014 r. dla operatora `AA` gdzie jego flightnum była większa niż 1. Załóżmy, że kolumny roku, miesiąca, dayofmonth, operatora i flightnum istnieją w przykładowej tabeli i są częścią złożony klucz podstawowy. Zapytania wyglądałby następująco:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Przeanalizujmy plan dla tego zapytania przy użyciu:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Wynikowy plan jest:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

Wartości w nawiasach kwadratowych są zakres wartości kluczy podstawowych. W tym przypadku wartości zakresu zostały usunięte z 2014 roku, miesiąc 1 i dayofmonth 2, ale zezwalaj na wartości flightnum uruchamiania przy użyciu 2 i w (`*`). Ten plan zapytania potwierdza, że klucz podstawowy jest w użyciu zgodnie z oczekiwaniami.

Następnie utwórz indeks w tabeli lotów o nazwie `carrier2_idx` który jest używany tylko w polu operatora. Ten indeks zawiera także flightdate, tailnum, pochodzenia i flightnum jako kolumny objęte usługą, którego dane są także przechowywane w indeksie.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Załóżmy, że chcesz pobrać operatora oraz flightdate i tailnum, tak jak w następującej kwerendy:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

Powinien zostać wyświetlony ten indeks używany:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

Aby uzyskać pełną listę elementów, które mogą wystąpić w zawierać wyjaśnienia wyników planu, w sekcji wyjaśniono plany, w [przewodnika dostrajania programu Apache Phoenix](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Dołącz do wydajnego

Ogólnie rzecz biorąc należy unikać sprzężeń, chyba że z jednej strony jest mały, zwłaszcza w przypadku często wykonywane zapytania.

Jeśli to konieczne, możesz to zrobić duże sprzężenia z `/*+ USE_SORT_MERGE_JOIN */` wskazówkę, jednak duże sprzężenia jest kosztowną operacją za pośrednictwem ogromną liczbę wierszy. Jeśli całkowity rozmiar wszystkich tabel po prawej stronie spowoduje przekroczenie ilość dostępnej pamięci, należy użyć `/*+ NO_STAR_JOIN */` wskazówki.

## <a name="scenarios"></a>Scenariusze

Poniższe wskazówki opisano niektóre typowe wzorce.

### <a name="read-heavy-workloads"></a>Odczycie obciążeń

Odczycie przypadki użycia, upewnij się, że używasz indeksów. Ponadto można zapisać — czas odczytu obciążenie, należy rozważyć utworzenie indeksów objętego usługą.

### <a name="write-heavy-workloads"></a>Zapis duże obciążenia

W przypadku obciążeń procesów, gdzie monotonicznie zwiększa się klucz podstawowy należy utworzyć ziarna zasobników, aby uniknąć obszarów aktywności zapisu, kosztem ogólną przepływność odczytu ze względu na dodatkowe skanowania, potrzebne. Ponadto za pomocą UPSERT napisać dużą liczbę rekordów, wyłącz automatycznego zatwierdzania i usługi batch rekordy.

### <a name="bulk-deletes"></a>Usuwa zbiorcze

Po usunięciu dużego zestawu danych, włączyć automatycznego zatwierdzania przed wystawieniem zapytanie DELETE, aby klient nie potrzebuje do zapamiętania kluczy wierszy dla wszystkich usuniętych wierszy. Automatycznego zatwierdzania uniemożliwi buforowania odpowiednie wiersze, usuwania, tak że Phoenix usunąć je bezpośrednio na serwerach regionu bez konieczności zwróceniem do klienta przez klienta.

### <a name="immutable-and-append-only"></a>Niezmienny i tylko do dołączania

Jeśli dany scenariusz preferuje szybkość zapisu za pośrednictwem integralność danych, należy rozważyć wyłączenie dziennik zapisu z wyprzedzeniem, podczas tworzenia tabel:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

Aby uzyskać więcej informacji na ten temat i inne opcje, zobacz [Apache Phoenix gramatyki](https://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Kolejne kroki

* [Przewodnik dostosowywania programu Apache Phoenix](https://phoenix.apache.org/tuning_guide.html)
* [Indeksy pomocnicze](https://phoenix.apache.org/secondary_indexing.html)
