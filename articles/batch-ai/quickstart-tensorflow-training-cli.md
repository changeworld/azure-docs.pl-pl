---
title: Samouczek Szybki start platformy Azure — szkolenie uczenia głębokiego — interfejs wiersza polecenia platformy | Microsoft Docs
description: Szybki start — szybko naucz się szkolić sieć neuronową uczenia głębokiego TensorFlow na jednym procesorze GPU za pomocą usługi Batch AI przy użyciu interfejsu wiersza polecenia platformy Azure
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 09/03/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 979253021f5503295e0572759b510e074ceb1a6b
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408072"
---
# <a name="quickstart-train-a-deep-learning-model-with-batch-ai"></a>Szybki start: szkolenie modelu uczenia głębokiego za pomocą usługi Batch AI

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

W tym przewodniku Szybki start przedstawiono szkolenie przykładowego modelu uczenia głębokiego na maszynie wirtualnej z procesorem GPU zarządzanej przez usługę Batch AI. Batch AI to zarządzana usługa, która umożliwia naukowcom zajmującym się danymi oraz badaczom sztucznej inteligencji szkolenie modeli sztucznej inteligencji i uczenia maszynowego na dużą skalę w klastrach z maszynami wirtualnymi platformy Azure. 

W tym przykładzie użyto interfejsu wiersza polecenia platformy Azure do skonfigurowania usługi Batch AI na potrzeby szkolenia przykładowej sieci neuronowej [TensorFlow](https://www.tensorflow.org/) w oparciu o [bazę danych MNIST](http://yann.lecun.com/exdb/mnist/) zawierającą ręcznie zapisane cyfry. Po ukończeniu tego przewodnika Szybki Start będziesz rozumieć kluczowe pojęcia związane z użyciem usługi Batch AI w celu szkolenia modelu sztucznej inteligencji lub uczenia maszynowego, co pozwoli Ci na podejmowanie prób szkolenia różnych modeli na większą skalę.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się na instalację i używanie interfejsu wiersza polecenia lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.38 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

W tym przewodniku Szybki start założono, że uruchamiasz polecenia w powłoce Bash w usłudze Cloud Shell lub na komputerze lokalnym. Jeśli znasz już przewodnik Szybki start dotyczący [tworzenia klastra usługi Batch AI za pomocą interfejsu wiersza polecenia platformy Azure](quickstart-create-cluster-cli.md), pomiń pierwsze dwa kroki dotyczące tworzenia grupy zasobów oraz klastra usługi Batch AI.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia `az group create`. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus2*. Koniecznie wybierz lokalizację Wschodnie stany USA 2 lub inną lokalizację, w której jest dostępna usługa Batch AI. 

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-batch-ai-cluster"></a>Tworzenie klastra usługi Batch AI

Najpierw użyj polecenia `az batchai workspace create`, aby utworzyć *obszar roboczy* usługi Batch AI. Obszar roboczy jest niezbędny do zorganizowania klastrów usługi Batch AI oraz innych zasobów.

```azurecli-interactive
az batchai workspace create \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Aby utworzyć klaster usługi Batch AI, użyj polecenia `az batchai cluster create`. W poniższym przykładzie utworzono klaster z jednym węzłem o następujących właściwościach:

* Korzysta z rozmiaru maszyny wirtualnej NC6, z jednym procesorem GPU NVIDIA Tesla K80. Platforma Azure oferuje wiele rozmiarów maszyn wirtualnych z różnymi procesorami GPU firmy NVIDIA.
* Uruchamia domyślny obraz serwera Ubuntu przeznaczony do hostowania aplikacji opartych na kontenerach. W ramach tej dystrybucji można uruchamiać większość obciążeń szkoleniowych. 
* Dodaje konto użytkownika o nazwie *myusername* i generuje klucze SSH, jeśli jeszcze nie znajdują się one w domyślnej lokalizacji kluczy (*~/.ssh*) w środowisku lokalnym.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --vm-size Standard_NC6 \
    --target 1 \
    --user-name myusername \
    --generate-ssh-keys
```

Dane wyjściowe polecenia zawierają właściwości klastra. Utworzenie i uruchomienie węzła może potrwać kilka minut. Aby sprawdzić stan klastra, uruchom polecenie `az batchai cluster show`.

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

Na wczesnym etapie tworzenia klastra dane wyjściowe będą przypominać dane w przykładzie poniżej, gdzie stanem klastra jest `resizing`:

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```

Wykonaj następujące czynności, aby przekazać skrypt szkolenia i utworzyć zadanie szkolenia, podczas gdy zmienia się stan klastra. Klaster jest gotowy do uruchomienia zadania szkolenia, gdy stanem jest `steady`, a jeden węzeł jest w stanie `Idle`.

## <a name="upload-training-script"></a>Przekazywanie skryptu szkolenia

Użyj polecenia `az storage account create`, aby utworzyć konto magazynu do przechowywania skryptu i danych wyjściowych szkolenia.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

Utwórz na koncie udział plików platformy Azure o nazwie `myshare` przy użyciu polecenia `az storage share create`:

```azurecli-interactive
az storage share create \
    --name myshare \
    --account-name mystorageaccount
```

Użyj polecenia `az storage directory create` do utworzenia katalogów w udziale plików platformy Azure. Utwórz katalog `scripts` dla skryptu szkolenia i katalog `logs` dla danych wyjściowych szkolenia:

```azurecli-interactive
# Create /scripts directory in file share
az storage directory create \
    --name scripts \
    --share-name myshare \
    --account-name mystorageaccount

# Create /logs directory in file share 
az storage directory create \
    --name logs \
    --share-name myshare \
    --account-name mystorageaccount
```

W powłoce Bash utwórz lokalny katalog roboczy i pobierz próbkę TensorFlow [convolutional.py](https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py). Skrypt w języku Python umożliwia szkolenie splotowej sieci neuronowej na podstawie zestawu obrazów MNIST obejmującego 60 000 ręcznie zapisanych cyfr od 0 do 9. Następnie następuje sprawdzanie modelu na podstawie zestawu przykładów testowych.

```bash
wget https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py
```

Przekaż skrypt do katalogu `scripts` w udziale przy użyciu polecenia `az storage file upload`.

```azurecli-interactive
az storage file upload \
    --share-name myshare \
    --path scripts \
    --source convolutional.py \
    --account-name mystorageaccount
```

## <a name="submit-training-job"></a>Przesyłanie zadania szkoleniowego

Najpierw utwórz *eksperyment* usługi Batch AI w obszarze roboczym przy użyciu polecenia `az batchai experiment create`. Eksperyment jest kontenerem logicznym zawierającym pokrewne zadania usługi Batch AI.

```azurecli-interactive
az batchai experiment create \
    --name myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

W katalogu roboczym utwórz plik konfiguracji zadania szkoleniowego `job.json` z następującą zawartością. Ten plik konfiguracji należy przekazać podczas przesyłania zadania szkoleniowego.

Ten plik `job.json` zawiera ustawienia umożliwiające zlokalizowanie pliku skryptu w języku Python oraz uruchomienie go w kontenerze TensorFlow w węźle procesora GPU. Określa również, gdzie powinny być zapisywane pliki wyjściowe zadania w magazynie platformy Azure. `<AZURE_BATCHAI_STORAGE_ACCOUNT>` wskazuje, że nazwa konta magazynu zostanie określona podczas przesyłania zadania.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 1,
        "tensorFlowSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/scripts/convolutional.py"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/logs",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

Użyj polecenia `az batchai job create`, aby przesłać zadanie do węzła, przekazując plik konfiguracji `job.json` oraz nazwę konta magazynu:

```azurecli-interactive
az batchai job create \
    --name myjob \
    --cluster mycluster \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --config-file job.json \
    --storage-account-name mystorageaccount
```

Polecenie zostaje zwrócone z właściwościami zadania, a jego ukończenie może potrwać kilka minut. Do monitorowania postępu tego zadania użyj polecenia `az batchai job file stream` do przesyłania strumieniowego pliku `stdout-wk-0.txt` ze standardowego katalogu wyjściowego w węźle. Skrypt szkolenia generuje ten plik po uruchomieniu zadania.  

```azurecli-interactive
az batchai job file stream \
    --job myjob \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --file-name stdout-wk-0.txt
```

Przykładowe dane wyjściowe:

```
File found with URL "https://mystorageaccount.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/myResourceGroup/workspaces/myworkspace/experiments/myexperiment/jobs/myjob/<JOB_ID>/stdouterr/stdout-wk-0.txt?sv=2016-05-31&sr=f&sig=Kih9baozMao8Ugos%2FVG%2BcsVsSeY1O%2FTocCNvLQhwtx4%3D&se=2018-06-20T22%3A07%3A30Z&sp=rl". Start streaming
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting data/train-images-idx3-ubyte.gz
Extracting data/train-labels-idx1-ubyte.gz
Extracting data/t10k-images-idx3-ubyte.gz
Extracting data/t10k-labels-idx1-ubyte.gz
Initialized!
Step 0 (epoch 0.00), 14.9 ms
Minibatch loss: 8.334, learning rate: 0.010000
Minibatch error: 85.9%
Validation error: 84.6%
Step 100 (epoch 0.12), 9.7 ms
Minibatch loss: 3.240, learning rate: 0.010000
Minibatch error: 6.2%
Validation error: 7.7%
Step 200 (epoch 0.23), 8.3 ms
Minibatch loss: 3.335, learning rate: 0.010000
Minibatch error: 7.8%
Validation error: 4.5%
Step 300 (epoch 0.35), 8.3 ms
Minibatch loss: 3.157, learning rate: 0.010000
Minibatch error: 3.1%
...
Step 8500 (epoch 9.89), 8.3 ms
Minibatch loss: 1.605, learning rate: 0.006302
Minibatch error: 0.0%
Validation error: 0.9%
Test error: 0.8%
```

Przesyłanie strumieniowe zostaje zatrzymane po zakończeniu zadania. Przykładowy skrypt przeprowadza szkolenie przez 10 *epok* (czyli cykli przejścia przez zestaw danych szkoleniowych). W tym przykładzie po 10 epokach przeszkolony model zwraca błąd testu jedynie na poziomie 0,8%.

## <a name="get-job-output"></a>Pobieranie danych wyjściowych zadania

Usługa Batch AI umożliwia tworzenie unikatowej struktury folderów w ramach konta magazynu dla danych wyjściowych każdego zadania. Ustaw zmienną środowiskową JOB_OUTPUT_PATH dla tej ścieżki. Następnie wyświetl listę plików wyjściowych w magazynie przy użyciu polecenia `az storage file list`:

```azurecli-interactive
export JOB_OUTPUT_PATH=$(az batchai job show --name myjob --experiment myexperiment --workspace myworkspace --resource-group myResourceGroup --query jobOutputDirectoryPathSegment --output tsv)

az storage file list \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr \
    --output table
```

Dane wyjściowe są podobne do następujących:

```
Name               Content Length  Type    Last Modified
---------------  ----------------  ------  ---------------
execution.log               14866  file
stderr-wk-0.txt              1527  file
stdout-wk-0.txt             11027  file
```

Użyj polecenia `az storage file download`, aby pobrać co najmniej jeden plik do lokalnego katalogu roboczego. Na przykład:

```azurecli-interactive
az storage file download \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr/stdout-wk-0.txt
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz kontynuować naukę z samouczkami i przykładami usługi Batch AI, korzystaj z obszaru roboczego usługi Batch AI, klastra oraz konta magazynu, które zostały utworzone w tym przewodniku Szybki start. 

Opłaty za korzystanie z klastra usługi Batch AI są naliczane, dopóki działają węzły. Jeśli chcesz zachować konfigurację klastra, gdy nie ma zadań do uruchomienia, zmień rozmiar klastra na 0 węzłów. 

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 0
```

Później, aby uruchomić swoje zadania, zmień go na 1 lub więcej węzłów. Gdy klaster nie jest już potrzebny, usuń go przy użyciu polecenia `az batchai cluster delete`:

```azurecli-interactive
az batchai cluster delete \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Gdy grupa zasobów nie jest już potrzebna, można ją usunąć dla usługi Batch AI i zasobów magazynu za pomocą polecenia `az group delete`. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki Start opisano, jak używać usługi Batch AI do szkolenia przykładowego modelu uczenia głębokiego TensorFlow na jednej maszynie wirtualnej z procesorem GPU przy użyciu interfejsu wiersza polecenia platformy Azure. Aby uzyskać informacje na temat dystrybucji szkolenia modeli w większym klastrze procesorów GPU, przejdź do samouczka dotyczącego usługi Batch AI.

> [!div class="nextstepaction"]
> [Samouczek dotyczący szkolenia rozproszonego](./tutorial-horovod-tensorflow.md)