---
title: Dostęp warunkowy — wymagaj zgodnych urządzeń — usługa Azure Active Directory
description: Tworzenie niestandardowych zasad dostępu warunkowego w celu wymagania zgodnych urządzeń
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
ms.openlocfilehash: cc6bd486c1e8338eaf875c7026764c80d49e2f05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295202"
---
# <a name="conditional-access-require-compliant-devices"></a>Dostęp warunkowy: wymagaj zgodnych urządzeń

Organizacje, które wdrożyły usługę Microsoft Intune, mogą używać informacji zwróconych z ich urządzeń do identyfikowania urządzeń spełniających wymagania zgodności, takie jak:

* Wymaganie numeru PIN do odblokowania
* Wymaganie szyfrowania urządzeń
* Wymaganie minimalnej lub maksymalnej wersji systemu operacyjnego
* Wymaganie urządzenia nie jest jailbroken lub zakorzenione

Te informacje o zgodności zasad są przekazywane do usługi Azure AD, gdzie dostęp warunkowy może podejmować decyzje o przyznaniu lub zablokowaniu dostępu do zasobów. Więcej informacji na temat zasad zgodności urządzeń można znaleźć w [artykule Ustaw reguły na urządzeniach, aby zezwolić na dostęp do zasobów w organizacji za pomocą usługi Intune](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Poniższe kroki pomogą utworzyć zasady dostępu warunkowego, aby wymagać, aby urządzenia uzyskujące dostęp do zasobów były oznaczone jako zgodne z zasadami zgodności usługi Intune organizacji.

1. Zaloguj się do **witryny Azure portal** jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **usługi Azure Active Directory** > **Security** > **Conditional Access**.
1. Wybierz **pozycję Nowa zasada**.
1. Nadaj polityce nazwę. Zaleca się, aby organizacje tworzyły znaczący standard nazw swoich zasad.
1. W obszarze **Przydziały**wybierz **pozycję Użytkownicy i grupy**
   1. W **obszarze Uwzględnij**wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz**wybierz **pozycję Użytkownicy i grupy** i wybierz dostęp awaryjny w organizacji lub konta typu break-glass. 
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **Aplikacje lub akcje** > w chmurze**Uwzględnij**wybierz pozycję **Wszystkie aplikacje w chmurze**.
   1. Jeśli musisz wykluczyć określone aplikacje z zasad, możesz wybrać je z karty **Wyklucz** w obszarze **Wybierz wykluczone aplikacje w chmurze** i wybierz pozycję **Wybierz**.
   1. Wybierz pozycję **Done** (Gotowe).
1. W **obszarze Warunki** > **aplikacje klienckie (Wersja zapoznawcza)** ustaw **pozycję Konfiguruj** na **Tak**i wybierz opcję **Gotowe**.
1. W obszarze **Kontrola** > dostępu**Udzielaj**wybierz pozycję **Wymagaj oznaczania urządzenia jako zgodnego**.
   1. Wybierz przycisk **Wybierz**.
1. Potwierdź ustawienia i ustaw **włącz zasadę** **na Włącz**.
1. Wybierz **pozycję Utwórz,** aby utworzyć, aby włączyć zasady.

> [!NOTE]
> Nowe urządzenia można zarejestrować w usłudze Intune, nawet jeśli wybierzesz **opcję Wymagaj, aby urządzenie było oznaczone jako zgodne** dla wszystkich **użytkowników** i wszystkich aplikacji **w chmurze,** wykonując powyższe kroki. **Wymagaj, aby urządzenie było oznaczone jako zgodne** formantu nie blokuje rejestracji usługi Intune. 

### <a name="known-behavior"></a>Znane zachowanie

W systemie Windows 7, iOS, Android, macOS i niektórych przeglądarkach internetowych innych firm usługi Azure AD identyfikuje urządzenie przy użyciu certyfikatu klienta, który jest aprowidyfikowany, gdy urządzenie jest zarejestrowane w usłudze Azure AD. Gdy użytkownik po raz pierwszy loguje się za pośrednictwem przeglądarki, użytkownik jest monitowany o wybranie certyfikatu. Użytkownik końcowy musi wybrać ten certyfikat, zanim będzie mógł nadal korzystać z przeglądarki.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Określanie wpływu przy użyciu trybu tylko dla dostępu warunkowego](howto-conditional-access-report-only.md)

[Symulowanie zachowania logowania za pomocą narzędzia Co jeśli dostęp warunkowy](troubleshoot-conditional-access-what-if.md)

[Współdziałanie zasad zgodności urządzeń z usługą Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
