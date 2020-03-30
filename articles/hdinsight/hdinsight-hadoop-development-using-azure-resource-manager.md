---
title: Migrowanie do narzędzi usługi Azure Resource Manager dla usługi HDInsight
description: Jak przeprowadzić migrację do narzędzi programistycznych usługi Azure Resource Manager dla klastrów usługi HDInsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 76eb3a135f7a32a30cfa62546a644bc77cf39998
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934581"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrowanie do narzędzi programistycznych opartych na usłudze Azure Resource Manager dla klastrów usługi HDInsight

Usługa HDInsight przestarzałe jest narzędzia oparte na usłudze Azure Service Manager (ASM) dla usługi HDInsight. Jeśli używasz narzędzia Azure PowerShell, klasycznego interfejsu wiersza polecenia platformy Azure lub zestawu HDInsight .NET SDK do pracy z klastrami USŁUGI HDInsight, zachęcamy do korzystania z wersji usługi Azure Resource Manager programu PowerShell, CLI i .NET SDK w przyszłości. W tym artykule przedstawiono wskazówki dotyczące sposobu migracji do nowego podejścia opartego na Menedżerze zasobów. W stosownych przypadkach w tym dokumencie przedstawiono różnice między asm i menedżer zasobów podejścia do HDInsight.

> [!IMPORTANT]  
> Obsługa opartych na asmach powershell, cli i .NET SDK zostanie wstrzymana **1 stycznia 2017**r. .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Migrowanie klasycznego interfejsu wiersza polecenia platformy Azure do usługi Azure Resource Manager

> [!IMPORTANT]  
> Narzędzie interfejsu wiersza polecenia platformy Azure nie zapewnia obsługi pracy z klastrami USŁUGI HDInsight. Nadal można używać narzędzia Azure Classic CLI z funkcją HDInsight, jednak klasyczna interfejs wiersza polecenia platformy Azure jest przestarzała.

Poniżej przedstawiono podstawowe polecenia do pracy z programem HDInsight za pośrednictwem interfejsu wiersza polecenia platformy Azure CLassic:

* `azure hdinsight cluster create`- tworzy nowy klaster HDInsight
* `azure hdinsight cluster delete`- usuwa istniejący klaster HDInsight
* `azure hdinsight cluster show`- wyświetlanie informacji o istniejącym klastrze
* `azure hdinsight cluster list`- wyświetla listę klastrów HDInsight dla subskrypcji platformy Azure

Użyj `-h` przełącznika, aby sprawdzić parametry i przełączniki dostępne dla każdego polecenia.

### <a name="new-commands"></a>Nowe polecenia
Nowe polecenia dostępne w usłudze Azure Resource Manager to:

* `azure hdinsight cluster resize`- dynamicznie zmienia liczbę węzłów procesu roboczego w klastrze
* `azure hdinsight cluster enable-http-access`- umożliwia dostęp HTTPs do klastra (domyślnie włączony)
* `azure hdinsight cluster disable-http-access`- wyłącza dostęp HTTPs do klastra
* `azure hdinsight script-action`- udostępnia polecenia do tworzenia/zarządzania akcjami skryptów w klastrze
* `azure hdinsight config`- udostępnia polecenia do tworzenia pliku konfiguracyjnego, który może być używany z poleceniem `hdinsight cluster create` w celu dostarczenia informacji o konfiguracji.

### <a name="deprecated-commands"></a>Przestarzałe polecenia
Jeśli `azure hdinsight job` polecenia są używane do przesyłania zadań do klastra HDInsight, polecenia te nie są dostępne za pośrednictwem poleceń Menedżera zasobów. Jeśli chcesz programowo przesłać zadania do programu HDInsight ze skryptów, należy zamiast tego użyć interfejsów API REST dostarczonych przez program HDInsight. Aby uzyskać więcej informacji na temat przesyłania zadań przy użyciu interfejsów API REST, zobacz następujące dokumenty.

* [Uruchamianie zadań MapReduce z Hadoop na HDInsight przy użyciu cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Uruchamianie zapytań akwizyjnych z Apache Hadoop w programie HDInsight przy użyciu cURL](hadoop/apache-hadoop-use-hive-curl.md)


