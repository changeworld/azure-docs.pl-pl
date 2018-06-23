---
title: Często zadawane pytania dotyczące usługi Azure moderatora zawartości | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące moderatora zawartości.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 11/21/2016
ms.author: sajagtap
ms.openlocfilehash: 7bf6c67bd0a83d9455d14253f4f4b2becafd29ba
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347013"
---
# <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

#### <a name="what-does-my-content-moderator-subscription-include"></a>Jaki jest Mój zawartości moderatora obejmują subskrypcji?
Twoja subskrypcja moderatora zawartości obejmuje dostęp do przeglądu narzędzia i interfejsy API. Można zdecydować, czy chcesz użyć jednej lub drugiej lub obu w zależności od potrzeb biznesowych.

#### <a name="what-are-the-limitsrestrictions-of-the-content-that-can-be-moderated-by-using-the-api"></a>Jakie są limity/ograniczenia zawartości moderowanej przy użyciu interfejsu API?
Korzystając z interfejsu API, obrazy muszą mieć co najmniej 128 pikseli i maksymalny rozmiar 4 MB. Tekst może mieć co najwyżej 1024 znaków. Nie ma żadnego limitu czasu wideo.

#### <a name="what-happens-if-the-content-passed-to-the-text-api-or-the-image-api-exceeds-the-size-limits"></a>Co się stanie, jeśli limity rozmiaru zawartości przekazanej do interfejsu API rozpoznawania tekstu i interfejsu API rozpoznawania obrazów zostaną przekroczone?
Tekst interfejsu API zwraca kod błędu, który informuje o tym, że tekst jest dłuższy, niż dozwolone. Obraz interfejsu API również zwraca kod błędu, który informuje o tym, że obraz nie spełnia wymagań w zakresie wielkości.

#### <a name="do-you-keep-the-images-text-or-videos-that-are-submitted-for-moderation"></a>Zachować obrazy, tekst lub pliki wideo, które są przesyłane do łagodzenia?
Zawartość jest własne i nie mogą być przechowywane przez firmę Microsoft bez zgody użytkownika. Firma Microsoft korzysta z branży środków bezpieczeństwa w celu ochrony zawartości.

#### <a name="can-i-use-content-moderator-to-screen-for-illegal-child-exploitation-images"></a>Czy można używać moderatora zawartości ekranu do podrzędnych niedozwolony wykorzystania obrazów
Nie. Jednak kwalifikowaną organizacje mogą używać [usługi w chmurze PhotoDNA](https://www.microsoft.com/photodna "usługi w chmurze PhotoDNA Microsoft") do ekranu dla tego typu zawartości.

#### <a name="up-to-how-many-review-teams-can-a-user-join-can-the-user-switch-between-teams"></a>Do ilu Przejrzyj zespoły użytkownika przyłączyć? Użytkownik może przełączać między zespołami?
Użytkownik może dołączać jeden zespół naraz.

#### <a name="what-kind-of-team-member-roles-are-supported-by-the-review-tool-how-are-they-different"></a>Jakiego rodzaju role są obsługiwane przez narzędzie przeglądu członek zespołu? Czym się od siebie różnią?
Studio obecnie umożliwia przypisywanie ról administratora i osoba dokonująca przeglądu. Administratorzy mogą zapraszać innych użytkowników i mają dostęp do ustawień konfiguracji, gdy osoby dokonujące przeglądu można przejrzeć wyniki łagodzenia i znaczników lub tylko oznaczenie zawartości.

#### <a name="what-is-a-tag-does-the-review-tool-support-custom-tags"></a>Co to jest tag? Narzędzie przeglądu obsługuje znaczniki niestandardowe?
Tag jest lub dwuliterowych krótki kod, która określa flagę łagodzenia, takich jak "a" dla dorosłych, "r" dla luksusowych i tak dalej. Administratorzy mogą definiować nowych tagów działalności biznesowej, zgodnie z potrzebami.

#### <a name="how-many-team-members-can-i-have-in-my-review-team"></a>Ile członków zespołu może mieć należących do mojego zespołu przeglądu?
Zespół może składać się z maksymalnie pięciu członków, w tym administratora.
