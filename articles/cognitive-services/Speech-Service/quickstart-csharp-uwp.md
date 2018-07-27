---
title: 'Szybki Start: Rozpoznawanie mowy w języku C# w aplikacji platformy uniwersalnej systemu Windows przy użyciu Cognitive Services SDK rozpoznawania mowy'
titleSuffix: Microsoft Cognitive Services
description: Dowiedz się, jak rozpoznawanie mowy w aplikacji platformy uniwersalnej systemu Windows przy użyciu Cognitive Services SDK rozpoznawania mowy
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: be15c8ca37e11f10c31c1fa8738d2b57308a00f2
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283752"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-using-the-speech-sdk"></a>Szybki Start: Rozpoznawanie mowy w aplikacji platformy uniwersalnej systemu Windows przy użyciu zestawu SDK rozpoznawania mowy

W tym artykule dowiesz się, jak utworzyć aplikację platformy uniwersalnej Windows (UWP), transkrypcja mowy na tekst za pomocą Cognitive Services SDK rozpoznawania mowy.
Aplikacja jest skompilowana przy użyciu [Microsoft Cognitive Services mowy zestawu SDK pakietu NuGet](https://aka.ms/csspeech/nuget) i Microsoft Visual Studio 2017.

> [!NOTE]
> Platforma uniwersalna Windows pozwala tworzyć aplikacje, które działają na dowolne urządzenie z systemem Windows 10: PC, konsoli Xbox, Surface Hub i innych urządzeń obsługuje. Aplikacje przy użyciu zestawu SDK rozpoznawania mowy nie przekazuj jeszcze Windows App certyfikacji Kit (WACK). Istnieje możliwość ma zostać załadowana bezpośrednio, których aplikacji, ale może nie obecnie można przesłać do Windows Store.

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji dla usługi mowy. Zobacz [bezpłatnie wypróbować usługę rozpoznawania mowy](get-started.md).
* Komputera z systemem Windows (Windows 10 Fall Creators Update lub nowszej) z mikrofonu pracy.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition lub nowszy.
* **Programowania na platformę uniwersalną Windows** obciążenia w Visual Studio.You ją włączyć w **narzędzia** \> **Pobierz narzędzia i funkcje**.

  ![Włącz programowania na platformę uniwersalną Windows](media/sdk/vs-enable-uwp-workload.png)

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. W programie Visual Studio 2017 Utwórz nowy Visual C# Windows Universal pustą aplikację. W **nowy projekt** okno dialogowe, w lewym okienku rozwiń **zainstalowane** \> **Visual C#** \> **Windows Universal** , a następnie wybierz **pusta aplikacja (Windows Universal)**. Wprowadź nazwę projektu *helloworld*.

    ![](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. W **nowy projekt platformy Windows Universal** POP, wybierz okno **Windows 10 Fall Creators Update (10.0; Kompilacja 16299)** jako **minimalna wersja**, a to lub dowolnym nowszej wersji, co **wersji docelowej**, następnie kliknij przycisk **OK**.

    ![](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Jeśli pracujesz w 64-bitowej instalacji Windows, może przełączyć się platforma kompilacji do `x64`.

   ![Przełącz platformę kompilacji do x64](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > W tej chwili zestaw SDK rozpoznawania mowy obsługuje procesory zgodnego z Intel, ale nie ARM.

1. Zainstalowania i odwołania [pakietu NuGet zestawu SDK mowy](https://aka.ms/csspeech/nuget). W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy i wybierz **Zarządzaj pakietami NuGet dla rozwiązania**.

    ![Kliknij prawym przyciskiem myszy Zarządzaj pakietami NuGet dla rozwiązania](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. W prawym górnym rogu w **źródła pakietu** pól, zaznacz **Nuget.org**. Wyszukiwanie i instalowanie `Microsoft.CognitiveServices.Speech` pakiet i zainstaluj go na **helloworld** projektu.

    ![Zainstaluj pakiet NuGet Microsoft.CognitiveServices.Speech](media/sdk/qs-csharp-uwp-05-nuget-install-0.5.0.png "pakietu Nuget instalacji")

1. Zaakceptuj licencję w oknie dialogowym.

    ![Akceptuje](media/sdk/qs-csharp-uwp-06-nuget-license.png "akceptuje")

1. Następujący wiersz danych wyjściowych zostanie wyświetlony w konsoli Menedżera pakietów.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.5.0' to helloworld
   ```

## <a name="add-the-sample-code"></a>Dodaj kod przykładowy

1. W Eksploratorze rozwiązań kliknij dwukrotnie **Package.appxmanifest** edytować manifest aplikacji.
   Wybierz **możliwości** , a następnie wybierz pole wyboru **mikrofon** możliwości i Zapisz zmiany.

   ![](media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Edytowanie interfejsu użytkownika aplikacji przez dwukrotne kliknięcie `MainPage.xaml` w Eksploratorze rozwiązań. 

    W widoku XAML w projektancie, wstaw poniższy fragment kodu XAML do tagu siatki (między `<Grid>` i `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Edytuj kod związany z XAML, klikając dwukrotnie plik `MainPage.xaml.cs` w Eksploratorze rozwiązań (są grupowane w ramach `MainPage.xaml` elementu).
   Zastąp cały kod w tym pliku następujących czynności.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. W `SpeechRecognitionFromMicrophone_ButtonClicked` obsługi, Zastąp ciąg `YourSubscriptionKey` z kluczem subskrypcji.

1. W `SpeechRecognitionFromMicrophone_ButtonClicked` obsługi, Zastąp ciąg `YourServiceRegion` z [region](regions.md) skojarzonych z subskrypcją (na przykład `westus` bezpłatnej subskrypcji wersji próbnej).

1. Zapisz wszystkie zmiany do projektu.

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Skompiluj aplikację. Na pasku menu wybierz **kompilacji** > **Kompiluj rozwiązanie**. Kod powinien teraz skompilować bez błędów.

    ![Pomyślnej kompilacji](media/sdk/qs-csharp-uwp-08-build.png "pomyślnej kompilacji")

1. Uruchom aplikację. Na pasku menu wybierz **debugowania** > **Rozpocznij debugowanie**, lub naciśnij **F5**.

    ![Uruchom aplikację do debugowania](media/sdk/qs-csharp-uwp-09-start-debugging.png "uruchomić aplikację do debugowania")

1. Pojawi się okno z graficznym interfejsem użytkownika. Najpierw kliknij **Włącz mikrofon** znajdujący się i potwierdza żądanie uprawnień, które się pojawi.

    ![Uruchom aplikację do debugowania](media/sdk/qs-csharp-uwp-10-access-prompt.png "uruchomić aplikację do debugowania")

1. Kliknij przycisk **rozpoznawania mowy z danymi wejściowymi mikrofon** i Mów krótkich fraz do mikrofonu na urządzeniu. Rozpoznany tekst jest wyświetlany w oknie.

    ![](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj tego przykładu w `quickstart/csharp-uwp` folderu.

## <a name="next-steps"></a>Kolejne kroki

- [Tłumaczenie mowy](how-to-translate-speech.md)
- [Dostosowywanie modeli akustycznych](how-to-customize-acoustic-models.md)
- [Dostosowywanie modeli językowych](how-to-customize-language-model.md)
