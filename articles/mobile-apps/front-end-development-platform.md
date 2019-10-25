---
title: Wybieranie platformy deweloperskiej frontonu do tworzenia aplikacji klienckich za pomocą programu Visual Studio i usług platformy Azure
description: Dowiedz się więcej o obsługiwanych językach natywnych i międzyplatformowych do kompilowania aplikacji klienckich.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 891967e74e52c1311d73cbe13fff8c95e479478c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795499"
---
# <a name="choose-mobile-development-framework"></a>Wybierz platformę tworzenia aplikacji mobilnych
Deweloperzy mogą używać technologii po stronie klienta do tworzenia aplikacji mobilnych, korzystając z określonych struktur i wzorców dla podejścia międzyplatformowego. Deweloperzy mogą tworzyć natywne (natywnie jednoplatformowe), takie jak aplikacje typu "Language-C i Java", aplikacje Międzyplatformowe korzystające C#z technologii Xamarin, .NET i) oraz hybrydowe (przy użyciu oprogramowania Cordova) i jego warianty, w zależności od ich decyzji elementy.

## <a name="native-platforms"></a>Platformy natywne
Tworzenie aplikacji natywnych wymaga języków programowania specyficznych dla platformy, zestawów SDK, środowiska programistycznego i innych narzędzi udostępnianych przez dostawców systemu operacyjnego.

### <a name="ios"></a>iOS
Utworzone i opracowane przez firmę Apple, aplikacje utworzone w systemie iOS działają na urządzeniach firmy Apple, np. iPhone i iPad.

- **Języki programowania** — cel-C, SWIFT
- **IDE** — Xcode
- **Zestaw SDK** — zestaw SDK systemu iOS

### <a name="android"></a>Android
Zaprojektowane przez firmę Google i najpopularniejszy system operacyjny na świecie, aplikacje utworzone w systemie Android mogą działać na różnych urządzeniach smartphone i tabletach.

- **Język programowania** — Java, Kotlin 
- **IDE** — Android Studio i Android narzędzia deweloperskie 
- **Zestaw SDK** — Android SDK

### <a name="windows"></a>Windows
- **Język programowania** —C#
- **IDE** — Visual Studio, Visual Studio Code
- **Zestaw SDK** — Windows SDK

#### <a name="pros"></a>Formaty
- Dobre środowisko użytkownika
- Reagowanie na aplikacje o wysokiej wydajności i możliwości interfejsów z natywnymi bibliotekami
- Wysoce bezpieczne aplikacje

#### <a name="cons"></a>Wady
- Aplikacja działa tylko na jednej platformie
- Więcej zasobów dla deweloperów i kosztowne do kompilowania aplikacji
- Obniż użycie kodu

## <a name="cross-platforms-and-hybrid-applications"></a>Aplikacje Międzyplatformowe i hybrydowe
Aplikacje dla wielu platform umożliwiają jednoczesne pisanie natywnych aplikacji mobilnych, udostępnianie kodu i uruchamianie ich w systemach iOS, Android i Windows.

### <a name="xamarin"></a>Xamarin
Należący do firmy Microsoft platforma [Xamarin](https://visualstudio.microsoft.com/xamarin/) umożliwia tworzenie niezawodnych, międzyplatformowych aplikacji C#mobilnych w systemie, z biblioteką klas i środowiskiem uruchomieniowym, które działają na wielu platformach, w tym dla systemów iOS, Android i Windows, podczas gdy nadal trwa Kompilowanie natywnych (nieinterpretowanych ) aplikacje, które są wysoce wydajne. Platforma Xamarin łączy wszystkie możliwości natywnych platform i dodaje wiele zaawansowanych funkcji.

- **Język programowania** —C#
- **IDE** — Visual Studio w systemie Windows lub Mac

### <a name="react-native"></a>React Native
Wydane przez serwis Facebook w systemie 2015, system [reaguje na środowisko macierzyste](https://facebook.github.io/react-native/) to język JavaScript w języku [Open Source](https://github.com/facebook/react-native) służący do pisania w rzeczywistości natywnego renderowania aplikacji mobilnych dla systemów iOS i Android. Jest on oparty na systemie reagowania, bibliotece języka JavaScript w serwisie Facebook do tworzenia interfejsów użytkownika, ale zamiast dla przeglądarki jest przeznaczony dla platform mobilnych. Reagowanie natywnie używa składników natywnych zamiast składników sieci Web jako bloków konstrukcyjnych.
 
- **Język programowania** — JavaScript
- **IDE** — Visual Studio Code

### <a name="unity"></a>Unity
 Unity to aparat zoptymalizowany pod kątem tworzenia gier, który umożliwia deweloperom tworzenie aplikacji z wysoką jakością 2D/3W C# przy użyciu różnych platform, w tym systemów Windows, iOS, Android i Xbox.

### <a name="cordova"></a>Cordova
Program Cordova umożliwia tworzenie aplikacji hybrydowych przy użyciu Visual Studio Tools dla oprogramowania Apache Codova lub Visual Studio Code z rozszerzeniami dla platformy Cordova. Korzystając z podejścia hybrydowego, można udostępniać składniki witrynom sieci Web i ponownie używać aplikacji opartych na serwerze internetowym z obsługiwanymi aplikacjami sieci Web opartymi na oprogramowaniu Cordova.

#### <a name="pros"></a>Formaty
- Zwiększenie użyteczności kodu przez utworzenie jednej bazy kodu dla wielu platform
- Zadbaj do szerszego grona użytkowników na wielu platformach
- Znacząca redukcja czasu projektowania
- Łatwa do uruchomienia i aktualizacji

#### <a name="cons"></a>Wady
- Niższa wydajność
- Brak elastyczności
- Każda platforma ma unikatowy zestaw funkcji i funkcji, które ułatwiają tworzenie aplikacji natywnych
- Zwiększony czas projektowania interfejsu użytkownika
- Ograniczenie narzędzi
