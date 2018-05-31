---
title: Szybki start na platformie Azure — szkolenie CNTK przy użyciu usługi Batch AI — interfejs wiersza polecenia platformy Azure | Microsoft Docs
description: Szybko dowiedz się, jak uruchamiać zadanie szkoleniowe CNTK przy użyciu usługi Batch AI i interfejsu wiersza polecenia platformy Azure
services: batch-ai
documentationcenter: na
author: AlexanderYukhanov
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: Alexander.Yukhanov
ms.openlocfilehash: 3601ea412790c991892a0c05210d2551810287b8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33869023"
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Uruchamianie zadania szkoleniowego CNTK przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure 2.0 pozwala tworzyć zasoby usługi Batch AI i zarządzać nimi. Możesz tworzyć i usuwać klastry oraz serwery plików usługi Batch AI, a także przesyłać, przerywać, usuwać i monitorować zadania szkoleniowe.

W tym przewodniku Szybki start pokazano, jak utworzyć klaster procesorów GPU i uruchomić zadanie szkoleniowe przy użyciu zestawu Microsoft Cognitive Toolkit.

Skrypt szkoleniowy [ConvNet_MNIST.py](https://github.com/Azure/BatchAI/blob/master/recipes/CNTK/CNTK-GPU-Python/CNTK-GPU-Python.ipynb) jest dostępny na stronie usługi Batch AI w serwisie GitHub. Ten skrypt umożliwia szkolenie splotowej sieci neuronowej na podstawie bazy danych MNIST ręcznie zapisanych cyfr.

W oficjalnym przykładzie CNTK zostały wprowadzone zmiany umożliwiające podawanie lokalizacji szkoleniowego zestawu danych i katalogu wyjściowego za pomocą argumentów wiersza polecenia.

## <a name="quickstart-overview"></a>Omówienie przewodnika Szybki start

* Tworzenie jednowęzłowego klastra procesorów GPU (o rozmiarze maszyny wirtualnej `Standard_NC6`) o nazwie `nc6`
* Tworzenie nowego konta magazynu do przechowywania danych wejściowych i wyjściowych zadania
* Tworzenie udziału plików platformy Azure z folderami `logs` i `scripts` do przechowywania danych wyjściowych zadania i skryptów szkoleniowych
* Tworzenie kontenera obiektów blob platformy Azure `data` do przechowywania danych szkoleniowych
* Wdrażanie skryptu szkoleniowego i danych szkoleniowych w utworzonym udziale plików i kontenerze
* Konfigurowanie zadania pod kątem instalacji udziału plików platformy Azure i kontenera obiektów blob platformy Azure w węźle klastra oraz ich udostępnienia jako regularnego systemu plików w lokalizacjach `$AZ_BATCHAI_JOB_MOUNT_ROOT/logs`, `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts` i `$AZ_BATCHAI_JOB_MOUNT_ROOT/data`.
`AZ_BATCHAI_JOB_MOUNT_ROOT` jest zmienną środowiskową ustawianą przez usługę Batch AI na potrzeby zadania
* Monitorowanie wykonywania zadania dzięki przesyłaniu strumieniowemu jego standardowych danych wyjściowych
* Sprawdzanie danych wyjściowych zadania i wygenerowanych modeli po ukończeniu zadania
* Usuwanie wszystkich przydzielonych zasobów po zakończeniu pracy

# <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja Azure — jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dostęp do interfejsu wiersza polecenia platformy Azure 2.0 w wersji 2.0.31 lub nowszej. Możesz użyć interfejsu wiersza polecenia platformy Azure 2.0 dostępnego w usłudze [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) lub zainstalować go lokalnie za pomocą [tych instrukcji](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

# <a name="cloud-shell-only"></a>Korzystanie tylko z usługi Cloud Shell

Jeśli korzystasz z usługi Cloud Shell, zmień katalog roboczy na `/usr/$USER/clouddrive`, ponieważ w katalogu głównym nie ma wolnego miejsca:

```azurecli
cd /usr/$USER/clouddrive
```

# <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższe polecenie tworzy nową grupę zasobów ```batchai.quickstart``` w lokalizacji Wschodnie stany USA:

```azurecli
az group create -n batchai.quickstart -l eastus
```

# <a name="create-gpu-cluster"></a>Tworzenie klastra procesora GPU

Poniższe polecenie tworzy jednowęzłowy klaster procesorów GPU (o rozmiarze maszyny wirtualnej Standardowa_NC6) przy użyciu obrazu systemu operacyjnego Ubuntu DSVM:

```azurecli
az batchai cluster create -n nc6 -g batchai.quickstart -s Standard_NC6 -i UbuntuDSVM -t 1 --generate-ssh-keys
```

Oparta na dystrybucji Ubuntu maszyna wirtualna do analizy danych pozwala uruchamiać dowolne zadania szkoleniowe w kontenerach platformy Docker i popularne platformy uczenia głębokiego bezpośrednio na maszynie wirtualnej.

Opcja `--generate-ssh-keys` nakazuje interfejsowi wiersza polecenia platformy Azure wygenerować prywatne i publiczne klucze ssh, jeśli jeszcze tego nie zrobiono. Dostęp do węzłów klastra jest możliwy za pomocą bieżącej nazwy użytkownika i wygenerowanego klucza ssh.

W przypadku używania usługi Cloud Shell warto utworzyć kopię zapasową folderu ~/.ssh w magazynie trwałym.

Przykładowe dane wyjściowe:
```json
{
  "allocationState": "steady",
  "allocationStateTransitionTime": "2018-04-11T21:17:26.345000+00:00",
  "creationTime": "2018-04-11T20:12:10.758000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
  "location": "eastus",
  "name": "nc6",
  "nodeSetup": null,
  "nodeStateCounts": {
    "additionalProperties": {},
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T20:12:11.445000+00:00",
  "resourceGroup": "batchai.quickstart",
  "scaleSettings": {
    "additionalProperties": {},
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": null,
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "additionalProperties": {},
    "adminUserName": "alex",
    "adminUserPassword": null,
    "adminUserSshPublicKey": "<YOUR SSH PUBLIC KEY HERE>"
  },
  "virtualMachineConfiguration": {
    "additionalProperties": {},
    "imageReference": {
      "additionalProperties": {},
      "offer": "linux-data-science-vm-ubuntu",
      "publisher": "microsoft-ads",
      "sku": "linuxdsvmubuntu",
      "version": "latest",
      "virtualMachineImageId": null
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
}
```

# <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Poniższe polecenie tworzy nowe konto magazynu w tym samym regionie, w którym znajduje się grupa zasobów batchai.recipes. Zaktualizuj to polecenie, wprowadzając unikatową nazwę konta magazynu.

```azurecli
az storage account create -n <storage account name> --sku Standard_LRS -g batchai.quickstart
```

Jeśli wybrane konto magazynu nie będzie dostępne, powyższe polecenie zgłosi odpowiedni błąd. W takim przypadku wybierz inną nazwę i spróbuj ponownie.

# <a name="data-deployment"></a>Wdrażanie danych

## <a name="download-the-training-script-and-training-data"></a>Pobieranie skryptu szkoleniowego i danych szkoleniowych

* Pobierz wstępnie przetworzoną bazę danych MNIST z [tej lokalizacji](https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D) i wyodrębnij ją do bieżącego folderu.

W systemie GNU/Linux lub usłudze Cloud Shell:

```azurecli
wget "https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D" -O mnist_dataset.zip
unzip mnist_dataset.zip
```

Może być konieczne zainstalowanie programu `unzip`, jeśli nie ma go w używanej dystrybucji systemu GNU/Linux.

* Pobierz przykładowy skrypt [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py) do bieżącego folderu:

W systemie GNU/Linux lub usłudze Cloud Shell:

```azurecli
wget https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py
```

## <a name="create-azure-file-share-and-deploy-the-training-script"></a>Tworzenie udziału plików platformy Azure i wdrażanie skryptu szkoleniowego

Następujące polecenia umożliwiają utworzenie udziałów plików platformy Azure `scripts` i `logs` oraz skopiowanie skryptu szkoleniowego do folderu `cntk` w udziale `scripts`:

```azurecli
az storage share create -n scripts --account-name <storage account name>
az storage share create -n logs --account-name <storage account name>
az storage directory create -n cntk -s scripts --account-name <storage account name>
az storage file upload -s scripts --source ConvNet_MNIST.py --path cntk --account-name <storage account name> 
```

## <a name="create-a-blob-container-and-deploy-training-data"></a>Tworzenie kontenera obiektów blob i wdrażanie danych szkoleniowych

Następujące polecenia umożliwiają utworzenie kontenera obiektów blob platformy Azure `data` i skopiowanie danych szkoleniowych do folderu `mnist_cntk`:
```azurecli
az storage container create -n data --account-name <storage account name>
az storage blob upload-batch -s . --pattern '*28x28_cntk*' --destination data --destination-path mnist_cntk --account-name <storage account name>
```

# <a name="submit-training-job"></a>Przesyłanie zadania szkoleniowego

## <a name="prepare-job-configuration-file"></a>Przygotowywanie pliku konfiguracji zadania

Utwórz plik konfiguracji zadania szkoleniowego `job.json` z następującą zawartością:
```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-03-01/cntk.json",
    "properties": {
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs"
        }],
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/logs",
                    "relativeMountPath": "logs"
                },
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/scripts",
                    "relativeMountPath": "scripts"
                }
            ],
            "azureBlobFileSystems": [
                {
                    "accountName": "<AZURE_BATCHAI_STORAGE_ACCOUNT>",
                    "containerName": "data",
                    "relativeMountPath": "data"
                }
            ]
        }
    }
}
```

Ten plik konfiguracji zawiera następujące elementy:

* `nodeCount` — liczba węzłów wymaganych przez zadanie (1 w tym przewodniku Szybki start).
* `cntkSettings` — określa ścieżkę do skryptu szkoleniowego i argumenty wiersza polecenia. Argumenty wiersza polecenia zawierają ścieżkę do danych szkoleniowych i ścieżkę docelową do przechowywania wygenerowanych modeli. `AZ_BATCHAI_OUTPUT_MODEL` jest zmienną środowiskową ustawianą przez usługę Batch AI na podstawie konfiguracji katalogu wyjściowego (patrz poniżej).
* `stdOutErrPathPrefix` — ścieżka określająca lokalizację, w której usługa Batch AI utworzy katalogi zawierające dane wyjściowe i dzienniki zadania.
* `outputDirectories` — kolekcja katalogów wyjściowych, które mają zostać utworzone przez usługę Batch AI. Dla każdego katalogu usługa Batch AI tworzy zmienną środowiskową o nazwie `AZ_BATCHAI_OUTPUT_<id>`, gdzie element `<id>` jest identyfikatorem katalogu.
* `mountVolumes` — lista systemów plików, które mają zostać zainstalowane podczas wykonywania zadania. Te systemy plików są instalowane w lokalizacji `AZ_BATCHAI_JOB_MOUNT_ROOT/<relativeMountPath>`. `AZ_BATCHAI_JOB_MOUNT_ROOT` jest zmienną środowiskową ustawianą przez usługę Batch AI.
* `<AZURE_BATCHAI_STORAGE_ACCOUNT>` — informuje o tym, że podczas przesyłania zadania zostanie podana nazwa konta magazynu — za pośrednictwem parametru -storage-account-name lub zmiennej środowiskowej `AZURE_BATCHAI_STORAGE_ACCOUNT` na komputerze.

## <a name="submit-the-job"></a>Przesyłanie zadania

Aby przesłać zadanie do klastra, użyj następującego polecenia:

```azurecli
az batchai job create -n cntk_python_1 -r nc6 -g batchai.quickstart -c job.json --storage-account-name <storage account name>
```

Przykładowe dane wyjściowe:
```
{
  "additionalProperties": {},
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "additionalProperties": {},
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
    "resourceGroup": "batchai.quickstart"
  },
  "cntkSettings": {
    "additionalProperties": {},
    "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py"
  },
  "constraints": {
    "additionalProperties": {},
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": null,
  "creationTime": "2018-04-11T21:48:10.303000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": null,
  "executionState": "queued",
  "executionStateTransitionTime": "2018-04-11T21:48:10.303000+00:00",
  "experimentName": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/jobs/cntk_python_1",
  "inputDirectories": null,
  "jobOutputDirectoryPathSegment": "00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/b9576bae-e878-4fb2-9390-2e962356b5b1",
  "jobPreparation": null,
  "location": null,
  "mountVolumes": {
    "additionalProperties": {},
    "azureBlobFileSystems": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "containerName": "data",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "mountOptions": null,
        "relativeMountPath": "data"
      }
    ],
    "azureFileShares": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>,
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/logs",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "logs"
      },
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/scripts",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "scripts"
      }
    ],
    "fileServers": null,
    "unmanagedFileSystems": null
  },
  "name": "cntk_python_1",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "additionalProperties": {},
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
      "pathSuffix": null,
      "type": "custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T21:48:11.577000+00:00",
  "pyTorchSettings": null,
  "resourceGroup": "batchai.quickstart",
  "secrets": null,
  "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "cntk",
  "type": "Microsoft.BatchAI/Jobs"
}
```

# <a name="monitor-job-execution"></a>Monitorowanie wykonywania zadania

Skrypt szkoleniowy umieszcza informacje o postępie szkolenia w pliku `stderr.txt`, który znajduje się w standardowym katalogu wyjściowym. Postęp możesz monitorować za pomocą następującego polecenia:

```azurecli
az batchai job file stream -n cntk_python_1 -g batchai.quickstart -f stderr.txt
```

Przykładowe dane wyjściowe:
```
File found with URL "https://<YOU STORAGE ACCOUNT>.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>/stdouterr/stderr.txt?sv=2016-05-31&sr=f&sig=n86JK9YowV%2BPQ%2BkBzmqr0eud%2FlpRB%2FVu%2FFlcKZx192k%3D&se=2018-04-11T23%3A05%3A54Z&sp=rl". Start streaming
Selected GPU[0] Tesla K80 as the process wide default device.
-------------------------------------------------------------------
Build info:

        Built time: Jan 31 2018 15:03:41
        Last modified date: Tue Jan 30 03:26:13 2018
        Build type: release
        Build target: GPU
        With 1bit-SGD: no
        With ASGD: yes
        Math lib: mkl
        CUDA version: 9.0.0
        CUDNN version: 7.0.4
        Build Branch: HEAD
        Build SHA1: a70455c7abe76596853f8e6a77a4d6de1e3ba76e
        MPI distribution: Open MPI
        MPI version: 1.10.7
-------------------------------------------------------------------
Training 98778 parameters in 10 parameter tensors.

Learning rate per 1 samples: 0.001
Momentum per 1 samples: 0.0
Finished Epoch[1 of 40]: [Training] loss = 0.405960 * 60000, metric = 13.01% * 60000 21.741s (2759.8 samples/s);
Finished Epoch[2 of 40]: [Training] loss = 0.106030 * 60000, metric = 3.09% * 60000 3.638s (16492.6 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.078542 * 60000, metric = 2.32% * 60000 3.477s (17256.3 samples/s);
...
Final Results: Minibatch[1-11]: errs = 0.54% * 10000
```

Przesyłanie danych strumieniowych ustaje wraz z zakończeniem zadania (z wynikiem pomyślnym lub niepomyślnym).

# <a name="inspect-generated-model-files"></a>Przeprowadzanie inspekcji wygenerowanych plików modelu

Wygenerowane przez zadanie pliki modelu z atrybutem `id` o wartości `MODEL` są przechowywane w katalogu wyjściowym. Przy użyciu następującego polecenia możesz wyświetlić listę plików modelu i uzyskać adresy URL pobierania:

```azurecli
az batchai job file list -n cntk_python_1 -g batchai.quickstart -d MODEL
```

Przykładowe dane wyjściowe:
```
[
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:51+00:00",
    "name": "ConvNet_MNIST_0.dnn"
  },
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:55+00:00",
    "name": "ConvNet_MNIST_1.dnn"
  },
...

```

Inspekcję wygenerowanych plików możesz też przeprowadzić w portalu lub za pomocą Eksploratora usługi Azure Storage. Usługa Batch AI tworzy unikatową strukturę folderów dla poszczególnych zadań, co ułatwia znalezienie odpowiednich danych wyjściowych. Ścieżkę do folderu zawierającego dane wyjściowe możesz znaleźć przy użyciu atrybutu `jobOutputDirectoryPathSegment` przesłanego zadania:

```azurecli
az batchai job show -n cntk_python_1 -g batchai.quickstart --query jobOutputDirectoryPathSegment
```

Przykładowe dane wyjściowe:
```
"00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>"
```

# <a name="delete-resources"></a>Usuwanie zasobów

Aby usunąć grupę zasobów i wszystkie przydzielone zasoby, uruchom następujące polecenie:

```azurecli
az batchai group delete -n batchai.quickstart -y
```
