---
title: Przenoszenie danych z lokalnego usługi HDFS
description: Dowiedz się, jak przenosić dane z lokalnego serwera HDFS przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7652ab72fb972230d98913c2d7e2601737982532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74924350"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Przenoszenie danych z lokalnego serwera PLIKÓW HDFS przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-hdfs-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-hdfs.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Złącze HDFS w wersji 2](../connector-hdfs.md).

W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z lokalnego serwera HDFS. Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania.

Można skopiować dane z usługi HDFS do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Fabryka danych obsługuje obecnie tylko przenoszenie danych z lokalnego usługi HDFS do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych do lokalnego usługi HDFS.

> [!NOTE]
> Działanie kopiowania nie powoduje usunięcia pliku źródłowego po pomyślnym skopiowaniu go do miejsca docelowego. Jeśli chcesz usunąć plik źródłowy po pomyślnej kopii, utwórz działanie niestandardowe, aby usunąć plik i użyć działania w potoku. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Włączanie łączności
Usługa Data Factory obsługuje łączenie się z lokalnym systemem plików HDFS przy użyciu bramy zarządzania danymi. Zobacz [przenoszenie danych między lokalizacjami lokalnymi a chmurą,](data-factory-move-data-between-onprem-and-cloud.md) aby dowiedzieć się więcej o bramie zarządzania danymi i instrukcjach krok po kroku dotyczących konfigurowania bramy. Użyj bramy, aby połączyć się z usługą HDFS, nawet jeśli jest hostowana na maszynie wirtualnej usługi Azure IaaS.

> [!NOTE]
> Upewnij się, że brama zarządzania danymi ma dostęp do **ALL** [serwera węzłów nazw]:[port węzła nazwy] i [serwery węzłów danych]:[port węzła danych] klastra Hadoop. Domyślnie [port węzła nazwy] to 50070, a domyślnie [port węzła danych] to 50075.

Chociaż można zainstalować bramę na tym samym komputerze lokalnym lub maszynie wirtualnej platformy Azure jako usługi HDFS, zaleca się zainstalowanie bramy na oddzielnym komputerze/maszynie wirtualnej usługi Azure IaaS. Posiadanie bramy na oddzielnym komputerze zmniejsza rywalizację o zasoby i zwiększa wydajność. Po zainstalowaniu bramy na oddzielnym komputerze urządzenie powinno mieć dostęp do urządzenia za pomocą systemu PLIKÓW HDFS.

## <a name="getting-started"></a>Wprowadzenie
Potok można utworzyć z działaniem kopiowania, które przenosi dane ze źródła HDFS przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych.

