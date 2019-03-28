---
title: Jak skonfigurować zasady ryzyka logowania w usłudze Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować zasady ryzyka logowania usługi Azure AD Identity Protection.
services: active-directory
keywords: Usługa Azure active directory identity protection odnajdywania aplikacji w chmurze, zarządzanie aplikacji, zabezpieczenia, ryzyka, poziom ryzyka, luk w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe9e0a4d481ef7b802c50fdc347872e389fa8ef7
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518049"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>Instrukcje: Konfigurowanie zasad ryzyka logowania

Usługa Azure Active Directory wykrywa [typy zdarzeń ryzyka](../reports-monitoring/concept-risk-events.md#risk-event-types) w czasie rzeczywistym, jak i offline. Każdego zdarzenia o podwyższonym ryzyku, która została wykryta podczas logowania użytkownika przyczynia się do logiczne koncepcji o nazwie ryzykowne logowania. Ryzykowne logowanie jest wskaźnikiem próby logowania, które nie mogły zostać wykonane przez prawowitym właścicielem konta użytkownika.


## <a name="what-is-the-sign-in-risk-policy"></a>Co to są zasady ryzyka logowania?

Usługa Azure AD analizuje każdego logowania użytkownika. Cel analizy jest wykrywa podejrzane akcje, które pochodzą wraz z logowania. Na przykład jest logowanie wykonywane przy użyciu anonimowego adresu IP lub jest logowanie inicjowane z nieznanej lokalizacji? W usłudze Azure AD podejrzane działania, które system może wykrywać znane są również jako zdarzeń o podwyższonym ryzyku. Oparte na zdarzenia o podwyższonym ryzyku, które zostały wykryte podczas logowania, usługi Azure AD pozwala obliczyć wartości. Wartość reprezentuje prawdopodobieństwo (niskiej, średniej, wysokiej), czy identyfikator logowania nie jest wykonywane przez wiarygodnego użytkownika. Prawdopodobieństwo, że jest wywoływana **poziom ryzyka logowania**.

Zasady ryzyka logowania to automatyczną odpowiedź, które można skonfigurować na poziomie określonej ryzyka logowania. W odpowiedzi możesz zablokować dostęp do zasobów lub wymagają, przekazując żądanie uwierzytelniania wieloskładnikowego (MFA) do uzyskania dostępu.

   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Jak uzyskać dostęp do zasad ryzyka logowania?
   
Policy ryzyka logowania jest dostępna w **Konfiguruj** sekcji na [strony usługi Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zasady ryzyka logowania](./media/howto-sign-in-risk-policy/1014.png "zasad ryzyka logowania")


## <a name="policy-settings"></a>Ustawienia zasad

Po skonfigurowaniu zasad ryzyka logowania, należy ustawić:

- Użytkownicy i grupy, które zostaną zastosowane zasady:

    ![Użytkownicy i grupy](./media/howto-sign-in-risk-policy/11.png)

- Poziom ryzyka logowania, wyzwalającego zasad:

    ![Poziom ryzyka logowania](./media/howto-sign-in-risk-policy/12.png)

- Typ dostępu, który ma być wymuszane, gdy poziom ryzyka logowania zostaną spełnione:  

    ![Dostęp](./media/howto-sign-in-risk-policy/13.png)

- Stan zasad:

    ![Wymuszanie zasad](./media/howto-sign-in-risk-policy/14.png)


W oknie dialogowym konfiguracji zasad zapewnia możliwość szacunkowa ocena wpływu ponownej konfiguracji.

![Szacowany wpływ](./media/howto-sign-in-risk-policy/15.png)

## <a name="what-you-should-know"></a>Co należy wiedzieć

Można skonfigurować zasady zabezpieczeń ryzyka logowania, które wymagają usługi MFA:

![Wymaganie usługi MFA](./media/howto-sign-in-risk-policy/16.png)

Jednak ze względu na bezpieczeństwo, to ustawienie działa tylko dla użytkowników, które zostały już zarejestrowane dla usługi MFA. Ochronę tożsamości blokuje użytkowników za pomocą wymaganie uwierzytelniania Wieloskładnikowego, jeśli ich nie są zarejestrowani do uwierzytelniania Wieloskładnikowego jeszcze.

Jeśli chcesz pozyskać usługę MFA dla ryzykownych logowań, należy:

1. Włącz [zasady rejestracji uwierzytelniania wieloskładnikowego](howto-mfa-policy.md) dla użytkowników, których to dotyczy.

2. Wymaga odpowiednich użytkowników, aby logowanie się — ryzykowne sesji w celu przeprowadzenia rejestracji usługi MFA.

Wykonanie tych kroków gwarantuje, że to uwierzytelnianie wieloskładnikowe jest wymagany do ryzykowne logowania.

Zasady ryzyka logowania to:

- Stosowane do całego ruchu w przeglądarce i logowania korzystające z nowoczesnego uwierzytelniania.

- Nie są stosowane do aplikacji przy użyciu starszych protokołów zabezpieczeń przez wyłączenie punktu końcowego protokołu WS-Trust u dostawcy tożsamości federacyjnych, takich jak usługi AD FS.


Omówienie środowiska użytkownika Zobacz:

* [Ryzykowne logowania odzyskiwania](flows.md#risky-sign-in-recovery)
* [Ryzykowne logowanie zablokowane](flows.md#risky-sign-in-blocked)  
* [Środowisko logowania za pomocą usługi Azure AD Identity Protection](flows.md)  

## <a name="best-practices"></a>Najlepsze praktyki

Wybieranie **wysokiej** próg zmniejsza liczbę razy, zasada zostanie wyzwolony i minimalizuje wpływ na użytkowników.  

Jednakże nie obejmuje **niski** i **średni** logowania oznaczonych flagą ryzyka z zasad, które nie mogą blokować atakujący korzystający z wykorzystanie tożsamości ze złamanymi zabezpieczeniami.

Podczas ustawiania zasad

- Wyklucz użytkowników, którzy nie obsługują / nie może mieć uwierzytelnianie wieloskładnikowe

- Wyklucz użytkowników w lokalizacjach, w którym włączenie zasad nie jest możliwe (na przykład brak dostępu do działu pomocy technicznej)

- Wyklucz użytkowników, którzy mogą generować wiele fałszywych alarmów (deweloperzy, analityków zabezpieczeń)

- Użyj **wysokiej** próg podczas zasady początkowe wdrożenie, czy należy zminimalizować wyzwania widoczne dla użytkowników końcowych.

- Użyj **niski** próg, jeśli Twoja organizacja wymaga zwiększenia bezpieczeństwa. Wybieranie **niski** próg wprowadza dodatkowego użytkownika logowania wyzwania, ale wyższy poziom bezpieczeństwa.

Zalecana domyślna w przypadku większości organizacji jest skonfigurowanie reguły dla **średni** próg, aby zachować równowagę pomiędzy użyteczność i zabezpieczeń.






## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać omówienie programu Azure AD Identity Protection, zobacz [Omówienie usługi Azure AD Identity Protection](overview.md).
