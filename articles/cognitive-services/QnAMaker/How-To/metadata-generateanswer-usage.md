---
title: Używanie metadanych w bazy wiedzy wraz z interfejsu API GenerateAnswer | Dokumentacja firmy Microsoft
description: Przy użyciu metadanych z interfejsem API GenerateAnswer
services: cognitive-services
author: pchoudhari
manager: rsrikan
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/18/2018
ms.author: pchoudh
ms.openlocfilehash: 94e3632884d7033971ff1c45b455afb9a09ee798
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "35356043"
---
# <a name="using-metadata-and-the-generateanswer-api"></a>Przy użyciu metadanych i interfejsu API GenerateAnswer

Maker — strona główna umożliwia dodanie metadanych, w postaci pary klucz wartość do Twojej zestawy pytań i odpowiedzi. Te informacje można na różne sposoby, takie jak filtrowanie wyników zapytania użytkownika, zwiększanie wyniku niektórych wyników i przechowywanie dodatkowe informacje, które mogą być używane w kolejnych konwersacji. Aby uzyskać więcej informacji, zobacz [wiedzy](../Concepts/knowledge-base.md).

## <a name="qna-entity"></a>Jednostki — strona główna

Najpierw ważne jest zrozumienie, jak Maker — strona główna przechowuje dane pytań i odpowiedzi. Na poniższej ilustracji przedstawiono jednostki — strona główna:

![Jednostki — strona główna](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Każdy obiekt — strona główna ma identyfikator unikatowy i trwałe. Identyfikator może służyć do wprowadzania aktualizacji danego podmiotu — strona główna.

## <a name="generateanswer-api"></a>GenerateAnswer interfejsu API

Za pomocą interfejsu API GenerateAnswer w aplikacji lub Bot zapytania bazy wiedzy z pytaniem użytkownika, aby uzyskać najlepsze dopasowanie z zestawów pytań i odpowiedzi.

### <a name="generateanswer-endpoint"></a>Punkt końcowy GenerateAnswer

Po opublikowaniu bazy wiedzy, albo z [Maker — strona główna portalu](https://www.qnamaker.ai), lub za pomocą [interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff), można uzyskać szczegółowe informacje o GenerateAnswer punktu końcowego.

Aby uzyskać szczegóły punktu końcowego:
1. Zaloguj się do [ https://www.qnamaker.ai ](https://www.qnamaker.ai).
2. W **Moje baz wiedzy**, kliknij **kod widoku** dla bazy wiedzy.
![Moje baz wiedzy](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
3. Pobierz szczegóły GenerateAnswer punktu końcowego.

![szczegóły punktu końcowego](../media/qnamaker-how-to-metadata-usage/view-code.png)

Można także uzyskać szczegóły punktu końcowego z **ustawienia** kartę bazy wiedzy.

### <a name="generateanswer-request"></a>Żądanie GenerateAnswer

GenerateAnswer zostanie wywołana za pomocą żądania HTTP POST. Przykładowy kod przedstawia sposób wywołania GenerateAnswer, zobacz [quickstarts](../quickstarts/csharp.md).

- **Adres URL żądania**: punktu końcowego Maker https://{QnA} /knowledgebases/ {identyfikator bazy wiedzy knowledge base} / generateAnswer

- **Parametry żądania**: 
    - **Identyfikator bazy wiedzy Knowledge base** (ciąg): identyfikator GUID dla bazy wiedzy.
    - **Punkt końcowy QnAMaker** (ciąg): Nazwa hosta punktu końcowego wdrożone w ramach subskrypcji platformy Azure.
- **Nagłówki żądania**
    - **Content-Type** (ciąg): typ nośnika treści wysyłane do interfejsu API.
    - **Autoryzacji** (ciąg): klucz punktu końcowego.
- **Treść żądania**
    - **pytanie** (ciąg): pytanie użytkownika, aby być zapytanie względem bazy wiedzy.
    - **TOP** (opcjonalne, liczba całkowita): liczba wyników uporządkowanej według rangi do uwzględnienia w danych wyjściowych. Wartość domyślna to 1.
    - **Identyfikator userId** (opcjonalne, ciąg): Unikatowy identyfikator do identyfikacji użytkownika. Ten identyfikator będą rejestrowane w dziennikach rozmów.
    - **strictFilters** (opcjonalne, ciąg): Jeśli zostanie określona, informuje Maker — strona główna można udzielać tylko odpowiedzi, które mają określonych metadanych. Aby uzyskać więcej informacji zobacz poniżej.
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

- **Odpowiedź 200** -pomyślnego połączenia zwraca wynik zapytania. Odpowiedź zawiera następujące pola:
    - **odpowiedzi** — Lista odpowiedzi na zapytanie użytkownika, posortowane według kolejności Klasyfikacja wynik.
        - **wynik**: wynik klasyfikację od 0 do 100.
        - **pytania**: pytania podanego przez użytkownika.
        - **źródło**: nazwę źródła, z której wyjęto lub zapisany w bazie wiedzy knowledge base odpowiedzi.
        - **metadane**: metadane skojarzone z odpowiedzią.
            - Nazwa: Nazwa metadanych. (ciąg, długość maksymalna: 100, wymagane)
            - wartość: wartość metadanych. (ciąg, długość maksymalna: 100, wymagane)
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

Należy wziąć pod uwagę poniżej restauracji w Hyderabad często zadawane pytania dotyczące danych. Dodaj metadane do bazy wiedzy, klikając ikonę Koło zębate.

![Dodawanie metadanych](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

### <a name="filter-results-with-strictfilters"></a>Filtrowanie wyników z strictFilters

Należy wziąć pod uwagę pytanie użytkownika "Gdy jest to hoteli Zamknij?" której celem jest domniemane dla restauracji "Paradise."

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
Odpowiedź GenerateAnswer zawiera odpowiednie metadane zestawu zgodnych pytanie/odpowiedzi, w następujący sposób.

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

Te informacje może służyć do rejestrowania kontekście poprzedniej konwersacji do użycia w późniejszym konwersacji. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie bazy wiedzy](./create-knowledge-base.md)
