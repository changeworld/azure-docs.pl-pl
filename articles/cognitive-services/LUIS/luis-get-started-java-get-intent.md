---
title: Pobieranie intencji, Java
titleSuffix: Language Understanding - Azure Cognitive Services
description: W tym przewodniku Szybki start dotyczącym języka Java użyjesz dostępnej publicznie aplikacji LUIS, aby określić intencję użytkownika w tekście konwersacji.
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 3b50678691342b5b106a375e79380cb8cc1515e4
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54855673"
---
# <a name="quickstart-get-intent-using-java"></a>Szybki start: pobieranie intencji przy użyciu języka Java

W tym przewodniku Szybki start przekażesz wypowiedzi do punktu końcowego aplikacji LUIS i uzyskasz intencje oraz jednostki.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Wymagania wstępne

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* Identyfikator aplikacji publicznej: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Pobieranie klucza usługi LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Pobieranie intencji za pomocą przeglądarki

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Pobieranie intencji w sposób programistyczny 

Aby uzyskać dostęp do tych samych wyników, które zostały wyświetlone w oknie przeglądarki w poprzednim kroku, możesz użyć języka Java. 

1. Skopiuj następujący kod, aby utworzyć klasę w pliku o nazwie `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Zastąp wartość zmiennej `YOUR-KEY` swoim kluczem usługi LUIS.

3. Skompiluj program Java przy użyciu polecenia `javac -cp ":lib/*" LuisGetRequest.java`. 

4. Uruchom aplikację klawiszem `java -cp ":lib/*" LuisGetRequest.java`. Wyświetli ona ten sam wynik JSON, który został wyświetlony wcześniej w oknie przeglądarki.

    ![W oknie konsoli zostanie wyświetlony wynik w formacie JSON z usługi LUIS](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>Klucze usługi LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Usuń plik Java. 

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Dodawanie wypowiedzi](luis-get-started-java-add-utterance.md)