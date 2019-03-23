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
ms.date: 02/25/2019
ms.custom: seodec18
ms.openlocfilehash: c171e35c6542febffc666ad5abfab50e093bb698
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359283"
---
# <a name="access-data-from-your-datastores"></a>Dostęp do danych z usługi magazynów danych

Magazynów danych umożliwiają interakcję i uzyskiwać dostęp do danych, czy używasz kodu lokalnie, w klastrze obliczeniowym, lub na maszynie wirtualnej. W tym artykule, Dowiedz się, że przepływy pracy usługi Azure Machine Learning, upewnij się, Twoje magazynów danych, które są dostępne i udostępnione do kontekstu obliczeniowego.

Niniejszy instruktaż zawiera przykłady dla następujących zadań:
* [Wybierz magazyn danych](#access)
* [Pobieranie danych](#get)
* [Przekazywanie i pobieranie danych do magazynów danych](#up-and-down)
* [Dostęp do magazynu danych, podczas szkolenia](#train)

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z magazynów danych, musisz mieć [obszaru roboczego](concept-azure-machine-learning-architecture.md#workspace) pierwszy. 

Rozpocznij od albo [tworzenia nowego obszaru roboczego](setup-create-workspace.md#sdk) lub pobierania istniejące:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>Wybierz magazyn danych

Możesz użyć domyślnego magazynu danych lub skorzystaj z własnych.

### <a name="use-the-default-datastore-in-your-workspace"></a>Użyj domyślnego magazynu danych w obszarze roboczym

Nie ma potrzeby tworzenia i konfigurowania konta magazynu, ponieważ każdy obszar roboczy ma domyślny magazyn danych. Można go używać, Magazyn danych razu postaci, w jakiej jest już zarejestrowany w obszarze roboczym. 

Aby uzyskać magazyn danych z domyślnego obszaru roboczego:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Zarejestruj własny magazyn danych z obszarem roboczym
Jeśli masz istniejące usługi Azure Storage, należy zarejestrować go jako magazyn danych w obszarze roboczym usługi.   Wszystkie metody register znajdują się na [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) klasy i mieć register_azure_ formularza *. 

W poniższych przykładach pokazano można zarejestrować kontenera obiektów Blob platformy Azure lub udziału plików platformy Azure jako magazynu danych.

+ Aby uzyskać **Datastore kontenera obiektów Blob platformy Azure**, użyj [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ Aby uzyskać **magazyn danych z udziału plików platformy Azure**, użyj [ `register_azure_file_share()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Na przykład: 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           container_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>Znajdź & zdefiniować magazynów danych

Aby uzyskać określonej zarejestrowana w bieżącym obszarze roboczym magazyn danych, użyj [ `get()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) :

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Aby uzyskać listę wszystkich magazynów danych w danym obszarze roboczym, należy użyć następującego kodu:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Aby zdefiniować różne domyślne magazyn danych dla bieżącego obszaru roboczego, należy użyć [ `set_default_datastore()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-):

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Przekazywanie i pobieranie danych
[ `upload()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) i [ `download()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) metod opisanych w poniższych przykładach są specyficzne dla i działać tak samo dla [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) i [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) klasy.

### <a name="upload"></a>Upload

 Przekaż katalogu lub poszczególne pliki do magazynu danych przy użyciu zestawu SDK języka Python.

Aby przekazać katalogu do magazynu danych `ds`:

```Python
import azureml.data
from azureml.data import AzureFileDatastore, AzureBlobDatastore

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

<a name="train"></a>
## <a name="access-datastores-during-training"></a>Dostęp do magazynów danych w trakcie szkolenia

Po udostępnieniu usługi magazynu danych na zdalne zasoby obliczeniowe, możesz do niego dostęp podczas przebiegów szkoleniowych (na przykład, szkoleń lub sprawdzania poprawności danych), po prostu przekazując ścieżkę do niego jako parametr w skrypcie szkolenia.

W poniższej tabeli wymieniono typowe [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) metody, które udostępnia magazynów danych na zdalne zasoby obliczeniowe.

# #

sposób|Metoda|Opis
----|-----|--------
Instalacja| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Użyj, aby zainstalować Magazyn danych w usłudze obliczeniowej zdalnego. Domyślny tryb dla magazynów danych.
Do pobrania|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Użyj, aby pobrać dane z lokalizacji określonej przez `path_on_compute` na usługi magazynu danych na zdalne zasoby obliczeniowe.
Upload|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Służy do przekazywania danych do katalogu głównego usługi magazynu danych z lokalizacji określonej przez `path_on_compute`.

```Python
import azureml.data
from azureml.data import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Aby odwoływać się do określonego folderu lub pliku w swojej magazynu danych, należy użyć magazynu danych [ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) funkcji.

```Python
#download the contents of the `./bar` directory from the datastore to the remote compute
ds.path('./bar').as_download()
```



> [!NOTE]
> Wszelkie `ds` lub `ds.path` obiektu jest rozpoznawana jako nazwę zmiennej środowiskowej formatu `"$AZUREML_DATAREFERENCE_XXXX"` których wartość reprezentuje ścieżkę instalacji/pobierania na zdalne zasoby obliczeniowe. Ścieżka magazynu danych w usłudze obliczeniowej zdalnego nie może być taka sama jak ścieżka wykonywania skryptu szkolenia.

### <a name="examples"></a>Przykłady 

Następujące pokazują przykłady specyficzne dla [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klasy do uzyskiwania dostępu do usługi magazynu danych podczas szkolenia.


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

Ponieważ `as_mount()` jest to domyślny tryb dla magazynu danych, można także bezpośrednio przekazać `ds` do `'--data_dir'` argumentu.

Lub przekazać listę magazynów danych do konstruktora narzędzie do szacowania `inputs` parametru, aby zainstalować, lub skopiować do i z niej swoje magazyny danych. Ten przykład kodu:
* Pliki do pobrania całej zawartości w magazynie danych `ds1` do zdalnego obliczeń przed skrypt szkolenia `train.py` jest uruchamiany
* Folder pobierania `'./foo'` w magazynie danych `ds2` do zdalnego obliczeń przed `train.py` jest uruchamiany
* Przekazuje plik `'./bar.pkl'` ze zdalnego obliczeniowego do magazynu danych `ds3` po uruchomieniu skryptu

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```


## <a name="next-steps"></a>Kolejne kroki

* [Uczenie modelu](how-to-train-ml-models.md)

* [Wdrażanie modelu](how-to-deploy-and-where.md)
