---
title: Baza wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker wiedzy składa się z zestaw par pytanie/odpowiedź (pytań i odpowiedzi) i opcjonalne metadane skojarzone z każdej pary pytań i odpowiedzi.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2b3e74f337cf8f57321c3a8d94f8191fc3ebb530
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093896"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Co to jest usługa QnA Maker wiedzy?

Usługa QnA Maker wiedzy składa się z zestaw par pytanie/odpowiedź (pytań i odpowiedzi) i opcjonalne metadane skojarzone z każdej pary pytań i odpowiedzi.

## <a name="key-knowledge-base-concepts"></a>Pojęcia dotyczące klucza bazy wiedzy

* **Pytania dotyczące** -pytanie zawiera tekst, który najlepiej reprezentuje zapytanie użytkownika. 
* **Odpowiedzi** — odpowiedź jest odpowiedzi, który jest zwracany, jeśli kwerenda użytkownika jest dopasowywany skojarzone zapytania.  
* **Metadane** -metadane są znaczniki skojarzone z pary pytań i odpowiedzi i są reprezentowane jako pary klucz wartość. Tagi metadanych są używane do filtrowania par QnA i ograniczają zestaw, w jakim jest wykonywane Dopasowywanie zapytań.

Pojedynczy QnA, reprezentowany przez identyfikator numeryczny pytań i odpowiedzi ma wiele wariantów pytanie (alternatywny pytań) wszystkie mapowane do jednej odpowiedzi. Ponadto każda para może mieć skojarzone wiele pól metadanych: jeden klucz i jedna wartość.

![Usługa QnA Maker baz wiedzy](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Format zawartości bazy wiedzy

Po sformatowanej zawartości jest pozyskiwania w bazie wiedzy, narzędzie QnA Maker próbuje przekonwertować zawartości markdown. Odczyt [to](https://aka.ms/qnamaker-docs-markdown-support) blog, aby zrozumieć języku znaczników markdown formatuje zrozumiały dla większości klientów rozmowy.

Pola metadanych składają się z pary klucz wartość oddzielone dwukropkiem **(produkt: strzępiarka)** . Klucz i wartość musi być tylko tekst. Klucza metadanych nie może zawierać spacji. Metadane obsługują tylko jedną wartość na klucz.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Jak QnA Maker przetwarza zapytanie użytkownika w celu wybrania najlepszej odpowiedzi

Przeszkolony i [opublikowany](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QNA Maker baza wiedzy otrzymuje zapytanie użytkownika z bot lub innej aplikacji klienckiej w [interfejsie API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Na poniższym diagramie przedstawiono proces po odebraniu zapytania użytkownika.

![Proces klasyfikowania zapytania użytkownika](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

Ten proces został wyjaśniony w poniższej tabeli:

|Krok|Cel|
|--|--|
|1|Aplikacja kliencka wysyła zapytanie użytkownika do [interfejsu API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|Program Qnaer wstępnie przetwarza zapytanie użytkownika przy użyciu wykrywania języka, modułu sprawdzania pisowni i wyłączników słów.|
|3|Przetwarzanie wstępne jest podejmowane w celu zmiany zapytania użytkownika w celu uzyskania najlepszych wyników wyszukiwania.|
|4|To zmienione zapytanie jest wysyłane do indeksu Azure Search i otrzymuje `top` liczbę wyników. Jeśli prawidłowa odpowiedź nie jest w tych wynikach, zwiększ wartość `top` nieco. Zazwyczaj wartość 10 dla `top` robót budowlanych w 90% zapytań.|
|5|QnA Maker stosuje zaawansowane cechowania, aby określić poprawność pobranych Azure Search wyników dla kwerendy użytkownika. |
|6|Przeszkolony model rangi używa wyniku funkcji, od kroku 5, do określania rangi Azure Search wyników.|
|7|Nowe wyniki są zwracane do aplikacji klienckiej w kolejności uporządkowanej.|
|||

Używane funkcje obejmują, ale nie są ograniczone do semantyki na poziomie wyrazów, ważności na poziomie terminu w korpus i głębokiego uczenia się modeli semantycznych w celu określenia podobieństwa i zgodności między dwoma ciągami tekstowymi.

## <a name="http-request-and-response-with-endpoint"></a>Żądanie HTTP i odpowiedź z punktem końcowym
Po opublikowaniu bazy wiedzy Usługa tworzy **punkt końcowy** http oparty na PROTOKOLe REST, który można zintegrować z aplikacją, zazwyczaj bot rozmowy. 

### <a name="the-user-query-request-to-generate-an-answer"></a>Żądanie kwerendy użytkownika w celu wygenerowania odpowiedzi

**Zapytanie użytkownika** to pytanie, że użytkownik końcowy prosi o bazę wiedzy, np `How do I add a collaborator to my app?`. Zapytanie jest często w formacie języka naturalnego lub kilka słów kluczowych reprezentujących pytanie, np `help with collaborators`. Zapytanie jest wysyłane do Twojej wiedzy z **żądania** http w aplikacji klienckiej.

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Aby kontrolować odpowiedź, należy ustawić właściwości, takie jak [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)i [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Użyj [kontekstu konwersacji](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) z [funkcją](../how-to/multiturn-conversation.md) wieloskładnikową, aby zachować konwersację w celu uściślenia pytań i odpowiedzi, aby znaleźć poprawną i końcową odpowiedź.

### <a name="the-response-from-a-call-to-generate-answer"></a>Odpowiedź z wywołania w celu wygenerowania odpowiedzi

**Odpowiedź** http to odpowiedź pobierana z bazy wiedzy, na podstawie najlepszego dopasowania dla danego zapytania użytkownika. Odpowiedź zawiera odpowiedź i wynik przewidywania. Jeśli zażądano więcej niż jednej górnej odpowiedzi z `top` właściwością, uzyskasz więcej niż jedną największą odpowiedź z wynikiem. 

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

### <a name="test-and-production-knowledge-base"></a>Baza wiedzy testowej i produkcyjnej
Baza wiedzy jest repozytorium pytania i odpowiedzi na utworzone, przechowywane i używane za pośrednictwem usługi QnA Maker. Każda warstwa usługi QnA Maker może służyć do wielu baz wiedzy.

Baza wiedzy ma dwa stany - Test opublikowane. 

**Baza wiedzy testowej** to wersja, która jest edytowana, zapisywana i przetestowana pod kątem dokładności i kompletności odpowiedzi. Zmiany wprowadzone w bazie wiedzy knowledge base test nie wpływają na użytkownika końcowego bota aplikacji/rozmowy. Baza wiedzy o testowaniu jest znana `test` jako żądanie HTTP. 

**Opublikowana baza wiedzy** jest wersją używaną w programie Chat bot/aplikacji. Akcja publikowanie bazy wiedzy umieszcza zawartość bazy wiedzy testu w opublikowaną wersję elementu w bazie wiedzy knowledge base. Ponieważ opublikowanych wiedzy wersję, która korzysta z aplikacji za pośrednictwem punktu końcowego, należy z rozwagą aby upewnić się, że zawartość jest poprawna i dobrze przetestowanych. Opublikowana baza wiedzy jest znana jako `prod` żądanie HTTP. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Cykl życia projektowania bazy wiedzy](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Zobacz także

[Omówienie usługi QnA Maker](../Overview/overview.md)

Tworzenie i edytowanie bazy wiedzy przy użyciu: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [Zestaw SDK platformy .NET](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Generuj odpowiedź przy użyciu: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [Zestaw SDK platformy .NET](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
