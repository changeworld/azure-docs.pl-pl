---
title: Cykl życia bazy wiedzy knowledge base — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker uczy się najlepiej w iteracyjny cykl zmiany modelu, przykłady wypowiedź, publikowanie oraz zbieranie danych z punktu końcowego zapytań.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 97a4673be2a611149806855e792c5bf1f7a0942a
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955175"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Cykl życia wiedzy, w usługi QnA Maker
Usługa QnA Maker uczy się najlepiej w iteracyjny cykl zmiany modelu, przykłady wypowiedź, publikowanie oraz zbieranie danych z punktu końcowego zapytań. 

![Cykl tworzenia](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Tworzenie bazy wiedzy usługi QnA Maker
Usługa QnA Maker punktu końcowego z bazy wiedzy knowledge base (KB) zapewnia najlepsze dopasowanie odpowiedzi z zapytaniem użytkownika na podstawie zawartości KB. Tworzenie bazy wiedzy jest akcją jednorazową Konfigurowanie repozytorium zawartości pytań, odpowiedzi i skojarzonych metadanych. Baza wiedzy mogą być tworzone przez przeszukiwania zawartości istniejącego takich jak strony — często zadawane pytania, podręczników lub strukturą pary A funkcji pytania i odpowiedzi. Dowiedz się, jak [tworzenie bazy wiedzy](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testowanie i aktualizowanie bazy wiedzy

Baza wiedzy jest gotowe do testowania, gdy jest on wypełniany zawartości, przez jej tradycyjne zredagowanie lub za pomocą automatycznego wyodrębniania. Testy interaktywne można wykonać w portalu QnA Maker za pomocą panelu **test** , wprowadzając typowe zapytania użytkownika i sprawdzając, czy odpowiedzi zwrócone z prawidłową odpowiedzią i odpowiednim wynikiem pewności. 

* **Aby naprawić oceny o niskim poziomie pewności**: Dodaj alternatywne pytania. 
* **Gdy zapytanie niepoprawnie zwróci [domyślną odpowiedź](confidence-score.md#change-default-answer)** : Dodaj nowe odpowiedzi do poprawnego pytania. 

Tej pętli aktualizacji testu będzie kontynuowane, dopóki nie jesteś zadowolony z wyników. Dowiedz się, jak [test bazy wiedzy](../How-To/test-knowledge-base.md).

W przypadku dużych artykułów bazy wiedzy Użyj testów automatycznych z [interfejsem API generateAnswer](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) i `isTest` właściwością Body, która `test` wysyła zapytanie do bazy wiedzy zamiast opublikowanej bazy wiedzy. 

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

Każda z tych baz wiedzy można zastosować do testowania oddzielnie. Korzystając z interfejsów API, można wskazać wersję testową bazy wiedzy z `isTest` właściwością Body w wywołaniu generateAnswer.

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
