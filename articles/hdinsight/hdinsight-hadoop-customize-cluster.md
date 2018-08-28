---
title: Dostosowywanie klastrów HDInsight za pomocą akcji skryptu, Azure
description: Dowiedz się, jak dostosowywanie klastrów HDInsight za pomocą akcji skryptu.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/05/2016
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 6c4652e65f0f320063d989f97a5428510913005b
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43105928"
---
# <a name="customize-windows-based-hdinsight-clusters-using-script-action"></a>Dostosowywanie klastrów HDInsight z systemem Windows za pomocą akcji skryptu
**Akcja skryptu** może służyć do wywołania [niestandardowe skrypty](hdinsight-hadoop-script-actions.md) podczas procesu tworzenia klastra w przypadku instalowania dodatkowego oprogramowania w klastrze.

Informacje przedstawione w tym artykule dotyczy klastrów HDInsight z systemem Windows. W przypadku klastrów opartych na systemie Linux, zobacz [HDInsight opartych na systemie Linux z Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

Klastry HDInsight można dostosować w różnych także inne sposoby, np. w tym dodatkowe konta usługi Azure Storage, zmiana Hadoop z plików konfiguracji (core-site.xml, gałąź site.xml itp.) lub dodawania udostępnionych bibliotek (np. programu Hive, Oozie) w typowe lokalizacje w klastrze. Te dostosowania można wykonać za pomocą programu Azure PowerShell, zestawu .NET SDK usługi Azure HDInsight lub witryny Azure portal. Aby uzyskać więcej informacji, zobacz [Tworzenie klastrów usługi Hadoop w HDInsight][hdinsight-provision-cluster].

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## <a name="script-action-in-the-cluster-creation-process"></a>Akcja skryptu w procesie tworzenia klastra
Akcja skryptu jest używany tylko w sytuacji, gdy klaster jest właśnie tworzona. Na poniższym diagramie przedstawiono podczas wykonywania w trakcie procesu tworzenia akcji skryptu:

![Dostosowywanie klastra HDInsight i etapy podczas tworzenia klastra][img-hdi-cluster-states]

Podczas wykonywania skryptu klaster przechodzi **ClusterCustomization** etapu. Na tym etapie skrypt jest uruchamiany na koncie administratora systemu, równolegle na określonych węzłów w klastrze i zapewnia uprawnienia Pełna Administracja w węzłach.

> [!NOTE]
> Ponieważ masz uprawnienia administratora na węzłach klastra podczas **ClusterCustomization** etapu, można użyć skryptu do wykonania operacji, takich jak zatrzymywanie i uruchamianie usług, w tym usług związanych z usługi Hadoop. Tak, jako część skryptu upewnij się, że usługi Ambari i innych usług związanych z usługi Hadoop zostały włączone i uruchomione przed skrypt zakończy się uruchamianie. Te usługi są wymagane do pomyślnie stwierdzenia kondycję i stan klastra podczas jego tworzenia. Jeśli zmienisz żadnej konfiguracji w klastrze, który ma wpływ na te usługi, należy użyć funkcji pomocnika, które są dostarczane. Aby uzyskać więcej informacji na temat funkcji pomocnika, zobacz [skrypty opracowywanie akcji skryptu do HDInsight][hdinsight-write-script].
>
>

Dane wyjściowe i dzienników błędów dla skryptu są przechowywane w domyślne konto magazynu, która została określona jako klaster. Dzienniki są przechowywane w tabeli o nazwie **u < \cluster-name-fragment >< \time-stamp > setuplog**. Są to agregować dzienniki z skrypt uruchamiany na wszystkie węzły (węzeł główny i węzły procesu roboczego) w klastrze.

Każdy klaster może akceptować wiele akcji skryptu, które są wywoływane w kolejności, w którym są określone. Skrypt można uruchomiono na węzeł główny i węzły procesu roboczego.

HDInsight udostępnia szereg skryptów do zainstalowania następujących składników w klastrach HDInsight:

| Name (Nazwa) | Skrypt |
| --- | --- |
| **Instalowanie platformy Spark** | `https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1`. Zobacz [instalacji i używania klastrów Spark on HDInsight][hdinsight-install-spark]. |
| **Instalowanie języka R** | `https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1`. Zobacz [instalacji i używania języka R w klastrach HDInsight](r-server/r-server-hdinsight-manage.md#install-additional-r-packages-on-the-cluster). |
| **Zainstalować platformę Solr** | `https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1`. Zobacz [instalacji i używania platformy Solr w HDInsight clusters](hdinsight-hadoop-solr-install.md). |
| **Zainstalować system Giraph** | `https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1`. Zobacz [instalacji i używania system Giraph w HDInsight clusters](hdinsight-hadoop-giraph-install.md). |
| **Wstępne ładowanie bibliotek technologii Hive** | `https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1`. Zobacz [Dodaj Hive bibliotek w klastrach HDInsight](hdinsight-hadoop-add-hive-libraries.md) |

## <a name="call-scripts-using-the-azure-portal"></a>Wywoływanie skryptów przy użyciu witryny Azure portal
**W witrynie Azure portal**

1. Rozpocznij tworzenie klastra zgodnie z opisem w [Tworzenie klastrów usługi Hadoop w HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
2. W obszarze Konfiguracja opcjonalna dla **akcji skryptu** bloku kliknij **Dodaj akcję skryptu** dostarczanie szczegółowych informacji o akcji skryptu, jak pokazano poniżej:

    ![Użyć akcji skryptu, aby dostosować klaster](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "użyj akcji skryptu, aby dostosować klastra")

    <table border='1'>
        <tr><th>Właściwość</th><th>Wartość</th></tr>
        <tr><td>Name (Nazwa)</td>
            <td>Określ nazwę dla akcji skryptu.</td></tr>
        <tr><td>Identyfikator URI skryptu</td>
            <td>Określ identyfikator URI do skryptu, który jest wywoływana w celu dostosowania do klastra. s</td></tr>
        <tr><td>Główny/procesu roboczego</td>
            <td>Określ węzły (**Head** lub **procesu roboczego**) uruchamiania skryptu dostosowania.</b>.
        <tr><td>Parametry</td>
            <td>Określ parametry, jeśli jest to wymagane przez skrypt.</td></tr>
    </table>

    Naciśnij klawisz ENTER, aby dodać więcej niż jedna akcja skryptu do zainstalowania wiele składników w klastrze.
3. Kliknij przycisk **wybierz** Aby zapisać konfigurację akcji skryptu, a następnie kontynuuj tworzenia klastra.

## <a name="call-scripts-using-azure-powershell"></a>Wywoływanie skryptów przy użyciu programu Azure PowerShell
Ten poniższy skrypt programu PowerShell pokazuje, jak zainstalować program w Windows oparte na klastrze HDInsight Spark.  

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

Po wyświetleniu monitu wprowadź poświadczenia dla klastra. Może upłynąć kilka minut, zanim został utworzony klaster.

## <a name="call-scripts-using-net-sdk"></a>Wywoływanie skryptów przy użyciu zestawu SDK platformy .NET
Poniższy przykład pokazuje, jak zainstalować program w Windows oparte na klastrze HDInsight Spark. Aby zainstalować inne oprogramowanie, należy zastąpić plik skryptu w kodzie.

**Aby utworzyć klaster usługi HDInsight za pomocą platformy Spark**

1. Tworzenie aplikacji konsolowej C# w programie Visual Studio.
2. Z poziomu konsoli Menedżera pakietów Nuget uruchom następujące polecenie.

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
4. Umieść kod w klasie następującym kodem:

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

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Pomoc techniczna dotycząca oprogramowania typu open-source używane w klastrach HDInsight
Usługa Microsoft Azure HDInsight jest elastyczna platforma, która umożliwia tworzenie aplikacji danych big data w chmurze przy użyciu ekosystem technologii open source utworzonych na platformie Hadoop. System Microsoft Azure oferuje ogólnego poziomu pomocy technicznej dla technologii open source, zgodnie z opisem w **zakres pomocy technicznej** części <a href="http://azure.microsoft.com/support/faq/" target="_blank">witryny sieci Web często zadawane pytania dotyczące pomocy technicznej Azure</a>. Usługa HDInsight zapewnia dodatkowy poziom pomocy technicznej dla niektórych składników, zgodnie z poniższym opisem.

Istnieją dwa typy składników typu open source, które są dostępne w usłudze HDInsight:

* **Wbudowane składniki** — te składniki są wstępnie zainstalowane w klastrach HDInsight i zapewnia podstawowe funkcje klastra. Na przykład Menedżer zasobów usługi YARN, język zapytań programu Hive (HiveQL) i biblioteki mahout dostępnych należą do tej kategorii. Pełną listę składniki klastra jest dostępna w [nowości w wersjach klastra Hadoop dostarczanych przez HDInsight?](hdinsight-component-versioning.md) </a>.
* **Składniki niestandardowe** —, jako użytkownik klastra, można zainstalować lub użyj w obciążenia dowolny składnik, dostępne w społeczności lub utworzonej przez użytkownika.

Wbudowane składniki są w pełni obsługiwane, a Microsoft Support pomoże wyizolować i rozwiązać problemy związane z tych składników.

> [!WARNING]
> Składniki dostarczony z klastrem usługi HDInsight są w pełni obsługiwane i Microsoft Support pomoże wyizolować i rozwiązać problemy związane z tych składników.
>
> Składniki niestandardowe otrzymują uzasadnioną komercyjnie pomoc techniczną, aby pomóc rozwiązać ten problem. Może to spowodować rozwiązuje problem lub pytaniem, dzięki którym można zaangażować dostępne kanały dla technologii "open source", gdzie znajduje się specjalistyczna dla tej technologii. Na przykład, istnieje wiele witryn społeczności, które mogą być używane, takie jak: [forum MSDN dotyczące HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Projektów Apache mieć witryny projektu na [ http://apache.org ](http://apache.org), na przykład: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).
>
>

Usługa HDInsight oferuje kilka sposobów używania niestandardowych składników. Niezależnie od tego, jak składnik jest używana lub zainstalowane w klastrze stosuje się ten sam poziom pomocy technicznej. Poniżej przedstawiono listę najbardziej typowych sposobów, że niestandardowe składniki mogą być używane w klastrach HDInsight:

1. Przesyłanie zadań — do Hadoop lub innych typów zadań, które wykonania lub użyć niestandardowych składników można przesyłać do klastra.
2. Dostosowywanie klastra — podczas tworzenia klastra, można określić dodatkowe ustawienia i składników niestandardowych, które będą instalowane w węzłach klastra.
3. Przykłady — dla popularnych składnikami niestandardowymi, firma Microsoft i inne osoby mogą zawierać przykładów, jak te składniki mogą być używane w klastrach HDInsight. Te przykłady są udostępniane bez pomocy technicznej.

## <a name="develop-script-action-scripts"></a>Tworzenie skryptów akcji skryptu
Zobacz [skrypty opracowywanie akcji skryptu do HDInsight][hdinsight-write-script].

## <a name="see-also"></a>Zobacz także
* [Tworzenie klastrów Hadoop w HDInsight] [ hdinsight-provision-cluster] zawiera instrukcje dotyczące sposobu tworzenia klastra usługi HDInsight przy użyciu niestandardowych opcji.
* [Tworzenie akcji skryptu skryptów dla HDInsight][hdinsight-write-script]
* [Instalowanie i używanie języka Spark w klastrach HDInsight][hdinsight-install-spark]
* [Instalowanie i korzystanie z platformy Solr w klastrach HDInsight](hdinsight-hadoop-solr-install.md).
* [Instalowanie i używanie systemu Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Etapy podczas tworzenia klastra"
