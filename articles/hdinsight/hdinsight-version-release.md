---
title: Omówienie usługi HDInsight 4.0 — platforma Azure
description: Porównanie funkcji i ograniczeń usług HDInsight 3.6 i HDInsight 4.0 oraz rekomendacje dotyczące uaktualniania.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 0463e3297bbb2fda50adfeefaa89f0a7a3ef8b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72901533"
---
# <a name="azure-hdinsight-40-overview"></a>Omówienie usługi Azure HDInsight 4.0

Azure HDInsight jest jedną z najpopularniejszych usług używanych przez klientów korporacyjnych na potrzeby analizy w narzędziach typu open source Apache Hadoop i Apache Spark na platformie Azure. HDInsight 4.0 to rozkład w chmurze komponentów Apache Hadoop. Ten artykuł zawiera informacje o najnowszym wydaniu usługi Azure HDInsight i sposobie jej uaktualniania.

## <a name="whats-new-in-hdinsight-40"></a>Co nowego w HDInsight 4.0?

### <a name="apache-hive-30-and-llap"></a>Apache Hive 3.0 i LLAP

Infrastruktura Apache Hive z przetwarzaniem analitycznym o małym opóźnieniu (LLAP) używa serwerów trwałych zapytań i buforowania w pamięci w celu szybkiego dostarczania wyników zapytań SQL dotyczących danych w zdalnym magazynie w chmurze. Hive LLAP wykorzystuje zestaw trwałych demonów, które wykonują fragmenty zapytań Hive. Wykonywanie zapytań z przetwarzaniem LLAP odbywa się podobnie jak w infrastrukturze Hive bez funkcji LLAP z tą różnicą, że zadania procesów roboczych działają wewnątrz demonów LLAP zamiast w kontenerach.

Przetwarzanie Hive LLAP daje następujące korzyści:

* Możliwość wykonywania szczegółowych analiz SQL z wykorzystaniem elementów, takich jak złożone sprzężenia, podzapytania, funkcji obsługi okien, sortowanie, funkcje zdefiniowane przez użytkownika i złożone agregacje, bez obniżania oczekiwanego poziomu wydajności i skalowalności.

* Możliwość wykonywania interaktywnych zapytań względem danych w tym samym magazynie, w którym dane są przygotowywane, co eliminuje konieczność przenoszenia danych z magazynu do innego aparatu na potrzeby przetwarzania analitycznego.

* Buforowanie wyników zapytań umożliwia ponowne wykorzystanie wcześniej obliczanych wyników zapytań, co pozwala zaoszczędzić czas i zasoby używane podczas uruchamiania zadań klastra wymaganych dla zapytania.

### <a name="hive-dynamic-materialized-views"></a>Dynamiczne zmaterializowane widoki Hive

Infrastruktura Hive obsługuje obecnie dynamiczne zmaterializowane widoki lub wstępne obliczanie istotnych podsumowań, które są używane do przyspieszania przetwarzania zapytań w hurtowni danych. Zmaterializowane widoki mogą być przechowywane w sposób natywny w infrastrukturze Hive i mogą bezproblemowo korzystać z przyspieszenia LLAP.

### <a name="hive-transactional-tables"></a>Tabele transakcyjne Hive

Usługa HDI 4.0 zawiera infrastrukturę Apache Hive 3, która wymaga zgodności pod względem niepodzielności, spójności, izolacji i trwałości (ACID) dla tabel transakcyjnych rezydujących w magazynie Hive. Tabele zgodne ze standardem ACID oraz przechowywane w nich dane są dostępne i zarządzane za pośrednictwem programu Hive. Dane przechowywane w tabelach CRUD, które zapewniają możliwość tworzenia, pobierania, aktualizacji i usuwania, muszą być w formacie pliku ORC (Optimized Row Column), ale tabele umożliwiające tylko wstawianie danych obsługują wszystkie formaty plików.

* Standard ACID w wersji 2 wprowadza ulepszenia wydajności zarówno w formacie magazynu, jak i aparatu wykonywania.

* Standard ACID jest domyślnie włączony, co umożliwia pełną obsługę aktualizacji danych.

* Ulepszone funkcje ACID pozwalają na aktualizowanie i usuwanie na poziomie wiersza.

* Nie występuje negatywny wpływ na wydajność.

* Nie jest wymagana obsługa zasobników.

* Platforma Spark może odczytywać i zapisywać dane w tabelach Hive ACID za pośrednictwem łącznika magazynu Hive.

