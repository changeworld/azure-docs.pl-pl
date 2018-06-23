---
title: Problem podczas konfiguracji hasła logowanie jednokrotne dla aplikacji z systemem innym niż galerii | Dokumentacja firmy Microsoft
description: Zrozumienie kroju osób typowe problemy podczas konfigurowania haseł logowanie jednokrotne dla aplikacji z systemem innym niż galerii niestandardowych, które nie są wymienione w galerii aplikacji usługi Azure AD
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
ms.openlocfilehash: 69a2bf6e622cd2338d57e62ed82951cb2385edd6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334911"
---
# <a name="problem-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problem podczas konfiguracji hasła logowanie jednokrotne dla aplikacji z systemem innym niż galerii

Ten artykuł ułatwia zrozumienie kroju osób typowe problemy podczas konfigurowania **hasło logowania jednokrotnego** z aplikacji innych niż galerii.

## <a name="how-to-capture-sign-in-fields-for-an-application"></a>Jak przechwycić pola logowania dla aplikacji

Przechwytywanie pola logowania jest obsługiwana tylko dla komputerów z obsługą HTML strony logowania i jest **nie jest obsługiwane dla niestandardowych stron logowania**, takich jak implementacje używające Flash i inne technologie włączone HTML.

Istnieją dwa sposoby, można przechwytywać logowania pól niestandardowych aplikacji:

-   Automatyczne logowanie pola przechwytywania

-   Ręczne znaku w polu przechwytywania

**Automatyczne logowanie pola przechwytywania** działa prawidłowo w przypadku większości włączone HTML strony logowania, jeśli używają one **dobrze znane identyfikatory DIV o nazwę użytkownika i hasło, wprowadź** pola. To działania jest oskrobaniu HTML na stronie, aby znaleźć identyfikatory DIV, spełniających określone kryteria, a następnie zapisywania tych metadanych dla tej aplikacji do powtarzania hasła do niego później.

**Ręczne znaku w polu przechwytywania** mogą być używane w przypadku który aplikacji **dostawcy nie etykietę** pole wejściowe, używane do logowania. Ręczne znaku w polu przechwytywania można również w przypadku gdy **dostawcy renderuje wielu pól** który nie może być wykrywane automatycznie. Usługi Azure AD mogą przechowywać dane na dowolną liczbę pól na stronie logowania, tak długo, jak możesz Napisz, których te pola są na stronie.

Ogólnie rzecz biorąc **Jeśli pole automatycznego logowania przechwytywania nie działa, spróbuj opcja ręcznego odbierania.**

### <a name="how-to-automatically-capture-sign-in-fields-for-an-application"></a>Automatycznie Przechwytywanie pola logowania dla aplikacji

Aby skonfigurować **opartego na hasłach rejestracji jednokrotnej** dla aplikacji przy użyciu **przechwytywania automatycznego logowania pola**, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, aby skonfigurować logowanie jednokrotne.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Wybierz tryb **opartego na hasłach logowania jednokrotnego.**

9.  Wprowadź **adres URL logowania**, adres URL, których użytkownicy wprowadzić swoją nazwę i hasło do logowania. **Upewnij się, pola logowania są widoczne w adresie URL, musisz podać**.

10. Kliknij przycisk **Zapisz**.

11. Raz można to zrobić, że adres URL jest automatycznie oskrobane dla nazwy użytkownika i hasła pole wprowadzania i umożliwiają używanie usługi Azure AD do bezpiecznego przesyłania hasła do tej aplikacji przy użyciu rozszerzenia przeglądarki panelu dostępu.

## <a name="how-to-manually-capture-sign-in-fields-for-an-application"></a>Ręcznie Przechwytywanie pola logowania dla aplikacji

