---
title: 'Szybki Start: synteza mowy C++ , (Linux) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić funkcję C++ syntezy mowy w systemie Linux przy użyciu zestawu Speech SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: 0846af20a2ee993742f648840bcbe49e187f6db9
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803172"
---
# <a name="quickstart-synthesize-speech-in-c-on-linux-by-using-the-speech-sdk"></a>Szybki Start: synteza mowy C++ w systemie Linux przy użyciu zestawu Speech SDK

Przewodniki Szybki Start są również dostępne do [rozpoznawania mowy](quickstart-cpp-linux.md).

W tym artykule opisano tworzenie aplikacji C++ konsolowej dla systemu Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9). Używasz [zestawu speech Cognitive Services SDK](speech-sdk.md) , aby wypróbować mowę z tekstu w czasie rzeczywistym i odtwarzać mowę na głośniku komputera. Aplikacja jest kompilowana przy użyciu [zestawu Speech SDK dla systemu Linux](https://aka.ms/csspeech/linuxbinary) i kompilatora języka C++ dystrybucji systemu Linux (na przykład `g++`).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, potrzebujesz klucza subskrypcji usługi Speech Services. Możesz go uzyskać bezpłatnie. Aby uzyskać szczegółowe informacje [, zobacz Wypróbuj bezpłatne usługi mowy](get-started.md) .

## <a name="install-speech-sdk"></a>Instalowanie zestawu Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Zestawu Speech SDK dla systemu Linux można używać do kompilowania aplikacji 64-bitowych i 32-bitowych. Wymagane biblioteki i pliki nagłówkowe można pobrać jako plik tar ze strony https://aka.ms/csspeech/linuxbinary.

Pobierz i zainstaluj zestaw SDK w następujący sposób:

1. Upewnij się, że zależności zestawu SDK zostały zainstalowane.

   * W systemie Ubuntu:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * W programie Debian 9:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

1. Wybierz katalog, do którego pliki zestawów Speech SDK powinny zostać wyodrębnione, i ustaw zmienną środowiskową `SPEECHSDK_ROOT`, aby wskazywała na ten katalog. Ta zmienna ułatwia odwoływanie się do katalogu w przyszłych poleceniach. Jeśli na przykład chcesz używać katalogu `speechsdk` w katalogu macierzystym, użyj polecenia podobnego do poniższego:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Utwórz katalog, jeśli jeszcze nie istnieje.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Pobierz i wyodrębnij archiwum `.tar.gz` zawierające pliki binarne zestawu Speech SDK:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Zweryfikuj zawartość katalogu najwyższego poziomu wyodrębnionego pakietu:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Wykaz katalogów powinien zawierać pliki licencji i powiadomienia innych firm, a także katalog `include` zawierający pliki nagłówkowe (`.h`) i katalog `lib` zawierający biblioteki.

   [!INCLUDE [Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Utwórz plik źródłowy języka C++ o nazwie `helloworld.cpp` i wklej w nim poniższy kod.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-linux/helloworld.cpp#code)]

1. W tym nowym pliku Zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji usługi Speech Services.

1. Zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

## <a name="build-the-app"></a>Kompilacja aplikacji

> [!NOTE]
> Pamiętaj o wprowadzeniu poniższych poleceń jako _pojedynczego wiersza polecenia_. Najprostszym sposobem wykonania tej czynności jest skopiowanie polecenia przy użyciu przycisku **Kopiuj** obok danego polecenia, a następnie wklejenie go w oknie powłoki.

* W systemie **x64** (wersja 64-bitowa) uruchom poniższe polecenie, aby skompilować aplikację.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* W systemie **x86** (wersja 32-bitowa) uruchom poniższe polecenie, aby skompilować aplikację.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
  ```

* W systemie **arm64** (64-bitowym) Uruchom następujące polecenie, aby skompilować aplikację.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/arm64" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>Uruchamianie aplikacji

1. Skonfiguruj ścieżkę biblioteki modułu ładującego tak, aby wskazywała bibliotekę zestawu Speech SDK.

   * W systemie **x64** (wersja 64-bitowa) wprowadź następujące polecenie.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * W systemie **x86** (wersja 32-bitowa) wprowadź to polecenie.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

   * W systemie **arm64** (64-bitowym) wprowadź następujące polecenie.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/arm64"
     ```

1. Uruchom aplikację.

   ```sh
   ./helloworld
   ```

1. W oknie konsoli zostanie wyświetlony monit z monitem o wpisanie tekstu. Wpisz kilka wyrazów lub zdania. Tekst, który wpisano, jest przesyłany do usługi mowy i można go wyrównać do mowy, który jest odtwarzany na głośniku.

   ```text
   Type some text that you want to speak...
   > hello
   Speech synthesized to speaker for text [hello]
   Press enter to exit...
   ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C++ w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Dostosuj czcionki głosowe](how-to-customize-voice-font.md)
- [Rejestruj przykłady głosu](record-custom-voice-samples.md)
