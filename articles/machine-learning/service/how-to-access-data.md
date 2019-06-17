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
ms.date: 05/24/2019
ms.custom: seodec18
ms.openlocfilehash: 93fc9a4e9e44bd7e8db3d49fe390ebe273c45ce9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66239047"
---
# <a name="access-data-from-your-datastores"></a>Dostęp do danych z usługi magazynów danych

 W usłudze Azure Machine Learning magazynów danych są zasobów obliczeniowych niezależnie od lokalizacji mechanizmy dostęp do magazynu bez konieczności wprowadzania zmian w kodzie źródłowym. Czy napisać kod szkolenia przepływał ścieżką jako parametr lub podaj magazyn danych bezpośrednio do szacowania przepływy pracy usługi Azure Machine Learning upewnij się, lokalizacjach magazynu danych są dostępne i udostępnione do kontekstu obliczeniowego.

Niniejszy instruktaż zawiera przykłady następujących zadań:
* [Wybierz magazyn danych](#access)
* [Pobieranie danych](#get)
* [Przekazywanie i pobieranie danych do magazynów danych](#up-and-down)
* [Dostęp do magazynu danych, podczas szkolenia](#train)

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z magazynów danych, należy najpierw [obszaru roboczego](concept-workspace.md).

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

 Każdy obszar roboczy został zarejestrowany, domyślny magazyn danych można użyć od razu.

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
                                           file_share_name='your file share name',
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

### <a name="upload"></a>Przekazanie

 Przekaż katalogu lub poszczególne pliki do magazynu danych przy użyciu zestawu SDK języka Python.

Aby przekazać katalogu do magazynu danych `ds`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

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

Po udostępnieniu usługi magazynu danych na szkolenie obliczeniowego elementu docelowego, dostępu podczas przebiegów szkoleniowych (na przykład, szkoleń lub sprawdzania poprawności danych), po prostu przekazując ścieżkę do niego jako parametr w skrypcie szkolenia.

W poniższej tabeli wymieniono [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) metody, które informują obliczeniowego elementu docelowego, jak korzystać z magazynu danych w czasie uruchomienia.

sposób|Metoda|Opis|
----|-----|--------
Instalacja| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Zainstaluj za pomocą magazynu danych na obliczeniowego elementu docelowego.
Do pobrania|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Służy do pobierania zawartości z magazynu danych do lokalizacji określonej przez `path_on_compute`. <br> W kontekście szkolenia, uruchom ten plik do pobrania ma miejsce przed uruchomieniem.
Przekazanie|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Służy do przekazywania pliku z lokalizacji określonej przez `path_on_compute` z magazynem danych. <br> W kontekście szkolenia, uruchom to przekazywanie odbywa się po ich zakończeniu.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Aby odwoływać się do określonego folderu lub pliku w swojej magazynu danych i udostępnić go w miejscu docelowym obliczeń, należy użyć magazynu danych [ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) funkcji.

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> Wszelkie `ds` lub `ds.path` obiektu jest rozpoznawana jako nazwę zmiennej środowiskowej formatu `"$AZUREML_DATAREFERENCE_XXXX"` których wartość reprezentuje ścieżkę instalacji/pobierania w usłudze obliczeniowej docelowego. Ścieżka magazynu danych w usłudze obliczeniowej docelowego nie może być taka sama jak ścieżka wykonywania skryptu szkolenia.

### <a name="training-compute-and-datastore-matrix"></a>Szkolenie macierzy zasobów obliczeniowych i magazynu danych

Następujące macierzy Wyświetla funkcje dostępu dostępnych danych dla celów obliczeń różnych szkolenia i scenariuszy magazynu danych. Dowiedz się więcej o [szkolenia celów obliczeń dla usługi Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Wystąpienia obliczeniowe|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Lokalny|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|ND         |ND                                                                         |
| Usługi Azure Machine Learning obliczeń |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|ND         |ND                                                                         |
| Maszyny wirtualne               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |ND         |ND                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |ND         |ND                                                                         |
| Transfer danych                  |[Uczenie Maszynowe&nbsp;potoków](concept-ml-pipelines.md)                                               |ND                                           |[Uczenie Maszynowe&nbsp;potoków](concept-ml-pipelines.md)            |[Uczenie Maszynowe&nbsp;potoków](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[Uczenie Maszynowe&nbsp;potoków](concept-ml-pipelines.md)                                              |ND                                           |[Uczenie Maszynowe&nbsp;potoków](concept-ml-pipelines.md)             |ND                                                                         |
| Azure Batch                    |[Uczenie Maszynowe&nbsp;potoków](concept-ml-pipelines.md)                                               |ND                                           |ND         |ND                                                                         |
| Azure DataLake Analytics       |ND                                           |ND                                           |[Uczenie Maszynowe&nbsp;potoków](concept-ml-pipelines.md)             |ND                                                                         |

> [!NOTE]
> Mogą istnieć scenariusze, w których wysoce iteracyjną, duże ilości danych procesy są uruchamiane szybciej przy użyciu [`as_download()`] zamiast [`as_mount()`]; może to być weryfikowane doświadczalnie.

### <a name="examples"></a>Przykłady 

Poniższe przykłady kodu są specyficzne dla [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klasy do uzyskiwania dostępu do usługi magazynu danych podczas szkolenia.

Ten kod tworzy narzędzie do szacowania za pomocą skryptu programu szkolenia `train.py`, z katalogu wskazanego źródła, przy użyciu parametrów zdefiniowanych w `script_params`, wszystkich szkoleń określonego obliczeniowego elementu docelowego.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Można również przekazać listę magazynów danych do konstruktora narzędzie do szacowania `inputs` parametru, aby zainstalować, lub skopiować do i z niej swoje magazyny danych. Ten przykład kodu:
* Pliki do pobrania całej zawartości w magazynie danych `ds1` do obliczeniowego elementu docelowego przed skrypt szkolenia `train.py` jest uruchamiany
* Folder pobierania `'./foo'` w magazynie danych `ds2` do obliczeniowego elementu docelowego przed `train.py` jest uruchamiany
* Przekazuje plik `'./bar.pkl'` z obliczeniowego elementu docelowego do magazynu danych `ds3` po uruchomieniu skryptu

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="next-steps"></a>Kolejne kroki

* [Uczenie modelu](how-to-train-ml-models.md)

* [Wdrażanie modelu](how-to-deploy-and-where.md)
