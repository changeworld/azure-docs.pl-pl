---
title: Używanie technologii Oozie usługi Hadoop w HDInsight
description: Użyj programu Oozie usługi Hadoop w HDInsight, Usługa danych big data. Dowiedz się, jak zdefiniować przepływ pracy programu Oozie i przesłać zadanie usługi Oozie.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/25/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 25694a44b1474cccaf62ed5005566cdb5230b15c
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360864"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-in-hdinsight"></a>Za pomocą programu Apache Oozie Apache Hadoop do definiowania i uruchomić przepływ pracy w HDInsight
[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Dowiedz się, jak zdefiniować przepływ pracy i uruchomić przepływ pracy na HDInsight za pomocą programu Apache Oozie. Aby uzyskać informacje dotyczące koordynatora programu Oozie, zobacz [za pomocą usług opartych na czasie koordynatora programu Oozie Apache HDInsight][hdinsight-oozie-coordinator-time]. Aby poznać usługi Azure Data Factory, zobacz [Use Apache Pig i Apache Hive z usługą Data Factory][azure-data-factory-pig-hive].

Apache Oozie to system koordynacji/przepływ pracy, który zarządza zadaniami na platformie Hadoop. Jest zintegrowany ze stosem platformy Hadoop i obsługuje zadania platformy Hadoop dla Apache MapReduce, Apache Pig, Apache Hive i Apache Sqoop. Może również służyć do planowania zadań, które są specyficzne dla systemu, np. programów Java lub skryptów powłoki.

Przepływ pracy, który implementuje zgodnie z instrukcjami w tym samouczku zawiera dwie akcje:

![Diagram przepływu pracy][img-workflow-diagram]

1. Działanie programu Hive jest uruchamiany skrypt HiveQL, zliczania wystąpień każdego typu poziomu dziennika w pliku Apache Log4j. Każdy plik log4j składa się z wiersz pola, który zawiera pola [poziom dziennika], które zawiera typ i ważność, na przykład:
   
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
2. Akcja Sqoop eksportuje dane wyjściowe HiveQL do tabeli w bazie danych Azure SQL. Aby uzyskać więcej informacji na temat narzędzia Sqoop zobacz [Użyj Apache Sqoop z HDInsight][hdinsight-use-sqoop].

> [!NOTE]  
> Obsługiwane wersje programu Oozie w klastrach HDInsight, zobacz [nowości w wersjach klastra platformy Apache Hadoop, dostarczone przez HDInsight?] [hdinsight-versions].
> 
> 

### <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed rozpoczęciem tego samouczka, konieczne jest posiadanie następujących elementów:

* **Stacja robocza z programem Azure PowerShell**. 
  

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
  

## <a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Definiowanie przepływu pracy programu Oozie i powiązane skrypt HiveQL
Definicje przepływów pracy programu Oozie są zapisywane w hPDL (język definicji procesu XML). Domyślna nazwa pliku przepływu pracy jest *workflow.xml*. Poniżej znajduje się plik przepływu pracy, którego używasz w tym samouczku.
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
Istnieją dwie akcje zdefiniowane w przepływie pracy. Akcja do rozpoczęcia jest *RunHiveScript*. Jeśli akcja zostanie pomyślnie uruchomione, jest następnej akcji *RunSqoopExport*.

RunHiveScript ma kilka zmiennych. Możesz przekazać wartości, gdy prześlesz zadanie Oozie ze swojej stacji roboczej przy użyciu programu Azure PowerShell.

|Zmienne przepływu pracy|Opis|
|---|---|
|${jobTracker}|Określa adres URL śledzenia zadań usługi Hadoop. Użyj **jobtrackerhost:9010** w HDInsight w wersji 3.0 i 2.1.|
|${nameNode}|Określa adres URL węzła nazwa usługi Hadoop. Użyj domyślnego adresu systemu plików, na przykład *wasb: / /&lt;containerName&gt;\@&lt;storageAccountName&gt;. blob.core.windows.net*.|
|${queueName}|Określa nazwę kolejki, który zadanie jest przesyłany do usługi. Użyj **domyślne**.|

|Hive zmiennej akcji|Opis|
|---|---|
|${hiveDataFolder}|Określa katalog źródłowy dla polecenia Hive Create Table.|
|${hiveOutputFolder}|Określa folder wyjściowy dla instrukcji INSERT zastąpić.|
|${hiveTableName}|Określa nazwę tabeli programu Hive, która odwołuje się do plików danych log4j.|

|Sqoop zmiennej akcji|Opis|
|---|---|
|${sqlDatabaseConnectionString}|Określa parametry połączenia bazy danych Azure SQL.|
|${sqlDatabaseTableName}|Określa, gdzie dane są eksportowane do tabeli bazy danych Azure SQL.|
|${hiveOutputFolder}|Określa folder wyjściowy dla instrukcji Hive Wstaw zastąpić. To ten sam folder eksportu Sqoop (export-dir).|

Aby uzyskać więcej informacji o przepływie pracy programu Oozie i przy użyciu akcji przepływu pracy, zobacz [dokumentację Apache Oozie 4.0] [ apache-oozie-400] (w przypadku HDInsight w wersji 3.0 lub nowszej) lub [dokumentację Apache Oozie 3.3.2] [ apache-oozie-332] (w przypadku HDInsight w wersji 2.1).

Działanie programu Hive w przepływie pracy wywołuje plik skryptu HiveQL. Ten plik skryptu zawiera trzy instrukcje HiveQL:

    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

1. **Instrukcja DROP TABLE** usuwa tabelę programu Hive log4j, jeśli taki istnieje.
2. **Wykonywanie instrukcji CREATE TABLE** tworzy tabelę zewnętrznych log4j Hive, który wskazuje na lokalizację pliku dziennika log4j. Ogranicznik pola jest ",". Ogranicznik wiersza domyślną jest "\n". Zewnętrzne tabeli programu Hive jest używana w celu uniknięcia pliku danych usuwany z oryginalnej lokalizacji, jeśli chcesz uruchomić przepływ pracy Oozie wiele razy.
3. **Instrukcja INSERT zastąpić** liczby wystąpień każdego typu poziomu dziennika z tabeli programu Hive log4j i zapisuje dane wyjściowe do obiektu blob w usłudze Azure Storage.

Istnieją trzy zmienne używane w skrypcie:

* ${hiveTableName}
* ${hiveDataFolder}
* ${hiveOutputFolder}

Plik definicji przepływu pracy (workflow.xml w ramach tego samouczka) przekazuje te wartości do tego skryptu HiveQL w czasie wykonywania.

Zarówno plik przepływu pracy, jak i plik HiveQL są przechowywane w kontenerze obiektów blob.  Skrypt programu PowerShell, których użyjesz w dalszej części tego samouczka kopiuje oba pliki, do domyślnego konta magazynu. 

## <a name="submit-oozie-jobs-using-powershell"></a>Przesyłanie zadań Oozie przy użyciu programu PowerShell
Program Azure PowerShell aktualnie nie zapewnia żadnych poleceń cmdlet do definiowania zadań Oozie. Możesz użyć **Invoke RestMethod** polecenia cmdlet do wywołania usługi sieci web programu Oozie. API usług sieci web programu Oozie jest JSON interfejsu API REST protokołu HTTP. Aby uzyskać więcej informacji o usługach sieci web programu Oozie interfejsu API, zobacz [dokumentację Apache Oozie 4.0] [ apache-oozie-400] (w przypadku HDInsight w wersji 3.0 lub nowszej) lub [dokumentację Apache Oozie 3.3.2] [ apache-oozie-332] (w przypadku HDInsight w wersji 2.1).

Skrypt programu PowerShell w tej sekcji wykonuje następujące czynności:

1. Łączenie z platformą Azure.
2. Utwórz grupę zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [użyciu programu Azure PowerShell z usługą Azure Resource Manager](../powershell-azure-resource-manager.md).
3. Tworzenie serwera usługi Azure SQL Database, Azure SQL database i dwie tabele. Są one używane przez akcję Sqoop w przepływie pracy.
   
    Nazwa tabeli jest *log4jLogCount*.
4. Utworzenie klastra usługi HDInsight używane do uruchamiania zadań programu Oozie.
   
    Aby zbadać klastra, można użyć witryny Azure portal lub programu Azure PowerShell.
5. Skopiuj plik przepływu pracy programu oozie oraz pliku skryptu HiveQL do domyślnego systemu plików.
   
    Oba pliki są przechowywane w publicznego kontenera obiektów Blob.
   
   * Skopiuj skrypt HiveQL (useoozie.hql) do usługi Azure Storage (wasb:///tutorials/useoozie/useoozie.hql).
   * Skopiuj workflow.xml wasb:///tutorials/useoozie/workflow.xml.
   * Skopiuj plik danych (/ example/data/sample.log) do wasb:///tutorials/useoozie/data/sample.log.
6. Prześlij zadanie Oozie.
   
    Badanie wyników zadania programu OOzie, należy użyć programu Visual Studio lub innych narzędzi do łączenia z usługą Azure SQL Database.

Oto skrypt.  Skrypt można uruchomić z programu Windows PowerShell ISE. Musisz skonfigurować zmienne pierwsze 7.
```powershell
    #region - provide the following values

    $subscriptionID = "<Enter your Azure subscription ID>"

    # SQL Database server login credentials used for creating and connecting
    $sqlDatabaseLogin = "<Enter SQL Database Login Name>"
    $sqlDatabasePassword = "<Enter SQL Database Login Password>"

    # HDInsight cluster HTTP user credential used for creating and connecting
    $httpUserName = "admin"  # The default name is "admin"
    $httpPassword = "<Enter HDInsight Cluster HTTP User Password>"

    # Used for creating Azure service names
    $nameToken = "<Enter an Alias>"
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
    catch{
        Connect-AzAccount
        Select-AzSubscription -SubscriptionId $subscriptionID
    }
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
        $sqlLoginCredentials = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

        $sqlDatabaseServerName = (New-AzSqlServer `
                                    -ResourceGroupName $resourceGroupName `
                                    -ServerName $sqlDatabaseServerName `
                                    -SqlAdministratorCredentials $sqlLoginCredentials `
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
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green

    try {
        Get-AzSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName
    }
    catch {
        New-AzSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName `
            -Edition "Standard" `
            -RequestedServiceObjectiveName "S1"
    }
    #endregion

    #region - Create SQL database tables
    Write-Host "Creating the log4jlogs table  ..." -ForegroundColor Green

    $sqlDatabaseTableName = "log4jLogsCount"
    $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
            [Level] [nvarchar](10) NOT NULL,
            [Total] float,
        CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
        (
        [Level] ASC
        )
        )"

    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = $sqlDatabaseConnectionString
    $conn.Open()

    # Create the log4jlogs table and index
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.Connection = $conn
    $cmd.CommandText = $cmdCreateLog4jCountTable
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
        -OSType Windows `
        -ClusterSizeInNodes 2 `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultBlobContainerName 

    # Validate the cluster
    Get-AzHDInsightCluster -ClusterName $hdinsightClusterName
    #endregion

    #region - copy Oozie workflow and HiveQL files

    Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green

    # Both files are stored in a public Blob
    $publicBlobContext = New-AzStorageContext -StorageAccountName "hditutorialdata" -Anonymous

    # WASB folder for storing the Oozie tutorial files.
    $destFolder = "tutorials/useoozie"  # Do NOT use the long path here

    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "useooziewf.hql"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/useooziewf.hql" `
        -Force

    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "workflow.xml"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/workflow.xml" `
        -Force

    #validate the copy
    Get-AzStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/workflow.xml

    Get-AzStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/useooziewf.hql

    #endregion

    #region - copy the sample.log file

    Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green

    Start-CopyAzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -SrcContainer $defaultBlobContainerName `
        -SrcBlob "example/data/sample.log"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -destBlob "$destFolder/data/sample.log" 

    #validate the copy
    Get-AzStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/data/sample.log

    #endregion

    #region - submit Oozie job

    $storageUri="wasb://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net"

    $oozieJobName = $namePrefix + "OozieJob"

    #Oozie WF variables
    $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
    $waitTimeBetweenOozieJobStatusCheck=10

    #Hive action variables
    $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
    $hiveTableName = "log4jlogs"
    $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
    $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

    #Sqoop action variables
    $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

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
        <value>$httpUserName</value>
    </property>

    <property>
        <name>oozie.wf.application.path</name>
        <value>$oozieWFPath</value>
    </property>

    </configuration>
    "@

    Write-Host "Checking Oozie server status..." -ForegroundColor Green
    $clusterUriStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/admin/status"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $httpCredential -OutVariable $OozieServerStatus

    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieServerStatus = $jsonResponse[0].("systemMode")
    Write-Host "Oozie server status is $oozieServerStatus."

    # create Oozie job
    Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
    Write-Host "`n--------`n$OoziePayload`n--------"
    $clusterUriCreateJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/jobs"
    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $httpCredential -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug

    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieJobId = $jsonResponse[0].("id")
    Write-Host "Oozie job id is $oozieJobId..."

    # start Oozie job
    Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
    $clusterUriStartJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $httpCredential | Format-Table -HideTableHeaders #-debug

    # get job status
    Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
    Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

    Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
    $clusterUriGetJobStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $JobStatus = $jsonResponse[0].("status")

    while($JobStatus -notmatch "SUCCEEDED|KILLED")
    {
        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")
        $jobStatus
    }

    Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green

    #endregion
```

**Aby ponownie uruchomić tego samouczka**

Aby ponownie uruchomić przepływ pracy, należy usunąć następujące elementy:

* Plik danych wyjściowych skryptu programu Hive
* Dane w tabeli log4jLogsCount

Poniżej przedstawiono przykładowy skrypt programu PowerShell, którego można używać:
```powershell
    $resourceGroupName = "<AzureResourceGroupName>"

    $defaultStorageAccountName = "<AzureStorageAccountName>"
    $defaultBlobContainerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServerName = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabasePassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
    $destContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    Remove-AzStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $defaultBlobContainerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()
```

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku pokazano, jak zdefiniować przepływ pracy programu Apache Oozie i sposób uruchamiania zadania programu Oozie przy użyciu programu PowerShell. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Za pomocą usług opartych na czasie koordynatora programu Oozie Apache HDInsight][hdinsight-oozie-coordinator-time]
* [Rozpoczynanie pracy przy użyciu technologii Apache Hadoop przy użyciu technologii Hive w HDInsight do analizowania użycia przenośnych słuchawki][hdinsight-get-started]
* [Usługi Azure Blob storage za pomocą HDInsight][hdinsight-storage]
* [Administrowanie HDInsight przy użyciu programu PowerShell][hdinsight-admin-powershell]
* [Przekazywanie danych na potrzeby zadań usługi Apache Hadoop w HDInsight][hdinsight-upload-data]
* [Przy użyciu technologii Apache Hadoop w HDInsight przy użyciu narzędzia Apache Sqoop][hdinsight-use-sqoop]
* [Apache Hive za pomocą technologii Apache Hadoop w HDInsight][hdinsight-use-hive]
* [Use Apache Pig z platformą Apache Hadoop w HDInsight][hdinsight-use-pig]
* [Opracowywanie programów MapReduce w języku Java dla HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: https://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md


[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[sqldatabase-get-started]: ../sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: https://hadoop.apache.org/
[apache-oozie-400]: https://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: https://oozie.apache.org/docs/3.3.2/

[powershell-download]: https://azure.microsoft.com/downloads/
[powershell-about-profiles]: https://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/library/ee176961.aspx

[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: https://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
