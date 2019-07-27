---
title: 'Szybki start: Funkcja syntezy mowy i aparatu Unity — usługa rozpoznawania mowy'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika, aby utworzyć aplikację zamiany tekstu na mowę przy użyciu aparatu Unity i zestawu Speech SDK dla aparatu Unity (beta). Po zakończeniu można wypróbować mowę z tekstu w czasie rzeczywistym do głośnika urządzenia.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/26/2019
ms.author: yinhew
ms.openlocfilehash: 507ab9ef9bb3e482e5a33d2406424dfb9116de54
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553610"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity-beta"></a>Szybki start: Wyszukiwać mowę przy użyciu zestawu Speech SDK dla aparatu Unity (beta)

Przewodniki Szybki Start są również dostępne do [rozpoznawania mowy](quickstart-csharp-unity.md).

Skorzystaj z tego przewodnika, aby utworzyć aplikację zamiany tekstu na mowę przy użyciu [aparatu Unity](https://unity3d.com/) i zestawu Speech SDK dla aparatu Unity (beta).
Po zakończeniu można wypróbować mowę z tekstu w czasie rzeczywistym do głośnika urządzenia.
Jeśli nie znasz środowiska Unity, zalecamy zapoznanie się z [podręcznikiem użytkownika środowiska Unity](https://docs.unity3d.com/Manual/UnityManual.html) przed rozpoczęciem tworzenia aplikacji.

> [!NOTE]
> Zestaw Speech SDK for Unity jest obecnie dostępny w wersji beta.
> Obsługuje Pulpity systemu Windows (x86 i x64) lub platforma uniwersalna systemu Windows (x86, x64, ARM/ARM64) i Android (x86, ARM32/64).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten projekt, będą potrzebne następujące elementy:

* [Środowisko unity 2018,3 lub nowsze](https://store.unity.com/) z funkcją [Unity 2019,1 Dodawanie obsługi platformy UWP arm64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
     * Aby uzyskać pomoc techniczną ARM64, zainstaluj [opcjonalne narzędzia kompilacji dla arm64 i zestaw Windows 10 SDK dla arm64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/) 
* Klucz subskrypcji dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).

## <a name="create-a-unity-project"></a>Tworzenie projektu środowiska Unity

* Uruchom środowisko Unity i w obszarze **Projects** (Projekty) wybierz opcję **New** (Nowy).
* Określ w polu **Project Name** (Nazwa projektu) wartość **csharp-unity**, w polu **Template** (Szablon) wartość **3D** i wybierz lokalizację.
  Następnie wybierz pozycję **Create project** (Utwórz projekt).
* Po pewnym czasie powinno zostać wyświetlone okno programu edytora środowiska Unity.

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Zestaw Speech SDK for Unity (Beta) jest pakietem elementów zawartości środowiska Unity (unitypackage).
  Pobierz go [stąd](https://aka.ms/csspeech/unitypackage).
* Zaimportuj zestaw SDK usługi Mowa, wybierając pozycję **Assets (Elementy zawartości)**  > **Import Packages (Importuj pakiety)**  > **Custom Package (Pakiet niestandardowy)** .
  Aby uzyskać szczegółowe informacje, zapoznaj się z [dokumentacją środowiska Unity](https://docs.unity3d.com/Manual/AssetPackages.html).
* W selektorze plików wybierz plik unitypackage zestawu SDK usługi Mowa pobrany powyżej.
* Upewnij się, że wszystkie pliki są wybrane, a następnie kliknij polecenie **Import** (Importuj).

  ![Zrzut ekranu edytora środowiska Unity podczas importowania pakietu elementów zawartości środowiska Unity dla zestawu SDK usługi Mowa](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>Dodawanie interfejsu użytkownika

Dodamy minimalny interfejs użytkownika do naszej sceny, składający się z pola wejściowego, aby wprowadzić tekst do syntezy, przycisk wyzwalający syntezę mowy i pole tekstowe, aby wyświetlić wynik.

* W [oknie Hierarchy (Hierarchia)](https://docs.unity3d.com/Manual/Hierarchy.html), domyślnie po lewej stronie, jest wyświetlona przykładowa scena utworzona przez środowisko Unity za pomocą nowego projektu.
* Kliknij przycisk **Utwórz** w górnej części okna hierarchia, a następnie wybierz**pole wejściowy** **interfejs użytkownika** > .
* Spowoduje to utworzenie trzech obiektów gry, które można wyświetlić w oknie hierarchii: obiekt **pola wejściowego** zagnieżdżony w  obrębie obiektu kanwy i obiektu **EventSystem** .
* [Przejdź do widoku sceny](https://docs.unity3d.com/Manual/SceneViewNavigation.html) , aby uzyskać dobry widok kanwy i pola wejściowego w [widoku sceny](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Kliknij obiekt **pola wejściowego** w oknie hierarchia, aby wyświetlić jego ustawienia w [oknie Inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej).
* Ustaw właściwości **pos X** i **pos** na **0**, aby pole wejściowe było wyśrodkowane w środku kanwy.
* Kliknij przycisk **Utwórz** w górnej części okna hierarchia, a następnie wybierz**przycisk** **interfejsu użytkownika** > , aby utworzyć przycisk.
* Kliknij obiekt **Button** w oknie Hierarchy (Hierarchia), aby wyświetlić jego ustawienia w [oknie Inspector (Inspektor)](https://docs.unity3d.com/Manual/UsingTheInspector.html), domyślnie po prawej stronie.
* Ustaw właściwości **pos X** i **pos** na **0** i **-48**, a następnie ustaw właściwości **Width** i **Height** na **160** i **30** , aby upewnić się, że przycisk i pole wejściowe nie nakładają się na siebie.
* Kliknij przycisk **Utwórz** w górnej części okna hierarchia, a następnie wybierz opcję**tekst** **interfejsu użytkownika** > , aby utworzyć pole tekstowe.
* Kliknij obiekt **Text** w oknie Hierarchy (Hierarchia), aby wyświetlić jego ustawienia w [oknie Inspector (Inspektor)](https://docs.unity3d.com/Manual/UsingTheInspector.html), domyślnie po prawej stronie.
* Ustaw właściwości **pos X** i **pos** na **0** i **80**, a następnie ustaw właściwości **Width** i **Height** na **320** i **80** , aby upewnić się, że pole tekstowe i pole wejściowe nie nakładają się na siebie.
* Kliknij przycisk **Utwórz** w górnej części okna hierarchia, a następnie wybierz pozycję **audio** > audio**Źródło** , aby utworzyć źródło audio.

Gdy wszystko będzie gotowe, interfejs użytkownika powinien wyglądać podobnie jak na następującym zrzucie ekranu:

[![Zrzut ekranu przedstawiający interfejs użytkownika szybkiego startu w edytorze środowiska Unity](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. W [oknie Project (Projekt)](https://docs.unity3d.com/Manual/ProjectView.html), domyślnie na dole po lewej stronie, kliknij przycisk **Create** (Utwórz), a następnie wybierz pozycję **C# script** (Skrypt C#). Nadaj skryptowi nazwę `HelloWorld`.

1. Otwórz skrypt do edycji, klikając go dwukrotnie.

   > [!NOTE]
   > Uruchamiany edytor kodu możesz skonfigurować za pomocą pozycji **Edit (Edytuj)**  > **Preferences (Preferencje)** , zobacz [podręcznik użytkownika środowiska Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Zastąp cały kod następującym:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Znajdź i Zamień ciąg `YourSubscriptionKey` na klucz subskrypcji usługi Speech Services.

1. Znajdź i zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z Twoją subskrypcją. Jeśli na przykład używasz bezpłatnej wersji próbnej, region to `westus`.

1. Zapisz zmiany skryptu.

1. W edytorze środowiska Unity należy dodać skrypt jako składnik jednego z obiektów gry.

   * Kliknij pozycję **Canvas** (Kanwa) obiektu w oknie Hierarchy (Hierarchia). Spowoduje to otwarcie ustawienia w [oknie Inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej stronie).
   * Kliknij przycisk **Add Component** (Dodaj składnik) w oknie Inspector (Inspektor), a następnie wyszukaj skrypt HelloWorld utworzony powyżej i dodaj go.
   * Należy zauważyć, że składnik Hello World ma cztery niezainicjowane właściwości, **tekst danych wyjściowych**, **pole wejściowe**, **przycisk mów** i **Źródło Audio** `HelloWorld` , które pasują do właściwości publicznych klasy.
     Aby powiązać je, kliknij selektor obiektów (małą ikonę okręgu po prawej stronie właściwości) i wybierz obiekty tekstowe i przycisku utworzone wcześniej.

     > [!NOTE]
     > Pole wejściowe i przycisk mają również zagnieżdżony obiekt tekstowy. Upewnij się, że nie wybierasz przypadkowo dla danych wyjściowych tekstu (lub Zmień nazwę obiektów tekstowych przy użyciu pola Nazwa w oknie Inspektora, aby uniknąć tego nieporozumień).

## <a name="run-the-application-in-the-unity-editor"></a>Uruchamianie aplikacji w edytorze środowiska Unity

* Naciśnij przycisk **Play** (Odtwórz) na pasku narzędzi edytora środowiska Unity (poniżej paska menu).

* Po uruchomieniu aplikacji wprowadź tekst w polu wejściowym, a następnie kliknij przycisk. Twój tekst jest przesyłany do usługi Speech Services i jest on wydawany na mowę, który jest odtwarzany na głośniku.

  [![Zrzut ekranu przedstawiający uruchamianie przewodnika szybkiego startu w oknie gry środowiska Unity](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Sprawdź komunikaty debugowania w [oknie konsoli](https://docs.unity3d.com/Manual/Console.html).

* Po zakończeniu syntezy mowy kliknij przycisk **Odtwórz** na pasku narzędzi edytora Unity, aby zatrzymać aplikację.

## <a name="additional-options-to-run-this-application"></a>Dodatkowe opcje uruchamiania aplikacji

Tę aplikację można także wdrożyć w systemie Android, jako autonomiczną aplikację systemu Windows lub jako aplikację platformy uniwersalnej systemu Windows.
Zobacz [repozytorium przykładów](https://aka.ms/csspeech/samples) w folderze quickstart/csharp-unity, które opisuje konfigurację dla dodatkowych elementów docelowych.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Dostosuj czcionki głosowe](how-to-customize-voice-font.md)
- [Rejestruj przykłady głosu](record-custom-voice-samples.md)
