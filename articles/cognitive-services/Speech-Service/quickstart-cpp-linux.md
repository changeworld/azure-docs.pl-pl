---
title: 'Szybki Start: Rozpoznawanie mowy, języka C++ w systemie Linux przy użyciu Cognitive Services SDK rozpoznawania mowy'
titleSuffix: Microsoft Cognitive Services
description: Dowiedz się, jak rozpoznawanie mowy, języka C++ w systemie Linux przy użyciu Cognitive Services SDK rozpoznawania mowy
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 8f794f1d29b5fde9acd43fa4a018ccb39a8ccd25
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324782"
---
# <a name="quickstart-recognize-speech-in-c-on-linux-using-the-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy, języka C++ w systemie Linux przy użyciu zestawu SDK rozpoznawania mowy

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule dowiesz się, jak utworzyć aplikację konsolową w języku C++ w systemie Linux (Ubuntu 16.04) Cognitive Services SDK rozpoznawania mowy do transkrypcja mowy na tekst.

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji dla usługi mowy. Zobacz [bezpłatnie wypróbować usługę rozpoznawania mowy](get-started.md).
* Ubuntu 16.04 komputera przy użyciu mikrofonu pracy.
* Aby zainstalować pakiety wymagane do skompilowania i uruchomienia tego przykładu, uruchom następujące polecenie:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="get-the-speech-sdk"></a>Pobierz zestaw SDK funkcji rozpoznawania mowy

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Bieżąca wersja zestawu SDK mowy usługi Cognitive Services to `0.5.0`.

Cognitive Services mowy zestawu SDK dla systemu Linux jest dostępny do kompilowania aplikacji 64-bitowe i 32-bitowych.
Wymagane pliki można pobrać jako plik tar z https://aka.ms/csspeech/linuxbinary.
Pobierz i zainstaluj zestaw SDK w następujący sposób:

1. Wybierz katalog (ścieżka bezwzględna), w którym chcesz umieścić pliki binarne zestawu SDK rozpoznawania mowy i nagłówki.
   Na przykład wybrać ścieżkę `speechsdk` w katalogu głównym:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Jeśli go jeszcze nie istnieje, należy utworzyć katalogu:

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Pobierz i Wyodrębnij `.tar.gz` archiwum przy użyciu plików binarnych zestawu SDK mowy:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Sprawdzanie poprawności zawartości katalogu najwyższego poziomu, wyodrębnione pakietu:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Uwagi dotyczące innych firm — oraz plików licencji powinny być widoczne, a także `include` katalogu dla nagłówków i `lib` katalogu bibliotek.

   [!include[Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-the-sample-code"></a>Dodaj kod przykładowy

1. Dodaj następujący kod do pliku o nazwie `helloworld.cpp`:

  [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-linux/helloworld.cpp#code)]

1. Zastąp ciąg `YourSubscriptionKey` z kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion` z [region](regions.md) skojarzonych z subskrypcją (na przykład `westus` bezpłatnej subskrypcji wersji próbnej).

## <a name="building"></a>Kompilowanie

> [!NOTE]
> Upewnij się, że skopiuj i wklej poniższe polecenia kompilacji, jako _pojedynczy wiersz_.

* Na **x64** maszyny, uruchom następujące polecenie, aby skompilować aplikację:

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* Na **x86** maszyny, uruchom następujące polecenie, aby skompilować aplikację:

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. Konfigurowanie konfiguracji Moduł ładujący ścieżka biblioteki, aby wskazywał Biblioteka zestawu SDK rozpoznawania mowy.

   * Na **x64** maszyny, uruchom:

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * Na **x86** maszyny, uruchom:

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. Uruchom aplikację w następujący sposób:

   ```sh
   ./helloworld
   ```

1. Powinny zostać wyświetlone dane wyjściowe podobne do poniższego:

   ```text
   Say something...
   We recognized: What's the weather
   ```

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj tego przykładu w `quickstart/cpp-linux` folderu.

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz nasze przykłady](speech-sdk.md#get-the-samples)
