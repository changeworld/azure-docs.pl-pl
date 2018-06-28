---
title: Samouczek dotyczący dodawania wypowiedzi do aplikacji usługi LUIS przy użyciu języka PHP | Microsoft Docs
description: Z tego samouczka dowiesz się, jak wywołać aplikację usługi LUIS przy użyciu języka PHP.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 59150b7ed6782c28f243041be2ed6aa17e69cc01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263784"
---
# <a name="tutorial-add-utterances-to-app-using-php"></a>Samouczek: dodawanie wypowiedzi do aplikacji przy użyciu języka PHP 
W tym samouczku pokazano, jak napisać program służący do dodawania wypowiedzi do intencji za pomocą interfejsów API tworzenia w języku PHP.

<!-- green checkmark -->
> [!div class="checklist"]
> * Tworzenie projektu konsolowego programu Visual Studio 
> * Dodawanie metody służącej do wywoływania interfejsu API usługi LUIS w celu dodawania wypowiedzi i uczenia aplikacji
> * Dodawanie pliku JSON z przykładowymi wypowiedziami do intencji BookFlight
> * Uruchamianie konsoli i wyświetlanie stanu uczenia dla wypowiedzi

Aby uzyskać więcej informacji, zobacz dokumentację techniczną dotyczącą interfejsów API [dodawania wypowiedzi do intencji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [uczenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) i [stanu uczenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Na potrzeby tego artykułu wymagane jest bezpłatne konto usługi [LUIS][LUIS] w celu tworzenia aplikacji LUIS.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza wersja języka [**PHP**](http://php.net/).
* Upewnij się, że biblioteka openssl jest dostępna jako zależność dla języka PHP.  
* Twój **[klucz tworzenia](luis-concept-keys.md#authoring-key)** usługi LUIS. Możesz go znaleźć w obszarze Ustawienia konta w witrynie internetowej usługi [LUIS](luis-reference-regions.md).
* Twój istniejący [**identyfikator aplikacji**](./luis-get-started-create-app.md) usługi LUIS. Identyfikator aplikacji jest wyświetlany na pulpicie nawigacyjnym aplikacji. Aplikacja usługi LUIS z intencjami i jednostkami użyta w pliku `utterances.json` musi istnieć przed uruchomieniem tego kodu w pliku `add-utterances.php`. Za pomocą kodu znajdującego się tym artykule nie są tworzone intencje ani jednostki. Służy on jedynie do dodawania wypowiedzi do istniejących intencji i jednostek. 
* **Identyfikator wersji** w aplikacji, w której odbierane są wypowiedzi. Domyślny identyfikator to „0.1”.
* Utwórz nowy plik o nazwie `add-utterances.php` w programie VSCode.

> [!NOTE] 
> Kompletny plik `add-utterances.cs` i przykładowy plik `utterances.json` są dostępne w [**repozytorium Github** LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/php/).


## <a name="write-the-php-code"></a>Tworzenie kodu w języku PHP

Dodaj zależności do pliku.

   [!code-php[PHP and LUIS Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=10-29 "PHP and LUIS Dependencies")]

Dodaj żądanie GET używane dla stanu uczenia.

   [!code-php[SendGet](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=31-50 "SendGet")]

Dodaj żądanie POST służące do tworzenia wypowiedzi lub uruchamiania uczenia. 

   [!code-php[SendPost](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=52-72 "SendPost")]

Dodaj funkcję `AddUtterances`.

   [!code-php[AddUtterances method](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=74-79 "AddUtterances method")]


Dodaj funkcję `Train`. 

   [!code-php[Train](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=81-88 "Train")]

Dodaj funkcję `Status`.

   [!code-php[Status](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=90-94 "Status")]

Aby zarządzać argumentami wiersza polecenia, dodaj główny blok kodu.

   [!code-php[Main code](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=96-120 "Main code")]

## <a name="specify-utterances-to-add"></a>Określanie wypowiedzi do dodania
Utwórz plik `utterances.json` i edytuj go w celu określenia **tablicy wypowiedzi** do dodania do aplikacji usługi LUIS. Intencje i jednostki **muszą** znajdować się już w aplikacji LUIS.

> [!NOTE]
> Aplikacja usługi LUIS z intencjami i jednostkami użyta w pliku `utterances.json` musi istnieć przed uruchomieniem tego kodu w pliku `add-utterances.php`. Za pomocą kodu znajdującego się tym artykule nie są tworzone intencje ani jednostki. Służy on jedynie do dodawania wypowiedzi do istniejących intencji i jednostek.

Pole `text` zawiera tekst wypowiedzi. Pole `intentName` musi odpowiadać nazwie wypowiedzi w aplikacji LUIS. Pole `entityLabels` jest wymagane. Jeśli nie chcesz oznaczać jakichkolwiek jednostek, podaj pustą listę, co przedstawiono w poniższym przykładzie:

Jeśli lista entityLabels nie jest pusta, elementy `startCharIndex` i `endCharIndex` muszą oznaczać jednostkę określoną w polu `entityName`. Oba indeksy to liczniki określane od zera, w związku z czym wartość 6 w górnym przykładzie odnosi się do litery „S” z ciągu Seattle, a nie do znaku spacji przed tą literą.

```json
[
    {
        "text": "go to Seattle",
        "intentName": "BookFlight",
        "entityLabels": [
            {
                "entityName": "Location::LocationTo",
                "startCharIndex": 6,
                "endCharIndex": 12
            }
        ]
    },
    {
        "text": "book a flight",
        "intentName": "BookFlight",
        "entityLabels": []
    }
]
```

## <a name="add-an-utterance-from-the-command-line"></a>Dodawanie wypowiedzi z poziomu wiersza polecenia

Uruchom aplikację z poziomu wiersza polecenia za pomocą języka PHP.

Wywołanie pliku `add-utterances.php` tylko z plikiem utterance.json jako argumentem powoduje dodanie nowych wypowiedzi, ale nie uczy aplikacji usługi LUIS za ich pomocą.
````
> php add-utterances.php ./utterances.json
````

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

## <a name="add-an-utterance-and-train-from-the-command-line"></a>Dodawanie wypowiedzi i uczenie z poziomu wiersza polecenia
Wywołaj plik `add-utterance.php` z argumentem `-train` w celu wysłania żądania uczenia. 

````
> php add-utterances.php ./utterances.json -train
````

> [!NOTE]
> Zduplikowane wypowiedzi nie są ponownie dodawane, ale nie powodują wystąpienia błędu. Pole `response` zawiera identyfikator oryginalnej wypowiedzi.

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

Po umieszczeniu żądania uczenia w kolejce zakończenie uczenia może trochę potrwać.

## <a name="get-training-status-from-the-command-line"></a>Pobieranie stanu uczenia z poziomu wiersza polecenia
Wywołaj aplikację z argumentem `-status`, aby sprawdzić stan uczenia i wyświetlić jego szczegóły.

````
> php add-utterances.php -status
````

```
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
Po zakończeniu tego samouczka usuń program Visual Studio i aplikację konsolową, jeśli nie będą Ci już potrzebne. 

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Programowe tworzenie aplikacji LUIS](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website