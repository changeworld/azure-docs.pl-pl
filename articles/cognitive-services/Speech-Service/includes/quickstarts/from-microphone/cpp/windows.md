---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 2ce40495617ec5a1024fd3b4b9faeec2d1c8ff06
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275494"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Tworzenie zasobu mowy platformy Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania dźwięku

## <a name="source-code"></a>Kod źródłowy

Utwórz plik źródłowy języka C++ o nazwie *helloworld.cpp*i wklej do niego następujący kod.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Wyjaśnienie kodu

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Tworzenie i uruchamianie aplikacji

1. Na pasku menu **wybierz** > build**build solution,** aby utworzyć aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **debugowanie** > **rozpocznij debugowanie** (lub naciśnij klawisz <kbd>F5),</kbd>aby uruchomić aplikację **helloworld.**

1. Wypowiedz zwrot lub zdanie w języku angielskim. Aplikacja przesyła mowy do usługi mowy, która transkrybuje tekst i wysyła go z powrotem do aplikacji do wyświetlania.

   ![Wyjście konsoli po pomyślnym rozpoznaniu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]