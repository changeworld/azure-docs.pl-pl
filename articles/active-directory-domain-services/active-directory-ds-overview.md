---
title: Omówienie usług domenowych w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Omówienie usług domenowych w usłudze Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 0d47178f-773e-45f9-9ff4-9e8cffa4ffa2
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 9c22ed1cca49e3e8789c3da5b7bd59d9aeb3ce42
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218459"
---
# <a name="azure-active-directory-ad-domain-services"></a>Usługi domenowe w usłudze Azure Active Directory (AD)
## <a name="overview"></a>Przegląd
Usług infrastruktury platformy Azure umożliwiają wdrażanie szeroką gamę rozwiązań opartych na przetwarzaniu w sposób elastyczne. Z maszyn wirtualnych platformy Azure można wdrożyć niemal natychmiast i płacisz tylko za minutę. Przy użyciu pomocy technicznej dla systemu Windows, Linux, SQL Server, Oracle, IBM, SAP i BizTalk, można wdrożyć żadnych obciążenie, dowolnego języka, w niemal dowolnym systemie operacyjnym. Korzyści te umożliwiają Migrowanie starszych aplikacji wdrożonych lokalnie na platformie Azure, aby zapisać na kosztów operacyjnych.

Kluczowym aspektem migrowanie aplikacji lokalnych do platformy Azure obsługuje potrzeb tożsamości tych aplikacji. Aplikacje z obsługą katalogu może polegać na LDAP do odczytu lub zapisu w katalogu firmy lub polegać na zintegrowane uwierzytelnianie systemu Windows (uwierzytelnianie Kerberos lub NTLM) do uwierzytelniania użytkowników końcowych. Biznesowych (LOB) w systemie Windows Server zwykle wdrożenia aplikacji na komputerach przyłączonych do domeny, więc będą one zarządzane przy użyciu zasad grupy. Aby "przyrostu i zmiana" aplikacji lokalnych do chmury te zależności w infrastrukturze tożsamością firmową muszą zostać rozwiązane.

Administratorzy często włączyć do jednego z następujących rozwiązań do zaspokojenia potrzeb tożsamości aplikacji wdrożonych na platformie Azure:

* Wdrożyć połączenia sieci VPN lokacja lokacja między obciążeń uruchomionych usług infrastruktury platformy Azure i katalogu firmowym lokalną.
* Konfigurowanie replik kontrolerów domeny przy użyciu maszyn wirtualnych platformy Azure, aby rozszerzyć firmowej infrastruktury domenie lub lesie usługi Active Directory.
* Wdrożenie autonomicznej domeny na platformie Azure przy użyciu kontrolery domeny wdrożone jako maszyny wirtualnej platformy Azure.

Tych sposobów obniżenie kosztu wysokiej i koszty administracyjne. Administratorzy są wymagane do wdrożenia kontrolerów domeny przy użyciu maszyn wirtualnych na platformie Azure. Ponadto należy zarządzać, secure poprawka, monitorowanie, kopii zapasowej i rozwiązywanie problemów z tych maszyn wirtualnych. Zależność od połączenia sieci VPN do katalogu lokalnego powoduje, że obciążeń wdrożonych na platformie Azure jako niebezpieczny błędami występującymi sieci lub awarie. Te awarii sieci z kolei powoduje niższe przestojów i zmniejszonej niezawodności tych aplikacji.

Firma Microsoft zaprojektowane usług domenowych Azure AD jako alternatywa łatwiejsze.

### <a name="watch-an-introductory-video"></a>Obejrzyj klip wideo wprowadzenia

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="introducing-azure-ad-domain-services"></a>Wprowadzenie do usług domenowych Azure AD

Usługi domenowe Azure AD zapewnia domeny zarządzanej usług, takich jak przyłączanie do domeny, uwierzytelniania Kerberos/NTLM zasad, LDAP, grupy, który są w pełni zgodne z usługą Active Directory systemu Windows Server. Będzie można korzystać z tych usług domeny bez konieczności wdrażania, zarządzania i poprawka kontrolerów domeny w chmurze. Usługi domenowe Azure AD jest zintegrowany z dzierżawy usługi Azure AD istniejących, umożliwiając użytkownikom zalogowanie się przy użyciu swoich poświadczeń firmowych. Ponadto można użyć istniejących grup i kont użytkowników do bezpieczny dostęp do zasobów, w związku z tym zapewnienie płynniejszy "przyrostu i shift" lokalnych zasobów usługi infrastruktury platformy Azure.

Azure funkcjonalności usług domenowych AD współdziała niezależnie od tego, czy dzierżawy usługi Azure AD tylko w chmurze lub zsynchronizowanej lokalnej usługi Active Directory.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Usługi domenowe Azure AD dla organizacji, tylko w chmurze

