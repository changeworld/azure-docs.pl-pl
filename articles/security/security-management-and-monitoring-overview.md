---
title: Omówienie monitorowania i zarządzania zabezpieczeniami platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie funkcji zabezpieczeń i usług udostępnianych przez platformę Azure do pomocy w zakresie zarządzania i monitorowania usług Azure cloud services i maszyn wirtualnych.
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
ms.date: 10/30/2018
ms.author: terrylan
ms.openlocfilehash: f79f94c277b02a9f377b90bf74763ac617f65c16
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597923"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Omówienie monitorowania i zarządzania zabezpieczeniami platformy Azure

Platforma Azure zapewnia mechanizmy zabezpieczeń ułatwiające zarządzanie i monitorowanie usług Azure cloud services i maszyn wirtualnych (VM). Ten artykuł zawiera omówienie tych podstawowych funkcji zabezpieczeń i usług. Podano linki do artykułów, które Podaj szczegóły każdego z nich, aby dowiedzieć się więcej.

Zabezpieczenia usług w chmurze firmy Microsoft jest nawiązanie partnerstwa i wspólna odpowiedzialność między użytkownikiem a firmą Microsoft. Firma Microsoft jest odpowiedzialna za platformę Azure i zabezpieczenia fizyczne w jej centrów danych (za pomocą zabezpieczenia, takie jak zablokowane wpis wskaźnika drzwi, ogrodzenia i osłony). Platforma Azure zapewnia silne poziomów zabezpieczeń chmury w warstwie oprogramowania, który spełnia wymagania bezpieczeństwa, prywatności i zgodności jej klientów.

Jesteś właścicielem, danych i tożsamości, odpowiedzialność za ochronę ich bezpieczeństwa zasobów w środowisku lokalnym i zabezpieczeń składników chmura, przez jaką masz pełną kontrolę. Firma Microsoft zapewnia środki kontroli bezpieczeństwa i możliwości, aby pomóc w ochronie danych i aplikacji. Twoje stopień odpowiedzialność za zabezpieczeń opiera się na typ usługi w chmurze.

Poniższej tabeli podsumowano saldo odpowiedzialność między firmami Microsoft i klienta.

![Wspólna odpowiedzialność][1]

Aby uzyskać więcej informacji na temat zarządzania zabezpieczeniami, zobacz [Zarządzanie zabezpieczeniami na platformie Azure](azure-security-management.md).

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Kontrola dostępu oparta na rolach (RBAC) umożliwia zarządzanie dostępem szczegółowe dla zasobów platformy Azure. Za pomocą funkcji RBAC, można przyznać osób tylko takiego dostępu, których potrzebują do wykonywania swoich zadań. RBAC może również pomóc upewnić się, że gdy ludzie opuszczenie organizacji, stracą dostęp do zasobów w chmurze.

Więcej informacji:

