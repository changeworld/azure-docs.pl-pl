---
title: Agenci autoryzacji i jak je włączyć | Azure
description: Dowiedz się więcej o różnych agentach autoryzacji, z których biblioteka uwierzytelniania Firmy Microsoft (MSAL) umożliwia korzystanie z aplikacji dla systemu Android i jak je włączyć.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: marsma
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.openlocfilehash: 4f1b3fc5b60069cfa47d437e4341ded141204418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085338"
---
# <a name="authorization-agents-android"></a>Agenci autoryzacji (Android)

W tym artykule opisano różnych agentów autoryzacji, których biblioteka uwierzytelniania Firmy Microsoft (MSAL) umożliwia aplikacji i jak je włączyć.

Wybór określonej strategii dla agentów autoryzacji jest opcjonalny i reprezentuje dodatkowe funkcje, które aplikacje można dostosować. Większość aplikacji będzie używać ustawień domyślnych MSAL (zobacz [Opis pliku konfiguracyjnego MSAL systemu Android,](msal-configuration.md) aby wyświetlić różne ustawienia domyślne).

MSAL obsługuje autoryzację za pomocą przeglądarki systemowej `WebView`lub systemowej.  Poniższa ilustracja przedstawia, `WebView`jak wygląda za pomocą , lub przeglądarki systemowej z CustomTabs lub bez CustomTabs:

