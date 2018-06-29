---
title: Kontener obciążenie partii zadań Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie uruchamiania aplikacji z kontenera obrazów w partii zadań Azure.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 06/04/2018
ms.author: danlep
ms.openlocfilehash: 8ef9d5a8e5212f6715769eecf4fde92a6d0b9d44
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060521"
---
# <a name="run-container-applications-on-azure-batch"></a>Uruchamianie aplikacji kontenera w partii zadań Azure

Partia zadań Azure umożliwia uruchamiania i skalowania dużej liczby partii przetwarzania zadania na platformie Azure. Partii zadań można uruchamiać bezpośrednio na maszyny wirtualne (węzłów) w puli partii, ale można również skonfigurować pulę partii do uruchamiania zadań w kontenerach zgodnego Docker na węzłach. W tym artykule przedstawiono sposób tworzenia puli węzłów obliczeniowych, które obsługują uruchomione zadania kontenera, a następnie uruchom zadania kontenera w puli. 

Należy zapoznać się z kontenera koncepcji i sposobu tworzenia puli partii i zadania. Przykłady kodu za pomocą partiami platformy .NET i zestawy SDK Python. Tworzenie puli partii włączone kontenera i uruchamiania zadań kontenera, można użyć innych zestawów SDK partii i narzędzi, w tym portalu Azure.

## <a name="why-use-containers"></a>Dlaczego warto używać kontenery?

Używanie kontenerów zapewnia prosty sposób do uruchamiania zadań wsadowych bez konieczności zarządzania środowiska i zależności do uruchamiania aplikacji. Kontenery wdrażania aplikacji jako lekkie, przenośny samowystarczalne jednostki, które można uruchomić w kilku różnych środowiskach. Na przykład użytkownik może kompilacji lokalnie kontener testu, a następnie Przekaż obraz kontenera w rejestrze w Azure lub gdziekolwiek indziej. Model wdrożenia kontenera zapewnia, że środowisko uruchomieniowe aplikacji jest zawsze poprawnie zainstalowana i skonfigurowana wszędzie tam, gdzie hostowania aplikacji. Na podstawie kontenera zadania w partii można również korzystać z funkcji zadań niebędących kontenerami, w tym pakiety aplikacji i zarządzanie plikami zasobów i plików wyjściowych. 

## <a name="prerequisites"></a>Wymagania wstępne

* **Wersje zestawu SDK**: wsadowego zestawów SDK pomocy technicznej kontener obrazów z następujących wersji:
    * Interfejs API REST partii wersji 2017-09-01.6.0
    * Wsadowe .NET SDK w wersji 8.0.0
    * Zestaw SDK Python usługi partia zadań w wersji 4.0
    * Wsadowe Java SDK w wersji 3.0
    * Wsadowe Node.js SDK w wersji 3.0

* **Konta**: W Twojej subskrypcji platformy Azure, musisz utworzyć konto usługi partia zadań i opcjonalnie konta usługi Azure Storage.

* **Obsługiwane obrazu maszyny Wirtualnej**: kontenery są obsługiwane tylko w pulach utworzone za pomocą konfiguracji maszyny wirtualnej z obrazów szczegółowo opisane w poniższej sekcji, "obsługiwane obrazy maszyny wirtualnej". Jeśli podasz niestandardowego obrazu, zobacz zagadnień opisane w poniższej sekcji i wymagań dotyczących [umożliwiają utworzenie puli maszyn wirtualnych zarządzanych niestandardowego obrazu](batch-custom-images.md). 

### <a name="limitations"></a>Ograniczenia

* Wsadowe obsługuje funkcję RDMA tylko kontenery z systemem Linux pule.

## <a name="supported-virtual-machine-images"></a>Obrazy obsługiwanych maszyn wirtualnych

