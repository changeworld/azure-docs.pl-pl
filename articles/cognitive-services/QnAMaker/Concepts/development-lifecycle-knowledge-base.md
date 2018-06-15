---
title: Programowanie cyklem życia wiedzy - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Cyklu programistycznym z bazy wiedzy
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 9ecdd2c7823eed145621b214690eff7681065507
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "35356035"
---
# <a name="knowledge-base-lifecycle"></a>Cykl życia wiedzy
Maker — strona główna uczy się najlepiej w iteracji cyklu zmian modelu, utterance przykłady publikowania i zbieranie danych z zapytania punktu końcowego. 

![Tworzenie cyklu](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Tworzenie Maker — strona główna bazy wiedzy
Punkt końcowy bazy wiedzy knowledge base (KB) Maker — strona główna zawiera najlepszego dopasowania odpowiedzi na kwerendę użytkownika, na podstawie zawartości KB. Tworzenie bazy wiedzy jest akcja jednorazowa przygotowywania zawartości repozytorium pytania, odpowiedzi i skojarzonych metadanych. Baza wiedzy mogą być tworzone przez przeszukiwania istniejące zawartości, takiej jak strony — często zadawane pytania, podręczniki lub strukturalnych pary Q-A. Dowiedz się, jak [utworzyć bazę wiedzy](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testowanie i aktualizowanie bazy wiedzy knowledge base
Baza wiedzy jest gotowy do testowania, gdy jest wypełnione z zawartością, redakcyjna lub za pomocą automatycznego wyodrębniania. Testowanie może odbywać się za pośrednictwem **testu** panelu, wprowadzając typowych kwerend użytkownika i weryfikowanie, czy odpowiedzi zwracane zgodnie z oczekiwaniami i wystarczające wynik zaufania. Możesz dodać alternatywne pytania, aby poprawić wyniki niski zaufania. Można także dodać nowe odpowiedzi, gdy zapytanie zwraca "nie znaleziono dopasowania w KB" Domyślna odpowiedź. Pętla ścisłej aktualizacji testu kontynuuje aż do uzyskania wymaganych wyników. Dowiedz się, jak [testowanie bazy wiedzy](../How-To/test-knowledge-base.md).

Dla dużych KB/s testowania można zautomatyzować za pomocą generateAnswer interfejsów API. 

## <a name="publish-the-knowledge-base"></a>Publikowanie w bazie wiedzy
Po zakończeniu testowania w bazie wiedzy, można opublikować. Publikowanie wypchnięć najnowszą wersję przetestowany wiedzy do dedykowanych usługi Azure Search index reprezentujący **opublikowane** bazy wiedzy knowledge base. Tworzy również punkt końcowy, który można wywołać w aplikacji lub bot rozmów.

Dzięki temu wszystkie zmiany do wersji testu w bazie wiedzy nie wpływają na opublikowanej wersji, która może być na żywo w aplikacji produkcyjnej.

Każdy z tych baz wiedzy można zastosować do testowania oddzielnie. Korzystanie z interfejsów API, można kierować bazy wiedzy z wersją testową `isTest=true` flagi w wywołaniu generateAnswer.

Dowiedz się, jak [publikowania bazy wiedzy](../How-To/publish-knowledge-base.md).

## <a name="monitor-usage"></a>Monitorowanie użycia
Aby móc zalogować się w dziennikach rozmów usługi, należy włączyć usługę Application Insights po możesz [tworzenia usługi Maker — strona główna](../How-To/set-up-qnamaker-service-azure.md).

Możesz uzyskać różne analizy użycia Twojej usługi. Dowiedz się więcej o sposobie używania usługi application insights, aby uzyskać [analytics usługi Maker — strona główna](../How-To/get-analytics-knowledge-base.md).

W oparciu o informacje z Twojej analityka, wprowadź odpowiednie [aktualizacji do bazy wiedzy](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wynik zaufania](./confidence-score.md)

## <a name="see-also"></a>Zobacz także 

[Baza wiedzy](./knowledge-base.md)
[omówienie Maker — strona główna](../Overview/overview.md)
