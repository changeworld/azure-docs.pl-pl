---
title: Omówienie usług domenowych Active Directory platformy Azure | Dokumenty firmy Microsoft
description: W tym przeglądzie dowiesz się, co zapewnia usługi domenowe Active Directory platformy Azure i jak używać ich w organizacji do świadczenia usług tożsamości aplikacjom i usługom w chmurze.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: cf961b00c4823d0c9efcf44b0db7bb75a80d3dfa
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474320"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Co to są Usługi domenowe Active Directory platformy Azure?

Usługi domenowe Usługi Active Directory (Azure AD DS) zapewniają usługi domeny zarządzanej, takie jak dołączanie do domeny, zasady grupy, protokół LDAP (Lightweight Directory Access Protocol) i uwierzytelnianie Kerberos / NTLM, które jest w pełni zgodne z usługą Active Directory systemu Windows Server. Te usługi domeny są używane bez konieczności wdrażania, zarządzania i instalowania poprawek kontrolerów domeny w chmurze. Usługi Azure AD DS integruje się z istniejącą dzierżawą usługi Azure AD, co umożliwia użytkownikom logowanie się przy użyciu istniejących poświadczeń. Można również użyć istniejących grup i kont użytkowników, aby zabezpieczyć dostęp do zasobów, co zapewnia płynniejsze podnoszenie i przenoszenie zasobów lokalnych na platformę Azure.

Usługi Azure AD DS replikują informacje o tożsamości z usługi Azure AD, więc współpracuje z dzierżawami usługi Azure AD, które są tylko w chmurze lub synchronizowane z lokalnym środowiskiem usług domenowych Active Directory (AD DS). Ten sam zestaw funkcji usługi Azure AD DS istnieje dla obu środowisk.

* Jeśli masz istniejące lokalne środowisko usług AD DS, możesz zsynchronizować informacje o koncie użytkownika, aby zapewnić spójną tożsamość użytkowników.
* W środowiskach tylko w chmurze nie potrzebujesz tradycyjnego lokalnego środowiska usług AD DS, aby korzystać ze scentralizowanych usług tożsamości usług Azure AD DS.

Poniższy klip wideo zawiera omówienie sposobu integrowania usług Azure AD DS z aplikacjami i obciążeniami w celu świadczenia usług tożsamości w chmurze:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Typowe sposoby dostarczania rozwiązań tożsamości w chmurze

Podczas migracji istniejących obciążeń do chmury aplikacje obsługujące katalogi mogą używać protokołu LDAP do odczytu lub zapisu dostępu do lokalnego katalogu usług AD DS. Aplikacje uruchamiane w systemie Windows Server są zazwyczaj wdrażane na maszynach wirtualnych przyłączonych do domeny, dzięki czemu można nimi bezpiecznie zarządzać za pomocą zasad grupy. Aby uwierzytelnić użytkowników końcowych, aplikacje mogą również polegać na uwierzytelnianiu zintegrowanym z systemem Windows, takim jak uwierzytelnianie Kerberos lub NTLM.

Administratorzy IT często używają jednego z następujących rozwiązań do świadczenia usługi tożsamości dla aplikacji uruchamianych na platformie Azure:

* Konfigurowanie połączenia sieci VPN między lokacją a lokacją między obciążeniami uruchamianym na platformie Azure a lokalnym środowiskiem usług AD DS.
    * Lokalne kontrolery domeny zapewniają uwierzytelnianie za pośrednictwem połączenia sieci VPN.
* Tworzenie replik kontrolerów domeny przy użyciu maszyn wirtualnych platformy Azure (VMs) w celu rozszerzenia domeny usług AD DS / lasu z lokalnego.
    * Kontrolery domeny uruchamiane na maszynach wirtualnych platformy Azure zapewniają uwierzytelnianie i replikowanie informacji katalogowych między lokalnym środowiskiem usług AD DS.
