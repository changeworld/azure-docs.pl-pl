---
title: Przenoszenie danych z baz danych ODBC | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z baz danych ODBC przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: ad70a598-c031-4339-a883-c6125403cb76
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4db720b2140a5208acc582b01b0b9613a7fd827a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621381"
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Przenieś magazyny danych ODBC z danych przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-odbc-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-odbc.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [łącznika ODBC w wersji 2](../connector-odbc.md).


W tym artykule opisano sposób używania działania kopiowania w fabryce danych Azure do przenoszenia danych z lokalnego magazynu danych ODBC. Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania.

Wszystkie obsługiwanych ujścia magazynu danych można skopiować danych z magazynu danych ODBC. Lista magazynów danych obsługiwane jako wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabeli. Fabryka danych aktualnie obsługuje tylko dane przenoszenie, z magazynu danych ODBC do innych magazynów danych, ale nie do przenoszenia danych z innych magazynów danych z magazynem danych ODBC. 

## <a name="enabling-connectivity"></a>Włączenie łączności
Usługi fabryka danych obsługuje połączenia ze źródłami ODBC lokalnymi przy użyciu bramy zarządzania danymi. Zobacz [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu, aby dowiedzieć się więcej na temat bramy zarządzania danymi i instrukcje krok po kroku dotyczące konfigurowania bramy. Użyj bramy w celu połączenia z magazynem danych ODBC, nawet jeśli jest obsługiwany w maszynie Wirtualnej platformy Azure IaaS.

Bramę można zainstalować na tym samym komputerze lokalnym lub maszynie Wirtualnej platformy Azure do przechowywania danych ODBC. Jednak zaleca się zainstalowanie bramy na oddzielnym komputerze/Azure IaaS maszyny Wirtualnej, aby uniknąć rywalizacji i lepszą wydajność. Po zainstalowaniu bramy na osobnym komputerze maszynie powinno być możliwe dostęp do komputera z magazynem danych ODBC.

Oprócz brama zarządzania danymi należy również zainstalować sterownika ODBC dla magazynu danych na komputerze bramy.

> [!NOTE]
> Zobacz [rozwiązywania problemów bramy](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) porady dotyczące rozwiązywania problemów z bramy/połączenia problemy związane z.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działania kopiowania, który przenosi dane z magazynu danych ODBC, za pomocą różnych narzędzi/interfejsów API.

Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych: 

1. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych.
2. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. 
3. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Dla przykładu z definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych z magazynu danych ODBC, zobacz [przykład JSON: magazynu kopii danych ze źródła danych ODBC do obiektów Blob platformy Azure](#json-example-copy-data-from-odbc-data-store-to-azure-blob) sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do magazynu danych ODBC:

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Poniższa tabela zawiera opis specyficzne dla ODBC elementy JSON połączonej usługi.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi mieć ustawioną: **OnPremisesOdbc** |Yes |
| Parametry połączenia |Poświadczenie dostępu z systemem innym niż część ciąg połączenia i opcjonalnie zaszyfrowane poświadczenia. Przykłady w poniższych sekcjach. <br/><br/>Można określić parametry połączenia ze wzorcem, takich jak `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, lub użyj systemową nazwę DSN (nazwa źródła danych), należy skonfigurować na komputerze bramy z `"DSN=<name of the DSN>;"` (trzeba nadal określać części poświadczeń w połączonej usłudze odpowiednio). |Yes |
| poświadczenia |Dostęp do poświadczeń część ciągu połączenia określonego w formacie wartości właściwości sterownika. Przykład: `"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`. |Nie |
| Typ authenticationType |Typ uwierzytelniania używany do łączenia się z magazynem danych ODBC. Możliwe wartości to: anonimowych, jak i podstawowych. |Yes |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego. |Nie |
| hasło |Określ hasło dla konta użytkownika, określone nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, która powinna być używana przez usługi fabryka danych do połączenia z magazynem danych ODBC. |Yes |

### <a name="using-basic-authentication"></a>Przy użyciu uwierzytelniania podstawowego

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```
### <a name="using-basic-authentication-with-encrypted-credentials"></a>Przy użyciu poświadczeń zaszyfrowanych przy użyciu uwierzytelniania podstawowego
Można szyfrować poświadczeń przy użyciu [AzureRMDataFactoryEncryptValue nowy](https://msdn.microsoft.com/library/mt603802.aspx) polecenia cmdlet (w wersji 1.0 programu Azure PowerShell) lub [AzureDataFactoryEncryptValue nowy](https://msdn.microsoft.com/library/dn834940.aspx) (0,9 lub starszej wersji programu Azure PowerShell).  

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Przy użyciu uwierzytelniania anonimowego

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "mygateway"
        }
    }
}
```


## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów obiektów dataset (Azure SQL, obiektów blob platformy Azure, Azure tabeli itp.).

**TypeProperties** sekcja jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. TypeProperties sekcja dla zestawu danych typu **RelationalTable** (w tym zestawie danych ODBC) ma następujące właściwości

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w magazynie danych ODBC. |Yes |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w **typeProperties** sekcji działania z drugiej strony zależą od każdego typu działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

W przypadku działania kopiowania, gdy źródłem jest typu **RelationalSource** (która obejmuje ODBC), w sekcji typeProperties dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Na przykład: Wybierz * z MyTable. |Yes |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>Przykład JSON: magazynu kopii danych ze źródła danych ODBC do obiektów Blob platformy Azure
W poniższym przykładzie przedstawiono definicje JSON, których można utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) lub [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Widoczny jest sposób kopiowania danych ze źródła danych ODBC do magazynu obiektów Blob Azure. Jednak można skopiować danych do dowolnego wychwytywanie podane [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.

Przykład zawiera następujące obiekty fabryki danych:

1. Połączonej usługi typu [OnPremisesOdbc](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [RelationalSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z wyniku kwerendy w magazynie danych ODBC do obiektu blob co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

Pierwszym krokiem należy skonfigurować bramę zarządzania danymi. Instrukcje znajdują się w [przenoszenie danych między lokalizacji lokalnej i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) artykułu.

**ODBC połączona usługa** w tym przykładzie użyto uwierzytelniania podstawowego. Zobacz [ODBC połączona usługa](#linked-service-properties) sekcji dla różnych typów uwierzytelniania, można użyć.

```json
{
    "name": "OnPremOdbcLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Połączona usługa Azure Storage**

```json
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

**Wejściowy zestaw danych ODBC**

Przykładzie przyjęto założenie, utworzono tabelę "MyTable" w bazie danych ODBC i zawiera kolumnę o nazwie "timestampcolumn" dla czasu serii danych.

Ustawienie "external": "prawda" informuje usługi fabryka danych czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremOdbcLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Ścieżka folderu dla obiekt blob jest dynamicznie obliczane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny części czas rozpoczęcia.

```json
{
    "name": "AzureBlobOdbcDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


**Działanie kopiowania w potoku ze źródłem ODBC (RelationalSource) i ujście obiektów Blob (BlobSink)**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania tych zestawów danych wejściowych i wyjściowych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **RelationalSource** i **zbiornika** ustawiono typ **BlobSink**. Określony dla zapytania SQL **zapytania** właściwości wybiera dane w ostatniej godziny do skopiowania.

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "OdbcDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOdbcDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "OdbcToBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-odbc"></a>Mapowanie typu dla ODBC
Jak wspomniano w [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, automatyczne konwersje z typów źródła do zbiornika typy z następujących rozwinięcie wykonuje działania kopiowania:

1. Konwertowanie typów natywnych źródła na typ architektury .NET
2. Konwertowanie na typ macierzysty ujścia typ architektury .NET

Podczas przenoszenia danych z baz danych ODBC, typy danych ODBC są mapowane do typów .NET, jak wspomniano w [mapowanie typu danych ODBC](https://msdn.microsoft.com/library/cc668763.aspx) tematu.

## <a name="map-source-to-sink-columns"></a>Obiekt sink kolumn mapy źródła
Aby uzyskać informacje dotyczące mapowania kolumn w zestawie źródła danych do kolumn w zestawie danych zbiornika, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Odczyt powtarzalny ze źródłami relacyjnymi
Podczas kopiowania danych z danych relacyjnych przechowuje, należy pamiętać, aby uniknąć niezamierzone wyniki powtarzalności. Fabryka danych Azure możesz ponownie ręcznie wycinek. Można również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek jest uruchamiany ponownie, gdy wystąpi błąd. Podczas wycinek zostanie uruchomiona ponownie w obu przypadkach, należy się upewnić, że te same dane jest do odczytu niezależnie od tego, ile razy wycinek jest uruchamiany. Zobacz [Repeatable odczytywać źródłami relacyjnymi](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="ge-historian-store"></a>GE historyk magazynu
Tworzenie usługi ODBC połączone, aby połączyć [historyk Proficy GE (teraz GE historyk)](http://www.geautomation.com/products/proficy-historian) magazynu danych do fabryki danych Azure, jak pokazano w poniższym przykładzie:

```json
{
    "name": "HistorianLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "connectionString": "DSN=<name of the GE Historian store>;",
            "gatewayName": "<gateway name>",
            "authenticationType": "Basic",
            "userName": "<user name>",
            "password": "<password>"
        }
    }
}
```

Instalowanie bramy zarządzania danymi na maszynie lokalnej i zarejestruj bramę przy użyciu portalu. Przy użyciu sterownika ODBC dla historyk GE brama zainstalowanej na komputerze lokalnym do nawiązania połączenia z magazynem danych historyk GE. W związku z tym należy zainstalować sterownik, jeśli nie jest już zainstalowany na komputerze bramy. Zobacz [włączenie łączności](#enabling-connectivity) sekcji, aby uzyskać szczegółowe informacje.

Przed użyciem magazynu historyk GE w rozwiązaniu fabryki danych, należy sprawdzić, czy brama łączy się magazynu danych przy użyciu instrukcji w następnej sekcji.

Przeczytaj artykuł od samego początku, aby uzyskać szczegółowy przegląd przy użyciu danych ODBC są przechowywane jako źródło danych magazynów w operacji kopiowania.  

## <a name="troubleshoot-connectivity-issues"></a>Rozwiązywanie problemów z połączeniem
Aby rozwiązać problemy z połączeniem, użyj **diagnostyki** karcie **Menedżera konfiguracji bramy zarządzania danymi**.

1. Uruchom **Menedżera konfiguracji bramy zarządzania danymi**. Możesz uruchomić "C:\Program Files\Microsoft danych zarządzania Gateway\1.0\Shared\ConfigManager.exe" bezpośrednio (lub) wyszukiwania dla **bramy** można znaleźć link do **brama zarządzania danymi firmy Microsoft** aplikacji, jak pokazano na poniższej ilustracji.

    ![Brama wyszukiwania](./media/data-factory-odbc-connector/search-gateway.png)
2. Przełącz się do **diagnostyki** kartę.

    ![Diagnostyki bramy](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Wybierz **typu** danych magazynu (połączonej usługi).
4. Określ **uwierzytelniania** , a następnie wprowadź **poświadczenia** (lub) wprowadź **ciąg połączenia** używany do nawiązania połączenia z magazynem danych.
5. Kliknij przycisk **połączenie testowe** do testowania połączenia z magazynem danych.

## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.
