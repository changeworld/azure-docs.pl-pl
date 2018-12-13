---
title: Co to jest portal MyApps, w usłudze Azure Active Directory? | Microsoft Docs
description: Dowiedz się, jak używać odmiany portalu MyApps (przeglądarki sieci web, aplikacji dla systemu Android, aplikacji dla telefonu iPhone i iPad) dostęp do aplikacji SaaS.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.reviewer: asteen
ms.openlocfilehash: 129f77ec9ea4d4e2633e1ea0b02795bc0df26433
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834710"
---
# <a name="what-is-the-myapps-portal"></a>Co to jest MyApps portal?

Jeśli masz służbowe konto w usłudze Azure Active Directory (Azure AD), można użyć portalu Moje aplikacje sieci web do wyświetlania i uruchamiania aplikacji opartych na chmurze, których administrator usługi Azure AD udzielił zyskujesz dostęp do. Można również użyć grupa samoobsługi i możliwości zarządzania aplikacjami za pośrednictwem portalu MyApps.

Portalu MyApps różni się w witrynie Azure portal. Nie wymagają posiadania subskrypcji platformy Azure.

![Portalu MyApps][1] za pomocą portalu MyApps, można edytować niektórych ustawień profilu i wykonaj następujące czynności:

- Zmień hasło skojarzone z konta służbowego lub szkolnego.

- Edytuj ustawienia resetowania hasła.

- Edytuj ustawienia skontaktuj się z pomocą i preferencji, które są związane z uwierzytelnianiem usługi Multi-Factor Authentication (dla kont, które są wymagane do używania go przez administratora).

- Wyświetl konto szczegóły, takie jak identyfikator użytkownika, alternatywny adres e-mail, numerów telefonów komórkowych i pakietu office i urządzeń.

- Wyświetl i uruchamiania aplikacji opartych na chmurze, których administrator usługi Azure AD udzielił, zyskujesz dostęp do. 

- Samodzielnie zarządzać grupami. Administratorzy mogą tworzyć i zarządzać grupami zabezpieczeń i żądanie członkostwa w grupach zabezpieczeń w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Samoobsługowe zarządzanie grupami dla użytkowników w usłudze Azure AD](../users-groups-roles/groups-self-service-management.md) i [Zarządzanie grupami](../fundamentals/active-directory-manage-groups.md).

## <a name="access-the-myapps-portal"></a>Przejdź do portalu MyApps

Możesz uzyskać dostęp do portalu MyApps, przechodząc do `https://myapps.microsoft.com`.

Jeśli masz niestandardowe znakowanie skonfigurowana dla strony logowania, należy załadować znakowanie, dodając domeny Twojej organizacji do adresu URL (na przykład `https://myapps.microsoft.com/<your domain>.com`).

Możesz użyć dowolnej nazwy aktywnych lub zweryfikowanej domeny, skonfigurowanego w portalu Azure, jak pokazano poniżej: ![nazwa domeny firmy Wingtip Toys][2]  

Dystrybuuj adres URL, aby wszyscy użytkownicy logujący się do aplikacji, które są zintegrowane z usługą Azure AD.

## <a name="authentication"></a>Authentication

Aby uzyskać dostęp do portalu MyApps, użytkownik musi zostać uwierzytelniony przy użyciu konta służbowego w usłudze Azure AD. Możesz zostać uwierzytelniony do usługi Azure AD bezpośrednio. Alternatywnie Jeśli organizacja skonfigurował Federacji przy użyciu usługi Active Directory Federation Services (AD FS) lub inne technologie, możesz zostać uwierzytelniony przez usługi Active Directory systemu Windows Server.

Jeśli masz subskrypcję platformy Azure lub usługi Office 365 i masz doświadczenie z witryny Azure portal lub aplikacji usługi Office 365, można wyświetlić listę aplikacji, bez ponownego logowania. Jeśli nie jesteś uwierzytelniony, monit logowania przy użyciu nazwy użytkownika i hasła dla konta w usłudze Azure AD. Jeśli Twoja organizacja skonfigurowała Federacji, wpisując nazwę użytkownika jest wystarczająca.

Gdy użytkownik jest uwierzytelniony, możesz porozmawiać z aplikacji, które administrator została zintegrowana z katalogiem. Aby dowiedzieć się, jak zintegrować aplikacje z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="web-browser-requirements"></a>Wymagania dotyczące przeglądarki sieci Web

