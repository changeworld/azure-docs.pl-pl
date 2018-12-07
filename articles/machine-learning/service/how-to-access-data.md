---
title: Dostęp do danych w magazynów danych z usługi Azure Machine Learning
description: Jak korzystać z magazynów danych w magazynie danych programu access w czasie szkolenie przy użyciu usługi Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 50df0647372832647712d514dd9d4651d8d9cc2d
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014058"
---
# <a name="how-to-access-data-during-training"></a>Jak uzyskać dostęp do danych podczas szkolenia
Magazyn danych umożliwia dostęp i interakcję z danymi w usłudze Azure Machine Learning w przepływach pracy.

Magazyn danych w usłudze Azure Machine Learning to Abstrakcja za pośrednictwem [usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction). Magazyn danych może odwoływać się albo [obiektów Blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) kontenera lub [udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) jako podstawowy magazyn. 

## <a name="create-a-datastore"></a>Utwórz magazyn danych
Aby korzystać z magazynów danych, należy najpierw [obszaru roboczego](concept-azure-machine-learning-architecture.md#workspace). Rozpocznij od albo [tworzenia nowego obszaru roboczego](quickstart-create-workspace-with-python.md) lub pobierania istniejące:

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Użyj domyślnego magazynu danych
Nie ma potrzeby tworzenia i konfigurowania konta magazynu.  Każdy obszar roboczy ma domyślne magazyn danych, którą można uruchomić od razu korzystać.

Aby uzyskać magazyn danych z domyślnego obszaru roboczego:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Rejestrowanie magazynu danych
Jeśli masz istniejące usługi Azure Storage, należy zarejestrować go jako magazyn danych w obszarze roboczym usługi. Jako magazyn danych można zarejestrować kontenera obiektów Blob platformy Azure lub udziału plików platformy Azure. Wszystkie metody register znajdują się na `Datastore` klasy i mają następującą formę `register_azure_*`.

#### <a name="azure-blob-container-datastore"></a>Kontener obiektów Blob platformy Azure w magazynie danych
Aby zarejestrować magazyn danych w kontenerze obiektów Blob platformy Azure:

```Python
ds = Datastore.register_azure_blob_container(workspace=ws, 
                                             datastore_name='your datastore name', 
                                             container_name='your azure blob container name',
                                             account_name='your storage account name', 
                                             account_key='your storage account key',
                                             create_if_not_exists=True)
```

#### <a name="azure-file-share-datastore"></a>Magazyn danych z udziału plików platformy Azure
Aby zarejestrować magazyn danych z udziału plików platformy Azure:

```Python
ds = Datastore.register_azure_file_share(workspace=ws, 
                                         datastore_name='your datastore name', 
                                         container_name='your file share name',
                                         account_name='your storage account name', 
                                         account_key='your storage account key',
                                         create_if_not_exists=True)
```

### <a name="get-an-existing-datastore"></a>Pobieranie istniejącego magazynu danych
Aby wykonać zapytanie dla zarejestrowanego magazynu danych według nazwy:
```Python
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Możesz również uzyskać wszystkich magazynów danych dla obszaru roboczego:
```Python
datastores = ws.datastores()
for name, ds in datastores.items(),
    print(name, ds.datastore_type)
```

Dla wygody należy ustawić jeden z Twojego zarejestrowanego magazynów danych jako domyślnego magazynu danych dla obszaru roboczego:
```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>Przekazywanie i pobieranie danych
### <a name="upload"></a>Upload
Przekaż katalogu lub poszczególne pliki do magazynu danych przy użyciu zestawu SDK języka Python.

Aby przekazać katalogu do magazynu danych `ds`:
```Python
ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```
`target_path` Określa lokalizację w udziale plików (lub kontenera obiektów blob) do przekazania. Jego wartość domyślna to `None`, w którym to przypadku pobiera dane przekazywane do katalogu głównego. `overwrite=True` spowoduje zastąpienie istniejących danych w `target_path`.

Lub przekazać listę pojedynczych plików do magazynu danych przy użyciu magazynu danych `upload_files()` metody.

### <a name="download"></a>Do pobrania
Podobnie należy pobrać dane z magazynu danych, do lokalnego systemu plików.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```
`target_path` jest to lokalizacja katalogu lokalnego do pobierania danych. Aby określić ścieżkę do folderu w udziale plików (lub kontenera obiektów blob) do pobrania, należy podać tę ścieżkę, aby `prefix`. Jeśli `prefix` jest `None`, zostanie Pobierz pobrana zawartość udziału plików (lub kontenera obiektów blob).

## <a name="access-datastores-for-training"></a>Dostęp do magazynów danych, szkolenia
Magazyn danych są dostępne podczas szkolenia z systemem (na przykład dla danych szkoleniowych lub sprawdzania poprawności) zdalnego obliczeniowego elementu docelowego za pomocą zestawu SDK języka Python. 

Istnieją dwa sposoby obsługiwanych, aby udostępnić swoje magazynu danych w usłudze obliczeniowej zdalnego:
* **Instalacji**  
`ds.as_mount()`:, określając ten tryb instalacji, Magazyn danych będzie zostać zainstalowany dla Ciebie na zdalne zasoby obliczeniowe. 
* **Pobieranie lub przekazywanie**  
    * `ds.as_download(path_on_compute='your path on compute')` pobiera dane z usługi magazynu danych na zdalne zasoby obliczeniowe w lokalizacji określonej przez `path_on_compute`.
    * `ds.as_upload(path_on_compute='yourfilename'` przekazuje dane do magazynu danych.  Załóżmy, że skrypt szkolenia tworzy `foo.pkl` plik w bieżącym katalogu roboczym na zdalne zasoby obliczeniowe. Przekaż ten plik do usługi magazynu danych przy użyciu `ds.as_upload(path_on_compute='./foo.pkl')` po utworzeniu pliku skryptu. Plik jest przekazywany do katalogu głównego usługi magazynu danych.
    
Aby odwoływać się do określonego folderu lub pliku w swojej magazynu danych, należy użyć magazynu danych **`path`** funkcji. Na przykład, aby pobrać zawartość `./bar` katalogu z magazynu danych do lokalizacji docelowej obliczeń, użyj `ds.path('./bar').as_download()`.

Wszelkie `ds` lub `ds.path` obiektu jest rozpoznawana jako nazwę zmiennej środowiskowej formatu `"$AZUREML_DATAREFERENCE_XXXX"` których wartość reprezentuje ścieżkę instalacji/pobierania na zdalne zasoby obliczeniowe. Ścieżka magazynu danych w usłudze obliczeniowej zdalnego nie może być taka sama jak ścieżka wykonywania skryptu.

Aby uzyskać dostęp do usługi magazynu danych, podczas szkolenia, przekaż go do skryptu szkolenia jako argument wiersza polecenia za pomocą `script_params`:

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py')
```
`as_mount()` jest to domyślny tryb magazyn danych, dzięki czemu można także bezpośrednio po prostu przekazać `ds` do `'--data_dir'` argumentu.

Lub przekazać listę magazynów danych do konstruktora narzędzie do szacowania `inputs` parametru, aby zainstalować, lub skopiować do i z niej swoje magazyny danych:

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```
Powyższy kod wykonują następujące czynności:
* Pobierz całą zawartość w magazynie danych `ds1` do zdalnego obliczeń przed skrypt szkolenia `train.py` jest uruchamiany
* Pobierz folder `'./foo'` w magazynie danych `ds2` do zdalnego obliczeń przed `train.py` jest uruchamiany
* Przekaż plik `'./bar.pkl'` ze zdalnego obliczeniowego do magazynu danych `d3` po uruchomieniu skryptu

## <a name="next-steps"></a>Kolejne kroki
* [Uczenie modelu](how-to-train-ml-models.md)
