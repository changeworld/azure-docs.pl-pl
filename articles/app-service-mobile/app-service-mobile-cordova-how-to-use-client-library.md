---
title: Jak korzystać z wtyczki Apache Cordova
description: Jak korzystać z wtyczki Apache Cordova dla aplikacji mobilnych platformy Azure
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: cafeea8afe571fc81548833952eee72a695fed41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459365"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Jak korzystać z biblioteki klienta Apache Cordova dla aplikacji mobilnych platformy Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Omówienie
W tym przewodniku dowiesz się, jak wykonywać typowe scenariusze przy użyciu najnowszej [wtyczki Apache Cordova dla aplikacji mobilnych platformy Azure.] Jeśli jesteś nowym użytkownikiem usługi Azure Mobile Apps, najpierw ukończ [usługę Azure Mobile Apps Quick Start,] aby utworzyć zaplecze, utworzyć tabelę i pobrać wstępnie utworzony projekt Apache Cordova. W tym przewodniku skupiamy się na wtyczce Apache Cordova po stronie klienta.

## <a name="supported-platforms"></a>Obsługiwane platformy
Ten zestaw SDK obsługuje apache Cordova w wersji 6.0.0 i nowszych na urządzeniach z systemami iOS, Android i Windows.  Obsługa platformy jest następująca:

* Android API 19-24 (KitKat przez Nugat).
* iOS w wersji 8.0 i nowszej.
* Windows Phone 8.1.
* Uniwersalna platforma Windows.

## <a name="setup-and-prerequisites"></a><a name="Setup"></a>Konfiguracja i wymagania wstępne
W tym przewodniku przyjęto założenie, że utworzono zaplecze z tabelą. W tym przewodniku przyjęto założenie, że tabela ma taki sam schemat jak tabele w tych samouczkach. W tym przewodniku przyjęto również założenie, że dodano wtyczkę Apache Cordova do kodu.  Jeśli nie zrobiłeś tego, możesz dodać wtyczkę Apache Cordova do swojego projektu w wierszu polecenia:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Aby uzyskać więcej informacji na temat tworzenia [pierwszej aplikacji Apache Cordova], zobacz ich dokumentację.

## <a name="setting-up-an-ionic-v2-app"></a><a name="ionic"></a>Konfigurowanie aplikacji Ionic v2

Aby poprawnie skonfigurować projekt Ionic v2, najpierw utwórz podstawową aplikację i dodaj wtyczkę Cordova:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Dodaj następujące wiersze, aby `app.component.ts` utworzyć obiekt klienta:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Teraz można skompilować i uruchomić projekt w przeglądarce:

```
ionic platform add browser
ionic run browser
```

Wtyczka Azure Mobile Apps Cordova obsługuje aplikacje Ionic w wersji 1 i 2.  Tylko aplikacje Ionic v2 wymagają `WindowsAzure` dodatkowej deklaracji dla obiektu.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="how-to-authenticate-users"></a><a name="auth"></a>Jak: Uwierzytelnij użytkowników
Usługa Azure App Service obsługuje uwierzytelnianie i autoryzowanie użytkowników aplikacji przy użyciu różnych zewnętrznych dostawców tożsamości: Facebook, Google, Microsoft Account i Twitter. Uprawnienia do tabel można ustawić w celu ograniczenia dostępu dla określonych operacji tylko do uwierzytelnionych użytkowników. Można również użyć tożsamości uwierzytelnionych użytkowników do zaimplementowania reguł autoryzacji w skryptach serwera. Aby uzyskać więcej informacji, zobacz [Samouczek Wprowadzenie do uwierzytelniania.]

Podczas korzystania z uwierzytelniania w aplikacji Apache Cordova muszą być dostępne następujące wtyczki Cordova:

* [cordova-plugin-urządzenie]
* [cordova-plugin-inappbrowser]

Obsługiwane są dwa przepływy uwierzytelniania: przepływ serwera i przepływ klienta.  Przepływ serwera zapewnia najprostsze środowisko uwierzytelniania, ponieważ opiera się na interfejsie uwierzytelniania sieci web dostawcy. Przepływ klienta umożliwia głębszą integrację z możliwości specyficznych dla urządzenia, takich jak logowanie jednokrotne, ponieważ opiera się na sdk specyficzne dla dostawcy specyficzne dla urządzenia.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="how-to-configure-your-mobile-app-service-for-external-redirect-urls"></a><a name="configure-external-redirect-urls"></a>Jak: Skonfiguruj usługę aplikacji mobilnej pod kątem zewnętrznych adresów URL przekierowań.
Kilka typów aplikacji Apache Cordova używać funkcji sprzężenia zwrotnego do obsługi przepływów interfejsu użytkownika OAuth.  Przepływy interfejsu użytkownika OAuth na localhost powodują problemy, ponieważ usługa uwierzytelniania wie tylko, jak korzystać z usługi domyślnie.  Przykłady problematycznych przepływów interfejsu użytkownika OAuth obejmują:

