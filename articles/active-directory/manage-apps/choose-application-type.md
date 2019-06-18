---
title: Jak wybrać typ aplikacji do użycia podczas dodawania aplikacji | Dokumentacja firmy Microsoft
description: Omówienie obsługiwanych typów aplikacji, które można zintegrować z usługą Azure AD i ich powiązane opcje konfiguracji
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30a4bfa2cf7a39a28db0575a6851f5b7b481524c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388169"
---
# <a name="choosing-the-application-type-when-adding-an-application-in-azure-active-directory"></a>Wybieranie typu aplikacji, podczas dodawania aplikacji w usłudze Azure Active Directory
Zapoznaj się z czterech typów aplikacji, które można dodać do usługi Azure Active Directory (Azure AD). Podczas dodawania aplikacji w usłudze Azure Active Directory, zostanie wyświetlony monit wybrać jeden typ cztery aplikacji. 

## <a name="what-are-the-types-of-applications"></a>Co to są typy aplikacji?

Usługa Azure AD obsługuje cztery typy aplikacji głównej, które można dodać za pomocą **Dodaj** funkcja dostępna w ramach **aplikacje dla przedsiębiorstw**. Należą do nich:

-   **Aplikacje galerii usługi Azure AD** — aplikacji, który został wstępnie zintegrowanych, aby uzyskać logowanie jednokrotne z usługą Azure AD.

-   **Aplikacje serwera Proxy aplikacji** — aplikacja działająca w środowisku w środowisku lokalnym, które chcesz udostępnić bezpiecznego logowania jednokrotnego na zewnątrz.

-   **Niestandardowej aplikacji** — aplikacji, które Twoja organizacja chce programowanie na platformie Azure programowanie aplikacji usługi AD, ale który nie istnieje jeszcze.

-   **Aplikacje spoza galerii** — Przenoszenie własnych aplikacji! Dowolny link sieci web, który ma, każda aplikacja, która renderuje pole nazwy użytkownika i hasła, obsługuje protokoły SAML lub OpenID Connect lub obsługuje standard SCIM, którą chcesz zintegrować, aby logowanie jednokrotne z usługą Azure AD.

## <a name="features-and-capabilities-supported-by-the-application-types"></a>Funkcje i możliwości obsługiwany przez typy aplikacji

Następujące funkcje są obsługiwane przez żaden z powyższych typów cztery aplikacji w usłudze Azure AD:

-   **Szybki start** — Rozpocznij pracę z usługą aplikacji, szybko wykonując [kroki proste wdrożenie](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)

-   **Ogólne właściwości zarządzania** — Pobierz [głębokiego łącza bezpośrednie](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) do aplikacji, [dostosowanie oznakowania](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) aplikacji, lub [wyłączyć aplikację](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) dla wszystkich użytkowników.

-   **Zarządzanie użytkownikami i grupami** — [przypisać](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) lub [Usuń](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) użytkowników i grup do aplikacji i opcjonalnie przypisywać role określonej aplikacji tych użytkowników i grupy mają dostęp do

-   **Dostęp do aplikacji samoobsługowej** — pozwalają użytkownikom na żądanie [samoobsługowego dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) do aplikacji w swoich panelach dostępu aplikacji, albo poprzez dodanie aplikacji bezpośrednio lub [ Dołączanie do grupy włączono Samoobsługowe](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)opcjonalnie wymaganiu zatwierdzania firm po drodze

-   **Dzienniki logowania** — zobacz [wszystkich logowań do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins), lub wszystkie swoje aplikacje

-   **Dzienniki inspekcji** — zobacz [szczegółowe dzienniki inspekcji dotyczące zmian w aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs), lub do wszystkich aplikacji

-   **Dostęp warunkowy i opartych na ryzykach** — ustawienie Zaawansowane [reguły dostępu na podstawie warunku](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) obowiązują, gdy użytkownicy próbują zalogować się do konkretnej aplikacji

-   **Uprawnienia widok** — przeglądać [uprawnień OAuth2](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) aplikacja ma dostęp do umieść w katalogu z jednej

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Logowania jednokrotnego i aprowizacji trybów obsługiwanych przez aplikacji o określonych typach