* Wdrażanie autonomicznego środowiska usług AD DS na platformie Azure przy użyciu kontrolerów domeny uruchamianych na maszynach wirtualnych platformy Azure.
    * Kontrolery domeny uruchamiane na maszynach wirtualnych platformy Azure zapewniają uwierzytelnianie, ale nie ma żadnych informacji katalogowych replikowanych z lokalnego środowiska usług AD DS.

Dzięki tym metodom połączenia sieci VPN z katalogiem lokalnym sprawiają, że aplikacje są narażone na przejściowe usterki lub awarie sieci. Jeśli wdrożysz kontrolery domeny przy użyciu maszyn wirtualnych na platformie Azure, zespół IT musi zarządzać maszynami wirtualnymi, a następnie zabezpieczyć, łatać, monitorować, wykonać kopię zapasową i rozwiązywać problemy z nimi.

Usługi Azure AD DS oferuje alternatywy dla konieczności tworzenia połączeń sieci VPN z powrotem do lokalnego środowiska usług AD DS lub uruchamiania maszyn wirtualnych na platformie Azure i zarządzania nimi w celu świadczenia usług tożsamości. Jako usługa zarządzana usługa Azure AD DS zmniejsza złożoność tworzenia zintegrowanego rozwiązania tożsamości dla środowisk hybrydowych i tylko w chmurze.

## <a name="azure-ad-ds-features-and-benefits"></a>Funkcje i korzyści usługi Azure AD DS

Aby zapewnić usługi tożsamości aplikacjom i maszynom wirtualnym w chmurze, usługa Azure AD DS jest w pełni zgodna z tradycyjnym środowiskiem usług AD DS dla operacji, takich jak przyłączanie do domeny, bezpieczne LDAP (LDAPS), zasady grupy, zarządzanie dns i obsługa powiązania LDAP i odczytu. Obsługa zapisu LDAP jest dostępna dla obiektów utworzonych w domenie zarządzanej usługi Azure AD DS, ale nie zasobów zsynchronizowanych z usługi Azure AD. Następujące funkcje usługi Azure AD DS upraszczają operacje wdrażania i zarządzania:

* **Uproszczone środowisko wdrażania:** Usługi Azure AD DS jest włączona dla dzierżawy usługi Azure AD przy użyciu jednego kreatora w witrynie Azure portal.
* **Zintegrowane z usługą Azure AD:** Konta użytkowników, członkostwa w grupach i poświadczenia są automatycznie dostępne w dzierżawie usługi Azure AD. Nowi użytkownicy, grupy lub zmiany atrybutów z dzierżawy usługi Azure AD lub lokalnego środowiska usług AD DS są automatycznie synchronizowane z usługą Azure AD DS.
    * Konta w katalogach zewnętrznych połączonych z usługą Azure AD nie są dostępne w usługach Azure AD DS. Poświadczenia nie są dostępne dla tych katalogów zewnętrznych, więc nie można zsynchronizować z domeną zarządzaną usługą Azure AD DS.
* **Użyj firmowych poświadczeń/haseł:** Hasła dla użytkowników w usługach Azure AD DS są takie same jak w dzierżawie usługi Azure AD. Użytkownicy mogą używać swoich poświadczeń firmowych do dołączania do domeny maszyn, logowania się interakcyjnie lub za pomocą pulpitu zdalnego i uwierzytelniania w domenie zarządzanej usług Azure AD DS.
* **Uwierzytelnianie NTLM i Kerberos:** Dzięki obsłudze uwierzytelniania NTLM i Protokołu Kerberos można wdrażać aplikacje oparte na uwierzytelnianiu zintegrowanym z systemem Windows.
* **Wysoka dostępność:** Usługi Azure AD DS zawiera wiele kontrolerów domeny, które zapewniają wysoką dostępność dla domeny zarządzanej. Ta wysoka dostępność gwarantuje czas pracy bez przestojów i odporność na awarie.
    * W regionach [obsługujących strefy dostępności platformy Azure][availability-zones]te kontrolery domeny są również rozproszone w strefach w celu zwiększenia odporności.

Niektóre kluczowe aspekty domeny zarządzanej usług Azure AD DS są następujące:

* Domena zarządzana usługą Azure AD DS jest domeną autonomiczną. Nie jest rozszerzeniem domeny lokalnej.
    * W razie potrzeby można utworzyć jednokierunkowe wychodzące relacje zaufania lasu z usług Azure AD DS do lokalnego środowiska usług AD DS. Aby uzyskać więcej informacji, zobacz [Pojęcia i funkcje lasu zasobów dla usług Azure AD DS][ forest-trusts].
* Twój zespół IT nie musi zarządzać kontrolerami domeny usługi Azure AD DS, łatać ani monitorować kontrolerów domeny.

W przypadku środowisk hybrydowych, które uruchamiają usługi AD DS lokalnie, nie trzeba zarządzać replikacją usługi AD do domeny zarządzanej usług Azure AD DS. Konta użytkowników, członkostwa w grupach i poświadczenia z katalogu lokalnego są synchronizowane z usługą Azure AD za pośrednictwem [usługi Azure AD Connect.][azure-ad-connect] Te konta użytkowników, członkostwa w grupach i poświadczenia są automatycznie dostępne w domenie zarządzanej usług Azure AD DS.

## <a name="how-does-azure-ad-ds-work"></a>Jak działa usługa Azure AD DS?

Aby zapewnić usługi tożsamości, platforma Azure tworzy wystąpienie usług AD DS w wybranej sieci wirtualnej. W tle tworzone są pary kontrolerów domeny systemu Windows Server, które są uruchamiane na maszynach wirtualnych platformy Azure. Nie trzeba zarządzać, konfigurować ani aktualizować tych kontrolerów domeny. Platforma Azure zarządza kontrolerami domeny w ramach usługi Azure AD DS.

Domena zarządzana usługą Azure AD DS jest skonfigurowana do wykonywania jednokierunkowej synchronizacji z usługi Azure AD w celu zapewnienia dostępu do centralnego zestawu użytkowników, grup i poświadczeń. Zasoby można tworzyć bezpośrednio w domenie zarządzanej usług Azure AD DS, ale nie są one synchronizowane z powrotem do usługi Azure AD. Aplikacje, usługi i maszyny wirtualne na platformie Azure, które łączą się z tą siecią wirtualną, mogą następnie używać typowych funkcji usług AD DS, takich jak dołączanie do domeny, zasady grupy, LDAP i uwierzytelnianie Kerberos / NTLM.

W środowisku hybrydowym z lokalnym środowiskiem usług AD DS [usługa Azure AD Connect][azure-ad-connect] synchronizuje informacje o tożsamości z usługą Azure AD, która jest następnie synchronizowana z usługą Azure AD DS.

