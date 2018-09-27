---
title: Często zadawane pytania — pakietu Content Moderator
titlesuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi Content Moderator.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: faq
ms.date: 11/21/2016
ms.author: sajagtap
ms.openlocfilehash: ce61a89069caf90695a2cb6c54f61de8fa8cb112
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226938"
---
# <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

#### <a name="what-does-my-content-moderator-subscription-include"></a>Co to jest Moja subskrypcja obejmuje pakietu Content Moderator?
Subskrypcja usługi Content Moderator obejmuje dostęp do narzędzia do przeglądu i interfejsów API. Aby zdecydować, czy chcesz używać jednego lub drugiego lub obu, w zależności od potrzeb biznesowych.

#### <a name="what-are-the-limitsrestrictions-of-the-content-that-can-be-moderated-by-using-the-api"></a>Jakie są limity/ograniczenia zawartości moderowanej przy użyciu interfejsu API?
Podczas korzystania z interfejsu API obrazy muszą mieć minimalnie 128 pikseli i maksymalny rozmiar 4 MB. Tekst może mieć maksymalnie 1024 znaków. Nie ma żadnego limitu na czas trwania wideo.

#### <a name="what-happens-if-the-content-passed-to-the-text-api-or-the-image-api-exceeds-the-size-limits"></a>Co się stanie, jeśli limity rozmiaru zawartości przekazanej do interfejsu API rozpoznawania tekstu i interfejsu API rozpoznawania obrazów zostaną przekroczone?
Interfejs API tekstu zwraca kod błędu informujący o tym, że długość tekstu jest większa niż dozwolona. Interfejs API obrazów również zwraca kod błędu informujący o tym, że obraz nie spełnia wymagań dotyczących rozmiaru.

#### <a name="do-you-keep-the-images-text-or-videos-that-are-submitted-for-moderation"></a>Zachować obrazów, tekstu lub filmy wideo, które są przesyłane do moderacji?
Zawartość jest własny i nie mogą być przechowywane przez firmę Microsoft bez Twojej zgody. Firma Microsoft używa wiodące w branży środki bezpieczeństwa w celu ochrony Twojej zawartości.

#### <a name="can-i-use-content-moderator-to-screen-for-illegal-child-exploitation-images"></a>Czy można użyć usługi Content Moderator do ekranu niedozwolony podrzędnych wykorzystywania obrazów?
Nie. Jednak kwalifikowaną organizacje mogą używać [usługi w chmurze PhotoDNA](https://www.microsoft.com/photodna "usługi firmy Microsoft w chmurze PhotoDNA") do ekranu dla tego typu zawartości.

#### <a name="up-to-how-many-review-teams-can-a-user-join-can-the-user-switch-between-teams"></a>Maksymalnie ile Przejrzyj zespoły użytkownika dołączyć? Czy użytkownik może przełączać między zespołami?
Użytkownik może przyłączyć jeden zespół w danym momencie.

#### <a name="what-kind-of-team-member-roles-are-supported-by-the-review-tool-how-are-they-different"></a>Jakiego rodzaju członka zespołu, które role są obsługiwane przez narzędzie do przeglądu? Czym się od siebie różnią?
Studio umożliwia obecnie korzystanie z przypisywanie ról administratora i osoba dokonująca przeglądu. Administratorzy mogą zapraszać innych użytkowników i mają dostęp do ustawień konfiguracji, a tylko przejrzeć wyniki moderacji oraz tag lub usunąć oznakowanie zawartości recenzentów.

#### <a name="what-is-a-tag-does-the-review-tool-support-custom-tags"></a>Co to jest znacznik? Narzędzie do przeglądu obsługuje znaczniki niestandardowe?
To jest znacznik lub dwuliterowych krótki kod, który oznacza flagę Moderowanie, takie jak "a" do treści dla dorosłych, "r" erotycznej i tak dalej. Administratorzy mogą definiować nowych znaczników działalności biznesowej, zgodnie z potrzebami.

#### <a name="how-many-team-members-can-i-have-in-my-review-team"></a>Z ilu członków może mieć w mojego zespołu?
Zespół może składać się z maksymalnie pięciu członków, w tym administratora.
