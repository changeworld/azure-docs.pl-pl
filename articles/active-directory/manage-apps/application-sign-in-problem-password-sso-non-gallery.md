---
title: Logowanie jednookrotne oparte na hasłach w Panelu dostępu | Dokumenty firmy Microsoft
description: W tym artykule omówiono obszary problemów, które zawierają wskazówki dotyczące rozwiązywania problemów związanych z logowaniem się do aplikacji usługi Azure AD Gallery skonfigurowanych do logowania jednokrotnego hasła.
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
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ca192c28757df189e531aee0ba2d8da288ba7e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381239"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problemy z logowaniem się do aplikacji usługi Azure AD Gallery skonfigurowanej do logowania jednokrotnego hasła

Panel dostępu to portal internetowy, który umożliwia użytkownikowi, który ma konto służbowe w usłudze Azure Active Directory (Azure AD) wyświetlanie i uruchamianie aplikacji opartych na chmurze, do których administrator usługi Azure AD przyznał im dostęp. Użytkownik, który ma wersje usługi Azure AD, może również korzystać z funkcji samoobsługowego zarządzania grupami i aplikacjami za pośrednictwem Panelu dostępu. Panel dostępu jest oddzielony od witryny Azure portal i nie wymaga od użytkowników subskrypcji platformy Azure.

Aby w Panelu dostępu można korzystać z logowania jednokrotnego opartego na hasłach, w przeglądarce użytkownika należy zainstalować rozszerzenie Panelu dostępu. To rozszerzenie jest pobierane automatycznie, gdy użytkownik wybierze aplikację skonfigurowaną dla loga loga bez hasła.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Spełnianie wymagań przeglądarki dla Panelu dostępu

Panel dostępu wymaga przeglądarki obsługującej język JavaScript i włączonej wersji CSS. Aby w Panelu dostępu można korzystać z logowania jednokrotnego opartego na hasłach, w przeglądarce użytkownika należy zainstalować rozszerzenie Panelu dostępu. To rozszerzenie jest pobierane automatycznie, gdy użytkownik wybierze aplikację skonfigurowaną dla loga loga bez hasła.

W przypadku logów samouszeńców opartych na hasłach przeglądarki użytkownika końcowego mogą być:

-   Internet Explorer 8, 9, 10, 11 — w systemie Windows 7 lub nowszym

-   Chrome — w systemie Windows 7 lub nowszym oraz w systemie MacOS X lub nowszym

-   Firefox 26.0 lub nowszy — w systemie Windows XP z dodatkiem SP2 lub nowszym oraz w systemie Mac OS X 10.6 lub nowszym

>[!NOTE]
>Rozszerzenie loga SSO oparte na hasłach staje się dostępne dla przeglądarki Microsoft Edge w systemie Windows 10, gdy rozszerzenia przeglądarki staną się obsługiwane przez microsoft edge.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak zainstalować rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj poniższe czynności:

