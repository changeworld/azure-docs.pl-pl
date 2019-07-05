---
title: Problemy z konfigurowaniem hasło logowania jednokrotnego dla aplikacji spoza galerii | Dokumentacja firmy Microsoft
description: Typowe problemy występujące podczas konfigurowania hasła logowania jednokrotnego (SSO) dotyczących niestandardowych aplikacji, które nie znajdują się w galerii aplikacji usługi Azure AD.
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
ms.openlocfilehash: 24330dc874173ba1c6f15abb7b4caf9f23e2e00c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440353"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problemy z konfigurowaniem hasło logowania jednokrotnego dla aplikacji spoza galerii

W tym artykule opisano typowe problemy, które mogą wystąpić podczas konfigurowania *hasło logowania jednokrotnego* (SSO) dla aplikacji spoza galerii.

## <a name="capture-sign-in-fields-for-an-app"></a>Przechwyć pola logowania dla aplikacji

Przechwyć pola logowania jest obsługiwany tylko w przypadku komputerów z obsługą HTML strony logowania. To ma nieobsługiwany dla niestandardowej strony logowania, takich jak te używające programu Adobe Flash lub inne technologie włączone HTML.

Istnieją dwa sposoby Przechwyć pola logowania w aplikacjach niestandardowych:

- **Automatyczne pól logowania przechwytywania** dobrze działa z większości włączone HTML strony logowania, *jeśli używają one dobrze znane identyfikatory DIV* dla pola nazwy i hasła użytkownika. HTML na stronie jest pozyskany znaleźć identyfikatorów DIV, spełniających określone kryteria. Metadane są zapisywane, aby go można odtworzyć aplikacji później.

- **Ręczne pól logowania przechwytywania** jest używany, gdy dostawcy aplikacji *nie etykiety logowania pól wejściowych*. Ręczne Przechwytywanie jest też używana, jeśli dostawca *renderuje wielu pól, które nie mogą być wykrywane automatycznie*. Azure Active Directory (Azure AD) może przechowywać dane na dowolną liczbę pól istnieją na stronie logowania, jeśli użytkownik stwierdzenie, gdzie te pola znajdują się na stronie.

Ogólnie rzecz biorąc Jeśli przechwytywanie pól automatycznego logowania nie działa, spróbuj użyć opcji ręcznej.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Automatycznie Przechwyć pola logowania dla aplikacji

Aby skonfigurować opartego na hasłach logowania jednokrotnego przy użyciu przechwytywania pól automatycznego logowania, wykonaj następujące kroki:

