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
ms.openlocfilehash: 82e3885021a2f2309dfed456d472e7027b8d6cf2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Uruchamianie zadania szkoleniowego CNTK przy użyciu interfejsu wiersza polecenia platformy Azure

Ten przewodnik Szybki start zawiera szczegóły dotyczące używania interfejsu wiersza polecenia (CLI) platformy Azure do uruchamiania zadań szkoleniowych Microsoft Cognitive Toolkit (CNTK) przy użyciu usługi Batch AI. Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów.

W tym przykładzie używa się bazy danych MNIST ręcznie zapisanych obrazów do szkolenia splotowej sieci neuronowej (CNN) w jednowęzłowym klastrze procesorów GPU zarządzanym przez usługę Batch AI. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Ten przewodnik Szybki start wymaga najnowszej wersji interfejsu wiersza polecenia platformy Azure. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

Dostawcy zasobów usługi Batch AI również muszą być zarejestrowani dla subskrypcji przy użyciu programu Azure Cloud Shell lub interfejsu wiersza polecenia platformy Azure. Rejestracja dostawcy może potrwać do 15 minut.

```azurecli
az provider register -n Microsoft.BatchAI
az provider register -n Microsoft.Batch
```


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Klastry i zadania usługi Batch AI to zasoby platformy Azure i muszą być umieszczone w grupie zasobów platformy Azure.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create).

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*. Następnie używane jest polecenie [az configure](/cli/azure/reference-index#az_configure) do ustawienia tej grupy zasobów i lokalizacji jako domyślnych.

```azurecli
az group create --name myResourceGroup --location eastus

az configure --defaults group=myResourceGroup

az configure --defaults location=eastus
```

>[!NOTE]
>Ustawienie wartości domyślnych dla polecenia `az` jest krokiem opcjonalnym. Możesz nie ustawiać wartości domyślnych. Jeśli zdecydujesz się ustawić wartości domyślne, po zakończeniu samouczka usuń ustawienia domyślne. Usuń ustawienia domyślne przy użyciu następujących poleceń:
>
>```azurecli
>az configure --defaults group=''
>
>az configure --defaults location=''
>```
>

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Ten przewodnik Szybki start używa konta magazynu platformy Azure do hostowania danych i skryptów na potrzeby zadania szkolenia. Utwórz konto magazynu przy użyciu polecenia [az storage account create](/cli/azure/storage/account#az_storage_account_create).

```azurecli
az storage account create --name mystorageaccount --sku Standard_LRS
```

>[!NOTE]
>Każde konto magazynu musi mieć unikatową nazwę. W poprzednim poleceniu `az` i innych podobnych poleceniach w tym samouczku należy zastąpić wartość ustawienia `mystorageaccount` nazwą konta magazynu.

## <a name="prepare-azure-file-share"></a>Przygotowanie udziału plików platformy Azure

W celach ilustracyjnych ten przewodnik Szybki start używa udziału plików platformy Azure do hostowania danych szkoleniowych i skryptów na potrzeby zadania uczenia.

1. Utwórz udział plików o nazwie *batchaiquickstart* przy użyciu polecenia [az storage share create](/cli/azure/storage/share#az_storage_share_create).

  ```azurecli
  az storage share create --account-name mystorageaccount --name batchaiquickstart
  ```
2. W udziale utwórz katalog o nazwie *mnistcntksample* przy użyciu polecenia [az storage directory create](/cli/azure/storage/directory#az_storage_directory_create).

  ```azurecli
  az storage directory create --share-name batchaiquickstart  --name mnistcntksample
  ```

3. Pobierz [pakiet przykładowy](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) i rozpakuj go. Przekaż zawartość do katalogu przy użyciu polecenia [az storage file upload](/cli/azure/storage/file#az_storage_file_upload):

  ```azurecli
  az storage file upload --share-name batchaiquickstart --source Train-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source Test-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source ConvNet_MNIST.py --path mnistcntksample
  ```


## <a name="create-gpu-cluster"></a>Tworzenie klastra procesora GPU
Użyj polecenia [az batchai cluster create](/cli/azure/batchai/cluster#az_batchai_cluster_create), aby utworzyć klaster usługi Batch AI zawierający jeden węzeł maszyny wirtualnej z procesorem GPU. W tym przykładzie maszyna wirtualna uruchamia domyślny obraz Ubuntu LTS. Określ zamiast tego wartość `image UbuntuDSVM`, aby uruchomić maszynę wirtualną do uczenia głębokiego firmy Microsoft, która obsługuje dodatkowe struktury szkoleniowe. Rozmiar NC6 ma jeden procesor GPU NVIDIA K80. Zainstaluj udział plików w folderze o nazwie *azurefileshare*. Pełna ścieżka do tego folderu w węźle obliczeniowym procesora GPU to $AZ_BATCHAI_MOUNT_ROOT/azurefileshare.


```azurecli
az batchai cluster create --name mycluster --vm-size STANDARD_NC6 --image UbuntuLTS --min 1 --max 1 --storage-account-name mystorageaccount --afs-name batchaiquickstart --afs-mount-path azurefileshare --user-name <admin_username> --password <admin_password>
```


Po utworzeniu klastra dane wyjściowe będą podobne do poniższych:

```azurecli
{
  "allocationState": "resizing",
  "allocationStateTransitionTime": "2017-10-05T02:09:03.194000+00:00",
  "creationTime": "2017-10-05T02:09:01.998000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
  "location": "eastus",
  "name": "mycluster",
  "nodeSetup": {
    "mountVolumes": {
      "azureBlobFileSystems": null,
      "azureFileShares": [
        {
          "accountName": "batchaisamples",
          "azureFileUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart",
          "credentialsInfo": {
            "accountKey": null,
            "accountKeySecretUrl": null
          },
          "directoryMode": "0777",
          "fileMode": "0777",
          "relativeMountPath": "azurefileshare"
        }
      ],
      "fileServers": null,
      "unmanagedFileSystems": null
    },
    "setupTask": null
  },
  "nodeStateCounts": {
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T02:09:02.857000+00:00",
  "resourceGroup": "myresourcegroup",
  "scaleSettings": {
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": {
    "id": null
  },
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "adminUserName": "demoUser",
    "adminUserPassword": null,
    "adminUserSshPublicKey": null
  },
  "virtualMachineConfiguration": {
    "imageReference": {
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04-LTS",
      "version": "latest"
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
```
## <a name="get-cluster-status"></a>Pobieranie stanu klastra

Aby uzyskać przegląd stanu klastra, uruchom polecenie [az batchai cluster list](/cli/azure/batchai/cluster#az_batchai_cluster_list):

```azurecli
az batchai cluster list -o table
```

Dane wyjściowe będą podobne do następujących:

```azurecli
Name        Resource Group    VM Size        State     Idle    Running    Preparing    Unusable    Leaving
---------   ----------------  -------------  -------   ------  ---------  -----------  ----------  --------
mycluster   myresourcegroup   STANDARD_NC6   steady    1       0          0            0            0
```

Aby uzyskać więcej szczegółów, uruchom polecenie [az batchai cluster show](/cli/azure/batchai/cluster#az_batchai_cluster_show). Polecenie zwraca wszystkie właściwości klastra wyświetlane po utworzeniu klastra.

Klaster będzie gotowy, gdy węzły zostaną przydzielone i zostanie zakończone przygotowanie (patrz atrybut `nodeStateCounts`). Jeśli wystąpił błąd, atrybut `errors` zawiera opis błędu.

## <a name="create-training-job"></a>Tworzenie zadania szkolenia

Gdy klaster jest gotowy, skonfiguruj i prześlij zadanie uczenia.

1. Utwórz plik szablonu JSON dla tworzenia zadania o nazwie job.json:

  ```JSON
  {
    "properties": {
        "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
       "inputDirectories": [{
            "id": "SAMPLE",
            "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
        }],
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
            "pathSuffix": "model",
            "type": "custom"
        }],
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0"
            }
        },
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL"
        }
    }
  }
  ```
2. Przy użyciu polecenia [az batchai job create](/cli/azure/batchai/job#az_batchai_job_create) utwórz zadanie o nazwie *myjob*, aby móc uruchomić je w klastrze:

  ```azurecli
  az batchai job create --name myjob --cluster-name mycluster --config job.json
  ```

Dane wyjściowe będą podobne do następujących:

```azurecli
{
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
    "resourceGroup": "myresourcegroup"
  },
  "cntkSettings": {
    "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py"
  },
  "constraints": {
    "maxTaskRetryCount": null,
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": {
    "imageSourceRegistry": {
      "credentials": null,
      "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0",
      "serverUrl": null
    }
  },
  "creationTime": "2017-10-05T06:41:42.163000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": {
    "endTime": null,
    "errors": null,
    "exitCode": null,
    "lastRetryTime": null,
    "retryCount": null,
    "startTime": "2017-10-05T06:41:44.392000+00:00"
  },
  "executionState": "running",
  "executionStateTransitionTime": "2017-10-05T06:41:44.953000+00:00",
  "experimentName": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.BatchAI/jobs/myjob",
  "inputDirectories": [
    {
      "id": "SAMPLE",
      "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
    }
  ],
  "jobPreparation": null,
  "location": null,
  "name": "cntk_job",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
      "pathSuffix": "model",
      "type": "Custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T06:41:44.238000+00:00",
  "resourceGroup": "demo",
  "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "CNTK",
  "type": "Microsoft.BatchAI/Jobs"
}
```

## <a name="monitor-job"></a>Monitorowanie zadania

Użyj polecenia [az batchai job list](/cli/azure/batchai/job#az_batchai_job_list), aby uzyskać przegląd stanu zadania:

```azurecli
az batchai job list -o table
```

Dane wyjściowe będą podobne do następujących:

```azurecli
Name        Resource Group    Cluster    Cluster RG      Nodes  State    Exit code
----------  ----------------  ---------  --------------- -----  -------  -----------
myjob       myresourcegroup   mycluster  myresourcegroup 1      running

```

Aby uzyskać więcej szczegółów, uruchom polecenie [az batchai job show](/cli/azure/batchai/job#az_batchai_job_show).

Atrybut `executionState` zawiera bieżący stan wykonania zadania:

* `queued`: zadanie oczekuje na dostępność węzłów klastra
* `running`: zadanie zostało uruchomione
*   `succeeded` (lub `failed`): zadanie jest zakończone; atrybut `executionInfo` zawiera szczegóły dotyczące wyniku


## <a name="list-stdout-and-stderr-output"></a>Wyświetlanie danych wyjściowych dzienników stdout i stderr
Użyj polecenia [az batchai job list-files](/cli/azure/batchai/job#az_batchai_job_list_files), aby wyświetlić listę linków do plików dziennika stdout i stderr:

```azurecli
az batchai job list-files --name myjob --output-directory-id stdouterr
```

Dane wyjściowe będą podobne do następujących:

```azurecli
[
  {
    "contentLength": 733,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stderr.txt?sv=2016-05-31&sr=f&sig=Rh%2BuTg9C1yQxm7NfA9YWiKb%2B5FRKqWmEXiGNRDeFMd8%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:38+00:00",
    "name": "stderr.txt"
  },
  {
    "contentLength": 300,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stdout.txt?sv=2016-05-31&sr=f&sig=jMhJfQOGry9jr4Hh3YyUFpW5Uaxnp38bhVWNrTTWMtk%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:29+00:00",
    "name": "stdout.txt"
  }
]
```


## <a name="observe-output"></a>Sprawdzanie danych wyjściowych

Istnieje możliwość przesyłania strumieniowego lub śledzenia plików wyjściowych zadania podczas wykonywania zadania. W poniższym przykładzie użyto polecenia [az batchai job stream-file](/cli/azure/batchai/job#az_batchai_job_stream_file) do przesyłania strumieniowego dziennika stderr.txt:

```azurecli
az batchai job stream-file --job-name myjob --output-directory-id stdouterr --name stderr.txt
```

Dane wyjściowe będą podobne do następujących. Aby przerwać wyświetlanie danych wyjściowych, naciśnij klawisze [Ctrl]-[C].

```azurecli
…
Finished Epoch[2 of 40]: [Training] loss = 0.104846 * 60000, metric = 3.00% * 60000 3.849s (15588.5 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.077043 * 60000, metric = 2.23% * 60000 3.902s (15376.7 samples/s);
Finished Epoch[4 of 40]: [Training] loss = 0.063050 * 60000, metric = 1.82% * 60000 3.811s (15743.9 samples/s);
…

```

## <a name="delete-resources"></a>Usuwanie zasobów

Użyj polecenia [az batchai job delete](/cli/azure/batchai/job#az_batchai_job_delete), aby usunąć zadanie:

```azurecli
az batchai job delete --name myjob
```
Użyj polecenia [az batchai cluster delete](/cli/azure/batchai/cluster#az_batchai_cluster_delete), aby usunąć klaster:

```azurecli
az batchai cluster delete --name mycluster
```

Użyj polecenia `az group delete`, aby usunąć grupę zasobów utworzoną na potrzeby tego przewodnika Szybki start:

```azurecli
az group delete --name myResourceGroup
```

## <a name="restore-azure-cli-20-default-settings"></a>Przywracanie domyślnych ustawień interfejsu wiersza polecenia platformy Azure w wersji 2.0

Usuń poprzednio skonfigurowane ustawienia domyślne dla lokalizacji i grupy zasobów:

```azurecli
az configure --defaults group=''

az configure --defaults location=''
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono sposób uruchamiania zadania szkolenia CNTK w klastrze usługi Batch AI przy użyciu interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się więcej o używaniu usługi Batch AI z różnymi zestawami narzędzi, zobacz [przepisy na szkolenie](https://github.com/Azure/BatchAI).

Aby dowiedzieć się więcej o używaniu interfejsu wiersza polecenia platformy Azure w wersji 2.0 na potrzeby zarządzania usługą Batch AI, zobacz [dokumentację github](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md).
