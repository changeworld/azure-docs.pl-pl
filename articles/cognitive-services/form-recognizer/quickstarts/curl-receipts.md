---
title: 'Szybki start: Wyodrębnianie danych otrzymania przy użyciu programu cURL - rozpoznawania formularza'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki Start użyjesz interfejsu API REST rozpoznawania formularza za pomocą programu cURL do wyodrębniania danych z obrazów paragonów.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: c232469886941a2db1ae57437c4b72d7565c123c
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503448"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>Szybki start: Wyodrębnianie danych potwierdzenia, przy użyciu interfejsu API REST rozpoznawania formularza za pomocą programu cURL

W tym przewodniku Szybki Start użyjesz interfejsu API REST usługi Azure formularza rozpoznawania za pomocą programu cURL do wyodrębniania i Zidentyfikuj odpowiednie informacje w paragonów.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki Start, musisz mieć:
- Dostęp ograniczony dostęp do aparatu rozpoznawania formularza w wersji zapoznawczej. Aby uzyskać dostęp do wersji zapoznawczej, wypełnij i Prześlij [żądanie dostępu do rozpoznawania formularza](https://aka.ms/FormRecognizerRequestAccess) formularza.
- [cURL](https://curl.haxx.se/windows/) zainstalowane.
- Adres URL obrazu odbioru. Możesz użyć [przykładowy obraz](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) dla tego przewodnika Szybki Start.

## <a name="create-a-form-recognizer-resource"></a>Utwórz zasób rozpoznawania formularza

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analizowanie potwierdzenia

Aby zacząć analizować potwierdzenie, należy wywołać **analizowanie otrzymania** interfejsu API przy użyciu poniższego polecenia cURL. Przed uruchomieniem polecenia dokonaj następujących zmian:

1. Zastąp `<Endpoint>` z punktem końcowym, uzyskany klucz subskrypcji rozpoznawania formularza. Można je znaleźć zasobu rozpoznawania formularza **Przegląd** kartę.
1. Zastąp `<your receipt URL>` przy użyciu adresu URL obrazu potwierdzenia.
1. Zastąp `<subscription key>` z kluczem subskrypcji został skopiowany w poprzednim kroku.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"url\": \"<your receipt URL>\"}"
```

Otrzymasz `202 (Success)` odpowiedź, która zawiera **lokalizacji operacji** nagłówka. Wartość tego nagłówka stanowi zawiera identyfikator operacji, który można użyć, aby wykonać zapytanie o stan operacji i uzyskać wyniki. W poniższym przykładzie ciąg po znaku `operations/` jest identyfikatorem operacji.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Pobierz wyniki potwierdzenia

Po po wywołaniu **analizowanie otrzymania** wywołanie interfejsu API, **uzyskać wynik otrzymania** interfejsu API można uzyskać stanu operacji i wyodrębnione dane.

1. Zastąp `<operationId>` identyfikatorem operacji z poprzedniego kroku.
1. Zastąp element `<subscription key>` kluczem subskrypcji.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/operations/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Otrzymasz `200 (Success)` odpowiedzi z danych wyjściowych JSON. Pierwsze pole `"status"`, wskazuje stan operacji. Jeśli operacja została zakończona, `"recognitionResults"` pole zawiera wartość każdego wiersza tekstu, który został wyodrębniony po otrzymaniu i `"understandingResults"` pole zawiera informacje o klucz/wartość dla najbardziej istotnych części odbieranie. Jeśli operacja nie została zakończona, wartość `"status"` będzie `"Running"` lub `"NotStarted"`, i powinna wywołać interfejs API ponownie ręcznie lub za pomocą skryptu. Firma Microsoft zaleca interwał między wywołaniami najmniej jedna sekunda.

Zobacz poniższy obraz potwierdzenia i jego odpowiedniego JSON danych wyjściowych. Dane wyjściowe został skrócony tak, aby zwiększyć czytelność.

![Potwierdzenie odbioru ze sklepu firmy Contoso](../media/contoso-receipt.png)

```json
{
  "status": "Succeeded",
  "recognitionResults": [{
    "page": 1,
    "clockwiseOrientation": 0.36,
    "width": 1688,
    "height": 3000,
    "unit": "pixel",
    "lines": [{
      "boundingBox": [616, 291, 1050, 278, 1053, 384, 620, 397],
      "text": "Contoso",
      "words": [{
        "boundingBox": [619, 292, 1051, 284, 1052, 382, 620, 398],
        "text": "Contoso"
      }]
    }, {
      "boundingBox": [322, 588, 501, 600, 497, 655, 318, 643],
      "text": "Contoso",
      "words": [{
        "boundingBox": [330, 590, 501, 602, 499, 654, 326, 644],
        "text": "Contoso"
      }]
    },
    ...
    ]
  }],
  "understandingResults": [{
    "pages": [1],
    "fields": {
      "Subtotal": {
        "valueType": "numberValue",
        "value": 1098.99,
        "text": "1098.99",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/14/words/1"
        }]
      },
      "Total": {
        "valueType": "numberValue",
        "value": 1203.39,
        "text": "1203.39",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/18/words/1"
        }]
      },
      "Tax": {
        "valueType": "numberValue",
        "value": 104.4,
        "text": "$104.40",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/16/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/16/words/1"
        }]
      },
      "MerchantAddress": {
        "valueType": "stringValue",
        "value": "123 Main Street Redmond, WA 98052",
        "text": "123 Main Street Redmond, WA 98052",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/2/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/2"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/2"
        }]
      },
      "MerchantName": {
        "valueType": "stringValue",
        "value": "Contoso",
        "text": "Contoso",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/1/words/0"
        }]
      },
      "MerchantPhoneNumber": {
        "valueType": "stringValue",
        "value": null,
        "text": "123-456-7890",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/4/words/0"
        }]
      },
      "TransactionDate": {
        "valueType": "stringValue",
        "value": "2019-06-10",
        "text": "6/10/2019",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/0"
        }]
      },
      "TransactionTime": {
        "valueType": "stringValue",
        "value": "13:59:00",
        "text": "13:59",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/1"
        }]
      }
    }
  }]
}
```

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start użyto interfejsu API REST rozpoznawania formularza za pomocą programu cURL do wyodrębniania zawartości przyjęcie sprzedaży. Następnie zobacz dokumentację referencyjną, aby zapoznać się z interfejsu API rozpoznawania fragmentów omówiona bardziej szczegółowo.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
