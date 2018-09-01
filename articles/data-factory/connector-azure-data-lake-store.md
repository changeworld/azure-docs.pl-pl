---
title: Kopiowanie danych z usługi Azure Data Lake Storage Gen1 przy użyciu usługi fabryka danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z obsługiwanego źródłowego magazynów danych do usługi Azure Data Lake Store (lub) Data Lake Store do ujścia obsługiwanych magazynów przy użyciu usługi fabryka danych.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: jingwang
ms.openlocfilehash: d500bc9c910858341d7fdacb4d85bffc8be215e1
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338766"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Kopiowanie danych z usługi Azure Data Lake Storage Gen1 lub za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-azure-datalake-connector.md)
> * [Bieżąca wersja](connector-azure-data-lake-store.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych do i z usługi Azure Data Lake magazynu Gen1 (wcześniej znane jako usługi Azure Data Lake Store). Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Kopiowanie danych z dowolnego obsługiwanego źródłowego magazynu danych do usługi Azure Data Lake Store lub skopiować dane z usługi Azure Data Lake Store do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła lub ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik usługi Azure Data Lake Store obsługuje:

- Kopiowanie plików przy użyciu **nazwy głównej usługi** lub **managed service identity (MSI)** uwierzytelniania.
- Kopiowanie plików jako — jest lub analizowania/Generowanie plików za pomocą [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> W przypadku kopiowania danych przy użyciu własne środowisko IR Konfigurowanie zapory firmowej, aby zezwolić na ruch wychodzący do `<ADLS account name>.azuredatalakestore.net` i `login.microsoftonline.com/<tenant>/oauth2/token` na porcie 443. Ta ostatnia jest Azure Usługa tokenu zabezpieczającego (STS) który IR muszą komunikować się z można uzyskać tokenu dostępu.

## <a name="get-started"></a>Rozpocznij

> [!TIP]
> Aby zapoznać się z przewodnikiem za pomocą łącznika usługi Azure Data Lake Store, zobacz [ładowanie danych do usługi Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

>[!NOTE]
>Gdy narzędzie do kopiowania danych umożliwia tworzenie potoku kopiowania lub wykonać testowania połączenia/przechodząc folderów podczas tworzenia za pomocą interfejsu użytkownika usługi ADF, wymaga uprawnień jednostki usługi lub plik MSI, zostanie im przyznany na poziomie głównym. While, wykonania działania kopiowania można pracować tak długo, jak to uprawnienie jest przydzielane do danych do skopiowania. Jeśli potrzebujesz ograniczyć uprawnienia, możesz pominąć operacji tworzenia.

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do usługi Azure Data lake Store.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla usługi Azure Data Lake Store połączoną usługę:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość type musi być równa **AzureDataLakeStore**. | Tak |
| dataLakeStoreUri | Informacje o koncie usługi Azure Data Lake Store. Informacja ta ma jedną z następujących formatów: `https://[accountname].azuredatalakestore.net/webhdfs/v1` lub `adl://[accountname].azuredatalakestore.net/`. | Tak |
| subscriptionId | Identyfikator subskrypcji platformy Azure, do której należy konto Data Lake Store. | Wymagane dla ujścia |
| resourceGroupName | Nazwa grupy zasobów platformy Azure, do której należy konto Data Lake Store. | Wymagane dla ujścia |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

Znaleźć w poniższych sekcjach więcej właściwości i przykłady kodu JSON dla różnymi typami uwierzytelniania:

- [Przy użyciu uwierzytelniania jednostki usługi](#using-service-principal-authentication)
- [Przy użyciu uwierzytelniania tożsamości usługi zarządzanej](#using-managed-service-identity-authentication)

### <a name="using-service-principal-authentication"></a>Przy użyciu uwierzytelniania jednostki usługi

Aby użyć uwierzytelniania jednostki usługi, zarejestruj jednostki aplikacji w usłudze Azure Active Directory (Azure AD), a następnie przyznać jej dostęp do programu Data Lake Store. Aby uzyskać szczegółowe instrukcje, zobacz [Service-to-service authentication](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Zanotuj następujące wartości, które służą do definiowania połączonej usługi:

- Identyfikator aplikacji
- Klucz aplikacji
- Identyfikator dzierżawy

>[!IMPORTANT]
> Upewnij się, że można przyznać usługi głównej odpowiednie uprawnienia w usłudze Azure Data Lake Store:
>- **Jako źródło**, w Eksploratorze danych -> dostępu, co najmniej udzielić **odczytu i wykonania** uprawnienia do listy, a następnie skopiuj pliki w folderze/podfoldery, lub **odczytu** uprawnień do kopiowania pojedynczego pliku; i wybierz opcję Dodaj do **ten folder i wszystkie obiekty podrzędne** dla cyklicznego i dodać **uprawnienia dostępu i domyślny wpis uprawnień**. Nie wymagań dotyczących kontroli dostępu na poziomie konta (IAM).
>- **Jako obiekt sink**, w Eksploratorze danych -> dostępu, co najmniej udzielić **zapisu i wykonania** uprawnień, aby tworzyć elementy podrzędne w folderze, a następnie wybierz do dodania do **ten folder i wszystkie obiekty podrzędne** dla cyklicznego i Dodaj jako **uprawnienia dostępu i domyślny wpis uprawnień**. Jeśli używasz środowiska Azure IR można skopiować (zarówno źródła i ujścia znajdują się w chmurze), kontroli dostępu (IAM), co najmniej udzielić **czytnika** roli w celu umożliwiają usłudze Data Factory wykryć regionu Data Lake Store. Jeśli chcesz uniknąć ta rola zarządzania tożsamościami i Dostępem jawnie [tworzenie środowiska Azure IR](create-azure-integration-runtime.md#create-azure-ir) z lokalizacji usługi Data Lake Store i skojarz w Data Lake Store połączone usługi, w poniższym przykładzie.

Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| dzierżawa | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można je pobrać, ustawiając kursor myszy w prawym górnym rogu witryny Azure portal. | Tak |

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

### <a name="using-managed-service-identity-authentication"></a>Przy użyciu uwierzytelniania tożsamości usługi zarządzanej

Fabrykę danych mogą być skojarzone z [tożsamości usługi zarządzanej](data-factory-service-identity.md), który reprezentuje tę fabrykę danych z konkretnych. Ta tożsamość usługi można korzystać bezpośrednio uwierzytelniającym podobne do jednostki usługi Data Lake Store. Umożliwia to wyznaczonych fabryki, aby dostęp i kopiowanie danych z i do usługi Data Lake Store.

Do uwierzytelniania usługa managed service identity (MSI):

1. [Pobierz tożsamość usługi fabryki danych](data-factory-service-identity.md#retrieve-service-identity) przez skopiowanie wartości "Identyfikator aplikacji tożsamości usługi" wygenerowane wraz z fabryką.
2. Taki sam sposób jak w przypadku następujących nazwy głównej usługi poniżej informacje o udzielać dostępu tożsamości usługi Data Lake Store.

>[!IMPORTANT]
> Upewnij się, że można przyznać data factory usługi tożsamości odpowiednie uprawnienia w usłudze Azure Data Lake Store:
>- **Jako źródło**, w Eksploratorze danych -> dostępu, co najmniej udzielić **odczytu i wykonania** uprawnienia do listy, a następnie skopiuj pliki w folderze/podfoldery, lub **odczytu** uprawnień do kopiowania pojedynczego pliku; i wybierz opcję Dodaj do **ten folder i wszystkie obiekty podrzędne** dla cyklicznego i dodać **uprawnienia dostępu i domyślny wpis uprawnień**. Nie wymagań dotyczących kontroli dostępu na poziomie konta (IAM).
>- **Jako obiekt sink**, w Eksploratorze danych -> dostępu, co najmniej udzielić **zapisu i wykonania** uprawnień, aby tworzyć elementy podrzędne w folderze, a następnie wybierz do dodania do **ten folder i wszystkie obiekty podrzędne** dla cyklicznego i Dodaj jako **uprawnienia dostępu i domyślny wpis uprawnień**. Jeśli używasz środowiska Azure IR można skopiować (zarówno źródła i ujścia znajdują się w chmurze), kontroli dostępu (IAM), co najmniej udzielić **czytnika** roli w celu umożliwiają usłudze Data Factory wykryć regionu Data Lake Store. Jeśli chcesz uniknąć ta rola zarządzania tożsamościami i Dostępem jawnie [tworzenie środowiska Azure IR](create-azure-integration-runtime.md#create-azure-ir) z lokalizacji usługi Data Lake Store i skojarz w Data Lake Store połączone usługi, w poniższym przykładzie.

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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych usługi Azure Data Lake Store.

Aby skopiować dane do/z usługi Azure Data Lake Store, należy ustawić właściwość typu zestawu danych na **AzureDataLakeStoreFile**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość typu elementu dataset musi być równa: **AzureDataLakeStoreFile** |Tak |
| folderPath | Ścieżka do folderu w Data Lake Store. Filtr z symbolami wieloznacznymi nie jest obsługiwana. Przykład: wartość rootfolder/podfolder / |Tak |
| fileName | **Filtr nazwy lub symbol wieloznaczny** dla plików w ramach określonego "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie pliki w folderze. <br/><br/>Dla filtru, dozwolone symbole wieloznaczne są: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak).<br/>— Przykład 1: `"fileName": "*.csv"`<br/>— Przykład 2: `"fileName": "???20180427.txt"`<br/>Użyj `^` jako znak ucieczki, jeśli Twoje rzeczywiste nazwy plików symboli wieloznacznych lub ten znak ucieczki wewnątrz.<br/><br/>Kiedy dla wyjściowego zestawu danych nie jest określona nazwa pliku i **preserveHierarchy** nie został określony w ujściu działania, działanie kopiowania automatycznie generuje nazwę pliku w następującym formacie: "*danych. [ uruchomienia działania identyfikator GUID]. [Identyfikator GUID Jeśli FlattenHierarchy]. [format skonfigurowanie]. [kompresji, jeśli skonfigurowano]* ". Przykładem jest "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". |Nie |
| Format | Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować lub generowanie plików za pomocą określonego formatu są obsługiwane następujące typy plików w formacie: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](supported-file-formats-and-compression-codecs.md#text-format), [formatu Json](supported-file-formats-and-compression-codecs.md#json-format), [Avro Format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs.md#orc-format), i [formatu Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) sekcje. |Brak (tylko w przypadku scenariusza kopia binarna) |
| Kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**.<br/>Są obsługiwane poziomy: **optymalna** i **najszybciej**. |Nie |

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
            "fileName": "myfile.csv.gz",
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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez usługę Azure Data Lake źródła i ujścia.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store jako źródło

Aby skopiować dane z usługi Azure Data Lake Store, należy ustawić typ źródłowego w działaniu kopiowania, aby **AzureDataLakeStoreSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Musi być równa wartości właściwości type źródło działania kopiowania: **AzureDataLakeStoreSource** |Tak |
| cykliczne | Wskazuje, czy dane są odczytywane cyklicznie z folderów podrzędnych lub tylko z określonego folderu. Należy pamiętać podczas cyklicznego jest ustawiona na wartość PRAWDA, a obiekt sink jest magazynu opartego na pliku, pusty folder/podrzędnych — folder nie będą kopiowane utworzone w ujścia.<br/>Dozwolone wartości to: **true** (ustawienie domyślne), **false** | Nie |

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

Aby skopiować dane do usługi Azure Data Lake Store, należy ustawić typ ujścia w działaniu kopiowania, aby **AzureDataLakeStoreSink**. Następujące właściwości są obsługiwane w **ujścia** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Musi być równa wartości właściwości type ujścia działania kopiowania: **AzureDataLakeStoreSink** |Tak |
| copyBehavior | Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest pliki z magazynu danych oparte na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (ustawienie domyślne)</b>: zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się w pierwszy poziom folderu docelowego. Pliki docelowe mają automatycznie wygeneruje nazwę. <br/><b>-MergeFiles</b>: scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku/obiektu Blob jest określony, nazwa pliku scalonego będzie określoną nazwą; w przeciwnym razie może być nazwą pliku generowanych automatycznie. | Nie |

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

### <a name="recursive-and-copybehavior-examples"></a>przykładów rekurencyjnych i copyBehavior

W tej sekcji opisano wynikowe zachowania operacji kopiowania różne kombinacje wartości cyklicznych i copyBehavior.

| cykliczne | copyBehavior | Źródło struktury folderów | Wynikowy docelowej |
|:--- |:--- |:--- |:--- |
| prawda |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | folder docelowy Folder1 jest tworzony przy użyciu tej samej struktury jako źródła:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| prawda |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Wygenerowany automatycznie nazwę plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę File5 |
| prawda |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + plik3 + File4 + 5 plików zawartości są scalane w jeden plik o nazwie wygenerowany automatycznie plik |
| fałsz |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| fałsz |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Wygenerowany automatycznie nazwę plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę plik2<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| fałsz |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 zawartości są scalane w jeden plik o nazwie wygenerowany automatycznie. Wygenerowany automatycznie nazwę plik1<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
