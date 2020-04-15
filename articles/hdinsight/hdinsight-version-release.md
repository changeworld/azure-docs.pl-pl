---
title: Omówienie usługi HDInsight 4.0 — platforma Azure
description: Porównanie funkcji i ograniczeń usług HDInsight 3.6 i HDInsight 4.0 oraz rekomendacje dotyczące uaktualniania.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d0fd9999abc4a67ded0f66977e1a3ba5310c87be
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383026"
---
# <a name="azure-hdinsight-40-overview"></a>Omówienie usługi Azure HDInsight 4.0

Usługa Azure HDInsight jest jedną z najpopularniejszych usług wśród klientów korporacyjnych dla Apache Hadoop i Apache Spark. HDInsight 4.0 to rozkład w chmurze komponentów Apache Hadoop. Ten artykuł zawiera informacje o najnowszym wydaniu usługi Azure HDInsight i sposobie jej uaktualniania.

## <a name="whats-new-in-hdinsight-40"></a>Co nowego w HDInsight 4.0?

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Apache Hive 3.0 i przetwarzanie analityczne o małych opóźnieniach

Apache Hive przetwarzania analitycznego o małym opóźnieniu (LLAP) używa serwerów trwałych zapytań i buforowania w pamięci. Ten proces zapewnia szybkie wyniki zapytań SQL na temat danych w zdalnym magazynie w chmurze. Ule llap używa zestawu trwałych demonów, które wykonują fragmenty zapytań hive. Wykonywanie zapytań z przetwarzaniem LLAP odbywa się podobnie jak w infrastrukturze Hive bez funkcji LLAP z tą różnicą, że zadania procesów roboczych działają wewnątrz demonów LLAP zamiast w kontenerach.

Przetwarzanie Hive LLAP daje następujące korzyści:

* Możliwość wykonywania głębokiej analizy SQL bez utraty wydajności i zdolności adaptacyjnych. Takie jak sprzężenia złożone, podksy, funkcje okien, sortowanie, funkcje zdefiniowane przez użytkownika i złożone agregacje.

* Możliwość wykonywania interaktywnych zapytań względem danych w tym samym magazynie, w którym dane są przygotowywane, co eliminuje konieczność przenoszenia danych z magazynu do innego aparatu na potrzeby przetwarzania analitycznego.

* Wyniki kwerendy buforowania umożliwia wcześniej obliczone wyniki kwerendy do ponownego korzystania. Ta pamięć podręczna oszczędza czas i zasoby wydane na uruchamianie zadań klastra wymaganych dla kwerendy.

### <a name="hive-dynamic-materialized-views"></a>Dynamiczne zmaterializowane widoki Hive

Gałąź obsługuje teraz dynamiczne zmaterializowane widoki lub wstępne obliczanie odpowiednich podsumowań. Widoki przyspieszają przetwarzanie zapytań w magazynach danych. Zmaterializowane widoki mogą być przechowywane w sposób natywny w infrastrukturze Hive i mogą bezproblemowo korzystać z przyspieszenia LLAP.

### <a name="hive-transactional-tables"></a>Tabele transakcyjne Hive

HDI 4.0 zawiera Apache Hive 3. Gałąź 3 wymaga niepodzielności, spójności, izolacji i zgodności z trwałością dla tabel transakcyjnych, które mieszkają w magazynie hive. Tabele zgodne ze standardem ACID oraz przechowywane w nich dane są dostępne i zarządzane za pośrednictwem programu Hive. Dane w tabelach tworzenia, pobierania, aktualizowania i usuwania (CRUD) muszą być w formacie pliku zoptymalizowana kolumna wiersza (ORC). Tabele tylko do wstawiania obsługują wszystkie formaty plików.

* Standard ACID w wersji 2 wprowadza ulepszenia wydajności zarówno w formacie magazynu, jak i aparatu wykonywania.

* Standard ACID jest domyślnie włączony, co umożliwia pełną obsługę aktualizacji danych.

* Ulepszone funkcje ACID pozwalają na aktualizowanie i usuwanie na poziomie wiersza.

* Nie występuje negatywny wpływ na wydajność.

* Nie jest wymagana obsługa zasobników.

* Platforma Spark może odczytywać i zapisywać dane w tabelach Hive ACID za pośrednictwem łącznika magazynu Hive.

