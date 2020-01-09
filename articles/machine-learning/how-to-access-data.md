---
title: Dostęp do danych w usługach Azure Storage
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać magazynów danych do bezpiecznego łączenia się z usługami Azure Storage podczas uczenia się z Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ylxiong
author: YLXiong1125
ms.reviewer: nibaccam
ms.date: 12/10/2019
ms.custom: seodec18
ms.openlocfilehash: ac6ef6341013ca13d5a9f27be8897365c1c2155d
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540947"
---
# <a name="access-data-in-azure-storage-services"></a>Dostęp do danych w usługach Azure Storage
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak łatwo uzyskać dostęp do danych w usługach Azure Storage za pośrednictwem Azure Machine Learning magazynów zasobów. Magazyny danych służą do przechowywania informacji o połączeniu, takich jak identyfikator subskrypcji i autoryzacja tokenu. Gdy używasz magazynów danych, możesz uzyskać dostęp do magazynu bez konieczności używania w skryptach informacji o połączeniu z kodem. 

Magazyny danych można tworzyć na podstawie [tych rozwiązań usługi Azure Storage](#matrix). W przypadku nieobsługiwanych rozwiązań magazynu i zapisania kosztów ruchu wychodzącego w trakcie eksperymentów z uczeniem maszynowym zalecamy [przeniesienie danych](#move) do obsługiwanych rozwiązań usługi Azure Storage. 

## <a name="prerequisites"></a>Wymagania wstępne
Będą potrzebne:
- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

- Konto usługi Azure Storage z [kontenerem obiektów blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) lub [udziałem plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)lub dostęp do programu [Azure Machine Learning Studio](https://ml.azure.com/).

- Obszar roboczy usługi Azure Machine Learning.
  
  [Utwórz obszar roboczy Azure Machine Learning](how-to-manage-workspace.md) lub Użyj istniejącego z nich za pomocą zestawu SDK języka Python:

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Tworzenie i rejestrowanie magazynów danych

Po zarejestrowaniu rozwiązania usługi Azure Storage jako magazynu danych automatycznie utworzysz ten magazyn danych w określonym obszarze roboczym. Magazyny danych można tworzyć i rejestrować w obszarze roboczym przy użyciu zestawu SDK języka Python lub Azure Machine Learning Studio.

### <a name="python-sdk"></a>Zestaw SDK dla języka Python

Wszystkie metody Register znajdują się w klasie [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) i mają `register_azure_*`formularzy.

Informacje, które należy wypełnić `register()` metodę, można znaleźć za pomocą [Azure Portal](https://portal.azure.com):

1. W okienku po lewej stronie wybierz pozycję **konta magazynu** , a następnie wybierz konto magazynu, które chcesz zarejestrować. 
2. Aby uzyskać informacje takie jak nazwa konta, kontener i nazwa udziału plików, przejdź do strony **Przegląd** . Aby uzyskać informacje o uwierzytelnianiu, takie jak klucz konta lub token SAS, przejdź do pozycji **klucze dostępu** w okienku **Ustawienia** . 

> [!IMPORTANT]
> Jeśli Twoje konto magazynu znajduje się w sieci wirtualnej, obsługiwane jest tylko tworzenie magazynu danych obiektów blob platformy Azure. Aby udzielić obszaru roboczego dostępu do konta magazynu, ustaw `grant_workspace_access` parametru na `True`.

W poniższych przykładach pokazano, jak zarejestrować kontener obiektów blob platformy Azure, udział plików platformy Azure i dane SQL platformy Azure jako magazyn danych.

#### <a name="blob-container"></a>Kontener obiektów blob

Aby zarejestrować kontener obiektów blob platformy Azure jako magazyn danych, użyj [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

Poniższy kod tworzy i rejestruje magazyn danych `blob_datastore_name` w obszarze roboczym `ws`. Ten magazyn danych uzyskuje dostęp do `my-container-name` kontenera obiektów BLOB na koncie magazynu `my-account-name` przy użyciu podanego klucza konta.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>Udział plików

Aby zarejestrować udział plików platformy Azure jako magazyn danych, użyj [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

Poniższy kod tworzy i rejestruje magazyn danych `file_datastore_name` w obszarze roboczym `ws`. Ten magazyn danych uzyskuje dostęp do udziału plików `my-fileshare-name` na koncie magazynu `my-account-name` przy użyciu podanego klucza konta.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                 datastore_name=file_datastore_name, 
                                                 file_share_name=file_share_name, 
                                                 account_name=account_name,
                                                 account_key=account_key)
```

#### <a name="sql-data"></a>Dane SQL

W przypadku usługi Azure SQL datastore Użyj [register_azure_sql_database ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-sql-database-workspace--datastore-name--server-name--database-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--endpoint-none--overwrite-false--username-none--password-none-) w celu zarejestrowania magazynu danych poświadczeń połączonego z bazą danych Azure SQL z uprawnieniami uwierzytelniania SQL lub nazwy głównej usługi. 

Aby zarejestrować się w ramach uwierzytelniania SQL:

```python
sql_datastore_name="azsqlsdksql"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the Azure SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the Azure SQL database
username=os.getenv("SQL_USER_NAME", "<my-sql-user-name>") # Username of the database user to access the database
password=os.getenv("SQL_USER_PASSWORD", "<my-sql-user-password>") # Password of the database user to access the database

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                  datastore_name=sql_datastore_name,
                                                  server_name=server_name,
                                                  database_name=database_name,
                                                  username=username,
                                                  password=password)

```

Aby zarejestrować się za pomocą nazwy głównej usługi:

```python 
sql_datastore_name="azsqlsdksp"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the SQL database
client_id=os.getenv("SQL_CLIENTNAME", "<my-client-id>") # Client ID of the service principal with permissions to access the database
client_secret=os.getenv("SQL_CLIENTSECRET", "<my-client-secret>") # Secret of the service principal
tenant_id=os.getenv("SQL_TENANTID", "<my-tenant-id>") # Tenant ID of the service principal

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                      datastore_name=sql_datastore_name,
                                                      server_name=server_name,
                                                      database_name=database_name,
                                                      client_id=client_id,
                                                      client_secret=client_secret,
                                                      tenant_id=tenant_id)
```

#### <a name="storage-guidance"></a>Wskazówki dotyczące magazynu

Zalecamy używanie kontenera obiektów blob platformy Azure. Magazyny w warstwie Standardowa i Premium są dostępne dla obiektów BLOB. Mimo że Usługa Premium Storage jest droższa, jego szybkość przepływności może zwiększyć szybkość przebiegów szkoleniowych, szczególnie w przypadku uczenia się z dużym zestawem danych. Aby uzyskać informacje o kosztach kont magazynu, zobacz [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

### <a name="azure-machine-learning-studio"></a>Studio uczenia maszynowego Azure 

Utwórz nowy magazyn danych w kilku krokach w programie Azure Machine Learning Studio:

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com/).
1. Wybierz pozycję **magazyny** danych w lewym okienku w obszarze **Zarządzaj**.
1. Wybierz pozycję **+ nowy magazyn**danych.
1. Wypełnij formularz dla nowego magazynu danych. Formularz jest inteligentnie aktualizowany na podstawie wybranych opcji typu usługi Azure Storage i typu uwierzytelniania.
  
Informacje potrzebne do wypełnienia formularza można znaleźć na [Azure Portal](https://portal.azure.com). W okienku po lewej stronie wybierz pozycję **konta magazynu** , a następnie wybierz konto magazynu, które chcesz zarejestrować. Na stronie **Przegląd** znajdują się takie informacje, jak nazwa konta, kontener i nazwa udziału plików. W przypadku elementów uwierzytelniania, takich jak klucz konta lub token sygnatury dostępu współdzielonego, przejdź do pozycji **klucze kont** w okienku **Ustawienia** .

Poniższy przykład pokazuje, jak wygląda formularz podczas tworzenia magazynu danych obiektów blob platformy Azure: 
    
![Formularz dla nowego magazynu danych](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Pobieranie magazynów danych z obszaru roboczego

Aby uzyskać określony magazyn danych zarejestrowany w bieżącym obszarze roboczym, użyj metody statycznej [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) w klasie `Datastore`:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Aby uzyskać listę magazynów danych zarejestrowanych w danym obszarze roboczym, można użyć właściwości [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) w obiekcie obszaru roboczego:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Podczas tworzenia obszaru roboczego kontener obiektów blob platformy Azure i udział plików platformy Azure są automatycznie rejestrowane w obszarze roboczym. Są one nazywane odpowiednio `workspaceblobstore` i `workspacefilestore`. Przechowują informacje o połączeniu dla kontenera obiektów blob oraz udział plików, które są udostępniane na koncie magazynu dołączonym do obszaru roboczego. Kontener `workspaceblobstore` jest ustawiony jako domyślny magazyn danych.

Aby uzyskać domyślny magazyn danych obszaru roboczego, użyj tego wiersza:

```Python
datastore = ws.get_default_datastore()
```

Aby zdefiniować inny domyślny magazyn danych dla bieżącego obszaru roboczego, użyj metody [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) w obiekcie obszaru roboczego:

```Python
# Define the default datastore for the current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Przekazywanie i pobieranie danych

Metody [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) i [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) opisane w poniższych przykładach są specyficzne dla i działają identycznie dla klas [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) i [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

### <a name="upload"></a>Przekazywanie

Przekaż katalog lub pojedyncze pliki do magazynu danych przy użyciu zestawu SDK języka Python:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path` parametr określa lokalizację w udziale plików (lub kontenerze obiektów BLOB) do przekazania. Wartość domyślna to `None`, więc dane są przekazywane do katalogu głównego. W przypadku `overwrite=True`wszystkie istniejące dane w `target_path` są zastępowane.

Możesz również przekazać listę pojedynczych plików do magazynu danych za pomocą metody `upload_files()`.

### <a name="download"></a>Pobierz

Pobierz dane z magazynu danych do lokalnego systemu plików:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Parametr `target_path` jest lokalizacją katalogu lokalnego, do którego mają zostać pobrane dane. Aby określić ścieżkę do folderu w udziale plików (lub kontenera obiektów blob) do pobrania, należy podać tę ścieżkę, aby `prefix`. Jeśli `prefix` jest `None`, zostanie pobrana cała zawartość udziału plików (lub kontenera obiektów BLOB).

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Uzyskiwanie dostępu do danych podczas szkoleń

> [!IMPORTANT]
> Teraz zalecamy korzystanie z [Azure Machine Learning zestawów](how-to-create-register-datasets.md) danych w celu uzyskania dostępu do Twoich potrzeb. Zestawy danych udostępniają funkcje, które ładują dane tabelaryczne do Pandas lub Spark Dataframe. Zestawy danych zapewniają również możliwość pobierania lub instalowania plików dowolnego formatu z usługi Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database i Azure Database for PostgreSQL. [Dowiedz się więcej na temat uczenia się z zestawami danych](how-to-train-with-datasets.md).

W poniższej tabeli wymieniono metody, które poinformują element docelowy obliczeń, jak używać magazynów danych podczas uruchamiania: 

Możliwości|Metoda|Opis|
----|-----|--------
Instalacja| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Służy do instalowania magazynu danych w elemencie docelowym obliczeń. Gdy magazyn danych jest zainstalowany, wszystkie pliki magazynu danych są dostępne dla obiektu docelowego obliczeń.
Pobierz|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Użyj, aby pobrać zawartość magazynu danych do lokalizacji określonej przez `path_on_compute`. <br><br> To pobieranie jest wykonywane przed uruchomieniem.
Przekazywanie|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Służy do przekazywania pliku z lokalizacji określonej przez `path_on_compute` do magazynu danych. <br><br> To przekazywanie jest wykonywane po uruchomieniu.

Aby odwołać się do określonego folderu lub pliku w magazynie danych i udostępnić go w elemencie docelowym obliczeń, użyj metody [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) magazynu danych:

```Python
# To mount the full contents in your storage to the compute target
datastore.as_mount()

# To download the contents of only the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Wszystkie określone `datastore` lub `datastore.path` obiektu są rozpoznawane jako nazwa zmiennej środowiskowej `"$AZUREML_DATAREFERENCE_XXXX"`formacie. Wartość tej nazwy reprezentuje ścieżkę instalacji/pobierania dla elementu docelowego obliczeń. Ścieżka magazynu danych w elemencie docelowym obliczeń może nie być taka sama jak ścieżka wykonywania skryptu szkoleniowego.

### <a name="examples"></a>Przykłady 

Zalecamy używanie klasy [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) do uzyskiwania dostępu do danych podczas szkoleń. 

Zmienna `script_params` jest słownikiem zawierającym parametry do `entry_script`. Użyj go, aby przekazać magazyn danych i opisać, w jaki sposób dane są dostępne w obiekcie docelowym obliczeń. Dowiedz się więcej z [kompleksowego samouczka](tutorial-train-models-with-aml.md).

```Python
from azureml.train.estimator import Estimator

# Notice that '/' is in front, which indicates the absolute path
script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Można także przekazać listę magazynów danych do parametru `inputs` konstruktora `Estimator`, aby zainstalować lub skopiować dane do/z magazynu danych. Ten przykład kodu:
* Pobiera całą zawartość `datastore1` do obiektu docelowego obliczeń przed uruchomieniem skryptu szkoleniowego `train.py`.
* Pobiera folder `'./foo'` w `datastore2` do elementu docelowego obliczeń przed uruchomieniem `train.py`.
* Przekazuje plik `'./bar.pkl'` z elementu docelowego obliczeń do `datastore3` po uruchomieniu skryptu.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
Jeśli wolisz używać obiektu `RunConfig` do szkolenia, musisz skonfigurować obiekt [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) . 

Poniższy kod przedstawia sposób pracy z obiektem `DataReference` w potoku szacowania. Pełny przykład można znaleźć w [tym notesie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb).

```Python
from azureml.core import Datastore
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

def_blob_store = Datastore(ws, "workspaceblobstore")

input_data = DataReference(
       datastore=def_blob_store,
       data_reference_name="input_data",
       path_on_datastore="20newsgroups/20news.pkl")

output = PipelineData("output", datastore=def_blob_store)
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Macierze obliczeniowe i magazyn danych

Magazyny danych obsługują obecnie przechowywanie informacji o połączeniu z usługami magazynu wymienionymi w poniższej macierzy. W tej macierzy przedstawiono dostępne funkcje dostępu do danych dla różnych obiektów docelowych obliczeń i scenariuszy magazynu danych. [Dowiedz się więcej na temat obiektów docelowych obliczeń dla Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Wystąpienia obliczeniowe|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Lokalna|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|ND         |ND                                                                         |
| Obliczeniowe platformy Azure Machine Learning |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [Machine Learning potoków](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [Machine Learning potoków](concept-ml-pipelines.md)|ND         |ND                                                                         |
| Maszyny wirtualne               |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |ND         |ND                                                                         |
| Azure HDInsight                      |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |ND         |ND                                                                         |
| Transfer danych                  |[Potoki Machine Learning](concept-ml-pipelines.md)                                               |ND                                           |[Potoki Machine Learning](concept-ml-pipelines.md)            |[Potoki Machine Learning](concept-ml-pipelines.md)                                                                            |
| Azure Databricks                     |[Potoki Machine Learning](concept-ml-pipelines.md)                                              |ND                                           |[Potoki Machine Learning](concept-ml-pipelines.md)             |ND                                                                         |
| Azure Batch                    |[Potoki Machine Learning](concept-ml-pipelines.md)                                               |ND                                           |ND         |ND                                                                         |
| Azure Data Lake Analytics       |ND                                           |ND                                           |[Potoki Machine Learning](concept-ml-pipelines.md)             |ND                                                                         |

> [!NOTE]
> Mogą być scenariusze, w których wysoce iteracyjne i duże procesy przetwarzania danych są uruchamiane szybciej przy użyciu `as_download()`, a nie `as_mount()`. Możesz sprawdzić poprawność tego eksperymentu.

### <a name="accessing-source-code-during-training"></a>Uzyskiwanie dostępu do kodu źródłowego podczas szkolenia

Usługa Azure Blob Storage ma większe szybkości przepływności niż udział plików platformy Azure i skalowanie na dużą liczbę zadań uruchomionych równolegle. Z tego powodu zalecamy skonfigurowanie przebiegów do korzystania z usługi BLOB Storage na potrzeby przesyłania plików kodu źródłowego.

Poniższy przykład kodu określa w konfiguracji uruchamiania, która magazyn danych obiektów BLOB ma być używana do transferów kodu źródłowego:

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
| [Moduł Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Wdróż modele na IoT Edge urządzeniach. |

W sytuacjach, w których zestaw SDK nie zapewnia dostępu do magazynów danych, może być możliwe utworzenie niestandardowego kodu przy użyciu odpowiedniego zestawu Azure SDK, aby uzyskać dostęp do tego programu. Na przykład [zestaw SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python) jest biblioteką kliencką, za pomocą której można uzyskać dostęp do danych przechowywanych w obiektach Blob lub plikach.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Przenoszenie danych do obsługiwanych rozwiązań usługi Azure Storage

Azure Machine Learning obsługuje dostęp do danych z usługi Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database i Azure Database for PostgreSQL. Jeśli używasz nieobsługiwanego magazynu, zalecamy przeniesienie danych do obsługiwanych rozwiązań usługi Azure Storage za pomocą Azure Data Factory. Przeniesienie danych do obsługiwanego magazynu może pomóc w zapisaniu kosztów ruchu wychodzącego w trakcie eksperymentów z uczeniem maszynowym. 

Azure Data Factory zapewnia wydajny i odporny na transfer danych z ponad 80 wstępnie skompilowanych łączników bez dodatkowych kosztów. Te łączniki obejmują usługi danych platformy Azure, lokalne źródła danych, Amazon S3 i RedShift oraz Google BigQuery. [Postępuj zgodnie z przewodnikiem krok po kroku, aby przenieść dane przy użyciu Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Następne kroki

* [Uczenie modelu](how-to-train-ml-models.md)
* [Wdrażanie modelu](how-to-deploy-and-where.md)
