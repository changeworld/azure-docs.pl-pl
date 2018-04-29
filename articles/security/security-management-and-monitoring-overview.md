---
title: Zabezpieczeń platformy Azure, zarządzania i monitorowania — omówienie | Dokumentacja firmy Microsoft
description: " Azure zapewnia mechanizmy zabezpieczeń ułatwiające zarządzanie i monitorowanie usług w chmurze Azure i maszyn wirtualnych.  W tym artykule omówiono te podstawowe funkcje zabezpieczeń i usług. "
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
ms.openlocfilehash: 66a3a31574778bb1238498b523d51dc0d32ac22f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="azure-security-management-and-monitoring-overview"></a>Zabezpieczeń platformy Azure, zarządzania i monitorowania — omówienie
Azure zapewnia mechanizmy zabezpieczeń ułatwiające zarządzanie i monitorowanie usług w chmurze Azure i maszyn wirtualnych. W tym artykule omówiono te podstawowe funkcje zabezpieczeń i usług. Zostały podane linki do artykułów, które zapewniają informacje na temat każdej, aby dowiedzieć się więcej.

Zabezpieczenia usług w chmurze firmy Microsoft jest powiązanie i udostępnionych odpowiedzialność między Tobą a firmą Microsoft. Odpowiedzialność udostępnionego oznacza, że firmy Microsoft jest odpowiedzialny za Microsoft Azure i zabezpieczenia fizyczne centrów danych (przy użyciu ochrony zabezpieczeń, takich jak zablokowanym wskaźnika wejścia drzwi, ogrodzenia i osłony). Ponadto platforma Azure udostępnia silne poziomów zabezpieczeń chmurze w warstwie oprogramowania, które zaspokoi potrzeby zabezpieczeń, prywatności i zgodności wymagających klientów.

Jesteś właścicielem danych i tożsamości, odpowiedzialność za ochrony ich zabezpieczeń zasobów lokalnych i bezpieczeństwa składniki chmury, nad którymi zarządzasz. Firma Microsoft udostępnia opcje zabezpieczeń i możliwości, aby pomóc w ochronie danych oraz aplikacji. Twoje stopień odpowiedzialność za bezpieczeństwo jest oparty na typie usługi w chmurze.

Poniżej przedstawiono saldo odpowiedzialność za zarówno klient, jak i Microsoft.

![Wspólna odpowiedzialność][1]

Aby uzyskać bardziej zgłębić temat do zarządzania zabezpieczeniami, zobacz [Zarządzanie zabezpieczeniami na platformie Azure](azure-security-management.md).

Poniżej przedstawiono podstawowe funkcje powinny być zawarte w tym artykule:

* Kontrola dostępu oparta na rolach
* Oprogramowanie chroniące przed złośliwym kodem
* Multi-Factor Authentication
* ExpressRoute
* Bramy sieci wirtualnej
* Zarządzanie tożsamościami uprzywilejowanymi
* Ochrona tożsamości
* Security Center

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
Kontrola dostępu oparta na rolach (RBAC) umożliwia precyzyjne zarządzanie dostępem do zasobów platformy Azure. Przy użyciu funkcji RBAC, można przyznać osób tylko takiego dostępu, które są niezbędne do wykonywania swoich zadań.  RBAC ułatwia również upewnij się, że gdy użytkownicy opuszczają organizację utracą dostęp do zasobów w chmurze.

Więcej informacji:

