---
title: Kopiowanie i Przekształcanie danych w usłudze Azure Blob Storage
description: Dowiedz się, jak kopiować dane do i z magazynu obiektów blob oraz przekształcać dane w usłudze BLOB Storage przy użyciu Data Factory.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/13/2019
ms.openlocfilehash: 3c6b6a403919c3545ec000a701ff31044343f73e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929950"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Kopiowanie i Przekształcanie danych w usłudze Azure Blob Storage za pomocą Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-blob-connector.md)
> * [Bieżąca wersja](connector-azure-blob-storage.md)

W tym artykule opisano sposób używania działania kopiowania w programie Azure Data Factory do kopiowania danych z i do usługi Azure Blob Storage oraz do przekształcania danych w usłudze Azure Blob Storage. Aby dowiedzieć się więcej na temat usługi Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik obiektów blob platformy Azure jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuń działanie](delete-activity.md)

W przypadku działania kopiowania ten łącznik usługi BLOB Storage obsługuje:

- Kopiowanie obiektów blob z konta magazynu ogólnego przeznaczenia platformy Azure oraz magazynu gorących/chłodnych obiektów blob. 
- Kopiowanie obiektów blob przy użyciu klucza konta, sygnatury dostępu współdzielonego usługi tożsamości podmiotu zabezpieczeń lub zarządzanej usługi dla uwierzytelnień zasobów platformy Azure.
- Kopiowanie obiektów blob z bloku, Dołącz lub stronicowe obiekty BLOB i kopiowanie danych tylko blokowe obiekty BLOB.
- Kopiowanie obiektów blob jest analiza kodu lub generowania obiektów blob za pomocą [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md).

