---
title: Dostęp warunkowy — dostęp do blokowania — usługa Azure Active Directory
description: Tworzenie niestandardowych zasad dostępu warunkowego w celu
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2834fd3d4901b6394eabe000f9efc572c2efd497
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755082"
---
# <a name="conditional-access-block-access"></a>Dostęp warunkowy: Blokowanie dostępu

W przypadku organizacji z podejściem do migracji w chmurze konserwatywnej blokowanie wszystkich zasad jest opcją, która może być używana. 

> [!CAUTION]
> Błędna konfiguracja zasad bloku może prowadzić do zablokowania organizacji w witrynie Azure portal.

Zasady takie jak te mogą mieć niezamierzone skutki uboczne. Prawidłowe testowanie i walidacja są niezbędne przed włączeniem. Podczas wprowadzania zmian administratorzy powinni korzystać z narzędzi, takich jak [tryb tylko do raportu dostępu warunkowego](concept-conditional-access-report-only.md) i narzędzie Co jeśli w [dostępie warunkowym.](what-if-tool.md)

## <a name="user-exclusions"></a>Wykluczenia użytkowników

Zasady dostępu warunkowego są zaawansowanymi narzędziami, zalecamy wyłączenie następujących kont z zasad:

* **Dostęp awaryjny** lub **break-glass** kont, aby zapobiec blokady konta całej dzierżawcy. W mało prawdopodobnym scenariuszu wszyscy administratorzy są zablokowane z dzierżawy, konto administracyjne dostępu awaryjnego może służyć do logowania się do dzierżawy podjąć kroki w celu odzyskania dostępu.
   * Więcej informacji można znaleźć w artykule [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Konta usług** i **podmioty usługi,** takie jak konto usługi Azure AD Connect Sync. Konta usług są kontami nieinterakcyjnymi, które nie są powiązane z żadnym konkretnym użytkownikiem. Są one zwykle używane przez usługi zaplecza umożliwiające programowy dostęp do aplikacji, ale są również używane do logowania się do systemów do celów administracyjnych. Konta usług, takie jak te, powinny być wykluczone, ponieważ nie można ukończyć programowo. Wywołania przez podmioty usługi nie są blokowane przez dostęp warunkowy.
   * Jeśli organizacja ma te konta w użyciu w skryptach lub kod, należy rozważyć zastąpienie ich [tożsamościami zarządzanymi](../managed-identities-azure-resources/overview.md). Jako tymczasowe obejście można wykluczyć te określone konta z zasad linii bazowej.

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Poniższe kroki pomogą utworzyć zasady dostępu warunkowego, aby zablokować dostęp do wszystkich aplikacji z wyjątkiem [usługi Office 365,](concept-conditional-access-cloud-apps.md#office-365-preview) jeśli użytkownicy nie są w zaufanej sieci. Te zasady są wprowadzane do trybu tylko do [raportu,](howto-conditional-access-report-only.md) aby rozpocząć, dzięki czemu administratorzy mogą określić wpływ, jaki będą miały na istniejących użytkowników. Gdy administratorzy mają komfort, że zasady mają zastosowanie zgodnie z ich zamiarem, mogą przełączyć je **na Włączone**.

Pierwsza zasada blokuje dostęp do wszystkich aplikacji z wyjątkiem aplikacji usługi Office 365, jeśli nie w zaufanej lokalizacji.

1. Zaloguj się do **witryny Azure portal** jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **usługi Azure Active Directory** > **Security** > **Conditional Access**.
1. Wybierz **pozycję Nowa zasada**.
1. Nadaj polityce nazwę. Zaleca się, aby organizacje tworzyły znaczący standard nazw swoich zasad.
1. W obszarze **Przypisania** wybierz pozycję **Użytkownicy i grupy**.
   1. W **obszarze Uwzględnij**wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz**wybierz **pozycję Użytkownicy i grupy** i wybierz dostęp awaryjny w organizacji lub konta typu break-glass. 
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **Aplikacje lub akcje w chmurze**wybierz następujące opcje:
   1. W **obszarze Uwzględnij**wybierz pozycję **Wszystkie aplikacje w chmurze**.
   1. W obszarze **Wyklucz**wybierz **pozycję Office 365 (wersja zapoznawcza)** wybierz pozycję **Wybierz**, a następnie wybierz pozycję **Gotowe**.
1. W **warunkach:**
   1. W **warunkach** > **Lokalizacja**.
      1. Ustaw **pozycję Konfiguruj** na **Tak**
      1. W obszarze **Uwzględnij**wybierz pozycję **Dowolna lokalizacja**.
      1. W obszarze **Wyklucz**wybierz **pozycję Wszystkie zaufane lokalizacje**.
      1. Wybierz pozycję **Done** (Gotowe).
   1. W obszarze **Aplikacje klienckie (wersja zapoznawcza)** ustaw **pozycję Konfiguruj** na **Tak**i wybierz pozycję **Gotowe**, a następnie **gotowe**.
1. W obszarze **Formanty** > dostępu**Przyznaj**, wybierz pozycję **Blokuj dostęp**, a następnie wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia i ustaw **włącz zasadę** **na Tylko raport**.
1. Wybierz **pozycję Utwórz,** aby utworzyć, aby włączyć zasady.

Poniżej tworzona jest druga zasada, która wymaga uwierzytelniania wieloskładnikowego lub zgodnego urządzenia dla użytkowników usługi Office 365.

1. Wybierz **pozycję Nowa zasada**.
1. Nadaj polityce nazwę. Zaleca się, aby organizacje tworzyły znaczący standard nazw swoich zasad.
1. W obszarze **Przypisania** wybierz pozycję **Użytkownicy i grupy**.
   1. W **obszarze Uwzględnij**wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz**wybierz **pozycję Użytkownicy i grupy** i wybierz dostęp awaryjny w organizacji lub konta typu break-glass. 
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **Aplikacje lub akcje w** > chmurze**Uwzględnij**wybierz pozycję **Wybierz aplikacje**, wybierz pozycję **Office 365 (wersja zapoznawcza)** i wybierz **pozycję Zaznacz**, a następnie **gotowe**.
1. W obszarze **Kontrola** > dostępu**Przyznanie**wybierz pozycję **Przyznaj dostęp**.
   1. Wybierz **opcję Wymagaj uwierzytelniania wieloskładnikowego** i **Wymagaj, aby urządzenie było oznaczone jako zgodne,** wybierz wybierz opcję **Wybierz**.
   1. Upewnij **się, że wybrano opcję Wymagaj wszystkich wybranych formantów.**
   1. Wybierz przycisk **Wybierz**.
1. Potwierdź ustawienia i ustaw **włącz zasadę** **na Tylko raport**.
1. Wybierz **pozycję Utwórz,** aby utworzyć, aby włączyć zasady.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Określanie wpływu przy użyciu trybu tylko dla dostępu warunkowego](howto-conditional-access-report-only.md)

[Symulowanie zachowania logowania za pomocą narzędzia Co jeśli dostęp warunkowy](troubleshoot-conditional-access-what-if.md)
