---
title: Pobierz zamierzenia z wywołaniem REST w języku Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 7199dfaaa476e4be27929010b76a6e0544857bdb
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838505"
---
## <a name="prerequisites"></a>Wymagania wstępne

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, wersja Standard)
* [Visual Studio Code](https://code.visualstudio.com/) lub ULUBIONEGO środowiska IDE
* Identyfikator aplikacji publicznej: df67dcdb-c37d-46AF-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Pobierz klucz LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Programowe pobieranie zamiarów

Możesz użyć języka Java, aby uzyskać dostęp do tych samych wyników w oknie przeglądarki w poprzednim kroku. Pamiętaj, aby dodać biblioteki Apache do projektu.

1. Skopiuj następujący kod, aby utworzyć klasę w pliku o nazwie `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Zastąp wartość zmiennej `YOUR-KEY` kluczem LUIS.

3. Zastąp ciąg ścieżką pliku i skompiluj program Java z wiersza polecenia: `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`.

4. Zastąp ciąg ścieżką pliku i uruchom aplikację z wiersza polecenia: `java -cp .;<FILE_PATH>\* LuisGetRequest.java`. Wyświetla ten sam kod JSON, który został wyświetlony wcześniej w oknie przeglądarki.

    ![W oknie konsoli jest wyświetlany wynik JSON z LUIS](../media/luis-get-started-java-get-intent/console-turn-on.png)
    


## <a name="luis-keys"></a>Klucze LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start Zamknij projekt programu Visual Studio i Usuń katalog projektu z systemu plików. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie wyrażenia długości i uczenie się za pomocą języka Java](../luis-get-started-java-add-utterance.md)