Aby uzyskać informacje na temat innych sposobów uruchamiania Apache Hadoop MapReduce, Apache Hive i Apache Pig interaktywnie, zobacz [Użyj MapReduce z Hadoop na HDInsight](hadoop/hdinsight-use-mapreduce.md), [Użyj Ul Apache z Apache Hadoop na HDInsight](hadoop/hdinsight-use-hive.md)i [Użyj Apache Pig z Apache Hadoop na HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Przykłady
**Tworzenie klastra**

* Stare polecenie (ASM) -`azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nowe polecenie -`azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Usuwanie klastra**

* Stare polecenie (ASM) -`azure hdinsight cluster delete myhdicluster`
* Nowe polecenie -`azure hdinsight cluster delete mycluster -g myresourcegroup`

**Listy klastrów**

* Stare polecenie (ASM) -`azure hdinsight cluster list`
* Nowe polecenie -`azure hdinsight cluster list`

> [!NOTE]  
> Dla polecenia listy określenie grupy zasobów `-g` przy użyciu zwróci tylko klastry w określonej grupie zasobów.

**Pokaż informacje o klastrze**

* Stare polecenie (ASM) -`azure hdinsight cluster show myhdicluster`
* Nowe polecenie -`azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrowanie usługi Azure PowerShell do usługi Azure Resource Manager
Ogólne informacje na temat programu Azure PowerShell w trybie usługi Azure Resource Manager można znaleźć w [usłudze Korzystanie z usługi Azure PowerShell z usługą Azure Resource Manager.](../powershell-azure-resource-manager.md)

Polecenia cmdlet usługi Azure PowerShell Resource Manager można zainstalować obok siebie z poleceniami cmdlet ASM. Polecenia cmdlet z dwóch trybów można odróżnić od ich nazw.  Tryb Menedżera zasobów ma *AzHDInsight* w nazwach poleceń cmdlet w porównaniu z *AzureHDInsight* w trybie ASM.  Na przykład *New-AzHDInsightCluster* vs. *New-AzureHDInsightCluster*. Parametry i przełączniki mogą mieć nazwy wiadomości i istnieje wiele nowych parametrów dostępnych podczas korzystania z Menedżera zasobów.  Na przykład kilka poleceń cmdlet wymaga nowego przełącznika o nazwie *-ResourceGroupName*. 

Aby można było używać poleceń cmdlet HDInsight, należy połączyć się z kontem platformy Azure i utworzyć nową grupę zasobów:

* [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Znaki cmdlet o zmienionej nazwie
Aby wyświetlić listę poleceń cmdlet ASM programu HDInsight w konsoli programu Windows PowerShell:

    help *azurehdinsight*

W poniższej tabeli wymieniono polecenia cmdlet ASM i ich nazwy w trybie Menedżera zasobów:

| Polecenia cmdlet ASM | Polecenia cmdlet Menedżera zasobów |
| --- | --- |
| Add-AzureHDInsightConfigValue |[Wartość dodana AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Dodatek AzHDInsightStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Właściwości Get-AzureHDInsightProperty |[Właściwości Get-AzHDInsightProperty](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightproperty) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzHDInsightHiveJob](https://docs.microsoft.com/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[Nowy-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[Nowy-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[Nowa-AzHDInsightHiveJobDefinacja](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[Nowa-AzHDInsightMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[Nowa-AzHDInsightPigJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[Nowa-AzHDInsightSqoopJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[Nowa-AzHDInsightStreamingMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Usuń-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Użyj-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Czekaj-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Nowe polecenia cmdlet
Poniżej przedstawiono nowe polecenia cmdlet, które są dostępne tylko w trybie Menedżera zasobów. 

**Polecenia cmdlet związane z akcjami skryptów:**

* **Get-AzHDInsightPersistedScriptAction:** Pobiera akcje utrwalonego skryptu dla klastra i wyświetla je w porządku chronologicznym lub pobiera szczegółowe informacje dotyczące określonej akcji skryptu utrwalonego. 
* **Get-AzHDInsightScriptActionHistory**: Pobiera historię akcji skryptu dla klastra i wyświetla ją w odwrotnej kolejności chronologicznej lub pobiera szczegóły wcześniej wykonanej akcji skryptu. 
* **Remove-AzHDInsightPersistedScriptAction**: Usuwa akcję utrwalonego skryptu z klastra HDInsight.
* **Set-AzHDInsightPersistedScriptAction**: Ustawia wcześniej wykonaną akcję skryptu jako akcję utrwalonego skryptu.
* **Submit-AzHDInsightScriptAction**: Przesyła nową akcję skryptu do klastra usługi Azure HDInsight. 

Aby uzyskać dodatkowe informacje o użyciu, zobacz [Dostosowywanie klastrów HDInsight opartych na systemie Linux przy użyciu akcji skryptów](hdinsight-hadoop-customize-cluster-linux.md).

**Polecenia cmdlet związane z tożsamością klastra:**

* **Add-AzHDInsightClusterIdentity**: Dodaje tożsamość klastra do obiektu konfiguracji klastra, dzięki czemu klaster HDInsight może uzyskać dostęp do usługi Azure Data Lake Storage. Zobacz [Tworzenie klastra usługi HDInsight z usługą Data Lake Storage przy użyciu programu Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

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

**Klaster listy**

Stare polecenie (ASM):

    Get-AzureHDInsightCluster

Nowe polecenie:

    Get-AzHDInsightCluster 

**Pokaż klaster**

Stare polecenie (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Nowe polecenie:

    Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Inne próbki
* [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Prześlij zadania Apache Hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Prześlij oferty pracy Apache Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migracja do nowego sdk HDInsight .NET
Zestaw [SDK .NET](https://msdn.microsoft.com/library/azure/mt416619.aspx) oparty na usłudze Azure Service Management (ASM) jest teraz przestarzały. Zachęcamy do korzystania z opartego na Usłudze Azure Resource [Manager sDK .NET oparty na usłudze Azure Resource Manager](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight). Następujące pakiety HDInsight oparte na ASM są przestarzałe.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Ta sekcja zawiera wskaźniki, aby uzyskać więcej informacji na temat wykonywania niektórych zadań przy użyciu SDK opartego na Menedżerze zasobów.

| Jak... przy użyciu sdk HDInsight oparty na Menedżerze zasobów | Linki |
| --- | --- |
| Zestaw SDK usługi Azure HDInsight dla platformy .NET|Zobacz [zestaw SDK usługi Azure HDInsight dla platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet) |
| Uwierzytelnij aplikacje interaktywnie przy użyciu usługi Azure Active Directory za pomocą narzędzia .NET SDK |Zobacz [Uruchamianie zapytań gałęzi apache przy użyciu pliku .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Fragment kodu w tym artykule używa podejścia uwierzytelniania interaktywnego. |
| Uwierzytelnij aplikacje nieinteraktywnie przy użyciu usługi Azure Active Directory za pomocą pliku .NET SDK |Zobacz [Tworzenie aplikacji nieinterakcyjnych dla usługi HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Prześlij zadanie gałęzi Apache przy użyciu pliku .NET SDK |Zobacz [Przekazywanie zadań Gałąź Apache](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Prześlij zadanie Apache Sqoop przy użyciu pliku .NET SDK |Zobacz [Prześlij oferty pracy Apache Sqoop](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Wyświetlanie listy klastrów usługi HDInsight przy użyciu zestawu SDK platformy .NET |Zobacz [Lista klastrów HDInsight](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Skalowanie klastrów hdinsight przy użyciu zestawu SDK platformy .NET |Zobacz [Skalowanie klastrów HDInsight](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Udzielanie/odwoływanie dostępu do klastrów usługi HDInsight przy użyciu zestawu SDK platformy .NET |Zobacz [Udzielanie/odwoływanie dostępu do klastrów HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Aktualizowanie poświadczeń użytkownika HTTP dla klastrów usługi HDInsight przy użyciu zestawu SDK platformy .NET |Zobacz [Aktualizowanie poświadczeń użytkownika HTTP dla klastrów usługi HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Znajdowanie domyślnego konta magazynu dla klastrów usługi HDInsight przy użyciu zestawu SDK platformy .NET |Zobacz [Znajdowanie domyślnego konta magazynu dla klastrów HDInsight](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Usuwanie klastrów usługi HDInsight przy użyciu zestawu SDK platformy .NET |Zobacz [Usuwanie klastrów HDInsight](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Przykłady
Oto kilka przykładów, jak operacja jest wykonywana przy użyciu SDK opartego na ASM i fragment kodu równoważnego dla SDK opartego na Menedżerze zasobów.

**Tworzenie klienta CRUD klastra**

* Stare polecenie (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Nowe polecenie (autoryzacja głównej usługi)
  
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
* Nowe polecenie (autoryzacja użytkownika)
  
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

