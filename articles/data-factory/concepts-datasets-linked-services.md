---
title: Zestawy danych w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat zestawów danych w usłudze Data Factory. Zestawy danych reprezentują dane wejściowe i wyjściowe.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: shlo
ms.openlocfilehash: 6b74f217d296b5de8886f608b1bc92e908b5d8b4
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866476"
---
# <a name="datasets-in-azure-data-factory"></a>Zestawy danych w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-create-datasets.md)
> * [Bieżąca wersja](concepts-datasets-linked-services.md)

W tym artykule opisano, jakie zestawy danych są, jak są one definiowane w formacie JSON i w jaki sposób są one używane w potoki usługi Azure Data Factory.

Jeśli jesteś nowym użytkownikiem usługi Data Factory, zobacz [wprowadzenie do usługi Azure Data Factory](introduction.md) omówienie.

## <a name="overview"></a>Omówienie
Fabryka danych może obejmować jeden lub wiele potoków. A **potoku** jest logicznym grupowaniem **działania** wspólnie wykonują zadanie. Działania w potoku definiują akcje do wykonania na danych. Teraz **dataset** jest nazwany widok danych, które po prostu wskazuje lub odwołuje się do danych, które mają być używane w Twojej **działania** jako dane wejściowe i wyjściowe. Zestawy danych identyfikują dane w różnych magazynach danych, takich jak tabele, pliki, foldery i dokumenty. Na przykład zestaw danych obiektów blob platformy Azure określa kontener obiektów blob i folder w usłudze Blob Storage, z których działanie ma odczytywać dane.

Przed utworzeniem zestawu danych, należy utworzyć [ **połączoną usługę** ](concepts-linked-services.md) połączyć usługi magazynu danych w usłudze data factory. Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Mechanizm działa w ten sposób; zestaw danych reprezentuje strukturę danych w połączonych magazynach danych, a połączona usługa zawiera definicję połączenia ze źródłem danych. Na przykład Azure Storage połączona usługa łączy konto usługi storage z fabryką danych. Zestaw danych obiektów Blob platformy Azure reprezentuje kontener obiektów blob oraz folder, w ramach tego konta magazynu platformy Azure, który zawiera wejściowe obiekty BLOB mają być przetwarzane.

Poniżej przedstawiono przykładowy scenariusz. Aby skopiować dane z magazynu obiektów Blob do usługi SQL database, utworzysz dwie połączone usługi: Azure Storage i Azure SQL Database. Następnie należy utworzyć dwa zestawy danych: Azure zestaw danych obiektów Blob (która odwołuje się do połączonej usługi Azure Storage) i zestaw danych tabeli SQL Azure, (która odwołuje się do usługi Azure SQL Database, połączone). Usługi Azure Storage i Azure SQL Database, połączone usługi zawiera parametry połączenia, które usługi Data Factory używa w środowisku uruchomieniowym połączyć się z usługi Azure Storage i Azure SQL Database, odpowiednio. Zestaw danych obiektów Blob platformy Azure Określa kontener obiektów blob i folder obiektów blob, który zawiera wejściowe obiekty BLOB w usłudze Blob storage. Zestaw danych tabeli SQL Azure Określa tabelę SQL w usłudze SQL database, do której ma zostać skopiowane dane.

Na poniższym diagramie przedstawiono relacje między potoku, działania, zestaw danych i połączonej usługi w usłudze Data Factory:

