---
title: Tworzenie — samouczek dotyczący hałasu w projekcie
titlesuffix: Azure Cognitive Services
description: W tym samouczku opisano akustyczne pieczenie przy użyciu akustycznych projektów w środowisku Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 2362b3916d1b1f430350d975dc0b61914a777be2
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706693"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Tworzenie — samouczek dotyczący hałasu w projekcie
W tym samouczku opisano akustyczne pieczenie przy użyciu akustycznych projektów w środowisku Unity.

Wymagania dotyczące oprogramowania:
* [Unity 2018.2 +](https://unity3d.com) dla systemu Windows
* [Wtyczka akustyczna projektu zintegrowana w projekcie Unity](unity-integration.md) lub [Przykładowa zawartość aparatu Unity w projekcie](unity-quickstart.md)
* Opcjonalnie: [Konto Azure Batch](create-azure-account.md) umożliwiające przyspieszenie tworzony przy użyciu chmury obliczeniowej

## <a name="open-the-project-acoustics-bake-window"></a>Otwórz okno Tworzenie akustyczne projektu
Wybierz **okno > akustyczne** z menu aparatu Unity:

![Zrzut ekranu edytora Unity z opcją menu okna akustyczne](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Tworzenie siatki nawigacji
Akustyczne projektu używa siatki nawigacji, aby umieścić punkty sondy odbiornika dla symulacji. Aby zaprojektować własną siatkę, można użyć [przepływu pracy siatki nawigacji](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)lub użyć innego pakietu modelowania 3W. 

## <a name="mark-acoustic-scene-objects"></a>Oznacz obiekty sceny akustycznych
Akustyczne projektu opiera się na dwóch typach obiektów sceny dla symulacji: obiekty, które będą odzwierciedlać i occlude dźwięk w symulacji, oraz siatkę nawigacji odtwarzacza, która ogranicza punkty sondy odbiornika w symulacji. Oba typy obiektów są oznaczone za pomocą karty **obiekty** . 

Ponieważ oznaczanie obiektów po prostu dodaje do obiektu składniki **AcousticsGeometry** lub **AcousticsNavigation** , można również użyć [przepływu pracy standardowego składnika Unity](https://docs.unity3d.com/Manual/UsingComponents.html) , aby oznaczyć lub usunąć oznaczenie obiektów. Tylko renderowanie siatki i tereny mogą być oznaczone. Wszystkie inne typy obiektów zostaną zignorowane. Pola wyboru spowodują oznaczenie lub usunięcie zaznaczenia wszystkich obiektów, których to dotyczy.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Oznacz zamknięcia akustyczny i geometrię odbicia
Otwórz kartę **obiekty** w oknie **akustyczny** . Oznacz wszystkie obiekty jako **geometryczne** , jeśli powinny occlude, odzwierciedlić lub wchłonąć dźwięk. Geometria akustyczna może obejmować takie rzeczy, jak Ziemia, ściany, dachy, szyby okna & Windows, Rugs i duże meble. Można użyć dowolnego poziomu złożoności dla tych obiektów. Ponieważ scena jest voxelized przed symulacją, wysoce szczegółowe siatki, takie jak drzewa z wieloma małymi liśćmi, nie są droższe do tworzenie niż uproszczone obiekty.

Nie należy uwzględniać elementów, które nie powinny mieć wpływu na akustyczne, takich jak siatki kolizji niewidocznych.

Przekształcanie obiektu w czasie obliczania sondy (za pomocą karty sondy poniżej) jest ustalone w wynikach tworzenie. Przeniesienie dowolnego oznaczonego obiektu w scenie wymaga ponownego wykonania obliczeń sondy i odprowadzenia sceny.

### <a name="mark-the-navigation-mesh"></a>Oznacz siatkę nawigacji
Siatki nawigacji utworzone za pomocą przepływu pracy aparatu Unity zostaną pobrane przez system akustyczny. Aby użyć własnych siatek, oznacz je na karcie **obiekty** .

### <a name="for-reference-the-objects-tab-parts"></a>Aby uzyskać odwołanie: Części karty obiekty
Części karty są następujące:

1. Przyciski wyboru karty (wybrana karta**obiekty** ). Użyj tych przycisków, aby zapoznać się z różnymi krokami wykonywania tworzenie akustycznych od lewej do prawej.
2. Krótki opis tego, co należy zrobić, korzystając z tej strony.
3. Dostępne filtry dla okna hierarchia. Służy do filtrowania okna hierarchii do obiektów określonego typu, dzięki czemu można łatwiej je oznaczyć. Jeśli nie oznaczono jeszcze niczego do poziomu akustycznego, wybranie dwóch ostatnich opcji spowoduje wyświetlenie niczego. Mogą jednak być przydatne do znajdowania zaznaczonych obiektów po wykonaniu tej czynności.
4. Gdy nie wybrano żadnych obiektów, ta sekcja pokazuje stan wszystkich obiektów w scenie:
    * Łącznie — całkowita liczba aktywnych, nieukrytych obiektów w scenie.
    * Zignorowane — liczba obiektów, które nie są renderowania lub terenów siatki.
    * Siatka — liczba obiektów renderowania siatki w scenie
    * Teren — liczba obiektów terenu w scenie
    * Geometria — liczba obiektów siatki lub terenów w scenie oznaczona jako "geometria".
    * Nawigacja — liczba obiektów siatki lub terenów w scenie oznaczona jako "Nawigacja akustyczna". Ta liczba nie zawiera NavMesh aparatu Unity.
5. Pokazuje łączną liczbę obiektów "Mark-Only" w scenie, która jest tylko renderowania siatki i terenów. Pokazuje pola wyboru, których można użyć do oznaczenia (Dodaj odpowiedni składnik do) tych obiektów jako geometria lub Nawigacja w przypadku hałasu
6. Gdy nic nie jest zaznaczone, ta Uwaga przypomina o zaznaczeniu obiektów do oznaczenia w razie potrzeby. Możesz również zaznaczyć jedno lub oba pola wyboru, aby oznaczyć wszystkie obiekty w scenie bez zaznaczania żadnych elementów.
7. Po wybraniu obiektów w tej sekcji zostanie wyświetlony stan tylko wybranych obiektów.
8. Pokazuje łączną liczbę wybranych obiektów "Mark-selected". Zaznaczenie lub usunięcie zaznaczenia pól wyboru spowoduje oznaczenie lub odróżnienie tylko wybranych obiektów.

Jeśli nie wybrano żadnych elementów sceny, karta obiekty będzie wyglądać jak na poniższej ilustracji:

![Zrzut ekranu karty obiekty akustyczne bez zaznaczenia](media/objects-tab-no-selection-detail.png)

Jeśli masz coś wybranego w oknie sceny lub hierarchii, będzie wyglądać tak, jak na poniższej ilustracji:

![Zrzut ekranu karty obiekty akustyczne z pokazanym wyborem](media/objects-tab-selection-detail.png)

Jeśli niektóre obiekty są oznaczone, a niektóre nie, w odpowiednim polu wyboru zostanie wyświetlona wartość "Mixed":

![Zrzut ekranu karty obiekty akustyczne z wyróżnioną ikoną zaznaczenia mieszanego](media/mixed-object-selection-detail.png)

Kliknięcie pola wyboru spowoduje wymuszenie oznaczenia wszystkich obiektów, a kliknięcie przycisku ponownie spowoduje usunięcie zaznaczenia wszystkich obiektów.

Obiekty mogą być oznaczone zarówno do geometrii, jak i do nawigacji.

## <a name="select-acoustic-materials"></a>Wybierz materiały akustyczne
Po oznaczeniu obiektów kliknij przycisk **materiały** i przypisz Materiały akustyczne. System materiałów akustycznych projektu jest powiązany z systemem materiałów wizualizacji aparatu Unity: w przypadku dwóch obiektów mających oddzielne Materiały akustyczne muszą mieć osobne materiały wizualne.

Materiały akustyczne kontrolują ilość energii dźwiękowej odzwierciedloną z tyłu z każdej powierzchni. Domyślny materiał akustyczny ma absorpcję podobną do konkretnych. Elementy akustyczne projektu sugerują materiały na podstawie nazwy materiału wizualnego. Można przypisać materiał akustyczny "niestandardowy" do materiału, aby włączyć suwak współczynnika absorpcji.

Czas reverberation danego materiału w pokoju jest odwrotnie związany ze współczynnikiem absorpcji, z większością materiałów mających wartości absorpcji z zakresu od 0,01 do 0,20. Materiały z współczynnikiem absorpcji poza tym zakresem są bardzo pochłaniane.

![Wykres przedstawiający ujemną korelację czasu reverberation z współczynnikiem absorpcji](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Aby uzyskać odwołanie: Części karty materiały
![Zrzut ekranu karty Materiały akustyczne w aparacie Unity](media/materials-tab-detail.png)

1. Przycisk karta **materiały** służący do wypełniania tej strony.
2. Krótki opis tego, co należy zrobić, korzystając z tej strony.
3. Po zaznaczeniu tego pola wyboru będą wyświetlane tylko materiały używane przez obiekty oznaczone jako geometryczne. W przeciwnym razie zostaną wyświetlone wszystkie materiały używane w scenie.
4. Użyj tych opcji, aby zmienić kolejność menu rozwijanego, które jest wyświetlane po kliknięciu listy rozwijanej w kolumnie akustyczne poniżej (#6). **Nazwa** sortuje Materiały akustyczne według nazwy. "Absorptivity" sortuje je w kolejności od absorptivity z niski do wysoki.
5. Lista materiałów użytych w scenie, posortowana alfabetycznie. Jeśli pole wyboru **Pokaż zaznaczone tylko** jest zaznaczone (#3), wyświetlane są tylko materiały używane przez obiekty oznaczone jako geometria. Kliknięcie materiału spowoduje wybranie wszystkich obiektów w scenie, które używają tego materiału.
6. Pokazuje materiał akustyczny, do którego przypisano materiał sceny. Kliknij listę rozwijaną, aby ponownie przypisać materiał sceny do innego materiału akustycznego. Można zmienić kolejność sortowania menu wyświetlanego po kliknięciu elementu w tym miejscu przy użyciu opcji Sortuj wartości **akustyczne według:** powyżej (#4).
7. Pokazuje współczynnik absorpcji akustycznej materiału zaznaczonego w poprzedniej kolumnie. Wartość zerowa oznacza idealnie odbijającą (bez absorpcji), natomiast wartość 1 oznacza doskonale absorptive (bez odbicia). Nie można zmienić współczynnika absorpcji, chyba że wybrany materiał jest "niestandardowy".
8. W przypadku materiału przypisanego do elementu "Custom" suwak nie jest już wyłączony i można wybrać współczynnik absorpcji przy użyciu suwaka lub wpisując wartość.

## <a name="calculate-and-review-listener-probe-locations"></a>Obliczanie i przeglądanie lokalizacji sondy odbiornika
Po przypisaniu materiałów przejdź do karty **sondy** , a następnie kliknij pozycję **Oblicz** , aby umieścić punkty sondy odbiornika dla symulacji.

### <a name="what-the-calculate-button-calculates"></a>Co to jest "Oblicz..." Obliczanie przycisku
Przycisk **Oblicz...** używa wybranej geometrii sceny, aby przygotować swoją scenę pod kątem symulacji:

1. Staje się geometrią z siatki sceny i oblicza wolumin Voxel. Wolumin Voxel to trójwymiarowy wolumin, który obejmuje całą scenę i składa się z małych sześciennych "voxels". Rozmiar voxels jest określany przez częstotliwość symulacji ustawioną przez ustawienie **rozdzielczości symulacji** . Każdy Voxel jest oznaczony jako "Open Air" lub zawierający geometrię sceny. Jeśli Voxel zawiera geometrię, Voxel jest oznakowany współczynnik absorpcji materiału przypisanego do tej geometrii.
2. Używa siatki nawigacji, aby umieścić punkty sondy odbiornika. Algorytm równoważy konkurencyjne zagadnienia związane z pokryciem przestrzennym oraz czasem symulacji i rozmiarem pliku, zapewniając, że wąskie korytarze i małe miejsca zawsze mają pewną ilość pokrycia. Typowe liczby punktów sondowania mieszczą się w zakresie od 100 dla małych scen do kilku tysięcy w przypadku dużych scen.

W zależności od rozmiaru sceny i szybkości działania tych obliczeń może potrwać kilka minut.

### <a name="review-voxel-and-probe-placement"></a>Przejrzyj Voxel i rozmieszczenie sondy
Wyświetl podgląd danych Voxel i lokalizacji punktów sondowania, aby upewnić się, że wszystko jest gotowe do tworzenienia sceny. Niepełna siatka nawigacji lub brakująca lub dodatkowa geometria jest zwykle szybko widoczna w wersji zapoznawczej. Voxel i umieszczanie sondy można włączyć lub wyłączyć za pomocą menu elementy Gizmo:

![Zrzut ekranu przedstawiający menu elementy Gizmo w środowisku Unity](media/gizmos-menu.png)

Voxels zawierające geometrię akustyczną są wyświetlane jako zielone moduły. Eksploruj swoją scenę i sprawdź, czy wszystkie elementy, które powinny być geometryczne, mają voxels. Aparat sceny musi znajdować się w przedziale około 5 metrów obiektu dla voxels do wyświetlenia.

W przypadku porównania voxels utworzonego za pomocą grubej rozdzielczości i rozdzielczości, zobaczysz, że Gruby voxels są dwa razy większe.

![Zrzut ekranu przedstawiający grubą wersję zapoznawczą voxels w edytorze Unity](media/voxel-cubes-preview.png)

Wyniki symulacji są interpolowane między lokalizacjami punktu sondy odbiornika w czasie wykonywania. Sprawdź, czy w sąsiedztwie znajdują się punkty sondy znajdujące się w dowolnym miejscu.

![Zrzut ekranu podglądu sond w edytorze Unity](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Weź pod uwagę nazwę sceny
Nazwa sceny służy do łączenia sceny z plikami przechowującymi rozmieszczenie punktów sondy i voxelization. Jeśli scena zostanie zmieniona po obliczeniu punktów sondy, dane o przypisaniu i umieszczeniu materiału zostaną utracone i należy je ponownie uruchomić.

### <a name="for-reference-parts-of-the-probes-tab"></a>Aby uzyskać odwołanie: Części karty sondy
![Zrzut ekranu karty sondy akustyczne w aparacie Unity](media/probes-tab-detail.png)

1. Przycisk karta sondy służący do wypełniania tej strony
2. Krótki opis czynności, które należy wykonać przy użyciu tej strony
3. Użyj tych opcji, aby wybrać duże lub precyzyjne rozwiązanie symulacji. Duże jest szybsze, ale ma pewne kompromisy. Aby uzyskać szczegółowe informacje, zobacz [rozwiązanie tworzenie](bake-resolution.md) .
4. Wybierz lokalizację, w której mają być umieszczone pliki danych akustycznych przy użyciu tego pola. Kliknij przycisk z "..." Aby użyć selektora folderów. Wartość domyślna to **Assets/AcousticsData**. Podfolder **edytora** również zostanie utworzony w tej lokalizacji. Aby uzyskać więcej informacji na temat plików danych, zobacz [pliki danych](#Data-Files) poniżej.
5. Pliki danych dla tej sceny będą nazwane przy użyciu prefiksu podanego w tym miejscu. Wartość domyślna to "Acoustics_ [nazwa sceny]".
6. Po obliczeniu sond, powyższe kontrolki zostaną wyłączone. Kliknij przycisk **Wyczyść** , aby wymazać obliczenia i włączyć kontrolki, aby można było ponownie obliczyć przy użyciu nowych ustawień.
7. Kliknij przycisk **Oblicz...** , aby voxelize scenę i obliczyć lokalizacje punktów sondowania. Ta czynność jest wykonywana lokalnie na maszynie i musi zostać wykonana przed wykonaniem tworzenie.

W tej wersji programu Project akustyczne sondy nie mogą być umieszczane ręcznie i muszą być umieszczone w zautomatyzowanym procesie podanym na karcie sondy.

Zobacz [rozwiązanie tworzenie](bake-resolution.md) , aby uzyskać więcej informacji na temat nieograniczonej rozdzielczości programu vs.

## <a name="bake-your-scene-using-azure-batch"></a>Tworzenie scenę przy użyciu Azure Batch
Swoją scenę można tworzenie z klastrem obliczeniowym w chmurze przy użyciu usługi Azure Batch. Wtyczka aparatu Unity akustycznych projektu łączy się bezpośrednio z Azure Batch, aby utworzyć wystąpienie klastra Azure Batch, zarządzać nim i wyłączać go dla każdego tworzenieu. Na karcie **Tworzenie** wprowadź swoje poświadczenia platformy Azure, wybierz typ i rozmiar maszyny klastra, a następnie kliknij przycisk **Tworzenie**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Aby uzyskać odwołanie: Części karty Tworzenie
![Zrzut ekranu karty Tworzenie akustycznych w aparacie Unity](media/bake-tab-details.png)

1. Przycisk karty Tworzenie używany do wyłączenia tej strony.
2. Krótki opis czynności, które należy wykonać na tej stronie.
3. Pola umożliwiające wprowadzanie poświadczeń platformy Azure po utworzeniu konta platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie konta Azure Batch](create-azure-account.md).
4. Tag obrazu platformy Docker dla zestawu narzędzi dźwiękowych.
5. Uruchom Azure Portal, aby zarządzać subskrypcjami, monitorować użycie i wyświetlać informacje dotyczące rozliczeń itp. 
6. Typ węzła obliczeniowego usługi Azure Batch, który ma być używany do obliczania. Typ węzła musi być obsługiwany przez lokalizację centrum danych platformy Azure. Jeśli nie masz pewności, pozostaw o godzinie **Standard_F8s_v2**.
7. Liczba węzłów do użycia w tym wyliczeniu. Wprowadzony tutaj numer ma wpływ na czas wykonywania tworzenie i jest ograniczony przez podstawową alokację Azure Batch. Alokacja domyślna zezwala tylko na dwa węzły z 8 rdzeniami lub węzeł podstawowy 1 16, ale można ją rozszerzyć. Aby uzyskać więcej informacji na temat ograniczeń dotyczących podstawowych alokacji, zobacz [Tworzenie konta Azure Batch](create-azure-account.md).
8. Zaznacz to pole wyboru, aby skonfigurować pulę obliczeń do używania [węzłów o niskim priorytecie](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Węzły obliczeniowe o niskim priorytecie mają znacznie niższy koszt, ale mogą być niezawsze dostępne lub mogą być przenoszone w dowolnym momencie.
9. Liczba sond dla sceny, obliczona na karcie **sondy** . Liczba sond określa liczbę symulacji, które muszą być uruchamiane w chmurze. Nie można określić większej liczby węzłów niż sondy.
10. Ilość czasu, który upłynął, aby zadanie zostało uruchomione w chmurze. Nie obejmuje to czasu uruchamiania węzła. Gdy zadanie zostanie uruchomione, jest to informacje o tym, jak długo powinna zostać pobrana z powrotem. Należy zauważyć, że jest to tylko oszacowanie.
11. Łączna ilość czasu obliczeniowego wymaganego do uruchomienia symulacji. Jest to łączny czas obliczeniowy węzła, który będzie używany na platformie Azure. Aby uzyskać więcej informacji na temat używania tej wartości, zobacz [szacunkowy koszt tworzenie](#Estimating-bake-cost) poniżej.
12. Ten komunikat informuje, gdzie wyniki tworzenie zostaną zapisane po zakończeniu zadania.
13. (Tylko użycie zaawansowane) Jeśli z jakiegoś powodu trzeba wymusić, aby środowisko Unity zapomnieć o przesłanym tworzenie (np. wyniki zostały pobrane przy użyciu innego komputera), kliknij przycisk **Wyczyść stan** , aby zapomnieć o przesłanym zadaniu. Należy zauważyć, że oznacza to, że plik wynikowy, gdy jest gotowy, **nie** zostanie pobrany i **nie jest taki sam, jak anulowanie zadania**. Zadanie, jeśli działa, będzie nadal działać w chmurze.
14. Kliknij przycisk **Tworzenie** , aby przesłać tworzenie do chmury. Gdy zadanie jest uruchomione, spowoduje to wyświetlenie **zadania anulowania** .
15. Przygotowuje się do przetwarzania [symulacji akustycznej na komputerze](#Local-bake).
16. Ten obszar pokazuje stan tworzenie. Po zakończeniu powinna być wyświetlana wartość **pobrane**.

W [Azure Portal](https://portal.azure.com)można zawsze uzyskać pełne informacje dotyczące aktywnych zadań, pul obliczeniowych i magazynu.

Gdy zadanie jest uruchomione, przycisk **Tworzenie** zmienia się na **Anuluj zadanie**. Ten przycisk służy do anulowania zadania w toku. Przed anulowaniem zadania zostanie wyświetlony monit o potwierdzenie. Anulowanie zadania nie może zostać cofnięte, żadne wyniki nie będą dostępne i nadal będzie naliczana opłata za dowolny użyty czas obliczeń platformy Azure.

Po rozpoczęciu tworzenie można zamknąć środowisko Unity. W zależności od projektu, typu węzła i liczby węzłów tworzenie w chmurze może potrwać kilka godzin. Stan zadania tworzenie zostanie zaktualizowany po ponownym załadowaniu projektu i otwarciu okna "akustyczne". Jeśli zadanie zostało ukończone, plik wyjściowy zostanie pobrany.

Poświadczenia platformy Azure są bezpiecznie przechowywane na komputerze lokalnym i powiązane z edytorem aparatu Unity. Są one używane wyłącznie w celu nawiązania bezpiecznego połączenia z platformą Azure.

### <a name="Estimating-bake-cost"></a>Szacowanie kosztu usługi Azure tworzenie

Aby oszacować, jakie dane tworzenie będzie kosztować, należy uzyskać wartość pokazaną dla **szacowanego kosztu obliczeniowego**, czyli czas trwania, i pomnożyć ją przez koszt godzinowy w lokalnej walucie wybranego **typu węzła maszyny wirtualnej** . Wynik nie będzie zawierać czasu węzła wymaganego do uruchomienia węzłów. Jeśli na przykład wybierzesz pozycję **Standard_F8s_v2** dla typu węzła, który ma koszt $0.40/hr, a szacowany koszt obliczeń wynosi 3 godziny i 57 minut, szacowany koszt uruchomienia zadania będzie $0,40 * ~ 4 godziny = ~ $1,60. Rzeczywisty koszt będzie prawdopodobnie nieco większy z powodu dodatkowego czasu na rozpoczęcie uruchamiania węzłów. Koszt węzła "co godzinę" można znaleźć na stronie [cennika Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (wybierz pozycję "obliczenia zoptymalizowane" lub "obliczenia o wysokiej wydajności" dla kategorii).

## <a name="Local-bake"></a>Tworzenie swoje sceny na komputerze
Swoją scenę można tworzenie na własnym komputerze. Może to być przydatne w przypadku eksperymentowania z niewielkimi scenami przed utworzeniem konta Azure Batch. Należy zauważyć, że symulacja akustyczna może zająć dużo czasu w zależności od rozmiaru sceny.

### <a name="minimum-hardware-requirements"></a>Minimalne wymagania sprzętowe
* Procesor x86-64 z co najmniej 8 rdzeniami i 32 GB pamięci RAM

Przykładowo w naszym testowaniu na maszynie 8 Core z technologią Intel Xeon E5-1660 @ 3 GHz i 32 GB pamięci RAM —
* Niewielka scena z 100 sondami może potrwać około 2 godzin przez bardzo duże Tworzenie lub 32 godz. Tworzenie.
* Sceny o średniej wielkości z sondami 1000 może potrwać około 20 godzin przez bardzo duże Tworzenie lub 21 dni w celu uzyskania dokładnego tworzenieu.

### <a name="setup-docker"></a>Skonfiguruj platformę Docker
Zainstaluj i skonfiguruj platformę Docker na komputerze, który będzie przetwarzać symulację —
1. Zainstaluj zestaw [narzędzi platformy Docker](https://www.docker.com/products/docker-desktop).
2. Uruchom ustawienia platformy Docker, przejdź do opcji Zaawansowane i skonfiguruj zasoby, aby mieć co najmniej 8 GB pamięci RAM. Im więcej procesorów CPU można przydzielić do platformy Docker, tym szybciej tworzenie zostanie ukończona. ![Zrzut ekranu przykładu ustawień platformy Docker](media/docker-settings.png)
3. Przejdź do "dyski udostępnione" i Włącz udostępnianie dysku używanego do przetwarzania.![Zrzut ekranu opcji udostępnionego dysku platformy Docker](media/docker-shared-drives.png)

### <a name="run-local-bake"></a>Uruchamianie lokalnego tworzenie
1. Kliknij przycisk "Przygotuj lokalny tworzenie" na karcie **Tworzenie** i wybierz folder, w którym zostaną zapisane pliki wejściowe i skrypty wykonywania. Następnie można uruchomić tworzenie na dowolnym komputerze, o ile spełnia minimalne wymagania sprzętowe i ma zainstalowany Aparat Docker, kopiując folder na tę maszynę.
2. Uruchom symulację przy użyciu skryptu "runlocalbake. bat". Ten skrypt pobierze obraz platformy Docker z projektorem, który jest niezbędny do przetwarzania symulacji i uruchamiania symulacji. 
3. Po zakończeniu symulacji Skopiuj powstały plik ACE z powrotem do projektu aparatu Unity. Aby upewnić się, że aparat Unity rozpoznaje ten element jako plik binarny, należy dołączyć ". b" do rozszerzenia pliku (na przykład "Scene1. asa. Bytes"). Szczegółowe dzienniki symulacji są przechowywane w "AcousticsLog. txt". Jeśli napotkasz jakiekolwiek problemy, Udostępnij ten plik, aby ułatwić diagnostykę.

## <a name="Data-Files"></a>Pliki danych dodane przez proces tworzenie

Podczas procesu tworzenie są tworzone cztery pliki danych. Jedna z nich zawiera wyniki symulacji i jest dostarczana z Twoim tytułem. Inne osoby przechowują dane dotyczące edytora Unity.

Wynik symulacji:
* **Majątek/AcousticsData/akustyczny\_[scena]. ACE. Bytes**: Jest to tabela odnośników środowiska uruchomieniowego i zawiera wyniki symulacji oraz voxelizede elementy sceny akustycznych. Lokalizację i nazwę tego pliku można zmienić przy użyciu pól na karcie sondy.

Pamiętaj, aby nie usuwać pliku wyników symulacji. Nie jest to możliwe do odzyskania, z wyjątkiem przypadków, gdy jest to konieczne.

Pliki danych edytora:
* **Zasoby/Edytor/[scenname]\_AcousticsParameters. Asset**: Ten plik przechowuje dane wprowadzane w polach w interfejsie użytkownika z dźwiękiem. Nie można zmienić lokalizacji i nazwy tego pliku.
* **Majątek/AcousticsData/redaktor/Acoustics_ [scena]. Vox**: Ten plik przechowuje geometrię voxelized akustycznych i właściwości materiału, które są obliczane przy użyciu przycisku **Oblicz...** na karcie sondy. Lokalizację i nazwę tego pliku można zmienić przy użyciu pól na karcie sondy.
* **Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml**: Ten plik przechowuje parametry symulacji obliczone przy użyciu przycisku **Oblicz...** na karcie sondy. Lokalizację i nazwę tego pliku można zmienić przy użyciu pól na karcie sondy.

## <a name="set-up-the-acoustics-lookup-table"></a>Konfigurowanie tabeli wyszukiwania akustycznego
Przeciągnij i upuść prefabość **projektu** z panelu projektu do sceny:

![Zrzut ekranu PREFAB akustycznych w środowisku Unity](media/acoustics-prefab.png)

Kliknij obiekt gry **ProjectAcoustics** i przejdź do panelu Inspektora. Określ lokalizację wyniku tworzenie (. Plik ACE, w obszarze **zasoby/AcousticsData**) przez przeciąganie i upuszczanie go do skryptu menedżera akustycznego lub kliknięcie przycisku okręgu obok pola tekstowego.

![Zrzut ekranu Menedżera akustycznego PREFAB w środowisku Unity](media/acoustics-manager.png)  

## <a name="next-steps"></a>Następne kroki
* Eksplorowanie [formantów projektowania dla aparatu Unity](unity-workflow.md)
* Eksploruj koncepcje projektu [dotyczące hałasu](design-process.md)