* [Blog zespołu usługi Active Directory na RBAC](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
* [Kontrola dostępu oparta na rolach na platformie Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Oprogramowanie chroniące przed złośliwym kodem
Przy użyciu platformy Azure ochrony przed złośliwym oprogramowaniem z dostawców głównych zabezpieczeń, takich jak Microsoft, firmy Symantec, Trend Micro, McAfee i Kaspersky służy do ochrony maszyn wirtualnych z złośliwych plików, adware i innymi zagrożeniami.

Microsoft Antimalware oferuje możliwość zainstalowania agenta ochrony przed złośliwym kodem dla ról PaaS i maszyny wirtualne. W oparciu o System Center Endpoint Protection, ta funkcja przełącza sprawdzonych lokalnymi technologii zabezpieczeń w chmurze.

Oferujemy również głęboką integrację dla trendu [głębokie zabezpieczeń](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ i [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ produktów platformy Azure. DeepSecurity to rozwiązanie w zakresie oprogramowania antywirusowego i SecureCloud to rozwiązanie do szyfrowania. DeepSecurity jest wdrażany wewnątrz maszyn wirtualnych przy użyciu modelu rozszerzenia. Przy użyciu interfejsu użytkownika portalu i programu PowerShell, można użyć DeepSecurity wewnątrz nowych maszyn wirtualnych, które są jest uruchomione lub istniejących maszyn wirtualnych, które są już wdrożone.

Symantec ochrony punktu końcowego (wrz) jest również obsługiwany na platformie Azure. Dzięki integracji portalu klientów można określić zamierzają użyć wrz w maszynie Wirtualnej. WRZ można zainstalować na maszynie Wirtualnej nowego portalu Azure lub można ją zainstalować na istniejącej maszyny Wirtualnej przy użyciu programu PowerShell.

Więcej informacji:

* [Wdrażanie rozwiązań do ochrony przed złośliwym kodem na maszynach wirtualnych platformy Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Ochrony przed złośliwym oprogramowaniem firmy Microsoft dla usług w chmurze Azure i maszyn wirtualnych](azure-security-antimalware.md)
* [Jak zainstalować i skonfigurować Trend Micro głębokie zabezpieczeń jako usługa na maszynie Wirtualnej systemu Windows](../virtual-machines/windows/classic/install-trend.md)
* [Jak zainstalować i skonfigurować Symantec Endpoint Protection na maszynie Wirtualnej systemu Windows](../virtual-machines/windows/classic/install-symantec.md)
* [Nowe opcje ochrony przed złośliwym kodem ochrony maszyn wirtualnych platformy Azure — McAfee programu Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Usługa Azure Multi-Factor authentication (MFA) jest metoda uwierzytelniania, która wymaga użycia więcej niż jednej metody weryfikacji i dodaje kluczową drugą warstwę zabezpieczeń do logowania użytkowników i transakcji. MFA ułatwia zabezpieczenie dostępu do danych i aplikacji, spełniając zapotrzebowanie na prosty proces logowania. Zapewnia silne uwierzytelnianie za pomocą różnych opcji weryfikacji — połączenie telefoniczne, wiadomość tekstowa lub aplikacja mobilna weryfikacji lub powiadamiania o kodzie i innych firm tokenów OATH.

Więcej informacji:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co to jest usługa Multi-Factor Authentication platformy Azure?](../active-directory/authentication/multi-factor-authentication.md)
* [Jak działa usługa Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute
Usługa Microsoft Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem specjalnego połączenia prywatnego wspieranego przez dostawcę połączenia. Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami Microsoft w chmurze, np. Microsoft Azure, Office 365 i CRM Online. Połączenie może być z sieci typu dowolna-dowolna (IP VPN), sieci Ethernet typu punkt-punkt lub przy użyciu łączności obejmującej wiele połączeń wirtualnych przez dostawcę połączenia w ramach infrastruktury współlokacji. Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Dzięki temu oferują one większą niezawodność i szybkość oraz mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet.

Więcej informacji:

* [Opis techniczny ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Bramy sieci wirtualnej
Bramy sieci VPN, nazywany również bramy sieci wirtualnej Azure, są używane do wysyłania ruchu sieciowego między sieciami wirtualnymi i lokalizacji lokalnej. Są one również używane do wysyłania ruchu między wieloma sieciami wirtualnymi w obrębie platformy Azure (VNet-VNet).  Bramy sieci VPN zapewniają łączność bezpiecznego między lokalizacjami platformy Azure i infrastruktury.

Więcej informacji:

* [Temat bram sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Przegląd zabezpieczeń sieci platformy Azure](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
Czasami użytkownicy muszą wykonać operacje uprzywilejowane w zasobów platformy Azure lub innych aplikacji SaaS. Często oznacza to, że organizacje mają nadanie im stałe uprzywilejowanego dostępu w usłudze Azure Active Directory (Azure AD). Jest to zagrożenie bezpieczeństwa rosnącym zasobów hostowanych w chmurze, ponieważ organizacje wystarczająco nie można monitorować, co robią tych użytkowników z ich uprzywilejowanego dostępu.
Ponadto w przypadku złamania zabezpieczeń konta użytkownika z dostępem uprzywilejowanym tego naruszenia co może mieć wpływ na ogólną zabezpieczeń chmury. Azure AD Privileged Identity Management pomaga Rozwiąż to zagrożenie, co zmniejsza czas ekspozycji uprawnień i zwiększając wydajność wgląd w użycie.  

Zarządzanie tożsamościami uprzywilejowanymi pojęcia związane z tymczasowego administratora dla roli lub "just in time" uprawnienia dostępu administratora, który jest użytkownik, który musi ukończyć proces aktywacji dla przypisania roli. Proces aktywacji zmienia przypisanie użytkownika do roli w usłudze Azure AD z nieaktywne na aktywny przez określony czas okresu, takich jak osiem godzin.

Więcej informacji:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Wprowadzenie do usługi Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Identity Protection
Ochronę tożsamości w usłudze Azure Active Directory (AD) zapewnia skonsolidowanego widoku podejrzanych działań logowania i potencjalnych luk w zabezpieczeniach w celu ochrony firmy. Identity Protection wykrywa podejrzane działania dla użytkowników i tożsamości uprzywilejowanych (Administrator), oparte na sygnały jak ataków siłowych ujawnione poświadczenia i logowania z nieznanych lokalizacji i zainfekowanych urządzeń.

Zapewniając powiadomień i zalecanych czynności naprawczych, Identity Protection pomaga ograniczyć ryzyko w czasie rzeczywistym. Ważność ryzyka użytkownika jest obliczana i można skonfigurować zasady oparte na ryzyko automatycznie ułatwia zabezpieczenie aplikacji dostępu przed zagrożeniami w przyszłości.

Więcej informacji:

* [Ochronę tożsamości usługi Azure Active Directory](../active-directory/active-directory-identityprotection.md)
* [Kanał 9: Usługi Azure AD i Pokaż tożsamości: Podgląd ochrony tożsamości](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center
Centrum zabezpieczeń Azure ułatwia zapobieganie, wykrywania i reagowania na zagrożenia i zapewnia zwiększyć widoczność i kontrolę nad, zabezpieczeń zasobów platformy Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań z zakresu zabezpieczeń.

Centrum zabezpieczeń ułatwia optymalizacji i monitorowania zabezpieczeń zasobów platformy Azure przez:

* Dzięki któremu można zdefiniować zasady dla zasobów subskrypcji platformy Azure zgodnie z potrzebami zabezpieczeń firmy i typem aplikacji oraz poufności danych w każdej subskrypcji.
* Monitorowanie stanu sieci maszyn wirtualnych platformy Azure, sieci i aplikacji.
* Dostarcza listę alertów zabezpieczeń uporządkowanych według priorytetu, między innymi alertów ze zintegrowanych rozwiązań partnerskich, oraz informacje potrzebne do szybkiego analizowania i zalecenia dotyczące skorygować atak.

Więcej informacji:

* [Wprowadzenie do usługi Azure Security Center](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
