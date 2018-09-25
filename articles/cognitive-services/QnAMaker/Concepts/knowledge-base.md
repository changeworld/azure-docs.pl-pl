---
title: Baza wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker wiedzy składa się z zestaw par pytanie/odpowiedź (pytań i odpowiedzi) i opcjonalne metadane skojarzone z każdej pary pytań i odpowiedzi.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: cb3426a960a6644b3ae149f02055cdb083febca7
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040765"
---
# <a name="knowledge-base"></a>Baza wiedzy

Usługa QnA Maker wiedzy składa się z zestaw par pytanie/odpowiedź (pytań i odpowiedzi) i opcjonalne metadane skojarzone z każdej pary pytań i odpowiedzi.

## <a name="key-knowledge-base-concepts"></a>Pojęcia dotyczące klucza bazy wiedzy

* **Pytania dotyczące** -pytanie zawiera tekst, który najlepiej reprezentuje zapytanie użytkownika. 
* **Odpowiedzi** — odpowiedź jest odpowiedzi, który jest zwracany, jeśli kwerenda użytkownika jest dopasowywany skojarzone zapytania.  
* **Metadane** -metadane są znaczniki skojarzone z pary pytań i odpowiedzi i są reprezentowane jako pary klucz wartość. Metadane są używane do filtrowania pary pytań i odpowiedzi i ograniczyć zestaw, w którym zapytaniu odpowiedników.

Pojedynczy QnA, reprezentowany przez identyfikator numeryczny pytań i odpowiedzi ma wiele wariantów pytanie (alternatywny pytań) wszystkie mapowane do jednej odpowiedzi. Ponadto każda para takie może mieć wiele pól metadane skojarzone z nią.

![Usługa QnA Maker baz wiedzy](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Format zawartości bazy wiedzy

Po sformatowanej zawartości jest pozyskiwania w bazie wiedzy, narzędzie QnA Maker próbuje przekonwertować zawartości markdown. Odczyt [to](https://aka.ms/qnamaker-docs-markdown-support) blog, aby zrozumieć języku znaczników markdown formatuje zrozumiały dla większości klientów rozmowy.

Pola metadanych składają się z pary klucz wartość oddzielone dwukropkiem **(produkt: strzępiarka)**. Klucz i wartość musi być tylko tekst. Klucza metadanych nie może zawierać spacji.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Cykl życia projektowania bazy wiedzy](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Zobacz także

[Omówienie usługi QnA Maker](../Overview/overview.md)