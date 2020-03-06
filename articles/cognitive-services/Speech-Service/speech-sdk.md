---
title: Informacje o zestawie SDK mowy — Speech Service
titleSuffix: Azure Cognitive Services
description: Zestaw Speech Software Development Kit (SDK) zapewnia aplikacjom natywny dostęp do funkcji usługi mowy, ułatwiając opracowywanie oprogramowania. Ten artykuł zawiera dodatkowe szczegóły dotyczące zestawu SDK dla Windows, Linux i Android.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: dapine
ms.openlocfilehash: 984d2dfe07faa22756b4be167aa86a69806b1a84
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331097"
---
# <a name="about-the-speech-sdk"></a>Informacje o zestawie SDK usługi Mowa

Mowy Software Development Kit (SDK) daje dostęp aplikacji do funkcji usługi rozpoznawania mowy, co ułatwia tworzenie oprogramowania dla komputerów z obsługą mowy. Obecnie zestawy SDK zapewniają dostęp do funkcji **zamiany mowy na tekst**, zamiany **tekstu na mowę**, **tłumaczenia mowy**, **rozpoznawania zamierzeń**i **bezpośredniego kanału mowy w programie bot Framework**.

Możesz łatwo przechwycić dźwięk z mikrofonu, odczytać ze strumienia lub uzyskać dostęp do plików audio z magazynu za pomocą zestawu Speech SDK. Zestaw Speech SDK obsługuje format WAV/PCM 16-bitowy, 16 kHz/8 kHz, dźwięk z jednym kanałem na potrzeby rozpoznawania mowy. Dodatkowe formaty audio są obsługiwane za pomocą [punktu końcowego REST zamiany mowy na tekst](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) lub [usługi transkrypcji partii](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

Ogólne omówienie możliwości i obsługiwanych platform można znaleźć na [stronie wprowadzanie](https://aka.ms/csspeech)dokumentacji.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Pobierz zestaw SDK

# <a name="windows"></a>[Windows](#tab/windows)

> [!WARNING]
> Zestaw Speech SDK obsługuje system Windows 10 lub nowszy. Wcześniejsze wersje systemu Windows **nie są obsługiwane**.

Dla Windows firma Microsoft obsługuje następujące języki:

* C# C++ (platformy UWP i programowi .NET): można odwołać się i używać najnowszej wersji naszych pakietu NuGet zestawu SDK rozpoznawania mowy. Ten pakiet zawiera biblioteki klienckie 32-bitowych i 64-bitowych i bibliotek zarządzanych (.NET). Zestaw SDK można zainstalować w programie Visual Studio za pomocą narzędzia NuGet, [Microsoft. CognitiveServices. Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech).

* Java: Możesz odwołać się i używać najnowszej wersji naszych pakietu Maven zestaw SDK rozpoznawania mowy, który obsługuje tylko Windows x64. W projekcie Maven Dodaj `https://csspeechstorage.blob.core.windows.net/maven/` jako dodatkowe repozytorium i odwołania `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0` jako zależność.

# <a name="linux"></a>[Linux](#tab/linux)

> [!NOTE]
> Obecnie obsługujemy tylko Ubuntu 16,04, Ubuntu 18,04, Debian 9, Red Hat Enterprise Linux (RHEL) 8 i CentOS 8 dla następujących architektur docelowych:
> - x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) i ARM64 (Debian/Ubuntu) do C++ programowania
> - x64, ARM32 (Debian/Ubuntu) i ARM64 (Debian/Ubuntu) dla języka Java
> - x64 dla platformy .NET Core i Python

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

W systemie RHEL/CentOS 8:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> W systemie RHEL/CentOS 8 postępuj zgodnie z instrukcjami dotyczącymi [sposobu konfigurowania OpenSSL dla systemu Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

* C#: Można odwołać się i używać najnowszej wersji naszych pakietu NuGet zestawu SDK rozpoznawania mowy. Aby odwołać się do zestawu SDK, należy dodać następujące odwołanie pakietu do projektu:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java: Możesz odwołać się i używać najnowszej wersji naszych pakietu Maven zestaw SDK rozpoznawania mowy. W projekcie Maven Dodaj `https://csspeechstorage.blob.core.windows.net/maven/` jako dodatkowe repozytorium i odwołania `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` jako zależność.

* C++: Pobierz zestaw SDK jako [pakiet tar](https://aka.ms/csspeech/linuxbinary) i rozpakuj pliki w wybranym katalogu. W poniższej tabeli przedstawiono strukturę folderu zestawu SDK:

  |Ścieżka|Opis|
  |-|-|
  |`license.md`|Licencja|
  |`ThirdPartyNotices.md`|Uwagi dotyczące innych firm|
  |`include`|Pliki nagłówkowe dla języków C i C++|
  |`lib/x64`|X64 natywnej biblioteki do łączenia się z aplikacją|
  |`lib/x86`|X86 natywnej biblioteki do łączenia się z aplikacją|

  Aby utworzyć aplikację, skopiuj lub Przenieś wymagane pliki binarne (i biblioteki) do środowiska deweloperskiego. Włączyć je zgodnie z potrzebami w procesie kompilacji.

# <a name="android"></a>[Android](#tab/android)

Zestaw Java SDK dla systemu Android jest spakowany jako [AAR (Biblioteka systemu Android)](https://developer.android.com/studio/projects/android-library), który obejmuje niezbędne biblioteki i wymagane uprawnienia systemu Android. Jest ona hostowana w repozytorium Maven w `https://csspeechstorage.blob.core.windows.net/maven/` jako `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`pakietu.

Korzystanie z pakietu z projektu w programie Android Studio, należy wprowadzić następujące zmiany:

* W pliku Build. Gradle na poziomie projektu Dodaj następujące polecenie do sekcji `repository`:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* W pliku Build. Gradle na poziomie modułu Dodaj następujące polecenie do sekcji `dependencies`:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

Zestaw Java SDK jest również częścią [zestawu Speech Devices SDK](speech-devices-sdk.md).

---

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Następne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznać mowęC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
