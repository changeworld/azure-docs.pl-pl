---
title: Omówienie monitorowania i zarządzania zabezpieczeń platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie funkcji zabezpieczeń i usług, które platforma Azure udostępnia ułatwiających zarządzanie i monitorowanie usług w chmurze Azure i maszyn wirtualnych.
services: security
documentationcenter: na
author: TerryLanfear
manager: StevenPo
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: terrylan
ms.openlocfilehash: 1e48131ff784ba5bb8d5a7dfffe8afb5ce8bb4c1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364373"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Omówienie monitorowania i zarządzania zabezpieczeń platformy Azure
Azure zapewnia mechanizmy zabezpieczeń ułatwiające zarządzanie i monitorowanie usług w chmurze Azure oraz maszynach wirtualnych (VM). W tym artykule omówiono te podstawowe funkcje zabezpieczeń i usług. Zostały podane linki do artykułów, które zapewniają informacje na temat każdej, aby dowiedzieć się więcej.

Zabezpieczenia usług w chmurze firmy Microsoft jest powiązanie i udostępnionych odpowiedzialność między Tobą a firmą Microsoft. Microsoft jest odpowiedzialny za platformy Azure i zabezpieczenia fizyczne jego centrów danych (przy użyciu ochrony zabezpieczeń, takich jak drzwi zablokowanym wpis wskaźnika, ogrodzenia i osłony). Platforma Azure udostępnia silne poziomów zabezpieczeń chmurze w warstwie oprogramowania, które zaspokoi potrzeby zabezpieczeń, prywatności i zgodności klientów.

Jesteś właścicielem danych i tożsamości, odpowiedzialność za ochrony ich zabezpieczeń zasobów lokalnych i bezpieczeństwa składniki chmury, nad którymi zarządzasz. Firma Microsoft daje opcji zabezpieczeń i możliwości, aby pomóc w ochronie danych oraz aplikacji. Twoje stopień odpowiedzialność za bezpieczeństwo jest oparty na typie usługi w chmurze.

Poniżej znajduje się podsumowanie saldo odpowiedzialność między firmą Microsoft a klienta.

![Wspólna odpowiedzialność][1]

Aby uzyskać więcej informacji dotyczących zarządzania zabezpieczeniami, zobacz [Zarządzanie zabezpieczeniami na platformie Azure](azure-security-management.md).

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
Kontroli dostępu opartej na rolach (RBAC) zapewnia zarządzanie szczegółowe dostępu do zasobów platformy Azure. Za pomocą RBAC, można przyznać osób tylko takiego dostępu, które są niezbędne do wykonywania swoich zadań. RBAC ułatwia również upewnij się, że gdy użytkownicy opuszczają organizację, utracą dostęp do zasobów w chmurze.

Więcej informacji:

