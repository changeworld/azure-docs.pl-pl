---
title: Opisy i uprawnienia roli administratora — Azure Active Directory | Microsoft Docs
description: Rola administratora może dodawać użytkowników, przypisywać role administracyjne, resetować hasła użytkowników, zarządzać licencjami użytkowników lub zarządzać domenami.
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 10/25/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: f769aafa3e2976d63e99cf58a6b67e1b4cf81b64
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72963927"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Uprawnienia roli administratora w Azure Active Directory

Za pomocą Azure Active Directory (Azure AD) można wyznaczyć ograniczonych administratorów do zarządzania zadaniami tożsamości w rolach z niższymi uprawnieniami. Administratorzy mogą być przypisani do takich celów, jak dodawanie lub zmiana użytkowników, przypisywanie ról administracyjnych, resetowanie haseł użytkowników, zarządzanie licencjami użytkowników i zarządzanie nazwami domen. Domyślne uprawnienia użytkownika można zmienić tylko w ustawieniach użytkownika w usłudze Azure AD.

## <a name="limit-the-use-of-global-administrator"></a>Ograniczanie użycia administratora globalnego

Użytkownicy przypisani do roli administratora globalnego mogą odczytywać i modyfikować każde ustawienie administracyjne w organizacji usługi Azure AD. Domyślnie osoba, która zarejestruje się w celu uzyskania subskrypcji platformy Azure, ma przypisaną rolę administratora globalnego dla organizacji usługi Azure AD. Tylko Administratorzy globalni i Administratorzy ról uprzywilejowanych mogą delegować role administratorów. Aby zmniejszyć ryzyko dla Twojej firmy, zalecamy przypisanie tej roli do najmniejszej możliwej liczby osób w organizacji.

Najlepszym rozwiązaniem jest przypisanie tej roli do mniej niż 5 osób w organizacji. Jeśli masz ponad pięciu użytkowników przypisanych do roli administratora globalnego w organizacji, Oto kilka sposobów na zmniejszenie ich użycia.

### <a name="find-the-role-you-need"></a>Znajdź potrzebną rolę

Jeśli frustrujące się, aby znaleźć rolę, której potrzebujesz, z listy wielu ról, usługa Azure AD może wyświetlać podzbiory ról na podstawie kategorii ról. Zapoznaj się z naszym nowym filtrem **typu** dla [ról i administratorów usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) , aby wyświetlić tylko role w wybranym typie.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Rola istnieje teraz, która nie istnieje po przypisaniu roli administratora globalnego

