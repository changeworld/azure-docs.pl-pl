---
title: Samouczek dotyczący akustycznych projektów Unreal tworzenie
titlesuffix: Azure Cognitive Services
description: W tym dokumencie opisano proces przesyłania tworzenie akustycznych przy użyciu rozszerzenia edytora Unreal.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: michem
ROBOTS: NOINDEX
ms.openlocfilehash: 47946570db305ff3d54dfed9ea6f698e5deb7b72
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704785"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Samouczek dotyczący akustycznych projektów Unreal tworzenie
W tym dokumencie opisano proces przesyłania tworzenie akustycznych przy użyciu rozszerzenia edytora Unreal.

Istnieje pięć kroków do wykonania tworzenie:

1. Utwórz lub Oznacz siatkę nawigacji odtwarzacza
2. Geometria znaczników akustycznych
3. Przypisywanie właściwości materiałów akustycznych do geometrii
4. Podgląd rozmieszczenia sondy
5. Tworzenie

## <a name="open-the-project-acoustics-editor-mode"></a>Otwórz tryb edytora akustycznego projektu

Zaimportuj pakiet wtyczki dla hałasu projektu do projektu. Aby uzyskać pomoc dotyczącą tego, zobacz temat integracja z usługą [Unreal](unreal-integration.md) . Po zintegrowaniu wtyczki Otwórz interfejs użytkownika, klikając ikonę Nowy tryb akustyczny.

