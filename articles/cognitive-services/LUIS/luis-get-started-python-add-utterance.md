---
title: Samouczek dotyczący dodawania wypowiedzi do aplikacji usługi LUIS przy użyciu języka Python | Microsoft Docs
description: Z tego samouczka dowiesz się, jak wywołać aplikację LUIS przy użyciu języka Python.
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 3e1ca2ea874b6b39ac8a1b1eaa94fe9ff1894ea3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264305"
---
# <a name="tutorial-add-utterances-to-app-using-python"></a>Samouczek: dodawanie wypowiedzi do aplikacji przy użyciu języka Python
W tym samouczku pokazano, jak napisać program służący do dodawania wypowiedzi do intencji za pomocą interfejsów API tworzenia w języku Python.

<!-- green checkmark -->
> [!div class="checklist"]
> * Tworzenie projektu konsolowego programu Visual Studio 
> * Dodawanie metody służącej do wywoływania interfejsu API usługi LUIS w celu dodawania wypowiedzi i uczenia aplikacji
> * Dodawanie pliku JSON z przykładowymi wypowiedziami do intencji BookFlight
> * Uruchamianie konsoli i wyświetlanie stanu uczenia dla wypowiedzi

Aby uzyskać więcej informacji, zobacz dokumentację techniczną dotyczącą interfejsów API [dodawania wypowiedzi do intencji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45), [uczenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) i [stanu uczenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Na potrzeby tego artykułu wymagane jest bezpłatne konto usługi [LUIS][LUIS] w celu tworzenia aplikacji LUIS.

## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko [Python 3.6](https://www.python.org/downloads/) lub nowsze.
* **[Zalecane]** Program [Visual Studio Code](https://code.visualstudio.com/) dla funkcji IntelliSense i debugowania.
* Twój **[klucz tworzenia](luis-concept-keys.md#authoring-key)** usługi LUIS. Możesz go znaleźć w obszarze Ustawienia konta w witrynie internetowej usługi [LUIS](luis-reference-regions.md).
* Twój istniejący [**identyfikator aplikacji**](./luis-get-started-create-app.md) usługi LUIS. Identyfikator aplikacji jest wyświetlany na pulpicie nawigacyjnym aplikacji. Aplikacja usługi LUIS z intencjami i jednostkami użyta w pliku `utterances.json` musi istnieć przed uruchomieniem tego kodu w pliku `add-utterances.js`. Za pomocą kodu znajdującego się tym artykule nie są tworzone intencje ani jednostki. Służy on jedynie do dodawania wypowiedzi do istniejących intencji i jednostek. 
* **Identyfikator wersji** w aplikacji, w której odbierane są wypowiedzi. Domyślny identyfikator to „0.1”.
* Utwórz nowy plik o nazwie `add-utterances-3-6.py` w programie VSCode.

> [!NOTE] 
> Kompletny plik `add-utterances-3-6.py` jest dostępny w [**repozytorium Github** LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/authoring-api-samples/python).


## <a name="write-the-python-code"></a>Pisanie kodu w języku Python

1. Skopiuj następujące fragmenty kodu:

   [!code-python[Console app code that adds an utterance Python 3.6](~/samples-luis/documentation-samples/authoring-api-samples/python/add-utterances-3-6.py)]

## <a name="specify-utterances-to-add"></a>Określanie wypowiedzi do dodania
Utwórz plik `utterances.json` i edytuj go w celu określenia **tablicy wypowiedzi** do dodania do aplikacji usługi LUIS. Intencje i jednostki **muszą** znajdować się już w aplikacji LUIS.

> [!NOTE]
> Aplikacja usługi LUIS z intencjami i jednostkami użyta w pliku `utterances.json` musi istnieć przed uruchomieniem tego kodu w pliku `add-utterances.js`. Za pomocą kodu znajdującego się tym artykule nie są tworzone intencje ani jednostki. Służy on jedynie do dodawania wypowiedzi do istniejących intencji i jednostek.

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

Uruchom aplikację z poziomu wiersza polecenia za pomocą języka Python 3.6.

Wywołanie polecenia add-utterance bez żadnych argumentów powoduje dodanie wypowiedzi do aplikacji bez jej uczenia.

````
> python add-utterances-3-6.py
````

Ten przykład umożliwia utworzenie pliku za pomocą pliku `results.json`, który zawiera wyniki wywołania interfejsu API dodawania wypowiedzi. Pole `response` jest w tym formacie na potrzeby dodanych wypowiedzi. Właściwość `hasError` ma wartość false, co oznacza, że wypowiedź została dodana.  

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
Wywołaj polecenie add-utterance z argumentem `-train`, aby wysłać żądanie uczenia, a następnie wysłać żądanie dotyczące stanu uczenia. Stan jest umieszczany w kolejce natychmiast po rozpoczęciu uczenia. Szczegóły stanu są zapisywane w pliku.

````
> python add-utterances-3-6.py -train
````

> [!NOTE]
> Zduplikowane wypowiedzi nie są ponownie dodawane, ale nie powodują wystąpienia błędu. Pole `response` zawiera identyfikator oryginalnej wypowiedzi.

Wywołanie przykładu z argumentem `-train` spowoduje utworzenie pliku `training-results.json` wskazującego, że żądanie uczenia aplikacji usługi LUIS zostało pomyślnie umieszczone w kolejce. 

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
Wywołaj przykład z argumentem `-status`, aby sprawdzić stan uczenia i zapisać szczegóły stanu do pliku.

````
> python add-utterances-3-6.py -status
````
## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Po zakończeniu tego samouczka usuń program Visual Studio i aplikację konsolową, jeśli nie będą Ci już potrzebne. 

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Programowe tworzenie aplikacji LUIS](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website

