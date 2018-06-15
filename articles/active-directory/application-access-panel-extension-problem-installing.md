---
title: Zainstaluj aplikację dostępu panelu przeglądarki rozszerzenie - Azure | Dokumentacja firmy Microsoft
description: Rozwiąż typowe błędy podczas instalowania rozszerzenia przeglądarki panelu dostępu.
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/4/18
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: 8db0c28b0ea6b6f78ef86c773ff24f9d22fe3ecd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34353878"
---
# <a name="install-the-access-panel-browser-extension"></a>Zainstaluj rozszerzenie przeglądarki panelu dostępu

Panel dostępu jest portalem sieci web. Jeśli masz służbowego lub szkolnego konta w usłudze Azure Active Directory (Azure AD), służy panelu dostępu do wyświetlania i uruchamiania aplikacji opartej na chmurze, które administrator usługi Azure AD udzielił dostęp do. 

Jeśli używasz wersji usługi Azure AD, można również użyć grupami samoobsługi, a funkcje zarządzania aplikacjami za pomocą panelu dostępu. 

Panel dostępu jest oddzielony od portalu Azure. Nie wymagają posiadania subskrypcji platformy Azure.

## <a name="web-browser-requirements"></a>Wymagania dotyczące przeglądarki sieci Web

Co najmniej panelu dostępu wymaga przeglądarki obsługującej JavaScript i włączył CSS. Zalogowani do aplikacji za pomocą opartego na hasłach logowania jednokrotnego w panelu dostępu, musi mieć rozszerzenie panelu dostępu, które są zainstalowane w przeglądarce. Rozszerzenie jest pobierany automatycznie po wybraniu aplikacji, która jest skonfigurowana do opartego na hasłach logowania jednokrotnego.

Dla logowania jednokrotnego opartego na hasłach można użyć dowolnego z poniższych przeglądarek:

- **Krawędź**: w systemie Windows 10 Anniversary Edition lub nowszy. 
- **Chrome**: w systemie Windows 7 lub nowszym i System MacOS x lub później.
- **Firefox 26.0 lub nowsze**: w systemie Windows XP SP2 lub nowszy i Mac OS X 10.6 lub później.

## <a name="install-the-access-panel-browser-extension"></a>Zainstaluj rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj następujące czynności:

