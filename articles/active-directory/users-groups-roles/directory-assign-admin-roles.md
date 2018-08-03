---
title: Przypisywanie ról administratorów w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Roli administratora można dodać użytkowników, przypisywać role administracyjne, resetować hasła użytkowników, zarządzać licencjami użytkowników lub zarządzać domenami. Użytkownik, któremu przypisano rolę administratora ma takie same uprawnienia dla wszystkich usług w chmurze do których zostały zasubskrybowane przez organizację.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/25/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: f5d53b75b8cc47fc8405a334ae9af32faa67a439
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39481057"
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Przypisywanie ról administratorów w usłudze Azure Active Directory

Za pomocą usługi Azure Active Directory (Azure AD), można wyznaczyć oddzielny administratorom różne funkcje. Administratorzy mogą umieszczoną w portalu usługi Azure AD do wykonania zadania, takie jak dodanie lub zmiana użytkowników, przypisywanie ról administracyjnych, resetowanie haseł użytkowników, zarządzanie licencjami użytkowników i zarządzanie nazwami domen.

## <a name="details-about-the-global-administrator-role"></a>Szczegółowe informacje o roli administratora globalnego

Administrator globalny ma dostęp do wszystkich funkcji administracyjnych. Domyślnie osoba, która zarejestruje się w subskrypcji platformy Azure przypisano rolę administratora globalnego dla katalogu. Tylko administratorzy globalni mogą przypisywać pozostałe role administratorów.

## <a name="assign-or-remove-administrator-roles"></a>Przypisywanie lub usuwanie ról administratora

