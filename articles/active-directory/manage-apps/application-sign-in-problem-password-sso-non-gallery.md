---
title: Korzystanie z logowania jednokrotnego opartego na hasłach (SSO) w panelu dostępu | Microsoft Docs
description: W tym artykule omówiono obszary problemów, które zapewniają wskazówki dotyczące rozwiązywania problemów związanych z logowaniem do aplikacji w galerii usługi Azure AD skonfigurowanych do logowania jednokrotnego hasła.
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
ms.openlocfilehash: c020f3ab3ea7ed96b72d0490b717225d44c4fac1
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277739"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problemy z logowaniem do aplikacji w galerii usługi Azure AD skonfigurowanej do logowania jednokrotnego hasła

Panel dostępu jest portalem opartym na sieci Web, który umożliwia użytkownikowi, który ma konto służbowe w usłudze Azure Active Directory (Azure AD) wyświetlanie i uruchamianie aplikacji opartych na chmurze, do których administrator usługi Azure AD udzielił im dostępu. Użytkownik, który ma wersje usługi Azure AD, może również korzystać z funkcji samoobsługowego zarządzania grupami i aplikacjami za pomocą panelu dostępu. Panel dostępu jest oddzielony od Azure Portal i nie wymaga, aby użytkownicy mieli subskrypcję platformy Azure.

Aby korzystać z logowania jednokrotnego opartego na hasłach (SSO) w panelu dostępu, rozszerzenie panelu dostępu musi być zainstalowane w przeglądarce użytkownika. To rozszerzenie jest pobierane automatycznie, gdy użytkownik wybierze aplikację, która jest skonfigurowana dla logowania jednokrotnego opartego na hasłach.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Wymagania dotyczące przeglądarki dla panelu dostępu

Panel dostępu wymaga przeglądarki obsługującej język JavaScript i ma włączoną obsługę CSS. Aby korzystać z logowania jednokrotnego opartego na hasłach (SSO) w panelu dostępu, rozszerzenie panelu dostępu musi być zainstalowane w przeglądarce użytkownika. To rozszerzenie jest pobierane automatycznie, gdy użytkownik wybierze aplikację, która jest skonfigurowana dla logowania jednokrotnego opartego na hasłach.

W przypadku logowania jednokrotnego opartego na haśle przeglądarki użytkownika końcowego mogą:

-   Internet Explorer 8, 9, 10, 11 — w systemie Windows 7 lub nowszym

-   Chrome — w systemie Windows 7 lub nowszym oraz w systemie MacOS X lub nowszym

-   Firefox 26,0 lub nowszy — w systemie Windows XP z dodatkiem SP2 lub nowszym oraz na Mac OS X 10,6 lub nowszym

>[!NOTE]
>Rozszerzenie logowania jednokrotnego oparte na hasłach staje się dostępne dla przeglądarki Microsoft Edge w systemie Windows 10, gdy rozszerzenia przeglądarek stają się obsługiwane w programie Microsoft Edge.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak zainstalować rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj następujące czynności:

1.  Otwórz [panel dostępu](https://myapps.microsoft.com) w jednej z obsługiwanych przeglądarek i zaloguj się jako **użytkownik** w usłudze Azure AD.

2.  Kliknij pozycję **hasło — logowanie** jednokrotne w panelu dostępu.

3.  W wierszu polecenia z monitem o zainstalowanie oprogramowania wybierz pozycję **Zainstaluj teraz**.

4.  W zależności od używanej przeglądarki do linku pobierania. **Dodaj** rozszerzenie do przeglądarki.

5.  Jeśli w przeglądarce zostanie wyświetlone pytanie, wybierz opcję **włączenia** lub **zezwolenia** na rozszerzenie.

6.  Po zainstalowaniu **Uruchom ponownie** sesję przeglądarki.

7.  Zaloguj się do panelu dostępu i sprawdź, czy możesz **uruchamiać** aplikacje logowania jednokrotnego

Możesz również pobrać rozszerzenie dla przeglądarki Chrome i Firefox z linków bezpośrednich poniżej:

-   [Rozszerzenie panelu dostępu dla programu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Konfigurowanie zasad grupy dla programu Internet Explorer

Można skonfigurować zasady grupy, które umożliwiają zdalne instalowanie rozszerzenia panelu dostępu dla programu Internet Explorer na komputerach użytkowników.

Wymagania wstępne obejmują:

-   Skonfigurowano [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)i przyłączono komputery użytkowników do domeny.

-   Aby edytować obiekt zasady grupy (GPO), musisz mieć uprawnienie "Edytuj ustawienia". Domyślnie członkowie następujących grup zabezpieczeń mają to uprawnienie: Administratorzy domeny, Administratorzy przedsiębiorstwa i właściciele zasady grupy Twórcy. [Dowiedz się więcej](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Postępuj zgodnie z samouczkiem [jak wdrożyć rozszerzenie panelu dostępu dla programu Internet Explorer przy użyciu zasady grupy](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) , aby uzyskać instrukcje krok po kroku dotyczące konfigurowania zasad grupy i wdrażania ich dla użytkowników.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Rozwiązywanie problemów z panelem dostępu w programie Internet Explorer

Postępuj zgodnie z instrukcjami w części [Rozwiązywanie problemów z rozszerzeniami panelu dostępu dla programu Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) , aby uzyskać dostęp do narzędzia diagnostycznego i instrukcje krok po kroku dotyczące konfigurowania rozszerzenia dla programu IE.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować Logowanie jednokrotne przy użyciu hasła dla aplikacji spoza galerii

Aby skonfigurować aplikację z galerii usługi Azure AD, należy wykonać następujące:

-   [Dodawanie aplikacji spoza galerii](#add-a-non-gallery-application)

-   [Konfigurowanie aplikacji do logowania jednokrotnego hasła](#configure-the-application-for-password-single-sign-on)

-   [Przypisywanie użytkowników do aplikacji](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Dodawanie aplikacji spoza galerii

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące czynności:

1.  Otwórz [Azure Portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub współadministrator

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5.  Kliknij przycisk **Dodaj** w prawym górnym rogu w okienku aplikacje dla **przedsiębiorstw** .

6.  Kliknij pozycję **aplikacja niebędąca galerią.**

7.  Wprowadź nazwę aplikacji w polu tekstowym **Nazwa** . Wybierz pozycję **Dodaj.**

Po krótkim czasie będzie możliwe wyświetlenie okienka konfiguracja aplikacji.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji do logowania jednokrotnego hasła

Aby skonfigurować Logowanie jednokrotne dla aplikacji, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub współadministrator **.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, dla której chcesz skonfigurować Logowanie jednokrotne

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Wybierz pozycję **Logowanie oparte na haśle.**

9. Wprowadź **adres URL logowania**. Jest to adres URL, pod którym użytkownicy wprowadzają nazwę użytkownika i hasło, aby się zalogować. Upewnij się, że pola logowania są widoczne pod adresem URL.

10. Przypisz użytkowników do aplikacji.

11. Dodatkowo można także podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając opcję **zaktualizuj poświadczenia** i wprowadzając nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy otrzymają monit o wprowadzenie poświadczeń podczas uruchamiania.

### <a name="assign-users-to-the-application"></a>Przypisywanie użytkowników do aplikacji

Aby przypisać co najmniej jednego użytkownika do aplikacji bezpośrednio, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz przypisać do użytkownika z listy.

7. Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Dodaj** przycisk w górnej części **użytkowników i grup** liście, aby otworzyć **Dodaj przydziału** okienka.

9. Kliknij przycisk **użytkowników i grup** selektor z **Dodaj przydziału** okienka.

10. Wpisz **Pełna nazwa** lub **adres e-mail** użytkownika, jesteś zainteresowany przypisywania do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **użytkownika** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok logo, aby dodać użytkownika, aby lub zdjęcie w profilu użytkownika **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, wpisz inną **pełną nazwę** lub **adres e-mail** w polu **Wyszukaj według nazwy lub adresu e-mail** , a następnie kliknij pole wyboru, aby dodać tego użytkownika do **wybranej** listy.

13. Gdy to zrobisz, Wybieranie użytkowników, kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup do przypisania do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektorze **Dodaj przydziału** okienku wybierz rolę, aby przypisać użytkownikom wybrania.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie wybrane przez Ciebie użytkownicy będą mogli uruchamiać te aplikacje w panelu dostępu.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie rozwiązują problemu

Otwórz bilet pomocy technicznej z następującymi informacjami, jeśli są dostępne:

-   Identyfikator błędu korelacji

-   Nazwa UPN (adres e-mail użytkownika)

-   TenantID

-   Typ przeglądarki

-   Strefa czasowa i czas/przedział czasu w trakcie wystąpienia błędu

-   Ślady programu Fiddler

## <a name="next-steps"></a>Następne kroki
[Udostępnij logowanie jednokrotne do aplikacji serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)

