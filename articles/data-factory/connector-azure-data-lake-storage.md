---
title: Kopiowanie danych do lub z Azure Data Lake Storage Gen2 przy użyciu Data Factory | Microsoft Docs
description: Dowiedz się, jak kopiować dane do i z Azure Data Lake Storage Gen2 przy użyciu Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: a0a7a413d6c3344ccf5c3f7e4d14dd3d82715034
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840306"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Kopiowanie danych do lub z Azure Data Lake Storage Gen2 przy użyciu Azure Data Factory

Azure Data Lake Storage Gen2 to zestaw funkcji przeznaczonych do analizy danych Big Data wbudowanych w [usługę Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md). Można jej używać do interfejsów z danymi przy użyciu zarówno odmian systemu plików, jak i magazynu obiektów.

W tym artykule opisano sposób kopiowania danych do i z Azure Data Lake Storage Gen2. Aby dowiedzieć się więcej na temat usługi Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik Azure Data Lake Storage Gen2 jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową lub ujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)

W przypadku tego łącznika możesz:

- Skopiuj dane przy użyciu klucza konta, nazwy głównej usługi lub tożsamości zarządzanych dla uwierzytelniania zasobów platformy Azure.
- Kopiuj pliki jako plik lub Analizuj lub generuj pliki z [obsługiwanymi formatami plików i koderami-dekoder kompresji](supported-file-formats-and-compression-codecs.md).

>[!TIP]
>Jeśli włączysz hierarchiczną przestrzeń nazw, obecnie nie istnieje współdziałanie operacji między obiektami BLOB i Data Lake Storage Gen2 interfejsów API. Jeśli napotkasz błąd "ErrorCode = FilesystemNotFound" z komunikatem "określony system plików nie istnieje", jest on spowodowany przez określony w tym systemie plik ujścia, który został utworzony za pośrednictwem interfejsu API obiektów BLOB zamiast Data Lake Storage Gen2 interfejsu API w innym miejscu. Aby rozwiązać ten problem, określ nowy system plików o nazwie, która nie istnieje jako nazwa kontenera obiektów BLOB. Następnie Data Factory automatycznie tworzy ten system plików podczas kopiowania danych.

>[!NOTE]
>Jeśli włączysz opcję **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu** w ustawieniach zapory usługi Azure Storage, środowisko Azure Integration Runtime nie nawiąże połączenia z Data Lake Storage Gen2 i wyświetli błąd zabroniony. Zostanie wyświetlony komunikat o błędzie, ponieważ Data Factory nie jest traktowana jako zaufana usługa firmy Microsoft. Aby nawiązać połączenie, użyj własnego środowiska Integration Runtime.

## <a name="get-started"></a>Wprowadzenie

