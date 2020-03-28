---
title: Projekt Akustyka Unity Piec Tutorial
titlesuffix: Azure Cognitive Services
description: W tym samouczku opisano akustykę pieczenia z projektem Akustyka w unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 310decf8053ea16ba46250ba3aabe81c9c254e5e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72243124"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Projekt Akustyka Unity Piec Tutorial
W tym samouczku opisano akustykę pieczenia przy użyciu akustyki projektu w unity.

Wymagania dotyczące oprogramowania:
* [Unity 2018.2+](https://unity3d.com) dla systemu Windows lub MacOS
* [Wtyczka Project Acoustics zintegrowana z projektem Unity](unity-integration.md) lub [przykładową zawartością Projektu Akustyka Unity](unity-quickstart.md)
* *Opcjonalnie:* [Konto usługi Azure Batch przyspiesza](create-azure-account.md) pieczenie przy użyciu przetwarzania w chmurze

## <a name="open-the-project-acoustics-bake-window"></a>Otwórz okno pieczenia Akustyka projektu
W obszarze Jedność wybierz polecenie **Akustyka** z menu **Okno.**

![Edytor Unity z wyróżnioną opcją Akustyka w menu Okno](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Tworzenie siatki nawigacyjnej
Akustyka projektu używa siatki nawigacyjnej do umieszczania punktów sondy odbiornika do symulacji. Można użyć [przepływu pracy siatki nawigacji](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)Unity . Możesz też użyć innego pakietu modelowania 3D do zaprojektowania własnej siatki.

## <a name="mark-acoustic-scene-objects"></a>Oznaczanie obiektów sceny akustycznej
Akustyka projektu opiera się na dwóch typach obiektów sceny do symulacji:
- Obiekty, które odbijają i zadymiają dźwięk w symulacji
- Siatka nawigacyjna gracza, która ogranicza punkty sondy detektora w symulacji

Oba typy obiektów są oznaczane za pomocą karty **Obiekty.**

Ponieważ oznaczanie obiektów po prostu dodaje *AcousticsGeometry* lub *AcousticsNavigation* składników do obiektu, można również użyć [standardowego przepływu pracy składnika Unity](https://docs.unity3d.com/Manual/UsingComponents.html) do oznaczania lub odznaczania obiektów. Renderery siatki i tereny można oznaczyć tylko za siatką. Wszystkie inne typy obiektów zostaną zignorowane. Pola wyboru oznaczają lub odznaczają wszystkie obiekty, których dotyczy problem.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Oznacz akustyczną geometrię okluzji i odbicia
Otwórz kartę **Obiekty** w oknie **Akustyka.** Oznacz wszystkie obiekty jako *Geometria Akustyki,* jeśli powinny one zakryć, odbić lub wchłonąć dźwięk. Geometria akustyki może obejmować takie elementy jak uziemie, ściany, dachy, okna i szyby okienne, dywany i duże meble. Można użyć dowolnego dowolnego poziomu złożoności dla tych obiektów. Ponieważ scena jest voxelized przed symulacją, bardzo szczegółowe siatki, takie jak drzewa, które mają wiele liści, nie są bardziej kosztowne do pieczenia niż uproszczone obiekty.

Nie dołączaj rzeczy, które nie powinny mieć wpływu na akustykę, takich jak niewidoczne siatki kolizji.

Transformacja obiektu podczas obliczania sondy (za pomocą karty **Sondy)** jest ustalana w wynikach pieczenia. Jeśli zaznaczone obiekty na scenie zostaną przeniesione później, obliczenia sondy i pieczenie muszą zostać ponownie przeredagowane.

### <a name="mark-the-navigation-mesh"></a>Oznaczanie siatki nawigacji
Siatki nawigacyjne utworzone za pośrednictwem przepływu pracy Unity zostaną pobrane przez system Akustyka. Aby użyć własnych siatek, zaznacz je na karcie **Obiekty.**

### <a name="for-reference-the-objects-tab-parts"></a>Dla uzyskania informacji: Części karty Obiekty
Części strony karty (na zdjęciu po opisach) to:

1. Przyciski wyboru karty (z zaznaczoną kartą **Obiekty).** Użyj tych przycisków, aby przejść przez różne etapy pieca akustyki, od lewej do prawej.
1. Krótki opis tego, co można zrobić za pomocą tej karty.
1. Dostępne filtry dla okna hierarchii. Te opcje umożliwiają filtrowanie okna hierarchii do obiektów określonego typu, tak aby można je było łatwo oznaczyć. Jeśli nie zaznaczyłeś jeszcze niczego dla akustyki, wybranie dwóch ostatnich opcji nic nie pokaże. Jednak te opcje ułatwiają znajdowanie obiektów po ich oznaczeniu.
1. Jeśli nie zaznaczono żadnych obiektów, w tej sekcji zostanie wyświetlona stan wszystkich obiektów w scenie.
    * Suma: Całkowita liczba aktywnych, nieukrytych obiektów.
    * Ignorowane: liczba obiektów, które nie są rendererami siatki ani terenami.
    * Siatka: liczba obiektów renderowania siatki.
    * Teren: liczba obiektów terenowych.
    * Geometria: Liczba obiektów siatki lub terenu oznaczonych jako **Geometria Akustyki**.
    * Nawigacja: Liczba obiektów siatki lub terenu oznaczonych jako **Akustyka .** Ta liczba nie obejmuje Unity NavMesh.
1. Całkowita liczba "markowalnych" obiektów w scenie, które są tylko rendererami siatki i terenami. Użyj pól wyboru, aby oznaczyć (dodać odpowiedni komponent) te obiekty jako geometrię lub nawigację dla akustyki.
1. Gdy nic nie jest zaznaczone, ta uwaga przypomina o wybraniu obiektów do oznaczenia, jeśli to konieczne. Można również zaznaczyć jedno lub oba pola wyboru, aby oznaczyć wszystkie obiekty w scenie.
1. Po zaznaczeniu obiektów w tej sekcji zostanie wyświetlona stan tylko zaznaczonych obiektów.
1. Całkowita liczba "markowalnych" zaznaczonych obiektów. Zaznaczanie lub wyczyszczenie znaczników pól wyboru lub odznaczenia tylko zaznaczonych obiektów.

Jeśli w scenie nie zaznaczono żadnych opcji, karta **Obiekty** wygląda następująco.

![Karta Obiekty akustyki bez zaznaczonych opcji](media/objects-tab-no-selection-detail.png)

Jeśli w oknie sceny lub hierarchii wybrano coś, karta wygląda jak na poniższej ilustracji.

![Karta Obiekty akustyki z zaznaczeniami](media/objects-tab-selection-detail.png)

Jeśli niektóre obiekty są zaznaczone, a niektóre nie, odpowiednie pola wyboru pokazują wartość "mieszaną", na przykład na poniższej ilustracji.

![Karta Obiekty akustyki z wyróżnionym mieszanym wyborem ikon](media/mixed-object-selection-detail.png)

Zaznacz to pole wyboru, aby oznaczyć wszystkie elementy. Odznacz go, aby usunąć zaznaczenie wszystkich obiektów.

Obiekty mogą być oznaczane zarówno dla geometrii, jak i nawigacji.

## <a name="select-acoustic-materials"></a>Wybierz materiały akustyczne
Po oznaczeniu obiektów wybierz przycisk **Materiały.** Następnie przypisz materiały akustyczne. System materiałów Project Acoustics jest powiązany z systemem materiałów wizualnych Unity. Aby dwa obiekty miały oddzielne materiały akustyczne, muszą mieć oddzielne materiały wizualne.

Wybór materiału akustycznego określa ilość energii dźwiękowej odbijanej z każdej powierzchni. Domyślny materiał akustyczny ma absorpcję podobną do stali. Projekt Akustyka sugeruje materiały na podstawie wizualnej nazwy materiału. Można również przypisać materiał akustyczny **Custom** do materiału, aby aktywować regulowany suwak współczynnika absorpcji.

Czas pogłosu danego materiału w pomieszczeniu jest odwrotnie związany z jego współczynnikiem absorpcji. Większość materiałów ma wartości absorpcyjne w zakresie od 0,01 do 0,20. Materiały, które mają współczynniki absorpcji poza tym zakresem są bardzo chłonne.

![Wykres przedstawia ujemną korelację czasu pogłosu i współczynnika absorpcji.](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Dla odniesienia: Części zakładki Materiały
![Karta Materiały akustyczne w obszarze Jedność](media/materials-tab-detail.png)
1. Przycisk **Materiały** wyświetla tę kartę.
2. Krótki opis tego, co można zrobić za pomocą tej karty.
3. Gdy to pole wyboru jest zaznaczone, wyświetlane są tylko materiały używane przez obiekty oznaczone jako **Geometria akustyki.** W przeciwnym razie zostaną wyświetlone wszystkie materiały używane w scenie.
4. Użyj tych opcji, aby zmienić kolejność opcji w menu w kolumnie **Akustyka** (#6). Sortuj materiały akustyczne według **nazwy** lub **absorpcji**, od niskiej do wysokiej.
5. Posortowana alfabetycznie lista materiałów, które są używane w scenie. Jeśli zaznaczone jest pole wyboru **Pokaż tylko oznaczone** (#3), wyświetlane są tylko materiały używane przez obiekty oznaczone jako **Geometria akustyki.** Wybierz tutaj materiał, aby zaznaczyć wszystkie obiekty w scenie, które używają tego materiału.
6. Materiał akustyczny, do której przypisano materiał sceny. Wybierz dowolny element, aby zmienić materiał akustyczny przypisany do tego materiału sceny. Aby zmienić kolejność sortowania tych menu, użyj opcji **Sortuj akustykę według** (#4).
7. Współczynnik absorpcji akustycznej materiału wybranego w kolumnie po lewej stronie (#6). Wartość 0 oznacza idealnie refleksyjny (bez absorpcji), podczas gdy 1 oznacza idealnie absorpcyjny (bez odbicia). Współczynnika absorpcji nie można zmienić, chyba że wybrany materiał to **Niestandardowy.**
8. W przypadku materiału oznaczonego jako **Niestandardowy**suwak jest aktywowany. Można przesunąć suwak lub wpisać wartość, aby przypisać współczynnik absorpcji.

## <a name="calculate-and-review-listener-probe-locations"></a>Obliczanie i przeglądanie lokalizacji sondy odbiornika
Po przypisaniu materiałów przełącz się na **Calculate** kartę **Sondy.**

### <a name="what-the-calculate-button-does"></a>Co robi przycisk "Oblicz"
Przycisk **Oblicz** używa wybranej geometrii sceny akustycznej do przygotowania sceny do symulacji:

- Pobiera geometrię z siatki sceny i oblicza *objętość voxel*. Objętość voxel jest objętość całej sceny składa się z małych sześciennych "voxels". Rozmiar woxelu jest określany przez częstotliwość symulacji, która jest kontrolowana przez ustawienie **Rozdzielczość symulacji.** Każdy voxel jest oznaczony jako "otwarte powietrze" lub zawierający geometrię sceny. Jeśli voxel zawiera geometrię, voxel jest oznaczony współczynnikiem absorpcji materiału przypisanego do tej geometrii.
- Używa siatki nawigacji do umieszczenia punktów sondy odbiornika. Algorytm równoważy konkurujące ze sobą problemy związane z zasięgiem przestrzennym i czasem symulacji oraz rozmiarem pliku. Jego celem jest zapewnienie, aby wąskie korytarze i małe przestrzenie zawsze miały pewne pokrycie. Typowa liczba punktów sondy waha się od 100 dla małych scen do kilku tysięcy dla dużych scen.

W zależności od rozmiaru sceny i prędkości maszyny obliczenia te mogą potrwać kilka minut.

### <a name="review-voxel-and-probe-placement"></a>Przejrzyj rozmieszczenie voxel i sondy
Podgląd zarówno danych voxel, jak i lokalizacji punktów sondy, aby upewnić się, że jesteś gotowy do pieczenia sceny. Niekompletna siatka nawigacyjna lub brak lub dodatkowa geometria akustyczna jest zwykle łatwo widoczna w podglądzie. Włącz lub wyłącz voxel i sondy umiejscowienia za pomocą menu **Gizmos.**

![Menu Gizmos w Unity](media/gizmos-menu.png)

Wokarze zawierające geometrię akustyczną są wyświetlane jako zielone kostki. Eksploruj swoją scenę i sprawdź, czy wszystko, co powinno być geometrią, ma voxels. Kamera sceny musi znajdować się w odległości około 5 metrów od obiektu, aby woczniki się pokazać.

Jeśli porównasz woki utworzone z grubą rozdzielczością w porównaniu z doskonałą rozdzielczością, zobaczysz, że grube woki są dwa razy większe.

![Gruboiste podgląd voxels w edytorze Unity](media/voxel-cubes-preview.png)

Wyniki symulacji są interpolowane między lokalizacjami punktów sondy odbiornika w czasie wykonywania. Sprawdź, czy w pobliżu miejsc, w których gracz ma wejść na scenę, znajdują się punkty sondy.

![Podgląd sond w edytorze Unity](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Zadbaj o zmianę nazwy sceny
Nazwa sceny służy do łączenia sceny z plikami, które przechowują położenie punktu sondy i voxelization. W przypadku zmiany nazwy sceny po obliczeniu punktów sondy dane przypisania i rozmieszczenia materiału zostaną utracone i powinny zostać ponownie użyte do ponownego uruchomienia.

### <a name="for-reference-parts-of-the-probes-tab"></a>Dla odniesienia: Części karty Sondy
![Karta Sondy akustyki w unity](media/probes-tab-detail.png)

1. Przycisk **Sondy** spowoduje wyświetlenia tej zakładki.
2. Krótki opis tego, co można zrobić na tej karcie.
3. Użyj tych opcji, aby ustawić rozdzielczość symulacyjnej. Gruba jest szybsza, ale są kompromisy. Aby uzyskać szczegółowe informacje, zobacz [Rozdzielczość pieczenia](bake-resolution.md).
4. Określa, gdzie umieścić pliki danych akustyki. Wybierz przycisk "**...**", aby uzyskać dostęp do selektora folderów. Domyślną lokalizacją jest *Assets/AcousticsData*. W tej lokalizacji jest również tworzony podfolder *edytora.* Aby uzyskać więcej informacji, zobacz [Pliki danych dodane przez proces pieczenia](#Data-Files), w dalszej części tego artykułu.
5. Prefiks, który jest określony w tym miejscu jest używany do nazwy plików danych dla tej sceny. Wartość domyślna to "Acoustics_*[Nazwa sceny]*".
6. Po obliczyniu sondy formanty, które właśnie opisaliśmy, są wyłączone. Wybierz przycisk **Wyczyść,** aby wymazać obliczenia i włączyć formanty, aby można było ponownie obliczyć z nowymi ustawieniami.
7. Wybierz **opcję Oblicz,** aby voxelize sceny i obliczyć lokalizacje punktów sondy. Obliczenia są wykonywane lokalnie na komputerze. Należy to zrobić przed zrobieniem pieca.

W tej wersji programu Project Acoustics sondy nie mogą być umieszczane ręcznie. Użyj zautomatyzowanego procesu na karcie **Sondy.**

Aby uzyskać więcej informacji na temat grubej i precyzyjnej rozdzielczości, zobacz [Rozdzielczość pieczenia](bake-resolution.md).

## <a name="bake-your-scene-by-using-azure-batch"></a>Upiec swoją scenę przy użyciu usługi Azure Batch
Możesz upiec scenę w klastrze obliczeniowym w chmurze przy użyciu usługi Azure Batch. Dodatek Project Acoustics Unity łączy się bezpośrednio z usługą Azure Batch w celu tworzenia wystąpienia, zarządzania i niszczenia klastra usługi Azure Batch dla każdego wypieku. Na karcie **Piec** wprowadź poświadczenia platformy Azure, wybierz typ i rozmiar maszyny klastra, a następnie wybierz pozycję **Piec**.

### <a name="for-reference-parts-of-the-bake-tab"></a>W celach informacyjnych: Części zakładki Piec
![Zakładka Piec akustyki w unity](media/bake-tab-details.png)

1. Przycisk **Piec** wyświetla tę kartę.
2. Krótki opis tego, co możesz zrobić na tej stronie.
3. Wprowadź poświadczenia platformy Azure w tych polach po utworzeniu konta platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Azure Batch](create-azure-account.md).
4. Pole znacznika obrazu platformy Docker dla zestawu narzędzi Akustyka.
5. Otwiera witrynę Azure portal, aby zarządzać subskrypcjami, monitorować użycie i wyświetlać informacje rozliczeniowe.
6. Określa typ węzła obliczeniowego usługi Azure Batch, który ma być używany do obliczeń. Typ węzła musi być obsługiwany przez lokalizację centrum danych platformy Azure. Jeśli nie masz pewności, zostaw **jako Standard_F8s_v2**.
7. Liczba węzłów do użycia do obliczeń. Liczba ta wpływa na czas pieczenia. Jest ograniczona przez alokację rdzenia usługi Azure Batch. Alokacja domyślna zezwala tylko na dwa węzły 8-rdzeniowe lub jeden węzeł 16-rdzeniowy, ale można ją rozwinąć. Aby uzyskać więcej informacji na temat podstawowych ograniczeń alokacji, zobacz [Tworzenie konta usługi Azure Batch](create-azure-account.md).
8. To pole wyboru należy zaznaczyć, aby skonfigurować pulę obliczeń do [używania węzłów o niskim priorytecie](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Węzły obliczeniowe o niskim priorytecie mają znacznie niższy koszt. Ale nie zawsze mogą być dostępne lub mogą być wywłaszczone w dowolnym momencie.
9. Liczba sondy dla sceny obliczona na karcie **Sondy.** Liczba sond określa liczbę symulacji, które muszą być uruchamiane w chmurze. Nie można określić więcej węzłów niż istnieją sondy.
10. Oszacowanie czasu, który zajmie zadanie do uruchomienia w chmurze, z wyłączeniem czasu uruchamiania węzła. Po uruchomieniu zadania to pole zawiera oszacowanie, jak długo do uzyskania wyników.
11. Całkowity czas przetwarzania potrzebny do uruchomienia symulacji. Ta wartość jest całkowita ilość czasu obliczeniowego węzła, które będą używane na platformie Azure. Aby uzyskać więcej informacji, zobacz [Szacowanie kosztu pieczenia platformy Azure](#Estimating-bake-cost) w dalszej części tego artykułu.
12. Ten komunikat informuje, gdzie wyniki wypieku zostaną zapisane po zakończeniu zadania.
13. *(Tylko użycie zaawansowane:)* Ten przycisk zmusza Unity do zapomnienia o upieczeniu, który został przesłany. Na przykład jeśli wyniki zostały pobrane przy użyciu innego komputera, wybierz przycisk **Wyczyść stan,** aby zapomnieć o tym zadaniu. Plik wynikowy, gdy będzie gotowy, *nie* zostanie pobrany. *To nie jest to samo, co anulowanie zadania. Zadanie, jeśli jest uruchomione, będzie nadal działać w chmurze.*
14. Wybierz ten przycisk, aby przesłać pieczenie do chmury. Gdy zadanie jest uruchomione, ten przycisk staje się **anuluj zadanie**.
15. Wybierz ten przycisk, aby przygotować się do przetwarzania [symulacji akustyki na komputerze](#Local-bake).
16. Ten obszar pokazuje stan pieca. Po zakończeniu pieczenia zostanie wyświetlone "Pobrane".

Zawsze możesz uzyskać pełne informacje o aktywnych zadaniach, pulach obliczeniowych i magazynie w [witrynie Azure portal](https://portal.azure.com).

Gdy zadanie jest uruchomione, etykieta przycisku **Piec** zmienia się na **Anuluj zadanie**. Użyj tego przycisku, aby anulować zadanie, które jest w toku. Zostanie wyświetlony monit o potwierdzenie. Anulowania zadania nie można cofnąć. Po anulowaniu nie będą dostępne żadne wyniki, ale nadal będzie naliczana opłata za dowolny czas użycia na platformie Azure.

Po rozpoczęciu pieca możesz zamknąć Unity. W zależności od projektu, typu węzła i liczby węzłów upieczenie chmury może potrwać kilka godzin. Stan zadania pieczenia zostanie zaktualizowany po ponownym załadowaniu projektu i otwarciu okna Akustyka. Jeśli zadanie zostanie zakończone, plik wyjściowy zostanie pobrany.

Ze względów bezpieczeństwa poświadczenia platformy Azure są przechowywane na komputerze lokalnym i skojarzone z edytorem Unity. Są one używane tylko do ustanowienia bezpiecznego połączenia z platformą Azure.

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>Znajdowanie stanu uruchomionego zadania w witrynie Azure portal

1. Znajdź identyfikator zadania pieczenia na karcie **Piec.**

    ![Identyfikator zadania pieca Unity wyróżniony na karcie Piec](media/unity-job-id.png)  

2. Otwórz [witrynę Azure portal](https://portal.azure.com), przejdź do konta usługi Batch, które było używane do pieczenia, i wybierz pozycję **Zadania**.

    ![Łącze Zadania w witrynie Azure portal](media/azure-batch-jobs.png)  

3. Wyszukaj identyfikator zadania na liście zadań.

   ![Stan zadania pieczenia wyróżniony w witrynie Azure portal](media/azure-bake-job-status.png)  

4. Wybierz identyfikator zadania, aby wyświetlić stan powiązanych zadań i ogólny stan zadania.

   ![Stan zadania pieczenia](media/azure-batch-task-state.png)  


### <a name="estimate-azure-bake-cost"></a><a name="Estimating-bake-cost"></a>Oszacuj koszt pieczenia platformy Azure

Aby oszacować, ile będzie kosztować wypiek, zacznij od wartości **Szacowany koszt obliczeń,** która jest czasem trwania. Pomnóż tę wartość przez koszt godzinowy w walucie lokalnej dla wybranego **typu węzła maszyny Wirtualnej.** Należy zauważyć, że wynik nie będzie zawierać czas węzła, który jest potrzebny do uzyskania węzłów i działa.

Załóżmy na przykład, że **wybierzesz Standard_F8s_v2** dla typu węzła, który ma koszt 0,40 USD/godz. Jeśli **szacowany koszt obliczeniowy** wynosi 3 godziny i 57 minut, szacowany koszt uruchomienia zadania wyniesie 0,40 USD * ~4 godziny = ~$1,60. Rzeczywisty koszt będzie prawdopodobnie nieco wyższa ze względu na dodatkowy czas, aby uzyskać węzły uruchomione.

Znajdź koszt węzłów godzinowych w [cenniku usługi Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux). (Wybierz **obliczenia zoptymalizowane pod kątem obliczeń** lub **obliczenia o wysokiej wydajności** jako kategorię).

## <a name="bake-your-scene-on-your-pc"></a><a name="Local-bake"></a>Upiecz swoją scenę na komputerze
Możesz również upiec scenę na własnym komputerze. Ta metoda może być przydatna do eksperymentowania z akustyką dla małych scen przed utworzeniem konta usługi Azure Batch. Należy jednak pamiętać, że lokalna symulacja akustyki może zająć dużo czasu w zależności od wielkości sceny.

### <a name="minimum-hardware-requirements"></a>Minimalne wymagania sprzętowe
* Procesor x86-64 z co najmniej 8 rdzeniami i 32 GB pamięci RAM
* [Funkcja Hyper-V włączona](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) do uruchamiania platformy Docker

Na przykład w naszych testach na 8-rdzeniowym komputerze, Intel Xeon E5-1660 @ 3 GHz i 32 GB pamięci RAM:
* Mała scena ze 100 sondami zajęła około 2 godzin na gruby piec lub 32 godziny na drobny piec.
* Średniej wielkości scena z 1000 sondami zajęła około 20 godzin na gruby piec lub 21 dni na drobny piec.

### <a name="set-up-docker"></a>Konfigurowanie platformy Docker
Zainstaluj i skonfiguruj program Docker na komputerze, który przetworzy symulację:
1. Zainstaluj [pulpit platformy Docker](https://www.docker.com/products/docker-desktop).
2. Otwórz pozycję Ustawienia platformy Docker, przejdź do **pozycji Zaawansowane**i skonfiguruj zasoby dla co najmniej 8 GB pamięci RAM. Im więcej procesorów można przydzielić do platformy Docker, tym szybciej piec zostanie ukończony.  
![Przykładowe ustawienia platformy Docker](media/docker-settings.png)
1. Przejdź do **folderu Dyski udostępnione**i włącz udostępnianie dysku używanego do przetwarzania.  
![Opcje udostępnionego dysku platformy Docker](media/docker-shared-drives.png)

### <a name="run-the-local-bake"></a>Uruchom lokalny piec
1. Wybierz przycisk **Przygotuj lokalne wypieki** na karcie **Piec.** Następnie wybierz lokalizację folderu, w którym chcesz zapisać pliki wejściowe i skrypty wykonywania. Następnie można uruchomić piec na dowolnym komputerze, o ile spełnia minimalne wymagania sprzętowe i zainstalować program Docker, kopiując folder do tego komputera.
2. Aby rozpocząć symulację, uruchom skrypt *runlocalbake.bat* w systemie Windows lub skrypt *runlocalbake.sh* w systemie MacOS. Ten skrypt pobiera obraz platformy Docker akustyka projektu z zestawem narzędzi niezbędnym do przetwarzania symulacji i rozpoczyna symulację.
3. Po zakończeniu symulacji skopiuj wynikowy plik *.ace* z powrotem do projektu Unity. Aby upewnić się, że Unity rozpoznaje go jako plik binarny, dołącz ".bytes" do rozszerzenia pliku (na przykład "Scene1.ace.bytes"). Szczegółowe dzienniki symulacji są przechowywane w *pliku AcousticsLog.txt.* Jeśli napotkasz jakiekolwiek problemy, sprawdź ten plik, aby zdiagnozować problem.

## <a name="data-files-added-by-the-bake-process"></a><a name="Data-Files"></a>Pliki danych dodane przez proces pieczenia

Następujące cztery pliki danych są tworzone podczas procesu pieczenia. Jeden zawiera wyniki symulacji i jest dostarczany z tytułem. Inne przechowują dane związane z edytorem Unity.

Wynik symulacji:
* *Assets/AcousticsData/Acoustics\_[SceneName].ace.bytes*: Ten plik jest tabelą odnośnika środowiska wykonawczego. Zawiera wyniki symulacji i woleksy akustyczne elementy sceny. Nazwę i lokalizację tego pliku można zmienić na karcie **Sondy.**

   *Należy uważać, aby nie usunąć pliku wyników symulacji. Nie można go odzyskać, z wyjątkiem ponownego wypiekania sceny.*

Edytor plików danych:
* *Assets/Editor/[SceneName]\_AcousticsParameters.asset*: Ten plik przechowuje dane wprowadzone w polach interfejsu użytkownika akustyki. Nie można zmienić nazwy i lokalizacji tego pliku.
* *Assets/AcousticsData/Editor/Acoustics_[SceneName].vox*: Ten plik przechowuje geometrię akustyki voxelized i właściwości materiału, które są obliczane po wybraniu przycisku **Oblicz** na karcie **Sondy.** Nazwę i lokalizację tego pliku można zmienić na karcie **Sondy.**
* *Assets/AcousticsData/Editor/Acoustics\_\_[SceneName] config.xml*: Ten plik przechowuje parametry symulacji obliczane po **wybraniu**opcji Oblicz . Nazwę i lokalizację tego pliku można zmienić na karcie **Sondy.**

## <a name="set-up-the-acoustics-lookup-table"></a>Konfigurowanie tabeli wyszukiwania akustyki
Przeciągnij prefabrykę **projektu Akustyka** z panelu projektu na scenę:

![Prefabrykowanie akustyki w Unity](media/acoustics-prefab.png)

Wybierz obiekt gry **ProjectAcoustics** i przejdź do jego panelu inspektora. Określ lokalizację wyniku wypieku (plik .ace w *obszarze Zasoby/AkustykaData):* Przeciągnij go do skryptu Menedżera akustyki lub wybierz przycisk okręgu obok pola tekstowego.

![Prefabrykowanie Menedżera Akustyki w Unity](media/acoustics-manager.png)

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z [kontrolkami projektu Unity](unity-workflow.md).
* Zapoznaj się z [koncepcjami projektowania akustyki projektu.](design-process.md)
