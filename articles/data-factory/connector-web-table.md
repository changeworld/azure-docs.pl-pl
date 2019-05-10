---
title: Kopiowanie danych z tabeli w sieci Web za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sieci Web tabeli łącznika usługi Azure Data Factory, która umożliwia kopiowania danych z tabeli w sieci web do magazynów danych obsługiwanych przez usługę Data Factory jako ujścia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: e578b3a6b3905569567b568b0130c1ed1b90d915
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557770"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Kopiowanie danych z tabeli w sieci Web przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-web-table-connector.md)
> * [Bieżąca wersja](connector-web-table.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z tabeli bazy danych w sieci Web. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

Różnica między ten łącznik Tabela sieci Web [REST łącznika](connector-rest.md) i [łącznik protokołu HTTP](connector-http.md) są:

- **Łącznik Tabela sieci Web** wyodrębnia tabelę zawartości z sieci Web w formacie HTML.
- **Łącznik REST** specjalnie do obsługi kopiowania danych z interfejsów API RESTful.
- **Łącznik protokołu HTTP** ogólnego do pobierania danych z dowolnego punktu końcowego HTTP, np. Aby pobrać plik. 

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z bazy danych tabel w sieci Web, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik Tabela sieci Web obsługuje **wyodrębniania zawartości tabeli ze strony HTML**.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika Tabela sieci Web, musisz skonfigurować środowiskiem Integration Runtime. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby uzyskać szczegółowe informacje.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika Tabela sieci Web.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla sieci Web tabeli połączonej usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **Sieć Web** |Yes |
| url | Adres URL źródła w sieci Web |Yes |
| authenticationType | Dozwolone wartości to: **Anonimowe**. |Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Własne środowisko IR jest wymagany, zgodnie z opisem w [wymagania wstępne](#prerequisites). |Yes |

**Przykład:**

```json
{
    "name": "WebLinkedService",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych tabeli w sieci Web.

Aby skopiować dane z tabeli w sieci Web, należy ustawić właściwość typu zestawu danych na **WebTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **Usługa WebTable** | Yes |
| path |Względny adres URL do zasobu, który zawiera tabelę. |Nie. Jeśli ścieżka nie zostanie określona, używana jest tylko adres URL, które są określone w definicji połączonej usługi. |
| index |Indeks tabeli w zasobie. Zobacz [Get indeksu tabeli na stronie HTML](#get-index-of-a-table-in-an-html-page) sekcji instrukcje pobierania indeksu tabeli na stronie HTML. |Yes |

**Przykład:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło tabeli w sieci Web.

### <a name="web-table-as-source"></a>Tabela sieci Web jako źródło

Aby skopiować dane z tabeli w sieci Web, należy ustawić typ źródłowego w działaniu kopiowania, aby **WebSource**, żadne dodatkowe właściwości są obsługiwane.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
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
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Pobieranie indeksu tabeli na stronie HTML

Aby uzyskać indeks tabeli, które należy skonfigurować w [właściwości zestawu danych](#dataset-properties), np. Excel 2016 narzędzia można użyć w następujący sposób:

1. Uruchom **programu Excel 2016** i przełącz się do **danych** kartę.
2. Kliknij przycisk **nowe zapytanie** na pasku narzędzi, wskaż polecenie **z innych źródeł** i kliknij przycisk **z sieci Web**.

    ![Menu zapytań zasilania](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. W **z sieci Web** okna dialogowego wprowadź **adresu URL** zostanie wykorzystany w połączonej usługi JSON (na przykład: https://en.wikipedia.org/wiki/) wraz ze ścieżką, należy określić dla zestawu danych (na przykład: AFI % 27s_100_Years... 100_Movies) i kliknij przycisk **OK**.

    ![W oknie dialogowym sieci Web](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    Adres URL używany w tym przykładzie: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Jeśli widzisz **zawartości sieci Web Access** okna dialogowego Wybierz po prawej stronie **adresu URL**, **uwierzytelniania**i kliknij przycisk **Connect**.

   ![Okno dialogowe zawartości sieci Web Access](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Kliknij przycisk **tabeli** elementu w widoku drzewa, aby wyświetlić zawartość z tabeli, a następnie kliknij przycisk **Edytuj** znajdujący się u dołu.  

   ![Okno dialogowe Nawigator](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. W **edytora zapytań** okna, kliknij przycisk **edytora zaawansowanego** przycisk na pasku narzędzi.

    ![Przycisk Edytor zaawansowany](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. Okno dialogowe Edytor zaawansowany numer obok "Źródło" to indeks.

    ![Zaawansowany edytor — indeks](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Jeśli używasz programu Excel 2013, należy użyć [dodatku Microsoft Power Query dla programu Excel](https://www.microsoft.com/download/details.aspx?id=39379) pobranie indeksu. Zobacz [Connect do strony sieci web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) artykuł, aby uzyskać szczegółowe informacje. Kroki są podobne, jeśli używasz [Microsoft Power BI for Desktop](https://powerbi.microsoft.com/desktop/).


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
