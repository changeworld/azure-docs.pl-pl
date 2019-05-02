---
title: Rozwiązywanie problemów z bazy danych HBase za pomocą usługi Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania na temat pracy z bazy danych HBase i usługi Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 6ba17a3839390ed5fe503a6fe57b63d8fb119138
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713498"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z bazy danych Apache HBase przy użyciu usługi Azure HDInsight

Dowiedz się więcej o najważniejszych problemach i ich rozwiązania podczas pracy z ładunków bazy danych Apache HBase w Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Jak uruchomić hbck polecenia Raporty z wieloma regionami nieprzypisanego?

Typowe komunikat o błędzie, można napotkać podczas uruchamiania `hbase hbck` polecenie jest "wiele regionów są nieprzypisane lub luki w łańcuchu regionów."

W Interfejsie użytkownika głównego interfejsu użytkownika HBase można zobaczyć przez liczbę regionów, które są niezrównoważone na wszystkich serwerach regionu. Następnie można uruchomić `hbase hbck` polecenie, aby wyświetlić luki w łańcuchu regionu.

Otwory może być spowodowany przez regionów w trybie offline, więc przydziały najpierw napraw. 

Aby przywrócić regionami nieprzypisanego do normalnego stanu, wykonaj następujące czynności:

1. Zaloguj się do klastra HDInsight HBase przy użyciu protokołu SSH.
2. Aby połączyć się z powłoki programu Apache ZooKeeper, uruchom `hbase zkcli` polecenia.
3. Uruchom `rmr /hbase/regions-in-transition` polecenia lub `rmr /hbase-unsecure/regions-in-transition` polecenia.
4. Aby wyjść z `hbase zkcli` powłoki, należy użyć `exit` polecenia.
5. Otwórz interfejsu użytkownika programu Apache Ambari, a następnie ponownie uruchom usługę Active głównego interfejsu użytkownika HBase.
6. Uruchom `hbase hbck` polecenia ponownie (bez żadnych opcji). Sprawdź dane wyjściowe tego polecenia, aby upewnić się, że we wszystkich regionach są przypisane.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Jak naprawić problemy limitu czasu, w przypadku używania polecenia hbck dla regionów przypisania?

### <a name="issue"></a>Problem

Potencjalną przyczyną problemów limitu czasu, gdy używasz `hbck` polecenie może być, że kilku regionach znajdują się w stanie "w przejścia" przez długi czas. Możesz zobaczyć tych regionów, jako offline w Interfejsie użytkownika głównego interfejsu użytkownika HBase. Ponieważ dużą liczbą regionów próbuje przejścia, głównego interfejsu użytkownika HBase może limitu czasu i być nie można przenieść z powrotem do trybu online w tych regionach.

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Zaloguj się do klastra HDInsight HBase przy użyciu protokołu SSH.
2. Aby połączyć się z powłoki programu Apache ZooKeeper, uruchom `hbase zkcli` polecenia.
3. Uruchom `rmr /hbase/regions-in-transition` lub `rmr /hbase-unsecure/regions-in-transition` polecenia.
4. Aby zakończyć działanie `hbase zkcli` powłoki, należy użyć `exit` polecenia.
5. W Interfejsie użytkownika Ambari Uruchom ponownie usługę Active głównego interfejsu użytkownika HBase.
6. Uruchom `hbase hbck -fixAssignments` ponownie polecenie.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Jak I Wymuś Wyłącz tryb awaryjny systemu plików HDFS w klastrze?

### <a name="issue"></a>Problem

Lokalne Apache pliku System (HDFS, Hadoop Distributed) została zablokowana w trybie awaryjnym w klastrze HDInsight.

### <a name="detailed-description"></a>Szczegółowy opis

Ten błąd może być spowodowany przez błąd podczas uruchamiania następujących poleceń systemu plików HDFS:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

Błąd, które można napotkać podczas próby uruchomienia polecenia wygląda następująco:

```apache
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
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

Klaster HDInsight skalowania w dół do bardzo kilku węzłów. Liczba węzłów znajduje się poniżej, lub w pobliżu współczynnika replikacji systemu plików HDFS.

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów 

1. Pobierz stan systemu plików HDFS w klastrze HDInsight, uruchamiając następujące polecenia:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdiuser@hn0-spark2:~$ hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
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
2. Możesz również sprawdzić integralność systemu plików HDFS w klastrze HDInsight przy użyciu następujących poleceń:

   ```apache
   hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
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

3. Jeśli stwierdzisz, że nie ma żadnych brakujące, uszkodzony, lub under-replikowanych bloków lub że można zignorować te bloki, uruchom następujące polecenie do wykonania nazwy węzła z trybu awaryjnego:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Jak naprawić połączenia sterownika JDBC lub SQLLine problemy związane z rozwiązaniem Apache Phoenix?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

