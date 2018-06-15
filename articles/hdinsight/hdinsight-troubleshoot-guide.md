---
title: Usługa Azure HDInsight wskazówki dotyczące rozwiązywania problemów | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z obciążeń Hadoop za pomocą usługi Azure HDInsight. Dokumentacja krok po kroku przedstawiono sposób HDInsight umożliwia rozwiązywanie typowych problemów z Hive, Spark, YARN, HBase, system plików HDFS i Storm.
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: ''
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: 785884b7fd03339d195fbd7853b17300effecc6a
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2017
ms.locfileid: "23984731"
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>Rozwiązywanie problemów przy użyciu usługi Azure HDInsight

| Apache obciążenia | Najczęściej zadawane pytania |
|---|---|
|![HBase](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[Rozwiązywanie problemów z bazy danych HBase](hbase/apache-troubleshoot-hbase.md)|<br>[Jak uruchomić raporty polecenie hbck z wielu regionach nieprzypisane?](hbase/apache-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[Jak rozwiązać problemy z limitu czasu, korzystając z polecenia hbck przypisania region?](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[Jak I Wymuś. Wyłącz tryb awaryjny systemu plików HDFS w klastrze?](hbase/apache-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)<br><br>[Jak rozwiązać łączności JDBC lub SQLLine problemy z Apache Phoenix?](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[Co powoduje, że serwer główny uruchomienie?](hbase/apache-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[Na serwerze, na region, co powoduje niepowodzenie ponownego uruchomienia?](hbase/apache-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[Rozwiązywanie problemów z systemem plików HDFS](hdinsight-troubleshoot-hdfs.md)|<br>[Jak uzyskać dostęp do lokalnego systemu plików HDFS z wewnątrz klastra?](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Jak I Wymuś. Wyłącz tryb awaryjny systemu plików HDFS w klastrze?](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![Hive](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[Rozwiązywanie problemów z bazy danych HBase](hdinsight-troubleshoot-hive.md)|<br>[Jak wyeksportować na potrzeby magazynu metadanych Hive i zaimportuj go w innym klastrze?](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Jak znaleźć gałęzi dzienniki w klastrze?](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[Jak uruchomić powłokę programu Hive o określonej konfiguracji w klastrze?](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Jak analizować dane Tez DAG na ścieżkę krytyczną klastra?](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[W jaki sposób pobierać dane Tez DAG z klastra?](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![Spark](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Rozwiązywanie problemów z Spark](hdinsight-troubleshoot-SPARK.md)|<br>[Jak skonfigurować aplikacji Spark przy użyciu Ambari w klastrach?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-ambari-on-clusters)<br><br>[Jak skonfigurować aplikacji Spark przy użyciu notesu Jupyter w klastrze?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[Jak skonfigurować aplikacji Spark przy użyciu programu Livy w klastrach?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-livy-on-clusters)<br><br>[Jak skonfigurować Spark, przesłać spark aplikacji przy użyciu w klastrach?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters)<br><br>[Dlaczego Spark wyjątek OutOfMemoryError aplikacji?](spark/apache-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![STORM](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[Rozwiązywanie problemów z Storm](hdinsight-troubleshoot-STORM.md)|<br>[Jak uzyskać dostęp do interfejsu użytkownika platformy Storm w klastrze?](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Jak przenieść informacje dotyczące punktu kontrolnego spout Centrum zdarzeń Storm od jednego topologii do innego](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Jak znaleźć plików binarnych systemu Storm w klastrze?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Określanie topologii wdrożenia klastra Storm?](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Jak znaleźć plików binarnych spout Centrum zdarzeń Storm do tworzenia aplikacji?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)<br><br>[Jak znaleźć pliki konfiguracji narzędzia Log4J Storm w klastrze?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[Rozwiązywanie problemów z YARN](hdinsight-troubleshoot-YARN.md)|<br>[Jak utworzyć nową kolejkę YARN w klastrze?](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[W jaki sposób pobierać dzienników YARN z klastra?](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Zasoby dotyczące rozwiązywania problemów w usłudze HDInsight

| Aby uzyskać informacje na temat | Zobacz następujące artykuły |
| --- | --- |
| HDInsight w systemie Linux i optymalizacji | - [Informacje dotyczące korzystania z usługi HDInsight w systemie Linux](hdinsight-hadoop-linux-information.md)<br>- [Pamięć Hadoop i rozwiązywanie problemów z wydajnością](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Wydajność zapytań hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Dzienniki i zrzuty | - [Dzienniki aplikacji Hadoop YARN dostępu w systemie Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Włącz zrzuty stosu dla usługi Hadoop w systemie Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [Analizowanie dzienników usługi HDInsight](hdinsight-debug-jobs.md)|
| Błędy | - [Zrozumienia i rozwiązania błędów usługi WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [Ustawienia gałąź, aby naprawić błąd zdarzeń OutofMemory](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Narzędzia | - [Debugowanie aplikacji Tez zadania przy użyciu widoków Ambari](hdinsight-debug-ambari-tez-view.md)<br>- [Optymalizacja zapytań programu Hive](hdinsight-hadoop-optimize-hive-query.md) |
