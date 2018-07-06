---
title: Usługa Azure Active Directory dowód bloki konstrukcyjne elementu playbook pojęcia | Dokumentacja firmy Microsoft
description: Eksploruj i szybkie Implementowanie scenariuszy zarządzania tożsamościami i dostępem
services: active-directory
keywords: Usługa Azure active directory, elementu playbook, weryfikacja koncepcji, weryfikacja koncepcji
documentationcenter: ''
author: dstefanMSFT
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: dstefan
ms.openlocfilehash: d2a63a1a9b335b7765c5eaf8c90e1d755b2ce9c9
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867124"
---
# <a name="azure-active-directory-proof-of-concept-playbook-building-blocks"></a>Usługa Azure Active Directory weryfikacji koncepcji Podręcznik: bloki konstrukcyjne

## <a name="catalog-of-roles"></a>Role katalogu

| Rola | Opis | Weryfikacja koncepcji (PoC) odpowiedzialności |
| --- | --- | --- |
| **Architektura tożsamości / zespół programistyczny** | Ten zespół jest zazwyczaj projektuje rozwiązania, który implementuje prototypy, dyski zatwierdzenia i na koniec przekazywało operacji | Zapewniają środowiska, a te ocenia różne scenariusze z punktu widzenia możliwości zarządzania |
| **Zespół operacyjny tożsamości lokalnych** | Zarządza tożsamości w różnych magazynach lokalnych: lasy usługi Active Directory, katalogów LDAP, systemy Kadrowe i dostawców tożsamości federacyjnych. | Zapewniają dostęp do lokalnych zasobów potrzebnych do scenariuszy weryfikacji koncepcji.<br/>One powinny być wykonywane jak najmniejszy|
| **Techniczne właścicieli aplikacji** | Techniczne właścicieli aplikacji inną chmurę i usług, zintegrowanych z usługą Azure AD | Podawanie szczegółów dotyczących aplikacji SaaS (potencjalnie wystąpień do testowania) |
| **Administrator globalny usługi Azure AD** | Zarządza konfiguracją usługi Azure AD | Podaj poświadczenia, aby skonfigurować usługi synchronizacji. Zazwyczaj takie same zespołu jako architektura tożsamości podczas weryfikacji koncepcji, ale niezależnie podczas fazy działania|
| **Zespół bazy danych** | Właściciele infrastrukturą bazy danych | Zapewniają dostęp do środowiska SQL (usług AD FS lub Azure AD Connect) dla konkretnego scenariusza przygotowań.<br/>One powinny być wykonywane jak najmniejszy |
| **Zespół sieci** | Właściciele infrastruktury sieci | Podaj wymagane prawa dostępu na poziomie sieci na serwerach synchronizacji prawidłowo dostęp do źródeł danych i cloud services (reguły zapory, otwierane porty, reguły protokołu IPSec itp.) |
| **Zespół ds. zabezpieczeń** | Definiuje strategii zabezpieczeń, analizuje raporty dotyczące zabezpieczeń z różnych źródeł i następuje za pośrednictwem na wyniki. | Zapewnić bezpieczeństwo docelowej scenariuszy oceny |

## <a name="common-prerequisites-for-all-building-blocks"></a>Wspólne wymagania wstępne dla wszystkich bloków konstrukcyjnych

Poniżej przedstawiono kilka wymagań wstępnych dla dowolnego POC za pomocą usługi Azure AD Premium.

