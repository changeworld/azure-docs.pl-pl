---
title: Uruchamianie systemu Linux na węzłach obliczeniowych maszyny wirtualnej — usługa Azure Batch | Dokumenty firmy Microsoft
description: Dowiedz się, jak przetwarzać równoległe obciążenia obliczeniowe na pulach maszyn wirtualnych systemu Linux w usłudze Azure Batch.
services: batch
documentationcenter: python
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 977504f41e93e37ae2c5ce9bdb1182a1cfe0a3fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252287"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Aprowizuj węzły obliczeniowe systemu Linux w pulach wsadowych

Usługi Azure Batch można używać do uruchamiania równoległych obciążeń obliczeniowych na maszynach wirtualnych systemu Linux i Windows. W tym artykule opisano sposób tworzenia pul węzłów obliczeniowych systemu Linux w usłudze Batch przy użyciu bibliotek klienckich [Batch Python][py_batch_package] i [Batch .NET.][api_net]

> [!NOTE]
> Pakiety aplikacji są obsługiwane we wszystkich pulach usługi Batch utworzonych po 5 lipca 2017 r. W pulach usługi Batch utworzonych między 10 marca 2016 r. a 5 lipca 2017 r. są one obsługiwane tylko w przypadku, gdy pula została utworzona za pomocą konfiguracji usługi w chmurze. Pule usługi Batch utworzone przed 10 marca 2016 r. nie obsługują pakietów aplikacji. Aby uzyskać więcej informacji o używaniu pakietów aplikacji do wdrażania aplikacji w węzłach usługi Batch, zobacz temat [Deploy applications to compute nodes with Batch application packages (Wdrażanie aplikacji w węzłach obliczeniowych za pomocą pakietów aplikacji usługi Batch)](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Konfiguracja maszyny wirtualnej
Podczas tworzenia puli węzłów obliczeniowych w usłudze Batch dostępne są dwie opcje wyboru rozmiaru węzła i systemu operacyjnego: Konfiguracja usług w chmurze i Konfiguracja maszyny wirtualnej.

**Konfiguracja usług Cloud Services** oferuje *tylko* węzły obliczeniowe systemu Windows. Dostępne rozmiary węzłów obliczeniowych są wymienione w [obszarze Rozmiary usług w chmurze,](../cloud-services/cloud-services-sizes-specs.md)a dostępne systemy operacyjne są wymienione w [wersjach systemu operacyjnego Azure Guest I macierzy zgodności SDK.](../cloud-services/cloud-services-guestos-update-matrix.md) Podczas tworzenia puli, która zawiera węzły usług w chmurze azure, należy określić rozmiar węzła i rodziny systemu operacyjnego, które są opisane w wcześniej wymienionych artykułów. W przypadku pul węzłów obliczeniowych systemu Windows usługi w chmurze są najczęściej używane.

**Konfiguracja maszyny wirtualnej** zapewnia obrazy systemu Linux i Windows dla węzłów obliczeniowych. Dostępne rozmiary węzłów obliczeniowych są wymienione w [rozmiary dla maszyn wirtualnych w platformie Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) i [rozmiary dla maszyn wirtualnych na platformie Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Podczas tworzenia puli zawierającej węzły konfiguracji maszyny wirtualnej należy określić rozmiar węzłów, odwołanie do obrazu maszyny wirtualnej i jednostkę SKU agenta węzła usługi Batch, która ma zostać zainstalowana w węzłach.

### <a name="virtual-machine-image-reference"></a>Odwołanie do obrazu maszyny wirtualnej

Usługa Batch używa [zestawów skalowania maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) w celu zapewnienia węzłów obliczeniowych w konfiguracji maszyny wirtualnej. Można określić obraz z [portalu Azure Marketplace][vm_marketplace]lub podać obraz niestandardowy, który został przygotowany. Aby uzyskać więcej informacji na temat obrazów niestandardowych, zobacz [Tworzenie puli za pomocą Galerii obrazów udostępnionych](batch-sig-images.md).

Podczas konfigurowania odwołania obrazu maszyny wirtualnej, należy określić właściwości obrazu maszyny wirtualnej. Podczas tworzenia odwołania do obrazu maszyny wirtualnej wymagane są następujące właściwości:

| **Właściwości odwołania do obrazu** | **Przykład** |
| --- | --- |
| Wydawca |Canonical |
| Oferta |UbuntuServer |
| SKU |18.04-LTS |
| Wersja |najnowsza |

> [!TIP]
> Możesz dowiedzieć się więcej o tych właściwościach i jak wyświetlić listę obrazów portalu Marketplace w [obszarze Nawigacja i wybrać obrazy maszyn wirtualnych systemu Linux na platformie Azure za pomocą interfejsu wiersza polecenia lub programu PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Należy zauważyć, że nie wszystkie obrazy z marketplace są obecnie zgodne z usługi Batch. Aby uzyskać więcej informacji, zobacz [SKU agenta węzła](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Jednostka SKU agenta węzła
Agent węzła usługi Batch to program, który działa w każdym węźle w puli i udostępnia interfejs polecenia i kontroli między węzłem a usługą Batch. Istnieją różne implementacje agenta węzła, znany jako jednostki SKU, dla różnych systemów operacyjnych. Zasadniczo podczas tworzenia konfiguracji maszyny wirtualnej najpierw należy określić odwołanie do obrazu maszyny wirtualnej, a następnie określić agenta węzła do zainstalowania na obrazie. Zazwyczaj każda jednostka SKU agenta węzła jest zgodna z wieloma obrazami maszyn wirtualnych. Oto kilka przykładów jednostek SKU agenta węzła:

* batch.node.ubuntu 18.04
* batch.node.centos 7
* plik batch.node.windows amd64

> [!IMPORTANT]
> Nie wszystkie obrazy maszyn wirtualnych, które są dostępne w portalu Marketplace są zgodne z aktualnie dostępnych agentów węzła usługi Batch. Użyj zestawy SDK partii, aby wyświetlić listę dostępnych jednostek SKU agenta węzła i obrazów maszyn wirtualnych, z którymi są zgodne. Zobacz [listę obrazów maszyny wirtualnej](#list-of-virtual-machine-images) w dalszej części tego artykułu, aby uzyskać więcej informacji i przykłady sposobu pobierania listy prawidłowych obrazów w czasie wykonywania.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Tworzenie puli linuksa: Batch Python
Poniższy fragment kodu zawiera przykład użycia [biblioteki klienta wsadowej platformy Microsoft Azure dla języka Python][py_batch_package] do utworzenia puli węzłów obliczeniowych serwera Ubuntu. Dokumentacja referencyjna dla modułu Batch Python można znaleźć w [pakiecie azure.batch][py_batch_docs] na Odczyt dokumentów.

Ten fragment kodu tworzy [ImageReference][py_imagereference] jawnie i określa każdą z jego właściwości (wydawca, oferta, jednostka SKU, wersja). W kodzie produkcyjnym zaleca się jednak użycie metody [list_supported_images][py_list_supported_images] do określenia i wybrania spośród dostępnych kombinacji jednostek SKU obrazów i węzłów w czasie wykonywania.

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
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Jak wspomniano wcześniej, zaleca się, aby zamiast tworzenia [ImageReference][py_imagereference] jawnie, należy użyć [list_supported_images][py_list_supported_images] metody dynamicznie wybrać z aktualnie obsługiwanych kombinacji agenta węzła/marketplace. Poniższy fragment kodu języka Python pokazuje, jak używać tej metody.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Tworzenie puli linuksa: partia .NET
Poniższy fragment kodu przedstawia przykład użycia biblioteki klienta [Batch .NET][nuget_batch_net] do utworzenia puli węzłów obliczeniowych serwera Ubuntu Server. [Dokumentację referencyjną Batch .NET][api_net] można znaleźć na stronie docs.microsoft.com.

Poniższy fragment kodu używa [PoolOperations][net_pool_ops]. [ListSupportedImages][net_list_supported_images] metoda, aby wybrać z listy aktualnie obsługiwanych kombinacji sku agenta obrazu i węzła marketplace. Ta technika jest pożądana, ponieważ lista obsługiwanych kombinacji może się zmieniać od czasu do czasu. Najczęściej dodawane są obsługiwane kombinacje.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

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

Chociaż poprzedni fragment kodu używa [PoolOperations][net_pool_ops]. [ListSupportedImages][net_list_supported_images] metoda dynamicznie listy i wybierz z obsługiwanych kombinacji sku agenta obrazu i węzła (zalecane), można również skonfigurować [ImageReference][net_imagereference] jawnie:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Lista obrazów maszyn wirtualnych
Aby uzyskać listę wszystkich obsługiwanych obrazów maszyn wirtualnych portalu Marketplace dla usługi Batch i odpowiadających im agentów węzłów, należy skorzystać z [list_supported_images][py_list_supported_images] (Python), [ListSupportedImages][net_list_supported_images] (Batch .NET) lub odpowiedniego interfejsu API w wybranym języku zestawie SDK.

## <a name="connect-to-linux-nodes-using-ssh"></a>Łączenie się z węzłami systemu Linux przy użyciu funkcji SSH
Podczas tworzenia lub rozwiązywania problemów może okazać się konieczne zalogowanie się do węzłów w puli. W przeciwieństwie do węzłów obliczeniowych systemu Windows nie można używać protokołu RDP (Remote Desktop Protocol) do łączenia się z węzłami systemu Linux. Zamiast tego usługa Batch umożliwia dostęp SSH w każdym węźle dla połączenia zdalnego.

Poniższy fragment kodu języka Python tworzy użytkownika w każdym węźle w puli, która jest wymagana do połączenia zdalnego. Następnie drukuje informacje o połączeniu bezpiecznej powłoki (SSH) dla każdego węzła.

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

Oto przykładowe dane wyjściowe dla poprzedniego kodu dla puli, która zawiera cztery węzły systemu Linux:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Zamiast hasła można określić klucz publiczny SSH podczas tworzenia użytkownika w węźle. W module SDK języka Python należy użyć parametru **ssh_public_key** w [pliku ComputeNodeUser][py_computenodeuser]. W .NET użyj [pliku ComputeNodeUser][net_computenodeuser]. [SshPublicKey][net_ssh_key] właściwość.

## <a name="pricing"></a>Cennik
Usługa Azure Batch jest oparta na usługach w chmurze azure i technologii Azure Virtual Machines. Sama usługa Batch jest oferowana bezpłatnie, co oznacza, że opłaty są naliczane tylko za zasoby obliczeniowe (i związane z nimi koszty), które zużyją rozwiązania usługi Batch. Po wybraniu **opcji Konfiguracja usług w chmurze**naliczana jest opłata na podstawie struktury [cenowej usług w chmurze.][cloud_services_pricing] Po wybraniu **opcji Konfiguracja maszyny wirtualnej**naliczana jest opłata na podstawie struktury [cenowej Maszyny wirtualne.][vm_pricing]

Jeśli wdrożysz aplikacje w węzłach usługi Batch przy użyciu [pakietów aplikacji,](batch-application-packages.md)opłaty są również naliczane za zasoby usługi Azure Storage, które korzystają z pakietów aplikacji.

## <a name="next-steps"></a>Następne kroki

[Przykłady kodu języka Python][github_samples_py] w repozytorium [przykładów azure][github_samples] w usłudze GitHub zawierają skrypty, które pokazują, jak wykonywać typowe operacje usługi Batch, takie jak pula, zadanie i tworzenie zadań. [Readme,][github_py_readme] który towarzyszy przykłady języka Python ma szczegółowe informacje na temat instalowania wymaganych pakietów.

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
[net_computenodeuser]: /dotnet/api/microsoft.azure.batch.computenodeuser?view=azure-dotnet
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_supported_images]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey?view=azure-dotnet#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure.github.io/azure-sdk-for-python/ref/Batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_supported_images]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
