---
title: Samouczek dotyczący dodawania wypowiedzi do aplikacji LUIS przy użyciu języka Java | Microsoft Docs
description: Z tego samouczka dowiesz się, jak wywołać aplikację LUIS przy użyciu języka Java.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 5b9c7b90ca96b23fbabfeb1e1d06e4124e65a0dc
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266043"
---
# <a name="tutorial-add-utterances-to-app-using-java"></a>Samouczek: Dodawanie wypowiedzi do aplikacji przy użyciu języka Java 
W tym samouczku pokazano, jak napisać program służący do dodawania wypowiedzi do intencji za pomocą interfejsów API tworzenia w języku Java.

<!-- green checkmark -->
> [!div class="checklist"]
> * Tworzenie projektu konsolowego programu Visual Studio 
> * Dodawanie metody służącej do wywoływania interfejsu API usługi LUIS w celu dodawania wypowiedzi i uczenia aplikacji
> * Dodawanie pliku JSON z przykładowymi wypowiedziami do intencji BookFlight
> * Uruchamianie konsoli i wyświetlanie stanu uczenia dla wypowiedzi

Aby uzyskać więcej informacji, zobacz dokumentację techniczną dotyczącą interfejsów API [dodawania wypowiedzi do intencji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [uczenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) i [stanu uczenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Na potrzeby tego artykułu wymagane jest bezpłatne konto usługi [LUIS][LUIS] w celu tworzenia aplikacji LUIS.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowszy pakiet [**Java/JDK**](http://www.oracle.com/technetwork/java/javase/downloads/index.html) firmy Oracle.
* [Biblioteka JSON GSON firmy Google](https://github.com/google/gson).
* Twój **[klucz tworzenia](luis-concept-keys.md#authoring-key)** usługi LUIS. Możesz go znaleźć w obszarze Ustawienia konta w witrynie internetowej usługi [LUIS](luis-reference-regions.md).
* Twój istniejący [**identyfikator aplikacji**](./luis-get-started-create-app.md) usługi LUIS. Identyfikator aplikacji jest wyświetlany na pulpicie nawigacyjnym aplikacji. Aplikacja LUIS z intencjami i jednostkami użyta w pliku `utterances.json` musi istnieć przed uruchomieniem tego kodu w pliku `AddUtterances.java`. Za pomocą kodu znajdującego się tym artykule nie będą tworzone intencje ani jednostki. Służy on jedynie do dodawania wypowiedzi do istniejących intencji i jednostek. 
* **Identyfikator wersji** w aplikacji, w której odbierane są wypowiedzi. Domyślny identyfikator to „0.1”.
* Utwórz nowy plik tekstowy o nazwie `AddUtterances.java`.

> [!NOTE] 
> Kompletny plik `add-utterances.cs` i przykładowy plik `utterances.json` są dostępne w [**repozytorium Github** LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/authoring-api-samples/java).


## <a name="write-the-java-code"></a>Tworzenie kodu w języku Java

Dodaj zależności Java do pliku.

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=31-34 "Java Dependencies")]

Tworzenie klasy `AddUtterances`

```Java
public class AddUtterances {
    // Insert code here
}
```

Ta klasa zawiera wszystkie fragmenty kodu znajdujące się poniżej.

Dodaj stałe usługi LUIS do klasy. Skopiuj poniższy kod i zmień na swój klucz tworzenia, identyfikator aplikacji i identyfikator wersji.

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=41-53 "LUIS-based IDs")]

Dodaj metodę do wywołania do interfejsu API usługi LUIS. 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=55-178 "HTTP request to LUIS")]

Dodaj metodę na potrzeby odpowiedzi HTTP do interfejsu API usługi LUIS.

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=180-226 "HTTP response from LUIS")]


Dodaj obsługę wyjątków. 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=228-256 "Exception Handling")]

Dodaj obsługę danych wyjściowych/drukowania.

   [!code-java[Add output handling](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=258-267 "Add configuration information for adding utterance")]

Dodaj funkcję main.

   [!code-java[Add main function](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=269-345 "Add main function")]


## <a name="specify-utterances-to-add"></a>Określanie wypowiedzi do dodania
Utwórz plik `utterances.json` i edytuj go w celu określenia **tablicy wypowiedzi** do dodania do aplikacji usługi LUIS. Intencje i jednostki **muszą** znajdować się już w aplikacji LUIS.

> [!NOTE]
> Aplikacja LUIS z intencjami i jednostkami użyta w pliku `utterances.json` musi istnieć przed uruchomieniem tego kodu w pliku `AddUtterances.java`. Za pomocą kodu znajdującego się tym artykule nie będą tworzone intencje ani jednostki. Służy on jedynie do dodawania wypowiedzi do istniejących intencji i jednostek.

Pole `text` zawiera tekst wypowiedzi. Pole `intentName` musi odpowiadać nazwie wypowiedzi w aplikacji LUIS. Pole `entityLabels` jest wymagane. Jeśli nie chcesz oznaczać jakichkolwiek jednostek, podaj pustą listę, co przedstawiono w poniższym przykładzie.

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

Kompilowanie elementu AddUtterance z zależnościami

```
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

Wywołanie polecenia `AddUtterance` bez żadnych argumentów powoduje dodanie wypowiedzi usługi LUIS do aplikacji bez jej uczenia.
````
> java -classpath .;gson-2.8.2.jar AddUtterances
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
Wywołaj plik `add-utterance` z argumentem `-train` w celu wysłania żądania uczenia. 

````
> java -classpath .;gson-2.8.2.jar AddUtterances -train
````

> [!NOTE]
> Zduplikowane wypowiedzi nie są ponownie dodawane, ale nie powodują wystąpienia błędu. Pole `response` zawiera identyfikator oryginalnej wypowiedzi.

Wywołanie aplikacji z argumentem `-train` spowoduje utworzenie pliku `training-results.json` wskazującego, że żądanie uczenia aplikacji LUIS zostało pomyślnie umieszczone w kolejce. 

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
Wywołaj aplikację z argumentem `-status`, aby sprawdzić stan uczenia i zapisać szczegóły stanu do pliku.

````
> java -classpath .;gson-2.8.2.jar AddUtterances -status
````

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Po zakończeniu tego samouczka usuń program Visual Studio i aplikację konsolową, jeśli nie będą Ci już potrzebne. 

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Programowe tworzenie aplikacji LUIS](luis-tutorial-node-import-utterances-csv.md) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website