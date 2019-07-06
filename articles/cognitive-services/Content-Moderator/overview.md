---
title: Czym jest usługa Azure Content Moderator?
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak używać usługi Content Moderator do śledzenia, flagowania, oceniania i filtrowania nieodpowiednich treści w zawartości wygenerowanej przez użytkownika.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: d814a943bc8dc789abe84b33583714beb998c0ef
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607012"
---
# <a name="what-is-azure-content-moderator"></a>Czym jest usługa Azure Content Moderator?

Interfejs API usługi Azure Content Moderator to usługa poznawcza, która sprawdza tekst, obrazy i zawartość wideo pod kątem treści potencjalnie obraźliwych, ryzykownych lub w jakikolwiek inny sposób niepożądanych. W przypadku znalezienia takich treści usługa stosuje odpowiednie etykiety (flagi) do zawartości. Aplikacja może następnie obsłużyć oflagowaną zawartość w celu zachowania zgodności z przepisami lub zapewnienia odpowiedniego środowiska dla użytkowników. Zobacz [interfejsy API moderowania](#moderation-apis) wskazują sekcji, aby dowiedzieć się więcej o flagi różną zawartość.

## <a name="where-it-is-used"></a>Gdzie jest używany

Poniżej przedstawiono kilka scenariuszy, w których deweloper lub zespół ds. oprogramowania będą używać pakietu Content Moderator:

- Rynki online średni wykazami różnych produktów i innych o zawartość wygenerowaną przez użytkowników.
- Prowadzenie rozgrywki firmach, w których średni wygenerowaną przez użytkowników artefaktów gier i pokoje rozmów.
- Społecznościowych platform obsługi komunikatów, które Moderowanie obrazów, tekstu i filmów wideo dodawanych przez ich użytkowników.
- Firmom nośnika, które implementują scentralizowane Moderowanie ich zawartości.
- Filtrowanie zawartości, która jest nieodpowiedni dla uczniów i nauczycieli dostawców rozwiązań K-12 instytucji edukacyjnych.

> [!NOTE]
> Za pomocą pakietu Content Moderator nie można wykryć niedozwolony podrzędnych wykorzystywania obrazów. Jednak kwalifikowaną organizacje mogą używać [usługi w chmurze PhotoDNA](https://www.microsoft.com/photodna "usługi firmy Microsoft w chmurze PhotoDNA") do ekranu dla tego typu zawartości.

## <a name="what-it-includes"></a>Co zawiera

Usługa Content Moderator składa się z kilku interfejsów API usług internetowych dostępnych za pośrednictwem wywołań REST i zestawu .NET SDK. Obejmuje również narzędzie przeglądu przez ludzi, dzięki któremu recenzenci mogą pomóc usłudze i ulepszyć lub dostosować jej funkcję moderowania.

## <a name="moderation-apis"></a>Interfejsy API moderowania

Usługa Content Moderator obejmuje interfejsy API moderowania, który Sprawdź zawartość materiału, który jest potencjalnie niewłaściwe lub niepożądanych.

![diagram blokowy dla interfejsy API moderowania pakietu Content Moderator](images/content-moderator-mod-api.png)

W poniższej tabeli opisano różne rodzaje interfejsy API moderowania.

| Grupa interfejsów API | Opis |
| ------ | ----------- |
|[**Moderowanie tekstu**](text-moderation-api.md)| Skanuje tekst obraźliwą zawartość, zawartość przekleństwa jawne lub sugerujących, wulgaryzmów i danych osobowych.|
|[**Niestandardowe listy terminów**](try-terms-list-api.md)| Skanuje tekst względem niestandardowych list terminów, poza terminami wbudowanymi. Użyj list niestandardowych, aby blokować zawartość lub zezwalać na nią zgodnie z własnymi zasadami dotyczącymi zawartości.|  
|[**Moderowanie obrazów**](image-moderation-api.md)| Skanuje obrazy pod kątem zawartości dla dorosłych i zawartości erotycznej, wykrywa tekst w obrazach za pomocą funkcji optycznego rozpoznawania znaków (OCR) i wykrywa twarze.|
|[**Niestandardowe listy obrazów**](try-image-list-api.md)| Skanuje obrazy względem niestandardowej listy obrazów. Użyj niestandardowych list obrazów, aby filtrować wystąpienia często powtarzającej się zawartości, której nie chcesz klasyfikować ponownie.|
|[**Moderowanie filmów wideo**](video-moderation-api.md)| Skanuje filmy wideo pod kątem potencjalnej zawartości dla dorosłych lub zawartości erotycznej i zwraca znaczniki czasu dla wypowiadanej zawartości.|

## <a name="review-apis"></a>Przegląd interfejsów API

Interfejsy API przeglądu pozwalają na integrację z potokiem Moderowanie z recenzentami ludzi. Użyj [zadania](review-api.md#jobs), [przeglądy](review-api.md#reviews), i [przepływu pracy](review-api.md#workflows) operacje do tworzenia i automatyzacji przepływów pracy człowieka w pętli [narzędzie do przeglądu](#the-review-tool) () poniżej).

> [!NOTE]
> Interfejs API przepływu pracy nie jest jeszcze dostępna w zestawie SDK platformy .NET, ale mogą być używane z punktem końcowym REST.

![diagram blokowy dla pakietu Content Moderator Przegląd interfejsów API](images/content-moderator-rev-api.png)

## <a name="the-review-tool"></a>Narzędzie do przeglądu

Usługa Content Moderator obejmuje również oparta na sieci web [narzędzie do przeglądu](Review-Tool-User-Guide/human-in-the-loop.md), który obsługuje zawartość przeglądy dla moderatorów ludzi do przetworzenia. Dane wejściowe wprowadzane przez ludzi nie uczą usługi, ale połączona praca usługi i zespołów ds. przeglądu przez ludzi pozwala deweloperom uzyskać równowagę między wydajnością i dokładnością. Narzędzie do przeglądu udostępnia przyjazny dla użytkownika frontonu dla różnych zasobów usługi Content Moderator.

![Strona główna narzędzia do przeglądu przez ludzi usługi Content Moderator](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi Content Moderator powinni znać zasady firmy Microsoft dotyczące danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Kolejne kroki

Zacznij korzystać z usługi Content Moderator, postępując zgodnie z instrukcjami w przewodniku [Wypróbowywanie usługi Content Moderator w Internecie](quick-start.md).