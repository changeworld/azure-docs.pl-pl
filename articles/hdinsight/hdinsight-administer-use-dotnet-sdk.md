---
title: Zarządzanie klastrami Apache Hadoop w HDInsight przy użyciu zestawu SDK platformy .NET — Azure
description: Dowiedz się, jak wykonywać zadania administracyjne dla klastrów Apache Hadoop w HDInsight przy użyciu zestawu .NET SDK HDInsight.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 2e57726c3519fbb2660d7dfb4794a885871acc0d
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793943"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Zarządzanie klastrami Apache Hadoop w HDInsight przy użyciu zestawu .NET SDK

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Dowiedz się, jak zarządzać klastrami HDInsight przy użyciu [HDInsight.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).

**Wymagania wstępne**

Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Nawiązać połączenie z usługi Azure HDInsight

Potrzebne są następujące pakiety NuGet:

```powershell
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

Poniższy przykład kodu pokazuje, jak łączenie z platformą Azure, zanim będzie możliwe zarządzanie klastrami HDInsight, w ramach Twojej subskrypcji platformy Azure.

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

Zobaczysz monit podczas uruchamiania tego programu.  Jeśli nie chcesz wyświetlić monit, zobacz [tworzenie aplikacji HDInsight platformy .NET z uwierzytelnianiem nieinterakcyjnym](hdinsight-create-non-interactive-authentication-dotnet-applications.md).

## <a name="create-clusters"></a>Tworzenie klastrów

Zobacz [opartych na systemie Linux z Tworzenie klastrów w HDInsight przy użyciu zestawu .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

## <a name="list-clusters"></a>Wyświetlanie listy klastrów

Poniższy fragment kodu zawiera listę klastrów i niektóre właściwości:

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

Aby usunąć klaster, synchronicznie lub asynchronicznie, należy użyć poniższego fragmentu kodu: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Skalowanie klastrów

Skalowanie funkcji klastra umożliwia zmianę liczby węzłów procesu roboczego używany przez klaster, który jest uruchamiany w usłudze Azure HDInsight bez konieczności ponownego tworzenia klastra.

> [!NOTE]  
> Tylko klastry HDInsight w wersji 3.1.3 lub nowszej są obsługiwane. Jeśli masz pewności, jaka wersja klastra, możesz sprawdzić na stronie właściwości.  Zobacz [listy i wyświetlaniu klastrów](hdinsight-administer-use-portal-linux.md#showClusters).

Wpływ zmianę liczby węzłów danych dla każdego typu klastra obsługiwane przez HDInsight:

* Apache Hadoop
  
    Możesz bezproblemowo zwiększyć liczbę węzłów procesu roboczego w klastrze usługi Hadoop, w którym jest uruchomiony bez wywierania wpływu na wszystkie oczekujące lub uruchomione zadania. Nowe zadania należy dostarczyć również w przypadku, gdy operacja jest w toku. Błędy trwaniem skalowania bez problemu zmieniała są obsługiwane, dzięki czemu klaster zawsze pozostanie w stanie działać.
  
    Gdy klaster Hadoop jest skalowane w dół dzięki zmniejszeniu liczby węzłów danych, zostaną ponownie uruchomione niektóre z tych usług w klastrze. To powoduje, że wszystkie uruchomione i oczekujące zadania na zakończenie operacji skalowania. Można jednak ponownie przesłać zadania po zakończeniu operacji.
* Apache HBase
  
    Możesz bezproblemowo Dodawanie lub usuwanie węzłów do klastra HBase jest uruchomiona. Serwery regionalne automatycznie są równoważone w ciągu kilku minut od zakończenia operacji skalowania. Można jednak również ręcznie równoważyć serwerów regionalnych, logując się do węzła głównego klastra i uruchamiając następujące polecenia z okna wiersza polecenia:
  

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm
  
    Bezproblemowo można dodać lub usunąć węzły danych z klastrem Storm, jest uruchomiona. Jednak po pomyślnym zakończeniu operacji skalowania, konieczne będzie ponowne zrównoważenie topologii.
  
    Ponowne równoważenie może się odbywać na dwa sposoby:
  
  * Interfejs użytkownika sieci web systemu STORM
  * Narzędzia interfejsu wiersza polecenia (CLI)
    
    Zapoznaj się [dokumentacji platformy Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) Aby uzyskać więcej informacji.
    
    Interfejs użytkownika sieci web systemu Storm jest dostępny w klastrze HDInsight:
    
    ![Ponowne równoważenie skalowania HDInsight Storm](./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Oto przykład jak ponowne zrównoważenie topologii Storm za pomocą polecenia interfejsu wiersza polecenia:
    

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Poniższy fragment kodu pokazuje, jak zmienić rozmiar klastra, synchronicznie lub asynchronicznie:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Dostęp do przydzielenia/odwołania

Klastry HDInsight mają następujące usługi sieci web HTTP (wszystkie te usługi mają punktów końcowych RESTful):

* ODBC
* JDBC
* Apache Ambari
* Apache Oozie
* Apache Templeton

Domyślnie te usługi są przyznawane dostępu. Możesz można odwołać/Udziel dostępu. Aby można było odwołać:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

Aby udzielić:

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
> Przez przyznanie/odbieranie prawa dostępu, możesz zresetować klastra, nazwę użytkownika i hasło.

Można to również zrobić w portalu. Zobacz [Zarządzanie Apache Hadoop clusters w HDInsight przy użyciu witryny Azure portal](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Aktualizowanie poświadczeń użytkownika HTTP

Jest tę samą procedurę jak dostęp Grant/revoke HTTP.  W przypadku klastra przyznano dostęp HTTP, należy je najpierw odwołać.  A następnie przyznać dostęp z nowymi poświadczeniami użytkownika protokołu HTTP.

## <a name="find-the-default-storage-account"></a>Znajdź domyślne konto magazynu

Poniższy fragment kodu pokazuje, jak domyślna nazwa konta magazynu i domyślny klucz konta magazynu dla klastra.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Przesyłanie zadań

**Aby przesłać zadania MapReduce**

Zobacz [uruchamianie przykładów technologii MapReduce w HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Aby przesłać zadania technologii Hive** 

Zobacz [uruchamianie Apache zapytań Hive przy użyciu zestawu .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**Aby przesłać zadania Apache Pig**

Zobacz [zadania uruchamiania Apache Pig, przy użyciu zestawu .NET SDK](hadoop/apache-hadoop-use-pig-dotnet-sdk.md).

**Do przesyłania zadań z wykorzystaniem narzędzia Apache Sqoop**

Zobacz [z HDInsight przy użyciu narzędzia Apache Sqoop](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Aby przesłać zadania programu Apache Oozie**

Zobacz [Użyj Apache Oozie z usługą Hadoop, aby zdefiniować i uruchomić przepływ pracy w HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do usługi Azure Blob storage

Zobacz [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Zobacz też

* [Dokumentacja zestawu SDK platformy .NET HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Zarządzanie klastrami Apache Hadoop w HDInsight przy użyciu witryny Azure portal](hdinsight-administer-use-portal-linux.md)
* [Administrowanie HDInsight przy użyciu interfejsu wiersza polecenia][hdinsight-admin-cli]
* [Tworzenie klastrów HDInsight][hdinsight-provision]
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