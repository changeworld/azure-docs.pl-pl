---
title: Jak używać wtyczki Apache Cordova dla platformy Azure Mobile Apps
description: Jak używać wtyczki Apache Cordova dla platformy Azure Mobile Apps
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
ms.openlocfilehash: 3cf18e6da56b25e453d52dc58020961f672da27d
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027437"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Jak używać Apache Cordovaej biblioteki klienckiej dla platformy Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi centralne do tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług **kompilowania**, **testowania** i **dystrybucji** , aby skonfigurować ciągłą integrację i potok dostarczania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie swojej aplikacji przy użyciu usług **analizy** i **diagnostyki** oraz angażować się z użytkownikami za pomocą usługi **wypychania** . Deweloperzy mogą również korzystać z **uwierzytelniania** w celu uwierzytelniania użytkowników i usługi **danych** w celu utrwalania i synchronizowania danych aplikacji w chmurze.
> Jeśli chcesz zintegrować usługi w chmurze w swojej aplikacji mobilnej, zarejestruj się w usłudze App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Przegląd
W tym przewodniku nauczysz się wykonywać typowe scenariusze przy użyciu najnowszej [Apache Cordova wtyczka dla platformy Azure Mobile Apps]. Jeśli dopiero zaczynasz pracę z platformą Azure Mobile Apps, najpierw Ukończ [Szybki start Mobile Apps platformy Azure] , aby utworzyć zaplecze, utworzyć tabelę i pobrać wstępnie utworzony projekt Apache Cordova. W tym przewodniku koncentrujemy się na dodatku Apache Cordova po stronie klienta.

## <a name="supported-platforms"></a>Obsługiwane platformy
Ten zestaw SDK obsługuje Apache Cordova v 6.0.0 i nowszych na urządzeniach z systemami iOS, Android i Windows.  Obsługa platformy jest następująca:

* Android API 19-24 (KitKat do Nougat).
* iOS w wersji 8,0 lub nowszej.
* Windows Phone 8,1.
* Platforma uniwersalna systemu Windows.

## <a name="Setup"></a>Instalacja i wymagania wstępne
W tym przewodniku przyjęto założenie, że utworzono zaplecze z tabelą. W tym przewodniku przyjęto założenie, że tabela ma ten sam schemat co tabele w tych samouczkach. W tym przewodniku przyjęto również założenie, że dodano wtyczkę Apache Cordova do kodu.  Jeśli nie zostało to zrobione, możesz dodać wtyczkę Apache Cordova do projektu w wierszu polecenia:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Aby uzyskać więcej informacji na temat tworzenia [Twoja pierwsza aplikacja Apache Cordova], zobacz dokumentację.

## <a name="ionic"></a>Konfigurowanie aplikacji jonowej w wersji 2

Aby poprawnie skonfigurować projekt jonowy w wersji 2, najpierw utwórz podstawową aplikację i Dodaj wtyczkę oprogramowania Cordova:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Dodaj następujące wiersze do `app.component.ts`, aby utworzyć obiekt klienta:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Teraz można skompilować i uruchomić projekt w przeglądarce:

```
ionic platform add browser
ionic run browser
```

Wtyczka platformy Azure Mobile Apps Cordova obsługuje zarówno aplikacje jonowe V1, jak i v2.  Tylko aplikacje jonowe v2 wymagają dodatkowej deklaracji dla obiektu `WindowsAzure`.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Instrukcje: uwierzytelnianie użytkowników
Azure App Service obsługuje uwierzytelnianie i Autoryzowanie użytkowników aplikacji przy użyciu różnych zewnętrznych dostawców tożsamości: Facebook, Google, konto Microsoft i Twitter. Możesz ustawić uprawnienia dla tabel, aby ograniczyć dostęp do określonych operacji tylko do użytkowników uwierzytelnionych. Aby zaimplementować reguły autoryzacji w skryptach serwera, można także użyć tożsamości uwierzytelnionych użytkowników. Aby uzyskać więcej informacji, zobacz samouczek [wprowadzenie do uwierzytelniania] .

W przypadku korzystania z uwierzytelniania w aplikacji Apache Cordova następujące wtyczki oprogramowania Cordova muszą być dostępne:

* [Cordova — wtyczka-urządzenie]
* [Cordova-wtyczka-wtyczkę InAppBrowser]

