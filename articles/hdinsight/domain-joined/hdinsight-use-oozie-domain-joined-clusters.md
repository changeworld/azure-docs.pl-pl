---
title: Przepływy pracy Apache Oozie & enterprise security — Azure HDInsight
description: Bezpieczne przepływy pracy Apache Oozie przy użyciu pakietu zabezpieczeń usługi Azure HDInsight Enterprise Security Package. Dowiedz się, jak zdefiniować przepływ pracy Oozie i przesłać zadanie Oozie.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seodec18
ms.date: 12/09/2019
ms.openlocfilehash: 9ef54707f7fac3dd1328e29f6d05f62c1dee2561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194907"
---
# <a name="run-apache-oozie-in-hdinsight-hadoop-clusters-with-enterprise-security-package"></a>Uruchamianie apache Oozie w klastrach HDInsight Hadoop z pakietem zabezpieczeń dla przedsiębiorstw

Apache Oozie to system przepływu pracy i koordynacji, który zarządza zadaniami Apache Hadoop. Oozie jest zintegrowany ze stosem Hadoop i obsługuje następujące zadania:

- Apache MapReduce
- Świnia Apache
- Ul Apache
- Apache Sqoop

Za pomocą funkcji Oozie można również zaplanować zadania specyficzne dla systemu, takie jak programy Java lub skrypty powłoki.

## <a name="prerequisite"></a>Wymagania wstępne

Klaster usługi Azure HDInsight Hadoop z pakietem zabezpieczeń przedsiębiorstwa (ESP). Zobacz [Konfigurowanie klastrów HDInsight za pomocą pliku ESP](./apache-domain-joined-configure-using-azure-adds.md).

