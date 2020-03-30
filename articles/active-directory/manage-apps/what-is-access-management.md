---
title: Zarządzanie dostępem do aplikacji przy użyciu usługi Azure AD | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak usługa Azure Active Directory umożliwia organizacjom określenie aplikacji, do których każdy użytkownik ma dostęp.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 489b15423add03d69070bc32057af97396a85309
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409070"
---
# <a name="managing-access-to-apps"></a>Zarządzanie dostępem do aplikacji

Bieżące zarządzanie dostępem, ocena użycia i raportowanie nadal są wyzwaniem po zintegrowaniu aplikacji z systemem tożsamości organizacji. W wielu przypadkach administratorzy IT lub dział pomocy technicznej muszą odgrywać stałą aktywną rolę w zarządzaniu dostępem do aplikacji. Czasami zadanie jest wykonywane przez ogólny lub oddziałowy zespół IT. Często decyzja o przypisaniu jest przeznaczona do delegowania do decydenta biznesowego, wymagając ich zatwierdzenia przed dokonaniem cesji przez it.  Inne organizacje inwestują w integrację z istniejącym zautomatyzowanym systemem zarządzania tożsamością i dostępem, takim jak kontrola dostępu oparta na rolach (RBAC) lub kontrola dostępu oparta na atrybutach (ABAC). Zarówno integracji i rozwoju reguł wydają się być wyspecjalizowane i kosztowne. Monitorowanie lub raportowanie podejścia do zarządzania jest odrębną, kosztowną i złożoną inwestycją.

## <a name="how-does-azure-active-directory-help"></a>W jaki sposób usługa Azure Active Directory pomaga?

Usługa Azure AD obsługuje rozbudowane zarządzanie dostępem dla skonfigurowanych aplikacji, umożliwiając organizacjom łatwe osiąganie odpowiednich zasad dostępu, począwszy od automatycznego przypisywania opartego na atrybutach (scenariusze ABAC lub RBAC) poprzez delegowanie i w tym zarządzania administratorem. Dzięki usłudze Azure AD można łatwo osiągnąć złożone zasady, łącząc wiele modeli zarządzania dla pojedynczej aplikacji, a nawet można ponownie używać reguł zarządzania w aplikacjach z tymi samymi odbiorcami.

Dzięki usłudze Azure AD raportowanie użycia i przydziału jest w pełni zintegrowane, dzięki czemu administratorzy mogą łatwo raportować stan przydziału, błędy przydziału, a nawet użycie.

### <a name="assigning-users-and-groups-to-an-app"></a>Przypisywanie użytkowników i grup do aplikacji

Przypisanie aplikacji usługi Azure AD koncentruje się na dwóch podstawowych trybach przypisania:

* **Indywidualne przypisanie** Administrator IT z uprawnieniami administratora globalnego katalogu może wybrać indywidualne konta użytkowników i udzielić im dostępu do aplikacji.

* **Przypisanie oparte na grupach (wymaga usługi Azure AD Premium P1 lub P2)** Administrator IT z uprawnieniami administratora globalnego katalogu może przypisać grupę do aplikacji. Dostęp określonych użytkowników zależy od tego, czy są członkami grupy w momencie próby uzyskania dostępu do aplikacji. Innymi słowy administrator może skutecznie utworzyć regułę przypisania z informacją, że "każdy bieżący członek przypisanej grupy ma dostęp do aplikacji". Korzystając z tej opcji przypisania, administratorzy mogą korzystać z dowolnej opcji zarządzania grupami usługi Azure AD, w tym [grup dynamicznych opartych na atrybutach,](../fundamentals/active-directory-groups-create-azure-portal.md)zewnętrznych grup systemowych (na przykład lokalnej usługi Active Directory lub Workday) lub grup zarządzanych przez administratora lub samoobsługowych. Do wielu aplikacji można łatwo przypisać jedną grupę, dzięki czemu aplikacje z koligalnością przypisania mogą współużytkować reguły przypisywania, zmniejszając ogólną złożoność zarządzania. Należy zauważyć, że zagnieżdżone członkostwa w grupach nie są obecnie obsługiwane w przypadku przypisywania aplikacji opartych na grupach.

Korzystając z tych dwóch trybów przypisania, administratorzy mogą osiągnąć dowolne pożądane podejście do zarządzania przydziałami.

### <a name="requiring-user-assignment-for-an-app"></a>Wymaganie przypisania użytkownika dla aplikacji

