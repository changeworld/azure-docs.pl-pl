---
title: Przegląd Azure Active Directory Domain Services | Microsoft Docs
description: W tym przeglądzie dowiesz się, co zapewnia Azure Active Directory Domain Services i jak korzystać z niego w organizacji w celu zapewnienia usług tożsamości dla aplikacji i usług w chmurze.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: 921266b78b82364b4610dcd74b6ee16ee44cb060
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617232"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Co to jest Azure Active Directory Domain Services?

Azure Active Directory Domain Services (Azure AD DS) oferuje zarządzane usługi domenowe, takie jak przyłączanie do domeny, zasady grupy, LDAP oraz uwierzytelnianie Kerberos/NTLM, które jest w pełni zgodne z systemem Windows Server Active Directory. Te usługi domenowe są używane bez konieczności wdrażania i poprawiania kontrolerów domeny w chmurze oraz zarządzania nimi. Usługa Azure AD DS integruje się z istniejącą dzierżawą usługi Azure AD, co umożliwia użytkownikom logowanie się przy użyciu istniejących poświadczeń. Istnieje również możliwość użycia istniejących grup i kont użytkowników w celu zabezpieczenia dostępu do zasobów, co zapewnia płynne podniesienie i przesunięcia zasobów lokalnych na platformę Azure.

Usługa Azure AD DS replikuje informacje o tożsamościach z usługi Azure AD, więc współpracuje z dzierżawami usługi Azure AD, które są przeznaczone tylko do chmury, lub zsynchronizowane ze środowiskiem lokalnym Active Directory Domain Services (AD DS). Jeśli masz istniejące lokalne środowisko AD DS, możesz synchronizować informacje o kontach użytkowników, aby zapewnić użytkownikom spójną tożsamość. W przypadku środowisk opartych tylko na chmurze nie jest wymagane tradycyjne lokalne środowisko AD DS do używania scentralizowanych usług tożsamości usługi Azure AD DS. Ten sam zestaw funkcji platformy Azure AD DS istnieje w obu środowiskach.

Poniższy klip wideo zawiera Omówienie integracji usługi Azure AD DS z aplikacjami i obciążeniami w celu zapewnienia usług tożsamości w chmurze:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Typowe sposoby dostarczania rozwiązań do obsługi tożsamości w chmurze

Podczas migrowania istniejących obciążeń do chmury aplikacje obsługujące katalogi mogą używać protokołu LDAP do odczytu lub zapisu w lokalnym katalogu AD DS. Aplikacje uruchamiane w systemie Windows Server są zwykle wdrażane na przyłączonych do domeny, aby można je było bezpiecznie zarządzać przy użyciu zasady grupy. W celu uwierzytelnienia użytkowników końcowych aplikacje mogą również polegać na uwierzytelnianiu zintegrowanym z systemem Windows, takim jak uwierzytelnianie Kerberos lub NTLM.

Administratorzy IT często używają jednego z następujących rozwiązań, aby zapewnić usłudze tożsamości aplikacje działające na platformie Azure:

* Skonfiguruj połączenie sieci VPN typu lokacja-lokacja między obciążeniami uruchomionymi na platformie Azure a lokalnym środowiskiem AD DS.
* Utwórz repliki kontrolerów domeny za pomocą usługi Azure Virtual Machines (VM), aby zwiększyć AD DS domeny/lasu.
* Wdróż autonomiczne środowisko AD DS na platformie Azure przy użyciu kontrolerów domeny działających na maszynach wirtualnych platformy Azure.

Korzystając z tych metod, połączenia sieci VPN z katalogiem lokalnym sprawiają, że aplikacje są podatne na przejściowe awarie sieciowe lub przestoje. W przypadku wdrażania kontrolerów domeny przy użyciu maszyn wirtualnych na platformie Azure maszyny wirtualne zespołu IT muszą zarządzać, zabezpieczać, poprawiać, monitorować, tworzyć kopie zapasowe i rozwiązywać problemy.

Usługa Azure AD DS oferuje alternatywy dla potrzeb tworzenia połączeń sieci VPN z powrotem do lokalnego środowiska AD DS lub uruchamiania maszyn wirtualnych i zarządzania nimi na platformie Azure w celu zapewnienia usług tożsamości. Jako usługa zarządzana platforma Azure AD DS zmniejsza złożoność tworzenia zintegrowanego rozwiązania do obsługi tożsamości zarówno dla środowisk hybrydowych, jak i chmurowych.

## <a name="azure-ad-ds-features-and-benefits"></a>Funkcje i zalety platformy Azure AD DS