> [!NOTE]  
> Aby uzyskać szczegółowe instrukcje dotyczące używania usługi Oozie w klastrach innych niż ESP, zobacz [Korzystanie z przepływów pracy Apache Oozie w usłudze Azure HDInsight opartej na systemie Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="connect-to-an-esp-cluster"></a>Łączenie się z klastrem ESP

Aby uzyskać więcej informacji na temat bezpiecznej powłoki (SSH), zobacz [Łączenie się z HDInsight (Hadoop) za pomocą SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Połącz się z klastrem HDInsight za pomocą funkcji SSH:

    ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```

1. Aby sprawdzić pomyślne uwierzytelnianie `klist` Kerberos, użyj tego polecenia. Jeśli nie, `kinit` użyj, aby uruchomić uwierzytelnianie Kerberos.

1. Zaloguj się do bramy USŁUGI HDInsight, aby zarejestrować token OAuth wymagany do uzyskania dostępu do usługi Azure Data Lake Storage:

    ```bash
    curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
    ```

    Kod odpowiedzi stanu **200 OK** wskazuje pomyślną rejestrację. Sprawdź nazwę użytkownika i hasło, jeśli zostanie odebrana nieautoryzowana odpowiedź, na przykład 401.

## <a name="define-the-workflow"></a>Definiowanie przepływu pracy

Definicje przepływu pracy Oozie są zapisywane w języku definicji procesu Apache Hadoop (hPDL). hPDL jest językiem definicji procesu XML. Aby zdefiniować przepływ pracy, należy wykonać następujące kroki:

1. Konfigurowanie obszaru roboczego użytkownika domeny:

   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```

   Zamień `DomainUser` na nazwę użytkownika domeny.
   Zamień `DomainUserPath` ścieżkę katalogu macierzystego dla użytkownika domeny.
   Zamień `ClusterVersion` wersję platformy danych klastra.

2. Użyj następującej instrukcji, aby utworzyć i edytować nowy plik:

   ```bash
   nano workflow.xml
   ```

3. Po otwarciu edytora nano wprowadź następujący kod XML jako zawartość pliku:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://<active-headnode-name>-<clustername>.<Domain>.com:9083</value>
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

4. Zamień `clustername` na nazwę klastra.

5. Aby zapisać plik, wybierz **ctrl+X**. Wprowadź **Y**. Następnie wybierz **pozycję Wprowadź**.

    Przepływ pracy jest podzielony na dwie części:

   - **Poświadczeń.** W tej sekcji przyjmuje poświadczenia, które są używane do uwierzytelniania akcji Oozie:

     W tym przykładzie użyto uwierzytelniania dla akcji gałęzi. Aby dowiedzieć się więcej, zobacz [Uwierzytelnianie akcji](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

     Usługa poświadczeń umożliwia akcje Oozie personifikacji użytkownika w celu uzyskania dostępu do usług Hadoop.

   - **Działania.** W tej sekcji są trzy akcje: map-reduce, Hive server 2 i Hive server 1:

     - Akcja map-reduce uruchamia przykład z pakietu Oozie dla map-reduce, który wyprowadza zagregowane liczby wyrazów.

     - Akcje serwera hive 2 i hive server 1 uruchamiają kwerendę w przykładowej tabeli gałęzi dostarczonej z programem HDInsight.

     Akcje Gałęzi używają poświadczeń zdefiniowanych w sekcji poświadczenia `cred` do uwierzytelniania przy użyciu słowa kluczowego w elemencie akcji.

6. Użyj następującego polecenia, `workflow.xml` aby `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`skopiować plik do:

    ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
    ```

7. Zamień `domainuser` swoją nazwę użytkownika dla domeny.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Definiowanie pliku właściwości zadania Oozie

1. Użyj następującej instrukcji, aby utworzyć i edytować nowy plik dla właściwości zadania:

    ```bash
    nano job.properties
    ```

2. Po otwarciu edytora nano użyj następującego kodu XML jako zawartości pliku:

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
   jdbcPrincipal=hive/<active-headnode-name>.<Domain>.com@<Domain>.COM
   jdbcURL=[jdbcurlvalue]
   hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
   hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   - Użyj `adl://home` identyfikatora URI dla `nameNode` właściwości, jeśli masz usługi Azure Data Lake Storage Gen1 jako podstawowy magazyn klastra. Jeśli używasz usługi Azure Blob Storage, `wasb://home`zmień to na . Jeśli używasz usługi Azure Data Lake Storage Gen2, zmień to na `abfs://home`.
   - Zamień `domainuser` swoją nazwę użytkownika dla domeny.  
   - Zamień `ClusterShortName` na krótką nazwę klastra. Na przykład, jeśli nazwa klastra jest https:// *[przykładowe łącze]* sechadoopcontoso.azurehdisnight.net, jest to `clustershortname` pierwsze sześć znaków klastra: **sechad**.  
   - Zamień `jdbcurlvalue` na adres URL JDBC z konfiguracji gałęzi. Przykładem jest jdbc:hive2://headnodehost:10001/;transportMode=http.
   - Aby zapisać plik, wybierz ctrl+X, wprowadź , `Y`a następnie wybierz pozycję **Wprowadź**.

   Ten plik właściwości musi być obecny lokalnie podczas uruchamiania zadań Oozie.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Tworzenie niestandardowych skryptów gałęzi dla zadań Oozie

Można utworzyć dwa skrypty hive dla serwera hive 1 i hive server 2, jak pokazano w poniższych sekcjach.

### <a name="hive-server-1-file"></a>Plik serwera hive 1

1. Tworzenie i edytowanie pliku dla akcji serwera hive 1:

    ```bash
    nano countrowshive1.hql
    ```

2. Utwórz skrypt:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    select devicemake from hivesampletable limit 2;
    ```

3. Zapisz plik w apache Hadoop Distributed File System (HDFS):

    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Plik serwera hive 2

1. Tworzenie i edytowanie pola dla akcji serwera hive 2:

    ```bash
    nano countrowshive2.hql
    ```

2. Utwórz skrypt:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3. Zapisz plik w systemie plików HDFS:

    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Prześlij oferty pracy Oozie

Przesyłanie zadań Oozie dla klastrów ESP jest jak przesyłanie zadań Oozie w klastrach innych niż ESP.

Aby uzyskać więcej informacji, zobacz [Używanie apache Oozie z Apache Hadoop do definiowania i uruchamiania przepływu pracy na platformie Azure HDInsight opartej na systemie Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Wyniki z zgłoszenia oozie pracy

Zadania Oozie są uruchamiane dla użytkownika. Tak więc zarówno Apache Hadoop YARN, jak i Apache Ranger dzienniki inspekcji pokazują zadania uruchamiane jako personifikowany użytkownik. Dane wyjściowe interfejsu wiersza polecenia zadania Oozie wygląda następująco:

```output
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

Dzienniki inspekcji Ranger dla hive server 2 akcje pokazują Oozie uruchomienie akcji dla użytkownika. Widoki Ranger i YARN są widoczne tylko dla administratora klastra.

## <a name="configure-user-authorization-in-oozie"></a>Konfigurowanie autoryzacji użytkownika w Oozie

Oozie sama w sobie ma konfigurację autoryzacji użytkownika, która może uniemożliwić użytkownikom zatrzymywanie lub usuwanie zadań innych użytkowników. Aby włączyć tę konfigurację, ustaw na `oozie.service.AuthorizationService.security.enabled` . `true` 

Aby uzyskać więcej informacji, zobacz [Apache Oozie Instalacja i konfiguracja](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

W przypadku składników, takich jak serwer Hive 1, w których wtyczka Ranger nie jest dostępna lub obsługiwana, możliwa jest tylko autoryzacja gruboziarnistej usługi HDFS. Autoryzacja drobnoziarnista jest dostępna tylko za pośrednictwem wtyczek Ranger.

## <a name="get-the-oozie-web-ui"></a>Pobierz interfejs użytkownika sieci Oozie

Interfejs użytkownika sieci Web Oozie udostępnia widok oparty na sieci Web do stanu zadań Oozie w klastrze. Aby uzyskać interfejs użytkownika sieci Web, należy wykonać następujące kroki w klastrach ESP:

1. Dodaj [węzeł krawędzi](../hdinsight-apps-use-edge-node.md) i włącz [uwierzytelnianie Protokołu Kerberos .](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. Postępuj zgodnie z krokiami [interfejsu użytkownika sieci Web Oozie,](../hdinsight-use-oozie-linux-mac.md) aby włączyć tunelowanie SSH do węzła brzegowego i uzyskać dostęp do interfejsu użytkownika sieci Web.

## <a name="next-steps"></a>Następne kroki

- [Użyj Apache Oozie z Apache Hadoop, aby zdefiniować i uruchomić przepływ pracy na platformie Azure HDInsight opartej na systemie Linux](../hdinsight-use-oozie-linux-mac.md).
- [Połącz się z HDInsight (Apache Hadoop) za pomocą SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
