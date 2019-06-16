---
title: Bezpieczne przepływy pracy programu Apache Oozie w z pakietem Enterprise Security — Azure HDInsight
description: Bezpieczne przepływy pracy programu Apache Oozie przy użyciu pakietu zabezpieczeń Azure HDInsight przedsiębiorstwa. Dowiedz się, jak zdefiniować przepływ pracy programu Oozie i przesłać zadanie usługi Oozie.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 7d7fbf5d72654c26edf09ab27f024eaf39f8c387
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64708999"
---
# <a name="run-apache-oozie-in-hdinsight-hadoop-clusters-with-enterprise-security-package"></a>Uruchamiaj Apache Oozie w usłudze HDInsight Hadoop klastry z pakietem Enterprise Security

Apache Oozie jest systemem przepływu pracy i koordynacji, który zarządza zadaniami na technologii Apache Hadoop. Oozie jest zintegrowany ze stosem platformy Hadoop i obsługuje następujące zadania:
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Można również użyć programu Oozie do planowania zadań, które są specyficzne dla systemu, np. programów Java lub skryptów powłoki.

## <a name="prerequisite"></a>Wymagania wstępne

- Klaster usługi Azure HDInsight Hadoop przy użyciu pakietu zabezpieczeń przedsiębiorstwa (ESP). Zobacz [HDInsight konfigurowanie klastrów przy użyciu ESP](./apache-domain-joined-configure-using-azure-adds.md).

    > [!NOTE]  
    > Aby uzyskać szczegółowe instrukcje na temat korzystania z technologii Oozie na klastrach-ESP, zobacz [przepływy Użyj Apache Oozie opartych na systemie Linux usługi Azure HDInsight](../hdinsight-use-oozie-linux-mac.md).

## <a name="connect-to-an-esp-cluster"></a>Łączenie z klastrem ESP

