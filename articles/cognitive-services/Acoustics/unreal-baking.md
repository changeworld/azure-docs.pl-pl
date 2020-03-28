---
title: Samouczek Akustyka projektu Unreal Bake
titlesuffix: Azure Cognitive Services
description: W tym dokumencie opisano proces przesyłania pieca akustyki przy użyciu rozszerzenia edytora Unreal.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 7a868a5f9b06499e23710399733b0659d97f900d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854901"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Samouczek Akustyka projektu Unreal Bake
W tym dokumencie opisano proces przesyłania pieca akustyki przy użyciu rozszerzenia edytora Unreal.

Istnieje pięć kroków do pieczenia:

1. Tworzenie lub oznaczanie siatki nawigacyjnej odtwarzacza
2. Geometria akustyki znaczników
3. Przypisywanie właściwości materiałów akustycznych do geometrii
4. Podgląd rozmieszczenia sondy
5. Piec

## <a name="open-the-project-acoustics-editor-mode"></a>Otwieranie trybu edytora Akustyka projektu

Zaimportuj pakiet wtyczki Project Acoustics do projektu. Aby uzyskać pomoc w tym zakresie, zobacz temat [Integracji nierealislnej.](unreal-integration.md) Po zintegrowaniu wtyczki otwórz interfejs akustyki, klikając nową ikonę trybu akustyki.

