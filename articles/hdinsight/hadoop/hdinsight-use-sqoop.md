---
title: Wykonywanie zadań Apache Sqoop z usługą Azure HDInsight (Apache Hadoop)
description: Dowiedz się, jak używać programu Azure PowerShell na stacji roboczej do uruchamiania narzędzia Sqoop importu i eksportu między klastrem usługi Hadoop a bazą danych Azure SQL database.
ms.reviewer: jasonh
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 9351ffabd1a263de1ff262fe5f6fef48be518836
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446200"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Z usługą Hadoop w HDInsight przy użyciu narzędzia Apache Sqoop
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak do importowania i eksportowania między klastra HDInsight i Azure SQL database lub bazy danych programu SQL Server przy użyciu narzędzia Apache Sqoop w HDInsight.

Mimo że Apache Hadoop jest naturalnym wyborem do przetwarzania danych z częściową strukturą i bez struktury, takich jak dzienniki i pliki, mogą również być potrzebne do przetwarzania danych strukturalnych, które są przechowywane w relacyjnych baz danych.

[Apache Sqoop] [ sqoop-user-guide-1.4.4] to narzędzie do transferu danych między klastrami Hadoop a relacyjnymi bazami danych. Służy do importowania danych z systemu zarządzania relacyjnymi bazami danych (RDBMS), takie jak SQL Server, MySQL lub Oracle do rozproszonego systemu plików Hadoop (HDFS), przekształcania danych na platformie Hadoop MapReduce lub Apache Hive i następnie eksportować dane z powrotem do RDBMS . W tym samouczku używasz bazy danych programu SQL Server Twoja relacyjna baza danych.

Dla wersji Sqoop, które są obsługiwane w klastrach HDInsight, zobacz [nowości w wersjach klastra, dostarczone przez HDInsight?][hdinsight-versions]

## <a name="understand-the-scenario"></a>Omówienie scenariusza

Klaster HDInsight jest dostarczany z pewnymi przykładowymi danymi. Możesz użyć następujących dwóch próbek:

* Pliku dziennika Apache Log4j, który znajduje się w folderze */example/data/sample.log*. Następujące dzienniki są wyodrębniane z pliku:
  
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
* Tabela programu Hive o nazwie *hivesampletable*, które odwołują się do pliku danych znajdującym się w */hive/warehouse/hivesampletable*. Tabela zawiera niektóre dane o urządzeniach mobilnych. 
  
  | Pole | Typ danych |
  | --- | --- |
  | clientid |string |
  | querytime |string |
  | na rynku |string |
  | deviceplatform |string |
  | devicemake |string |
  | devicemodel |string |
  | state |string |
  | Kraj |string |
  | querydwelltime |double |
  | Identyfikator sesji |bigint |
  | sessionpagevieworder |bigint |

W tym samouczku użyjesz tymi dwoma zestawami danych do testowania Sqoop importu i eksportu.

