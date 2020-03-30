---
title: Problemy z konfigurowaniem hasła logującego do osłaniania dla aplikacji innych niż galeria
description: Typowe problemy, które występują podczas konfigurowania hasła logowania jednokrotnego (Logowanie jednokrotne) dla aplikacji niestandardowych, które nie są w galerii aplikacji usługi Azure AD.
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
ms.openlocfilehash: ed8bafe7f5bc28cf37205107f8ab6dd5cdb4907c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274148"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problemy z konfigurowaniem logowania jednokrotnego hasła dla aplikacji spoza galerii

W tym artykule opisano typowe problemy, które mogą wystąpić podczas konfigurowania *logowania jednokrotnego hasła* (Logowanie jednokrotne) dla aplikacji spoza galerii.

## <a name="capture-sign-in-fields-for-an-app"></a>Przechwytywanie pól logowania dla aplikacji

Przechwytywanie pól logowania jest obsługiwane tylko dla stron logowania z włączoną funkcją HTML. Nie jest obsługiwany w przypadku niestandardowych stron logowania, takich jak te, które korzystają z programu Adobe Flash lub innych technologii innych niż HTML.

Istnieją dwa sposoby przechwytywania pól logowania dla aplikacji niestandardowych:

- **Automatyczne przechwytywanie pól logowania** działa dobrze z większością stron logowania z obsługą HTML, *jeśli używają dobrze znanych identyfikatorów DIV* dla pól nazwy użytkownika i hasła. Kod HTML na stronie jest zeskrobany w celu znalezienia identyfikatorów DIV spełniających określone kryteria. Te metadane są zapisywane, dzięki czemu można je odtworzyć w aplikacji później.

- **Ręczne przechwytywanie pól logowania** jest używane, jeśli dostawca aplikacji *nie oznaczy pól wejściowych logowania.* Przechwytywanie ręczne jest również używane, jeśli dostawca *renderuje wiele pól, których nie można automatycznie wykryć.* Usługa Azure Active Directory (Azure AD) może przechowywać dane dla tylu pól, ile znajduje się na stronie logowania, jeśli powiesz, gdzie te pola znajdują się na stronie.

Ogólnie rzecz biorąc, jeśli automatyczne przechwytywanie pól logowania nie działa, wypróbuj opcję ręczną.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Automatyczne przechwytywanie pól logowania dla aplikacji

Aby skonfigurować logowanie logowanie oparte na hasłach przy użyciu automatycznego przechwytywania pól logowania, wykonaj następujące kroki:

