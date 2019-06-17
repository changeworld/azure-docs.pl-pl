---
title: Przenoszenie danych z tabeli w sieci Web za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobach przenoszenia danych z tabeli na stronie sieci Web przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 81b7bf7c230c66087bf286ebd9369d992e93be90
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61250581"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Przenoszenie danych ze źródła tabeli w sieci Web przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](data-factory-web-table-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-web-table.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznik Tabela sieci Web w wersji 2](../connector-web-table.md).

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory, aby przenieść dane z tabeli na stronie sieci Web do obsługiwanego magazynu danych ujścia. W tym artykule opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólne omówienie przenoszenie danych za pomocą działania kopiowania i listę magazynów danych obsługiwanych jako źródła/ujścia.

Usługa Data factory obsługuje obecnie tylko przenosi dane z tabeli sieci Web w innych magazynach danych, ale nie przenosi dane z innych danych są przechowywane do tabeli docelowej sieci Web.

> [!IMPORTANT]
> Ten łącznik sieci Web obsługuje obecnie tylko wyodrębniania zawartości tabeli ze strony HTML. Aby pobrać dane z punktu końcowego HTTP/HTTPS, należy użyć [łącznik protokołu HTTP](data-factory-http-connector.md) zamiast tego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika Tabela sieci Web, musisz skonfigurować własne środowisko IR (zwane również bramy zarządzania danymi) i `gatewayName` właściwości w ujściu połączoną usługę. Na przykład: do skopiowania Tabela sieci Web w usłudze Azure Blob storage, można skonfigurować następujące połączoną usługę Azure Storage:

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
Utworzysz potok z działaniem kopiowania, które przenosi dane z lokalnego magazynu danych Cassandra przy użyciu różnych narzędzi/interfejsów API. 