* Emulator Ripple.
* Live Reload z Ionic.
* Lokalne uruchamianie zaplecza mobilnego
* Uruchamianie zaplecza mobilnego w innej usłudze aplikacji platformy Azure niż usługa uwierzytelniania.

Postępuj zgodnie z poniższymi instrukcjami, aby dodać ustawienia lokalne do konfiguracji:

1. Zaloguj się do [portalu Azure]
2. Wybierz **pozycję Wszystkie zasoby** lub usługi **aplikacji,** a następnie kliknij nazwę aplikacji mobilnej.
3. Kliknij **pozycję Narzędzia**
4. Kliknij **polecenie Eksplorator zasobów** w menu OBSERWOWAĆ, a następnie kliknij polecenie **Przejdź**.  Zostanie otwarte nowe okno lub karta.
5. Rozwiń **węzeł config**, **authsettings** węzłów dla witryny w lewej nawigacji.
6. Kliknij **pozycję Edytuj**
7. Poszukaj elementu "allowedExternalRedirectUrls".  Może być ustawiona na wartość null lub tablicę wartości.  Zmień wartość na następującą wartość:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Zastąp adresy URL adresami URL usługi.  Przykłady `http://localhost:3000` obejmują (dla usługi przykładowej Node.js) lub `http://localhost:4400` (dla usługi Ripple).  Jednak te adresy URL są przykładami - twoja sytuacja, w tym dla usług wymienionych w przykładach, może być inna.
8. Kliknij przycisk **Odczyt/Zapis** w prawym górnym rogu ekranu.
9. Kliknij zielony przycisk **PUT.**

Ustawienia są zapisywane w tym momencie.  Nie zamykaj okna przeglądarki, dopóki ustawienia nie zostaną zakończone.
Dodaj również te adresy URL sprzężenia zwrotnego do ustawień CORS dla usługi App Service:

1. Zaloguj się do [portalu Azure]
2. Wybierz **pozycję Wszystkie zasoby** lub usługi **aplikacji,** a następnie kliknij nazwę aplikacji mobilnej.
3. Blok Ustawienia zostanie otwarty automatycznie.  Jeśli tak nie jest, kliknij pozycję **Wszystkie ustawienia**.
4. Kliknij **cors** w menu INTERFEJSU API.
5. Wprowadź adres URL, który chcesz dodać w podanym polu, i naciśnij klawisz Enter.
6. W razie potrzeby wprowadź dodatkowe adresy URL.
7. Kliknij polecenie **Zapisz**, aby zapisać ustawienia.

Nowe ustawienia mogą potrwać około 10-15 sekund.

## <a name="how-to-register-for-push-notifications"></a><a name="register-for-push"></a>Jak: Zarejestruj się, aby otrzymywać powiadomienia push
Zainstaluj [phonegap-plugin-push] do obsługi powiadomień push.  Tę wtyczkę można łatwo `cordova plugin add` dodać za pomocą polecenia w wierszu polecenia lub za pośrednictwem instalatora wtyczki Git w programie Visual Studio.  Poniższy kod w aplikacji Apache Cordova rejestruje urządzenie do powiadomień wypychanych:

```javascript
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Użyj sdk centrum powiadomień do wysyłania powiadomień wypychanych z serwera.  Nigdy nie wysyłaj powiadomień wypychanych bezpośrednio od klientów. W ten sposób może służyć do wyzwalania ataku typu "odmowa usługi" przeciwko centrum powiadomień lub usługi PNS.  PNS może zakazać ruchu w wyniku takich ataków.

## <a name="more-information"></a>Więcej informacji

Szczegółowe szczegóły dotyczące interfejsu API można znaleźć w naszej [dokumentacji API.](https://azure.github.io/azure-mobile-apps-js-client/)

<!-- URLs. -->
[Portal Azure]: https://portal.azure.com
[Szybki start aplikacji mobilnych platformy Azure]: app-service-mobile-cordova-get-started.md
[Wprowadzenie do uwierzytelniania]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Wtyczka Apache Cordova dla aplikacji mobilnych platformy Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[twoja pierwsza aplikacja Apache Cordova]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-urządzenie]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
