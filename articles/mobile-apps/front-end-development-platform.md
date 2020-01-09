---
title: Wybieranie platformy deweloperskiej frontonu do tworzenia aplikacji klienckich za pomocą programu Visual Studio i usług platformy Azure
description: Poznaj obsługiwane języki natywne i Międzyplatformowe umożliwiające tworzenie aplikacji klienckich.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: c55c9c36d84c8545f4192e1c9a65d8af0c580396
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453155"
---
# <a name="choose-mobile-development-frameworks"></a>Wybierz platformy deweloperskie dla urządzeń przenośnych
Deweloperzy mogą używać technologii po stronie klienta do kompilowania aplikacji mobilnych, korzystając z określonych struktur i wzorców dla podejścia międzyplatformowego. W oparciu o czynniki decyzyjne deweloperzy mogą tworzyć:
- Natywne aplikacje pojedynczej platformy przy użyciu języków, takich jak cele C i Java
- Aplikacje dla wielu platform za pomocą platformy Xamarin, .NET iC#
- Aplikacje hybrydowe przy użyciu oprogramowania Cordova i jego wariantów

## <a name="native-platforms"></a>Platformy natywne
Tworzenie aplikacji natywnych wymaga specyficznych dla platformy języków programowania, zestawów SDK, środowisk programistycznych i innych narzędzi oferowanych przez dostawców systemu operacyjnego.

### <a name="ios"></a>iOS
Utworzona i opracowana przez firmę Apple system iOS służy do kompilowania aplikacji na urządzeniach firmy Apple, a mianowicie dla telefonu iPhone i tabletu iPad.

- **Języki programowania**: obiektyw-C, SWIFT
- **IDE**: Xcode
- **Zestaw SDK**: zestaw SDK systemu iOS

### <a name="android"></a>Android
SYSTEM Android jest przeznaczony do tworzenia aplikacji, które mogą być uruchamiane na różnych urządzeniach smartphone i tabletach.

- **Język programowania**: Java, Kotlin 
- **IDE**: Android Studio i narzędzia deweloperskie dla systemu Android 
- **Zestaw SDK**: Android SDK

### <a name="windows"></a>Windows
- **Język programowania**:C#
- **IDE**: Visual Studio, Visual Studio Code
- **Zestaw SDK**: Windows SDK

#### <a name="pros"></a>Specjaliści
- Dobre środowisko użytkownika
- Reagowanie aplikacji o wysokiej wydajności i możliwości interfejsów z natywnymi bibliotekami
- Wysoce bezpieczne aplikacje

#### <a name="cons"></a>Wady
- Aplikacja działa tylko na jednej platformie
- Większa intensywność zasobów dla deweloperów i kosztowna kompilacja aplikacji
- Obniż użycie kodu

## <a name="cross-platforms-and-hybrid-applications"></a>Aplikacje Międzyplatformowe i hybrydowe
Aplikacje dla wielu platform umożliwiają jednoczesne pisanie natywnych aplikacji mobilnych, udostępnianie kodu i uruchamianie ich w systemach iOS, Android i Windows.

### <a name="xamarin"></a>Xamarin
Należące do firmy Microsoft, Platforma [Xamarin](https://visualstudio.microsoft.com/xamarin/) służy do tworzenia niezawodnych aplikacji mobilnych międzyplatformowych w programie C#. Platforma Xamarin ma bibliotekę klas i środowisko uruchomieniowe działające na wielu platformach, takich jak iOS, Android i Windows. Kompiluje także natywne (nieinterpretowane) aplikacje, które zapewniają wysoką wydajność. Platforma Xamarin łączy wszystkie możliwości natywnych platform i dodaje wiele zaawansowanych funkcji.

- **Język programowania**:C#
- **IDE**: program Visual Studio w systemie Windows lub Mac

### <a name="react-native"></a>React Native
W serwisie Facebook w systemie 2015 system [reaguje na natywną](https://facebook.github.io/react-native/) platformę [JavaScript do](https://github.com/facebook/react-native) pisania w rzeczywistości natywnego renderowania aplikacji mobilnych dla systemów iOS i Android. Jest on oparty na reakcji, biblioteki języka JavaScript w serwisie Facebook do tworzenia interfejsów użytkownika. Zamiast wskazywać przeglądarkę, jest przeznaczona dla platform mobilnych. Reagowanie natywnie używa składników natywnych zamiast składników sieci Web jako bloków konstrukcyjnych.
 
- **Język programowania**: JavaScript
- **IDE**: Visual Studio Code

### <a name="unity"></a>Unity
 Aparat Unity jest zoptymalizowany pod kątem tworzenia gier. Za jego pomocą można wystawić wysokiej jakości aplikacje 2D lub 3W w C# przypadku platform takich jak Windows, iOS, Android i Xbox.

### <a name="cordova"></a>Cordova
Program Cordova umożliwia tworzenie hybrydowych aplikacji przy użyciu Visual Studio Tools Apache Cordova lub Visual Studio Code z rozszerzeniami dla oprogramowania Cordova. Korzystając z podejścia hybrydowego, można udostępniać składniki witrynom sieci Web i ponownie używać aplikacji opartych na serwerze internetowym z podejściem hostowanych aplikacji sieci Web opartym na oprogramowaniu Cordova.

#### <a name="pros"></a>Specjaliści
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
