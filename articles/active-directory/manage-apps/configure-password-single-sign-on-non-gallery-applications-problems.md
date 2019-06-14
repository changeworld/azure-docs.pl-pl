---
title: Problem z konfigurowaniem hasło logowania jednokrotnego dla aplikacji spoza galerii | Dokumentacja firmy Microsoft
description: Zrozumienie typowych twarzy osób problemy podczas konfigurowania hasła logowania jednokrotnego dla aplikacji spoza galerii niestandardowych, które nie są wyświetlane w galerii aplikacji usługi Azure AD
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8787008b396c2dd8ce1c006a40fee1e32e8100d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60442068"
---
# <a name="problem-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problem z konfigurowaniem hasło logowania jednokrotnego dla aplikacji spoza galerii

Ten artykuł ułatwia zrozumienie typowych twarzy osób problemy podczas konfigurowania **hasło logowania jednokrotnego** za pomocą aplikacji spoza galerii.

## <a name="how-to-capture-sign-in-fields-for-an-application"></a>Jak przechwycić pola logowania dla aplikacji

Przechwyć pola logowania jest obsługiwana tylko w przypadku komputerów z obsługą HTML strony logowania i jest **nieobsługiwane w przypadku niestandardowych stron logowania**, podobne do tych korzystających z technologii Flash lub innych włączone HTML.

Istnieją dwie metody pól logowania można przechwycić do niestandardowych aplikacji:

-   Przechwytywanie pól automatycznego logowania

-   Przechwytywanie ręczne pól logowania

**Automatyczne pól logowania przechwytywania** dobrze działa z większości włączone HTML strony logowania, jeśli używają one **dobrze znane identyfikatory DIV dla nazwy użytkownika i hasło, wprowadź** pola. To działania jest oskrobaniu HTML na stronie, aby znaleźć identyfikatory DIV, spełniających określone kryteria i następnie zapisywania tych metadanych dla tej aplikacji w celu odtworzenia haseł do niego później.

**Ręczne pól logowania przechwytywania** mogą być używane w przypadku, aplikacja **dostawcy nie nadaje etykiety** pola wejściowe, używane do logowania. Przechwytywanie ręczne pól logowania można również w przypadku gdy **dostawcy renderuje wielu pól** , nie może być wykrywane automatycznie. Usługa Azure AD może przechowywać dane na dowolną liczbę pól znajdują się na stronie logowania, tak długo, jak możesz Powiedz nam, gdzie te pola znajdują się na stronie.

Ogólnie rzecz biorąc **Jeśli przechwytywania pól automatycznego logowania nie działa, spróbuj opcji ręcznej.**

### <a name="how-to-automatically-capture-sign-in-fields-for-an-application"></a>Jak automatycznie Przechwyć pola logowania dla aplikacji

Aby skonfigurować **opartego na hasłach logowania jednokrotnego** dla aplikacji za pomocą **przechwytywania pól automatycznego logowania**, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Wybierz tryb **opartego na hasłach logowania jednokrotnego.**

9. Wprowadź **adres URL logowania**, adres URL, gdzie użytkownicy wprowadzają swoją nazwę i hasło do logowania. **Upewnij się pola logowania są widoczne w adresie URL, możesz podać**.

10. Kliknij przycisk **Zapisz**.

11. Po wykonaniu, adres URL jest automatycznie pozyskany o nazwę użytkownika i hasło pole wprowadzania i pozwalają na korzystanie z usługi Azure AD na bezpieczne przesyłanie haseł do tej aplikacji przy użyciu rozszerzenia przeglądarki panelu dostępu.

## <a name="how-to-manually-capture-sign-in-fields-for-an-application"></a>Jak ręcznie Przechwyć pola logowania dla aplikacji

