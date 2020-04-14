---
title: 'Szybki start: rozpoznawanie mowy z mikrofonu, C# (Unity)- Usługa mowy'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 951ae2c48bcdd92f640a37ddbb6430ca62a3b816
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275348"
---
> [!NOTE]
> Zestaw SDK mowy dla unity obsługuje komputery Windows Desktop (x86 i x64) lub uniwersalną platformę windows (x86, x64, ARM/ARM64), android (x86, ARM32/64) i iOS (symulator x64, ARM32 i ARM64)

## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz:

> [!div class="checklist"]
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania dźwięku

Jeśli już to zrobiłeś, świetnie. Chodźmy dalej.

## <a name="create-a-unity-project"></a>Tworzenie projektu środowiska Unity

1. Otwórz jedność. Jeśli używasz Unity po raz pierwszy, pojawi się okno **Unity Hub.** *<version number>* (Można również otworzyć Unity Hub bezpośrednio, aby przejść do tego okna.)

   [![Okno Centrum unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Wybierz **pozycję Nowy**. Zostanie **wyświetle okno Utwórz nowy projekt z unity.** *<version number>*

   [![Tworzenie nowego projektu w Unity Hub](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. W **nazwie projektu**wprowadź **csharp-unity**.
1. W **obszarze Szablony**, jeśli **3D** nie jest jeszcze zaznaczone, wybierz go.
1. W **obszarze Lokalizacja**wybierz lub utwórz folder, w którym chcesz zapisać projekt.
1. Wybierz **pozycję Utwórz**.

Po upływie trochę czasu zostanie wyświetlene okno Edytor Unity.



## <a name="add-ui"></a>Dodawanie interfejsu użytkownika

Teraz dodajmy minimalny interfejs użytkownika do naszej sceny. Ten interfejs użytkownika składa się z przycisku wyzwalającego rozpoznawanie mowy i pola tekstowego, aby wyświetlić wynik. W [oknie **Hierarchia** ](https://docs.unity3d.com/Manual/Hierarchy.html)pokazano przykładową scenę, która została utworzona przez Unity z nowym projektem.

1. U góry okna **Hierarchia** wybierz pozycję **Utwórz** > przycisk**interfejsu użytkownika** > **Button**.

   Ta akcja tworzy trzy obiekty gry, które można zobaczyć w oknie **hierarchii:** **Button** obiektu, **Canvas** obiektu zawierającego przycisk i **EventSystem** obiektu.

   [![Środowisko Edytora Jedności](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Nawiguj po widoku **Scena,** ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) aby mieć dobry widok obszaru roboczego i przycisk w widoku [ **Scena** ](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. W [oknie **Inspektor** ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej stronie) ustaw właściwości **Pos X** i **Pos Y** na **0**, aby przycisk był wyśrodkowany na środku obszaru roboczego.

1. W oknie **Hierarchia** wybierz pozycję **Utwórz** > tekst**interfejsu użytkownika,** > **Text** aby utworzyć obiekt **Tekst.**

1. W oknie **Inspektor** ustaw właściwości **Pos X** i **Pos Y** na **0** i **120**i ustaw właściwości **Szerokość** i **Wysokość** na **240** i **120**. Te wartości zapewniają, że pole tekstowe i przycisk nie nakładają się na siebie.

Po zakończeniu widok **Sceny** powinien wyglądać podobnie do tego zrzutu ekranu:

[![Widok sceny w Edytorze Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

Aby dodać przykładowy kod skryptu dla projektu Unity, wykonaj następujące kroki:

1. W [oknie Projektu](https://docs.unity3d.com/Manual/ProjectView.html)wybierz pozycję **Utwórz** > **skrypt języka C#,** aby dodać nowy skrypt języka C#.

   [![Okno projektu w Edytorze Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Nadaj skryptowi nazwę `HelloWorld`.

1. Kliknij dwukrotnie, `HelloWorld` aby edytować nowo utworzony skrypt.

   > [!NOTE]
   > Aby skonfigurować edytor kodu używany przez unity do edycji, wybierz pozycję **Edytuj** > **preferencje**, a następnie przejdź do preferencji **Narzędzia zewnętrzne.** Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Zastąp istniejący skrypt następującym kodem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. Znajdź i zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji usługi mowy.

1. Znajdź i zastąp ciąg `YourServiceRegion` **identyfikatorem Region** z [regionu skojarzonego](https://aka.ms/speech/sdkregion) z subskrypcją. Jeśli na przykład używasz bezpłatnej wersji próbnej, region to `westus`.

1. Zapisz zmiany skryptu.

Teraz wróć do Edytora Unity i dodaj skrypt jako składnik do jednego z obiektów gry:

1. W oknie **Hierarchia** zaznacz obiekt **Kanwa.**

1. W oknie **Inspektor** wybierz przycisk **Dodaj komponent.**

   [![Okno Inspektora w Edytorze Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. Na liście rozwijanej wyszukaj `HelloWorld` skrypt, który utworzyliśmy powyżej i dodaj go. W oknie **Inspektora** pojawi się sekcja **Hello World (Script),** w tym dwie niezainicjowane właściwości: **Tekst wyjściowy** i **Przycisk Rozpocznij reco**. Te właściwości składnika Unity odpowiadają `HelloWorld` właściwościom publicznym klasy.

1. Wybierz selektor obiektów właściwości **Start Reco Button** (ikona małego okręgu po prawej stronie właściwości) i wybierz utworzony wcześniej obiekt **Button.**

1. Zaznacz selektor obiektów właściwości **Tekst wyjściowy** i wybierz utworzony wcześniej **obiekt Tekst.**

   > [!NOTE]
   > Przycisk ma także zagnieżdżony obiekt tekstu. Upewnij się, że nie przypadkowo wybrać go dla danych wyjściowych tekstu (lub zmienić nazwę jednego z obiektów tekstowych za pomocą pola **Nazwa** w oknie **Inspektora,** aby uniknąć nieporozumień).

## <a name="run-the-application-in-the-unity-editor"></a>Uruchamianie aplikacji w edytorze środowiska Unity

Teraz możesz przystąpić do uruchamiania aplikacji w Edytorze Unity.

1. Na pasku narzędzi Edytor unity (poniżej paska menu) wybierz przycisk **Odtwórz** (trójkąt skierowany prawym klawiszem).

1. Przejdź do widoku [ **Gry** ](https://docs.unity3d.com/Manual/GameView.html)i poczekaj, aż obiekt **Tekst** wyświetli **przycisk Kliknij, aby rozpoznać mowę**. (Wyświetla **nowy tekst,** gdy aplikacja nie została uruchomiona lub nie jest gotowa do odpowiedzi).

1. Wybierz przycisk i mów w języku angielskim frazę lub zdanie do mikrofonu komputera. Twoja mowa jest przekazywana do usługi Mowy i transkrybowana na tekst, który pojawia się w widoku **Gra.**

   [![Widok gry w Edytorze Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Sprawdź [w oknie **konsoli,** ](https://docs.unity3d.com/Manual/Console.html) czy nie ma komunikatów debugowania. Jeśli okno **Konsoli** nie jest wyświetlane, przejdź do paska menu i wybierz pozycję**Konsola** **generalna** >  **okna,** > aby ją wyświetlić.

1. Po zakończeniu rozpoznawania mowy, wybierz przycisk **Odtwórz** na pasku narzędzi Edytor unity, aby zatrzymać aplikację.

## <a name="additional-options-to-run-this-application"></a>Dodatkowe opcje uruchamiania aplikacji

Tę aplikację można również wdrożyć jako aplikację systemu Android, autonomiczną aplikację systemu Windows lub aplikację platformy uniwersalnej systemu Windows.
Aby uzyskać więcej informacji, zobacz nasze [przykładowe repozytorium](https://aka.ms/csspeech/samples). Folder `quickstart/csharp-unity` opisuje konfigurację dla tych dodatkowych obiektów docelowych.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

