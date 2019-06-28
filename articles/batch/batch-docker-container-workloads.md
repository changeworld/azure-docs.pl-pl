---
title: Obciążenia kontenera — Azure Batch | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uruchamiać aplikacje z obrazów kontenerów w usłudze Azure Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 11/19/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: cfd00ad124db33cec8e30e8e1bb701388ee71838
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340241"
---
# <a name="run-container-applications-on-azure-batch"></a>Uruchamianie aplikacji kontenera w usłudze Azure Batch

Usługa Azure Batch umożliwia uruchamianie i skalowanie dużej liczby partii zadań na platformie Azure obliczeń. Zadania podrzędne usługi Batch można uruchomić bezpośrednio na maszynach wirtualnych (węzłów) w puli usługi Batch, ale możesz również skonfigurować pulę usługi Batch do uruchamiania zadań w kontenerach zgodnego z platformą Docker w węzłach. W tym artykule pokazano, jak utworzyć pulę węzłów obliczeniowych, które obsługują uruchomione zadania kontenera, a następnie uruchom zadania kontenera w puli. 

Należy zapoznać się z kontenera koncepcji i sposobu tworzenia puli usługi Batch i zadanie. Przykłady kodu skorzystaj z platformy .NET usługi Batch i zestawów SDK języka Python. Aby utworzyć pule usługi Batch z włączoną funkcją kontenera i uruchamianie zadań w kontenerze, można użyć innych zestawów SDK usługi Batch i narzędzi, takich jak witryny Azure portal.

## <a name="why-use-containers"></a>Dlaczego warto używać kontenery?

Używanie kontenerów zapewnia łatwy sposób uruchamiania zadań wsadowych bez konieczności zarządzania środowisko i zależności w celu uruchamiania aplikacji. Kontenery wdrażania aplikacji jako lekki, przenośny samowystarczalne jednostek, które mogą być uruchamiane w kilku różnych środowiskach. Na przykład tworzenie i testowanie kontenera w środowisku lokalnym, a następnie Przekaż obraz kontenera do rejestru na platformie Azure lub w innym miejscu. Modelu wdrażania przy użyciu kontenera gwarantuje, że środowisko uruchomieniowe aplikacji jest zawsze poprawnie zainstalowany i skonfigurowany wszędzie tam, gdzie hostowania aplikacji. Oparte na kontenerach podzadania w usłudze Batch można również korzystać z funkcji zadań niebędących kontenerami, w tym pakiety aplikacji i zarządzanie plikami zasobów i plików wyjściowych. 

## <a name="prerequisites"></a>Wymagania wstępne

* **Wersje zestawu SDK**: Zestawów SDK usługi Batch obsługują obrazów kontenerów, począwszy od następujących wersji:
    * Interfejs API REST usługi Batch w wersji 2017-09-01.6.0
    * Zestawu SDK .NET usługi Batch w wersji 8.0.0
    * Zestaw SDK języka Python usługi Batch w wersji 4.0
    * Zestaw SDK Java usługi Batch w wersji 3.0
    * Zestaw SDK środowiska Node.js usługi Batch w wersji 3.0

* **Konta**: W ramach subskrypcji platformy Azure musisz utworzyć konto usługi Batch i opcjonalnie konta usługi Azure Storage.

* **Obsługiwane obrazu maszyny Wirtualnej**: Kontenery są obsługiwane tylko w przypadku pul utworzonych za pomocą konfiguracji maszyny wirtualnej z obrazów szczegółowo opisane w poniższej sekcji "obsługiwane obrazy maszyn wirtualnych". Jeśli podasz niestandardowego obrazu, zobacz uwagi w poniższej sekcji i wymagania [zarządzany obraz niestandardowy umożliwia tworzenie puli maszyn wirtualnych](batch-custom-images.md). 

### <a name="limitations"></a>Ograniczenia

* Usługa Batch udostępnia obsługi funkcji RDMA, tylko w przypadku kontenery działające w pulach systemu Linux

* Dla obciążeń kontenerów Windows zaleca się wybrać wyspecjalizowanymi rozmiar maszyny Wirtualnej dla puli

## <a name="supported-virtual-machine-images"></a>Obrazy maszyn wirtualnych obsługiwanych

