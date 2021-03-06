---
title: 'Szybki start: tworzenie bazy wiedzy — REST, Python — QnA Maker'
description: Ten oparty na protokole REST przewodnik Szybki start dla języka Python zawiera omówienie programowego tworzenia przykładowej bazy wiedzy usługi QnA Maker, która będzie wyświetlana na pulpicie nawigacyjnym platformy Azure w ramach konta interfejsu API usługi Cognitive Services.
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: bb51a47efc7bcae5014d5ea004674fed7cb33fe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851818"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-python"></a>Szybki start: tworzenie bazy wiedzy w usłudze QnA Maker przy użyciu języka Python

Ten przewodnik Szybki start przeprowadzi Cię przez programowe tworzenie i publikowanie przykładowej bazy wiedzy usługi QnA Maker. Usługa QnA Maker automatycznie wyodrębnia pytania i odpowiedzi z częściowo ustrukturyzowanej zawartości, na przykład często zadawanych pytań, ze [źródeł danych](../Concepts/knowledge-base.md). Model bazy wiedzy jest zdefiniowany w formacie JSON wysyłanym w treści żądania interfejsu API.

Ten przewodnik Szybki start wywołuje interfejsy API usługi QnA Maker:
* [Tworzenie bazy wiedzy](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Pobieranie szczegółów operacji](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Przykład dokumentacji referencyjnej](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [języka Python](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Środowisko Python w wersji 3.7](https://www.python.org/downloads/)
* Musisz mieć [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby pobrać klucz i punkt końcowy (który zawiera nazwę zasobu), wybierz **szybki start** dla zasobu w witrynie Azure portal.

## <a name="create-a-knowledge-base-python-file"></a>Tworzenie pliku Python bazy wiedzy

Utwórz plik o nazwie `create-new-knowledge-base-3x.py`.

## <a name="add-the-required-dependencies"></a>Dodawanie wymaganych zależności

Na początku pliku `create-new-knowledge-base-3x.py` dodaj następujące wiersze, aby dodać niezbędne zależności do projektu:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Dodawanie wymaganych stałych
Po poprzednich wymaganych zależnościach dodaj wymagane stałe umożliwiające dostęp do usługi QnA Maker. Zastąp `<your-qna-maker-subscription-key>` wartość `<your-resource-name>` i z własnym kluczem QnA Maker i nazwą zasobu.

W górnej części klasy Program dodaj wymagane stałe, aby uzyskać dostęp do programu QnA Maker.

Ustaw następujące wartości:

* `<your-qna-maker-subscription-key>`- **Klucz** jest ciągiem znaków 32 i jest dostępny w witrynie Azure portal, na zasobie QnA Maker, na stronie Szybki start. To nie jest taka sama jak klucz punktu końcowego przewidywania.
* `<your-resource-name>`- **Nazwa zasobu** jest używana do konstruowania tworzenia adresu URL `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`punktu końcowego do tworzenia, w formacie . Nie jest to ten sam adres URL, który jest używany do wykonywania zapytań o punkt końcowy przewidywania.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=5-13 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Dodawanie definicji modelu bazy wiedzy

Po stałych dodaj następującą definicję modelu bazy wiedzy. Po definicji model jest konwertowany na ciąg.

[!code-python[Add the KB model definition](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=15-41 "Add the KB model definition")]

## <a name="add-supporting-function"></a>Dodawanie funkcji pomocniczej

Dodaj następującą funkcję, aby wyświetlić dane JSON w czytelnym formacie:

[!code-python[Add supporting function](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=43-45 "Add supporting function")]

## <a name="add-function-to-create-kb"></a>Dodawanie funkcji tworzącej bazę wiedzy

Dodaj następującą funkcję, aby wysłać żądanie POST HTTP w celu utworzenia bazy wiedzy.
To wywołanie interfejsu API zwraca odpowiedź w formacie JSON, która zawiera identyfikator operacji w polu nagłówka **Location**. Użyj tego identyfikatora operacji, aby określić, czy baza wiedzy została pomyślnie utworzona. `Ocp-Apim-Subscription-Key` to klucz usługi QnA Maker używany do uwierzytelniania.

[!code-python[Add function to create KB](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=48-59 "Add function to create KB")]

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

## <a name="add-function-to-check-creation-status"></a>Dodawanie funkcji sprawdzającej stan tworzenia

Poniższa funkcja sprawdza stan tworzenia, wysyłając identyfikator operacji na końcu trasy adresu URL. Wywołanie funkcji `check_status` znajduje się w głównej pętli _while_.

[!code-python[Add function to check creation status](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=61-67 "Add function to check creation status")]

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

## <a name="add-main-code-block"></a>Dodawanie głównego bloku kodu
Następująca pętla okresowo sonduje stan operacji tworzenia, dopóki operacja nie zostanie zakończona.

[!code-python[Add main code block](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=70-96 "Add main code block")]

## <a name="build-and-run-the-program"></a>Kompilowanie i uruchamianie programu

Wprowadź następujące polecenie w wierszu polecenia, aby uruchomić program. Program wyśle żądanie do interfejsu API usługi QnA Maker, aby utworzyć bazę wiedzy, a następnie będzie sondować wyniki co 30 sekund. Każda odpowiedź jest wypisywana w oknie konsoli.

```bash
python create-new-knowledge-base-3x.py
```

Utworzoną bazę wiedzy można wyświetlić w portalu usługi QnA Maker, na stronie [My knowledge bases (Moje bazy wiedzy)](https://www.qnamaker.ai/Home/MyServices). Wybierz nazwę bazy wiedzy, na przykład „QnA Maker — FAQ”, aby ją wyświetlić.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://go.microsoft.com/fwlink/?linkid=2092179)