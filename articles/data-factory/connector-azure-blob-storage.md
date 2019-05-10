---
title: Kopiowanie danych do i z usługi Azure Blob storage przy użyciu usługi fabryka danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z magazynów danych obsługiwanych źródłowych do magazynu obiektów Blob platformy Azure lub z magazynu obiektów Blob do magazynów danych obsługiwanych ujścia, za pomocą usługi Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 1ff20322f1d4f6024d4f41037ca18c327a0cc21f
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233185"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Kopiowanie danych do i z usługi Azure Blob storage za pomocą usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-azure-blob-connector.md)
> * [Bieżąca wersja](connector-azure-blob-storage.md)

W tym artykule opisano sposób kopiowania danych do i z usługi Azure Blob storage. Aby dowiedzieć się więcej na temat usługi Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik obiektu Blob platformy Azure jest obsługiwane dla następujących działań:

- [Działanie kopiowania, które](copy-activity-overview.md) z [obsługiwane źródło/ujście macierzy](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)

W szczególności ten łącznik magazynu obiektów Blob obsługuje:

- Kopiowanie obiektów blob z konta magazynu ogólnego przeznaczenia platformy Azure oraz magazynu gorących/chłodnych obiektów blob. 
- Kopiowanie obiektów blob przy użyciu klucza konta, sygnatury dostępu współdzielonego usługi tożsamości podmiotu zabezpieczeń lub zarządzanej usługi dla uwierzytelnień zasobów platformy Azure.
- Kopiowanie obiektów blob z bloku, Dołącz lub stronicowe obiekty BLOB i kopiowanie danych tylko blokowe obiekty BLOB.
- Kopiowanie obiektów blob jest analiza kodu lub generowania obiektów blob za pomocą [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Po włączeniu _"Zezwalaj na zaufane usługi firmy Microsoft dostęp do tego konta magazynu"_ opcję w ustawieniach zapory usługi Azure Storage, za pomocą środowiska Azure Integration Runtime do połączenia z magazynem obiektów Blob zakończy się niepowodzeniem z błąd "niedozwolone", ponieważ nie jest ADF traktowane jako zaufane usługi firmy Microsoft. Połącz za pośrednictwem środowiskiem Integration Runtime zamiast tego.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do magazynu obiektów Blob.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Łącznik usługi Azure Blob obsługuje następujące typy uwierzytelniania, zajrzyj do odpowiedniej sekcji na potrzeby szczegółów:

- [Uwierzytelnianie za pomocą klucza konta](#account-key-authentication)
- [Uwierzytelniania sygnatury dostępu współdzielonego](#shared-access-signature-authentication)
- [Uwierzytelnianie jednostki usługi](#service-principal-authentication)
- [Zarządzanych tożsamości do uwierzytelniania zasobów platformy Azure](#managed-identity)

>[!NOTE]
>HDInsights, Azure Machine Learning i Azure SQL Data Warehouse PolyBase obciążenia obsługują tylko uwierzytelnianie klucza konta magazynu obiektów Blob platformy Azure.

### <a name="account-key-authentication"></a>Uwierzytelnianie za pomocą klucza konta

Aby użyć uwierzytelniania klucza konta magazynu, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **usłudze Azure blob Storage** (zalecane) lub **AzureStorage** (zobacz uwagi poniżej). |Yes |
| connectionString | Określ informacje wymagane do połączenia z magazynem dla właściwości connectionString. <br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory. Możesz również umieścić klucz konta w usłudze Azure Key Vault i ściągania `accountKey` konfiguracji poza parametry połączenia. Zobacz poniższe przykłady i [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md) artykułu z bardziej szczegółowymi informacjami. |Yes |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

>[!NOTE]
>Jeśli używano "AzureStorage" typu połączonej usługi jest nadal obsługiwany jako — jest, gdy są zalecane, aby użyć tego nowego "usłudze Azure blob Storage" połączony typ usługi, w przyszłości.

**Przykład:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: przechowywanie klucza konta w usłudze Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>Uwierzytelniania sygnatury dostępu współdzielonego

Sygnatury dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Sygnatury dostępu współdzielonego umożliwia jest przyznanie klientowi ograniczonych uprawnień do obiektów na koncie magazynu przez wyznaczony czas. Nie masz udostępniania kluczy dostępu do Twojego konta. Sygnatura dostępu współdzielonego to identyfikator URI, który obejmuje jego parametry zapytań, wszystkie informacje niezbędne do uwierzytelnionego dostępu do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu przy użyciu sygnatury dostępu współdzielonego, klient musi tylko przekazanej sygnatury dostępu współdzielonego do odpowiedniego konstruktora lub metody. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [sygnatur dostępu współdzielonego: Informacje o modelu sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Usługa Data Factory obsługuje teraz zarówno **sygnatury dostępu współdzielonego usługi** i **sygnatury dostępu współdzielonego konta**. Aby uzyskać więcej informacji na temat tych dwóch typów i jak je utworzyć, zobacz [rodzaje sygnatur dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures).
>- W późniejszej konfiguracji zestawu danych ścieżka folderu jest ścieżka bezwzględna, wychodząc z poziomu kontenera. Należy skonfigurować jedną dostosowane do ścieżki z identyfikatora URI sygnatury dostępu Współdzielonego.

> [!TIP]
> Aby wygenerować sygnaturę dostępu współdzielonego usługi dla konta magazynu, można wykonać następujące polecenia programu PowerShell. Zastąp symbole zastępcze i udzielić wymaganych uprawnień.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Aby użyć uwierzytelniania sygnatury dostępu współdzielonego, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **usłudze Azure blob Storage** (zalecane) lub **AzureStorage** (zobacz uwagi poniżej). |Yes |
| sasUri | Określ identyfikatora URI sygnatury dostępu współdzielonego do zasobów magazynu, takich jak/z kontenera obiektów blob. <br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory. Możesz również umieścić tokenu sygnatury dostępu Współdzielonego w usłudze Azure Key Vault, aby wykorzystać automatyczne obracanie i usunąć część tokenu. Zobacz poniższe przykłady i [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md) artykułu z bardziej szczegółowymi informacjami. |Yes |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

>[!NOTE]
>Jeśli używano "AzureStorage" typu połączonej usługi jest nadal obsługiwany jako — jest, gdy są zalecane, aby użyć tego nowego "usłudze Azure blob Storage" połączony typ usługi, w przyszłości.

**Przykład:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<container>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: przechowywanie klucza konta w usłudze Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<container>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Podczas tworzenia identyfikatora URI sygnatury dostępu współdzielonego, należy wziąć pod uwagę następujące kwestie:

- Ustaw uprawnienia odczytu/zapisu odpowiednie obiekty w zależności od sposobu połączonej usługi (Odczyt, zapis, Odczyt/zapis) jest używany w fabryce danych.
- Ustaw **czas wygaśnięcia** odpowiednio. Upewnij się, że dostęp do obiektów nie wygasa w aktywnym okresie potoku.
- Identyfikator URI powinien zostać utworzony w prawo kontenera/opartymi na obiektach blob na potrzeby. Identyfikator URI sygnatury dostępu współdzielonego do obiektu blob umożliwia Data Factory w celu dostępu do tego konkretnego obiektu blob. Identyfikator URI sygnatury dostępu współdzielonego kontenera magazynu obiektów Blob umożliwia usługi Data Factory do iteracji przez obiekty BLOB w kontenerze. Aby zapewnić dostęp do później więcej lub mniej obiektów lub zaktualizować identyfikatora URI sygnatury dostępu współdzielonego, pamiętaj, aby zaktualizować połączoną usługę o nowy identyfikator URI.

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Uwierzytelnianie jednostki usługi Azure Storage ogólnie rzecz biorąc, można znaleźć [uwierzytelniania dostępu do usługi Azure Storage przy użyciu usługi Azure Active Directory](../storage/common/storage-auth-aad.md).

Aby użyć uwierzytelniania jednostki usługi, wykonaj następujące kroki:

1. Zarejestruj jednostki aplikacji w usłudze Azure Active Directory (Azure AD), postępując zgodnie z [Zarejestruj swoją aplikację z dzierżawy usługi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Zanotuj następujące wartości, które służą do definiowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Przyznaj usługi głównej odpowiednie uprawnienia w usłudze Azure Blob storage. Zapoznaj się [Zarządzaj prawa dostępu do danych usługi Azure Storage za pomocą funkcji RBAC](../storage/common/storage-auth-aad-rbac.md) z bardziej szczegółowymi informacjami na rolach.

    - **Jako źródło**, sterowanie dostępu (IAM), co najmniej udzielić **czytnik danych obiektu Blob magazynu** roli.
    - **Jako obiekt sink**, sterowanie dostępu (IAM), co najmniej udzielić **Współautor danych obiektu Blob magazynu** roli.

Te właściwości są obsługiwane w przypadku obiektów Blob platformy Azure połączonej usługi storage:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **usłudze Azure blob Storage**. |Yes |
| serviceEndpoint | Określ punkt końcowy usługi dla magazynu obiektów Blob platformy Azure przy użyciu wzorca `https://<accountName>.blob.core.windows.net/`. |Yes |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Yes |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| tenant | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Pobierz go przez umieszczenie nad nim kursora myszy w prawym górnym rogu witryny Azure Portal. | Yes |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

>[!NOTE]
>Usługa uwierzytelniania podmiotu zabezpieczeń jest obsługiwana tylko przez typ "Usłudze Azure blob Storage" połączonej usługi, ale nie poprzedniej "AzureStorage" typu połączonej usługi.

**Przykład:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
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

Fabrykę danych mogą być skojarzone z [tożsamości zarządzanej dla zasobów platformy Azure](data-factory-service-identity.md), który reprezentuje tę fabrykę danych z konkretnych. Ta tożsamość zarządzaną służy bezpośrednio dla uwierzytelniania magazynu obiektów Blob, podobnie jak za pomocą jednostki usługi. Umożliwia ona tej fabryki wyznaczonym dostęp i kopiowanie danych z i do usługi Blob storage.

Zapoznaj się [uwierzytelniania dostępu do usługi Azure Storage przy użyciu usługi Azure Active Directory](../storage/common/storage-auth-aad.md) uwierzytelniania usługi Azure Storage ogólnego. Aby użyć zarządzanych tożsamości do uwierzytelniania zasobów platformy Azure, wykonaj następujące kroki:

1. [Pobieranie informacji o tożsamości zarządzanych fabryki danych](data-factory-service-identity.md#retrieve-managed-identity) przez skopiowanie wartości "Identyfikator aplikacji tożsamości usługi" wygenerowane wraz z fabryką.

2. Przyznaj odpowiednie uprawnienia tożsamość zarządzaną w usłudze Azure Blob storage. Zapoznaj się [Zarządzaj prawa dostępu do danych usługi Azure Storage za pomocą funkcji RBAC](../storage/common/storage-auth-aad-rbac.md) z bardziej szczegółowymi informacjami na rolach.

    - **Jako źródło**, sterowanie dostępu (IAM), co najmniej udzielić **czytnik danych obiektu Blob magazynu** roli.
    - **Jako obiekt sink**, sterowanie dostępu (IAM), co najmniej udzielić **Współautor danych obiektu Blob magazynu** roli.

Te właściwości są obsługiwane w przypadku obiektów Blob platformy Azure połączonej usługi storage:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **usłudze Azure blob Storage**. |Yes |
| serviceEndpoint | Określ punkt końcowy usługi dla magazynu obiektów Blob platformy Azure przy użyciu wzorca `https://<accountName>.blob.core.windows.net/`. |Yes |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

> [!NOTE]
> Zarządzanych tożsamości dla zasobów platformy Azure, uwierzytelnianie jest obsługiwana tylko przez "Usłudze Azure blob Storage" typu połączonej usługi, ale nie poprzedniej "AzureStorage" typu połączonej usługi. 

**Przykład:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
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

- Dla **Parquet i format tekstu rozdzielanego**, można znaleźć [zestawu danych formatu Parquet i tekst rozdzielany](#parquet-and-delimited-text-format-dataset) sekcji.
- Dla innych formatów, takich jak **format ORC/Avro/JSON/dane binarne**, można znaleźć [innych zestawu danych w formacie](#other-format-dataset) sekcji.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet i zestaw danych, format tekstu rozdzielanego

Aby skopiować dane do i z magazynu obiektów Blob w Parquet lub format tekstu rozdzielanego, zapoznaj się [formatu Parquet](format-parquet.md) i [format tekstu rozdzielanego](format-delimited-text.md) artykuł na temat formatu na podstawie zestawu danych i obsługiwane ustawienia. Następujące właściwości są obsługiwane w przypadku obiektów Blob platformy Azure w ramach `location` ustawienia w formacie na podstawie zestawu danych:

| Właściwość   | Opis                                                  | Wymagane |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Właściwości typu lokalizacji zestawu danych musi być równa **AzureBlobStorageLocation**. | Yes      |
| container  | Kontener obiektów blob.                                          | Yes      |
| folderPath | Ścieżka do folderu, w ramach danego kontenera. Jeśli chcesz używać symboli wieloznacznych, do folderu filtru, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |
| fileName   | Nazwa pliku pod podanym kontenerem + folderPath. Jeśli chcesz użyć symboli wieloznacznych, aby odfiltrować pliki, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |

> [!NOTE]
>
> **AzureBlob** typ zestawu danych w formacie Parquet/tekstu opisane w następnej sekcji nadal jest obsługiwany jako — jest dla działania kopiowania/wyszukiwania/GetMetadata dla zgodności z poprzednimi wersjami, ale nie działa w przypadku mapowania przepływu danych. Zaleca się użyć tego nowego modelu idąc dalej, a ADF tworzenia interfejsu użytkownika zostało przełączone do generowania te nowe typy.

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
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

Aby skopiować dane do i z magazynu obiektów Blob w formacie ORC/Avro/JSON/dane binarne, należy ustawić właściwość typu zestawu danych na **AzureBlob**. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa **AzureBlob**. |Yes |
| folderPath | Ścieżka do kontenera i folderu w magazynie obiektów blob. <br/><br/>Filtr z symbolami wieloznacznymi jest obsługiwany dla ścieżki, z wyjątkiem nazwy kontenera. Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak); użyj `^` jako znak ucieczki, jeśli nazwą rzeczywistego folderu ma symboli wieloznacznych lub ten znak ucieczki wewnątrz. <br/><br/>Przykłady: myblobcontainer/myblobfolder/see więcej przykładów w [folderowi i plikowi Przykłady filtrów](#folder-and-file-filter-examples). |Tak w przypadku działania kopiowania/Lookup, nie na działaniu GetMetadata |
| fileName | **Filtr nazwy lub symbol wieloznaczny** dla obiektów blob w ramach określonego "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie obiekty BLOB w folderze. <br/><br/>Dla filtru, dozwolone symbole wieloznaczne są: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak).<br/>— Przykład 1: `"fileName": "*.csv"`<br/>— Przykład 2: `"fileName": "???20180427.txt"`<br/>Użyj `^` jako znak ucieczki, jeśli Twoje rzeczywiste nazwy plików symboli wieloznacznych lub ten znak ucieczki wewnątrz.<br/><br/>Kiedy dla wyjściowego zestawu danych nie jest określona nazwa pliku i **preserveHierarchy** nie został określony w ujściu działania, działanie kopiowania automatycznie generuje nazwę obiektu blob przy użyciu następującego wzorca: "*Danych. [identyfikator GUID uruchamiania działania]. [Identyfikator GUID Jeśli FlattenHierarchy]. [format skonfigurowanie]. [kompresji, jeśli skonfigurowano]* ", np. "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; w przypadku kopiowania z tabelaryczne źródła przy użyciu nazwy tabeli zamiast zapytania wzorzec nazwy to "*[Nazwa tabeli]. [ format]. [kompresji, jeśli skonfigurowano]* ", np. "MyTable.csv". |Nie |
| modifiedDatetimeStart | Filtr plików, na podstawie atrybutu: Data ostatniej modyfikacji. Pliki zostanie wybrana, w przypadku ich godzina ostatniej modyfikacji w okresie między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że ogólnej wydajności przenoszenia danych będzie mieć wpływ na włączenie tego ustawienia, gdy użytkownik chce pliku filtru z ogromne ilości plików. <br/><br/> Właściwości może mieć wartość NULL, która oznacza, że żaden filtr atrybutu pliku zostaną zastosowane do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza pliki, których ostatniej modyfikacji atrybut jest większa niż lub równe wartością daty/godziny, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty i godziny zostanie wybrany.| Nie |
| modifiedDatetimeEnd | Filtr plików, na podstawie atrybutu: Data ostatniej modyfikacji. Pliki zostanie wybrana, w przypadku ich godzina ostatniej modyfikacji w okresie między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że ogólnej wydajności przenoszenia danych będzie mieć wpływ na włączenie tego ustawienia, gdy użytkownik chce pliku filtru z ogromne ilości plików. <br/><br/> Właściwości może mieć wartość NULL, która oznacza, że żaden filtr atrybutu pliku zostaną zastosowane do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza pliki, których ostatniej modyfikacji atrybut jest większa niż lub równe wartością daty/godziny, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty i godziny zostanie wybrany.| Nie |
| format | Jeśli chcesz skopiować pliki się między magazynami oparte na plikach (kopia binarna), Pomiń sekcji format w definicji zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować lub generowanie plików za pomocą określonego formatu, obsługiwane są następujące typy formatów plików: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, i **ParquetFormat**. Ustaw **typu** właściwości **format** do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstu](supported-file-formats-and-compression-codecs.md#text-format), [formatu JSON](supported-file-formats-and-compression-codecs.md#json-format), [Avro format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc format](supported-file-formats-and-compression-codecs.md#orc-format), i [formatu Parquet ](supported-file-formats-and-compression-codecs.md#parquet-format) sekcje. |Brak (tylko w przypadku scenariusza kopia binarna) |
| compression | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Obsługiwane typy to **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**.<br/>Są obsługiwane poziomy **optymalna** i **najszybciej**. |Nie |

>[!TIP]
>Aby skopiować wszystkie obiekty BLOB w folderze, określ **folderPath** tylko.<br>Aby skopiować z pojedynczym obiektem blob o określonej nazwie, należy określić **folderPath** z część z folderem i **fileName** z nazwą pliku.<br>Aby skopiować podzestaw obiektów blob w folderze, podaj **folderPath** z część z folderem i **fileName** z filtr z symbolami wieloznacznymi. 

**Przykład:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez obiekt Blob magazynu źródła i ujścia.

### <a name="blob-storage-as-a-source-type"></a>Magazyn obiektów blob jako typ źródła

- Na potrzeby kopiowania z **Parquet i format tekstu rozdzielanego**, można znaleźć [Parquet i źródło format tekstu rozdzielanego](#parquet-and-delimited-text-format-source) sekcji.
- Na potrzeby kopiowania z innych formatów, takich jak **format ORC/Avro/JSON/dane binarne**, można znaleźć [innego formatu źródła](#other-format-source) sekcji.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet i źródło format tekstu rozdzielanego

Aby skopiować dane z magazynu obiektów Blob w Parquet lub format tekstu rozdzielanego, zapoznaj się [formatu Parquet](format-parquet.md) i [format tekstu rozdzielanego](format-delimited-text.md) artykuł na temat źródła działania kopiowania oparta na format i obsługiwane ustawienia. Następujące właściwości są obsługiwane w przypadku obiektów Blob platformy Azure w ramach `storeSettings` ustawienia źródła kopiowania oparta na format:

| Właściwość                 | Opis                                                  | Wymagane                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Właściwość type w obszarze `storeSettings` musi być równa **AzureBlobStorageReadSetting**. | Yes                                           |
| recursive                | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. Zwróć uwagę, że gdy cyklicznego jest ustawiona na wartość PRAWDA, a obiekt sink magazynem opartych na plikach, pusty folder lub podfolder nie jest kopiowany lub utworzono obiekt sink. Dozwolone wartości to **true** (ustawienie domyślne) i **false**. | Nie                                            |
| wildcardFolderPath       | Ścieżka folderu przy użyciu symboli wieloznacznych w ramach danego kontenera skonfigurowane w zestawie danych do filtru źródła folderów. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak); użyj `^` jako znak ucieczki, jeśli nazwą rzeczywistego folderu ma symboli wieloznacznych lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [folderowi i plikowi Przykłady filtrów](#folder-and-file-filter-examples). | Nie                                            |
| wildcardFileName         | Nazwa pliku przy użyciu symboli wieloznacznych w ramach wybranego kontenera + folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak); użyj `^` jako znak ucieczki, jeśli nazwą rzeczywistego folderu ma symboli wieloznacznych lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [folderowi i plikowi Przykłady filtrów](#folder-and-file-filter-examples). | Tak, jeśli `fileName` nie została określona w zestawie danych |
| modifiedDatetimeStart    | Filtr plików, na podstawie atrybutu: Data ostatniej modyfikacji. Pliki zostanie wybrana, w przypadku ich godzina ostatniej modyfikacji w okresie między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br> Właściwości może mieć wartość NULL, która oznacza, że żaden filtr atrybutu pliku zostaną zastosowane do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza pliki, których ostatniej modyfikacji atrybut jest większa niż lub równe wartością daty/godziny, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty i godziny zostanie wybrany. | Nie                                            |
| modifiedDatetimeEnd      | Wartość taka sama jak powyżej.                                               | Nie                                            |
| maxConcurrentConnections | Liczba połączeń połączyć się z magazynu magazynu jednocześnie. Należy określić tylko wtedy, gdy chcesz ograniczyć liczby jednoczesnych połączeń z magazynem danych. | Nie                                            |

> [!NOTE]
> Dla formatu Parquet/rozdzielany tekst **BlobSource** źródło działania kopiowania typu opisane w następnej sekcji nadal jest obsługiwany jako — jest zgodności z poprzednimi wersjami. Zaleca się użyć tego nowego modelu idąc dalej, a ADF tworzenia interfejsu użytkownika zostało przełączone do generowania te nowe typy.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageReadSetting",
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

Aby skopiować dane z magazynu obiektów Blob w formacie ORC/Avro/JSON/dane binarne, należy ustawić typ źródłowego w działaniu kopiowania, aby **BlobSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być równa **BlobSource**. |Yes |
| recursive | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. Zwróć uwagę, że gdy cyklicznego jest ustawiona na wartość PRAWDA, a obiekt sink magazynem opartych na plikach, pusty folder lub podfolder nie jest kopiowany lub utworzono obiekt sink.<br/>Dozwolone wartości to **true** (ustawienie domyślne) i **false**. | Nie |
| maxConcurrentConnections | Liczba połączeń połączyć się z magazynu magazynu jednocześnie. Należy określić tylko wtedy, gdy chcesz ograniczyć liczby jednoczesnych połączeń z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>Magazyn obiektów blob jako typ ujścia

- Związanym z kopiowaniem do **Parquet i format tekstu rozdzielanego**, można znaleźć [Parquet, jak i ujście format tekstu rozdzielanego](#parquet-and-delimited-text-format-sink) sekcji.
- Związanym z kopiowaniem w innych formatach, takich jak **format ORC/Avro/JSON/dane binarne**, można znaleźć [ujścia innego formatu](#other-format-sink) sekcji.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet, jak i ujście format tekstu rozdzielanego

Aby skopiować dane do magazynu obiektów Blob w Parquet lub format tekstu rozdzielanego, zapoznaj się [formatu Parquet](format-parquet.md) i [format tekstu rozdzielanego](format-delimited-text.md) artykuł na temat ujścia działania kopiowania oparta na format i obsługiwane ustawienia. Następujące właściwości są obsługiwane w przypadku obiektów Blob platformy Azure w ramach `storeSettings` ustawienia ujścia kopiowania oparta na format:

| Właściwość                 | Opis                                                  | Wymagane |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Właściwość type w obszarze `storeSettings` musi być równa **AzureBlobStorageWriteSetting**. | Yes      |
| copyBehavior             | Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest pliki z magazynu danych oparte na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (ustawienie domyślne)</b>: Zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><b>-FlattenHierarchy</b>: Wszystkie pliki z folderu źródłowego znajdują się w pierwszy poziom folderu docelowego. Pliki docelowe mają nazwy wygenerowany automatycznie. <br/><b>-MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku lub obiektu blob jest określony, nazwa pliku scalonego jest określonej nazwy. W przeciwnym razie jest automatycznie wygenerowana nazwa pliku. | Nie       |
| maxConcurrentConnections | Liczba połączeń połączyć się z magazynu magazynu jednocześnie. Należy określić tylko wtedy, gdy chcesz ograniczyć liczby jednoczesnych połączeń z magazynem danych. | Nie       |

> [!NOTE]
> Dla formatu Parquet/rozdzielany tekst **BlobSink** typ ujścia działania dla kopiowania opisane w następnej sekcji nadal jest obsługiwany jako — jest zgodności z poprzednimi wersjami. Zaleca się użyć tego nowego modelu idąc dalej, a ADF tworzenia interfejsu użytkownika zostało przełączone do generowania te nowe typy.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Ujścia innego formatu

Aby skopiować dane do magazynu obiektów Blob, należy ustawić typ ujścia w działaniu kopiowania, aby **BlobSink**. Następujące właściwości są obsługiwane w **ujścia** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type ujścia działania kopiowania musi być równa **BlobSink**. |Yes |
| copyBehavior | Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest pliki z magazynu danych oparte na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (ustawienie domyślne)</b>: Zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><b>-FlattenHierarchy</b>: Wszystkie pliki z folderu źródłowego znajdują się w pierwszy poziom folderu docelowego. Pliki docelowe mają nazwy wygenerowany automatycznie. <br/><b>-MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku lub obiektu blob jest określony, nazwa pliku scalonego jest określonej nazwy. W przeciwnym razie jest automatycznie wygenerowana nazwa pliku. | Nie |
| maxConcurrentConnections | Liczba połączeń połączyć się z magazynu magazynu jednocześnie. Należy określić tylko wtedy, gdy chcesz ograniczyć liczby jednoczesnych połączeń z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
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
| `container/Folder*` | (puste, użyta domyślna) | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (puste, użyta domyślna) | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Kilka przykładów rekurencyjnych i copyBehavior

W tej sekcji opisano wynikowe zachowania operacji kopiowania różne kombinacje wartości cyklicznych i copyBehavior.

| recursive | copyBehavior | Źródło struktury folderów | Wynikowy docelowej |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | folder docelowy Folder1 jest tworzony przy użyciu tej samej struktury jako źródła:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + plik3 + File4 + File5 zawartości są scalane w jeden plik o nazwie pliku wygenerowany automatycznie. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | folder docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | folder docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa plik2<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 zawartości są scalane w jeden plik o nazwie pliku wygenerowany automatycznie. automatycznie wygenerowana nazwa Plik1<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości z przepływu danych

Dowiedz się, szczegółowe informacje z [źródła przekształcenia](data-flow-source.md) i [ujścia przekształcania](data-flow-sink.md) w mapowanie przepływu danych.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
