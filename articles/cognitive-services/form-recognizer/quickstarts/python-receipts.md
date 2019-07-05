---
title: 'Szybki start: Wyodrębnianie danych otrzymania przy użyciu języka Python — aparat rozpoznawania formularza'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki Start użyjesz interfejsu API REST rozpoznawania formularza za pomocą języka Python do wyodrębniania danych z obrazów paragonów.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: 8bd4d441859df6dbb36f594d8423eefd84274ec4
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592544"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>Szybki start: Wyodrębnianie danych otrzymania przy użyciu interfejsu API REST rozpoznawania formularza za pomocą języka Python

W tym przewodniku Szybki Start użyjesz interfejsu API REST usługi Azure formularza rozpoznawania za pomocą języka Python do wyodrębniania i Zidentyfikuj odpowiednie informacje w paragonów.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki Start, musisz mieć:
- Dostęp ograniczony dostęp do aparatu rozpoznawania formularza w wersji zapoznawczej. Aby uzyskać dostęp do wersji zapoznawczej, wypełnij i Prześlij [żądanie dostępu do rozpoznawania formularza](https://aka.ms/FormRecognizerRequestAccess) formularza.
- [Python](https://www.python.org/downloads/) zainstalowany (Jeśli chcesz uruchamianie przykładu lokalnie).
- Adres URL obrazu odbioru. Możesz użyć [przykładowy obraz](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) dla tego przewodnika Szybki Start.

## <a name="create-a-form-recognizer-resource"></a>Utwórz zasób rozpoznawania formularza

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analizowanie potwierdzenia

Aby zacząć analizować potwierdzenie, należy wywołać **analizowanie otrzymania** interfejsu API za pomocą poniższego skryptu języka Python. Przed uruchomieniem skryptu, dokonaj następujących zmian:

1. Zastąp `<Endpoint>` z punktem końcowym, uzyskany klucz subskrypcji rozpoznawania formularza. Można je znaleźć zasobu rozpoznawania formularza **Przegląd** kartę.
1. Zastąp `<your receipt URL>` przy użyciu adresu URL obrazu potwierdzenia.
1. Zastąp `<subscription key>` z kluczem subskrypcji został skopiowany w poprzednim kroku.

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

1. Zapisz kod w pliku z rozszerzeniem py. Na przykład *formularza — aparat rozpoznawania receipts.py*.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python form-recognizer-receipts.py`.

Otrzymasz `202 (Success)` odpowiedź, która zawiera **lokalizacji operacji** nagłówka, który skryptu zostanie wydrukowany do konsoli. Ten nagłówek zawiera identyfikator operacji, który służy do sprawdzania stanu operacji i uzyskiwać wyniki analizy. Następujące wartości przykładowy ciąg po znaku `operations/` jest identyfikatorem operacji.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Pobierz wyniki potwierdzenia

Po po wywołaniu **analizowanie otrzymania** wywołanie interfejsu API, **uzyskać wynik otrzymania** interfejsu API można uzyskać stanu operacji i wyodrębnione dane. Dodaj następujący kod na końcu skryptu w języku Python. Wyodrębnianie wartości Identyfikatora operacji i przekazuje go do nowego wywołania interfejsu API. Operacja jest asynchroniczna, więc ten skrypt wywołuje interfejs API w regularnych odstępach czasu, aż wyniki zostaną udostępnione. Firma Microsoft zaleca interwału sekundy jeden lub więcej.

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
1. Ponownie użyć `python` polecenia do uruchomienia przykładu. Na przykład `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Skrypt zostanie wydrukowany odpowiedzi do konsoli, aż do zakończenia operacji analizy. Następnie spowodują danych wyodrębniony tekst w formacie JSON. `"recognitionResults"` Pole zawiera wartość każdego wiersza tekstu, który został wyodrębniony po otrzymaniu i `"understandingResults"` pole zawiera informacje o klucz/wartość dla najbardziej istotnych części odbieranie.

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

W tym przewodniku Szybki Start użyto interfejsu API REST rozpoznawania formularza za pomocą języka Python do nauczenia modelu i uruchom go w przykładowym scenariuszu. Następnie zobacz dokumentację referencyjną, aby zapoznać się z interfejsu API rozpoznawania fragmentów omówiona bardziej szczegółowo.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