![Synchronizacja w usługach domenowych usługi Azure AD z usługą Azure AD i lokalnymi usługami domenowymi Active Directory przy użyciu usługi AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Aby wyświetlić usługi Azure AD DS w akcji, przyjrzyjmy się kilku przykładom:

* [Usługi Azure AD DS dla organizacji hybrydowych](#azure-ad-ds-for-hybrid-organizations)
* [Usługi Azure AD DS dla organizacji tylko w chmurze](#azure-ad-ds-for-cloud-only-organizations)

### <a name="azure-ad-ds-for-hybrid-organizations"></a>Usługi Azure AD DS dla organizacji hybrydowych

Wiele organizacji uruchamia infrastrukturę hybrydową, która obejmuje zarówno obciążenia aplikacji w chmurze, jak i lokalne. Starsze aplikacje migrowane na platformę Azure w ramach strategii windy i zmiany mogą używać tradycyjnych połączeń LDAP w celu dostarczania informacji o tożsamości. Aby obsługiwać tę infrastrukturę hybrydową, informacje o tożsamości z lokalnego środowiska usług AD DS można zsynchronizować z dzierżawą usługi Azure AD. Usługa Azure AD DS udostępnia następnie te starsze aplikacje na platformie Azure ze źródłem tożsamości, bez konieczności konfigurowania i zarządzania łącznością aplikacji z lokalnymi usługami katalogowymi.

Przyjrzyjmy się przykładowi dla Litware Corporation, organizacji hybrydowej, która uruchamia zarówno zasoby lokalne, jak i platformy Azure:

![Usługi domenowe Usługi domenowe Active Directory platformy Azure dla organizacji hybrydowej, która obejmuje synchronizację lokalną](./media/overview/synced-tenant.png)

* Aplikacje i obciążenia serwerów, które wymagają usług domeny są wdrażane w sieci wirtualnej na platformie Azure.
    * Może to obejmować starsze aplikacje migrowane na platformę Azure w ramach strategii podnoszenia i zmiany.
* Aby zsynchronizować informacje o tożsamości z katalogu lokalnego z dzierżawą usługi Azure AD, firma Litware Corporation wdraża usługę [Azure AD Connect.][azure-ad-connect]
    * Informacje o tożsamości, które są synchronizowane, obejmują konta użytkowników i członkostwo w grupach.
* Zespół IT firmy Litware umożliwia usługi Azure AD DS dla dzierżawy usługi Azure AD w tej lub równorzędnej sieci wirtualnej.
* Aplikacje i maszyny wirtualne wdrożone w sieci wirtualnej platformy Azure można następnie użyć funkcji usługi Azure AD DS, takich jak sprzężenie domeny, odczyt LDAP, powiązanie LDAP, uwierzytelnianie NTLM i Kerberos oraz zasady grupy.

> [!IMPORTANT]
> Usługa Azure AD Connect powinna być instalowana i konfigurowana tylko do synchronizacji z lokalnymi środowiskami usług AD DS. Nie jest obsługiwana instalacja usługi Azure AD Connect w domenie zarządzanej usług Azure AD DS w celu synchronizowania obiektów z powrotem do usługi Azure AD.

### <a name="azure-ad-ds-for-cloud-only-organizations"></a>Usługi Azure AD DS dla organizacji tylko w chmurze

Dzierżawa usługi Azure AD tylko w chmurze nie ma lokalnego źródła tożsamości. Konta użytkowników i członkostwa w grupach, na przykład, są tworzone i zarządzane bezpośrednio w usłudze Azure AD.

Teraz przyjrzyjmy się przykład contoso, tylko w chmurze organizacji, która używa usługi Azure AD dla tożsamości. Wszystkie tożsamości użytkowników, ich poświadczenia i członkostwa w grupach są tworzone i zarządzane w usłudze Azure AD. Nie ma żadnej dodatkowej konfiguracji usługi Azure AD Connect, aby zsynchronizować wszelkie informacje o tożsamości z katalogu lokalnego.

![Usługi domenowe Active Directory platformy Azure dla organizacji tylko w chmurze bez synchronizacji lokalnej](./media/overview/cloud-only-tenant.png)

* Aplikacje i obciążenia serwerów, które wymagają usług domeny są wdrażane w sieci wirtualnej na platformie Azure.
* Zespół IT firmy Contoso umożliwia usługi Azure AD DS dla dzierżawy usługi Azure AD w tej lub równorzędnej sieci wirtualnej.
* Aplikacje i maszyny wirtualne wdrożone w sieci wirtualnej platformy Azure można następnie użyć funkcji usługi Azure AD DS, takich jak sprzężenie domeny, odczyt LDAP, powiązanie LDAP, uwierzytelnianie NTLM i Kerberos oraz zasady grupy.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o usługach Azure AD DS w porównaniu z innymi rozwiązaniami tożsamości i jak działa synchronizacja, zobacz następujące artykuły:

* [Porównanie usług Azure AD DS z usługą Azure AD, usługami domenowymi Active Directory na maszynach wirtualnych platformy Azure i lokalnymi usługami domenowymi Active Directory][compare]
* [Dowiedz się, jak usługi domenowe usługi Azure AD synchronizują się z katalogiem usługi Azure AD][synchronization]

Aby rozpocząć, [utwórz domenę zarządzaną usługą Azure AD DS przy użyciu witryny Azure portal][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
