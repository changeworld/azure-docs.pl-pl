---
title: Wydajność usługi Phoenix w usłudze Azure HDInsight
description: Najważniejsze wskazówki dotyczące optymalizacji wydajności usługi Apache Phoenix dla klastrów usługi Azure HDInsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 7f8f20be81e815414c283f7ec48aa6503e3b60ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552648"
---
# <a name="apache-phoenix-performance-best-practices"></a>Najlepsze rozwiązania w zakresie wydajności dla rozwiązania Apache Phoenix

Najważniejszym aspektem wydajności [Apache Phoenix](https://phoenix.apache.org/) jest optymalizacja podstawowej [Apache HBase](https://hbase.apache.org/). Phoenix tworzy relacyjny model danych na bazie HBase, który konwertuje zapytania SQL na operacje HBase, takie jak skanowanie. Projekt schematu tabeli, wybór i kolejność pól w kluczu podstawowym i użycie indeksów mają wpływ na wydajność phoenix.

## <a name="table-schema-design"></a>Projekt schematu tabeli

Podczas tworzenia tabeli w Phoenix, że tabela jest przechowywana w tabeli HBase. Tabela HBase zawiera grupy kolumn (rodzin kolumn), które są dostępne razem. Wiersz w tabeli Phoenix to wiersz w tabeli HBase, w którym każdy wiersz składa się z komórek wersjonatowanych skojarzonych z jedną lub kilkoma kolumnami. Logicznie rzecz biorąc pojedynczy wiersz Bazy danych HBase jest kolekcją par klucz-wartość, z których każda ma tę samą wartość klucza wiersza. Oznacza to, że każda para klucza wartości ma atrybut klucza wiersza, a wartość tego atrybutu klucza wiersza jest taka sama dla określonego wiersza.

Projekt schematu tabeli Phoenix zawiera projekt klucza podstawowego, projekt rodziny kolumn, projekt poszczególnych kolumn i sposób podziału danych.

### <a name="primary-key-design"></a>Projekt klucza podstawowego

Klucz podstawowy zdefiniowany w tabeli w Phoenix określa sposób przechowywania danych w kluczu wiersza podstawowej tabeli HBase. W HBase jedynym sposobem uzyskania dostępu do określonego wiersza jest z rowkey. Ponadto dane przechowywane w tabeli HBase są sortowane według klucza wiersza. Phoenix tworzy wartość rowkey przez łączenie wartości każdej z kolumn w wierszu, w kolejności, w jakiej są zdefiniowane w kluczu podstawowym.

Na przykład tabela kontaktów ma imię, nazwisko, numer telefonu i adres, wszystkie w tej samej rodzinie kolumn. Klucz podstawowy można zdefiniować na podstawie rosnącego numeru sekwencjonowego:

|klucz wiersza|       adres|   phone| firstName| lastName|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 Dr. San Gabriel|1-425-000-0002|    John|Dole (dole)|
|  8396|5415 Dr. San Gabriel|1-230-555-0191|  Calvin|Raji|

Jednak jeśli często kwerendy przez lastName ten klucz podstawowy może nie działać dobrze, ponieważ każda kwerenda wymaga pełnego skanowania tabeli, aby odczytać wartość co lastName. Zamiast tego można zdefiniować klucz podstawowy w kolumnach lastName, firstName i social security number. Ta ostatnia kolumna ma na celu odróżnienie dwóch mieszkańców pod tym samym adresem o tej samej nazwie, takich jak ojciec i syn.

|klucz wiersza|       adres|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 Dr. San Gabriel|1-425-000-0002|    John|Dole (dole)| 111 |
|  8396|5415 Dr. San Gabriel|1-230-555-0191|  Calvin|Raji| 222 |

Z tym nowym kluczem podstawowym klucze wiersza generowane przez Phoenix będzie:

|klucz wiersza|       adres|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 Dr. San Gabriel|1-425-000-0002|    John|Dole (dole)| 111 |
|  Raji-Calvin-222|5415 Dr. San Gabriel|1-230-555-0191|  Calvin|Raji| 222 |

W pierwszym wierszu powyżej dane dla klucza wiersza są reprezentowane w sposób pokazany:

|klucz wiersza|       key|   value|
|------|--------------------|---|
|  Dole-John-111|adres |1111 Dr. San Gabriel|  
|  Dole-John-111|phone |1-425-000-0002|  
|  Dole-John-111|firstName |John|  
|  Dole-John-111|lastName |Dole (dole)|  
|  Dole-John-111|socialSecurityNum |111|

Ten klucz wiersza przechowuje teraz zduplikowaną kopię danych. Należy wziąć pod uwagę rozmiar i liczbę kolumn, które zostaną uwzględnione w kluczu podstawowym, ponieważ ta wartość jest uwzględniana w każdej komórce w podstawowej tabeli HBase.

Ponadto, jeśli klucz podstawowy ma wartości, które są monotonicznie rosnące, należy utworzyć stół z *zasobnikami soli,* aby uniknąć tworzenia hotspotów zapisu - zobacz [Dane partycji](#partition-data).

### <a name="column-family-design"></a>Projekt rodziny kolumn

Jeśli niektóre kolumny są dostępne częściej niż inne, należy utworzyć wiele rodzin kolumn, aby oddzielić często używane kolumny od rzadko używanych kolumn.

Ponadto jeśli niektóre kolumny mają tendencję do uzyskiwania dostępu razem, umieść te kolumny w tej samej rodzinie kolumn.

### <a name="column-design"></a>Projekt kolumny

* Zachowaj kolumny VARCHAR poniżej około 1 MB ze względu na koszty we/wy dużych kolumn. Podczas przetwarzania zapytań HBase materializuje komórki w całości przed wysłaniem ich do klienta, a klient odbiera je w całości przed przekazaniem ich do kodu aplikacji.
* Przechowuj wartości kolumn przy użyciu kompaktowego formatu, takiego jak protobuf, Avro, msgpack lub BSON. JSON nie jest zalecane, ponieważ jest większy.
* Należy wziąć pod uwagę kompresji danych przed magazynem, aby zmniejszyć opóźnienia i koszty we/wy.

### <a name="partition-data"></a>Partycjonowanie danych

Phoenix umożliwia kontrolowanie liczby regionów, w których dane są dystrybuowane, co może znacznie zwiększyć wydajność odczytu/zapisu. Podczas tworzenia tabeli Phoenix można sól lub wstępnie podzielić dane.

Aby sól tabeli podczas tworzenia, należy określić liczbę wiadra soli:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

To solenie dzieli tabelę wzdłuż wartości kluczy podstawowych, wybierając wartości automatycznie. 

Aby kontrolować, gdzie występują podziały tabeli, można wstępnie podzielić tabelę, podając wartości zakresu, wzdłuż których występuje podział. Na przykład, aby utworzyć tabelę podzieloną wzdłuż trzech regionów:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Projekt indeksu

Indeks Phoenix to tabela bazy danych HBase, która przechowuje kopię niektórych lub wszystkich danych z tabeli indeksowanych. Indeks zwiększa wydajność dla określonych typów zapytań.

Jeśli zdefiniowano wiele indeksów, a następnie kwerenda tabeli, Phoenix automatycznie wybiera najlepszy indeks dla kwerendy. Indeks podstawowy jest tworzony automatycznie na podstawie wybranych kluczy podstawowych.

W przypadku przewidywanych zapytań można również utworzyć indeksy pomocnicze, określając ich kolumny.

Podczas projektowania indeksów:

* Utwórz tylko potrzebne indeksy.
* Ogranicz liczbę indeksów w często aktualizowanych tabelach. Aktualizacje tabeli przekładają się na zapisy zarówno do tabeli głównej, jak i tabel indeksu.

## <a name="create-secondary-indexes"></a>Tworzenie indeksów pomocniczych

Indeksy pomocnicze można poprawić wydajność odczytu, obracając, co byłoby pełne skanowanie tabeli w wyszukiwanie punktów, kosztem miejsca do magazynowania i szybkość zapisu. Indeksy pomocnicze mogą być dodawane lub usuwane po utworzeniu tabeli i nie wymagają zmian w istniejących kwerendach — kwerendy po prostu działają szybciej. W zależności od potrzeb należy rozważyć utworzenie indeksów objętych, indeksy funkcjonalne lub obu.

### <a name="use-covered-indexes"></a>Używanie indeksów objętych gwarancją

Indeksy objęte są indeksy, które zawierają dane z wiersza oprócz wartości, które są indeksowane. Po znalezieniu żądanego wpisu indeksu nie ma potrzeby uzyskiwania dostępu do tabeli podstawowej.

Na przykład w przykładowej tabeli kontaktów można utworzyć indeks pomocniczy tylko w kolumnie socialSecurityNum. Ten indeks pomocniczy przyspieszy kwerendy, które filtrują według wartości socialSecurityNum, ale pobieranie innych wartości pól będzie wymagało innego odczytu względem tabeli głównej.

|klucz wiersza|       adres|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 Dr. San Gabriel|1-425-000-0002|    John|Dole (dole)| 111 |
|  Raji-Calvin-222|5415 Dr. San Gabriel|1-230-555-0191|  Calvin|Raji| 222 |

Jednak jeśli zazwyczaj chcesz wyszukać imię i lastName biorąc pod uwagę socialSecurityNum, można utworzyć indeks objęte, który zawiera firstName i lastName jako rzeczywiste dane w tabeli indeksu:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

Ten objęty indeks umożliwia następujące zapytanie do uzyskania wszystkich danych tylko przez odczyt z tabeli zawierającej indeks pomocniczy:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>Używanie indeksów funkcjonalnych

Indeksy funkcjonalne umożliwiają utworzenie indeksu dla dowolnego wyrażenia, które ma być używane w kwerendach. Gdy masz indeks funkcjonalny w miejscu i kwerendy używa tego wyrażenia, indeks może służyć do pobierania wyników, a nie tabeli danych.

Na przykład można utworzyć indeks, aby umożliwić wyszukiwanie bez uwzględniania wielkości liter na połączone imię i nazwisko osoby:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Projekt kwerendy

Główne zagadnienia w projekcie kwerendy to:

* Zrozumieć plan kwerend i zweryfikować jego oczekiwane zachowanie.
* Dołącz wydajnie.

### <a name="understand-the-query-plan"></a>Opis planu kwerend

W [programie SQLLine](http://sqlline.sourceforge.net/)użyj funkcji WYJAŚNIJ, a następnie kwerendy SQL, aby wyświetlić plan operacji, które phoenix będzie wykonywać. Sprawdź, czy plan:

* W razie potrzeby używa klucza podstawowego.
* Używa odpowiednich indeksów pomocniczych, a nie tabeli danych.
* Używa skanowania zakresu lub pomijania skanowania, gdy tylko jest to możliwe, a nie skanowania tabeli.

#### <a name="plan-examples"></a>Przykłady planu

Na przykład załóżmy, że masz tabelę o nazwie FLIGHTS, która przechowuje informacje o opóźnieniu lotu.

Aby wybrać wszystkie loty z `19805`samolotem linii lotniczych , gdzie airlineid jest polem, które nie znajduje się w kluczu podstawowym lub w dowolnym indeksie:

    select * from "FLIGHTS" where airlineid = '19805';

Uruchom polecenie explain w następujący sposób:

    explain select * from "FLIGHTS" where airlineid = '19805';

Plan kwerend wygląda następująco:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

W tym planie zanotuj frazę FULL SCAN OVER FLIGHTS. Ta fraza wskazuje, że wykonanie wykonuje skanowanie tabeli we wszystkich wierszach w tabeli, a nie przy użyciu bardziej wydajnej opcji SKANOWANIA zakresu lub POMIJANIA SKANOWANIA.

Teraz, powiedzmy, że chcesz zapytać o loty w dniu `AA` 2 stycznia 2014 dla przewoźnika, gdzie jego flightnum był większy niż 1. Załóżmy, że kolumny rok, miesiąc, miesiąc, przewoźnik i flightnum istnieją w tabeli przykładowej i są częścią złożonego klucza podstawowego. Kwerenda będzie wyglądać w następujący sposób:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Przeanalizujmy plan dla tej kwerendy za pomocą:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Powstały plan jest:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

Wartości w nawiasach kwadratowych są zakresem wartości dla kluczy podstawowych. W takim przypadku wartości zakresu są ustalane z rokiem 2014, miesiącem 1 i miesiącem 2, ale`*`pozwalają na wartości dla flightnum, zaczynając od 2 i na górze ( ). Ten plan kwerend potwierdza, że klucz podstawowy jest używany zgodnie z oczekiwaniami.

Następnie utwórz indeks w tabeli LOTY o nazwie, `carrier2_idx` która znajduje się tylko w polu przewoźnika. Indeks ten obejmuje również flightdate, tailnum, origin i flightnum jako objęte kolumny, których dane są również przechowywane w indeksie.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Załóżmy, że chcesz uzyskać przewoźnika wraz z flightdate i tailnum, jak w poniższej kwerendzie:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

Powinien zostać wyświetlony ten indeks używany:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

Aby uzyskać pełną listę elementów, które mogą pojawić się w wynikach planu wyjaśnienia, zobacz sekcję Wyjaśnij plany w [Przewodniku dostrajania Apache Phoenix](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Efektywne dołączanie

Ogólnie rzecz biorąc, chcesz uniknąć sprzężenia, chyba że jedna strona jest mała, szczególnie w przypadku częstych zapytań.

Jeśli to konieczne, można wykonać `/*+ USE_SORT_MERGE_JOIN */` duże sprzężenia z podpowiedzią, ale duże sprzężenie jest kosztowną operacją na ogromną liczbę wierszy. Jeśli ogólny rozmiar wszystkich tabel po prawej stronie przekracza dostępnej `/*+ NO_STAR_JOIN */` pamięci, użyj wskazówki.

## <a name="scenarios"></a>Scenariusze

W poniższych wytycznych opisano niektóre typowe wzorce.

### <a name="read-heavy-workloads"></a>Obciążenia z dużą wysoką łańszeń odczytu

W przypadku przypadków użycia intensywnie odczytu upewnij się, że używasz indeksów. Ponadto, aby zaoszczędzić obciążenie czasu odczytu, należy rozważyć utworzenie indeksów objętych.

### <a name="write-heavy-workloads"></a>Obciążenia związane z zapisami

W przypadku obciążeń o dużej wydajności, w których klucz podstawowy jest monotonicznie zwiększa, należy utworzyć zasobniki soli, aby uniknąć punktów aktywnych zapisu, kosztem ogólnej przepływności odczytu z powodu dodatkowych potrzebnych skanów. Ponadto, korzystając z usługi UPSERT do zapisywania dużej liczby rekordów, należy wyłączyć autoCommit i wy partii rekordy.

### <a name="bulk-deletes"></a>Usuwanie zbiorcze

Podczas usuwania dużego zestawu danych włącz autoCommit przed wydaniem kwerendy DELETE, aby klient nie musiał zapamiętywać kluczy wierszy dla wszystkich usuniętych wierszy. AutoCommit uniemożliwia klientowi buforowanie wierszy, których dotyczy delete, dzięki czemu Phoenix może usunąć je bezpośrednio na serwerach regionu bez kosztu zwrócenia ich do klienta.

### <a name="immutable-and-append-only"></a>Niezmienne i tylko do dołączania

Jeśli scenariusz sprzyja szybkości zapisu nad integralnością danych, należy rozważyć wyłączenie dziennika zapisu z wyprzedzeniem podczas tworzenia tabel:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

Aby uzyskać szczegółowe informacje na temat tej i innych opcji, zobacz [Apache Phoenix Grammar](https://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Następne kroki

* [Apache Phoenix Tuning Przewodnik](https://phoenix.apache.org/tuning_guide.html)
* [Indeksy pomocnicze](https://phoenix.apache.org/secondary_indexing.html)
