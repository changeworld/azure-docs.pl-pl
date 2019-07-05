---
title: Jak używać wtyczki Apache Cordova dla usługi Azure Mobile Apps
description: Jak używać wtyczki Apache Cordova dla usługi Azure Mobile Apps
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 327cb3a3667c63454549ec694790769c9ea1fd58
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446423"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Jak używać biblioteki klienckiej Apache Cordova dla usługi Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center jest inwestujemy w nowe i zintegrowane usługi decydujące znaczenie dla aplikacji mobilnych. Deweloperzy mogą używać **kompilacji**, **testu** i **dystrybucji** usług do konfigurowania potoku ciągłej integracji i ciągłego dostarczania. Gdy aplikacja jest wdrażana, deweloperzy mogą monitorować stan i użycie ich przy użyciu aplikacji **Analytics** i **diagnostyki** usług i angażuj użytkowników za pomocą **wypychania** Usługa. Deweloperzy mogą również wykorzystać **uwierzytelniania** do uwierzytelniania użytkowników i **danych** usługę, aby utrwalić i synchronizowanie danych aplikacji w chmurze. Zapoznaj się z [platformy App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-cordova-how-to-use-client-library) już dziś.
>

## <a name="overview"></a>Przegląd
Tego przewodnika dowiesz się, aby wykonać typowe scenariusze za pomocą najnowszej [Wtyczka Apache Cordova dla usługi Azure Mobile Apps]. Jeśli jesteś nowym użytkownikiem usługi Azure Mobile Apps, najpierw wykonaj [Azure mobilnych: aplikacje Szybki Start] Aby utworzyć zaplecze, utworzyć tabelę, a następnie Pobierz wstępnie skompilowanym projektem Apache Cordova. W tym przewodniku skupimy się na wtyczkę Apache Cordova po stronie klienta.

## <a name="supported-platforms"></a>Obsługiwane platformy
Ten zestaw SDK obsługuje v6.0.0 Apache Cordova i później dla systemu iOS, Android i Windows urządzenia.  Obsługa platform jest następująca:

* Interfejs API systemu android 19 – 24 (KitKat za pośrednictwem określić wersję Nougat).
* dla systemu iOS w wersji 8.0 lub nowszej.
* Windows Phone 8.1.
* Platforma Universal Windows.

## <a name="Setup"></a>Instalacji i wymagania wstępne
W tym przewodniku założono, że utworzono zaplecza za pomocą tabeli. W tym przewodniku założono, że tabela ma ten sam schemat, jak tabele w tych samouczkach. W tym przewodniku założono również, że dodano wtyczki programu Apache Cordova w kodzie.  Jeśli użytkownik nie zostało zrobione, można dodawać wtyczki programu Apache Cordova do projektu w wierszu polecenia:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Aby uzyskać więcej informacji na temat tworzenia [Twoja pierwsza aplikacja Apache Cordova], zobacz ich dokumentację.

## <a name="ionic"></a>Konfigurowanie aplikacji platformy Ionic 2

Aby poprawnie skonfigurować projekt środowiska Ionic w wersji 2, utworzysz podstawową aplikację i dodawanie wtyczki Cordova:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Dodaj następujące wiersze do `app.component.ts` do utworzenia obiektu klienta:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Teraz można tworzyć i uruchomić projekt w przeglądarce:

```
ionic platform add browser
ionic run browser
```

Wtyczka usługi Azure Mobile Apps Cordova obsługuje obie platformy Ionic aplikacje v1 i v2.  Tylko aplikacje platformy Ionic 2 wymagają dodatkowej deklaracji pod kątem `WindowsAzure` obiektu.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Jak: Uwierzytelnianie użytkowników
Usługa Azure App Service obsługuje uwierzytelnianie i autoryzowanie użytkowników aplikacji za pomocą różnych dostawców tożsamości zewnętrznych: Facebook, Google, konta Microsoft i Twitter. Możesz ustawić uprawnienia w tabelach ograniczyć dostęp dla określonych operacji tylko do uwierzytelnionych użytkowników. Tożsamość uwierzytelnionych użytkowników umożliwia również zaimplementować reguły autoryzacji w skryptów serwera. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z uwierzytelnianiem] samouczka.

