---
title: Przypisywanie ról administratorów w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Rolę administratora można dodać użytkowników, Przypisz role administracyjne, resetowanie haseł użytkowników, zarządzanie licencjami użytkowników lub zarządzania domenami. Użytkownik, któremu przypisano rolę administratora ma te same uprawnienia we wszystkich usługach chmury, do których zostały zasubskrybowane przez organizację.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 06/04/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 94062c69a1de32d061b97bbaae0d7028b0424feb
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757676"
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Przypisywanie ról administratorów w usłudze Azure Active Directory

Za pomocą usługi Azure Active Directory (Azure AD), możesz określić osobne administratorom różne funkcje. Administratorzy mają dostęp do różnych funkcji w portalu Azure i, w zależności od ich roli, mogą tworzyć lub Edytuj użytkowników, przypisywanie ról administracyjnych do innych użytkowników, resetowania haseł użytkowników, zarządzanie licencjami użytkowników i zarządzanie domenami, między innymi. Użytkownik, któremu przypisano rolę administratora będą mają te same uprawnienia we wszystkich usług chmury, do których zostały zasubskrybowane przez organizację, niezależnie od tego, czy przypisanie roli w portalu usługi Office 365 lub w portalu Azure lub za pomocą modułu Azure AD dla Środowisko Windows PowerShell.

## <a name="details-about-the-global-administrator-role"></a>Szczegółowe informacje o roli administratora globalnego
Administrator globalny ma dostęp do wszystkich funkcji administracyjnych. Domyślnie osoba, która zarejestruje się w celu uzyskania subskrypcji platformy Azure przypisany do roli administratora globalnego dla katalogu. Tylko administratorzy globalni mogą przypisywać pozostałe role administratorów.

## <a name="assign-or-remove-administrator-roles"></a>Przypisywanie lub usuwanie ról administratora
Aby dowiedzieć się, jak przypisywanie ról administracyjnych do użytkownika w usłudze Azure Active Directory, zobacz [przypisać użytkownika do ról administratora w usłudze Azure Active Directory](active-directory-users-assign-role-azure-portal.md).

## <a name="available-roles"></a>Dostępne role
Dostępne są następujące role administratora:

* **Administrator rozliczeń**: dokonuje zakupów, zarządza subskrypcjami, zarządza biletami pomocy technicznej i monitoruje kondycję usługi.

* **Administrator zgodności**: użytkownicy z tą rolą mają uprawnienia zarządzania w ramach pakietu Office 365 zabezpieczeń & Centrum zgodności i Centrum administracyjnego programu Exchange. Więcej informacji na "[o rolach administratora usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)."

* **Administrator dostępu warunkowego**: użytkownicy z tą rolą mieć możliwość zarządzania ustawieniami dostępu warunkowego do usługi Azure Active Directory.
  > [!NOTE]
  > Aby wdrożyć zasady dostępu warunkowego programu Exchange ActiveSync na platformie Azure, użytkownik musi być administratorem globalnym.
  
