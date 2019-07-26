---
title: Jak skonfigurować zasady dotyczące ryzyka w programie Azure Active Directory Identity Protection (odświeżone) | Microsoft Docs
description: Jak skonfigurować zasady dotyczące ryzyka w programie Azure Active Directory Identity Protection (odświeżone).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ce4e2958978de9339f4340755e3740730025a5f
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334032"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>Instrukcje: Konfigurowanie zasad ryzyka w programie Azure Active Directory Identity Protection (odświeżanie)

Usługa Azure AD wykrywa zdarzenia ryzyka, które są wskaźnikami dla potencjalnie złamanych tożsamości. Konfigurując zasady dotyczące ryzyka, można definiować automatyczne odpowiedzi na wyniki wykrywania:

- Za pomocą zasad dotyczących ryzyka związanego z logowaniem można skonfigurować odpowiedź na zdarzenia ryzyka w czasie rzeczywistym, które zostały wykryte podczas logowania użytkownika. 
- Korzystając z zasad ryzyka dla użytkowników, można skonfigurować odpowiedzi na wszystkie aktywne zagrożenia użytkownika, które zostały wykryte dla użytkownika w czasie.  

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="what-is-the-sign-in-risk-policy"></a>Jakie są zasady dotyczące ryzyka związanego z logowaniem?

Usługa Azure AD analizuje każdy zalogowanie użytkownika. Celem analizy jest wykrycie podejrzanych działań, które są związane z logowaniem. Na przykład czy logowanie odbywa się przy użyciu anonimowego adresu IP lub czy logowanie zostało zainicjowane z nieznanej lokalizacji? W usłudze Azure AD podejrzane działania wykrywane przez system są również znane jako zdarzenia ryzyka. W oparciu o zdarzenia ryzyka wykryte podczas logowania usługa Azure AD oblicza wartość. Wartość reprezentuje prawdopodobieństwo (niski, średni, wysoki), że logowanie nie jest wykonywane przez uprawnionego użytkownika. Prawdopodobieństwo jest nazywane **poziomem ryzyka logowania**.

Zasady dotyczące ryzyka związanego z logowaniem to zautomatyzowana odpowiedź, którą można skonfigurować dla określonego poziomu ryzyka związanego z logowaniem. W odpowiedzi można zablokować dostęp do zasobów lub wymagać przekazywania wyzwania uwierzytelniania wieloskładnikowego (MFA) w celu uzyskania dostępu.

Gdy użytkownik pomyślnie ukończy monit usługi MFA wyzwalany przez zasady dotyczące ryzyka związanego z logowaniem, przekazuje informacje zwrotne do ochrony tożsamości, że logowanie pochodzi od uprawnionego użytkownika. Oznacza to, że zdarzenie związane z logowaniem, które wyzwoliło monit usługi MFA, zostanie automatycznie zamknięte, a Ochrona tożsamości nie będzie mogła przyczynić się do podniesienia ryzyka dla użytkownika. Włączenie zasad dotyczących ryzyka związanego z logowaniem może ograniczyć noisiness w widoku ryzykowne logowania, umożliwiając użytkownikom samodzielne korygowanie po wyświetleniu monitu dotyczącego usługi MFA, a następnie automatycznie zamykające skojarzone ryzykowne logowanie.

## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Jak mogę uzyskać dostęp do zasad dotyczących ryzyka związanego z logowaniem?
   
Zasady dotyczące ryzyka związanego z logowaniem znajduje się w sekcji **Konfigurowanie** na [stronie Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zasady dotyczące ryzyka związanego z logowaniem](./media/howto-configure-risk-policies/1014.png "Zasady dotyczące ryzyka związanego z logowaniem")

## <a name="sign-in-risk-policy-settings"></a>Ustawienia zasad ryzyka związanego z logowaniem

Konfigurując zasady dotyczące ryzyka związanego z logowaniem, należy ustawić:

- Użytkownicy i grupy, których dotyczą zasady:

   ![Użytkownicy i grupy](./media/howto-configure-risk-policies/11.png)

- Poziom ryzyka logowania, który wyzwala zasady:

   ![Poziom ryzyka logowania](./media/howto-configure-risk-policies/12.png)

- Typ dostępu, który ma zostać wymuszony po spełnieniu poziomu ryzyka związanego z logowaniem:  

   ![Access](./media/howto-configure-risk-policies/13.png)

- Stan zasad:

   ![Wymuś zasady](./media/howto-configure-risk-policies/14.png)

W oknie dialogowym Konfiguracja zasad dostępna jest opcja oszacowania wpływu ponownej konfiguracji.

