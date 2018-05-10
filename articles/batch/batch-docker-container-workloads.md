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
ms.date: 05/07/2018
ms.author: danlep
ms.openlocfilehash: 8c9f772c9d3908e450961239797f6ce2bd4982e4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="run-container-applications-on-azure-batch"></a>Uruchamianie aplikacji kontenera w partii zadań Azure

Partia zadań Azure umożliwia uruchamiania i skalowania dużej liczby partii przetwarzania zadania na platformie Azure. Do tej pory partii zadań zostało uruchomione bezpośrednio na maszynach wirtualnych (VM) w puli partii, ale teraz należy skonfigurować pulę partii, uruchamianie zadań w kontenerach Docker. W tym artykule przedstawiono tworzenie puli węzłów obliczeniowych, które obsługują uruchomione zadania kontenera przy użyciu zestawu .NET SDK usługi partia zadań i uruchamiania zadań kontenera w puli.

Używanie kontenerów zapewnia prosty sposób do uruchamiania zadań wsadowych bez konieczności zarządzania środowiska i zależności do uruchamiania aplikacji. Kontenery wdrażania aplikacji jako lekkie, przenośny samowystarczalne jednostki, które można uruchomić w różnych środowiskach. Na przykład użytkownik może kompilacji lokalnie kontener testu, a następnie Przekaż obraz kontenera w rejestrze w Azure lub gdziekolwiek indziej. Model wdrożenia kontenera zapewnia środowisko uruchomieniowe aplikacji jest zawsze poprawnie zainstalowany i skonfigurowany, niezależnie od tego, gdzie hostowania aplikacji. Na podstawie kontenera zadania w partii można również korzystać z funkcji zadań niebędących kontenerami, w tym pakiety aplikacji i zarządzanie plikami zasobów i plików wyjściowych. 

W tym artykule założono znajomość pojęcia kontenera Docker i tworzenie puli partii i zadania przy użyciu zestawu .NET SDK. Fragmenty kodu są przeznaczone do użycia w aplikacji klienckiej, podobnie jak [próbki DotNetTutorial](batch-dotnet-get-started.md), i podano przykłady kodu będzie potrzebny do obsługi aplikacji kontenera w partii.


## <a name="prerequisites"></a>Wymagania wstępne

* **Wersje zestawu SDK**: wsadowego zestawów SDK pomocy technicznej kontener obrazów z następujących wersji:
    * Interfejs API REST partii wersji 2017-09-01.6.0
    * Wsadowe .NET SDK w wersji 8.0.0
    * Zestaw SDK Python usługi partia zadań w wersji 4.0
    * Wsadowe Java SDK w wersji 3.0
    * Wsadowe Node.js SDK w wersji 3.0

* **Konta**: W Twojej subskrypcji platformy Azure, musisz utworzyć konto usługi partia zadań i opcjonalnie konta usługi Azure Storage.

* **Obsługiwane obrazu maszyny Wirtualnej**: kontenery są obsługiwane tylko w pulach utworzone za pomocą konfiguracji maszyny wirtualnej z obrazów szczegółowo opisane w poniższej sekcji, "obsługiwane obrazy maszyny wirtualnej". Jeśli podasz niestandardowego obrazu, aplikacja musi korzystać z usługi Azure Active Directory [uwierzytelniania (Azure AD)](batch-aad-auth.md) aby można było uruchamiać obciążenia na podstawie kontenera. 


## <a name="supported-virtual-machine-images"></a>Obrazy obsługiwanych maszyn wirtualnych

Należy użyć obsługiwanych systemu Windows lub obrazu systemu Linux, aby utworzyć pulę wirtualna obliczeniowe węzłów dla obciążeń kontenera.

### <a name="windows-images"></a>Obrazy systemu Windows

W przypadku obciążeń kontenera systemu Windows partii obecnie obsługuje niestandardowych obrazów utworzonych z maszyn wirtualnych z systemem Windows Docker lub za pomocą Centrum danych systemu Windows Server 2016 kontenery obrazu z portalu Azure Marketplace. Ten obraz jest kompatybilny z `batch.node.windows amd64` agenta węzła identyfikator jednostki SKU. Typ kontenera obsługiwane jest obecnie ograniczona do Docker.

### <a name="linux-images"></a>Obrazy systemu Linux

W przypadku obciążeń kontenera Linux partii aktualnie obsługuje tylko niestandardowych obrazów, które utworzono z maszyn wirtualnych pracujących Docker na następujących dystrybucje systemu Linux: Ubuntu 16.04 LTS i CentOS 7.3. Jeśli użytkownik chce dostarczyć własny obraz niestandardowy Linux, zobacz instrukcje w [umożliwiają utworzenie puli maszyn wirtualnych zarządzanych niestandardowego obrazu](batch-custom-images.md).

