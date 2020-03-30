---
title: Wersje uwierzytelniania wieloskładnikowego platformy Azure i plany zużycia
description: Dowiedz się więcej o kliencie uwierzytelniania wieloskładnikowego platformy Azure oraz o różnych dostępnych metodach i wersjach.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e74a7ab0c003aaf9d90211484b39f8322cd9c329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648006"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Funkcje i licencje na uwierzytelnianie wieloskładnikowe platformy Azure

Aby chronić konta użytkowników w organizacji, należy użyć uwierzytelniania wieloskładnikowego. Ta funkcja jest szczególnie ważna dla kont, które mają uprzywilejowany dostęp do zasobów. Podstawowe funkcje uwierzytelniania wieloskładnikowego są dostępne dla administratorów usługi Office 365 i usługi Azure Active Directory (Azure AD) bez dodatkowych kosztów. Jeśli chcesz uaktualnić funkcje dla administratorów lub rozszerzyć uwierzytelnianie wieloskładnikowe na pozostałych użytkowników, możesz zakupić uwierzytelnianie wieloskładnikowe platformy Azure na kilka sposobów.

> [!IMPORTANT]
> W tym artykule opisano różne sposoby licencjonowannia i użycia uwierzytelniania wieloskładnikowego platformy Azure. Szczegółowe informacje na temat cen i rozliczeń można znaleźć na [stronie cennika uwierzytelniania wieloskładnikowego platformy Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Dostępne wersje uwierzytelniania wieloskładnikowego platformy Azure

Uwierzytelnianie wieloskładnikowe platformy Azure może być używane i licencjonowane na kilka różnych sposobów, w zależności od potrzeb organizacji. Użytkownik może już mieć prawo do korzystania z usługi Azure Multi-Factor Authentication w zależności od aktualnej licencji usługi Azure AD, Office 365, EMS lub Microsoft 365. W poniższej tabeli opisano różne sposoby uzyskania uwierzytelniania wieloskładnikowego platformy Azure oraz niektóre funkcje i przypadki użycia dla każdego z nich.

| Jeśli jesteś użytkownikiem | Możliwości i przypadki użycia |
| --- | --- |
| EMS lub Microsoft 365 E3 i E5 | EMS E3 lub Microsoft 365 E3 (w tym EMS i Office 365), obejmuje usługę Azure AD Premium P1. Usługa EMS E5 lub Microsoft 365 E5 obejmuje usługę Azure AD Premium P2. Można użyć tych samych funkcji dostępu warunkowego, które zostały odnotowane w poniższych sekcjach, aby zapewnić użytkownikom uwierzytelnianie wieloskładnikowe. |
| Usługa Azure AD — warstwa Premium P1 | Za pomocą [usługi Azure AD Dostęp warunkowy](../conditional-access/overview.md) monituje użytkowników o uwierzytelnianie wieloskładnikowe podczas niektórych scenariuszy lub zdarzeń, aby dopasować je do wymagań biznesowych. |
| Usługa Azure AD — warstwa Premium P2 | Zapewnia najsilniejszą pozycję zabezpieczeń i lepsze środowisko użytkownika. Dodaje [oparty na ryzyku dostęp warunkowy](../conditional-access/howto-conditional-access-policy-risk.md) do funkcji usługi Azure AD Premium P1, która dostosowuje się do wzorców użytkownika i minimalizuje monity o uwierzytelnianie wieloskładnikowe. |
| Usługa Office 365 Business Premium, E3 lub E5 | Uwierzytelnianie wieloskładnikowe platformy Azure jest włączone lub wyłączone dla wszystkich użytkowników dla wszystkich zdarzeń logowania. Nie ma możliwości włączania tylko uwierzytelniania wieloskładnikowego dla podzbioru użytkowników lub tylko w niektórych scenariuszach. Zarządzanie odbywa się za pośrednictwem portalu usługi Office 365. Aby uzyskać lepsze środowisko użytkownika, uaktualnij do usługi Azure AD Premium P1 lub P2 i użyj dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [bezpieczne zasoby usługi Office 365 z uwierzytelnianiem wieloskładnikowym](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). |
| Usługa Azure AD — warstwa Bezpłatna | [Domyślne zabezpieczenia](../fundamentals/concept-fundamentals-security-defaults.md) można użyć, aby włączyć uwierzytelnianie wieloskładnikowe dla wszystkich użytkowników, za każdym razem, gdy jest składane żądanie uwierzytelniania. Nie masz szczegółowej kontroli włączonych użytkowników lub scenariuszy, ale zapewnia ten dodatkowy krok zabezpieczeń.<br /> Nawet wtedy, gdy domyślne zabezpieczenia nie są używane do włączania uwierzytelniania wieloskładnikowego dla wszystkich, użytkownicy przypisani do roli *administratora globalnego usługi Azure AD* można skonfigurować do używania uwierzytelniania wieloskładnikowego. Ta funkcja warstwy bezpłatnej zapewnia ochronę krytycznych kont administratorów przez uwierzytelnianie wieloskładnikowe. |

## <a name="feature-comparison-of-versions"></a>Porównanie funkcji wersji

Poniższa tabela zawiera listę funkcji, które są dostępne w różnych wersjach uwierzytelniania wieloskładnikowego platformy Azure. Zaplanuj swoje potrzeby w zakresie zabezpieczania uwierzytelniania użytkowników, a następnie określ, które podejście spełnia te wymagania. Na przykład chociaż usługa Azure AD Free zapewnia domyślne zabezpieczenia, które zapewniają uwierzytelnianie wieloskładnikowe platformy Azure, tylko aplikacja mobile authenticator może służyć do monitu o uwierzytelnienie, a nie połączenia telefonicznego lub wiadomości SMS. Takie podejście może być ograniczeniem, jeśli nie można upewnić się, że aplikacja uwierzytelniania mobilnego jest zainstalowana na urządzeniu osobistym użytkownika.

| Funkcja | Bezpłatna usługa Azure AD — domyślne ustawienia zabezpieczeń | Bezpłatna usługa Azure AD — globalni administratorzy usługi Azure AD | Usługa Office 365 Business Premium, E3 lub E5 | Usługa Azure AD Premium P1 lub P2 |
| --- |:---:|:---:|:---:|:---:|
| Ochrona kont administratorów dzierżawy usługi Azure AD za pomocą usługi MFA | ● | ● (tylko konta*administratora globalnego usługi Azure AD)* | ● | ● |
| Aplikacja mobilna jako drugi czynnik | ● | ● | ● | ● |
| Rozmowa telefoniczna jako drugi czynnik | | ● | ● | ● |
| SMS jako drugi czynnik | | ● | ● | ● |
| Kontrola administracyjna nad metodami weryfikacji | | ● | ● | ● |
| Alert dotyczący wykrycia oszustwa | | | | ● |
| Raporty usługi MFA | | | | ● |
| Niestandardowe powitania dla połączeń telefonicznych | | | | ● |
| Niestandardowy identyfikator rozmówcy dla połączeń telefonicznych | | | | ● |
| Zaufane adresy IP | | | | ● |
| Pamiętanie uwierzytelniania MFA w przypadku zaufanych urządzeń | | ● | ● | ● |
| Usługa MFA do zastosowań lokalnych | | | | ● |

> [!IMPORTANT]
> Od marca 2019 r. opcje połączeń telefonicznych nie są już dostępne dla użytkowników uwierzytelniania wieloskładnikowego platformy Azure i samoobsługowego resetowania haseł platformy Azure w dzierżawach bez użycia usługi Azure / próbnych. Zmiana ta nie ma wpływu na wiadomości SMS. Połączenia telefoniczne są nadal dostępne dla użytkowników w dzierżawach usługi Azure AD Premium P1 lub P2 lub użyciach usługi Office 365 Business Premium, E3 lub E5.

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Kupowanie i włączanie uwierzytelniania wieloskładnikowego platformy Azure

Aby korzystać z uwierzytelniania wieloskładnikowego platformy Azure, zarejestruj się lub kup kwalifikującą się warstwę usługi Azure AD. Usługa Azure AD jest dostępna w czterech wersjach — bezpłatnej, usługi Office 365 w wersji (dla klientów usługi Office 365 Business Premium E3 lub E5), Premium P1 i Premium P2.

Bezpłatna wersja jest dołączona do subskrypcji platformy Azure. Zobacz [sekcję poniżej,](#azure-ad-free-tier) aby uzyskać informacje na temat używania ustawień domyślnych zabezpieczeń lub ochrony kont za pomocą roli *administratora globalnego usługi Azure AD.*

Wersje Usługi Azure AD Premium są dostępne za pośrednictwem przedstawiciela firmy Microsoft, [programu licencji zbiorczej open](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)oraz programu Dostawców [rozwiązań w chmurze.](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409) Subskrybenci platformy Azure i usługi Office 365 mogą również kupować usługi Azure Active Directory Premium P1 i P2 w trybie online. [Zaloguj się](https://portal.office.com/Commerce/Catalog.aspx) do zakupu.

> [!IMPORTANT]
> Licencjonowanie oparte na zużyciu nie jest już dostępne dla nowych klientów od 1 września 2018 r. Istniejący klienci korzystający z modelu opartego na zużyciu mogą nadal używać na włączonego użytkownika lub na rozliczanie uwierzytelniania.

Po zakupie wymaganej warstwy usługi Azure AD [zaplanuj i wdrażaj uwierzytelnianie wieloskładnikowe platformy Azure.](howto-mfa-getstarted.md)

### <a name="azure-ad-free-tier"></a>Warstwa bezpłatna usługi Azure AD

Wszyscy użytkownicy w dzierżawie bezpłatnej usługi Azure AD mogą korzystać z uwierzytelniania azure multi-factor za pomocą ustawień domyślnych zabezpieczeń. Te domyślne zabezpieczenia umożliwiają uwierzytelnianie azure multi-factor dla wszystkich użytkowników, za każdym razem, gdy się logują. Aplikacja uwierzytelniania mobilnego jest jedyną metodą, która może służyć do uwierzytelniania wieloskładnikowego platformy Azure podczas korzystania z domyślnych zabezpieczeń usługi Azure AD Free.

* [Dowiedz się więcej o ustawieniach domyślnych zabezpieczeń usługi Azure AD](../fundamentals/concept-fundamentals-security-defaults.md)
* [Włączanie ustawień domyślnych zabezpieczeń dla użytkowników w usłudze Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Jeśli nie chcesz włączać uwierzytelniania wieloskładnikowego platformy Azure dla wszystkich użytkowników i każdego zdarzenia logowania, możesz zamiast tego chronić tylko konta użytkowników za pomocą roli *administratora globalnego usługi Azure AD.* Takie podejście zapewnia dodatkowe monity o uwierzytelnianie dla krytycznych kont administratora. Włącz uwierzytelnianie wieloskładnikowe platformy Azure w jeden z następujących sposobów, w zależności od typu używanego konta:

* Jeśli używasz konta Microsoft, zarejestruj się w [celu uwierzytelniania wieloskładnikowego](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Jeśli nie używasz konta Microsoft, [włącz uwierzytelnianie wieloskładnikowe dla użytkownika lub grupy w usłudze Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat kosztów, zobacz [Ceny uwierzytelniania wieloskładnikowego platformy Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
