---
title: Problem z konfigurowaniem hasło logowania jednokrotnego dla aplikacji galerii usługi Azure AD | Dokumentacja firmy Microsoft
description: Zrozumienie typowych twarzy osób problemy podczas konfigurowania hasła logowania jednokrotnego dla aplikacji, które już są wymienione w galerii aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: a35ef95074099499186eae0fadd37f1995d8e725
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190298"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problem z konfigurowaniem hasło logowania jednokrotnego dla aplikacji galerii usługi Azure AD

Ten artykuł pomoże Ci zrozumieć wspólnej twarzy osób problemy podczas konfigurowania **hasło logowania jednokrotnego** za pomocą aplikacji galerii usługi Azure AD.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Poświadczenia są wypełniane, ale rozszerzenie nie przedstawi je

Ten problem zazwyczaj ma miejsce, jeśli dostawca aplikacji ma zmienić ich strony logowania, ostatnio, aby dodać pole, zmienić identyfikator używany na potrzeby wykrywania pola nazwy użytkownika i hasło lub zmodyfikowane, jak obsługa logowania w działa w przypadku aplikacji. Na szczęście w wielu przypadkach firma Microsoft może współpracować z dostawcami aplikacji, aby szybko rozwiązać te problemy.

Gdy firma Microsoft ma technologie, aby automatycznie wykryć, kiedy integracji break, nie może nie być możliwe znaleźć problemy następnie od razu lub problemy zająć trochę czasu, aby rozwiązać problem. W przypadku gdy jeden z tych integracji nie działa prawidłowo, otwórz zgłoszenie do pomocy technicznej, można naprawić tak szybko, jak to możliwe.

**Jeśli jesteś kontaktu z usługą dostawcy tej aplikacji** wysłać im NAS, dzięki czemu firma Microsoft może współpracować z nimi natywnie integracji aplikacji z usługi Azure Active Directory. Możesz wysłać z dostawcą, aby [listę aplikacji w galerii aplikacji usługi Azure Active Directory](../develop/howto-app-gallery-listing.md) do ich pracy.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Poświadczenia są wypełnione i przesyłane, ale strona wskazuje, że poświadczenia są nieprawidłowe

Aby rozwiązać ten problem, spróbuj najpierw te rzeczy:

