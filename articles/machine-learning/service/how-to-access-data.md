---
title: Użyj magazynów danych w usłudze Azure Machine Learning na dostęp do danych
description: Jak korzystać z magazynów danych w magazynie danych programu access w czasie szkolenia
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 615ab592c040eedf7d31e3a3036f558ea6c09740
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990811"
---
# <a name="how-to-access-data-during-training"></a>Jak uzyskać dostęp do danych podczas szkolenia
Magazyn danych w usłudze Azure Machine Learning services to Abstrakcja za pośrednictwem [usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction). Magazyn danych może odwoływać się albo [obiektów Blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) kontenera lub [udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) jako podstawowy magazyn. Magazynów danych pozwalają na łatwe dostępu i interakcji z magazynem danych podczas przepływów pracy usługi Azure Machine Learning za pomocą zestawu SDK języka Python lub interfejsu wiersza polecenia.

## <a name="create-a-datastore"></a>Utwórz magazyn danych
Aby można było korzystać z magazynów danych, należy najpierw [obszaru roboczego](concept-azure-machine-learning-architecture.md#workspace). Możesz utworzyć nowy obszar roboczy lub pobrać istniejącą grupę:

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Użyj domyślnego magazynu danych
Każdy obszar roboczy ma domyślny magazyn danych możesz zacząć używać natychmiast, co pozwoli zaoszczędzić pracy tworzenia i konfigurowania konta magazynu.

Aby uzyskać magazyn danych z domyślnego obszaru roboczego:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Rejestrowanie magazynu danych
Jeśli masz już istniejące usługi Azure Storage, należy zarejestrować go jako magazyn danych w obszarze roboczym usługi. Usługa Azure Machine Learning oferuje funkcje, które można zarejestrować kontenera obiektów Blob platformy Azure lub udziału plików platformy Azure jako magazyn danych. Wszystkie metody register znajdują się na `Datastore` klasy i mają następującą formę `register_azure_*`.

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
    print(name, ds.datastore_type)"
```

Dla wygody Jeśli chcesz ustawić jeden z Twojego zarejestrowanego magazynów danych jako domyślnego magazynu danych dla obszaru roboczego, możesz to zrobić w następujący sposób:
```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>Przekazywanie i pobieranie danych
### <a name="upload"></a>Upload
Możesz przekazać katalogu lub pojedyncze pliki z magazynem danych przy użyciu zestawu SDK języka Python.

Aby przekazać katalogu do magazynu danych `ds`:
```Python
ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```
`target_path` Określa lokalizację w udziale plików (lub kontenera obiektów blob) do przekazania. Jego wartość domyślna to `None`, w którym to przypadku pobiera dane przekazywane do katalogu głównego. `overwrite=True` spowoduje zastąpienie istniejących danych w `target_path`.

Możesz też przekazać listę pojedynczych plików z magazynem danych przy użyciu magazynu danych `upload_files()` metody.

### <a name="download"></a>Do pobrania
Podobnie można pobrać danych z magazynu danych do lokalnego systemu plików.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```
`target_path` jest to lokalizacja katalogu lokalnego do pobierania danych. Aby określić ścieżkę do folderu w udziale plików (lub kontenera obiektów blob) do pobrania, należy podać tę ścieżkę, aby `prefix`. Jeśli `prefix` jest `None`, zostanie Pobierz pobrana zawartość udziału plików (lub kontenera obiektów blob).

## <a name="access-datastores-for-training"></a>Dostęp do magazynów danych, szkolenia
Magazyn danych są dostępne podczas szkolenia z systemem (na przykład do szkoleń lub sprawdzania poprawności danych) zdalnego obliczeniowego elementu docelowego za pomocą zestawu SDK języka Python. 

Istnieją dwa sposoby obsługiwanych, aby udostępnić swoje magazynu danych w usłudze obliczeniowej zdalnego:
* **Instalacji**  
`ds.as_mount()`:, określając ten tryb instalacji, Magazyn danych będzie zostać zainstalowany dla Ciebie na zdalne zasoby obliczeniowe. 
* **Pobieranie lub przekazywanie**  
    * `ds.as_download(path_on_compute='your path on compute')`: w tym trybie pobierania danych będzie pobrać pobrane z usługi magazynu danych na zdalne zasoby obliczeniowe w lokalizacji określonej przez `path_on_compute`.
    * Z drugiej strony możesz również przekazać dane, które zostało utworzone z szkolenia uruchomić maksymalnie do magazynu danych. Na przykład, jeśli skrypt szkolenia tworzy `foo.pkl` plik w bieżącym katalogu roboczym na zdalne zasoby obliczeniowe, można określić dla niej zostać przekazane do usługi magazynu danych, po uruchomieniu skryptu: `ds.as_upload(path_on_compute='./foo.pkl')`. Można przekazać plik do katalogu głównego usługi magazynu danych.
    
Jeśli chcesz odwoływać się do określonego folderu lub pliku w swojej magazynu danych, możesz użyć magazynu danych **`path`** funkcji. Na przykład, jeśli usługi magazynu danych ma katalog za pomocą ścieżki względnej `./bar`i chcesz pobrać zawartość tego folderu do obliczeniowego elementu docelowego, możesz to zrobić w następujący sposób: `ds.path('./bar').as_download()`

Wszelkie `ds` lub `ds.path` obiektu jest rozpoznawana jako nazwę zmiennej środowiskowej formatu `"$AZUREML_DATAREFERENCE_XXXX"` których wartość reprezentuje ścieżkę instalacji/pobierania na zdalne zasoby obliczeniowe. Ścieżka magazynu danych w usłudze obliczeniowej zdalnego nie może być taka sama jak ścieżka wykonywania skryptu.

Aby uzyskać dostęp do usługi magazynu danych, podczas szkolenia, można je przekazać do skryptu szkolenia jako argument wiersza polecenia za pomocą `script_params`:

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

Alternatywnie, możesz przekazać listę magazynów danych, aby `inputs` parametr konstruktora narzędzie do szacowania, aby zainstalować, lub skopiować do i z niej swoje magazyny danych:

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
