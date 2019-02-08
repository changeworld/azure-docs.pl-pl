---
title: Metadane z GenerateAnswer interfejsu API — usługa QnA Maker
titleSuffix: Azure Cognitive Services
description: Narzędzie QnA Maker umożliwia dodanie metadanych, w postaci par klucz wartość do zestawów z pytań i odpowiedzi. Informacja ta może służyć do filtrowania wyników na zapytania użytkowników i do przechowywania dodatkowych informacji, które mogą być używane w kolejnych konwersacje.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/18/2018
ms.author: tulasim88
ms.openlocfilehash: 1294b714c217178d53ed69cc886cd89f23620274
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859491"
---
# <a name="using-metadata-and-the-generateanswer-api"></a>Przy użyciu metadanych i interfejsu API GenerateAnswer

Narzędzie QnA Maker umożliwia dodanie metadanych, w postaci par kluczy i wartości, do Twojego zestawów pytań i odpowiedzi. Informacja ta może służyć do filtrowania wyników na zapytania użytkowników i do przechowywania dodatkowych informacji, które mogą być używane w kolejnych konwersacje. Aby uzyskać więcej informacji, zobacz [wiedzy](../Concepts/knowledge-base.md).

## <a name="qna-entity"></a>Jednostki pytań i odpowiedzi

Najpierw ważne jest zrozumienie, jak usługa QnA Maker przechowuje dane pytań i odpowiedzi. Na poniższej ilustracji przedstawiono jednostki pytań i odpowiedzi:

![Jednostki pytań i odpowiedzi](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Każda jednostka pytań i odpowiedzi ma identyfikator unikatowy i trwałe. Identyfikator może służyć do wykonywania aktualizacji danego podmiotu pytań i odpowiedzi.

## <a name="generateanswer-api"></a>GenerateAnswer interfejsu API

Zapytanie bazy wiedzy przy użyciu pytanie użytkownika, aby uzyskać najlepsze dopasowanie z zestawów pytań i odpowiedzi za pomocą interfejsu API GenerateAnswer w aplikacji lub Botów.

### <a name="generateanswer-endpoint"></a>Punkt końcowy GenerateAnswer

Po opublikowaniu wiedzy, albo z [portalu narzędzia QnA Maker](https://www.qnamaker.ai), lub za pomocą [interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff), możesz uzyskać szczegółowe informacje o punkcie końcowym usługi GenerateAnswer.

Aby uzyskać informacje dotyczące punktu końcowego:
1. Zaloguj się do [ https://www.qnamaker.ai ](https://www.qnamaker.ai).
2. W **moich baz wiedzy**, kliknij pozycję **Wyświetl kod** dla Twojej bazy wiedzy.
![Moje bazy wiedzy](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
3. Pobranie szczegółowych informacji o GenerateAnswer punktu końcowego.

![szczegóły punktu końcowego](../media/qnamaker-how-to-metadata-usage/view-code.png)

Możesz też pobrać Twoje szczegóły punktu końcowego z **ustawienia** kartę bazy wiedzy.

### <a name="generateanswer-request"></a>Żądanie GenerateAnswer

Możesz wywołać GenerateAnswer za pomocą żądania HTTP POST. Przykładowy kod przedstawia sposób wywołania GenerateAnswer, zobacz [przewodników Szybki Start](../quickstarts/csharp.md).

- **Adres URL żądania**: punkt końcowy Maker https://{QnA} /knowledgebases/ {identyfikator bazy wiedzy knowledge base} / generateAnswer

- **Parametry żądania**: 
    - **Identyfikator bazy wiedzy** (ciąg): Identyfikator GUID bazy wiedzy.
    - **Punkt końcowy interfejsu QnAMaker** (ciąg): Nazwa hosta punktu końcowego, wdrożonych w ramach subskrypcji platformy Azure.
- **Nagłówki żądania**
    - **Content-Type** (ciąg): Typ nośnika treści wysłanej do interfejsu API.
    - **Autoryzacja** (ciąg): Klucz punktu końcowego (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).
- **Treść żądania**
    - **pytanie** (ciąg): Pytanie użytkownika, aby być wyszukiwane względem bazy wiedzy.
    - **TOP** (opcjonalne, liczba całkowita): Liczba wyników w rankingu do uwzględnienia w danych wyjściowych. Wartość domyślna to 1.
    - **Identyfikator userId** (opcjonalnie, ciąg): Unikatowy identyfikator, aby zidentyfikować użytkownika. Ten identyfikator będą rejestrowane w dziennikach rozmowy.
    - **strictFilters** (opcjonalnie, ciąg): Jeśli zostanie określony, informuje narzędzie QnA Maker, aby zwrócić tylko odpowiedzi, które mają określonych metadanych. Aby uzyskać więcej informacji zobacz poniżej.
    ```json
    {
        "question": "qna maker and luis",
        "top": 6,
        "strictFilters": [
        {
            "name": "category",
            "value": "api"
        }],
        "userId": "sd53lsY="
    }
    ```

### <a name="generateanswer-response"></a>GenerateAnswer odpowiedzi

- **Odpowiedź 200** -pomyślnego wywołania zwraca wynik zapytania. Odpowiedź zawiera następujące pola:
    - **odpowiedzi** — Lista odpowiedzi na zapytanie użytkownika, sortowane malejąco według klasyfikacji wynik.
        - **wynik**: Wynik klasyfikacji od 0 do 100.
        - **pytania dotyczące**: Pytania, dostarczone przez użytkownika.
        - **answer**: Odpowiedź na pytanie.
        - **źródło**: Nazwa źródła, z której wyjęto lub zapisany w bazie wiedzy knowledge base odpowiedź.
        - **metadane**: Metadane skojarzone z odpowiedzią.
            - Nazwa: Nazwa metadanych. (string, maksymalna długość: 100, które są wymagane)
            - value: Wartość metadanych. (string, maksymalna długość: 100, które są wymagane)
        - **Identyfikator**: Unikatowy identyfikator przypisany do odpowiedzi.
    ```json
    {
        "answers": [
            {
                "score": 28.54820341616869,
                "Id": 20,
                "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
                "source": "Custom Editorial",
                "questions": [
                    "How can I integrate LUIS with QnA Maker?"
                ],
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

## <a name="metadata-example"></a>Przykład metadanych

Należy wziąć pod uwagę poniższe często zadawane pytania dotyczące danych. Dodaj metadane do bazy wiedzy, klikając ikonę metadanych.

![Dodawanie metadanych](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

### <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrowanie wyników za pomocą strictFilters tagów metadanych

Należy wziąć pod uwagę pytanie użytkownika "Podczas odbywa się to hotelu Zamknij?" których celem jest implikowane dla restauracji "Paradise."

Ponieważ wyniki są wymagane tylko w przypadku restauracji "Paradise", można ustawić filtr w wywołaniu GenerateAnswer na metadanych "Restauracji Name", w następujący sposób.

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

### <a name="keep-context"></a>Zachowaj kontekstu
Odpowiedź GenerateAnswer zawiera odpowiednie informacje o metadanych zestawu dopasowane/odpowiedź na pytanie, w następujący sposób.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

Te informacje może służyć do rejestrowania kontekście poprzedniej rozmowy dla danego Użyj nowszego konwersacji. 

## <a name="next-steps"></a>Kolejne kroki

Strona publikowania zawiera również informacje potrzebne do generowania odpowiedzi za pomocą [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) i [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Tworzenie bazy wiedzy](./create-knowledge-base.md)
