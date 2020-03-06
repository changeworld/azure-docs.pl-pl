---
title: Tworzenie aplikacji za pomocą zestawu Speech SDK — Speech Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wdrożyć aplikację, która korzysta z zestawu Speech SDK na obsługiwanych platformach.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 9507428e63b337b3d8419a833d03d081d494c522
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330808"
---
# <a name="ship-an-application"></a>Dostarczanie aplikacji

Zaobserwuj [licencję zestawu Speech SDK](https://aka.ms/csspeech/license201809), a także [Informacje o oprogramowaniu](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) innych firm podczas dystrybucji zestawu Speech sdk dla platformy Azure Cognitive Services. Ponadto zapoznaj się z [zasadami zachowania poufności informacji firmy Microsoft](https://aka.ms/csspeech/privacy).

W zależności od platformy różnych składników zależnych istnieje uruchomić aplikację.

## <a name="windows"></a>Windows

Cognitive Services SDK rozpoznawania mowy jest testowana w systemie Windows 10 i systemie Windows Server 2016.

Zestaw SDK mowy Cognitive Services wymaga programu [Microsoft Visual C++ redystrybucyjnego dla programu Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) w systemie. Instalatory dla najnowszej wersji `Microsoft Visual C++ Redistributable for Visual Studio 2019` można pobrać tutaj:

- [System](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [procesorów](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Jeśli aplikacja używa kodu zarządzanego, na maszynie docelowej wymagane są `.NET Framework 4.6.1` lub nowsze.

Dla danych wejściowych mikrofonu muszą być zainstalowane biblioteki platformy Media Foundation. Biblioteki te są częścią systemu Windows 10 i Windows Server 2016. Istnieje możliwość używania zestawu SDK mowy bez tych bibliotek, tak długo, jak mikrofon nie jest używana jako urządzenie wejściowe audio.

W tym samym katalogu co aplikację można wdrożyć wymagane pliki zestawów SDK rozpoznawania mowy. Dzięki temu aplikacja można uzyskać dostęp do biblioteki. Upewnij się, że Wybierz prawidłową wersję — Win32/x64 64, który odpowiada aplikacji.

| Name (Nazwa) | Funkcja |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core SDK, wymaganych do wdrożenia natywnych i zarządzanych |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | wymagane do wdrażania zarządzanego                      |

> [!NOTE]
> Począwszy od wersji 1.3.0 plik `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (wysłane w poprzednich wersjach) nie jest już wymagany. Ta funkcja jest teraz zintegrowana z podstawowym zestawem SDK.

> [!NOTE]
> W przypadku projektu aplikacji Windows Forms (.NET Framework C# ) Upewnij się, że biblioteki są uwzględnione w ustawieniach wdrożenia projektu. Możesz to sprawdzić w obszarze `Properties -> Publish Section`. Kliknij przycisk `Application Files` i Znajdź odpowiednie biblioteki z listy rozwijanej. Upewnij się, że wartość jest ustawiona na `Included`. Program Visual Studio uwzględni plik, gdy projekt jest publikowany/wdrażany.

## <a name="linux"></a>Linux

Zestaw Speech SDK obecnie obsługuje dystrybucje Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8.
W przypadku aplikacji natywnych musisz dostarczyć bibliotekę zestawu Speech SDK, `libMicrosoft.CognitiveServices.Speech.core.so`.
Upewnij się, że wybrano wersję (x86, x64), która jest zgodna z aplikacji. W zależności od wersji systemu Linux, również może być konieczne obejmują następujące zależności:

- Biblioteki udostępnione biblioteki GNU C (w tym biblioteki programowania wątków POSIX, `libpthreads`)
- Biblioteka OpenSSL (`libssl.so.1.0.0` lub `libssl.so.1.0.2`)
- Biblioteka udostępniona dla aplikacji ALSA (`libasound.so.2`)

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

W systemie RHEL/CentOS 8:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> W systemie RHEL/CentOS 8 postępuj zgodnie z instrukcjami dotyczącymi [sposobu konfigurowania OpenSSL dla systemu Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

## <a name="next-steps"></a>Następne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Zobacz, jak rozpoznać mowęC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
