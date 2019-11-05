---
title: Tworzenie aplikacji za pomocą zestawu Speech SDK — Speech Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak tworzyć aplikacje przy użyciu zestawu Speech SDK.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.custom: seodec18
ms.openlocfilehash: 166ae00085f07ef24d746b60947a31e7680a0f00
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491001"
---
# <a name="ship-an-application"></a>Dostarczanie aplikacji

Zaobserwuj [licencję zestawu Speech SDK](https://aka.ms/csspeech/license201809), a także [Informacje o oprogramowaniu](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) innych firm podczas dystrybucji zestawu Speech sdk dla platformy Azure Cognitive Services. Ponadto zapoznaj się z [zasadami zachowania poufności informacji firmy Microsoft](https://aka.ms/csspeech/privacy).

W zależności od platformy istnieją różne zależności do wykonania aplikacji.

## <a name="windows"></a>Windows

Zestaw SDK mowy Cognitive Services jest testowany w systemie Windows 10 i w systemie Windows Server 2016.

Zestaw SDK mowy Cognitive Services wymaga programu [Microsoft Visual C++ redystrybucyjnego dla programu Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) w systemie. Instalatory dla najnowszej wersji `Microsoft Visual C++ Redistributable for Visual Studio 2019` można pobrać tutaj:

- [System](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [procesorów](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Jeśli aplikacja używa kodu zarządzanego, na maszynie docelowej wymagane są `.NET Framework 4.6.1` lub nowsze.

W przypadku danych wejściowych mikrofonu należy zainstalować biblioteki platforma Media Foundation. Te biblioteki są częścią systemów Windows 10 i Windows Server 2016. Istnieje możliwość użycia zestawu Speech SDK bez tych bibliotek, o ile mikrofon nie jest używany jako urządzenie wejściowe audio.

Wymagane pliki zestawu Speech SDK można wdrożyć w tym samym katalogu, w którym znajduje się aplikacja. Dzięki temu aplikacja może bezpośrednio uzyskiwać dostęp do bibliotek. Upewnij się, że wybrano poprawną wersję (Win32/x64) zgodną z aplikacją.

| Nazwa | Funkcja
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Podstawowy zestaw SDK wymagany do wdrożenia natywnego i zarządzanego
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Wymagane do wdrożenia zarządzanego

>[!NOTE]
> Począwszy od wersji 1.3.0 plik `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (wysłane w poprzednich wersjach) nie jest już wymagany. Ta funkcja jest teraz zintegrowana z podstawowym zestawem SDK.

>[!NOTE]
> W przypadku projektu aplikacji Windows Forms (.NET Framework C# ) Upewnij się, że biblioteki są uwzględnione w ustawieniach wdrożenia projektu. Możesz to sprawdzić w obszarze `Properties -> Publish Section`. Kliknij przycisk `Application Files` i Znajdź odpowiednie biblioteki z listy rozwijanej. Upewnij się, że wartość jest ustawiona na `Included`. Program Visual Studio uwzględni plik, gdy projekt jest publikowany/wdrażany.

## <a name="linux"></a>Linux

Zestaw Speech SDK obecnie obsługuje dystrybucje Ubuntu 16,04, Ubuntu 18,04 i Debian 9.
W przypadku aplikacji natywnych musisz dostarczyć bibliotekę zestawu Speech SDK, `libMicrosoft.CognitiveServices.Speech.core.so`.
Upewnij się, że wybrano wersję (x86, x64) zgodną z aplikacją. W zależności od wersji systemu Linux może być również konieczne uwzględnienie następujących zależności:

* Biblioteki udostępnione biblioteki GNU C (w tym biblioteki programowania wątków POSIX, `libpthreads`)
* Biblioteka OpenSSL (`libssl.so.1.0.0` lub `libssl.so.1.0.2`)
* Biblioteka udostępniona dla aplikacji ALSA (`libasound.so.2`)

W systemie Ubuntu biblioteki GNU C powinny już być instalowane domyślnie. Ostatnie trzy można zainstalować za pomocą następujących poleceń:

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

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznać mowęC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
