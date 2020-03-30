---
title: Korzystanie z kodu JavaScript SDK
description: Jak używać v dla aplikacji mobilnych platformy Azure
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 029b01f3aacc928ebdae0e8fe90871437afccea5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461525"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Jak korzystać z biblioteki klienta JavaScript dla aplikacji mobilnych platformy Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Omówienie
W tym przewodniku dowiesz się, jak wykonywać typowe scenariusze przy użyciu najnowszego [kodu JavaScript SDK dla aplikacji azure mobile apps.] Jeśli jesteś nowym użytkownikiem usługi Azure Mobile Apps, najpierw ukończ [szybki start aplikacji Azure Mobile Apps,] aby utworzyć zaplecze i utworzyć tabelę. W tym przewodniku koncentrujemy się na korzystaniu z zaplecza mobilnego w aplikacjach sieci Web HTML/ JavaScript.

## <a name="supported-platforms"></a>Obsługiwane platformy
Ograniczamy obsługę przeglądarki do bieżących i ostatnich wersji głównych przeglądarek: Google Chrome, Microsoft Edge, Microsoft Internet Explorer i Mozilla Firefox.  Oczekujemy, że SDK będzie działać z każdą stosunkowo nowoczesną przeglądarką.

Pakiet jest dystrybuowany jako uniwersalny moduł JavaScript, więc obsługuje formaty globals, AMD i CommonJS.

## <a name="setup-and-prerequisites"></a><a name="Setup"></a>Konfiguracja i wymagania wstępne
W tym przewodniku przyjęto założenie, że utworzono zaplecze z tabelą. W tym przewodniku przyjęto założenie, że tabela ma taki sam schemat jak tabele w tych samouczkach.

Instalowanie zestawu SDK JavaScript w usłudze Azure Mobile Apps można wykonać za pomocą `npm` polecenia:

```
npm install azure-mobile-apps-client --save
```

Biblioteka może być również używana jako moduł ES2015, w środowiskach CommonJS, takich jak Browserify i Webpack oraz jako biblioteka AMD.  Przykład:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Możesz również użyć wstępnie utworzonej wersji SDK, pobierając bezpośrednio z naszej sieci CDN:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="how-to-authenticate-users"></a><a name="auth"></a>Jak: Uwierzytelnij użytkowników
Usługa Azure App Service obsługuje uwierzytelnianie i autoryzowanie użytkowników aplikacji przy użyciu różnych zewnętrznych dostawców tożsamości: Facebook, Google, Microsoft Account i Twitter. Uprawnienia do tabel można ustawić w celu ograniczenia dostępu dla określonych operacji tylko do uwierzytelnionych użytkowników. Można również użyć tożsamości uwierzytelnionych użytkowników do zaimplementowania reguł autoryzacji w skryptach serwera. Aby uzyskać więcej informacji, zobacz [Samouczek Wprowadzenie do uwierzytelniania.]

Obsługiwane są dwa przepływy uwierzytelniania: przepływ serwera i przepływ klienta.  Przepływ serwera zapewnia najprostsze środowisko uwierzytelniania, ponieważ opiera się na interfejsie uwierzytelniania sieci web dostawcy. Przepływ klienta umożliwia głębszą integrację z możliwości specyficznych dla urządzenia, takich jak logowanie jednokrotne, ponieważ opiera się na sdk specyficzne dla dostawcy.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="how-to-configure-your-mobile-app-service-for-external-redirect-urls"></a><a name="configure-external-redirect-urls"></a>Jak: Skonfiguruj usługę aplikacji mobilnej pod kątem zewnętrznych adresów URL przekierowań.
Kilka typów aplikacji JavaScript używać funkcji sprzężenia zwrotnego do obsługi przepływów interfejsu użytkownika OAuth.  Te funkcje obejmują:

* Lokalne uruchamianie usługi
* Korzystanie z live reload z ionic framework
* Przekierowywanie do usługi app service w celu uwierzytelnienia.

Uruchamianie lokalnie może powodować problemy, ponieważ domyślnie uwierzytelnianie usługi app service jest skonfigurowane tylko w celu umożliwienia dostępu z zaplecza aplikacji mobilnej. Aby włączyć uwierzytelnianie podczas lokalnego uruchamiania serwera, należy wykonać następujące kroki:

1. Zaloguj się do [portalu Azure]
2. Przejdź do wewnętrznej bazy danych aplikacji mobilnej.
3. Wybierz **Eksploratora zasobów** w menu **NARZĘDZIA ROZWOJU.**
4. Kliknij **przycisk Przejdź,** aby otworzyć eksploratora zasobów zaplecza aplikacji mobilnej na nowej karcie lub oknie.
5. Rozwiń węzeł**authsettings** **konfiguracji** > dla aplikacji.
6. Kliknij przycisk **Edytuj,** aby włączyć edycję zasobu.
7. Znajdź **allowedExternalRedirectUrls** element, który powinien być null. Dodawanie adresów URL w tablicy:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Zastąp adresy URL w tablicy adresami URL `http://localhost:3000` usługi, która w tym przykładzie jest dla lokalnej usługi przykładowej Node.js. Można również `http://localhost:4400` użyć dla usługi Ripple lub innego adresu URL, w zależności od konfiguracji aplikacji.
8. U góry strony kliknij pozycję **Odczyt/Zapis,** a następnie kliknij przycisk **PUT,** aby zapisać aktualizacje.

Należy również dodać te same adresy URL sprzężenia zwrotnego do ustawień białej listy CORS:

1. Przejdź z powrotem do [witryny Azure portal].
2. Przejdź do wewnętrznej bazy danych aplikacji mobilnej.
3. Kliknij **cors** w menu **INTERFEJSU API.**
4. Wprowadź każdy adres URL w pustym polu tekstowym **Dozwolone pochodzenie.**  Zostanie utworzone nowe pole tekstowe.
5. Kliknij **przycisk ZAPISZ**

Po aktualizacji wewnętrznej bazy danych będzie można używać nowych adresów URL sprzężenia zwrotnego w aplikacji.

<!-- URLs. -->
[Szybki start aplikacji mobilnych platformy Azure]: app-service-mobile-cordova-get-started.md
[Wprowadzenie do uwierzytelniania]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Portal Azure]: https://portal.azure.com/
[Zestaw JavaScript SDK dla aplikacji mobilnych platformy Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
