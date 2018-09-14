---
title: Analizowanie tekstu w języku naturalnym w usłudze Language Understanding (LUIS) przy użyciu języka C# — Azure Cognitive Services | Microsoft Docs
description: W tym przewodniku Szybki start użyjesz dostępnej publicznie aplikacji LUIS, aby określić intencję użytkownika w tekście konwersacji. Przy użyciu języka C# wyślesz intencję użytkownika jako tekst do punktu końcowego przewidywania HTTP aplikacji publicznej. W punkcie końcowym usługa LUIS zastosuje model aplikacji publicznej, aby przeanalizować tekst w języku naturalnym pod kątem znaczenia, określając ogólną intencję i wyodrębniając dane dotyczące domeny podmiotu aplikacji.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 676546a215bbb8964f1cb2d26ae0fb9fd2ed9289
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "43771660"
---
# <a name="quickstart-analyze-text-using-c"></a>Szybki start: analiza tekstu przy użyciu języka C#

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* [Visual Studio Community 2017](https://visualstudio.microsoft.com/vs/community/)
* Język programowania C# (dołączony do programu VS Community 2017)
* Identyfikator aplikacji publicznej: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Pobieranie klucza usługi LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analiza tekstu przy użyciu przeglądarki

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-c"></a>Analiza tekstu przy użyciu języka C# 

Użyj języka C#, aby wysłać zapytanie do interfejsu [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) GET punktu końcowego przewidywania i uzyskać takie same wyniki, jakie przedstawiono w oknie przeglądarki w poprzedniej sekcji. 

1. Utwórz nową aplikację konsolową w programie Visual Studio. 

    ![Dostęp do menu ustawień użytkownika usługi LUIS](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. W projekcie programu Visual Studio, w Eksploratorze rozwiązań wybierz opcję **Dodaj odwołanie**, a następnie wybierz opcję **System.Web** na karcie Zestawy.

    ![Dostęp do menu ustawień użytkownika usługi LUIS](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Zastąp plik Program.cs przy użyciu następującego kodu:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Zastąp wartość `YOUR_KEY` swoim kluczem usługi LUIS.

5. Skompiluj i uruchom aplikację konsolową. Wyświetli ona ten sam wynik JSON, który został wyświetlony wcześniej w oknie przeglądarki.

    ![W oknie konsoli zostanie wyświetlony wynik w formacie JSON z usługi LUIS](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>Klucze usługi LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu przewodnika Szybki start zamknij projekt programu Visual Studio i usuń katalog projektu z systemu plików. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie wypowiedzi i uczenie przy użyciu języka C#](luis-get-started-cs-add-utterance.md)