---
title: Instalowanie rozszerzenia przeglądarki panelu dostępu do aplikacji — usługa Azure AD
description: Napraw typowe błędy napotkane podczas instalowania rozszerzenia przeglądarki panelu dostępu.
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
ms.topic: article
ms.date: 05/04/2018
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 771ba79f067cbff1ab8bbfece64f4028b4ca50b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275852"
---
# <a name="install-the-access-panel-browser-extension"></a>Instalowanie rozszerzenia przeglądarki panelu dostępu

Panel dostępu jest portalem internetowym. Jeśli masz konto służbowe w usłudze Azure Active Directory (Azure AD), możesz użyć panelu dostępu do wyświetlania i uruchamiania aplikacji opartych na chmurze, do których administrator usługi Azure AD udzielił ci dostępu. 

Jeśli używasz wersji usługi Azure AD, możesz również korzystać z funkcji samoobsługowego zarządzania grupami i aplikacjami za pośrednictwem panelu dostępu. 

Panel dostępu jest oddzielony od witryny Azure portal. Nie wymaga subskrypcji platformy Azure.

## <a name="web-browser-requirements"></a>Wymagania dotyczące przeglądarki sieci Web

Co najmniej panel dostępu wymaga przeglądarki obsługującej javascript i obsługującej css. Aby zalogować się do aplikacji za pomocą logowania logowania opartego na hasłach w panelu dostępu, w przeglądarce musi być zainstalowane rozszerzenie panelu dostępu. Rozszerzenie jest pobierane automatycznie po wybraniu aplikacji skonfigurowanej dla loga loga loga.

W przypadku logów samouszeńców opartych na hasłach można użyć dowolnej z następujących przeglądarek:

- **Microsoft Edge**: w systemie Windows 10 Anniversary Edition lub nowszym. 
- **Chrome**: w systemie Windows 7 lub nowszym oraz w systemie MacOS X lub nowszym.
- **Firefox 26.0 lub nowszy**: w systemie Windows XP z dodatkiem SP2 lub nowszym oraz w systemie Mac OS X 10.6 lub nowszym.

## <a name="install-the-access-panel-browser-extension"></a>Instalowanie rozszerzenia przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj następujące czynności:

1.  W jednej z obsługiwanych przeglądarek otwórz [panel dostępu](https://myapps.microsoft.com), a następnie zaloguj się jako użytkownik na swoim koncie usługi Azure AD.

2.  Wybierz aplikację logajną opartą na hasłach.

3.  Po wyświetleniu monitu wybierz pozycję **Zainstaluj teraz**.  
    Zostaniesz przekierowany do linku do pobrania wybranej przeglądarki. 
    
4.  Wybierz pozycję **Dodaj**.

5.  Jeśli zostanie wyświetlony monit, **włącz** lub **zezwól** na rozszerzenie.

6.  Po zakończeniu instalacji uruchom ponownie przeglądarkę.

7.  Zaloguj się do panelu dostępu i sprawdź, czy możesz uruchomić aplikacje logowania logowania opartego na hasłach.

Rozszerzenie dla Chrome i Microsoft Edge można również pobrać bezpośrednio z następujących witryn:

- [Rozszerzenie chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Rozszerzenie Microsoft Edge](https://www.microsoft.com/en-us/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)
- [Rozszerzenie Firefoksa](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Korzystanie z rozszerzenia logowania bezpieczne moje aplikacje
* Jeśli używasz adresu URL usługi `https://myapps.microsoft.com`Moje aplikacje innego niż , skonfiguruj domyślny adres URL, wykonując następujące czynności:
   1. Jeśli *nie* zalogujesz się do rozszerzenia, kliknij prawym przyciskiem myszy ikonę rozszerzenia.
   2. W menu wybierz pozycję **Adres URL moje aplikacje**.
   3. Wybierz domyślny adres URL.
   4. Wybierz ikonę rozszerzenia.
   5. Aby zalogować się do rozszerzenia, wybierz pozycję **Zaloguj się, aby rozpocząć**.

* Aby zalogować się bezpośrednio do aplikacji z przeglądarki, wykonaj następujące czynności:
   1. Po zainstalowaniu rozszerzenia zaloguj się do niego, wybierając pozycję **Zaloguj się, aby rozpocząć**.
   2. Zaloguj się do aplikacji przy pomocy adresu URL logowania.  
       Adres URL logowania jest zwykle adresem URL aplikacji, który wyświetla formularz logowania.
      Rozszerzenie powinno zmienić stan i poinformować, że hasło jest dostępne.
   3. Aby się zalogować, wybierz ikonę rozszerzenia.

* Aby uruchomić aplikację z rozszerzenia, wykonaj następujące czynności:
   1. Po zainstalowaniu rozszerzenia zaloguj się do niego, wybierając pozycję **Zaloguj się, aby rozpocząć**.
   2. Wybierz ikonę rozszerzenia, aby otworzyć jej menu.
   3. Wyszukaj aplikację dostępną w portalu Moje aplikacje.
   4. Na liście wyników wyszukiwania wybierz aplikację.  
       Trzy ostatnie używane aplikacje są wyświetlane na liście **ostatnio używanych** skrótów.
       
* Aby używać wewnętrznych firmowych adresów URL podczas zdalnego sterowania, wykonaj następujące czynności:
    1. [Konfigurowanie serwera proxy aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) w dzierżawie
    2. [Publikowanie aplikacji](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) i adresu URL za pośrednictwem serwera proxy aplikacji
    3. Zainstaluj rozszerzenie i zaloguj się do niego, wybierając pozycję Zaloguj się, aby rozpocząć
    4. Teraz możesz przejść do wewnętrznego adresu URL firmy, nawet podczas

> [!NOTE]
> Powyższe opcje są dostępne tylko dla przeglądarki Microsoft Edge, Chrome i Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Konfigurowanie zasad grupy dla programu Internet Explorer

Można skonfigurować zasady grupy, które umożliwiają zdalne instalowanie rozszerzenia panelu dostępu dla programu Internet Explorer na komputerach użytkowników.

Przed skonfigurowaniem zasad grupy należy upewnić się, że:

-   Skonfigurowano [Usługi domenowe Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)i przyłączyłeś komputery użytkowników do domeny.

-   Aby edytować obiekt zasad grupy(GPO), musisz mieć uprawnienia *Do edycji ustawień.* Domyślnie to uprawnienie jest przyznawane członkom następujących grup zabezpieczeń: administratorów domeny, administratorów przedsiębiorstw i właścicieli twórców zasad grupy.

Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania zasad grupy i wdrażania jej dla użytkowników, zobacz [Wdrażanie rozszerzenia panelu dostępu dla programu Internet Explorer przy użyciu zasad grupy](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Rozwiązywanie problemów z rozszerzeniem panelu dostępu w programie Internet Explorer

Aby uzyskać dostęp do narzędzia diagnostycznego i informacji dotyczących konfigurowania rozszerzenia dla programu Internet Explorer, zobacz [Rozwiązywanie problemów z rozszerzeniem panelu dostępu dla programu Internet Explorer](manage-access-panel-browser-extension.md).

> [!NOTE]
> Program Internet Explorer korzysta z ograniczonej pomocy technicznej i nie otrzymuje już nowych aktualizacji oprogramowania. Microsoft Edge jest zalecaną przeglądarką.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Jeśli poprzednie kroki nie rozwiążą problemu

Otwórz bilet pomocy technicznej z następującymi informacjami, jeśli jest dostępny:

-   Identyfikator błędu korelacji
-   UPN (adres e-mail użytkownika)
-   Identyfikator dzierżawy
-   Typ przeglądarki
-   Strefa czasowa i czas lub ramy czasowe wystąpienia błędu
-   Ślady skrzypka

## <a name="next-steps"></a>Następne kroki
[Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](what-is-single-sign-on.md)
