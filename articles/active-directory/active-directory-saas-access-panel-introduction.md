---
title: Co to jest panel dostępu w usłudze Azure Active Directory? | Microsoft Docs
description: Dowiedz się, jak używać różnych wersji panelu dostępu (przeglądarki sieci web, aplikacji systemu Android, aplikacji iPhone i iPad) dostępu do aplikacji SaaS.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2018
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c8ea85a0906071d6b30f176de7509e5f346b513
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-the-access-panel"></a>Co to jest panel dostępu?

Panel dostępu jest portalem sieci web. Jeśli masz służbowego lub szkolnego konta w usłudze Azure Active Directory (Azure AD), służy panelu dostępu do wyświetlania i uruchamiania aplikacji opartej na chmurze, które administrator usługi Azure AD udzielił dostęp do. Można również użyć grupami samoobsługi i funkcje zarządzania aplikacjami za pomocą panelu dostępu.

Panel dostępu jest oddzielony od portalu Azure. Nie wymagają posiadania subskrypcji platformy Azure.

![Panel dostępu][1] , korzystając z panelu dostępu, możesz edytować niektóre ustawienia profilu i wykonaj następujące czynności:

- Zmień hasło skojarzone z konta służbowego.

- Edytuj ustawienia resetowania hasła.

- Edytuj ustawienia kontaktowe i preferencje dotyczące uwierzytelniania wieloskładnikowego (dla kont, które są wymagane do używania go przez administratora).

- Wyświetl konta alternatywne szczegóły, takie jak nazwa użytkownika, poczty e-mail, numerów telefonów mobile i pakietu office i urządzeń.

- Wyświetl i uruchamiania aplikacji opartej na chmurze, które administrator usługi Azure AD udzielił dostęp do. 

- Własnym Zarządzanie grupami. Administratorzy mogą tworzyć i Zarządzaj grupami zabezpieczeń i żądania członkostwa w grupach zabezpieczeń w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Samoobsługowe zarządzanie grupami użytkowników w usłudze Azure AD](active-directory-accessmanagement-self-service-group-management.md) i [Zarządzanie grupami](active-directory-manage-groups.md).




## <a name="access-the-access-panel"></a>Dostęp do panelu dostępu

Dostęp do panelu dostępu, przechodząc do `http://myapps.microsoft.com`.

Jeśli masz niestandardowe znakowanie skonfigurowana dla strony logowania, można załadować znakowania przez dołączenie do adresu URL domeny organizacji (na przykład `http://myapps.microsoft.com/<your domain>.com`).

Można użyć dowolnej nazwy aktywnych lub zweryfikowanej domeny, który został skonfigurowany w portalu usługi Azure, jak pokazano poniżej: ![Wingtip Toys nazwy domeny][2]  

Dystrybuuj adresu URL dla wszystkich użytkowników, zaloguj się do aplikacji, które są zintegrowane z usługą Azure AD.

## <a name="authentication"></a>Authentication

Aby uzyskać dostęp do panelu dostępu, użytkownik musi zostać uwierzytelniony przy użyciu konta służbowego w usłudze Azure AD. Użytkownik może zostać uwierzytelniony do usługi Azure AD bezpośrednio. Alternatywnie Jeśli organizacji skonfigurował Federacji przy użyciu usługi Active Directory Federation Services (AD FS) ani innych technologii, możesz mogą być uwierzytelniane przez usługę Active Directory systemu Windows Server.

Ma subskrypcji platformy Azure lub usługi Office 365, której używasz portalu Azure lub aplikacji pakietu Office 365 można wyświetlić listę aplikacji bez zalogować się ponownie. Jeśli użytkownik nie jest uwierzytelniony, zostanie wyświetlony monit logowania przy użyciu nazwy użytkownika i hasło dla swojego konta w usłudze Azure AD. Jeśli organizacji skonfigurował federacyjnych, wpisując nazwę użytkownika jest wystarczająca.

Gdy użytkownik jest uwierzytelniony, mogą współdziałać z aplikacjami, które administrator ma zintegrowane z katalogiem. Aby dowiedzieć się, jak integrować aplikacje z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="web-browser-requirements"></a>Wymagania dotyczące przeglądarki sieci Web

