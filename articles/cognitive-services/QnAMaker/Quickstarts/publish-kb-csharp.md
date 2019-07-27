---
title: 'Szybki start: Publikowanie bazy wiedzy, REST i C# QNA Maker'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki start oparty na protokole REST języka C# przeprowadzi Cię przez proces publikowania bazy wiedzy, który polega na wypchnięciu najnowszej wersji przetestowanej bazy wiedzy do dedykowanego indeksu usługi Azure Search reprezentującego opublikowaną bazę wiedzy. Zostanie również utworzony punkt końcowy, który można wywoływać w aplikacji lub czatbocie.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 0ce0f3673a297964932f3b97dfd1d49d7c1e55e5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559780"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Szybki start: Publikowanie bazy wiedzy w usłudze QnA Maker przy użyciu języka C#

Ten przewodnik Szybki start oparty na protokole REST przeprowadzi Cię przez programowe publikowanie bazy wiedzy. Publikowanie wypycha najnowszą wersję bazy wiedzy do dedykowanego indeksu usługi Azure Search i tworzy punkt końcowy, który może być wywoływany w Twojej aplikacji lub czatbocie.

Ten przewodnik Szybki start wywołuje interfejsy API usługi QnA Maker:
* [Publikowanie](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) — ten interfejs API nie wymaga żadnych informacji zawartych w treści żądania.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza [**wersja programu Visual Studio Community**](https://www.visualstudio.com/downloads/).
* Musisz mieć [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby pobrać klucz, wybierz pozycję **Klucze** w obszarze **Zarządzanie zasobami** na pulpicie nawigacyjnym. 
* Identyfikator bazy wiedzy usługi QnA Maker dostępny w adresie URL w parametrze ciągu zapytania kbid, jak pokazano poniżej.

    ![Identyfikator bazy wiedzy usługi QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Jeśli nie masz jeszcze bazy wiedzy, możesz utworzyć przykładową bazę na potrzeby tego podręcznika Szybki start: [Tworzenie nowej bazy wiedzy](create-new-kb-csharp.md).

> [!NOTE] 
> Pliki kompletnego rozwiązania są dostępne w [repozytorium GitHub **Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-knowledge-base-project"></a>Tworzenie projektu bazy wiedzy

1. Otwórz program Visual Studio 2019 Community Edition.
1. Utwórz nowy projekt **Aplikacja konsolowa (.NET Core)** i nadaj mu nazwę `QnaMakerQuickstart`. Zaakceptuj wartości domyślne pozostałych ustawień.

## <a name="add-required-dependencies"></a>Dodawanie wymaganych zależności

Na początku pliku Program.cs zastąp pojedynczą instrukcję using następującymi wierszami, aby dodać do projektu niezbędne zależności:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>Dodawanie wymaganych stałych

W metodzie **Main** dodaj wymagane stałe na potrzeby uzyskiwania dostępu do usługi QnA Maker. Przedstawione wartości zastąp własnymi.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=11-14 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Dodawanie żądania POST w celu publikowania bazy wiedzy

Poniżej wymaganych stałych dodaj następujący kod, który wysyła żądanie HTTPS do interfejsu API usługi QnA Maker, aby opublikować bazę wiedzy, oraz odbiera odpowiedź:

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=16-29&dedent=8 "Add HTTP Post request and response")]

W przypadku pomyślnego publikowania wywołanie interfejsu API zwraca stan 204 bez jakiejkolwiek zawartości w treści odpowiedzi. 
 
## <a name="build-and-run-the-program"></a>Kompilowanie i uruchamianie programu

Skompiluj i uruchom program. Spowoduje to automatyczne wysłanie do interfejsu API usługi QnA Maker żądania opublikowania bazy wiedzy. Odpowiedź jest następnie wyświetlana w oknie konsoli.

Po opublikowaniu bazy wiedzy można tworzyć do niej zapytania z punktu końcowego przy użyciu aplikacji klienckiej lub czatbota. 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Następne kroki

Po opublikowaniu bazy wiedzy potrzebny jest [adres URL punktu końcowego do wygenerowania odpowiedzi](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://go.microsoft.com/fwlink/?linkid=2092179)