1. Otwórz witrynę [Azure Portal](https://portal.azure.com/). Zaloguj się jako administrator globalny lub współadministrator.

2. W okienku nawigacji po lewej stronie wybierz pozycję **Wszystkie usługi,** aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **usługę Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję Azure Active **Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz żądanej aplikacji, użyj kontrolki **Filtruj** u góry listy **Wszystkie aplikacje.** Ustaw opcję **Pokaż** na "Wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować dla aplikacji SSO.

7. Po załadowaniu aplikacji wybierz **opcję Logowanie jednokrotne** w okienku nawigacji po lewej stronie.

8. Wybierz **tryb logowania oparty na hasłach.**

9. Wprowadź **adres URL logowania**, który jest adresem URL strony, na której użytkownicy wejdą swoją nazwę użytkownika i hasło do logowania. *Upewnij się, że pola logowania są widoczne na stronie podanych adresów URL*.

10. Wybierz **pozycję Zapisz**.

    Strona jest automatycznie zeskrobana dla pól wprowadzania nazwy użytkownika i hasła. Teraz można użyć usługi Azure AD do bezpiecznego przesyłania haseł do tej aplikacji przy użyciu rozszerzenia przeglądarki Panelu dostępu.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Ręczne przechwytywanie pól logowania dla aplikacji

Aby ręcznie przechwycić pola logowania, musisz mieć zainstalowane rozszerzenie przeglądarki Panelu dostępu. Ponadto, przeglądarka nie może być uruchomiona *w trybie inPrivate*, *incognito*lub *prywatnym.*

Aby zainstalować rozszerzenie, zobacz [sekcję Instalowanie rozszerzenia przeglądarki panelu dostępu](#install-the-access-panel-browser-extension) w tym artykule.

Aby skonfigurować logowanie logowanie oparte na hasłach dla aplikacji przy użyciu ręcznego przechwytywania pól logowania, wykonaj następujące kroki:

1. Otwórz witrynę [Azure Portal](https://portal.azure.com/). Zaloguj się jako administrator globalny lub współadministrator.

2. W okienku nawigacji po lewej stronie wybierz pozycję **Wszystkie usługi,** aby otworzyć rozszerzenie usługi Azure AD.

3. Wpisz **usługę Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję Azure Active **Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę aplikacji.

   > [!NOTE] 
   > Jeśli nie widzisz żądanej aplikacji, użyj kontrolki **Filtruj** u góry listy **Wszystkie aplikacje.** Ustaw opcję **Pokaż** na "Wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować dla aplikacji SSO.

7. Po załadowaniu aplikacji wybierz **opcję Logowanie jednokrotne** w okienku nawigacji po lewej stronie.

8. Wybierz **tryb logowania oparty na hasłach.**

9. Wprowadź **adres URL logowania**, czyli stronę, na której użytkownicy wejdą swoją nazwę użytkownika i hasło do logowania. *Upewnij się, że pola logowania są widoczne na stronie podanych adresów URL*.

10. Wybierz **pozycję Konfiguruj * &lt;&gt; * ustawienia logowania jednokrotnego hasła hasła.**

11. Wybierz **pozycję Ręczne wykrywanie pól logowania**.

14. Wybierz **ok**.

15. Wybierz **pozycję Zapisz**.

16. Postępuj zgodnie z instrukcjami, aby korzystać z Panelu dostępu.

## <a name="troubleshoot-problems"></a>Rozwiązywanie problemów

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Pojawia się błąd "Nie możemy znaleźć żadnych pól logowania pod tym adresem URL"

Ten komunikat o błędzie pojawia się, gdy automatyczne wykrywanie pól logowania nie powiedzie się. Aby rozwiązać ten problem, spróbuj ręcznego wykrywania pól logowania. Zobacz [Ręcznie przechwytywać pola logowania dla](#manually-capture-sign-in-fields-for-an-app) sekcji aplikacji w tym artykule.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Pojawia się błąd "Nie można zapisać konfiguracji logowania jednokrotnego"

Rzadko aktualizowanie konfiguracji SSO kończy się niepowodzeniem. Aby rozwiązać ten problem, spróbuj ponownie zapisać konfigurację.

Jeśli nadal pojawia się błąd, otwórz przypadek pomocy technicznej. Dołącz informacje opisane w szczegóły [powiadomienia z witryny Wyświetlanie portalu](#view-portal-notification-details) i [wyślij szczegóły powiadomień do inżyniera pomocy technicznej, aby uzyskać sekcje pomocy](#send-notification-details-to-a-support-engineer-to-get-help) tego artykułu.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Nie mogę ręcznie wykryć pól logowania dla mojej aplikacji

Możesz zaobserwować następujące zachowania, gdy ręczne wykrywanie nie działa:

- Proces ręcznego przechwytywania wydawał się działać, ale przechwycone pola nie są poprawne.

- Poprawne pola nie są wyróżnione po uruchomieniu procesu przechwytywania.

- Proces przechwytywania przeniesie Cię do strony logowania aplikacji zgodnie z oczekiwaniami, ale nic się nie dzieje.

- Ręczne przechwytywanie wydaje się działać, ale sygowanie bez instrukcji sprzedaży nie odbywa się, gdy użytkownicy przechodzą do aplikacji z Panelu dostępu.

Jeśli wystąpi którykolwiek z tych problemów, wykonaj następujące czynności:

- Upewnij się, że masz *zainstalowaną i włączoną*najnowszą wersję rozszerzenia przeglądarki Panelu dostępu . Zobacz [sekcję Instalowanie rozszerzenia przeglądarki Panelu dostępu](#install-the-access-panel-browser-extension) w tym artykule.

- Upewnij się, że przeglądarka nie jest w *trybie incognito*, *inPrivate*lub *Tryb prywatny* podczas procesu przechwytywania. Rozszerzenie Panelu dostępu nie jest obsługiwane w tych trybach.

- Upewnij się, że użytkownicy nie próbują zalogować się do aplikacji z Panelu dostępu w *trybie* *incognito*, *inPrivate*lub Private.

- Spróbuj ponownie wykonać proces ręcznego przechwytywania. Upewnij się, że czerwone znaczniki znajdują się nad właściwymi polami.

- Jeśli proces ręcznego przechwytywania wydaje się przestać odpowiadać lub strona logowania nie odpowiada, spróbuj ponownie wykonać proces ręcznego przechwytywania. Ale tym razem, po zakończeniu procesu, naciśnij klawisz F12, aby otworzyć konsolę programisty przeglądarki. Wybierz kartę **konsoli.** Wpisz **window.location="*&lt;adres URL logowania określony podczas&gt;konfigurowania aplikacji*"**, a następnie naciśnij klawisz Enter. Wymusza to przekierowanie strony, które kończy proces przechwytywania i przechowuje pola, które zostały przechwycone.

### <a name="contact-support"></a>Kontakt z pomocą techniczną

Jeśli nadal występują problemy, otwórz sprawę z pomocą techniczną firmy Microsoft. Opisz, co próbowałeś. Dołącz szczegóły, które są opisane w [zobacz szczegóły powiadomień portalu](#view-portal-notification-details) i Wyślij szczegóły [powiadomień do inżyniera pomocy technicznej, aby uzyskać](#send-notification-details-to-a-support-engineer-to-get-help) sekcje pomocy tego artykułu (jeśli dotyczy).

## <a name="install-the-access-panel-browser-extension"></a>Instalowanie rozszerzenia przeglądarki Panelu dostępu

Wykonaj następujące kroki:

1. Otwórz [Panel dostępu](https://myapps.microsoft.com) w obsługiwanej przeglądarce. Zaloguj się do usługi Azure AD jako *użytkownik*.

2. Wybierz **aplikację logaj-logowawcze** w Panelu dostępu.

3. Po wyświetleniu monitu o zainstalowanie oprogramowania wybierz pozycję **Zainstaluj teraz**.

4. Zostaniesz przekierowany na stronę pobierania przeglądarki. Wybierz opcję **Dodaj** rozszerzenie.

5. Jeśli zostanie wyświetlony monit, wybierz pozycję **Włącz** lub **Zezwól**.

6. Po zakończeniu instalacji uruchom ponownie przeglądarkę.

7. Zaloguj się do Panelu dostępu. Sprawdź, czy możesz otworzyć aplikacje obsługujące funkcję logować hasło.

Możesz również bezpośrednio pobrać rozszerzenie przeglądarki dla Chrome i Firefox za pośrednictwem tych linków:

-   [Rozszerzenie panelu dostępu do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie Panelu dostępu do Firefoksa](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Wyświetlanie szczegółów powiadomień portalu

Aby wyświetlić szczegóły powiadomienia portalu, wykonaj następujące kroki:

1. Wybierz ikonę **Powiadomienia** (dzwonek) w prawym górnym rogu witryny Azure portal.

2. Wybierz dowolne powiadomienie z komunikatem *o błędzie.* (Mają czerwone "!".)

   > [!NOTE]
   > Nie można wybrać powiadomień, które są w stanie *Pomyślny* lub *W toku.*

3. Zostanie otwarte okienko **Szczegóły powiadomienia.** Przeczytaj informacje, aby dowiedzieć się o problemie.

5. Jeśli nadal potrzebujesz pomocy, udostępnij informacje inżynierowi pomocy technicznej lub grupie produktów. Wybierz ikonę **kopiowania** po prawej stronie pola **Błąd kopiowania,** aby skopiować szczegóły powiadomienia do udostępnienia.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Wysyłanie szczegółów powiadomień do inżyniera pomocy technicznej w celu uzyskania pomocy

Ważne jest, aby udostępnić *wszystkie* szczegóły wymienione w tej sekcji z obsługą, aby mogły ci szybko pomóc. Aby go nagrać, możesz zrobić zrzut ekranu lub wybrać **błąd kopiowania**.

Poniżej przedstawiono następujące informacje, co oznacza każdy element powiadomienia i podaje przykłady.

### <a name="essential-notification-items"></a>Podstawowe elementy powiadomień

- **Tytuł**: opisowy tytuł powiadomienia.

   Przykład: *Ustawienia serwera proxy aplikacji*

- **Opis**: co nastąpiło w wyniku operacji.

   Przykład: *wprowadzony wewnętrzny adres URL jest już używany przez inną aplikację.*

- **Identyfikator powiadomienia:** unikatowy identyfikator powiadomienia.

    Przykład: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **Identyfikator żądania klienta:** identyfikator żądania określonego, który został wykonany przez przeglądarkę.

    Przykład: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Time Stamp UTC**: sygnatura czasowa, kiedy wystąpiło powiadomienie, w czasie UTC.

    Przykład: *2017-03-23T19:50:43.7583681Z*

- **Identyfikator transakcji wewnętrznej:** wewnętrzny identyfikator używany do wyszukiwania błędów w naszych systemach.

    Przykład: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **Nazwa UPN:** Użytkownik, który uruchomił operację.

    Przykład: *tperkins\@f128.info*

- **Identyfikator dzierżawy:** unikatowy identyfikator dzierżawy, którego członkiem jest użytkownik, który uruchomił operację.

    Przykład: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Identyfikator obiektu użytkownika:** unikatowy identyfikator użytkownika, który uruchomił operację.

    Przykład: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Szczegółowe pozycje powiadomień

- **Nazwa wyświetlana:**(może być pusta) bardziej szczegółowa nazwa wyświetlana błędu.

    Przykład: *Ustawienia serwera proxy aplikacji*

- **Status:** określony stan powiadomienia.

    Przykład: *Nie powiodło się*

- **Identyfikator obiektu:**(może być pusty) identyfikator obiektu, przeciwko któremu została wprowadzona operacja.

   Przykład: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Szczegóły:** szczegółowy opis tego, co nastąpiło w wyniku operacji.

    Przykład: *Wewnętrzny<https://bing.com/>adres URL ' jest nieprawidłowy, ponieważ jest już używany.*

- **Błąd kopiowania**: Umożliwia wybranie **ikony kopiowania** po prawej stronie pola tekstowego **Błędu kopiowania,** aby skopiować szczegóły powiadomienia w celu uzyskania pomocy technicznej.

    Przykład:```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Następne kroki
[Zapewnianie logowania jednokrotnego do aplikacji za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
