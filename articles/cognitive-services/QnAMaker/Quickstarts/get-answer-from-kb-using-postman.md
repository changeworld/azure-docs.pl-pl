---
title: 'Szybki start: Uzyskiwanie odpowiedzi z bazy wiedzy przy użyciu narzędzia Postman — QnA Maker'
titlesuffix: Azure Cognitive Services
description: W tym samouczku Szybki start opisano sposób uzyskiwania odpowiedzi z bazy wiedzy przy użyciu narzędzia Postman.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: diberry
ms.openlocfilehash: 476e982bdddd41c1daf06c3a673d964ce2a85f98
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270896"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>Szybki start: Uzyskiwanie odpowiedzi z bazy wiedzy przy użyciu narzędzia Postman

W tym samouczku Szybki start opisano sposób uzyskiwania odpowiedzi z bazy wiedzy przy użyciu narzędzia Postman.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza wersja narzędzia [**Postman**](https://www.getpostman.com/).
* Konieczne jest posiadanie [usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md) oraz [bazy wiedzy zawierającej pytania i odpowiedzi](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Publikowanie w celu pobrania punktu końcowego

Aby wygenerować odpowiedź na pytanie z bazy wiedzy, [opublikuj](../How-to/publish-knowledge-base.md) bazę wiedzy.

## <a name="use-production-endpoint-with-postman"></a>Używanie produkcyjnego punktu końcowego z narzędziem Postman

Po opublikowaniu bazy wiedzy na stronie **Publikowanie** zostaną wyświetlone ustawienia żądania HTTP do generowania odpowiedzi. W widoku domyślnym są wyświetlane ustawienia wymagane do generowania odpowiedzi przy użyciu narzędzia [Postman](https://www.getpostman.com).

[![Publikowanie wyników](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Aby wygenerować odpowiedź za pomocą narzędzia Postman, wykonaj następujące czynności:

1. Otwórz narzędzie Postman. 
1. Wybierz blok konstrukcyjny, aby utworzyć podstawowe żądanie.
1. Jako **nazwę żądania** ustaw `Generate QnA Maker answer`, a jako nazwę **kolekcji** ustaw `Generate QnA Maker answers`.
1. W obszarze roboczym wybierz metodę HTTP **POST**.
1. Aby utworzyć pełny adres URL, połącz wartości HOST i Post. 

    [![W narzędziu Postman ustaw metodę Post i pełny adres URL](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox).

1. Wybierz kartę **Nagłówki** pod adresem URL, a następnie wybierz pozycję **Edycja zbiorcza**. 
1. Skopiuj nagłówki do obszaru tekstu.

    [![W narzędziu Postman ustaw nagłówki](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox).

1. Wybierz kartę **Treść**.
1. Wybierz format **raw** i wprowadź dane JSON reprezentujące pytanie.

    [![W narzędziu Postman ustaw wartość JSON treści](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox).

1. Kliknij przycisk **Wyślij**.
1. Odpowiedź zawiera odpowiedź oraz inne informacje, które mogą być istotne dla aplikacji klienckiej. 

    [![W narzędziu Postman ustaw wartość JSON treści](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox).

## <a name="use-staging-endpoint-with-curl"></a>Korzystanie z przejściowego punktu końcowego przy użyciu programu cURL

Aby uzyskać odpowiedź z przejściowego punktu końcowego, użyj parametru logicznego ciągu kwerendy `isTest` o wartości `true`.

`isTest=true`

## <a name="next-steps"></a>Następne kroki

Strona publikowania zawiera również informacje dotyczące [generowania odpowiedzi](get-answer-from-kb-using-curl.md) przy użyciu programu cURL. 

> [!div class="nextstepaction"]
> [Korzystanie z metadanych podczas generowania odpowiedzi](../How-to/metadata-generateanswer-usage.md)