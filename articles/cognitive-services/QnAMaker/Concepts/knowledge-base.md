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
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 022b16669791b9b9cce066b3dd17c70b33569cc0
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955230"
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


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Cykl życia projektowania bazy wiedzy](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Zobacz także

[Omówienie usługi QnA Maker](../Overview/overview.md)
