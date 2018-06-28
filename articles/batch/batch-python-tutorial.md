---
title: Samouczek — korzystanie z zestawu SDK usługi Azure Batch dla języka Python | Microsoft Docs
description: Podstawowe pojęcia dotyczące usługi Azure Batch i tworzenie prostego rozwiązania przy użyciu platformy Python.
services: batch
documentationcenter: python
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 42cae157-d43d-47f8-88f5-486ccfd334f4
ms.service: batch
ms.devlang: python
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 20c205d62809680ed118f768bdaa75f5504d4222
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333316"
---
# <a name="get-started-with-the-batch-sdk-for-python"></a>Wprowadzenie do zestawu SDK usługi Batch dla języka Python

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
> * [Node.js](batch-nodejs-get-started.md)
>
>

Artykuł zawiera podstawowe informacje dotyczące usługi [Azure Batch][azure_batch] i klienta usługi [Batch dla środowiska Python][py_azure_sdk] w oparciu o omówienie małej aplikacji usługi Batch napisanej w języku Python. Pokazano, jak dwa przykładowe skrypty używają usługi Batch do przetwarzania równoległego obciążenia na maszynach wirtualnych z systemem Linux w chmurze, a także jak wchodzą w interakcję z usługą [Azure Storage](../storage/common/storage-introduction.md) w celu przygotowania i pobrania plików. Przedstawiono tu typowy przepływ pracy w aplikacji usługi Batch oraz wyjaśniono podstawowe zagadnienia dotyczące najważniejszych składników usługi Batch, np. zadań, podzadań, pul i węzłów obliczeniowych.

![Przepływ pracy rozwiązania w usłudze Batch (podstawowy)][11]<br/>

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz praktyczną wiedzę dotyczącą języka Python oraz znasz system Linux. Przyjęto również założenie, że jesteś w stanie spełnić wymagania dotyczące tworzenia konta, które zostały wyszczególnione poniżej dla platformy Azure oraz usług Batch i Storage.

