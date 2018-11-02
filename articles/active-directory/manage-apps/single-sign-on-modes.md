---
title: Jak ustalić, jakie logowania jednokrotnego metodę | Dokumentacja firmy Microsoft
description: Zrozumienie pojedynczego trybów logowania jednokrotnego obsługiwany przez usługę Azure AD i wybieranie odpowiedniej opcji dla aplikacji Cię interesuje.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: barbkess
ms.openlocfilehash: a9afb3f611a5762d329788b0aa7e1bc52e41fab3
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356907"
---
# <a name="how-to-determine-what-single-sign-on-method-to-use"></a>Jak ustalić, jakie logowania jednokrotnego metoda do użycia

Ten artykuł ułatwia zrozumienie pojedynczego trybów logowania jednokrotnego obsługiwany przez usługę Azure AD i wybieranie odpowiedniej opcji dla aplikacji Cię interesuje.

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Logowania jednokrotnego i aprowizacji trybów obsługiwanych przez aplikacji o określonych typach

W poniższej tabeli opisano różne pojedynczego logowania jednokrotnego i aprowizacji tryby obsługiwanych przez każdą z powyższych typów aplikacji. Aby ułatwić zrozumienie aplikacji, które należy dodać do obsługi określonego celu, można użyć tej tabeli.

  ![Tabela typów aplikacji](./media/single-sign-on-modes/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Jak wybrać jedno trybu logowania jednokrotnego

Obsługiwane są następujące **logowanie jednokrotne** tryby aplikacji usługi Azure AD.

-   **Azure AD logowanie jednokrotne wyłączone** — Wybieranie usługi Azure AD logowanie jednokrotne wyłączone **tryb rejestracji jednokrotnej** Jeśli nie są jeszcze gotowe, aby połączyć tę aplikację za pomocą logowania jednokrotnego z usługą Azure AD lub ją po prostu testowania

-   **Połączone logowanie jednokrotne** — wybierz [połączone logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **tryb rejestracji jednokrotnej** Jeśli masz aplikację, która jest już połączony z istniejącym pojedynczego logowania jednokrotnego rozwiązaniu lub jeśli chcesz, aby Publikowanie proste łącze dla użytkowników w ich [panelu dostępu do aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) lub [uruchamiania aplikacji usługi Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

-   **Logowanie na podstawie hasła** — wybierz [opartego na hasłach logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **tryb rejestracji jednokrotnej** Jeśli aplikacja renderuje pola HTML nazwę użytkownika i hasło, a chcesz przechowywać danej nazwy użytkownika i bezpieczne hasło i odtworzone później w aplikacji

-   **Logowanie na podstawie protokołu SAML** — wybierz [opartej na SAML logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) logowania jednokrotnego w trybie, jeśli aplikacja obsługuje protokoły SAML lub OpenID Connect lub jeśli chcesz mieć możliwość zmapowania użytkowników do ról aplikacji, na podstawie reguł należy zdefiniować w swoje oświadczenia języka SAML *(Uwaga:* \* ta opcja nie jest dostępna, gdy serwer proxy aplikacji jest skonfigurowana dla aplikacji)\*

-   **Logowanie na podstawie nagłówka** — wybierz tę opcję, [logowanie na podstawie nagłówka](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) pojedynczego tryb logowania jednokrotnego w przypadku aplikacji przy użyciu PingAccess, która obsługuje uwierzytelnianie na podstawie nagłówków HTTP, który chcesz wykonać logowania jednokrotnego do *(Uwaga:* \* ta opcja jest dostępna tylko, gdy skonfigurowano serwer proxy aplikacji usługi i oprogramowanie PingAccess dla aplikacji)\*

-   **Zintegrowane uwierzytelnianie Windows** — wybierz [zintegrowane uwierzytelnianie Windows](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) logowania jednokrotnego tryb w przypadku udostępniania aplikacji WIA w środowisku lokalnym, którą chcesz wykonać logowania jednokrotnego do *(Uwaga:* \* ta opcja jest dostępna tylko wtedy, gdy serwer proxy aplikacji jest skonfigurowana dla aplikacji)\*

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Tryby pojedynczego logowania jednokrotnego dla aplikacji niestandardowej

Aplikacje mają niestandardowe opracowanych przez [aplikacji niestandardowej](#_Custom-Developed_Applications) środowisko obsługują także dodatkowe pojedynczego logowania jednokrotnego tryby nie wymienionych powyżej, obejmujące:

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) logowania opartego na

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) logowania opartego na

-   [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) logowania opartego na

-   [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) logowania opartego na

Odczyt [przewodnik dewelopera usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) Aby dowiedzieć się więcej o sposobie tworzenia aplikacji niestandardowej, który obsługuje pojedynczego logowania jednokrotnego trybów.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Jak skonfigurować aplikację pojedynczego tryb logowania jednokrotnego

Aby ustawić aplikacji **logowanie jednokrotne** tryb, wykonaj te instrukcje:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6.  Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

## <a name="next-steps"></a>Kolejne kroki
[Udostępnij logowanie jednokrotne do aplikacji serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)

