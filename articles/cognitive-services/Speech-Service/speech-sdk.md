---
title: Temat mowy usług Cognitive Services SDK
description: Przegląd zestawy SDK dostępne dla usługi mowy.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/17/2018
ms.author: v-jerkin
ms.openlocfilehash: 20fe0c3501e562584cd7762555479457a34a9297
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281327"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Temat mowy usług Cognitive Services SDK

Cognitive Services mowy Software Development Kit (SDK) udostępnia aplikacje natywne funkcje usługi rozpoznawania mowy, ułatwia tworzenie oprogramowania. Obecnie, zestaw SDK zapewnia dostęp do **zamiana mowy na tekst**, **tłumaczenia mowy**, i **rozpoznawanie intencji**.

[!include[Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-windows-sdk"></a>Pobierz Windows SDK

Zestaw SDK rozpoznawania mowy w wersji Windows zawiera 32-bitowych i 64-bitowych bibliotek klienta języka C/C++, a także zarządzane biblioteki (.NET) do użycia w języku C#. Zestaw SDK można zainstalować w programie Visual Studio za pomocą narzędzia NuGet; po prostu wyszukać `Microsoft.CognitiveServices.Speech`.

## <a name="get-the-linux-sdk"></a>Pobierz zestaw SDK systemu Linux

Upewnij się, że masz wymagany kompilator i biblioteki, uruchamiając następujące polecenia powłoki:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

> [!NOTE]
> W instrukcjach przyjęto założenie, że korzystasz z systemu Ubuntu 16.04 na komputerze (x86 lub x64). Na innej wersji systemu Ubuntu lub różnych dystrybucji systemu Linux należy dostosować te kroki dla danego środowiska.

Następnie [Pobierz zestaw SDK](https://aka.ms/csspeech/linuxbinary) i Rozpakowywanie plików do wybranego katalogu. Poniższej tabeli przedstawiono strukturę folderu zestawu SDK.

|Ścieżka|Opis|
|-|-|
|`license.md`|Licencja|
|`third-party-notices.md`|Uwagi dotyczące innych firm|
|`include`|Pliki nagłówkowe dla języków C i C++|
|`lib/x64`|X64 natywnej biblioteki do łączenia się z aplikacją|
|`lib/x86`|X86 natywnej biblioteki do łączenia się z aplikacją|

Do tworzenia aplikacji, skopiuj lub Przenieś wymagane pliki binarne (i biblioteki) do środowiska deweloperskiego i włączyć je zgodnie z potrzebami w procesie kompilacji.

## <a name="get-the-java-sdk"></a>Pobierz wersję oprogramowania Java SDK

Zestaw Java SDK dla systemu Android jest spakowany jako [AAR (biblioteka systemu Android)](https://developer.android.com/studio/projects/android-library), która obejmuje niezbędnych bibliotek, a także wymagane uprawnienia dla systemu Android dotyczące korzystania z niego.
Znajduje się w repozytorium narzędzia Maven w `https://csspeechstorage.blob.core.windows.net/maven/` jako pakiet `com.microsoft.cognitiveservices.speech:client-sdk:0.5.0`.
Zużywania pakietu z projektu w programie Android Studio dokonaj następujących zmian:

* Na poziomie projektu `build.gradle` plików, Dodaj następujący kod do `repository` sekcji:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* Na poziomie modułu `build.gradle` plików, Dodaj następujący kod do `dependencies` sekcji:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:0.5.0'
  ```

Zestaw Java SDK jest również częścią [zestawu Speech Devices SDK](speech-devices-sdk.md).

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
