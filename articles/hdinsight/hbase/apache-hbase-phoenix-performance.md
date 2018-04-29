---
title: Phoenix wydajności w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Najlepsze rozwiązania w celu optymalizacji wydajności Phoenix.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 58ecf22fa0f9349a767455fe3ab08fca058d02da
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="phoenix-performance-best-practices"></a>Najlepsze rozwiązania w zakresie wydajności dla rozwiązania Phoenix

Najważniejszym aspektem wydajności Phoenix jest zoptymalizować podstawowej bazy danych HBase. Phoenix tworzy model danych relacyjnych nad HBase, który konwertuje zapytań SQL na operacje bazy danych HBase, takie jak skanowanie. Projekt schemat tabeli, wybór i kolejność pól w primary key i korzystanie z wszystkich indeksów wpłynąć na wydajność Phoenix.

## <a name="table-schema-design"></a>Projekt schematu tabeli

Po utworzeniu tabeli w Phoenix tym tabela jest przechowywana w tabeli HBase. W tabeli HBase zawiera grupy kolumn (rodzinami kolumn), które są dostępne ze sobą. Wiersz w tabeli Phoenix jest wiersz w tabeli HBase, w którym każdy wiersz składa się z kontrolą wersji komórek skojarzony z co najmniej jedną kolumnę. Logicznie pojedynczy wiersz HBase jest kolekcją par klucz wartość, o tej samej wartości rowkey. Oznacza to, że każda para klucz wartość ma atrybut rowkey, i wartość tego atrybutu rowkey jest taki sam dla określonego wiersza.

Projekt schematu tabeli Phoenix obejmuje podstawowego klucza projektu, projektu rodziny kolumny projektu poszczególnych kolumny i jak dane są podzielone na partycje.

### <a name="primary-key-design"></a>Projekt klucza podstawowego

Klucz podstawowy, zdefiniowanego w tabeli Phoenix Określa, jak dane są przechowywane w rowkey tabeli podstawowej bazy danych HBase. W bazie danych HBase jedynym sposobem na dostęp do określonego wiersza jest z rowkey. Ponadto dane przechowywane w tabeli HBase jest sortowana według rowkey. Phoenix tworzy wartość rowkey przy wartości poszczególnych kolumn w wierszu, w kolejności, w którym są zdefiniowane w kluczu podstawowym.

Na przykład tabela kontaktów ma imię, ostatnia nazwa, numer telefonu i adres, w tej samej rodziny kolumn. Można zdefiniować klucz podstawowy oparty na rosnący numer sekwencji:

|rowkey|       Adres|   Telefon| Imię| Nazwisko|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 Dr Gabriel sieci San.|1-425-000-0002|    Jan|Dole|
|  8396|5415 Dr Gabriel sieci San.|1-230-555-0191|  Calvina|Raji|

Jednak jeśli często zapytania według lastName to klucz podstawowy mogą nie działać, ponieważ każde zapytanie wymaga skanowania tabeli pełne można odczytać wartości co nazwisko. Zamiast tego można zdefiniować klucz podstawowy lastName, firstName i kolumny numer ubezpieczenia społecznego. Jest to ostatnia kolumna do odróżniania dwóch mieszkańców pod tym samym adresem o takiej samej nazwie, takie jak ojciec i syn.

|rowkey|       Adres|   Telefon| Imię| Nazwisko| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 Dr Gabriel sieci San.|1-425-000-0002|    Jan|Dole| 111 |
|  8396|5415 Dr Gabriel sieci San.|1-230-555-0191|  Calvina|Raji| 222 |

Z tego nowego klucza podstawowego wiersz będzie klucze generowane przez Phoenix:

|rowkey|       Adres|   Telefon| Imię| Nazwisko| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 Dr Gabriel sieci San.|1-425-000-0002|    Jan|Dole| 111 |
|  Raji Calvina-222|5415 Dr Gabriel sieci San.|1-230-555-0191|  Calvina|Raji| 222 |

