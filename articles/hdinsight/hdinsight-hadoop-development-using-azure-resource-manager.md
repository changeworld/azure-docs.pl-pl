---
title: Migrowanie do narzędzi usługi Azure Resource Manager dla usługi HDInsight | Dokumentacja firmy Microsoft
description: Jak przeprowadzić migrację do usługi Azure Resource Manager narzędzia deweloperskie do klastrów usługi HDInsight
services: hdinsight
editor: cgronlun
manager: jhubbard
author: nitinme
documentationcenter: ''
ms.assetid: 05efedb5-6456-4552-87ff-156d77fbe2e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 095205752b8432a741aab16983b175c21b02c0f4
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017874"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrowanie do narzędzi programistycznych opartych na usłudze Azure Resource Manager dla klastrów usługi HDInsight

HDInsight wycofano narzędzi Azure Service Manager ASM dla usługi HDInsight. Jeśli użytkownik był używany programu Azure PowerShell, interfejsu wiersza polecenia Azure lub zestawu .NET SDK usługi HDInsight do pracy z klastrami usługi HDInsight, zachęca się do wersji usługi Azure Resource Manager programu PowerShell, interfejsu wiersza polecenia i zestawu .NET SDK w przyszłości. Ten artykuł zawiera wskaźniki dotyczące sposobu przeprowadzenia migracji do nowe podejście polegające na podstawie Menedżera zasobów. Gdy mają zastosowanie, ten dokument przedstawia różnice między metodami ASM i Menedżera zasobów dla usługi HDInsight.

> [!IMPORTANT]
> Obsługę funkcji ASM programu PowerShell, interfejsu wiersza polecenia, jak i zestawu .NET SDK nie będzie kontynuowane na **1 stycznia 2017**.
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>Migrowanie interfejsu wiersza polecenia platformy Azure do usługi Azure Resource Manager

> [!IMPORTANT]
> Azure CLI 2.0 nie zapewniają obsługę pracy z klastrami usługi HDInsight. Można nadal używać 1.0 interfejsu wiersza polecenia platformy Azure z usługą HDInsight, jednak Azure CLI 1.0 jest przestarzały.

Poniżej przedstawiono podstawowe polecenia do pracy z usługą HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 1.0:

* `azure hdinsight cluster create` -Tworzy nowy klaster usługi HDInsight
* `azure hdinsight cluster delete` — Usuwa istniejącego klastra usługi HDInsight
* `azure hdinsight cluster show` -wyświetlać informacje na temat istniejącego klastra
* `azure hdinsight cluster list` — Wyświetla klastrów usługi HDInsight dla subskrypcji platformy Azure

Użyj `-h` przełącznik, aby sprawdzić parametry i opcje dostępne dla każdego polecenia.

### <a name="new-commands"></a>Nowe polecenia
Są dostępne z usługi Azure Resource Manager nowego polecenia:

* `azure hdinsight cluster resize` -dynamicznie zmienia liczbę węzłów procesu roboczego w klastrze
* `azure hdinsight cluster enable-http-access` — Umożliwia HTTPs dostęp do klastra (na domyślne)
* `azure hdinsight cluster disable-http-access` — Wyłącza HTTPs dostęp do klastra
* `azure hdinsight script-action` -Zawiera polecenia służące do tworzenia/Zarządzanie akcji skryptu w klastrze
* `azure hdinsight config` -Zawiera polecenia służące do tworzenia pliku konfiguracji, który może być używany z `hdinsight cluster create` polecenie, aby podać informacje o konfiguracji.

### <a name="deprecated-commands"></a>Przestarzałe poleceń
Jeśli używasz `azure hdinsight job` polecenia umożliwiają przesyłanie zadań do klastra usługi HDInsight tych poleceń nie są dostępne za pomocą poleceń Menedżera zasobów. Jeśli potrzebujesz programowo przesyłanie zadań do usługi HDInsight ze skryptów, zamiast tego należy używać interfejsów API REST dostarczanych z usługą HDInsight. Aby uzyskać więcej informacji na przesyłanie zadań przy użyciu interfejsów API REST należy znaleźć w następujących dokumentach.

