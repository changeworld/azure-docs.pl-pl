---
title: 'Szybki start: synteza mowy, C++ (Windows) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak syntetyzować mowę w języku C++ na pulpicie systemu Windows przy użyciu pakietu SDK mowy
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: ab2193a1ea34b176e5f97806f0099dfc86d75965
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925838"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=windows)

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz plik źródłowy **helloworld.cpp**.

1. Zastąp cały kod następującym fragmentem kodu:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/text-to-speech/helloworld/helloworld.cpp#code)]

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion`[regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Na pasku menu wybierz pozycję Zapisz**wszystkie** **pliki** > .

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu **wybierz** > build**build solution,** aby utworzyć aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **debugowanie** > **rozpocznij debugowanie** (lub naciśnij klawisz **F5),** aby uruchomić aplikację **helloworld.**

1. Wpisz angielską frazę lub zdanie. Aplikacja przesyła tekst do usługi Mowy, która wysyła syntetyzowaną mowę do aplikacji w celu odtworzenia na głośniku.

   ![Wyjście konsoli po udanej syntezie mowy](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zobacz też

- [Tworzenie niestandardowego głosu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Nagrywanie niestandardowych próbek głosu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