1.  W jednym z obsługiwanych przeglądarek, otwórz [panelu dostępu](https://myapps.microsoft.com), a następnie zaloguj się jako użytkownik na koncie usługi Azure AD.

2.  Wybierz aplikację rejestracji Jednokrotnej opartego na hasłach.

3.  Po wyświetleniu monitu wybierz **Zainstaluj teraz**.  
    Nastąpi przekierowanie do łącze Pobierz dostępne dla wybranej przeglądarki. 
    
4.  Wybierz pozycję **Dodaj**.

5.  Jeśli zostanie wyświetlony monit, albo **włączyć** lub **Zezwalaj** rozszerzenia.

6.  Po zakończeniu instalacji uruchom ponownie przeglądarkę.

7.  Zaloguj się do panelu dostępu, a także Sprawdź, aby zobaczyć, czy można uruchomić aplikacji rejestracji Jednokrotnej opartego na hasłach.

Możesz również pobrać rozszerzenia dla programu Chrome i krawędzi bezpośrednio z następujących witryn:

- [Rozszerzenia programu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Rozszerzenie krawędzi](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Użyj mojej aplikacji bezpiecznego logowania rozszerzenia
* Jeśli używasz Mój adres URL aplikacji innych niż `https://myapps.microsoft.com`, skonfiguruj adres URL domyślnej przez wykonanie następujących czynności:
   1. Podczas *nie* zalogowany do rozszerzenia, kliknij prawym przyciskiem myszy ikonę rozszerzenia.
   2. W menu, wybierz **adres URL aplikacji Mój**.
   3. Wybierz adres URL domyślnej.
   4. Wybierz ikonę rozszerzenia.
   5. Aby zalogować się do rozszerzenia, wybierz **Zaloguj się rozpocząć**.

* Aby zalogować się bezpośrednio do aplikacji z przeglądarki, wykonaj następujące czynności:
   1. Po zainstalowaniu rozszerzenia, zaloguj się do niego, wybierając **Zaloguj się rozpocząć**.
   2. Zaloguj się do aplikacji z adresem URL logowania jednokrotnego.  
       Adres URL logowania jest zwykle adres URL aplikacji, która wyświetla formularz logowania.
      Rozszerzenia należy zmienić stan i informacją o tym, że hasło jest dostępny.
   3. Aby się zarejestrować, wybierz ikonę rozszerzenia.

* Aby uruchomić aplikację z rozszerzenia, wykonaj następujące czynności:
   1. Po zainstalowaniu rozszerzenia, zaloguj się do niego, wybierając **Zaloguj się rozpocząć**.
   2. Wybierz ikonę rozszerzenia, aby otworzyć menu.
   3. Wyszukaj aplikację, która jest dostępna w portalu Moje aplikacje.
   4. Na liście wyników wyszukiwania wybierz aplikację.  
       Ostatnie trzy aplikacje używane są wyświetlane w **ostatnio używane** listy skrótów.
       
* Aby użyć adresów URL wewnętrznego firmy podczas pracy zdalnej, wykonaj następujące czynności:
    1. [Skonfiguruj serwer Proxy aplikacji](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-enable) na dzierżawy
    2. [Opublikuj aplikację](https://docs.microsoft.com/en-us/azure/active-directory/application-proxy-publish-azure-portal) i adresu URL za pośrednictwem serwera Proxy aplikacji
    3. Zainstaluj rozszerzenie, a następnie zaloguj do niego, wybierając logowania rozpoczęcie pracy
    4. Możesz teraz przejść do adresu URL wewnętrzne firmy, nawet podczas zdalnego

> [!NOTE]
> Poprzednie opcje są dostępne tylko dla krawędzi, Chrome i Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Konfigurowanie zasad grupy dla programu Internet Explorer

Można skonfigurować zasady grupy, która pozwala na zdalnie zainstalować rozszerzenia panel dostępu dla programu Internet Explorer na komputerach użytkowników.

Przed rozpoczęciem konfigurowania zasad grupy, upewnij się, że:

-   Po skonfigurowaniu [usług domenowych w usłudze Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), i dołączeniu komputerów użytkowników do domeny.

-   Aby dokonać edycji obiektu zasad grupy (GPO), musisz mieć *edytować ustawienia* uprawnienia. Domyślnie to uprawnienie jest przyznawane członków z następujących grup zabezpieczeń: Administratorzy domeny, Administratorzy przedsiębiorstwa oraz Twórcy-właściciele zasad grupy.

Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania zasad grupy i wdrożeniem jej u użytkowników, zobacz [wdrażania rozszerzenia panel dostępu dla programu Internet Explorer przy użyciu zasad grupy](active-directory-saas-ie-group-policy.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Rozwiązywanie problemów z rozszerzeniem panelu dostępu w programie Internet Explorer

Aby uzyskać dostęp do narzędzia diagnostycznego i informacji o konfigurowaniu rozszerzenia dla programu Internet Explorer, zobacz [Rozwiązywanie problemów z rozszerzeniem panel dostępu dla programu Internet Explorer](active-directory-saas-ie-troubleshooting.md).

> [!NOTE]
> Internet Explorer znajduje się na ograniczony pomocy technicznej i nie będzie już otrzymywać nowe aktualizacje oprogramowania. Krawędź jest zalecane przeglądarki.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Jeśli te czynności nie rozwiąże problemu

Jeśli jest dostępna, otwórz bilet pomocy technicznej następujące informacje:

-   Identyfikator błędu korelacji
-   Nazwa UPN (adres e-mail użytkownika)
-   Dla identyfikatora dzierżawcy
-   Typ przeglądarki
-   Strefa czasowa i godziny lub przedział czasu w momencie wystąpienia błędu
-   Ślady fiddler

## <a name="next-steps"></a>Kolejne kroki
[Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
