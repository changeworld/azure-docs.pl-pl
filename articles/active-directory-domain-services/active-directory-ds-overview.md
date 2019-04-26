---
title: Omówienie usługi Azure Active Directory Domain Services | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 0d47178f-773e-45f9-9ff4-9e8cffa4ffa2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: ergreenl
ms.openlocfilehash: c9833ac1dd6f5b046a89a1c2d9007e2cd5c9f813
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416698"
---
# <a name="azure-active-directory-ad-domain-services"></a>Azure Active Directory (AD) Domain Services
## <a name="overview"></a>Omówienie
Usługi infrastruktury platformy Azure umożliwiają wdrażanie szerokiego zakresu rozwiązań obliczeniowych zwinne. Usługa Azure Virtual Machines można wykonywać niemal natychmiastowe wdrażanie, i płacisz tylko za wykorzystane minuty. Przy użyciu obsługi Windows, Linux, SQL Server, Oracle, IBM, SAP i BizTalk, możesz wdrożyć dowolne obciążenie, w dowolnym języku i w prawie każdym systemie operacyjnym. Korzyści te umożliwiają migrować starsze aplikacje wdrożone w środowisku lokalnym na platformie Azure, aby zaoszczędzić na wydatki operacyjne.

Kluczowym aspektem migrowania aplikacji lokalnych do platformy Azure jest obsługa wymagań tożsamości tych aplikacji. Aplikacji obsługujących katalog może Polegaj na LDAP w celu odczytu lub zapisu do katalogu firmy, zależą od Windows zintegrowanego uwierzytelniania (uwierzytelnianie Kerberos lub NTLM) do uwierzytelniania użytkowników końcowych. Line-of-business (LOB) działających w systemie Windows Server są zazwyczaj wdrożone na komputerach przyłączonych do domeny, aby można było zarządzać nimi przy użyciu zasad grupy. Te zależności w infrastrukturze tożsamość firmowa aplikacjom "lift-and-shift" środowiska lokalnego do chmury, muszą zostać rozwiązane.

Administratorzy często włączyć do jednego z następujących rozwiązań do zaspokojenia potrzeb tożsamości aplikacji wdrożonych na platformie Azure:

* Wdrażanie połączenia VPN lokacja lokacja między obciążeń działających w usługach infrastruktury platformy Azure i katalogu firmowego w środowisku lokalnym.
* Konfigurowanie replik kontrolerów domeny przy użyciu maszyn wirtualnych platformy Azure, aby rozszerzyć firmowej infrastruktury usługi AD w domenie lub lesie.
* Wdrażanie autonomicznego domeny na platformie Azure przy użyciu kontrolery domeny wdrożone jako maszyny wirtualne platformy Azure.

Te metody borykają się z dużymi kosztami i nakładem pracy administracyjnej. Administratorzy są wymagane do wdrożenia kontrolerów domeny za pomocą maszyn wirtualnych na platformie Azure. Ponadto muszą one zarządzanie, zabezpieczenia, poprawki, monitorowania, kopii zapasowych i rozwiązywanie problemów tych maszyn wirtualnych. Poleganie na połączenia sieci VPN do katalogu lokalnego powoduje, że obciążeń wdrożonych na platformie Azure są narażone na błędy przejściowe problemy z siecią lub awarie. Te awarie sieci z kolei powodować niższe czasu i zmniejszenie niezawodność dla tych aplikacji.

Został zaprojektowany usług domenowych Azure AD jako alternatywa łatwiejsze.

### <a name="watch-an-introductory-video"></a>Obejrzyj klip wideo

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="introducing-azure-ad-domain-services"></a>Wprowadzenie do usług domenowych Azure AD

Azure AD Domain Services oferuje zarządzane usługi domenowe przyłączania do domeny, grupy zasad, LDAP, Kerberos/NTLM uwierzytelniania są w pełni zgodne z usługą Active Directory systemu Windows Server. Mogą wykorzystywać te usługi domeny bez konieczności wdrażania, zarządzania i stosowanie poprawek do kontrolerów domeny w chmurze. Azure AD Domain Services integruje się z istniejącej dzierżawy usługi Azure AD, umożliwiając użytkownikom zalogowanie się przy użyciu swoich poświadczeń firmowych. Ponadto można użyć istniejących grup i kont użytkowników do zabezpieczania dostępu do zasobów, co pozwala na zapewnienie gładsze "lift-and-shift" zasobów lokalnych do usług infrastruktury platformy Azure.

Funkcjonalność platformy Azure AD Domain Services współpracuje bezproblemowo niezależnie od tego, czy dzierżawy usługi Azure AD tylko w chmurze lub synchronizowanych z usługą Active Directory w środowisku lokalnym.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Azure AD Domain Services dla organizacji, tylko w chmurze

