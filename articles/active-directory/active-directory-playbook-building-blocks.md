---
title: Azure Active Directory dowód bloków konstrukcyjnych podręcznika dotyczącego koncepcji | Dokumentacja firmy Microsoft
description: Eksploruj i szybkie rozpoczęcie scenariusze Zarządzanie tożsamościami i dostępem
services: active-directory
keywords: Usługa Azure active directory, podręcznika dotyczącego koncepcji, aby zapewnić
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
ms.openlocfilehash: 019785ae0bde58b33cc4b09e2e2746f3fd474b70
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="azure-active-directory-proof-of-concept-playbook-building-blocks"></a>Azure Active Directory dowód podręcznikowym koncepcji: bloki konstrukcyjne

## <a name="catalog-of-roles"></a>Wykaz ról

| Rola | Opis | Weryfikacja koncepcji odpowiedzialności |
| --- | --- | --- |
| **Architektura tożsamości / zespół deweloperów** | Zespół ten jest zwykle projektuje rozwiązania, implementuje prototypy, dyski zatwierdzenia i przekazuje koniec operacji | Udostępniają one tych środowisk i te ocenia różnych scenariuszy z punktu widzenia możliwości zarządzania |
| **Zespół operacyjny tożsamości lokalnych** | Zarządza inną tożsamość źródeł lokalną: lasy usługi Active Directory, katalogów LDAP HR systemów i dostawców tożsamości federacyjnych. | Dostęp do lokalnych zasobów niezbędnych do scenariuszy fazy weryfikacji koncepcji.<br/>One powinny być wykonywane jak najmniejszy|
| **Właściciele technicznego aplikacji** | Właściciele techniczne inną chmurę aplikacji i usług, które zostanie zintegrowana z usługą Azure AD | Szczegółowe informacje dotyczące aplikacji SaaS (potencjalnie wystąpień do testowania) |
| **Administrator globalny usługi Azure AD** | Zarządza konfiguracją usługi Azure AD | Podaj poświadczenia, aby skonfigurować usługę synchronizacji. Zazwyczaj taki sam zespół jako architektura tożsamości podczas fazy weryfikacji koncepcji, ale niezależnie w fazie operacji|
| **Zespół bazy danych** | Właściciele infrastruktury bazy danych | Zapewniają dostęp do środowiska programu SQL (AD FS lub Azure AD Connect) dla danego scenariusza przygotowań.<br/>One powinny być wykonywane jak najmniejszy |
| **Zespół sieci** | Właściciele infrastruktury sieci | Podaj wymagane prawa dostępu na poziomie sieci dla serwerów synchronizacji poprawnie dostępu do źródeł danych i chmury usługi (reguły zapory, otwierane porty, reguły IPSec itp.) |
| **Zespół ds. zabezpieczeń** | Definiuje strategii zabezpieczeń analizuje raporty dotyczące zabezpieczeń z różnych źródeł i za pośrednictwem jest zgodna w wynikach. | Zabezpieczenia docelowy scenariusze oceny |

## <a name="common-prerequisites-for-all-building-blocks"></a>Typowe wymagania wstępne dotyczące wszystkie bloki konstrukcyjne

Poniżej przedstawiono niektóre wymagań wstępnych dla dowolnego fazy weryfikacji Koncepcji z usługą Azure AD Premium.

