---
title: Tworzenie, publikowanie i odpowiadanie
titleSuffix: QnA Maker - Azure Cognitive Services
description: Ten oparty na protokole REST samouczek przedstawia sposób programowego tworzenia i publikowania bazy wiedzy, a następnie odpowiadania na pytanie z bazy wiedzy.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: a14bc14e6115c1bc25582c3de71382ae17d7debd
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792282"
---
# <a name="tutorial-using-c-create-knowledge-base-then-answer-question"></a>Samouczek: używanie języka C# do tworzenia bazy wiedzy, a następnie odpowiadania na pytanie z bazy wiedzy

Ten samouczek przedstawia sposób programowego tworzenia i publikowania bazy wiedzy, a następnie odpowiadania na pytanie klienta za pomocą bazy wiedzy. 

> [!div class="checklist"]
> * Tworzenie bazy wiedzy 
> * Sprawdzanie stanu tworzenia
> * Uczenie i publikowanie bazy wiedzy
> * Uzyskiwanie informacji o punkcie końcowym
> * Używanie programu Curl do wykonywania zapytań w bazie wiedzy


Ten przewodnik Szybki start wywołuje interfejsy API usługi QnA Maker:

* [Tworzenie bazy wiedzy](https://go.microsoft.com/fwlink/?linkid=2092179)
* [Pobieranie szczegółów operacji](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)
* [Pobieranie szczegółów bazy wiedzy](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/getdetails) 
* [Pobieranie punktów końcowych bazy wiedzy](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/getkeys)
* [Publikowanie](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) 

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza [**wersja programu Visual Studio Community**](https://www.visualstudio.com/downloads/).
* Musisz mieć [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby pobrać klucz, wybierz pozycję **Klucze** w obszarze **Zarządzanie zasobami** na pulpicie nawigacyjnym. 

> [!NOTE] 
> Pliki kompletnego rozwiązania są dostępne w [repozytorium GitHub **Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="create-a-knowledge-base-project"></a>Tworzenie projektu bazy wiedzy

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>Dodawanie wymaganych zależności

W górnej części pliku Program.cs zastąp pojedynczą instrukcję _using_ następującymi wierszami, aby dodać niezbędne zależności do projektu:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-a-kbdetails-class"></a>Dodawanie klasy KBDetails
Dodaj tę klasę KBDetails wewnątrz nawiasów przestrzeni nazw. Ta klasa umożliwia bibliotece NewtonSoft deserializację odpowiedzi JSON do klasy C#.

[!code-csharp[Add a KBDetails class](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=15-26 "Add a KBDetails class")]

## <a name="add-the-required-constants"></a>Dodawanie wymaganych stałych

W górnej części klasy Program dodaj następujące stałe, aby uzyskać dostęp do usługi QnA Maker:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=30-57 "Add the required constants")]

## <a name="add-the-kb-definition"></a>Dodawanie definicji bazy wiedzy

Po stałych dodaj następującą definicję modelu bazy wiedzy:

[!code-csharp[Add the KB definition](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=59-85 "Add the KB definition")]

## <a name="add-supporting-functions-and-structures"></a>Dodawanie pomocniczych funkcji i struktur
Dodaj następujący blok kodu wewnątrz klasy Program:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=87-123 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Dodawanie żądania POST w celu utworzenia bazy wiedzy

Poniższy kod umożliwia wysłanie żądania HTTPS do interfejsu API usługi QnA Maker w celu utworzenia bazy wiedzy oraz odebranie odpowiedzi:

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=124-141 "Add a POST request to create KB")]

To wywołanie interfejsu API zwraca odpowiedź w formacie JSON, która zawiera identyfikator operacji. Później program użyje tego identyfikatora operacji, aby określić, czy baza wiedzy została pomyślnie utworzona. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Dodawanie żądania GET w celu ustalenia stanu tworzenia

Sprawdź stan operacji tworzenia.

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=142-151 "Add GET request to determine creation status")]

To wywołanie interfejsu API zwraca odpowiedź w formacie JSON, która zawiera stan operacji: 

