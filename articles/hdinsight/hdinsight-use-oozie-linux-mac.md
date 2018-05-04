---
title: Użyj przepływy pracy Hadoop Oozie w usłudze Azure HDInsight opartych na systemie Linux | Dokumentacja firmy Microsoft
description: Użyj Hadoop Oozie w HDInsight opartych na systemie Linux. Dowiedz się, jak zdefiniować Oozie przepływu pracy i przesłać zadanie Oozie.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 8a25507ab076c4eecccea4e8a503d68ff1441ae5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Za pomocą Oozie Hadoop do definiowania i uruchomić przepływ pracy w usłudze Azure HDInsight opartych na systemie Linux

[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Dowiedz się, jak używać Apache Oozie z platformą Hadoop w usłudze Azure HDInsight. Oozie jest przepływu pracy i koordynacji system, który zarządza zadaniami na platformie Hadoop. Oozie jest zintegrowany ze stosem platformy Hadoop i obsługuje następujące zadania:

* Apache MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

Umożliwia także Oozie do planowania zadań, które są specyficzne dla systemu, np. programów Java lub skryptów powłoki.

> [!NOTE]
> Innym rozwiązaniem do definiowania przepływów pracy z usługą HDInsight jest do używania fabryki danych Azure. Aby dowiedzieć się więcej na temat fabryki danych, zobacz [Use Pig i Hive z fabryką danych][azure-data-factory-pig-hive].

> [!IMPORTANT]
> Oozie nie jest włączona w usłudze HDInsight z przyłączonych do domeny.

## <a name="prerequisites"></a>Wymagania wstępne

* **Klaster usługi HDInsight**: zobacz [Rozpoczynanie pracy z usługą HDInsight w systemie Linux](/hadoop/apache-hadoop-linux-tutorial-get-started.md)

> [!IMPORTANT]
> Kroki opisane w tym dokumencie wymagają klastra usługi HDInsight, który używa systemu Linux. Linux jest tylko system operacyjny używany w usłudze HDInsight w wersji 3.4 lub nowszej. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="example-workflow"></a>Przykładowy przepływ pracy

Przepływ pracy, używane w tym dokumencie zawiera dwie akcje. Akcje są definicje dla zadania, takie jak uruchomienie Hive, Sqoop, MapReduce lub innych procesów:

![Diagram przepływu pracy][img-workflow-diagram]

1. Akcja Hive uruchamia skrypt HiveQL, aby wyodrębnić rekordy z **hivesampletable** który wchodzi w skład HDInsight. Każdy wiersz danych opisuje odwiedziny od określonego urządzenia przenośnego. Format rekordu wygląda następujący tekst:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    Skrypt Hive używane w tym dokumencie liczby całkowitej kontroli dla poszczególnych platform, takich jak Android i iPhone i przechowywania liczby do nowej tabeli Hive.

    Aby uzyskać więcej informacji na temat programu Hive, zobacz temat [Use Hive with HDInsight][hdinsight-use-hive] (Korzystanie z programu Hive z usługą HDInsight).

2. Akcja Sqoop eksportuje zawartość nową tabelę programu Hive z tabelą utworzone w bazie danych SQL Azure. Aby uzyskać więcej informacji o Sqoop, zobacz [Hadoop Sqoop użycia z usługą HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Obsługiwane wersje Oozie w klastrach HDInsight, zobacz [nowości w wersjach klastra Hadoop dostarczanych z usługą HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Utwórz katalog roboczy

Oozie oczekuje na przechowywanie wszystkich zasobów, które są wymagane dla zadania w tym samym katalogu. W tym przykładzie użyto **wasb: / / / samouczki/useoozie**. Aby utworzyć ten katalog, wykonaj następujące kroki:

1. Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Zastąp `sshuser` z nazwą użytkownika SSH dla klastra. Zastąp `clustername` nazwą klastra. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Można utworzyć katalogu, użyj następującego polecenia:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]
    > `-p` Parametr powoduje utworzenie wszystkich katalogów w ścieżce. **Danych** katalog jest używany do przechowywania danych używanych przez **useooziewf.hql** skryptu.

3. Aby upewnić się, że Oozie mogą personifikować konta użytkownika, użyj następującego polecenia:

    ```bash
    sudo adduser username users
    ```

    Zastąp `username` z nazwą użytkownika SSH.

    > [!NOTE]
    > Możesz zignorować błędów wskazujących, użytkownik jest już członkiem `users` grupy.

## <a name="add-a-database-driver"></a>Dodaj sterownik bazy danych

Ponieważ ten przepływ pracy używa Sqoop, aby wyeksportować dane do bazy danych SQL, należy podać kopię sterownik JDBC służy do interakcji z bazy danych SQL. Aby skopiować sterownik JDBC do katalogu roboczego, użyj następującego polecenia w sesji SSH:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/
```

> [!NOTE]
> Może zostać wyświetlony komunikat, że plik już istnieje.

Jeśli przepływ pracy używane inne zasoby, takie jak jar, który zawiera aplikację MapReduce, należy dodać także te zasoby.

## <a name="define-the-hive-query"></a>Zdefiniuj zapytanie Hive

Poniższe kroki umożliwiają utworzenie skryptu języka (HiveQL) zapytania Hive, który definiuje kwerendę. Zapytanie będzie używany w przepływie pracy Oozie w dalszej części tego dokumentu.

1. Z połączenia SSH, użyj następującego polecenia, aby utworzyć plik o nazwie `useooziewf.hql`:

    ```bash
    nano useooziewf.hql
    ```

3. Po otwarciu edytora nano GNU, użyj następującego zapytania jako zawartość pliku:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Istnieją dwie zmienne używane w skrypcie:

    * `${hiveTableName}`: Zawiera nazwę tabeli, która ma zostać utworzony.

    * `${hiveDataFolder}`: Zawiera lokalizację do przechowywania plików danych dla tabeli.

    Plik definicji przepływu pracy workflow.xml w tym samouczku przekazuje te wartości na ten skrypt HiveQL w czasie wykonywania.

4. Aby zakończyć działanie edytora, wybierz kombinację klawiszy Ctrl + X. Po wyświetleniu monitu wybierz `Y` można zapisać pliku, wprowadź `useooziewf.hql` jako nazwę pliku, a następnie wybierz **Enter**.

5. Użyj następujących poleceń, aby skopiować `useooziewf.hql` do `wasb:///tutorials/useoozie/useooziewf.hql`:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    To polecenie zapisuje `useooziewf.hql` pliku w magazynie systemu plików HDFS zgodnego dla klastra.

## <a name="define-the-workflow"></a>Zdefiniuj przepływu pracy

Oozie definicji przepływu pracy są zapisywane w Hadoop procesu Definition Language (hPDL), który jest językiem definicji XML procesu. Aby zdefiniować przepływu pracy, wykonaj następujące kroki:

1. Można tworzyć i edytować plik, należy użyć następującej instrukcji:

    ```bash
    nano workflow.xml
    ```

2. Po otwarciu edytora nano wprowadzili zawartość pliku XML następujące:

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
            <archive>sqljdbc41.jar</archive>
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

    Istnieją dwie akcje zdefiniowane w przepływie pracy:

   * `RunHiveScript`: Ta akcja jest akcji uruchamiania i uruchamia `useooziewf.hql` wykonanie skryptu technologii Hive.

   * `RunSqoopExport`: Ta akcja umożliwia wyeksportowanie danych utworzone na podstawie skryptu Hive z bazą danych SQL za pomocą Sqoop. Ta akcja działa tylko wtedy, jeśli `RunHiveScript` akcja zakończy się pomyślnie.

     Przepływ pracy ma kilka wpisów, takich jak `${jobTracker}`. Te wpisy spowoduje zamianę wartości używanej w definicji zadania. Utworzysz definicji zadania w dalszej części tego dokumentu.

     Należy również zauważyć `<archive>sqljdbc4.jar</archive>` wpis w sekcji Sqoop. Ten wpis informuje Oozie, aby udostępnić to archiwum Sqoop po uruchomieniu tej akcji.

3. Aby zapisać plik, wybierz kombinację klawiszy Ctrl + X, wprowadź `Y`, a następnie wybierz **Enter**. 

4. Użyj następującego polecenia, aby skopiować `workflow.xml` pliku `/tutorials/useoozie/workflow.xml`:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-the-database"></a>Utwórz bazę danych

Aby utworzyć bazę danych SQL, postępuj zgodnie z instrukcjami [Utwórz bazę danych SQL](../sql-database/sql-database-get-started.md) dokumentu. Podczas tworzenia bazy danych, użyj `oozietest` jako nazwa bazy danych. Również Zanotuj nazwę serwera bazy danych.

### <a name="create-the-table"></a>Tworzenie tabeli

> [!NOTE]
> Istnieje wiele sposobów łączenia z bazą danych SQL, aby utworzyć tabelę. Następujące kroki użyj [protokół FreeTDS](http://www.freetds.org/) z klastrem usługi HDInsight.


1. Aby zainstalować protokół FreeTDS w klastrze usługi HDInsight, użyj następującego polecenia:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Po zainstalowaniu protokół FreeTDS należy użyć następującego polecenia do łączenia się z serwerem bazy danych SQL, utworzonej wcześniej:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest
    ```

    Pojawi się dane wyjściowe podobne do następującego tekstu:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. W `1>` monit, wprowadź następujące wiersze:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Gdy `GO` instrukcja została wprowadzona, poprzednie instrukcje są oceniane. Te instrukcje tworzenia tabeli o nazwie **mobiledata**, który jest używany przez przepływ pracy.

    Aby sprawdzić, czy tabela została utworzona, użyj następujących poleceń:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Zostaną wyświetlone dane wyjściowe podobne do następującego tekstu:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE

4. Aby wyjść z narzędzia tsql, wprowadź `exit` na `1>` wiersza.

## <a name="create-the-job-definition"></a>Tworzenie definicji zadania

Definicji zadania opisano, gdzie można znaleźć workflow.xml. Gdzie można znaleźć inne pliki używane przez przepływ pracy, takich jak opisano również `useooziewf.hql`. Ponadto definiuje wartości dla właściwości używane w ramach przepływu pracy i skojarzone pliki.

1. Aby uzyskać pełny adres magazynu domyślne, użyj następującego polecenia. Ten adres jest używany w pliku konfiguracji, którą utworzysz w następnym kroku.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    To polecenie zwraca informacje, na przykład następujący kod XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]
    > Jeśli klaster usługi HDInsight używa magazynu Azure jako magazynu domyślne `<value>` zaczynać zawartość elementu `wasb://`. Jeśli zamiast niego jest używana usługa Azure Data Lake Store, rozpoczyna się od `adl://`.

    Zapisz zawartość `<value>` elementu, ponieważ jest używany w następnych krokach.

2. Aby utworzyć konfigurację definicji zadania Oozie, użyj następującego polecenia:

    ```bash
    nano job.xml
    ```

3. Po otwarciu edytora nano, użyj następujących XML jako zawartość pliku:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
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
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>YourName</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

   * Zastąp wszystkie wystąpienia zmiennej `wasb://mycontainer@mystorageaccount.blob.core.windows.net` z wcześniej odebrana magazynu domyślną wartość.

     > [!WARNING]
     > Jeśli ścieżka jest `wasb` ścieżki, należy użyć pełnej ścieżki. Nie skrócić go, aby dokładnie `wasb:///`.

   * Zastąp `YourName` z nazwy logowania dla klastra usługi HDInsight.
   * Zastąp `serverName`, `adminLogin`, i `adminPassword` wraz z informacjami dla Twojej bazy danych SQL.

     Większość informacji w tym pliku używany do wypełnienia wartości używane w plikach workflow.xml lub ooziewf.hql, takich jak `${nameNode}`.

     > [!NOTE]
     > `oozie.wf.application.path` Definiuje ona, gdzie można znaleźć pliku workflow.xml. Ten plik zawiera przepływu pracy, które zostało uruchomione przez tego zadania.

5. Aby zapisać plik, wybierz kombinację klawiszy Ctrl + X, wprowadź `Y`, a następnie wybierz **Enter**.

## <a name="submit-and-manage-the-job"></a>Prześlij i zadania zarządzania

Poniższe kroki polecenie Oozie służy do przesyłania i Zarządzaj przepływami pracy Oozie w klastrze. Polecenie Oozie umieszczeniu przyjazny interfejs [interfejsu API REST Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]
> Korzystając z polecenia Oozie, należy użyć nazwy FQDN węzła głównego usługi HDInsight. Ta nazwa FQDN jest dostępna tylko w klastrze, lub gdy klaster znajduje się w sieci wirtualnej platformy Azure, z innych komputerów w tej samej sieci.


1. Aby uzyskać adres URL usługi Oozie, użyj następującego polecenia:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    To polecenie zwróci informacje, na przykład następujący kod XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` Fragment jest adres URL do użycia z poleceniem Oozie.

2. Aby utworzyć zmienną środowiskową dla adresu URL, należy użyć następującego, dzięki czemu nie trzeba wprowadź każde polecenie:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

    Zastąp adres URL, który uzyskano wcześniej.
3. Aby przesłać zadanie, użyj następującego polecenia:

    ```bash
    oozie job -config job.xml -submit
    ```

    To polecenie ładuje informacje o zadaniu z `job.xml` i przesyła do Oozie, ale nie jest uruchamiane.

    Po zakończeniu działania polecenia, powinien on zwrócić Identyfikatora zadania, na przykład `0000005-150622124850154-oozie-oozi-W`. Ten identyfikator jest używany do zarządzania zadaniem.

4. Aby wyświetlić stan zadania, użyj następującego polecenia:

    ```bash
    oozie job -info <JOBID>
    ```

    > [!NOTE]
    > Zastąp `<JOBID>` o identyfikatorze zwracanej w poprzednim kroku.

    To polecenie zwróci informacje, na przykład następujący tekst:

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

    To zadanie ma stan `PREP`. Ten stan wskazuje, że zadania został utworzony, ale nie jest uruchomiona.

5. Aby uruchomić zadanie, użyj następującego polecenia:

    ```bash
    oozie job -start JOBID
    ```

    > [!NOTE]
    > Zastąp `<JOBID>` o identyfikatorze zwracane wcześniej.

    Jeśli po wykonaniu tego polecenia możesz sprawdzić stan, jest w stanie uruchomienia, a informacje są zwracane do działania w ramach zadania.

6. Po pomyślnym zakończeniu zadania, należy sprawdzić, czy dane został wygenerowany i wyeksportowane do tabeli bazy danych SQL za pomocą następującego polecenia:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest
    ```

    W `1>` monit, wprowadź następujące zapytanie:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Zwrócone informacje przypomina następujący tekst:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Aby uzyskać więcej informacji na temat polecenia Oozie, zobacz [narzędzia wiersza polecenia Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>Oozie interfejsu API REST

Przy użyciu interfejsu API REST Oozie można tworzyć własne narzędzia, które współpracują z Oozie. Poniżej przedstawiono informacje specyficzne dla usługi HDInsight dotyczące korzystania z interfejsu API REST Oozie:

* **Identyfikator URI**: można uzyskać dostępu do interfejsu API REST z poza klaster w `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Uwierzytelnianie**: w celu uwierzytelnienia za pomocą interfejsu API klastra HTTP konta (Administrator) i hasło. Na przykład:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Aby uzyskać więcej informacji na temat korzystania z interfejsu API REST Oozie, zobacz [API usług sieci Web Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Oozie interfejsu użytkownika sieci web

Interfejs użytkownika sieci web Oozie zawiera widok opartych na sieci web do stanu zadań Oozie w klastrze. Z interfejsu użytkownika sieci web można wyświetlić następujące informacje:

   * Stan zadania
   * Definicja zadania
   * Konfigurowanie
   * Wykres akcje w zadaniu
   * Dzienniki zadania

Można również wyświetlić szczegóły dla działania w ramach zadania.

Aby uzyskać dostęp do interfejsu użytkownika sieci web Oozie, wykonaj następujące czynności:

1. Tworzenie tunelu SSH w klastrze usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Użyj SSH Tunneling z usługą HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Po utworzeniu tunelu, Otwórz interfejs użytkownika sieci web Ambari w przeglądarce sieci web. Identyfikator URI dla witryny Ambari jest `https://CLUSTERNAME.azurehdinsight.net`. Zastąp `CLUSTERNAME` o nazwie klastra usługi HDInsight opartej na systemie Linux.

3. Wybierz z lewej strony **Oozie** > **szybkie linki** > **interfejsu użytkownika sieci Web Oozie**.

    ![Obraz menu](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. Oozie domyślnie interfejs użytkownika sieci web Wyświetl uruchomione zadania przepływu pracy. Aby wyświetlić wszystkie zadania przepływu pracy, wybierz **wszystkie zadania**.

    ![Wszystkie zadania wyświetlane](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Aby wyświetlić więcej informacji o zadaniu, wybierz zadanie.

    ![Informacje o stanowisku](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. Z **informacji o zadaniu** kartę, można wyświetlić informacje o zadaniu podstawowe i poszczególnych działań w ramach zadania. Karty u góry służy do wyświetlania **definicji zadania**, **zadania konfiguracji**, dostępu **dziennik zadań**, lub wyświetlić ukierunkowanego wykresu acyklicznego (DAG) zadania w obszarze **Zadania DAG**.

   * **Dziennik zadań**: Wybierz **Pobierz dzienniki** przycisk, aby pobrać wszystkie dzienniki dla zadania, lub użyj **filtr wyszukiwania wprowadź** pole ma zostać przefiltrowane dzienniki.

       ![Dziennik zadań](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **Zadanie DAG**: DAG jest graficznego przeglądu ścieżek danych przez przepływ pracy.

       ![Zadanie DAG](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Po wybraniu jednej akcji z **informacji o zadaniu** karcie stwarza informacji dla akcji. Na przykład wybierz **RunSqoopExport** akcji.

    ![Informacje o akcji](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Aby wyświetlić szczegóły dla tej akcji, takich jak łącze do **adres URL konsoli**. To łącze umożliwia wyświetlanie informacji o śledzeniu zadań dla zadania.

## <a name="schedule-jobs"></a>Planowanie zadań

Koordynator służy do określania rozpoczęcia, punktu końcowego i częstotliwości występowania zadania. Aby zdefiniować harmonogram dla przepływu pracy, wykonaj następujące kroki:

1. Użyj następującego polecenia, aby utworzyć plik o nazwie **coordinator.xml**:

    ```bash
    nano coordinator.xml
    ```

    Użyj następujących XML jako zawartość pliku:

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
    > `${...}` Zmienne są zastępowane przez wartości w definicji zadania w czasie wykonywania. Dostępne są następujące zmienne:
    >
    > * `${coordFrequency}`: Czas między uruchomione wystąpienia zadania.
    > * `${coordStart}`: Czas rozpoczęcia zadanie.
    > * `${coordEnd}`Godzina zakończenia zadania.
    > * `${coordTimezone}`: Koordynator zadań znajdują się w stałej strefy czasowej nie czasu letniego, zazwyczaj reprezentowany przy użyciu czasu UTC. Ta strefa czasowa jest określana jako *Oozie przetwarzania w strefie czasowej.*
    > * `${wfPath}`: Ścieżka do workflow.xml.

2. Aby zapisać plik, wybierz kombinację klawiszy Ctrl + X, wprowadź `Y`, a następnie wybierz **Enter**.

3. Aby skopiować plik do katalogu roboczego dla tego zadania, użyj następującego polecenia:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Aby zmodyfikować `job.xml` plików, użyj następującego polecenia:

    ```
    nano job.xml
    ```

    Wprowadź następujące zmiany:

   * Aby nakazać Oozie, aby uruchomić plik koordynatora zamiast przepływu pracy, zmień `<name>oozie.wf.application.path</name>` do `<name>oozie.coord.application.path</name>`.

   * Aby ustawić `workflowPath` zmiennej używanej przez koordynatora, Dodaj następujący kod XML:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Zastąp `wasb://mycontainer@mystorageaccount.blob.core.windows` tekst na wartość używana w innych pozycji w pliku job.xml.

   * Aby zdefiniować początek, koniec i częstotliwość koordynatora, Dodaj następujący kod XML:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2017-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2017-05-12T12:00Z</value>
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

       Te wartości są ustawiane 12:00 PM 10 maja 2017 czas rozpoczęcia i czas zakończenia 12 maja 2017 r. Interwał wykonywania tego zadania jest ustawiona na codziennie. Jest częstotliwość w minutach, więc 24 godziny x 60 minut = 1440 minut. Na koniec strefę czasową ustawiono na czas UTC.

5. Aby zapisać plik, wybierz kombinację klawiszy Ctrl + X, wprowadź `Y`, a następnie wybierz **Enter**.

6. Aby uruchomić to zadanie, użyj następującego polecenia:

    ```
    oozie job -config job.xml -run
    ```

    To polecenie przesyła i uruchamia zadanie.

7. Jeśli przejdziesz do Oozie interfejs użytkownika sieci web i wybierz **koordynator zadań** kartę, zobacz informacje, takie jak na poniższej ilustracji:

    ![Koordynator kartę zadania](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    **Materialization dalej** wpis zawiera przy następnym uruchomieniu zadania.

8. Jak wcześniej zadania przepływu pracy Jeśli wybierz pozycję zadania w interfejsie użytkownika sieci web wyświetla informacje o zadaniu:

    ![Informacji o zadaniu koordynatora](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    > [!NOTE]
    > Ten obraz zawiera tylko pomyślnie uruchamia zadanie, nie poszczególnych działań w ramach zaplanowanego przepływu pracy. Aby wyświetlić poszczególne działania, wybierz jedną z **akcji** wpisów.

    ![Informacje o akcji](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Przy użyciu interfejsu użytkownika Oozie Oozie dzienniki można wyświetlać. Interfejs użytkownika Oozie zawiera również linki do dzienników JobTracker zadań MapReduce, które zostały uruchomione przez przepływ pracy. Rozwiązywanie problemów z wzorcem powinny być:

   1. Wyświetl zadanie w Oozie interfejsu użytkownika sieci web.

   2. Jeśli istnieje komunikat o błędzie lub awaria dla określonej akcji, wybierz akcję, aby sprawdzić, czy **komunikat o błędzie** pole zawiera więcej informacji o błędzie.

   3. Jeśli jest dostępna, należy użyć adresu URL z akcji można zobaczyć więcej szczegółów, takich jak dzienniki JobTracker, dla akcji.

Poniżej przedstawiono określonych błędów, które mogą wystąpić i sposobu ich rozwiązania.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Nie można zainicjować klastra

**Objawy**: stan zadania zmieni się na **zawieszone**. Szczegóły zadania Pokaż `RunHiveScript` statusu **START_MANUAL**. Wybranie akcji wyświetli następujący komunikat o błędzie:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Przyczyna**: adresy magazynu obiektów Blob platformy Azure, używane w **job.xml** plik nie zawiera kontenera magazynu lub nazwy konta magazynu. Format adresu magazynu obiektów Blob musi być `wasb://containername@storageaccountname.blob.core.windows.net`.

**Rozdzielczość**: zmiany adresów magazynu obiektów Blob używane przez zadania.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002: Oozie nie może spersonifikować &lt;użytkownika >

**Objawy**: stan zadania zmieni się na **zawieszone**. Szczegóły zadania Pokaż `RunHiveScript` statusu **START_MANUAL**. W przypadku wybrania działania zawiera następujący komunikat o błędzie:

    JA002: User: oozie is not allowed to impersonate <USER>

**Przyczyna**: nie zezwalaj na bieżące ustawienia uprawnień Oozie personifikować określonego konta użytkownika.

**Rozdzielczość**: Oozie mogą personifikować użytkowników w **użytkowników** grupy. Użyj `groups USERNAME` Aby wyświetlić grupy, których należy konto użytkownika. Jeśli użytkownik nie jest członkiem **użytkowników** grupy, użyj następującego polecenia, aby dodać użytkownika do grupy:

    sudo adduser USERNAME users

> [!NOTE]
> Może upłynąć kilka minut, zanim HDInsight rozpoznaje, czy użytkownik został dodany do grupy.

### <a name="launcher-error-sqoop"></a>Uruchamianie błąd (Sqoop)

**Objawy**: stan zadania zmieni się na **KILLED**. Szczegóły zadania Pokaż `RunSqoopExport` statusu **błąd**. W przypadku wybrania działania zawiera następujący komunikat o błędzie:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Przyczyna**: Sqoop nie może załadować sterownika bazy danych wymaganych do dostępu do bazy danych.

**Rozdzielczość**: korzystając z zadania Oozie Sqoop, musi zawierać sterownik bazy danych z innych zasobów, takich jak workflow.xml, używa zadania. Ponadto odwołanie zawierającą sterownik bazy danych z archiwum `<sqoop>...</sqoop>` sekcji workflow.xml.

Na przykład dla zadania w tym dokumencie, możesz użyć następujące czynności:

1. Kopiuj `sqljdbc4.1.jar` pliku **/samouczki/useoozie** katalogu:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar
    ```

2. Modyfikowanie `workflow.xml` można dodać następujących XML w nowym wierszu powyżej `</sqoop>`:

    ```xml
    <archive>sqljdbc41.jar</archive>
    ```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób definiowania Oozie przepływu pracy i sposób uruchamiania zadania Oozie. Aby dowiedzieć się więcej na temat sposobu pracy z usługą HDInsight, zobacz następujące artykuły:

* [Korzystanie z usługą HDInsight koordynatora Oozie oparte na czasie][hdinsight-oozie-coordinator-time]
* [Przekazywanie danych dotyczących zadań Hadoop w usłudze HDInsight][hdinsight-upload-data]
* [Używanie Sqoop z platformą Hadoop w usłudze HDInsight][hdinsight-use-sqoop]
* [Korzystanie z programu Hive z usługą Hadoop w usłudze HDInsight][hdinsight-use-hive]
* [Korzystanie z języka Pig z usługą Hadoop w usłudze HDInsight][hdinsight-use-pig]
* [Tworzenie programów Java MapReduce dla usługi HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
