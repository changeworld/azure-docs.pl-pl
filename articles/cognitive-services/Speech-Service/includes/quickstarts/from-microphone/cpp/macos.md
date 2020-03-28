---
title: 'Szybki start: rozpoznawanie mowy z mikrofonu, C++ (macOS) — usługa mowy'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: wolfma
ms.openlocfilehash: 96836c494cdf8f3df1621ee0e2eea6c6f64a039c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78926004"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz:

> [!div class="checklist"]
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=macos)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania dźwięku

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Utwórz plik źródłowy języka C++ o nazwie `helloworld.cpp` i wklej w nim poniższy kod.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/macos/from-microphone/helloworld.cpp#code)]

1. W nowym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji usługi rozpoznawania mowy.

1. Zastąp ciąg `YourServiceRegion` **identyfikatorem Region** z [regionu skojarzonego](https://aka.ms/speech/sdkregion) z subskrypcją (na przykład `westus` dla bezpłatnej subskrypcji próbnej).

> [!NOTE]
> SDK mowy domyślnie rozpoznaje przy użyciu en-us dla języka, zobacz [Określanie języka źródłowego mowy do tekstu, aby](../../../../how-to-specify-source-language.md) uzyskać informacje na temat wybierania języka źródłowego.

## <a name="build-the-app"></a>Kompilacja aplikacji

> [!NOTE]
> Pamiętaj o wprowadzeniu poniższych poleceń jako _pojedynczego wiersza polecenia_. Najprostszym sposobem wykonania tej czynności jest skopiowanie polecenia przy użyciu przycisku **Kopiuj** obok danego polecenia, a następnie wklejenie go w oknie powłoki.

* Uruchom następujące polecenie, aby utworzyć aplikację.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>Uruchomienie aplikacji

1. Skonfiguruj ścieżkę biblioteki modułu ładującego tak, aby wskazywała bibliotekę zestawu Speech SDK.

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
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

[!INCLUDE [footer](./footer.md)]