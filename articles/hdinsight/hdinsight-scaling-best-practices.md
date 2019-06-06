---
title: Skalowanie klastrów rozmiarów — Azure HDInsight
description: Skalowanie klastra usługi Azure HDInsight, elastyczne, aby dopasować obciążenia.
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: eb68421c4f62d94eedf266a0c34a0e276eacc4a6
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479280"
---
# <a name="scale-hdinsight-clusters"></a>Skaluj klastry HDInsight

HDInsight zapewnia elastyczność, oferując możliwość skalowania w górę i skalowania w dół liczbę węzłów procesu roboczego w klastrach. Ta elastyczność umożliwia zmniejszania klastra po godzinach lub w weekendy i rozwiń go podczas szczytowego zapotrzebowania biznesowych.

W przypadku przetwarzania wsadowego okresowe klastra HDInsight może być skalowany za kilka minut przed wykonaniem tej operacji, aby klaster miał odpowiedniej ilości pamięci i mocy procesora CPU.  Później po zakończeniu przetwarzania i użycie pogarsza, możesz skalować w dół do mniejszej liczby węzłów procesu roboczego klastra HDInsight.

Skalowanie klastra ręcznie przy użyciu jednej z metod opisanych poniżej, lub użyj [skalowania automatycznego](hdinsight-autoscale-clusters.md) opcje, aby system automatycznie skalować w górę i w dół w odpowiedzi na procesor CPU, pamięci i innych metryk.

## <a name="utilities-to-scale-clusters"></a>Narzędzia Skalowanie klastrów

Firma Microsoft udostępnia następujące narzędzia, które skalowanie klastrów:

|Narzędzie | Opis|
|---|---|
|[Az programu PowerShell](https://docs.microsoft.com/powershell/azure)|[Zestaw AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) - ClusterName \<nazwa klastra > - TargetInstanceCount \<NewSize >|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Zestaw AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) - ClusterName \<nazwa klastra > - TargetInstanceCount \<NewSize >|
|[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --resource-group \<Resource group> --name \<Cluster Name> --target-instance-count \<NewSize>|
|[Interfejs wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md)|zmiany rozmiaru klastra usługi Azure hdinsight \<Nazwa_klastra > \<liczba wystąpień docelowy > |
|[Azure Portal](https://portal.azure.com)|Otwórz okienko klastra usługi HDInsight, wybierz opcję **rozmiar klastra** w menu po lewej stronie, a następnie w okienku rozmiaru klastra, wpisz liczbę węzłów procesu roboczego i wybierz przycisk Zapisz.|  

![Skalowanie klastra](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Przy użyciu dowolnej z tych metod, można skalować klastra usługi HDInsight w górę lub w dół w ciągu kilku minut.

> [!IMPORTANT]  
> * Klasyczny Azure interfejs wiersza polecenia jest przestarzały i powinna służyć wyłącznie przy użyciu klasycznego modelu wdrażania. W przypadku wszystkich innych wdrożeń, użyj [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * Moduł PowerShell AzureRM jest przestarzały.  Użyj [modułu Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) zawsze, gdy jest to możliwe.

## <a name="impact-of-scaling-operations"></a>Wpływ operacji skalowania

Po użytkownik **Dodaj** węzłów do uruchomionego klastra HDInsight (skalowanie w górę), wszystkie oczekujące lub uruchomione zadania nie zostaną zmienione. Nowe zadania można bezpiecznie przesyłać proces skalowania jest uruchomiona. Jeśli operacja skalowania nie powiedzie się z jakiegokolwiek powodu, obsługi awarii pozostawić klastra w stanie działać.

Jeśli użytkownik **Usuń** węzłów (skalowania w dół), wszelkie oczekujące na zatwierdzenie lub uruchomione zadania zostaną zakończyć się niepowodzeniem, po zakończeniu operacji skalowania. Ten błąd jest spowodowany niektóre z tych usług, ponownego uruchamiania podczas procesu skalowania. Istnieje również ryzyko klastra można uzyskać zablokowane w trybie awaryjnym podczas ręcznego skalowanie w operacji.

## <a name="how-to-safely-scale-down-a-cluster"></a>Jak bezpiecznie skalowanie w dół klastra

### <a name="scale-down-a-cluster-with-running-jobs"></a>Skaluj w dół klastra z uruchomionych zadań

Aby uniknąć uruchomionych zadań się nie powieść podczas operacja skalowania w dół, możesz spróbować trzy rzeczy:

1. Poczekaj, aż zadania, które należy wykonać przed skalowania klastra.
1. Ręcznie zakończenia zadania.
1. Prześlij zadania po zakończył operację skalowania.

Aby wyświetlić listę oczekujące i uruchomione zadania, należy użyć usługi YARN **interfejsu użytkownika Menedżera zasobów**, wykonaj następujące czynności:

1. Z [witryny Azure portal](https://portal.azure.com/), wybierz klaster.  Zobacz [listy i wyświetlaniu klastrów](./hdinsight-administer-use-portal-linux.md#showClusters) Aby uzyskać instrukcje. Klaster zostanie otwarty na nowej stronie portalu.
2. W widoku głównego, przejdź do **pulpity nawigacyjne klastra** > **Ambari macierzystego**. Wprowadź swoje poświadczenia klastra.
3. Wybierz z interfejsu użytkownika Ambari **YARN** na liście usług, w menu po lewej stronie.  
4. Na stronie usługi YARN wybierz **szybkich łączy** i umieść kursor nad aktywnego węzła głównego, a następnie wybierz **interfejsu użytkownika Menedżera zasobów**.

    ![ResourceManager UI](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

Może uzyskać bezpośredniego dostępu interfejsu użytkownika Menedżera zasobów z `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Możesz wyświetlić listę zadań wraz z ich bieżącego stanu. Na zrzucie ekranu istnieje jedno zadanie uruchomione:

![Aplikacje interfejsu użytkownika Menedżera zasobów](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Można ręcznie skasować tego uruchomionej aplikacji, wykonaj następujące polecenie z poziomu powłoki SSH:

```bash
yarn application -kill <application_id>
```

Na przykład:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Gromadzą się w trybie awaryjnym

Skalowanie w dół klaster HDInsight używa interfejsów zarządzania Apache Ambari do najpierw likwidowanie węzłów procesu roboczego dodatkowe, które replikują ich bloki systemu plików HDFS do innych węzłów procesu roboczego w trybie online. Po tym HDInsight bezpiecznie umożliwia skalowanie klastra w dół. System plików HDFS przechodzi w trybie awaryjnym podczas operacji skalowania i powinna pochodzić po zakończeniu skalowanie. W niektórych przypadkach jednak system plików HDFS zablokowania w trybie awaryjnym podczas skalowania operacji z powodu niepełnego bloku replikację.

Domyślnie, system plików HDFS jest skonfigurowany przy użyciu `dfs.replication` ustawienie 3, który kontroluje liczbę kopii każdej blokowania plików są dostępne. Każda kopia blokowania plików są przechowywane w innym węźle klastra.

Gdy system plików HDFS wykryje, że oczekiwana liczba kopii bloku nie są dostępne, system plików HDFS przechodzi w tryb awaryjny i generuje alerty, Ambari. Jeśli system plików HDFS przejdzie do trybu bezpieczne dla operacji skalowania, ale następnie nie może zamknąć tryb awaryjny, ponieważ wymagana liczba węzłów nie są wykrywane na potrzeby replikacji, klaster może zostać wstrzymana w trybie awaryjnym.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Przykładowe błędy, gdy jest włączony tryb awaryjny

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Możesz przejrzeć dzienniki węzła nazwy z `/var/log/hadoop/hdfs/` folderu zbliżonym czasie, gdy została przeskalowana klastra, aby zobaczyć, po wprowadzeniu go trybu awaryjnego. Pliki dziennika są nazywane `Hadoop-hdfs-namenode-hn0-clustername.*`.

Przyczyny poprzednie błędy to, czy gałąź jest zależna od pliki tymczasowe w systemie plików HDFS podczas uruchamiania zapytań. Gdy system plików HDFS wejdzie w trybie awaryjnym, gałąź nie można uruchomić zapytania, ponieważ nie można zapisać do systemu plików HDFS. Pliki tymczasowe w systemie plików HDFS znajdują się na dysku lokalnym, zainstalowany w węźle procesu roboczego poszczególnych maszyn wirtualnych i replikowane wśród innych węzłów procesu roboczego na trzech replik, minimalna.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Jak uniemożliwić HDInsight gromadzą się w trybie awaryjnym

Istnieje kilka sposobów, aby uniemożliwić pozostaje w trybie awaryjnym HDInsight:

* Przed skalowanie w dół HDInsight, należy zatrzymać wszystkie zadania Hive. Alternatywnie można zaplanować skalowania w dół do procesu, aby uniknąć ryzyka powstania konfliktu z uruchamiania zadań Hive.
* Ręcznie wyczyścić podstaw gałęzi `tmp` pliki katalogu w systemie plików HDFS przed skalowanie w dół.
* Tylko skalować w dół HDInsight z trzema węzłami procesu roboczego minimalnej. Należy unikać, przechodząc zaledwie jednego procesu roboczego węzła.
* Jeśli to konieczne, należy uruchomić polecenie, aby opuścić tryb awaryjny.

W poniższych sekcjach opisano te opcje.

#### <a name="stop-all-hive-jobs"></a>Zatrzymanie wszystkich zadań Hive

Przed skalowanie w dół do węzła jednego procesu roboczego, należy zatrzymać wszystkie zadania Hive. Jeżeli obciążenie jest zaplanowane, wykonujących dół usługi po zakończeniu pracy programu Hive.

Zatrzymywanie zadań Hive przed skalowaniem, pomaga zminimalizować liczbę pliki tymczasowe pliki w folderze tmp (jeśli istnieje).

#### <a name="manually-clean-up-hives-scratch-files"></a>Ręcznie wyczyścić pliki tymczasowe pliki w gałęzi

Jeśli gałąź ma pozostać plików tymczasowych, a następnie możesz ręcznie wyczyścić tych plików przed skalowane w dół do uniknąć trybu awaryjnego.

1. Sprawdź, lokalizacji, do której jest on używany na pliki tymczasowe Hive, analizując `hive.exec.scratchdir` właściwość konfiguracji. Ten parametr ma wartość w ramach `/etc/hive/conf/hive-site.xml`:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Zatrzymywanie usług programu Hive i upewnij się, że wszystkie zadania i zapytania są wykonywane.
2. Wyświetlanie zawartości katalogu tymczasowego znaleziony powyżej, `hdfs://mycluster/tmp/hive/` czy zawiera on żadnych plików:

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Poniżej przedstawiono przykładowe dane wyjściowe, gdy pliki istnieją:

    ```
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Jeśli wiesz, że Hive jest wykonywane przy użyciu tych plików, można je usunąć. Pamiętaj, gałąź nie ma żadnych zapytań, uruchomione przez wyszukiwanie w interfejsie użytkownika Yarn ResourceManager strony.

    Przykładowy wiersz polecenia, aby usunąć pliki z systemu plików HDFS:

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>HDInsight skalowania do co najmniej trzy węzły procesu roboczego

Jeśli klastrów utknąć w trybie awaryjnym często, podczas skalowania w dół do mniejszej liczby węzłów procesu roboczego, a poprzednie kroki nie zadziałają, można uniknąć pracę w trybie awaryjnym całkowicie przechowując co najmniej trzy węzły procesu roboczego klastra.

Zachowywanie trzy węzły procesu roboczego jest droższy niż skalowanie w dół do węzła tylko jednego procesu roboczego, ale uniemożliwi klastra z gromadzą się w trybie awaryjnym.

#### <a name="run-the-command-to-leave-safe-mode"></a>Uruchom polecenie, aby opuścić tryb awaryjny

Ostatnia opcja polega można wykonać polecenia opuszczania trybu awaryjnego. Jeśli znasz to przyczyna systemu plików HDFS, wprowadzając w trybie awaryjnym, ze względu na gałąź niepełną replikację, można wykonać następujące polecenie, aby opuścić tryb awaryjny:


```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Skalowanie w dół, klaster Apache HBase

Serwery regionów są automatycznie równoważone w ciągu kilku minut, po zakończeniu operacji skalowania. Aby ręcznie równoważyć serwery regionów, wykonaj następujące czynności:

1. Łączenie z klastrem HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Uruchom powłokę HBase:

    ```bash
    hbase shell
    ```

3. Aby ręcznie równoważyć serwery regionów, użyj następującego polecenia:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Kolejne kroki

* [Automatyczne skalowanie klastrów Azure HDInsight](hdinsight-autoscale-clusters.md)
* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md)
* [Skalowanie klastrów](hdinsight-administer-use-portal-linux.md#scale-clusters)
