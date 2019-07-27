---
title: 'Szybki start: Rozpoznawanie mowy, .NET Framework (Windows) — usługa mowy'
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
ms.openlocfilehash: 47f955734070be4adfe7f58da98265b976e643d5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554158"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Szybki start: Rozpoznawanie mowy przy użyciu zestawu SDK usługi Mowa dla platformy .NET Framework (Windows)

Przewodniki Szybki Start są również dostępne w przypadku zamiany [tekstu na mowę](quickstart-text-to-speech-dotnet-windows.md) i [zamiany mowy](quickstart-translate-speech-dotnetframework-windows.md)na mowę.

W razie potrzeby wybierz inny język programowania i/lub środowisko:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Korzystając z tego przewodnika, możesz utworzyć aplikację konsolową zamieniającą mowę na tekst przy użyciu programu .NET Framework dla systemu Windows oraz zestawu SDK usługi Mowa. Następnie za pomocą mikrofonu komputera będzie możliwa transkrypcja mowy na tekst w czasie rzeczywistym.

Aby uzyskać szybką demonstrację (bez kompilowania projektu programu Visual Studio, jak pokazano poniżej):

Pobierz najnowsze [przykłady Cognitive Services mowy SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk) z usługi GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten projekt, będą potrzebne następujące elementy:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klucz subskrypcji dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).
* Dostęp do mikrofonu komputera

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Dodaj kod przykładowy

1. Otwórz plik `Program.cs` i zastąp automatycznie wygenerowany kod następującym przykładem:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Znajdź i Zamień ciąg `YourSubscriptionKey` na klucz subskrypcji usługi Speech Services.

1. Znajdź i zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z Twoją subskrypcją. Jeśli na przykład używasz bezpłatnej wersji próbnej, region to `westus`.

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu wybierz kolejno pozycje **Kompiluj** > **Kompiluj rozwiązanie**. Kod powinien teraz zostać skompilowany bez błędów.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Kompiluj rozwiązanie](media/sdk/qs-csharp-dotnet-windows-08-build.png "Kompilacja ukończona pomyślnie")

1. Na pasku menu wybierz pozycję **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz **F5**, aby uruchomić aplikację.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Rozpocznij debugowanie](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Uruchamianie aplikacji do debugowania")

1. Zostanie wyświetlone okno konsoli z prośbą o wypowiedź. Powiedz coś w języku angielskim. Twoja Zamiana jest przekazywana do usługi mowy i uzyskanego do tekstu w czasie rzeczywistym. Wynik zostanie wyświetlony w konsoli.

    ![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania")

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
