---
title: Problem z logowaniem się do aplikacji galerii usługi Azure AD skonfigurowanych dla logowania sytuowanego | Dokumenty firmy Microsoft
description: Jak rozwiązywać problemy z aplikacją usługi Azure AD Gallery skonfigurowaną do logowania jednokrotnego hasła.
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
ms.openlocfilehash: e9fd17d9e066be6a1abff5165436a09b8921184e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381298"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Problemy z logowaniem się z aplikacją galerii usługi Azure AD skonfigurowaną dla logowania typu Loga

Panel dostępu jest portalem internetowym. Umożliwia użytkownikom, którzy mają usługi Azure Active Directory (Azure AD) pracy lub kont szkolnych dostępu do aplikacji opartych na chmurze, które mają uprawnienia do. Użytkownicy, którzy mają wersje usługi Azure AD, mogą również korzystać z funkcji samoobsługi grupy i zarządzania aplikacjami za pośrednictwem Panelu dostępu.

Panel dostępu jest oddzielony od witryny Azure portal. Użytkownicy nie potrzebują subskrypcji platformy Azure, aby korzystać z Panelu dostępu.

Aby używać logowania jednokrotnego opartego na hasłach w Panelu dostępu, rozszerzenie Panelu dostępu musi być zainstalowane w przeglądarce. Rozszerzenie zostanie pobrane automatycznie po wybraniu aplikacji skonfigurowanej dla loga loga bez podania przy logowane.

## <a name="browser-requirements-for-access-panel"></a>Wymagania dotyczące przeglądarki dla Panelu dostępu

Panel dostępu wymaga przeglądarki obsługującej język JavaScript i obsługującej css.

Następujące przeglądarki obsługują logi loga wieloskładnikowe oparte na hasłach:

- Internet Explorer 8, 9, 10 i 11 w systemie Windows 7 lub nowszym

- Chrome w systemie Windows 7 lub nowszym lub w systemie MacOS X lub nowszym

- Firefox 26.0 lub nowszy w systemie Windows XP z dodatkiem SP2 lub nowszym lub w systemie Mac OS X 10.6 lub nowszym

>[!NOTE]
>Rozszerzenie loga SSO oparte na hasłach staje się dostępne dla przeglądarki Microsoft Edge w systemie Windows 10, gdy obsługa rozszerzeń przeglądarki została dodana do przeglądarki Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Instalowanie rozszerzenia przeglądarki panelu dostępu

Wykonaj następujące kroki:

1. Otwórz [Panel dostępu](https://myapps.microsoft.com) w obsługiwanej przeglądarce i zaloguj się jako użytkownik w usłudze Azure AD.

2. Wybierz aplikację obsługującą funkcję logajną z hasłem w Panelu dostępu.

3. Po wyświetleniu monitu wybierz pozycję **Zainstaluj teraz**.

4. Zostaniesz przekierowany do linku do pobrania na podstawie przeglądarki. Wybierz **pozycję Dodaj,** aby zainstalować rozszerzenie przeglądarki.

5. Jeśli zostanie wyświetlony monit, wybierz pozycję **Włącz** lub **Zezwól**.

6. Po zakończeniu instalacji uruchom ponownie przeglądarkę.

7.  Zaloguj się do Panelu dostępu i sprawdź, czy możesz uruchomić aplikacje obsługujące funkcję logowania bez logowania do haseł.

Możesz również bezpośrednio pobrać rozszerzenia do Chrome i Firefox za pośrednictwem tych linków:

-   [Rozszerzenie panelu dostępu do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie Panelu dostępu do Firefoksa](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Konfigurowanie zasad grupy dla programu Internet Explorer

Można skonfigurować zasady grupy, które umożliwiają zdalne instalowanie rozszerzenia Panelu dostępu dla programu Internet Explorer na komputerach użytkowników.

Są to wymagania wstępne:

-   Należy skonfigurować [Usługi domenowe Active Directory,](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) a komputery użytkowników muszą zostać przyłączone do domeny.

-   Masz uprawnienie "Edytuj ustawienia" do edytowania obiektu zasad grupy (GPO). Domyślnie członkowie następujących grup zabezpieczeń mają takie uprawnienia: Administratorzy domeny, Administratorzy przedsiębiorstwa i Właściciele twórców zasad grupy. [Dowiedz się więcej](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Aby skonfigurować zasady grupy i wdrożyć ją dla użytkowników, zobacz [Jak wdrożyć rozszerzenie Panelu dostępu dla programu Internet Explorer przy użyciu zasad grupy](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Rozwiązywanie problemów z Panelem dostępu w programie Internet Explorer

Aby uzyskać dostęp do narzędzia diagnostycznego i instrukcji konfigurowania rozszerzenia, zobacz [Rozwiązywanie problemów z rozszerzeniem Panelu dostępu dla programu Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Konfigurowanie logajniania haseł dla aplikacji galerii usługi Azure AD

Aby skonfigurować aplikację z galerii usługi Azure AD, należy wykonać następujące czynności:

-   Dodawanie aplikacji z galerii usługi Azure AD
-   [Konfigurowanie aplikacji do logowania jednokrotnego hasła](#configure-the-app-for-password-sso)
-   [Przypisywanie użytkowników do aplikacji](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Wykonaj następujące kroki:

1. Otwórz [witrynę Azure portal](https://portal.azure.com) i zaloguj się jako administrator globalny lub współadministrator.

2. Wybierz **wszystkie usługi** w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **usługę Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję Azure Active **Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** z okienka nawigacji usługi Azure AD.

5. Wybierz **pozycję Dodaj** w prawym górnym rogu okienka **Aplikacje przedsiębiorstwa.**

6. W sekcji **Dodaj z galerii** wpisz nazwę aplikacji w polu **Wprowadź nazwę.**

7. Wybierz aplikację, którą chcesz skonfigurować dla aplikacji SSO.

8. *Opcjonalnie:* Przed dodaniem aplikacji można zmienić jej nazwę w polu **Nazwa.**

9. Kliknij przycisk **Dodaj**, aby dodać aplikację.

   Po krótkim opóźnieniu będzie można wyświetlić okienko konfiguracji aplikacji.

### <a name="configure-the-app-for-password-sso"></a>Konfigurowanie aplikacji dla hasła logować jako loga bez zmiany hasła

Wykonaj następujące kroki:

1. Otwórz [witrynę Azure portal](https://portal.azure.com/) i zaloguj się jako administrator globalny lub współadministrator.

2. Wybierz **wszystkie usługi** w górnej części okienka nawigacji po lewej stronie, aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **usługę Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję Azure Active **Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz żądanej aplikacji, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje**. Ustaw opcję **Pokaż** na "Wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować dla aplikacji SSO.

7. Po załadowaniu aplikacji wybierz **pozycję Logowanie jednokrotne** w okienku po lewej stronie aplikacji.

8. Wybierz **tryb logowania oparty na hasłach.**

9. Przypisz użytkowników do aplikacji.

10. Można również podać poświadczenia dla użytkowników. (W przeciwnym razie użytkownicy zostaną poproszeni o wprowadzenie poświadczeń podczas uruchamiania aplikacji). Aby to zrobić, zaznacz wiersze użytkowników. Następnie wybierz pozycję **Aktualizuj poświadczenia** i wprowadź ich nazwy użytkowników i hasła.

### <a name="assign-users-to-the-app"></a>Przypisywanie użytkowników do aplikacji

Aby bezpośrednio przypisać użytkowników do aplikacji, wykonaj następujące czynności:

1. Otwórz [witrynę Azure portal](https://portal.azure.com/) i zaloguj się jako administrator globalny.

2. Wybierz **wszystkie usługi** w bólu nawigacji po lewej stronie, aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **usługę Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję Azure Active **Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz żądanej aplikacji, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje**. Ustaw opcję **Pokaż** na "Wszystkie aplikacje".

6. Z listy wybierz aplikację, do której chcesz przypisać użytkownika.

7. Po załadowaniu aplikacji wybierz **pozycję Użytkownicy i grupy** z okienka nawigacji aplikacji po lewej stronie.

8. Wybierz **pozycję Dodaj** u góry listy **Użytkownicy i grupy,** aby otworzyć okienko **Dodaj przypisanie.**

9. Wybierz **pozycję Użytkownicy i grupy** w okienku Dodawanie **przydziału.**

10. W polu **Wyszukiwanie według nazwy lub adresu e-mail** wpisz imię i nazwisko lub adres e-mail użytkownika, który chcesz przypisać.

11. Umieść wskaźnik myszy na użytkowniku na liście. Zaznacz pole wyboru obok zdjęcia profilowego lub logo użytkownika, aby dodać go do listy **Zaznaczone.**

12. *Opcjonalnie:* Aby dodać innego użytkownika, wpisz inną nazwę lub adres e-mail w polu **Wyszukiwanie według nazwy lub adresu e-mail,** a następnie zaznacz pole wyboru, aby dodać go do listy **Zaznaczone.**

13. Po zakończeniu wybierania użytkowników kliknij przycisk **Wybierz,** aby dodać ich do listy użytkowników i grup przypisanych do aplikacji.

14. *Opcjonalnie:* Kliknij **pozycję Wybierz rolę** w okienku Dodaj **przypisanie,** aby wybrać rolę, którą chcesz przypisać do wybranych użytkowników.

15. Wybierz **pozycję Przypisz,** aby przypisać aplikację do wybranych użytkowników.

    Po krótkim opóźnieniu użytkownicy będą mogli uzyskać dostęp do tych aplikacji z Panelu dostępu.

## <a name="request-support"></a>Poproś o pomoc techniczną 
Jeśli podczas konfigurowania aplikacji SSO i przypisywania użytkowników zostanie wyświetlony komunikat o błędzie, otwórz bilet pomocy technicznej. Podaj jak najwięcej z następujących informacji:

-   Identyfikator błędu korelacji
-   UPN (adres e-mail użytkownika)
-   Identyfikator dzierżawy
-   Typ przeglądarki
-   Strefa czasowa i czas/ramy czasowe, kiedy wystąpił błąd
-   Ślady skrzypka

## <a name="next-steps"></a>Następne kroki
[Zapewnianie logowania jednokrotnego do aplikacji za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
