---
title: Tworzenie — samouczek dotyczący hałasu w projekcie
titlesuffix: Azure Cognitive Services
description: W tym samouczku opisano akustyczne pieczenie przy użyciu akustycznych projektów w środowisku Unity.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243124"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Tworzenie — samouczek dotyczący hałasu w projekcie
W tym samouczku opisano akustyczne pieczenie przy użyciu funkcji akustycznych projektu w środowisku Unity.

Wymagania dotyczące oprogramowania:
* [Unity 2018.2 +](https://unity3d.com) dla systemu Windows lub MacOS
* [Wtyczka akustyczna projektu zintegrowana z projektem środowiska Unity](unity-integration.md) lub [przykładową zawartością aparatu Unity w projekcie](unity-quickstart.md)
* *Opcjonalne:* [Konto Azure Batch](create-azure-account.md) umożliwiające przyspieszenie tworzony przy użyciu chmury obliczeniowej

## <a name="open-the-project-acoustics-bake-window"></a>Otwórz okno Tworzenie akustyczne projektu
W aparacie Unity wybierz pozycję **akustyczne** w menu **okno** .

![Edytor Unity z opcją akustyczne wyróżnioną w menu okno](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Tworzenie siatki nawigacji
Akustyczne projektu używa siatki nawigacji, aby umieścić punkty sondy odbiornika dla symulacji. Można użyć [przepływu pracy siatki nawigacji](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)środowiska Unity. Można też użyć innego pakietu modelowania 3W do projektowania własnej siatki.

## <a name="mark-acoustic-scene-objects"></a>Oznacz obiekty sceny akustycznych
Akustyczne projektu polega na dwóch typach obiektów sceny na potrzeby symulacji:
- Obiekty, które odzwierciedlają i occlude dźwięk w symulacji
- Siatka nawigacji odtwarzacza, która ogranicza punkty sondy odbiornika w symulacji

Oba typy obiektów są oznaczone za pomocą karty **obiekty** .

Ponieważ oznaczanie obiektów po prostu dodaje do obiektu składniki *AcousticsGeometry* lub *AcousticsNavigation* , można również użyć [przepływu pracy standardowego składnika Unity](https://docs.unity3d.com/Manual/UsingComponents.html) , aby oznaczyć lub usunąć oznaczenie obiektów. Można oznaczyć tylko renderowania siatki i terenów. Wszystkie inne typy obiektów zostaną zignorowane. Zaznacz pola wyboru lub usuń zaznaczenie wszystkich odpowiednich obiektów.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Oznacz zamknięcia akustyczny i geometrię odbicia
Otwórz kartę **obiekty** w oknie **akustyczny** . Oznacz wszystkie obiekty jako *geometryczne* , jeśli powinny occlude, odzwierciedlić lub wchłonąć dźwięk. Geometria akustyczna może obejmować takie rzeczy, jak Ziemia, ściany, dachy, okna i szyby okien, Rugs i duże meble. Można użyć dowolnego poziomu złożoności dla tych obiektów. Ponieważ scena jest voxelized przed symulacją, wysoce szczegółowe siatki, takie jak drzewa, które mają wiele liści, nie są droższe do tworzenie niż uproszczone obiekty.

Nie należy uwzględniać elementów, które nie powinny mieć wpływu na akustyczne, takich jak siatki kolizji niewidocznych.

Przekształcanie obiektu podczas obliczania sondy (za pomocą karty **sondy** ) zostało rozwiązane w wynikach tworzenie. Jeśli wszystkie oznaczone obiekty w scenie zostaną przeniesione później, należy ponownie wykonać obliczenia sondy i pieczenie.

### <a name="mark-the-navigation-mesh"></a>Oznacz siatkę nawigacji
Siatki nawigacji, które zostały utworzone za pomocą przepływu pracy Unity, zostaną pobrane przez system akustyczny. Aby użyć własnych siatek, oznacz je na karcie **obiekty** .

### <a name="for-reference-the-objects-tab-parts"></a>For Reference: elementy karty obiekty
Części strony karty (z obrazami po opisach) są następujące:

1. Przyciski wyboru karty (z wybraną kartą **obiekty** ). Za pomocą tych przycisków można przechodzić między różnymi krokami tworzenie, od lewej do prawej.
1. Krótki opis tego, co można zrobić za pomocą tej karty.
1. Dostępne filtry dla okna hierarchia. Użyj tych opcji, aby odfiltrować okno hierarchii do obiektów określonego typu, aby można je było łatwo oznaczyć. Jeśli nie zostało to jeszcze oznaczone jako elementy akustyczne, wybranie dwóch ostatnich opcji spowoduje wyświetlenie niczego. Jednak te opcje ułatwią znalezienie obiektów po ich oznaczeniu.
1. Gdy nie wybrano żadnych obiektów, ta sekcja pokazuje stan wszystkich obiektów w scenie.
    * Łącznie: całkowita liczba aktywnych, nieukrytych obiektów.
    * Zignorowane: liczba obiektów, które nie są renderowanymi przez renderki siatki ani terenami.
    * Siatka: liczba obiektów renderowania siatki.
    * Teren: liczba obiektów terenu.
    * Geometria: liczba obiektów siatki lub terenów, które są oznaczone jako **geometrii akustyczne**.
    * Nawigacja: liczba obiektów siatki lub terenu oznaczona jako Nawigacja w trybie **akustycznym**. Ta liczba nie obejmuje aparatu Unity NavMesh.
1. Całkowita liczba "oznaczonych" obiektów w scenie, które są tylko renderowania siatki i terenów. Użyj pól wyboru, aby oznaczyć (dodać odpowiedni składnik do) tych obiektów jako geometria lub Nawigacja dla hałasu.
1. Gdy nic nie jest zaznaczone, ta Uwaga przypomina o zaznaczeniu obiektów do oznaczenia, w razie potrzeby. Możesz również zaznaczyć jedno lub oba pola wyboru, aby oznaczyć wszystkie obiekty w scenie.
1. Po wybraniu obiektów w tej sekcji zostanie wyświetlony stan tylko wybranych obiektów.
1. Całkowita liczba zaznaczonych obiektów "oznaczonych jako". Zaznaczanie lub czyszczenie pól wyboru oznacza lub oznacza oznaczenie tylko wybranych obiektów.

Jeśli nie wybrano żadnych elementów sceny, karta **obiekty** wygląda jak na poniższej ilustracji.

![Karta obiekty akustyczne bez zaznaczenia](media/objects-tab-no-selection-detail.png)

Jeśli masz coś wybranego w oknie sceny lub hierarchii, karta wygląda jak na poniższej ilustracji.

![Karta obiekty akustyczne z wybranymi opcjami](media/objects-tab-selection-detail.png)

Jeśli niektóre obiekty są oznaczone i niektóre z nich nie są, odpowiednie pola wyboru pokazują wartość "Mixed", jak na poniższej ilustracji.

![Karta obiekty akustyczne z zaznaczonym wyborem ikon](media/mixed-object-selection-detail.png)

Zaznacz to pole wyboru, aby oznaczyć wszystkie elementy. Usuń zaznaczenie, aby usunąć zaznaczenie wszystkich obiektów.

Obiekty mogą być oznaczone zarówno do geometrii, jak i do nawigacji.

## <a name="select-acoustic-materials"></a>Wybierz materiały akustyczne
Po oznaczeniu obiektów wybierz przycisk **materiały** . Następnie przypisz Materiały akustyczne. System materiałów akustycznych projektu jest powiązany z systemem materiałów wizualizacji środowiska Unity. W przypadku dwóch obiektów, które mają oddzielne Materiały akustyczne, muszą mieć osobne materiały wizualne.

Wybór materiału akustycznego określa ilość energii dźwiękowej, która jest odwracana z każdej powierzchni. Domyślny materiał akustyczny ma absorpcję podobną do stalowej. Elementy akustyczne projektu sugerują materiały na podstawie nazwy materiału wizualnego. Można również przypisać materiał akustyczny **niestandardowy** do materiału w celu aktywowania regulowanego suwaka współczynnika absorpcji.

Czas reverberation danego materiału w pokoju jest odwrotnie związany ze współczynnikiem absorpcji. Większość materiałów ma wartości absorpcji z zakresu od 0,01 do 0,20. Materiały, które mają współczynniki absorpcji poza tym zakresem, są bardzo pochłaniane.

![Wykres pokazuje negatywną korelację czasu reverberation i współczynnika absorpcji.](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>For Reference: części karty materiały
![Karta Materiały akustyczne w aparacie Unity](media/materials-tab-detail.png)
1. Przycisk **materiały** wyświetla tę kartę.
2. Krótki opis tego, co można zrobić za pomocą tej karty.
3. Gdy to pole wyboru jest zaznaczone, na liście są wyświetlane tylko materiały, które są używane przez obiekty oznaczone jako **geometryczne** . W przeciwnym razie wyświetlane są wszystkie materiały, które są używane w scenie.
4. Te opcje umożliwiają zmianę kolejności opcji w menu w kolumnie ustawienia **akustyczne** (#6). Sortuj Materiały akustyczne według **nazwy** lub **absorptivity**, od niskich do dużych.
5. Alfabetyczna lista materiałów, które są używane w scenie. Jeśli jest zaznaczone pole wyboru **Pokaż tylko zaznaczone** (#3), wyświetlane są tylko materiały używane przez obiekty oznaczone jako **geometria** . Wybierz materiał tutaj, aby zaznaczyć wszystkie obiekty w scenie, które korzystają z tego materiału.
6. Materiał akustyczny, do którego przypisano materiał sceny. Zaznacz dowolny element, aby zmienić materiał akustyczny przypisany do tego materiału sceny. Aby zmienić kolejność sortowania tych menu, użyj opcji Sortuj ustawienia **akustyczne według** (#4).
7. Współczynnik absorpcji akustycznej materiału zaznaczonego w kolumnie z lewej strony (#6). Wartość 0 oznacza idealnie odbijającą (bez absorpcji), natomiast 1 oznacza doskonale absorptive (bez odbicia). Nie można zmienić współczynnika absorpcji, chyba że wybrany materiał jest **niestandardowy.**
8. Dla materiału oznaczonego jako **niestandardowy**, suwak jest aktywowany. Możesz przenieść suwak lub wpisać wartość, aby przypisać współczynnik absorpcji.

## <a name="calculate-and-review-listener-probe-locations"></a>Obliczanie i przeglądanie lokalizacji sondy odbiornika
Po przypisaniu materiałów przejdź do karty **sondy** . Wybierz pozycję **Oblicz** , aby umieścić punkty sondy odbiornika dla symulacji.

### <a name="what-the-calculate-button-does"></a>Co robi przycisk "Oblicz"
Przycisk **Oblicz** używa wybranej geometrii sceny w celu przygotowania sceny pod kątem symulacji:

- Staje się geometrią z siatki sceny i oblicza *wolumin Voxel*. Wolumin Voxel to wolumin całej sceny składający się z małych sześciennych "voxels". Rozmiar Voxel jest określany przez częstotliwość symulacji, która jest kontrolowana przez ustawienie **rozdzielczości symulacji** . Każdy Voxel jest oznaczony jako "Open Air" lub zawierający geometrię sceny. Jeśli Voxel zawiera geometrię, Voxel jest oznakowany współczynnik absorpcji materiału, który jest przypisany do tej geometrii.
- Używa siatki nawigacji, aby umieścić punkty sondy odbiornika. Algorytm równoważy konkurencyjne zagadnienia związane z pokryciem przestrzennym oraz czasem symulacji i rozmiarem pliku. Celem jest zapewnienie, że wąskie korytarze i małe miejsca zawsze mają pewne pokrycie. Typowe liczby punktów sondowania mieszczą się w zakresie od 100 dla małych scen do kilku tysięcy w przypadku dużych scen.

W zależności od rozmiaru sceny i szybkości działania tych obliczeń może potrwać kilka minut.

### <a name="review-voxel-and-probe-placement"></a>Przejrzyj Voxel i rozmieszczenie sondy
Wyświetl podgląd lokalizacji danych i punktów sondy Voxel, aby upewnić się, że wszystko jest gotowe do tworzenieu sceny. Niepełna siatka nawigacji lub brakująca lub dodatkowa geometria jest zwykle łatwo widoczna w wersji zapoznawczej. Włączać lub wyłączać Voxel oraz umieszczanie sond przy użyciu menu **elementy Gizmo** .

![Menu elementy Gizmo w środowisku Unity](media/gizmos-menu.png)

Voxels, które zawierają geometrię akustyczną, są wyświetlane jako zielone moduły. Eksploruj swoją scenę i sprawdź, czy wszystkie elementy, które powinny być geometryczne, mają voxels. Aparat sceny musi znajdować się w przedziale około 5 metrów obiektu dla voxels do wyświetlenia.

W przypadku porównania voxels utworzonego przy użyciu rozdzielczości szerokiej i precyzyjnej rozdzielczości zobaczysz, że Gruby voxels jest dwa razy większy.

![Gruba voxels Preview w edytorze Unity](media/voxel-cubes-preview.png)

Wyniki symulacji są interpolowane między lokalizacjami punktu sondy odbiornika w czasie wykonywania. Sprawdź, czy punkty sondy znajdują się w sąsiedztwie wszystkich miejsc, w których gracz jest oczekiwany w scenie.

![Sondy Podgląd w edytorze Unity](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Weź pod uwagę nazwę sceny
Nazwa sceny służy do łączenia sceny z plikami, które przechowują rozmieszczenie punktów sondy i voxelization. Jeśli zmienisz nazwę sceny po obliczeniu punktów sondowania, dane o przypisaniu i umieszczeniu materiału zostaną utracone i należy je ponownie uruchomić.

### <a name="for-reference-parts-of-the-probes-tab"></a>For Reference: części karty sondy
![Karta sondy akustyczne w aparacie Unity](media/probes-tab-detail.png)

1. Przycisk **sondy** powoduje wyświetlenie tej karty.
2. Krótki opis czynności, które można wykonać na tej karcie.
3. Użyj tych opcji, aby ustawić duże lub precyzyjne rozwiązanie symulacji. Duże jest szybsze, ale istnieją kompromisy. Aby uzyskać szczegółowe informacje, zobacz [rozpoznawanie tworzenie](bake-resolution.md).
4. Określa, gdzie mają zostać umieszczone pliki danych akustycznych. Wybierz przycisk " **...** ", aby uzyskać dostęp do selektora folderów. Domyślna lokalizacja to *Assets/AcousticsData*. Podfolder *edytora* również jest tworzony w tej lokalizacji. Aby uzyskać więcej informacji, zobacz [pliki danych dodane przez proces tworzenie](#Data-Files)w dalszej części tego artykułu.
5. Prefiks, który jest określony w tym miejscu, służy do nazwy plików danych dla tej sceny. Wartość domyślna to "Acoustics_ *[nazwa sceny]* ".
6. Po obliczeniu sond, które właśnie opisano, są wyłączone. Wybierz przycisk **Wyczyść** , aby wymazać obliczenia i włączyć kontrolki, aby można było je ponownie obliczyć przy użyciu nowych ustawień.
7. Wybierz pozycję **Oblicz** , aby voxelize scenę i Oblicz lokalizacje punktów sondowania. Obliczenia są wykonywane lokalnie na komputerze. Należy to zrobić przed wykonaniem tworzenie.

W tej wersji programu Project akustyczne sondy nie mogą być umieszczane ręcznie. Użyj zautomatyzowanego procesu na karcie **sondy** .

Aby uzyskać więcej informacji na temat nieograniczonego i precyzyjnego rozwiązywania problemów, zobacz [rozpoznawanie tworzenie](bake-resolution.md).

## <a name="bake-your-scene-by-using-azure-batch"></a>Tworzenie scenę przy użyciu Azure Batch
Swoją scenę można tworzenie w klastrze obliczeniowym w chmurze za pomocą usługi Azure Batch. Wtyczka aparatu Unity akustyczna projektu łączy się bezpośrednio z Azure Batch, aby utworzyć wystąpienie klastra Azure Batch, zarządzać nim i wyłączać go dla każdego tworzenieu. Na karcie **Tworzenie** wprowadź swoje poświadczenia platformy Azure, wybierz typ i rozmiar maszyny klastra, a następnie wybierz pozycję **Tworzenie**.

### <a name="for-reference-parts-of-the-bake-tab"></a>For Reference: części karty Tworzenie
![Karta Tworzenie akustycznych w aparacie Unity](media/bake-tab-details.png)

1. Na tej karcie jest wyświetlana przycisk **Tworzenie** .
2. Krótki opis tego, co można zrobić na tej stronie.
3. Wprowadź swoje poświadczenia platformy Azure w tych polach po utworzeniu konta platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie konta Azure Batch](create-azure-account.md).
4. Pole tag obrazu platformy Docker dla zestawu narzędzi "akustyczne".
5. Otwiera Azure Portal w celu zarządzania subskrypcjami, monitorowania użycia i wyświetlania informacji dotyczących rozliczeń.
6. Określa typ węzła obliczeniowego Azure Batch, który ma być używany do obliczania. Typ węzła musi być obsługiwany przez lokalizację centrum danych platformy Azure. Jeśli nie masz pewności, pozostaw to **Standard_F8s_v2**.
7. Liczba węzłów do użycia podczas obliczania. Ta liczba ma wpływ na czas tworzenie. Jest on ograniczony przez alokację Azure Batch Core. Alokacja domyślna zezwala tylko na dwa węzły 8-rdzeniowe lub węzeł 1 16-rdzeń, ale może być rozwinięty. Aby uzyskać więcej informacji na temat ograniczeń podstawowych alokacji, zobacz [Tworzenie konta Azure Batch](create-azure-account.md).
8. Zaznacz to pole wyboru, aby skonfigurować pulę obliczeniową do używania [węzłów o niskim priorytecie](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Węzły obliczeniowe o niskim priorytecie mają znacznie niższy koszt. Ale mogą one nie zawsze być dostępne lub mogą być zastępujące w dowolnym momencie.
9. Liczba sond dla sceny, obliczona na karcie **sondy** . Liczba sond określa liczbę symulacji, które muszą być uruchamiane w chmurze. Nie można określić większej liczby węzłów niż sondy.
10. Oszacowanie czasu, przez który zadanie zostanie wykonane w chmurze, z wyłączeniem czasu uruchamiania węzła. Po rozpoczęciu wykonywania zadania to pole pokazuje oszacowanie czasu, po upływie którego wyniki zostaną przywrócone.
11. Łączna ilość czasu obliczeniowego wymaganego do uruchomienia symulacji. Ta wartość to łączny czas obliczeniowy węzła, który będzie używany na platformie Azure. Aby uzyskać więcej informacji, zobacz [szacunkowy koszt platformy Azure tworzenie](#Estimating-bake-cost) w dalszej części tego artykułu.
12. Ten komunikat informuje, gdzie zostaną zapisane wyniki tworzenie po zakończeniu zadania.
13. *(Tylko w przypadku użycia zaawansowanego:)* ten przycisk wymusza, aby środowisko Unity zapomnieć o przesłanym przez Ciebie tworzenie. Na przykład jeśli pobrano wyniki przy użyciu innego komputera, wybierz przycisk **Wyczyść stan** , aby zapomnieć to zadanie. W przypadku gotowości plik wynikowy *nie* zostanie pobrany. *Ta wartość nie jest taka sama jak anulowanie zadania. Zadanie, jeśli działa, będzie nadal działać w chmurze.*
14. Wybierz ten przycisk, aby przesłać tworzenie do chmury. Gdy zadanie jest uruchomione, ten przycisk zostaje **anulowany**.
15. Wybierz ten przycisk, aby przygotować się do przetwarzania [symulacji akustycznej na komputerze](#Local-bake).
16. Ten obszar pokazuje stan tworzenie. Po zakończeniu tworzenie zostanie wyświetlona wartość "pobrane".

W [Azure Portal](https://portal.azure.com)można zawsze uzyskać pełne informacje dotyczące aktywnych zadań, pul obliczeniowych i magazynu.

Gdy zadanie jest uruchomione, etykieta przycisku **Tworzenie** zmienia się na **Anuluj zadanie**. Użyj tego przycisku, aby anulować zadanie, które jest w toku. Zostanie wyświetlony monit o potwierdzenie. Anulowanie zadania nie może być cofnięte. Po anulowaniu nie będą dostępne żadne wyniki, ale nadal będzie naliczana opłata za dowolny użyty czas obliczeń platformy Azure.

Po rozpoczęciu tworzenie można zamknąć środowisko Unity. W zależności od projektu, typu węzła i liczby węzłów tworzenie w chmurze może potrwać kilka godzin. Stan zadania tworzenie zostanie zaktualizowany po ponownym załadowaniu projektu i otwarciu okna "akustyczne". Jeśli zadanie zostanie ukończone, plik wyjściowy zostanie pobrany.

W celu zapewnienia bezpieczeństwa poświadczenia platformy Azure są przechowywane na komputerze lokalnym i kojarzone z edytorem aparatu Unity. Są one używane tylko w celu nawiązania bezpiecznego połączenia z platformą Azure.

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>Znajdź stan uruchomionego zadania na Azure Portal

1. Znajdź identyfikator zadania tworzenie na karcie **Tworzenie** .

    ![Identyfikator zadania Tworzenie środowiska Unity wyróżniony na karcie Tworzenie](media/unity-job-id.png)  

2. Otwórz [Azure Portal](https://portal.azure.com), przejdź do konta usługi Batch, które zostało użyte w tworzenie, a następnie wybierz pozycję **Jobs (zadania**).

    ![Link zadania w Azure Portal](media/azure-batch-jobs.png)  

3. Wyszukaj identyfikator zadania na liście zadań.

   ![W Azure Portal zostanie wyróżniony stan zadania tworzenie](media/azure-bake-job-status.png)  

4. Wybierz identyfikator zadania, aby wyświetlić stan powiązanych zadań i ogólny stan zadania.

   ![Stan zadania tworzenie](media/azure-batch-task-state.png)  


### <a name="Estimating-bake-cost"></a>Oszacuj koszt usługi Azure tworzenie

Aby oszacować, co tworzenie będzie kosztować, Rozpocznij od **szacowanej wartości kosztu obliczeniowego** , czyli czasu trwania. Pomnóż tę wartość przez koszt godzinowy w lokalnej walucie dla wybranych **typów węzłów maszyny wirtualnej** . Należy zauważyć, że wynik nie będzie zawierać czasu węzła, który jest wymagany do uruchomienia węzłów.

Załóżmy na przykład, że wybrano pozycję **Standard_F8s_v2** dla typu węzła, który ma koszt $0.40/hr. Jeśli **szacowany koszt obliczeniowy** wynosi 3 godziny i 57 minut, szacowany koszt uruchomienia zadania będzie $0,40 * ~ 4 godziny = ~ $1,60. Rzeczywisty koszt będzie prawdopodobnie nieco większy z powodu dodatkowego czasu na rozpoczęcie uruchamiania węzłów.

Znajdź koszt węzłów co godzinę w [Azure Batch cenach](https://azure.microsoft.com/pricing/details/virtual-machines/linux). (Wybierz opcję **obliczenia zoptymalizowane** lub **o wysokiej wydajności** jako kategoria).

## <a name="Local-bake"></a>Tworzenie swoje sceny na komputerze
Możesz również tworzenie swoją scenę na swoim komputerze. Ta metoda może być przydatna do eksperymentowania z dźwiękiem dla małych scen przed utworzeniem konta Azure Batch. Należy jednak pamiętać, że w zależności od rozmiaru sceny może upłynąć dużo czasu.

### <a name="minimum-hardware-requirements"></a>Minimalne wymagania sprzętowe
* Procesor x86-64 z co najmniej 8 rdzeniami i 32 GB pamięci RAM
* [Funkcja Hyper-V włączona](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) do uruchomienia platformy Docker

Przykładowo w teście na maszynie 8-rdzeniowej Intel Xeon E5-1660 @ 3 GHz i 32 GB pamięci RAM:
* Niewielka scena z 100 sondami trwała około 2 godzin w przypadku bardzo długiego tworzenieu lub 32 godz. Tworzenie.
* Sceny o średnim rozmiarze z 1 000 sondami zajęły około 20 godzin przez bardzo duże Tworzenie lub 21 dni w celu uzyskania dokładnej tworzenie.

### <a name="set-up-docker"></a>Skonfiguruj platformę Docker
Zainstaluj i skonfiguruj platformę Docker na komputerze, który będzie przetwarzać symulację:
1. Zainstaluj program [Docker Desktop](https://www.docker.com/products/docker-desktop).
2. Otwórz ustawienia platformy Docker, przejdź do pozycji **Zaawansowane**i skonfiguruj zasoby dla co najmniej 8 GB pamięci RAM. Im więcej procesorów CPU można przydzielić do platformy Docker, tym szybciej tworzenie zostanie ukończona.  
![Sample Docker Settings @ no__t-1
1. Przejdź do obszaru **udostępnione dyski**i Włącz udostępnianie dysku używanego do przetwarzania.  
![Docker opcje dysku udostępnionego @ no__t-1

### <a name="run-the-local-bake"></a>Uruchamianie lokalnego tworzenie
1. Wybierz przycisk **Przygotuj lokalny tworzenie** na karcie **Tworzenie** . Następnie wybierz lokalizację folderu, do której mają zostać zapisane pliki wejściowe i skrypty wykonywania. Następnie można uruchomić tworzenie na dowolnym komputerze, o ile spełnia minimalne wymagania sprzętowe i zainstaluje platformę Docker przez skopiowanie folderu na tę maszynę.
2. Aby rozpocząć symulację, uruchom skrypt *runlocalbake. bat* w systemie Windows lub w skrypcie *runlocalbake.sh* na MacOS. Ten skrypt pobiera obraz platformy Docker z projektorem z zestawem narzędzi niezbędnych do przetwarzania symulacji i uruchamia symulację.
3. Po zakończeniu symulacji Skopiuj powstały plik *ACE* z powrotem do projektu aparatu Unity. Aby upewnić się, że aparat Unity rozpoznaje go jako plik binarny, należy dołączyć ". b" do rozszerzenia pliku (na przykład "Scene1. asa. Bytes"). Szczegółowe dzienniki symulacji są przechowywane w *AcousticsLog. txt.* Jeśli napotkasz jakiekolwiek problemy, zbadaj ten plik, aby ułatwić zdiagnozowanie problemu.

## <a name="Data-Files"></a>Pliki danych dodane przez proces tworzenie

Podczas procesu tworzenie tworzone są następujące cztery pliki danych. Jedna z nich zawiera wyniki symulacji i jest dostarczana z Twoim tytułem. Inne osoby przechowują dane dotyczące edytora Unity.

Wynik symulacji:
* *Zasoby/AcousticsData/akustyczne @ no__t-1 [scenname]. ACE. Bytes*: ten plik jest tabelą odnośników czasu wykonywania. Zawiera wyniki symulacji i voxelized elementy sceny akustycznych. Nazwę i lokalizację tego pliku można zmienić na karcie **sondy** .

   *Należy zachować ostrożność, aby nie usunąć pliku z wynikami symulacji. Nie jest to możliwe do odzyskania, z wyjątkiem sytuacji, w której nie należy odzyskać sceny*

Pliki danych edytora:
* *Zasoby/Edytor/[scena] \_AcousticsParameters. Asset*: ten plik przechowuje dane wprowadzane w polach w interfejsie użytkownika. Nie można zmienić nazwy i lokalizacji tego pliku.
* *Majątek/AcousticsData/redaktor/Acoustics_ [scena]. Vox*: ten plik przechowuje geometrię voxelized i właściwości materiału, które są obliczane po wybraniu przycisku **Oblicz** na karcie **sondy** . Nazwę i lokalizację tego pliku można zmienić na karcie **sondy** .
* *Zasoby/AcousticsData/Edytor/akustyczne @ no__t-1 [scenname] @no__t -2config. XML*: ten plik przechowuje parametry symulacji, które są obliczane po wybraniu opcji **Oblicz**. Nazwę i lokalizację tego pliku można zmienić na karcie **sondy** .

## <a name="set-up-the-acoustics-lookup-table"></a>Konfigurowanie tabeli wyszukiwania akustycznego
Przeciągnij PREFAB **akustyczny projektu** z panelu projektu do swojej sceny:

![PREFAB akustyczne w środowisku Unity](media/acoustics-prefab.png)

Wybierz obiekt Game **ProjectAcoustics** , a następnie przejdź do panelu Inspektora. Określ lokalizację wyniku tworzenie (plik ACE, w obszarze *Assets/AcousticsData*): przeciągnij go do skryptu menedżera akustycznego lub wybierz przycisk kółka obok pola tekstowego.

![Menedżer akustyczny PREFAB w środowisku Unity](media/acoustics-manager.png)

## <a name="next-steps"></a>Następne kroki
* Eksploruj [kontrolki projektowe aparatu Unity](unity-workflow.md).
* Eksploruj [koncepcje projektu dotyczące akustycznych projektów](design-process.md).
