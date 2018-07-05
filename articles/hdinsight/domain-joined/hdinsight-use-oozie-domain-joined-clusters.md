---
title: Przepływy klastry przyłączone do domeny HDInsight Hadoop Oozie
description: Użyj usługi Hadoop Oozie w domenie HDInsight opartych na systemie Linux dołączony pakiet Enterprise Security. Dowiedz się, jak zdefiniować przepływ pracy programu Oozie i przesłać zadanie usługi Oozie.
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: omidm
ms.openlocfilehash: 9e5b7303830f2064f764d2de023b4a3ff9b0ea9f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450226"
---
#<a name="run-apache-oozie-in-domain-joined-hdinsight-hadoop-clusters"></a>Uruchamianie programu Apache Oozie w klastrach HDInsight przyłączone do domeny w usłudze Hadoop
Oozie jest systemem przepływu pracy i koordynacji, który zarządza zadaniami na platformie Hadoop. Oozie jest zintegrowany ze stosem platformy Hadoop i obsługuje następujące zadania:
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Można również użyć programu Oozie do planowania zadań, które są specyficzne dla systemu, np. programów Java lub skryptów powłoki.

##<a name="prerequisites"></a>Wymagania wstępne:
- Klaster przyłączony do domeny usługi HDInsight Hadoop. Zobacz [klastry HDInsight przyłączone do domeny skonfiguruj](./apache-domain-joined-configure-using-azure-adds.md)

    > [!NOTE]
    > Aby zobaczyć szczegółowe instrukcje dla dołączonych przy użyciu technologii Oozie na nienależących do domeny do klastrów zobacz [to](../hdinsight-use-oozie-linux-mac.md)

