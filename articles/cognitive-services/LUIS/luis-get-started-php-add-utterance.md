---
title: Szybki start — zmiana modelu i uczenie aplikacji LUIS przy użyciu języka PHP — Azure Cognitive Services | Microsoft Docs
description: W tym przewodniku Szybki start języka PHP dodasz przykładowe wypowiedzi do aplikacji Home Automation i przeprowadzisz uczenie aplikacji. Przykładowe wypowiedzi to tekst z rozmowami użytkownika mapowany na intencje. Poprzez zapewnienie przykładowych wypowiedzi dla intencji możesz nauczyć aplikację LUIS, jakim intencjom odpowiada rodzaj tekstu podanego przez użytkownika.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: ae2d3624cb3f8314a613af356730fb1d8b5d4b29
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43771828"
---
# <a name="quickstart-change-model-using-php"></a>Szybki start: zmiana modelu przy użyciu języka PHP 

[!include[Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!include[Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* Najnowsza wersja języka [**PHP**](http://php.net/).
* Upewnij się, że biblioteka openssl jest dostępna jako zależność dla języka PHP.  

[!include[Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Plik JSON z przykładowymi wypowiedziami

[!include[Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Tworzenie kodu przewodnika Szybki start 

Dodaj zależności do pliku o nazwie `add-utterances.php`.

   [!code-php[PHP and LUIS Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/php/add-utterances.php?range=1-22 "PHP and LUIS Dependencies")]

Dodaj żądanie GET używane dla stanu uczenia.

   [!code-php[SendGet](~/samples-luis/documentation-samples/quickstarts/change-model/php/add-utterances.php?range=24-43 "SendGet")]

Dodaj żądanie POST służące do tworzenia wypowiedzi lub uruchamiania uczenia. 

   [!code-php[SendPost](~/samples-luis/documentation-samples/quickstarts/change-model/php/add-utterances.php?range=45-65 "SendPost")]

Dodaj funkcję `AddUtterances`.

   [!code-php[AddUtterances method](~/samples-luis/documentation-samples/quickstarts/change-model/php/add-utterances.php?range=67-72 "AddUtterances method")]


Dodaj funkcję `Train`. 

   [!code-php[Train](~/samples-luis/documentation-samples/quickstarts/change-model/php/add-utterances.php?range=74-81 "Train")]

Dodaj funkcję `Status`.

   [!code-php[Status](~/samples-luis/documentation-samples/quickstarts/change-model/php/add-utterances.php?range=83-87 "Status")]

Aby zarządzać argumentami wiersza polecenia, dodaj główny blok kodu.

   [!code-php[Main code](~/samples-luis/documentation-samples/quickstarts/change-model/php/add-utterances.php?range=89-93 "Main code")]

## <a name="run-code"></a>Uruchamianie kodu

Uruchom aplikację z poziomu wiersza polecenia za pomocą języka PHP.

### <a name="add-an-utterance-from-the-command-line"></a>Dodawanie wypowiedzi z poziomu wiersza polecenia

Uruchom aplikację z poziomu wiersza polecenia za pomocą języka PHP.

Wywołanie pliku `add-utterances.php` spowoduje dodanie wypowiedzi, przeprowadzenie uczenia i uzyskanie stanu uczenia.

```CMD
> php add-utterances.php 
```

Następujący kod JSON jest zwracany z wywołania interfejsu API dodawania wypowiedzi. Pole `response` jest w tym formacie na potrzeby dodanych wypowiedzi. Właściwość `hasError` ma wartość false, co oznacza, że wypowiedź została dodana.  

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

W poniższym kodzie znajduje się wynik pomyślnego żądania uczenia:

```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```


```JSON
Requested training status.
[
   {
      "modelId": "eb2f117c-e10a-463e-90ea-1a0176660acc",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c1bdfbfc-e110-402e-b0cc-2af4112289fb",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "863023ec-2c96-4d68-9c44-34c1cbde8bc9",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "82702162-73ba-4ae9-a6f6-517b5244c555",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "37121f4c-4853-467f-a9f3-6dfc8cad2763",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "de421482-753e-42f5-a765-ad0a60f50d69",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "80f58a45-86f2-4e18-be3d-b60a2c88312e",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c9eb9772-3b18-4d5f-a1e6-e0c31f91b390",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "2afec2ff-7c01-4423-bb0e-e5f6935afae8",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "95a81c87-0d7b-4251-8e07-f28d180886a1",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   }
]
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu przewodnika Szybki start usuń wszystkie pliki utworzone w tym przewodniku Szybki start. 

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Programowe tworzenie aplikacji LUIS](luis-tutorial-node-import-utterances-csv.md)