---
title: Użyj oparte na czasie koordynatora Hadoop Oozie w usłudze HDInsight | Dokumentacja firmy Microsoft
description: Użyj oparte na czasie koordynatora Hadoop Oozie w usłudze HDInsight, Usługa danych big data. Dowiedz się, jak zdefiniować przepływy pracy Oozie i koordynatora i przesyłanie zadań.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00c3a395-d51a-44ff-af2d-1f116c4b1c83
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 10/04/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: db7f1f85601cfcf4531bb11176a21126625ce7f0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="use-time-based-oozie-coordinator-with-hadoop-in-hdinsight-to-define-workflows-and-coordinate-jobs"></a>Za pomocą opartego na czasie Oozie coordinator Hadoop w usłudze HDInsight do definiowania przepływów pracy i koordynowania zadania
W tym artykule dowiesz się, jak zdefiniować przepływy pracy i koordynatora i sposób włączania zadania koordynatora, w oparciu o czas. Warto przeprowadzić [Oozie użycia z usługą HDInsight] [ hdinsight-use-oozie] przed przeczytaniem tego artykułu. Oprócz Oozie można również zaplanować zadania przy użyciu fabryki danych Azure. Aby uzyskać fabryki danych Azure, zobacz [Use Pig i Hive z fabryką danych](../data-factory/transform-data.md).

> [!NOTE]
> W tym artykule wymaga klastra usługi HDInsight opartej na systemie Windows. Dla informacji o korzystaniu z Oozie, w tym zadania na podstawie czasu, w klastrze opartych na systemie Linux, zobacz [Oozie użycia z usługą Hadoop do definiowania i uruchomić przepływ pracy na HDInsight opartych na systemie Linux](hdinsight-use-oozie-linux-mac.md)

## <a name="what-is-oozie"></a>Co to jest Oozie
Apache Oozie to system koordynacji/przepływu pracy, który zarządza zadaniami na platformie Hadoop. Jest zintegrowany ze stosem platformy Hadoop i obsługuje zadania platformy Hadoop dla Apache MapReduce, Apache Pig Apache Hive i Apache Sqoop. Można go również używane do planowania zadań, które są specyficzne dla systemu, np. programów Java lub skryptów powłoki.

Na poniższej ilustracji przedstawiono przepływ pracy, który będzie implementowany:

![Diagram przepływu pracy][img-workflow-diagram]

Przepływ pracy zawiera dwa działania:

1. Akcja Hive uruchamia skrypt HiveQL zliczania wystąpień poszczególnych typów poziom dziennika, w pliku dziennika log4j. Każdy dziennik log4j składa się z linii pola zawiera pole [poziom dziennika], aby wyświetlić typ i ważność, na przykład:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    Dane wyjściowe skryptu Hive jest podobny do:

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Aby uzyskać więcej informacji na temat programu Hive, zobacz temat [Use Hive with HDInsight][hdinsight-use-hive] (Korzystanie z programu Hive z usługą HDInsight).
2. Akcja Sqoop eksportuje wynik akcji HiveQL do tabeli w bazie danych Azure SQL. Aby uzyskać więcej informacji o Sqoop, zobacz [Sqoop użycia z usługą HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Obsługiwane wersje Oozie w klastrach HDInsight, zobacz [nowości w wersjach klastra dostarczanych z usługą HDInsight?] [hdinsight-versions].
>
>

## <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Stacja robocza z programem Azure PowerShell**.

    > [!IMPORTANT]
    > Obsługa środowiska Azure PowerShell do celów zarządzania zasobami usługi HDInsight przy użyciu usługi Azure Service Manager jest **przestarzała** i zostanie usunięta z dniem 1 stycznia 2017 r. W czynnościach opisanych w niniejszym dokumencie są używane nowe polecenia cmdlet usługi HDInsight współpracujące z usługą Azure Resource Manager.
    >
    > Wykonaj kroki opisane w temacie [Instalowanie i konfigurowanie środowiska Azure PowerShell](/powershell/azureps-cmdlets-docs), aby zainstalować najnowszą wersję środowiska Azure PowerShell. Jeśli masz skrypty wymagające modyfikacji w celu użycia nowych poleceń cmdlet współpracujących z usługą Azure Resource Manager, zobacz temat [Migrowanie do narzędzi programistycznych opartych na usłudze Azure Resource Manager w celu obsługi klastrów usługi HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md), aby uzyskać więcej informacji.

* **Klaster usługi HDInsight**. Aby uzyskać informacje dotyczące tworzenia klastra usługi HDInsight, zobacz [Tworzenie klastrów usługi HDInsight][hdinsight-provision], lub [Rozpoczynanie pracy z usługą HDInsight][hdinsight-get-started]. Potrzebne są następujące dane do wykonywania kroków samouczka:

    <table border = "1">
    <tr><th>Właściwości klastra</th><th>Nazwa zmiennej środowiska Windows PowerShell</th><th>Wartość</th><th>Opis</th></tr>
    <tr><td>Nazwa klastra usługi HDInsight</td><td>$clusterName</td><td></td><td>Klaster usługi HDInsight, na którym będzie uruchomiona w tym samouczku.</td></tr>
    <tr><td>Nazwa użytkownika klastra usługi HDInsight</td><td>$clusterUsername</td><td></td><td>Nazwa użytkownika klastra usługi HDInsight. </td></tr>
    <tr><td>Hasło użytkownika klastra usługi HDInsight </td><td>$clusterPassword</td><td></td><td>Hasło użytkownika klastra usługi HDInsight.</td></tr>
    <tr><td>Nazwa konta magazynu platformy Azure</td><td>$storageAccountName</td><td></td><td>Dostępne w klastrze usługi HDInsight konto Azure Storage. W tym samouczku Użyj domyślnego konta magazynu, które zostały określone podczas procesu udostępniania klastra.</td></tr>
    <tr><td>Nazwa kontenera obiektów Blob platformy Azure</td><td>$containerName</td><td></td><td>Na przykład użyć kontenera magazynu obiektów Blob platformy Azure, służący do domyślnego systemu plików klastra usługi HDInsight. Domyślnie ma taką samą nazwę jak klaster usługi HDInsight.</td></tr>
    </table>

* **Baza danych Azure SQL**. Należy skonfigurować reguły zapory dla serwera bazy danych SQL, aby umożliwić dostęp ze stacji roboczej. Aby uzyskać instrukcje dotyczące tworzenia bazy danych Azure SQL i konfigurowania zapory, zobacz [rozpocząć korzystanie z bazy danych Azure SQL][sqldatabase-get-started]. Ten artykuł zawiera skrypt programu Windows PowerShell do tworzenia tabeli bazy danych Azure SQL, wymagającym w tym samouczku.

    <table border = "1">
    <tr><th>Właściwości bazy danych SQL</th><th>Nazwa zmiennej środowiska Windows PowerShell</th><th>Wartość</th><th>Opis</th></tr>
    <tr><td>Nazwa serwera bazy danych SQL</td><td>$sqlDatabaseServer</td><td></td><td>Bazy danych SQL server, do którego Sqoop będzie eksportować dane. </td></tr>
    <tr><td>Nazwa logowania bazy danych SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Nazwa logowania SQL Database.</td></tr>
    <tr><td>Hasło nazwy logowania bazy danych SQL</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Hasło nazwy logowania bazy danych SQL.</td></tr>
    <tr><td>Nazwa bazy danych SQL</td><td>$sqlDatabaseName</td><td></td><td>Baza danych Azure SQL, do którego Sqoop będzie eksportować dane. </td></tr>
    </table>

  > [!NOTE]
  > Domyślnie baza danych Azure SQL zezwala na połączenia z usługami Azure, takimi jak Azure HDInsight. Wyłączenie tego ustawienia zapory, należy włączyć ją z portalu Azure. Aby uzyskać instrukcje dotyczące tworzenia bazy danych SQL i konfigurowania reguł zapory, zobacz [tworzenie i Konfigurowanie bazy danych SQL][sqldatabase-get-started].

> [!NOTE]
> Wypełnianie wartości w tabelach. Jest przydatne w przypadku przechodzenia przez tego samouczka.

## <a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Zdefiniuj Oozie przepływu pracy i powiązanych skrypt HiveQL
Oozie definicje przepływów pracy są zapisywane w hPDL (XML procesu definition language). Domyślna nazwa pliku przepływu pracy jest *workflow.xml*.  Będzie Zapisz lokalnie plik przepływu pracy, a następnie wdrożyć w klastrze usługi HDInsight przy użyciu programu Azure PowerShell w dalszej części tego samouczka.

Akcja gałęzi w przepływie pracy wywołuje skrypt HiveQL. Ten plik skryptu zawiera trzy instrukcje HiveQL:

1. **Wykonywanie instrukcji DROP TABLE** usuwa tabeli Hive log4j, jeśli istnieje.
2. **Wykonywanie instrukcji CREATE TABLE** tworzy log4j tabeli programu Hive zewnętrznego, który wskazuje lokalizację pliku dziennika narzędzia log4j;
3. **Lokalizacja pliku dziennika log4j**. Ogranicznik pola jest ",". Ogranicznik wiersza domyślną jest "\n". Gałąź tabeli zewnętrznej służy do uniknięcia pliku danych usuwana z oryginalnej lokalizacji, w przypadku, gdy chcesz uruchomić przepływ pracy Oozie wiele razy.
4. **Instrukcja INSERT zastąpić** liczby wystąpień poszczególnych typów poziomu dziennika z tabeli Hive log4j i zapisuje dane wyjściowe do lokalizacji magazynu obiektów Blob platformy Azure.

> [!NOTE]
> Jest to znany problem ścieżki gałęzi. Należy uruchomić na ten problem podczas przesyłania zadania Oozie. Instrukcje dotyczące rozwiązywania problemu można znaleźć w witrynie TechNet Wiki: [HDInsight Hive błąd: nie można zmienić nazwy][technetwiki-hive-error].

**Aby określić plik skryptu HiveQL do wywołania przez przepływ pracy**

1. Utwórz plik tekstowy o następującej treści:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Istnieją trzy zmienne używane w skrypcie:

   * ${hiveTableName}
   * ${hiveDataFolder}
   * ${hiveOutputFolder}

     Plik definicji przepływu pracy (workflow.xml w tym samouczku) przekazuje te wartości do ten skrypt HiveQL w czasie wykonywania.
2. Zapisz plik jako **C:\Tutorials\UseOozie\useooziewf.hql** przy użyciu kodowania ANSI (ASCII). (Użyj Notatnik, jeśli ta opcja nie są dostępne w edytorze tekstów). Ten plik skryptu zostanie wdrożony w klastrze usługi HDInsight w dalszej części tego samouczka.

**Aby zdefiniować przepływu pracy**

1. Utwórz plik tekstowy o następującej treści:

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

    Istnieją dwie akcje zdefiniowane w przepływie pracy. Akcja do rozpoczęcia jest *RunHiveScript*. Jeśli akcja uruchamia *OK*, jest następnej akcji *RunSqoopExport*.

    RunHiveScript ma kilka zmiennych. Po przesłaniu zadania Oozie ze stacji roboczej za pomocą programu Azure PowerShell, przechodzą wartości.

    Zmienne przepływu pracy

    <table border = "1">
    <tr><th>Zmienne przepływu pracy</th><th>Opis</th></tr>
    <tr><td>${jobTracker}</td><td>Podaj adres URL śledzenia zadania usługi Hadoop. Użyj <strong>jobtrackerhost:9010</strong> w usłudze HDInsight klastra w wersji 3.0 i 2.0.</td></tr>
    <tr><td>${nameNode}</td><td>Podaj adres URL węzła nazwa usługi Hadoop. Użyj domyślnej wasb systemu plików: / / adresu, na przykład <i>wasb: / /&lt;containerName&gt;@&lt;storageAccountName&gt;. blob.core.windows.net</i>.</td></tr>
    <tr><td>${queueName}</td><td>Określa nazwę kolejki, która zadania zostaną przesłane do. Użyj <strong>domyślne</strong>.</td></tr>
    </table>

    Zmienne akcji gałęzi

    <table border = "1">
    <tr><th>Gałąź zmiennej akcji</th><th>Opis</th></tr>
    <tr><td>${hiveDataFolder}</td><td>Katalog źródłowy dla polecenia Hive Create Table.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>Folder wyjściowy dla instrukcji INSERT zastąpić.</td></tr>
    <tr><td>${hiveTableName}</td><td>Nazwa tabeli Hive, która odwołuje się do plików danych log4j.</td></tr>
    </table>

    Zmienne akcji Sqoop

    <table border = "1">
    <tr><th>Sqoop zmiennej akcji</th><th>Opis</th></tr>
    <tr><td>${sqlDatabaseConnectionString}</td><td>Parametry połączenia bazy danych SQL.</td></tr>
    <tr><td>${sqlDatabaseTableName}</td><td>Tabela bazy danych Azure SQL, do którego zostaną wyeksportowane dane.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>Folder wyjściowy dla instrukcji Hive Wstaw zastąpić. To jest tym samym folderze eksportu Sqoop (export-dir).</td></tr>
    </table>

    Aby uzyskać więcej informacji o przepływie pracy Oozie i przy użyciu działań przepływu pracy, zobacz [dokumentację Apache Oozie 4.0] [ apache-oozie-400] (dla usługi HDInsight klastra w wersji 3.0) lub [dokumentację Apache Oozie 3.3.2] [ apache-oozie-332] (dla usługi HDInsight klastra w wersji 2.1).

1. Zapisz plik jako **C:\Tutorials\UseOozie\workflow.xml** przy użyciu kodowania ANSI (ASCII). (Użyj Notatnik, jeśli ta opcja nie są dostępne w edytorze tekstów).

**Aby zdefiniować koordynatora**

1. Utwórz plik tekstowy o następującej treści:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
            <workflow>
                <app-path>${wfPath}</app-path>
            </workflow>
        </action>
    </coordinator-app>
    ```

    Istnieje pięć zmienne używane w pliku definicji:

   | Zmienna | Opis |
   | --- | --- |
   | ${coordFrequency} |Czas wstrzymania zadania. Częstotliwość zawsze jest wyrażone w minutach. |
   | ${coordStart} |Godzina rozpoczęcia zadania. |
   | ${coordEnd} |Godzina zakończenia zadania. |
   | ${coordTimezone} |Oozie przetwarza koordynator zadań w stałej strefy czasowej z nie czasu letniego (zazwyczaj reprezentowany przy użyciu czasu UTC). Ta strefa czasowa jest określane jako "przetwarzania Oozie strefa czasowa." |
   | ${wfPath} |Ścieżka workflow.xml.  Jeśli nazwa pliku przepływu pracy nie jest to domyślna nazwa pliku (workflow.xml), należy ją określić. |
2. Zapisz plik jako **C:\Tutorials\UseOozie\coordinator.xml** przy użyciu kodowania ANSI (ASCII). (Użyj Notatnik, jeśli ta opcja nie są dostępne w edytorze tekstów).

## <a name="deploy-the-oozie-project-and-prepare-the-tutorial"></a>Wdrażanie projektu Oozie i przygotowania samouczka
Będzie uruchomić skrypt programu Azure PowerShell, aby wykonać następujące czynności:

* Skopiuj skrypt HiveQL (useoozie.hql) do magazynu obiektów Blob platformy Azure, wasb:///tutorials/useoozie/useoozie.hql.
* Skopiuj workflow.xml wasb:///tutorials/useoozie/workflow.xml.
* Skopiuj coordinator.xml wasb:///tutorials/useoozie/coordinator.xml.
* Skopiuj plik danych (/ example/data/sample.log) do wasb:///tutorials/useoozie/data/sample.log.
* Tworzenie tabeli bazy danych Azure SQL do przechowywania danych eksportu Sqoop. Nazwa tabeli jest *log4jLogCount*.

**Informacje magazynie usługi HDInsight**

HDInsight używa magazynu obiektów Blob platformy Azure do przechowywania danych. wasb: / / to implementacja firmy Microsoft Hadoop rozproszonego systemu plików (HDFS) w magazynie obiektów Blob Azure. Aby uzyskać więcej informacji, zobacz [magazynu obiektów Blob Azure użycia z usługą HDInsight][hdinsight-storage].

Podczas obsługi administracyjnej klastra usługi HDInsight, konta magazynu obiektów Blob platformy Azure i w określonym kontenerze z tego konta zostaje wyznaczony jako domyślny system plików, podobnie jak w systemie plików HDFS. Oprócz tego konta magazynu można dodać dodatkowe konta magazynu z tej samej subskrypcji platformy Azure lub różnych subskrypcji Azure podczas procesu inicjowania obsługi administracyjnej. Aby uzyskać instrukcje dotyczące dodawania dodatkowych kont magazynu, zobacz [Obsługa administracyjna klastrów HDInsight][hdinsight-provision]. Aby uprościć skryptu programu Azure PowerShell używanych w tym samouczku, wszystkie pliki są przechowywane w domyślnym kontenerze systemu plików w lokalizacji */samouczki/useoozie*. Domyślnie ten kontener ma taką samą nazwę jak nazwa klastra usługi HDInsight.
Składnia jest następująca:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [!NOTE]
> Tylko *wasb: / /* składni jest obsługiwane w klastrze usługi HDInsight w wersji 3.0. Starszy *asv: / /* składnia jest obsługiwana w HDInsight 2.1 i 1.6 klastrów, ale nie jest obsługiwana w klastrach HDInsight 3.0.
>
> Wasb: / / ścieżka jest ścieżką wirtualną. Aby uzyskać więcej informacji, zobacz [magazynu obiektów Blob Azure użycia z usługą HDInsight][hdinsight-storage].

Plik, który jest przechowywany w domyślnym kontenerze systemu plików jest możliwy z usługi HDInsight przy użyciu dowolnej z poniższych identyfikatorów URI (na przykład używam programu workflow.xml):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Jeśli chcesz uzyskać dostęp do pliku bezpośrednio z konta magazynu jest nazwa obiektu blob dla pliku:

    tutorials/useoozie/workflow.xml

**Zrozumienie tabele programu Hive wewnętrznych i zewnętrznych**

Istnieje kilka rzeczy, które musisz wiedzieć o tabele programu Hive wewnętrznych i zewnętrznych:

* Polecenie CREATE TABLE tworzy wewnętrznej tabeli, znanej także jako zarządzane tabeli. Plik danych musi znajdować się w domyślnym kontenerze.
* Polecenie CREATE TABLE przenosi plik danych do/hive/magazynu/<TableName> folderu w domyślnym kontenerze.
* Polecenie CREATE TABLE zewnętrznych tworzy tabelę zewnętrzną. Plik danych może znajdować się poza kontener domyślny.
* Polecenie CREATE TABLE zewnętrznych nie powoduje przeniesienia pliku danych.
* Polecenie CREATE TABLE zewnętrznych nie zezwala na wszystkie podfoldery w folderze, który określono w klauzuli lokalizacji. Jest to powód, dlaczego samouczka tworzy kopię pliku sample.log.

Aby uzyskać więcej informacji, zobacz [HDInsight: Hive wewnętrznych i zewnętrznych wprowadzenie tabel][cindygross-hive-tables].

**Do przygotowania samouczka**

1. Otwórz program Windows PowerShell ISE (na ekranie Start systemu Windows 8, wpisz **PowerShell_ISE**, a następnie kliknij przycisk **programu Windows PowerShell ISE**. Aby uzyskać więcej informacji, zobacz [programu Windows PowerShell Start w systemie Windows 8 i Windows][powershell-start]).
2. W okienku u dołu uruchom następujące polecenie, aby nawiązać połączenia z subskrypcją platformy Azure:

    ```powershell
    Add-AzureAccount
    ```

    Pojawi się monit o podanie poświadczeń konta platformy Azure. Ta metoda dodawania połączenia subskrypcji wygaśnie, a po 12 godzinach, należy ponownie uruchomić polecenie cmdlet.

   > [!NOTE]
   > Jeśli masz wiele subskrypcji platformy Azure i subskrypcji domyślne nie chcesz, należy użyć <strong>AzureSubscription wybierz</strong> polecenia cmdlet, aby wybrać subskrypcję.

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

    Opisy więcej zmiennych, zobacz [wymagania wstępne](#prerequisites) sekcji, w tym samouczku.

4. Dołącz następujące skryptu w okienku skryptów:

    ```powershell
    # Create a storage context object
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    function uploadOozieFiles()
    {
        Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
        Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
        Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
        Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
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

5. Kliknij przycisk **Uruchom skrypt** lub naciśnij klawisz **F5** do uruchomienia skryptu. Dane wyjściowe będą podobne do:

    ![Dane wyjściowe przygotowania samouczka][img-preparation-output]

## <a name="run-the-oozie-project"></a>Uruchom projekt Oozie
Program Azure PowerShell obecnie nie udostępnia żadnych poleceń cmdlet do definiowania Oozie zadań. Można użyć **Invoke RestMethod** polecenia cmdlet do wywołania usługi sieci web Oozie. Interfejs API usług sieci web Oozie jest JSON interfejsu API REST protokołu HTTP. Aby uzyskać więcej informacji o usługach sieci web Oozie interfejsu API, zobacz [dokumentację Apache Oozie 4.0] [ apache-oozie-400] (dla usługi HDInsight klastra w wersji 3.0) lub [dokumentację Apache Oozie 3.3.2] [ apache-oozie-332] (dla usługi HDInsight klastra w wersji 2.1).

**Aby przesłać zadanie Oozie**

1. Otwórz program Windows PowerShell ISE (na ekranie Start systemu Windows 8, wpisz **PowerShell_ISE**, a następnie kliknij przycisk **programu Windows PowerShell ISE**. Aby uzyskać więcej informacji, zobacz [programu Windows PowerShell Start w systemie Windows 8 i Windows][powershell-start]).
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

    Opisy więcej zmiennych, zobacz [wymagania wstępne](#prerequisites) sekcji, w tym samouczku.

    $coordstart i $coordend są przepływu pracy rozpoczęcia i godzina zakończenia. Aby sprawdzić czas UTC/GMT, wyszukaj "czas utc" na bing.com. $CoordFrequency jest częstotliwość w minutach, który chcesz uruchomić przepływ pracy.
3. Dołącz następujące do skryptu. Ta część definiuje ładunek Oozie:

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
   > Główną różnicą w porównaniu do plik ładunku przesłanie przepływu pracy jest zmienna **oozie.coord.application.path**. Po przesłaniu zadania przepływu pracy, należy użyć **oozie.wf.application.path** zamiast tego.

4. Dołącz następujące do skryptu. Ta część umożliwia sprawdzenie stanu usługi sieci web Oozie:

    ```powershell
    function checkOozieServerStatus()
    {
        Write-Host "Checking Oozie server status..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieServerSatus = $jsonResponse[0].("systemMode")
        Write-Host "Oozie server status is $oozieServerSatus..."

        if($oozieServerSatus -notmatch "NORMAL")
        {
            Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
        }
    }
    ```

5. Dołącz następujące do skryptu. Ta część tworzy zadanie Oozie:

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
   > Po przesłaniu zadania przepływu pracy, należy wywołać, aby uruchomić zadanie, po utworzeniu zadania innej usługi sieci web. W takim przypadku zadania koordynatora jest wyzwalany przez czas. Zadanie zostanie uruchomione automatycznie.

6. Dołącz następujące do skryptu. Ta część umożliwia sprawdzenie stanu zadania Oozie:

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

7. (Opcjonalnie) Dołącz następujące do skryptu.

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

8. Dołącz następujące do skryptu:

    ```powershell
    checkOozieServerStatus
    # listOozieJobs
    $oozieJobId = createOozieJob($oozieJobId)
    checkOozieJobStatus($oozieJobId)
    # ShowOozieJobLog($oozieJobId)
    # killOozieJob($oozieJobId)
    ```

Usuń znaki #, jeśli chcesz uruchamiać dodatkowe funkcje.

9. Jeśli z klastrem usługi HDinsight w wersji 2.1, zastąp "https://$clusterName.azurehdinsight.net:443/oozie/v2/" z "https://$clusterName.azurehdinsight.net:443/oozie/v1/". Klaster usługi HDInsight w wersji 2.1 nie nie obsługuje wersji 2 usługi sieci web.
10. Kliknij przycisk **Uruchom skrypt** lub naciśnij klawisz **F5** do uruchomienia skryptu. Dane wyjściowe będą podobne do:

     ![Samouczek uruchomienia przepływu pracy w danych wyjściowych][img-runworkflow-output]
11. Połączenia z bazą danych SQL, aby zobaczyć wyeksportowane dane.

**Aby sprawdzić dziennik błędów zadania**

Rozwiązywać przepływu pracy można znaleźć w pliku dziennika Oozie w C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log z headnode klastra. Aby uzyskać informacji na temat protokołu RDP, zobacz [klastrów HDInsight administrowanie przy użyciu portalu Azure][hdinsight-admin-portal].

**Aby ponownie uruchomić samouczka**

Aby ponownie uruchomić przepływ pracy, należy wykonać następujące zadania:

* Usuń plik danych wyjściowych skryptu Hive.
* Usuń dane w tabeli log4jLogsCount.

Poniżej przedstawiono przykładowy skrypt programu Windows PowerShell, którego można używać:

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
$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

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
W tym samouczku przedstawiono sposób definiowania przepływów pracy Oozie i Oozie coordinator i jak uruchamiać zadanie Oozie coordinator przy użyciu programu Azure PowerShell. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Rozpoczynanie pracy z usługą HDInsight][hdinsight-get-started]
* [Użyj magazynu obiektów Blob platformy Azure z usługą HDInsight][hdinsight-storage]
* [Administrowanie HDInsight przy użyciu programu Azure PowerShell][hdinsight-admin-powershell]
* [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data]
* [Korzystanie z usługą HDInsight Sqoop][hdinsight-use-sqoop]
* [Korzystanie z programu Hive z usługą HDInsight][hdinsight-use-hive]
* [Korzystanie z języka Pig z usługą HDInsight][hdinsight-use-pig]
* [Tworzenie programów Java MapReduce dla usługi HDInsight][hdinsight-develop-java-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md

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

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