Tylko w chmurze dzierżawę usługi Azure AD (często określanymi jako "dzierżaw zarządzanych") nie ma żadnych zużycie tożsamości lokalnych. Innymi słowy konta użytkownika, hasła i członkostwa w grupach są wszystkie natywna względem chmury — czyli tworzone i zarządzane w usłudze Azure AD. Należy wziąć pod uwagę kilka minut, Contoso jest tylko w chmurze dzierżawę usługi Azure AD. Jak pokazano na poniższej ilustracji, administrator firmy Contoso skonfigurował sieci wirtualnej w usługach infrastruktury platformy Azure. Aplikacje i obciążenia serwera są wdrażane w tej sieci wirtualnej, maszynach wirtualnych platformy Azure. Ponieważ firma Contoso ma dzierżawy tylko w chmurze, wszystkie tożsamości użytkownika, poświadczeń i członkostwa w grupach są tworzone i zarządzane w usłudze Azure AD.

![Omówienie usług domenowych Azure AD](./media/active-directory-domain-services-overview/aadds-overview.png)

Firmy Contoso administratora IT, można włączyć usług domenowych Azure AD dla swojej dzierżawy usługi Azure AD i chce udostępnić usług domain services w tej sieci wirtualnej. Dzięki temu usługi Azure AD Domain Services aprowizowanie domeny zarządzanej i udostępnia go w sieci wirtualnej. Wszystkie konta użytkowników, członkostw w grupie i poświadczenia użytkownika są dostępne w dzierżawie usługi Azure AD firmy Contoso również są dostępne w tej domenie nowo utworzony. Ta funkcja umożliwia użytkownikom w organizacji Zaloguj się do domeny przy użyciu swoich poświadczeń firmowych — na przykład podczas zdalnego łączenia się z komputerów przyłączonych do domeny za pomocą pulpitu zdalnego. Administratorzy mogą aprowizować dostęp do zasobów w domenie przy użyciu istniejących członkostw w grupie. Aplikacje wdrożone na maszynach wirtualnych w sieci wirtualnej można użyć funkcji, takich jak przyłączanie do domeny, LDAP odczytu, LDAP bind, uwierzytelniania NTLM i Kerberos i zasad grupy.

Oto kilka istotne aspekty domeny zarządzanej, który jest obsługiwany przez usługi domenowe Azure AD:

* Firmy Contoso IT administrator nie musi zarządzać, patch lub monitorowanie tej domeny lub kontrolerów domeny dla tej domeny zarządzanej.
* Nie ma potrzeby zarządzania replikacją AD dla tej domeny. Konta użytkowników, członkostwa w grupach i poświadczeń z dzierżawą usługi Azure AD firmy Contoso są automatycznie dostępne w ramach tej domeny zarządzanej.
* Ponieważ domeny jest zarządzane przez usługę Azure AD Domain Services, firma Contoso firmy IT administrator nie ma uprawnienia administratora domeny lub administratora przedsiębiorstwa w tej domenie.

### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Azure AD Domain Services dla organizacji hybrydowych
Organizacje z hybrydowej infrastruktury informatycznej korzystać z różnych zasobów w chmurze i zasobów lokalnych. Takie organizacje synchronizować informacje o tożsamościach z ich z katalogu lokalnego do swojej dzierżawy usługi Azure AD. Jak migrować bardziej "patrzą" organizacji hybrydowych aplikacji lokalnych do chmury, szczególnie starszych aplikacji obsługujących katalogi, usług domenowych Azure AD może być przydatne do nich.

Firma litware została wdrożona [program Azure AD Connect](../active-directory/hybrid/whatis-hybrid-identity.md), aby zsynchronizować informacje o tożsamościach z ich z katalogu lokalnego do swojej dzierżawy usługi Azure AD. Informacje o tożsamości, która będzie synchronizowana obejmuje konta użytkowników, ich skrótów poświadczeń dla uwierzytelniania (synchronizacji skrótów haseł) i członkostwa w grupach.

> [!NOTE]
> **Synchronizacja skrótów haseł jest obowiązkowa dla organizacji hybrydowych do korzystania z usług domenowych Azure AD**. To wymaganie wynika poświadczeń użytkownika są potrzebne w domenie zarządzanej udostępniane przez usługi domenowe Azure AD do uwierzytelniania tych użytkowników za pomocą metod uwierzytelniania NTLM lub Kerberos.
>
>

