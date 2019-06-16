---
title: 'Szybki start: Aby uzyskać odpowiedzi z bazy wiedzy knowledge base — QnA Maker korzystanie z programu cURL'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki Start przeprowadzi Cię przez uzyskiwanie odpowiedzi z bazy wiedzy przy użyciu programu cURL.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: 817a19d5cabd7d20dc17154f29b4430e6b96c5fa
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072055"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>Szybki start: Uzyskiwanie odpowiedzi z bazy wiedzy przy użyciu programu cURL

Szybki Start to narzędzie cURL przeprowadzi Cię przez uzyskiwanie odpowiedzi z bazy wiedzy.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsze [ **cURL**](https://curl.haxx.se/).
* Konieczne jest posiadanie [usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md) oraz [bazy wiedzy zawierającej pytania i odpowiedzi](../Tutorials/create-publish-query-in-portal.md).

## <a name="publish-to-get-endpoint"></a>Publikowanie w celu pobrania punktu końcowego

Aby wygenerować odpowiedź na pytanie z bazy wiedzy, [opublikuj](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) bazę wiedzy.

## <a name="use-production-endpoint-with-curl"></a>Produkcyjny punkt końcowy za pomocą programu cURL

Po opublikowaniu bazy wiedzy na stronie **Publikowanie** zostaną wyświetlone ustawienia żądania HTTP do generowania odpowiedzi. **CURL** karta zawiera ustawienia wymagane do generowania odpowiedzi za pomocą narzędzia wiersza polecenia [CURL](https://www.getpostman.com).

[![Publikowanie wyników](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

Aby wygenerować odpowiedzi za pomocą programu CURL, wykonaj następujące czynności:

1. Skopiuj tekst, na karcie programu CURL. 
1. Otwieranie wiersza polecenia lub terminalu i wkleić tekst.
1. Edytuj zapytania odpowiednie do bazy wiedzy. Uważaj, aby nie usunąć JSON zawierający otaczającego pytanie.
1. Wprowadź polecenie. 
1. Odpowiedź zawiera istotne informacje o odpowiedzi. 

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

Do uzyskania odpowiedzi na podstawie przemieszczania punktu końcowego, należy użyć `isTest` treści właściwości.

```json
isTest:true
```

## <a name="next-steps"></a>Kolejne kroki

Strona publikowania zawiera również informacje [Generowanie odpowiedzi](get-answer-from-kb-using-postman.md) za pomocą narzędzia Postman. 

> [!div class="nextstepaction"]
> [Korzystanie z metadanych podczas generowania odpowiedzi](../How-to/metadata-generateanswer-usage.md)