Ręcznie przechwytywania pola logowania, najpierw musi mieć zainstalowane rozszerzenia przeglądarki panelu dostępu i **nie jest uruchomiona w trybie inPrivate, incognito lub prywatnej.** Aby zainstalować rozszerzenie przeglądarki, postępuj zgodnie z instrukcjami [jak zainstalować rozszerzenie przeglądarki panelu dostępu](#i-cannot-manually-detect-sign-in-fields-for-my-application) sekcji.

Aby skonfigurować **opartego na hasłach rejestracji jednokrotnej** dla aplikacji przy użyciu **przechwytywania ręczne znaku w polu**, wykonaj następujące czynności:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **ko-administratora.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikację, aby skonfigurować logowanie jednokrotne.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Wybierz tryb **opartego na hasłach logowania jednokrotnego.**

9.  Wprowadź **adres URL logowania**, adres URL, których użytkownicy wprowadzić swoją nazwę i hasło do logowania. **Upewnij się, pola logowania są widoczne w adresie URL, musisz podać**.

10. Kliknij przycisk **Zapisz**.

11. Raz można to zrobić, że adres URL jest automatycznie oskrobane dla nazwy użytkownika i hasła pole wprowadzania i umożliwiają używanie usługi Azure AD do bezpiecznego przesyłania hasła do tej aplikacji przy użyciu rozszerzenia przeglądarki panelu dostępu. W przypadku niepowodzenia, można **zmienić tryb logowania, aby używać przechwytywania ręczne znaku w polu** kontynuując krok 12.

12. Kliknij przycisk **Konfiguruj &lt;appname&gt; ustawienia jednego hasła logowania jednokrotnego**.

13. Wybierz **ręcznie wykryć pola logowania** opcji konfiguracji.

14. Kliknij przycisk **OK**.

15. Kliknij pozycję **Zapisz**.

16. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie do panelu dostępu.

## <a name="i-see-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Wyświetlany jest komunikat Błąd "Nie można odnaleźć żadnych pól logowania pod tym adresem URL"

Zostanie wyświetlony ten błąd, gdy automatyczne wykrywanie pól logowanie nie powiodło się. Aby rozwiązać ten problem, spróbuj wykrywania ręczne pola logowania, wykonując kroki opisane w [ręcznie Przechwytywanie pola logowania dla aplikacji](#how-to-manually-capture-sign-in-fields-for-an-application) sekcji.

## <a name="i-see-an-unable-to-save-single-sign-on-configuration-error"></a>Widać "Nie można zapisać konfiguracji logowania jednokrotnego" Błąd

W niektórych rzadkich przypadkach aktualizacja jednej konfiguracji logowania jednokrotnego może zakończyć się niepowodzeniem. Aby rozwiązać, spróbuj zapisać pojedynczego logowania jednokrotnego ponownie konfigurację.

Jeśli nadal stale zwracają błąd, otwieranie sprawy pomocy technicznej i podaj informacje zebrane w [sposób wyświetlić szczegóły powiadomieniu portalu](#i-cannot-manually-detect-sign-in-fields-for-my-application) i [jak uzyskać pomoc, wysyłając szczegóły powiadomienia do pomocy technicznej inżynier](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) sekcje.

## <a name="i-cannot-manually-detect-sign-in-fields-for-my-application"></a>Pola logowania nie może ręcznie wykryć dla mojej aplikacji

Zachowania, które można napotkać podczas ręcznego wykrywania nie działa, należą:

-   Proces przechwytywania ręczne pojawił się do pracy, ale pola przechwytywane nie była prawidłowa

-   Pola prawo nie pobrać wyróżniony podczas wykonywania procesu przechwytywania

-   Przechwytywanie powoduje przejście do strony logowania aplikacji zgodnie z oczekiwaniami, ale nic się nie dzieje

-   Ręczne przechwytywania wydaje się działać, ale logowania jednokrotnego nie się zdarzyć, gdy Moi użytkownicy przejdź do aplikacji, z panelu dostępu.

Jeśli którekolwiek z tych problemów sprawdź następujące kwestie:

-   Upewnij się, że masz najnowszej wersji rozszerzenia przeglądarki panelu dostępu **zainstalowane** i **włączone** wykonując kroki opisane w [sposób instalowania rozszerzenia przeglądarki panelu dostępu](#how-to-install-the-access-panel-browser-extension) sekcji.

-   Upewnij się, że nie próbujesz proces przechwytywania podczas do przeglądarki, **incognito, przeglądanie inPrivate lub prywatnej tryb**. Rozszerzenie panelu dostępu nie jest obsługiwana w tych trybach.

-   Upewnij się, że użytkownicy nie próbuje zalogować się do aplikacji w panelu dostępu podczas w **incognito, przeglądanie inPrivate lub prywatnej tryb**. Rozszerzenie panelu dostępu nie jest obsługiwana w tych trybach.

-   Spróbuj ręcznie proces przechwytywania ponownie, zapewnienie, że czerwonych znaczników przekroczyli odpowiednich pól.

-   Proces przechwytywania ręczne wydaje się zawieszenie, czy strona logowania nie wszystko (przypadku 3 powyżej), spróbuj proces przechwytywania ręczne ponownie. Jednak to czas, po zakończeniu procesu, naciśnij klawisz **F12** przycisk, aby otworzyć konsoli dla deweloperów w przeglądarce. Raz, otwórz **konsoli** i typ **window.location= "&lt;wprowadź url logowania określony podczas konfigurowania aplikacji&gt;"** , a następnie naciśnij klawisz **Enter** . Dzięki temu przekierowania strony, które kończy proces przechwytywania i przechowuje pola, które została przechwycona.

Jeśli z tych metod nie działają dla Ciebie, może pomóc dział pomocy technicznej. Otwieranie sprawy pomocy technicznej Szczegóły co próbujesz, a także informacje zebrane w [sposób wyświetlić szczegóły powiadomieniu portalu](#i-cannot-manually-detect-sign-in-fields-for-my-application) i [jak uzyskać pomoc, wysyłając szczegóły powiadomienia do pracownika pomocy technicznej ](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) sekcje (jeśli dotyczy).

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak zainstalować rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj następujące czynności:

1.  Otwórz [panelu dostępu](https://myapps.microsoft.com) w jednym z obsługiwanych przeglądarkach i zaloguj się jako **użytkownika** w usługi Azure AD.

2.  Kliknij przycisk **logowania jednokrotnego hasła aplikacji** w panelu dostępu.

3.  W oknie komunikatu z pytaniem do zainstalowania oprogramowania, zaznaczyć **Zainstaluj teraz**.

4.  Oparte na przeglądarce być kierowane do łącza pobierania. **Dodaj** rozszerzenia do przeglądarki.

5.  Jeśli przeglądarka pytanie, wybierz albo **włączyć** lub **Zezwalaj** rozszerzenia.

6.  Wcześniej zainstalowano **ponowne uruchomienie** sesji przeglądarki.

7.  Zaloguj się do panelu dostępu i zobacz, czy można **uruchamianie** logowania jednokrotnego hasła aplikacji.

Może również pobrać rozszerzenia dla programu Chrome, a program Firefox z bezpośrednich łączy poniżej:

-   [Rozszerzenie panelu dostępu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Jak wyświetlić szczegóły powiadomieniu portalu

Szczegółowe informacje o każdym powiadomieniu portalu można wyświetlić, wykonując poniższe kroki:

1.  Kliknij przycisk **powiadomienia** ikonę (dzwonka) w prawym górnym rogu portalu Azure

2.  Wybierz wszystkich powiadomień w **błąd** stanu (te z czerwonym znakiem (!) obok nich).

  >! Należy zwrócić uwagę] możesz nie kliknij powiadomienia w **Powodzenie** lub **w toku** stanu.
  >
  >

3.  **Szczegóły powiadomienia** zostanie otwarte okienko.

4.  Skorzystaj z informacji, aby zrozumieć więcej szczegółów o problemie.

5.  Jeśli nadal potrzebujesz pomocy, mogą również współużytkować informacje z pracownikiem pomocy technicznej lub grupa produktów, aby uzyskać pomoc dotyczącą tego problemu.

6.  Kliknij przycisk **kopiowania** **ikona** z prawej strony **błąd kopiowania** skopiuj wszystkie szczegóły powiadomienia na udostępnianie z pracownikiem pomocy technicznej lub produkt grupy w polu tekstowym.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Jak uzyskać pomoc, wysyłając szczegóły powiadomienia do pracownika pomocy technicznej

Jest bardzo ważne, aby udostępniać **poniższymi szczegółami** z pracownikiem pomocy technicznej, jeśli potrzebujesz pomocy, dzięki czemu mogą one ułatwić szybkie. Możesz **wykonać zrzut ekranu,** lub kliknij przycisk **ikony błędu kopiowania**, liczba znalezionych na prawo od **błąd kopiowania** pola tekstowego.

## <a name="notification-details-explained"></a>Szczegóły powiadomienia wyjaśniono

Poniżej przedstawiono więcej funkcji każdego, powiadomienia elementów oznacza i zawiera przykłady każdego z nich.

### <a name="essential-notification-items"></a>Elementy podstawowych powiadomień

-   **Tytuł** — opisowy tytuł powiadomienia

    -   Przykład — **ustawienia serwera proxy aplikacji**

-   **Opis elementu** — opis co nastąpiło w wyniku operacji

    -   Przykład — **wewnętrzny wprowadzony adres url jest już używana przez inną aplikację**

-   **Identyfikator powiadomień** — Unikatowy identyfikator powiadomienia.

    -   Przykład — **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **Identyfikator żądania klienta** — identyfikator określonego żądania przez przeglądarkę

    -   Przykład — **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Czas UTC sygnatury** — znacznik czasu, w którym wystąpił powiadomienia, w formacie UTC

    -   Przykład — **2017-03-23T19:50:43.7583681Z**

-   **Wewnętrzny identyfikator transakcji** — wewnętrzny identyfikator używaną do odszukania błąd w naszym systemie.

    -   Przykład — **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **Nazwa UPN** — użytkownik, który wykonał działanie

    -   Przykład — **tperkins@f128.info**

-   **Identyfikator dzierżawy** — Unikatowy identyfikator dzierżawy, który użytkownik, który wykonał działanie był członkiem

    -   Przykład — **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **Identyfikator obiektu użytkownika** — Unikatowy identyfikator użytkownika, który wykonał działanie

    -   Przykład — **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Elementy szczegółowe powiadomienia

-   **Nazwa wyświetlana** — **(może być pusta)** nazwę wyświetlaną bardziej szczegółowy kod błędu

    -   Przykład * — **ustawienia serwera proxy aplikacji**

-   **Stan** — stan określonego powiadomienia

    -   Przykład * — **nie powiodło się**

-   **Obiekt o identyfikatorze** — **(może być pusta)** Identyfikatora obiektu, względem którego wykonano operację

    -   Example – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Szczegóły** — szczegółowy opis co nastąpiło w wyniku operacji

    -   Przykład — **wewnętrznego adresu url "http://bing.com/" jest nieprawidłowy, ponieważ jest już używana**

-   **Błąd kopiowania** — kliknij przycisk **ikonę kopiowania** z prawej strony **błąd kopiowania** pole tekstowe, aby skopiować wszystkie szczegóły powiadomienia na udostępnianie z pracownikiem pomocy technicznej lub produkt grupy

    -   Przykład — ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Kolejne kroki
[Podaj logowanie jednokrotne do aplikacji przy użyciu serwera Proxy aplikacji](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

