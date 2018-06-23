---
title: Omówienie usługi Azure zawartości moderatora | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać moderatora zawartości do śledzenia, Flaga, oceny i filtrować nieodpowiednie zawartość zawartość wygenerowaną przez użytkowników.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/15/2017
ms.author: sajagtap
ms.openlocfilehash: cd9e2e9c10e9dc5ba118c8319f76174bf6f0da9f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349380"
---
# <a name="what-is-content-moderator"></a>Co to jest moderatora zawartości?

Łagodzenie zawartości to proces monitorowania możliwe zawartości obraźliwe, niepożądanych i ryzykowne. Moderowane zawartości może być obrazy, tekst i wideo.

## <a name="where-it-is-used"></a>Gdzie jest używane

Na poniższej liście przedstawiono kilka przykładowych scenariuszy, gdzie są używane moderatora zawartości:

- Zawartość wygenerowany online rynków Moderowanie katalogów produktów i użytkownika
- Artefakty gier i pokoje rozmów wygenerował firm gier Moderowanie użytkownika
- Społecznościowych platformy obsługi komunikatów Moderowanie obrazy, tekst i filmy dodane przez użytkowników
- Implementowanie scentralizowane łagodzenia zawartości ze względu na zawartość firm nośnika Enterprise
- Filtrowanie zawartości zły i obraźliwe dla uczniów lub studentów i instytucjom dostawców rozwiązań K-12 education

## <a name="what-it-includes"></a>Co zawiera

Zawartości moderatora składa się z kilku usług sieci web interfejsów API i wbudowane człowieka w — pętlę Przejrzyj narzędzie, które ułatwia umiarkowane obrazy, tekst i wideo.

![Diagram blokowy moderatora zawartości](images/content-moderator-block-diagram.png)

## <a name="apis"></a>Interfejsy API

Moderatora zawartości zawiera następujące interfejsy API:
  - [**Tekst łagodzenia interfejsu API**](text-moderation-api.md): Skanuj tekst możliwości niestosownych wyrażeń, jawne, sugerujących obraźliwe i dane osobowe lub informacje za pomocą tego interfejsu API.
  - [**Lista termin niestandardowego interfejsu API**](try-terms-list-api.md): Użyj tego interfejsu API do dopasowywania listy warunków oprócz wbudowanych warunków. Użyj ich, aby zablokować lub zezwolić zawartości zgodnie z zasadami dotyczącymi zawartości.  
  - [**Obraz łagodzenia interfejsu API**](image-moderation-api.md): skanowanie obrazów na zawartość dla dorosłych i luksusowych, wykrywania tekstu w obrazach, z możliwością OCR (Rozpoznawania) i wykrywania kroje za pomocą tego interfejsu API.
  - [**Listy obrazów niestandardowego interfejsu API**](try-image-list-api.md): Użyj tego interfejsu API do dopasowywania list niestandardowych obrazów, wstępnie zidentyfikowane zawartość, która nie należy do klasyfikowania ponownie.
  - [**Łagodzenie wideo interfejsu API**](video-moderation-api.md): użycie tego interfejsu API w celu skanowania plików wideo na potencjalne zawartość dla dorosłych i luksusowych.
  - [**Przejrzyj interfejsów API**](try-review-api-job.md): Użyj [zadania](try-review-api-job.md), [przeglądami](try-review-api-review.md), i [przepływu pracy](try-review-api-workflow.md) na tworzenie i automatyzować przepływy pracy ludzkich w pętli w ramach operacji narzędzie przeglądu.

## <a name="human-review-tool"></a>Narzędzie do przeglądu przez ludzi

Twoja subskrypcja moderatora zawartości obejmuje wbudowane [narzędzie przeglądu człowieka](Review-Tool-User-Guide/human-in-the-loop.md). Umożliwia utworzenie przeglądy tekst, obrazy i wideo dla Twojego człowieka moderatorów podjęcie decyzji końcowego wymienione wcześniej API przeglądu.

![Przegląd zawartości wideo moderatora narzędzia](images/video-review-default-view.png)

## <a name="next-steps"></a>Kolejne kroki

Użyj [szybkiego startu](quick-start.md) wprowadzenie moderatora zawartości.
