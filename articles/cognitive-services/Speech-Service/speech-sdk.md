---
title: Temat mowy usług Cognitive Services SDK
description: Przegląd zestawy SDK dostępne dla usługi mowy.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 08/16/2018
ms.author: v-jerkin
ms.openlocfilehash: c26aeb1d29c3b2c8b5b43d1a1face818295e9d2f
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122086"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Temat mowy usług Cognitive Services SDK

Cognitive Services mowy Software Development Kit (SDK) udostępnia aplikacje natywne funkcje usługi rozpoznawania mowy, ułatwia tworzenie oprogramowania. Obecnie, zestaw SDK zapewnia dostęp do **zamiana mowy na tekst**, **tłumaczenia mowy**, i **rozpoznawanie intencji**.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Pobierz zestaw SDK

### <a name="windows"></a>Windows

Dla Windows firma Microsoft obsługuje następujące języki:

* C# C++ (platformy UWP i programowi .NET): można odwołać się i używać najnowszej wersji naszych pakietu NuGet zestawu SDK rozpoznawania mowy.
  Pakiet zawiera biblioteki klienckie 32-bitowych i 64-bitowych, a także zarządzane biblioteki (.NET).
  Zestaw SDK można zainstalować w programie Visual Studio za pomocą narzędzia NuGet; po prostu wyszukać `Microsoft.CognitiveServices.Speech`.

* Java: Możesz odwołać się i używać najnowszej wersji naszych pakietu Maven zestaw SDK rozpoznawania mowy, który obsługuje tylko Windows x64.
  W projekcie Maven Dodaj `https://csspeechstorage.blob.core.windows.net/maven/` jako dodatkowe repozytorium, a odwołanie `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0` jako zależność. 

### <a name="linux"></a>Linux

> [!NOTE]
> Obecnie obsługiwany jest tylko Ubuntu 16.04 na komputerze (x86 lub x64 dla programowania w języku C++, x64 dla platformy .NET Core i Java).

Upewnij się, że masz wymagany kompilator i biblioteki zainstalowane, uruchamiając następujące polecenia powłoki:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

* C#: Można odwołać się i używać najnowszej wersji naszych pakietu NuGet zestawu SDK rozpoznawania mowy.
  Aby odwołać się do zestawu SDK, należy dodać następujące odwołanie do pakietu do projektu:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="0.6.0" />
  ```

* Java: Możesz odwołać się i używać najnowszej wersji naszych pakietu Maven zestaw SDK rozpoznawania mowy.
  W projekcie Maven Dodaj `https://csspeechstorage.blob.core.windows.net/maven/` jako dodatkowe repozytorium, a odwołanie `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0` jako zależność. 

* C++: Pobierz zestaw SDK jako [pakietu tar](https://aka.ms/csspeech/linuxbinary) i Rozpakowywanie plików do wybranego katalogu. W poniższej tabeli przedstawiono strukturę folderu zestawu SDK.

  |Ścieżka|Opis|
  |-|-|
  |`license.md`|Licencja|
  |`ThirdPartyNotices.md`|Uwagi dotyczące innych firm|
  |`include`|Pliki nagłówkowe dla języków C i C++|
  |`lib/x64`|X64 natywnej biblioteki do łączenia się z aplikacją|
  |`lib/x86`|X86 natywnej biblioteki do łączenia się z aplikacją|

  Do tworzenia aplikacji, skopiuj lub Przenieś wymagane pliki binarne (i biblioteki) do środowiska deweloperskiego i włączyć je zgodnie z potrzebami w procesie kompilacji.

### <a name="android"></a>Android

Zestaw Java SDK dla systemu Android jest spakowany jako [AAR (biblioteka systemu Android)](https://developer.android.com/studio/projects/android-library), która obejmuje niezbędnych bibliotek, a także wymagane uprawnienia dla systemu Android dotyczące korzystania z niego.
Znajduje się w repozytorium narzędzia Maven w `https://csspeechstorage.blob.core.windows.net/maven/` jako pakiet `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0`.
Korzystanie z pakietu z projektu w programie Android Studio, wprowadź następujące zmiany:

* Na poziomie projektu `build.gradle` plików, Dodaj następujący kod do `repository` sekcji:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* Na poziomie modułu `build.gradle` plików, Dodaj następujący kod do `dependencies` sekcji:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:0.6.0'
  ```

Zestaw Java SDK jest również częścią [zestawu Speech Devices SDK](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
