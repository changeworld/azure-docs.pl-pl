---
title: Pobierz intencje C# ,-Luis
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dotyczącym języka C# użyjesz dostępnej publicznie aplikacji LUIS, aby określić intencję użytkownika w tekście konwersacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 92f74174d35a58e54ae0078f146f86dbfc7aa709
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563847"
---
# <a name="quickstart-get-intent-using-c"></a>Szybki start: pobieranie intencji za pomocą języka C#

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* [Visual Studio Community 2017](https://visualstudio.microsoft.com/vs/community/)
* Język programowania C# (dołączony do programu VS Community 2017)
* Identyfikator aplikacji publicznej: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Pobieranie klucza usługi LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Pobieranie intencji za pomocą przeglądarki

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Pobieranie intencji w sposób programistyczny

Użyj języka C#, aby wysłać zapytanie do interfejsu [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) GET punktu końcowego przewidywania i uzyskać takie same wyniki, jakie przedstawiono w oknie przeglądarki w poprzedniej sekcji. 

1. Utwórz nową aplikację konsolową w programie Visual Studio. 

    ![Tworzenie nowej aplikacji konsolowej w programie Visual Studio](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. W projekcie programu Visual Studio, w Eksploratorze rozwiązań wybierz opcję **Dodaj odwołanie**, a następnie wybierz opcję **System.Web** na karcie Zestawy.

    ![Wybierz opcję Dodaj odwołanie, a następnie wybierz opcję System.Web na karcie Zestawy.](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Zastąp plik Program.cs przy użyciu następującego kodu:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Zastąp wartość `YOUR_KEY` swoim kluczem usługi LUIS.

5. Skompiluj i uruchom aplikację konsolową. Wyświetli ona ten sam wynik JSON, który został wyświetlony wcześniej w oknie przeglądarki.

    ![W oknie konsoli zostanie wyświetlony wynik w formacie JSON z usługi LUIS](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>Klucze usługi LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu przewodnika Szybki start zamknij projekt programu Visual Studio i usuń katalog projektu z systemu plików. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie wypowiedzi i uczenie przy użyciu języka C#](luis-get-started-cs-add-utterance.md)