* [Blog zespołu usługi Active Directory na ROLACH](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Kontrola dostępu oparta na rolach na platformie Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Oprogramowanie chroniące przed złośliwym kodem

Za pomocą platformy Azure można użyć ochrony przed złośliwym oprogramowaniem przez najważniejszych dostawców zabezpieczeń, takich jak Microsoft, Symantec, Trend Micro, McAfee i Kaspersky. To oprogramowanie pomaga chronić Twoje maszyny wirtualne przed złośliwymi plikami, oprogramowaniem reklamowym i innymi zagrożeniami.

Microsoft Antimalware dla usług Azure Cloud Services i Virtual Machines oferuje możliwość instalowania agenta ochrony przed złośliwym kodem dla maszyn wirtualnych i ról PaaS. Oparte na System Center Endpoint Protection, ta funkcja udostępnia sprawdzone w środowisku lokalnym technologii zabezpieczeń w chmurze.

Oferujemy również ścisłą integrację dla trendu [Deep Security](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) i [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) produktów na platformie Azure. Deep Security jest rozwiązanie antywirusowe i SecureCloud to rozwiązanie do szyfrowania. Deep Security jest wdrażana wewnątrz maszyn wirtualnych za pośrednictwem modelu rozszerzeń. Za pomocą interfejsu użytkownika witryny Azure portal i programu PowerShell, można użyć Deep Security wewnątrz nowych maszyn wirtualnych, które są uruchamiane są lub istniejących maszyn wirtualnych, które są już wdrożone.

Symantec Endpoint Protection (SEP) jest również obsługiwana na platformie Azure. Dzięki integracji z portalu można określić, czy zamierzasz używać wrz na maszynie Wirtualnej. WRZ można zainstalować w nowej maszyny Wirtualnej w witrynie Azure portal lub może być zainstalowana na istniejącej maszyny Wirtualnej za pomocą programu PowerShell.

Więcej informacji:

* [Wdrażanie rozwiązań do ochrony przed złośliwym kodem na maszynach wirtualnych platformy Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft Antimalware dla usług Azure Cloud Services i Virtual Machines](azure-security-antimalware.md)
* [Jak zainstalować i skonfigurować Trend Micro Deep Security as a Service, na maszynie Wirtualnej Windows](../virtual-machines/windows/classic/install-trend.md)
* [Jak zainstalować i skonfigurować rozwiązanie Symantec Endpoint Protection na maszyny Wirtualnej z systemem Windows](../virtual-machines/windows/classic/install-symantec.md)
* [Nowe opcje ochrony przed złośliwym kodem na potrzeby ochrony maszyn wirtualnych platformy Azure](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Usługa Azure Multi-Factor Authentication to metoda uwierzytelniania, która wymaga użycia więcej niż jednej metody weryfikacji. Dodaje kluczową drugą warstwę zabezpieczeń do logowania użytkowników i transakcji. 

Multi-Factor Authentication zabezpiecza dostęp do danych i aplikacji, jednocześnie spełnia wymagania użytkowników dotyczące prostoty procesu logowania. Zapewnia ona silne uwierzytelnianie za pomocą zakresu, opcje weryfikacji (połączenie telefoniczne, wiadomość SMS lub powiadomień lub weryfikacji kodu aplikacji mobilnej) i tokenów OATH innej firmy.

Więcej informacji:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co to jest usługa Multi-Factor Authentication platformy Azure?](../active-directory/authentication/multi-factor-authentication.md)
* [Jak działa usługa Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Usługa Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na Microsoft Cloud za pośrednictwem specjalnego połączenia prywatnego, który jest zapewniana przez dostawcę połączenia. Dzięki usłudze ExpressRoute można ustanowić połączenia z usług chmurowych firmy Microsoft, takich jak Azure, Office 365 i CRM Online. Łączność może być z zakresu:

* Sieć dowolna dowolna (IP VPN).
* Sieć Ethernet typu punkt-punkt.
* Wiele połączeń wirtualnych za pośrednictwem dostawcy łączności, we wspólnej lokalizacji. 

Połączenia ExpressRoute omijają publiczny internet. Może zaoferować więcej niezawodność, większe szybkości, krótsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez internet.

Więcej informacji:

* [ExpressRoute — opis techniczny](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Bramy sieci wirtualnej

Bramy sieci VPN, nazywany również bramy sieci wirtualnej platformy Azure są używane do wysyłania ruchu sieciowego między sieciami wirtualnymi i lokalizacjami lokalnymi. Służą one także wysyłać ruch sieciowy między wieloma sieciami wirtualnymi na platformie Azure (sieci). Bramy sieci VPN zapewniają bezpiecznego połączenia obejmującego wiele lokalizacji — między platformą Azure i infrastruktury.

Więcej informacji:

* [Temat bram sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Omówienie zabezpieczeń sieci platformy Azure](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Czasami użytkownicy potrzebują do wykonania uprzywilejowanych operacji korzystających z zasobów platformy Azure lub innych aplikacji SaaS. Często oznacza to, że organizacje ciągowych trwałego dostępu uprzywilejowanego w usłudze Azure Active Directory (Azure AD). 

Jest to rosnący zagrożenie bezpieczeństwa dla zasobów hostowanych w chmurze, ponieważ organizacje wystarczająco nie można monitorować, użytkownicy ci korzystają z ich uprzywilejowanego dostępu. Ponadto w przypadku naruszenia zabezpieczeń konta użytkownika z dostępem uprzywilejowanym tego jednego naruszenie może mieć wpływ na ogólne bezpieczeństwo chmury w organizacji. Usługa Azure AD Privileged Identity Management pomaga rozwiązać to zagrożenie, zmniejszyć czas narażenia uprawnień i zwiększyć wgląd w użycie.  

Privileged Identity Management wprowadza koncepcję tymczasowe administratora dla roli lub "just in time" dostępu administratora. Tego rodzaju administratora jest użytkownik, który musi zakończyć proces aktywacji dla przypisania roli. Proces aktywacji zmienia przypisanie użytkownika do roli w usłudze Azure AD z okresu nieaktywne na aktywny, przez wyznaczony czas.

Więcej informacji:

* [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)
* [Rozpoczynanie pracy z usługą Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

Usługa Azure AD Identity Protection udostępnia skonsolidowany widok podejrzanych działań logowania i potencjalnych luk w zabezpieczeniach, aby pomóc chronić Twoją firmę. Identity Protection wykrywa podejrzane działania dla użytkowników i tożsamości uprzywilejowane (Administrator), w oparciu o sygnały, takie jak:

* Ataków siłowych.
* Ujawnione poświadczenia.
* Logowania z nieznanych lokalizacji i zainfekowanych urządzeń.

Dostarczając powiadomień i zalecanych czynności naprawczych, Identity Protection pomaga ograniczyć ryzyko w czasie rzeczywistym. Obliczany jest ważność ryzyka użytkownika. Można skonfigurować zasady oparte na ryzyku, aby automatycznie aplikacji zabezpieczenie dostępu przez przyszłymi zagrożeniami.

Więcej informacji:

* [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory/active-directory-identityprotection.md)
* [Witryna Channel 9: Usługa Azure AD i wyświetlanie tożsamości: Identity Protection w wersji zapoznawczej](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center

Usługa Azure Security Center pomaga zapobiegać, wykrywanie ich i reagowanie na zagrożenia. Usługa Security Center zapewnia możesz zwiększyć wgląd w i skuteczniejszą kontrolę ich zabezpieczeń zasobów platformy Azure. Zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure. Pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

Usługa Security Center pomaga optymalizować i monitorować zabezpieczenia zasobów platformy Azure przez:

* Włączanie można zdefiniować zasady dla zasobów subskrypcji platformy Azure na podstawie:
  * W zakresie zabezpieczeń firmy.
  * Typ aplikacji lub wrażliwości danych w ramach każdej subskrypcji.
* Monitorowanie stanu maszynach wirtualnych platformy Azure, sieci i aplikacji.
* Dostarczanie listy alertów zabezpieczeń uporządkowanych według priorytetu, między innymi alertów ze zintegrowanych rozwiązań partnerskich. Zapewnia również informacje, potrzebne do szybkiego analizowania ataku i zalecenia dotyczące sposobu jego rozwiązania.

Więcej informacji:

* [Wprowadzenie do usługi Azure Security Center](../security-center/security-center-intro.md)
* [Poprawa bezpiecznego ocenę w usłudze Azure Security Center](../security-center/security-center-secure-score.md)

## <a name="intelligent-security-graph"></a>Usługę Intelligent Security Graph

Intelligent Security Graph zapewnia ochronę przed zagrożeniami w czasie rzeczywistym w produktów i usług. Używa zaawansowanych analiz, która łączy ogromne ilości zagrożeń danych analizy i zabezpieczeń w celu zapewnienia szczegółowe informacje, które mogą wzmocnić zabezpieczenia organizacji. Firma Microsoft korzysta z zaawansowanych analiz — przetwarzanie ponad 450 MLD uwierzytelnień na miesiąc, skanowanie 400 MLD wiadomości e-mail pod kątem złośliwego oprogramowania i wyłudzania informacji oraz aktualizowanie 1 MLD urządzeń — dostarczać pełniejszy wgląd. Statystyki te mogą pomóc Twojej organizacji wykrywać zagrożenia i szybko na nie reagować.

* [Usługę Intelligent Security Graph](https://www.microsoft.com/security/intelligence)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png

