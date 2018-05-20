---
title: Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostosować klastry usługi HDInsight przy użyciu akcji skryptu.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3a63e216-4163-40c1-aa04-6b42fd0162ad
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 10/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 15fa3e7738810ada48f471a685f79a82445ad70c
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="customize-windows-based-hdinsight-clusters-using-script-action"></a>Dostosowywanie klastrów usługi HDInsight opartej na systemie Windows za pomocą akcji skryptu
**Akcja skryptu** może służyć do wywołania [niestandardowych skryptów](hdinsight-hadoop-script-actions.md) podczas procesu tworzenia klastra w przypadku instalowania dodatkowego oprogramowania w klastrze.

Informacje przedstawione w tym artykule jest specyficzne dla klastrów usługi HDInsight opartych na systemie Windows. W klastrach opartych na systemie Linux, zobacz [klastrów usługi HDInsight opartej na dostosowanie systemu Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

Klastry usługi HDInsight można dostosować w różnych innych metod, a także takie jak tym dodatkowych kont usługi Azure Storage, zmiana Hadoop plików konfiguracji (core-site.xml, hive-site.xml, itp.) lub biblioteki (np. Hive, Oozie) dodawanie udostępnione do wspólnej lokalizacji w klastrze. Te modyfikacje może odbywać się za pomocą programu Azure PowerShell, zestawu .NET SDK usługi Azure HDInsight lub w portalu Azure. Aby uzyskać więcej informacji, zobacz [klastrów utworzyć Hadoop w usłudze HDInsight][hdinsight-provision-cluster].

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## <a name="script-action-in-the-cluster-creation-process"></a>Akcja skryptu w ramach procesu tworzenia klastra
Akcja skryptu jest używana tylko w przypadku, gdy klaster jest właśnie tworzona. Na poniższym diagramie przedstawiono podczas wykonywania akcji skryptu w trakcie procesu tworzenia:

![Dostosowywanie klastrów usługi HDInsight i etapy podczas tworzenia klastra][img-hdi-cluster-states]

Podczas wykonywania skryptu klastra wprowadza **ClusterCustomization** etapu. Na tym etapie skrypt jest uruchamiany na koncie administratora systemu, równolegle dla określonych węzłów w klastrze i zawiera uprawnienia administrator o pełnych uprawnieniach w węzłach.

> [!NOTE]
> Ponieważ użytkownik ma uprawnienia administratora w węzłach klastra podczas **ClusterCustomization** etapie umożliwia skrypt wykonywać operacje, takie jak zatrzymanie i uruchomienie usług, w tym usług związanych z usługi Hadoop. Tak, jako część skryptu należy się upewnić czy usług Ambari i innych usług związanych z Hadoop i uruchomić przed zakończeniu skryptu. Te usługi są wymagane do pomyślnie ustalić kondycję i stan klastra podczas jego tworzenia. Jeśli zmienisz żadnej konfiguracji w klastrze, który ma wpływ na tych usług, należy użyć funkcji pomocniczych, które są udostępniane. Aby uzyskać więcej informacji o funkcji pomocnika, zobacz [skryptów tworzenie akcji skryptu dla usługi HDInsight][hdinsight-write-script].
>
>

Dane wyjściowe i dzienników błędów dla skryptu są przechowywane w domyślne konto magazynu, określone dla klastra. Dzienniki są przechowywane w tabeli o nazwie **u < \cluster-name-fragment >< \time-stamp > setuplog**. Są to dzienniki agregacji w skrypcie uruchamiania we wszystkich węzłach (węzła głównego i węzły procesów roboczych) w klastrze.

Każdy klaster może akceptować wiele akcji skryptu, które jest wywoływane w kolejności, w którym są określone. Skrypt może uruchomionego na węzła głównego i węzłów procesu roboczego.

Usługa HDInsight zapewnia kilka skryptów do zainstalowania następujących składników w klastrach HDInsight:

| Name (Nazwa) | Skrypt |
| --- | --- |
| **Zainstaluj Spark** |https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Zobacz [instalacji i używania platformy Spark w usłudze HDInsight clusters][hdinsight-install-spark]. |
| **Zainstalować język R** |https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Zobacz [instalacji i użyj R w klastrach HDInsight] [hdinsight-install-r]. |
| **Zainstaluj Solr** |https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Zobacz [instalacji i używania Solr w usłudze HDInsight clusters](hdinsight-hadoop-solr-install.md). |
| - **Zainstaluj Giraph** |https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Zobacz [instalacji i używania Giraph w usłudze HDInsight clusters](hdinsight-hadoop-giraph-install.md). |
| **Wstępne ładowanie bibliotek technologii Hive** |https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1. Zobacz [dodać Hive bibliotek w klastrach HDInsight](hdinsight-hadoop-add-hive-libraries.md) |

## <a name="call-scripts-using-the-azure-portal"></a>Skrypty wywołania przy użyciu portalu Azure
**W portalu Azure**

1. Rozpocznij tworzenie klastra, zgodnie z opisem w [klastrów utworzyć Hadoop w HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
2. W obszarze Konfiguracja opcjonalne dla **akcji skryptu** bloku, kliknij przycisk **dodać akcję skryptu** do udostępnienia szczegółów dotyczących akcji skryptu, jak pokazano poniżej:

    ![Aby dostosować klastra, użyj akcji skryptu](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "użyj akcji skryptu, aby dostosować klastra")

    <table border='1'>
        <tr><th>Właściwość</th><th>Wartość</th></tr>
        <tr><td>Name (Nazwa)</td>
            <td>Określ nazwę akcji skryptu.</td></tr>
        <tr><td>Identyfikator URI skryptu</td>
            <td>Określ identyfikator URI do skryptu, które jest wywoływane, aby dostosować klastra. s</td></tr>
        <tr><td>HEAD/proces roboczy.</td>
            <td>Określ węzły (**Head** lub **procesu roboczego**) uruchamiania skryptu dostosowania.</b>.
        <tr><td>Parametry</td>
            <td>Określ parametry, jeśli jest to wymagane przez skrypt.</td></tr>
    </table>

    Naciśnij klawisz ENTER, aby dodać więcej niż jedna akcja skryptu, aby zainstalować wiele składników w klastrze.
3. Kliknij przycisk **wybierz** Aby zapisać konfigurację akcji skryptu i kontynuować tworzenia klastra.

## <a name="call-scripts-using-azure-powershell"></a>Skrypty wywołania przy użyciu programu Azure PowerShell
Tego poniższy skrypt programu PowerShell pokazano, jak zainstalować Spark w klastrze usługi HDInsight opartych na systemie Windows.  

    # Provide values for these variables
    $subscriptionID = "<Azure Suscription ID>" # After "Connect-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

    $nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.

    $hdinsightClusterName = $namePrefix + "spark"
    $httpUserName = "admin"
    $httpPassword = "<Enter a Password>"

    $defaultStorageAccountName = "$namePrefix" + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    #############################################################
    # Connect to Azure
    #############################################################

    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Connect-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    #############################################################
    # Prepare the dependent components
    #############################################################

    # Create resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext

    #############################################################
    # Create cluster with ApacheSpark
    #############################################################

    # Specify the configuration options
    $config = New-AzureRmHDInsightClusterConfig `
                -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $defaultStorageAccountKey


    # Add a script action to the cluster configuration
    $config = Add-AzureRmHDInsightScriptAction `
                -Config $config `
                -Name "Install Spark" `
                -NodeType HeadNode `
                -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1 `

    # Start creating a cluster with Spark installed
    New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -Location $location `
            -ClusterSizeInNodes 2 `
            -ClusterType Hadoop `
            -OSType Windows `
            -DefaultStorageContainer $defaultBlobContainerName `
            -Config $config


Aby zainstalować inne oprogramowanie, należy zastąpić plik skryptu w skrypcie:

Po wyświetleniu monitu wprowadź poświadczenia dla klastra. Może upłynąć kilka minut przed utworzeniem klastra.

## <a name="call-scripts-using-net-sdk"></a>Skrypty wywołania przy użyciu zestawu .NET SDK
W poniższym przykładzie pokazano, jak zainstalować Spark w klastrze usługi HDInsight opartych na systemie Windows. Aby zainstalować inne oprogramowanie, należy zastąpić plik skryptu w kodzie.

**Aby utworzyć klaster usługi HDInsight za pomocą Spark**

1. Tworzenie aplikacji konsolowej C# w programie Visual Studio.
2. Za pomocą konsoli Menedżera pakietów Nuget uruchom następujące polecenie.

        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight
3. Należy użyć następującego przy użyciu instrukcji w pliku Program.cs:

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
4. Umieść kod w klasie, z następujących czynności:

        private static HDInsightManagementClient _hdiManagementClient;

        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId;
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumWorkerNodes = 2;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const string NewClusterType = "Hadoop";
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            CreateCluster();
        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumWorkerNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,
                DefaultStorageInfo = new AzureStorageInfo(ExistingStorageName, ExistingStorageKey, ExistingContainer),
                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        /// <param name="TenantId">The AAD tenant ID</param>
        /// <param name="ClientId">The AAD client ID</param>
        /// <param name="SubscriptionId">The Azure subscription ID</param>
        /// <returns></returns>
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
5. Naciśnij klawisz **F5**, aby uruchomić aplikację.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Obsługa oprogramowania typu open source używane w klastrach HDInsight
Usługa Microsoft Azure HDInsight jest elastyczna platforma, która umożliwia tworzenie aplikacji danych big data w chmurze przy użyciu ekosystem technologii open source utworzona wokół Hadoop. Microsoft Azure udostępnia ogólnego poziomu wsparcia dla technologii open source, zgodnie z opisem w **obsługuje zakresu** sekcji <a href="http://azure.microsoft.com/support/faq/" target="_blank">witryny sieci Web często zadawane pytania dotyczące obsługi Azure</a>. Usługa HDInsight zapewnia dodatkowy poziom obsługi dla niektórych składników, zgodnie z poniższym opisem.

Istnieją dwa typy składników open source, które są dostępne w usłudze HDInsight:

* **Wbudowanych składników** — te składniki są wstępnie zainstalowane w klastrach HDInsight i podaj podstawowe funkcje klastra. Na przykład YARN ResourceManager, język zapytań Hive (HiveQL) i biblioteki Mahout należą do tej kategorii. Pełna lista składniki klastra jest dostępna w [nowości w wersjach klastra Hadoop dostarczanych z usługą HDInsight?](hdinsight-component-versioning.md) </a>.
* **Niestandardowe składniki** -, jako użytkownik klastra, można zainstalować lub użyć w obciążenie żadnych składników dostępnych w społeczności lub utworzonych przez użytkownika.

Wbudowane składniki są w pełni obsługiwane, a Support firmy Microsoft pomoże w celu odizolowania i rozwiązać problemy związane z tych składników.

> [!WARNING]
> Składniki dostarczony z klastrem usługi HDInsight są w pełni obsługiwane, a Microsoft Support pomoże w celu odizolowania i rozwiązać problemy związane z tych składników.
>
> Niestandardowe składniki otrzymywanie pomocy uzasadnione ekonomicznie ułatwiające aby dalej rozwiązywać ten problem. Może to spowodować w rozwiązaniu problemu lub monitem o Uwzględnij dostępnych kanałów dla technologiach typu open source wykryto głębokie doświadczenia z tej technologii. Na przykład istnieje wiele witryn społeczności, które mogą być używane, takie jak: [forum MSDN dla usługi HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Projekty Apache mieć witryny projektu na [ http://apache.org ](http://apache.org), na przykład: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).
>
>

Usługa HDInsight zapewnia kilka sposobów użycia niestandardowych składników. Niezależnie od tego, jak składnik jest używany lub zainstalowany w klastrze dotyczą tego samego poziomu wsparcia. Poniżej znajduje się lista typowych sposobów, że niestandardowe składniki mogą być używane w klastrach HDInsight:

1. Przesyłanie zadań — usługi Hadoop i innych typów zadań, które wykonać albo użyć niestandardowych składników może zostać przesłane do klastra.
2. Dostosowywanie klastra — podczas tworzenia klastra, można określić dodatkowe ustawienia i niestandardowych składników, które zostaną zainstalowane na węzłach klastra.
3. Próbek - dla popularnych niestandardowych składników, firma Microsoft i inne osoby mogą powodować przykłady sposobu użycia tych składników na klastry usługi HDInsight. Te przykłady są udostępniane bez obsługi.

## <a name="develop-script-action-scripts"></a>Tworzenie skryptów akcji skryptu
Zobacz [skryptów tworzenie akcji skryptu dla usługi HDInsight][hdinsight-write-script].

## <a name="see-also"></a>Zobacz także
* [Tworzenie klastrów Hadoop w usłudze HDInsight] [ hdinsight-provision-cluster] zawiera instrukcje dotyczące sposobu tworzenia klastra usługi HDInsight przy użyciu niestandardowych opcji.
* [Tworzenie skryptów akcji skryptu dla usługi HDInsight][hdinsight-write-script]
* [Zainstalować i używać platformy Spark w usłudze hdinsight][hdinsight-install-spark]
* [Zainstalować i używać Solr w klastrach HDInsight](hdinsight-hadoop-solr-install.md).
* [Zainstalować i używać Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Etapy podczas tworzenia klastra"