Użyj jednej z następujących obsługiwane systemu Windows lub Linux obrazów, aby utworzyć pulę maszyny wirtualnej obliczeniowe węzłów dla obciążeń kontenera. Aby uzyskać więcej informacji o obrazach Marketplace, które są zgodne z partii, zobacz [listy obrazów maszyny wirtualnej](batch-linux-nodes.md#list-of-virtual-machine-images). 

### <a name="windows-images"></a>Obrazy systemu Windows

W przypadku obciążeń kontenera systemu Windows partii obecnie obsługuje **systemu Windows Server 2016 Datacenter z kontenerami** obrazu w portalu Azure Marketplace. W systemie Windows, obsługiwane są tylko obrazy kontenera Docker.

Można również utworzyć niestandardowe obrazy z maszyn wirtualnych z systemem Windows Docker.

### <a name="linux-images"></a>Obrazy systemu Linux

W przypadku obciążeń kontenera Linux partii obecnie obsługuje następujące obrazy Linux opublikowanych przez partia zadań Microsoft Azure w portalu Azure Marketplace:

* **CentOS dla pul kontenera partii zadań Azure**

* **CentOS (ze sterownikami RDMA) dla pul kontenera partii zadań Azure**

* **Ubuntu Server dla pul kontenera partii zadań Azure**

* **Ubuntu Server (ze sterownikami RDMA) dla pul kontenera partii zadań Azure**

Te obrazy są obsługiwane tylko do użytku w pulach partii zadań Azure. Ich funkcji:

* Wstępnie zainstalowane [Moby](https://github.com/moby/moby) kontener środowiska uruchomieniowego 

* Wstępnie zainstalowane NVIDIA wersji sterowników procesora GPU, aby usprawnić wdrażanie na maszynach wirtualnych Azure N-series

* Obrazy z lub bez wstępnie zainstalowane sterowniki RDMA; te sterowniki zezwala węzłom puli na dostęp do sieci Azure RDMA po wdrożeniu na rozmiarów maszyn wirtualnych z funkcją RDMA  

Można również utworzyć niestandardowe obrazy z maszyn wirtualnych uruchomionych Docker na jedną dystrybucje systemu Linux, które nie są zgodne z partii. Jeśli użytkownik chce dostarczyć własny obraz niestandardowy Linux, zobacz instrukcje w [umożliwiają utworzenie puli maszyn wirtualnych zarządzanych niestandardowego obrazu](batch-custom-images.md).

Obsługa Docker na obraz niestandardowy, można zainstalować [Docker Community Edition (CE)](https://www.docker.com/community-edition) lub [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Dodatkowe zagadnienia dotyczące korzystania z niestandardowego obrazu systemu Linux:

* Aby móc korzystać z wydajności procesora GPU rozmiary serii N Azure przy użyciu obrazu niestandardowego, należy wstępnie zainstalować sterowniki NVIDIA. Ponadto należy zainstalować narzędzia aparat Docker dla jednostki GPU NVIDIA, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Dostęp do sieci Azure RDMA, należy użyć rozmiaru maszyny Wirtualnej z funkcją RDMA. Niezbędne sterowniki RDMA są zainstalowane w CentOS HPC i Ubuntu obrazów obsługiwane przez partię. Dodatkowej konfiguracji mogą być wymagane do uruchamiania obciążeń MPI. Zobacz [użycia z funkcją RDMA lub włączone GPU wystąpień w puli partii](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Kontener konfiguracji puli partii

Aby włączyć puli partii do uruchamiania obciążeń kontenera, należy określić [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) ustawienia z puli [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) obiektu. (Ten artykuł zawiera linki do dokumentacji interfejsu API platformy .NET usługi partia zadań. Odpowiednie ustawienia znajdują się w [Python partii](/python/api/azure.batch) interfejsu API.)

Można utworzyć pulę włączone kontenera lub bez obrazów prefetched kontenera, jak pokazano w poniższych przykładach. Proces ściągania (lub pobieranie z wyprzedzeniem) umożliwia wstępne ładowanie kontener obrazów z Centrum Docker lub innego rejestru kontenera w Internecie. Aby uzyskać najlepszą wydajność, należy użyć [rejestru kontenera platformy Azure](../container-registry/container-registry-intro.md) w tym samym regionie co konto usługi partia zadań.

Zaletą odczyt z wyprzedzeniem kontener obrazów jest, czy po pierwszym uruchomieniu zadania uruchamiania ich nie musisz czekać do pobierania obrazu kontenera. Konfigurację kontenera pobiera kontener obrazów do maszyn wirtualnych, po utworzeniu puli. Zadania, które są uruchamiane w puli można odwołać listy obrazów kontenera i kontener uruchamianie opcje.


### <a name="pool-without-prefetched-container-images"></a>Pula bez kontenera prefetched obrazów

Do skonfigurowania puli komputerów z obsługą kontenera bez kontenera prefetched obrazów, zdefiniuj `ContainerConfiguration` i `VirtualMachineConfiguration` obiektów, jak pokazano w poniższym przykładzie Python. W tym przykładzie używane Ubuntu Server partii zadań Azure kontenera pule obrazu z witryny Marketplace.


```python
image_ref_to_use = batch.models.ImageReference(
        publisher='microsoft-azure-batch',
        offer='ubuntu-server-container',
        sku='16-04-lts',
        version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            container_configuration=container_conf,
            node_agent_sku_id='batch.node.ubuntu 16.04'),
        vm_size='STANDARD_D1_V2',
        target_dedicated_nodes=1)
...
```


### <a name="prefetch-images-for-container-configuration"></a>Pobrana z wyprzedzeniem obrazy dla kontenera konfiguracji

Aby pobrana z wyprzedzeniem kontener obrazów w puli, Dodaj listy obrazów kontenera (`container_image_names`, w języku Python) do `ContainerConfiguration`. 

W poniższym przykładzie Python podstawowe pokazano, jak pobrana z wyprzedzeniem standardowy obraz kontenera Ubuntu z [Centrum Docker](https://hub.docker.com).

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub. 
"""

container_conf = batch.models.ContainerConfiguration(container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


W poniższym przykładzie przykład C# założono, że ma zostać pobrana z wyprzedzeniem obraz TensorFlow z [Centrum Docker](https://hub.docker.com). Ten przykład zawiera rozpoczęcia zadania w hoście maszyny Wirtualnej w węzłach puli. Należy zainstalować na serwerze plików, które są dostępne z kontenerów, może uruchomić zadania uruchamiania na przykład na hoście.

```csharp

ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);
...
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Wyprzedzeniem obrazów z rejestru Kontener prywatny

Możesz można również pobrana z wyprzedzeniem kontener obrazów uwierzytelniając się serwerze rejestru Kontener prywatny. W poniższym przykładzie `ContainerConfiguration` i `VirtualMachineConfiguration` obiektów pobrana z wyprzedzeniem prywatnej obrazu TensorFlow z rejestru prywatnej kontenera platformy Azure. Odwołanie do obrazu jest taki sam, jak w poprzednim przykładzie.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```


## <a name="container-settings-for-the-task"></a>Kontener ustawienia zadania

Aby uruchomić zadania kontenera w węzłach obliczeniowych, należy określić ustawienia specyficzne dla kontenera, takie jak zadanie uruchamiania opcje, obrazów do użycia i rejestru.

Użyj `ContainerSettings` właściwości klasy zadania, aby skonfigurować ustawienia specyficzne dla kontenera. Te ustawienia są definiowane przez [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) klasy.

Po uruchomieniu zadania na obrazy kontenera [zadań chmury](/dotnet/api/microsoft.azure.batch.cloudtask) i [zadanie Menedżer zadania](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) wymagają ustawienia kontenera. Jednak [Uruchom zadanie](/dotnet/api/microsoft.azure.batch.starttask), [zadanie przygotowanie zadania](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask), i [zadania Zwolnienie zadania](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) nie wymagają ustawienia kontenera (to znaczy, zostaną one uruchomione w kontekście kontenera lub bezpośrednio w węźle).

Wiersz polecenia dla zadania kontenera partii zadań Azure wykonuje się w katalogu roboczym, w kontenerze, który jest bardzo podobny do środowiska, które partii konfiguruje regularne zadania (z systemem innym niż kontener):

* Wszystkie katalogi rekursywnie poniżej `AZ_BATCH_NODE_ROOT_DIR` (root partii zadań Azure katalogi w węźle) są mapowane do kontenera.
* Wszystkie zmienne środowiskowe zadań są mapowane do kontenera.
* Katalog roboczy aplikacji ustawiono takie same jak w przypadku regularnego zadań, dzięki czemu można użyć funkcji, takich jak pakiety aplikacji i pliki zasobów

Ponieważ partii zmienia domyślny katalog roboczy w kontenerze sieci, to zadanie jest uruchamiane w lokalizacji innej z punktu wejścia typowe kontenera (na przykład `c:\` domyślnie do kontenera systemu Windows lub `/` w systemie Linux). Upewnij się, że punktem wejścia wiersza polecenia lub kontener zadanie Określa ścieżkę bezwzględną, jeśli go nie jest już skonfigurowana w ten sposób.

Poniższy fragment kodu języka Python zawiera podstawowe wiersza polecenia uruchomione w kontenerze Ubuntu pobierane z Centrum Docker. Dodatkowe argumenty są opcje kontener uruchamianie `docker create` zadanie jest uruchamiane polecenie. W tym miejscu `--rm` opcja umożliwia usunięcie kontenera po zakończeniu zadania.

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='ubuntu', 
    container_run_options='--rm')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/echo hello',
    container_settings=task_container_settings
)

```

W poniższym przykładzie C# przedstawiono ustawienia kontenera podstawowe zadania chmury:

```csharp
// Simple container task command

string cmdLine = "c:\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu",
    containerRunOptions: "--rm --read-only"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>Kolejne kroki

* Zobacz też [stoczni partii](https://github.com/Azure/batch-shipyard) zestawu narzędzi w celu łatwiejszego wdrażania obciążeń kontenera w partii zadań Azure za pośrednictwem [przepisami stoczni](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Aby uzyskać więcej informacji o instalowaniu i używaniu Docker CE na systemie Linux, zobacz [Docker](https://docs.docker.com/engine/installation/) dokumentacji.

* Aby uzyskać więcej informacji na temat używania niestandardowych obrazów, zobacz [umożliwiają utworzenie puli maszyn wirtualnych zarządzanych niestandardowego obrazu ](batch-custom-images.md).

* Dowiedz się więcej o [projektu Moby](https://mobyproject.org/), struktura umożliwiająca tworzenie systemów opartych na procesorze kontenera.