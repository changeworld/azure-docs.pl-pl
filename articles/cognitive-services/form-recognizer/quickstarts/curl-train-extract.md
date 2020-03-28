---
title: 'Szybki start: szkolenie modelu i wyodrębnianie danych formularza przy użyciu cURL — aparatu rozpoznawania formularzy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start użyjesz interfejsu API REST rozpoznawania formularzy z cURL do szkolenia modelu i wyodrębniania danych z formularzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 32756187852de0834afc1dc034d3f7419f0c8087
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118388"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Szybki start: szkolenie modelu rozpoznawania formularzy i wyodrębnianie danych formularza przy użyciu interfejsu API REST z cURL

W tym przewodniku Szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy platformy Azure z cURL do szkolenia i oceniania formularzy w celu wyodrębnienia par i tabel wartości klucza.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten szybki start, musisz mieć:
- [cURL.](https://curl.haxx.se/windows/)
- Zestaw co najmniej sześciu form tego samego typu. Użyjesz pięciu z nich, aby wyszkolić model, a następnie przetestujesz go z szóstą formą. Formularze mogą mieć różne typy plików, ale muszą być tego samego typu dokumentu. Do tego szybkiego startu można użyć [przykładowego zestawu danych.](https://go.microsoft.com/fwlink/?linkid=2090451) Przekaż pliki szkoleniowe do katalogu głównego kontenera magazynu obiektów blob na koncie usługi Azure Storage. Można umieścić pliki testowe w osobnym folderze.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularzy

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Trenuj model aparatu rozpoznawania formularzy

Najpierw musisz zestaw danych szkoleniowych w obiekcie blob usługi Azure Storage. Powinieneś mieć co najmniej pięć wypełnionych formularzy (dokumentów PDF i/lub obrazów) tego samego typu/struktury co główne dane wejściowe. Można też użyć pojedynczego pustego formularza z dwoma wypełnionymi formularzami. Nazwa pliku pustego formularza musi zawierać słowo "pusty". Zobacz [Tworzenie zestawu danych szkoleniowych dla modelu niestandardowego,](../build-training-data-set.md) aby uzyskać porady i opcje dotyczące tworzenia danych szkoleniowych.

> [!NOTE]
> Za pomocą funkcji danych oznaczonych etykietą można ręcznie oznaczyć niektóre lub wszystkie dane szkoleniowe wcześniej. Jest to bardziej złożony proces, ale wyniki w lepiej wyszkolony model. Zobacz [Train with labels](../overview.md#train-with-labels) sekcji przeglądu, aby dowiedzieć się więcej o tej funkcji.

Aby wyszkolić model rozpoznawania formularzy z dokumentami w kontenerze obiektów blob platformy Azure, należy wywołać interfejs API **[modelu niestandardowego pociągu,](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** uruchamiając następujące polecenie cURL. Przed uruchomieniem polecenia należy wprowadzić następujące zmiany:

1. Zamień `<Endpoint>` punkt końcowy, który został uzyskany za pomocą subskrypcji aparatu rozpoznawania formularzy.
1. Zamień `<subscription key>` klucz subskrypcji skopiowany z poprzedniego kroku.
1. Zamień `<SAS URL>` adres URL sygnatury dostępu współdzielonego (SAS) kontenera magazynu obiektów Blob platformy Azure. Aby pobrać adres URL sygnatury dostępu Współdzielonego, otwórz Eksploratora magazynu Platformy Microsoft Azure, kliknij prawym przyciskiem myszy kontener i wybierz pozycję **Pobierz podpis dostępu współdzielonego**. Upewnij się, że uprawnienia **Do odczytu** i **listy** są zaznaczone, a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość w sekcji **ADRES URL.** Powinien mieć formularz: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Otrzymasz odpowiedź `201 (Success)` z nagłówkiem **Lokalizacja.** Wartość tego nagłówka jest identyfikator nowego modelu jest szkolony. 

## <a name="get-training-results"></a>Uzyskaj wyniki treningów

Po rozpoczęciu operacji pociągu, należy użyć nowej operacji, **[Pobierz model niestandardowy,](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetCustomModel)** aby sprawdzić stan szkolenia. Przekaż identyfikator modelu do tego wywołania interfejsu API, aby sprawdzić stan szkolenia:

1. Zamień `<Endpoint>` punkt końcowy uzyskany za pomocą klucza subskrypcji aparatu rozpoznawania formularzy.
1. Zamień `<subscription key>` na klucz subskrypcji
1. Zamień `<model ID>` identyfikator modelu otrzymany w poprzednim kroku

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Otrzymasz odpowiedź `200 (Success)` z treścią JSON w następującym formacie. Zwróć `"status"` uwagę na to pole. Będzie to miało `"ready"` wartość po zakończeniu szkolenia. Jeśli model nie jest zakończony szkolenia, należy ponownie zbadać usługi przez ponowne uruchomienie polecenia. Zaleca się interwał jednej sekundy lub więcej między wywołaniami.

To `"modelId"` pole zawiera identyfikator modelu, którego trenujesz. Będzie to potrzebne do następnego kroku.

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

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analizowanie formularzy dla par klucza i wartości i tabel

Następnie użyjesz nowo przeszkolonego modelu do analizowania dokumentu i wyodrębniania z niego par klucza wartości i tabel. Wywołanie interfejsu API **[formularza analizy,](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** uruchamiając następujące polecenie cURL. Przed uruchomieniem polecenia należy wprowadzić następujące zmiany:

1. Zamień `<Endpoint>` punkt końcowy uzyskany z klucza subskrypcji aparatu rozpoznawania formularzy. Można go znaleźć na karcie **Omówienie** zasobu aparatu rozpoznawania formularzy.
1. Zamień `<model ID>` identyfikator modelu otrzymany w poprzedniej sekcji.
1. Zamień `<SAS URL>` na adres URL sygnatury dostępu Współdzielonego do pliku w magazynie platformy Azure. Wykonaj kroki opisane w sekcji Szkolenia, ale zamiast otrzymywać adres URL sygnatury dostępu Współdzielonego dla całego kontenera obiektów blob, pobierz go dla określonego pliku, który chcesz przeanalizować.
1. Zastąp element `<subscription key>` kluczem subskrypcji.

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

Otrzymasz odpowiedź `202 (Success)` z nagłówkiem **Lokalizacja operacji.** Wartość tego nagłówka zawiera identyfikator wyników używany do śledzenia wyników operacji Analizowanie. Zapisz ten identyfikator wyników w następnym kroku.

## <a name="get-the-analyze-results"></a>Uzyskaj wyniki analizy

Użyj następującego interfejsu API, aby zbadać wyniki operacji Analizowanie.

1. Zamień `<Endpoint>` punkt końcowy uzyskany z klucza subskrypcji aparatu rozpoznawania formularzy. Można go znaleźć na karcie **Omówienie** zasobu aparatu rozpoznawania formularzy.
1. Zamień `<result ID>` identyfikator otrzymany w poprzedniej sekcji.
1. Zastąp element `<subscription key>` kluczem subskrypcji.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Otrzymasz odpowiedź `200 (Success)` z treścią JSON w następującym formacie. Dane wyjściowe zostały skrócone dla uproszczenia. Zwróć `"status"` uwagę na pole w pobliżu dołu. Będzie to miało `"succeeded"` wartość po zakończeniu operacji analizy. Jeśli operacja Analizowanie nie została ukończona, należy ponownie zbadać usługę, ponownie korzystając z polecenia. Zaleca się interwał jednej sekundy lub więcej między wywołaniami.

Główne skojarzenia i tabele pary kluczy/wartości znajdują się w węźle. `"pageResults"` Jeśli określono również wyodrębnianie zwykłego tekstu za pomocą parametru `"readResults"` url *includeTextDetails,* węzeł wyświetli zawartość i pozycje całego tekstu w dokumencie.

```json
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

W tym przewodniku Szybki start użyto interfejsu API REST rozpoznawania formularzy z cURL do wytrenowania modelu i uruchamiania go w przykładowym scenariuszu. Następnie zapoznaj się z dokumentacją odwołania, aby zbadać interfejs API rozpoznawania formularzy bardziej szczegółowo.

> [!div class="nextstepaction"]
> [Dokumentacja referencyjna interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
