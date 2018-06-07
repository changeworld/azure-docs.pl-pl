---
title: Przenoszenie danych z tabeli sieci Web przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu przenoszenia danych z tabeli na stronie sieci Web przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: eb26e7a6ae4a0756ec9d20d7a3d9f9b37e8ab779
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622903"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Przenoszenie danych ze źródła tabeli sieci Web przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-web-table-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-web-table.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [łącznika tabeli sieci Web w wersji 2](../connector-web-table.md).

Ten artykuł przedstawia sposób użycia działanie kopiowania w fabryce danych Azure do przenoszenia danych z tabeli na stronie sieci Web w magazynie danych obsługiwanych ujścia. W tym artykule opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przepływu danych działanie kopiowania i listę magazynów danych są obsługiwane jako źródła/sink.

Fabryka danych aktualnie obsługuje tylko przenoszenia danych z tabeli sieci Web do innych magazynów danych, ale nie przenoszenia danych z innych danych są przechowywane w tabeli docelowej lokalizacji sieci Web.

> [!IMPORTANT]
> Ten łącznik sieci Web obsługuje obecnie tylko wyodrębnianie zawartości tabeli ze strony HTML. Aby pobrać dane z punktu końcowego protokołu HTTP/s, użyj [łącznika HTTP](data-factory-http-connector.md) zamiast tego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika tabeli sieci Web, musisz skonfigurować Self-hosted integrację środowiska uruchomieniowego (alias brama zarządzania danymi) i `gatewayName` właściwości w obiekt sink połączonej usługi. Na przykład można skopiować danych z tabeli sieci Web do magazynu obiektów Blob platformy Azure, skonfiguruj połączoną usługą magazynu Azure następujące parametry:

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działania kopiowania, który przenosi dane z magazynu lokalnego Cassandra danych przy użyciu różnych narzędzi/interfejsów API. 

- Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych. 
- Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych:

1. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych.
2. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. 
3. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Dla przykładu z definicji JSON dla jednostek fabryki danych, które są używane do skopiowania danych z tabeli sieci web, zobacz [przykład JSON: kopiowanie danych z tabeli sieci Web do obiektów Blob platformy Azure](#json-example-copy-data-from-web-table-to-azure-blob) sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do tabeli sieci Web:

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Poniższa tabela zawiera opis elementów JSON specyficzne dla połączonej usługi sieci Web.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi mieć ustawioną: **sieci Web** |Yes |
| Url |Adres URL źródła w sieci Web |Yes |
| Typ authenticationType |Anonimowe. |Yes |

### <a name="using-anonymous-authentication"></a>Przy użyciu uwierzytelniania anonimowego

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów obiektów dataset (Azure SQL, obiektów blob platformy Azure, Azure tabeli itp.).

**TypeProperties** sekcja jest różne dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. TypeProperties sekcja dla zestawu danych typu **tabeli WebTable** ma następujące właściwości

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Typ zestawu danych. należy wybrać opcję **tabeli WebTable** |Yes |
| ścieżka |Względny adres URL do zasobu, który zawiera tabelę. |Nie. Jeśli ścieżka nie jest określona, używana jest tylko adres URL określony w definicji połączonej usługi. |
| indeks |Indeks tabeli w zasobie. Zobacz [Get indeksu tabeli na stronie HTML](#get-index-of-a-table-in-an-html-page) sekcji, aby instrukcje dotyczące pobierania indeksu tabeli na stronie HTML. |Yes |

**Przykład:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Właściwości, które są dostępne w sekcji typeProperties działania różnić się z każdym typem działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

Obecnie, gdy źródła w przypadku działania kopiowania jest typu **WebSource**, są obsługiwane żadne dodatkowe właściwości.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>Przykład JSON: kopiowanie danych z tabeli sieci Web do obiektów Blob platformy Azure
Poniższy przykład przedstawia:

1. Połączonej usługi typu [Web](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [tabeli WebTable](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [WebSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z tabeli sieci Web do obiektów blob platformy Azure co godzinę. Właściwości JSON używane w te przykłady są opisane w sekcjach poniżej próbek.

Poniższy przykład pokazuje, jak można skopiować danych z tabeli sieci Web do obiektów blob platformy Azure. Jednak dane mogą być kopiowane bezpośrednio do wychwytywanie w [działań przepływu danych](data-factory-data-movement-activities.md) artykułu za pomocą działania kopiowania w fabryce danych Azure.

**Sieci Web połączonej usługi** w tym przykładzie korzysta z usługi sieci Web połączone przy użyciu uwierzytelniania anonimowego. Zobacz [sieci Web połączonej usługi](#linked-service-properties) sekcji dla różnych typów uwierzytelniania, można użyć.

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
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
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

**Wejściowy zestaw danych tabeli WebTable** ustawienie **zewnętrznych** do **true** usługi fabryka danych informuje, czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

> [!NOTE]
> Zobacz [Get indeksu tabeli na stronie HTML](#get-index-of-a-table-in-an-html-page) sekcji, aby instrukcje dotyczące pobierania indeksu tabeli na stronie HTML.  
>
>

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```


**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```



**W potoku z działanie kopiowania**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **WebSource** i **zbiornika** ustawiono typ **BlobSink**.

Zobacz [właściwości typu WebSource](#copy-activity-type-properties) listę obsługiwanych przez WebSource właściwości.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "WebSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Pobierz indeksu tabeli na stronie HTML
1. Uruchom **Excel 2016** i przejdź do **danych** kartę.  
2. Kliknij przycisk **nowe zapytanie** na pasku narzędzi, wskaż polecenie **z innych źródeł** i kliknij przycisk **z sieci Web**.

    ![Power Query menu](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. W **z sieci Web** okna dialogowego wprowadź **adres URL** można skorzystać w połączonej usłudze JSON (na przykład: https://en.wikipedia.org/wiki/) oraz ścieżkę należy określić dla zestawu danych (na przykład: AFI % 27s_100_Years... 100_Movies) i kliknij przycisk **OK**.

    ![Z okna dialogowego sieci Web](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    Adres URL używany w tym przykładzie: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Jeśli widzisz **zawartości sieci Web Access** oknie dialogowym Wybierz prawa **adres URL**, **uwierzytelniania**i kliknij przycisk **Connect**.

   ![Okno dialogowe zawartości sieci Web Access](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Kliknij przycisk **tabeli** elementu w widoku drzewa, aby wyświetlić zawartość z tabeli, a następnie kliknij przycisk **Edytuj** znajdujący się u dołu.  

   ![Nawigator okna dialogowego](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. W **edytora zapytań** okna, kliknij przycisk **Zaawansowany edytor** przycisk na pasku narzędzi.

    ![Przycisk Zaawansowane Edytora](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. W oknie dialogowym Zaawansowany edytor numer obok "Source" jest indeksem.

    ![Zaawansowany edytor - indeksu](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Jeśli korzystasz z programu Excel 2013, użyj [Microsoft Power Query dla programu Excel](https://www.microsoft.com/download/details.aspx?id=39379) można uzyskać indeksu. Zobacz [Connect do strony sieci web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) artykułu, aby uzyskać szczegółowe informacje. Te kroki są podobne, jeśli używasz [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Aby mapować kolumn z zestawu źródła danych do kolumn z obiektu sink zestawu danych, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajność i dostrajania
Zobacz [wydajności działania kopiowania & dostrajanie przewodnik](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego wydajność wpływ przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby.
