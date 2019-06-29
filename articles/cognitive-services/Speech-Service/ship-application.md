---
title: Tworzenie aplikacji za pomocą mowy SDK — usługi mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak tworzyć aplikacje przy użyciu zestawu SDK rozpoznawania mowy.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: ae075dbc922932a4eaffd9126560c159d33459d0
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466968"
---
# <a name="ship-an-application"></a>Dostarczanie aplikacji

Obserwuj [licencja pakietu SDK rozpoznawania mowy](https://aka.ms/csspeech/license201809), także [uwagi dotyczące innych firm](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) dystrybucji Azure Cognitive Services SDK rozpoznawania mowy. Ponadto przejrzyj [zasady zachowania poufności informacji firmy Microsoft](https://aka.ms/csspeech/privacy).

W zależności od platformy różnych składników zależnych istnieje uruchomić aplikację.

## <a name="windows"></a>Windows

Cognitive Services SDK rozpoznawania mowy jest testowana w systemie Windows 10 i systemie Windows Server 2016.

Cognitive Services SDK mowy wymaga [programu Microsoft Visual C++ Redistributable dla programu Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) w systemie. Możesz pobrać pliki instalacyjne, aby uzyskać najnowszą wersję `Microsoft Visual C++ Redistributable for Visual Studio 2019` tutaj:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Jeśli aplikacja korzysta z kodu zarządzanego, `.NET Framework 4.6.1` lub nowszy jest wymagany na komputerze docelowym.

Dla danych wejściowych mikrofonu muszą być zainstalowane biblioteki platformy Media Foundation. Biblioteki te są częścią systemu Windows 10 i Windows Server 2016. Istnieje możliwość używania zestawu SDK mowy bez tych bibliotek, tak długo, jak mikrofon nie jest używana jako urządzenie wejściowe audio.

W tym samym katalogu co aplikację można wdrożyć wymagane pliki zestawów SDK rozpoznawania mowy. Dzięki temu aplikacja można uzyskać dostęp do biblioteki. Upewnij się, że Wybierz prawidłową wersję — Win32/x64 64, który odpowiada aplikacji.

| Name (Nazwa) | Funkcja
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK, wymaganych do wdrożenia natywnych i zarządzanych
| `Microsoft.CognitiveServices.Speech.csharp.dll` | wymagane do wdrażania zarządzanego

>[!NOTE]
> Począwszy od wersji 1.3.0 pliku `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (dostarczane w poprzednich wersjach) nie jest już potrzebny. Funkcje jest teraz zintegrowana w programie core SDK.

>[!NOTE]
> W aplikacji Windows Forms (.NET Framework) C# projektu, upewnij się, bibliotek znajdują się w ustawieniach wdrażania projektu. Możesz to sprawdzić w obszarze `Properties -> Publish Section`. Kliknij przycisk `Application Files` przycisk i znaleźć odpowiedniej biblioteki z przewiń w dół listy. Upewnij się, że wartość jest równa `Included`. Program Visual Studio będzie zawierać plik, gdy projekt jest opublikowana lub wdrożone.

## <a name="linux"></a>Linux

Zestaw SDK rozpoznawania mowy obsługuje obecnie dystrybucji Ubuntu 16.04 i Ubuntu 18.04, Debian 9.
Aplikację natywną, musisz wysłać biblioteki zestawu SDK rozpoznawania mowy, `libMicrosoft.CognitiveServices.Speech.core.so`.
Upewnij się, że wybrano wersję (x86, x64), która jest zgodna z aplikacji. W zależności od wersji systemu Linux, również może być konieczne obejmują następujące zależności:

* Biblioteki udostępnione biblioteki GNU C (łącznie z biblioteki programowania wątków POSIX `libpthreads`)
* Biblioteki OpenSSL (`libssl.so.1.0.0` lub `libssl.so.1.0.2`)
* Biblioteki udostępnionej dla aplikacji ALSA (`libasound.so.2`)

W systemie Ubuntu bibliotek GNU C powinno być już zainstalowane domyślnie. Trzy ostatnie można zainstalować za pomocą poniższych poleceń:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Debian 9 zainstalować te pakiety:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