Istnieje możliwość, że rola lub role zostały dodane do usługi Azure AD, która zapewnia bardziej szczegółowe uprawnienia, które nie były opcją w przypadku podniesienia poziomu niektórych użytkowników do administratora globalnego. W miarę upływu czasu wprowadzamy dodatkowe role wykonujące zadania, które mogą wykonać tylko rola administratora globalnego. Są one widoczne w poniższych [dostępnych rolach](#available-roles).

## <a name="assign-or-remove-administrator-roles"></a>Przypisywanie lub usuwanie ról administratorów

Aby dowiedzieć się, jak przypisać role administracyjne do użytkownika w Azure Active Directory, zobacz [Wyświetlanie i przypisywanie ról administratorów w Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Dostępne role

Dostępne są następujące role administratorów:

### <a name="application-administratorapplication-administrator-permissions"></a>[Administrator aplikacji](#application-administrator-permissions)

Użytkownicy w tej roli mogą tworzyć wszystkie aspekty aplikacji przedsiębiorstwa, rejestracji aplikacji i ustawień serwera proxy aplikacji oraz zarządzać nimi. Należy pamiętać, że użytkownicy przypisani do tej roli nie są dodawani jako właściciele podczas tworzenia nowych rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

> [!IMPORTANT]
> Ta rola umożliwia zarządzanie poświadczeniami aplikacji. Użytkownicy przypisani do tej roli mogą dodawać poświadczenia do aplikacji i używać tych poświadczeń do personifikacji tożsamości aplikacji. Jeśli tożsamość aplikacji ma udzielony dostęp do Azure Active Directory, na przykład możliwość tworzenia lub aktualizowania użytkowników lub innych obiektów, wówczas użytkownik przypisany do tej roli może wykonywać te akcje podczas personifikowania aplikacji. Ta możliwość personifikacji tożsamości aplikacji może być podniesieniem uprawnień przez użytkownika za pośrednictwem ich przypisań ról w usłudze Azure AD. Ważne jest, aby zrozumieć, że przypisanie użytkownika do roli administratora aplikacji daje im możliwość personifikacji tożsamości aplikacji.

Ta rola zapewnia również możliwość _wyrażania zgody_ na delegowane uprawnienia i uprawnienia aplikacji, z wyjątkiem uprawnień do Microsoft Graph i wykresu usługi Azure AD.

> [!IMPORTANT]
> Ten wyjątek oznacza, że nadal można wyrazić zgodę na uprawnienia do _innych_ aplikacji (np. aplikacji innych firm lub aplikacji, które zostały zarejestrowane), ale nie do uprawnień do samej usługi Azure AD. Nadal możesz _poprosić_ o te uprawnienia w ramach rejestracji aplikacji, ale _udzielając_ (tj. zgodę na) te uprawnienia wymagają administratora usługi Azure AD. Oznacza to, że złośliwy użytkownik nie może łatwo podnieść poziomu uprawnień, na przykład przez utworzenie i zgodę na dostęp do aplikacji, która może zapisywać w całym katalogu i za pośrednictwem uprawnień tej aplikacji Podnieś poziom do administratora globalnego.

### <a name="application-developerapplication-developer-permissions"></a>[Deweloper aplikacji](#application-developer-permissions)

Użytkownicy w tej roli mogą tworzyć rejestracje aplikacji, gdy ustawienie "użytkownicy mogą rejestrować aplikacje" ma wartość nie. Ta rola przyznaje także uprawnienia do wyrażania zgody w imieniu użytkownika, gdy ustawienie "użytkownicy mogą wyrazić zgodę na dostęp do danych firmowych w ich imieniu" jest ustawione na wartość nie. Użytkownicy przypisani do tej roli są dodawani jako właściciele podczas tworzenia nowych rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

### <a name="authentication-administratorauthentication-administrator-permissions"></a>[Administrator uwierzytelniania](#authentication-administrator-permissions)

Użytkownicy z tą rolą mogą ustawiać lub resetować poświadczenia bez hasła oraz aktualizować hasła dla wszystkich użytkowników. Administratorzy uwierzytelniania mogą wymagać od użytkowników ponownego zarejestrowania istniejących poświadczeń niezwiązanych z hasłem (na przykład MFA lub FIDO) i odwołać się do **zapamiętania usługi MFA na urządzeniu**, które monituje o uwierzytelnianie wieloskładnikowe przy następnym logowaniu użytkowników, którzy nie są administratorami lub tylko przypisane następujące role:

* Administrator uwierzytelniania
* Czytelnicy katalogów
* Zapraszający gościa
* Czytelnik centrum wiadomości
* Czytelnik raportów

> [!IMPORTANT]
> Użytkownicy z tą rolą mogą zmieniać poświadczenia dla osób, które mogą mieć dostęp do poufnych lub prywatnych informacji lub konfiguracji krytycznej wewnątrz i na zewnątrz Azure Active Directory. Zmiana poświadczeń użytkownika może oznaczać, że założono, że tożsamość i uprawnienia tego użytkownika. Na przykład:

* Rejestracja aplikacji i właściciele aplikacji przedsiębiorstwa, którzy mogą zarządzać poświadczeniami aplikacji, których są właścicielami. Te aplikacje mogą mieć uprawnienia uprzywilejowane w usłudze Azure AD, a w innym miejscu nie są przyznawane administratorom uwierzytelniania. Za pomocą tej ścieżki administrator uwierzytelniania może założyć tożsamość właściciela aplikacji, a następnie ponownie założyć tożsamość aplikacji uprzywilejowanej przez zaktualizowanie poświadczeń dla aplikacji.
* Właściciele subskrypcji platformy Azure, którzy mogą mieć dostęp do poufnych lub prywatnych informacji lub konfiguracji krytycznej na platformie Azure.
* Grupa zabezpieczeń i właściciele grupy Office 365, którzy mogą zarządzać członkostwem w grupie. Te grupy mogą udzielić dostępu do poufnych lub prywatnych informacji lub konfiguracji krytycznej w usłudze Azure AD i w innym miejscu.
* Administratorzy w innych usługach poza usługą Azure AD, np. Exchange Online, Centrum zabezpieczeń i zgodności pakietu Office oraz systemy kadr.
* Użytkownicy niebędący administratorami, w tym członkowie kierownictwa, prawnik prawny i pracownicy działu kadr, którzy mogą mieć dostęp do poufnych lub prywatnych informacji.

### <a name="azure-devops-administratorazure-devops-administrator-permissions"></a>[Administrator usługi Azure DevOps](#azure-devops-administrator-permissions)

Użytkownicy z tą rolą mogą zarządzać zasadami usługi Azure DevOps, aby ograniczyć Tworzenie nowej organizacji usługi Azure DevOps do zestawu konfigurowalnych użytkowników lub grup. Użytkownicy w tej roli mogą zarządzać tymi zasadami za pomocą dowolnej organizacji usługi Azure DevOps, która jest objęta organizacją organizacji usługi Azure AD firmy.

Wszystkie zasady usługi Azure DevOps dla przedsiębiorstw mogą być zarządzane przez użytkowników w tej roli.

### <a name="azure-information-protection-administratorazure-information-protection-administrator-permissions"></a>[Azure Information Protection administrator](#azure-information-protection-administrator-permissions)

Użytkownicy z tą rolą mają wszystkie uprawnienia w usłudze Azure Information Protection. Ta rola umożliwia konfigurowanie etykiet dla zasad Azure Information Protection, zarządzanie szablonami ochrony i aktywowanie ochrony. Ta rola nie udziela żadnych uprawnień w centrum ochrony tożsamości, Privileged Identity Management, Monitoruj pakiet Office 365 Service Health lub Centrum zabezpieczeń i zgodności pakietu Office 365.

### <a name="b2c-user-flow-administratorb2c-user-flow-administrator-permissions"></a>[Administrator przepływu użytkownika B2C](#b2c-user-flow-administrator-permissions)

Użytkownicy z tą rolą mogą tworzyć i zarządzać Przepływy użytkownika B2C (nazywane również zasadami "wbudowanymi") w Azure Portal. Tworząc lub edytując przepływy użytkownika, Ci użytkownicy mogą zmieniać zawartość HTML/CSS/JavaScript środowiska użytkownika, zmieniać wymagania usługi MFA dla przepływu użytkownika, zmieniać oświadczenia w tokenie i dostosowywać ustawienia sesji dla wszystkich zasad w dzierżawie. Z drugiej strony ta rola nie obejmuje możliwości przeglądania danych użytkownika ani wprowadzania zmian w atrybutach zawartych w schemacie dzierżawców. Zmiany w strukturze środowiska tożsamości (znane również jako niestandardowe) również są poza zakresem tej roli.

### <a name="b2c-user-flow-attribute-administratorb2c-user-flow-attribute-administrator-permissions"></a>[B2C atrybutu przepływu użytkownika](#b2c-user-flow-attribute-administrator-permissions)

Użytkownicy z tą rolą dodają lub usuwają atrybuty niestandardowe dostępne dla wszystkich przepływów użytkowników w dzierżawie. W związku z tym użytkownicy z tą rolą mogą zmieniać lub dodawać nowe elementy do schematu użytkownika końcowego i wpływać na zachowanie wszystkich przepływów użytkownika, a także pośrednio wprowadzić zmiany w jakie dane mogą być zadawane przez użytkowników końcowych i ostatecznie wysyłane jako oświadczenia do aplikacji. Ta rola nie może edytować przepływów użytkowników.

### <a name="b2c-ief-keyset-administratorb2c-ief-keyset-administrator-permissions"></a>[Administrator zestawu kluczy B2C IEF](#b2c-ief-keyset-administrator-permissions)

Użytkownik może tworzyć klucze zasad i wpisy tajne oraz zarządzać nimi na potrzeby szyfrowania tokenów, podpisów tokenów oraz szyfrowania i odszyfrowywania roszczeń. Przez dodanie nowych kluczy do istniejących kontenerów kluczy, ten ograniczony administrator może przerzucać wpisy tajne w razie potrzeby bez wpływu na istniejące aplikacje. Ten użytkownik może zobaczyć pełną zawartość tych kluczy tajnych i ich daty wygaśnięcia nawet po ich utworzeniu.

> [!IMPORTANT]
> Jest to poufna rola. Rolę administratora zestawu kluczy należy uważnie poddać inspekcji i przypisać ją do środowiska produkcyjnego.

### <a name="b2c-ief-policy-administratorb2c-ief-policy-administrator-permissions"></a>[Administrator zasad B2C IEF](#b2c-ief-policy-administrator-permissions)

Użytkownicy w tej roli mogą tworzyć, odczytywać, aktualizować i usuwać wszystkie niestandardowe zasady w Azure AD B2C i dlatego mieć pełną kontrolę nad strukturą środowiska tożsamości w odpowiedniej dzierżawie Azure AD B2C. Edytując zasady, ten użytkownik może ustanowić bezpośrednią Federacji z zewnętrznymi dostawcami tożsamości, zmienić schemat katalogu, zmienić całą zawartość dodaną przez użytkownika (HTML, CSS, JavaScript), zmienić wymagania dotyczące kończenia uwierzytelniania, utworzyć nowych użytkowników, wysłać dane użytkowników z systemami zewnętrznymi, w tym pełną migracją, i edytują wszystkie informacje o użytkowniku, w tym poufne pola, takie jak hasła i numery telefonów. Z drugiej strony ta rola nie może zmienić kluczy szyfrowania ani edytować wpisów tajnych używanych dla Federacji w dzierżawie.

> [!IMPORTANT]
> IEF zasad jest wysoce poufną rolą, która powinna być przypisana na bardzo ograniczonym poziomie dla dzierżawców w środowisku produkcyjnym. Działania tych użytkowników powinny być ściśle poddawane inspekcji, szczególnie w przypadku dzierżawców w środowisku produkcyjnym.

### <a name="billing-administratorbilling-administrator-permissions"></a>[Administrator rozliczeń](#billing-administrator-permissions)

Dokonuje zakupów, zarządza subskrypcjami, zarządza biletami pomocy technicznej i monitoruje kondycję usługi.

### <a name="cloud-application-administratorcloud-application-administrator-permissions"></a>[Administrator aplikacji w chmurze](#cloud-application-administrator-permissions)

Użytkownicy w tej roli mają takie same uprawnienia jak rola administratora aplikacji, z wyłączeniem możliwości zarządzania serwerem proxy aplikacji. Ta rola umożliwia tworzenie wszystkich aspektów aplikacji dla przedsiębiorstw i rejestracji aplikacji oraz zarządzanie nimi. Ta rola umożliwia również zgodę na uprawnienia delegowane i uprawnienia aplikacji z wyłączeniem Microsoft Graph i Azure AD Graph. Użytkownicy przypisani do tej roli nie są dodawani jako właściciele podczas tworzenia nowych rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

> [!IMPORTANT]
> Ta rola umożliwia zarządzanie poświadczeniami aplikacji. Użytkownicy przypisani do tej roli mogą dodawać poświadczenia do aplikacji i używać tych poświadczeń do personifikacji tożsamości aplikacji. Jeśli tożsamość aplikacji ma udzielony dostęp do Azure Active Directory, na przykład możliwość tworzenia lub aktualizowania użytkowników lub innych obiektów, wówczas użytkownik przypisany do tej roli może wykonywać te akcje podczas personifikowania aplikacji. Ta możliwość personifikacji tożsamości aplikacji może być podniesieniem uprawnień przez użytkownika za pośrednictwem ich przypisań ról w usłudze Azure AD. Ważne jest, aby zrozumieć, że przypisanie użytkownika do roli administratora aplikacji w chmurze daje im możliwość personifikacji tożsamości aplikacji.

### <a name="cloud-device-administratorcloud-device-administrator-permissions"></a>[Administrator urządzenia w chmurze](#cloud-device-administrator-permissions)

Użytkownicy w tej roli mogą włączać, wyłączać i usuwać urządzenia w usłudze Azure AD oraz odczytywać klucze funkcji BitLocker systemu Windows 10 (jeśli istnieją) w Azure Portal. Rola nie udziela uprawnień do zarządzania wszystkimi innymi właściwościami na urządzeniu.

### <a name="compliance-administratorcompliance-administrator-permissions"></a>[Administrator zgodności](#compliance-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia do zarządzania funkcjami zgodności w centrum Microsoft 365 zgodności, Microsoft 365 centrum administracyjnego, platformy Azure i Centrum zabezpieczeń i zgodności pakietu Office 365. Osoby przydzielone mogą również zarządzać wszystkimi funkcjami w centrum administracyjnym programu Exchange i zespołami & centrami administracyjnymi Skype dla firm i tworzyć bilety pomocy technicznej dla platformy Azure i Microsoft 365. Więcej informacji można znaleźć w [tematach role administratorów pakietu Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Podczas | Można wykonać
----- | ----------
[Microsoft 365 Centrum zgodności](https://protection.office.com) | Ochrona danych organizacji i zarządzanie nimi w ramach usług Microsoft 365 Services<br>Zarządzanie alertami zgodności
[Menedżer zgodności](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Śledź, przypisuj i Weryfikuj działania dotyczące zgodności z przepisami organizacji
[Centrum zabezpieczeń i zgodności pakietu Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zarządzanie nadzorem danych<br>Wykonaj badanie prawne i dane<br>Zarządzaj żądaniem podmiotu danych
[Usługa](https://docs.microsoft.com/intune/role-based-access-control) | Wyświetl wszystkie dane inspekcji usługi Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Ma uprawnienia tylko do odczytu i może zarządzać alertami<br>Może tworzyć i modyfikować zasady dotyczące plików i zezwalać na akcje ładu plików<br> Może wyświetlać wszystkie wbudowane raporty w obszarze Zarządzanie danymi

### <a name="compliance-data-administratorcompliance-data-administrator-permissions"></a>[Administrator danych zgodności](#compliance-data-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia do śledzenia danych w centrum Microsoft 365 zgodności, Microsoft 365 centrum administracyjnego i na platformie Azure. Użytkownicy mogą również śledzić dane zgodności w centrum administracyjnym programu Exchange, w Menedżerze zgodności i zespołach & centrum administracyjnym usługi Skype dla firm i tworzyć bilety pomocy technicznej dla platformy Azure i Microsoft 365.

Podczas | Można wykonać
----- | ----------
[Microsoft 365 Centrum zgodności](https://protection.office.com) | Monitorowanie zasad związanych z zgodnością w ramach usług Microsoft 365 Services<br>Zarządzanie alertami zgodności
[Menedżer zgodności](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Śledź, przypisuj i Weryfikuj działania dotyczące zgodności z przepisami organizacji
[Centrum zabezpieczeń i zgodności pakietu Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zarządzanie nadzorem danych<br>Wykonaj badanie prawne i dane<br>Zarządzaj żądaniem podmiotu danych<br><br>Ta rola ma takie same uprawnienia jak w przypadku roli [administratora danych zgodności](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) w ramach kontroli dostępu opartej na rolach w pakiecie Office 365.
[Usługa](https://docs.microsoft.com/intune/role-based-access-control) | Wyświetl wszystkie dane inspekcji usługi Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Ma uprawnienia tylko do odczytu i może zarządzać alertami<br>Może tworzyć i modyfikować zasady dotyczące plików i zezwalać na akcje ładu plików<br> Może wyświetlać wszystkie wbudowane raporty w obszarze Zarządzanie danymi

### <a name="conditional-access-administratorconditional-access-administrator-permissions"></a>[Administrator dostępu warunkowego](#conditional-access-administrator-permissions)

Użytkownicy z tą rolą mają możliwość zarządzania ustawieniami dostępu warunkowego Azure Active Directory.
> [!NOTE]
> Aby wdrożyć zasady dostępu warunkowego programu Exchange ActiveSync na platformie Azure, użytkownik musi być również administratorem globalnym.

### <a name="customer-lockbox-access-approvercustomer-lockbox-access-approver-permissions"></a>[Osoba zatwierdzająca Skrytka klienta dostępu](#customer-lockbox-access-approver-permissions)

Zarządza [żądaniami skrytka klienta](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) w organizacji. Odbierają one powiadomienia e-mail dla żądań Skrytka klienta i mogą zatwierdzać i odrzucać żądania z centrum administracyjnego Microsoft 365. Mogą również włączać lub wyłączać funkcję Skrytka klienta. Tylko Administratorzy globalni mogą resetować hasła osób przypisanych do tej roli.

### <a name="desktop-analytics-administratordesktop-analytics-administrator-permissions"></a>[Administrator usługi Desktop Analytics](#desktop-analytics-administrator-permissions)


Użytkownicy w tej roli mogą zarządzać narzędziami do analizy pulpitu i dostosowywania pakietu Office &ymi usługami zasad. W przypadku usługi Desktop Analytics obejmuje to możliwość wyświetlania spisu zasobów, tworzenia planów wdrażania, wyświetlania stanu wdrożenia i kondycji. Ta rola umożliwia użytkownikom zarządzanie zasadami pakietu Office w przypadku dostosowywania pakietu Office & Policy Service.

### <a name="device-administratordevice-administrators-permissions"></a>[Administrator urządzenia](#device-administrators-permissions)

Ta rola jest dostępna do przypisania tylko jako dodatkowy administrator lokalny w [ustawieniach urządzenia](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Użytkownicy z tą rolą stają się administratorami maszyn lokalnych na wszystkich urządzeniach z systemem Windows 10, które są przyłączone do Azure Active Directory. Nie mają możliwości zarządzania obiektami obiektów w Azure Active Directory.

### <a name="directory-readersdirectory-readers-permissions"></a>[Czytelnicy katalogów](#directory-readers-permissions)

Użytkownicy w tej roli mogą odczytywać podstawowe informacje o katalogu. Ta rola powinna być używana na potrzeby:
* Przyznanie określonego zestawu Gościom dostępu do odczytu zamiast udzielania go wszystkim użytkownikom-Gościom.
* Przyznanie konkretnego zestawu użytkownikom niebędącym administratorami dostępu do witryny Azure Portal w przypadku wybrania opcji "Ogranicz dostęp do portalu usługi Azure AD tylko do administratorów" ma wartość "tak".
* Przyznanie podmiotom usługi dostępu do katalogu, w którym znajduje się katalog. Read. All nie jest opcją.

### <a name="directory-synchronization-accountsdirectory-synchronization-accounts-permissions"></a>[Konta synchronizacji katalogów](#directory-synchronization-accounts-permissions)

Nie należy używać. Ta rola jest automatycznie przypisana do usługi Azure AD Connect i nie jest przeznaczona do użycia ani nie jest obsługiwana w żadnym innym przypadku.

### <a name="directory-writersdirectory-writers-permissions"></a>[Autorzy katalogów](#directory-writers-permissions)

Jest to Starsza rola, która ma zostać przypisana do aplikacji, które nie obsługują [struktury wyrażania zgody](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Nie powinien być przypisany do żadnych użytkowników.

### <a name="dynamics-365-administrator--crm-administratorcrm-service-administrator-permissions"></a>[Administrator programu Dynamics 365/programu CRM](#crm-service-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne w systemie Microsoft Dynamics 365 online, gdy usługa jest obecna, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji na temat [używania roli administratora usługi do zarządzania dzierżawcą](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> W Microsoft Graph interfejsie API usługi Azure AD interfejs API programu Graph i Azure AD PowerShell ta rola jest identyfikowana jako "administrator usługi Dynamics 365." Jest to "administrator systemu Dynamics 365" w [Azure Portal](https://portal.azure.com).

### <a name="exchange-administratorexchange-service-administrator-permissions"></a>[Administrator programu Exchange](#exchange-service-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Exchange Online, gdy usługa jest obecna. Program ma także możliwość tworzenia wszystkich grup pakietu Office 365 i zarządzania nimi, zarządzania biletami pomocy technicznej oraz monitorowania kondycji usługi. Więcej informacji na [temat ról administracyjnych pakietu Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> W Microsoft Graph interfejsie API usługi Azure AD interfejs API programu Graph i Azure AD PowerShell ta rola jest identyfikowana jako "administrator usługi Exchange". Jest to "Administrator programu Exchange" w [Azure Portal](https://portal.azure.com). Jest to "administrator usługi Exchange Online" w [centrum administracyjnym programu Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144).

### <a name="external-identity-provider-administratorexternal-identity-provider-administrator-permissions"></a>[Administrator zewnętrznego dostawcy tożsamości](#external-identity-provider-administrator-permissions)

Ten administrator zarządza Federacją między dzierżawami Azure Active Directory i zewnętrznymi dostawcami tożsamości. Za pomocą tej roli użytkownicy mogą dodawać nowych dostawców tożsamości i konfigurować wszystkie dostępne ustawienia (np. ścieżkę uwierzytelniania, identyfikator usługi, przypisane kontenery kluczy). Ten użytkownik może umożliwić dzierżawcom ufanie uwierzytelnieniom od zewnętrznych dostawców tożsamości. Wynikowy wpływ na środowisko użytkownika końcowego zależy od typu dzierżawy:

* Azure Active Directory dzierżawców dla pracowników i partnerów: dodanie Federacji (np. z użyciem usługi Gmail) natychmiast wpłynie na wszystkie zaproszenia gościa, które nie zostały jeszcze zrealizowane. Zobacz [Dodawanie usługi Google jako dostawcy tożsamości dla użytkowników-Gości B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
* Azure Active Directory B2C dzierżawców: dodanie Federacji (na przykład w usłudze Facebook lub innej organizacji usługi Azure AD) nie wpłynie natychmiast na przepływy użytkowników końcowych, dopóki dostawca tożsamości nie zostanie dodany jako opcja w przepływie użytkownika (nazywany również wbudowanym zasady). Zapoznaj się z przykładem dotyczącym [konfigurowania konto Microsoft jako dostawcy tożsamości](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) . Aby zmienić przepływy użytkowników, wymagana jest ograniczona rola "Administrator przepływu użytkownika B2C".

### <a name="global-administrator--company-administratorcompany-administrator-permissions"></a>[Administrator globalny/administrator firmy](#company-administrator-permissions)

Użytkownicy z tą rolą mają dostęp do wszystkich funkcji administracyjnych w programie Azure Active Directory, a także usług korzystających z tożsamości Azure Active Directory, takich jak Microsoft 365 Security Center, Microsoft 365 Centrum zgodności, Exchange Online, SharePoint Online i Skype dla firm Online. Osoba, która zarejestruje się dla dzierżawy Azure Active Directory, zostaje administratorem globalnym. Tylko Administratorzy globalni mogą przypisywać inne role administratorów. W Twojej firmie może istnieć więcej niż jeden administrator globalny. Administratorzy globalni mogą zresetować hasło dla dowolnego użytkownika i wszystkich innych administratorów.

> [!NOTE]
> W Microsoft Graph interfejsie API usługi Azure AD interfejs API programu Graph i Azure AD PowerShell ta rola jest identyfikowana jako "administrator firmy". Jest to "Administrator globalny" w [Azure Portal](https://portal.azure.com).
>
>

### <a name="global-readerglobal-reader-permissions"></a>[Czytnik globalny](#global-reader-permissions)

Użytkownicy w tej roli mogą odczytywać ustawienia i informacje administracyjne w ramach usług Microsoft 365, ale nie mogą podejmować działań związanych z zarządzaniem. Global Reader to odpowiedni dla administratora globalnego tylko do odczytu. Przypisywanie globalnego czytnika zamiast administratora globalnego do planowania, inspekcji lub badań. Używaj globalnego czytnika w połączeniu z innymi ograniczonymi rolami administratora, takimi jak administrator programu Exchange, aby ułatwić wykonywanie zadań bez przypisywania roli administratora globalnego. Global Reader współpracuje z centrum administracyjnym Microsoft 365, centrum administracyjnym programu Exchange, centrum administracyjnym zespołów, Centrum zabezpieczeń, centrum zgodności, centrum administracyjnym usługi Azure AD i centrum administracyjnym zarządzania urządzeniami.

> [!NOTE]
> Globalna rola czytnika ma teraz kilka ograniczeń —
>
>* Centrum administracyjne programu SharePoint — centrum administracyjne programu SharePoint nie obsługuje roli czytnika globalnego. Nie zobaczysz "SharePoint" w lewym okienku w obszarze centra administracyjne w [Microsoft 365 centrum administracyjnym](https://admin.microsoft.com/Adminportal/Home#/homepage).
>* [Centrum administracyjne usługi OneDrive](https://admin.onedrive.com/) — centrum administracyjne usługi OneDrive nie obsługuje roli czytnika globalnego.
>* [Portal usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) — globalny czytnik nie może odczytać trybu aprowizacji aplikacji dla przedsiębiorstw.
>* [Centrum administracyjne M365](https://admin.microsoft.com/Adminportal/Home#/homepage) — globalny czytnik nie może odczytać żądań skrytki klienta. Nie znajdziesz karty **żądania skrytki klienta** w obszarze **Pomoc techniczna** w lewym okienku Centrum administracyjnego M365.
>* [M365 Security Center](https://security.microsoft.com/homepage) — czytnik globalny nie może odczytać etykiet czułości i przechowywania. W lewym okienku Centrum zabezpieczeń M365 nie znajdziesz **etykiet czułości**, **etykiet przechowywania**oraz kart **analitycznych etykiet** .
>* [Centrum administracyjne zespołów](https://admin.teams.microsoft.com) — globalny czytnik nie może odczytywać **zespołów cykl życia**, **Analiza & raporty**, **Zarządzanie urządzeniami telefonicznymi IP** i **wykaz aplikacji**.
>* [Privileged Access Management (PAM)](https://docs.microsoft.com/en-us/office365/securitycompliance/privileged-access-management-overview) nie obsługuje roli czytnika globalnego.
>* [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) — Global Reader jest obsługiwany tylko w przypadku [raportowania centralnego](https://docs.microsoft.com/azure/information-protection/reports-aip) i gdy dzierżawca nie znajduje się na [ujednoliconej platformie etykietowania](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
>
> Te funkcje są obecnie opracowywane.
>

### <a name="guest-inviterguest-inviter-permissions"></a>[Zapraszający gościa](#guest-inviter-permissions)

Użytkownicy w tej roli mogą zarządzać Azure Active Directory zaproszeniami użytkowników typu B2B, gdy **Członkowie mogą zapraszać** ustawienie użytkownika na wartość nie. Więcej informacji na temat współpracy B2B na [temat współpracy B2B w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nie zawiera żadnych innych uprawnień.

### <a name="helpdesk-administratorhelpdesk-administrator-permissions"></a>[Administrator pomocy technicznej](#helpdesk-administrator-permissions)

Użytkownicy z tą rolą mogą zmieniać hasła, unieważniać tokeny odświeżania, zarządzać żądaniami obsługi oraz monitorować kondycję usługi. Unieważnienie tokenu odświeżania Wymusza ponowne zalogowanie użytkownika. Administratorzy pomocy technicznej mogą resetować hasła i unieważniać tokeny odświeżania innych użytkowników, którzy nie są administratorami lub przypisani tylko do następujących ról:

* Czytelnicy katalogów
* Zapraszający gościa
* Administrator pomocy technicznej
* Czytelnik centrum wiadomości
* Czytelnik raportów

> [!IMPORTANT]
> Użytkownicy z tą rolą mogą zmieniać hasła dla osób, które mogą mieć dostęp do poufnych lub prywatnych informacji lub konfiguracji krytycznej wewnątrz i na zewnątrz Azure Active Directory. Zmiana hasła użytkownika może oznaczać, że założono, że tożsamość i uprawnienia tego użytkownika. Na przykład:

* Rejestracja aplikacji i właściciele aplikacji przedsiębiorstwa, którzy mogą zarządzać poświadczeniami aplikacji, których są właścicielami. Aplikacje te mogą mieć uprawnienia uprzywilejowane w usłudze Azure AD i nie są przyznawane administratorom pomocy technicznej. Za pomocą tej ścieżki Administrator pomocy technicznej może być w stanie założyć tożsamość właściciela aplikacji, a następnie ponownie założyć tożsamość aplikacji uprzywilejowanej przez zaktualizowanie poświadczeń dla aplikacji.
* Właściciele subskrypcji platformy Azure, którzy mogą mieć dostęp do poufnych lub prywatnych informacji lub konfiguracji krytycznej na platformie Azure.
* Grupa zabezpieczeń i właściciele grupy Office 365, którzy mogą zarządzać członkostwem w grupie. Te grupy mogą udzielić dostępu do poufnych lub prywatnych informacji lub konfiguracji krytycznej w usłudze Azure AD i w innym miejscu.
* Administratorzy w innych usługach poza usługą Azure AD, np. Exchange Online, Centrum zabezpieczeń i zgodności pakietu Office oraz systemy kadr.
* Użytkownicy niebędący administratorami, w tym członkowie kierownictwa, prawnik prawny i pracownicy działu kadr, którzy mogą mieć dostęp do poufnych lub prywatnych informacji.

> [!NOTE]
> Delegowanie uprawnień administracyjnych przez podzbiory użytkowników i stosowanie zasad do podzbioru użytkowników jest możliwe z [jednostkami administracyjnymi (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).
>
> Ta rola była wcześniej nazywana "administratorem haseł" w [Azure Portal](https://portal.azure.com/). Twoja nazwa została zmieniona na "Administrator pomocy technicznej" w celu dopasowania jej do nazwy w programie Azure AD PowerShell, usłudze Azure AD interfejs API programu Graph i Microsoft Graph interfejsie API.

### <a name="intune-administratorintune-service-administrator-permissions"></a>[Administrator usługi Intune](#intune-service-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Intune online, gdy usługa jest obecna. Ponadto ta rola obejmuje możliwość zarządzania użytkownikami i urządzeniami w celu kojarzenia zasad, a także tworzenia grup i zarządzania nimi. Więcej informacji na temat [kontroli administracji opartej na rolach (RBAC) z Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)

> [!NOTE]
> W Microsoft Graph interfejsie API usługi Azure AD interfejs API programu Graph i Azure AD PowerShell ta rola jest identyfikowana jako "administrator usługi Intune". Jest to "administrator usługi Intune" w [Azure Portal](https://portal.azure.com).

### <a name="kaizala-administratorkaizala-administrator-permissions"></a>[Usługi kaizala administrator](#kaizala-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne do zarządzania ustawieniami w programie Microsoft usługi kaizala, gdy usługa jest obecna, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Ponadto użytkownik może uzyskiwać dostęp do raportów związanych z wdrażaniem & użyciu usługi kaizala przez członków organizacji i raportów biznesowych wygenerowanych za pomocą akcji usługi kaizala.

### <a name="license-administratorlicense-administrator-permissions"></a>[Administrator licencji](#license-administrator-permissions)

Użytkownicy w tej roli mogą dodawać, usuwać i aktualizować przypisania licencji dla użytkowników, grup (przy użyciu licencjonowania opartego na grupach) i zarządzać lokalizacją użycia na użytkownikach. Rola nie pozwala na kupowanie subskrypcji ani zarządzanie nimi, tworzenie grup ani zarządzanie nimi, a także tworzenie użytkowników poza lokalizacją użycia ani zarządzanie nimi. Ta rola nie ma dostępu do wyświetlania biletów pomocy technicznej, tworzenia ich ani zarządzania nimi.

### <a name="message-center-privacy-readermessage-center-privacy-reader-permissions"></a>[Czytnik prywatności centrum wiadomości](#message-center-privacy-reader-permissions)

Użytkownicy w tej roli mogą monitorować wszystkie powiadomienia w centrum wiadomości, w tym komunikaty o ochronie prywatności danych. Czytelnicy ochrony prywatności centrum wiadomości otrzymują powiadomienia e-mail, w tym dotyczące prywatności danych i mogą anulować subskrypcję przy użyciu preferencji centrum wiadomości. Tylko administrator globalny i czytnik zasad ochrony prywatności centrum wiadomości mogą odczytywać wiadomości dotyczące prywatności danych. Ponadto ta rola zawiera możliwość wyświetlania grup, domen i subskrypcji. Ta rola nie ma uprawnień do wyświetlania, tworzenia ani zarządzania żądaniami obsługi.

### <a name="message-center-readermessage-center-reader-permissions"></a>[Czytelnik centrum wiadomości](#message-center-reader-permissions)

Użytkownicy w tej roli mogą monitorować powiadomienia i aktualizacje kondycji doradców w [centrum komunikatów pakietu Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) dla swojej organizacji na skonfigurowanych usługach, takich jak Exchange, Intune i Microsoft Teams. Czytelnicy centrum wiadomości otrzymują cotygodniowe podsumowanie wiadomości e-mail z ogłoszeń, aktualizacji i mogą udostępniać wpisy centrum wiadomości w pakiecie Office 365. W usłudze Azure AD Użytkownicy przypisani do tej roli będą mieli dostęp tylko do odczytu w ramach usług Azure AD, takich jak użytkownicy i grupy. Ta rola nie ma dostępu do wyświetlania biletów pomocy technicznej, tworzenia ich ani zarządzania nimi.

### <a name="partner-tier1-supportpartner-tier1-support-permissions"></a>[Obsługa pomoc partnera](#partner-tier1-support-permissions)

Nie należy używać. Ta rola jest przestarzała i zostanie usunięta z usługi Azure AD w przyszłości. Ta rola jest przeznaczona do użytku przez małą liczbę partnerów odsprzedaży firmy Microsoft i nie jest przeznaczona do użytku ogólnego.

### <a name="partner-tier2-supportpartner-tier2-support-permissions"></a>[Obsługa SVR partnera](#partner-tier2-support-permissions)

Nie należy używać. Ta rola jest przestarzała i zostanie usunięta z usługi Azure AD w przyszłości. Ta rola jest przeznaczona do użytku przez małą liczbę partnerów odsprzedaży firmy Microsoft i nie jest przeznaczona do użytku ogólnego.

### <a name="password-administratorpassword-administrator-permissions"></a>[Administrator haseł](#password-administrator-permissions)

Użytkownicy z tą rolą mają ograniczoną możliwość zarządzania hasłami. Ta rola nie przyznaje możliwości zarządzania żądaniami obsługi ani monitorowania kondycji usługi. Administratorzy haseł mogą resetować hasła innych użytkowników, którzy nie są administratorami lub tylko członkami następujących ról:

* Czytelnicy katalogów
* Zapraszający gościa
* Administrator haseł

### <a name="power-bi-administratorpower-bi-service-administrator-permissions"></a>[Power BI administrator](#power-bi-service-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne w programie Microsoft Power BI, gdy usługa jest obecna, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji na [temat Power BI roli administratora](https://docs.microsoft.com/power-bi/service-admin-role).

> [!NOTE]
> W Microsoft Graph interfejsie API usługi Azure AD interfejs API programu Graph i Azure AD PowerShell ta rola jest identyfikowana jako "administrator usługi Power BI. W [Azure Portal](https://portal.azure.com)jest "Power BI administrator".

### <a name="privileged-authentication-administratorprivileged-authentication-administrator-permissions"></a>[Administrator uprzywilejowanego uwierzytelniania](#privileged-authentication-administrator-permissions)

Użytkownicy z tą rolą mogą ustawiać lub resetować poświadczenia bez hasła dla wszystkich użytkowników, w tym administratorów globalnych, a także aktualizować hasła dla wszystkich użytkowników. Administratorzy uprzywilejowanego uwierzytelniania mogą zmusić użytkowników do ponownego zarejestrowania istniejących poświadczeń niezwiązanych z hasłem (np. MFA, FIDO) i odwołać "Zapamiętaj usługę MFA na urządzeniu", monitując o usługę MFA przy następnym logowaniu wszystkich użytkowników.

### <a name="privileged-role-administratorprivileged-role-administrator-permissions"></a>[Administrator ról uprzywilejowanych](#privileged-role-administrator-permissions)

Użytkownicy z tą rolą mogą zarządzać przypisaniami ról w Azure Active Directory, a także w Azure AD Privileged Identity Management. Ponadto ta rola umożliwia zarządzanie wszystkimi aspektami Privileged Identity Management i jednostek administracyjnych.

> [!IMPORTANT]
> Ta rola umożliwia zarządzanie przypisaniami wszystkich ról usługi Azure AD, w tym roli administratora globalnego. Ta rola nie obejmuje żadnych innych uprzywilejowanych możliwości w usłudze Azure AD, takich jak tworzenie i aktualizowanie użytkowników. Jednak użytkownicy przypisani do tej roli mogą udzielić sobie lub innym dodatkowym uprawnieniem, przypisując dodatkowe role.

### <a name="reports-readerreports-reader-permissions"></a>[Czytelnik raportów](#reports-reader-permissions)

Użytkownicy z tą rolą mogą wyświetlać dane raportowania użycia i pulpit nawigacyjny raportów w centrum administracyjnym programu Microsoft 365 i pakietem kontekstu wdrażania w programie Power BI. Ponadto rola zapewnia dostęp do raportów i działań związanych z logowaniem w usłudze Azure AD oraz danymi zwracanymi przez interfejs API raportowania Microsoft Graph. Użytkownik przypisany do roli czytelnik raportów może uzyskać dostęp tylko do odpowiednich metryk użycia i wdrażania. Nie mają uprawnień administratora do konfigurowania ustawień lub uzyskiwania dostępu do centrów administracyjnych specyficznych dla produktu, takich jak program Exchange. Ta rola nie ma dostępu do wyświetlania biletów pomocy technicznej, tworzenia ich ani zarządzania nimi.

### <a name="search-administratorsearch-administrator-permissions"></a>[Administrator wyszukiwania](#search-administrator-permissions)

Użytkownicy w tej roli mają pełny dostęp do wszystkich funkcji zarządzania wyszukiwaniem firmy Microsoft w centrum administracyjnym Microsoft 365. Administratorzy wyszukiwania mogą delegować role administratorów wyszukiwania i edytora wyszukiwania do użytkowników, a także tworzyć zawartość, jak zakładki, pytania Q & jako i lokalizacje. Ponadto Ci użytkownicy mogą wyświetlać centrum wiadomości, monitorować kondycję usługi i tworzyć żądania obsługi.

### <a name="search-editorsearch-editor-permissions"></a>[Edytor wyszukiwania](#search-editor-permissions)

Użytkownicy w tej roli mogą tworzyć i usuwać zawartość usługi Microsoft Search w centrum administracyjnym Microsoft 365, w tym zakładki, pytania Q & jako lokalizacje oraz zarządzać nimi.

### <a name="security-administratorsecurity-administrator-permissions"></a>[Administrator zabezpieczeń](#security-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia do zarządzania funkcjami związanymi z zabezpieczeniami w programie Microsoft 365 Security Center, Azure Active Directory Identity Protection, Azure Information Protection i Centrum zabezpieczeń i zgodności pakietu Office 365. Więcej informacji o uprawnieniach pakietu Office 365 jest dostępnych w [uprawnieniach w centrum zabezpieczeń i zgodności pakietu office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

Podczas | Można wykonać
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Monitorowanie zasad związanych z zabezpieczeniami w ramach usług Microsoft 365 Services<br>Zarządzanie zagrożeniami i alertami zabezpieczeń<br>Wyświetlanie raportów
Centrum ochrony tożsamości | Wszystkie uprawnienia roli czytelnik zabezpieczeń<br>Ponadto możliwość wykonywania wszystkich operacji usługi Identity Protection Center z wyjątkiem resetowania haseł
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Wszystkie uprawnienia roli czytelnik zabezpieczeń<br>**Nie można** zarządzać przypisaniami ról lub ustawieniami usługi Azure AD
[Centrum zabezpieczeń i zgodności pakietu Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zarządzanie zasadami zabezpieczeń<br>Wyświetlanie, badanie i reagowanie na zagrożenia bezpieczeństwa<br>Wyświetlanie raportów
Zaawansowana ochrona przed zagrożeniami w usłudze Azure | Monitoruj podejrzane działania zabezpieczeń i odpowiadaj na nie
Windows Defender ATP i EDR | Przypisywanie ról<br>Zarządzanie grupami maszyn<br>Konfigurowanie wykrywania zagrożeń punktów końcowych i zautomatyzowanego korygowania<br>Wyświetlanie alertów, badanie ich i reagowanie na nie
[Usługa](https://docs.microsoft.com/intune/role-based-access-control) | Wyświetla informacje o użytkownikach, urządzeniach, rejestracji, konfiguracji i aplikacji<br>Nie można wprowadzać zmian w usłudze Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Dodawanie administratorów, Dodawanie zasad i ustawień, przekazywanie dzienników i wykonywanie akcji ładu
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Może wyświetlać zasady zabezpieczeń, wyświetlać Stany zabezpieczeń, edytować zasady zabezpieczeń, wyświetlać alerty i zalecenia, odrzucać alerty i zalecenia
[Kondycja usługi Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Wyświetlanie kondycji usług Office 365

### <a name="security-operatorsecurity-operator-permissions"></a>[Operator zabezpieczeń](#security-operator-permissions)

Użytkownicy z tą rolą mogą zarządzać alertami i mieć globalny dostęp tylko do odczytu do funkcji związanych z zabezpieczeniami, w tym wszystkie informacje w Microsoft 365 Security Center, Azure Active Directory, Identity Protection, Privileged Identity Management i Office 365 Centrum zabezpieczeń i zgodności. Więcej informacji o uprawnieniach pakietu Office 365 jest dostępnych w [uprawnieniach w centrum zabezpieczeń i zgodności pakietu office 365](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

Podczas | Można wykonać
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Wszystkie uprawnienia roli czytelnik zabezpieczeń<br>Wyświetlanie alertów dotyczących zagrożeń zabezpieczeń, badanie ich i reagowanie na nie
Centrum ochrony tożsamości | Wszystkie uprawnienia roli czytelnik zabezpieczeń<br>Ponadto możliwość wykonywania wszystkich operacji usługi Identity Protection Center z wyjątkiem resetowania haseł
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Wszystkie uprawnienia roli czytelnik zabezpieczeń
[Centrum zabezpieczeń i zgodności pakietu Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Wszystkie uprawnienia roli czytelnik zabezpieczeń<br>Wyświetlanie alertów zabezpieczeń, badanie ich i reagowanie na nie
Windows Defender ATP i EDR | Wszystkie uprawnienia roli czytelnik zabezpieczeń<br>Wyświetlanie alertów zabezpieczeń, badanie ich i reagowanie na nie
[Usługa](https://docs.microsoft.com/intune/role-based-access-control) | Wszystkie uprawnienia roli czytelnik zabezpieczeń
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Wszystkie uprawnienia roli czytelnik zabezpieczeń
[Kondycja usługi Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Wyświetlanie kondycji usług Office 365

### <a name="security-readersecurity-reader-permissions"></a>[Czytelnik zabezpieczeń](#security-reader-permissions)

Użytkownicy z tą rolą mają globalny dostęp tylko do odczytu w funkcji związanych z zabezpieczeniami, w tym wszystkie informacje w Microsoft 365 Centrum zabezpieczeń, Azure Active Directory, Ochrona tożsamości, Privileged Identity Management, a także możliwość odczytywania usługi Azure Active Raporty logowania do katalogu i dzienniki inspekcji oraz w pakiecie Office 365 Centrum zabezpieczeń i zgodności. Więcej informacji o uprawnieniach pakietu Office 365 jest dostępnych w [uprawnieniach w centrum zabezpieczeń i zgodności pakietu office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

Podczas | Można wykonać
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Wyświetlanie zasad związanych z zabezpieczeniami w ramach usług Microsoft 365 Services<br>Wyświetl zagrożenia i alerty zabezpieczeń<br>Wyświetlanie raportów
Centrum ochrony tożsamości | Przeczytaj wszystkie raporty i ustawienia zabezpieczeń dotyczące funkcji zabezpieczeń<br><ul><li>Antyspam<li>Szyfrowanie<li>Ochrona przed utratą danych<li>Ochrona przed złośliwym oprogramowaniem<li>Zaawansowana ochrona przed zagrożeniami<li>Ochrona przed phishingiem<li>Reguły Mailflow
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Ma dostęp tylko do odczytu do wszystkich informacji podanych w Azure AD Privileged Identity Management: zasady i raporty dotyczące przypisań ról i przeglądów zabezpieczeń usługi Azure AD.<br>**Nie można** zarejestrować się w usłudze Azure AD Privileged Identity Management ani wprowadzać w niej żadnych zmian. W portalu Privileged Identity Management lub za pośrednictwem programu PowerShell ktoś w tej roli może aktywować dodatkowe role (na przykład administratorów globalnych lub administrator ról uprzywilejowanych), jeśli użytkownik jest uprawniony do korzystania z nich.
[Centrum zabezpieczeń i zgodności pakietu Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Wyświetlanie zasad zabezpieczeń<br>Wyświetl i zbadaj zagrożenia bezpieczeństwa<br>Wyświetlanie raportów
Windows Defender ATP i EDR | Wyświetlanie i badanie alertów. Po włączeniu kontroli dostępu opartej na rolach w usłudze Windows Defender ATP użytkownicy z uprawnieniami tylko do odczytu, takimi jak rola czytnika zabezpieczeń usługi Azure AD, tracą dostęp do momentu przypisania ich do roli usługi Windows Defender ATP.
[Usługa](https://docs.microsoft.com/intune/role-based-access-control) | Wyświetla informacje o użytkownikach, urządzeniach, rejestracji, konfiguracji i aplikacji. Nie można wprowadzać zmian w usłudze Intune.
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Ma uprawnienia tylko do odczytu i może zarządzać alertami
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Może wyświetlać zalecenia i alerty, wyświetlać zasady zabezpieczeń, wyświetlać Stany zabezpieczeń, ale nie mogą wprowadzać zmian
[Kondycja usługi Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Wyświetlanie kondycji usług Office 365

### <a name="service-support-administratorservice-support-administrator-permissions"></a>[Administrator pomocy technicznej usługi](#service-support-administrator-permissions)

Użytkownicy z tą rolą mogą otwierać żądania pomocy technicznej w ramach usług firmy Microsoft dla platformy Azure i pakietu Office 365 i przegląda pulpit nawigacyjny usługi i centrum wiadomości w [Azure Portal](https://portal.azure.com) i [Microsoft 365 centrum administracyjnego](https://admin.microsoft.com). Więcej informacji na [temat ról administratorów](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> W Microsoft Graph interfejsie API usługi Azure AD interfejs API programu Graph i Azure AD PowerShell ta rola jest identyfikowana jako "Administrator pomocy technicznej usługi". Jest to "administrator usługi" w [Azure Portal](https://portal.azure.com), [centrum administracyjnym Microsoft 365](https://admin.microsoft.com)i portalu usługi Intune.

### <a name="sharepoint-administratorsharepoint-service-administrator-permissions"></a>[Administrator programu SharePoint](#sharepoint-service-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft SharePoint Online, gdy usługa jest obecna, a także możliwość tworzenia wszystkich grup pakietu Office 365 i zarządzania nimi, zarządzania biletami pomocy technicznej oraz monitorowania kondycji usługi. Więcej informacji na [temat ról administratorów](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> W Microsoft Graph interfejsie API usługi Azure AD interfejs API programu Graph i Azure AD PowerShell ta rola jest identyfikowana jako "administrator usługi SharePoint". Jest to "Administrator programu SharePoint" w [Azure Portal](https://portal.azure.com).

### <a name="skype-for-business--lync-administratorlync-service-administrator-permissions"></a>[Administrator programu Skype dla firm/Lync](#lync-service-administrator-permissions)

Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Skype dla firm, gdy usługa jest obecna, a także do zarządzania atrybutami użytkownika specyficznymi dla programu Skype w Azure Active Directory. Ponadto ta rola zapewnia możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi oraz uzyskiwania dostępu do zespołów i centrum administracyjnego programu Skype dla firm. Konto musi być również licencjonowane dla zespołów lub nie może uruchamiać poleceń cmdlet programu PowerShell dla zespołów. Więcej informacji na [temat roli administratora programu Skype dla firm](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) i zespołów informacje o licencjonowaniu w usłudze [Skype dla firm i licencjonowaniu dodatku Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> W Microsoft Graph interfejsie API usługi Azure AD interfejs API programu Graph i Azure AD PowerShell ta rola jest identyfikowana jako "administrator usługi Lync". Jest to "Administrator programu Skype dla firm" w [Azure Portal](https://portal.azure.com/).

### <a name="teams-administratorteams-service-administrator-permissions"></a>[Administrator zespołów](#teams-service-administrator-permissions)

Użytkownicy w tej roli mogą zarządzać wszystkimi aspektami obciążeń programu Microsoft Teams za pośrednictwem Centrum administracyjnego Microsoft Teams & Skype dla firm i odpowiednich modułów programu PowerShell. Obejmuje to między innymi wszystkie narzędzia do zarządzania dotyczące telefonii, wiadomości, spotkań i samych zespołów. Ta rola dodatkowo umożliwia tworzenie wszystkich grup pakietu Office 365 i zarządzanie nimi, zarządzanie biletami pomocy technicznej i monitorowanie kondycji usługi.
> [!NOTE]
> W Microsoft Graph interfejsie API usługi Azure AD interfejs API programu Graph i Azure AD PowerShell ta rola jest identyfikowana jako "administrator usługi Team Service". Jest to "Administrator zespołów" w [Azure Portal](https://portal.azure.com).

### <a name="teams-communications-administratorteams-communications-administrator-permissions"></a>[Administratorzy zespołu ds. komunikacji](#teams-communications-administrator-permissions)

Użytkownicy w tej roli mogą zarządzać aspektami obciążeń programu Microsoft Teams związanymi z usługą telefon & Voice. Obejmuje to narzędzia do zarządzania do przypisywania numerów telefonów, zasad dotyczących głosu i spotkania oraz pełny dostęp do zestawu narzędzi do analizy wywołań.

### <a name="teams-communications-support-engineerteams-communications-support-engineer-permissions"></a>[Inżynierowie pomocy technicznej dla zespołów](#teams-communications-support-engineer-permissions)

Użytkownicy w tej roli mogą rozwiązywać problemy z komunikacją w usłudze Microsoft Teams & Skype dla firm przy użyciu narzędzi do rozwiązywania problemów z wywołaniem użytkownika w centrum administracyjnym usługi Microsoft Teams & Skype dla firm. Użytkownicy w tej roli mogą wyświetlać pełne informacje o rekordzie wywołania dla wszystkich uczestników. Ta rola nie ma dostępu do wyświetlania biletów pomocy technicznej, tworzenia ich ani zarządzania nimi.

### <a name="teams-communications-support-specialistteams-communications-support-specialist-permissions"></a>[Zespoły ds. pomocy technicznej](#teams-communications-support-specialist-permissions)

Użytkownicy w tej roli mogą rozwiązywać problemy z komunikacją w usłudze Microsoft Teams & Skype dla firm przy użyciu narzędzi do rozwiązywania problemów z wywołaniem użytkownika w centrum administracyjnym usługi Microsoft Teams & Skype dla firm. Użytkownicy w tej roli mogą tylko wyświetlać szczegóły użytkownika w wywołaniu dla określonego użytkownika, który wyszukał. Ta rola nie ma dostępu do wyświetlania biletów pomocy technicznej, tworzenia ich ani zarządzania nimi.

### <a name="user-administratoruser-administrator-permissions"></a>[Administrator użytkowników](#user-administrator-permissions)

Użytkownicy z tą rolą mogą tworzyć użytkowników i zarządzać wszystkimi aspektami użytkowników z pewnymi ograniczeniami (zobacz poniżej) i mogą aktualizować zasady wygasania haseł. Ponadto użytkownicy z tą rolą mogą tworzyć wszystkie grupy i zarządzać nimi. Ta rola obejmuje również możliwość tworzenia widoków użytkowników i zarządzania nimi, zarządzania biletami pomocy technicznej oraz monitorowania kondycji usługi. Administratorzy użytkowników nie mają uprawnień do zarządzania niektórymi właściwościami użytkowników w większości ról administratorów. Role, które są wyjątkami do tego ograniczenia, są wymienione w poniższej tabeli.

| | |
| --- | --- |
|Uprawnienia ogólne|<p>Tworzenie użytkowników i grup</p><p>Tworzenie widoków użytkowników i zarządzanie nimi</p><p>Zarządzanie biletami pomocy technicznej pakietu Office<p>Aktualizowanie zasad wygasania haseł|
|<p>Dla wszystkich użytkowników, w tym wszystkich administratorów</p>|<p>Zarządzanie licencjami</p><p>Zarządzanie wszystkimi właściwościami użytkowników z wyjątkiem głównej nazwy użytkownika</p>
|Tylko dla użytkowników, którzy nie są administratorami ani w ramach jednej z następujących ograniczonych ról administratora:<ul><li>Czytelnicy katalogów<li>Zapraszający gościa<li>Administrator pomocy technicznej<li>Czytelnik centrum wiadomości<li>Czytelnik raportów<li>Administrator użytkowników|<p>Usuń i Przywróć</p><p>Wyłącz i Włącz</p><p>Unieważnianie tokenów odświeżania</p><p>Zarządzanie wszystkimi właściwościami użytkowników, w tym główną nazwą użytkownika</p><p>Resetowanie hasła</p><p>Aktualizuj klucze urządzeń (FIDO)</p>|

> [!IMPORTANT]
> Użytkownicy z tą rolą mogą zmieniać hasła dla osób, które mogą mieć dostęp do poufnych lub prywatnych informacji lub konfiguracji krytycznej wewnątrz i na zewnątrz Azure Active Directory. Zmiana hasła użytkownika może oznaczać, że założono, że tożsamość i uprawnienia tego użytkownika. Na przykład:

* Rejestracja aplikacji i właściciele aplikacji przedsiębiorstwa, którzy mogą zarządzać poświadczeniami aplikacji, których są właścicielami. Aplikacje te mogą mieć uprawnienia uprzywilejowane w usłudze Azure AD, a w innym miejscu nie są przyznawane administratorom użytkowników. Za pomocą tej ścieżki administrator użytkownika może być w stanie założyć tożsamość właściciela aplikacji, a następnie ponownie założyć tożsamość aplikacji uprzywilejowanej przez zaktualizowanie poświadczeń dla aplikacji.
* Właściciele subskrypcji platformy Azure, którzy mogą mieć dostęp do poufnych lub prywatnych informacji lub konfiguracji krytycznej na platformie Azure.
* Grupa zabezpieczeń i właściciele grupy Office 365, którzy mogą zarządzać członkostwem w grupie. Te grupy mogą udzielić dostępu do poufnych lub prywatnych informacji lub konfiguracji krytycznej w usłudze Azure AD i w innym miejscu.
* Administratorzy w innych usługach poza usługą Azure AD, np. Exchange Online, Centrum zabezpieczeń i zgodności pakietu Office oraz systemy kadr.
* Użytkownicy niebędący administratorami, w tym członkowie kierownictwa, prawnik prawny i pracownicy działu kadr, którzy mogą mieć dostęp do poufnych lub prywatnych informacji.

## <a name="role-permissions"></a>Uprawnienia roli

W poniższych tabelach opisano określone uprawnienia w Azure Active Directory poszczególnych ról. Niektóre role mogą mieć dodatkowe uprawnienia w usługach firmy Microsoft poza programem Azure Active Directory.

### <a name="application-administrator-permissions"></a>Uprawnienia administratora aplikacji

Może tworzyć wszystkie aspekty rejestracji aplikacji i aplikacji przedsiębiorstwa oraz zarządzać nimi.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Application/appProxyAuthentication/Update | Aktualizowanie właściwości uwierzytelniania serwera proxy aplikacji w jednostkach usługi w Azure Active Directory. |
| Microsoft. Directory/Application/appProxyUrlSettings/Update | Zaktualizuj wewnętrzne i zewnętrzne adresy URL serwera proxy aplikacji w Azure Active Directory. |
| Microsoft. Directory/Applications/applicationProxy/Read | Zapoznaj się ze wszystkimi właściwościami serwera proxy aplikacji. |
| Microsoft. Directory/Applications/applicationProxy/Update | Zaktualizuj wszystkie właściwości serwera proxy aplikacji. |
| Microsoft. katalog/aplikacje/odbiorcy/aktualizacja | Aktualizowanie właściwości Applications. odbiorców w Azure Active Directory. |
| Microsoft. katalog/aplikacje/uwierzytelnianie/aktualizacja | Aktualizowanie właściwości Applications. Authentication w Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Update | Zaktualizuj podstawowe właściwości aplikacji w Azure Active Directory. |
| Microsoft. Directory/Applications/Create | Twórz aplikacje w Azure Active Directory. |
| Microsoft. Directory/Applications/Credentials/Update | Zaktualizuj Właściwość Applications. Credentials w Azure Active Directory. |
| Microsoft. katalog/aplikacje/usuwanie | Usuń aplikacje w Azure Active Directory. |
| Microsoft. Directory/Applications/Owners/Update | Aktualizowanie właściwości Applications. Owners w Azure Active Directory. |
| Microsoft. Directory/Applications/Permissions/Update | Aktualizowanie właściwości Applications. Permissions w Azure Active Directory. |
| Microsoft. Directory/Applications/policies/Update | Zaktualizuj Właściwość Applications. policies w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Create | Utwórz appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/odczyt | Odczytaj appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Update | Aktualizacja appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Delete | Usuń appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/auditLogs/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie auditLogs w Azure Active Directory. |
| Microsoft. Directory/connectorGroups/wszystko/odczyt | Odczytaj właściwości grupy łączników serwera proxy aplikacji w Azure Active Directory. |
| Microsoft. Directory/connectorGroups/wszystko/aktualizacja | Zaktualizuj wszystkie właściwości grupy łączników serwera proxy aplikacji w Azure Active Directory. |
| Microsoft. Directory/connectorGroups/Create | Utwórz grupy łączników serwera proxy aplikacji w Azure Active Directory. |
| Microsoft. Directory/connectorGroups/Delete | Usuń grupy łączników serwera proxy aplikacji w Azure Active Directory. |
| Microsoft. katalog/łączniki/wszystko/odczyt | Odczytaj wszystkie właściwości łącznika serwera proxy aplikacji w Azure Active Directory. |
| Microsoft. Directory/Connectors/Create | Utwórz łączniki serwera proxy aplikacji w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Read | Odczytaj Właściwość zasad. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Update | Zaktualizuj Właściwość policies. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Create | Utwórz zasady w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Delete | Usuń zasady w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/właściciele/odczyt | Odczytaj Właściwość zasad. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/właściciele/aktualizacja | Zaktualizuj Właściwość policies. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/policyAppliedTo/odczyt | Odczytaj Właściwość zasad. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Update | Zaktualizuj Właściwość serviceprincipals. appRoleAssignedTo w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignments/Update | Zaktualizuj Właściwość serviceprincipals. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/odbiorcy/aktualizacja | Aktualizacja właściwości serviceprincipals. odbiorca w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Authentication/Update | Aktualizacja właściwości serviceprincipals. Authentication w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Basic/Update | Zaktualizuj podstawowe właściwości obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Create | Tworzenie obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Credentials/Update | Aktualizacja właściwości serviceprincipals. Credentials w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Delete | Usuwanie obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Owners/Update | Aktualizacja właściwości serviceprincipals. Owners w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Permissions/Update | Aktualizacja właściwości serviceprincipals. Permissions w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/policies/Update | Aktualizacja właściwości serviceprincipals. policies w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="application-developer-permissions"></a>Uprawnienia deweloperów aplikacji

Można utworzyć rejestracje aplikacji niezależne od ustawienia "użytkownicy mogą rejestrować aplikacje".

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Applications/createAsOwner | Twórz aplikacje w Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a tworzony obiekt jest liczony według przydziału 250 obiektów utworzonych przez twórcę. |
| Microsoft. Directory/appRoleAssignments/createAsOwner | Utwórz appRoleAssignments w Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a tworzony obiekt jest liczony według przydziału 250 obiektów utworzonych przez twórcę. |
| Microsoft. Directory/elementu oauth2permissiongrants/createAsOwner | Utwórz elementu oauth2permissiongrants w Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a tworzony obiekt jest liczony według przydziału 250 obiektów utworzonych przez twórcę. |
| Microsoft. Directory/serviceprincipals/createAsOwner | Tworzenie obiektów serviceprincipals w Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a tworzony obiekt jest liczony według przydziału 250 obiektów utworzonych przez twórcę. |

### <a name="authentication-administrator-permissions"></a>Uprawnienia administratora uwierzytelniania

Zezwolenie na wyświetlanie, ustawianie i Resetowanie informacji o metodach uwierzytelniania dla dowolnego użytkownika niebędącego administratorem.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Unieważnienie wszystkich tokenów odświeżania użytkownika w Azure Active Directory. |
| Microsoft. Directory/Users/strongAuthentication/Update | Zaktualizuj właściwości silnego uwierzytelniania, takie jak informacje o poświadczeniach usługi MFA. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. Directory/Users/hasło/aktualizacja | Aktualizowanie haseł dla wszystkich użytkowników w organizacji pakietu Office 365. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |

### <a name="azure-devops-administrator-permissions"></a>Uprawnienia administratora usługi Azure DevOps

Może zarządzać zasadami i ustawieniami organizacji usługi Azure DevOps.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Opis roli](#azure-devops-administrator) powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. devOps/allEntities/allTasks | Odczytaj i skonfiguruj usługę Azure DevOps. |

### <a name="azure-information-protection-administrator-permissions"></a>Uprawnienia administratora Azure Information Protection

Może zarządzać wszystkimi aspektami usługi Azure Information Protection.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Opis roli](#) powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. informationProtection/allEntities/allTasks | Zarządzaj wszystkimi aspektami Azure Information Protection. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="b2c-user-flow-administrator-permissions"></a>B2C uprawnień administratora przepływu użytkownika

Twórz wszystkie aspekty przepływów użytkowników i zarządzaj nimi.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. AAD. B2C/userFlows/allTasks | Odczytywanie i Konfigurowanie przepływów użytkowników w Azure Active Directory B2C. |

### <a name="b2c-user-flow-attribute-administrator-permissions"></a>Uprawnienia administratora atrybutu przepływu użytkownika B2C

Utwórz schemat atrybutów dostępny dla wszystkich przepływów użytkowników i Zarządzaj nim.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. AAD. B2C/userAttributes/allTasks | Odczytywanie i Konfigurowanie atrybutów użytkownika w Azure Active Directory B2C. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>Uprawnienia administratora zestawu kluczy B2C IEF

Zarządzaj wpisami tajnymi Federacji i szyfrowania w strukturze środowiska tożsamości.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. AAD. B2C/trustFramework/zestawy danych/allTasks | Odczytywanie i konfigurowanie zestawów kluczy w Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator-permissions"></a>Uprawnienia administratora zasad B2C IEF

Tworzenie zasad zaufania platformy i zarządzanie nimi w strukturze środowiska tożsamości.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. AAD. B2C/trustFramework/policies/allTasks | Odczytaj i skonfiguruj zasady niestandardowe w Azure Active Directory B2C. |

### <a name="billing-administrator-permissions"></a>Uprawnienia administratora rozliczeń

Może wykonywać typowe zadania związane z rozliczeniami, takie jak aktualizowanie informacji o płatności.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Organization/Basic/Update | Aktualizuj podstawowe właściwości organizacji w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. Commerce. rozliczenia/allEntities/allTasks | Zarządzaj wszystkimi aspektami rozliczeń pakietu Office 365. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="cloud-application-administrator-permissions"></a>Uprawnienia administratora aplikacji w chmurze

Może tworzyć wszystkie aspekty rejestracji aplikacji i aplikacji przedsiębiorstwa oraz zarządzać nimi, z wyjątkiem serwera proxy aplikacji.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. katalog/aplikacje/odbiorcy/aktualizacja | Aktualizowanie właściwości Applications. odbiorców w Azure Active Directory. |
| Microsoft. katalog/aplikacje/uwierzytelnianie/aktualizacja | Aktualizowanie właściwości Applications. Authentication w Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Update | Zaktualizuj podstawowe właściwości aplikacji w Azure Active Directory. |
| Microsoft. Directory/Applications/Create | Twórz aplikacje w Azure Active Directory. |
| Microsoft. Directory/Applications/Credentials/Update | Zaktualizuj Właściwość Applications. Credentials w Azure Active Directory. |
| Microsoft. katalog/aplikacje/usuwanie | Usuń aplikacje w Azure Active Directory. |
| Microsoft. Directory/Applications/Owners/Update | Aktualizowanie właściwości Applications. Owners w Azure Active Directory. |
| Microsoft. Directory/Applications/Permissions/Update | Aktualizowanie właściwości Applications. Permissions w Azure Active Directory. |
| Microsoft. Directory/Applications/policies/Update | Zaktualizuj Właściwość Applications. policies w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Create | Utwórz appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Update | Aktualizacja appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Delete | Usuń appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/auditLogs/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie auditLogs w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Create | Utwórz zasady w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Read | Odczytaj Właściwość zasad. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Basic/Update | Zaktualizuj Właściwość policies. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/Delete | Usuń zasady w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/właściciele/odczyt | Odczytaj Właściwość zasad. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/właściciele/aktualizacja | Zaktualizuj Właściwość policies. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/policies/applicationConfiguration/policyAppliedTo/odczyt | Odczytaj Właściwość zasad. applicationConfiguration w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Update | Zaktualizuj Właściwość serviceprincipals. appRoleAssignedTo w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignments/Update | Zaktualizuj Właściwość serviceprincipals. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/odbiorcy/aktualizacja | Aktualizacja właściwości serviceprincipals. odbiorca w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Authentication/Update | Aktualizacja właściwości serviceprincipals. Authentication w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Basic/Update | Zaktualizuj podstawowe właściwości obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Create | Tworzenie obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Credentials/Update | Aktualizacja właściwości serviceprincipals. Credentials w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Delete | Usuwanie obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Owners/Update | Aktualizacja właściwości serviceprincipals. Owners w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Permissions/Update | Aktualizacja właściwości serviceprincipals. Permissions w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/policies/Update | Aktualizacja właściwości serviceprincipals. policies w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="cloud-device-administrator-permissions"></a>Uprawnienia administratora urządzenia w chmurze

Pełny dostęp do zarządzania urządzeniami w usłudze Azure AD.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie auditLogs w Azure Active Directory. |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Odczytaj Właściwość Devices. bitLockerRecoveryKeys w Azure Active Directory. |
| Microsoft. katalog/urządzenia/usuwanie | Usuń urządzenia w Azure Active Directory. |
| Microsoft. Directory/Devices/Disable | Wyłącz urządzenia w Azure Active Directory. |
| Microsoft. Directory/devices/Włącz | Włącz urządzenia w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |

### <a name="company-administrator-permissions"></a>Uprawnienia administratora firmy

Może zarządzać wszystkimi aspektami usługi Azure AD i usługami firmy Microsoft, które korzystają z tożsamości usługi Azure AD.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. AAD. cloudAppSecurity/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Microsoft. AAD. cloudAppSecurity. |
| Microsoft. Directory/administrativeUnits/allProperties/allTasks | Tworzenie i usuwanie administrativeUnits oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/Applications/allProperties/allTasks | Tworzenie i usuwanie aplikacji oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/allProperties/allTasks | Tworzenie i usuwanie appRoleAssignments oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/auditLogs/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie auditLogs w Azure Active Directory. |
| Microsoft. Directory/Contacts/allProperties/allTasks | Tworzenie i usuwanie kontaktów oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/kontrakty/allProperties/allTasks | Tworzenie i usuwanie kontraktów oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/Devices/allProperties/allTasks | Tworzenie i usuwanie urządzeń oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/directoryRoles/allProperties/allTasks | Tworzenie i usuwanie directoryRoles oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/directoryRoleTemplates/allProperties/allTasks | Tworzenie i usuwanie directoryRoleTemplates oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/domen/allProperties/allTasks | Tworzenie i usuwanie domen oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/Groups/allProperties/allTasks | Tworzenie i usuwanie grup oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/groupSettings/allProperties/allTasks | Tworzenie i usuwanie groupSettings oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/groupSettingTemplates/allProperties/allTasks | Tworzenie i usuwanie groupSettingTemplates oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/loginTenantBranding/allProperties/allTasks | Tworzenie i usuwanie loginTenantBranding oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/elementu oauth2permissiongrants/allProperties/allTasks | Tworzenie i usuwanie elementu oauth2permissiongrants oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/Organization/allProperties/allTasks | Tworzenie i usuwanie organizacji, a ponadto odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/policies/allProperties/allTasks | Tworzenie i usuwanie zasad oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/roleAssignments/allProperties/allTasks | Tworzenie i usuwanie roleAssignments oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/roleDefinitions/allProperties/allTasks | Tworzenie i usuwanie roleDefinitions oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/scopedRoleMemberships/allProperties/allTasks | Tworzenie i usuwanie scopedRoleMemberships oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/ServiceAction/activateService | Może wykonać akcję usługi Activateservice w Azure Active Directory |
| Microsoft. Directory/ServiceAction/disableDirectoryFeature | Może wykonać akcję usługi Disabledirectoryfeature w Azure Active Directory |
| Microsoft. Directory/ServiceAction/enableDirectoryFeature | Może wykonać akcję usługi Enabledirectoryfeature w Azure Active Directory |
| Microsoft. Directory/ServiceAction/getAvailableExtentionProperties | Może wykonać akcję usługi Getavailableextentionproperties w Azure Active Directory |
| Microsoft. Directory/serviceprincipals/allProperties/allTasks | Tworzenie i usuwanie obiektów serviceprincipals oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |
| Microsoft. Directory/subscribedSkus/allProperties/allTasks | Tworzenie i usuwanie subscribedSkus oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. Directory/Users/allProperties/allTasks | Tworzenie i usuwanie użytkowników oraz odczytywanie i aktualizowanie wszystkich właściwości w Azure Active Directory. |
| Microsoft. directorySync/allEntities/allTasks | Wykonaj wszystkie akcje w Azure AD Connect. |
| Microsoft. AAD. identityProtection/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Microsoft. AAD. identityProtection. |
| Microsoft. AAD. privilegedIdentityManagement/allEntities/odczyt | Odczytuj wszystkie zasoby w Microsoft. AAD. privilegedIdentityManagement. |
| Microsoft. Azure. advancedThreatProtection/allEntities/odczyt | Odczytaj wszystkie zasoby w Microsoft. Azure. advancedThreatProtection. |
| Microsoft. Azure. informationProtection/allEntities/allTasks | Zarządzaj wszystkimi aspektami Azure Information Protection. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. Commerce. rozliczenia/allEntities/allTasks | Zarządzaj wszystkimi aspektami rozliczeń pakietu Office 365. |
| Microsoft. Intune/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Intune. |
| Microsoft. Office 365. zgodnośćmanager/allEntities/allTasks | Zarządzanie wszystkimi aspektami programu Office 365 — Menedżer zgodności |
| Microsoft. Office 365. desktopAnalytics/allEntities/allTasks | Zarządzaj wszystkimi aspektami analizy pulpitu. |
| Microsoft. Office 365. Exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| Microsoft. 365. skrytka/allEntities/allTasks | Zarządzaj wszystkimi aspektami pakietu Office 365 Skrytka klienta |
| Microsoft. Office 365. messageCenter/messages/Read | Odczytuj wiadomości w Microsoft. 365. messageCenter. |
| Microsoft. Office 365. messageCenter/securityMessages/odczyt | Przeczytaj securityMessages w Microsoft. 365. messageCenter. |
| Microsoft. Office 365. protectionCenter/allEntities/allTasks | Zarządzaj wszystkimi aspektami pakietu Office 365 Protection Center. |
| Microsoft. Office 365. securityComplianceCenter/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Microsoft. 365. securityComplianceCenter. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office. SharePoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Microsoft. Office 365. SharePoint. |
| Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Skype dla firm Online. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. Office 365. usageReports/allEntities/odczyt | Przeczytaj raporty dotyczące użycia pakietu Office 365. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. powerApps. dynamics365/allEntities/allTasks | Zarządzaj wszystkimi aspektami programu Dynamics 365. |
| Microsoft. powerApps. powerBI/allEntities/allTasks | Zarządzaj wszystkimi aspektami Power BI. |
| Microsoft. Windows. defenderAdvancedThreatProtection/allEntities/odczyt | Odczytaj wszystkie zasoby w Microsoft. Windows. defenderAdvancedThreatProtection. |

### <a name="compliance-administrator-permissions"></a>Uprawnienia administratora zgodności

Może odczytywać i zarządzać konfiguracją zgodności i raportami w usłudze Azure AD i pakiecie Office 365.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. zgodnośćmanager/allEntities/allTasks | Zarządzanie wszystkimi aspektami programu Office 365 — Menedżer zgodności |
| Microsoft. Office 365. Exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office. SharePoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Microsoft. Office 365. SharePoint. |
| Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Skype dla firm Online. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="compliance-data-administrator-permissions"></a>Uprawnienia administratora danych zgodności

Tworzy i zarządza zawartością zgodności.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. AAD. cloudAppSecurity/allEntities/allTasks | Odczytaj i skonfiguruj Microsoft Cloud App Security. |
| Microsoft. Azure. informationProtection/allEntities/allTasks | Zarządzaj wszystkimi aspektami Azure Information Protection. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. zgodnośćmanager/allEntities/allTasks | Zarządzanie wszystkimi aspektami programu Office 365 — Menedżer zgodności |
| Microsoft. Office 365. Exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office. SharePoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Microsoft. Office 365. SharePoint. |
| Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Skype dla firm Online. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="conditional-access-administrator-permissions"></a>Uprawnienia administratora dostępu warunkowego

Może zarządzać możliwościami dostępu warunkowego.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/policies/conditionalAccess/Basic/Read | Odczytaj Właściwość zasad. conditionalAccess w Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/Basic/Update | Zaktualizuj Właściwość policies. conditionalAccess w Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/Create | Utwórz zasady w Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/Delete | Usuń zasady w Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/właściciele/odczyt | Odczytaj Właściwość zasad. conditionalAccess w Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/właściciele/aktualizacja | Zaktualizuj Właściwość policies. conditionalAccess w Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/policiesAppliedTo/odczyt | Odczytaj Właściwość zasad. conditionalAccess w Azure Active Directory. |
| Microsoft. Directory/policies/conditionalAccess/tenantDefault/Update | Zaktualizuj Właściwość policies. conditionalAccess w Azure Active Directory. |

### <a name="crm-service-administrator-permissions"></a>Uprawnienia administratora usługi CRM

Może zarządzać wszystkimi aspektami produktu Dynamics 365.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. powerApps. dynamics365/allEntities/allTasks | Zarządzaj wszystkimi aspektami programu Dynamics 365. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="customer-lockbox-access-approver-permissions"></a>Uprawnienia osoby zatwierdzającej dostęp skrytki klienta

Może zatwierdzić żądania pomocy technicznej firmy Microsoft w celu uzyskania dostępu do danych organizacji klienta.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. 365. skrytka/allEntities/allTasks | Zarządzaj wszystkimi aspektami pakietu Office 365 Skrytka klienta |

### <a name="desktop-analytics-administrator-permissions"></a>Uprawnienia administratora usługi Desktop Analytics

Może zarządzać narzędziami do analizy pulpitu i dostosowywania pakietu Office &ymi usługami zasad. W przypadku usługi Desktop Analytics obejmuje to możliwość wyświetlania spisu zasobów, tworzenia planów wdrażania, wyświetlania stanu wdrożenia i kondycji. Ta rola umożliwia użytkownikom zarządzanie zasadami pakietu Office w przypadku dostosowywania pakietu Office & Policy Service.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. desktopAnalytics/allEntities/allTasks | Zarządzaj wszystkimi aspektami analizy pulpitu. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="device-administrators-permissions"></a>Uprawnienia administratorów urządzeń

Użytkownicy przypisani do tej roli są dodawani do lokalnej grupy administratorów na urządzeniach dołączonych do usługi Azure AD.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/groupSettings/Basic/Read | Zapoznaj się z podstawowymi właściwościami groupSettings w Azure Active Directory. |
| Microsoft. Directory/groupSettingTemplates/Basic/Read | Zapoznaj się z podstawowymi właściwościami groupSettingTemplates w Azure Active Directory. |

### <a name="directory-readers-permissions"></a>Uprawnienia czytelnika katalogu
Może odczytywać podstawowe informacje o katalogu. Do udzielania dostępu do aplikacji nieprzeznaczonych dla użytkowników.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/administrativeUnits/Basic/Read | Zapoznaj się z podstawowymi właściwościami administrativeUnits w Azure Active Directory. |
| Microsoft. Directory/administrativeUnits/Members/odczyt | Przeczytaj Właściwość administrativeUnits. Members w Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Read | Zapoznaj się z podstawowymi właściwościami aplikacji w Azure Active Directory. |
| Microsoft. Directory/Applications/Owners/Read | Odczytywanie właściwości Applications. Owners w Azure Active Directory. |
| Microsoft. Directory/Applications/policies/Read | Odczytaj Właściwość Applications. policies w Azure Active Directory. |
| Microsoft. Directory/Contacts/Basic/Read | Zapoznaj się z podstawowymi właściwościami kontaktów w Azure Active Directory. |
| Microsoft. Directory/Contacts/memberOf/odczyt | Przeczytaj Właściwość Contacts. memberOf w Azure Active Directory. |
| Microsoft. Directory/kontrakts/Basic/Read | Zapoznaj się z podstawowymi właściwościami umów w Azure Active Directory. |
| Microsoft. Directory/Devices/podstawowa/odczytana | Zapoznaj się z podstawowymi właściwościami urządzeń w Azure Active Directory. |
| Microsoft. Directory/Devices/memberOf/odczyt | Odczytaj właściwości Devices. memberOf w Azure Active Directory. |
| Microsoft. Directory/Devices/registeredOwners/Read | Odczytaj Właściwość Devices. registeredOwners w Azure Active Directory. |
| Microsoft. Directory/Devices/registeredUsers/Read | Odczytaj Właściwość Devices. registeredUsers w Azure Active Directory. |
| Microsoft. Directory/directoryRoles/Basic/Read | Zapoznaj się z podstawowymi właściwościami directoryRoles w Azure Active Directory. |
| Microsoft. Directory/directoryRoles/eligibleMembers/odczyt | Odczytaj Właściwość directoryRoles. eligibleMembers w Azure Active Directory. |
| Microsoft. Directory/directoryRoles/Members/odczyt | Przeczytaj Właściwość directoryRoles. Members w Azure Active Directory. |
| Microsoft. Directory/domen/podstawowa/odczytana | Zapoznaj się z podstawowymi właściwościami domen w Azure Active Directory. |
| Microsoft. Directory/Groups/appRoleAssignments/Read | Odczytaj właściwości groups. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Read | Zapoznaj się z podstawowymi właściwościami grup w Azure Active Directory. |
| Microsoft. Directory/Groups/memberOf/Read | Odczytaj właściwości groups. memberOf w Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Read | Odczytywanie właściwości groups. Members w Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Read | Odczytywanie właściwości groups. Owners w Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Read | Odczytywanie właściwości groups. Settings w Azure Active Directory. |
| Microsoft. Directory/groupSettings/Basic/Read | Zapoznaj się z podstawowymi właściwościami groupSettings w Azure Active Directory. |
| Microsoft. Directory/groupSettingTemplates/Basic/Read | Zapoznaj się z podstawowymi właściwościami groupSettingTemplates w Azure Active Directory. |
| Microsoft. Directory/elementu oauth2permissiongrants/Basic/Read | Zapoznaj się z podstawowymi właściwościami elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/organizacja/podstawowa/odczytana | Odczytaj podstawowe właściwości organizacji w Azure Active Directory. |
| Microsoft. Directory/Organization/trustedCAsForPasswordlessAuth/Read | Przeczytaj Właściwość Organization. trustedCAsForPasswordlessAuth w Azure Active Directory. |
| Microsoft. Directory/roleAssignments/Basic/Read | Zapoznaj się z podstawowymi właściwościami roleAssignments w Azure Active Directory. |
| Microsoft. Directory/roleDefinitions/Basic/Read | Zapoznaj się z podstawowymi właściwościami roleDefinitions w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Read | Odczytywanie właściwości serviceprincipals. appRoleAssignedTo w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignments/Read | Odczytywanie właściwości serviceprincipals. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Basic/Read | Zapoznaj się z podstawowymi właściwościami obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/memberOf/Read | Odczytywanie właściwości serviceprincipals. memberOf w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/elementu oauth2permissiongrants/Basic/Read | Odczytywanie właściwości serviceprincipals. elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/ownedObjects/Read | Odczytywanie właściwości serviceprincipals. ownedObjects w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Owners/Read | Odczytywanie właściwości serviceprincipals. Owners w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/policies/Read | Odczytywanie właściwości serviceprincipals. policies w Azure Active Directory. |
| Microsoft. Directory/subscribedSkus/Basic/Read | Zapoznaj się z podstawowymi właściwościami subscribedSkus w Azure Active Directory. |
| Microsoft. Directory/Users/appRoleAssignments/Read | Odczytywanie właściwości users. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Users/Basic/Read | Zapoznaj się z podstawowymi właściwościami użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/directReports/Read | Odczytywanie właściwości users. directReports w Azure Active Directory. |
| Microsoft. Directory/Users/Manager/Odczytaj | Odczytywanie właściwości users. Manager w Azure Active Directory. |
| Microsoft. Directory/Users/memberOf/odczyt | Odczytaj właściwości users. memberOf w Azure Active Directory. |
| Microsoft. Directory/Users/elementu oauth2permissiongrants/Basic/Read | Odczytywanie właściwości users. elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/Users/ownedDevices/Read | Odczytywanie właściwości users. ownedDevices w Azure Active Directory. |
| Microsoft. Directory/Users/ownedObjects/Read | Odczytywanie właściwości users. ownedObjects w Azure Active Directory. |
| Microsoft. Directory/Users/registeredDevices/Read | Odczytywanie właściwości users. registeredDevices w Azure Active Directory. |

### <a name="directory-synchronization-accounts-permissions"></a>Uprawnienia kont synchronizacji katalogów

Używane przez usługę Azure AD Connect.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Organization/dirSync/Update | Zaktualizuj Właściwość Organization. dirSync w Azure Active Directory. |
| Microsoft. katalog/zasady/tworzenie | Utwórz zasady w Azure Active Directory. |
| Microsoft. Directory/zasady/usuwanie | Usuń zasady w Azure Active Directory. |
| Microsoft. katalog/zasady/podstawowe/odczyt | Zapoznaj się z podstawowymi właściwościami zasad w Azure Active Directory. |
| Microsoft. katalog/zasady/podstawowa/aktualizacja | Zaktualizuj podstawowe właściwości zasad w Azure Active Directory. |
| Microsoft. katalog/zasady/właściciele/odczyt | Odczytywanie właściwości policies. Owners w Azure Active Directory. |
| Microsoft. katalog/zasady/właściciele/aktualizacja | Aktualizowanie właściwości policies. Owners w Azure Active Directory. |
| Microsoft. Directory/policies/policiesAppliedTo/Read | Odczytaj Właściwość zasad. policiesAppliedTo w Azure Active Directory. |
| Microsoft. Directory/policies/tenantDefault/Update | Zaktualizuj Właściwość policies. tenantDefault w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Read | Odczytywanie właściwości serviceprincipals. appRoleAssignedTo w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Update | Zaktualizuj Właściwość serviceprincipals. appRoleAssignedTo w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignments/Read | Odczytywanie właściwości serviceprincipals. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignments/Update | Zaktualizuj Właściwość serviceprincipals. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/odbiorcy/aktualizacja | Aktualizacja właściwości serviceprincipals. odbiorca w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Authentication/Update | Aktualizacja właściwości serviceprincipals. Authentication w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Basic/Read | Zapoznaj się z podstawowymi właściwościami obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Basic/Update | Zaktualizuj podstawowe właściwości obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Create | Tworzenie obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Credentials/Update | Aktualizacja właściwości serviceprincipals. Credentials w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/memberOf/Read | Odczytywanie właściwości serviceprincipals. memberOf w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/elementu oauth2permissiongrants/Basic/Read | Odczytywanie właściwości serviceprincipals. elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Owners/Read | Odczytywanie właściwości serviceprincipals. Owners w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Owners/Update | Aktualizacja właściwości serviceprincipals. Owners w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/ownedObjects/Read | Odczytywanie właściwości serviceprincipals. ownedObjects w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Permissions/Update | Aktualizacja właściwości serviceprincipals. Permissions w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/policies/Read | Odczytywanie właściwości serviceprincipals. policies w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/policies/Update | Aktualizacja właściwości serviceprincipals. policies w Azure Active Directory. |
| Microsoft. directorySync/allEntities/allTasks | Wykonaj wszystkie akcje w Azure AD Connect. |

### <a name="directory-writers-permissions"></a>Uprawnienia autorów katalogów

Może odczytywać & pisać podstawowe informacje o katalogu. Do udzielania dostępu do aplikacji nieprzeznaczonych dla użytkowników.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Groups/Create | Utwórz grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/createAsOwner | Utwórz grupy w Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a tworzony obiekt jest liczony według przydziału 250 obiektów utworzonych przez twórcę. |
| Microsoft. Directory/Groups/appRoleAssignments/Update | Zaktualizuj Właściwość groups. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Update | Zaktualizuj podstawowe właściwości dla grup w Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Update | Aktualizowanie właściwości groups. Members w Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizacja właściwości groups. Owners w Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Update | Aktualizowanie właściwości groups. Settings w Azure Active Directory. |
| Microsoft. Directory/groupSettings/Basic/Update | Zaktualizuj podstawowe właściwości groupSettings w Azure Active Directory. |
| Microsoft. Directory/groupSettings/Create | Utwórz groupSettings w Azure Active Directory. |
| Microsoft. Directory/groupSettings/Delete | Usuń groupSettings w Azure Active Directory. |
| Microsoft. Directory/Users/appRoleAssignments/Update | Zaktualizuj Właściwość Users. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Users/assignLicense | Zarządzanie licencjami użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Basic/Update | Zaktualizuj podstawowe właściwości użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Unieważnienie wszystkich tokenów odświeżania użytkownika w Azure Active Directory. |
| Microsoft. Directory/Users/Manager/Update | Aktualizowanie właściwości users. Manager w Azure Active Directory. |
| Microsoft. Directory/Users/userPrincipalName/Update | Zaktualizuj Właściwość Users. userPrincipalName w Azure Active Directory. |

### <a name="exchange-service-administrator-permissions"></a>Uprawnienia administratora usługi Exchange

Może zarządzać wszystkimi aspektami produktu Exchange.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Groups/Unified/appRoleAssignments/Update | Aktualizacja właściwości groups. Unified w Azure Active Directory. |
| Microsoft. Directory/Groups/Unified/Basic/Update | Aktualizuj podstawowe właściwości grup pakietu Office 365. |
| Microsoft. Directory/Groups/Unified/Create | Utwórz grupy pakietu Office 365. |
| Microsoft. Directory/Groups/Unified/Delete | Usuń grupy pakietu Office 365. |
| Microsoft. Directory/Groups/Unified/Members/Update | Aktualizowanie członkostwa w grupach pakietu Office 365. |
| Microsoft. Directory/Groups/Unified/Owners/Update | Aktualizowanie własności grup pakietu Office 365. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. Exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="external-identity-provider-administrator-permissions"></a>Uprawnienia administratora zewnętrznego dostawcy tożsamości

Skonfiguruj dostawców tożsamości do użycia w Federacji bezpośredniej.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. AAD. B2C/skojarzeni/allTasks | Odczytaj i skonfiguruj dostawców tożsamości w Azure Active Directory B2C. |

### <a name="global-reader-permissions"></a>Globalne uprawnienia czytelnika
Może odczytywać wszystko, co Administrator globalny może, ale nie edytować niczego. 

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Opis roli](#global-reader) powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Commerce. rozliczenia/allEntities/odczyt   | Przeczytaj wszystkie aspekty rozliczeń pakietu Office 365. |
| Microsoft. Directory/administrativeUnits/Basic/Read    | Zapoznaj się z podstawowymi właściwościami administrativeUnits w Azure Active Directory. |
| Microsoft. Directory/administrativeUnits/Members/odczyt  | Przeczytaj Właściwość administrativeUnits. Members w Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Read   | Zapoznaj się z podstawowymi właściwościami aplikacji w Azure Active Directory. |
| Microsoft. Directory/Applications/Owners/Read  | Odczytywanie właściwości Applications. Owners w Azure Active Directory. |
| Microsoft. Directory/Applications/policies/Read    | Odczytaj Właściwość Applications. policies w Azure Active Directory. |
| Microsoft. Directory/Contacts/Basic/Read   | Zapoznaj się z podstawowymi właściwościami kontaktów w Azure Active Directory. |
| Microsoft. Directory/Contacts/memberOf/odczyt    | Przeczytaj Właściwość Contacts. memberOf w Azure Active Directory. |
| Microsoft. Directory/kontrakts/Basic/Read  | Zapoznaj się z podstawowymi właściwościami umów w Azure Active Directory. |
| Microsoft. Directory/Devices/podstawowa/odczytana    | Zapoznaj się z podstawowymi właściwościami urządzeń w Azure Active Directory. |
| Microsoft. Directory/Devices/memberOf/odczyt | Odczytaj właściwości Devices. memberOf w Azure Active Directory. |
| Microsoft. Directory/Devices/registeredOwners/Read | Odczytaj Właściwość Devices. registeredOwners w Azure Active Directory. |
| Microsoft. Directory/Devices/registeredUsers/Read  | Odczytaj Właściwość Devices. registeredUsers w Azure Active Directory. |
| Microsoft. Directory/directoryRoles/Basic/Read | Zapoznaj się z podstawowymi właściwościami directoryRoles w Azure Active Directory. |
| Microsoft. Directory/directoryRoles/eligibleMembers/odczyt   | Odczytaj Właściwość directoryRoles. eligibleMembers w Azure Active Directory. |
| Microsoft. Directory/directoryRoles/Members/odczyt   | Przeczytaj Właściwość directoryRoles. Members w Azure Active Directory. |
| Microsoft. Directory/domen/podstawowa/odczytana    | Zapoznaj się z podstawowymi właściwościami domen w Azure Active Directory. |
| Microsoft. Directory/Groups/appRoleAssignments/Read    | Odczytaj właściwości groups. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Read | Zapoznaj się z podstawowymi właściwościami grup w Azure Active Directory. |
| Microsoft. Directory/Groups/hiddenMembers/Read | Odczytaj właściwości groups. hiddenMembers w Azure Active Directory. |
| Microsoft. Directory/Groups/memberOf/Read  | Odczytaj właściwości groups. memberOf w Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Read   | Odczytywanie właściwości groups. Members w Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Read    | Odczytywanie właściwości groups. Owners w Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Read  | Odczytywanie właściwości groups. Settings w Azure Active Directory. |
| Microsoft. Directory/groupSettings/Basic/Read  | Zapoznaj się z podstawowymi właściwościami groupSettings w Azure Active Directory. |
| Microsoft. Directory/groupSettingTemplates/Basic/Read  | Zapoznaj się z podstawowymi właściwościami groupSettingTemplates w Azure Active Directory. |
| Microsoft. Directory/elementu oauth2permissiongrants/Basic/Read | Zapoznaj się z podstawowymi właściwościami elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/organizacja/podstawowa/odczytana   | Odczytaj podstawowe właściwości organizacji w Azure Active Directory. |
| Microsoft. Directory/Organization/trustedCAsForPasswordlessAuth/Read   | Przeczytaj Właściwość Organization. trustedCAsForPasswordlessAuth w Azure Active Directory. |
| Microsoft. katalog/zasady/standardowe/odczyt    | Odczytaj standardowe zasady w Azure Active Directory. |
| Microsoft. Directory/roleAssignments/Basic/Read    | Zapoznaj się z podstawowymi właściwościami roleAssignments w Azure Active Directory. |
| Microsoft. Directory/roleDefinitions/Basic/Read    | Zapoznaj się z podstawowymi właściwościami roleDefinitions w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignedTo/Read  | Odczytywanie właściwości serviceprincipals. appRoleAssignedTo w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/appRoleAssignments/Read | Odczytywanie właściwości serviceprincipals. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Basic/Read  | Zapoznaj się z podstawowymi właściwościami obiektów serviceprincipals w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/memberOf/Read   | Odczytywanie właściwości serviceprincipals. memberOf w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/elementu oauth2permissiongrants/Basic/Read   | Odczytywanie właściwości serviceprincipals. elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/ownedObjects/Read   | Odczytywanie właściwości serviceprincipals. ownedObjects w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/Owners/Read | Odczytywanie właściwości serviceprincipals. Owners w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/policies/Read   | Odczytywanie właściwości serviceprincipals. policies w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt  | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |
| Microsoft. Directory/subscribedSkus/Basic/Read | Zapoznaj się z podstawowymi właściwościami subscribedSkus w Azure Active Directory. |
| Microsoft. Directory/Users/appRoleAssignments/Read | Odczytywanie właściwości users. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Users/Basic/Read  | Zapoznaj się z podstawowymi właściwościami użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/directReports/Read  | Odczytywanie właściwości users. directReports w Azure Active Directory. |
| Microsoft. Directory/Users/Manager/Odczytaj    | Odczytywanie właściwości users. Manager w Azure Active Directory. |
| Microsoft. Directory/Users/memberOf/odczyt   | Odczytaj właściwości users. memberOf w Azure Active Directory. |
| Microsoft. Directory/Users/elementu oauth2permissiongrants/Basic/Read   | Odczytywanie właściwości users. elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/Users/ownedDevices/Read   | Odczytywanie właściwości users. ownedDevices w Azure Active Directory. |
| Microsoft. Directory/Users/ownedObjects/Read   | Odczytywanie właściwości users. ownedObjects w Azure Active Directory. |
| Microsoft. Directory/Users/registeredDevices/Read  | Odczytywanie właściwości users. registeredDevices w Azure Active Directory. |
| Microsoft. Directory/Users/strongAuthentication/Read   | Odczytywanie właściwości silnego uwierzytelniania, takich jak informacje o poświadczeniach usługi MFA. |
| Microsoft. Office 365. Exchange/allEntities/Read | Przeczytaj wszystkie aspekty usługi Exchange Online. |
| Microsoft. Office 365. messageCenter/messages/Read   | Odczytuj wiadomości w Microsoft. 365. messageCenter. |
| Microsoft. Office 365. messageCenter/securityMessages/odczyt   | Przeczytaj securityMessages w Microsoft. 365. messageCenter. |
| Microsoft. Office 365. protectionCenter/allEntities/odczyt | Przeczytaj wszystkie aspekty Centrum ochrony pakietu Office 365. |
| Microsoft. Office 365. securityComplianceCenter/allEntities/odczyt | Odczytaj wszystkie standardowe właściwości w Microsoft. 365. securityComplianceCenter. |
| Microsoft. Office 365. usageReports/allEntities/odczyt | Przeczytaj raporty dotyczące użycia pakietu Office 365. |
| Microsoft. 365. webports/allEntities/Standard/Read   | Odczytywanie właściwości standardowych wszystkich zasobów w Microsoft. 365. webport. |

### <a name="guest-inviter-permissions"></a>Uprawnienia osoby zapraszające gościa
Może zapraszać użytkowników-Gości niezależnie od ustawienia "członkowie mogą zapraszać Gości".

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Users/appRoleAssignments/Read | Odczytywanie właściwości users. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Users/Basic/Read | Zapoznaj się z podstawowymi właściwościami użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/directReports/Read | Odczytywanie właściwości users. directReports w Azure Active Directory. |
| Microsoft. Directory/Users/inviteGuest | Zapraszanie użytkowników-Gości w Azure Active Directory. |
| Microsoft. Directory/Users/Manager/Odczytaj | Odczytywanie właściwości users. Manager w Azure Active Directory. |
| Microsoft. Directory/Users/memberOf/odczyt | Odczytaj właściwości users. memberOf w Azure Active Directory. |
| Microsoft. Directory/Users/elementu oauth2permissiongrants/Basic/Read | Odczytywanie właściwości users. elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/Users/ownedDevices/Read | Odczytywanie właściwości users. ownedDevices w Azure Active Directory. |
| Microsoft. Directory/Users/ownedObjects/Read | Odczytywanie właściwości users. ownedObjects w Azure Active Directory. |
| Microsoft. Directory/Users/registeredDevices/Read | Odczytywanie właściwości users. registeredDevices w Azure Active Directory. |

### <a name="helpdesk-administrator-permissions"></a>Uprawnienia administratora pomocy technicznej

Można resetować hasła dla administratorów nie będących administratorami i pomocą techniczną.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Odczytaj Właściwość Devices. bitLockerRecoveryKeys w Azure Active Directory. |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Unieważnienie wszystkich tokenów odświeżania użytkownika w Azure Active Directory. |
| Microsoft. Directory/Users/hasło/aktualizacja | Aktualizowanie haseł dla wszystkich użytkowników w Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="intune-service-administrator-permissions"></a>Uprawnienia administratora usługi Intune

Może zarządzać wszystkimi aspektami produktu usługi Intune.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Contacts/Basic/Update | Aktualizowanie podstawowych właściwości kontaktów w Azure Active Directory. |
| Microsoft. Directory/Contacts/Create | Utwórz kontakty w Azure Active Directory. |
| Microsoft. Directory/kontakty/usuwanie | Usuń kontakty w Azure Active Directory. |
| Microsoft. Directory/Devices/podstawowa/aktualizacja | Zaktualizuj podstawowe właściwości na urządzeniach w Azure Active Directory. |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Odczytaj Właściwość Devices. bitLockerRecoveryKeys w Azure Active Directory. |
| Microsoft. Directory/Devices/Create | Utwórz urządzenia w Azure Active Directory. |
| Microsoft. katalog/urządzenia/usuwanie | Usuń urządzenia w Azure Active Directory. |
| Microsoft. Directory/Devices/registeredOwners/Update | Zaktualizuj Właściwość Devices. registeredOwners w Azure Active Directory. |
| Microsoft. Directory/Devices/registeredUsers/Update | Zaktualizuj Właściwość Devices. registeredUsers w Azure Active Directory. |
| Microsoft. Directory/Groups/appRoleAssignments/Update | Zaktualizuj Właściwość groups. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Update | Zaktualizuj podstawowe właściwości dla grup w Azure Active Directory. |
| Microsoft. Directory/Groups/Create | Utwórz grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/createAsOwner | Utwórz grupy w Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a tworzony obiekt jest liczony według przydziału 250 obiektów utworzonych przez twórcę. |
| Microsoft. Directory/Groups/Delete | Usuń grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/hiddenMembers/Read | Odczytaj właściwości groups. hiddenMembers w Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Update | Aktualizowanie właściwości groups. Members w Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizacja właściwości groups. Owners w Azure Active Directory. |
| Microsoft. Directory/Groups/Restore | Przywróć grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Update | Aktualizowanie właściwości groups. Settings w Azure Active Directory. |
| Microsoft. Directory/Users/appRoleAssignments/Update | Zaktualizuj Właściwość Users. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Users/Basic/Update | Zaktualizuj podstawowe właściwości użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Manager/Update | Aktualizowanie właściwości users. Manager w Azure Active Directory. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. Intune/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Intune. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="kaizala-administrator-permissions"></a>Uprawnienia administratora usługi kaizala

Może zarządzać ustawieniami programu Microsoft usługi kaizala.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Przeczytaj centrum administracyjne pakietu Office 365. |

### <a name="license-administrator-permissions"></a>Uprawnienia administratora licencji

Może zarządzać licencjami produktów dla użytkowników i grup.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Users/assignLicense | Zarządzanie licencjami użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/usageLocation/Update | Zaktualizuj Właściwość Users. usageLocation w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |

### <a name="lync-service-administrator-permissions"></a>Uprawnienia administratora usługi Lync

Może zarządzać wszystkimi aspektami produktu Skype dla firm.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Skype dla firm Online. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="message-center-privacy-reader-permissions"></a>Uprawnienia czytelnika do ochrony prywatności centrum wiadomości

Może odczytywać wpisy centrum wiadomości, komunikaty dotyczące prywatności danych, grupy, domeny i subskrypcje.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. messageCenter/messages/Read | Odczytuj wiadomości w Microsoft. 365. messageCenter. |
| Microsoft. Office 365. messageCenter/securityMessages/odczyt | Przeczytaj securityMessages w Microsoft. 365. messageCenter. |

### <a name="message-center-reader-permissions"></a>Uprawnienia czytelnika centrum wiadomości
Może odczytywać wiadomości i aktualizacje dla swojej organizacji tylko w centrum wiadomości pakietu Office 365. 

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. messageCenter/messages/Read | Odczytuj wiadomości w Microsoft. 365. messageCenter. |

### <a name="partner-tier1-support-permissions"></a>Uprawnienia do obsługi pomoc partnerów

Nie używaj — nie jest przeznaczony do użytku ogólnego.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Contacts/Basic/Update | Aktualizowanie podstawowych właściwości kontaktów w Azure Active Directory. |
| Microsoft. Directory/Contacts/Create | Utwórz kontakty w Azure Active Directory. |
| Microsoft. Directory/kontakty/usuwanie | Usuń kontakty w Azure Active Directory. |
| Microsoft. Directory/Groups/Create | Utwórz grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/createAsOwner | Utwórz grupy w Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a tworzony obiekt jest liczony według przydziału 250 obiektów utworzonych przez twórcę. |
| Microsoft. Directory/Groups/Members/Update | Aktualizowanie właściwości groups. Members w Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizacja właściwości groups. Owners w Azure Active Directory. |
| Microsoft. Directory/Users/appRoleAssignments/Update | Zaktualizuj Właściwość Users. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Users/assignLicense | Zarządzanie licencjami użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Basic/Update | Zaktualizuj podstawowe właściwości użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Delete | Usuń użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Unieważnienie wszystkich tokenów odświeżania użytkownika w Azure Active Directory. |
| Microsoft. Directory/Users/Manager/Update | Aktualizowanie właściwości users. Manager w Azure Active Directory. |
| Microsoft. Directory/Users/hasło/aktualizacja | Aktualizowanie haseł dla wszystkich użytkowników w Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| Microsoft. Directory/Users/Restore | Przywróć usuniętych użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/userPrincipalName/Update | Zaktualizuj Właściwość Users. userPrincipalName w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="partner-tier2-support-permissions"></a>Uprawnienia do obsługi SVR partnerów

Nie używaj — nie jest przeznaczony do użytku ogólnego.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Contacts/Basic/Update | Aktualizowanie podstawowych właściwości kontaktów w Azure Active Directory. |
| Microsoft. Directory/Contacts/Create | Utwórz kontakty w Azure Active Directory. |
| Microsoft. Directory/kontakty/usuwanie | Usuń kontakty w Azure Active Directory. |
| Microsoft. Directory/domen/allTasks | Tworzenie i usuwanie domen oraz odczytywanie i aktualizowanie standardowych właściwości w Azure Active Directory. |
| Microsoft. Directory/Groups/Create | Utwórz grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/Delete | Usuń grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Update | Aktualizowanie właściwości groups. Members w Azure Active Directory. |
| Microsoft. Directory/Groups/Restore | Przywróć grupy w Azure Active Directory. |
| Microsoft. Directory/Organization/Basic/Update | Aktualizuj podstawowe właściwości organizacji w Azure Active Directory. |
| Microsoft. Directory/Users/appRoleAssignments/Update | Zaktualizuj Właściwość Users. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Users/assignLicense | Zarządzanie licencjami użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Basic/Update | Zaktualizuj podstawowe właściwości użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Delete | Usuń użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Unieważnienie wszystkich tokenów odświeżania użytkownika w Azure Active Directory. |
| Microsoft. Directory/Users/Manager/Update | Aktualizowanie właściwości users. Manager w Azure Active Directory. |
| Microsoft. Directory/Users/hasło/aktualizacja | Aktualizowanie haseł dla wszystkich użytkowników w Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| Microsoft. Directory/Users/Restore | Przywróć usuniętych użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/userPrincipalName/Update | Zaktualizuj Właściwość Users. userPrincipalName w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="password-administrator-permissions"></a>Uprawnienia administratora hasła

Można resetować hasła dla administratorów nie będących administratorami i haseł.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Users/hasło/aktualizacja | Aktualizowanie haseł dla wszystkich użytkowników w Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="power-bi-service-administrator-permissions"></a>Uprawnienia administratora usługi Power BI

Może zarządzać wszystkimi aspektami produktu Power BI.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. powerApps. powerBI/allEntities/allTasks | Zarządzaj wszystkimi aspektami Power BI. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="privileged-authentication-administrator-permissions"></a>Uprawnienia administratora uwierzytelniania uprzywilejowanego

Zezwolenie na wyświetlanie, ustawianie i Resetowanie informacji o metodach uwierzytelniania dla dowolnego użytkownika (administratora lub nie administratora).

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Unieważnienie wszystkich tokenów odświeżania użytkownika w Azure Active Directory. |
| Microsoft. Directory/Users/strongAuthentication/Update | Zaktualizuj właściwości silnego uwierzytelniania, takie jak informacje o poświadczeniach usługi MFA. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. Directory/Users/hasło/aktualizacja | Aktualizowanie haseł dla wszystkich użytkowników w organizacji pakietu Office 365. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |

### <a name="privileged-role-administrator-permissions"></a>Uprawnienia administratora ról uprzywilejowanych

Może zarządzać przypisaniami ról w usłudze Azure AD i wszystkimi aspektami Privileged Identity Management.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. AAD. privilegedIdentityManagement/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Microsoft. AAD. privilegedIdentityManagement. |
| Microsoft. Directory/serviceprincipals/appRoleAssignedTo/allTasks | Odczytaj i skonfiguruj Właściwość serviceprincipals. appRoleAssignedTo w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/elementu oauth2permissiongrants/allTasks | Odczytaj i skonfiguruj Właściwość serviceprincipals. elementu oauth2permissiongrants w Azure Active Directory. |
| Microsoft. Directory/administrativeUnits/allProperties/allTasks | Tworzenie jednostek administracyjnych (w tym członków) i zarządzanie nimi |
| Microsoft. Directory/roleAssignments/allProperties/allTasks | Tworzenie przypisań ról i zarządzanie nimi. |
| Microsoft. Directory/roleDefinitions/allProperties/allTasks | Utwórz definicje ról i zarządzaj nimi. |

### <a name="reports-reader-permissions"></a>Uprawnienia czytelnika raportów

Może odczytywać raporty logowania i inspekcji.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie auditLogs w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Office 365. usageReports/allEntities/odczyt | Przeczytaj raporty dotyczące użycia pakietu Office 365. |

### <a name="search-administrator-permissions"></a>Wyszukaj uprawnienia administratora

Może tworzyć wszystkie aspekty ustawień wyszukiwania firmy Microsoft i zarządzać nimi.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Office 365. messageCenter/messages/Read | Odczytuj wiadomości w Microsoft. 365. messageCenter. |
| Microsoft. Office 365. Search/allEntities/allProperties/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie wszystkich właściwości w Microsoft. Office 365. Search. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. Office 365. usageReports/allEntities/odczyt | Przeczytaj raporty dotyczące użycia pakietu Office 365. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |

### <a name="search-editor-permissions"></a>Uprawnienia edytora wyszukiwania

Może tworzyć i zarządzać zawartością redakcyjną, taką jak zakładki, Q i AS, lokalizacje, floorplan.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Office 365. messageCenter/messages/Read | Odczytuj wiadomości w Microsoft. 365. messageCenter. |
| Microsoft. 365. Search/Content/allProperties/allTasks | Tworzenie i usuwanie zawartości oraz odczytywanie i aktualizowanie wszystkich właściwości w Microsoft. Office 365. Search. |
| Microsoft. Office 365. usageReports/allEntities/odczyt | Przeczytaj raporty dotyczące użycia pakietu Office 365. |

### <a name="security-administrator-permissions"></a>Uprawnienia administratora zabezpieczeń

Może odczytywać informacje o zabezpieczeniach i raporty oraz zarządzać konfiguracją w usługach Azure AD i Office 365.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Applications/policies/Update | Zaktualizuj Właściwość Applications. policies w Azure Active Directory. |
| Microsoft. Directory/auditLogs/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie auditLogs w Azure Active Directory. |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Odczytaj Właściwość Devices. bitLockerRecoveryKeys w Azure Active Directory. |
| Microsoft. katalog/zasady/podstawowa/aktualizacja | Zaktualizuj podstawowe właściwości zasad w Azure Active Directory. |
| Microsoft. katalog/zasady/tworzenie | Utwórz zasady w Azure Active Directory. |
| Microsoft. Directory/zasady/usuwanie | Usuń zasady w Azure Active Directory. |
| Microsoft. katalog/zasady/właściciele/aktualizacja | Aktualizowanie właściwości policies. Owners w Azure Active Directory. |
| Microsoft. Directory/policies/tenantDefault/Update | Zaktualizuj Właściwość policies. tenantDefault w Azure Active Directory. |
| Microsoft. Directory/serviceprincipals/policies/Update | Aktualizacja właściwości serviceprincipals. policies w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |
| Microsoft. AAD. identityProtection/allEntities/odczyt | Odczytuj wszystkie zasoby w Microsoft. AAD. identityProtection. |
| Microsoft. AAD. identityProtection/allEntities/Update | Zaktualizuj wszystkie zasoby w usłudze Microsoft. AAD. identityProtection. |
| Microsoft. AAD. privilegedIdentityManagement/allEntities/odczyt | Odczytuj wszystkie zasoby w Microsoft. AAD. privilegedIdentityManagement. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. protectionCenter/allEntities/odczyt | Przeczytaj wszystkie aspekty Centrum ochrony pakietu Office 365. |
| Microsoft. Office 365. protectionCenter/allEntities/Update | Zaktualizuj wszystkie zasoby w Microsoft. 365. protectionCenter. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |

### <a name="security-operator-permissions"></a>Uprawnienia operatora zabezpieczeń

Tworzy i zarządza zdarzeniami zabezpieczeń.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. AAD. cloudAppSecurity/allEntities/allTasks | Odczytaj i skonfiguruj Microsoft Cloud App Security. |
| Microsoft. AAD. identityProtection/allEntities/odczyt | Odczytuj wszystkie zasoby w Microsoft. AAD. identityProtection. |
| Microsoft. AAD. privilegedIdentityManagement/allEntities/odczyt | Odczytuj wszystkie zasoby w Microsoft. AAD. privilegedIdentityManagement. |
| Microsoft. Azure. advancedThreatProtection/allEntities/odczyt | Przeczytaj i skonfiguruj zaawansowaną ochronę przed zagrożeniami w usłudze Azure AD. |
| Microsoft. Intune/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Intune. |
| Microsoft. Office 365. securityComplianceCenter/allEntities/allTasks | Odczytaj i skonfiguruj Centrum zabezpieczeń i zgodności. |
| Microsoft. Office 365. usageReports/allEntities/odczyt | Przeczytaj raporty dotyczące użycia pakietu Office 365. |
| Microsoft. Windows. defenderAdvancedThreatProtection/allEntities/odczyt | Odczytywanie i Konfigurowanie zaawansowanej ochrony przed zagrożeniami w usłudze Windows Defender. |

### <a name="security-reader-permissions"></a>Uprawnienia czytelnika zabezpieczeń

Może odczytywać informacje o zabezpieczeniach i raporty w usługach Azure AD i Office 365.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie auditLogs w Azure Active Directory. |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Odczytaj Właściwość Devices. bitLockerRecoveryKeys w Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/odczyt | Odczytaj wszystkie właściwości (w tym właściwości uprzywilejowane) w witrynie signInReports w Azure Active Directory. |
| Microsoft. AAD. identityProtection/allEntities/odczyt | Odczytuj wszystkie zasoby w Microsoft. AAD. identityProtection. |
| Microsoft. AAD. privilegedIdentityManagement/allEntities/odczyt | Odczytuj wszystkie zasoby w Microsoft. AAD. privilegedIdentityManagement. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. protectionCenter/allEntities/odczyt | Przeczytaj wszystkie aspekty Centrum ochrony pakietu Office 365. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |

### <a name="service-support-administrator-permissions"></a>Uprawnienia administratora usługi Service Support

Może odczytywać informacje o kondycji usługi i zarządzać biletami pomocy technicznej.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="sharepoint-service-administrator-permissions"></a>Uprawnienia administratora usługi programu SharePoint

Może zarządzać wszystkimi aspektami usługi programu SharePoint.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Groups/Unified/appRoleAssignments/Update | Aktualizacja właściwości groups. Unified w Azure Active Directory. |
| Microsoft. Directory/Groups/Unified/Basic/Update | Aktualizuj podstawowe właściwości grup pakietu Office 365. |
| Microsoft. Directory/Groups/Unified/Create | Utwórz grupy pakietu Office 365. |
| Microsoft. Directory/Groups/Unified/Delete | Usuń grupy pakietu Office 365. |
| Microsoft. Directory/Groups/Unified/Members/Update | Aktualizowanie członkostwa w grupach pakietu Office 365. |
| Microsoft. Directory/Groups/Unified/Owners/Update | Aktualizowanie własności grup pakietu Office 365. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office. SharePoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów oraz odczytywanie i aktualizowanie standardowych właściwości w Microsoft. Office 365. SharePoint. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

### <a name="teams-communications-administrator-permissions"></a>Zespoły — uprawnienia administratora

Może zarządzać funkcjami wywoływania i spotkań w usłudze Microsoft Teams.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. Office 365. usageReports/allEntities/odczyt | Przeczytaj raporty dotyczące użycia pakietu Office 365. |

### <a name="teams-communications-support-engineer-permissions"></a>Zespoły — uprawnienia inżynierów pomocy technicznej

Program może rozwiązywać problemy z komunikacją w zespołach przy użyciu zaawansowanych narzędzi.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |

### <a name="teams-communications-support-specialist-permissions"></a>Zespoły komunikacyjne obsługują uprawnienia specjalistyczne

Program może rozwiązywać problemy z komunikacją w zespołach przy użyciu podstawowych narzędzi.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |

### <a name="teams-service-administrator-permissions"></a>Uprawnienia administratora usługi Teams

Może zarządzać usługą Microsoft Teams.

> [!NOTE]
> Ta rola ma dodatkowe uprawnienia poza Azure Active Directory. Aby uzyskać więcej informacji, zobacz opis roli powyżej.
>
>

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/Groups/hiddenMembers/Read | Odczytaj właściwości groups. hiddenMembers w Azure Active Directory. |
| Microsoft. Directory/Groups/Unified/appRoleAssignments/Update | Aktualizacja właściwości groups. Unified w Azure Active Directory. |
| Microsoft. Directory/Groups/Unified/Basic/Update | Aktualizuj podstawowe właściwości grup pakietu Office 365. |
| Microsoft. Directory/Groups/Unified/Create | Utwórz grupy pakietu Office 365. |
| Microsoft. Directory/Groups/Unified/Delete | Usuń grupy pakietu Office 365. |
| Microsoft. Directory/Groups/Unified/Members/Update | Aktualizowanie członkostwa w grupach pakietu Office 365. |
| Microsoft. Directory/Groups/Unified/Owners/Update | Aktualizowanie własności grup pakietu Office 365. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |
| Microsoft. Office 365. usageReports/allEntities/odczyt | Przeczytaj raporty dotyczące użycia pakietu Office 365. |

### <a name="user-administrator-permissions"></a>Uprawnienia administratora użytkownika
Może zarządzać wszystkimi aspektami użytkowników i grup, w tym resetowania haseł dla ograniczonych administratorów.

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft. Directory/appRoleAssignments/Create | Utwórz appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Delete | Usuń appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/appRoleAssignments/Update | Aktualizacja appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Contacts/Basic/Update | Aktualizowanie podstawowych właściwości kontaktów w Azure Active Directory. |
| Microsoft. Directory/Contacts/Create | Utwórz kontakty w Azure Active Directory. |
| Microsoft. Directory/kontakty/usuwanie | Usuń kontakty w Azure Active Directory. |
| Microsoft. Directory/Groups/appRoleAssignments/Update | Zaktualizuj Właściwość groups. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Update | Zaktualizuj podstawowe właściwości dla grup w Azure Active Directory. |
| Microsoft. Directory/Groups/Create | Utwórz grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/createAsOwner | Utwórz grupy w Azure Active Directory. Twórca jest dodawany jako pierwszy właściciel, a tworzony obiekt jest liczony według przydziału 250 obiektów utworzonych przez twórcę. |
| Microsoft. Directory/Groups/Delete | Usuń grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/hiddenMembers/Read | Odczytaj właściwości groups. hiddenMembers w Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Update | Aktualizowanie właściwości groups. Members w Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizacja właściwości groups. Owners w Azure Active Directory. |
| Microsoft. Directory/Groups/Restore | Przywróć grupy w Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Update | Aktualizowanie właściwości groups. Settings w Azure Active Directory. |
| Microsoft. Directory/Users/appRoleAssignments/Update | Zaktualizuj Właściwość Users. appRoleAssignments w Azure Active Directory. |
| Microsoft. Directory/Users/assignLicense | Zarządzanie licencjami użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Basic/Update | Zaktualizuj podstawowe właściwości użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Create | Utwórz użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/Delete | Usuń użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/invalidateAllRefreshTokens | Unieważnienie wszystkich tokenów odświeżania użytkownika w Azure Active Directory. |
| Microsoft. Directory/Users/Manager/Update | Aktualizowanie właściwości users. Manager w Azure Active Directory. |
| Microsoft. Directory/Users/hasło/aktualizacja | Aktualizowanie haseł dla wszystkich użytkowników w Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| Microsoft. Directory/Users/Restore | Przywróć usuniętych użytkowników w Azure Active Directory. |
| Microsoft. Directory/Users/userPrincipalName/Update | Zaktualizuj Właściwość Users. userPrincipalName w Azure Active Directory. |
| Microsoft. Azure. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Azure Service Health. |
| Microsoft. Azure. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej systemu Azure i zarządzaj nimi. |
| Microsoft. 365. webports/allEntities/Basic/Read | Zapoznaj się z podstawowymi właściwościami wszystkich zasobów w Microsoft. 365. webport. |
| Microsoft. Office 365. servicehealth/allEntities/allTasks | Odczytaj i skonfiguruj Service Health pakietu Office 365. |
| Microsoft. Office 365. supportTickets/allEntities/allTasks | Twórz bilety pomocy technicznej pakietu Office 365 i zarządzaj nimi. |

## <a name="role-template-ids"></a>Identyfikatory szablonu roli

Identyfikatory szablonów ról są używane głównie przez użytkowników interfejs API programu Graph lub PowerShell.

Nazwa wyświetlana wykresu | Nazwa wyświetlana Azure Portal | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Administrator aplikacji | Administrator aplikacji | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Deweloper aplikacji | Deweloper aplikacji | CF1C38E5-3621-4004-A7CB-879624DCED7C
Administrator uwierzytelniania | Administrator uwierzytelniania | c4e39bd9-1100-46d3-8c65-fb160da0071f
Administrator usługi Azure DevOps | Administrator usługi Azure DevOps | e3973bdf-4987-49ae-837a-ba8e231c7286
Azure Information Protection administrator | Azure Information Protection administrator | 7495fdc4-34c4-4d15-a289-98788ce399fd
Administrator przepływu użytkownika B2C | Administrator przepływu użytkownika B2C | 6e591065-9bad-43ed-90f3-e9424366d2f0
administrator atrybutów przepływów użytkownika B2C | administrator atrybutów przepływów użytkownika B2C | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
administrator zestawu kluczy IEF B2C | administrator zestawu kluczy IEF B2C | aaf43236-0c0d-4d5f-883a-6955382ac081
administrator zasad IEF B2C | administrator zasad IEF B2C | 3edaf663-341e-4475-9f94-5c398ef6c070
Administrator rozliczeń | Administrator rozliczeń | b0f54661-2d74-4c50-afa3-1ec803f12efe
Administrator aplikacji w chmurze | Administrator aplikacji w chmurze | 158c047a-c907-4556-b7ef-446551a6b5f7
Administrator urządzenia w chmurze | Administrator urządzenia w chmurze | 7698a772-787b-4ac8-901f-60d6b08affd2
Administrator firmy | Administrator globalny | 62e90394-69f5-4237-9190-012177145e10
Administrator zgodności | Administrator zgodności | 17315797-102d-40b4-93e0-432062caca18
Administrator danych zgodności | Administrator danych zgodności | e6d1a23a-da11-4be4-9570-befc86d067a7
Administrator dostępu warunkowego | Administrator dostępu warunkowego | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Administrator usługi CRM | Administrator systemu Dynamics 365 | 44367163-eba1-44c3-98af-f5787879f96a
Osoba zatwierdzająca dostęp do skrytki klienta | Osoba zatwierdzająca Skrytka klienta dostępu | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Administrator usługi Desktop Analytics | Administrator usługi Desktop Analytics | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Administratorzy urządzeń | Administratorzy urządzeń | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Przyłączanie urządzenia | Przyłączanie urządzenia | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Menedżerowie urządzeń | Menedżerowie urządzeń | 2b499bcd-da44-4968-8aec-78e1674fa64d
Użytkownicy urządzeń | Użytkownicy urządzeń | d405c6df-0af8-4e3b-95e4-4d06e542189e
Czytelnicy katalogów | Czytelnicy katalogów | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Konta synchronizacji katalogów | Konta synchronizacji katalogów | d29b2b05-8046-44ba-8758-1e26182fcf32
Autorzy katalogów | Autorzy katalogów | 9360feb5-f418-4baa-8175-e2a00bac4301
Administrator usługi Exchange | Administrator programu Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Administrator zewnętrznego dostawcy tożsamości | Administrator zewnętrznego dostawcy tożsamości | be2f45a1-457d-42af-a067-6ec1fa63bc45
Czytnik globalny | Czytnik globalny | f2ef992c-3afb-46b9-b7cf-a126ee74c451
Zapraszający gościa | Zapraszający gościa | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Administrator pomocy technicznej | Administrator haseł | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Administrator usługi Intune | Administrator usługi Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Usługi kaizala administrator | Usługi kaizala administrator | 74ef975b-6605-40af-a5d2-b9539d836353
Administrator licencji | Administrator licencji | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Administrator usługi Lync | Administrator programu Skype dla firm | 75941009-915A-4869-abe7-691bff18279e
Czytnik prywatności centrum wiadomości | Czytnik prywatności centrum wiadomości | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Czytelnik centrum wiadomości | Czytelnik centrum wiadomości | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Obsługa pomoc partnera | Obsługa pomoc partnera | 4ba39ca4-527c-499a-b93d-d9b492c50246
Obsługa SVR partnera | Obsługa SVR partnera | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Administrator haseł | Administrator haseł | 966707d0-3269-4727-9be2-8c3a10f19b9d
Administrator usługi Power BI | Power BI administrator | a9ea8996-122f-4c74-9520-8edcd192826c
Administrator uprzywilejowanego uwierzytelniania | Administrator uprzywilejowanego uwierzytelniania | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Administrator ról uprzywilejowanych | Administrator ról uprzywilejowanych | e8611ab8-c189-46e8-94e1-60213ab1f814
Czytelnik raportów | Czytelnik raportów | 4a5d8f65-41da-4de4-8968-e035b65339cf
Administrator wyszukiwania | Administrator wyszukiwania | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Edytor wyszukiwania | Edytor wyszukiwania | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Administrator zabezpieczeń | Administrator zabezpieczeń | 194ae4cb-b126-40b2-bd5b-6091b380977d
Operator zabezpieczeń | Operator zabezpieczeń | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Czytelnik zabezpieczeń | Czytelnik zabezpieczeń | 5d6b6bb7-de71-4623-b4af-96380a352509
Administrator pomocy technicznej usługi | Administrator usługi | f023fd81-a637-4b56-95fd-791ac0226033
Administrator usługi SharePoint | Administrator programu SharePoint | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Administratorzy zespołu ds. komunikacji | Administratorzy zespołu ds. komunikacji | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Inżynierowie pomocy technicznej dla zespołów | Inżynierowie pomocy technicznej dla zespołów | f70938a0-fc10-4177-9e90-2178f8765737
Zespoły ds. pomocy technicznej | Zespoły ds. pomocy technicznej | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Administrator usługi Teams | Administrator usługi Teams | 69091246-20e8-4a56-aa4d-066075b2a7a8
Użytkownik | Użytkownik | a0b1b346-4d3e-4e8b-98f8-753987be4970
Administrator konta użytkownika | Administrator użytkowników | fe930be7-5e62-47db-91af-98c3a49a38b1
Dołączanie urządzenia w miejscu pracy | Dołączanie urządzenia w miejscu pracy | c34f683f-4d5a-4403-AFFD-6615e00e3a7f

## <a name="deprecated-roles"></a>Przestarzałe role

Nie należy używać następujących ról. Są one przestarzałe i zostaną usunięte z usługi Azure AD w przyszłości.

* Administrator licencji ad hoc
* Przyłączanie urządzenia
* Menedżerowie urządzeń
* Użytkownicy urządzeń
* Kreator zweryfikowanych użytkowników e-mail
* Administrator skrzynek pocztowych
* Dołączanie urządzenia w miejscu pracy

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o sposobie przypisywania użytkownika jako administratora subskrypcji platformy Azure, zobacz [Zarządzanie dostępem przy użyciu RBAC i Azure Portal](../../role-based-access-control/role-assignments-portal.md)
* Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).
* Aby uzyskać więcej informacji dotyczących relacji między usługą Azure Active Directory i subskrypcją platformy Azure, zobacz [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
