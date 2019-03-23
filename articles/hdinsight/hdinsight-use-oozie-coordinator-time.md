---
title: Użyj opartego na czasie koordynatora programu Oozie usługi Hadoop w HDInsight
description: Użyj opartego na czasie koordynatora programu Oozie usługi Hadoop w HDInsight, Usługa danych big data. Dowiedz się, jak zdefiniować przepływy pracy programu Oozie i koordynatorów oraz przesyłania zadań.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/04/2017
ROBOTS: NOINDEX
ms.openlocfilehash: bf1c7360157b3b4af467cf53634056d70b91ebd2
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361340"
---
# <a name="use-time-based-apache-oozie-coordinator-with-apache-hadoop-in-hdinsight-to-define-workflows-and-coordinate-jobs"></a>Za pomocą opartego na czasie koordynatora programu Apache Oozie Apache Hadoop w HDInsight do definiowania przepływów pracy i koordynowania zadań
W tym artykule dowiesz się, jak zdefiniować przepływy pracy i koordynatorów oraz sposób wyzwalania zadania koordynatora, w oparciu o czas. Warto poświęcić na przejście [Użyj Apache Oozie z HDInsight] [ hdinsight-use-oozie] przed przeczytaniem tego artykułu. Oprócz programu Oozie można także zaplanować zadania przy użyciu usługi Azure Data Factory. Aby poznać usługi Azure Data Factory, zobacz [Use Apache Pig i Apache Hive z usługą Data Factory](../data-factory/transform-data.md).