Obsługa Docker można zainstalować [Docker Community Edition (CE)](https://www.docker.com/community-edition) lub [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Jeśli chcesz móc korzystać z wydajności procesora GPU Azure NC lub maszyn wirtualnych z wirtualizacją sieci, należy zainstalować NVIDIA sterowniki do obrazu. Ponadto należy zainstalować i uruchomić narzędzie aparat Docker dla jednostki GPU NVIDIA, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

Dostęp do sieci Azure RDMA, użyj rozmiarów maszyn wirtualnych z funkcją RDMA, jak A8 i A9, H16r, H16mr lub NC24r. Niezbędne sterowniki RDMA są zainstalowane w CentOS 7.3 HPC i Ubuntu 16.04 LTS obrazów z portalu Azure Marketplace. Dodatkowej konfiguracji mogą być wymagane do uruchamiania obciążeń MPI. Zobacz [użycia z funkcją RDMA lub włączone GPU wystąpień w puli partii](batch-pool-compute-intensive-sizes.md).


## <a name="limitations"></a>Ograniczenia

* Wsadowe obsługuje funkcję RDMA tylko kontenery z systemem Linux pule.


## <a name="authenticate-using-azure-active-directory"></a>Uwierzytelnianie za pomocą usługi Azure Active Directory

Jeśli używasz niestandardowego obrazu maszyny Wirtualnej można utworzyć puli partii, aplikacja kliencka musi uwierzytelnić przy użyciu zintegrowanego uwierzytelniania usługi Azure AD (uwierzytelniania klucza współużytkowanego nie działa). Przed uruchomieniem aplikacji, upewnij się, że możesz zarejestrować w usłudze Azure AD do ustalenia tożsamości dla niego i określanie jego uprawnień do innych aplikacji.

Ponadto użycie niestandardowego obrazu maszyny Wirtualnej, należy przyznać IAM kontroli dostępu do aplikacji można uzyskać dostępu do obrazu maszyny Wirtualnej. W portalu Azure kliknij **wszystkie zasoby**, wybierz obraz kontenera i z **(IAM) kontroli dostępu** sekcji strony obrazu, kliknij przycisk **Dodaj**. W **dodać uprawnienia** określ **roli**w **przypisany dostęp**, wybierz pozycję **użytkownika usługi Azure AD, grupy lub aplikacji**, a następnie w  **Wybierz** wprowadź nazwę aplikacji.

W aplikacji należy przekazać tokenu uwierzytelniania usługi Azure AD podczas tworzenia klienta przetwarzania wsadowego. Jeśli tworzysz przy użyciu zestawu SDK .NET usługi partia zadań, użyj [BatchClient.Open](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_), zgodnie z opisem w [rozwiązań usług uwierzytelniania partii z usługą Active Directory](batch-aad-auth.md).


## <a name="reference-a-vm-image-for-pool-creation"></a>Odwołanie do obrazu maszyny Wirtualnej do utworzenia puli

W kodzie aplikacji Podaj odwołanie do obrazu maszyny Wirtualnej do użycia podczas tworzenia węzłów obliczeniowych w puli. Można to zrobić, tworząc [elementu ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) obiektu. Można określić obrazu do użycia w jednym z następujących sposobów:

* Jeśli używasz niestandardowego obrazu, podaj identyfikator zasobu usługi Azure Resource Manager dla obrazu maszyny wirtualnej. Identyfikator obrazu ma format ścieżki, jak pokazano w poniższym przykładzie:

  ```csharp
  // Provide a reference to a custom image using an image ID
  ImageReference imageReference = new ImageReference("/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.Compute/images/<imageName>");
  ```

    Aby uzyskać ten identyfikator obrazu z portalu Azure, otwórz **wszystkie zasoby**, wybierz obraz niestandardowy i z **omówienie** sekcji strony obraz należy skopiować ścieżkę w **identyfikator zasobu**.

* Jeśli używasz [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?page=1&subcategories=windows-based) obraz, podaj grupę parametry opisujące obrazu: wydawcy, typu oferty, jednostki SKU i wersji obrazu na liście [listy obrazów maszyny wirtualnej](batch-linux-nodes.md#list-of-virtual-machine-images):

  ```csharp
  // Provide a reference to an Azure Marketplace image for
  // "Windows Server 2016 Datacenter with Containers"
  ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-with-Containers",
    version: "latest");
  ```


## <a name="container-configuration-for-batch-pool"></a>Kontener konfiguracji puli partii

Aby włączyć puli partii do uruchamiania obciążeń kontenera, należy określić [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) ustawienia z puli [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) obiektu.

Można utworzyć pulę włączone kontenera lub bez obrazów prefetched kontenera, jak pokazano w poniższych przykładach. Proces ściągania (lub pobieranie z wyprzedzeniem) umożliwia wstępne ładowanie kontener obrazów z Centrum Docker lub innego rejestru kontenera w Internecie. Zaletą odczyt z wyprzedzeniem kontener obrazów jest, czy po pierwszym uruchomieniu zadania uruchamiania ich nie musisz czekać do pobierania obrazu kontenera. Konfigurację kontenera pobiera kontener obrazów do maszyn wirtualnych, po utworzeniu puli. Zadania, które są uruchamiane w puli można odwołać listy obrazów kontenera i kontener uruchamianie opcje.



### <a name="pool-without-prefetched-container-images"></a>Pula bez kontenera prefetched obrazów

Do skonfigurowania puli komputerów z obsługą kontenera bez kontenera prefetched obrazów, zdefiniuj `ContainerConfiguration` i `VirtualMachineConfiguration` obiektów, jak pokazano w poniższym przykładzie. To, jak i w poniższych przykładach założono korzystasz z niestandardowego obrazu Ubuntu 16.04 LTS z aparatem platformy Docker zainstalowane.

```csharp
// Specify container configuration. This is required even though there are no prefetched images.
ContainerConfiguration containerConfig = new ContainerConfiguration();

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

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-for-container-configuration"></a>Pobrana z wyprzedzeniem obrazy dla kontenera konfiguracji

Aby pobrana z wyprzedzeniem kontener obrazów w puli, Dodaj listy obrazów kontenera (`containerImageNames`) do `ContainerConfiguration`i nadaj nazwę listy obrazów. W poniższym przykładzie założono są przy użyciu niestandardowego obrazu Ubuntu 16.04 LTS i pobrana z wyprzedzeniem obraz TensorFlow z [Centrum Docker](https://hub.docker.com). Ten przykład zawiera rozpoczęcia zadania w hoście maszyny Wirtualnej w węzłach puli. Należy zainstalować na serwerze plików, które są dostępne z kontenerów, może uruchomić zadania uruchamiania na przykład na hoście.

```csharp
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

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Wyprzedzeniem obrazów z rejestru Kontener prywatny

Możesz można również pobrana z wyprzedzeniem kontener obrazów uwierzytelniając się serwerze rejestru Kontener prywatny. W poniższym przykładzie `ContainerConfiguration` i `VirtualMachineConfiguration` obiektów użyć niestandardowego obrazu Ubuntu 16.04 LTS i pobrana z wyprzedzeniem prywatnej obrazu TensorFlow z rejestru prywatnej kontenera platformy Azure.

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

// Commit pool creation
pool.Commit();
```


## <a name="container-settings-for-the-task"></a>Kontener ustawienia zadania

Aby uruchomić zadania kontenera w węzłach obliczeniowych, należy określić ustawienia specyficzne dla kontenera, takie jak zadanie uruchamiania opcje, obrazów do użycia i rejestru.

Użyj `ContainerSettings` właściwości klasy zadania, aby skonfigurować ustawienia specyficzne dla kontenera. Te ustawienia są definiowane przez [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) klasy.

Po uruchomieniu zadania na obrazy kontenera [zadań chmury](/dotnet/api/microsoft.azure.batch.cloudtask) i [zadanie Menedżer zadania](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) wymagają ustawienia kontenera. Jednak [Uruchom zadanie](/dotnet/api/microsoft.azure.batch.starttask), [zadanie przygotowanie zadania](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask), i [zadania Zwolnienie zadania](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) nie wymagają ustawienia kontenera (to znaczy, zostaną one uruchomione w kontekście kontenera lub bezpośrednio w węźle).

Po skonfigurowaniu kontenera, wszystkie katalogi rekursywnie poniżej `AZ_BATCH_NODE_ROOT_DIR` (root partii zadań Azure katalogi w węźle) są mapowane do kontenera, wszystkie środowiska zadań zmienne są mapowane do kontenera i wiersz polecenia zadania reguły są wykonywane w kontenerze.

Przykładowy kod w [pobrana z wyprzedzeniem obrazy dla kontenera konfiguracji](#prefetch-images-for-container-configuration) pokazano, jak określić konfigurację kontenera zadania uruchamiania. Poniższy przykład kodu pokazuje, jak określić konfigurację kontenera zadania chmury:

```csharp
// Simple container task command

string cmdLine = "<my-command-line>";

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
