---
title: Przeglądanie użytkownika wypowiedź — LUIS
titleSuffix: Azure Cognitive Services
description: Aktywna nauka, swoje wypowiedzi punkt końcowy Przegląd poprawne intencji i jednostki. Usługa LUIS wybiera wypowiedzi punktu końcowego, który jest nieznany.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710540"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Pojęcia dotyczące włączania aktywne uczenie, przeglądając wypowiedzi punktu końcowego
Aktywna nauka jest jednym z trzech strategii w celu zwiększenia dokładności prognozy i najłatwiejsza do wdrożenia. Aktywna nauka, swoje wypowiedzi punkt końcowy Przegląd poprawne intencji i jednostki. Usługa LUIS wybiera wypowiedzi punktu końcowego, który jest nieznany.

## <a name="what-is-active-learning"></a>Co to jest aktywna nauka
Aktywna nauka jest procesem dwuetapowym. Po pierwsze LUIS wybiera wypowiedzi otrzymywanych w punkcie końcowym aplikacji, które wymagają weryfikacji. Drugi etap odbywa się przez właściciela aplikacji lub Współautor, który można zweryfikować wybranego wypowiedzi dla [Przejrzyj](luis-how-to-review-endpoint-utterances.md), w tym poprawny intencji i wszystkie jednostki w ramach intencji. Po zapoznaniu się z wypowiedzi, szkolenie i ponownie Opublikuj aplikację.

## <a name="which-utterances-are-on-the-review-list"></a>Które wypowiedzi znajdują się na liście przeglądu
LUIS wypowiedzi są dodawane do listy przeglądu, jeśli u góry, wyzwalania intencji znajduje się niskim wynikiem lub zbyt Zamknij oceny najważniejszych dwóch intencji.

## <a name="single-pool-for-utterances-per-app"></a>Jednej puli dla wypowiedzi na aplikację
**Przejrzyj wypowiedzi punktu końcowego** listy nie zmienia się zależnie od wersji. Jest tylko jedna pula wypowiedzi do przejrzenia, niezależnie od aktywnie edytowanej wersji wypowiedzi ani od wersji aplikacji opublikowanej w punkcie końcowym.

## <a name="where-are-the-utterances-from"></a>Gdzie są wypowiedzi z
Punkt końcowy wypowiedzi są pobierane z zapytań przez użytkownika końcowego na punkt końcowy HTTP w aplikacji. Jeśli Twoja aplikacja nie został opublikowany lub nie otrzymał trafień jeszcze, nie masz wypowiedzi, aby przejrzeć. Odebranie nie trafienia punktu końcowego dla określonych przeznaczenie lub jednostki nie masz wypowiedzi do przeglądania, zawierające je.

## <a name="schedule-review-periodically"></a>Okresowo przeglądu harmonogramu
Przeglądania sugerowanych wypowiedzi nie musi odbywać się codziennie, ale powinna być częścią Twojego regularnej konserwacji usługi LUIS.

## <a name="delete-review-items-programmatically"></a>Usuń elementy przeglądu programowe
Użyj interfejsu API **[usuwania bez etykiet wyrażenia długości](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** . Utwórz kopię zapasową tych wypowiedzi przed usunięciem przez  **[eksportowania plików dziennika](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** .

## <a name="enable-active-learning"></a>Włącz aktywną naukę

Aby włączyć aktywną naukę, należy zarejestrować zapytania użytkownika. Jest to realizowane przez wywołanie [zapytania punktu końcowego](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) z `log=true` ciągu QueryString i wartością.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [Przejrzyj](luis-how-to-review-endpoint-utterances.md) wypowiedzi punktu końcowego
