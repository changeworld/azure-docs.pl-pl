---
title: Informacje o SDK mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Zestaw SDK (Speech Software Development Kit) zapewnia aplikacjom natywny dostęp do funkcji usługi Mowy, co ułatwia tworzenie oprogramowania. Ten artykuł zawiera dodatkowe szczegóły dotyczące sdk dla systemów Windows, Linux i Android.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: dapine
ms.openlocfilehash: 984d2dfe07faa22756b4be167aa86a69806b1a84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331097"
---
# <a name="about-the-speech-sdk"></a>Informacje o zestawie SDK usługi Mowa

Zestaw SDK (Speech Software Development Kit) zapewnia aplikacjom dostęp do funkcji usługi Mowy, co ułatwia tworzenie oprogramowania obsługującego mowę. Obecnie zestawY SDK zapewniają dostęp do **zamiany mowy na tekst,** **zamiany tekstu na mowę,** **tłumaczenia mowy,** **rozpoznawania intencji**i **kanału Direct Line Speech firmy Bot Framework.**

Za pomocą zestawu SDK mowy można łatwo przechwytywać dźwięk z mikrofonu, odczytywać go ze strumienia lub uzyskiwać dostęp do plików audio z pamięci masowej. Zestaw Speech SDK obsługuje 16-bitowy, 16 kHz/8 kHz, jednokanałowy dźwięk WAV/PCM do rozpoznawania mowy. Dodatkowe formaty audio są obsługiwane przy użyciu [punktu końcowego REST zamiany mowy na tekst](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) lub usługi [transkrypcji wsadowej.](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)

Ogólny przegląd możliwości i obsługiwanych platform można znaleźć na [stronie wpisu](https://aka.ms/csspeech)dokumentacji .

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Pobierz sdk

# <a name="windows"></a>[Windows](#tab/windows)

> [!WARNING]
> Zestaw SDK mowy obsługuje system Windows 10 lub nowsze wersje. Wcześniejsze wersje systemu Windows nie są **obsługiwane**.

W systemie Windows obsługujemy następujące języki:

* C# (UWP i .NET), C++: Można odwoływać się i używać najnowszej wersji naszego pakietu NuGet zestawu SDK mowy. Pakiet zawiera 32-bitowe i 64-bitowe biblioteki klienckie i biblioteki zarządzane (.NET). SDK można zainstalować w programie Visual Studio przy użyciu NuGet, [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech).

* Java: Możesz odwoływać się i używać najnowszej wersji naszego pakietu Speech SDK Maven, który obsługuje tylko system Windows x64. W projekcie Maven `https://csspeechstorage.blob.core.windows.net/maven/` dodaj jako dodatkowe repozytorium i odwołanie `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0` jako zależność.

# <a name="linux"></a>[Linux](#tab/linux)

> [!NOTE]
> Obecnie obsługujemy tylko Ubuntu 16.04, Ubuntu 18.04, Debian 9, Red Hat Enterprise Linux (RHEL) 8 i CentOS 8 na następujących architekturach docelowych:
> - x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) i ARM64 (Debian/Ubuntu) dla rozwoju C++
> - x64, ARM32 (Debian/Ubuntu) i ARM64 (Debian/Ubuntu) dla Javy
> - x64 dla .NET Core i Python

Upewnij się, że masz zainstalowane wymagane biblioteki, uruchamiając następujące polecenia powłoki:

W systemie Ubuntu:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Na Debianie 9:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

Na RHEL/CentOS 8:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> Na RHEL/CentOS 8 postępuj zgodnie z instrukcjami [dotyczącymi konfigurowania OpenSSL dla systemu Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

* C#: Możesz odwoływać się i używać najnowszej wersji naszego pakietu NuGet zestawu SDK mowy. Aby odwołać się do zestawu SDK, dodaj do projektu następujące odwołanie do pakietu:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java: Możesz odwoływać się i używać najnowszej wersji naszego pakietu Speech SDK Maven. W projekcie Maven `https://csspeechstorage.blob.core.windows.net/maven/` dodaj jako dodatkowe repozytorium i odwołanie `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` jako zależność.

* C++: Pobierz pakiet SDK jako [pakiet .tar](https://aka.ms/csspeech/linuxbinary) i rozpakuj pliki w wybranym katalogu. W poniższej tabeli przedstawiono strukturę folderów SDK:

  |Ścieżka|Opis|
  |-|-|
  |`license.md`|Licencja|
  |`ThirdPartyNotices.md`|Uwagi dotyczące innych firm|
  |`include`|Pliki nagłówkowe dla języka C i C++|
  |`lib/x64`|Natywna biblioteka x64 do łączenia z aplikacją|
  |`lib/x86`|Natywna biblioteka x86 do łączenia z aplikacją|

  Aby utworzyć aplikację, skopiuj lub przenieś wymagane pliki binarne (i biblioteki) do środowiska programistycznego. Uwzględnij je zgodnie z wymaganiami w procesie kompilacji.

# <a name="android"></a>[Android](#tab/android)

Zestaw Java SDK dla systemu Android jest dostarczany jako [biblioteka AAR (Android Library),](https://developer.android.com/studio/projects/android-library)która zawiera niezbędne biblioteki i wymagane uprawnienia systemu Android. Jest hostowany w repozytorium Maven `https://csspeechstorage.blob.core.windows.net/maven/` w `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`pakiecie.

Aby korzystać z pakietu z projektu Android Studio, należy wprowadzić następujące zmiany:

* W pliku build.gradle na poziomie projektu dodaj `repository` do sekcji następujące elementy:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* W pliku build.gradle na poziomie modułu `dependencies` dodaj do sekcji następujące elementy:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

Java SDK jest również częścią [SDK urządzeń mowy](speech-devices-sdk.md).

---

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Następne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznać mowę w języku C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
