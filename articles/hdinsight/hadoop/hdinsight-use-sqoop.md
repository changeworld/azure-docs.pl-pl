---
title: Uruchamianie zadań Apache Sqoop w usłudze Azure HDInsight (Hadoop) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać programu Azure PowerShell na stacji roboczej uruchom Sqoop importowania i eksportowania między klastrem Hadoop i bazy danych Azure SQL.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
ms.assetid: 2fdcc6b7-6ad5-4397-a30b-e7e389b66c7a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: 0e72585906b972c3d7ffb2513fceb27e3e8bc1c5
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="use-sqoop-with-hadoop-in-hdinsight"></a>Używanie Sqoop z platformą Hadoop w usłudze HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Dowiedz się, jak używać Sqoop w usłudze HDInsight umożliwia importowanie i eksportowanie między klastrem usługi HDInsight i bazy danych Azure SQL lub bazy danych SQL Server.

Mimo że Hadoop to fizyczne wybór przetwarzanie częściową strukturą i bez struktury danych, takie jak dzienniki i pliki, może również być potrzebne do przetwarzania danych strukturalnych, który jest przechowywany w relacyjnych baz danych.

[Sqoop] [ sqoop-user-guide-1.4.4] to narzędzie przeznaczone do transferu danych między klastrów platformy Hadoop a relacyjnymi bazami danych. Można go użyć do importowania danych z systemu zarządzania relacyjnymi bazami danych (RDBMS), takie jak SQL Server, MySQL lub Oracle w systemie plików usługi Hadoop distributed (HDFS), Przekształć dane w platformy Hadoop za pomocą MapReduce lub Hive, a następnie wyeksportować dane do RDBMS. W tym samouczku używasz bazy danych programu SQL Server relacyjnej bazy danych.

Dla wersji Sqoop, które są obsługiwane w klastrach HDInsight, zobacz [nowości w wersjach klastra dostarczanych z usługą HDInsight?][hdinsight-versions]

## <a name="understand-the-scenario"></a>Zrozumienie tego scenariusza

Klaster usługi HDInsight jest dostarczany z przykładowymi danymi. Można użyć następujących dwóch próbek:

* Plik dziennika log4j, który znajduje się pod adresem */example/data/sample.log*. Następujące dzienniki są wyodrębniane z pliku:
  
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
* Tabeli programu Hive o nazwie *hivesampletable*, który znajduje się w pliku danych odwołuje się do */hive/warehouse/hivesampletable*. Tabela zawiera niektóre dane z urządzeń przenośnych. 
  
  | Pole | Typ danych |
  | --- | --- |
  | ClientID |ciąg |
  | querytime |ciąg |
  | rynku |ciąg |
  | deviceplatform |ciąg |
  | devicemake |ciąg |
  | devicemodel |ciąg |
  | state |ciąg |
  | Kraju |ciąg |
  | querydwelltime |O podwójnej precyzji |
  | Identyfikator sesji |bigint |
  | sessionpagevieworder |bigint |

W tym samouczku te dwa zestawy danych służy do testowania Sqoop importu i eksportu.

