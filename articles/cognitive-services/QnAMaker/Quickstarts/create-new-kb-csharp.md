---
title: 'Szybki start: tworzenie bazy wiedzy — środowisko REST, C# — QnA Maker'
description: Ten oparty na protokole REST przewodnik Szybki start dla języka C# zawiera omówienie programowego tworzenia przykładowej bazy wiedzy usługi QnA Maker, która będzie wyświetlana na pulpicie nawigacyjnym platformy Azure w ramach konta interfejsu API usługi Cognitive Services.
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 78608d3acdfea07f21ccc42e8d530ff502fbb0b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851928"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c-with-rest"></a>Szybki start: tworzenie bazy wiedzy w aplikacji QnA Maker przy użyciu języka C# z rest

Ten przewodnik Szybki start przeprowadzi Cię przez programowe tworzenie i publikowanie przykładowej bazy wiedzy usługi QnA Maker. Usługa QnA Maker automatycznie wyodrębnia pytania i odpowiedzi z częściowo ustrukturyzowanej zawartości, na przykład często zadawanych pytań, ze [źródeł danych](../Concepts/knowledge-base.md). Model bazy wiedzy jest zdefiniowany w formacie JSON wysyłanym w treści żądania interfejsu API.

Ten przewodnik Szybki start wywołuje interfejsy API usługi QnA Maker:
* [Tworzenie bazy wiedzy](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Pobieranie szczegółów operacji](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Przykład dokumentacji referencyjnej](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [C#](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Bieżąca wersja programu [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Musisz mieć [zasób QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby pobrać klucz i punkt końcowy (który zawiera nazwę zasobu), wybierz **szybki start** dla zasobu w witrynie Azure portal.

### <a name="create-a-new-c-application"></a>Tworzenie nowej aplikacji języka C#

Utwórz nową aplikację .NET Core w preferowanym edytorze lub ide.

W oknie konsoli (takim jak cmd, PowerShell `dotnet new` lub Bash) użyj polecenia, `qna-maker-quickstart`aby utworzyć nową aplikację konsoli o nazwie . To polecenie tworzy prosty projekt "Hello World" C# z jednym plikiem źródłowym: *Program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można utworzyć za pomocą:

```dotnetcli
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać żadnych ostrzeżeń ani błędów.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="add-the-required-dependencies"></a>Dodawanie wymaganych zależności

Na początku pliku Program.cs zastąp pojedynczą instrukcję using następującymi wierszami, aby dodać do projektu niezbędne zależności:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Dodawanie wymaganych stałych

W górnej części klasy Program dodaj wymagane stałe, aby uzyskać dostęp do programu QnA Maker.

Ustaw następujące wartości w zmiennych środowiskowych:

* `QNA_MAKER_SUBSCRIPTION_KEY`- **Klucz** jest ciągiem znaków 32 i jest dostępny w witrynie Azure portal, na zasobie QnA Maker, na stronie Szybki start. To nie jest taka sama jak klucz punktu końcowego przewidywania.
* `QNA_MAKER_ENDPOINT`- Punkt **końcowy** jest adresem URL do tworzenia, w formacie `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Nie jest to ten sam adres URL, który jest używany do wykonywania zapytań o punkt końcowy przewidywania.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=17-26 "Add the required constants")]

## <a name="add-the-kb-definition"></a>Dodawanie definicji bazy wiedzy

Po dodaniu stałych dodaj następującą definicję bazy wiedzy:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=28-58 "Add the knowledge base definition")]

## <a name="add-supporting-functions-and-structures"></a>Dodawanie pomocniczych funkcji i struktur
Dodaj następujący blok kodu wewnątrz klasy Program:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=60-99 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Dodawanie żądania POST w celu utworzenia bazy wiedzy

Poniższy kod umożliwia wysłanie żądania HTTPS do interfejsu API usługi QnA Maker w celu utworzenia bazy wiedzy oraz odebranie odpowiedzi:

[!code-csharp[Add PostCreateKB to request via POST](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=145-165 "Add PostCreateKB to request via POST")]

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=101-122 "Add a POST request to create KB")]

To wywołanie interfejsu API zwraca odpowiedź w formacie JSON, która zawiera identyfikator operacji. Użyj tego identyfikatora operacji, aby określić, czy baza wiedzy została pomyślnie utworzona.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Dodawanie żądania GET w celu ustalenia stanu tworzenia

Sprawdź stan operacji.

[!code-csharp[Add GetStatus to request via GET](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=167-187 "Add GetStatus to request via GET")]

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=124-143 "Add GET request to determine creation status")]

To wywołanie interfejsu API zwraca odpowiedź w formacie JSON, która zawiera stan operacji:

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
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
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>Dodawanie metody CreateKB

Poniższa metoda tworzy bazę wiedzy i powtarza sprawdzanie stanu.  Identyfikator **operacji** _tworzenia_ jest zwracany w polu nagłówek odpowiedzi POST **Lokalizacja**, a następnie używany jako część trasy w żądaniu GET. Tworzenie bazy wiedzy może nieco potrwać, dlatego musisz powtarzać wywołania sprawdzenia stanu do czasu uzyskania stanu powodzenia lub niepowodzenia. Po pomyślnym zakończeniu operacji identyfikator bazy wiedzy jest zwracany w elemencie **resourceLocation**.

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=189-254 "Add CreateKB method")]

## <a name="add-the-createkb-method-to-main"></a>Dodawanie metody CreateKB do metody Main

Zmień metodę Main, aby wywołać metodę CreateKB:

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=256-265 "Add CreateKB method")]

## <a name="build-and-run-the-program"></a>Kompilowanie i uruchamianie programu

Skompiluj i uruchom program. Program automatycznie wyśle żądanie do interfejsu API usługi QnA Maker, aby utworzyć bazę wiedzy, a następnie będzie sondować wyniki co 30 sekund. Każda odpowiedź jest wypisywana w oknie konsoli.

Utworzoną bazę wiedzy można wyświetlić w portalu usługi QnA Maker, na stronie [My knowledge bases (Moje bazy wiedzy)](https://www.qnamaker.ai/Home/MyServices).


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://go.microsoft.com/fwlink/?linkid=2092179)