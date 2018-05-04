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
ms.date: 02/26/2018
ms.author: danlep
ms.openlocfilehash: fc8af53b0e0cfbe19a6509e8d126646badd0abbb
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="run-container-applications-on-azure-batch"></a>Uruchamianie aplikacji kontenera w partii zadań Azure

Partia zadań Azure umożliwia uruchamiania i skalowania bardzo dużą liczbę partii przetwarzania zadania na platformie Azure. Do tej pory partii zadań zostało uruchomione bezpośrednio na maszynach wirtualnych (VM) w puli partii, ale teraz należy skonfigurować pulę partii, uruchamianie zadań w kontenerach Docker.

Używanie kontenerów zapewnia prosty sposób do uruchamiania zadań wsadowych bez konieczności zarządzania pakietów aplikacji i zależności. Kontenery wdrażania aplikacji jako lekkie, przenośny samowystarczalne jednostki, które można uruchomić w różnych środowiskach. Na przykład użytkownik może kompilacji lokalnie kontener testu, a następnie Przekaż obraz kontenera w rejestrze w Azure lub gdziekolwiek indziej. Model wdrożenia kontenera zapewnia środowisko uruchomieniowe aplikacji jest zawsze poprawnie zainstalowany i skonfigurowany, niezależnie od tego, gdzie hostowania aplikacji. W tym samouczku przedstawiono sposób użycia zestawu .NET SDK usługi partia zadań do utworzenia puli węzłów obliczeniowych, które obsługują uruchomione zadania kontenera i sposób uruchamiania zadań kontenera w puli.

W tym artykule założono znajomość pojęcia kontenera Docker i tworzenie puli partii i zadania przy użyciu zestawu .NET SDK. Fragmenty kodu są przeznaczone do użycia w aplikacji klienckiej, podobnie jak [próbki DotNetTutorial](batch-dotnet-get-started.md), i podano przykłady kodu będzie potrzebny do obsługi aplikacji kontenera w partii.


## <a name="prerequisites"></a>Wymagania wstępne

* Wersje zestawu SDK: wsadowego zestawów SDK pomocy technicznej kontener obrazów z następujących wersji:
    * Interfejs API REST partii wersji 2017-09-01.6.0
    * Wsadowe .NET SDK w wersji 8.0.0
    * Zestaw SDK Python usługi partia zadań w wersji 4.0
    * Wsadowe Java SDK w wersji 3.0
    * Wsadowe Node.js SDK w wersji 3.0

* Konta: Na konto platformy Azure, musisz utworzyć konto usługi partia zadań i opcjonalnie konta usługi Azure Storage.

* Obsługiwane obrazu maszyny Wirtualnej. Kontenery są obsługiwane tylko w pulach utworzone za pomocą konfiguracji maszyny wirtualnej z obrazów szczegółowo opisane w poniższej sekcji, "obsługiwane obrazy maszyny wirtualnej".

* Jeśli podasz niestandardowego obrazu aplikacji należy użyć uwierzytelniania usługi Azure Active Directory (Azure AD), aby można było uruchamiać obciążenia na podstawie kontenera. Jeśli używasz obrazu portalu Azure Marketplace, nie potrzebujesz uwierzytelniania usługi Azure AD; uwierzytelniania klucza współużytkowanego będzie działać. Obsługa usługi Azure Batch dla usługi Azure AD jest udokumentowana w temacie [Authenticate Batch service solutions with Active Directory (Uwierzytelnianie rozwiązań usługi Batch za pomocą usługi Active Directory)](batch-aad-auth.md).


## <a name="supported-virtual-machine-images"></a>Obrazy obsługiwanych maszyn wirtualnych

Należy podać systemu Windows lub węzły obliczeniowe obrazu systemu Linux, aby utworzyć pulę maszyny wirtualnej.

### <a name="windows-images"></a>Obrazy systemu Windows

W przypadku obciążeń kontenera systemu Windows partii obecnie obsługuje niestandardowych obrazów utworzonych z maszyn wirtualnych z systemem Windows Docker lub za pomocą Centrum danych systemu Windows Server 2016 kontenery obrazu z portalu Azure Marketplace. Ten obraz jest kompatybilny z `batch.node.windows amd64` agenta węzła identyfikator jednostki SKU. Typ kontenera obsługiwane jest obecnie ograniczona do Docker.

