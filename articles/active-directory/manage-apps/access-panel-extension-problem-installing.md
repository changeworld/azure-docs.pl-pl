---
title: Instalowanie programu access panel rozszerzenia przeglądarki do aplikacji — Azure | Dokumentacja firmy Microsoft
description: Rozwiąż typowe błędy występujące podczas instalowania rozszerzenia przeglądarki panelu dostępu.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/4/2018
ms.author: celested
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92999f23b468c975d0f95f2628b9a5069d929031
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707323"
---
# <a name="install-the-access-panel-browser-extension"></a>Zainstaluj rozszerzenie przeglądarki panelu dostępu

Panel dostępu jest portalem sieci web. Jeśli masz służbowe konto w usłudze Azure Active Directory (Azure AD), można użyć panelu dostępu do wyświetlania i uruchamiania aplikacji opartych na chmurze, których administrator usługi Azure AD udzielił zyskujesz dostęp do. 

Jeśli używasz wersji usługi Azure AD umożliwia także grupa samoobsługi i możliwości zarządzania aplikacjami za pomocą panelu dostępu. 

Panel dostępu jest oddzielony od witryny Azure portal. Nie wymagają posiadania subskrypcji platformy Azure.

## <a name="web-browser-requirements"></a>Wymagania dotyczące przeglądarki sieci Web

Co najmniej panelu dostępu wymaga przeglądarki, która obsługuje język JavaScript i CSS została włączona. Być zalogowani do aplikacji za pomocą opartego na hasłach logowania jednokrotnego w panelu dostępu, musi mieć rozszerzenie panelu dostępu, które są zainstalowane w przeglądarce. Rozszerzenie jest automatycznie pobierany po wybraniu aplikacji, która jest skonfigurowana na podstawie hasła logowania jednokrotnego.

Logowania jednokrotnego opartego na hasłach można użyć dowolnej z poniższych przeglądarek:

- **Microsoft Edge**: Windows 10 Anniversary Edition lub nowszy. 
- **Dla programu Chrome**: Windows 7 lub nowszy i System MacOS x lub później.
- **Firefox 26.0 lub nowsze**: na Windows XP z dodatkiem SP2 lub nowszym i Mac OS X 10.6 lub później.

## <a name="install-the-access-panel-browser-extension"></a>Zainstaluj rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj następujące czynności:

1.  W jednym z obsługiwanych przeglądarek, otwórz [panelu dostępu](https://myapps.microsoft.com), a następnie zaloguj się jako użytkownik w ramach konta usługi Azure AD.

2.  Wybierz aplikację logowania jednokrotnego opartego na hasłach.

3.  Po wyświetleniu monitu wybierz **Zainstaluj teraz**.  
    Nastąpi przekierowanie do link pobierania dla wybranej przeglądarki. 
    
4.  Wybierz pozycję **Dodaj**.

5.  Jeśli zostanie wyświetlony monit, albo **Włącz** lub **Zezwalaj** rozszerzenia.

6.  Po zakończeniu instalacji uruchom ponownie przeglądarkę.

7.  Zaloguj się do panelu dostępu i kontroli, aby zobaczyć, czy można uruchomić aplikacji logowania jednokrotnego opartego na hasłach.

Możesz również pobrać rozszerzenie dla programu Chrome i Microsoft Edge bezpośrednio z następujących witryn:

- [Rozszerzenie przeglądarki Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Rozszerzenia Microsoft Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84)
- [Rozszerzenia programu Firefox](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Użyj mojej aplikacji bezpiecznego logowania rozszerzenia
* Jeśli używasz Mój adres URL aplikacji innych niż `https://myapps.microsoft.com`, skonfigurować domyślny adres URL, wykonując następujące czynności:
   1. Gdy jesteś *nie* logowanie się do rozszerzenia, kliknij prawym przyciskiem myszy ikonę rozszerzenia.
   2. W menu, wybierz **adres URL aplikacji Mój**.
   3. Wybierz domyślny adres URL.
   4. Wybierz ikonę rozszerzenia.
   5. Aby zalogować się do rozszerzenia, wybierz **Zaloguj się rozpocząć**.

* Aby zalogować się bezpośrednio do aplikacji w przeglądarce, wykonaj następujące czynności:
   1. Po zainstalowaniu rozszerzenia należy zalogować się w niej, wybierając **Zaloguj się rozpocząć**.
   2. Zaloguj się do aplikacji za pomocą adresu URL logowania jednokrotnego.  
       Adres URL logowania jest zwykle adres URL aplikacji, który wyświetla formularz logowania.
      Rozszerzenie powinno zmiany stanu i powiadomienie Cię o tym, że hasło jest dostępny.
   3. Aby się zarejestrować, wybierz ikonę rozszerzenia.

* Aby uruchomić aplikację z rozszerzenia, wykonaj następujące czynności:
   1. Po zainstalowaniu rozszerzenia należy zalogować się w niej, wybierając **Zaloguj się rozpocząć**.
   2. Wybierz ikonę rozszerzenia, aby otworzyć jego menu.
   3. Wyszukaj aplikację, która jest dostępna w portalu Moje aplikacje.
   4. Na liście wyników wyszukiwania wybierz aplikację.  
       Ostatnie trzy aplikacje, które wykorzystano są wyświetlane na **ostatnio używane** listy skrótów.
       
* Aby użyć firmy wewnętrznych adresów URL, podczas pracy zdalnej, wykonaj następujące czynności:
    1. [Skonfiguruj serwer Proxy aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) w dzierżawie
    2. [Opublikuj aplikację](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) i adresem URL za pośrednictwem serwera Proxy aplikacji
    3. Zainstaluj rozszerzenie, a następnie zaloguj do niego, wybierając pozycję Zaloguj się na rozpoczęcie pracy
    4. Teraz możesz przejść do adresu URL wewnętrzne firmy, nawet podczas pracy zdalnej.

> [!NOTE]
> Poprzedni opcje są dostępne tylko w przypadku Microsoft Edge, Chrome i Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Konfigurowanie zasad grupy dla programu Internet Explorer

Można skonfigurować zasady grupy, która pozwala na zdalnie zainstalować rozszerzenia Panelu dostępu do programu Internet Explorer na komputerach użytkowników.

Przed rozpoczęciem konfigurowania zasad grupy, upewnij się, że:

-   Po skonfigurowaniu [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), i komputerów użytkowników mają być przyłączone do domeny.

-   Aby edytować obiekt zasad grupy (GPO), konieczne jest posiadanie *edytować ustawienia* uprawnienia. Domyślnie to uprawnienie jest przyznane uprawnienia do członków z następujących grup zabezpieczeń: Administratorzy domeny, Administratorzy przedsiębiorstwa i Twórcy-właściciele zasad grupy.

Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania zasad grupy i wdrożeniem jej u użytkowników, zobacz [wdrażanie rozszerzenia Panelu dostępu do programu Internet Explorer przy użyciu zasad grupy](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Rozwiązywanie problemów z rozszerzeniem panelu dostępu w programie Internet Explorer

Aby uzyskać dostęp do narzędzia do diagnostyki i informacji o konfigurowaniu rozszerzenia dla programu Internet Explorer, zobacz [Rozwiązywanie problemów z rozszerzenia Panelu dostępu do programu Internet Explorer](manage-access-panel-browser-extension.md).

> [!NOTE]
> Internet Explorer jest ograniczona pomoc techniczną i nie będzie już otrzymywać nowe aktualizacje oprogramowania. Microsoft Edge jest zalecane przeglądarki.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Jeśli poprzednie kroki nie rozwiązują problemu

Jeśli jest on dostępny, otwórz bilet pomocy technicznej z następującymi informacjami:

-   Identyfikator błędu korelacji
-   Nazwy UPN (adres e-mail użytkownika)
-   Identyfikator dzierżawy
-   Typ przeglądarki
-   Strefę czasową i czas lub gdy wystąpił błąd
-   Ślady narzędzia fiddler

## <a name="next-steps"></a>Kolejne kroki
[Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](what-is-single-sign-on.md)
