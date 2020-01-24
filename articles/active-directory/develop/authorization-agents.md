---
title: Agenci autoryzacji i sposoby ich włączania | Azure
description: Dowiedz się więcej o różnych agentach autoryzacji Biblioteka Microsoft Authentication Library (MSAL) umożliwia korzystanie z aplikacji systemu Android i włączanie ich.
services: active-directory
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.openlocfilehash: c2d0c8a877ba856aa7a93bfc960c44b63cbe8f9c
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698003"
---
# <a name="authorization-agents-android"></a>Agenci autoryzacji (Android)

W tym artykule opisano różnych agentów autoryzacji, które mogą być używane przez aplikację Microsoft Authentication Library (MSAL) oraz jak je włączać.

Wybór konkretnej strategii dla agentów autoryzacji jest opcjonalny i reprezentuje dodatkowe funkcje, które można dostosować. Większość aplikacji będzie używać ustawień domyślnych MSAL (zobacz [Opis pliku konfiguracji systemu Android MSAL](msal-configuration.md) , aby zobaczyć różne ustawienia domyślne).

MSAL obsługuje autoryzację przy użyciu `WebView`lub przeglądarki systemowej.  Na poniższej ilustracji przedstawiono sposób korzystania z `WebView`lub przeglądarki systemu z CustomTabs lub bez CustomTabs:

![Przykłady logowania MSAL](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Implikacje logowania jednokrotnego

Domyślnie aplikacje zintegrowane z usługą MSAL używają niestandardowych kart przeglądarki systemu do autoryzacji. W przeciwieństwie do widoków sieci Web, karty niestandardowe udostępniają plik cookie jar z domyślną przeglądarką systemową, co pozwala na mniejszą liczbę logowań z aplikacjami internetowymi lub innymi natywnymi, które zostały zintegrowane z kartami niestandardowymi.

Jeśli aplikacja korzysta z strategii `WebView` bez integrowania Microsoft Authenticator lub obsługi Portal firmy z ich aplikacjami, użytkownicy nie będą mogli korzystać z logowania jednokrotnego na urządzeniu ani między natywnymi aplikacjami i aplikacjami sieci Web.

Jeśli aplikacja używa usługi MSAL z obsługą Microsoft Authenticator lub Portal firmy, użytkownicy mogą korzystać z logowania jednokrotnego w aplikacjach, jeśli użytkownik ma aktywne Logowanie przy użyciu jednej z aplikacji.

## <a name="webview"></a>WebView

Aby użyć widoku webapp w aplikacji, należy umieścić następujący wiersz w pliku JSON konfiguracji aplikacji, który jest przesyłany do MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

W przypadku korzystania z `WebView`w aplikacji użytkownik loguje się bezpośrednio do aplikacji. Tokeny są przechowywane w piaskownicy aplikacji i nie są dostępne poza plikiem jar plików cookie aplikacji. W związku z tym użytkownik nie może korzystać z logowania jednokrotnego w aplikacjach, chyba że aplikacje integrują się z wystawcą uwierzytelnienia lub Portal firmy.

Jednak `WebView` oferuje możliwość dostosowania wyglądu i sposobu działania interfejsu użytkownika logowania. Aby dowiedzieć się więcej o tym, jak to zrobić, zobacz temat [WebViews dla systemu Android](https://developer.android.com/reference/android/webkit/WebView) .

## <a name="default-browser-plus-custom-tabs"></a>Domyślna przeglądarka i karty niestandardowe

Domyślnie MSAL używa przeglądarki i strategii [kart niestandardowych](https://developer.chrome.com/multidevice/android/customtabs) . Można jawnie wskazać tę strategię, aby zapobiec zmianom w przyszłych wersjach `DEFAULT` przy użyciu następującej konfiguracji JSON w pliku konfiguracji niestandardowej:

```json
"authorization_user_agent" : "BROWSER"
```

Użyj tej metody, aby zapewnić obsługę logowania jednokrotnego za pomocą przeglądarki urządzenia. MSAL korzysta z udostępnionego pliku cookie jar, który umożliwia innym natywnym aplikacjom lub aplikacjom sieci Web uzyskanie rejestracji jednokrotnej na urządzeniu przy użyciu plików cookie sesji utrwalania ustawionych przez MSAL.

## <a name="browser-selection-heuristic"></a>Algorytm heurystyczny wyboru przeglądarki

Ponieważ nie jest możliwe, aby MSAL określony pakiet przeglądarki był używany w każdej z szerokiej macierzy telefonów z systemem Android, MSAL implementuje algorytm heurystyczny wyboru przeglądarki, który próbuje zapewnić najlepszą obsługę logowania jednokrotnego dla wielu urządzeń.

MSAL pobiera pełną listę przeglądarek zainstalowanych na urządzeniu w celu wybrania używanej przeglądarki. Lista znajduje się w kolejności zwróconej przez Menedżera pakietów, która pośrednio odzwierciedla preferencje użytkownika. Na przykład domyślna przeglądarka, jeśli jest ustawiona, to pierwszy wpis na liście. _Pierwsza_ przeglądarka na liście zostanie wybrana bez względu na to, czy obsługuje ona karty niestandardowe. Jeśli przeglądarka obsługuje niestandardowe karty, MSAL uruchomi kartę niestandardową. karty niestandardowe mają wygląd i czują się bliżej `WebView` w aplikacji i umożliwiają podstawowe dostosowanie interfejsu użytkownika. Aby dowiedzieć się więcej, zobacz [niestandardowe karty w systemie Android](https://developer.chrome.com/multidevice/android/customtabs) .

Jeśli na urządzeniu nie ma żadnych pakietów przeglądarki, MSAL używa `WebView`w aplikacji.

Kolejność przeglądarek w przeglądarce jest określana przez system operacyjny. Jest ona w kolejności od największej do najmniej. Jeśli ustawienie domyślne urządzenia nie zostanie zmienione, należy uruchomić tę samą przeglądarkę dla każdego logowania, aby zapewnić obsługę logowania jednokrotnego.

> [!NOTE]
> MSAL nie zawsze preferuje Chrome, jeśli inna przeglądarka jest ustawiona jako domyślna. Na przykład na urządzeniu, które ma zarówno program Chrome, jak i inną wstępnie zainstalowaną przeglądarkę, MSAL będzie używać przeglądarki ustawionej jako domyślny.

### <a name="tested-browsers"></a>Przetestowane przeglądarki

Następujące przeglądarki zostały przetestowane w celu sprawdzenia, czy prawidłowo przekierują do `"redirect_uri"` określonej w pliku konfiguracyjnym:

| | Wbudowana przeglądarka | Chrome | Opera  | Microsoft Edge | Przeglądarka UC | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (interfejs API 17) | chodzenia | chodzenia |nie dotyczy |nie dotyczy |nie dotyczy |nie dotyczy |
| Samsung S7 (interfejs API 25) | chodzenia | chodzenia | chodzenia | chodzenia | fail |chodzenia |
| Huawei (interfejs API 26) |Pass * * | chodzenia | fail | chodzenia | chodzenia |chodzenia |
| Vivo (interfejs API 26) |chodzenia|chodzenia|chodzenia|chodzenia|chodzenia|fail|
| Piksel 2 (interfejs API 26) |chodzenia | chodzenia | chodzenia | chodzenia | fail |chodzenia |
| Oppo | chodzenia | nie dotyczy * * * |nie dotyczy  |nie dotyczy |nie dotyczy | nie dotyczy|
| OnePlus (interfejs API 25) |chodzenia | chodzenia | chodzenia | chodzenia | fail |chodzenia |
| Nexus (interfejs API 28) |chodzenia | chodzenia | chodzenia | chodzenia | fail |chodzenia |
|MI | chodzenia | chodzenia | chodzenia | chodzenia | fail |chodzenia |

\* Wbudowana przeglądarka Samsung to Samsung Internet.  
\* * Wbudowana przeglądarka Huawei jest przeglądarką Huawei.  
Domyślnej przeglądarki nie można zmienić w ustawieniu urządzenia OPPO.