### <a name="linux-images"></a>Obrazy systemu Linux

W przypadku obciążeń kontenera Linux partii aktualnie obsługuje tylko niestandardowych obrazów, które utworzono z maszyn wirtualnych pracujących Docker na następujących dystrybucje systemu Linux: Ubuntu 16.04 LTS i CentOS 7.3. Jeśli użytkownik chce dostarczyć własny obraz niestandardowy Linux, zobacz instrukcje w [umożliwiają utworzenie puli maszyn wirtualnych zarządzanych niestandardowego obrazu](batch-custom-images.md).

Można użyć [Docker Community Edition (CE)](https://www.docker.com/community-edition) lub [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Jeśli chcesz móc korzystać z wydajności procesora GPU Azure NC lub maszyn wirtualnych z wirtualizacją sieci, należy zainstalować NVIDIA sterowniki do obrazu. Ponadto należy zainstalować i uruchomić narzędzie aparat Docker dla jednostki GPU NVIDIA, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

Dostęp do sieci Azure RDMA, użyj maszyn wirtualnych z następujących rozmiarów: A8 A9, H16r, H16mr albo NC24r. Niezbędne sterowniki RDMA są zainstalowane w CentOS 7.3 HPC i Ubuntu 16.04 LTS obrazów z portalu Azure Marketplace. Dodatkowej konfiguracji mogą być wymagane do uruchamiania obciążeń MPI. Zobacz [użycia z funkcją RDMA lub włączone GPU wystąpień w puli partii](batch-pool-compute-intensive-sizes.md).


## <a name="limitations"></a>Ograniczenia

* Wsadowe obsługuje funkcję RDMA tylko kontenery z systemem Linux pule.


## <a name="authenticate-using-azure-active-directory"></a>Uwierzytelnianie za pomocą usługi Azure Active Directory

Jeśli używasz niestandardowego obrazu maszyny Wirtualnej można utworzyć puli partii, aplikacja kliencka musi uwierzytelnić przy użyciu zintegrowanego uwierzytelniania usługi Azure AD (uwierzytelniania klucza współużytkowanego nie działa). Przed uruchomieniem aplikacji, upewnij się, że możesz zarejestrować w usłudze Azure AD do ustalenia tożsamości dla niego i określanie jego uprawnień do innych aplikacji.

Ponadto użycie niestandardowego obrazu maszyny Wirtualnej, należy przyznać IAM kontroli dostępu do aplikacji można uzyskać dostępu do obrazu maszyny Wirtualnej. W portalu Azure, otwórz **wszystkie zasoby**, wybierz obraz kontenera i z **(IAM) kontroli dostępu** części bloku obrazu, a następnie kliknij przycisk **Dodaj**. W **dodać uprawnienia** bloku, określ **roli**w **przypisany dostęp**, wybierz pozycję **użytkownika usługi Azure AD, grupy lub aplikacji**, a następnie w  **Wybierz** wprowadź nazwę aplikacji.

W aplikacji, Przekaż token uwierzytelniania usługi Azure AD podczas tworzenia partii klienta przy użyciu [BatchClient.Open](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_), zgodnie z opisem w [rozwiązań usług uwierzytelniania partii z usługą Active Directory](batch-aad-auth.md).


## <a name="reference-a-vm-image-for-pool-creation"></a>Odwołanie do obrazu maszyny Wirtualnej do utworzenia puli

W kodzie aplikacji Podaj odwołanie do obrazu maszyny Wirtualnej do użycia podczas tworzenia węzłów obliczeniowych w puli. Można to zrobić, tworząc [elementu ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) obiektu. Można określić obrazu do użycia w jednym z następujących sposobów:

* Jeśli używasz niestandardowego obrazu, podaj identyfikator zasobu usługi Azure Resource Manager dla obrazu maszyny wirtualnej. Identyfikator obrazu ma format ścieżki, jak pokazano w poniższym przykładzie:

  ```csharp
  // Provide a reference to a custom image using an image ID
  ImageReference imageReference = new ImageReference("/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.Compute/images/<imageName>");
  ```

    Aby uzyskać ten identyfikator obrazu z portalu Azure, otwórz **wszystkie zasoby**, wybierz obraz niestandardowy i z **omówienie** sekcji bloku obraz należy skopiować ścieżkę w **identyfikator zasobu**.

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

Puli partii jest kolekcja węzłów obliczeniowych, na których partii wykonuje zadania w ramach zadania. Podczas tworzenia puli, podaj w konfiguracji maszyny Wirtualnej dla węzłów obliczeniowych. [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) obiektu zawiera odwołanie do [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) obiektu. Aby włączyć kontenera obciążeń w puli, określ `ContainerConfiguration` ustawienia. Konfiguracja maszyny Wirtualnej jest również której określić odwołanie do obrazu i obrazu węzła agenta identyfikator jednostki SKU, jak pokazano w poniższych przykładach.

Dostępnych jest kilka opcji tworzenia puli. Można utworzyć pulę z lub bez obrazów prefetched kontenera. 

Proces ściągania (lub pobieranie z wyprzedzeniem) umożliwia wstępne ładowanie kontener obrazów z Centrum Docker lub innego rejestru kontenera w Internecie. Zaletą odczyt z wyprzedzeniem kontener obrazów jest, czy po pierwszym uruchomieniu zadania uruchamiania ich nie musisz czekać do pobierania obrazu kontenera. Konfigurację kontenera pobiera kontener obrazów do maszyn wirtualnych, po utworzeniu puli. Zadania, które są uruchamiane w puli można odwołać listy obrazów kontenera i kontener uruchamianie opcje.



### <a name="pool-without-prefetched-container-images"></a>Pula bez kontenera prefetched obrazów

Aby skonfigurować pulę bez kontenera prefetched obrazów, zdefiniuj `ContainerConfiguration` i `VirtualMachineConfiguration` obiektów, jak pokazano w poniższym przykładzie. To, jak i w poniższych przykładach założono korzystasz z niestandardowego obrazu Ubuntu 16.04 LTS z aparatem platformy Docker zainstalowane.

```csharp
// Specify container configuration
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

Aby pobrana z wyprzedzeniem kontener obrazów w puli, Dodaj listy obrazów kontenera (`containerImageNames`) do `ContainerConfiguration`i nadaj nazwę listy obrazów. W poniższym przykładzie założono, że używasz niestandardowego obrazu Ubuntu 16.04 LTS, pobrana z wyprzedzeniem obraz TensorFlow z [Centrum Docker](https://hub.docker.com), i uruchomić TensorFlow podczas uruchamiania zadania.

```csharp
// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Define container settings
TaskContainerSettings startTaskContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu");
StartTask startTaskContainer = new StartTask(
    CommandLine: "<docker-image-command-line>",
    TaskContainerSettings: startTaskContainerSettings);

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

Podczas konfigurowania zadania do uruchomienia w węzłach obliczeniowych, należy określić ustawienia specyficzne dla kontenera, takie jak zadanie uruchamiania opcje, obrazów do użycia i rejestru.

Aby skonfigurować ustawienia specyficzne dla kontenera, należy użyć właściwości ContainerSettings klasy zadania. Te ustawienia są definiowane przez [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) klasy.

Po uruchomieniu zadania na obrazy kontenera [zadań chmury](/dotnet/api/microsoft.azure.batch.cloudtask) i [zadanie Menedżer zadania](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) wymagają ustawienia kontenera. Jednak [Uruchom zadanie](/dotnet/api/microsoft.azure.batch.starttask), [zadanie przygotowanie zadania](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask), i [zadania Zwolnienie zadania](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) nie wymagają ustawienia kontenera (to znaczy, zostaną one uruchomione w kontekście kontenera lub bezpośrednio w węźle).

Po skonfigurowaniu kontenera, wszystkie katalogi rekursywnie poniżej `AZ_BATCH_NODE_ROOT_DIR` (root partii zadań Azure katalogi w węźle) są mapowane do kontenera, wszystkie środowiska zadań zmienne są mapowane do kontenera i wiersz polecenia zadania reguły są wykonywane w kontenerze.

Przykładowy kod w [pobrana z wyprzedzeniem obrazy dla kontenera konfiguracji](#prefetch-images-for-container-configuration) pokazano, jak określić konfigurację kontenera zadania uruchamiania. Poniższy przykład kodu pokazuje, jak określić konfigurację kontenera zadania chmury:

```csharp
// Simple task command

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
