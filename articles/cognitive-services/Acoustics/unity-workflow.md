---
title: Samouczek dotyczący projektowania architektury Unity w projekcie
titlesuffix: Azure Cognitive Services
description: W tym samouczku opisano przepływ pracy projektowania pod kątem hałasu związanych z projektem w środowisku Unity.
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
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854263"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Samouczek dotyczący projektowania architektury Unity w projekcie
W tym samouczku opisano narzędzia projektowania i przepływ pracy dla hałasu projektu w środowisku Unity.

Wymagania wstępne:
* Unity 2018.2 + dla systemu Windows
* Scena środowiska Unity z rozszerzania akustycznymi

Na potrzeby tego samouczka można uzyskać scenę aparatu Unity z rozszerzania akustyczną na dwa sposoby:
* [Dodaj do projektu środowiska Unity akustyczne projektu](unity-integration.md), a następnie [Uzyskaj konto Azure Batch](create-azure-account.md), a następnie [Tworzenie scenę aparatu Unity](unity-baking.md)
* Można też użyć [zawartości przykładowej środowiska Unity](unity-quickstart.md)w środowisku projektowym.

## <a name="review-design-process-concepts"></a>Przegląd pojęć dotyczących procesu projektowania
Funkcja akustyczna projektu korzysta ze wspólnych metod przetwarzania sygnałów audio (DSP), aby przetwarzać źródła, i zapewnia kontrolę nad znanymi właściwościami akustycznymi, w tym zamknięcia, mokre/suche mieszanki i reverberation (RT60). Mimo że podstawowe [koncepcje procesu projektowania](design-process.md) polegają na tym, że zamiast bezpośrednio ustawić te właściwości, można kontrolować sposób, w jaki wyniki symulacji są używane do kierowania tych właściwości. Ustawienia domyślne dla każdej kontrolki reprezentują fizycznie dokładne możliwości akustyczne.

## <a name="design-acoustics-for-each-source"></a>Zaprojektuj akustyczne dla każdego źródła
Akustyczny projektowe zapewnia wiele kontroli charakterystycznych dla konkretnych źródeł. Dzięki temu można kontrolować mieszanie w scenie, podkreślając niektóre źródła i wyróżniać inne.

### <a name="adjust-distance-based-attenuation"></a>Dostosuj tłumienie na podstawie odległości
Procesor DSP audio, który jest dostarczany przez wtyczkę spatializera w środowisku Unity, uwzględnia tłumienie oparte na odległości dla poszczególnych źródeł wbudowane w Edytor aparatu Unity. Kontrolki przeciwstawnej na podstawie odległości znajdują się w składniku **Źródło Audio** znalezionym w panelu **inspektorów** źródeł dźwięku, w obszarze **Ustawienia dźwięku 3W**:

![Zrzut ekranu przedstawiający panel Opcje tłumienia odległości aparatu Unity](media/distance-attenuation.png)

Akustyczne wykonuje obliczenia w polu "region symulacji" na wyśrodkach wokół lokalizacji odtwarzacza. Jeśli źródło dźwięku jest oddalone od odtwarzacza, znajduje się poza tym regionem symulacji, tylko geometria w polu będzie miała wpływ na propagację dźwięku (na przykład powodującą zamknięcia), która działa dobrze, gdy occluders znajdują się w sąsiedztwie odtwarzacza. Jednak w przypadkach, gdy gracz jest w otwartym miejscu, ale occluders jest blisko odległego źródła dźwięku, dźwięk może stać się nierealistycznie disoccluded. Nasze sugerowane obejście polega na tym, że w takich przypadkach tłumienie dźwięku jest wyłączone do 0 o około 45 m, domyślna odległość odtwarzacza do krawędzi pola.

