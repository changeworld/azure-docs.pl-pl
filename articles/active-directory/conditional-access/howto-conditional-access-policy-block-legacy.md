---
title: Dostęp warunkowy — Blokuj starsze uwierzytelnianie — Azure Active Directory
description: Tworzenie niestandardowych zasad dostępu warunkowego w celu blokowania starszych protokołów uwierzytelniania
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0aca5019f4f7fca47195fb8fb821b1af1ae9ec77
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024249"
---
# <a name="conditional-access-block-legacy-authentication"></a>Dostęp warunkowy: Blokuj starsze uwierzytelnianie

Ze względu na zwiększone ryzyko związane ze starszymi protokołami uwierzytelniania firma Microsoft zaleca, aby organizacje blokowały żądania uwierzytelniania przy użyciu tych protokołów i wymagały nowoczesnego uwierzytelniania.

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Poniższe kroki pomogą w tworzeniu zasad dostępu warunkowego w celu blokowania starszych żądań uwierzytelniania.

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory** > **zabezpieczenia** > **dostęp warunkowy**.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania**wybierz pozycję **Użytkownicy i grupy**
   1. W obszarze **dołączanie**wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz**wybierz pozycję **Użytkownicy i grupy** , a następnie wybierz wszystkie konta, które muszą mieć możliwość korzystania z starszego uwierzytelniania. Należy wykluczyć co najmniej jedno konto, aby zapobiec jego zablokowaniu. Jeśli nie wykluczasz żadnego konta, nie będziesz w stanie utworzyć tych zasad.
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **aplikacje lub akcje w chmurze** wybierz pozycję **wszystkie aplikacje w chmurze**.
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **warunki** > **aplikacje klienckie (wersja zapoznawcza)** ustaw opcję **Skonfiguruj** na **wartość tak**.
   1. Sprawdź tylko ramki **aplikacje mobilne i klienci stacjonarni** > **innych klientów**.
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **kontroli dostępu** > **Udziel**wybierz pozycję **Blokuj dostęp**.
   1. Wybierz przycisk **Wybierz**.
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć zasady.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Określanie wpływu przy użyciu trybu tylko Raport z dostępem warunkowym](howto-conditional-access-report-only.md)

[Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)
