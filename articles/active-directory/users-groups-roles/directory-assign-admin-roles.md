---
title: Opisy ról administratora i uprawnienia — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Roli administratora można dodać użytkowników, przypisywać role administracyjne, resetować hasła użytkowników, zarządzać licencjami użytkowników lub zarządzać domenami.
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 06/04/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d31bde1a33d622c2c0b7aa716cbbbfbc8ef42ecf
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514575"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Uprawnienia roli administratora w usłudze Azure Active Directory

Za pomocą usługi Azure Active Directory (Azure AD), można wyznaczyć ograniczone administratorów do zarządzania zadaniami tożsamości w mniej uprzywilejowane role. Administratorzy mogą być przypisane do celów takich jak dodawanie lub zmienianie użytkowników, przypisywanie ról administracyjnych, resetowanie haseł użytkowników, zarządzanie licencjami użytkowników i zarządzanie nazwami domen. Domyślne uprawnienia użytkowników można zmienić tylko w ustawieniach użytkownika w usłudze Azure AD.

Administrator globalny ma dostęp do wszystkich funkcji administracyjnych. Domyślnie osoba, która zarejestruje się w subskrypcji platformy Azure przypisano rolę administratora globalnego dla katalogu. Tylko administratorzy globalni i Administratorzy ról uprzywilejowanych mogą delegować ról administratora. Aby zmniejszyć ryzyko dla Twojej firmy, zaleca się przypisania tej roli tylko kilku osobom w firmie.


## <a name="assign-or-remove-administrator-roles"></a>Przypisywanie lub usuwanie ról administratora

