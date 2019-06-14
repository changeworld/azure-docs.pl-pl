---
title: Jak skonfigurować zasady o podwyższonym ryzyku w usługi Azure Active Directory identity protection (odświeżane) | Dokumentacja firmy Microsoft
description: Jak skonfigurować zasady ryzykiem w usługi Azure Active Directory identity protection (odświeżane).
services: active-directory
keywords: Usługa Azure active directory identity protection odnajdywania aplikacji w chmurze, zarządzanie aplikacji, zabezpieczenia, ryzyka, poziom ryzyka, luk w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdacdf604ab7a4ded7ddf302a217084630f60b31
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60295797"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>Instrukcje: Konfigurowanie zasad usługi o podwyższonym ryzyku ochronę tożsamości usługi Azure Active Directory (odświeżyć)


Usługa Azure AD wykrywa zdarzenia o podwyższonym ryzyku, które z nich są wskaźnikami tożsamości mogą mieć złamane zabezpieczenia. Przez skonfigurowanie zasad o podwyższonym ryzyku, można zdefiniować automatycznych odpowiedzi z wynikami wykrywania:

- Za pomocą zasad ryzyka logowania można skonfigurować odpowiedzi na zdarzenia ryzyka w czasie rzeczywistym, które zostały wykryte podczas logowania użytkownika. 
- Zasady użytkownika o podwyższonym ryzyku można skonfigurować odpowiedzi na wszystkie ryzyka aktywnego użytkownika, które zostały wykryte dla użytkownika wraz z upływem czasu.  

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]


## <a name="what-is-the-sign-in-risk-policy"></a>Co to są zasady ryzyka logowania?

Usługa Azure AD analizuje każdego logowania użytkownika. Cel analizy jest wykrywa podejrzane akcje, które pochodzą wraz z logowania. Na przykład jest logowanie wykonywane przy użyciu anonimowego adresu IP lub jest logowanie inicjowane z nieznanej lokalizacji? W usłudze Azure AD podejrzane działania, które system może wykrywać znane są również jako zdarzeń o podwyższonym ryzyku. Oparte na zdarzenia o podwyższonym ryzyku, które zostały wykryte podczas logowania, usługi Azure AD pozwala obliczyć wartości. Wartość reprezentuje prawdopodobieństwo (niskiej, średniej, wysokiej), czy identyfikator logowania nie jest wykonywane przez wiarygodnego użytkownika. Prawdopodobieństwo, że jest wywoływana **poziom ryzyka logowania**.

Zasady ryzyka logowania to automatyczną odpowiedź, które można skonfigurować na poziomie określonej ryzyka logowania. W odpowiedzi możesz zablokować dostęp do zasobów lub wymagają, przekazując żądanie uwierzytelniania wieloskładnikowego (MFA) do uzyskania dostępu.

Gdy użytkownik pomyślnie ukończy wiersza MFA wyzwalanych przez zasady ryzyka logowania, udostępnia informacje zwrotne do usługi Identity Protection, która logowania pochodzi od wiarygodnego użytkownika. W związku z tym zdarzenie ryzyka logowania, które wiersza MFA zostaną automatycznie zamknięte, a Identity Protection uniemożliwi to zdarzenie mające wpływ na podniesienie poziomu ryzyka związanego z użytkownikiem. Włączenie zasad ryzyka logowania mogą zmniejszyć noisiness w widoku ryzykownych logowań, pozwalając użytkownikom na samodzielną korektę po wyświetleniu monitu o uwierzytelnianie wieloskładnikowe, a następnie automatycznie zamyka skojarzony ryzykowne logowania w.

## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Jak uzyskać dostęp do zasad ryzyka logowania?
   
Policy ryzyka logowania jest dostępna w **Konfiguruj** sekcji na [strony usługi Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zasady ryzyka logowania](./media/howto-configure-risk-policies/1014.png "zasad ryzyka logowania")


## <a name="sign-in-risk-policy-settings"></a>Ustawienia zasad ryzyka logowania

Po skonfigurowaniu zasad ryzyka logowania, należy ustawić:

- Użytkownicy i grupy, które zostaną zastosowane zasady:

    ![Użytkownicy i grupy](./media/howto-configure-risk-policies/11.png)

- Poziom ryzyka logowania, wyzwalającego zasad:

    ![Poziom ryzyka logowania](./media/howto-configure-risk-policies/12.png)

- Typ dostępu, który ma być wymuszane, gdy poziom ryzyka logowania zostaną spełnione:  

    ![Access](./media/howto-configure-risk-policies/13.png)

- Stan zasad:

    ![Wymuszanie zasad](./media/howto-configure-risk-policies/14.png)


W oknie dialogowym konfiguracji zasad zapewnia możliwość szacunkowa ocena wpływu ponownej konfiguracji.

