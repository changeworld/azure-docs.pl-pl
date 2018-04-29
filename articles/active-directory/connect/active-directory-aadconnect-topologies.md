---
title: 'Usługi Azure AD Connect: Topologie obsługiwane przez | Dokumentacja firmy Microsoft'
description: W tym temacie szczegółowo obsługiwane i nieobsługiwane topologie programu Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: article
ms.date: 02/27/2018
ms.author: billmath
ms.openlocfilehash: 2f72f2dd3dbaaf17494d09a36159afc464cc64d4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="topologies-for-azure-ad-connect"></a>Topologie obsługiwane w programie Azure AD Connect
W tym artykule opisano różne lokalnymi i topologii usługi Azure Active Directory (Azure AD), używające synchronizacja programu Azure AD Connect jako rozwiązanie integracji klucza. W tym artykule opisano zarówno obsługiwane i nieobsługiwane konfiguracje.

Oto legendy obrazów w artykule:

| Opis | Symbol |
| --- | --- |
| W lokalnym lesie usługi Active Directory |![W lokalnym lesie usługi Active Directory](./media/active-directory-aadconnect-topologies/LegendAD1.png) |
| W lokalnej usłudze Active Directory z filtrowanych importu |![Usługi Active Directory z filtrowanych importu](./media/active-directory-aadconnect-topologies/LegendAD2.png) |
| Serwer synchronizacji usługi Azure AD Connect |![Serwer synchronizacji usługi Azure AD Connect](./media/active-directory-aadconnect-topologies/LegendSync1.png) |
| Azure AD Connect serwera synchronizacji "Tryb przejściowy" |![Azure AD Connect serwera synchronizacji "Tryb przejściowy"](./media/active-directory-aadconnect-topologies/LegendSync2.png) |
| Usługi GALSync z programu Forefront Identity Manager (FIM) 2010 lub Microsoft Identity Manager (MIM) 2016 |![Usługi GALSync z programu FIM 2010 lub MIM 2016](./media/active-directory-aadconnect-topologies/LegendSync3.png) |
| Serwer synchronizacji usługi Azure AD Connect, szczegółowe |![Serwer synchronizacji usługi Azure AD Connect, szczegółowe](./media/active-directory-aadconnect-topologies/LegendSync4.png) |
| Azure AD |![Usługa Azure Active Directory](./media/active-directory-aadconnect-topologies/LegendAAD.png) |
| Nieobsługiwany scenariusz |![Nieobsługiwany scenariusz](./media/active-directory-aadconnect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> Microsoft nie obsługuje modyfikowania lub działania synchronizacji Azure AD Connect poza konfiguracje lub akcje, które są udokumentowane formalnie. Te konfiguracje lub akcje może spowodować niespójne lub nieobsługiwany stan synchronizacji Azure AD Connect. W związku z tym firma Microsoft nie może świadczyć pomocy technicznej w przypadku takich wdrożeń.


## <a name="single-forest-single-azure-ad-tenant"></a>Pojedynczy las, pojedynczy dzierżawy usługi Azure AD
![Topologia jednego lasu i pojedynczej dzierżawy](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

Najbardziej typowe topologia jest pojedynczego lokalnego lasu z jednym lub wielu domen i jednej usługi Azure AD dzierżawy. W przypadku uwierzytelniania usługi Azure AD jest używana synchronizacji skrótu hasła. Instalacja ekspresowa programu Azure AD Connect obsługuje tylko tej topologii.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Pojedynczy las, wiele serwerów synchronizacji do jednej dzierżawy usługi Azure AD
![Nieobsługiwany topologia filtrowane do jednego lasu](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

Posiadanie wielu serwerów synchronizacji Azure AD Connect podłączone do tej samej dzierżawy usługi Azure AD nie jest obsługiwana, z wyjątkiem [przemieszczania serwera](#staging-server). Ma on nieobsługiwany nawet jeśli te serwery są skonfigurowane do synchronizacji z wykluczają się wzajemnie zestaw obiektów. Użytkownik może mieć uznane za tej topologii Jeśli nie można uzyskać dostęp do wszystkich domen w lesie z jednego serwera lub jeśli chcesz rozłożenie obciążenia między kilka serwerów.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Wiele lasów, pojedynczy dzierżawy usługi Azure AD
![Topologia wiele lasów i pojedynczej dzierżawy](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Organizacje często mają środowisk z wieloma lokalnymi lasami usługi Active Directory. Istnieją różne przyczyny mających więcej niż jednym lesie usługi Active Directory lokalnymi. Typowymi przykładami są projektów z lasów kont zasobów i wynik fuzji lub przejęcia.

Jeśli masz wiele lasów, wszystkich lasach musi być dostępny dla pojedynczego serwera synchronizacji Azure AD Connect. Nie masz przyłączyć serwer do domeny. Jeśli to konieczne do osiągnięcia wszystkich lasach, należy umieścić serwer w sieci obwodowej (znanej także jako strefa DMZ, strefą zdemilitaryzowaną i podsiecią ekranowaną).

Kreator instalacji Azure AD Connect oferuje kilka opcji skonsolidować użytkowników, którzy są reprezentowane w wielu lasach. Celem jest, że użytkownik odpowiada tylko jeden raz w usłudze Azure AD. Brak niektórych typowych topologie, które można skonfigurować w ścieżce instalacji niestandardowej w Kreatorze instalacji. Na **unikatowa identyfikacja użytkowników** wybierz odpowiednią opcję, która reprezentuje topologii. Konsolidacja jest skonfigurowany tylko dla użytkowników. Zduplikowany grup nie są konsolidowane z konfiguracji domyślnej.

Popularne topologie omówiono w sekcji o [oddzielnych topologie](#multiple-forests-separate-topologies), [pełne siatki](#multiple-forests-full-mesh-with-optional-galsync), i [topologii zasobów konta](#multiple-forests-account-resource-forest).

Założono domyślnej konfiguracji synchronizacji Azure AD Connect:

* Każdy użytkownik ma tylko jedno konto włączone i lesie, w którym znajduje się to konto jest używane do uwierzytelnienia użytkownika. Jest to założenie dla synchronizacji skrótów haseł, uwierzytelnianie i federacji. UserPrincipalName i sourceAnchor/nazwę immutableID pochodzą z tego lasu.
* Każdy użytkownik ma tylko jedną skrzynkę pocztową.
* Las, który hostuje skrzynki pocztowej użytkownika ma najlepszą jakość danych dla atrybutów widoczne w globalnej listy adresów (GAL) programu Exchange. Jeśli nie ma żadnych skrzynki pocztowej użytkownika, dowolnym lesie może służyć do ich współtworzenia wartości tych atrybutów.
* Jeśli masz połączoną skrzynkę pocztową, istnieje również konto w innym lesie używane do logowania.

Jeśli środowisko nie jest zgodny z tych założeń, stanie następujących czynności:

* Jeśli masz więcej niż jedno konto active lub więcej niż jedną skrzynkę pocztową, aparat synchronizacji wybiera jeden i ignoruje innych.
* Połączoną skrzynkę pocztową z żadnego aktywnego konta nie są eksportowane do usługi Azure AD. Konto użytkownika nie jest reprezentowany jako element członkowski w dowolnej grupie. Połączoną skrzynkę pocztową w DirSync zawsze jest reprezentowany jako normalne skrzynki pocztowej. Ta zmiana jest celowo inaczej w celu lepszej obsługi scenariuszy z wieloma lasami.

Szczegółowe informacje można znaleźć [opis konfiguracji domyślnej](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Wiele lasów, wiele serwerów synchronizacji do jednej dzierżawy usługi Azure AD
![Nieobsługiwany topologii dla wielu serwerów synchronizacji i wiele lasów](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Mających więcej niż jeden serwer synchronizacji Azure AD Connect podłączony do pojedynczej dzierżawy usługi Azure AD nie jest obsługiwane. Wyjątkiem jest użycie [przemieszczania serwera](#staging-server).

### <a name="multiple-forests-separate-topologies"></a>Wiele lasów, oddzielne topologii
![Opcja reprezentujących użytkowników tylko raz we wszystkich katalogach](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Sceny wiele lasów i oddzielne topologii](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

W tym środowisku wszystkich lasach lokalnych są traktowane jako osobne jednostki. Żaden użytkownik nie jest obecny w innym lesie. W każdym lesie jest jego własnej organizacji programu Exchange, i nie ma żadnych usługi GALSync między lasami. Ta topologia może być sytuacja po połączeniu/nabycia lub w organizacji, których poszczególnych jednostek biznesowych działa niezależnie. Takich lasach znajdują się w tej samej organizacji w usłudze Azure AD i są wyświetlane z ujednoliconego usługi GAL. Na powyższej ilustracji każdego obiektu w każdym lesie są reprezentowane raz w magazynie metaverse i agregowane w dzierżawie docelowej usługi Azure AD.

### <a name="multiple-forests-match-users"></a>Wiele lasów: dopasować użytkowników
Wspólne dla wszystkich tych scenariuszach jest to, że dystrybucji i grupy zabezpieczeń mogą zawierać zarówno użytkowników, kontaktów i obce podmioty zabezpieczeń (FSP). FSP są używane w usługach domenowych w usłudze Active Directory (AD DS) do reprezentowania elementów członkowskich znajdujących się w innych lasach należących do grupy zabezpieczeń. Wszystkie FSP zostaną rozwiązane do rzeczywistego obiektu w usłudze Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Wiele lasów: pełne o usługi GALSync opcjonalne
![Opcja dopasowania, gdy tożsamości użytkowników istnieją w wielu katalogach przy użyciu atrybutu poczty](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Topologia pełnej sieci dla wielu lasów](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Topologia pełnej sieci umożliwia użytkownikom i znajdować się w dowolnym lesie zasobów. Zazwyczaj istnieją dwukierunkowe relacje zaufania między lasami.

Jeśli programu Exchange znajduje się w więcej niż jednym lesie, mogą wystąpić (opcjonalnie) używane rozwiązanie lokalne usługi GALSync. Każdy użytkownik, następnie jest reprezentowany jako kontakt w innych lasach. Usługi GALSync często jest implementowane za pośrednictwem programu FIM 2010 lub MIM 2016. Azure AD Connect nie może służyć do lokalnej usługi GALSync.

W tym scenariuszu obiekty tożsamości są łączone za pomocą atrybutu poczty. Użytkownik, który ma skrzynkę pocztową w jednym lesie jest połączony z kontaktów w innych lasach.

### <a name="multiple-forests-account-resource-forest"></a>Wiele lasów: lasu zasobów konta
![Opcji korzystania z funkcji atrybuty ObjectSID i msExchMasterAccountSID do dopasowania, gdy istnieją tożsamości w wielu katalogach](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Topologia lasu zasobów konta dla wielu lasów](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

W topologii lasu zasobów dla konta, masz co najmniej jeden *konta* lasów z aktywne konta użytkowników. Masz również jedną lub więcej *zasobów* lasów z wyłączonych kont.

W tym scenariuszu jeden (lub więcej) lasu zasobów ufa wszystkich lasów kont. Las zasobów ma zazwyczaj rozszerzony schemat usługi Active Directory z programem Exchange i usługi Lync. Usługi wszystkie programu Exchange i Lync, wraz z innych usług udostępnionych, znajdują się w tym lesie. Użytkownicy mają wyłączonego konta użytkownika, w tym lesie, a skrzynki pocztowej jest połączony z lasu kont.

## <a name="office-365-and-topology-considerations"></a>Office 365 i zagadnienia dotyczące topologii
Niektórych obciążeń usługi Office 365 mają niektórych ograniczeń dotyczących obsługiwanych topologii:

| Obciążenie | Ograniczenia |
| --------- | --------- |
| Exchange Online | Aby uzyskać więcej informacji na temat hybrydowe topologie obsługiwane przez usługę Exchange Online, zobacz [hybrydowych wdrożeń z wieloma lasami usługi Active Directory](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype dla firm | Podczas korzystania z wieloma lokalnymi lasami, topologią lasu zasobów konta jest obsługiwana. Aby uzyskać więcej informacji, zobacz [środowiska wymagania dla usługi Skype dla firm Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Jeśli są większe organizacji, a następnie należy użyć [Office 365 PreferredDataLocation](active-directory-aadconnectsync-feature-preferreddatalocation.md) funkcji. Umożliwia zdefiniowanie, w którym regionie centrum danych znajdują się zasoby użytkownika.

## <a name="staging-server"></a>Serwer przemieszczania
![Przemieszczania serwer w topologii](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect obsługuje, instalacji drugiego serwera w *Tryb przejściowy*. Serwer, w tym trybie odczytuje dane ze wszystkich podłączonych katalogów, ale nie zapisuje niczego połączonych katalogów. Używa cykl synchronizacji normalne, a w związku z tym ma zaktualizowanej kopii danych tożsamości.

W przypadku awarii, w którym awarii serwera podstawowego może przełączyć się na serwerze. Możesz to zrobić w Kreatora programu Azure AD Connect. Ten drugi serwer może znajdować się w różnych centrach danych, ponieważ nie infrastruktury jest współużytkowany z serwera podstawowego. Należy ręcznie skopiować zmiany konfiguracji wprowadzone na podstawowym serwerze do drugiego serwera.

Serwerze tymczasowym służy do testowania nowej niestandardowej konfiguracji i jej wpływ na podstawie danych. Można wyświetlić podgląd zmian i dostosowanie konfiguracji. Po zakończeniu modyfikowania przy użyciu nowej konfiguracji, możesz ustawić serwerze aktywnego serwera, a następnie ustaw stary serwer aktywny tryb przejściowy.

Ta metoda umożliwia również zastąpić serwer synchronizacji usługi active. Przygotuj nowy serwer, a następnie ustaw tryb przejściowy. Upewnij się, że jest w dobrym stanie, wyłącz (dzięki czemu active), Tryb przejściowy i zamknąć aktualnie aktywnego serwera.

Użytkownik może mieć więcej niż jeden serwer tymczasowej, gdy mają wiele kopii zapasowych w różnych centrach danych.

## <a name="multiple-azure-ad-tenants"></a>Wiele dzierżaw usługi Azure AD
Zaleca się o pojedynczej dzierżawy w usłudze Azure AD dla organizacji.
Przed planujesz używać wiele dzierżaw usługi Azure AD, zobacz artykuł [Zarządzanie jednostkami administracyjnymi w usłudze Azure AD](../active-directory-administrative-units-management.md). Obejmuje on typowe scenariusze, w których można użyć pojedynczej dzierżawy.

![Topologia wiele lasów i wieloma dzierżawcami](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Istnieje relacja 1:1 między serwerem synchronizacji Azure AD Connect i dzierżawa usługi Azure AD. Dla każdego dzierżawcy usługi Azure AD należy jedna instalacja serwera synchronizacji Azure AD Connect. Wystąpienia dzierżawy usługi Azure AD są izolowane zgodnie z projektem. Oznacza to użytkownicy w jednej dzierżawy nie widzą użytkowników w dzierżawie programu. Jeśli chcesz, aby ta separacja, jest obsługiwana konfiguracja. W przeciwnym razie należy używać pojedynczego modelu dzierżawy usługi Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Każdy obiekt tylko raz w dzierżawie usługi Azure AD
![Filtrowane topologii w przypadku pojedynczego lasu](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

W tej topologii jeden serwer synchronizacji Azure AD Connect jest podłączona do każdego dzierżawcy usługi Azure AD. Serwery synchronizacji Azure AD Connect muszą być skonfigurowane filtrowania, dzięki czemu każdy ma wykluczają się wzajemnie zestaw obiektów do działania na. Można na przykład zakres każdego serwera do określonej domeny lub jednostki organizacyjnej.

Domena DNS może być zarejestrowany w tylko jednej dzierżawy usługi Azure AD. Nazwy UPN użytkowników w lokalnym wystąpieniu usługi Active Directory również należy użyć oddzielnych przestrzeni nazw. Na przykład na powyższej ilustracji trzech oddzielnych sufiksy są zarejestrowane w lokalnym wystąpieniem usługi Active Directory: contoso.com, fabrikam.com i nadrzędnych. Użytkownicy w każdej domenie usługi Active Directory lokalnymi używać różnych przestrzeni nazw.

>[!NOTE]
>Synchronizacji listy adresów (usługi GalSync) nie jest wykonywana automatycznie w tej topologii i wymaga dodatkowych niestandardowych implementacji MIM do upewnij się, że poszczególne dzierżawy zawierają pełny globalnego adresu listy (GAL) w usłudze Exchange Online i Skype dla firm Online.


Ta topologia ma następujące ograniczenia w inny sposób obsługiwane scenariusze:

* Tylko jeden dzierżaw usługi Azure AD można włączyć hybrydowym programu Exchange z lokalnego wystąpienia usługi Active Directory.
* Urządzenia z systemem Windows 10 można skojarzyć z tylko jedną dzierżawy usługi Azure AD.
* Pojedynczy logowania jednokrotnego (SSO) opcja uwierzytelniania przekazywanego i synchronizacji skrótu hasła może służyć z tylko jedną dzierżawy usługi Azure AD.

Wymaganie wykluczają się wzajemnie zestaw obiektów ma również zastosowanie do zapisywania zwrotnego. Niektóre funkcje zapisywania zwrotnego nie są obsługiwane z tej topologii, ponieważ zakładają konfiguracji pojedynczym lokalnym. Te funkcje obejmują:

* Grupa zapisywania zwrotnego z konfiguracji domyślnej.
* Zapisywanie zwrotne urządzeń.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Każdy obiekt wiele razy w dzierżawie usługi Azure AD
![Nieobsługiwany topologii dla jednego lasu i wieloma dzierżawcami](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Nieobsługiwany topologii dla jednego lasu i wiele łączników](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

Te zadania nie są obsługiwane:

* Synchronizacja tego samego użytkownika, aby wiele dzierżaw usługi Azure AD.
* Wprowadź zmiany użytkowników w jednej dzierżawy usługi Azure AD będą wyświetlane jako kontakty w innej dzierżawie usługi Azure AD konfiguracji.
* Zmodyfikuj synchronizacji Azure AD Connect do nawiązania połączenia wiele dzierżaw usługi Azure AD.

### <a name="galsync-by-using-writeback"></a>Usługi GALSync przy użyciu zapisywania zwrotnego
![Nieobsługiwany topologii wiele lasów i wiele katalogów, z usługi GALSync koncentrujących się na usługę Azure AD](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Nieobsługiwany topologii wiele lasów i wiele katalogów, z usługi GALSync koncentrujących się na lokalnej usługi Active Directory](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Dzierżaw usługi Azure AD są izolowane zgodnie z projektem. Te zadania nie są obsługiwane:

* Zmień konfigurację synchronizacji usługi Azure AD Connect można odczytać danych z innej dzierżawy usługi Azure AD.
* Eksportuj użytkowników jako kontakty do innego lokalnego wystąpienia usługi Active Directory za pomocą synchronizacji usługi Azure AD Connect.

### <a name="galsync-with-on-premises-sync-server"></a>Usługi GALSync z lokalnego serwera synchronizacji
![Usługi GALSync w topologii wiele lasów i wielu katalogów](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

FIM 2010 lub MIM 2016 lokalnymi umożliwia synchronizowanie użytkowników (przy użyciu usługi GALSync) między dwiema organizacjami Exchange. Użytkownicy w jednej z organizacji są wyświetlane jako obcego użytkowników/contacts w innej organizacji. Te różne lokalnej usługi Active Directory wystąpień mogą następnie zostać zsynchronizowany z własnych dzierżaw usługi Azure AD.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak zainstalować program Azure AD Connect dotyczących następujących scenariuszy, zobacz [Instalacja niestandardowa programu Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Dowiedz się więcej o [synchronizacja programu Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfiguracji.

Dowiedz się więcej o [integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
