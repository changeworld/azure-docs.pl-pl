---
title: 'Szybki start: Syntetyzowania mowy, Unity — usługi mowy'
titleSuffix: Azure Cognitive Services
description: Użyj tego przewodnika, aby utworzyć aplikację zamiany tekstu na mowę przy użyciu aparatu Unity i zestaw SDK rozpoznawania mowy, for Unity (wersja Beta). Po zakończeniu możesz syntetyzowania mowę na podstawie tekstu w czasie rzeczywistym do osoby mówiącej na urządzeniu.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/26/2019
ms.author: yinhew
ms.openlocfilehash: 5240ea45097ce3c0ae7ccbc15a7f99b2f5990832
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467488"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity-beta"></a>Szybki start: Syntetyzowania mowy z zestawem SDK mowy for Unity (wersja Beta)

Przewodniki Szybki Start są również dostępne dla [rozpoznawania mowy](quickstart-csharp-unity.md).

Użyj tego przewodnika do tworzenia aplikacji zamiany tekstu na mowę przy użyciu [Unity](https://unity3d.com/) i zestaw SDK rozpoznawania mowy for Unity (wersja Beta).
Po zakończeniu możesz syntetyzowania mowę na podstawie tekstu w czasie rzeczywistym do osoby mówiącej na urządzeniu.
Jeśli nie znasz środowiska Unity, zalecamy zapoznanie się z [podręcznikiem użytkownika środowiska Unity](https://docs.unity3d.com/Manual/UnityManual.html) przed rozpoczęciem tworzenia aplikacji.

> [!NOTE]
> Zestaw Speech SDK for Unity jest obecnie dostępny w wersji beta.
> Obsługuje on Windows Desktop (x86 i x64) lub platformy Windows uniwersalnej (x86, x64, ARM/ARM64) i Android (x86 ARM32/64).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten projekt, będą potrzebne następujące elementy:

* [Unity 2018.3 lub nowszej](https://store.unity.com/) z [2019.1 Unity, dodanie obsługi architektury ARM64 platformy uniwersalnej systemu Windows](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
     * Do obsługi architektury ARM64 zainstalować [narzędzi kompilacji opcjonalne dla architektury ARM64 i Windows 10 SDK dla architektury ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/) 
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

Dodamy minimalnego interfejsu użytkownika do naszych sceny składające się z pola wejściowego wprowadzania tekstu dla syntezy, przycisk umożliwiający synteza mowy wyzwalacza i pole tekstowe, aby wyświetlić wynik.

* W [oknie Hierarchy (Hierarchia)](https://docs.unity3d.com/Manual/Hierarchy.html), domyślnie po lewej stronie, jest wyświetlona przykładowa scena utworzona przez środowisko Unity za pomocą nowego projektu.
* Kliknij przycisk **Utwórz** znajdujący się u góry okna hierarchii, a następnie wybierz pozycję **interfejsu użytkownika** > **pola danych wejściowych**.
* Spowoduje to utworzenie trzech obiektów gry, które widać w oknie hierarchii: **pola danych wejściowych** obiektu zagnieżdżone w obrębie **kanwy** obiektu i **EventSystem** obiektu.
* [Przejdź do widoku sceny](https://docs.unity3d.com/Manual/SceneViewNavigation.html) , dzięki czemu masz dobry widok obszaru roboczego i pola wejściowego w [widoku sceny](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Kliknij przycisk **pola danych wejściowych** obiektu w oknie hierarchii, aby wyświetlić jej ustawienia przy [oknie Inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej stronie).
* Ustaw **X punktu sprzedaży** i **Y punktu sprzedaży** właściwości **0**, więc pole wejściowe skupia się na środku kanwy.
* Kliknij przycisk **Utwórz** znajdujący się u góry okna hierarchii ponownie, a następnie wybierz pozycję **interfejsu użytkownika** > **przycisk** to utworzenia przycisku.
* Kliknij obiekt **Button** w oknie Hierarchy (Hierarchia), aby wyświetlić jego ustawienia w [oknie Inspector (Inspektor)](https://docs.unity3d.com/Manual/UsingTheInspector.html), domyślnie po prawej stronie.
* Ustaw **X punktu sprzedaży** i **Y punktu sprzedaży** właściwości **0** i **-48**i ustaw **szerokość** i **Wysokość** właściwości **160** i **30** aby upewnić się, że przycisk i pole wejściowe nie pokrywają się.
* Kliknij przycisk **Utwórz** znajdujący się u góry okna hierarchii ponownie, a następnie wybierz pozycję **interfejsu użytkownika** > **tekstu** utworzyć pole tekstowe.
* Kliknij obiekt **Text** w oknie Hierarchy (Hierarchia), aby wyświetlić jego ustawienia w [oknie Inspector (Inspektor)](https://docs.unity3d.com/Manual/UsingTheInspector.html), domyślnie po prawej stronie.
* Ustaw **X punktu sprzedaży** i **Y punktu sprzedaży** właściwości **0** i **80**i ustaw **szerokość** i  **Wysokość** właściwości **320** i **80** aby upewnić się, że pole tekstowe i pole wejściowe nie pokrywają się.
* Kliknij przycisk **Utwórz** znajdujący się u góry okna hierarchii ponownie, a następnie wybierz pozycję **Audio** > **źródła Audio** do utworzenia źródła audio.

Gdy wszystko będzie gotowe, interfejs użytkownika powinien wyglądać podobnie jak na następującym zrzucie ekranu:

[![Zrzut ekranu przedstawiający interfejs użytkownika szybkiego startu w edytorze środowiska Unity](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. W [oknie Project (Projekt)](https://docs.unity3d.com/Manual/ProjectView.html), domyślnie na dole po lewej stronie, kliknij przycisk **Create** (Utwórz), a następnie wybierz pozycję **C# script** (Skrypt C#). Nadaj skryptowi nazwę `HelloWorld`.

1. Otwórz skrypt do edycji, klikając go dwukrotnie.

   > [!NOTE]
   > Uruchamiany edytor kodu możesz skonfigurować za pomocą pozycji **Edit (Edytuj)**  > **Preferences (Preferencje)** , zobacz [podręcznik użytkownika środowiska Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Zastąp cały kod następującym:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Znajdź i Zastąp ciąg `YourSubscriptionKey` z kluczem subskrypcji usług przetwarzania mowy.

1. Znajdź i zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z Twoją subskrypcją. Jeśli na przykład używasz bezpłatnej wersji próbnej, region to `westus`.

1. Zapisz zmiany skryptu.

1. W edytorze środowiska Unity należy dodać skrypt jako składnik jednego z obiektów gry.

   * Kliknij pozycję **Canvas** (Kanwa) obiektu w oknie Hierarchy (Hierarchia). Spowoduje to otwarcie ustawienie [oknie Inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej stronie).
   * Kliknij przycisk **Add Component** (Dodaj składnik) w oknie Inspector (Inspektor), a następnie wyszukaj skrypt HelloWorld utworzony powyżej i dodaj go.
   * Należy zauważyć, że składnik Witaj, świecie ma cztery niezainicjowanych właściwości, **widoczny będzie tekst wyjścia**, **pola danych wejściowych**, **mówić przycisk** i **źródła Audio**, zgodnych publicznymi właściwościami `HelloWorld` klasy.
     Aby powiązać je, kliknij selektor obiektów (małą ikonę okręgu po prawej stronie właściwości) i wybierz obiekty tekstowe i przycisku utworzone wcześniej.

     > [!NOTE]
     > Pole wejściowe i przycisk również ma obiektu zagnieżdżonego tekstu. Upewnij się, że użytkownik nie może przypadkowo wybrać jako tekst wyjściowy (lub zmianę nazw obiektów tekstu, używając pole nazwy w oknie Inspektor, aby uniknąć nieporozumień tego).

## <a name="run-the-application-in-the-unity-editor"></a>Uruchamianie aplikacji w edytorze środowiska Unity

* Naciśnij przycisk **Play** (Odtwórz) na pasku narzędzi edytora środowiska Unity (poniżej paska menu).

* Po uruchomieniu aplikacji, wprowadź jakiś tekst do pola wejściowego, a następnie kliknij przycisk. Tekst jest przesyłane do usług przetwarzania mowy i przekształcony na mowę, na którym odtwarzany na głośników.

  [![Zrzut ekranu przedstawiający uruchamianie przewodnika szybkiego startu w oknie gry środowiska Unity](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Sprawdź komunikaty debugowania w [oknie konsoli](https://docs.unity3d.com/Manual/Console.html).

* Gdy skończysz Syntetyzujące mowy, kliknij przycisk **Odtwórz** przycisk na pasku narzędzi edytora środowiska Unity, aby zatrzymać aplikację.

## <a name="additional-options-to-run-this-application"></a>Dodatkowe opcje uruchamiania aplikacji

Tę aplikację można także wdrożyć w systemie Android, jako autonomiczną aplikację systemu Windows lub jako aplikację platformy uniwersalnej systemu Windows.
Zobacz [repozytorium przykładów](https://aka.ms/csspeech/samples) w folderze quickstart/csharp-unity, które opisuje konfigurację dla dodatkowych elementów docelowych.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Dostosowywanie czcionek głosowych](how-to-customize-voice-font.md)
- [Próbki głosu rekord](record-custom-voice-samples.md)
