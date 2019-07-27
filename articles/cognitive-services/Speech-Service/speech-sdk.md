---
title: Informacje o zestawie SDK mowy — Speech Service
titleSuffix: Azure Cognitive Services
description: Zestaw Speech Software Development Kit (SDK) zapewnia aplikacjom natywny dostęp do funkcji usługi mowy, ułatwiając opracowywanie oprogramowania. Ten artykuł zawiera dodatkowe szczegóły dotyczące zestawu SDK dla Windows, Linux i Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: c5e12812a4305493be2cdc234946796b21dd26d8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558940"
---
# <a name="about-the-speech-sdk"></a>Informacje o zestawie SDK mowy

Zestaw Speech Software Development Kit (SDK) umożliwia aplikacjom dostęp do funkcji usługi mowy, ułatwiając tworzenie oprogramowania z obsługą mowy. Obecnie zestawy SDK zapewniają dostęp do funkcji **zamiany mowy na tekst**, zamiany **tekstu na mowę**, **tłumaczenia mowy**, **rozpoznawania**zamierzeń i **bezpośredniego kanału mowy w programie bot Framework**. Ogólne omówienie możliwości i obsługiwanych platform można znaleźć na [stronie wprowadzanie](https://aka.ms/csspeech)dokumentacji.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Pobierz zestaw SDK

### <a name="windows"></a>Windows

Dla Windows firma Microsoft obsługuje następujące języki:

* C#(Platformy UWP i .NET) C++: Możesz odwołać się do najnowszej wersji naszego pakietu NuGet zestawu mowy SDK i korzystać z niej. Ten pakiet zawiera biblioteki klienckie 32-bitowych i 64-bitowych i bibliotek zarządzanych (.NET). Zestaw SDK można zainstalować za pomocą pakietu NuGet w programie Visual Studio. Wyszukaj **Microsoft.CognitiveServices.Speech**.

* Java: Możesz odwołać się do najnowszej wersji naszego pakietu Maven zestawu mowy SDK, który obsługuje tylko system Windows x64. W projekcie Maven Dodaj `https://csspeechstorage.blob.core.windows.net/maven/` jako dodatkowe repozytorium i odwołania `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0` jako zależność.

### <a name="linux"></a>Linux

> [!NOTE]
> Obecnie obsługujemy tylko Ubuntu 16,04, Ubuntu 18,04 i Debian 9 na komputerze (x86 lub x64 na potrzeby C++ programowania i x64 dla programów .NET Core, Java i Python).

Upewnij się, że wymagane biblioteki są zainstalowane, uruchamiając następujące polecenia powłoki:

W systemie Ubuntu:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

W programie Debian 9:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

* C#: Możesz odwołać się do najnowszej wersji naszego pakietu NuGet zestawu mowy SDK i korzystać z niej. Aby odwołać się do zestawu SDK, należy dodać następujące odwołanie pakietu do projektu:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.6.0" />
  ```

* Java: Możesz odwołać się do najnowszej wersji naszego pakietu Maven zestawu mowy SDK. W projekcie Maven Dodaj `https://csspeechstorage.blob.core.windows.net/maven/` jako dodatkowe repozytorium i odwołania `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0` jako zależność.

* C++: Pobierz zestaw SDK jako [pakiet tar](https://aka.ms/csspeech/linuxbinary) i rozpakuj pliki w wybranym katalogu. W poniższej tabeli przedstawiono strukturę folderu zestawu SDK:

  |Ścieżka|Opis|
  |-|-|
  |`license.md`|Licencja|
  |`ThirdPartyNotices.md`|Uwagi dotyczące innych firm|
  |`include`|Pliki nagłówkowe dla języków C i C++|
  |`lib/x64`|X64 natywnej biblioteki do łączenia się z aplikacją|
  |`lib/x86`|X86 natywnej biblioteki do łączenia się z aplikacją|

  Aby utworzyć aplikację, skopiuj lub Przenieś wymagane pliki binarne (i biblioteki) do środowiska deweloperskiego. Włączyć je zgodnie z potrzebami w procesie kompilacji.

### <a name="android"></a>Android

Zestaw Java SDK dla systemu Android jest spakowany jako [AAR (Biblioteka systemu Android)](https://developer.android.com/studio/projects/android-library), który obejmuje niezbędne biblioteki i wymagane uprawnienia systemu Android. Znajduje się w repozytorium narzędzia Maven w `https://csspeechstorage.blob.core.windows.net/maven/` jako pakiet `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0`.

Korzystanie z pakietu z projektu w programie Android Studio, należy wprowadzić następujące zmiany:

* W pliku build.gradle na poziomie projektu, Dodaj następujący kod do `repository` sekcji:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* W pliku build.gradle poziom modułu, Dodaj następujący kod do `dependencies` sekcji:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.6.0'
  ```

Zestaw Java SDK jest również częścią [zestawu Speech Devices SDK](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
