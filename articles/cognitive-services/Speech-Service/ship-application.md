---
title: Dokumentacja interfejsu API dokumentacji — samouczki, mowy interfejsu API zestawu SDK usług kognitywnych usług Azure, kognitywnych | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia i opracowywania aplikacji z zestawem SDK kognitywnych mowy usługi
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: d410dda09fdd30181b633c454b1d44610b10c472
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "35356236"
---
# <a name="shipping-an-application"></a>Wysyłanie aplikacji

Obserwować [licencji SDK mowy](license.md), jak również [powiadomienia oprogramowania innych firm](third-party-notices.md) do rozpowszechniania kognitywnych Services SDK mowy. Sprawdź również [zasady zachowania poufności informacji firmy Microsoft](https://aka.ms/csspeech/privacy).

W zależności od platformy różnych zależności istnieje wykonania aplikacji.

## <a name="windows"></a>Windows

Zestaw SDK kognitywnych mowy usługi jest testowany w systemie Windows 10 i Windows Server 2016.

Zestaw SDK kognitywnych mowy usług wymaga [Microsoft Visual C++ Redistributable dla Visual Studio 2017](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) w systemie. Możesz pobrać instalatorów do najnowszej wersji `Microsoft Visual C++ Redistributable for Visual Studio 2017` tutaj:

- [Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

Jeśli aplikacja korzysta z kodu zarządzanego `.Net Framework 4.6.1` lub nowszy jest wymagany na komputerze docelowym.

Dane wejściowe mikrofonu Media Foundation biblioteki muszą być zainstalowane. Te biblioteki są częścią systemu Windows 10 i Windows Server 2016. Użytkownik może korzystać z zestawu SDK mowy bez te biblioteki tak długo, jak mikrofon nie są używane jako urządzenia wejściowego audio.

Wymagane pliki mowy zestawu SDK można wdrożyć w tym samym katalogu co aplikacja. W ten sposób aplikacji można uzyskać dostęp do biblioteki. Upewnij się, że należy wybrać prawidłową wersję (Win32/x64) dopasowania aplikacji.

| Name (Nazwa) | Funkcja
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK, wymaganych do wdrożenia natywnych i zarządzanych
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | wymagane do wdrażania zarządzanego
| `Microsoft.CognitiveServices.Speech.csharp.dll` | wymagane do wdrażania zarządzanego

## <a name="linux"></a>Linux

Dla natywnych aplikacji, musisz wysłać biblioteki SDK mowy `libMicrosoft.CognitiveServices.Speech.core.so`.
Upewnij się, że zostanie wybrana wersja (x86, x64) dopasowania aplikacji. W zależności od wersji systemu Linux należy również uwzględnić następujące zależności:

* Współużytkowanych bibliotekach biblioteki C GNU (łącznie z biblioteki programowania wątków POSIX `libpthreads`)
* Biblioteki OpenSSL (`libssl.so.1.0.0`)
* Biblioteka cURL (`libcurl.so.4`)
* Biblioteki udostępnionej dla aplikacji ALSA (`libasound.so.2`)

Na 16.04 Ubuntu na przykład bibliotek GNU C powinno być już zainstalowane domyślnie. Ostatnich trzech można zainstalować przy użyciu tych poleceń:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libcurl3 libasound2 wget
```

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz wersję próbną subskrypcji mowy](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz rozpoznawanie mowy w języku C#](quickstart-csharp-windows.md)
