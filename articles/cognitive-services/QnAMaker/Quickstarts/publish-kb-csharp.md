---
title: 'Szybki start: publikowanie bazy wiedzy, REST, C# - QnA Maker'
description: Ten szybki start oparty na restie języka C# publikuje bazę wiedzy i tworzy punkt końcowy, który można wywołać w aplikacji lub bota czatu.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 0a2f64795d82928e33a10c7e9d162f2333cdf7bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851708"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Szybki start: publikowanie bazy wiedzy w usłudze QnA Maker przy użyciu języka C#

Ten przewodnik Szybki start oparty na protokole REST przeprowadzi Cię przez programowe publikowanie bazy wiedzy. Publikowanie wypycha najnowszą wersję bazy wiedzy do dedykowanego indeksu usługi Azure Cognitive Search i tworzy punkt końcowy, który można wywołać w aplikacji lub bota czatu.

Ten przewodnik Szybki start wywołuje interfejsy API usługi QnA Maker:
* [Publikowanie](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) — ten interfejs API nie wymaga żadnych informacji zawartych w treści żądania.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza [**wersja programu Visual Studio Community**](https://www.visualstudio.com/downloads/).
* Musisz mieć [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby pobrać klucz i punkt końcowy (który zawiera nazwę zasobu), wybierz **szybki start** dla zasobu w witrynie Azure portal.
* Identyfikator bazy wiedzy QnA Maker (KB) znajdujący się w adresie URL w parametrze `kbid` ciągu zapytania, jak pokazano poniżej.

    ![Identyfikator bazy wiedzy usługi QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Jeśli nie masz jeszcze bazy wiedzy, możesz utworzyć przykładową bazę na potrzeby tego podręcznika Szybki start: [Tworzenie nowej bazy wiedzy](create-new-kb-csharp.md).

> [!NOTE]
> Kompletne pliki rozwiązań są dostępne w [repozytorium **Azure-Samples/cognitive-services-qnamaker-csharp** GitHub.](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base)

## <a name="create-knowledge-base-project"></a>Tworzenie projektu bazy wiedzy

1. Otwórz wersję społecznościową programu Visual Studio 2019.
1. Utwórz nowy projekt **aplikacji konsoli (.NET Core)** i nadaj nazwę projektowi `QnaMakerQuickstart`. Zaakceptuj wartości domyślne pozostałych ustawień.

## <a name="add-required-dependencies"></a>Dodawanie wymaganych zależności

Na początku pliku Program.cs zastąp pojedynczą instrukcję using następującymi wierszami, aby dodać do projektu niezbędne zależności:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>Dodawanie wymaganych stałych

W **klasie Program** dodaj wymagane stałe, aby uzyskać dostęp do programu QnA Maker.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=8-34 "Add the required constants")]

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>Dodaj metodę Main, aby opublikować bazę wiedzy

Poniżej wymaganych stałych dodaj następujący kod, który wysyła żądanie HTTPS do interfejsu API usługi QnA Maker, aby opublikować bazę wiedzy, oraz odbiera odpowiedź:

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=36-56 "Add HTTP Post request and response")]

W przypadku pomyślnego publikowania wywołanie interfejsu API zwraca stan 204 bez jakiejkolwiek zawartości w treści odpowiedzi.

## <a name="build-and-run-the-program"></a>Kompilowanie i uruchamianie programu

Skompiluj i uruchom program. Spowoduje to automatyczne wysłanie do interfejsu API usługi QnA Maker żądania opublikowania bazy wiedzy. Odpowiedź jest następnie wyświetlana w oknie konsoli.

Po opublikowaniu bazy wiedzy można tworzyć do niej zapytania z punktu końcowego przy użyciu aplikacji klienckiej lub czatbota.

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

Po opublikowaniu bazy wiedzy potrzebny jest [adres URL punktu końcowego do wygenerowania odpowiedzi](./get-answer-from-knowledge-base-csharp.md).

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://go.microsoft.com/fwlink/?linkid=2092179)