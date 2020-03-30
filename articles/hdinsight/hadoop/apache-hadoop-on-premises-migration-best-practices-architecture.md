---
title: 'Architektura: Lokalna usługa Apache Hadoop do usługi Azure HDInsight'
description: Poznaj najlepsze rozwiązania dotyczące architektury podczas migracji lokalnych klastrów Hadoop do usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 2d0d5bb871612bc5e16a26eb49808c39661ffb50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934688"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — najważniejsze wskazówki dotyczące architektury

W tym artykule przedstawiono zalecenia dotyczące architektury systemów usługi Azure HDInsight. Jest to część serii, która zapewnia najlepsze rozwiązania ułatwiające migrację lokalnych systemów Apache Hadoop do usługi Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Używanie wielu klastrów zoptymalizowanych pod kątem obciążenia

Wiele lokalnych wdrożeń Apache Hadoop składa się z jednego dużego klastra, który obsługuje wiele obciążeń. Ten pojedynczy klaster może być złożony i może wymagać kompromisów do poszczególnych usług, aby wszystko działało razem. Migrowanie lokalnych klastrów Hadoop do usługi Azure HDInsight wymaga zmiany podejścia.

Klastry usługi Azure HDInsight są przeznaczone do określonego typu użycia obliczeń. Ponieważ magazyn może być współużytkowany przez wiele klastrów, możliwe jest utworzenie wielu klastrów obliczeniowych zoptymalizowanych pod kątem obciążenia, aby zaspokoić potrzeby różnych zadań. Każdy typ klastra ma optymalną konfigurację dla tego określonego obciążenia. W poniższej tabeli wymieniono obsługiwane typy klastrów w umiań HD i odpowiadające im obciążenia.

|Obciążenie|Typ klastra usługi HDInsight|
|---|---|
|Przetwarzanie wsadowe (ETL / ELT)|Hadoop, Iskra|
|Magazynowanie danych|Hadoop, Iskra, Interaktywne zapytanie|
|IoT / Streaming|Kafka, Burza, Iskra|
|Przetwarzanie transakcyjne NoSQL|HBase|
|Interaktywne i szybsze zapytania z buforowania w pamięci|Zapytanie interakcyjne|
|Nauki o danych|Usługi ML, Iskra|

W poniższej tabeli przedstawiono różne metody, które mogą służyć do tworzenia klastra HDInsight.

