---
title: Samouczek — rozproszone szkolenie przy użyciu usługi Azure Batch AI i struktury Horovod | Microsoft Docs
description: Samouczek — jak szkolić rozproszony model w strukturze Horovod przy użyciu usługi Azure Batch AI i interfejsu wiersza polecenia platformy Azure.
services: batch-ai
author: johnwu10
manager: jeconnoc
ms.service: batch-ai
ms.topic: tutorial
ms.date: 09/03/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 45255845d8645391ee33471830ac2ef27870a40d
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408633"
---
# <a name="tutorial-train-a-distributed-model-with-horovod"></a>Samouczek: Szkolenie modelu rozproszonego przy użyciu struktury Horovod

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Ten samouczek przedstawia szkolenie rozproszonego modelu uczenia głębokiego uruchamiane równolegle na wielu węzłach w klastrze usługi Batch AI. Batch AI to zarządzana usługa szkolenia modeli uczenia maszynowego i sztucznej inteligencji ze skalowaniem w klastrach procesorów GPU platformy Azure. 

W tym samouczku przedstawiono typowy przepływ pracy usługi Batch AI oraz sposoby interakcji z zasobami usługi Batch AI przy użyciu wiersza polecenia platformy Azure. Omawiane tematy to m.in.:

> [!div class="checklist"]
> * Konfigurowanie obszaru roboczego, eksperymentu i klastra usługi Batch AI
> * Konfigurowanie udziału plików platformy Azure na dane wejściowe i wyjściowe
> * Wprowadzenie równoległości do modelu uczenia głębokiego przy użyciu struktury Horovod
> * Przesyłanie zadania szkoleniowego
> * Monitorowanie zadania
> * Pobieranie wyników szkolenia

