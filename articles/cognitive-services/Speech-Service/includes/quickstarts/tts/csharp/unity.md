---
title: 'Szybki start: Synteza mowy, C# (Jedność) - Usługa mowy'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik służy do tworzenia aplikacji zamiany tekstu na mowę z Unity i SDK mowy dla unity. Po zakończeniu można syntetyzować mowę z tekstu w czasie rzeczywistym do głośnika urządzenia.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 3a40dd445384ac35749ff2b016c672fb3c40dfb0
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671907"
---
> [!NOTE]
> Unity obsługuje windows desktop (x86 i x64) lub uniwersalną platformę windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) i iOS (symulator x64, ARM32 i ARM64).

## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp)

## <a name="add-a-ui"></a>Dodawanie interfejsu użytkownika

Do naszej sceny dodajemy minimalny interfejs użytkownika, który składa się z pola wejściowego, które wprowadza tekst do syntezy, przycisku wyzwalającego syntezę mowy i pola tekstowego do wyświetlenia wyniku.

* W [oknie Hierarchia](https://docs.unity3d.com/Manual/Hierarchy.html) (domyślnie po lewej stronie) pokazano przykładową scenę, która została utworzona przez Unity z nowym projektem.
* Wybierz przycisk **Utwórz** u góry okna **Hierarchia** i wybierz pozycję**Pole wprowadzania interfejsu** **użytkownika** > .
* Ta opcja tworzy trzy obiekty gry widoczne w oknie **Hierarchia:** obiekt **Pole wejściowe** zagnieżdżone w obiekcie **Canvas** i **eventsystem.**
* [Nawiguj po widoku Scena,](https://docs.unity3d.com/Manual/SceneViewNavigation.html) aby mieć dobry widok obszaru roboczego i pola wejściowego w [widoku Scena](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Wybierz obiekt **Pole wejściowe** w oknie **Hierarchia,** aby wyświetlić jego ustawienia w [oknie Inspektor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej stronie).
* Ustaw właściwości **Pos X** i **Pos Y** na **0,** tak aby pole wejściowe było wyśrodkowane na środku obszaru roboczego.
* Ponownie zaznacz przycisk **Utwórz** u góry okna **Hierarchia.** Wybierz przycisk **interfejsu użytkownika,** > **Button** aby utworzyć przycisk.
* Wybierz **button** obiektu w oknie **Hierarchia,** aby wyświetlić jego ustawienia w [oknie Inspektor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej stronie).
* Ustaw właściwości **Pos X** i **Pos Y** na **0** i **-48**. Ustaw **szerokość** i **wysokość** właściwości **na 160** i **30,** aby upewnić się, że przycisk i pole wejściowe nie nakładają się na siebie.
* Ponownie zaznacz przycisk **Utwórz** u góry okna **Hierarchia.** Wybierz **tekst interfejsu użytkownika,** > **Text** aby utworzyć pole tekstowe.
* Zaznacz obiekt **Tekst** w oknie **Hierarchia,** aby wyświetlić jego ustawienia w [oknie Inspektor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej stronie).
* Ustaw właściwości **Pos X** i **Pos Y** na **0** i **80**. Ustaw właściwości **Szerokość** i **Wysokość** na **320** i **80,** aby upewnić się, że pole tekstowe i pole wejściowe nie nakładają się na siebie.
* Ponownie zaznacz przycisk **Utwórz** u góry okna **Hierarchia.** Wybierz źródło audio **audio,** > **Audio Source** aby utworzyć źródło dźwięku.

Po zakończeniu interfejsu użytkownika powinien wyglądać podobnie do tego zrzutu ekranu:

[![Zrzut ekranu przedstawiający interfejs użytkownika przewodnika Szybki start w Edytorze Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. W [oknie Projekt](https://docs.unity3d.com/Manual/ProjectView.html) (domyślnie na lewym dole) wybierz przycisk **Utwórz,** a następnie wybierz **skrypt C#.** Nadaj skryptowi nazwę `HelloWorld`.

1. Otwórz skrypt do edycji, klikając go dwukrotnie.

   > [!NOTE]
   > Można skonfigurować, który edytor kodu jest uruchamiany, wybierając **pozycję Edytuj** > **preferencje**. Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Zastąp cały kod następującym:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. Zlokalizuj `YourSubscriptionKey` i zastąp ciąg kluczem subskrypcji usługi mowy.

1. Znajdź i zastąp ciąg `YourServiceRegion`[regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z Twoją subskrypcją. Na przykład region `westus` jest, jeśli używasz bezpłatnej wersji próbnej.

1. Zapisz zmiany skryptu.

1. W edytorze Unity Editor dodaj skrypt jako składnik do jednego z obiektów gry.

   * Wybierz obiekt **Kanwa** w oknie **Hierarchia,** aby otworzyć to ustawienie w [oknie Inspektor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej stronie).
   * Wybierz przycisk **Dodaj komponent** w oknie **Inspektor.** Następnie wyszukaj skrypt, `HelloWorld` który wcześniej stworzyliśmy i dodaj go.
   * Składnik HelloWorld ma cztery niezainicjowane właściwości, **Tekst wyjściowy,** **Pole wejściowe,** Przycisk `HelloWorld` **mówienia** i Źródło **dźwięku**, które pasują do właściwości publicznych klasy.
     Aby je podłączyć, wybierz selektor obiektów (małą ikonę okręgu po prawej stronie właściwości). Zaznacz utworzone wcześniej obiekty tekstowe i przyciskowe.

     > [!NOTE]
     > Pole i przycisk wprowadzania mają również zagnieżdżony obiekt tekstowy. Upewnij się, że nie przypadkowo wybrać go do wyjścia tekstowego. Można też zmienić nazwę obiektów tekstowych, które używają pola **Nazwa** w oknie **Inspektor,** aby uniknąć tego zamieszania.

## <a name="run-the-application-in-the-unity-editor"></a>Uruchamianie aplikacji w edytorze środowiska Unity

* Wybierz przycisk **Odtwórz** na pasku narzędzi Edytora Unity znajdujący się pod paskiem menu.
* Po uruchomieniu aplikacji wprowadź tekst w polu wprowadzania i wybierz przycisk. Tekst jest przesyłany do usługi Mowy i syntetyzowany do mowy, która jest odtwarzana na głośniku.

  [![Zrzut ekranu przedstawiający uruchomiony szybki start w oknie Gry Unity](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Sprawdź [w oknie konsoli,](https://docs.unity3d.com/Manual/Console.html) czy nie ma komunikatów debugowania.

## <a name="additional-options-to-run-this-application"></a>Dodatkowe opcje uruchamiania aplikacji

Tę aplikację można również wdrożyć w systemie Android jako autonomiczną aplikację systemu Windows lub aplikację platformy uniwersalnej systemu Windows.
Zobacz [przykładowe repozytorium](https://aka.ms/csspeech/samples) w folderze szybki start/csharp-unity, który opisuje konfigurację dla tych dodatkowych obiektów docelowych.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zobacz też

- [Tworzenie niestandardowego głosu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Nagrywanie niestandardowych próbek głosu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
