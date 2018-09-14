---
title: Cykl życia projektowania — wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker uczy się najlepiej w iteracyjny cykl zmiany modelu, przykłady wypowiedź, publikowanie oraz zbieranie danych z punktu końcowego zapytań.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: d38eaf466e6013d9b20063fe1e1feb2c36ed4205
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543419"
---
# <a name="knowledge-base-lifecycle"></a>Cykl życia w bazie wiedzy Knowledge base
Usługa QnA Maker uczy się najlepiej w iteracyjny cykl zmiany modelu, przykłady wypowiedź, publikowanie oraz zbieranie danych z punktu końcowego zapytań. 

![Cykl tworzenia](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Tworzenie bazy wiedzy usługi QnA Maker
Usługa QnA Maker punktu końcowego z bazy wiedzy knowledge base (KB) zapewnia najlepsze dopasowanie odpowiedzi z zapytaniem użytkownika na podstawie zawartości KB. Tworzenie bazy wiedzy jest akcją jednorazową Konfigurowanie repozytorium zawartości pytań, odpowiedzi i skojarzonych metadanych. Baza wiedzy mogą być tworzone przez przeszukiwania zawartości istniejącego takich jak strony — często zadawane pytania, podręczników lub strukturą pary A funkcji pytania i odpowiedzi. Dowiedz się, jak [tworzenie bazy wiedzy](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testowanie i aktualizowanie bazy wiedzy
Baza wiedzy jest gotowe do testowania, gdy jest on wypełniany zawartości, przez jej tradycyjne zredagowanie lub za pomocą automatycznego wyodrębniania. Testowanie może odbywać się za pośrednictwem **testu** panelu, wprowadzając typowych zapytań użytkownika i weryfikowanie, czy odpowiedzi, zwracane są zgodne z oczekiwaniami i współczynnik ufności wystarczające. Można dodawać alternatywnego pytania, aby poprawić wyniki niski zaufania. Możesz także dodać nowe odpowiedzi, gdy zapytanie zwróci wartość "nie znaleziono dopasowania w w bazie wiedzy" Domyślna odpowiedź. Tej pętli aktualizacji testu będzie kontynuowane, dopóki nie jesteś zadowolony z wyników. Dowiedz się, jak [test bazy wiedzy](../How-To/test-knowledge-base.md).

Dla dużych artykułów bazy wiedzy testowania można zautomatyzować za pomocą generateAnswer interfejsów API. 

## <a name="publish-the-knowledge-base"></a>Publikowanie bazy wiedzy
Gdy skończysz testowanie bazy wiedzy knowledge base, możesz ją opublikować. Publikowanie wypchnięć najnowszą wersję przetestowane wiedzy do dedykowanych usługi Azure Search index reprezentujący **opublikowane** bazy wiedzy knowledge base. Tworzy również punkt końcowy, który można wywoływać w aplikacji lub czatbot.

Dzięki temu wszelkie zmiany zostaną wprowadzone do wersji testu w bazie wiedzy knowledge base nie wpływają na opublikowanej wersji, które mogą okazać się na żywo w aplikacji produkcyjnej.

Każda z tych baz wiedzy można zastosować do testowania oddzielnie. Korzystając z interfejsów API, możesz wybrać docelową wersją testową bazy wiedzy przy użyciu `isTest=true` flagi w wywołaniu generateAnswer.

Dowiedz się, jak [Opublikuj bazę wiedzy](../How-To/publish-knowledge-base.md).

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
