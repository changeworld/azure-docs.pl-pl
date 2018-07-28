---
title: 'Szybki Start: Rozpoznawanie mowy w języku C# na platformie .NET Framework na Windows przy użyciu Cognitive Services SDK rozpoznawania mowy'
titleSuffix: Microsoft Cognitive Services
description: Dowiedz się, jak rozpoznawanie mowy w języku C# na platformie .NET Framework na Windows przy użyciu Cognitive Services SDK rozpoznawania mowy
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 587ae9f6452f85dee867047e47ccc272ee508b81
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325194"
---
# <a name="quickstart-recognize-speech-in-c-under-net-framework-on-windows-using-the-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy w języku C# na platformie .NET Framework na Windows przy użyciu zestawu SDK rozpoznawania mowy

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule dowiesz się, jak utworzyć aplikację konsolową C# dla platformy .NET Framework na Windows transkrypcja mowy na tekst za pomocą Cognitive Services SDK rozpoznawania mowy.
Aplikacja jest skompilowana przy użyciu [Microsoft Cognitive Services mowy zestawu SDK pakietu NuGet](https://aka.ms/csspeech/nuget) i Microsoft Visual Studio 2017.

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji dla usługi mowy. Zobacz [bezpłatnie wypróbować usługę rozpoznawania mowy](get-started.md).
* Komputera z systemem Windows przy użyciu mikrofonu pracy.
* Program Visual Studio 2017, wersja Community Edition lub nowszy.
* **Programowanie aplikacji klasycznych dla platformy .NET** obciążenia w programie Visual Studio. Możesz ją włączyć w **narzędzia** \> **Pobierz narzędzia i funkcje**.

  ![Włącz programowanie aplikacji klasycznych dla platformy .NET](media/sdk/vs-enable-net-desktop-workload.png)

  ![Włącz programowanie dla wielu platform .NET Core](media/sdk/vs-enable-net-desktop-workload.png)

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. W programie Visual Studio 2017 Utwórz nową Visual C# Console aplikację. W **nowy projekt** okno dialogowe, w lewym okienku rozwiń **zainstalowane** \> **Visual C#** \> **Windows Desktop** , a następnie wybierz **Aplikacja konsoli (.NET Framework)**. Wprowadź nazwę projektu *helloworld*.

    ![Tworzenie języka Visual C# Aplikacja konsoli (.NET Framework)](media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "tworzenie Visual C# Aplikacja konsoli (.NET Framework)")

1. Zainstalowania i odwołania [pakietu NuGet zestawu SDK mowy](https://aka.ms/csspeech/nuget). W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy i wybierz **Zarządzaj pakietami NuGet dla rozwiązania**.

    ![Kliknij prawym przyciskiem myszy Zarządzaj pakietami NuGet dla rozwiązania](media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Zarządzaj pakietami NuGet dla rozwiązania")

1. W prawym górnym rogu w **źródła pakietu** pól, zaznacz **Nuget.org**. Wyszukaj `Microsoft.CognitiveServices.Speech` pakietu, a następnie zainstalować ją na **helloworld** projektu.

    ![Zainstaluj pakiet NuGet Microsoft.CognitiveServices.Speech](media/sdk/qs-csharp-dotnet-windows-03-nuget-install-0.5.0.png "pakietu Nuget instalacji")

1. Zaakceptuj licencję w oknie dialogowym.

    ![Akceptuje](media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "akceptuje")

1. Następujący wiersz danych wyjściowych zostanie wyświetlony w konsoli Menedżera pakietów.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.5.0' to helloworld
   ```

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>Utwórz konfigurację platformy dopasowywanie architektury komputera

W tej sekcji dodasz nowa platforma do konfiguracji, który pasuje do architektury procesora.

1. Uruchom Menedżera konfiguracji. Wybierz **kompilacji** > **programu Configuration Manager**.

    ![Uruchom program configuration manager](media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Uruchom program configuration manager")

1. W **programu Configuration Manager** okna dialogowego Dodaj nową platformę. Z **aktywną platformą rozwiązania** listy rozwijanej wybierz **New**.

    ![Dodaj nową platformę w oknie Menedżera konfiguracji](media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Dodaj nową platformę w oknie Menedżera konfiguracji")

1. Jeśli korzystasz z 64-bitowych Windows, Utwórz nową konfigurację platformy o nazwie `x64`. Jeśli używasz 32-bitowa Windows, Utwórz nową konfigurację platformy o nazwie `x86`. W tym artykule opisano tworzenie `x64` konfigurację platformy.

    ![Na Windows 64-bitowych, Dodaj nową platformę o nazwie "x64"](media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Dodaj x64 platformy")

## <a name="add-the-sample-code"></a>Dodaj kod przykładowy

1. Otwórz `Program.cs` w programu Visual Studio projektu i Zastąp cały kod w tym pliku następującym kodem.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Zastąp ciąg `YourSubscriptionKey` z kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion` z [region](regions.md) skojarzonych z subskrypcją (na przykład `westus` bezpłatnej subskrypcji wersji próbnej).

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Skompiluj aplikację. Na pasku menu wybierz **kompilacji** > **Kompiluj rozwiązanie**. Kod powinien teraz skompilować bez błędów.

    ![Pomyślnej kompilacji](media/sdk/qs-csharp-dotnet-windows-08-build.png "pomyślnej kompilacji")

1. Uruchom aplikację. Na pasku menu wybierz **debugowania** > **Rozpocznij debugowanie**, lub naciśnij **F5**.

    ![Uruchom aplikację do debugowania](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "uruchomić aplikację do debugowania")

1. Zostanie wyświetlone okno konsoli monitem powiedzieć coś (w języku angielskim). Rozpoznany tekst pojawi się w tym samym oknie.

    ![Dane wyjściowe konsoli po pomyślnym rozpoznawania](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "dane wyjściowe konsoli po pomyślnym rozpoznawania")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj tego przykładu w `quickstart/csharp-dotnet-windows` folderu.

## <a name="next-steps"></a>Kolejne kroki

- [Tłumaczenie mowy](how-to-translate-speech-csharp.md)
- [Dostosowywanie modeli akustycznych](how-to-customize-acoustic-models.md)
- [Dostosowywanie modeli językowych](how-to-customize-language-model.md)