Dowiedz się więcej o infrastrukturze [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

Platforma Apache Spark pobiera możliwe do zaktualizowania tabele i transakcje ACID za pomocą łącznika magazynu Hive. Łącznik magazynu Hive umożliwia rejestrowanie tabel transakcyjnych Hive jako tabel zewnętrznych na platformie Spark w celu uzyskania dostępu do wszystkich funkcji transakcyjnych. Poprzednie wersje obsługiwały tylko manipulowanie partycjami tabel. Łącznik magazynu hive obsługuje również ramki danych przesyłania strumieniowego.  Ten proces strumieni odczytuje i zapisuje do transakcyjnych i przesyłania strumieniowego tabel hive z platformy Spark.

Funkcje wykonawcze platformy Spark mogą łączyć się bezpośrednio z demonami Hive LLAP na potrzeby pobierania i aktualizowania danych w sposób transakcyjny, co umożliwia zachowanie kontroli nad danymi przez infrastrukturę Hive.

Platforma Apache Spark w usłudze HDInsight 4.0 obsługuje następujące scenariusze:

* Uruchamianie szkolenia modelu uczenia maszynowego z wykorzystaniem tej samej tabeli transakcyjnej, która jest używana na potrzeby raportowania.
* Wykorzystanie transakcji ACID w celu bezpiecznego dodania kolumn z modelu Spark ML do tabeli Hive.
* Uruchamianie zadania przesyłania strumieniowego platformy Spark podczas zestawiania zmian z tabeli przesyłania strumieniowego Hive.
* Tworzenie plików ORC bezpośrednio na podstawie zadania przesyłania strumieniowego ze strukturą platformy Spark.

Nie musisz już martwić się o przypadkowe próby uzyskania dostępu do tabel transakcyjnych hive bezpośrednio z platformy Spark. W rezultacie niespójne wyniki, zduplikowane dane lub uszkodzenie danych. W umiań HDInsight 4.0 tabele Spark i tabele Hive są przechowywane w oddzielnych sklepach. Za pomocą łącznika magazynu danych Hive możesz jawnie zarejestrować tabele transakcyjne programu Hive jako tabele zewnętrzne platformy Spark.

Dowiedz się więcej o platformie [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apache Oozie

Usługa HDI 4.0 zawiera system Apache Oozie 4.3.1 z następującymi zmianami:

* W systemie Oozie nie można już uruchamiać akcji programu Hive. Usunięto interfejs Hive CLI i zastąpiono go usługą BeeLine.

* Niepożądane zależności można wykluczyć z biblioteki udziałów, umieszczając wzorzec wykluczania w pliku **job.properties**.

Dowiedz się więcej o systemie [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Jak uaktualnić do HDInsight 4.0

Dokładnie przetestuj składniki przed wdrożeniem najnowszej wersji w środowisku produkcyjnym. Program HDInsight 4.0 jest dostępny do rozpoczęcia procesu uaktualniania. HDInsight 3.6 jest domyślną opcją zapobiegawczym przypadkowym wpadkami.

Nie ma obsługiwanej ścieżki uaktualnienia z poprzednich wersji programu HDInsight do programu HDInsight 4.0. Ponieważ formaty danych metastore i obiektów blob uległy zmianie, wersja 4.0 nie jest zgodna z poprzednimi wersjami. Ważne jest, aby nowe środowisko HDInsight 4.0 było oddzielone od bieżącego środowiska produkcyjnego. Jeśli wdrożysz hdinsight 4.0 w bieżącym środowisku, metasklep zostanie trwale uaktualniony.  

## <a name="limitations"></a>Ograniczenia

* HdInsight 4.0 nie obsługuje MapReduce dla Apache Hive. Zamiast tego należy użyć środowiska Apache Tez. Dowiedz się więcej o środowisku [Apache Tez](https://tez.apache.org/).
* HDInsight 4.0 nie obsługuje Apache Storm.
* Widok gałęzi nie jest już dostępny w umiań HDInsight 4.0.
* Interpreter powłoki w Apache Zeppelin nie jest obsługiwany w klastrach Spark i Interactive Query.
* Nie można *wyłączyć* funkcji LLAP w klastrze Spark LLAP. Możesz tylko wyłączyć LLAP.
* Usługa Azure Data Lake Storage Gen2 nie może zapisać notesów usługi Jupyter w klastrze platformy Spark.

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja usługi Azure HDInsight](index.yml)
* [Wersji](hdinsight-release-notes.md)
