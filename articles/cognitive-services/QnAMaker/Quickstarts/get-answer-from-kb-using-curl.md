---
title: 'Szybki Start: Aby uzyskać odpowiedzi z bazy wiedzy knowledge base — QnA Maker korzystanie z programu cURL'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki Start przeprowadzi Cię przez uzyskiwanie odpowiedzi z bazy wiedzy przy użyciu programu cURL.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 0cbd25c0ea906c0b0f35b6ac0ae798505863ac8a
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273259"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>Szybki Start: Uzyskiwanie odpowiedzi z bazy wiedzy przy użyciu programu cURL

Szybki Start to narzędzie cURL przeprowadzi Cię przez uzyskiwanie odpowiedzi z bazy wiedzy.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsze [ **cURL**](https://curl.haxx.se/).
* Konieczne jest posiadanie [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md) i [wiedzy pytań i odpowiedzi](../Tutorials/create-publish-query-in-portal.md).

## <a name="publish-to-get-endpoint"></a>Publikować można pobrać punktu końcowego

Gdy wszystko jest gotowe do generowania odpowiedzi na pytania z bazy wiedzy, [publikowania](../How-to/publish-knowledge-base.md) bazy wiedzy.

## <a name="use-production-endpoint-with-curl"></a>Produkcyjny punkt końcowy za pomocą programu cURL

Po opublikowaniu bazy wiedzy **Publikuj** strony wyświetli ustawienia żądania HTTP do generowania odpowiedzi. **CURL** karta zawiera ustawienia wymagane do generowania odpowiedzi za pomocą narzędzia wiersza polecenia [CURL](https://www.getpostman.com).

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
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
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

## <a name="use-staging-endpoint-with-curl"></a>Używanie przemieszczania punktu końcowego za pomocą programu cURL

Jeśli chcesz uzyskać odpowiedzi z przemieszczania punktu końcowego, użyj parametru logiczna querystring `isTest` z wartością `true`.

`isTest=true`

## <a name="next-steps"></a>Kolejne kroki

Strona publikowania zawiera również informacje [Generowanie odpowiedzi](get-answer-from-kb-using-postman.md) za pomocą narzędzia Postman. 

> [!div class="nextstepaction"]
> [Używanie metadanych podczas generowania odpowiedzi](../How-to/metadata-generateanswer-usage.md)