---
title: 'Szybki Start: Publikowanie bazy wiedzy, REST i Java — QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki Start oparty na języku Java przeprowadzi Cię przez proces publikowania bazy wiedzy, która wypycha najnowszą wersję przetestowanej bazy wiedzy do dedykowanego indeksu Azure Search reprezentującego opublikowaną bazę wiedzy. Tworzy również punkt końcowy, który można wywołać w aplikacji lub bot rozmowy.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: diberry
ms.openlocfilehash: 4ee622c944c5ccd39331ab395eca7b6ff9692b35
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71836081"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Szybki Start: Publikowanie bazy wiedzy w QnA Maker przy użyciu języka Java

Ten przewodnik Szybki Start zawiera instrukcje programistyczne publikowania bazy wiedzy (KB). Publikowanie powoduje wypchnięcie najnowszej wersji bazy wiedzy do dedykowanego indeksu Azure Search i utworzenie punktu końcowego, który można wywołać w aplikacji lub rozmowie bot.

Ten przewodnik Szybki Start wywołuje QnA Maker interfejsów API:
* [Publikowanie](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) — ten interfejs API nie wymaga żadnych informacji w treści żądania.

## <a name="prerequisites"></a>Wymagania wstępne

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, wersja Standard)
* Ten przykład korzysta z [klienta Apache HTTP](https://hc.apache.org/httpcomponents-client-ga/) ze składników http. Do projektu należy dodać następujące biblioteki klienta Apache HTTP: 
    * HttpClient-4.5.3. jar
    * httpcore-4.4.6. jar
    * Commons-Logging-1.2. jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Wymagana jest [usługa QNA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby pobrać klucz i punkt końcowy (w tym nazwę zasobu), wybierz pozycję **Szybki Start** dla zasobu w Azure Portal.
* QnA Maker identyfikator bazy wiedzy (KB) znaleziony w adresie URL w parametrze ciągu zapytania kbid, jak pokazano poniżej.

    ![Identyfikator bazy wiedzy QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Jeśli nie masz jeszcze bazy wiedzy, możesz utworzyć przykład jednej do użycia w tym przewodniku szybki start: [Tworzenie nowej bazy wiedzy](create-new-kb-csharp.md).

> [!NOTE] 
> Kompletne pliki rozwiązania są dostępne w [repozytorium **Azure-Samples/poznawcze-Services-qnamaker-Java** GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-java-file"></a>Utwórz plik Java

Otwórz programu vscode i Utwórz nowy plik o nazwie `PublishKB.java`.

## <a name="add-the-required-dependencies"></a>Dodaj wymagane zależności

W górnej części `PublishKB.java`, powyżej klasy, Dodaj następujące wiersze, aby dodać wymagane zależności do projektu:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>Utwórz klasę PublishKB za pomocą metody Main

Po zależności, Dodaj następującą klasę:

```Go
public class PublishKB {

    public static void main(String[] args) 
    {
    }
}
```

## <a name="add-required-constants"></a>Dodaj wymagane stałe

W metodzie **Main** Dodaj wymagane stałe, aby uzyskać dostęp do QNA Maker. Zastąp wartości własnymi.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Dodaj żądanie POST w celu opublikowania bazy wiedzy

Po wprowadzeniu wymaganych stałych Dodaj następujący kod, który wysyła żądanie HTTPS do interfejs API usługi QnA Maker w celu opublikowania bazy wiedzy i otrzymuje odpowiedź:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

Wywołanie interfejsu API zwraca stan 204 dla pomyślnego opublikowania bez żadnej zawartości w treści odpowiedzi. Kod dodaje zawartość do 204 odpowiedzi.

W przypadku każdej innej odpowiedzi odpowiedź jest zwracana bez zmian.

## <a name="build-and-run-the-program"></a>Kompilowanie i uruchamianie programu

Kompiluj i uruchamiaj program z wiersza polecenia. Żądanie zostanie automatycznie wysłane do interfejs API usługi QnA Maker, a następnie będzie drukowane w oknie konsoli.

1. Kompilowanie pliku:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Uruchom plik:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Następne kroki

Po opublikowaniu bazy wiedzy wymagany jest [adres URL punktu końcowego w celu wygenerowania odpowiedzi](../Tutorials/create-publish-answer.md#generating-an-answer).  

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST usługi QnA Maker (v4)](https://go.microsoft.com/fwlink/?linkid=2092179)
