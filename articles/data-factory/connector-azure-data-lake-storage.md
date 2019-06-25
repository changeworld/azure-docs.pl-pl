---
title: Kopiowanie danych do i z usługi Azure Data Lake Storage Gen2 przy użyciu usługi fabryka danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane do i z usługi Azure Data Lake Storage Gen2 przy użyciu usługi Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: jingwang
ms.openlocfilehash: 536d7a572eddc2cf75f6ce135c3cd4f4f2635416
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203300"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Kopiowanie danych do i z usługi Azure Data Lake Storage Gen2 przy użyciu usługi Azure Data Factory

Azure Data Lake magazynu Gen2 to zestaw funkcji przeznaczonych do analizy danych big data, wbudowane w [usługi Azure Blob storage](../storage/blobs/storage-blobs-introduction.md). Służy on do połączenia interfejsem z danych za pomocą obu paradygmatów magazynu plików, jak systemu i obiekt.

W tym artykule opisano sposób kopiowania danych do i z usługi Azure Data Lake Storage Gen2. Aby dowiedzieć się więcej na temat usługi Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik usługi Azure Data Lake Storage Gen2 jest obsługiwane w przypadku następujących działań:

- [Działanie kopiowania, które](copy-activity-overview.md) z [obsługiwanych macierzy źródła lub ujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)

W szczególności za pomocą tego łącznika możesz wykonywać następujące czynności:

- Kopiowanie danych przy użyciu klucza konta, nazwa główna usługi lub zarządzanych tożsamości do uwierzytelnienia zasobów platformy Azure.
- Kopiowanie plików to przeanalizować lub generowanie plików za pomocą [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md).

>[!TIP]
>Jeśli włączysz hierarchicznej przestrzeni nazw, obecnie nie ma żadnych współdziałanie operacji między obiektem Blob i interfejsy API usługi Data Lake Storage Gen2. Jeśli napotkasz błąd "ErrorCode = FilesystemNotFound" z komunikatem "nie istnieje określony system plików", jest to spowodowane przez system plików określonego obiektu sink, który został utworzony za pomocą interfejsu API obiektu Blob zamiast interfejsu API usługi Data Lake Storage Gen2 gdzie indziej. Aby rozwiązać ten problem, należy określić nowy system plików o nazwie, która nie istnieje jako nazwa kontenera obiektów Blob. Następnie Data Factory automatycznie tworzy ten system plików podczas kopiowania danych.

>[!NOTE]
>Po włączeniu **dozwolonych zaufanych usług firmy Microsoft dostęp do tego konta magazynu** opcję w ustawieniach zapory usługi Azure Storage, Azure integration runtime nie nawiązać połączenie Data Lake Storage Gen2 i pokazuje błąd "niedozwolone". Komunikat o błędzie pojawia się, ponieważ fabryka danych nie jest traktowane jako zaufane usługi firmy Microsoft. Za pomocą własnego środowiska integration runtime, aby połączyć je w zamian.

## <a name="get-started"></a>Rozpoczęcie pracy