> [!NOTE]  
> W tym artykule wymaga klastra HDInsight z systemem Windows. Aby uzyskać informacje na temat korzystania z technologii Oozie, w tym zadań na podstawie czasu, w klastrze opartych na systemie Linux zobacz [Użyj Oozie z usługą Hadoop, aby zdefiniować i uruchomić przepływ pracy na HDInsight opartych na systemie Linux](hdinsight-use-oozie-linux-mac.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-is-oozie"></a>Co to jest Oozie
Apache Oozie to system koordynacji/przepływ pracy, który zarządza zadaniami na platformie Hadoop. Jest zintegrowany ze stosem platformy Hadoop i Apache Hadoop MapReduce, Apache Pig, Apache Hive i Apache Sqoop go obsługuje zadania platformy Hadoop. Może również służyć do planowania zadań, które są specyficzne dla systemu, np. programów Java lub skryptów powłoki.

Na poniższej ilustracji przedstawiono przepływ pracy, który będzie implementowany:

![Diagram przepływu pracy][img-workflow-diagram]

Przepływ pracy zawiera dwie akcje:

1. Działanie programu Hive jest uruchamiany skrypt HiveQL, zliczania wystąpień każdego typu poziomu dziennika w Apache pliku dziennika log4j i. Każdego dziennika log4j składa się z wiersz pola, który zawiera pola [poziom dziennika], aby wyświetlić typ i ważność, na przykład:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    Dane wyjściowe skryptu programu Hive jest podobny do:

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Aby uzyskać więcej informacji na temat programu Hive, zobacz [używanie programu Apache Hive z HDInsight][hdinsight-use-hive].
2. Akcja Sqoop eksportuje dane wyjściowe akcji HiveQL do tabeli w bazie danych Azure SQL. Aby uzyskać więcej informacji na temat narzędzia Sqoop zobacz [Użyj Apache Sqoop z HDInsight][hdinsight-use-sqoop].

> [!NOTE]  
> Obsługiwane wersje programu Oozie w klastrach HDInsight, zobacz [nowości w wersjach klastra, dostarczone przez HDInsight?] [hdinsight-versions].
>
>

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Stacja robocza z programem Azure PowerShell**.

    > [!IMPORTANT]  
    > Obsługa środowiska Azure PowerShell do celów zarządzania zasobami usługi HDInsight przy użyciu usługi Azure Service Manager jest **przestarzała** i zostanie usunięta z dniem 1 stycznia 2017 r. W czynnościach opisanych w niniejszym dokumencie są używane nowe polecenia cmdlet usługi HDInsight współpracujące z usługą Azure Resource Manager.
    >
    > Wykonaj kroki opisane w temacie [Instalowanie i konfigurowanie środowiska Azure PowerShell](/powershell/azureps-cmdlets-docs), aby zainstalować najnowszą wersję środowiska Azure PowerShell. Jeśli masz skrypty wymagające modyfikacji w celu użycia nowych poleceń cmdlet współpracujących z usługą Azure Resource Manager, zobacz temat [Migrowanie do narzędzi programistycznych opartych na usłudze Azure Resource Manager w celu obsługi klastrów usługi HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md), aby uzyskać więcej informacji.

* **Klaster usługi HDInsight**. Aby uzyskać informacje dotyczące tworzenia klastra usługi HDInsight, zobacz [klastrów HDInsight tworzenie][hdinsight-provision], lub [Rozpoczynanie pracy z usługą HDInsight][hdinsight-get-started]. Następujące dane, aby wykonać kroki tego samouczka jest potrzebne:

    |Właściwości klastra|Nazwa zmiennej środowiska Windows PowerShell|Wartość|Opis|
    |---|---|---|---|
    |Nazwa klastra HDInsight|$clusterName||Klaster HDInsight, na którym będzie działać w tym samouczku.|
    |Nazwa użytkownika klastra HDInsight|$clusterUsername||Nazwa użytkownika klastra HDInsight. |
    |Hasło użytkownika klastra HDInsight |$clusterPassword||Hasło użytkownika klastra HDInsight.|
    |Nazwa konta magazynu platformy Azure|$storageAccountName||Konto usługi Azure Storage dostępnych z klastrem HDInsight. Na potrzeby tego samouczka użyj domyślnego konta magazynu, który określiłeś w trakcie procesu aprowizacji klastra.|
    |Nazwa kontenera obiektów Blob Azure|$containerName||W tym przykładzie należy użyć kontenera Azure Blob storage, która jest używana do domyślnego systemu plików klastra HDInsight. Domyślnie ma taką samą nazwę jak klastra HDInsight.|


* **Azure SQL database**. Należy skonfigurować reguły zapory dla serwera bazy danych SQL zezwolić na dostęp ze swojej stacji roboczej. Aby uzyskać instrukcje na temat tworzenia usługi Azure SQL database i konfigurowanie zapory, zobacz [rozpoczęcie korzystania z bazy danych Azure SQL][sqldatabase-get-started]. Ten artykuł zawiera skrypt programu Windows PowerShell do tworzenia tabeli bazy danych Azure SQL, potrzebne w ramach tego samouczka.

    |Właściwości bazy danych SQL|Nazwa zmiennej środowiska Windows PowerShell|Wartość|Opis|
    |---|---|---|---|
    |Nazwa serwera bazy danych SQL|$sqlDatabaseServer||Serwer bazy danych SQL, do którego Sqoop zostaną wyeksportowane dane. |
    |Nazwa logowania do bazy danych SQL|$sqlDatabaseLogin||Nazwa logowania SQL Database.|
    |Hasło logowania do bazy danych SQL|$sqlDatabaseLoginPassword||Hasło logowania SQL Database.|
    |Nazwa bazy danych SQL|$sqlDatabaseName||Baza danych Azure SQL, do którego Sqoop zostaną wyeksportowane dane. |

  > [!NOTE]   
  > Domyślnie usługi Azure SQL database zezwala na połączenia z usług platformy Azure, takich jak Azure HDInsight. Wyłączenie tego ustawienia zapory, należy włączyć je w witrynie Azure portal. Aby uzyskać instrukcje dotyczące tworzenia bazy danych SQL i konfigurowania reguł zapory, zobacz [tworzenie i Konfigurowanie bazy danych SQL][sqldatabase-get-started].

> [!NOTE]  
> Wypełnianie wartości w tabelach. Będzie ona przydatne w przypadku przechodzenia przez w tym samouczku.

## <a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Definiowanie przepływu pracy programu Oozie i powiązane skrypt HiveQL
Definicje przepływów pracy programu Oozie są zapisywane w hPDL (język definicji procesu XML). Domyślna nazwa pliku przepływu pracy jest *workflow.xml*.  Będzie zapisać lokalnie plik przepływu pracy, a następnie wdrożyć ją w klastrze HDInsight przy użyciu programu Azure PowerShell w dalszej części tego samouczka.

Działanie programu Hive w przepływie pracy wywołuje plik skryptu HiveQL. Ten plik skryptu zawiera trzy instrukcje HiveQL:

1. **Instrukcja DROP TABLE** usuwa tabelę programu Hive log4j, jeśli taki istnieje.
2. **Wykonywanie instrukcji CREATE TABLE** tworzy log4j tabeli programu Hive zewnętrznego, który wskazuje na lokalizację pliku dziennika log4j;
3. **Lokalizacja pliku dziennika log4j**. Ogranicznik pola jest ",". Ogranicznik wiersza domyślną jest "\n". Zewnętrzne tabeli programu Hive jest używana w celu uniknięcia usuwany z oryginalnej lokalizacji pliku danych, w przypadku, gdy chcesz uruchomić przepływ pracy Oozie wiele razy.
4. **Instrukcja INSERT zastąpić** liczby wystąpień każdego typu poziomu dziennika z tabeli programu Hive log4j i zapisuje dane wyjściowe do lokalizacji magazynu obiektów Blob platformy Azure.

> [!NOTE]  
> Istnieje znany problem ze ścieżką Hive. Napotkasz ten problem podczas przesyłania zadania usługi Oozie. Instrukcje dotyczące rozwiązywania problemu można znaleźć w witrynie TechNet Wiki: [Błąd HDInsight Hive: Nie można zmienić nazwy][technetwiki-hive-error].

**Aby określić plik skryptu HiveQL, który ma zostać wywołana przez przepływ pracy**

1. Utwórz plik tekstowy o następującej zawartości:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Istnieją trzy zmienne używane w skrypcie:

   * ${hiveTableName}
   * ${hiveDataFolder}
   * ${hiveOutputFolder}

     Plik definicji przepływu pracy (workflow.xml w ramach tego samouczka) przekazuje te wartości do tego skryptu HiveQL w czasie wykonywania.
2. Zapisz plik jako **C:\Tutorials\UseOozie\useooziewf.hql** przy użyciu kodowania ANSI (ASCII). (Użyj Notatnik, jeśli edytor tekstu nie zapewniają tej opcji). Ten plik skryptu zostanie wdrożony do klastra HDInsight w dalszej części tego samouczka.

**Aby zdefiniować przepływ pracy**

1. Utwórz plik tekstowy o następującej zawartości:

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
                <param>hiveOutputFolder=${hiveOutputFolder}</param>
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
            <arg>${hiveOutputFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\001"</arg>
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

    Istnieją dwie akcje zdefiniowane w przepływie pracy. Akcja do rozpoczęcia jest *RunHiveScript*. Jeśli akcja działa *OK*, jest następnej akcji *RunSqoopExport*.

    RunHiveScript ma kilka zmiennych. Po przesłaniu zadania Oozie ze swojej stacji roboczej za pomocą programu Azure PowerShell będzie przekazać wartości.

    Zmienne przepływu pracy

    |Zmienne przepływu pracy|Opis|
    |---|---|
    |${jobTracker}|Określ adres URL śledzenia zadań usługi Hadoop. Użyj **jobtrackerhost:9010** na HDInsight klastra w wersji 3.0 i w wersji 2.0.|
    |${nameNode}|Podaj adres URL węzła nazwa usługi Hadoop. Użyj wasb system plików domyślne: / / adresu, na przykład *wasb: / /&lt;containerName&gt;\@&lt;storageAccountName&gt;. blob.core.windows.net*.|
    |${queueName}|Określa nazwę kolejki, która zadania zostaną przesłane do. Użyj **domyślne**.|

    Zmienne akcji gałęzi

    |Hive zmiennej akcji|Opis|
    |----|----|
    |${hiveDataFolder}|Katalog źródłowy dla polecenia Hive Create Table.|
    |${hiveOutputFolder}|Folder wyjściowy dla instrukcji INSERT zastąpić.|
    |${hiveTableName}|Nazwa tabeli programu Hive, która odwołuje się do plików danych log4j.|

    Zmienne akcji narzędzia Sqoop

    |Sqoop zmiennej akcji|Opis|
    |---|---|
    |${sqlDatabaseConnectionString}|Parametry połączenia bazy danych SQL.|
    |${sqlDatabaseTableName}|Tabela bazy danych Azure SQL, do której zostaną wyeksportowane dane.|
    |${hiveOutputFolder}|Folder wyjściowy dla instrukcji Hive Wstaw zastąpić. To ten sam folder eksportu Sqoop (export-dir).|

    Aby uzyskać więcej informacji o przepływie pracy programu Oozie i przy użyciu akcji przepływu pracy, zobacz [dokumentację Apache Oozie 4.0] [ apache-oozie-400] (dla wersji klastra HDInsight 3.0) lub [Apache Oozie 3.3.2 dokumentacji ] [ apache-oozie-332] (dla wersji klastra HDInsight 2.1).

1. Zapisz plik jako **C:\Tutorials\UseOozie\workflow.xml** przy użyciu kodowania ANSI (ASCII). (Użyj Notatnik, jeśli edytor tekstu nie zapewniają tej opcji).

**Aby zdefiniować koordynatora**

1. Utwórz plik tekstowy o następującej zawartości:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
            <workflow>
                <app-path>${wfPath}</app-path>
            </workflow>
        </action>
    </coordinator-app>
    ```

    Istnieje pięć zmiennych używanych w pliku definicji:

   | Zmienna | Opis |
   | --- | --- |
   | ${coordFrequency} |Czas wstrzymania zadania. Częstotliwość zawsze jest wyrażona w ciągu kilku minut. |
   | ${coordStart} |Godzina rozpoczęcia zadania. |
   | ${coordEnd} |Godzina zakończenia zadania. |
   | ${coordTimezone} |Oozie przetwarza koordynator zadań w stałej strefy czasowej z nie czasu (zwykle reprezentowany przez przy użyciu czasu UTC). Ta strefa czasowa jest określane jako "przetwarzania Oozie strefę czasową." |
   | ${wfPath} |Ścieżka workflow.xml.  Jeśli nazwa pliku przepływu pracy nie jest domyślna nazwa pliku (workflow.xml), należy określić go. |
2. Zapisz plik jako **C:\Tutorials\UseOozie\coordinator.xml** przy użyciu kodowania ANSI (ASCII). (Użyj Notatnik, jeśli edytor tekstu nie zapewniają tej opcji).

## <a name="deploy-the-oozie-project-and-prepare-the-tutorial"></a>Wdrażanie projektu programu Oozie i przygotowania samouczka
Uruchomi skrypt programu Azure PowerShell, wykonaj następujące czynności:

* Skopiuj skrypt HiveQL (useoozie.hql) do usługi Azure Blob storage wasb:///tutorials/useoozie/useoozie.hql.
* Skopiuj workflow.xml wasb:///tutorials/useoozie/workflow.xml.
* Skopiuj coordinator.xml wasb:///tutorials/useoozie/coordinator.xml.
* Skopiuj plik danych (/ example/data/sample.log) do wasb:///tutorials/useoozie/data/sample.log.
* Utwórz tabelę bazy danych Azure SQL do przechowywania danych eksportu Sqoop. Nazwa tabeli jest *log4jLogCount*.

**Omówienie magazynu HDInsight**

HDInsight używa usługi Azure Blob storage do przechowywania danych. wasb: / / to implementacja firmy Microsoft Rozproszony system plików Hadoop (HDFS) w usłudze Azure Blob storage. Aby uzyskać więcej informacji, zobacz [użycia usługi Azure Blob storage za pomocą HDInsight][hdinsight-storage].

Podczas aprowizowania klastra usługi HDInsight konto usługi Azure Blob storage i z tego konta określonego kontenera jest wyznaczony jako domyślny system plików, podobnie jak w systemie plików HDFS. Oprócz tego konta magazynu można dodać dodatkowe konta magazynu z tej samej subskrypcji platformy Azure lub różnych subskrypcji platformy Azure podczas procesu inicjowania obsługi administracyjnej. Aby uzyskać instrukcje dotyczące dodawania dodatkowych kont magazynu, zobacz [Provision HDInsight clusters][hdinsight-provision]. Aby uprościć skrypt programu Azure PowerShell, używane w tym samouczku, są wszystkie pliki przechowywane w domyślnym kontenerze system plików znajdujący się w */samouczki/useoozie*. Domyślnie ten kontener ma taką samą nazwę jak nazwa klastra HDInsight.
Składnia jest następująca:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [!NOTE]  
> Tylko *wasb: / /* składnia jest obsługiwana w wersji klastra HDInsight 3.0 lub nowszej. Starszy *asv: / /* składnia jest obsługiwana w HDInsight 2.1 i 1.6 klastrów, ale nie jest obsługiwana w klastrach HDInsight 3.0.
>
> Wasb: / / ścieżka jest ścieżką wirtualną. Aby uzyskać więcej informacji, zobacz [użycia usługi Azure Blob storage za pomocą HDInsight][hdinsight-storage].

Plik, który znajduje się w domyślnym kontenerze system plików jest możliwy z HDInsight przy użyciu dowolnej z następujących identyfikatorów URI (używam workflow.xml jako przykład):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Jeśli chcesz uzyskać dostęp do pliku bezpośrednio z konta magazynu jest nazwa obiektu blob dla pliku:

    tutorials/useoozie/workflow.xml

**Omówienie tabel programu Hive wewnętrznych i zewnętrznych**

Istnieje kilka rzeczy, które musisz wiedzieć o wewnętrznych i zewnętrznych tabel programu Hive:

* Polecenie CREATE TABLE umożliwia utworzenie tabeli wewnętrznej, znany także jako tabelę zarządzanych. Plik danych musi znajdować się w domyślnym kontenerze.
* Polecenia CREATE TABLE przenosi plik danych do/hive/warehouse/<TableName> folderu w domyślnym kontenerze.
* Polecenie CREATE EXTERNAL TABLE umożliwia utworzenie tabeli zewnętrznej. Plik danych mogą znajdować się poza kontener domyślny.
* Polecenie CREATE EXTERNAL TABLE nie powoduje przeniesienia pliku danych.
* Polecenie CREATE EXTERNAL TABLE nie zezwala na wszystkie podfoldery w folderze, który jest określony w klauzuli lokalizacji. Jest to powód, dlaczego samouczka sprawia, że kopia pliku sample.log.

Aby uzyskać więcej informacji, zobacz [HDInsight: Apache Hive wprowadzenie tabel wewnętrznych i zewnętrznych][cindygross-hive-tables].

**Do przygotowania samouczka**

1. Otwórz program Windows PowerShell ISE (ekran początkowy systemu Windows 8, należy wpisać **PowerShell_ISE**, a następnie kliknij przycisk **środowiska Windows PowerShell ISE**. Aby uzyskać więcej informacji, zobacz [programu Windows PowerShell Start systemu Windows 8 i Windows][powershell-start]).
2. W dolnym okienku uruchom następujące polecenie, aby nawiązać połączenie z subskrypcją platformy Azure:

    ```powershell
    Add-AzureAccount
    ```

    Użytkownik jest monitowany o podanie poświadczeń konta platformy Azure. Ta metoda dodawania połączenia subskrypcji upłynie limit czasu, a po 12 godzinach, trzeba będzie ponownie uruchom polecenie cmdlet.

   > [!NOTE]  
   > Jeśli masz wiele subskrypcji platformy Azure i Domyślna subskrypcja nie jest to chcesz użyć, użyj <strong>Select-AzureSubscription</strong> polecenia cmdlet, aby wybrać subskrypcję.

3. Skopiuj poniższy skrypt w okienku skryptów, a następnie ustaw zmienne pierwszych sześciu:

    ```powershell
    # WASB variables
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<BlobStorageContainerName>"

    # SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    # Oozie files for the tutorial
    $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
    $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
    $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

    # WASB folder for storing the Oozie tutorial files.
    $destFolder = "tutorials/useoozie"  # Do NOT use the long path here
    ```

    Opis więcej zmiennych, zobacz [wymagania wstępne](#prerequisites) sekcji, w tym samouczku.

4. Dołącz następujący ciąg do skryptu w okienku skryptu:

    ```powershell
    # Create a storage context object
    $storageaccountkey = get-AzStoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    function uploadOozieFiles()
    {
        Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
        Set-AzStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
        Set-AzStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
        Set-AzStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
    }

    function prepareHiveDataFile()
    {
        Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
        Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
    }

    function prepareSQLDatabase()
    {
        # SQL query string for creating log4jLogsCount table
        $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [Level] [nvarchar](10) NOT NULL,
                [Total] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
            (
            [Level] ASC
            )
            )"

        #Create the log4jLogsCount table
        Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $cmdCreateLog4jCountTable
        $cmd.executenonquery()

        $conn.close()
    }

    # upload workflow.xml, coordinator.xml, and ooziewf.hql
    uploadOozieFiles;

    # make a copy of example/data/sample.log to example/data/log4j/sample.log
    prepareHiveDataFile;

    # create log4jlogsCount table on SQL database
    prepareSQLDatabase;
    ```

5. Kliknij przycisk **uruchamianie skryptu** lub naciśnij **F5** do uruchomienia skryptu. Dane wyjściowe będą podobne do:

    ![Samouczek przygotowywania danych wyjściowych][img-preparation-output]

## <a name="run-the-oozie-project"></a>Uruchom projekt programu Oozie
Program Azure PowerShell aktualnie nie zapewnia żadnych poleceń cmdlet do definiowania zadań Oozie. Możesz użyć **Invoke RestMethod** polecenia cmdlet do wywołania usługi sieci web programu Oozie. API usług sieci web programu Oozie jest JSON interfejsu API REST protokołu HTTP. Aby uzyskać więcej informacji o usługach sieci web programu Oozie interfejsu API, zobacz [dokumentację Apache Oozie 4.0] [ apache-oozie-400] (dla wersji klastra HDInsight 3.0) lub [dokumentację Apache Oozie 3.3.2] [ apache-oozie-332] (dla wersji klastra HDInsight 2.1).

**Aby przesłać zadanie usługi Oozie**

1. Otwórz program Windows PowerShell ISE (ekran początkowy systemu Windows 8, należy wpisać **PowerShell_ISE**, a następnie kliknij przycisk **środowiska Windows PowerShell ISE**. Aby uzyskać więcej informacji, zobacz [programu Windows PowerShell Start systemu Windows 8 i Windows][powershell-start]).
2. Skopiuj poniższy skrypt w okienku skryptów, a następnie ustaw zmienne najpierw czternastu (jednak pominąć **$storageUri**).

    ```powershell
    #HDInsight cluster variables
    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterUserPassword>"

    #Azure Blob storage (WASB) variables
    $storageAccountName = "<StorageAccountName>"
    $storageContainerName = "<BlobContainerName>"
    $storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"

    #Azure SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"

    #Oozie WF/coordinator variables
    $coordStart = "2014-03-21T13:45Z"
    $coordEnd = "2014-03-21T13:45Z"
    $coordFrequency = "1440"    # in minutes, 24h x 60m = 1440m
    $coordTimezone = "UTC"    #UTC/GMT

    $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
    $waitTimeBetweenOozieJobStatusCheck=10

    #Hive action variables
    $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
    $hiveTableName = "log4jlogs"
    $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
    $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

    #Sqoop action variables
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"  
    $sqlDatabaseTableName = "log4jLogsCount"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
    ```

    Opis więcej zmiennych, zobacz [wymagania wstępne](#prerequisites) sekcji, w tym samouczku.

    $coordstart i $coordend są przepływu pracy, początkowy i końcowy czasu. Aby sprawdzić czas UTC/GMT, wyszukaj "czas utc" w witrynie bing.com. $CoordFrequency jest częstotliwość w minutach, aby uruchomić przepływ pracy.
3. Dołącz następujący ciąg do skryptu. Ta część definiuje ładunek Oozie:

    ```powershell
    #OoziePayload used for Oozie web service submission
    $OoziePayload =  @"
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
            <name>nameNode</name>
            <value>$storageUrI</value>
        </property>

        <property>
            <name>jobTracker</name>
            <value>jobtrackerhost:9010</value>
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
            <name>oozie.coord.application.path</name>
            <value>$oozieWFPath</value>
        </property>

        <property>
            <name>wfPath</name>
            <value>$oozieWFPath</value>
        </property>

        <property>
            <name>coordStart</name>
            <value>$coordStart</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>$coordEnd</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>$coordFrequency</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>$coordTimezone</value>
        </property>

        <property>
            <name>hiveScript</name>
            <value>$hiveScript</value>
        </property>

        <property>
            <name>hiveTableName</name>
            <value>$hiveTableName</value>
        </property>

        <property>
            <name>hiveDataFolder</name>
            <value>$hiveDataFolder</value>
        </property>

        <property>
            <name>hiveOutputFolder</name>
            <value>$hiveOutputFolder</value>
        </property>

        <property>
            <name>sqlDatabaseConnectionString</name>
            <value>&quot;$sqlDatabaseConnectionString&quot;</value>
        </property>

        <property>
            <name>sqlDatabaseTableName</name>
            <value>$SQLDatabaseTableName</value>
        </property>

        <property>
            <name>user.name</name>
            <value>admin</value>
        </property>

    </configuration>
    "@
    ```

   > [!NOTE]  
   > Główna różnica w porównaniu do plik ładunku przesyłania przepływu pracy jest zmienna **oozie.coord.application.path**. Po przesłaniu zadania przepływu pracy, możesz użyć **oozie.wf.application.path** zamiast tego.

4. Dołącz następujący ciąg do skryptu. Ta część umożliwia sprawdzenie stanu usługi sieci web programu Oozie:

    ```powershell
    function checkOozieServerStatus()
    {
        Write-Host "Checking Oozie server status..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieServerStatus = $jsonResponse[0].("systemMode")
        Write-Host "Oozie server status is $oozieServerStatus..."

        if($oozieServerStatus -notmatch "NORMAL")
        {
            Write-Host "Oozie server status is $oozieServerStatus...cannot submit Oozie jobs. Check the server status and re-run the job."
        }
    }
    ```

5. Dołącz następujący ciąg do skryptu. Ta część tworzy zadanie usługi Oozie:

    ```powershell
    function createOozieJob()
    {
        # create Oozie job
        Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
        Write-Host "`n--------`n$OoziePayload`n--------"
        $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieJobId = $jsonResponse[0].("id")
        Write-Host "Oozie job id is $oozieJobId..."

        return $oozieJobId
    }
    ```

   > [!NOTE]  
   > Po przesłaniu zadania przepływu pracy należy innej usługi sieci web wywołać, aby uruchomić zadanie, po utworzeniu zadania. W tym przypadku zadania Koordynator jest wywoływane. Zadanie zostanie uruchomione automatycznie.

6. Dołącz następujący ciąg do skryptu. Ta część umożliwia sprawdzenie stanu zadania Oozie:

    ```powershell
    function checkOozieJobStatus($oozieJobId)
    {
        # get job status
        Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

        Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
        $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")

        while($JobStatus -notmatch "SUCCEEDED|KILLED")
        {
            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")
        }

        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
        if($JobStatus -notmatch "SUCCEEDED")
        {
            Write-Host "Check logs at http://headnode0:9014/cluster for detais."
        }
    }
    ```

7. (Opcjonalnie) Dołącz następujący ciąg do skryptu.

    ```powershell
    function listOozieJobs()
    {
        Write-Host "Listing Oozie jobs..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds

        write-host "Job ID                                   App Name        Status      Started                         Ended"
        write-host "----------------------------------------------------------------------------------------------------------------------------------"
        foreach($job in $response.workflows)
        {
            Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
        }
    }

    function ShowOozieJobLog($oozieJobId)
    {
        Write-Host "Showing Oozie job info..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds
        write-host $response
    }

    function killOozieJob($oozieJobId)
    {
        Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
        $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
        $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
    }
    ```

8. Dołącz następujący ciąg do skryptu:

    ```powershell
    checkOozieServerStatus
    # listOozieJobs
    $oozieJobId = createOozieJob($oozieJobId)
    checkOozieJobStatus($oozieJobId)
    # ShowOozieJobLog($oozieJobId)
    # killOozieJob($oozieJobId)
    ```

Usuń znaki #, jeśli chcesz uruchamiać dodatkowe funkcje.

1. Jeśli klaster HDInsight jest w wersji 2.1, należy zastąpić "https://$clusterName.azurehdinsight.net:443/oozie/v2/" z "https://$clusterName.azurehdinsight.net:443/oozie/v1/". Klastra HDInsight w wersji 2.1 nie nie obsługuje wersji 2, usług sieci web.
1. Kliknij przycisk **uruchamianie skryptu** lub naciśnij **F5** do uruchomienia skryptu. Dane wyjściowe będą podobne do:

    ![Samouczek uruchamiania danych wyjściowych przepływu pracy][img-runworkflow-output]
1. Połącz w bazie danych SQL, aby zobaczyć wyeksportowane dane.

**Aby Sprawdź dziennik błędów zadanie**

Aby rozwiązać problemy w przepływie pracy, Oozie plik dziennika znajduje się w temacie C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log z głównym węzłem klastra. Aby uzyskać informacji na temat protokołu RDP, zobacz [Zarządzanie Apache Hadoop clusters w HDInsight przy użyciu witryny Azure portal](hdinsight-administer-use-portal-linux.md).

**Aby ponownie uruchomić tego samouczka**

Aby ponownie uruchomić przepływ pracy, należy wykonać następujące zadania:

* Usuń plik danych wyjściowych skryptu programu Hive.
* Usuń dane w tabeli log4jLogsCount.

Poniżej przedstawiono przykładowy skrypt programu Windows PowerShell, który można użyć:

```powershell
$storageAccountName = "<AzureStorageAccountName>"
$containerName = "<ContainerName>"

#SQL database variables
$sqlDatabaseServer = "<SQLDatabaseServerName>"
$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
$sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
$sqlDatabaseName = "<SQLDatabaseName>"
$sqlDatabaseTableName = "log4jLogsCount"

Write-host "Delete the Hive script output file ..." -ForegroundColor Green
$storageaccountkey = get-AzStoragekey $storageAccountName | %{$_.Primary}
$destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
Remove-AzStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
$conn.open()
$cmd = New-Object System.Data.SqlClient.SqlCommand
$cmd.connection = $conn
$cmd.commandtext = "delete from $sqlDatabaseTableName"
$cmd.executenonquery()

$conn.close()
```

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku pokazano, jak zdefiniować przepływ pracy programu Oozie i koordynatora programu Oozie i jak uruchamiać zadanie usługi Oozie coordinator przy użyciu programu Azure PowerShell. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Rozpoczynanie pracy z usługą HDInsight][hdinsight-get-started]
* [Usługi Azure Blob storage za pomocą HDInsight][hdinsight-storage]
* [Administrowanie HDInsight za pomocą programu Azure PowerShell][hdinsight-admin-powershell]
* [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data]
* [Korzystanie z programu Apache Sqoop z usługą HDInsight][hdinsight-use-sqoop]
* [Korzystanie z programu Apache Hive z usługą HDInsight][hdinsight-use-hive]
* [Korzystanie z programu Apache Pig z usługą HDInsight][hdinsight-use-pig]
* [Opracowywanie programów MapReduce w języku Java dla HDInsight][hdinsight-develop-java-mapreduce]

[hdinsight-cmdlets-download]: https://go.microsoft.com/fwlink/?LinkID=325563

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-develop-java-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: https://hadoop.apache.org/
[apache-oozie-400]: https://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: https://oozie.apache.org/docs/3.3.2/

[powershell-download]: https://azure.microsoft.com/downloads/
[powershell-about-profiles]: https://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: https://docs.microsoft.com/powershell/scripting/setup/starting-windows-powershell?view=powershell-6
[powershell-script]: https://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png

[technetwiki-hive-error]: https://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
