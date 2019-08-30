---
title: Jak skonfigurować zasady dotyczące ryzyka związanego z logowaniem w Azure Active Directory Identity Protection | Microsoft Docs
description: Dowiedz się, jak skonfigurować zasady dotyczące ryzyka związanego z logowaniem Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d00376c6689b6be773f24e8acd09c3697fb6a799
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126308"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>Instrukcje: Konfigurowanie zasad ryzyka logowania

Azure Active Directory wykrywa [typy wykrywania ryzyka](../reports-monitoring/concept-risk-events.md#risk-detection-types) w czasie rzeczywistym i w trybie offline. Każde wykrywanie ryzyka wykryte w przypadku logowania użytkownika przyczynia się do logicznej koncepcji zwanej ryzykownym logowaniem. Ryzykowne logowanie jest wskaźnikiem próby logowania, które mogły nie zostać wykonane przez uprawnionego właściciela konta użytkownika.

## <a name="what-is-the-sign-in-risk-policy"></a>Jakie są zasady dotyczące ryzyka związanego z logowaniem?

Usługa Azure AD analizuje każdy zalogowanie użytkownika. Celem analizy jest wykrycie podejrzanych działań, które są związane z logowaniem. Na przykład czy logowanie odbywa się przy użyciu anonimowego adresu IP lub czy logowanie zostało zainicjowane z nieznanej lokalizacji? W usłudze Azure AD podejrzane działania wykrywane przez system są również znane jako wykrywanie zagrożeń. Na podstawie wykrytych wykryć ryzyka podczas logowania usługa Azure AD oblicza wartość. Wartość reprezentuje prawdopodobieństwo (niski, średni, wysoki), że logowanie nie jest wykonywane przez uprawnionego użytkownika. Prawdopodobieństwo jest nazywane **poziomem ryzyka logowania**.

Zasady dotyczące ryzyka związanego z logowaniem to zautomatyzowana odpowiedź, którą można skonfigurować dla określonego poziomu ryzyka związanego z logowaniem. W odpowiedzi można zablokować dostęp do zasobów lub wymagać przekazywania wyzwania uwierzytelniania wieloskładnikowego (MFA) w celu uzyskania dostępu.
   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Jak mogę uzyskać dostęp do zasad dotyczących ryzyka związanego z logowaniem?
   
Zasady dotyczące ryzyka związanego z logowaniem znajduje się w sekcji **Konfigurowanie** na [stronie Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zasady dotyczące ryzyka związanego z logowaniem](./media/howto-sign-in-risk-policy/1014.png "Zasady dotyczące ryzyka związanego z logowaniem")

## <a name="policy-settings"></a>Ustawienia zasad

Konfigurując zasady dotyczące ryzyka związanego z logowaniem, należy ustawić:

- Użytkownicy i grupy, których dotyczą zasady:

    ![Użytkownicy i grupy](./media/howto-sign-in-risk-policy/11.png)

- Poziom ryzyka logowania, który wyzwala zasady:

    ![Poziom ryzyka związanego z logowaniem](./media/howto-sign-in-risk-policy/12.png)

- Typ dostępu, który ma zostać wymuszony po spełnieniu poziomu ryzyka związanego z logowaniem:  

    ![Access](./media/howto-sign-in-risk-policy/13.png)

- Stan zasad:

    ![Wymuś zasady](./media/howto-sign-in-risk-policy/14.png)

W oknie dialogowym Konfiguracja zasad dostępna jest opcja oszacowania wpływu ponownej konfiguracji.

![Szacowany wpływ](./media/howto-sign-in-risk-policy/15.png)

## <a name="what-you-should-know"></a>Co należy wiedzieć

Aby wymagać uwierzytelniania wieloskładnikowego, można skonfigurować zasady zabezpieczeń dotyczące ryzyka logowania:

![Wymaganie usługi MFA](./media/howto-sign-in-risk-policy/16.png)

Ze względów bezpieczeństwa to ustawienie działa tylko w przypadku użytkowników, którzy zostali już zarejestrowani na potrzeby uwierzytelniania wieloskładnikowego. Funkcja Identity Protection blokuje użytkownikom wymóg uwierzytelniania MFA, jeśli nie są jeszcze zarejestrowani dla usługi MFA.

Aby wymagać uwierzytelniania wieloskładnikowego w przypadku ryzykownych logowań, należy:

1. Włącz [zasady rejestracji uwierzytelniania](howto-mfa-policy.md) wieloskładnikowego dla użytkowników, których to dotyczy.
2. Zażądaj zalogowanych użytkowników do logowania się do sesji nieryzykownej w celu przeprowadzenia rejestracji usługi MFA.

Wykonanie tych kroków gwarantuje, że uwierzytelnianie wieloskładnikowe jest wymagane w przypadku ryzykownego logowania.

Zasady dotyczące ryzyka związanego z logowaniem:

- Stosowane do całego ruchu przeglądarki i logowania przy użyciu nowoczesnego uwierzytelniania.
- Nie dotyczy aplikacji korzystających ze starszych protokołów zabezpieczeń poprzez wyłączenie punktu końcowego WS-Trust w dostawcy tożsamości federacyjnym, na przykład ADFS.

Aby zapoznać się z omówieniem środowiska użytkownika powiązanego, zobacz:

* [Ryzykowne odzyskiwanie do logowania](flows.md#risky-sign-in-recovery)
* [Ryzykowne logowanie zostało zablokowane](flows.md#risky-sign-in-blocked)  
* [Środowisko logowania za pomocą Azure AD Identity Protection](flows.md)  

## <a name="best-practices"></a>Najlepsze praktyki

Wybranie **wysokiego** progu zmniejsza liczbę wyzwalanych zasad i minimalizuje wpływ na użytkowników.  

Nie obejmuje to jednak **małych** i **średnich** logowań oflagowanych ze względu na ryzyko z zasad, które mogą nie blokować wykorzystywania przez osobę atakującą tożsamości.

Podczas ustawiania zasad,

- Wyklucz użytkowników, którzy nie mogą korzystać z uwierzytelniania wieloskładnikowego
- Wykluczanie użytkowników w lokalizacjach lokalnych, w których włączenie zasad nie jest praktyczne (na przykład brak dostępu do pomocy technicznej)
- Wyklucz użytkowników, którzy mogą generować wiele fałszywie fałszywych (deweloperów, analityków zabezpieczeń)
- Użyj **wysokiego** progu podczas wstępnego wdrożenia zasad lub jeśli musisz zminimalizować wyzwania występujące dla użytkowników końcowych.
- Jeśli organizacja wymaga większego **poziomu** zabezpieczeń, należy użyć niskiego progu. Wybranie **niskiego** progu powoduje wprowadzenie dodatkowych wyzwań związanych z logowaniem użytkowników, ale zwiększenie bezpieczeństwa.

Zalecanym ustawieniem domyślnym w przypadku większości organizacji jest skonfigurowanie reguły dla **średniego** progu w celu zrównoważenia równowagi między użytecznością a bezpieczeństwem.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem Azure AD Identity Protection, zobacz [omówienie Azure AD Identity Protection](overview.md).
