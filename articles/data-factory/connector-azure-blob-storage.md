---
title: Kopiowanie danych do lub z usługi Azure Blob Storage za pomocą Data Factory
description: Informacje o kopiowaniu danych z obsługiwanych magazynów danych źródłowych do usługi Azure Blob Storage lub z magazynu obiektów BLOB do obsługiwanych magazynów danych w programie przy użyciu Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 7d17d1ee60f2049dccfb8bc711f3b76bb51689b6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681356"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Kopiowanie danych do lub z usługi Azure Blob Storage za pomocą Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-blob-connector.md)
> * [Bieżąca wersja](connector-azure-blob-storage.md)

W tym artykule opisano sposób kopiowania danych do i z usługi Azure Blob Storage. Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik obiektów blob platformy Azure jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuń działanie](delete-activity.md)

W każdym przypadku ten łącznik magazynu obiektów BLOB obsługuje:

- Kopiowanie obiektów BLOB do i z kont usługi Azure Storage ogólnego przeznaczenia i gorącą/chłodny magazyn obiektów BLOB. 
- Kopiowanie obiektów BLOB przy użyciu klucza konta, sygnatury dostępu współdzielonego usługi, nazwy głównej usługi lub tożsamości zarządzanych dla uwierzytelniania zasobów platformy Azure.
- Kopiowanie obiektów blob z bloków, dołączania lub stronicowych obiektów blob i kopiowanie danych tylko do blokowych obiektów BLOB.
- Kopiowanie obiektów BLOB jako lub analizowanie lub Generowanie obiektów blob z [obsługiwanymi formatami plików i kodekami kompresji](supported-file-formats-and-compression-codecs.md).