## <a name="create-cluster-and-sql-database"></a>Tworzenie klastra i bazy danych SQL
W tej sekcji przedstawiono sposób tworzenia klastra, bazy danych SQL i schematów bazy danych SQL do uruchamiania tego samouczka przy użyciu portalu Azure i szablonu usługi Azure Resource Manager. Szablon można znaleźć w [szablonów Szybki Start Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Szablon usługi Resource Manager wymaga pliku bacpac pakiet do wdrożenia schematy tabeli z bazą danych SQL.  Pakiet pliku bacpac znajduje się w publicznym kontenerze obiektów blob, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Jeśli chcesz użyć kontenera prywatne dla pliku bacpac plików, użyj następujących wartości w szablonie:
   
```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

Jeśli wolisz korzystać z programu Azure PowerShell do tworzenia klastra i bazy danych SQL, zobacz [dodatek a](#appendix-a---a-powershell-sample).

> [!NOTE]
> Importowanie przy użyciu szablonu lub portalu Azure obsługuje wyłącznie Importowanie pliku BACPAC plik z magazynu obiektów blob platformy Azure.

**Aby skonfigurować środowisko przy użyciu szablonu administracyjnego zasobów**
1. Kliknij poniższy obraz, aby otworzyć szablon Menedżera zasobów w portalu Azure.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
2. Wprowadź następujące właściwości:

    - **Subskrypcja**: Wprowadź subskrypcji platformy Azure.
    - **Grupa zasobów**: Utwórz nową grupę zasobów platformy Azure, lub wybierz istniejącą grupę zasobów.  Grupa zasobów to w celu zarządzania.  Jest to kontener dla obiektów.
    - **Lokalizacja**: Wybierz region.
    - **ClusterName**: Wprowadź nazwę klastra usługi Hadoop.
    - **Nazwa logowania i hasło klastra**: domyślna nazwa logowania to admin.
    - **Nazwa użytkownika i hasło SSH**.
    - **Nazwa logowania serwera i hasło bazy danych SQL**.
    - **_artifacts lokalizacji**: Użyj wartości domyślnej, chyba że chcesz użyć pliku backpac w innej lokalizacji.
    - **Token sygnatury dostępu współdzielonego lokalizacji _artifacts**: pozostaw to pole puste.
    - **Nazwa pliku pliku Bacpac**: Użyj wartości domyślnej, chyba że chcesz użyć pliku backpac.
     
        Zapisane na stałe w sekcji zmiennych są następujące wartości:
        
        |Name (Nazwa)|Wartość|
        |----|-----|
        | Domyślna nazwa konta magazynu | &lt;CluterName > przechowywania |
        | Nazwa serwera bazy danych SQL Azure | &lt;ClusterName>dbserver |
        | Nazwa bazy danych SQL Azure | &lt;ClusterName>db |
     
3. Wybierz **akceptuję warunki i postanowienia, o których wspomniano**.
4. Kliknij pozycję **Kup**. Zostanie wyświetlony nowy Kafelek zatytułowany Submitting deployment dla wdrożenia szablonu. Utworzenie klastra i bazy danych SQL trwa około 20 minut.

Jeśli chcesz użyć istniejącej bazy danych Azure SQL lub programu Microsoft SQL Server

* **Baza danych SQL Azure**: należy skonfigurować reguły zapory dla serwera bazy danych Azure SQL, aby umożliwić dostęp ze stacji roboczej. Aby uzyskać instrukcje dotyczące tworzenia bazy danych Azure SQL i konfigurowania zapory, zobacz [rozpocząć korzystanie z bazy danych Azure SQL][sqldatabase-get-started]. 
  
  > [!NOTE]
  > Domyślnie bazy danych Azure SQL umożliwia połączenia z usługami Azure, takich jak Azure HDInsight. Wyłączenie tego ustawienia zapory, należy ją włączyć w portalu Azure. Aby uzyskać instrukcje dotyczące tworzenia bazy danych Azure SQL i konfigurowania reguł zapory, zobacz [tworzenie i Konfigurowanie bazy danych SQL][sqldatabase-create-configue].
  > 
  > 
* **SQL Server**: z klastrem usługi HDInsight znajduje się w tej samej sieci wirtualnej na platformie Azure jako serwera SQL, można użyć kroki opisane w tym artykule, umożliwia importowanie i eksportowanie danych do bazy danych programu SQL Server.
  
  > [!NOTE]
  > HDInsight obsługuje tylko na podstawie lokalizacji sieci wirtualnych, a jego obecnie nie współpracujesz z sieci wirtualne oparte na grupach koligacji.
  > 
  > 
  
  * Aby utworzyć i skonfigurować sieć wirtualną, zobacz [utworzyć sieć wirtualną przy użyciu portalu Azure](../../virtual-network/quick-create-portal.md).
    
    * Jeśli używasz programu SQL Server w centrum danych, należy skonfigurować sieci wirtualnej co *lokacja lokacja* lub *punkt lokacja*.
      
      > [!NOTE]
      > Dla **punkt lokacja** sieci wirtualnych, programu SQL Server musi być uruchomiona klienta sieci VPN konfiguracji aplikacji, które są dostępne z **pulpitu nawigacyjnego** konfiguracji sieci wirtualnej platformy Azure.
      > 
      > 
    * Używając programu SQL Server na maszynie wirtualnej platformy Azure, żadnej konfiguracji sieci wirtualnej umożliwia maszynie wirtualnej hostowany program SQL Server jest członkiem tej samej sieci wirtualnej jako HDInsight.
  * Aby utworzyć klaster usługi HDInsight w sieci wirtualnej, zobacz [klastrów utworzyć Hadoop w HDInsight przy użyciu niestandardowych opcji](../hdinsight-hadoop-provision-linux-clusters.md)
    
    > [!NOTE]
    > SQL Server należy także zezwolić uwierzytelniania. Aby wykonać kroki opisane w tym artykule, należy użyć identyfikatora logowania programu SQL Server.
    > 
    > 

**Aby zweryfikować konfigurację**

1. Otwórz grupę zasobów w portalu Azure. Zostanie wyświetlona cztery zasoby w grupie:

    - klastra
    - Serwer bazy danych
    - bazy danych
    - domyślne konto magazynu

2. Otwórz bazę danych w programie Microsoft SQL Server Management Studio.  Powinny zostać wyświetlone dwa baz danych wdrożonych:

    ![Azure HDInsight Sqoop SQL Management Studio](./media/hdinsight-use-sqoop/hdinsight-sqoop-sql-management-studio.png)


## <a name="run-sqoop-jobs"></a>Uruchamianie zadań Sqoop
HDInsight można uruchamiać zadania Sqoop przy użyciu różnych metod. Skorzystaj z poniższej tabeli do określania, która metoda jest odpowiednie dla Ciebie, a następnie kliknij link, aby uzyskać wskazówki.

| **Użyj tej** Jeśli chcesz... | ...an **interakcyjne** powłoki | ...**partii** przetwarzania | ...zwykle to **systemu operacyjnego klastra** | ...from to **system operacyjny klienta** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X lub systemu Windows |
| [Zestaw SDK dla platformy .NET usługi Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |✔ |Linux lub Windows |Systemu Windows (na razie) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |✔ |Linux lub Windows |Windows |

## <a name="limitations"></a>Ograniczenia
* Zbiorcze export - opartych na systemie Linux z usługi HDInsight, łącznik Sqoop, używany do eksportowania danych do programu Microsoft SQL Server lub bazy danych SQL Azure nie obsługuje obecnie zbiorcze operacje wstawiania.
* Przetwarzanie wsadowe — z opartą na systemie Linux usługą HDInsight przy użyciu `-batch` przełączyć podczas wykonywania operacji wstawienia, Sqoop wykonuje wiele operacji wstawienia zamiast przetwarzanie wsadowe operacji insert.

## <a name="next-steps"></a>Kolejne kroki
Teraz ma przedstawiono sposób używania Sqoop. Aby dowiedzieć się więcej, zobacz:

* [Korzystanie z programu Hive z usługą HDInsight](../hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą HDInsight](../hdinsight-use-pig.md)
* [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data]: znajdowanie innych metod do przekazywania danych do magazynu obiektów Blob HDInsight/Azure.

## <a name="appendix-a---a-powershell-sample"></a>Dodatek a. — przykład środowiska PowerShell
Przykładowe PowerShell wykonuje następujące czynności:

1. Połączenia z platformą Azure.
2. Utwórz grupę zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](../../azure-resource-manager/powershell-azure-resource-manager.md)
3. Utwórz serwer bazy danych SQL Azure, bazy danych Azure SQL i dwie tabele. 
   
    Jeśli zamiast tego użyj programu SQL Server umożliwia tworzenie tabel następujące instrukcje:
   
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
   
    Najprostszym sposobem Sprawdź, czy baza danych i tabele jest używać programu Visual Studio. Serwer bazy danych i bazy danych można zbadać za pomocą portalu Azure.
4. Tworzenie klastra usługi HDInsight.
   
    Aby zbadać klastra, można użyć portalu Azure lub programu Azure PowerShell.
5. Wstępnie przetworzyć plik źródła danych.
   
    W tym samouczku możesz wyeksportować plik dziennika narzędzia log4j (rozdzielany plik) i tabeli programu Hive z bazą danych Azure SQL. Rozdzielany plik jest nazywany */example/data/sample.log*. Kilka przykładów log4j dzienników widać wcześniej w samouczku. W pliku dziennika istnieją pewne puste wiersze i wiersze podobne do następujących:
   
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
   
    Jest to poprawnie inne przykłady korzystających z tych danych, ale możemy było, należy usunąć te wyjątki można zaimportować do bazy danych Azure SQL lub programu SQL Server. Eksport Sqoop zakończy się niepowodzeniem, jeśli jest ciągiem pustym ani wiersza z mniejszą element niż liczba pól zdefiniowanych w tabeli bazy danych Azure SQL. Tabela log4jlogs zawiera siedem pola typu ciąg.
   
    Ta procedura tworzy nowy plik w klastrze: tutorials/usesqoop/data/sample.log. Do modyfikacji danych w pliku można użyć portalu Azure, narzędzia do Eksploratora magazynu Azure lub programu Azure PowerShell. [Rozpoczynanie pracy z usługą HDInsight] [ hdinsight-get-started] zawiera przykładowy kod do pobierania pliku i wyświetlić zawartość pliku przy użyciu programu Azure PowerShell.
6. Eksportuj plik danych do bazy danych Azure SQL.
   
    Plik źródłowy jest tutorials/usesqoop/data/sample.log. Tabela, w którym dane są eksportowane do nosi nazwę log4jlogs.
   
   > [!NOTE]
   > Inne niż informacje o parametrach połączenia kroki opisane w tej sekcji powinny działać dla bazy danych Azure SQL lub programu SQL Server. Kroki te zostały przetestowane przy użyciu następującej konfiguracji:
   > 
   > * **Konfiguracja punktu do lokacji sieci wirtualnej platformy Azure**: sieci wirtualnej połączenia klastra usługi HDInsight do programu SQL Server w prywatnym centrum danych. Zobacz [skonfigurowania sieci VPN punkt-lokacja w portalu zarządzania](../../vpn-gateway/vpn-gateway-point-to-site-create.md) Aby uzyskać więcej informacji.
   > * **Usługa Azure HDInsight**: zobacz [klastrów utworzyć Hadoop w HDInsight przy użyciu niestandardowych opcji](../hdinsight-hadoop-provision-linux-clusters.md) informacji o tworzeniu klastra w sieci wirtualnej.
   > * **SQL Server 2014**: skonfigurowanych umożliwia uwierzytelnianie i uruchamianie klienta VPN pakiet konfiguracji do nawiązania bezpiecznego sieci wirtualnej.
   > 
   > 
7. Eksportowanie tabeli programu Hive z bazą danych Azure SQL.
8. Importowanie tabeli mobiledata w klastrze usługi HDInsight.
   
    Do modyfikacji danych w pliku można użyć portalu Azure, narzędzia do Eksploratora magazynu Azure lub programu Azure PowerShell.  [Rozpoczynanie pracy z usługą HDInsight] [ hdinsight-get-started] ma próbki kodu o pobranie pliku i wyświetlić zawartość pliku za pomocą programu Azure PowerShell.

### <a name="the-powershell-sample"></a>Przykładowe programu PowerShell

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
$ipAddressRestService = "http://bot.whatismyipaddress.com"
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
try{Get-AzureRmContext}
catch{Connect-AzureRmAccount}
#endregion

#region - Create Azure resouce group
Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
try{
    Get-AzureRmResourceGroup -Name $resourceGroupName
}
catch{
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
}
#endregion

#region - Create Azure SQL database server
Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
try{
    Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
catch{
    Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

    $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

    $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                -ResourceGroupName $resourceGroupName `
                                -ServerName $sqlDatabaseServerName `
                                -SqlAdministratorCredentials $credential `
                                -Location $location).ServerName
    Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan

    Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
    $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
    New-AzureRmSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-workstation" `
        -StartIpAddress $workstationIPAddress `
        -EndIpAddress $workstationIPAddress

    #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
    #Note that this allows Azure traffic from any Azure subscription to access the server.
    New-AzureRmSqlServerFirewallRule `
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
    Get-AzureRmSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName
}
catch {
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
    New-AzureRMSqlDatabase `
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
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -Type Standard_LRS

# Create the default Blob container
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
$defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey 
New-AzureStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageAccountContext 

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

New-AzureRmHDInsightCluster `
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
Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
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
$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose
Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput

#endregion

#region - export a Hive table

$tableName_mobile = "mobiledata"
$exportDir_mobile = "/hive/warehouse/hivesampletable"

$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
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

$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
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
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../../sql-database/sql-database-get-started.md
[sqldatabase-create-configue]: ../../sql-database/sql-database-get-started.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
