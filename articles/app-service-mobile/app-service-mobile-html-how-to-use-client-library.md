---
title: Jak używać zestawu SDK języka JavaScript dla usługi Azure Mobile Apps
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
ms.openlocfilehash: 16871bdc59d141334bc2c95f26929f270d7971cf
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106748"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Jak używać biblioteki klienckiej JavaScript dla usługi Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Tego przewodnika dowiesz się, aby wykonać typowe scenariusze za pomocą najnowszej [zestaw JavaScript SDK usługi Azure Mobile Apps]. Jeśli jesteś nowym użytkownikiem usługi Azure Mobile Apps, najpierw wykonaj [Azure mobilnych: aplikacje Szybki Start] tworzenie zaplecza i Utwórz tabelę. W tym przewodniku skupimy się na korzystanie z zaplecza aplikacji mobilnych w aplikacji sieci Web HTML/JavaScript.

## <a name="supported-platforms"></a>Obsługiwane platformy
Ograniczona obsługa przeglądarek na bieżące i ostatnie wersje ważniejszymi przeglądarkami:  Google Chrome, Microsoft Edge, Microsoft Internet Explorer, and Mozilla Firefox.  Oczekujemy, że zestaw SDK do funkcji z dowolnej stosunkowo nowoczesnej przeglądarce.

Dystrybucji pakietu jako moduł uniwersalny język JavaScript obsługuje funkcje globalne, AMD, i formatuje CommonJS.

## <a name="Setup"></a>Instalacji i wymagania wstępne
W tym przewodniku założono, że utworzono zaplecza za pomocą tabeli. W tym przewodniku założono, że tabela ma ten sam schemat, jak tabele w tych samouczkach.

Instalowanie zestawu SDK języka JavaScript usługi Azure Mobile Apps może odbywać się za pośrednictwem `npm` polecenia:

```
npm install azure-mobile-apps-client --save
```

Biblioteki może również służyć jako moduł ES2015 w środowiskach CommonJS, takie jak Browserify i Webpack i jako biblioteka AMD.  Na przykład:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Umożliwia także wstępnie skompilowanych wersji zestawu SDK, pobierając bezpośrednio z naszej sieci CDN:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Jak: Uwierzytelnianie użytkowników
Usługa Azure App Service obsługuje uwierzytelnianie i autoryzowanie użytkowników aplikacji za pomocą różnych dostawców tożsamości zewnętrznych: Facebook, Google, konta Microsoft i Twitter. Możesz ustawić uprawnienia w tabelach ograniczyć dostęp dla określonych operacji tylko do uwierzytelnionych użytkowników. Tożsamość uwierzytelnionych użytkowników umożliwia również zaimplementować reguły autoryzacji w skryptów serwera. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z uwierzytelnianiem] samouczka.

Obsługiwane są dwa przepływy uwierzytelniania: przepływ serwera i klienta przepływu.  Przepływ serwera zapewnia najprostszą proces uwierzytelniania opiera się na interfejs uwierzytelniania sieci web dostawcy. Przepływ klienta pozwala na lepszą integrację z funkcjami specyficznych dla urządzenia, takich jak logowanie jednokrotne ponieważ opiera się na zestawy SDK specyficzne dla dostawcy.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Jak: Konfigurowanie usługi Mobile App Service dla adresy URL zewnętrznego przekierowania.
Kilka typów aplikacji JavaScript Użyj możliwości sprzężenia zwrotnego do obsługi uwierzytelniania OAuth interfejsu użytkownika przepływów.  Te funkcje obejmują:

* Uruchamianie usługi lokalnie
* Ponowne ładowanie na żywo przy użyciu struktury Ionic
* Przekierowywanie do usługi App Service do uwierzytelniania.

Działająca lokalnie może powodować problemy, ponieważ domyślnie uwierzytelniania usługi App Service jest tylko skonfigurowane i umożliwiają dostęp z zapleczem aplikacji mobilnej. Aby zmienić ustawienia usługi App Service, aby włączyć uwierzytelnianie podczas uruchamiania lokalnego serwera, wykonaj następujące kroki:

1. Zaloguj się do witryny [Azure Portal].
2. Przejdź do zaplecza aplikacji mobilnej.
3. Wybierz **Eksploratora zasobów** w **narzędzia PROGRAMISTYCZNE** menu.
4. Kliknij przycisk **Przejdź** otwarcie Eksploratora zasobów do zaplecza aplikacji mobilnej w nowej karcie lub w oknie.
5. Rozwiń **config** > **authsettings** węzła dla aplikacji.
6. Kliknij przycisk **Edytuj** przycisk, aby włączyć edytowanie zasobów.
7. Znajdź **allowedExternalRedirectUrls** element, który powinien mieć wartość null. Dodaj swoje adresy URL w tablicy:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Zastąp adresy URL w tablicy przy użyciu adresów URL usługi, czyli w tym przykładzie `http://localhost:3000` lokalnej usługi Node.js w próbce. Można także użyć `http://localhost:4400` usługi Ripple lub niektórych innych adresu URL, w zależności od sposobu skonfigurowania aplikacji.
8. W górnej części strony kliknij **odczytu/zapisu**, następnie kliknij przycisk **umieścić** Aby zapisać zmiany.

Należy również dodać tych samych adresów URL sprzężenia zwrotnego do ustawień listy dozwolonych CORS:

1. Przejdź z powrotem do [Azure Portal].
2. Przejdź do zaplecza aplikacji mobilnej.
3. Kliknij przycisk **CORS** w **API** menu.
4. Wprowadź każdy adres URL w pustych **dozwolone źródła** pola tekstowego.  Nowe pole tekstowe zostanie utworzony.
5. Kliknij przycisk **ZAPISZ**

Po zaktualizowaniu wewnętrznej bazy danych będzie można korzystać z nowych sprzężenia zwrotnego adresów URL w aplikacji.

<!-- URLs. -->
[Azure mobilnych: aplikacje Szybki Start]: app-service-mobile-cordova-get-started.md
[Rozpoczynanie pracy z uwierzytelnianiem]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure Portal]: https://portal.azure.com/
[Zestaw JavaScript SDK usługi Azure Mobile Apps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
