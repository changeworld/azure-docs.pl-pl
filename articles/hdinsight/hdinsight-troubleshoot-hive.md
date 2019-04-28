---
title: Rozwiązywanie problemów z programu Hive przy użyciu usługi Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania na temat pracy z usługą Apache Hive i usługi Azure HDInsight.
keywords: Usługa Azure HDInsight, Hive, często zadawane pytania, przewodnik, często zadawane pytania dotyczące rozwiązywania problemów
ms.service: hdinsight
author: dharmeshkakadia
ms.author: dharmeshkakadia
ms.topic: conceptual
ms.date: 11/2/2017
ms.openlocfilehash: 43886a132f2f3cf75f0ec7a0b2dc0680a0f69589
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765808"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z Apache Hive za pomocą usługi Azure HDInsight

Dowiedz się więcej o najczęściej zadawane pytania oraz dostępnych rozwiązaniach poszczególnych problemów podczas pracy z usługą Apache Hive ładunków w Apache Ambari.


## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Jak wyeksportować Magazyn metadanych Hive i zaimportować go do innego klastra


### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Nawiąż połączenie z klastrem HDInsight przy użyciu klienta Secure Shell (SSH). Aby uzyskać więcej informacji, zobacz [dodatkowe materiały](#additional-reading-end).

2. Uruchom następujące polecenie w klastrze HDInsight, z którego chcesz wyeksportować magazynu metadanych:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   To polecenie spowoduje wygenerowanie pliku o nazwie allatables.sql.

3. Skopiuj alltables.sql plików do nowego klastra HDInsight, a następnie uruchom następujące polecenie:

   ```apache
   hive -f alltables.sql
   ```

Kod w kroki rozwiązania przyjęto założenie, że ścieżki danych w nowym klastrze są takie same jak ścieżki danych w starym klastrze. Jeśli ścieżki danych są różne, należy ręcznie zmodyfikować pliku wygenerowanego alltables.sql, aby odzwierciedlić zmiany.

### <a name="additional-reading"></a>Materiały uzupełniające

- [Łączenie z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Jak znaleźć dzienniki programu Hive w klastrze?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Nawiąż połączenie z klastrem HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz **dodatkowe materiały**.

2. Aby wyświetlić dzienniki klienta programu Hive, użyj następującego polecenia:

   ```apache
   /tmp/<username>/hive.log 
   ```

3. Aby wyświetlić dzienniki magazynu metadanych Hive, użyj następującego polecenia:

   ```apache
   /var/log/hive/hivemetastore.log 
   ```

4. Aby wyświetlić dzienniki Hiveserver, użyj następującego polecenia:

   ```apache
   /var/log/hive/hiveserver2.log 
   ```

### <a name="additional-reading"></a>Materiały uzupełniające

- [Łączenie z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Jak uruchomić powłoki usługi Hive przy użyciu określonej konfiguracji w klastrze?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Określ pary klucz wartość konfiguracji podczas uruchamiania powłoki usługi Hive. Aby uzyskać więcej informacji, zobacz [dodatkowe materiały](#additional-reading-end).

   ```apache
   hive -hiveconf a=b 
   ```

2. Aby wyświetlić listę wszystkich konfiguracje skuteczne na powłokę programu Hive, użyj następującego polecenia:

   ```apache
   hive> set;
   ```

   Na przykład następujące polecenie do Uruchom powłokę programu Hive za pomocą funkcji rejestrowania debugowania, włączone na konsoli:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console 
   ```

### <a name="additional-reading"></a>Materiały uzupełniające

- [Właściwości konfiguracji programu hive](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Jak analizować dane Apache Tez DAG na ścieżkę krytyczną klastra?


### <a name="resolution-steps"></a>Kroki rozwiązywania problemów
 
1. Aby analizować Apache Tez skierowanym grafie acyklicznym (DAG) na wykresie klastra o znaczeniu krytycznym, łączenia z klastrem HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [dodatkowe materiały](#additional-reading-end).

2. W wierszu polecenia uruchom następujące polecenie:
   
   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Aby wyświetlić listę innych analizatorów, które mogą służyć do analizowania aplikacji Tez w grupie DAG, użyj następującego polecenia:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Należy podać przykładowy program jako pierwszy argument.

   Program prawidłowe nazwy zawierają:
    - **ContainerReuseAnalyzer**: Drukowanie szczegółów ponowne użycie kontenerów w grupie DAG
    - **CriticalPath**: Znajdź ścieżki krytycznej do grupy DAG
    - **LocalityAnalyzer**: Drukowanie szczegółów lokalizacji, w grupie DAG
    - **ShuffleTimeAnalyzer**: Analizowanie szczegółów czasu shuffle w grupie DAG
    - **SkewAnalyzer**: Analizowanie szczegółów niesymetryczność, w grupie DAG
    - **SlowNodeAnalyzer**: Szczegóły węzła wydruku w grupie DAG
    - **SlowTaskIdentifier**: Drukowanie szczegółów zadania powolnego w grupie DAG
    - **SlowestVertexAnalyzer**: Drukowanie najwolniejsze szczegółów wierzchołka w grupie DAG
    - **SpillAnalyzer**: Drukowanie rozlania szczegółów w grupie DAG
    - **TaskConcurrencyAnalyzer**: Drukowanie szczegółów współbieżności zadań w grupie DAG
    - **VertexLevelCriticalPathAnalyzer**: Znajdź ścieżkę krytyczną na poziomie wierzchołka w grupie DAG


### <a name="additional-reading"></a>Materiały uzupełniające

- [Łączenie z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Jak pobrać dane w aplikacji Tez DAG z klastra?


#### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

Istnieją dwa sposoby na zbieranie danych Tez DAG:

- W wierszu polecenia:
 
    Nawiąż połączenie z klastrem HDInsight przy użyciu protokołu SSH. W wierszu polecenia Uruchom następujące polecenie:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
  ```

- Użyj widoku Ambari Tez:
   
  1. Przejdź do narzędzia Ambari. 
  2. Przejdź do widoku aplikacji Tez (w obszarze Kafelki ikonę w prawym górnym rogu). 
  3. Wybierz grupy DAG, którą chcesz wyświetlić.
  4. Wybierz **pobierania danych**.

### <a name="additional-reading-end"></a>Materiały uzupełniające

[Łączenie z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


### <a name="see-also"></a>Zobacz też
[Rozwiązywanie problemów przy użyciu usługi Azure HDInsight](hdinsight-troubleshoot-guide.md)




