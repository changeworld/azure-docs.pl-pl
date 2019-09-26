---
title: Baza wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Baza wiedzy QnA Maker składa się z zestawu par pytań i odpowiedzi (QnA) oraz opcjonalnych metadanych skojarzonych z poszczególnymi parami QnA.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 78fc9fe34eb3463021dae69990fe1d30668d453f
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300508"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Co to jest QnA Maker baza wiedzy?

Baza wiedzy QnA Maker składa się z zestawu par pytań i odpowiedzi (QnA) oraz opcjonalnych metadanych skojarzonych z poszczególnymi parami QnA.

## <a name="key-knowledge-base-concepts"></a>Pojęcia dotyczące klucza bazy wiedzy

* **Pytania**: Pytanie zawiera tekst, który najlepiej reprezentuje zapytanie użytkownika. 
* **Odpowiedzi**: Odpowiedź jest odpowiedzią zwracaną po dopasowaniu zapytania użytkownika do skojarzonego pytania.  
* **Metadane**: Metadane są tagami skojarzonymi z parą QnA i są reprezentowane jako pary klucz-wartość. Tagi metadanych są używane do filtrowania par QnA i ograniczają zestaw, w jakim jest wykonywane Dopasowywanie zapytań.

Pojedynczy QnA, reprezentowany przez identyfikator numeryczny pytań i odpowiedzi ma wiele wariantów pytanie (alternatywny pytań) wszystkie mapowane do jednej odpowiedzi. Ponadto każda para może mieć skojarzone wiele pól metadanych: jeden klucz i jedna wartość.

![Usługa QnA Maker baz wiedzy](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Format zawartości bazy wiedzy

Po sformatowanej zawartości jest pozyskiwania w bazie wiedzy, narzędzie QnA Maker próbuje przekonwertować zawartości markdown. Przeczytaj [ten blog](https://aka.ms/qnamaker-docs-markdown-support) , aby dowiedzieć się więcej o formatach promocji, które są zrozumiałe dla większości klientów rozmowy.

Pola metadanych zawierają pary klucz-wartość oddzielone dwukropkiem, takie jak produkt: Shredder. Zarówno klucz, jak i wartość muszą być tylko do tekstu. Klucza metadanych nie może zawierać spacji. Metadane obsługują tylko jedną wartość na klucz.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Jak QnA Maker przetwarza zapytanie użytkownika w celu wybrania najlepszej odpowiedzi

Przeszkolony i [opublikowany](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QNA Maker baza wiedzy otrzymuje zapytanie użytkownika z bot lub innej aplikacji klienckiej w [interfejsie API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Na poniższym diagramie przedstawiono proces po odebraniu zapytania użytkownika.

![Proces klasyfikowania zapytania użytkownika](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Proces rangi

Ten proces został wyjaśniony w poniższej tabeli.

|Krok|Cel|
|--|--|
|1|Aplikacja kliencka wysyła zapytanie użytkownika do [interfejsu API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker wstępnie przetworzy zapytanie użytkownika przy użyciu wykrywania języka, modułu sprawdzania pisowni i wyłączników słów.|
|3|Przetwarzanie wstępne jest podejmowane w celu zmiany zapytania użytkownika w celu uzyskania najlepszych wyników wyszukiwania.|
|4|To zmienione zapytanie jest wysyłane do indeksu Azure Search, który otrzymuje `top` liczbę wyników. Jeśli prawidłowa odpowiedź nie jest w tych wynikach, zwiększ wartość `top` nieco. Ogólnie rzecz biorąc, wartość 10 dla `top` robót budowlanych w 90% zapytań.|
|5|QnA Maker stosuje zaawansowane cechowania, aby określić poprawność pobranych Azure Search wyników dla zapytania użytkownika. |
|6|Przeszkolony model rangi używa wyniku funkcji, od kroku 5, do określania rangi Azure Search wyników.|
|7|Nowe wyniki są zwracane do aplikacji klienckiej w kolejności uporządkowanej.|
|||

Używane funkcje obejmują, ale nie są ograniczone do semantyki na poziomie wyrazów, ważności na poziomie terminu w korpus i głębokiego uczenia się modeli semantycznych w celu określenia podobieństwa i zgodności między dwoma ciągami tekstowymi.

## <a name="http-request-and-response-with-endpoint"></a>Żądanie HTTP i odpowiedź z punktem końcowym
Po opublikowaniu bazy wiedzy Usługa tworzy punkt końcowy HTTP oparty na protokole REST, który można zintegrować z aplikacją, zazwyczaj bot rozmowy. 

### <a name="the-user-query-request-to-generate-an-answer"></a>Żądanie kwerendy użytkownika w celu wygenerowania odpowiedzi

Zapytanie użytkownika to pytanie, że użytkownik końcowy prosi o bazę wiedzy, np `How do I add a collaborator to my app?`. Zapytanie jest często w formacie języka naturalnego lub kilka słów kluczowych, które reprezentują pytanie, na przykład `help with collaborators`. Zapytanie jest wysyłane do bazy wiedzy z żądania HTTP w aplikacji klienckiej.

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

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Odpowiedź z wywołania do wygenerowania odpowiedzi

Odpowiedź HTTP to odpowiedź pobierana z bazy wiedzy, na podstawie najlepszego dopasowania dla danego zapytania użytkownika. Odpowiedź zawiera odpowiedź i wynik przewidywania. Jeśli zażądano więcej niż jednej górnej odpowiedzi z `top` właściwością, uzyskasz więcej niż jedną największą odpowiedź z wynikiem. 

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
Baza wiedzy to repozytorium pytań i odpowiedzi utworzonych, obsługiwanych i używanych przez QnA Maker. Każda warstwa QnA Maker może być używana w wielu bazach wiedzy.

Baza wiedzy ma dwa stany: *test* i *opublikowano*.

*Baza wiedzy testowej* to wersja, która jest edytowana, zapisywana i przetestowana pod kątem dokładności i kompletności odpowiedzi. Zmiany wprowadzone w bazie wiedzy testowej nie mają wpływu na użytkownika końcowego aplikacji lub rozmowy bot. Baza wiedzy o testowaniu jest znana `test` jako żądanie HTTP. 

*Opublikowana baza wiedzy* jest wersją używaną w programie Chat bot lub aplikacji. Akcja publikowania bazy wiedzy umieszcza zawartość bazy wiedzy testowej w opublikowanej wersji bazy wiedzy. Ponieważ opublikowana baza wiedzy jest wersją używaną przez aplikację w punkcie końcowym, upewnij się, że zawartość jest poprawna i dobrze przetestowana. Opublikowana baza wiedzy jest znana jako `prod` żądanie HTTP.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Cykl życia projektowania bazy wiedzy](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Zobacz także

[Omówienie usługi QnA Maker](../Overview/overview.md)

Tworzenie i edytowanie bazy wiedzy przy użyciu: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [Zestaw SDK platformy .NET](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Generuj odpowiedź z: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [Zestaw SDK platformy .NET](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
