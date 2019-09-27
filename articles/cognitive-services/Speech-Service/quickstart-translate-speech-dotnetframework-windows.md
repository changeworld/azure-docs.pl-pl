---
title: 'Szybki start: Tłumaczenie mowy, C# (.NET Framework Windows) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start utworzysz aplikację .NET Framework, aby przechwycić mowę użytkownika, przetłumaczyć ją na inny język i wyjście tekstu do wiersza polecenia. Ten przewodnik jest przeznaczony dla użytkowników systemu Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: erhopf
ms.openlocfilehash: bb710a3e3adb13aa3999c13043c8bb93f6b885f1
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327343"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework-windows"></a>Szybki start: Tłumaczenie mowy przy użyciu zestawu Speech SDK dla .NET Framework (system Windows)

Przewodniki Szybki Start są również dostępne dla [funkcji rozpoznawania mowy](quickstart-csharp-dotnet-windows.md) i [syntezy mowy](quickstart-text-to-speech-dotnet-windows.md).

W tym przewodniku szybki start utworzysz aplikację .NET Framework, która przechwytuje mowę użytkownika z mikrofonu komputera, przekształci mowę i przekształca przetłumaczony tekst w wierszu polecenia w czasie rzeczywistym. Ta aplikacja może działać na 32-bitowym lub 64-bitowym systemie Windows i została skompilowana przy użyciu [pakietu NuGet zestawu mowy SDK](https://aka.ms/csspeech/nuget) i Microsoft Visual Studio 2019.

Aby uzyskać pełną listę języków dostępnych na potrzeby tłumaczenia mowy, zapoznaj się z [listą obsługiwanych języków](language-support.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Dodaj kod przykładowy

1. Otwórz **program.cs**i Zastąp cały kod następującym kodem.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Znajdź ciąg `YourSubscriptionKey` i zastąp go kluczem subskrypcji.

1. Znajdź ciąg `YourServiceRegion` i zastąp go [regionem](regions.md) skojarzonym z subskrypcją. Na przykład jeśli używasz bezpłatnej subskrypcji wersji próbnej, region jest `westus`.

1. Na pasku menu wybierz kolejno opcje **plik** > **Zapisz wszystko**.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu wybierz kolejno opcje **kompiluj** > **Kompiluj rozwiązanie** , aby skompilować aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **debuguj** > **Rozpocznij debugowanie** (lub wybierz **F5**), aby uruchomić aplikację **HelloWorld** .

1. Zacznij od angielskiej frazy lub zdania do mikrofonu Twojego urządzenia. Aplikacja przesyła mowę do usługi mowy, która tłumaczy mowę na tekst w innym języku (w tym przypadku w języku niemieckim). Usługa mowy wysyła przetłumaczony tekst z powrotem do aplikacji, co spowoduje wyświetlenie tłumaczenia w oknie.

   ![Interfejs użytkownika tłumaczenia mowy](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png)

## <a name="next-steps"></a>Następne kroki

Dodatkowe przykłady, dotyczące między innymi odczytywania mowy z pliku audio i zwracania przetłumaczonego tekstu jako syntetyzowanej mowy, są dostępne w usłudze GitHub.

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Uczenie modelu dla Custom Speech](how-to-custom-speech-train-model.md)