W przypadku niektórych typów aplikacji można [wymagać od użytkowników przypisania ich do aplikacji.](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment) W ten sposób można uniemożliwić wszystkim logowanie się z wyjątkiem tych użytkowników, które jawnie przypisać do aplikacji. Ta opcja jest obsługiwana przez następujące typy aplikacji:

* Aplikacje skonfigurowane do rejestracji sfederowanej (Logowanie jednokrotne) z uwierzytelnianiem opartym na SAML
* Aplikacje proxy aplikacji korzystające z uwierzytelniania wstępnego usługi Azure Active Directory
* Aplikacje utworzone na platformie aplikacji usługi Azure AD, które używają uwierzytelniania OAuth 2.0 / OpenID Connect po zgodę użytkownika lub administratora na tę aplikację. Niektóre aplikacje dla przedsiębiorstw oferują dodatkową kontrolę nad tym, kto może się zalogować.

Jeśli przypisanie użytkownika nie jest *wymagane,* nieprzydzieleni użytkownicy nie widzą aplikacji w panelu dostępu Moje aplikacje, ale nadal mogą logować się do samej aplikacji (znanej również jako logowanie inicjowane przez SP) lub mogą używać **adresu URL dostępu użytkownika** na stronie **Właściwości** aplikacji (znanej również jako logowanie inicjowane przez IDP).

W przypadku niektórych aplikacji opcja wymagania przypisania użytkownika nie jest dostępna we właściwościach aplikacji. W takich przypadkach można użyć programu PowerShell, aby ustawić właściwość appRoleAssignmentRequired na jednostkę usługi.

### <a name="determining-the-user-experience-for-accessing-apps"></a>Określanie środowiska użytkownika podczas uzyskiwania dostępu do aplikacji

Usługa Azure AD udostępnia [kilka konfigurowalnych sposobów wdrażania aplikacji](end-user-experiences.md) dla użytkowników końcowych w organizacji:

* Panel dostępu usługi Azure AD My Apps
* Uruchamianie aplikacji usługi Office 365
* Bezpośrednie logowanie do aplikacji federacyjnych (service-pr)
* Linki bezpośrednie do federacyjnych, opartych na hasłach lub istniejących aplikacjach

Można określić, czy użytkownicy przypisani do aplikacji przedsiębiorstwa mogą ją zobaczyć w panelu dostępu i w programie Uruchamianie aplikacji usługi Office 365.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Przykład: złożone przypisanie aplikacji za pomocą usługi Azure AD
Należy wziąć pod uwagę aplikację, taką jak Salesforce. W wielu organizacjach Salesforce jest używany głównie przez zespoły marketingu i sprzedaży. Często członkowie zespołu marketingowego mają bardzo uprzywilejowany dostęp do Salesforce, podczas gdy członkowie zespołu sprzedaży mają ograniczony dostęp. W wielu przypadkach szeroka populacja pracowników informacji ma ograniczony dostęp do aplikacji. Wyjątki od tych zasad komplikują sprawy. Często prerogatywą zespołów kierowniczych marketingu lub sprzedaży jest przyznanie użytkownikowi dostępu lub zmiana ról niezależnie od tych reguł ogólnych.

Za pomocą usługi Azure AD aplikacje, takie jak Salesforce, można wstępnie skonfigurować do logowania jednokrotnego (logowania jednokrotnego) i automatycznego inicjowania obsługi administracyjnej. Po skonfigurowaniu aplikacji administrator może podjąć jednorazową akcję, aby utworzyć i przypisać odpowiednie grupy. W tym przykładzie administrator może wykonać następujące przypisania:

* [Grupy dynamiczne](../fundamentals/active-directory-groups-create-azure-portal.md) można zdefiniować w celu automatycznego reprezentowania wszystkich członków zespołów marketingowych i sprzedażowych przy użyciu atrybutów, takich jak dział lub rola:
  
  * Wszyscy członkowie grup marketingowych zostaną przypisani do roli "marketing" w Salesforce
  * Wszyscy członkowie grup zespołów sprzedaży zostaną przypisani do roli "sprzedaż" w Salesforce. Dalsze udoskonalenie może korzystać z wielu grup, które reprezentują regionalne zespoły sprzedaży przypisane do różnych ról Salesforce.