>[!TIP]
>Aby uzyskać szczegółowe omówienie sposobu korzystania z łącznika usługi Data Lake Storage Gen2, zobacz [ładowanie danych do usługi Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają informacje na temat właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Łącznik usługi Azure Data Lake Storage Gen2 obsługuje następujące typy uwierzytelniania. Znajduje się w odpowiednich sekcjach, aby uzyskać szczegółowe informacje:

- [Uwierzytelnianie za pomocą klucza konta](#account-key-authentication)
- [Uwierzytelnianie jednostki usługi](#service-principal-authentication)
- [Zarządzanych tożsamości do uwierzytelniania zasobów platformy Azure](#managed-identity)

>[!NOTE]
>Ładowanie danych do usługi SQL Data Warehouse, jeśli źródło Data Lake Storage Gen2 jest skonfigurowana z punktu końcowego sieci wirtualnej za pomocą programu PolyBase, należy użyć uwierzytelniania tożsamości zarządzanej zgodnie z wymaganiami programu PolyBase. Zobacz [uwierzytelniania tożsamości zarządzanej](#managed-identity) sekcję więcej wymagania wstępne dotyczące konfiguracji.

### <a name="account-key-authentication"></a>Uwierzytelnianie za pomocą klucza konta

Aby użyć uwierzytelniania klucza konta magazynu, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **AzureBlobFS**. |Yes |
| url | Punkt końcowy dla Data Lake Storage Gen2 o strukturze `https://<accountname>.dfs.core.windows.net`. | Yes |
| accountKey | Klucz konta Data Lake Storage Gen2. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Środowisko uruchomieniowe integracji platformy Azure lub własnego środowiska integration runtime można użyć, jeśli magazyn danych znajduje się w sieci prywatnej. Jeśli ta właściwość nie jest określona, domyślne środowisko uruchomieniowe integracji platformy Azure jest używana. |Nie |

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

1. Zarejestruj jednostki aplikacji w usłudze Azure Active Directory (Azure AD), wykonując kroki opisane w [Zarejestruj swoją aplikację z dzierżawy usługi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Zanotuj następujące wartości, które służą do definiowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Przyznaj odpowiednie uprawnienia jednostki usługi. Dowiedz się więcej na temat działania uprawnień w Data Lake Storage Gen2 z [listy kontroli dostępu do plików i katalogów](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Jako źródło**: W Eksploratorze usługi Storage, co najmniej udzielić **Execute** uprawnień, począwszy od systemu plików źródłowych, wraz z **odczytu** uprawnień do plików do skopiowania. Alternatywnie kontroli dostępu (IAM), można nadać co najmniej **czytnik danych obiektu Blob magazynu** roli.
    - **Jako obiekt sink**: W Eksploratorze usługi Storage, co najmniej udzielić **Execute** uprawnień, począwszy od systemu plików ujścia, wraz z **zapisu** uprawnień dla folderu ujścia. Alternatywnie kontroli dostępu (IAM), można nadać co najmniej **Współautor danych obiektu Blob magazynu** roli.

>[!NOTE]
>Do listy folderów uruchamianie z poziomu konta lub aby przetestować połączenie, należy ustawić uprawnienia jednostki usługi, zostanie im przyznany do **konta magazynu z uprawnieniami "Czytnik danych obiektu Blob magazynu" w IAM**. Jest to wartość true, gdy:
>- **Narzędzia do kopiowania danych** do potoku kopiowania autora.
>- **Interfejs użytkownika usługi Data Factory** do testowania połączenia i przechodząc folderów podczas tworzenia. 
>Jeśli masz wątpliwości dotyczące udzielania uprawnień na poziomie konta, można pominąć połączenie testowe i ścieżka wejściowa ręcznie podczas tworzenia. Działanie kopiowania wciąż działa tak długo, jak nazwa główna usługi jest przyznawana z odpowiednimi uprawnieniami na pliki do skopiowania.

Te właściwości są obsługiwane w przypadku połączonej usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **AzureBlobFS**. |Yes |
| url | Punkt końcowy dla Data Lake Storage Gen2 o strukturze `https://<accountname>.dfs.core.windows.net`. | Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Yes |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako `SecureString` można bezpiecznie przechowywać w usłudze Data Factory. Można też [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| tenant | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Pobierz go przez umieszczenie nad nim kursora myszy w prawym górnym rogu witryny Azure portal. | Tak |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Środowisko uruchomieniowe integracji platformy Azure lub własnego środowiska integration runtime można użyć, jeśli magazyn danych znajduje się w sieci prywatnej. Jeśli nie zostanie określony, używane jest domyślne środowisko uruchomieniowe integracji Azure. |Nie |

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

Fabrykę danych mogą być skojarzone z [tożsamości zarządzanej dla zasobów platformy Azure](data-factory-service-identity.md), który reprezentuje tę fabrykę danych z konkretnych. Tej tożsamości zarządzanej służy bezpośrednio do uwierzytelniania Data Lake Storage Gen2, podobnie jak za pomocą jednostki usługi. Ta fabryka wyznaczonym umożliwia dostęp i kopiowanie danych do i z usługi Data Lake magazynu Gen2.

Aby użyć zarządzanych tożsamości do uwierzytelniania zasobów platformy Azure, wykonaj następujące kroki.

1. [Pobrać informacje o tożsamości usługi Data Factory zarządzać](data-factory-service-identity.md#retrieve-managed-identity) przez skopiowanie wartości **identyfikator aplikacji tożsamości usługi** wygenerowane wraz z fabryką.

2. Przyznaj uprawnienie odpowiednie tożsamość zarządzaną. Dowiedz się więcej na temat działania uprawnień w Data Lake Storage Gen2 z [listy kontroli dostępu do plików i katalogów](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Jako źródło**: W Eksploratorze usługi Storage, co najmniej udzielić **Execute** uprawnień, począwszy od systemu plików źródłowych, wraz z **odczytu** uprawnień do plików do skopiowania. Alternatywnie kontroli dostępu (IAM), można nadać co najmniej **czytnik danych obiektu Blob magazynu** roli.
    - **Jako obiekt sink**: W Eksploratorze usługi Storage, co najmniej udzielić **Execute** uprawnień, począwszy od systemu plików ujścia, wraz z **zapisu** uprawnień dla folderu ujścia. Alternatywnie kontroli dostępu (IAM), można nadać co najmniej **Współautor danych obiektu Blob magazynu** roli.

>[!NOTE]
>Do listy folderów uruchamianie z poziomu konta lub aby przetestować połączenie, musisz ustawić uprawnienie tożsamość zarządzaną, zostanie im przyznany do **konta magazynu z uprawnieniami "Czytnik danych obiektu Blob magazynu" w IAM**. Jest to wartość true, gdy:
>- **Narzędzia do kopiowania danych** do potoku kopiowania autora.
>- **Interfejs użytkownika usługi Data Factory** do testowania połączenia i przechodząc folderów podczas tworzenia. 
>Jeśli masz wątpliwości dotyczące udzielania uprawnień na poziomie konta, można pominąć połączenie testowe i ścieżka wejściowa ręcznie podczas tworzenia. Działanie kopiowania wciąż działa tak długo, jak tożsamość zarządzaną otrzymuje z odpowiednimi uprawnieniami na pliki do skopiowania.

>[!IMPORTANT]
>Jeśli używasz programu PolyBase do ładowania danych z Data Lake Storage Gen2 SQL Data Warehouse, w przypadku używania uwierzytelniania tożsamości zarządzanej dla Data Lake Storage Gen2, upewnij się, możesz również wykonać kroki 1 i 2 w [Niniejsze wskazówki](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) 1) Zarejestruj SQL Serwer bazy danych w usłudze Azure Active Directory (Azure AD) i 2) Współautor danych obiektu Blob magazynu, aby przypisać rolę serwera usługi SQL Database; pozostałe są obsługiwane przez usługę Data Factory. Jeśli usługi Data Lake magazynu Gen2 jest skonfigurowany z punktem końcowym usługi Azure Virtual Network, aby przy użyciu technologii PolyBase do ładowania danych z, należy użyć uwierzytelniania tożsamości zarządzanej zgodnie z wymaganiami programu PolyBase.

Te właściwości są obsługiwane w przypadku połączonej usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **AzureBlobFS**. |Yes |
| url | Punkt końcowy dla Data Lake Storage Gen2 o strukturze `https://<accountname>.dfs.core.windows.net`. | Tak |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Środowisko uruchomieniowe integracji platformy Azure lub własnego środowiska integration runtime można użyć, jeśli magazyn danych znajduje się w sieci prywatnej. Jeśli nie zostanie określony, używane jest domyślne środowisko uruchomieniowe integracji Azure. |Nie |

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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md).

- Parquet i format tekstu rozdzielanego, zobacz [zestawu danych formatu Parquet i tekst rozdzielany](#parquet-and-delimited-text-format-dataset) sekcji.
- W przypadku innych formatów, takich jak ORC, Avro, JSON lub format binarny, zobacz [innych zestawu danych w formacie](#other-format-dataset) sekcji.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet i zestaw danych, format tekstu rozdzielanego

Aby skopiować dane do i z Data Lake Storage Gen2 parquet lub format tekstu rozdzielanego, zobacz [formatu Parquet](format-parquet.md) i [format tekstu rozdzielanego](format-delimited-text.md) artykułów na format na podstawie zestawu danych i obsługiwane ustawienia. Następujące właściwości są obsługiwane w przypadku Data Lake Storage Gen2 w obszarze `location` ustawienia w zestawie danych na podstawie formatu:

| Właściwość   | Opis                                                  | Wymagane |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Właściwość type w obszarze `location` w zestawie danych musi być równa **AzureBlobFSLocation**. | Tak      |
| fileSystem | Nazwa systemu plików Data Lake Storage Gen2.                              | Nie       |
| folderPath | Ścieżka do folderu w danym systemie plików. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania folderów, pomiń to ustawienie i je określić w ustawieniach źródła działania. | Nie       |
| fileName   | Nazwa pliku w danym systemie plików + folderPath. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania plików, pomiń to ustawienie i je określić w ustawieniach źródła działania. | Nie       |

> [!NOTE]
> **AzureBlobFSFile** typ zestawu danych w formacie parquet lub wiadomości SMS, wymienione w poniższej sekcji nadal jest obsługiwany, ponieważ jest kopiowania, wyszukiwania i działaniu GetMetadata zgodności z poprzednimi wersjami. Ale nie działa z funkcją mapowanie przepływu danych. Firma Microsoft zaleca używanie tego nowego modelu, w przyszłości. Usługi Data Factory do tworzenia interfejsu użytkownika generuje te nowe typy.

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

### <a name="other-format-dataset"></a>Innym formacie zestawu danych

Aby skopiować dane do i z Data Lake Storage Gen2 ORC, Avro, JSON lub format binarny, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa **AzureBlobFSFile**. |Yes |
| folderPath | Ścieżka do folderu w Data Lake Storage Gen2. Jeśli nie zostanie określony, wskazuje katalog główny. <br/><br/>Filtr z symbolami wieloznacznymi jest obsługiwany. Dozwolone symbole wieloznaczne są `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak). Użyj `^` jako znak ucieczki czy nazwą rzeczywistego folderu zawiera symbol wieloznaczny ten znak ucieczki znajduje się wewnątrz. <br/><br/>Przykłady: system plików/folderów /. Zobacz więcej przykładów w [folderowi i plikowi Przykłady filtrów](#folder-and-file-filter-examples). |Nie |
| fileName | Filtr nazwy lub symboli wieloznacznych plików w określonym "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie pliki w folderze. <br/><br/>Dla filtru, są dozwolone symbole wieloznaczne `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak).<br/>— Przykład 1: `"fileName": "*.csv"`<br/>— Przykład 2: `"fileName": "???20180427.txt"`<br/>Użyj `^` jako znak ucieczki, czy Twoje rzeczywiste nazwy plików zawiera symbol wieloznaczny ten znak ucieczki znajduje się wewnątrz.<br/><br/>Kiedy dla wyjściowego zestawu danych nie jest określona nazwa pliku i **preserveHierarchy** nie został określony w ujściu działania, działanie kopiowania automatycznie generuje nazwę pliku z następującym wzorcem: "*Danych. [identyfikator GUID uruchamiania działania]. [Identyfikator GUID Jeśli FlattenHierarchy]. [format skonfigurowanie]. [kompresji, jeśli skonfigurowano]* ", na przykład"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". W przypadku kopiowania z tabelaryczne źródła przy użyciu nazwy tabeli zamiast zapytania wzorzec nazwy to " *[Nazwa tabeli]. [ format]. [kompresji, jeśli skonfigurowano]* ", na przykład"MyTable.csv". |Nie |
| modifiedDatetimeStart | Filtr plików, na podstawie atrybutu ostatniej modyfikacji. Pliki są zaznaczone, jeśli ich godzina ostatniej modyfikacji znajduje się w zakresie czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Ogólną wydajność przenoszenia danych ma wpływ włączenie tego ustawienia plik filtru z bardzo duże ilości plików. <br/><br/> Właściwości może być wartością NULL, co oznacza, że żaden filtr atrybutu pliku jest stosowany do zestawu danych. Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest większa niż lub równa wartości daty/godziny są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza, że zaznaczone są pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty/godziny.| Nie |
| modifiedDatetimeEnd | Filtr plików, na podstawie atrybutu ostatniej modyfikacji. Pliki są zaznaczone, jeśli ich godzina ostatniej modyfikacji znajduje się w zakresie czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Ogólną wydajność przenoszenia danych ma wpływ włączenie tego ustawienia plik filtru z bardzo duże ilości plików. <br/><br/> Właściwości może być wartością NULL, co oznacza, że żaden filtr atrybutu pliku jest stosowany do zestawu danych. Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest większa niż lub równa wartości daty/godziny są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza, że zaznaczone są pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty/godziny.| Nie |
| format | Jeśli chcesz skopiować pliki się między magazynami oparte na plikach (kopia binarna), Pomiń sekcji format w definicji zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować lub generowanie plików za pomocą określonego formatu, obsługiwane są następujące typy formatów plików: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, i **ParquetFormat**. Ustaw **typu** właściwości **format** do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstu](supported-file-formats-and-compression-codecs.md#text-format), [formatu JSON](supported-file-formats-and-compression-codecs.md#json-format), [Avro format](supported-file-formats-and-compression-codecs.md#avro-format), [ORC format](supported-file-formats-and-compression-codecs.md#orc-format), i [formatu Parquet ](supported-file-formats-and-compression-codecs.md#parquet-format) sekcje. |Brak (tylko w przypadku scenariusza kopia binarna) |
| compression | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Obsługiwane typy to **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**.<br/>Są obsługiwane poziomy **optymalna** i **najszybciej**. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ **folderPath** tylko.<br>Aby skopiować pojedynczy plik o określonej nazwie, należy określić **folderPath** z częścią folderu i **fileName** z nazwą pliku.<br>Aby skopiować podzestaw plików w folderze, podaj **folderPath** z częścią folderu i **fileName** za pomocą filtru symbolu wieloznacznego. 

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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań, zobacz [kopiowanie konfiguracji działań](copy-activity-overview.md#configuration) i [potoki i działania](concepts-pipelines-activities.md). Ta sekcja zawiera listę właściwości obsługiwanych przez Data Lake Storage Gen2 źródła i ujścia.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake magazynu Gen2 jako typ źródła

- Aby skopiować z parquet lub format tekstu rozdzielanego, zobacz [Parquet i źródło format tekstu rozdzielanego](#parquet-and-delimited-text-format-source) sekcji.
- Aby skopiować z innych formatów, takich jak ORC, Avro, JSON lub format binarny, zobacz [innego formatu źródła](#other-format-source) sekcji.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet i źródło format tekstu rozdzielanego

Aby skopiować dane z Data Lake Storage Gen2 parquet lub format tekstu rozdzielanego, zobacz [formatu Parquet](format-parquet.md) i [format tekstu rozdzielanego](format-delimited-text.md) artykuł na temat źródła działania kopiowania oparta na format i obsługiwane ustawienia. Następujące właściwości są obsługiwane w przypadku Data Lake Storage Gen2 w obszarze `storeSettings` ustawienia źródła kopiowania oparta na format:

| Właściwość                 | Opis                                                  | Wymagane                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Właściwość type w obszarze `storeSettings` musi być równa **AzureBlobFSReadSetting**. | Yes                                           |
| recursive                | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. Podczas rekursywnego jest ustawiona na wartość PRAWDA, a obiekt sink jest magazynem opartych na plikach, pusty folder lub podfolder nie jest kopiowany lub utworzono obiekt sink. Dozwolone wartości to **true** (ustawienie domyślne) i **false**. | Nie                                            |
| wildcardFolderPath       | Ścieżka folderu z symbolami wieloznacznymi w systemie dany plik skonfigurowany w zestawie danych do filtru źródła folderów. <br>Dozwolone symbole wieloznaczne są `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak). Użyj `^` jako znak ucieczki, jeśli symbol wieloznaczny lub ten znak ucieczki wewnątrz nazwą rzeczywistego folderu. <br>Zobacz więcej przykładów w [folderowi i plikowi Przykłady filtrów](#folder-and-file-filter-examples). | Nie                                            |
| wildcardFileName         | Nazwa pliku przy użyciu symboli wieloznacznych w danym systemie plików + folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne są `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak). Użyj `^` jako znak ucieczki, jeśli symbol wieloznaczny lub ten znak ucieczki wewnątrz nazwą rzeczywistego folderu. Zobacz więcej przykładów w [folderowi i plikowi Przykłady filtrów](#folder-and-file-filter-examples). | Tak, jeśli `fileName` nie jest określona w zestawie danych |
| modifiedDatetimeStart    | Filtr plików, na podstawie atrybutu ostatniej modyfikacji. Pliki są zaznaczone, jeśli ich godzina ostatniej modyfikacji znajduje się w zakresie czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br> Właściwości może być wartością NULL, co oznacza, że żaden filtr atrybutu pliku jest stosowane do zestawu danych. Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza to, że pliki, których ostatniej modyfikacji atrybutu jest większa niż lub równa wartości daty/godziny są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, że zaznaczone są pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty/godziny. | Nie                                            |
| modifiedDatetimeEnd      | Wartość taka sama jak powyżej.                                               | Nie                                            |
| maxConcurrentConnections | Liczba nawiązywanie połączeń magazynu magazynu jednocześnie. Należy określić tylko wtedy, gdy chcesz ograniczyć liczby jednoczesnych połączeń z magazynem danych. | Nie                                            |

> [!NOTE]
> Parquet lub format tekstu rozdzielanego **AzureBlobFSSource** źródło działania kopiowania typów wymienionych w poniższej sekcji nadal jest obsługiwany, ponieważ jest zgodności z poprzednimi wersjami. Firma Microsoft zaleca używanie tego nowego modelu, w przyszłości. Usługi Data Factory do tworzenia interfejsu użytkownika generuje te nowe typy.

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

#### <a name="other-format-source"></a>Inne źródła formatu

Aby skopiować dane z Data Lake Storage Gen2 ORC, Avro, JSON lub format binarny, następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być równa **AzureBlobFSSource**. |Yes |
| recursive | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. Podczas rekursywnego jest ustawiona na wartość PRAWDA, a obiekt sink jest magazynem opartych na plikach, pusty folder lub podfolder nie jest kopiowany lub utworzono obiekt sink.<br/>Dozwolone wartości to **true** (ustawienie domyślne) i **false**. | Nie |
| maxConcurrentConnections | Liczba nawiązywanie połączeń z magazynem danych jednocześnie. Należy określić tylko wtedy, gdy chcesz ograniczyć liczby jednoczesnych połączeń z magazynem danych. | Nie |

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

- Aby skopiować parquet lub format tekstu rozdzielanego, zobacz [Parquet, jak i ujście format tekstu rozdzielanego](#parquet-and-delimited-text-format-sink) sekcji.
- Aby skopiować do innych formatów, takich jak ORC, Avro, JSON lub format binarny, zobacz [ujścia innego formatu](#other-format-sink) sekcji.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet, jak i ujście format tekstu rozdzielanego

Aby skopiować dane do Data Lake Storage Gen2 parquet lub format tekstu rozdzielanego, zobacz [formatu Parquet](format-parquet.md) i [format tekstu rozdzielanego](format-delimited-text.md) artykuły o ujścia działania kopiowania oparta na format i obsługiwane ustawienia. Następujące właściwości są obsługiwane w przypadku Data Lake Storage Gen2 w obszarze `storeSettings` ustawienia ujścia kopiowania oparta na format:

| Właściwość                 | Opis                                                  | Wymagane |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Właściwość type w obszarze `storeSettings` musi być równa **AzureBlobFSWriteSetting**. | Yes      |
| copyBehavior             | Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest pliki z magazynu danych oparte na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (ustawienie domyślne)</b>: Zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego, do folderu docelowego.<br/><b>-FlattenHierarchy</b>: Wszystkie pliki z folderu źródłowego znajdują się w pierwszy poziom folderu docelowego. Pliki docelowe mają nazwy wygenerowany automatycznie. <br/><b>-MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określony, nazwa pliku scalonego jest określona nazwa. W przeciwnym razie jest automatycznie wygenerowana nazwa pliku. | Nie       |
| maxConcurrentConnections | Liczba nawiązywanie połączeń z magazynem danych jednocześnie. Należy określić tylko wtedy, gdy chcesz ograniczyć liczby jednoczesnych połączeń z magazynem danych. | Nie       |

> [!NOTE]
> Parquet lub format tekstu rozdzielanego **AzureBlobFSSink** ujścia działania typu kopiowania z wymienionych w poniższej sekcji nadal jest obsługiwany, ponieważ jest zgodności z poprzednimi wersjami. Firma Microsoft zaleca używanie tego nowego modelu, w przyszłości. Usługi Data Factory do tworzenia interfejsu użytkownika generuje te nowe typy.

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

#### <a name="other-format-sink"></a>Ujścia innego formatu

Aby skopiować dane do Data Lake Storage Gen2 ORC, Avro, JSON lub format binarny, następujące właściwości są obsługiwane w **ujścia** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type ujścia działania kopiowania musi być równa **AzureBlobFSSink**. |Yes |
| copyBehavior | Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest pliki z magazynu danych oparte na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (ustawienie domyślne)</b>: Zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego, do folderu docelowego.<br/><b>-FlattenHierarchy</b>: Wszystkie pliki z folderu źródłowego znajdują się w pierwszy poziom folderu docelowego. Pliki docelowe mają nazwy wygenerowany automatycznie. <br/><b>-MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określony, nazwa pliku scalonego jest określona nazwa. W przeciwnym razie jest automatycznie wygenerowana nazwa pliku. | Nie |
| maxConcurrentConnections | Liczba nawiązywanie połączeń z magazynem danych jednocześnie. Należy określić tylko wtedy, gdy chcesz ograniczyć liczby jednoczesnych połączeń z magazynem danych. | Nie |

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

### <a name="folder-and-file-filter-examples"></a>Folder i plik Przykłady filtrów

W tej sekcji opisano wynikowe zachowania ścieżkę i nazwę folderu z filtrami symboli wieloznacznych.

| folderPath | fileName | recursive | Źródło folder struktury i filtrowanie wyników (pliki **bold** są pobierane)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Puste, użyta domyślna) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Puste, użyta domyślna) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Kilka przykładów rekurencyjnych i copyBehavior

W tej sekcji opisano wynikowe zachowania operacji kopiowania różne kombinacje wartości cyklicznych i copyBehavior.

| recursive | copyBehavior | Źródło struktury folderów | Wynikowy docelowej |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Element docelowy Folder1 jest tworzony przy użyciu tej samej struktury jako źródła:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + plik3 + File4 + File5 zawartości są scalane w jeden plik o nazwie pliku wygenerowany automatycznie. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa plik2<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 zawartości są scalane w jeden plik o nazwie pliku wygenerowany automatycznie. automatycznie wygenerowana nazwa Plik1<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Zachowaj listy ACL z Gen1 Lake magazynu danych

>[!TIP]
>Aby skopiować dane z usługi Azure Data Lake Storage Gen1 do Gen2 ogólnie rzecz biorąc, zobacz [kopiowanie danych z usługi Azure Data Lake Storage Gen1 do Gen2 za pomocą usługi Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) szczegółowe omówienie i najlepsze rozwiązania.

Podczas kopiowania plików z usługi Azure Data Lake Storage Gen1 do Gen2, można zachować POSIX listy kontroli dostępu (ACL) wraz z danymi. Aby uzyskać więcej informacji na temat kontroli dostępu, zobacz [kontrola dostępu w usługach Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) i [kontrola dostępu w usługach Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

Następujące typy list ACL może zostać zachowane przez za pomocą działania kopiowania w usłudze Azure Data Factory. Można wybrać jeden lub więcej typów:

- **LISTY ACL**: Skopiuj i Zachowaj list kontroli dostępu POSIX, plików i katalogów. Pełne istniejących list ACL są kopiowane ze źródła do ujścia. 
- **Właściciel**: Skopiuj i Zachowaj użytkownik będący właścicielem plików i katalogów. Wymagany jest dostęp administratora do ujścia Data Lake Storage Gen2.
- **Grupa**: Skopiuj i Zachowaj grupa będąca właścicielem plików i katalogów. Wymagany jest dostęp administratora do ujścia Data Lake Storage Gen2 lub użytkownik będący właścicielem (Jeśli użytkownik będący właścicielem jest również członkiem grupy docelowej).

Jeśli określisz, aby skopiować z folderu, Data Factory replikuje listy ACL dla danego folderu oraz plików i katalogów w nim, jeśli `recursive` jest ustawiona na wartość true. Jeśli wybierzesz opcję skopiowania pojedynczy plik, listy ACL dla tego pliku są kopiowane.

>[!IMPORTANT]
>Jeśli zdecydujesz się zachować list ACL, upewnij się, że możesz wysokiego przyznane wystarczające uprawnienia do usługi Data Factory działanie względem obiektu sink konta Data Lake Storage Gen2. Na przykład używają uwierzytelniania kluczem konta lub przypisanie roli właściciela danych obiektu Blob magazynu tożsamości podmiotu zabezpieczeń lub zarządzanej usługi.

Podczas konfigurowania źródła jako Data Lake Storage Gen1 z opcją kopia binarna lub format binarny i ujścia jako Data Lake Storage Gen2 z opcją kopia binarna lub format binarny, można znaleźć **zachować** opcja **kopiowania Ustawienia narzędzia danych** strony lub na **działania kopiowania** > **ustawienia** kartę na potrzeby tworzenia działania.

![Data Lake Storage Gen1 można zachować Gen2 listy ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Poniżej przedstawiono przykład konfiguracji JSON (zobacz `preserve`): 

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

Dowiedz się więcej o [źródła przekształcenia](data-flow-source.md) i [ujścia przekształcania](data-flow-sink.md) w funkcji mapowania przepływu danych.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
