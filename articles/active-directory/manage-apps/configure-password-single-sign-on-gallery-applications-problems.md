---
title: Problemy z konfigurowaniem hasła logowania przy logowaniu do aplikacji Usługi Azure AD Gallery
description: Opis typowych problemów napotykanych przez użytkowników podczas konfigurowania logowania jednokrotnego hasła dla aplikacji, które są już wymienione w Galerii aplikacji usługi Azure AD
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
ms.openlocfilehash: e933c82229415a71182096a6aca9a2e535934d89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159050"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problem z konfigurowaniem logowania jednokrotnego hasła dla aplikacji usługi Azure AD Gallery

Ten artykuł pomaga zrozumieć typowe problemy, z którymi borykają się użytkownicy podczas konfigurowania **logowania jednokrotnego hasła za** pomocą aplikacji usługi Azure AD Gallery.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Poświadczenia są wypełniane, ale rozszerzenie nie przesyła ich

Ten problem zazwyczaj występuje, jeśli dostawca aplikacji niedawno zmienił stronę logowania, aby dodać pole, zmienił identyfikator używany do wykrywania pól nazwy użytkownika i hasła lub zmodyfikował sposób działania środowiska logowania dla ich aplikacji. Na szczęście w wielu przypadkach firma Microsoft może współpracować z dostawcami aplikacji, aby szybko rozwiązać te problemy.

Firma Microsoft ma technologie umożliwiające automatyczne wykrywanie przerwania integracji, ale znalezienie problemów może nie być możliwe od razu lub rozwiązanie problemów może zająć trochę czasu. W przypadku, gdy jedna z tych integracji nie działa poprawnie, otwórz przypadek pomocy technicznej, aby można było go naprawić tak szybko, jak to możliwe.

**Jeśli masz kontakt z dostawcą tej aplikacji,** wyślij je w nasz sposób, aby firma Microsoft mogła z nimi współpracować, aby natywnie zintegrować ich aplikację z usługą Azure Active Directory. Możesz wysłać dostawcę do [listy aplikacji w galerii aplikacji usługi Azure Active Directory,](../azuread-dev/howto-app-gallery-listing.md) aby rozpocząć.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Poświadczenia są wypełniane i przesyłane, ale strona wskazuje, że poświadczenia są niepoprawne

Aby rozwiązać ten problem, najpierw spróbuj wykonać następujące czynności:

- Poproś użytkownika o uprzednią próbę **zalogowania się do witryny sieci Web aplikacji bezpośrednio** przy użyciu przechowywanych dla nich poświadczeń.

  * Jeśli logowanie działa, poproś użytkownika o kliknięcie przycisku **Aktualizuj poświadczenia** na **kafelku Aplikacji** w sekcji **Aplikacje** w Panelu dostępu do [aplikacji,](https://myapps.microsoft.com/) aby zaktualizować je do najnowszej znanej działającej nazwy użytkownika i hasła.

  * Jeśli ty lub inny administrator przypisał poświadczenia dla tego użytkownika, znajdź przypisanie aplikacji użytkownika lub grupy, przechodząc do karty **Użytkownicy & Grupy** aplikacji, wybierając przypisanie i klikając przycisk **Aktualizuj poświadczenia.**

- Jeśli użytkownik przypisał własne poświadczenia, należy sprawdzić, czy użytkownik **ma pewność, że jego hasło nie wygasło w aplikacji,** a jeśli tak, **zaktualizuj swoje wygasłe hasło,** logując się bezpośrednio do aplikacji.

  * Po zaktualizowaniu hasła w aplikacji poproś użytkownika o kliknięcie przycisku **Aktualizuj poświadczenia** na **kafelku Aplikacji** w sekcji **Aplikacje** w Panelu dostępu do [aplikacji,](https://myapps.microsoft.com/) aby zaktualizować je do najnowszej znanej działającej nazwy użytkownika i hasła.

  * Jeśli ty lub inny administrator przypisał poświadczenia dla tego użytkownika, znajdź przypisanie aplikacji użytkownika lub grupy, przechodząc do karty **Użytkownicy & Grupy** aplikacji, wybierając przypisanie i klikając przycisk **Aktualizuj poświadczenia.**

- Należy zaktualizować rozszerzenie przeglądarki panelu dostępu, wykonując poniższe czynności opisane w sekcji [Jak zainstalować rozszerzenie przeglądarki panelu dostępu.](#how-to-install-the-access-panel-browser-extension)

- Upewnij się, że rozszerzenie przeglądarki panelu dostępu jest uruchomione i włączone w przeglądarce użytkownika.

- Upewnij się, że użytkownicy nie próbują zalogować się do aplikacji z panelu dostępu w **trybie incognito, inPrivate lub Private**. Rozszerzenie panelu dostępu nie jest obsługiwane w tych trybach.

W przypadku, gdy poprzednie sugestie nie działają, może być tak, że nastąpiła zmiana po stronie aplikacji, która tymczasowo przerwała integrację aplikacji z usługą Azure AD. Na przykład może to nastąpić, gdy dostawca aplikacji wprowadza skrypt na swojej stronie, który zachowuje się inaczej dla ręcznego i automatycznego wprowadzania danych, co powoduje automatyczną integrację, jak nasza własna, do przerwania. Na szczęście w wielu przypadkach firma Microsoft może współpracować z dostawcami aplikacji, aby szybko rozwiązać te problemy.

Firma Microsoft ma technologie umożliwiające automatyczne wykrywanie przerwania integracji aplikacji, ale znalezienie problemów może nie być możliwe od razu lub rozwiązanie problemów może zająć trochę czasu. Gdy integracja nie działa poprawnie, można otworzyć przypadek pomocy technicznej, aby uzyskać go naprawić tak szybko, jak to możliwe. 

Oprócz tego, **jeśli masz kontakt z dostawcą tej aplikacji,** wyślij je na **nasz sposób,** dzięki czemu możemy pracować z nimi, aby natywnie zintegrować ich aplikację z usługą Azure Active Directory. Możesz wysłać dostawcę do [listy aplikacji w galerii aplikacji usługi Azure Active Directory,](../azuread-dev/howto-app-gallery-listing.md) aby rozpocząć.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>Rozszerzenie działa w Chrome i Firefox, ale nie w programie Internet Explorer

Istnieją dwie główne przyczyny tego problemu:

- W zależności od ustawień zabezpieczeń włączonych w programie Internet Explorer, jeśli witryna sieci Web nie jest częścią **strefy zaufanej,** czasami nasz skrypt może być zablokowany przed wykonaniem dla aplikacji.

  *  Aby rozwiązać ten problem, poinstruuj **użytkownika, aby dodał witrynę sieci Web aplikacji** do listy **Zaufane witryny** w **ustawieniach zabezpieczeń programu Internet Explorer**. Możesz wysłać użytkowników do artykułu [Jak dodać witrynę do listy Moje zaufane witryny, aby](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) uzyskać szczegółowe instrukcje.

- W rzadkich przypadkach sprawdzanie poprawności zabezpieczeń programu Internet Explorer może czasami spowodować, że strona będzie ładowana wolniej niż wykonanie naszego skryptu.

  * Niestety, sytuacja ta może się różnić w zależności od wersji przeglądarki, szybkość komputera, lub odwiedzanych witryn. W takim przypadku zalecamy skontaktowanie się z pomocą techniczną, abyśmy mogli rozwiązać problem integracji dla tej konkretnej aplikacji.

Oprócz tego, **jeśli masz kontakt z dostawcą tej aplikacji,** wyślij je na **nasz sposób,** dzięki czemu możemy pracować z nimi, aby natywnie zintegrować ich aplikację z usługą Azure Active Directory. Możesz wysłać dostawcę do [listy aplikacji w galerii aplikacji usługi Azure Active Directory,](../azuread-dev/howto-app-gallery-listing.md) aby rozpocząć.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Sprawdź, czy strona logowania aplikacji uległa ostatnio zmianie lub wymaga dodatkowego pola

Jeśli strona logowania aplikacji zmieniła się drastycznie, czasami powoduje to przerwanie naszych integracji. Przykładem tego jest, gdy dostawca aplikacji dodaje pole logowania, captcha lub uwierzytelniania wieloskładnikowego do swoich środowisk. Na szczęście w wielu przypadkach firma Microsoft może współpracować z dostawcami aplikacji, aby szybko rozwiązać te problemy.

Firma Microsoft ma technologie umożliwiające automatyczne wykrywanie przerwania integracji aplikacji, ale znalezienie problemów może nie być możliwe od razu lub rozwiązanie problemów może zająć trochę czasu. Gdy integracja nie działa poprawnie, można otworzyć przypadek pomocy technicznej, aby uzyskać go naprawić tak szybko, jak to możliwe. 

Oprócz tego, **jeśli masz kontakt z dostawcą tej aplikacji,** wyślij je na **nasz sposób,** dzięki czemu możemy pracować z nimi, aby natywnie zintegrować ich aplikację z usługą Azure Active Directory. Możesz wysłać dostawcę do [listy aplikacji w galerii aplikacji usługi Azure Active Directory,](../azuread-dev/howto-app-gallery-listing.md) aby rozpocząć.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak zainstalować rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj poniższe czynności:

1.  Otwórz [Panel dostępu](https://myapps.microsoft.com) w jednej z obsługiwanych przeglądarek i zaloguj się jako **użytkownik** w usłudze Azure AD.

2.  kliknij **aplikację** logajną hasła w Panelu dostępu.

3.  W wierszu z prośbą o zainstalowanie oprogramowania wybierz pozycję **Zainstaluj teraz**.

4.  Na podstawie przeglądarki, jesteś kierowany do linku do pobrania. **Dodaj** rozszerzenie do przeglądarki.

5.  Jeśli twoja przeglądarka poprosi, wybierz opcję **Włącz** lub **Zezwól** na rozszerzenie.

6.  Po zainstalowaniu **uruchom ponownie** sesję przeglądarki.

7.  Zaloguj się do Panelu dostępu i sprawdź, czy możesz **uruchomić** aplikacje logowania bez logowania do haseł

Możesz również pobrać rozszerzenie dla Chrome i Firefox z bezpośrednich linków poniżej:

-   [Rozszerzenie panelu dostępu do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu do Firefoksa](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Następne kroki
[Zapewnianie logowania jednokrotnego do aplikacji za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)

