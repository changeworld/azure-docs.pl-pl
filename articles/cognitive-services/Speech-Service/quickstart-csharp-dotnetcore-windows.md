---
title: 'Szybki Start: Rozpoznawanie mowy w języku C# w ramach platformy .NET Core w Windows za pomocą mowy zestawem SDK Cognitive Services | Dokumentacja firmy Microsoft'
titleSuffix: Microsoft Cognitive Services
description: Dowiedz się, jak rozpoznawanie mowy w języku C# w ramach platformy .NET Core w Windows przy użyciu Cognitive Services SDK rozpoznawania mowy
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 50cf1bbbe529b30da6bfe39281d11eee1c788dd8
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259164"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-using-the-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy w języku C# w ramach platformy .NET Core w Windows przy użyciu zestawu SDK rozpoznawania mowy

W tym artykule dowiesz się, jak utworzyć aplikację konsolową C# dla platformy .NET Core w Windows transkrypcja mowy na tekst za pomocą Cognitive Services SDK rozpoznawania mowy.
Aplikacja jest skompilowana przy użyciu [Microsoft Cognitive Services mowy zestawu SDK pakietu NuGet](https://aka.ms/csspeech/nuget) i Microsoft Visual Studio 2017.

> [!NOTE]
> .NET core to platforma .NET typu open source, Międzyplatformowe Implementowanie [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) specyfikacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji dla usługi mowy. Zobacz [bezpłatnie wypróbować usługę rozpoznawania mowy](get-started.md).
* Komputera z systemem Windows przy użyciu mikrofonu pracy.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition lub nowszy.
* **Programowanie dla wielu platform .NET Core** obciążenia w programie Visual Studio. Możesz ją włączyć w **narzędzia** \> **Pobierz narzędzia i funkcje**.

  ![Włącz programowanie dla wielu platform .NET Core](media/sdk/vs-enable-net-core-workload.png)

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. W programie Visual Studio 2017 Utwórz nowy Visual C# aplikacji Konsolowej .NET Core. W **nowy projekt** okno dialogowe, w lewym okienku rozwiń **zainstalowane** \> **Visual C#** \> **platformy .NET Core**, a następnie wybierz **Aplikacja konsoli (.NET Core)**. Wprowadź nazwę projektu *helloworld*.

    ![Tworzenie języka Visual C# Aplikacja konsoli (.NET Core)](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "tworzenie Visual C# Aplikacja konsoli (.NET Core)")

1. Zainstalowania i odwołania [pakietu NuGet zestawu SDK mowy](https://aka.ms/csspeech/nuget). W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy i wybierz **Zarządzaj pakietami NuGet dla rozwiązania**.

    ![Kliknij prawym przyciskiem myszy Zarządzaj pakietami NuGet dla rozwiązania](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Zarządzaj pakietami NuGet dla rozwiązania")

1. W prawym górnym rogu w **źródła pakietu** pól, zaznacz **Nuget.org**. Wyszukiwanie i instalowanie `Microsoft.CognitiveServices.Speech` pakiet i zainstaluj go na **helloworld** projektu.

    ![Zainstaluj pakiet NuGet Microsoft.CognitiveServices.Speech](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-0.5.0.png "pakietu Nuget instalacji")

1. Zaakceptuj licencję w oknie dialogowym.

    ![Akceptuje](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "akceptuje")

## <a name="add-the-sample-code"></a>Dodaj kod przykładowy

1. Otwórz `Program.cs` w programu Visual Studio projektu i Zastąp cały kod w tym pliku następującym kodem.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore-windows/helloworld/Program.cs#code)]

1. Zastąp ciąg `YourSubscriptionKey` z kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion` z [region](regions.md) skojarzonych z subskrypcją (na przykład `westus` bezpłatnej subskrypcji wersji próbnej).

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Skompiluj aplikację. Na pasku menu wybierz **kompilacji** > **Kompiluj rozwiązanie**. Kod powinien teraz skompilować bez błędów.

    ![Pomyślnej kompilacji](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "pomyślnej kompilacji")

1. Uruchom aplikację. Na pasku menu wybierz **debugowania** > **Rozpocznij debugowanie**, lub naciśnij **F5**.

    ![Uruchom aplikację do debugowania](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "uruchomić aplikację do debugowania")

1. Zostanie wyświetlone okno konsoli monitem powiedzieć coś (w języku angielskim). Rozpoznany tekst pojawi się w tym samym oknie.

    ![Dane wyjściowe konsoli po pomyślnym rozpoznawania](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "dane wyjściowe konsoli po pomyślnym rozpoznawania")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj tego przykładu w `quickstart/csharp-dotnetcore-windows` folderu.

## <a name="next-steps"></a>Kolejne kroki

- [Tłumaczenie mowy](how-to-translate-speech.md)
- [Dostosowywanie modeli akustycznych](how-to-customize-acoustic-models.md)
- [Dostosowywanie modeli językowych](how-to-customize-language-model.md)
