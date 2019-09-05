---
title: 'Szybki start: Funkcja syntezy mowy C++ (Windows) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić C++ funkcję syntezy mowy na pulpicie systemu Windows przy użyciu zestawu Speech SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: 686b21d3e02266af77687778c32f0d1ca6d55154
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383066"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Szybki start: Wykorzystaj mowę C++ w systemie Windows przy użyciu zestawu Speech SDK

Przewodniki Szybki Start są również dostępne dla [funkcji rozpoznawania mowy](quickstart-cpp-windows.md) i [tłumaczenia mowy](quickstart-translate-speech-cpp-windows.md).

W tym artykule opisano tworzenie aplikacji konsolowej w języku C++ dla systemu Windows. Używasz [zestawu speech Cognitive Services SDK](speech-sdk.md) , aby wypróbować mowę z tekstu w czasie rzeczywistym i odtwarzać mowę na głośniku komputera. Aplikacja jest zbudowana z [pakietem NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget) i Microsoft Visual Studio 2019 (w dowolnej wersji).

Aby uzyskać pełną listę języków/głosów dostępnych dla syntezy mowy, zobacz temat [Obsługa języków](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, potrzebujesz klucza subskrypcji usługi Speech Services. Możesz go uzyskać bezpłatnie. Aby uzyskać szczegółowe informacje [, zobacz Wypróbuj bezpłatne usługi mowy](get-started.md) .

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Dodaj kod przykładowy

1. Otwórz plik źródłowy **helloworld.cpp**.

1. Zastąp cały kod następującym fragmentem kodu:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Na pasku menu wybierz kolejno opcje **plik** > **Zapisz wszystko**.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu wybierz kolejno opcje **Kompiluj** > kompilacje**rozwiązanie** , aby skompilować aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **Debuguj** > **Rozpocznij debugowanie** (lub naciśnij klawisz **F5**), aby uruchomić aplikację **HelloWorld** .

1. Wpisz frazę lub zdanie w języku angielskim. Aplikacja przesyła swój tekst do usług mowy, które wysyłają do aplikacji aplikację, która ma zostać przegrana przez prelegenta.

   ![Dane wyjściowe konsoli po pomyślnym przeprowadzeniu syntezy mowy](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Następne kroki

Dodatkowe przykłady, takie jak zapisywanie mowy w pliku audio, są dostępne w serwisie GitHub.

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C++ w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Tworzenie niestandardowego głosu](how-to-custom-voice-create-voice.md)
- [Rejestruj niestandardowe przykłady głosu](record-custom-voice-samples.md)
