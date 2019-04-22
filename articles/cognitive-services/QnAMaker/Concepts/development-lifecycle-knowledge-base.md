---
title: Cykl życia bazy wiedzy knowledge base — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker uczy się najlepiej w iteracyjny cykl zmiany modelu, przykłady wypowiedź, publikowanie oraz zbieranie danych z punktu końcowego zapytań.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/16/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 3f78b8a2566137d596f4ab3f083e1d14289365c3
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684025"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Cykl życia wiedzy, w usługi QnA Maker
Usługa QnA Maker uczy się najlepiej w iteracyjny cykl zmiany modelu, przykłady wypowiedź, publikowanie oraz zbieranie danych z punktu końcowego zapytań. 

![Cykl tworzenia](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Tworzenie bazy wiedzy usługi QnA Maker
Usługa QnA Maker punktu końcowego z bazy wiedzy knowledge base (KB) zapewnia najlepsze dopasowanie odpowiedzi z zapytaniem użytkownika na podstawie zawartości KB. Tworzenie bazy wiedzy jest akcją jednorazową Konfigurowanie repozytorium zawartości pytań, odpowiedzi i skojarzonych metadanych. Baza wiedzy mogą być tworzone przez przeszukiwania zawartości istniejącego takich jak strony — często zadawane pytania, podręczników lub strukturą pary A funkcji pytania i odpowiedzi. Dowiedz się, jak [tworzenie bazy wiedzy](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testowanie i aktualizowanie bazy wiedzy

Baza wiedzy jest gotowe do testowania, gdy jest on wypełniany zawartości, przez jej tradycyjne zredagowanie lub za pomocą automatycznego wyodrębniania. Interaktywne testowania może odbywać się w portalu narzędzia QnA Maker za pośrednictwem **testu** panelu, wprowadzając typowych zapytań użytkownika i weryfikowanie, czy zwrócone odpowiedzi z prawidłową odpowiedź i współczynnik ufności wystarczające. 

* **Aby rozwiązać problem niskiej ufności wyniki**: Dodaj alternatywne pytania. 
* **Jeśli zapytanie zwraca niepoprawnie [Domyślna odpowiedź](confidence-score.md#change-default-answer)**: Dodaj nowe odpowiedzi na prawidłowe zapytania. 

Tej pętli aktualizacji testu będzie kontynuowane, dopóki nie jesteś zadowolony z wyników. Dowiedz się, jak [test bazy wiedzy](../How-To/test-knowledge-base.md).

Dla dużych artykułów bazy wiedzy, należy korzystać z automatycznych testów z [generateAnswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) i `isTest` treści właściwości, które zapytania `test` wiedzy zamiast opublikowanych bazy wiedzy knowledge base. 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publikowanie bazy wiedzy
Gdy skończysz testowanie bazy wiedzy knowledge base, możesz ją opublikować. Publikowanie wypchnięć najnowszą wersję przetestowane wiedzy do dedykowanych usługi Azure Search index reprezentujący **opublikowane** bazy wiedzy knowledge base. Zostanie również utworzony punkt końcowy, który można wywoływać w aplikacji lub czatbocie.

Dzięki temu wszelkie zmiany zostaną wprowadzone do wersji testu w bazie wiedzy knowledge base nie wpływają na opublikowanej wersji, które mogą okazać się na żywo w aplikacji produkcyjnej.

Każda z tych baz wiedzy można zastosować do testowania oddzielnie. Korzystając z interfejsów API, możesz wybrać docelową wersją testową bazy wiedzy przy użyciu `isTest` właściwość w wywołaniu generateAnswer treści.

Dowiedz się, jak [Opublikuj bazę wiedzy](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorowanie użycia
Aby móc rejestrować dzienniki czatu usługi, czy należy włączyć usługi Application Insights po użytkownik [utworzenie usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

Można umieścić dane analityczne różnych użycia usługi. Dowiedz się więcej o tym, jak używać usługi application insights, aby uzyskać [analizy dotyczące usługi QnA Maker](../How-To/get-analytics-knowledge-base.md).

Oparte na ucz się od analizy, wprowadź odpowiednie [aktualizacje bazy wiedzy](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Współczynnik ufności](./confidence-score.md)

## <a name="see-also"></a>Zobacz także 

[Baza wiedzy](./knowledge-base.md)
[QnA Maker — omówienie](../Overview/overview.md)
