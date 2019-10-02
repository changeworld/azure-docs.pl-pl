---
title: Informacje o zestawie SDK mowy — Speech Service
titleSuffix: Azure Cognitive Services
description: Zestaw Speech Software Development Kit (SDK) zapewnia aplikacjom natywny dostęp do funkcji usługi mowy, ułatwiając opracowywanie oprogramowania. Ten artykuł zawiera dodatkowe szczegółowe informacje na temat zestawu SDK dla systemów Windows, Linux i Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: ee505cc78c16d85640c7e13541272f1bafe30061
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71800001"
---
# <a name="about-the-speech-sdk"></a>Informacje o zestawie SDK mowy

Zestaw Speech Software Development Kit (SDK) umożliwia aplikacjom dostęp do funkcji usługi mowy, ułatwiając tworzenie oprogramowania z obsługą mowy. Obecnie zestawy SDK zapewniają dostęp do funkcji **zamiany mowy na tekst**, zamiany **tekstu na mowę**, **tłumaczenia mowy**, **rozpoznawania zamierzeń**i **bezpośredniego kanału mowy w programie bot Framework**. Ogólne omówienie możliwości i obsługiwanych platform można znaleźć na [stronie wprowadzanie](https://aka.ms/csspeech)dokumentacji.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Pobierz zestaw SDK

### <a name="windows"></a>Windows

W przypadku systemu Windows obsługiwane są następujące języki:

* C#(Platformy UWP i .NET) C++: możesz odwoływać się do najnowszej wersji pakietu NuGet zestawu mowy SDK i korzystać z niej. Pakiet zawiera 32-bitowe i 64-bitowe biblioteki klienckie oraz zarządzane biblioteki (.NET). Zestaw SDK można zainstalować w programie Visual Studio za pomocą narzędzia NuGet. Wyszukaj **Microsoft. CognitiveServices. Speech**.

* Java: można odwołać się do najnowszej wersji naszego pakietu Maven zestawu mowy SDK, który obsługuje tylko system Windows x64. W projekcie Maven Dodaj `https://csspeechstorage.blob.core.windows.net/maven/` jako dodatkowe repozytorium i odwołania `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` jako zależność.

### <a name="linux"></a>Linux

> [!NOTE]
> Obecnie obsługujemy tylko Ubuntu 16,04, Ubuntu 18,04 i Debian 9 w następujących architekturach docelowych:
> - x86, x64 i ARM64 na potrzeby C++ programowania
> - x64 i ARM64 dla języka Java
> - x64 dla platformy .NET Core i Python

Upewnij się, że wymagane biblioteki są zainstalowane, uruchamiając następujące polecenia powłoki:

w Ubuntu:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

W programie Debian 9:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

* C#: Możesz odwoływać się do najnowszej wersji pakietu NuGet zestawu mowy SDK i korzystać z niej. Aby odwołać się do zestawu SDK, Dodaj do projektu następujące odwołanie do pakietu:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.7.0" />
  ```

* Java: można odwołać się do najnowszej wersji naszego pakietu Maven zestawu mowy SDK. W projekcie Maven Dodaj `https://csspeechstorage.blob.core.windows.net/maven/` jako dodatkowe repozytorium i odwołania `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` jako zależność.

* C++: Pobierz zestaw SDK jako [pakiet tar](https://aka.ms/csspeech/linuxbinary) i rozpakuj pliki w wybranym katalogu. W poniższej tabeli przedstawiono strukturę folderów zestawu SDK:

  |Ścieżka|Opis|
  |-|-|
  |`license.md`|Licencjonowan|
  |`ThirdPartyNotices.md`|Uwagi dotyczące innych firm|
  |`include`|Pliki nagłówkowe dla C iC++|
  |`lib/x64`|Natywna biblioteka x64 do łączenia z aplikacją|
  |`lib/x86`|Natywna biblioteka x86 do łączenia z aplikacją|

  Aby utworzyć aplikację, skopiuj lub Przenieś wymagane pliki binarne (i biblioteki) do środowiska deweloperskiego. Uwzględnij je zgodnie z potrzebami w procesie kompilacji.

### <a name="android"></a>Android

Zestaw Java SDK dla systemu Android jest spakowany jako [AAR (Biblioteka systemu Android)](https://developer.android.com/studio/projects/android-library), który obejmuje niezbędne biblioteki i wymagane uprawnienia systemu Android. Jest ona hostowana w repozytorium Maven w `https://csspeechstorage.blob.core.windows.net/maven/` jako pakiet `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`.

Aby użyć pakietu z projektu Android Studio, wprowadź następujące zmiany:

* W pliku Build. Gradle na poziomie projektu Dodaj następujące polecenie do sekcji `repository`:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* W pliku Build. Gradle na poziomie modułu Dodaj następujące polecenie do sekcji `dependencies`:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

Zestaw Java SDK jest również częścią [zestawu Speech Devices SDK](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj subskrypcję wersji próbnej usługi Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznać mowęC#](quickstart-csharp-dotnet-windows.md)