Co najmniej portalu MyApps wymaga przeglądarki, która obsługuje język JavaScript i CSS została włączona. Aby zarejestrować się w usłudze aplikacji za pomocą opartego na hasłach logowanie jednokrotne (SSO), musi mieć zainstalowane w przeglądarce rozszerzenie portalu MyApps. Rozszerzenie jest automatycznie pobierany po wybraniu aplikacji, która jest skonfigurowana na podstawie hasła logowania jednokrotnego.

Instalator jest architektury. Jeśli klikniesz link do pobierania, tylko Pobierz instalatora dla architektury systemu operacyjnego, które są aktualnie uruchomione na. Jeśli jesteś administratorem wdrożenia aplikacji, upewnij się, skorzystaj z linku pobierania z 64-bitowe i 32-bitowe urządzenie, aby oba instalatory.


Rozszerzenie portalu MyApps jest obecnie dostępna dla:
- **Krawędź**: Windows 10 Anniversary Edition lub nowszy. 
- **Dla programu Chrome**: Windows 7 lub nowszy i System MacOS x lub później.
- **Firefox 26.0 lub nowsze**: na Windows XP z dodatkiem SP2 lub nowszym i Mac OS X 10.6 lub później.
- **Internet Explorer 11**: Windows 7 lub nowszy (ograniczona obsługa).

## <a name="my-apps-secure-sign-in-extension"></a>Rozszerzenie bezpiecznego logowania do moich aplikacji
Aby logować się na podstawie hasła logowania jednokrotnego, należy użyć rozszerzenia. Po zainstalowaniu rozszerzenia użytkownik może zalogować się w niej włączyć dodatkowe funkcje, wybierając **Zaloguj się rozpocząć**. 

- Zaloguj się do aplikacji bezpośrednio za pomocą aplikacji **adres URL logowania**. Korzystając z adresu URL aplikacji, rozszerzenie wykrywa akcji i zapewnia możliwość logowania z rozszerzenia.
- Aby uruchomić aplikacji z poziomu portalu MyApps przy użyciu *szybkie wyszukiwanie* funkcji rozszerzenia. 
- Rozszerzenie pokazuje ostatnie trzy aplikacje, które można uruchomić **ostatnio używane** sekcji.
- Możesz użyć firmy wewnętrznych adresów URL, podczas pracy zdalnej za pośrednictwem [serwera Proxy aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)

> [!NOTE]
> Dodatkowe funkcje są dostępne tylko dla przeglądarki Microsoft Edge, Chrome i Firefox.
>
Możesz pobrać rozszerzenia bezpośrednio z następujących witryn:
- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

Jeśli używasz Mój adres URL aplikacji innych niż `https://myapps.microsoft.com`, skonfigurować domyślny adres URL, wykonując następujące czynności:
1. Gdy jesteś *nie* logowanie się do rozszerzenia, kliknij prawym przyciskiem myszy ikonę rozszerzenia.
2. W menu, wybierz **adres URL aplikacji Mój**.
3. Wybierz domyślny adres URL.
4. Wybierz ikonę rozszerzenia.
5. Wybierz **Zaloguj się rozpocząć**.

Aby użyć wewnętrznego firmy adresy URL, podczas pracy zdalnej przy użyciu rozszerzenia, wykonaj następujące czynności:
1. [Skonfiguruj serwer Proxy aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) w dzierżawie.
2. [Opublikuj aplikację](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) i adresem URL za pośrednictwem serwera Proxy aplikacji.
3. Zainstaluj rozszerzenie, a następnie zalogować się w niej, wybierając pozycję Zaloguj się na rozpoczęcie pracy.
4. Możesz teraz przejść do adresu URL wewnętrzne firmy, nawet w trakcie zdalnego.

> [!NOTE]
> Mogą także wyłączyć automatyczne przekierowanie do adresów URL firmy koło zębate ustawień w menu głównym a zaznaczając **poza** firmy wewnętrzny adres URL przekierowania opcji.


## <a name="mobile-app-support"></a>Obsługa aplikacji mobilnych

Zespół usługi Azure Active Directory publikuje aplikację mobilną Moje aplikacje. Po zainstalowaniu aplikacji, możesz zalogować się do hasła logowania jednokrotnego aplikacji na urządzeniach z systemem Android i iOS.

