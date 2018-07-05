---
title: Informacje o starszych wersjach dla usługi Azure HDInsight | Dokumentacja firmy Microsoft
description: Informacje o starszych wersjach i wersje usługi Azure HDInsight.
services: hdinsight
documentationcenter: ''
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: nitinme
ms.openlocfilehash: d1bb9ed33b8f580afd837cafb9cc2a2e37d1cee4
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37438011"
---
# <a name="archived-release-notes-for-azure-hdinsight"></a>Informacje o starszych wersjach dla usługi Azure HDInsight

Dla **najnowszych** aktualizacje wersji usługi Azure HDInsight, zobacz [informacje o wersji w HDInsight](hdinsight-release-notes.md).

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [artykułu versioning HDInsight](hdinsight-component-versioning.md).

## <a name="notes-for-06272018---release-of-new-open-source-versions-adls-gen2-etc-on-hdinsight-36"></a>Informacje o 06 27 2018 - wydaniem nowej wersji "open source" itp. ADLS Gen2 w HDInsight 3.6
Wersja czerwca 2018 r. HDInsight jest znaczący wersji z dużą liczbą nowych aktualizacji i możliwości dla naszych klientów. Przeczytaj ten [wpis](https://azure.microsoft.com/en-us/blog/enterprises-get-deeper-insights-with-hadoop-and-spark-updates-on-azure-hdinsight/) Aby uzyskać więcej informacji.

Poniżej przedstawiono najważniejsze nowości. Aby uzyskać szczegółowe informacje o wersji, przeczytaj usterki usunięte, znanych problemów, itp., [informacje o wersji dla usługi Azure HDInsight](hdinsight-release-notes.md).

- **Aktualizowanie usługi Hadoop i innych projektów typu open-source** — oprócz ponad 1000 poprawki w projektach typu open-source ponad 20, ta aktualizacja zawiera nową wersję (2.3) platformy Spark i Kafka (1.0).
- **Aktualizacja oprogramowania R Server 9.1 do Machine Learning usług 9.3** — w tej wersji, zapewniamy naukowcy i inżynierowie z najlepszymi typu open source rozszerzone o konsolidatorze innowacje i łatwość operacjonalizacji wszystkie dostępne w ich Preferowany język z szybkością platformy Apache Spark. W tej wersji rozszerza możliwości dostępnych w programie R Server dodano obsługę języka Python, co prowadzi do zmiany nazwy klastra z oprogramowania R Server z usługami uczenia Maszynowego. 
- **Obsługa usługi Azure Data Lake Storage Gen2** — HDInsight będzie obsługiwać wersję zapoznawczą usługi Azure Data Lake Storage Gen2. W dostępnych regionów klienci będą mogli wybrać konto usługi ADLS Gen2 jako magazyn dla ich klastrów HDInsight.
- **Aktualizacje pakietu zabezpieczeń przedsiębiorstwa HDInsight (wersja zapoznawcza)** — (wersja zapoznawcza) z punktów końcowych sieci wirtualnej Obsługa obiektów blob platformy Azure Storage, Gen1 usługi ADLS, Cosmos DB i Azure DB. 

## <a name="notes-for-03202018---release-of-spark-22-on-hdinsight-36"></a>Informacje o 03/20/2018 r. — wersja platformy Spark 2.2 w HDInsight 3.6

- Spark 2.2.0 zwiększa stabilność Spark Core, SQL, uczenie Maszynowe i umożliwia przesyłanie strumieniowe ze strukturą stanu wersji ogólnie dostępnej. Spark 2.2.0 jest teraz dostępna w HDInsight 3.6.


## <a name="notes-for-08012017-release-of-hdinsight"></a>Informacje o wersji 2017-08-01 HDInsight

| Stanowisko | Opis | Obszar objęte wpływem  | Typ klastra  | 
| --- | --- | --- | --- | --- |
| Wydanie oprogramowania Microsoft R Server 9.1 na HDInsight |HDInsight teraz obsługuje inicjowania obsługi klastrów programu R Server 9.1 na HDInsight. Aby uzyskać więcej informacji na temat wersji programu Microsoft R Server 9.1, zobacz [ten blog](https://blogs.technet.microsoft.com/dataplatforminsider/2017/04/19/introducing-microsoft-r-server-9-1-release/). |Usługa |R Server |
| HDInsight 3.6 zawiera teraz nowsze wersje stosu Hadoop|<ul><li>Aby uzyskać szczegółową listę zaktualizowanych wersji, zobacz [wersje składników usługi Hadoop jest dostępna w HDInsight](hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions).</li><li>Aby uzyskać listę usterki usunięte w najnowszych wersjach stosu Hadoop, zobacz [informacje dotyczące poprawki Apache](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html).</li><li>Aby uzyskać listę przełomowych zmian między wersjami HDP 2.6.1, (która jest teraz dostępny w HDInsight 3.6), zobacz [ https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html ](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html).</li><li>Aby uzyskać listę znanych problemów występujących w HDP 2.6.1 zobacz [znane problemy dotyczące](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html).</li></ul> |Usługa |Wszyscy |ND |
| Aktualizacje do klastrów Interactive Hive (wersja zapoznawcza) |<ul><li><b>Ulepszenia funkcji.</b> Implementacja magazynu pamięci podręcznej metadanych, redukuje obciążenie sieci wewnętrznej bazy danych SQL przez buforowanie metadanych, który zapewnia lepszą wydajność dla wszystkich operacji metadanych.  To ulepszenie jest teraz domyślnie na wszystkich klastrach Interactive Hive. Aby uzyskać więcej informacji, zobacz [https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520).</li><li><b>Ulepszenia funkcji.</b> Zoptymalizowano ładowanie partycji dynamicznych. Aby uzyskać więcej informacji, zobacz [https://issues.apache.org/jira/browse/HIVE-14204] (https://issues.apache.org/jira/browse/HIVE-14204).</li><li><b>Ulepszenia funkcji.</b> Optymalizacje Configuration for HDInsight w systemie Linux.</li><li><b>Naprawienie usterki.</b> `CredentialProviderFactory$getProviders` nie jest metodą o bezpiecznych wątkach. Ten problem został rozwiązany. Aby uzyskać więcej informacji, zobacz [https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195).</li><li><b>Naprawienie usterki.</b> Wysokie użycie procesora CPU ze sterownikiem WASB `liststatus` skutkuje zły ATS wydajność interfejsu API. Ten problem został rozwiązany. Aby uzyskać więcej informacji, zobacz [https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154).</li></ul> |Usługa |Interactive Hive (wersja zapoznawcza) |
| Aktualizacje do klastrów Hadoop |Zwiększona niezawodność operacji zadania Templeton. Aby uzyskać więcej informacji zobacz [https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947) |Usługa |Hadoop |
| Aktualizacje usługi YARN | HDInsight teraz tworzy 250 GB Ambari bazę danych (bez zwiększania kosztów), co powoduje lepsze doświadczenia klientom. Tej zmiany należy uniemożliwić ATS wprowadzenie wypełniono w górę i prawdopodobnie mają lepszą wydajność. |Usługa |Wszyscy |
| Aktualizacje platformy Spark | Wersja platformy Spark 2.1.1. Aby uzyskać więcej informacji, zobacz [wersji platformy Spark 2.1.1](https://spark.apache.org/releases/spark-release-2-1-1.html). | Usługa | platforma Spark |

  



## <a name="04062017---general-availability-of-hdinsight-36"></a>04/06/2017 — ogólne udostępnienie usług HDInsight 3.6

* W tej wersji usługi Azure HDInsight dodaje wersji 3.6, która jest oparta na HDP 2.6. Dostępne są informacje o wersji HDP 2.6 [tutaj](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) i można go znaleźć więcej informacji na temat wersji HDInsight [tutaj](hdinsight-component-versioning.md). HDInsight 3.6 jest dostępne dla następujących zadań:

    * V2.7.3 usługi Hadoop
    * V1.1.2 bazy danych HBase
    * STORM v1.1.0
    * V2.1.0 platformy Spark
    * V2.1.0 interakcyjną technologią Hive

* **Obsługa widoku Hive w wersji 2.0**. Należy to poprawić środowisko użytkownika Interactive Hive. Aby uzyskać więcej informacji, zobacz [dokumentacji Hortonworks](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Ulepszenia wydajności przy użyciu LLAP programu Hive**. Aby uzyskać więcej informacji, zobacz [dokumentacji Hortonworks](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Nowe funkcje w gałęzi**. Zobacz [dokumentacji Hortonworks](https://hortonworks.com/apache/hive/#section_4).

* **Gałąź obsługi interfejsu wiersza polecenia**: Hive interfejsu wiersza polecenia jest wycofywany, a klientów zachęca się na korzystanie z usługi Beeline zamiast tego. Aby uzyskać więcej informacji, zobacz [dokumentacji oprogramowania Apache](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Aby uzyskać instrukcje na temat korzystania z usługi Beeline z HDInsight, zobacz [korzystać z usługi Beeline przy użyciu usługi HDInsight Hadoop clusters](hadoop/apache-hadoop-use-hive-beeline.md).

* **Nowe funkcje w Apache Phoenix i bazy danych HBase**.
    * Obsługa przydział magazynowania: często używane w środowiskach wielodostępnych, dzięki czemu ograniczoną ilość miejsca na każdej tabeli i na poziomie przestrzeni nazw.
    * Phoenix indeksowania ulepszenia: tworzenie indeksów przyrostowych i ponownej kompilacji/wznowień indeksowanie z wcześniejszych niepowodzeń.
    * Narzędzie integralności danych Phoenix: obsługuje sprawdzanie poprawności schematu, indeksów i innych metadanych.


* **Problem z bazą danych HBase**: podczas uruchamiania przekazywania masowego CSV MapReduce zadania, następująca składnia może spowodować wystąpienie błędu.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Zamiast tego należy użyć następującej składni:

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>02/28/2017 r. — wersja platformy Spark 2.1 w HDInsight 3.6 (wersja zapoznawcza)
* [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html) zwiększa wielu stabilności i użyteczności tworzonych rozwiązań problemów z poprzednimi wersjami. Udostępnia również nowe funkcje, dla wszystkich obciążeń platformy Spark, takich jak Spark Core, SQL, uczenie Maszynowe i przesyłania strumieniowego.
* Przesyłanie strumieniowe ze strukturą pobiera zwiększona skalowalność dzięki obsłudze zdarzeń czasu ze znaków wodnych i Kafka 0.10 łącznika.
* Partycjonowanie Spark SQL są teraz obsługiwane za pomocą nowego mechanizmu obsługi skalowalnych partycji. Zobacz więcej szczegółów [tutaj](http://spark.apache.org/releases/spark-release-2-1-0.html) dotyczącymi uaktualniania.
* Spark 2.1 w usłudze Azure HDInsight 3.6 (wersja zapoznawcza) aktualnie nie obsługuje łączności narzędzia do analizy Biznesowej za pomocą sterownika ODBC.
* Dostęp usługi Azure Data Lake Store z klastrami Spark 2.1 nie jest obsługiwana w tej wersji zapoznawczej.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>11/18/2016 - wersji platformy Spark 2.0.1 w HDInsight 3.5
Platforma Spark 2.0.1 jest teraz dostępne w klastrach Spark (HDInsight w wersji 3.5).

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>11/16/2016 - wersji oprogramowania R Server 9.0 na HDInsight 3.5 (Spark 2.0)
*   Klastry platformy R Server zawierają teraz opcję w dwóch wersjach: R Server 9.0 na HDI 3.5 (Spark 2.0) i R Server 8.0 w usłudze HDI 3.4 (Spark 1.6).
*   Program R Server 9.0 na HDI 3.5 (Spark 2.0) jest oparta na R 3.3.2 i obejmuje nowych funkcji programu ScaleR danych źródła, o nazwie RxHiveData i RxParquetData dotyczące ładowania danych z programów Hive i Parquet bezpośrednio do elementy Spark Dataframe do analizy przez program ScaleR. Aby uzyskać więcej informacji, zobacz wbudowanej pomocy na temat tych funkcji w języku R za pośrednictwem **? RxHiveData** i **? RxParquetData** poleceń.
*   RStudio Server community edition jest zainstalowany domyślnie (opcja rezygnacji z) w bloku konfiguracji klastra jako część przepływu inicjowania obsługi administracyjnej.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>11/09/2016 - wersji platformy Spark w wersji 2.0 w HDInsight
* Klastry Spark 2.0 na HDInsight 3.5 obsługują teraz usługi Livy i Jupyter.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>10 26 / / 2016 - wersji oprogramowania R Server w HDInsight
* Identyfikator URI, aby uzyskać dostęp do węzła krawędzi została zmieniona na **clustername**-ed-ssh.azurehdinsight.net
* Program R Server dla inicjowania obsługi klastra HDInsight została ulepszona.
* Program R Server w HDInsight jest teraz dostępny jako regularne HDInsight "R Server" typ klastra i nie jest już zainstalowany jako osobnych aplikacji HDInsight. Węzła krawędzi i pliki binarne oprogramowania R Server jest teraz udostępniony jako część wdrożenia klastra oprogramowania R Server. Zwiększa to szybkość i niezawodność inicjowania obsługi administracyjnej. Model cenowy dla oprogramowania R Server jest odpowiednio aktualizowana.
* Cena typu klastra oprogramowania R Server jest teraz na podstawie ceny w warstwie standardowa oraz ceny opłata za opcję R Server. Ta zmiana nie ma wpływu na ceny obowiązujące oprogramowania R Server; zmienia się tylko, jak opłaty są prezentowane na rachunku. Wszystkie istniejące klastry platformy R Server w dalszym ciągu działać i szablonów usługi Resource Manager w dalszym ciągu działać do czasu powiadomienie o wycofaniu. **Zaleca się, chociaż można zaktualizować przy użyciu skryptu wdrożenia do użycia nowego szablonu usługi Resource Manager.**