![Zrzut ekranu opcji Tryb akustyczny edytora Unreal](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Otaguj aktorów pod kątem hałasu

Karta obiekty to pierwsza karta, która jest wyświetlana po otwarciu trybu akustycznego. Ta karta służy do oznaczania aktorów na poziomie, który dodaje do aktorów Tagi **AcousticsGeometry** lub **AcousticsNavigation** .

Wybierz co najmniej jeden obiekt w świecie światowym lub Skorzystaj z sekcji **wybór zbiorczy** , aby wybrać wszystkie obiekty określonej kategorii. Po wybraniu obiektów Użyj sekcji **tagowania** , aby zastosować żądany tag do wybranych obiektów.

Jeśli coś nie ma znacznika **AcousticsGeometry** ani **AcousticsNavigation** , zostanie zignorowane w symulacji. Obsługiwane są tylko statyczne siatki, siatki nawigacji i Landscapes. Jeśli oznaczesz coś innego, zostanie on zignorowany.

### <a name="for-reference-the-objects-tab-parts"></a>Aby uzyskać odwołanie: Części karty obiekty

![Zrzut ekranu karty akustycznych obiektów w Unreal](media/unreal-objects-tab-details.png)

1. Przyciski wyboru karty (wybrana karta**obiekty** ). Użyj tych przycisków, aby zapoznać się z różnymi krokami wykonywania operacji akustycznych tworzenie, od góry do dołu.
2. Krótki opis tego, co należy zrobić, korzystając z tej strony.
3. Dostępne selektory dla aktorów na poziomie.
4. Kliknięcie pozycji **Wybierz** spowoduje zaznaczenie wszystkich obiektów na poziomie, które pasują do co najmniej jednego z wybranych typów aktorów.
5. Kliknięcie przycisku Usuń **Zaznacz wszystko** spowoduje wyczyszczenie bieżącego zaznaczenia. Jest to takie samo, jak naciśnięcie klawisza Escape.
6. Użyj tych przycisków radiowych, aby określić, czy do wybranych aktorów ma być stosowany tag geometryczny, czy też nawigacyjny.
7. Kliknięcie **tagu** spowoduje dodanie wybranego tagu do wszystkich aktualnie wybranych aktorów.
8. Kliknięcie **UNTAG** spowoduje usunięcie wybranego tagu z wszystkich aktualnie wybranych aktorów.
9. Kliknięcie pozycji **Wybierz oznaczone** spowoduje wyczyszczenie bieżącego zaznaczenia i zaznaczenie wszystkich aktorów z aktualnie wybranym tagiem.
10. Te statystyki pokazują, ile aktorów jest oznakowanych przez każdy typ znacznika.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Oznacz akustyczne zamknięcia i geometrii odbicia

Otwórz kartę obiekty w oknie akustyczny. Oznacz wszystkie obiekty jako geometryczne, jeśli powinny occlude, odzwierciedlić lub wchłonąć dźwięk. Geometria akustyczna może obejmować takie rzeczy, jak Ziemia, ściany, dachy, szyby okna & Windows, Rugs i duże meble. Można użyć dowolnego poziomu złożoności dla tych obiektów. Ponieważ scena jest voxelized przed symulacją, wysoce szczegółowe siatki, takie jak drzewa z wieloma małymi liśćmi, nie są droższe do tworzenie niż uproszczone obiekty.

Nie należy uwzględniać elementów, które nie powinny mieć wpływu na akustyczne, takich jak siatki kolizji niewidocznych.

Przekształcanie obiektu w czasie obliczania sondy (za pomocą karty sondy poniżej) jest ustalone w wynikach tworzenie. Przeniesienie dowolnego oznaczonego obiektu w scenie wymaga ponownego wykonania obliczeń sondy i odprowadzenia sceny.

### <a name="create-or-tag-a-navigation-mesh"></a>Utwórz lub Oznacz siatkę nawigacji

Siatka nawigacji służy do umieszczania punktów sondy na potrzeby symulacji. Możesz użyć [woluminu związanego z siatką](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html)nawigacyjną Unreal lub określić własną siatkę nawigacji. Należy oznaczyć co najmniej jeden obiekt jako **nawigację akustyczną**. Jeśli używasz siatki nawigacji w programie Unreal, upewnij się, że został on skompilowany jako pierwszy.

### <a name="acoustics-volumes"></a>Woluminy akustyczne ###

Dodatkowo zaawansowane dostosowywanie można wykonać w obszarach nawigacyjnych, używając **woluminów akustycznych**. **Woluminy akustyczne** są aktorami, które można dodać do swojej sceny, dzięki czemu można wybrać obszary do dołączenia i zignorowania z siatki nawigacji. Aktor ujawnia właściwość, którą można przełączać między "include" i "exclude". Woluminy "include" zapewniają, że tylko obszary siatki nawigacyjnej wewnątrz nich są brane pod uwagę, a woluminy "exclude" oznaczają te obszary, które mają zostać pominięte. Woluminy "exclude" są zawsze stosowane po woluminach "include". Upewnij się, że znaczniki **głośności** są oznaczane jako **akustyczne** przez zwykły proces na karcie obiekty. Te uczestników ***nie*** są otagowane automatycznie.

![Zrzut ekranu przedstawiający właściwości woluminu akustycznego w Unreal](media/unreal-acoustics-volume-properties.png)

Woluminy "exclude" są głównie przeznaczone do zapewnienia precyzyjnej kontroli nad tym, gdzie nie należy umieszczać sond do zwiększania użycia zasobów.

![Zrzut ekranu przedstawiający wykluczenie woluminu akustycznego w Unreal](media/unreal-acoustics-volume-exclude.png)

Woluminy "include" są przydatne do tworzenia ręcznych sekcji sceny, takich jak Jeśli chcesz podzielić swoją scenę na wiele stref akustycznych. Na przykład jeśli masz dużą scenę, w kwadracie wiele kilometrów i masz dwa obszary zainteresowań, na których chcesz tworzenieć wartości akustyczne. Można rysować dwa duże woluminy "include" w scenie i generować pliki ACE dla każdej z nich naraz. Następnie w grze można użyć woluminów wyzwalanych połączonych z wywołaniami strategii w celu załadowania odpowiedniego pliku ACE, gdy gracz zbliża się do każdego kafelka.

**Woluminy akustyczne** ograniczają jedynie nawigację, a ***nie*** geometrię. Każda sonda w woluminie " dołączania" ma nadal pociągnąć za sobą wszystkie niezbędne geometrie poza woluminem podczas wykonywania symulacji Wave. W związku z tym nie powinno być żadnych ciągłości w zamknięcia ani inne akustyczne, które wynikają z przekroczenia przez odtwarzacz z jednej sekcji do innej.

## <a name="select-acoustic-materials"></a>Wybierz materiały akustyczne

Po oznakowaniu obiektów kliknij przycisk **materiały** , aby przejść do karty materiały. Ta karta zostanie użyta do określenia właściwości materiału dla każdego materiału na poziomie. Zanim wszystkie aktory zostaną otagowane, będą puste.

Materiały akustyczne kontrolują ilość energii dźwiękowej odzwierciedloną z tyłu z każdej powierzchni. Domyślny materiał akustyczny ma absorpcję podobną do konkretnych. Akustyczne projektu sugeruje materiały na podstawie nazwy materiału sceny.

Czas reverberation danego materiału w pokoju jest odwrotnie związany ze współczynnikiem absorpcji, z większością materiałów mających wartości absorpcji z zakresu od 0,01 do 0,20. Materiały z współczynnikiem absorpcji powyżej tego zakresu są bardzo pochłaniane. Na przykład, Jeśli pozostało zbyt reverberant, Zmień materiał akustyczny ścian, podłogi lub sufitu na coś o wyższej absorptivity. Przypisanie materiału akustycznego ma zastosowanie do wszystkich aktorów korzystających z tego materiału sceny.

![Wykres przedstawiający ujemną korelację czasu reverberation z współczynnikiem absorpcji](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Aby uzyskać odwołanie: Części karty materiały

![Zrzut ekranu karty akustycznych obiektów w Unreal](media/unreal-materials-tab-details.png)

1. Przycisk karta **materiały** służący do wypełniania tej strony.
2. Krótki opis tego, co należy zrobić, korzystając z tej strony.
3. Lista materiałów użytych na poziomie, pobranych z aktorów oznaczonych jako **AcousticsGeometry**. Kliknięcie materiału spowoduje wybranie wszystkich obiektów w scenie, które używają tego materiału.
4. Pokazuje materiał akustyczny, do którego przypisano materiał sceny. Kliknij listę rozwijaną, aby ponownie przypisać materiał sceny do innego materiału akustycznego.
5. Pokazuje współczynnik absorpcji akustycznej materiału zaznaczonego w poprzedniej kolumnie. Wartość zerowa oznacza idealnie odbijającą (bez absorpcji), natomiast wartość 1 oznacza doskonale absorptive (bez odbicia). Zmiana tej wartości spowoduje zaktualizowanie materiału akustycznego (krok #4) do **niestandardowego**.

Jeśli wprowadzisz zmiany w materiałach w scenie, musisz przełączyć karty w wtyczki akustycznej projektu, aby zobaczyć te zmiany odzwierciedlone na karcie **materiały** .

## <a name="calculate-and-review-listener-probe-locations"></a>Obliczanie i przeglądanie lokalizacji sondy odbiornika

Po przypisaniu materiałów przejdź do karty **sondy** .

### <a name="for-reference-parts-of-the-probes-tab"></a>Aby uzyskać odwołanie: Części karty sondy

![Zrzut ekranu karty sondy akustyczne w Unreal](media/unreal-probes-tab-details.png)

1. Przycisk karta sondy służący do wypełniania tej strony
2. Krótki opis czynności, które należy wykonać przy użyciu tej strony
3. Użyj tej opcji, aby wybrać duże lub cienkie rozwiązanie symulacji. Duże jest szybsze, ale ma pewne kompromisy. Aby uzyskać szczegółowe informacje, zobacz [rozwiązanie tworzenie](bake-resolution.md) .
4. Wybierz lokalizację, w której mają być umieszczone pliki danych akustycznych przy użyciu tego pola. Kliknij przycisk z "..." Aby użyć selektora folderów. Aby uzyskać więcej informacji na temat plików danych, zobacz [pliki danych](#Data-Files) poniżej.
5. Pliki danych dla tej sceny będą nazwane przy użyciu prefiksu podanego w tym miejscu. Wartość domyślna to "[nazwa poziomu] _AcousticsData".
6. Kliknij przycisk **Oblicz** , aby voxelize scenę i obliczyć lokalizacje punktów sondowania. Ta czynność jest wykonywana lokalnie na maszynie i musi zostać wykonana przed wykonaniem tworzenie. Po obliczeniu sond, powyższe kontrolki zostaną wyłączone i ten przycisk zmieni się na " **Wyczyść**". Kliknij przycisk **Wyczyść** , aby wymazać obliczenia i włączyć kontrolki, aby można było ponownie obliczyć przy użyciu nowych ustawień.

Sondy muszą być umieszczone w zautomatyzowanym procesie podanym na karcie sondy.


### <a name="what-the-calculate-button-calculates"></a>Co oblicza przycisk "Oblicz"

Przycisk **Oblicz** pobiera wszystkie przekazane do tej pory dane (Geometria, nawigacja, materiały i ustawienie bardzo duże/precyzyjne) i przechodzi przez kilka kroków:

1. Staje się geometrią z siatki sceny i oblicza wolumin Voxel. Wolumin Voxel to trójwymiarowy wolumin, który obejmuje całą scenę i składa się z małych sześciennych "voxels". Rozmiar voxels jest określany przez częstotliwość symulacji ustawioną przez ustawienie **rozdzielczości symulacji** . Każdy Voxel jest oznaczony jako "Open Air" lub zawierający geometrię sceny. Jeśli Voxel zawiera geometrię, Voxel jest oznakowany współczynnik absorpcji materiału przypisanego do tej geometrii.
2. Następnie używa danych nawigacyjnych do obliczenia interesujących się lokalizacji, w których gracz może przejść. Próbuje znaleźć dostatecznie mały zbiór tych lokalizacji, który obejmuje mniejsze obszary, takie jak Doorways i korytarzach, a następnie do pokojów, do otwartych miejsc. W przypadku małych scen zwykle jest to mniej niż 100 lokalizacji, podczas gdy duże sceny mogą mieć do 1000.
3. Dla każdej końcowej lokalizacji odbiornika, która jest obliczana, określa kilka parametrów, takich jak "Otwórz" jest miejscem, rozmiarem miejsca, w którym znajduje się, itp.
4. Wyniki tych obliczeń są przechowywane w plikach w określonej lokalizacji (zobacz [pliki danych](#Data-Files) poniżej)

W zależności od rozmiaru sceny i szybkości działania tych obliczeń może potrwać kilka minut.

Po ukończeniu tych obliczeń można wyświetlić podgląd danych Voxel i lokalizacji punktów sondowania, aby zapewnić, że tworzenie da dobre wyniki. Elementy, takie jak zła siatka nawigacji lub brakująca lub dodatkowa geometria, są zwykle szybko widoczne w wersji zapoznawczej, aby można było ją poprawić.


## <a name="debug-display"></a>Debugowanie ekranu

Po zakończeniu obliczania sondy zostanie wyświetlony nowy aktor w świecie światowym o nazwie **AcousticsDebugRenderer**. Zaznaczenie pól wyboru **sondy renderowania** i **Renderuj Voxels** spowoduje włączenie wyświetlania debugowania wewnątrz okienka ekranu edytora.

![Zrzut ekranu przedstawiający aktora Debuguj debugowanie debugowania w edytorze Unreal](media/acoustics-debug-renderer.png)

Jeśli nie widzisz żadnych voxels lub sond na poziomie, upewnij się, że w okienku ekranu jest włączona funkcja renderowania w czasie rzeczywistym.

![Zrzut ekranu opcji renderowania w czasie rzeczywistym w Unreal](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels

Voxels są wyświetlane w oknie sceny jako zielone moduły wokół uczestniczącej geometrii. Voxels, które zawierają tylko powietrze, nie są wyświetlane. Istnieje duże zielony prostokąt wokół całej sceny, który oznacza kompletny wolumin Voxel, który będzie używany w symulacji.
Poruszaj się wokół sceny i sprawdzaj, czy geometria akustyczna occluding ma voxels. Sprawdź również, czy obiekty nieakustyczne, takie jak siatki kolizji, nie zostały voxelized. Aparat sceny musi znajdować się w przedziale około 5 metrów obiektu dla voxels do wyświetlenia.

W przypadku porównania voxels utworzonego za pomocą grubej rozdzielczości i rozdzielczości, zobaczysz, że Gruby voxels są dwa razy większe.

![Zrzut ekranu przedstawiający voxelsy w wersji zapoznawczej w edytorze Unreal](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Punkty sond

Punkty sondy są równoznaczne z możliwymi lokalizacjami odtwarzacza (odbiornika). W przypadku pieczenia symulacja oblicza wartość akustyczną łączącą wszystkie możliwe lokalizacje źródłowe z każdym punktem sondowania. W czasie wykonywania lokalizacja odbiornika jest interpolowana między pobliżu punktów sondy.

Ważne jest, aby sprawdzić, czy punkty sond istnieją wszędzie tam, gdzie gracz oczekuje na podróż w scenie. Punkty sondy są umieszczane w siatce nawigacji przez aparat akustyczny projektu i nie mogą być przenoszone ani edytowane, dlatego należy upewnić się, że siatka nawigacji obejmuje wszystkie możliwe lokalizacje odtwarzacza przez sprawdzenie punktów sondowania.

![Zrzut ekranu przedstawiający sondy akustyczne w wersji zapoznawczej w Unreal](media/unreal-probes-preview.png)

Zobacz [rozwiązanie tworzenie](bake-resolution.md) , aby uzyskać więcej informacji na temat nieograniczonej rozdzielczości programu vs.

## <a name="bake-your-level-using-azure-batch"></a>Tworzenie swój poziom przy użyciu Azure Batch

Swoją scenę można tworzenie z klastrem obliczeniowym w chmurze przy użyciu usługi Azure Batch. Wtyczka Unreal akustyczna projektu łączy się bezpośrednio z Azure Batch, aby utworzyć wystąpienie klastra Azure Batch, zarządzać nim i wyłączać go dla każdego tworzenieu. Na karcie Tworzenie wprowadź swoje poświadczenia platformy Azure, wybierz typ i rozmiar maszyny klastra, a następnie kliknij przycisk Tworzenie.

### <a name="for-reference-parts-of-the-bake-tab"></a>Aby uzyskać odwołanie: Części karty Tworzenie

![Zrzut ekranu karty Tworzenie akustyczne w Unreal](media/unreal-bake-tab-details.png)

1. Przycisk karty Tworzenie używany do wyłączenia tej strony.
2. Krótki opis czynności, które należy wykonać na tej stronie.
3. Pola umożliwiające wprowadzanie poświadczeń platformy Azure po utworzeniu konta platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie konta Azure Batch](create-azure-account.md).
4. Uruchom Azure Portal, aby zarządzać subskrypcjami, monitorować użycie i wyświetlać informacje dotyczące rozliczeń itp. 
5. Typ węzła obliczeniowego usługi Azure Batch, który ma być używany do obliczania. Typ węzła musi być obsługiwany przez lokalizację centrum danych platformy Azure. Jeśli nie masz pewności, pozostaw o godzinie **Standard_F8s_v2**.
6. Liczba węzłów do użycia w tym wyliczeniu. Wprowadzony tutaj numer ma wpływ na czas wykonywania tworzenie i jest ograniczony przez podstawową alokację Azure Batch. Alokacja domyślna zezwala tylko na dwa węzły z 8 rdzeniami lub węzeł podstawowy 1 16, ale można ją rozszerzyć. Aby uzyskać więcej informacji na temat ograniczeń dotyczących podstawowych alokacji, zobacz [Tworzenie konta Azure Batch](create-azure-account.md).
7. Zaznacz to pole wyboru, aby skonfigurować pulę obliczeń do używania [węzłów o niskim priorytecie](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Węzły obliczeniowe o niskim priorytecie mają znacznie niższy koszt, ale mogą być niezawsze dostępne lub mogą być przenoszone w dowolnym momencie.
8. Ilość czasu, który upłynął, aby zadanie zostało uruchomione w chmurze. Nie obejmuje to czasu uruchamiania węzła. Gdy zadanie zostanie uruchomione, jest to informacje o tym, jak długo powinna zostać pobrana z powrotem. Należy zauważyć, że jest to tylko oszacowanie.
9. Łączna ilość czasu obliczeniowego wymaganego do uruchomienia symulacji. Jest to łączny czas obliczeniowy węzła, który będzie używany na platformie Azure. Aby uzyskać więcej informacji na temat używania tej wartości, zobacz [szacunkowy koszt tworzenie](#Estimating-bake-cost) poniżej.
10. Kliknij przycisk Tworzenie, aby przesłać tworzenie do chmury. Gdy zadanie jest uruchomione, spowoduje to wyświetlenie **zadania anulowania** . Jeśli na tej karcie występują błędy lub przepływ pracy na karcie sondy nie został ukończony, ten przycisk zostanie wyłączony.
11. Liczba sond dla sceny, obliczona na karcie **sondy** . Liczba sond określa liczbę symulacji, które muszą być uruchamiane w chmurze. Nie można określić większej liczby węzłów niż sondy.
12. Ten komunikat informuje o bieżącym stanie zadania lub o wystąpieniu błędów na tej karcie, czego dotyczą te błędy.

W [Azure Portal](https://portal.azure.com)można zawsze uzyskać pełne informacje dotyczące aktywnych zadań, pul obliczeniowych i magazynu.

Gdy zadanie jest uruchomione, przycisk **Tworzenie** zmienia się na **Anuluj zadanie**. Ten przycisk służy do anulowania zadania w toku. Anulowanie zadania nie może zostać cofnięte, żadne wyniki nie będą dostępne i nadal będą naliczane opłaty za dowolny czas obliczeń platformy Azure używany przed anulowaniem.

Po rozpoczęciu tworzenie można zamknąć Unreal. W zależności od projektu, typu węzła i liczby węzłów tworzenie w chmurze może potrwać kilka godzin. Stan zadania tworzenie zostanie zaktualizowany po ponownym załadowaniu projektu i otwarciu okna "akustyczne". Jeśli zadanie zostało ukończone, plik wyjściowy zostanie pobrany.

Poświadczenia platformy Azure są bezpiecznie przechowywane na komputerze lokalnym i skojarzone z projektem Unreal. Są one używane wyłącznie w celu nawiązania bezpiecznego połączenia z platformą Azure.

### <a name="Estimating-bake-cost"></a>Szacowanie kosztu usługi Azure tworzenie

Aby oszacować, jakie dane tworzenie będzie kosztować, należy uzyskać wartość pokazaną dla **szacowanego kosztu obliczeniowego**, czyli czas trwania, i pomnożyć ją przez koszt godzinowy w lokalnej walucie wybranego **typu węzła maszyny wirtualnej** . Wynik nie będzie zawierać czasu węzła wymaganego do uruchomienia węzłów. Jeśli na przykład wybierzesz pozycję **Standard_F8s_v2** dla typu węzła, który ma koszt $0.40/hr, a szacowany koszt obliczeń wynosi 3 godziny i 57 minut, szacowany koszt uruchomienia zadania będzie $0,40 * ~ 4 godziny = ~ $1,60. Rzeczywisty koszt będzie prawdopodobnie nieco większy z powodu dodatkowego czasu na rozpoczęcie uruchamiania węzłów. Koszt węzła "co godzinę" można znaleźć na stronie [cennika Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (wybierz pozycję "obliczenia zoptymalizowane" lub "obliczenia o wysokiej wydajności" dla kategorii).

### <a name="reviewing-the-bake-results"></a>Przeglądanie wyników tworzenie

Po zakończeniu tworzenie Sprawdź, czy voxels i punkty sondy znajdują się w oczekiwanych lokalizacjach, uruchamiając wtyczkę środowiska uruchomieniowego.

## <a name="Data-Files"></a>Pliki danych

Ta wtyczka zawiera cztery pliki danych utworzone w różnych punktach. Tylko jeden z nich jest wymagany w czasie wykonywania i znajduje się w folderze Content/akustycznym projektu, który jest automatycznie dodawany do ścieżki pakowania projektu. Pozostałe trzy znajdują się w folderze Dane akustyczne i nie są spakowane.

* **[Projekt]/config/ProjectAcoustics.cfg**: Ten plik przechowuje dane wprowadzane w polach w interfejsie użytkownika trybu akustycznego. Nie można zmienić lokalizacji i nazwy tego pliku. W tym pliku znajdują się inne wartości, które mają wpływ na tworzenie, ale są one przeznaczone dla zaawansowanych użytkowników i nie powinny być zmieniane.
* **[Project]/Content/Acoustics/[LevelName]\_AcousticsData.ace**: Ten plik jest tworzony podczas symulacji tworzenie i zawiera dane wyszukiwania używane przez środowisko uruchomieniowe do renderowania wartości akustycznych sceny. Lokalizację i nazwę tego pliku można zmienić przy użyciu pól na karcie sondy. Jeśli chcesz zmienić nazwę tego pliku po jego utworzeniu, Usuń UAsset z projektu Unreal, Zmień nazwę pliku poza Unreal w Eksploratorze plików, a następnie ponownie zaimportuj ten plik do Unreal, aby utworzyć nowy UAsset. Zmiana nazwy UAsset przez siebie nie będzie zadziałała.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData.vox**: Ten plik przechowuje geometrię voxelized akustycznych i właściwości materiału. Obliczane przy użyciu przycisku **Oblicz** na karcie **sondy** . Lokalizację i nazwę tego pliku można zmienić przy użyciu pól na karcie sondy.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData\_config.xml**: Ten plik przechowuje parametry obliczane przy użyciu przycisku **Oblicz** na karcie **sondy** . Lokalizację i nazwę tego pliku można zmienić przy użyciu pól na karcie sondy.

Pamiętaj, aby nie usuwać pliku *. asa pobranego z platformy Azure. Ten plik nie jest możliwy do odzyskania, z wyjątkiem tego, że jest to możliwe.

## <a name="next-steps"></a>Następne kroki
* Eksplorowanie [formantów projektu dla Unreal](unreal-workflow.md)
* Eksploruj koncepcje projektu [dotyczące hałasu](design-process.md)