Tylko w chmurze dzierżawę usługi Azure AD (często określany jako "dzierżaw zarządzanych") nie ma żadnych wpływ tożsamości lokalnych. Innymi słowy konta użytkowników, hasła i członkostwa w grupach są wszystkie natywna względem chmury — to znaczy tworzone i zarządzane w usłudze Azure AD. Należy wziąć pod uwagę chwilę czy firma Contoso jest tylko w chmurze dzierżawę usługi Azure AD. Jak pokazano na poniższej ilustracji, administrator firmy Contoso została skonfigurowana sieć wirtualną w usługi infrastruktury platformy Azure. Aplikacje i obciążenia serwera są wdrażane w tej sieci wirtualnej w maszynach wirtualnych platformy Azure. Ponieważ firma Contoso używa dzierżawy tylko w chmurze, wszystkie tożsamości użytkownika, poświadczeń i członkostwa w grupach są tworzone i zarządzane w usłudze Azure AD.

![Omówienie usług domenowych Azure AD](./media/active-directory-domain-services-overview/aadds-overview.png)

Firmy Contoso administratora IT mogą włączyć usługi domenowe Azure AD dla swojej dzierżawy usługi Azure AD i wybierz opcję Udostępnianie usług domenowych w tej sieci wirtualnej. Później usługi domenowe Azure AD obsługuje domeną zarządzaną i udostępnia go w sieci wirtualnej. Wszystkie konta użytkowników, członkostwa w grupach i poświadczeń użytkownika należące do firmy Contoso dzierżawy usługi Azure AD są dostępne również w tej domenie nowo utworzony. Ta funkcja umożliwia użytkownikom w organizacji Zaloguj się do domeny za pomocą swoich poświadczeń firmowych — na przykład przy połączeniu zdalnym na komputerach przyłączonych do domeny za pośrednictwem pulpitu zdalnego. Administratorzy mogą zapewnić obsługę administracyjną dostęp do zasobów w domenie przy użyciu istniejącego członkostwa w grupach. Aplikacje wdrożone na maszynach wirtualnych w sieci wirtualnej można użyć funkcji, takich jak przyłączanie do domeny, LDAP odczytu LDAP bind, uwierzytelniania NTLM i Kerberos i zasad grupy.

Oto kilka istotne aspekty domeny zarządzanej, które jest udostępniane przez usługi domenowe Azure AD:

* Firmy Contoso IT administrator nie musi zarządzać, patch lub monitorowanie ta domena lub dowolnym kontrolerze domeny dla tej domeny zarządzanej.
* Nie istnieje potrzeba do zarządzania replikacją AD dla tej domeny. Konta użytkowników, członkostwa w grupach i poświadczeń z dzierżawy usługi Azure AD firmy Contoso są automatycznie dostępne w ramach tej domeny zarządzanej.
* Ponieważ domena jest zarządzana przez usługi domenowe Azure AD, Contoso przez administratora IT nie ma uprawnień administratora domeny lub administratora przedsiębiorstwa w tej domenie.

### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Usługi domenowe Azure AD dla organizacji hybrydowego
Organizacje z hybrydowym infrastruktury IT używać różnych zasobów w chmurze i zasobów lokalnych. Takie organizacje synchronizacja informacji z ich katalogu lokalnego do swojej dzierżawy usługi Azure AD. Przy migracji więcej organizacje hybrydowych aplikacji lokalnych do chmury, szczególnie starszych aplikacji obsługujących katalogu, usługi domenowe Azure AD może być przydatne do nich.

Wdrożono litware Corporation [Azure AD Connect](../active-directory/active-directory-aadconnect.md), aby zsynchronizować informacje tożsamość swojej dzierżawy usługi Azure AD z katalogiem lokalnym. Informacje o tożsamości, który jest synchronizowany zawiera konta użytkowników, ich skrótów poświadczeń uwierzytelniania (synchronizacja haseł) i członkostwa w grupach.

> [!NOTE]
> **Synchronizacja haseł jest obowiązkowa w przypadku hybrydowych organizacji korzystanie z usług domenowych Azure AD**. To wymaganie dotyczy, ponieważ są wymagane poświadczenia użytkowników domeny zarządzanej udostępniane przez usługi domenowe Azure AD do uwierzytelniania użytkowników za pomocą metody uwierzytelniania NTLM lub Kerberos.
>
>