Aby zapewnić obsługę tożsamości dla aplikacji i maszyn wirtualnych w chmurze, platforma Azure AD DS jest w pełni zgodna z tradycyjnym środowiskiem AD DS dla operacji takich jak przyłączanie do domeny, bezpieczne LDAP (LDAPs), zarządzanie zasady grupy i DNS oraz obsługa powiązań LDAP i odczytu. Obsługa zapisu LDAP jest dostępna dla obiektów utworzonych w domenie zarządzanej AD DS platformy Azure, ale nie zasobów synchronizowanych z usługi Azure AD. Następujące funkcje platformy Azure AD DS upraszczają operacje wdrażania i zarządzania:

* **Uproszczone środowisko wdrażania:** Usługa Azure AD DS jest włączona dla dzierżawy usługi Azure AD przy użyciu jednego kreatora w Azure Portal.
* **Integracja z usługą Azure AD:** Konta użytkowników, członkostwa w grupach i poświadczenia są automatycznie dostępne z dzierżawy usługi Azure AD. Nowi użytkownicy, grupy lub zmiany atrybutów z dzierżawy usługi Azure AD lub lokalnego środowiska AD DS są automatycznie synchronizowane z usługą Azure AD DS.
* **Użyj poświadczeń/haseł firmowej:** Hasła użytkowników w dzierżawie usługi Azure AD współpracują z usługą Azure AD DS. Użytkownicy mogą używać swoich poświadczeń firmowych do przyłączania do domeny komputerów, logować się interaktywnie lub za pośrednictwem pulpitu zdalnego i uwierzytelniać się w domenie zarządzanej platformy Azure AD DS.
* **Uwierzytelnianie NTLM i Kerberos:** Dzięki obsłudze uwierzytelniania NTLM i Kerberos można wdrażać aplikacje korzystające z uwierzytelniania zintegrowanego z systemem Windows.
* **Wysoka dostępność:** Usługa Azure AD DS obejmuje wiele kontrolerów domeny, które zapewniają wysoką dostępność dla domeny zarządzanej. Ta wysoka dostępność gwarantuje czas pracy usługi i odporność na awarie.

Poniżej przedstawiono niektóre kluczowe aspekty domeny zarządzanej platformy Azure AD DS:

* Domena zarządzana AD DS platformy Azure jest domeną autonomiczną. Nie jest to rozszerzenie domeny lokalnej.
* Twój zespół IT nie musi zarządzać, poprawiać ani monitorować kontrolerów domeny dla tej domeny zarządzanej platformy Azure AD DS.

W przypadku środowisk hybrydowych, które są uruchamiane AD DS lokalnie, nie trzeba zarządzać replikacją usługi AD do domeny zarządzanej AD DS platformy Azure. Konta użytkowników, członkostwa w grupach i poświadczenia z katalogu lokalnego są synchronizowane z usługą Azure AD za pośrednictwem Azure AD Connect. Te konta użytkowników, członkostwa w grupach i poświadczenia są automatycznie dostępne w ramach domeny zarządzanej AD DS platformy Azure.

## <a name="how-does-azure-ad-ds-work"></a>Jak działa usługa Azure AD DS?

Aby zapewnić usługi tożsamości, platforma Azure tworzy domenę zarządzaną AD DS dostępną w wybranej sieci wirtualnej. W tle i bez konieczności zarządzania, zabezpieczania i aktualizowania nadmiarowości jest zapewniana przez parę kontrolerów domeny systemu Windows Server. Domena zarządzana AD DS Azure jest skonfigurowana tak, aby przeprowadzać jednokierunkową synchronizację z usługi Azure AD w celu zapewnienia dostępu do centralnego zestawu użytkowników, grup i poświadczeń. Zasoby można tworzyć bezpośrednio w domenie zarządzanej AD DS platformy Azure, ale nie są one zsynchronizowane z powrotem z usługą Azure AD. Aplikacje, usługi i maszyny wirtualne na platformie Azure, które łączą się z tą siecią wirtualną, mogą używać typowych funkcji AD DS, takich jak przyłączanie do domeny, zasady grupy, LDAP i uwierzytelnianie Kerberos/NTLM. W środowisku hybrydowym z lokalnym środowiskiem AD DS [Azure AD Connect][azure-ad-connect] synchronizuje informacje o tożsamości z usługą Azure AD.

