---
title: 'Szybki Start: wyodrębnianie danych przyjęcia przy użyciu narzędzia do rozpoznawania formularzy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy z zwinięciem, aby wyodrębnić dane z obrazów paragonów sprzedaży.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 12/18/2019
ms.author: pafarley
ms.openlocfilehash: 5dd459adec95eb764cf3458d1eceb455b36fd965
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473913"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>Szybki Start: wyodrębnianie danych o odbiorze przy użyciu interfejsu API REST aparatu rozpoznawania formularzy z zwinięciem

W tym przewodniku szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy platformy Azure z zwinięciem, aby wyodrębnić i zidentyfikować odpowiednie informacje w paragonach sprzedaży.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> Ten przewodnik Szybki Start używa interfejsu API aparatu rozpoznawania w wersji 1.0. Jeśli Twoja subskrypcja znajduje się w regionie `West US 2` lub `West Europe`, należy postępować zgodnie z instrukcjami[interfejsu API szybkiego startu](./python-train-extract.md) w wersji 2,0.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki Start, musisz dysponować:
- Dostęp do programu rozpoznawania formularzy z ograniczonym dostępem. Aby uzyskać dostęp do wersji zapoznawczej, Wypełnij i Prześlij formularz [żądania dostępu do aparatu rozpoznawania formularza](https://aka.ms/FormRecognizerRequestAccess) .
- zainstalowano [zwinięcie](https://curl.haxx.se/windows/) .
- Adres URL obrazu potwierdzenia. Możesz użyć [przykładowego obrazu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) dla tego przewodnika Szybki Start.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularza

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analizowanie paragonu

Aby rozpocząć analizowanie paragonu, należy wywołać interfejs API **analizy paragonów** przy użyciu poniższego polecenia. Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień `<Endpoint>` na punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zastąp `<your receipt URL>` adresem URL obrazu paragonu.
1. Zastąp `<subscription key>` kluczem subskrypcji skopiowanym w poprzednim kroku.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"url\": \"<your receipt URL>\"}"
```

Otrzymasz odpowiedź `202 (Success)`, która zawiera nagłówek z **lokalizacją operacji** . Wartość tego nagłówka zawiera identyfikator operacji, którego można użyć w celu zbadania stanu operacji i uzyskania wyników. W poniższym przykładzie ciąg po `operations/` jest IDENTYFIKATORem operacji.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Pobierz wyniki odbioru

Po wywołaniu interfejsu API **Analizowanie paragonów** należy wywołać interfejs API **wyników Get paragonu** w celu uzyskania stanu operacji i wyodrębnionych danych.

1. Zastąp `<operationId>` IDENTYFIKATORem operacji z poprzedniego kroku.
1. Zastąp element `<subscription key>` kluczem subskrypcji.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/operations/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Otrzymasz odpowiedź `200 (Success)`ową z danymi wyjściowymi JSON. Pierwsze pole, `"status"`, wskazuje stan operacji. Jeśli operacja zostanie ukończona, pole `"recognitionResults"` zawiera wszystkie wiersze tekstu wyodrębnione z przyjęcia, a pole `"understandingResults"` zawiera informacje o klucz/wartość dla najbardziej odpowiednich części paragonu. Jeśli operacja nie zostanie ukończona, wartość `"status"` będzie `"Running"` lub `"NotStarted"`i należy ponownie wywołać interfejs API, ręcznie lub za pomocą skryptu. Zalecamy przedziału co najmniej jednej sekundy między wywołaniami.

Zapoznaj się z poniższym obrazem paragonu i odpowiednimi danymi wyjściowymi JSON. Dane wyjściowe zostały skrócone w celu zapewnienia czytelności.

![Potwierdzenie ze sklepu contoso](../media/contoso-receipt.png)

Węzeł `"recognitionResults"` zawiera wszystkie rozpoznane teksty. Tekst jest zorganizowany według strony, następnie według wiersza, a następnie poszczególnych słów. Węzeł `"understandingResults"` zawiera wartości specyficzne dla paragonu wykryte przez model. Tutaj znajdziesz przydatne pary klucz/wartość, takie jak podatek, łączny, adres handlowca itd.

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

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto interfejsu API REST aparatu rozpoznawania formularzy z zwinięciem, aby wyodrębnić zawartość paragonu sprzedaży. Następnie zapoznaj się z dokumentacją referencyjną w celu eksplorowania interfejsu API rozpoznawania formularzy.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)