---
title: Problem podczas konfiguracji hasła logowanie jednokrotne dla aplikacji w galerii Azure AD | Dokumentacja firmy Microsoft
description: Zrozumienie kroju osób typowe problemy podczas konfigurowania haseł logowanie jednokrotne dla aplikacji, które są już wyświetlane w galerii aplikacji usługi Azure AD
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
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 37c2a50a3aca281596a06e0d666edff05347f660
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331159"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problem podczas konfiguracji hasła logowanie jednokrotne dla aplikacji w galerii Azure AD

Ten artykuł pomaga w zrozumieniu kroju osób typowe problemy podczas konfigurowania **hasło logowania jednokrotnego** przy użyciu aplikacji do galerii Azure AD.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Poświadczenia są wypełniane, ale rozszerzenie nie przekazywać

Ten problem zwykle odbywa się z dostawcą aplikacji ma zmienić ich strony logowania ostatnio, aby dodać pole, zmienić identyfikator używany do wykrywania pola Nazwa użytkownika i hasło lub zmodyfikowane, jak obsługa logowania w działa w przypadku ich aplikacji. Na szczęście w wielu przypadkach firmy Microsoft może współpracować z dostawcami aplikacji w celu szybkiego rozwiązywania tych problemów.

Gdy firma Microsoft udostępnia technologie, aby automatycznie Wykryj, kiedy Podziel integracji, może nie być możliwe można znaleźć problemy od razu lub problemy zająć trochę czasu, aby rozwiązać. W przypadku gdy jeden z tych integracji nie działa prawidłowo, otwórz sprawę pomocy technicznej, można ustalić tak szybko, jak to możliwe.

**W przypadku kontaktu z dostawcą tej aplikacji,** wysyłać je sposób, aby firma Microsoft może współpracować z ich natywnie integracja aplikacji z usługą Azure Active Directory. Możesz wysłać z dostawcą, aby [listę aplikacji w galerii aplikacji usługi Azure Active Directory](./develop/active-directory-app-gallery-listing.md) do ich pracy.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Poświadczenia są wypełnione i przesłane, ale strona wskazuje, że poświadczenia są nieprawidłowe

Aby rozwiązać ten problem, należy najpierw spróbuj użyć następujących rozwiązań:

-   Użytkownik pierwszej próby **Zaloguj się do witryny sieci Web aplikacji bezpośrednio** przy użyciu poświadczeń przechowywanych dla nich.

  * Jeśli działa logowanie ma użytkownika, kliknij przycisk **zaktualizować poświadczenia** znajdującego się na **kafelka aplikacji** w **aplikacje** sekcji [dostęp do aplikacji Panel](https://myapps.microsoft.com/) ich aktualizacji do najnowszej znane pracy nazwy użytkownika i hasła.

   * Jeśli użytkownik lub inny administrator przypisane poświadczenia dla tego użytkownika, znaleźć użytkownika lub grupy aplikacji przydziału, przechodząc do **użytkownicy i grupy** aplikacji, wybierając przypisania i klikając pozycję  **Zaktualizuj poświadczenia** przycisku.

-   Jeśli użytkownik jest przypisany własnymi poświadczeniami, ma użytkownika **wyboru należy upewnić się, że ich hasła nie wygasł w aplikacji** , a jeśli tak, **aktualizacja wygasłego hasła** logując się do aplikacji bezpośrednio.

   * Po zaktualizowaniu hasło aplikacji żądania użytkownika do kliknięcia **zaktualizować poświadczenia** znajdującego się na **kafelka aplikacji** w **aplikacji** sekcji [Panelu dostępu aplikacji](https://myapps.microsoft.com/) ich aktualizacji do najnowszej znane pracy nazwy użytkownika i hasła.

   * Jeśli użytkownik lub inny administrator przypisane poświadczenia dla tego użytkownika, znaleźć użytkownika lub grupy aplikacji przydziału, przechodząc do **użytkownicy i grupy** aplikacji, wybierając przypisania i klikając pozycję  **Zaktualizuj poświadczenia** przycisku.

-   Użytkownik powinien zaktualizować rozszerzenia przeglądarki panel dostępu, wykonując poniższe kroki w [jak zainstalować rozszerzenie przeglądarki panelu dostępu](#how-to-install-the-access-panel-browser-extension) sekcji.

-   Upewnij się, że rozszerzenie przeglądarki panel dostępu jest uruchomiony i włączone w przeglądarce użytkownika.

-   Upewnij się, że użytkownicy nie próbuje zalogować się do aplikacji w panelu dostępu podczas w **incognito, przeglądanie inPrivate lub prywatnej tryb**. Rozszerzenie panelu dostępu nie jest obsługiwana w tych trybach.

W przypadku poprzednich sugestie nie działa, może to być to zostało zmienione po stronie aplikacji tymczasowo podzielonym integracji aplikacji z usługą Azure AD. Na przykład to może wystąpić, gdy wprowadza się z dostawcą aplikacji w danych wejściowych, co powoduje, że automatyczne integracji, takich jak własnej, aby przerwać automatycznie skrypt na ich strona, która zależy od ręczne vs. Na szczęście w wielu przypadkach firmy Microsoft może współpracować z dostawcami aplikacji w celu szybkiego rozwiązywania tych problemów.

Gdy firma Microsoft udostępnia technologie, aby automatycznie Wykryj, kiedy Podziel integracji aplikacji, może nie być możliwe można znaleźć problemy od razu lub problemów może zająć trochę czasu, aby rozwiązać problem. Podczas integracji nie działa poprawnie, możesz otworzyć sprawę pomocy technicznej, aby został rozwiązany możliwie jak najszybciej. 

Oprócz tego **w przypadku kontaktu z dostawcą tej aplikacji,** **wysyłać je sposób** , firma Microsoft może pracować z nimi natywnie integracja aplikacji z usługą Azure Active Directory. Możesz wysłać z dostawcą, aby [listę aplikacji w galerii aplikacji usługi Azure Active Directory](./develop/active-directory-app-gallery-listing.md) do ich pracy.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>Rozszerzenie działania w Chrome, a program Firefox, ale nie w programie Internet Explorer

Istnieją dwa główne przyczyny tego problemu:

-   W zależności od ustawienia zabezpieczeń włączone w programie Internet Explorer, jeśli witryna sieci Web nie jest częścią **zaufane**, czasami naszych skryptu zablokować dostęp do wykonywania dla aplikacji.

  *  Aby rozwiązać ten problem, poinstruuj użytkowników, aby **Dodaj witrynę sieci Web aplikacji** do **Zaufane witryny** listy w ich **ustawień zabezpieczeń programu Internet Explorer**. Możesz wysłać użytkownikom [sposób dodawania witryny do listy zaufanych witryn](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) artykuł, aby uzyskać szczegółowe instrukcje.

-   W rzadkich przypadkach walidacji zabezpieczeń programu Internet Explorer mogą wywoływać stronę, aby załadować wolniej niż wykonanie naszych skryptu.

   * Niestety ta sytuacja zależy od wersji przeglądarki, szybkość komputera lub odwiedzi witrynę. W takim przypadku zaleca się z obsługą, możemy naprawić integracji dla tej określonej aplikacji.

Oprócz tego **w przypadku kontaktu z dostawcą tej aplikacji,** **wysyłać je sposób** , firma Microsoft może pracować z nimi natywnie integracja aplikacji z usługą Azure Active Directory. Możesz wysłać z dostawcą, aby [listę aplikacji w galerii aplikacji usługi Azure Active Directory](./develop/active-directory-app-gallery-listing.md) do ich pracy.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Sprawdź, czy strona logowania aplikacji został ostatnio zmieniony lub wymaga dodatkowego pola

Jeśli strona logowania aplikacji zmienił znacząco, czasami powoduje naszych integracji przerwanie. Przykładem jest dostawcą aplikacji dodaje pola logowania, captcha lub uwierzytelnianie wieloskładnikowe się swoimi doświadczeniami. Na szczęście w wielu przypadkach firmy Microsoft może współpracować z dostawcami aplikacji w celu szybkiego rozwiązywania tych problemów.

Gdy firma Microsoft udostępnia technologie, aby automatycznie Wykryj, kiedy Podziel integracji aplikacji, może nie być możliwe można znaleźć problemy od razu lub problemów może zająć trochę czasu, aby rozwiązać problem. Podczas integracji nie działa poprawnie, możesz otworzyć sprawę pomocy technicznej, aby został rozwiązany możliwie jak najszybciej. 

Oprócz tego **w przypadku kontaktu z dostawcą tej aplikacji,** **wysyłać je sposób** , firma Microsoft może pracować z nimi natywnie integracja aplikacji z usługą Azure Active Directory. Możesz wysłać z dostawcą, aby [listę aplikacji w galerii aplikacji usługi Azure Active Directory](./develop/active-directory-app-gallery-listing.md) do ich pracy.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak zainstalować rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj następujące czynności:

1.  Otwórz [panelu dostępu](https://myapps.microsoft.com) w jednym z obsługiwanych przeglądarkach i zaloguj się jako **użytkownika** w usługi Azure AD.

2.  Kliknij przycisk **logowania jednokrotnego hasła aplikacji** w panelu dostępu.

3.  W oknie komunikatu z pytaniem do zainstalowania oprogramowania, zaznaczyć **Zainstaluj teraz**.

4.  Oparte na przeglądarce, użytkownik jest kierowany do łącza pobierania. **Dodaj** rozszerzenia do przeglądarki.

5.  Jeśli przeglądarka pytanie, wybierz albo **włączyć** lub **Zezwalaj** rozszerzenia.

6.  Wcześniej zainstalowano **ponowne uruchomienie** sesji przeglądarki.

7.  Zaloguj się do panelu dostępu i zobacz, czy można **uruchamianie** logowania jednokrotnego hasła aplikacji

Może również pobrać rozszerzenia dla programu Chrome, a program Firefox z bezpośrednich łączy poniżej:

-   [Rozszerzenie panelu dostępu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Kolejne kroki
[Podaj logowanie jednokrotne do aplikacji przy użyciu serwera Proxy aplikacji](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