![W przypadku Litware usługi domenowe Azure AD](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

Wcześniejsza ilustracja pokazuje, jak organizacji mających hybrydowej infrastruktury IT, takich jak Litware Corporation, można użyć usług domenowych Azure AD. Aplikacje i obciążenia serwera, które wymagają usług domenowych w usłudze litware's są wdrażane w sieci wirtualnej w usługi infrastruktury platformy Azure. Litware's administratora IT mogą włączyć usługi domenowe Azure AD dla swojej dzierżawy usługi Azure AD i wybierz opcję Udostępnianie domeny zarządzanej w tej sieci wirtualnej. Ponieważ Litware organizacji z hybrydowej infrastruktury IT, kont użytkowników, grup i poświadczenia są synchronizowane swojej dzierżawy usługi Azure AD z katalogiem lokalnym. Ta funkcja umożliwia użytkownikom na logowanie się do domeny za pomocą poświadczeń firmowych — na przykład podczas nawiązywania połączenia zdalnego maszyny przyłączone do domeny za pośrednictwem pulpitu zdalnego. Administratorzy mogą zapewnić obsługę administracyjną dostęp do zasobów w domenie przy użyciu istniejącego członkostwa w grupach. Aplikacje wdrożone na maszynach wirtualnych w sieci wirtualnej można użyć funkcji, takich jak przyłączanie do domeny, LDAP odczytu LDAP bind, uwierzytelniania NTLM i Kerberos i zasad grupy.

Oto kilka istotne aspekty domeny zarządzanej, które jest udostępniane przez usługi domenowe Azure AD:

* Zarządzane domena jest domeną autonomicznej. Nie jest rozszerzeniem Litware's lokalnej domeny.
* Litware's IT administrator nie musi zarządzać, poprawki, albo Monitoruj kontrolery domeny dla tej domeny zarządzanej.
* Nie istnieje potrzeba do zarządzania replikacją AD do tej domeny. Konta użytkowników, członkostwa w grupach i poświadczeń z katalogiem lokalnym Litware's są synchronizowane z usługą Azure AD za pomocą usługi Azure AD Connect. Te konta użytkowników, członkostwa w grupach i poświadczeń są automatycznie dostępne w ramach domeny zarządzanej.
* Ponieważ domena jest zarządzana przez usługi domenowe Azure AD, Litware przez administratora IT nie ma uprawnień administratora domeny lub administratora przedsiębiorstwa w tej domenie.

## <a name="benefits"></a>Korzyści
Z usług domenowych Azure AD można korzystać z zalet następujące:

* **Proste** — może zaspokoić potrzeby tożsamość maszyn wirtualnych wdrożonych usług infrastruktury platformy Azure za pomocą kilku kliknięć proste. Nie ma potrzeby wdrażania i zarządzania nimi infrastruktury tożsamości w usłudze Azure lub ustawienia łączności do lokalnej infrastruktury tożsamości.
* **Zintegrowane** — usługi domenowe Azure AD jest ściśle zintegrowana z dzierżawy usługi Azure AD. Usługi Azure AD można teraz używać jak katalog zintegrowanego przedsiębiorstwa oparte na chmurze przeznaczoną na potrzeby nowoczesnych aplikacji i tradycyjnych aplikacjom obsługującym architekturę katalogu.
* **Zgodne** — usługi domenowe Azure AD jest oparty na sprawdzonych infrastrukturze klasy usługi Active Directory systemu Windows Server. W związku z tym aplikacji może polegać na wysoki stopień zgodności z funkcji usługi Active Directory systemu Windows Server. Nie wszystkie funkcje dostępne w systemie Windows Server AD są obecnie dostępne w usługach domenowych Azure AD. Jednak funkcje dostępne są zgodne z odpowiednie funkcje systemu Windows Server AD, które zależą w infrastrukturze lokalnej. Możliwości sprzężenia LDAP, protokołu Kerberos, NTLM, zasady grupy i domeny stanowią dojrzałe oferty, które zostały przetestowane i wprowadzono ulepszenia w różnych wersjach systemu Windows Server.
* **Ekonomiczne** — z usługami domenowymi w usłudze Azure AD, można uniknąć obciążenia infrastruktury i zarządzania skojarzony z zarządzania infrastrukturą tożsamości do obsługi tradycyjnych obsługujący katalogu aplikacji. Można przenieść te aplikacje do usługi infrastruktury platformy Azure i korzystać z niższych kosztów operacyjnych.


## <a name="next-steps"></a>Kolejne kroki
### <a name="learn-more-about-azure-ad-domain-services"></a>Dowiedz się więcej o usługach domenowych Azure AD
* [Funkcje](active-directory-ds-features.md)
* [Scenariusze wdrażania](active-directory-ds-scenarios.md)
* [Dowiedz się, czy usługi domenowe Azure AD pasujące do przypadków użycia](active-directory-ds-comparison.md)
* [Zrozumienie, jak usługi domenowe Azure AD synchronizacji z katalogiem usługi Azure AD](active-directory-ds-synchronization.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Wprowadzenie do usług Azure AD Domain Services
* [Włączanie usług domenowych Azure AD przy użyciu portalu Azure](active-directory-ds-getting-started.md)