```JSON
{
  "operationState": "Running",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

Powtarzaj wywołanie do momentu uzyskania stanu powodzenia lub niepowodzenia: 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>Dodawanie metody CreateKB

Poniższa metoda hermetyzuje wywołania operacji tworzenia bazy wiedzy i sprawdzania stanu.  **Identyfikator operacji** _create_ jest zwracany w polu **Location** nagłówka odpowiedzi POST, a następnie używany jako część trasy w żądaniu GET. Tworzenie bazy wiedzy może nieco potrwać, dlatego musisz powtarzać wywołania sprawdzenia stanu do czasu uzyskania stanu powodzenia lub niepowodzenia. Po pomyślnym zakończeniu operacji identyfikator bazy wiedzy jest zwracany w elemencie **resourceLocation**. 

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=152-227 "Add GET request to determine creation status")]

## <a name="add-publish-method"></a>Dodawanie metody publikowania

Po pomyślnym utworzeniu bazy wiedzy opublikuj bazę wiedzy. Być może oczekiwano wywołania interfejsu API szkolenia. Nie jest to wymagane w tej wersji. 

Poniższy kod umożliwia wysłanie żądania HTTPS do interfejsu API usługi QnA Maker w celu opublikowania bazy wiedzy oraz odebranie odpowiedzi:

[!code-csharp[Add publish method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=228-259 "Add publish method")]

W przypadku pomyślnego publikowania wywołanie interfejsu API zwraca stan 204 bez jakiejkolwiek zawartości w treści odpowiedzi. Kod przewodnika Szybki start dodaje tekst dla odpowiedzi 204, dzięki czemu możesz zobaczyć wynik.

W przypadku wszystkich innych odpowiedzi są one zwracane w niezmienionej postaci.

## <a name="generating-an-answer"></a>Generowanie odpowiedzi
Aby uzyskać dostęp do bazy wiedzy w celu wysłania pytania i otrzymania najlepszej odpowiedzi, program potrzebuje _hosta punktu końcowego_ z interfejsu API szczegółów bazy wiedzy oraz _klucza podstawowego punktu końcowego_ z interfejsu API punktów końcowych. Te metody są dodawane w poniższych sekcjach wraz z metodą do generowania odpowiedzi. 

W poniższej tabeli przedstawiono sposób, w jaki dane są używane do konstruowania identyfikatora URI:

|Generowanie szablonu identyfikatora URI odpowiedzi|
|--|
|https://**NAZWA HOSTA**.azurewebsites.net/qnamaker/knowledgebases/**KBID**/generateAnswer|

_Podstawowy punkt końcowy_ jest przekazywany jako nagłówek do uwierzytelniania żądania w celu wygenerowania odpowiedzi:

|Nazwa nagłówka|Wartość nagłówka|
|--|--|
|Autoryzacja|`Endpoint` + **podstawowy punkt końcowy**<br>Przykład: `Endpoint xxxxxxx`<br>Zwróć uwagę na odstęp między tekstem `Endpoint` a wartością podstawowego punktu końcowego. 

Treść żądania musi przekazać prawidłowe dane w formacie JSON:

```JSON
{
    question: "What languages does QnA Maker support?"
}
```

## <a name="get-kb-details"></a>Pobieranie szczegółów bazy wiedzy
Dodaj następującą metodę w celu pobrania szczegółów bazy wiedzy. Te szczegóły zawierają nazwę hosta bazy wiedzy. Nazwa hosta to nazwa usługi internetowej platformy Azure dla usługi QnA Maker, wprowadzona podczas tworzenia zasobu usługi QnA Maker. 

[!code-csharp[Get KB Details](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=260-273 "Add publish method")]

To wywołanie interfejsu API zwraca odpowiedź w formacie JSON: 

```JSON
{
  "id": "ZZZ31e19-cba7-48d1-8594-5c4297ecc9c1",
  "hostName": "https://qnamaker-s0-s.azurewebsites.net",
  "lastAccessedTimestamp": "2018-10-11T18:10:05Z",
  "lastChangedTimestamp": "2018-10-11T18:09:37Z",
  "lastPublishedTimestamp": "2018-10-11T18:10:15Z",
  "name": "QnA Maker FAQ from quickstart",
  "userId": "AAAc3841df0b42cdb00f53a49d51a89c",
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

## <a name="get-kb-endpoints"></a>Pobieranie punktów końcowych bazy wiedzy
Dodaj następującą metodę w celu pobrania podstawowych punktów końcowych usługi QnA Maker. Te punkty końcowe nie są powiązane z bazą wiedzy i są prawidłowe dla wszystkich baz wiedzy skojarzonych z kluczami zasobów usługi QnA Maker w witrynie Azure Portal.  

[!code-csharp[Get KB Endpoints](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=274-289 "Get KB Endpoints")]

To wywołanie interfejsu API zwraca odpowiedź w formacie JSON: 

```JSON
{
  "primaryEndpointKey": "AAAb5719-e2f7-4a33-937d-7a3b4736a1be",
  "secondaryEndpointKey": "BBBcba78-c1d2-4166-b98f-c77255aefaba",
  "installedVersion": "4.2.0",
  "lastStableVersion": "4.2.0"
}
```

## <a name="get-an-answer"></a>Uzyskiwanie odpowiedzi
Dodaj następującą metodę w celu uzyskania odpowiedzi na pytanie użytkownika. 

[!code-csharp[Get Answer](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=290-315 "Get Answer")]

To wywołanie interfejsu API zwraca odpowiedź w formacie JSON: 

```JSON
{
  "answers": [
    {
      "questions": [
        "Does QnA Maker support non-English languages?"
      ],
      "answer": "See more details about [supported languages](https://docs.microsoft.com/azure/cognitive-services/qnamaker/overview/languages-supported).\n\n\nIf you have content from multiple languages, be sure to create a separate service for each language.",
      "score": 82.19,
      "id": 11,
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    }
  ]
}
```

## <a name="main-method"></a>Metoda main
Metoda main pokazuje synchroniczne wywołania tworzenia, publikowania i generowania odpowiedzi. 

[!code-csharp[Main method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=316-337 "Main method")]

## <a name="build-and-run-the-program"></a>Kompilowanie i uruchamianie programu

Skompiluj i uruchom program. 

Utworzoną bazę wiedzy można wyświetlić w portalu usługi QnA Maker, na stronie [My knowledge bases (Moje bazy wiedzy)](https://www.qnamaker.ai/Home/MyServices). Kiedy już wiesz, jak używać interfejsu API generowania odpowiedzi, możesz użyć tego interfejsu API z dowolnym językiem lub strukturą żądania HTTP. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://go.microsoft.com/fwlink/?linkid=2092179)
