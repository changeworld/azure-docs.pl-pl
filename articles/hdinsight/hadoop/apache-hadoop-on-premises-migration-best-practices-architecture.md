---
title: Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — najlepsze rozwiązania architektury
description: Poznaj architekturę najlepsze rozwiązania dotyczące migrowania lokalnych klastrów Hadoop do usługi Azure HDInsight.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: d1f2b79ff3ae33adb0b6e3ce5a6d96ad38fb1562
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129335"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — najlepsze rozwiązania architektury

Ten artykuł zawiera zalecenia dotyczące architektury systemu Azure HDInsight. Jest częścią serii, która zapewnia najlepsze rozwiązania w celu ułatwienia migrowania lokalnych systemów Apache Hadoop do usługi Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Za pomocą wielu klastrów zoptymalizowanych pod kątem obciążeń

Wiele lokalnych wdrożeń usługi Apache Hadoop składają się z pojedynczego dużego klastra, która obsługuje wiele obciążeń. Ten pojedynczy klaster może być skomplikowane i mogą wymagać naruszeń do poszczególnych usług, aby wszystkie elementy współpracują ze sobą. Migracja lokalnych klastrów Hadoop do usługi Azure HDInsight wymaga zmiany w przypadku zastosowania podejścia.

Usługa Azure klastry HDInsight są przeznaczone dla określonego typu wykorzystanie mocy obliczeniowej. Ponieważ magazynu mogą być współużytkowane przez wiele klastrów, jest możliwe utworzenie wielu klastrów zoptymalizowanych pod kątem obciążeń obliczeń do potrzeb różnych zadań. Każdy typ klastra ma konfiguracją optymalną dla tego określonego obciążenia. W poniższej tabeli wymieniono typy obsługiwanych klastra HDInsight i odpowiedniego obciążenia.

|**Obciążenie**|**Typ klastra HDInsight**|
|---|---|
|Przetwarzanie wsadowe (ETL / ELT)|Hadoop, Spark|
|Magazynowanie danych|Hadoop, Spark, zapytanie interaktywne|
|IoT / przesyłania strumieniowego|Platforma Kafka, Storm, Spark|
|Przetwarzanie transakcyjne NoSQL|HBase|
|Zapytania interakcyjne i szybciej za pomocą pamięci podręcznej w pamięci|Zapytanie interakcyjne|
|Do nauki o danych|ML Services, Spark|

W poniższej tabeli przedstawiono różne metody, które mogą służyć do tworzenia klastra usługi HDInsight.

