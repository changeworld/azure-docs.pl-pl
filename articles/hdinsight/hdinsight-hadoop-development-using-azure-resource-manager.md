---
title: Migracja do narzędzi usługi Azure Resource Manager dla HDInsight
description: Jak przeprowadzić migrację do usługi Azure Resource Manager narzędzia programistyczne dla klastrów HDInsight
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 501f215ae3daf24db6307b4f8afb0c7d3271d8a5
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361867"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrowanie do narzędzi programistycznych opartych na usłudze Azure Resource Manager w celu obsługi klastrów HDInsight

HDInsight wycofano narzędzi opartych na usłudze Azure Service Manager ASM for HDInsight. Jeśli masz doświadczenie z programu Azure PowerShell, klasyczny interfejs wiersza polecenia platformy Azure lub zestawu .NET SDK HDInsight do pracy z klastrami HDInsight, zachęcamy do używania w wersjach usługi Azure Resource Manager programu PowerShell, interfejsu wiersza polecenia i zestawu SDK platformy .NET w przyszłości. Ten artykuł zawiera wskaźniki dotyczące sposobu przeprowadzenia migracji do nowego podejścia opartego na usłudze Resource Manager. Wszędzie tam, gdzie ma to zastosowanie, w tym dokumencie wyróżniono różnice między metodami ASM i usługą Resource Manager dla HDInsight.

> [!IMPORTANT]  
> Pomoc techniczna dotycząca usługi ASM na podstawie programu PowerShell, interfejsu wiersza polecenia, i zestawu .NET SDK zostanie wycofana **1 stycznia 2017**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Migrowanie klasycznej wiersza polecenia platformy Azure do usługi Azure Resource Manager

> [!IMPORTANT]  
> Wiersza polecenia platformy Azure nie zapewnia pomocy technicznej do pracy z klastrami HDInsight. Nadal za pomocą klasyczny interfejs wiersza polecenia usługi Azure HDInsight, jednak klasyczny interfejs wiersza polecenia platformy Azure jest przestarzały.

Poniżej przedstawiono podstawowe polecenia do pracy z HDInsight za pośrednictwem klasycznego wiersza polecenia platformy Azure:

* `azure hdinsight cluster create` -Tworzy nowy klaster HDInsight
* `azure hdinsight cluster delete` -Usuwa istniejący klaster HDInsight
* `azure hdinsight cluster show` — Wyświetlanie informacji o istniejącego klastra
* `azure hdinsight cluster list` — Wyświetla listę klastry usługi HDInsight na potrzeby subskrypcji platformy Azure

Użyj `-h` przełącznik, aby sprawdzić parametry i przełączniki dostępne dla każdego polecenia.

### <a name="new-commands"></a>Nowe polecenia
Dostępne są następujące nowe polecenia dostępne w usłudze Azure Resource Manager:

* `azure hdinsight cluster resize` -dynamicznie zmienia liczbę węzłów procesu roboczego w klastrze
* `azure hdinsight cluster enable-http-access` — Włącza HTTPs dostęp do klastra (na domyślnie)
* `azure hdinsight cluster disable-http-access` — Wyłącza HTTPs dostęp do klastra
* `azure hdinsight script-action` -Zawiera polecenia służące do tworzenia/zarządzania akcji skryptów w klastrze
* `azure hdinsight config` -Zawiera polecenia służące do tworzenia pliku konfiguracji, które mogą być używane z `hdinsight cluster create` polecenie, aby podać informacje o konfiguracji.

### <a name="deprecated-commands"></a>Przestarzałych poleceń
Jeśli używasz `azure hdinsight job` polecenia, aby przesyłać zadania do klastra usługi HDInsight, te polecenia nie są dostępne za pomocą poleceń usługi Resource Manager. Jeśli zachodzi potrzeba programowo przesyłania zadań do HDInsight ze skryptów, należy w zamian użyć interfejsów API REST, dostarczonych przez HDInsight. Więcej informacji na temat przesyłanie zadań za pomocą interfejsów API REST na ten temat można znaleźć w następujących dokumentach.

* [Uruchamianie zadań MapReduce z usługą Hadoop w HDInsight przy użyciu programu cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Uruchamianie zapytania usługi Apache Hive przy użyciu technologii Apache Hadoop w HDInsight przy użyciu programu cURL](hadoop/apache-hadoop-use-hive-curl.md)
* [Wykonywanie zadań Apache Pig z usługą Apache Hadoop w HDInsight przy użyciu programu cURL](hadoop/apache-hadoop-use-pig-curl.md)

