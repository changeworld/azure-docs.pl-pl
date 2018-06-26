---
title: Szybki Start — mowy zestawu SDK dla języka C++ i Linux | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy z systemem Linux i C++ w usługach kognitywnych przy użyciu zestawu SDK mowy.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: cee70ba585f93dda3249fc5b39f25fb613b57a45
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753663"
---
# <a name="quickstart-for-c-and-linux"></a>Szybki Start dla języka C++ i Linux

Bieżąca wersja zestawu SDK kognitywnych mowy usługi jest `0.4.0`.

Kognitywnych usług mowy zestawu SDK dla systemu Linux jest dostępna dla tworzenia aplikacji 64-bitowe i 32-bitowych. Wymagane pliki można pobrać jako plik tar z https://aka.ms/csspeech/linuxbinary.

> [!NOTE]
> Jeśli szukasz szybkiego startu dla języka C++ i Windows, przejdź [tutaj](quickstart-cpp-windows.md).
> Jeśli szukasz szybkiego startu dla C# i systemu Windows, przejdź [tutaj](quickstart-csharp-windows.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> W instrukcjach przyjęto założenie, że używasz 16.04 Ubuntu na komputerze (x86 lub x64).
> Na inną wersję Ubuntu lub różnych dystrybucji systemu Linux trzeba będzie dostosować wymagane kroki.

## <a name="prerequisites"></a>Wymagania wstępne

[!include[Ubuntu Prerequisites](includes/ubuntu1604-prerequisites.md)]

## <a name="getting-the-binary-package"></a>Pobieranie pakietu binarne

[!include[License Notice](includes/license-notice.md)]

1. Wybierz katalog (ścieżka bezwzględna), w której chcesz umieścić pliki binarne SDK mowy i nagłówków.
   Na przykład wybierz ścieżkę `speechsdk` w obszarze katalogu macierzystego:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Jeśli go jeszcze nie istnieje, należy utworzyć katalogu:

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Pobierać i wyodrębniać `.tar.gz` archiwum przy użyciu plików binarnych mowy SDK:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Sprawdzanie poprawności zawartości katalogu najwyższego poziomu wyodrębnionego pakietu:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Powinny mieć powiadomienia innych firm i plików licencji, a także `include` katalogu dla nagłówków i `lib` katalogu bibliotek.

   [!include[Linux Binary Archive Content](includes/linuxbinary-content.md)]

## <a name="sample-code"></a>Przykładowy kod

Poniższy kod rozpoznaje angielskiej wersji językowej mowy mikrofonu.
Umieszczenie go w pliku o nazwie `quickstart-linux.cpp`:

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Linux/quickstart-linux/quickstart-linux.cpp#code)]

Zastąp klucz subskrypcji w kodzie, który został uzyskany.

## <a name="building"></a>Kompilowanie

> [!NOTE]
> Upewnij się, że skopiuj i wklej poniższe polecenia kompilacji jako _pojedynczy wiersz_.

* Uruchom następujące polecenie, aby utworzyć aplikację na x64 maszyny:

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* Uruchom następujące polecenie, aby utworzyć aplikację na x86 maszyny:

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="running"></a>Działanie

Aby uruchomić aplikację, musisz skonfigurować moduł ładujący ścieżkę biblioteki, aby wskazywała na biblioteki SDK mowy.

* Na x64 komputera, uruchom:

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
  ```

* Na x86 komputera, uruchom:

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
  ```

Uruchom aplikację w następujący sposób:

```sh
./quickstart-linux
```

Jeśli wszystko przebiegnie poprawnie, powinny być widoczne dane wyjściowe podobne do poniższego:

```text
Say something...
We recognized: What's the weather
```

## <a name="downloading-the-sample"></a>Pobieranie próbki

Dla zestawu najnowsze przykłady, zobacz [repozytorium GitHub próbki SDK kognitywnych usług mowy](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Kolejne kroki

* Odwiedź stronę [przykłady strony](samples.md) dla dodatkowych przykładów.
