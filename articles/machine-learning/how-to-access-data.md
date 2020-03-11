---
title: Dostęp do danych w usługach Azure Storage
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać magazynów danych do bezpiecznego łączenia się z usługami Azure Storage podczas uczenia się z Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: 36d622bf2873b7e629a0f6abeecded33e32898f5
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082099"
---
# <a name="access-data-in-azure-storage-services"></a>Dostęp do danych w usługach Azure Storage
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak łatwo uzyskać dostęp do danych w usługach Azure Storage za pośrednictwem Azure Machine Learning magazynów zasobów. Magazyny danych służą do przechowywania informacji o połączeniu, takich jak identyfikator subskrypcji i autoryzacja tokenu. Gdy używasz magazynów danych, możesz uzyskać dostęp do magazynu bez konieczności używania w skryptach informacji o połączeniu z kodem. 

Magazyny danych można tworzyć na podstawie [tych rozwiązań usługi Azure Storage](#matrix). W przypadku nieobsługiwanych rozwiązań magazynu i zapisania kosztów ruchu wychodzącego w trakcie eksperymentów z uczeniem maszynowym zalecamy [przeniesienie danych](#move) do obsługiwanych rozwiązań usługi Azure Storage. 

## <a name="prerequisites"></a>Wymagania wstępne
Będą potrzebne:
- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

- Konto usługi Azure Storage z [kontenerem obiektów blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) lub [udziałem plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)lub dostęp do programu [Azure Machine Learning Studio](https://ml.azure.com/).

- Obszar roboczy usługi Azure Machine Learning.
  
  [Utwórz obszar roboczy Azure Machine Learning](how-to-manage-workspace.md) lub Użyj istniejącego z nich za pomocą zestawu SDK języka Python. Zaimportuj klasę `Workspace` i `Datastore` i Załaduj informacje o subskrypcji z pliku `config.json` przy użyciu `from_config()`funkcji. Spowoduje to, że plik JSON jest domyślnie używany w bieżącym katalogu, ale można także określić parametr ścieżki, aby wskazywał plik przy użyciu `from_config(path="your/file/path")`.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Obsługiwane typy usługi magazynu danych

Magazyny danych obsługują obecnie przechowywanie informacji o połączeniu z usługami magazynu wymienionymi w poniższej macierzy. W tej chwili usługa Azure Data Warehouse nie jest obsługiwana. 

| Typ&nbsp;magazynu | Typ&nbsp;uwierzytelniania | [Azure&nbsp;Machine&nbsp;Learning Studio](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Interfejs wiersza polecenia uczenia maszynowego Azure&nbsp;Machine&nbsp;](reference-azure-machine-learning-cli.md) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; interfejs API REST](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Usługa Azure&nbsp;BLOB&nbsp;Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Klucz konta <br> Token SAS | ✓ | ✓ | ✓ |✓
[Udział&nbsp;plików&nbsp;platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Klucz konta <br> Token SAS | ✓ | ✓ | ✓ |✓
[Magazyn Azure&nbsp;Data Lake&nbsp;gen&nbsp;1](https://docs.microsoft.com/azure/data-lake-store/)| Nazwa główna usługi| ✓ | ✓ | ✓ |✓
[Magazyn Azure&nbsp;Data Lake&nbsp;gen&nbsp;2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Nazwa główna usługi| ✓ | ✓ | ✓ |✓
Baza danych SQL&nbsp;usługi Azure&nbsp;| Uwierzytelnianie SQL <br>Nazwa główna usługi| ✓ | ✓ | ✓ |✓
Usługa Azure&nbsp;PostgreSQL | Uwierzytelnianie SQL| ✓ | ✓ | ✓ |✓
Usługa Azure&nbsp;Database&nbsp;dla programu&nbsp;MySQL | Uwierzytelnianie SQL|  | ✓* | ✓* |✓*
&nbsp;&nbsp;system plików| Brak uwierzytelniania | | ✓** | ✓ ** |✓** 

*Baza danych MySQL jest obsługiwana tylko w przypadku potoku [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br> \** Datakostki są obsługiwane tylko dla [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) potoków

### <a name="storage-guidance"></a>Wskazówki dotyczące magazynu

Zalecamy utworzenie magazynu danych dla kontenera obiektów blob platformy Azure.  
Magazyny w warstwie Standardowa i Premium są dostępne dla obiektów BLOB. Mimo że Usługa Premium Storage jest droższa, jego szybkość przepływności może zwiększyć szybkość przebiegów szkoleniowych, szczególnie w przypadku uczenia się z dużym zestawem danych. Aby uzyskać informacje o kosztach kont magazynu, zobacz [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

Podczas tworzenia obszaru roboczego kontener obiektów blob platformy Azure i udział plików platformy Azure są automatycznie rejestrowane w obszarze roboczym. Są one nazywane odpowiednio `workspaceblobstore` i `workspacefilestore`. Przechowują informacje o połączeniu dla kontenera obiektów blob oraz udział plików, które są udostępniane na koncie magazynu dołączonym do obszaru roboczego. Kontener `workspaceblobstore` jest ustawiony jako domyślny magazyn danych.

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Tworzenie i rejestrowanie magazynów danych

Po zarejestrowaniu rozwiązania usługi Azure Storage jako magazynu danych automatycznie utworzysz i zarejestrujesz ten magazyn danych w określonym obszarze roboczym. Magazyny danych można tworzyć i rejestrować w obszarze roboczym przy użyciu zestawu SDK języka Python lub Azure Machine Learning Studio.

>[!IMPORTANT]
> W ramach bieżącego procesu tworzenia i rejestrowania magazynu danych Azure Machine Learning sprawdza poprawność podanego przez użytkownika podmiotu zabezpieczeń (nazwa użytkownika, główna usługa lub token sygnatury dostępu współdzielonego), który ma dostęp do podstawowej usługi magazynu. 
<br><br>
Jednak w przypadku Azure Data Lake Storage generacji 1 i 2 magazynów danych sprawdzanie poprawności odbywa się później, gdy wywoływana jest metoda dostępu do danych, taka jak [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) lub [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) . 

### <a name="python-sdk"></a>Zestaw SDK dla języka Python

Wszystkie metody Register znajdują się w klasie [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) i mają `register_azure_*`formularzy.

Możesz znaleźć informacje potrzebne do wypełnienia metody `register()` na [Azure Portal](https://portal.azure.com).
W okienku po lewej stronie wybierz pozycję **konta magazynu** , a następnie wybierz konto magazynu, które chcesz zarejestrować. Na stronie **Przegląd** znajdują się takie informacje, jak nazwa konta, kontener i nazwa udziału plików. 

* W przypadku elementów uwierzytelniania, takich jak klucz konta lub token sygnatury dostępu współdzielonego, przejdź do pozycji **klucze kont** w okienku **Ustawienia** . 

* W przypadku elementów nazwy głównej usługi, takich jak identyfikator dzierżawy i identyfikator klienta, przejdź do **rejestracje aplikacji** i wybierz aplikację, której chcesz użyć. Odpowiadająca jej Strona **przeglądu** będzie zawierać te elementy.

> [!IMPORTANT]
> Jeśli Twoje konto magazynu znajduje się w sieci wirtualnej, obsługiwane jest tylko tworzenie obiektów blob, udziałów plików, ADLS generacji 1 i ADLS generacji 2 danych **z użyciem zestawu SDK** . Aby udzielić obszaru roboczego dostępu do konta magazynu, ustaw `grant_workspace_access` parametru na `True`.

W poniższych przykładach pokazano, jak zarejestrować kontener obiektów blob platformy Azure, udział plików platformy Azure i Azure Data Lake Storage generacja 2 jako magazyn danych. W przypadku innych usług magazynu należy zapoznać [się z dokumentacją dotyczącą `register_azure_*` metod](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

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

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage generacja 2

W przypadku magazynu danych Azure Data Lake Storage Generation 2 (ADLS Gen 2) należy użyć [register_azure_data_lake_gen2 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) do zarejestrowania magazynu danych poświadczeń połączonego z magazynem usługi Azure datalake Generation 2 z [uprawnieniami nazw głównych](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Aby można było korzystać z jednostki usługi, należy [zarejestrować aplikację](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) i ustawić przypisania ról do czytnika i dostępu do danych. Dowiedz się więcej [na temat kontroli dostępu skonfigurowanej do ADLS generacji 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Poniższy kod tworzy i rejestruje magazyn danych `adlsgen2_datastore_name` w obszarze roboczym `ws`. Ten magazyn danych uzyskuje dostęp do systemu plików `test` na koncie magazynu `account_name` przy użyciu podanych poświadczeń jednostki usługi.

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```

### <a name="azure-machine-learning-studio"></a>Studio uczenia maszynowego Azure 

Utwórz nowy magazyn danych w kilku krokach w programie Azure Machine Learning Studio:

> [!IMPORTANT]
> Jeśli konto magazynu znajduje się w sieci wirtualnej, obsługiwane jest tylko tworzenie magazynów danych [za pośrednictwem zestawu SDK](#python-sdk) . 

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com/).
1. Wybierz pozycję **magazyny** danych w lewym okienku w obszarze **Zarządzaj**.
1. Wybierz pozycję **+ nowy magazyn**danych.
1. Wypełnij formularz dla nowego magazynu danych. Formularz jest inteligentnie aktualizowany na podstawie wybranych opcji typu usługi Azure Storage i typu uwierzytelniania.
  
Informacje potrzebne do wypełnienia formularza można znaleźć na [Azure Portal](https://portal.azure.com). W okienku po lewej stronie wybierz pozycję **konta magazynu** , a następnie wybierz konto magazynu, które chcesz zarejestrować. Na stronie **Przegląd** znajdują się takie informacje, jak nazwa konta, kontener i nazwa udziału plików. 

* W przypadku elementów uwierzytelniania, takich jak klucz konta lub token sygnatury dostępu współdzielonego, przejdź do pozycji **klucze kont** w okienku **Ustawienia** . 

* W przypadku elementów nazwy głównej usługi, takich jak identyfikator dzierżawy i identyfikator klienta, przejdź do **rejestracje aplikacji** i wybierz aplikację, której chcesz użyć. Odpowiadająca jej Strona **przeglądu** będzie zawierać te elementy. 

Poniższy przykład pokazuje, jak wygląda formularz podczas tworzenia magazynu danych obiektów blob platformy Azure: 
    
![Formularz dla nowego magazynu danych](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Pobieranie magazynów danych z obszaru roboczego

> [!IMPORTANT]
> Program Azure Machine Learning Designer (wersja zapoznawcza) utworzy magazyn danych o nazwie **azureml_globaldatasets** automatycznie po otwarciu przykładu na stronie głównej projektanta. Ten magazyn danych zawiera tylko przykładowe zestawy danych. **Nie** używaj tego magazynu danych do uzyskiwania dostępu do poufnych informacji.
> ![utworzony przez projektanta magazyny danych dla przykładów przykładowych](media/how-to-access-data/datastore-designer-sample.png)

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

### <a name="upload"></a>Upload

Przekaż katalog lub pojedyncze pliki do magazynu danych przy użyciu zestawu SDK języka Python:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path` parametr określa lokalizację w udziale plików (lub kontenerze obiektów BLOB) do przekazania. Wartość domyślna to `None`, więc dane są przekazywane do katalogu głównego. W przypadku `overwrite=True`wszystkie istniejące dane w `target_path` są zastępowane.

Możesz również przekazać listę pojedynczych plików do magazynu danych za pomocą metody `upload_files()`.

### <a name="download"></a>Pobieranie

Pobierz dane z magazynu danych do lokalnego systemu plików:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Parametr `target_path` jest lokalizacją katalogu lokalnego, do którego mają zostać pobrane dane. Aby określić ścieżkę do folderu w udziale plików (lub kontenerze obiektów BLOB) do pobrania, podaj tę ścieżkę do `prefix`. Jeśli `prefix` jest `None`, zostanie pobrana cała zawartość udziału plików (lub kontenera obiektów BLOB).

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Uzyskiwanie dostępu do danych podczas szkoleń

Aby móc korzystać z danych w twoich sklepach datastores lub spakować dane do obiektu, którego można używać do wykonywania zadań uczenia maszynowego, takich jak szkolenie, [utwórz Azure Machine Learning zestaw danych](how-to-create-register-datasets.md). Zestawy danych udostępniają funkcje, które ładują dane tabelaryczne do Pandas lub Spark Dataframe. Zestawy danych zapewniają również możliwość pobierania lub instalowania plików dowolnego formatu z usługi Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database i Azure Database for PostgreSQL. [Dowiedz się więcej na temat uczenia się z zestawami danych](how-to-train-with-datasets.md).

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
| [Prognoza wsadowa](how-to-use-parallel-run-step.md) | ✔ | Asynchroniczne wykonywanie prognoz dotyczących dużych ilości danych. |
| [Usługa sieci Web](how-to-deploy-and-where.md) | &nbsp; | Wdróż modele jako usługę sieci Web. |
| [Moduł Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Wdróż modele na IoT Edge urządzeniach. |

W sytuacjach, w których zestaw SDK nie zapewnia dostępu do magazynów danych, może być możliwe utworzenie niestandardowego kodu przy użyciu odpowiedniego zestawu Azure SDK, aby uzyskać dostęp do tego programu. Na przykład [zestaw SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python) jest biblioteką kliencką, za pomocą której można uzyskać dostęp do danych przechowywanych w obiektach Blob lub plikach.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Przenoszenie danych do obsługiwanych rozwiązań usługi Azure Storage

Azure Machine Learning obsługuje dostęp do danych z usługi Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database i Azure Database for PostgreSQL. Jeśli używasz nieobsługiwanego magazynu, zalecamy przeniesienie danych do obsługiwanych rozwiązań usługi Azure Storage przy użyciu [Azure Data Factory i tych kroków](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). Przeniesienie danych do obsługiwanego magazynu może pomóc w zapisaniu kosztów ruchu wychodzącego w trakcie eksperymentów z uczeniem maszynowym. 

Azure Data Factory zapewnia wydajny i odporny na transfer danych z ponad 80 wstępnie skompilowanych łączników bez dodatkowych kosztów. Te łączniki obejmują usługi danych platformy Azure, lokalne źródła danych, Amazon S3 i RedShift oraz Google BigQuery.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zestawu danych usługi Azure Machine Learning](how-to-create-register-datasets.md)
* [Uczenie modelu](how-to-train-ml-models.md)
* [Wdrażanie modelu](how-to-deploy-and-where.md)
