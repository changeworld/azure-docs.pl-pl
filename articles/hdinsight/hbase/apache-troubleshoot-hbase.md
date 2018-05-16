---
title: Rozwiązywanie problemów z bazy danych HBase przy użyciu usługi Azure HDInsight | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania na temat pracy z bazy danych HBase i usłudze Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinver
manager: ashitg
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: nitinver
ms.openlocfilehash: d5d50121cd0375af1b57baadeb40efb237aaea11
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="troubleshoot-hbase-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z bazy danych HBase przy użyciu usługi Azure HDInsight

Dowiedz się więcej o Najważniejsze problemy i rozwiązania ich podczas pracy z bazy danych Apache HBase ładunków w Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Jak uruchomić raporty polecenie hbck z wielu regionach nieprzypisane?

Typowe komunikat o błędzie, że można napotkać podczas uruchamiania `hbase hbck` polecenie jest "wiele regionów trwa nieprzypisane lub luk w łańcuchu regiony."

W interfejsie użytkownika głównego HBase widać numer regionów, które są niezrównoważone na wszystkich serwerach regionu. Następnie można uruchomić `hbase hbck` polecenie, aby wyświetlić luk w łańcuchu regionu.

Luk może być spowodowany regiony w trybie offline, więc poprawić przypisania. 

Aby przywrócić regionów nieprzypisane do normalnego stanu, wykonaj następujące kroki:

1. Zaloguj się do klastra HDInsight HBase przy użyciu protokołu SSH.
2. Aby połączyć się z powłoki dozorcy, uruchom `hbase zkcli` polecenia.
3. Uruchom `rmr /hbase/regions-in-transition` polecenia lub `rmr /hbase-unsecure/regions-in-transition` polecenia.
4. Aby wyjść z `hbase zkcli` powłoki, użyj `exit` polecenia.
5. Otwórz interfejs użytkownika Apache Ambari i ponownie uruchom usługę Active głównego HBase.
6. Uruchom `hbase hbck` polecenia ponownie (bez żadnych opcji). Sprawdź dane wyjściowe tego polecenia, aby upewnić się, że wszystkie regiony są przypisane.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Jak rozwiązać problemy z limitu czasu, korzystając z polecenia hbck przypisania region?

### <a name="issue"></a>Problem

Potencjalną przyczyną problemów limitu czasu, gdy używasz `hbck` polecenie może być kilka regiony są przez długi czas w stanie "w ramach przejścia". Regionach jest widoczny jako w trybie offline w Interfejsie użytkownika głównego HBase. Ponieważ dużej liczby regiony są próby przejścia, głównego HBase może limitu czasu i nie można wyświetlić regionach ponownie do trybu online.

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Zaloguj się do klastra HDInsight HBase przy użyciu protokołu SSH.
2. Aby połączyć się z powłoki dozorcy, uruchom `hbase zkcli` polecenia.
3. Uruchom `rmr /hbase/regions-in-transition` lub `rmr /hbase-unsecure/regions-in-transition` polecenia.
4. Aby zakończyć `hbase zkcli` powłoki, użyj `exit` polecenia.
5. W Interfejsie użytkownika narzędzia Ambari Uruchom ponownie usługę Active głównego HBase.
6. Uruchom `hbase hbck -fixAssignments` polecenie ponownie.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Jak I Wymuś. Wyłącz tryb awaryjny systemu plików HDFS w klastrze?

### <a name="issue"></a>Problem

Lokalne usługi Hadoop Distributed pliku System (HDFS) jest zablokowana w trybie awaryjnym w klastrze usługi HDInsight.

### <a name="detailed-description"></a>Szczegółowy opis

Przyczyną tego błędu może być awarii po uruchomieniu poniższego polecenia systemu plików HDFS:

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

Klaster usługi HDInsight był skalowany w dół do bardzo kilku węzłów. Liczba węzłów to poniżej lub bliski współczynnik replikacji systemu plików HDFS.

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów 

1. Pobierz stan systemu plików HDFS w klastrze usługi HDInsight, uruchamiając następujące polecenia:

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
2. Możesz również sprawdzić integralność systemu plików HDFS w klastrze usługi HDInsight przy użyciu następujących poleceń:

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

3. Jeśli okaże się, że nie ma żadnych Brak, uszkodzony, lub bloków under-replikowanych lub że te bloki można zignorować, uruchom następujące polecenie podjęcie węzła nazwa tryb awaryjny:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Jak rozwiązać łączności JDBC lub SQLLine problemy z Apache Phoenix?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

Aby połączyć się z Phoenix, należy podać adres IP w aktywnym węźle dozorcy. Upewnij się, że usługi dozorcy, do których sqlline.py próbuje nawiązać połączenie jest uruchomiona.
1. Zaloguj się do klastra usługi HDInsight przy użyciu protokołu SSH.
2. Wprowadź następujące polecenie:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > Adres IP w aktywnym węźle dozorcy można uzyskać z poziomu interfejsu użytkownika narzędzia Ambari. Przejdź do **HBase** > **szybkie linki** > **ZK\* (aktywny)** > **informacji dozorcy**. 

