---
title: 'Szybki Start: Rozpoznawanie mowy z mikrofonu, C++ (macOS) — usługa mowy'
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
ms.openlocfilehash: 6f937683490df29e39bd2dc0a39cfc76d64ef41d
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796020"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Konfigurowanie środowiska deweloperskiego](../../../../quickstarts/setup-platform.md?tabs=macos)
> * [Tworzenie pustego przykładowego projektu](../../../../quickstarts/create-project.md?tabs=macos)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania audio

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Utwórz plik źródłowy języka C++ o nazwie `helloworld.cpp` i wklej w nim poniższy kod.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/macos/from-microphone/helloworld.cpp#code)]

1. W tym nowym pliku Zastąp ciąg `YourSubscriptionKey` za pomocą klucza subskrypcji usługi Speech Services.

1. Zastąp ciąg `YourServiceRegion` [regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

> [!NOTE]
> Zestaw Speech SDK będzie domyślnie rozpoznawał użycie języka en-us w celu uzyskania informacji na temat wybierania [języka źródłowego.](../../../../how-to-specify-source-language.md)

## <a name="build-the-app"></a>Kompilacja aplikacji

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

1. W oknie konsoli zostanie wyświetlony monit o wypowiedzenie tekstu. Wymów frazę lub zdanie w języku angielskim. Twoja Zamiana jest przekazywana do usługi mowy i uzyskanego do tekstu, który pojawia się w tym samym oknie.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]