![Przykłady logowania MSAL](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Implikacje logowania jednokrotnego

Domyślnie aplikacje zintegrowane z msal używać przeglądarki systemu karty niestandardowe do autoryzacji. W przeciwieństwie do webviews, karty niestandardowe współużytkują plik cookie jar z domyślną przeglądarką systemową, umożliwiając mniejszą liczbę logowania z aplikacjami sieci Web lub innymi natywnymi aplikacjami zintegrowanymi z kartami niestandardowymi.

Jeśli aplikacja używa `WebView` strategii bez integrowania pomocy technicznej microsoft authenticator lub portal firmy do swojej aplikacji, użytkownicy nie będą mieli logowania jednokrotnego (Logowanie jednokrotne) na urządzeniu lub między aplikacjami natywnymi i aplikacjami sieci Web.

Jeśli aplikacja używa usługi MSAL z pomocą techniczną usługi Microsoft Authenticator lub portal firmy, użytkownicy mogą mieć środowisko logowania jednokrotnego w aplikacjach, jeśli użytkownik ma aktywne logowanie za pomocą jednej z aplikacji.

## <a name="webview"></a>WebView

Aby użyć webview w aplikacji, umieść następujący wiersz w konfiguracji aplikacji JSON, który jest przekazywany do usługi MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Podczas korzystania z `WebView`aplikacji użytkownik loguje się bezpośrednio do aplikacji. Tokeny są przechowywane wewnątrz piaskownicy aplikacji i nie są dostępne poza plikiem cookie aplikacji. W rezultacie użytkownik nie może mieć środowisko samouszeńców w aplikacjach, chyba że aplikacje integrują się z authenticatorem lub portalem firmy.

Jednak `WebView` zapewnia możliwość dostosowania wyglądu i działanie dla interfejsu użytkownika logowania. Zobacz [Android WebViews](https://developer.android.com/reference/android/webkit/WebView) aby uzyskać więcej informacji na temat tego dostosowania.

## <a name="default-browser-plus-custom-tabs"></a>Domyślna przeglądarka plus karty niestandardowe

Domyślnie msal używa przeglądarki i [strategii niestandardowych kart.](https://developer.chrome.com/multidevice/android/customtabs) Można jawnie wskazać tę strategię, aby `DEFAULT` zapobiec zmianom w przyszłych wersjach przy użyciu następującej konfiguracji JSON w pliku konfiguracji niestandardowej:

```json
"authorization_user_agent" : "BROWSER"
```

Użyj tej metody, aby zapewnić środowisko rejestracji rejestracji w tym przeglądarce za pośrednictwem urządzenia. MSAL używa udostępnionego pliku cookie jar, który umożliwia innym aplikacjom macierzystym lub aplikacjom internetowym osiągnięcie rejestracji jednodzielniczej na urządzeniu przy użyciu pliku cookie persist session ustawionego przez msal.

## <a name="browser-selection-heuristic"></a>Heurystyczny wybór przeglądarki

Ponieważ jest to niemożliwe dla MSAL, aby określić dokładny pakiet przeglądarki do użycia na każdym z szerokiej tablicy telefonów z systemem Android, MSAL implementuje heurystykę wyboru przeglądarki, która stara się zapewnić najlepsze sso krzyżowe.

MSAL pobiera pełną listę przeglądarek zainstalowanych na urządzeniu, aby wybrać przeglądarkę, której chcesz użyć. Lista jest w kolejności zwracana przez menedżera pakietów, który pośrednio odzwierciedla preferencje użytkownika. Na przykład domyślna przeglądarka, jeśli jest ustawiona, jest pierwszym wpisem na liście. _Pierwsza_ przeglądarka na liście zostanie wybrana niezależnie od tego, czy obsługuje niestandardowe karty. Jeśli przeglądarka obsługuje karty niestandardowe, msal uruchomi kartę niestandardową. `WebView` Aby dowiedzieć się [więcej, zobacz Karty niestandardowe w Systemie Android.](https://developer.chrome.com/multidevice/android/customtabs)

Jeśli na urządzeniu nie ma pakietów przeglądarki, msal `WebView`używa w aplikacji .

Kolejność przeglądarek na liście przeglądarek jest określana przez system operacyjny. Jest w kolejności od najbardziej preferowanych do najmniej. Jeśli domyślne ustawienie urządzenia nie zostanie zmienione, dla każdego logowania powinna zostać uruchomiona ta sama przeglądarka, aby zapewnić środowisko logowania jednośliowego.

> [!NOTE]
> MSAL nie zawsze preferuje Chrome, jeśli inna przeglądarka jest ustawiona jako domyślna. Na przykład na urządzeniu, na którym jest zainstalowana zarówno Chrome, jak i inna przeglądarka, msal użyje przeglądarki ustawionej przez użytkownika jako domyślnej.

### <a name="tested-browsers"></a>Przetestowane przeglądarki

Przetestowano następujące przeglądarki, aby sprawdzić, czy poprawnie `"redirect_uri"` przekierowywały do określonego w pliku konfiguracyjnym:

| | Wbudowana przeglądarka | Chrome | Opera  | Microsoft Edge | Przeglądarka UC | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | Przekazać | Przekazać |nie dotyczy |nie dotyczy |nie dotyczy |nie dotyczy |
| Samsung S7 (API 25) | przepustka* | Przekazać | Przekazać | Przekazać | Nie |Przekazać |
| Huawei (API 26) |przepustka** | Przekazać | Nie | Przekazać | Przekazać |Przekazać |
| Vivo (API 26) |Przekazać|Przekazać|Przekazać|Przekazać|Przekazać|Nie|
| Pixel 2 (API 26) |Przekazać | Przekazać | Przekazać | Przekazać | Nie |Przekazać |
| Oppo | Przekazać | nie dotyczy*** |nie dotyczy  |nie dotyczy |nie dotyczy | nie dotyczy|
| OnePlus (API 25) |Przekazać | Przekazać | Przekazać | Przekazać | Nie |Przekazać |
| Nexus (API 28) |Przekazać | Przekazać | Przekazać | Przekazać | Nie |Przekazać |
|MI | Przekazać | Przekazać | Przekazać | Przekazać | Nie |Przekazać |

* Wbudowana przeglądarka Samsunga to Internet Samsung.  
**Wbudowana przeglądarka Huawei to Przeglądarka Huawei.  
Domyślnej przeglądarki nie można zmienić wewnątrz ustawienia urządzenia Oppo.
