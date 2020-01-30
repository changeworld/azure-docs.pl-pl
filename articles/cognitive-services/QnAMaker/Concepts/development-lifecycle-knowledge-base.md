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
ms.date: 01/27/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b1978e45a7554358ddd948879143411f89e4c1b2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843409"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Cykl życia wiedzy, w usługi QnA Maker
Usługa QnA Maker uczy się najlepiej w iteracyjny cykl zmiany modelu, przykłady wypowiedź, publikowanie oraz zbieranie danych z punktu końcowego zapytań.

![Cykl tworzenia](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Tworzenie bazy wiedzy usługi QnA Maker
Usługa QnA Maker punktu końcowego z bazy wiedzy knowledge base (KB) zapewnia najlepsze dopasowanie odpowiedzi z zapytaniem użytkownika na podstawie zawartości KB. Tworzenie bazy wiedzy jest akcją jednorazową Konfigurowanie repozytorium zawartości pytań, odpowiedzi i skojarzonych metadanych. Baza wiedzy mogą być tworzone przez przeszukiwania zawartości istniejącego takich jak strony — często zadawane pytania, podręczników lub strukturą pary A funkcji pytania i odpowiedzi. Dowiedz się, jak [tworzenie bazy wiedzy](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testowanie i aktualizowanie bazy wiedzy

Baza wiedzy jest gotowe do testowania, gdy jest on wypełniany zawartości, przez jej tradycyjne zredagowanie lub za pomocą automatycznego wyodrębniania. Testy interaktywne można wykonać w portalu QnA Maker za pomocą panelu **test** , wprowadzając typowe zapytania użytkownika i sprawdzając, czy odpowiedzi zwrócone z prawidłową odpowiedzią i odpowiednim wynikiem pewności.

* **Aby naprawić oceny o niskim poziomie pewności**: Dodaj alternatywne pytania.
* **Gdy zapytanie niepoprawnie zwróci [domyślną odpowiedź](../How-to/change-default-answer.md)** : Dodaj nowe odpowiedzi do poprawnego pytania.

Tej pętli aktualizacji testu będzie kontynuowane, dopóki nie jesteś zadowolony z wyników. Dowiedz się, jak [test bazy wiedzy](../How-To/test-knowledge-base.md).

W przypadku dużych artykułów bazy wiedzy Użyj testów automatycznych z [interfejsem API generateAnswer](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) i właściwością treści `isTest`, która wysyła zapytanie do bazy wiedzy `test` zamiast opublikowanej bazy wiedzy.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publikowanie bazy wiedzy
Gdy skończysz testowanie bazy wiedzy knowledge base, możesz ją opublikować. Opublikowanie powoduje wypchnięcie najnowszej wersji przetestowanej bazy wiedzy na dedykowany indeks Wyszukiwanie poznawcze platformy Azure reprezentujący **opublikowaną** bazę wiedzy. Zostanie również utworzony punkt końcowy, który można wywoływać w aplikacji lub czatbocie.

Dzięki temu wszelkie zmiany zostaną wprowadzone do wersji testu w bazie wiedzy knowledge base nie wpływają na opublikowanej wersji, które mogą okazać się na żywo w aplikacji produkcyjnej.

Każda z tych baz wiedzy można zastosować do testowania oddzielnie. Korzystając z interfejsów API, można wskazać wersję testową bazy wiedzy z właściwością `isTest` treści w wywołaniu generateAnswer.

Dowiedz się, jak [Opublikuj bazę wiedzy](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorowanie użycia
Aby móc rejestrować dzienniki czatu usługi, czy należy włączyć usługi Application Insights po użytkownik [utworzenie usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

Można umieścić dane analityczne różnych użycia usługi. Dowiedz się więcej o tym, jak używać usługi application insights, aby uzyskać [analizy dotyczące usługi QnA Maker](../How-To/get-analytics-knowledge-base.md).

Oparte na ucz się od analizy, wprowadź odpowiednie [aktualizacje bazy wiedzy](../How-To/edit-knowledge-base.md).

## <a name="version-control-for-data-in-your-knowledge-base"></a>Kontrola wersji danych w bazie wiedzy

Kontrola wersji danych jest dostępna za pomocą funkcji Import/Export na stronie **Ustawienia** w portalu QNA Maker.

Można utworzyć kopię zapasową bazy wiedzy, eksportując bazę wiedzy w formacie `.tsv` lub `.xls`. Po wyeksportowaniu należy uwzględnić ten plik jako część regularnego sprawdzania kontroli źródła.

Jeśli musisz wrócić do określonej wersji, musisz zaimportować ten plik z systemu lokalnego. Wyeksportowana baza wiedzy **może** być używana tylko przez import na stronie **Ustawienia** . Nie można jej użyć jako źródła danych dokumentu lub pliku adresu URL. Spowoduje to zamienienie pytań i odpowiedzi znajdujących się w bazie wiedzy z zawartością zaimportowanego pliku.

## <a name="test-and-production-knowledge-base"></a>Baza wiedzy testowej i produkcyjnej
Baza wiedzy to repozytorium pytań i zestawów odpowiedzi utworzonych, obsługiwanych i używanych przez QnA Maker. Każdy zasób QnA Maker może zawierać wiele baz wiedzy.

Baza wiedzy ma dwa stany: *test* i *opublikowano*.

### <a name="test-knowledge-base"></a>Baza wiedzy testowej

*Baza wiedzy testowej* jest obecnie edytowana, zapisywana i przetestowana pod kątem dokładności i kompletności odpowiedzi. Zmiany wprowadzone w bazie wiedzy testowej nie mają wpływu na użytkownika końcowego aplikacji lub rozmowy bot. Baza wiedzy testowej jest znana jako `test` w żądaniu HTTP. Wiedza `test` jest dostępna w okienku interaktywnego **testu** portalu QNA Maker.

### <a name="production-knowledge-base"></a>Baza wiedzy produkcyjnej

*Opublikowana baza wiedzy* jest wersją używaną w programie Chat bot lub aplikacji. Akcja publikowania bazy wiedzy umieszcza zawartość bazy wiedzy testowej w opublikowanej wersji bazy wiedzy. Ponieważ opublikowana baza wiedzy jest wersją używaną przez aplikację w punkcie końcowym, upewnij się, że zawartość jest poprawna i dobrze przetestowana. Opublikowana baza wiedzy jest znana jako `prod` w żądaniu HTTP.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Współczynnik ufności](./confidence-score.md)