![Zrzut ekranu przedstawiający opcję Unreal Editor Acoustics Mode](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Tag aktorów dla akustyki

Karta obiekty jest pierwszą kartą, która jest wyświetlana po otwarciu trybu akustyki. Ta karta służy do oznaczania aktorów na swoim poziomie, co powoduje dodanie **tagów AcousticsGeometry** lub **AcousticsNavigation** do aktorów.

Zaznacz jeden lub więcej obiektów w konspekcie świata lub użyj sekcji **Zaznaczanie zbiorcze,** aby zaznaczyć wszystkie obiekty określonej kategorii. Po zaznaczeniu obiektów użyj sekcji **Oznaczanie,** aby zastosować żądany znacznik do zaznaczonych obiektów.

Jeśli coś nie ma ani **AcousticsGeometry** ani **AcousticsNavigation** tag, zostanie zignorowany w symulacji. Obsługiwane są tylko siatki statyczne, siatki nawigacyjne i krajobrazy. Jeśli oznaczysz cokolwiek innego, zostanie ono zignorowane.

### <a name="for-reference-the-objects-tab-parts"></a>Dla uzyskania informacji: Części karty Obiekty

![Zrzut ekranu przedstawiający kartę Obiekty akustyki w obszarze Nierealne](media/unreal-objects-tab-details.png)

1. Przyciski wyboru karty (Zaznaczona karta**Obiekty).** Użyj tych przycisków, aby przejść przez różne etapy robienia piec akustyki, od góry do dołu.
2. Krótki opis tego, co musisz zrobić za pomocą tej strony.
3. Dostępne selektory dla aktorów na poziomie.
4. Kliknięcie **przycisku Wybierz** spowoduje zaznaczenie wszystkich obiektów na poziomie, które pasują do co najmniej jednego z zaznaczonych typów aktorów.
5. Kliknięcie **przycisku Usuń zaznaczenie wszystkich** spowoduje wyczyszczenie bieżącego zaznaczenia. Jest to to samo, co naciśnięcie klawisza ucieczki.
6. Użyj tych przycisków radiowych, aby wybrać, czy znacznik Geometria lub Nawigacja ma być stosowany do wybranych podmiotów.
7. Kliknięcie **przycisku Znacznik** spowoduje dodanie wybranego tagu do wszystkich aktualnie wybranych podmiotów.
8. Kliknięcie przycisku **Usuń** znacznik spowoduje usunięcie wybranego tagu ze wszystkich aktualnie wybranych aktorów.
9. Kliknięcie **przycisku Wybierz oznakowane** spowoduje wyczyszczenie bieżącego zaznaczenia i wybranie wszystkich aktorów z aktualnie zaznaczonym tagiem.
10. Te statystyki pokazują, ilu aktorów jest oznaczonych za pomocą każdego typu tagu.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Tag akustyka okluzji i geometrii odbicia

Otwórz kartę Obiekty w oknie Akustyka. Oznacz wszystkie obiekty jako Geometria Akustyki, jeśli powinny one zakryć, odbić lub wchłonąć dźwięk. Geometria akustyki może obejmować takie rzeczy jak uziemie, ściany, dachy, okna & szyby okienne, dywany i duże meble. Można użyć dowolnego dowolnego poziomu złożoności dla tych obiektów. Ponieważ scena jest voxelized przed symulacją, bardzo szczegółowe siatki, takie jak drzewa z wielu małych liści, nie są bardziej kosztowne do pieczenia niż uproszczone obiekty.

Nie dołączaj rzeczy, które nie powinny mieć wpływu na akustykę, takich jak niewidoczne siatki kolizji.

Transformacja obiektu w czasie obliczania sondy (za pomocą zakładki Sondy poniżej) jest ustalana w wynikach pieczenia. Przesunięcie dowolnego z zaznaczonych obiektów w scenie będzie wymagało ponownego obliczenia sondy i przeczesywania sceny.

### <a name="create-or-tag-a-navigation-mesh"></a>Tworzenie lub oznaczanie siatki nawigacji

Siatka nawigacyjna służy do umieszczania punktów sondy do symulacji. Można użyć [woluminu nawigacyjnej](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html)siatki unreal lub można określić własną siatkę nawigacji. Należy oznaczyć co najmniej jeden obiekt jako **Akustyka .** Jeśli używasz siatki nawigacji Unreal, upewnij się, że masz ją najpierw skonkrecyjną.

### <a name="acoustics-volumes"></a>Akustyka Głośność ###

Istnieje również, zaawansowane dostosowanie można dokonać na obszarach nawigacji z **Akustyka Volumes**. **Akustyka Woluminy** to aktorzy, których można dodać do sceny, które umożliwiają wybranie obszarów do uwzględnienia i zignorowania z siatki nawigacji. Aktor udostępnia właściwość, która może być przełączana między "Include" i "Exclude". Woluminy "Include" zapewniają uwzględnienie tylko obszarów siatki nawigacji w nich, a woluminy "Wyklucz" oznaczają te obszary, które mają zostać zignorowane. Woluminy "Wyklucz" są zawsze stosowane po woluminach "Uwzględnij". Pamiętaj, aby **oznaczyć akustykę woluminów** jako **nawigację akustyka** przez zwykły proces na karcie Obiekty. Aktorzy ci ***nie*** są automatycznie oznaczani.

![Zrzut ekranu przedstawiający właściwości głośności akustyki w unreal](media/unreal-acoustics-volume-properties.png)

Woluminy "Wyklucz" mają na celu przede wszystkim zapewnienie precyzyjnej kontroli nad miejscem, w którym nie należy umieszczać sond do dokręcania wykorzystania zasobów.

![Zrzut ekranu przedstawiający wykluczeń głośności akustyki w unreal](media/unreal-acoustics-volume-exclude.png)

Woluminy "Uwzględnij" są przydatne do tworzenia ręcznych sekcji sceny, na przykład jeśli chcesz podzielić scenę na wiele stref akustycznych. Na przykład, jeśli masz dużą scenę, wiele kilometrów do kwadratu i masz dwa obszary zainteresowania, na których chcesz upiec akustykę. Możesz narysować dwa duże woluminy "Include" w scenie i utworzyć pliki ACE dla każdego z nich po jednym naraz. Następnie w grze można użyć woluminów wyzwalaczy w połączeniu z wezwaniami planu, aby załadować odpowiedni plik ACE, gdy gracz zbliży się do każdego kafelka.

**Akustyka Objętości** ograniczają tylko nawigację, a ***nie*** geometrię. Każda sonda wewnątrz **woluminu "Include" Acoustics volume** będzie nadal ciągnąć całą niezbędną geometrię poza głośnością podczas wykonywania symulacji fal. W związku z tym nie powinno być żadnych nieciągłości w okluzji lub innej akustyki wynikającej z przejścia gracza z jednego odcinka do drugiego.

## <a name="select-acoustic-materials"></a>Wybierz materiały akustyczne

Po oznaczeniu obiektów kliknij przycisk **Materiały,** aby przejść do karty Materiały. Ta karta będzie używana do określania właściwości materiału dla każdego materiału na poziomie. Zanim którykolwiek z aktorów zostanie oznaczony, będzie pusty.

Materiały akustyczne kontrolują ilość energii dźwiękowej odbijanej z każdej powierzchni. Domyślny materiał akustyczny ma absorpcję podobną do betonu. Project Acoustics sugeruje materiały na podstawie nazwy materiału sceny.

Czas pogłosu danego materiału w pomieszczeniu jest odwrotnie związany z jego współczynnikiem absorpcji, przy czym większość materiałów ma wartości absorpcyjne w zakresie od 0,01 do 0,20. Materiały o współczynnikach absorpcji powyżej tego zakresu są bardzo chłonne. Na przykład, jeśli pokój brzmi zbyt pogłosowo, zmień materiał akustyczny ścian, podłogi lub sufitu na coś o wyższej absorpcji. Przypisanie materiału akustycznego dotyczy wszystkich podmiotów, które używają tego materiału sceny.

![Wykres przedstawiający ujemną korelację czasu pogłosu ze współczynnikiem absorpcji](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Dla odniesienia: Części zakładki Materiały

![Zrzut ekranu przedstawiający kartę Obiekty akustyki w obszarze Nierealne](media/unreal-materials-tab-details.png)

1. Przycisk zakładki **Materiały,** używany do wyświetlenia tej strony.
2. Krótki opis tego, co musisz zrobić za pomocą tej strony.
3. Lista materiałów użytych w poziomie, zaczerpnięte z aktorów oznaczone jako **AcousticsGeometry**. Kliknięcie na materiał tutaj spowoduje zaznaczenie wszystkich obiektów w scenie, które używają tego materiału.
4. Pokazuje materiał akustyczny, do której przypisano materiał sceny. Kliknij rozwijaną, aby ponownie przypisać materiał sceny do innego materiału akustycznego.
5. Pokazuje współczynnik pochłaniania akustycznego materiału wybranego w poprzedniej kolumnie. Wartość zero oznacza doskonale refleksyjne (bez absorpcji), podczas gdy wartość 1 oznacza doskonale absorpcyjne (bez odbicia). Zmiana tej wartości spowoduje zaktualizowanie materiału akustyki (krok #4) na **Niestandardowy**.

Jeśli wniesiesz zmiany do materiałów w scenie, musisz przełączyć karty w wtyczce Akustyka projektu, aby zobaczyć te zmiany odzwierciedlone na karcie **Materiały.**

## <a name="calculate-and-review-listener-probe-locations"></a>Obliczanie i przeglądanie lokalizacji sondy odbiornika

Po przypisaniu materiałów przełącz się na kartę **Sondy.**

### <a name="for-reference-parts-of-the-probes-tab"></a>Dla odniesienia: Części karty Sondy

![Zrzut ekranu przedstawiający kartę Sondy akustyki w obszarze Nierealne](media/unreal-probes-tab-details.png)

1. Przycisk karty **Sondy** używany do wyświetlenia tej strony
2. Krótki opis tego, co musisz zrobić za pomocą tej strony
3. Użyj tej opcji, aby wybrać rozdzielczość symulacyjnej grubo lub drobną. Gruba jest szybsza, ale ma pewne kompromisy. Zobacz [Bake Rozdzielczość](bake-resolution.md) poniżej, aby uzyskać szczegółowe informacje.
4. Wybierz lokalizację, w której należy umieścić pliki danych akustyki za pomocą tego pola. Kliknij przycisk "..." , aby użyć selektora folderów. Aby uzyskać więcej informacji o plikach danych, zobacz [Pliki danych](#Data-Files) poniżej.
5. Pliki danych dla tej sceny zostaną nazwane przy użyciu prefiksu podanego tutaj. Wartość domyślna to "[Nazwa poziomu]_AcousticsData".
6. Kliknij przycisk **Oblicz,** aby voxelize sceny i obliczyć lokalizacje punktów sondy. Odbywa się to lokalnie na komputerze i musi być wykonane przed zrobieniem pieca. Po obliczeniu sondy powyższe formanty zostaną wyłączone, a ten przycisk zmieni się, aby powiedzieć **Wyczyść**. Kliknij przycisk **Wyczyść,** aby wymazać obliczenia i włączyć formanty, aby można było ponownie obliczyć przy użyciu nowych ustawień.

Sondy muszą być umieszczone w zautomatyzowanym procesie podanym w zakładce **Sondy.**


### <a name="what-the-calculate-button-calculates"></a>Co oblicza przycisk "Oblicz"

Przycisk **Oblicz** pobiera wszystkie podane do tej pory dane (geometria, nawigacja, materiały i ustawienie grubo-precyzyjne) i przechodzi przez kilka kroków:

1. Pobiera geometrię z siatki sceny i oblicza objętość woksala. Głośność woxel jest 3-wymiarową głośnością, która otacza całą scenę i składa się z małych sześciennych "voxels". Rozmiar voxels jest określany przez częstotliwość symulacji, która jest ustawiana przez ustawienie **Rozdzielczość symulacji.** Każdy voxel jest oznaczony jako "otwarte powietrze" lub zawierający geometrię sceny. Jeśli voxel zawiera geometrię, wówczas woxel jest oznaczony współczynnikiem absorpcji materiału przypisanego do tej geometrii.
2. Następnie wykorzystuje dane nawigacyjne do obliczania interesujących akustycznie miejsc, w których gracz może się udać. Stara się znaleźć dość mały zestaw tych miejsc, który obejmuje mniejsze obszary, takie jak drzwi i korytarze, a następnie do pomieszczeń, do otwartych przestrzeni. W przypadku małych scen jest to zazwyczaj mniej niż 100 lokalizacji, podczas gdy duże sceny mogą mieć do tysiąca.
3. Dla każdej z lokalizacji odbiornika końcowego oblicza, określa liczbę parametrów, takich jak jak "otwarte" jest przestrzeń, rozmiar pomieszczenia, w jakim znajduje się, itp.
4. Wyniki tych obliczeń są przechowywane w plikach w określonej lokalizacji (zobacz [pliki danych](#Data-Files) poniżej)

W zależności od rozmiaru sceny i prędkości maszyny obliczenia te mogą potrwać kilka minut.

Po zakończeniu tych obliczeń można wyświetlić podgląd zarówno danych voxel, jak i lokalizacji punktów sondy, aby upewnić się, że piec da dobre wyniki. Elementy takie jak zła siatka nawigacyjna lub brak/dodatkowa geometria będą zazwyczaj szybko widoczne w podglądzie, dzięki czemu można ją poprawić.


## <a name="debug-display"></a>Wyświetlanie debugowania

Po zakończeniu obliczania sondy, nowy aktor pojawi się w World Outliner nazwie **AcousticsDebugRenderer**. Zaznaczenie pól wyboru **Sondy renderowania** i **Renderowanie Voxels** spowoduje włączenie wyświetlania debugowania wewnątrz rzutni edytora.

![Zrzut ekranu przedstawiający aktora renderowania debugowania akustyki w edytorze nierealisty](media/acoustics-debug-renderer.png)

Jeśli na twoim poziomie nie są widoczne żadne woki lub sondy, upewnij się, że renderowanie w czasie rzeczywistym jest włączone w rzutni.

![Zrzut ekranu przedstawiający opcję renderowania w czasie rzeczywistym w](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels ( Voxels )

Woksele są wyświetlane w oknie sceny jako zielone kostki wokół geometrii uczestniczącej. Voxels, które zawierają tylko powietrze nie są wyświetlane. Wokół całej sceny znajduje się duże zielone pole, które oznacza pełną objętość woxel, która będzie używana w symulacji.
Poruszaj się po scenie i sprawdź, czy geometria akustycznie zaklętowa ma woki. Sprawdź również, czy obiekty nieakustyczne, takie jak siatki kolizji, nie zostały wyojawione. Kamera sceny musi znajdować się w odległości około 5 metrów od obiektu, aby woczniki się pokazać.

Jeśli porównasz woki utworzone z grubą rozdzielczością a drobną rozdzielczością, zobaczysz, że grube woki są dwa razy większe.

![Zrzut ekranu przedstawiający podgląd voxels akustyki w edytorze Unreal](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Punkty sondy

Punkty sondy są synonimem możliwych lokalizacji gracza (odbiornika). Podczas pieczenia symulacja oblicza akustykę łączącą wszystkie możliwe lokalizacje źródłowe z każdym punktem sondy. W czasie wykonywania lokalizacja odbiornika jest interpolowana między pobliskimi punktami sondy.

Ważne jest, aby sprawdzić, czy punkty sondy istnieją wszędzie tam, gdzie gracz ma podróżować w scenie. Punkty sondy są umieszczane w siatce nawigacji przez silnik Akustyka projektu i nie mogą być przenoszone ani edytowane, więc upewnij się, że siatka nawigacyjna obejmuje wszystkie możliwe lokalizacje odtwarzacza, sprawdzając punkty sondy.

![Zrzut ekranu przedstawiający podgląd sond akustyki w trybie Unreal](media/unreal-probes-preview.png)

Zobacz [Bake Resolution więcej](bake-resolution.md) szczegółów na temat grubej vs grzywny rozdzielczości.

## <a name="bake-your-level-using-azure-batch"></a>Upiecz swój poziom przy użyciu usługi Azure Batch

Możesz upiec scenę za pomocą klastra obliczeniowego w chmurze przy użyciu usługi Azure Batch. Wtyczka Project Acoustics Unreal łączy się bezpośrednio z usługą Azure Batch w celu tworzenia wystąpienia, zarządzania i niszczenia klastra usługi Azure Batch dla każdego wypieku. Na karcie Piec wprowadź poświadczenia platformy Azure, wybierz typ i rozmiar maszyny klastra, a następnie kliknij przycisk Piec.

### <a name="for-reference-parts-of-the-bake-tab"></a>W celach informacyjnych: Części zakładki pieczenia

![Zrzut ekranu przedstawiający kartę Piec akustyki w obszarze Unreal](media/unreal-bake-tab-details.png)

1. Przycisk Bake Tab używany do wyświetlenia tej strony.
2. Krótki opis tego, co zrobić na tej stronie.
3. Pola, aby wprowadzić poświadczenia platformy Azure po utworzeniu konta platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie konta wsadowego platformy Azure](create-azure-account.md).
4. Uruchom witrynę Azure portal, aby zarządzać subskrypcjami, monitorować użycie i wyświetlać informacje rozliczeniowe itp. 
5. Typ węzła obliczeniowego usługi Azure do użycia w obliczeniach. Typ węzła musi być obsługiwany przez lokalizację centrum danych platformy Azure. Jeśli nie wiesz, zostaw w **Standard_F8s_v2**.
6. Liczba węzłów do użycia w tym obliczeniu. Liczba wprowadzona w tym miejscu ma wpływ na czas ukończenia wypieku i jest ograniczona przez alokację rdzenia usługi Azure Batch. Alokacja domyślna umożliwia tylko dwa węzły 8 rdzeni lub jeden węzeł 16 rdzenia, ale można go rozwinąć. Aby uzyskać więcej informacji na temat podstawowych ograniczeń alokacji, zobacz [Tworzenie konta wsadowego platformy Azure](create-azure-account.md).
7. Zaznacz to pole wyboru, aby skonfigurować pulę obliczeń do [używania węzłów o niskim priorytecie](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Węzły obliczeniowe o niskim priorytecie mają znacznie niższy koszt, ale nie zawsze mogą być dostępne lub mogą być wywłaszczone w dowolnym momencie.
8. Ilość czasu, jaki powinien upłynąć, aby zadanie działało w chmurze. Nie obejmuje to czasu uruchamiania węzła. Po uruchomieniu zadania jest o tym, jak długo powinno być przed uzyskaniem wyników. Należy pamiętać, że jest to tylko oszacowanie.
9. Całkowity czas przetwarzania potrzebny do uruchomienia symulacji. Jest to całkowita ilość czasu obliczeniowego węzła, który będzie używany na platformie Azure. Zobacz [Szacowanie kosztu pieczenia](#Estimating-bake-cost) poniżej, aby uzyskać więcej informacji na temat korzystania z tej wartości.
10. Kliknij przycisk Piec, aby przesłać piec do chmury. Gdy zadanie jest uruchomione, zamiast tego zostanie **wyświetle anulowanie zadania.** Jeśli na tej karcie występują błędy lub przepływ pracy na karcie Sondy nie został **ukończony,** ten przycisk zostanie wyłączony.
11. Liczba sondy dla sceny obliczona na karcie **Sondy.** Liczba sond określa liczbę symulacji, które muszą być uruchamiane w chmurze. Nie można określić więcej węzłów niż istnieją sondy.
12. Ten komunikat informuje o bieżącym stanie zadania lub jeśli są jakieś błędy na tej karcie, jakie są te błędy.

Zawsze możesz uzyskać pełne informacje o aktywnych zadaniach, pulach obliczeniowych i magazynie w [witrynie Azure portal.](https://portal.azure.com)

Gdy zadanie jest **uruchomione,** przycisk Piec zmienia się na **Anuluj zadanie**. Użyj tego przycisku, aby anulować zadanie w toku. Anulowania zadania nie można cofnąć, żadne wyniki nie będą dostępne, a opłata będzie nadal naliczana za dowolny czas użycia na platformie Azure przed anulowaniem.

Po rozpoczęciu pieca, można zamknąć Unreal. W zależności od projektu, typu węzła i liczby węzłów upieczenie chmury może potrwać kilka godzin. Stan zadania pieczenia zostanie zaktualizowany po ponownym załadowaniu projektu i otwarciu okna Akustyka. Jeśli zadanie zostało ukończone, plik wyjściowy zostanie pobrany.

Poświadczenia platformy Azure są bezpiecznie przechowywane na komputerze lokalnym i skojarzone z projektem Unreal. Są one używane wyłącznie do ustanawiania bezpiecznego połączenia z platformą Azure.

### <a name="estimating-azure-bake-cost"></a><a name="Estimating-bake-cost"></a>Szacowanie kosztów pieczenia platformy Azure

Aby oszacować, ile będzie kosztować dany wypiek, należy wziąć wartość wyświetlaną dla **szacowanego kosztu obliczeniowego**, która jest czasem trwania, i pomnóż ją przez koszt godzinowy w walucie lokalnej wybranego **typu węzła maszyny Wirtualnej.** Wynik nie będzie zawierać czas węzła potrzebne do uzyskania węzłów i uruchomiony. Na przykład jeśli wybierzesz **Standard_F8s_v2** dla typu węzła, który ma koszt $0.40/h, a szacowany koszt obliczeń wynosi 3 godziny i 57 minut, szacowany koszt uruchomienia zadania wyniesie $0.40 * ~4 godziny = ~$1.60. Rzeczywisty koszt będzie prawdopodobnie nieco wyższy ze względu na dodatkowy czas, aby rozpocząć węzły. Koszt węzła godzinowego można znaleźć na stronie [Cennik wsadowy platformy Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (wybierz "Obliczenia zoptymalizowane" lub "Obliczenia o wysokiej wydajności" dla kategorii).

### <a name="reviewing-the-bake-results"></a>Przeglądanie wyników wypieku

Po zakończeniu pieczenia sprawdź, czy punkty voxels i probe znajdują się w oczekiwanych lokalizacjach, uruchamiając wtyczkę środowiska uruchomieniowego.

## <a name="data-files"></a><a name="Data-Files"></a>Pliki danych

Istnieją cztery pliki danych utworzone przez tę wtyczkę w różnych punktach. Tylko jeden z nich jest potrzebny w czasie wykonywania i jest umieszczany w folderze Zawartość/Akustyka projektu, który jest automatycznie dodawany do ścieżki pakowania projektu. Pozostałe trzy znajdują się wewnątrz folderu Dane akustyki i nie są pakowane.

* **[Project]/Config/ProjectAcoustics.cfg**: Ten plik przechowuje dane wprowadzane w polach w interfejsie użytkownika trybu akustyki. Lokalizacji i nazwy tego pliku nie można zmienić. Istnieją inne wartości przechowywane w tym pliku, które wpływają na piec, ale są one dla zaawansowanych użytkowników i nie powinny być zmieniane.
* **[Project]/Content/Acoustics/[LevelName]\_AcousticsData.ace**: Ten plik jest tworzony podczas symulacji pieczenia i zawiera dane wyszukiwania używane przez środowisko wykonawcze do renderowania akustyki sceny. Lokalizację i nazwę tego pliku można zmienić za pomocą pól na karcie **Sondy.** Jeśli chcesz zmienić nazwę tego pliku po jego utworzeniu, usuń zestaw UAsset z projektu Unreal, zmień nazwę pliku poza Unreal w Eksploratorze plików, a następnie ponownie zaimportuj ten plik do Unreal, aby utworzyć nowy zestaw UA. Zmiana nazwy zestawu UAsset sama w sobie nie będzie działać.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData.vox**: Ten plik przechowuje geometrię akustyki voxelized i właściwości materiału. Obliczany za pomocą przycisku **Oblicz** na karcie **Sondy.** Lokalizację i nazwę tego pliku można zmienić za pomocą pól na karcie **Sondy.**
* **[Projekt]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData\_config.xml**: Ten plik przechowuje parametry obliczone za pomocą przycisku **Oblicz** na karcie **Sondy.** Lokalizację i nazwę tego pliku można zmienić za pomocą pól na karcie **Sondy.**

Należy uważać, aby nie usunąć pliku *.ace pobranego z platformy Azure. Ten plik nie można odzyskać, z wyjątkiem ponownego wypiekania sceny.

## <a name="next-steps"></a>Następne kroki
* Poznaj [kontrolki projektu dla Unreal](unreal-workflow.md)
* Poznaj [koncepcje projektowe akustyka projektu](design-process.md)