![Synchronizacja w Azure AD Domain Services z usługą Azure AD i lokalnym Active Directory Domain Services przy użyciu programu AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Aby zobaczyć AD DS platformy Azure w działaniu, przyjrzyjmy się kilku przykładom:

* [AD DS platformy Azure dla organizacji hybrydowych](#azure-ad-ds-for-hybrid-organizations)
* [AD DS platformy Azure dla organizacji tylko w chmurze](#azure-ad-ds-for-cloud-only-organizations)

### <a name="azure-ad-ds-for-hybrid-organizations"></a>AD DS platformy Azure dla organizacji hybrydowych

Wiele organizacji uruchamia infrastrukturę hybrydową obejmującą zarówno obciążenia aplikacji w chmurze, jak i lokalne. Starsze aplikacje migrowane do platformy Azure w ramach strategii podnoszenia i przesunięcia mogą nadal używać tradycyjnych połączeń LDAP do dostarczania informacji o tożsamości. Aby zapewnić obsługę tej infrastruktury hybrydowej, informacje o tożsamościach ze środowiska lokalnego Active Directory Domain Services (AD DS) można synchronizować z dzierżawą usługi Azure AD. Usługa Azure AD DS może następnie udostępnić te starsze aplikacje na platformie Azure przy użyciu źródła tożsamości, bez konieczności konfigurowania i zarządzania łącznością aplikacji z powrotem do lokalnych usług katalogowych.

Przyjrzyjmy się przykładowi do litware Corporation — organizacji hybrydowej, która działa zarówno lokalnie, jak i zasoby platformy Azure:

![Azure Active Directory Domain Services organizacji hybrydowej, która obejmuje synchronizację lokalną](./media/overview/synced-tenant.png)

* Obciążenia aplikacji i serwera, które wymagają usług domenowych, są wdrażane w sieci wirtualnej na platformie Azure.
    * Może to obejmować starsze aplikacje migrowane do platformy Azure w ramach strategii podnoszenia i przesunięcia.
* Aby synchronizować informacje o tożsamości z katalogu lokalnego do swojej dzierżawy usługi Azure AD, firma litware Corporation wdraża [Azure AD Connect][azure-ad-connect].
    * Synchronizowane informacje o tożsamości obejmują konta użytkowników i członkostwa w grupach.
* Zespół IT litware umożliwia korzystanie z platformy AD DS Azure w ramach tej dzierżawy usługi Azure AD w ramach tej lub komunikacji równorzędnej sieci wirtualnej.
* Aplikacje i maszyny wirtualne wdrożone w usłudze Azure Virtual Network mogą następnie używać funkcji AD DS platformy Azure, takich jak przyłączanie do domeny, odczyt LDAP, powiązanie LDAP, uwierzytelnianie NTLM i Kerberos oraz zasady grupy.

### <a name="azure-ad-ds-for-cloud-only-organizations"></a>AD DS platformy Azure dla organizacji tylko w chmurze

Dzierżawa usługi Azure AD tylko w chmurze nie ma lokalnego źródła tożsamości. Konta użytkowników i członkostwa w grupach, na przykład, są tworzone i zarządzane w usłudze Azure AD.

Teraz przyjrzyjmy się przykładowi do firmy Contoso — organizacji tylko w chmurze, która korzysta tylko z usługi Azure AD na potrzeby tożsamości. Wszystkie tożsamości użytkowników, ich poświadczenia i członkostwa w grupach są tworzone i zarządzane w usłudze Azure AD. Nie ma dodatkowej konfiguracji Azure AD Connect do synchronizowania informacji o tożsamości z katalogu lokalnego.

![Azure Active Directory Domain Services organizacji tylko w chmurze bez synchronizacji lokalnej](./media/overview/cloud-only-tenant.png)

* Obciążenia aplikacji i serwera, które wymagają usług domenowych, są wdrażane w sieci wirtualnej na platformie Azure.
* Zespół IT firmy Contoso umożliwia korzystanie z platformy Azure AD DS w ramach tej dzierżawy usługi Azure AD w ramach tej lub komunikacji równorzędnej sieci wirtualnej.
* Aplikacje i maszyny wirtualne wdrożone w usłudze Azure Virtual Network mogą następnie używać funkcji AD DS platformy Azure, takich jak przyłączanie do domeny, odczyt LDAP, powiązanie LDAP, uwierzytelnianie NTLM i Kerberos oraz zasady grupy.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o platformie Azure AD DS porównać z innymi rozwiązaniami do tworzenia tożsamości i jak działa synchronizacja, zobacz następujące artykuły:

* [Porównanie AD DS platformy Azure z usługą Azure AD, Active Directory Domain Services na maszynach wirtualnych platformy Azure i Active Directory Domain Services lokalnych][compare]
* [Dowiedz się, jak Azure AD Domain Services synchronizuje się z katalogiem usługi Azure AD][synchronization]

Aby rozpocząć, [Utwórz domenę zarządzaną platformy Azure AD DS przy użyciu Azure Portal][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-hybrid-identity.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
