---
title: 'Szybki start: wyodrębnianie informacji tekstowych i o układzie za pomocą języka Python — aparat rozpoznawania formularzy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start użyjesz interfejsu API REST układu rozpoznawania formularzy z pythonem do odczytywania danych tekstowych i tabel z formularzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 342ae7e42c85ad661c04ba4ebb6629673f4af4dc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77482280"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>Szybki start: wyodrębnianie informacji tekstowych i układu przy użyciu interfejsu API REST rozpoznawania formularzy w języku Python

W tym przewodniku Szybki start użyjesz interfejsu API REST rozpoznawania formularzy platformy Azure z językiem Python, aby wyodrębnić informacje o układzie tekstu i dane tabeli z dokumentów formularza.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten szybki start, musisz mieć:
- [Python](https://www.python.org/downloads/) zainstalowany (jeśli chcesz uruchomić przykład lokalny).
- Dokument formularza. Możesz pobrać obraz z [przykładowego zestawu danych](https://go.microsoft.com/fwlink/?linkid=2090451) dla tego przewodnika Szybki start.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularzy

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>Analizowanie układu formularza

Aby rozpocząć analizowanie układu, należy **[wywołać interfejs](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** API układu analizy przy użyciu skryptu Języka Python poniżej. Przed uruchomieniem skryptu należy wprowadzić następujące zmiany:

1. Zamień `<Endpoint>` punkt końcowy, który został uzyskany za pomocą subskrypcji aparatu rozpoznawania formularzy.
1. Zastąp `<path to your form>` ścieżką do lokalnego dokumentu formularza.
1. Zamień `<subscription key>` klucz subskrypcji skopiowany z poprzedniego kroku.

    ```python
    ########### Python Form Recognizer Async Layout #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Zapisz kod w pliku z rozszerzeniem .py. Na przykład *form-recognizer-layout.py*.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python form-recognizer-layout.py`.

Otrzymasz odpowiedź `202 (Success)` zawierającą nagłówek **Lokalizacja operacji,** który skrypt zostanie wydrukowany na konsoli. Ten nagłówek zawiera identyfikator operacji, którego można użyć do wykonywania zapytań o stan operacji asynchronicznej i uzyskania wyników. W poniższej wartości przykładowej `operations/` ciąg po jest identyfikatorem operacji.

```console
https://cognitiveservice/formrecognizer/v2.0-preview/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-layout-results"></a>Uzyskaj wyniki układu

Po **wywołaniu interfejsu** API układu analizy, należy **[wywołać Get Analizowanie wynik układu](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** API, aby uzyskać stan operacji i wyodrębnione dane. Dodaj następujący kod na dole skryptu Pythona. Ten kod używa wartości identyfikatora operacji w nowym wywołaniu interfejsu API. Ten skrypt wywołuje interfejs API w regularnych odstępach czasu, dopóki wyniki nie są dostępne. Zalecamy interwał jednej sekundy lub więcej.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Layout results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Layout Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Layout Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Zapisz skrypt.
1. Ponownie użyj `python` polecenia, aby uruchomić próbkę. Na przykład `python form-recognizer-layout.py`.

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Skrypt będzie drukować odpowiedzi do konsoli, dopóki operacja **Analizuj układ** zostanie zakończona. Następnie wydrukuje wyodrębnione dane w formacie JSON. Węzeł `"readResults"` zawiera każdy wiersz tekstu z odpowiednim położeniem pola ograniczającego na stronie. Pole `"pageResults"` zawiera każdy element tekstu w tabelach, z których każdy ma współrzędną wiersza-kolumnę.

Zobacz poniższy obraz faktury i odpowiadające mu dane wyjściowe JSON. Dane wyjściowe zostały skrócone dla uproszczenia.

> [!div class="mx-imgBorder"]
> ![Dokument faktury Contoso z tabelą](../media/contoso-invoice.png)

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-11-12T19:55:36Z",
  "lastUpdatedDateTime":"2019-11-12T19:55:43Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "language":"en",
        "angle":0,
        "width":8.5,
        "height":11,
        "unit":"inch",
        "lines":[ 
          { 
            "language":"en",
            "boundingBox":[ 
              0.5384,
              1.1583,
              1.4466,
              1.1583,
              1.4466,
              1.3534,
              0.5384,
              1.3534
            ],
            "text":"Contoso",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.5384,
                  1.1583,
                  1.4466,
                  1.1583,
                  1.4466,
                  1.3534,
                  0.5384,
                  1.3534
                ],
                "text":"Contoso",
                "confidence":1
              }
            ]
          },
          { 
            "language":"en",
            "boundingBox":[ 
              0.7994,
              1.5143,
              1.3836,
              1.5143,
              1.3836,
              1.6154,
              0.7994,
              1.6154
            ],
            "text":"Address:",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.7994,
                  1.5143,
                  1.3836,
                  1.5143,
                  1.3836,
                  1.6154,
                  0.7994,
                  1.6154
                ],
                "text":"Address:",
                "confidence":1
              }
            ]
          },
          ...
          { 
            "language":"en",
            "boundingBox":[ 
              6.2285,
              3.4114,
              6.3919,
              3.4114,
              6.3919,
              3.5119,
              6.2285,
              3.5119
            ],
            "text":"PT",
            "words":[ 
              { 
                "boundingBox":[ 
                  6.2285,
                  3.4114,
                  6.3919,
                  3.4114,
                  6.3919,
                  3.5119,
                  6.2285,
                  3.5119
                ],
                "text":"PT",
                "confidence":1
              }
            ]
          }
        ]
      }
    ],
    "pageResults":[ 
      { 
        "page":1,
        "tables":[ 
          { 
            "rows":2,
            "columns":6,
            "cells":[ 
              { 
                "rowIndex":0,
                "columnIndex":0,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5075,
                  2.8088,
                  1.9061,
                  2.8088,
                  1.9061,
                  3.3219,
                  0.5075,
                  3.3219
                ],
                "elements":[ 
                  "#/readResults/0/lines/8/words/0",
                  "#/readResults/0/lines/8/words/1"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto interfejsu API REST rozpoznawania formularzy z programem Python, aby wyodrębnić układ tekstowy faktury. Następnie zapoznaj się z dokumentacją odwołania, aby zbadać interfejs API rozpoznawania formularzy bardziej szczegółowo.

> [!div class="nextstepaction"]
> [Dokumentacja referencyjna interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)
