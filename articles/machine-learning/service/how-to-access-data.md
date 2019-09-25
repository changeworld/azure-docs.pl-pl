---
title: Dostęp do danych w usługach Azure Storage
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać magazynów danych do uzyskiwania dostępu do usług Azure Storage podczas uczenia się z Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 08/2/2019
ms.custom: seodec18
ms.openlocfilehash: ecd1212385473e33d05f38f67db5708bff060daa
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218242"
---
# <a name="access-data-in-azure-storage-services"></a>Dostęp do danych w usługach Azure Storage

 W tym artykule dowiesz się, jak łatwo uzyskać dostęp do danych w usługach Azure Storage za pośrednictwem Azure Machine Learning magazynów zasobów. Magazyny danych służą do przechowywania informacji o połączeniu, takich jak identyfikator subskrypcji i autoryzacja tokenów, w celu uzyskania dostępu do magazynu bez konieczności podawania twardych kodów informacji w skryptach.

W tym przykładzie przedstawiono przykłady następujących zadań:
* [Rejestrowanie magazynów danych](#access)
* [Pobierz magazyny danych z obszaru roboczego](#get)
* [Przekazuj i pobieraj dane przy użyciu magazynów danych](#up-and-down)
* [Uzyskiwanie dostępu do danych podczas szkoleń](#train)

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z magazynów danych, należy najpierw [obszaru roboczego](concept-workspace.md).

Rozpocznij od albo [tworzenia nowego obszaru roboczego](how-to-manage-workspace.md) lub pobierania istniejące:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="register-datastores"></a>Rejestrowanie magazynów danych

Wszystkie metody Register znajdują się na [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) klasie i mają postać register_azure_ *.

W poniższych przykładach pokazano, jak zarejestrować kontener obiektów blob platformy Azure lub udział plików platformy Azure jako magazyn danych.

+ W przypadku **magazynu danych kontenera obiektów blob platformy Azure**Użyj[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                      datastore_name='your datastore name', 
                                                      container_name='your azure blob container name',
                                                      account_name='your storage account name', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
  ```

+ W przypadku **magazynu danych udziału plików platformy Azure**Użyj [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Na przykład: 
  ```Python
  datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                  datastore_name='your datastore name', 
                                                  file_share_name='your file share name',
                                                  account_name='your storage account name', 
                                                  account_key='your storage account key',
                                                  create_if_not_exists=True)
  ```

####  <a name="storage-guidance"></a>Wskazówki dotyczące magazynu

Zalecamy używanie kontenera obiektów blob platformy Azure. Magazyny w warstwie Standardowa i Premium są dostępne dla obiektów BLOB. Chociaż jest droższa, sugerujemy magazyn w warstwie Premium z powodu szybszych szybkości przepływności, które mogą zwiększyć szybkość przebiegów szkoleniowych, szczególnie w przypadku uczenia się z dużym zestawem danych. Informacje o kosztach konta magazynu można znaleźć na stronie [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) .

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Pobieranie magazynów danych z obszaru roboczego

Aby uzyskać określony magazyn danych zarejestrowany w bieżącym obszarze roboczym, użyj [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) metody statycznej w klasie magazynu danych:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Aby uzyskać listę magazynów danych zarejestrowanych w danym obszarze roboczym, możesz użyć `datastores` właściwości w obiekcie obszaru roboczego:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Podczas tworzenia obszaru roboczego kontener obiektów blob platformy Azure i udział plików platformy Azure są rejestrowane w obszarze roboczym o `workspaceblobstore` nazwie `workspacefilestore` i odpowiednio. Przechowują informacje o połączeniu kontenera obiektów blob oraz udział plików, który jest zainicjowany na koncie magazynu dołączonym do obszaru roboczego. `workspaceblobstore` Jest ustawiony jako domyślny magazyn danych.

Aby uzyskać magazyn danych z domyślnego obszaru roboczego:

```Python
datastore = ws.get_default_datastore()
```

Aby zdefiniować inny domyślny magazyn danych dla bieżącego obszaru roboczego, użyj [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) metody dla obiektu obszaru roboczego:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Przekaż dane pobierania &
Metody [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) i [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) opisane w poniższych przykładach są specyficzne dla i działają identycznie dla klas [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) i [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

### <a name="upload"></a>Upload

 Przekaż katalogu lub poszczególne pliki do magazynu danych przy użyciu zestawu SDK języka Python.

Aby przekazać katalogu do magazynu danych `datastore`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path` Parametr określa lokalizację w udziale plików (lub kontenerze obiektów BLOB) do przekazania. Jego wartość domyślna to `None`, w którym to przypadku pobiera dane przekazywane do katalogu głównego. Gdy `overwrite=True` istniejące`target_path` dane są zastępowane.

Lub Przekaż listę pojedynczych plików do magazynu danych za pomocą `upload_files()` metody.

### <a name="download"></a>Do pobrania

Podobnie należy pobrać dane z magazynu danych, do lokalnego systemu plików.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path` Parametr jest lokalizacją katalogu lokalnego, do którego mają zostać pobrane dane. Aby określić ścieżkę do folderu w udziale plików (lub kontenera obiektów blob) do pobrania, należy podać tę ścieżkę, aby `prefix`. Jeśli `prefix` jest `None`, zostanie Pobierz pobrana zawartość udziału plików (lub kontenera obiektów blob).

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Uzyskiwanie dostępu do danych podczas szkoleń

> [!IMPORTANT]
> Korzystanie z [Azure Machine Learning zestawy danych (wersja zapoznawcza)](how-to-create-register-datasets.md) to nowy zalecany sposób uzyskiwania dostępu do danych w szkole. Zestawy danych udostępniają funkcje, które ładują dane tabelaryczne do Pandas lub Spark Dataframe oraz możliwość pobierania lub instalowania plików dowolnego formatu z obiektów blob platformy Azure, Azure File, Azure Data Lake Gen 1, Azure Data Lake Gen 2, Azure SQL i Azure PostgreSQL. Dowiedz się więcej [na temat uczenia się z zestawami danych](how-to-train-with-datasets.md).

W poniższej tabeli wymieniono metody, które poinformują element docelowy obliczeń, jak używać magazynów danych podczas uruchamiania. 

Możliwości|Metoda|Opis|
----|-----|--------
Zainstaluj| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Służy do instalowania magazynu danych w elemencie docelowym obliczeń.
Do pobrania|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Użyj, aby pobrać zawartość magazynu danych do lokalizacji określonej przez `path_on_compute`. <br> To pobieranie jest wykonywane przed uruchomieniem.
Upload|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Służy do przekazywania pliku z lokalizacji określonej przez `path_on_compute` magazyn danych. <br> To przekazywanie jest wykonywane po uruchomieniu.

Aby odwołać się do określonego folderu lub pliku w magazynie danych i udostępnić go w elemencie docelowym obliczeń, użyj metody magazynu [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) danych.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Każdy `datastore` `"$AZUREML_DATAREFERENCE_XXXX"`lub `datastore.path` obiekt jest rozpoznawany jako nazwa zmiennej środowiskowej w formacie, którego wartość reprezentuje ścieżkę instalacji/pobierania w docelowym obliczeniu. Ścieżka magazynu danych w docelowym obliczeniach może nie być taka sama jak ścieżka wykonywania skryptu szkoleniowego.

### <a name="examples"></a>Przykłady 

Poniższe przykłady kodu są specyficzne [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) dla klasy do uzyskiwania dostępu do danych podczas szkoleń. 

`script_params`jest słownikiem zawierającym parametry do entry_script. Można go używać do przekazywania danych i określania sposobu, w jaki dane powinny być udostępniane w celu obliczenia. Dowiedz się więcej z naszego kompleksowego [samouczka](tutorial-train-models-with-aml.md).

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Możesz również przekazać listę magazynów danych do parametru konstruktora `inputs` szacowania, aby zainstalować lub skopiować dane do/z magazynów elementów. Ten przykład kodu:
* Pobiera całą zawartość `datastore1` do obiektu docelowego obliczeń przed uruchomieniem skryptu `train.py` szkoleniowego
* Pobiera folder `'./foo'` `datastore2` do elementu docelowego obliczeń przed `train.py` uruchomieniem
* Przekazuje plik `'./bar.pkl'` z elementu docelowego obliczeń `datastore3` do po uruchomieniu skryptu

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```

### <a name="compute-and-datastore-matrix"></a>Macierze obliczeniowe i magazyn danych

Magazyny danych obsługują obecnie przechowywanie informacji o połączeniu z usługami magazynu wymienionymi w poniższej macierzy. Ta macierz przedstawia dostępne funkcje dostępu do danych dla różnych obiektów docelowych obliczeń i scenariuszy magazynu danych. Dowiedz się więcej [na temat obiektów docelowych obliczeń dla Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Wystąpienia obliczeniowe|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Lokalny|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|ND         |ND                                                                         |
| Usługi Azure Machine Learning obliczeń |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|ND         |ND                                                                         |
| Maszyny wirtualne               |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |ND         |ND                                                                         |
| HDInsight                      |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |ND         |ND                                                                         |
| Transfer danych                  |[Potoki ml&nbsp;](concept-ml-pipelines.md)                                               |ND                                           |[Potoki ml&nbsp;](concept-ml-pipelines.md)            |[Potoki ml&nbsp;](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[Potoki ml&nbsp;](concept-ml-pipelines.md)                                              |ND                                           |[Potoki ml&nbsp;](concept-ml-pipelines.md)             |ND                                                                         |
| Azure Batch                    |[Potoki ml&nbsp;](concept-ml-pipelines.md)                                               |ND                                           |ND         |ND                                                                         |
| Usługa Azure datalake Analytics       |ND                                           |ND                                           |[Potoki ml&nbsp;](concept-ml-pipelines.md)             |ND                                                                         |

> [!NOTE]
> Mogą istnieć scenariusze, w których wysoce iteracyjne i duże procesy danych są uruchamiane `as_download()` szybciej przy `as_mount()`użyciu zamiast programu; może to być sprawdzona eksperymentowo.

### <a name="accessing-source-code-during-training"></a>Uzyskiwanie dostępu do kodu źródłowego podczas szkolenia

Usługa Azure Blob Storage ma większe szybkości przepływności niż udział plików platformy Azure i będzie skalowana do dużej liczby zadań uruchomionych równolegle. Z tego powodu zalecamy skonfigurowanie przebiegów do korzystania z usługi BLOB Storage na potrzeby przesyłania plików kodu źródłowego.

Poniższy przykład kodu określa w konfiguracji uruchamiania, która magazyn danych obiektów BLOB ma być używana do transferów kodu źródłowego.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Uzyskiwanie dostępu do danych podczas oceniania

Azure Machine Learning oferuje kilka sposobów na korzystanie z modeli do oceniania. Niektóre z tych metod nie zapewniają dostępu do magazynów danych. Skorzystaj z poniższej tabeli, aby zrozumieć, które metody umożliwiają dostęp do magazynów danych podczas oceniania:

| Metoda | Dostęp do magazynu danych | Opis |
| ----- | :-----: | ----- |
| [Prognoza wsadowa](how-to-run-batch-predictions.md) | ✔ | Asynchroniczne wykonywanie prognoz dotyczących dużych ilości danych. |
| [Usługa sieci Web](how-to-deploy-and-where.md) | &nbsp; | Wdróż modele jako usługę sieci Web. |
| [Moduł IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Wdróż modele do IoT Edge urządzeń. |

W sytuacjach, w których zestaw SDK nie zapewnia dostępu do magazynów danych, można utworzyć niestandardowy kod przy użyciu odpowiedniego zestawu Azure SDK, aby uzyskać dostęp do tego programu. Na przykład do uzyskiwania dostępu do danych przechowywanych w obiektach Blob za pomocą [zestawu SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python) .


## <a name="next-steps"></a>Następne kroki

* [Uczenie modelu](how-to-train-ml-models.md)

* [Wdrażanie modelu](how-to-deploy-and-where.md)
