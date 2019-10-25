---
title: Uwagi dotyczące platformy Xamarin Android (Microsoft Authentication Library for .NET)
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: 8f5caad4b136c9ef2686cc4befc70e6720e27855
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802756"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Zagadnienia specyficzne dla platformy Xamarin Android z MSAL.NET
W tym artykule omówiono określone zagadnienia dotyczące korzystania z przeglądarki systemowej w programie Xamarin Android z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).

Począwszy od MSAL.NET 2.4.0 — wersja zapoznawcza, MSAL.NET obsługuje przeglądarki inne niż Chrome i nie wymagają już, aby program Chrome był instalowany na urządzeniu z systemem Android w celu uwierzytelnienia.

Zalecamy korzystanie z przeglądarek, które obsługują niestandardowe karty, takie jak:

| Obsługa przeglądarek z kartami niestandardowymi | Nazwa pakietu |
|------| ------- |
|Chrome | com. Android. Chrome|
|Microsoft Edge | com. Microsoft. emmx|
|Firefox | org. mozilla. Firefox|
|Ecosia | com. ecosia. Android|
|Kiwi | com. kiwibrowser. Browser|
|Brave | com. Brave. Browser|

Oprócz obsługi przeglądarek z kartami niestandardowymi w oparciu o nasze testy, niektóre przeglądarki, które nie obsługują kart niestandardowych, również będą działały w celu uwierzytelniania: Opera, Opera Mini, inbrowser i Maxthon. Aby uzyskać więcej informacji, Przeczytaj [tabelę dla wyników testu](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Znane problemy

- Jeśli na urządzeniu nie jest włączona przeglądarka, MSAL.NET wygeneruje wyjątek `AndroidActivityNotFound`. 
  - Środki **zaradcze**: Powiadom użytkownika, że powinni włączyć przeglądarkę (najlepiej ją z obsługą kart niestandardowych) na urządzeniu.

- W przypadku niepowodzenia uwierzytelniania (np. Uwierzytelnianie jest uruchamiane z DuckDuckGo), MSAL.NET zwróci `AuthenticationCanceled MsalClientException`. 
  - **Problem główny**: Obsługa przeglądarki z kartami niestandardowymi nie została włączona na urządzeniu. Uwierzytelnianie zostało uruchomione przy użyciu alternatywnej przeglądarki, która nie mogła ukończyć uwierzytelniania. 
  - Środki **zaradcze**: Powiadom użytkownika, że powinni zainstalować przeglądarkę (najlepiej ją z obsługą kart niestandardowych) na urządzeniu.

## <a name="devices-and-browsers-tested"></a>Przetestowane urządzenia i przeglądarki
W poniższej tabeli wymieniono urządzenia i przeglądarki, które zostały przetestowane.

| | &ast; przeglądarki     |  Wynik  | 
| ------------- |:-------------:|:-----:|
| Huawei/jeden + | &ast; Chrome | Chodzenia|
| Huawei/jeden + | &ast; brzegowe | Chodzenia|
| Huawei/jeden + | &ast; Firefox | Chodzenia|
| Huawei/jeden + | Brave&ast; | Chodzenia|
| Jeden + | Ecosia&ast; | Chodzenia|
| Jeden + | Kiwi&ast; | Chodzenia|
| Huawei/jeden + | Opera | Chodzenia|
| Huawei | OperaMini | Chodzenia|
| Huawei/jeden + | Brak przeglądarki | Chodzenia|
| Jeden + | Maxthon | Chodzenia|
| Huawei/jeden + | DuckDuckGo | Użytkownik anulował uwierzytelnianie|
| Huawei/jeden + | Przeglądarka UC | Użytkownik anulował uwierzytelnianie|
| Jeden + | Tuńczyk | Użytkownik anulował uwierzytelnianie|
| Jeden + | Przeglądarka CM | Użytkownik anulował uwierzytelnianie|
| Huawei/jeden + | Brak zainstalowanych | AndroidActivityNotFound ex|

&ast; obsługuje niestandardowe karty

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z fragmentami kodu i dodatkowymi informacjami na temat korzystania z przeglądarki systemowej z systemem Xamarin Android, przeczytaj ten [Przewodnik](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  