![Usług domenowych Azure AD dla Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

Na poprzedniej ilustracji przedstawiono, jak używać usług domenowych Azure AD w organizacji za pomocą hybrydowego rozwiązania łączącego program infrastruktury IT, takich jak firma Litware. Aplikacje i obciążenia serwera, które wymagają usług domain services firmy litware są wdrażane w sieci wirtualnej w usługach infrastruktury platformy Azure. Firmy litware administratora IT, można włączyć usług domenowych Azure AD dla swojej dzierżawy usługi Azure AD i chce udostępnić w tej sieci wirtualnej w domenie zarządzanej. Ponieważ Litware organizacji za pomocą hybrydowego rozwiązania łączącego program infrastruktury IT, konta użytkowników, grupami i poświadczeniami są synchronizowane ich dzierżawy usługi Azure AD z ich z katalogu lokalnego. Ta funkcja umożliwia użytkownikom na logowanie się do domeny przy użyciu swoich poświadczeń firmowych — na przykład gdy zdalne łączenie się z maszyn przyłączony do domeny za pomocą pulpitu zdalnego. Administratorzy mogą aprowizować dostęp do zasobów w domenie przy użyciu istniejących członkostw w grupie. Aplikacje wdrożone na maszynach wirtualnych w sieci wirtualnej można użyć funkcji, takich jak przyłączanie do domeny, LDAP odczytu, LDAP bind, uwierzytelniania NTLM i Kerberos i zasad grupy.

Oto kilka istotne aspekty domeny zarządzanej, który jest obsługiwany przez usługi domenowe Azure AD:

* Domena zarządzana jest autonomicznym domeny. Nie jest rozszerzeniem firmy Litware domeny lokalnej.
* Firmy litware IT administrator nie musi zarządzać poprawkami, lub monitoruje kontrolery domeny dla tej domeny zarządzanej.
* Nie ma potrzeby zarządzania replikacją AD, do tej domeny. Konta użytkowników, członkostwa w grupach i poświadczeń z katalogu lokalnego firmy Litware synchronizowanych z usługą Azure AD za pomocą usługi Azure AD Connect. Te konta użytkowników, członkostw w grupie i poświadczenia są automatycznie dostępne w ramach domeny zarządzanej.
* Ponieważ domena jest zarządzany przez usługę Azure AD Domain Services, firma Litware firmy IT administrator nie ma uprawnienia administratora domeny lub administratora przedsiębiorstwa w tej domenie.

## <a name="benefits"></a>Korzyści
Za pomocą usług domenowych Azure AD można korzystać z następujących zalet:

* **Proste** — może zaspokoić potrzeby tożsamości maszyny wirtualne wdrożone w usługach infrastruktury platformy Azure przy użyciu jedynie kilku kliknięć. Nie jest konieczne wdrażanie infrastruktury i Zarządzanie tożsamościami w platformie Azure lub Instalator łączności lokalnej infrastrukturze tożsamości.
* **Zintegrowane** — usługi domenowe Azure AD jest ściśle zintegrowana z dzierżawą usługi Azure AD. Można teraz używać usługi Azure AD jak katalog zintegrowanych przedsiębiorstwa opartej na chmurze przeznaczoną do potrzeb nowoczesnych aplikacji i tradycyjnych aplikacji obsługujących katalog.
* **Zgodne** — usługi domenowe Azure AD jest oparta na sprawdzonej infrastrukturę klasy korporacyjnej z usługi Active Directory systemu Windows Server. Dlatego aplikacje polegać na większy stopień zgodności z funkcjami usługi Active Directory systemu Windows Server. Nie wszystkie funkcje dostępne w systemie Windows Server AD są aktualnie dostępne w usługach domenowych Azure AD. Jednak funkcje dostępne są zgodne z odpowiedniej funkcji systemu Windows Server AD polegać w infrastrukturze lokalnej. Możliwości dołączania LDAP, Kerberos, NTLM, zasady grupy i domeny stanowią dojrzała oferty, które zostały przetestowane i Elegancja przez różne wersje systemu Windows Server.
* **Ekonomiczne** — za pomocą usług domenowych w usłudze Azure AD, możesz uniknąć obciążeń infrastruktury i zarządzania skojarzony z zarządzania infrastrukturą tożsamości do obsługi tradycyjnych aplikacji obsługujących katalog. Można przenieść te aplikacje do usługi infrastruktury platformy Azure i korzystać z niższych kosztów operacyjnych.


## <a name="next-steps"></a>Kolejne kroki
### <a name="learn-more-about-azure-ad-domain-services"></a>Dowiedz się więcej o usłudze Azure AD Domain Services
* [Funkcje](active-directory-ds-features.md)
* [Scenariusze wdrażania](active-directory-ds-scenarios.md)
* [Dowiedz się, jeśli program Azure AD Domain Services odpowiada przypadki użycia](active-directory-ds-comparison.md)
* [Zrozumienie, jak usługi domenowe Azure AD synchronizuje się z katalogiem usługi Azure AD](active-directory-ds-synchronization.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Wprowadzenie do usług Azure AD Domain Services
* [Włączanie usług domenowych Azure AD przy użyciu witryny Azure portal](active-directory-ds-getting-started.md)
