---
title: 'Szybki start: Uczenie modelu i wyodrębnianie danych formularza z językiem Python — aparat rozpoznawania formularza przy użyciu interfejsu API REST'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki Start użyjesz interfejsu API REST rozpoznawania formularza za pomocą języka Python do uczenia modelu i wyodrębnianie danych z formularzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 04/24/2019
ms.author: pafarley
ms.openlocfilehash: bbc285c35c010c9c0a38e9b3d6938c5dd3b76fe4
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544854"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-using-rest-api-with-python"></a>Szybki start: Wytrenuj model rozpoznawania formularza i wyodrębnianie danych formularza przy użyciu interfejsu API REST przy użyciu języka Python

W tym przewodniku Szybki Start użyjesz interfejsu API REST rozpoznawania formularza za pomocą języka Python do nauczanie i ocena formularzy w celu wyodrębnienia pary klucz wartość i tabel.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

- Należy uzyskać dostęp do korzystania z wersji zapoznawczej ograniczony dostęp do aparatu rozpoznawania formularza. Aby uzyskać dostęp do wersji zapoznawczej, wypełnij i Prześlij [żądanie dostępu do rozpoznawania formularz usługi Cognitive Services](https://aka.ms/FormRecognizerRequestAccess) formularza. 
- Jeśli chcesz uruchomić przykładowy kod lokalnie, musisz mieć zainstalowany język [Python](https://www.python.org/downloads/).
- Musi mieć klucz subskrypcji dla rozpoznawania formularza. Postępuj zgodnie z instrukcjami subskrypcji jednousługowa [Tworzenie konta usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#single-service-subscription) subskrybować rozpoznawania formularza, i Uzyskaj klucz. Nie należy używać subskrypcji wielu usług, ponieważ nie będzie zawierać usługi rozpoznawania formularza.
- Konieczne jest posiadanie minimalny zestaw pięciu rodzaje tego samego typu. Możesz użyć [przykładowego zestawu danych](https://go.microsoft.com/fwlink/?linkid=2090451) dla tego przewodnika Szybki Start.

## <a name="create-and-run-the-sample"></a>Tworzenie i uruchamianie przykładowego kodu

Aby utworzyć i uruchomić przykład, należy wprowadzić następujące zmiany do poniższy fragment kodu:

1. Zastąp wartość `<subscription_key>` kluczem subskrypcji.
1. Zastąp wartość `<Endpoint>` przy użyciu adresu URL punktu końcowego dla zasobu rozpoznawania formularza w regionie platformy Azure, gdzie uzyskać klucze subskrypcji.
1. Zastąp `<SAS URL>` z kontenera usługi Azure Blob Storage, udostępniony uzyskać dostęp do adresu URL sygnatury (SAS), w którym znajduje się dane szkoleniowe.  

    ```python
    ########### Python Form Recognizer Train #############
    from requests import post as http_post

    # Endpoint URL
    base_url = r"<Endpoint>" + "/formrecognizer/v1.0-preview/custom"
    source = r"<SAS URL>"
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<Subscription Key>',
    }
    url = base_url + "/train" 
    body = {"source": source}
    try:
        resp = http_post(url = url, json = body, headers = headers)
        print("Response status code: %d" % resp.status_code)
        print("Response body: %s" % resp.json())
    except Exception as e:
        print(str(e))
    ```
1. Zapisz kod jako plik z rozszerzeniem `.py`. Na przykład `form-recognize-train.py`.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python form-recognize-train.py`.

Zostanie wyświetlony `200 (Success)` odpowiedzi na następujące dane wyjściowe JSON:

```json
{
  "modelId": "59e2185e-ab80-4640-aebc-f3653442617b",
  "trainingDocuments": [
    {
      "documentName": "Invoice_1.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_2.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_3.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_4.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_5.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    }
  ],
  "errors": []
}
```

Zwróć uwagę na `"modelId"` wartość; będzie on potrzebny dla następujących kroków.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Wyodrębnianie pary klucz wartość i tabel z formularzy

Następnie będzie analizowanie dokumentu i wyodrębnić z niego pary klucz wartość i tabel. Wywołaj **modelu — analizowanie** interfejsu API przez wykonanie poniższego skryptu języka Python. Przed uruchomieniem polecenia, należy wprowadzić następujące zmiany:

1. Zastąp `<Endpoint>` z punktem końcowym został uzyskany z kluczem subskrypcji rozpoznawania formularza. Mogą ją odnaleźć kart Przegląd zasobów rozpoznawania formularza.
1. Zastąp `<File Path>` z lokalizacji ścieżki pliku lub adres URL, w którym znajduje się formularz, aby wyodrębnić dane.
1. Zastąp `<modelID>` o identyfikatorze modelu otrzymany w poprzednim kroku uczenia modelu.
1. Zastąp `<file type>` z typem pliku — obsługiwane typy plików pdf, image/jpeg, image/png.
1. Zastąp element `<subscription key>` kluczem subskrypcji.

    ```python
    ########### Python Form Recognizer Analyze #############
    from requests import post as http_post
    
    # Endpoint URL
    base_url = r"<Endpoint>" + "/formrecognizer/v1.0-preview/custom"
    file_path = r"<File Path>"
    model_id = "<modelID>"
    headers = {
        # Request headers
        'Content-Type': 'application/<file type>',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        url = base_url + "/models/" + model_id + "/analyze" 
        with open(file_path, "rb") as f:
            data_bytes = f.read()  
        resp = http_post(url = url, data = data_bytes, headers = headers)
        print("Response status code: %d" % resp.status_code)    
        print("Response body:\n%s" % resp.json())   
    except Exception as e:
        print(str(e))
    ```

1. Zapisz kod jako plik z rozszerzeniem `.py`. Na przykład `form-recognize-analyze.py`.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Odpowiedź oznaczająca Powodzenie są zwracane w formacie JSON i reprezentuje wyodrębnione pary klucz wartość i tabele wyodrębnione z formularza.

```bash
{
  "status": "success",
  "pages": [
    {
      "number": 1,
      "height": 792,
      "width": 612,
      "clusterId": 0,
      "keyValuePairs": [
        {
          "key": [
            {
              "text": "Address:",
              "boundingBox": [
                57.4,
                683.1,
                100.5,
                683.1,
                100.5,
                673.7,
                57.4,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "1 Redmond way Suite",
              "boundingBox": [
                57.4,
                671.3,
                154.8,
                671.3,
                154.8,
                659.2,
                57.4,
                659.2
              ],
              "confidence": 0.86
            },
            {
              "text": "6000 Redmond, WA",
              "boundingBox": [
                57.4,
                657.1,
                146.9,
                657.1,
                146.9,
                645.5,
                57.4,
                645.5
              ],
              "confidence": 0.86
            },
            {
              "text": "99243",
              "boundingBox": [
                57.4,
                643.4,
                85,
                643.4,
                85,
                632.3,
                57.4,
                632.3
              ],
              "confidence": 0.86
            }
          ]
        },
        {
          "key": [
            {
              "text": "Invoice For:",
              "boundingBox": [
                316.1,
                683.1,
                368.2,
                683.1,
                368.2,
                673.7,
                316.1,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "Microsoft",
              "boundingBox": [
                374,
                687.9,
                418.8,
                687.9,
                418.8,
                673.7,
                374,
                673.7
              ],
              "confidence": 1
            },
            {
              "text": "1020 Enterprise Way",
              "boundingBox": [
                373.9,
                673.5,
                471.3,
                673.5,
                471.3,
                659.2,
                373.9,
                659.2
              ],
              "confidence": 1
            },
            {
              "text": "Sunnayvale, CA 87659",
              "boundingBox": [
                373.8,
                659,
                479.4,
                659,
                479.4,
                645.5,
                373.8,
                645.5
              ],
              "confidence": 1
            }
          ]
        }
      ],
      "tables": [
        {
          "id": "table_0",
          "columns": [
            {
              "header": [
                {
                  "text": "Invoice Number",
                  "boundingBox": [
                    38.5,
                    585.2,
                    113.4,
                    585.2,
                    113.4,
                    575.8,
                    38.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "34278587",
                    "boundingBox": [
                      38.5,
                      547.3,
                      82.8,
                      547.3,
                      82.8,
                      537,
                      38.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Date",
                  "boundingBox": [
                    139.7,
                    585.2,
                    198.5,
                    585.2,
                    198.5,
                    575.8,
                    139.7,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/18/2017",
                    "boundingBox": [
                      139.7,
                      546.8,
                      184,
                      546.8,
                      184,
                      537,
                      139.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Due Date",
                  "boundingBox": [
                    240.5,
                    585.2,
                    321,
                    585.2,
                    321,
                    575.8,
                    240.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/24/2017",
                    "boundingBox": [
                      240.5,
                      546.8,
                      284.8,
                      546.8,
                      284.8,
                      537,
                      240.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Charges",
                  "boundingBox": [
                    341.3,
                    585.2,
                    381.2,
                    585.2,
                    381.2,
                    575.8,
                    341.3,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "$56,651.49",
                    "boundingBox": [
                      387.6,
                      546.4,
                      437.5,
                      546.4,
                      437.5,
                      537,
                      387.6,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "VAT ID",
                  "boundingBox": [
                    442.1,
                    590,
                    474.8,
                    590,
                    474.8,
                    575.8,
                    442.1,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "PT",
                    "boundingBox": [
                      447.7,
                      550.6,
                      460.4,
                      550.6,
                      460.4,
                      537,
                      447.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            }
          ]
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku użyto interfejsów API REST rozpoznawania formularza za pomocą języka Python do uczenia modelu i uruchomienia jej w przykładowym scenariuszu. Następnie zobacz dokumentację referencyjną, aby zapoznać się z interfejsu API rozpoznawania fragmentów omówiona bardziej szczegółowo.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://aka.ms/form-recognizer/api)
