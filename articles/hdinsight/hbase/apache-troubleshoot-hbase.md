---
title: Rozwiązywanie problemów z HBase za pomocą usługi Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące pracy z usługą HBase i usługą Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 13a4831d946eb7e25e586cafae4cae51b49fd8a7
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780769"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z programem Apache HBase przy użyciu usługi Azure HDInsight

Poznaj najważniejsze problemy i ich rozwiązania podczas pracy z ładunkiem HBase w usłudze Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Jak mogę uruchomić raporty poleceń hbck z wieloma nieprzypisanymi regionami?

Typowym komunikatem o błędzie, który może zostać wyświetlony po `hbase hbck` uruchomieniu polecenia, jest "wiele regionów, które są nieprzypisane lub otwory w łańcuchu regionów".

W interfejsie użytkownika HBase Master można zobaczyć liczbę regionów, które są niezrównoważone na wszystkich serwerach regionów. Następnie można uruchomić `hbase hbck` polecenie, aby zobaczyć otwory w łańcuchu regionów.

Otwory mogą być powodowane przez regiony w trybie offline, więc najpierw należy usunąć przypisania. 

Aby przywrócić nieprzypisane regiony z powrotem do normalnego stanu, wykonaj następujące czynności:

1. Zaloguj się do klastra HBase usługi HDInsight przy użyciu protokołu SSH.
2. Aby nawiązać połączenie z powłoką Apache ZooKeeper, `hbase zkcli` Uruchom polecenie.
3. `rmr /hbase/regions-in-transition` Uruchom polecenie`rmr /hbase-unsecure/regions-in-transition` lub polecenie.
4. Aby wyjść z `hbase zkcli` powłoki, `exit` Użyj polecenia.
5. Otwórz interfejs użytkownika Apache Ambari, a następnie uruchom ponownie usługę Active HBase Master.
6. Uruchom ponownie `hbase hbck` polecenie (bez żadnych opcji). Sprawdź dane wyjściowe tego polecenia, aby upewnić się, że wszystkie regiony są przypisane.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Jak mogę rozwiązać problemy z limitem czasu podczas korzystania z poleceń hbck dla przypisań regionów?

### <a name="issue"></a>Problem

Potencjalną przyczyną problemów z przekroczeniem limitu czasu `hbck` , gdy użycie polecenia może być w stanie "w przejściu" przez długi czas. Te regiony można zobaczyć jako offline w interfejsie użytkownika HBase Master. Ze względu na to, że duża liczba regionów próbuje przejść, HBase Master może przekroczyć limitu czasu i nie może przywrócić tych regionów z powrotem do trybu online.

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Zaloguj się do klastra HBase usługi HDInsight przy użyciu protokołu SSH.
2. Aby nawiązać połączenie z powłoką Apache ZooKeeper, `hbase zkcli` Uruchom polecenie.
3. `rmr /hbase/regions-in-transition` Uruchom polecenie`rmr /hbase-unsecure/regions-in-transition` lub.
4. Aby wyjść `hbase zkcli` z powłoki, `exit` Użyj polecenia.
5. W interfejsie użytkownika Ambari Uruchom ponownie usługę Active HBase Master.
6. Ponownie uruchom `hbase hbck -fixAssignments` polecenie.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Jak mogę wymusić wyłączenie bezpiecznego trybu HDFS w klastrze?

### <a name="issue"></a>Problem

Lokalny rozproszony system plików Apache Hadoop (HDFS) jest zablokowany w trybie awaryjnym w klastrze usługi HDInsight.

### <a name="detailed-description"></a>Szczegółowy opis

Ten błąd może być spowodowany błędem po uruchomieniu następującego polecenia HDFS:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

Błąd, który może się pojawić podczas próby uruchomienia polecenia, wygląda następująco:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>Prawdopodobna przyczyna

Klaster usługi HDInsight został przeskalowany w dół do kilku węzłów. Liczba węzłów znajduje się poniżej lub blisko współczynnika replikacji systemu plików HDFS.

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów 