Aby ręcznie Przechwyć pola logowania, najpierw musisz mieć zainstalowane rozszerzenie przeglądarki panelu dostępu i **nie jest uruchomiona w trybie inPrivate, incognito lub prywatnych.** Aby zainstalować rozszerzenia przeglądarki, wykonaj kroki opisane w [jak zainstalować rozszerzenie przeglądarki panelu dostępu](#i-cannot-manually-detect-sign-in-fields-for-my-application) sekcji.

Aby skonfigurować **opartego na hasłach logowania jednokrotnego** dla aplikacji za pomocą **przechwytywania ręczne pól logowania**, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Wybierz tryb **opartego na hasłach logowania jednokrotnego.**

9. Wprowadź **adres URL logowania**, adres URL, gdzie użytkownicy wprowadzają swoją nazwę i hasło do logowania. **Upewnij się pola logowania są widoczne w adresie URL, możesz podać**.

10. Kliknij przycisk **Zapisz**.

11. Po wykonaniu, adres URL jest automatycznie pozyskany o nazwę użytkownika i hasło pole wprowadzania i pozwalają na korzystanie z usługi Azure AD na bezpieczne przesyłanie haseł do tej aplikacji przy użyciu rozszerzenia przeglądarki panelu dostępu. W przypadku awarii, możesz **Zmień tryb logowania, aby ręcznie pól logowania przechwyceniem** , przechodząc do kroku 12.

12. Kliknij przycisk **Konfiguruj &lt;appname&gt; jednego hasła ustawień logowania jednokrotnego**.

13. Wybierz **ręcznie Wykryj pola logowania** opcji konfiguracji.

14. Kliknij przycisk **OK**.

15. Kliknij pozycję **Zapisz**.

16. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie za pomocą panelu dostępu.

## <a name="i-see-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Czy mogę zobaczyć błąd "Nie znaleziono żadnych pól logowania pod tym adresem URL"

Zostanie wyświetlony ten błąd, gdy automatyczne wykrywanie pól logowania nie powiodło się. Aby rozwiązać ten problem, spróbuj ręcznego pól logowania wykrywania, wykonując kroki opisane w [jak ręcznie Przechwyć pola logowania dla aplikacji](#how-to-manually-capture-sign-in-fields-for-an-application) sekcji.

## <a name="i-see-an-unable-to-save-single-sign-on-configuration-error"></a>Czy mogę zobaczyć "Nie można zapisać konfiguracji logowania jednokrotnego" Błąd

W niektórych rzadkich przypadkach aktualizacja jednej konfiguracji logowania jednokrotnego może zakończyć się niepowodzeniem. Aby rozwiązać problem, spróbuj Zapisywanie pojedynczego logowania jednokrotnego ponownie konfigurację.

Jeśli nadal nie będzie działać spójnie, otwórz zgłoszenie do pomocy technicznej i podaj informacje zebrane w [sposobu wyświetlania szczegółów powiadomienia portalu](#i-cannot-manually-detect-sign-in-fields-for-my-application) i [jak uzyskać pomoc, wysyłając szczegóły powiadomienia do pomocy technicznej inżynier](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) sekcje.

## <a name="i-cannot-manually-detect-sign-in-fields-for-my-application"></a>Pola logowania nie można ręcznie Wykryj dla mojej aplikacji

Zachowania, które można napotkać podczas ręcznego wykrywania nie działa, należą:

-   Proces przechwytywania ręczne pojawiły się do pracy, ale pola przechwytywane nie była prawidłowa

-   Nie uzyskać wyróżnione pola po prawej stronie, podczas wykonywania procesu przechwytywania

-   Proces przechwytywania powoduje przejście do strony logowania aplikacji zgodnie z oczekiwaniami, ale nic się nie dzieje

-   Ręczne Przechwytywanie wydaje się działać, ale gdy Moi użytkownicy przejdą do aplikacji przy użyciu panelu dostępu nie jest realizowane logowania jednokrotnego.

Jeśli wystąpi dowolne z tych problemów sprawdź następujące kwestie:

-   Upewnij się, że masz najnowszą wersję rozszerzenie przeglądarki panelu dostępu **zainstalowane** i **włączone** wykonując kroki opisane w [jak zainstalować rozszerzenie przeglądarki panelu dostępu](#how-to-install-the-access-panel-browser-extension) sekcji.

-   Upewnij się, że nie ma proces przechwytywania podczas do przeglądarki, **trybu incognito, przeglądanie inPrivate lub prywatnych**. Rozszerzenie panelu dostępu nie jest obsługiwane w tych trybach.

-   Upewnij się, że użytkownicy nie próbuje zalogować się do aplikacji z poziomu panelu dostępu podczas w **trybu incognito, przeglądanie inPrivate lub prywatnych**. Rozszerzenie panelu dostępu nie jest obsługiwane w tych trybach.

-   Spróbuj ręcznego procesu przechwytywania ponownie, zapewnienie, że czerwone znaczniki są za pomocą odpowiednich pól.

-   Czy Proces przechwytywania ręczne wydaje się, że przestanie odpowiadać, strony logowania nie robi nic (przypadek 3 powyżej), ponownie proces przechwytywania ręczne. Ale tym razem po ukończeniu procesu, naciśnij klawisz **F12** przycisk, aby otworzyć konsolę dla deweloperów w przeglądarce. Gdy istnieje, otwórz **konsoli** i typ **window.location= "&lt;wprowadź url logowania zostało określone podczas konfigurowania aplikacji&gt;"** , a następnie naciśnij klawisz **Enter** . Wymusza przekierowanie strony, która kończy proces przechwytywania i przechowuje pola, które zostały przechwycone.

Jeśli żadna z tych metod działa, mogą pomóc pomocy technicznej. Otwórz zgłoszenie do pomocy technicznej ze szczegółowymi informacjami o czym chcesz wypróbować, a także informacje zebrane w [sposobu wyświetlania szczegółów powiadomienia portalu](#i-cannot-manually-detect-sign-in-fields-for-my-application) i [jak uzyskać pomoc, wysyłając powiadomienia, szczegółowe informacje ze specjalistą pomocy technicznej ](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) sekcje (jeśli dotyczy).

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak zainstalować rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj następujące czynności:

1.  Otwórz [panelu dostępu](https://myapps.microsoft.com) w jednym z obsługiwanych przeglądarek i zaloguj się jako **użytkownika** w usłudze Azure AD.

2.  Kliknij przycisk **logowania jednokrotnego hasła aplikacji** w panelu dostępu.

3.  W oknie komunikatu z pytaniem zainstalować oprogramowanie, wybierz **Zainstaluj teraz**.

4.  W zależności od przeglądarki być przekierowywany link pobierania. **Dodaj** rozszerzenie do przeglądarki.

5.  Jeśli przeglądarka zapyta, wybrać opcję **Włącz** lub **Zezwalaj** rozszerzenia.

6.  Po zainstalowaniu **ponowne uruchomienie** sesji przeglądarki.

7.  Zaloguj się do panelu dostępu i zobacz, jeśli możesz to zrobić **Uruchom** logowania jednokrotnego hasła aplikacji.

Może również Pobierz rozszerzenie dla programu Chrome i Firefox z bezpośrednie linki poniżej:

-   [Rozszerzenie panelu dostępu dla programu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu do przeglądarki Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Jak wyświetlić szczegóły powiadomieniu portalu

Możesz zobaczyć szczegółowe informacje o wszystkich powiadomień portalu, wykonując poniższe kroki:

1. Kliknij przycisk **powiadomienia** ikonę (dzwonek) w prawym górnym rogu witryny Azure portal

2. Wybierz powiadomienia w **błąd** stanu (te z czerwonym (!) obok nich).

   >! PAMIĘTAJ], że nie można kliknąć powiadomienia w **pomyślnie** lub **w toku** stanu.
   >
   >

3. **Szczegóły powiadomienia** zostanie otwarte okienko.

4. Skorzystaj z informacji, aby poznać więcej szczegółów o problemie.

5. Jeśli nadal potrzebujesz pomocy, możesz również udostępniać informacje inżynier pomocy technicznej lub grupa produktu, aby uzyskać pomoc dotyczącą danego problemu.

6. Kliknij przycisk **kopiowania** **ikonę** po prawej stronie **błąd kopiowania** skopiuj wszystkie szczegóły powiadomienia na udostępnianie pomocy technicznej lub produktu inżynier grupy w polu tekstowym.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Jak uzyskać pomoc, wysyłając powiadomienia, szczegółowe informacje ze specjalistą pomocy technicznej

Bardzo ważne jest, że udostępniasz **poniższymi szczegółami** z inżynierem pomocy technicznej, jeśli potrzebujesz pomocy, dzięki czemu mogą one pomóc szybko. Możesz **wykonać zrzut ekranu,** lub kliknij przycisk **ikona błędu kopiowania**, liczba znalezionych na prawo od **błąd kopiowania** pola tekstowego.

## <a name="notification-details-explained"></a>Szczegóły powiadomienia wyjaśniono

Poniżej opisano bardziej każdej powiadomienia oznacza, że elementy oraz zawiera przykłady każdego z nich.

### <a name="essential-notification-items"></a>Essential Notification Items

-   **Tytuł** — opisowy tytuł powiadomienia

    -   Przykład — **ustawienia serwera proxy aplikacji**

-   **Opis** — opisu co nastąpiło w wyniku operacji

    -   Przykład — **wprowadzony wewnętrzny adres url jest już używana przez inną aplikację**

-   **Identyfikator powiadomienia** — Unikatowy identyfikator powiadomienia

    -   Przykład — **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **Identyfikator żądania klienta** — identyfikator określonego żądania przez przeglądarkę

    -   Przykład — **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Czas UTC sygnatury** — sygnatura czasowa, w którym wystąpił powiadomienia, w formacie UTC

    -   Przykład — **2017-03-23T19:50:43.7583681Z**

-   **Wewnętrzny identyfikator transakcji** — wewnętrzny identyfikator używany do wyszukiwania błędów w naszych systemach

    -   Przykład — **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **Nazwa UPN** — użytkownik, który wykonał operację

    -   Przykład — **tperkins\@f128.info**

-   **Identyfikator dzierżawy** — Unikatowy identyfikator dzierżawy, który był użytkownika, który wykonał operację

    -   Przykład — **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **Identyfikator obiektu użytkownika** — Unikatowy identyfikator użytkownika, który wykonał operację

    -   Przykład — **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Elementy szczegółowe powiadomienia

-   **Nazwa wyświetlana** — **(może być pusta)** bardziej szczegółowe nazwy wyświetlane dla błędu

    -   Przykład — **ustawienia serwera proxy aplikacji**

-   **Stan** — określonych stan powiadomienia

    -   Przykład — **nie powiodło się**

-   **Obiekt o identyfikatorze** — **(może być pusta)** identyfikator obiektu, dla której wykonano operację

    -   Example – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Szczegóły** — szczegółowy opis co nastąpiło w wyniku operacji

    -   Przykład — **wewnętrznego adresu url "<https://bing.com/>" jest nieprawidłowa, ponieważ jest już używana**

-   **Błąd kopiowania** — kliknij przycisk **ikonę kopiowania** po prawej stronie **błąd kopiowania** skopiuj wszystkie szczegóły powiadomienia na udostępnianie pomocy technicznej lub produktu inżynier grupy w polu tekstowym

    -   Przykład — ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Kolejne kroki
[Udostępnij logowanie jednokrotne do aplikacji serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)

