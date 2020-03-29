---
title: Przeglądanie wypowiedzi użytkownika — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Dzięki aktywnemu uczeniu wypowiedzi punktu końcowego przeglądu dla poprawnej intencji i jednostki. Usługa LUIS wybiera wypowiedzi punktu końcowego, o które nie ma pewności.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: 375d4b4e7c3fcafbdfde1ff447bedc3e16aff2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219941"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Pojęcia dotyczące włączania aktywnego uczenia się przez przeglądanie wypowiedzi punktów końcowych
Aktywne uczenie się jest jedną z trzech strategii poprawiania dokładności przewidywania i najłatwiejszych do wdrożenia. Dzięki aktywnemu uczeniu wypowiedzi punktu końcowego przeglądu dla poprawnej intencji i jednostki. Usługa LUIS wybiera wypowiedzi punktu końcowego, o które nie ma pewności.

## <a name="what-is-active-learning"></a>Czym jest aktywne uczenie się
Aktywne uczenie się jest procesem dwuetapowym. Najpierw usługa LUIS wybiera wypowiedzi, które otrzymuje w punkcie końcowym aplikacji, które wymagają weryfikacji. Drugi krok jest wykonywany przez właściciela aplikacji lub współpracownika, aby sprawdzić poprawność wybranych wypowiedzi do [przeglądu,](luis-how-to-review-endpoint-utterances.md)w tym poprawne intencji i wszystkie jednostki w ramach intencji. Po przejrzeniu wypowiedzi, pociągu i opublikować aplikację ponownie.

## <a name="which-utterances-are-on-the-review-list"></a>Które wypowiedzi znajdują się na liście recenzji
Usługa LUIS dodaje wypowiedzi do listy recenzji, gdy górna intencja wypalania ma niski wynik lub wyniki dwóch najlepszych intencji są zbyt blisko.

## <a name="single-pool-for-utterances-per-app"></a>Pojedyncza pula wypowiedzi na aplikację
Lista **przejrzyj wypowiedzi punktu końcowego** nie zmienia się na podstawie wersji. Jest tylko jedna pula wypowiedzi do przejrzenia, niezależnie od aktywnie edytowanej wersji wypowiedzi ani od wersji aplikacji opublikowanej w punkcie końcowym.

## <a name="where-are-the-utterances-from"></a>Gdzie są wypowiedzi z
Wypowiedzi punktu końcowego są pobierane z zapytań użytkowników końcowych w punkcie końcowym HTTP aplikacji. Jeśli aplikacja nie została opublikowana lub nie otrzymała jeszcze trafień, nie masz żadnych wypowiedzi do przejrzenia. Jeśli nie trafienia punktu końcowego są odbierane dla określonego zamiaru lub jednostki, nie masz wypowiedzi do przeglądu, które je zawierają.

## <a name="schedule-review-periodically"></a>Okresowo planuj przegląd
Przeglądanie sugerowane wypowiedzi nie musi być wykonywane codziennie, ale powinny być częścią regularnej konserwacji usługi LUIS.

## <a name="delete-review-items-programmatically"></a>Programowo usuwanie elementów recenzji
Użyj interfejsu API **[usuwania wypowiedzi bez etykiety.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** Wykonaj te wypowiedzi przed usunięciem, **[eksportując pliki dziennika](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)**.

## <a name="enable-active-learning"></a>Włącz aktywne uczenie się

Aby włączyć aktywne uczenie, należy rejestrować zapytania użytkowników. Jest to realizowane przez wywołanie [kwerendy punktu końcowego](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) `log=true` z querystring parametru i wartości.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przeglądać](luis-how-to-review-endpoint-utterances.md) wypowiedzi punktów końcowych
