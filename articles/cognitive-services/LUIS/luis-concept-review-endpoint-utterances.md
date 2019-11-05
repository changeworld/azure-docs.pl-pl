---
title: Przeglądanie użytkownika wypowiedź — LUIS
titleSuffix: Azure Cognitive Services
description: Dzięki aktywnej uczeniu punkt końcowy recenzji wyrażenia długości się w celu poprawnego zamiaru i jednostki. LUIS wybiera punkt końcowy wyrażenia długości nie ma pewności.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 772d2bdff0a5e1c6cd999482ec4ef6be29dc2bb1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486785"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Pojęcia dotyczące włączania aktywnego uczenia przez przeglądanie punktu końcowego wyrażenia długości
Usługa Active uczenie jest jedną z trzech strategii, aby poprawić dokładność przewidywania i najłatwiej ją wdrożyć. Dzięki aktywnej uczeniu punkt końcowy recenzji wyrażenia długości się w celu poprawnego zamiaru i jednostki. LUIS wybiera punkt końcowy wyrażenia długości nie ma pewności.

## <a name="what-is-active-learning"></a>Co to jest aktywna nauka
Usługa Active Learning jest procesem dwuetapowym. Najpierw LUIS wybiera wyrażenia długości, które są odbierane w punkcie końcowym aplikacji, który wymaga sprawdzenia poprawności. Drugi krok jest wykonywany przez właściciela aplikacji lub współpracownika, aby zweryfikować wybrane wyrażenia długości do [przeglądu](luis-how-to-review-endpoint-utterances.md), w tym odpowiednie zamierzenie i wszystkie jednostki w ramach zamiaru. Po przejrzeniu wyrażenia długości ponownie nauczenie i opublikowanie aplikacji. 

## <a name="which-utterances-are-on-the-review-list"></a>Które wyrażenia długości znajdują się na liście przeglądów
LUIS dodaje wyrażenia długości do listy przeglądów, gdy najwyższy zamiara uruchamiania ma niski wynik lub dwa wyniki założeń są zbyt blisko. 

## <a name="single-pool-for-utterances-per-app"></a>Pojedyncza Pula dla wyrażenia długości na aplikację
Lista **wyrażenia długości punktu końcowego recenzji** nie zmienia się w zależności od wersji. Jest tylko jedna pula wypowiedzi do przejrzenia, niezależnie od aktywnie edytowanej wersji wypowiedzi ani od wersji aplikacji opublikowanej w punkcie końcowym. 

## <a name="where-are-the-utterances-from"></a>Gdzie są wyrażenia długości
Punkty końcowe wyrażenia długości są pobierane z zapytań użytkowników końcowych w punkcie końcowym HTTP aplikacji. Jeśli Twoja aplikacja nie została opublikowana lub nie otrzymano jeszcze żadnych trafień, nie masz żadnych wyrażenia długości do przejrzenia. Jeśli nie otrzymasz żadnych trafień punktów końcowych dla konkretnego zamiaru lub jednostki, nie masz wyrażenia długości do przeglądania, które je zawierają. 

## <a name="schedule-review-periodically"></a>Okresowe przeglądy harmonogramu
Przegląd sugerowanych wyrażenia długości nie musi być wykonywany codziennie, ale powinien być częścią regularnej konserwacji LUIS. 

## <a name="delete-review-items-programmatically"></a>Programistyczne usuwanie elementów recenzji
Użyj interfejsu API **[usuwania bez etykiet wyrażenia długości](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** . Przed usunięciem wykonaj kopię zapasową tych wyrażenia długości, **[eksportując pliki dziennika](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przeglądać](luis-how-to-review-endpoint-utterances.md) punkt końcowy wyrażenia długości