![Szacowany wpływ](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>Co należy wiedzieć o zasadach dotyczących ryzyka związanego z logowaniem

Aby wymagać uwierzytelniania wieloskładnikowego, można skonfigurować zasady zabezpieczeń dotyczące ryzyka logowania:

![Wymaganie usługi MFA](./media/howto-configure-risk-policies/16.png)

Ze względów bezpieczeństwa to ustawienie działa tylko w przypadku użytkowników, którzy zostali już zarejestrowani na potrzeby uwierzytelniania wieloskładnikowego. Funkcja Identity Protection blokuje użytkownikom wymóg uwierzytelniania MFA, jeśli nie są jeszcze zarejestrowani dla usługi MFA.

Aby wymagać uwierzytelniania wieloskładnikowego w przypadku ryzykownych logowań, należy:

1. Włącz zasady rejestracji uwierzytelniania wieloskładnikowego dla użytkowników, których to dotyczy.
2. Zażądaj, aby użytkownicy mogli zalogować się w sesji nieryzykownej w celu przeprowadzenia rejestracji usługi MFA.

Wykonanie tych kroków gwarantuje, że uwierzytelnianie wieloskładnikowe jest wymagane w przypadku ryzykownego logowania.

Zasady dotyczące ryzyka związanego z logowaniem:

- Stosowane do całego ruchu przeglądarki i logowania przy użyciu nowoczesnego uwierzytelniania.
- Nie dotyczy aplikacji korzystających ze starszych protokołów zabezpieczeń poprzez wyłączenie punktu końcowego WS-Trust w dostawcy tożsamości federacyjnym, na przykład ADFS.

Aby zapoznać się z omówieniem środowiska użytkownika powiązanego, zobacz:

* [Ryzykowne odzyskiwanie do logowania](flows.md#risky-sign-in-recovery)
* [Ryzykowne logowanie zostało zablokowane](flows.md#risky-sign-in-blocked)  
* [Środowisko logowania za pomocą Azure AD Identity Protection](flows.md)  

## <a name="what-is-a-user-risk-policy"></a>Co to są zasady ryzyka dla użytkowników?

Usługa Azure AD analizuje każdy zalogowanie użytkownika. Celem analizy jest wykrycie podejrzanych działań, które są związane z logowaniem. W usłudze Azure AD podejrzane działania wykrywane przez system są również znane jako zdarzenia ryzyka. Niektóre zdarzenia ryzyka mogą być wykrywane w czasie rzeczywistym, ale istnieją także zdarzenia wymagające więcej czasu. Na przykład w celu wykrycia niemożliwej podróży do nietypowych lokalizacji system wymaga początkowego okresu szkoleniowego 14 dni, aby poznać jego zwykłe zachowanie. Istnieje kilka opcji rozwiązywania wykrytych zdarzeń o podwyższonym ryzyku. Można na przykład ręcznie rozwiązać poszczególne zdarzenia związane z ryzykiem lub je rozwiązać przy użyciu ryzyka związanego z logowaniem lub zasad dostępu warunkowego do ryzyka użytkownika.

Wszystkie zdarzenia ryzyka, które zostały wykryte dla użytkownika i nie zostały rozpoznane, są znane jako aktywne zdarzenia ryzyka. Zdarzenia aktywnego ryzyka, które są skojarzone z użytkownikiem, są nazywane ryzykiem użytkownika. Na podstawie ryzyka związanego z użytkownikiem usługa Azure AD oblicza prawdopodobieństwo naruszenia bezpieczeństwa użytkownika. Prawdopodobieństwo jest nazywane poziomem ryzyka użytkownika.

![Czynniki ryzyka użytkownika](./media/howto-configure-risk-policies/11031.png)

Zasady ryzyka dla użytkowników to zautomatyzowana odpowiedź, którą można skonfigurować dla określonego poziomu ryzyka użytkownika. Korzystając z zasad ryzyka dla użytkowników, można zablokować dostęp do zasobów lub wymagać zmiany hasła w celu przywrócenia stanu konta użytkownika.

## <a name="how-do-i-access-the-user-risk-policy"></a>Jak mogę uzyskać dostęp do zasad ryzyka dla użytkowników?
   
Zasady ryzyka dla użytkowników znajduje się w sekcji **Konfigurowanie** na [stronie Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zasady dotyczące ryzyka związanego z użytkownikiem](./media/howto-configure-risk-policies/11014.png)

## <a name="user-risk-policy-settings"></a>Ustawienia zasad ryzyka użytkownika

Konfigurując zasady ryzyka dla użytkowników, należy ustawić:

- Użytkownicy i grupy, których dotyczą zasady:

   ![Użytkownicy i grupy](./media/howto-configure-risk-policies/111.png)

- Poziom ryzyka logowania, który wyzwala zasady:

   ![Poziom ryzyka użytkownika](./media/howto-configure-risk-policies/112.png)

- Typ dostępu, który ma zostać wymuszony po spełnieniu poziomu ryzyka związanego z logowaniem:  

   ![Access](./media/howto-configure-risk-policies/113.png)

- Stan zasad:

   ![Wymuś zasady](./media/howto-configure-risk-policies/114.png)

W oknie dialogowym Konfiguracja zasad dostępna jest opcja oszacowania wpływu konfiguracji.

![Szacowany wpływ](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>Co należy wiedzieć na temat zasad ryzyka dla użytkowników

Można ustawić zasady zabezpieczeń ryzyka dla użytkowników, aby blokować użytkownikom logowanie się w zależności od poziomu ryzyka.

![Blokowanie](./media/howto-configure-risk-policies/116.png)

Blokowanie logowania:

* Zapobiega generowaniu nowych zdarzeń ryzyka użytkownika dla danego użytkownika
* Umożliwia administratorom ręczne korygowanie zdarzeń ryzyka mających wpływ na tożsamość użytkownika i przywrócenie go do stanu bezpiecznego

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

## <a name="next-steps"></a>Kolejne kroki

 [Kanał 9: Usługa Azure AD i tożsamość show: Wersja zapoznawcza programu Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