W poniższej tabeli opisano różne pojedynczego logowania jednokrotnego i aprowizacji tryby obsługiwanych przez każdą z powyższych typów aplikacji. Aby ułatwić zrozumienie aplikacji, które należy dodać do obsługi określonego celu, można użyć tej tabeli.

  ![Tabela typów aplikacji](./media/choose-application-type/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Jak wybrać jedno trybu logowania jednokrotnego

Obsługiwane są następujące **logowanie jednokrotne** tryby aplikacji usługi Azure AD.

- **Azure AD logowanie jednokrotne wyłączone** — Wybieranie usługi Azure AD logowanie jednokrotne wyłączone **tryb rejestracji jednokrotnej** Jeśli nie są jeszcze gotowe, aby połączyć tę aplikację za pomocą logowania jednokrotnego z usługą Azure AD lub ją po prostu testowania

- **Połączone logowanie jednokrotne** — wybierz [połączone logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) **tryb rejestracji jednokrotnej** Jeśli masz aplikację, która jest już połączony z istniejącym pojedynczego logowania jednokrotnego rozwiązaniu lub jeśli chcesz, aby Publikowanie proste łącze dla użytkowników w ich [panelu dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) lub [uruchamiania aplikacji usługi Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

- **Logowanie na podstawie hasła** — wybierz [opartego na hasłach logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) **tryb rejestracji jednokrotnej** Jeśli aplikacja renderuje pola HTML nazwę użytkownika i hasło, a chcesz przechowywać danej nazwy użytkownika i bezpieczne hasło i odtworzone później w aplikacji

- **Logowanie na podstawie protokołu SAML** — wybierz [opartej na SAML logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) logowania jednokrotnego w trybie, jeśli aplikacja obsługuje protokoły SAML lub OpenID Connect lub jeśli chcesz mieć możliwość zmapowania użytkowników do ról aplikacji, na podstawie reguł należy zdefiniować w swoje oświadczenia języka SAML *

  >[!NOTE]
  >Ta opcja nie jest dostępna, gdy serwer proxy aplikacji jest skonfigurowana dla aplikacji.
  >
  >

- **Logowanie na podstawie nagłówka** — wybierz tę opcję, [logowanie na podstawie nagłówka](application-proxy-configure-single-sign-on-with-ping-access.md) pojedynczego tryb logowania jednokrotnego w przypadku aplikacji przy użyciu PingAccess, która obsługuje uwierzytelnianie na podstawie nagłówków HTTP, który chcesz wykonać logowania jednokrotnego do 

  >[!NOTE]
  >Ta opcja jest dostępna tylko w przypadku skonfigurowania serwera proxy aplikacji i PingAccess dla aplikacji.
  >
  >

- **Zintegrowane uwierzytelnianie Windows** — wybierz [zintegrowane uwierzytelnianie Windows](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) logowania jednokrotnego w trybie, podczas udostępniania, którą chcesz wykonać logowania jednokrotnego do aplikacji WIA w środowisku lokalnym 

  >[!NOTE]
  >Ta opcja jest dostępna tylko w przypadku, gdy serwer proxy aplikacji jest skonfigurowana dla aplikacji.
  >
  >

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Tryby pojedynczego logowania jednokrotnego dla aplikacji niestandardowej

Aplikacji niestandardowych opracowanych za pomocą aplikacji niestandardowej również środowisko pomocy technicznej dodatkowe pojedynczego logowania jednokrotnego tryby nie wymienionych powyżej, obejmujące:

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) logowania opartego na

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) logowania opartego na

-   [WS-Federation 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) logowania opartego na

-   [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) logowania opartego na

Odczyt [przewodnik dewelopera usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) Aby dowiedzieć się więcej o sposobie tworzenia aplikacji niestandardowej, obsługującego pojedynczego logowania jednokrotnego trybów.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Jak skonfigurować aplikację pojedynczego tryb logowania jednokrotnego

Aby ustawić aplikacji **logowanie jednokrotne** tryb, wykonaj te instrukcje:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, dla której chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

## <a name="how-to-choose-a-provisioning-mode"></a>Jak wybrać tryb obsługi administracyjnej

- **Aprowizacja ręczna** — wybierz [ręczne](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) tryb obsługi administracyjnej, jeśli masz istniejące konta lub chcesz zarządzać kontami dla tej aplikacji spoza usługi Azure AD.

- **Automatyczne Aprowizowanie** — wybierz [automatyczne](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) **tryb obsługi administracyjnej** Jeśli chcesz włączyć automatyczną aprowizację oparte na interfejsie API i/lub anulowania obsługi administracyjnej kont użytkowników do tej aplikacji 

  >[!NOTE]
  >Ta opcja jest dostępna tylko dla aplikacji w ramach **polecane** kategorii [galerii aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal).
  >
  >

- **Standard SCIM automatycznego inicjowania obsługi opartej na** — użyj [Standard SCIM automatycznego inicjowania obsługi opartej na](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) Jeśli aplikacja obsługuje protokół Standard SCIM wykrywania zmian dla użytkowników i grup, które są automatycznie emitowane zmian Każda aplikacja zintegrowana z usługą Azure AD 

  >[!NOTE]
  >Ta opcja nie jest wymieniony jako określony tryb inicjowania obsługi administracyjnej, ale jest domyślnie włączona dla wszystkich aplikacji, które są zintegrowane z usługą Azure AD.
  >
  >

## <a name="how-to-set-an-applications-provisioning-mode"></a>Jak skonfigurować aplikację w tryb obsługi administracyjnej

Można ustawić aplikacji **aprowizacji** tryb, wykonaj te instrukcje:

Aby ustawić aplikacji **logowanie jednokrotne** tryb, wykonaj te instrukcje:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, dla którego chcesz skonfigurować aprowizację.

7. Po załadowaniu aplikacji, kliknij przycisk **aprowizacji** menu nawigacji po lewej stronie aplikacji.

## <a name="next-steps"></a>Kolejne kroki
[Managing Applications with Azure Active Directory (Zarządzanie aplikacjami za pomocą usługi Azure Active Directory)](what-is-application-management.md)