![Relacja potoku, działania, zestaw danych, połączonych usług](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Dataset JSON
Zestaw danych w usłudze Data Factory jest zdefiniowana w następującym formacie JSON:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
W poniższej tabeli opisano właściwości w powyższy kod JSON:

Właściwość | Opis | Wymagane |
-------- | ----------- | -------- |
name | Nazwa zestawu danych. Zobacz [usługi Azure Data Factory — reguły nazewnictwa](naming-rules.md). |  Yes |
type | Typ zestawu danych. Określ jeden z typów obsługiwanych przez usługę Data Factory (na przykład: AzureBlob, AzureSqlTable). <br/><br/>Aby uzyskać więcej informacji, zobacz [typy zestawów danych](#dataset-type). | Yes |
Struktura | Schemat zestawu danych. Aby uzyskać więcej informacji, zobacz [schemat zestawu danych](#dataset-structure-or-schema). | Nie |
typeProperties | Właściwości typu są różne dla każdego typu (na przykład: Usługa Azure Blob, tabela Azure SQL). Szczegółowe informacje na temat obsługiwanych typów i ich właściwości, [typ zestawu danych](#dataset-type). | Yes |

### <a name="data-flow-compatible-dataset"></a>Dataset zgodne przepływu danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Zobacz [obsługiwane typy zestawów danych](#dataset-type) listę typów z zestawu danych, które są [przepływ danych](concepts-data-flow-overview.md) zgodny. Zestawy danych, które są zgodne na przepływ danych wymaga definicji zestawu szczegółowych danych przekształceń. W związku z tym definicja JSON jest nieco inne. Zamiast _struktury_ właściwość, zestawy danych, które są zgodne przepływ danych ma _schematu_ właściwości.

W przepływu danych zestawy danych są używane w przekształcenia źródła i ujścia. Zestawy danych definiować schematy danych podstawowych. Jeśli dane bez schematu, można użyć dryfu schematu dla źródła i ujścia. Schemat w zestawie danych reprezentuje typ danych fizycznych i kształt.

Definiując schematu z zestawu danych, otrzymasz typy danych powiązane, formatów danych, lokalizacja pliku i informacji o połączeniu ze skojarzonej połączonej usługi. Metadane z zestawów danych pojawia się w swojej transformacji źródła jako źródło *projekcji*. Projekcji w Przekształcenie źródła reprezentuje dane przepływu danych przy użyciu zdefiniowanych nazw i typów.

Podczas importowania schematu przepływ danych zestawu danych wybierz **Importowanie schematu** przycisku i wybierz opcję Importuj ze źródła lub z pliku lokalnego. W większości wypadków zaimportujesz schematu bezpośrednio ze źródła. Ale jeśli masz już plik lokalny schematu (pliku Parquet lub CSV z nagłówkami), można kierować usługi Data Factory można oprzeć schematu dla tego pliku.


```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```

W poniższej tabeli opisano właściwości w powyższy kod JSON:

Właściwość | Opis | Wymagane |
-------- | ----------- | -------- |
name | Nazwa zestawu danych. Zobacz [usługi Azure Data Factory — reguły nazewnictwa](naming-rules.md). |  Yes |
type | Typ zestawu danych. Określ jeden z typów obsługiwanych przez usługę Data Factory (na przykład: AzureBlob, AzureSqlTable). <br/><br/>Aby uzyskać więcej informacji, zobacz [typy zestawów danych](#dataset-type). | Yes |
Schemat | Schemat zestawu danych. Aby uzyskać więcej informacji, zobacz [przepływ danych zgodne zestawy danych](#dataset-type). | Nie |
typeProperties | Właściwości typu są różne dla każdego typu (na przykład: Usługa Azure Blob, tabela Azure SQL). Szczegółowe informacje na temat obsługiwanych typów i ich właściwości, [typ zestawu danych](#dataset-type). | Yes |


## <a name="dataset-example"></a>Przykładowy zestaw danych
W poniższym przykładzie zestaw danych reprezentuje tabelę o nazwie MyTable w usłudze SQL database.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
Pamiętaj o następujących kwestiach:

- Typ jest ustawiony na AzureSqlTable.
- (specyficzne typu AzureSqlTable) właściwość type tableName jest równa MyTable.
- linkedServiceName odnosi się do połączonej usługi typu AzureSqlDatabase, która jest zdefiniowana w następnym fragmencie kodu JSON.

## <a name="dataset-type"></a>Typ zestawu danych
Istnieje wiele różnych typów zestawów danych, w zależności od magazynu danych, którego używasz. Zobacz poniższą tabelę, aby uzyskać listę magazynów danych obsługiwanych przez usługę Data Factory. Kliknij magazyn danych, aby dowiedzieć się, jak utworzyć połączoną usługę i zestaw danych dla tego magazynu danych.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-dataflow.md)]

W przykładzie w poprzedniej sekcji, typ zestawu danych jest równa **AzureSqlTable**. Podobnie dla zestawu danych obiektów Blob platformy Azure, typ zestawu danych jest równa **AzureBlob**, jak pokazano w poniższym formacie JSON:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                "type": "LinkedServiceReference",
        },

        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```

## <a name="dataset-structure-or-schema"></a>Struktura zestawu danych lub schemat
**Struktury** sekcji lub **schematu** (zgodne przepływ danych) zestawów danych sekcja jest opcjonalna. Definiuje schemat zestawu danych, zawierający kolekcję nazwy i typy danych kolumn. Sekcja struktury umożliwia dostarczyć informacji o typie, który służy do konwersji typów oraz mapowanie kolumn ze źródła do miejsca docelowego.

Każda kolumna w strukturze zawiera następujące właściwości:

Właściwość | Opis | Wymagane
-------- | ----------- | --------
name | Nazwa kolumny. | Yes
type | Typ danych kolumny. Usługa Data Factory obsługuje następujące typy danych tymczasowych jako dozwolone wartości: **Int16, Int32, Int64, pojedynczego, Double, Decimal, bajt [], atrybut typu wartość logiczna, ciąg, identyfikator Guid, daty/godziny, Datetimeoffset i przedziału czasu** | Nie
Kultury | . Kulturę opartą na sieci ma być używany, gdy typem jest typ architektury .NET: `Datetime` lub `Datetimeoffset`. Wartość domyślna to `en-us`. | Nie
format | Format ciągu ma być używany, gdy typem jest typ architektury .NET: `Datetime` lub `Datetimeoffset`. Zapoznaj się [Custom Date and Time Format Strings](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) w sposób formatowania daty/godziny. | Nie

### <a name="example"></a>Przykład
W poniższym przykładzie załóżmy, że źródło danych obiektu Blob jest w formacie CSV i zawiera trzy kolumny: nazwa użytkownika, nazwę i lastlogindate. Są one typu Int64, ciągów i typu Datetime w formacie niestandardowe daty/godziny przy użyciu skrócone nazwy francuska dzień tygodnia.

Zdefiniuj strukturę zestawu danych obiektów Blob w następujący sposób wraz z definicji typu dla kolumny:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Wskazówki

Poniższe wskazówki ułatwiają zrozumienie, kiedy należy uwzględnić informacje o strukturze i co należy uwzględnić w **struktury** sekcji. Dowiedz się więcej na sposób mapowania danych źródła do ujścia przez fabrykę danych i kiedy należy określić informacje o strukturze z [mapowanie typu i schematu](copy-activity-schema-and-type-mapping.md).

- **Dla źródeł danych schematu silne**, określ sekcji struktury, tylko wtedy, gdy chcesz, aby zamapować kolumny źródła do ujścia kolumn i ich nazwy nie są takie same. Tego rodzaju źródła danych ze strukturą są przechowywane informacje schematu i typu danych oraz samych danych. Przykładami źródeł danych ze strukturą programu SQL Server, Oracle i usługi Azure SQL Database.<br/><br/>Jak informacje o typie jest już dostępna dla źródeł danych ze strukturą, nie może zawierać informacje o typie, gdy zawiera sekcję struktury.
- **Dla schematu nie/weak np. plik tekstowy w magazynie obiektów blob źródła danych**, obejmują strukturę, gdy zestaw danych jest wartością wejściową dla działania kopiowania, a typy danych źródłowy zestaw danych powinny być konwertowane na typach natywnych ujścia. I mają strukturę, gdy chcesz mapować kolumny źródła i ujścia kolumn...

## <a name="create-datasets"></a>Tworzenie zestawów danych
Zestawy danych można utworzyć przy użyciu jednej z następujących narzędzi lub zestawów SDK: [interfejsu API platformy .NET](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [interfejsu API REST](quickstart-create-data-factory-rest-api.md), szablon usługi Azure Resource Manager i witryny Azure portal

## <a name="current-version-vs-version-1-datasets"></a>Bieżąca wersja i zestawów danych w wersji 1

Poniżej przedstawiono niektóre różnice między usługi Data Factory i zestawów danych w wersji 1 usługi Data Factory:

- Właściwość zewnętrznych nie jest obsługiwana w bieżącej wersji. Zastępuje się wyrazami [wyzwalacza](concepts-pipeline-execution-triggers.md).
- Właściwości zasad i dostępności nie są obsługiwane w bieżącej wersji. Czas rozpoczęcia dla potoku, który jest zależny od [wyzwalaczy](concepts-pipeline-execution-triggers.md).
- O określonym zakresie zestawów danych (zbiory danych zdefiniowanych w potoku) nie są obsługiwane w bieżącej wersji.

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące samouczki krok po kroku dotyczące tworzenia potoków i zestawów danych przy użyciu jednej z następujących narzędzi lub zestawów SDK.

- [Quickstart: create a data factory using .NET (Szybki start: tworzenie fabryki danych przy użyciu platformy .NET)](quickstart-create-data-factory-dot-net.md)
- [Szybki Start: tworzenie fabryki danych przy użyciu programu PowerShell](quickstart-create-data-factory-powershell.md)
- [Szybki Start: tworzenie fabryki danych przy użyciu interfejsu API REST](quickstart-create-data-factory-rest-api.md)
- [Szybki Start: tworzenie fabryki danych przy użyciu witryny Azure portal](quickstart-create-data-factory-portal.md)
