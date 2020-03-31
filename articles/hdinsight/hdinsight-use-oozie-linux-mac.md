---
title: Korzystanie z przepływów pracy Usługi Hadoop Oozie w usłudze Azure HDInsight opartej na systemie Linux
description: Użyj Hadoop Oozie w hdinsight opartym na systemie Linux. Dowiedz się, jak zdefiniować przepływ pracy Oozie i przesłać zadanie Oozie.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: ece6fdb743035069bc6c666d6e90c76860f63e82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75744914"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Definiowanie i uruchamianie przepływu pracy w opartej na systemie Linux usłudze Azure HDInsight za pomocą programu Apache Oozie z narzędziem Apache Hadoop

Dowiedz się, jak korzystać z apache Oozie z Apache Hadoop na platformie Azure HDInsight. Oozie to system przepływu pracy i koordynacji, który zarządza zadaniami Hadoop. Oozie jest zintegrowany ze stosem Hadoop i obsługuje następujące zadania:

* Apache Hadoop MapaReduce
* Świnia Apache
* Ul Apache
* Apache Sqoop

Za pomocą funkcji Oozie można również zaplanować zadania specyficzne dla systemu, takie jak programy Java lub skrypty powłoki.

> [!NOTE]  
> Inną opcją definiowania przepływów pracy za pomocą usługi HDInsight jest użycie usługi Azure Data Factory. Aby dowiedzieć się więcej o fabryce danych, zobacz [Korzystanie z usługi Apache Pig i Apache Hive z fabryką danych][azure-data-factory-pig-hive]. Aby użyć funkcji Oozie w klastrach z pakietem zabezpieczeń przedsiębiorstwa, zobacz [Uruchamianie apache Oozie w klastrach programu HDInsight Hadoop z pakietem zabezpieczeń przedsiębiorstwa](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Klaster Hadoop na HDInsight**. Zobacz [Wprowadzenie do HDInsight w systemie Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Klient SSH**. Zobacz [Podłącz do HDInsight (Apache Hadoop) za pomocą SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Baza danych SQL platformy Azure**.  Zobacz [Tworzenie bazy danych SQL platformy Azure w witrynie Azure portal](../sql-database/sql-database-get-started.md).  W tym artykule użyto bazy danych o nazwie **oozietest**.

* [Schemat identyfikatorów URI](./hdinsight-hadoop-linux-information.md#URI-and-scheme) dla magazynu podstawowego klastrów. Dotyczy to `wasb://` usługi Azure `abfs://` Storage, usługi Azure `adl://` Data Lake Storage Gen2 lub usługi Azure Data Lake Storage Gen1. Jeśli bezpieczny transfer jest włączony dla usługi Azure `wasbs://`Storage, identyfikator URI będzie . Zobacz też [bezpieczny transfer](../storage/common/storage-require-secure-transfer.md).

## <a name="example-workflow"></a>Przykładowy przepływ pracy

Przepływ pracy używany w tym dokumencie zawiera dwie akcje. Akcje to definicje zadań, takich jak uruchamianie hive, Sqoop, MapReduce lub innych procesów:

![Diagram przepływu pracy programu HDInsight oozie](./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png)

1. Akcja Hive uruchamia skrypt HiveQL, aby `hivesampletable` wyodrębnić rekordy z dołączonego do usługi HDInsight. Każdy wiersz danych opisuje wizytę z określonego urządzenia przenośnego. Format rekordu wygląda następująco:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Skrypt hive używany w tym dokumencie zlicza całkowitą liczbę wizyt dla każdej platformy, takiej jak Android lub iPhone, i przechowuje liczby w nowej tabeli hive.

    Aby uzyskać więcej informacji na temat gałęzi, zobacz [Używanie gałęzi Apache hive z hdinsight][hdinsight-use-hive].

2. Akcja Sqoop eksportuje zawartość nowej tabeli hive do tabeli utworzonej w bazie danych SQL azure. Aby uzyskać więcej informacji o Sqoop, zobacz [Korzystanie z Apache Sqoop z HDInsight][hdinsight-use-sqoop].

> [!NOTE]  
> Aby uzyskać obsługiwane wersje Oozie w klastrach HDInsight, zobacz [Co nowego w wersjach klastra Hadoop dostarczonych przez HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Tworzenie katalogu roboczego

Oozie oczekuje, że do przechowywania wszystkich zasobów wymaganych dla zadania w tym samym katalogu. W tym `wasbs:///tutorials/useoozie`przykładzie użyto pliku . Aby utworzyć ten katalog, wykonaj następujące czynności:

1. Edytuj poniższy kod, aby zastąpić `sshuser` nazwą użytkownika SSH `CLUSTERNAME` dla klastra i zastąp nazwą klastra.  Następnie wprowadź kod, aby połączyć się z klastrem HDInsight [za pomocą programu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Aby utworzyć katalog, użyj następującego polecenia:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > Parametr `-p` powoduje utworzenie wszystkich katalogów w ścieżce. Katalog `data` jest używany do przechowywania danych `useooziewf.hql` używanych przez skrypt.

3. Edytuj poniższy kod, aby zastąpić `sshuser` go nazwą użytkownika SSH.  Aby upewnić się, że firma Oozie może personifikować konto użytkownika, użyj następującego polecenia:

    ```bash
    sudo adduser sshuser users
    ```

    > [!NOTE]  
    > Można zignorować błędy, które wskazują, że `users` użytkownik jest już członkiem grupy.

## <a name="add-a-database-driver"></a>Dodawanie sterownika bazy danych

Ponieważ ten przepływ pracy używa Sqoop do eksportowania danych do bazy danych SQL, należy podać kopię sterownika JDBC używanego do interakcji z bazą danych SQL. Aby skopiować sterownik JDBC do katalogu roboczego, użyj następującego polecenia z sesji SSH:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Sprawdź rzeczywisty sterownik JDBC, `/usr/share/java/`który istnieje w pliku .

Jeśli przepływ pracy używane inne zasoby, takie jak jar, który zawiera mapreduce aplikacji, należy dodać te zasoby, jak również.

## <a name="define-the-hive-query"></a>Definiowanie kwerendy gałęzi

Poniższe kroki można wykonać, aby utworzyć skrypt języka hive query (HiveQL), który definiuje kwerendę. Kwerendy użyjesz w przepływie pracy Oozie w dalszej części tego dokumentu.

1. Z połączenia SSH użyj następującego polecenia, `useooziewf.hql`aby utworzyć plik o nazwie:

    ```bash
    nano useooziewf.hql
    ```

1. Po otwarciu edytora nano GNU użyj następującej kwerendy jako zawartości pliku:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Istnieją dwie zmienne używane w skrypcie:

   * `${hiveTableName}`: Zawiera nazwę tabeli, która ma zostać utworzona.

   * `${hiveDataFolder}`: Zawiera lokalizację do przechowywania plików danych dla tabeli.

     Plik definicji przepływu pracy, workflow.xml w tym artykule, przekazuje te wartości do tego skryptu HiveQL w czasie wykonywania.

1. Aby zapisać plik, wybierz **klawisze Ctrl+X**, wprowadź **Y**, a następnie wybierz pozycję **Wprowadź**.  

1. Użyj następującego polecenia, aby skopiować `useooziewf.hql` do: `wasbs:///tutorials/useoozie/useooziewf.hql`

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    To polecenie `useooziewf.hql` przechowuje plik w magazynie zgodnym z systemem HDFS dla klastra.

## <a name="define-the-workflow"></a>Definiowanie przepływu pracy

Definicje przepływu pracy Oozie są zapisywane w języku definicji procesu Hadoop (hPDL), który jest językiem definicji procesu XML. Aby zdefiniować przepływ pracy, należy wykonać następujące czynności:

1. Użyj następującej instrukcji, aby utworzyć i edytować nowy plik:

    ```bash
    nano workflow.xml
    ```

2. Po otwarciu edytora nano wprowadź następujący kod XML jako zawartość pliku:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
        </hive>
        <ok to="RunSqoopExport"/>
        <error to="fail"/>
        </action>
        <action name="RunSqoopExport">
        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.compress.map.output</name>
                <value>true</value>
            </property>
            </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveDataFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\t"</arg>
            <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
            </sqoop>
        <ok to="end"/>
        <error to="fail"/>
        </action>
        <kill name="fail">
        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

    W przepływie pracy zdefiniowano dwie akcje:

   * `RunHiveScript`: Ta akcja jest działaniem `useooziewf.hql` początkowym i uruchamia skrypt Hive.

   * `RunSqoopExport`: Ta akcja eksportuje dane utworzone ze skryptu Hive do bazy danych SQL przy użyciu sqoop. Ta akcja jest `RunHiveScript` uruchamiana tylko wtedy, gdy akcja zakończy się pomyślnie.

     Przepływ pracy ma kilka wpisów, takich jak `${jobTracker}`. Te wpisy zostaną zastąpione wartościami używanymi w definicji zadania. Definicję zadania utworzysz w dalszej części tego dokumentu.

     Należy również `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` zwrócić uwagę na wpis w sekcji Sqoop. Ten wpis instruuje Oozie, aby udostępnić to archiwum dla Sqoop po uruchomieniu tej akcji.

3. Aby zapisać plik, wybierz **klawisze Ctrl+X**, wprowadź **Y**, a następnie wybierz pozycję **Wprowadź**.  

4. Użyj następującego polecenia, `workflow.xml` aby `/tutorials/useoozie/workflow.xml`skopiować plik do:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Tworzenie tabeli

> [!NOTE]  
> Istnieje wiele sposobów na połączenie się z bazą danych SQL, aby utworzyć tabelę. W poniższej procedurze użyto rozwiązania [FreeTDS](https://www.freetds.org/) z klastra usługi HDInsight.

1. Użyj następującego polecenia, aby zainstalować FreeTDS w klastrze HDInsight:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Edytuj poniższy kod, aby zastąpić `<serverName>` swoją `<sqlLogin>` nazwą serwera SQL platformy Azure i logowaniem serwera SQL azure.  Wprowadź polecenie, aby połączyć się z bazą danych SQL wymaganego.  Wprowadź hasło w wierszu polecenia.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Otrzymasz dane wyjściowe, takie jak następujący tekst:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. W wierszu `1>` wprowadź następujące wiersze:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Jeśli wprowadzono instrukcję `GO`, zostaną obliczone poprzednie instrukcje. Te instrukcje tworzą `mobiledata`tabelę o nazwie , która jest używana przez przepływ pracy.

    Aby sprawdzić, czy tabela została utworzona, użyj następujących poleceń:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Dane wyjściowe są widoczne w następujący sposób:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo             mobiledata      BASE TABLE

4. Wyjdź z narzędzia tsql, wpisując `exit` monit. `1>`

## <a name="create-the-job-definition"></a>Tworzenie definicji zadania

Definicja zadania opisuje, gdzie można znaleźć plik workflow.xml. Opisano w nim również, gdzie można znaleźć inne `useooziewf.hql`pliki używane przez przepływ pracy, takie jak . Ponadto definiuje wartości właściwości używanych w przepływie pracy i skojarzonych plików.

1. Aby uzyskać pełny adres magazynu domyślnego, użyj następującego polecenia. Ten adres jest używany w pliku konfiguracyjnym utworzonym w następnym kroku.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    To polecenie zwraca informacje, takie jak następujący kod XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Jeśli klaster HDInsight używa usługi Azure Storage `<value>` jako magazynu `wasbs://`domyślnego, zawartość elementu zaczyna się od . Jeśli zamiast tego używana jest usługa Azure Data `adl://`Lake Storage Gen1, zaczyna się od . Jeśli używana jest usługa Azure Data Lake `abfs://`Storage Gen2, zaczyna się od .

    Zapisz zawartość `<value>` elementu, ponieważ jest on używany w następnych krokach.

2. Edytuj xml poniżej w następujący sposób:

    |Wartość symbolu zastępczego| Zamieniono wartość|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| Wartość otrzymana z kroku 1.|
    |administrator| Twoja nazwa logowania dla klastra HDInsight, jeśli nie administratora.|
    |Nazwa_serwera| Nazwa serwera bazy danych SQL platformy Azure.|
    |sqlLogin (dziennik sqlLogin)| Logowanie do serwera bazy danych SQL platformy Azure.|
    |sql Hasło| Hasło logowania serwera bazy danych SQL platformy Azure.|

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=sqlLogin;password=sqlPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>admin</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

    Większość informacji w tym pliku jest używana do wypełniania wartości używanych w plikach workflow.xml `${nameNode}`lub ooziewf.hql, takich jak .  Jeśli ścieżka jest `wasbs` ścieżką, należy użyć pełnej ścieżki. Nie skracaj go `wasbs:///`do po prostu . Wpis `oozie.wf.application.path` określa, gdzie można znaleźć plik workflow.xml. Ten plik zawiera przepływ pracy, który został uruchomiony przez to zadanie.

3. Aby utworzyć konfigurację definicji zadania Oozie, należy użyć następującego polecenia:

    ```bash
    nano job.xml
    ```

4. Po otwarciu edytora nano wklej edytowany kod XML jako zawartość pliku.

5. Aby zapisać plik, wybierz **klawisze Ctrl+X**, wprowadź **Y**, a następnie wybierz pozycję **Wprowadź**.

## <a name="submit-and-manage-the-job"></a>Przesyłanie zadania i zarządzanie nim

Poniższe kroki używają polecenia Oozie do przesyłania przepływów pracy usługi Oozie i zarządzania nimi w klastrze. Polecenie Oozie jest przyjaznym interfejsem za pomocą [interfejsu API Oozie REST](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]  
> W przypadku korzystania z polecenia Oozie należy użyć pliku FQDN dla węzła głównego HDInsight. Ta nazwa FQDN jest dostępna tylko z klastra lub jeśli klaster znajduje się w sieci wirtualnej platformy Azure, z innych maszyn w tej samej sieci.

1. Aby uzyskać adres URL usługi Oozie, użyj następującego polecenia:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Spowoduje to zwraca informacje, takie jak następujący kod XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    Część `http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie` jest adresem URL używanym z poleceniem Oozie.

2. Edytuj kod, aby zastąpić adres URL adresem, który został odebrany wcześniej. Aby utworzyć zmienną środowiskową dla adresu URL, użyj następujących czynności, aby nie trzeba było jej wprowadzać dla każdego polecenia:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. Aby przesłać zadanie, użyj następujących czynności:

    ```bash
    oozie job -config job.xml -submit
    ```

    To polecenie ładuje informacje `job.xml` o zadaniu i przesyła je do Oozie, ale nie uruchamia ich.

    Po zakończeniu polecenia należy zwrócić identyfikator zadania, na przykład `0000005-150622124850154-oozie-oozi-W`. Ten identyfikator jest używany do zarządzania zadaniem.

4. Edytuj poniższy kod, aby zastąpić `<JOBID>` identyfikatorem zwróconym w poprzednim kroku.  Aby wyświetlić stan zadania, użyj następującego polecenia:

    ```bash
    oozie job -info <JOBID>
    ```

    Zwraca informacje, takie jak następujący tekst:

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasb:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    To zadanie ma `PREP`status . Ten stan wskazuje, że zadanie zostało utworzone, ale nie zostało uruchomione.

5. Edytuj poniższy kod, aby zastąpić `<JOBID>` identyfikatorem zwróconym wcześniej.  Aby uruchomić zadanie, użyj następującego polecenia:

    ```bash
    oozie job -start <JOBID>
    ```

    Jeśli sprawdzisz stan po tym poleceniu, jest w stanie uruchomionym, a informacje są zwracane dla akcji w zadaniu.  Zadanie potrwa kilka minut.

6. Edytuj poniższy kod, aby zastąpić `<serverName>` swoją `<sqlLogin>` nazwą serwera SQL platformy Azure i logowaniem serwera SQL azure.  *Po pomyślnym zakończeniu zadania* można sprawdzić, czy dane zostały wygenerowane i wyeksportowane do tabeli bazy danych SQL za pomocą następującego polecenia.  Wprowadź hasło w wierszu polecenia.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    W `1>` wierszu polecenia wprowadź następującą kwerendę:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Zwracane informacje są podobne do następującego tekstu:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Aby uzyskać więcej informacji na temat polecenia Oozie, zobacz [Narzędzie wiersza polecenia Apache Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie REST API

Za pomocą interfejsu API Oozie REST można tworzyć własne narzędzia, które współpracują z Oozie. Poniżej przedstawiono informacje specyficzne dla usługi HDInsight dotyczące korzystania z interfejsu API Oozie REST:

* **Identyfikator URI**: Dostęp do interfejsu API `https://CLUSTERNAME.azurehdinsight.net/oozie`REST można uzyskać spoza klastra pod adresem .

* **Uwierzytelnianie**: Aby uwierzytelnić, użyj interfejsu API konta HTTP klastra (administratora) i hasła. Przykład:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Aby uzyskać więcej informacji na temat korzystania z interfejsu API Oozie REST, zobacz [Apache Oozie Web Services API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Interfejs użytkownika sieci Oozie

Interfejs użytkownika sieci Web Oozie udostępnia widok oparty na sieci Web do stanu zadań Oozie w klastrze. Za pomocą internetowego interfejsu użytkownika można wyświetlić następujące informacje:

   * Stan zadania
   * Definicja zadania
   * Konfigurowanie
   * Wykres działań w zadaniu
   * Dzienniki dla zadania

Można również wyświetlić szczegóły dotyczące akcji w zadaniu.

Aby uzyskać dostęp do interfejsu użytkownika sieci Web Oozie, wykonaj następujące kroki:

1. Utwórz tunel SSH do klastra HDInsight. Aby uzyskać więcej informacji, zobacz [Korzystanie z tunelowania SSH z hdinsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Po utworzeniu tunelu otwórz interfejs użytkownika sieci Web Ambari `http://headnodehost:8080`w przeglądarce internetowej za pomocą identyfikatora URI .

3. Z lewej strony wybierz pozycję **Oozie** > **Quick Links** > **Oozie Web UI**.

    ![Apache Ambari oozie web ui kroki](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. Domyślnie oozie interfejsu użytkownika sieci web, aby wyświetlić uruchomione zadania przepływu pracy. Aby wyświetlić wszystkie zadania przepływu pracy, wybierz pozycję **Wszystkie zadania**.

    ![Zadania przepływu pracy konsoli sieci Web Oozie](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. Aby wyświetlić więcej informacji o zadaniu, wybierz zadanie.

    ![INFORMACJE O pracy HDInsight Apache Oozie](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. Na karcie **Informacje o z oejdie** można wyświetlić podstawowe informacje o zadaniu i poszczególne akcje w zadaniu. Za pomocą kart u góry można wyświetlić **definicję zadania,** **konfigurację zadania**, dostęp do **dziennika zadań**lub wyświetlić skierowany wykres acykliczny (DAG) zadania w obszarze **Zadanie DAG**.

   * **Dziennik zadań:** Wybierz przycisk **Pobierz dzienniki,** aby uzyskać wszystkie dzienniki dla zadania, lub użyj pola **Wprowadź filtr wyszukiwania,** aby filtrować dzienniki.

       ![HdInsight Apache Oozie dziennik pracy](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **Zadanie DAG:** DAG to graficzny przegląd ścieżek danych przejmunych przez przepływ pracy.

       ![HDInsight Apache Oozie praca dag](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. Jeśli wybierzesz jedną z akcji z karty **Informacje o zadaniu,** zostaną wyświetlene informacje o akcji. Na przykład wybierz **RunSqoopExport** akcji.

    ![Informacje o akcji oozie oozie](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. Możesz zobaczyć szczegóły akcji, takie jak łącze do **adresu URL konsoli**. Użyj tego łącza, aby wyświetlić informacje o monitorze zadań dla zadania.

## <a name="schedule-jobs"></a>Planowanie zadań

Koordynatora można użyć do określenia początku, końca i częstotliwości występowania zadań. Aby zdefiniować harmonogram przepływu pracy, wykonaj następujące kroki:

1. Aby utworzyć plik o nazwie **coordinator.xml,** użyj następującego polecenia:

    ```bash
    nano coordinator.xml
    ```

    Użyj następującego kodu XML jako zawartości pliku:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]  
    > Zmienne `${...}` są zastępowane wartościami w definicji zadania w czasie wykonywania. Zmienne to:
    >
    > * `${coordFrequency}`: Czas między uruchomionymi wystąpieniami zadania.
    > * `${coordStart}`: Godzina rozpoczęcia zadania.
    > * `${coordEnd}`: Godzina zakończenia zadania.
    > * `${coordTimezone}`: Zadania koordynatora znajdują się w stałej strefie czasowej bez czasu letniego, zazwyczaj reprezentowane przy użyciu czasu UTC. Ta strefa czasowa jest określana jako *strefa czasowa przetwarzania Oozie.*
    > * `${wfPath}`: Ścieżka do pliku workflow.xml.

2. Aby zapisać plik, wybierz **klawisze Ctrl+X**, wprowadź **Y**, a następnie wybierz pozycję **Wprowadź**.

3. Aby skopiować plik do katalogu roboczego dla tego zadania, użyj następującego polecenia:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Aby zmodyfikować utworzony wcześniej `job.xml` plik, użyj następującego polecenia:

    ```bash
    nano job.xml
    ```

    Wprowadź następujące zmiany:

   * Aby poinstruować Oozie, aby uruchamiała plik koordynatora zamiast przepływu pracy, zmień `<name>oozie.wf.application.path</name>` na `<name>oozie.coord.application.path</name>`.

   * Aby ustawić `workflowPath` zmienną używaną przez koordynatora, dodaj następujący kod XML:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Zastąp `wasbs://mycontainer@mystorageaccount.blob.core.windows` tekst wartością używaną w innych wpisach w pliku job.xml.

   * Aby zdefiniować początek, koniec i częstotliwość dla koordynatora, dodaj następujący kod XML:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2018-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2018-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Te wartości ustawiają czas rozpoczęcia 12:00 10 maja 2018 r., a czas zakończenia na 12 maja 2018 r. Interwał uruchamiania tego zadania jest ustawiony na codziennie. Częstotliwość jest w minutach, więc 24 godziny x 60 minut = 1440 minut. Na koniec strefa czasowa jest ustawiona na UTC.

5. Aby zapisać plik, wybierz **klawisze Ctrl+X**, wprowadź **Y**, a następnie wybierz pozycję **Wprowadź**.

6. Aby przesłać i uruchomić zadanie, użyj następującego polecenia:

    ```bash
    oozie job -config job.xml -run
    ```

7. Jeśli przejdziesz do interfejsu użytkownika sieci Web Oozie i wybierzesz kartę **Zadania koordynatora,** zobaczysz informacje, takie jak na poniższej ilustracji:

    ![Karta Zadania koordynatora konsoli internetowej Oozie](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    Następny wpis **materializacji** zawiera przy następnym uruchomieniu zadania.

8. Podobnie jak wcześniejsze zadanie przepływu pracy, jeśli wybierzesz wpis zlecenia w interfejsie użytkownika sieci Web, zostaną wyświetlone informacje o zadaniu:

    ![Apache Oozie koordynator pracy info](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > Ten obraz pokazuje tylko pomyślne przebiegi zadania, a nie poszczególne akcje w ramach zaplanowanego przepływu pracy. Aby wyświetlić poszczególne akcje, wybierz jeden z wpisów **Akcji.**

    ![Karta Informacje o pracy konsoli internetowej OOzie](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Za pomocą interfejsu użytkownika Oozie można wyświetlać dzienniki Oozie. Interfejs użytkownika Oozie zawiera również łącza do dzienników JobTracker dla zadań MapReduce, które zostały uruchomione przez przepływ pracy. Wzorzec rozwiązywania problemów powinien być:

   1. Wyświetl zadanie w interfejsie użytkownika sieci Web Oozie.

   2. Jeśli występuje błąd lub błąd dla określonej akcji, wybierz akcję, aby sprawdzić, czy pole **Komunikat o błędzie** zawiera więcej informacji na temat błędu.

   3. Jeśli jest to możliwe, użyj adresu URL z akcji, aby wyświetlić więcej szczegółów, takich jak dzienniki JobTracker, dla akcji.

Poniżej przedstawiono konkretne błędy, które mogą wystąpić i jak je rozwiązać.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Nie można zainicjować klastra

**Symptomy**: Stan zadania zmienia się na **ZAWIESZONY**. Szczegóły dotyczące zadania `RunHiveScript` pokazują stan **jako START_MANUAL**. Wybranie akcji powoduje wyświetlenie następującego komunikatu o błędzie:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Przyczyna:** Adresy magazynu obiektów Blob platformy Azure używane w pliku **job.xml** nie zawierają nazwy kontenera magazynu ani konta magazynu. Format adresu magazynu obiektów `wasbs://containername@storageaccountname.blob.core.windows.net`blob musi być .

**Rozwiązanie**: Zmień adresy magazynu obiektów blob używane przez zadanie.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltusergt"></a>JA002: Oozie nie może podszywać się pod &lt;UŻYTKOWNIKA&gt;

**Symptomy**: Stan zadania zmienia się na **ZAWIESZONY**. Szczegóły dotyczące zadania `RunHiveScript` pokazują stan **jako START_MANUAL**. Jeśli wybierzesz akcję, zostanie wyświetlony następujący komunikat o błędzie:

    JA002: User: oozie is not allowed to impersonate <USER>

**Przyczyna:** Bieżące ustawienia uprawnień nie zezwalają Oozie na podszywanie się pod określone konto użytkownika.

**Rozwiązanie**: Oozie może personifikować użytkowników w grupie **użytkowników.** Użyj, `groups USERNAME` aby wyświetlić grupy, których członkiem jest konto użytkownika. Jeśli użytkownik nie jest członkiem grupy **użytkowników,** użyj następującego polecenia, aby dodać użytkownika do grupy:

    sudo adduser USERNAME users

> [!NOTE]  
> Może upłynąć kilka minut, zanim program HDInsight rozpozna, że użytkownik został dodany do grupy.

### <a name="launcher-error-sqoop"></a>Błąd wyrzutni (Sqoop)

**Symptomy**: Stan zadania zmienia się na **ZABITY**. Szczegóły zadania pokazują `RunSqoopExport` stan jako **BŁĄD**. Jeśli wybierzesz akcję, zostanie wyświetlony następujący komunikat o błędzie:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Przyczyna:** Sqoop nie może załadować sterownika bazy danych wymaganego do uzyskania dostępu do bazy danych.

**Rozwiązanie:** Podczas korzystania z Sqoop z zadania Oozie, należy dołączyć sterownik bazy danych z innymi zasobami, takimi jak workflow.xml, zadanie używa. Ponadto odwołaj się do archiwum, `<sqoop>...</sqoop>` które zawiera sterownik bazy danych z sekcji workflow.xml.

Na przykład dla zadania w tym dokumencie należy wykonać następujące kroki:

1. Skopiuj `mssql-jdbc-7.0.0.jre8.jar` plik do **katalogu /tutorials/useoozie:**

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Zmodyfikuj, `workflow.xml` aby dodać następujący `</sqoop>`kod XML w nowym wierszu powyżej:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak zdefiniować przepływ pracy Oozie i jak uruchomić zadanie Oozie. Aby dowiedzieć się więcej o pracy z programem HDInsight, zobacz następujące artykuły:

* [Przesyłanie danych dla zadań Apache Hadoop w HDInsight][hdinsight-upload-data]
* [Użyj Apache Sqoop z Apache Hadoop w HDInsight][hdinsight-use-sqoop]
* [Użyj Apache Hive z Apache Hadoop na HDInsight][hdinsight-use-hive]
* [Tworzenie programów Java MapReduce dla programu HDInsight](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)

[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