Aby uzyskać informacji na temat innych sposobów uruchomić interaktywnie, Apache Hadoop MapReduce, Apache Hive i Apache Pig, zobacz [używanie MapReduce z Hadoop w HDInsight](hadoop/hdinsight-use-mapreduce.md), [używanie programu Apache Hive przy użyciu technologii Apache Hadoop w HDInsight](hadoop/hdinsight-use-hive.md), i [korzystanie z technologii Apache Pig z platformą Apache Hadoop w HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Przykłady
**Tworzenie klastra**

* Stare polecenie (ASM) — `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nowe polecenie- `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Usuwanie klastra**

* Stare polecenie (ASM) — `azure hdinsight cluster delete myhdicluster`
* Nowe polecenie- `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Wyświetlanie listy klastrów**

* Stare polecenie (ASM) — `azure hdinsight cluster list`
* Nowe polecenie- `azure hdinsight cluster list`

> [!NOTE]  
> Dla polecenia listy określania grupy zasobów za pomocą `-g` zwróci tylko klastry w określonej grupie zasobów.

**Pokaż informacje o klastrze**

* Stare polecenie (ASM) — `azure hdinsight cluster show myhdicluster`
* Nowe polecenie- `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrowanie programu Azure PowerShell do usługi Azure Resource Manager
Informacje ogólne dotyczące programu Azure PowerShell w trybie usługi Azure Resource Manager znajduje się w temacie [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](../powershell-azure-resource-manager.md).

Poleceń cmdlet programu Azure PowerShell Resource Manager można zainstalować równolegle z poleceń cmdlet programu ASM. Polecenia cmdlet z dwóch trybów można rozróżnić przy użyciu ich nazw.  Tryb usługi Resource Manager ma *AzHDInsight* w nazwy poleceń cmdlet porównując do *AzureHDInsight* w trybie ASM.  Na przykład *New AzHDInsightCluster* programu vs. *New-AzureHDInsightCluster*. Parametry i przełączniki może mieć nazwy grup dyskusyjnych, i jest dostępnych wiele nowych parametrów przy użyciu usługi Resource Manager.  Na przykład kilka poleceń cmdlet wymaga nowego przełącznika o nazwie *- ResourceGroupName*. 

Zanim będzie można użyć polecenia cmdlet HDInsight, możesz nawiązać połączenie z kontem platformy Azure i Utwórz nową grupę zasobów:

* [Połącz AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Zmieniono nazwę polecenia cmdlet
Aby wyświetlić listę poleceń cmdlet usługi HDInsight ASM w konsoli środowiska Windows PowerShell:

    help *azurermhdinsight*

W poniższej tabeli wymieniono poleceń cmdlet programu ASM i ich nazwy w trybie usługi Resource Manager:

| Polecenia cmdlet usługi ASM | Polecenia cmdlet usługi Resource Manager |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzHDInsightConfigValues](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalues) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-AzHDInsightStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperties |[Get-AzHDInsightProperties](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightproperties) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzHDInsightHiveJob](https://docs.microsoft.com/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[New-AzHDInsightHiveJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzHDInsightMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[New-AzHDInsightPigJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzHDInsightSqoopJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzHDInsightStreamingMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Remove-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Use-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Wait-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Nowe polecenia cmdlet
Dostępne są następujące nowe polecenia cmdlet, które są dostępne tylko w trybie usługi Resource Manager. 

**Polecenia cmdlet związane z akcji skryptu:**

* **Get-AzHDInsightPersistedScriptAction**: Pobiera akcje utrwalonego skryptu dla klastra i wyświetli je w kolejności chronologicznej lub pobiera szczegóły określonego utrwalonego skryptu akcji. 
* **Get-AzHDInsightScriptActionHistory**: Pobiera Historia akcji skryptu dla klastra i wyświetla go w odwrotnej kolejności chronologicznej lub pobiera szczegóły akcji wykonanych wcześniej skryptu. 
* **Remove-AzHDInsightPersistedScriptAction**: Usuwa Akcja utrwalonego skryptu z klastra usługi HDInsight.
* **Set-AzHDInsightPersistedScriptAction**: Ustawia akcji skryptu poprzednio wykonanych akcji utrwalonego skryptu.
* **Submit-AzHDInsightScriptAction**: Przesyła nową akcję skryptu w klastrze Azure HDInsight. 

Użycie dodatkowych informacji, zobacz [HDInsight opartych na systemie Linux z Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

**Klastra polecenia cmdlet związane z tożsamościami:**

* **Add-AzHDInsightClusterIdentity**: Dodaje tożsamość klastra do obiektu konfiguracji klastra, co klaster HDInsight mogą uzyskiwać dostęp do usługi Azure Data Lake Storage. Zobacz [Tworzenie klastra usługi HDInsight przy użyciu usługi Data Lake Storage przy użyciu programu Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Przykłady
**Tworzenie klastra**

Stare polecenie (ASM): 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

Nowe polecenie:

    New-AzHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**Usuwanie klastra**

Stare polecenie (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Nowe polecenie:

    Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Lista klastra**

Stare polecenie (ASM):

    Get-AzureHDInsightCluster

Nowe polecenie:

    Get-AzHDInsightCluster 

**Pokaż klastra**

Stare polecenie (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Nowe polecenie:

    Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Inne przykłady
* [Tworzenie klastrów HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Przesyłanie zadań Apache Hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Przesyłanie zadań Apache Pig](hadoop/apache-hadoop-use-pig-powershell.md)
* [Przesyłanie zadań z wykorzystaniem narzędzia Apache Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrowanie do nowy zestaw .NET SDK usługi HDInsight
Zarządzanie usługami platformy Azure na podstawie [zestawu SDK platformy .NET HDInsight (ASM)](https://msdn.microsoft.com/library/azure/mt416619.aspx) jest już przestarzały. Zaleca się używać usługi Azure Resource Management na podstawie [opartych na usłudze Resource Manager HDInsight zestawu SDK platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight). Następujące pakiety na podstawie ASM HDInsight zostaną wycofane.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Ta sekcja zawiera łącza do dodatkowych informacji na temat sposobu wykonywania określonych zadań przy użyciu zestawu SDK opartych na usłudze Resource Manager.

| Jak... przy użyciu zestawu SDK HDInsight opartych na usłudze Resource Manager | Linki |
| --- | --- |
| Tworzenie klastrów HDInsight za pomocą zestawu SDK platformy .NET |Zobacz [HDInsight Tworzenie klastrów przy użyciu zestawu .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| Dostosowywanie klastra za pomocą akcji skryptu za pomocą zestawu SDK platformy .NET |Zobacz [HDInsight Linux Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Uwierzytelnianie aplikacji interaktywnie przy użyciu usługi Azure Active Directory przy użyciu zestawu .NET SDK |Zobacz [uruchamianie Apache zapytań Hive przy użyciu zestawu .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Fragment kodu, w tym artykule wykorzystano podejście przeprowadzić uwierzytelnianie interakcyjne. |
| Uwierzytelnianie aplikacji nieinterakcyjny przy użyciu usługi Azure Active Directory przy użyciu zestawu .NET SDK |Zobacz [tworzenia nieinterakcyjnych aplikacji na HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Przesyłanie zadania Apache Hive, przy użyciu zestawu .NET SDK |Zobacz [zadania przesyłania Apache Hive](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Przesyłanie zadania Apache Pig, przy użyciu zestawu .NET SDK |Zobacz [zadania przesyłania Apache Pig](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| Przesyłanie zadania Apache Sqoop przy użyciu zestawu .NET SDK |Zobacz [zadania przesyłania Apache Sqoop](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Lista klastrów HDInsight za pomocą zestawu SDK platformy .NET |Zobacz [klastrów HDInsight listy](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Skalowanie klastrów HDInsight za pomocą zestawu SDK platformy .NET |Zobacz [klastrów HDInsight skalowania](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Przydzielenia/odwołania dostępu do klastrów HDInsight za pomocą zestawu SDK platformy .NET |Zobacz [przydzielenia/odwołania dostępu do klastrów HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Aktualizowanie poświadczeń użytkownika HTTP dla klastrów HDInsight za pomocą zestawu SDK platformy .NET |Zobacz [poświadczeń użytkownika HTTP aktualizacji dla klastrów HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Znajdź domyślne konto magazynu dla klastrów HDInsight za pomocą zestawu SDK platformy .NET |Zobacz [znaleźć domyślne konto magazynu dla klastrów HDInsight](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Usuń z klastrami HDInsight przy użyciu zestawu SDK platformy .NET |Zobacz [klastrów HDInsight Usuń](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Przykłady
Poniżej przedstawiono kilka przykładów, w jaki sposób operacja jest wykonywane przy użyciu zestawu SDK programu ASM i fragment kodu równoważne dla zestawu SDK opartych na usłudze Resource Manager.

**Tworzenie klienta CRUD klastra**

* Stare polecenie (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Nowe polecenie (autoryzacji nazwy głównej usługi)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Nowe polecenie (Autoryzacja użytkownika)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Tworzenie klastra**

* Stare polecenie (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* Nowe polecenie
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**Włączanie dostępu HTTP**

* Stare polecenie (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Nowe polecenie
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**Usuwanie klastra**

* Stare polecenie (ASM)
  
        client.DeleteCluster(dnsName);
* Nowe polecenie
  
        client.Clusters.Delete(resourceGroup, dnsname);

