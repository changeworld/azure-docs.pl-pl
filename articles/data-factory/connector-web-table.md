---
title: Kopiowanie danych z tabeli sieci Web przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sieci Web tabeli łącznika z fabryki danych Azure, która umożliwia kopiowania danych z tabeli sieci web do magazynów danych obsługiwane przez fabrykę danych jako sink.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/28/2018
ms.author: jingwang
ms.openlocfilehash: 995bf4586b88671c65077d965b0588de8de74e5c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048938"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Kopiowanie danych z tabeli sieci Web przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [W wersji 1](v1/data-factory-web-table-connector.md)
> * [Bieżąca wersja](connector-web-table.md)

W tym artykule omówiono sposób używania działania kopiowania w fabryce danych Azure można skopiować danych z tabeli bazy danych w sieci Web. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Dane z bazy danych z tabeli sieci Web należy skopiować wszystkie obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik tabeli sieci Web obsługuje **wyodrębniające zawartość tabel z strony HTML**. Aby pobrać dane z punktu końcowego protokołu HTTP/s, użyj [łącznika HTTP](connector-http.md) zamiast tego.

## <a name="prerequisites"></a>Wymagania wstępne

Używanie tego łącznika tabeli sieci Web, należy skonfigurować środowisko uruchomieniowe Self-hosted integracji. Zobacz [środowiska uruchomieniowego integracji Self-hosted](create-self-hosted-integration-runtime.md) artykułu, aby uzyskać szczegółowe informacje.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika tabeli sieci Web.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Następujące właściwości są obsługiwane dla sieci Web tabeli połączonej usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **sieci Web** |Yes |
| url | Adres URL źródła w sieci Web |Yes |
| Typ authenticationType | Dozwolone wartości to: **anonimowe**. |Yes |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. Środowisko uruchomieniowe integracji Self-hosted jest wymagana, jak wspomniano w [wymagania wstępne](#prerequisites). |Yes |

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

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych z tabeli sieci Web.

Aby skopiować dane z tabeli sieci Web, ustaw właściwość Typ zestawu danych do **tabeli WebTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type zestawu danych: **tabeli WebTable** | Yes |
| ścieżka |Względny adres URL do zasobu, który zawiera tabelę. |Nie. Jeśli ścieżka nie jest określona, używana jest tylko adres URL określony w definicji połączonej usługi. |
| indeks |Indeks tabeli w zasobie. Zobacz [Get indeksu tabeli na stronie HTML](#get-index-of-a-table-in-an-html-page) sekcji, aby instrukcje dotyczące pobierania indeksu tabeli na stronie HTML. |Yes |

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

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez źródło tabeli sieci Web.

### <a name="web-table-as-source"></a>Tabela sieci Web jako źródło

Aby skopiować dane z tabeli sieci Web, Ustaw typ źródła w przypadku działania kopiowania do **WebSource**, są obsługiwane żadne dodatkowe właściwości.

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Pobierz indeksu tabeli na stronie HTML

Aby uzyskać wartość indeksu tabeli, które należy skonfigurować w [właściwości zestawu danych](#dataset-properties), np. Excel 2016 jako narzędzia można użyć w następujący sposób:

1. Uruchom **Excel 2016** i przejdź do **danych** kartę.
2. Kliknij przycisk **nowe zapytanie** na pasku narzędzi, wskaż polecenie **z innych źródeł** i kliknij przycisk **z sieci Web**.

    ![Power Query menu](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. W **z sieci Web** okna dialogowego wprowadź **adres URL** można skorzystać w połączonej usłudze JSON (na przykład: https://en.wikipedia.org/wiki/) oraz ścieżkę należy określić dla zestawu danych (na przykład: AFI % 27s_100_Years... 100_Movies) i kliknij przycisk **OK**.

    ![Z okna dialogowego sieci Web](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    Adres URL używany w tym przykładzie: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Jeśli widzisz **zawartości sieci Web Access** oknie dialogowym Wybierz prawa **adres URL**, **uwierzytelniania**i kliknij przycisk **Connect**.

   ![Okno dialogowe zawartości sieci Web Access](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Kliknij przycisk **tabeli** elementu w widoku drzewa, aby wyświetlić zawartość z tabeli, a następnie kliknij przycisk **Edytuj** znajdujący się u dołu.  

   ![Nawigator okna dialogowego](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. W **edytora zapytań** okna, kliknij przycisk **Zaawansowany edytor** przycisk na pasku narzędzi.

    ![Przycisk Zaawansowane Edytora](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. W oknie dialogowym Zaawansowany edytor numer obok "Source" jest indeksem.

    ![Zaawansowany edytor - indeksu](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Jeśli korzystasz z programu Excel 2013, użyj [Microsoft Power Query dla programu Excel](https://www.microsoft.com/download/details.aspx?id=39379) można uzyskać indeksu. Zobacz [Connect do strony sieci web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) artykułu, aby uzyskać szczegółowe informacje. Te kroki są podobne, jeśli używasz [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/).


## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
