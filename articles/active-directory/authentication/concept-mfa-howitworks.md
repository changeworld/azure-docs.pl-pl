---
title: Omówienie uwierzytelniania wieloskładnikowego platformy Azure
description: Dowiedz się, jak uwierzytelnianie wieloskładnikowe platformy Azure pomaga chronić dostęp do danych i aplikacji, spełniając jednocześnie zapotrzebowanie użytkowników na prosty proces logowania.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c50232abd12c8c0390409bd7bf72833b4f153e02
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667360"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Jak to działa: usługa Azure Multi-Factor Authentication

Uwierzytelnianie wieloskładnikowe to proces, w którym użytkownik jest monitowany podczas procesu logowania o dodatkową formę identyfikacji, taką jak wprowadzenie kodu na telefonie komórkowym lub dostarczenie skanowania odcisków palców.

Jeśli używasz tylko hasła do uwierzytelniania użytkownika, pozostawia niezabezpieczony wektor do ataku. Jeśli hasło jest słabe lub zostało ujawnione w innym miejscu, czy rzeczywiście użytkownik loguje się przy użyciu nazwy użytkownika i hasła, czy też jest to osoba atakująca? Jeśli potrzebujesz drugiej formy uwierzytelniania, zwiększa się bezpieczeństwo, ponieważ ten dodatkowy czynnik nie jest łatwym dla osoby atakującej w uzyskaniu lub zduplikowaniu.

![Obraz koncepcyjny różnych form uwierzytelniania wieloskładnikowego](./media/concept-mfa-howitworks/methods.png)

Uwierzytelnianie wieloskładnikowe platformy Azure działa, wymagając co najmniej dwóch z następujących metod uwierzytelniania:

* Coś, co wiesz, zazwyczaj hasło.
* Coś, co masz, takie jak zaufane urządzenie, które nie jest łatwo powielane, takie jak telefon lub klucz sprzętowy.
* Coś, czym jesteś - biometria, taka jak odcisk palca lub skanowanie twarzy.

Użytkownicy mogą zarejestrować się w celu resetowania hasła samoobsługowego i uwierzytelniania wieloskładnikowego platformy Azure w jednym kroku, aby uprościć środowisko przy wejściu na pokład. Administratorzy mogą definiować, jakie formy uwierzytelniania pomocniczego mogą być używane. Uwierzytelnianie wieloskładnikowe platformy Azure może być również wymagane, gdy użytkownicy wykonują samoobsługowe resetowanie hasła w celu dalszego zabezpieczenia tego procesu.

![Metody uwierzytelniania używane na ekranie logowania](media/concept-authentication-methods/overview-login.png)

Uwierzytelnianie wieloskładnikowe platformy Azure pomaga chronić dostęp do danych i aplikacji przy zachowaniu prostoty dla użytkowników. Zapewnia dodatkowe bezpieczeństwo, wymagając drugiej formy uwierzytelniania i zapewnia silne uwierzytelnianie za pomocą szeregu łatwych w użyciu [metod uwierzytelniania.](concept-authentication-methods.md) Użytkownicy mogą lub nie mogą być kwestionowane dla usługi MFA na podstawie decyzji konfiguracji, które administrator sprawia, że.

Aplikacje lub usługi nie trzeba wprowadzać żadnych zmian, aby korzystać z uwierzytelniania wieloskładnikowego platformy Azure. Monity weryfikacji są częścią zdarzenia logowania usługi Azure AD, które automatycznie żąda i przetwarza wyzwanie usługi MFA, gdy jest to wymagane.

## <a name="available-verification-methods"></a>Dostępne metody weryfikacji

Gdy użytkownik loguje się do aplikacji lub usługi i otrzymuje monit usługi MFA, może wybrać jeden z zarejestrowanych formularzy dodatkowej weryfikacji. Administrator może wymagać rejestracji tych metod weryfikacji uwierzytelniania wieloskładnikowego platformy Azure lub użytkownik może uzyskać dostęp do własnego [profilu Mój profil,](https://myprofile.microsoft.com) aby edytować lub dodać metody weryfikacji.

Z uwierzytelnianiem wieloskładnikowym azure multiskładnikowego można używać następujących dodatkowych form weryfikacji:

* Aplikacja Microsoft Authenticator
* TOKEN SPRZĘTOWY OATH
* SMS
* Połączenie głosowe

## <a name="how-to-enable-and-use-azure-multi-factor-authentication"></a>Jak włączyć i używać uwierzytelniania wieloskładnikowego platformy Azure

Użytkownicy i grupy można włączyć dla usługi Azure Multi-Factor Authentication, aby monitować o dodatkową weryfikację podczas zdarzenia logowania. [Domyślne zabezpieczenia](../fundamentals/concept-fundamentals-security-defaults.md) są dostępne dla wszystkich dzierżaw usługi Azure AD, aby szybko włączyć korzystanie z aplikacji Microsoft Authenticator dla wszystkich użytkowników.

W przypadku bardziej szczegółowych formantów zasady [dostępu warunkowego](../conditional-access/overview.md) mogą być używane do definiowania zdarzeń lub aplikacji, które wymagają usługi MFA. Te zasady mogą zezwalać na regularne zdarzenia logowania, gdy użytkownik znajduje się w sieci firmowej lub zarejestrowanym urządzeniu, ale monitują o dodatkowe czynniki weryfikacji, gdy są zdalne lub na urządzeniu osobistym.

![Omówienie diagramu działania programu Dostęp warunkowy w celu zabezpieczenia procesu logowania](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o licencjonowaniu, zobacz [Funkcje i licencje na uwierzytelnianie wieloskładnikowe platformy Azure](concept-mfa-licensing.md).

Aby wyświetlić usługę MFA w akcji, włącz uwierzytelnianie wieloskładnikowe platformy Azure dla zestawu użytkowników testowych w następującym samouczku:

> [!div class="nextstepaction"]
> [Włączanie usługi Azure Multi-Factor Authentication](tutorial-mfa-applications.md)