>[!TIP]
>Aby zapoznać się z przewodnikiem dotyczącym korzystania z łącznika Data Lake Storage Gen2, zobacz [ładowanie danych do Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają informacje o właściwościach, które są używane do definiowania Data Factory jednostek specyficznych dla Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Łącznik Azure Data Lake Storage Gen2 obsługuje następujące typy uwierzytelniania. Szczegółowe informacje znajdują się w odpowiednich sekcjach:

- [Uwierzytelnianie za pomocą klucza konta](#account-key-authentication)
- [Uwierzytelnianie jednostki usługi](#service-principal-authentication)
- [Zarządzanych tożsamości do uwierzytelniania zasobów platformy Azure](#managed-identity)

>[!NOTE]
>W przypadku ładowania danych do SQL Data Warehouse, jeśli Data Lake Storage Gen2 źródłowa została skonfigurowana za pomocą usługi Base Virtual Network Endpoint, należy użyć uwierzytelniania tożsamości zarządzanej wymagane przez bazę danych. Zapoznaj się z sekcją [uwierzytelnianie tożsamości zarządzanej](#managed-identity) , podając więcej wymagań wstępnych dotyczących konfiguracji.

### <a name="account-key-authentication"></a>Uwierzytelnianie za pomocą klucza konta

Aby użyć uwierzytelniania klucza konta magazynu, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **AzureBlobFS**. |Yes |
| url | Punkt końcowy dla Data Lake Storage Gen2 ze wzorcem `https://<accountname>.dfs.core.windows.net`. | Yes |
| accountKey | Klucz konta dla Data Lake Storage Gen2. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Jeśli magazyn danych znajduje się w sieci prywatnej, możesz użyć środowiska Azure Integration Runtime lub własnego środowiska Integration Runtime. Jeśli ta właściwość nie jest określona, zostanie użyta domyślna usługa Azure Integration Runtime. |Nie |

**Przykład:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Aby użyć uwierzytelniania jednostki usługi, wykonaj następujące kroki.

1. Zarejestruj jednostkę aplikacji w Azure Active Directory (Azure AD), wykonując czynności opisane w temacie [Rejestrowanie aplikacji w dzierżawie usługi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Zanotuj następujące wartości, które służą do definiowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Przyznaj jednostce usługi odpowiednie uprawnienia. Dowiedz się więcej na temat tego, jak uprawnienie działa w Data Lake Storage Gen2 z [list kontroli dostępu do plików i katalogów](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Jako źródło**: W Eksplorator usługi Storage Udziel co najmniej uprawnienia **Execute** rozpoczynającego się od źródłowego systemu plików, wraz z uprawnieniem **Odczyt** dla plików do skopiowania. Alternatywnie, w kontroli dostępu (IAM), należy przyznać co najmniej rolę **czytnika danych obiektów blob magazynu** .
    - **Jako ujścia**: W Eksplorator usługi Storage Udziel co najmniej uprawnienia **Execute** rozpoczynającego się od systemu plików ujścia oraz uprawnienia do **zapisu** dla folderu ujścia. Alternatywnie, w kontroli dostępu (IAM), udziel co najmniej roli **współautor danych obiektów blob magazynu** .

>[!NOTE]
>Aby wyświetlić listę folderów zaczynających się na poziomie konta lub w celu przetestowania połączenia, należy ustawić uprawnienia jednostki usługi przyznawane na **konto magazynu z uprawnieniem "czytnik danych obiektów blob magazynu" w usłudze IAM**. Ta wartość obowiązuje w przypadku użycia:
>- **Narzędzie do kopiowania danych** w celu utworzenia potoku kopiowania.
>- **Data Factory interfejsie użytkownika** do testowania połączenia i nawigowania po folderach podczas tworzenia. 
>Jeśli masz problemy z udzieleniem uprawnienia na poziomie konta podczas tworzenia, Pomiń testowanie połączenia i wprowadź ścieżkę nadrzędną z przyznanymi uprawnieniami, a następnie wybierz opcję Przeglądaj z tej określonej ścieżki. Działanie kopiowania działa tak długo, jak jednostka usługi jest udzielana z odpowiednimi uprawnieniami w plikach do skopiowania.

Te właściwości są obsługiwane dla połączonej usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **AzureBlobFS**. |Yes |
| url | Punkt końcowy dla Data Lake Storage Gen2 ze wzorcem `https://<accountname>.dfs.core.windows.net`. | Yes |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Yes |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako `SecureString` , aby bezpiecznie przechowywać je w Data Factory. Lub można odwołać [się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| tenant | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Pobierz ją przez umieszczenie kursora myszy w prawym górnym rogu Azure Portal. | Tak |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Jeśli magazyn danych znajduje się w sieci prywatnej, możesz użyć środowiska Azure Integration Runtime lub własnego środowiska Integration Runtime. Jeśli nie zostanie określony, zostanie użyta domyślna usługa Azure Integration Runtime. |Nie |

**Przykład:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Zarządzanych tożsamości do uwierzytelniania zasobów platformy Azure

Fabrykę danych mogą być skojarzone z [tożsamości zarządzanej dla zasobów platformy Azure](data-factory-service-identity.md), który reprezentuje tę fabrykę danych z konkretnych. Tej tożsamości zarządzanej można bezpośrednio użyć do uwierzytelniania Data Lake Storage Gen2, podobnie jak w przypadku korzystania z własnej nazwy głównej usługi. Umożliwia to wyznaczeniu fabryki dostęp do danych i ich kopiowanie do lub z Data Lake Storage Gen2.

Aby używać tożsamości zarządzanych do uwierzytelniania zasobów platformy Azure, wykonaj następujące kroki.

1. [Pobierz Data Factory Informacje o zarządzanej tożsamości](data-factory-service-identity.md#retrieve-managed-identity) , kopiując wartość **identyfikatora aplikacji tożsamości usługi** wygenerowanego wraz z fabryką.

2. Przyznaj prawidłowe uprawnienia tożsamości zarządzanej. Dowiedz się więcej na temat tego, jak uprawnienie działa w Data Lake Storage Gen2 z [list kontroli dostępu do plików i katalogów](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Jako źródło**: W Eksplorator usługi Storage Udziel co najmniej uprawnienia **Execute** rozpoczynającego się od źródłowego systemu plików, wraz z uprawnieniem **Odczyt** dla plików do skopiowania. Alternatywnie, w kontroli dostępu (IAM), należy przyznać co najmniej rolę **czytnika danych obiektów blob magazynu** .
    - **Jako ujścia**: W Eksplorator usługi Storage Udziel co najmniej uprawnienia **Execute** rozpoczynającego się od systemu plików ujścia oraz uprawnienia do **zapisu** dla folderu ujścia. Alternatywnie, w kontroli dostępu (IAM), udziel co najmniej roli **współautor danych obiektów blob magazynu** .

>[!NOTE]
>Aby wyświetlić listę folderów zaczynających się na poziomie konta lub w celu przetestowania połączenia, należy ustawić uprawnienia zarządzanej tożsamości na **konto magazynu z uprawnieniem "czytnik danych obiektów blob magazynu" w usłudze IAM**. Ta wartość obowiązuje w przypadku użycia:
>- **Narzędzie do kopiowania danych** w celu utworzenia potoku kopiowania.
>- **Data Factory interfejsie użytkownika** do testowania połączenia i nawigowania po folderach podczas tworzenia. 
>Jeśli masz problemy z udzieleniem uprawnienia na poziomie konta podczas tworzenia, Pomiń testowanie połączenia i wprowadź ścieżkę nadrzędną z przyznanymi uprawnieniami, a następnie wybierz opcję Przeglądaj z tej określonej ścieżki. Działanie kopiowania działa tak długo, jak jednostka usługi jest udzielana z odpowiednimi uprawnieniami w plikach do skopiowania.

>[!IMPORTANT]
>W przypadku korzystania z bazy danych do ładowania Data Lake Storage Gen2 do SQL Data Warehouse przy użyciu uwierzytelniania tożsamości zarządzanej na potrzeby Data Lake Storage Gen2 należy również wykonać kroki 1 i 2 w [tych wskazówkach](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) do 1) zarejestrować serwer SQL Database przy użyciu Azure Active Directory (Azure AD) i 2) Przypisz rolę współautor danych obiektów blob magazynu do serwera SQL Database; pozostałe są obsługiwane przez Data Factory. Jeśli Data Lake Storage Gen2 jest skonfigurowany za pomocą punktu końcowego Virtual Network platformy Azure, aby można było załadować z niego dane, należy użyć uwierzytelniania tożsamości zarządzanej zgodnie z wymaganiami firmy Base.

Te właściwości są obsługiwane dla połączonej usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **AzureBlobFS**. |Yes |
| url | Punkt końcowy dla Data Lake Storage Gen2 ze wzorcem `https://<accountname>.dfs.core.windows.net`. | Tak |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Jeśli magazyn danych znajduje się w sieci prywatnej, możesz użyć środowiska Azure Integration Runtime lub własnego środowiska Integration Runtime. Jeśli nie zostanie określony, zostanie użyta domyślna usługa Azure Integration Runtime. |Nie |

**Przykład:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [zestawy danych](concepts-datasets-linked-services.md).

- W przypadku **Parquet, rozdzielany tekst i format binarny**, zapoznaj się z sekcją [Parquet, rozdzielaną tekstem i binarnym zestawem danych](#format-based-dataset) .
- W przypadku innych formatów, takich jak **Orc/Avro/JSON**, zapoznaj się z sekcją [innego formatu zestawu danych](#other-format-dataset) .

### <a name="format-based-dataset"></a>Parquet, rozdzielany tekst i binarny zestaw danych

Aby skopiować dane do i z **Parquet, rozdzielonego tekstu lub formatu binarnego**, zapoznaj się z [formatem Parquet](format-parquet.md), [rozdzielanym formatem tekstowym](format-delimited-text.md) i artykułem [formatu binarnego](format-binary.md) w oparciu o zestaw danych oparty na formacie i obsługiwane ustawienia. Następujące właściwości są obsługiwane dla Data Lake Storage Gen2 w obszarze `location` ustawienia w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagane |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Właściwość `location` typu w elemencie DataSet musi być ustawiona na **AzureBlobFSLocation**. | Tak      |
| fileSystem | Nazwa systemu plików Data Lake Storage Gen2.                              | Nie       |
| folderPath | Ścieżka do folderu w danym systemie plików. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania folderów, pomiń to ustawienie i określ je w ustawieniach źródła działania. | Nie       |
| fileName   | Nazwa pliku w ramach danego systemu plików i folderPath. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania plików, pomiń to ustawienie i określ je w ustawieniach źródła działania. | Nie       |

> [!NOTE]
> Zestaw danych typu **AzureBlobFSFile** z Parquet lub formatem tekstowym wymienionym w poniższej sekcji jest nadal obsługiwany w przypadku działania kopiowania, wyszukiwania lub GetMetadata w celu zapewnienia zgodności z poprzednimi wersjami. Ale nie współpracuje z funkcją przepływu danych mapowania. Zalecamy użycie nowego modelu do przechodzenia do przodu. Interfejs użytkownika tworzenia Data Factory generuje te nowe typy.

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Inny zestaw danych formatu

Aby skopiować dane do i z Data Lake Storage Gen2 w **formacie Orc/Avro/JSON**, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa **AzureBlobFSFile**. |Yes |
| folderPath | Ścieżka do folderu w Data Lake Storage Gen2. Jeśli nie zostanie określony, wskazuje katalog główny. <br/><br/>Filtr symboli wieloznacznych jest obsługiwany. Dozwolone symbole wieloznaczne `*` to (dopasowuje zero lub więcej znaków `?` ) i (dopasowuje zero lub pojedynczy znak). Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub że ten znak ucieczki jest wewnątrz. <br/><br/>Przykłady: system plików/folder/. Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). |Nie |
| fileName | Nazwa lub filtr symboli wieloznacznych dla plików pod określonym "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie pliki w folderze. <br/><br/>W przypadku filtru, dozwolone symbole wieloznaczne `*` to (dopasowuje zero lub więcej znaków `?` ) i (dopasowuje zero lub pojedynczy znak).<br/>— Przykład 1: `"fileName": "*.csv"`<br/>— Przykład 2: `"fileName": "???20180427.txt"`<br/>Użyj `^` do ucieczki, jeśli rzeczywista nazwa pliku ma symbol wieloznaczny lub że ten znak ucieczki jest wewnątrz.<br/><br/>Jeśli nazwa pliku nie została określona dla wyjściowego zestawu danych, a **preserveHierarchy** nie jest określona w ujścia aktywności, działanie kopiowania automatycznie generuje nazwę pliku z następującym wzorcem: "*Dane. [identyfikator uruchomienia przebiegu działania]. [GUID if FlattenHierarchy]. [Format, jeśli skonfigurowano]. [kompresja, jeśli jest skonfigurowana]* ", na przykład" Data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz ". Jeśli skopiujesz ze źródła tabelarycznego przy użyciu nazwy tabeli zamiast zapytania, wzorzec nazwy to " *[nazwa tabeli]. [ Format]. [kompresja, jeśli jest skonfigurowana]* ", na przykład" MyTable. csv ". |Nie |
| modifiedDatetimeStart | Filtr plików oparty na ostatniej modyfikacji atrybutu. Pliki są wybierane, jeśli czas ostatniej modyfikacji mieści się w przedziale `modifiedDatetimeStart` czasu `modifiedDatetimeEnd`między i. Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Aby można było wykonać filtr plików z ogromną ilością plików, należy włączyć ogólną wydajność przenoszenia danych. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie jest stosowany do zestawu danych. Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime, są zaznaczone.| Nie |
| modifiedDatetimeEnd | Filtr plików oparty na ostatniej modyfikacji atrybutu. Pliki są wybierane, jeśli czas ostatniej modyfikacji mieści się w przedziale `modifiedDatetimeStart` czasu `modifiedDatetimeEnd`między i. Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Aby można było wykonać filtr plików z ogromną ilością plików, należy włączyć ogólną wydajność przenoszenia danych. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie jest stosowany do zestawu danych. Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime, są zaznaczone.| Nie |
| format | Jeśli chcesz skopiować pliki się między magazynami oparte na plikach (kopia binarna), Pomiń sekcji format w definicji zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować lub generować pliki o określonym formacie, obsługiwane są następujące typy formatu plików: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**i **ParquetFormat**. Ustaw **typu** właściwości **format** do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz sekcję [Format tekstu](supported-file-formats-and-compression-codecs.md#text-format), [Format JSON](supported-file-formats-and-compression-codecs.md#json-format), [Format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs.md#orc-format)i [Parquet format](supported-file-formats-and-compression-codecs.md#parquet-format) . |Brak (tylko w przypadku scenariusza kopia binarna) |
| compression | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Obsługiwane typy to **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**.<br/>Są obsługiwane poziomy **optymalna** i **najszybciej**. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ **folderPath** tylko.<br>Aby skopiować pojedynczy plik o podanej nazwie, należy określić **folderPath** z częścią folderu i nazwą pliku o nazwie.<br>Aby skopiować podzestaw plików w folderze, należy określić **folderPath** z częścią folderu i **nazwą pliku** z filtrem symboli wieloznacznych. 

**Przykład:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [konfiguracje działania kopiowania](copy-activity-overview.md#configuration) oraz [potoki i działania](concepts-pipelines-activities.md). Ta sekcja zawiera listę właściwości obsługiwanych przez Data Lake Storage Gen2 źródła i ujścia.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 jako typ źródła

- Aby skopiować z **Parquet, rozdzielany tekstem i binarny format**, zapoznaj się z sekcją [Parquet, rozdzielaną tekstem i źródłem formatu binarnego](#format-based-source) .
- Aby skopiować dane z innych formatów, takich jak **Orc/Avro/JSON**, zapoznaj się z sekcją [inne źródło formatu](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, rozdzielone Źródło tekstu i format binarny

Aby skopiować dane z **Parquet, rozdzielonego tekstu lub formatu binarnego**, zapoznaj się z [formatem Parquet](format-parquet.md), [rozdzielanym formatem tekstowym](format-delimited-text.md) i artykułem [formatu binarnego](format-binary.md) w oparciu o źródło działania kopiowania opartego na formacie i obsługiwane ustawienia. Następujące właściwości są obsługiwane dla Data Lake Storage Gen2 w obszarze `storeSettings` ustawienia w źródle kopiowania opartego na formacie:

| Właściwość                 | Opis                                                  | Wymagane                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **AzureBlobFSReadSetting**. | Yes                                           |
| recursive                | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. Gdy wartość cykliczna jest ustawiona na wartość true, a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia. Dozwolone wartości to **true** (ustawienie domyślne) i **false**. | Nie                                            |
| wildcardFolderPath       | Ścieżka folderu z symbolami wieloznacznymi w danym systemie plików skonfigurowanym w zestawie danych do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne `*` to (dopasowuje zero lub więcej znaków `?` ) i (dopasowuje zero lub pojedynczy znak). Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Nie                                            |
| wildcardFileName         | Nazwa pliku z symbolami wieloznacznymi w danym systemie plików + folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne `*` to (dopasowuje zero lub więcej znaków `?` ) i (dopasowuje zero lub pojedynczy znak). Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Tak, `fileName` Jeśli nie jest określony w zestawie danych |
| modifiedDatetimeStart    | Filtr plików oparty na ostatniej modyfikacji atrybutu. Pliki są wybierane, jeśli czas ostatniej modyfikacji mieści się w przedziale `modifiedDatetimeStart` czasu `modifiedDatetimeEnd`między i. Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie jest stosowany do zestawu danych. Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` jest wartością null, oznacza, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` jest wartością null, oznacza, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime, są zaznaczone. | Nie                                            |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                            |
| maxConcurrentConnections | Liczba połączeń do współbieżnego połączenia z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie                                            |

> [!NOTE]
> W przypadku formatu tekstu Parquet lub Unlimited, Źródło działania kopiowania typu **AzureBlobFSSource** wymienione w poniższej sekcji jest nadal obsługiwane w celu zapewnienia zgodności z poprzednimi wersjami. Zalecamy użycie nowego modelu do przechodzenia do przodu. Interfejs użytkownika tworzenia Data Factory generuje te nowe typy.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
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
                    "type": "AzureBlobFSReadSetting",
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

#### <a name="other-format-source"></a>Inne źródło formatowania

Aby skopiować dane z Data Lake Storage Gen2 w **formacie Orc/Avro/JSON**, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być równa **AzureBlobFSSource**. |Yes |
| recursive | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. Gdy wartość cykliczna jest ustawiona na wartość true, a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia.<br/>Dozwolone wartości to **true** (ustawienie domyślne) i **false**. | Nie |
| maxConcurrentConnections | Liczba połączeń do równoczesnego połączenia z magazynem danych. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake magazynu Gen2 jako typ ujścia

- Aby skopiować do **Parquet, rozdzielonego tekstu lub formatu binarnego**, zapoznaj się z sekcją [Parquet, rozdzielaną tekstem i ujścia formatu binarnego](#format-based-sink) .
- Aby skopiować do innych formatów, takich jak **Orc/Avro/JSON**, zobacz sekcję [inne formatowanie ujścia](#other-format-sink) .

#### <a name="format-based-sink"></a>Parquet, rozdzielony ujścia tekstu i formatu binarnego

Aby skopiować dane do **Parquet, rozdzielonego tekstu lub formatu binarnego**, zapoznaj się z [formatem Parquet](format-parquet.md), rozdzielonym [formatem tekstowym](format-delimited-text.md) i [formatem binarnym](format-binary.md) w artykule dotyczącym ujścia działania kopiowania opartego na formacie i obsługiwanych ustawień. Następujące właściwości są obsługiwane dla Data Lake Storage Gen2 w obszarze `storeSettings` ustawienia w ujściach kopiowania opartych na formacie:

| Właściwość                 | Opis                                                  | Wymagane |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **AzureBlobFSWriteSetting**. | Tak      |
| copyBehavior             | Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest pliki z magazynu danych oparte na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (domyślnie)</b>: Zachowuje hierarchię plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><b>-FlattenHierarchy</b>: Wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają nazwy wygenerowany automatycznie. <br/><b>-MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określony, nazwa pliku scalonego jest określona nazwa. W przeciwnym razie jest automatycznie wygenerowana nazwa pliku. | Nie       |
| maxConcurrentConnections | Liczba połączeń do równoczesnego połączenia z magazynem danych. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie       |

> [!NOTE]
> W odniesieniu do formatu tekstu Parquet lub Unlimited, obiekt ujścia działania kopiowania typu **AzureBlobFSSink** , określony w poniższej sekcji, jest nadal obsługiwany jako w celu zapewnienia zgodności z poprzednimi wersjami. Zalecamy użycie nowego modelu do przechodzenia do przodu. Interfejs użytkownika tworzenia Data Factory generuje te nowe typy.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
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
                    "type": "AzureBlobFSWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Inny ujścia formatu

Aby skopiować dane do Data Lake Storage Gen2 w **formacie Orc/Avro/JSON**, w sekcji **ujścia** są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type ujścia działania kopiowania musi być równa **AzureBlobFSSink**. |Yes |
| copyBehavior | Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest pliki z magazynu danych oparte na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (domyślnie)</b>: Zachowuje hierarchię plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><b>-FlattenHierarchy</b>: Wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają nazwy wygenerowany automatycznie. <br/><b>-MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określony, nazwa pliku scalonego jest określona nazwa. W przeciwnym razie jest automatycznie wygenerowana nazwa pliku. | Nie |
| maxConcurrentConnections | Liczba połączeń do równoczesnego połączenia z magazynem danych. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Przykłady filtrów folderów i plików

W tej sekcji opisano skutki zachowania ścieżki folderu i nazwy pliku z filtrami symboli wieloznacznych.

| folderPath | fileName | recursive | Źródłowa Struktura folderu i wynik filtru (pliki **pogrubione** są pobierane)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Puste, Użyj domyślnego) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Puste, Użyj domyślnego) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Kilka przykładów rekurencyjnych i copyBehavior

W tej sekcji opisano zachowanie operacji kopiowania dla różnych kombinacji wartości cyklicznych i copyBehavior.

| recursive | copyBehavior | Źródło struktury folderów | Wynikowy docelowej |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Element docelowy Folder1 jest tworzony z tą samą strukturą co Źródło:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 zawartości są scalane w jeden plik o nazwie pliku wygenerowany automatycznie. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 File3, File4 i File5 nie są pobierane. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File2<br/><br/>Subfolder1 File3, File4 i File5 nie są pobierane. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 zawartości są scalane w jeden plik o nazwie pliku wygenerowany automatycznie. automatycznie wygenerowana nazwa File1<br/><br/>Subfolder1 File3, File4 i File5 nie są pobierane. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Zachowaj listy ACL z Data Lake Storage Gen1

>[!TIP]
>Aby w ogólny sposób skopiować dane z Azure Data Lake Storage Gen1 do Gen2, zobacz artykuł [Kopiowanie danych z Azure Data Lake Storage Gen1 do Gen2 z Azure Data Factoryami](load-azure-data-lake-storage-gen2-from-gen1.md) w celu uzyskania wskazówek i najlepszych rozwiązań.

Podczas kopiowania plików z Azure Data Lake Storage Gen1 do Gen2, można wybrać opcję zachowania list kontroli dostępu POSIX (ACL) wraz z danymi. Aby uzyskać więcej informacji na temat kontroli dostępu, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) i [Kontrola dostępu w programie Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

Następujące typy list ACL można zachować przy użyciu działania kopiowania Azure Data Factory. Można wybrać jeden lub więcej typów:

- **LISTA ACL**: Kopiowanie i zachowywanie list kontroli dostępu POSIX dla plików i katalogów. Kopiuje wszystkie istniejące listy ACL ze źródła do ujścia. 
- **Właściciel**: Kopiowanie i zachowywanie użytkownika będącego właścicielem plików i katalogów. Wymagany jest dostęp administratora do Data Lake Storage Gen2 ujścia.
- **Grupa**: Skopiuj i Zachowaj grupę będącą właścicielem plików i katalogów. Dostęp przez administratora do Data Lake Storage Gen2 ujścia lub użytkownika będącego właścicielem (jeśli jest również członkiem grupy docelowej) jest wymagane.

Jeśli wybierzesz opcję kopiowania z folderu, Data Factory replikuje listy ACL dla danego folderu oraz plików i katalogów w tym folderze, jeśli `recursive` jest ustawiona na true. W przypadku wybrania kopiowania z jednego pliku listy ACL w tym pliku zostaną skopiowane.

>[!IMPORTANT]
>Po wybraniu opcji zachowania list kontroli dostępu należy się upewnić, że przyznano wystarczające uprawnienia, aby Data Factory działały na koncie ujścia Data Lake Storage Gen2. Na przykład Użyj uwierzytelniania klucza konta lub Przypisz rolę właściciela danych obiektu blob magazynu do nazwy głównej usługi lub tożsamości zarządzanej.

Podczas konfigurowania źródła jako Data Lake Storage Gen1 przy użyciu opcji kopiowania binarnego lub formatu binarnego i ujścia jako Data Lake Storage Gen2 z opcją kopiowania binarnego lub formatem binarnym można znaleźć opcję **Zachowaj** na stronie **ustawień narzędzia kopiowanie danych** lub w Karta**Ustawienia** **działania** > kopiowania umożliwia tworzenie działań.

![Data Lake Storage Gen1 zachować listy ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Oto przykład konfiguracji JSON (zobacz `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Dowiedz się więcej o [transformacji źródłowej](data-flow-source.md) i [transformacji ujścia](data-flow-sink.md) w funkcji przepływu danych mapowania.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