Do utworzenia potoku można również użyć następujących narzędzi: **Azure portal**, **Visual Studio**, Azure **PowerShell**, **szablon usługi Azure Resource Manager,** **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania.

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON.  W przypadku przykładu z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych z magazynu danych HDFS, zobacz [przykład JSON: Kopiowanie danych z lokalnego serwera HDFS do usługi Azure Blob](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) sekcji tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory specyficznych dla usługi HDFS:

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Połączona usługa łączy magazyn danych z fabryką danych. Tworzysz połączony serwis typu **Hdfs,** aby połączyć lokalny system HDFS z fabryką danych. Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej z usługą HDFS.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość typu musi być ustawiona na: **Hdfs** |Tak |
| url |Adres URL do plików TWARDYCH |Tak |
| authenticationType |Anonimowy lub Windows. <br><br> Aby użyć **uwierzytelniania Kerberos** dla łącznika HDFS, zapoznaj się z [tą sekcją,](#use-kerberos-authentication-for-hdfs-connector) aby odpowiednio skonfigurować środowisko lokalne. |Tak |
| userName |Nazwa użytkownika do uwierzytelniania systemu Windows. W przypadku uwierzytelniania `<username>@<domain>.com`Kerberos określ opcję . |Tak (dla uwierzytelniania systemu Windows) |
| hasło |Hasło do uwierzytelniania systemu Windows. |Tak (dla uwierzytelniania systemu Windows) |
| nazwa bramy |Nazwa bramy, której usługa Data Factory powinna używać do łączenia się z usługą HDFS. |Tak |
| encryptedCredential |[New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) dane wyjściowe poświadczeń dostępu. |Nie |

### <a name="using-anonymous-authentication"></a>Korzystanie z uwierzytelniania anonimowego

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Korzystanie z uwierzytelniania systemu Windows

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```
## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych (Azure SQL, Azure blob, tabela platformy Azure itp.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja typeProperties dla zestawu danych typu **FileShare** (który zawiera zestaw danych HDFS) ma następujące właściwości

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| folderPath |Ścieżka do folderu. Przykład: `myfolder`<br/><br/>Użyj znaku ucieczki \ ' dla znaków specjalnych w ciągu. Na przykład: dla folderu\podfolder, określ podfolder folderów\\\\i\\\\d:\samplefolder, określ d: samplefolder.<br/><br/>Tę właściwość można połączyć z **partitionBy,** aby ścieżki folderów były oparte na data-godzinach rozpoczęcia/zakończenia plasterka. |Tak |
| fileName |Określ nazwę pliku w **folderPath,** jeśli chcesz, aby tabela odwoływała się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nazwa pliku nie jest określona dla wyjściowego zestawu danych, nazwa wygenerowanego pliku będzie w następującym formacie: <br/><br/>`Data.<Guid>.txt`(na przykład: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy może służyć do określenia folderu dynamicznegoPath, nazwa pliku dla danych szeregów czasowych. Przykład: folderPath sparametryzowany dla każdej godziny danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **typu** w formacie na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [Sekcje Format tekstu,](data-factory-supported-file-and-compression-formats.md#text-format) [Format Json,](data-factory-supported-file-and-compression-formats.md#json-format) [Format Avro,](data-factory-supported-file-and-compression-formats.md#avro-format) [Format orków](data-factory-supported-file-and-compression-formats.md#orc-format)i [Format parkietu.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Jeśli chcesz **skopiować pliki jako znajdujące się** między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach danych wejściowych i wyjściowych. |Nie |
| kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**. Obsługiwane poziomy to: **Optymalne** i **najszybsze**. Aby uzyskać więcej informacji, zobacz [Formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

> [!NOTE]
> nazwa pliku i fileFilter nie mogą być używane jednocześnie.

### <a name="using-partionedby-property"></a>Korzystanie z właściwości partionedBy
Jak wspomniano w poprzedniej sekcji, można określić folder dynamicznyPath i nazwa pliku dla danych szeregów czasowych z **partitionedBy** właściwości, [funkcje fabryki danych i zmiennych systemowych](data-factory-functions-variables.md).

Aby dowiedzieć się więcej o zestawach danych szeregów czasowych, planowaniu i plasterkach, zobacz [Tworzenie zestawów danych,](data-factory-create-datasets.md) [Planowanie & wykonywanie](data-factory-scheduling-and-execution.md)i Tworzenie [artykułów Potoki.](data-factory-create-pipelines.md)

#### <a name="sample-1"></a>Próbka 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
W tym przykładzie {Slice} jest zastępowany wartością zmiennej systemowej Data Factory SliceStart w określonym formacie (RRRRMMDDHH). SliceStart odnosi się do czasu rozpoczęcia plasterka. Ścieżka folderu jest inna dla każdego plasterka. Na przykład: wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Próbka 2:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
W tym przykładzie rok, miesiąc, dzień i godzina SliceStart są wyodrębniane do oddzielnych zmiennych, które są używane przez właściwości folderPath i fileName.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań.

Mając na uwadze, właściwości dostępne w sekcji typeProperties działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze.

W przypadku działania kopiowania, gdy źródło jest typu **FileSystemSource,** w sekcji typeProperties dostępne są następujące właściwości:

**FileSystemSource** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| Cykliczne |Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda, fałsz (domyślnie) |Nie |

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji
Zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artykuł o szczegółach.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Przykład JSON: kopiowanie danych z lokalnego serwera HDFS do obiektu Blob platformy Azure
W tym przykładzie pokazano, jak skopiować dane z lokalnego usługi HDFS do usługi Azure Blob Storage. Jednak dane mogą być kopiowane **bezpośrednio** do dowolnego ujścia podane [w tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.  

Przykład zawiera definicje JSON dla następujących jednostek usługi Data Factory. Za pomocą tych definicji można utworzyć potok do kopiowania danych z usługi HDFS do usługi Azure Blob Storage przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md)

1. Połączona usługa typu [OnPremisesHdfs](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [FileShare](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [FileSystemSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy kopiuje dane z lokalnego usługi HDFS do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

W pierwszym kroku skonfiguruj bramę zarządzania danymi. Instrukcje w [przenoszeniu danych między lokalizacjami lokalnymi i](data-factory-move-data-between-onprem-and-cloud.md) w chmurze artykułu.

**Usługa połączona z hdfs:** W tym przykładzie użyto uwierzytelniania systemu Windows. Zobacz [sekcję usługi połączonej HDFS](#linked-service-properties) dla różnych typów uwierzytelniania, których można użyć.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Usługa połączona z usługą Azure Storage:**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Zestaw danych wejściowych HDFS:** Ten zestaw danych odnosi się do folderu HDFS DataTransfer/UnitTest/. Potok kopiuje wszystkie pliki w tym folderze do miejsca docelowego.

Ustawienie "zewnętrzne": "true" informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Zestaw danych wyjściowych obiektów blob platformy Azure:**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu obiektu blob jest dynamicznie oceniana na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części czasu rozpoczęcia z roku, miesiąca, dnia i godziny.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Działanie kopiowania w potoku ze źródłem systemu plików i ujściem obiektu Blob:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z tych danych wejściowych i wyjściowych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **FileSystemSource** i typ **ujścia** jest ustawiony na **BlobSink**. Kwerenda SQL określona dla właściwości **kwerendy** wybiera dane w ciągu ostatniej godziny do skopiowania.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Używanie uwierzytelniania Kerberos dla złącza HDFS
Istnieją dwie opcje konfigurowania środowiska lokalnego, tak aby używać uwierzytelniania Kerberos w łączniku HDFS. Możesz wybrać ten, który lepiej pasuje do Twojej sprawy.
* Opcja 1: [Dołącz do maszyny bramy w krainie Kerberos](#kerberos-join-realm)
* Opcja 2: [Włączanie wzajemnego zaufania między domeną systemu Windows a domeną Protokołu Kerberos](#kerberos-mutual-trust)

### <a name="option-1-join-gateway-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Opcja 1: Dołącz do maszyny bramy w krainie Kerberos

#### <a name="requirement"></a>Wymóg:

* Maszyna bramy musi dołączyć do obszaru Kerberos i nie może dołączyć do żadnej domeny systemu Windows.

#### <a name="how-to-configure"></a>Jak skonfigurować:

**Na maszynie bramy:**

1.  Uruchom narzędzie **Ksetup,** aby skonfigurować serwer i obszar KDC protokołu Kerberos.

    Komputer musi być skonfigurowany jako członek grupy roboczej, ponieważ obszar Protokołu Kerberos różni się od domeny systemu Windows. Można to osiągnąć, ustawiając obszar Kerberos i dodając serwer centrum dystrybucji danych w następujący sposób. W razie potrzeby wymień *REALM.COM* na własne, odpowiednie królestwo.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Uruchom ponownie** komputer po wykonaniu tych 2 poleceń.

2.  Sprawdź konfigurację za pomocą polecenia **Ksetup.** Dane wyjściowe powinny być takie:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**W fabryce danych platformy Azure:**

* Skonfiguruj łącznik HDFS przy użyciu **uwierzytelniania systemu Windows** wraz z główną nazwą i hasłem protokołu Kerberos, aby połączyć się ze źródłem danych HDFS. Sprawdź sekcję [Właściwości usługi połączonej usługi HDFS,](#linked-service-properties) aby uzyskać szczegółowe informacje o konfiguracji.

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Opcja 2: Włączanie wzajemnego zaufania między domeną systemu Windows a domeną Protokołu Kerberos

#### <a name="requirement"></a>Wymóg:
*   Komputer bramy musi dołączyć do domeny systemu Windows.
*   Aby zaktualizować ustawienia kontrolera domeny, potrzebne jest uprawnienie.

#### <a name="how-to-configure"></a>Jak skonfigurować:

> [!NOTE]
> W razie potrzeby zamień REALM.COM i AD.COM na własny odpowiedni sferę i kontroler domeny.

**Na serwerze KDC:**

1. Edytuj konfigurację centrum dystrybucji kluczy w pliku **krb5.conf,** aby umożliwić administratorowi usługi Windows zaufanie do domeny systemu Windows w odniesieniu do następującego szablonu konfiguracji. Domyślnie konfiguracja znajduje się na **/etc/krb5.conf**.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log

           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true

           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }

           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM

           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   **Uruchom ponownie** usługę centrum dystrybucji kluczy po zakończeniu konfiguracji.

2. Przygotuj podmiot o nazwie **krbtgt/REALM.COM\@AD.COM** na serwerze KDC za pomocą następującego polecenia:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. W pliku konfiguracyjnym usługi **hadoop.security.auth_to_local,** dodaj `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**Na kontrolerze domeny:**

1.  Uruchom następujące polecenia **Ksetup,** aby dodać wpis obszaru:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Ustanawianie zaufania z domeny systemu Windows do obszaru Kerberos. [hasło] to hasło do głównego **krbtgt/REALM.COM\@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Wybierz algorytm szyfrowania używany w protokołu Kerberos.

    1. Przejdź do usługi Menedżer serwera > zarządzanie zasadami grupy > obiekty zasad grupy > domeny > domyślne lub aktywne zasady domeny oraz edytuj.

    2. W oknie **podręcznym Edytor zarządzania zasadami grupy** przejdź do pozycji Konfiguracja komputera > Zasady > ustawienia systemu Windows > ustawienia zabezpieczeń > zasady lokalne > opcje zabezpieczeń i skonfiguruj **zabezpieczenia sieciowe: Konfigurowanie typów szyfrowania dozwolonych dla protokołu Kerberos**.

    3. Wybierz algorytm szyfrowania, którego chcesz użyć podczas łączenia się z kedz. Często można po prostu wybrać wszystkie opcje.

        ![Typy szyfrowania konfiguracji dla protokołu Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Użyj polecenia **Ksetup,** aby określić algorytm szyfrowania, który ma być używany w określonym pliku REALM.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Utwórz mapowanie między kontem domeny a podmiotem zabezpieczeń Protokołu Kerberos, aby używać podmiotu zabezpieczeń Protokołu Kerberos w domenie systemu Windows.

    1. Uruchom narzędzia administracyjne > **użytkowników i komputerów usługi Active Directory**.

    2. Skonfiguruj zaawansowane funkcje, klikając pozycję **Wyświetl** > **funkcje zaawansowane**.

    3. Znajdź konto, na którym chcesz utworzyć mapowania, a następnie kliknij prawym przyciskiem myszy, aby wyświetlić **mapowania nazw** > kliknij kartę **Nazwy protokołu Kerberos.**

    4. Dodaj podmiot z obszaru.

        ![Tożsamość zabezpieczeń mapy](media/data-factory-hdfs-connector/map-security-identity.png)

**Na maszynie bramy:**

* Uruchom następujące polecenia **Ksetup,** aby dodać wpis obszaru.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**W fabryce danych platformy Azure:**

* Skonfiguruj łącznik HDFS przy użyciu **uwierzytelniania systemu Windows** wraz z kontem domeny lub podmiotem zabezpieczeń Kerberos, aby połączyć się ze źródłem danych HDFS. Sprawdź sekcję [Właściwości usługi połączonej usługi HDFS,](#linked-service-properties) aby uzyskać szczegółowe informacje o konfiguracji.

> [!NOTE]
> Aby mapować kolumny ze źródłowego zestawu danych na kolumny z zestawu danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Wydajność i strojenie
Zobacz [Kopiowanie wydajności działania & dostrajania przewodnika,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Copy Activity) w usłudze Azure Data Factory i na różne sposoby optymalizacji.
