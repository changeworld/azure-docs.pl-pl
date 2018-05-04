---
title: Jak używać zestawu JavaScript SDK usługi Azure Mobile Apps
description: Sposób użycia v usługi Azure Mobile Apps
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: ba2eb5a7f888e4cffcd798259afa8194b4021025
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Jak używać biblioteki klienckiej JavaScript w usłudze Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ten przewodnik zawiera wskazówki do wykonywania typowych scenariuszy przy użyciu najnowszych [JavaScript SDK usługi Azure Mobile Apps]. Jeśli jesteś nowym użytkownikiem usługi Azure Mobile Apps, najpierw wykonać [Azure Mobile Apps Szybki Start] tworzenie zaplecza i Utwórz tabelę. W tym przewodniku możemy skupić się na przy użyciu zaplecza aplikacji mobilnych w aplikacji sieci Web HTML/JavaScript.

## <a name="supported-platforms"></a>Obsługiwane platformy
Ograniczona obsługa przeglądarek do wersji bieżącej i ostatni główne przeglądarki: Google Chrome, Microsoft Edge, programu Microsoft Internet Explorer i Mozilla Firefox.  Przewidujemy, zestawu SDK do funkcji ze względnie nowoczesne przeglądarki.

Pakiet jest dystrybuowany jako moduł uniwersalnych JavaScript tak CommonJS formaty i obsługuje zmienne globalne, AMD.

## <a name="Setup"></a>Instalacji i wymagania wstępne
W tym przewodniku założono, że utworzono wewnętrznej bazie danych z tabeli. W tym przewodniku założono, że tabela ma ten sam schemat jako tabele w tych samouczkach.

Instalowanie zestawu SDK usługi Azure Mobile Apps JavaScript może odbywać się za pośrednictwem `npm` polecenia:

```
npm install azure-mobile-apps-client --save
```

Biblioteki mogą służyć jako moduł ES2015 w środowiskach CommonJS, takie jak Browserify i Webpack i jako AMD biblioteki.  Na przykład:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Można także użyć wbudowanych wersji zestawu SDK, pobierając bezpośrednio z naszych CDN:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Porady: uwierzytelnianie użytkowników
Usługa aplikacji Azure obsługuje uwierzytelniania i autoryzacji użytkowników aplikacji przy użyciu różnych dostawców tożsamości zewnętrznych: Facebook, Google, Microsoft Account i Twitter. Tabele, aby ograniczyć dostęp dla określonych operacji tylko do uwierzytelnionych użytkowników można ustawić uprawnienia. Umożliwia także tożsamość uwierzytelnionych użytkowników do zaimplementowania reguły autoryzacji w skryptach serwera. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z uwierzytelnianiem] samouczka.

Obsługiwane są dwa przepływy uwierzytelniania: przepływu serwera i klienta przepływu.  Przepływ server zapewnia najprostszą środowisko uwierzytelniania, zależy od interfejsu uwierzytelniania sieci web dostawcy. Przepływ klienta zezwala lepszą integrację z możliwości specyficznych dla urządzeń, takich jak single-sign-on, ponieważ zależy od określonego dostawcy SDK.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Porady: Konfigurowanie usługi Mobile App Service dla adresy URL przekierowań zewnętrznych.
Kilka typów aplikacji JavaScript używania możliwości sprzężenia zwrotnego na potrzeby obsługi przepływu OAuth interfejsu użytkownika.  Te możliwości to m.in.:

* Usługą lokalnie
* Przy użyciu przeładowania na żywo z jonowych Framework
* Przekierowanie do usługi App Service dla uwierzytelniania.

Uruchomiony lokalnie może spowodować problemy, ponieważ domyślnie uwierzytelniania usługi aplikacji jest tylko skonfigurowane i umożliwiają dostęp z zaplecza aplikacji mobilnej. Aby zmienić ustawienia usługi aplikacji, aby włączyć uwierzytelnianie podczas uruchamiania lokalnego serwera, wykonaj następujące kroki:

1. Zaloguj się do witryny [portalu Azure].
2. Przejdź do zaplecza aplikacji mobilnej.
3. Wybierz **Eksploratora zasobów** w **narzędzi PROGRAMISTYCZNYCH** menu.
4. Kliknij przycisk **Przejdź** można otworzyć w nowej karcie lub w oknie Eksploratora zasobów dla zaplecza aplikacji mobilnej.
5. Rozwiń węzeł **config** > **authsettings** węzła dla aplikacji.
6. Kliknij przycisk **Edytuj** przycisk, aby umożliwić edytowanie zasobu.
7. Znajdź **allowedExternalRedirectUrls** element, który może mieć wartości null. Dodaj adresami URL w tablicy:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Zamień na adresy URL usługi, czyli w tym przykładzie adresy URL w tablicy `http://localhost:3000` lokalnej usługi Node.js w próbce. Można także użyć `http://localhost:4400` usługi Ripple lub innych URL, w zależności od sposobu skonfigurowania aplikacji.
8. W górnej części strony kliknij **odczytu/zapisu**, następnie kliknij przycisk **PUT** Aby zapisać zmiany.

Należy również dodać tych samych adresów URL sprzężenia zwrotnego do CORS ustawień listy dozwolonych adresów IP:

1. Przejdź z powrotem do [portalu Azure].
2. Przejdź do zaplecza aplikacji mobilnej.
3. Kliknij przycisk **CORS** w **interfejsu API** menu.
4. Wprowadź każdy adres URL w pustej **dozwolone źródła** pola tekstowego.  Utworzono nowe pole tekstowe.
5. Kliknij przycisk **ZAPISZ**

Po aktualizacji wewnętrznej bazy danych, można korzystać z nowych sprzężenia zwrotnego adresów URL w aplikacji.

<!-- URLs. -->
[Azure Mobile Apps Szybki Start]: app-service-mobile-cordova-get-started.md
[Rozpoczynanie pracy z uwierzytelnianiem]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[portalu Azure]: https://portal.azure.com/
[JavaScript SDK usługi Azure Mobile Apps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