- Użytkownik najpierw spróbują **bezpośrednio zalogować się do witryny sieci Web aplikacji** z poświadczeniami przechowywanymi dla nich.

  * Jeśli działa logowanie, należy mieć użytkownika, kliknij przycisk **zaktualizować poświadczenia** znajdujący się na **kafelka aplikacji** w **aplikacje** części [dostęp do aplikacji Panel](https://myapps.microsoft.com/) je zaktualizować najnowszych znane pracy nazwy użytkownika i hasło.

  * Jeśli użytkownik lub inny administrator przypisane poświadczenia dla tego użytkownika, odnaleźć użytkownika lub przypisanie do grupy aplikacji, przechodząc do **użytkownikami i grupami** kartę aplikacji, wybierając przypisania, a następnie klikając polecenie  **Zaktualizuj poświadczenia** przycisku.

- Jeśli użytkownikowi przypisano własnych poświadczeń, należy mieć użytkownika **wyboru należy upewnić się, że ich hasło nie wygasł w aplikacji** , a jeśli tak, **aktualizacja wygasłego hasła** , logując się do aplikacji bezpośrednio.

  * Po aktualizacji hasła w aplikacji, żądanie użytkownika, kliknij pozycję **zaktualizować poświadczenia** znajdujący się na **kafelka aplikacji** w **aplikacje** sekcji [Panelu dostępu do aplikacji](https://myapps.microsoft.com/) je zaktualizować najnowszych znane pracy nazwy użytkownika i hasło.

  * Jeśli użytkownik lub inny administrator przypisane poświadczenia dla tego użytkownika, odnaleźć użytkownika lub przypisanie do grupy aplikacji, przechodząc do **użytkownikami i grupami** kartę aplikacji, wybierając przypisania, a następnie klikając polecenie  **Zaktualizuj poświadczenia** przycisku.

- Użytkownik powinien zaktualizować rozszerzenie przeglądarki panelu dostępu, wykonując poniższe kroki w [jak zainstalować rozszerzenie przeglądarki panelu dostępu](#how-to-install-the-access-panel-browser-extension) sekcji.

- Upewnij się, że rozszerzenie przeglądarki panelu dostępu jest uruchomiony i włączone w przeglądarce użytkownika.

- Upewnij się, że użytkownicy nie próbuje zalogować się do aplikacji z poziomu panelu dostępu podczas w **trybu incognito, przeglądanie inPrivate lub prywatnych**. Rozszerzenie panelu dostępu nie jest obsługiwane w tych trybach.

W przypadku, gdy nie działają powyższych sugestii, może to być tak, że nastąpiła zmiana na stronie aplikacji, która została tymczasowo uszkodzone, integracji aplikacji z usługą Azure AD. Na przykład to może wystąpić, gdy dostawca aplikacji wprowadza skryptu na swojej stronie, która zależy od ręcznego vs automatycznie danych wejściowych, co powoduje, że zautomatyzowane integracji, takich jak własną, aby przerwać. Na szczęście w wielu przypadkach firma Microsoft może współpracować z dostawcami aplikacji, aby szybko rozwiązać te problemy.

Gdy firma Microsoft ma technologie, aby automatycznie wykryć, kiedy Przerwij integracji aplikacji, nie może nie być możliwe znaleźć problemy następnie od razu lub problemów może zająć trochę czasu, aby rozwiązać problem. Podczas integracji nie działa prawidłowo, możesz otworzyć sprawę pomocy technicznej, aby został rozwiązany jak najszybciej. 

Oprócz tego **przypadku kontaktu z usługą dostawcy tej aplikacji** **przysłanie NAS** firma Microsoft może więc pracować z nimi natywnie integracji aplikacji z usługi Azure Active Directory. Możesz wysłać z dostawcą, aby [listę aplikacji w galerii aplikacji usługi Azure Active Directory](../develop/howto-app-gallery-listing.md) do ich pracy.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>To rozszerzenie działa w przeglądarce Chrome i Firefox, ale nie w programie Internet Explorer

Istnieją dwie główne przyczyny tego problemu:

- W zależności od ustawienia zabezpieczeń włączone w programie Internet Explorer, jeśli witryna sieci Web nie jest częścią **zaufane**, czasami naszego skryptu zablokowany wykonywania dla aplikacji.

  *  Aby rozwiązać ten problem, poinstruuj użytkownika, aby **Dodaj witrynę sieci Web aplikacji** do **Zaufane witryny** listy w ramach ich **ustawienia zabezpieczeń programu Internet Explorer**. Możesz wysłać użytkownikom [sposób dodawania witryny do listy zaufanych witryn](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) artykuł, aby uzyskać szczegółowe instrukcje.

- W rzadkich przypadkach walidacji zabezpieczeń programu Internet Explorer mogą wywoływać strony aby ładują się wolniej niż wykonywanie naszego skryptu.

  * Niestety ta sytuacja może się różnić w zależności od wersji przeglądarki, szybkość komputera lub odwiedzi witrynę. W tym przypadku zaleca się, czy możesz się z pomocą techniczną abyśmy mogli rozwiązać integracji dla tej konkretnej aplikacji.

Oprócz tego **przypadku kontaktu z usługą dostawcy tej aplikacji** **przysłanie NAS** firma Microsoft może więc pracować z nimi natywnie integracji aplikacji z usługi Azure Active Directory. Możesz wysłać z dostawcą, aby [listę aplikacji w galerii aplikacji usługi Azure Active Directory](../develop/howto-app-gallery-listing.md) do ich pracy.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Sprawdź, czy strona logowania aplikacji został ostatnio zmieniony lub wymaga dodatkowego pola

Jeśli strona logowania aplikacji zmienił się znacząco, czasami powoduje to nasze integracji przerwać. Na przykład jest, gdy dostawcy aplikacji dodaje pól logowania, captcha lub uwierzytelnianie wieloskładnikowe do swojego środowiska. Na szczęście w wielu przypadkach firma Microsoft może współpracować z dostawcami aplikacji, aby szybko rozwiązać te problemy.

Gdy firma Microsoft ma technologie, aby automatycznie wykryć, kiedy Przerwij integracji aplikacji, nie może nie być możliwe znaleźć problemy następnie od razu lub problemów może zająć trochę czasu, aby rozwiązać problem. Podczas integracji nie działa prawidłowo, możesz otworzyć sprawę pomocy technicznej, aby został rozwiązany jak najszybciej. 

Oprócz tego **przypadku kontaktu z usługą dostawcy tej aplikacji** **przysłanie NAS** firma Microsoft może więc pracować z nimi natywnie integracji aplikacji z usługi Azure Active Directory. Możesz wysłać z dostawcą, aby [listę aplikacji w galerii aplikacji usługi Azure Active Directory](../develop/howto-app-gallery-listing.md) do ich pracy.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak zainstalować rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj następujące czynności:

1.  Otwórz [panelu dostępu](https://myapps.microsoft.com) w jednym z obsługiwanych przeglądarek i zaloguj się jako **użytkownika** w usłudze Azure AD.

2.  Kliknij przycisk **logowania jednokrotnego hasła aplikacji** w panelu dostępu.

3.  W oknie komunikatu z pytaniem zainstalować oprogramowanie, wybierz **Zainstaluj teraz**.

4.  W zależności od przeglądarki są kierowane do link pobierania. **Dodaj** rozszerzenie do przeglądarki.

5.  Jeśli przeglądarka zapyta, wybrać opcję **Włącz** lub **Zezwalaj** rozszerzenia.

6.  Po zainstalowaniu **ponowne uruchomienie** sesji przeglądarki.

7.  Zaloguj się do panelu dostępu i zobacz, jeśli możesz to zrobić **Uruchom** logowania jednokrotnego hasła aplikacji

Może również Pobierz rozszerzenie dla programu Chrome i Firefox z bezpośrednie linki poniżej:

-   [Rozszerzenie panelu dostępu dla programu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu do przeglądarki Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Kolejne kroki
[Udostępnij logowanie jednokrotne do aplikacji serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)