| Wymagania wstępne | Zasoby |
| --- | --- |
| Zdefiniowane za pomocą ważnej subskrypcji platformy Azure dzierżawą platformy Azure AD | [Jak uzyskać dzierżawę usługi Azure Active Directory](active-directory-howto-tenant.md)<br/>**Uwaga:** Jeśli istnieje już środowisko o licencji Azure AD Premium, możesz uzyskać zero zakończenia subskrypcji, przechodząc do https://aka.ms/accessaad <br/>Dowiedz się więcej, zobacz: https://blogs.technet.microsoft.com/enterprisemobility/2016/02/26/azure-ad-mailbag-azure-subscriptions-and-azure-ad-2/ i https://technet.microsoft.com/library/dn832618.aspx |
| Domen zdefiniowany i weryfikacji | [Dodawanie niestandardowej nazwy domeny do usługi Azure Active Directory](active-directory-domains-add-azure-portal.md)<br/>**Uwaga:** niektórych obciążeń, takich jak usługi Power BI można elastycznie dzierżawy usługi azure AD w tle. Aby sprawdzić, czy skojarzony z dzierżawą danej domeny, przejdź do https://login.microsoftonline.com/{domain}/v2.0/.well-known/openid-configuration. Jeśli uzyskać pomyślnej odpowiedzi, a następnie domena jest już przypisany do dzierżawy i przejęcia mogą być wymagane. Jeśli tak, aby uzyskać dalsze informacje kontakt z firmą Microsoft. Dowiedz się więcej na temat opcji przejęcia na: [czym jest rejestracja samoobsługowa na platformie Azure?](active-directory-self-service-signup.md) |
| Azure AD Premium lub pakietu EMS włączone wersji próbnej | [Azure Active Directory Premium wolnego przez jeden miesiąc](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Aby zapewnić użytkownikom przypisano licencji pakietu EMS lub Azure AD Premium | [Licencja użytkownika, jak i użytkowników w usłudze Azure Active Directory](active-directory-licensing-get-started-azure-portal.md) |
| Poświadczenia usługi Azure AD administratora globalnego | [Przypisywanie ról administratorów w usłudze Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md) |
| Opcjonalny, lecz zdecydowanie zalecane: środowiska laboratoryjnego równoległych jako rezerwowe | [Prerequisites for Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) (Wymagania wstępne programu Azure AD Connect) |

## <a name="directory-synchronization---password-hash-sync-phs---new-installation"></a>Katalog synchronizacji — synchronizacja skrótów haseł (PHS, który) — nowa instalacja

Przybliżona godzina ukończone: dopiero po godzinie mniej niż 1000 użytkowników fazy weryfikacji koncepcji

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Serwer, aby uruchomić usługę Azure AD Connect | [Prerequisites for Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) (Wymagania wstępne programu Azure AD Connect) |
| Docelowa użytkowników fazy weryfikacji Koncepcji, w tej samej domenie i część grupy zabezpieczeń i jednostki Organizacyjnej | [Niestandardowa instalacja programu Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) |
| Funkcje platformy Azure AD Connect potrzebne w fazie weryfikacji koncepcji są identyfikowane | [Połączenie usługi Active Directory z usługą Azure Active Directory — Konfigurowanie synchronizacji funkcji](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Potrzeby poświadczeń dla lokalnych i środowisk w chmurze  | [Azure AD Connect: Accounts and permissions](./connect/active-directory-aadconnect-accounts-permissions.md) (Azure AD Connect: konta i uprawnienia) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Pobierz najnowszą wersję programu Azure AD Connect | [Pobierz Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) |
| Instalowanie usługi Azure AD Connect przy użyciu to najprostsza ścieżka: Express <br/>1. Filtruj do docelowej jednostki Organizacyjnej, aby zminimalizować czas cyklu synchronizacji<br/>2. Wybierz zestaw docelowy użytkowników do grupy lokalnej.<br/>3. Wdrożenie funkcji wymaganej przez inne motywy fazy weryfikacji Koncepcji | [Azure AD Connect: Instalacja niestandardowa: domenę i jednostkę Organizacyjną filtrowania](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) <br/>[Azure AD Connect: Instalacja niestandardowa: grupy na podstawie filtrowania](./connect/active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)<br/>[Azure AD Connect: Integrowanie tożsamości lokalnych z usługą Azure Active Directory: Konfigurowanie funkcji synchronizacji](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Otwórz program Azure AD Connect interfejsu użytkownika i zobacz uruchomionych profilów ukończone (Import, synchronizacji i eksportowanie) | [Synchronizacja programu Azure AD Connect: Harmonogram](./connect/active-directory-aadconnectsync-feature-scheduler.md) |
| Otwórz [portalu zarządzania usługi Azure AD](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/), przejdź do bloku "Wszyscy użytkownicy", Dodaj kolumnę "Źródłowego urzędu" i zobacz, który pojawia się użytkowników, prawidłowo oznaczone jako pochodzący od "Windows Server AD" | [Portal zarządzania usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) |

### <a name="considerations"></a>Zagadnienia do rozważenia

1. Przyjrzyj się zagadnienia dotyczące zabezpieczeń z synchronizacji skrótów haseł [tutaj](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).  Jeśli synchronizacja skrótów haseł dla użytkowników pilotażowych produkcji nie jest ostatecznie opcji, należy rozważyć następujących alternatyw:
   * Tworzenie użytkowników testowych w domenie produkcji. Upewnij się, że nie Synchronizuj innego konta
   * Przenieś do środowiska UAT
2.  Jeśli chcesz wykonywać federacyjnych, warto poznać koszty związane federacyjnych rozwiązania z dostawcy tożsamości lokalnych poza fazy weryfikacji Koncepcji i miary, która przed korzyści, które użytkownik chce się dowiedzieć:
    * Dlatego należy projektować pod kątem wysokiej dostępności jest w ścieżce krytyczne
    * Jest to usługa lokalnymi, potrzebne do planowania pojemności
    * Jest to usługa lokalnymi, potrzebne do monitora/Obsługa/poprawki

Dowiedz się więcej: [tożsamości opis Office 365 i Azure Active Directory - tożsamości federacyjnych](https://support.office.com/article/Understanding-Office-365-identity-and-Azure-Active-Directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9#bk_federated)

## <a name="branding"></a>Znakowania.

Przybliżona godzina ukończone: 15 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Zasoby (obrazy logo, itp.); Najlepsze wizualizacji upewnij się, że zasoby mieć zalecane rozmiary. | [Dodawanie znakowania firmowego do strony logowania w usłudze Azure Active Directory](active-directory-branding-custom-signon-azure-portal.md) |
| Opcjonalnie: Jeśli środowisko zawiera serwer usług AD FS, dostęp do serwera, aby dostosować motyw sieci web | [Usługi AD FS logowania dostosowaniem wprowadzanym przez użytkowników](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/ad-fs-user-sign-in-customization) |
| Komputer kliencki może wykonać logowanie użytkownika końcowego |  |
| Opcjonalnie: Urządzenia przenośne można sprawdzić poprawności środowisko |  |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Przejdź do portalu zarządzania usługi Azure AD | [Portal zarządzania usługi Azure AD — logo firmy](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/LoginTenantBranding) |
| Przekaż zasoby na stronie logowania (bohater logo, małego logo, etykiety itp.). Opcjonalnie Jeśli usługi AD FS, align sam zasoby z strony logowania usług AD FS | [Dodawanie znakowania firmowego do logowania i panelu dostępu stron: elementy dostosowywalne](customize-branding.md) |
| Poczekaj kilka minut, aby zmiany w pełni obowiązywać |  |
| Zaloguj się przy użyciu poświadczeń użytkownika fazy weryfikacji Koncepcji do https://myapps.microsoft.com |  |
| Potwierdź wygląd i działanie w przeglądarce | [Dodawanie znakowania firmowego do logowania i panelu dostępu stron](customize-branding.md) |
| Opcjonalnie można potwierdzić wyglądu i działania na innych urządzeniach |  |

### <a name="considerations"></a>Zagadnienia do rozważenia

Jeśli stary wygląd i działanie pozostaje po dostosowaniu opróżnić pamięć podręczną klienta przeglądarki i spróbuj ponownie wykonać operację.

## <a name="group-based-licensing"></a>Oparta na grupy licencji

Przybliżona godzina ukończone: 10 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Wszyscy użytkownicy w fazie weryfikacji Koncepcji należą do grupy zabezpieczeń (chmurze lub lokalnie) | [Utwórz grupy i Dodaj elementy członkowskie w usłudze Azure Active Directory](active-directory-groups-create-azure-portal.md) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Przejdź do bloku licencji w portalu zarządzania usługi Azure AD | [Portal zarządzania usługi Azure AD: licencjonowania](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) |
| Przypisywanie licencji do grupy zabezpieczeń z użytkownikami fazy weryfikacji Koncepcji. | [Przypisywanie licencji do grupy użytkowników w usłudze Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) |

### <a name="considerations"></a>Zagadnienia do rozważenia

W przypadku problemów, przejdź do [scenariuszy, ograniczenia i znane problemy dotyczące korzystania z grup do zarządzania Licencjonowanie w usłudze Azure Active Directory](active-directory-licensing-group-advanced.md)

## <a name="saas-federated-sso-configuration"></a>Konfiguracja federacyjną rejestracją Jednokrotną w modelu SaaS

Przybliżona godzina ukończone: 60 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Środowisko testowe dostępnych aplikacji SaaS. W tym przewodniku używamy usługi ServiceNow jako przykład.<br/>Stanowczo zaleca się używanie wystąpienia testu, aby zminimalizować tarcia na nawigowanie po istniejących jakości danych i mapowania. | Przejdź do https://developer.servicenow.com/app.do#! / macierzysty można uruchomić procesu pobierania wystąpienia testu |
| Dostęp administratora do konsoli zarządzania usługi ServiceNow | [Samouczek: Integracji Azure Active Directory z usługi ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Docelowy zestaw użytkowników można przypisać do aplikacji. Zaleca się grupę zabezpieczeń zawierającą użytkowników fazy weryfikacji koncepcji. <br/>Tworzenie grupy nie jest realne, przypisując użytkownikom bezpośrednio do aplikacji w fazie weryfikacji koncepcji | [Przypisanie użytkownika lub grupę do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Udostępnianie w samouczku wszystkich podmiotów z Microsoft Documentation  | [Samouczek: Integracji Azure Active Directory z usługi ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Ustaw spotkania pracy, a następnie wykonaj kroki samouczka z każdym aktora. | [Samouczek: Integracji Azure Active Directory z usługi ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Przypisywanie aplikacji do grupy określonej w wymaganiach wstępnych. Jeśli fazy weryfikacji Koncepcji ma dostęp warunkowy w zakresie, można ponownie, które później i dodać uwierzytelnianie wieloskładnikowe i podobne. <br/>Należy zauważyć, że to rozpocząć w procesie inicjowania obsługi administracyjnej (jeśli jest skonfigurowane) |  [Przypisanie użytkownika lub grupę do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) <br/>[Utwórz grupy i Dodaj elementy członkowskie w usłudze Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Przy użyciu usługi Azure AD management Portal Dodaj aplikację usługi ServiceNow z galerii| [Usługa Azure AD management Portal: aplikacje dla przedsiębiorstw](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/Overview) <br/>[What's new in zarządzania aplikacjami przedsiębiorstwa w usłudze Azure Active Directory](active-directory-enterprise-apps-whats-new-azure-portal.md) |
| W bloku aplikacji usługi ServiceNow "Logowanie jednokrotne" Włącz "na podstawie SAML logowania" |  |
| Wypełnij pola "Zaloguj się na adres URL" i "Identyfikator" z adresem URL usługi ServiceNow<br/>Pole wyboru, aby "Uaktywnij nowy certyfikat"<br/>i Zapisz ustawienia |  |
| Otwarcie bloku "Konfigurowanie usługi ServiceNow" w dolnej części panelu, aby wyświetlić instrukcje niestandardowe do skonfigurowania usługi ServiceNow |  |
| Postępuj zgodnie z instrukcjami, aby skonfigurować usługi ServiceNow |  |
| W bloku aplikacji usługi ServiceNow "Aprowizowanie" Włącz "Automatyczny" Inicjowanie obsługi administracyjnej | [Zarządzanie kontami użytkowników, inicjowanie obsługi administracyjnej dla aplikacji przedsiębiorstwa w portalu Azure](active-directory-enterprise-apps-manage-provisioning.md) |
| Poczekaj kilka minut, podczas udostępniania.  Do tego czasu można sprawdzić w raportach inicjowania obsługi administracyjnej |  |
| Zaloguj się do https://myapps.microsoft.com/ jako użytkownika testowego, które ma dostęp | [Co to jest Panel dostępu?](active-directory-saas-access-panel-introduction.md) |
| Kliknij Kafelek dla aplikacji, która została właśnie utworzona. Potwierdź dostęp |  |
| Opcjonalnie można sprawdzić raporty użycia aplikacji. Należy zauważyć, że istnieje pewnego opóźnienia przesyłania, więc musisz Poczekaj chwilę, aby wyświetlić dane w raportach. | [Działania logowania raportów w portalu usługi Azure Active Directory: użycie aplikacji zarządzanej](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Azure Active Directory report retention policies](active-directory-reporting-retention.md) (Zasady przechowywania raportów w usłudze Azure Active Directory) |

### <a name="considerations"></a>Zagadnienia do rozważenia

1. Powyżej [samouczek](active-directory-saas-servicenow-tutorial.md) odwołuje się do starego Azure AD, możliwości zarządzania. Jednak fazy weryfikacji koncepcji jest obliczana na podstawie [szybki start](active-directory-enterprise-apps-whats-new-azure-portal.md#quick-start-get-going-with-your-new-application-right-away) wystąpić.
2. Jeśli w aplikacji docelowej nie jest obecny w galerii, można "Przynieś własne aplikacji". Dowiedz się więcej: [What's new in zarządzania aplikacjami przedsiębiorstwa w usłudze Azure Active Directory: Dodawanie niestandardowych aplikacji w jednym miejscu](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

## <a name="saas-password-sso-configuration"></a>Konfiguracja rejestracji Jednokrotnej SaaS hasła

Przybliżona godzina ukończone: 15 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Środowisko testowe dla aplikacji SaaS. Przykładem hasło rejestracji Jednokrotnej jest HipChat i Twitter. Dla żadnej innej aplikacji należy dokładny adres URL strony z formularza html logowania. | [Na platformie Microsoft Azure Marketplace w usłudze Twitter](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[HipChat na platformie Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.hipchat) |
| Testowanie kont dla aplikacji. | [Załóż konto usługi Twitter](https://twitter.com/signup?lang=en)<br/>[Załóż bezpłatne konto: HipChat](https://www.hipchat.com/sign_up) |
| Docelowy zestaw użytkowników można przypisać do aplikacji. Grupa zabezpieczeń zawiera użytkowników jest zalecane. | [Przypisanie użytkownika lub grupę do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Prawa dostępu lokalnego administratora na komputerze, aby wdrożyć rozszerzenie Panel dostępu dla programu Internet Explorer, Chrome lub Firefox | [Rozszerzenia Panel dostępu dla programu Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Rozszerzenia Panel dostępu dla programu Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Rozszerzenia Panel dostępu dla przeglądarki Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Zainstaluj rozszerzenie przeglądarki | [Rozszerzenia Panel dostępu dla programu Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Rozszerzenia Panel dostępu dla programu Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Rozszerzenia Panel dostępu dla przeglądarki Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Skonfigurować aplikację z galerii | [What's new in zarządzania aplikacjami przedsiębiorstwa w usłudze Azure Active Directory: galerii nowych i ulepszonych aplikacji](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Skonfiguruj hasło logowania jednokrotnego | [Zarządzanie rejestracji jednokrotnej dla przedsiębiorstwa aplikacji w portalu Azure: opartego na hasłach logowania](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| Przypisywanie aplikacji do grupy określonej w wymaganiach wstępnych | [Przypisanie użytkownika lub grupę do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Zaloguj się do https://myapps.microsoft.com/ jako użytkownika testowego, które ma dostęp |  |
| Kliknij Kafelek dla aplikacji, która została właśnie utworzona. | [Co to jest Panel dostępu?: opartego na hasłach logowania jednokrotnego bez obsługi tożsamości](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Podaj poświadczenia aplikacji | [Co to jest Panel dostępu?: opartego na hasłach logowania jednokrotnego bez obsługi tożsamości](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Zamknij przeglądarkę i powtórz nazwy logowania. Użytkownik powinien go zobaczyć bezproblemowy dostęp do aplikacji. |  |
| Opcjonalnie można sprawdzić raporty użycia aplikacji. Należy zauważyć, że istnieje pewnego opóźnienia przesyłania, więc musisz Poczekaj chwilę, aby wyświetlić dane w raportach. | [Działania logowania raportów w portalu usługi Azure Active Directory: użycie aplikacji zarządzanej](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Azure Active Directory report retention policies](active-directory-reporting-retention.md) (Zasady przechowywania raportów w usłudze Azure Active Directory) |

### <a name="considerations"></a>Zagadnienia do rozważenia

Jeśli w aplikacji docelowej nie jest obecny w galerii, można "Przynieś własne aplikacji". Dowiedz się więcej: [What's new in zarządzania aplikacjami przedsiębiorstwa w usłudze Azure Active Directory: Dodawanie niestandardowych aplikacji w jednym miejscu](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Należy pamiętać, następujące wymagania:
   * Aplikacja powinna mieć adres URL logowania znane
   * Strona logowania powinna zawierać formularza HTML z jednego więcej pól tekstowych, które rozszerzenia przeglądarki może automatyczne wypełnianie. W polach minimum i powinien zawierać nazwę użytkownika i hasło.

## <a name="saas-shared-accounts-configuration"></a>SaaS kont konfiguracji udostępnionej

Przybliżona godzina ukończone: 30 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Lista aplikacji docelowej i dokładne logowania adresy URL wcześniejsze. Na przykład można użyć usługi Twitter. | [Na platformie Microsoft Azure Marketplace w usłudze Twitter](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[Załóż konto usługi Twitter](https://twitter.com/signup?lang=en) |
| Udostępnione poświadczenia dla tej aplikacji SaaS. | [Udostępnianie kont za pomocą usługi Azure AD](active-directory-sharing-accounts.md)<br/>[Hasło przerzucania dla usługi Facebook, Twitter i LinkedIn dostępna w wersji zapoznawczej usługi azure AD automatycznego! -Enterprise Mobility and Security Blog] (https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/ ) |
| Poświadczenia dla co najmniej dwóch członków zespołu, którzy będą uzyskiwać dostęp do tego samego konta. Należy do grupy zabezpieczeń. | [Przypisanie użytkownika lub grupę do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Prawa dostępu lokalnego administratora na komputerze, aby wdrożyć rozszerzenie Panel dostępu dla programu Internet Explorer, Chrome lub Firefox | [Rozszerzenia Panel dostępu dla programu Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Rozszerzenia Panel dostępu dla programu Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Rozszerzenia Panel dostępu dla przeglądarki Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Zainstaluj rozszerzenie przeglądarki | [Rozszerzenia Panel dostępu dla programu Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Rozszerzenia Panel dostępu dla programu Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Rozszerzenia Panel dostępu dla przeglądarki Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Skonfigurować aplikację z galerii | [What's new in zarządzania aplikacjami przedsiębiorstwa w usłudze Azure Active Directory: galerii nowych i ulepszonych aplikacji](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Skonfiguruj hasło logowania jednokrotnego | [Zarządzanie rejestracji jednokrotnej dla przedsiębiorstwa aplikacji w portalu Azure: opartego na hasłach logowania](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| Przypisywanie aplikacji do grupy określonej w wymaganiach wstępnych podczas przypisywania do nich poświadczeń | [Przypisanie użytkownika lub grupę do aplikacji przedsiębiorstwa w usłudze Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Zaloguj się jako inny użytkownik, aplikacja dostępu jako **sam udostępnionego konta.**  |  |
| Opcjonalnie można sprawdzić raporty użycia aplikacji. Należy zauważyć, że istnieje pewnego opóźnienia przesyłania, więc musisz Poczekaj chwilę, aby wyświetlić dane w raportach. | [Działania logowania raportów w portalu usługi Azure Active Directory: użycie aplikacji zarządzanej](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Azure Active Directory report retention policies](active-directory-reporting-retention.md) (Zasady przechowywania raportów w usłudze Azure Active Directory) |


### <a name="considerations"></a>Zagadnienia do rozważenia

Jeśli w aplikacji docelowej nie jest obecny w galerii, można "Przynieś własne aplikacji". Dowiedz się więcej: [What's new in zarządzania aplikacjami przedsiębiorstwa w usłudze Azure Active Directory: Dodawanie niestandardowych aplikacji w jednym miejscu](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Należy pamiętać, następujące wymagania:
   * Aplikacja powinna mieć adres URL logowania znane
   * Strona logowania powinna zawierać formularza HTML z jednego więcej pól tekstowych, które rozszerzenia przeglądarki może automatyczne wypełnianie. W polach minimum i powinien zawierać nazwę użytkownika i hasło.

## <a name="app-proxy-configuration"></a>Konfiguracja serwera Proxy aplikacji

Przybliżona godzina ukończone: 20 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Basic usługi Microsoft Azure AD lub subskrypcję usługi premium i katalog usługi Azure AD, dla którego jesteś administratorem globalnym | [Wersje usługi Azure Active Directory](active-directory-editions.md) |
| Aplikacja sieci web hostowanej lokalnego, który chcesz skonfigurować dla dostępu zdalnego |  |
| Serwer z systemem Windows Server 2012 R2 lub Windows 8.1 lub nowszym, na którym można zainstalować łącznik serwera Proxy aplikacji | [Zrozumienie łączniki serwera Proxy aplikacji usługi Azure AD](application-proxy-understand-connectors.md) |
| Jeśli w ścieżce znajduje się zapora, upewnij się, że jest on otwarty, aby łącznik mogą wysyłać żądania HTTPS (TCP) do serwera Proxy aplikacji | [Włącz serwer Proxy aplikacji w portalu Azure: wymagania wstępne serwera Proxy aplikacji](active-directory-application-proxy-enable.md#application-proxy-prerequisites) |
| Jeśli organizacja używa serwerów proxy, aby nawiązać połączenie z Internetem, wykonaj przyjrzeć się we wpisie blogu pracy z istniejących serwerów proxy lokalnymi szczegółowe informacje o sposobach ich konfigurowania | [Praca z istniejącym lokalnych serwerów proxy](application-proxy-working-with-proxy-servers.md) |


### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Zainstaluj łącznik na serwerze | [Włącz serwer Proxy aplikacji w portalu Azure: Instalowanie i rejestrowanie łącznika](active-directory-application-proxy-enable.md#install-and-register-a-connector) |
| Publikowanie aplikacji lokalnych w usłudze Azure AD jako aplikacja serwera Proxy aplikacji | [Publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-publish-azure-portal.md) |
| Przypisywanie użytkowników testowych | [Publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD: Dodaj użytkownika testowego](application-proxy-publish-azure-portal.md#add-a-test-user) |
| Opcjonalnie można skonfigurować pojedynczy jednokrotnego dla użytkowników | [Podaj rejestracji jednokrotnej z serwera Proxy aplikacji usługi Azure AD](application-proxy-sso-azure-portal.md) |
| Testowanie aplikacji, logując się do portalu MyApps jako przypisany użytkownik | https://myapps.microsoft.com |

### <a name="considerations"></a>Zagadnienia do rozważenia

1. Zaleca się umieszczanie łącznika w sieci firmowej, powodują zgłoszenia spowoduje wyświetlenie lepszą wydajność umieszczenie go w chmurze. Dowiedz się więcej: [zagadnienia dotyczące topologii sieci, używając serwera Proxy usługi Azure Active Directory aplikacji](application-proxy-network-topology-considerations.md)
2. Dalsze szczegóły zabezpieczeń i jak szczególnie bezpieczny dostęp zdalny zapewnia rozwiązanie utrzymując tylko połączeń wychodzących dla: [zagadnienia dotyczące zabezpieczeń do uzyskiwania dostępu do aplikacji zdalnie przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-security-considerations.md)

## <a name="generic-ldap-connector-configuration"></a>Ogólny łącznik LDAP konfiguracji

Przybliżona godzina ukończone: 60 minut

> [!IMPORTANT]
> Jest to Konfiguracja zaawansowana wymagające masz pewną znajomość programu FIM/MIM. Jeśli używane w środowisku produkcyjnym, zaleca się pytania dotyczące tej konfiguracji przejść przez [pomocy technicznej Premium](https://support.microsoft.com/premier).

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Azure AD Connect zainstalowane i skonfigurowane | Bloków konstrukcyjnych: [synchronizacja katalogów — synchronizacja skrótów haseł](#directory-synchronization--password-hash-sync-phs--new-installation) |
| Wymagania dotyczące spotkań wystąpienia ADLDS | [Ogólny łącznik LDAP informacje techniczne: omówienie ogólny łącznik LDAP](./connect/active-directory-aadconnectsync-connector-genericldap.md#overview-of-the-generic-ldap-connector) |
| Lista obciążeń, które użytkownicy korzystają z i atrybuty skojarzone z tych obciążeń. | [Synchronizacja programu Azure AD Connect: atrybuty synchronizowane z usługą Azure Active Directory](./connect/active-directory-aadconnectsync-attributes-synchronized.md) |


### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Dodaj ogólny łącznik LDAP | [Ogólny łącznik LDAP informacje techniczne: Tworzenie nowego łącznika](./connect/active-directory-aadconnectsync-connector-genericldap.md#create-a-new-connector) |
| Tworzenie profilów uruchamiania dla utworzonego łącznika (pełny import, import zmian, pełnej synchronizacji, w ramach synchronizacji przyrostowej, eksport) | [Tworzenie profilu uruchamiania agenta zarządzania](https://technet.microsoft.com/library/jj590219(v=ws.10).aspx)<br/> [Używanie łączników przy użyciu usługi Azure AD Connect synchronizacji Menedżera usług](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md)|
| Pełny import profilu uruchamiania i sprawdź, czy istnieją obiekty w przestrzeni łącznika | [Wyszukiwania dla obiekt miejsca łącznika](https://technet.microsoft.com/library/jj590287(v=ws.10).aspx)<br/>[Używanie łączników przy użyciu usługi Azure AD Connect synchronizacji Menedżera usług: przestrzeni łącznika wyszukiwania](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md#search-connector-space) |
| Tworzenie reguły synchronizacji tak, aby obiekty w magazynie Metaverse mają atrybuty niezbędne dla obciążeń | [Synchronizacja programu Azure AD Connect: najlepsze rozwiązania dotyczące zmieniania konfiguracji domyślnej: zmian reguł synchronizacji](./connect/active-directory-aadconnectsync-best-practices-changing-default-configuration.md#changes-to-synchronization-rules)<br/>[Synchronizacja programu Azure AD Connect: opis Aprowizacją deklaratywną](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)<br/>[Synchronizacja programu Azure AD Connect: opis deklaratywne wyrażenia inicjowania obsługi administracyjnej](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| Uruchom pełną synchronizację cyklu | [Synchronizacja programu Azure AD Connect: harmonogramu: uruchomiony harmonogram](./connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler) |
| W przypadku problemów czy Rozwiązywanie problemów | [Troubleshoot an object that is not synchronizing to Azure AD](./connect/active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) (Rozwiązywanie problemów z obiektem, który nie jest synchronizowany z usługą Azure AD) |
| Sprawdź, czy LDAP użytkownik może logowania i dostępu do aplikacji | https://myapps.microsoft.com |

### <a name="considerations"></a>Zagadnienia do rozważenia

> [!IMPORTANT]
> Jest to Konfiguracja zaawansowana wymagające masz pewną znajomość programu FIM/MIM. Jeśli używane w środowisku produkcyjnym, zaleca się pytania dotyczące tej konfiguracji przejść przez [pomocy technicznej Premium](https://support.microsoft.com/premier).

## <a name="groups---delegated-ownership"></a>Grupy - delegować prawa własności

Przybliżona godzina ukończone: 10 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Aplikacja SaaS (federacyjnej usługi logowania jednokrotnego lub hasło logowania jednokrotnego) został już skonfigurowany | Bloków konstrukcyjnych: [SaaS Federacyjna usługa rejestracji Jednokrotnej w konfiguracji](#saas-federated-sso-configuration) |
| Określono grupy chmury, przypisany dostęp do aplikacji w #1 | Bloków konstrukcyjnych: [SaaS Federacyjna usługa rejestracji Jednokrotnej w konfiguracji](#saas-federated-sso-configuration) <br/>[Utwórz grupy i Dodaj elementy członkowskie w usłudze Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Dostępne są poświadczenia dla właściciela grupy | [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](active-directory-manage-groups.md) |
| Wykryto poświadczeń do uzyskiwania dostępu do aplikacji Pracownik przetwarzający informacje | [Co to jest Panel dostępu?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Identyfikator grupy, który ma zostać przyznany dostęp do aplikacji i skonfigurować właścicielem danej grupy| [Zarządzanie ustawieniami grupy w usłudze Azure Active Directory ](active-directory-groups-settings-azure-portal.md) |
| Zaloguj się jako właściciel grupy, zobacz członkostwo w grupach na karcie panelu dostępu | [Strony Zarządzanie grupami w usłudze Active Directory systemu Azure](https://account.activedirectory.windowsazure.com/r/#/groups) |
| Dodaj Pracownik przetwarzający informacje, które ma zostać przetestowana. |  |
| Zaloguj się jako pracownik przetwarzający informacje, upewnij się, że Kafelek jest dostępny | [Co to jest Panel dostępu?](active-directory-saas-access-panel-introduction.md) |

### <a name="considerations"></a>Zagadnienia do rozważenia

Jeśli aplikacja ma inicjowania obsługi administracyjnej włączone, może być konieczne Poczekaj kilka minut na zakończenie inicjowania obsługi przed uzyskaniem dostępu do aplikacji jako pracownik przetwarzający informacje.

## <a name="saas-and-identity-lifecycle"></a>SaaS i cyklem życia tożsamości

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Aplikacja SaaS (federacyjnej usługi logowania jednokrotnego lub hasło logowania jednokrotnego) został już skonfigurowany | Bloków konstrukcyjnych: [SaaS Federacyjna usługa rejestracji Jednokrotnej w konfiguracji](#saas-federated-sso-configuration) |
| Określono grupy chmury, przypisany dostęp do aplikacji w #1 | Bloków konstrukcyjnych: [SaaS Federacyjna usługa rejestracji Jednokrotnej w konfiguracji](#saas-federated-sso-configuration) <br/>[Utwórz grupy i Dodaj elementy członkowskie w usłudze Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Wykryto poświadczeń do uzyskiwania dostępu do aplikacji Pracownik przetwarzający informacje | [Co to jest Panel dostępu?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Usuń użytkownika z grupy, których aplikacja jest przypisany do | [Zarządzanie członkostwami grup użytkowników w dzierżawie usługi Azure Active Directory](active-directory-groups-members-azure-portal.md) |
| Poczekaj kilka minut, aż anulowanie obsługi. | [Automatyczne inicjowanie obsługi użytkowników aplikacji SaaS w usłudze Azure AD: jak działa automatycznego inicjowania obsługi administracyjnej?](active-directory-saas-app-provisioning.md#how-does-automatic-provisioning-work) |
| Na sesję przeglądarki oddzielne zalogować się jako pracownik przetwarzający informacje do mojej aplikacji portalu i upewnij się, brak tego kafelka | http://myapps.microsoft.com |


### <a name="considerations"></a>Zagadnienia do rozważenia

Ekstrapolować scenariusz weryfikacji koncepcji leavers i/lub urlopu scenariuszy. Jeśli użytkownik jest wyłączony w lokalnej usłudze AD lub usunięty, nie jest już sposobem logowania do aplikacji SaaS.

## <a name="self-service-access-to-application-management"></a>Samoobsługowego dostępu do zarządzania aplikacjami

Przybliżona godzina ukończone: 10 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Identyfikowanie użytkowników fazy weryfikacji Koncepcji, żądających dostępu do aplikacji, w ramach grupy zabezpieczeń | Bloków konstrukcyjnych: [SaaS Federacyjna usługa rejestracji Jednokrotnej w konfiguracji](#saas-federated-sso-configuration) |
| Wdrożona aplikacja docelowa | Bloków konstrukcyjnych: [SaaS Federacyjna usługa rejestracji Jednokrotnej w konfiguracji](#saas-federated-sso-configuration) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Przejdź do bloku przedsiębiorstwa aplikacji w portalu zarządzania usługi Azure AD | [Portalu zarządzania usługi Azure AD: Aplikacje dla przedsiębiorstw](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) |
| Skonfiguruj aplikację z wstępne z samoobsługi | [What's new in zarządzania aplikacjami przedsiębiorstwa w usłudze Azure Active Directory: Konfigurowanie dostępu do aplikacji Sklep internetowy](active-directory-enterprise-apps-whats-new-azure-portal.md#configure-self-service-application-access) |
| Zaloguj się jako pracownik przetwarzający informacje do mojej aplikacji portalu | http://myapps.microsoft.com |
| Zwróć uwagę, "+ Dodaj aplikację" przycisk na op strony. Użyj, aby uzyskać dostęp do aplikacji |  |

### <a name="considerations"></a>Zagadnienia do rozważenia

Wybrane aplikacje mogą mieć, inicjowanie obsługi wymagań, tak przejście bezpośrednio do aplikacji może spowodować błędy. Jeśli wybrana aplikacja to umożliwia inicjowanie obsługi administracyjnej z usługą azure ad i jest skonfigurowana, tej opcji można użyć jako okazji do wyświetlenia całego przepływu pracy na trasie. Zobacz blokiem [SaaS Federacyjna usługa rejestracji Jednokrotnej w konfiguracji](#saas-federated-sso-configuration) dla dalszego zalecenia

## <a name="self-service-password-reset"></a>Samodzielne resetowanie hasła

Przybliżona godzina ukończone: 15 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Włącz zarządzanie samoobsługi hasła w dzierżawie. | [Azure Active Directory resetowania hasła dla administratorów IT](active-directory-passwords-update-your-own-password.md) |
| Włączanie zapisywania zwrotnego haseł do zarządzania hasłami z lokalnymi. Uwaga wymaga określonej usługi Azure AD Connect wersji | [Wymagania wstępne dotyczące funkcji zapisywania zwrotnego haseł](authentication/howto-sspr-writeback.md) |
| Zidentyfikuj użytkowników fazy weryfikacji koncepcji, które będą korzystać z tej funkcji i upewnij się, że są oni członkami grupy zabezpieczeń. Użytkownicy muszą być z systemem innym niż administratorzy pełni prezentować możliwości | [Dostosowanie: Zarządzanie hasłami AD platformy Azure: ograniczanie dostępu do resetowania hasła](authentication/howto-sspr-writeback.md) |


### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Przejdź do portalu zarządzania usługi Azure AD: resetowanie hasła | [Portalu zarządzania usługi Azure AD: Resetowanie hasła](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) |
| Określa zasady resetowania hasła. Do celów weryfikacji Koncepcji można użyć połączenia telefonicznego i Q & A. Zalecane jest aby włączyć rejestrację, aby wymagać Zaloguj się do panelu dostępu |  |
| Wyloguj się i zaloguj się jako pracownik przetwarzający informacje |  |
| Źródło danych samoobsługowego resetowania hasła, zgodnie z konfiguracją na krok 2 | https://aka.ms/ssprsetup |
| Zamknij przeglądarkę |  |
| Rozpoczęcie procesu logowania jako pracownik przetwarzający informacje używane w kroku 4 |  |
| Resetowanie hasła | [Zaktualizuj własnego hasła: resetowania hasła](active-directory-passwords-update-your-own-password.md) |
| Spróbuj zalogować się przy użyciu nowego hasła do usługi Azure AD, jak również do zasobów lokalnych |  |

### <a name="considerations"></a>Zagadnienia do rozważenia

1. Jeśli uaktualnienie Azure AD Connect będzie powodować tarcia, należy rozważyć użycie go przed kontach w chmurze lub była pokaz oddzielne środowisku
2. Administratorzy mają inne zasady i zresetuj hasło przy użyciu konta administratora może skażenia fazy weryfikacji koncepcji i dezorientację. Upewnij się, że operacje resetowania testu za pomocą konta zwykłych użytkowników


## <a name="azure-multi-factor-authentication-with-phone-calls"></a>Uwierzytelnianie wieloskładnikowe platformy Azure z połączeń telefonicznych

Przybliżona godzina ukończone: 10 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Identyfikowanie użytkowników fazy weryfikacji Koncepcji, które będą korzystać z usługi MFA  |  |
| Telefon z dobrym odbioru dla żądania usługi MFA  | [Co to jest usługa Multi-Factor Authentication platformy Azure?](authentication/multi-factor-authentication.md) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Przejdź do bloku "Użytkownicy i grupy" w portalu zarządzania usługi Azure AD | [Portalu zarządzania Azure AD: Użytkownicy i grupy](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Overview/menuId/) |
| Wybierz blok "Wszyscy użytkownicy" |  |
| W górnym pasku, wybierz przycisk "Usługi Multi-Factor Authentication" | Bezpośredni adres URL do portalu usługi Azure MFA: https://aka.ms/mfaportal |
| W ustawieniach "Użytkownika" Wybierz użytkowników, aby zapewnić i włączyć je do usługi MFA | [Stany użytkowników w usłudze Azure Multi-Factor Authentication](authentication/howto-mfa-userstates.md) |
| Zaloguj się jako użytkownika fazy weryfikacji koncepcji i przechodzenia przez proces dowód w górę  |  |

### <a name="considerations"></a>Zagadnienia do rozważenia

1. Kroki weryfikacji koncepcji tego bloku konstrukcyjnego jawne ustawienie usługi MFA dla użytkownika na wszystkich identyfikatorów logowania. Brak innych narzędzi, takich jak dostęp warunkowy i ochronę tożsamości, które stosują usługę MFA na więcej określonych scenariuszach. Są to coś wziąć pod uwagę podczas przenoszenia z fazy weryfikacji Koncepcji do środowiska produkcyjnego.
2. Kroki weryfikacji koncepcji, w tym bloku konstrukcyjnego jawnie korzystają z połączeń telefonicznych jako metody uwierzytelniania Wieloskładnikowego dla expedience. Przejście z fazy weryfikacji Koncepcji do środowiska produkcyjnego zaleca się przy użyciu aplikacji, takich jak [Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) jako Twoje czynnika, jeśli to możliwe.
Dowiedz się więcej: [projekt NIST Special Publication 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="mfa-conditional-access-for-saas-applications"></a>Dostęp warunkowy usługi MFA dla aplikacji SaaS

Przybliżona godzina ukończone: 10 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Zidentyfikuj użytkowników fazy weryfikacji koncepcji pod kątem zasad. Ci użytkownicy musi należeć do grupy zabezpieczeń, aby określić zakres zasad dostępu warunkowego | [Konfiguracja federacyjną rejestracją Jednokrotną w modelu SaaS](#saas-federated-sso-configuration) |
| Aplikacja SaaS został już skonfigurowany |  |
| Aby zapewnić użytkownikom są już przypisane do aplikacji |  |
| Poświadczenia użytkownika fazy weryfikacji Koncepcji są dostępne |  |
| Aby Zapewnić użytkownik jest zarejestrowany w usłudze MFA. Za pomocą telefonu z dobrym odbioru | https://aka.ms/ssprsetup |
| Urządzenia w sieci wewnętrznej. Adres IP skonfigurowany w zakresie wewnętrznym adresem | Znajdowanie adresu ip: https://www.bing.com/search?q=what%27s+my+ip |
| Urządzenia w sieci zewnętrznej (może to być telefon przy użyciu operatora sieci komórkowej) |  |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Przejdź do portalu zarządzania usługi Azure AD: bloku dostępu warunkowego | [Portalu zarządzania usługi Azure AD: Warunkowy dostęp](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) |
| Tworzenie zasad dostępu warunkowego:<br/>-Użytkownicy fazy weryfikacji koncepcji docelowych w obszarze "Użytkownicy i grupy"<br/>— Aplikacja fazy weryfikacji koncepcji docelowa w obszarze "Aplikacje w chmurze"<br/>-Target wszystkich lokalizacji z wyjątkiem tych zaufanych "Warunkach" -> "W lokalizacji" **Uwaga:** listę zaufanych adresów IP są konfigurowane w [Portal usługi MFA](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx)<br/>-Wymusić uwierzytelnianie wieloskładnikowe w obszarze "Grant" | [Rozpoczynanie pracy z dostępu warunkowego w usłudze Azure Active Directory: kroki konfiguracji zasad](active-directory-conditional-access-azure-portal-get-started.md#policy-configuration-steps) |
| Dostęp do aplikacji z wewnątrz sieci firmowej | [Rozpoczynanie pracy z dostępu warunkowego w usłudze Azure Active Directory: testowania zasad](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |
| Dostęp do aplikacji w sieci publicznej | [Rozpoczynanie pracy z dostępu warunkowego w usłudze Azure Active Directory: testowania zasad](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |

### <a name="considerations"></a>Zagadnienia do rozważenia

Jeśli używasz federacyjnych, można użyć lokalnego dostawcy tożsamości (IdP) do komunikacji wewnątrz/poza siecią firmową stanu z oświadczeń. Można użyć tej metody bez konieczności zarządzania na liście adresów IP, które mogą być skomplikowane, aby ocenić i zarządzanie nimi w dużych organizacjach. W tej konfiguracji należy konta dla scenariusza "roaming sieci" (zalogowanie się użytkownika z sieci wewnętrznej, a podczas przełączniki zalogowany na przykład w kawiarni) i upewnij się, że rozumiesz konsekwencje. Dowiedz się więcej: [zabezpieczanie zasobów w chmurze Azure Multi-Factor Authentication i usług AD FS: zaufanych adresów IP dla użytkowników federacyjnych](authentication/howto-mfa-adfs.md#trusted-ips-for-federated-users)

## <a name="privileged-identity-management-pim"></a>Zarządzanie tożsamościami uprzywilejowanymi (PIM)

Przybliżona godzina ukończone: 15 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Zidentyfikuj Administrator globalny, który będzie częścią fazy weryfikacji Koncepcji PIM | [Rozpoczynanie korzystania z usługi Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) |
| Zidentyfikuj Administrator globalny, który ma zostać Administrator zabezpieczeń | [Rozpoczynanie korzystania z usługi Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)<br/> [Różne role administracyjne w usłudze Azure Active Directory PIM](active-directory-privileged-identity-management-roles.md) |
| Opcjonalnie: Upewnij się, jeśli Administratorzy globalni mają dostęp do poczty e-mail do wykonywania powiadomienia e-mail w PIM | [Co to jest usługa Azure AD Privileged Identity Management?: Konfigurowanie ustawień roli aktywacji](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)


### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Zaloguj się do https://portal.azure.com jako administrator globalny (GA) i ładowania początkowego bloku PIM. Administrator globalny, który wykonuje ten krok jest obsługiwany jako administratora zabezpieczeń.  Umożliwia wywołanie tego aktora GA1 | [Za pomocą Kreatora zabezpieczeń w usłudze Azure AD Privileged Identity Management](active-directory-privileged-identity-management-security-wizard.md) |
| Zidentyfikuj administratora globalnego i przenieś je z stałe kwalifikujących się. Powinno to być oddzielne admin niż tego używanego w kroku 1 dla uzyskania przejrzystości. Umożliwia wywołanie tego aktora GA2 | [Azure AD Privileged Identity Management: Jak dodać lub usunąć rolę użytkownika](active-directory-privileged-identity-management-how-to-add-role-to-user.md)<br/>[Co to jest usługa Azure AD Privileged Identity Management?: Konfigurowanie ustawień roli aktywacji](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)  |
| Teraz, zaloguj się jako GA2 do https://portal.azure.com i spróbuj zmienić "Ustawienia użytkownika". Zwróć uwagę, że niektóre opcje są wygaszone. | |
| Na nowej karcie i w tej samej sesji w kroku 3, teraz przejdź do https://portal.azure.com i Dodaj blok PIM do pulpitu nawigacyjnego. | [Jak aktywować lub dezaktywować role w programie Azure AD Privileged Identity Management: Dodawanie aplikacji Privileged Identity Management](active-directory-privileged-identity-management-how-to-activate-role.md#add-the-privileged-identity-management-application) |
| Żądanie aktywacji do roli administratora globalnego | [Jak aktywować lub dezaktywować role w programie Azure AD Privileged Identity Management: aktywować rolę](active-directory-privileged-identity-management-how-to-activate-role.md#activate-a-role) |
| Należy pamiętać, że GA2 nigdy nie zarejestrowała się w usłudze MFA, rejestracji dla usługi Azure MFA będzie to konieczne |  |
| Przejdź wstecz do oryginalnego karty w kroku 3, a następnie kliknij przycisk Odśwież w przeglądarce. Należy pamiętać, że masz teraz dostęp do ustawień "użytkownika" | |
| Opcjonalnie Jeśli Twoje Administratorzy globalni mają włączone poczty e-mail, można sprawdzić GA1 i GA2 w skrzynce odbiorczej i wyświetlone powiadomienie roli aktywowane |  |
| 8 Sprawdź Historia inspekcji i sprawdź, że raport, aby potwierdzić podniesienie GA2 jest widoczny. | [Co to jest usługa Azure AD Privileged Identity Management?: Przejrzyj działania roli](active-directory-privileged-identity-management-configure.md#review-role-activity) |

### <a name="considerations"></a>Zagadnienia do rozważenia

Ta funkcja jest częścią usługi Azure AD Premium P2 i/lub EMS E5

## <a name="discovering-risk-events"></a>Wykrywanie zdarzenia ryzyka

Przybliżona godzina ukończone: 20 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Urządzenia z przeglądarką Tor pobrane i zainstalowane | [Pobierz Tor przeglądarki](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Dostęp do weryfikacji Koncepcji użytkownika do logowania | [Azure podręcznikowym ochronę tożsamości w usłudze Active Directory](active-directory-identityprotection-playbook.md) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Otwórz tor przeglądarki | [Pobierz Tor przeglądarki](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Zaloguj się do https://myapps.microsoft.com przy użyciu konta użytkownika w fazie weryfikacji Koncepcji | [Azure Active Directory Identity Protection podręcznika dotyczącego: Symulowanie zdarzeń o podwyższonym ryzyku](active-directory-identityprotection-playbook.md#simulating-risk-events) |
| Zaczekaj 5-7 minut |  |
| Zaloguj się jako administrator globalny do https://portal.azure.com i otwórz blok Identity Protection | https://aka.ms/aadipgetstarted |
| Otwiera blok zdarzenia ryzyka. Powinien zostać wyświetlony w obszarze "Logowania z anonimowych adresów IP"  | [Azure Active Directory Identity Protection podręcznika dotyczącego: Symulowanie zdarzeń o podwyższonym ryzyku](active-directory-identityprotection-playbook.md#simulating-risk-events) |

### <a name="considerations"></a>Zagadnienia do rozważenia

Ta funkcja jest częścią usługi Azure AD Premium P2 i/lub EMS E5

## <a name="deploying-sign-in-risk-policies"></a>Wdrażanie zasad logowania ryzyka

Przybliżona godzina ukończone: 10 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Urządzenia z przeglądarką Tor pobrane i zainstalowane | [Pobierz Tor przeglądarki](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Dostęp jako użytkownik fazy weryfikacji Koncepcji należy dziennika podczas testowania |  |
| Aby Zapewnić użytkownik jest zarejestrowany za pomocą usługi MFA. Upewnij się, że używać telefonu z dobrym odbioru | Bloków konstrukcyjnych: [uwierzytelnianie wieloskładnikowe platformy Azure z połączeń telefonicznych](#azure-multi-factor-authentication-with-phone-calls) |


### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Zaloguj się jako administrator globalny do https://portal.azure.com i otwórz blok Identity Protection | https://aka.ms/aadipgetstarted |
| Włącz zasady logowania ryzyko w następujący sposób:<br/>-Przypisane do: fazy weryfikacji Koncepcji użytkownika<br/>— Warunki: Logowania ryzyka średnia lub nowszej (logowania z anonimowych lokalizacji jest traktowany jako poziom ryzyka średnia)<br/>-Formanty: Wymagają usługi MFA | [Azure Active Directory Identity Protection podręcznika dotyczącego: ryzyka logowania](active-directory-identityprotection-playbook.md) |
| Otwórz tor przeglądarki | [Pobierz Tor przeglądarki](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Zaloguj się do https://myapps.microsoft.com przy użyciu konta użytkownika w fazie weryfikacji koncepcji |  |
| Zwróć uwagę, żądanie uwierzytelniania MFA | [Logowanie napotyka przy użyciu usługi Azure AD Identity Protection: ryzykowne odzyskiwania logowania](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

### <a name="considerations"></a>Zagadnienia do rozważenia

Ta funkcja jest częścią usługi Azure AD Premium P2 i/lub EMS E5. Aby dowiedzieć się więcej na temat zdarzeń o podwyższonym ryzyku odwiedź: [zdarzenia o podwyższonym ryzyku usługi Azure Active Directory](active-directory-reporting-risk-events.md)

## <a name="configuring-certificate-based-authentication"></a>Konfigurowanie uwierzytelniania opartego na certyfikatach

Przybliżona godzina, aby zakończyć: 20 minut

### <a name="pre-requisites"></a>Wymagania wstępne

| Wymagania wstępne | Zasoby |
| --- | --- |
| Urządzenia z certyfikatu użytkownika udostępniane (z systemem Windows, iOS lub Android) z infrastruktury kluczy publicznych przedsiębiorstwa | [Wdrażanie certyfikatów użytkowników](https://msdn.microsoft.com/library/cc770857.aspx) |
| Sfederowane domenowych Azure AD przy użyciu usług AD FS | [Program Azure AD Connect a federacja](./connect/active-directory-aadconnectfed-whatis.md)<br/>[Usługi certyfikatów Active Directory — omówienie](https://technet.microsoft.com/library/hh831740.aspx)|
| Dla urządzeń z systemem iOS mieć zainstalowanej aplikacji Microsoft Authenticator | [Wprowadzenie do aplikacji Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

### <a name="steps"></a>Kroki

| Krok | Zasoby |
| --- | --- |
| Włączyć "Uwierzytelnianie certyfikatu" w usługach AD FS | [Konfigurowanie zasad uwierzytelniania: Aby skonfigurować uwierzytelnianie podstawowe globalnie w systemie Windows Server 2012 R2](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-authentication-policies#to-configure-primary-authentication-globally-in-windows-server-2012-r2) |
| Opcjonalnie: Włącz uwierzytelnianie certyfikatu w usłudze Azure AD dla klientów programu Exchange Active Sync | [Wprowadzenie do uwierzytelniania opartego na certyfikacie w usłudze Azure Active Directory](active-directory-certificate-based-authentication-get-started.md) |
| Przejdź do panelu dostępu i uwierzytelniania za pomocą certyfikatu użytkownika | https://myapps.microsoft.com |

### <a name="considerations"></a>Zagadnienia do rozważenia

Aby dowiedzieć się więcej na temat ostrzeżenia tego wdrożenia można znaleźć: [usług AD FS: uwierzytelnianie certyfikatu z usługą Azure AD & usługi Office 365](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)



> [!NOTE]
> Posiadanie certyfikatu użytkownika powinien być chroniony. Przy użyciu zarządzania urządzeniami lub kod PIN w przypadku kart inteligentnych.



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
