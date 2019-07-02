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
ms.openlocfilehash: 6119bacee7be65588f2d9cb5becb86296fcf1559
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502829"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-python"></a>Szybki start: Wytrenuj model rozpoznawania formularza i wyodrębnić dane formularza za pomocą interfejsu API REST przy użyciu języka Python

W tym przewodniku Szybki Start użyjesz interfejsu API REST usługi Azure formularza rozpoznawania za pomocą języka Python szkolenie i ocenianie formularzy w celu wyodrębnienia pary klucz wartość i tabel.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki Start, musisz mieć:
- Dostęp ograniczony dostęp do aparatu rozpoznawania formularza w wersji zapoznawczej. Aby uzyskać dostęp do wersji zapoznawczej, wypełnij i Prześlij [żądanie dostępu do rozpoznawania formularza](https://aka.ms/FormRecognizerRequestAccess) formularza.
- [Python](https://www.python.org/downloads/) zainstalowany (Jeśli chcesz uruchamianie przykładu lokalnie).
- Zestaw co najmniej pięć metod tego samego typu. Użyjesz tych danych do nauczenia modelu. Możesz użyć [Przykładowy zestaw danych](https://go.microsoft.com/fwlink/?linkid=2090451) dla tego przewodnika Szybki Start. Przekaż dane do głównego konta usługi Azure Blob Storage.

## <a name="create-a-form-recognizer-resource"></a>Utwórz zasób rozpoznawania formularza

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Wytrenuj model rozpoznawania formularza

Po pierwsze należy zestaw danych szkoleniowych w kontenerze obiektów blob usługi Azure Storage. Powinien mieć co najmniej pięć przykładowe rodzaje (PDF, dokumentów i/lub obrazów) tego samego typu/struktury jako główny danych wejściowych. Lub za pomocą pojedynczego pusty formularz dwie formy wypełnione. Nazwa pliku pusty formularz musi zawierać słowo "empty".

Aby wytrenuj model rozpoznawania formularza za pomocą dokumentów w kontenerze obiektów blob platformy Azure, należy wywołać **szkolenie** interfejsu API, uruchamiając kod, który następuje po języka python. Przed uruchomieniem kodu, dokonaj następujących zmian:

1. Zastąp `<Endpoint>` przy użyciu adresu URL punktu końcowego dla zasobu rozpoznawania formularza w regionie platformy Azure, gdzie uzyskać klucze subskrypcji.
1. Zastąp `<SAS URL>` udostępnionych kontenera magazynu obiektów blob Azure dostęp do adresu URL sygnatury (SAS). Aby pobrać to, Otwórz Eksplorator usługi Microsoft Azure Storage, kliknij prawym przyciskiem myszy kontener, a wybierz **sygnatury dostępu współdzielonego Get**. Kliknij przycisk Dalej okno dialogowe, a następnie skopiuj wartość w **adresu URL** sekcji. Powinien on mieć postać: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
1. Zastąp `<Subscription key>` z kluczem subskrypcji został skopiowany w poprzednim kroku.
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
1. Zapisz kod w pliku z rozszerzeniem py. Na przykład *formularza rozpoznaje train.py*.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python form-recognize-train.py`.

Otrzymasz `200 (Success)` odpowiedź z tych danych wyjściowych JSON:

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

Uwaga `"modelId"` wartość. Będzie potrzebny dla następujących kroków.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Wyodrębnianie pary klucz wartość i tabel z formularzy

Następnie będzie analizowanie dokumentu i wyodrębnić z niego pary klucz wartość i tabel. Wywołaj **modelu — analizowanie** interfejsu API, uruchamiając skrypt w języku Python, która jest zgodna. Przed uruchomieniem polecenia dokonaj następujących zmian:

1. Zastąp `<Endpoint>` przy użyciu punktu końcowego, który został uzyskany z kluczem subskrypcji rozpoznawania formularza. Można je znaleźć zasobu rozpoznawania formularza **Przegląd** kartę.
1. Zastąp `<path to your form>` ze ścieżką pliku formularza (na przykład C:\temp\file.pdf).
1. Zastąp `<modelID>` o identyfikatorze modelu odebrane w poprzedniej sekcji.
1. Zastąp `<file type>` z typem pliku. Obsługiwane typy: `application/pdf`, `image/jpeg`, `image/png`.
1. Zastąp element `<subscription key>` kluczem subskrypcji.

    ```python
    ########### Python Form Recognizer Analyze #############
    from requests import post as http_post
    
    # Endpoint URL
    base_url = r"<Endpoint>" + "/formrecognizer/v1.0-preview/custom"
    file_path = r"<path to your form>"
    model_id = "<modelID>"
    headers = {
        # Request headers
        'Content-Type': '<file type>',
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

1. Zapisz kod w pliku z rozszerzeniem py. Na przykład *formularza rozpoznaje analyze.py*.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Odpowiedź sukcesu jest zwracany w formacie JSON. Reprezentuje pary klucz wartość i tabele wyodrębnione z formularza:

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

W tym przewodniku Szybki Start użyto interfejsu API REST rozpoznawania formularza za pomocą języka Python do nauczenia modelu i uruchom go w przykładowym scenariuszu. Następnie zobacz dokumentację referencyjną, aby zapoznać się z interfejsu API rozpoznawania fragmentów omówiona bardziej szczegółowo.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://aka.ms/form-recognizer/api)