* [Blog zespołu usługi Active Directory na RBAC](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
* [Kontrola dostępu oparta na rolach na platformie Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Oprogramowanie chroniące przed złośliwym kodem
Przy użyciu platformy Azure można użyć ochrony przed złośliwym oprogramowaniem z dostawców głównych zabezpieczeń, takich jak Microsoft, firmy Symantec, Trend Micro, McAfee i Kaspersky. To oprogramowanie chroni maszyny wirtualne z złośliwych plików, adware i innymi zagrożeniami.

Antimalware firmy Microsoft dla usług Azure Cloud Services i maszyn wirtualnych zapewnia możliwość zainstalowania agenta ochrony przed złośliwym kodem dla ról PaaS i maszyny wirtualne. W oparciu o System Center Endpoint Protection, ta funkcja przełącza sprawdzonych lokalnymi technologii zabezpieczeń w chmurze.

Oferujemy również głęboką integrację dla trendu [głębokie zabezpieczeń](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) i [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) produktów platformy Azure. Głębokie zabezpieczeń to rozwiązanie w zakresie oprogramowania antywirusowego i SecureCloud to rozwiązanie do szyfrowania. Głębokie zabezpieczeń jest wdrażany wewnątrz maszyn wirtualnych za pomocą modelu rozszerzenia. Przy użyciu interfejsu użytkownika portalu Azure i programu PowerShell, można używać bezpośrednich zabezpieczeń wewnątrz nowych maszyn wirtualnych, które są jest uruchomione lub istniejących maszyn wirtualnych, które są już wdrożone.

Symantec Endpoint Protection (wrz) jest również obsługiwany na platformie Azure. Dzięki integracji portalu możesz określić, czy zamierzasz używać wrz na maszynie Wirtualnej. WRZ można zainstalować w nowej maszyny Wirtualnej za pośrednictwem portalu Azure, lub można ją zainstalować na istniejącej maszyny Wirtualnej za pomocą programu PowerShell.

Więcej informacji:

* [Wdrażanie rozwiązań do ochrony przed złośliwym kodem na maszynach wirtualnych platformy Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Ochrony przed złośliwym oprogramowaniem firmy Microsoft dla usług w chmurze Azure i maszyn wirtualnych](azure-security-antimalware.md)
* [Jak zainstalować i skonfigurować Trend Micro głębokie zabezpieczeń jako usługa na maszynie Wirtualnej systemu Windows](../virtual-machines/windows/classic/install-trend.md)
* [Jak zainstalować i skonfigurować Symantec Endpoint Protection na maszynie Wirtualnej systemu Windows](../virtual-machines/windows/classic/install-symantec.md)
* [Nowe opcje ochrony przed złośliwym kodem ochrony maszyn wirtualnych platformy Azure](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Usługa Azure Multi-Factor Authentication to metoda uwierzytelniania, która wymaga użycia więcej niż jednej metody weryfikacji. Dodaje kluczową drugą warstwę zabezpieczeń do logowania użytkowników i transakcji. 

Usługi Multi-Factor Authentication ułatwia zabezpieczenie dostępu do danych i aplikacji, spełniając zapotrzebowanie na prosty proces logowania. Zapewnia silne uwierzytelnianie za pomocą zakresu opcji weryfikacji (połączenie telefoniczne, wiadomość tekstowa lub kodu weryfikacji lub powiadamiania aplikacji mobilnej) i tokenów OATH innej firmy.

Więcej informacji:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co to jest usługa Multi-Factor Authentication platformy Azure?](../active-directory/authentication/multi-factor-authentication.md)
* [Jak działa usługa Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute
Usługa Azure ExpressRoute umożliwia rozszerzanie sieci lokalnej w Microsoft Cloud przez dedykowane połączenie prywatne, które umożliwiają to dostawca połączenia. Z usługi ExpressRoute można ustanowić połączenia z usługami w chmurze Microsoft Azure, Office 365 i CRM Online. Łączność może pochodzić z:

- Dowolny z każdym sieci (IP sieci VPN).
- Sieć Ethernet point-to-point.
- Cross połączenia wirtualnego przez dostawcę łączność w zakładzie wspólnej lokalizacji. 

Połączenia ExpressRoute nie go za pośrednictwem publicznej sieci internet. Można oferują więcej niezawodności, szybkości szybsze, niższe opóźnienia i lepsze zabezpieczenia niż typowe połączenia za pośrednictwem Internetu.

Więcej informacji:

* [Opis techniczny ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Bramy sieci wirtualnej
Bramy sieci VPN, nazywany również bramy sieci wirtualnej platformy Azure, są używane do wysyłania ruchu sieciowego między sieciami wirtualnymi i lokalizacji lokalnej. Są one również używane do wysyłania ruchu między wieloma sieciami wirtualnymi w obrębie platformy Azure (sieci). Bramy sieci VPN zapewniają łączność bezpiecznego między lokalizacjami platformy Azure i infrastruktury.

Więcej informacji:

* [Temat bram sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Przegląd zabezpieczeń sieci platformy Azure](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
Czasami użytkownicy muszą wykonać operacje uprzywilejowane w zasobów platformy Azure lub innych aplikacji SaaS. Często oznacza to, że organizacjom nadanie im stałe uprzywilejowanego dostępu w usłudze Azure Active Directory (Azure AD). 

Jest to zagrożenie bezpieczeństwa rosnącym zasobów hostowanych w chmurze, ponieważ organizacje wystarczająco nie można monitorować, co robią tych użytkowników z ich uprzywilejowanego dostępu. Ponadto w przypadku złamania zabezpieczeń konta użytkownika z dostępem uprzywilejowanym tego naruszenia co może mieć wpływ na ogólne bezpieczeństwo chmury w organizacji. Azure AD Privileged Identity Management pomaga Rozwiąż to zagrożenie, co zmniejsza czas ekspozycji uprawnień i zwiększając wydajność wgląd w użycie.  

Zarządzanie tożsamościami uprzywilejowanymi pojęcia związane z tymczasowego administratora dla roli lub "just in time" dostępu administratora. Tego rodzaju administratora jest użytkownik, który musi ukończyć proces aktywacji dla przypisania roli. Proces aktywacji zmienia przypisanie użytkownika do roli w usłudze Azure AD z okresu nieaktywne na aktywny w wyznaczonym czasie.

Więcej informacji:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Wprowadzenie do usługi Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Identity Protection
Azure AD Identity Protection zawiera skonsolidowanego widoku podejrzanych działań logowania i potencjalnych luk w zabezpieczeniach w celu ochrony firmy. Identity Protection wykrywa podejrzane działania dla użytkowników i tożsamości uprzywilejowanych (Administrator), oparte na sygnały, takich jak:

- Ataków siłowych.
- Ujawnione poświadczenia.
- Logowania z nieznanych lokalizacji i zainfekowanych urządzeń.

Zapewniając powiadomień i zalecanych czynności naprawczych, Identity Protection pomaga ograniczyć ryzyko w czasie rzeczywistym. Obliczanie ważność ryzyka użytkownika. Można skonfigurować zasady oparte na ryzyko automatycznie ułatwia zabezpieczenie aplikacji dostępu przed zagrożeniami w przyszłości.

Więcej informacji:

* [Ochronę tożsamości usługi Azure Active Directory](../active-directory/active-directory-identityprotection.md)
* [Kanał 9: Usługi Azure AD i Pokaż tożsamości: Podgląd ochrony tożsamości](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center
Centrum zabezpieczeń Azure ułatwia zapobieganie, wykrywania i reagowania na zagrożenia. Centrum zabezpieczeń zapewnia należy zwiększyć widoczność i skuteczniejszą kontrolę zabezpieczeń zasobów platformy Azure. Zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure. Pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

Centrum zabezpieczeń ułatwia optymalizacji i monitorowania zabezpieczeń zasobów platformy Azure przez:

* Dzięki któremu można zdefiniować zasady dla zasobów subskrypcji platformy Azure zgodnie z tym:
  * W zakresie zabezpieczeń w firmie.
  * Typ aplikacji oraz poufności danych w każdej subskrypcji.
* Monitorowanie stanu sieci maszyn wirtualnych platformy Azure, sieci i aplikacji.
* Dostarcza listę alertów zabezpieczeń uporządkowanych według priorytetu, między innymi alertów ze zintegrowanych rozwiązań partnerskich. Zapewnia także informacje potrzebne do szybkiego analizowania ataku i zalecenia dotyczące sposobu jego rozwiązania.

Więcej informacji:

* [Wprowadzenie do usługi Azure Security Center](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
