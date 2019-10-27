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
ms.openlocfilehash: 07c23d3b93c85e3409814ab0eb635a51344e2ab2
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72929250"
---
# <a name="access-data-in-azure-storage-services"></a>Dostęp do danych w usługach Azure Storage

W tym artykule dowiesz się, jak łatwo uzyskać dostęp do danych w usługach Azure Storage za pośrednictwem Azure Machine Learning magazynów zasobów. Magazyny danych służą do przechowywania informacji o połączeniu, takich jak identyfikator subskrypcji i autoryzacja tokenu. Użycie magazynów danych umożliwia dostęp do magazynu bez konieczności używania w skryptach informacji o połączeniu z kodem. Magazyny danych można tworzyć na podstawie tych [rozwiązań usługi Azure Storage](#matrix). W przypadku nieobsługiwanych rozwiązań magazynu, aby zaoszczędzić koszt ruchu wychodzącego w trakcie eksperymentów z uczeniem maszynowym, zalecamy przeniesienie danych do naszych obsługiwanych rozwiązań usługi Azure Storage. [Dowiedz się, jak przenieść dane](#move). 

W tym przykładzie przedstawiono przykłady następujących zadań:
* [Rejestrowanie magazynów danych](#access)
* [Pobierz magazyny danych z obszaru roboczego](#get)
* [Przekazuj i pobieraj dane przy użyciu magazynów danych](#up-and-down)
* [Uzyskiwanie dostępu do danych podczas szkoleń](#train)
* [Przenoszenie danych na platformę Azure](#move)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

- Konto usługi Azure Storage z [kontenerem obiektów blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) lub [udziałem plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)lub dostęp do [strony docelowej obszaru roboczego (wersja zapoznawcza)](https://ml.azure.com/).

- Obszar roboczy usługi Azure Machine Learning. 
    - [Utwórz obszar roboczy Azure Machine Learning](how-to-manage-workspace.md) lub Użyj istniejącego z nich przy użyciu zestawu SDK języka Python.

        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore
        
        ws = Workspace.from_config()
        ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Tworzenie i rejestrowanie magazynów danych

Po zarejestrowaniu rozwiązania usługi Azure Storage jako magazynu danych automatycznie utworzysz ten magazyn danych w określonym obszarze roboczym. Magazyny danych można tworzyć i rejestrować w obszarze roboczym przy użyciu zestawu SDK języka Python lub strony docelowej obszaru roboczego.

### <a name="using-the-python-sdk"></a>Używanie zestawu Python SDK

Wszystkie metody Register znajdują się na klasie [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) i mają postać register_azure_ *.

Informacje potrzebne do wypełniania metody Register () można znaleźć za pośrednictwem [Azure Portal](https://ms.portal.azure.com). W lewym okienku wybierz pozycję **konta magazynu** , a następnie wybierz konto magazynu, które chcesz zarejestrować. Na stronie **Przegląd** znajdują się takie informacje, jak nazwa konta i kontener lub nazwa udziału plików. Aby uzyskać informacje o uwierzytelnianiu, takie jak klucz konta lub token sygnatury dostępu współdzielonego, przejdź do pozycji **klucze konta** w okienku **Ustawienia** po lewej stronie. 

W poniższych przykładach pokazano, jak zarejestrować kontener obiektów blob platformy Azure lub udział plików platformy Azure jako magazyn danych.

+ W przypadku **magazynu danych kontenera obiektów blob platformy Azure**Użyj [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)

    Poniższy kod tworzy i rejestruje magazyn danych `my_datastore`, w obszarze roboczym, `ws`. Ten magazyn danych uzyskuje dostęp do kontenera obiektów blob platformy Azure, `my_blob_container`na koncie usługi Azure Storage `my_storage_account` przy użyciu podanego klucza konta.

    ```Python
       datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                          datastore_name='my_datastore', 
                                                          container_name='my_blob_container',
                                                          account_name='my_storage_account', 
                                                          account_key='your storage account key',
                                                          create_if_not_exists=True)
    ```
    Jeśli Twoje konto magazynu znajduje się w sieci wirtualnej, obsługiwane jest tylko tworzenie magazynu danych obiektów blob platformy Azure. Ustaw parametr `grant_workspace_access` na `True`, aby udzielić obszaru roboczego dostępu do konta magazynu.

+ W przypadku **magazynu danych udziału plików platformy Azure**Użyj [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

    Poniższy kod tworzy i rejestruje magazyn danych `my_datastore`, w obszarze roboczym, `ws`. Ten magazyn danych uzyskuje dostęp do udziału plików platformy Azure, `my_file_share`na koncie usługi Azure Storage `my_storage_account` przy użyciu podanego klucza konta.

    ```Python
       datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                      datastore_name='my_datastore', 
                                                      file_share_name='my_file_share',
                                                      account_name='my_storage account', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
    ```

####  <a name="storage-guidance"></a>Wskazówki dotyczące magazynu

Zalecamy używanie kontenera obiektów blob platformy Azure. Magazyny w warstwie Standardowa i Premium są dostępne dla obiektów BLOB. Chociaż jest droższa, sugerujemy magazyn w warstwie Premium z powodu szybszych szybkości przepływności, które mogą zwiększyć szybkość przebiegów szkoleniowych, szczególnie w przypadku uczenia się z dużym zestawem danych. Informacje o kosztach konta magazynu można znaleźć na stronie [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) .

### <a name="using-the-workspace-landing-page"></a>Korzystanie ze strony docelowej obszaru roboczego 

Utwórz nowy magazyn danych w kilku krokach na stronie docelowej obszaru roboczego.

1. Zaloguj się do [strony docelowej obszaru roboczego](https://ml.azure.com/).
1. Wybierz pozycję **magazyny** danych w lewym okienku w obszarze **Zarządzaj**.
1. Wybierz pozycję **+ nowy magazyn**danych.
1. Zakończ pracę z nowym formularzem magazynu danych. Formularz jest inteligentnie aktualizowany w zależności od typu usługi Azure Storage i opcji typu uwierzytelniania.
  
Informacje potrzebne do wypełnienia formularza można znaleźć za pośrednictwem [Azure Portal](https://ms.portal.azure.com). W lewym okienku wybierz pozycję **konta magazynu** , a następnie wybierz konto magazynu, które chcesz zarejestrować. Na stronie **Przegląd** znajdują się takie informacje, jak nazwa konta i kontener lub nazwa udziału plików. W przypadku elementów uwierzytelniania, takich jak klucz konta lub token sygnatury dostępu współdzielonego, przejdź do pozycji **klucze konta** w okienku **Ustawienia** po lewej stronie.

W poniższym przykładzie pokazano, jak będzie wyglądać formularz do tworzenia magazynu danych obiektów blob platformy Azure. 
    
 ![Nowy magazyn danych](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Pobieranie magazynów danych z obszaru roboczego

Aby uzyskać określony magazyn danych zarejestrowany w bieżącym obszarze roboczym, użyj metody statycznej [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) w klasie magazynu danych:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Aby uzyskać listę magazynów danych zarejestrowanych w danym obszarze roboczym, można użyć właściwości [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) w obiekcie obszaru roboczego:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Podczas tworzenia obszaru roboczego kontener obiektów blob platformy Azure i udział plików platformy Azure są rejestrowane odpowiednio do obszaru roboczego o nazwie `workspaceblobstore` i `workspacefilestore`. Przechowują informacje o połączeniu kontenera obiektów blob oraz udział plików, który jest zainicjowany na koncie magazynu dołączonym do obszaru roboczego. `workspaceblobstore` jest ustawiony jako domyślny magazyn danych.

Aby uzyskać domyślny magazyn danych obszaru roboczego:

```Python
datastore = ws.get_default_datastore()
```

Aby zdefiniować inny domyślny magazyn danych dla bieżącego obszaru roboczego, użyj metody [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) w obiekcie obszaru roboczego:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Przekaż dane pobierania &
Metody [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) i [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) opisane w poniższych przykładach są specyficzne dla i działają identycznie dla klas [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) i [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

### <a name="upload"></a>Przekazywanie

 Przekaż katalog lub pojedyncze pliki do magazynu danych za pomocą zestawu SDK języka Python.

Aby przekazać katalog do magazynu danych `datastore`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path` parametr określa lokalizację w udziale plików (lub kontenerze obiektów BLOB) do przekazania. Wartość domyślna to `None`. w takim przypadku dane są przekazywane do katalogu głównego. Gdy `overwrite=True` istniejące dane w `target_path` zostanie nadpisany.

Lub Przekaż listę pojedynczych plików do magazynu danych za pomocą metody `upload_files()`.

### <a name="download"></a>Pobierz

Podobnie należy pobrać dane z magazynu danych do lokalnego systemu plików.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Parametr `target_path` jest lokalizacją katalogu lokalnego, do którego mają zostać pobrane dane. Aby określić ścieżkę do folderu w udziale plików (lub kontenerze obiektów BLOB) do pobrania, podaj tę ścieżkę do `prefix`. Jeśli `prefix` to `None`, zostanie pobrana cała zawartość udziału plików (lub kontenera obiektów BLOB).

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Uzyskiwanie dostępu do danych podczas szkoleń

> [!IMPORTANT]
> Korzystanie z [Azure Machine Learning zestawy danych (wersja zapoznawcza)](how-to-create-register-datasets.md) to nowy zalecany sposób uzyskiwania dostępu do danych w szkole. Zestawy danych udostępniają funkcje, które ładują dane tabelaryczne do Pandas lub Spark Dataframe oraz możliwość pobierania lub instalowania plików dowolnego formatu z obiektów blob platformy Azure, Azure File, Azure Data Lake Gen 1, Azure Data Lake Gen 2, Azure SQL i Azure PostgreSQL. Dowiedz się więcej [na temat uczenia się z zestawami danych](how-to-train-with-datasets.md).

W poniższej tabeli wymieniono metody, które poinformują element docelowy obliczeń, jak używać magazynów danych podczas uruchamiania. 

Możliwości|Metoda|Opis|
----|-----|--------
Instalacja| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Służy do instalowania magazynu danych w elemencie docelowym obliczeń.
Pobierz|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Użyj, aby pobrać zawartość magazynu danych do lokalizacji określonej przez `path_on_compute`. <br><br> To pobieranie jest wykonywane przed uruchomieniem.
Przekazywanie|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Służy do przekazywania pliku z lokalizacji określonej przez `path_on_compute` do magazynu danych. <br><br> To przekazywanie jest wykonywane po uruchomieniu.

Aby odwołać się do określonego folderu lub pliku w magazynie danych i udostępnić go w elemencie docelowym obliczeń, użyj metody [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) magazynu danych.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Każdy określony `datastore` lub obiekt `datastore.path` jest rozpoznawany jako nazwa zmiennej środowiskowej `"$AZUREML_DATAREFERENCE_XXXX"`, której wartość reprezentuje ścieżkę instalacji/pobierania w docelowym obliczeniu. Ścieżka magazynu danych w docelowym obliczeniach może nie być taka sama jak ścieżka wykonywania skryptu szkoleniowego.

### <a name="examples"></a>Przykłady 

Poniższe przykłady kodu są specyficzne dla klasy [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) do uzyskiwania dostępu do danych podczas szkoleń. 

`script_params` jest słownikiem zawierającym parametry do entry_script. Użyj go, aby przekazać magazyn danych i opisać, w jaki sposób dane są dostępne w obiekcie docelowym obliczeń. Dowiedz się więcej z naszego kompleksowego [samouczka](tutorial-train-models-with-aml.md).

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

Możesz również przekazać listę magazynów danych do konstruktora szacowania `inputs` parametru, aby zainstalować lub skopiować dane do/ze swoich magazynów danych. Ten przykład kodu:
* Pobiera całą zawartość `datastore1` do obiektu docelowego obliczeń przed uruchomieniem skryptu szkoleniowego `train.py`
* Pobiera folder `'./foo'` w `datastore2` do elementu docelowego obliczeń przed uruchomieniem `train.py`
* Przekazuje plik `'./bar.pkl'` z elementu docelowego obliczeń do `datastore3` po uruchomieniu skryptu

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Macierze obliczeniowe i magazyn danych

Magazyny danych obsługują obecnie przechowywanie informacji o połączeniu z usługami magazynu wymienionymi w poniższej macierzy. Ta macierz przedstawia dostępne funkcje dostępu do danych dla różnych obiektów docelowych obliczeń i scenariuszy magazynu danych. Dowiedz się więcej [na temat obiektów docelowych obliczeń dla Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Wystąpienia obliczeniowe|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Lokalna|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|ND         |ND                                                                         |
| Azure Machine Learning obliczeń |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ml&nbsp;potoki](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ml&nbsp;potoki](concept-ml-pipelines.md)|ND         |ND                                                                         |
| Maszyny wirtualne               |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |ND         |ND                                                                         |
| HDInsight                      |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |ND         |ND                                                                         |
| Transfer danych                  |[Potoki&nbsp;ML](concept-ml-pipelines.md)                                               |ND                                           |[Potoki&nbsp;ML](concept-ml-pipelines.md)            |[Potoki&nbsp;ML](concept-ml-pipelines.md)                                                                            |
| Usługa Databricks                     |[Potoki&nbsp;ML](concept-ml-pipelines.md)                                              |ND                                           |[Potoki&nbsp;ML](concept-ml-pipelines.md)             |ND                                                                         |
| Azure Batch                    |[Potoki&nbsp;ML](concept-ml-pipelines.md)                                               |ND                                           |ND         |ND                                                                         |
| Usługa Azure datalake Analytics       |ND                                           |ND                                           |[Potoki&nbsp;ML](concept-ml-pipelines.md)             |ND                                                                         |

> [!NOTE]
> Mogą istnieć scenariusze, w których wysoce iteracyjne i duże procesy przetwarzania danych są uruchamiane szybciej przy użyciu `as_download()` zamiast `as_mount()`; można to zweryfikować eksperymentowo.

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

W przypadku sytuacji, w których zestaw SDK nie zapewnia dostępu do magazynów danych, można utworzyć niestandardowy kod przy użyciu odpowiedniego zestawu Azure SDK, aby uzyskać dostęp do tego programu. Na przykład [zestaw SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python) jest biblioteką kliencką, za pomocą której można uzyskać dostęp do danych przechowywanych w obiektach Blob lub plikach.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Przenoszenie danych do obsługiwanych rozwiązań usługi Azure Storage

Usługa Azure Machine Learning obsługuje dostęp do danych z obiektów blob platformy Azure, plików platformy Azure, Azure Data Lake Gen 1, Azure Data Lake Gen 2, Azure SQL, Azure PostgreSQL. W przypadku nieobsługiwanego magazynu, aby zaoszczędzić koszt ruchu wychodzącego w trakcie eksperymentów z uczeniem maszynowym, zalecamy przeniesienie danych do naszych obsługiwanych rozwiązań usługi Azure Storage przy użyciu Azure Data Factory. Azure Data Factory zapewnia wydajny i odporny transfer danych z ponad 80 wstępnie skompilowanymi łącznikami, w tym usługami Azure Data Services, lokalnymi źródłami danych, Amazon S3 i RedShift oraz Google BigQuery — bez dodatkowych kosztów. [Postępuj zgodnie z przewodnikiem krok po kroku, aby przenieść dane przy użyciu Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Następne kroki

* [Uczenie modelu](how-to-train-ml-models.md)

* [Wdrażanie modelu](how-to-deploy-and-where.md)