>[!IMPORTANT]
>Jeśli włączysz opcję **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu** w ustawieniach zapory usługi Azure Storage i chcesz używać usługi Azure Integration Runtime do nawiązywania połączenia z BLOB Storage, musisz użyć [uwierzytelniania tożsamości zarządzanej](#managed-identity).

## <a name="get-started"></a>Rozpocznij

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do magazynu obiektów Blob.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Łącznik usługi Azure Blob obsługuje następujące typy uwierzytelniania, zajrzyj do odpowiedniej sekcji na potrzeby szczegółów:

- [Uwierzytelnianie za pomocą klucza konta](#account-key-authentication)
- [Uwierzytelniania sygnatury dostępu współdzielonego](#shared-access-signature-authentication)
- [Uwierzytelnianie jednostki usługi](#service-principal-authentication)
- [Zarządzanych tożsamości do uwierzytelniania zasobów platformy Azure](#managed-identity)

>[!NOTE]
>W przypadku ładowania danych do SQL Data Warehouse, Jeśli źródłowy lub przejściowy magazyn obiektów BLOB jest skonfigurowany z Virtual Network punktem końcowym, należy użyć uwierzytelniania tożsamości zarządzanej, zgodnie z wymaganiami GeoBase, i korzystać z własnej Integration Runtime z wersją 3,18 lub nowszy. Zapoznaj się z sekcją [uwierzytelnianie tożsamości zarządzanej](#managed-identity) , podając więcej wymagań wstępnych dotyczących konfiguracji.

>[!NOTE]
>Działania HDInsight i Azure Machine Learning obsługują tylko uwierzytelnianie klucza konta usługi Azure Blob Storage.

### <a name="account-key-authentication"></a>Uwierzytelnianie za pomocą klucza konta

Aby użyć uwierzytelniania klucza konta magazynu, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **usłudze Azure blob Storage** (zalecane) lub **AzureStorage** (zobacz uwagi poniżej). |Tak |
| connectionString | Określ informacje wymagane do połączenia z magazynem dla właściwości connectionString. <br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory. Możesz również umieścić klucz konta w Azure Key Vault i ściągnąć konfigurację `accountKey` z parametrów połączenia. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. |Tak |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

>[!NOTE]
>Dodatkowy punkt końcowy usługi BLOB Service nie jest obsługiwany w przypadku korzystania z uwierzytelniania klucza konta. Można użyć innych typów uwierzytelniania.

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

**Przykład: Zapisz klucz konta w Azure Key Vault**

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

Sygnatury dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Sygnatury dostępu współdzielonego umożliwia jest przyznanie klientowi ograniczonych uprawnień do obiektów na koncie magazynu przez wyznaczony czas. Nie masz udostępniania kluczy dostępu do Twojego konta. Sygnatura dostępu współdzielonego to identyfikator URI, który obejmuje jego parametry zapytań, wszystkie informacje niezbędne do uwierzytelnionego dostępu do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu przy użyciu sygnatury dostępu współdzielonego, klient musi tylko przekazanej sygnatury dostępu współdzielonego do odpowiedniego konstruktora lub metody. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [sygnatur dostępu współdzielonego: omówienie modelu sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Usługa Data Factory obsługuje teraz zarówno **sygnatury dostępu współdzielonego usługi** i **sygnatury dostępu współdzielonego konta**. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md).
>- W późniejszej konfiguracji zestawu danych ścieżka folderu jest ścieżka bezwzględna, wychodząc z poziomu kontenera. Należy skonfigurować jedną dostosowane do ścieżki z identyfikatora URI sygnatury dostępu Współdzielonego.

> [!TIP]
> Aby wygenerować sygnaturę dostępu współdzielonego usługi dla konta magazynu, można wykonać następujące polecenia programu PowerShell. Zastąp symbole zastępcze i udzielić wymaganych uprawnień.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Aby użyć uwierzytelniania sygnatury dostępu współdzielonego, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **usłudze Azure blob Storage** (zalecane) lub **AzureStorage** (zobacz uwagi poniżej). |Tak |
| sasUri | Określ identyfikator URI sygnatury dostępu współdzielonego do zasobów magazynu, takich jak BLOB/Container. <br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory. Token sygnatury dostępu współdzielonego można również umieścić w Azure Key Vault, aby wykorzystać funkcję autorotacji i usunąć część tokenu. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. |Tak |
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

**Przykład: Zapisz klucz konta w Azure Key Vault**

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
- Identyfikator URI należy utworzyć w odpowiednim kontenerze/obiekcie blob zależnie od potrzeb. Identyfikator URI sygnatury dostępu współdzielonego do obiektu blob umożliwia Data Factory w celu dostępu do tego konkretnego obiektu blob. Identyfikator URI sygnatury dostępu współdzielonego do kontenera magazynu obiektów BLOB umożliwia Data Factory iteracji za pomocą obiektów BLOB w tym kontenerze. Aby zapewnić dostęp do później więcej lub mniej obiektów lub zaktualizować identyfikatora URI sygnatury dostępu współdzielonego, pamiętaj, aby zaktualizować połączoną usługę o nowy identyfikator URI.

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
| type | Właściwość type musi być równa **usłudze Azure blob Storage**. |Tak |
| serviceEndpoint | Określ punkt końcowy usługi dla magazynu obiektów Blob platformy Azure przy użyciu wzorca `https://<accountName>.blob.core.windows.net/`. |Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako **SecureString** można bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| tenant | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Pobierz go przez umieszczenie nad nim kursora myszy w prawym górnym rogu witryny Azure Portal. | Tak |
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

Fabrykę danych mogą być skojarzone z [tożsamości zarządzanej dla zasobów platformy Azure](data-factory-service-identity.md), który reprezentuje tę fabrykę danych z konkretnych. Tej tożsamości zarządzanej można bezpośrednio użyć do uwierzytelniania za pomocą usługi BLOB Storage, podobnie jak w przypadku korzystania z własnej nazwy głównej. Umożliwia ona tej fabryki wyznaczonym dostęp i kopiowanie danych z i do usługi Blob storage.

Zapoznaj się z ogólnym uwierzytelnianiem [dostępu do usługi Azure Storage przy użyciu Azure Active Directory](../storage/common/storage-auth-aad.md) na potrzeby uwierzytelniania usługi Azure Storage. Aby użyć zarządzanych tożsamości do uwierzytelniania zasobów platformy Azure, wykonaj następujące kroki:

1. [Pobierz informacje o tożsamości zarządzanej fabryki danych](data-factory-service-identity.md#retrieve-managed-identity) przez skopiowanie wartości "Identyfikator aplikacji tożsamości usługi", która została wygenerowana wraz z fabryką.

2. Przyznaj odpowiednie uprawnienia tożsamość zarządzaną w usłudze Azure Blob storage. Zapoznaj się [Zarządzaj prawa dostępu do danych usługi Azure Storage za pomocą funkcji RBAC](../storage/common/storage-auth-aad-rbac.md) z bardziej szczegółowymi informacjami na rolach.

    - **Jako źródło**, sterowanie dostępu (IAM), co najmniej udzielić **czytnik danych obiektu Blob magazynu** roli.
    - **Jako obiekt sink**, sterowanie dostępu (IAM), co najmniej udzielić **Współautor danych obiektu Blob magazynu** roli.

>[!IMPORTANT]
>W przypadku korzystania z bazy danych w celu załadowania z magazynu obiektów BLOB (jako źródła lub jako tymczasowego) do SQL Data Warehouse, w przypadku używania uwierzytelniania tożsamości zarządzanej dla obiektów blob, należy wykonać kroki 1 i 2 w [tych wskazówkach](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) do 1) zarejestrować serwer SQL Database za pomocą Azure Active Directory (Azure AD) i 2) przypisać rolę współautor danych obiektów blob magazynu do serwera SQL Database; pozostałe są obsługiwane przez Data Factory. Jeśli magazyn obiektów BLOB jest skonfigurowany za pomocą punktu końcowego usługi Azure Virtual Network, aby można było załadować z niego dane, należy użyć uwierzytelniania tożsamości zarządzanej, zgodnie z wymaganiami firmy Base.

Te właściwości są obsługiwane w przypadku obiektów Blob platformy Azure połączonej usługi storage:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **usłudze Azure blob Storage**. |Tak |
| serviceEndpoint | Określ punkt końcowy usługi dla magazynu obiektów Blob platformy Azure przy użyciu wzorca `https://<accountName>.blob.core.windows.net/`. |Tak |
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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w przypadku obiektów blob platformy Azure w obszarze Ustawienia `location` w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagane |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Właściwość Type lokalizacji w zestawie danych musi być ustawiona na wartość **AzureBlobStorageLocation**. | Tak      |
| kontener  | Kontener obiektów BLOB.                                          | Tak      |
| folderPath | Ścieżka do folderu pod podanym kontenerem. Jeśli chcesz użyć symboli wieloznacznych do filtrowania folderów, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |
| fileName   | Nazwa pliku pod podanym kontenerem i folderPath. Jeśli chcesz użyć symboli wieloznacznych do filtrowania plików, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |

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

### <a name="legacy-dataset-model"></a>Model starszego zestawu danych

>[!NOTE]
>Następujący model zestawu danych jest nadal obsługiwany w przypadku zapewnienia zgodności z poprzednimi wersjami. Zalecane jest użycie nowego modelu wymienionego w powyższej sekcji, co przechodzenie do przodu, a interfejs użytkownika tworzenia ADF został przełączony w celu wygenerowania nowego modelu.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa **AzureBlob**. |Tak |
| folderPath | Ścieżka do kontenera i folderu w magazynie obiektów blob. <br/><br/>Filtr symboli wieloznacznych jest obsługiwany dla ścieżki wykluczającej nazwę kontenera. Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br/><br/>Przykłady: myblobcontainer/myblobfolder/, Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). |Tak w przypadku działania kopiowania/Lookup, nie na działaniu GetMetadata |
| fileName | **Filtr nazwy lub symbol wieloznaczny** dla obiektów blob w ramach określonego "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie obiekty BLOB w folderze. <br/><br/>Dla filtru, dozwolone symbole wieloznaczne są: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak).<br/>— Przykład 1: `"fileName": "*.csv"`<br/>— Przykład 2: `"fileName": "???20180427.txt"`<br/>Użyj `^` jako znak ucieczki, jeśli Twoje rzeczywiste nazwy plików symboli wieloznacznych lub ten znak ucieczki wewnątrz.<br/><br/>Jeśli nazwa pliku nie została określona dla wyjściowego zestawu danych, a **preserveHierarchy** nie jest określona w obiekcie ujścia aktywności, działanie kopiowania automatycznie generuje nazwę obiektu BLOB z następującym wzorcem: "*Data. [ Identyfikator GUID przebiegu działania]. [GUID if FlattenHierarchy]. [Format, jeśli skonfigurowano]. [kompresja, jeśli jest skonfigurowana]* ", np." Data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz "; Jeśli skopiujesz ze źródła tabelarycznego przy użyciu nazwy tabeli zamiast zapytania, wzorzec nazwy to " *[nazwa tabeli]. [ Format]. [kompresja, jeśli jest skonfigurowana]* ", np." MyTable. csv ". |Nie |
| modifiedDatetimeStart | Filtr plików, na podstawie atrybutu: ostatniej modyfikacji. Pliki zostanie wybrana, w przypadku ich godzina ostatniej modyfikacji w okresie między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że będzie to miało wpływ na ogólną wydajność przenoszenia danych przez włączenie tego ustawienia, jeśli chcesz, aby filtr plików był objęty dużą ilością plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza pliki, których ostatniej modyfikacji atrybut jest większa niż lub równe wartością daty/godziny, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty i godziny zostanie wybrany.| Nie |
| modifiedDatetimeEnd | Filtr plików, na podstawie atrybutu: ostatniej modyfikacji. Pliki zostanie wybrana, w przypadku ich godzina ostatniej modyfikacji w okresie między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że będzie to miało wpływ na ogólną wydajność przenoszenia danych przez włączenie tego ustawienia, jeśli chcesz, aby filtr plików był objęty dużą ilością plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza pliki, których ostatniej modyfikacji atrybut jest większa niż lub równe wartością daty/godziny, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty i godziny zostanie wybrany.| Nie |
| format | Jeśli chcesz skopiować pliki się między magazynami oparte na plikach (kopia binarna), Pomiń sekcji format w definicji zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować lub generowanie plików za pomocą określonego formatu są obsługiwane następujące typy plików w formacie: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, i **ParquetFormat**. Ustaw **typu** właściwości **format** do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstu](supported-file-formats-and-compression-codecs.md#text-format), [formatu JSON](supported-file-formats-and-compression-codecs.md#json-format), [Avro format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc format](supported-file-formats-and-compression-codecs.md#orc-format), i [formatu Parquet ](supported-file-formats-and-compression-codecs.md#parquet-format) sekcje. |Brak (tylko w przypadku scenariusza kopia binarna) |
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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w przypadku obiektów blob platformy Azure w obszarze Ustawienia `storeSettings` w źródle kopiowania opartym na formacie:

| Właściwość                 | Opis                                                  | Wymagane                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **AzureBlobStorageReadSetting**. | Tak                                           |
| recursive                | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. Zwróć uwagę, że gdy cyklicznego jest ustawiona na wartość PRAWDA, a obiekt sink magazynem opartych na plikach, pusty folder lub podfolder nie jest kopiowany lub utworzono obiekt sink. Dozwolone wartości to **true** (ustawienie domyślne) i **false**. | Nie                                            |
| wildcardFolderPath       | Ścieżka folderu z symbolami wieloznacznymi pod podanym kontenerem skonfigurowanym w zestawie danych do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Nie                                            |
| wildcardFileName         | Nazwa pliku z symbolami wieloznacznymi pod podanym kontenerem + folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Tak, jeśli nie określono `fileName` w zestawie danych |
| modifiedDatetimeStart    | Filtr plików, na podstawie atrybutu: ostatniej modyfikacji. Pliki zostanie wybrana, w przypadku ich godzina ostatniej modyfikacji w okresie między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br> Właściwości może mieć wartość NULL, która oznacza, że żaden filtr atrybutu pliku zostaną zastosowane do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość daty/godziny, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza pliki, których ostatniej modyfikacji atrybut jest większa niż lub równe wartością daty/godziny, zostanie wybrany.  Gdy `modifiedDatetimeEnd` ma wartość daty/godziny, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, pliki, których ostatniej modyfikacji atrybut jest mniejsza niż wartość daty i godziny zostanie wybrany. | Nie                                            |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                            |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie                                            |

> [!NOTE]
> W przypadku formatu tekstu Parquet/Unlimited Źródło działania kopiowania typu **BlobSource** wymienione w następnej sekcji jest nadal obsługiwane w przypadku zgodności z poprzednimi wersjami. Zalecamy użycie nowego modelu do przechodzenia do przodu, a interfejs użytkownika tworzenia ADF został przełączony w celu wygenerowania tych nowych typów.

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

#### <a name="legacy-source-model"></a>Starszy model źródłowy

>[!NOTE]
>Następujący model źródłowy kopiowania jest nadal obsługiwany w przypadku zapewnienia zgodności z poprzednimi wersjami. Zalecane jest użycie nowego modelu wymienionego powyżej, a interfejs użytkownika tworzenia ADF został przełączony w celu wygenerowania nowego modelu.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być równa **BlobSource**. |Tak |
| recursive | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. Zwróć uwagę, że gdy cyklicznego jest ustawiona na wartość PRAWDA, a obiekt sink magazynem opartych na plikach, pusty folder lub podfolder nie jest kopiowany lub utworzono obiekt sink.<br/>Dozwolone wartości to **true** (ustawienie domyślne) i **false**. | Nie |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w przypadku obiektów blob platformy Azure w obszarze Ustawienia `storeSettings` w ujściach kopiowania opartych na formacie:

| Właściwość                 | Opis                                                  | Wymagane |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **AzureBlobStorageWriteSetting**. | Tak      |
| copyBehavior             | Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest pliki z magazynu danych oparte na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (ustawienie domyślne)</b>: zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się w pierwszy poziom folderu docelowego. Pliki docelowe mają nazwy wygenerowany automatycznie. <br/><b>-MergeFiles</b>: scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku lub obiektu blob jest określony, nazwa pliku scalonego jest określonej nazwy. W przeciwnym razie jest automatycznie wygenerowana nazwa pliku. | Nie       |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie       |

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

#### <a name="legacy-sink-model"></a>Model starszej ujścia

>[!NOTE]
>Następujący model ujścia kopiowania jest nadal obsługiwany w przypadku zapewnienia zgodności z poprzednimi wersjami. Zalecane jest użycie nowego modelu wymienionego powyżej, a interfejs użytkownika tworzenia ADF został przełączony w celu wygenerowania nowego modelu.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type ujścia działania kopiowania musi być równa **BlobSink**. |Tak |
| copyBehavior | Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest pliki z magazynu danych oparte na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (ustawienie domyślne)</b>: zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się w pierwszy poziom folderu docelowego. Pliki docelowe mają nazwy wygenerowany automatycznie. <br/><b>-MergeFiles</b>: scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku lub obiektu blob jest określony, nazwa pliku scalonego jest określonej nazwy. W przeciwnym razie jest automatycznie wygenerowana nazwa pliku. | Nie |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

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

### <a name="folder-and-file-filter-examples"></a>Przykłady filtrów folderów i plików

W tej sekcji opisano skutki zachowania ścieżki folderu i nazwy pliku z filtrami symboli wieloznacznych.

| folderPath | fileName | recursive | Źródłowa Struktura folderu i wynik filtru (pliki **pogrubione** są pobierane)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (puste, Użyj domyślnego) | false | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (puste, Użyj domyślnego) | true | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Kilka przykładów rekurencyjnych i copyBehavior

W tej sekcji opisano wynikowe zachowania operacji kopiowania różne kombinacje wartości cyklicznych i copyBehavior.

| recursive | copyBehavior | Źródło struktury folderów | Wynikowy docelowej |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | folder docelowy Folder1 jest tworzony przy użyciu tej samej struktury jako źródła:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 zawartości są scalane w jeden plik o nazwie pliku wygenerowany automatycznie. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | folder docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | folder docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa File2<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 zawartości są scalane w jeden plik o nazwie pliku wygenerowany automatycznie. automatycznie wygenerowana nazwa File1<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Dowiedz się więcej o [przekształceniu źródłowym](data-flow-source.md) i [transformacji ujścia](data-flow-sink.md) w mapowaniu przepływu danych.

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby uzyskać szczegółowe informacje na temat właściwości, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Usuń właściwości działania

Aby uzyskać szczegółowe informacje na temat właściwości, zaznacz pozycję [Usuń działanie](delete-activity.md) .

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