Użyj jednej z następujących obsługiwanych Windows lub obrazów systemu Linux, aby utworzyć pulę maszyny Wirtualnej z węzłami obliczeniowymi w celu obciążeń kontenerów. Aby uzyskać więcej informacji na temat obrazów z portalu Marketplace, które są zgodne z usługą Batch, zobacz [listy obrazów maszyn wirtualnych](batch-linux-nodes.md#list-of-virtual-machine-images). 

### <a name="windows-images"></a>Obrazy Windows

Dla obciążeń kontenerów Windows, usługa Batch obsługuje obecnie **systemu Windows Server 2016 Datacenter z kontenerami** obrazów w portalu Azure Marketplace. Tylko obrazów kontenerów platformy Docker są obsługiwane w Windows.

Można również utworzyć niestandardowe obrazy maszyn wirtualnych uruchomionych platformy Docker na Windows.

### <a name="linux-images"></a>Obrazy systemu Linux

Dla obciążeń kontenerów systemu Linux usługa Batch obsługuje obecnie następujące obrazy systemu Linux opublikowana przez Microsoft Azure Batch w witrynie Azure Marketplace:

* **CentOS dla pul kontenera usługi Azure Batch**

* **CentOS (ze sterownikami RDMA) dla pul kontenera usługi Azure Batch**

* **Serwer Ubuntu dla pul kontenera usługi Azure Batch**

* **Serwer Ubuntu (ze sterownikami RDMA) dla pul kontenera usługi Azure Batch**

Te obrazy są obsługiwane tylko do użytku w pulach usługi Azure Batch. Obejmują one:

* Wstępnie zainstalowane [Moby](https://github.com/moby/moby) kontener środowiska uruchomieniowego 

* Wstępnie zainstalowanych sterowników procesora GPU NVIDIA, aby usprawnić wdrażanie na maszynach wirtualnych platformy Azure serii N

* Wybór obrazów z lub bez wstępnie zainstalowanych sterowników RDMA. Te sterowniki zezwala na węzłach puli usługi dostępu do sieci Azure RDMA po wdrożeniu na temat rozmiarów maszyny Wirtualnej funkcją RDMA. 

Można również utworzyć niestandardowe obrazy maszyn wirtualnych z uruchomionym platformy Docker w jednym z dystrybucje systemu Linux, które są zgodne z usługą Batch. Jeśli zdecydujesz się zapewniać własnych niestandardowych obrazów systemu Linux, zobacz instrukcje w [zarządzany obraz niestandardowy umożliwia tworzenie puli maszyn wirtualnych](batch-custom-images.md).

Obsługę platformy Docker na obrazach niestandardowych, można zainstalować [Docker Community Edition (CE)](https://www.docker.com/community-edition) lub [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Dodatkowe zagadnienia dotyczące korzystania z niestandardowych obrazów systemu Linux:

* Aby móc korzystać z wydajności procesora GPU platformy Azure serii N rozmiarów, korzystając z niestandardowego obrazu, należy wstępnie zainstalować sterowniki NVIDIA. Ponadto należy zainstalować narzędzia aparatu Docker dla procesory GPU NVIDIA [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Dostęp do sieci Azure RDMA, należy użyć rozmiaru maszyny Wirtualnej funkcją RDMA. Niezbędne sterowniki RDMA są instalowane w CentOS HPC i Ubuntu obrazy obsługiwane przy użyciu usługi Batch. Dodatkowa konfiguracja może być potrzebne do uruchamiania obciążeń MPI. Zobacz [Użyj obsługą dostępu RDMA lub włączonymi procesorami GPU wystąpień w puli usługi Batch](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Konfiguracja kontenera dla puli usługi Batch

Aby włączyć puli usługi Batch do uruchamiania obciążeń kontenerów, należy określić [związanych z przygotowaniem](/dotnet/api/microsoft.azure.batch.containerconfiguration) ustawienia z puli [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) obiektu. (Ten artykuł zawiera linki do dokumentacja interfejsu API .NET usługi Batch. Odpowiednie ustawienia znajdują się w [języka Python usługi Batch](/python/api/azure.batch) interfejsu API.)

Można utworzyć pulę z włączoną funkcją kontenera z lub bez obrazów kontenerów pobieranych z wyprzedzeniem, jak pokazano w poniższych przykładach. Proces replikacji ściąganej (lub pobieranie z wyprzedzeniem) umożliwia wstępne ładowanie obrazu kontenera z poziomu usługi Docker Hub lub inny rejestr kontenerów w Internecie. Aby uzyskać najlepszą wydajność, należy użyć [usługi Azure container registry](../container-registry/container-registry-intro.md) w tym samym regionie co konto usługi Batch.

Zaletą obrazów kontenerów Trwa pobieranie z wyprzedzeniem to, czy po pierwszym uruchomieniu zadania uruchamiania nie musieli czekać na obraz kontenera pobrać. Konfiguracja kontenera ściąga obrazów kontenera do maszyn wirtualnych, po utworzeniu puli. Zadania uruchamiane na puli może się wtedy odwoływać listy obrazów kontenerów i opcje uruchamiania kontenera.


### <a name="pool-without-prefetched-container-images"></a>Puli bez obrazów kontenerów pobieranych z wyprzedzeniem

Aby skonfigurować pulę włączone kontenera bez obrazów kontenerów pobieranych z wyprzedzeniem, zdefiniuj `ContainerConfiguration` i `VirtualMachineConfiguration` obiekty, jak pokazano w poniższym przykładzie w języku Python. W tym przykładzie używa Ubuntu Server dla usługi Azure Batch kontenera pule obrazu z witryny Marketplace.


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


### <a name="prefetch-images-for-container-configuration"></a>Pobrana z wyprzedzeniem obrazy kontenera konfiguracji

Aby w pobrana z wyprzedzeniem obrazów kontenerów w puli, dodać listy obrazów kontenerów (`container_image_names`, w języku Python) do `ContainerConfiguration`. 

Poniższy przykład podstawowy Python pokazuje, jak pobrana z wyprzedzeniem standardowy obraz kontenera Ubuntu z [usługi Docker Hub](https://hub.docker.com).

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub. 
"""

container_conf = batch.models.ContainerConfiguration(
    container_image_names=['ubuntu'])

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


Następujące C# przykładzie założono, że pobrana z wyprzedzeniem obraz TensorFlow, z [usługi Docker Hub](https://hub.docker.com). Ten przykład obejmuje rozpoczęcia zadania w hoście maszyny Wirtualnej w węzłach puli. Aby zainstalować serwer plików, który jest możliwy z kontenerów, może działać zadanie podrzędne uruchamiania na przykład na hoście.

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


### <a name="prefetch-images-from-a-private-container-registry"></a>Pobierania z wyprzedzeniem obrazów z prywatnego rejestru kontenerów

Użytkownik może również pobrana z wyprzedzeniem obrazów kontenerów, którzy uwierzytelniają się do serwera rejestru kontenera prywatnych. W poniższym przykładzie `ContainerConfiguration` i `VirtualMachineConfiguration` obiektów pobrana z wyprzedzeniem obrazu prywatnego TensorFlow z rejestru prywatnego kontenerów platformy Azure. Odwołanie do obrazu jest taka sama, jak w poprzednim przykładzie.

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

## <a name="container-settings-for-the-task"></a>Ustawienia kontenera dla zadania

Aby uruchomić zadanie kontenera w puli z włączoną funkcją kontenera, należy określić ustawienia specyficzne dla kontenera. Ustawienia obejmują obrazu do użycia, rejestru i opcji uruchamiania kontenera.

* Użyj `ContainerSettings` właściwości klasy zadania, aby skonfigurować ustawienia specyficzne dla kontenera. Te ustawienia są definiowane przez [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) klasy.

* Po uruchomieniu zadania w obrazów kontenerów, [zadania w chmurze](/dotnet/api/microsoft.azure.batch.cloudtask) i [zadanie podrzędne Menedżera zadań](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) Wymagaj ustawień kontenera. Jednak [zadanie podrzędne uruchamiania](/dotnet/api/microsoft.azure.batch.starttask), [zadania podrzędnego przygotowania zadania](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask), i [zadanie podrzędne zwolnienia zadania](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) nie wymagają ustawienia kontenera (czyli mogą uruchamiać w kontekście kontenera lub bezpośrednio w węźle).

### <a name="container-task-command-line"></a>Wiersz polecenia zadania kontenera

Po uruchomieniu zadania kontenera, Batch automatycznie używa [docker Utwórz](https://docs.docker.com/engine/reference/commandline/create/) polecenie, aby utworzyć kontener korzystając z obrazu określone w zadaniu. Batch to kontroluje wykonywanie zadań w kontenerze. 

Jako za pomocą zadania podrzędne usługi Batch — do kontenera, możesz ustawić wiersza polecenia zadania podrzędnego kontenera. Ponieważ usługa Batch tworzy automatycznie kontenera, w wierszu polecenia określa tylko polecenie lub polecenia, które będą uruchamiane w kontenerze.

Jeśli obraz kontenera dla zadania usługi Batch jest skonfigurowany z [punktu wejścia](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) skryptu, można ustawić wiersza polecenia można użyć domyślnego punktu wejścia lub jej zastąpienie: 

* Aby użyć domyślnego punktu wejścia obrazu kontenera, należy ustawić wiersza polecenia zadania do pustego ciągu `""`.

* Aby zastąpić domyślne punktu wejścia lub jeśli obraz nie ma punktu wejścia, Ustaw wiersz polecenia odpowiedni dla tego kontenera, na przykład `/app/myapp` lub `/bin/sh -c python myscript.py`.

Opcjonalnie [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) są dodatkowe argumenty uzyskiwanie `docker create` polecenia, że usługi Batch używa do tworzenia i uruchamiania kontenera. Na przykład, aby określić katalog roboczy dla kontenera, należy ustawić `--workdir <directory>` opcji. Zobacz [docker Utwórz](https://docs.docker.com/engine/reference/commandline/create/) dokumentacja dotycząca dodatkowych opcji.

### <a name="container-task-working-directory"></a>Katalog roboczy zadania kontenera

Wykonuje zadania wsadowe kontenera w katalogu roboczym, w kontenerze, który jest bardzo podobne do katalogu, w którym usługi Batch został skonfigurowany zadaniem regularnym (niebędących kontenerami). Należy pamiętać, że ten katalog roboczy różni się od [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) Jeśli skonfigurowany obraz lub kontener domyślny katalog roboczy (`C:\` na kontenerze Windows lub `/` w kontenerze systemu Linux). 

Kontener zadania usługi Batch:

* Wszystkie katalogi cyklicznie poniżej `AZ_BATCH_NODE_ROOT_DIR` na hoście węzła (główne katalogi usługi Azure Batch) są mapowane do kontenera.
* Wszystkie zmienne środowiskowe zadania są mapowane do kontenera.
* Katalog roboczy zadania `AZ_BATCH_TASK_WORKING_DIR` w węźle jest takie samo jak w typowym zadaniem i mapowany do kontenera. 

Te mapowania umożliwiają pracę z zadaniami kontenera, w podobny sposób jak zadania nie będącego kontenerem. Na przykład instalowanie aplikacji za pomocą pakietów aplikacji, dostęp do zasobów plików z usługi Azure Storage, użyj ustawienia środowiska dla zadań i plików wyjściowych zadania są zachowywane po zatrzymaniu kontenera.

### <a name="troubleshoot-container-tasks"></a>Rozwiązywanie problemów z zadaniami kontenera

Jeśli zadanie kontener nie działa zgodnie z oczekiwaniami, może być konieczne uzyskać informacje na temat konfiguracji WORKDIR lub punktu wejścia, obrazu kontenera. Aby sprawdzić konfigurację, uruchom [obrazu platformy docker Sprawdź](https://docs.docker.com/engine/reference/commandline/image_inspect/) polecenia. 

Jeśli to konieczne, należy dostosować ustawienia zadania kontenera, na podstawie obrazu:

* Określ ścieżkę bezwzględną w wierszu polecenia zadania. Obraz punktu wejścia używana jest wartość domyślna dla wiersza polecenia zadania upewnij się, czy ustawiono ścieżką bezwzględną.

* W opcji uruchamiania kontenera zadania podrzędnego Zmień katalog roboczy, aby dopasować WORKDIR na obrazie. Na przykład ustawić `--workdir /app`.

## <a name="container-task-examples"></a>Przykładowe zadania podrzędne kontenera

Poniższy fragment kodu języka Python przedstawia podstawowe wiersza polecenia, uruchomione w kontenerze, który został utworzony na podstawie fikcyjnej obrazu, pobierane z usługi Docker Hub. W tym miejscu `--rm` kontenera opcja usuwa kontener, po zakończeniu zadania, a `--workdir` opcja określa katalog roboczy. W wierszu polecenia zastępuje kontenera punktu wejścia przy użyciu polecenia powłoki prostego, który zapisuje mały plik do katalogu roboczego zadań na hoście. 

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage',
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)
```

C# poniższy kod przedstawia ustawienia kontenera podstawowe zadania w chmurze:

```csharp
// Simple container task command

string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>Kolejne kroki

* Zobacz też [usługa Batch Shipyard](https://github.com/Azure/batch-shipyard) zestaw narzędzi w celu łatwiejszego wdrażania obciążeń kontenerów w usłudze Azure Batch za pomocą [przepisy usługa Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Aby uzyskać więcej informacji o instalowaniu i używaniu Docker CE w systemie Linux, zobacz [Docker](https://docs.docker.com/engine/installation/) dokumentacji.

* Aby uzyskać więcej informacji na temat korzystania z niestandardowych obrazów, zobacz [zarządzany obraz niestandardowy umożliwia tworzenie puli maszyn wirtualnych](batch-custom-images.md).

* Dowiedz się więcej o [projektu Moby](https://mobyproject.org/), umożliwiająca tworzenie systemów opartych na kontenerach.