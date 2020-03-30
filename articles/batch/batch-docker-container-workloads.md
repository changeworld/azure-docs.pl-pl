---
title: Obciążenia kontenerów — usługa Azure Batch
description: Dowiedz się, jak uruchamiać i skalować aplikacje z obrazów kontenerów w usłudze Azure Batch. Utwórz pulę węzłów obliczeniowych obsługujących uruchamianie zadań kontenera.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 03/02/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 81f4e753ffbaaefd5761c9396a6533bac9f212c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254838"
---
# <a name="run-container-applications-on-azure-batch"></a>Uruchamianie aplikacji kontenerów w usłudze Azure Batch

Usługa Azure Batch umożliwia uruchamianie i skalowanie dużej liczby zadań przetwarzania wsadowego na platformie Azure. Zadania wsadowe można uruchamiać bezpośrednio na maszynach wirtualnych (węzłach) w puli usługi Batch, ale można również skonfigurować pulę usługi Batch do uruchamiania zadań w kontenerach zgodnych z usługą Docker w węzłach. W tym artykule pokazano, jak utworzyć pulę węzłów obliczeniowych, które obsługują uruchamianie zadań kontenera, a następnie uruchomić zadania kontenera w puli.

Należy zapoznać się z pojęć kontenera i jak utworzyć batch puli i zadania. Przykłady kodu używają batch .NET i Python SDK. Można również użyć innych zestawów SDK partii i narzędzi, w tym witryny Azure Portal, do tworzenia pule wsadowych z obsługą kontenera i uruchamiania zadań kontenera.

## <a name="why-use-containers"></a>Dlaczego warto korzystać z kontenerów?

Korzystanie z kontenerów zapewnia łatwy sposób uruchamiania zadań usługi Batch bez konieczności zarządzania środowiskiem i zależności do uruchamiania aplikacji. Kontenery wdrażają aplikacje jako lekkie, przenośne, samowystarczalne jednostki, które mogą działać w kilku różnych środowiskach. Na przykład skompiluj i przetestuj kontener lokalnie, a następnie przekaż obraz kontenera do rejestru na platformie Azure lub w innym miejscu. Model wdrażania kontenera zapewnia, że środowisko wykonawcze aplikacji jest zawsze poprawnie zainstalowane i skonfigurowane wszędzie tam, gdzie hostujesz aplikację. Zadania oparte na kontenerach w usłudze Batch mogą również korzystać z funkcji zadań innych niż kontenery, w tym pakietów aplikacji i zarządzania plikami zasobów i plikami wyjściowymi.

## <a name="prerequisites"></a>Wymagania wstępne

* **Wersje SDK:** SDK partii obsługują obrazy kontenerów w następujących wersjach:
    * Wersja interfejsu API REST wsadowej 2017-09-01.6.0
    * Plik SDK w wersji 8.0.0 w wersji batch.NET
    * Plik SDK języka Python w wersji 4.0
    * Wsadowy SDK Java w wersji 3.0
    * Plik SDK węzła wsadowego w wersji 3.0

* **Konta:** W ramach subskrypcji platformy Azure należy utworzyć konto usługi Batch i opcjonalnie konto usługi Azure Storage.

* **Obsługiwany obraz maszyny Wirtualnej:** Kontenery są obsługiwane tylko w pulach utworzonych za pomocą konfiguracji maszyny wirtualnej, z obrazów opisanych w poniższej sekcji "Obsługiwane obrazy maszyn wirtualnych". Jeśli podasz obraz niestandardowy, zobacz zagadnienia w poniższej sekcji i wymagania w [użyj zarządzanego obrazu niestandardowego, aby utworzyć pulę maszyn wirtualnych](batch-custom-images.md).

### <a name="limitations"></a>Ograniczenia

* Batch zapewnia obsługę RDMA tylko dla kontenerów działających w pulach systemu Linux

* W przypadku obciążeń kontenerów systemu Windows zaleca się wybranie wielordzeniowego rozmiaru maszyny Wirtualnej dla puli

## <a name="supported-virtual-machine-images"></a>Obsługiwane obrazy maszyn wirtualnych