W pierwszym wierszu powyżej dane rowkey odpowiada, jak pokazano:

|rowkey|       key|   wartość| 
|------|--------------------|---|
|  Dole-John-111|Adres |1111 Dr Gabriel sieci San.|  
|  Dole-John-111|Telefon |1-425-000-0002|  
|  Dole-John-111|Imię |Jan|  
|  Dole-John-111|Nazwisko |Dole|  
|  Dole-John-111|socialSecurityNum |111| 

Ta rowkey teraz przechowuje kopię danych. Należy wziąć pod uwagę rozmiar i liczba kolumn, który zawiera klucz podstawowy, ponieważ ta wartość jest dołączana do każdej komórki w tabeli podstawowej bazy danych HBase.

Ponadto jeśli klucz podstawowy zawiera wartości, które monotonicznie rosną, należy utworzyć tabelę z *soli zasobników* można uniknąć tworzenie punktów zapisu aktywnych — zobacz [partycjonowania danych](#partition-data).

### <a name="column-family-design"></a>Rodziny projektu kolumny

Jeśli niektóre kolumny są dostępne częściej niż inne, należy utworzyć wiele rodzin kolumn do oddzielania rzadziej używanych kolumn z kolumn rzadko używane.

Ponadto jeśli niektóre kolumny często można uzyskać dostępu do siebie, umieść tych kolumn w tej samej rodziny kolumn.

### <a name="column-design"></a>Kolumny projektu

* Zachowaj kolumny VARCHAR poniżej około 1 MB z powodu kosztów operacji We/Wy dużych kolumn. Podczas przetwarzania zapytania, HBase zostaje komórek w pełni przed wysłaniem ich przez klienta, a klient otrzymuje ich w pełni przed przekazaniem ich do kodu aplikacji.
* Przechowywanie wartości kolumny za pomocą formacie kompaktowym, takich jak protobuf, Avro, msgpack lub BSON. JSON nie jest zalecane, ponieważ ma on większy.
* Rozważ możliwość kompresowania danych przed magazynu wycinania opóźnienia i kosztów operacji We/Wy.

### <a name="partition-data"></a>Partycjonowanie danych

Phoenix umożliwia kontrolowanie liczby regionów miejsce dystrybucji danych, które może znacznie zwiększyć wydajność odczytu i zapisu. Podczas tworzenia tabeli Phoenix, możesz soli lub wstępnie podziału danych.

Aby soli tabelę podczas tworzenia, określ liczbę przedziałów ziarna:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

Ta solenie dzieli tabeli wzdłuż wartości kluczy podstawowych, wybierając wartości automatycznie. 

Aby kontrolować, gdzie występuje podziałów tabeli, tabeli można podzielić wstępnie podając wartości zakresu, w których podziału występuje. Na przykład aby utworzyć tabelę Podziel wzdłuż trzech regionów:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Projekt indeksu

Indeks Phoenix jest tabeli HBase, która przechowuje kopię niektórych lub wszystkich danych z indeksowanej tabeli. Indeks poprawia wydajność dla określonych typów kwerend.

Gdy ma wiele indeksów zdefiniowana, a następnie zapytania dotyczącego tabeli, Phoenix automatycznie wybiera najlepsze indeksu dla zapytania. Podstawowy indeks jest tworzony automatycznie w oparciu o wybrane klucze podstawowe.

Indeksy dodatkowej można również utworzyć dla przewidywanego zapytań, określając ich kolumn.

Podczas projektowania sieci indeksów:

* Tylko tworzenie indeksów, które są potrzebne.
* Ogranicz liczbę indeksów tabel często aktualizowanych. Zapisuje dane w tabeli głównego oraz tabel indeksu przetłumaczenie aktualizacji do tabeli.

## <a name="create-secondary-indexes"></a>Tworzenie indeksów pomocniczych

Indeksów pomocniczych może poprawić wydajność odczytu przez włączenie, co może być skanowanie pełne tabeli do wyszukiwania punktów, kosztem miejsca do magazynowania i szybkość zapisu. Indeksów pomocniczych można dodać lub usunąć po utworzeniu tabeli i nie wymaga zmian w istniejących zapytaniach — zapytań tylko szybsze. W zależności od potrzeb należy rozważyć utworzenie objęte usługą indeksy i/lub indeksy funkcjonalności.

### <a name="use-covered-indexes"></a>Użyj objętych indeksów

Objęte usługą indeksy są indeksy, które zawierają dane z wiersza oprócz wartości, które są indeksowane. Po znalezieniu wpis żądanego indeksu, nie istnieje potrzeba dostępu do tabeli podstawowej.

Na przykład w tym przykładzie należy skontaktować się z tabeli, można utworzyć pomocniczego indeksu tylko kolumny socialSecurityNum. Pomocniczy indeks może przyspieszyć zapytań, które filtrować według wartości socialSecurityNum, ale pobieranie inne wartości pól będzie wymagać innego odczytu względem tabeli głównej.

|rowkey|       Adres|   Telefon| Imię| Nazwisko| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 Dr Gabriel sieci San.|1-425-000-0002|    Jan|Dole| 111 |
|  Raji Calvina-222|5415 Dr Gabriel sieci San.|1-230-555-0191|  Calvina|Raji| 222 |

Jednak zwykle można odszukać imię i nazwisko podane socialSecurityNum, można utworzyć indeksu objęte usługą, zawierający imię i nazwisko rzeczywiste dane w tabeli indeksu:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

Ten indeks objęte usługą umożliwia następujące zapytanie, aby pobrać wszystkie dane tylko przez odczyt z tabeli zawierającej indeks dodatkowej:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>Użyj funkcjonalności indeksów

Indeksy funkcjonalności umożliwiają tworzenie indeksu na dowolne wyrażenie, które mają być używane w zapytaniach. Po ma funkcjonalności indeksu w miejscu, a zapytanie używa tego wyrażenia, indeks może służyć do pobierania wyników, a nie w tabeli danych.

Na przykład można utworzyć indeksu, aby umożliwić wykonywanie wyszukiwania bez uwzględniania wielkości liter w scalonej imię i nazwisko osoby:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Projekt kwerendy

Główne zagadnienia dotyczące projektu zapytania są:

* Zrozumienie planu zapytania i sprawdź jej oczekiwane zachowanie.
* Dołącz wydajnie.

### <a name="understand-the-query-plan"></a>Zrozumienie planu zapytania

W [SQLLine](http://sqlline.sourceforge.net/), WYJAŚNIJ, a następnie przez zapytanie SQL umożliwia wyświetlanie planu Phoenix będzie wykonywać operacje. Sprawdź, czy plan:

* Korzysta z klucza podstawowego, gdy jest to konieczne.
* Używa odpowiednie indeksów pomocniczych, a nie w tabeli danych.
* Używa zakresu skanowania lub POMIŃ skanowania, jeśli to możliwe, zamiast skanowania tabeli.

#### <a name="plan-examples"></a>Przykłady planu

Na przykład załóżmy, że w tabeli o nazwie LOTACH, która przechowuje informacje o lotach opóźnienia.

Aby wybrać wszystkie lotach z airlineid z `19805`, gdzie airlineid to pole, które nie jest w kluczu podstawowym lub w dowolnym indeksu:

    select * from "FLIGHTS" where airlineid = '19805';

Uruchom polecenie Wyjaśnij w następujący sposób:

    explain select * from "FLIGHTS" where airlineid = '19805';

Plan zapytania wygląda następująco:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

W tym planie Pamiętaj frazy PEŁNEGO skanowania przez LOTACH. Ta fraza wskazuje, że wykonywanie obsługuje skanowania tabeli za pośrednictwem wszystkie wiersze w tabeli, a nie za pomocą opcji efektywniejsze skanowania zakresu lub POMIŃ skanowania.

Teraz, że chcesz wyszukać lotach na 2 stycznia 2014 r. dla operatora `AA` gdzie jego flightnum była większa niż 1. Załóżmy, że kolumn rok, miesiąc, dayofmonth, operator i flightnum istnieje w tabeli przykład czy są częścią klucza podstawowego złożonego. Zapytanie będzie wyglądać następująco:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Przeanalizujmy planu dla tego zapytania:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Wynikowy plan jest:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

Wartości w nawiasy kwadratowe są zakres wartości kluczy podstawowych. W takim przypadku zostały usunięte z 2014 roku, miesiąc, 1 i dayofmonth 2 wartości zakresu, ale zezwala wartości dla flightnum uruchamianie z 2 i na (`*`). Ten plan zapytania potwierdza, że klucz podstawowy jest używana, zgodnie z oczekiwaniami.

Następnie utwórz indeks tabeli LOTACH o nazwie `carrier2_idx` znajdujący się w polu operatora tylko. Ten indeks obejmuje również flightdate, tailnum pochodzenia i flightnum jako kolumny objęte usługą, którego dane są także przechowywane w indeksie.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Załóżmy, że chcesz pobrać operatora wraz z flightdate i tailnum, tak jak następujące zapytanie:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

Powinien zostać wyświetlony ten indeks:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

Aby uzyskać pełną listę elementów, które mogą być wyświetlane w wyjaśnić wyniki planu, zobacz sekcję wyjaśnić plany w [Apache Phoenix dostrajanie przewodnik](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Dołącz wydajnie

Ogólnie rzecz biorąc należy unikać sprzężenia, chyba że jednej strony jest mały, szczególnie w przypadku używane kwerendy.

Jeśli to konieczne, możesz to zrobić dużych dołączeń `/*+ USE_SORT_MERGE_JOIN */` wskazówki, ale dużych sprzężenia jest kosztowna operacja za pośrednictwem ogromnej liczby wierszy. Jeśli całkowity rozmiar wszystkich tabel prawo nadwozia1 strony przekroczy ilość dostępnej pamięci, użyj `/*+ NO_STAR_JOIN */` wskazówki.

## <a name="scenarios"></a>Scenariusze

Poniższe wskazówki opisano niektóre typowe wzorce.

### <a name="read-heavy-workloads"></a>Obciążeń intensywnie odczytu

Odczyt silnie przypadki użycia, upewnij się, że używasz indeksów. Ponadto aby zapisać — czas odczytu nakładów pracy, należy rozważyć utworzenie objęte usługą indeksów.

### <a name="write-heavy-workloads"></a>Obciążeń intensywnie zapisu

W przypadku obciążeń intensywnie zapisu gdzie monotonicznie zwiększa się klucz podstawowy utworzyć ziarna zasobników, aby uniknąć hotspotami zapisu, kosztem ogólną przepustowość odczytu z powodu dodatkowych skanowania, potrzebne. Ponadto za pomocą UPSERT zapisu dużej liczby rekordów, wyłącz autozatwierdzania i partii rekordów.

### <a name="bulk-deletes"></a>Usuwa zbiorcze

Podczas usuwania dużych zestawów danych, Włącz autozatwierdzania przed wystawieniem zapytanie DELETE, aby klient nie potrzebuje do zapamiętania klucze wiersza dla wszystkich usuniętych wierszy. Autozatwierdzania uniemożliwi buforowanie wierszy objętych DELETE, więc Phoenix tym usunąć je bezpośrednio na serwerach regionu, bez konieczności zwróceniem do klienta przez klienta.

### <a name="immutable-and-append-only"></a>Niezmienne i tylko Dołącz

Jeśli scenariusz objawach szybkość zapisu na integralność danych, należy rozważyć wyłączenie dziennika kolejnej operacji zapisu podczas tworzenia tabel:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

Aby uzyskać więcej informacji na ten temat oraz innych opcji, zobacz [gramatyki Phoenix](http://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Kolejne kroki

* [Phoenix dostrajanie przewodnik](https://phoenix.apache.org/tuning_guide.html)
* [Indeksów pomocniczych](http://phoenix.apache.org/secondary_indexing.html)