![Zrzut ekranu przedstawiający panel opcji głośnika Unity](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Dostosuj zamknięcia i transmisja
Dołączanie skryptu **AcousticsAdjust** do źródła włącza parametry dostrajania dla tego źródła. Aby dołączyć skrypt, kliknij przycisk **Dodaj składnik** u dołu panelu **Inspektor** i przejdź do **skryptów > Ustawienia akustyczne**. Skrypt ma sześć formantów:

![Zrzut ekranu przedstawiający skrypt AcousticsAdjust środowiska Unity](media/acoustics-adjust.png)

* **Włącz akustyczne** — kontroluje, czy do tego źródła są stosowane akustyczne. Po usunięciu zaznaczenia źródło zostanie rozHRTFse lub panoramowanie, ale nie będzie żadnych akustycznych. Oznacza to, że nie są to przeszkody, zamknięcia lub dynamiczne parametry reverberation, takie jak poziom i czas zanikają. Reverberation nadal są stosowane ze stałym poziomem i nieprzerwanym czasem.
* **Zamknięcia** — Zastosuj mnożnik do poziomu bazy danych zamknięcia obliczanego przez system akustyczny. Jeśli ten mnożnik jest większy niż 1, zamknięcia będzie exaggerated, podczas gdy wartości mniejsze niż 1 sprawiają, że efekt zamknięcia jest bardziej delikatny, a wartość 0 powoduje wyłączenie zamknięcia.
* **Transmisja (DB)** — ustawia tłumienie (w bazie danych) powodowane przez transmisję za pomocą geometrii. Ustaw ten suwak na najniższy poziom, aby wyłączyć transmisję. Akustyczny spatializes początkowe suche audio jako docieranie do geometrii sceny (Portal). Transmisja zapewnia dodatkowe suche, które są przestrzenne w kierunku szczegółowym. Należy zauważyć, że stosowana jest również krzywa tłumienie odległości dla źródła.

### <a name="adjust-reverberation"></a>Dostosuj reverberation
* **Wetness (DB)** — dostosowuje moc Reverb, w bazie danych, zgodnie z odległością od źródła. Wartości dodatnie sprawiają, że dźwięk jest bardziej reverberant, podczas gdy wartości ujemne sprawiają, że dźwięk jest bardziej suchy. Kliknij formant krzywej (zielona linia), aby wyświetlić Edytor krzywej. Zmodyfikuj krzywą przez kliknięcie lewym przyciskiem myszy, aby dodać punkty i przeciągnąć te punkty, aby utworzyć żądaną funkcję. Oś x jest odległości od źródła, a oś y jest korektą Reverb w bazie danych. Aby uzyskać więcej informacji na temat edytowania krzywych, zobacz ten [Podręcznik aparatu Unity](https://docs.unity3d.com/Manual/EditingCurves.html). Aby przywrócić domyślne krzywej, kliknij prawym przyciskiem myszy pozycję **wetness** i wybierz polecenie **Zresetuj**.
* **Skala czasu zaniku** — dostosowuje mnożnik dla czasu zanikania. Na przykład, jeśli wynik tworzenie określa czas zaniku wynoszący 750 milisekund, ale ta wartość jest ustawiona na 1,5, czas zaniku zastosowany do źródła to 1 125 milisekund.
* Niezależność — dostosowanie dodatku dla systemu akustycznego, na którym ma zostać określony dźwięk, reverberation na źródle. Ustawienie tej wartości na 1 spowoduje, że źródło zawsze będzie odtwarzane całkowicie na zewnątrz, podczas gdy ustawienie tego ustawienia na-1 spowoduje, że źródło dźwięku jest całkowicie niedrzwiowe.

Dołączenie skryptu **AcousticsAdjustExperimental** do źródła umożliwia wykonywanie dodatkowych doświadczalnych parametrów dostrajania dla tego źródła. Aby dołączyć skrypt, kliknij przycisk **Dodaj składnik** u dołu panelu **Inspektor** i przejdź do **skryptów, > akustyczne dostosowuje eksperymentalne**. Obecnie istnieje jedna kontrolka eksperymentalna:

![Zrzut ekranu przedstawiający skrypt AcousticsAdjustExperimental środowiska Unity](media/acoustics-adjust-experimental.png)

* **Wypaczanie odległości Percepcyjna** — stosuj wykładnicze Zawijanie do odległości używanej do obliczania współczynnika mokrego. System akustyczny oblicza poziomy w całym miejscu, które różnią się bezproblemowo z odległością i udostępniają wskaźniki odległości Percepcyjna. Zniekształcenie wartości większe niż 1 exaggerate ten efekt przez zwiększenie poziomów reverberation związanych z odległością, co sprawia, że dźwięk "odległe". Wypaczanie wartości mniejszej niż 1 sprawia, że zmiany reverberation na odległość są bardziej subtelne, co sprawia, że dźwięk jest bardziej "obecny".

## <a name="design-acoustics-for-all-sources"></a>Zaprojektuj akustyczne dla wszystkich źródeł
Aby dostosować parametry dla wszystkich źródeł, kliknij pasek kanałów w **mikserze audio**aparatu Unity i Dostosuj parametry efektu miksera dźwięku.

![Zrzut ekranu przedstawiający panel dostosowania miksera aparatu Unity](media/mixer-parameters.png)

* **Wetness Dostosowywanie** — dostosowuje Reverb moc, w bazie danych, we wszystkich źródłach sceny w oparciu o odległość odbiornika źródła. Wartości dodatnie sprawiają, że dźwięk jest bardziej reverberant, podczas gdy wartości ujemne sprawiają, że dźwięk jest bardziej suchy.
* **RT60 Skala** mnożenia wartości skalarnej dla czasu Reverb.
* **Korzystanie** z panoramowania — określa, czy dźwięk jest wyprowadzany jako Binaural (0) czy panoramowanie wielokanałowe (1). Każda wartość oprócz 1 wskazuje Binaural. Dane wyjściowe Binaural są umieszczane w HRTFs do użycia z słuchawki i wielokanałowych danych wyjściowych z VBAP do użycia z wielokanałowymi systemami głośników. W przypadku korzystania z narzędzia panoramowanie wielokanałowe upewnij się, że wybrano tryb głośnika odpowiadający ustawieniom urządzenia, który znajduje się w obszarze **Ustawienia** > projektu**audio**.

## <a name="check-proper-sound-source-placement"></a>Sprawdź poprawność położenia źródła dźwięku
Źródła dźwięku umieszczone wewnątrz zajmowanych voxels nie będą miały zastosowania akustycznego. Ponieważ voxels wykracza poza widoczną geometrię sceny, możliwe jest umieszczenie źródła wewnątrz Voxel, gdy jest ono widoczne unoccluded według geometrii wizualizacji. Można wyświetlić voxels akustyczne projektu, przełączając pole wyboru siatka Voxel w menu **elementy Gizmo** , w prawym górnym rogu widoku **sceny** .

![Zrzut ekranu przedstawiający menu elementy Gizmo środowiska Unity](media/gizmos-menu.png)  

Wyświetlacz Voxel może także pomóc w ustaleniu, czy do nich zastosowano transformację elementów wizualnych w grze. W takim przypadku Zastosuj to samo przekształcenie do gry gameobject, która obsługuje **Menedżera hałasu**.

### <a name="bake-time-vs-run-time-voxels"></a>Czas Tworzenie a czas wykonywania voxels
Można wyświetlić voxels w oknie edytora w czasie projektowania gier i w oknie gry w czasie wykonywania. Rozmiar voxels różni się w tych widokach. Wynika to z faktu, że Interpolacja środowiska uruchomieniowego akustycznego używa dokładniejszej siatki Voxel na potrzeby wygładzania wyników interpolacji. Umieszczanie źródła dźwięku należy zweryfikować przy użyciu voxels środowiska uruchomieniowego.

Voxels czasu projektowania:

![Zrzut ekranu przedstawiający wartości akustyczne projektu voxels w czasie projektowania](media/voxels-design-time.png)

Voxels środowiska uruchomieniowego:

![Zrzut ekranu przedstawiający wartości akustyczne projektu voxels w czasie wykonywania](media/voxels-runtime.png)

## <a name="next-steps"></a>Następne kroki
* Eksploruj analizy przypadków, podkreślając koncepcje związane z [procesem projektowania](design-process.md)