Dowiedz się więcej o infrastrukturze [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

Platforma Apache Spark pobiera możliwe do zaktualizowania tabele i transakcje ACID za pomocą łącznika magazynu Hive. Łącznik magazynu Hive umożliwia rejestrowanie tabel transakcyjnych Hive jako tabel zewnętrznych na platformie Spark w celu uzyskania dostępu do wszystkich funkcji transakcyjnych. Poprzednie wersje obsługiwały tylko manipulowanie partycjami tabel. Łącznik magazynu Hive obsługuje również przesyłanie strumieniowe elementów DataFrame na potrzeby strumieniowego odczytu i zapisu w tabelach transakcyjnych oraz przesyłanie strumieniowe tabel Hive z platformy Spark.

Funkcje wykonawcze platformy Spark mogą łączyć się bezpośrednio z demonami Hive LLAP na potrzeby pobierania i aktualizowania danych w sposób transakcyjny, co umożliwia zachowanie kontroli nad danymi przez infrastrukturę Hive.

Platforma Apache Spark w usłudze HDInsight 4.0 obsługuje następujące scenariusze:

* Uruchamianie szkolenia modelu uczenia maszynowego z wykorzystaniem tej samej tabeli transakcyjnej, która jest używana na potrzeby raportowania.
* Wykorzystanie transakcji ACID w celu bezpiecznego dodania kolumn z modelu Spark ML do tabeli Hive.
* Uruchamianie zadania przesyłania strumieniowego platformy Spark podczas zestawiania zmian z tabeli przesyłania strumieniowego Hive.
* Tworzenie plików ORC bezpośrednio na podstawie zadania przesyłania strumieniowego ze strukturą platformy Spark.

Nie musisz się już martwić przypadkowymi próbami uzyskania dostępu do tabel transakcyjnych Hive bezpośrednio z platformy Spark, co prowadziło do niespójności wyników, duplikowania danych lub ich uszkodzenia. W umiań HDInsight 4.0 tabele Spark i tabele Hive są przechowywane w oddzielnych sklepach. Za pomocą łącznika magazynu danych Hive możesz jawnie zarejestrować tabele transakcyjne programu Hive jako tabele zewnętrzne platformy Spark.

Dowiedz się więcej o platformie [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apache Oozie

Usługa HDI 4.0 zawiera system Apache Oozie 4.3.1 z następującymi zmianami:

* W systemie Oozie nie można już uruchamiać akcji programu Hive. Usunięto interfejs Hive CLI i zastąpiono go usługą BeeLine.

* Niepożądane zależności można wykluczyć z biblioteki udziałów, umieszczając wzorzec wykluczania w pliku **job.properties**.

Dowiedz się więcej o systemie [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Jak uaktualnić do HDInsight 4.0

Podobnie jak w przypadku każdego głównego wydania, przed zaimplementowaniem najnowszej wersji w środowisku produkcyjnym należy dokładnie przetestować składniki. HdInsight 4.0 jest dostępny, aby rozpocząć proces aktualizacji, ale HDInsight 3.6 jest domyślną opcją, aby zapobiec przypadkowym wpadek.

Nie ma obsługiwanej ścieżki uaktualnienia z poprzednich wersji programu HDInsight do programu HDInsight 4.0. Ponieważ formaty danych metastore i obiektów blob uległy zmianie, program HDInsight 4.0 nie jest zgodny z poprzednimi wersjami. Ważne jest, aby nowe środowisko HDInsight 4.0 było oddzielone od bieżącego środowiska produkcyjnego. Jeśli wdrożysz hdinsight 4.0 w bieżącym środowisku, metasklep zostanie uaktualniony i nie będzie można go cofnąć.  

## <a name="limitations"></a>Ograniczenia

* HdInsight 4.0 nie obsługuje MapReduce dla Apache Hive. Zamiast tego należy użyć środowiska Apache Tez. Dowiedz się więcej o środowisku [Apache Tez](https://tez.apache.org/).
* HDInsight 4.0 nie obsługuje Apache Storm.
* Widok gałęzi nie jest już dostępny w umiań HDInsight 4.0.
* Interpreter powłoki w Apache Zeppelin nie jest obsługiwany w klastrach Spark i Interactive Query.
* Nie można *wyłączyć* funkcji LLAP w klastrze Spark LLAP. Można tylko wyłączyć funkcję LLAP.
* Usługa Azure Data Lake Storage Gen2 nie pozwala zapisywać notesów Juypter w klastrze Spark.

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja usługi Azure HDInsight](index.yml)
* [Wersji](hdinsight-release-notes.md)
