---
title: Samouczek projektu Akustyka Unity Design
titlesuffix: Azure Cognitive Services
description: W tym samouczku opisano przepływ pracy projektu dla akustyki projektu w unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fd00e4105ce4edae9d014df2a83c5ae3aaf778da
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854263"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Samouczek projektu Akustyka Unity Design
W tym samouczku opisano narzędzia do projektowania i przepływ pracy dla akustyki projektu w unity.

Wymagania wstępne:
* Unity 2018.2+ dla systemu Windows
* Scena Unity z upieczonym akustykią

W tym samouczku możesz uzyskać scenę Unity z zasóbem akustyki pieczonej na dwa sposoby:
* [Dodaj akustykę projektu do projektu Unity,](unity-integration.md)a następnie [uzyskaj konto usługi Azure Batch](create-azure-account.md), a następnie [upiecz scenę Unity](unity-baking.md)
* Można też użyć [przykładowej zawartości projektu Akustyka Unity](unity-quickstart.md).

## <a name="review-design-process-concepts"></a>Przeglądanie koncepcji procesu projektowania
Projekt Akustyka wykorzystuje typowe metody przetwarzania sygnału cyfrowego audio (DSP) do przetwarzania źródeł i daje kontrolę nad znanymi właściwościami akustyki, w tym okluzją, mieszaniem mokrym/suchym i długością ogona pogłosu (RT60). Jednak podstawowa [koncepcja procesu projektowania akustyki projektu](design-process.md) polega na tym, że zamiast bezpośrednio ustawiać te właściwości, można kontrolować sposób, w jaki wyniki symulacji są używane do kierowania tymi właściwościami. Domyślne ustawienia dla każdego formantu reprezentują fizycznie dokładną akustykę.

## <a name="design-acoustics-for-each-source"></a>Projektowanie akustyki dla każdego źródła
Projekt Akustyka zapewnia szereg elementów sterujących projektem akustyki specyficzne dla źródła. Dzięki temu można kontrolować mix w scenie, podkreślając niektóre źródła i de-podkreślając inne.

### <a name="adjust-distance-based-attenuation"></a>Dostosowywanie tłumienia na podstawie odległości
Audio DSP dostarczone przez **Projekt Akustyka** Unity spatializer wtyczki respektuje na odległość na źródło tłumienia wbudowane w Edytor Unity. Elementy sterujące tłumienia na odległość znajdują się w składniku **Źródło dźwięku** znajdującym się w panelu **Inspektor** źródeł dźwięku, w obszarze Ustawienia **dźwięku 3D:**

![Zrzut ekranu przedstawiający panel opcji tłumienia odległości unity](media/distance-attenuation.png)

Akustyka wykonuje obliczenia w polu "region symulacji" wyśrodkowanym wokół lokalizacji odtwarzacza. Jeśli źródło dźwięku znajduje się poza tym obszarem symulacji, tylko geometria w polu wpłynie na propagację dźwięku (np. spowodowanie okluzji), która działa dość dobrze, gdy okludery znajdują się w pobliżu odtwarzacza. Jednak w przypadkach, gdy gracz znajduje się na otwartej przestrzeni, ale okludery znajdują się w pobliżu odległego źródła dźwięku, dźwięk może stać się nierealistycznie pozbawiony. Nasze sugerowane obejście polega na zapewnieniu w takich przypadkach, że tłumienie dźwięku spada do 0 na około 45 m, domyślnej poziomej odległości odtwarzacza do krawędzi pudełka.

