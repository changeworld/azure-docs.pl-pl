---
title: Tworzenie aplikacji za pomocą usługi Speech SDK — Speech
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wdrożyć aplikację, która używa SDK mowy na obsługiwanych platformach.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330808"
---
# <a name="ship-an-application"></a>Dostarczanie aplikacji

Należy przestrzegać [licencji zestawów SDK mowy,](https://aka.ms/csspeech/license201809)a także [powiadomień o oprogramowaniu innych firm](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) podczas dystrybucji pakietu Azure Cognitive Services Speech SDK. Zapoznaj się również z [Zasadami zachowania poufności informacji firmy Microsoft](https://aka.ms/csspeech/privacy).

W zależności od platformy istnieją różne zależności do wykonania aplikacji.

## <a name="windows"></a>Windows

SDK mowy usług Cognitive Services jest testowany w systemie Windows 10 i Windows Server 2016.

Pakiet SDK mowy usług Cognitive Services wymaga [redystrybucyjnego programu Microsoft Visual C++ dla programu Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) w systemie. Możesz pobrać instalatory najnowszej wersji `Microsoft Visual C++ Redistributable for Visual Studio 2019` tutaj:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Jeśli aplikacja używa kodu zarządzanego, `.NET Framework 4.6.1` lub później jest wymagane na komputerze docelowym.

W przypadku wejścia mikrofonu należy zainstalować biblioteki Media Foundation. Te biblioteki są częścią systemu Windows 10 i Windows Server 2016. Można używać zestawu SDK mowy bez tych bibliotek, o ile mikrofon nie jest używany jako urządzenie wejściowe audio.

Wymagane pliki SDK mowy można wdrożyć w tym samym katalogu co aplikacja. W ten sposób aplikacja może bezpośrednio uzyskać dostęp do bibliotek. Upewnij się, że wybrano poprawną wersję (Win32/x64), która pasuje do aplikacji.

| Nazwa | Funkcja |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | Podstawowy sdk wymagany do wdrożenia natywnego i zarządzanego |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Wymagane do wdrożenia zarządzanego                      |

> [!NOTE]
> Począwszy od wersji 1.3.0 plik `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (dostarczany w poprzednich wersjach) nie jest już potrzebny. Funkcja jest teraz zintegrowana z podstawowym SDK.

> [!NOTE]
> W przypadku projektu C# aplikacji Windows Forms (.NET Framework) upewnij się, że biblioteki są uwzględnione w ustawieniach wdrażania projektu. Możesz to sprawdzić `Properties -> Publish Section`w obszarze . Kliknij `Application Files` przycisk i znajdź odpowiednie biblioteki z listy przewijania w dół. Upewnij się, że `Included`wartość jest ustawiona na . Visual Studio będzie zawierać plik, gdy projekt jest publikowany/wdrażany.

## <a name="linux"></a>Linux

Speech SDK obsługuje obecnie dystrybucje Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8.
W przypadku aplikacji natywnej należy wysłać `libMicrosoft.CognitiveServices.Speech.core.so`bibliotekę SDK mowy, .
Upewnij się, że wybrano wersję (x86, x64), która pasuje do aplikacji. W zależności od wersji systemu Linux może być również konieczne uwzględnienie następujących zależności:

- Biblioteki współdzielone biblioteki GNU C (w tym biblioteka programowania wątków POSIX), `libpthreads`)
- Biblioteka OpenSSL`libssl.so.1.0.0` `libssl.so.1.0.2`( lub )
- Biblioteka udostępniona dla aplikacji`libasound.so.2`ALSA ( )

Na Ubuntu biblioteki GNU C powinny być już domyślnie zainstalowane. Ostatnie trzy można zainstalować za pomocą następujących poleceń:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Na Debianie 9 instaluj te pakiety:

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

## <a name="next-steps"></a>Następne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Zobacz, jak rozpoznać mowę w języku C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