Podczas uwierzytelniania w aplikacji Apache Cordova, następujące wtyczki Cordova muszą być dostępne:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Obsługiwane są dwa przepływy uwierzytelniania: przepływ serwera i klienta przepływu.  Przepływ serwera zapewnia najprostszą proces uwierzytelniania opiera się na interfejs uwierzytelniania sieci web dostawcy. Przepływ klienta pozwala na lepszą integrację z funkcjami specyficznych dla urządzenia, takich jak logowanie jednokrotne ponieważ opiera się na zestawy SDK specyficzne dla urządzenia specyficzny dla dostawcy.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Jak: Konfigurowanie usługi Mobile App Service dla adresy URL zewnętrznego przekierowania.
Kilka typów aplikacji Apache Cordova używać możliwości sprzężenia zwrotnego do obsługi uwierzytelniania OAuth interfejsu użytkownika przepływów.  Przepływy uwierzytelniania OAuth interfejsu użytkownika na hoście lokalnym powodować problemy, ponieważ usługa uwierzytelniania wie jedynie, sposób wykorzystywania usługi domyślnie.  Przykłady problematyczne przepływów uwierzytelniania OAuth interfejsu użytkownika:

* Ripple emulator.
* Ponowne ładowanie dzięki zastosowaniu środowiska Ionic na żywo.
* Działająca lokalnie zaplecza aplikacji mobilnych
* Uruchamianie zaplecza aplikacji mobilnych w innej usłudze Azure App Service niż jeden zapewniający uwierzytelnianie.

Wykonaj te instrukcje, aby dodać ustawienia lokalne do konfiguracji:

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz **wszystkie zasoby** lub **App Services** kliknij nazwę swojej aplikacji mobilnej.
3. Kliknij przycisk **narzędzia**
4. Kliknij przycisk **Eksploratora zasobów** w OBSERVE menu, następnie kliknij przycisk **Przejdź**.  Zostanie otwarte nowe okno lub kartę.
5. Rozwiń **config**, **authsettings** węzłów do lokacji w obszarze nawigacji po lewej stronie.
6. Kliknij przycisk **edycji**
7. Zwróć uwagę na element "allowedExternalRedirectUrls".  Może zostać ustawiony na wartość null lub tablicę wartości.  Zmień wartość na następującą wartość:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Zastąp adresy URL przy użyciu adresów URL usługi.  Przykłady obejmują `http://localhost:3000` (dla środowiska Node.js przykładowej usługi), lub `http://localhost:4400` (usługi Ripple).  Jednak te adresy URL są przykłady — w sytuacji, w tym dla usług wymienionych w przykładach, mogą być inne.
8. Kliknij przycisk **odczytu/zapisu** przycisk w prawym górnym rogu ekranu.
9. Kliknij zielony **umieścić** przycisku.

Ustawienia są zapisywane w tym momencie.  Nie zamykaj okna przeglądarki do czasu zakończenia ustawienia zapisywania.
Te adresy URL sprzężenia zwrotnego można również dodać ustawienia mechanizmu CORS usługi App Service:

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz **wszystkie zasoby** lub **App Services** kliknij nazwę swojej aplikacji mobilnej.
3. Automatycznie zostanie otwarty blok ustawienia.  Jeśli nie kliknij przycisk **wszystkie ustawienia**.
4. Kliknij przycisk **CORS** menu interfejsu API.
5. Wprowadź adres URL, który chcesz dodać w polu pod warunkiem i naciśnij klawisz Enter.
6. Wprowadź dodatkowe adresy URL, zgodnie z potrzebami.
7. Kliknij polecenie **Zapisz**, aby zapisać ustawienia.

Trwa około 10 – 15 sekund nowe ustawienia zaczęły obowiązywać.

## <a name="register-for-push"></a>Jak: Rejestrowanie na potrzeby powiadomień wypychanych
Zainstaluj [phonegap wtyczka push] do obsługi powiadomień wypychanych.  Ta wtyczka można łatwo dodać przy użyciu `cordova plugin add` polecenia w wierszu polecenia lub za pomocą Instalatora wtyczki usługi Git w programie Visual Studio.  Poniższy kod w swojej aplikacji Apache Cordova wykonywana jest rejestracja urządzenia dla powiadomień wypychanych:

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

Zestaw SDK usługi Notification Hubs umożliwia wysyłanie powiadomień wypychanych z serwera.  Nigdy nie wysyłaj powiadomienia wypychane bezpośrednio od klientów. Ten sposób może służyć do wyzwolenia "odmowa usługi" przeciwko usługi Notification Hubs lub system powiadomień platformy.  System powiadomień platformy można zablokować ruch sieciowy w wyniku takich ataków.

## <a name="more-information"></a>Więcej informacji

Można znaleźć szczegółowe szczegółów interfejsu API w naszym [dokumentacji interfejsu API](https://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com
[Azure mobilnych: aplikacje Szybki Start]: app-service-mobile-cordova-get-started.md
[Rozpoczynanie pracy z uwierzytelnianiem]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Wtyczka Apache Cordova dla usługi Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[Twoja pierwsza aplikacja Apache Cordova]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap wtyczka push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
