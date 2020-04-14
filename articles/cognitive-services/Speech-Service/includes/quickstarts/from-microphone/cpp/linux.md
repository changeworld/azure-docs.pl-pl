---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: bdff6c0e65d341bb4948ff772171d8b42728ff0a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275286"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Tworzenie zasobu mowy platformy Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=linux&pivots=programming-language-cpp)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania dźwięku

## <a name="source-code"></a>Kod źródłowy

Utwórz plik źródłowy języka C++ o nazwie *helloworld.cpp*i wklej do niego następujący kod.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/linux/from-microphone/helloworld.cpp#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Wyjaśnienie kodu

[!INCLUDE [code explanation](../code-explanation.md)]

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

* W systemie **ARM64** (64-bitowym) uruchom następujące polecenie, aby utworzyć aplikację.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/arm64" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>Uruchomienie aplikacji

1. Skonfiguruj ścieżkę biblioteki modułu ładującego tak, aby wskazywała bibliotekę zestawu Speech SDK.

   * W systemie **x64** (wersja 64-bitowa) wprowadź następujące polecenie.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * W systemie **x86** (wersja 32-bitowa) wprowadź to polecenie.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

   * W systemie **ARM64** (64-bitowym) wprowadź następujące polecenie.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/arm64"
     ```

1. Uruchom aplikację.

   ```sh
   ./helloworld
   ```

1. W oknie konsoli zostanie wyświetlony monit o wypowiedzenie tekstu. Wypowiedz zwrot lub zdanie w języku angielskim. Wypowiedź zostanie przesłana do usługi rozpoznawania mowy i nastąpi jest transkrypcja na tekst, który zostanie wyświetlony w tym samym oknie.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