- Najprostszym sposobem utworzenia potoku jest użycie **kreatora kopiowania**. Zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybki przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych. 
- Aby utworzyć potok umożliwia także następujących narzędzi: **Witryna Azure portal**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**i  **Interfejs API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania. 

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania. 
3. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON.  Przykład przy użyciu definicji JSON dla jednostek fabryki danych, które są używane w celu skopiowania danych z tabeli sieci web, zobacz [przykład kodu JSON: Kopiowanie danych z tabeli w sieci Web do usługi Azure Blob](#json-example-copy-data-from-web-table-to-azure-blob) dalszej części tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi fabryka danych określonej do tabeli w sieci Web:

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Poniższa tabela zawiera opis dla elementów JSON, które są specyficzne dla połączonej usługi sieci Web.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi być równa: **Sieć Web** |Tak |
| Adres URL |Adres URL źródła w sieci Web |Tak |
| authenticationType |Anonimowe. |Yes |

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
Aby uzyskać pełną listę sekcje & właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje, takie jak struktury, dostępność i zasady zestawem danych JSON są podobne dla wszystkich typów na zestaw danych (Azure SQL, obiektów blob platformy Azure, usługa Azure table itp.).

**TypeProperties** sekcji różni się dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Zestaw danych o typie sekcji typeProperties **WebTable** ma następujące właściwości

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Typ zestawu danych. musi być równa **WebTable** |Tak |
| path |Względny adres URL do zasobu, który zawiera tabelę. |Nie. Jeśli ścieżka nie zostanie określona, używana jest tylko adres URL, które są określone w definicji połączonej usługi. |
| index |Indeks tabeli w zasobie. Zobacz [Get indeksu tabeli na stronie HTML](#get-index-of-a-table-in-an-html-page) sekcji instrukcje pobierania indeksu tabeli na stronie HTML. |Yes |

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
Aby uzyskać pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Natomiast właściwości, które są dostępne w sekcji typeProperties działania zależą od każdego typu działania. Działanie kopiowania ich różnią się w zależności od typów źródła i ujścia.

Obecnie, gdy źródłowego w działaniu kopiowania jest typu **WebSource**, żadne dodatkowe właściwości są obsługiwane.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>Przykład kodu JSON: Kopiowanie danych z tabeli w sieci Web do obiektów Blob platformy Azure
Poniższy przykład pokazuje:

1. Połączonej usługi typu [Web](#linked-service-properties).
2. Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Dane wejściowe [dataset](data-factory-create-datasets.md) typu [WebTable](#dataset-properties).
4. Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [WebSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z tabeli sieci Web do obiektu blob platformy Azure co godzinę. Właściwości JSON używanych w tych przykładach są opisane w sekcjach poniżej przykładów.

Poniższy przykład pokazuje, jak skopiować dane z tabeli sieci Web do obiektu blob platformy Azure. Jednak możesz skopiować dane bezpośrednio do ujścia, o których wspomniano w [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu za pomocą działania kopiowania w usłudze Azure Data Factory.

**W sieci Web połączoną usługę** w tym przykładzie użyto usługi sieci Web połączone przy użyciu uwierzytelniania anonymous. Zobacz [Web połączoną usługę](#linked-service-properties) sekcji dla różnych typów uwierzytelniania, można użyć.

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

**Wejściowy zestaw danych tabeli WebTable** ustawienie **zewnętrznych** do **true** usługi Data Factory informuje, że zestaw danych jest zewnętrzne w usłudze data factory i nie jest generowany przez działanie w usłudze data factory.

> [!NOTE]
> Zobacz [Get indeksu tabeli na stronie HTML](#get-index-of-a-table-in-an-html-page) sekcji instrukcje pobierania indeksu tabeli na stronie HTML.  
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

Dane są zapisywane do nowego obiektu blob, co godzinę (frequency: godzina, interwał: 1).

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



**Potok z działaniem kopiowania**

Potoku zawierającego działanie kopiowania, który jest skonfigurowany do korzystania z danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **WebSource** i **ujścia** ustawiono typ **BlobSink**.

Zobacz właściwości typu WebSource, aby uzyskać listę właściwości obsługiwanych przez WebSource.

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Pobieranie indeksu tabeli na stronie HTML
1. Uruchom **programu Excel 2016** i przełącz się do **danych** kartę.  
2. Kliknij przycisk **nowe zapytanie** na pasku narzędzi, wskaż polecenie **z innych źródeł** i kliknij przycisk **z sieci Web**.

    ![Menu zapytań zasilania](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. W **z sieci Web** okna dialogowego wprowadź **adresu URL** zostanie wykorzystany w połączonej usługi JSON (na przykład: https://en.wikipedia.org/wiki/) wraz ze ścieżką, należy określić dla zestawu danych (na przykład: AFI % 27s_100_Years... 100_Movies) i kliknij przycisk **OK**.

    ![W oknie dialogowym sieci Web](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    Adres URL używany w tym przykładzie: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Jeśli widzisz **zawartości sieci Web Access** okna dialogowego Wybierz po prawej stronie **adresu URL**, **uwierzytelniania**i kliknij przycisk **Connect**.

   ![Okno dialogowe zawartości sieci Web Access](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Kliknij przycisk **tabeli** elementu w widoku drzewa, aby wyświetlić zawartość z tabeli, a następnie kliknij przycisk **Edytuj** znajdujący się u dołu.  

   ![Okno dialogowe Nawigator](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. W **edytora zapytań** okna, kliknij przycisk **edytora zaawansowanego** przycisk na pasku narzędzi.

    ![Przycisk Edytor zaawansowany](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. Okno dialogowe Edytor zaawansowany numer obok "Źródło" to indeks.

    ![Zaawansowany edytor — indeks](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Jeśli używasz programu Excel 2013, należy użyć [dodatku Microsoft Power Query dla programu Excel](https://www.microsoft.com/download/details.aspx?id=39379) pobranie indeksu. Zobacz [Connect do strony sieci web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) artykuł, aby uzyskać szczegółowe informacje. Kroki są podobne, jeśli używasz [Microsoft Power BI for Desktop](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Aby zamapować kolumny z zestawu danych źródłowych do kolumn z zestawu danych ujścia, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [wydajności działania kopiowania & przewodnika dostrajania](data-factory-copy-activity-performance.md) Aby dowiedzieć się więcej o kluczowych czynników tego obniżenie wydajności przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory i różne sposoby, aby zoptymalizować ją.