1.  Otwórz [Panel dostępu](https://myapps.microsoft.com) w jednej z obsługiwanych przeglądarek i zaloguj się jako **użytkownik** w usłudze Azure AD.

2.  kliknij **aplikację** logajną hasła w Panelu dostępu.

3.  W wierszu z prośbą o zainstalowanie oprogramowania wybierz pozycję **Zainstaluj teraz**.

4.  Na podstawie przeglądarki zostaniesz przekierowany do linku do pobrania. **Dodaj** rozszerzenie do przeglądarki.

5.  Jeśli twoja przeglądarka poprosi, wybierz opcję **Włącz** lub **Zezwól** na rozszerzenie.

6.  Po zainstalowaniu **uruchom ponownie** sesję przeglądarki.

7.  Zaloguj się do Panelu dostępu i sprawdź, czy możesz **uruchomić** aplikacje logowania bez logowania do haseł

Możesz również pobrać rozszerzenie dla Chrome i Firefox z bezpośrednich linków poniżej:

-   [Rozszerzenie panelu dostępu do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu do Firefoksa](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Konfigurowanie zasad grupy dla programu Internet Explorer

Można skonfigurować zasady grupy, które umożliwiają zdalne instalowanie rozszerzenia Panelu dostępu dla programu Internet Explorer na komputerach użytkowników.

Wymagania wstępne obejmują:

-   Skonfigurowano [Usługi domenowe Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)i przyłączyłeś komputery użytkowników do domeny.

-   Aby edytować obiekt zasad grupy, musisz mieć uprawnienie "Edytuj ustawienia". Domyślnie członkowie następujących grup zabezpieczeń mają takie uprawnienia: Administratorzy domeny, Administratorzy przedsiębiorstwa i Właściciele twórców zasad grupy. [Dowiedz się więcej](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Postępuj zgodnie z samouczkiem [Jak wdrożyć rozszerzenie panelu dostępu dla programu Internet Explorer przy użyciu zasad grupy,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) aby uzyskać instrukcje krok po kroku dotyczące konfigurowania zasad grupy i wdrażania jej dla użytkowników.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Rozwiązywanie problemów z Panelem dostępu w programie Internet Explorer

Postępuj zgodnie [z instrukcją Rozwiązywanie problemów z rozszerzeniem panelu dostępu dla programu Internet Explorer,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) aby uzyskać dostęp do narzędzia diagnostycznego i instrukcjami krok po kroku dotyczącymi konfigurowania rozszerzenia dla programu IE.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować logowanie jednokrotne hasła dla aplikacji spoza galerii

Aby skonfigurować aplikację z galerii usługi Azure AD, należy:

-   [Dodawanie aplikacji spoza galerii](#add-a-non-gallery-application)

-   [Konfigurowanie aplikacji do logowania jednokrotnego hasła](#configure-the-application-for-password-single-sign-on)

-   [Przypisywanie użytkowników do aplikacji](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Dodawanie aplikacji spoza galerii

Aby dodać aplikację z galerii usługi Azure AD Gallery, wykonaj poniższe czynności:

1.  Otwórz [witrynę Azure portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub **współadministrator**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  kliknij przycisk **Dodaj** w prawym górnym rogu okienka **Aplikacje dla przedsiębiorstw.**

6.  kliknij pozycję **Aplikacja niezwiązane z galerią.**

7.  Wprowadź nazwę aplikacji w polach tekstowych **Nazwa.** Wybierz **pozycję Dodaj.**

Po krótkim czasie można wyświetlić okienko konfiguracji aplikacji.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji do logowania jednokrotnego hasła

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne

7. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8. Wybierz tryb **logowania opartego na hasłach.**

9. Wprowadź **adres URL logowania**. Jest to adres URL, w którym użytkownicy wejdą swoją nazwę użytkownika i hasło, aby się zalogować. Upewnij się, że pola logowania są widoczne pod adresem URL.

10. Przypisz użytkowników do aplikacji.

11. Ponadto można również podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając **przycisk Aktualizuj poświadczenia** i wprowadzając nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy są monitowani o wprowadzenie poświadczeń samodzielnie po uruchomieniu.

### <a name="assign-users-to-the-application"></a>Przypisywanie użytkowników do aplikacji

Aby bezpośrednio przypisać jednego lub więcej użytkowników do aplikacji, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację, do której chcesz przypisać użytkownika z listy.

7. Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** z menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Dodaj** u góry listy **Użytkownicy i grupy,** aby otworzyć okienko **Dodaj przypisanie.**

9. kliknij selektor **Użytkownicy i grupy** w okienku **Dodawanie przypisania.**

10. Wpisz **pełną nazwę** lub **adres e-mail** użytkownika, którego jesteś zainteresowany, aby przypisać do pola wyszukiwania według nazwy lub adresu **e-mail.**

11. Umieść wskaźnik myszy na **użytkowniku** na liście, aby wyświetlić **pole wyboru**. Kliknij pole wyboru obok zdjęcia profilowego lub logo użytkownika, aby dodać go do **wybranej** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika,** wpisz inne imię i **nazwisko** lub **adres e-mail** w polu wyszukiwania według nazwy lub adresu **e-mail,** a następnie kliknij to pole wyboru, aby dodać tego użytkownika do **wybranej** listy.

13. Po zakończeniu wybierania użytkowników kliknij przycisk **Wybierz,** aby dodać ich do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. **Opcjonalnie:** kliknij selektor **wybierz rolę** w okienku **Dodaj przypisanie,** aby wybrać rolę przypisaną do wybranych użytkowników.

15. Kliknij przycisk **Przypisz,** aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie wybrani użytkownicy będą mogli uruchamiać te aplikacje w Panelu dostępu.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie rozwiążą problemu

otwórz bilet pomocy technicznej z następującymi informacjami, jeśli są dostępne:

-   Identyfikator błędu korelacji

-   UPN (adres e-mail użytkownika)

-   Identyfikator dzierżawy

-   Typ przeglądarki

-   Strefa czasowa i czas/ramy czasowe podczas wystąpienia błędu

-   Ślady skrzypka

## <a name="next-steps"></a>Następne kroki
[Zapewnianie logowania jednokrotnego do aplikacji za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)

