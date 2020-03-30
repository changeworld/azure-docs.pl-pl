---
title: Funkcje zabezpieczeń zarządzania i monitorowania — Microsoft Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie funkcji zabezpieczeń i usług, które platforma Azure zapewnia w celu ułatwienia zarządzania i monitorowania usług w chmurze platformy Azure i maszyn wirtualnych.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 7ad7a29a92d25556190b4cf44f4e48158a6f0952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73162747"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Omówienie zarządzania zabezpieczeniami i monitorowania zabezpieczeń platformy Azure
Ten artykuł zawiera omówienie funkcji zabezpieczeń i usług, które platforma Azure zapewnia w celu ułatwienia zarządzania i monitorowania usług w chmurze platformy Azure i maszyn wirtualnych.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Kontrola dostępu oparta na rolach (RBAC) zapewnia szczegółowe zarządzanie dostępem dla zasobów platformy Azure. Za pomocą RBAC, można przyznać ludziom tylko ilość dostępu, które są potrzebne do wykonywania swoich zadań. RBAC może również pomóc w zapewnieniu, że gdy ludzie opuszczają organizację, tracą dostęp do zasobów w chmurze.

Więcej informacji:

* [Blog zespołu usługi Active Directory na RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Kontrola dostępu oparta na rolach na platformie Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Antimalware

Dzięki platformie Azure możesz używać oprogramowania ochrony przed złośliwym oprogramowaniem od głównych dostawców zabezpieczeń, takich jak Microsoft, Symantec, Trend Micro, McAfee i Kaspersky. To oprogramowanie pomaga chronić maszyny wirtualne przed złośliwymi plikami, adware i innymi zagrożeniami.

Ochrona przed złośliwym oprogramowaniem firmy Microsoft dla usług w chmurze i maszyn wirtualnych platformy Azure oferuje możliwość zainstalowania agenta ochrony przed złośliwym oprogramowaniem zarówno dla ról PaaS, jak i maszyn wirtualnych. Ta funkcja oparta na programie System Center Endpoint Protection wprowadza do chmury sprawdzoną lokalną technologię zabezpieczeń.

Oferujemy również głęboką integrację produktów [Deep Security](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) i [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) firmy Trend na platformie Azure. Deep Security to rozwiązanie antywirusowe, a SecureCloud jest rozwiązaniem szyfru. Deep Security jest wdrażany wewnątrz maszyn wirtualnych za pośrednictwem modelu rozszerzenia. Korzystając z interfejsu użytkownika witryny azure portal i programu PowerShell, można użyć deep security wewnątrz nowych maszyn wirtualnych, które są obracane w górę lub istniejących maszyn wirtualnych, które są już wdrożone.

Program Symantec Endpoint Protection (SEP) jest również obsługiwany na platformie Azure. Za pośrednictwem integracji portalu można określić, że zamierzasz używać SEP na maszynie wirtualnej. Sep można zainstalować na nowej maszynie wirtualnej za pośrednictwem witryny Azure portal lub można go zainstalować na istniejącej maszynie wirtualnej za pośrednictwem programu PowerShell.

Więcej informacji:

* [Wdrażanie rozwiązań do ochrony przed złośliwym kodem na maszynach wirtualnych platformy Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Ochrona przed złośliwym oprogramowaniem firmy Microsoft dla usług w chmurze i maszyn wirtualnych platformy Azure](antimalware.md)
* [Jak zainstalować i skonfigurować trend Trend Micro Deep Security jako usługa na maszynie Wirtualnej systemu Windows](/azure/virtual-machines/windows/classic/install-trend)
* [Jak zainstalować i skonfigurować program Symantec Endpoint Protection na maszynie Wirtualnej systemu Windows](/azure/virtual-machines/windows/classic/install-symantec)
* [Nowe opcje ochrony przed złośliwym oprogramowaniem do ochrony maszyn wirtualnych platformy Azure](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Uwierzytelnianie wieloskładnikowe platformy Azure jest metodą uwierzytelniania, która wymaga użycia więcej niż jednej metody weryfikacji. Dodaje krytyczną drugą warstwę zabezpieczeń do logowania użytkowników i transakcji.

Uwierzytelnianie wieloskładnikowe pomaga chronić dostęp do danych i aplikacji, spełniając jednocześnie zapotrzebowanie użytkowników na prosty proces logowania. Zapewnia silne uwierzytelnianie za pośrednictwem szeregu opcji weryfikacji (połączenia telefonicznego, wiadomości tekstowej lub powiadomienia lub kodu weryfikacyjnego aplikacji mobilnej) oraz tokenów OATH innych firm.

Więcej informacji:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co to jest usługa Multi-Factor Authentication platformy Azure?](/azure/active-directory/authentication/multi-factor-authentication)
* [Jak działa usługa Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Za pomocą usługi Azure ExpressRoute można rozszerzyć sieci lokalne do chmury Microsoft Cloud za pośrednictwem dedykowanego połączenia prywatnego, które jest ułatwione przez dostawcę łączności. Za pomocą usługi ExpressRoute można nawiązywać połączenia z usługami w chmurze firmy Microsoft, takimi jak Azure, Office 365 i CRM Online. Łączność może pochodzić z:

* Sieć typu "any-to-any" (IP VPN).
* Sieć Ethernet typu punkt-punkt.
* Wirtualne połączenie krzyżowe za pośrednictwem dostawcy łączności w układzie kolokowania.

Połączenia usługi ExpressRoute nie przechodzą przez publiczny Internet. Mogą one oferować większą niezawodność, szybsze prędkości, mniejsze opóźnienia i wyższe bezpieczeństwo niż typowe połączenia przez Internet.

Więcej informacji:

* [ExpressRoute — opis techniczny](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Bramy sieci wirtualnej

Bramy sieci VPN, nazywane również bramami sieci wirtualnej platformy Azure, są używane do wysyłania ruchu sieciowego między sieciami wirtualnymi a lokalizacjami lokalnymi. Są one również używane do wysyłania ruchu między wieloma sieciami wirtualnymi na platformie Azure (sieć do sieci). Bramy sieci VPN zapewniają bezpieczną łączność między lokalizacjami między platformą Azure a infrastrukturą.

Więcej informacji:

* [Bramy sieci VPN — informacje](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Omówienie zabezpieczeń sieci platformy Azure](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Czasami użytkownicy muszą wykonywać uprzywilejowane operacje w zasobach platformy Azure lub innych aplikacjach SaaS. Często oznacza to, że organizacje dają im stały uprzywilejowany dostęp w usłudze Azure Active Directory (Azure AD).

Jest to rosnące zagrożenie bezpieczeństwa dla zasobów hostowanych w chmurze, ponieważ organizacje nie mogą wystarczająco monitorować, co ci użytkownicy robią z ich uprzywilejowanym dostępem. Ponadto jeśli konto użytkownika z uprzywilejowanym dostępem zostanie naruszone, to jedno naruszenie może mieć wpływ na ogólne bezpieczeństwo w chmurze w organizacji. Usługa Azure AD Privileged Identity Management pomaga rozwiązać to ryzyko, obniżając czas narażenia uprawnień i zwiększając wgląd w użycie.  

Zarządzanie tożsamościami uprzywilejowanymi wprowadza koncepcję tymczasowego administratora dla roli lub dostępu administratora "w samą porę". Ten rodzaj administratora jest użytkownikiem, który musi ukończyć proces aktywacji dla tej przypisanej roli. Proces aktywacji zmienia przypisanie użytkownika do roli w usłudze Azure AD z nieaktywne do aktywne, przez określony czas.

Więcej informacji:

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Wprowadzenie do aplikacji Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

Usługa Azure AD Identity Protection zapewnia skonsolidowany widok podejrzanych działań logowania i potencjalnych luk w zabezpieczeniach, aby chronić firmę. Usługa Identity Protection wykrywa podejrzane działania użytkowników i uprzywilejowanych (admin) tożsamości na podstawie takich sygnałów, jak:

* Brutalne ataki siłowe.
* Wyciekły poświadczenia.
* Logowania z nieznanych lokalizacji i zainfekowanych urządzeń.

Dostarczając powiadomienia i zalecane środki zaradcze, ochrona tożsamości pomaga ograniczyć ryzyko w czasie rzeczywistym. Oblicza ważność ryzyka użytkownika. Można skonfigurować zasady oparte na ryzyku, aby automatycznie chronić dostęp aplikacji przed przyszłymi zagrożeniami.

Więcej informacji:

* [Ochrona tożsamości w usłudze Azure Active Directory](/azure/active-directory/active-directory-identityprotection)
* [Kanał 9: Usługa Azure AD i pokaz tożsamości: wersja zapoznawcza ochrony tożsamości](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center

Usługa Azure Security Center pomaga w zapobieganiu zagrożeniom, wykrywaniu ich i reagowaniu na nie. Usługa Security Center zapewnia lepszy wgląd w zabezpieczenia zasobów platformy Azure i kontrolę nad tym. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure. Pomaga wykrywać zagrożenia, które w przeciwnym razie mogą przejść niezauważone, i współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

Usługa Security Center ułatwia optymalizację i monitorowanie zabezpieczeń zasobów platformy Azure przez:

* Umożliwia zdefiniowanie zasad dla zasobów subskrypcji platformy Azure zgodnie z:
  * Bezpieczeństwo Twojej firmy.
  * Typ aplikacji lub czułość danych w każdej subskrypcji.
* Monitorowanie stanu maszyn wirtualnych platformy Azure, sieci i aplikacji.
* Wyświetlanie listy priorytetowych alertów zabezpieczeń, w tym alertów ze zintegrowanych rozwiązań partnerskich. Zawiera również informacje, które należy szybko zbadać atak i zalecenia dotyczące sposobu jego korygować.

Więcej informacji:

* [Wprowadzenie do usługi Azure Security Center](../../security-center/security-center-intro.md)
* [Popraw swój bezpieczny wynik w usłudze Azure Security Center](../../security-center/security-center-secure-score.md)

## <a name="intelligent-security-graph"></a>Inteligentny wykres zabezpieczeń

Inteligentny wykres zabezpieczeń zapewnia ochronę przed zagrożeniami w czasie rzeczywistym w produktach i usługach firmy Microsoft. Korzysta z zaawansowanej analizy, która łączy ogromną ilość danych analizy zagrożeń i zabezpieczeń, aby zapewnić szczegółowe informacje, które mogą wzmocnić bezpieczeństwo organizacyjne. Firma Microsoft korzysta z zaawansowanej analizy — przetwarza ponad 450 miliardów uwierzytelnień miesięcznie, skanuje 400 miliardów wiadomości e-mail w poszukiwaniu złośliwego oprogramowania i wyłudzania informacji oraz aktualizuje miliard urządzeń, aby dostarczać bogatsze informacje. Te szczegółowe informacje mogą pomóc twojej organizacji szybko wykrywać ataki i reagować na nie.

* [Inteligentny wykres zabezpieczeń](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [modelu współużytkowania](shared-responsibility.md) i zadaniach zabezpieczeń obsługiwanych przez firmę Microsoft i które zadania są obsługiwane przez użytkownika.

Aby uzyskać więcej informacji na temat zarządzania zabezpieczeniami, zobacz [Zarządzanie zabezpieczeniami na platformie Azure](management.md).
