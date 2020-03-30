---
title: Dostęp warunkowy — dostęp warunkowy oparty na ryzyku — usługa Azure Active Directory
description: Tworzenie zasad dostępu warunkowego w celu włączenia ulepszeń ochrony tożsamości w zasadach
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
ms.openlocfilehash: 8054d8985596095db32d9262322d7fb0f4aab8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295152"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Dostęp warunkowy: dostęp warunkowy oparty na ryzyku

Organizacje posiadające licencje Usługi Azure AD Premium P2 mogą tworzyć zasady dostępu warunkowego zawierające wykrywanie ryzyka usługi Azure AD Identity Protection. Istnieją trzy domyślne zasady, które można włączyć po wyjęciu z pudełka. 

* Wymagaj od wszystkich użytkowników, aby zarejestrowali się w celu uzyskania uwierzytelniania wieloskładnikowego platformy Azure.
* Wymagaj zmiany hasła dla użytkowników wysokiego ryzyka.
* Wymagaj uwierzytelniania wieloskładnikowego dla użytkowników o średnim lub wysokim ryzyku logowania.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Wymaganie od wszystkich użytkowników rejestracji w celu uzyskania uwierzytelniania wieloskładnikowego platformy Azure

Włączenie tej zasady będzie wymagać od wszystkich użytkowników zarejestrowania się w celu uzyskania uwierzytelniania wieloskładnikowego platformy Azure w ciągu 14 dni. 

1. Zaloguj się do **Portalu Azure**.
1. Kliknij pozycję **Wszystkie usługi**, a następnie przejdź do usługi **Azure AD Identity Protection**.
1. Kliknij pozycję **Rejestracja w usłudze MFA**.
1. W obszarze **Przydziały**wybierz pozycję **Użytkownicy**.
   1. W **obszarze Uwzględnij**wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz**wybierz **pozycję Wybierz wykluczonych użytkowników**, wybierz dostęp awaryjny w organizacji lub konta typu break-glass i wybierz pozycję **Wybierz**. 
   1. Wybierz pozycję **Done** (Gotowe).
1. Ustaw **zasadę wymuszania** **na wł.**
1. Kliknij przycisk **Zapisz**.

## <a name="require-a-password-change-high-risk-users"></a>Wymaganie zmiany hasła przez użytkowników wysokiego ryzyka

Firma Microsoft współpracuje z naukowcami, organami ścigania, różnymi zespołami ds. zabezpieczeń w firmie Microsoft i innymi zaufanymi źródłami w celu wyszukiwania par „nazwa użytkownika i hasło”. Gdy jedna z takich par jest zgodna z kontem w Twoim środowisku, można wyzwolić zmianę hasła opartą na ryzykach, używając następujących zasad.

1. Zaloguj się do **Portalu Azure**.
1. Kliknij pozycję **Wszystkie usługi**, a następnie przejdź do usługi **Azure AD Identity Protection**.
1. Kliknij **politykę ryzyka użytkownika**.
1. W obszarze **Przydziały**wybierz **pozycję Użytkownicy**
   1. W **obszarze Uwzględnij**wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz**wybierz **pozycję Wybierz wykluczonych użytkowników**, wybierz dostęp awaryjny w organizacji lub konta typu break-glass i wybierz pozycję **Wybierz**.
   1. Wybierz pozycję **Done** (Gotowe).
1. W **obszarze Warunki**wybierz opcję Ryzyko **użytkownika**, a następnie wybierz pozycję **Wysoki**.
   1. Kliknij **pozycję Zaznacz,** a następnie **gotowe**.
1. W obszarze **Kontrola** > **dostępu**wybierz pozycję **Zezwalaj na dostęp**, a następnie wybierz pozycję **Wymagaj zmiany hasła**.
   1. Kliknij **pozycję Wybierz**.
1. Ustaw **zasadę wymuszania** **na wł.**
1. Kliknij przycisk **Zapisz**.

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>Wymagaj użytkowników o średnim lub wysokim ryzyku logowania z pomocą mfa

Większości użytkowników dotyczy zachowanie normalne, które można śledzić. Gdy ta norma zostanie przekroczona, zezwolenie tym użytkownikom na zwykłe logowanie się może stanowić ryzyko. Możesz zablokować tego użytkownika, a może po prostu poprosić go o uwierzytelnianie wieloskładnikowe, aby udowodnić, że są naprawdę tym, kim mówią, że są. Aby włączyć zasady wymagające uwierzytelniania wieloskładnikowego, gdy zostanie wykryte ryzykowne logowanie, użyj następujących zasad.

1. Zaloguj się do **Portalu Azure**.
1. Kliknij pozycję **Wszystkie usługi**, a następnie przejdź do usługi **Azure AD Identity Protection**.
1. Kliknij **zasady dotyczące ryzyka logowania**
1. W obszarze **Przydziały**wybierz **pozycję Użytkownicy**
   1. W **obszarze Uwzględnij**wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz**wybierz **pozycję Wybierz wykluczonych użytkowników**, wybierz dostęp awaryjny w organizacji lub konta typu break-glass i wybierz pozycję **Wybierz**.
   1. Wybierz pozycję **Done** (Gotowe).
1. W **obszarze Warunki**wybierz opcję Ryzyko **logowania**, a następnie wybierz pozycję Średni **i wyższy**.
   1. Kliknij **pozycję Zaznacz,** a następnie **gotowe**.
1. W obszarze **Kontrola** > **dostępu**wybierz pozycję **Zezwalaj na dostęp**, a następnie wybierz pozycję **Wymagaj uwierzytelniania wieloskładnikowego**.
   1. Kliknij **pozycję Wybierz**.
1. Ustaw **zasadę wymuszania** **na wł.**
1. Kliknij przycisk **Zapisz**.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Określanie wpływu przy użyciu trybu tylko dla dostępu warunkowego](howto-conditional-access-report-only.md)

[Symulowanie zachowania logowania za pomocą narzędzia Co jeśli dostęp warunkowy](troubleshoot-conditional-access-what-if.md)

[Jak to działa: usługa Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

[Co to jest usługa Azure Active Directory Identity Protection?](../identity-protection/overview.md)
