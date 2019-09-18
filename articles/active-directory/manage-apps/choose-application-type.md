---
title: Jak wybrać typ aplikacji, który ma być używany podczas dodawania aplikacji | Microsoft Docs
description: Poznaj obsługiwane typy aplikacji, które można zintegrować z usługą Azure AD i powiązane z nimi opcje konfiguracji
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
ms.date: 09/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: d5bd2397c345a4f670bde343f751cd69f825ecb9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056062"
---
# <a name="choosing-the-application-type-when-adding-an-application-in-azure-active-directory"></a>Wybieranie typu aplikacji podczas dodawania aplikacji w Azure Active Directory

Poznaj cztery typy aplikacji, które można dodać do Azure Active Directory (Azure AD). Po dodaniu aplikacji w Azure Active Directory zostanie wyświetlony monit o wybranie jednego z czterech typów aplikacji.

## <a name="what-are-the-types-of-applications"></a>Jakie są typy aplikacji?

Usługa Azure AD obsługuje cztery główne typy aplikacji, które można dodać za pomocą funkcji **Dodaj** znalezionej w obszarze **aplikacje dla przedsiębiorstw**. Należą do nich:

- **Aplikacje z galerii usługi Azure AD** — aplikacja, która została wstępnie zintegrowana na potrzeby logowania jednokrotnego w usłudze Azure AD.

- **Aplikacje serwera proxy aplikacji** — aplikacja działająca w środowisku lokalnym, która ma zapewnić bezpieczne logowanie jednokrotne do zewnętrznego.

- **Aplikacje opracowane niestandardowo** — aplikacja, którą organizacja chce opracowywać na platformie tworzenia aplikacji usługi Azure AD, ale może jeszcze nie istnieć.

- **Aplikacje spoza galerii** — przenoszenie własnych aplikacji Dowolny link internetowy lub każda aplikacja, która renderuje pole nazwy użytkownika i hasła, obsługuje protokoły protokołu SAML lub OpenID Connect Connect lub obsługuje standard scim, które chcesz zintegrować z logowaniem jednokrotnym w usłudze Azure AD.

## <a name="features-and-capabilities-supported-by-the-application-types"></a>Funkcje i możliwości obsługiwane przez typy aplikacji

Następujące funkcje są obsługiwane przez którykolwiek z czterech powyższych typów aplikacji w usłudze Azure AD:

- **Szybki Start** — szybkie rozpoczynanie pracy z aplikacją przez wykonanie [prostych kroków wdrażania](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)

- **Ogólne zarządzanie właściwościami** — umożliwia uzyskanie [bezpośredniego linku](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) do aplikacji, [dostosowanie znakowania](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) aplikacji lub [wyłączenie aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) dla wszystkich użytkowników.

- **Zarządzanie użytkownikami i grupami** — [przypisywanie](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) lub [usuwanie](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) użytkowników i grup do aplikacji oraz opcjonalnie przypisywanie określonych ról aplikacji, do których Ci użytkownicy i grupy mają dostęp

- **Samoobsługowy dostęp do aplikacji** — umożliwia użytkownikom zażądanie samoobsługowego [dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) do aplikacji ze swoich paneli dostępu do aplikacji przez dodanie aplikacji bezpośrednio lub [dołączenie do grupy z włączonymi samoobsługą. ](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), opcjonalnie wymaganie zatwierdzenia biznesowego w sposób

- **Dzienniki logowania** — zobacz [wszystkie logowania do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)lub wszystkie aplikacje

- **Dzienniki inspekcji** — zobacz [szczegółowe dzienniki inspekcji dotyczące modyfikacji aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)lub wszystkich aplikacji

- **Dostęp warunkowy i oparty na ryzyku** — Ustawianie zaawansowanych [reguł dostępu opartych na warunkach](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) , które są wymuszane, gdy użytkownicy próbują zalogować się do określonej aplikacji

- **Widok uprawnień** — Wyświetl dowolne [uprawnienia OAuth2](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) , do których aplikacja ma dostęp w katalogu z jednego miejsca

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Logowanie jednokrotne i tryby aprowizacji obsługiwane przez określone typy aplikacji

