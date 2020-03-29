---
title: Apache Spark – najważniejsze wskazówki dotyczące usługi Azure HDInsight
description: Poznaj najlepsze wskazówki dotyczące korzystania z platformy Apache Spark w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71106127"
---
# <a name="apache-spark-best-practices"></a>Apache Spark najlepsze rozwiązania

Ten artykuł zawiera różne najlepsze rozwiązania dotyczące korzystania z platformy Apache Spark w usłudze Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Jak uruchomić lub przesłać zadania platformy Spark?

| Opcja | Dokumenty |
|---|---|
| VSCode | [Korzystanie z narzędzi gałęzi spark & dla kodu programu Visual Studio](../hdinsight-for-vscode.md) |
| Notesy programu Jupyter | [Samouczek: ładowanie danych i uruchamianie zapytań w klastrze platformy Apache Spark w usłudze Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Samouczek: Tworzenie aplikacji Apache Spark dla klastra USŁUGI HDInsight za pomocą zestawu narzędzi Platformy Azure dla technologii IntelliJ](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Samouczek: tworzenie aplikacji Scala Maven dla platformy Apache Spark w usłudze HDInsight przy użyciu środowiska IntelliJ](./apache-spark-create-standalone-application.md) |
| Notesy Zeppelin | [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Zdalne składanie zadań z Livy | [Przesyłanie zadań zdalnych do klastra usługi HDInsight Spark przy użyciu interfejsu API REST platformy Apache Spark](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Jak monitorować i debugować zadania platformy Spark?

| Opcja | Dokumenty |
|---|---|
| Zestaw Azure Toolkit for IntelliJ | [Debugowanie zadania iskry awarii za pomocą zestawu narzędzi Azure dla intellij (wersja zapoznawcza)](apache-spark-intellij-tool-failure-debug.md) |
| Zestaw narzędzi platformy Azure dla technologii IntelliJ przez SSH | [Lokalne lub zdalne debugowanie aplikacji platformy Apache Spark w klastrze usługi HDInsight za pomocą zestawu narzędzi Azure Toolkit for IntelliJ](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Zestaw narzędzi platformy Azure dla technologii IntelliJ przez sieć VPN | [Użyj zestawu narzędzi Azure toolkit dla intellij do zdalnego debugowania aplikacji Apache Spark w usłudze HDInsight przez sieć VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Wykres zadań na serwerze historii platformy Spark Apache | [Debugowanie i diagnozowanie aplikacji platformy Apache Spark za pomocą rozszerzonego serwera historii platformy Apache Spark](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Jak sprawić, by moje zadania platformy Spark działały wydajniej?

| Opcja | Dokumenty |
|---|---|
| Pamięć podręczna we/wy | [Zwiększ wydajność obciążeń Platformy Apache Spark przy użyciu pamięci podręcznej we/wy usługi Azure HDInsight (wersja zapoznawcza)](./apache-spark-improve-performance-iocache.md) |
| Opcje konfiguracji | [Optymalizowanie zadań platformy Apache Spark](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Jak połączyć się z innymi usługami platformy Azure?

| Opcja | Dokumenty |
|---|---|
| Ul Apache na HDInsight | [Integracja platformy Apache Spark i ula Apache z łącznikiem magazynu hive](../interactive-query/apache-hive-warehouse-connector.md) |
| Usługa Apache HBase w usłudze HDInsight | [Odczytywanie i zapisywanie danych w bazie danych Apache HBase za pomocą platformy Apache Spark](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka na HDInsight | [Samouczek: używanie strumieni ze strukturą platformy Apache Spark z platformą Kafka w usłudze HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Usługa Azure Cosmos DB: Implementowanie architektury lambda na platformie Azure](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Jakie są moje opcje przechowywania?

| Opcja | Dokumenty |
|---|---|
| Usługa Data Lake Storage 2. generacji | [Korzystanie z usługi Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Usługa Data Lake Storage 1. generacji | [Używanie usługi Data Lake Storage Gen1 w klastrach usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md) |
| Azure Blob Storage | [Korzystanie z usługi Azure Storage w połączeniu z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie ustawień platformy Apache Spark](apache-spark-settings.md)
* [Optymalizuj zadania Apache Spark w hdinsight](apache-spark-perf.md)
