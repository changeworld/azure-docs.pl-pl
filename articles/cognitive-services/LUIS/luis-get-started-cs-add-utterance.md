---
title: Samouczek dotyczący dodawania wypowiedzi do aplikacji usługi LUIS przy użyciu języka C# | Microsoft Docs
description: Z tego samouczka dowiesz się, jak wywołać aplikację usługi LUIS przy użyciu języka C#.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: v-geberr
ms.openlocfilehash: d9b3ca46cc635d961edadf3e3555f9656b6b5a1d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264247"
---
# <a name="tutorial-add-utterances-to-a-luis-app-using-c"></a>Samouczek: dodawanie wypowiedzi do aplikacji usługi LUIS przy użyciu języka C# 
W tym samouczku pokazano, jak napisać program służący do dodawania wypowiedzi do intencji za pomocą interfejsów API tworzenia w języku C#.

<!-- green checkmark -->
> [!div class="checklist"]
> * Tworzenie projektu konsolowego programu Visual Studio 
> * Dodawanie metody służącej do wywoływania interfejsu API usługi LUIS w celu dodawania wypowiedzi i uczenia aplikacji
> * Dodawanie pliku JSON z przykładowymi wypowiedziami do intencji BookFlight
> * Uruchamianie konsoli i wyświetlanie stanu uczenia dla wypowiedzi

Aby uzyskać więcej informacji, zobacz dokumentację techniczną dotyczącą interfejsów API [dodawania wypowiedzi do intencji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [uczenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) i [stanu uczenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Na potrzeby tego artykułu wymagane jest bezpłatne konto usługi [LUIS][LUIS] w celu tworzenia aplikacji LUIS.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza [**wersja programu Visual Studio Community**](https://www.visualstudio.com/downloads/). 
* Twój **[klucz tworzenia](luis-concept-keys.md#authoring-key)** usługi LUIS. Możesz go znaleźć w obszarze Ustawienia konta w witrynie internetowej usługi [LUIS](luis-reference-regions.md).
* Twój istniejący [**identyfikator aplikacji**](./luis-get-started-create-app.md) usługi LUIS. Identyfikator aplikacji jest wyświetlany na pulpicie nawigacyjnym aplikacji. Aplikacja LUIS z intencjami i jednostkami użyta w pliku `utterances.json` musi istnieć przed uruchomieniem tego kodu. Za pomocą kodu znajdującego się tym artykule nie są tworzone intencje ani jednostki. Służy on do dodawania wypowiedzi do istniejących intencji i jednostek. 
* **Identyfikator wersji** w aplikacji, w której odbierane są wypowiedzi. Domyślny identyfikator to „0.1”.
* Utwórz nowy plik o nazwie `add-utterances.cs` w programie VSCode.

> [!NOTE] 
> Kompletne rozwiązanie w języku C# zawierające przykładowy plik `utterances.json` jest dostępne w [**repozytorium Github** LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/csharp/).

## <a name="create-the-project-in-visual-studio"></a>Tworzenie projektu w programie Visual Studio

W programie Visual Studio utwórz nową **klasyczną aplikację konsolową systemu Windows** za pomocą programu .NET Framework. 

![Typ projektu programu Visual Studio](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

## <a name="add-the-systemweb-dependency"></a>Dodawanie zależności System.Web

W projekcie programu Visual Studio wymagana jest zależność **System.Web**. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **Odwołania**, a następnie wybierz pozycję **Dodaj odwołanie**.

![Dodawanie odwołania System.Web](./media/luis-quickstart-cs-add-utterance/system.web.png)

## <a name="write-the-c-code"></a>Tworzenie kodu w języku C#
Plik **Program.cs** powinien mieć następującą zawartość:

```CSharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}

```

Dodaj zależności System.IO i System.Net.Http.

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=1-6 "Add the dependencies")]


Dodaj identyfikatory i ciągi usługi LUIS do klasy **Program**.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=12-30&dedent=8 "Add the LUIS IDs and strings")]

Dodaj metodę JsonPrettyPrint.

   [!code-csharp[Add the JsonPrettyPrint method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=32-92 "Add the JsonPrettyPrint method")]

Dodaj żądanie GET służące do tworzenia wypowiedzi lub uruchamiania uczenia. 

   [!code-csharp[SendGet](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=93-103 "SendGet")]


Dodaj żądanie POST służące do tworzenia wypowiedzi lub uruchamiania uczenia. 

   [!code-csharp[SendPost](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=104-115 "SendPost")]

Dodaj funkcję `AddUtterances`.

   [!code-csharp[AddUtterances method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=116-125 "AddUtterances method")]


Dodaj funkcję `Train`. 

   [!code-csharp[Train](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=126-136 "Train")]

Dodaj funkcję `Status`.

   [!code-csharp[Status](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=137-143 "Status")]

Aby zarządzać argumentami, dodaj kody główny.

   [!code-csharp[Main code](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=144-172 "Main code")]

## <a name="specify-utterances-to-add"></a>Określanie wypowiedzi do dodania
Utwórz plik `utterances.json` i edytuj go w celu określenia **tablicy wypowiedzi** do dodania do aplikacji usługi LUIS. Intencje i jednostki **muszą** znajdować się już w aplikacji LUIS.

> [!NOTE]
> Aplikacja LUIS z intencjami i jednostkami użyta w pliku `utterances.json` musi istnieć przed uruchomieniem tego kodu w pliku `add-utterances.cs`. Za pomocą kodu znajdującego się tym artykule nie są tworzone intencje ani jednostki. Służy on jedynie do dodawania wypowiedzi do istniejących intencji i jednostek.

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

## <a name="mark-the-json-file-as-content"></a>Oznaczanie pliku JSON jako zawartości
W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy plik `utterances.json`, a następnie wybierz pozycję **Właściwości**. W oknach właściwości dla pozycji **Akcja kompilacji** wybierz wartość `Content`, a dla pozycji **Kopiuj do katalogu wyjściowego** wartość `Copy Always`.  

![Oznaczanie pliku JSON jako zawartości](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="add-an-utterance-from-the-command-line"></a>Dodawanie wypowiedzi z poziomu wiersza polecenia

Skompiluj i uruchom aplikację z poziomu wiersza polecenia przy użyciu języka C# z katalogu /bin/Debug. Upewnij się, że plik utterances.json również znajduje się w tym katalogu.

Wywołanie pliku utterances.cs tylko z plikiem utterance.json jako argumentem powoduje dodanie nowych wypowiedzi, ale nie uczy aplikacji usługi LUIS za ich pomocą.
````
ConsoleApp\bin\Debug> ConsoleApp1.exe utterances.json
````

W tym wierszu polecenia wyświetlane są wyniki wywołania interfejsu API dodawania wypowiedzi. Pole `response` jest w tym formacie na potrzeby dodanych wypowiedzi. Właściwość `hasError` ma wartość false, co oznacza, że wypowiedź została dodana.  

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
Wywołaj polecenie add-utterance z argumentem `-train` w celu wysłania żądania uczenia. 

````
ConsoleApp\bin\Debug> ConsoleApp1.exe -train utterances.json
````

> [!NOTE]
> Zduplikowane wypowiedzi nie są ponownie dodawane, ale nie powodują wystąpienia błędu. Pole `response` zawiera identyfikator oryginalnej wypowiedzi.

W poniższym kodzie w formacie JSON znajduje się wynik pomyślnego żądania uczenia:

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
ConsoleApp\bin\Debug> ConsoleApp1.exe -status
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
