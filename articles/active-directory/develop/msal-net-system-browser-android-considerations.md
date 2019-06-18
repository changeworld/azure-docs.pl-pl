---
title: Zagadnienia dotyczące platformy Xamarin Android (Biblioteka Microsoft Authentication Library dla platformy .NET) | Azure
description: Dowiedz się więcej o tu konkretne uwagi przy użyciu platformy Xamarin Android Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c57feb33967732481d78e0ddaba5e90f4f82f327
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544439"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Zagadnienia specyficzne dla platformy Xamarin Android przy użyciu platformy MSAL.NET
W tym artykule omówiono tu konkretne uwagi, korzystając z przeglądarki systemu na Xamarin dla systemu Android i Biblioteka Microsoft Authentication Library dla platformy .NET (platformy MSAL.NET).

Począwszy od platformy MSAL.NET 2.4.0-preview platformy MSAL.NET obsługuje przeglądarek innych niż Chrome i nie wymaga już Chrome można zainstalować na urządzeniu z systemem Android do uwierzytelniania.

Zaleca się użyć przeglądarki, które obsługują niestandardowe karty, takie jak te:

| Przeglądarki z obsługą niestandardowych kart | Nazwa pakietu |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Brave | com.brave.browser|

Oprócz przeglądarki, z obsługą niestandardowych kart, w oparciu o Nasze testy kilku przeglądarkach, które nie obsługują niestandardowych kart również będzie działać w przypadku uwierzytelniania: Opera, Opera Mini InBrowser i Maxthon. Aby uzyskać więcej informacji, przeczytaj [tabeli wyników testów](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Znane problemy

- Jeśli użytkownik ma przeglądarka nie jest włączone na urządzeniu, spowoduje zgłoszenie platformy MSAL.NET `AndroidActivityNotFound` wyjątku. 
  - **Środki zaradcze**: Informuje użytkownika powinien pozwalający przeglądarce (najlepiej jeden z obsługą niestandardowych kart) na urządzeniu.

- W przypadku niepowodzenia uwierzytelniania (np.) Uwierzytelnianie uruchomieniu DuckDuckGo), zwróci platformy MSAL.NET `AuthenticationCanceled MsalClientException`. 
  - **Główny Problem**: Przeglądarka z obsługą niestandardowych kart nie zostało włączone na urządzeniu. Uwierzytelnianie jest uruchomiona z innych przeglądarek, który nie był w stanie zakończyć uwierzytelnianie. 
  - **Środki zaradcze**: Informować użytkownika, że powinni oni zainstalować przeglądarkę (najlepiej jeden z obsługą niestandardowej karty) na urządzeniu.

## <a name="devices-and-browsers-tested"></a>Urządzenia i przeglądarki przetestowane
Poniższa tabela zawiera listę urządzeń i przeglądarek, które zostały przetestowane.

| | Przeglądarka&ast;     |  Wynik  | 
| ------------- |:-------------:|:-----:|
| Huawei jeden + / | Chrome&ast; | — Dostęp próbny|
| Huawei jeden + / | Edge&ast; | — Dostęp próbny|
| Huawei jeden + / | Firefox&ast; | — Dostęp próbny|
| Huawei jeden + / | Brave&ast; | — Dostęp próbny|
| Jeden + | Ecosia&ast; | — Dostęp próbny|
| Jeden + | Hodowca&ast; | — Dostęp próbny|
| Huawei jeden + / | Opera | — Dostęp próbny|
| Huawei | OperaMini | — Dostęp próbny|
| Huawei jeden + / | InBrowser | — Dostęp próbny|
| Jeden + | Maxthon | — Dostęp próbny|
| Huawei jeden + / | DuckDuckGo | Użytkownik anulował uwierzytelnianie|
| Huawei jeden + / | UC przeglądarki | Użytkownik anulował uwierzytelnianie|
| Jeden + | Delfinów | Użytkownik anulował uwierzytelnianie|
| Jeden + | Przeglądarka CM | Użytkownik anulował uwierzytelnianie|
| Huawei jeden + / | Brak zainstalowanych | AndroidActivityNotFound ex|

&ast; Obsługa niestandardowych kart

## <a name="next-steps"></a>Kolejne kroki
Dla kodu fragmentów i dodatkowe informacje na temat korzystania z przeglądarki systemu za pomocą platformy Xamarin Android, przeczytaj ten [przewodnik](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  