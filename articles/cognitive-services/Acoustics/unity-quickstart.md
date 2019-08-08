---
title: Dźwięk — Przewodnik Szybki Start z programem Unity
titlesuffix: Azure Cognitive Services
description: Korzystając z przykładowej zawartości, należy eksperymentować z kontrolkami projektu w środowisku Unity i wdrażać je na pulpicie systemu Windows.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a36a16b6cb64c300647d16695edfbb73f4884104
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854858"
---
# <a name="project-acoustics-unity-quickstart"></a>Środowisko projektowe — Przewodnik Szybki Start
Użyj zawartości przykładowej akustycznej projektu dla aparatu Unity, aby eksperymentować z kontrolkami projektu z symulacją.

Wymagania dotyczące oprogramowania:
* [Unity 2018.2 +](https://unity3d.com) dla systemu Windows
* [Przykładowy pakiet zawartości w projekcie](https://www.microsoft.com/download/details.aspx?id=57346)

Co obejmuje pakiet przykładowy?
* Sceny środowiska Unity z geometrią, źródłami dźwięku i kontrolkami rozgrywkę
* Wtyczka do akustycznych projektów 
* Rozszerzania elementy akustyczne dla sceny

## <a name="import-the-sample-package"></a>Importowanie przykładowego pakietu
Zaimportuj przykładowy pakiet do nowego projektu środowiska Unity. 
* W środowisku Unity przejdź do pozycji **zasoby > Importuj pakiet > pakietu niestandardowego...**

    ![Zrzut ekranu opcji pakietu importowania środowiska Unity](media/import-package.png)  

* Wybierz **ProjectAcoustics. UNITYPACKAGE**

W przypadku importowania pakietu do istniejącego projektu zapoznaj się z tematem [integracja aparatu Unity](unity-integration.md) w celu uzyskania dodatkowych kroków i notatek.

## <a name="restart-unity"></a>Uruchom ponownie środowisko Unity
Część Tworzenie zestawu narzędzi akustycznych wymaga wersji środowiska uruchomieniowego obsługi skryptów .NET 4. x. Importowanie pakietu zaktualizuje ustawienia odtwarzacza Unity. Uruchom ponownie środowisko Unity, aby to ustawienie zaczęło obowiązywać.

Aby sprawdzić, czy to ustawienie zostało wykonane, można otworzyć **Ustawienia odtwarzacza**:

![Zrzut ekranu przedstawiający panel ustawień odtwarzacza Unity](media/player-settings.png)

![Zrzut ekranu przedstawiający panel ustawień odtwarzacza Unity z wybranym programem .NET 4,5](media/net45.png)

## <a name="experiment-with-design-controls"></a>Eksperymentowanie z kontrolkami projektu
Otwórz przykładową scenę w folderze **ProjectAcousticsSample** , a następnie kliknij przycisk Odtwórz w edytorze aparatu Unity. Użyj W, a, S, D i myszy, aby poruszać się. Aby porównać, jak brzmi scena z akustyką i bez niej, naciskaj przycisk **R** do momentu, w którym tekst nakładki zmieni się na czerwony i będzie wyświetlany komunikat „Akustyka: wyłączona”. Aby wyświetlić skróty klawiaturowe zapewniające większą liczbę kontrolek, naciśnij klawisz **F1**. Kontrolki są również użyteczne przez kliknięcie prawym przyciskiem myszy w celu wybrania akcji do wykonania, a następnie kliknięcie lewym przyciskiem w celu wykonania akcji.

Skrypt **AcousticsAdjust** jest dołączany do źródeł dźwięku w przykładowej scenie, co umożliwia parametry projektu dla poszczególnych źródeł. 

![Zrzut ekranu przedstawiający skrypt AcousticsAdjust środowiska Unity](media/acoustics-adjust.png)

Poniżej przedstawiono niektóre efekty, które mogą zostać utworzone przy użyciu podanych kontrolek. Aby uzyskać szczegółowe informacje na temat poszczególnych kontrolek, zobacz Samouczek dotyczący środowiska projektowego dla [aparatu Unity](unreal-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Modyfikuj tłumienie na podstawie odległości
Procesor DSP audio, który jest dostarczany przez wtyczkę spatializera w środowisku Unity, uwzględnia tłumienie oparte na odległości dla poszczególnych źródeł wbudowane w Edytor aparatu Unity. Kontrolki przeciwstawnej na podstawie odległości znajdują się w składniku **Źródło Audio** znalezionym w panelu **inspektorów** źródeł dźwięku, w obszarze **Ustawienia dźwięku 3W**:

![Zrzut ekranu przedstawiający panel Opcje tłumienia odległości aparatu Unity](media/distance-attenuation.png)

Akustyczne projektu wykonuje obliczenia w polu "region symulacji" na wyśrodkować wokół lokalizacji odtwarzacza. Ponieważ elementy akustyczne w przykładowym pakiecie były rozszerzania z rozmiarem regionu symulacji o rozmiarze 45m otaczającym gracz, tłumienie dźwięku powinno być zaprojektowane tak, aby mieściły się w około 45 m.

### <a name="modify-occlusion-and-transmission"></a>Modyfikuj zamknięcia i transmisja
* Jeśli mnożnik **zamknięcia** jest większy niż 1 (wartość domyślna to 1), zamknięcia będzie exaggerated. Ustawienie go mniejszej niż 1 sprawia, że efekt zamknięcia jest bardziej subtelny.

* Aby włączyć przekazywanie przez ściany, przesuń suwak **transmisja (DB)** na najniższy poziom. 

### <a name="modify-wetness-for-a-source"></a>Modyfikuj wetness dla źródła
* Aby zmienić, jak szybko wetness zmiany z odległości, użyj **wypaczenia odległości Percepcyjna**. Ruch **akustyczny projektu** oblicza poziomy w całym miejscu od symulacji, które różnią się bezproblemowo i zapewniają podpowiedzi Percepcyjna. Zwiększenie zakrzywienia odległości exaggerates ten efekt przez zwiększenie poziomów mokrych związanych z odległością. Zniekształcanie wartości mniejszej niż 1 sprawia, że reverberation na odległość zmienia się bardziej subtelne. Ten efekt można również dostosować w szczegółowym szczegółach przez dostosowanie **wetness (DB)** .

* Zwiększ czas zaniku w całym miejscu, dostosowując **skalę czasu zanikania**. Jeśli wynik symulacji dla określonej pary lokalizacji odbiornika źródła jest czasem o wartości 1,5 s, a **Skala czasu zaniku** jest ustawiona na 2, czas zaniku zastosowany do źródła to – 3 s.

## <a name="next-steps"></a>Następne kroki
* Przeczytaj wszystkie szczegóły dotyczące kontrolek projektowych opartych na technologii [Unity](unity-workflow.md)
* Więcej szczegółowych koncepcji związanych z [procesem projektowania](design-process.md)
* [Utwórz konto platformy Azure](create-azure-account.md) , aby poznać procesy poprzedzające tworzenie i tworzenie

