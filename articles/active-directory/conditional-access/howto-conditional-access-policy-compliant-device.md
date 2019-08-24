---
title: Dostęp warunkowy — Wymagaj zgodnych urządzeń — Azure Active Directory
description: Tworzenie niestandardowych zasad dostępu warunkowego w celu wymagania zgodnych urządzeń
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: da1a5f54e5e989f661770d518a6753b831b59bd4
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990512"
---
# <a name="conditional-access-require-compliant-devices"></a>Dostęp warunkowy: Wymaganie zgodnych urządzeń

Organizacje, które wdrożyły Microsoft Intune mogą korzystać z informacji zwróconych z urządzeń w celu identyfikowania urządzeń spełniających wymagania dotyczące zgodności, takich jak:

* Wymaganie kodu PIN do odblokowania
* Wymaganie szyfrowania urządzenia
* Wymaganie minimalnej lub maksymalnej wersji systemu operacyjnego
* Wymaganie, aby urządzenie nie zostało złamane lub odblokowane

Te informacje o zgodności z zasadami są przekazywane do usługi Azure AD, gdzie dostęp warunkowy może podejmować decyzje o udzieleniu lub zablokowaniu dostępu do zasobów.

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Poniższe kroki pomogą utworzyć zasady dostępu warunkowego, aby wymagać, aby urządzenia uzyskujące dostęp do zasobów były oznaczone jako zgodne z zasadami zgodności usługi Intune w organizacji.

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory** > **dostęp warunkowy**.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania**wybierz pozycję **Użytkownicy i grupy**
   1. Wobszarze Dołączanie wybierz pozycję **Wszyscy użytkownicy**.
   1. Wobszarze Wyklucz wybierz pozycję **Użytkownicy i grupy** , a następnie wybierz opcję dostęp awaryjny lub konta w ramach swojej organizacji. 
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **aplikacje w chmurze lub akcje** > **Dołącz**wybierz pozycję **wszystkie aplikacje w chmurze**.
   1. Jeśli musisz wykluczyć określone aplikacje z zasad, możesz je wybrać z karty Wyklucz w obszarze **Wybierz wykluczone aplikacje w chmurze** i wybierz **pozycję Wybierz**.
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **Kontrola** > dostępu wybierz pozycję **Wymagaj, aby urządzenie było oznaczone jako zgodne**.
   1. Wybierz **wybierz**.
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć zasady.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)

[Zasady zgodności urządzeń współpracują z usługą Azure AD](https://docs.microsoft.com/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
