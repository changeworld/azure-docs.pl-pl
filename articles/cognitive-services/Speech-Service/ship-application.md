---
title: Usługi Azure Cognitive Services, Cognitive Services — zestaw SDK interfejsu API rozpoznawania mowy dokumentacja — samouczki i dokumentacja interfejsu API
description: Dowiedz się, jak utworzyć i twórz aplikacje z zestawem SDK mowy usług Cognitive
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 65ff0e47cf7a53d519bfd0c50ea4c3ebd09a5766
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "41987467"
---
# <a name="shipping-an-application"></a>Wysyłanie aplikacji

Sprawdź [licencja pakietu SDK rozpoznawania mowy](license.md), jak również [uwagi dotyczące oprogramowania innych firm](third-party-notices.md) podczas dystrybucji Cognitive Services SDK rozpoznawania mowy. Ponadto przejrzyj [zasady zachowania poufności informacji firmy Microsoft](https://aka.ms/csspeech/privacy).

W zależności od platformy różnych składników zależnych istnieje uruchomić aplikację.

## <a name="windows"></a>Windows

Cognitive Services SDK rozpoznawania mowy jest testowana w systemie Windows 10 i systemie Windows Server 2016.

Cognitive Services SDK mowy wymaga [Microsoft Visual C++ Redistributable for Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) w systemie. Możesz pobrać pliki instalacyjne, aby uzyskać najnowszą wersję `Microsoft Visual C++ Redistributable for Visual Studio 2017` tutaj:

- [Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

Jeśli aplikacja korzysta z kodu zarządzanego, `.NET Framework 4.6.1` lub nowszy jest wymagany na komputerze docelowym.

Dane wejściowe mikrofonu Media Foundation biblioteki muszą być zainstalowane. Biblioteki te są częścią systemu Windows 10 i Windows Server 2016. Istnieje możliwość używania zestawu SDK mowy bez tych bibliotek, tak długo, jak mikrofon nie jest używana jako urządzenie wejściowe audio.

W tym samym katalogu co aplikację można wdrożyć wymagane pliki zestawów SDK rozpoznawania mowy. Dzięki temu aplikacja można uzyskać dostęp do biblioteki. Upewnij się, że wybierz poprawną wersję — Win32/x64 64 dopasowania aplikacji.

| Name (Nazwa) | Funkcja
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK, wymaganych do wdrożenia natywnych i zarządzanych
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | wymagane do wdrażania zarządzanego
| `Microsoft.CognitiveServices.Speech.csharp.dll` | wymagane do wdrażania zarządzanego

## <a name="linux"></a>Linux

Dla natywnych aplikacji, musisz wysłać biblioteki zestawu SDK rozpoznawania mowy `libMicrosoft.CognitiveServices.Speech.core.so`.
Upewnij się, zostanie wybrana wersja (x86, x64) Dopasowywanie aplikacji. W zależności od wersji systemu Linux może być również konieczne obejmują następujące zależności:

* Biblioteki udostępnione biblioteki C GNU (łącznie z biblioteki programowania wątków POSIX `libpthreads`)
* Biblioteki OpenSSL (`libssl.so.1.0.0`)
* Biblioteka programu cURL (`libcurl.so.4`)
* Biblioteki udostępnionej dla aplikacji ALSA (`libasound.so.2`)

Na Ubuntu 16.04 na przykład bibliotek GNU C powinno być już zainstalowane domyślnie. Trzy ostatnie można zainstalować przy użyciu następujących poleceń:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libcurl3 libasound2 wget
```

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
