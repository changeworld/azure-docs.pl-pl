---
title: 'Szybki start: Rozpoznawanie mowy, C++ (macOS) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznać C++ mowę w systemie macOS przy użyciu zestawu Speech SDK
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: d7d35c251848af5cfd5852080d01831085401227
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554132"
---
# <a name="quickstart-recognize-speech-in-c-on-macos-by-using-the-speech-sdk"></a>Szybki start: Rozpoznawanie mowy w C++ systemie macOS przy użyciu zestawu Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule opisano tworzenie aplikacji C++ konsolowej dla macOS 10,13 i nowszych. Używasz [zestawu speech Cognitive Services SDK](speech-sdk.md) , aby transkrypcja mowę do tekstu w czasie rzeczywistym z mikrofonu na komputerze Mac. Aplikacja została skompilowana przy użyciu [zestawu Speech SDK dla macOS](https://aka.ms/csspeech/macosbinary) oraz domyślnego C++ kompilatora `g++`dla komputerów Mac (na przykład).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, potrzebujesz klucza subskrypcji usługi Speech Services. Możesz go uzyskać bezpłatnie. Aby uzyskać szczegółowe informacje [, zobacz Wypróbuj bezpłatne usługi mowy](get-started.md) .

## <a name="install-speech-sdk"></a>Instalowanie zestawu Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Bieżąca wersja zestawu Speech SDK usługi Cognitive Services to `1.6.0`.

Zestaw Speech SDK for macOS można pobrać jako pakiet spakowanej struktury z programu https://aka.ms/csspeech/macosbinary.

Pobierz i zainstaluj zestaw SDK w następujący sposób:

1. Wybierz katalog, do którego pliki zestawów Speech SDK powinny zostać wyodrębnione, i ustaw zmienną środowiskową `SPEECHSDK_ROOT`, aby wskazywała na ten katalog. Ta zmienna ułatwia odwoływanie się do katalogu w przyszłych poleceniach. Jeśli na przykład chcesz używać katalogu `speechsdk` w katalogu macierzystym, użyj polecenia podobnego do poniższego:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Utwórz katalog, jeśli jeszcze nie istnieje.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Pobierz i Wyodrębnij `.zip` Archiwum zawierające strukturę zestawu Speech SDK:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Zweryfikuj zawartość katalogu najwyższego poziomu wyodrębnionego pakietu:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Lista katalogów powinna zawierać pliki powiadomień i licencji innych firm, a także `MicrosoftCognitiveServicesSpeech.framework` katalog.

## <a name="add-sample-code"></a>Dodaj kod przykładowy

1. Utwórz plik źródłowy języka C++ o nazwie `helloworld.cpp` i wklej w nim poniższy kod.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-macos/helloworld.cpp#code)]

1. W tym nowym pliku Zamień ciąg `YourSubscriptionKey` na klucz subskrypcji usługi Speech Services.

1. Zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

## <a name="build-the-app"></a>Tworzenie aplikacji

> [!NOTE]
> Pamiętaj o wprowadzeniu poniższych poleceń jako _pojedynczego wiersza polecenia_. Najprostszym sposobem wykonania tej czynności jest skopiowanie polecenia przy użyciu przycisku **Kopiuj** obok danego polecenia, a następnie wklejenie go w oknie powłoki.

* Uruchom następujące polecenie, aby skompilować aplikację.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>Uruchamianie aplikacji

1. Skonfiguruj ścieżkę biblioteki modułu ładującego tak, aby wskazywała bibliotekę zestawu Speech SDK.

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. Uruchom aplikację.

   ```sh
   ./helloworld
   ```

1. W oknie konsoli zostanie wyświetlony monit o wypowiedzenie tekstu. Wypowiedz zwrot lub zdanie w języku angielskim. Twoja Zamiana jest przekazywana do usługi mowy i uzyskanego do tekstu, który pojawia się w tym samym oknie.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C++ w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
