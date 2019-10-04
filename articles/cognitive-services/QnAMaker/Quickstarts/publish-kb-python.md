---
title: 'Szybki Start: Publikowanie bazy wiedzy, REST i Python — QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki Start oparty na języku Python przeprowadzi Cię przez proces publikowania bazy wiedzy, która wypycha najnowszą wersję przetestowanej bazy wiedzy do dedykowanego indeksu Azure Search reprezentującego opublikowaną bazę wiedzy. Tworzy również punkt końcowy, który można wywołać w aplikacji lub bot rozmowy.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 09523bb6c9c8a58f5f7bd102d2ac30ad77f28d1c
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71840187"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>Szybki Start: Publikowanie bazy wiedzy w QnA Maker przy użyciu języka Python

Ten przewodnik Szybki Start zawiera instrukcje programistyczne publikowania bazy wiedzy (KB). Publikowanie powoduje wypchnięcie najnowszej wersji bazy wiedzy do dedykowanego indeksu Azure Search i utworzenie punktu końcowego, który można wywołać w aplikacji lub rozmowie bot.

To wywołanie narzędzia do szybkiego startu QnA Maker interfejsy API REST:
* [Publikowanie](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) — ten interfejs API nie wymaga żadnych informacji w treści żądania.

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 3,7](https://www.python.org/downloads/)
* Wymagana jest [usługa QNA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby pobrać klucz i punkt końcowy (w tym nazwę zasobu), wybierz pozycję **Szybki Start** dla zasobu w Azure Portal.
* QnA Maker identyfikator bazy wiedzy (KB) znaleziony w adresie URL w parametrze ciągu zapytania kbid, jak pokazano poniżej.

    ![Identyfikator bazy wiedzy QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Jeśli nie masz jeszcze bazy wiedzy, możesz utworzyć przykład jednej do użycia w tym przewodniku szybki start: [Tworzenie nowej bazy wiedzy](../how-to/create-knowledge-base.md).

> [!NOTE] 
> Kompletne pliki rozwiązania są dostępne w [repozytorium GitHub **Azure-Samples/poznawcze-Services-qnamaker-Python** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-knowledge-base-python-file"></a>Utwórz plik bazy wiedzy w języku Python

Utwórz plik o nazwie `publish-kb-3x.py`.

## <a name="add-the-required-dependencies"></a>Dodaj wymagane zależności

W górnej części `publish-kb-3x.py` Dodaj następujące wiersze, aby dodać wymagane zależności do projektu:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-required-constants"></a>Dodaj wymagane stałe

Po powyższych wymaganych zależnościach Dodaj wymagane stałe, aby uzyskać dostęp do QnA Maker. Zastąp wartości własnymi.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=5-15 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Dodaj żądanie POST w celu opublikowania bazy wiedzy

Po wprowadzeniu wymaganych stałych Dodaj następujący kod, który wysyła żądanie HTTPS do interfejs API usługi QnA Maker w celu opublikowania bazy wiedzy i otrzymuje odpowiedź:

[!code-python[Add a POST request to publish knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=17-26 "Add a POST request to publish knowledge base")]

Wywołanie interfejsu API zwraca stan 204 dla pomyślnego opublikowania bez żadnej zawartości w treści odpowiedzi. Kod dodaje zawartość do 204 odpowiedzi.

W przypadku każdej innej odpowiedzi odpowiedź jest zwracana bez zmian.

## <a name="build-and-run-the-program"></a>Kompilowanie i uruchamianie programu

Wprowadź następujące polecenie w wierszu polecenia, aby uruchomić program. Wyśle żądanie do interfejs API usługi QnA Maker w celu opublikowania bazy wiedzy, a następnie drukuje 204 dla sukcesu lub błędów.

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Następne kroki

Po opublikowaniu bazy wiedzy wymagany jest [adres URL punktu końcowego w celu wygenerowania odpowiedzi](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST usługi QnA Maker (v4)](https://go.microsoft.com/fwlink/?linkid=2092179)

[Przegląd QnA Maker](../Overview/overview.md)
