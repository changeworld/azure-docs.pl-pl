---
title: Rozwiązywanie problemów z Hive za pomocą usługi Azure HDInsight | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania na temat pracy z Apache Hive i usłudze Azure HDInsight.
keywords: Azure HDInsight, Hive, często zadawane pytania, rozwiązywanie problemów z przewodnika, często zadawane pytania
services: Azure HDInsight
documentationcenter: na
author: dharmeshkakadia
manager: ''
editor: ''
ms.assetid: 15B8D0F3-F2D3-4746-BDCB-C72944AA9252
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: dharmeshkakadia
ms.openlocfilehash: d397552285466dc038fe580a084e2d1d0d69bfe2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31407701"
---
# <a name="troubleshoot-hive-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z Hive za pomocą usługi Azure HDInsight

Dowiedz się więcej o najczęściej zadawane pytania i ich rozwiązania podczas pracy z Apache Hive ładunków w Apache Ambari.


## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Jak wyeksportować na potrzeby magazynu metadanych Hive i zaimportuj go w innym klastrze?


### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Połącz się z klastrem usługi HDInsight przy użyciu klienta Secure Shell (SSH). Aby uzyskać więcej informacji, zobacz [dodatkowe materiały](#additional-reading-end).

2. Uruchom następujące polecenie w klastrze usługi HDInsight, z którego chcesz wyeksportować potrzeby magazynu metadanych:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

  To polecenie generuje plik o nazwie allatables.sql.

3. Skopiuj plik alltables.sql do nowego klastra usługi HDInsight, a następnie uruchom następujące polecenie:

  ```apache
  hive -f alltables.sql
  ```

Kod w kroki rozwiązania przyjęto założenie, że ścieżki danych w nowym klastrze są takie same jak ścieżek danych w starym klastrze. Jeśli ścieżek danych są różne, można ręcznej edycji pliku wygenerowanego alltables.sql, aby odzwierciedlić zmiany.

### <a name="additional-reading"></a>Dodatkowe materiały

- [Połącz z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Jak znaleźć gałęzi dzienniki w klastrze?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz **dodatkowe materiały**.

2. Aby wyświetlić dzienniki klienta Hive, użyj następującego polecenia:

  ```apache
  /tmp/<username>/hive.log 
  ```

3. Aby wyświetlić dzienniki potrzeby magazynu metadanych Hive, użyj następującego polecenia:

  ```apache
  /var/log/hive/hivemetastore.log 
  ```

4. Aby wyświetlić dzienniki Hiveserver, użyj następującego polecenia:

  ```apache
  /var/log/hive/hiveserver2.log 
  ```

### <a name="additional-reading"></a>Dodatkowe materiały

- [Połącz z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Jak uruchomić powłokę programu Hive o określonej konfiguracji w klastrze?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Określ parę klucz wartość konfiguracji podczas uruchamiania powłokę programu Hive. Aby uzyskać więcej informacji, zobacz [dodatkowe materiały](#additional-reading-end).

  ```apache
  hive -hiveconf a=b 
  ```

2. Listy wszystkich konfiguracji skuteczne na gałąź powłoki, użyj następującego polecenia:

  ```apache
  hive> set;
  ```

  Na przykład następujące polecenie można uruchomić powłoki gałąź z rejestrowaniem debugowania włączona w konsoli:

  ```apache
  hive -hiveconf hive.root.logger=ALL,console 
  ```

### <a name="additional-reading"></a>Dodatkowe materiały

- [Właściwości konfiguracji gałęzi](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Jak analizować dane Tez DAG na ścieżkę krytyczną klastra?


### <a name="resolution-steps"></a>Kroki rozwiązywania problemów
 
1. Do analizowania Apache Tez ukierunkowanego wykresu acyklicznego (DAG) na wykresie klastra o znaczeniu krytycznym, połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [dodatkowe materiały](#additional-reading-end).

2. W wierszu polecenia Uruchom następujące polecenie:
   
  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
  ```

3. Aby wyświetlić listę innych analizatorów, które mogą służyć do analizowania aplikacji Tez DAG, użyj następującego polecenia:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
  ```

  Należy podać przykład programu jako pierwszego argumentu.

  Program prawidłowe nazwy zawierają:
    - **ContainerReuseAnalyzer**: drukowanie szczegółów ponownemu kontenera w grupie DAG
    - **CriticalPath**: Znajdź ścieżkę krytyczną do grupy DAG
    - **LocalityAnalyzer**: drukowanie szczegółów miejscowości, w grupie DAG
    - **ShuffleTimeAnalyzer**: analizowanie losowa szczegóły czasu w grupie DAG
    - **SkewAnalyzer**: analizowanie pochylenia szczegółów w grupie DAG
    - **SlowNodeAnalyzer**: drukowanie szczegółów węzła w grupie DAG
    - **SlowTaskIdentifier**: szczegóły powolne zadania drukowania w grupie DAG
    - **SlowestVertexAnalyzer**: drukowanie najwolniejsze szczegółów wierzchołków w grupie DAG
    - **SpillAnalyzer**: szczegóły usuwania wycieków drukowania w grupie DAG
    - **TaskConcurrencyAnalyzer**: drukowanie szczegółów współbieżności zadań w grupie DAG
    - **VertexLevelCriticalPathAnalyzer**: Znajdź ścieżkę krytyczną na poziomie wierzchołków w grupie DAG


### <a name="additional-reading"></a>Dodatkowe materiały

- [Połącz z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>W jaki sposób pobierać dane Tez DAG z klastra?


#### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

Istnieją dwa sposoby zbierania danych Tez DAG:

- W wierszu polecenia:
 
    Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. W wierszu polecenia Uruchom następujące polecenie:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
  ```

- Użyj widoku Ambari Tez:
   
  1. Przejdź do narzędzia Ambari. 
  2. Przejdź do widoku Tez (w obszarze ikony Kafelki w prawym górnym narożniku). 
  3. Wybierz grupy DAG, który chcesz wyświetlić.
  4. Wybierz **pobierania danych**.

### <a name="additional-reading-end"></a>Dodatkowe materiały

[Połącz z klastrem usługi HDInsight przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


### <a name="see-also"></a>Zobacz też
[Rozwiązywanie problemów przy użyciu usługi Azure HDInsight](hdinsight-troubleshoot-guide.md)




