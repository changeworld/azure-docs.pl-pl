---
title: Kopiowanie danych do i z usługi Azure Data Lake Storage Gen1 przy użyciu usługi fabryka danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z obsługiwanego źródłowego magazynów danych do usługi Azure Data Lake Store lub Data Lake Store do ujścia obsługiwanych magazynów za pomocą usługi Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: jingwang
ms.openlocfilehash: 433824c4e375cf1ce7d7a6fe16730044628ccab1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001630"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Kopiowanie danych z usługi Azure Data Lake Storage Gen1 lub za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-azure-datalake-connector.md)
> * [Bieżąca wersja](connector-azure-data-lake-store.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych do i z usługi Azure Data Lake magazynu Gen1 (wcześniej znane jako usługi Azure Data Lake Store). Opiera się na [omówienie działania kopiowania](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Kopiowanie danych z dowolnego obsługiwanego źródłowego magazynu danych do usługi Azure Data Lake Store lub skopiować dane z usługi Azure Data Lake Store do dowolnego obsługiwanego magazynu danych ujścia. Zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik usługi Azure Data Lake Store obsługuje:

- Kopiowanie plików przy użyciu jednej z następujących metod uwierzytelniania: **nazwy głównej usługi** lub **zarządzanych tożsamości dla zasobów platformy Azure**.
- Kopiowanie plików jako — analizowania lub generowanie plików za pomocą [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> W przypadku kopiowania danych przy użyciu własnego środowiska integration runtime, należy skonfigurować zapory firmowej, aby zezwolić na ruch wychodzący do `<ADLS account name>.azuredatalakestore.net` i `login.microsoftonline.com/<tenant>/oauth2/token` na porcie 443. Drugim jest usługa tokenu zabezpieczeń platformy Azure, która środowiska integration runtime musi komunikować się z można pobrać tokenu dostępu.

## <a name="get-started"></a>Rozpoczęcie pracy

> [!TIP]
> Aby zapoznać się z przewodnikiem za pomocą łącznika usługi Azure Data Lake Store, zobacz [ładowanie danych do usługi Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do usługi Azure Data Lake Store.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla usługi Azure Data Lake Store połączoną usługę:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | `type` Właściwość musi być równa **AzureDataLakeStore**. | Yes |
| dataLakeStoreUri | Informacje o koncie usługi Azure Data Lake Store. Informacja ta ma jedną z następujących formatów: `https://[accountname].azuredatalakestore.net/webhdfs/v1` lub `adl://[accountname].azuredatalakestore.net/`. | Yes |
| subscriptionId | Identyfikator subskrypcji platformy Azure, do której należy konto Data Lake Store. | Wymagane dla ujścia |
| resourceGroupName | Nazwa grupy zasobów platformy Azure, do której należy konto Data Lake Store. | Wymagane dla ujścia |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Można użyć środowiska Azure integration runtime lub może być samodzielnie hostowane środowisko IR (Jeśli Twój magazyn danych znajduje się w sieci prywatnej). Jeśli ta właściwość nie zostanie określony, zostanie użyta domyślne środowisko uruchomieniowe integracji Azure. |Nie |

### <a name="use-service-principal-authentication"></a>Użyj uwierzytelniania jednostki usługi

Aby użyć uwierzytelniania jednostki usługi, zarejestrować jednostki aplikacji w usłudze Azure Active Directory, a następnie przyznać jej dostęp do programu Data Lake Store. Aby uzyskać szczegółowe instrukcje, zobacz [Service-to-service authentication](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Zanotuj następujące wartości, które służą do definiowania połączonej usługi:

- Identyfikator aplikacji
- Klucz aplikacji
- Identyfikator dzierżawy

>[!IMPORTANT]
> Upewnij się, że można przyznać usługi głównej odpowiednie uprawnienia w Data Lake Store:
>- **Jako źródło**: W **Eksplorator danych** > **dostępu**, co najmniej udzielić **odczytu i wykonania** uprawnienia do listy, a następnie skopiuj pliki w folderach i podfolderach. Ewentualnie możesz udzielić **odczytu** uprawnień do kopiowania pojedynczy plik. Możesz dodać do **ten folder i wszystkie obiekty podrzędne** na rekursywny i Dodaj jako **uprawnienia dostępu i domyślny wpis uprawnień**. Nie jest wymagane na kontroli dostępu na poziomie konta (IAM).
>- **Jako obiekt sink**: W **Eksplorator danych** > **dostępu**, co najmniej udzielić **zapisu i wykonania** uprawnień, aby tworzyć elementy podrzędne w folderze. Możesz dodać do **ten folder i wszystkie obiekty podrzędne** na rekursywny i Dodaj jako **uprawnienia dostępu i domyślny wpis uprawnień**. Jeśli używasz środowiska Azure integration runtime można skopiować (zarówno źródła i ujścia znajdują się w chmurze), w zarządzania tożsamościami i Dostępem, należy udzielić co najmniej **czytnika** roli, aby można było umożliwiają wykrywanie region dla Data Lake Store w usłudze Data Factory. Jeśli chcesz uniknąć ta rola zarządzania tożsamościami i Dostępem jawnie [tworzenie środowiska Azure integration runtime](create-azure-integration-runtime.md#create-azure-ir) lokalizacją Data Lake Store. Skojarz je w usłudze Data Lake Store połączone w poniższym przykładzie.

>[!NOTE]
>Do listy folderów, począwszy od katalogu głównego, należy ustawić uprawnienia jednostki usługi, zostanie im przyznany do **na poziomie głównym z uprawnieniami "Execute"**. Jest to wartość true, gdy:
>- **Narzędzie do kopiowania danych** do potoku kopiowania autora.
>- **Interfejs użytkownika usługi Data Factory** do testowania połączenia i przechodząc folderów podczas tworzenia.
>Jeśli masz obawy na nadawanie uprawnień na poziomie głównym, można pominąć połączenie testowe i ścieżka wejściowa ręcznie podczas tworzenia. Działanie kopiowania, będą nadal działać tak długo, jak nazwa główna usługi jest przyznawana z odpowiednimi uprawnieniami na pliki do skopiowania.

Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Yes |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako `SecureString` można bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| dzierżawa | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można je pobrać, ustawiając kursor myszy w prawym górnym rogu witryny Azure portal. | Yes |

**Przykład:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Używać zarządzanych tożsamości do uwierzytelniania zasobów platformy Azure

Fabrykę danych mogą być skojarzone z [tożsamości zarządzanej dla zasobów platformy Azure](data-factory-service-identity.md), który reprezentuje tę fabrykę danych z konkretnych. Tej tożsamości zarządzanej służy bezpośrednio do uwierzytelniania Data Lake Store, podobnie jak za pomocą jednostki usługi. Ta fabryka wyznaczonym umożliwia dostęp i kopiowanie danych do lub z Data Lake Store.

Aby użyć zarządzanych tożsamości do uwierzytelniania zasobów platformy Azure:

1. [Pobrania informacji o tożsamości zarządzanych fabryki danych](data-factory-service-identity.md#retrieve-managed-identity) przez skopiowanie wartości "identyfikator aplikacji tożsamości usługi" wygenerowane wraz z fabryką.
2. Udzielić dostępu do tożsamości zarządzanej Data Lake Store, tak samo jak w przypadku jednostki usługi, po te informacje.

>[!IMPORTANT]
> Upewnij się, że można przyznać data factory zarządzanych tożsamości odpowiednie uprawnienia w Data Lake Store:
>- **Jako źródło**: W **Eksplorator danych** > **dostępu**, co najmniej udzielić **odczytu i wykonania** uprawnienia do listy, a następnie skopiuj pliki w folderach i podfolderach. Ewentualnie możesz udzielić **odczytu** uprawnień do kopiowania pojedynczy plik. Możesz dodać do **ten folder i wszystkie obiekty podrzędne** na rekursywny i Dodaj jako **uprawnienia dostępu i domyślny wpis uprawnień**. Nie jest wymagane na kontroli dostępu na poziomie konta (IAM).
>- **Jako obiekt sink**: W **Eksplorator danych** > **dostępu**, co najmniej udzielić **zapisu i wykonania** uprawnień, aby tworzyć elementy podrzędne w folderze. Możesz dodać do **ten folder i wszystkie obiekty podrzędne** na rekursywny i Dodaj jako **uprawnienia dostępu i domyślny wpis uprawnień**. Jeśli używasz środowiska Azure integration runtime można skopiować (zarówno źródła i ujścia znajdują się w chmurze), w zarządzania tożsamościami i Dostępem, należy udzielić co najmniej **czytnika** roli, aby można było umożliwiają wykrywanie region dla Data Lake Store w usłudze Data Factory. Jeśli chcesz uniknąć ta rola zarządzania tożsamościami i Dostępem jawnie [tworzenie środowiska Azure integration runtime](create-azure-integration-runtime.md#create-azure-ir) lokalizacją Data Lake Store. Skojarz je w usłudze Data Lake Store połączone w poniższym przykładzie.

>[!NOTE]
>Do listy folderów, począwszy od katalogu głównego, należy ustawić uprawnienia tożsamość zarządzaną, zostanie im przyznany do **na poziomie głównym z uprawnieniami "Execute"**. Jest to wartość true, gdy:
>- **Narzędzie do kopiowania danych** do potoku kopiowania autora.
>- **Interfejs użytkownika usługi Data Factory** do testowania połączenia i przechodząc folderów podczas tworzenia.
>Jeśli masz obawy na nadawanie uprawnień na poziomie głównym, można pominąć połączenie testowe i ścieżka wejściowa ręcznie podczas tworzenia. Działanie kopiowania, będą nadal działać tak długo, jak tożsamość zarządzaną otrzymuje z odpowiednimi uprawnieniami na pliki do skopiowania.

W usłudze Azure Data Factory nie trzeba określać żadnych właściwości, oprócz ogólnych informacji Data Lake Store w połączonej usługi.

**Przykład:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby skopiować dane do i z usługi Azure Data Lake Store, należy ustawić `type` właściwości zestawu danych na **AzureDataLakeStoreFile**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **AzureDataLakeStoreFile** |Yes |
| folderPath | Ścieżka do folderu w Data Lake Store. Jeśli nie zostanie określony, wskazuje katalog główny. <br/><br/>Filtr z symbolami wieloznacznymi jest obsługiwany, dozwolone symbole wieloznaczne są: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak); użyj `^` jako znak ucieczki, jeśli nazwą rzeczywistego folderu ma symboli wieloznacznych lub ten znak ucieczki wewnątrz. <br/><br/>Przykłady: wartość rootfolder/podfolder/see więcej przykładów w [folderowi i plikowi Przykłady filtrów](#folder-and-file-filter-examples). |Nie |
| fileName | **Filtr nazwy lub symbol wieloznaczny** dla plików w ramach określonego "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie pliki w folderze. <br/><br/>Dla filtru, dozwolone symbole wieloznaczne są: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak).<br/>— Przykład 1: `"fileName": "*.csv"`<br/>— Przykład 2: `"fileName": "???20180427.txt"`<br/>Użyj `^` jako znak ucieczki, jeśli Twoje rzeczywiste nazwy plików symboli wieloznacznych lub ten znak ucieczki wewnątrz.<br/><br/>Kiedy dla wyjściowego zestawu danych nie jest określona nazwa pliku i **preserveHierarchy** nie został określony w ujściu działania, działanie kopiowania automatycznie generuje nazwę pliku z następującym wzorcem: "*Danych. [identyfikator GUID uruchamiania działania]. [Identyfikator GUID Jeśli FlattenHierarchy]. [format skonfigurowanie]. [kompresji, jeśli skonfigurowano]* ". Na przykład "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". W przypadku kopiowania z tabelaryczne źródła przy użyciu nazwy tabeli zamiast zapytania wzorzec nazwy to "*[Nazwa tabeli]. [ format]. [kompresji, jeśli skonfigurowano]* ". Na przykład "MyTable.csv". |Nie |
| modifiedDatetimeStart | Filtr plików, na podstawie atrybutu: Data ostatniej modyfikacji. Pliki zostanie wybrana, w przypadku ich godzina ostatniej modyfikacji w okresie między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Właściwości może mieć wartość NULL, która oznacza, że żaden filtr atrybutu pliku zostaną zastosowane do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza pliki, których ostatniej modyfikacji atrybut jest większa niż lub równe wartością daty/godziny, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty i godziny zostanie wybrany.| Nie |
| modifiedDatetimeEnd | Filtr plików, na podstawie atrybutu: Data ostatniej modyfikacji. Pliki zostanie wybrana, w przypadku ich godzina ostatniej modyfikacji w okresie między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Właściwości może mieć wartość NULL, która oznacza, że żaden filtr atrybutu pliku zostaną zastosowane do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza pliki, których ostatniej modyfikacji atrybut jest większa niż lub równe wartością daty/godziny, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty i godziny zostanie wybrany.| Nie |
| format | Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować lub generowanie plików za pomocą określonego formatu, obsługiwane są następujące typy formatów plików: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](supported-file-formats-and-compression-codecs.md#text-format), [formatu Json](supported-file-formats-and-compression-codecs.md#json-format), [Avro Format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs.md#orc-format), i [formatu Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) sekcje. |Brak (tylko w przypadku scenariusza kopia binarna) |
| Kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**.<br/>Są obsługiwane poziomy: **Optymalne** i **najszybszy**. |Nie |


>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ **folderPath** tylko.<br>Aby skopiować pojedynczy plik o określonej nazwie, należy określić **folderPath** z część z folderem i **fileName** z nazwą pliku.<br>Aby skopiować podzestaw plików w folderze, podaj **folderPath** z część z folderem i **fileName** z filtr z symbolami wieloznacznymi. 

**Przykład:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md). Ta sekcja zawiera listę właściwości obsługiwanych przez usługi Azure Data Lake Store źródła i ujścia.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store jako źródło

Aby skopiować dane z Data Lake Store, należy ustawić typ źródła w działaniu kopiowania, aby **AzureDataLakeStoreSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | `type` Musi być równa właściwość źródła działania kopiowania: **AzureDataLakeStoreSource**. |Yes |
| cykliczne | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. Należy pamiętać, że w przypadku `recursive` jest ustawiona na wartość PRAWDA, a obiekt sink jest magazynem opartych na plikach, pusty folder lub podfolder nie jest kopiowany lub utworzono obiekt sink. Dozwolone wartości to: **true** (ustawienie domyślne) i **false**. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store jako ujście

Aby skopiować dane do programu Data Lake Store, należy ustawić typ ujścia w działaniu kopiowania, aby **AzureDataLakeStoreSink**. Następujące właściwości są obsługiwane w **ujścia** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | `type` Musi być równa właściwości ujścia działania kopiowania: **AzureDataLakeStoreSink**. |Yes |
| copyBehavior | Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest pliki z magazynu danych oparte na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (ustawienie domyślne)</b>: zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego, do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się w pierwszy poziom folderu docelowego. Pliki docelowe mają nazwy wygenerowany automatycznie. <br/><b>-MergeFiles</b>: scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku/obiektu blob jest określony, nazwa pliku scalonego jest określona nazwa. W przeciwnym razie nazwa pliku jest generowana automatycznie. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Folder i plik Przykłady filtrów

W tej sekcji opisano wynikowe zachowania ścieżkę i nazwę folderu z filtrami symboli wieloznacznych.

| folderPath | fileName | cykliczne | Źródło folder struktury i filtrowanie wyników (pliki **bold** są pobierane)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (puste, użyta domyślna) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (puste, użyta domyślna) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Przykłady działania kopiowania

W tej sekcji opisano wynikowe zachowania operacji kopiowania dla różnych kombinacji `recursive` i `copyBehavior` wartości.

| cykliczne | copyBehavior | Źródło struktury folderów | Wynikowy docelowej |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Element docelowy Folder1 jest tworzony przy użyciu tej samej struktury jako źródła:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Wygenerowany automatycznie nazwę plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + plik3 + File4 + 5 plików zawartości są scalane w jeden plik o nazwie wygenerowany automatycznie plik. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Wygenerowany automatycznie nazwę plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę plik2<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 zawartości są scalane w jeden plik o nazwie wygenerowany automatycznie. Wygenerowany automatycznie nazwę plik1<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