1. Otwórz [portal Azure](https://portal.azure.com/). Zaloguj się jako administrator globalny lub współadministratora.

2. W okienku nawigacji po lewej stronie wybierz **wszystkich usług** do otwierania rozszerzenia usługi Azure AD.

3. Typ **usługi Azure Active Directory** w filtru pole wyszukiwania, a następnie wybierz **usługi Azure Active Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **wszystkie aplikacje** Aby wyświetlić listę aplikacji.

   > [!NOTE]
   > Jeśli nie widzisz aplikacji, które mają używać **filtru** formant w górnej części **wszystkie aplikacje** listy. Ustaw **Pokaż** opcję "Wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować na potrzeby logowania jednokrotnego.

7. Po załadowaniu aplikacji wybierz **logowanie jednokrotne** w okienku nawigacji po lewej stronie.

8. Wybierz **opartego na hasłach logowania jednokrotnego** trybu.

9. Wprowadź **adres URL logowania**, czyli adres URL strony, gdzie użytkownicy wprowadzają swoje nazwy użytkownika i hasła do logowania. *Upewnij się, że pola logowania są widoczne na stronie dla adresu URL, który możesz udostępnić*.

10. Wybierz pozycję **Zapisz**.

    Strona jest automatycznie pozyskany dla użytkownika nazwy i hasła pól wejściowych. Można teraz używać usługi Azure AD na bezpieczne przesyłanie haseł do tej aplikacji przy użyciu rozszerzenia przeglądarki panelu dostępu.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Ręcznie Przechwyć pola logowania dla aplikacji

Aby ręcznie Przechwyć pola logowania, musisz mieć zainstalowane rozszerzenie przeglądarki panelu dostępu. Ponadto przeglądarka nie może być uruchomiona *inPrivate*, *incognito*, lub *prywatnej* trybu.

Aby zainstalować rozszerzenie, zobacz [zainstalować rozszerzenie przeglądarki panelu dostępu](#install-the-access-panel-browser-extension) dalszej części tego artykułu.

Aby skonfigurować opartego na hasłach logowania jednokrotnego dla aplikacji przy użyciu przechwytywania ręczne pól logowania, wykonaj następujące kroki:

1. Otwórz [portal Azure](https://portal.azure.com/). Zaloguj się jako administrator globalny lub współadministratora.

2. W okienku nawigacji po lewej stronie wybierz **wszystkich usług** do otwierania rozszerzenia usługi Azure AD.

3. Typ **usługi Azure Active Directory** w filtru pole wyszukiwania, a następnie wybierz **usługi Azure Active Directory**.

4. Wybierz **aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.

5. Wybierz **wszystkie aplikacje** Aby wyświetlić listę aplikacji.

   > [!NOTE] 
   > Jeśli nie widzisz aplikacji, które mają używać **filtru** formant w górnej części **wszystkie aplikacje** listy. Ustaw **Pokaż** opcję "Wszystkie aplikacje".

6. Wybierz aplikację, którą chcesz skonfigurować na potrzeby logowania jednokrotnego.

7. Po załadowaniu aplikacji wybierz **logowanie jednokrotne** w okienku nawigacji po lewej stronie.

8. Wybierz **opartego na hasłach logowania jednokrotnego** trybu.

9. Wprowadź **adres URL logowania**, czyli strony, gdzie użytkownicy wprowadzają swoje nazwy użytkownika i hasła do logowania. *Upewnij się, że pola logowania są widoczne na stronie dla adresu URL, który możesz udostępnić*.

10. Wybierz **Konfiguruj *&lt;appname&gt;* jednego hasła ustawień logowania jednokrotnego**.

11. Wybierz **ręcznie Wykryj pola logowania**.

14. Wybierz przycisk **OK**.

15. Wybierz pozycję **Zapisz**.

16. Postępuj zgodnie z instrukcjami, aby za pomocą panelu dostępu.

## <a name="troubleshoot-problems"></a>Rozwiązywanie problemów

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Otrzymuję błąd "Nie znaleziono żadnych pól logowania pod tym adresem URL"

Otrzymujesz ten komunikat o błędzie, gdy automatyczne wykrywanie pól logowania nie powiodło się. Aby rozwiązać ten problem, spróbuj ręcznego pól logowania wykrywania. Zobacz [ręcznie Przechwyć pola logowania dla aplikacji](#manually-capture-sign-in-fields-for-an-app) dalszej części tego artykułu.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Czy mogę uzyskać "Nie można zapisać konfiguracji rejestracji jednokrotnej" Błąd

Rzadko Trwa aktualizowanie konfiguracji logowania jednokrotnego nie powiedzie się. Aby rozwiązać ten problem, spróbuj zapisać ponownie konfigurację.

Jeśli nadal jest wyświetlany błąd, należy otworzyć zgłoszenie do pomocy technicznej. Zawierają informacje, które jest opisane w [szczegółowe powiadomienia portalu](#view-portal-notification-details) i [wysłać szczegóły powiadomienia z inżynierem pomocy technicznej, aby uzyskać Pomoc](#send-notification-details-to-a-support-engineer-to-get-help) sekcjach tego artykułu.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Pola logowania nie można ręcznie Wykryj dla mojej aplikacji

Podczas ręcznego wykrywania nie działa, może obserwować następujące zachowania:

- Proces przechwytywania ręczne pojawiły się do pracy, ale przechwyconych pola nie są poprawne.

- Nie uzyskać wyróżnione odpowiednich pól, podczas uruchamiania procesu przechwytywania.

- Proces przechwytywania spowoduje przejście do strony logowania w aplikacji zgodnie z oczekiwaniami, ale nic się nie dzieje.

- Ręczne Przechwytywanie wydaje się działać, ale logowania jednokrotnego nie się zdarzyć, gdy użytkownicy przejdą do aplikacji z poziomu panelu dostępu.

Jeśli wystąpi dowolne z tych problemów, wykonaj następujące czynności:

- Upewnij się, że masz najnowszą wersję rozszerzenie przeglądarki panelu dostępu *zainstalowane i włączone*. Zobacz [zainstalować rozszerzenie przeglądarki panelu dostępu](#install-the-access-panel-browser-extension) dalszej części tego artykułu.

- Upewnij się, że przeglądarka nie znajduje się w *incognito*, *inPrivate*, lub *prywatnej* trybu w procesie przechwytywania. Rozszerzenie panelu dostępu nie jest obsługiwane w tych trybach.

- Upewnij się, że użytkownicy nie są próby Zaloguj się do aplikacji za pomocą panelu dostępu podczas w *incognito*, *inPrivate*, lub *trybie prywatnym*.

- Spróbuj ponownie proces przechwytywania ręczne. Upewnij się, że czerwone znaczniki są w polach prawidłowy.

- Jeśli proces przechwytywania ręczne wydaje się, że przestanie odpowiadać lub przestaje odpowiadać strony logowania, spróbuj ponownie proces przechwytywania ręczne. Jednak tym razem po ukończeniu procesu, naciśnij klawisz F12, aby otworzyć konsolę dla deweloperów w przeglądarce. Wybierz **konsoli** kartę. Typ **window.location= " *&lt;logowania adres URL, który zostały określone podczas konfigurowania aplikacji&gt;* "** , a następnie naciśnij klawisz Enter. Wymusza przekierowanie strony, która kończy proces przechwytywania i przechowuje pola, które zostały przechwycone.

### <a name="contact-support"></a>Skontaktuj się z pomocą techniczną

Jeśli problemy wciąż występują, otwórz przypadek, z Microsoft Support. Opisz, co próbujesz zrobić. Zawierają szczegółowe informacje, które są opisane w [szczegółowe powiadomienia portalu](#view-portal-notification-details) i [wysłać szczegóły powiadomienia z inżynierem pomocy technicznej, aby uzyskać Pomoc](#send-notification-details-to-a-support-engineer-to-get-help) sekcjach tego artykułu (jeśli dotyczy).

## <a name="install-the-access-panel-browser-extension"></a>Zainstaluj rozszerzenie przeglądarki panelu dostępu

Wykonaj następujące kroki:

1. Otwórz [panelu dostępu](https://myapps.microsoft.com) w obsługiwanej przeglądarki. Zaloguj się do usługi Azure AD jako *użytkownika*.

2. Wybierz **logowania jednokrotnego hasła aplikacji** panelu dostępu.

3. Po wyświetleniu monitu, aby zainstalować oprogramowanie, wybierz **Zainstaluj teraz**.

4. Nastąpi przekierowanie do strony pobierania w przeglądarce. Możliwość **Dodaj** rozszerzenia.

5. Po wyświetleniu monitu wybierz **Włącz** lub **Zezwalaj**.

6. Po zakończeniu instalacji uruchom ponownie przeglądarkę.

7. Zaloguj się do panelu dostępu. Zobacz, można otworzyć hasło logowania jednokrotnego z włączonym aplikacji.

Można również bezpośrednio pobrać rozszerzenia przeglądarki Chrome i Firefox za pomocą poniższych linków:

-   [Rozszerzenie panelu dostępu dla programu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu w przeglądarce Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Wyświetl szczegóły powiadomienia w portalu

Aby wyświetlić szczegółowe informacje o wszystkich powiadomień portalu, wykonaj następujące kroki:

1. Wybierz **powiadomienia** ikonę (dzwonek) w prawym górnym rogu witryny Azure portal.

2. Wybierz wszelkie powiadomienia, który pokazuje *błąd* stanu. (Mają czerwony znak "!".)

   > [!NOTE]
   > Nie można wybrać powiadomienia, które znajdują się w *pomyślnie* lub *w toku* stanu.

3. **Szczegóły powiadomienia** zostanie otwarte okienko. Przeczytaj informacje, aby dowiedzieć się więcej o problemie.

5. Jeśli nadal potrzebujesz pomocy, udostępnianie informacji za pomocą inżynier pomocy technicznej lub grupa produktów. Wybierz **kopiowania** ikony po prawej stronie **błąd kopiowania** pole, aby skopiować szczegóły powiadomienia na udostępnianie.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Wyślij szczegóły powiadomienia ze specjalistą pomocy technicznej w celu uzyskania pomocy

Jest ważne, aby udostępniać *wszystkich* szczegółowe informacje, które są wymienione w tej sekcji z obsługą, dzięki czemu mogą one pomóc szybko. Aby zarejestrować go, możesz wykonać zrzut ekranu lub wybrać **błąd kopiowania**.

Następujące informacje wyjaśnia, co każdy element powiadomienia oznacza oraz przykłady.

### <a name="essential-notification-items"></a>Elementy podstawowe powiadomienie

- **Tytuł**: opisowy tytuł powiadomienia.

   Przykład: *Ustawienia serwera proxy aplikacji*

- **Opis**: co nastąpiło w wyniku operacji.

   Przykład: *Wprowadzony wewnętrzny adres URL jest już używana przez inną aplikację.*

- **Identyfikator powiadomienia**: Unikatowy identyfikator powiadomienia.

    Przykład: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **Identyfikator żądania klienta**: zgłaszający w przeglądarce identyfikator określonego żądania.

    Przykład: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Czas UTC sygnatury**: sygnatura czasowa powiadomienia wystąpienia, w formacie UTC.

    Przykład: *2017-03-23T19:50:43.7583681Z*

- **Wewnętrzny identyfikator transakcji**: wewnętrzny identyfikator, który służy do wyszukiwania błędów w naszych systemach.

    Przykład: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: Użytkownik, który uruchomił operację.

    Przykład: *tperkins\@f128.info*

- **Identyfikator dzierżawy**: Unikatowy identyfikator dzierżawy, której członkiem jest użytkownik, który uruchomił operację.

    Przykład: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Identyfikator obiektu użytkownika**: Unikatowy identyfikator użytkownika, który uruchomił operację.

    Przykład: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Elementy szczegółowe powiadomienia

- **Nazwa wyświetlana**: (może być pusta) Nazwa wyświetlana bardziej szczegółowy błąd.

    Przykład: *Ustawienia serwera proxy aplikacji*

- **Stan**: stan określonego powiadomienia.

    Przykład: *Niepowodzenie*

- **Obiekt o identyfikatorze**: (może być pusta) identyfikator obiektu, względem którego uruchomiono operację.

   Przykład: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Szczegóły**: szczegółowego opisu co nastąpiło w wyniku operacji.

    Przykład: *Wewnętrzny adres url "<https://bing.com/>" jest nieprawidłowa, ponieważ jest już używana.*

- **Błąd kopiowania**: Umożliwia wybranie **ikonę kopiowania** po prawej stronie **błąd kopiowania** pole tekstowe, aby skopiować szczegóły powiadomienia, aby pomóc z pomocą techniczną.

    Przykład:   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Kolejne kroki
[Udostępnij logowanie jednokrotne do aplikacji serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
