---
title: Zarządzanie klastrami Hadoop w HDInsight przy użyciu zestawu .NET SDK - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykonywać zadania administracyjne dla klastrów Hadoop w usłudze HDInsight przy użyciu zestawu SDK .NET usługi HDInsight.
services: hdinsight
editor: cgronlun
manager: jhubbard
tags: azure-portal
author: mumian
documentationcenter: ''
ms.assetid: fd134765-c2a0-488a-bca6-184d814d78e9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: jgao
ms.openlocfilehash: 6926ef6ee3634a2bc469f56e2b4f7cfe9278d2a0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Zarządzanie klastrami Hadoop w usłudze HDInsight przy użyciu zestawu .NET SDK
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Informacje o sposobie zarządzania klastrami HDInsight przy użyciu [HDInsight.NET SDK](https://msdn.microsoft.com/library/mt271028.aspx).

**Wymagania wstępne**

Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Połączenie do usługi Azure HDInsight

Potrzebne są następujące pakiety NuGet:

```
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

Poniższy przykład kodu pokazuje sposób podłączania na platformie Azure, zanim będzie możliwe zarządzanie klastrami usługi HDInsight w ramach Twojej subskrypcji platformy Azure.

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

Zobaczysz monit po uruchomieniu tego programu.  Jeśli nie chcesz wyświetlić monit, zobacz [tworzenie aplikacji usługi HDInsight .NET uwierzytelnianie nieinteraktywne](hdinsight-create-non-interactive-authentication-dotnet-applications.md).

## <a name="create-clusters"></a>Tworzenie klastrów
Zobacz [opartych na systemie Linux z tworzenia klastrów w usłudze HDInsight przy użyciu zestawu .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

## <a name="list-clusters"></a>Lista klastrów
Poniższy fragment kodu zawiera klastrów i niektóre właściwości:

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
Aby usunąć klaster, synchronicznie lub asynchronicznie, użyj poniższy fragment kodu: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Skalowanie klastrów
Skalowanie funkcji klastra umożliwia zmianę liczby węzłów procesu roboczego używane przez klaster, który jest uruchomiony w usłudze Azure HDInsight bez konieczności ponownego tworzenia klastra.

> [!NOTE]
> Tylko klastry usługi HDInsight w wersji 3.1.3 lub nowszym są obsługiwane. Jeśli masz pewności, jaka wersja klastra, można sprawdzić na stronie właściwości.  Zobacz [klastrów listy i Pokaż](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
> 
> 

Wpływ zmianę liczby węzłów danych dla każdego typu obsługiwanych przez HDInsight klastra:

* Hadoop
  
    Można bezproblemowo zwiększyć liczbę węzłów procesu roboczego w klastrze platformy Hadoop, który jest uruchomiony bez wpływu na wszystkie oczekujące lub uruchomione zadania. Również można przesłać nowe zadania, gdy operacja jest w toku. Błędy w operacji skalowania bezpiecznie obsługi tak, aby zawsze pozostanie w stanie funkcjonalności klastra.
  
    Podczas skalowania klastra usługi Hadoop w dół dzięki zmniejszeniu liczby węzłów danych są ponownie uruchamiane niektóre z tych usług w klastrze. Powoduje to wszystkich uruchomionych i oczekujące zadania się niepowodzeniem po zakończeniu operacji skalowania. Można jednak Prześlij ponownie zadania po zakończeniu operacji.
* HBase
  
    Można bezproblemowo dodać lub usunąć węzły do klastra HBase jest uruchomiona. Serwery regionalne automatycznie równoważy w ciągu kilku minut od zakończenia operacji skalowania. Można jednak również ręcznie saldo serwery regionalne logowanie do headnode klastra i uruchamiając następujące polecenia z poziomu okna wiersza polecenia:
  
    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```
* Storm
  
    Bezproblemowo można dodawać i usuwać dane węzły do klastra Storm, jest uruchomiona. Jednak po pomyślnym zakończeniu operacji skalowania, konieczne będzie ponowne zrównoważenie topologii.
  
    Ponowne równoważenie można zrobić na dwa sposoby:
  
  * STORM interfejsu użytkownika sieci web
  * Narzędzia interfejsu wiersza polecenia (CLI)
    
    Zapoznaj się z [dokumentację Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) więcej szczegółów.
    
    Interfejs użytkownika sieci web Storm jest dostępna w klastrze usługi HDInsight:
    
    ![HDInsight Storm Zrównoważ skali](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Oto przykład sposobu użycia polecenia interfejsu wiersza polecenia do ponowne zrównoważenie topologii Storm:
    
    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Poniższy fragment kodu przedstawia sposób zmiany rozmiaru klastra synchronicznie lub asynchronicznie:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Dostęp do przydzielenia/odwołania
Klastry HDInsight są następujące usługi sieci web HTTP (wszystkie te usługi mają RESTful punkty końcowe):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Domyślnie te usługi są przyznawane dostępu. Możesz można odwołać/udzielenie dostępu. Aby można było odwołać:

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
> Przez przyznawanie/odwoływanie dostępu, spowoduje zresetowanie klastra, nazwa użytkownika i hasło.
> 
> 

Można to również zrobić w portalu. Zobacz [administrowania HDInsight przy użyciu portalu Azure][hdinsight-admin-portal].

## <a name="update-http-user-credentials"></a>Zaktualizuj poświadczenia użytkownika HTTP
Jest tej samej procedury co [dostępu Grant/revoke HTTP](#grant/revoke-access). Jeśli klaster przyznano dostęp HTTP, należy je najpierw odwołać.  A następnie przyznać dostęp z nowymi poświadczeniami użytkownika HTTP.

## <a name="find-the-default-storage-account"></a>Znajdź domyślne konto magazynu
Poniższy fragment kodu przedstawia sposób uzyskać domyślna nazwa konta magazynu i domyślny klucz konta magazynu dla klastra.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Przesyłanie zadań
**Aby przesłać zadania MapReduce**

Zobacz [przykłady Uruchom MapReduce z Hadoop w usłudze HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Do przesyłania zadań Hive** 

Zobacz [uruchamianie zapytań Hive przy użyciu zestawu .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**Do przesyłania zadań Pig**

Zobacz [Pig uruchomienie zadania przy użyciu zestawu .NET SDK](hadoop/apache-hadoop-use-pig-dotnet-sdk.md).

**Do przesyłania zadań Sqoop**

Zobacz [Sqoop za pomocą usługi HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Do przesyłania zadań Oozie**

Zobacz [Oozie użycia z usługą Hadoop do definiowania i uruchomić przepływ pracy w usłudze HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do magazynu obiektów Blob platformy Azure
Zobacz [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Zobacz też
* [Dokumentacji zestawu SDK .NET usługi HDInsight](https://msdn.microsoft.com/library/mt271028.aspx)
* [Administrowanie HDInsight przy użyciu portalu Azure][hdinsight-admin-portal]
* [Administrowanie HDInsight przy użyciu interfejsu wiersza polecenia][hdinsight-admin-cli]
* [Tworzenie klastrów usługi HDInsight][hdinsight-provision]
* [Przekazywanie danych do usługi HDInsight][hdinsight-upload-data]
* [Wprowadzenie do usługi Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-portal-linux.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md