Na potrzeby tego samouczka model wykrywania obiektów jest modyfikowany w celu działania równoległego w strukturze [Horovod](https://github.com/uber/horovod). Szkolenie modelu jest przeprowadzane na obrazach [zestawu danych CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html). Zadanie szkoleniowe działa w klastrze zawierającym 24 procesory vCPU i 4 procesory GPU i zajmuje około 60 minut.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, zauważ, że ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.38 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

## <a name="why-use-horovod"></a>Dlaczego warto używać struktury Horovod?

Horovod to używana w tym samouczku struktura do szkolenia rozproszonego dla bibliotek Tensorflow, Keras i PyTorch. Za pomocą struktury Horovod można przekonwertować skrypt szkolenia przeznaczony do działania na jednym procesorze GPU na działający efektywnie w systemie rozproszonym przy użyciu zaledwie kilku wierszy kodu.

Usługa Batch AI obsługuje nie tylko strukturę Horovod, ale także kilka innych popularnych struktur open source. Koniecznie przejrzyj postanowienia licencyjne dotyczące struktury używanej do szkolenia modeli w środowisku produkcyjnym.

## <a name="prepare-the-batch-ai-environment"></a>Przygotowanie środowiska usługi Batch AI

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Użyj polecenia `az group create`, aby utworzyć grupę zasobów o nazwie `batchai.horovod` w regionie `eastus`. Grupa zasobów służy do wdrażania zasobów usługi Batch AI.

```azurecli-interactive
az group create --name batchai.horovod --location eastus
```

### <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Utwórz obszar roboczy usługi Batch AI przy użyciu polecenia `az batchai workspace create`. Obszar roboczy jest kolekcją najwyższego poziomu innych zasobów usługi Batch AI. Poniższe polecenie tworzy obszar roboczy `batchaidev` w grupie zasobów.

```azurecli-interactive
az batchai workspace create --resource-group batchai.horovod --workspace batchaidev 
```

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Eksperyment usługi Batch AI grupuje zadania (co najmniej jedno) do wspólnego wykonywania zapytań i zarządzania. Poniższe polecenie `az batchai experiment create` tworzy eksperyment o nazwie `cifar` w obszarze roboczym i grupie zasobów.

```azurecli-interactive
az batchai experiment create --resource-group batchai.horovod --workspace batchaidev --name cifar 
```

## <a name="set-up-a-gpu-cluster"></a>Konfigurowanie klastra procesora GPU

Następnie skonfiguruj klaster procesorów GPU do przebiegu eksperymentu. Usługa Batch AI zapewnia elastyczny zakres opcji do dostosowywania klastrów do określonych potrzeb.

Poniższe polecenie `az batchai cluster create` tworzy 4-węzłowy klaster o nazwie `nc6cluster` w obszarze roboczym i grupie zasobów. Domyślnie na maszynach wirtualnych w klastrze działa obraz serwera Ubuntu przeznaczony do hostowania aplikacji opartych na kontenerach. Węzły klastra w tym przykładzie mają rozmiar `Standard_NC6`, czyli zawierają jeden procesor GPU NVIDIA Tesla K80.

```azurecli-interactive
az batchai cluster create --resource-group batchai.horovod --workspace batchaidev --name nc6cluster --vm-priority dedicated  --vm-size Standard_NC6 --target 4 --generate-ssh-keys
```

Uruchom polecenie `az batchai cluster show`, aby wyświetlić stan klastra. Zazwyczaj pełne ustanowienie klastra zajmuje kilka minut.

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --output table
```

Na wczesnym etapie tworzenia klaster znajduje się w stanie `resizing`. Wykonuj kolejne kroki podczas zmian stanu klastra. Klaster jest gotowy do uruchomienia zadania szkoleniowego, gdy ma stan `steady`, a węzły są w stanie `idle`. Na przykład:

```
Name        Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
----------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
nc6cluster  batchai.horovod  batchaidev   STANDARD_NC6  steady        4          0            0          0           0
```

## <a name="set-up-storage"></a>Konfigurowanie magazynu

Użyj polecenia `az storage account create`, aby utworzyć konto magazynu do przechowywania skryptu i danych wyjściowych szkolenia.

```azurecli-interactive
az storage account create --resource-group batchai.horovod --name mystorageaccount --location eastus --sku Standard_LRS
```

Utwórz na koncie udział plików platformy Azure o nazwie `myshare` przy użyciu polecenia `az storage share create`:

```azurecli-interactive
az storage share create --name myshare --account-name mystorageaccount
```

W praktyce ten sam magazyn może służyć do wielu zadań i eksperymentów. Aby zachować porządek, utwórz katalog w udziale plików w celu przechowywania plików związanych z tym kreślonym eksperymentem. Następujące polecenie `az storage directory create` tworzy katalog o nazwie `cifar`.

```azurecli-interactive
az storage directory create --name cifar --share-name myshare --account-name mystorageaccount
```

Następnym krokiem jest przygotowanie rzeczywistego skryptu szkolenia, który zostanie przekazany do nowo utworzonego katalogu.

## <a name="create-the-training-script"></a>Tworzenie skryptu szkolenia

W tym eksperymencie uruchomisz skrypt w języku Python, zaktualizowany za pomocą kilku zmian w celu równoległego uruchomienia modelu wykrywania obiektów przy użyciu struktury Horovod. [Model oryginalny](https://raw.githubusercontent.com/keras-team/keras/master/examples/cifar10_cnn.py) używa biblioteki Keras z zapleczem TensorFlow. 

W katalogu roboczym w powłoce użyj ulubionego edytora tekstów, aby utworzyć plik o nazwie `cifar_cnn_distributed.py` z następującą zawartością. Zmiany oryginalnego kodu źródłowego mają komentarze z prefiksem `HOROVOD`.

```python
from __future__ import print_function
import keras
from keras.datasets import cifar10
from keras.preprocessing.image import ImageDataGenerator
from keras.models import Sequential
from keras.layers import Dense, Dropout, Activation, Flatten
from keras.layers import Conv2D, MaxPooling2D
import tensorflow as tf
import horovod.keras as hvd
import os
from keras import backend as K
import math
import argparse 

# HOROVOD: initialize Horovod.
hvd.init()

# HOROVOD: pin GPU to be used to process local rank (one GPU per process)
config = tf.ConfigProto()
config.gpu_options.allow_growth = True
config.gpu_options.visible_device_list = str(hvd.local_rank())
K.set_session(tf.Session(config=config))

batch_size = 32
num_classes = 10
# HOROVOD: adjust number of epochs based on number of GPUs.
epochs = int(math.ceil(100.0 / hvd.size()))

data_augmentation = True
num_predictions = 20
# BATCH AI: change save directory to mounted storage path
parser = argparse.ArgumentParser()
parser.add_argument("-d", "--dir", help="directory to save model to")
args = parser.parse_args()
save_dir = os.path.join(args.dir, 'saved_models')
model_name = 'keras_cifar10_trained_model.h5'

# The data, split between train and test sets:
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
print('x_train shape:', x_train.shape)
print(x_train.shape[0], 'train samples')
print(x_test.shape[0], 'test samples')

# Convert class vectors to binary class matrices.
y_train = keras.utils.to_categorical(y_train, num_classes)
y_test = keras.utils.to_categorical(y_test, num_classes)

model = Sequential()
model.add(Conv2D(32, (3, 3), padding='same',
                 input_shape=x_train.shape[1:]))
model.add(Activation('relu'))
model.add(Conv2D(32, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Conv2D(64, (3, 3), padding='same'))
model.add(Activation('relu'))
model.add(Conv2D(64, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Flatten())
model.add(Dense(512))
model.add(Activation('relu'))
model.add(Dropout(0.5))
model.add(Dense(num_classes))
model.add(Activation('softmax'))

# HOROVOD: adjust learning rate based on number of GPUs.
opt = keras.optimizers.rmsprop(lr=0.0001 * hvd.size(), decay=1e-6)

# HOROVOD: add Horovod Distributed Optimizer.
opt = hvd.DistributedOptimizer(opt)

# Let's train the model using RMSprop
model.compile(loss='categorical_crossentropy',
              optimizer=opt,
              metrics=['accuracy'])

callbacks = [
    # HOROVOD: broadcast initial variable states from rank 0 to all other processes.
    # This is necessary to ensure consistent initialization of all workers when
    # training is started with random weights or restored from a checkpoint.
    hvd.callbacks.BroadcastGlobalVariablesCallback(0),
]

# HOROVOD: save checkpoints only on worker 0 to prevent other workers from corrupting them.
if hvd.rank() == 0:
    callbacks.append(keras.callbacks.ModelCheckpoint('./checkpoint-{epoch}.h5'))

x_train = x_train.astype('float32')
x_test = x_test.astype('float32')
x_train /= 255
x_test /= 255

if not data_augmentation:
    print('Not using data augmentation.')
    model.fit(x_train, y_train,
              batch_size=batch_size,
              epochs=epochs,
              validation_data=(x_test, y_test),
              shuffle=True)
else:
    print('Using real-time data augmentation.')
    # This will do preprocessing and realtime data augmentation:
    datagen = ImageDataGenerator(
        featurewise_center=False,  # set input mean to 0 over the dataset
        samplewise_center=False,  # set each sample mean to 0
        featurewise_std_normalization=False,  # divide inputs by std of the dataset
        samplewise_std_normalization=False,  # divide each input by its std
        zca_whitening=False,  # apply ZCA whitening
        zca_epsilon=1e-06,  # epsilon for ZCA whitening
        rotation_range=0,  # randomly rotate images in the range (degrees, 0 to 180)
        width_shift_range=0.1,  # randomly shift images horizontally (fraction of total width)
        height_shift_range=0.1,  # randomly shift images vertically (fraction of total height)
        shear_range=0.,  # set range for random shear
        zoom_range=0.,  # set range for random zoom
        channel_shift_range=0.,  # set range for random channel shifts
        fill_mode='nearest',  # set mode for filling points outside the input boundaries
        cval=0.,  # value used for fill_mode = "constant"
        horizontal_flip=True,  # randomly flip images
        vertical_flip=False,  # randomly flip images
        rescale=None,  # set rescaling factor (applied before any other transformation)
        preprocessing_function=None,  # set function that will be applied on each input
        data_format=None,  # image data format, either "channels_first" or "channels_last"
        validation_split=0.0)  # fraction of images reserved for validation (strictly between 0 and 1)

    # Compute quantities required for feature-wise normalization
    # (std, mean, and principal components if ZCA whitening is applied).
    datagen.fit(x_train)

    # Fit the model on the batches generated by datagen.flow().
    model.fit_generator(datagen.flow(x_train, y_train,
                                     batch_size=batch_size),
                        epochs=epochs,
                        validation_data=(x_test, y_test),
                        workers=4)

# Save model and weights
if not os.path.isdir(save_dir):
    os.makedirs(save_dir)
model_path = os.path.join(save_dir, model_name)
model.save(model_path)
print('Saved trained model at %s ' % model_path)

# Score trained model.
scores = model.evaluate(x_test, y_test, verbose=1)
print('Test loss:', scores[0])
print('Test accuracy:', scores[1])
```

Jak pokazano w tym przykładzie, wystarczy wprowadzić do modelu kilka aktualizacji, aby umożliwić szkolenie rozproszone przy użyciu struktury Horovod. 

Należy pamiętać, że ten skrypt używa stosunkowo małego modelu i zestawu danych w celach demonstracyjnych, więc ten rozproszony model niekoniecznie pokaże znaczącą poprawę wydajności. Aby naprawdę przekonać się o potędze szkolenia rozproszonego, należy użyć znacznie większego modelu i zestawu danych.

## <a name="upload-the-training-script"></a>Przekazywanie skryptu szkolenia

Gdy skrypt będzie gotowy, następnym krokiem jest przekazanie go do katalogu udziału plików, który został utworzony wcześniej. Następujące polecenie `az storage file upload` przekazuje go z lokalnego katalogu roboczego do właściwej lokalizacji.

```azurecli-interactive
az storage file upload --path cifar --share-name myshare --source cifar_cnn_distributed.py --account-name mystorageaccount
```

## <a name="submit-the-training-job"></a>Przesyłanie zadania szkoleniowego

Po wykonaniu poprzednich kroków, należy utworzyć zadanie szkoleniowe. W usłudze Batch AI plik `job.json` służy do definiowania parametrów określających sposób uruchamiania zadania. Za pomocą ulubionego edytora tekstu utwórz plik konfiguracji zadania o nazwie `job.json` z następującą zawartością.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 4,
        "horovodSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar/cifar_cnn_distributed.py",
            "commandLineArgs": "--dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "jobPreparation": {
            "commandLine": "apt update; apt install mpi-default-dev mpi-default-bin -y; pip install keras horovod"
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

Wyjaśnienie właściwości:

| Właściwość | Opis |
| --------- | --------- |
| `nodeCount` | Liczba węzłów, która mają być przeznaczone do zadania. Tutaj zadanie będzie uruchamiane równolegle na `4` węzłach. |
| `horovodSettings` | Pole `pythonScriptFilePath` określa ścieżkę do skryptu Horovod, znajdującego się w utworzonym wcześniej katalogu `cifar`. Pole `commandLineArgs` zawiera argumenty wiersza polecenia do uruchamiania skryptu. W tym eksperymencie katalog miejsca zapisania modelu jest jedynym wymaganych argumentem. `$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare` to ścieżka, gdzie został zainstalowany udział plików. | 
| `stdOutErrPathPrefix` | Ścieżka do przechowywania wyników i dzienników zadania. W tym przykładzie jest to ten sam katalog `cifar`. |
| `jobPreparation` | Wszelkie specjalne instrukcje dotyczące przygotowania środowiska do uruchomienia zadania. Ten skrypt wymaga instalacji wskazanych pakietów MPI i Horovod. |
| `containerSettings` | Ustawienia kontenera, w którym działa zadanie. To zadanie używa kontenera platformy Docker wykorzystującego bibliotekę `tensorflow`.

Korzystając z konfiguracji, utwórz zadanie przy użyciu polecenia `az batchai job create`. Następujące polecenie umieszcza w kolejce nowe zadanie o nazwie `cifar_distributed` przy użyciu wszystkich zasobów, które zostały skonfigurowane do tej pory. 

```azurecli-interactive
az batchai job create --cluster nc6cluster --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --experiment cifar --config-file job.json --storage-account-name mystorageaccount
```

Jeśli węzły są obecnie zajęte, przed uruchomieniem zadania może upłynąć trochę czasu. Użyj polecenia `az batchai job show`, aby wyświetlić stan wykonania zadania.

```azurecli-interactive
az batchai job show --experiment cifar --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --query "executionState"
```

### <a name="visualize-the-distributed-training"></a>Wizualizacja rozproszonego szkolenia

Gdy zadanie zacznie działać, należy ponownie użyć polecenia `az batchai cluster show`, aby wykonać zapytanie o stan węzłów klastra. 

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --query "nodeStateCounts"
```

Dane wyjściowe powinny być podobne do poniższych, gdzie wszystkie cztery węzły są w stanie uruchomienia. Ten wynik pokazuje, że wszystkie cztery węzły są obecnie używane do szkolenia rozproszonego.

```
{
  "idleNodeCount": 0,
  "leavingNodeCount": 0,
  "preparingNodeCount": 0,
  "runningNodeCount": 4,
  "unusableNodeCount": 0
}
```

## <a name="monitor-the-job"></a>Monitorowanie zadania

### <a name="list-output-files"></a>Lista plików wyjściowych

Gdy zadanie jest uruchomione, użyj polecenia `az batchai job file list`, aby wyświetlić listę plików wyjściowych, które są generowane przez zadanie.

```azurecli-interactive
az batchai job file list --experiment cifar --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev --output table
```

W tym konkretnym eksperymencie dane wyjściowe powinny przypominać następujące. Ogólne dane wyjściowe zadania są rejestrowane w pliku `stdout.txt`, a plik `stderr.txt` zawiera wyniki dotyczące błędów występujących w głównym wykonaniu. Inne pliki zawierają dane wyjściowe, błędy i dzienniki przygotowania zadania odpowiadające poszczególnym węzłom.

```
Name                                                    Type       Size  Modified
------------------------------------------------------  ------  -------  -------------------------
execution-tvm-676767296_1-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_2-20180718t174802z-p.log        file      15094  2018-07-18T22:41:55+00:00
execution-tvm-676767296_3-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_4-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
stderr-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr.txt                                              file       7653  2018-07-18T22:46:32+00:00
stdout-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout.txt                                              file    2316480  2018-07-18T22:46:32+00:00
```

### <a name="stream-an-output-file"></a>Przesyłanie strumieniowe pliku wyjściowego

Polecenie `az batchai job file stream` służy do przesyłania strumieniowego zawartości pliku. W poniższym przykładzie przesyłamy strumieniowo główny dziennik wyjściowy.

```azurecli-interactive
az batchai job file stream --experiment cifar --file-name stdout.txt --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev
```

Gdy zadanie zostanie uruchomione, polecenie przesyła strumieniowo standardowe dane wyjściowe zadania szkoleniowego, przedstawiając dane wyjściowe podobne do następujących.

```
...
50000 train samples
10000 test samples
Using real-time data augmentation.
Epoch 1/25


   1/1563 [..............................] - ETA: 2:42:25 - loss: 2.3334 - acc: 0.0312   1/1563 [..............................] - ETA: 2:30:42 - loss: 2.2973 - acc: 0.0938
   1/1563 [..............................] - ETA: 30:36 - loss: 2.3175 - acc: 0.1250
   1/1563 [..............................] - ETA: 2:32:58 - loss: 2.3489 - acc: 0.0625
   2/1563 [..............................] - ETA: 1:21:59 - loss: 2.3230 - acc: 0.0625

   2/1563 [..............................]   2/1563 [..............................] - ETA: 1:16:09 - loss: 2.2913 - acc: 0.0938 - ETA: 1:17:15 - loss: 2.3147 - acc: 0.0781
   2/1563 [..............................] - ETA: 16:07 - loss: 2.3678 - acc: 0.0938
   3/1563 [..............................] - ETA: 55:05 - loss: 2.3232 - acc: 0.0938  
   3/1563 [..............................] - ETA: 51:57 - loss: 2.3185 - acc: 0.1146  
   3/1563 [..............................] - ETA: 51:12 - loss: 2.3179 - acc: 0.1042  
   3/1563 [..............................] - ETA: 11:13 - loss: 2.3504 - acc: 0.0833
   4/1563 [..............................] - ETA: 39:43 - loss: 2.3224 - acc: 0.1094
   4/1563 [..............................] - ETA: 42:09 - loss: 2.3049 - acc: 0.1250
   4/1563 [..............................] - ETA: 39:15 - loss: 2.3089 - acc: 0.1094
   4/1563 [..............................] - ETA: 9:16 - loss: 2.3316 - acc: 0.1016 
   5/1563 [..............................] - ETA: 39:51 - loss: 2.3153 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:58 - loss: 2.3197 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:35 - loss: 2.3148 - acc: 0.1062
   5/1563 [..............................] - ETA: 13:38 - loss: 2.3263 - acc: 0.1062
   6/1563 [..............................] - ETA: 35:48 - loss: 2.3168 - acc: 0.1198

   6/1563 [..............................]   6/1563 [..............................] - ETA: 34:13 - loss: 2.3142 - acc: 0.1198 - ETA: 33:51 - loss: 2.3162 - acc: 0.1042
   6/1563 [..............................] - ETA: 13:54 - loss: 2.3225 - acc: 0.1094
   7/1563 [..............................] - ETA: 30:53 - loss: 2.3181 - acc: 0.1071

   7/1563 [..............................]   7/1563 [..............................] - ETA: 29:32 - loss: 2.3149 - acc: 0.1161 - ETA: 29:13 - loss: 2.3140 - acc: 0.0938
   7/1563 [..............................] - ETA: 12:09 - loss: 2.3174 - acc: 0.1205
   8/1563 [..............................] - ETA: 26:04 - loss: 2.3113 - acc: 0.1133
   8/1563 [..............................] - ETA: 27:15 - loss: 2.3169 - acc: 0.1133
   8/1563 [..............................] - ETA: 10:51 - loss: 2.3152 - acc: 0.1172
...
```

Skrypt przeprowadza szkolenie przez 25 epok (czyli cykli przejścia przez zestaw danych szkoleniowych). Ten proces zajmuje około 60–30 minut. 

## <a name="retrieve-the-results"></a>Pobieranie wyników

Po ukończeniu działania skryptu, jeśli wszystko poszło dobrze, dokładność sprawdzania poprawności powinna wynosić około 70–75%, a szkolony model zostanie zapisywany do udziału plików w `cifar/saved_models/keras_cifar10_trained_model.h5`. 

Szkolenie modelu jest zwykle częścią większego przepływu pracy. Na przykład można uwidocznić szkolony model w innej aplikacji. Aby pobrać lokalnie szkolony model, użyj polecenia `az storage file download`. 

```azurecli-interactive
az storage file download --path cifar/saved_models/keras_cifar10_trained_model.h5 --share-name myshare --account-name mystorageaccount
```
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu działania zadania najlepszym rozwiązaniem w celu zaoszczędzenia kosztów obliczeniowych jest skalowanie w dół wszystkich klastrów do `0 nodes`, aby nie była naliczana opłata za czas bezczynności. Użyj następującego polecenia `az batchai cluster resize`. 

```azurecli-interactive
az batchai cluster resize --name nc6cluster --resource-group batchai.horovod --target 0 --workspace batchaidev
```

Później zmień rozmiar na 1 lub więcej węzłów, aby uruchamiać zadania. 

Jeśli nie zamierzasz w przyszłości korzystać z obszaru roboczego i konta magazynu, usuń grupę zasobów za pomocą polecenia `az group delete`. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów stanowiących jej część.

```azurecli-interactive
az group delete --name batchai.horovod
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obszaru roboczego, eksperymentu i klastra usługi Batch AI
> * Konfigurowanie udziału plików platformy Azure na dane wejściowe i wyjściowe
> * Wprowadzenie równoległości modelu przy użyciu struktury Horovod
> * Przesyłanie zadania szkoleniowego
> * Monitorowanie zadania
> * Pobieranie wyników szkolenia

Przykłady użycia usługi Batch AI z różnymi strukturami są zawarte w przepisach w usłudze GitHub.

> [!div class="nextstepaction"]
> [Przepisy usługi Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes)
