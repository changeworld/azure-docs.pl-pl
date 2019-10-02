---
title: 'Szybki Start: Rozpoznawanie mowy, aparatu Unity — usługa rozpoznawania mowy'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika, aby utworzyć aplikację zamiany mowy na tekst za pomocą aparatu Unity i zestawu Speech SDK dla aparatu Unity. Następnie za pomocą mikrofonu komputera będzie możliwa transkrypcja mowy na tekst w czasie rzeczywistym.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: jhakulin
ms.openlocfilehash: a7ac6831fd21fcc0dc425b57f5d73d8c328f9350
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803463"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity"></a>Szybki Start: Rozpoznawanie mowy przy użyciu zestawu Speech SDK dla aparatu Unity

Przewodniki Szybki Start są również dostępne dla [zamiany tekstu na mowę](quickstart-text-to-speech-csharp-unity.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Skorzystaj z tego przewodnika, aby utworzyć aplikację zamiany mowy na tekst przy użyciu [aparatu Unity](https://unity3d.com/) i zestawu Speech SDK dla aparatu Unity.
Po zakończeniu możesz porozmawiać z urządzeniem, aby transkrypcja mowę do tekstu w czasie rzeczywistym.
Jeśli jesteś nowym użytkownikiem aparatu Unity, zalecamy badanie [podręcznika użytkownika środowiska Unity](https://docs.unity3d.com/Manual/UnityManual.html) przed rozpoczęciem opracowywania aplikacji.

> [!NOTE]
> Zestaw Speech SDK for Unity obsługuje Pulpity systemu Windows (x86 i x64) lub platforma uniwersalna systemu Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) i iOS (x64 symulator, ARM32 i ARM64)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten projekt, będą potrzebne następujące elementy:

- Aparat [unity 2018,3 lub nowszy](https://store.unity.com/) z funkcją [Unity 2019,1 Dodawanie obsługi platformy UWP arm64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Wersja 15,9 lub nowsza programu Visual Studio 2017 jest również akceptowalna.
- Aby uzyskać pomoc techniczną dla systemu Windows ARM64, zainstaluj [opcjonalne narzędzia kompilacji dla arm64 i zestaw Windows 10 SDK dla arm64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).
- Klucz subskrypcji dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).
- Dostęp do mikrofonu komputera.

## <a name="create-a-unity-project"></a>Tworzenie projektu środowiska Unity

1. Otwórz aparat Unity. Jeśli używasz aparatu Unity po raz pierwszy, zostanie wyświetlone okno **centrum Unity** *<version number>* . (Aby uzyskać dostęp do tego okna, można także otworzyć Centrum Unity bezpośrednio).

   [@no__t — okno centrum 1Unity](media/sdk/qs-csharp-unity-hub.png)](media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Wybierz pozycję **Nowy**. Zostanie wyświetlone okno **Utwórz nowy projekt z systemem Unity** *<version number>* .

   [@no__t — 1Create nowy projekt w centrum Unity](media/sdk/qs-csharp-unity-create-a-new-project.png)](media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. W polu **Nazwa projektu**wprowadź **CSharp-Unity**.
1. W **szablonach**, jeśli **3W** nie jest jeszcze zaznaczone, zaznacz je.
1. W obszarze **Lokalizacja**wybierz lub Utwórz folder, w którym ma zostać zapisany projekt.
1. Wybierz pozycję **Utwórz**.

Po upływie tego czasu zostanie wyświetlone okno Edytor Unity.

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Aby zainstalować zestaw Speech SDK dla aparatu Unity, wykonaj następujące kroki:

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

1. Pobierz i Otwórz [zestaw Speech SDK dla aparatu Unity](https://aka.ms/csspeech/unitypackage), który jest spakowany jako pakiet zasobów aparatu Unity (. UNITYPACKAGE). Po otwarciu pakietu zasobów zostanie wyświetlone okno dialogowe **Importuj pakiet Unity** .

   [@no__t — okno dialogowe pakiet 1Import Unity w edytorze aparatu Unity](media/sdk/qs-csharp-unity-01-import.png)](media/sdk/qs-csharp-unity-01-import.png#lightbox)
1. Upewnij się, że wszystkie pliki są zaznaczone, a następnie wybierz pozycję **Importuj**. Po kilku chwilach pakiet zasobów aparatu Unity zostanie zaimportowany do projektu.

Więcej informacji o importowaniu pakietów zasobów do aparatu Unity znajduje się w [dokumentacji aparatu Unity](https://docs.unity3d.com/Manual/AssetPackages.html).

## <a name="add-ui"></a>Dodawanie interfejsu użytkownika

Teraz Dodajmy minimalny interfejs użytkownika do naszej sceny. Ten interfejs użytkownika zawiera przycisk służący do wyzwalania rozpoznawania mowy i pola tekstowego, aby wyświetlić wynik. W oknie [ **Hierarchia** ](https://docs.unity3d.com/Manual/Hierarchy.html)Przykładowa scena jest pokazywana przez środowisko Unity utworzone przy użyciu nowego projektu.

1. W górnej części okna **Hierarchia** wybierz pozycję **Utwórz** > **interfejs użytkownika** > **przycisk**.

   Ta akcja powoduje utworzenie trzech obiektów gry, które można wyświetlić w oknie **hierarchii** : obiekt **Button** , obiekt **kanwy** zawierający przycisk i obiekt **EventSystem** .

   [@no__t — środowisko edytora 1Unity](media/sdk/qs-csharp-unity-editor-window.png)](media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Przejdź do widoku **sceny** ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) , aby uzyskać dobry widok kanwy i przycisk w [widoku **sceny** ](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. W oknie [ **inspektora** ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej stronie) ustaw właściwości **pos X** i **pos** na **0**, aby przycisk został wyśrodkowany w środku kanwy.

1. W oknie **Hierarchia** wybierz pozycję **Utwórz** > **interfejs użytkownika** > **tekst** , aby utworzyć obiekt **tekstowy** .

1. W oknie **Inspektor** ustaw właściwości **pos X** i **pos** na **0** i **120**, a następnie ustaw właściwości **Width** i **Height** na **240** i **120**. Te wartości zapewniają, że pole tekstowe i przycisk nie nakładają się.

Gdy skończysz, widok **sceny** powinien wyglądać podobnie do tego zrzutu ekranu:

[@no__t — widok 1Scene w edytorze Unity](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

Aby dodać przykładowy kod skryptu dla projektu Unity, wykonaj następujące kroki:

1. W [oknie projekt](https://docs.unity3d.com/Manual/ProjectView.html)wybierz pozycję **Utwórz** **C# skrypt**  > , aby dodać nowy C# skrypt.

   [![Project okno w edytorze Unity](media/sdk/qs-csharp-unity-project-window.png)](media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Nadaj skryptowi nazwę `HelloWorld`.

1. Kliknij dwukrotnie `HelloWorld`, aby edytować nowo utworzony skrypt.

   > [!NOTE]
   > Aby skonfigurować Edytor kodu, który ma być używany przez aparat Unity do edycji, wybierz opcję edytuj**Preferencje** > , a następnie przejdź do okna Preferencje **narzędzi zewnętrznych** . Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika środowiska Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Zastąp istniejący skrypt następującym kodem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Znajdź i Zamień ciąg `YourSubscriptionKey` na klucz subskrypcji usługi Speech Services.

1. Znajdź i Zamień ciąg `YourServiceRegion` z [regionem](regions.md) skojarzonym z subskrypcją. Jeśli na przykład używasz bezpłatnej wersji próbnej, region to `westus`.

1. Zapisz zmiany skryptu.

Teraz wróć do edytora aparatu Unity i Dodaj skrypt jako składnik do jednego z obiektów gry:

1. W oknie **Hierarchia** wybierz obiekt **kanwy** .

1. W oknie **inspektora** wybierz przycisk **Dodaj składnik** .

   [![Inspector okno w edytorze Unity](media/sdk/qs-csharp-unity-inspector-window.png)](media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. Na liście rozwijanej Wyszukaj utworzony powyżej skrypt `HelloWorld` i dodaj go. W oknie **Inspektor** pojawia się sekcja **Hello World (skrypt)** , która wyświetla dwie niezainicjowane właściwości, **tekst wyjściowy** i **przycisk Rozpocznij odzyski**. Te właściwości składnika Unity są zgodne z właściwościami publicznymi klasy `HelloWorld`.

1. Wybierz selektora obiektów właściwości **Start odzyski Button** (mała ikona okręgu z prawej strony właściwości) i wybierz utworzony wcześniej obiekt **Button** .

1. Wybierz Selektor obiektów **wyjściowej właściwości text** i wybierz utworzony wcześniej obiekt **tekstowy** .

   > [!NOTE]
   > Przycisk ma także zagnieżdżony obiekt tekstu. Upewnij się, że nie wybierasz przypadkowo dla danych wyjściowych tekstu (lub Zmień nazwę jednego z obiektów tekstowych przy użyciu pola **Nazwa** w oknie **inspektora** , aby uniknąć pomyłek).

## <a name="run-the-application-in-the-unity-editor"></a>Uruchamianie aplikacji w edytorze środowiska Unity

Teraz wszystko jest gotowe do uruchomienia aplikacji w edytorze aparatu Unity.

1. Na pasku narzędzi edytora Unity (pod paskiem menu) wybierz przycisk **odtwarzania** (trójkąt skierowany w prawo).

1. Przejdź do [widoku **gry** ](https://docs.unity3d.com/Manual/GameView.html)i poczekaj na wyświetlenie obiektu **tekstowego** w **celu rozpoznania mowy**. (Wyświetla **Nowy tekst** , gdy aplikacja nie została uruchomiona lub nie jest gotowa do odpowiedzi).

1. Wybierz przycisk i zacznij mówić do angielskiej frazy lub zdania na mikrofon komputera. Twoja Zamiana jest przekazywana do usługi mowy i uzyskanego do tekstu, który jest wyświetlany w widoku **gry** .

   [@no__t — widok 1Game w edytorze Unity](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Sprawdź okno [ **konsoli** ](https://docs.unity3d.com/Manual/Console.html) pod kątem komunikatów debugowania. Jeśli okno **konsoli** nie jest wyświetlane, przejdź do paska menu i wybierz polecenie **okno** > **Ogólne** > **konsoli** , aby je wyświetlić.

1. Po zakończeniu rozpoznawania mowy wybierz przycisk **odtwarzania** na pasku narzędzi edytora Unity, aby zatrzymać aplikację.

## <a name="additional-options-to-run-this-application"></a>Dodatkowe opcje uruchamiania aplikacji

Tę aplikację można również wdrożyć jako aplikację systemu Android, autonomiczną aplikację systemu Windows lub aplikację platformy UWP.
Aby uzyskać więcej informacji, zobacz nasze [przykładowe repozytorium](https://aka.ms/csspeech/samples). Folder `quickstart/csharp-unity` opisuje konfigurację dla tych dodatkowych obiektów docelowych.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Uczenie modelu dla Custom Speech](how-to-custom-speech-train-model.md)