3. Jeśli sqlline.py łączy się Phoenix i jest nie limitu czasu, uruchom następujące polecenie do sprawdzania dostępności i kondycji Phoenix:

   ```apache
           !tables
           !quit
   ```      
4. Jeśli to polecenie działa, nie ma żadnych problem. Adres IP podany przez użytkownika mogą być niepoprawne. Jednak jeśli polecenie wstrzymuje przez dłuższy czas, a następnie wyświetla następujący błąd, przejdź do kroku 5.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Uruchom następujące polecenia z węzłem głównym (hn0) do diagnozowania warunek Phoenix systemu. Tabela katalogu:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   Polecenie powinien zwrócić błąd podobny do następującego: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. W Interfejsie użytkownika narzędzia Ambari wykonaj następujące kroki, aby ponownie uruchomić usługę HMaster we wszystkich węzłach dozorcy:

    1. W **Podsumowanie** części bazy danych HBase, przejdź do **HBase** > **głównego HBase aktywny**. 
    2. W **składniki** sekcji, uruchom ponownie usługę głównego HBase.
    3. Powtórz te kroki dla wszystkich pozostałych **głównego HBase wstrzymania** usług. 

Może upłynąć do pięciu minut, zanim usługa głównego HBase ustabilizowania i zakończyć proces odzyskiwania. Po kilku minutach Powtórz polecenia sqlline.py, aby potwierdzić, że SYSTEM. Tabela katalogu jest uruchomiony i że można tworzyć zapytania. 

Gdy SYSTEM. Tabela katalogu jest na normalne, problem dotyczący łączności do Phoenix powinien zostać automatycznie rozwiązane.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Co powoduje, że serwer główny uruchomienie?

### <a name="error"></a>Błąd 

Niepodzielne zmiany nazwy awarii.

### <a name="detailed-description"></a>Szczegółowy opis

W trakcie uruchamiania HMaster wykonuje wiele kroków inicjowania. Należą do przenoszenia danych z folderu podstaw (TMP) do folderu danych. HMaster również wygląda w folderze logs zapisu z wyprzedzeniem (WALs), czy są serwerom odpowiadać regionu i tak dalej. 

Podczas uruchamiania HMaster jest podstawowy `list` polecenia w tych folderach. Jeśli w dowolnym momencie HMaster widzi nieoczekiwany plik w żadnym z tych folderów, zgłasza wyjątek, a nie uruchamia.  

### <a name="probable-cause"></a>Prawdopodobna przyczyna

W dziennikach serwera region spróbuj zidentyfikować osi czasu utworzenia pliku, a następnie sprawdź, czy został awarii procesu w czasie zbliżonym do utworzenia pliku. (Się z pomocą techniczną bazy danych HBase, aby pomóc w ten sposób). Dzięki temu nam zapewniają bardziej niezawodne mechanizmy, tak, aby uniknąć naciśnięcie tego błędu i upewnij się, proces bezpiecznego zamknięcia systemu.

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

Stos wywołań i spróbuj ustalenie, który folder może być przyczyną problemu (na przykład może to być WALs folder lub TMP). Następnie w Eksploratorze chmury lub za pomocą poleceń systemu plików HDFS próbę zlokalizuj plik problem. Zazwyczaj jest to \*-renamePending.json pliku. ( \*-RenamePending.json plik jest używany do wykonania operacji zmiany nazwy atomic w sterowniku WASB pliku dziennika. Z powodu błędów w tej implementacji te pliki mogą pozostać za pośrednictwem po awarii procesów itd.) Wymuś Usuń ten plik w Eksploratorze chmury lub za pomocą poleceń systemu plików HDFS. 

Czasami może znajdować się plik tymczasowy o nazwie przypominać *$$$. $$$* w tej lokalizacji. Należy użyć systemu plików HDFS `ls` polecenia, aby wyświetlić ten plik, nie można sprawdzić plik w Eksploratorze chmury. Aby usunąć ten plik, użyj polecenia systemu plików HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.  

Po uruchomieniu tych poleceń, HMaster należy zacząć od razu. 

### <a name="error"></a>Błąd

Żaden adres serwera znajduje się w *hbase: meta* dla regionu xxx.

### <a name="detailed-description"></a>Szczegółowy opis

