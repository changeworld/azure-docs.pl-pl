---
title: Szybki start akustyki projektu z unity
titlesuffix: Azure Cognitive Services
description: Przykładowa zawartość służy do eksperymentowania z formantami projektu Project Acoustics w aplikacji Unity i wdrażania na pulpicie systemu Windows.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fabdd221ef99414eae0156babbd76dedb1f0745d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72243020"
---
# <a name="project-acoustics-unity-quickstart"></a>Szybki start w programie Project Acoustics Unity
Użyj przykładowej zawartości projektu Akustyka dla unity eksperymentować z kontrolkami projektu opartymi na symulacji.

Wymagania dotyczące oprogramowania:
* [Unity 2018.2+](https://unity3d.com) dla systemu Windows
* [Przykładowy pakiet zawartości projektu Akustyka](https://www.microsoft.com/download/details.aspx?id=57346)

Co zawiera przykładowy pakiet?
* Scena jedności z geometrią, źródłami dźwięku i elementami sterującymi rozgrywką
* Wtyczka Projekt Akustyka
* Pieczone akustyka aktywów dla sceny

## <a name="import-the-sample-package"></a>Importowanie przykładowego pakietu
Zaimportuj przykładowy pakiet do nowego projektu Unity.
1. W unity przejdź do **pakietu** > **niestandardowego** > **Custom Package**importu zasobów .

    ![Opcje pakietu importu Unity](media/import-package.png)  

1. Wybierz **polecenie ProjectAcoustics.unitypackage**.

1. Wybierz przycisk **Importuj,** aby zintegrować pakiet Unity z projektem.  
  
    ![Okno dialogowe Pakiet importu jedności](media/import-dialog.png)  

Aby zaimportować pakiet do istniejącego projektu, zobacz [integracja unity](unity-integration.md) dla dodatkowych kroków i notatek.

>[!NOTE]
>Po zakończeniu importowania w dzienniku konsoli pojawi się kilka komunikatów o błędach. Przejdź do następnego kroku i uruchom ponownie unity.

## <a name="restart-unity"></a>Uruchom ponownie jedność
Część pieca zestawu narzędzi akustyki wymaga .NET 4. *x* wersja środowiska wykonawczego skryptów. Import pakietu aktualizuje ustawienia odtwarzacza Unity. Uruchom ponownie unity dla tego ustawienia, aby wejść w życie. Aby sprawdzić, czy to ustawienie zostało zastosowane, otwórz ustawienia **odtwarzacza:**

![Menu Ustawienia projektu Unity](media/player-settings.png)  

![Panel Ustawienia odtwarzacza Unity z wybraną pozycją .NET 4.x](media/net45.png)  

>[!NOTE]
>Ten zrzut ekranu został zaczerpnięty z Unity 2018. *x*. Obraz może się różnić w nowszych wersjach Unity.

## <a name="open-the-project-acoustics-bake-window"></a>Otwórz okno pieczenia Akustyka projektu
W obszarze Jedność wybierz polecenie **Akustyka** w menu **Okno.**

![Edytor Unity z wyróżnioną opcją Akustyka w menu Okno](media/window-acoustics.png)

Otworzy się pływające okno **Akustyka.** W tym oknie można ustawić właściwości symulacji akustycznej.

![Otwarty edytor Unity z otwartym oknem Akustyka](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>Eksperymentuj z kontrolkami projektu
Otwórz przykładową scenę w folderze *ProjectAcousticsSample* i wybierz przycisk odtwarzania w edytorze Unity. Użyj klawiszy W, A, S i D oraz myszy, aby się poruszać. Aby porównać, jak scena brzmi z akustyką i bez niej, wybierz klawisz R, aż tekst nakładki zmieni kolor na czerwony i pokaże "Akustyka: Wyłączona". Aby wyświetlić skróty klawiaturowe, aby uzyskać więcej kontrolek, wybierz pozycję F1. Możesz również kliknąć prawym przyciskiem myszy, aby wybrać akcję, a następnie kliknąć lewym przyciskiem myszy, aby wykonać tę akcję.

Skrypt *AcousticsAdjust* jest dołączony do źródeł dźwięku w przykładowej scenie. Umożliwia parametry projektu dla źródła.

![Skrypt Unity AcousticsAdjust](media/acoustics-adjust.png)

W poniższych sekcjach eksploruj niektóre efekty, które można utworzyć przy użyciu dostępnych formantów. Aby uzyskać szczegółowe informacje na temat każdego formantu, zobacz [Project Acoustics Unity Design Tutorial](unity-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Modyfikowanie tłumienia na podstawie odległości
Przetwarzanie sygnału cyfrowego audio w **programie Project Acoustics** Unity spatializer plug-in odnosi się do tłumienia na odległość źródła, które jest wbudowane w edytor Unity. Elementy sterujące tłumienia na odległość znajdują się w komponencie **Źródło dźwięku,** który znajduje się w panelu **Inspektor** źródeł dźwięku w obszarze **Ustawienia dźwięku 3D:**

![Panel opcji tłumienia odległości Jedności](media/distance-attenuation.png)

Projekt Akustyka oblicza w polu "region symulacji", który jest wyśrodkowany wokół lokalizacji odtwarzacza. Zasoby akustyki w opakowaniu próbki zostały upieczone w regionie symulacji o wielkości 45 metrów wokół odtwarzacza. Tak więc tłumienie dźwięku powinno być zaprojektowane tak, aby spadać do 0 na około 45 m.

### <a name="modify-occlusion-and-transmission"></a>Modyfikowanie niedrożności i transmisji
* Jeśli **mnożnik okluzji** jest większy niż 1 (wartość domyślna to 1), niedrożność jest przesadzone. Aby efekt okluzji był bardziej subtelny, ustaw go na mniej niż 1.

* Aby włączyć transmisję przez ścianę, odsuń suwak **Transmisja (dB)** od najniższego ustawienia.

### <a name="modify-wetness-for-a-source"></a>Modyfikowanie wilgotności dla źródła
* Aby zmienić szybkość zmiany wilgotności z odległością, użyj **percepcyjnej wypaczenia odległości**. Dzięki symulacji projekt Akustyka oblicza poziomy mokre, które zapewniają percepcyjne sygnały odległości i zmieniają się płynnie z odległością. Zwiększenie osnowy odległości wyolbrzymia ten efekt poprzez zwiększenie poziomów mokrych związanych z odległością. Wartości wypaczeń mniejsze niż 1 sprawiają, że pogłos oparty na odległości zmienia się bardziej subtelnie.

   Aby dokonać bardziej rygorystycznych regulacji, zmień ustawienie **Wilgotność (dB).**

* Aby wydłużyć czas rozpadu w całej przestrzeni, dostosuj **skalę czasu rozpadu**. Jeśli wynik symulacji dla pary lokalizacji określonego źródła odbiornika jest czas rozpadu 1,5 sekundy i **Skala czasu rozpadu** jest ustawiona na 2, czas rozpadu, który jest stosowany do źródła wynosi 3 sekundy.

## <a name="next-steps"></a>Następne kroki
* Przeczytaj szczegółowe informacje na temat [formantów projektu Akustyka projektu opartego](unity-workflow.md)na jedności .
* Dalsze zapoznaj się z koncepcjami [procesu projektowania.](design-process.md)
* [Utwórz konto platformy Azure,](create-azure-account.md) aby eksplorować procesy pre-bake i bake.