Co najmniej panelu dostępu wymaga przeglądarki obsługującej JavaScript i włączył CSS. Zalogowani do aplikacji za pomocą opartego na hasłach rejestracji jednokrotnej (SSO), musi mieć rozszerzenie panelu dostępu, które są zainstalowane w przeglądarce. Rozszerzenie jest pobierany automatycznie po wybraniu aplikacji, która jest skonfigurowana do opartego na hasłach logowania jednokrotnego.

Instalator jest architektury. Po kliknięciu łącza pobierania tylko Pobierz Instalator dla architektury systemu operacyjnego, które są obecnie uruchomione na. Jeśli jesteś administratorem wdrożenia aplikacji, upewnij się, skorzystaj z łącza pobierania z 64-bitowe i 32-bitowych urządzenie, aby oba pliki instalacyjne.


Rozszerzenie panelu dostępu jest obecnie dostępny dla:
- **Krawędź**: w systemie Windows 10 Anniversary Edition lub nowszy. 
- **Chrome**: w systemie Windows 7 lub nowszym i System MacOS x lub później.
- **Firefox 26.0 lub nowsze**: w systemie Windows XP SP2 lub nowszy i Mac OS X 10.6 lub później.
- **Internet Explorer 11**: w systemie Windows 7 lub nowszy (obsługa ograniczona).

## <a name="my-apps-secure-sign-in-extension"></a>Rozszerzenie bezpiecznego logowania do moich aplikacji
Aby zalogować się do opartego na hasłach logowanie jednokrotne, należy użyć rozszerzenia. Po zainstalowaniu rozszerzenia może logowania się do niego do włączyć dodatkowe funkcje, wybierając **Zaloguj się rozpocząć**. 

- Zaloguj się do aplikacji za pomocą aplikacji **adres URL logowania**. Korzystając z adresu URL aplikacji, rozszerzenia wykrywa akcji i daje możliwość logowania z rozszerzenia.
- Aby uruchomić aplikacji z poziomu panelu dostępu za pomocą *szybkiego wyszukiwania* funkcji rozszerzenia. 
- Rozszerzenie zawiera ostatnich trzech aplikacji, które można uruchomić w **ostatnio używane** sekcji.

> [!NOTE]
> Dodatkowe funkcje są dostępne tylko w przypadku krawędzi, Chrome i Firefox.
>

Jeśli używasz Mój adres URL aplikacji innych niż `https://myapps.microsoft.com`, skonfiguruj adres URL domyślnej przez wykonanie następujących czynności:
1. Podczas *nie* zalogowany do rozszerzenia, kliknij prawym przyciskiem myszy ikonę rozszerzenia.
2. W menu, wybierz **adres URL aplikacji Mój**.
3. Wybierz adres URL domyślnej.
4. Wybierz ikonę rozszerzenia.
5. Wybierz **Zaloguj się rozpocząć**.

## <a name="mobile-app-support"></a>Obsługa aplikacji mobilnej

Zespół usługi Azure Active Directory publikuje Moje aplikacje aplikacji mobilnej. Po zainstalowaniu aplikacji można logowania się do aplikacji opartych na hasło logowania jednokrotnego w systemach iOS i urządzeniach z systemem Android.