|Narzędzie|Oparte na przeglądarce|Wiersz polecenia|Interfejs API REST|SDK|
|---|---|---|---|---|
|[Portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Fabryka danych platformy Azure](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Narzędzie interfejsu wiersza polecenia platformy Azure (wersja 1.0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[Curl](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[Zestaw SDK platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)||||X|
|[Python SDK](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||X|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||X|
|[Szablony usługi Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Aby uzyskać więcej informacji, zobacz artykuł [Typy klastrów w pliku HDInsight](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Używanie przejściowych klastrów na żądanie

Klastry HDInsight mogą być nieużywane przez dłuższy czas. Aby zaoszczędzić na kosztach zasobów, usługa HDInsight obsługuje klastry przejściowe na żądanie, które można usunąć po pomyślnym zakończeniu obciążenia.

Po usunięciu klastra skojarzone konto magazynu i zewnętrzne metadane nie są usuwane. Klaster można później ponownie utworzyć przy użyciu tych samych kont magazynu i meta-magazynów.

Usługa Azure Data Factory może służyć do planowania tworzenia klastrów HDInsight na żądanie. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie klastrów Apache Hadoop na żądanie w programie HDInsight przy użyciu usługi Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Oddzielenie magazynu od obliczeń

Typowe wdrożenia w środowisku lokalnym Hadoop używają tego samego zestawu maszyn do przechowywania danych i przetwarzania danych. Ponieważ są one współlokowane, obliczeń i magazynu muszą być skalowane razem.

W klastrach HDInsight magazyn nie musi być współlokowany z obliczeń i może znajdować się w usłudze Azure Storage, usłudze Azure Data Lake Storage lub obu. Oddzielenie magazynu od obliczeń obliczeniowych ma następujące zalety:

- Udostępnianie danych w klastrach.
- Użycie klastrów przejściowych, ponieważ dane nie są zależne od klastra.
- Niższe koszty magazynowania.
- Skalowanie pamięci masowej i obliczeń oddzielnie.
- Replikacja danych w różnych regionach.

Klastry obliczeniowe są tworzone w pobliżu zasobów konta magazynu w regionie platformy Azure w celu zmniejszenia kosztów wydajności oddzielania danych obliczeniowych i magazynu. Szybkie sieci sprawiają, że dostęp do danych w magazynie platformy Azure jest efektywny dla węzłów obliczeniowych.

## <a name="use-external-metadata-stores"></a>Korzystanie z zewnętrznych magazynów metadanych

Istnieją dwa główne metastores, które działają z klastrów HDInsight: [Apache Hive](https://hive.apache.org/) i [Apache Oozie](https://oozie.apache.org/). Magazyn sieci hive jest centralnym repozytorium schematów, które mogą być używane przez aparaty przetwarzania danych, w tym Hadoop, Spark, LLAP, Presto i Apache Pig. W sklepie sieciowym Oozie znajdują się szczegółowe informacje o planowaniu i stanie w toku i ukończonych zadaniach programu Hadoop.

Usługa HDInsight używa usługi Azure SQL Database dla sieci zwłok hive i oozie. Istnieją dwa sposoby konfigurowania magazynu metastore w klastrach HDInsight:

1. Domyślny metasklep

    - Bez dodatkowych kosztów.
    - Magazyn meta jest usuwany po usunięciu klastra.
    - Sklep metastore nie może być współużytkowane przez różne klastry.
    - Używa podstawowej bazy danych SQL platformy Azure, która ma limit pięciu instrukcji DTU.

1. Niestandardowy zewnętrzny magazyn metastore

    - Określ zewnętrzną bazę danych SQL azure jako magazyn meteoger.
    - Klastry można tworzyć i usuwać bez utraty metadanych, w tym szczegóły zadania schematu hive Oozie.
    - Pojedynczy db magazynu metastore mogą być współużytkowane z różnych typów klastrów.
    - W razie potrzeby sklep metastore można skalować w górę.
    - Aby uzyskać więcej informacji, zobacz [Używanie zewnętrznych magazynów metadanych w usłudze Azure HDInsight](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Najważniejsze wskazówki dotyczące hive metastore

Niektóre najlepsze rozwiązania w metastore hive HDInsight są następujące:

- Użyj niestandardowego zewnętrznego magazynu metastore, aby oddzielić zasoby obliczeniowe i metadane.
- Zacznij od wystąpienia SQL platformy Azure warstwy S2, które zapewnia 50 DTU i 250 GB miejsca. Jeśli widzisz wąskie gardło, można skalować bazę danych w górę.
- Nie udostępniaj magazynu metastore utworzonego dla jednej wersji klastra HDInsight z klastrami innej wersji. Różne wersje hive używają różnych schematów. Na przykład nie można udostępnić magazynu metastore zarówno klastrów Hive 1.2, jak i Hive 2.1.
- Okresowo owak owak zapasowej niestandardowego magazynu metastore.
- Przechowuj metasklep i klaster HDInsight w tym samym regionie.
- Monitoruj magazyn meteożyny pod kątem wydajności i dostępności za pomocą narzędzi do monitorowania bazy danych SQL platformy Azure, takich jak witryna Azure portal lub dzienniki usługi Azure Monitor.
- Wykonaj `ANALYZE TABLE` polecenie zgodnie z wymaganiami do generowania statystyk dla tabel i kolumn. Na przykład `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Najważniejsze wskazówki dotyczące różnych obciążeń

- Należy rozważyć użycie klastra LLAP dla interaktywnych zapytań hive z ulepszonym czasem odpowiedzi [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) jest nową funkcją w hive 2.0, która umożliwia buforowanie w pamięci zapytań. LLAP sprawia, że zapytania Hive znacznie szybciej, do [26x szybciej niż Hive 1.x w niektórych przypadkach.](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/)
- Rozważ użycie zadań platformy Spark zamiast zadań hive.
- Rozważ zastąpienie zapytań opartych na impala zapytaniami LLAP.
- Rozważ zastąpienie zadań MapReduce zadaniami platformy Spark.
- Należy rozważyć zastąpienie zadań wsadowych spark o małym opóźnieniu przy użyciu zadań przesyłania strumieniowego strukturyskracyjnego platformy Spark.
- Należy rozważyć użycie usługi Azure Data Factory (ADF) 2.0 do aranżacji danych.
- Należy wziąć pod uwagę Ambari do zarządzania klastrem.
- Zmień magazyn danych z lokalnego systemu plików HDFS na WASB lub ADLS lub ADFS do przetwarzania skryptów.
- Należy rozważyć użycie Ranger RBAC na tabelach hive i inspekcji.
- Rozważ użycie usługi CosmosDB zamiast MongoDB lub Cassandra.

## <a name="next-steps"></a>Następne kroki

Przeczytaj następny artykuł z tej serii:

- [Najważniejsze wskazówki dotyczące infrastruktury dla lokalnej migracji usługi Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)