* Aby włączyć mechanizm wyjątków, można utworzyć grupę samoobsługi dla każdej roli. Na przykład grupa "Wyjątek marketingowy Salesforce" może być utworzona jako grupa samoobsługowa. Grupa może być przypisana do roli marketingowej Salesforce, a zespół liderów marketingu może zostać właścicielem. Członkowie zespołu kierownictwa marketingowego mogą dodawać lub usuwać użytkowników, ustawiać zasady dołączania, a nawet zatwierdzać lub odrzucać prośby poszczególnych użytkowników o dołączenie. Mechanizm ten jest obsługiwany przez pracownika informacji odpowiednie doświadczenie, które nie wymaga specjalistycznego szkolenia dla właścicieli lub członków.

W takim przypadku wszyscy przypisani użytkownicy będą automatycznie aprowizowani do Salesforce, ponieważ są dodawane do różnych grup ich przypisanie roli zostanie zaktualizowane w Salesforce. Użytkownicy będą mogli odnajdywać i uzyskiwać dostęp do salesforce za pośrednictwem panelu dostępu do aplikacji firmy Microsoft, klientów sieci Web pakietu Office, a nawet przechodząc do ich organizacyjnej strony logowania Salesforce. Administratorzy będą mogli łatwo wyświetlać stan użycia i przydziału przy użyciu raportowania usługi Azure AD.

Administratorzy mogą korzystać z [usługi Azure AD Dostęp warunkowy,](../active-directory-conditional-access-azure-portal.md) aby ustawić zasady dostępu dla określonych ról. Te zasady mogą obejmować, czy dostęp jest dozwolony poza środowiskiem firmowym, a nawet uwierzytelniania wieloskładnikowego lub wymagania urządzenia, aby uzyskać dostęp w różnych przypadkach.

## <a name="access-to-microsoft-applications"></a>Dostęp do aplikacji firmy Microsoft

Aplikacje firmy Microsoft (takie jak Usługa Office 365 Exchange, SharePoint, Yammer itp.) są przypisywane i zarządzane nieco inaczej niż aplikacje SaaS innych firm lub inne aplikacje integrane z usługą Azure AD do logowania jednokrotnego.

Istnieją trzy główne sposoby, aby użytkownik mógł uzyskać dostęp do aplikacji opublikowanej przez firmę Microsoft.

- W przypadku aplikacji w usłudze Office 365 lub innych płatnych pakietach użytkownicy mają dostęp za pośrednictwem **przypisania licencji** bezpośrednio do konta użytkownika lub za pośrednictwem grupy korzystającej z naszej możliwości przypisywania licencji opartej na grupach.
- W przypadku aplikacji, które firma Microsoft lub osoba trzecia publikuje swobodnie dla każdego, aby korzystać, użytkownicy mogą uzyskać dostęp za [zgodą użytkownika](configure-user-consent.md). Oznacza to, że logują się do aplikacji za pomocą konta usługi Azure AD Work lub School i umożliwiają jej dostęp do pewnego ograniczonego zestawu danych na swoim koncie.
- W przypadku aplikacji, które firma Microsoft lub osoba trzecia publikuje swobodnie dla każdego, aby korzystać, użytkownicy mogą również uzyskać dostęp za [zgodą administratora](manage-consent-requests.md). Oznacza to, że administrator ustalił, że aplikacja może być używana przez wszystkich w organizacji, więc logują się do aplikacji za pomocą konta administratora globalnego i udzielają dostępu wszystkim osobom w organizacji.

Niektóre aplikacje łączą te metody. Na przykład niektóre aplikacje firmy Microsoft są częścią subskrypcji usługi Office 365, ale nadal wymagają zgody.

Użytkownicy mogą uzyskiwać dostęp do aplikacji usługi Office 365 za pośrednictwem portali usługi Office 365. Aplikacje usługi Office 365 można również wyświetlać lub ukrywać w panelu Dostęp do usługi Moje aplikacje za pomocą [przełącznika widoczności usługi Office 365](hide-application-from-user-portal.md) w **ustawieniach użytkownika**katalogu. 

Podobnie jak w przypadku aplikacji dla przedsiębiorstw, można [przypisać użytkowników](assign-user-or-group-access-portal.md) do niektórych aplikacji firmy Microsoft za pośrednictwem witryny Azure portal lub, jeśli opcja portalu nie jest dostępna, za pomocą programu PowerShell.

## <a name="next-steps"></a>Następne kroki
* [Ochrona aplikacji za pomocą dostępu warunkowego](../active-directory-conditional-access-azure-portal.md)
* [Samoobsługowe zarządzanie grupą/SSAA](../users-groups-roles/groups-self-service-management.md)
