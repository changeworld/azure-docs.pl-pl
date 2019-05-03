---
title: 'Szybki start: Rozpoznawanie mowy, Unity — usługi mowy'
titleSuffix: Azure Cognitive Services
description: Korzystając z tego przewodnika, możesz utworzyć aplikację zamiany mowy na tekst za pomocą środowiska Unity i zestawu Speech SDK for Unity (Beta). Następnie za pomocą mikrofonu komputera będzie możliwa transkrypcja mowy na tekst w czasie rzeczywistym.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: wolfma
ms.openlocfilehash: 3cedfaf1ae16c17026314fc24dbdc7bb11494caf
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020953"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>Szybki start: Rozpoznawanie mowy przy użyciu zestawu Speech SDK for Unity (Beta)

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Korzystając z tego przewodnika, możesz utworzyć aplikację zamiany mowy na tekst za pomocą środowiska [Unity](https://unity3d.com/) i zestawu Speech SDK for Unity (Beta).
Następnie za pomocą mikrofonu komputera będzie możliwa transkrypcja mowy na tekst w czasie rzeczywistym.
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
* Dostęp do mikrofonu komputera.

## <a name="create-a-unity-project"></a>Tworzenie projektu środowiska Unity

* Uruchom środowisko Unity i w obszarze **Projects** (Projekty) wybierz opcję **New** (Nowy).
* Określ w polu **Project Name** (Nazwa projektu) wartość **csharp-unity**, w polu **Template** (Szablon) wartość **3D** i wybierz lokalizację.
  Następnie wybierz pozycję **Create project** (Utwórz projekt).
* Po pewnym czasie powinno zostać wyświetlone okno programu edytora środowiska Unity.

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Zestaw Speech SDK for Unity (Beta) jest pakietem elementów zawartości środowiska Unity (unitypackage).
  Pobierz go [stąd](https://aka.ms/csspeech/unitypackage).
* Zaimportuj zestaw SDK usługi Mowa, wybierając pozycję **Assets (Elementy zawartości)** > **Import Packages (Importuj pakiety)** > **Custom Package (Pakiet niestandardowy)**.
  Aby uzyskać szczegółowe informacje, zapoznaj się z [dokumentacją środowiska Unity](https://docs.unity3d.com/Manual/AssetPackages.html).
* W selektorze plików wybierz plik unitypackage zestawu SDK usługi Mowa pobrany powyżej.
* Upewnij się, że wszystkie pliki są wybrane, a następnie kliknij polecenie **Import** (Importuj).

  ![Zrzut ekranu edytora środowiska Unity podczas importowania pakietu elementów zawartości środowiska Unity dla zestawu SDK usługi Mowa](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>Dodawanie interfejsu użytkownika

Dodamy do sceny minimalny interfejs użytkownika składający się z przycisku wyzwalającego rozpoznawanie mowy i pole tekstowego wyświetlającego wynik.

* W [oknie Hierarchy (Hierarchia)](https://docs.unity3d.com/Manual/Hierarchy.html), domyślnie po lewej stronie, jest wyświetlona przykładowa scena utworzona przez środowisko Unity za pomocą nowego projektu.
* Kliknij przycisk **Utwórz** znajdujący się u góry okna hierarchii, a następnie wybierz pozycję **interfejsu użytkownika** > **przycisk**.
* Spowoduje to utworzenie trzech obiektów gry, które są widoczne w oknie Hierarchy (Hierarchia): obiektu **Button** zagnieżdżonego w obiekcie **Canvas** i obiektu **EventSystem**.
* [Przejdź do widoku sceny](https://docs.unity3d.com/Manual/SceneViewNavigation.html) , dzięki czemu masz dobry widok obszaru roboczego i przycisku w [widoku sceny](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Kliknij obiekt **Button** w oknie Hierarchy (Hierarchia), aby wyświetlić jego ustawienia w [oknie Inspector (Inspektor)](https://docs.unity3d.com/Manual/UsingTheInspector.html), domyślnie po prawej stronie.
* Ustaw właściwości **Pos X** (Pozycja X) i **Pos Y** (Pozycja Y) na wartość **0**, aby wyśrodkować przycisk na kanwie.
* Kliknij przycisk **Utwórz** znajdujący się u góry okna hierarchii ponownie, a następnie wybierz pozycję **interfejsu użytkownika** > **tekstu** utworzyć pole tekstowe.
* Kliknij obiekt **Text** w oknie Hierarchy (Hierarchia), aby wyświetlić jego ustawienia w [oknie Inspector (Inspektor)](https://docs.unity3d.com/Manual/UsingTheInspector.html), domyślnie po prawej stronie.
* Ustaw właściwości **Pos X** (Pozycja X) i **Pos Y** (Pozycja Y) na wartości **0** i **120**, a następnie ustaw właściwości **Width** (Szerokość) i **Height** (Wysokość) na wartości **240** i **120**, aby zapewnić, że pole tekstowe i przycisk nie nakładają się.

Gdy wszystko będzie gotowe, interfejs użytkownika powinien wyglądać podobnie jak na następującym zrzucie ekranu:

[![Zrzut ekranu przedstawiający interfejs użytkownika szybkiego startu w edytorze środowiska Unity](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. W [oknie Project (Projekt)](https://docs.unity3d.com/Manual/ProjectView.html), domyślnie na dole po lewej stronie, kliknij przycisk **Create** (Utwórz), a następnie wybierz pozycję **C# script** (Skrypt C#). Nadaj skryptowi nazwę `HelloWorld`.

1. Otwórz skrypt do edycji, klikając go dwukrotnie.

   > [!NOTE]
   > Uruchamiany edytor kodu możesz skonfigurować za pomocą pozycji **Edit (Edytuj)** > **Preferences (Preferencje)**, zobacz [podręcznik użytkownika środowiska Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Zastąp cały kod następującym:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Znajdź i Zastąp ciąg `YourSubscriptionKey` z kluczem subskrypcji usług przetwarzania mowy.

1. Znajdź i zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z Twoją subskrypcją. Jeśli na przykład używasz bezpłatnej wersji próbnej, region to `westus`.

1. Zapisz zmiany skryptu.

1. W edytorze środowiska Unity należy dodać skrypt jako składnik jednego z obiektów gry.

   * Kliknij pozycję **Canvas** (Kanwa) obiektu w oknie Hierarchy (Hierarchia). Spowoduje to otwarcie ustawienie [oknie Inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej stronie).
   * Kliknij przycisk **Add Component** (Dodaj składnik) w oknie Inspector (Inspektor), a następnie wyszukaj skrypt HelloWorld utworzony powyżej i dodaj go.
   * Zwróć uwagę na to, że składnik Hello World ma dwie niezainicjowane właściwości: **Output Text** (Tekst wyjściowy) i **przycisk Start reco (Rozpocznij nagrywanie)**, które odpowiadają właściwościom publicznym klasy `HelloWorld`.
     Aby powiązać je, kliknij selektor obiektów (małą ikonę okręgu po prawej stronie właściwości) i wybierz obiekty tekstowe i przycisku utworzone wcześniej.

     > [!NOTE]
     > Przycisk ma także zagnieżdżony obiekt tekstu. Upewnij się, że nie został on przypadkowo wybrany dla tekstu wyjściowego, lub zmień nazwę jednego z obiektów tekstowych, używając pola Name (Nazwa) w oknie Inspector (Inspektor), aby uniknąć pomyłki.

## <a name="run-the-application-in-the-unity-editor"></a>Uruchamianie aplikacji w edytorze środowiska Unity

* Naciśnij przycisk **Play** (Odtwórz) na pasku narzędzi edytora środowiska Unity (poniżej paska menu).

* Po uruchomieniu aplikacji kliknij przycisk i powiedz frazę lub zdanie w języku angielskim do mikrofonu komputera. Mowa jest przesyłane do usług przetwarzania mowy i przetłumaczone na tekst, który jest wyświetlany w oknie.

  [![Zrzut ekranu przedstawiający uruchamianie przewodnika szybkiego startu w oknie gry środowiska Unity](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-expanded.png#lightbox)

* Sprawdź komunikaty debugowania w [oknie konsoli](https://docs.unity3d.com/Manual/Console.html).

* Po zakończeniu rozpoznawaniu mowy kliknij przycisk **Play** (Odtwórz) na pasku narzędzi edytora środowiska Unity, aby zatrzymać aplikację.

## <a name="additional-options-to-run-this-application"></a>Dodatkowe opcje uruchamiania aplikacji

Tę aplikację można także wdrożyć w systemie Android, jako autonomiczną aplikację systemu Windows lub jako aplikację platformy uniwersalnej systemu Windows.
Zobacz [repozytorium przykładów](https://aka.ms/csspeech/samples) w folderze quickstart/csharp-unity, które opisuje konfigurację dla dodatkowych elementów docelowych.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
