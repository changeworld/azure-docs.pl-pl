---
title: Skalowanie klastra rozmiary - Azure HDInsight | Dokumentacja firmy Microsoft
description: Skalowanie klastra usługi HDInsight w taki sposób, aby obciążenie.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: ashish
ms.openlocfilehash: 8b76d7d0441a5c1c25ad17b73083ec0e4feef1fe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="scale-hdinsight-clusters"></a>Klastry HDInsight skali

Usługa HDInsight zapewnia elastyczność, zapewniając możliwość skalowania w i w dół liczbę węzłów procesu roboczego w klastrach. Dzięki temu można zmniejszyć rozmiar klastra po godzinach lub w weekendy i rozwiń go podczas szczytowego potrzeb biznesowych.

Na przykład jeśli masz niektórych przetwarzania wsadowego tak się stanie, raz dziennie lub raz w miesiącu, klaster usługi HDInsight umożliwia skalowanie za kilka minut przed tym zaplanowane zdarzenie będzie odpowiedniej ilości pamięci i mocy obliczeniowej Procesora. Skalowanie za pomocą polecenia cmdlet programu PowerShell można zautomatyzować [ `Set–AzureRmHDInsightClusterSize` ](hdinsight-administer-use-powershell.md#scale-clusters).  Później po zakończeniu przetwarzania i użycia przejdzie ponownie, można skalować klastra usługi HDInsight do mniej węzłów roboczych w dół.

* Skalowanie klastra za pośrednictwem [PowerShell](hdinsight-administer-use-powershell.md):

    ```powershell
    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
    ```
    
* Skalowanie klastra za pośrednictwem [interfejsu wiersza polecenia Azure](hdinsight-administer-use-command-line.md):

    ```
    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
    ```
    
* Skalowanie klastra za pośrednictwem [portalu Azure](https://portal.azure.com), Otwórz swoje okienko klastra usługi HDInsight, wybierz **klaster w skali** w menu po lewej stronie, a następnie w okienku klastra skali, wpisz odpowiednią liczbę węzłów procesu roboczego i Wybierz opcję Zapisz.

    ![Skalowanie klastra](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Przy użyciu jednej z tych metod, można skalować klastra usługi HDInsight w górę lub w dół w ciągu minut.

## <a name="scaling-impacts-on-running-jobs"></a>Skalowanie wpływu na uruchomione zadania

Gdy użytkownik **dodać** węzły do uruchomionego z klastrem usługi HDInsight, nie będzie mieć wpływ na wszystkie oczekujące lub uruchomione zadania. Ponadto nowe zadania można bezpiecznie przesyłać podczas skalowania proces jest uruchomiony. Jeśli operacje skalowania nie jakiejkolwiek przyczyny, awarii bezpiecznie obsługi, opuszczenie klastra prawidłowo.

Jednak jeśli są zmniejszania klastra przez **usuwanie** węzłów, oczekujące lub uruchomione zadania zostaną wykonane po zakończeniu operacji skalowania. Ten błąd jest spowodowany niektórych usług ponowne uruchamianie podczas procesu.

Aby rozwiązać ten problem, można poczekać na zakończenie przed skalowania klastra, ręcznie zakończenie zadania lub ponawiania przesyłania zadań, po operacji skalowania zawarła zadań.

Aby zobaczyć listę oczekujących i uruchomionych zadań, program interfejsu użytkownika Menedżera ResourceManager YARN, wykonaj następujące czynności:

1. Zaloguj się w [portalu Azure](https://portal.azure.com).
2. W menu po lewej stronie wybierz **Przeglądaj**, wybierz pozycję **klastrów usługi HDInsight**, a następnie wybierz klaster.
3. Twoje okienko klastra usługi HDInsight, zaznacz **pulpitu nawigacyjnego** w górnym menu, aby otworzyć Interfejs użytkownika narzędzia Ambari. Wprowadź poświadczenia logowania klastra.
4. Kliknij przycisk **YARN** na liście usług w menu po lewej stronie. Na stronie YARN wybierz **szybkie linki** , umieść kursor nad aktywnego węzła głównego, a następnie kliknij przycisk **interfejsu użytkownika Menedżera ResourceManager**.

    ![ResourceManager UI](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

Może bezpośrednio uzyskać dostęp ResourceManager interfejsu użytkownika z `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Możesz wyświetlić listę zadań wraz z ich bieżącego stanu. Zrzut ekranu jest aktualnie uruchomione zadanie co:

![Aplikacje ResourceManager interfejsu użytkownika](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Aby ręcznie kill tego uruchomienia aplikacji, uruchom następujące polecenie z poziomu powłoki SSH:

```bash
yarn application -kill <application_id>
```

Na przykład:

```bash
yarn application -kill "application_1499348398273_0003"
```

## <a name="rebalancing-an-hbase-cluster"></a>Ponowne równoważenie klastra HBase

Serwery region automatycznie równoważy w ciągu kilku minut po zakończeniu operacji skalowania. Aby ręcznie równoważyć serwerów regionu, wykonaj następujące kroki:

1. Połącz z klastrem usługi HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Uruchom powłokę HBase:

        hbase shell

3. Aby ręcznie równoważyć serwerów regionu, użyj następującego polecenia:

        balancer

## <a name="scale-down-implications"></a>Dół efekty

Jak wspomniano wcześniej, wszystkie oczekujące lub uruchomione zadania kończą się po zakończeniu skalowania operacji. Istnieją inne potencjalne skutki skalowania w dół, który może wystąpić.

## <a name="hdinsight-name-node-stays-in-safe-mode-after-scaling-down"></a>HDInsight nazwa węzła pozostaje w trybie awaryjnym po skalowania

![Skalowanie klastra](./media/hdinsight-scaling-best-practices/scale-cluster.png)

Jeśli zmniejszania klastra do co najmniej jednego procesu roboczego węzła, jak pokazano na poprzedniej ilustracji, mogą stać się zatrzymane systemu plików HDFS w trybie awaryjnym, gdy węzłów procesu roboczego są ponownie uruchamiane z powodu stosowania poprawek, lub od razu po wykonaniu operacji skalowania.

Główną przyczyną jest, że gałęzi używa kilku `scratchdir` pliki i domyślnie oczekuje repliki trzech kolejnych bloków, ale jest tylko jedna replika możliwe skala w dół do węzła minimalna jednego procesu roboczego. W rezultacie, pliki w `scratchdir` stają się *under-replikowanych*. Może to spowodować, że system plików HDFS pozostanie w trybie awaryjnym, po uruchomieniu usługi po wykonaniu operacji skalowania.

W przypadku skalowania dół próba HDInsight, opiera się na interfejsy zarządzania Ambari do najpierw zlikwidować węzłów bardzo niechcianego procesu roboczego, które replikuje ich bloki HDFS do innych węzłów procesu roboczego w trybie online, a następnie bezpiecznie skalowania klastra w dół. System plików HDFS przechodzi w tryb awaryjny podczas okna obsługi, a powinien pochodzić po zakończeniu skalowanie. Jest w tym momencie że system plików HDFS może zostać wstrzymana w trybie awaryjnym.

System plików HDFS jest konfigurowana `dfs.replication` ustawienie 3. W związku z tym bloków pliki tymczasowe pliki są under-replikowanych zawsze, gdy istnieją co najmniej trzech węzłów procesu roboczego w trybie online, ponieważ nie dostępnych nie oczekiwano trzy kopie każdego pliku bloku.

Można wykonać polecenia, aby można było przełączyć tryb awaryjny systemu plików HDFS. Na przykład jeśli wiadomo, że jest jedynym powodem jest w trybie awaryjnym, ponieważ pliki tymczasowe są under-zreplikowane, a następnie możesz bezpiecznie pozostaw trybu awaryjnego. Jest to spowodowane under-replikowanych plików są pliki tymczasowe pliki tymczasowe Hive.

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

Po opuszczeniu trybu awaryjnego, można ręcznie usunąć pliki tymczasowe lub poczekaj, aż Hive ostatecznie wyczyścić je automatycznie.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Przykład błędów po włączeniu trybu awaryjnego

* H070 nie można otworzyć sesji Hive. org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **Cannot create directory** /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. **Nazwa węzła jest w trybie awaryjnym**. Bloki zgłoszone 75 musi dodatkowe bloki 12 do osiągnięcia wartości progowej 0.9900 całkowita liczba bloków 87. Liczba datanodes na żywo 10 osiągnęła minimalnej liczby 0. Tryb awaryjny zostaną wyłączone automatycznie po osiągnięciu progów.

* Pokaż H100 nie można przesłać instrukcji baz danych: org.apache.thrift.transport.TTransportException: org.apache.http.conn.HttpHostConnectException: nawiązać hn0-clustername.servername.internal.cloudapp.net:10001 [hn0 clustername.servername . internal.cloudapp.NET/1.1.1.1] nie powiodła się: **połączenie zostało odrzucone**

* Nie można H020 ustanowić połączenie .net hn0 hdisrv.servername.bx.internal.cloudapp: 10001: org.apache.thrift.transport.TTransportException: nie można utworzyć połączenia http http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: nawiązać hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] nie powiodła się: połączenie zostało odrzucone: org.apache.thrift.transport.TTransportException: nie można utworzyć połączenia http http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: nawiązać hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] nie powiodła się: **połączenie zostało odrzucone**

* Z dzienników Hive: Ostrzeżenie [main]: serwera. Serwera HiveServer2 (HiveServer2.java:startHiveServer2(442)) — błąd podczas uruchamiania serwera HiveServer2 21, próba zostanie ponowiona w java.lang.RuntimeException 60 sekund: Wystąpił błąd podczas stosowania zasad autoryzacji w konfiguracji hive: org.apache.hadoop.ipc.RemoteException () org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **nie może utworzyć katalogu** /tmp/hive/hive/70a42b8a-9437-466e-acbe-da90b1614374. **Nazwa węzła jest w trybie awaryjnym**.
    Bloki zgłoszone 0 musi dodatkowe bloki 9 do osiągnięcia wartości progowej 0.9900 całkowita liczba bloków 9.
    Liczba datanodes na żywo 10 osiągnęła minimalnej liczby 0. **Tryb awaryjny zostaną wyłączone automatycznie po osiągnięciu progów**.
    at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1324)

Możesz przejrzeć dzienniki nazwa węzła z `/var/log/hadoop/hdfs/` folderu w pobliżu czas, kiedy został skalowania klastra, aby zobaczyć, kiedy pojawił się tryb awaryjny. Pliki dziennika są nazywane `Hadoop-hdfs-namenode-hn0-clustername.*`.

Główną przyczynę poprzednie błędy jest zależna gałęzi na pliki tymczasowe w systemie plików HDFS podczas wykonywania zapytania. Gdy system plików HDFS wprowadza tryb awaryjny, gałęzi nie można uruchomić zapytania, ponieważ nie można zapisać do systemu plików HDFS. Pliki tymczasowe w systemie plików HDFS znajdują się na dysku lokalnym zainstalowany w węźle procesu roboczego poszczególnych maszyn wirtualnych i replikowane wśród innych węzłów procesu roboczego na trzy repliki, minimalna.

`hive.exec.scratchdir` Parametru w gałęzi jest skonfigurowany w ramach `/etc/hive/conf/hive-site.xml`:

```xml
<property>
    <name>hive.exec.scratchdir</name>
    <value>hdfs://mycluster/tmp/hive</value>
</property>
```

### <a name="view-the-health-and-state-of-your-hdfs-file-system"></a>Wyświetl kondycję i stan systemu plików HDFS

Możesz wyświetlić raport o stanie z każdego węzła nazwę, aby zobaczyć, czy węzły są w trybie awaryjnym. Aby wyświetlić raport, SSH do każdego węzła głównego i uruchom następujące polecenie:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode get
```

![Wyłączony tryb awaryjny](./media/hdinsight-scaling-best-practices/safe-mode-off.png)

> [!NOTE]
> `-D` Przełącznik jest konieczne, ponieważ domyślny system plików w usłudze HDInsight jest magazynu Azure lub usługi Azure Data Lake Store. `-D` Określa, że polecenie zostanie wykonane przeciwko lokalny system plików HDFS.

Następnie można wyświetlić raportu, który zawiera szczegółowe informacje o stanie systemu plików HDFS:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -report
```

To polecenie powoduje zwrócenie w dobrej kondycji klastra, w której wszystkie bloki są replikowane do oczekiwanego stopnia następujące:

![Wyłączony tryb awaryjny](./media/hdinsight-scaling-best-practices/report.png)

Obsługuje system plików HDFS `fsck` polecenie, aby sprawdzić niezgodności z różnych plików, takich jak brakujące blokuje dla pliku lub under-replikowanych bloków. Aby uruchomić `fsck` polecenia przed `scratchdir` plików (tymczasowego dysku magazynującego):

```
hdfs fsck -D 'fs.default.name=hdfs://mycluster/' /tmp/hive/hive
```

Podczas wykonywania w dobrej kondycji systemu plików HDFS z nie under-replikowanych bloki pojawić się dane wyjściowe podobne do następującego:

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:07:01 UTC 2017
..Status: HEALTHY
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
 Minimally replicated blocks:   2 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          4
 Number of racks:               1
FSCK ended at Thu Jul 06 20:07:01 UTC 2017 in 3 milliseconds


The filesystem under path '/tmp/hive/hive' is HEALTHY
```

W przypadku, gdy `fsck` polecenie jest wykonywane w systemie plików HDFS z niektórych under-replikowanych bloków, wynik jest podobny do następującego:

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:13:58 UTC 2017
.
/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info:  Under replicated BP-1867508080-10.0.0.21-1499348422953:blk_1073741826_1002. Target Replicas is 3 but found 1 live replica(s), 0 decommissioned replica(s) and 0 decommissioning replica(s).
.
/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: CORRUPT blockpool BP-1867508080-10.0.0.21-1499348422953 block blk_1073741825

/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: MISSING 1 blocks of total size 26 B.Status: CORRUPT
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
  ********************************
  UNDER MIN REPL'D BLOCKS:      1 (50.0 %)
  dfs.namenode.replication.min: 1
  CORRUPT FILES:        1
  MISSING BLOCKS:       1
  MISSING SIZE:         26 B
  CORRUPT BLOCKS:       1
  ********************************
 Minimally replicated blocks:   1 (50.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       1 (50.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     0.5
 Corrupt blocks:                1
 Missing replicas:              2 (33.333332 %)
 Number of data-nodes:          1
 Number of racks:               1
FSCK ended at Thu Jul 06 20:13:58 UTC 2017 in 28 milliseconds


The filesystem under path '/tmp/hive/hive' is CORRUPT
```

Można również wyświetlić stan systemu plików HDFS w interfejsie użytkownika narzędzia Ambari, wybierając **HDFS** usługi, po lewej stronie, lub z `https://<HDInsightClusterName>.azurehdinsight.net/#/main/services/HDFS/summary`.

![Stan systemu plików HDFS Ambari](./media/hdinsight-scaling-best-practices/ambari-hdfs.png)

Może również zostać wyświetlony co najmniej jeden błąd krytyczny na NameNodes aktywnych lub wstrzymania. Aby wyświetlić kondycję bloki NameNode, wybierz łącze NameNode obok alertu.

![NameNode bloki kondycji](./media/hdinsight-scaling-best-practices/ambari-hdfs-crit.png)

Aby czyszczenie pliki tymczasowe pliki, które usuwa błędy replikacji bloków SSH do każdego węzła głównego i uruchom następujące polecenie:

```
hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
```

> [!NOTE]
> Jeśli niektóre zadania są nadal uruchomione polecenie mogą być dzielone Hive.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode-due-to-under-replicated-blocks"></a>Jak zapobiec HDInsight gromadzą się w trybie awaryjnym z powodu under-replikowanych bloków

Istnieje kilka sposobów, aby zapobiec pozostaje w trybie awaryjnym HDInsight:

* Przed skalowania usługi HDInsight, należy zatrzymać wszystkie zadania Hive. Alternatywnie zaplanować skali procesu, aby zapobiec konfliktom między uruchomionych zadań Hive w dół.
* Ręcznie wyczyścić podstaw gałęzi `tmp` katalogu plików w systemie plików HDFS przed skalowania.
* Skalować tylko HDInsight w dół do trzech węzłów procesu roboczego minimalnej. Unikaj przechodzi najmniejsza węzłów jednego procesu roboczego.
* W razie potrzeby, uruchom polecenie pozostawienie w trybie awaryjnym.

W poniższych sekcjach opisano te opcje.

#### <a name="stop-all-hive-jobs"></a>Zatrzymaj wszystkie zadania Hive

Przed skalowania do węzła jednego procesu roboczego, należy zatrzymać wszystkie zadania Hive. Jeżeli obciążenie jest zaplanowane, wykonujących skali dół po ukończeniu pracy Hive.

Dzięki temu można zminimalizować liczbę pliki tymczasowe pliki w folderze tmp (jeśli istnieje).

#### <a name="manually-clean-up-hives-scratch-files"></a>Ręcznie wyczyścić pliki tymczasowe pliki gałęzi

Jeśli gałąź ma pozostawione pliki tymczasowe, a następnie możesz ręcznie wyczyścić tych plików przed skalowanie w dół do uniknięcia trybu awaryjnego.

1. Zatrzymaj usługi Hive i upewnij się, że wszystkie zadania i zapytań zostały zakończone.

2. Wyświetl listę zawartości `hdfs://mycluster/tmp/hive/` katalogu, aby sprawdzić, czy zawiera wszystkie pliki:

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```
    
    Oto przykładowe dane wyjściowe, gdy pliki istnieją:

    ```
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Jeśli wiesz, że Hive jest wykonywane przy użyciu tych plików, można je usunąć. Pamiętaj, że gałęzi nie ma żadnych zapytań uruchomiony przez wyszukiwanie w interfejsie użytkownika Yarn ResourceManager strony.

    Przykład wiersz polecenia, aby usunąć pliki z systemu plików HDFS:

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```
    
#### <a name="scale--hdinsight-to-three-worker-nodes"></a>HDInsight skali do trzech węzłów procesu roboczego

Jeśli gromadzą się w trybie awaryjnym jest trwały problem i poprzednie kroki nie są opcje, a następnie można uniknąć problemu, skalując tylko do trzech węzłów procesu roboczego. To może nie być optymalne, ze względu na ograniczenia kosztów, w porównaniu do skalowania do jednego węzła. Jednak z tylko jednym węzłem procesu roboczego, system plików HDFS nie może zagwarantować, że trzy repliki danych są dostępne w klastrze.

#### <a name="run-the-command-to-leave-safe-mode"></a>Uruchom polecenie, aby zakończyć tryb awaryjny

Ostatnia opcja ma wyszukiwać w rzadkich przypadkach, w którym system plików HDFS wprowadza tryb awaryjny, a następnie wykonaj polecenie pozostaw trybu awaryjnego. Po określeniu, dlatego system plików HDFS wprowadził tryb awaryjny wynika z plików gałęzi jest under-replikowany, uruchom następujące polecenie, aby wyjść z trybu bezpiecznego:

* HDInsight w systemie Linux:

    ```bash
    hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
    ```
    
* HDInsight w systemie Windows:

    ```bash
    hdfs dfsadmin -fs hdfs://headnodehost:9000 -safemode leave
    ```
    
## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md)
* [Klastry skali](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari](hdinsight-hadoop-manage-ambari.md)