>[!IMPORTANT]
>Jeśli włączysz opcję **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu** w ustawieniach zapory usługi Azure Storage i chcesz używać usługi Azure Integration Runtime do nawiązywania połączenia z BLOB Storage, musisz użyć [uwierzytelniania tożsamości zarządzanej](#managed-identity).

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek Data Factory specyficznych dla magazynu obiektów BLOB.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Łącznik obiektów blob platformy Azure obsługuje następujące typy uwierzytelniania, zapoznaj się z odpowiednią sekcją, aby uzyskać szczegółowe informacje:

- [Uwierzytelnianie klucza konta](#account-key-authentication)
- [Uwierzytelnianie sygnatury dostępu współdzielonego](#shared-access-signature-authentication)
- [Uwierzytelnianie jednostki usługi](#service-principal-authentication)
- [Zarządzane tożsamości na potrzeby uwierzytelniania zasobów platformy Azure](#managed-identity)

>[!NOTE]
>W przypadku ładowania danych do SQL Data Warehouse, Jeśli źródłowy lub przejściowy magazyn obiektów BLOB jest skonfigurowany z Virtual Network punktem końcowym, należy użyć uwierzytelniania tożsamości zarządzanej, zgodnie z wymaganiami GeoBase, i korzystać z własnej Integration Runtime z wersją 3,18 lub nowszy. Zapoznaj się z sekcją [uwierzytelnianie tożsamości zarządzanej](#managed-identity) , podając więcej wymagań wstępnych dotyczących konfiguracji.

>[!NOTE]
>Działania HDInsight i Azure Machine Learning obsługują tylko uwierzytelnianie klucza konta usługi Azure Blob Storage.

### <a name="account-key-authentication"></a>Uwierzytelnianie klucza konta

Aby użyć uwierzytelniania klucza konta magazynu, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć wartość **AzureBlobStorage** (Sugerowana) lub **AzureStorage** (Zobacz uwagi poniżej). |Tak |
| Przekształcon | Określ informacje, które są konieczne do nawiązania połączenia z magazynem dla właściwości connectionString. <br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory. Możesz również umieścić klucz konta w Azure Key Vault i ściągnąć konfigurację `accountKey` z parametrów połączenia. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. |Tak |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

>[!NOTE]
>Dodatkowy punkt końcowy usługi BLOB Service nie jest obsługiwany w przypadku korzystania z uwierzytelniania klucza konta. Można użyć innych typów uwierzytelniania.

>[!NOTE]
>Jeśli używasz połączonej usługi typu "AzureStorage", jest ona nadal obsługiwana w stanie takim, w jakim będziesz mieć możliwość użycia nowego typu połączonej usługi "AzureBlobStorage" do przodu.

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

### <a name="shared-access-signature-authentication"></a>Uwierzytelnianie sygnatury dostępu współdzielonego

Sygnatura dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Za pomocą sygnatury dostępu współdzielonego można udzielić klientowi ograniczonych uprawnień do obiektów na koncie magazynu przez określony czas. Nie musisz udostępniać kluczy dostępu do konta. Sygnatura dostępu współdzielonego to identyfikator URI, który obejmuje parametry zapytania wszystkie informacje niezbędne do uzyskania dostępu uwierzytelnionego do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu za pomocą sygnatury dostępu współdzielonego, klient musi przekazać sygnaturę dostępu współdzielonego tylko do odpowiedniego konstruktora lub metody. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [sygnatury dostępu współdzielonego: Opis modelu sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Data Factory obsługuje teraz zarówno **sygnatury dostępu współdzielonego usługi** , jak i **sygnatury dostępu współdzielonego konta**. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md).
>- W konfiguracji późniejszego zestawu danych ścieżka folderu jest ścieżką bezwzględną rozpoczynającą się od poziomu kontenera. Należy skonfigurować jeden wyrównany z ścieżką w identyfikatorze URI SAS.

> [!TIP]
> Aby wygenerować sygnaturę dostępu współdzielonego usługi dla konta magazynu, można wykonać następujące polecenia programu PowerShell. Zastąp symbole zastępcze i Udziel wymaganych uprawnień.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Aby można było korzystać z uwierzytelniania sygnatury dostępu współdzielonego, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć wartość **AzureBlobStorage** (Sugerowana) lub **AzureStorage** (Zobacz uwagi poniżej). |Tak |
| sasUri | Określ identyfikator URI sygnatury dostępu współdzielonego do zasobów magazynu, takich jak BLOB/Container. <br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory. Token sygnatury dostępu współdzielonego można również umieścić w Azure Key Vault, aby wykorzystać funkcję autorotacji i usunąć część tokenu. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. |Tak |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

>[!NOTE]
>Jeśli używasz połączonej usługi typu "AzureStorage", jest ona nadal obsługiwana w stanie takim, w jakim będziesz mieć możliwość użycia nowego typu połączonej usługi "AzureBlobStorage" do przodu.

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

Podczas tworzenia identyfikatora URI sygnatury dostępu współdzielonego należy wziąć pod uwagę następujące kwestie:

- Ustaw odpowiednie uprawnienia do odczytu/zapisu dla obiektów na podstawie sposobu używania połączonej usługi (odczyt, zapis, odczyt/zapis) w fabryce danych.
- Ustaw odpowiednio **czas wygaśnięcia** . Upewnij się, że dostęp do obiektów magazynu nie wygasa w aktywnym okresie potoku.
- Identyfikator URI należy utworzyć w odpowiednim kontenerze/obiekcie blob zależnie od potrzeb. Identyfikator URI sygnatury dostępu współdzielonego do obiektu BLOB umożliwia Data Factory dostępu do określonego obiektu BLOB. Identyfikator URI sygnatury dostępu współdzielonego do kontenera magazynu obiektów BLOB umożliwia Data Factory iteracji za pomocą obiektów BLOB w tym kontenerze. Aby zapewnić dostęp do większej lub mniejszej liczby obiektów później lub zaktualizować identyfikator URI sygnatury dostępu współdzielonego, pamiętaj, aby zaktualizować połączoną usługę nowym identyfikatorem URI.

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Aby uzyskać ogólne informacje na temat uwierzytelniania jednostki usługi Azure Storage, zapoznaj się z tematem [uwierzytelnianie dostępu do usługi Azure Storage przy użyciu Azure Active Directory](../storage/common/storage-auth-aad.md).

Aby użyć uwierzytelniania jednostki usługi, wykonaj następujące kroki:

1. Zarejestruj jednostkę aplikacji w Azure Active Directory (Azure AD), wykonując czynności opisane w temacie [Rejestrowanie aplikacji w dzierżawie usługi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Należy zwrócić uwagę na następujące wartości, których można użyć do zdefiniowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Przyznaj jednostce usługi odpowiednie uprawnienia w usłudze Azure Blob Storage. Więcej informacji na temat ról można znaleźć w tematach [Zarządzanie prawami dostępu do danych usługi Azure Storage za pomocą RBAC](../storage/common/storage-auth-aad-rbac.md) .

    - **Jako źródło**w kontroli dostępu (IAM) Przydziel co najmniej rolę **czytnika danych obiektów blob magazynu** .
    - **Jako ujścia**w kontroli dostępu (IAM) Przydziel co najmniej rolę **współautor danych obiektu blob magazynu** .

Te właściwości są obsługiwane dla połączonej usługi Azure Blob Storage:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi być ustawiona na wartość **AzureBlobStorage**. |Tak |
| Końcowego | Określ punkt końcowy usługi Azure Blob Storage z wzorcem `https://<accountName>.blob.core.windows.net/`. |Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako element **SecureString** , aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| dzierżaw | Określ informacje o dzierżawie (nazwę domeny lub identyfikator dzierżawy), w których znajduje się Twoja aplikacja. Pobierz go, aktywując wskaźnik myszy w prawym górnym rogu Azure Portal. | Tak |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

>[!NOTE]
>Uwierzytelnianie nazwy głównej usługi jest obsługiwane tylko przez połączoną usługę typu "AzureBlobStorage", ale nie poprzednią połączoną z typem "AzureStorage".

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

### <a name="managed-identity"></a>Zarządzane tożsamości na potrzeby uwierzytelniania zasobów platformy Azure

Fabryka danych może być skojarzona z [zarządzaną tożsamością dla zasobów platformy Azure](data-factory-service-identity.md), która reprezentuje tę konkretną fabrykę danych. Tej tożsamości zarządzanej można bezpośrednio użyć do uwierzytelniania za pomocą usługi BLOB Storage, podobnie jak w przypadku korzystania z własnej nazwy głównej. Umożliwia to wyznaczeniu fabryki dostęp do danych z/do magazynu obiektów blob i ich kopiowanie.

Zapoznaj się z ogólnym uwierzytelnianiem [dostępu do usługi Azure Storage przy użyciu Azure Active Directory](../storage/common/storage-auth-aad.md) na potrzeby uwierzytelniania usługi Azure Storage. Aby używać tożsamości zarządzanych do uwierzytelniania zasobów platformy Azure, wykonaj następujące kroki:

1. [Pobierz informacje o tożsamości zarządzanej fabryki danych](data-factory-service-identity.md#retrieve-managed-identity) przez skopiowanie wartości "Identyfikator aplikacji tożsamości usługi", która została wygenerowana wraz z fabryką.

2. Przyznaj zarządzanej tożsamości odpowiednie uprawnienia w usłudze Azure Blob Storage. Więcej informacji na temat ról można znaleźć w tematach [Zarządzanie prawami dostępu do danych usługi Azure Storage za pomocą RBAC](../storage/common/storage-auth-aad-rbac.md) .

    - **Jako źródło**w kontroli dostępu (IAM) Przydziel co najmniej rolę **czytnika danych obiektów blob magazynu** .
    - **Jako ujścia**w kontroli dostępu (IAM) Przydziel co najmniej rolę **współautor danych obiektu blob magazynu** .

>[!IMPORTANT]
>Jeśli korzystasz z bazy danych Base, aby załadować dane z obiektu BLOB (jako źródło lub jako tymczasowe) do SQL Data Warehouse, podczas korzystania z uwierzytelniania tożsamości zarządzanej dla obiektu BLOB upewnij się, że w [tych wskazówkach](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) opisano kroki 1 i 2 w temacie 1) rejestrowanie serwera SQL Database za pomocą usługi Azure Active Katalog (Azure AD) i 2) przypisują rolę współautor danych obiektów blob magazynu do serwera SQL Database; pozostałe są obsługiwane przez Data Factory. Jeśli magazyn obiektów BLOB jest skonfigurowany za pomocą punktu końcowego usługi Azure Virtual Network, aby można było załadować z niego dane, należy użyć uwierzytelniania tożsamości zarządzanej, zgodnie z wymaganiami firmy Base.

Te właściwości są obsługiwane dla połączonej usługi Azure Blob Storage:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi być ustawiona na wartość **AzureBlobStorage**. |Tak |
| Końcowego | Określ punkt końcowy usługi Azure Blob Storage z wzorcem `https://<accountName>.blob.core.windows.net/`. |Tak |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

> [!NOTE]
> Zarządzane tożsamości dla uwierzytelniania zasobów platformy Azure są obsługiwane tylko przez połączoną usługę typu "AzureBlobStorage", ale nie poprzednią połączoną usługę typu "AzureStorage". 

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w przypadku obiektów blob platformy Azure w obszarze Ustawienia `location` w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagany |
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

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość **AzureBlob**. |Tak |
| folderPath | Ścieżka do kontenera i folderu w magazynie obiektów BLOB. <br/><br/>Filtr symboli wieloznacznych jest obsługiwany dla ścieżki wykluczającej nazwę kontenera. Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br/><br/>Przykłady: myblobcontainer/myblobfolder/, Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). |Tak dla działania kopiowania/wyszukiwania, nie dla działania GetMetadata |
| fileName | **Nazwa lub filtr symboli wieloznacznych** dla obiektów BLOB w ramach określonego elementu "folderPath". Jeśli nie określisz wartości tej właściwości, zestaw danych wskazuje wszystkie obiekty blob w folderze. <br/><br/>W przypadku filtru dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak).<br/>-Przykład 1: `"fileName": "*.csv"`<br/>-Przykład 2: `"fileName": "???20180427.txt"`<br/>Użyj `^`, aby wyjść, jeśli rzeczywista nazwa pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.<br/><br/>Jeśli nazwa pliku nie została określona dla wyjściowego zestawu danych, a **preserveHierarchy** nie jest określona w obiekcie ujścia aktywności, działanie kopiowania automatycznie generuje nazwę obiektu BLOB z następującym wzorcem: "*Data. [ Identyfikator GUID przebiegu działania]. [GUID if FlattenHierarchy]. [Format, jeśli skonfigurowano]. [kompresja, jeśli jest skonfigurowana]* ", np." Data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz "; Jeśli skopiujesz ze źródła tabelarycznego przy użyciu nazwy tabeli zamiast zapytania, wzorzec nazwy to " *[nazwa tabeli]. [ Format]. [kompresja, jeśli jest skonfigurowana]* ", np." MyTable. csv ". |Nie |
| modifiedDatetimeStart | Filtr plików oparty na atrybucie: ostatnio modyfikowane. Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że będzie to miało wpływ na ogólną wydajność przenoszenia danych przez włączenie tego ustawienia, jeśli chcesz, aby filtr plików był objęty dużą ilością plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.| Nie |
| modifiedDatetimeEnd | Filtr plików oparty na atrybucie: ostatnio modyfikowane. Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że będzie to miało wpływ na ogólną wydajność przenoszenia danych przez włączenie tego ustawienia, jeśli chcesz, aby filtr plików był objęty dużą ilością plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.| Nie |
| Formatowanie | Jeśli chcesz skopiować pliki w postaci między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować lub generować pliki o określonym formacie, obsługiwane są następujące typy formatu plików: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**i **ParquetFormat**. Ustaw właściwość **Type** w polu **Format** na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz sekcję [Format tekstu](supported-file-formats-and-compression-codecs.md#text-format), [Format JSON](supported-file-formats-and-compression-codecs.md#json-format), [Format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs.md#orc-format)i [Parquet format](supported-file-formats-and-compression-codecs.md#parquet-format) . |Nie (tylko w przypadku scenariusza kopiowania binarnego) |
| skompresowane | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekoder kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Obsługiwane typy to **gzip**, **Wklęśnięcie**, **BZip2**i **ZipDeflate**.<br/>Obsługiwane poziomy są **optymalne** i **najszybciej**. |Nie |

>[!TIP]
>Aby skopiować wszystkie obiekty blob w folderze, określ tylko **folderPath** .<br>Aby skopiować pojedynczy obiekt BLOB o podanej nazwie, należy określić **folderPath** z częścią **folderu i nazwą pliku.**<br>Aby skopiować podzestaw obiektów BLOB w folderze, należy określić **folderPath** z częścią folderu i **nazwą pliku** z filtrem symboli wieloznacznych. 

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia magazynu obiektów BLOB.

### <a name="blob-storage-as-a-source-type"></a>BLOB Storage jako typ źródła

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w przypadku obiektów blob platformy Azure w obszarze Ustawienia `storeSettings` w źródle kopiowania opartym na formacie:

| Właściwość                 | Opis                                                  | Wymagany                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **AzureBlobStorageReadSetting**. | Tak                                           |
| rozpoznawania                | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy pamiętać, że gdy wartość cykliczna jest ustawiona na wartość true, a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia. Dozwolone wartości to **true** (wartość domyślna) i **false**. | Nie                                            |
| wildcardFolderPath       | Ścieżka folderu z symbolami wieloznacznymi pod podanym kontenerem skonfigurowanym w zestawie danych do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Nie                                            |
| wildcardFileName         | Nazwa pliku z symbolami wieloznacznymi pod podanym kontenerem + folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^`, aby wyjść, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Tak, jeśli nie określono `fileName` w zestawie danych |
| modifiedDatetimeStart    | Filtr plików oparty na atrybucie: ostatnio modyfikowane. Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` ma wartość NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` ma wartość NULL, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana. | Nie                                            |
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

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość **BlobSource**. |Tak |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy pamiętać, że gdy wartość cykliczna jest ustawiona na wartość true, a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia.<br/>Dozwolone wartości to **true** (wartość domyślna) i **false**. | Nie |
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

### <a name="blob-storage-as-a-sink-type"></a>BLOB Storage jako typ ujścia

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w przypadku obiektów blob platformy Azure w obszarze Ustawienia `storeSettings` w ujściach kopiowania opartych na formacie:

| Właściwość                 | Opis                                                  | Wymagany |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **AzureBlobStorageWriteSetting**. | Tak      |
| copyBehavior             | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartego na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (domyślnie)</b>: zachowuje hierarchię plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie generowane nazwy. <br/><b>-MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku lub obiektu BLOB jest określona, scalona nazwa pliku jest podaną nazwą. W przeciwnym razie jest to automatycznie wygenerowana nazwa pliku. | Nie       |
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

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type ujścia działania Copy musi być ustawiona na wartość **wartość blobsink**. |Tak |
| copyBehavior | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartego na plikach.<br/><br/>Dozwolone wartości to:<br/><b>-PreserveHierarchy (domyślnie)</b>: zachowuje hierarchię plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego do folderu docelowego.<br/><b>-FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie generowane nazwy. <br/><b>-MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku lub obiektu BLOB jest określona, scalona nazwa pliku jest podaną nazwą. W przeciwnym razie jest to automatycznie wygenerowana nazwa pliku. | Nie |
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

| folderPath | fileName | rozpoznawania | Źródłowa Struktura folderu i wynik filtru (pliki **pogrubione** są pobierane)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (puste, Użyj domyślnego) | false | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**plik2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | (puste, Użyj domyślnego) | true | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**plik2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | `*.csv` | false | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;plik2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `container/Folder*` | `*.csv` | true | kontener<br/>&nbsp;&nbsp;&nbsp;&nbsp;folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;plik2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Kilka przykładów rekurencyjnych i copyBehavior

W tej sekcji opisano zachowanie operacji kopiowania dla różnych kombinacji wartości cyklicznych i copyBehavior.

| rozpoznawania | copyBehavior | Struktura folderu źródłowego | Wyniki docelowe |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy Folder1 jest tworzony z tą samą strukturą co Źródło:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik1 + plik2 + file3 + File4 + File5 zawartość jest scalana w jeden plik z automatycznie wygenerowaną nazwą pliku. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik2<br/><br/>Subfolder1 z file3, File4 i File5 nie są pobierane. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy Folder1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa dla plik2<br/><br/>Subfolder1 z file3, File4 i File5 nie są pobierane. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;plik1 + plik2 zawartości są scalane w jeden plik z automatycznie wygenerowaną nazwą pliku. automatycznie wygenerowana nazwa dla plik1<br/><br/>Subfolder1 z file3, File4 i File5 nie są pobierane. |

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Dowiedz się więcej o [przekształceniu źródłowym](data-flow-source.md) i [transformacji ujścia](data-flow-sink.md) w mapowaniu przepływu danych.

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby uzyskać szczegółowe informacje na temat właściwości, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Usuń właściwości działania

Aby uzyskać szczegółowe informacje na temat właściwości, zaznacz pozycję [Usuń działanie](delete-activity.md) .

## <a name="next-steps"></a>Następne kroki

Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
