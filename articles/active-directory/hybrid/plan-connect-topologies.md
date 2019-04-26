---
title: 'Program Azure AD Connect: Obsługiwane topologie | Dokumentacja firmy Microsoft'
description: Ten temat wyszczególnia obsługiwane i nieobsługiwane topologie programu Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: conceptual
ms.date: 11/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b1c0d33a7d920f76bcbea6d8d6babc7390003bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60383761"
---
# <a name="topologies-for-azure-ad-connect"></a>Topologie obsługiwane w programie Azure AD Connect
W tym artykule opisano różne lokalne i topologii usługi Azure Active Directory (Azure AD), korzystających z synchronizacji programu Azure AD Connect jako rozwiązania do integracji kluczy. Ten artykuł zawiera zarówno obsługiwanych i nieobsługiwanych konfiguracji.


Oto legendy dla obrazów znajdujących się w artykule:

| Opis | Symbol |
| --- | --- |
| W środowisku lokalnym lesie usługi Active Directory |![W środowisku lokalnym lesie usługi Active Directory](./media/plan-connect-topologies/LegendAD1.png) |
| Lokalnej usługi Active Directory przy użyciu filtrowane importu |![Usługi Active Directory z filtrowanych importu](./media/plan-connect-topologies/LegendAD2.png) |
| Serwer synchronizacji programu Azure AD Connect |![Serwer synchronizacji programu Azure AD Connect](./media/plan-connect-topologies/LegendSync1.png) |
| Usługa Azure AD Connect serwer synchronizacji "staging tryb" |![Usługa Azure AD Connect serwer synchronizacji "staging tryb"](./media/plan-connect-topologies/LegendSync2.png) |
| GALSync za pomocą programu Forefront Identity Manager (FIM) 2010 lub Microsoft Identity Manager (MIM) 2016 |![GALSync za pomocą programu FIM 2010 lub programu MIM 2016](./media/plan-connect-topologies/LegendSync3.png) |
| Serwer synchronizacji programu Azure AD Connect, szczegółowe |![Serwer synchronizacji programu Azure AD Connect, szczegółowe](./media/plan-connect-topologies/LegendSync4.png) |
| Azure AD |![Usługa Azure Active Directory](./media/plan-connect-topologies/LegendAAD.png) |
| Nieobsługiwany scenariusz |![Nieobsługiwany scenariusz](./media/plan-connect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> Firma Microsoft nie obsługuje modyfikowania ani działania synchronizacji Azure AD Connect poza konfiguracje lub akcje, które zostały formalnie udokumentowane. Te konfiguracje lub akcji może spowodować niespójny lub nieobsługiwany stan synchronizacji programu Azure AD Connect. W związku z tym firma Microsoft nie może świadczyć pomocy technicznej w przypadku takich wdrożeń.


## <a name="single-forest-single-azure-ad-tenant"></a>Pojedynczy las, pojedynczy dzierżawy usługi Azure AD
![Topologia jednego lasu i pojedynczej dzierżawy](./media/plan-connect-topologies/SingleForestSingleDirectory.png)

Najbardziej typowe topologia jest pojedynczego lokalnego lasu za pomocą jednego lub wielu domen i pojedynczej usłudze Azure AD dzierżawy. Uwierzytelnianie usługi Azure AD używany jest synchronizacja skrótów haseł. Ekspresowa instalacja programu Azure AD Connect obsługuje tylko tej topologii.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Pojedynczy las, wiele serwerów synchronizacji do jednej dzierżawy usługi Azure AD
![Nieobsługiwana filtrowane topologia przypadku pojedynczego lasu](./media/plan-connect-topologies/SingleForestFilteredUnsupported.png)

Posiadanie wielu serwerów synchronizacji Azure AD Connect, połączone z tą samą dzierżawą usługi Azure AD nie jest obsługiwana, z wyjątkiem [przemieszczania serwera](#staging-server). Ma on nieobsługiwane, nawet jeśli te serwery są skonfigurowane do synchronizacji z wzajemnie wykluczającym się zestawie obiektów. Użytkownik może mieć uznane za Ta topologia czy wszystkich domen w lesie z jednego serwera nie może się połączyć, czy ma być rozłożenie obciążenia na kilku serwerach.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Wiele lasów, jeden dzierżawy usługi Azure AD
![Topologia wiele lasów i pojedynczej dzierżawy](./media/plan-connect-topologies/MultiForestSingleDirectory.png)

Wiele organizacji ma środowisk z wieloma lasami usługi Active Directory w środowisku lokalnym. Istnieją różne powody występowania więcej niż jednym lesie usługi Active Directory w środowisku lokalnym. Typowym przykładem są projektów z lasów kont zasobów i wynik fuzji lub przejęcia.

Jeśli masz wiele lasów, wszystkie lasy musi być osiągalna przez jeden serwer synchronizacji usługi Azure AD Connect. Nie masz przyłączyć serwer do domeny. Jeśli to konieczne dotrzeć do wszystkich lasów, użytkownik może spowodować przełączenie serwera w sieci obwodowej (znany także jako DMZ, strefa zdemilitaryzowana i podsieć ekranowana).

Kreatora instalacji usługi Azure AD Connect oferuje kilka opcji w celu skonsolidowania użytkowników, którzy są reprezentowane w wielu lasach. Celem jest, że użytkownik jest tylko jeden raz reprezentowana w usłudze Azure AD. Istnieją pewne typowe topologie, które można skonfigurować w ścieżce instalacji niestandardowej w Kreatorze instalacji. Na **unikatowa identyfikacja użytkowników** wybierz odpowiednią opcję, która reprezentuje topologii. Konsolidacja jest skonfigurowany tylko dla użytkowników. Zduplikowane grupy nie są skonsolidowane w konfiguracji domyślnej.

Popularne topologie zostały omówione w sekcjach dotyczących oddzielne topologie [pełnej siatki](#multiple-forests-full-mesh-with-optional-galsync), i [topologii zasób konta](#multiple-forests-account-resource-forest).

Przyjęto założenie, domyślna konfiguracja przedstawiona w usłudze Azure AD Connect sync:

* Każdy użytkownik ma tylko jedno konto włączone, a las, w którym znajduje się to konto służy do uwierzytelniania użytkownika. Jest to założenie dla synchronizacji skrótów haseł, uwierzytelniania przekazywanego i federacji. UserPrincipalName i sourceAnchor/immutableID pochodzą z tego lasu.
* Każdy użytkownik ma tylko jedną skrzynkę pocztową.
* Lasu, który hostuje skrzynki pocztowej użytkownika ma najlepszą jakość danych dla atrybutów widoczne w globalnej listy adresów (GAL) programu Exchange. Jeśli nie ma żadnych skrzynki pocztowej użytkownika dla użytkownika, każdego lasu może służyć do współtworzenia wartości tych atrybutów.
* Jeśli masz połączoną skrzynkę pocztową, istnieje również konto w innym lesie, używane do logowania.

Jeśli środowisko nie jest zgodny z tych założeń, się zdarzyć następujących czynności:

* Jeśli masz więcej niż jednego aktywnego konta lub więcej niż jedną skrzynkę pocztową, aparat synchronizacji wybiera jeden i pomija drugi.
* Połączoną skrzynkę pocztową przy użyciu nie aktywne konta nie są eksportowane do usługi Azure AD. Konto użytkownika nie jest uwzględniona jako element członkowski w dowolnej grupie. Połączoną skrzynkę pocztową w funkcji DirSync zawsze jest przedstawiana jako normalny skrzynki pocztowej. Ta zmiana jest celowo różny sposób w celu lepszej obsługi scenariuszy z wieloma lasami.

Można znaleźć więcej szczegółów w [opis konfiguracji domyślnej](concept-azure-ad-connect-sync-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Wiele lasów, wiele serwerów synchronizacji do jednej dzierżawy usługi Azure AD
![Nieobsługiwana topologii dla wielu lasów i wielu serwerów synchronizacji](./media/plan-connect-topologies/MultiForestMultiSyncUnsupported.png)

Mających więcej niż jeden serwer synchronizacji usługi Azure AD Connect jest podłączony do pojedynczej dzierżawy usługi Azure AD nie jest obsługiwane. Wyjątek polega na użyciu [przemieszczania serwera](#staging-server).

Ta topologia różni się od przedstawionego poniżej w tym **wielu serwerów synchronizacji** połączone na pojedynczej usłudze Azure AD dzierżawy nie jest obsługiwana.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Wiele lasów, jednym serwerem synchronizacji, użytkownicy są reprezentowani w tylko jednym katalogu
![Opcja do reprezentowania tylko raz użytkowników we wszystkich katalogach](./media/plan-connect-topologies/MultiForestUsersOnce.png)

![Sceny wiele lasów i oddzielne topologie](./media/plan-connect-topologies/MultiForestSeparateTopologies.png)

W tym środowisku wszystkich lasów lokalnych są traktowane jako osobne jednostki. Żaden użytkownik nie jest obecny w innym lesie. Każdy las ma swój własny organizacji programu Exchange i ma nie GALSync między lasami. Ta topologia może być sytuacji po połączeniu/nabycia lub w organizacji, gdzie każda jednostka biznesowa działa niezależnie. Te lasy znajdują się w tej samej organizacji w usłudze Azure AD i są wyświetlane z ujednoliconego GAL. Na powyższym rysunku każdy obiekt w każdym lesie jest reprezentowany w jeden raz w magazynie metaverse programu i zagregowane w dzierżawie docelowej usługi Azure AD.

### <a name="multiple-forests-match-users"></a>Wiele lasów: dopasować użytkowników
Wspólne dla wszystkich tych scenariuszach jest fakt, że dystrybucji i grupy zabezpieczeń może zawierać zarówno użytkowników, kontaktów i obce podmioty zabezpieczeń (FSP). FSP są używane w usługach domenowych w usłudze Active Directory (AD DS) do reprezentowania członków z innych lasów w grupie zabezpieczeń. Wszystkie FSP są rozwiązywane do rzeczywistego obiektu w usłudze Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Wiele lasów: Pełna siatka przy użyciu usługi GALSync opcjonalne
![Opcji korzystania z funkcji atrybut poczty do dopasowania, gdy tożsamości użytkowników istnieją w wielu katalogach](./media/plan-connect-topologies/MultiForestUsersMail.png)

![Topologia pełnej sieci dla wielu lasów](./media/plan-connect-topologies/MultiForestFullMesh.png)

Topologia pełnej sieci umożliwia użytkownikom i zasoby, które muszą znajdować się w dowolnym lesie. Zwykle istnieją dwukierunkowe relacje zaufania między lasami.

W przypadku programu Exchange znajduje się w więcej niż jednym lesie, mogą istnieć (opcjonalnie) to rozwiązanie GALSync w środowisku lokalnym. Każdy użytkownik jest następnie reprezentowany jako kontakt w innych lasach. GALSync często jest implementowane za pomocą programu FIM 2010 lub programu MIM 2016. Program Azure AD Connect nie może służyć do GALSync w środowisku lokalnym.

W tym scenariuszu obiekty tożsamości są łączone za pomocą atrybutu poczty. Użytkownik, który ma skrzynkę pocztową w jednym lesie jest sprzężony z kontaktów w innych lasach.

### <a name="multiple-forests-account-resource-forest"></a>Wiele lasów: lasu zasobów dla konta
![Opcji korzystania z funkcji atrybuty ObjectSID i msExchMasterAccountSID do dopasowania, gdy tożsamości istnieje między wieloma katalogami](./media/plan-connect-topologies/MultiForestUsersObjectSID.png)

![Topologią lasu kont zasobów dla wielu lasów](./media/plan-connect-topologies/MultiForestAccountResource.png)

W topologii lasu zasobów dla konta, mają co najmniej jeden *konta* lasów z aktywne konta użytkowników. Masz również co najmniej jeden *zasobów* lasów z wyłączonymi kontami.

W tym scenariuszu wszystkich lasów kont przez zaufania lasów zasobów (przynajmniej jeden). Las zasobów ma zazwyczaj rozszerzonym schematem usługi Active Directory za pomocą programu Exchange i usługi Lync. Usługi wszystkie programu Exchange i usługi Lync, wraz z innymi usługami udostępnionymi, znajdują się w tym lesie. Użytkownicy mają wyłączonego konta użytkownika, w tym lesie, a skrzynki pocztowej jest połączony z lasów kont.

## <a name="office-365-and-topology-considerations"></a>Office 365 i zagadnienia dotyczące topologii
Niektórych obciążeń usługi Office 365 mają pewne ograniczenia na obsługiwane topologie:

| Obciążenie | Ograniczenia |
| --------- | --------- |
| Exchange Online | Aby uzyskać więcej informacji na temat hybrydowe topologie obsługiwane przez usługę Exchange Online, zobacz [hybrydowych wdrożeń z wieloma lasami usługi Active Directory](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype dla firm | Podczas korzystania z wieloma lokalnymi lasami topologią lasu konto zasobu jest obsługiwana. Aby uzyskać więcej informacji, zobacz [środowiska wymagania dotyczące programu Skype dla firm Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Jeśli masz większych organizacji, a następnie należy wziąć pod uwagę do użycia [Office 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) funkcji. Dzięki temu można zdefiniować w regionie centrum danych, które znajdują się zasoby użytkownika.

## <a name="staging-server"></a>Serwer przejściowy
![Serwer przejściowy w topologii](./media/plan-connect-topologies/MultiForestStaging.png)

Środowisko usługi Azure AD Connect obsługuje instalowanie drugi serwer w *Tryb przejściowy*. Serwer, w tym trybie odczytuje dane ze wszystkich podłączonych katalogów, ale nie zapisuje nic w połączonych katalogów. Używa cykl synchronizacji normalne i dlatego ma zaktualizowaną kopię danych tożsamości.

W przypadku awarii, gdy serwer podstawowy ulegnie awarii może zostać przeniesiony do serwer przejściowy. Można to zrobić w kreatorze program Azure AD Connect. Ten drugi serwer może znajdować się w innym centrum danych, ponieważ nie infrastruktury jest współużytkowany z serwera podstawowego. Musisz ręcznie skopiować zmiany konfiguracji wprowadzone na podstawowym serwerze na drugim serwerze.

Serwer przejściowy umożliwia testowanie nowej niestandardowej konfiguracji i wpływ, jaki ma ona na podstawie posiadanych danych. Możesz przejrzeć i dostosowanie konfiguracji. Po zakończeniu nowej konfiguracji został serwer przejściowy serwer stał się aktywne i ustaw stary serwer aktywny tryb przejściowy.

Można również użyć tej metody, aby zamienić serwera synchronizacji usługi active. Przygotuj nowy serwer i ustaw ją na tryb przejściowy. Upewnij się, że jest w dobrym stanie, wyłącz (dzięki czemu active), Tryb przejściowy i zamknąć aktualnie aktywnego serwera.

Użytkownik może mieć więcej niż jeden serwer przejściowy, gdy chcesz mieć wiele kopii zapasowych w różnych centrach danych.

## <a name="multiple-azure-ad-tenants"></a>Wiele dzierżaw usługi Azure AD
Firma Microsoft zaleca posiadanie pojedynczej dzierżawy w usłudze Azure AD dla organizacji.
Zanim użytkownik chce używać wielu dzierżaw usługi Azure AD, zapoznaj się z artykułem [Zarządzanie jednostkami administracyjnymi w usłudze Azure AD](../users-groups-roles/directory-administrative-units.md). Poruszono w nim typowe scenariusze, w którym można korzystać z pojedynczej dzierżawy.

![Topologia wiele lasów i wielu dzierżaw](./media/plan-connect-topologies/MultiForestMultiDirectory.png)

Brak relacji 1:1 między serwera usługi Azure AD Connect sync i dzierżawy usługi Azure AD. Dla każdej dzierżawy usługi Azure AD należy jedna instalacja serwera synchronizacji Azure AD Connect. Wystąpienia dzierżawy usługi Azure AD są izolowane zgodnie z projektem. Oznacza to w jednej dzierżawy nie widzą użytkownicy w dzierżawie usługi. Jeśli chcesz, aby ta separacja, ta konfiguracja jest obsługiwana. W przeciwnym razie należy używać pojedynczego modelu dzierżawy usługi Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Każdego obiektu tylko raz w dzierżawie usługi Azure AD
![Filtrowane topologii w przypadku pojedynczego lasu](./media/plan-connect-topologies/SingleForestFiltered.png)

W tej topologii jeden serwer synchronizacji Azure AD Connect jest podłączony do każdej dzierżawy usługi Azure AD. Serwery synchronizacji Azure AD Connect musi być skonfigurowany w celu filtrowania tak, aby każdy miał wzajemnie wykluczającym się zestawie obiektów do wykonywania operacji. Możesz na przykład ograniczyć każdego serwera do określonej domeny lub jednostki organizacyjnej.

Domena DNS mogą być rejestrowane w tylko jednej dzierżawy usługi Azure AD. Nazwy UPN użytkowników w lokalnym wystąpieniem usługi Active Directory, należy również użyć oddzielnych przestrzeni nazw. Na przykład na powyższym rysunku trzech oddzielnych sufiksy nazw UPN są rejestrowane w lokalnym wystąpieniem usługi Active Directory: contoso.com i fabrikam.com, nadrzędnych. Użytkownicy w każdej domenie usługi Active Directory w środowisku lokalnym, użyj innej przestrzeni nazw.

>[!NOTE]
>Globalne synchronizacji listy adresowej (GalSync) odbywa się automatycznie w tej topologii i wymaga dodatkowych niestandardowych implementacji programu MIM, aby upewnić się, że każda dzierżawa ma pełną globalnego adresu listy (GAL) w programie Exchange Online i Skype dla firm Online.


Ta topologia ma następujące ograniczenia, w przeciwnym razie obsługiwane scenariusze:

* Tylko jedną z dzierżaw usługi Azure AD można włączyć wdrożenie hybrydowe programu Exchange przy użyciu lokalnego wystąpienia usługi Active Directory.
* Urządzenia z systemem Windows 10 może być skojarzony z tylko jedną dzierżawą usługi Azure AD.
* Logowanie jednokrotne (SSO) opcja pojedyncze do uwierzytelniania przekazywanego i synchronizacji skrótu hasła może służyć z tylko jedną dzierżawą usługi Azure AD.

Wymaganie wzajemnie wykluczającym się zestawie obiektów dotyczy również funkcję zapisywania zwrotnego. Niektórych funkcji zapisywania zwrotnego nie są obsługiwane przy użyciu tej topologii, ponieważ zakładają konfiguracji jednego środowiska lokalnego. Te funkcje obejmują:

* Grupa funkcji zapisywania zwrotnego o domyślnych konfiguracjach.
* Zapisywanie zwrotne urządzeń.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Każdy obiekt wielokrotnie w dzierżawie usługi Azure AD
![Nieobsługiwana topologii dla jednego lasu i wielu dzierżaw](./media/plan-connect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Nieobsługiwana topologii dla jednego lasu i obsługę wielu łączników](./media/plan-connect-topologies/SingleForestMultiConnectorsUnsupported.png)

Te zadania nie są obsługiwane:

* Synchronizuj tego samego użytkownika do wielu dzierżaw usługi Azure AD.
* Wprowadź zmiany, aby użytkownicy w jednej dzierżawy usługi Azure AD są wyświetlane jako kontakty w innej dzierżawie usługi Azure AD konfiguracji.
* Zmodyfikuj synchronizacji Azure AD Connect, aby nawiązać połączenie z wieloma dzierżawami usługi Azure AD.

### <a name="galsync-by-using-writeback"></a>GALSync za pomocą funkcji zapisywania zwrotnego
![Nieobsługiwana topologii dla wielu lasów i wiele katalogów przy użyciu usługi GALSync, skupiając się na usłudze Azure AD](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Nieobsługiwana topologii dla wielu lasów i wiele katalogów przy użyciu usługi GALSync, koncentrując się na lokalnej usługi Active Directory](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Dzierżawy usługi Azure AD są izolowane zgodnie z projektem. Te zadania nie są obsługiwane:

* Zmień konfigurację synchronizacji Azure AD Connect można odczytać danych z innej dzierżawy usługi Azure AD.
* Eksportuj użytkowników jako kontakty do innego wystąpienia usługi Active Directory w środowisku lokalnym za pomocą synchronizacji programu Azure AD Connect.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync za pomocą lokalnego serwera synchronizacji
![GALSync w topologii dla wielu lasów i wieloma katalogami](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync.png)

Aby zsynchronizować użytkowników (przy użyciu usługi GALSync) między dwiema organizacjami Exchange, można użyć programu FIM 2010 lub programu MIM 2016 w środowisku lokalnym. Użytkownicy w jednej z organizacji są traktowane jako obcego użytkowników/contacts w innej organizacji. Te różne lokalne usługi Active Directory wystąpień, następnie mogą być synchronizowane z ich własnych dzierżaw usługi Azure AD.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak zainstalować program Azure AD Connect dla tych scenariuszy, zobacz [instalację niestandardową programu Azure AD Connect](how-to-connect-install-custom.md).

Dowiedz się więcej o [synchronizacji programu Azure AD Connect](how-to-connect-sync-whatis.md) konfiguracji.

Dowiedz się więcej o [integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
