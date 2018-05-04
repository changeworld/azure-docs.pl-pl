---
title: Analizowanie danych opóźnienie transmitowane przy użyciu platformy Hadoop w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać jednego skryptu środowiska Windows PowerShell do tworzenia klastra usługi HDInsight, uruchamiać zadania Hive, uruchom zadanie Sqoop i usunąć klaster.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00e26aa9-82fb-4dbe-b87d-ffe8e39a5412
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 0e91cf994306c115911d9dd9cf0018f7947502d8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analizowanie danych opóźnienie transmitowane przy użyciu usługi Hive w usłudze HDInsight
Gałąź umożliwia uruchomionych zadań MapReduce z Hadoop za pomocą skryptów języka przypominającego SQL o nazwie  *[HiveQL][hadoop-hiveql]*, które można zastosować do podsumowania, wyszukiwanie i analizowania dużych ilości danych.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie wymagają klastra usługi HDInsight opartej na systemie Windows. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows). Opis czynności, które pracy z opartą na systemie Linux klastrem, zobacz [analizowanie danych opóźnienie transmitowane przy użyciu usługi Hive w usłudze HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md).

Jedną z najważniejszych zalet usługi Azure HDInsight polega na rozdzieleniu magazynu danych i zasobów obliczeniowych. HDInsight używa magazynu obiektów Blob platformy Azure do przechowywania danych. Typowe zadania obejmuje trzy części:

1. **Przechowywanie danych w magazynie obiektów Blob Azure.**  Na przykład pogodowe danych czujnika, danych, dzienników sieci web, a w tym przypadku transmitowane opóźnienie danych są zapisywane w magazynie obiektów Blob Azure.
2. **Uruchamianie zadań.** Gdy nadejdzie czas przetwarzania danych, należy uruchomić skrypt programu Windows PowerShell (lub aplikacja kliencka) do tworzenia klastra usługi HDInsight, uruchamiania zadań i usunąć klaster. Zadania zapisać danych wyjściowych do magazynu obiektów Blob Azure. Dane są przechowywane, nawet po usunięciu klastra. W ten sposób płacisz za tylko co użytkownik ma używane.
3. **Pobieranie danych wyjściowych z magazynu obiektów Blob Azure**, lub w tym samouczku, należy wyeksportować dane do bazy danych Azure SQL.

Na poniższym diagramie przedstawiono scenariusza i struktura tego samouczka:

![HDI. FlightDelays.flow][img-hdi-flightdelays-flow]

Należy pamiętać, że numery na diagramie odpowiadają tytuły sekcji. **M** oznacza głównego procesu. **A** oznacza zawartość w dodatku.

Głównych części samouczka przedstawiono sposób użycia jednego skryptu środowiska Windows PowerShell do wykonywania następujących zadań:

* Tworzenie klastra usługi HDInsight.
* Uruchom zadania Hive w klastrze do obliczenia średniej opóźnień na lotniskach. Dane opóźnienia przesyłane są przechowywane na koncie magazynu obiektów Blob platformy Azure.
* Uruchom zadanie Sqoop, aby wyeksportować dane wyjściowe zadania Hive do bazy danych Azure SQL.
* Usuń klaster usługi HDInsight.

W dodatkach instrukcje można znaleźć przekazywanie danych opóźnienie transmitowane, ciąg zapytania Hive tworzenia/przekazywania i przygotowywanie bazy danych Azure SQL dla zadania Sqoop.

> [!NOTE]
> Kroki opisane w tym dokumencie dotyczą klastrów usługi HDInsight opartych na systemie Windows. Opis czynności, które pracy z opartą na systemie Linux klastrem, zobacz [analizowanie danych opóźnienie transmitowane przy użyciu Hive w usłudze HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md)

