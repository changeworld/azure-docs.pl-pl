---
title: Zapytanie do bazy wiedzy — QnA Maker
description: Należy opublikować bazę wiedzy. Po opublikowaniu baza wiedzy jest wyszukiwana w punkcie końcowym przewidywania środowiska wykonawczego przy użyciu interfejsu API generateAnswer.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb777aa16fada50811cce1bbf49f28662c62b49b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220722"
---
# <a name="query-the-knowledge-base-for-answers"></a>Zapytanie do bazy wiedzy o odpowiedzi

Należy opublikować bazę wiedzy. Po opublikowaniu baza wiedzy jest wyszukiwana w punkcie końcowym przewidywania środowiska wykonawczego przy użyciu interfejsu API generateAnswer. Zapytanie zawiera tekst pytania i inne ustawienia, aby pomóc QnA Maker wybrać najlepsze możliwe dopasowanie do odpowiedzi.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Jak program QnA Maker przetwarza zapytanie użytkownika w celu wybrania najlepszej odpowiedzi

Przeszkolona i [opublikowana](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) baza wiedzy QnA Maker odbiera zapytanie użytkownika, z bota lub innej aplikacji klienckiej, w [interfejsie API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Na poniższym diagramie przedstawiono proces po odebraniu kwerendy użytkownika.

![Proces modelu klasyfikacji dla kwerendy użytkownika](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Proces rankera

Proces ten jest wyjaśniony w poniższej tabeli.

|Krok|Przeznaczenie|
|--|--|
|1|Aplikacja kliencka wysyła zapytanie użytkownika do [interfejsu API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|Program QnA Maker wstępnie przetwarza kwerendę użytkownika za pomocą wykrywania języka, czarowników i modułów sprawdzania wyrazów.|
|3|To przetwarzanie wstępne jest podejmowane w celu zmiany zapytania użytkownika dla najlepszych wyników wyszukiwania.|
|4|Ta zmieniona kwerenda jest wysyłana do indeksu usługi Azure Cognitive Search Index, który otrzymuje `top` liczbę wyników. Jeśli poprawnej odpowiedzi nie ma w tych `top` wynikach, zwiększ wartość nieznacznie. Ogólnie rzecz biorąc wartość 10 dla `top` prac w 90% zapytań.|
|5|QnA Maker używa składni i semantycznej featurization w celu określenia podobieństwa między kwerendą użytkownika i pobranych wyników QnA.|
|6|Model rangeru nauczony maszynowo używa różnych funkcji, od kroku 5, do określenia wyników zaufania i nowej kolejności rankingowej.|
|7|Nowe wyniki są zwracane do aplikacji klienckiej w kolejności rankingowej.|
|||

Używane funkcje obejmują, ale nie są ograniczone do semantyki na poziomie słowa, znaczenie na poziomie terminu w korpusie i głęboko wyuczonych modeli semantycznych w celu określenia podobieństwa i trafności między dwoma ciągami tekstowymi.

## <a name="http-request-and-response-with-endpoint"></a>Żądanie i odpowiedź HTTP z punktem końcowym
Podczas publikowania bazy wiedzy usługa tworzy punkt końcowy HTTP oparty na rest, który można zintegrować z aplikacją, często bot czatu.

### <a name="the-user-query-request-to-generate-an-answer"></a>Żądanie kwerendy użytkownika w celu wygenerowania odpowiedzi

Zapytanie użytkownika to pytanie, które użytkownik końcowy zadaje `How do I add a collaborator to my app?`bazy wiedzy, na przykład . Zapytanie jest często w formacie języka naturalnego lub kilka słów `help with collaborators`kluczowych, które reprezentują pytanie, takie jak . Kwerenda jest wysyłana do bazy wiedzy z żądania HTTP w aplikacji klienckiej.

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

Odpowiedź można kontrolować, ustawiając właściwości, takie jak [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)i [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Użyj [kontekstu konwersacji](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) z [funkcją multi-turn,](../how-to/multiturn-conversation.md) aby utrzymać konwersację w celu uściślenia pytań i odpowiedzi, aby znaleźć poprawną i ostateczną odpowiedź.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Odpowiedź z połączenia w celu wygenerowania odpowiedzi

Odpowiedź HTTP jest odpowiedzią pobraną z bazy wiedzy, opartą na najlepszym dopasowaniu dla danego zapytania użytkownika. Odpowiedź zawiera odpowiedź i wynik przewidywania. Jeśli poprosisz o więcej niż `top` jedną najlepszą odpowiedź z nieruchomości, otrzymasz więcej niż jedną najlepszą odpowiedź, każda z wynikiem.

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Współczynnik ufności](./confidence-score.md)
