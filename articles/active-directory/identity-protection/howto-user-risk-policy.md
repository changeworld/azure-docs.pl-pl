---
title: Jak skonfigurować zasady ryzyka dla użytkowników w Azure Active Directory Identity Protection | Microsoft Docs
description: Dowiedz się, jak skonfigurować zasady ryzyka dla Azure AD Identity Protection użytkownika.
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
ms.openlocfilehash: 92bfb921833d99a3538ffa8c4c5d16a9f0cd3acd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126282"
---
# <a name="how-to-configure-the-user-risk-policy"></a>Instrukcje: Konfigurowanie zasad ryzyka użytkowników

Przy ryzyku użytkownika usługa Azure AD wykrywa prawdopodobieństwo naruszenia bezpieczeństwa konta użytkownika. Jako administrator możesz skonfigurować zasady dostępu warunkowego dotyczące ryzyka użytkownika, aby automatycznie reagować na określony poziom ryzyka użytkownika.
 
Ten artykuł zawiera informacje potrzebne do skonfigurowania zasad ryzyka dla użytkowników.

## <a name="what-is-a-user-risk-policy"></a>Co to są zasady ryzyka dla użytkowników?

Usługa Azure AD analizuje każdy zalogowanie użytkownika. Celem analizy jest wykrycie podejrzanych działań, które są związane z logowaniem. W usłudze Azure AD podejrzane działania wykrywane przez system są również znane jako wykrywanie zagrożeń. Niektóre wykryte zagrożenia mogą być wykrywane w czasie rzeczywistym, ale również wykrycia ryzyka wymagają więcej czasu. Na przykład w celu wykrycia niemożliwej podróży do nietypowych lokalizacji system wymaga początkowego okresu szkoleniowego 14 dni, aby poznać jego zwykłe zachowanie. Istnieje kilka opcji rozwiązywania wykrytych wykrywania zagrożeń. Można na przykład ręcznie rozwiązać poszczególne wykrycia ryzyka lub można je rozwiązać przy użyciu ryzyka związanego z logowaniem lub zasad dostępu warunkowego do ryzyka użytkownika.

Wszystkie wykrycia ryzyka, które zostały wykryte dla użytkownika i nie zostały rozpoznane, są znane jako aktywne wykrycia ryzyka. Aktywne wykrywania ryzyka, które są skojarzone z użytkownikiem, są nazywane ryzykiem użytkownika. Na podstawie ryzyka związanego z użytkownikiem usługa Azure AD oblicza prawdopodobieństwo naruszenia bezpieczeństwa użytkownika. Prawdopodobieństwo jest nazywane poziomem ryzyka użytkownika.

![Czynniki ryzyka użytkownika](./media/howto-user-risk-policy/1031.png)

Zasady ryzyka dla użytkowników to zautomatyzowana odpowiedź, którą można skonfigurować dla określonego poziomu ryzyka użytkownika. Korzystając z zasad ryzyka dla użytkowników, można zablokować dostęp do zasobów lub wymagać zmiany hasła w celu przywrócenia stanu konta użytkownika.

## <a name="how-do-i-access-the-user-risk-policy"></a>Jak mogę uzyskać dostęp do zasad ryzyka dla użytkowników?
   
Zasady dotyczące ryzyka związanego z logowaniem znajduje się w sekcji **Konfigurowanie** na [stronie Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zasady dotyczące ryzyka związanego z użytkownikiem](./media/howto-user-risk-policy/1014.png)

## <a name="policy-settings"></a>Ustawienia zasad

Konfigurując zasady dotyczące ryzyka związanego z logowaniem, należy ustawić:

- Użytkownicy i grupy, których dotyczą zasady:

    ![Użytkownicy i grupy](./media/howto-user-risk-policy/11.png)

- Poziom ryzyka logowania, który wyzwala zasady:

    ![Poziom ryzyka użytkownika](./media/howto-user-risk-policy/12.png)

- Typ dostępu, który ma zostać wymuszony po spełnieniu poziomu ryzyka związanego z logowaniem:  

    ![Access](./media/howto-user-risk-policy/13.png)

- Stan zasad:

    ![Wymuś zasady](./media/howto-user-risk-policy/14.png)

W oknie dialogowym Konfiguracja zasad dostępna jest opcja oszacowania wpływu konfiguracji.

![Szacowany wpływ](./media/howto-user-risk-policy/15.png)

## <a name="what-you-should-know"></a>Co należy wiedzieć

Można ustawić zasady zabezpieczeń ryzyka dla użytkowników, aby blokować użytkownikom logowanie się w zależności od poziomu ryzyka.

![Blokowanie](./media/howto-user-risk-policy/16.png)

Blokowanie logowania:

* Zapobiega generowaniu nowych wykrycia ryzyka użytkownika dla danego użytkownika
* Umożliwia administratorom ręczne korygowanie wykrywania ryzyka, które mają wpływ na tożsamość użytkownika, i przywrócenie go do stanu bezpiecznego

## <a name="best-practices"></a>Najlepsze praktyki

Wybranie **wysokiego** progu zmniejsza liczbę wyzwalanych zasad i minimalizuje wpływ na użytkowników.
Jednak nie obejmuje **małych** i **średnich** użytkowników oflagowanych z powodu ryzyka z zasad, które mogą nie zabezpieczać tożsamości lub urządzeń, które zostały wcześniej podejrzane lub uznane za zagrożone.

Podczas ustawiania zasad,

* Wyklucz użytkowników, którzy mogą generować wiele fałszywych (dla deweloperów, analityków zabezpieczeń)
* Wykluczanie użytkowników w lokalizacjach lokalnych, w których włączenie zasad nie jest praktyczne (na przykład brak dostępu do pomocy technicznej)
* Podczas wstępnego wypełniania zasad należy używać **wysokiego** progu lub w przypadku konieczności minimalizowania wyzwań widzianych przez użytkowników końcowych.
* Jeśli organizacja wymaga większego **poziomu** zabezpieczeń, należy użyć niskiego progu. Wybranie **niskiego** progu powoduje wprowadzenie dodatkowych wyzwań związanych z logowaniem użytkowników, ale zwiększenie bezpieczeństwa.

Zalecanym ustawieniem domyślnym w przypadku większości organizacji jest skonfigurowanie reguły dla **średniego** progu w celu zrównoważenia równowagi między użytecznością a bezpieczeństwem.

Aby zapoznać się z omówieniem środowiska użytkownika powiązanego, zobacz:

* [Przepływ odzyskiwania z naruszeniem konta](flows.md#compromised-account-recovery).  
* [Zablokowany przepływ na koncie z naruszeniem zabezpieczeń](flows.md#compromised-account-blocked).  

**Aby otworzyć okno dialogowe powiązana konfiguracja**:

- W bloku **Azure AD Identity Protection** w sekcji **Konfiguracja** kliknij pozycję **zasady ryzyka użytkownika**.

    ![Zasady ryzyka dla użytkowników](./media/howto-user-risk-policy/1009.png "Zasady ryzyka dla użytkowników")

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem Azure AD Identity Protection, zobacz [omówienie Azure AD Identity Protection](overview.md).