## <a name="create-cluster-and-sql-database"></a>Tworzenie klastra i bazy danych SQL
W tej sekcji dowiesz się, jak utworzyć klaster, bazy danych SQL i schematy bazy danych SQL do wykonywania instrukcji samouczka przy użyciu witryny Azure portal i szablonu usługi Azure Resource Manager. Szablon można znaleźć w [szablony szybkiego startu platformy](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Szablon usługi Resource Manager wywołuje pakiet bacpac do wdrożenia schematów tabel do usługi SQL database.  Pakiet bacpac znajduje się w publicznym kontenerze obiektów blob, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Jeśli chcesz użyć kontenera prywatnych dla plików bacpac, użyj następujących wartości w szablonie:
   
```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

Jeśli wolisz korzystać z programu Azure PowerShell do tworzenia klastra i bazy danych SQL, zobacz [dodatek a](#appendix-a---a-powershell-sample).

> [!NOTE]  
> Importowanie za pomocą szablonu lub witryny Azure portal obsługuje tylko importowanie pliku BACPAC z magazynu obiektów blob platformy Azure.

**Aby skonfigurować środowisko przy użyciu szablonu usługi resource management**
1. Kliknij poniższy obraz, aby otworzyć szablon usługi Resource Manager w witrynie Azure portal.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
2. Wprowadź następujące właściwości:

    - **Subskrypcja**: Wprowadź swoją subskrypcję platformy Azure.
    - **Grupa zasobów**: Utwórz nową grupę zasobów platformy Azure, lub wybierz istniejącą grupę zasobów.  Grupa zasobów to w celu zarządzania.  Jest kontenerem dla obiektów.
    - **Lokalizacja**: Wybierz region.
    - **ClusterName**: Wprowadź nazwę klastra usługi Hadoop.
    - **Nazwa logowania i hasło klastra**: Domyślna nazwa logowania to admin.
    - **Nazwa użytkownika i hasło SSH**.
    - **Baza danych SQL server, nazwa logowania i hasło**.
    - **Lokalizacja _artifacts**: Użyj wartości domyślnej, chyba że chcesz użyć własnego pliku bacpac w innej lokalizacji.
    - **Token sygnatury dostępu współdzielonego lokalizacji _artifacts**: Pozostaw to pole puste.
    - **Nazwa pliku Bacpac**: Użyj wartości domyślnej, chyba że chcesz użyć własnego pliku bacpac.
     
        Trwale zakodowana w sekcji zmiennych są następujące wartości:
        
        |Name (Nazwa)|Wartość|
        |----|-----|
        | Domyślna nazwa konta magazynu | &lt;ClusterName>store |
        | Nazwa serwera bazy danych SQL Azure | &lt;ClusterName>dbserver |
        | Nazwa bazy danych SQL Azure | &lt;ClusterName>db |
     
3. Wybierz **zgodę na warunki i postanowienia, o których wspomniano**.
4. Kliknij pozycję **Kup**. Zostanie wyświetlony nowy Kafelek zatytułowany przesyłanie wdrożenia dla wdrożenia szablonu. Utworzenie klastra i bazy danych SQL trwa około 20 minut.

Jeśli zdecydujesz się używać istniejącej bazy danych Azure SQL lub programu Microsoft SQL Server

* **Usługa Azure SQL database**: Należy skonfigurować reguły zapory dla serwera bazy danych Azure SQL zezwolić na dostęp ze swojej stacji roboczej. Aby uzyskać instrukcje na temat tworzenia usługi Azure SQL database i konfigurowanie zapory, zobacz [rozpoczęcie korzystania z bazy danych Azure SQL][sqldatabase-get-started]. 
  
  > [!NOTE]  
  > Domyślnie usługi Azure SQL database zezwala na połączenia z usługami platformy Azure, takich jak Azure HDInsight. Jeśli to ustawienie zapory jest wyłączone, musisz ją włączyć w witrynie Azure portal. Aby uzyskać instrukcje dotyczące tworzenia usługi Azure SQL database i konfigurowanie reguły zapory, zobacz [tworzenie i Konfigurowanie bazy danych SQL][sqldatabase-create-configure].

* **Program SQL Server**: W przypadku klastra usługi HDInsight w tej samej sieci wirtualnej na platformie Azure w programie SQL Server, umożliwia kroki opisane w tym artykule importowanie i eksportowanie danych do bazy danych programu SQL Server.
  
  > [!NOTE]  
  > HDInsight obsługuje tylko na podstawie lokalizacji sieci wirtualne, a jego obecnie nie współpracujesz z sieci wirtualne oparte na grupy koligacji.

  
  * Aby utworzyć i skonfigurować sieć wirtualną, zobacz [Utwórz sieć wirtualną przy użyciu witryny Azure portal](../../virtual-network/quick-create-portal.md).
    
    * Jeśli używasz programu SQL Server w centrum danych, należy skonfigurować sieci wirtualnej co *site-to-site* lub *point-to-site*.
      
      > [!NOTE]  
      > Dla **point-to-site** sieci wirtualne programu SQL Server musi być uruchomiona klienta sieci VPN konfiguracji aplikacji, które są dostępne z **pulpit nawigacyjny** konfiguracji sieci wirtualnej platformy Azure.


    * Używając programu SQL Server na maszynie wirtualnej platformy Azure, żadnej konfiguracji sieci wirtualnej można Jeśli maszyna wirtualna, hostowany program SQL Server jest członkiem tej samej sieci wirtualnej jako HDInsight.
  * Aby utworzyć klaster usługi HDInsight w sieci wirtualnej, zobacz [klastrów utworzyć Apache Hadoop w HDInsight przy użyciu opcji niestandardowych](../hdinsight-hadoop-provision-linux-clusters.md)
    
    > [!NOTE]  
    > Program SQL Server, należy także zezwolić uwierzytelniania. Wykonanie czynności opisanych w tym artykule, należy użyć identyfikatora logowania programu SQL Server.


**Aby zweryfikować konfigurację**

1. Otwórz grupę zasobów w witrynie Azure portal. Zostanie wyświetlona cztery zasoby w grupie:

    - klastra
    - Serwer bazy danych
    - Baza danych
    - domyślne konto magazynu

2. Otwórz bazę danych w programie Microsoft SQL Server Management Studio.  Zobaczysz dwie bazy danych, wdrożeniu:

    ![Usługa Azure HDInsight Sqoop SQL Management Studio](./media/hdinsight-use-sqoop/hdinsight-sqoop-sql-management-studio.png)


## <a name="run-sqoop-jobs"></a>Uruchamianie zadań narzędzia Sqoop
HDInsight można uruchomić zadania Sqoop przy użyciu różnych metod. Skorzystaj z poniższej tabeli do określania, która metoda jest odpowiedni dla Ciebie, a następnie kliknij link, aby uzyskać wskazówki.

| **Użyj tej** Jeśli chcesz... | ...an **interakcyjne** powłoki | ...**partii** przetwarzania | ...zwykle to **systemu operacyjnego klastra** | ...from to **system operacyjny klienta** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux |Linux, Unix, Mac OS X lub Windows |
| [Zestaw SDK dla platformy .NET usługi Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |? |System Linux lub Windows |Windows (na razie) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |System Linux lub Windows |Windows |

## <a name="limitations"></a>Ograniczenia
* Zbiorcze export - HDInsight opartych na systemie Linux za pomocą, łącznik Sqoop, używany do eksportowania danych do programu Microsoft SQL Server lub usługi Azure SQL Database nie obsługuje obecnie zbiorcze operacje wstawiania.
* Przetwarzanie wsadowe — za pomocą HDInsight opartych na systemie Linux, korzystając z `-batch` przełącznika podczas wykonywania operacji wstawienia, Sqoop wykonuje wiele operacji wstawiania zamiast przetwarzanie wsadowe operacji wstawiania.

## <a name="next-steps"></a>Kolejne kroki
Teraz masz pokazaliśmy, jak przy użyciu narzędzia Sqoop. Aby dowiedzieć się więcej, zobacz:

* [Use Apache Hive z HDInsight](../hdinsight-use-hive.md)
* [Apache Pig za pomocą HDInsight](../hdinsight-use-pig.md)
* [Przekazywanie danych do HDInsight][hdinsight-upload-data]: Znajdź inne metody przekazywania danych do usługi HDInsight/Azure Blob storage.

## <a name="appendix-a---a-powershell-sample"></a>Dodatek A — przykład programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Przykładowy skrypt programu PowerShell wykonuje następujące czynności:

1. Łączenie z platformą Azure.
2. Utwórz grupę zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](../../azure-resource-manager/manage-resource-groups-powershell.md)
3. Tworzenie serwera usługi Azure SQL Database, Azure SQL database i dwie tabele. 
   
    Jeśli zamiast tego użyj programu SQL Server należy użyć następujących instrukcji, do tworzenia tabel:
   
        CREATE TABLE [dbo].[log4jlogs](
         [t1] [nvarchar](50),
         [t2] [nvarchar](50),
         [t3] [nvarchar](50),
         [t4] [nvarchar](50),
         [t5] [nvarchar](50),
         [t6] [nvarchar](50),
         [t7] [nvarchar](50))
   
        CREATE TABLE [dbo].[mobiledata](
         [clientid] [nvarchar](50),
         [querytime] [nvarchar](50),
         [market] [nvarchar](50),
         [deviceplatform] [nvarchar](50),
         [devicemake] [nvarchar](50),
         [devicemodel] [nvarchar](50),
         [state] [nvarchar](50),
         [country] [nvarchar](50),
         [querydwelltime] [float],
         [sessionid] [bigint],
         [sessionpagevieworder][bigint])
   
    Najprostszym sposobem badać bazy danych i tabel jest przy użyciu programu Visual Studio. Serwer bazy danych i bazy danych może być badane za pomocą witryny Azure portal.
4. Tworzenie klastra usługi HDInsight.
   
    Aby zbadać klastra, można użyć witryny Azure portal lub programu Azure PowerShell.
5. Wstępne przetwarzanie źródłowego pliku danych.
   
    W tym samouczku są eksportowane (rozdzielany plik) pliku dziennika log4j i tabeli programu Hive do usługi Azure SQL database. Rozdzielany plik nosi */example/data/sample.log*. Wcześniej w tym samouczku pokazano kilka przykłady dzienników log4j. W pliku dziennika istnieją pewne puste wiersze i wiersze podobne do następujących:
   
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
   
    Jest to poprawne dla innych przykładów, które używają tych danych, ale firma Microsoft możemy można zaimportować do bazy danych Azure SQL database lub SQL Server należy usunąć te wyjątki. Sqoop eksportu zakończy się niepowodzeniem, jeśli jest ciągiem pustym ani linii z mniejszą element niż liczba pól zdefiniowanych w tabeli bazy danych Azure SQL. Tabela log4jlogs zawiera siedem pola typu string.
   
    Ta procedura powoduje utworzenie nowego pliku w klastrze: tutorials/usesqoop/data/sample.log. Aby zbadać plików zmodyfikowanych danych, można użyć witryny Azure portal, Eksploratora usługi Azure Storage lub Azure PowerShell. [Rozpoczynanie pracy z usługą HDInsight] [ hdinsight-get-started] zawiera przykład kodu służącego do pobierania pliku i wyświetlić zawartość pliku przy użyciu programu Azure PowerShell.
6. Wyeksportuj plik danych do bazy danych Azure SQL.
   
    Plik źródłowy jest tutorials/usesqoop/data/sample.log. Tabela, której dane są eksportowane do nosi nazwę log4jlogs.
   
   > [!NOTE]  
   > Inne niż informacje o parametrach połączenia kroki opisane w tej sekcji powinny działać dla usługi Azure SQL database lub SQL Server. Kroki te zostały przetestowane przy użyciu następującej konfiguracji:
   > 
   > * **Konfiguracja punktu do lokacji sieci wirtualnej platformy Azure**: Sieć wirtualna połączona klastra HDInsight z programem SQL Server w prywatnym centrum danych. Zobacz [konfigurowania sieci VPN punkt-lokacja w portalu zarządzania](../../vpn-gateway/vpn-gateway-point-to-site-create.md) Aby uzyskać więcej informacji.
   > * **Usługa Azure HDInsight**: Zobacz [Tworzenie klastrów usługi Hadoop w HDInsight przy użyciu opcji niestandardowych](../hdinsight-hadoop-provision-linux-clusters.md) informacji o tworzeniu klastra w sieci wirtualnej.
   > * **SQL Server 2014**: Skonfigurowane i umożliwiają uwierzytelnianie i uruchomienia klienta sieci VPN pakiet konfiguracji, aby bezpiecznie połączyć się z siecią wirtualną.
   > 
   > 
7. Eksportowanie tabeli programu Hive w bazie danych Azure SQL.
8. Importowanie tabeli mobiledata z klastrem HDInsight.
   
    Aby zbadać plików zmodyfikowanych danych, można użyć witryny Azure portal, Eksploratora usługi Azure Storage lub Azure PowerShell.  [Rozpoczynanie pracy z usługą HDInsight] [ hdinsight-get-started] zawiera przykładowy kod o pobranie pliku i wyświetlić zawartość pliku przy użyciu programu Azure PowerShell.

### <a name="the-powershell-sample"></a>Przykładowy skrypt programu PowerShell

```powershell
# Prepare an Azure SQL database to be used by the Sqoop tutorial

#region - provide the following values

$subscriptionID = "<Enter your Azure Subscription ID>"

$sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
$sqlDatabasePassword = "<Enter a Password>"

$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = "<Enter a Password>"

$sshUserName = "sshuser" #HDInsight ssh username
$sshPassword = $httpPassword 

# used for creating Azure service names
$nameToken = "<Enter an alias>" 
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
#endregion

#region - variables

# Resource group variables
$resourceGroupName = $namePrefix + "rg"
$location = "East US 2" # used by all Azure services defined in this tutorial

# SQL database varialbes
$sqlDatabaseServerName = $namePrefix + "sqldbserver"
$sqlDatabaseName = $namePrefix + "sqldb"
$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
$sqlDatabaseMaxSizeGB = 10

# Used for retrieving external IP address and creating firewall rules
$ipAddressRestService = "https://bot.whatismyipaddress.com"
$fireWallRuleName = "UseSqoop"

# Used for creating tables and clustered indexes
$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
    [t1] [nvarchar](50),
    [t2] [nvarchar](50),
    [t3] [nvarchar](50),
    [t4] [nvarchar](50),
    [t5] [nvarchar](50),
    [t6] [nvarchar](50),
    [t7] [nvarchar](50))"

