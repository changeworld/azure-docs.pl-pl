---
title: Dźwięk — Przewodnik Szybki Start z programem Unity
titlesuffix: Azure Cognitive Services
description: Korzystaj z przykładowej zawartości, aby eksperymentować z kontrolkami projektu w środowisku Unity i wdrażać je na pulpicie systemu Windows.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243020"
---
# <a name="project-acoustics-unity-quickstart"></a>Środowisko projektowe — Przewodnik Szybki Start
Użyj zawartości przykładowej akustycznej projektu dla aparatu Unity, aby eksperymentować z kontrolkami projektu z symulacją.

Wymagania dotyczące oprogramowania:
* [Unity 2018.2 +](https://unity3d.com) dla systemu Windows
* [Przykładowy pakiet zawartości w projekcie](https://www.microsoft.com/download/details.aspx?id=57346)

Co obejmuje pakiet przykładowy?
* Sceny środowiska Unity z geometrią, źródłami dźwięku i kontrolkami rozgrywkę
* Wtyczka akustyczna projektu
* Rozszerzania elementy akustyczne dla sceny

## <a name="import-the-sample-package"></a>Importowanie przykładowego pakietu
Zaimportuj przykładowy pakiet do nowego projektu środowiska Unity.
1. W środowisku Unity przejdź do pozycji **zasoby** > **Importuj pakiet** > **niestandardowy pakiet**.

    ![Opcje pakietu importowania środowiska Unity](media/import-package.png)  

1. Wybierz **ProjectAcoustics. UNITYPACKAGE**.

1. Wybierz przycisk **Importuj** , aby zintegrować pakiet Unity z projektem.  
  
    ![Okno dialogowe pakiet importowania aparatu Unity](media/import-dialog.png)  

Aby zaimportować pakiet do istniejącego projektu, zobacz [integracja aparatu Unity](unity-integration.md) w celu uzyskania dodatkowych kroków i notatek.

>[!NOTE]
>Po zakończeniu importowania pojawią się w dzienniku konsoli kilka komunikatów o błędach. Przejdź do następnego kroku i ponownie uruchom środowisko Unity.

## <a name="restart-unity"></a>Uruchom ponownie środowisko Unity
Część Tworzenie zestawu narzędzi akustycznych wymaga programu .NET 4. wersja środowiska uruchomieniowego tworzenia skryptów *x* . Importowanie pakietu aktualizuje ustawienia odtwarzacza Unity. Uruchom ponownie środowisko Unity, aby to ustawienie zaczęło obowiązywać. Aby sprawdzić, czy ustawienie zostało wykonane, Otwórz ustawienia **odtwarzacza** :

![Menu Ustawienia projektu aparatu Unity](media/player-settings.png)  

![Panel ustawień odtwarzacza Unity z wybranym programem .NET 4. x](media/net45.png)  

>[!NOTE]
>Ten zrzut ekranu został pobrany z aparatu Unity 2018. *x*. Obraz może się różnić w nowszych wersjach aparatu Unity.

## <a name="open-the-project-acoustics-bake-window"></a>Otwórz okno Tworzenie akustyczne projektu
W aparacie Unity wybierz pozycję **akustyczne** w menu **okno** .

![Edytor Unity z opcją akustyczne wyróżnioną w menu okno](media/window-acoustics.png)

Zostanie otwarte okno przestawne wartości **akustyczne** . To okno służy do ustawiania właściwości symulacji akustycznej.

![Edytor Unity z otwartym oknem "akustyczne"](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>Eksperymentowanie z kontrolkami projektu
Otwórz przykładową scenę w folderze *ProjectAcousticsSample* , a następnie wybierz przycisk odtwarzania w edytorze aparatu Unity. Użyj klawiszy W, a, S i D i myszy, aby poruszać się wokół siebie. Aby porównać, w jaki sposób dźwięk sceny jest odtwarzany z dźwiękiem i bez niego, wybierz klawisz R do momentu, gdy tekst nakładki zmieni kolor na czerwony i pokaże "akustyczne: wyłączone". Aby wyświetlić skróty klawiaturowe w celu uzyskania większej liczby kontrolek, wybierz F1. Możesz również kliknąć prawym przyciskiem myszy, aby wybrać akcję, a następnie kliknąć ją ponownie, aby wykonać tę akcję.

Skrypt *AcousticsAdjust* jest dołączany do źródeł dźwięku w przykładowej scenie. Umożliwia włączenie parametrów projektu dla poszczególnych źródeł.

![Skrypt AcousticsAdjust środowiska Unity](media/acoustics-adjust.png)

W poniższych sekcjach opisano niektóre efekty, które można utworzyć przy użyciu dostępnych kontrolek. Aby uzyskać szczegółowe informacje na temat poszczególnych kontrolek, zobacz [Samouczek dotyczący](unity-workflow.md)środowiska projektowego dla aparatu Unity.

### <a name="modify-distance-based-attenuation"></a>Modyfikuj tłumienie na podstawie odległości
W przypadku przetwarzania sygnałów cyfrowych audio w **środowisku Unity spatializer** wtyczka jest zgodna z tą, która jest wbudowana w Edytor aparatu Unity. Kontrolki dla przeciwdziałania na odległość znajdują się w składniku **Źródło Audio** , który znajduje się w panelu **inspektora** źródeł dźwięku w obszarze **Ustawienia dźwięku 3W**:

![Panel opcje tłumienia odległości aparatu Unity](media/distance-attenuation.png)

W oknie "region symulacji" są używane obliczenia akustyczne projektu, które są wyśrodkowane wokół lokalizacji odtwarzacza. Zasoby akustyczne w przykładowym pakiecie zostały rozszerzania w regionie symulacji o rozmiarze 45 metrów wokół odtwarzacza. Tak więc tłumienie dźwięku powinno być zaprojektowane tak, aby mieściły się w zakresie od około 45 m.

### <a name="modify-occlusion-and-transmission"></a>Modyfikuj zamknięcia i transmisja
* Jeśli mnożnik **zamknięcia** jest większy niż 1 (wartość domyślna to 1), zamknięcia jest exaggerated. Aby efekt zamknięcia był bardziej delikatny, ustaw go na wartość mniejszą niż 1.

* Aby włączyć transmisję za pośrednictwem ściany, przesuń suwak **transmisja (DB)** z najniższym ustawieniem.

### <a name="modify-wetness-for-a-source"></a>Modyfikuj wetness dla źródła
* Aby zmienić sposób szybkiego wetness zmian z odległością, należy użyć **wypaczenia na odległość Percepcyjna**. Dzięki symulacji ruch w programie projects jest obliczany na podstawie poziomów, które zapewniają wskaźniki odległości Percepcyjna i różnią się bezproblemowo z odległością. Zwiększenie zakrzywienia odległości exaggerates ten efekt przez zwiększenie poziomów mokrych związanych z odległością. Zniekształcanie wartości mniejszej niż 1 sprawia, że reverberation na odległość zmienia się bardziej subtelne.

   Aby wprowadzić bardziej precyzyjne zmiany w tym efekcie, Zmień ustawienie **wetness (DB)** .

* Aby zwiększyć czas zaniku w całym miejscu, Dostosuj **skalę czasu zanikania**. Jeśli wynik symulacji dla określonej pary lokalizacji odbiornika źródła to czas zaniku wynoszący 1,5 sekund, a **Skala czasu zaniku** jest ustawiona na 2, czas zaniku zastosowany do źródła wynosi 3 sekundy.

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się ze szczegółami dotyczącymi [kontrolek projektowych opartych](unity-workflow.md)na technologii Unity.
* Zapoznaj się z pojęciami związanymi z [procesem projektowania](design-process.md).
* [Utwórz konto platformy Azure](create-azure-account.md) , aby poznać procesy poprzedzające tworzenie i tworzenie.
