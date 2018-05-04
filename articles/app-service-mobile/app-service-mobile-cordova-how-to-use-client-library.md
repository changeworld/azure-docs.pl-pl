---
title: Jak używać wtyczki programu Apache Cordova dla usługi Azure Mobile Apps
description: Jak używać wtyczki programu Apache Cordova dla usługi Azure Mobile Apps
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 6fb8be96c9793e96f1f7d2ad8e212d056d7e9ba5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Jak używać oprogramowania Apache Cordova biblioteki klienta usługi Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ten przewodnik zawiera wskazówki do wykonywania typowych scenariuszy przy użyciu najnowszych [Apache wtyczki Cordova dla usługi Azure Mobile Apps]. Jeśli jesteś nowym użytkownikiem usługi Azure Mobile Apps, najpierw wykonać [Azure Mobile Apps Szybki Start] tworzenie zaplecza, Utwórz tabelę i Pobierz wstępnie skompilowanym projektem Apache Cordova. W tym przewodniku możemy skupić się na wtyczki po stronie klienta Apache Cordova.

## <a name="supported-platforms"></a>Obsługiwane platformy
Zestaw SDK obsługuje Apache Cordova 6.0.0 i później z systemem iOS, Android i Windows urządzeń.  Obsługa platform wygląda następująco:

* Interfejs API systemu android 19 – 24 (KitKat za pośrednictwem nugacie).
* System iOS w wersji 8.0 lub nowszej.
* Windows Phone 8.1.
* Platforma uniwersalna systemu Windows.

## <a name="Setup"></a>Instalacji i wymagania wstępne
W tym przewodniku założono, że utworzono wewnętrznej bazie danych z tabeli. W tym przewodniku założono, że tabela ma ten sam schemat jako tabele w tych samouczkach. W tym przewodniku założono również, że dodano wtyczki Apache Cordova w kodzie.  Jeśli użytkownik nie zostało zrobione, można dodawać wtyczki oprogramowania Apache Cordova do projektu w wierszu polecenia:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Aby uzyskać więcej informacji na temat tworzenia [pierwszej aplikacji platformy Apache Cordova], ich w dokumentacji.

## <a name="ionic"></a>Konfigurowanie aplikacji jonowych v2

Aby poprawnie skonfigurować projekt jonowych v2, utworzona Podstawowa aplikacja i dodawanie wtyczki Cordova:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Dodaj następujące wiersze do `app.component.ts` można utworzyć obiektu klienta:

```
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Teraz możesz skompilować i uruchomić projekt w przeglądarce:

```
ionic platform add browser
ionic run browser
```

Wtyczka Cordova aplikacji mobilnej Azure obsługuje zarówno jonowych aplikacje v1 i v2.  Tylko aplikacje jonowych v2 wymagają dodatkowych deklaracji `WindowsAzure` obiektu.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Porady: uwierzytelnianie użytkowników
Usługa aplikacji Azure obsługuje uwierzytelniania i autoryzacji użytkowników aplikacji przy użyciu różnych dostawców tożsamości zewnętrznych: Facebook, Google, Microsoft Account i Twitter. Tabele, aby ograniczyć dostęp dla określonych operacji tylko do uwierzytelnionych użytkowników można ustawić uprawnienia. Umożliwia także tożsamość uwierzytelnionych użytkowników do zaimplementowania reguły autoryzacji w skryptach serwera. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z uwierzytelnianiem] samouczka.

Korzystając z uwierzytelniania w aplikacji oprogramowania Apache Cordova, następujące wtyczki Cordova muszą być dostępne:

* [cordova wtyczka urządzenie]
* [cordova wtyczka inappbrowser]

Obsługiwane są dwa przepływy uwierzytelniania: przepływu serwera i klienta przepływu.  Przepływ server zapewnia najprostszą środowisko uwierzytelniania, zależy od interfejsu uwierzytelniania sieci web dostawcy. Przepływ klienta zezwala lepszą integrację z możliwości specyficznych dla urządzeń, takich jak single-sign-on jako opiera się na zestawy SDK urządzenia specyficznego dla dostawcy.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Porady: Konfigurowanie usługi Mobile App Service dla adresy URL przekierowań zewnętrznych.
Kilka typów aplikacji oprogramowania Apache Cordova używania możliwości sprzężenia zwrotnego na potrzeby obsługi przepływu OAuth interfejsu użytkownika.  Przepływu OAuth interfejsu użytkownika na hoście lokalnym spowodować problemy, ponieważ usługa uwierzytelniania tylko potrafi mogą korzystać z usługi domyślnie.  Przykłady problematyczne przepływu OAuth interfejs użytkownika:

* Ripple emulator.
* Załaduj ponownie z Ionic na żywo.
* Uruchomienie zaplecze aplikacji mobilnej lokalnie
* Uruchomiony zaplecza aplikacji mobilnych w innej usłudze Azure App Service niż jeden zapewniający uwierzytelnianie.

Wykonaj te instrukcje, aby dodać ustawienia lokalne dla konfiguracji:

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz **wszystkie zasoby** lub **usługi aplikacji** następnie kliknij nazwę aplikacji mobilnej.
3. Kliknij przycisk **narzędzia**
4. Kliknij przycisk **Eksploratora zasobów** w OBSERVE menu, następnie kliknij przycisk **Przejdź**.  Powoduje otwarcie nowego okna lub karty.
5. Rozwiń węzeł **config**, **authsettings** węzłów witryny w obszarze nawigacji po lewej stronie.
6. Kliknij przycisk **edycji**
7. Wyszukaj element "allowedExternalRedirectUrls".  Może zostać ustawiony na zero lub tablicy wartości.  Zmień wartość na następującą wartość:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Zastąp adresy URL adresy URL usługi.  Przykłady obejmują "http://localhost:3000" (w przypadku usługi próbki Node.js) lub "http://localhost:4400" (dla usługi Ripple).  Jednak te adresy URL są przykłady - sytuacji, w tym dla usług wymienionych w przykładach, mogą być inne.
8. Kliknij przycisk **odczytu/zapisu** przycisk w prawym górnym rogu ekranu.
9. Kliknij zielony **PUT** przycisku.

Ustawienia są zapisywane w tym momencie.  Nie zamykaj okna przeglądarki, aż do zakończenia ustawienia zapisywania.
Te adresy URL sprzężenia zwrotnego należy również dodać do ustawienia mechanizmu CORS usługi aplikacji:

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz **wszystkie zasoby** lub **usługi aplikacji** następnie kliknij nazwę aplikacji mobilnej.
3. Automatycznie zostanie otwarty blok ustawień.  Jeśli nie kliknij przycisk **wszystkie ustawienia**.
4. Kliknij przycisk **CORS** w menu interfejsu API.
5. Wprowadź adres URL, który chcesz dodać w polu pod warunkiem i naciśnij klawisz Enter.
6. Wprowadź dodatkowe adresy URL, zgodnie z potrzebami.
7. Kliknij polecenie **Zapisz**, aby zapisać ustawienia.

Trwa około 10 – 15 sekund nowe ustawienia zaczęły obowiązywać.

## <a name="register-for-push"></a>Porady: rejestrowanie do powiadomień wypychanych
Zainstaluj [phonegap wtyczka wypychanie] do obsługi powiadomień wypychanych.  Ten dodatek plug-in można łatwo dodać przy użyciu `cordova plugin add` polecenie w wierszu polecenia lub za pomocą Instalatora dodatku Git w programie Visual Studio.  Następujący kod w aplikacji platformy Apache Cordova rejestruje urządzenie dla powiadomień wypychanych:

```
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

Zestaw SDK usługi Notification Hubs umożliwia wysyłanie powiadomień wypychanych z serwera.  Nigdy nie wysyłaj powiadomienia wypychane bezpośrednio od klientów. Dzięki temu może służyć do wyzwolenia odmowa atak usługi Notification Hubs lub systemu powiadomień platformy.  System powiadomień platformy można zakaz ruchu wyniku takich ataków.

## <a name="more-information"></a>Więcej informacji

Można znaleźć szczegółowe szczegóły interfejsu API w naszym [dokumentacji interfejsu API](http://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com
[Azure Mobile Apps Szybki Start]: app-service-mobile-cordova-get-started.md
[Rozpoczynanie pracy z uwierzytelnianiem]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache wtyczki Cordova dla usługi Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[pierwszej aplikacji platformy Apache Cordova]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap wtyczka wypychanie]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova wtyczka urządzenie]: https://www.npmjs.com/package/cordova-plugin-device
[cordova wtyczka inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
