---
title: Czym jest usługa Azure Content Moderator?
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak używać usługi Content Moderator do śledzenia, flagowania, oceniania i filtrowania nieodpowiednich treści w zawartości wygenerowanej przez użytkownika.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: overview
ms.date: 10/05/2018
ms.author: sajagtap
ms.openlocfilehash: 5756e8fb451b073c68271359848ab27373ad85ed
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309556"
---
# <a name="what-is-content-moderator"></a>Czym jest pakiet Content Moderator?

Moderowanie zawartości to proces monitorowania tekstów, obrazów lub filmów wideo pod kątem zawartości, która może być obraźliwa, niepożądana lub ryzykowna. Oflagowana zawartość może zostać następnie ukryta lub w inny sposób oznaczona w celu zachowania zgodności z przepisami lub zapewnienia odpowiedniego środowiska dla użytkowników.

## <a name="where-it-is-used"></a>Gdzie jest używany

Na poniższej liście przedstawiono kilka przykładowych scenariuszy użycia usługi Content Moderator:

- Platformy handlowe online moderujące katalogi produktów i zawartość wygenerowaną przez użytkowników
- Firmy produkujące gry moderujące artefakty w grze wygenerowane przez użytkownika i pokoje rozmów
- Platformy społecznościowe moderujące obrazy, teksty i filmy wideo dodawane przez użytkowników
- Firmy oferujące multimedia dla przedsiębiorstw, implementujące scentralizowane moderowanie zawartości
- Dostawcy rozwiązań dla szkół podstawowych i średnich filtrujący niewłaściwą i obraźliwą zawartość dla uczniów i nauczycieli

## <a name="what-it-includes"></a>Co zawiera

Pakiet Content Moderator składa się z kilku interfejsów API usług internetowych i wbudowanego narzędzia do przeprowadzania przeglądu obsługiwanego przez człowieka, które pomaga moderować obrazy, tekst i filmy wideo.

![Diagram blokowy pakietu Content Moderator](images/content-moderator-block-diagram.png)

### <a name="apis"></a>Interfejsy API

Usługa Content Moderator zawiera następujące interfejsy API:
  - [**Interfejs API moderowania tekstu**](text-moderation-api.md): ten interfejs API służy do skanowania tekstu pod kątem możliwych wulgaryzmów — jawnych, sugestywnych, obraźliwych — oraz danych osobowych.
  - [**Interfejs API listy terminów niestandardowych**](try-terms-list-api.md): ten interfejs API służy do wyszukiwania dopasowań względem niestandardowych list terminów, poza terminami wbudowanymi. Użyj tych list, aby blokować zawartość lub zezwalać na nią zgodnie z określonymi zasadami dotyczącymi zawartości.  
  - [**Interfejs API moderowania obrazów**](image-moderation-api.md): ten interfejs API służy do skanowania obrazów pod kątem zawartości dla dorosłych i zawartości erotycznej, wykrywania tekstu w obrazach za pomocą funkcji optycznego rozpoznawania znaków (OCR) i wykrywania twarzy.
  - [**Interfejs API listy obrazów niestandardowych**](try-image-list-api.md): ten interfejs API służy do przeprowadzania dopasowań względem niestandardowych list obrazów, czyli wstępnie zdefiniowanej zawartości, której nie trzeba klasyfikować ponownie.
  - [**Interfejs API moderowania wideo**](video-moderation-api.md): ten interfejs API służy do skanowania wideo pod kątem potencjalnej zawartości dla dorosłych i zawartości erotycznej.
  - [**Interfejsy API przeglądu**](try-review-api-job.md): przy użyciu operacji [Zadania](try-review-api-job.md), [Przeglądy](try-review-api-review.md) i [Przepływ pracy](try-review-api-workflow.md) utwórz i zautomatyzuj przepływy pracy obsługiwane przez człowieka w ramach narzędzia do przeglądu.

### <a name="human-review-tool"></a>Narzędzie do przeglądu przez ludzi

Subskrypcja pakietu Content Moderator obejmuje wbudowane [narzędzie do przeglądu przez ludzi](Review-Tool-User-Guide/human-in-the-loop.md). Użyj wspomnianego wcześniej interfejsu API przeglądu, aby tworzyć przeglądy tekstu, obrazów i filmów wideo względem których ostateczną decyzję mają podjąć moderatorzy ludzcy.

![Narzędzie służące do przeprowadzania przeglądu wideo usługi Content Moderator](images/video-review-default-view.png)

## <a name="next-steps"></a>Następne kroki

Zacznij pracę z pakietem Content Moderator, korzystając z [przewodnika Szybki start](quick-start.md).