1. Aby uzyskać stan systemu plików HDFS w klastrze usługi HDInsight, należy uruchomić następujące polecenia:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   Safe mode is ON
   Configured Capacity: 3372381241344 (3.07 TB)
   Present Capacity: 3138625077248 (2.85 TB)
   DFS Remaining: 3102710317056 (2.82 TB)
   DFS Used: 35914760192 (33.45 GB)
   DFS Used%: 1.14%
   Under replicated blocks: 0
   Blocks with corrupt replicas: 0
   Missing blocks: 0
   Missing blocks (with replication factor 1): 0

   -------------------------------------------------
   Live datanodes (8):

   Name: 10.0.0.17:30010 (10.0.0.17)
   Hostname: 10.0.0.17
   Decommission Status : Normal
   Configured Capacity: 421547655168 (392.60 GB)
   DFS Used: 5288128512 (4.92 GB)
   Non DFS Used: 29087272960 (27.09 GB)
   DFS Remaining: 387172253696 (360.58 GB)
   DFS Used%: 1.25%
   DFS Remaining%: 91.85%
   Configured Cache Capacity: 0 (0 B)
   Cache Used: 0 (0 B)
   Cache Remaining: 0 (0 B)
   Cache Used%: 100.00%
   Cache Remaining%: 0.00%
   Xceivers: 2
   Last contact: Wed Apr 05 16:22:00 UTC 2017
   ...

   ```
2. Możesz również sprawdzić integralność systemu plików HDFS w klastrze usługi HDInsight przy użyciu następujących poleceń:

   ```apache
   hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
   ```

   ```apache
   Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
   FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
   ....................................................................................................

   ....................................................................................................
   ..................Status: HEALTHY
   Total size:    9330539472 B
   Total dirs:    37
   Total files:   2618
   Total symlinks:                0 (Files currently being written: 2)
   Total blocks (validated):      2535 (avg. block size 3680686 B)
   Minimally replicated blocks:   2535 (100.0 %)
   Over-replicated blocks:        0 (0.0 %)
   Under-replicated blocks:       0 (0.0 %)
   Mis-replicated blocks:         0 (0.0 %)
   Default replication factor:    3
   Average block replication:     3.0
   Corrupt blocks:                0
   Missing replicas:              0 (0.0 %)
   Number of data-nodes:          8
   Number of racks:               1
   FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds

   The filesystem under path '/' is HEALTHY
   ```

3. Jeśli okaże się, że nie ma żadnych uszkodzonych lub niereplikowanych bloków albo że te bloki można zignorować, uruchom następujące polecenie, aby przełączyć węzeł nazwy z trybu awaryjnego:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Jak mogę rozwiązać problemy z połączeniem JDBC lub SqlLine z Apache Phoenix?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

Aby nawiązać połączenie z Apache Phoenix, należy podać adres IP aktywnego węzła Apache ZooKeeper. Upewnij się, że usługa dozorcy, do której sqlline.py próbuje nawiązać połączenie, jest uruchomiona.
1. Zaloguj się do klastra usługi HDInsight przy użyciu protokołu SSH.
2. Wprowadź następujące polecenie:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > Adres IP aktywnego węzła dozorcy można uzyskać z interfejsu użytkownika Ambari. Przejdź do **HBase** > **szybkie linki** > **ZK(\* aktywne)**  > **dozorcy info**. 

3. Jeśli sqlline.py nawiązuje połączenie z usługą Phoenix i nie przekroczy limitu czasu, uruchom następujące polecenie, aby sprawdzić dostępność i kondycję Phoenix:

   ```apache
           !tables
           !quit
   ```      
4. Jeśli to polecenie działa, nie ma problemu. Adres IP podany przez użytkownika może być nieprawidłowy. Jeśli jednak polecenie zostanie wstrzymane przez dłuższy czas, a następnie zostanie wyświetlony następujący błąd, przejdź do kroku 5.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Uruchom następujące polecenia z węzła głównego (hn0), aby zdiagnozować warunek systemu Phoenix. Tabela wykazu:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   Polecenie powinno zwrócić błąd podobny do następującego: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. W interfejsie użytkownika Apache Ambari wykonaj następujące kroki, aby ponownie uruchomić usługę serwera hmaster na wszystkich węzłach dozorcy:

    1. W sekcji **Podsumowanie** HBase przejdź do **HBase** > **aktywnego HBase Master**. 
    2. W sekcji **składniki** Uruchom ponownie usługę HBase Master.
    3. Powtórz te kroki dla wszystkich pozostałych usług **HBase Master w stanie wstrzymania** . 

Zakończenie procesu odzyskiwania przez usługę HBase Master może potrwać do 5 minut. Po kilku minutach Powtórz sqlline.py polecenia, aby upewnić się, że SYSTEM. Tabela wykazu jest nieaktualna i można z niej wykonywać zapytania. 

Gdy SYSTEM. Tabela wykazu jest wsteczna, problem z łącznością z Phoenix powinien zostać automatycznie rozwiązany.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Co sprawia, że uruchomienie serwera głównego nie powiodło się?

### <a name="error"></a>Błąd 

Wystąpił błąd niepodzielnej zmiany nazwy.

### <a name="detailed-description"></a>Szczegółowy opis

W trakcie procesu uruchamiania serwera hmaster wykonuje wiele kroków inicjalizacji. Obejmują one przeniesienie danych z folderu Scratch (. tmp) do folderu danych. Serwera hmaster również przegląda folder dzienników zapisu (WALs), aby sprawdzić, czy istnieją nieodpowiadające serwery regionów i tak dalej. 

Podczas uruchamiania serwera hmaster wykonuje podstawowe `list` polecenie dla tych folderów. Jeśli w dowolnym momencie serwera hmaster widzi nieoczekiwany plik w żadnym z tych folderów, zgłosi wyjątek i nie zostanie uruchomiony.  

### <a name="probable-cause"></a>Prawdopodobna przyczyna

W dziennikach serwera regionów spróbuj zidentyfikować oś czasu tworzenia pliku, a następnie sprawdź, czy wystąpił awaria procesu podczas tworzenia pliku. (Skontaktuj się z pomocą techniczną HBase, aby Ci pomóc). Pomaga nam zapewnić bardziej niezawodne mechanizmy, dzięki czemu można uniknąć tego błędu i zapewnić bezpieczne zamykanie procesów.

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

Sprawdź stos wywołań i spróbuj ustalić, który folder może powodować problem (na przykład może być folderem WALs lub folderem. tmp). Następnie w Eksploratorze chmury lub przy użyciu poleceń systemu plików HDFS Spróbuj zlokalizować plik problemu. Zwykle jest \*to plik-renamePending. JSON. (Plik \*-renamePending. JSON to plik dziennika, który służy do implementowania operacji niepodzielnej zmiany nazwy w sterowniku WASB. Ze względu na błędy w tej implementacji te pliki mogą pozostać po awarii procesu i tak dalej.) Wymuś usunięcie tego pliku w programie Cloud Explorer lub przy użyciu systemu plików HDFS. 

Czasami może być również plik tymczasowy o nazwie, taki jak *$ $ $. $ $ $* w tej lokalizacji. Aby wyświetlić ten plik, `ls` należy użyć systemu HDFS. plik nie jest widoczny w programie Cloud Explorer. Aby usunąć ten plik, użyj polecenia `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`HDFS.  

Po uruchomieniu tych poleceń serwera hmaster powinny zacząć działać od razu. 

### <a name="error"></a>Błąd

W *HBase: meta* dla regionu XXX nie jest wyświetlany żaden adres serwera.

### <a name="detailed-description"></a>Szczegółowy opis

W klastrze systemu Linux może zostać wyświetlony komunikat informujący o tym, że tabela *HBase: meta* nie jest w trybie online. Uruchomienie `hbck` może zgłosić, że w żadnym regionie nie znaleziono "HBase: meta Table replicaId 0". Problem może być taki, że nie można zainicjować serwera hmaster po ponownym uruchomieniu HBase. W dziennikach serwera hmaster może zostać wyświetlony komunikat: "Brak adresu serwera wymienionego w HBase: meta for region HBase: \<nazwa\>regionu kopii zapasowej".  

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. W HBase Shell wprowadź następujące polecenia (Zmień wartości rzeczywiste stosownie do potrzeb):  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Usuń wpis *HBase: Namespace* . Ten wpis może być tym samym błędem, który jest raportowany podczas skanowania tabeli *HBase: Namespace* .

3. Aby wyświetlić HBase w stanie uruchomienia, w interfejsie użytkownika Ambari Uruchom ponownie usługę Active serwera hmaster.  

4. W powłoce HBase, aby wyświetlić wszystkie tabele w trybie offline, uruchom następujące polecenie:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>Materiały uzupełniające

[Nie można przetworzyć tabeli HBase](https://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Błąd

Serwera hmaster limit czasu w przypadku wyjątku krytycznego podobnego do "Java. IO. IOException: TimedOut 300000ms oczekiwanie na przypisanie tabeli przestrzeni nazw.

### <a name="detailed-description"></a>Szczegółowy opis

Ten problem może wystąpić, jeśli masz wiele tabel i regionów, które nie zostały opróżnione po ponownym uruchomieniu usług serwera hmaster. Ponowne uruchomienie może zakończyć się niepowodzeniem i zobaczysz poprzedni komunikat o błędzie.  

### <a name="probable-cause"></a>Prawdopodobna przyczyna

Jest to znany problem dotyczący usługi serwera hmaster. Ogólne zadania uruchamiania klastra mogą zająć dużo czasu. Serwera hmaster zamyka się, ponieważ tabela przestrzeni nazw nie jest jeszcze przypisana. Dzieje się tak tylko w scenariuszach, w których istnieją duże ilości nieopróżnionych danych i przekroczenie limitu czasu wynoszącego pięć minut.
  
### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. W interfejsie użytkownika Apache Ambari przejdź do pozycji **HBase** > **configs**. W pliku Custom HBase-site. XML Dodaj następujące ustawienie: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Uruchom ponownie wymagane usługi (serwera hmaster, a także inne usługi HBase).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Co powoduje niepowodzenie ponownego uruchomienia na serwerze regionów?

### <a name="issue"></a>Problem

Poniższe najlepsze rozwiązania mogą uniemożliwić niepowodzenie ponownego uruchamiania na serwerze regionów. Zalecane jest wstrzymanie działania dużego obciążenia, gdy planujesz ponowne uruchomienie serwerów regionów HBase. Jeśli aplikacja nadal nawiązuje połączenie z serwerami regionów, gdy trwa zamykanie, operacja ponownego uruchomienia serwera regionu będzie wolniejsza o kilka minut. Ponadto dobrym pomysłem jest pierwsze opróżnianie wszystkich tabel. Aby uzyskać informacje na temat sposobu opróżniania tabel, [Zobacz HDInsight HBase: Jak zwiększyć czas ponownego uruchamiania klastra Apache HBase przez opróżnianie tabel](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Jeśli zainicjujesz operację ponownego uruchamiania na serwerach regionów HBase z poziomu interfejsu użytkownika Apache Ambari, natychmiast zobaczysz, że serwery regionów zostały odrzucone, ale nie są od razu uruchamiane ponownie. 

W tym miejscu nowości się w tle: 

1. Agent Ambari wysyła żądanie zatrzymania do serwera regionu.
2. Agent Ambari czeka 30 sekund, aż serwer regionu zostanie bezpiecznie zamknięty. 
3. Jeśli aplikacja nadal nawiązuje połączenie z serwerem regionu, serwer nie zostanie natychmiast zamknięty. Limit czasu 30 sekund upływa przed zamknięciem. 
4. Po 30 sekundach Agent Ambari wysyła polecenie Force-Kill (`kill -9`) do serwera regionu. Można to sprawdzić w dzienniku Ambari-Agent (w katalogu/var/log/odpowiedniego węzła procesu roboczego):

   ```apache
           2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
           est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
           f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
           -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
           2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
           /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
           PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
           nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
           2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
           2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
   ```
   Z powodu nieoczekiwanego zamknięcia port skojarzony z procesem może nie zostać wydaną, nawet jeśli proces serwera regionu został zatrzymany. Ta sytuacja może prowadzić do AddressBindException, gdy jest uruchamiany serwer regionu, jak pokazano w poniższych dziennikach. Można to sprawdzić w dzienniku region-Server. log w katalogu/var/log/HBase w węzłach procesu roboczego, w których nie można uruchomić serwera regionu. 

   ```apache

   2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
   java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
   at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
   at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
        
   Caused by: java.lang.reflect.InvocationTargetException
   at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
   at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
   at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
   at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
   ... 5 more
        
   Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
   at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
   at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
   at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
   ... 10 more
        
   Caused by: java.net.BindException: Address already in use
   at sun.nio.ch.Net.bind0(Native Method)
   at sun.nio.ch.Net.bind(Net.java:463)
   at sun.nio.ch.Net.bind(Net.java:455)
   at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
   at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
   ... 15 more
   ```

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Spróbuj zmniejszyć obciążenie serwerów regionu HBase przed zainicjowaniem ponownego uruchomienia. 
2. Alternatywnie (Jeśli krok 1 nie pomoże), spróbuj ręcznie ponownie uruchomić serwery regionów w węzłach procesu roboczego, używając następujących poleceń:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>Zobacz też
[Rozwiązywanie problemów przy użyciu usługi Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
