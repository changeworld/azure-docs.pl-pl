---
title: Uruchom systemu Linux na maszynie wirtualnej obliczeń węzłów — Azure Batch | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do przetwarzania obciążeń przetwarzania równoległego w pulach maszyn wirtualnych systemu Linux w usłudze Azure Batch.
services: batch
documentationcenter: python
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 24576a46b47b22ef447793b4105730ed2755701d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050634"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Aprowizowanie węzłów obliczeniowych systemu Linux w pulach usługi Batch

Azure Batch umożliwia uruchamianie obciążeń przetwarzania równoległego na maszynach wirtualnych z systemem Linux i Windows. Ten artykuł szczegółowo opisuje sposób tworzenia pul węzłów obliczeniowych systemu Linux w usłudze Batch przy użyciu zarówno [języka Python usługi Batch] [ py_batch_package] i [platformy .NET usługi Batch] [ api_net]bibliotek klienckich.

> [!NOTE]
> Pakiety aplikacji są obsługiwane we wszystkich pulach usługi Batch utworzonych po 5 lipca 2017 r. W pulach usługi Batch utworzonych między 10 marca 2016 r. a 5 lipca 2017 r. są one obsługiwane tylko w przypadku, gdy pula została utworzona za pomocą konfiguracji usługi w chmurze. Pule usługi Batch utworzone przed 10 marca 2016 r. nie obsługują pakietów aplikacji. Aby uzyskać więcej informacji o używaniu pakietów aplikacji do wdrażania aplikacji w węzłach usługi Batch, zobacz temat [Deploy applications to compute nodes with Batch application packages (Wdrażanie aplikacji w węzłach obliczeniowych za pomocą pakietów aplikacji usługi Batch)](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Konfiguracja maszyny wirtualnej
Podczas tworzenia puli węzłów obliczeniowych w usłudze Batch dostępne są dwie opcje, z którego można wybrać rozmiar węzła i systemu operacyjnego: Konfiguracja usług cloud Services i konfiguracji maszyny wirtualnej.

**Konfiguracja usług Cloud Services** oferuje *tylko* węzły obliczeniowe systemu Windows. Rozmiary węzłów obliczeniowych dostępne są wymienione w [rozmiary usług Cloud Services](../cloud-services/cloud-services-sizes-specs.md), i dostępnych systemów operacyjnych są wymienione w [wersji systemu operacyjnego gościa platformy Azure i zgodności zestawów SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Podczas tworzenia puli, która zawiera węzły usług Azure Cloud Services, określ rozmiar węzła i rodziny systemów operacyjnych, które są opisane w artykułach z wymienionych wcześniej. Dla węzłów obliczeniowych w pulach systemu Windows, usług w chmurze jest najczęściej używana.

**Konfiguracja maszyny wirtualnej** zawiera obrazy z systemem Linux i Windows dla węzłów obliczeniowych. Rozmiary węzłów obliczeniowych dostępne są wymienione w [rozmiary maszyn wirtualnych na platformie Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) i [rozmiary maszyn wirtualnych na platformie Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Podczas tworzenia puli, która zawiera węzły konfiguracji maszyny wirtualnej, należy określić rozmiar węzłów, odwołanie do obrazu maszyny wirtualnej i usługi Batch jednostkę SKU węzła agenta do zainstalowania w węzłach.

### <a name="virtual-machine-image-reference"></a>Odwołanie do obrazu maszyny wirtualnej
Używane przez usługę Batch [zestawy skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) zapewnienie węzłów obliczeniowych w konfiguracji maszyny wirtualnej. Można określić obrazu z [portalu Azure Marketplace][vm_marketplace], albo podać niestandardowy obraz, którego zostały przygotowane. Aby uzyskać więcej informacji o obrazach niestandardowych, zobacz [tworzenia puli za pomocą obrazu niestandardowego](batch-custom-images.md).

Podczas konfigurowania odwołanie do obrazu maszyny wirtualnej, należy określić właściwości obrazu maszyny wirtualnej. Następujące właściwości są wymagane, gdy tworzysz odwołanie do obrazu maszyny wirtualnej:

| **Właściwości odwołania do obrazu** | **Przykład** |
| --- | --- |
| Wydawca |Canonical |
| Oferta |UbuntuServer |
| SKU |14.04.4-LTS |
| Version |najnowsza |

> [!TIP]
> Dowiedz się więcej na temat tych właściwości i sposób wyświetlenia listy obrazów z witryny Marketplace w [wybierz obrazów maszyn wirtualnych systemu Linux na platformie Azure przy użyciu interfejsu wiersza polecenia lub programu PowerShell i Navigate](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Należy pamiętać, że nie wszystkie obrazy z Marketplace są obecnie zgodna z usługą Batch. Aby uzyskać więcej informacji, zobacz [jednostkę SKU węzła agenta](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Jednostkę SKU węzła agenta
Agent węzłów usługi Batch to program, który jest uruchamiany w każdym węźle w puli i udostępnia interfejs poleceń i kontroli między węzłem, a usługa Batch. Istnieją różne implementacje agenta węzła, znane jako jednostek SKU, dla różnych systemów operacyjnych. Zasadniczo podczas tworzenia konfiguracji maszyny wirtualnej należy najpierw określić odwołanie do obrazu maszyny wirtualnej, a następnie określ węzła agenta, aby zainstalować w obrazie. Zazwyczaj każdego agenta w węźle, jednostka SKU jest zgodny z wielu obrazów maszyn wirtualnych. Poniżej przedstawiono kilka przykładów jednostki SKU agenta węzła:

* batch.node.ubuntu 14.04
* batch.node.centos 7
* Batch.node.Windows amd64

> [!IMPORTANT]
> Nie wszystkie obrazy maszyn wirtualnych, które są dostępne w witrynie Marketplace są zgodne z obecnie dostępnych agentów usługi Batch w węźle. Korzystanie z zestawów SDK usługi Batch, aby wyświetlić listę jednostek SKU agenta węzła dostępne i obrazy maszyn wirtualnych, z którymi są one zgodne. Zobacz [obrazów listy maszyn wirtualnych](#list-of-virtual-machine-images) później w tym artykule, aby uzyskać więcej informacji i przykładów jak pobrać listę prawidłowe obrazów w czasie wykonywania.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Tworzenie puli systemu Linux: Batch Python
Poniższy fragment kodu przedstawia przykład sposobu użycia [biblioteki klienta usługi Microsoft Azure Batch dla środowiska Python] [ py_batch_package] tworzenie puli systemu Ubuntu Server węzłów obliczeniowych. Dokumentacja dotycząca modułów języka Python usługi Batch, można znaleźć w folderze [pakietu azure.batch] [ py_batch_docs] przy odczycie dokumentów.

Ten fragment kodu tworzy [ImageReference] [ py_imagereference] jawnie i określa każdego z jego właściwości (wydawcy, oferty, jednostki SKU i wersji). W kodzie produkcyjnym, jednak firma Microsoft zaleca użycie [list_node_agent_skus] [ py_list_skus] metodę, aby określić, a następnie wybierz z dostępnych obrazów i języka node agenta jednostki SKU kombinacje w czasie wykonywania.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Jak wspomniano wcześniej, zalecamy zamiast tworzenia [ImageReference] [ py_imagereference] jawnie, użyj [list_node_agent_skus] [ py_list_skus] metody na dynamiczne Wybieranie od kombinacji obraz agenta/Marketplace węzła obecnie obsługiwane. Poniższy fragment kodu języka Python pokazuje, jak użyć tej metody.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Tworzenie puli systemu Linux: Batch .NET
Poniższy fragment kodu przedstawia przykład sposobu użycia [platformy .NET usługi Batch] [ nuget_batch_net] biblioteki klienta, aby utworzyć pulę Ubuntu Server węzłów obliczeniowych. Możesz znaleźć [dokumentacja platformy .NET usługi Batch] [ api_net] w witrynie docs.microsoft.com.

Poniższy kod używa fragment [PoolOperations][net_pool_ops].[ ListNodeAgentSkus] [ net_list_skus] metodę, aby wybrać z listy aktualnie obsługiwane portalu Marketplace obrazu i języka node agenta jednostki SKU kombinacje. Ta technika jest pożądane, ponieważ lista obsługiwane kombinacje mogą ulec zmianie od czasu do czasu. Najczęściej obsługiwane kombinacje są dodawane.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Mimo że w poprzednim fragmencie kodu używa [PoolOperations][net_pool_ops].[ ListNodeAgentSkus] [ net_list_skus] metodę, aby dynamicznie listy, a następnie wybierz z obsługiwane kombinacje jednostki SKU obrazu i języka node agenta (zalecane), można również skonfigurować [ImageReference] [ net_imagereference] jawnie:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Lista obrazów maszyn wirtualnych
Poniższa tabela zawiera listę obrazów maszyn wirtualnych portalu Marketplace, które są zgodne z dostępnych agentów usługi Batch w węźle, datę ostatniej aktualizacji w tym artykule. Należy zauważyć, że ta lista nie jest ostateczną, ponieważ obrazy i agentów węzła może dodać lub usunąć w dowolnym momencie. Firma Microsoft zaleca, aby Twoje aplikacje usługi Batch i usługi zawsze używać [list_node_agent_skus] [ py_list_skus] (Python) lub [ListNodeAgentSkus] [ net_list_skus] () Usługi Batch — .NET) do określenia, a następnie wybierz z dostępnych jednostek SKU.

> [!WARNING]
> Poniższa lista mogą ulec zmianie w dowolnym momencie. Zawsze używaj **listy jednostkę SKU węzła agenta** metod w interfejsach API usługi Batch, aby wyświetlić listę zgodnych maszyn wirtualnych i jednostek SKU agenta węzła podczas uruchamiania zadań wsadowych.
>
>

| **Publisher** | **Oferta** | **Jednostka SKU obrazu** | **Wersja** | **Agenta węzła identyfikator jednostki SKU** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| partia | rendering-centos73 | Renderowanie | najnowsza | batch.node.centos 7 |
| partia | rendering-windows2016 | Renderowanie | najnowsza | Batch.node.Windows amd64 |
| Canonical | UbuntuServer | 16.04-LTS | najnowsza | batch.node.ubuntu 16.04 |
| Canonical | UbuntuServer | 14.04.5-LTS | najnowsza | batch.node.ubuntu 14.04 |
| credativ | Debian | 9 | najnowsza | batch.node.debian 9 |
| credativ | Debian | 8 | najnowsza | batch.node.debian 8 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | najnowsza | batch.node.centos 7 |
| microsoft-ads | standard-data-science-vm | standard-data-science-vm | najnowsza | Batch.node.Windows amd64 |
| microsoft-azure-batch | centos-container | 7-4 | najnowsza | batch.node.centos 7 |
| microsoft-azure-batch | centos-container-rdma | 7-4 | najnowsza | batch.node.centos 7 |
| microsoft-azure-batch | ubuntu-server-container | 16-04-lts | najnowsza | batch.node.ubuntu 16.04 |
| microsoft-azure-batch | ubuntu-server-container-rdma | 16-04-lts | najnowsza | batch.node.ubuntu 16.04 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter | najnowsza | Batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-smalldisk | najnowsza | Batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016 centrum danych za pomocą kontenerów | najnowsza | Batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | najnowsza | Batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | najnowsza | Batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | najnowsza | Batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter-smalldisk | najnowsza | Batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | najnowsza | Batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | Z dodatkiem SP1 smalldisk, 2008 R2 w- | najnowsza | Batch.node.Windows amd64 |
| OpenLogic | CentOS | 7.4 | najnowsza | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.4 | najnowsza | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.3 | najnowsza | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.1 | najnowsza | batch.node.centos 7 |
| Oracle | Oracle-Linux | 7.4 | najnowsza | batch.node.centos 7 |
| SUSE | SLES-HPC | 12-SP2 | najnowsza | batch.node.opensuse 42.1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>Łączenie z węzłami systemu Linux przy użyciu protokołu SSH
Podczas tworzenia lub podczas rozwiązywania problemów może okazać się konieczne, zaloguj się do węzłów w puli. W przeciwieństwie do węzłów obliczeniowych Windows nie można użyć protokołu RDP (Remote Desktop), połączyć się z węzłów systemu Linux. Zamiast tego usługa Batch umożliwia dostęp protokołu SSH w każdym węźle połączenia zdalnego.

Poniższy fragment kodu języka Python tworzy użytkownika w każdym węźle w puli, który jest wymagany do połączenia zdalnego. Go następnie wyświetla informacje o połączeniu protokołu secure shell (SSH) dla każdego węzła.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
        credentials,
        base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Poniżej przedstawiono przykładowe dane wyjściowe dla poprzedniego kodu dla puli, która zawiera cztery węzły z systemem Linux:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Zamiast hasła można określić publicznego klucza SSH podczas tworzenia użytkownika w węźle. W zestawie SDK języka Python za pomocą **ssh_public_key** parametru [ComputeNodeUser][py_computenodeuser]. Na platformie .NET, należy użyć [ComputeNodeUser][net_computenodeuser].[ Klucz publiczny SSH] [ net_ssh_key] właściwości.

## <a name="pricing"></a>Cennik
Usługa Azure Batch jest oparta na technologii usług Azure Cloud Services i Azure Virtual Machines. Sama usługa Batch jest oferowana bez ponoszenia kosztów, co oznacza, że opłaty są naliczane tylko za zasoby obliczeniowe że korzysta z rozwiązań usługi Batch. Po wybraniu **konfiguracji usług Cloud Services**, jest naliczana na podstawie [cennika usług Cloud Services] [ cloud_services_pricing] struktury. Po wybraniu **konfiguracji maszyny wirtualnej**, jest naliczana na podstawie [ceny maszyn wirtualnych] [ vm_pricing] struktury. 

W przypadku wdrożenia aplikacji w węzłach usługi Batch przy użyciu [pakiety aplikacji](batch-application-packages.md), są naliczane za zasoby usługi Azure Storage, używanie pakiety aplikacji. Ogólnie rzecz biorąc kosztów usługi Azure Storage są minimalne. 

## <a name="next-steps"></a>Kolejne kroki

[Przykłady kodu Python] [ github_samples_py] w [azure-batch-samples] [ github_samples] repozytorium w serwisie GitHub zawiera skrypty, które pokazują, jak wykonać typowe operacje wsadowe, takie jak puli, zadań i tworzenie zadań. [README] [ github_py_readme] dołączony Python przykładów zawiera szczegółowe informacje na temat zainstalować wymagane pakiety.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure-sdk-for-python.readthedocs.io/batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: https://docs.microsoft.com/python/api/azure.batch.models.computenodeuser
[py_imagereference]: https://docs.microsoft.com/python/api/azure.mgmt.batch.models.imagereference
[py_list_skus]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
