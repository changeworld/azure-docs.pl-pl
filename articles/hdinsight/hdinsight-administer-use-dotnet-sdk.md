---
title: Zarządzanie klastrami Apache Hadoop w usłudze HDInsight za pomocą zestawu .NET SDK — Azure
description: Dowiedz się, jak wykonywać zadania administracyjne dla klastrów Apache Hadoop w udziale HDInsight przy użyciu zestawu SDK .NET usługi HDInsight.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 36a77d49b507d3d0158d1b4b492d0141350de50f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240636"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu zestawu SDK platformy .NET

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Dowiedz się, jak zarządzać klastrami HDInsight przy użyciu [HDInsight.NET SDK.](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)

**Wymagania wstępne**

Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Łączenie się z usługą Azure HDInsight

Potrzebne są następujące pakiety NuGet:

```powershell
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

Poniższy przykładowy kod pokazuje, jak połączyć się z platformą Azure, zanim będzie można administrować klastrami USŁUGI HDInsight w ramach subskrypcji platformy Azure.

```csharp
using System;
using Microsoft.Azure;
using Microsoft.Azure.Management.HDInsight;
using Microsoft.Azure.Management.HDInsight.Models;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace HDInsightManagement
{
    class Program
    {
        private static HDInsightManagementClient _hdiManagementClient;
        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

        static void Main(string[] args)
        {
            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            // insert code here

            System.Console.WriteLine("Press ENTER to continue");
            System.Console.ReadLine();
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }
    }
}
```

Po uruchomieniu tego programu zostanie wyświetlony monit.  Jeśli nie chcesz widzieć monitu, zobacz Tworzenie aplikacji net [hdinsight uwierzytelniania nieinterakcyjnego](hdinsight-create-non-interactive-authentication-dotnet-applications.md).


## <a name="list-clusters"></a>Listy klastrów

Poniższy fragment kodu zawiera listę klastrów i niektórych właściwości:

```csharp
var results = _hdiManagementClient.Clusters.List();
foreach (var name in results.Clusters) {
    Console.WriteLine("Cluster Name: " + name.Name);
    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
    Console.WriteLine("\t Cluster location: " + name.Location);
    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
}
```

## <a name="delete-clusters"></a>Usuwanie klastrów

Użyj następującego fragmentu kodu, aby usunąć klaster synchronicznie lub asynchronicznie: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Skalowanie klastrów

Funkcja skalowania klastra umożliwia zmianę liczby węzłów procesu roboczego używanych przez klaster uruchomiony w usłudze Azure HDInsight bez konieczności ponownego tworzenia klastra.

> [!NOTE]  
> Obsługiwane są tylko klastry z hdinsight w wersji 3.1.3 lub nowszej. Jeśli nie masz pewności co do wersji klastra, możesz sprawdzić stronę Właściwości.  Zobacz [Lista i pokaż klastry](hdinsight-administer-use-portal-linux.md#showClusters).

Wpływ zmiany liczby węzłów danych dla każdego typu klastra obsługiwanego przez program HDInsight:

* Apache Hadoop
  
    Można bezproblemowo zwiększyć liczbę węzłów procesu roboczego w klastrze Hadoop, który jest uruchomiony bez wpływu na żadnych oczekujących lub uruchomionych zadań. Nowe zadania można również przesłać w trakcie operacji. Błędy w operacji skalowania są bezpiecznie obsługiwane, tak aby klaster zawsze pozostaje w stanie funkcjonalnym.
  
    Gdy klaster Hadoop jest skalowany w dół przez zmniejszenie liczby węzłów danych, niektóre usługi w klastrze są ponownie uruchamiane. Powoduje to, że wszystkie uruchomione i oczekujące zadania zakończyć się niepowodzeniem po zakończeniu operacji skalowania. Można jednak ponownie przesłać zadania po zakończeniu operacji.
* Apache HBase
  
    Można bezproblemowo dodać lub usunąć węzły do klastra HBase, gdy jest uruchomiony. Serwery regionalne są automatycznie równoważące w ciągu kilku minut od ukończenia operacji skalowania. Można jednak również ręcznie zrównoważyć serwery regionalne, logując się do węzła głównego klastra i uruchamiając następujące polecenia w oknie wiersza polecenia:
  

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm
  
    Podczas działania klastra Storm można bezproblemowo dodawać lub usuwać węzły danych. Ale po pomyślnym zakończeniu operacji skalowania, trzeba będzie zrównoważyć topologii.
  
    Ponowne równoważenie można wykonać na dwa sposoby:
  
  * Interfejs użytkownika sieci Burzowej
  * Narzędzie interfejsu wiersza polecenia (CLI)
    
    Więcej informacji można znaleźć w [dokumentacji Apache Storm.](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)
    
    Interfejs użytkownika sieci Storm jest dostępny w klastrze HDInsight:
    
    ![Równoważenie skali burzy HDInsight](./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Oto przykład użycia polecenia CLI w celu zrównoważenia topologii burzy:
    

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Poniższy fragment kodu pokazuje, jak zmienić rozmiar klastra synchronicznie lub asynchronicznie:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Udzielanie/odwoływanie dostępu

Klastry HDInsight mają następujące usługi sieci web HTTP (wszystkie te usługi mają punkty końcowe RESTful):

* ODBC
* JDBC
* Apache Ambari
* Apache Oozie
* Apache Templeton

Domyślnie te usługi są przyznawane za dostęp. Można odwołać/udzielić dostępu. Aby odwołać:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

Przyznanie:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = enable,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

> [!NOTE]  
> Przyznając/cofając dostęp, zresetujesz nazwę użytkownika i hasło klastra.

Można to również zrobić za pośrednictwem portalu. Zobacz [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu portalu Azure.](hdinsight-administer-use-portal-linux.md)

## <a name="update-http-user-credentials"></a>Aktualizowanie poświadczeń użytkownika HTTP

Jest to taka sama procedura jak Grant/revoke dostępu HTTP.  Jeśli klaster otrzymał dostęp HTTP, należy go najpierw odwołać.  A następnie udzielić dostępu z nowych poświadczeń użytkownika HTTP.

## <a name="find-the-default-storage-account"></a>Znajdowanie domyślnego konta magazynu

Poniższy fragment kodu pokazuje, jak uzyskać domyślną nazwę konta magazynu i domyślny klucz konta magazynu dla klastra.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Przesyłanie ofert pracy

**Aby przesłać zadania MapReduce**

Zobacz [Uruchamianie mapDoredunia próbek w hdinsight](hadoop/apache-hadoop-run-samples-linux.md).

**Aby przesłać oferty pracy Apache Hive** 

Zobacz [Uruchamianie zapytań gałęzi apache przy użyciu pliku .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**Aby przesłać oferty pracy Apache Sqoop**

Zobacz [Korzystanie z Apache Sqoop z HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Aby przesłać oferty pracy Apache Oozie**

Zobacz [Używanie apache Oozie z Hadoop do definiowania i uruchamiania przepływu pracy w umiaśnie HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do magazynu obiektów blob platformy Azure

Zobacz [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Zobacz też

* [Dokumentacja referencyjna sdk.NET .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu portalu Azure](hdinsight-administer-use-portal-linux.md)
* [Administrowanie programem HDInsight za pomocą interfejsu wiersza polecenia][hdinsight-admin-cli]
* [Tworzenie klastrów usługi HDInsight][hdinsight-provision]
* [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data]
* [Wprowadzenie do usługi Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
