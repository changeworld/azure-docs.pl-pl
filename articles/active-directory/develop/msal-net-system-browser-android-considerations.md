---
title: Uwagi dotyczące platformy Xamarin Android (Microsoft Authentication Library for .NET) | Azure
description: Informacje o określonych kwestiach dotyczących korzystania z platformy Xamarin Android z biblioteką uwierzytelniania firmy Microsoft dla programu .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b0c810097913e896027245b15600ed75aabcd25
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532576"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Zagadnienia specyficzne dla platformy Xamarin Android z MSAL.NET
W tym artykule omówiono określone zagadnienia dotyczące korzystania z przeglądarki systemowej w programie Xamarin Android z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).

Począwszy od MSAL.NET 2.4.0 — wersja zapoznawcza, MSAL.NET obsługuje przeglądarki inne niż Chrome i nie wymagają już, aby program Chrome był instalowany na urządzeniu z systemem Android w celu uwierzytelnienia.

Zalecamy korzystanie z przeglądarek, które obsługują niestandardowe karty, takie jak:

| Obsługa przeglądarek z kartami niestandardowymi | Nazwa pakietu |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com. Microsoft. emmx|
|Firefox | org. mozilla. Firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com. kiwibrowser. Browser|
|Brave | com. Brave. Browser|

Oprócz obsługi przeglądarek z kartami niestandardowymi w oparciu o nasze testy, niektóre przeglądarki, które nie obsługują kart niestandardowych, również będą działały w celu uwierzytelnienia: Opera, Opera, inbrowser i Maxthon. Aby uzyskać więcej informacji, Przeczytaj [tabelę dla wyników testu](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Znane problemy

- Jeśli użytkownik nie ma włączonej przeglądarki na urządzeniu, MSAL.NET zgłosi `AndroidActivityNotFound` wyjątek. 
  - Środki zaradcze: Poinformuj użytkownika o tym, że powinni włączyć przeglądarkę (najlepiej z obsługą kart niestandardowych) na urządzeniu.

- W przypadku niepowodzenia uwierzytelniania (np. Uwierzytelnianie jest uruchamiane z DuckDuckGo), MSAL.NET zwróci `AuthenticationCanceled MsalClientException`. 
  - **Problem z głównym**: Nie włączono obsługi przeglądarki z kartami niestandardowymi na urządzeniu. Uwierzytelnianie zostało uruchomione przy użyciu alternatywnej przeglądarki, która nie mogła ukończyć uwierzytelniania. 
  - Środki zaradcze: Zawiadom użytkownika, że powinni zainstalować przeglądarkę (najlepiej ją z obsługą kart niestandardowych) na urządzeniu.

## <a name="devices-and-browsers-tested"></a>Przetestowane urządzenia i przeglądarki
W poniższej tabeli wymieniono urządzenia i przeglądarki, które zostały przetestowane.

| | Przeglądarka&ast;     |  Wynik  | 
| ------------- |:-------------:|:-----:|
| Huawei/jeden + | Własnego&ast; | Powodzenie|
| Huawei/jeden + | Edge&ast; | Powodzenie|
| Huawei/jeden + | Firefox&ast; | Powodzenie|
| Huawei/jeden + | Brave&ast; | Powodzenie|
| Jeden + | Ecosia&ast; | Powodzenie|
| Jeden + | Kiwi&ast; | Powodzenie|
| Huawei/jeden + | Opera | Powodzenie|
| Huawei | OperaMini | Powodzenie|
| Huawei/jeden + | Brak przeglądarki | Powodzenie|
| Jeden + | Maxthon | Powodzenie|
| Huawei/jeden + | DuckDuckGo | Użytkownik anulował uwierzytelnianie|
| Huawei/jeden + | Przeglądarka UC | Użytkownik anulował uwierzytelnianie|
| Jeden + | Tuńczyk | Użytkownik anulował uwierzytelnianie|
| Jeden + | Przeglądarka CM | Użytkownik anulował uwierzytelnianie|
| Huawei/jeden + | Brak zainstalowanych | AndroidActivityNotFound ex|

&ast;Obsługuje karty niestandardowe

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z fragmentami kodu i dodatkowymi informacjami na temat korzystania z przeglądarki systemowej z systemem Xamarin Android, przeczytaj ten [Przewodnik](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  