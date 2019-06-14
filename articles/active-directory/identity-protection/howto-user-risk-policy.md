---
title: Sposób konfigurowania zasad ryzyka użytkownika w usłudze Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować zasady usługi Azure AD Identity Protection ryzyka użytkownika.
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
ms.openlocfilehash: b94e9b7267c956c07f4171f8cce46c6159affd90
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60459787"
---
# <a name="how-to-configure-the-user-risk-policy"></a>Instrukcje: Konfigurowanie zasad ryzyka użytkowników

Za pomocą ryzyka związanego z użytkownikiem usługi Azure AD wykrywa prawdopodobieństwo, że konto użytkownika zostało naruszone. Jako administrator można skonfigurować zasady dostępu warunkowego ryzyka użytkownika, automatycznie odpowiadać na poziom ryzyka określonego użytkownika.
 
Ten artykuł zawiera informacje potrzebne do skonfigurowania zasad ryzyka dla użytkownika.


## <a name="what-is-a-user-risk-policy"></a>Co to jest zasad ryzyka dla użytkownika?

Usługa Azure AD analizuje każdego logowania użytkownika. Cel analizy jest wykrywa podejrzane akcje, które pochodzą wraz z logowania. W usłudze Azure AD podejrzane działania, które system może wykrywać znane są również jako zdarzeń o podwyższonym ryzyku. Podczas gdy pewne ryzyko zdarzeń może zostać wykryte w czasie rzeczywistym, chociaż są również zdarzeń o podwyższonym ryzyku wymagające jeszcze raz. Na przykład aby wykryć niemożliwa podróż do nietypowych lokalizacji, systemu wymaga wstępny okres uczenia wynoszący 14 dni, aby dowiedzieć się więcej na temat regularnych zachowania użytkownika. Dostępnych jest kilka opcji, które można rozpoznać zdarzenia ryzyka wykryte. Na przykład można rozwiązać zdarzenia o podwyższonym ryzyku w poszczególnych ręcznie lub ich rozwiązać za pomocą ryzyka logowania lub zasady dostępu warunkowego ryzyka użytkownika.

Wszystkie zdarzenia o podwyższonym ryzyku, które zostały wykryte dla użytkownika i nie można rozwiązać, są znane jako zdarzenia aktywnego ryzyka. Zdarzenia aktywnego ryzyka, które są skojarzone z użytkownikami są nazywane ryzyka związanego z użytkownikiem. Usługi Azure AD, oparte na ryzyko związane z użytkownikiem, oblicza prawdopodobieństwo (niskiej, średniej, wysokiej), że użytkownik został złamany. Prawdopodobieństwo, że jest nazywany poziomie ryzyka użytkownika.

![Ryzyka użytkownika](./media/howto-user-risk-policy/1031.png)

Zasady ryzyka użytkownika jest automatycznej odpowiedzi skonfigurowanych dla określonego użytkownika poziom ryzyka. Za pomocą zasad ryzyka użytkownika można zablokować dostęp do zasobów lub wymagać zmiany hasła dostępu do stanu czystego konta użytkownika.


## <a name="how-do-i-access-the-user-risk-policy"></a>Jak uzyskać dostęp do zasad ryzyka użytkownika?
   
Policy ryzyka logowania jest dostępna w **Konfiguruj** sekcji na [strony usługi Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zasady dotyczące ryzyka związanego z użytkownika](./media/howto-user-risk-policy/1014.png)



## <a name="policy-settings"></a>Ustawienia zasad

Po skonfigurowaniu zasad ryzyka logowania, należy ustawić:

- Użytkownicy i grupy, które zostaną zastosowane zasady:

    ![Użytkownicy i grupy](./media/howto-user-risk-policy/11.png)

- Poziom ryzyka logowania, wyzwalającego zasad:

    ![Poziom ryzyka użytkownika](./media/howto-user-risk-policy/12.png)

- Typ dostępu, który ma być wymuszane, gdy poziom ryzyka logowania zostaną spełnione:  

    ![Access](./media/howto-user-risk-policy/13.png)

- Stan zasad:

    ![Wymuszanie zasad](./media/howto-user-risk-policy/14.png)

W oknie dialogowym konfiguracji zasad zapewnia możliwość szacunkowa ocena wpływu konfigurację.

![Szacowany wpływ](./media/howto-user-risk-policy/15.png)

## <a name="what-you-should-know"></a>Co należy wiedzieć

Zasady zabezpieczeń ryzyka użytkownika można ustawić w celu zablokowania użytkowników podczas logowania się w zależności od poziomu zagrożenia.

![Blokowanie](./media/howto-user-risk-policy/16.png)


Blokowania logowania:

* Zapobiega generowania nowego użytkownika zdarzeń o podwyższonym ryzyku dla użytkownika, którego dotyczy
* Administratorzy mogą ręcznie korygowania zdarzeń o podwyższonym ryzyku wpływających na tożsamości użytkownika i przywracania go w bezpiecznym stanu

## <a name="best-practices"></a>Najlepsze praktyki

Wybieranie **wysokiej** próg zmniejsza liczbę razy, zasada zostanie wyzwolony i minimalizuje wpływ na użytkowników.
Jednakże nie obejmuje **niski** i **średni** użytkowników generujących ryzyko związane z zasad, które mogą nie zapewnić tożsamości lub urządzeń, zostały wcześniej podejrzenie lub znane naruszenia.

Podczas ustawiania zasad

* Wyklucz użytkowników, którzy mogą wygenerować dużą liczbę fałszywych alarmów (deweloperzy, analityków zabezpieczeń)
* Wyklucz użytkowników w lokalizacjach, w którym włączenie zasad nie jest możliwe (na przykład brak dostępu do działu pomocy technicznej)
* Użyj **wysokiej** próg podczas wdrażania zasad początkowej, czy należy zminimalizować wyzwania widoczne dla użytkowników końcowych.
* Użyj **niski** próg, jeśli Twoja organizacja wymaga zwiększenia bezpieczeństwa. Wybieranie **niski** próg wprowadza dodatkowego użytkownika logowania wyzwania, ale wyższy poziom bezpieczeństwa.

Zalecana domyślna w przypadku większości organizacji jest skonfigurowanie reguły dla **średni** próg, aby zachować równowagę pomiędzy użyteczność i zabezpieczeń.

Omówienie środowiska użytkownika Zobacz:

* [Naruszenia zabezpieczeń konta odzyskiwania przepływ](flows.md#compromised-account-recovery).  
* [Naruszone zablokowano konto przepływu](flows.md#compromised-account-blocked).  

**Aby otworzyć okno dialogowe związanej z nimi konfiguracji**:

- Na **usługi Azure AD Identity Protection** bloku, w **Konfiguruj** kliknij **zasad ryzyka dla użytkownika**.

    ![Zasady dotyczące ryzyka związanego z użytkownika](./media/howto-user-risk-policy/1009.png "zasad ryzyka dla użytkownika")




## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać omówienie programu Azure AD Identity Protection, zobacz [Omówienie usługi Azure AD Identity Protection](overview.md).
