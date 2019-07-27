---
title: Opcje pobierania, Java — LUIS
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start dotyczącym języka Java użyjesz dostępnej publicznie aplikacji LUIS, aby określić intencję użytkownika w tekście konwersacji.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 7a80aefd7a88727cf3a2261115c076853bd45b3f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563809"
---
# <a name="quickstart-get-intent-using-java"></a>Szybki start: pobieranie intencji przy użyciu języka Java

W tym przewodniku Szybki start przekażesz wypowiedzi do punktu końcowego aplikacji LUIS i uzyskasz intencje oraz jednostki.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/) lub ULUBIONEGO środowiska IDE
* Identyfikator aplikacji publicznej: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Pobieranie klucza usługi LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Pobieranie intencji za pomocą przeglądarki

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Pobieranie intencji w sposób programistyczny

Aby uzyskać dostęp do tych samych wyników, które zostały wyświetlone w oknie przeglądarki w poprzednim kroku, możesz użyć języka Java. Pamiętaj, aby dodać biblioteki Apache do projektu.

1. Skopiuj następujący kod, aby utworzyć klasę w pliku o nazwie `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Zastąp wartość zmiennej `YOUR-KEY` swoim kluczem usługi LUIS.

3. Zastąp ciąg ścieżką pliku i skompiluj program Java z wiersza polecenia: `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`.

4. Zastąp ciąg ścieżką pliku i uruchom aplikację z wiersza polecenia: `java -cp .;<FILE_PATH>\* LuisGetRequest.java`. Wyświetli ona ten sam wynik JSON, który został wyświetlony wcześniej w oknie przeglądarki.

    ![W oknie konsoli zostanie wyświetlony wynik w formacie JSON z usługi LUIS](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>Klucze usługi LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Usuń folder plik/projekt środowiska Java.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Dodawanie wypowiedzi](luis-get-started-java-add-utterance.md)