![Zrzut ekranu przedstawiający panel opcji Unity SpeakerMode](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Regulacja okluzji i transmisji
Dołączanie **acousticsAdjust** skrypt do źródła umożliwia dostrajanie parametrów dla tego źródła. Aby dołączyć skrypt, kliknij pozycję **Dodaj komponent** u dołu panelu **Inspektor** i przejdź do **pozycji Skrypty > Akustyki Dopasowywanie**. Skrypt ma sześć formantów:

![Zrzut ekranu przedstawiający skrypt Unity AcousticsAdjust](media/acoustics-adjust.png)

* **Włącz akustykę** — określa, czy akustyka jest stosowana do tego źródła. Po odznaczeniu źródło będzie przestrzennie z HRTFs lub panoramowania, ale nie będzie akustyki. Oznacza to brak przeszkód, okluzji lub parametrów pogłosu dynamicznego, takich jak poziom i czas zanikania. Pogłos jest nadal stosowany ze stałym poziomem i czasem rozpadu.
* **Okluzja** - Zastosuj mnożnik do poziomu dB okluzji obliczonego przez system akustyki. Jeśli ten mnożnik jest większy niż 1, okluzja będzie przesadzone, podczas gdy wartości mniejsze niż 1 sprawiają, że efekt okluzji bardziej subtelne i wartość 0 wyłącza okluzji.
* **Transmisja (dB)** - Ustawić tłumienie (w dB) spowodowane przez transmisję przez geometrię. Ustaw ten suwak na najniższym poziomie, aby wyłączyć transmisję. Akustyka przestrzennie wykorzystuje początkowy suchy dźwięk jako docierający wokół geometrii sceny (portaling). Transmisja zapewnia dodatkowe suche przybycie, które jest przestrzennie w kierunku linii wzroku. Należy zauważyć, że stosowana jest również krzywa tłumienia odległości dla źródła.

### <a name="adjust-reverberation"></a>Dopasowywanie pogłosu
* **Wilgotność (dB)** - Reguluje moc pogłosu w dB, w zależności od odległości od źródła. Wartości dodatnie sprawiają, że dźwięk jest bardziej pogłosowy, podczas gdy wartości ujemne sprawiają, że dźwięk jest bardziej suchy. Kliknij na sterowanie krzywą (zielona linia), aby przywołać edytor krzywych. Zmodyfikuj krzywą, klikając lewym przyciskiem myszy, aby dodać punkty i przeciągając te punkty, aby utworzyć odpowiednią funkcję. Oś x jest oddalona od źródła, a oś y jest regulacją pogłosu w dB. Aby uzyskać więcej informacji na temat edytowania krzywych, zobacz ten [Podręcznik Unity](https://docs.unity3d.com/Manual/EditingCurves.html). Aby zresetować krzywą z powrotem do wartości domyślnej, kliknij prawym przyciskiem myszy na **Mokrość** i wybierz **reset**.
* **Skala czasu rozpadu** - dostosowuje mnożnik dla czasu rozpadu. Na przykład jeśli wynik wyjmowania określa czas rozpadu 750 milisekund, ale ta wartość jest ustawiona na 1,5, czas rozpadu stosowany do źródła wynosi 1125 milisekund.
* **Outdoorness** - Addytywna regulacja na oszacowanie systemu akustyki, jak "na zewnątrz" pogłos na źródle powinien brzmieć. Ustawienie tej wartości na 1 spowoduje, że źródło będzie zawsze brzmieć całkowicie na zewnątrz, podczas gdy ustawienie go na -1 spowoduje, że dźwięk źródłowy będzie całkowicie w pomieszczeniu.

Dołączanie **acousticsAdjustExperimental** skrypt do źródła umożliwia dodatkowe parametry strojenia eksperymentalnego dla tego źródła. Aby dołączyć skrypt, kliknij przycisk **Dodaj komponent** u dołu panelu **Inspektor** i przejdź do **sekcji Skrypty > Akustyka Dostosuj eksperymentalne**. Obecnie istnieje jedna kontrola eksperymentalna:

![Zrzut ekranu przedstawiający skrypt Unity AcousticsAdjustExperimental](media/acoustics-adjust-experimental.png)

* **Percepna wypaczenie odległości** — zastosuj wykładnicze wypaczenie do odległości używanej do obliczania stosunku sucho-mokrego. System akustyki oblicza mokre poziomy w całej przestrzeni, które różnią się płynnie z odległością i zapewniają percepcyjną odległość. Wartości wypaczające większe niż 1 wyolbrzymiają ten efekt, zwiększając poziomy pogłosu związane z odległością, czyniąc dźwięk "odległym". Wartości wypaczania mniejsze niż 1 sprawiają, że pogłos oparty na odległości zmienia się bardziej subtelnie, dzięki czemu dźwięk jest bardziej "obecny".

## <a name="design-acoustics-for-all-sources"></a>Projektowanie akustyki dla wszystkich źródeł
Aby dostosować parametry dla wszystkich źródeł, kliknij pasek kanału w **mikserze audio**Unity i dostosuj parametry efektu **Mikser akustyki projektu.**

![Zrzut ekranu przedstawiający panel dostosowywania miksera Project Acoustics Unity](media/mixer-parameters.png)

* **Regulacja wilgotności** — umożliwia regulację pogłosu w dB we wszystkich źródłach sceny na podstawie odległości słuchacza źródła. Wartości dodatnie sprawiają, że dźwięk jest bardziej pogłosowy, podczas gdy wartości ujemne sprawiają, że dźwięk jest bardziej suchy.
* **Skala RT60** — multiplikacyjna skalarna dla czasu pogłosu.
* **Użyj przesuwania** — określa, czy dźwięk jest odtwarzany jako obuuszny (0) czy panoramowanie wielokanałowe (1). Każda wartość oprócz 1 wskazuje obuuszne. Wyjście obuuszne jest przestrzenniezowane z HRTF do użytku ze słuchawkami, a wyjście wielokanałowe jest przestrzennie z VBAP do użytku z wielokanałowymi systemami głośników przestrzennych. Jeśli korzystasz z panneru wielokanałowego, należy wybrać tryb głośnika zgodny z ustawieniami urządzenia, który można znaleźć w obszarze **Ustawienia** > projektu**Audio**.

## <a name="check-proper-sound-source-placement"></a>Sprawdzanie właściwego umiejscowienia źródła dźwięku
Źródła dźwięku umieszczone wewnątrz zajmowanych woklenów nie otrzymają leczenia akustycznego. Ponieważ woksele wykraczają poza widoczna geometria sceny, możliwe jest umieszczenie źródła wewnątrz voxel, gdy wydaje się ono niezakupowane geometrią wizualną. Wzespołów Akustyka projektu można wyświetlić, przełączając pole wyboru siatki voxel w menu **Gadżety** w prawym górnym rogu widoku **Scena.**

![Zrzut ekranu przedstawiający menu Unity Gizmos](media/gizmos-menu.png)  

Wyświetlacz voxel może również pomóc w określeniu, czy komponenty wizualne w grze mają zastosowaną transformację. Jeśli tak, zastosuj tę samą transformację do GameObject hosting **akustyka Manager**.

### <a name="bake-time-vs-run-time-voxels"></a>Czas pieczenia vs. czas wykonywania voxels
Można przeglądać voxels w oknie edytora w czasie projektowania gry i w oknie gry w czasie wykonywania. Rozmiar voxels jest inny w tych widokach. Dzieje się tak, ponieważ interpolacja środowiska wykonawczego akustyki używa dokładniejszej siatki voxel, aby uzyskać płynniejsze wyniki interpolacji. Umiejscowienie źródła dźwięku należy zweryfikować przy użyciu voxels środowiska wykonawczego.

Czas projektowania voxels:

![Zrzut ekranu przedstawiający voxels project acoustics w czasie projektowania](media/voxels-design-time.png)

Voxels środowiska uruchomieniowego:

![Zrzut ekranu przedstawiający voxels projektu Akustyka w czasie wykonywania](media/voxels-runtime.png)

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się ze studiami przypadku przedstawiającymi pojęcia stojące za [procesem projektowania](design-process.md)