##<a name="connecting-to-domain-joined-cluster"></a>Nawiązywanie połączenia z domeny z klastrem przyłączonym
Aby uzyskać więcej informacji, dotyczące protokołu SSH, zobacz [uwierzytelnianie: przyłączony do domeny HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
- Nawiąż połączenie z klastrem usługi HDInsight przy użyciu protokołu SSH:
     ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```
Aby sprawdzić, czy uwierzytelnianie Kerberos się powiodła, użyj `klist` polecenia. Jeśli nie, użyj `kinit` można zainicjować uwierzytelniania Kerberos.

- Zaloguj się do bramy HDInsight, aby zarejestrować token oAuth wymagany do uzyskania dostępu do usługi Azure Data Lake Store (ADLS)
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    Kod stanu odpowiedzi z _200 OK_ oznacza pomyślną rejestrację. Sprawdź nazwę użytkownika i hasło, jeśli odpowiedź nieautoryzowanych odebranych (401).

## <a name="define-the-workflow"></a>Zdefiniuj przepływ pracy
Definicje przepływów pracy programu Oozie są zapisywane w Hadoop proces definicji języka (hPDL), który jest język definicji procesu XML. Zdefiniuj przepływ pracy, wykonaj następujące kroki:

-   Konfigurowanie obszaru roboczego użytkownika domeny:
 ```bash
hdfs dfs -mkdir /user/<DomainUser>
cd /home/<DomainUserPath>
cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
tar -xvf oozie-examples.tar.gz
hdfs dfs -put examples /user/<DomainUser>/
 ```
Zastąp `DomainUser` z nazwą użytkownika domeny. Zastąp `DomainUserPath` przy użyciu ścieżki katalogu macierzystego użytkownika domeny. Zastąp `ClusterVersion` z wersją HDP klastra.

-   Aby tworzyć i edytować plik, należy użyć następującej instrukcji:
 ```bash
nano workflow.xml
 ```

- Po otwarciu edytora nano, wprowadź następujący kod XML jako zawartość pliku:
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
Zastąp `clustername` nazwą klastra. 

Aby zapisać plik, wybierz klawisze Ctrl + X, należy wprowadzić `Y`, a następnie wybierz pozycję **Enter**.

Przepływ pracy jest podzielony na dwie części:
*   Sekcja poświadczeń: Sekcji poświadczenia przyjmuje poświadczenia, które będą używane do uwierzytelniania akcje oozie:

    W tym przykładzie jest używany uwierzytelniania dla działania Hive. Aby dowiedzieć się więcej, zobacz [to]( https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

    Usługa dostępu do poświadczeń zezwala na akcje oozie personifikować użytkownika do uzyskiwania dostępu do usług Hadoop.

*   Sekcja akcji: Mamy trzy czynności, w tym miejscu mapreduce, hive server 2 akcji i gałąź serwera 1:

    Przebiegi map-reduce, na przykład z pakietu oozie mapreduce która wysyła zagregowaną wyrazów.

    Hive server 2 i gałąź serwera 1 akcje wykonuje proste zapytanie na tabeli hivesample dołączonym HDInsight.

    Działania programu hive, Użyj poświadczeń określonych w sekcji poświadczenia uwierzytelniania przy użyciu słowa kluczowego `cred` w elemencie akcji

- Użyj następującego polecenia, aby skopiować `workflow.xml` plik `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`:
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

    Zastąp `domainuser` ze swoją nazwą użytkownika w domenie.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Zdefiniuj plik właściwości zadania programu Oozie

1.  Poniższa instrukcja umożliwia tworzenie i edytowanie nowego pliku dla właściwości zadania:
     ```bash
    nano job.properties
     ```

2.   Po otwarciu edytora nano, należy użyć następujący kod XML jako zawartość pliku:

    ```bash
        nameNode=adl://home
        jobTracker=headnodehost:8050
        queueName=default
        examplesRoot=examples
        oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
        hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
        hiveScript2=${nameNode}/user/${user.name}/countrowshive1.hql
        oozie.use.system.libpath=true
        user.name=[domainuser]
        jdbcPrincipal=hive/hn0-<ClusterShortName>.<Domain>.com@<Domain>.COM
        jdbcURL=[jdbcurlvalue]
        hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
        hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
    ```
    

   * Zastąp `domainuser` ze swoją nazwą użytkownika w domenie.
   * Zastąp `ClusterShortName` z shortname dla klastra. Jeśli jest clustername https://sechadoopcontoso.azurehdisnight.net, `clustershortname` jest pierwszych sześciu liter dla klastra: sechad
   * Zastąp `jdbcurlvalue` adresem url JDBC z konfiguracji programu hive. Na przykład jdbc:hive2: / / headnodehost:10001 /; transportMode = http.
    
   * Aby zapisać plik, wybierz klawisze Ctrl + X, należy wprowadzić `Y`, a następnie wybierz pozycję **Enter**.

   * Plik właściwości musi znajdować się lokalnie, podczas uruchamiania zadań programu oozie

## <a name="creating-custom-hive-scripts-for-the-oozie-job"></a>Tworzenie skryptów niestandardowych hive dla zadania programu Oozie
Skrypty 2 hive, hive server 1 i hive server 2 mogą być tworzone jako pokazano poniżej:
-   Hive Server 1 plik:
1.  Poniższa instrukcja umożliwia tworzenie i edytowanie pliku dla akcji serwer 1 programu hive:
    ```bash
    nano countrowshive1.xml
    ```

2.  Tworzenie skryptu
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  Zapisz plik w systemie plików hdfs
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

-   Hive Server 2 pliku:
1.  Poniższa instrukcja umożliwia tworzenie i edytowanie pola hive server 2 akcji:
    ```bash
    nano countrowshive2.xml
    ```

2.  Tworzenie skryptu
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  Zapisz plik w systemie plików hdfs
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submission-of-oozie-jobs"></a>Przesyłanie zadań programu Oozie
Przesyłanie zadań programu oozie w przypadku klastrów przyłączonych do domeny jest podobny do przesyłania zadań programu oozie w klastry przyłączone do domeny.

Aby uzyskać więcej informacji można znaleźć [przesyłać oraz zarządzać nim](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-oozie-job-submission"></a>Wyniki z Oozie przesłania zadania
Ponieważ oozie zadania są uruchamiane w imieniu użytkownika, Yarn i Ranger inspekcji Pokaż dzienniki zadania wykonywane jako nazwa spersonifikowanego użytkownika. Interfejs wiersza polecenia dane wyjściowe zadania oozie wyglądać jak poniżej:


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

*    Dzienniki inspekcji platformy Ranger hive Server 2 oozie przedstawiono akcje wykonywania akcji w imieniu użytkownika. Ranger i Yarn widok jest widoczne tylko dla administratora klastra.

## <a name="configuration-of-user-authorization-in-oozie"></a>Konfiguracja uwierzytelnienia użytkownika w Oozie
Oozie samodzielnie ma konfiguracji autoryzacji użytkownika może uniemożliwić użytkownikom zatrzymywanie zabicia zadania przez innych użytkowników. Ta opcja jest włączona, ustawiając `oozie.service.AuthorizationService.security.enabled` do `true`. 

Szczegółowe informacje na ten temat można znaleźć w sekcji dokumentacji programu Oozie - [Konfiguracja autoryzacji użytkownika]( https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html):

Dla składników, takich jak hive server 1, gdy wtyczka platformy Ranger nie jest dostępna/obsługiwanych możliwe jest tylko gruboziarnistych systemu plików hdfs autoryzacji. Dobrym rozwiązaniem szczegółowe autoryzacji jest dostępna tylko za pomocą wtyczki platformy ranger.

## <a name="oozie-web-ui"></a>Interfejs użytkownika sieci web programu Oozie
Interfejs użytkownika sieci web programu Oozie zapewnia widok stanu zadań Oozie opartych na sieci web w klastrze. W domenie przyłączone do klastrów, które należy:

1. Dodaj [węzła brzegowego](../hdinsight-apps-use-edge-node.md) i włączyć [uwierzytelnianie SSH Kerberos](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. Postępuj zgodnie z [interfejsu użytkownika sieci web programu Oozie](../hdinsight-use-oozie-linux-mac.md) kroki, aby włączyć tunelowania SSH z węzłem krawędzi i dostęp do interfejsu użytkownika sieci web.

## <a name="next-steps"></a>Kolejne kroki
* [Używanie technologii Oozie z usługą Hadoop, aby zdefiniować i uruchomić przepływ pracy na opartą na systemie Linux usługi Azure HDInsight](../hdinsight-use-oozie-linux-mac.md)
* [Używanie koordynatora technologii Oozie na podstawie czasu](../hdinsight-use-oozie-coordinator-time.md)
* [Uruchamianie zapytań Hive przy użyciu protokołu SSH w klastrach HDInsight przyłączone do domeny](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
