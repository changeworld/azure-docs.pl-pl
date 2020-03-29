---
title: Dostęp warunkowy — blokowanie uwierzytelniania starszego — usługa Azure Active Directory
description: Tworzenie niestandardowych zasad dostępu warunkowego w celu blokowania starszych protokołów uwierzytelniania
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a868c8199ac34a498a280e2522d6b1e4c7ec370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295211"
---
# <a name="conditional-access-block-legacy-authentication"></a>Dostęp warunkowy: blokowanie uwierzytelniania starszego

Ze względu na zwiększone ryzyko związane ze starszymi protokołami uwierzytelniania firma Microsoft zaleca organizacjom blokowanie żądań uwierzytelniania przy użyciu tych protokołów i wymaganie nowoczesnego uwierzytelniania.

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Poniższe kroki pomogą utworzyć zasady dostępu warunkowego do blokowania starszych żądań uwierzytelniania. Ta zasada jest wprowadzana w [trybie tylko](howto-conditional-access-report-only.md) do raportu, aby rozpocząć, aby administratorzy mogli określić wpływ, jaki będą mieli na istniejących użytkowników. Gdy administratorzy są wygodne, że zasady stosuje się zgodnie z ich zamiarem, mogą przełączyć **się na włącz** lub etap wdrożenia, dodając określone grupy i wykluczając inne.

1. Zaloguj się do **witryny Azure portal** jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **usługi Azure Active Directory** > **Security** > **Conditional Access**.
1. Wybierz **pozycję Nowa zasada**.
1. Nadaj polityce nazwę. Zaleca się, aby organizacje tworzyły znaczący standard nazw swoich zasad.
1. W obszarze **Przydziały**wybierz **pozycję Użytkownicy i grupy**
   1. W **obszarze Uwzględnij**wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz**wybierz **pozycję Użytkownicy i grupy** i wybierz wszystkie konta, które muszą zachować możliwość korzystania ze starszego uwierzytelniania. Wyklucz co najmniej jedno konto, aby zapobiec zablokowaniu siebie. Jeśli nie wykluczysz żadnego konta, nie będziesz mógł utworzyć tej zasady.
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **Aplikacje lub akcje w chmurze**wybierz pozycję **Wszystkie aplikacje w chmurze**.
   1. Wybierz pozycję **Done** (Gotowe).
1. W **obszarze Warunki** > **aplikacje klienckie (wersja zapoznawcza)** ustaw **configure** na **Tak**.
   1. Zaznacz tylko pola **Aplikacje mobilne i klienci komputerowi** > **Inni klienci**.
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **Formanty** > dostępu**Przyznanie**wybierz pozycję **Zablokuj dostęp**.
   1. Wybierz przycisk **Wybierz**.
1. Potwierdź ustawienia i ustaw **włącz zasadę** **na Tylko raport**.
1. Wybierz **pozycję Utwórz,** aby utworzyć, aby włączyć zasady.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Określanie wpływu przy użyciu trybu tylko dla dostępu warunkowego](howto-conditional-access-report-only.md)

[Symulowanie zachowania logowania za pomocą narzędzia Co jeśli dostęp warunkowy](troubleshoot-conditional-access-what-if.md)
