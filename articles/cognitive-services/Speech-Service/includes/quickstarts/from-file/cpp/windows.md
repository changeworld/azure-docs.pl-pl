---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: dapine
ms.openlocfilehash: 33173390b9bdd874936ac6625e1755b96297cd7c
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671857"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz plik źródłowy **helloworld.cpp**.

1. Zastąp cały kod następującym fragmentem kodu:
   
   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion` **identyfikatorem Region** z [regionu skojarzonego](https://aka.ms/speech/sdkregion) z subskrypcją (na przykład `westus` dla bezpłatnej subskrypcji próbnej).

1. Zastąp ciąg `whatstheweatherlike.wav` własną nazwę pliku.

1. Na pasku menu wybierz pozycję Zapisz**wszystkie** **pliki** > .

> [!NOTE]
> SDK mowy domyślnie rozpoznaje przy użyciu en-us dla języka, zobacz [Określanie języka źródłowego mowy do tekstu, aby](../../../../how-to-specify-source-language.md) uzyskać informacje na temat wybierania języka źródłowego.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu **wybierz** > build**build solution,** aby utworzyć aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **debugowanie** > **rozpocznij debugowanie** (lub naciśnij klawisz **F5),** aby uruchomić aplikację **helloworld.**

1. Plik audio jest przesyłany do usługi Mowy, a pierwsza wypowiedź w pliku jest transkrybowana na tekst, który pojawia się w tym samym oknie.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]