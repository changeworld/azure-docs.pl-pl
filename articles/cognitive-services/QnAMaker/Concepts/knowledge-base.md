---
title: Baza wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker wiedzy składa się z zestaw par pytanie/odpowiedź (pytań i odpowiedzi) i opcjonalne metadane skojarzone z każdej pary pytań i odpowiedzi.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e40af9b2362ee52a1d00f29cdc112d3c2b9a842
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565848"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Co to jest usługa QnA Maker wiedzy?

Usługa QnA Maker wiedzy składa się z zestaw par pytanie/odpowiedź (pytań i odpowiedzi) i opcjonalne metadane skojarzone z każdej pary pytań i odpowiedzi.

## <a name="key-knowledge-base-concepts"></a>Pojęcia dotyczące klucza bazy wiedzy

* **Pytania dotyczące** -pytanie zawiera tekst, który najlepiej reprezentuje zapytanie użytkownika. 
* **Odpowiedzi** — odpowiedź jest odpowiedzi, który jest zwracany, jeśli kwerenda użytkownika jest dopasowywany skojarzone zapytania.  
* **Metadane** -metadane są znaczniki skojarzone z pary pytań i odpowiedzi i są reprezentowane jako pary klucz wartość. Znaczniki metadane są używane do filtrowania pary pytań i odpowiedzi i ograniczyć zestaw, w którym zapytaniu odpowiedników.

Pojedynczy QnA, reprezentowany przez identyfikator numeryczny pytań i odpowiedzi ma wiele wariantów pytanie (alternatywny pytań) wszystkie mapowane do jednej odpowiedzi. Ponadto każda para takie może mieć wiele pól metadane skojarzone z nią: jeden klucz i jedną wartość.

![Usługa QnA Maker baz wiedzy](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Format zawartości bazy wiedzy

Po sformatowanej zawartości jest pozyskiwania w bazie wiedzy, narzędzie QnA Maker próbuje przekonwertować zawartości markdown. Odczyt [to](https://aka.ms/qnamaker-docs-markdown-support) blog, aby zrozumieć języku znaczników markdown formatuje zrozumiały dla większości klientów rozmowy.

Pola metadanych składają się z pary klucz wartość oddzielone dwukropkiem **(produkt: strzępiarka)** . Klucz i wartość musi być tylko tekst. Klucza metadanych nie może zawierać spacji. Metadane obsługuje tylko jedną wartość na klucz.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Jak usługa QnA Maker przetwarza zapytania użytkownika, aby wybrać najlepszą odpowiedź

Uczony i [opublikowane](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker wiedzy odbierze zapytanie użytkownika, z robota lub innych aplikacji klienckiej w [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). Na poniższym diagramie przedstawiono proces po otrzymaniu kwerendy użytkownika.

![Proces klasyfikacji dla zapytania użytkownika](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

W poniższej tabeli objaśniono proces:

|Krok|Przeznaczenie|
|--|--|
|1|Aplikacja kliencka wysyła zapytanie użytkownika, aby [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|Usługa Qna Maker przetwarzania wstępnego zapytanie użytkownika o wykrywanie języka, spellers i moduły dzielenia wyrazów.|
|3|Tego przetwarzania wstępnego jest pobierana do zmiany zapytania użytkownika w celu uzyskania najlepszych wyników wyszukiwania.|
|4|Ta zmienionego zapytanie jest wysyłane do indeksu wyszukiwania platformy Azure, odbieranie `top` liczba wyników. Jeśli nie ma poprawną odpowiedź tych wyników, należy zwiększyć wartość `top` nieco. Zazwyczaj wartość 10 `top` działa w 90% firm z zapytania.|
|5|Usługa QnA Maker ma zastosowanie zaawansowanych cechowania w celu określenia poprawności pobrano wyniki usługi Azure Search dla zapytania użytkownika. |
|6|Uczony model oceniania używa wynik funkcji, w kroku 5, pozycji wyniki wyszukiwania platformy Azure.|
|7|Nowe wyniki są zwracane do aplikacji klienckiej w kolejności rangi.|
|||

Funkcje używane obejmują, ale nie są ograniczone do poziomu word semantykę, termin poziomu ważności w głównej części i głębokiego nauczony modeli semantycznych na ustalenie podobieństwa i istotność między dwa ciągi tekstowe.


## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Cykl życia projektowania bazy wiedzy](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Zobacz także

[Omówienie usługi QnA Maker](../Overview/overview.md)
