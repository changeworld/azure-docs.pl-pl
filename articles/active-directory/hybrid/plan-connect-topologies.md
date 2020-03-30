---
title: 'Usługa Azure AD Connect: obsługiwane topologie | Dokumenty firmy Microsoft'
description: W tym temacie opisano szczegółowe informacje o obsługiwanych i nieobsługiwionych topologiach usługi Azure AD Connect
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
ms.openlocfilehash: 9618e02f54fbb2a3b92771761c5fcf700d126b5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253834"
---
# <a name="topologies-for-azure-ad-connect"></a>Topologie obsługiwane w programie Azure AD Connect
W tym artykule opisano różne lokalne i usługi Azure Active Directory (Azure AD) topologie, które używają synchronizacji usługi Azure AD Connect jako rozwiązania integracji klucza. Ten artykuł zawiera zarówno obsługiwane, jak i nieobsługiwały konfiguracje.


Oto legenda zdjęć w artykule:

| Opis | Symbol |
| --- | --- |
| Lokalny las usługi Active Directory |![Lokalny las usługi Active Directory](./media/plan-connect-topologies/LegendAD1.png) |
| Lokalna usługa Active Directory z filtrowanym importem |![Usługa Active Directory z filtrowanym importem](./media/plan-connect-topologies/LegendAD2.png) |
| Serwer synchronizacji usługi Azure AD Connect |![Serwer synchronizacji usługi Azure AD Connect](./media/plan-connect-topologies/LegendSync1.png) |
| Serwer synchronizacji usługi Azure AD Connect "tryb przejściowy" |![Serwer synchronizacji usługi Azure AD Connect "tryb przejściowy"](./media/plan-connect-topologies/LegendSync2.png) |
| GALSync z forefront identity managerem (FIM) 2010 lub Microsoft Identity Manager (MIM) 2016 |![GALSync z FIM 2010 lub MIM 2016](./media/plan-connect-topologies/LegendSync3.png) |
| Serwer synchronizacji usługi Azure AD Connect, szczegółowe informacje |![Serwer synchronizacji usługi Azure AD Connect, szczegółowe informacje](./media/plan-connect-topologies/LegendSync4.png) |
| Azure AD |![Usługa Azure Active Directory](./media/plan-connect-topologies/LegendAAD.png) |
| Scenariusz nieobsługiwał |![Scenariusz nieobsługiwał](./media/plan-connect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> Firma Microsoft nie obsługuje modyfikowania lub obsługi synchronizacji usługi Azure AD Connect poza konfiguracjami lub akcjami, które są formalnie udokumentowane. Każda z tych konfiguracji lub akcji może spowodować niespójny lub nieobsługiowany stan synchronizacji usługi Azure AD Connect. W rezultacie firma Microsoft nie może zapewnić pomocy technicznej dla takich wdrożeń.


## <a name="single-forest-single-azure-ad-tenant"></a>Pojedynczy las, pojedyncza dzierżawa usługi Azure AD
![Topologia dla pojedynczego lasu i jednego dzierżawcy](./media/plan-connect-topologies/SingleForestSingleDirectory.png)

Najczęstszą topologią jest pojedynczy las lokalny z jedną lub wieloma domenami i jedną dzierżawą usługi Azure AD. W przypadku uwierzytelniania usługi Azure AD używana jest synchronizacja skrótów haseł. Ekspresowa instalacja usługi Azure AD Connect obsługuje tylko tę topologię.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Pojedynczy las, wiele serwerów synchronizacji z jedną dzierżawą usługi Azure AD
![Nieobsługiwała, filtrowana topologia dla pojedynczego lasu](./media/plan-connect-topologies/SingleForestFilteredUnsupported.png)

Posiadanie wielu serwerów synchronizacji usługi Azure AD Connect połączonych z tą samą dzierżawą usługi Azure AD nie jest obsługiwane, z wyjątkiem [serwera przejściowego.](#staging-server) Nie jest obsługiwane, nawet jeśli te serwery są skonfigurowane do synchronizacji z wzajemnie wykluczające się zestaw obiektów. Być może ta topologia może zostać rozważona, jeśli nie można dotrzeć do wszystkich domen w lesie z jednego serwera lub jeśli chcesz rozłożyć obciążenie na kilka serwerów.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Wiele lasów, pojedyncza dzierżawa usługi Azure AD
![Topologia wielu lasów i jednego dzierżawcy](./media/plan-connect-topologies/MultiForestSingleDirectory.png)

Wiele organizacji ma środowiska z wieloma lokalnymi lasami usługi Active Directory. Istnieją różne powody, dla których więcej niż jeden lokalny las usługi Active Directory. Typowymi przykładami są projekty z lasami zasobów konta i wynikiem fuzji lub przejęcia.

Jeśli masz wiele lasów, wszystkie lasy muszą być osiągalne przez jeden serwer synchronizacji usługi Azure AD Connect. Serwer musi być przyłączony do domeny. Jeśli to konieczne, aby dotrzeć do wszystkich lasów, można umieścić serwer w sieci obwodowej (znanej również jako STREFA DMZ, strefa zdemilitaryzowana i podsieć ekranowana).

Kreator instalacji usługi Azure AD Connect oferuje kilka opcji konsolidacji użytkowników, którzy są reprezentowani w wielu lasach. Celem jest, że użytkownik jest reprezentowany tylko raz w usłudze Azure AD. Istnieje kilka typowych topologii, które można skonfigurować w niestandardowej ścieżce instalacji w kreatorze instalacji. Na stronie **Unikatowa identyfikacja użytkowników** wybierz odpowiednią opcję reprezentującą topologię. Konsolidacja jest skonfigurowana tylko dla użytkowników. Zduplikowane grupy nie są konsolidowane z konfiguracją domyślną.

Wspólne topologie są omówione w sekcjach dotyczących oddzielnych topologii, [pełnej siatki](#multiple-forests-full-mesh-with-optional-galsync)i [topologii zasobów konta.](#multiple-forests-account-resource-forest)

Domyślna konfiguracja w synchronizacji usługi Azure AD Connect zakłada:

* Każdy użytkownik ma tylko jedno włączone konto, a las, w którym znajduje się to konto, jest używany do uwierzytelniania użytkownika. To założenie dotyczy synchronizacji skrótów haseł, uwierzytelniania przekazywanego i federacji. UserPrincipalName i sourceAnchor/immutableID pochodzą z tego lasu.
* Każdy użytkownik ma tylko jedną skrzynkę pocztową.
* Las, w którym znajduje się skrzynka pocztowa dla użytkownika, ma najlepszą jakość danych dla atrybutów widocznych na globalnej liście adresów programu Exchange (GAL). Jeśli nie ma skrzynki pocztowej dla użytkownika, każdy las może służyć do współtworzenia tych wartości atrybutów.
* Jeśli masz połączonybox pocztowy, istnieje również konto w innym lesie używane do logowania.

Jeśli środowisko nie spełnia tych założeń, dzieją się następujące czynności:

* Jeśli masz więcej niż jedno aktywne konto lub więcej niż jedną skrzynkę pocztową, aparat synchronizacji wybiera jedno i ignoruje inne.
* Połączona skrzynka pocztowa bez innego aktywnego konta nie jest eksportowana do usługi Azure AD. Konto użytkownika nie jest reprezentowane jako członek żadnej grupy. Połączona skrzynka pocztowa w dirsync jest zawsze reprezentowana jako normalna skrzynka pocztowa. Ta zmiana jest celowo inne zachowanie, aby lepiej obsługiwać scenariusze wielu lasów.

Więcej szczegółów można znaleźć w [opisie domyślnej konfiguracji](concept-azure-ad-connect-sync-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Wiele lasów, wiele serwerów synchronizacji z jedną dzierżawą usługi Azure AD
![Nieobsługiwała topologia wielu lasów i wielu serwerów synchronizacji](./media/plan-connect-topologies/MultiForestMultiSyncUnsupported.png)

Posiadanie więcej niż jednego serwera synchronizacji usługi Azure AD Connect połączonego z jedną dzierżawą usługi Azure AD nie jest obsługiwane. Wyjątkiem jest użycie [serwera przemieszczania](#staging-server).

Ta topologia różni się od poniższej, ponieważ **wiele serwerów synchronizacji połączonych** z jedną dzierżawą usługi Azure AD nie jest obsługiwane.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Wiele lasów, pojedynczy serwer synchronizacji, użytkownicy są reprezentowani tylko w jednym katalogu
![Opcja reprezentowania użytkowników tylko raz we wszystkich katalogach](./media/plan-connect-topologies/MultiForestUsersOnce.png)

![Przedstawianie wielu lasów i oddzielnych topologii](./media/plan-connect-topologies/MultiForestSeparateTopologies.png)

W tym środowisku wszystkie lasy lokalne są traktowane jako oddzielne jednostki. Żaden użytkownik nie jest obecny w żadnym innym lesie. Każdy las ma własną organizację programu Exchange i nie ma programu GALSync między lasami. Ta topologia może być sytuacja po fuzji / przejęcia lub w organizacji, w której każda jednostka biznesowa działa niezależnie. Lasy te znajdują się w tej samej organizacji w usłudze Azure AD i są wyświetlane przy ujednoliconym gal. W poprzednim obrazie każdy obiekt w każdym lesie jest reprezentowany raz w metaverse i agregowane w docelowej dzierżawy usługi Azure AD.

### <a name="multiple-forests-match-users"></a>Wiele lasów: dopasowywuj użytkowników
Typowe dla wszystkich tych scenariuszy jest to, że grupy dystrybucji i zabezpieczeń może zawierać mieszankę użytkowników, kontaktów i zagranicznych podmiotów zabezpieczeń (FSPs). Usługi FSPs są używane w Usługach domenowych Active Directory (AD DS) do reprezentowania członków z innych lasów w grupie zabezpieczeń. Wszystkie fsps są rozpoznawane do rzeczywistego obiektu w usłudze Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Wiele lasów: pełna siatka z opcjonalnym GALSync
![Opcja używania atrybutu poczty do dopasowywania, gdy tożsamości użytkowników istnieją w wielu katalogach](./media/plan-connect-topologies/MultiForestUsersMail.png)

![Pełna topologia siatki dla wielu lasów](./media/plan-connect-topologies/MultiForestFullMesh.png)

Topologia pełnej siatki umożliwia użytkownikom i zasobom zlokalizowanie w dowolnym lesie. Często istnieją dwukierunkowe zaufania między lasami.

Jeśli program Exchange znajduje się w więcej niż jednym lesie, może istnieć (opcjonalnie) lokalne rozwiązanie GALSync. Każdy użytkownik jest następnie reprezentowany jako kontakt we wszystkich innych lasach. GALSync jest powszechnie wdrażany przez FIM 2010 lub MIM 2016. Usługi Azure AD Connect nie można używać dla lokalnego programu GALSync.

W tym scenariuszu obiekty tożsamości są dołączane za pośrednictwem atrybutu poczty. Użytkownik, który ma skrzynkę pocztową w jednym lesie, jest dołączany do kontaktów w innych lasach.

### <a name="multiple-forests-account-resource-forest"></a>Wiele lasów: las zasobów konta
![Opcja używania atrybutów ObjectSID i msExchMasterAccountSID do dopasowywania, gdy tożsamości istnieją w wielu katalogach](./media/plan-connect-topologies/MultiForestUsersObjectSID.png)

![Topologia lasu zasobów konta dla wielu lasów](./media/plan-connect-topologies/MultiForestAccountResource.png)

W topologii lasu zasobów kont masz co najmniej jeden las *kont* z aktywnymi kontami użytkowników. Masz również jeden lub więcej lasów *zasobów* z wyłączonymi kontami.

W tym scenariuszu jeden (lub więcej) las zasobów ufa wszystkim lasom kont. Las zasobów zazwyczaj ma rozszerzony schemat usługi Active Directory z programami Exchange i Lync. Wszystkie usługi Exchange i Lync, wraz z innymi usługami udostępnionymi, znajdują się w tym lesie. Użytkownicy mają wyłączone konto użytkownika w tym lesie, a skrzynka pocztowa jest połączona z lasem kont.

## <a name="office-365-and-topology-considerations"></a>Zagadnienia dotyczące usługi Office 365 i topologii
Niektóre obciążenia usługi Office 365 mają pewne ograniczenia dotyczące obsługiwanych topologii:

| Obciążenie | Ograniczenia |
| --------- | --------- |
| Exchange Online | Aby uzyskać więcej informacji na temat topologii hybrydowych obsługiwanych przez program Exchange Online, zobacz [Wdrożenia hybrydowe z wieloma lasami usługi Active Directory](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype dla firm | Gdy używasz wielu lasów lokalnych, obsługiwane jest tylko topologia lasu zasobów konta. Aby uzyskać więcej informacji, zobacz [Wymagania środowiskowe dotyczące programu Skype dla programu Skype dla firm Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Jeśli jesteś większą organizacją, należy rozważyć użycie funkcji [PreferredDataLocation usługi Office 365.](how-to-connect-sync-feature-preferreddatalocation.md) Umożliwia zdefiniowanie, w którym regionie centrum danych znajdują się zasoby użytkownika.

## <a name="staging-server"></a>Serwer przemieszczania
![Serwer przemieszczania w topologii](./media/plan-connect-topologies/MultiForestStaging.png)

Usługa Azure AD Connect obsługuje instalowanie drugiego serwera w *trybie przejściowym*. Serwer w tym trybie odczytuje dane ze wszystkich połączonych katalogów, ale nie zapisuje niczego do połączonych katalogów. Używa normalnego cyklu synchronizacji i dlatego ma zaktualizowaną kopię danych tożsamości.

W przypadku awarii serwera podstawowego, można przejść awaryjnie do serwera przemieszczania. Można to zrobić w kreatorze usługi Azure AD Connect. Ten drugi serwer może znajdować się w innym centrum danych, ponieważ żadna infrastruktura nie jest współużytkowana z serwerem podstawowym. Należy ręcznie skopiować wszelkie zmiany konfiguracji wprowadzone na serwerze podstawowym na drugi serwer.

Serwer przejściowy służy do testowania nowej konfiguracji niestandardowej i wpływu, jaki ma na dane. Można wyświetlić podgląd zmian i dostosować konfigurację. Jeśli jesteś zadowolony z nowej konfiguracji, można ustawić serwer przejściowy aktywny serwer i ustawić stary serwer aktywny w trybie przejściowym.

Za pomocą tej metody można również zastąpić aktywny serwer synchronizacji. Przygotuj nowy serwer i ustaw go w trybie przejściowym. Upewnij się, że jest w dobrym stanie, wyłącz tryb przejściowy (czyniąc go aktywnym) i zamknij aktualnie aktywny serwer.

Możliwe jest, że masz więcej niż jeden serwer przemieszczania, jeśli chcesz mieć wiele kopii zapasowych w różnych centrach danych.

## <a name="multiple-azure-ad-tenants"></a>Wielu dzierżaw usługi Azure AD
Zaleca się posiadanie jednej dzierżawy w usłudze Azure AD dla organizacji.
Przed zaplanowaniem używania wielu dzierżaw usługi Azure AD zapoznaj się z artykułem [Zarządzanie jednostkami administracyjnymi w usłudze Azure AD.](../users-groups-roles/directory-administrative-units.md) Obejmuje typowe scenariusze, w których można użyć jednej dzierżawy.

![Topologia wielu lasów i wielu dzierżawców](./media/plan-connect-topologies/MultiForestMultiDirectory.png)

Istnieje relacja 1:1 między serwerem synchronizacji usługi Azure AD Connect a dzierżawą usługi Azure AD. Dla każdej dzierżawy usługi Azure AD potrzebna jest jedna instalacja serwera synchronizacji usługi Azure AD Connect. Wystąpienia dzierżawy usługi Azure AD są izolowane według projektu. Oznacza to, że użytkownicy w jednej dzierżawie nie widzą użytkowników w innej dzierżawie. Jeśli chcesz tej separacji, jest to obsługiwana konfiguracja. W przeciwnym razie należy użyć modelu pojedynczej dzierżawy usługi Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Każdy obiekt tylko raz w dzierżawie usługi Azure AD
![Filtrowana topologia dla pojedynczego lasu](./media/plan-connect-topologies/SingleForestFiltered.png)

W tej topologii jeden serwer synchronizacji usługi Azure AD Connect jest połączony z każdą dzierżawą usługi Azure AD. Serwery synchronizacji usługi Azure AD Connect muszą być skonfigurowane do filtrowania, tak aby każdy z nich miał wzajemnie wykluczający się zestaw obiektów do działania. Można na przykład szesnastą zakres każdego serwera do określonej domeny lub jednostki organizacyjnej.

Domena DNS może być zarejestrowana tylko w jednej dzierżawie usługi Azure AD. Sieci UPn użytkowników w lokalnym wystąpieniu usługi Active Directory muszą również używać oddzielnych obszarów nazw. Na przykład na poprzednim zdjęciu w lokalnym wystąpieniu usługi Active Directory zarejestrowane są trzy oddzielne sufiksy nazwy UPN: contoso.com, fabrikam.com i wingtiptoys.com. Użytkownicy w każdej lokalnej domenie usługi Active Directory używają innego obszaru nazw.

>[!NOTE]
>Globalna synchronizacja list adresów (GalSync) nie jest wykonywana automatycznie w tej topologii i wymaga dodatkowej niestandardowej implementacji MIM, aby upewnić się, że każdy dzierżawca ma pełną globalną listę adresów (GAL) w usłudze Exchange Online i usłudze Skype dla firm Online.


Ta topologia ma następujące ograniczenia dotyczące scenariuszy obsługiwanych w inny sposób:

* Tylko jeden z dzierżaw usługi Azure AD może włączyć hybrydę programu Exchange z lokalnym wystąpieniem usługi Active Directory.
* Urządzenia z systemem Windows 10 mogą być skojarzone tylko z jedną dzierżawą usługi Azure AD.
* Opcja logowania jednokrotnego (Logowanie jednokrotne) dla synchronizacji skrótów haseł i uwierzytelniania przekazywanego może być używana tylko z jedną dzierżawą usługi Azure AD.

Wymóg wzajemnie wykluczania się zestaw obiektów stosuje się również do stornia zwrotnego. Niektóre funkcje stornowanie nie są obsługiwane w tej topologii, ponieważ zakładają jedną konfigurację lokalną. Między innymi są to następujące funkcje:

* Grupowanie storców z konfiguracją domyślną.
* Odpisanie urządzenia.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Każdy obiekt wiele razy w dzierżawie usługi Azure AD
![Nieobsługiwała topologia dla jednego lasu i wielu dzierżawców](./media/plan-connect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Nieobsługiwała topologia dla pojedynczego lasu i wielu łączników](./media/plan-connect-topologies/SingleForestMultiConnectorsUnsupported.png)

Te zadania nie są obsługiwane:

* Synchronizacja tego samego użytkownika z wieloma dzierżawami usługi Azure AD.
* Wprowadzać zmiany konfiguracji, tak aby użytkownicy w jednej dzierżawie usługi Azure AD były wyświetlane jako kontakty w innej dzierżawie usługi Azure AD.
* Zmodyfikuj synchronizację usługi Azure AD Connect, aby połączyć się z wieloma dzierżawami usługi Azure AD.

### <a name="galsync-by-using-writeback"></a>GALSync przy użyciu stornowanie
![Nieobsługiwana topologia wielu lasów i wielu katalogów, a program GALSync koncentruje się na usłudze Azure AD](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Nieobsługiwana topologia wielu lasów i wielu katalogów, a program GALSync koncentruje się na lokalnej usłudze Active Directory](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Dzierżawy usługi Azure AD są izolowane według projektu. Te zadania nie są obsługiwane:

* Zmień konfigurację synchronizacji usługi Azure AD Connect, aby odczytać dane z innej dzierżawy usługi Azure AD.
* Eksportuj użytkowników jako kontakty do innego lokalnego wystąpienia usługi Active Directory przy użyciu synchronizacji usługi Azure AD Connect.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync z lokalnym serwerem synchronizacji
![GALSync w topologii dla wielu lasów i wielu katalogów](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync.png)

Za pomocą programu FIM 2010 lub MIM 2016 lokalnie można synchronizować użytkowników (za pośrednictwem programu GALSync) między dwiema organizacjami programu Exchange. Użytkownicy w jednej organizacji są wyświetlane jako użytkownicy zagraniczni/kontakty w innej organizacji. Te różne lokalne wystąpienia usługi Active Directory można następnie zsynchronizować z ich własnymi dzierżawami usługi Azure AD.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak zainstalować usługę Azure AD Connect w tych scenariuszach, zobacz [Instalacja niestandardowa usługi Azure AD Connect](how-to-connect-install-custom.md).

Dowiedz się więcej o konfiguracji [synchronizacji usługi Azure AD Connect.](how-to-connect-sync-whatis.md)

Dowiedz się więcej o [integrując tożsamości lokalne z usługą Azure Active Directory.](whatis-hybrid-identity.md)