### <a name="accounts"></a>Konta
* **Konto platformy Azure**: jeśli nie masz jeszcze subskrypcji platformy Azure, [utwórz bezpłatne konto platformy Azure][azure_free_account].
* **Konto usługi Batch**: po uzyskaniu subskrypcji platformy Azure [utwórz konto usługi Azure Batch](batch-account-create-portal.md).
* **Konto magazynu**: zobacz sekcję [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md#create-a-storage-account) w temacie [Informacje o kontach magazynu Azure](../storage/common/storage-create-storage-account.md).

### <a name="code-sample"></a>Przykład kodu
[Przykład kodu][github_article_samples] z samouczka dotyczącego środowiska Python jest jednym z wielu przykładów kodów znajdujących się w repozytorium [azure-batch-samples][github_samples] w witrynie GitHub. Możesz pobrać wszystkie przykłady, klikając przycisk **Clone or download > Download ZIP** (Sklonuj lub pobierz > Pobierz plik ZIP) na stronie głównej repozytorium lub klikając bezpośredni link pobierania pliku [azure-batch-samples-master.zip][github_samples_zip]. Po wyodrębnieniu zawartości pliku ZIP dwa skrypty do tego samouczka znajdują się w katalogu `article_samples`:

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/>
`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### <a name="python-environment"></a>Środowisko Python
Do uruchomienia przykładowego skryptu *python_tutorial_client.py* na lokalnej stacji roboczej potrzebny jest **interpreter języka Python** zgodny z wersją **2.7** lub **3.3+**. Skrypt przetestowano w systemach Windows i Linux.

### <a name="cryptography-dependencies"></a>Zależności kryptograficzne
Musisz zainstalować zależności dla biblioteki [kryptograficznej][crypto], wymagane przez pakiety języka Python `azure-batch` i `azure-storage`. Wykonaj jedną z następujących czynności odpowiednio do używanej platformy lub zapoznaj się ze szczegółowymi informacjami na temat [instalowania kryptografii][crypto_install]:

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`
* CentOS

    `yum update && yum install -y gcc openssl-devel libffi-devel python-devel`
* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`
* Windows

    `pip install cryptography`

> [!NOTE]
> W przypadku instalowania języka Python 3.3 + w systemie Linux, użyj odpowiedników środowiska python3 jako zależności języka Python. Na przykład na platformie Ubuntu: `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`
>
>

### <a name="azure-packages"></a>Pakiety platformy Azure
Następnie zainstaluj pakiety **Azure Batch** i **Azure Storage** dla środowiska Python. Oba pakiety można zainstalować za pomocą narzędzia **pip** i pliku *requirements.txt* dostępnych tutaj:

`/azure-batch-samples/Python/Batch/requirements.txt`

Wydaj poniższe polecenie **pip**, aby zainstalować pakiety usług Batch i Storage:

`pip install -r requirements.txt`

Możesz także ręcznie zainstalować pakiety [azure-batch][pypi_batch] i [azure-storage][pypi_storage] dla środowiska Python:

`pip install azure-batch`<br/>
`pip install azure-storage`

> [!TIP]
> Jeśli używasz konta nieuprzywilejowanego, może być konieczne umieszczenie prefiksu `sudo` na początku poleceń. Na przykład `sudo pip install -r requirements.txt`. Więcej informacji na temat instalowania pakietów dla środowiska Python znajduje się w temacie [Instalowanie pakietów][pypi_install] w witrynie python.org.
>
>

## <a name="batch-python-tutorial-code-sample"></a>Przykład kodu z samouczka dotyczącego usługi Batch dla środowiska Python
Przykład kodu z samouczka dotyczącego usługi Batch dla środowiska Python zawiera dwa skrypty w języku Python oraz kilka plików danych.

* **python_tutorial_client.py**: współdziała z usługami Batch i Storage w celu wykonania równoległego obciążenia w węzłach obliczeniowych (na maszynach wirtualnych). Skrypt *python_tutorial_client.py* jest uruchamiany na lokalnej stacji roboczej.
* **python_tutorial_task.py**: skrypt, który jest uruchamiany w węzłach obliczeniowych na platformie w celu wykonywania właściwej pracy. W przykładzie skrypt *python_tutorial_task.py* analizuje tekst w pliku pobranym z usługi Azure Storage (pliku wejściowym). Następnie tworzy plik tekstowy (plik wyjściowy) zawierający listę trzech słów najczęściej występujących w pliku wejściowym. Po utworzeniu pliku wyjściowego skrypt *python_tutorial_task.py* przekazuje plik do usługi Azure Storage. Dzięki temu można go pobrać do skryptu klienta uruchomionego na stacji roboczej. Skrypt *python_tutorial_task.py* jest uruchamiany równolegle w wielu węzłach obliczeniowych w usłudze Batch.
* **./data/taskdata\*.txt**: te trzy pliki tekstowe zawierają dane wejściowe dla podzadań uruchomionych w węzłach obliczeniowych.

Na poniższym diagramie przedstawiono podstawowe operacje, które są wykonywane przez klienta i skrypty podzadań. Ten podstawowy przepływ pracy jest typowy dla wielu rozwiązań obliczeniowych utworzonych za pomocą usługi Batch. Chociaż nie przedstawiono tu wszystkich funkcji dostępnych w usłudze Batch, prawie każdy scenariusz usługi Batch obejmuje elementy tego przepływu pracy.

![Przykładowy przepływ pracy w usłudze Batch][8]<br/>

[**Krok 1.**](#step-1-create-storage-containers) Utwórz **kontenery** w usłudze Azure Blob Storage.<br/>
[**Krok 2.**](#step-2-upload-task-script-and-data-files) Przekaż skrypt podzadań i pliki wejściowe do kontenerów.<br/>
[**Krok 3.**](#step-3-create-batch-pool) Utwórz **pulę** w usłudze Batch.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** Pula funkcji **StartTask** pobiera skrypt podzadań (python_tutorial_task.py) do węzłów, gdy zostają dołączone do puli.<br/>
[**Krok 4.**](#step-4-create-batch-job) Utwórz **zadanie** w usłudze Batch.<br/>
[**Krok 5.**](#step-5-add-tasks-to-job) Dodaj **podzadania** do zadania.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Zadania są zaplanowane do wykonania w węzłach.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Każde zadanie pobiera dane wejściowe z usługi Azure Storage, a następnie rozpoczyna się wykonywanie.<br/>
[**Krok 6.**](#step-6-monitor-tasks) Monitoruj podzadania.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Gdy zadania zostaną ukończone, przekazują dane wyjściowe do usługi Azure Storage.<br/>
[**Krok 7.**](#step-7-download-task-output) Pobierz dane wyjściowe podzadań z usługi Storage.

Jak wspomniano wcześniej, nie wszystkie rozwiązania usługi Batch obejmują dokładnie te kroki i mogą obejmować wiele innych, natomiast w tym przykładzie przedstawiono typowe procesy w ramach rozwiązania usługi Batch.

## <a name="prepare-client-script"></a>Przygotowanie skryptu klienta
Przed uruchomieniem przykładu dodaj poświadczenia kont usług Batch i Storage do skryptu *python_tutorial_client.py*. Jeśli jeszcze tego nie zrobiono, otwórz plik w preferowanym edytorze i zaktualizuj następujące wiersze przy użyciu poświadczeń.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
BATCH_ACCOUNT_NAME = ""
BATCH_ACCOUNT_KEY = ""
BATCH_ACCOUNT_URL = ""

# Storage account credentials
STORAGE_ACCOUNT_NAME = ""
STORAGE_ACCOUNT_KEY = ""
```

Poświadczenia konta usług Batch i Storage znajdziesz w blokach konta poszczególnych usług w witrynie [Azure Portal][azure_portal]:

![Poświadczenia usługi Batch w portalu][9]
![Poświadczenia usługi Storage w portalu][10]<br/>

W poniższych sekcjach analizowane są kroki używane przez skrypty do przetwarzania obciążenia w usłudze Batch. Zachęcamy do systematycznego odwoływania się do skryptów w edytorze podczas wykonywania instrukcji podanych w dalszych częściach tego artykułu.

Przejdź do poniższego wiersza skryptu **python_tutorial_client.py**, aby rozpocząć krok 1:

```python
if __name__ == '__main__':
```

## <a name="step-1-create-storage-containers"></a>Krok 1: tworzenie kontenerów w usłudze Storage
![Tworzenie kontenerów w usłudze Azure Storage][1]
<br/>

Usługa Batch ma wbudowaną funkcję obsługi interakcji z usługą Azure Storage. Kontenery na koncie usługi Storage będą udostępniać pliki potrzebne zadaniom, które będą uruchamiane na koncie usługi Batch. Kontenery zapewniają również miejsce do przechowywania danych wyjściowych wytworzonych przez zadania. Najpierw skrypt *python_tutorial_client.py* tworzy trzy kontenery w usłudze [Azure Blob Storage](../storage/common/storage-introduction.md#blob-storage):

* **aplikacja**: ten kontener będzie przechowywać skrypt języka Python uruchamiany przez podzadania *python_tutorial_task.py*.
* **dane wejściowe**: podzadania będą pobierać pliki danych do przetwarzania z kontenera *dane wejściowe*.
* **dane wyjściowe**: gdy podzadania ukończą przetwarzanie plików wejściowych, przekażą wyniki do kontenera *wyjściowego*.

Aby nawiązać interakcję z kontem usługi Storage i utworzyć kontenery, należy użyć pakietu [azure-storage][pypi_storage] do utworzenia obiektu [BlockBlobService][py_blockblobservice] — „klienta obiektu blob”. Następnie należy utworzyć trzy kontenery na koncie usługi Storage przy użyciu klienta obiektu blob.

```python
import azure.storage.blob as azureblob

# Create the blob client, for use in obtaining references to
# blob storage containers and uploading files to containers.
blob_client = azureblob.BlockBlobService(
    account_name=STORAGE_ACCOUNT_NAME,
    account_key=STORAGE_ACCOUNT_KEY)

# Use the blob client to create the containers in Azure Storage if they
# don't yet exist.
APP_CONTAINER_NAME = 'application'
INPUT_CONTAINER_NAME = 'input'
OUTPUT_CONTAINER_NAME = 'output'
blob_client.create_container(APP_CONTAINER_NAME, fail_on_exist=False)
blob_client.create_container(INPUT_CONTAINER_NAME, fail_on_exist=False)
blob_client.create_container(OUTPUT_CONTAINER_NAME, fail_on_exist=False)
```

Po utworzeniu kontenerów aplikacja może teraz przekazać pliki, które będą używane przez podzadania.

> [!TIP]
> Artykuł [How to use Azure Blob storage from Python](../storage/blobs/storage-python-how-to-use-blob-storage.md) (Jak korzystać z usługi Azure Blob Storage w środowisku Python) zawiera szczegółowe omówienie pracy z kontenerami i obiektami blob w usłudze Azure Storage. Powinna to być jedna z najważniejszych pozycji do przeczytania po rozpoczęciu pracy z usługą Batch.
>
>

## <a name="step-2-upload-task-script-and-data-files"></a>Krok 2: przekazywanie skryptu podzadań i plików danych
![Przekazywanie aplikacji podzadań i plików danych wejściowych do kontenerów][2]
<br/>

Podczas operacji przekazywania plików skrypt *python_tutorial_client.py* najpierw definiuje kolekcje ścieżek plików **aplikacji** i **danych wejściowych** występujących na komputerze lokalnym. Następnie przekazuje te pliki do kontenerów, które zostały utworzone w poprzednim kroku.

```python
# Paths to the task script. This script will be executed by the tasks that
# run on the compute nodes.
application_file_paths = [os.path.realpath('python_tutorial_task.py')]

# The collection of data files that are to be processed by the tasks.
input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                    os.path.realpath('./data/taskdata2.txt'),
                    os.path.realpath('./data/taskdata3.txt')]

# Upload the application script to Azure Storage. This is the script that
# will process the data files, and is executed by each of the tasks on the
# compute nodes.
application_files = [
    upload_file_to_container(blob_client, APP_CONTAINER_NAME, file_path)
    for file_path in application_file_paths]

# Upload the data files. This is the data that will be processed by each of
# the tasks executed on the compute nodes in the pool.
input_files = [
    upload_file_to_container(blob_client, INPUT_CONTAINER_NAME, file_path)
    for file_path in input_file_paths]
```

Przy użyciu wytwornika listy funkcja `upload_file_to_container` zostaje wywołana dla każdego pliku z kolekcji i zostają wypełnione dwie kolekcje [ResourceFile][py_resource_file]. Poniżej widoczna jest funkcja `upload_file_to_container`:

```python
def upload_file_to_container(block_blob_client, container_name, path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """

    import datetime
    import azure.storage.blob as azureblob
    import azure.batch.models as batchmodels

    blob_name = os.path.basename(path)

    print('Uploading file {} to container [{}]...'.format(path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### <a name="resourcefiles"></a>ResourceFiles
Klasa [ResourceFile][py_resource_file] zawiera podzadania w usłudze Batch z adresem URL do pliku w usłudze Azure Storage, które są pobierane do węzła obliczeniowego przed uruchomieniem tego podzadania. Właściwość [ResourceFile][py_resource_file].**blob_source** określa pełny adres URL pliku przechowywanego w usłudze Azure Storage. Adres URL może także zawierać sygnaturę dostępu współdzielonego (SAS), która zapewnia bezpieczny dostęp do pliku. Większość typów podzadań w ramach usługi Batch obejmuje właściwość *ResourceFiles*, m.in.:

* [CloudTask][py_task]
* [StartTask][py_starttask]
* [JobPreparationTask][py_jobpreptask]
* [JobReleaseTask][py_jobreltask]

W tym przykładzie nie używa się typów podzadań JobPreparationTask ani JobReleaseTask, ale więcej informacji o nich można znaleźć w artykule [Run job preparation and completion tasks on Azure Batch compute nodes](batch-job-prep-release.md) (Uruchamianie podzadań przygotowania i ukończenia zadania w węzłach obliczeniowych w usłudze Azure Batch).

### <a name="shared-access-signature-sas"></a>Sygnatura dostępu współdzielonego (SAS)
Sygnatury dostępu współdzielonego to ciągi, które zapewniają bezpieczny dostęp do kontenerów i obiektów blob w usłudze Azure Storage. Skrypt *python_tutorial_client.py* używa sygnatur dostępu współdzielonego dla kontenera i obiektów blob i służy do pokazania, jak uzyskać te ciągi sygnatur dostępu współdzielonego z usługi Storage.

* **Sygnatury dostępu współdzielonego obiektów blob**: Funkcja StartTask puli używa sygnatur dostępu współdzielonego obiektów blob podczas pobierania skryptu podzadań i plików danych wejściowych z usługi Storage (zobacz [rok 3](#step-3-create-batch-pool) poniżej). Funkcja `upload_file_to_container` w skrypcie *python_tutorial_client.py* obejmuje kod, który uzyskuje dostęp do sygnatur dostępu współdzielonego poszczególnych obiektów blob. Odbywa się to przez wywołanie [BlockBlobService.make_blob_url][py_make_blob_url] w module usługi Storage.
* **Sygnatura dostępu współdzielonego kontenera**: gdy poszczególne podzadania zakończą pracę w węźle obliczeniowym, przekażą pliki wyjściowe do kontenera *dane wyjściowe* w usłudze Azure Storage. W tym celu skrypt *python_tutorial_task.py* korzysta z sygnatury dostępu współdzielonego kontenera, która umożliwia zapis w kontenerze. Funkcja `get_container_sas_token` w skrypcie *python_tutorial_client.py* uzyskuje sygnaturę dostępu współdzielonego kontenera, która jest następnie przekazywana jako argument wiersza polecenia do podzadań. W kroku 5 [Dodawanie podzadań do zadania](#step-5-add-tasks-to-job) omówiono używanie sygnatury dostępu współdzielonego kontenera.

> [!TIP]
> Aby dowiedzieć się więcej o zapewnieniu bezpiecznego dostępu do danych na koncie usługi Storage, zapoznaj się z dwuczęściową serią dotyczącą sygnatur dostępu współdzielonego: [Part 1: Understanding the SAS model](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (Część 1: Opis modelu SAS) i [Part 2: Create and use a SAS with the Blob service](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md) (Część 2: Tworzenie i korzystanie z modelu SAS za pomocą usługi Blob). 
>
>

## <a name="step-3-create-batch-pool"></a>Krok 3: tworzenie puli usługi Batch
![Tworzenie puli usługi Batch][3]
<br/>

**Pula** usługi Batch jest kolekcją węzłów obliczeniowych (maszyn wirtualnych), w których usługa Batch wykonuje podzadania danego zadania.

Po przekazaniu skryptu podzadań i plików danych do konta usługi Storage skrypt *python_tutorial_client.py* zaczyna wchodzić w interakcję z usługą Batch przy użyciu modułu usługi Batch dla środowiska Python. W tym celu zostaje utworzony klient [BatchServiceClient][py_batchserviceclient]:

```python
# Create a Batch service client. We'll now be interacting with the Batch
# service in addition to Storage.
credentials = batchauth.SharedKeyCredentials(BATCH_ACCOUNT_NAME,
                                             BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL)
```

Następnie tworzona jest pula węzłów obliczeniowych na koncie usługi Batch z wywołaniem funkcji `create_pool`.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Podczas tworzenia puli należy zdefiniować parametr [PoolAddParameter][py_pooladdparam] określający kilka właściwości puli:

* **Identyfikator** puli (element *id* — wymagany)<p/>Podobnie jak w przypadku większości obiektów w usłudze Batch nowa pula musi mieć unikatowy identyfikator w ramach konta usługi Batch. Tworzony kod odnosi się do tej puli przez użycie jej identyfikatora. Identyfikator ten służy także do identyfikacji puli w witrynie [Azure Portal][azure_portal].
* **Liczba węzłów obliczeniowych** (element *target_dedicated* — wymagany)<p/>Ta właściwość określa, ile maszyn wirtualnych należy wdrożyć w puli. Należy pamiętać, że wszystkie konta usługi Batch mają domyślny **przydział**, który ogranicza liczbę **rdzeni** (a tym samym węzłów obliczeniowych) na koncie usługi Batch. Domyślne limity przydziału oraz instrukcje [zwiększania limitów przydziału](batch-quota-limit.md#increase-a-quota) (np. maksymalnej liczby rdzeni na koncie usługi Batch) znajdują się w artykule [Quotas and limits for the Azure Batch service](batch-quota-limit.md) (Limity przydziału i limity dla usługi Azure Batch). Jeśli zaczniesz zastanawiać się: „Dlaczego moja pula nie może przekroczyć X rdzeni?”, przyczyną może być ten podstawowy przydział.
* **System operacyjny** dla węzłów (element *virtual_machine_configuration* **lub** *cloud_service_configuration* — wymagany)<p/>W skrypcie *python_tutorial_client.py* zostanie utworzona pula węzłów systemu Linux przy użyciu polecenia [VirtualMachineConfiguration][py_vm_config]. Funkcja `select_latest_verified_vm_image_with_node_agent_sku` pakietu `common.helpers` upraszcza pracę z obrazami dostępnymi w portalu [Marketplace usługi Azure Virtual Machines][vm_marketplace]. Więcej informacji o używaniu obrazów z rynku znajduje się w artykule [Provision Linux compute nodes in Azure Batch pools](batch-linux-nodes.md) (Inicjowanie obsługi węzłów obliczeniowych systemu Linux w pulach usługi Azure Batch).
* **Rozmiar węzłów obliczeniowych** (element *vm_size* — wymagany)<p/>Ponieważ należy określać węzły obliczeniowe systemu Linux dla parametru [VirtualMachineConfiguration][py_vm_config], określamy rozmiar maszyny wirtualnej (`STANDARD_A1` w tym przykładzie) na podstawie artykułu [Sizes for virtual machines in Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Rozmiary maszyn wirtualnych na platformie Azure). Więcej informacji znajduje się w artykule [Provision Linux compute nodes in Azure Batch pools](batch-linux-nodes.md) (Inicjowanie obsługi węzłów obliczeniowych systemu Linux w pulach usługi Azure Batch).
* **Podzadanie uruchamiania** (element *start_task* — niewymagany)<p/>Wraz z powyższymi fizycznymi właściwościami węzłów można też określić funkcję [StartTask][py_starttask] dla puli (nie jest to wymagane). Funkcja StartTask jest wykonywana w każdym węźle, gdy tylko ten węzeł zostanie dołączony do puli, oraz za każdym razem, gdy węzeł będzie uruchamiany ponownie. Podzadanie StartTask jest szczególnie przydatne w przypadku przygotowywania węzłów obliczeniowych do wykonywania podzadań, takich jak instalowanie aplikacji, które są uruchamiane przez podzadania.<p/>W tej przykładowej aplikacji podzadanie StartTask kopiuje pliki, które pobiera z usługi Storage (określone za pomocą właściwości **resource_files** podzadania StartTask) z *katalogu roboczego* podzadania StartTask do *współdzielonego* katalogu, do którego mają dostęp wszystkie podzadania uruchomione w węźle. Zasadniczo powoduje to skopiowanie pliku `python_tutorial_task.py` do współdzielonego katalogu w każdym węźle, gdy węzeł dołącza do puli, by miały do niego dostęp wszystkie podzadania uruchomione w węźle.

Zwróć uwagę na wywołanie do funkcji pomocnika `wrap_commands_in_shell`. Funkcja ta z kolekcji oddzielnych poleceń tworzy jeden wiersz polecenia odpowiedni dla właściwości wiersza polecenia podzadania.

W powyższym fragmencie kodu warto również zwrócić uwagę na użycie dwóch zmiennych środowiskowych we właściwości **command_line** podzadania StartTask: `AZ_BATCH_TASK_WORKING_DIR` i `AZ_BATCH_NODE_SHARED_DIR`. Każdy węzeł obliczeniowy w puli usługi Batch jest automatycznie konfigurowany za pomocą kilku zmiennych środowiskowych właściwych dla usługi Batch. Wszystkie procesy wykonywane przez zadanie mają dostęp do tych zmiennych środowiskowych.

> [!TIP]
> Informacje na temat zmiennych środowiskowych dostępnych dla węzłów obliczeniowych w puli usługi Batch oraz na temat katalogów roboczych podzadań znajdują się w sekcjach **Environment settings for tasks** (Ustawienia środowiska dla podzadań) i **Files and directories** (Pliki i katalogi) w [omówieniu dotyczącym funkcji usługi Azure Batch](batch-api-basics.md).
>
>

## <a name="step-4-create-batch-job"></a>Krok 4: tworzenie zadania w usłudze Batch
![Tworzenie zadania w usłudze Batch][4]<br/>

**Zadanie** usługi Batch jest kolekcją podzadań i jest skojarzone z pulą węzłów obliczeniowych. Podzadania tego zadania są wykonywane w węzłach obliczeniowych skojarzonej puli.

Zadań można użyć nie tylko do organizowania i śledzenia podzadań w powiązanych obciążeniach, ale także do nakładania pewnych ograniczeń — takich jak maksymalny czas wykonywania zadania (a co za tym idzie podzadań, które ono obejmuje), a także priorytet zadania w odniesieniu do innych zadań w ramach konta usługi Batch. Jednak w tym przykładzie zadanie jest skojarzone tylko z pulą, która została utworzona w kroku 3. Żadne dodatkowe właściwości nie są konfigurowane.

Wszystkie zadania usługi Batch są skojarzone z określoną pulą. To skojarzenie wskazuje, w których węzłach są wykonywane podzadania wchodzące w skład zadania. Należy określić pulę za pomocą właściwości [PoolInformation][py_poolinfo], jak pokazano we fragmencie kodu poniżej.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Teraz, kiedy zadanie zostało utworzone, są dodawane podzadania, aby wykonać pracę.

## <a name="step-5-add-tasks-to-job"></a>Krok 5: dodawanie podzadań do zadania
![Dodawanie podzadań do zadania][5]<br/>
*(1) Podzadania są dodawane do zadania, (2) podzadania są planowane do uruchomienia w węzłach i (3) podzadania pobierają pliki danych do przetwarzania*

**Podzadania** usługi Batch to pojedyncze jednostki robocze, które są wykonywane w węzłach obliczeniowych. Podzadanie ma wiersz polecenia i uruchamia skrypty lub pliki wykonywalne określone w tym wierszu polecenia.

Aby praca została rzeczywiście wykonana, należy dodać podzadania do zadania. Każde podzadanie typu [CloudTask][py_task] jest konfigurowane za pomocą właściwości wiersza polecenia i właściwości [ResourceFiles][py_resource_file] (tak jak w przypadku podzadania StartTask w puli), które podzadanie pobiera do węzła, zanim zostanie automatycznie wykonany jego wiersz polecenia. W przykładzie każde podzadanie przetwarza tylko jeden plik. W związku z tym jego kolekcja ResourceFiles zawiera jeden element.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [!IMPORTANT]
> Podczas uzyskiwania dostępu do zmiennych środowiskowych (takich jak `$AZ_BATCH_NODE_SHARED_DIR`) lub wykonywania aplikacji nieznajdującej się w parametrze `PATH` węzła wiersze poleceń podzadań muszą wywoływać powłokę bezpośrednio, jak w przypadku polecenia `/bin/sh -c MyTaskApplication $MY_ENV_VAR`. To wymaganie nie jest konieczne, jeśli podzadania wykonują aplikację w elemencie `PATH` węzła i nie odnoszą się do żadnych zmiennych środowiskowych.
>
>

W pętli `for` w powyższym fragmencie kodu widać, że wiersz polecenia dla podzadania jest zbudowany z pięciu argumentów wiersza polecenia, które zostają przekazane do skryptu *python_tutorial_task.py*:

1. **filepath**: jest to ścieżka lokalna do pliku zgodnie z jego lokalizacją w węźle. Gdy obiekt ResourceFile w funkcji `upload_file_to_container` został utworzony w kroku 2 powyżej, nazwa pliku została użyta dla tej właściwości (parametru `file_path` w konstruktorze ResourceFile). Oznacza to, że plik znajduje się w tym samym katalogu w węźle co skrypt *python_tutorial_task.py*.
2. **numwords**: najczęściej występujące *N* słów powinno być zapisywane do pliku wyjściowego.
3. **storageaccount**: nazwa konta usługi Storage, do którego należy kontener, do którego powinny zostać przekazane dane wyjściowe podzadania.
4. **storagecontainer**: nazwa kontenera usługi Storage, do którego powinny zostać przekazane pliki wyjściowe.
5. **sastoken**: sygnatura dostępu współdzielonego (SAS), która zapewnia prawo do zapisu w kontenerze **dane wyjściowe** w usłudze Azure Storage. Skrypt *python_tutorial_task.py* korzysta z sygnatury dostępu współdzielonego podczas tworzenia odwołania BlockBlobService. Zapewnia to prawo do zapisu do kontenera bez konieczności podawania klucza dostępu do konta magazynu.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## <a name="step-6-monitor-tasks"></a>Krok 6: monitorowanie podzadań
![Monitorowanie podzadań][6]<br/>
*Skrypt (1) monitoruje podzadania pod kątem stanu ukończenia, a (2) podzadania przekazują dane wynikowe do usługi Azure Storage*

Gdy podzadania są dodawane do zadania, zostają automatycznie dodane do kolejki i zaplanowane do wykonania w węzłach obliczeniowych w puli skojarzonej z zadaniem. Na podstawie określonych przez użytkownika ustawień usługa Batch samodzielnie obsługuje dodawanie podzadań do kolejki, ich planowanie, ponawianie prób ich wykonania oraz inne czynności administracyjne.

Istnieje wiele sposobów, w jakie można monitorować wykonanie podzadań. Funkcja `wait_for_tasks_to_complete` w skrypcie *python_tutorial_client.py* stanowi prosty przykład monitorowania podzadań pod kątem pewnego stanu, w tym przypadku stanu [ukończone][py_taskstate].

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## <a name="step-7-download-task-output"></a>Krok 7: pobranie danych wyjściowych podzadań
![Pobieranie danych wyjściowych zadań podrzędnych z usługi Storage][7]<br/>

Po ukończeniu zadania można pobrać dane wyjściowe podzadań przy użyciu usługi Azure Storage. Odbywa się to przy użyciu wywołania funkcji `download_blobs_from_container` w skrypcie *python_tutorial_client.py*:

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [!NOTE]
> Wywołanie funkcji `download_blobs_from_container` w skrypcie *python_tutorial_client.py* określa, że pliki powinny być pobierane do Twojego katalogu głównego. Można także swobodnie modyfikować tę lokalizację danych wyjściowych.
>
>

## <a name="step-8-delete-containers"></a>Krok 8: usuwanie kontenerów
Ponieważ użytkownik jest rozliczany za dane przechowywane w usłudze Azure Storage, zawsze dobrym rozwiązaniem jest usunięcie wszystkich obiektów blob, które nie są już potrzebne do zadań w ramach usługi Batch. W skrypcie *python_tutorial_client.py* odbywa się to przy użyciu trzech wywołań funkcji [BlockBlobService.delete_container][py_delete_container]:

```python
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Krok 9: usuwanie zadania i puli
W ostatnim kroku zostaje wyświetlony monit o usunięcie zadania i puli, które zostały utworzone za pomocą skryptu *python_tutorial_client.py*. Mimo że nie są naliczane opłaty za same zadania i podzadania, *są* naliczane opłaty za węzły obliczeniowe. W związku z tym zaleca się przydzielanie węzłów tylko zależnie do potrzeb. Usuwanie nieużywanych pul może odbywać się podczas konserwacji.

Dla obu obiektów [JobOperations][py_job] i [PoolOperations][py_pool] klienta BatchServiceClient istnieją odpowiednie metody usuwania, które są wywoływane, jeśli potwierdzisz usunięcie:

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [!IMPORTANT]
> Należy pamiętać, że opłaty są naliczane za zasoby obliczeniowe — usunięcie nieużywanych pul zminimalizuje koszty. Ponadto należy pamiętać, że usunięcie puli spowoduje usunięcie wszystkich węzłów obliczeniowych w tej puli i że po usunięciu puli danych z węzłów nie da się odzyskać.
>
>

## <a name="run-the-sample-script"></a>Uruchamianie przykładowego skryptu
Po uruchomieniu skryptu *python_tutorial_client.py* z [przykładu kodu][github_article_samples] samouczka, dane wyjściowe konsoli przypominają poniższe. Zostanie wyświetlony przycisk wstrzymania po pojawieniu się komunikatu `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...` podczas tworzenia i uruchamiania węzłów obliczeniowych puli oraz wykonywania poleceń w podzadaniu startowym puli. Użyj witryny [Azure Portal][azure_portal] do monitorowania puli, węzłów obliczeniowych, zadania i podzadań w trakcie wykonywania i po nim. Użyj witryny [Azure Portal][azure_portal] lub programu [Microsoft Azure Storage Explorer][storage_explorer] do wyświetlania zasobów usługi Storage (kontenerów i obiektów blob) tworzonych przez aplikację.

> [!TIP]
> Uruchom skrypt *python_tutorial_client.py* z katalogu `azure-batch-samples/Python/Batch/article_samples`. Skrypt ten używa ścieżki względnej w celu zaimportowania modułu `common.helpers`, dlatego w przypadku uruchomienia skryptu w innym katalogu może pojawić się komunikat o błędzie `ImportError: No module named 'common'`.
>
>

Typowy czas wykonywania wynosi **około 5–7 minut** w przypadku uruchomienia przykładu w konfiguracji domyślnej.

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## <a name="next-steps"></a>Następne kroki
Możesz wprowadzić zmiany w skryptach *python_tutorial_client.py* i *python_tutorial_task.py* w celu poeksperymentowania z różnymi scenariuszami obliczeniowymi. Na przykład spróbuj dodać opóźnienie wykonywania do skryptu *python_tutorial_task.py* w celu symulowania podzadań długotrwałych i monitorowania ich w portalu. Spróbuj dodać więcej podzadań lub dostosować liczbę węzłów obliczeniowych. Dodaj logikę do sprawdzenia i zezwól na użycie istniejącej puli, aby przyspieszyć czas wykonywania.

Po zapoznaniu się z podstawowym przepływem pracy rozwiązania usługi Batch nadszedł czas, by poszerzyć wiedzę na temat dodatkowych funkcji w usłudze Batch.

* Przejrzyj artykuł [Overview of Azure Batch features](batch-api-basics.md) (Omówienie funkcji w usłudze Azure Batch), który zalecamy użytkownikom rozpoczynającym korzystanie z tej usługi.
* Przeczytaj inne artykuły dotyczące programowania w usłudze Batch w części **Development in-depth** (Szczegółowy opis programowania) w [ścieżce szkoleniowej dotyczącej usługi Batch][batch_learning_path].
* Zapoznaj się z inną implementacją przetwarzania obciążenia „N najczęściej występujących słów” za pomocą usługi Batch w przykładzie [TopNWords][github_topnwords].

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: https://docs.microsoft.com/en-us/python/api/azure.batch.batchserviceclient?view=azure-python
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: https://docs.microsoft.com/en-us/python/api/azure.batch.models.CloudTask?view=azure-python
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: https://docs.microsoft.com/en-us/python/api/azure.batch.operations.JobOperations?view=azure-python
[py_jobpreptask]: https://docs.microsoft.com/en-us/python/api/azure.batch.models.JobPreparationTask?view=azure-python
[py_jobreltask]: https://docs.microsoft.com/en-us/python/api/azure.batch.models.JobReleaseTask?view=azure-python
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: https://docs.microsoft.com/en-us/python/api/azure.batch.operations.PoolOperations?view=azure-python
[py_pooladdparam]: https://docs.microsoft.com/en-us/python/api/azure.batch.models.PoolAddParameter?view=azure-python
[py_poolinfo]: https://docs.microsoft.com/en-us/python/api/azure.batch.models.PoolInformation?view=azure-python
[py_resource_file]: https://docs.microsoft.com/en-us/python/api/azure.batch.models.ResourceFile?view=azure-python
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: https://docs.microsoft.com/en-us/python/api/azure.batch.models.StartTask?view=azure-python
[py_starttask]: https://docs.microsoft.com/en-us/python/api/azure.batch.models.StartTask?view=azure-python
[py_task]: https://docs.microsoft.com/en-us/python/api/azure.batch.models.CloudTask?view=azure-python
[py_taskstate]: https://docs.microsoft.com/en-us/python/api/azure.batch.models.TaskState?view=azure-python
[py_vm_config]: https://docs.microsoft.com/en-us/python/api/azure.batch.models.VirtualMachineConfiguration?view=azure-python
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage
[pypi_install]: https://packaging.python.org/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Tworzenie kontenerów w usłudze Azure Storage"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "Przekazywanie aplikacji podzadań i plików danych wejściowych do kontenerów"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Tworzenie puli usługi Batch"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Tworzenie zadania w usłudze Batch"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "Dodawanie podzadań do zadania"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "Monitorowanie podzadań"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Pobieranie danych wyjściowych podzadań z usługi Storage"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Przepływ pracy w rozwiązaniu usługi Batch (pełny diagram)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "Poświadczenia usługi Batch w portalu"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "Poświadczenia usługi Storage w portalu"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Przepływ pracy rozwiązania w usłudze Batch (diagram minimalny)"
