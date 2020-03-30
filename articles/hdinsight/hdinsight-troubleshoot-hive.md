---
title: Rozwiązywanie problemów z gałęzią przy użyciu usługi Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące pracy z usługą Apache Hive i usługi Azure HDInsight.
keywords: Azure HDInsight, Hive, często zadawane pytania, przewodnik rozwiązywania problemów, często zadawane pytania
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 02247adb9852a72b386feb2ef0924b0f1b3d6277
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895230"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Troubleshoot Apache Hive by using Azure HDInsight (Rozwiązywanie problemów z programem Apache Hive za pomocą usługi Azure HDInsight)

Dowiedz się więcej o najważniejszych pytaniach i ich rozwiązaniach podczas pracy z ładunkami Apache Hive w Apache Ambari.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Jak wyeksportować magazyn meteoru hive i zaimportować go do innego klastra?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Połącz się z klastrem HDInsight przy użyciu klienta bezpiecznej powłoki (SSH). Aby uzyskać więcej informacji, zobacz [Dodatkowe czytanie](#additional-reading-end).

2. Uruchom następujące polecenie w klastrze HDInsight, z którego chcesz wyeksportować magazyn metastore:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   To polecenie generuje plik o nazwie allatables.sql.

3. Skopiuj plik alltables.sql do nowego klastra HDInsight, a następnie uruchom następujące polecenie:

    ```apache
    hive -f alltables.sql
    ```

Kod w krokach rozpoznawania zakłada, że ścieżki danych w nowym klastrze są takie same jak ścieżki danych w starym klastrze. Jeśli ścieżki danych są różne, można ręcznie `alltables.sql` edytować wygenerowany plik, aby odzwierciedlić wszelkie zmiany.

### <a name="additional-reading"></a>Dodatkowa lektura

- [Łączenie się z klastrem HDInsight przy użyciu funkcji SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Jak zlokalizować dzienniki gałęzi w klastrze?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Połącz się z klastrem HDInsight za pomocą funkcji SSH. Aby uzyskać więcej informacji, zobacz **Dodatkowe czytanie**.

2. Aby wyświetlić dzienniki klientów hive, należy użyć następującego polecenia:

   ```apache
   /tmp/<username>/hive.log
   ```

3. Aby wyświetlić dzienniki metastore hive, należy użyć następującego polecenia:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. Aby wyświetlić dzienniki serwera hive, należy użyć następującego polecenia:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>Dodatkowa lektura

- [Łączenie się z klastrem HDInsight przy użyciu funkcji SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Jak uruchomić powłokę Hive z określonymi konfiguracjami w klastrze?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Określ parę klucz konfiguracji-wartość podczas uruchamiania powłoki Gałąź. Aby uzyskać więcej informacji, zobacz [Dodatkowe czytanie](#additional-reading-end).

   ```apache
   hive -hiveconf a=b
   ```

2. Aby wyświetlić listę wszystkich skutecznych konfiguracji w powłoce hive, użyj następującego polecenia:

   ```apache
   hive> set;
   ```

   Na przykład użyj następującego polecenia, aby uruchomić powłokę Hive z włączonym rejestrowaniem debugowania na konsoli:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>Dodatkowa lektura

- [Właściwości konfiguracji gałęzi](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-apache-tez-dag-data-on-a-cluster-critical-path"></a><a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Jak analizować dane Apache Tez DAG na ścieżce krytycznej klastra?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Aby przeanalizować apache Tez skierowane wykres acykliczny (DAG) na wykresie krytycznych klastra, połącz się z klastrem HDInsight przy użyciu SSH. Aby uzyskać więcej informacji, zobacz [Dodatkowe czytanie](#additional-reading-end).

2. W wierszu polecenia uruchom następujące polecenie:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Aby wyświetlić listę innych analizatorów, które mogą być używane do analizowania Tez DAG, należy użyć następującego polecenia:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Jako pierwszy argument należy podać przykładowy program.

   Prawidłowe nazwy programów obejmują:
    - **ContainerReuseAnalyzer:** Drukowanie szczegółów ponownego użycia kontenera w dag
    - **CriticalPath**: Znajdź krytyczną ścieżkę DAG
    - **LocalityAnalyzer**: Drukuj szczegóły miejscowości w DAG
    - **ShuffleTimeAnalyzer**: Analiza szczegółów czasu losowania w DAG
    - **SkewAnalyzer**: Analizowanie szczegółów pochylenia w DAG
    - **SlowNodeAnalyzer**: Szczegóły węzła drukowania w DAG
    - **SlowTaskIdentifier**: Drukowanie powolnych szczegółów zadania w DAG
    - **SlowestVertexAnalyzer**: Drukuj najwolniejsze szczegóły wierzchołka w DAG
    - **SpillAnalyzer**: Szczegóły wycieku druku w DAG
    - **TaskConcurrencyAnalyzer**: Drukowanie szczegółów współbieżności zadań w dag
    - **VertexLevelCriticalPathAnalyzer**: Znajdź ścieżkę krytyczną na poziomie wierzchołka w DAG

### <a name="additional-reading"></a>Dodatkowa lektura

- [Łączenie się z klastrem HDInsight przy użyciu funkcji SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Jak pobrać dane Tez DAG z klastra?

#### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

Istnieją dwa sposoby zbierania danych Tez DAG:

- Z wiersza polecenia:

    Połącz się z klastrem HDInsight za pomocą funkcji SSH. W wierszu polecenia wpisz następujące polecenie:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Użyj widoku Ambari Tez:

  1. Idź do Ambari.
  2. Przejdź do widoku Tez (pod ikoną kafelków w prawym górnym rogu).
  3. Wybierz dag, który chcesz wyświetlić.
  4. Wybierz **pozycję Pobierz dane**.

### <a name="additional-reading"></a><a name="additional-reading-end"></a>Dodatkowa lektura

[Łączenie się z klastrem HDInsight przy użyciu funkcji SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

- Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

- Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