$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"

$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder][bigint])"

$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

# HDInsight variables
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName
#endregion

# Treat all errors as terminating
$ErrorActionPreference = "Stop"

#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzContext}
catch{Connect-AzAccount}
#endregion

#region - Create Azure resource group
Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
try{
    Get-AzResourceGroup -Name $resourceGroupName
}
catch{
    New-AzResourceGroup -Name $resourceGroupName -Location $location
}
#endregion

#region - Create Azure SQL database server
Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
try{
    Get-AzSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
catch{
    Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

    $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

    $sqlDatabaseServerName = (New-AzSqlServer `
                                -ResourceGroupName $resourceGroupName `
                                -ServerName $sqlDatabaseServerName `
                                -SqlAdministratorCredentials $credential `
                                -Location $location).ServerName
    Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan

    Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
    $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
    New-AzSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-workstation" `
        -StartIpAddress $workstationIPAddress `
        -EndIpAddress $workstationIPAddress

    #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
    #Note that this allows Azure traffic from any Azure subscription to access the server.
    New-AzSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-Azureservices" `
        -StartIpAddress "0.0.0.0" `
        -EndIpAddress "0.0.0.0"
}

#endregion

#region - Create and validate Azure SQL database
Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green

try {
    Get-AzSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName
}
catch {
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
    New-AzSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName `
        -Edition "Standard" `
        -RequestedServiceObjectiveName "S1"
}

#endregion

#region - Create tables
Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green

$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = $sqlDatabaseConnectionString
$conn.Open()

# Create the log4jlogs table and index
$cmd = New-Object System.Data.SqlClient.SqlCommand
$cmd.Connection = $conn
$cmd.CommandText = $cmdCreateLog4jTable
$ret = $cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateLog4jClusteredIndex
$cmd.ExecuteNonQuery()

# Create the mobiledata table and index
$cmd.CommandText = $cmdCreateMobileTable
$cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
$cmd.ExecuteNonQuery()

$conn.close()

#endregion


#region - Create HDInsight cluster

Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green

# Create the default storage account
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -Type Standard_LRS

# Create the default Blob container
$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
$defaultStorageAccountContext = New-AzStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey 
New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageAccountContext 

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $HDInsightClusterName `
    -Location $location `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version 3.6 `
    -ClusterSizeInNodes 2 `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $defaultBlobContainerName  

# Validate the cluster
Get-AzHDInsightCluster -ClusterName $hdinsightClusterName
#endregion

#region - pre-process the source file

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

# This procedure creates a new file with $destBlobName
$sourceBlobName = "example/data/sample.log"
$destBlobName = "tutorials/usesqoop/data/sample.log"

# Define the connection string
$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

# Create block blob objects referencing the source and destination blob.
$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
$storageClient = $storageAccount.CreateCloudBlobClient();
$storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

# Define a MemoryStream and a StreamReader for reading from the source file
$stream = New-Object System.IO.MemoryStream
$stream = $sourceBlob.OpenRead()
$sReader = New-Object System.IO.StreamReader($stream)

# Define a MemoryStream and a StreamWriter for writing into the destination file
$memStream = New-Object System.IO.MemoryStream
$writeStream = New-Object System.IO.StreamWriter $memStream

# Pre-process the source blob
$exString = "java.lang.Exception:"
while(-Not $sReader.EndOfStream){
    $line = $sReader.ReadLine()
    $split = $line.Split(" ")

    # remove the "java.lang.Exception" from the first element of the array
    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
    if ($split[0] -eq $exString){
        #create a new ArrayList to remove $split[0]
        $newArray = [System.Collections.ArrayList] $split
        $newArray.Remove($exString)

        # update $split and $line
        $split = $newArray
        $line = $newArray -join(" ")
    }

    # remove the lines that has less than 7 elements
    if ($split.count -ge 7){
        write-host $line
        $writeStream.WriteLine($line)
    }
}

# Write to the destination blob
$writeStream.Flush()
$memStream.Seek(0, "Begin")
$destBlob.UploadFromStream($memStream)

#endregion

#region - export a log file from the cluster to the SQL database

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

$tableName_log4j = "log4jlogs"

# Connection string for Azure SQL Database.
# Comment if using SQL Server
$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
# Connection string for SQL Server.
# Uncomment if using SQL Server.
#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"

$exportDir_log4j = "/tutorials/usesqoop/data"

# Submit a Sqoop job
$sqoopDef = New-AzHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
$sqoopJob = Start-AzHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose
Wait-AzHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
Write-Host "Standard Output" -BackgroundColor Green
Get-AzHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput

#endregion

#region - export a Hive table

$tableName_mobile = "mobiledata"
$exportDir_mobile = "/hive/warehouse/hivesampletable"

$sqoopDef = New-AzHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
$sqoopJob = Start-AzHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion

#region - import a database

$targetDir_mobile = "/tutorials/usesqoop/importeddata/"

$sqoopDef = New-AzHDInsightSqoopJobDefinition `
    -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

$sqoopJob = Start-AzHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion
```


[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: ../hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../../sql-database/sql-database-get-started.md
[sqldatabase-create-configure]: ../../sql-database/sql-database-get-started.md

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: https://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