W poniższej tabeli opisano różne tryby logowania jednokrotnego i inicjowania obsługi administracyjnej obsługiwane przez poszczególne typy aplikacji. Za pomocą tej tabeli można zrozumieć, która aplikacja musi zostać dodana do obsługi określonego celu.

  ![Tabele Różne tryby logowania jednokrotnego i obsługi administracyjnej obsługiwane przez poszczególne typy aplikacji](./media/choose-application-type/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Jak wybrać tryb logowania jednokrotnego

Poniżej przedstawiono Obsługiwane tryby **logowania** jednokrotnego dla aplikacji usługi Azure AD.

- Logowanie jednokrotne **usługi Azure AD jest wyłączone** — wybierz opcję Logowanie jednokrotne w usłudze Azure AD w **trybie rejestracji** jednokrotnej, jeśli nie masz jeszcze gotowości do integracji tej aplikacji z logowaniem jednokrotnym za pomocą usługi Azure AD lub po prostu przetestowanie jej

- **Połączenie połączone** — wybierz **tryb** logowania jednokrotnego [logowania,](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) Jeśli masz aplikację, która jest już połączona z istniejącym rozwiązaniem rejestracji jednokrotnej lub jeśli chcesz po prostu opublikować proste łącze dla użytkowników w ich [ Panel dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) i [Uruchamianie aplikacji pakietu Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

- **Logowanie oparte na hasłach** — wybierz **tryb** logowania jednokrotnego [opartego na hasłach](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) , jeśli aplikacja renderuje pole nazwy użytkownika i hasła w formacie HTML i chcesz bezpiecznie zapisać tę nazwę użytkownika i hasło do Aplikacja później

- **Logowanie oparte** na protokole SAML — wybierz tryb logowania jednokrotnego [opartego na](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) protokole SAML, jeśli aplikacja obsługuje protokoły połączenia SAML lub OpenID Connect, lub chcesz mieć możliwość mapowania użytkowników do określonych ról aplikacji na podstawie reguł zdefiniowanych w elemencie SAML oświadczeń

  >[!NOTE]
  >Ta opcja jest niedostępna, gdy serwer proxy aplikacji jest skonfigurowany dla aplikacji.

- **Logowanie oparte na nagłówkach** — wybierz tryb logowania jednokrotnego [opartego na nagłówkach](application-proxy-configure-single-sign-on-with-ping-access.md) , jeśli masz aplikację korzystającą z usługi PingAccess, która obsługuje uwierzytelnianie oparte na nagłówkach HTTP, w przypadku których chcesz wykonać logowanie jednokrotne do

  >[!NOTE]
  >Ta opcja jest dostępna tylko wtedy, gdy serwer proxy aplikacji i PingAccess jest skonfigurowany dla aplikacji.

- **Zintegrowane uwierzytelnianie systemu Windows** — wybór trybu logowania jednokrotnego [uwierzytelniania systemu Windows](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) w przypadku udostępnienia lokalnej aplikacji WIA, w której ma zostać przeprowadzone Logowanie jednokrotne

  >[!NOTE]
  >Ta opcja jest dostępna tylko wtedy, gdy serwer proxy aplikacji jest skonfigurowany dla aplikacji.

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Tryby logowania jednokrotnego dla aplikacji niestandardowych

Aplikacje, które są opracowywane przez niestandardowe środowisko aplikacji, obsługują również dodatkowe tryby logowania jednokrotnego, które nie zostały wcześniej wymienione w tym:

- Logowanie przy użyciu protokołu [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)

- Logowanie oparte na [OpenID Connect Connect 1,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)

- Logowanie oparte na protokole [WS-Federation 1,2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)

- Logowanie oparte na protokole [SAML 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)

Przeczytaj [przewodnik dewelopera Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) , aby dowiedzieć się więcej na temat tworzenia niestandardowej aplikacji, która obsługuje te tryby logowania jednokrotnego.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Jak ustawić tryb logowania jednokrotnego aplikacji

Aby ustawić tryb logowania jednokrotnego aplikacji, wykonaj następujące instrukcje:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub współadministrator **.**
1. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.
1. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.
1. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.
1. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

1. Wybierz aplikację, dla której chcesz skonfigurować Logowanie jednokrotne.
1. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

## <a name="how-to-choose-a-provisioning-mode"></a>Jak wybrać tryb aprowizacji

- **Ręczna obsługa administracyjna** — wybierz tryb [ręcznego](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) inicjowania obsługi, jeśli masz już istniejące konta lub chcesz zarządzać kontami tej aplikacji poza usługą Azure AD.

- **Automatyczne Inicjowanie obsługi** — wybierz tryb [automatycznej](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) **aprowizacji** , jeśli chcesz włączyć automatyczne Inicjowanie obsługi oparte na interfejsie API i/lub anulowanie aprowizacji kont użytkowników w tej aplikacji 

- **Automatyczne Inicjowanie obsługi opartej na Standard scim** — umożliwia korzystanie z [funkcji automatycznego inicjowania obsługi opartej na Standard scim](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) , jeśli aplikacja obsługuje protokół Standard scim na potrzeby wykrywania zmian dla użytkowników i grup, które są automatycznie emitowane pod kątem zmian w przypadku wszystkich zintegrowanych aplikacji. z usługą Azure AD 

  >[!NOTE]
  >Ta opcja nie jest wyświetlana jako określony tryb aprowizacji, ale jest domyślnie włączona dla wszystkich aplikacji zintegrowanych z usługą Azure AD.

## <a name="how-to-set-an-applications-provisioning-mode"></a>Jak ustawić tryb aprowizacji aplikacji

Aby ustawić tryb **aprowizacji** aplikacji, wykonaj następujące instrukcje:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub współadministrator **.**
1. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.
1. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.
1. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.
1. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

1. Wybierz aplikację, dla której chcesz skonfigurować obsługę administracyjną.
1. Po załadowaniu aplikacji kliknij pozycję **Inicjowanie obsługi** w menu nawigacji po lewej stronie aplikacji.

## <a name="next-steps"></a>Następne kroki

[Managing Applications with Azure Active Directory (Zarządzanie aplikacjami za pomocą usługi Azure Active Directory)](what-is-application-management.md)