### <a name="prerequisites"></a>Wymagania wstępne
Przed przystąpieniem do wykonywania kroków opisanych w tym samouczku musisz mieć poniższe:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Stacja robocza z programem Azure PowerShell**.

    > [!IMPORTANT]
    > Obsługa programu Azure PowerShell do celów zarządzania zasobami usługi HDInsight przy użyciu usługi Azure Service Manager jest **przestarzała** i została usunięta z dniem 1 stycznia 2017 r. W czynnościach opisanych w niniejszym dokumencie są używane nowe polecenia cmdlet usługi HDInsight współpracujące z usługą Azure Resource Manager.
    >
    > Wykonaj kroki opisane w temacie [Instalowanie i konfigurowanie środowiska Azure PowerShell](/powershell/azureps-cmdlets-docs), aby zainstalować najnowszą wersję środowiska Azure PowerShell. Jeśli masz skrypty wymagające modyfikacji w celu użycia nowych poleceń cmdlet współpracujących z usługą Azure Resource Manager, zobacz temat [Migrowanie do narzędzi programistycznych opartych na usłudze Azure Resource Manager w celu obsługi klastrów usługi HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md), aby uzyskać więcej informacji.

**Pliki używane w tym samouczku**

W tym samouczku używana wydajności na czas linii lotniczych dane transmitowane z [badań i innowacyjnych administracyjnej technologii, biura statystyk transportu lub RICIE][rita-website].
Kopię danych, został przesłany do kontenera magazynu obiektów Blob platformy Azure z uprawnieniem dostępu publicznego obiektu Blob.
Część skrypt programu PowerShell kopiuje dane z publicznym kontenerze obiektów blob do domyślnego kontenera obiektów blob klastra. Skrypt HiveQL również zostanie skopiowany do tego samego kontenera obiektów Blob.
Jeśli chcesz dowiedzieć się, jak get/przekazywania danych do konta magazynu i Utwórz/przekazywania pliku skrypt HiveQL, zobacz [dodatek a.](#appendix-a) i [dodatek B](#appendix-b).

W poniższej tabeli wymieniono pliki używane w tym samouczku:

<table border="1">
<tr><th>Pliki</th><th>Opis</th></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>Plik skryptu HiveQL używane przez zadania Hive. Ten skrypt został przekazany do konta magazynu obiektów Blob platformy Azure z dostępem public. <a href="#appendix-b">Dodatek B</a> zawiera instrukcje dotyczące przygotowania i przekazywanie tego pliku do swojego konta magazynu obiektów Blob platformy Azure.</td></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Dane wejściowe zadania Hive. Dane przekazane do konta magazynu obiektów Blob platformy Azure z dostępem public. <a href="#appendix-a">Dodatek a.</a> zawiera instrukcje dotyczące pobierania danych i przekazywanie danych do swojego konta magazynu obiektów Blob platformy Azure.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Ścieżka danych wyjściowych zadania Hive. Domyślny kontener jest używany do przechowywania danych wyjściowych.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>Folder stan zadania Hive na domyślny kontener.</td></tr>
</table>

## <a name="create-cluster-and-run-hivesqoop-jobs"></a>Tworzenie klastra i uruchamianie zadania Hive/Sqoop
MapReduce z Hadoop jest przetwarzanie wsadowe. Najbardziej ekonomiczny sposób uruchamiania zadania Hive jest utworzenie klastra zadania i usunąć zadanie po zakończeniu zadania. Poniższy skrypt obejmuje całego procesu.
Aby uzyskać więcej informacji na temat tworzenia klastra usługi HDInsight i uruchamiania zadań Hive, zobacz [klastrów utworzyć Hadoop w usłudze HDInsight] [ hdinsight-provision] i [używanie Hive z usługą HDInsight][hdinsight-use-hive].

**Uruchamianie zapytań Hive programu Azure PowerShell**

1. Tworzenie bazy danych Azure SQL i tabeli dla danych wyjściowych zadania Sqoop zgodnie z instrukcjami podanymi w [dodatku C](#appendix-c).
2. Otwórz program Windows PowerShell ISE, a następnie uruchom następujący skrypt:

    ```powershell
    $subscriptionID = "<Azure Subscription ID>"
    $nameToken = "<Enter an Alias>"

    ###########################################
    # You must configure the follwing variables
    # for an existing Azure SQL Database
    ###########################################
    $existingSqlDatabaseServerName = "<Azure SQL Database Server>"
    $existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
    $existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
    $existingSqlDatabaseName = "<Azure SQL Database name>"

    $localFolder = "E:\Tutorials\Downloads\" # A temp location for copying files.
    $azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different

    ###########################################
    # (Optional) configure the following variables
    ###########################################

    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2"

    $HDInsightClusterName = $namePrefix + "hdi"
    $httpUserName = "admin"
    $httpPassword = "<Enter the Password>"

    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $HDInsightClusterName # use the cluster name

    $existingSqlDatabaseTableName = "AvgDelays"
    $sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"

    $hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql"

    $jobStatusFolder = "/tutorials/flightdelays/jobstatus"

    ###########################################
    # Login
    ###########################################
    try{
        $acct = Get-AzureRmSubscription
    }
    catch{
        Connect-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionID $subscriptionID

    ###########################################
    # Create a new HDInsight cluster
    ###########################################

    # Create ARM group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the default storage account
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS

    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext

    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -Location $location `
        -ClusterType Hadoop `
        -OSType Windows `
        -ClusterSizeInNodes 2 `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $existingDefaultBlobContainerName

    ###########################################
    # Prepare the HiveQL script and source data
    ###########################################

    # Create the temp location
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
    $blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
    #$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload data to default container

    $azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"

    Invoke-Expression -Command:$azcopycmd

    ###########################################
    # Submit the Hive job
    ###########################################
    Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
    $response = Invoke-AzureRmHDInsightHiveJob `
                    -Files $hqlScriptFile `
                    -DefaultContainer $defaultBlobContainerName `
                    -DefaultStorageAccountName $defaultStorageAccountName `
                    -DefaultStorageAccountKey $defaultStorageAccountKey `
                    -StatusFolder $jobStatusFolder

    write-Host $response

    ###########################################
    # Submit the Sqoop job
    ###########################################
    $exportDir = "wasb://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"

    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
                    -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ResourceGroupName $resourceGroupName `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose

    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -HttpCredential $httpCredential `
        -WaitTimeoutInSeconds 3600 `
        -Job $sqoopJob.JobId

    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -DefaultContainer $existingDefaultBlobContainerName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError

    ###########################################
    # Delete the cluster
    ###########################################
    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName
    ```
3. Połączenia z bazą danych SQL i zobacz opóźnienia średni transmitowane przez Miasto w tabeli AvgDelays:

    ![HDI. FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]

- - -

## <a id="appendix-a"></a>Dodatek a. — Przekaż transmitowane opóźnienie dane do magazynu obiektów Blob platformy Azure
Przekazywanie plików danych i plików skryptu po stronie HiveQL (zobacz [dodatek B](#appendix-b)) wymaga niektórych planowania. Będzie przechowywać pliki danych i pliku HiveQL przed utworzeniem klastra usługi HDInsight i uruchamiania zadań Hive. Dostępne są dwie opcje:

* **Użyj tego samego konta magazynu Azure, który będzie używany przez klaster usługi HDInsight jako domyślny system plików.** Ponieważ klaster usługi HDInsight nie będzie mieć klucz dostępu do konta magazynu, nie trzeba wprowadzać żadnych dodatkowych zmian.
* **Użyj innego konta usługi Azure Storage z systemu plików domyślnego klastra usługi HDInsight.** Jeśli jest to możliwe, należy zmodyfikować tworzenia część programu Windows PowerShell skrypt znalezione w [klaster tworzenia usługi HDInsight i uruchamiania zadań Hive/Sqoop](#runjob) połączenia konta magazynu jako dodatkowe konta magazynu. Aby uzyskać instrukcje, zobacz [klastrów utworzyć Hadoop w usłudze HDInsight][hdinsight-provision]. Klaster usługi HDInsight wie, następnie klucz dostępu dla konta magazynu.

> [!NOTE]
> Ścieżki do magazynu obiektów Blob dla pliku danych jest trudna kodowanych w pliku skryptu HiveQL. Należy zaktualizować go odpowiednio.

**Aby pobrać dane transmitowane**

1. Przejdź do [badań i Nowatorska technologia administracji, biura statystyk transportu][rita-website].
2. Na stronie wybierz następujące wartości:

    <table border="1">
    <tr><th>Name (Nazwa)</th><th>Wartość</th></tr>
    <tr><td>Filtr roku</td><td>2013 </td></tr>
    <tr><td>Filtruj okres</td><td>styczeń</td></tr>
    <tr><td>Pola</td><td>*Rok*, *FlightDate*, *UniqueCarrier*, *operatora*, *FlightNum*, *OriginAirportID*, *Pochodzenia*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*,  *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*,  *LateAircraftDelay* (Usuń zaznaczenie wszystkich innych pól)</td></tr>
    </table>
3. Kliknij przycisk **Pobierz**.
4. Rozpakuj plik **C:\Tutorials\FlightDelay\2013Data** folderu. Każdy plik jest plikiem CSV i jest rozmiar około 60GB.
5. Zmień nazwę miesiąca, który zawiera dane dla pliku. Na przykład, czy nazwę pliku zawierającego dane stycznia *January.csv*.
6. Powtórz kroki 2 i 5, aby pobrać plik dla każdej z 12 miesięcy w 2013. Konieczne będzie co najmniej jeden plik do uruchamiania w samouczku.

**Aby przekazać dane opóźnienie przesyłane do magazynu obiektów Blob platformy Azure**

1. Przygotowanie parametrów:

    <table border="1">
    <tr><th>Nazwa zmiennej</th><th>Uwagi</th></tr>
    <tr><td>$storageAccountName</td><td>Konto magazynu Azure, której chcesz przekazać dane do.</td></tr>
    <tr><td>$blobContainerName</td><td>Gdzie chcesz przekazać dane do kontenera obiektów Blob.</td></tr>
    </table>
2. Otwórz program Azure PowerShell ISE.
3. Wklej poniższy skrypt w okienku skryptów:

    ```powershell
    [CmdletBinding()]
    Param(

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
        [String]$storageAccountName,

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
        [String]$blobContainerName
    )

    #Region - Variables
    $localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
    $destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
    #EndRegion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Connect-AzureRmAccount}
    #EndRegion

    #Region - Validate user input
    Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
    # Validate the Storage account
    if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
    {
        Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
        exit
    }
    else{
        $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
    }

    # Validate the container
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
    {
        Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
        Exit
    }
    #EngRegion

    #Region - Copy the file from local workstation to Azure Blob storage
    if (test-path -Path $localFolder)
    {
        foreach ($item in Get-ChildItem -Path $localFolder){
            $fileName = "$localFolder\$item"
            $blobName = "$destFolder/$item"

            Write-Host "Copying $fileName to $blobName" -ForegroundColor Green

            Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
        }
    }
    else
    {
        Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
    }

    # List the uploaded files on HDInsight
    Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
    #EndRegion
    ```
4. Naciśnij klawisz **F5**, aby uruchomić skrypt.

Jeśli chcesz użyć innej metody do przekazywania plików, upewnij się, czy ścieżka pliku jest samouczki/flightdelay/danych. Aby uzyskać dostęp do plików składnia jest następująca:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

Ścieżka samouczki/flightdelay/danych jest folder wirtualny utworzony po przekazaniu plików. Sprawdź, czy pliki 12, po jednej dla każdego miesiąca.

> [!NOTE]
> Zaktualizuj zapytanie Hive można odczytać z nowej lokalizacji.
>
> Należy albo skonfigurować uprawnienia dostępu do kontenera do być publiczne lub konto magazynu z klastra usługi HDInsight. W przeciwnym razie nie będzie dostęp do plików danych ciąg zapytania Hive.

- - -

## <a id="appendix-b"></a>Dodatek B — tworzenie i przekazywanie skrypt HiveQL
Przy użyciu programu Azure PowerShell, można uruchomić wiele instrukcje HiveQL jeden naraz lub pakietu w pliku skryptu za pomocą instrukcji HiveQL. W tej sekcji przedstawiono sposób utworzyć skrypt HiveQL i Prześlij skrypt do magazynu obiektów Blob platformy Azure przy użyciu programu Azure PowerShell. Gałąź wymaga skrypty HiveQL, które mają być przechowywane w magazynie obiektów Blob Azure.

Skrypt HiveQL będzie wykonywać następujące czynności:

1. **Tabelę delays_raw**, w przypadku, gdy tabela już istnieje.
2. **Tworzenie tabeli Hive zewnętrznych delays_raw** wskazanie lokalizacji magazynu obiektów Blob z plikami opóźnienie transmitowane. To zapytanie określa, czy pola są rozdzielone "," i zakończenia wierszy przy "\n". Stanowi to problem, gdy wartości pól zawierać przecinków, ponieważ gałąź nie może odróżnić przecinkami, która jest ogranicznik pola i jedną, która jest częścią wartość pola (co ma miejsce w wartości pól dla źródła\_MIASTA\_nazwę i DOCELOWY\_MIASTA\_nazwy). Aby rozwiązać ten problem, zapytanie tworzy TEMP kolumn do przechowywania danych, niepoprawnie podzielonej na kolumny.
3. **Tabelę opóźnienia**, w przypadku, gdy tabela już istnieje.
4. **Utwórz tabelę opóźnienia**. Warto wyczyścić dane przed dalsze przetwarzanie. To zapytanie tworzy nową tabelę, *opóźnienia*, z tabeli delays_raw. Należy pamiętać, że kolumny TEMP (jak już wspomniano) nie są kopiowane, a **podciąg** funkcja służy do usuwania cudzysłów z danych.
5. **Obliczenia bazy danych pogody średnie opóźnienie i grup wyników przez nazwę miejscowości.** Będzie on również zapisuje wyniki do magazynu obiektów Blob. Zapytanie spowoduje usunięcie apostrofów z danych i pominie wiersze, w których wartość **weather_delay** ma wartość null. Jest to konieczne, ponieważ Sqoop, używane w dalszej części tego samouczka nie bezpiecznie obsłużyć te wartości domyślne.

Aby uzyskać pełną listę poleceń HiveQL, zobacz [Hive języka definicji danych][hadoop-hiveql]. Każde polecenie HiveQL musi kończyć się średnikiem.

**Aby utworzyć plik skryptu HiveQL**

1. Przygotowanie parametrów:

    <table border="1">
    <tr><th>Nazwa zmiennej</th><th>Uwagi</th></tr>
    <tr><td>$storageAccountName</td><td>Gdzie chcesz przekazać skrypt HiveQL, aby konto usługi Azure Storage.</td></tr>
    <tr><td>$blobContainerName</td><td>Kontener obiektów Blob, gdy chcesz przekazać skrypt HiveQL.</td></tr>
    </table>
    
2. Otwórz program Azure PowerShell ISE.  

3. Skopiuj i wklej poniższy skrypt w okienku skryptów:  

    ```powershell
    [CmdletBinding()]
    Param(

        # Azure Blob storage variables
        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
        [String]$storageAccountName,

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
        [String]$blobContainerName
    )

    #region - Define variables
    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    # The HiveQL script file is exported as this file before it's uploaded to Blob storage
    $hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"

    # The HiveQL script file will be uploaded to Blob storage as this blob name
    $hqlBlobName = "tutorials/flightdelay/flightdelays.hql"

    # These two constants are used by the HiveQL script file
    #$srcDataFolder = "tutorials/flightdelay/data"
    $dstDataFolder = "/tutorials/flightdelay/output"
    #endregion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Connect-AzureRmAccount}
    #EndRegion

    #Region - Validate user input
    Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
    # Validate the Storage account
    if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
    {
        Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
        exit
    }
    else{
        $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
    }

    # Validate the container
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
    {
        Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
        Exit
    }
    #EngRegion

    #region - Validate the file and file path

    # Check if a file with the same file name already exists on the workstation
    Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
    if (test-path $hqlLocalFileName){

        $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'

        if ($isDelete.ToLower() -ne "y")
        {
            Exit
        }
    }

    # Create the folder if it doesn't exist
    $folder = split-path $hqlLocalFileName
    if (-not (test-path $folder))
    {
        Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green

        new-item $folder -ItemType directory
    }
    #end region

    #region - Write the Hive script into a local file
    Write-Host "`nWriting the Hive script into a file on your workstation ..." `
                -ForegroundColor Green

    $hqlDropDelaysRaw = "DROP TABLE delays_raw;"

    $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
            "YEAR string, " +
            "FL_DATE string, " +
            "UNIQUE_CARRIER string, " +
            "CARRIER string, " +
            "FL_NUM string, " +
            "ORIGIN_AIRPORT_ID string, " +
            "ORIGIN string, " +
            "ORIGIN_CITY_NAME string, " +
            "ORIGIN_CITY_NAME_TEMP string, " +
            "ORIGIN_STATE_ABR string, " +
            "DEST_AIRPORT_ID string, " +
            "DEST string, " +
            "DEST_CITY_NAME string, " +
            "DEST_CITY_NAME_TEMP string, " +
            "DEST_STATE_ABR string, " +
            "DEP_DELAY_NEW float, " +
            "ARR_DELAY_NEW float, " +
            "CARRIER_DELAY float, " +
            "WEATHER_DELAY float, " +
            "NAS_DELAY float, " +
            "SECURITY_DELAY float, " +
            "LATE_AIRCRAFT_DELAY float) " +
        "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
        "LINES TERMINATED BY '\n' " +
        "STORED AS TEXTFILE " +
        "LOCATION 'wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';"

    $hqlDropDelays = "DROP TABLE delays;"

    $hqlCreateDelays = "CREATE TABLE delays AS " +
        "SELECT YEAR AS year, " +
            "FL_DATE AS flight_date, " +
            "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
            "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
            "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
            "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
            "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
            "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
            "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
            "DEST_AIRPORT_ID AS dest_airport_id, " +
            "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
            "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
            "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
            "DEP_DELAY_NEW AS dep_delay_new, " +
            "ARR_DELAY_NEW AS arr_delay_new, " +
            "CARRIER_DELAY AS carrier_delay, " +
            "WEATHER_DELAY AS weather_delay, " +
            "NAS_DELAY AS nas_delay, " +
            "SECURITY_DELAY AS security_delay, " +
            "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
        "FROM delays_raw;"

    $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
        "SELECT regexp_replace(origin_city_name, '''', ''), " +
            "avg(weather_delay) " +
        "FROM delays " +
        "WHERE weather_delay IS NOT NULL " +
        "GROUP BY origin_city_name;"

    $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

    $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force
    #endregion

    #region - Upload the Hive script to the default Blob container
    Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green

    # Create a storage context object
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Upload the file from local workstation to Blob storage
    Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
    #endregion

    Write-host "`nEnd of the PowerShell script" -ForegroundColor Green
    ```

    Poniżej przedstawiono zmiennych w skrypcie:

   * **$hqlLocalFileName** -skrypt zapisuje plik skryptu HiveQL lokalnie przed przekazaniem go do magazynu obiektów Blob. Jest to nazwa pliku. Wartość domyślna to <u>C:\tutorials\flightdelay\flightdelays.hql</u>.
   * **$hqlBlobName** — jest to nazwa obiektu blob pliku skryptu HiveQL używana w magazynie obiektów Blob platformy Azure. Wartość domyślna to tutorials/flightdelay/flightdelays.hql. Ponieważ plik zostanie zapisany bezpośrednio do magazynu obiektów Blob platformy Azure, nie istnieje "/" na początku nazwy obiektu blob. Jeśli chcesz uzyskać dostęp do pliku z magazynu obiektów Blob, należy dodać "/" na początku nazwy pliku.
   * **$srcDataFolder** i **$dstDataFolder** -= "data samouczki/flightdelay" = "samouczki flightdelay/wyjściowej"

- - -
## <a id="appendix-c"></a>Dodatek C — przygotowanie bazy danych Azure SQL dla danych wyjściowych zadania Sqoop
**Aby przygotować bazy danych SQL (Scalaj to przy użyciu skryptu Sqoop)**

1. Przygotowanie parametrów:

    <table border="1">
    <tr><th>Nazwa zmiennej</th><th>Uwagi</th></tr>
    <tr><td>$sqlDatabaseServerName</td><td>Nazwa serwera bazy danych Azure SQL. Wprowadź nic do utworzenia nowego serwera.</td></tr>
    <tr><td>$sqlDatabaseUsername</td><td>Nazwa logowania dla serwera bazy danych Azure SQL. Jeśli $sqlDatabaseServerName jest istniejący serwer, logowania i hasło logowania są używane do uwierzytelniania z serwerem. W przeciwnym razie są one używane do utworzenia nowego serwera.</td></tr>
    <tr><td>$sqlDatabasePassword</td><td>Hasło nazwy logowania dla serwera bazy danych Azure SQL.</td></tr>
    <tr><td>$sqlDatabaseLocation</td><td>Ta wartość jest używana tylko wtedy, gdy tworzysz nowy serwer bazy danych platformy Azure.</td></tr>
    <tr><td>$sqlDatabaseName</td><td>Baza danych SQL, użyty do utworzenia tabeli AvgDelays zadania Sqoop. Utworzy bazę danych o nazwie HDISqoop pozostawić je puste. Nazwa tabeli dla danych wyjściowych zadania Sqoop jest AvgDelays. </td></tr>
    </table>
    
2. Otwórz program Azure PowerShell ISE.

3. Skopiuj i wklej poniższy skrypt w okienku skryptów:  

    ```powershell
    [CmdletBinding()]
    Param(

        # Azure Resource group variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
        [String]$resourceGroupName,

        # SQL database server variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
        [String]$sqlDatabaseServer,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database admin user.")]
        [String]$sqlDatabaseLogin,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database admin user password.")]
        [String]$sqlDatabasePassword,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the region to create the Database in.")]
        [String]$sqlDatabaseLocation,   #For example, West US.

        # SQL database variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
        [String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
    )

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Constants and variables

    # IP address REST service used for retrieving external IP address and creating firewall rules
    [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
    [String]$fireWallRuleName = "FlightDelay"

    # SQL database variables
    [String]$sqlDatabaseMaxSizeGB = 10

    #SQL query string for creating AvgDelays table
    [String]$sqlDatabaseTableName = "AvgDelays"
    [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [origin_city_name] [nvarchar](50) NOT NULL,
                [weather_delay] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
            (
                [origin_city_name] ASC
            )
            )"
    #endregion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Connect-AzureRmAccount}
    #EndRegion

    #region - Create and validate Azure resouce group
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
    }

    #EndRegion

    #region - Create and validate Azure SQL database server
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

        $sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan

        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress

        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
    }

    #endregion

    #region - Create and validate Azure SQL database

    try {
        Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
    }
    catch {
        Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
        New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
    }

    #endregion

    #region -  Execute an SQL command to create the AvgDelays table

    Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = $sqlCreateAvgDelaysTable
    $cmd.executenonquery()

    $conn.close()

    Write-host "`nEnd of the PowerShell script" -ForegroundColor Green
    ```

   > [!NOTE]
   > Skrypt używa representational stanu usługi transfer (REST), http://bot.whatismyipaddress.com, aby pobrać zewnętrzny adres IP. Adres IP jest używany do utworzenia reguły zapory dla serwera bazy danych SQL.

    Poniżej przedstawiono niektóre zmienne używane w skrypcie:

   * **$ipAddressRestService** — wartość domyślna to http://bot.whatismyipaddress.com. Jest on publiczny adres IP usługi REST do pobierania zewnętrzny adres IP. Korzystania z innych usług, jeśli chcesz. Zewnętrzny adres IP pobrane za pośrednictwem usługi będzie służyć do utworzenia reguły zapory dla serwera bazy danych Azure SQL, dzięki czemu można dostęp do bazy danych ze stacji roboczej (przy użyciu skryptu programu Windows PowerShell).
   * **$fireWallRuleName** — jest to nazwa reguły zapory dla serwera bazy danych Azure SQL. Nazwa domyślna to <u>FlightDelay</u>. Można zmienić jego nazwę, jeśli chcesz.
   * **$sqlDatabaseMaxSizeGB** — ta wartość jest używana tylko wtedy, gdy tworzysz nowy serwer bazy danych Azure SQL. Wartość domyślna wynosi 10GB. 10GB to wystarczający do celów tego samouczka.
   * **$sqlDatabaseName** — ta wartość jest używana tylko wtedy, gdy tworzysz nową bazę danych Azure SQL. Wartość domyślna to HDISqoop. Jeśli należy zmienić jego nazwę, należy zaktualizować odpowiednio skrypt programu Windows PowerShell Sqoop.
4. Naciśnij klawisz **F5**, aby uruchomić skrypt.
5. Sprawdzanie poprawności danych wyjściowych skryptu. Upewnij się, że skrypt został uruchomiony pomyślnie.

## <a id="nextsteps"></a> Następne kroki
Teraz można poznać sposób przekazywania pliku do magazynu obiektów Blob platformy Azure, jak wypełnianie tabeli Hive za pomocą danych z magazynu obiektów Blob platformy Azure, sposób uruchamiania zapytań Hive i sposobu użycia Sqoop do eksportowania danych z systemu plików HDFS do bazy danych Azure SQL. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Rozpoczynanie pracy z usługą HDInsight][hdinsight-get-started]
* [Korzystanie z programu Hive z usługą HDInsight][hdinsight-use-hive]
* [Korzystanie z Oozie z usługą HDInsight][hdinsight-use-oozie]
* [Korzystanie z usługą HDInsight Sqoop][hdinsight-use-sqoop]
* [Korzystanie z języka Pig z usługą HDInsight][hdinsight-use-pig]
* [Tworzenie programów Java MapReduce dla usługi HDInsight][hdinsight-develop-mapreduce]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
