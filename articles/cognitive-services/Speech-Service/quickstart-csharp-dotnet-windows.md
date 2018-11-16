---
title: 'Szybki start: rozpoznawanie i transkrypcja mowy, .NET Framework (Windows) — usługa Mowa'
titleSuffix: Azure Cognitive Services
description: Korzystając z tego przewodnika, możesz utworzyć aplikację konsolową zamieniającą mowę na tekst przy użyciu programu .NET Framework dla systemu Windows oraz zestawu SDK usługi Mowa. Następnie za pomocą mikrofonu komputera będzie możliwa transkrypcja mowy na tekst w czasie rzeczywistym.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/05/2018
ms.author: wolfma
ms.openlocfilehash: 502f59c88808480f26e6ea5a6d2c5b362c78869b
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683503"
---
# <a name="quickstart-recognize-and-transcribe-speech-using-the-speech-sdk-and-net-framework-windows"></a>Szybki start: rozpoznawanie i transkrypcja mowy przy użyciu zestawu SDK usługi Mowa i programu .NET Framework (Windows)

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Korzystając z tego przewodnika, możesz utworzyć aplikację konsolową zamieniającą mowę na tekst przy użyciu programu .NET Framework dla systemu Windows oraz zestawu SDK usługi Mowa. Następnie za pomocą mikrofonu komputera będzie możliwa transkrypcja mowy na tekst w czasie rzeczywistym.

Ten przewodnik Szybki start wymaga [konta usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z włączoną usługą Mowa firmy Microsoft. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten projekt, będą potrzebne następujące elementy:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klucz subskrypcji dla usługi Mowa
* Dostęp do mikrofonu komputera

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz plik `Program.cs` i zastąp automatycznie wygenerowany kod następującym przykładem:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Znajdź i zastąp ciąg `YourSubscriptionKey` swoim kluczem subskrypcji usługi Mowa.

1. Znajdź i zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z Twoją subskrypcją. Jeśli na przykład używasz bezpłatnej wersji próbnej, region to `westus`.

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu wybierz kolejno pozycje **Kompiluj** > **Kompiluj rozwiązanie**. Kod powinien teraz zostać skompilowany bez błędów.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Kompiluj rozwiązanie](media/sdk/qs-csharp-dotnet-windows-08-build.png "Kompilacja ukończona pomyślnie")

1. Na pasku menu wybierz pozycję **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz **F5**, aby uruchomić aplikację.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Rozpocznij debugowanie](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Uruchamianie aplikacji do debugowania")

1. Zostanie wyświetlone okno konsoli z prośbą o wypowiedź. Powiedz coś w języku angielskim. Wypowiedź będzie przesyłana do usługi Mowa i transkrybowana na tekst w czasie rzeczywistym. Wynik zostanie wyświetlony w konsoli.

    ![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Kod jest dostępny w folderze `quickstart/csharp-dotnet-windows`.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Recognize intents from speech by using the Speech SDK for C#](how-to-recognize-intents-from-speech-csharp.md) (Rozpoznawanie intencji z mowy przy użyciu zestawu Speech SDK dla języka C#)

## <a name="see-also"></a>Zobacz też

- [Translate speech with the Cognitive Services Speech SDK for C#](how-to-translate-speech-csharp.md) (Tłumaczenie mowy za pomocą zestawu Speech SDK usługi Cognitive Services dla języka C#)
- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