Aby uzyskać więcej informacji na Secure Shell (SSH), zobacz [nawiązywanie połączenia z HDInsight (Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Połącz się z klastrem HDInsight przy użyciu protokołu SSH:  
   ```bash
   ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
   ```

2. Aby sprawdzić pomyślnego uwierzytelnienia Kerberos, użyj `klist` polecenia. Jeśli nie, użyj `kinit` można uruchomić uwierzytelniania Kerberos.

3. Zaloguj się do bramy usługi HDInsight można zarejestrować tokenu protokołu OAuth, wymagane do uzyskania dostępu usługi Azure Data Lake Storage:   
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    Kod stanu odpowiedzi z **200 OK** oznacza pomyślną rejestrację. Sprawdź nazwę użytkownika i hasło, jeśli zostanie odebrana odpowiedź nieautoryzowany, takich jak 401.

## <a name="define-the-workflow"></a>Zdefiniuj przepływ pracy
Definicje przepływów pracy programu Oozie są zapisywane w Apache Hadoop procesu Definition Language (hPDL). hPDL to język definicji procesu XML. Wykonaj poniższe kroki, aby zdefiniować przepływ pracy:

1. Skonfiguruj obszar roboczy użytkownika domeny:
   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```
   Zastąp `DomainUser` z nazwą użytkownika domeny. 
   Zastąp `DomainUserPath` przy użyciu ścieżki katalogu macierzystego użytkownika domeny. 
   Zastąp `ClusterVersion` z wersją Hortonworks Data Platform (HDP) klastra.

2. Aby tworzyć i edytować plik, należy użyć następującej instrukcji:
   ```bash
   nano workflow.xml
   ```

3. Po otwarciu edytora nano, wprowadź następujący kod XML jako zawartość pliku:
   ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://hn0-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
   ```
4. Zastąp `clustername` nazwą klastra. 

5. Aby zapisać plik, wybierz klawisze Ctrl + X. Wprowadź polecenie `Y`. Następnie wybierz pozycję **Enter**.

    Przepływ pracy jest podzielony na dwie części:
   * **Sekcja poświadczeń.** W tej sekcji przyjmuje poświadczenia, które są używane do uwierzytelniania Oozie akcje:

     W tym przykładzie korzysta z uwierzytelniania dla działania Hive. Aby dowiedzieć się więcej, zobacz [uwierzytelniania akcji](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

     Usługa dostępu do poświadczeń zezwala na akcje Oozie personifikować użytkownika do uzyskiwania dostępu do usług Hadoop.

   * **Sekcja akcji.** Ta sekcja zawiera trzy czynności:-mapreduce, Hive server 2 i Hive server 1:

     - Mapreduce uruchomień działania przykład z pakietu programu Oozie dla mapreduce, które wyświetla zagregowane wyrazów.

     - Hive server 2 i gałąź serwera 1 akcji w przypadku uruchamiania zapytania na przykładową tabelę programu Hive, wyposażone w HDInsight.

     Działania programu Hive, Użyj poświadczeń określonych w sekcji poświadczenia do uwierzytelniania za pomocą słowa kluczowego `cred` w elemencie akcji.

6. Użyj następującego polecenia, aby skopiować `workflow.xml` plik `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`:
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

7. Zastąp `domainuser` ze swoją nazwą użytkownika w domenie.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Zdefiniuj plik właściwości zadania programu Oozie

1. Poniższa instrukcja umożliwia tworzenie i edytowanie nowego pliku dla właściwości zadania:

   ```bash
   nano job.properties
   ```

2. Po otwarciu edytora nano, należy użyć następujący kod XML jako zawartość pliku:

   ```bash
       nameNode=adl://home
       jobTracker=headnodehost:8050
       queueName=default
       examplesRoot=examples
       oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
       hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
       hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
       oozie.use.system.libpath=true
       user.name=[domainuser]
       jdbcPrincipal=hive/hn0-<ClusterShortName>.<Domain>.com@<Domain>.COM
       jdbcURL=[jdbcurlvalue]
       hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
       hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   * Użyj `adl://home` identyfikator URI dla `nameNode` właściwość, jeśli masz usługi Azure Data Lake Storage Gen1 jako magazynu klastra podstawowego. Jeśli używasz usługi Azure Blob Storage to zmienić tutaj, aby `wasb://home`. Jeśli używasz usługi Azure Data Lake Storage Gen2 to zmienić tutaj, aby `abfs://home`.
   * Zastąp `domainuser` ze swoją nazwą użytkownika w domenie.  
   * Zastąp `ClusterShortName` nazwą krótkim dla klastra. Na przykład, jeśli nazwa klastra jest https:// *[przykład link]* sechadoopcontoso.azurehdisnight.net, `clustershortname` pierwszych sześciu znaków klastra: **sechad**.  
   * Zastąp `jdbcurlvalue` za pomocą adresu URL JDBC z konfiguracji programu Hive. Przykładem jest jdbc:hive2: / / headnodehost:10001 /; transportMode = http.      
   * Aby zapisać plik, wybierz klawisze Ctrl + X, należy wprowadzić `Y`, a następnie wybierz pozycję **Enter**.

   Ten plik właściwości musi znajdować się lokalnie, podczas uruchamiania zadań programu Oozie.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Utwórz niestandardowe skrypty Hive dla zadania programu Oozie

Można utworzyć dwa skrypty Hive, Hive server 1 i Hive server 2, jak pokazano w poniższych sekcjach.

### <a name="hive-server-1-file"></a>Plik serwer 1 programu hive

1.  Tworzenie i edytowanie pliku dla akcji serwer 1 programu Hive:
    ```bash
    nano countrowshive1.hql
    ```

2.  Utwórz skrypt:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  Zapisz plik do Apache Hadoop Distributed pliku System (HDFS):
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Hive server 2 pliku

1.  Utwórz i Edytuj pole Hive server 2 akcji:
    ```bash
    nano countrowshive2.hql
    ```

2.  Utwórz skrypt:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  Zapisz plik w systemie plików hdfs:
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Przesyłanie zadań programu Oozie

Przesyłanie zadań programu Oozie w przypadku klastrów ESP jest podobne do przesyłania zadań programu Oozie w klastrach-ESP.

Aby uzyskać więcej informacji, zobacz [Użyj Apache Oozie przy użyciu technologii Apache Hadoop, aby zdefiniować i uruchomić przepływ pracy na opartą na systemie Linux usługi Azure HDInsight](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Wyniki z Oozie przesłania zadania
Oozie zadania są uruchamiane dla użytkownika. Dlatego zarówno Apache Hadoop YARN, jak i struktury Apache Ranger inspekcji Pokaż dzienniki zadania są uruchamiane jako nazwa spersonifikowanego użytkownika. Interfejs wiersza polecenia dane wyjściowe zadania Oozie wygląda podobnie do poniższego kodu:



```bash
    Job ID : 0000015-180626011240801-oozie-oozi-W
    ------------------------------------------------------------------------------------------------
    Workflow Name : map-reduce-wf
    App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
    Status        : SUCCEEDED
    Run           : 0
    User          : alicetest
    Group         : -
    Created       : 2018-06-26 19:25 GMT
    Started       : 2018-06-26 19:25 GMT
    Last Modified : 2018-06-26 19:30 GMT
    Ended         : 2018-06-26 19:30 GMT
    CoordAction ID: -
    
    Actions
    ------------------------------------------------------------------------------------------------
    ID                      Status  Ext ID          ExtStatus   ErrCode
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@:start:    OK  -           OK      -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@mr-test    OK  job_1529975666160_0051  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive2    OK  job_1529975666160_0053  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive OK  job_1529975666160_0055  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@end    OK  -           OK      -
    -----------------------------------------------------------------------------------------------
```

Dzienniki inspekcji platformy Ranger Hive server 2 akcji Pokaż Oozie uruchamianie akcji dla użytkownika. Widoki Ranger i YARN są widoczne tylko dla administratora klastra.

## <a name="configure-user-authorization-in-oozie"></a>Konfigurowanie autoryzacji użytkownika w Oozie

Oozie samodzielnie ma Konfiguracja autoryzacji użytkownika, który może uniemożliwić użytkownikom zatrzymywanie lub usuwanie zadań innym użytkownikom. Aby włączyć tę konfigurację, ustaw `oozie.service.AuthorizationService.security.enabled` do `true`. 

Aby uzyskać więcej informacji, zobacz [Apache Oozie instalacja i Konfiguracja](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

Dla składników, takich jak Hive server 1, w której Ranger wtyczki nie jest dostępna lub nie jest obsługiwany możliwe jest tylko gruboziarnistych autoryzacji systemu plików HDFS. Autoryzacja szczegółowych jest dostępna wyłącznie za pośrednictwem wtyczek platformy Ranger.

## <a name="get-the-oozie-web-ui"></a>Pobierz interfejs użytkownika sieci web programu Oozie

Interfejs użytkownika sieci web programu Oozie zapewnia widok stanu zadań Oozie opartych na sieci web w klastrze. Aby uzyskać interfejs użytkownika sieci web, wykonaj następujące kroki w klastrach ESP:

1. Dodaj [węzła brzegowego](../hdinsight-apps-use-edge-node.md) i włączyć [uwierzytelnianie SSH Kerberos](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Postępuj zgodnie z [interfejsu użytkownika sieci web programu Oozie](../hdinsight-use-oozie-linux-mac.md) kroki, aby włączyć tunelowania SSH z węzłem krawędzi i dostęp do interfejsu użytkownika sieci web.

## <a name="next-steps"></a>Kolejne kroki
* [Za pomocą programu Apache Oozie Apache Hadoop do definiowania i uruchomić przepływ pracy na opartą na systemie Linux usługi Azure HDInsight](../hdinsight-use-oozie-linux-mac.md).
* [Nawiązać połączenie z HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
