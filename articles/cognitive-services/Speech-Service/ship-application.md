---
title: Tworzenie aplikacji za pomocą zestawu Speech SDK — Speech Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wdrożyć aplikację, która korzysta z zestawu Speech SDK na obsługiwanych platformach.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.custom: seodec18
ms.openlocfilehash: f4200a0a43507a335f0e4e59e5a794d2bc4d6142
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816549"
---
# <a name="ship-an-application"></a>Dostarczanie aplikacji

Obserwuj [licencja pakietu SDK rozpoznawania mowy](https://aka.ms/csspeech/license201809), także [uwagi dotyczące innych firm](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) dystrybucji Azure Cognitive Services SDK rozpoznawania mowy. Ponadto przejrzyj [zasady zachowania poufności informacji firmy Microsoft](https://aka.ms/csspeech/privacy).

W zależności od platformy różnych składników zależnych istnieje uruchomić aplikację.

## <a name="windows"></a>Windows

Cognitive Services SDK rozpoznawania mowy jest testowana w systemie Windows 10 i systemie Windows Server 2016.

Zestaw SDK mowy Cognitive Services wymaga programu [Microsoft Visual C++ redystrybucyjnego dla programu Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) w systemie. Możesz pobrać pliki instalacyjne, aby uzyskać najnowszą wersję `Microsoft Visual C++ Redistributable for Visual Studio 2019` tutaj:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Jeśli aplikacja korzysta z kodu zarządzanego, `.NET Framework 4.6.1` lub nowszy jest wymagany na komputerze docelowym.

Dla danych wejściowych mikrofonu muszą być zainstalowane biblioteki platformy Media Foundation. Biblioteki te są częścią systemu Windows 10 i Windows Server 2016. Istnieje możliwość używania zestawu SDK mowy bez tych bibliotek, tak długo, jak mikrofon nie jest używana jako urządzenie wejściowe audio.

W tym samym katalogu co aplikację można wdrożyć wymagane pliki zestawów SDK rozpoznawania mowy. Dzięki temu aplikacja można uzyskać dostęp do biblioteki. Upewnij się, że Wybierz prawidłową wersję — Win32/x64 64, który odpowiada aplikacji.

| Nazwa | Funkcja |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core SDK, wymaganych do wdrożenia natywnych i zarządzanych |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | wymagane do wdrażania zarządzanego                      |

> [!NOTE]
> Począwszy od wersji 1.3.0 plik `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (wysłane w poprzednich wersjach) nie jest już wymagany. Ta funkcja jest teraz zintegrowana z podstawowym zestawem SDK.

> [!NOTE]
> W przypadku projektu aplikacji Windows Forms (.NET Framework C# ) Upewnij się, że biblioteki są uwzględnione w ustawieniach wdrożenia projektu. Możesz to sprawdzić w obszarze `Properties -> Publish Section`. Kliknij przycisk `Application Files` i Znajdź odpowiednie biblioteki z listy rozwijanej. Upewnij się, że wartość jest ustawiona na `Included`. Program Visual Studio uwzględni plik, gdy projekt jest publikowany/wdrażany.

## <a name="linux"></a>Linux

Zestaw Speech SDK obecnie obsługuje dystrybucje Ubuntu 16,04, Ubuntu 18,04 i Debian 9.
Aplikację natywną, musisz wysłać biblioteki zestawu SDK rozpoznawania mowy, `libMicrosoft.CognitiveServices.Speech.core.so`.
Upewnij się, że wybrano wersję (x86, x64), która jest zgodna z aplikacji. W zależności od wersji systemu Linux, również może być konieczne obejmują następujące zależności:

- Biblioteki udostępnione biblioteki GNU C (łącznie z biblioteki programowania wątków POSIX `libpthreads`)
- Biblioteka OpenSSL (`libssl.so.1.0.0` lub `libssl.so.1.0.2`)
- Biblioteki udostępnionej dla aplikacji ALSA (`libasound.so.2`)

W systemie Ubuntu biblioteki GNU C powinny już być instalowane domyślnie. Trzy ostatnie można zainstalować za pomocą poniższych poleceń:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

W systemie Debian 9 Zainstaluj następujące pakiety:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>Następne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Zobacz, jak rozpoznawanie mowy w języku C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