W klastrze systemu Linux, który wskazuje, że może zostać wyświetlony komunikat *hbase: meta* tabeli nie jest w trybie online. Uruchomiona `hbck` może raportować który "hbase: meta tabeli replicaId 0 nie została znaleziona na dowolny region." Może to oznaczać HMaster nie można zainicjować po ponownym uruchomieniu bazy danych HBase. W dziennikach HMaster, zostanie wyświetlony komunikat: "nie adres serwera na liście hbase: meta dla regionu hbase: kopii zapasowej \<nazwa regionu\>".  

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Powłoka HBase wprowadź następujące polecenia (zmiana wartości rzeczywistych zgodnie z wymaganiami):  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Usuń *hbase: przestrzeń nazw* wpisu. Ten wpis może być ten sam błąd, który jest zgłaszane, gdy *hbase: przestrzeń nazw* jest skanowany w tabeli.

3. Można wyświetlić bazy danych HBase w stanie uruchomienia, w Interfejsie użytkownika narzędzia Ambari, uruchom ponownie usługę Active HMaster.  

4. Powłoka HBase Aby wyświetlić wszystkie tabele w trybie offline, uruchom następujące polecenie:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>Dodatkowe materiały

[Nie można przetworzyć tabeli HBase](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Błąd

HMaster limitu czasu z wyjątek krytyczny, podobnie jak "java.io.IOException: Upłynął limit czasu 300000ms oczekiwania dla przestrzeni nazw tabeli do przypisania."

### <a name="detailed-description"></a>Szczegółowy opis

Ten problem może wystąpić, jeśli masz wiele tabel i regionów, które nie zostały opróżnione po ponownym uruchomieniu usługi HMaster. Ponowne uruchomienie może zakończyć się niepowodzeniem i pojawi się poprzedni komunikat o błędzie.  

### <a name="probable-cause"></a>Prawdopodobna przyczyna

Jest to znany problem z usługą HMaster. Zadania uruchamiania ogólne klastra może zająć dużo czasu. HMaster zamknięty, ponieważ w tabeli nazw nie jest jeszcze przypisana. Dzieje się tak tylko w scenariuszach, w którym dużych ilości danych unflushed istnieje i nie wystarcza limitem czasu równym 5 minut.
  
### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. W Interfejsie użytkownika narzędzia Ambari, przejdź do **HBase** > **Configs**. W pliku niestandardowej bazy danych hbase-site.xml Dodaj następujące ustawienia: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Ponownie uruchom wymagane usługi (HMaster i prawdopodobnie innych usług HBase).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Na serwerze, na region, co powoduje niepowodzenie ponownego uruchomienia?

### <a name="issue"></a>Problem

Błąd ponownego uruchomienia na serwerze, na region może być niemożliwe przez następujące najlepsze rozwiązania. Zaleca się wstrzymanie działania mocno obciążone, planując ponowne uruchomienie serwerów region HBase. Jeśli aplikacja będzie nadal Połącz się z serwerami regionu, gdy trwa zamykanie, operacja ponownego uruchomienia serwera w regionie będzie wolniejsze za kilka minut. Ponadto jest dobrym pomysłem jest najpierw opróżnić wszystkich tabel. Aby uzyskać informacje na temat sposobu opróżnić tabel, zobacz [HDInsight HBase: jak zwiększyć czas ponownego uruchamiania klastra HBase przez opróżnianie tabel](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Jeśli użytkownik inicjuje operacji ponownego uruchomienia na serwerach regionu bazy danych HBase w interfejsie użytkownika narzędzia Ambari, natychmiast zobaczysz czy serwery region zakończył działanie, ale nie ich ponownego uruchomienia od razu. 

Oto, co dzieje się w tle: 

1. Ambari agent wysyła żądanie zatrzymania z serwerem regionu.
2. Ambari agent czeka na 30 sekund na serwerze regionie można bezpiecznie zamknąć. 
3. Jeśli aplikacja w dalszym ciągu połączenia z serwerem regionu, serwer nie będzie natychmiast zamknięty. Limit czasu 30 sekund wygasa, zanim nastąpi jego zamknięcia. 
4. Po 30 sekund, Ambari agent wysyła życie polecenia kill (`kill -9`) polecenia na serwerze regionu. Można to zobaczyć w dzienniku agenta ambari (w /var/dziennika/katalogu z węzłem procesu roboczego odpowiednich):

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
Z powodu niespodziewane wyłączanie portu skojarzonych z procesem może nie zostać zwolnione, nawet jeśli proces serwera region jest zatrzymana. Taka sytuacja może prowadzić do AddressBindException podczas uruchamiania serwera region opisane w następujących dziennikach. Można to sprawdzić, w regionie server.log w katalogu /var/log/hbase na węzłów procesu roboczego, w którym serwer region nie powiedzie się. 

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

1. Spróbuj zmniejszyć obciążenie serwerów region HBase przed rozpoczęciem ponownego uruchomienia komputera. 
2. Możesz też (Jeśli krok 1 nie Pomoc), spróbuj ręcznie ponownie uruchomić serwery region na węzłów procesu roboczego przy użyciu następujących poleceń:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>Zobacz też
[Rozwiązywanie problemów przy użyciu usługi Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