* **Administrator Dynamics 365**: użytkownicy z tą rolą mają uprawnienia globalne w ramach programu Microsoft Dynamics 365, gdy usługa jest obecne, a także możliwość Zarządzanie biletami pomocy technicznej i monitoruje kondycję usługi. Więcej informacji na [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administratorzy urządzenia**: użytkownicy z tą rolą stają się Administratorzy komputera lokalnego na wszystkich urządzeniach z systemem Windows 10, które dołączyły do usługi Azure Active Directory. Nie mają możliwość zarządzania obiektami urządzeń w usłudze Azure Active Directory.

* **Czytniki katalogu**: jest to starszy rolę, która ma być przypisana do aplikacji, które nie obsługują [Framework zgody](active-directory-integrating-applications.md). Nie należy można przypisać do użytkowników.

* **Konta synchronizacji katalogu**: nie używaj. Ta rola zostanie automatycznie przypisany do usługi Azure AD Connect i jest nie przeznaczone lub obsługiwane do innego użytku.

* **Składniki zapisywania katalogu**: jest to starszy rolę, która ma być przypisana do aplikacji, które nie obsługują [zgody Framework](active-directory-integrating-applications.md). Nie należy można przypisać do użytkowników.

* **Administrator usługi Exchange**: użytkownicy z tą rolą uprawnień globalnych w ramach programu Microsoft Exchange Online, gdy usługa jest obecny. Więcej informacji na [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrator globalny / Administrator firmy**: użytkownicy z tej roli mają dostęp do wszystkich funkcji administracyjnych w usłudze Azure Active Directory, a także usług, które Federację do usługi Azure Active Directory, takie jak Exchange Online, SharePoint Online i Skype dla firm Online. Osoba, która zarejestruje się dla dzierżawy usługi Azure Active Directory staje się administratorem globalnym. Tylko administratorzy globalni mogą przypisywać pozostałe role administratorów. Może istnieć więcej niż jeden administrator globalny w firmie. Administratorzy globalni mogą resetować hasła dla każdego użytkownika i innych administratorów.

  > [!NOTE]
  > W interfejsu API Graph usługi Microsoft Azure AD Graph API i Azure AD PowerShell ta rola jest identyfikowane jako "Administrator firmy". "Administrator globalny" znajduje się w [portalu Azure](https://portal.azure.com).
  >
  >

* **Gość zapraszającej**: użytkownicy w tej roli mogą zarządzać użytkownika zaproszeń do skorzystania z usługi Azure Active Directory B2B gościa, gdy ustawienia użytkownika "Zaprosić elementy członkowskie" jest ustawiona na nie. Więcej informacji na temat współpracy B2B w [współpracy dotyczące usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nie ma inne uprawnienia.

* **Administrator ochrony informacji**: użytkownicy z tą rolą mają uprawnienia tylko w usłudze Azure Information Protection. Nie są przyznawane prawa użytkownika w Centrum ochrony tożsamości, Zarządzanie tożsamościami uprzywilejowanymi, Monitor Office 365 usługi kondycji lub Office 365 zabezpieczeń i Centrum zgodności. Ich konfigurowanie etykiety dla zasad usługi Azure Information Protection, Zarządzanie szablonami ochrony i aktywować ochrony.

* **Administrator usługi Intune**: użytkownicy z tą rolą uprawnień globalnych w ramach programu Microsoft Intune Online, gdy usługa jest obecny. Ponadto ta rola zawiera zdolność do zarządzania użytkownikami i urządzeniami, aby skojarzyć zasady, oraz tworzenie grup i zarządzanie nimi.

* **Skrzynki pocztowej administratora**: Ta rola jest używana tylko w ramach usługi Exchange Online obsługa poczty e-mail dla urządzeń Blackberry KRAWĘDZI. Jeśli Twoja organizacja nie korzysta z poczty e-mail usługi Exchange Online urządzeń Blackberry KRAWĘDZI, nie należy używać tej roli.

* **Czytnik Centrum wiadomości**: użytkowników w tej roli można monitorować powiadomienia i aktualizacje advisory kondycji w [Centrum Office 365 wiadomości](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) organizacji na skonfigurowanych usług, takich jak Exchange, Intune i Microsoft Zespoły. Czytniki Centrum wiadomości otrzymywać cotygodniowe roztwór e-mail wpisów, aktualizacji i może współużytkować wpisów Centrum wiadomości w usłudze Office 365. W usłudze Azure AD użytkownicy przypisani do tej roli będzie mieć tylko dostęp tylko do odczytu w usług Azure AD, takich jak użytkownicy i grupy. 

* **Warstwa obsługi 1 partnera**: nie używaj. Ta rola jest przestarzała i zostanie usunięte z usługi Azure AD w przyszłości. Ta rola jest przeznaczony dla niewielkiej liczby partnerów odsprzedaż firmy Microsoft i nie jest przeznaczona do użytku ogólnego.

* **Warstwy 2 w ramach partnera**: nie używaj. Ta rola jest przestarzała i zostanie usunięte z usługi Azure AD w przyszłości. Ta rola jest przeznaczony dla niewielkiej liczby partnerów odsprzedaż firmy Microsoft i nie jest przeznaczona do użytku ogólnego.

* **Administrator haseł / Administrator pomocy technicznej**: użytkownicy z tą rolą mogą zmiany haseł, zarządzanie żądaniami obsługi i monitoruje kondycję usługi. Pomoc techniczna administrator może zmienić hasła wyłącznie użytkowników i innych administratorów pomocy technicznej. 

  > [!NOTE]
  > W interfejsu API Graph usługi Microsoft Azure AD Graph API i Azure AD PowerShell ta rola jest identyfikowane jako "Administrator pomocy technicznej". "Hasło administratora" znajduje się w [portalu Azure](https://portal.azure.com/).
  >
  >
  
* **Administrator usługi Power BI**: użytkownicy z tą rolą mają uprawnienia globalne w ramach usługi Microsoft Power BI, gdy usługa jest obecne, a także możliwość Zarządzanie biletami pomocy technicznej i monitoruje kondycję usługi. Więcej informacji na [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-001&ad=US).

* **Uprzywilejowany roli administratora**: użytkownicy z tą rolą mogą zarządzać przypisań ról w usłudze Azure Active Directory, a także w usłudze Azure AD Privileged Identity Management. Ponadto ta rola umożliwia zarządzanie wszystkich aspektów Privileged Identity Management.

* **Raporty czytnika**: użytkownicy z tą rolą mogą wyświetlać użycia raportowania danych i raportów pulpitu nawigacyjnego w Centrum administracyjnym usługi Office 365 i kontekst przyjęcia pakietu w usługi Power BI. Ponadto roli zapewnia dostęp do logowania jednokrotnego raporty i działania w usłudze Azure AD i interfejsem API raportowania danych zwróconych przez program Microsoft Graph. Użytkownicy przypisani do roli Czytelnik raporty dostępne tylko odpowiednie użycia i przyjęcia metryki. Nie ma uprawnień administratora do skonfigurowania ustawień lub dostępu do Centrum administracyjnego określonego produktu, takich jak program Exchange. 

* **Administrator zabezpieczeń**: użytkownicy z tą rolą mają wszystkie uprawnienia tylko do odczytu z czytnika rolę zabezpieczeń, a także możliwość zarządzania konfiguracją dla usług związanych z zabezpieczeniami: Azure Active Directory Identity Protection, platforma Azure Information Protection, Zarządzanie tożsamościami uprzywilejowanymi oraz zabezpieczeń usługi Office 365 i Centrum zgodności. Więcej informacji na temat uprawnień usługi Office 365 są dostępne pod adresem [uprawnień w programie Office 365 zabezpieczeń & Centrum zgodności](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Czytnik zabezpieczeń**: użytkownicy z tą rolą mają globalny dostęp tylko do odczytu, w tym wszystkie informacje w usłudze Azure Active Directory, ochronę tożsamości, Privileged Identity Management, a także pozwala na odczyt raportów logowania w usłudze Azure Active Directory i dzienniki inspekcji. Rola przyznaje również uprawnienia tylko do odczytu w programie Office 365 zabezpieczeń & Centrum zgodności. Więcej informacji na temat uprawnień usługi Office 365 są dostępne pod adresem [uprawnień w programie Office 365 zabezpieczeń & Centrum zgodności](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Administrator obsługuje usługi**: użytkownicy z tą rolą mogą otwierać żądania pomocy technicznej z firmą Microsoft dla usług Azure i usługi Office 365 i widoki pulpitu nawigacyjnego usługi i komunikat Centrum w portalu Azure i portalu administracyjnego usługi Office 365. Więcej informacji na [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrator usługi SharePoint**: użytkownicy z tą rolą mają uprawnienia globalne w ramach programu Microsoft SharePoint Online, gdy usługa jest obecne, a także możliwość Zarządzanie biletami pomocy technicznej i monitoruje kondycję usługi. Więcej informacji na [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Skype dla firm / Administrator usługi Lync**: użytkownicy z tą rolą uprawnień globalnych w Microsoft Skype dla firm, gdy usługa jest obecne, a także zarządzać specyficzne dla usługi Skype atrybutów użytkownika w usłudze Azure Active Directory. Ponadto ta rola daje możliwość Zarządzanie biletami pomocy technicznej i monitoruje kondycję usługi. Więcej informacji na [ról administratora o usługi Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  > [!NOTE]
  > W interfejsu API Graph usługi Microsoft Azure AD Graph API i Azure AD PowerShell ta rola jest identyfikowane jako "Administratora usługi Lync". ""Skype dla firm usługi Administrator znajduje się w [portalu Azure](https://portal.azure.com/).
  >
  >

* **Administrator konta użytkownika**: użytkownicy z tą rolą mogą tworzyć i zarządzać wszystkimi aspektami użytkowników i grup. Ponadto ta rola obejmuje możliwość Zarządzanie biletami pomocy technicznej i monitoruje usługi kondycji. Niektóre ograniczenia są stosowane. Na przykład ta rola nie zezwala na usuwanie administratora globalnego. Administratorzy konta użytkownika, można zmienić hasła dla użytkowników, administratorów pomocy technicznej i innych administratorów konta użytkownika.

## <a name="administrator-permissions"></a>Uprawnienia administratora

### <a name="billing-administrator"></a>Administrator rozliczeń

| Możliwość | Nie można wykonać |
| --- | --- |
|<p>Wyświetlanie informacji o firmy i użytkownika</p><p>Zarządzanie biletami pomocy technicznej pakietu Office</p><p>Wykonywanie operacji rozliczeń i zakupów dla produktów pakietu Office</p> |<p>Resetowanie haseł użytkowników</p><p>Tworzenie i zarządzanie widokami użytkownika</p><p>Tworzenie, edycję, usuwanie użytkowników i grup i zarządzanie licencjami użytkowników</p><p>Zarządzanie domenami</p><p>Zarządzanie informacjami o firmy</p><p>Delegowanie ról administracyjnych do innych użytkowników</p><p>Używanie synchronizacji katalogów</p><p>Wyświetlanie dzienników inspekcji</p>|

### <a name="conditional-access-administrator"></a>Administrator dostępu warunkowego

| Możliwość | Nie można wykonać |
| --- | --- |
|<p>Wyświetlanie informacji o firmy i użytkownika</p><p>Zarządzanie ustawieniami dostępu warunkowego</p> |<p>Resetowanie haseł użytkowników</p><p>Tworzenie i zarządzanie widokami użytkownika</p><p>Tworzenie, edycję, usuwanie użytkowników i grup i zarządzanie licencjami użytkowników</p><p>Zarządzanie domenami</p><p>Zarządzanie informacjami o firmy</p><p>Delegowanie ról administracyjnych do innych użytkowników</p><p>Używanie synchronizacji katalogów</p><p>Wyświetlanie dzienników inspekcji</p>|

### <a name="global-administrator"></a>Administrator globalny
| Możliwość | Nie można wykonać |
| --- | --- |
|<p>Wyświetlanie informacji o firmy i użytkownika</p><p>Zarządzanie biletami pomocy technicznej pakietu Office</p><p>Wykonywanie operacji rozliczeń i zakupów dla produktów pakietu Office</p><p>Resetowanie haseł użytkowników</p><p>Resetowanie haseł innym administratorom</p> <p>Tworzenie i zarządzanie widokami użytkownika</p><p>Tworzenie, edycję, usuwanie użytkowników i grup i zarządzanie licencjami użytkowników</p><p>Zarządzanie domenami</p><p>Zarządzanie informacjami o firmy</p><p>Delegowanie ról administracyjnych do innych użytkowników</p><p>Używanie synchronizacji katalogów</p><p>Włącz lub Wyłącz uwierzytelnianie wieloskładnikowe</p><p>Wyświetlanie dzienników inspekcji</p> |ND |

### <a name="password-administrator--helpdesk-administrator"></a>Administrator haseł / Administrator pomocy technicznej
| Możliwość | Nie można wykonać |
| --- | --- |
| <p>Wyświetlanie informacji o firmy i użytkownika</p><p>Zarządzanie biletami pomocy technicznej pakietu Office</p><p>Zmienianie haseł dla użytkowników i innych administratorów pomocy technicznej</p>|<p>Wykonywanie operacji rozliczeń i zakupów dla produktów pakietu Office</p><p>Tworzenie i zarządzanie widokami użytkownika</p><p>Tworzenie, edycję, usuwanie użytkowników i grup i zarządzanie licencjami użytkowników</p><p>Zarządzanie domenami</p><p>Zarządzanie informacjami o firmy</p><p>Delegowanie ról administracyjnych do innych użytkowników</p><p>Używanie synchronizacji katalogów</p><p>Wyświetlanie raportów</p>|

### <a name="information-protection-administrator"></a>Administrator usługi Information Protection
W | Możliwość
-------- | ---------
Azure Information Protection | <li>Konfigurowanie ustawień i etykiet w zasadach globalne i zakresami<li>Konfigurowanie i Zarządzanie szablonami ochrony<li>Aktywować lub dezaktywować ochrona —
 
### <a name="reports-reader"></a>Czytnik raportów 
Możliwość | Nie można wykonać
------ | ----------
Widok usługi Azure AD logowania raporty i dzienniki inspekcji<br>Wyświetlanie informacji o firmy i użytkownika<br>Dostęp do pulpitu nawigacyjnego z użycia usługi Office 365 | Tworzenie i zarządzanie widokami użytkownika<br>Tworzenie, edycję, usuwanie użytkowników i grup i zarządzanie licencjami użytkowników<br>Delegowanie ról administracyjnych do innych użytkowników<br>Zarządzanie informacjami o firmy

### <a name="security-reader"></a>Odczytywanie zabezpieczeń
| W | Możliwość |
| --- | --- |
| Centrum usługi Identity Protection |Wszystkie raporty dotyczące zabezpieczeń i informacje o funkcjach zabezpieczeń ustawienia do odczytu<ul><li>Antyspamowy<li>Szyfrowanie<li>Zapobieganie utracie danych<li>Przed złośliwym oprogramowaniem<li>Advanced threat protection<li>Przed wyłudzaniem informacji<li>Reguły Mailflow |
| Privileged Identity Management |<p>Ma dostęp tylko do odczytu do wszystkich informacji udostępniane w usłudze Azure AD PIM: zasady i raporty dla usługi Azure AD przypisań ról zabezpieczeń monitoruje i w przyszłości dostęp do odczytu danych zasad i raporty dla scenariuszy oprócz przypisania roli usługi Azure AD.<p>**Nie można** utworzyć konto usługi Azure AD PIM lub wprowadzać żadnych zmian. W portalu firmy usługi PIM lub za pomocą programu PowerShell ktoś jest w tej roli może aktywować dodatkowych ról (na przykład administrator globalny lub Administrator ról uprzywilejowanych), jeśli użytkownik jest kandydatem do nich. |
| <p>Kondycja usługi Office 365 monitora</p><p>Bezpieczeństwo usługi Office 365 i Centrum zgodności</p> |<ul><li>Przeczytaj alerty i zarządzaj nimi<li>Przeczytaj zasady zabezpieczeń<li>Odczytywanie analizy zagrożeń, Cloud App Discovery i kwarantanny w obszarze wyszukiwania i Zbadaj<li>Odczyt wszystkich raportów |

### <a name="security-administrator"></a>Administrator zabezpieczeń
| W | Możliwość |
| --- | --- |
| Centrum usługi Identity Protection |<ul><li>Wszystkie uprawnienia roli zabezpieczeń czytnika.<li>Ponadto możliwość wykonywać wszystkie operacje IPC z wyjątkiem resetowanie haseł. |
| Privileged Identity Management |<ul><li>Wszystkie uprawnienia roli zabezpieczeń czytnika.<li>**Nie można** zarządzania członkostwa w roli usługi Azure AD lub ustawienia. |
| <p>Kondycja usługi Office 365 monitora</p><p>Bezpieczeństwo usługi Office 365 i Centrum zgodności |<ul><li>Wszystkie uprawnienia roli zabezpieczeń czytnika.<li>Można skonfigurować wszystkie ustawienia w funkcji Advanced Threat Protection (ochrony przed złośliwym oprogramowaniem i wirusami, złośliwy konfiguracji adresu URL, adres URL śledzenia itp.). |

### <a name="service-administrator"></a>Administrator usługi
| Możliwość | Nie można wykonać |
| --- | --- |
| <p>Wyświetlanie informacji o firmy i użytkownika</p><p>Zarządzanie biletami pomocy technicznej pakietu Office</p> |<p>Resetowanie haseł użytkowników</p><p>Wykonywanie operacji rozliczeń i zakupów dla produktów pakietu Office</p><p>Tworzenie i zarządzanie widokami użytkownika</p><p>Tworzenie, edycję, usuwanie użytkowników i grup i zarządzanie licencjami użytkowników</p><p>Zarządzanie domenami</p><p>Zarządzanie informacjami o firmy</p><p>Delegowanie ról administracyjnych do innych użytkowników</p><p>Używanie synchronizacji katalogów</p><p>Wyświetlanie dzienników inspekcji</p> |

### <a name="user-account-administrator"></a>Administrator kont użytkowników
| Możliwość | Nie można wykonać |
| --- | --- |
| <p>Wyświetlanie informacji o firmy i użytkownika</p><p>Zarządzanie biletami pomocy technicznej pakietu Office</p><p>Zmienianie haseł dla użytkowników, administratorów pomocy technicznej i innych administratorów konta użytkownika</p><p>Tworzenie i zarządzanie widokami użytkownika</p><p>Tworzenie, edycję, usuwanie użytkowników i grup i zarządzanie licencjami użytkowników z ograniczeniami. Użytkownik nie można usuwać administratorów globalnych ani tworzyć innych administratorów.</p> |<p>Wykonywanie operacji rozliczeń i zakupów dla produktów pakietu Office</p><p>Zarządzanie domenami</p><p>Zarządzanie informacjami o firmy</p><p>Delegowanie ról administracyjnych do innych użytkowników</p><p>Używanie synchronizacji katalogów</p><p>Włącz lub Wyłącz uwierzytelnianie wieloskładnikowe</p><p>Wyświetlanie dzienników inspekcji</p> |

### <a name="to-add-a-user-as-a-global-administrator"></a>Aby dodać użytkownika jako administrator globalny

1. Zaloguj się do [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu konta administratora globalnego dla katalogu dzierżawcy.

   ![Otwieranie Centrum administracyjnego usługi azure AD](./media/active-directory-assign-admin-roles-azure-portal/active-directory-admin-center.png)

2. Wybierz **użytkowników i grup &gt; wszyscy użytkownicy**

3. Znajdź użytkownika, które chcesz wyznaczyć jako Administrator globalny i otwórz blok dla tego użytkownika.

4. W bloku użytkownika, wybierz **roli katalogu**.
 
5. W bloku roli katalogu, wybierz **administratora globalnego** roli i Zapisz.

## <a name="deprecated-roles"></a>Przestarzałe ról

Nie można używać następujących ról. One zostały przestarzałe i zostanie usunięte z usługi Azure AD w przyszłości.

* Administrator licencji ad hoc
* Tworzenie użytkowników zweryfikowanych za pośrednictwem poczty e-mail
* Dołącz do urządzenia
* Menedżerowie urządzenia
* Użytkownicy urządzeń
* Dołączanie urządzeń w miejscu pracy

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej o modyfikowaniu administratorów subskrypcji platformy Azure, zobacz [How to add or change Azure administrator roles](../billing-add-change-azure-subscription-administrator.md) (Jak dodać lub zmienić role administratora platformy Azure).
* Aby dowiedzieć się więcej o kontrolowaniu dostępu do zasobów na platformie Microsoft Azure, zobacz [Understanding resource access in Azure](../role-based-access-control/rbac-and-directory-admin-roles.md) (Opis dostępu do zasobów na platformie Azure).
* Aby uzyskać więcej informacji dotyczących sposobu usługi Azure Active Directory odnosi się do subskrypcji platformy Azure, zobacz [jak subskrypcje platformy Azure są kojarzone z usługi Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Zarządzanie użytkownikami](active-directory-create-users.md)
* [Zarządzanie hasłami](active-directory-manage-passwords.md)
* [Zarządzanie grupami](active-directory-manage-groups.md)