Aby dowiedzieć się, jak przypisywać role administracyjne dla użytkownika w usłudze Azure Active Directory, zobacz [widoku i przypisywanie ról administratorów w usłudze Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Dostępne role

Dostępne są następujące role administratora:

* **[Administrator aplikacji](#application-administrator)** : Użytkownicy w tej roli można tworzyć i zarządzać wszystkimi aspektami aplikacje dla przedsiębiorstw i rejestracje aplikacji, ustawienia serwera proxy aplikacji. Ta rola daje również możliwość wyrazić zgodę na delegowane uprawnienia i uprawnienia aplikacji, z wyjątkiem programu Microsoft Graph i Azure AD Graph. Użytkownicy przypisani do tej roli nie są dodawane jako właścicieli, podczas tworzenia nowej rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

  <b>Ważne</b>: Ta rola umożliwia zarządzanie poświadczeniami aplikacji. Użytkownicy przypisani do tej roli można dodać poświadczeń do aplikacji i spersonifikować tożsamości aplikacji przy użyciu tych poświadczeń. Jeśli tożsamości aplikacji udzielono dostępu do usługi Azure Active Directory, takie jak możliwość utworzenia lub zaktualizowania użytkownika lub inne obiekty, przypisani do tej roli użytkownika może wykonać te czynności podczas personifikacji aplikacji. Ta możliwość spersonifikować tożsamości aplikacji może być podniesienie uprawnień za pośrednictwem co użytkownik może wykonać za pomocą swoje przypisania roli w usłudze Azure AD. Jest ważne dowiedzieć się, że przypisanie użytkownika do roli administratora aplikacji daje im możliwość personifikacji tożsamość aplikacji.

* **[Deweloper aplikacji](#application-developer)** : Użytkownicy w tej roli mogą tworzyć rejestracje aplikacji po "Użytkownicy mogą rejestrować aplikacje" został ustawiony na nie. Ta rola również przyznaje uprawnienia do wyrażenia zgody na rzecz własnych po "Użytkownicy mogą zezwalać aplikacjom uzyskiwanie dostępu do danych firmy w ich imieniu" został ustawiony na nie. Użytkownicy przypisani do tej roli są dodawane jako właścicieli, podczas tworzenia nowej rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

* **[Administrator uwierzytelniania](#authentication-administrator)** : Użytkownicy z tą rolą ustawieniem lub zresetowaniem hasła bez poświadczeń. Uwierzytelnianie Administratorzy mogą wymagać od użytkowników ponownej rejestracji dla istniejących poświadczeń-password (na przykład, MFA lub FIDO) i odwoływanie **pamiętanie uwierzytelniania MFA w urządzeniu**, powoduje wyświetlenie monitu o usługi MFA na następnym logowaniu użytkowników, którzy są użytkowników niebędących administratorami lub przypisane następujące role:
  * Administrator uwierzytelniania
  * Odczytywanie katalogów
  * Osoba zapraszająca gości
  * Czytnik Centrum wiadomości
  * Czytnik raportów

  Rola administratora uwierzytelniania jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

  <b>Ważne</b>: Użytkownicy z tą rolą mogą zmienić poświadczenia dla osób, które mogą uzyskiwać dostęp do informacje poufne lub prywatne lub krytyczne konfiguracji i spoza usługi Azure Active Directory. Zmiana poświadczeń użytkownika, może oznaczać, że możliwość przyjęcia tożsamości i uprawnienia tego użytkownika. Na przykład:

  * Rejestrowanie aplikacji i aplikacji przedsiębiorstwa właścicieli, którzy mogą zarządzać poświadczeniami aplikacje, które są właścicielami. Te aplikacje mogą mieć uprzywilejowany uprawnień w usłudze Azure AD i gdzie indziej nie są przyznawane administratorom uwierzytelniania. Za pomocą tej ścieżki, które Administrator uwierzytelniania może przyjąć tożsamość właściciela aplikacji, a następnie dalej przyjąć tożsamość uprzywilejowanych aplikacji przez zaktualizowanie poświadczeń dla aplikacji.
  * Właścicieli subskrypcji platformy Azure, którzy mogą uzyskiwać dostęp do informacje poufne lub prywatne lub krytyczne konfiguracji na platformie Azure.
  * Grupy zabezpieczeń i grupy usługi Office 365 właścicieli, którzy mogą zarządzać członkostwem. Te grupy mogą udzielać dostępu do informacje poufne lub prywatne lub krytyczne konfiguracji w usłudze Azure AD i w innych miejscach.
  * Administratorzy w innych usługach poza usługi Azure AD, takich jak Exchange Online, zabezpieczenia pakietu Office i Centrum zgodności i zarządzania zasobami ludzkimi systemów.
  * Kierownicy doradcą prawnym i pracownicy działu kadr, którzy mogą uzyskiwać dostęp do poufne lub prywatne informacje, takie jak użytkownicy niebędący administratorami.

* **[Administrator usługi Azure Information Protection](#azure-information-protection-administrator)** : Użytkownicy z tą rolą mają wszystkie uprawnienia w usłudze Azure Information Protection. Ta rola umożliwia konfigurowanie etykiet w zasadach usługi Azure Information Protection, Zarządzanie szablonami ochrony i aktywacja ochrony. Ta rola nie przyznaje wszystkie uprawnienia w Centrum usługi Identity Protection, Privileged Identity Management, kondycji usługi Monitor Office 365, lub Office 365 Centrum zabezpieczeń i zgodności.

* **[Administrator przepływ użytkownika B2C](#b2c-user-flow-administrator)** : Użytkownicy posiadający tę rolę można tworzyć i zarządzać B2C obszarze przepływy użytkownika (zwane również "wbudowane" zasady) w witrynie Azure Portal. Przez utworzenie lub edycję przepływy użytkownika, tych użytkowników można zmienić zawartość html/CSS/javascript środowisko użytkownika, Zmień wymagania uwierzytelniania MFA na przepływ użytkownika, zmień oświadczenia w tokenie i dostosować ustawienia sesji dla wszystkich zasad w ramach dzierżawy. Z drugiej strony ta rola nie obejmują możliwość sprawdzenia danych użytkownika, lub wprowadzić zmiany atrybutów, które znajdują się w schemacie dzierżawy. Zmiany platformy środowiska tożsamości (zwane również niestandardowych) zasady również znajduje się poza zakresem tej roli.

* **[Administrator atrybut przepływ użytkownika B2C](#b2c-user-flow-attribute-administrator)** : Użytkownicy z tą rolą, dodawanie lub usuwanie atrybutów niestandardowych, które są dostępne dla wszystkich przepływów użytkownika w dzierżawie. Jako takie użytkownicy z tą rolą można zmienić lub dodać nowe elementy do schematu użytkownika końcowego i mieć wpływ na zachowanie wszystkie przepływy użytkownika i pośrednio powodują zmiany w jakie dane mogą być zadawane użytkowników końcowych i ostatecznie wysyłane jako oświadczenia do aplikacji. Tej roli nie można edytować przepływy użytkownika.

* **[Administrator zestawu kluczy IEF B2C](#b2c-ief-keyset-administrator)** :    Użytkownika można tworzyć i zarządzać zasadami kluczy i wpisów tajnych dla funkcji szyfrowania tokenów podpisów tokenów i oświadczeń szyfrowania i odszyfrowywania. Przez dodawanie nowych kluczy do istniejących kontenerów kluczy, ograniczony administrator może przerzucania kluczy tajnych, zgodnie z potrzebami, bez wywierania wpływu na istniejące aplikacje. Ten użytkownik może wyświetlić pełnej zawartości tych kluczy tajnych oraz ich daty wygaśnięcia, nawet po ich utworzeniu.
    
  <b>Ważne:</b> jest rola poufnych. Rola administratora zestawu kluczy należy starannie inspekcji i przypisane z rozwagą w środowisku przedprodukcyjnym i produkcyjnych.

* **[Administrator zasad IEF B2C](#b2c-ief-policy-administrator)** : Użytkownicy w tej roli ma możliwość tworzenia, odczytu, aktualizacji i Usuń wszystkie zasady niestandardowe w usłudze Azure AD B2C i w związku z tym mają pełną kontrolę nad struktura środowiska tożsamości w odpowiednich dzierżawy usługi Azure AD B2C. Edytując zasady, tego użytkownika można ustanowić bezpośrednie Federację z usługą zewnętrzny dostawca tożsamości, zmienianie schematu katalogu, zmiana zawartości wszystkich widocznych dla użytkownika (HTML, CSS, JavaScript), zmienianie wymagania aby zakończyć uwierzytelnianie, tworzenie nowych użytkowników, wysyłać dane użytkownika z systemami zewnętrznymi, łącznie z pełnej migracji i edytować wszystkie informacje o użytkownikach, w tym pól poufnych, takich jak hasła i numery telefonów. Z drugiej strony ta rola nie można zmienić klucze szyfrowania lub edytowania wpisów tajnych, używana do Federacji w dzierżawie.

  <b>Ważne:</b> Administrator zasad IEF B2 jest wysoce poufne roli, która powinna być przydzielona w bardzo ograniczonym zakresie, w przypadku dzierżaw w środowisku produkcyjnym. Działań w oparciu o tych użytkowników należy ściśle monitorowane, szczególnie w przypadku dzierżaw w środowisku produkcyjnym.

* **[Administrator rozliczeń](#billing-administrator)** : Dokonuje zakupów, zarządza subskrypcjami, zarządza biletami pomocy technicznej i monitoruje kondycję usługi.

* **[Administrator aplikacji w chmurze](#cloud-application-administrator)** : Użytkownicy w tej roli mają takie same uprawnienia, jak rolę Administrator aplikacji z wyjątkiem możliwości zarządzania serwera proxy aplikacji. Ta rola umożliwia tworzenie i zarządzanie nimi wszystkie aspekty aplikacji dla przedsiębiorstw i rejestracje aplikacji. Ta rola daje również możliwość wyrazić zgodę na delegowane uprawnienia i uprawnienia aplikacji, z wyjątkiem programu Microsoft Graph i Azure AD Graph. Użytkownicy przypisani do tej roli nie są dodawane jako właścicieli, podczas tworzenia nowej rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

  <b>Ważne</b>: Ta rola umożliwia zarządzanie poświadczeniami aplikacji. Użytkownicy przypisani do tej roli można dodać poświadczeń do aplikacji i spersonifikować tożsamości aplikacji przy użyciu tych poświadczeń. Jeśli tożsamości aplikacji udzielono dostępu do usługi Azure Active Directory, takie jak możliwość utworzenia lub zaktualizowania użytkownika lub inne obiekty, przypisani do tej roli użytkownika może wykonać te czynności podczas personifikacji aplikacji. Ta możliwość spersonifikować tożsamości aplikacji może być podniesienie uprawnień za pośrednictwem co użytkownik może wykonać za pomocą swoje przypisania roli w usłudze Azure AD. Jest ważne dowiedzieć się, że przypisanie użytkownika do roli Administrator aplikacji w chmurze daje im możliwość personifikacji tożsamość aplikacji.

* **[Administrator urządzenia w chmurze](#cloud-device-administrator)** : Użytkownicy w tej roli można włączyć, wyłączyć i usuwanie urządzeń w usłudze Azure AD i klucze do odczytu funkcją BitLocker systemu Windows 10 (jeśli istnieje) w witrynie Azure portal. Rola nie powoduje przyznania uprawnień do zarządzania innych właściwości, na urządzeniu.

* **[Administrator do spraw zgodności](#compliance-administrator)** : Użytkownicy z tą rolą mają uprawnienia do zarządzania związanych ze zgodnością funkcji w Centrum zgodności usługi Microsoft 365, Centrum administracyjnego usługi Microsoft 365, Azure i zabezpieczeń usługi Office 365 i Centrum zgodności. Użytkownicy mogą również zarządzać wszystkie funkcje w ramach Centrum administracyjne Exchange, zespoły i Skype dla firm Centrum administracyjnego i tworzyć bilety pomocy technicznej dla platformy Azure i usługi Microsoft 365. Więcej informacji znajduje się w temacie [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  W | Można zrobić
  ----- | ----------
  [Microsoft 365 Centrum zgodności](https://protection.office.com) | Ochrona danych i zarządzanie nimi w organizacji w usługach Microsoft 365<br>Zarządzanie alertami zgodności
  [Menedżer zgodności](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Śledzenie, przypisz i Zweryfikuj działań zgodność z przepisami w organizacji
  [Centrum zabezpieczeń usługi Office 365 i zgodności](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zarządzanie nadzór nad danymi<br>Sprawdzać informacje prawne i danych<br>Zarządzanie żądanie podmiotu danych
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Wyświetlać wszystkie dane inspekcji usługi Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Ma uprawnienia tylko do odczytu i może zarządzać alertami<br>Można tworzyć i modyfikować zasady dotyczące plików i Zezwalaj na akcje nadzoru plików<br> Można wyświetlić wszystkie wbudowane raporty w obszarze Zarządzanie danymi

<!--* **[Compliance Data Administrator](#compliance-data-administrator)**: Users with this role have permissions to protect and track data in the Microsoft 365 compliance center, Microsoft 365 admin center, and Azure. Users can also manage all features within the Exchange admin center, Compliance Manager, and Teams & Skype for Business admin center and create support tickets for Azure and Microsoft 365.

  In | Can do
  ----- | ----------
  [Microsoft 365 compliance center](https://protection.office.com) | Monitor compliance-related policies across Microsoft 365 services<br>Manage compliance alerts
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Track, assign, and verify your organization's regulatory compliance activities
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Manage data governance<br>Perform legal and data investigation<br>Manage Data Subject Request
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | View all Intune audit data
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Has read-only permissions and can manage alerts<br>Can create and modify file policies and allow file governance actions<br> Can view all the built-in reports under Data Management
-->
* **[Administrator dostępu warunkowego](#conditional-access-administrator)** : Użytkownicy z tą rolą mają możliwość zarządzania ustawieniami dostępu warunkowego usługi Azure Active Directory.
  > [!NOTE]
  > Aby wdrożyć zasady dostępu warunkowego programu Exchange ActiveSync na platformie Azure, użytkownik musi być administratorem globalnym.
  
* **[Osoba zatwierdzająca dostęp do skrytki klienta](#customer-lockbox-access-approver)** : Zarządza [skrytki klienta żąda](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) w Twojej organizacji. One otrzymywać powiadomienia e-mail dla żądania do skrytki klienta można zatwierdzać i odrzucać żądania z poziomu Centrum administracyjnego usługi Microsoft 365. Funkcja skrytki klienta mogą również włączyć lub wyłączyć. Tylko administratorzy globalni mogą resetować hasła użytkowników przypisanych do tej roli.
  <!--  This was announced in August of 2018. https://techcommunity.microsoft.com/t5/Security-Privacy-and-Compliance/Customer-Lockbox-Approver-Role-Now-Available/ba-p/223393-->

* **[Administratorzy urządzenia](#device-administrators)** : Ta rola jest dostępne do przypisania tylko jako dodatkowego administratora lokalnego w [ustawienia urządzenia](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Użytkownicy z tą rolą stają się administratorami maszyny lokalnej na wszystkich urządzeniach systemu Windows 10, które są przyłączone do usługi Azure Active Directory. Nie mają możliwość zarządzania obiektami urządzeń w usłudze Azure Active Directory. 

* **[Odczytywanie katalogów](#directory-readers)** : Jest to rola, który powinien być przypisany tylko do starszych aplikacjach, które nie obsługują [zgody Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Nie przypisuj je do użytkowników.

* **[Konta synchronizacji katalogu](#directory-synchronization-accounts)** : Nie należy używać. Ta rola jest przypisywany do usługi Azure AD Connect i jest nie przeznaczone lub obsługiwane w przypadku innych celów.

* **[Zapisywanie katalogów](#directory-writers)** : Jest to rola starszej wersji, która ma być przypisana do aplikacji, które nie obsługują [zgody Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Nie powinien zostać przypisany do żadnych użytkowników.

* **[Dynamics 365 administratora / administratora CRM](#crm-service-administrator)** : Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Dynamics 365 Online, jeśli usługa została zainstalowana, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji o [używać roli administratora usługi do zarządzania dzierżawą](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator usługi Dynamics 365". Jest on "Administrator Dynamics 365" [witryny Azure portal](https://portal.azure.com).

* **[Administrator programu Exchange](#exchange-service-administrator)** : Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft CRM Online, gdy ta usługa została zainstalowana. Ma również możliwość tworzenia i zarządzania wszystkich grup usługi Office 365, zarządzanie biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji o [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator usługi Exchange". Jest on "Administrator programu Exchange" [witryny Azure portal](https://portal.azure.com). Jest "Exchange Online polu" [Centrum administracyjne Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144). 

* **[Administrator dostawcy tożsamości zewnętrznych](#external-identity-provider-administrator)** : Administrator zarządza federacji między dzierżawami usługi Azure Active Directory i zewnętrzny dostawca tożsamości. Z tą rolą użytkownicy mogą dodawać nowych dostawców tożsamości i skonfigurować wszystkie ustawienia dostępne (np. ścieżka uwierzytelniania, identyfikator usługi, przypisane kontenery kluczy). Temu użytkownikowi można włączyć dzierżawcy w celu zaufania uwierzytelnienia od dostawcy tożsamości zewnętrznej. Wynikowy wpływ na wrażenia użytkownika końcowego, zależy od typu dzierżawy:
  * Dzierżaw usługi Azure Active Directory dla pracowników i partnerów: Dodanie federacyjnego (np. z usługi Gmail) natychmiast będzie miało wpływ na wszystkie zaproszenia gościa nie zostały jeszcze zrealizowane. Zobacz [Dodawanie Google jako dostawcy tożsamości dla użytkowników-gości B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
  * Dzierżawy usługi Azure Active Directory B2C: Dodanie federacyjnego (np. Facebook, lub z innej usługi Azure Active Directory) nie natychmiast wpływu na przepływy użytkownika końcowego do momentu dostawca tożsamości został dodany jako opcja w przepływ użytkownika (zwane również wbudowane zasady). Zobacz [Konfigurowanie konta Microsoft jako dostawcy tożsamości](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) przykład. Aby zmienić przepływy użytkownika, wymagany jest ograniczona rolę "Administrator przepływu użytkowników B2C".

* **[Administrator globalny / Administrator firmy](#company-administrator)** : Użytkownicy z tą rolą mają dostęp do wszystkich funkcji administracyjnych w usłudze Azure Active Directory, a także usług korzystających z tożsamości usługi Azure Active Directory, takich jak Centrum zabezpieczeń firmy Microsoft 365, Microsoft 365 Centrum zgodności, Exchange Online, SharePoint Online i Skype dla firm Online. Osoba, która zarejestruje się dla dzierżawy usługi Azure Active Directory staje się administratorem globalnym. Tylko administratorzy globalni mogą przypisywać pozostałe role administratorów. Może istnieć więcej niż jednego administratora globalnego w Twojej firmie. Administratorzy globalni mogą resetować hasła dla wszystkich użytkowników oraz wszystkich innych administratorów.

  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator firmy". Jest on "Administrator globalny" [witryny Azure portal](https://portal.azure.com).
  >
  >

* **[Osoba zapraszająca gości](#guest-inviter)** : Użytkownicy w tej roli mogą zarządzać zaproszeniami użytkowników gości B2B usługi Azure Active Directory po **członkowie mogą zapraszać** użytkownika został ustawiony na nie. Więcej informacji na temat współpracy B2B w [współpracy usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nie ma żadnych innych uprawnień.

* **[Administrator usługi Intune](#intune-service-administrator)** : Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft CRM Online, gdy ta usługa została zainstalowana. Ponadto ta rola oferuje możliwość zarządzania użytkownikami i urządzeniami w celu kojarzenia zasad, a także tworzenie grup i zarządzanie nimi. Więcej informacji o [kontrola administracji opartej na rolach (RBAC) w usłudze Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)
  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator usługi Intune". Jest on "Administrator usługi Intune" [witryny Azure portal](https://portal.azure.com).
  
 * **[Administrator usługi Kaizala](#kaizala-administrator)** : Użytkownicy z tą rolą mają uprawnienia globalne Zarządzanie ustawienia w ramach Microsoft Kaizala, gdy usługa jest obecna, a także możliwość zarządzania biletami pomocy technicznej i monitoruje kondycję usługi.
Ponadto użytkownik ma dostęp raporty związane z przyjęciem i użycie usługi Kaizala członków organizacji i raportach biznesowych, wygenerowany przy użyciu akcji usługi Kaizala. 

* **[Administrator licencji](#license-administrator)** : Użytkownicy w tej roli mogą dodawać, usuwanie i aktualizowanie licencji przydziały dla użytkowników, grup (z wykorzystaniem Licencjonowanie na podstawie grupy), a zarządzanie lokalizacji użytkowania — na użytkownikach. Rola nie powoduje przyznania możliwość zakupu lub Zarządzaj subskrypcjami Tworzenie lub zarządzać grupami, lub Utwórz lub Zarządzanie użytkownikami poza lokalizacji użytkowania. Ta rola nie ma dostępu do wyświetlania, tworzenia lub zarządzania biletami pomocy technicznej.

* **[Czytnik prywatność w Centrum wiadomości](#message-center-privacy-reader)** : Użytkownicy w tej roli można monitorować wszystkie powiadomienia w Centrum wiadomości, w tym komunikaty o prywatności danych. Czytelnicy prywatność w Centrum wiadomości otrzymywać powiadomień e-mail, w tym dotyczące prywatności danych i ich subskrypcję możesz anulować za pomocą preferencji Centrum wiadomości. Tylko Administrator globalny i czytnik zachowania Centrum wiadomości może odczytywać dane ochrony prywatności. Ponadto ta rola oferuje możliwość wyświetlania grup, domenami i subskrypcjami. Ta rola nie ma uprawnień do wyświetlania, tworzenia lub zarządzanie żądaniami obsługi.

* **[Czytnik Centrum wiadomości](#message-center-reader)** : Użytkownicy w tej roli można monitorować powiadomienia i aktualizacje porad dotyczących kondycji w [Centrum wiadomości usługi Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) dla swojej organizacji w skonfigurowanych usług, takich jak Exchange, usługa Intune i Microsoft Teams. Czytelnicy Centrum wiadomości otrzymasz tygodniowy skróty e-mail wpisów i aktualizacji i mogą udostępniać wpisy Centrum wiadomości w usłudze Office 365. W usłudze Azure AD użytkownicy przypisani do tej roli tylko mają dostęp tylko do odczytu w usługach Azure AD, takich jak użytkownicy i grupy. Ta rola nie ma dostępu do wyświetlania, tworzenia lub zarządzania biletami pomocy technicznej.

* **[Partner obsługi 1](#partner-tier1-support)** : Nie należy używać. Ta rola jest przestarzała i zostanie usunięty z usługi Azure AD w przyszłości. Ta rola jest przeznaczony do użytku przez małą liczbę partnerów firmy Microsoft w odsprzedaży i nie jest przeznaczona do użytku ogólnego.

* **[Partner pomocy technicznej w warstwie 2](#partner-tier2-support)** : Nie należy używać. Ta rola jest przestarzała i zostanie usunięty z usługi Azure AD w przyszłości. Ta rola jest przeznaczony do użytku przez małą liczbę partnerów firmy Microsoft w odsprzedaży i nie jest przeznaczona do użytku ogólnego.

* **[Administrator pomocy technicznej (hasło)](#helpdesk-administrator)** : Użytkownicy z tą rolą można zmienić hasła, unieważnienie tokeny odświeżania, zarządzanie żądaniami obsługi i monitorowania kondycji usługi. Unieważnienie token odświeżania wymusza na użytkowniku, aby zalogować się ponownie. Administratorzy pomocy technicznej mogą resetować hasła i unieważnić tokenów odświeżania innych użytkowników, którzy są użytkowników niebędących administratorami lub przypisane następujące role:
  * Odczytywanie katalogów
  * Osoba zapraszająca gości
  * Administrator pomocy technicznej
  * Czytnik Centrum wiadomości
  * Czytnik raportów
  
  <b>Ważne</b>: Użytkownicy z tą rolą mogą zmienić hasła dla osób, które mogą uzyskiwać dostęp do informacje poufne lub prywatne lub krytyczne konfiguracji i spoza usługi Azure Active Directory. Zmiana hasła użytkownika może oznaczać, że możliwość przyjęcia tożsamości i uprawnienia tego użytkownika. Na przykład:
  * Rejestrowanie aplikacji i aplikacji przedsiębiorstwa właścicieli, którzy mogą zarządzać poświadczeniami aplikacje, które są właścicielami. Te aplikacje mogą mieć uprzywilejowany uprawnień w usłudze Azure AD i gdzie indziej nie zostały przyznane dla administratorów pomocy technicznej. Za pomocą tej ścieżki, które Administrator pomocy technicznej może przyjąć tożsamość właściciela aplikacji, a następnie dalej przyjąć tożsamość uprzywilejowanych aplikacji przez zaktualizowanie poświadczeń dla aplikacji.
  * Właścicieli subskrypcji platformy Azure, którzy mogą uzyskiwać dostęp do informacje poufne lub prywatne lub krytyczne konfiguracji na platformie Azure.
  * Grupy zabezpieczeń i grupy usługi Office 365 właścicieli, którzy mogą zarządzać członkostwem. Te grupy mogą udzielać dostępu do informacje poufne lub prywatne lub krytyczne konfiguracji w usłudze Azure AD i w innych miejscach.
  * Administratorzy w innych usługach poza usługi Azure AD, takich jak Exchange Online, zabezpieczenia pakietu Office i Centrum zgodności i zarządzania zasobami ludzkimi systemów.
  * Kierownicy doradcą prawnym i pracownicy działu kadr, którzy mogą uzyskiwać dostęp do poufne lub prywatne informacje, takie jak użytkownicy niebędący administratorami.


  > [!NOTE]
  > Delegowanie uprawnień administracyjnych za pośrednictwem podzbiór użytkowników i stosowanie zasad do podzbioru użytkowników jest możliwe dzięki [jednostki administracyjne (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).


  > [!NOTE]
  > Tej roli był wcześniej nazywany "Administrator haseł" [witryny Azure portal](https://portal.azure.com/). Zmieniamy jej nazwę na "Administrator pomocy technicznej", aby dopasować jej nazwę w Azure AD PowerShell, interfejsu API usługi Azure AD Graph i interfejsu API Microsoft Graph. Przez krótki czas zmienimy nazwę na "Administrator pomocy technicznej (hasło)" w witrynie Azure portal, zanim zmiany "Administrator pomocy technicznej".


* **[Power BI Administrator](#power-bi-service-administrator)** : Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Power BI, gdy usługa jest obecna, a także możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji o [opis roli administratora usługi Power BI](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator usługi Power BI". Jest on "Administrator usługi Power BI" [witryny Azure portal](https://portal.azure.com).

* **[Uprzywilejowany uwierzytelniania administratora](#privileged-authentication-administrator)** : Użytkownicy posiadający tę rolę można ustawić lub zresetować hasła innych poświadczeń dla wszystkich użytkowników, w tym administratorzy globalni. Uprzywilejowani Administratorzy uwierzytelniania umożliwia wymuszenie użytkowników ponownej rejestracji dla istniejących poświadczeń-password (np. uwierzytelnianie wieloskładnikowe, FIDO) i odwołać "uwierzytelnianie wieloskładnikowe na urządzeniu" należy pamiętać, że monitowania o MFA podczas następnego logowania wszystkich użytkowników. Uprzywilejowanych administratorów Authentication wykonywać następujące czynności:
  * Wymuszenie użytkowników, aby ponownie zarejestrować względem istniejących poświadczeń-password (np. uwierzytelnianie wieloskładnikowe, FIDO)
  * Odwołanie "uwierzytelnianie wieloskładnikowe na urządzeniu" należy pamiętać, że monitowania o uwierzytelnianie wieloskładnikowe przy następnym logowaniu

* **[Administrator ról uprzywilejowanych](#privileged-role-administrator)** : Użytkownicy z tą rolą mogą zarządzać przypisaniami ról, w usłudze Azure Active Directory, a także w ramach usługi Azure AD Privileged Identity Management. Ponadto ta rola umożliwia zarządzanie wszystkimi aspektami Zarządzanie tożsamościami uprzywilejowanymi i jednostek administracyjnych.

  <b>Ważne</b>: Ta rola daje możliwość zarządzania przypisaniami dla wszystkich ról usługi Azure AD, łącznie z rolą administratora globalnego. Ta rola nie ma innych możliwości uprzywilejowanego w usłudze Azure AD, takie jak tworzenie lub aktualizowanie użytkowników. Użytkownicy przypisani do tej roli można przyznać samych lub innych dodatkowych uprawnień, przypisując dodatkowych ról.

* **[Czytnik raportów](#reports-reader)** : Użytkownicy z tą rolą mogą przeglądać dane raportowania użycia i pulpit nawigacyjny raportów w Centrum administracyjnym usługi Microsoft 365 i kontekstu przyjęcia pakietu w usłudze Power BI. Ponadto rola zapewnia dostęp do logowania w raportach i działań w usłudze Azure AD i interfejsu API raportowania danych zwróconych przez program Microsoft Graph. Użytkownik przypisany do roli czytelnika raportów dostęp tylko odpowiednie użycia i metryk przyjęcia. Nie mają żadnych uprawnień administratora do skonfigurowania ustawień lub dostępu do Centrum administracyjnego konkretnych produktów, takich jak program Exchange. Ta rola nie ma dostępu do wyświetlania, tworzenia lub zarządzania biletami pomocy technicznej.

* **[Administrator wyszukiwania](#search-administrator)** : Użytkownicy w tej roli mają pełny dostęp do wszystkich funkcji programu Microsoft Search management w Centrum administracyjnym usługi Microsoft 365. Wyszukaj administratorów można delegować uprawnienia do administratorów wyszukiwania i wyszukiwanie edytora ról do użytkowników i tworzyć i zarządzania zawartością, takich jak zakładek, Q & jako i lokalizacje. Ponadto te użytkownicy mogą przeglądać Centrum wiadomości, monitorowania kondycji usługi i tworzenia żądań obsługi.

* **[Edytor wyszukiwania](#search-editor)** : Użytkownicy w tej roli można utworzyć, zarządzanie i usunąć zawartość dla programu Microsoft Search w Centrum administracyjnym usługi Microsoft 365 zakładek, pytania i odpowiedzi w tym & jako i lokalizacji.

* **[Administrator zabezpieczeń](#security-administrator)** : Użytkownicy z tą rolą mają uprawnienia do zarządzania funkcjach zabezpieczeń dostępnych w Centrum zabezpieczeń firmy Microsoft 365, Azure Active Directory Identity Protection, Azure Information Protection i zabezpieczeń usługi Office 365 i Centrum zgodności. Więcej informacji na temat usługi Office 365 uprawnień znajduje się w temacie [uprawnienia w Centrum zgodności i zabezpieczeń usługi Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  W | Można zrobić
  --- | ---
  [Centrum zabezpieczeń firmy Microsoft 365](https://protection.office.com) | Monitorowanie zasad zabezpieczeń w usługach Microsoft 365<br>Zarządzanie zagrożeniami i alerty<br>Wyświetlanie raportów
  Centrum usługi Identity Protection | Wszystkie uprawnienia roli Czytelnik zabezpieczeń<br>Ponadto możliwość wykonywania wszystkich operacji Centrum usługi Identity Protection z wyjątkiem resetowania haseł
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Wszystkie uprawnienia roli Czytelnik zabezpieczeń<br>**Nie można** zarządzać przypisaniami ról usługi Azure AD lub ustawienia
  [Centrum zabezpieczeń usługi Office 365 i zgodności](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zarządzanie zasadami zabezpieczeń<br>Wyświetl, zbadaj i reagowania na zagrożenia bezpieczeństwa<br>Wyświetlanie raportów
  Azure Advanced Threat Protection | Monitorowanie i odpowiadanie na podejrzane zabezpieczeń działania
  Windows Defender ATP i EDR | Przypisywanie ról<br>Zarządzaj grupami maszyn<br>Konfigurowanie punktu końcowego wykrywania zagrożeń i automatyczne rozwiązywanie problemów<br>Wyświetl, zbadaj i odpowiadanie na alerty
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Widoki użytkownika, urządzenia, rejestracji, konfiguracji i informacje o aplikacji<br>Nie można wprowadzać zmiany w usłudze Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Dodaj administratorów, dodawania zasad i ustawień, przekazywania dzienników i wykonywania akcji ładu
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Można wyświetlić zasady zabezpieczeń, widoku stany zabezpieczeń, edytować zasady zabezpieczeń, wyświetlanie alertów i zaleceń, Odrzuć alerty i zalecenia
  [Kondycja usługi Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Wyświetl kondycję usługi Office 365

<!--* **[Security operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management, as well as the ability to read Azure Active Directory sign-in reports and audit logs, and in Office 365 Security & Compliance Center.

  In | Can do
  --- | ---
  [Microsoft 365 security center](https://protection.office.com) | All permissions of the Security Reader role<br>View, investigate, and respond to security threats alerts
  Identity Protection Center | All permissions of the Security Reader role<br>Additionally, the ability to perform all Identity Protection Center operations except for resetting passwords
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | All permissions of the Security Reader role
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  Windows Defender ATP and EDR | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | All permissions of the Security Reader role
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | All permissions of the Security Reader role
  [Office 365 service health](https://docs.microsoft.com/office365/enterprise/view-service-health) | View the health of Office 365 services
-->
* **[Czytelnik zabezpieczeń](#security-reader)** : Użytkownicy z tą rolą mają globalny dostęp tylko do odczytu w funkcji związanych z zabezpieczeniami, w tym wszystkie informacje w Microsoft 365 usługi security center, Azure Active Directory, Identity Protection, Privileged Identity Management, a także możliwość odczytu w usłudze Azure Active Katalog raportów logowania i dzienniki inspekcji w Centrum zgodności i zabezpieczeń usługi Office 365. Więcej informacji na temat usługi Office 365 uprawnień znajduje się w temacie [uprawnienia w Centrum zgodności i zabezpieczeń usługi Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  W | Można zrobić
  --- | ---
  [Centrum zabezpieczeń firmy Microsoft 365](https://protection.office.com) | Wyświetl zasady zabezpieczeń w usługach Microsoft 365<br>Zagrożenia bezpieczeństwa View i alerty<br>Wyświetlanie raportów
  Centrum usługi Identity Protection | Odczyt wszystkich raporty dotyczące zabezpieczeń i informacje o ustawieniach dla funkcji zabezpieczeń<br><ul><li>Anti-spam<li>Szyfrowanie<li>Ochrona przed utratą danych<li>Chroniące przed złośliwym kodem<li>Zaawansowana ochrona przed zagrożeniami<li>Przed wyłudzaniem<li>Reguły Mailflow
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Ma dostęp tylko do odczytu, aby wszystkie informacje są prezentowane z użyciem usługi Azure AD PIM: Zasady i raportów dotyczących przypisania roli usługi Azure AD, zabezpieczenia przeglądów i w przyszłości dostęp do odczytu do zasad, dane i raporty dla scenariuszy, oprócz przypisania roli usługi Azure AD.<br>**Nie można** Zarejestruj się w usłudze Azure AD PIM lub wprowadzać żadnych zmian. W portalu usługi PIM lub za pośrednictwem programu PowerShell ktoś jest w tej roli można aktywować dodatkowe role (na przykład administrator globalny lub Administrator ról uprzywilejowanych), jeśli użytkownik kwalifikuje się do nich.
  [Centrum zabezpieczeń usługi Office 365 i zgodności](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Wyświetlanie zasad zabezpieczeń<br>Wyświetlanie i badanie zagrożeń<br>Wyświetlanie raportów
  Windows Defender ATP i EDR | Wyświetlanie i badanie alertów
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Widoki użytkownika, urządzenia, rejestracji, konfiguracji i informacje o aplikacji. Nie można wprowadzać zmiany w usłudze Intune.
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Ma uprawnienia tylko do odczytu i może zarządzać alertami
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Można wyświetlić, zaleceń i alertów, widok zasady zabezpieczeń, wyświetlanie stanów zabezpieczeń, ale nie może wprowadzać zmian
  [Kondycja usługi Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Wyświetl kondycję usługi Office 365

* **[Administrator pomocy technicznej usługi](#service-support-administrator)** : Użytkownicy z tą rolą mogą otwierać żądania pomocy technicznej firmy Microsoft dla usług platformy Azure i usługi Office 365 i widoki pulpitu nawigacyjnego usług i Centrum komunikatów w [witryny Azure portal](https://portal.azure.com) i [Centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com). Więcej informacji o [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator pomocy technicznej usługi." "Administrator usługi" jest w [witryny Azure portal](https://portal.azure.com), [Centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com), jak i portalu usługi Intune.

* **[Administrator programu SharePoint](#sharepoint-service-administrator)** : Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft SharePoint Online, jeśli usługa została zainstalowana, a także możliwość tworzenia i zarządzania wszystkich grup usługi Office 365, zarządzanie biletami pomocy technicznej i monitorowania kondycji usługi. Więcej informacji o [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator usługi SharePoint." Jest on "Administrator programu SharePoint" [witryny Azure portal](https://portal.azure.com).

* **[Skype dla firm / Administrator usługi Lync](#lync-service-administrator)** : Użytkownicy z tą rolą mają uprawnienia globalne w usłudze Microsoft Skype dla firm, gdy usługa jest obecna, a także zarządzać specyficzne dla programu Skype atrybutów użytkownika w usłudze Azure Active Directory. Ponadto ta rola daje możliwość zarządzania biletami pomocy technicznej i monitorowania kondycji usługi i uzyskać dostęp do zespołów i Skype dla firm Centrum administracyjnego. Konto musi mieć również licencję dla zespołów lub nie można uruchomić polecenia cmdlet programu PowerShell zespołów. Więcej informacji o [dotyczące programu Skype dla firm przypisaną rolę administratora](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) i zespołów, informacje o licencjonowaniu na [Skype dla firm i Microsoft Teams dodatek licencjonowania](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "Administrator usługi Lync". ""Skype dla firm Administrator znajduje się w [witryny Azure portal](https://portal.azure.com/).

* **[Zespoły administratora](#teams-service-administrator)** : Użytkownicy w tej roli mogą zarządzać wszystkimi aspektami obciążenie Microsoft Teams przy użyciu programu Microsoft Teams & Skype Centrum administracyjnego usługi biznesowe i odpowiednie moduły programu PowerShell. W tym, wśród innych obszarów, wszystkie narzędzia do zarządzania związane z telefoniczne, wiadomości, spotkań i same zespoły dbają. Ta rola również przyznaje możliwość tworzenia i zarządzania wszystkich grup usługi Office 365, zarządzanie biletami pomocy technicznej i monitorowania kondycji usługi.
  > [!NOTE]
  > W interfejsu API Microsoft Graph, interfejs API usługi Azure AD Graph i Azure AD PowerShell ta rola jest rozpoznawana jako "zespoły Administrator usługi". "Administrator zespoły" znajduje się w [witryny Azure portal](https://portal.azure.com).

* **[Zespoły administratora komunikacji](#teams-communications-administrator)** : Użytkownicy w tej roli może zarządzać aspektami Microsoft Teams obciążenie związane z głosu & telefonii. W tym narzędzia do zarządzania dla przypisywania numerów telefonów, głos i spotkania zasady i pełny dostęp do narzędzi analizy wywołania.

* **[Zespoły ze specjalistą pomocy technicznej komunikacji](#teams-communications-support-engineer)** : Użytkownicy w tej roli można rozwiązywać problemy z komunikacją w ramach Microsoft Teams i Skype dla firm przy użyciu wywołania użytkownika narzędzi w Microsoft Teams i Skype dla firm Centrum administracyjnego do rozwiązywania problemów. Użytkownicy w tej roli mogą wyświetlać pełną wywołanie rejestrowanie informacji dla wszystkich uczestników związane. Ta rola nie ma dostępu do wyświetlania, tworzenia lub zarządzania biletami pomocy technicznej.

* **[Zespoły, specjalista ds. pomocy technicznej komunikacji](#teams-communications-support-specialist)** : Użytkownicy w tej roli można rozwiązywać problemy z komunikacją w ramach Microsoft Teams i Skype dla firm przy użyciu wywołania użytkownika narzędzi w Microsoft Teams i Skype dla firm Centrum administracyjnego do rozwiązywania problemów. Użytkownicy w tej roli mogą wyświetlać szczegóły użytkownika tylko w wywołaniu dla określonego użytkownika, że ma wyszukiwać. Ta rola nie ma dostępu do wyświetlania, tworzenia lub zarządzania biletami pomocy technicznej.

* **[Administrator użytkowników](#user-administrator)** : Użytkownicy z tej roli mogą tworzyć użytkowników, zarządzać wszystkimi aspektami użytkowników z pewnymi ograniczeniami (patrz poniżej) i można zaktualizować zasad wygasania haseł. Ponadto użytkownicy posiadający tę rolę można tworzyć i zarządzać wszystkich grup. Ta rola obejmuje również możliwość tworzenia i zarządzania widoki użytkowników, zarządzanie biletami pomocy technicznej i monitorowania kondycji usługi.

  | | |
  | --- | --- |
  |Uprawnieniami ogólnymi|<p>Tworzenie użytkowników i grup</p><p>Tworzenie i zarządzanie nimi widoki użytkowników</p><p>Zarządzanie biletami pomocy technicznej pakietu Office<p>Zaktualizuj zasady wygasania haseł|
  |<p>Na wszystkich użytkowników w tym wszystkich administratorów</p>|<p>Zarządzanie licencjami</p><p>Zarządzanie wszystkich właściwości użytkownika, z wyjątkiem główna nazwa użytkownika</p>
  |Tylko na użytkowników, którzy są administratorami bez lub w jednym z następujących ograniczonych ról administratora:<ul><li>Odczytywanie katalogów<li>Osoba zapraszająca gości<li>Administrator pomocy technicznej<li>Czytnik Centrum wiadomości<li>Czytnik raportów<li>Administrator użytkowników|<p>Usuń i przywracania</p><p>Wyłącz i Włącz</p><p>Unieważnienie tokenów odświeżania</p><p>Zarządzanie wszystkich właściwości użytkownika, w tym nazwa główna użytkownika</p><p>Resetowanie hasła</p><p>Aktualizuj klucze urządzenia (FIDO)</p>
  
  <b>Ważne</b>: Użytkownicy z tą rolą mogą zmienić hasła dla osób, które mogą uzyskiwać dostęp do informacje poufne lub prywatne lub krytyczne konfiguracji i spoza usługi Azure Active Directory. Zmiana hasła użytkownika może oznaczać, że możliwość przyjęcia tożsamości i uprawnienia tego użytkownika. Na przykład:
  * Rejestrowanie aplikacji i aplikacji przedsiębiorstwa właścicieli, którzy mogą zarządzać poświadczeniami aplikacje, które są właścicielami. Te aplikacje mogą mają uprzywilejowany uprawnień w usłudze Azure AD i gdzie indziej nie przyznawać Administratorzy użytkowników. Za pomocą tej ścieżki, które Administrator użytkowników może przyjąć tożsamość właściciela aplikacji, a następnie dalej przyjąć tożsamość uprzywilejowanych aplikacji przez zaktualizowanie poświadczeń dla aplikacji.
  * Właścicieli subskrypcji platformy Azure, którzy mogą uzyskiwać dostęp do informacje poufne lub prywatne lub krytyczne konfiguracji na platformie Azure.
  * Grupy zabezpieczeń i grupy usługi Office 365 właścicieli, którzy mogą zarządzać członkostwem. Te grupy mogą udzielać dostępu do informacje poufne lub prywatne lub krytyczne konfiguracji w usłudze Azure AD i w innych miejscach.
  * Administratorzy w innych usługach poza usługi Azure AD, takich jak Exchange Online, zabezpieczenia pakietu Office i Centrum zgodności i zarządzania zasobami ludzkimi systemów.
  * Kierownicy doradcą prawnym i pracownicy działu kadr, którzy mogą uzyskiwać dostęp do poufne lub prywatne informacje, takie jak użytkownicy niebędący administratorami.

## <a name="role-permissions"></a>Uprawnienia roli
W poniższych tabelach opisano określone uprawnienia w usłudze Azure Active Directory do każdej roli. Niektóre role mogą mieć dodatkowe uprawnienia w usługach firmy Microsoft poza usługą Azure Active Directory.

### <a name="application-administrator"></a>Administrator aplikacji
Można tworzyć i zarządzać wszystkimi aspektami rejestracje aplikacji i aplikacji przedsiębiorstwa.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Zaktualizuj właściwość applications.audience w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Zaktualizuj właściwość applications.authentication w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Aktualizacja podstawowe właściwości aplikacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/create | Tworzenie aplikacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Zaktualizuj właściwość applications.credentials w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/DELETE | Usuwanie aplikacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Zaktualizuj właściwość applications.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Zaktualizuj właściwość applications.permissions w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Zaktualizuj właściwość applications.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Utwórz appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Przeczytaj appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Zaktualizuj appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Usuń appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Odczyt wszystkich właściwości (w tym właściwości uprzywilejowanych) na auditLogs w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Odczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Zaktualizuj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Usuń zasady w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Odczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Zaktualizuj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Odczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Zaktualizuj właściwość servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Zaktualizuj właściwość servicePrincipals.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Zaktualizuj właściwość servicePrincipals.audience w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Zaktualizuj właściwość servicePrincipals.authentication w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aktualizacja właściwości podstawowe servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Utwórz servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Zaktualizuj właściwość servicePrincipals.credentials w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Usuń servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Zaktualizuj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Zaktualizuj właściwość servicePrincipals.permissions w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Zaktualizuj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Odczyt wszystkich właściwości (w tym właściwości uprzywilejowanych) na signInReports w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="application-developer"></a>Deweloper aplikacji
Rejestracje aplikacji można tworzyć niezależnie od "Użytkownicy mogą rejestrować aplikacje" ustawienie.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Tworzenie aplikacji w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Utwórz appRoleAssignments w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Utwórz oAuth2PermissionGrants w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Utwórz servicePrincipals w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |

### <a name="authentication-administrator"></a>Administrator uwierzytelniania
Można wyświetlić, wartości i zresetować informacje dotyczące metody uwierzytelniania dla dowolnego użytkownika bez uprawnień administratora.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Unieważnienie wszystkie tokeny odświeżania użytkownika w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Aktualizacja właściwości silnego uwierzytelniania, takich jak informacji o poświadczeniach usługi MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="azure-information-protection-administrator"></a>Administrator usługi Azure Information Protection
Może zarządzać wszystkimi aspektami usługi Azure Information Protection.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="b2c-user-flow-administrator"></a>administrator przepływów użytkownika B2C
Utwórz i Zarządzaj wszystkimi aspektami przepływy użytkownika.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Przeczytaj i skonfigurować przepływy użytkownika w usłudze Azure Active Directory B2C. |

### <a name="b2c-user-flow-attribute-administrator"></a>administrator atrybutów przepływów użytkownika B2C
Tworzenie i Zarządzanie schematami atrybut dostępne dla wszystkich przepływów użytkownika.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Przeczytaj i konfigurowanie atrybutów użytkownika w usłudze Azure Active Directory B2C. |

### <a name="b2c-ief-keyset-administrator"></a>administrator zestawu kluczy IEF B2C
Zarządzanie wpisami tajnymi federacyjnych i szyfrowania w struktura środowiska tożsamości.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Przeczytaj i zestaw kluczy należy skonfigurować w usłudze Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator"></a>administrator zasad IEF B2C
Tworzenie i zarządzanie zasadami framework zaufania w struktura środowiska tożsamości.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Przeczytaj i skonfigurować zasady niestandardowe w usłudze Azure Active Directory B2C. |

### <a name="billing-administrator"></a>Administrator rozliczeń
Mogą wykonywać typowe zadania związane z rozliczeniami takie jak aktualizowanie informacji o płatności.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/Organization/Basic/Update | Zaktualizuj właściwości podstawowe w organizacji w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Zarządzaj wszystkimi aspektami rozliczeń usługi Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="desktop-analytics-administrator"></a>Administrator usługi Analytics pulpitu
Można zarządzać analizy pulpitu i dostosowywanie pakietu Office i zasady dotyczące usług. Analiza pulpitu obejmuje to możliwość wyświetlania spisu zasobów, tworzenie planów wdrożenia, wyświetlić stan wdrożenia i kondycji. Dostosowywanie pakietu Office i zasady dotyczące usługi ta rola umożliwia użytkownikom zarządzanie zasadami pakietu Office.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Zarządzaj wszystkimi aspektami Analytics pulpitu. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="cloud-application-administrator"></a>Administrator aplikacji w chmurze
Można tworzyć i zarządzać wszystkimi aspektami rejestracje aplikacji i aplikacji przedsiębiorstwa, z wyjątkiem serwera Proxy aplikacji.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Zaktualizuj właściwość applications.audience w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Zaktualizuj właściwość applications.authentication w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Aktualizacja podstawowe właściwości aplikacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/create | Tworzenie aplikacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Zaktualizuj właściwość applications.credentials w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/DELETE | Usuwanie aplikacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Zaktualizuj właściwość applications.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Zaktualizuj właściwość applications.permissions w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Zaktualizuj właściwość applications.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Utwórz appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Zaktualizuj appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Usuń appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Odczyt wszystkich właściwości (w tym właściwości uprzywilejowanych) na auditLogs w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Odczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Zaktualizuj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Usuń zasady w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Odczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Zaktualizuj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Odczytaj właściwość policies.applicationConfiguration w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Zaktualizuj właściwość servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Zaktualizuj właściwość servicePrincipals.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Zaktualizuj właściwość servicePrincipals.audience w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Zaktualizuj właściwość servicePrincipals.authentication w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aktualizacja właściwości podstawowe servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Utwórz servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Zaktualizuj właściwość servicePrincipals.credentials w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Usuń servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Zaktualizuj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Zaktualizuj właściwość servicePrincipals.permissions w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Zaktualizuj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Odczyt wszystkich właściwości (w tym właściwości uprzywilejowanych) na signInReports w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="cloud-device-administrator"></a>Administrator urządzenia w chmurze
Pełny dostęp do zarządzania urządzeniami w usłudze Azure AD.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Odczyt wszystkich właściwości (w tym właściwości uprzywilejowanych) na auditLogs w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Odczytaj właściwość devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/DELETE | Usuwanie urządzeń w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/disable | Wyłącz urządzenia w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/enable | Włącz urządzeń w usłudze Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Odczyt wszystkich właściwości (w tym właściwości uprzywilejowanych) na signInReports w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |

### <a name="company-administrator"></a>Administrator firmy
Może zarządzać wszystkimi aspektami usługi Azure AD i usługi firmy Microsoft, tożsamość tego usługa Azure AD.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów i odczytywanie i aktualizowanie właściwości standardowe w microsoft.aad.cloudAppSecurity. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Tworzenie i usuwanie administrativeUnits, odczytu i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/allProperties/allTasks | Tworzenie i usuwać aplikacje, Odczyt i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Tworzenie i usuwanie appRoleAssignments, odczytu i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Odczyt wszystkich właściwości (w tym właściwości uprzywilejowanych) na auditLogs w usłudze Azure Active Directory. |
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
| microsoft.aad.directory/signInReports/allProperties/read | Odczyt wszystkich właściwości (w tym właściwości uprzywilejowanych) na signInReports w usłudze Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Tworzenie i usuwanie subscribedSkus, odczytu i aktualizowania wszystkich właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/allProperties/allTasks | Tworzenie i usunąć użytkowników, przeczytaj i Aktualizuj wszystkie właściwości w usłudze Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Wykonuje wszystkie działania w programie Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów i odczytywanie i aktualizowanie właściwości standardowe w microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Odczyt wszystkich zasobów w microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Odczyt wszystkich zasobów w microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Zarządzaj wszystkimi aspektami rozliczeń usługi Office 365. |
| microsoft.intune/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Intune. |
| microsoft.office365.complianceManager/allEntities/allTasks | Zarządzać wszystkimi aspektami Office 365 kierownikiem ds. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Zarządzaj wszystkimi aspektami Analytics pulpitu. |
| microsoft.office365.exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Zarządzać wszystkimi aspektami skrytki klienta programu Office 365 |
| microsoft.office365.messageCenter/messages/read | Przeczytaj komunikaty w microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Przeczytaj securityMessages w microsoft.office365.messageCenter. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Zarządzaj wszystkimi aspektami Centrum ochrony usługi Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów i odczytywanie i aktualizowanie właściwości standardowe w microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów i odczytywanie i aktualizowanie właściwości standardowe w microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Skype dla firm Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.office365.usageReports/allEntities/read | Raporty użycia odczytu Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Dynamics 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Odczyt wszystkich zasobów w microsoft.windows.defenderAdvancedThreatProtection. |

### <a name="compliance-administrator"></a>Administrator do spraw zgodności
Może odczytywać i zarządzać konfigurację i raporty zgodności w usłudze Azure AD i Office 365.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Zarządzać wszystkimi aspektami Office 365 kierownikiem ds. |
| microsoft.office365.exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów i odczytywanie i aktualizowanie właściwości standardowe w microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Skype dla firm Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="conditional-access-administrator"></a>Administrator dostępu warunkowego
Może zarządzać możliwościami dostępu warunkowego.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Odczytaj właściwość policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Zaktualizuj właściwość policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Usuń zasady w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Odczytaj właściwość policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Zaktualizuj właściwość policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Odczytaj właściwość policies.conditionalAccess w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | Zaktualizuj właściwość policies.conditionalAccess w usłudze Azure Active Directory. |

### <a name="crm-service-administrator"></a>Administrator usługi CRM
Może zarządzać wszystkimi aspektami produktu Dynamics 365.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="customer-lockbox-access-approver"></a>Osoba zatwierdzająca dostęp do skrytki klienta
Zatwierdzać żądania pomocy technicznej firmy Microsoft na dostęp do danych organizacji klienta.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.lockbox/allEntities/allTasks | Zarządzać wszystkimi aspektami skrytki klienta programu Office 365 |

### <a name="device-administrators"></a>Administratorzy urządzenia
Użytkownicy przypisani do tej roli są dodawane do grupy Administratorzy lokalni na urządzeniach przyłączonych do usługi AD systemu Azure.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Przeczytaj podstawowe właściwości groupSettings w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Przeczytaj podstawowe właściwości groupSettingTemplates w usłudze Azure Active Directory. |

### <a name="directory-readers"></a>Odczytywanie katalogów
Może odczytywać informacje o katalogu podstawowego. Przyznawania dostępu do aplikacji, nie przeznaczone dla użytkowników.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Przeczytaj podstawowe właściwości administrativeUnits w usłudze Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Odczytaj właściwość administrativeUnits.members w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/basic/read | Przeczytaj podstawowe właściwości aplikacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/owners/read | Odczytaj właściwość applications.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/applications/policies/read | Odczytaj właściwość applications.policies w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Basic/Read | Przeczytaj podstawowe właściwości dla kontaktów w usłudze Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Odczytaj właściwość contacts.memberOf w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/contracts/Basic/Read | Przeczytaj podstawowe właściwości dotyczące zamówień w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/basic/read | Przeczytaj podstawowe właściwości urządzeń w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Odczytaj właściwość devices.memberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Odczytaj właściwość devices.registeredOwners w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Odczytaj właściwość devices.registeredUsers w usłudze Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/basic/read | Przeczytaj podstawowe właściwości directoryRoles w usłudze Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Odczytaj właściwość directoryRoles.eligibleMembers w usłudze Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Odczytaj właściwość directoryRoles.members w usłudze Azure Active Directory. |
| microsoft.aad.directory/domains/basic/read | Przeczytaj podstawowe właściwości na temat domen w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Odczytaj właściwość groups.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/basic/read | Przeczytaj podstawowe właściwości grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Odczytaj właściwość groups.memberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/members/read | Odczytaj właściwość groups.members w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Odczytaj właściwość groups.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/settings/read | Odczytaj właściwość groups.settings w usłudze Azure Active Directory. |
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
| microsoft.aad.directory/users/basic/read | Przeczytaj podstawowe właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Odczytaj właściwość users.directReports w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Odczytaj właściwość users.manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Odczytaj właściwość users.memberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Odczytaj właściwość users.oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Odczytaj właściwość users.ownedDevices w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Odczytaj właściwość users.ownedObjects w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Odczytaj właściwość users.registeredDevices w usłudze Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Konta synchronizacji katalogu
Używane tylko przez usługę Azure AD Connect.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Zaktualizuj właściwość organization.dirSync w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Usuń zasady w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/policies/Basic/Read | Przeczytaj podstawowe właściwości zasady w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Aktualizacja właściwości podstawowe zasady w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/owners/read | Odczytaj właściwość policies.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Zaktualizuj właściwość policies.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Odczytaj właściwość policies.policiesAppliedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Zaktualizuj właściwość policies.tenantDefault w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Odczytaj właściwość servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Zaktualizuj właściwość servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Odczytaj właściwość servicePrincipals.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Zaktualizuj właściwość servicePrincipals.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Zaktualizuj właściwość servicePrincipals.audience w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Zaktualizuj właściwość servicePrincipals.authentication w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Przeczytaj podstawowe właściwości servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aktualizacja właściwości podstawowe servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Utwórz servicePrincipals w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Zaktualizuj właściwość servicePrincipals.credentials w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Odczytaj właściwość servicePrincipals.memberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Odczytaj właściwość servicePrincipals.oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Odczytaj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Zaktualizuj właściwość servicePrincipals.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Odczytaj właściwość servicePrincipals.ownedObjects w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Zaktualizuj właściwość servicePrincipals.permissions w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Odczytaj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Zaktualizuj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Wykonuje wszystkie działania w programie Azure AD Connect. |

### <a name="directory-writers"></a>Zapisywanie katalogów
Może odczytywać i zapisywać informacje katalogu podstawowego. Przyznawania dostępu do aplikacji, nie przeznaczone dla użytkowników.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/groups/create | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Tworzenie grup w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Zaktualizuj właściwość groups.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Aktualizacja podstawowe właściwości grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Zaktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Zaktualizuj właściwość groups.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Zaktualizuj właściwość groups.settings w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/update | Aktualizacja właściwości podstawowe groupSettings w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Utwórz groupSettings w usłudze Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Usuń groupSettings w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aktualizacja podstawowe właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Unieważnienie wszystkie tokeny odświeżania użytkownika w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Zaktualizuj właściwość users.manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Zaktualizuj właściwość users.userPrincipalName w usłudze Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Administrator usługi Exchange
Może zarządzać wszystkimi aspektami produktu Exchange.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Zaktualizuj właściwość groups.unified w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Zaktualizuj podstawowe właściwości grup usługi Office 365. |
| microsoft.aad.directory/groups/unified/create | Utwórz grupy usługi Office 365. |
| microsoft.aad.directory/groups/unified/delete | Usuń grupy usługi Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Zaktualizuj członkostwo grup usługi Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Zaktualizuj własności grup usługi Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.exchange/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="external-identity-provider-administrator"></a>Administrator dostawcy tożsamości zewnętrznych
Skonfiguruj dostawców tożsamości do użycia w bezpośrednich federacji.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Przeczytaj i Konfigurowanie dostawcy tożsamości w usłudze Azure Active Directory B2C. |

### <a name="guest-inviter"></a>Osoba zapraszająca gości
Może zapraszać użytkowników-gości niezależnie od "Członkowie mogą zapraszać gości" ustawienie.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Odczytaj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Przeczytaj podstawowe właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Odczytaj właściwość users.directReports w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Zaproś użytkowników gości w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Odczytaj właściwość users.manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Odczytaj właściwość users.memberOf w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Odczytaj właściwość users.oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Odczytaj właściwość users.ownedDevices w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Odczytaj właściwość users.ownedObjects w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Odczytaj właściwość users.registeredDevices w usłudze Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Administrator pomocy technicznej
Można resetować hasła dla użytkowników niebędących administratorami i administratorów pomocy technicznej.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Odczytaj właściwość devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Unieważnienie wszystkie tokeny odświeżania użytkownika w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Zaktualizuj hasła dla wszystkich użytkowników w usłudze Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="intune-service-administrator"></a>Administrator usługi Intune
Może zarządzać wszystkimi aspektami produktu Intune.

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
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Odczytaj właściwość devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/create | Tworzenie urządzenia w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/DELETE | Usuwanie urządzeń w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Zaktualizuj właściwość devices.registeredOwners w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Zaktualizuj właściwość devices.registeredUsers w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Zaktualizuj właściwość groups.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Aktualizacja podstawowe właściwości grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/create | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Tworzenie grup w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/groups/delete | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Odczytaj właściwość groups.hiddenMembers w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Zaktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Zaktualizuj właściwość groups.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Przywracanie grupy w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Zaktualizuj właściwość groups.settings w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aktualizacja podstawowe właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Zaktualizuj właściwość users.manager w usłudze Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.intune/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |

### <a name="kaizala-administrator"></a>Administrator usługi Kaizala
Można zarządzać ustawieniami dla Microsoft Kaizala.  

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >  
  
| **Akcje** | **Opis** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Centrum administracyjne usługi Office 365 odczytu. |

### <a name="license-administrator"></a>Administrator licencji
Można zarządzać licencje produktów dla użytkowników i grup.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Zaktualizuj właściwość users.usageLocation w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |

### <a name="lync-service-administrator"></a>Administrator usługi Lync
Może zarządzać wszystkimi aspektami produktu Skype dla firm.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Skype dla firm Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="message-center-privacy-reader"></a>Czytnik prywatność w Centrum wiadomości
Może odczytywać Centrum wiadomości wpisy, wiadomości prywatności danych, grup, domeny i subskrypcje.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Przeczytaj komunikaty w microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Przeczytaj securityMessages w microsoft.office365.messageCenter. |

### <a name="message-center-reader"></a>Czytnik Centrum wiadomości
Może odczytywać wiadomości i aktualizacje dla danej organizacji tylko w Centrum wiadomości usługi Office 365. 

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Przeczytaj komunikaty w microsoft.office365.messageCenter. |

### <a name="partner-tier1-support"></a>Pomoc techniczna dla partnerów (warstwa 1)
Nie używaj — nie są przeznaczone do użytku ogólnego.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| Microsoft.AAD.Directory/Contacts/Basic/Update | Aktualizacja właściwości podstawowe kontakty w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Utwórz kontakty w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/DELETE | Usuwanie kontaktów w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/create | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Tworzenie grup w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/groups/members/update | Zaktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Zaktualizuj właściwość groups.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aktualizacja podstawowe właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/delete | Usuwanie użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Unieważnienie wszystkie tokeny odświeżania użytkownika w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Zaktualizuj właściwość users.manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Zaktualizuj hasła dla wszystkich użytkowników w usłudze Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| microsoft.aad.directory/users/restore | Przywracanie usuniętych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Zaktualizuj właściwość users.userPrincipalName w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="partner-tier2-support"></a>Pomoc techniczna dla partnerów (warstwa 2)
Nie używaj — nie są przeznaczone do użytku ogólnego.

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
| microsoft.aad.directory/groups/create | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/delete | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Zaktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Przywracanie grupy w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Organization/Basic/Update | Zaktualizuj właściwości podstawowe w organizacji w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aktualizacja podstawowe właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/delete | Usuwanie użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Unieważnienie wszystkie tokeny odświeżania użytkownika w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Zaktualizuj właściwość users.manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Zaktualizuj hasła dla wszystkich użytkowników w usłudze Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| microsoft.aad.directory/users/restore | Przywracanie usuniętych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Zaktualizuj właściwość users.userPrincipalName w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="power-bi-service-administrator"></a>Administrator usługi Power BI
Może zarządzać wszystkimi aspektami produktu usługi Power BI.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Zarządzaj wszystkimi aspektami usługi Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="privileged-authentication-administrator"></a>Administrator uprzywilejowanych uwierzytelniania
Mogą wyświetlać dane, wartości i zresetować informacje dotyczące metody uwierzytelniania dla dowolnego użytkownika (administratora lub bez uprawnień administratora).

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Unieważnienie wszystkie tokeny odświeżania użytkownika w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Aktualizacja właściwości silnego uwierzytelniania, takich jak informacji o poświadczeniach usługi MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="privileged-role-administrator"></a>Administrator ról uprzywilejowanych
Mogą zarządzać przypisaniami ról w usłudze Azure AD i wszystkimi aspektami Privileged Identity Management.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów i odczytywanie i aktualizowanie właściwości standardowe w microsoft.aad.privilegedIdentityManagement. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/allTasks | Przeczytaj i skonfigurować właściwości servicePrincipals.appRoleAssignedTo w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Przeczytaj i skonfigurować właściwości servicePrincipals.oAuth2PermissionGrants w usłudze Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Tworzenie i zarządzanie jednostkami administracyjnymi (w tym elementów członkowskich) |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Tworzenie i Zarządzanie przypisaniami ról. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Tworzenie i zarządzanie nimi definicji ról. |

### <a name="reports-reader"></a>Czytnik raportów
Można odczytywać logowania i raporty inspekcji.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Odczyt wszystkich właściwości (w tym właściwości uprzywilejowanych) na auditLogs w usłudze Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Odczyt wszystkich właściwości (w tym właściwości uprzywilejowanych) na signInReports w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.usageReports/allEntities/read | Raporty użycia odczytu Office 365. |

### <a name="search-administrator"></a>Administrator wyszukiwania
Można tworzyć i zarządzać wszystkimi aspektami ustawienia Microsoft Search.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Przeczytaj komunikaty w microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Tworzenie i usunąć wszystkie zasoby, Odczyt i aktualizowania wszystkich właściwości microsoft.office365.search. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.office365.usageReports/allEntities/read | Raporty użycia odczytu Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |

### <a name="search-editor"></a>Edytor wyszukiwania
Można tworzyć i zarządzać zawartości redakcyjnej, takimi jak zakładek, pytania i jako lokalizacje, rozkład pomieszczeń.

  > [!NOTE]
  > Ta rola ma uprawnienia dodatkowy poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Przeczytaj komunikaty w microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Tworzenie i usunąć zawartość, Odczyt i aktualizowania wszystkich właściwości microsoft.office365.search. |
| microsoft.office365.usageReports/allEntities/read | Raporty użycia odczytu Office 365. |

### <a name="security-administrator"></a>Administrator zabezpieczeń
Może odczytywać informacje o zabezpieczeniach i raporty oraz zarządzać konfiguracją w usłudze Azure AD i Office 365.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Zaktualizuj właściwość applications.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Odczyt wszystkich właściwości (w tym właściwości uprzywilejowanych) na auditLogs w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Odczytaj właściwość devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Aktualizacja właściwości podstawowe zasady w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/create | Tworzenie zasad w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Usuń zasady w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Zaktualizuj właściwość policies.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Zaktualizuj właściwość policies.tenantDefault w usłudze Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Zaktualizuj właściwość servicePrincipals.policies w usłudze Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Odczyt wszystkich właściwości (w tym właściwości uprzywilejowanych) na signInReports w usłudze Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Odczyt wszystkich zasobów w microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Zaktualizuj wszystkie zasoby w microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Odczyt wszystkich zasobów w microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Odczyt wszystkich aspektów Centrum ochrony usługi Office 365. |
| microsoft.office365.protectionCenter/allEntities/update | Zaktualizuj wszystkie zasoby w microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |

### <a name="security-reader"></a>Czytelnik zabezpieczeń
Może odczytywać informacje o zabezpieczeniach i raporty w usłudze Azure AD i Office 365.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Odczyt wszystkich właściwości (w tym właściwości uprzywilejowanych) na auditLogs w usłudze Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Odczytaj właściwość devices.bitLockerRecoveryKeys w usłudze Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Odczyt wszystkich właściwości (w tym właściwości uprzywilejowanych) na signInReports w usłudze Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Odczyt wszystkich zasobów w microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Odczyt wszystkich zasobów w microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Odczyt wszystkich aspektów Centrum ochrony usługi Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |

### <a name="service-support-administrator"></a>Administrator pomocy technicznej usługi
Może odczytywać informacje o kondycji usług i zarządzania biletami pomocy technicznej.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="sharepoint-service-administrator"></a>Administrator usługi SharePoint
Może zarządzać wszystkimi aspektami usługi SharePoint.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Zaktualizuj właściwość groups.unified w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Zaktualizuj podstawowe właściwości grup usługi Office 365. |
| microsoft.aad.directory/groups/unified/create | Utwórz grupy usługi Office 365. |
| microsoft.aad.directory/groups/unified/delete | Usuń grupy usługi Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Zaktualizuj członkostwo grup usługi Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Zaktualizuj własności grup usługi Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Tworzenie i usuwanie wszystkich zasobów i odczytywanie i aktualizowanie właściwości standardowe w microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

### <a name="teams-communications-administrator"></a>Zespoły komunikacji administratora
Można zarządzać wywołania i funkcje spotkania w usłudze Microsoft Teams.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.office365.usageReports/allEntities/read | Raporty użycia odczytu Office 365. |

### <a name="teams-communications-support-engineer"></a>Ze specjalistą pomocy technicznej komunikacji zespołów
Można rozwiązywać problemy łączności z poziomu usługi Teams przy użyciu zaawansowanych narzędzi.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |

### <a name="teams-communications-support-specialist"></a>Specjalista ds. pomocy technicznej komunikacji zespołów
Można rozwiązywać problemy łączności z poziomu usługi Teams przy użyciu podstawowych narzędzi.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |

### <a name="teams-service-administrator"></a>Administrator usługi teams
Można zarządzać usługą Microsoft Teams.

  > [!NOTE]
  > Ta rola ma dodatkowe uprawnienia poza usługą Azure Active Directory. Aby uzyskać więcej informacji zobacz opis roli powyżej.
  >
  >

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Odczytaj właściwość groups.hiddenMembers w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Zaktualizuj właściwość groups.unified w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Zaktualizuj podstawowe właściwości grup usługi Office 365. |
| microsoft.aad.directory/groups/unified/create | Utwórz grupy usługi Office 365. |
| microsoft.aad.directory/groups/unified/delete | Usuń grupy usługi Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Zaktualizuj członkostwo grup usługi Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Zaktualizuj własności grup usługi Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |
| microsoft.office365.usageReports/allEntities/read | Raporty użycia odczytu Office 365. |

### <a name="user-administrator"></a>Administrator użytkowników
Może zarządzać wszystkimi aspektami użytkowników i grup, w tym resetowaniem haseł dla administratorów z ograniczonymi uprawnieniami.

| **Akcje** | **Opis** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Utwórz appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Usuń appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Zaktualizuj appRoleAssignments w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Basic/Update | Aktualizacja właściwości podstawowe kontakty w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Utwórz kontakty w usłudze Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/DELETE | Usuwanie kontaktów w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Zaktualizuj właściwość groups.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Aktualizacja podstawowe właściwości grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/create | Tworzenie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Tworzenie grup w usłudze Azure Active Directory. Twórca nie zostanie dodany jako pierwszy właściciela, a utworzony obiekt zmniejsza limit przydziału 250 utworzonych obiektów twórcy. |
| microsoft.aad.directory/groups/delete | Usuwanie grup w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Odczytaj właściwość groups.hiddenMembers w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Zaktualizuj właściwość groups.members w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Zaktualizuj właściwość groups.owners w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Przywracanie grupy w usłudze Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Zaktualizuj właściwość groups.settings w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Zaktualizuj właściwość users.appRoleAssignments w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Zarządzanie licencjami użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aktualizacja podstawowe właściwości użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/create | Tworzenie użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/delete | Usuwanie użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Unieważnienie wszystkie tokeny odświeżania użytkownika w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Zaktualizuj właściwość users.manager w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Zaktualizuj hasła dla wszystkich użytkowników w usłudze Azure Active Directory. Zobacz dokumentację online, aby uzyskać więcej szczegółów. |
| microsoft.aad.directory/users/restore | Przywracanie usuniętych użytkowników w usłudze Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Zaktualizuj właściwość users.userPrincipalName w usłudze Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Przeczytaj i konfigurowanie usługi Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej platformy Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Przeczytaj podstawowe właściwości dla wszystkich zasobów w microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Przeczytaj i skonfiguruj kondycji usługi Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Tworzenie i zarządzanie biletami pomocy technicznej usługi Office 365. |

## <a name="role-template-ids"></a>Szablon roli identyfikatorów

Szablon roli identyfikatory są używane przede wszystkim przez interfejs API programu Graph lub programu PowerShell użytkowników.

Graph displayName | Nazwa wyświetlana portalu Azure | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Administrator aplikacji | Administrator aplikacji | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Deweloper aplikacji | Deweloper aplikacji | CF1C38E5-3621-4004-A7CB-879624DCED7C
Administrator uwierzytelniania | Administrator uwierzytelniania | c4e39bd9-1100-46d3-8c65-fb160da0071f
Administrator usługi Azure Information Protection | Administrator usługi Azure Information Protection | 7495fdc4-34c4-4d15-a289-98788ce399fd
Przepływ B2C użytkownika administratora | Przepływ B2C użytkownika administratora | 6e591065-9bad-43ed-90f3-e9424366d2f0
administrator atrybutów przepływów użytkownika B2C | administrator atrybutów przepływów użytkownika B2C | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
administrator zestawu kluczy IEF B2C | administrator zestawu kluczy IEF B2C | aaf43236-0c0d-4d5f-883a-6955382ac081
administrator zasad IEF B2C | administrator zasad IEF B2C | 3edaf663-341e-4475-9f94-5c398ef6c070
Administrator rozliczeń | Administrator rozliczeń | b0f54661-2d74-4c50-afa3-1ec803f12efe
Administrator usługi Analytics pulpitu | Administrator usługi Analytics pulpitu | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Administrator aplikacji w chmurze | Administrator aplikacji w chmurze | 158c047a-c907-4556-b7ef-446551a6b5f7
Administrator urządzenia w chmurze | Administrator urządzenia w chmurze | 7698a772-787b-4ac8-901f-60d6b08affd2
Administrator firmy | Administrator globalny | 62e90394-69f5-4237-9190-012177145e10
Administrator do spraw zgodności | Administrator do spraw zgodności | 17315797-102d-40b4-93e0-432062caca18
Administrator dostępu warunkowego | Administrator dostępu warunkowego | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Administrator usługi CRM | Dynamics 365 administratora | 44367163-eba1-44c3-98af-f5787879f96a
Osoba zatwierdzająca dostęp do skrytki klienta | Osoba zatwierdzająca dostęp do skrytki klienta | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Administratorzy urządzenia | Administratorzy urządzenia | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Dołączanie urządzeń | Dołączanie urządzeń | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Menedżerowie urządzeń | Menedżerowie urządzeń | 2b499bcd-da44-4968-8aec-78e1674fa64d
Użytkownicy urządzeń | Użytkownicy urządzeń | d405c6df-0af8-4e3b-95e4-4d06e542189e
Odczytywanie katalogów | Odczytywanie katalogów | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Konta synchronizacji katalogu | Konta synchronizacji katalogu | d29b2b05-8046-44ba-8758-1e26182fcf32
Zapisywanie katalogów | Zapisywanie katalogów | 9360feb5-f418-4baa-8175-e2a00bac4301
Administrator usługi Exchange | Administrator programu Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Administrator dostawcy tożsamości zewnętrznych | Administrator dostawcy tożsamości zewnętrznych | be2f45a1-457d-42af-a067-6ec1fa63bc45
Osoba zapraszająca gości | Osoba zapraszająca gości | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Administrator pomocy technicznej | Administrator haseł | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Administrator usługi Intune | Administrator usługi Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Administrator usługi Kaizala | Administrator usługi Kaizala | 74ef975b-6605-40af-a5d2-b9539d836353
Administrator licencji | Administrator licencji | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Administrator usługi Lync | Skype dla firm administratora | 75941009-915a-4869-abe7-691bff18279e
Czytnik prywatność w Centrum wiadomości | Czytnik zachowania Centrum wiadomości | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Czytnik Centrum wiadomości | Czytnik Centrum wiadomości | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Pomoc techniczna dla partnerów (warstwa 1) | Pomoc techniczna dla partnerów 1 | 4ba39ca4-527c-499a-b93d-d9b492c50246
Pomoc techniczna dla partnerów (warstwa 2) | Pomoc techniczna dla partnerów w warstwie 2 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Administrator usługi Power BI | Power BI administrator | a9ea8996-122f-4c74-9520-8edcd192826c
Administrator uprzywilejowanych uwierzytelniania | Administrator uwierzytelniania uprzywilejowanego | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Administrator ról uprzywilejowanych | Administrator roli uprzywilejowanej | e8611ab8-c189-46e8-94e1-60213ab1f814
Czytnik raportów | Czytnik raportów | 4a5d8f65-41da-4de4-8968-e035b65339cf
Administrator wyszukiwania | Administrator wyszukiwania | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Edytor wyszukiwania | Edytor wyszukiwania | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Administrator zabezpieczeń | Administrator zabezpieczeń | 194ae4cb-b126-40b2-bd5b-6091b380977d
Czytelnik zabezpieczeń | Czytelnik zabezpieczeń | 5d6b6bb7-de71-4623-b4af-96380a352509
Administrator pomocy technicznej usługi | Administrator usługi | f023fd81-a637-4b56-95fd-791ac0226033
Administrator usługi SharePoint | Administrator programu SharePoint | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Zespoły komunikacji administratora | Zespoły komunikacji administratora | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Ze specjalistą pomocy technicznej komunikacji zespołów | Ze specjalistą pomocy technicznej komunikacji zespołów | f70938a0-fc10-4177-9e90-2178f8765737
Specjalista ds. pomocy technicznej komunikacji zespołów | Specjalista ds. pomocy technicznej komunikacji zespołów | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Administrator usługi teams | Administrator usługi teams | 69091246-20e8-4a56-aa4d-066075b2a7a8
Użytkownik | Użytkownik | a0b1b346-4d3e-4e8b-98f8-753987be4970
Administrator kont użytkowników | Administrator użytkowników | fe930be7-5e62-47db-91af-98c3a49a38b1
Dołączanie urządzeń | Dołączanie urządzeń | c34f683f-4d5a-4403-affd-6615e00e3a7f


## <a name="deprecated-roles"></a>Przestarzałe ról

Nie można używać następujących ról. One są przestarzałe i zostaną usunięte z usługi Azure AD w przyszłości.

* Administrator licencji ad hoc
* Dołączanie urządzeń
* Menedżerowie urządzeń
* Użytkownicy urządzeń
* Tworzenie użytkowników zweryfikowanych poczty e-mail
* Administrator skrzynki pocztowej
* Dołączanie urządzeń

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat sposobu przypisywania użytkownika jako administratora subskrypcji platformy Azure, zobacz [zarządzanie dostępem przy użyciu RBAC i witryny Azure portal](../../role-based-access-control/role-assignments-portal.md)
* Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).
* Aby uzyskać więcej informacji dotyczących relacji między usługą Azure Active Directory i subskrypcją platformy Azure, zobacz [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