Użyj jednego z następujących obsługiwanych obrazów systemu Windows lub Linux, aby utworzyć pulę węzłów obliczeniowych maszyny Wirtualnej dla obciążeń kontenerów. Aby uzyskać więcej informacji na temat obrazów portalu Marketplace zgodnych z usługą Batch, zobacz [listę obrazów maszyn wirtualnych](batch-linux-nodes.md#list-of-virtual-machine-images).

### <a name="windows-support"></a>Obsługa systemu Windows

Usługa Batch obsługuje obrazy serwera Windows, które mają oznaczenia obsługi kontenerów. Zazwyczaj te nazwy sku obrazu są `-with-containers` sufiksami z lub `-with-containers-smalldisk`. Ponadto [interfejs API do listy wszystkich obsługiwanych obrazów w usłudze Batch](batch-linux-nodes.md#list-of-virtual-machine-images) będzie oznaczać `DockerCompatible` możliwość, jeśli obraz obsługuje kontenery platformy Docker.

Można również tworzyć obrazy niestandardowe z maszyn wirtualnych z systemem Docker w systemie Windows.

### <a name="linux-support"></a>Obsługa systemu Linux

W przypadku obciążeń kontenerów systemu Linux usługa Batch obsługuje obecnie następujące obrazy systemu Linux opublikowane przez usługę Microsoft Azure Batch w portalu Azure Marketplace bez konieczności stosowania obrazu niestandardowego.

#### <a name="vm-sizes-without-rdma"></a>Rozmiary maszyn wirtualnych bez rdma

- Wydawca:`microsoft-azure-batch`
  - Oferta:`centos-container`
  - Oferta:`ubuntu-server-container`

#### <a name="vm-sizes-with-rdma"></a>Rozmiary maszyn wirtualnych z usługą RDMA

- Wydawca:`microsoft-azure-batch`
  - Oferta:`centos-container-rdma`
  - Oferta:`ubuntu-server-container-rdma`

Te obrazy są obsługiwane tylko do użytku w pulach usługi Azure Batch i są przeznaczone do wykonywania kontenera platformy Docker. Są one wyposażone w:

* Wstępnie zainstalowany środowisko wykonawcze kontenera [Moby](https://github.com/moby/moby) zgodnego z programem Docker

* Wstępnie zainstalowane sterowniki GPU NVIDIA i środowisko wykonawcze kontenerów NVIDIA usprawniają wdrażanie na maszynach wirtualnych z serii N platformy Azure

* Wstępnie zainstalowany/wstępnie skonfigurowany obraz z obsługą rozmiarów maszyn wirtualnych RDMA infiniband `-rdma`dla obrazów z przyrostkiem . Obecnie te obrazy nie obsługują rozmiarów maszyn wirtualnych SR-IOV IB/RDMA.

Można również tworzyć obrazy niestandardowe z maszyn wirtualnych z systemem Docker na jednej z dystrybucji systemu Linux, która jest zgodna z partią. Jeśli zdecydujesz się udostępnić własny niestandardowy obraz systemu Linux, zapoznaj się z instrukcjami w [sekcji Użyj zarządzanego obrazu niestandardowego, aby utworzyć pulę maszyn wirtualnych](batch-custom-images.md).

Aby uzyskać pomoc techniczną platformy Docker na obrazie niestandardowym, zainstaluj program [Docker Community Edition (CE)](https://www.docker.com/community-edition) lub [Docker Enterprise Edition (EE).](https://www.docker.com/enterprise-edition)

Dodatkowe zagadnienia dotyczące używania niestandardowego obrazu systemu Linux:

* Aby skorzystać z wydajności procesora GPU w rozmiarach n serii Azure podczas korzystania z niestandardowego obrazu, wstępnie zainstaluj sterowniki NVIDIA. Ponadto, trzeba zainstalować Narzędzie Docker Engine dla procesorów graficznych NVIDIA, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Aby uzyskać dostęp do sieci rdma platformy Azure, należy użyć rozmiaru maszyny wirtualnej obsługującej usługę RDMA. Niezbędne sterowniki RDMA są instalowane w obrazach CentOS HPC i Ubuntu obsługiwanych przez firmę Batch. Do uruchamiania obciążeń MPI może być konieczna dodatkowa konfiguracja. Zobacz [Używanie wystąpień obsługujących technologię RDMA lub GPU w puli wsadowej](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Konfiguracja kontenera dla puli partii

Aby włączyć pulę usługi Batch do uruchamiania obciążeń kontenera, należy określić ustawienia [kontenerakonfiguracji](/dotnet/api/microsoft.azure.batch.containerconfiguration) w obiekcie [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) puli. (Ten artykuł zawiera łącza do odwołania do interfejsu API usługi Batch .NET. Odpowiednie ustawienia znajdują się w interfejsie API [języka Python.)](/python/api/overview/azure/batch)

Można utworzyć pulę z włączoną obsługą kontenera z obrazami kontenerów z wyprzedzeniem lub bez niej, jak pokazano w poniższych przykładach. Proces ściągania (lub wstępnego dostępu) umożliwia wstępne ładowanie obrazów kontenerów z usługi Docker Hub lub innego rejestru kontenerów w Internecie. Aby uzyskać najlepszą wydajność, należy użyć [rejestru kontenerów platformy Azure](../container-registry/container-registry-intro.md) w tym samym regionie co konto usługi Batch.

Zaletą pobierania obrazów kontenerów z prefchingiem jest to, że podczas pierwszego uruchamiania zadań nie trzeba czekać na pobranie obrazu kontenera. Konfiguracja kontenera pobiera obrazy kontenerów do maszyn wirtualnych podczas tworzenia puli. Zadania uruchamiane w puli można następnie odwołać się do listy obrazów kontenerów i opcji uruchamiania kontenera.


### <a name="pool-without-prefetched-container-images"></a>Pula bez wstępnie naliczonych obrazów kontenerów

Aby skonfigurować pulę z obsługą kontenera bez wstępnie `ContainerConfiguration` `VirtualMachineConfiguration` naborowanych obrazów kontenerów, należy zdefiniować i obiekty, jak pokazano w poniższym przykładzie języka Python. W tym przykładzie użyto obrazu pul kontenerów usługi Azure Batch z witryny Marketplace.


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


### <a name="prefetch-images-for-container-configuration"></a>Obrazy z fragmentem wstępnym dla konfiguracji kontenera

Aby wstępnie wyciąć obrazy kontenerów w puli, dodaj listę obrazów kontenerów (`container_image_names`, w Pythonie) `ContainerConfiguration`do pliku .

W poniższym podstawowym przykładzie języka Python pokazano, jak wstępnie wyciąć standardowy obraz kontenera Ubuntu z [usługi Docker Hub](https://hub.docker.com).

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


W poniższym przykładzie języka C# przyjęto założenie, że chcesz wstępnie wyciąć obraz TensorFlow z [centrum platformy Docker](https://hub.docker.com)Hub. W tym przykładzie znajduje się zadanie uruchamiania, które jest uruchamiane w hoście maszyny Wirtualnej w węzłach puli. Można uruchomić zadanie uruchamiania na hoście, na przykład, aby zainstalować serwer plików, który można uzyskać z kontenerów.

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


### <a name="prefetch-images-from-a-private-container-registry"></a>Obrazy z fragmentem wstępnym z rejestru kontenerów prywatnych

Można również wstępnie wyciąć obrazy kontenerów, uwierzytelniając się na prywatnym serwerze rejestru kontenerów. W poniższym `ContainerConfiguration` przykładzie `VirtualMachineConfiguration` i obiekty wstępnie pobierać prywatny obraz TensorFlow z prywatnego rejestru kontenerów platformy Azure. Odwołanie do obrazu jest taka sama jak w poprzednim przykładzie.

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

Aby uruchomić zadanie kontenera w puli z włączoną obsługą kontenera, należy określić ustawienia specyficzne dla kontenera. Ustawienia obejmują opcje uruchamiania obrazu, rejestru i kontenera.

* Użyj `ContainerSettings` właściwości klas zadań, aby skonfigurować ustawienia specyficzne dla kontenera. Te ustawienia są definiowane przez [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) klasy. Należy zauważyć, że opcja `--rm` kontenera `--runtime` nie wymaga dodatkowej opcji, ponieważ jest pod opieką przez partię.

* Jeśli uruchamiasz zadania na obrazach kontenerów, [zadanie w chmurze](/dotnet/api/microsoft.azure.batch.cloudtask) i [zadanie menedżera zadań](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) wymagają ustawień kontenera. Jednak [zadanie uruchamiania,](/dotnet/api/microsoft.azure.batch.starttask) [zadanie przygotowania zadania](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)i zadanie [zwalniania zadania](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) nie wymagają ustawień kontenera (oznacza to, że mogą działać w kontekście kontenera lub bezpośrednio w węźle).

### <a name="container-task-command-line"></a>Wiersz polecenia zadania kontenera

Po uruchomieniu zadania kontenera usługa Batch automatycznie używa polecenia [docker create](https://docs.docker.com/engine/reference/commandline/create/) do utworzenia kontenera przy użyciu obrazu określonego w zadaniu. Batch następnie kontroluje wykonywanie zadań w kontenerze.

Podobnie jak w przypadku zadań usługi Batch innych niż kontener, wiersz polecenia dla zadania kontenera. Ponieważ usługa Batch automatycznie tworzy kontener, wiersz polecenia określa tylko polecenie lub polecenia, które będą uruchamiane w kontenerze.

Jeśli obraz kontenera dla zadania batch jest skonfigurowany ze skryptem [ENTRYPOINT,](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) można ustawić wiersz polecenia tak, aby używał domyślnego entrypointa lub zastępował go:

* Aby użyć domyślnego punktu WEJŚCIA obrazu kontenera, ustaw wiersz `""`polecenia zadania na pusty ciąg .

* Aby zastąpić domyślny punkt WEJŚCIA lub jeśli obraz nie ma entrypointa, ustaw wiersz polecenia odpowiedni `/app/myapp` `/bin/sh -c python myscript.py`dla kontenera, na przykład lub .

Opcjonalne [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) są dodatkowe `docker create` argumenty, które można podać do polecenia, które usługa Batch używa do tworzenia i uruchamiania kontenera. Na przykład, aby ustawić katalog roboczy dla `--workdir <directory>` kontenera, należy ustawić opcję. Zobacz [docker utworzyć](https://docs.docker.com/engine/reference/commandline/create/) odwołanie dla dodatkowych opcji.

### <a name="container-task-working-directory"></a>Katalog roboczy zadań kontenera

Zadanie kontenera usługi Batch jest wykonywane w katalogu roboczym w kontenerze, który jest bardzo podobny do katalogu Batch konfiguruje dla zadania zwykłego (nie kontenerowego). Należy zauważyć, że ten katalog roboczy różni się od [WORKDIR,](https://docs.docker.com/engine/reference/builder/#workdir) jeśli jest`C:\` skonfigurowany w `/` obrazie lub domyślnego katalogu roboczego kontenera (w kontenerze systemu Windows lub w kontenerze systemu Linux).

Dla zadania kontenera wsadowego:

* Wszystkie katalogi rekursywnie poniżej węzła `AZ_BATCH_NODE_ROOT_DIR` hosta (katalogu głównego katalogów usługi Azure Batch) są mapowane do kontenera
* Wszystkie zmienne środowiska zadania są mapowane do kontenera
* Katalog roboczy `AZ_BATCH_TASK_WORKING_DIR` zadania w węźle jest ustawiony tak samo jak dla zwykłego zadania i mapowany do kontenera.

Te mapowania umożliwiają pracę z zadaniami kontenera w taki sam sposób jak zadania niebędące kontenerami. Na przykład zainstaluj aplikacje przy użyciu pakietów aplikacji, dostęp do plików zasobów z usługi Azure Storage, użyj ustawień środowiska zadań i utrwalić pliki wyjściowe zadań po zatrzymaniu kontenera.

### <a name="troubleshoot-container-tasks"></a>Rozwiązywanie problemów z zadaniami kontenera

Jeśli zadanie kontenera nie działa zgodnie z oczekiwaniami, może być konieczne uzyskanie informacji o konfiguracji WORKDIR lub ENTRYPOINT obrazu kontenera. Aby wyświetlić konfigurację, uruchom polecenie [inspekcji obrazu platformy docker.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

W razie potrzeby dostosuj ustawienia zadania kontenera na podstawie obrazu:

* Określ ścieżkę bezwzględną w wierszu polecenia zadania. Jeśli domyślny punkt WEJŚCIA obrazu jest używany dla wiersza polecenia zadania, upewnij się, że ustawiona jest ścieżka bezwzględna.

* W opcjach uruchamiania kontenera zadania zmień katalog roboczy, aby był zgodny z workdirem obrazu. Na przykład `--workdir /app`ustaw .

## <a name="container-task-examples"></a>Przykłady zadań kontenera

Poniższy fragment kodu języka Python przedstawia podstawowy wiersz polecenia uruchomiony w kontenerze utworzonym na podstawie fikcyjnego obrazu pobranego z usługi Docker Hub. W tym `--rm` miejscu opcja kontenera usuwa kontener po `--workdir` zakończeniu zadania, a opcja ustawia katalog roboczy. Wiersz polecenia zastępuje punkt wejścia kontenera za pomocą prostego polecenia powłoki, które zapisuje mały plik do katalogu roboczego zadania na hoście.

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

W poniższym przykładzie języka C# przedstawiono podstawowe ustawienia kontenera dla zadania w chmurze:

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


## <a name="next-steps"></a>Następne kroki

* Zobacz też zestaw narzędzi [wsadowa stocznia](https://github.com/Azure/batch-shipyard) dla łatwego wdrażania obciążeń kontenerów na platformie Azure Batch za pośrednictwem [receptur stoczni.](https://github.com/Azure/batch-shipyard/tree/master/recipes)

* Aby uzyskać więcej informacji na temat instalowania i używania platformy Docker CE w systemie Linux, zobacz dokumentację [platformy Docker.](https://docs.docker.com/engine/installation/)

* Aby uzyskać więcej informacji na temat używania obrazów niestandardowych, zobacz [Tworzenie puli maszyn wirtualnych za pomocą zarządzanego obrazu niestandardowego.](batch-custom-images.md)

* Dowiedz się więcej o [projekcie Moby](https://mobyproject.org/), ramach tworzenia systemów kontenerowych.