Aby dowiedzieć się, jak przypisywać role administracyjne dla użytkownika w usłudze Azure Active Directory, zobacz [przypisać użytkownika do ról administratora w usłudze Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="available-roles"></a>Dostępne role

Dostępne są następujące role administratora:

* **[Administrator aplikacji](#application-administrator)**: użytkownicy w tej roli można tworzyć i zarządzać wszystkimi aspektami aplikacje dla przedsiębiorstw i rejestracje aplikacji, ustawienia serwera proxy aplikacji. Ta rola daje również możliwość wyrazić zgodę na delegowane uprawnienia i uprawnienia aplikacji, z wyjątkiem programu Microsoft Graph i Azure AD Graph. Członkowie tej roli nie są dodawane jako właścicieli, podczas tworzenia nowej rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

* **[Deweloper aplikacji](#application-developer)**: użytkownicy w tej roli mogą tworzyć rejestracje aplikacji po "Użytkownicy mogą rejestrować aplikacje" został ustawiony na nie. Ta rola pozwala również elementy członkowskie do wyrażenia zgody we własnym imieniu po "Użytkownicy mogą zezwalać aplikacjom uzyskiwanie dostępu do danych firmy w ich imieniu" został ustawiony na nie. Członkowie tej roli są dodawane jako właścicieli, podczas tworzenia nowej rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

* **[Administrator rozliczeń](#billing-administrator)**: dokonuje zakupów, zarządza subskrypcjami, zarządza biletami pomocy technicznej i monitoruje kondycję usługi.

* **[Administrator aplikacji w chmurze](#cloud-application-administrator)**: użytkownicy w tej roli mają takie same uprawnienia, jak roli administratora aplikacji z wyjątkiem możliwości zarządzania serwera proxy aplikacji. Ta rola umożliwia tworzenie i zarządzanie nimi wszystkie aspekty aplikacji dla przedsiębiorstw i rejestracje aplikacji. Ta rola daje również możliwość wyrazić zgodę na delegowane uprawnienia i uprawnienia aplikacji, z wyjątkiem programu Microsoft Graph i Azure AD Graph. Członkowie tej roli nie są dodawane jako właścicieli, podczas tworzenia nowej rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

* **[Administrator do spraw zgodności](#compliance-administrator)**: użytkownicy z tą rolą mają uprawnienia do zarządzania w ramach zabezpieczeń usługi Office 365 i Centrum zgodności i Centrum administracyjnego programu Exchange. Więcej informacji o [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administrator dostępu warunkowego](#conditional-access-administrator)**: użytkownicy z tą rolą mają możliwość zarządzania ustawieniami dostępu warunkowego usługi Azure Active Directory.
  > [!NOTE]
  > Aby wdrożyć zasady dostępu warunkowego programu Exchange ActiveSync na platformie Azure, użytkownik musi być administratorem globalnym.
  
* **[Administratorzy urządzenia](#device-administrators)**: Ta rola jest dostępne do przypisania tylko jako dodatkowego administratora lokalnego w [ustawienia urządzenia](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Użytkownicy z tą rolą stają się administratorami maszyny lokalnej na wszystkich urządzeniach z systemem Windows 10 dołączonych do usługi Azure Active Directory. Nie mają możliwość zarządzania obiektami urządzeń w usłudze Azure Active Directory. 

* **[Odczytywanie katalogów](#directory-readers)**: jest to rola starszej wersji, która ma być przypisana do aplikacji, które nie obsługują [zgody Framework](../develop/active-directory-integrating-applications.md). Nie powinien zostać przypisany do żadnych użytkowników.

* **[Konta synchronizacji katalogu](#directory-synchronization-accounts)**: nie używaj. Ta rola jest przypisywany do usługi Azure AD Connect i jest nie przeznaczone lub obsługiwane w przypadku innych celów.

* **[Zapisywanie katalogów](#directory-writers)**: jest to rola starszej wersji, która ma być przypisana do aplikacji, które nie obsługują [zgody Framework](../develop/active-directory-integrating-applications.md). Nie powinien zostać przypisany do żadnych użytkowników.

* **[Administrator usługi Dynamics 365 / Administrator usługi CRM](#dynamics-365-service-administrator)**: użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Dynamics 365 Online, jeśli usługa została zainstalowana, a także możliwość zarządzania biletami pomocy technicznej i Monitorowanie kondycji usługi. Więcej informacji o [używać roli administratora usługi do zarządzania dzierżawą](https://docs.microsoft.com/en-us/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

* **[Administrator usługi Exchange](#exchange-service-administrator)**: użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft CRM Online, gdy ta usługa została zainstalowana. Więcej informacji o [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administrator globalny / Administrator firmy](#company-administrator)**: użytkownicy z tą rolą mają dostęp do wszystkich funkcji administracyjnych w usłudze Azure Active Directory, a także usług korzystających z tożsamości usługi Azure Active Directory, takich jak usługa Exchange Online SharePoint Online i Skype dla firm Online. Osoba, która zarejestruje się dla dzierżawy usługi Azure Active Directory staje się administratorem globalnym. Tylko administratorzy globalni mogą przypisywać pozostałe role administratorów. Może istnieć więcej niż jednego administratora globalnego w Twojej firmie. Administratorzy globalni mogą resetować hasła dla wszystkich użytkowników oraz wszystkich innych administratorów.

  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator firmy". Jest on "Administrator globalny" [witryny Azure portal](https://portal.azure.com).
  >
  >

* **[Osoba zapraszająca gości](#guest-inviter)**: użytkownicy w tej roli mogą zarządzać zaproszeniami użytkowników gości B2B usługi Azure Active Directory po **członkowie mogą zapraszać** użytkownika został ustawiony na nie. Więcej informacji na temat współpracy B2B w [współpracy usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nie ma żadnych innych uprawnień.

* **[Administrator usługi Information Protection](#information-protection-administrator)**: użytkownicy z tą rolą mają wszystkie uprawnienia w usłudze Azure Information Protection. Ta rola umożliwia konfigurowanie etykiet w zasadach usługi Azure Information Protection, Zarządzanie szablonami ochrony i aktywacja ochrony. Ta rola nie przyznaje wszystkie uprawnienia w Centrum usługi Identity Protection, Privileged Identity Management, kondycji usługi Monitor Office 365, lub Office 365 Centrum zabezpieczeń i zgodności.

* **[Administrator usługi Intune](#intune-service-administrator)**: użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft CRM Online, gdy ta usługa została zainstalowana. Ponadto ta rola oferuje możliwość zarządzania użytkownikami i urządzeniami w celu kojarzenia zasad, a także tworzenie grup i zarządzanie nimi. Więcej informacji o [kontrola administracji opartej na rolach (RBAC) w usłudze Microsoft Intune](https://docs.microsoft.com/en-us/intune/role-based-access-control)

* **[Komunikat czytnika Centrum](#message-center-reader)**: użytkownicy w tej roli można monitorować powiadomienia i aktualizacje porad dotyczących kondycji w [Centrum wiadomości usługi Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) dla swojej organizacji w skonfigurowanych usług, takich jak Exchange i usługi Intune i Microsoft Teams. Czytelnicy Centrum wiadomości otrzymasz tygodniowy skróty e-mail wpisów i aktualizacji i mogą udostępniać wpisy Centrum wiadomości w usłudze Office 365. W usłudze Azure AD użytkownicy przypisani do tej roli tylko mają dostęp tylko do odczytu w usługach Azure AD, takich jak użytkownicy i grupy. 

* **[Partner obsługi 1](#partner-tier1-support)**: nie używaj. Ta rola jest przestarzała i zostanie usunięty z usługi Azure AD w przyszłości. Ta rola jest przeznaczony do użytku przez małą liczbę partnerów firmy Microsoft w odsprzedaży i nie jest przeznaczona do użytku ogólnego.

* **[Partner pomocy technicznej w warstwie 2](#partner-tier2-support)**: nie używaj. Ta rola jest przestarzała i zostanie usunięty z usługi Azure AD w przyszłości. Ta rola jest przeznaczony do użytku przez małą liczbę partnerów firmy Microsoft w odsprzedaży i nie jest przeznaczona do użytku ogólnego.

* **[Administrator haseł / Administrator pomocy technicznej](#helpdesk-administrator)**: użytkownicy z tą rolą mogą zmienić hasła, zarządzać żądaniami obsługi i monitorowania kondycji usługi. Administratorzy pomocy technicznej mogą zmieniać hasła tylko dla użytkowników i innych administratorów pomocy technicznej. 

  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator pomocy technicznej". Jest on "Administrator haseł" [witryny Azure portal](https://portal.azure.com/).
  >
  >
  
* **[Administrator usługi Power BI](#power-bi-service-administrator)**: użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Power BI, gdy usługa jest obecna, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji o [opis roli administratora usługi Power BI](https://docs.microsoft.com/en-us/power-bi/service-admin-role).

* **[Administrator ról uprzywilejowanych](#privileged-role-administrator)**: użytkownicy z tą rolą mogą zarządzać przypisaniami ról w usłudze Azure Active Directory, a także w ramach usługi Azure AD Privileged Identity Management. Ponadto ta rola umożliwia zarządzanie wszystkimi aspektami Privileged Identity Management.

* **[Czytnik raportów](#reports-reader)**: użytkownicy z tą rolą mogą wyświetlać użycia raportowania danych i raporty pulpitu nawigacyjnego w Centrum administracyjnym usługi Office 365 i kontekstu przyjęcia pakietu w usłudze Power BI. Ponadto zapewnia dostęp do logowania jednokrotnego w roli raporty i działania w usłudze Azure AD i interfejsu API raportowania danych zwróconych przez program Microsoft Graph. Użytkownik przypisany do roli czytelnika raportów dostęp tylko odpowiednie użycia i metryk przyjęcia. Nie mają żadnych uprawnień administratora do skonfigurowania ustawień lub dostępu do Centrum administracyjnego określonych produktów, takich jak program Exchange. 

* **[Administrator zabezpieczeń](#security-administrator)**: użytkownicy z tą rolą mają wszystkie uprawnienia tylko do odczytu roli czytelnika zabezpieczeń oraz możliwość zarządzania konfiguracją usług związanych z zabezpieczeniami: Azure Active Directory Identity Protection Usługa Azure Information Protection, Privileged Identity Management i usługi Office 365 Centrum zabezpieczeń i zgodności. Więcej informacji na temat usługi Office 365 uprawnień znajduje się w temacie [uprawnienia w Centrum zgodności i zabezpieczeń usługi Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  | W | Można zrobić |
  | --- | --- |
  | Centrum usługi Identity Protection |<ul><li>Wszystkie uprawnienia roli Czytelnik zabezpieczeń.<li>Ponadto możliwość wykonywania wszystkich operacji IPC z wyjątkiem resetowania haseł. |
  | Privileged Identity Management |<ul><li>Wszystkie uprawnienia roli Czytelnik zabezpieczeń.<li>**Nie można** zarządzania członkostwa w roli usługi Azure AD lub ustawienia. |
  | <p>Monitorowanie kondycji usługi Office 365</p><p>Centrum zabezpieczeń i zgodności usługi Office 365 |<ul><li>Wszystkie uprawnienia roli Czytelnik zabezpieczeń.<li>Można skonfigurować wszystkie ustawienia w funkcji zaawansowanej ochrony przed zagrożeniami (ochrony przed złośliwym oprogramowaniem i wirusami, złośliwy adres URL konfiguracji, adres URL śledzenia itp.). |
  
* **[Czytelnik zabezpieczeń](#security-reader)**: użytkownicy z tą rolą mają globalny dostęp tylko do odczytu, w tym wszystkie informacje w usłudze Azure Active Directory, Identity Protection, Privileged Identity Management, a także możliwość odczytu usługi Azure Active Directory Raporty logowania i dzienników inspekcji. Rola również przyznaje uprawnienia tylko do odczytu w Centrum zgodności i zabezpieczeń usługi Office 365. Więcej informacji na temat usługi Office 365 uprawnień znajduje się w temacie [uprawnienia w Centrum zgodności i zabezpieczeń usługi Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  | W | Można zrobić |
  | --- | --- |
  | Centrum usługi Identity Protection |Odczyt wszystkich raporty dotyczące zabezpieczeń i informacje o ustawieniach dla funkcji zabezpieczeń<ul><li>Antyspamowy<li>Szyfrowanie<li>Ochrona przed utratą danych<li>Chroniące przed złośliwym kodem<li>Zaawansowana ochrona przed zagrożeniami<li>Przed wyłudzaniem<li>Reguły Mailflow |
  | Privileged Identity Management |<p>Ma dostęp tylko do odczytu, aby wszystkie informacje są prezentowane z użyciem usługi Azure AD PIM: zasady i raportów dotyczących przypisania roli usługi Azure AD, zabezpieczenia przeglądów i w przyszłości dostęp do odczytu do zasad, dane i raporty dla scenariuszy, oprócz przypisania roli usługi Azure AD.<p>**Nie można** Zarejestruj się w usłudze Azure AD PIM lub wprowadzać żadnych zmian. W portalu firmy usługi PIM lub za pośrednictwem programu PowerShell ktoś jest w tej roli Aktywacja dodatkowe role (na przykład administrator globalny lub Administrator ról uprzywilejowanych), jeśli użytkownik jest kandydatem do nich. |
  | <p>Monitorowanie kondycji usługi Office 365</p><p>Centrum zabezpieczeń i zgodności usługi Office 365</p> |<ul><li>Przeczytaj alerty i zarządzaj nimi<li>Przeczytaj zasady zabezpieczeń<li>Przeczytaj analizy zagrożeń, odnajdywania aplikacji w chmurze i kwarantanny w wyszukiwaniu i Zbadaj<li>Odczytuj wszystkie raporty |

* **[Administrator pomocy technicznej usługi](#service-support-administrator)**: użytkownicy z tą rolą mogą otwierać żądania pomocy technicznej firmy Microsoft dla usług platformy Azure i usługi Office 365 i widoki pulpitu nawigacyjnego usług i Centrum komunikatów w witrynie Azure portal i portalu administracyjnego usługi Office 365. Więcej informacji o [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administrator usługi SharePoint](#sharepoint-service-administrator)**: użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft SharePoint Online, jeśli usługa została zainstalowana, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji o [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Skype dla firm / Administrator usługi Lync](#lync-service-administrator)**: użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Skype dla firm, gdy usługa jest obecna, a także zarządzać specyficzne dla programu Skype atrybutów użytkownika w usłudze Azure Active Katalog. Ponadto ta rola daje możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji o [dotyczące programu Skype dla firm przypisaną rolę administratora](https://support.office.com/en-us/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5).

  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator usługi Lync". "Usługi Skype dla firm Administrator usługi" jest w [witryny Azure portal](https://portal.azure.com/).
  >
  >

* **[Administrator kont użytkowników](#user-account-administrator)**: użytkownicy, z tą rolą mogą tworzyć i zarządzać wszystkimi aspektami użytkowników i grup. Ponadto ta rola obejmuje możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Obowiązują pewne ograniczenia. Na przykład ta rola nie zezwala na usuwanie administratora globalnego. Administratorzy kont użytkowników można zmienić hasła dla użytkowników, Administratorzy pomocy technicznej i innych administratorów konta użytkownika.

| Można zrobić | Nie można wykonać |
| --- | --- |
| <p>Wyświetlanie informacji firmy i użytkownika</p><p>Zarządzanie biletami pomocy technicznej pakietu Office</p><p>Zmienianie haseł dla użytkowników, Administratorzy pomocy technicznej i innych administratorów konta użytkownika</p><p>Tworzenie i zarządzanie nimi widoki użytkowników</p><p>Tworzenie, edytowanie, usuwanie użytkowników i grup i zarządzanie licencjami użytkowników, z ograniczeniami. Użytkownik nie można usunąć administratora globalnego ani tworzyć innych administratorów.</p> |<p>Wykonywanie operacji rozliczeń i zakupów dla produktów pakietu Office</p><p>Zarządzanie domenami</p><p>Zarządzanie informacjami o firmie</p><p>Delegowanie ról administracyjnych do innych osób</p><p>Używanie synchronizacji katalogów</p><p>Włącz lub Wyłącz uwierzytelnianie wieloskładnikowe</p><p>Wyświetlanie dzienników inspekcji</p> |

## <a name="deprecated-roles"></a>Przestarzałe ról

Nie można używać następujących ról. One zostały przestarzały i zostanie usunięty z usługi Azure AD w przyszłości.

* Administrator licencji ad hoc
* Dołączanie urządzeń
* Menedżerowie urządzenia
* Użytkownicy urządzeń
* Tworzenie użytkowników zweryfikowanych za pośrednictwem poczty e-mail
* Administrator skrzynki pocztowej
* Dołączanie urządzeń w miejscu pracy


### <a name="to-add-a-colleague-as-a-global-administrator"></a>Aby dodać współpracowników jako administrator globalny

1. Zaloguj się do [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym lub administratorem ról uprzywilejowanych w dzierżawie katalogu.

   ![Otwieranie Centrum administracyjne usługi azure AD](./media/directory-assign-admin-roles/active-directory-admin-center.png)

2. Wybierz **użytkowników**.

3. Znajdź użytkownika, który chcesz wyznaczyć jako administrator globalny, a następnie otwórz blok dla tego użytkownika.

4. W bloku użytkownika wybierz **roli w katalogu**.
 
5. W bloku roli katalogu, wybierz **administratora globalnego** roli i Zapisz.

## <a name="detailed-azure-active-directory-permissions"></a>Szczegółowe uprawnienia usługi Azure Active Directory
W poniższych tabelach opisano określone uprawnienia w usłudze Azure Active Directory do każdej roli. Niektóre role, takie jak Administrator globalny może mieć dodatkowe uprawnienia w outide usług Microsoft Azure Active Directory.


### <a name="application-administrator"></a>Administrator aplikacji
Może tworzyć wszystkie aspekty rejestracji aplikacji i aplikacji przedsiębiorstwa oraz zarządzać nimi.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Tworzenie obiektów Applications w usłudze Azure Active Directory. |
| microsoft.aad.directory/Application/Delete | Usuwanie obiektów Applications w usłudze Azure Active Directory. |
| microsoft.aad.directory/Application/Update | Zaktualizuj właściwości standardowych aplikacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Aktualizowanie właściwości Applications.DefaultPolicy w usłudze Azure Active Directory. |
| microsoft.aad.directory/Application/Update/Owners | Aktualizowanie właściwości Applications.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Create | Tworzenie obiektów AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Usuwanie obiektów AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Aktualizowanie właściwości standardowych obiektów AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Usuwanie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Aktualizacja właściwości standardowych w zasadach w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Aktualizacja właściwości Policies.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Może wyrazić zgodę w imieniu wszystkich użytkowników dla wszystkich zasobów z wyjątkiem usługi Azure Active Directory (Azure AD Graph i Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/Create | Tworzenie elementu ServicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Delete | Usuwanie elementu ServicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Aktualizacja właściwości standardowe ServicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Aktualizacja właściwości ServicePrincipals.AppRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Aktualizacja właściwości ServicePrincipals.AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Aktualizacja właściwości ServicePrincipals.DefaultPolicy w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Aktualizacja właściwości ServicePrincipals.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.reports/AllEntities/Read | Odczytywanie raportów usługi Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="application-developer"></a>Deweloper aplikacji
Rejestracje aplikacji można tworzyć niezależnie od **użytkownicy mogą rejestrować aplikacje** ustawienie.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/Application/CreateAsOwner | Tworzenie obiektów Applications w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/AppRoleAssignment/CreateAsOwner | Tworzenie obiektów AppRoleAssignments w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/OAuth2PermissionGrant/CreateAsOwner | Tworzenie elementu OAuth2PermissionGrants w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/ServicePrincipal/CreateAsOwner | Tworzenie elementu ServicePrincipals w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |

### <a name="billing-administrator"></a>Administrator rozliczeń
Może wykonywać typowe zadania związane z rozliczeniami, takie jak aktualizowanie informacji o płatności.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Odczytaj właściwość Organizations.TrustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.directory/Organization/Update | Zaktualizuj właściwości standardowych dla organizacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Zaktualizuj właściwość Organizations.TrustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Kontroli dostępu platformy Azure. |
| microsoft.aad.billing/AllEntities/AllActions | Zarządzaj wszystkimi aspektami rozliczeń usługi Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="cloud-application-administrator"></a>Administrator aplikacji w chmurze
Może tworzyć wszystkie aspekty rejestracji aplikacji i aplikacji przedsiębiorstwa (z wyjątkiem serwera proxy aplikacji) oraz zarządzać nimi.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Tworzenie obiektów Applications w usłudze Azure Active Directory. |
| microsoft.aad.directory/Application/Delete | Usuwanie obiektów Applications w usłudze Azure Active Directory. |
| microsoft.aad.directory/Application/Update | Zaktualizuj właściwości standardowych aplikacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Aktualizowanie właściwości Applications.DefaultPolicy w usłudze Azure Active Directory. |
| microsoft.aad.directory/Application/Update/Owners | Aktualizowanie właściwości Applications.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Create | Tworzenie obiektów AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Usuwanie obiektów AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Aktualizowanie właściwości standardowych obiektów AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Usuwanie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Aktualizacja właściwości standardowych w zasadach w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Aktualizacja właściwości Policies.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Może wyrazić zgodę w imieniu wszystkich użytkowników dla wszystkich zasobów z wyjątkiem usługi Azure Active Directory (Azure AD Graph i Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/Create | Tworzenie elementu ServicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Delete | Usuwanie elementu ServicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Aktualizacja właściwości standardowe ServicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Aktualizacja właściwości ServicePrincipals.AppRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Aktualizacja właściwości ServicePrincipals.AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Aktualizacja właściwości ServicePrincipals.DefaultPolicy w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Aktualizacja właściwości ServicePrincipals.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.reports/AllEntities/Read | Odczytywanie raportów usługi Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="company-administrator"></a>Administrator firmy
Może zarządzać wszystkimi aspektami usług Azure AD i Microsoft korzystających z tożsamości usługi Azure AD. W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator firmy". Jest on "Administrator globalny" [witryny Azure portal](https://portal.azure.com).

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/AllActions/AllProperties | Tworzenie i usuwanie obiektów AdministrativeUnits oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/Application/AllActions/AllProperties | Tworzenie i usuwanie obiektów Applications oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/AllActions/AllProperties | Tworzenie i usuwanie obiektów AppRoleAssignments oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/AllActions/AllProperties | Tworzenie i usuwanie obiektów CollaborationSpaces oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/Contact/AllActions/AllProperties | Tworzenie i usuwanie obiektów Contacts oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/Device/AllActions/AllProperties | Tworzenie i usuwanie obiektów Devices oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/AllActions/AllProperties | Tworzenie i usuwanie obiektów DirectoryRoles oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/DirectoryRoleTemplate/AllActions/AllProperties | Tworzenie i usuwanie obiektów DirectoryRoleTemplates oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/AllActions/AllProperties | Tworzenie i usuwanie obiektów DirectorySettings oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/DirectorySettingTemplate/AllActions/AllProperties | Tworzenie i usuwanie obiektów DirectorySettingTemplates oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/Domain/AllActions/AllProperties | Tworzenie i usuwanie obiektów Domains oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/AllActions/AllProperties | Tworzenie i usuwanie obiektów Groups oraz odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/LoginTenantBranding/AllActions/AllProperties | Tworzenie i usuwanie elementu LoginTenantBrandings, a także odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/OAuth2PermissionGrant/AllActions/AllProperties | Tworzenie i usuwanie elementu OAuth2PermissionGrants, a także odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/AllActions/AllProperties | Tworzenie i usuwanie zasad, a także odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllWithDirectory | Może wyrazić zgodę w imieniu wszystkich użytkowników dla wszystkich zasobów, w tym dla usługi Azure Active Directory (Azure AD Graph i Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/AllActions/AllProperties | Tworzenie i usuwanie elementu ServicePrincipals, a także odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/Organization/AllActions/AllProperties | Tworzenie i usuwanie organizacji, odczytu i aktualizacji wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/AllActions/AllProperties | Tworzenie i usuwanie użytkowników, a także odczytywanie i aktualizowanie wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.aadconnect/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów i odczytywanie i aktualizowanie właściwości standardowe w microsoft.aad.aadconnect. |
| microsoft.aad.accessservice/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Kontroli dostępu platformy Azure. |
| microsoft.aad.billing/AllEntities/AllActions | Zarządzaj wszystkimi aspektami rozliczeń usługi Office 365. |
| microsoft.aad.compliance/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Centrum zgodności. |
| microsoft.aad.directorysync/AllEntities/AllActions | Wykonywanie wszystkich akcji w programie Azure AD Connect. |
| microsoft.aad.lockbox/AllEntities/AllActions | Zarządzaj wszystkimi aspektami usługi skrytki. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | Zarządzaj wszystkimi aspektami usługi Privileged zarządzania rolami. |
| microsoft.aad.reports/AllEntities/AllActions | Odczytywanie i konfigurowanie raportów usługi Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.crm/AllEntities/AllActions | Zarządzaj wszystkimi aspektami usługi Dynamics 365. |
| microsoft.exchange/AllEntities/AllActions | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Zarządzaj wszystkimi aspektami Information Protection. |
| microsoft.intune/AllEntities/AllActions | Zarządzaj wszystkimi aspektami usługi Intune. |
| microsoft.powerbi/AllEntities/AllActions | Zarządzaj wszystkimi aspektami usługi Power BI. |
| microsoft.protectioncenter/AllEntities/AllActions | Zarządzanie Centrum ochrony usługi Office 365. |
| microsoft.sharepoint/AllEntities/AllActions | Zarządzanie usługą SharePoint Online. |
| microsoft.skypeforbusiness/AllEntities/AllActions | Zarządzanie usługą Skype dla firm Online. |

### <a name="compliance-administrator"></a>Administrator zgodności
Może odczytywać konfigurację i raporty zgodności oraz zarządzać nimi w usługach Azure AD i Office 365.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Kontroli dostępu platformy Azure. |
| microsoft.aad.compliance/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Centrum zgodności. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.exchange/Compliance/AllActions | Zarządzanie zgodnością w usłudze Exchange Online. |
| microsoft.sharepoint/Compliance/AllActions | Zarządzanie zgodnością w usłudze SharePoint Online. |
| microsoft.skypeforbusiness/Compliance/AllActions | Zarządzanie zgodnością w programie Skype dla firm Online. |

### <a name="conditional-access-administrator"></a>Administrator dostępu warunkowego
Może zarządzać możliwościami dostępu warunkowego.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/ConditionalAccessPolicy/Create | Utwórz ConditionalAccessPolicys w usłudze Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Delete | Usuń ConditionalAccessPolicys w usłudze Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read | Przeczytaj standardowe właściwości ConditionalAccessPolicys w usłudze Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/Owners | Odczytaj właściwość ConditionalAccessPolicys.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/PolicyAppliedTo | Odczytaj właściwość ConditionalAccessPolicys.PolicyAppliedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update | Aktualizacja właściwości standardowe ConditionalAccessPolicys w usłudze Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update/Owners | Zaktualizuj właściwość ConditionalAccessPolicys.Owners w usłudze Azure Active Directory. |

### <a name="device-administrators"></a>Administratorzy urządzenia

Użytkownicy z tą rolą stają się administratorami maszyny lokalnej na wszystkich urządzeniach z systemem Windows 10 dołączonych do usługi Azure Active Directory. Nie mają możliwość zarządzania obiektami urządzeń w usłudze Azure Active Directory.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

### <a name="directory-readers"></a>Odczytywanie katalogów
Może odczytywać informacje o katalogu podstawowego. Przyznawania dostępu do aplikacji.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/Read | Odczytywanie właściwości standardowych obiektów AdministrativeUnits w usłudze Azure Active Directory. |
| microsoft.aad.directory/AdministrativeUnit/Read/Members | Odczytywanie właściwości AdministrativeUnits.Members w usłudze Azure Active Directory. |
| microsoft.aad.directory/Application/Read | Przeczytaj standardowe właściwości aplikacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/Application/Read/Owners | Odczytywanie właściwości Applications.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/Read | Odczytywanie właściwości standardowych obiektów CollaborationSpaces w usłudze Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/Read/Owners | Odczytywanie właściwości CollaborationSpaces.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/Contact/Read | Odczytywanie właściwości standardowych obiektów Contacts w usłudze Azure Active Directory. |
| microsoft.aad.directory/Contact/Read/MemberOf | Odczytywanie właściwości Contacts.MemberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/Device/Read | Odczytywanie właściwości standardowych obiektów Devices w usłudze Azure Active Directory. |
| microsoft.aad.directory/Device/Read/MemberOf | Odczytywanie właściwości Devices.MemberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/Device/Read/RegisteredOwners | Odczytywanie właściwości Devices.RegisteredOwners w usłudze Azure Active Directory. |
| microsoft.aad.directory/Device/Read/RegisteredUsers | Odczytywanie właściwości Devices.RegisteredUsers w usłudze Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read | Przeczytaj standardowe właściwości DirectoryRoles w usłudze Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read/EligibleMembers | Odczytywanie właściwości DirectoryRoles.EligibleMembers w usłudze Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read/Members | Odczytywanie właściwości DirectoryRoles.Members w usłudze Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Read | Odczytywanie właściwości standardowych obiektów DirectorySettings w usłudze Azure Active Directory. |
| microsoft.aad.directory/DirectorySettingTemplate/Read | Odczytywanie właściwości standardowych obiektów DirectorySettingTemplates w usłudze Azure Active Directory. |
| microsoft.aad.directory/Domain/Read | Odczytywanie właściwości standardowych obiektów Domains w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Przeczytaj standardowe właściwości grupy w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Read/AppRoleAssignments | Odczytywanie właściwości Groups.AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Read/MemberOf | Odczytywanie właściwości Groups.MemberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Members | Odczytywanie właściwości Groups.Members w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Owners | Odczytywanie właściwości Groups.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Settings | Odczytywanie właściwości Groups.Settings w usłudze Azure Active Directory. |
| microsoft.aad.directory/OAuth2PermissionGrant/Read | Odczytywanie właściwości standardowych w elemencie OAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read | Przeczytaj standardowe właściwości ServicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Odczytywanie właściwości ServicePrincipals.AppRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Odczytywanie właściwości ServicePrincipals.AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Odczytywanie właściwości ServicePrincipals.DefaultPolicy w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Odczytywanie właściwości ServicePrincipals.MemberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Odczytywanie właściwości ServicePrincipals.OAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Odczytywanie właściwości ServicePrincipals.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Odczytywanie właściwości ServicePrincipals.OwnedObjects w usłudze Azure Active Directory. |
| microsoft.aad.directory/Organization/Read | Przeczytaj standardowe właściwości dla organizacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Odczytaj właściwość Organizations.TrustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read | Odczyt właściwości standardowych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Odczytywanie właściwości Users.AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/DirectReports | Odczytywanie właściwości Users.DirectReports w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedBy | Odczytywanie właściwości Users.InvitedBy w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedUsers | Odczytywanie właściwości Users.InvitedUsers w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/Manager | Odczytywanie właściwości Users.Manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/MemberOf | Odczytywanie właściwości Users.MemberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Odczytywanie właściwości Users.OAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedDevices | Odczytywanie właściwości Users.OwnedDevices w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedObjects | Odczytywanie właściwości Users.OwnedObjects w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/RegisteredDevices | Odczytywanie właściwości Users.RegisteredDevices w usłudze Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Konta synchronizacji katalogu
Używane tylko przez usługę Azure AD Connect.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/Policy/Create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Usuwanie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Read | Odczytywanie właściwości standardowych w zasadach w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Read/Owners | Odczytywanie właściwości Policies.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Read/PolicyAppliedTo | Odczytywanie właściwości Policies.PolicyAppliedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Aktualizacja właściwości standardowych w zasadach w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Aktualizacja właściwości Policies.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Create | Tworzenie elementu ServicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read | Przeczytaj standardowe właściwości ServicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Odczytywanie właściwości ServicePrincipals.AppRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Odczytywanie właściwości ServicePrincipals.AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Odczytywanie właściwości ServicePrincipals.DefaultPolicy w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Odczytywanie właściwości ServicePrincipals.MemberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Odczytywanie właściwości ServicePrincipals.OAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Odczytywanie właściwości ServicePrincipals.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Odczytywanie właściwości ServicePrincipals.OwnedObjects w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Aktualizacja właściwości standardowe ServicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Aktualizacja właściwości ServicePrincipals.AppRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Aktualizacja właściwości ServicePrincipals.AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Aktualizacja właściwości ServicePrincipals.DefaultPolicy w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Aktualizacja właściwości ServicePrincipals.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/DirSync | Zaktualizuj właściwość Organizations.DirSync w usłudze Azure Active Directory. |
| microsoft.aad.directorysync/AllEntities/AllActions | Wykonywanie wszystkich akcji w programie Azure AD Connect. |

### <a name="directory-writer"></a>Składnik zapisywania katalogu
Może odczytywać i zapisywać informacje katalogu podstawowego. Przyznawania dostępu do aplikacji

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/DirectorySetting/Create | Tworzenie obiektów DirectorySettings w usłudze Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Delete | Usuwanie obiektów DirectorySettings w usłudze Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Update | Aktualizowanie właściwości standardowych obiektów DirectorySettings w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Tworzenie obiektów Groups w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Tworzenie obiektów Groups w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/Group/Read | Przeczytaj standardowe właściwości grupy w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Zaktualizuj właściwości standardowe w grupach w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Aktualizacja właściwości Groups.AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Aktualizacja właściwości Groups.Members w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Aktualizacja właściwości Groups.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Aktualizacja właściwości Groups.Settings w usłudze Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Odczytaj właściwość Organizations.TrustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Unieważnianie wszystkich tokenów odświeżania użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update | Zaktualizuj właściwości standardowych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Aktualizacja właściwości Users.AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Aktualizacja właściwości Users.Manager w usłudze Azure Active Directory. |

### <a name="dynamics-365-service-administrator"></a>Administrator usługi Dynamics 365
Może zarządzać wszystkimi aspektami produktu Dynamics 365.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Odczytaj właściwość Organizations.TrustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Kontroli dostępu platformy Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.crm/AllEntities/AllActions | Zarządzaj wszystkimi aspektami usługi Dynamics 365. |

### <a name="exchange-service-administrator"></a>Administrator usługi Exchange
Może zarządzać wszystkimi aspektami produktu Exchange.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Kontroli dostępu platformy Azure. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.exchange/AllEntities/AllActions | Zarządzaj wszystkimi aspektami usługi Exchange Online. |

### <a name="guest-inviter"></a>Osoba zapraszająca gościa
Może zapraszać użytkowników-gości niezależnie od **członkowie mogą zapraszać gości** ustawienie.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli gości.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/User/InviteGuest | Zapraszanie użytkowników-gości w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read | Odczyt właściwości standardowych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Odczytywanie właściwości Users.AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/DirectReports | Odczytywanie właściwości Users.DirectReports w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedBy | Odczytywanie właściwości Users.InvitedBy w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedUsers | Odczytywanie właściwości Users.InvitedUsers w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/Manager | Odczytywanie właściwości Users.Manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/MemberOf | Odczytywanie właściwości Users.MemberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Odczytywanie właściwości Users.OAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedDevices | Odczytywanie właściwości Users.OwnedDevices w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedObjects | Odczytywanie właściwości Users.OwnedObjects w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Read/RegisteredDevices | Odczytywanie właściwości Users.RegisteredDevices w usłudze Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Administrator pomocy technicznej
Może resetować hasła dla użytkowników niebędących administratorami i administratorów pomocy.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Odczytaj właściwość Organizations.TrustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Unieważnianie wszystkich tokenów odświeżania użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordHelpdeskScope | Zaktualizuj hasła dla administratorów z ograniczonymi uprawnieniami i innych administratorów pomocy technicznej usługi Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| microsoft.aad.accessservice/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Kontroli dostępu platformy Azure. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |

### <a name="information-protection-administrator"></a>Administrator usługi Information Protection
Może zarządzać wszystkimi aspektami produktu Azure Information Protection.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/Group/Read | Przeczytaj standardowe właściwości grupy w usłudze Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Odczytaj właściwość Organizations.TrustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Zarządzaj wszystkimi aspektami Information Protection. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="intune-service-administrator"></a>Administrator usługi Intune
Może zarządzać wszystkimi aspektami produktu Intune.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/Contact/Create | Tworzenie obiektów Contacts w usłudze Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Usuwanie obiektów Contacts w usłudze Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Aktualizowanie właściwości standardowych obiektów Contacts w usłudze Azure Active Directory. |
| microsoft.aad.directory/Device/Create | Tworzenie obiektów Devices w usłudze Azure Active Directory. |
| microsoft.aad.directory/Device/Delete | Usuwanie obiektów Devices w usłudze Azure Active Directory. |
| microsoft.aad.directory/Device/Update | Aktualizowanie właściwości standardowych obiektów Devices w usłudze Azure Active Directory. |
| microsoft.aad.directory/Device/Update/RegisteredOwners | Aktualizowanie właściwości Devices.RegisteredOwners w usłudze Azure Active Directory. |
| microsoft.aad.directory/Device/Update/RegisteredUsers | Aktualizowanie właściwości Devices.RegisteredUsers w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Tworzenie obiektów Groups w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Tworzenie obiektów Groups w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/Group/Delete | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Przeczytaj standardowe właściwości grupy w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Read/HiddenMembers | Odczytywanie właściwości Groups.HiddenMembers w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Przywracanie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Zaktualizuj właściwości standardowe w grupach w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Aktualizacja właściwości Groups.AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Aktualizacja właściwości Groups.Members w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Aktualizacja właściwości Groups.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Aktualizacja właściwości Groups.Settings w usłudze Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Odczytaj właściwość Organizations.TrustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update | Zaktualizuj właściwości standardowych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Aktualizacja właściwości Users.AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Aktualizacja właściwości Users.Manager w usłudze Azure Active Directory. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.intune/AllEntities/AllActions | Zarządzaj wszystkimi aspektami usługi Intune. |

### <a name="lync-service-administrator"></a>Administrator usługi Lync
Może zarządzać wszystkimi aspektami produktu Skype dla firm.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Kontroli dostępu platformy Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.skypeforbusiness/AllEntities/AllActions | Zarządzanie usługą Skype dla firm Online. |

### <a name="message-center-reader"></a>Czytelnik Centrum wiadomości
Może czytać wiadomości i aktualizacje dla swojej organizacji tylko w Centrum wiadomości usługi Office 365. 

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/Group/Read | Przeczytaj standardowe właściwości grupy w usłudze Azure Active Directory. |
| microsoft.aad.accessmessagecenter/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Centrum wiadomości. |
| microsoft.aad.accessservice/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Kontroli dostępu platformy Azure. |

### <a name="partner-tier1-support"></a>Pomoc techniczna dla partnerów (warstwa 1)
Nie używaj — nie są przeznaczone do użytku ogólnego.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Kontroli dostępu platformy Azure. |
| microsoft.aad.directory/Contact/Create | Tworzenie obiektów Contacts w usłudze Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Usuwanie obiektów Contacts w usłudze Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Aktualizowanie właściwości standardowych obiektów Contacts w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Tworzenie obiektów Groups w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Tworzenie obiektów Groups w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/Group/Read | Przeczytaj standardowe właściwości grupy w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Aktualizacja właściwości Groups.Members w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Aktualizacja właściwości Groups.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Odczytaj właściwość Organizations.TrustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Usuwanie użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Unieważnianie wszystkich tokenów odświeżania użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Przywracanie usuniętych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update | Zaktualizuj właściwości standardowych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Aktualizacja właściwości Users.AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Aktualizacja właściwości Users.Manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordUserScope | Aktualizowanie haseł dla użytkowników innych niż administratorzy w usłudze Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="partner-tier2-support"></a>Pomoc techniczna dla partnerów (warstwa 2)
Nie używaj — nie są przeznaczone do użytku ogólnego.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Kontroli dostępu platformy Azure. |
| microsoft.aad.directory/Contact/Create | Tworzenie obiektów Contacts w usłudze Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Usuwanie obiektów Contacts w usłudze Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Aktualizowanie właściwości standardowych obiektów Contacts w usłudze Azure Active Directory. |
| microsoft.aad.directory/Domain/AllActions | Tworzenie i usuwanie obiektów Domains oraz odczytywanie i aktualizowanie właściwości standardowych w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Tworzenie obiektów Groups w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Delete | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Przeczytaj standardowe właściwości grupy w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Przywracanie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Aktualizacja właściwości Groups.Members w usłudze Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Odczytaj właściwość Organizations.TrustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.directory/Organization/Update | Zaktualizuj właściwości standardowych dla organizacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Zaktualizuj właściwość Organizations.TrustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Usuwanie użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Unieważnianie wszystkich tokenów odświeżania użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Przywracanie usuniętych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update | Zaktualizuj właściwości standardowych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Aktualizacja właściwości Users.AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Aktualizacja właściwości Users.Manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update/Password | Zaktualizuj hasła dla wszystkich użytkowników w usłudze Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="power-bi-service-administrator"></a>Administrator usługi Power BI
Może zarządzać wszystkimi aspektami produktu Power BI.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Odczytaj właściwość Organizations.TrustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Kontroli dostępu platformy Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.powerbi/AllEntities/AllActions | Zarządzaj wszystkimi aspektami usługi Power BI. |

### <a name="privileged-role-administrator"></a>Administrator ról uprzywilejowanych
Mogą zarządzać przypisaniami ról w usłudze Azure AD

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/DirectoryRole/Update | Aktualizacja właściwości standardowe DirectoryRoles w usłudze Azure Active Directory. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | Zarządzaj wszystkimi aspektami usługi Privileged zarządzania rolami. |

### <a name="reports-reader"></a>Czytnik raportów
Może odczytywać raporty logowania i inspekcji.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.reports/AllEntities/Read | Odczytywanie raportów usługi Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.usagereports/AllEntities/Read | Raporty użycia odczytu Office 365. |

### <a name="security-administrator"></a>Administrator zabezpieczeń
Może odczytywać informacje o zabezpieczeniach i raporty

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Aktualizowanie właściwości Applications.DefaultPolicy w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Usuwanie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Aktualizacja właściwości standardowych w zasadach w usłudze Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Aktualizacja właściwości Policies.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Aktualizacja właściwości ServicePrincipals.DefaultPolicy w usłudze Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Odczytaj właściwość Organizations.TrustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Kontroli dostępu platformy Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.aad.privilegedrolemanagement/AllEntities/Read | Odczyt wszystkich aspektów Privileged Identity Management. |
| microsoft.protectioncenter/AllEntities/Read | Odczytywanie wszystkich aspektów Centrum ochrony usługi Office 365. |
| microsoft.protectioncenter/AllEntities/Update | Zarządzanie Centrum ochrony usługi Office 365. |

### <a name="security-reader"></a>Odczytywanie zabezpieczeń
Może odczytywać informacje i raporty o zabezpieczeniach w usługach Azure AD i Office 365.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Odczytaj właściwość Organizations.TrustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Kontroli dostępu platformy Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.privilegedidentitymanagement/AllEntities/Read | Odczyt wszystkich aspektów Privileged Identity Management. |
| microsoft.protectioncenter/AllEntities/Read | Odczytywanie wszystkich aspektów Centrum ochrony usługi Office 365. |

### <a name="service-support-administrator"></a>Administrator pomocy technicznej dotyczącej usług
Może odczytywać informacje o kondycji usług i zarządzać biletami pomocy technicznej.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Odczytaj właściwość Organizations.TrustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Kontroli dostępu platformy Azure. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |

### <a name="sharepoint-service-administrator"></a>Administrator usługi programu SharePoint
Może zarządzać wszystkimi aspektami usługi SharePoint.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Zobacz opis roli powyżej, aby uzyskać więcej informacji.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Kontroli dostępu platformy Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.sharepoint/AllEntities/AllActions | Zarządzanie usługą SharePoint Online. |

### <a name="user-account-administrator"></a>Administrator kont użytkowników
Może zarządzać wszystkimi aspektami użytkowników i grup

  > [!NOTE]
  > Ta rola dziedziczy dodatkowych uprawnień od [roli użytkownika](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/AppRoleAssignment/Create | Tworzenie obiektów AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Usuwanie obiektów AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Aktualizowanie właściwości standardowych obiektów AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/Contact/Create | Tworzenie obiektów Contacts w usłudze Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Usuwanie obiektów Contacts w usłudze Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Aktualizowanie właściwości standardowych obiektów Contacts w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Tworzenie obiektów Groups w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Tworzenie obiektów Groups w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/Group/Delete | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Przeczytaj standardowe właściwości grupy w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Read/HiddenMembers | Odczytywanie właściwości Groups.HiddenMembers w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Przywracanie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Zaktualizuj właściwości standardowe w grupach w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Aktualizacja właściwości Groups.AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Aktualizacja właściwości Groups.Members w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Aktualizacja właściwości Groups.Owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Aktualizacja właściwości Groups.Settings w usłudze Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Odczytaj właściwość Organizations.TrustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Create | Tworzenie użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Usuwanie użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Unieważnianie wszystkich tokenów odświeżania użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Przywracanie usuniętych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update | Zaktualizuj właściwości standardowych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Aktualizacja właściwości Users.AppRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Aktualizacja właściwości Users.Manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordUserAcctAdminScope | Zaktualizuj hasła dla administratorów z ograniczonymi uprawnieniami administratorów pomocy technicznej i innych administratorów konta użytkownika w usłudze Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| microsoft.aad.accessservice/AllEntities/AllActions | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Kontroli dostępu platformy Azure. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Odczytywanie i konfigurowanie kondycji usługi Office 365. |

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej o modyfikowaniu administratorów subskrypcji platformy Azure, zobacz [How to add or change Azure administrator roles](../../billing/billing-add-change-azure-subscription-administrator.md) (Jak dodać lub zmienić role administratora platformy Azure).
* Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).
* Aby uzyskać więcej informacji dotyczących relacji między usługą Azure Active Directory i subskrypcją platformy Azure, zobacz [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
