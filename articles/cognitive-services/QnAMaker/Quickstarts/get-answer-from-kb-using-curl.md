---
title: 'Szybki Start: używanie założenia do uzyskania odpowiedzi z bazy wiedzy QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki Start przeprowadzi Cię przez proces uzyskiwania odpowiedzi z bazy wiedzy przy użyciu zwinięcia.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: b698b40546ee1655ebbef3980692ede6b51fc7f1
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803025"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>Szybki Start: uzyskiwanie odpowiedzi z bazy wiedzy przy użyciu zwinięcie

Ten przewodnik Szybki Start przeprowadzi Cię przez proces uzyskiwania odpowiedzi z bazy wiedzy.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsze [**zwinięcie**](https://curl.haxx.se/).
* Konieczne jest posiadanie [usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md) oraz [bazy wiedzy zawierającej pytania i odpowiedzi](../Tutorials/create-publish-query-in-portal.md).

## <a name="publish-to-get-endpoint"></a>Publikowanie w celu pobrania punktu końcowego

Aby wygenerować odpowiedź na pytanie z bazy wiedzy, [opublikuj](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) bazę wiedzy.

## <a name="use-production-endpoint-with-curl"></a>Użyj punktu końcowego produkcji z zwinięciem

Po opublikowaniu bazy wiedzy na stronie **Publikowanie** zostaną wyświetlone ustawienia żądania HTTP do generowania odpowiedzi. Na karcie **zwinięcie** są wyświetlane ustawienia wymagane do wygenerowania odpowiedzi z narzędzia wiersza polecenia, [zwinięcie](https://www.getpostman.com).

[![Publikowanie wyników](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

Aby wygenerować odpowiedź z ZWINIĘCIEm, wykonaj następujące czynności:

1. Skopiuj tekst na karcie ZWINIĘCIE. 
1. Otwórz wiersz polecenia lub terminal i wklej tekst.
1. Edytuj pytanie, aby mieć istotne znaczenie dla bazy wiedzy. Należy zachować ostrożność, aby nie usuwać zawierającej kodu JSON otaczającego pytanie.
1. Wprowadź polecenie. 
1. Odpowiedź zawiera odpowiednie informacje dotyczące odpowiedzi. 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
          "metadata": [
            {
              "name": "category",
              "value": "api"
            }
          ]
        }
      ]
    }
    ```

## <a name="use-staging-endpoint-with-curl"></a>Korzystanie z przejściowego punktu końcowego przy użyciu programu cURL

Jeśli chcesz uzyskać odpowiedź z punktu końcowego przemieszczania, użyj właściwości treści `isTest`.

```json
isTest:true
```

## <a name="next-steps"></a>Następne kroki

Strona publikowanie zawiera również informacje umożliwiające [wygenerowanie odpowiedzi](get-answer-from-kb-using-postman.md) z programem Poster. 

> [!div class="nextstepaction"]
> [Korzystanie z metadanych podczas generowania odpowiedzi](../How-to/metadata-generateanswer-usage.md)
