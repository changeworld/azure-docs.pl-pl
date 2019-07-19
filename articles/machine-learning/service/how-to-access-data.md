---
title: Dostęp do danych w magazynach/obiektach Blob w celu szkolenia
titleSuffix: Azure Machine Learning service
description: Dowiedz się, w jaki sposób używać magazynów danych w celu uzyskiwania dostępu do usługi BLOB Data Storage podczas uczenia się z usługą Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 05/24/2019
ms.custom: seodec18
ms.openlocfilehash: 97a4bc20394553b97211763cedaa76c3711306f2
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68319322"
---
# <a name="access-data-from-your-datastores"></a>Dostęp do danych z Twoich magazynów

 W ramach usługi Azure Machine Learning magazyny danych są mechanizmami obliczeniowymi niezależnymi od lokalizacji, aby uzyskać dostęp do magazynu bez konieczności wprowadzania zmian w kodzie źródłowym. Bez względu na to, czy piszesz kod szkoleniowy, aby przyjąć ścieżkę jako parametr, czy udostępnić magazyn danych bezpośrednio do szacowania, Azure Machine Learning przepływy pracy upewnij się, że lokalizacje magazynu danych są dostępne i udostępnione dla kontekstu obliczeniowego.

W tym przykładzie przedstawiono przykłady następujących zadań:
* [Wybierz magazyn danych](#access)
* [Pobieranie danych](#get)
* [Przekazuj i pobieraj dane do magazynów danych](#up-and-down)
* [Dostęp do magazynu danych podczas szkolenia](#train)

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

Możesz użyć domyślnego magazynu danych lub wprowadzić własny.

### <a name="use-the-default-datastore-in-your-workspace"></a>Używanie domyślnego magazynu danych w obszarze roboczym

 Każdy obszar roboczy ma zarejestrowany, domyślny magazyn danych, którego można użyć od razu.

Aby uzyskać magazyn danych z domyślnego obszaru roboczego:

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Rejestrowanie własnego magazynu danych za pomocą obszaru roboczego

Jeśli masz istniejące usługi Azure Storage, należy zarejestrować go jako magazyn danych w obszarze roboczym usługi. 

<a name="store"></a>

####  <a name="storage-guidance"></a>Wskazówki dotyczące magazynu

Zalecamy użycie magazynu obiektów blob i magazynów danych BLOB. Magazyny w warstwie Standardowa i Premium są dostępne dla obiektów BLOB. Chociaż jest droższa, sugerujemy magazyn w warstwie Premium z powodu szybszych szybkości przepływności, które mogą zwiększyć szybkość przebiegów szkoleniowych, szczególnie w przypadku uczenia się z dużym zestawem danych. Informacje o kosztach konta magazynu można znaleźć na stronie [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) .

>[!NOTE]
> Usługa Azure Machine Learning obsługuje inne typy magazynów danych, które mogą być przydatne w określonych scenariuszach. Na przykład jeśli chcesz nauczyć się przy użyciu danych przechowywanych w bazie danych, możesz użyć AzureSQLDatabaseDatastore lub AzurePostgreSqlDatastore. Zapoznaj się z [tą tabelą](#matrix) dla dostępnych typów magazynu danych.

#### <a name="register-your-datastore"></a>Rejestrowanie magazynu danych
Wszystkie metody Register znajdują się na [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) klasie i mają postać register_azure_ *.

W poniższych przykładach pokazano, jak zarejestrować kontener obiektów blob platformy Azure lub udział plików platformy Azure jako magazyn danych.

+ W przypadku **magazynu danych kontenera obiektów blob platformy Azure**Użyj[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ W przypadku **magazynu danych udziału plików platformy Azure**Użyj [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Na przykład: 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>Znajdowanie & Definiowanie magazynów danych

Aby uzyskać określony magazyn danych zarejestrowany w bieżącym obszarze roboczym, użyj [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) :

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Aby uzyskać listę wszystkich magazynów danych w danym obszarze roboczym, użyj tego kodu:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Aby zdefiniować inny domyślny magazyn danych dla bieżącego obszaru roboczego, użyj [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-):

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Przekaż dane pobierania &
Metody [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) i [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) opisane w poniższych przykładach są specyficzne dla i działają identycznie dla klas [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) i [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

### <a name="upload"></a>Upload

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
## <a name="access-datastores-during-training"></a>Dostęp do magazynów danych podczas szkoleń

Po udostępnieniu magazynu danych w miejscu docelowym obliczeń szkoleniowych możesz uzyskać do niego dostęp podczas przebiegów szkoleniowych (na przykład dane szkoleniowe lub walidacji), po prostu przekazując ścieżkę do niej jako parametr w skrypcie szkoleniowym.

W poniższej tabeli wymieniono [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) metody, które poinformują element docelowy obliczeń, jak używać magazynu danych podczas uruchamiania.

Możliwości|Metoda|Opis|
----|-----|--------
Instalacja| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Służy do instalowania magazynu danych w elemencie docelowym obliczeń.
Do pobrania|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Użyj, aby pobrać zawartość magazynu danych do lokalizacji określonej przez `path_on_compute`. <br> W przypadku kontekstu uruchomienia szkolenia ten proces jest wykonywany przed uruchomieniem.
Upload|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Służy do przekazywania pliku z lokalizacji określonej przez `path_on_compute` magazyn danych. <br> W przypadku kontekstu uruchomienia szkolenia to przekazywanie odbywa się po uruchomieniu.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Aby odwołać się do określonego folderu lub pliku w magazynie danych i udostępnić go w elemencie docelowym obliczeń, użyj funkcji magazynu danych [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) .

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> Każdy `ds` `"$AZUREML_DATAREFERENCE_XXXX"` lub `ds.path` obiekt jest rozpoznawany jako nazwa zmiennej środowiskowej w formacie, którego wartość reprezentuje ścieżkę instalacji/pobierania w docelowym obliczeniu. Ścieżka magazynu danych w docelowym obliczeniach może nie być taka sama jak ścieżka wykonywania skryptu szkoleniowego.

<a name="matrix"></a>
### <a name="training-compute-and-datastore-matrix"></a>Szkolenie dotyczące obliczeniowych i magazynów danych

W poniższej macierzy przedstawiono dostępne funkcje dostępu do danych dla różnych obiektów docelowych obliczeń szkoleniowych i scenariuszy magazynu danych. Dowiedz się więcej na temat [obiektów docelowych obliczeń szkoleniowych dla Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

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

### <a name="examples"></a>Przykłady 

Poniższe przykłady kodu są specyficzne [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) dla klasy umożliwiającej dostęp do magazynu danych podczas szkolenia.

Ten kod tworzy szacowania przy użyciu skryptu `train.py`szkoleniowego, ze wskazanego katalogu źródłowego przy użyciu parametrów zdefiniowanych w `script_params`, wszystkie w określonym miejscu docelowym obliczeń szkoleniowych.

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

Możesz również przekazać listę magazynów danych do parametru konstruktora `inputs` szacowania, aby zainstalować lub skopiować do/z magazynów danych. Ten przykład kodu:
* Pobiera całą zawartość ze sklepu datastore `ds1` do obiektu docelowego obliczeń przed uruchomieniem skryptu `train.py` szkoleniowego
* Pobiera folder `'./foo'` z magazynu `ds2` danych do elementu docelowego obliczeń przed `train.py` uruchomieniem
* Przekazuje plik `'./bar.pkl'` z miejsca docelowego obliczeń do magazynu `ds3` danych po uruchomieniu skryptu

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="access-datastores-during-for-scoring"></a>Dostęp do magazynów danych podczas oceniania

Usługa Azure Machine Learning oferuje kilka sposobów na korzystanie z modeli do oceniania. Niektóre z tych metod nie zapewniają dostępu do magazynów danych. Skorzystaj z poniższej tabeli, aby zrozumieć, które metody umożliwiają dostęp do magazynów danych podczas oceniania:

| Metoda | Dostęp do magazynu danych | Opis |
| ----- | :-----: | ----- |
| [Prognoza wsadowa](how-to-run-batch-predictions.md) | ✔ | Asynchroniczne wykonywanie prognoz dotyczących dużych ilości danych. |
| [Usługa sieci Web](how-to-deploy-and-where.md) | &nbsp; | Wdróż modele jako usługę sieci Web. |
| [Moduł IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Wdróż modele do IoT Edge urządzeń. |

W sytuacjach, w których zestaw SDK nie zapewnia dostępu do magazynów danych, można utworzyć niestandardowy kod przy użyciu odpowiedniego zestawu Azure SDK, aby uzyskać dostęp do tego programu. Na przykład do uzyskiwania dostępu do danych przechowywanych w obiektach Blob za pomocą [zestawu SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python) .

## <a name="next-steps"></a>Następne kroki

* [Uczenie modelu](how-to-train-ml-models.md)

* [Wdrażanie modelu](how-to-deploy-and-where.md)