* [Uruchamianie zadań MapReduce z Hadoop w usłudze HDInsight przy użyciu programu cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Uruchamianie zapytań Hive z usługą Hadoop w usłudze HDInsight przy użyciu programu cURL](hadoop/apache-hadoop-use-hive-curl.md)
* [Uruchamianie zadań Pig z usługą Hadoop w usłudze HDInsight przy użyciu programu cURL](hadoop/apache-hadoop-use-pig-curl.md)

Aby informacji na temat innych sposobów uruchamiania MapReduce, Hive i wieprzowa interaktywnego, zobacz [Użyj MapReduce z Hadoop w usłudze HDInsight](hadoop/hdinsight-use-mapreduce.md), [używanie Hive z usługą Hadoop w usłudze HDInsight](hadoop/hdinsight-use-hive.md), i [Use Pig z platformą Hadoop w HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Przykłady
**Tworzenie klastra**

* Polecenie stary (ASM)- `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nowe polecenie: `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Usuwanie klastra**

* Polecenie stary (ASM)- `azure hdinsight cluster delete myhdicluster`
* Nowe polecenie: `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Lista klastrów**

* Polecenie stary (ASM)- `azure hdinsight cluster list`
* Nowe polecenie: `azure hdinsight cluster list`

> [!NOTE]
> Dla polecenia listy określenie grupy zasobów przy użyciu `-g` zwróci tylko klastry w określonej grupy zasobów.
> 
> 

**Pokaż informacje o klastrze**

* Polecenie stary (ASM)- `azure hdinsight cluster show myhdicluster`
* Nowe polecenie: `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrowanie programu Azure PowerShell do usługi Azure Resource Manager
Informacje ogólne dotyczące programu Azure PowerShell w trybie Azure Resource Manager można znaleźć w folderze [przy użyciu programu Azure PowerShell z usługą Azure Resource Manager](../powershell-azure-resource-manager.md).

Polecenia cmdlet programu Azure PowerShell Resource Manager można zainstalować równolegle z poleceń cmdlet funkcji ASM. Polecenia cmdlet z dwóch trybów można rozróżnić przy użyciu ich nazw.  Tryb usługi Resource Manager ma *AzureRmHDInsight* w nazwach polecenia cmdlet porównanie z *AzureHDInsight* w trybie ASM.  Na przykład *AzureRmHDInsightCluster nowy* vs. *New-AzureHDInsightCluster*. Parametry i opcje może mieć nazwy wiadomości i dostępnych wiele nowych parametrów podczas korzystania z Menedżera zasobów.  Na przykład kilka poleceń cmdlet wymagają nowy przełącznik o nazwie *- ResourceGroupName*. 

Przed użyciem polecenia cmdlet usługi HDInsight, należy połączyć z kontem platformy Azure i Utwórz nową grupę zasobów:

* Connect-AzureRmAccount lub [AzureRmProfile wybierz](https://msdn.microsoft.com/library/mt619310.aspx). Zobacz [uwierzytelniania nazwy głównej usługi z usługą Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Zmieniono nazwę polecenia cmdlet
Aby wyświetlić listę poleceń cmdlet funkcji ASM usługi HDInsight w konsoli środowiska Windows PowerShell:

    help *azurermhdinsight*

W poniższej tabeli wymieniono poleceń cmdlet funkcji ASM i nazw w trybie Menedżera zasobów:

| Polecenia cmdlet funkcji ASM | Polecenia cmdlet usługi Resource Manager |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzureRmHDInsightConfigValues](https://msdn.microsoft.com/library/mt603530.aspx) |
| Add-AzureHDInsightMetastore |[Add-AzureRmHDInsightMetastore](https://msdn.microsoft.com/library/mt603670.aspx) |
| Add-AzureHDInsightScriptAction |[Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) |
| Add-AzureHDInsightStorage |[Add-AzureRmHDInsightStorage](https://msdn.microsoft.com/library/mt619445.aspx) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619371.aspx) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603590.aspx) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://msdn.microsoft.com/library/mt603793.aspx) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://msdn.microsoft.com/library/mt603546.aspx) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619407.aspx) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603717.aspx) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://msdn.microsoft.com/library/mt603593.aspx) |
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) |
| New-AzureHDInsightClusterConfig |[New-AzureRmHDInsightClusterConfig](https://msdn.microsoft.com/library/mt603700.aspx) |
| New-AzureHDInsightHiveJobDefinition |[New-AzureRmHDInsightHiveJobDefinition](https://msdn.microsoft.com/library/mt619448.aspx) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzureRmHDInsightMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| New-AzureHDInsightPigJobDefinition |[New-AzureRmHDInsightPigJobDefinition](https://msdn.microsoft.com/library/mt603671.aspx) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzureRmHDInsightSqoopJobDefinition](https://msdn.microsoft.com/library/mt608551.aspx) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzureRmHDInsightStreamingMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619431.aspx) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619375.aspx) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603523.aspx) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://msdn.microsoft.com/library/mt603513.aspx) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://msdn.microsoft.com/library/mt603486.aspx) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603798.aspx) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt619424.aspx) |
| Use-AzureHDInsightCluster |[Use-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619442.aspx) |
| Wait-AzureHDInsightJob |[Wait-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603834.aspx) |

### <a name="new-cmdlets"></a>Nowe polecenia cmdlet
Poniżej przedstawiono nowe polecenia cmdlet, które są dostępne tylko w trybie Menedżera zasobów. 

**Polecenia cmdlet związane z akcji skryptu:**

* **Get-AzureRmHDInsightPersistedScriptAction**: pobiera akcji utrwalonego skryptu dla klastra i wyświetla je w porządku chronologicznym lub pobiera szczegóły dla działania określonego utrwalonego skryptu. 
* **Get-AzureRmHDInsightScriptActionHistory**: pobiera historii akcji skryptu dla klastra i wyświetla go w odwrotnej kolejności chronologicznej lub pobiera szczegóły akcji wcześniej wykonanie skryptu. 
* **Usuń AzureRmHDInsightPersistedScriptAction**: usuwa akcji utrwalonego skryptu z klastra usługi HDInsight.
* **Zestaw AzureRmHDInsightPersistedScriptAction**: ustawia akcji skryptu poprzednio wykonane jako akcji utrwalonego skryptu.
* **Przedstawia AzureRmHDInsightScriptAction**: przesyła nowa akcja skryptu do klastra usługi Azure HDInsight. 

Użycie dodatkowych informacji, zobacz [klastrów usługi HDInsight opartej na dostosowanie systemu Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

**Klastra poleceń cmdlet związanych z tożsamości:**

* **Dodaj AzureRmHDInsightClusterIdentity**: dodaje tożsamości klastra obiektowi konfiguracji klastra, dzięki czemu klastra usługi HDInsight mogą uzyskiwać dostęp do usługi Azure Data Lake magazynów. Zobacz [tworzenia klastra usługi HDInsight z Data Lake Store za pomocą programu Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Przykłady
**Tworzenie klastra**

Polecenie stary (ASM): 

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

    New-AzureRmHDInsightCluster `
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

Polecenie stary (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Nowe polecenie:

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Klaster listą**

Polecenie stary (ASM):

    Get-AzureHDInsightCluster

Nowe polecenie:

    Get-AzureRmHDInsightCluster 

**Pokaż klastra**

Polecenie stary (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Nowe polecenie:

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Inne przykłady
* [Tworzenie klastrów usługi HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Przesyłania zadań Hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Przesyłanie zadań Pig](hadoop/apache-hadoop-use-pig-powershell.md)
* [Przesyłanie zadań Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migracja do nowego zestawu .NET SDK usługi HDInsight
Systemem zarządzania usługą Azure [zestawu .NET SDK usługi HDInsight (ASM)](https://msdn.microsoft.com/library/azure/mt416619.aspx) jest już przestarzały. Zachęcamy do użycia na podstawie zarządzania zasobami Azure [na podstawie Menedżera zasobów SDK .NET usługi HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight). Następujące pakiety usługi HDInsight opartych na funkcji ASM są przestarzałe.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Ta sekcja zawiera łącza do dodatkowych informacji na temat sposobu wykonywania określonych zadań przy użyciu zestawu SDK na podstawie Menedżera zasobów.

| Jak... przy użyciu zestawu SDK usługi HDInsight opartej na Menedżera zasobów | Linki |
| --- | --- |
| Tworzenie klastrów usługi HDInsight przy użyciu zestawu .NET SDK |Zobacz [Tworzenie klastrów usługi HDInsight przy użyciu zestawu .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| Dostosowywanie klastra przy użyciu akcji skryptu przy użyciu zestawu .NET SDK |Zobacz [dostosować HDInsight Linux klastrów za pomocą akcji skryptu](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Uwierzytelniania aplikacji interaktywnego przy użyciu usługi Azure Active Directory przy użyciu zestawu .NET SDK |Zobacz [uruchamianie zapytań Hive przy użyciu zestawu .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Fragment kodu w tym artykule używa podejście uwierzytelnianie interakcyjne. |
| Uwierzytelniania aplikacji nieinteraktywnie przy użyciu usługi Azure Active Directory przy użyciu zestawu .NET SDK |Zobacz [utworzyć nieinterakcyjnych aplikacji dla usługi HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Przesłać zadania technologii Hive, przy użyciu zestawu .NET SDK |Zobacz [Hive przesyłania zadań](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Przesłać zadania programu Pig przy użyciu zestawu .NET SDK |Zobacz [Pig przesyłania zadań](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| Prześlij zadanie Sqoop przy użyciu zestawu .NET SDK |Zobacz [Sqoop przesyłania zadań](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Lista klastrów usługi HDInsight przy użyciu zestawu .NET SDK |Zobacz [klastrów usługi HDInsight listy](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Skalowanie klastrów usługi HDInsight przy użyciu zestawu .NET SDK |Zobacz [klastrów usługi HDInsight skali](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Dostęp do przydzielenia/odwołania do klastrów usługi HDInsight przy użyciu zestawu .NET SDK |Zobacz [dostępu do przydzielenia/odwołania do klastrów usługi HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Zaktualizuj poświadczenia użytkownika HTTP dla klastrów usługi HDInsight przy użyciu zestawu .NET SDK |Zobacz [HTTP aktualizacji poświadczeń użytkownika do klastrów usługi HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Znajdź domyślne konto magazynu dla klastrów usługi HDInsight przy użyciu zestawu .NET SDK |Zobacz [znaleźć domyślne konto magazynu dla klastrów usługi HDInsight](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Usuwać klastry usługi HDInsight przy użyciu zestawu .NET SDK |Zobacz [klastrów HDInsight usunąć](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Przykłady
Poniżej przedstawiono kilka przykładów w sposób operacja jest wykonywane przy użyciu zestawu SDK na podstawie ASM i fragment kodu równoważne dla Menedżera zasobów na podstawie zestawu SDK.

**Tworzenie klienta CRUD klastra**

* Polecenie stary (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Nowe polecenie (autoryzacji głównej usługi)
  
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

* Polecenie stary (ASM)
  
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

* Polecenie stary (ASM)
  
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

* Polecenie stary (ASM)
  
        client.DeleteCluster(dnsName);
* Nowe polecenie
  
        client.Clusters.Delete(resourceGroup, dnsname);