> [!NOTE]
> Możesz zalogować się do aplikacji, które obsługują federacji z usługą Azure AD (w tym Salesforce, Google Apps, Dropbox, Box, Concur, Workday, usługi Office 365 i ponad 70 innych) na obsługujących praktycznie dowolną przeglądarkę sieci web na dowolnym urządzeniu, bez konieczności używania aplikacji wtyczki lub urządzeniu przenośnym. Do użycia na urządzeniu przenośnym, druga [napotyka portalu MyApps](https://myapps.microsoft.com/) również nie wymagają aplikacji mobilnej Moje aplikacje.

### <a name="my-apps-for-iphone-and-ipad"></a>Moje aplikacje dla urządzeń iPhone i tabletu iPad

Moje aplikacje dla systemu iOS jest obsługiwana w żadnych iPhone lub iPad, z systemem iOS w wersji 7 lub nowszy.  

Jest on dostępny w [Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![Moje aplikacje dla systemu iOS][4]    


## <a name="intune-managed-browser-for-my-apps"></a>Intune Managed Browser dla mojej aplikacji

Moje aplikacje jest także zintegrowana z aplikacji Intune Managed Browser. Intune Managed Browser dla systemu iOS i android ułatwia bezpieczniej przeglądać i przejdź do strony sieci Web, który może zawierać informacje o firmie, ułatwiając dostarczanie bardziej bezpieczne środowisko przeglądania sieci web.  

Możesz uzyskać na Moje aplikacje z obie strony głównej Managed Browser i zakładek, co oznacza brak mniejszej liczbie kliknięć wymaganych do przejścia aplikacji.

Intune Managed Browser znajduje się w temacie [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) i [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser).

![Programu Managed browser dla środowiska Moje aplikacje][5]    


## <a name="tips-for-testing-the-user-experience"></a>Porady dotyczące testowania interfejsu użytkownika

Jeśli jesteś administratorem platformy Azure, a użytkownik jest zalogowany do witryny Azure portal przy użyciu konta w katalogu, użytkownik są automatycznie zalogowany do portalu MyApps jako bieżącego konta. Ten widok przedstawia wszystkie aplikacje, które są przypisane do użytkownika.

Testowanie w *różnych* użytkownika konta, wykonaj następujące czynności:

1. W prawym górnym rogu witryny Azure portal lub portalu MyApps, wybierz **Wyloguj**. 
2. Przejdź do [portalu MyApps](https://myapps.microsoft.com).
3. Na stronie logowania wpisz nazwę użytkownika i hasło dla konta w katalogu, który ma zostać przetestowana.


## <a name="starting-applications"></a>Uruchamianie aplikacji

W tej sekcji omówiono kilka typów aplikacji, które mogą być wyświetlane w portalu MyApps.

### <a name="office-365-applications"></a>Aplikacje usługi Office 365

Jeśli organizacja korzysta z aplikacjami usługi Office 365, a masz licencję dla nich aplikacje usługi Office 365 są wyświetlane w portalu MyApps.

Po wybraniu kafelka aplikacji dla aplikacji usługi Office 365, są przekierowywane do aplikacji i zostanie automatycznie zalogowany.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Aplikacje firmy Microsoft i innych firm, skonfigurowaną opartych na federacyjnego logowania jednokrotnego

Administrator może dodać aplikacje w sekcji usługi Active Directory w witrynie Azure Portal przy użyciu trybu logowania jednokrotnego, ustaw **usługi Azure AD logowania jednokrotnego**. Te aplikacje jest widoczny tylko wtedy, gdy administrator ma jawnie przyznane uprawnienia dostępu do nich.

Po wybraniu kafelka dla aplikacji, są przekierowywane i zostanie automatycznie zalogowany do niego.

### <a name="password-based-sso-without-identity-provisioning"></a>Na podstawie hasła logowania jednokrotnego bez tożsamości inicjowania obsługi administracyjnej.

Administrator może dodać aplikacje w sekcji usługi Active Directory w witrynie Azure Portal przy użyciu trybu logowania jednokrotnego, ustaw **opartego na hasłach logowania jednokrotnego**. Wszyscy użytkownicy w katalogu widoczne wszystkie aplikacje, które zostały skonfigurowane w tym trybie.

Wybierz Kafelek aplikacji po raz pierwszy monit Zainstaluj wtyczkę logowania jednokrotnego haseł dla programu Internet Explorer lub Chrome. Instalacja może wymagać ponownego uruchomienia przeglądarki sieci web. Gdy powróć do portalu MyApps i ponownie wybierz Kafelek aplikacji, zostanie wyświetlony monit o nazwę użytkownika i hasło dla aplikacji. Po wprowadzeniu nazwy użytkownika i hasła, poświadczenia są bezpiecznie przechowywane i połączone z Twoim kontem w usłudze Azure AD.

Następnym razem, wybierz Kafelek aplikacji, możesz automatycznie zalogowano się w aplikacji.  

Nie musisz wprowadzać poświadczeń ponownie i zainstaluj wtyczkę logowania jednokrotnego hasła.

Jeśli poświadczenia zostały zmienione w aplikacji docelowej innej firmy, należy również zaktualizować swoje poświadczenia, które są przechowywane w usłudze Azure AD. 

Aby zaktualizować swoje poświadczenia, wykonaj następujące czynności:

1. Wybierz ikonę na kafelku aplikacji.
2. Wybierz **zaktualizować poświadczenia** ponownie wprowadzić nazwę użytkownika i hasło dla aplikacji.


### <a name="password-based-sso-with-identity-provisioning"></a>Oparte na hasłach logowania jednokrotnego przy użyciu tożsamości inicjowania obsługi administracyjnej.

Administrator może dodać aplikacje w sekcji usługi Active Directory w witrynie Azure Portal przy użyciu trybu logowania jednokrotnego, ustaw **opartego na hasłach logowania jednokrotnego**, wraz z aprowizacji tożsamości.

Wybierz Kafelek aplikacji po raz pierwszy monit Zainstaluj wtyczkę logowania jednokrotnego haseł dla programu Internet Explorer lub Chrome. Instalacja może wymagać ponownego uruchomienia przeglądarki sieci web.  

Gdy wróć do portalu MyApps i ponownie, wybierz Kafelek aplikacji jest automatycznie zalogowany do aplikacji.

Niektóre aplikacje może być konieczna zmiana hasła podczas pierwszego logowania. Jeśli poświadczenia zostały zmienione w aplikacji docelowej innej firmy, należy również zaktualizować poświadczenia, które są przechowywane w usłudze Azure AD. 

Aby zaktualizować swoje poświadczenia, wykonaj następujące czynności:

1. Wybierz ikonę na kafelku aplikacji.
2. Wybierz **zaktualizować poświadczenia** ponownie wprowadzić nazwę użytkownika i hasło dla aplikacji.


### <a name="application-with-existing-sso-solutions"></a>Aplikacja z istniejącymi rozwiązaniami logowania jednokrotnego

Aby skonfigurować logowanie Jednokrotne dla aplikacji, witryny Azure portal zapewnia trzecią opcję o nazwie istniejące logowanie jednokrotne. Ta opcja umożliwia administratorem, aby utworzyć link do aplikacji i umieścić go w portalu MyApps dla wybranych użytkowników.

Na przykład jeśli aplikacja jest skonfigurowana do uwierzytelniania użytkowników za pomocą usług AD FS 2.0, administrator może opcja istniejących logowania jednokrotnego spowoduje utworzenie łącza do niego w portalu MyApps. Gdy uzyskujesz dostęp do łącza, są uwierzytelniane za pośrednictwem usług AD FS 2.0 lub niezależnie od istniejącej rejestracji Jednokrotnej rozwiązania przez aplikację.


## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się o zarządzanie aplikacjami, zobacz [zarządzania aplikacjami w usłudze Azure Active Directory](../manage-apps/what-is-application-management.md).
 
- Aby dowiedzieć się, jak zintegrować aplikację SaaS z usługą Azure AD, zobacz [listę samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md).
 
- Aby dowiedzieć się więcej o zarządzaniu aplikacjami w usłudze Azure AD, zobacz [wprowadzenie pojedynczego logowania jednokrotnego i zarządzanie nimi dostępu do aplikacji za pomocą usługi Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
 
- Aby dowiedzieć się więcej na temat inicjowania obsługi użytkowników, zobacz [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS](../manage-apps/user-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
