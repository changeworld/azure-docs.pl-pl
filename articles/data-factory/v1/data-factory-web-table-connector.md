---
title: Przenoszenie danych z tabeli sieci Web przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak przenosić dane z tabeli na stronie sieci Web przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d2ea038c7d7212529185d77a6ba9e64deacb1c9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265716"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Przenoszenie danych ze źródła tabel sieci Web przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-web-table-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-web-table.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Łącznik tabeli sieci Web w wersji 2](../connector-web-table.md).

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych z tabeli na stronie sieci Web do obsługiwanego magazynu danych ujścia. W tym artykule opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z aktywnością kopiowania i listą magazynów danych obsługiwanych jako źródła/pochłaniacze.

Fabryka danych obsługuje obecnie tylko przenoszenie danych z tabeli sieci Web do innych magazynów danych, ale nie przenoszenie danych z innych magazynów danych do miejsca docelowego tabeli sieci Web.

> [!IMPORTANT]
> Ten łącznik sieci Web obsługuje obecnie tylko wyodrębnianie zawartości tabeli ze strony HTML. Aby pobrać dane z punktu końcowego HTTP/s, należy użyć [łącznika HTTP.](data-factory-http-connector.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika tabeli sieci Web, należy skonfigurować własny hostowany środowisko uruchomieniowe integracji (aka Brama zarządzania danymi) i skonfigurować `gatewayName` właściwość w połączonej usłudze ujścia. Na przykład, aby skopiować z tabeli sieci Web do magazynu obiektów blob platformy Azure, skonfiguruj usługę połączony usługi Azure Storage w następujący sposób:

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
Można utworzyć potok z działaniem kopiowania, który przenosi dane z lokalnego magazynu danych Cassandra przy użyciu różnych narzędzi/interfejsów API. 

- Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych. 
- Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania. 

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych.
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania. 
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. 

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON.  W przypadku przykładu z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych z tabeli sieci web, zobacz [przykład JSON: Kopiowanie danych z tabeli sieci Web do](#json-example-copy-data-from-web-table-to-azure-blob) usługi Azure Blob sekcji tego artykułu. 

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory specyficznych dla tabeli sieci Web:

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej w sieci Web.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość typu musi być ustawiona na: **Sieć Web** |Tak |
| Url |Adres URL do źródła sieci Web |Tak |
| authenticationType |Anonimowy. |Tak |

### <a name="using-anonymous-authentication"></a>Korzystanie z uwierzytelniania anonimowego

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
Aby uzyskać pełną listę sekcji & właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych (Azure SQL, Azure blob, tabela platformy Azure itp.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja typeProperties dla zestawu danych typu **WebTable** ma następujące właściwości

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type |typu zestawu danych. musi być ustawiona na **WebTable** |Tak |
| ścieżka |Względny adres URL do zasobu zawierającego tabelę. |Nie. Jeśli ścieżka nie jest określona, używany jest tylko adres URL określony w definicji połączonej usługi. |
| indeks |Indeks tabeli w zasobie. Aby uzyskać indeks tabeli w sekcji strony HTML, zobacz [Uzyskiwanie indeksu tabeli](#get-index-of-a-table-in-an-html-page) w sekcji html. |Tak |

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
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań.

Mając na uwadze, właściwości dostępne w sekcji typeProperties działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze.

Obecnie, gdy źródło w działaniu kopiowania jest typu **WebSource,** nie są obsługiwane żadne dodatkowe właściwości.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>Przykład JSON: Kopiowanie danych z tabeli sieci Web do obiektu Blob platformy Azure
W poniższej próbce przedstawiono:

1. Połączona usługa typu [Web](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [WebTable](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [websource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy kopiuje dane z tabeli sieci Web do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

W poniższym przykładzie pokazano, jak skopiować dane z tabeli sieci Web do obiektu blob platformy Azure. Jednak dane mogą być kopiowane bezpośrednio do dowolnego ujścia określone w działania [przenoszenia danych](data-factory-data-movement-activities.md) artykułu przy użyciu działania kopiowania w usłudze Azure Data Factory.

**Usługa połączona z siecią Web** W tym przykładzie użyto połączonej usługi sieci Web z uwierzytelnianiem anonimowym. Zobacz [sekcję usługi połączonej sieci Web,](#linked-service-properties) aby uzyskać różne typy uwierzytelniania, których można użyć.

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

**Usługa połączona usługi Usługi Azure Storage**

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

**Zestaw danych wejściowych WebTable** Ustawienie na **wartość true** **z zewnątrz** informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych.

> [!NOTE]
> Aby uzyskać indeks tabeli w sekcji strony HTML, zobacz [Uzyskiwanie indeksu tabeli](#get-index-of-a-table-in-an-html-page) w sekcji html.  
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

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1).

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



**Potok z działaniem Kopiowania**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **WebSource** i typ **ujścia** jest ustawiony na **BlobSink**.

Zobacz Właściwości typu WebSource dla listy właściwości obsługiwanych przez źródło websource.

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Pobierz indeks tabeli na stronie HTML
1. Uruchom **program Excel 2016** i przełącz się na kartę **Dane.**  
2. Kliknij **pozycję Nowa kwerenda** na pasku narzędzi, wskaż **pozycję Z innych źródeł** i kliknij pozycję Z **sieci Web**.

    ![Menu dodatku Power Query](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. W oknie dialogowym **Z sieci Web** wprowadź adres **URL,** który https://en.wikipedia.org/wiki/) będzie używany w połączonym usłudze JSON (na przykład: wraz ze ścieżką określoną dla zestawu danych (na przykład: AFI%27s_100_Years... 100_Movies) i kliknij przycisk **OK**.

    ![Z okna dialogowego Sieci Web](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    Adres URL użyty w tym przykładzie:https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Jeśli widzisz okno dialogowe **Zawartość sieci Web programu Access,** wybierz odpowiedni adres **URL**, **uwierzytelnianie**i kliknij pozycję **Połącz**.

   ![Okno dialogowe Zawartość sieci Web programu Access](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Kliknij element **tabeli** w widoku drzewa, aby wyświetlić zawartość z tabeli, a następnie kliknij przycisk **Edytuj** u dołu.  

   ![Okno dialogowe Nawigator](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. W oknie **Edytor zapytań** kliknij przycisk **Edytor zaawansowany** na pasku narzędzi.

    ![Przycisk Edytor zaawansowany](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. W oknie dialogowym Edytor zaawansowany jest numerem obok pozycji "Źródło".

    ![Zaawansowany edytor - Indeks](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Jeśli używasz programu Excel 2013, użyj [programu Microsoft Power Query dla programu Excel,](https://www.microsoft.com/download/details.aspx?id=39379) aby uzyskać indeks. Szczegółowe informacje można znaleźć w artykule [Połącz ze stroną sieci Web.](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) Kroki są podobne w przypadku korzystania z usługi [Microsoft Power BI dla komputerów stacjonarnych](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Aby mapować kolumny ze źródłowego zestawu danych na kolumny z zestawu danych ujścia, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajność i strojenie
Zobacz [Kopiowanie wydajności działania & dostrajania przewodnika,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Copy Activity) w usłudze Azure Data Factory i na różne sposoby optymalizacji.