![Szacowany wpływ](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>Co należy wiedzieć na temat zasad ryzyka logowania

Można skonfigurować zasady zabezpieczeń ryzyka logowania, które wymagają usługi MFA:

![Wymaganie usługi MFA](./media/howto-configure-risk-policies/16.png)

Jednak ze względu na bezpieczeństwo, to ustawienie działa tylko dla użytkowników, które zostały już zarejestrowane dla usługi MFA. Ochronę tożsamości blokuje użytkowników za pomocą wymaganie uwierzytelniania Wieloskładnikowego, jeśli ich nie są zarejestrowani do uwierzytelniania Wieloskładnikowego jeszcze.

Jeśli chcesz pozyskać usługę MFA dla ryzykownych logowań, należy:

1. Włącz zasady rejestracji uwierzytelniania wieloskładnikowego dla użytkowników, których to dotyczy.

2. Wymaga odpowiednich użytkowników do logowania w sesji — ryzykowne do przeprowadzenia rejestracji usługi MFA.

Wykonanie tych kroków gwarantuje, że to uwierzytelnianie wieloskładnikowe jest wymagany do ryzykowne logowania.

Zasady ryzyka logowania to:

- Stosowane do całego ruchu w przeglądarce i logowania korzystające z nowoczesnego uwierzytelniania.

- Nie są stosowane do aplikacji przy użyciu starszych protokołów zabezpieczeń przez wyłączenie punktu końcowego protokołu WS-Trust u dostawcy tożsamości federacyjnych, takich jak usługi AD FS.


Omówienie środowiska użytkownika Zobacz:

* [Ryzykowne logowania odzyskiwania](flows.md#risky-sign-in-recovery)
* [Ryzykowne logowanie zablokowane](flows.md#risky-sign-in-blocked)  
* [Środowisko logowania za pomocą usługi Azure AD Identity Protection](flows.md)  









## <a name="what-is-a-user-risk-policy"></a>Co to jest zasad ryzyka dla użytkownika?

Usługa Azure AD analizuje każdego logowania użytkownika. Cel analizy jest wykrywa podejrzane akcje, które pochodzą wraz z logowania. W usłudze Azure AD podejrzane działania, które system może wykrywać znane są również jako zdarzeń o podwyższonym ryzyku. Podczas gdy pewne ryzyko zdarzeń może zostać wykryte w czasie rzeczywistym, chociaż są również zdarzeń o podwyższonym ryzyku wymagające jeszcze raz. Na przykład aby wykryć niemożliwa podróż do nietypowych lokalizacji, systemu wymaga wstępny okres uczenia wynoszący 14 dni, aby dowiedzieć się więcej na temat regularnych zachowania użytkownika. Dostępnych jest kilka opcji, które można rozpoznać zdarzenia ryzyka wykryte. Na przykład można rozwiązać zdarzenia o podwyższonym ryzyku w poszczególnych ręcznie lub ich rozwiązać za pomocą ryzyka logowania lub zasady dostępu warunkowego ryzyka użytkownika.

Wszystkie zdarzenia o podwyższonym ryzyku, które zostały wykryte dla użytkownika i nie można rozwiązać, są znane jako zdarzenia aktywnego ryzyka. Zdarzenia aktywnego ryzyka, które są skojarzone z użytkownikami są nazywane ryzyka związanego z użytkownikiem. Usługi Azure AD, oparte na ryzyko związane z użytkownikiem, oblicza prawdopodobieństwo (niskiej, średniej, wysokiej), że użytkownik został złamany. Prawdopodobieństwo, że jest nazywany poziomie ryzyka użytkownika.

![Ryzyka użytkownika](./media/howto-configure-risk-policies/11031.png)

Zasady ryzyka użytkownika jest automatycznej odpowiedzi skonfigurowanych dla określonego użytkownika poziom ryzyka. Za pomocą zasad ryzyka użytkownika można zablokować dostęp do zasobów lub wymagać zmiany hasła dostępu do stanu czystego konta użytkownika.


## <a name="how-do-i-access-the-user-risk-policy"></a>Jak uzyskać dostęp do zasad ryzyka użytkownika?
   
Zasady ryzyka użytkownika znajduje się w **Konfiguruj** sekcji na [strony usługi Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zasady dotyczące ryzyka związanego z użytkownika](./media/howto-configure-risk-policies/11014.png)



## <a name="user-risk-policy-settings"></a>Ustawienia zasad ryzyka dla użytkownika

Po skonfigurowaniu zasad ryzyka użytkownika, musisz ustawić:

- Użytkownicy i grupy, które zostaną zastosowane zasady:

    ![Użytkownicy i grupy](./media/howto-configure-risk-policies/111.png)

- Poziom ryzyka logowania, wyzwalającego zasad:

    ![Poziom ryzyka użytkownika](./media/howto-configure-risk-policies/112.png)

- Typ dostępu, który ma być wymuszane, gdy poziom ryzyka logowania zostaną spełnione:  

    ![Access](./media/howto-configure-risk-policies/113.png)

- Stan zasad:

    ![Wymuszanie zasad](./media/howto-configure-risk-policies/114.png)

W oknie dialogowym konfiguracji zasad zapewnia możliwość szacunkowa ocena wpływu konfigurację.

![Szacowany wpływ](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>Co należy wiedzieć o ryzyka związanego z użytkownikiem zasady

Zasady zabezpieczeń ryzyka użytkownika można ustawić w celu zablokowania użytkowników podczas logowania się w zależności od poziomu zagrożenia.

![Blokowanie](./media/howto-configure-risk-policies/116.png)


Blokowania logowania:

* Zapobiega generowania nowego użytkownika zdarzeń o podwyższonym ryzyku dla użytkownika, którego dotyczy
* Administratorzy mogą ręcznie korygowania zdarzeń o podwyższonym ryzyku wpływających na tożsamości użytkownika i przywracania go w bezpiecznym stanu


























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

 [Witryna Channel 9: Usługa Azure AD i wyświetlanie tożsamości: Identity Protection w wersji zapoznawczej](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

