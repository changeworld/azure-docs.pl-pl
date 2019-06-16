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
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: aedfa381f6520a5295467821097b38dd28dcd60c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057915"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Kopiowanie danych do i z usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Azure Data Factory, której używasz:"]
> * [Wersja 1](v1/data-factory-azure-datalake-connector.md)
> * [Bieżąca wersja](connector-azure-data-lake-store.md)

W tym artykule opisano sposób kopiowania danych do i z usługi Azure Data Lake Storage Gen1. Aby dowiedzieć się więcej na temat usługi Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik usługi Azure Data Lake Storage Gen1 jest obsługiwane w przypadku następujących działań:

- [Działanie kopiowania, które](copy-activity-overview.md) z [obsługiwanych macierzy źródła lub ujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)

W szczególności za pomocą tego łącznika możesz wykonywać następujące czynności:

- Skopiuj pliki przy użyciu jednej z następujących metod uwierzytelniania: Usługa tożsamości podmiotu zabezpieczeń lub zarządzanych zasobów platformy Azure.
- Kopiowanie plików to przeanalizować lub generowanie plików za pomocą [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> W przypadku kopiowania danych przy użyciu własnego środowiska integration runtime, należy skonfigurować zapory firmowej, aby zezwolić na ruch wychodzący do `<ADLS account name>.azuredatalakestore.net` i `login.microsoftonline.com/<tenant>/oauth2/token` na porcie 443. Drugim jest usługa tokenu zabezpieczeń platformy Azure, która środowiska integration runtime musi komunikować się z można pobrać tokenu dostępu.

## <a name="get-started"></a>Rozpoczęcie pracy

> [!TIP]
> Aby uzyskać szczegółowe omówienie sposobu korzystania z łącznika usługi Azure Data Lake Store, zobacz [ładowanie danych do usługi Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają informacje na temat właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do usługi Azure Data Lake Store.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla usługi Azure Data Lake Store połączoną usługę:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | `type` Właściwość musi być równa **AzureDataLakeStore**. | Yes |
| dataLakeStoreUri | Informacje o koncie usługi Azure Data Lake Store. Informacja ta ma jedną z następujących formatów: `https://[accountname].azuredatalakestore.net/webhdfs/v1` lub `adl://[accountname].azuredatalakestore.net/`. | Yes |
| subscriptionId | Identyfikator subskrypcji platformy Azure, do której należy konto Data Lake Store. | Wymagane dla ujścia |
| resourceGroupName | Nazwa grupy zasobów platformy Azure, do której należy konto Data Lake Store. | Wymagane dla ujścia |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Środowisko uruchomieniowe integracji platformy Azure lub własnego środowiska integration runtime można użyć, jeśli magazyn danych znajduje się w sieci prywatnej. Jeśli ta właściwość nie jest określona, domyślne środowisko uruchomieniowe integracji platformy Azure jest używana. |Nie |

### <a name="use-service-principal-authentication"></a>Użyj uwierzytelniania jednostki usługi

Aby użyć uwierzytelniania jednostki usługi, zarejestrować jednostki aplikacji w usłudze Azure Active Directory, a następnie przyznać jej dostęp do programu Data Lake Store. Aby uzyskać szczegółowe instrukcje, zobacz [Service-to-service authentication](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Zanotuj następujące wartości, które służą do definiowania połączonej usługi:

- Identyfikator aplikacji
- Klucz aplikacji
- Identyfikator dzierżawy

>[!IMPORTANT]
> Przydział usługi głównej odpowiednie uprawnienia w Data Lake Store:
>- **Jako źródło**: W **Eksplorator danych** > **dostępu**, co najmniej udzielić **odczytu i wykonania** uprawnienia do listy, a następnie skopiuj pliki w folderach i podfolderach. Ewentualnie możesz udzielić **odczytu** uprawnień do kopiowania pojedynczy plik. Możesz dodać do **ten folder i wszystkie obiekty podrzędne** na rekursywny i Dodaj jako **uprawnienia dostępu i domyślny wpis uprawnień**. Nie jest wymagane na kontroli dostępu na poziomie konta (IAM).
>- **Jako obiekt sink**: W **Eksplorator danych** > **dostępu**, co najmniej udzielić **zapisu i wykonania** uprawnień, aby tworzyć elementy podrzędne w folderze. Możesz dodać do **ten folder i wszystkie obiekty podrzędne** na rekursywny i Dodaj jako **uprawnienia dostępu i domyślny wpis uprawnień**. Jeśli używasz środowiska Azure integration runtime można skopiować (zarówno źródła i ujścia znajdują się w chmurze), w zarządzania tożsamościami i Dostępem, należy udzielić co najmniej **czytnika** roli, aby można było umożliwiają wykrywanie region dla Data Lake Store w usłudze Data Factory. Jeśli chcesz uniknąć ta rola zarządzania tożsamościami i Dostępem jawnie [tworzenie środowiska Azure integration runtime](create-azure-integration-runtime.md#create-azure-ir) lokalizacją Data Lake Store. Na przykład w przypadku usługi Data Lake Store w regionie Europa Zachodnia, utworzyć środowisko IR platformy Azure z lokalizacją ustawiona na "Europa Zachodnia." Skojarz je w usłudze Data Lake Store połączone, jak pokazano w poniższym przykładzie.

>[!NOTE]
>Do listy folderów, począwszy od katalogu głównego, należy ustawić uprawnienia jednostki usługi, zostanie im przyznany do **na poziomie głównym z uprawnieniami "Execute"** . Jest to wartość true, gdy:
>- **Narzędzia do kopiowania danych** do potoku kopiowania autora.
>- **Interfejs użytkownika usługi Data Factory** do testowania połączenia i przechodząc folderów podczas tworzenia.
>Jeśli masz wątpliwości dotyczące udzielania uprawnień na poziomie głównym, pominie ten test połączenia i wprowadź ścieżkę ręcznie podczas tworzenia. Działanie kopiowania działa tak długo, jak nazwa główna usługi jest przyznawana z odpowiednimi uprawnieniami, plików do skopiowania.

Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Yes |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako `SecureString` można bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| tenant | Określ informacji o dzierżawie, takie jak nazwa domeny lub identyfikator, pod którą znajduje się aplikacja dzierżawy. Można je pobrać, ustawiając kursor myszy w prawym górnym rogu witryny Azure portal. | Yes |

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
>- **Jako obiekt sink**: W **Eksplorator danych** > **dostępu**, co najmniej udzielić **zapisu i wykonania** uprawnień, aby tworzyć elementy podrzędne w folderze. Możesz dodać do **ten folder i wszystkie obiekty podrzędne** na rekursywny i Dodaj jako **uprawnienia dostępu i domyślny wpis uprawnień**. Jeśli używasz środowiska Azure integration runtime można skopiować (zarówno źródła i ujścia znajdują się w chmurze), w zarządzania tożsamościami i Dostępem, należy udzielić co najmniej **czytnika** roli, aby można było umożliwiają wykrywanie region dla Data Lake Store w usłudze Data Factory. Jeśli chcesz uniknąć ta rola zarządzania tożsamościami i Dostępem jawnie [tworzenie środowiska Azure integration runtime](create-azure-integration-runtime.md#create-azure-ir) lokalizacją Data Lake Store. Skojarz je w usłudze Data Lake Store połączone, jak pokazano w poniższym przykładzie.

>[!NOTE]
>Do listy folderów, począwszy od katalogu głównego, należy ustawić uprawnienia tożsamość zarządzaną, zostanie im przyznany do **na poziomie głównym z uprawnieniami "Execute"** . Jest to wartość true, gdy:
>- **Narzędzia do kopiowania danych** do potoku kopiowania autora.
>- **Interfejs użytkownika usługi Data Factory** do testowania połączenia i przechodząc folderów podczas tworzenia.
>Jeśli masz wątpliwości dotyczące udzielania uprawnień na poziomie głównym, pominie ten test połączenia i wprowadź ścieżkę ręcznie podczas tworzenia. Działanie kopiowania działa tak długo, jak tożsamość zarządzaną otrzymuje z odpowiednimi uprawnieniami na pliki do skopiowania.

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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. 

- Parquet i format tekstu rozdzielanego, zobacz [zestawu danych formatu Parquet i tekst rozdzielany](#parquet-and-delimited-text-format-dataset) sekcji.
- W przypadku innych formatów, takich jak ORC, Avro, JSON lub format binarny, zobacz [innych zestawu danych w formacie](#other-format-dataset) sekcji.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet i zestaw danych, format tekstu rozdzielanego

Aby skopiować dane do i z usługi Azure Data Lake Store Gen1 parquet lub format tekstu rozdzielanego, zobacz [formatu Parquet](format-parquet.md) i [format tekstu rozdzielanego](format-delimited-text.md) artykułów na format na podstawie zestawu danych i obsługiwane ustawienia. Następujące właściwości są obsługiwane w przypadku usługi Azure Data Lake Store Gen1 w obszarze `location` ustawienia w zestawie danych na podstawie formatu:

| Właściwość   | Opis                                                  | Wymagane |
| ---------- | ------------------------------------------------------------ | -------- |
| — typ       | Właściwość type w obszarze `location` w zestawie danych musi być równa **AzureDataLakeStoreLocation**. | Yes      |
| folderPath | Ścieżka do folderu. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania folderów, pomiń to ustawienie i je określić w ustawieniach źródła działania. | Nie       |
| fileName   | Nazwa pliku w ramach danego folderu folderPath. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania plików, pomiń to ustawienie i je określić w ustawieniach źródła działania. | Nie       |

> [!NOTE]
>
> **AzureDataLakeStoreFile** typ zestawu danych w formacie parquet lub wiadomości SMS, wymienione w poniższej sekcji nadal jest obsługiwany, ponieważ służy do kopiowania, wyszukiwania i działaniu GetMetadata zgodności z poprzednimi wersjami. Ale nie działa z funkcją mapowanie przepływu danych. Firma Microsoft zaleca używanie tego nowego modelu, w przyszłości. Usługi Data Factory do tworzenia interfejsu użytkownika generuje te nowe typy.

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Innym formacie zestawu danych

Aby skopiować dane do i z usługi Azure Data Lake Store Gen1 ORC, Avro, JSON lub format binarny, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| — typ | Właściwość typu elementu dataset musi być równa **AzureDataLakeStoreFile**. |Tak |
| folderPath | Ścieżka do folderu w Data Lake Store. Jeśli nie zostanie określony, wskazuje katalog główny. <br/><br/>Filtr z symbolami wieloznacznymi jest obsługiwany. Dozwolone symbole wieloznaczne są `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak). Użyj `^` jako znak ucieczki, jeśli symbol wieloznaczny lub ten znak ucieczki wewnątrz nazwą rzeczywistego folderu. <br/><br/>Na przykład: wartość rootfolder/podfolder /. Zobacz więcej przykładów w [folderowi i plikowi Przykłady filtrów](#folder-and-file-filter-examples). |Nie |
| fileName | Filtr nazwy lub symboli wieloznacznych plików w określonym "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie pliki w folderze. <br/><br/>Dla filtru, są dozwolone symbole wieloznaczne `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak).<br/>— Przykład 1: `"fileName": "*.csv"`<br/>— Przykład 2: `"fileName": "???20180427.txt"`<br/>Użyj `^` jako znak ucieczki, jeśli Twoje rzeczywiste nazwy plików ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.<br/><br/>Kiedy dla wyjściowego zestawu danych nie jest określona nazwa pliku i **preserveHierarchy** nie został określony w ujściu działania, działanie kopiowania automatycznie generuje nazwę pliku z następującym wzorcem: "*Danych. [identyfikator GUID uruchamiania działania]. [Identyfikator GUID Jeśli FlattenHierarchy]. [format skonfigurowanie]. [kompresji, jeśli skonfigurowano]* ", na przykład"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". W przypadku kopiowania z tabelaryczne źródła przy użyciu nazwy tabeli zamiast zapytania wzorzec nazwy to " *[Nazwa tabeli]. [ format]. [kompresji, jeśli skonfigurowano]* ", na przykład"MyTable.csv". |Nie |
| modifiedDatetimeStart | Filtr plików, na podstawie atrybutu ostatniej modyfikacji. Pliki są zaznaczone, jeśli ich godzina ostatniej modyfikacji znajduje się w zakresie czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Ogólną wydajność przenoszenia danych ma wpływ włączenie tego ustawienia plik filtru z bardzo duże ilości plików. <br/><br/> Właściwości może być wartością NULL, co oznacza, że żaden filtr atrybutu pliku jest stosowany do zestawu danych. Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest większa niż lub równa wartości daty/godziny są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza, że zaznaczone są pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty/godziny.| Nie |
| modifiedDatetimeEnd | Filtr plików, na podstawie atrybutu ostatniej modyfikacji. Pliki są zaznaczone, jeśli ich godzina ostatniej modyfikacji znajduje się w zakresie czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Ogólną wydajność przenoszenia danych ma wpływ włączenie tego ustawienia plik filtru z bardzo duże ilości plików. <br/><br/> Właściwości może być wartością NULL, co oznacza, że żaden filtr atrybutu pliku jest stosowany do zestawu danych. Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest większa niż lub równa wartości daty/godziny są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza, że zaznaczone są pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty/godziny.| Nie |
| format | Jeśli chcesz skopiować pliki się między magazynami oparte na plikach (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować lub generowanie plików za pomocą określonego formatu, obsługiwane są następujące typy formatów plików: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, i **ParquetFormat**. Ustaw **typu** właściwości **format** do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstu](supported-file-formats-and-compression-codecs.md#text-format), [formatu JSON](supported-file-formats-and-compression-codecs.md#json-format), [Avro format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc format](supported-file-formats-and-compression-codecs.md#orc-format), i [formatu Parquet ](supported-file-formats-and-compression-codecs.md#parquet-format) sekcje. |Brak (tylko w przypadku scenariusza kopia binarna) |
| compression | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Obsługiwane typy to **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**.<br/>Są obsługiwane poziomy **optymalna** i **najszybciej**. |Nie |


>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ **folderPath** tylko.<br>Aby skopiować pojedynczy plik o określonej nazwie, należy określić **folderPath** z częścią folderu i **fileName** z nazwą pliku.<br>Aby skopiować podzestaw plików w folderze, podaj **folderPath** z częścią folderu i **fileName** za pomocą filtru symbolu wieloznacznego. 

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

- Aby skopiować z parquet lub format tekstu rozdzielanego, zobacz [Parquet i źródło format tekstu rozdzielanego](#parquet-and-delimited-text-format-source) sekcji.
- Aby skopiować z innych formatów, takich jak ORC, Avro, JSON lub format binarny, zobacz [innego formatu źródła](#other-format-source) sekcji.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet i źródło format tekstu rozdzielanego

Aby skopiować dane z usługi Azure Data Lake Store Gen1 parquet lub format tekstu rozdzielanego, zobacz [formatu Parquet](format-parquet.md) i [format tekstu rozdzielanego](format-delimited-text.md) artykułów na źródło działania kopiowania oparta na format i obsługiwane ustawienia. Następujące właściwości są obsługiwane w przypadku usługi Azure Data Lake Store Gen1 w obszarze `storeSettings` ustawienia źródła kopiowania oparta na format:

| Właściwość                 | Opis                                                  | Wymagane                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Właściwość type w obszarze `storeSettings` musi być równa **AzureDataLakeStoreReadSetting**. | Yes                                           |
| recursive                | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. Podczas rekursywnego jest ustawiona na wartość PRAWDA, a obiekt sink jest magazynem opartych na plikach, pusty folder lub podfolder nie jest kopiowany lub utworzono obiekt sink. Dozwolone wartości to **true** (ustawienie domyślne) i **false**. | Nie                                            |
| wildcardFolderPath       | Ścieżka folderu przy użyciu symboli wieloznacznych, aby filtrować foldery źródłowe. <br>Dozwolone symbole wieloznaczne są `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak). Użyj `^` jako znak ucieczki, jeśli symbol wieloznaczny lub ten znak ucieczki wewnątrz nazwą rzeczywistego folderu. <br>Zobacz więcej przykładów w [folderowi i plikowi Przykłady filtrów](#folder-and-file-filter-examples). | Nie                                            |
| wildcardFileName         | Nazwa pliku przy użyciu symboli wieloznacznych w ramach danego folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne są `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak). Użyj `^` jako znak ucieczki, jeśli symbol wieloznaczny lub ten znak ucieczki wewnątrz nazwą rzeczywistego folderu. Zobacz więcej przykładów w [folderowi i plikowi Przykłady filtrów](#folder-and-file-filter-examples). | Tak, jeśli `fileName` nie jest określona w zestawie danych |
| modifiedDatetimeStart    | Filtr plików, na podstawie atrybutu ostatniej modyfikacji. Pliki są zaznaczone, jeśli ich godzina ostatniej modyfikacji znajduje się w zakresie czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br> Właściwości może być wartością NULL, co oznacza, że żaden filtr atrybutu pliku jest stosowany do zestawu danych. Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest większa niż lub równa wartości daty/godziny są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza, że zaznaczone są pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty/godziny. | Nie                                            |
| modifiedDatetimeEnd      | Wartość taka sama jak powyżej.                                               | Nie                                            |
| maxConcurrentConnections | Liczba nawiązywanie połączeń magazynu magazynu jednocześnie. Należy określić tylko wtedy, gdy chcesz ograniczyć liczby jednoczesnych połączeń z magazynem danych. | Nie                                            |

> [!NOTE]
> Parquet lub format tekstu rozdzielanego **AzureDataLakeStoreSource** źródło działania kopiowania typów wymienionych w poniższej sekcji nadal jest obsługiwany, ponieważ jest zgodności z poprzednimi wersjami. Firma Microsoft zaleca używanie tego nowego modelu, w przyszłości. Usługi Data Factory do tworzenia interfejsu użytkownika generuje te nowe typy.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureDataLakeStoreReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Inne źródła formatu

Aby skopiować dane z usługi Azure Data Lake Store Gen1 ORC, Avro, JSON lub format binarny, następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | `type` Właściwość źródła działania kopiowania musi być równa **AzureDataLakeStoreSource**. |Yes |
| recursive | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. Gdy `recursive` jest ustawiona na wartość PRAWDA, a obiekt sink jest magazynem opartych na plikach, pusty folder lub podfolder nie jest kopiowany lub utworzono obiekt sink. Dozwolone wartości to **true** (ustawienie domyślne) i **false**. | Nie |
| maxConcurrentConnections | Liczba nawiązywanie połączeń z magazynem danych jednocześnie. Należy określić tylko wtedy, gdy chcesz ograniczyć liczby jednoczesnych połączeń z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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

- Aby skopiować parquet i format tekstu rozdzielanego, zobacz [Parquet, jak i ujście format tekstu rozdzielanego](#parquet-and-delimited-text-format-sink) sekcji.
- Aby skopiować do innych formatów, takich jak ORC, Avro, JSON lub format binarny, zobacz [ujścia innego formatu](#other-format-sink) sekcji.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet, jak i ujście format tekstu rozdzielanego

Aby skopiować dane do usługi Azure Data Lake Store Gen1 parquet lub format tekstu rozdzielanego, zobacz [formatu Parquet](format-parquet.md) i [format tekstu rozdzielanego](format-delimited-text.md) artykuły o ujścia działania kopiowania oparta na format i obsługiwane ustawienia. Następujące właściwości są obsługiwane w przypadku usługi Azure Data Lake Store Gen1 w obszarze `storeSettings` ustawienia ujścia kopiowania oparta na format:

| Właściwość                 | Opis                                                  | Wymagane |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Właściwość type w obszarze `storeSettings` musi być równa **AzureDataLakeStoreWriteSetting**. | Yes      |
| copyBehavior             | Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest pliki z magazynu danych oparte na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (ustawienie domyślne)</b>: Zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego, do folderu docelowego.<br/><b>-FlattenHierarchy</b>: Wszystkie pliki z folderu źródłowego znajdują się w pierwszy poziom folderu docelowego. Pliki docelowe mają nazwy wygenerowany automatycznie. <br/><b>-MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określony, nazwa pliku scalonego jest określona nazwa. W przeciwnym razie jest automatycznie wygenerowana nazwa pliku. | Nie       |
| maxConcurrentConnections | Liczba nawiązywanie połączeń z magazynem danych jednocześnie. Należy określić tylko wtedy, gdy chcesz ograniczyć liczby jednoczesnych połączeń z magazynem danych. | Nie       |

> [!NOTE]
> Parquet lub format tekstu rozdzielanego **AzureDataLakeStoreSink** ujścia działania typu kopiowania z wymienionych w poniższej sekcji nadal jest obsługiwany, ponieważ jest zgodności z poprzednimi wersjami. Firma Microsoft zaleca używanie tego nowego modelu, w przyszłości. Usługi Data Factory do tworzenia interfejsu użytkownika generuje te nowe typy.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureDataLakeStoreWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Ujścia innego formatu

Aby skopiować dane do usługi Azure Data Lake Store Gen1 ORC, Avro, JSON lub format binarny, następujące właściwości są obsługiwane w **ujścia** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| — typ | `type` Właściwość ujścia działania kopiowania musi być równa **AzureDataLakeStoreSink**. |Yes |
| copyBehavior | Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest pliki z magazynu danych oparte na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (ustawienie domyślne)</b>: Zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego, do folderu docelowego.<br/><b>-FlattenHierarchy</b>: Wszystkie pliki z folderu źródłowego znajdują się w pierwszy poziom folderu docelowego. Pliki docelowe mają nazwy wygenerowany automatycznie. <br/><b>-MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określony, nazwa pliku scalonego jest określona nazwa. W przeciwnym razie nazwa pliku jest generowana automatycznie. | Nie |
| maxConcurrentConnections | Liczba nawiązywanie połączeń z magazynem danych jednocześnie. Należy określić tylko wtedy, gdy chcesz ograniczyć liczby jednoczesnych połączeń z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
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

| folderPath | fileName | recursive | Źródło folder struktury i filtrowanie wyników (pliki **bold** są pobierane)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Puste, użyta domyślna) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Puste, użyta domyślna) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Przykłady działania kopiowania

W tej sekcji opisano wynikowe zachowania operacji kopiowania dla różnych kombinacji `recursive` i `copyBehavior` wartości.

| recursive | copyBehavior | Źródło struktury folderów | Wynikowy docelowej |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Element docelowy Folder1 jest tworzony przy użyciu tej samej struktury jako źródła:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + plik3 + File4 + File5 zawartości są scalane w jeden plik o nazwie pliku wygenerowany automatycznie. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa plik2<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 zawartości są scalane w jeden plik o nazwie wygenerowany automatycznie. automatycznie wygenerowana nazwa Plik1<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Zachowaj listy ACL, aby Data Lake Storage Gen2

Jeśli chcesz replikować listy kontroli dostępu (ACL) wraz z plikami danych podczas uaktualniania z programu Data Lake Storage Gen1 Data Lake Storage Gen2, zobacz [zachować listy ACL z Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1).

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Dowiedz się więcej o [źródła przekształcenia](data-flow-source.md) i [ujścia przekształcania](data-flow-sink.md) w funkcji mapowania przepływu danych.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
