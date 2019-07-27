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
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: 6936d8f93221402fca9b250d09a842ce753e0e12
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559316"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Szybki start: Wykorzystaj mowę C++ w systemie Windows przy użyciu zestawu Speech SDK

Przewodniki Szybki Start są również dostępne do [rozpoznawania mowy](quickstart-cpp-windows.md) i [translacji mowy](quickstart-translate-speech-cpp-windows.md).

W tym artykule opisano tworzenie aplikacji konsolowej w języku C++ dla systemu Windows. Używasz [zestawu speech Cognitive Services SDK](speech-sdk.md) , aby wypróbować mowę z tekstu w czasie rzeczywistym i odtwarzać mowę na głośniku komputera. Aplikacja będzie kompilowana przy użyciu [pakietu NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget) i programu Microsoft Visual Studio 2017 (w dowolnej wersji).

Funkcja opisana w tym artykule jest dostępna z [zestawu Speech SDK 1.5.0](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/1.5.0).

Aby uzyskać pełną listę języków/głosów dostępnych dla syntezy mowy, zobacz temat [Obsługa języków](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, potrzebujesz klucza subskrypcji usługi Speech Services. Możesz go uzyskać bezpłatnie. Aby uzyskać szczegółowe informacje [, zobacz Wypróbuj bezpłatne usługi mowy](get-started.md) .

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Dodaj kod przykładowy

1. Otwórz plik źródłowy *helloworld.cpp*. Zastąp cały kod poniżej początkowej instrukcji include (`#include "stdafx.h"` lub `#include "pch.h"`) następującymi instrukcjami:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Skompiluj aplikację. Na pasku menu wybierz kolejno pozycje **Kompiluj** > **Kompiluj rozwiązanie**. Kod powinien zostać skompilowany bez błędów.

   ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Kompiluj rozwiązanie](media/sdk/qs-cpp-windows-06-build.png)

1. Uruchom aplikację. Na pasku menu wybierz kolejno pozycje **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz **F5**.

   ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Rozpocznij debugowanie](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Zostanie wyświetlone okno konsoli z monitem o wpisanie tekstu. Wpisz kilka wyrazów lub zdania. Tekst, który wpisano, jest przesyłany do usługi mowy i można go wyrównać do mowy, który jest odtwarzany na głośniku.

   ![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnej syntezie](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Kolejne kroki

Dodatkowe przykłady, takie jak zapisywanie mowy w pliku audio, są dostępne w serwisie GitHub.

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C++ w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Dostosuj czcionki głosowe](how-to-customize-voice-font.md)
- [Rejestruj przykłady głosu](record-custom-voice-samples.md)
