---
title: Skalowanie rozmiarów klastra — usługa Azure HDInsight
description: Elastyczne skalowanie klastra Apache Hadoop w celu dopasowania do obciążenia w usłudze Azure HDInsight
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 96a72541255ad0059abe5ad280f1728518dbf68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234732"
---
# <a name="scale-azure-hdinsight-clusters"></a>Skalowanie klastrów usługi Azure HDInsight

Usługa HDInsight zapewnia elastyczność, udostępniając możliwość skalowania w górę i skalowania w dół liczby węzłów procesu roboczego w klastrach. Ta elastyczność umożliwia zmniejszenie klastra po godzinach lub w weekendy i rozszerzenie go podczas szczytowych wymagań biznesowych.

Jeśli masz okresowe przetwarzanie wsadowe, klaster HDInsight można skalować w górę kilka minut przed tą operacją, dzięki czemu klaster ma odpowiednią pamięć i moc procesora CPU. Później po zakończeniu przetwarzania i użycie idzie w dół ponownie, można skalować w dół klastra HDInsight do mniejszej liczby węzłów procesu roboczego.

Klastra można skalować ręcznie przy użyciu jednej z metod opisanych poniżej lub użyć opcji [skalowania automatycznego,](hdinsight-autoscale-clusters.md) aby system automatycznie skalował w górę i w dół w odpowiedzi na procesor, pamięć i inne metryki.

> [!NOTE]  
> Obsługiwane są tylko klastry z hdinsight w wersji 3.1.3 lub nowszej. Jeśli nie masz pewności co do wersji klastra, możesz sprawdzić stronę Właściwości.

## <a name="utilities-to-scale-clusters"></a>Narzędzia do skalowania klastrów

Firma Microsoft udostępnia następujące narzędzia do skalowania klastrów:

|Narzędzie | Opis|
|---|---|
|[Moduł Az programu PowerShell](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[Moduł AzureRM programu PowerShell](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --resource-group \<Grupa zasobów> \<--name Cluster Name> --workernode-count \<NewSize>|
|[Klasyczna fraza cli platformy Azure](hdinsight-administer-use-command-line.md)|azure hdinsight cluster \<resize clusterName \<> liczba wystąpień docelowych> |
|[Portal Azure](https://portal.azure.com)|Otwórz okienko klastra HDInsight, wybierz **rozmiar klastra** w menu po lewej stronie, a następnie w okienku Rozmiar klastra wpisz liczbę węzłów procesu roboczego i wybierz pozycję Zapisz.|  

![Opcja klastra skalowania usługi Azure portal](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

Za pomocą dowolnej z tych metod można skalować klaster HDInsight w górę lub w dół w ciągu kilku minut.

> [!IMPORTANT]  
> * Klasyczna interfejs wiersza polecenia platformy Azure jest przestarzała i powinna być używana tylko z klasycznym modelem wdrażania. W przypadku wszystkich innych wdrożeń należy użyć interfejsu [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * Moduł AzureRM programu PowerShell jest przestarzały.  W miarę możliwości należy korzystać z [modułu Az.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0)

## <a name="impact-of-scaling-operations"></a>Wpływ operacji skalowania

Po **dodaniu** węzłów do uruchomionego klastra HDInsight (skalowanie w górę) żadne oczekujące lub uruchomione zadania nie będą miały wpływu. Nowe zadania można bezpiecznie przesłać, gdy proces skalowania jest uruchomiony. Jeśli operacja skalowania nie powiedzie się z jakiegokolwiek powodu, błąd zostanie obsłużona, aby pozostawić klaster w stanie funkcjonalnym.

Jeśli **usuniesz** węzły (skalowanie w dół), wszystkie oczekujące lub uruchomione zadania zakończy się niepowodzeniem po zakończeniu operacji skalowania. Ten błąd jest spowodowany niektóre usługi ponownego uruchamiania podczas procesu skalowania. Istnieje również ryzyko, że klaster może utknąć w trybie awaryjnym podczas operacji ręcznego skalowania.

Wpływ zmiany liczby węzłów danych różni się dla każdego typu klastra obsługiwanego przez program HDInsight:

* Apache Hadoop

    Można bezproblemowo zwiększyć liczbę węzłów procesu roboczego w klastrze Hadoop, który jest uruchomiony bez wpływu na żadnych oczekujących lub uruchomionych zadań. Nowe zadania można również przesłać w trakcie operacji. Błędy w operacji skalowania są bezpiecznie obsługiwane, tak aby klaster zawsze pozostaje w stanie funkcjonalnym.

    Gdy klaster Hadoop jest skalowany w dół przez zmniejszenie liczby węzłów danych, niektóre usługi w klastrze są ponownie uruchamiane. To zachowanie powoduje, że wszystkie uruchomione i oczekujące zadania zakończyć się niepowodzeniem po zakończeniu operacji skalowania. Można jednak ponownie przesłać zadania po zakończeniu operacji.

* Apache HBase

    Można bezproblemowo dodać lub usunąć węzły do klastra HBase, gdy jest uruchomiony. Serwery regionalne są automatycznie równoważące w ciągu kilku minut od ukończenia operacji skalowania. Można jednak również ręcznie zrównoważyć serwery regionalne, logując się do węzła głównego klastra i uruchamiając następujące polecenia w oknie wiersza polecenia:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Aby uzyskać więcej informacji na temat korzystania z powłoki HBase, zobacz [Wprowadzenie do przykładu Apache HBase w programie HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Podczas działania klastra Storm można bezproblemowo dodawać lub usuwać węzły danych. Jednak po pomyślnym zakończeniu operacji skalowania, należy ponownie zrównoważyć topologii.

    Ponowne równoważenie można wykonać na dwa sposoby:

  * Interfejs użytkownika sieci Burzowej
  * Narzędzie interfejsu wiersza polecenia (CLI)

    Więcej informacji można znaleźć w [dokumentacji Burzy Apache.](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)

    Interfejs użytkownika sieci Storm jest dostępny w klastrze HDInsight:

    ![Równoważenie skali burzy HDInsight](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Oto przykładowe polecenie interfejsu wiersza polecenia, aby zrównoważyć topologię storm:

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Jak bezpiecznie skalować w dół klastra

### <a name="scale-down-a-cluster-with-running-jobs"></a>Skalowanie w dół klastra z uruchomionymi zadaniami

Aby uniknąć niepowodzenia uruchomionych zadań podczas operacji skalowania w dół, możesz wypróbować trzy rzeczy:

1. Poczekaj na wykonanie zadań przed skalowaniem w dół klastra.
1. Ręcznie zakończ zadania.
1. Ponownie prześlij zadania po zakończeniu operacji skalowania.

Aby wyświetlić listę oczekujących i uruchomionych zadań, można użyć **interfejsu użytkownika Menedżera zasobów**YARN, wykonując następujące kroki:

1. Z [witryny Azure portal](https://portal.azure.com/)wybierz swój klaster.  Aby uzyskać instrukcje, zobacz [Lista i pokazyj klastry.](./hdinsight-administer-use-portal-linux.md#showClusters) Klaster jest otwierany na nowej stronie portalu.
2. Z widoku głównego przejdź do **pulpitów nawigacyjnych Klastra** > **Ambari home**. Wprowadź poświadczenia klastra.
3. W interfejsie użytkownika Ambari wybierz **pozycję YARN** na liście usług w menu po lewej stronie.  
4. Na stronie YARN wybierz pozycję **Szybkie łącza** i umieść wskaźnik myszy na aktywnym węźle głównym, a następnie wybierz pozycję **ResourceManager UI**.

    ![Apache Ambari szybkie linki ResourceManager UI](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

Użytkownik może uzyskać bezpośredni dostęp do `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`interfejsu resourcemanageru za pomocą programu .

Zobaczysz listę zadań wraz z ich bieżącym stanem. Na zrzucie ekranu jest jedno zadanie aktualnie uruchomione:

![Aplikacje interfejsu użytkownika ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Aby ręcznie zabić uruchominą aplikację, wykonaj następujące polecenie z powłoki SSH:

```bash
yarn application -kill <application_id>
```

Przykład:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Utknięcie w trybie awaryjnym

Podczas skalowania w dół klastra, HDInsight używa interfejsów zarządzania Apache Ambari do pierwszego wycofania dodatkowych węzłów procesu roboczego, które replikują ich bloki HDFS do innych węzłów procesu roboczego online. Następnie usługa HDInsight bezpiecznie skaluje klaster w dół. System PLIKÓW HDFS przechodzi w tryb awaryjny podczas operacji skalowania i ma wyjść po zakończeniu skalowania. W niektórych przypadkach jednak usługa HDFS utknie w trybie awaryjnym podczas operacji skalowania z powodu niedoreplancji bloku plików.

Domyślnie system plików HDFS `dfs.replication` jest skonfigurowany z ustawieniem 1, które określa, ile kopii każdego bloku plików jest dostępnych. Każda kopia bloku plików jest przechowywana w innym węźle klastra.

Gdy hdfs wykryje, że oczekiwana liczba kopii bloków nie są dostępne, HDFS wchodzi w tryb awaryjny i Ambari generuje alerty. Jeśli usługa HDFS wejdzie w tryb awaryjny dla operacji skalowania, ale nie może zakończyć trybu awaryjnego, ponieważ wymagana liczba węzłów nie zostanie wykryta dla replikacji, klaster może utknąć w trybie awaryjnym.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Przykładowe błędy, gdy włączony jest tryb awaryjny

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Można przejrzeć dzienniki węzłów `/var/log/hadoop/hdfs/` nazw z folderu, w pobliżu czasu, kiedy klaster został przeskalowany, aby zobaczyć, kiedy wszedł w tryb awaryjny. Pliki dziennika mają `Hadoop-hdfs-namenode-<active-headnode-name>.*`nazwy .

Główną przyczyną poprzednich błędów jest to, że hive zależy od plików tymczasowych w hdfs podczas uruchamiania kwerend. Gdy hdfs wchodzi w tryb awaryjny, Hive nie można uruchomić kwerendy, ponieważ nie można zapisać do hdfs. Pliki tymczasowe w hdfs znajdują się na dysku lokalnym zamontowanym na maszynach wirtualnych poszczególnych węzłów procesu roboczego i replikowane między innymi węzłami procesu roboczego w trzech replikach, minimum.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Jak zapobiec utknięciu hdinsight w trybie awaryjnym

Istnieje kilka sposobów zapobiegania pozostawieniu programu HDInsight w trybie awaryjnym:

* Przed skalowaniem w dół pliku HDInsight należy zatrzymać wszystkie zadania gałęzi. Alternatywnie należy zaplanować proces skalowania w dół, aby uniknąć konfliktu z uruchomionymi zadaniami hive.
* Ręcznie oczyść pliki katalogów `tmp` zarysowania Hive w hdfs przed skalowaniem w dół.
* Skalowanie tylko w dół HDInsight do trzech węzłów procesu roboczego, minimum. Unikaj przechodzenia tak niskie, jak jeden węzeł procesu roboczego.
* Uruchom polecenie, aby w razie potrzeby pozostawić tryb awaryjny.

W poniższych sekcjach opisano te opcje.

#### <a name="stop-all-hive-jobs"></a>Zatrzymywać wszystkie zadania Hive

Zatrzymaj wszystkie zadania hive przed skalowaniem w dół do jednego węzła procesu roboczego. Jeśli obciążenie jest zaplanowane, a następnie wykonać skalowanie w dół po zakończeniu pracy hive.

Zatrzymanie zadań gałęzi przed skalowaniem pomaga zminimalizować liczbę plików zarysowań w folderze tmp (jeśli istnieje).

#### <a name="manually-clean-up-hives-scratch-files"></a>Ręczne czyszczenie plików zdrapki Hive

Jeśli hive pozostawił pliki tymczasowe, można ręcznie oczyścić te pliki przed skalowaniem w dół, aby uniknąć trybu awaryjnego.

1. Sprawdź, która lokalizacja jest używana dla plików `hive.exec.scratchdir` tymczasowych hive, patrząc na właściwość konfiguracji. Ten parametr jest `/etc/hive/conf/hive-site.xml`ustawiony w zakresie:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Zatrzymaj usługi hive i upewnij się, że wszystkie kwerendy i zadania zostały ukończone.
2. Wyświetl listę zawartości katalogu podstaw znalezionego powyżej, aby sprawdzić, `hdfs://mycluster/tmp/hive/` czy zawiera on jakieś pliki:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Oto przykładowe dane wyjściowe, gdy istnieją pliki:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Jeśli wiesz, że hive jest zrobione z tych plików, można je usunąć. Upewnij się, że hive nie ma żadnych zapytań uruchomionych, patrząc na stronie interfejsu użytkownika Yarn ResourceManager.

    Przykładowy wiersz polecenia do usuwania plików z plików HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Skalowanie hdinsight do trzech lub więcej węzłów procesu roboczego

Jeśli klastry utknąć w trybie awaryjnym często podczas skalowania w dół do mniej niż trzech węzłów procesu roboczego, a poprzednie kroki nie działają, następnie można uniknąć klastra przechodzi w trybie awaryjnym całkowicie, zachowując co najmniej trzy węzły procesu roboczego.

Zachowanie trzech węzłów procesu roboczego jest bardziej kosztowne niż skalowanie w dół tylko do jednego węzła procesu roboczego, ale zapobiega utknięciu klastra w trybie awaryjnym.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Skalowanie hdinsight w dół do jednego węzła procesu roboczego

Nawet wtedy, gdy klaster jest skalowany w dół do 1 węzła, węzeł roboczy 0 będzie nadal przetrwać. Węzeł roboczy 0 nigdy nie może zostać wycofany.

#### <a name="run-the-command-to-leave-safe-mode"></a>Uruchom polecenie, aby opuścić tryb awaryjny

Ostatnią opcją jest wykonanie polecenia trybu awaryjnego . Jeśli wiesz, że powodem wejścia w tryb awaryjny usługi HDFS jest niedoregacja pliku Hive, możesz wykonać następujące polecenie, aby opuścić tryb awaryjny:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Skalowanie w dół klastra Apache HBase

Serwery regionu są automatycznie równoważące w ciągu kilku minut po zakończeniu operacji skalowania. Aby ręcznie zrównoważyć serwery regionu, wykonaj następujące czynności:

1. Połącz się z klastrem HDInsight za pomocą funkcji SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Uruchom powłokę HBase:

    ```bash
    hbase shell
    ```

3. Użyj następującego polecenia, aby ręcznie zrównoważyć serwery regionu:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Następne kroki

* [Automatyczne skalowanie klastrów usługi Azure HDInsight](hdinsight-autoscale-clusters.md)
* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md)