Obsługiwane są dwa przepływy uwierzytelniania: przepływ serwera i przepływ klienta.  Przepływ serwera zapewnia najprostsze środowisko uwierzytelniania, ponieważ opiera się on na interfejsie uwierzytelniania sieci Web dostawcy. Przepływ klienta umożliwia dokładniejszą integrację z funkcjami specyficznymi dla urządzenia, takimi jak logowanie jednokrotne, w zależności od specyficznych dla dostawcy zestawów SDK specyficznych dla urządzeń.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Instrukcje: Konfigurowanie App Service mobilnego dla zewnętrznych adresów URL przekierowań.
Kilka typów aplikacji Apache Cordova używa funkcji sprzężenia zwrotnego do obsługi przepływów interfejsu użytkownika OAuth.  Przepływy pracy uwierzytelniania OAuth na hoście lokalnym powodują problemy, ponieważ usługa uwierzytelniania wie, jak używać usługi domyślnie.  Przykłady problemów dotyczących przepływów interfejsu użytkownika OAuth obejmują:

* Emulator Ripple.
* Załaduj ponownie na żywo z użyciem jonowej.
* Lokalne uruchamianie zaplecza mobilnego
* Uruchamianie zaplecza mobilnego w innym Azure App Service niż ten, który zapewnia uwierzytelnianie.

Postępuj zgodnie z poniższymi instrukcjami, aby dodać ustawienia lokalne do konfiguracji:

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz pozycję **wszystkie zasoby** lub **App Services** następnie kliknij nazwę swojej aplikacji mobilnej.
3. Kliknij pozycję **Narzędzia**
4. W menu Obserwuj kliknij pozycję **Eksplorator zasobów** , a następnie kliknij pozycję **Przejdź**.  Zostanie otwarte nowe okno lub karta.
5. Rozwiń węzły **config**, **authsettings** witryny w obszarze nawigacji po lewej stronie.
6. Kliknij przycisk **Edytuj**
7. Poszukaj elementu "allowedExternalRedirectUrls".  Może być ustawiona na wartość null lub tablicę wartości.  Zmień wartość na następującą wartość:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Zastąp adresy URL adresami URL usługi.  Przykładami mogą być `http://localhost:3000` (dla przykładowej usługi Node. js) lub `http://localhost:4400` (w przypadku usługi Ripple).  Jednak te adresy URL to przykłady — Twoja sytuacja, która obejmuje usługi wymienione w przykładach, może się różnić.
8. Kliknij przycisk do **odczytu/zapisu** w prawym górnym rogu ekranu.
9. Kliknij zielony przycisk **Put** .

Ustawienia są zapisywane w tym momencie.  Nie zamykaj okna przeglądarki do momentu zakończenia zapisywania ustawień.
Należy również dodać te adresy URL sprzężenia zwrotnego do ustawień CORS dla App Service:

1. Zaloguj się do witryny [Azure Portal].
2. Wybierz pozycję **wszystkie zasoby** lub **App Services** następnie kliknij nazwę swojej aplikacji mobilnej.
3. Blok ustawienia zostanie otwarty automatycznie.  Jeśli nie, kliknij przycisk **wszystkie ustawienia**.
4. Kliknij pozycję **CORS** w menu interfejsu API.
5. Wprowadź adres URL, który chcesz dodać, w polu udostępnionym, a następnie naciśnij klawisz ENTER.
6. Wprowadź dodatkowe adresy URL zgodnie z wymaganiami.
7. Kliknij polecenie **Zapisz**, aby zapisać ustawienia.

Nowe ustawienia zaczną obowiązywać przez około 10-15 sekund.

## <a name="register-for-push"></a>Instrukcje: rejestrowanie w celu otrzymywania powiadomień wypychanych
Zainstaluj program [PhoneGap — wtyczka — wypychanie] w celu obsługi powiadomień wypychanych.  Tę wtyczkę można łatwo dodać przy użyciu polecenia `cordova plugin add` w wierszu polecenia lub za pośrednictwem Instalatora wtyczki Git w programie Visual Studio.  Poniższy kod w aplikacji Apache Cordova rejestruje Twoje urządzenie na potrzeby powiadomień wypychanych:

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

Użyj zestawu SDK Notification Hubs, aby wysyłać powiadomienia wypychane z serwera.  Nigdy nie wysyłaj powiadomień wypychanych bezpośrednio od klientów. Wykonanie tej czynności może być wykorzystane do wyzwolenia ataku typu "odmowa usługi" na Notification Hubs lub PNS.  PNS może uniemożliwić ruch w wyniku takich ataków.

## <a name="more-information"></a>Więcej informacji

Szczegółowe informacje o interfejsie API można znaleźć w [dokumentacji interfejsu API](https://azure.github.io/azure-mobile-apps-js-client/).

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com
[Szybki start Mobile Apps platformy Azure]: app-service-mobile-cordova-get-started.md
[Wprowadzenie do uwierzytelniania]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova wtyczka dla platformy Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[Twoja pierwsza aplikacja Apache Cordova]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[PhoneGap — wtyczka — wypychanie]: https://www.npmjs.com/package/phonegap-plugin-push
[Cordova — wtyczka-urządzenie]: https://www.npmjs.com/package/cordova-plugin-device
[Cordova-wtyczka-wtyczkę InAppBrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
