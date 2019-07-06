---
title: 'Szybki start: rozpoznawanie mowy, .NET Framework (Windows) — usługi mowy'
titleSuffix: Azure Cognitive Services
description: Korzystając z tego przewodnika, możesz utworzyć aplikację konsolową zamieniającą mowę na tekst przy użyciu programu .NET Framework dla systemu Windows oraz zestawu SDK usługi Mowa. Następnie za pomocą mikrofonu komputera będzie możliwa transkrypcja mowy na tekst w czasie rzeczywistym.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: d8738357a3bad6626ef6d79248aef1c4d2cb1ead
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603085"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Szybki start: Rozpoznawanie mowy przy użyciu zestawu SDK usługi Mowa dla platformy .NET Framework (Windows)

Przewodniki Szybki Start są również dostępne dla [zamiany tekstu na mowę](quickstart-text-to-speech-dotnet-windows.md) i [tłumaczenia mowy](quickstart-translate-speech-dotnetframework-windows.md).

Jeśli to konieczne, należy wybrać inny język programowania i/lub środowiska:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Korzystając z tego przewodnika, możesz utworzyć aplikację konsolową zamieniającą mowę na tekst przy użyciu programu .NET Framework dla systemu Windows oraz zestawu SDK usługi Mowa. Następnie za pomocą mikrofonu komputera będzie możliwa transkrypcja mowy na tekst w czasie rzeczywistym.

Szybkie demonstracyjne (bez kompilowania projektu programu Visual Studio, samodzielnie, jak pokazano poniżej):

Dostęp do najnowszych [mowy Cognitive Services — przykłady zestawu SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk) z usługi GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten projekt, będą potrzebne następujące elementy:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klucz subskrypcji dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).
* Dostęp do mikrofonu komputera

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz plik `Program.cs` i zastąp automatycznie wygenerowany kod następującym przykładem:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Znajdź i Zastąp ciąg `YourSubscriptionKey` z kluczem subskrypcji usług przetwarzania mowy.

1. Znajdź i zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z Twoją subskrypcją. Jeśli na przykład używasz bezpłatnej wersji próbnej, region to `westus`.

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu wybierz kolejno pozycje **Kompiluj** > **Kompiluj rozwiązanie**. Kod powinien teraz zostać skompilowany bez błędów.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Kompiluj rozwiązanie](media/sdk/qs-csharp-dotnet-windows-08-build.png "Kompilacja ukończona pomyślnie")

1. Na pasku menu wybierz pozycję **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz **F5**, aby uruchomić aplikację.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Rozpocznij debugowanie](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Uruchamianie aplikacji do debugowania")

1. Zostanie wyświetlone okno konsoli z prośbą o wypowiedź. Powiedz coś w języku angielskim. Mowa jest przesyłane do usług przetwarzania mowy i przetłumaczone na tekst w czasie rzeczywistym. Wynik zostanie wyświetlony w konsoli.

    ![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania")

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
