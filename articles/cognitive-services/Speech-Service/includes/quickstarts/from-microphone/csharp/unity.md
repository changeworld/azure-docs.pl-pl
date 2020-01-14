---
title: 'Szybki Start: Rozpoznawanie mowy z mikrofonu, C# (Unity) — usługa mowy'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: 149ec84732171cd57b3f8a9590d5340107536eac
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75928153"
---
> [!NOTE]
> Zestaw Speech SDK for Unity obsługuje Pulpity systemu Windows (x86 i x64) lub platforma uniwersalna systemu Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) i iOS (x64 symulator, ARM32 i ARM64)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Konfigurowanie środowiska deweloperskiego](../../../../quickstarts/setup-platform.md?tabs=unity)
> * [Tworzenie pustego przykładowego projektu](../../../../quickstarts/create-project.md?tabs=unity)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania audio

Jeśli to już zrobione, świetnie. Kontynuujmy.

## <a name="create-a-unity-project"></a>Tworzenie projektu środowiska Unity

1. Otwórz aparat Unity. Jeśli używasz aparatu Unity po raz pierwszy, zostanie wyświetlone okno *<version number>* **centrum Unity** . (Aby uzyskać dostęp do tego okna, można także otworzyć Centrum Unity bezpośrednio).

   [okno centrum ![Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Wybierz pozycję **Nowy**. Zostanie wyświetlone okno **Utwórz nowy projekt z** *<version number>* Unity.

   [![utworzyć nowy projekt w centrum Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. W polu **Nazwa projektu**wprowadź **CSharp-Unity**.
1. W **szablonach**, jeśli **3W** nie jest jeszcze zaznaczone, zaznacz je.
1. W obszarze **Lokalizacja**wybierz lub Utwórz folder, w którym ma zostać zapisany projekt.
1. Wybierz pozycję **Utwórz**.

Po upływie tego czasu zostanie wyświetlone okno Edytor Unity.



## <a name="add-ui"></a>Dodawanie interfejsu użytkownika

Teraz Dodajmy minimalny interfejs użytkownika do naszej sceny. Ten interfejs użytkownika zawiera przycisk służący do wyzwalania rozpoznawania mowy i pola tekstowego, aby wyświetlić wynik. W oknie [ **Hierarchia** ](https://docs.unity3d.com/Manual/Hierarchy.html)Przykładowa scena jest pokazywana przez środowisko Unity utworzone przy użyciu nowego projektu.

1. W górnej części okna **Hierarchia** wybierz **przycisk** **Utwórz** > **interfejs użytkownika** > .

   Ta akcja powoduje utworzenie trzech obiektów gry, które można wyświetlić w oknie **hierarchii** : obiekt **Button** , obiekt **kanwy** zawierający przycisk i obiekt **EventSystem** .

   [Środowisko edytora środowiska ![Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Przejdź do widoku **sceny** ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) , aby uzyskać dobry widok kanwy i przycisk w [widoku **sceny** ](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. W oknie [ **inspektora** ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej stronie) ustaw właściwości **pos X** i **pos** na **0**, aby przycisk został wyśrodkowany w środku kanwy.

1. W oknie **Hierarchia** wybierz pozycję **Utwórz** > **interfejs użytkownika** > **tekst** , aby utworzyć obiekt **tekstowy** .

1. W oknie **Inspektor** ustaw właściwości **pos X** i **pos** na **0** i **120**, a następnie ustaw właściwości **Width** i **Height** na **240** i **120**. Te wartości zapewniają, że pole tekstowe i przycisk nie nakładają się.

Gdy skończysz, widok **sceny** powinien wyglądać podobnie do tego zrzutu ekranu:

[Widok sceny ![w edytorze Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

Aby dodać przykładowy kod skryptu dla projektu Unity, wykonaj następujące kroki:

1. W [oknie projekt](https://docs.unity3d.com/Manual/ProjectView.html)wybierz pozycję **Utwórz**  **C# skrypt** > , aby dodać nowy C# skrypt.

   [okno projektu ![w edytorze Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Nadaj skryptowi nazwę `HelloWorld`.

1. Kliknij dwukrotnie `HelloWorld`, aby edytować nowo utworzony skrypt.

   > [!NOTE]
   > Aby skonfigurować Edytor kodu, który ma być używany przez aparat Unity do edycji, wybierz pozycję **edytuj** > **Preferencje**, a następnie przejdź do sekcji Preferencje **narzędzi zewnętrznych** . Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika środowiska Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Zastąp istniejący skrypt następującym kodem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. Znajdź i Zastąp ciąg `YourSubscriptionKey`m kluczem subskrypcji usługi rozpoznawania mowy.

1. Znajdź i Zamień ciąg `YourServiceRegion` z [regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją. Jeśli na przykład używasz bezpłatnej wersji próbnej, region to `westus`.

1. Zapisz zmiany skryptu.

Teraz wróć do edytora aparatu Unity i Dodaj skrypt jako składnik do jednego z obiektów gry:

1. W oknie **Hierarchia** wybierz obiekt **kanwy** .

1. W oknie **inspektora** wybierz przycisk **Dodaj składnik** .

   [okno Inspektora ![w edytorze Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. Na liście rozwijanej Wyszukaj utworzony powyżej skrypt `HelloWorld` i dodaj go. W oknie **Inspektor** pojawia się sekcja **Hello World (skrypt)** , która wyświetla dwie niezainicjowane właściwości, **tekst wyjściowy** i **przycisk Rozpocznij odzyski**. Te właściwości składnika Unity są zgodne z właściwościami publicznymi klasy `HelloWorld`.

1. Wybierz selektora obiektów właściwości **Start odzyski Button** (mała ikona okręgu z prawej strony właściwości) i wybierz utworzony wcześniej obiekt **Button** .

1. Wybierz Selektor obiektów **wyjściowej właściwości text** i wybierz utworzony wcześniej obiekt **tekstowy** .

   > [!NOTE]
   > Przycisk ma także zagnieżdżony obiekt tekstu. Upewnij się, że nie wybierasz przypadkowo dla danych wyjściowych tekstu (lub Zmień nazwę jednego z obiektów tekstowych przy użyciu pola **Nazwa** w oknie **inspektora** , aby uniknąć pomyłek).

## <a name="run-the-application-in-the-unity-editor"></a>Uruchamianie aplikacji w edytorze środowiska Unity

Teraz wszystko jest gotowe do uruchomienia aplikacji w edytorze aparatu Unity.

1. Na pasku narzędzi edytora Unity (pod paskiem menu) wybierz przycisk **odtwarzania** (trójkąt skierowany w prawo).

1. Przejdź do [widoku **gry** ](https://docs.unity3d.com/Manual/GameView.html)i poczekaj na wyświetlenie obiektu **tekstowego** w **celu rozpoznania mowy**. (Wyświetla **Nowy tekst** , gdy aplikacja nie została uruchomiona lub nie jest gotowa do odpowiedzi).

1. Wybierz przycisk i zacznij mówić do angielskiej frazy lub zdania na mikrofon komputera. Twoja Zamiana jest przesyłana do usługi mowy i uzyskanego do tekstu, która jest wyświetlana w widoku **gry** .

   [![widoku gry w edytorze aparatu Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Sprawdź okno [ **konsoli** ](https://docs.unity3d.com/Manual/Console.html) pod kątem komunikatów debugowania. Jeśli okno **konsoli** nie jest wyświetlane, przejdź do paska menu i wybierz polecenie **okno** > **Ogólne** > **konsoli** , aby je wyświetlić.

1. Po zakończeniu rozpoznawania mowy wybierz przycisk **odtwarzania** na pasku narzędzi edytora Unity, aby zatrzymać aplikację.

## <a name="additional-options-to-run-this-application"></a>Dodatkowe opcje uruchamiania aplikacji

Tę aplikację można również wdrożyć jako aplikację systemu Android, autonomiczną aplikację systemu Windows lub aplikację platformy UWP.
Aby uzyskać więcej informacji, zobacz nasze [przykładowe repozytorium](https://aka.ms/csspeech/samples). W folderze `quickstart/csharp-unity` opisano konfigurację dla tych dodatkowych obiektów docelowych.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