> [!NOTE]
> Można logowania się do aplikacji, które obsługują federacji z usługą Azure AD (w tym Salesforce, usługi Google Apps, Dropbox, pole, Concur, produktu Workday, usługi Office 365 i więcej niż 70 innych użytkowników) w praktycznie dowolnej przeglądarki sieci web, na dowolnym urządzeniu, bez konieczności wtyczki lub przenośnych aplikacji. Do użycia na urządzeniu przenośnym, innych [dostęp do środowiska panelu](https://myapps.microsoft.com/) również nie wymagają aplikacji mobilnej Moje aplikacje.
>
>

### <a name="my-apps-for-android"></a>Moje aplikacje dla systemu Android

Moje aplikacje dla systemu Android jest obsługiwana na dowolnym urządzeniu z systemem Android jest uruchomiona wersja systemu Android 4.1 lub nowszą.  

Jest ona dostępna w [sklepu Google Play](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![Moje aplikacje dla systemu Android][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>Moje aplikacje dla urządzenia iPhone i iPad

Moje aplikacje dla systemu iOS jest obsługiwana na wszystkie urządzenia iPhone lub iPad z systemem iOS w wersji 7 lub nowszy.  

Jest ona dostępna w [sklepu Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![Moje aplikacje dla systemu iOS][4]    


## <a name="managed-browser-for-my-apps"></a>Zarządzane przeglądarki pod kątem Moje aplikacje

Moje aplikacje są również zintegrowane z Intune Managed Browser. Intune Managed Browser dla urządzeń iOS i Android odgrywa kluczową rolę w by upewnić się, że dane na urządzeniach przenośnych jest bezpieczne. Przeglądarki umożliwia bezpieczne przeglądanie i przejdź do strony sieci Web, który może zawierać informacje o firmie, a także pomaga zapewnić bezpieczne środowisko przeglądania sieci web.  

Możesz uzyskać szybki dostęp do Moje aplikacje na stronie głównej Managed Browser oraz w zakładki, dzięki mniejszej liczby kliknięć są wymagane do dowolnej aplikacji, którego chcesz uzyskać dostęp.

Intune Managed Browser są dostępne pod adresem [sklepu Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) i [sklepu Google Play](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en).

![Zarządzane przeglądarki pod kątem Moje aplikacje][5]    


## <a name="tips-for-testing-the-user-experience"></a>Porady dotyczące testowania czynności użytkownika

Jeśli jesteś administratorem systemu Azure i użytkownik jest zalogowany do portalu Azure przy użyciu konta w katalogu, użytkownik zostanie automatycznie zarejestrowany do panelu dostępu jako swoim aktualnym kontem. Ten widok przedstawia wszystkie aplikacje, które są przypisane do Ciebie.

Aby przetestować w *różnych* użytkownika konto, wykonaj następujące czynności:

1. W prawym górnym rogu portalu Azure lub panel dostępu, wybierz **Wyloguj**. 
2. Przejdź do [panelu dostępu](http://myapps.microsoft.com).
3. Na stronie logowania wpisz nazwę użytkownika i hasło dla konta w katalogu, który ma zostać przetestowana.


## <a name="starting-applications"></a>Uruchamianie aplikacji

W tej sekcji omówiono kilka typów aplikacji, które mogą być wyświetlane w panelu dostępu.

### <a name="office-365-applications"></a>Aplikacje pakietu Office 365

Jeśli organizacja korzysta z aplikacji usługi Office 365, jest udzielana dla nich aplikacje pakietu Office 365 pojawiają się na Twoje panelu dostępu.

Po wybraniu kafelka aplikacji dla aplikacji usługi Office 365 są przekierowywane do aplikacji i zostanie automatycznie zalogowany.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Aplikacje firmy Microsoft i innych firm skonfigurowaną na podstawie federacyjnego logowania jednokrotnego

Administrator może dodać aplikacje w sekcji usługi Active Directory w portalu Azure w trybie logowania jednokrotnego ustawioną **Azure AD rejestracji jednokrotnej**. Te aplikacje jest widoczny tylko wtedy, gdy administrator ma jawnie przyznane uprawnienia dostępu do nich.

Po wybraniu kafelka aplikacji są przekierowywane i zostanie automatycznie zalogowany do niego.

### <a name="password-based-sso-without-identity-provisioning"></a>Na podstawie hasła logowania jednokrotnego bez obsługi tożsamości

Administrator może dodać aplikacje w sekcji usługi Active Directory w portalu Azure w trybie logowania jednokrotnego ustawioną **opartego na hasłach rejestracji jednokrotnej**. Wszyscy użytkownicy w katalogu widzą wszystkie aplikacje, które zostały skonfigurowane w tym trybie.

Wybierz kafelka aplikacji po raz pierwszy monit Zainstaluj wtyczkę logowania jednokrotnego hasła dla programu Internet Explorer lub przeglądarki Chrome. Instalacja może wymagać ponownego uruchomienia przeglądarki sieci web. Po powrocie do panelu dostępu i ponownie wybierz Kafelek aplikacji, monit o nazwę użytkownika i hasło dla aplikacji. Po wprowadzeniu nazwy użytkownika i hasła, poświadczenia są bezpiecznie przechowywane i połączony z kontem w usłudze Azure AD.

Przy następnym wybierz Kafelek aplikacji, użytkownik zostanie automatycznie zarejestrowany aplikacji.  

Nie trzeba ponownie wprowadź swoje poświadczenia i/lub Zainstaluj wtyczkę logowania jednokrotnego hasła.

Jeśli poświadczenia zostały zmienione w aplikacji docelowej innych firm, należy również zaktualizować swoje poświadczenia, które są przechowywane w usłudze Azure AD. 

Aby zaktualizować swoje poświadczenia, wykonaj następujące czynności:

1. Wybierz ikonę na kafelku aplikacji.
2. Wybierz **zaktualizować poświadczenia** ponownie wprowadzić nazwę użytkownika i hasło dla aplikacji.


### <a name="password-based-sso-with-identity-provisioning"></a>Oparte na hasłach rejestracji Jednokrotnej z inicjowaniem obsługi administracyjnej tożsamości

Administrator może dodać aplikacje w sekcji usługi Active Directory w portalu Azure w trybie logowania jednokrotnego ustawioną **opartego na hasłach rejestracji jednokrotnej**, oraz inicjowanie obsługi tożsamości.

Wybierz kafelka aplikacji po raz pierwszy monit Zainstaluj wtyczkę logowania jednokrotnego hasła dla programu Internet Explorer lub przeglądarki Chrome. Instalacja może wymagać ponownego uruchomienia przeglądarki sieci web.  

Po powrocie do panelu dostępu i ponownie wybierz Kafelek aplikacji, użytkownik zostanie automatycznie zarejestrowany aplikacji.

Niektóre aplikacje mogą wymagać zmiany hasła przy pierwszym logowaniu. Jeśli poświadczenia zostały zmienione w aplikacji docelowej innych firm, należy również zaktualizować poświadczenia, które są przechowywane w usłudze Azure AD. 

Aby zaktualizować swoje poświadczenia, wykonaj następujące czynności:

1. Wybierz ikonę na kafelku aplikacji.
2. Wybierz **zaktualizować poświadczenia** ponownie wprowadzić nazwę użytkownika i hasło dla aplikacji.


### <a name="application-with-existing-sso-solutions"></a>Aplikacja z istniejącymi rozwiązaniami do logowania jednokrotnego

Aby skonfigurować logowanie Jednokrotne dla aplikacji, portalu Azure udostępnia trzecia opcja o nazwie istniejących rejestracji jednokrotnej. Ta opcja umożliwia administratorem, aby utworzyć łącze do aplikacji i umieść ją na panel dostępu dla wybranych użytkowników.

Na przykład jeśli aplikacja jest skonfigurowana do uwierzytelniania użytkowników za pomocą usług AD FS 2.0, administrator może opcja istniejących rejestracji jednokrotnej do utworzenia na panelu dostępu do niej łącza. Gdy uzyskujesz dostęp do łącza, są uwierzytelniane za pośrednictwem usług AD FS 2.0 lub niezależnie od istniejącej rejestracji Jednokrotnej rozwiązania przez aplikację.


## <a name="next-steps"></a>Kolejne kroki

- Aby wyświetlić listę wszystkich tematów, które są powiązane z zarządzaniem aplikacjami, zobacz [indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](active-directory-apps-index.md).
 
- Aby uzyskać informacje o integracji aplikacji SaaS z usługą Azure AD, zobacz [lista samouczków dotyczących sposobów integracji aplikacji SaaS](active-directory-saas-tutorial-list.md).
 
- Aby dowiedzieć się więcej o zarządzaniu aplikacjami za pomocą usługi Azure AD, zobacz [wprowadzenie do uzyskania dostępu do pojedynczego aplikacji logowania jednokrotnego i zarządzanie nimi w usłudze Azure Active Directory](active-directory-appssoaccess-whatis.md).
 
- Aby dowiedzieć się więcej na temat Inicjowanie obsługi użytkowników, zobacz [zautomatyzować użytkownika alokowania i anulowania alokowania do aplikacji SaaS](active-directory-saas-app-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
