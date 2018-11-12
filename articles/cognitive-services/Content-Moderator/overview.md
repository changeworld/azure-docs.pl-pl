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
ms.date: 10/22/2018
ms.author: sajagtap
ms.openlocfilehash: 076948e7434802af7f0ad47f279335009817d40e
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209595"
---
# <a name="what-is-azure-content-moderator"></a>Czym jest usługa Azure Content Moderator?

Interfejs API usługi Azure Content Moderator to usługa poznawcza, która sprawdza tekst, obrazy i zawartość wideo pod kątem treści potencjalnie obraźliwych, ryzykownych lub w jakikolwiek inny sposób niepożądanych. W przypadku znalezienia takich treści usługa stosuje odpowiednie etykiety (flagi) do zawartości. Aplikacja może następnie obsłużyć oflagowaną zawartość w celu zachowania zgodności z przepisami lub zapewnienia odpowiedniego środowiska dla użytkowników. Zobacz sekcję [Interfejsy API pakietu Content Moderator](#content-moderator-apis), aby dowiedzieć się więcej o tym, co oznaczają różne flagi zawartości.

## <a name="where-it-is-used"></a>Gdzie jest używany

Poniżej przedstawiono kilka scenariuszy, w których deweloper lub zespół ds. oprogramowania będą używać pakietu Content Moderator:

- Platformy handlowe online moderujące katalogi produktów i inną zawartość wygenerowaną przez użytkowników
- Firmy produkujące gry moderujące artefakty w grze wygenerowane przez użytkownika i pokoje rozmów
- Platformy społecznościowe moderujące obrazy, teksty i filmy wideo dodawane przez użytkowników
- Firmy oferujące multimedia dla przedsiębiorstw, implementujące scentralizowane moderowanie zawartości
- Dostawcy rozwiązań dla szkół podstawowych i średnich, filtrujący zawartość nieodpowiednią dla uczniów i nauczycieli

## <a name="what-it-includes"></a>Co zawiera

Usługa Content Moderator składa się z kilku interfejsów API usług internetowych dostępnych za pośrednictwem wywołań REST i zestawu .NET SDK. Obejmuje również narzędzie przeglądu przez ludzi, dzięki któremu recenzenci mogą pomóc usłudze i ulepszyć lub dostosować jej funkcję moderowania.

![Diagram blokowy usługi Content Moderator przedstawiający interfejsy API moderowania, interfejsy API przeglądu oraz narzędzie przeglądu przez ludzi](images/content-moderator-block-diagram.png)

### <a name="content-moderator-apis"></a>Interfejsy API usługi Content Moderator

Usługa Content Moderator zawiera interfejsy API dla następujących scenariuszy.

| Akcja | Opis |
| ------ | ----------- |
|[**Moderowanie tekstu**](text-moderation-api.md)| Skanuje tekst pod kątem obraźliwej zawartości, zawartości o charakterze seksualnym, wulgaryzmów i danych osobowych.|
|[**Niestandardowe listy terminów**](try-terms-list-api.md)| Skanuje tekst względem niestandardowych list terminów, poza terminami wbudowanymi. Użyj list niestandardowych, aby blokować zawartość lub zezwalać na nią zgodnie z własnymi zasadami dotyczącymi zawartości.|  
|[**Moderowanie obrazów**](image-moderation-api.md)| Skanuje obrazy pod kątem zawartości dla dorosłych i zawartości erotycznej, wykrywa tekst w obrazach za pomocą funkcji optycznego rozpoznawania znaków (OCR) i wykrywa twarze.|
|[**Niestandardowe listy obrazów**](try-image-list-api.md)| Skanuje obrazy względem niestandardowej listy obrazów. Użyj niestandardowych list obrazów, aby filtrować wystąpienia często powtarzającej się zawartości, której nie chcesz klasyfikować ponownie.|
|[**Moderowanie filmów wideo**](video-moderation-api.md)| Skanuje filmy wideo pod kątem potencjalnej zawartości dla dorosłych lub zawartości erotycznej i zwraca znaczniki czasu dla wypowiadanej zawartości.|
|[**Przegląd**](try-review-api-job.md)| Przy użyciu operacji [Zadania](try-review-api-job.md), [Przeglądy](try-review-api-review.md) i [Przepływ pracy](try-review-api-workflow.md) utwórz i zautomatyzuj przepływy pracy obsługiwane przez człowieka za pomocą narzędzia do przeglądu przez ludzi. Interfejs API przepływu pracy nie jest jeszcze dostępny za pośrednictwem zestawu .NET SDK.|

### <a name="human-review-tool"></a>Narzędzie do przeglądu przez ludzi

Usługa Content Moderator obejmuje również internetowe [narzędzie do przeglądu przez ludzi](Review-Tool-User-Guide/human-in-the-loop.md). 

![Strona główna narzędzia do przeglądu przez ludzi usługi Content Moderator](images/homepage.PNG)

Interfejsów API przeglądu możesz użyć do skonfigurowania zespołowych przeglądów tekstu, obrazów i zawartości wideo zgodnie z określonymi przez Ciebie filtrami. Następnie moderatorzy-ludzie mogą podejmować ostateczne decyzje dotyczące moderowania. Dane wejściowe wprowadzane przez ludzi nie uczą usługi, ale połączona praca usługi i zespołów ds. przeglądu przez ludzi pozwala deweloperom uzyskać równowagę między wydajnością i dokładnością.

## <a name="next-steps"></a>Następne kroki

Skorzystaj z [przewodnika Szybki start](quick-start.md), aby rozpocząć pracę z usługą Content Moderator.
