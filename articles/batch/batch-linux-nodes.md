---
title: Węzły - partii zadań Azure obliczeniowe uruchamiania systemu Linux na maszynie wirtualnej | Dokumentacja firmy Microsoft
description: Informacje o sposobie przetwarzania obciążeń obliczeniowych równoległe w pulach maszyn wirtualnych systemu Linux w partii zadań Azure.
services: batch
documentationcenter: python
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a9aa896bfc4c860c87757f9379fc44cc5ee8d18a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Zapewnij węzły obliczeniowe systemu Linux w puli partii

Partia zadań Azure służy do uruchamiania obciążeń obliczeniowych równoległe w przypadku maszyn wirtualnych zarówno systemu Windows, jak i Linux. W tym artykule szczegółowo przedstawiają, jak utworzyć pule węzłów obliczeniowych systemu Linux w usługa partia zadań za pomocą obu [Python partii] [ py_batch_package] i [partiami platformy .NET] [ api_net] bibliotek klienckich.

> [!NOTE]
> Pakiety aplikacji są obsługiwane we wszystkich pulach usługi Batch utworzonych po 5 lipca 2017 r. W pulach usługi Batch utworzonych między 10 marca 2016 r. a 5 lipca 2017 r. są one obsługiwane tylko w przypadku, gdy pula została utworzona za pomocą konfiguracji usługi w chmurze. Pule usługi Batch utworzone przed 10 marca 2016 r. nie obsługują pakietów aplikacji. Aby uzyskać więcej informacji o używaniu pakietów aplikacji do wdrażania aplikacji w węzłach usługi Batch, zobacz temat [Deploy applications to compute nodes with Batch application packages (Wdrażanie aplikacji w węzłach obliczeniowych za pomocą pakietów aplikacji usługi Batch)](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Konfiguracja maszyny wirtualnej
Po utworzeniu puli węzłów obliczeniowych w partii, masz dwie opcje, które można wybierać rozmiaru węzła i systemu operacyjnego: Konfiguracja usług w chmurze i konfiguracji maszyny wirtualnej.

**Konfiguracja usług Cloud Services** oferuje *tylko* węzły obliczeniowe systemu Windows. Rozmiary węzła obliczeń dostępne są wymienione w [rozmiary dla usług w chmurze](../cloud-services/cloud-services-sizes-specs.md), i dostępnych systemów operacyjnych znajduje się w [wersji systemu operacyjnego gościa Azure i zgodność pakietu SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Podczas tworzenia puli, która zawiera węzły usługi w chmurze Azure należy określić rozmiaru węzła i rodziny systemów operacyjnych, które zostały opisane w artykułach opisane powyżej. Dla węzły obliczeniowe pule systemu Windows, usługi w chmurze jest najczęściej używana.

**Konfiguracja maszyny wirtualnej** zawiera obrazy zarówno systemu Windows, jak i Linux dla węzły obliczeniowe. Rozmiary węzła obliczeń dostępne są wymienione w [rozmiary maszyn wirtualnych na platformie Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) i [rozmiary maszyn wirtualnych na platformie Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (system Windows). Podczas tworzenia puli, która zawiera węzły w konfiguracji maszyny wirtualnej, należy określić rozmiar węzłów, odwołanie do obrazu maszyny wirtualnej i agenta węzła partii jednostka SKU można zainstalować na węzłach.

### <a name="virtual-machine-image-reference"></a>Odwołanie do obrazu maszyny wirtualnej
Używane przez usługę partii [zestawy skalowania maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) zapewnienie węzły obliczeniowe systemu Linux. Można określić obrazu z [portalu Azure Marketplace][vm_marketplace], lub podaj niestandardowego obrazu, które zostały przygotowane. Aby uzyskać szczegółowe informacje o obrazach niestandardowych, zobacz [Tworzenie rozbudowanych rozwiązań przetwarzania równoległego przy użyciu usługi Batch](batch-api-basics.md#pool).

Po skonfigurowaniu odwołanie do obrazu maszyny wirtualnej, należy określić właściwości obrazu maszyny wirtualnej. Po utworzeniu odwołanie do obrazu maszyny wirtualnej, wymagane są następujące właściwości:

| **Obraz właściwości odwołania** | **Przykład** |
| --- | --- |
| Wydawca |Canonical |
| Oferta |UbuntuServer |
| SKU |14.04.4-LTS |
| Wersja |najnowsza |

> [!TIP]
> Dowiedz się więcej na temat tych właściwości i sposób wyświetlania obrazów Marketplace w [Nawigacja i wybierz obrazy maszyny wirtualnej systemu Linux na platformie Azure z interfejsu wiersza polecenia lub środowiska PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Należy pamiętać, że nie wszystkie obrazy Marketplace są obecnie zgodne z partii. Aby uzyskać więcej informacji, zobacz [agenta węzła jednostki SKU](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Agent węzła jednostki SKU
Agenta węzła partii to program, który jest uruchamiany na każdym węźle w puli i udostępnia interfejs polecenia i kontroli między węzłem a usługa partia zadań. Istnieją różne implementacje agenta węzła, znany jako jednostki SKU, dla różnych systemów operacyjnych. Zasadniczo podczas tworzenia konfiguracji maszyny wirtualnej, najpierw określ odwołanie do obrazu maszyny wirtualnej, a następnie określ agenta węzeł, aby zainstalować w obrazie. Zazwyczaj każdego agenta węzła, jednostka SKU jest zgodny z wielu obrazów maszyny wirtualnej. Oto kilka przykładów agenta węzła jednostki SKU:

* batch.node.ubuntu 14.04
* Batch.node.centos 7
* Batch.node.Windows amd64

> [!IMPORTANT]
> Nie wszystkie obrazy maszyny wirtualnej, które są dostępne w witrynie Marketplace są zgodne z aktualnie dostępnych agentów węzła partii. Korzystaj z zestawów SDK partii, aby wyświetlić listę agenta węzła dostępne jednostki SKU i obrazy maszyny wirtualnej, z którymi są zgodne. Zobacz [obrazy listy maszyny wirtualnej](#list-of-virtual-machine-images) opisaną w tym artykule, aby uzyskać więcej informacji i przykłady można pobrać listy prawidłowy obrazów w czasie wykonywania.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Utwórz pulę Linux: Python partii
Poniższy fragment kodu przedstawia przykład sposobu użycia [Biblioteka klienta usługi partia zadań Microsoft Azure dla języka Python] [ py_batch_package] można utworzyć puli Ubuntu Server węzłów obliczeniowych. Dokumentację referencyjną dla modułu Python partii znajduje się w temacie [pakietu azure.batch] [ py_batch_docs] przy odczycie dokumentów.

Ta Wstawka kodu tworzy [elementu ImageReference] [ py_imagereference] jawnie i określa każdego z jego właściwości (Oferta, jednostka SKU, wersja wydawcy). W kodzie produkcyjnym, jednak zaleca się używanie [list_node_agent_skus] [ py_list_skus] metodę, aby określić i wybrać jeden z dostępnych obrazów i węzła agenta SKU kombinacji w czasie wykonywania.

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
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

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

Jak wspomniano wcześniej, zalecamy, aby zamiast tworzenia [elementu ImageReference] [ py_imagereference] jawnie, użyj [list_node_agent_skus] [ py_list_skus] metody na dynamiczne Wybieranie z kombinacji obrazu agenta/Marketplace węźle aktualnie obsługiwane. Poniższy fragment kodu języka Python pokazano, jak użyć tej metody.

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

## <a name="create-a-linux-pool-batch-net"></a>Utwórz pulę Linux: partiami platformy .NET
Poniższy fragment kodu przedstawia przykład sposobu użycia [partiami platformy .NET] [ nuget_batch_net] biblioteki klienta, aby utworzyć pulę Ubuntu Server węzłów obliczeniowych. Można znaleźć [dokumentacji partiami platformy .NET] [ api_net] w witrynie MSDN.

Poniższy kod używa fragment [PoolOperations][net_pool_ops].[ ListNodeAgentSkus] [ net_list_skus] metoda, aby wybrać z listy aktualnie obsługiwane Marketplace obrazu i węzła agenta SKU kombinacje. Ta technika jest pożądane, ponieważ lista obsługiwanej kombinacji może ulec zmianie od czasu do czasu. Najczęściej są dodawane obsługiwanej kombinacji.

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

Chociaż korzysta z poprzedniego fragmentu [PoolOperations][net_pool_ops].[ ListNodeAgentSkus] [ net_list_skus] metody dynamicznie listy i wybierz z obsługiwane kombinacje jednostka SKU obrazu i węzła agenta (zalecane), można również skonfigurować [elementu ImageReference] [ net_imagereference] jawnie:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Lista obrazów maszyny wirtualnej
W poniższej tabeli wymieniono Marketplace obrazy maszyny wirtualnej, które są zgodne z dostępnych agentów węzła partii datę ostatniej aktualizacji w tym artykule. Należy pamiętać, że ta lista nie jest ostatecznym, ponieważ obrazów i agentów węzła może dodać lub usunąć w dowolnej chwili. Firma Microsoft zaleca, aby partii aplikacje i usługi zawsze używać [list_node_agent_skus] [ py_list_skus] (Python) i [ListNodeAgentSkus] [ net_list_skus] (partii .NET), aby ustalić i wybierz z aktualnie dostępne jednostki SKU.

> [!WARNING]
> Poniższa lista może zmienić w dowolnym momencie. Zawsze używaj **agent węzła listy SKU** metod w interfejsach API partii do listy kompatybilnej maszynie wirtualnej i agenta węzła jednostki SKU podczas wykonywania zadań wsadowych.
>
>

| **Wydawca** | **Oferta** | **Jednostka SKU obrazu** | **Wersja** | **Agent węzła identyfikator jednostki SKU** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| Canonical | UbuntuServer | 14.04.5-LTS | najnowsza | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 16.04.0-LTS | najnowsza | batch.node.ubuntu 16.04 |
| Credativ | Debian | 8 | najnowsza | batch.node.debian 8 |
| OpenLogic | CentOS | 7.0 | najnowsza | Batch.node.centos 7 |
| OpenLogic | CentOS | 7.1 | najnowsza | Batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.1 | najnowsza | Batch.node.centos 7 |
| OpenLogic | CentOS | 7.2 | najnowsza | Batch.node.centos 7 |
| Oracle | Oracle-Linux | 7.0 | najnowsza | Batch.node.centos 7 |
| Oracle | Oracle-Linux | 7.2 | najnowsza | Batch.node.centos 7 |
| SUSE | openSUSE | 13.2 | najnowsza | batch.node.opensuse 13.2 |
| SUSE | openSUSE-Leap | 42.1 | najnowsza | batch.node.opensuse 42.1 |
| SUSE | SLES | 12-SP1 | najnowsza | batch.node.opensuse 42.1 |
| SUSE | SLES-HPC | 12-SP1 | najnowsza | batch.node.opensuse 42.1 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | najnowsza | Batch.node.centos 7 |
| microsoft-ads | Standard danych nauki vm | Standard danych nauki vm | najnowsza | Batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008 R2 SP1 | najnowsza | Batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | najnowsza | Batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | najnowsza | Batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | Centrum danych 2016 | najnowsza | Batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016 centrum danych z kontenerów | najnowsza | Batch.node.Windows amd64 |

## <a name="connect-to-linux-nodes-using-ssh"></a>Podłącz do węzłów systemu Linux przy użyciu protokołu SSH
Podczas tworzenia lub podczas rozwiązywania problemów może być konieczne logować się do węzłów w puli. W przeciwieństwie do węzłów obliczeniowych systemu Windows nie można użyć protokołu RDP (Remote Desktop) nawiązywania połączenia z węzłami systemu Linux. Zamiast tego usługa partia zadań umożliwia SSH dostępu w każdym węźle dla połączenia zdalnego.

Poniższy fragment kodu języka Python tworzy użytkownika w każdym węźle w puli, który jest wymagany dla połączenia zdalnego. Wyświetla informacje o połączeniu bezpiecznej powłoki (SSH) dla każdego węzła.

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

Oto przykładowe dane wyjściowe poprzedniego kodu dla puli, która zawiera cztery węzły Linux:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Zamiast hasła można określić klucz publiczny SSH, podczas tworzenia użytkownika w węźle. W zestawie SDK Python za pomocą **ssh_public_key** parametru [ComputeNodeUser][py_computenodeuser]. W środowisku .NET, należy użyć [ComputeNodeUser][net_computenodeuser].[ Parametry SshPublicKey] [ net_ssh_key] właściwości.

## <a name="pricing"></a>Cennik
Partia zadań Azure jest oparty na technologii usług Azure Cloud Services i maszyn wirtualnych platformy Azure. Sama usługa partii jest oferowane bezpłatnie, co oznacza, że są naliczane tylko za zasoby obliczeniowe, w że korzystać z własnych rozwiązań partii. Po wybraniu **konfiguracji usługi w chmurze**, są naliczane na podstawie [cennik usługi w chmurze] [ cloud_services_pricing] struktury. Po wybraniu **konfiguracji maszyny wirtualnej**, są naliczane na podstawie [cennik maszyn wirtualnych] [ vm_pricing] struktury. 

W przypadku wdrożenia aplikacji węzły partii przy użyciu [pakietów aplikacji](batch-application-packages.md), naliczane są również opłaty za zasoby usługi Azure Storage czy używać pakietów aplikacji. Ogólnie rzecz biorąc koszty usługi Azure Storage są minimalne. 

## <a name="next-steps"></a>Kolejne kroki
### <a name="batch-python-tutorial"></a>Samouczek języka Python dla usługi Batch
Więcej informacji na temat samouczek dotyczący sposobu pracy z partii przy użyciu języka Python, zapoznaj się z [Rozpoczynanie pracy z klientem Python usługi partia zadań Azure](batch-python-tutorial.md). Pomocnik jego [przykładowy kod] [ github_samples_pyclient] obejmuje funkcję Pomocnika `get_vm_config_for_distro`, który pokazuje innej techniki, aby uzyskać konfigurację maszyny wirtualnej.

### <a name="batch-python-code-samples"></a>Przykłady kodu Python partii
[Przykłady kodu Python] [ github_samples_py] w [azure partii próbek] [ github_samples] repozytorium w usłudze GitHub zawierać skrypty, które opisano sposób wykonywania typowych operacji wsadowych, takie jak pula, zadania i tworzenia zadań. [README] [ github_py_readme] dołączony Python próbek zawiera szczegółowe informacje dotyczące sposobu zainstalowania wymaganych pakietów.

### <a name="batch-forum"></a>Forum usługi Batch
[Forum usługi partia zadań Azure] [ forum] w witrynie MSDN jest doskonałym miejscem do omówienia partii i zadać pytania dotyczące usługi. Odczyt przydatne "przypięte" ogłoszeń i opublikuj swoje pytania powstałych podczas tworzenia własnych rozwiązań partii.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
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
[nuget_batch_net]: https://www.nuget.org/packages/Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
