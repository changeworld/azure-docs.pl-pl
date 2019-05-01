---
title: Przewodnik Szybki Start projektu Akustyka przy użyciu aparatu Unity
titlesuffix: Azure Cognitive Services
description: Za pomocą przykładowej zawartości, eksperymentować Akustyka projektu kontrolki projekt na platformie Unity i wdrażanie pulpitu Windows.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 1c790e0fa726c719d5b888d42b5f59739777566b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917119"
---
# <a name="project-acoustics-unity-quickstart"></a>Szybki Start Unity Akustyka projektu
Użyj projektu Akustyka przykładowe zawartości dla aparatu Unity do eksperymentowania z kontrolkami projektowania opartych na symulacji.

Wymagania dotyczące oprogramowania:
* [Unity 2018.2 +](https://unity3d.com) dla Windows
* [Akustyka projektu przykładowego pakietu zawartości](https://www.microsoft.com/download/details.aspx?id=57346)

Co obejmuje przykładowego pakietu?
* Unity sceny przy użyciu geometrii, źródeł dźwięku i formanty rozgrywkę
* Wtyczka Akustyka projektu 
* Zasoby ramach Akustyka dla sceny

## <a name="import-the-sample-package"></a>Zaimportować przykładowy pakiet
Zaimportować przykładowy pakiet do nowego projektu środowiska Unity. 
* Na platformie Unity, przejdź do **zasoby > Importuj pakiet > Pakiet niestandardowy...**

    ![Opcje zrzut ekranu z importowanie pakietu Unity](media/import-package.png)  

* Wybierz **ProjectAcoustics.unitypackage**

Jeśli importujesz pakiet w istniejącym projekcie, zobacz [integracja aparatu Unity](unity-integration.md) dla dodatkowych kroków i notatki.

## <a name="restart-unity"></a>Ponowne uruchomienie aparatu Unity
Tworzenie części toolkit Akustyka wymaga wersji programu .NET 4.x skryptów środowiska uruchomieniowego. Importowanie pakietu zaktualizuje ustawienia odtwarzacza aparatu Unity. Ponowne uruchomienie aparatu Unity dla tego ustawienia zaczęły obowiązywać.

Możesz sprawdzić to ustawienie weszło w życie, otwierając **ustawienia odtwarzacza**:

![Zrzut ekranu Unity Player ustawienia panelu](media/player-settings.png)

![Zrzut ekranu Unity ustawienia odtwarzacza panel .NET 4.5 wybrane](media/net45.png)

## <a name="experiment-with-design-controls"></a>Eksperymentowanie z kontrolkami projektu
Otwórz przykładowy sceny w **ProjectAcousticsSample** folder i kliknij przycisk play programu Unity editor. Użyj W, A, S, D i myszy, aby poruszać się. Aby porównać, jak brzmi scena z akustyką i bez niej, naciskaj przycisk **R** do momentu, w którym tekst nakładki zmieni się na czerwony i będzie wyświetlany komunikat „Akustyka: wyłączona”. Aby wyświetlić skróty klawiaturowe zapewniające większą liczbę kontrolek, naciśnij klawisz **F1**. Formanty są również użyteczny przez kliknięcie prawym przyciskiem myszy, aby wybrać akcję do wykonania, pozostanie kliknięcie do wykonania akcji.

Skrypt **AcousticsAdjust** jest dołączony do źródeł dźwięku w scenie próbki, co umożliwia parametry projektu-source. 

![Zrzut ekranu AcousticsAdjust Unity skryptu](media/acoustics-adjust.png)

Następujące analizuje niektóre efekty, których można tworzyć za pomocą podanego kontrolek. Aby uzyskać szczegółowe informacje dotyczące każdego formantu, zobacz [samouczek projektu środowiska Unity Akustyka projektu](unreal-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Modyfikowanie tłumienie na podstawie odległości
Audio DSP dostarczone przez **Akustyka projektu** wtyczki spatializer Unity szanuje tłumienie opartego na źródło wbudowana do aparatu Unity Editor. Formanty na podstawie odległości tłumienie znajdują się w **źródła Audio** składnik znaleziony w **Inspektor** panelu dźwięk źródeł, w obszarze **3D ustawienia dźwięku**:

![Zrzut ekranu Unity odległość tłumienie opcji — panel](media/distance-attenuation.png)

Projekt Akustyka wykonuje obliczenia w polu "symulacji region" skupia się wokół lokalizacji odtwarzacza. Ponieważ zasoby Akustyka w pakiecie przykładowych zostały wbudowanymi o rozmiarze region symulacji 45m otaczającego odtwarzacza, powinny być zaprojektowane dźwięku Tłumienie można podzielić na 0 w około 45 m.

### <a name="modify-occlusion-and-transmission"></a>Modyfikowanie zamknięcia i transmisji
* Jeśli **zamknięcia** mnożnik jest większa niż 1 (wartość domyślna to 1), zamknięcia będzie exaggerated. Ustawienie sprawia, że mniej niż 1 zamknięcia efektu bardziej subtelny.

* Aby włączyć przekazywanie za pośrednictwem tablicy, Przenieś **transmisji (baza danych)** suwaka off najniższy poziom. 

### <a name="modify-wetness-for-a-source"></a>Modyfikowanie wetness źródła
* Aby zmienić, jak szybko wetness zmieniają się w odległości, użyj **Percepcyjna Warp odległość**. **Projekt Akustyka** oblicza mokre poziomów w całej przestrzeni z symulacji, które różnią się płynnie z odległości i podaj odległość Percepcyjna podpowiedzi. Zwiększenie warp odległość exaggerates ten efekt, zwiększając powiązane odległość mokrą poziomów. Zniekształcania wartości poniżej 1 należy na podstawie odległości reverberation, zmień bardziej subtelny. Ten efekt można również dostosować szczegółowo bardziej szczegółowej, dostosowując **Wetness (baza danych)**.

* Wydłużenie czasu zanikający w całej przestrzeni przez dostosowanie **Skala czasu zanikania**. Jeśli wynik symulacji dla pary lokalizacji określonego odbiornika źródła jest zanikania ich 1.5s i **Skala czasu zanikania** ma wartość 2, czas zanikania stosowane do źródła jest 3s.

## <a name="next-steps"></a>Kolejne kroki
* Przeczytaj pełne szczegóły na [opartych na platformie Unity Akustyka projektu projektowania formanty](unity-workflow.md)
* Dalsze Eksplorowanie pojęć dotyczących [Projektowanie procesu](design-process.md)
* [Utwórz konto platformy Azure](create-azure-account.md) wstępne tworzenie i tworzenie procesów

