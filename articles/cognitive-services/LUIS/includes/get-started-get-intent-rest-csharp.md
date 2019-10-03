---
title: Pobierz zamierzenia z wywołaniem REST wC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: e6ae9590cee3a2ddc3b8e121161fcf84815da28a
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838555"
---
## <a name="prerequisites"></a>Wymagania wstępne

* [Visual Studio Community 2017 Edition](https://visualstudio.microsoft.com/vs/community/)
* C#język programowania (dołączony do programu VS Community 2017)
* Identyfikator aplikacji publicznej: df67dcdb-c37d-46AF-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Pobierz klucz LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Programowe pobieranie zamiarów

Służy C# do wykonywania zapytań dotyczących [interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) usługi przewidywania, aby uzyskać te same wyniki, jak w oknie przeglądarki w poprzedniej sekcji. 

1. Utwórz nową aplikację konsolową w programie Visual Studio. 

    ![Tworzenie nowej aplikacji konsolowej w programie Visual Studio](../media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. W projekcie programu Visual Studio, w Eksploratorze rozwiązań wybierz pozycję **Dodaj odwołanie**, a następnie wybierz pozycję **System. Web** z karty zestawy.

    ![Wybierz pozycję Dodaj odwołanie, a następnie wybierz pozycję System. Web z karty Zestawy](../media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Zastąp Program.cs następującym kodem:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Zastąp wartość `YOUR_KEY` kluczem LUIS.

5. Kompiluj i uruchom aplikację konsolową. Wyświetla ten sam kod JSON, który został wyświetlony wcześniej w oknie przeglądarki.

    ![W oknie konsoli jest wyświetlany wynik JSON z LUIS](../media/luis-get-started-cs-get-intent/console-turn-on.png)



## <a name="luis-keys"></a>Klucze LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start Zamknij projekt programu Visual Studio i Usuń katalog projektu z systemu plików. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie wyrażenia długości i uczenie się za pomocąC#](../luis-get-started-cs-add-utterance.md)