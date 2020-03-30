---
title: Wybierz platformę rozwoju frontonu do tworzenia aplikacji klienckich za pomocą programu Visual Studio i usług platformy Azure
description: Dowiedz się więcej o obsługiwanych językach natywnych i międzyplatformowych do tworzenia aplikacji klienckich.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 714916c755ce50f6e596cee4be85ceb0b142c789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240863"
---
# <a name="choose-mobile-development-frameworks"></a>Wybierz mobilne struktury programistyczne
Deweloperzy mogą używać technologii po stronie klienta do tworzenia aplikacji mobilnych samodzielnie przy użyciu określonych struktur i wzorców dla podejścia między platformami. Na podstawie czynników decyzyjnych deweloperzy mogą tworzyć:
- Natywne aplikacje jednoplatformowe przy użyciu języków takich jak Objective C i Java
- Aplikacje wieloplatformowe przy użyciu platformy Xamarin, .NET i C #
- Zastosowania hybrydowe przy użyciu Cordova i jej wariantów

## <a name="native-platforms"></a>Platformy natywne
Tworzenie aplikacji natywnej wymaga języków programowania specyficznych dla platformy, zestawów SDK, środowisk programistycznych i innych narzędzi dostarczanych przez dostawców systemu operacyjnego.

### <a name="ios"></a>iOS
Stworzony i opracowany przez Apple, iOS służy do tworzenia aplikacji na urządzeniach Apple, a mianowicie iPhone i iPad.

- **Języki programowania**: Objective-C, Swift
- **IDE**: Xcode
- **SDK**: SDK iOS

### <a name="android"></a>Android
Zaprojektowany przez Google i najpopularniejszy system operacyjny na świecie, Android służy do tworzenia aplikacji, które mogą działać na wielu smartfonach i tabletach.

- **Język programowania**: Java, Kotlin 
- **IDE**: Android Studio i Android narzędzia programistyczne 
- **SDK**: Android SDK

### <a name="windows"></a>Windows
- **Język programowania**: C #
- **IDE**: Visual Studio, kod programu Visual Studio
- **SDK**: Windows SDK

#### <a name="pros"></a>Zalety
- Dobre wrażenia użytkownika
- Responsywne aplikacje o wysokiej wydajności i możliwości interfejsu z natywnymi bibliotekami
- Wysoce bezpieczne aplikacje

#### <a name="cons"></a>Wady
- Aplikacja działa tylko na jednej platformie
- Tworzenie aplikacji bardziej zasobochłonnych dla deweloperów i kosztownych
- Ponowne użycie dolnego kodu

## <a name="cross-platforms-and-hybrid-applications"></a>Międzyplatformowe i hybrydowe
Aplikacje wieloplatformowe umożliwiają jednorazowe pisanie natywnych aplikacji mobilnych, udostępnianie kodu i uruchamianie ich w systemach iOS, Android i Windows.

### <a name="xamarin"></a>Xamarin
Należący do firmy Microsoft platforma [Xamarin](https://visualstudio.microsoft.com/xamarin/) służy do tworzenia niezawodnych aplikacji mobilnych między platformami w języku C#. Platforma Xamarin ma bibliotekę klas i środowisko wykonawcze, które działa na wielu platformach, takich jak iOS, Android i Windows. Kompiluje również natywne (nieinterpretowane) aplikacje, które zapewniają wysoką wydajność. Xamarin łączy w sobie wszystkie możliwości rodzimych platform i dodaje szereg zaawansowanych funkcji własnych.

- **Język programowania**: C #
- **IDE**: Visual Studio w systemie Windows lub Mac

### <a name="react-native"></a>Reaguj natywne
Wydany przez Facebooka w 2015 [r., React Native](https://facebook.github.io/react-native/) to [struktura JavaScript typu open source](https://github.com/facebook/react-native) do pisania prawdziwych, natywnie renderujących aplikacje mobilne dla systemów iOS i Android. Jest on oparty na React, facebookowej bibliotece JavaScript do tworzenia interfejsów użytkownika. Zamiast kierować reklamy na przeglądarkę, jest ona przeznaczona dla platform mobilnych. React Native używa składników natywnych zamiast składników sieci web jako bloków konstrukcyjnych.
 
- **Język programowania**: JavaScript
- **IDE**: Kod programu Visual Studio

### <a name="unity"></a>Unity
 Unity to silnik zoptymalizowany do tworzenia gier. Można go używać do tworzenia wysokiej jakości aplikacji 2D lub 3D z języka C# dla platform, takich jak Windows, iOS, Android i Xbox.

### <a name="cordova"></a>Cordova
Cordova umożliwia tworzenie aplikacji hybrydowych przy użyciu narzędzi programu Visual Studio dla Apache Cordova lub Visual Studio Code z rozszerzeniami dla Cordova. Dzięki podejściu hybrydowemu można udostępniać składniki witrynom sieci Web i ponownie używać aplikacji opartych na serwerze sieci Web z hostowanymi metodami aplikacji internetowych opartymi na cordova.

#### <a name="pros"></a>Zalety
- Zwiększona użyteczność kodu przez utworzenie jednej bazy kodu dla wielu platform
- Zaspokajanie potrzeb szerszego grona odbiorców na wielu platformach
- Dramatyczne skrócenie czasu rozwoju
- Łatwe uruchamianie i aktualizowanie

#### <a name="cons"></a>Wady
- Niższa wydajność
- Brak elastyczności
- Każda platforma ma unikalny zestaw funkcji i funkcji, dzięki którym natywna aplikacja jest bardziej kreatywna
- Wydłużyć czas projektowania interfejsu użytkownika
- Ograniczenie narzędzia
