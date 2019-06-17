---
title: 'Szybki start: Uczenie modelu i wyodrębnić dane formularza za pomocą programu cURL - rozpoznawania formularza'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki Start użyjesz interfejsu API REST rozpoznawania formularza za pomocą programu cURL do uczenia modelu i wyodrębnianie danych z formularzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: pafarley
ms.openlocfilehash: aed18cd33078d6af65e749cf9dd4950087b6b72c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063908"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Szybki start: Uczenie modelu rozpoznawania formularza i wyodrębnić dane formularza za pomocą interfejsu API REST za pomocą programu cURL

W tym przewodniku Szybki Start użyjesz interfejsu API REST usługi Azure formularza rozpoznawania za pomocą programu cURL szkolenie i ocenianie formularzy w celu wyodrębnienia pary klucz wartość i tabel.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki Start, musisz mieć:
- Dostęp ograniczony dostęp do aparatu rozpoznawania formularza w wersji zapoznawczej. Aby uzyskać dostęp do wersji zapoznawczej, wypełnij i Prześlij [żądanie dostępu do rozpoznawania formularza](https://aka.ms/FormRecognizerRequestAccess) formularza.
- [cURL](https://curl.haxx.se/windows/) zainstalowane.
- Zestaw co najmniej pięć metod tego samego typu. Użyjesz tych danych do nauczenia modelu. Możesz użyć [przykładowego zestawu danych](https://go.microsoft.com/fwlink/?linkid=2090451) dla tego przewodnika Szybki Start. Przekaż dane do głównego konta usługi Azure Blob Storage.

## <a name="create-a-form-recognizer-resource"></a>Utwórz zasób rozpoznawania formularza

Po nadaniu prawa dostępu do używania rozpoznawania formularza otrzymasz Witaj poczty e-mail przy użyciu kilka linków i zasobów. Użyj linku "Portal platformy Azure" w tej wiadomości, aby Otwórz witrynę Azure portal i Utwórz zasób rozpoznawania formularza. W **Utwórz** okienku, podaj następujące informacje:

|    |    |
|--|--|
| **Nazwa** | Opisowa nazwa zasobu bazy danych. Firma Microsoft zaleca używanie nazwę opisową, na przykład *MyNameFormRecognizer*. |
| **Subskrypcja** | Wybierz subskrypcję platformy Azure, którym udzielono dostępu. |
| **Lokalizacja** | Lokalizacja wystąpienia usługi cognitive Services. Różne lokalizacje może wprowadzić opóźnienie, ale nie mają wpływu na dostępność Twojego zasobu w czasie wykonywania. |
| **Warstwa cenowa** | Koszt zasobu zależy od warstwy cenowej, który wybierzesz i użycie. Aby uzyskać więcej informacji, zobacz omówienie interfejsu API [cennik](https://azure.microsoft.com/pricing/details/cognitive-services/).
| **Grupa zasobów** | [Grupy zasobów platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) który będzie zawierał zasobu. Można utworzyć nową grupę lub dodaj je do wcześniej istniejącej grupy. |

> [!IMPORTANT]
> Zwykle podczas tworzenia zasobu usługi cognitive Services w witrynie Azure portal, masz możliwość utworzenia klucz subskrypcji z wieloma usługami (używane w wielu usługach cognitive) lub klucz subskrypcji jednousługowa (używane tylko z określonej usługi cognitive service). Jednak ponieważ rozpoznawania formularza jest wersja zapoznawcza, nie znajduje się w ramach wielu usług subskrypcji i nie można utworzyć subskrypcji jednousługowa, chyba że za pomocą linku podanego w powitalnej wiadomości e-mail.

Po zakończeniu wdrażania zasobu rozpoznawania formularza Znajdź i wybierz go z **wszystkie zasoby** listy w portalu. Następnie wybierz pozycję **klucze** kartę, aby wyświetlić klucze subskrypcji. Żadnego z nich umożliwia uzyskanie dostępu do zasobu. Skopiuj wartość **klucz 1**. Zostanie użyty w następnej sekcji.

## <a name="train-a-form-recognizer-model"></a>Wytrenuj model rozpoznawania formularza

Po pierwsze należy zestaw danych szkoleniowych w rozszerzeniu Azure Storage blob. Powinien mieć co najmniej pięć przykładowe rodzaje (PDF, dokumentów i/lub obrazów) tego samego typu/struktury jako główny danych wejściowych. Lub za pomocą pojedynczego pusty formularz dwie formy wypełnione. Nazwa pliku pusty formularz musi zawierać słowo "empty".

Aby wytrenuj model rozpoznawania formularza za pomocą dokumentów w kontenerze obiektów blob platformy Azure, należy wywołać **szkolenie** interfejsu API, uruchamiając polecenie programu cURL, która jest zgodna. Przed uruchomieniem polecenia dokonaj następujących zmian:

1. Zastąp `<Endpoint>` z punktem końcowym, uzyskany klucz subskrypcji rozpoznawania formularza. Można je znaleźć zasobu rozpoznawania formularza **Przegląd** kartę.
1. Zastąp `<SAS URL>` z kontenera magazynu obiektów Blob platformy Azure udostępnione adresu URL sygnatury (SAS) lokalizacji danych szkoleniowych dostępu. (Pobierz adres URL sygnatury dostępu Współdzielonego, klikając pozycję "Shared Access Signature" w menu Ustawienia konta magazynu i "Generowanie sygnatury dostępu Współdzielonego i parametry połączenia". Spowoduje to wyświetlenie adres URL SAS usługi Blob. Dostosuj ten adres url, dodając containername po .net / i przed? sv = w adresie url, na przykład:.blob.core.windows.net/ < name_of_your_container > /? sv =... Jest to URL sygnatury dostępu Współdzielonego ma być używany).
1. Zastąp `<subscription key>` z kluczem subskrypcji został skopiowany w poprzednim kroku.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Otrzymasz `200 (Success)` odpowiedzi na następujące dane wyjściowe JSON:

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

Uwaga `"modelId"` wartość. Będzie potrzebny w kolejnych krokach.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Wyodrębnianie pary klucz wartość i tabel z formularzy

Następnie będzie analizowanie dokumentu i wyodrębnić z niego pary klucz wartość i tabel. Wywołaj **modelu — analizowanie** interfejsu API, uruchamiając polecenie programu cURL, która jest zgodna. Przed uruchomieniem polecenia dokonaj następujących zmian:

1. Zastąp `<Endpoint>` z punktem końcowym, uzyskany klucz subskrypcji rozpoznawania formularza. Można je znaleźć zasobu rozpoznawania formularza **Przegląd** kartę.
1. Zastąp `<modelID>` o identyfikatorze modelu, który otrzymał w poprzedniej sekcji.
1. Zastąp `<path to your form>` ze ścieżką pliku formularza (na przykład C:\temp\file.pdf).
1. Zastąp `<file type>` z typem pliku. Obsługiwane typy: pdf, image/jpeg, image/png.
1. Zastąp element `<subscription key>` kluczem subskrypcji.


```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@\"<path to your form>\";type=application/<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

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

W tym przewodniku Szybki Start użyto interfejsu API REST rozpoznawania formularza za pomocą programu cURL uczenia modelu i uruchom je w przykładowym scenariuszu. Następnie zobacz dokumentację referencyjną, aby zapoznać się z interfejsu API rozpoznawania fragmentów omówiona bardziej szczegółowo.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://aka.ms/form-recognizer/api)