| Wymagania wstępne | Zasoby |
| --- | --- |
| Dzierżawy usługi Azure AD zdefiniowane za pomocą ważnej subskrypcji platformy Azure | [Jak uzyskać dzierżawę usługi Azure Active Directory](active-directory-howto-tenant.md)<br/>**Uwaga:** Jeśli masz już środowisko z licencji usługi Azure AD Premium, można uzyskać zero limit subskrypcji, przechodząc do węzła https://aka.ms/accessaad <br/>Dowiedz się więcej o: https://blogs.technet.microsoft.com/enterprisemobility/2016/02/26/azure-ad-mailbag-azure-subscriptions-and-azure-ad-2/ i https://technet.microsoft.com/library/dn832618.aspx |
| Domen definiowane i zweryfikowane | [Dodawanie niestandardowej nazwy domeny do usługi Azure Active Directory](active-directory-domains-add-azure-portal.md)<br/>**Uwaga:** niektórych obciążeń, takich jak usługa Power BI można aprowizowaniu dzierżawy usługi azure AD w sposób niewidoczny. Aby sprawdzić, czy skojarzona z dzierżawą danej domeny, przejdź do https://login.microsoftonline.com/{domain}/v2.0/.well-known/openid-configuration. Jeśli pobieranie pomyślnej odpowiedzi, a następnie domena jest już przypisany do dzierżawy i przejęcia mogą być wymagane. Jeśli tak, aby uzyskać dalsze wskazówki kontakt z firmą Microsoft. Więcej informacji na temat opcji przejęcia u: [co to jest Samoobsługowa na platformie Azure?](users-groups-roles/directory-self-service-signup.md) |
| Usługa Azure AD Premium lub EMS włączone wersji próbnej | [Usługa Azure Active Directory — wersja Premium bezpłatnie na jeden miesiąc](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Aby zapewnić użytkownikom przypisano licencje pakietu EMS lub Azure AD Premium | [Licencja sobie i użytkownikom w usłudze Azure Active Directory](active-directory-licensing-get-started-azure-portal.md) |
| Poświadczenia administratora globalnego usługi AD platformy Azure | [Przypisywanie ról administratorów w usłudze Azure Active Directory](users-groups-roles/directory-assign-admin-roles.md) |
| Opcjonalne, ale zdecydowanie zalecane: środowisko laboratoryjne równoległe jako rezerwowe | [Prerequisites for Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) (Wymagania wstępne programu Azure AD Connect) |

## <a name="directory-synchronization---password-hash-sync-phs---new-installation"></a>Katalog synchronizacji — synchronizacja skrótów haseł (wersji) — nowa instalacja

Przybliżony czas wykonania: dopiero po godzinie mniej niż 1000 użytkowników weryfikacji koncepcji

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Serwer, aby uruchomić usługę Azure AD Connect | [Prerequisites for Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) (Wymagania wstępne programu Azure AD Connect) |
| Skieruj użytkowników weryfikacji Koncepcji, w tej samej domenie i częścią grupy zabezpieczeń i jednostki Organizacyjnej | [Niestandardowa instalacja programu Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) |
| Azure AD Connect funkcje służące do weryfikacji Koncepcji są identyfikowane | [Łączenie usługi Active Directory z usługą Azure Active Directory — Konfigurowanie synchronizacji funkcji](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Masz wymagane poświadczenia dla lokalnych i środowiskach w chmurze  | [Azure AD Connect: Accounts and permissions](./connect/active-directory-aadconnect-accounts-permissions.md) (Azure AD Connect: konta i uprawnienia) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Pobierz najnowszą wersję programu Azure AD Connect | [Pobierz platformy Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) |
| Zainstaluj program Azure AD Connect z to najprostsza ścieżka: Express <br/>1. Filtruj do docelowej jednostki Organizacyjnej, aby zminimalizować czas cykl synchronizacji<br/>2. Wybierz docelowy zestaw użytkowników w grupie w środowisku lokalnym.<br/>3. Wdrażanie funkcji wymaganych przez inne motywy weryfikacji Koncepcji | [Usługa Azure AD Connect: Instalacja niestandardowa: domenę i jednostkę Organizacyjną filtrowania](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) <br/>[Usługa Azure AD Connect: Instalacja niestandardowa: filtrowaniu na podstawie grupy](./connect/active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)<br/>[Azure AD Connect: Integrowanie tożsamości lokalnych z usługą Azure Active Directory: Konfigurowanie funkcji synchronizacji](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Otwarcie usługi Azure AD Connect interfejsu użytkownika i zobacz, uruchamianie profilów ukończone (importowania, synchronizacji i eksportowanie) | [Synchronizacja programu Azure AD Connect: Harmonogram](./connect/active-directory-aadconnectsync-feature-scheduler.md) |
| Otwórz [portalu zarządzania usługi Azure AD](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/), przejdź do bloku "Wszyscy użytkownicy", Dodaj kolumny "Źródło własności" i przekonać się, że użytkownicy są wyświetlane, prawidłowo oznaczone jako pochodzący od "Windows Server AD" | [Portal zarządzania usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) |

### <a name="considerations"></a>Zagadnienia do rozważenia

1. Przyjrzyj się zagadnienia dotyczące zabezpieczeń z synchronizacją skrótów haseł [tutaj](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).  Jeśli nie opcję synchronizacji skrótów haseł dla użytkowników pilotażowych produkcyjnych jest ostatecznie, rozważ następujących alternatyw:
   * Utwórz użytkowników testowych w domenie produkcyjnej. Upewnij się, że nie Synchronizuj jakiegokolwiek innego konta
   * Przenoszenie do środowiska przeprowadzających testowanie Akceptacyjne
2.  Federacyjnego zyskania jest cenna wiedzą, że koszty związane z rozwiązaniem federacyjnym z lokalnego dostawcy tożsamości poza środowisko weryfikacji Koncepcji i miary, która przed korzyści, których szukasz:
    * Jest w ścieżce krytycznej, dlatego należy tak zaprojektować w celu zapewnienia wysokiej dostępności
    * Jest to usługa w środowisku lokalnym, czego potrzebujesz do planowania pojemności
    * Jest to usługa w środowisku lokalnym, potrzebne do monitorowania/Obsługa/patch

Dowiedz się więcej: [tożsamości opis Office 365 i Azure Active Directory - tożsamości federacyjnych](https://support.office.com/article/Understanding-Office-365-identity-and-Azure-Active-Directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9#bk_federated)

## <a name="branding"></a>Znakowanie

Przybliżony czas wykonania: 15 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Zasoby (obrazów, logo, itp.) Aby uzyskać najlepszą wizualizację upewnij się, że zasoby mają zalecane rozmiary. | [Dodawanie znakowania firmowego do strony logowania w usłudze Azure Active Directory](active-directory-branding-custom-signon-azure-portal.md) |
| Opcjonalnie: Jeśli środowisko zawiera serwer AD FS, dostęp do serwera, aby dostosować motyw sieci web | [Usługi AD FS użytkownik logowania dostosowywania](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/ad-fs-user-sign-in-customization) |
| Komputer kliencki do wykonywania logowania użytkownika końcowego |  |
| Opcjonalnie: Urządzeń przenośnych, aby sprawdzić środowisko |  |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Przejdź do portalu zarządzania usługi Azure AD | [Portal zarządzania usługi Azure AD — logo firmy](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/LoginTenantBranding) |
| Przekaż zasoby do strony logowania (hero logo, małe logo, etykiety, itp.). Opcjonalnie w przypadku usług AD FS wyrównywanie samej zasoby przy użyciu stron logowania usług AD FS | [Dodawanie znakowania firmowego do logowania i panelu dostępu do stron: elementy dostosowywalne](fundamentals/customize-branding.md) |
| Poczekaj kilka minut, zanim zmiany w pełni obowiązywać |  |
| Zaloguj się przy użyciu poświadczeń użytkownika usługi POC do https://myapps.microsoft.com |  |
| Upewnij się, wyglądu i działania w przeglądarce | [Dodawanie znakowania firmowego do logowania i panelu dostępu do stron](fundamentals/customize-branding.md) |
| Opcjonalnie upewnij się, wygląd i działanie na innych urządzeniach |  |

### <a name="considerations"></a>Zagadnienia do rozważenia

Jeśli stary wygląd i działanie pozostaje po dostosowaniu opróżnić pamięć podręczną klienta przeglądarki i spróbuj ponownie wykonać operację.

## <a name="group-based-licensing"></a>Licencjonowaniem opartym na grupie

Przybliżony czas wykonania: 10 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Wszyscy użytkownicy POC należą do grupy zabezpieczeń (w chmurze lub lokalnie) | [Tworzenie grupy i dodawać członków w usłudze Azure Active Directory](fundamentals/active-directory-groups-create-azure-portal.md) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Przejdź do bloku licencji w portalu zarządzania usługi Azure AD | [Portal zarządzania usługi Azure AD: Licencjonowanie](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) |
| Przypisać licencji do grupy zabezpieczeń, aby Zapewnić użytkownikom. | [Przypisywanie licencji do grupy użytkowników w usłudze Azure Active Directory](users-groups-roles/licensing-groups-assign.md) |

### <a name="considerations"></a>Zagadnienia do rozważenia

W przypadku problemów, przejdź do [scenariuszy, ograniczenia i znane problemy związane z korzystania z grup do zarządzania licencjonowaniem w usłudze Azure Active Directory](users-groups-roles/licensing-group-advanced.md)

## <a name="saas-federated-sso-configuration"></a>Konfiguracja federacyjnego logowania jednokrotnego SaaS

Przybliżony czas wykonania: 60 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Środowisko testowe aplikacji SaaS. W tym przewodniku używamy usługi ServiceNow jako przykład.<br/>Zdecydowanie zalecamy używanie wystąpienia testu do minimum Ogranicz liczbę problemów dotyczących poruszania się po istniejącej dobrą jakość danych i mapowania. | Przejdź do https://developer.servicenow.com/app.do#! / głównego, aby rozpocząć proces pobierania wystąpieniem testu |
| Dostęp administracyjny do konsoli zarządzania usługi ServiceNow | [Samouczek: Integracja usługi Azure Active Directory przy użyciu usługi ServiceNow](saas-apps/servicenow-tutorial.md) |
| Docelowy zestaw użytkowników można przypisać aplikacji. Zaleca się grupę zabezpieczeń zawierającą użytkowników weryfikacji koncepcji. <br/>Jeśli tworzona grupa nie jest możliwe, następnie przypisać użytkownikom bezpośrednio do aplikacji w celu weryfikacji koncepcji | [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Udostępniać w samouczku wszystkich uczestników z Microsoft Documentation  | [Samouczek: Integracja usługi Azure Active Directory przy użyciu usługi ServiceNow](saas-apps/servicenow-tutorial.md) |
| Ustaw na spotkanie pracy i wykonaj kroki samouczka z każdego aktora. | [Samouczek: Integracja usługi Azure Active Directory przy użyciu usługi ServiceNow](saas-apps/servicenow-tutorial.md) |
| Przypisywanie aplikacji do grupy określonej w wymaganiach wstępnych. Jeśli środowisko weryfikacji Koncepcji dostępu warunkowego w zakresie, możesz ponownie, który później i dodać uwierzytelnianie wieloskładnikowe i podobnych zastosowaniach. <br/>Należy pamiętać, że spowoduje to rozpoczęcie procesu inicjowania obsługi administracyjnej (jeśli jest skonfigurowane) |  [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) <br/>[Tworzenie grupy i dodawać członków w usłudze Azure Active Directory](fundamentals/active-directory-groups-create-azure-portal.md) |
| Za pomocą usługi Azure AD management Portal można dodać aplikację usługi ServiceNow z galerii| [Zarządzanie usługami AD systemu Azure Portal: aplikacje dla przedsiębiorstw](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/Overview) <br/>[What's new in zarządzania aplikacjami przedsiębiorstwa w usłudze Azure Active Directory](active-directory-enterprise-apps-whats-new-azure-portal.md) |
| W bloku aplikacji ServiceNow "Logowanie jednokrotne" Włącz "opartej na SAML logowania jednokrotnego" |  |
| Wypełnij pola "Na adres URL logowania" i "Identyfikator" z adresem URL usługi ServiceNow<br/>Pole wyboru, aby "Ustaw nowy certyfikat jako aktywny"<br/>i Zapisz ustawienia |  |
| Otwórz blok "Konfigurowanie usługi ServiceNow" w dolnej części panelu, aby wyświetlić dostosowany instrukcje dotyczące konfigurowania usługi ServiceNow |  |
| Postępuj zgodnie z instrukcjami, aby skonfigurować usługi ServiceNow |  |
| W bloku aplikacji ServiceNow "Aprowizowanie" Włącz "Automatyczny" Inicjowanie obsługi administracyjnej | [Zarządzanie kontami użytkowników, inicjowanie obsługi administracyjnej dla aplikacji dla przedsiębiorstw w nowej witrynie Azure portal](manage-apps/configure-automatic-user-provisioning-portal.md) |
| Poczekaj kilka minut, podczas inicjowania obsługi administracyjnej.  W międzyczasie można sprawdzić, czy w raportach inicjowania obsługi administracyjnej |  |
| Zaloguj się do https://myapps.microsoft.com/ konto użytkownika testowego, który ma dostęp | [Co to jest panelu dostępu?](active-directory-saas-access-panel-introduction.md) |
| Kliknij Kafelek dla aplikacji, która właśnie została utworzona. Potwierdzanie dostępu |  |
| Opcjonalnie można sprawdzić w raportach dotyczących użycia aplikacji. Należy pamiętać, że istnieje pewne opóźnienie, więc należy zaczekać trochę czasu, aby wyświetlić dane w raportach. | [Raporty dotyczące logowań w portalu usługi Azure Active Directory: użycie zarządzanych aplikacji](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Azure Active Directory report retention policies](active-directory-reporting-retention.md) (Zasady przechowywania raportów w usłudze Azure Active Directory) |

### <a name="considerations"></a>Zagadnienia do rozważenia

1. Powyżej [samouczek](saas-apps/servicenow-tutorial.md) odwołuje się do starej usługi Azure AD możliwości zarządzania. Ale PoC opiera się na [Szybki Start](active-directory-enterprise-apps-whats-new-azure-portal.md#quickstart-get-going-with-your-new-application-right-away) środowiska.
2. Jeśli aplikacja docelowa nie jest dostępna w galerii, następnie należy użyć "Przynieś własną aplikację". Dowiedz się więcej: [What's new in zarządzania aplikacjami przedsiębiorstwa w usłudze Azure Active Directory: Dodawanie niestandardowych aplikacji z jednego miejsca](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

## <a name="saas-password-sso-configuration"></a>Konfiguracja logowania jednokrotnego SaaS hasła

Przybliżony czas wykonania: 15 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Środowisko testowe dla aplikacji SaaS. Przykładem hasło logowania jednokrotnego jest HipChat i Twitter. Dla każdej aplikacji należy dokładny adres URL strony z formularza html logowania. | [W usłudze Twitter na platformie Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[HipChat w witrynie Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.hipchat) |
| Testowanie kont dla aplikacji. | [Zarejestruj się w serwisie Twitter](https://twitter.com/signup?lang=en)<br/>[Utwórz bezpłatne konto: HipChat](https://www.hipchat.com/sign_up) |
| Docelowy zestaw użytkowników można przypisać aplikacji. Grupy zabezpieczeń zawartych użytkowników jest zalecane. | [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |
| Prawa dostępu lokalnego administratora na komputerze, aby wdrożyć rozszerzenie panelu dostępu dla programu Internet Explorer, Chrome lub Firefox | [Rozszerzenie panelu dostępu dla programu Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Rozszerzenie panelu dostępu dla programu Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Rozszerzenie panelu dostępu dla programu Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Zainstaluj rozszerzenie przeglądarki | [Rozszerzenie panelu dostępu dla programu Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Rozszerzenie panelu dostępu dla programu Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Rozszerzenie panelu dostępu dla programu Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Konfigurowanie aplikacji z galerii | [What's new in zarządzania aplikacjami przedsiębiorstwa w usłudze Azure Active Directory: galerii nowych i ulepszonych aplikacji](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Skonfigurować hasło logowania jednokrotnego | [Zarządzanie logowania jednokrotnego dla aplikacji korporacyjnych w nowej witrynie Azure portal: opartego na hasłach logowanie jednokrotne](manage-apps/configure-single-sign-on-portal.md#password-based-sign-on) |
| Przypisywanie aplikacji do grupy określonej w wymaganiach wstępnych | [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |
| Zaloguj się do https://myapps.microsoft.com/ konto użytkownika testowego, który ma dostęp |  |
| Kliknij Kafelek dla aplikacji, która właśnie została utworzona. | [Co to jest panelu dostępu?: opartego na hasłach logowania jednokrotnego bez tożsamości inicjowania obsługi administracyjnej.](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Podaj poświadczenia aplikacji | [Co to jest panelu dostępu?: opartego na hasłach logowania jednokrotnego bez tożsamości inicjowania obsługi administracyjnej.](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Zamknij przeglądarkę i powtórz logowania. Użytkownik powinien go zobaczyć bezproblemowy dostęp do aplikacji. |  |
| Opcjonalnie można sprawdzić w raportach dotyczących użycia aplikacji. Należy pamiętać, że istnieje pewne opóźnienie, więc należy zaczekać trochę czasu, aby wyświetlić dane w raportach. | [Raporty dotyczące logowań w portalu usługi Azure Active Directory: użycie zarządzanych aplikacji](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Azure Active Directory report retention policies](active-directory-reporting-retention.md) (Zasady przechowywania raportów w usłudze Azure Active Directory) |

### <a name="considerations"></a>Zagadnienia do rozważenia

Jeśli aplikacja docelowa nie jest dostępna w galerii, następnie należy użyć "Przynieś własną aplikację". Dowiedz się więcej: [What's new in zarządzania aplikacjami przedsiębiorstwa w usłudze Azure Active Directory: Dodawanie niestandardowych aplikacji z jednego miejsca](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Należy pamiętać, następujące wymagania:
   * Aplikacja powinna mieć adres URL logowania znane
   * Strona logowania może zawierać formularza HTML za pomocą jednego więcej pól tekstowych, które rozszerzenia przeglądarki może automatycznie wypełniać. Jako minimum powinien zawierać nazwę użytkownika i hasło.

## <a name="saas-shared-accounts-configuration"></a>Konfiguracja udostępnionych kont SaaS

Przybliżony czas wykonania: 30 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Lista aplikacji docelowej i dokładnie logowania adresy URL wcześniej. Na przykład możesz użyć usługi Twitter. | [W usłudze Twitter na platformie Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[Zarejestruj się w serwisie Twitter](https://twitter.com/signup?lang=en) |
| Udostępnione poświadczenia dla tej aplikacji SaaS. | [Udostępnianie kont za pomocą usługi Azure AD](active-directory-sharing-accounts.md)<br/>[Azure AD zautomatyzowane hasło przerzucania dla usługi Facebook, Twitter i LinkedIn teraz w wersji zapoznawczej! — Enterprise Mobility and Security Blog] (https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/ ) |
| Poświadczenia dla co najmniej dwóch członków zespołu, którzy będą korzystać z tego samego konta. Muszą one być częścią grupy zabezpieczeń. | [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |
| Prawa dostępu lokalnego administratora na komputerze, aby wdrożyć rozszerzenie panelu dostępu dla programu Internet Explorer, Chrome lub Firefox | [Rozszerzenie panelu dostępu dla programu Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Rozszerzenie panelu dostępu dla programu Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Rozszerzenie panelu dostępu dla programu Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Zainstaluj rozszerzenie przeglądarki | [Rozszerzenie panelu dostępu dla programu Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Rozszerzenie panelu dostępu dla programu Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Rozszerzenie panelu dostępu dla programu Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Konfigurowanie aplikacji z galerii | [What's new in zarządzania aplikacjami przedsiębiorstwa w usłudze Azure Active Directory: galerii nowych i ulepszonych aplikacji](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Skonfigurować hasło logowania jednokrotnego | [Zarządzanie logowania jednokrotnego dla aplikacji korporacyjnych w nowej witrynie Azure portal: opartego na hasłach logowanie jednokrotne](manage-apps/configure-single-sign-on-portal.md#password-based-sign-on) |
| Przypisywanie aplikacji do grupy określone w wymaganiach wstępnych podczas przypisywania ich poświadczeń | [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |
| Zaloguj się jako inny użytkownik niż dostęp do aplikacji jako **takie same udostępnionego konta.**  |  |
| Opcjonalnie można sprawdzić w raportach dotyczących użycia aplikacji. Należy pamiętać, że istnieje pewne opóźnienie, więc należy zaczekać trochę czasu, aby wyświetlić dane w raportach. | [Raporty dotyczące logowań w portalu usługi Azure Active Directory: użycie zarządzanych aplikacji](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Azure Active Directory report retention policies](active-directory-reporting-retention.md) (Zasady przechowywania raportów w usłudze Azure Active Directory) |


### <a name="considerations"></a>Zagadnienia do rozważenia

Jeśli aplikacja docelowa nie jest dostępna w galerii, następnie należy użyć "Przynieś własną aplikację". Dowiedz się więcej: [What's new in zarządzania aplikacjami przedsiębiorstwa w usłudze Azure Active Directory: Dodawanie niestandardowych aplikacji z jednego miejsca](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Należy pamiętać, następujące wymagania:
   * Aplikacja powinna mieć adres URL logowania znane
   * Strona logowania może zawierać formularza HTML za pomocą jednego więcej pól tekstowych, które rozszerzenia przeglądarki może automatycznie wypełniać. Jako minimum powinien zawierać nazwę użytkownika i hasło.

## <a name="app-proxy-configuration"></a>Konfiguracja serwera Proxy aplikacji

Przybliżony czas wykonania: 20 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Podstawowa usługi Microsoft Azure AD lub subskrypcji w wersji premium i katalog usługi Azure AD, dla której jesteś administratorem globalnym | [Wersje usługi Azure Active Directory](fundamentals/active-directory-whatis.md) |
| Aplikacja sieci web hostowanych lokalnie, czy chcesz skonfigurować dla dostępu zdalnego |  |
| Serwer z systemem Windows Server 2012 R2 lub Windows 8.1 lub nowszy, na którym można zainstalować łącznik serwera Proxy aplikacji | [Omówienie łączników serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-connectors.md) |
| Jeśli na ścieżce znajduje się zapora, upewnij się, że jest otwarta, tak aby umożliwić łącznikowi wysyłanie żądań protokołu HTTPS (TCP) do serwera Proxy aplikacji | [Włącz serwer Proxy aplikacji w witrynie Azure portal: wymagania wstępne dotyczące serwera Proxy aplikacji](manage-apps/application-proxy-enable.md#application-proxy-prerequisites) |
| Jeśli Twoja organizacja używa serwerów proxy, aby nawiązać połączenie z Internetem, wykonaj zobacz w blogu wpis pracy przy użyciu istniejących serwerów proxy w środowisku lokalnym, szczegółowe informacje na temat ich konfigurowania | [Praca z istniejących serwerów proxy w środowisku lokalnym](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md) |


### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Zainstaluj łącznik na serwerze | [Włącz serwer Proxy aplikacji w witrynie Azure portal: Instalowanie i rejestrowanie łącznika](manage-apps/application-proxy-enable.md#install-and-register-a-connector) |
| Publikowanie aplikacji lokalnych w usłudze Azure AD jako aplikacje serwera Proxy aplikacji | [Publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-publish-azure-portal.md) |
| Przypisywanie użytkowników testowych | [Publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD: Dodaj użytkownika testowego](manage-apps/application-proxy-publish-azure-portal.md#add-a-test-user) |
| Opcjonalnie można skonfigurować funkcji logowania jednokrotnego dla użytkowników | [Zapewnienia logowania jednokrotnego przy użyciu serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) |
| Testowanie aplikacji, logując się do portalu MyApps jako przypisany użytkownik | https://myapps.microsoft.com |

### <a name="considerations"></a>Zagadnienia do rozważenia

1. Gdy Sugerujemy umieszczanie łącznika w sieci firmowej, istnieją przypadki, gdy zostanie wyświetlony lepszą wydajność, umieszczając go w chmurze. Dowiedz się więcej: [zagadnienia dotyczące topologii sieci, korzystając z serwera Proxy usługi Azure Active Directory Application](manage-apps/application-proxy-network-topology.md)
2. Dalsze szczegóły zabezpieczeń i jak szczególnie bezpieczny dostęp zdalny zapewnia Zobacz rozwiązania dzięki przechowywaniu tylko połączeń wychodzących: [zagadnienia dotyczące zabezpieczeń do uzyskiwania dostępu do aplikacji zdalnie przy użyciu serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-security.md)

## <a name="generic-ldap-connector-configuration"></a>Ogólny łącznik LDAP konfiguracji

Przybliżony czas wykonania: 60 minut

> [!IMPORTANT]
> To jest Konfiguracja zaawansowanych wymagające pewną znajomość programu FIM/programu MIM. Jeśli używane w środowisku produkcyjnym zaleca się pytania dotyczące tej konfiguracji przejść przez [pomocy technicznej Premier](https://support.microsoft.com/premier).

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Zainstalowany i skonfigurowany program Azure AD Connect | Blok konstrukcyjny: [synchronizacja katalogów — synchronizacja skrótów haseł](#directory-synchronization--password-hash-sync-phs--new-installation) |
| Wymagania dotyczące spotkania wystąpienia ADLDS | [Ogólny łącznik LDAP, dokumentacja techniczna: omówienie ogólnego łącznika LDAP](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap#overview-of-the-generic-ldap-connector) |
| Listy obciążeń, które użytkownicy korzystają z i atrybuty skojarzone z tych obciążeń | [Synchronizacja programu Azure AD Connect: atrybuty synchronizowane z usługą Azure Active Directory](./connect/active-directory-aadconnectsync-attributes-synchronized.md) |


### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Dodaj ogólny łącznik LDAP | [Ogólny łącznik LDAP, dokumentacja techniczna: Utwórz nowy łącznik](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap#create-a-new-connector) |
| Tworzenie profilów uruchamiania dla utworzonego łącznika (pełny import, importu zmian, pełną synchronizację, rozpoczynana jest synchronizacja przyrostowa, export) | [Tworzenie profilu przebiegu agenta zarządzania](https://technet.microsoft.com/library/jj590219(v=ws.10).aspx)<br/> [Za pomocą łączników za pomocą usługi Azure AD Connect synchronizacji Menedżera usług](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md)|
| Pełny import profil przebiegu oraz sprawdzić, czy istnieją obiekty w przestrzeni łącznika | [Wyszukaj obiektu przestrzeni łącznika](https://technet.microsoft.com/library/jj590287(v=ws.10).aspx)<br/>[Za pomocą łączników za pomocą usługi Azure AD Connect synchronizacji Menedżera usług: wyszukiwania obszaru łącznika](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md#search-connector-space) |
| Tworzenie reguły synchronizacji, tak aby obiekty w magazynie Metaverse mają wymaganych atrybutów dla obciążeń | [Synchronizacja programu Azure AD Connect: najlepsze rozwiązania dotyczące zmieniania konfiguracji domyślnej: zmiany reguł synchronizacji](./connect/active-directory-aadconnectsync-best-practices-changing-default-configuration.md#changes-to-synchronization-rules)<br/>[Synchronizacja programu Azure AD Connect: opis Aprowizacja Deklaratywna](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)<br/>[Synchronizacja programu Azure AD Connect: opis wyrażenia inicjowania obsługi administracyjnej deklaratywne](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| Uruchom pełną synchronizację cyklu | [Synchronizacja programu Azure AD Connect: harmonogram: uruchomić harmonogram](./connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler) |
| W razie problemów wykonaj rozwiązywania problemów | [Troubleshoot an object that is not synchronizing to Azure AD](./connect/active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) (Rozwiązywanie problemów z obiektem, który nie jest synchronizowany z usługą Azure AD) |
| Sprawdź, czy LDAP użytkownik może zalogować i uzyskać dostęp do aplikacji | https://myapps.microsoft.com |

### <a name="considerations"></a>Zagadnienia do rozważenia

> [!IMPORTANT]
> To jest Konfiguracja zaawansowanych wymagające pewną znajomość programu FIM/programu MIM. Jeśli używane w środowisku produkcyjnym zaleca się pytania dotyczące tej konfiguracji przejść przez [pomocy technicznej Premier](https://support.microsoft.com/premier).

## <a name="groups---delegated-ownership"></a>Grupy — delegować prawa własności

Przybliżony czas wykonania: 10 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Aplikacja SaaS (Federacyjna usługa rejestracji Jednokrotnej lub hasło logowania jednokrotnego) został już skonfigurowany | Blok konstrukcyjny: [SaaS Federacyjna usługa rejestracji Jednokrotnej w konfiguracji](#saas-federated-sso-configuration) |
| Grupa chmury, który jest przypisany dostęp do aplikacji w #1 jest identyfikowana | Blok konstrukcyjny: [SaaS Federacyjna usługa rejestracji Jednokrotnej w konfiguracji](#saas-federated-sso-configuration) <br/>[Tworzenie grupy i dodawać członków w usłudze Azure Active Directory](fundamentals/active-directory-groups-create-azure-portal.md) |
| Dostępne są poświadczenia dla właściciela grupy | [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](fundamentals/active-directory-manage-groups.md) |
| Wykryto poświadczenia na potrzeby uzyskiwania dostępu do aplikacji Pracownik przetwarzający informacje | [Co to jest panelu dostępu?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Identyfikator grupy, która ma zostać przyznany dostęp do aplikacji i skonfigurować właścicielem danej grupy| [Zarządzaj ustawieniami dla grupy w usłudze Azure Active Directory ](fundamentals/active-directory-groups-settings-azure-portal.md) |
| Zaloguj się jako właściciel grupy, zobacz członkostwa w grupach w grupy na karcie panelu dostępu | [Strona zarządzania grupami w usłudze Active Directory Azure](https://account.activedirectory.windowsazure.com/r#/groups) |
| Dodaj Pracownik przetwarzający informacje, które mają zostać przetestowane |  |
| Zaloguj się jako pracownik przetwarzający informacje, upewnij się, że Kafelek jest dostępny | [Co to jest panelu dostępu?](active-directory-saas-access-panel-introduction.md) |

### <a name="considerations"></a>Zagadnienia do rozważenia

Jeśli aplikacja ma aprowizacji, włączone, może być konieczne Poczekaj kilka minut na zakończenie inicjowania obsługi przed uzyskaniem dostępu do aplikacji jako pracownika przetwarzającego informacje.

## <a name="saas-and-identity-lifecycle"></a>SaaS i cyklem życia tożsamości

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Aplikacja SaaS (Federacyjna usługa rejestracji Jednokrotnej lub hasło logowania jednokrotnego) został już skonfigurowany | Blok konstrukcyjny: [SaaS Federacyjna usługa rejestracji Jednokrotnej w konfiguracji](#saas-federated-sso-configuration) |
| Grupa chmury, który jest przypisany dostęp do aplikacji w #1 jest identyfikowana | Blok konstrukcyjny: [SaaS Federacyjna usługa rejestracji Jednokrotnej w konfiguracji](#saas-federated-sso-configuration) <br/>[Tworzenie grupy i dodawać członków w usłudze Azure Active Directory](fundamentals/active-directory-groups-create-azure-portal.md) |
| Wykryto poświadczenia na potrzeby uzyskiwania dostępu do aplikacji Pracownik przetwarzający informacje | [Co to jest panelu dostępu?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Usuń użytkownika z grupy, w której aplikacja zostanie przypisana do | [Zarządzanie członkostwem w grupie użytkowników w dzierżawie usługi Azure Active Directory](fundamentals/active-directory-groups-members-azure-portal.md) |
| Poczekaj kilka minut na anulowanie obsługi. | [Automatyczne inicjowanie obsługi użytkowników aplikacji SaaS w usłudze Azure AD: jak działa automatyczne inicjowania obsługi administracyjnej?](active-directory-saas-app-provisioning.md#how-does-automatic-provisioning-work) |
| Na sesję przeglądarki oddzielne Zaloguj się jako pracownika przetwarzającego informacje portalu Moje aplikacje i upewnij się, brak tego kafelka | http://myapps.microsoft.com |


### <a name="considerations"></a>Zagadnienia do rozważenia

Ekstrapolację scenariusz weryfikacji koncepcji leavers i/lub scenariuszy urlopu. Jeśli użytkownik pobiera wyłączone w lokalnej usługi AD lub usunięty, nie ma już sposób, aby zalogować się do aplikacji SaaS.

## <a name="self-service-access-to-application-management"></a>Samoobsługowego dostępu do zarządzania aplikacjami

Przybliżony czas wykonania: 10 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Identyfikowanie użytkowników weryfikacji Koncepcji, żądających dostępu do aplikacji, w ramach grupy zabezpieczeń | Blok konstrukcyjny: [SaaS Federacyjna usługa rejestracji Jednokrotnej w konfiguracji](#saas-federated-sso-configuration) |
| Wdróż aplikację docelowym | Blok konstrukcyjny: [SaaS Federacyjna usługa rejestracji Jednokrotnej w konfiguracji](#saas-federated-sso-configuration) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Przejdź do bloku aplikacji dla przedsiębiorstw w portalu zarządzania usługi Azure AD | [Portalu zarządzania usługi Azure AD: Aplikacje dla przedsiębiorstw](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) |
| Konfigurowanie aplikacji z warunków wstępnych przy użyciu samoobsługowego | [What's new in zarządzania aplikacjami przedsiębiorstwa w usłudze Azure Active Directory: Konfigurowanie samoobsługowego dostępu do aplikacji](active-directory-enterprise-apps-whats-new-azure-portal.md#configure-self-service-application-access) |
| Zaloguj się jako pracownika przetwarzającego informacje portalu Moje aplikacje | http://myapps.microsoft.com |
| Zwróć uwagę, "+ Dodaj aplikację" przycisk na op strony. Użyj, aby uzyskać dostęp do aplikacji |  |

### <a name="considerations"></a>Zagadnienia do rozważenia

Aplikacje wybrane mogą mieć, wymagania dotyczące udostępniania, więc przechodząc bezpośrednio do aplikacji może spowodować błędy. Jeśli aplikacji, wybranych obsługę z usługą azure ad i jest skonfigurowany, można użyć to jako okazję do wyświetlenia całego przepływu pracy typu end to end. Zobacz blokiem [SaaS Federacyjna usługa rejestracji Jednokrotnej konfiguracji](#saas-federated-sso-configuration) aby otrzymać dalsze zalecenia

## <a name="self-service-password-reset"></a>Samodzielne resetowanie hasła

Przybliżony czas wykonania: 15 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Włącz samoobsługowe zarządzanie hasłami w swojej dzierżawie. | [Usługa Azure Active Directory resetowania hasła dla administratorów IT](active-directory-passwords-update-your-own-password.md) |
| Włączanie zapisywania zwrotnego haseł do zarządzania hasłami ze środowiska lokalnego. Uwaga ta migracja wymaga określonej usługi Azure AD Connect w wersji | [Wymagania wstępne dotyczące funkcji zapisywania zwrotnego haseł](authentication/howto-sspr-writeback.md) |
| Zidentyfikuj użytkowników weryfikacji koncepcji, które będą korzystać z tej funkcji i upewnij się, że są członkami grupy zabezpieczeń. Użytkownicy muszą być użytkowników innych niż administratorzy w pełni prezentować możliwości | [Dostosowywanie: Zarządzanie hasłami usługi AD platformy Azure: ograniczanie dostępu do resetowania hasła](authentication/howto-sspr-writeback.md) |


### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Przejdź do portalu zarządzania usługi Azure AD: resetowanie hasła | [Portalu zarządzania usługi Azure AD: Resetowanie hasła](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) |
| Określ zasady resetowania hasła. Do celów weryfikacji Koncepcji można użyć połączenia telefonicznego i pytań. Zalecane jest aby włączyć rejestrację, aby wymagać Zaloguj się do panelu dostępu |  |
| Wyloguj się i zaloguj się jako pracownik przetwarzający informacje |  |
| Podaj dane samoobsługowego resetowania hasła, zgodnie z konfiguracją dla kroku 2 | https://aka.ms/ssprsetup |
| Zamknij przeglądarkę |  |
| Zacznij od początku procesu logowania jako pracownik przetwarzający informacje, których użyto w kroku 4 |  |
| Resetowanie hasła | [Aktualizowanie hasła: resetowanie hasła](active-directory-passwords-update-your-own-password.md) |
| Spróbuj zalogować się przy użyciu nowego hasła do usługi Azure AD, jak również do zasobów lokalnych |  |

### <a name="considerations"></a>Zagadnienia do rozważenia

1. W przypadku uaktualniania programu Azure AD Connect będzie powodować Ogranicz liczbę problemów, należy rozważyć użycie go przed kont w chmurze lub ułatwiają pokaz przeciw oddzielnego środowiska
2. Administratorzy mają inne zasady, i przy użyciu konta administratora, aby zresetować hasło może być skażenia środowisko weryfikacji koncepcji i spowodować nieporozumienie. Upewnij się, że używasz to zwykłe konto użytkownika do testowania operacji resetowania


## <a name="azure-multi-factor-authentication-with-phone-calls"></a>Uwierzytelnianie wieloskładnikowe systemu Azure za pomocą połączeń telefonicznych

Przybliżony czas wykonania: 10 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Identyfikowanie użytkowników weryfikacji Koncepcji, korzystających z usługi MFA  |  |
| Telefon z dobrą odbioru dla żądania uwierzytelniania MFA  | [Co to jest usługa Multi-Factor Authentication platformy Azure?](authentication/multi-factor-authentication.md) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Przejdź do bloku "Użytkownicy i grupy" w portalu zarządzania usługi Azure AD | [Portal zarządzania usługi Azure AD: Użytkowników i grup](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Overview/menuId/) |
| Wybierz bloku "Wszyscy użytkownicy" |  |
| W prawym górnym pasku wybierz opcję "Uwierzytelnianie Multi-Factor Authentication" przycisk | Bezpośredni adres URL do portalu usługi Azure MFA: https://aka.ms/mfaportal |
| W ustawieniach "User" Wybierz użytkowników, weryfikacji koncepcji i włączyć je do usługi MFA | [Stany użytkowników w usłudze Azure Multi-Factor Authentication](authentication/howto-mfa-userstates.md) |
| Zaloguj się jako użytkownik weryfikacji koncepcji i przewodnik Weryfikacja procesu  |  |

### <a name="considerations"></a>Zagadnienia do rozważenia

1. Kroki weryfikacji koncepcji w tym bloku tworzenia jawne ustawienie uwierzytelniania Wieloskładnikowego dla użytkownika na wszystkie nazwy logowania. Innych narzędzi, takich jak dostęp warunkowy i Identity Protection, które przyciągną usługi MFA na więcej określonych scenariuszach. Jest to coś, co należy wziąć pod uwagę podczas przenoszenia z weryfikacji Koncepcji w środowisku produkcyjnym.
2. Kroki weryfikacji koncepcji w tym bloku konstrukcyjnego są jawnie przy użyciu połączeń telefonicznych jako metoda uwierzytelniania Wieloskładnikowego w celu expedience. Przejście od weryfikacji Koncepcji do środowiska produkcyjnego, zaleca się przy użyciu aplikacji, takich jak [Microsoft Authenticator](authentication/end-user/current/microsoft-authenticator-app-how-to.md) jako swojej drugiego składnika, jeśli to możliwe.
Dowiedz się więcej: [publikacji specjalne narzędzia DRAFT NIST 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="mfa-conditional-access-for-saas-applications"></a>Dostęp warunkowy usługi MFA dla aplikacji SaaS

Przybliżony czas wykonania: 10 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Zidentyfikuj użytkowników PoC pod kątem zasad. Tacy użytkownicy musi należeć do grupy zabezpieczeń, aby określić zakres zasad dostępu warunkowego | [Konfiguracja federacyjnego logowania jednokrotnego SaaS](#saas-federated-sso-configuration) |
| Aplikacja SaaS została już skonfigurowana |  |
| Aby zapewnić użytkownikom są już przypisane do aplikacji |  |
| Dostępne są poświadczenia użytkownika weryfikacji Koncepcji |  |
| Weryfikacja Koncepcji, użytkownik jest zarejestrowany do uwierzytelniania Wieloskładnikowego. Za pomocą telefonu z dobrą odbioru | https://aka.ms/ssprsetup |
| Urządzenie w sieci wewnętrznej. Adres IP skonfigurowany w zakresie wewnętrznym adresem | Znajdowanie adresu ip: https://www.bing.com/search?q=what%27s+my+ip |
| Urządzenia w sieci zewnętrznej (może to być telefon, za pomocą operatora sieci komórkowej) |  |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Przejdź do portalu zarządzania usługi Azure AD: bloku dostępu warunkowego | [Portalu zarządzania usługi Azure AD: Dostęp warunkowy](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) |
| Tworzenie zasad dostępu warunkowego:<br/>-Target użytkowników weryfikacji koncepcji, w obszarze "Użytkownicy i grupy"<br/>-Target PoC aplikacji w obszarze "Aplikacji w chmurze"<br/>-Docelowe lokalizacje wszystkie z wyjątkiem tych zaufanych pod "Warunki" -> "Lokalizacje" **Uwaga:** zaufane adresy IP są konfigurowane w [portalu usługi MFA](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx)<br/>-Wymagaj uwierzytelniania wieloskładnikowego w obszarze "Przydział" | [Rozpoczynanie pracy przy użyciu dostępu warunkowego w usłudze Azure Active Directory: z krokami konfiguracji zasad](active-directory-conditional-access-azure-portal-get-started.md#policy-configuration-steps) |
| Dostęp do aplikacji z wewnątrz sieci firmowej | [Rozpoczynanie pracy przy użyciu dostępu warunkowego w usłudze Azure Active Directory: testowania zasad](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |
| Dostęp do aplikacji z sieci publicznej | [Rozpoczynanie pracy przy użyciu dostępu warunkowego w usłudze Azure Active Directory: testowania zasad](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |

### <a name="considerations"></a>Zagadnienia do rozważenia

Jeśli używasz Federacji, można użyć lokalnego dostawcy tożsamości (IdP) do komunikowania się stan w obrębie/poza siecią firmową za pomocą oświadczeń. Tej techniki można używać bez konieczności zarządzania listę adresów IP, które mogą być złożone, aby ocenić i zarządzanie nimi w dużych organizacjach. W tej konfiguracji należy uwzględnić w scenariuszu "roaming sieci" (zalogowanie się użytkownika z sieci wewnętrznej, a podczas przełączników zalogowanego na przykład w kawiarni) i upewnij się, że rozumiesz implikacje. Dowiedz się więcej: [zabezpieczanie zasobów w chmurze za pomocą usługi Azure Multi-Factor Authentication i usług AD FS: zaufane adresy IP dla użytkowników federacyjnych](authentication/howto-mfa-adfs.md#trusted-ips-for-federated-users)

## <a name="privileged-identity-management-pim"></a>Usługa Privileged Identity Management (PIM)

Przybliżony czas wykonania: 15 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Identyfikowanie Administrator globalny, który będzie częścią POC przez usługę PIM | [Rozpoczynanie korzystania z usługi Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) |
| Identyfikowanie Administrator globalny, który ma zostać Administrator zabezpieczeń | [Rozpoczynanie korzystania z usługi Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)<br/> [Różne role administracyjne w usłudze PIM usługi Azure Active Directory](active-directory-privileged-identity-management-roles.md) |
| Opcjonalnie: Upewnij się, jeśli Administratorzy globalni mają dostęp do poczty e-mail do wykonywania powiadomienia e-mail w usłudze PIM | [Co to jest usługa Azure AD Privileged Identity Management?: Konfigurowanie ustawień aktywacji roli](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)


### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Zaloguj się do https://portal.azure.com jako administrator globalny (GA), a następnie ładowania początkowego bloku usługi PIM. Administrator globalny, który wykonuje krok ten jest obsługiwany jako administrator zabezpieczeń.  Nadajmy tej GA1 aktora | [Za pomocą Kreatora zabezpieczeń w usłudze Azure AD Privileged Identity Management](active-directory-privileged-identity-management-security-wizard.md) |
| Zidentyfikuj administratora globalnego i przenieść je z stałe na kwalifikujących. Powinna to być jako osobne administrator komentarzowi użytemu w kroku 1 w celu uściślenia. Nadajmy tej GA2 aktora | [Usługa Azure AD Privileged Identity Management: Jak dodać lub usunąć rolę użytkownika](active-directory-privileged-identity-management-how-to-add-role-to-user.md)<br/>[Co to jest usługa Azure AD Privileged Identity Management?: Konfigurowanie ustawień aktywacji roli](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)  |
| Teraz, zaloguj się jako GA2 do https://portal.azure.com i spróbuj zmienić "Ustawienia użytkownika". Należy zauważyć, że niektóre opcje są wygaszone. | |
| W nowej karcie i w tej samej sesji w kroku 3, przejdź teraz do https://portal.azure.com i Dodaj blok PIM do pulpitu nawigacyjnego. | [Jak aktywować lub dezaktywować ról w usłudze Azure AD Privileged Identity Management: Dodawanie aplikacji Privileged Identity Management](active-directory-privileged-identity-management-how-to-activate-role.md#add-the-privileged-identity-management-application) |
| Żądanie aktywacji do roli administratora globalnego | [Jak aktywować lub dezaktywować ról w usłudze Azure AD Privileged Identity Management: aktywować rolę](active-directory-privileged-identity-management-how-to-activate-role.md#activate-a-role) |
| Należy pamiętać, że GA2 nigdy nie zarejestrowali się do uwierzytelniania Wieloskładnikowego, rejestracji dla usługi Azure MFA będzie to konieczne |  |
| Wróć do oryginalnego karty w kroku 3, a następnie kliknij przycisk Odśwież w przeglądarce. Należy zauważyć, że możesz teraz dostęp do ustawień "użytkownika" | |
| Opcjonalnie Jeśli Twoja Administratorzy globalni mają włączoną pocztą e-mail, można sprawdzić GA1 i GA2 w skrzynce odbiorczej oraz wyświetlone powiadomienie roli aktywowany |  |
| 8 można znaleźć w historii inspekcji i sprawdź, czy raport, aby potwierdzić podniesienie GA2 jest wyświetlany. | [Co to jest usługa Azure AD Privileged Identity Management?: Przejrzyj działania roli](active-directory-privileged-identity-management-configure.md#review-role-activity) |

### <a name="considerations"></a>Zagadnienia do rozważenia

Ta funkcja jest częścią usługi Azure AD Premium P2 i/lub EMS E5

## <a name="discovering-risk-events"></a>Wykrywanie zdarzeń o podwyższonym ryzyku

Przybliżony czas wykonania: 20 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Urządzenie z przeglądarką sieci Tor pobrane i zainstalowane | [Pobierz przeglądarki Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Dostęp do weryfikacji Koncepcji użytkownikowi logowanie | [Podręcznik usługi Azure Active Directory Identity Protection](active-directory-identityprotection-playbook.md) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Tor Otwieranie przeglądarki | [Pobierz przeglądarki Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Zaloguj się do https://myapps.microsoft.com przy użyciu konta użytkownika weryfikacji Koncepcji | [Podręcznik usługi Azure Active Directory Identity Protection: Symulowanie zdarzeń o podwyższonym ryzyku](active-directory-identityprotection-playbook.md#simulating-risk-events) |
| Poczekaj 5 – 7 minut |  |
| Zaloguj się jako administrator globalny do https://portal.azure.com i otwórz blok Identity Protection | https://aka.ms/aadipgetstarted |
| Otwórz blok zdarzeń o podwyższonym ryzyku. Powinien zostać wyświetlony wpis w obszarze "Połączenia logowania z anonimowych adresów IP"  | [Podręcznik usługi Azure Active Directory Identity Protection: Symulowanie zdarzeń o podwyższonym ryzyku](active-directory-identityprotection-playbook.md#simulating-risk-events) |

### <a name="considerations"></a>Zagadnienia do rozważenia

Ta funkcja jest częścią usługi Azure AD Premium P2 i/lub EMS E5

## <a name="deploying-sign-in-risk-policies"></a>Wdrażanie zasad logowania o podwyższonym ryzyku

Przybliżony czas wykonania: 10 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Urządzenie z przeglądarką sieci Tor pobrane i zainstalowane | [Pobierz przeglądarki Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Dostęp jako użytkownik weryfikacji Koncepcji w celu dziennika podczas testowania |  |
| Weryfikacja Koncepcji, użytkownik jest zarejestrowany za pomocą usługi MFA. Pamiętaj używać telefonu z dobrą odbioru | Blok konstrukcyjny: [uwierzytelnianie wieloskładnikowe systemu Azure za pomocą połączeń telefonicznych](#azure-multi-factor-authentication-with-phone-calls) |


### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Zaloguj się jako administrator globalny do https://portal.azure.com , a następnie otwórz blok Identity Protection | https://aka.ms/aadipgetstarted |
| Włącz zasadę ryzyka logowania w następujący sposób:<br/>-Przypisane do: użytkownik weryfikacji Koncepcji<br/>-Warunki: Ryzyka logowania średniej lub nowszej (logowania z anonimowych lokalizacji jest uznawany za poziomu średniego ryzyka)<br/>-Kontrolki: Wymagaj uwierzytelniania Wieloskładnikowego | [Podręcznik usługi Azure Active Directory Identity Protection: ryzyka logowania](active-directory-identityprotection-playbook.md) |
| Tor Otwieranie przeglądarki | [Pobierz przeglądarki Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Zaloguj się do https://myapps.microsoft.com przy użyciu konta użytkownika weryfikacji koncepcji |  |
| Zwróć uwagę, żądania uwierzytelniania MFA | [Logowania środowisk przy użyciu usługi Azure AD Identity Protection: odzyskiwania ryzykowne logowania](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

### <a name="considerations"></a>Zagadnienia do rozważenia

Ta funkcja jest częścią usługi Azure AD Premium P2 i/lub EMS E5. Aby dowiedzieć się więcej na temat zdarzeń o podwyższonym ryzyku, odwiedź stronę: [zdarzeń o podwyższonym ryzyku Azure Active Directory](active-directory-reporting-risk-events.md)

## <a name="configuring-certificate-based-authentication"></a>Konfigurowanie uwierzytelniania opartego na certyfikatach

Przybliżony czas trwania: 20 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Urządzenia przy użyciu certyfikatu użytkownika zainicjowano obsługę administracyjną (Windows, iOS lub Android) z Enterprise PKI | [Wdrażanie certyfikatów użytkowników](https://msdn.microsoft.com/library/cc770857.aspx) |
| Domena usługi Azure AD Sfederowane za pomocą usług AD FS | [Program Azure AD Connect a federacja](./connect/active-directory-aadconnectfed-whatis.md)<br/>[Omówienie usług certyfikatów Active Directory](https://technet.microsoft.com/library/hh831740.aspx)|
| Dla urządzeń z systemem iOS należy mieć zainstalowaną aplikację Microsoft Authenticator | [Rozpoczynanie pracy z aplikacją Microsoft Authenticator](authentication/end-user/current/microsoft-authenticator-app-how-to.md) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Włączyć "Uwierzytelnianie certyfikatu" w usługach ADFS | [Konfigurowanie zasad uwierzytelniania: Można skonfigurować uwierzytelniania podstawowego globalnie w systemie Windows Server 2012 R2](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-authentication-policies#to-configure-primary-authentication-globally-in-windows-server-2012-r2) |
| Opcjonalnie: Włącz uwierzytelnianie certyfikatu w usłudze Azure AD dla klientów programu Exchange Active Sync | [Wprowadzenie do uwierzytelniania opartego na certyfikacie w usłudze Azure Active Directory](active-directory-certificate-based-authentication-get-started.md) |
| Przejdź do panelu dostępu i uwierzytelniania za pomocą certyfikatu użytkownika | https://myapps.microsoft.com |

### <a name="considerations"></a>Zagadnienia do rozważenia

Aby dowiedzieć się więcej na temat ostrzeżenia tego wdrożenia można znaleźć: [usług AD FS: uwierzytelnianie certyfikatu za pomocą usługi Azure AD & Office 365](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)



> [!NOTE]
> Powinny być chronione podobnie posiadania certyfikatu użytkownika. Przy użyciu zarządzania urządzeniami lub za pomocą numeru PIN w przypadku kart inteligentnych.



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