Aby połączyć się z rozwiązaniem Apache Phoenix, należy podać adres IP w aktywnym węźle Apache ZooKeeper. Upewnij się, że usługi ZooKeeper które pliku sqlline.py próbuje nawiązać połączenie jest uruchomiona.
1. Zaloguj się w klastrze HDInsight przy użyciu protokołu SSH.
2. Wprowadź następujące polecenie:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > Adres IP aktywnego węzła dozorcy można uzyskać z poziomu interfejsu użytkownika Ambari. Przejdź do **HBase** > **szybkich łączy** > **ZK\* (aktywny)** > **informacje dozorcy**. 

3. Jeśli pliku sqlline.py Phoenix umożliwia nawiązywanie połączeń i jest nie limitu czasu, uruchom następujące polecenie, aby sprawdzić dostępność i kondycję Phoenix:

   ```apache
           !tables
           !quit
   ```      
4. To polecenie działa, czy żaden problem. Adres IP, dostarczone przez użytkownika mogą być niepoprawne. Jednakże jeśli polecenie wstrzymuje przez dłuższy czas, a następnie wyświetla następujący błąd, przejdź do kroku 5.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Uruchom następujące polecenia z węzła głównego (hn0) do diagnozowania stanu systemu Phoenix. Tabela katalogu:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   Polecenie powinna zwrócić błąd podobny do następującego: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. W Interfejsie użytkownika Apache Ambari wykonaj następujące kroki, aby ponownie uruchomić usługę serwera HMaster we wszystkich węzłach dozorcy:

    1. W **Podsumowanie** części bazy danych HBase, przejdź do **HBase** > **głównego interfejsu użytkownika HBase aktywny**. 
    2. W **składniki** sekcji, uruchom ponownie usługę głównego interfejsu użytkownika HBase.
    3. Powtórz te czynności dla wszystkich pozostałych **głównego interfejsu użytkownika HBase wstrzymania** usług. 

Może upłynąć do pięciu minut, zanim usługa głównego interfejsu użytkownika HBase na stabilizowaniu i zakończyć proces odzyskiwania. Po kilku minutach Powtórz polecenia pliku sqlline.py, aby potwierdzić, że SYSTEM. Tabela katalogu jest włączony i że mogą być przeszukiwane. 

Gdy SYSTEM. Tabela katalogu znajduje się na normalne, problem z łącznością Phoenix powinien zostać automatycznie rozwiązane.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Co powoduje, że serwer główny uruchomienie?

### <a name="error"></a>Błąd 

Występuje błąd Atomowej zmiany nazwy.

### <a name="detailed-description"></a>Szczegółowy opis

W trakcie uruchamiania serwera HMaster kończy się wiele kroków inicjowania. Należą do przenoszenia danych z folderu podstaw (.tmp) do folderu danych. Serwera HMaster również wygląda w folderze dzienników zapisu z wyprzedzeniem (WALs), aby zobaczyć, czy serwery nie odpowiada region i tak dalej. 

Podczas uruchamiania serwera HMaster jest podstawowym `list` polecenia w tych folderach. Jeśli w dowolnym momencie serwera HMaster widzi nieoczekiwany pliku w żadnym z tych folderów, zgłasza wyjątek i nie uruchomi się.  

### <a name="probable-cause"></a>Prawdopodobna przyczyna

W regionie dzienniki serwera spróbuj zidentyfikować osi czasu utworzenia pliku, a następnie zobacz, jeśli było awaria procesu w czasie zbliżonym do czasu, który został utworzony. (Skontaktuj się z pomocą techniczną bazy danych HBase, aby pomóc w ten sposób.) Ta ułatwia nam zapewniają bardziej niezawodne mechanizmy, tak, aby uniknąć osiągnięcia tego błędu i upewnij się, łagodne procesu zamykania.

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

Sprawdź stos wywołań i spróbuj określić folder może być przyczyną problemu (na przykład może to być WALs folder lub .tmp). Następnie w programie Cloud Explorer lub przy użyciu poleceń systemu plików HDFS, spróbuj zlokalizuj plik problem. Zazwyczaj jest \*-renamePending.json pliku. ( \*-RenamePending.json plik znajduje się w pliku dziennika, który jest używany do implementowania operacji Atomowej zmiany nazwy w sterowniku WASB. Z powodu błędów w tej implementacji te pliki mogą pozostać za pośrednictwem po procesie awarie i tak dalej.) Wymuszenie usunięcia tego pliku w programie Cloud Explorer lub przy użyciu poleceń systemu plików HDFS. 

Czasami może znajdować się plik tymczasowy o nazwie podobny *$$$. $$$* w tej lokalizacji. Należy użyć systemu plików HDFS `ls` polecenia, aby wyświetlić ten plik; nie można wyświetlić pliku w programie Cloud Explorer. Aby usunąć ten plik, użyj polecenia systemu plików HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.  

Po uruchomieniu tych poleceń, serwera HMaster należy zacząć od razu. 

### <a name="error"></a>Błąd

Brak adresu serwera znajduje się w *hbase: metadane* dla regionu xxx.

### <a name="detailed-description"></a>Szczegółowy opis

