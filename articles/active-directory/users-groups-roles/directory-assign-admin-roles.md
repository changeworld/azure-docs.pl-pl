---
title: Uprawnienia roli administratora w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Roli administratora można dodać użytkowników, przypisywać role administracyjne, resetować hasła użytkowników, zarządzać licencjami użytkowników lub zarządzać domenami.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/25/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 755b94b71fdaefb261741f4d4e756b90d8148280
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116899"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Uprawnienia roli administratora w usłudze Azure Active Directory

Za pomocą usługi Azure Active Directory (Azure AD), można wyznaczyć oddzielny administratorom różne funkcje. Administratorzy mogą umieszczoną w portalu usługi Azure AD do wykonania zadania, takie jak dodanie lub zmiana użytkowników, przypisywanie ról administracyjnych, resetowanie haseł użytkowników, zarządzanie licencjami użytkowników i zarządzanie nazwami domen.

Administrator globalny ma dostęp do wszystkich funkcji administracyjnych. Domyślnie osoba, która zarejestruje się w subskrypcji platformy Azure przypisano rolę administratora globalnego dla katalogu. Tylko administratorzy globalni mogą delegować ról administratora.

## <a name="assign-or-remove-administrator-roles"></a>Przypisywanie lub usuwanie ról administratora

Aby dowiedzieć się, jak przypisywać role administracyjne dla użytkownika w usłudze Azure Active Directory, zobacz [widoku i przypisywanie ról administratorów w usłudze Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Dostępne role

Dostępne są następujące role administratora:

* **[Administrator aplikacji](#application-administrator)**: użytkownicy w tej roli można tworzyć i zarządzać wszystkimi aspektami aplikacje dla przedsiębiorstw i rejestracje aplikacji, ustawienia serwera proxy aplikacji. Ta rola daje również możliwość wyrazić zgodę na delegowane uprawnienia i uprawnienia aplikacji, z wyjątkiem programu Microsoft Graph i Azure AD Graph. Członkowie tej roli nie są dodawane jako właścicieli, podczas tworzenia nowej rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

  <b>Ważne</b>: Ta rola umożliwia zarządzanie poświadczeniami aplikacji. Użytkownicy przypisani do tej roli można dodać poświadczeń do aplikacji i spersonifikować tożsamości aplikacji przy użyciu tych poświadczeń. Jeśli tożsamości aplikacji udzielono dostępu do usługi Azure Active Directory, takie jak możliwość utworzenia lub zaktualizowania użytkownika lub inne obiekty, przypisani do tej roli użytkownika może wykonać te czynności podczas personifikacji aplikacji. Ta możliwość spersonifikować tożsamości aplikacji może być podniesienie uprawnień za pośrednictwem co użytkownik może wykonać za pomocą swoje przypisania roli w usłudze Azure AD. Jest ważne dowiedzieć się, że przypisanie użytkownika do roli administratora aplikacji daje im możliwość personifikacji tożsamość aplikacji.

* **[Deweloper aplikacji](#application-developer)**: użytkownicy w tej roli mogą tworzyć rejestracje aplikacji po "Użytkownicy mogą rejestrować aplikacje" został ustawiony na nie. Ta rola pozwala również elementy członkowskie do wyrażenia zgody we własnym imieniu po "Użytkownicy mogą zezwalać aplikacjom uzyskiwanie dostępu do danych firmy w ich imieniu" został ustawiony na nie. Członkowie tej roli są dodawane jako właścicieli, podczas tworzenia nowej rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

* **[Administrator rozliczeń](#billing-administrator)**: dokonuje zakupów, zarządza subskrypcjami, zarządza biletami pomocy technicznej i monitoruje kondycję usługi.

* **[Administrator aplikacji w chmurze](#cloud-application-administrator)**: użytkownicy w tej roli mają takie same uprawnienia, jak roli administratora aplikacji z wyjątkiem możliwości zarządzania serwera proxy aplikacji. Ta rola umożliwia tworzenie i zarządzanie nimi wszystkie aspekty aplikacji dla przedsiębiorstw i rejestracje aplikacji. Ta rola daje również możliwość wyrazić zgodę na delegowane uprawnienia i uprawnienia aplikacji, z wyjątkiem programu Microsoft Graph i Azure AD Graph. Członkowie tej roli nie są dodawane jako właścicieli, podczas tworzenia nowej rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

  <b>Ważne</b>: Ta rola umożliwia zarządzanie poświadczeniami aplikacji. Użytkownicy przypisani do tej roli można dodać poświadczeń do aplikacji i spersonifikować tożsamości aplikacji przy użyciu tych poświadczeń. Jeśli tożsamości aplikacji udzielono dostępu do usługi Azure Active Directory, takie jak możliwość utworzenia lub zaktualizowania użytkownika lub inne obiekty, przypisani do tej roli użytkownika może wykonać te czynności podczas personifikacji aplikacji. Ta możliwość spersonifikować tożsamości aplikacji może być podniesienie uprawnień za pośrednictwem co użytkownik może wykonać za pomocą swoje przypisania roli w usłudze Azure AD. Jest ważne dowiedzieć się, że przypisanie użytkownika do roli Administrator aplikacji w chmurze daje im możliwość personifikacji tożsamość aplikacji.

* **[Administrator urządzenia w chmurze](#cloud-device-administrator)**: użytkownicy w tej roli można włączyć, wyłączyć, usuwać urządzenia w usłudze Azure AD i klucze do odczytu funkcją BitLocker systemu Windows 10 (jeśli istnieje) w witrynie Azure portal. Rola nie powoduje przyznania uprawnień do zarządzania innych właściwości, na urządzeniu.

* **[Administrator do spraw zgodności](#compliance-administrator)**: użytkownicy z tą rolą mają uprawnienia do zarządzania w ramach zabezpieczeń usługi Office 365 i Centrum zgodności i Centrum administracyjnego programu Exchange. Więcej informacji o [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administrator dostępu warunkowego](#conditional-access-administrator)**: użytkownicy z tą rolą mają możliwość zarządzania ustawieniami dostępu warunkowego usługi Azure Active Directory.
  > [!NOTE]
  > Aby wdrożyć zasady dostępu warunkowego programu Exchange ActiveSync na platformie Azure, użytkownik musi być administratorem globalnym.
  
* **[Administratorzy urządzenia](#device-administrators)**: Ta rola jest dostępne do przypisania tylko jako dodatkowego administratora lokalnego w [ustawienia urządzenia](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Użytkownicy z tą rolą stają się administratorami maszyny lokalnej na wszystkich urządzeniach z systemem Windows 10 dołączonych do usługi Azure Active Directory. Nie mają możliwość zarządzania obiektami urządzeń w usłudze Azure Active Directory. 

* **[Odczytywanie katalogów](#directory-readers)**: jest to rola starszej wersji, która ma być przypisana do aplikacji, które nie obsługują [zgody Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Nie powinien zostać przypisany do żadnych użytkowników.

* **[Konta synchronizacji katalogu](#directory-synchronization-accounts)**: nie używaj. Ta rola jest przypisywany do usługi Azure AD Connect i jest nie przeznaczone lub obsługiwane w przypadku innych celów.

* **[Zapisywanie katalogów](#directory-writers)**: jest to rola starszej wersji, która ma być przypisana do aplikacji, które nie obsługują [zgody Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Nie powinien zostać przypisany do żadnych użytkowników.

* **[Administrator usługi Dynamics 365 / Administrator usługi CRM](#dynamics-365-service-administrator)**: użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Dynamics 365 Online, jeśli usługa została zainstalowana, a także możliwość zarządzania biletami pomocy technicznej i Monitorowanie kondycji usługi. Więcej informacji o [używać roli administratora usługi do zarządzania dzierżawą](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

* **[Administrator usługi Exchange](#exchange-service-administrator)**: użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft CRM Online, gdy ta usługa została zainstalowana. Więcej informacji o [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Administrator globalny / Administrator firmy](#company-administrator)**: użytkownicy z tą rolą mają dostęp do wszystkich funkcji administracyjnych w usłudze Azure Active Directory, a także usług korzystających z tożsamości usługi Azure Active Directory, takich jak usługa Exchange Online SharePoint Online i Skype dla firm Online. Osoba, która zarejestruje się dla dzierżawy usługi Azure Active Directory staje się administratorem globalnym. Tylko administratorzy globalni mogą przypisywać pozostałe role administratorów. Może istnieć więcej niż jednego administratora globalnego w Twojej firmie. Administratorzy globalni mogą resetować hasła dla wszystkich użytkowników oraz wszystkich innych administratorów.

  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator firmy". Jest on "Administrator globalny" [witryny Azure portal](https://portal.azure.com).
  >
  >

* **[Osoba zapraszająca gości](#guest-inviter)**: użytkownicy w tej roli mogą zarządzać zaproszeniami użytkowników gości B2B usługi Azure Active Directory po **członkowie mogą zapraszać** użytkownika został ustawiony na nie. Więcej informacji na temat współpracy B2B w [współpracy usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nie ma żadnych innych uprawnień.

* **[Administrator usługi Information Protection](#information-protection-administrator)**: użytkownicy z tą rolą mają wszystkie uprawnienia w usłudze Azure Information Protection. Ta rola umożliwia konfigurowanie etykiet w zasadach usługi Azure Information Protection, Zarządzanie szablonami ochrony i aktywacja ochrony. Ta rola nie przyznaje wszystkie uprawnienia w Centrum usługi Identity Protection, Privileged Identity Management, kondycji usługi Monitor Office 365, lub Office 365 Centrum zabezpieczeń i zgodności.

* **[Administrator usługi Intune](#intune-service-administrator)**: użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft CRM Online, gdy ta usługa została zainstalowana. Ponadto ta rola oferuje możliwość zarządzania użytkownikami i urządzeniami w celu kojarzenia zasad, a także tworzenie grup i zarządzanie nimi. Więcej informacji o [kontrola administracji opartej na rolach (RBAC) w usłudze Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)

* **[Administrator licencji](#license-administrator)**: użytkownicy w tej roli mogą dodawania, usuwania i aktualizacji przypisań licencji dla użytkowników i grup (z wykorzystaniem Licencjonowanie na podstawie grupy) i zarządzanie lokalizacji użytkowania — na użytkownikach. Rola nie powoduje przyznania możliwość zakupu lub Zarządzaj subskrypcjami Tworzenie lub zarządzać grupami, lub Utwórz lub Zarządzanie użytkownikami poza lokalizacji użytkowania.

* **[Komunikat czytnika Centrum](#message-center-reader)**: użytkownicy w tej roli można monitorować powiadomienia i aktualizacje porad dotyczących kondycji w [Centrum wiadomości usługi Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) dla swojej organizacji w skonfigurowanych usług, takich jak Exchange i usługi Intune i Microsoft Teams. Czytelnicy Centrum wiadomości otrzymasz tygodniowy skróty e-mail wpisów i aktualizacji i mogą udostępniać wpisy Centrum wiadomości w usłudze Office 365. W usłudze Azure AD użytkownicy przypisani do tej roli tylko mają dostęp tylko do odczytu w usługach Azure AD, takich jak użytkownicy i grupy. 

* **[Partner obsługi 1](#partner-tier1-support)**: nie używaj. Ta rola jest przestarzała i zostanie usunięty z usługi Azure AD w przyszłości. Ta rola jest przeznaczony do użytku przez małą liczbę partnerów firmy Microsoft w odsprzedaży i nie jest przeznaczona do użytku ogólnego.

* **[Partner pomocy technicznej w warstwie 2](#partner-tier2-support)**: nie używaj. Ta rola jest przestarzała i zostanie usunięty z usługi Azure AD w przyszłości. Ta rola jest przeznaczony do użytku przez małą liczbę partnerów firmy Microsoft w odsprzedaży i nie jest przeznaczona do użytku ogólnego.

* **[Administrator haseł / Administrator pomocy technicznej](#helpdesk-administrator)**: użytkownicy z tą rolą można zmienić hasła, unieważnienie tokeny odświeżania, zarządzanie żądaniami obsługi i monitorowania kondycji usługi. Unieważnienie token odświeżania wymusza na użytkowniku, aby zalogować się ponownie. Administratorzy pomocy technicznej można resetować hasła i unieważnić tokenów odświeżania innych użytkowników, którzy są użytkowników niebędących administratorami lub członków z następujących ról:
  * Odczytywanie katalogów
  * Osoba zapraszająca gościa
  * Administrator pomocy technicznej
  * Czytelnik Centrum wiadomości
  * Czytnik raportów
  
  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator pomocy technicznej". Jest on "Administrator haseł" [witryny Azure portal](https://portal.azure.com/).
  >
  
* **[Administrator usługi Power BI](#power-bi-service-administrator)**: użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Power BI, gdy usługa jest obecna, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji o [opis roli administratora usługi Power BI](https://docs.microsoft.com/power-bi/service-admin-role).

* **[Administrator ról uprzywilejowanych](#privileged-role-administrator)**: użytkownicy z tą rolą mogą zarządzać przypisaniami ról w usłudze Azure Active Directory, a także w ramach usługi Azure AD Privileged Identity Management. Ponadto ta rola umożliwia zarządzanie wszystkimi aspektami Privileged Identity Management.

  <b>Ważne</b>: Ta rola umożliwia zarządzanie członkostwem we wszystkich rolach usługi Azure AD, łącznie z rolą administratora globalnego. Ta rola nie ma innych możliwości uprzywilejowanego w usłudze Azure AD, takie jak tworzenie lub aktualizowanie użytkowników. Użytkownicy przypisani do tej roli można przyznać sobie ani innych dodatkowych uprawnień, przypisując dodatkowych ról.

* **[Czytnik raportów](#reports-reader)**: użytkownicy z tą rolą mogą wyświetlać użycia raportowania danych i raporty pulpitu nawigacyjnego w Centrum administracyjnym usługi Office 365 i kontekstu przyjęcia pakietu w usłudze Power BI. Ponadto rola zapewnia dostęp do logowania w raportach i działań w usłudze Azure AD i interfejsu API raportowania danych zwróconych przez program Microsoft Graph. Użytkownik przypisany do roli czytelnika raportów dostęp tylko odpowiednie użycia i metryk przyjęcia. Nie mają żadnych uprawnień administratora do skonfigurowania ustawień lub dostępu do Centrum administracyjnego konkretnych produktów, takich jak program Exchange. 

* **[Administrator zabezpieczeń](#security-administrator)**: użytkownicy z tą rolą mają wszystkie uprawnienia tylko do odczytu roli czytelnika zabezpieczeń oraz możliwość zarządzania konfiguracją usług związanych z zabezpieczeniami: Azure Active Directory Identity Protection Usługa Azure Information Protection i usługi Office 365 Centrum zabezpieczeń i zgodności. Więcej informacji na temat usługi Office 365 uprawnień znajduje się w temacie [uprawnienia w Centrum zgodności i zabezpieczeń usługi Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
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

* **[Skype dla firm / Administrator usługi Lync](#lync-service-administrator)**: użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Skype dla firm, gdy usługa jest obecna, a także zarządzać specyficzne dla programu Skype atrybutów użytkownika w usłudze Azure Active Katalog. Ponadto ta rola daje możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi i uzyskać dostęp do zespołów i Skype dla firm Centrum administracyjnego. Konto musi mieć również licencję dla zespołów lub nie można uruchomić polecenia cmdlet programu PowerShell zespołów. Więcej informacji o [dotyczące programu Skype dla firm przypisaną rolę administratora](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) i zespołów, informacje o licencjonowaniu na [Skype dla firm i Microsoft Teams dodatek licencjonowania](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator usługi Lync". "Usługi Skype dla firm Administrator usługi" jest w [witryny Azure portal](https://portal.azure.com/).
  >
  >

* **[Zespoły administratora komunikacji](#teams-communications-administrator)**: użytkownicy w tej roli mogą zarządzać aspektów Microsoft Teams obciążenie związane z głosu & telefonii. W tym narzędzia do zarządzania dla przypisywania numerów telefonów, głos i spotkania zasady i pełny dostęp do narzędzi analizy wywołania.

* **[Zespoły ze specjalistą pomocy technicznej komunikacji](#teams-communications-support-engineer)**: użytkownicy w tej roli można rozwiązywać problemy z komunikacją w ramach Microsoft Teams i Skype dla firm przy użyciu użytkownika wywołać narzędzia do rozwiązywania problemów w Microsoft Teams i Skype dla Centrum administratora firmy. Użytkownicy w tej roli mogą wyświetlać pełną wywołanie rejestrowanie informacji dla wszystkich uczestników związane.

* **[Zespoły, specjalista ds. pomocy technicznej komunikacji](#teams-communications-support-specialist)**: użytkownicy w tej roli można rozwiązywać problemy z komunikacją w ramach Microsoft Teams i Skype dla firm przy użyciu użytkownika wywołać narzędzia do rozwiązywania problemów w Microsoft Teams i Skype dla Centrum administratora firmy. Użytkownicy w tej roli mogą wyświetlać szczegóły użytkownika tylko w wywołaniu dla określonego użytkownika, że ma wyszukiwać.

* **[Zespoły, Administrator usługi](#teams-service-administrator)**: użytkownicy w tej roli mogą zarządzać wszystkimi aspektami obciążenie Microsoft Teams przy użyciu programu Microsoft Teams & Skype Centrum administracyjnego usługi biznesowe i odpowiednie moduły programu PowerShell. W tym, wśród innych obszarów, wszystkie narzędzia do zarządzania związane z telefoniczne, wiadomości, spotkań i same zespoły dbają. Ta rola również przyznaje możliwość zarządzania grupami usługi Office 365.

* **[Administrator kont użytkowników](#user-account-administrator)**: użytkownicy z tą rolą mogą tworzyć użytkowników i zarządzać wszystkimi aspektami użytkowników z pewnymi ograniczeniami (patrz poniżej). Ponadto użytkownicy posiadający tę rolę można tworzyć i zarządzać wszystkich grup. Ta rola obejmuje również możliwość tworzenia i zarządzania widoki użytkowników, zarządzanie biletami pomocy technicznej i monitorowania kondycji usługi.

  | | |
  | --- | --- |
  |Uprawnieniami ogólnymi|<p>Tworzenie użytkowników i grup</p><p>Tworzenie i zarządzanie nimi widoki użytkowników</p><p>Zarządzanie biletami pomocy technicznej pakietu Office|
  |<p>Na wszystkich użytkowników w tym wszystkich administratorów</p>|<p>Zarządzanie licencjami</p><p>Zarządzanie wszystkich właściwości użytkownika, z wyjątkiem główna nazwa użytkownika</p>
  |Tylko na użytkowników, którzy są administratorami bez lub w jednym z następujących ograniczonych ról administratora:<ul><li>Odczytywanie katalogów<li>Osoba zapraszająca gościa<li>Administrator pomocy technicznej<li>Czytelnik Centrum wiadomości<li>Czytnik raportów<li>Administrator kont użytkowników|<p>Usuń i przywracania</p><p>Wyłącz i Włącz</p><p>Unieważnienie tokenów odświeżania</p><p>Zarządzanie wszystkich właściwości użytkownika, w tym nazwa główna użytkownika</p><p>Resetowanie hasła</p><p>Aktualizuj klucze urządzenia (FIDO)</p>

W poniższych tabelach opisano określone uprawnienia w usłudze Azure Active Directory do każdej roli. Niektóre role mogą mieć dodatkowe uprawnienia w usługach firmy Microsoft poza usługą Azure Active Directory.

### <a name="application-administrator"></a>Administrator aplikacji
Może tworzyć wszystkie aspekty rejestracji aplikacji i aplikacji przedsiębiorstwa oraz zarządzać nimi.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/Applications/Audience/Update | Zaktualizuj właściwość applications.audience w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/Authentication/Update | Zaktualizuj właściwość applications.authentication w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/Basic/Update | Aktualizacja podstawowe właściwości aplikacji w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/Create | Tworzenie aplikacji w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/Credentials/Update | Zaktualizuj właściwość applications.credentials w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/DELETE | Usuwanie aplikacji w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/owners/Update | Zaktualizuj właściwość applications.owners w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/permissions/Update | Zaktualizuj właściwość applications.permissions w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/policies/Update | Zaktualizuj właściwość applications.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Utwórz appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Przeczytaj appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Zaktualizuj appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Usuń appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Odczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Zaktualizuj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Usuń zasady w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Odczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Zaktualizuj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Odczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aktualizacja właściwości podstawowe servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Utwórz servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Usuń servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Zaktualizuj właściwość servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Zaktualizuj właściwość servicePrincipals.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Zaktualizuj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Zaktualizuj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.reports/allEntities/read | Odczytywanie raportów usługi Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="application-developer"></a>Deweloper aplikacji
Rejestracje aplikacji można tworzyć niezależnie od "Użytkownicy mogą rejestrować aplikacje" ustawienie.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Tworzenie aplikacji w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Utwórz appRoleAssignments w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Utwórz oAuth2PermissionGrants w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Utwórz servicePrincipals w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |

### <a name="billing-administrator"></a>Administrator rozliczeń
Może wykonywać typowe zadania związane z rozliczeniami, takie jak aktualizowanie informacji o płatności.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/Organization/Basic/Update | Zaktualizuj właściwości podstawowe w organizacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Zaktualizuj właściwość organization.trustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Zarządzaj wszystkimi aspektami rozliczeń usługi Office 365. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="cloud-application-administrator"></a>Administrator aplikacji w chmurze
Może tworzyć wszystkie aspekty rejestracji aplikacji i aplikacji przedsiębiorstwa (z wyjątkiem serwera proxy aplikacji) oraz zarządzać nimi.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/Applications/Audience/Update | Zaktualizuj właściwość applications.audience w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/Authentication/Update | Zaktualizuj właściwość applications.authentication w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/Basic/Update | Aktualizacja podstawowe właściwości aplikacji w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/Create | Tworzenie aplikacji w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/Credentials/Update | Zaktualizuj właściwość applications.credentials w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/DELETE | Usuwanie aplikacji w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/owners/Update | Zaktualizuj właściwość applications.owners w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/permissions/Update | Zaktualizuj właściwość applications.permissions w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/policies/Update | Zaktualizuj właściwość applications.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Utwórz appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Zaktualizuj appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Usuń appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Odczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Zaktualizuj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Usuń zasady w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Odczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Zaktualizuj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Odczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Zaktualizuj właściwość servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Zaktualizuj właściwość servicePrincipals.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aktualizacja właściwości podstawowe servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Utwórz servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Usuń servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Zaktualizuj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Zaktualizuj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.reports/allEntities/read | Odczytywanie raportów usługi Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="cloud-device-administrator"></a>Administrator urządzenia w chmurze
Pełny dostęp do zarządzania urządzeniami w usłudze Azure AD.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/Devices/DELETE | Usuwanie urządzeń w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/disable | Wyłącz urządzenia w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/enable | Włącz urządzeń w usłudze Azure Active Directory. |
| microsoft.aad.reports/allEntities/read | Odczytywanie raportów usługi Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |

### <a name="company-administrator"></a>Administrator firmy
Może zarządzać wszystkimi aspektami usług Azure AD i Microsoft korzystających z tożsamości usługi Azure AD.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia z roli.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Tworzenie i usuwanie administrativeUnits, odczytu i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/allProperties/allTasks | Tworzenie i usuwać aplikacje, Odczyt i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Tworzenie i usuwanie appRoleAssignments, odczytu i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Tworzenie i usuwanie kontaktów, przeczytaj i Aktualizuj wszystkie właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Tworzenie i usunięcia umów, przeczytaj i Aktualizuj wszystkie właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/allProperties/allTasks | Tworzenie i usuwać urządzenia, przeczytaj i Aktualizuj wszystkie właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Tworzenie i usuwanie directoryRoles, odczytu i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Tworzenie i usuwanie directoryRoleTemplates, odczytu i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/domains/allProperties/allTasks | Tworzenie i usuwanie domen, przeczytaj i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/allProperties/allTasks | Tworzenie i usuwanie grup, przeczytaj i Aktualizuj wszystkie właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Tworzenie i usuwanie groupSettings, odczytu i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Tworzenie i usuwanie groupSettingTemplates, odczytu i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Tworzenie i usuwanie loginTenantBranding, odczytu i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Tworzenie i usuwanie oAuth2PermissionGrants, odczytu i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/organization/allProperties/allTasks | Tworzenie i usunąć organizację, przeczytaj i Aktualizuj wszystkie właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/allProperties/allTasks | Tworzenie i usunąć zasady, przeczytaj i Aktualizuj wszystkie właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Tworzenie i usuwanie tym, Odczyt i zaktualizować wszystkie właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Tworzenie i usuwanie roleDefinitions, odczytu i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Tworzenie i usuwanie scopedRoleMemberships, odczytu i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/serviceAction/activateService | Można wykonać akcji usługi Activateservice w usłudze Azure Active Directory |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Można wykonać akcji usługi Disabledirectoryfeature w usłudze Azure Active Directory |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Można wykonać akcji usługi Enabledirectoryfeature w usłudze Azure Active Directory |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Można wykonać akcji usługi Getavailableextentionproperties w usłudze Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Tworzenie i usuwanie servicePrincipals, odczytu i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Tworzenie i usuwanie subscribedSkus, odczytu i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/allProperties/allTasks | Tworzenie i usunąć użytkowników, przeczytaj i Aktualizuj wszystkie właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Wykonywanie wszystkich akcji w programie Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów i odczytywanie i aktualizowanie właściwości standardowe w microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Odczyt wszystkich zasobów w microsoft.aad.privilegedIdentityManagement. |
| microsoft.aad.reports/allEntities/allTasks | Odczytywanie i konfigurowanie raportów usługi Azure AD. |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.informationProtection/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Zarządzaj wszystkimi aspektami rozliczeń usługi Office 365. |
| microsoft.intune/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Intune. |
| Microsoft.office365.complianceManager/allEntities/allTasks | Zarządzać wszystkimi aspektami Office 365 kierownikiem ds. |
| Microsoft.office365.Exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| Microsoft.office365.lockbox/allEntities/allTasks | Zarządzać wszystkimi aspektami skrytki klienta programu Office 365 |
| microsoft.powerApps.powerBI/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Power BI. |
| Microsoft.office365.protectionCenter/allEntities/allTasks | Zarządzaj wszystkimi aspektami Centrum ochrony usługi Office 365. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów i odczytywanie i aktualizowanie właściwości standardowe w microsoft.office365.sharepoint. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Skype dla firm Online. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Dynamics 365. |

### <a name="compliance-administrator"></a>Administrator zgodności
Może odczytywać konfigurację i raporty zgodności oraz zarządzać nimi w usługach Azure AD i Office 365.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| Microsoft.office365.complianceManager/allEntities/allTasks | Zarządzać wszystkimi aspektami Office 365 kierownikiem ds. |
| Microsoft.office365.Exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów i odczytywanie i aktualizowanie właściwości standardowe w microsoft.office365.sharepoint. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Skype dla firm Online. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="conditional-access-administrator"></a>Administrator dostępu warunkowego
Może zarządzać możliwościami dostępu warunkowego.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Odczytaj właściwość policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Zaktualizuj właściwość policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Usuń zasady w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Odczytaj właściwość policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Zaktualizuj właściwość policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Odczytaj właściwość policies.conditionalAccess w usłudze Azure Active Directory. |

### <a name="crm-service-administrator"></a>Administrator usługi CRM
Może zarządzać wszystkimi aspektami produktu Dynamics 365.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Dynamics 365. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="customer-lockbox-access-approver"></a>Osoba zatwierdzająca dostęp do skrytki klienta
Może zatwierdzać żądania pomocy technicznej firmy Microsoft dotyczące uzyskania dostępu do danych organizacyjnych klienta.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| Microsoft.office365.lockbox/allEntities/allTasks | Zarządzać wszystkimi aspektami skrytki klienta programu Office 365 |

### <a name="device-administrators"></a>Administratorzy urządzenia
Członkowie tej roli są dodawane do grupy Administratorzy lokalni na urządzeniach przyłączonych do usługi AD systemu Azure.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Przeczytaj podstawowe właściwości groupSettings w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Przeczytaj podstawowe właściwości groupSettingTemplates w usłudze Azure Active Directory. |

### <a name="directory-readers"></a>Odczytywanie katalogów
Może odczytywać informacje o katalogu podstawowego. Przyznawania dostępu do aplikacji, nie przeznaczone dla użytkowników.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia z roli.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Przeczytaj podstawowe właściwości administrativeUnits w usłudze Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Odczytaj właściwość administrativeUnits.members w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/Audience/Read | Odczytaj właściwość applications.audience w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/Authentication/Read | Odczytaj właściwość applications.authentication w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/Basic/Read | Przeczytaj podstawowe właściwości aplikacji w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/Credentials/Read | Odczytaj właściwość applications.credentials w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/owners/Read | Odczytaj właściwość applications.owners w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/permissions/Read | Odczytaj właściwość applications.permissions w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/policies/Read | Odczytaj właściwość applications.policies w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Basic/Read | Przeczytaj podstawowe właściwości dla kontaktów w usłudze Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Odczytaj właściwość contacts.memberOf w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/contracts/Basic/Read | Przeczytaj podstawowe właściwości dotyczące zamówień w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/Basic/Read | Przeczytaj podstawowe właściwości urządzeń w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Odczytaj właściwość devices.memberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Odczytaj właściwość devices.registeredOwners w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Odczytaj właściwość devices.registeredUsers w usłudze Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/basic/read | Przeczytaj podstawowe właściwości directoryRoles w usłudze Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Odczytaj właściwość directoryRoles.eligibleMembers w usłudze Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Odczytaj właściwość directoryRoles.members w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Domains/Basic/Read | Przeczytaj podstawowe właściwości na temat domen w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Odczytaj właściwość groups.appRoleAssignments w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/Basic/Read | Przeczytaj podstawowe właściwości grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Odczytaj właściwość groups.memberOf w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/members/Read | Odczytaj właściwość groups.members w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/owners/Read | Odczytaj właściwość groups.owners w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/Settings/Read | Odczytaj właściwość groups.settings w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/read | Przeczytaj podstawowe właściwości groupSettings w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Przeczytaj podstawowe właściwości groupSettingTemplates w usłudze Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Przeczytaj podstawowe właściwości oAuth2PermissionGrants w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Organization/Basic/Read | Przeczytaj podstawowe właściwości w organizacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Odczytaj właściwość organization.trustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/basic/read | Przeczytaj podstawowe właściwości w tym usługi Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/basic/read | Przeczytaj podstawowe właściwości roleDefinitions w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Odczytaj właściwość servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Odczytaj właściwość servicePrincipals.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Przeczytaj podstawowe właściwości servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Odczytaj właściwość servicePrincipals.memberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Odczytaj właściwość servicePrincipals.oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Odczytaj właściwość servicePrincipals.ownedObjects w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Odczytaj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Odczytaj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/basic/read | Przeczytaj podstawowe właściwości subscribedSkus w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Odczytaj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Basic/Read | Przeczytaj podstawowe właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Odczytaj właściwość users.directReports w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Odczytaj właściwość users.invitedBy w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Odczytaj właściwość users.invitedUsers w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Manager/Read | Odczytaj właściwość users.manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Odczytaj właściwość users.memberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Odczytaj właściwość users.oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Odczytaj właściwość users.ownedDevices w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Odczytaj właściwość users.ownedObjects w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Odczytaj właściwość users.registeredDevices w usłudze Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Konta synchronizacji katalogu
Używane tylko przez usługę Azure AD Connect.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia z roli.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Zaktualizuj właściwość organization.dirSync w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/policies/Create | Tworzenie zasad w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/policies/DELETE | Usuń zasady w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/policies/Basic/Read | Przeczytaj podstawowe właściwości zasady w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/policies/Basic/Update | Aktualizacja właściwości podstawowe zasady w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/policies/owners/Read | Odczytaj właściwość policies.owners w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/policies/owners/Update | Zaktualizuj właściwość policies.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Odczytaj właściwość policies.policiesAppliedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Odczytaj właściwość servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Zaktualizuj właściwość servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Odczytaj właściwość servicePrincipals.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Zaktualizuj właściwość servicePrincipals.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Przeczytaj podstawowe właściwości servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aktualizacja właściwości podstawowe servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Utwórz servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Odczytaj właściwość servicePrincipals.memberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Odczytaj właściwość servicePrincipals.oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Odczytaj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Zaktualizuj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Odczytaj właściwość servicePrincipals.ownedObjects w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Odczytaj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Zaktualizuj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Wykonywanie wszystkich akcji w programie Azure AD Connect. |

### <a name="directory-writers"></a>Zapisywanie katalogów
Może odczytywać i zapisywać informacje katalogu podstawowego. Przyznawania dostępu do aplikacji, nie przeznaczone dla użytkowników.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/groups/Create | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Tworzenie grup w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Zaktualizuj właściwość groups.appRoleAssignments w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/Basic/Update | Aktualizacja podstawowe właściwości grup w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/members/Update | Zaktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/owners/Update | Zaktualizuj właściwość groups.owners w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/Settings/Update | Zaktualizuj właściwość groups.settings w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/update | Aktualizacja właściwości podstawowe groupSettings w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Utwórz groupSettings w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Usuń groupSettings w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Basic/Update | Aktualizacja podstawowe właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Unieważnianie wszystkich tokenów odświeżania użytkowników w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Manager/Update | Zaktualizuj właściwość users.manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Zaktualizuj właściwość users.userPrincipalName w usłudze Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Administrator usługi Exchange
Może zarządzać wszystkimi aspektami produktu Exchange.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| Microsoft.office365.Exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="guest-inviter"></a>Osoba zapraszająca gościa
Może zapraszać użytkowników-gości niezależnie od ustawienia „członkowie mogą zapraszać gości”.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia z roli.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Odczytaj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Basic/Read | Przeczytaj podstawowe właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Odczytaj właściwość users.directReports w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Odczytaj właściwość users.invitedBy w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Zapraszanie użytkowników-gości w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Odczytaj właściwość users.invitedUsers w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Manager/Read | Odczytaj właściwość users.manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Odczytaj właściwość users.memberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Odczytaj właściwość users.oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Odczytaj właściwość users.ownedDevices w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Odczytaj właściwość users.ownedObjects w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Odczytaj właściwość users.registeredDevices w usłudze Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Administrator pomocy technicznej
Może resetować hasła dla użytkowników niebędących administratorami i administratorów pomocy.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Unieważnianie wszystkich tokenów odświeżania użytkowników w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Password/Update | Zaktualizuj hasła dla wszystkich użytkowników w usłudze Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="information-protection-administrator"></a>Administrator usługi Information Protection
Może zarządzać wszystkimi aspektami produktu Azure Information Protection.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="intune-service-administrator"></a>Administrator usługi Intune
Może zarządzać wszystkimi aspektami produktu Intune.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/Contacts/Basic/Update | Aktualizacja właściwości podstawowe kontakty w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Utwórz kontakty w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/DELETE | Usuwanie kontaktów w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/Basic/Update | Zaktualizuj podstawowe właściwości urządzeń w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/Create | Tworzenie urządzenia w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/DELETE | Usuwanie urządzeń w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Zaktualizuj właściwość devices.registeredOwners w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Zaktualizuj właściwość devices.registeredUsers w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Zaktualizuj właściwość groups.appRoleAssignments w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/Basic/Update | Aktualizacja podstawowe właściwości grup w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/Create | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Tworzenie grup w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| Microsoft.AAD.Directory/groups/DELETE | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Odczytaj właściwość groups.hiddenMembers w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/members/Update | Zaktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/owners/Update | Zaktualizuj właściwość groups.owners w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/Restore | Przywracanie grupy w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/Settings/Update | Zaktualizuj właściwość groups.settings w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Basic/Update | Aktualizacja podstawowe właściwości użytkowników w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Manager/Update | Zaktualizuj właściwość users.manager w usłudze Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.intune/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Intune. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="license-administrator"></a>Administrator licencji
Można zarządzać licencje produktów dla użytkowników i grup.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Zaktualizuj właściwość users.usageLocation w usłudze Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |

### <a name="lync-service-administrator"></a>Administrator usługi Lync
Może zarządzać wszystkimi aspektami produktu Skype dla firm.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Skype dla firm Online. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="message-center-reader"></a>Czytelnik Centrum wiadomości
Może czytać wiadomości i aktualizacje dla swojej organizacji tylko w Centrum wiadomości usługi Office 365. 

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.accessmessagecenter/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie właściwości standardowych w Centrum wiadomości. |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |

### <a name="partner-tier1-support"></a>Pomoc techniczna dla partnerów (warstwa 1)
Nie używaj — nie są przeznaczone do użytku ogólnego.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/Contacts/Basic/Update | Aktualizacja właściwości podstawowe kontakty w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Utwórz kontakty w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/DELETE | Usuwanie kontaktów w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/Create | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Tworzenie grup w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| Microsoft.AAD.Directory/groups/members/Update | Zaktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/owners/Update | Zaktualizuj właściwość groups.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Basic/Update | Aktualizacja podstawowe właściwości użytkowników w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/DELETE | Usuwanie użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Unieważnianie wszystkich tokenów odświeżania użytkowników w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Manager/Update | Zaktualizuj właściwość users.manager w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Password/Update | Zaktualizuj hasła dla wszystkich użytkowników w usłudze Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| Microsoft.AAD.Directory/Users/Restore | Przywracanie usuniętych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Zaktualizuj właściwość users.userPrincipalName w usłudze Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="partner-tier2-support"></a>Pomoc techniczna dla partnerów (warstwa 2)
Nie używaj — nie są przeznaczone do użytku ogólnego.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/Contacts/Basic/Update | Aktualizacja właściwości podstawowe kontakty w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Utwórz kontakty w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/DELETE | Usuwanie kontaktów w usłudze Azure Active Directory. |
| microsoft.aad.directory/domains/allTasks | Tworzenie i usuwanie domen i odczytywanie i aktualizowanie właściwości standardowych w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/Create | Tworzenie grup w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/DELETE | Usuwanie grup w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/members/Update | Zaktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/Restore | Przywracanie grupy w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Organization/Basic/Update | Zaktualizuj właściwości podstawowe w organizacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Zaktualizuj właściwość organization.trustedCAsForPasswordlessAuth w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Basic/Update | Aktualizacja podstawowe właściwości użytkowników w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/DELETE | Usuwanie użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Unieważnianie wszystkich tokenów odświeżania użytkowników w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Manager/Update | Zaktualizuj właściwość users.manager w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Password/Update | Zaktualizuj hasła dla wszystkich użytkowników w usłudze Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| Microsoft.AAD.Directory/Users/Restore | Przywracanie usuniętych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Zaktualizuj właściwość users.userPrincipalName w usłudze Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="power-bi-service-administrator"></a>Administrator usługi Power BI
Może zarządzać wszystkimi aspektami produktu Power BI.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Power BI. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="privileged-role-administrator"></a>Administrator ról uprzywilejowanych
Mogą zarządzać przypisaniami ról w usłudze Azure AD i wszystkimi aspektami Privileged Identity Management.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/directoryRoles/update | Zaktualizuj directoryRoles w usłudze Azure Active Directory. |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów i odczytywanie i aktualizowanie właściwości standardowe w microsoft.aad.privilegedIdentityManagement. |

### <a name="reports-reader"></a>Czytnik raportów
Może odczytywać raporty logowania i inspekcji.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.reports/allEntities/read | Odczytywanie raportów usługi Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.usageReports/allEntities/Read | Raporty użycia odczytu Office 365. |

### <a name="security-administrator"></a>Administrator zabezpieczeń
Może odczytywać informacje o zabezpieczeniach i raporty oraz zarządzać konfiguracją w usłudze Azure AD i Office 365.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/Applications/policies/Update | Zaktualizuj właściwość applications.policies w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/policies/Basic/Update | Aktualizacja właściwości podstawowe zasady w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/policies/Create | Tworzenie zasad w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/policies/DELETE | Usuń zasady w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/policies/owners/Update | Zaktualizuj właściwość policies.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Zaktualizuj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Odczyt wszystkich zasobów w microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Zaktualizuj wszystkie zasoby w microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Odczyt wszystkich zasobów w microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| Microsoft.office365.protectionCenter/allEntities/Read | Odczytywanie wszystkich aspektów Centrum ochrony usługi Office 365. |
| Microsoft.office365.protectionCenter/allEntities/Update | Zaktualizuj wszystkie zasoby w microsoft.office365.protectionCenter. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |

### <a name="security-reader"></a>Odczytywanie zabezpieczeń
Może odczytywać informacje zabezpieczające i raporty o zabezpieczeniach w usługach Azure AD i Office 365.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.identityProtection/allEntities/read | Odczyt wszystkich zasobów w microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Odczyt wszystkich zasobów w microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| Microsoft.office365.protectionCenter/allEntities/Read | Odczytywanie wszystkich aspektów Centrum ochrony usługi Office 365. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |

### <a name="service-support-administrator"></a>Administrator pomocy technicznej dotyczącej usług
Może odczytywać informacje o kondycji usług i zarządzać biletami pomocy technicznej.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="sharepoint-service-administrator"></a>Administrator usługi programu SharePoint
Może zarządzać wszystkimi aspektami usługi SharePoint.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów i odczytywanie i aktualizowanie właściwości standardowe w microsoft.office365.sharepoint. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="teams-communications-administrator"></a>Zespoły komunikacji administratora
Można zarządzać wywołania i funkcje spotkania w usłudze Microsoft Teams.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/policies/Basic/Read | Przeczytaj podstawowe właściwości zasady w usłudze Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| Microsoft.office365.usageReports/allEntities/Read | Raporty użycia odczytu Office 365. |

### <a name="teams-communications-support-engineer"></a>Ze specjalistą pomocy technicznej komunikacji zespołów
Można rozwiązywać problemy łączności z poziomu usługi Teams przy użyciu zaawansowanych narzędzi.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/policies/Basic/Read | Przeczytaj podstawowe właściwości zasady w usłudze Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |

### <a name="teams-communications-support-specialist"></a>Specjalista ds. pomocy technicznej komunikacji zespołów
Można rozwiązywać problemy łączności z poziomu usługi Teams przy użyciu podstawowych narzędzi.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/policies/Basic/Read | Przeczytaj podstawowe właściwości zasady w usłudze Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |

### <a name="teams-service-administrator"></a>Administrator usługi teams
Można zarządzać usługą Microsoft Teams.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Odczytaj właściwość groups.hiddenMembers w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/policies/Basic/Read | Przeczytaj podstawowe właściwości zasady w usłudze Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| Microsoft.office365.usageReports/allEntities/Read | Raporty użycia odczytu Office 365. |

### <a name="user-account-administrator"></a>Administrator kont użytkowników
Może zarządzać wszystkimi aspektami użytkowników i grup, w tym resetowaniem haseł dla administratorów z ograniczonymi uprawnieniami.

  > [!NOTE]
  > Ta rola dziedziczy dodatkowe uprawnienia roli czytelników katalogu.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Utwórz appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Usuń appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Zaktualizuj appRoleAssignments w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Basic/Update | Aktualizacja właściwości podstawowe kontakty w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Utwórz kontakty w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/DELETE | Usuwanie kontaktów w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Zaktualizuj właściwość groups.appRoleAssignments w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/Basic/Update | Aktualizacja podstawowe właściwości grup w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/Create | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Tworzenie grup w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| Microsoft.AAD.Directory/groups/DELETE | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Odczytaj właściwość groups.hiddenMembers w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/members/Update | Zaktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/owners/Update | Zaktualizuj właściwość groups.owners w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/Restore | Przywracanie grupy w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/groups/Settings/Update | Zaktualizuj właściwość groups.settings w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Basic/Update | Aktualizacja podstawowe właściwości użytkowników w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Create | Tworzenie użytkowników w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/DELETE | Usuwanie użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Unieważnianie wszystkich tokenów odświeżania użytkowników w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Manager/Update | Zaktualizuj właściwość users.manager w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Password/Update | Zaktualizuj hasła dla wszystkich użytkowników w usłudze Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| Microsoft.AAD.Directory/Users/Restore | Przywracanie usuniętych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Zaktualizuj właściwość users.userPrincipalName w usłudze Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi dostępu do platformy Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| Microsoft.office365.serviceHealth/allEntities/allTasks | Odczytywanie i konfigurowanie kondycji usługi Office 365. |
| Microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |


## <a name="deprecated-roles"></a>Przestarzałe ról

Nie można używać następujących ról. One są przestarzałe i zostaną usunięte z usługi Azure AD w przyszłości.

* Administrator licencji ad hoc
* Dołączanie urządzeń
* Menedżerowie urządzenia
* Użytkownicy urządzeń
* Tworzenie użytkowników zweryfikowanych za pośrednictwem poczty e-mail
* Administrator skrzynki pocztowej
* Dołączanie urządzeń w miejscu pracy

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat sposobu przypisywania użytkownika jako administratora subskrypcji platformy Azure, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../../role-based-access-control/role-assignments-portal.md)
* Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).
* Aby uzyskać więcej informacji dotyczących relacji między usługą Azure Active Directory i subskrypcją platformy Azure, zobacz [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
