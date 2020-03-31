---
title: Xamarin Android zagadnienia przeglądarki systemu Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o zagadnieniach dotyczących korzystania z przeglądarek systemowych w systemie Xamarin w systemie Android z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad26a4d619a7984f08a8decc87f9339adae47cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132611"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Xamarin Android zagadnienia przeglądarki systemu za pomocą MSAL.NET

W tym artykule omówiono, co należy wziąć pod uwagę podczas korzystania z przeglądarki systemowej na platformie Xamarin Android z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).

Począwszy od MSAL.NET wersji zapoznawczej 2.4.0, MSAL.NET obsługuje przeglądarki inne niż Chrome. Nie wymaga już instalacji Chrome na urządzeniu z Androidem do uwierzytelniania.

Zaleca się korzystanie z przeglądarek obsługujących karty niestandardowe. Oto kilka przykładów tych przeglądarek:

| Przeglądarki z niestandardowymi kartami obsługują | Nazwa pakietu |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | witryna com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia (Ecosia) | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Odważny | com.brave.browser (strona główna)|

Oprócz identyfikowania przeglądarek, które oferują obsługę kart niestandardowych, nasze testy wskazują, że kilka przeglądarek, które nie obsługują kart niestandardowych również działa do uwierzytelniania. Przeglądarki te obejmują Opera, Opera Mini, InBrowser i Maxthon. 

## <a name="tested-devices-and-browsers"></a>Przetestowane urządzenia i przeglądarki
W poniższej tabeli wymieniono urządzenia i przeglądarki, które zostały przetestowane pod kątem zgodności uwierzytelniania.

| Urządzenie | Przeglądarka     |  Wynik  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome\* | Przekazać|
| Huawei/One+ | Edge\* | Przekazać|
| Huawei/One+ | Firefox\* | Przekazać|
| Huawei/One+ | Odważny\* | Przekazać|
| Jeden+ | Ecosia (Ecosia)\* | Przekazać|
| Jeden+ | Kiwi\* | Przekazać|
| Huawei/One+ | Opera | Przekazać|
| Huawei | OperaMini (OperaMini) | Przekazać|
| Huawei/One+ | InBrowser (Wbrązowie) | Przekazać|
| Jeden+ | Maxthon | Przekazać|
| Huawei/One+ | Duckduckgo | Anulowane uwierzytelnianie użytkownika|
| Huawei/One+ | Przeglądarka UC | Anulowane uwierzytelnianie użytkownika|
| Jeden+ | Dolphin | Anulowane uwierzytelnianie użytkownika|
| Jeden+ | Przeglądarka CM | Anulowane uwierzytelnianie użytkownika|
| Huawei/One+ | Brak zainstalowany | Wyjątek AndroidActivityNotFound|

\*Obsługa kart niestandardowych

## <a name="known-issues"></a>Znane problemy

Jeśli użytkownik nie ma włączonej przeglądarki na urządzeniu, MSAL.NET `AndroidActivityNotFound` zgłosić wyjątek.  
  - **Środki zaradcze:** Poproś użytkownika o włączenie przeglądarki na swoim urządzeniu. Polec przeglądarkę obsługującą karty niestandardowe.

Jeśli uwierzytelnianie nie powiedzie się (na przykład, jeśli uwierzytelnianie `AuthenticationCanceled MsalClientException`zostanie uruchomiony za pomocą DuckDuckGo), MSAL.NET zwróci . 
  - **Główny problem:** przeglądarka obsługująca niestandardowe karty nie została włączona na urządzeniu. Uwierzytelnianie uruchomione za pomocą przeglądarki, która nie mogła ukończyć uwierzytelniania. 
  - **Środki zaradcze:** Poproś użytkownika o włączenie przeglądarki na swoim urządzeniu. Polec przeglądarkę obsługującą karty niestandardowe.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji i przykłady kodu, zobacz [Wybieranie między osadzoną przeglądarką internetową a przeglądarką systemową na systemie Xamarin Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) i [embedded versus system web ui](msal-net-web-browsers.md#embedded-vs-system-web-ui).  