Może pojawić się komunikat w klastrze systemu Linux, oznacza to, że *hbase: metadane* tabeli nie jest w trybie online. Uruchamianie `hbck` może zgłaszać, że "bazy danych hbase: metadanych tabeli replicaId 0 nie znajduje się w dowolnym regionie." Może to oznaczać serwera HMaster nie można zainicjować po ponownym bazy danych HBase. W dzienniki serwera HMaster może zostać wyświetlony komunikat: "Brak adresu serwera na liście w bazie danych hbase: metadane dla regionu hbase: kopii zapasowej \<nazwa regionu\>".  

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Powłoka HBase wprowadź następujące polecenia (zmiana rzeczywiste wartości zgodnie z wymaganiami):  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Usuń *hbase: przestrzeń nazw* wpisu. Ten wpis może być ten sam błąd, który jest zgłaszany podczas *hbase: przestrzeń nazw* jest skanowany w tabeli.

3. Aby wyświetlić bazy danych HBase w stanie uruchomienia, w interfejsie użytkownika Ambari, należy ponownie uruchomić usługę serwera HMaster Active.  

4. Powłoka HBase, aby wyświetlić wszystkie tabele w trybie offline, uruchom następujące polecenie:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>Materiały uzupełniające

[Nie można przetworzyć w tabeli bazy danych HBase](https://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Błąd

Limit czasu serwera HMaster z wyjątku krytycznego podobny do "java.io.IOException: Przekroczono limit czasu 300000ms oczekiwanie na przestrzeń nazw tabeli do przypisania."

### <a name="detailed-description"></a>Szczegółowy opis

Ten problem może wystąpić, jeśli masz wiele tabel i regiony, które nie zostać wyczyszczona po ponownym uruchomieniu usługi serwera HMaster. Ponowne uruchomienie może zakończyć się niepowodzeniem, a zobaczysz poprzedni komunikat o błędzie.  

### <a name="probable-cause"></a>Prawdopodobna przyczyna

Jest to znany problem z usługą serwera HMaster. Zadania uruchamiania ogólnego klastra może zająć dużo czasu. Serwera HMaster zamknięty, ponieważ tabela przestrzeni nazw nie jest jeszcze przypisana. Dzieje się tak tylko w scenariuszach, w którym duże ilości danych unflushed istnieje i nie wystarcza limit czasu równy pięć minut.
  
### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. W Interfejsie użytkownika Apache Ambari, przejdź do **HBase** > **Configs**. W pliku niestandardowej bazy danych hbase-site.xml Dodaj następujące ustawienie: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Ponownie uruchom wymagane usługi (serwera HMaster i ewentualnie inne usługi bazy danych HBase).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Co powoduje awarię ponownego uruchamiania serwera regionalnego?

### <a name="issue"></a>Problem

Błąd ponownego uruchamiania serwera regionalnego może być niemożliwe, poniższe najlepsze rozwiązania. Firma Microsoft zaleca wstrzymać działanie duże obciążenia, podczas planowania ponownie uruchomić serwery regionów HBase. Jeśli aplikacja nadal łączyć się z serwery regionów, gdy trwa zamykanie, operacji ponownego uruchamiania serwera region będzie przebiegać wolniej przez kilka minut. Ponadto jest dobry pomysł, aby najpierw opróżnienia wszystkich tabel. Aby uzyskać informacje na temat sposobu opróżniania tabel, zobacz [HDInsight HBase: Jak poprawić czas ponownego uruchamiania klastra Apache HBase, opróżniania tabel](https://web.archive.org/web/20190112153155/ https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Jeśli zainicjujesz operacji ponownego uruchamiania serwery regionów HBase z poziomu interfejsu użytkownika Ambari Apache, możesz natychmiast zobaczyć, że serwery regionów zakończył działanie, ale nie ich ponownego natychmiast. 

Oto, co się dzieje w tle: 

1. Agent systemu Ambari wysyła żądanie zatrzymania serwerowi regionu.
2. Agent systemu Ambari czeka na 30 sekund w przypadku serwera regionalnego zamknięcie. 
3. Jeśli aplikacja będzie nadal nawiązać połączenie z serwerem region, serwer nie będzie natychmiast zamknie. Upłynie limit czasu 30 sekund, zanim nastąpi zamknięcie. 
4. Po 30 sekundach Ambari agent wysyła życie polecenia kill (`kill -9`) polecenie do serwera regionu. Widać to w dzienniku systemu ambari-agent (w /var/log/katalogu z węzłem procesu roboczego odpowiednich):

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
   W związku z nieoczekiwanym zamknięciu portu skojarzonego z procesem nie może być zwolniony, mimo że proces serwera regionu jest zatrzymana. Ta sytuacja może prowadzić do AddressBindException podczas uruchamiania serwera regionalnego, jak pokazano w następujących dziennikach. Można to sprawdzić, w regionie server.log w katalogu /var/log/hbase na węzłach procesu roboczego, w którym serwer regionu nie można uruchomić. 

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

1. Spróbuj zmniejszyć obciążenie serwery regionów HBase przed inicjować ponownego uruchomienia. 
2. Alternatywnie (Jeśli krok 1 nie pomoże), spróbuj ręcznie ponownie uruchomić serwery regionów na węzłach procesu roboczego przy użyciu następujących poleceń:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>Zobacz też
[Rozwiązywanie problemów przy użyciu usługi Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
