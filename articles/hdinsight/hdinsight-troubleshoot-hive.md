---
title: Rozwiązywanie problemów z programem Hive przy użyciu usługi Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące pracy z usługami Apache Hive i Azure HDInsight.
keywords: Azure HDInsight, Hive, często zadawane pytania, Przewodnik rozwiązywania problemów, często zadawane pytania
ms.service: hdinsight
author: dharmeshkakadia
ms.author: dkakadia
ms.topic: conceptual
ms.date: 11/2/2017
ms.openlocfilehash: 91e6803e0a1302a33a3bf176ad84d0b0e0c8c5b6
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875942"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z Apache Hive przy użyciu usługi Azure HDInsight

Zapoznaj się z najważniejszymi pytaniami i ich rozwiązaniami podczas pracy z Apache Hive ładunku w programie Apache Ambari.


## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Jak mogę wyeksportować magazyn metadanych Hive i zaimportować go do innego klastra?


### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Nawiąż połączenie z klastrem HDInsight przy użyciu klienta Secure Shell (SSH). Aby uzyskać więcej informacji, zobacz [dodatkowe materiały](#additional-reading-end).

2. Uruchom następujące polecenie w klastrze usługi HDInsight, z którego chcesz wyeksportować magazyn metadanych:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   To polecenie generuje plik o nazwie allatables. SQL.

3. Skopiuj plik alltables. SQL do nowego klastra usługi HDInsight, a następnie uruchom następujące polecenie:

   ```apache
   hive -f alltables.sql
   ```

Kod w krokach rozwiązania założono, że ścieżki danych w nowym klastrze są takie same jak ścieżki danych w starym klastrze. Jeśli ścieżki danych są różne, można ręcznie edytować wygenerowany plik alltables. SQL w celu odzwierciedlenia wszelkich zmian.

### <a name="additional-reading"></a>Materiały uzupełniające

- [Nawiązywanie połączenia z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Jak mogę zlokalizować dzienników Hive w klastrze?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz **dodatkowe materiały**.

2. Aby wyświetlić dzienniki klienta programu Hive, użyj następującego polecenia:

   ```apache
   /tmp/<username>/hive.log 
   ```

3. Aby wyświetlić dzienniki magazyn metadanych Hive, użyj następującego polecenia:

   ```apache
   /var/log/hive/hivemetastore.log 
   ```

4. Aby wyświetlić dzienniki Hiveserver, użyj następującego polecenia:

   ```apache
   /var/log/hive/hiveserver2.log 
   ```

### <a name="additional-reading"></a>Materiały uzupełniające

- [Nawiązywanie połączenia z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Jak mogę uruchomić powłokę programu Hive z określonymi konfiguracjami w klastrze?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Określ parę klucz-wartość konfiguracji podczas uruchamiania powłoki programu Hive. Aby uzyskać więcej informacji, zobacz [dodatkowe materiały](#additional-reading-end).

   ```apache
   hive -hiveconf a=b 
   ```

2. Aby wyświetlić listę wszystkich efektywnych konfiguracji w usłudze Hive Shell, użyj następującego polecenia:

   ```apache
   hive> set;
   ```

   Na przykład użyj następującego polecenia, aby uruchomić powłokę programu Hive z włączonym rejestrowaniem debugowania w konsoli programu:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console 
   ```

### <a name="additional-reading"></a>Materiały uzupełniające

- [Właściwości konfiguracji programu Hive](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Jak mogę analizować Apache Tez DAG danych w ścieżce krytycznej dla klastra?


### <a name="resolution-steps"></a>Kroki rozwiązywania problemów
 
1. Aby analizować Apache Tez ukierunkowany wykres (DAG) na wykresie krytycznym dla klastra, Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [dodatkowe materiały](#additional-reading-end).

2. W wierszu polecenia uruchom następujące polecenie:
   
   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Aby wyświetlić listę innych analizatorów, których można użyć do analizowania tez DAG, użyj następującego polecenia:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Musisz podać Przykładowy program jako pierwszy argument.

   Prawidłowe nazwy programów to:
    - **ContainerReuseAnalyzer**: Szczegóły ponownego użycia kontenera drukowania w DAG
    - **CriticalPath**: Znajdowanie ścieżki krytycznej DAG
    - **LocalityAnalyzer**: Szczegóły lokalnego drukowania w DAG
    - **ShuffleTimeAnalyzer**: Analizuj szczegóły czasu losowego w DAG
    - **SkewAnalyzer**: Analizowanie szczegółów pochylenia w DAG
    - **SlowNodeAnalyzer**: Drukowanie szczegółów węzła w DAG
    - **SlowTaskIdentifier**: Drukuj wolne szczegóły zadania w DAG
    - **SlowestVertexAnalyzer**: Drukuj najwolniejsze szczegóły wierzchołka w DAG
    - **SpillAnalyzer**: Drukuj szczegóły rozlania w DAG
    - **TaskConcurrencyAnalyzer**: Drukuj szczegóły współbieżności zadania w DAG
    - **VertexLevelCriticalPathAnalyzer**: Znajdowanie ścieżki krytycznej na poziomie wierzchołka w DAG


### <a name="additional-reading"></a>Materiały uzupełniające

- [Nawiązywanie połączenia z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Jak mogę pobrać danych tez DAG z klastra?


#### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

Istnieją dwa sposoby zbierania danych tez DAG:

- W wierszu polecenia:
 
    Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. W wierszu polecenia Uruchom następujące polecenie:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
  ```

- Użyj widoku Ambari tez:
   
  1. Przejdź do Ambari. 
  2. Przejdź do widoku tez (w obszarze ikony kafelków w prawym górnym rogu). 
  3. Wybierz DAG, które chcesz wyświetlić.
  4. Wybierz pozycję **Pobierz dane**.

### <a name="additional-reading-end"></a>Materiały uzupełniające

[Nawiązywanie połączenia z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


### <a name="see-also"></a>Zobacz też
[Rozwiązywanie problemów przy użyciu usługi Azure HDInsight](hdinsight-troubleshoot-guide.md)




