---
title: 'Szybki Start: synteza mowy, usługa Unity — Speech'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika, aby utworzyć aplikację zamiany tekstu na mowę przy użyciu aparatu Unity i zestawu Speech SDK dla aparatu Unity. Po zakończeniu można wypróbować mowę z tekstu w czasie rzeczywistym do głośnika urządzenia.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: b4d329d9d3c2a259fb90b0278c54ba8590590995
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675482"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity"></a>Szybki Start: syntezowanie mowy przy użyciu zestawu Speech SDK dla aparatu Unity

Przewodniki Szybki Start są również dostępne dla [funkcji rozpoznawania mowy](quickstart-csharp-unity.md).

Skorzystaj z tego przewodnika, aby utworzyć aplikację zamiany tekstu na mowę przy użyciu [aparatu Unity](https://unity3d.com/) i zestawu Speech SDK dla platformy Azure Cognitive Services dla aparatu Unity.
Gdy skończysz, możesz wypróbować mowę z tekstu w czasie rzeczywistym do głośnika urządzenia.
Jeśli nie znasz już aparatu Unity, przed rozpoczęciem tworzenia aplikacji Zbadaj [Podręcznik użytkownika środowiska Unity](https://docs.unity3d.com/Manual/UnityManual.html) .

> [!NOTE]
> Środowisko Unity obsługuje Pulpity systemu Windows (x86 i x64) lub platforma uniwersalna systemu Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) i iOS (x64 symulator, ARM32 i ARM64).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten projekt, będą potrzebne następujące elementy:

* Aparat [unity 2018,3 lub nowszy](https://store.unity.com/) z funkcją [Unity 2019,1 Dodawanie obsługi platformy UWP arm64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
* [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Wersja 15,9 lub nowsza programu Visual Studio 2017 jest również akceptowalna.
* Aby uzyskać pomoc techniczną dla systemu Windows ARM64, zainstaluj [opcjonalne narzędzia kompilacji dla arm64 i zestaw Windows 10 SDK dla arm64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).
* Klucz subskrypcji dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).

## <a name="create-a-unity-project"></a>Tworzenie projektu środowiska Unity

* Uruchom środowisko Unity i na karcie **projekty** wybierz pozycję **Nowy**.
* Określ **nazwę projektu** jako **CSharp-Unity** i **szablon** jako **trójwymiarowy**, a następnie wybierz lokalizację.
  Następnie wybierz pozycję **Create project** (Utwórz projekt).
* Po pewnym czasie powinno zostać wyświetlone okno programu edytora środowiska Unity.

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Zestaw Speech SDK for Unity (Beta) jest pakietem elementów zawartości środowiska Unity (unitypackage). Pobierz ją z [tej witryny sieci Web](https://aka.ms/csspeech/unitypackage).
* Zaimportuj zestaw SDK usługi Mowa, wybierając pozycję **Assets (Elementy zawartości)**  > **Import Packages (Importuj pakiety)**  > **Custom Package (Pakiet niestandardowy)** . Aby uzyskać więcej informacji, zobacz [dokumentację aparatu Unity](https://docs.unity3d.com/Manual/AssetPackages.html).
* W selektorze plików wybierz pobrany plik zestawu Speech SDK. UNITYPACKAGE.
* Upewnij się, że wszystkie pliki są zaznaczone, a następnie wybierz pozycję **Importuj**.

  ![Zrzut ekranu edytora środowiska Unity podczas importowania pakietu elementów zawartości środowiska Unity dla zestawu SDK usługi Mowa](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-a-ui"></a>Dodawanie interfejsu użytkownika

Dodamy minimalny interfejs użytkownika do naszej sceny, która składa się z pola wejściowego, aby wprowadzić tekst do syntezy, przycisk służący do wyzwalania syntezy mowy i pola tekstowego, aby wyświetlić wynik.

* W [oknie hierarchia](https://docs.unity3d.com/Manual/Hierarchy.html) (domyślnie po lewej stronie) jest wyświetlana Przykładowa scena utworzona przy użyciu nowego projektu.
* Wybierz przycisk **Utwórz** w górnej części okna **Hierarchia** , a następnie wybierz opcję **interfejs użytkownika**  > **dane wejściowe**.
* Ta opcja tworzy trzy obiekty gry, które można wyświetlić w oknie **hierarchii** : obiekt **pola wejściowego** zagnieżdżony w obrębie obiektu **kanwy** i obiektu **EventSystem** .
* [Przejdź do widoku sceny](https://docs.unity3d.com/Manual/SceneViewNavigation.html) , aby mieć dobry widok kanwy i pole wejściowe w [widoku sceny](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Wybierz obiekt **pola wejściowego** w oknie **Hierarchia** , aby wyświetlić jego ustawienia w [oknie Inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej).
* Ustaw właściwości **pos X** i **pos** na **wartość 0** , aby pole wejściowe było wyśrodkowane w środku kanwy.
* Ponownie wybierz przycisk **Utwórz** w górnej części okna **Hierarchia** . Wybierz**przycisk**  >  **interfejsu użytkownika** , aby utworzyć przycisk.
* Wybierz obiekt **Button** w oknie **Hierarchia** , aby wyświetlić jego ustawienia w [oknie Inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej stronie).
* Ustaw właściwości **pos X** i **pos** na **0** i **-48**. Ustaw właściwości **Width** i **Height** na **160** i **30** , aby upewnić się, że przycisk i pole wejściowe nie nakładają się na siebie.
* Ponownie wybierz przycisk **Utwórz** w górnej części okna **Hierarchia** . Wybierz pozycję **interfejs użytkownika**  > **tekst** , aby utworzyć pole tekstowe.
* Wybierz obiekt **tekstowy** w oknie **Hierarchia** , aby wyświetlić jego ustawienia w [oknie Inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej).
* Ustaw właściwości **pos X** i **pos** na **0** i **80**. Ustaw właściwości **Width** i **Height** na **320** i **80** , aby upewnić się, że pole tekstowe i pole wejściowe nie nakładają się na siebie.
* Ponownie wybierz przycisk **Utwórz** w górnej części okna **Hierarchia** . Wybierz pozycję **audio**  > **Źródło Audio** , aby utworzyć źródło audio.

Po zakończeniu interfejs użytkownika powinien wyglądać podobnie do tego zrzutu ekranu:

[![Zrzut ekranu przedstawiający interfejs użytkownika szybkiego startu w edytorze środowiska Unity](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. W [oknie projektu](https://docs.unity3d.com/Manual/ProjectView.html) (domyślnie w lewym dolnym rogu) wybierz przycisk **Utwórz** , a następnie wybierz pozycję  **C# skrypt**. Nadaj skryptowi nazwę `HelloWorld`.

1. Otwórz skrypt do edycji, klikając go dwukrotnie.

   > [!NOTE]
   > Można skonfigurować Edytor kodu, który jest uruchamiany, wybierając **edytuj**  > **preferencji**. Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika środowiska Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Zastąp cały kod następującym:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Znajdź i Zastąp ciąg `YourSubscriptionKey` za pomocą klucza subskrypcji usługi Speech Services.

1. Znajdź i zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z Twoją subskrypcją. Na przykład region jest `westus` w przypadku korzystania z bezpłatnej wersji próbnej.

1. Zapisz zmiany skryptu.

1. Z powrotem w edytorze aparatu Unity Dodaj skrypt jako składnik do jednego z obiektów gry.

   * Wybierz obiekt **kanwy** w oknie **Hierarchia** , aby otworzyć ustawienie w [oknie Inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej stronie).
   * W oknie **inspektora** wybierz przycisk **Dodaj składnik** . Następnie wyszukaj utworzony wcześniej skrypt `HelloWorld` i dodaj go.
   * Składnik HelloWorld ma cztery niezainicjowane właściwości, **tekst wyjściowy**, **pole wejściowe**, **przycisk mów** i **Źródło Audio**, które pasują do właściwości publicznych klasy `HelloWorld`.
     Aby je połączyć, wybierz Selektor obiektów (ikona małego okręgu z prawej strony właściwości). Wybierz utworzone wcześniej obiekty text i Button.

     > [!NOTE]
     > Pole wejściowe i przycisk mają również zagnieżdżony obiekt tekstowy. Upewnij się, że nie przypadkowo wybierzesz go do wyjścia tekstu. Lub można zmienić nazwy obiektów tekstowych, które używają pola **Nazwa** w oknie **inspektora** , aby uniknąć tego nieporozumień.

## <a name="run-the-application-in-the-unity-editor"></a>Uruchamianie aplikacji w edytorze środowiska Unity

* Wybierz przycisk **odtwarzania** na pasku narzędzi edytora aparatu Unity poniżej paska menu.
* Po uruchomieniu aplikacji wprowadź tekst w polu wejściowym, a następnie wybierz przycisk. Twój tekst jest przesyłany do usługi Speech Service i jest on wydawany na mowę, który jest odtwarzany na Twoim głośnikze.

  [![Screenshot uruchomionego przewodnika Szybki Start w oknie gry Unity](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Sprawdź [okno konsoli](https://docs.unity3d.com/Manual/Console.html) pod kątem komunikatów debugowania.
* Po zakończeniu syntezy mowy wybierz przycisk **odtwarzania** na pasku narzędzi edytora Unity, aby zatrzymać aplikację.

## <a name="additional-options-to-run-this-application"></a>Dodatkowe opcje uruchamiania aplikacji

Tę aplikację można również wdrożyć w systemie Android jako autonomiczną aplikację systemu Windows lub aplikację platformy UWP.
Zapoznaj się z [przykładowym repozytorium](https://aka.ms/csspeech/samples) w folderze szybkiego startu/CSharp-Unity opisującym konfigurację tych dodatkowych obiektów docelowych.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Dostosuj czcionki głosowe](how-to-customize-voice-font.md)
- [Rejestruj przykłady głosu](record-custom-voice-samples.md)
