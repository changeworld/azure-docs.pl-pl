---
title: Skalowanie klastrów rozmiarów — Azure HDInsight
description: Skalowanie klastra usługi HDInsight do obciążenia.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: ashish
ms.openlocfilehash: a172024e4662e647b39fe999f1be3cfcef04b5ce
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698244"
---
# <a name="scale-hdinsight-clusters"></a>Skaluj klastry HDInsight

HDInsight zapewnia elastyczność, oferując możliwość skalowania w górę i skalowania w dół liczbę węzłów procesu roboczego w klastrach. Dzięki temu można zmniejszyć klastra po godzinach lub w weekendy i rozwiń go podczas szczytowego zapotrzebowania biznesowych.

Na przykład jeśli masz jakieś operacje przetwarzania wsadowego tak się stanie, raz dziennie lub raz w miesiącu, klaster HDInsight może być skalowany za kilka minut przed tym zaplanowane zdarzenie będzie odpowiedniej ilości pamięci i mocy obliczeniowej procesora CPU.  Później po zakończeniu przetwarzania i użycie pogarsza, możesz skalować w dół do mniejszej liczby węzłów procesu roboczego klastra HDInsight.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="utilities-to-scale-clusters"></a>Narzędzia Skalowanie klastrów

Firma Microsoft udostępnia następujące narzędzia, które skalowanie klastrów:

|Narzędzie | Opis|
|---|---|
|[Az programu PowerShell](https://docs.microsoft.com/powershell/azure)|[Zestaw AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) - ClusterName \<nazwa klastra > - TargetInstanceCount \<NewSize >|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Zestaw AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) - ClusterName \<nazwa klastra > - TargetInstanceCount \<NewSize >|
|[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)|[az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --resource-group \<Resource group> --name \<Cluster Name> --target-instance-count \<NewSize>|
|[Klasyczny interfejs wiersza polecenia Azure](hdinsight-administer-use-command-line.md)|zmiany rozmiaru klastra usługi Azure hdinsight \<Nazwa_klastra > \<liczba wystąpień docelowy >|
|[Azure Portal](https://portal.azure.com)|Otwórz okienko klastra usługi HDInsight, wybierz opcję **rozmiar klastra** w menu po lewej stronie, a następnie w okienku rozmiaru klastra, wpisz liczbę węzłów procesu roboczego i wybierz przycisk Zapisz.|  

![Skalowanie klastra](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Przy użyciu dowolnej z tych metod, można skalować klastra usługi HDInsight w górę lub w dół w ciągu kilku minut.

> [!IMPORTANT]  
> * Klasyczny Azure interfejs wiersza polecenia jest przestarzały i powinna służyć wyłącznie przy użyciu klasycznego modelu wdrażania. W przypadku wszystkich innych wdrożeń, użyj [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * Moduł PowerShell AzureRM jest przestarzały.  Użyj [modułu Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) zawsze, gdy jest to możliwe.

## <a name="scaling-impacts-on-running-jobs"></a>Skalowanie wpływu na uruchomione zadania

Gdy użytkownik **Dodaj** węzłów z klastrem HDInsight uruchomione, nie będzie mieć wpływ na wszystkie oczekujące lub uruchomione zadania. Ponadto nowe zadania można bezpiecznie przesyłać proces skalowania jest uruchomiona. Jeśli operacje skalowania się nie powieść z jakiegokolwiek powodu, awarii bez problemu zmieniała odbywa się, pozostawiając klastra w stanie działać.

Jednak jeśli skalowanie w dół klastra przez **usuwanie** węzłów, wszystkie oczekujące lub uruchomione zadania zakończą się niepowodzeniem po zakończeniu operacji skalowania. Ten błąd jest spowodowany niektóre z tych usług, ponownego uruchamiania podczas procesu.

Aby rozwiązać ten problem, możesz poczekać na ukończenie przed skalowaniem klaster, ręcznie zakończyć zadania, próby lub ponawiania przesyłania zadań po zakończył operację skalowania zadań.

Aby wyświetlić listę oczekujących i uruchomionych zadań, można użyć interfejsu użytkownika Menedżera zasobów YARN, wykonaj następujące czynności:

1. Zaloguj się w [portalu Azure](https://portal.azure.com).
2. Z lewej strony, przejdź do **wszystkich usług** > **Analytics** > **klastry HDInsight**, a następnie wybierz klaster.
3. W widoku głównego, przejdź do **pulpity nawigacyjne klastra** > **Ambari macierzystego**. Wprowadź poświadczenia logowania do klastra.
4. Wybierz z interfejsu użytkownika Ambari **YARN** na liście usług, w menu po lewej stronie.  
5. Na stronie usługi YARN wybierz **szybkich łączy** i umieść kursor nad aktywnego węzła głównego, a następnie wybierz **interfejsu użytkownika Menedżera zasobów**.

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

## <a name="rebalancing-an-apache-hbase-cluster"></a>Ponowne równoważenie klastra Apache HBase

Serwery regionów są automatycznie równoważone w ciągu kilku minut, po zakończeniu operacji skalowania. Ręcznie równoważyć serwery regionów, wykonaj następujące kroki:

1. Łączenie z klastrem HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Uruchom powłokę HBase:

        hbase shell

3. Aby ręcznie równoważyć serwery regionów, użyj następującego polecenia:

        balancer

## <a name="scale-down-implications"></a>Skaluj w dół skutki

Jak wspomniano wcześniej, wszystkie oczekujące lub uruchomione zadania są kończone po zakończeniu skalowanie w dół operacji. Istnieją jednak inne implikacje potencjalnych do skalowania w dół, które mogą wystąpić.

## <a name="hdinsight-name-node-stays-in-safe-mode-after-scaling-down"></a>HDInsight nazwa węzła pozostaje w trybie awaryjnym po skalowanie w dół

Zmniejszania klastra w dół do co najmniej jednego procesu roboczego węzła Apache HDFS może stać się zatrzymany w trybie awaryjnym podczas węzłów procesu roboczego wykonywany jest ponowny rozruch z powodu stosowania poprawek lub od razu po wykonaniu operacji skalowania.

Podstawową przyczyną tego jest, że gałąź używa kilku `scratchdir` pliki i domyślnie oczekuje, że trzy repliki każdy blok, ale istnieje tylko jedna replika możliwe skalowanie w dół do minimalnej węzła jednego procesu roboczego. W rezultacie, pliki w `scratchdir` stają się *under-replikowanych*. Może to spowodować, że system plików HDFS pozostać w trybie awaryjnym, gdy usługi są ponownie uruchamiane po zakończeniu operacji skalowania.

W przypadku skalowania w dół próba HDInsight opiera się na interfejsów zarządzania Apache Ambari, aby najpierw likwidowanie węzłów procesu roboczego bardzo niepożądane, które replikują ich bloki systemu plików HDFS do innych węzłów procesu roboczego w trybie online, a następnie bezpiecznie skalowanie klastra w dół. System plików HDFS przechodzi w trybie awaryjnym podczas okna obsługi, a powinien pochodzić po zakończeniu skalowanie. Jest na tym etapie, system plików HDFS może zostać wstrzymana w trybie awaryjnym.

Skonfigurowano systemu plików HDFS `dfs.replication` ustawienie 3. W związku z tym bloki konstrukcyjne pliki tymczasowe pliki są under-replikowanych zawsze wtedy, gdy istnieją mniej niż trzy węzły procesu roboczego w trybie online, ponieważ nie oczekiwano trzy kopie każdego bloku plików są dostępne.

Można wykonać polecenia, aby można było przełączyć tryb awaryjny systemu plików HDFS. Na przykład jeśli wiesz, że jest jedyny przypadek, kiedy jest w trybie awaryjnym na pliki tymczasowe są under-zreplikowane, a następnie możesz bezpiecznie opuścić tryb awaryjny. Jest to spowodowane under-replikowanych plików są pliki tymczasowe pliki tymczasowe Hive.

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

Po wyjściu z trybu awaryjnego, można ręcznie usunąć pliki tymczasowe lub poczekaj, aż gałęzi po pewnym czasie pora to oczyścić automatycznie.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Przykładowe błędy, gdy jest włączony tryb awaryjny

* H070 nie można otworzyć sesji programu Hive. org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **Cannot create directory** /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. **Nazwa węzła jest w trybie awaryjnym**. Bloki zgłoszonych 75 musi dodatkowe bloki 12 do osiągnięcia progu 0.9900 łączna liczba bloków 87. Liczba na żywo datanodes 10 została osiągnięta minimalny numer 0. Tryb awaryjny być wyłączona automatycznie, gdy progi zostały osiągnięte.

* Wyświetl H100 nie można przesłać instrukcji bazy danych: org.apache.thrift.transport.TTransportException: org.apache.http.conn.HttpHostConnectException: Nawiązać połączenie z hn0-clustername.servername.internal.cloudapp.net:10001 [hn0 clustername.servername. internal.cloudapp.net/1.1.1.1] failed: **Połączenie zostało odrzucone**

* Nie można H020 ustanowić połączenie hn0 hdisrv.servername.bx.internal.cloudapp .net: 10001: org.apache.thrift.transport.TTransportException: Nie można utworzyć połączenia http http:\//hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: Nawiązywanie połączenia hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] nie powiodło się: Połączenie zostało odrzucone: org.apache.thrift.transport.TTransportException: Nie można utworzyć połączenia http http:\//hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: Nawiązywanie połączenia hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] nie powiodło się: **Połączenie zostało odrzucone**

* Z dzienniki programu Hive: OSTRZEGAJ [main]: serwer. Usługi HiveServer2 (HiveServer2.java:startHiveServer2(442)) — wystąpił błąd podczas uruchamiania usługi HiveServer2 przy próbie 21, spróbuje ponowić operację w 60 sekund java.lang.RuntimeException: Błąd podczas stosowania zasad autoryzacji w konfiguracji programu hive: org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **Nie można utworzyć katalogu** /tmp/hive/hive/70a42b8a-9437-466e-acbe-da90b1614374. **Nazwa węzła jest w trybie awaryjnym**.
    Bloki zgłoszonych 0 wymaga dodatkowe bloki 9 do osiągnięcia progu 0.9900 łączna liczba bloków 9.
    Liczba na żywo datanodes 10 została osiągnięta minimalny numer 0. **Tryb awaryjny zostaną wyłączone automatycznie po progi zostały osiągnięte**.
    at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1324)

Możesz przejrzeć dzienniki węzła nazwy z `/var/log/hadoop/hdfs/` folderu zbliżonym czasie, gdy została przeskalowana klastra, aby zobaczyć, po wprowadzeniu go trybu awaryjnego. Pliki dziennika są nazywane `Hadoop-hdfs-namenode-hn0-clustername.*`.

Przyczyny poprzednie błędy to, czy gałąź jest zależna od pliki tymczasowe w systemie plików HDFS podczas uruchamiania zapytań. Gdy system plików HDFS wejdzie w trybie awaryjnym, gałąź nie można uruchomić zapytania, ponieważ nie można zapisać do systemu plików HDFS. Pliki tymczasowe w systemie plików HDFS znajdują się na dysku lokalnym, zainstalowany w węźle procesu roboczego poszczególnych maszyn wirtualnych i replikowane wśród innych węzłów procesu roboczego na trzech replik, minimalna.

`hive.exec.scratchdir` Parametru w gałęzi jest konfigurowany w `/etc/hive/conf/hive-site.xml`:

```xml
<property>
    <name>hive.exec.scratchdir</name>
    <value>hdfs://mycluster/tmp/hive</value>
</property>
```

### <a name="view-the-health-and-state-of-your-hdfs-file-system"></a>Wyświetl kondycję i stan systemu plików HDFS

Można wyświetlić raport o stanie z każdego węzła nazwę, aby sprawdzić, czy węzły są w trybie awaryjnym. Aby wyświetlić raport, SSH do każdego węzła głównego i uruchom następujące polecenie:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode get
```

![Wyłączony tryb awaryjny](./media/hdinsight-scaling-best-practices/safe-mode-off.png)

> [!NOTE]  
> `-D` Przełącznik jest konieczne, ponieważ domyślny system plików w HDInsight to usługa Azure Storage lub Azure Data Lake Storage. `-D` Określa, że polecenie zostanie wykonane względem lokalnego systemu plików HDFS.

Następnie możesz wyświetlić raport zawierający szczegółowe informacje o stanie systemu plików HDFS:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -report
```

To polecenie powoduje zwrócenie w dobrej kondycji klastra, w której wszystkie bloki są replikowane do oczekiwanej stopień następujące:

![Wyłączony tryb awaryjny](./media/hdinsight-scaling-best-practices/report.png)

Obsługuje system plików HDFS `fsck` polecenie, aby sprawdzić niezgodności z różnych plików, takich jak brakujące blokuje dla pliku lub under-replikowanych bloków. Aby uruchomić `fsck` polecenia względem `scratchdir` plików (pliki tymczasowe dysku tymczasowego):

```
hdfs fsck -D 'fs.default.name=hdfs://mycluster/' /tmp/hive/hive
```

Podczas wykonywania w dobrej kondycji systemu plików HDFS przy użyciu nie under-replikowanych bloków, zobaczysz dane wyjściowe podobne do następujących:

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

W odróżnieniu od nich, gdy `fsck` polecenie jest wykonywane w systemie plików HDFS przy użyciu niektórych under-replikowanych bloków, dane wyjściowe są podobne do następujących:

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

Możesz również wyświetlić stan systemu plików HDFS w interfejsie użytkownika Ambari, wybierając **HDFS** usługi po lewej stronie lub przy użyciu `https://<HDInsightClusterName>.azurehdinsight.net/#/main/services/HDFS/summary`.

![Stan systemu plików HDFS systemu Ambari](./media/hdinsight-scaling-best-practices/ambari-hdfs.png)

Na NameNodes aktywnych lub wstrzymania, może również sprawdzić jeden lub więcej błędów krytycznych. Aby wyświetlić kondycję bloki NameNode, wybierz link NameNode obok alertu.

![NameNode bloki kondycji](./media/hdinsight-scaling-best-practices/ambari-hdfs-crit.png)

Aby wyczyścić pliki tymczasowe pliki, które należy usunąć błędy replikacji bloku, SSH do każdej z nich głównymi węzła i uruchom następujące polecenie:

```
hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
```

> [!NOTE]  
> To polecenie może przerwać gałęzi, jeśli niektóre zadania są wykonywane.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode-due-to-under-replicated-blocks"></a>Jak uniemożliwić HDInsight gromadzą się w trybie awaryjnym, ze względu na bloki under-replikowane

Istnieje kilka sposobów, aby uniemożliwić pozostaje w trybie awaryjnym HDInsight:

* Przed skalowanie w dół HDInsight, należy zatrzymać wszystkie zadania Hive. Alternatywnie można zaplanować skalowania w dół do procesu, aby uniknąć ryzyka powstania konfliktu z uruchamiania zadań Hive.
* Ręcznie wyczyścić podstaw gałęzi `tmp` pliki katalogu w systemie plików HDFS przed skalowanie w dół.
* Tylko skalować w dół HDInsight z trzema węzłami procesu roboczego minimalnej. Należy unikać, przechodząc zaledwie jednego procesu roboczego węzła.
* Jeśli to konieczne, należy uruchomić polecenie, aby opuścić tryb awaryjny.

W poniższych sekcjach opisano te opcje.

#### <a name="stop-all-hive-jobs"></a>Zatrzymanie wszystkich zadań Hive

Przed skalowanie w dół do węzła jednego procesu roboczego, należy zatrzymać wszystkie zadania Hive. Jeżeli obciążenie jest zaplanowane, wykonujących dół usługi po zakończeniu pracy programu Hive.

Dzięki temu można zminimalizować liczbę pliki tymczasowe pliki w folderze tmp (jeśli istnieje).

#### <a name="manually-clean-up-hives-scratch-files"></a>Ręcznie wyczyścić pliki tymczasowe pliki w gałęzi

Jeśli gałąź ma pozostać plików tymczasowych, a następnie możesz ręcznie wyczyścić tych plików przed skalowane w dół do uniknąć trybu awaryjnego.

1. Zatrzymywanie usług programu Hive i upewnij się, że wszystkie zadania i zapytania są wykonywane.

2. Wyświetlanie zawartości `hdfs://mycluster/tmp/hive/` katalogu, aby ustalić, czy zawiera wszystkie pliki:

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
    
#### <a name="scale--hdinsight-to-three-worker-nodes"></a>HDInsight skalowania do trzech węzłów procesu roboczego

Jeśli gromadzą się w trybie awaryjnym trwałego problemu i poprzednie kroki nie są opcje, a następnie chcesz uniknąć problemu, tylko skalowanie w dół do trzech węzłów procesu roboczego. To może nie być optymalne, ze względu na ograniczenia kosztów, w porównaniu do skalowania w dół do jednego węzła. Jednak z tylko jednym węzłem procesu roboczego, system plików HDFS nie gwarantuje, że trzy repliki danych są dostępne w klastrze.

#### <a name="run-the-command-to-leave-safe-mode"></a>Uruchom polecenie, aby opuścić tryb awaryjny

Ostatnia opcja polega na obserwowanie rzadkich przypadkach, w którym system plików HDFS wprowadza tryb awaryjny, a następnie wykonaj polecenie Tryb awaryjny pozostaw. Po określeniu, czy przyczyną systemu plików HDFS wprowadził tryb awaryjny wynika z plików programu Hive under-replikowane, wykonaj następujące polecenie, aby opuścić tryb awaryjny:

* HDInsight w systemie Linux:

    ```bash
    hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
    ```
    
* HDInsight w Windows:

    ```bash
    hdfs dfsadmin -fs hdfs://headnodehost:9000 -safemode leave
    ```
    
## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-introduction.md)
* [Skalowanie klastrów](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Zarządzanie klastrami HDInsight przy użyciu internetowego interfejsu użytkownika systemu Apache Ambari](hdinsight-hadoop-manage-ambari.md)
