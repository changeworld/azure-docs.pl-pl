---
title: Informacje o wersji programu Składniki platformy Hadoop w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Informacje o najnowszej wersji i wersje składników platformy Hadoop dla usługi Azure HDInsight. Uzyskać porad programistycznych i szczegóły Spark, R Server, Hive i inne.
services: hdinsight
documentationcenter: ''
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.assetid: a363e5f6-dd75-476a-87fa-46beb480c1fe
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: nitinme
ms.openlocfilehash: 17ba4af006587dd1d34f61a0f5f77cbcd768fcf8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Informacje o wersji dla składników platformy Hadoop w usłudze Azure HDInsight

Ten artykuł zawiera informacje na temat **najnowszych** Azure HDInsight wersji aktualizacji. Aby uzyskać informacje we wcześniejszych wersjach, zobacz [HDInsight Release Notes archiwum](hdinsight-release-notes-archive.md).

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [artykułu przechowywanie wersji usługi HDInsight](hdinsight-component-versioning.md).

## <a name="notes-for-03202018---release-of-spark-22-on-hdinsight-36"></a>Informacje o 03/20/2018 - wersji 2.2 platformy Spark w usłudze HDInsight 3,6

- Spark 2.2.0 zwiększa stabilność Spark Core, SQL, ML i oferuje strukturalnych przesyłania strumieniowego GA stanu. Platforma Spark 2.2.0 jest teraz dostępna w usłudze HDInsight 3,6.


## <a name="notes-for-08012017-release-of-hdinsight"></a>Informacje o wersji 2017-08/01 usługi HDInsight