|**Narzędzie**|**Oparte na przeglądarce**|**Wiersz polecenia**|**Interfejs API REST**|**SDK**|
|---|---|---|---|---|
|[Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Interfejs wiersza polecenia platformy Azure (wersja 1.0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[Zestaw SDK platformy .NET](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)||||X|
|[Zestaw SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||X|
|[Zestaw SDK Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||X|
|[Szablony usługi Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Aby uzyskać więcej informacji, zobacz artykuł [klastra typów w HDInsight](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Używaj przejściowy klastrów na żądanie

Klastry HDInsight nieużywane przejść przez długi czas. Aby zaoszczędzić na kosztach zasobów HDInsight obsługuje przejściowy klastry na żądanie, które zostaną usunięte po pomyślnym ukończeniu obciążenia.

Po usunięciu klastra skojarzone konto magazynu i metadane zewnętrznych nie są usuwane. Klaster można później ponownie utworzyć przy użyciu tego samego konta magazynu i magazynami metadanych.

Usługa Azure Data Factory może służyć do zaplanowania Tworzenie klastrów HDInsight na żądanie. Aby uzyskać więcej informacji, zobacz artykuł [utworzyć klastry platformy Apache Hadoop na żądanie w HDInsight przy użyciu usługi Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Rozdzielenie magazyn od zasobów obliczeniowych

Typowe lokalnych wdrożeń usługi Hadoop na użytek ten sam zestaw maszyn magazyn danych i przetwarzania danych. Ponieważ są one wspólnie przechowywane, zasobów obliczeniowych i magazynu musi być skalowane razem.

W klastrach HDInsight Magazyn musi być wspólnie przechowywane z mocą obliczeniową i mogą albo należeć do usługi Azure storage, usługi Azure Data Lake Storage lub obu. Oddzielenie magazyn od zasobów obliczeniowych ma następujące zalety:

- Udostępnianie danych między klastrami.
- Korzystanie z klastrów przejściowy, ponieważ dane nie są zależne od klastra.
- Mniejsze koszty magazynowania.
- Skalowanie magazynu i obliczeń oddzielnie.
- Replikacja danych między regionami.

Obliczeniowe tworzenia klastrów w pobliżu zasobów konta magazynu w regionie platformy Azure, aby zmniejszyć koszt wydajności związany z oddzielenie zasobów obliczeniowych i magazynowych. Szybkich sieci, należy wydajny dostęp węzłów obliczeniowych do danych wewnątrz usługi Azure storage.

## <a name="use-external-metadata-stores"></a>Korzystanie z zewnętrznych magazynów metadanych


Istnieją dwa główne magazyny metadanych, które działają z klastrami HDInsight: [Apache Hive](https://hive.apache.org/) i [Apache Oozie](https://oozie.apache.org/). Magazyn metadanych Hive jest repozytorium centralnym schematu, które mogą być używane przez silniki przetwarzania danych, w tym usługi Hadoop, Spark, LLAP, Presto i Apache Pig. Magazyn metadanych programu Oozie przechowuje szczegółowe informacje dotyczące planowania i ich stan w toku i zakończonych zadań usługi Hadoop.


HDInsight używa usługi Azure SQL Database dla gałęzi i Oozie magazyny metadanych. Istnieją dwa sposoby konfigurowania metadanych w klastrach HDInsight:

1. Domyślny magazyn metadanych

    - Bez dodatkowych kosztów.
    - Magazyn metadanych jest usuwany, gdy klaster jest usuwany.
    - Magazyn metadanych nie może być współużytkowane przez różnych klastrach.
    - Używa podstawowe bazy danych SQL Azure, która ma ograniczenie do pięciu jednostek DTU.

1. Niestandardowe zewnętrzny Magazyn metadanych

    - Określ zewnętrznej bazy danych SQL Azure jako magazynu metadanych.
    - Klastry można tworzyć i usunięte bez utraty metadane, takie jak szczegóły zadania Oozie schematu programu Hive.
    - Pojedynczy Magazyn metadanych bazy danych mogą być udostępniane w różnych typów klastrów.
    - Magazyn metadanych można przeskalować w górę odpowiednio do potrzeb.
    - Aby uzyskać więcej informacji, zobacz [używać zewnętrznych magazynów metadanych w usłudze Azure HDInsight](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Najlepsze rozwiązania dotyczące Hive magazynu metadanych

Najważniejsze wskazówki magazynu metadanych HDInsight Hive są następujące:

- Użyj niestandardowego zewnętrzny Magazyn metadanych zasoby obliczeniowe oddzielne i metadanych.
- Zacznij od S2 warstwa wystąpieniem usługi Azure SQL, który zapewnia 50 jednostek DTU i 250 GB miejsca do magazynowania. Jeśli widzisz "wąskie gardło", można zwiększać bazy danych.
- Nie udostępniaj magazynu metadanych utworzone dla jednej wersji klastra HDInsight z klastrami w innej wersji. Różne wersje programu Hive używają różnych schematów. Na przykład metadanych nie mogą być udostępniane klastry Hive 1.2 i Hive 2.1.
- Utwórz kopię zapasową niestandardowy Magazyn metadanych okresowo.
- Zachowaj magazynu metadanych i klaster HDInsight, w tym samym regionie.
- Monitorowanie magazynu metadanych dla wydajności i dostępności przy użyciu narzędzi monitorowania bazy danych SQL platformy Azure, takich jak witryny Azure portal lub dzienniki usługi Azure Monitor.
- Wykonaj **ANALIZOWANIE tabeli** polecenia na potrzeby generowania statystyk dla tabel i kolumn. Na przykład `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Najlepsze rozwiązania dotyczące różnych obciążeń

- Należy wziąć pod uwagę przy użyciu funkcji LLAP klastra dla interakcyjnych zapytań Hive z czasem odpowiedzi ulepszone [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) to nowa funkcja programu Hive w wersji 2.0 umożliwiająca wewnątrzpamięciowa pamięć podręczna zapytań. LLAP sprawia, że zapytania programu Hive, które znacznie szybciej, maksymalnie [26 x szybciej niż Hive 1.x w niektórych przypadkach](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Należy wziąć pod uwagę przy użyciu zadań platformy Spark, zamiast zadań Hive.
- Należy wziąć pod uwagę, zastępując zapytania oparte na aparacie impala LLAP zapytania.
- Należy wziąć pod uwagę, zastępując zadań MapReduce zadań platformy Spark.
- Należy wziąć pod uwagę, zastępując partii zadań platformy Spark o małych opóźnieniach, przy użyciu zadań platformy Spark Structured Streaming.
- Rozważ użycie usługi Azure Data Factory (ADF) w wersji 2.0 dla danych aranżacji.
- Do zarządzania klastrem, należy wziąć pod uwagę Ambari.
- Zmień magazyn danych z lokalnym systemem plików HDFS WASB lub ADLS i usług AD FS dla przetwarzania skryptów.
- Należy wziąć pod uwagę przy użyciu platformy Ranger RBAC dla tabel programu Hive i inspekcji.
- Należy rozważyć użycie bazy danych cosmos DB zamiast bazy danych MongoDB lub bazy danych Cassandra.

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj następny artykuł w tej serii:

- [Infrastruktura najlepsze rozwiązania dotyczące lokalnych do migracji usługi Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)
