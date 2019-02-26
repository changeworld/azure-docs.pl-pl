---
title: Dostęp do danych w magazynów danych / obiekty BLOB na potrzeby szkolenia
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak dostęp do magazynu danych obiektów blob podczas szkolenia z usługi Azure Machine Learning za pomocą magazynów danych
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 98977f20af734e3adf927213b685f8c33b9383ea
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816882"
---
# <a name="access-data-from-your-datastores"></a>Dostęp do danych z usługi magazynów danych
W tym artykule dowiesz się różne sposoby dostępu i interakcję z danymi w przepływach pracy usługi Azure Machine Learning, za pomocą magazynów danych.

Magazyn danych w usłudze Azure Machine Learning to Abstrakcja za pośrednictwem [usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction). Magazyn danych może odwoływać się albo [obiektów Blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) kontenera lub [udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) jako podstawowy magazyn. 

Niniejszy instruktaż zawiera przykłady dla następujących zadań: 
* Utwórz magazyn danych
* [Przekazywanie i pobieranie danych do magazynów danych](#upload-and-download-data)
* [Dostęp do magazynu danych, szkolenia](#access-datastores-for-training)

## <a name="create-a-datastore"></a>Utwórz magazyn danych
Aby korzystać z magazynów danych, należy najpierw [obszaru roboczego](concept-azure-machine-learning-architecture.md#workspace). Rozpocznij od albo [tworzenia nowego obszaru roboczego](quickstart-create-workspace-with-python.md) lub pobierania istniejące:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Użyj domyślnego magazynu danych
Nie ma potrzeby tworzenia i konfigurowania konta magazynu.  Każdy obszar roboczy ma domyślne magazyn danych, którą można uruchomić od razu korzystać.

Aby uzyskać magazyn danych z domyślnego obszaru roboczego:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Rejestrowanie magazynu danych
Jeśli masz istniejące usługi Azure Storage, należy zarejestrować go jako magazyn danych w obszarze roboczym usługi. Jako magazyn danych, można zarejestrować kontenera obiektów Blob platformy Azure lub udziału plików platformy Azure. Wszystkie metody register znajdują się na [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) klasy i mają następującą formę `register_azure_*`.

#### <a name="azure-blob-container-datastore"></a>Kontener obiektów Blob platformy Azure w magazynie danych
Aby zarejestrować magazyn danych w kontenerze obiektów Blob platformy Azure, użyj [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-:)

```Python
ds = Datastore.register_azure_blob_container(workspace=ws, 
                                             datastore_name='your datastore name', 
                                             container_name='your azure blob container name',
                                             account_name='your storage account name', 
                                             account_key='your storage account key',
                                             create_if_not_exists=True)
```

#### <a name="azure-file-share-datastore"></a>Magazyn danych z udziału plików platformy Azure
Aby zarejestrować magazyn danych z udziału plików platformy Azure, użyj [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)

```Python
ds = Datastore.register_azure_file_share(workspace=ws, 
                                         datastore_name='your datastore name', 
                                         container_name='your file share name',
                                         account_name='your storage account name', 
                                         account_key='your storage account key',
                                         create_if_not_exists=True)
```

### <a name="get-an-existing-datastore"></a>Pobieranie istniejącego magazynu danych
Aby wykonać zapytanie dla już zarejestrowany magazyn danych według nazwy:

```Python
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Możesz również uzyskać wszystkich magazynów danych dla obszaru roboczego:

```Python
datastores = ws.datastores
for name, ds in datastores.items():
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
    * `ds.as_mount()`, określając ten tryb instalacji, Magazyn danych pobiera zainstalowanych dla Ciebie na zdalne zasoby obliczeniowe. 

* **Pobieranie lub przekazywanie**  
    * `ds.as_download(path_on_compute='your path on compute')` pobiera dane z usługi magazynu danych na zdalne zasoby obliczeniowe w lokalizacji określonej przez `path_on_compute`.

    * `ds.as_upload(path_on_compute='yourfilename'` przekazuje dane do katalogu głównego usługi magazynu danych z lokalizacji określonej przez `path_on_compute`
    
Aby odwoływać się do określonego folderu lub pliku w swojej magazynu danych, należy użyć magazynu danych **`path()`** funkcji.

```Python
#download the contents of the `./bar` directory from the datastore 
ds.path('./bar').as_download()
```
Wszelkie `ds` lub `ds.path` obiektu jest rozpoznawana jako nazwę zmiennej środowiskowej formatu `"$AZUREML_DATAREFERENCE_XXXX"` których wartość reprezentuje ścieżkę instalacji/pobierania na zdalne zasoby obliczeniowe. Ścieżka magazynu danych w usłudze obliczeniowej zdalnego nie może być taka sama jak ścieżka wykonywania skryptu.

Aby uzyskać dostęp do usługi magazynu danych, podczas szkolenia, przekaż go do skryptu szkolenia jako argument wiersza polecenia za pomocą `script_params` z [narzędzie do szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klasy:

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
`as_mount()` jest to domyślny tryb magazyn danych, dzięki czemu można również bezpośrednio przekazać `ds` do `'--data_dir'` argumentu.

Lub przekazać listę magazynów danych do konstruktora narzędzie do szacowania `inputs` parametru, aby zainstalować, lub skopiować do i z niej swoje magazyny danych

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
* [Wdrażanie modelu](how-to-deploy-and-where.md)

