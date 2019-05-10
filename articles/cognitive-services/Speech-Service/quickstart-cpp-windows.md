---
title: 'Szybki start: rozpoznawanie mowy, C++ (Windows) — usługi mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w języku C++ na komputerze z systemem Windows przy użyciu zestawu SDK usługi Mowa
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: 672a691cdad5c83583ea840907fa7dd193c7ae3a
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466963"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Szybki start: rozpoznawanie mowy w języku C++ w systemie Windows przy użyciu zestawu Speech SDK

Przewodniki Szybki Start są również dostępne dla [zamiany tekstu na mowę](quickstart-text-to-speech-cpp-windows.md) i [tłumaczenia mowy](quickstart-translate-speech-cpp-windows.md).

Jeśli to konieczne, należy wybrać inny język programowania i/lub środowiska:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule opisano tworzenie aplikacji konsolowej w języku C++ dla systemu Windows. Użyjesz zestawu [Speech SDK](speech-sdk.md) usługi Cognitive Services do transkrypcji na tekst w czasie rzeczywistym mowy z mikrofonu komputera. Aplikacja będzie kompilowana przy użyciu [pakietu NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget) i programu Microsoft Visual Studio 2017 (w dowolnej wersji).

## <a name="prerequisites"></a>Wymagania wstępne

Należy klucz subskrypcji usług przetwarzania mowy, aby ukończyć ten przewodnik Szybki Start. Możesz go uzyskać bezpłatnie. Zobacz [bezpłatnego wypróbowania usługi mowy](get-started.md) Aby uzyskać szczegółowe informacje.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Dodaj kod przykładowy

1. Otwórz plik źródłowy *helloworld.cpp*. Zastąp cały kod poniżej początkowej instrukcji include (`#include "stdafx.h"` lub `#include "pch.h"`) następującymi instrukcjami:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Skompiluj aplikację. Na pasku menu wybierz kolejno pozycje **Kompiluj** > **Kompiluj rozwiązanie**. Kod powinien zostać skompilowany bez błędów.

   ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Kompiluj rozwiązanie](media/sdk/qs-cpp-windows-06-build.png)

1. Uruchom aplikację. Na pasku menu wybierz kolejno pozycje **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz **F5**.

   ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Rozpocznij debugowanie](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Zostanie wyświetlone okno konsoli z monitem o wypowiedź ustną. Wypowiedz zwrot lub zdanie w języku angielskim. Mowa jest przesyłane do usług przetwarzania mowy i przetłumaczone na tekst, który pojawia się w tym samym oknie.

   ![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Kolejne kroki

Dodatkowe przykłady, dotyczące np. sposobu odczytywania mowy z pliku audio, są dostępne w witrynie GitHub.

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C++ w serwisie GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
