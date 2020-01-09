---
title: 'Szybki Start: wyodrębnianie danych o odbiorze przy użyciu języka Python — formularz rozpoznawania'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy w języku Python, aby wyodrębnić dane z obrazów paragonów sprzedaży.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 12/18/2019
ms.author: pafarley
ms.openlocfilehash: 5d95527fa5333b113fb53840fd687313a139413a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473878"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>Szybki Start: wyodrębnianie danych przyjęcia przy użyciu interfejsu API REST aparatu rozpoznawania w języku Python

W tym przewodniku szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy platformy Azure w języku Python, aby wyodrębnić i zidentyfikować odpowiednie informacje w paragonach sprzedaży.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> Ten przewodnik Szybki Start używa interfejsu API aparatu rozpoznawania w wersji 1.0. Jeśli Twoja subskrypcja znajduje się w regionie `West US 2` lub `West Europe`, należy postępować zgodnie z instrukcjami[interfejsu API szybkiego startu](./python-train-extract.md) w wersji 2,0.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki Start, musisz dysponować:
- Dostęp do programu rozpoznawania formularzy z ograniczonym dostępem. Aby uzyskać dostęp do wersji zapoznawczej, Wypełnij i Prześlij formularz [żądania dostępu do aparatu rozpoznawania formularza](https://aka.ms/FormRecognizerRequestAccess) .
- Zainstalowana w języku [Python](https://www.python.org/downloads/) (Jeśli chcesz uruchomić przykład lokalnie).
- Adres URL obrazu potwierdzenia. Możesz użyć [przykładowego obrazu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) dla tego przewodnika Szybki Start.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularza

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analizowanie paragonu

Aby rozpocząć analizowanie paragonu, należy wywołać interfejs API **analizy paragonów** przy użyciu poniższego skryptu języka Python. Przed uruchomieniem skryptu wprowadź następujące zmiany:

1. Zamień `<Endpoint>` na punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zastąp `<your receipt URL>` adresem URL obrazu paragonu.
1. Zastąp `<subscription key>` kluczem subskrypcji skopiowanym w poprzednim kroku.

    ```python
    import http.client, urllib.request, urllib.parse, urllib.error, base64

    source = r"<your receipt URL>"
    body = {"url":source}
    body = json.dumps(body)

    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        conn = http.client.HTTPSConnection('<Endpoint>')
        conn.request("POST", "/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze", body, headers)
        response = conn.getresponse()
        data = response.read()
        operationURL = "" + response.getheader("Operation-Location")
        print ("Operation-Location header: " + operationURL)
        conn.close()
    except Exception as e:
        print(e)
        exit()
    ```

1. Zapisz kod w pliku z rozszerzeniem. pr. Na przykład *form-Recognizer-Receipts.py*.
1. Otwórz okno wiersza polecenia.
1. W monicie użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python form-recognizer-receipts.py`.

Otrzymasz odpowiedź `202 (Success)`, która zawiera nagłówek z **lokalizacją operacji** , który skrypt będzie drukowany w konsoli programu. Ten nagłówek zawiera identyfikator operacji, którego można użyć w celu zbadania stanu operacji i pobrania wyników analizy. W poniższym przykładzie wartość ciąg po `operations/` jest IDENTYFIKATORem operacji.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Pobierz wyniki odbioru

Po wywołaniu interfejsu API **Analizowanie paragonów** należy wywołać interfejs API **wyników Get paragonu** w celu uzyskania stanu operacji i wyodrębnionych danych. Dodaj następujący kod w dolnej części skryptu języka Python. Spowoduje to wyodrębnienie wartości identyfikatora operacji i przekazanie jej do nowego wywołania interfejsu API. Operacja jest asynchroniczna, dlatego ten skrypt wywołuje interfejs API w regularnych odstępach czasu, dopóki wyniki nie będą dostępne. Zalecamy interwał co najmniej jednej sekundy.

```python
operationId = operationURL.split("operations/")[1]

conn = http.client.HTTPSConnection('<Endpoint>')
while True:
    try:
        conn.request("GET", f"/formrecognizer/v1.0-preview/prebuilt/receipt/operations/{operationId}", "", headers)
        responseString = conn.getresponse().read().decode('utf-8')
        responseDict = json.loads(responseString)
        conn.close()
        print(responseString)
        if 'status' in responseDict and responseDict['status'] not in ['NotStarted','Running']:
            break
        time.sleep(1)
    except Exception as e:
        print(e)
        exit()
```

1. Zapisz skrypt.
1. Ponownie Użyj `python` polecenia, aby uruchomić przykład. Na przykład `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Skrypt będzie drukował odpowiedzi do konsoli do momentu zakończenia operacji analizy. Następnie drukuje wyodrębnione dane tekstowe w formacie JSON. Pole `"recognitionResults"` zawiera wszystkie wiersze tekstu wyodrębnione z przyjęcia, a pole `"understandingResults"` zawiera informacje o klucz/wartość dla najbardziej odpowiednich części paragonu.

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

W tym przewodniku szybki start użyto interfejsu API REST aparatu rozpoznawania formularzy w języku Python do uczenia modelu i uruchomienia go w przykładowym scenariuszu. Następnie zapoznaj się z dokumentacją referencyjną w celu eksplorowania interfejsu API rozpoznawania formularzy.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)