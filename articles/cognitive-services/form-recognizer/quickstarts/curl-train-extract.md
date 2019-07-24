---
title: 'Szybki start: Uczenie modelu i wyodrębnianie danych formularza przy użyciu narzędzia do rozpoznawania formularzy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy z zwinięciem do uczenia modelu i wyodrębnienia danych z formularzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: b6af9e512d5df7f4701ad981a0db89278873ec7e
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442887"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Szybki start: Uczenie modelu aparatu rozpoznawania formularzy i wyodrębnianie danych formularza przy użyciu interfejsu API REST z zwinięciem

W tym przewodniku szybki start użyjesz interfejsu API REST usługi Azure Forms rozpoznającego z zwinięciem, aby przeszkolić i wypróbować formularze w celu wyodrębnienia par klucz-wartość i tabel.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki Start, musisz dysponować:
- Dostęp do programu rozpoznawania formularzy z ograniczonym dostępem. Aby uzyskać dostęp do wersji zapoznawczej, Wypełnij i Prześlij formularz [żądania dostępu do aparatu rozpoznawania formularza](https://aka.ms/FormRecognizerRequestAccess) .
- zainstalowano [zwinięcie](https://curl.haxx.se/windows/) .
- Zestaw składający się z co najmniej pięciu form tego samego typu. Te dane będą używane do uczenia modelu. Możesz użyć przykładowego [zestawu danych](https://go.microsoft.com/fwlink/?linkid=2090451) dla tego przewodnika Szybki Start. Przekaż dane do katalogu głównego kontenera magazynu obiektów BLOB na koncie usługi Azure Storage.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularza

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Uczenie modelu aparatu rozpoznawania formularzy

Najpierw będziesz potrzebować zestawu danych szkoleniowych w obiekcie blob usługi Azure Storage. Należy mieć co najmniej pięć wypełnionych formularzy (dokumentów PDF i/lub obrazów) tego samego typu i struktury co główne dane wejściowe. Lub można użyć pojedynczego pustego formularza z dwoma wypełnionymi formularzami. Nazwa pliku pustego formularza musi zawierać słowo "Empty". Zapoznaj się z tematem [Tworzenie zestawu danych szkoleniowych dla modelu niestandardowego](../build-training-data-set.md) w celu uzyskania wskazówek i opcji tworzenia danych szkoleniowych.

Aby przeprowadzić uczenie modelu aparatu rozpoznawania formularzy przy użyciu dokumentów w kontenerze obiektów blob platformy  Azure, Wywołaj interfejs API uczenia, uruchamiając następujące polecenie. Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień `<Endpoint>` na punkt końcowy uzyskany z klucza subskrypcji aparatu rozpoznawania formularza. Można go znaleźć na karcie **Przegląd** zasobów aparatu rozpoznawania formularza.
1. Zamień `<subscription key>` na klucz subskrypcji skopiowany z poprzedniego kroku.
1. Zamień `<SAS URL>` na adres URL sygnatury dostępu współdzielonego (SAS) kontenera magazynu obiektów blob platformy Azure. Aby pobrać adres URL SAS, Otwórz Eksplorator usługi Microsoft Azure Storage, kliknij prawym przyciskiem myszy kontener i wybierz polecenie **Pobierz sygnaturę dostępu**współdzielonego. Upewnij się, że uprawnienia do **odczytu** i **listy** są zaznaczone, a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość z sekcji **URL** . Powinna mieć postać: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Otrzymasz `200 (Success)` odpowiedź z następującym wyjściem JSON:

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

Zwróć uwagę `"modelId"` na wartość. Będzie ona potrzebna w poniższych krokach.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Wyodrębnij pary klucz-wartość i tabele z formularzy

Następnie analizujesz dokument i wyodrębniasz z niego pary klucz-wartość i tabele. Wywołaj interfejs API **analizy modelu** , uruchamiając poniższe polecenie. Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień `<Endpoint>` na punkt końcowy uzyskany z klucza subskrypcji aparatu rozpoznawania formularza. Można go znaleźć na karcie **Przegląd** zasobów aparatu rozpoznawania formularza.
1. Zamień `<modelID>` na identyfikator modelu otrzymany w poprzedniej sekcji.
1. Zamień `<path to your form>` na ścieżkę pliku formularza (na przykład C:\temp\file.PDF).
1. Zamień `<file type>` na typ pliku. Obsługiwane typy: `application/pdf`, `image/jpeg`, `image/png`.
1. Zastąp element `<subscription key>` kluczem subskrypcji.


```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@\"<path to your form>\";type=<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

W formacie JSON zostanie zwrócona odpowiedź o powodzeniu. Reprezentuje pary klucz-wartość i tabele wyodrębnione z formularza:

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

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto interfejsu API REST aparatu rozpoznawania formularzy z zwinięciem do uczenia modelu i uruchomienia go w przykładowym scenariuszu. Następnie zapoznaj się z dokumentacją referencyjną w celu eksplorowania interfejsu API rozpoznawania formularzy.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://aka.ms/form-recognizer/api)
