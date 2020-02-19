---
title: Korzystanie z zestawu SDK języka JavaScript
description: Jak używać programu v dla usługi Azure Mobile Apps
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 029b01f3aacc928ebdae0e8fe90871437afccea5
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461525"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Jak używać biblioteki klienckiej języka JavaScript dla platformy Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Omówienie
W tym przewodniku nauczysz się wykonywać typowe scenariusze przy użyciu najnowszego [Zestaw JavaScript SDK dla platformy Azure Mobile Apps]. Jeśli dopiero zaczynasz pracę z platformą Azure Mobile Apps, najpierw Ukończ [Szybki start Mobile Apps platformy Azure] , aby utworzyć zaplecze i utworzyć tabelę. W tym przewodniku koncentrujemy się na korzystaniu z zaplecza mobilnego w aplikacjach sieci Web w języku HTML/JavaScript.

## <a name="supported-platforms"></a>Obsługiwane platformy
Ograniczamy obsługę przeglądarki do bieżących i ostatnich wersji głównych przeglądarek: Google Chrome, Microsoft Edge, Microsoft Internet Explorer i Mozilla Firefox.  Oczekujemy, że zestaw SDK będzie działał z każdą relatywnie nowoczesnym przeglądarką.

Pakiet jest dystrybuowany jako moduł uniwersalny języka JavaScript, dlatego obsługuje formaty Globals, AMD i CommonJS.

## <a name="Setup"></a>Instalacja i wymagania wstępne
W tym przewodniku przyjęto założenie, że utworzono zaplecze z tabelą. W tym przewodniku przyjęto założenie, że tabela ma ten sam schemat co tabele w tych samouczkach.

Instalowanie zestawu Azure Mobile Apps JavaScript SDK można wykonać za pomocą polecenia `npm`:

```
npm install azure-mobile-apps-client --save
```

Biblioteka może być również używana jako moduł ES2015 w środowiskach CommonJS, takich jak Browserify i WebPack oraz jako biblioteka AMD.  Na przykład:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Możesz również użyć wstępnie skompilowanej wersji zestawu SDK, pobierając bezpośrednio z naszej sieci CDN:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Instrukcje: uwierzytelnianie użytkowników
Azure App Service obsługuje uwierzytelnianie i Autoryzowanie użytkowników aplikacji przy użyciu różnych zewnętrznych dostawców tożsamości: Facebook, Google, konto Microsoft i Twitter. Możesz ustawić uprawnienia dla tabel, aby ograniczyć dostęp do określonych operacji tylko do użytkowników uwierzytelnionych. Aby zaimplementować reguły autoryzacji w skryptach serwera, można także użyć tożsamości uwierzytelnionych użytkowników. Aby uzyskać więcej informacji, zobacz samouczek [wprowadzenie do uwierzytelniania] .

Obsługiwane są dwa przepływy uwierzytelniania: przepływ serwera i przepływ klienta.  Przepływ serwera zapewnia najprostsze środowisko uwierzytelniania, ponieważ opiera się on na interfejsie uwierzytelniania sieci Web dostawcy. Przepływ klienta umożliwia dokładniejszą integrację z funkcjami specyficznymi dla konkretnego urządzenia, takimi jak logowanie jednokrotne, w zależności od specyficznych dla dostawcy zestawów SDK.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Instrukcje: Konfigurowanie App Service mobilnego dla zewnętrznych adresów URL przekierowań.
Kilka typów aplikacji JavaScript używa funkcji sprzężenia zwrotnego do obsługi przepływów interfejsu użytkownika OAuth.  Dostępne są następujące możliwości:

* Uruchamianie usługi lokalnie
* Używanie usługi Live reload z platformą jonową
* Przekierowanie do App Service na potrzeby uwierzytelniania.

Uruchamianie lokalne może spowodować problemy, ponieważ domyślnie uwierzytelnianie App Service jest skonfigurowane tak, aby zezwalać na dostęp z zaplecza aplikacji mobilnej. Aby zmienić ustawienia App Service, należy wykonać następujące kroki, aby włączyć uwierzytelnianie w przypadku lokalnego uruchamiania serwera:

1. Zaloguj się do witryny [Azure Portal].
2. Przejdź do zaplecza aplikacji mobilnej.
3. Wybierz pozycję **Eksplorator zasobów** w menu **Narzędzia programistyczne** .
4. Kliknij pozycję **Przejdź** , aby otworzyć Eksploratora zasobów dla zaplecza aplikacji mobilnej na nowej karcie lub w nowym oknie.
5. Rozwiń węzeł **config** > **authsettings** dla swojej aplikacji.
6. Kliknij przycisk **Edytuj** , aby włączyć edytowanie zasobu.
7. Znajdź element **allowedExternalRedirectUrls** , który powinien mieć wartość null. Dodawanie adresów URL w tablicy:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Zastąp adresy URL w tablicy adresami URL usługi, które w tym przykładzie są `http://localhost:3000` dla lokalnej usługi przykładowej Node. js. W zależności od konfiguracji aplikacji można również użyć `http://localhost:4400` dla usługi Ripple lub innego adresu URL.
8. W górnej części strony kliknij pozycję **Odczytaj/Zapisz**, a następnie kliknij pozycję **Umieść** , aby zapisać aktualizacje.

Należy również dodać te same adresy URL sprzężenia zwrotnego do ustawień CORS dozwolonych:

1. Przejdź z powrotem do [Azure Portal].
2. Przejdź do zaplecza aplikacji mobilnej.
3. Kliknij pozycję **CORS** w menu **interfejsu API** .
4. Wprowadź każdy adres URL w polu tekstowym puste **dozwolone źródła** .  Zostanie utworzone nowe pole tekstowe.
5. Kliknij przycisk **Zapisz**

Po aktualizacji zaplecza będziesz mieć możliwość użycia nowych adresów URL sprzężenia zwrotnego w aplikacji.

<!-- URLs. -->
[Szybki start Mobile Apps platformy Azure]: app-service-mobile-cordova-get-started.md
[Wprowadzenie do uwierzytelniania]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure Portal]: https://portal.azure.com/
[Zestaw JavaScript SDK dla platformy Azure Mobile Apps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
