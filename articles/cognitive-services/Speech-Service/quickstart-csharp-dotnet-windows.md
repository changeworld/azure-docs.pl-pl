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
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: cb140647394858fbc0a9a00ea125365d5b7a08d5
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327049"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Szybki start: Rozpoznawanie mowy przy użyciu zestawu SDK usługi Mowa dla platformy .NET Framework (Windows)

Przewodniki Szybki Start są również dostępne dla [syntezy mowy](quickstart-text-to-speech-dotnet-windows.md) i [tłumaczenia mowy](quickstart-translate-speech-dotnetframework-windows.md).

Jeśli chcesz, wybierz inny język programowania i/lub środowisko:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Korzystając z tego przewodnika, możesz utworzyć aplikację konsolową zamieniającą mowę na tekst przy użyciu programu .NET Framework dla systemu Windows oraz zestawu SDK usługi Mowa. Następnie za pomocą mikrofonu komputera będzie możliwa transkrypcja mowy na tekst w czasie rzeczywistym.

Aby uzyskać szybką demonstrację (bez kompilowania projektu programu Visual Studio zgodnie z opisem w tym artykule), Pobierz najnowsze [przykłady zestawu SDK mowy Cognitive Services](https://github.com/Azure-Samples/cognitive-services-speech-sdk) z usługi GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten projekt, będą potrzebne następujące elementy:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Klucz subskrypcji dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).
* Dostęp do mikrofonu komputera.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Dodaj kod przykładowy

1. Otwórz **program.cs** i Zastąp automatycznie wygenerowany kod tym przykładem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Znajdź ciąg `YourSubscriptionKey` i zastąp go kluczem subskrypcji usługi Speech Services.

1. Znajdź ciąg `YourServiceRegion` i zastąp go [regionem](regions.md) skojarzonym z subskrypcją. Na przykład jeśli używasz bezpłatnej subskrypcji wersji próbnej, region jest `westus`.

1. Na pasku menu wybierz kolejno opcje **plik** > **Zapisz wszystko**.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu wybierz kolejno opcje **kompiluj** > **Kompiluj rozwiązanie** , aby skompilować aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **debuguj** > **Rozpocznij debugowanie** (lub wybierz **F5**), aby uruchomić aplikację **HelloWorld** .

1. Zacznij od angielskiej frazy lub zdania do mikrofonu Twojego urządzenia. Aplikacja przesyła mowę do usługi mowy, co spowoduje wysłanie do wyświetlania tekstu uzyskanego z powrotem do aplikacji.

   ![Interfejs użytkownika rozpoznawania mowy](media/sdk/qs-csharp-dotnet-windows-10-console-output.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Uczenie modelu dla Custom Speech](how-to-custom-speech-train-model.md)
