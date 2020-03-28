---
title: 'Szybki start: szkolenie modelu i wyodrębnianie danych formularza przy użyciu interfejsu API REST za pomocą języka Python — aparat rozpoznawania formularzy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start użyjesz interfejsu API REST rozpoznawania formularzy w języku Python, aby trenować model i wyodrębniać dane z formularzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 66668f46595c22426984a02c489297e962d061d0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118089"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-python"></a>Szybki start: trenuj model aparatu rozpoznawania formularzy i wyodrębniaj dane formularza przy użyciu interfejsu API REST z programem Python

W tym przewodniku Szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy platformy Azure z pythonem do szkolenia i oceniania formularzy w celu wyodrębnienia par i tabel wartości klucza.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten szybki start, musisz mieć:
- [Python](https://www.python.org/downloads/) zainstalowany (jeśli chcesz uruchomić przykład lokalny).
- Zestaw co najmniej pięciu form tego samego typu. Użyjesz tych danych do trenowania modelu. Formularze mogą mieć różne typy plików, ale muszą być tego samego typu dokumentu. Do tego szybkiego startu można użyć [przykładowego zestawu danych.](https://go.microsoft.com/fwlink/?linkid=2090451) Przekaż pliki szkoleniowe do katalogu głównego kontenera magazynu obiektów blob na koncie usługi Azure Storage.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularzy

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Trenuj model aparatu rozpoznawania formularzy

Najpierw musisz zestaw danych szkoleniowych w kontenerze obiektów blob usługi Azure Storage. Powinieneś mieć co najmniej pięć wypełnionych formularzy (dokumentów PDF i/lub obrazów) tego samego typu/struktury co główne dane wejściowe. Można też użyć pojedynczego pustego formularza z dwoma wypełnionymi formularzami. Nazwa pliku pustego formularza musi zawierać słowo "pusty". Zobacz [Tworzenie zestawu danych szkoleniowych dla modelu niestandardowego,](../build-training-data-set.md) aby uzyskać porady i opcje dotyczące tworzenia danych szkoleniowych.

> [!NOTE]
> Za pomocą funkcji danych oznaczonych etykietą można ręcznie oznaczyć niektóre lub wszystkie dane szkoleniowe wcześniej. Jest to bardziej złożony proces, ale wyniki w lepiej wyszkolony model. Więcej informacji można znaleźć w sekcji [Pociąg z etykietami](../overview.md#train-with-labels) w przeglądzie.

Aby wyszkolić model rozpoznawania formularzy z dokumentami w kontenerze obiektów blob platformy Azure, należy wywołać interfejs API **[modelu niestandardowego pociągu,](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** uruchamiając następujący kod języka Python. Przed uruchomieniem kodu należy wprowadzić następujące zmiany:

1. Zamień `<SAS URL>` adres URL sygnatury dostępu współdzielonego (SAS) kontenera magazynu obiektów Blob platformy Azure. Aby pobrać adres URL sygnatury dostępu Współdzielonego, otwórz Eksploratora magazynu Platformy Microsoft Azure, kliknij prawym przyciskiem myszy kontener i wybierz pozycję **Pobierz podpis dostępu współdzielonego**. Upewnij się, że uprawnienia **Do odczytu** i **listy** są zaznaczone, a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość w sekcji **ADRES URL.** Powinien mieć formularz: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
1. Zamień `<subscription key>` klucz subskrypcji skopiowany z poprzedniego kroku.
1. Zamień `<endpoint>` na adres URL punktu końcowego zasobu aparatu rozpoznawania formularzy.
1. Zamień `<Blob folder name>` ścieżkę do folderu w magazynie obiektów blob, w którym znajdują się formularze. Jeśli formularze znajdują się w katalogu głównym kontenera, pozostaw ten ciąg pusty.

    ```python
    ########### Python Form Recognizer Labeled Async Train #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    post_url = endpoint + r"/formrecognizer/v2.0-preview/custom/models"
    source = r"<SAS URL>"
    prefix = "<Blob folder name>"
    includeSubFolders = False
    useLabelFile = False
    
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<subsription key>',
    }
    
    body =  {
        "source": source,
        "sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
        },
        "useLabelFile": useLabelFile
    }
    
    try:
        resp = post(url = post_url, json = body, headers = headers)
        if resp.status_code != 201:
            print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
            quit()
        print("POST model succeeded:\n%s" % resp.headers)
        get_url = resp.headers["location"]
    except Exception as e:
        print("POST model failed:\n%s" % str(e))
        quit() 
    ```
1. Zapisz kod w pliku z rozszerzeniem .py. Na przykład *form-recognizer-train.py*.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python form-recognizer-train.py`.

## <a name="get-training-results"></a>Uzyskaj wyniki treningów

Po rozpoczęciu operacji pociągu, należy użyć zwróconego identyfikatora, aby uzyskać stan operacji. Dodaj następujący kod na dole skryptu Pythona. Używa wartości identyfikatora z wywołania szkoleniowego w nowym wywołaniu interfejsu API. Operacja szkoleniowa jest asynchronizawo, więc ten skrypt wywołuje interfejs API w regularnych odstępach czasu, aż do ukończenia stanu szkolenia. Zalecamy interwał jednej sekundy lub więcej.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = headers)
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
            quit()
        model_status = resp_json["modelInfo"]["status"]
        if model_status == "ready":
            print("Training succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if model_status == "invalid":
            print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
            quit()
        # Training still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET model failed:\n%s" % str(e)
        print(msg)
        quit()
print("Train operation did not complete within the allocated time.")
```

Po zakończeniu procesu szkolenia otrzymasz odpowiedź `201 (Success)` z zawartością JSON, następującą:

```json
{ 
  "modelInfo":{ 
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{ 
    "trainingDocuments":[ 
      { 
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      }
    ],
    "errors":[ 

    ]
  },
  "keys":{ 
    "0":[ 
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

Skopiuj `"modelId"` wartość do użycia w poniższych krokach.

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

Po zakończeniu procesu otrzymasz odpowiedź `200 (Success)` z zawartością JSON w następującym formacie. Odpowiedź została skrócona dla uproszczenia. Główne skojarzenia i tabele pary kluczy/wartości znajdują się w węźle. `"pageResults"` Jeśli określono również wyodrębnianie zwykłego tekstu za pomocą parametru `"readResults"` url *includeTextDetails,* węzeł wyświetli zawartość i pozycje całego tekstu w dokumencie.

```bash
{
  "analyzeResult":{ 
    "readResults":[ 
      { 
        "page":1,
        "width":8.5,
        "height":11.0,
        "angle":0,
        "unit":"inch",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              0.5278,
              1.0597,
              1.4569,
              1.0597,
              1.4569,
              1.4347,
              0.5278,
              1.4347
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  0.5278,
                  1.0597,
                  1.4569,
                  1.0597,
                  1.4569,
                  1.4347,
                  0.5278,
                  1.4347
                ]
              }
            ]
          },
          ...
          { 
            "text":"PT",
            "boundingBox":[ 
              6.2181,
              3.3528,
              6.3944,
              3.3528,
              6.3944,
              3.5417,
              6.2181,
              3.5417
            ],
            "words":[ 
              { 
                "text":"PT",
                "boundingBox":[ 
                  6.2181,
                  3.3528,
                  6.3944,
                  3.3528,
                  6.3944,
                  3.5417,
                  6.2181,
                  3.5417
                ]
              }
            ]
          }
        ]
      }
    ],
    "version":"2.0.0",
    "errors":[ 

    ],
    "documentResults":[ 

    ],
    "pageResults":[ 
      { 
        "page":1,
        "clusterId":1,
        "keyValuePairs":[ 
          { 
            "key":{ 
              "text":"Address:",
              "boundingBox":[ 
                0.7972,
                1.5125,
                1.3958,
                1.5125,
                1.3958,
                1.6431,
                0.7972,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/1/words/0"
              ]
            },
            "value":{ 
              "text":"1 Redmond way Suite 6000 Redmond, WA 99243",
              "boundingBox":[ 
                0.7972,
                1.6764,
                2.15,
                1.6764,
                2.15,
                2.2181,
                0.7972,
                2.2181
              ],
              "elements":[ 
                "#/readResults/0/lines/4/words/0",
                "#/readResults/0/lines/4/words/1",
                "#/readResults/0/lines/4/words/2",
                "#/readResults/0/lines/4/words/3",
                "#/readResults/0/lines/6/words/0",
                "#/readResults/0/lines/6/words/1",
                "#/readResults/0/lines/6/words/2",
                "#/readResults/0/lines/8/words/0"
              ]
            },
            "confidence":0.86
          },
          { 
            "key":{ 
              "text":"Invoice For:",
              "boundingBox":[ 
                4.3903,
                1.5125,
                5.1139,
                1.5125,
                5.1139,
                1.6431,
                4.3903,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/2/words/0",
                "#/readResults/0/lines/2/words/1"
              ]
            },
            "value":{ 
              "text":"Microsoft 1020 Enterprise Way Sunnayvale, CA 87659",
              "boundingBox":[ 
                5.1917,
                1.4458,
                6.6583,
                1.4458,
                6.6583,
                2.0347,
                5.1917,
                2.0347
              ],
              "elements":[ 
                "#/readResults/0/lines/3/words/0",
                "#/readResults/0/lines/5/words/0",
                "#/readResults/0/lines/5/words/1",
                "#/readResults/0/lines/5/words/2",
                "#/readResults/0/lines/7/words/0",
                "#/readResults/0/lines/7/words/1",
                "#/readResults/0/lines/7/words/2"
              ]
            },
            "confidence":0.86
          },
          ...
        ],
        "tables":[ 
          { 
            "caption":null,
            "rows":2,
            "columns":5,
            "cells":[ 
              { 
                "rowIndex":0,
                "colIndex":0,
                "header":true,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5347,
                  2.8722,
                  1.575,
                  2.8722,
                  1.575,
                  3.0028,
                  0.5347,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/9/words/0",
                  "#/readResults/0/lines/9/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":1,
                "header":true,
                "text":"Invoice Date",
                "boundingBox":[ 
                  1.9403,
                  2.8722,
                  2.7569,
                  2.8722,
                  2.7569,
                  3.0028,
                  1.9403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/10/words/0",
                  "#/readResults/0/lines/10/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":2,
                "header":true,
                "text":"Invoice Due Date",
                "boundingBox":[ 
                  3.3403,
                  2.8722,
                  4.4583,
                  2.8722,
                  4.4583,
                  3.0028,
                  3.3403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/11/words/0",
                  "#/readResults/0/lines/11/words/1",
                  "#/readResults/0/lines/11/words/2"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  },
  "lastUpdatedDateTime":"2019-10-07T19:32:18+00:00",
  "status":"succeeded",
  "createdDateTime":"2019-10-07T19:32:15+00:00"
}
```

## <a name="improve-results"></a>Poprawa wyników

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto interfejsu API REST rozpoznawania formularzy z pythonem do wytrenowania modelu i uruchamiania go w przykładowym scenariuszu. Następnie zapoznaj się z dokumentacją odwołania, aby zbadać interfejs API rozpoznawania formularzy bardziej szczegółowo.

> [!div class="nextstepaction"]
> [Dokumentacja referencyjna interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