| Stanowisko | Opis | Wpływ na obszaru  | Typ klastra  | 
| --- | --- | --- | --- | --- |
| Wersja programu Microsoft R Server 9.1 w usłudze HDInsight |HDInsight obsługuje teraz inicjowania obsługi klastrów 9.1 serwerem R w usłudze HDInsight. Aby uzyskać więcej informacji o wersji programu Microsoft R Server 9.1, zobacz [ten blog](https://blogs.technet.microsoft.com/dataplatforminsider/2017/04/19/introducing-microsoft-r-server-9-1-release/). |Usługa |R Server |
| 3,6 HDInsight zawiera teraz nowsze wersje stosem platformy Hadoop|<ul><li>Aby uzyskać szczegółową listę zaktualizowanych wersji, zobacz [Hadoop wersji składników dostępnych w usłudze HDInsight](hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions).</li><li>Lista błędów stałej w najnowszych wersjach stosem platformy Hadoop, zobacz [informacji poprawki Apache](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html).</li><li>Aby uzyskać listę fundamentalne zmiany między HDP 2.6.1 (która jest teraz dostępna w usłudze HDInsight 3,6), zobacz [ https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html ](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html).</li><li>Aby uzyskać listę znanych problemów dotyczących HDP 2.6.1 zobacz [znane problemy](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html).</li></ul> |Usługa |Wszyscy |ND |
| Aktualizacje klastrów interakcyjne gałęzi rejestru (wersja zapoznawcza) |<ul><li><b>Ulepszenia funkcji.</b> Implementacja na potrzeby magazynu metadanych pamięci podręcznej, która zmniejsza obciążenie wewnętrznej bazy danych SQL przez buforowanie metadanych i zwiększa wydajność w przypadku wszystkich operacji na metadanych.  To ulepszenie jest teraz domyślnie na wszystkich klastrach Hive interaktywnego. Aby uzyskać więcej informacji, zobacz [https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520).</li><li><b>Ulepszenia funkcji.</b> Ładowanie partycji dynamicznych jest zoptymalizowany. Aby uzyskać więcej informacji, zobacz [https://issues.apache.org/jira/browse/HIVE-14204] (https://issues.apache.org/jira/browse/HIVE-14204).</li><li><b>Ulepszenia funkcji.</b> Optymalizacje konfiguracji dla usługi HDInsight w systemie Linux.</li><li><b>Poprawka błędu.</b> `CredentialProviderFactory$getProviders` nie jest bezpieczne wątkowo. Ten problem teraz zostanie usunięty. Aby uzyskać więcej informacji, zobacz [https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195).</li><li><b>Poprawka błędu.</b> Wysokie użycie procesora CPU ze sterownikiem WASB `liststatus` wynikające z nieprawidłowych wydajności ATS interfejsu API. Ten problem teraz zostanie usunięty. Aby uzyskać więcej informacji, zobacz [https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154).</li></ul> |Usługa |Gałąź interakcyjne (wersja zapoznawcza) |
| Aktualizacje klastrów platformy Hadoop |Zwiększona niezawodność operacji zadania Templeton. Aby uzyskać więcej informacji zobacz [https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947) |Usługa |Hadoop |
| YARN aktualizacji | HDInsight teraz tworzy 250 GB Ambari bazę danych (bez zwiększania kosztów), co powoduje lepsze środowisko dla klientów. Tej zmiany należy uniemożliwić ATS pobierania wypełnione i prawdopodobnie mają lepszą wydajność. |Usługa |Wszyscy |
| Platforma Spark aktualizacji | Wersja platformy Spark 2.1.1. Aby uzyskać więcej informacji, zobacz [wersji Spark 2.1.1](https://spark.apache.org/releases/spark-release-2-1-1.html). | Usługa | platforma Spark |

  



## <a name="04062017---general-availability-of-hdinsight-36"></a>04/06/2017 - ogólnej dostępności 3,6 HDInsight

* W tej wersji usługi Azure HDInsight dodaje 3,6, wersji, która jest oparta na HDP 2.6. Dostępne są informacje o wersji HDP 2.6 [tutaj](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) i można go znaleźć więcej informacji na temat wersji usługi HDInsight [tutaj](hdinsight-component-versioning.md). HDInsight 3,6 jest dostępne dla następujących zadań:

    * Hadoop v2.7.3
    * HBase v1.1.2
    * STORM v1.1.0
    * Spark v2.1.0
    * V2.1.0 interakcyjne gałęzi

* **Obsługa widoku Hive 2.0**. Powinno to zwiększyć czynności użytkownika dla interaktywnego Hive. Aby uzyskać więcej informacji, zobacz [dokumentacji Hortonworks](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Ulepszenia wydajności z Hive LLAP**. Aby uzyskać więcej informacji, zobacz [dokumentacji Hortonworks](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Nowe funkcje w gałęzi**. Zobacz [dokumentacji Hortonworks](https://hortonworks.com/apache/hive/#section_4).

* **Hive amortyzacja CLI**: Hive interfejsu wiersza polecenia jest przestarzałe i zamiast tego użyć Beeline zachęcamy klientów. Aby uzyskać więcej informacji, zobacz [dokumentację Apache](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Aby uzyskać instrukcje dotyczące sposobu używania Beeline z usługą HDInsight, zobacz [Beeline korzystać z usługi HDInsight Hadoop clusters](hadoop/apache-hadoop-use-hive-beeline.md).

* **Nowe funkcje programu Apache Phoenix i HBase**.
    * Obsługa przydziałów magazynowania: często używane w środowiskach wielodostępnych, dzięki czemu ograniczoną ilością miejsca na na tabelę i na poziomie przestrzeni nazw.
    * Phoenix indeksowania ulepszeń: tworzenie indeksów przyrostowych i Odbuduj/wznowień indeksowania z wcześniejszych niepowodzeń.
    * Narzędzie integralności danych Phoenix: obsługuje sprawdzanie poprawności schematu, indeksu i innych metadanych.


* **Problem z bazy danych HBase**: podczas przekazywania CSV w zbiorczego MapReduce uruchamiania zadania, następującej składni może spowodować błąd.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Zamiast tego należy użyć następującej składni:

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>02/28/2017 - wersji 2.1 platformy Spark w usłudze HDInsight 3,6 (wersja zapoznawcza)
* [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html) zwiększa wiele problemów stabilność i użyteczność z poprzednimi wersjami. Stwarza również nowe funkcje we wszystkich obciążeń Spark, takich jak Spark Core, SQL ML i przesyłania strumieniowego.
* Strukturalne Streaming pobiera zwiększona skalowalność z obsługą limitów czasu zdarzenia i Kafka 0.10 łącznika.
* Partycjonowanie Spark SQL jest teraz obsługiwane przy użyciu nowego mechanizmu obsługi skalowalne partycji. Zobacz szczegółowe [tutaj](http://spark.apache.org/releases/spark-release-2-1-0.html) dotyczącymi uaktualniania.
* 2.1 Spark on Azure HDInsight 3,6 Preview aktualnie nie obsługuje łączności narzędzia BI za pomocą sterownika ODBC.
* Dostęp do usługi Azure Data Lake Store z klastrami Spark 2.1 nie jest obsługiwana w tej wersji zapoznawczej.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>11 18/2016 — wersja 2.0.1 platformy Spark w usłudze HDInsight 3.5
Platforma Spark 2.0.1 jest teraz dostępna w klastrze Spark (usługi HDInsight w wersji 3.5).

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>11 16/2016 — wersja R Server 9.0 w usłudze HDInsight 3.5 (Spark 2.0)
*   Klastry serwerów R zawierają teraz opcję dwie wersje: R Server 9.0 HDI 3.5 (Spark 2.0) i R Server 8.0 na HDI 3.4 (Spark 1.6).
*   R Server 9.0 na HDI 3.5 (Spark 2.0) jest oparty na R 3.3.2 i zawiera nowe funkcje ScaleR danych źródła, nazywanych RxHiveData i RxParquetData ładowania danych z Hive i Parquet bezpośrednio do DataFrames Spark do analizy przez ScaleR. Aby uzyskać więcej informacji, zobacz Pomoc na temat tych funkcji w R za pomocą wbudowanej **? RxHiveData** i **? RxParquetData** polecenia.
*   Serwer programu RStudio community edition jest teraz zainstalowany domyślnie (z opcją rezygnacji) w bloku konfiguracji klastra jako część przepływu inicjowania obsługi administracyjnej.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>11/09/2016 — wersja 2.0 platformy Spark w usłudze HDInsight
* Klastry Spark 2.0 na HDInsight 3.5 obsługuje teraz usług Livy i Jupyter.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>10 26/2016 — wersja R Server w usłudze HDInsight
* Identyfikator URI dla dostępu do węzła krawędzi zmieniła się na **clustername**-ed-ssh.azurehdinsight.net
* Usprawniono R Server w inicjowania obsługi klastra usługi HDInsight.
* Serwer R w usłudze HDInsight jest teraz dostępna jako regularne HDInsight "R Server" typ klastra i nie jest już zainstalowana jako osobne aplikacji usługi HDInsight. Węzeł krawędzi i plików binarnych serwera R są teraz udostępniony jako część wdrożenia klastra R Server. Zwiększa to szybkość i niezawodność inicjowania obsługi administracyjnej. Modelu cenowego dla R Server jest odpowiednio aktualizowany.
* R Server klastra typu cena zależy teraz na cen warstwy standardowa plus cen przeciążenia serwera R. Ta zmiana nie wpływa na efektywne cennik serwera R. zmienia tylko jak opłaty są prezentowane w zestawieniu. Wszystkich istniejących klastrów R Server w dalszym ciągu działać i szablony Menedżera zasobów w dalszym ciągu działać do czasu powiadomienie o wycofaniu. **Zaleca się do aktualizacji wdrożeń inicjowanych przez skrypty można użyć nowego szablonu usługi Resource Manager.**






