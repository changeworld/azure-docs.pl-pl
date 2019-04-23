---
title: Samouczek Unreal tworzenie Akustyka projektu
titlesuffix: Azure Cognitive Services
description: W tym dokumencie opisano proces przesyłania tworzenie Akustyka przy użyciu rozszerzenia edytora unreal Engine.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: michem
ms.openlocfilehash: 48a1c4350b438761aa2e2d8c7e57a872c86ca292
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797179"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Samouczek Unreal tworzenie Akustyka projektu
W tym dokumencie opisano proces przesyłania tworzenie Akustyka przy użyciu rozszerzenia edytora unreal Engine.

Istnieje pięć kroków do tworzenie czynności:

1. Tworzenie lub tag usługi siatki nawigacji player
2. Tag Akustyka geometrii
3. Przypisywanie właściwości akustyczny materiałów do geometrii
4. Umieszczanie sondy (wersja zapoznawcza)
5. Tworzenie

## <a name="open-the-project-acoustics-editor-mode"></a>Otwórz tryb edytora Akustyka projektu

Zaimportować pakiet wtyczki Akustyka projektu do projektu. Aby uzyskać pomoc dotyczącą tego, zobacz [Unreal integracji](unreal-integration.md) tematu. Po zintegrowaniu wtyczki Otwórz Akustyka interfejsu użytkownika, klikając ikonę Nowy tryb Akustyka.

![Zrzut ekranu edytora Unreal Akustyka tryb](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Aktorzy tagu dla Akustyka

Karta obiektów jest pierwsza karta, która pobiera wyświetlana po otwarciu trybu Akustyka. Ta karta służy do podmiotów tagu w poziomie dodaje **AcousticsGeometry** lub **AcousticsNavigation** tagi do uczestników.

Wybierz jeden lub więcej obiektów w tworzenie konspektu świata, lub użyj **zbiorczo wybrane** sekcji mogą ułatwić wybór wszystkie obiekty w określonej kategorii. Gdy obiekty są wybrane, użyć **znakowanie** sekcji, aby zastosować żądanego tagu do wybranych obiektów.

Jeśli coś nie ma **AcousticsGeometry** ani **AcousticsNavigation** tagu, zostaną zignorowane w symulacji. Tylko statyczne oczek, nav siatki i krajobrazów są obsługiwane. Jeśli oznaczysz czymkolwiek zostaną zignorowane.

### <a name="for-reference-the-objects-tab-parts"></a>Aby uzyskać informacje dotyczące: Części karty obiektów

![Zrzut ekranu Akustyka obiektów karcie Unreal](media/unreal-objects-tab-details.png)

1. Przyciski wyboru karty (**obiektów** wybraną kartą). Użyj tych przycisków, aby poznać procedurę różne działania tworzenie Akustyka od góry do dołu.
2. Krótki opis co należy zrobić, korzystając z tej strony.
3. Dostępne selektory aktorów w poziomie.
4. Klikając **wybierz** wybierze wszystkich obiektów na poziomie spełniające co najmniej jeden z typów zaznaczone aktora.
5. Klikając **Usuń zaznaczenie wszystkich** wyczyści bieżące zaznaczenie. To jest taka sama jak naciskając klawisz ESC.
6. Użyj tych przycisków radiowych, aby wybrać czy zastosować tag Geometry ani nawigacji do wybranych uczestników.
7. Klikając **Tag** doda wybrany tag do wszystkich podmiotów aktualnie wybrany.
8. Klikając **Usuń oznakowanie** spowoduje usunięcie wybrany tag z wszystkich podmiotów aktualnie wybrany.
9. Klikając **wybierz oznakowane** Usuń bieżące zaznaczenie, a wybierz wszystkich uczestników z aktualnie wybrany tag.
10. Te statystyki pokazują, ile aktorów są oznaczane za pomocą poszczególnych typów znaczników.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Tag Akustyka geometrii zamknięcia i odbicie

Otwórz kartę obiektów okna Akustyka. Oznacz wszystkie obiekty jako Akustyka geometrii, jakby powinien occlude, odzwierciedlają lub ochrony przed rozproszonymi dźwięk. Geometria Akustyka może zawierać np podstaw, ściany, dachy, systemu windows i szkła okna, kilimy i meble dużych. Dla tych obiektów, można użyć dowolnego dowolnego poziomu złożoności. Ponieważ sceny voxelized przed symulacji, bardzo szczegółowe siatki, takie jak drzewa z wielu małych liści, nie jest droższy wprowadzić niż uproszczone obiekty.

Nie dołączaj rzeczy, które nie powinien wpływać na Akustyka, takich jak siatek kolizji niewidoczne.

Przekształcanie obiektu w czasie obliczania sondy (za pośrednictwem karty sondy poniżej) zostanie rozwiązany w wynikach tworzenie. Przenoszenie wszystkich zaznaczonych obiektów w scenie będzie wymagać ponownego wykonywania obliczeń sondowania i rebaking sceny.

### <a name="create-or-tag-a-navigation-mesh"></a>Tworzenie lub tagu siatki nawigacji

Siatka nawigacji służy umieścić punkty sondy symulacji. Można użyć w Unreal [Nav siatki granice woluminu](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html), lub można określić własne nawigacyjne siatki. Musisz otagować co najmniej jeden obiekt jako **nawigacji Akustyka**. Jeśli używasz firmy Unreal nawigacyjne siatki, upewnij się, że masz skompilowane na początku.

### <a name="acoustics-volumes"></a>Woluminy Akustyka ###

Brak dalszych, zaawansowane dostosowania można wprowadzić na obszary, nawigacji za pomocą **woluminy Akustyka**. **Woluminy Akustyka** są aktorami można dodać do sceny, które pozwalają na wybór obszary, które obejmują i Ignoruj z siatki nawigacji. Aktor udostępnia właściwości, które mogą być przełączane między "Include" i "Wyklucz". Woluminy "Include" Upewnij się, tylko obszary siatki nawigacji w nich znajdują, są traktowane jako i woluminy "Wyklucz" Oznacz tych obszarów, które mają być ignorowane. Woluminy "Wyklucz" są one zawsze stosowane po woluminy "Include". Upewnij się, że tag **woluminy Akustyka** jako **nawigacji Akustyka** zwykle proces na karcie obiektów. Aktorzy są ***nie*** automatycznie oznakowane.

![Zrzut ekranu Akustyka woluminu właściwości Unreal](media/unreal-acoustics-volume-properties.png)

Woluminy "Wyklucz" są głównie przeznaczone do szczegółową kontrolę na lokalizację nie sondy obostrzenie użycia zasobów.

![Zrzut ekranu przedstawiający wykluczania Akustyka woluminu w Unreal](media/unreal-acoustics-volume-exclude.png)

"Include" woluminy są przydatne w przypadku tworzenia ręczne części scenę, takich jak chcesz podzielić sceny w wielu strefach akustyczny. Na przykład w przypadku dużych sceny kwadrat wielu kilometrów i mieć dwóch obszarach zainteresowań, aby wprowadzić Akustyka na. Można narysować dwa woluminy "Include" big Data w scenie i tworzenia plików ACE dla każdego z nich pojedynczo. Następnie w grze, można użyć wyzwalacza woluminy łączyć z wywołaniami planu można załadować odpowiedniego pliku wpisu kontroli dostępu, gdy gracz zbliża się do każdego kafelka.

**Woluminy Akustyka** tylko ograniczenia nawigacji i ***nie*** geometrii. Każdy sondy wewnątrz "Include" **woluminu Akustyka** nadal będzie pobierać wszystkie niezbędne geometrii poza woluminu podczas przeprowadzania symulacji wave. W związku z tym nie powinien być żadnych przerw w zamknięcia lub innych Akustyka wynikające z odtwarzacza wykraczania poza granice z jednej sekcji do innego.

## <a name="select-acoustic-materials"></a>Wybierz materiały akustycznych

Gdy obiekty są oznaczone, kliknij przycisk **materiałów** przycisk, aby przejść do karty materiałów. Ta karta będzie służyć do określania właściwości materiału każdego materiału na poziomie. Zanim wszystkie podmioty są oznaczone, będzie ona pusta.

Akustyczny materiałów kontroli ilości energii dźwięku odzwierciedlone powrót po awarii z każdej powierzchni. Domyślny materiał akustyczny ma absorpcji, podobnie jak konkretny. Projekt Akustyka sugeruje materiałów, na podstawie nazwy materiału w scenie.

Czas reverberation danego materiału w pomieszczeniu odwrotnie jest powiązana z jego współczynnik, materiałami większość posiada absorpcji wartości z zakresu od 0,01 do 0,20. Materiały z współczynniki absorpcji powyżej tego zakresu są bardzo pochłaniający. Na przykład dźwięki pokoju zbyt reverberant, zmiana akustyczny materiał ściany, piętro lub limitu na coś absorptivity wyższy. Akustyczny przydziału materiału ma zastosowanie do wszystkich podmiotów, korzystających z tego materiału sceny.

![Wykres przedstawiający korelacja ujemna reverberation czasu współczynnik](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Aby uzyskać informacje dotyczące: Elementy na karcie materiały

![Zrzut ekranu Akustyka obiektów karcie Unreal](media/unreal-materials-tab-details.png)

1. **Materiałów** kartę przycisku używane do tej strony.
2. Krótki opis co należy zrobić, korzystając z tej strony.
3. Listę materiałów w poziomie z uczestnikami oznaczone jako **AcousticsGeometry**. Klikając tutaj materiał zaznaczyć wszystkie obiekty w scenie, używanego przez tego materiału.
4. Zawiera materiały akustyczny powierzonych materiału w scenie. Kliknij listę rozwijaną, aby ponownie przypisać materiał sceny do innego materiału akustyczny.
5. Pokazuje akustyczny współczynnik materiał wybraną w poprzednim kolumnę. Wartość zero oznacza, że dokładnie odzwierciedlają (nie absorpcji) podczas wartość 1 oznacza, że doskonale pochłaniającym (bez odbicia). Zmiana tej wartości spowoduje zaktualizowanie Akustyka materiał (krok #4) **niestandardowe**.

Po wprowadzeniu zmian do materiałów w do sceny, musisz przełączyć kart we wtyczce Akustyka projektu, aby wyświetlić te zmiany wpłynęły na **materiałów** kartę.

## <a name="calculate-and-review-listener-probe-locations"></a>Obliczanie i przejrzyj lokalizacji funkcji badania odbiornika

Po przypisaniu materiałów, przełącz się do **sondy** kartę.

### <a name="for-reference-parts-of-the-probes-tab"></a>Aby uzyskać informacje dotyczące: Elementy na karcie sondy

![Zrzut ekranu sondy Akustyka karcie Unreal](media/unreal-probes-tab-details.png)

1. **Sondy** przycisk karta umożliwia wyświetlenie na tej stronie
2. Krótki opis co należy zrobić, korzystając z tej strony
3. Umożliwia wybieranie rozpoznawania symulacji zgrubnym lub dobrym rozwiązaniem. Duże jest szybsze, ale ma pewne wady i zalety. Zobacz [tworzenie rozwiązania](bake-resolution.md) poniżej szczegółowe informacje.
4. Wybierz lokalizację, w których można umieścić pliki danych Akustyka za pomocą tego pola. Kliknij przycisk "...", aby użyć selektora folderów. Aby uzyskać więcej informacji na temat plików danych, zobacz [pliki danych](#Data-Files) poniżej.
5. Pliki danych dla tego sceny będzie miała przy użyciu prefiksu podane w tym miejscu. Wartość domyślna to "_AcousticsData [nazwa poziomu]".
6. Kliknij przycisk **Calculate** znajdujący się voxelize sceny i obliczyć lokalizacji punktu sondowania. Odbywa się lokalnie na urządzeniu i należy wykonać przed sposób tworzenie. Po sondy zostały obliczone, kontrolki powyżej zostanie wyłączona i kliknięcie tego przycisku spowoduje zmianę powiedzieć **wyczyść**. Kliknij przycisk **wyczyść** przycisk, aby wymazać obliczeń i włączyć formanty, tak, aby ponownie obliczyć przy użyciu nowych ustawień.

Sondy muszą znajdować się za pomocą zautomatyzowanego procesu w **sondy** kartę.


### <a name="what-the-calculate-button-calculates"></a>Oblicza przycisku "Oblicz"

**Calculate** przycisk ma wszystkie dane zostały podane w do tej pory (geometrii, nawigacja, materiałów i ustawienie zgrubnym/szczegółowe) i przechodzi przez kilka kroków:

1. On przyjmuje geometrii z siatek sceny i oblicza voxel woluminu. Voxel wolumin jest 3-wymiarowej, która obejmuje całą sceny, składa się z małych trzeciego stopnia "voxels". Rozmiar voxels jest określana przez częstotliwość symulacji, która została ustawiona przez **rozpoznawania symulacji** ustawienie. Każdy voxel jest oznaczony jako opcję "Otwórz udziału użytkownika" lub zawierających geometrii sceny. Jeśli voxel zawiera geometrii voxel zostanie oznaczony za pomocą współczynnik przypisanych do tej geometrii materiałów.
2. Następnie używa danych nawigacji do obliczania pod względem akustycznym interesujące lokalizacji, gdzie gracz. Próbuje znaleźć zestaw rozsądnie małe rozmiary z tych lokalizacji, który zawiera mniejszych obszarów, takich jak drzwi i korytarzach, a następnie do pomieszczenia, aby otworzyć miejsca do magazynowania. Dla małych scen zazwyczaj jest to mniej niż 100 lokalizacji podczas dużych sceny może mieć maksymalnie tysiąc.
3. Dla każdej lokalizacji końcowej odbiornik, który oblicza określa liczbę parametrów, takich jak jak "otwarte" to miejsce, rozmiar miejsca, w którym się itp.
4. Wyniki te obliczenia są przechowywane w plikach w miejscu określonym przez użytkownika (zobacz [pliki danych](#Data-Files) poniżej)

W zależności od rozmiaru sceny i szybkość maszyny tych obliczeń może potrwać kilka minut.

Po zakończeniu tych obliczeń możesz wyświetlić podgląd danych voxel i lokalizacji punktu sondowania, aby mieć pewność, że tworzenie prześle Ci dobre wyniki. Elementy, takie jak siatki zły nawigacji lub geometrii Brak dodatkowych zazwyczaj będzie szybko widoczne w wersji zapoznawczej, więc można ją poprawić.


## <a name="debug-display"></a>Debugowanie wyświetlania

Po zakończeniu obliczania sondowania nowych aktora pojawi się w świecie Tworzenie konspektu, o nazwie **AcousticsDebugRenderer**. Sprawdzanie **renderowania sondy** i **renderowania Voxels** pola wyboru spowoduje wyświetlanie debugowania w edytorze okienka ekranu.

![Zrzut ekranu przedstawiający renderowania debugowania Akustyka aktora w edytorze Unreal](media/acoustics-debug-renderer.png)

Jeśli nie widzisz żadnych voxels lub sond nałożony na Twoim poziomie, upewnij się, że renderowania w czasie rzeczywistym jest włączona w okienku ekranu.

![Zrzut ekranu przedstawiający opcję renderowania w czasie rzeczywistym w Unreal](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels

Voxels są wyświetlane w oknie sceny jako zielony modułów wokół geometrii uczestniczących w programie. Voxels, które zawierają tylko air nie są wyświetlane. Występuje duże zielone pole wokół sceny całego, który oznacza wolumin voxel pełną, który będzie używany w symulacji.
Poruszanie się sceny i sprawdzić, czy geometrii pod względem akustycznym occluding voxels. Należy także sprawdzić, czy inne niż Akustyka obiekty, takie jak kolizja siatek opublikowano voxelized. Aparat sceny musi można w ciągu około 5 liczniki obiektu dla voxels do wyświetlenia.

Możesz porównać voxels utworzone przy użyciu rozpoznawania poprawnie vs zgrubnym rozwiązania, pojawi się zgrubnym voxels dwukrotnie są tak duże.

![Zrzut ekranu Akustyka voxels w wersji zapoznawczej w edytorze Unreal](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Punkty sondy

Sonda punkty oznaczają to samo z lokalizacjami możliwe player (odbiornika). Gdy pieczenie, symulacja oblicza Akustyka wszystkie lokalizacje źródłowe możliwe nawiązywanie połączenia z każdego punktu sondowania. W czasie wykonywania lokalizacja odbiornika są interpolowane między pobliskimi punktami sondowania.

Należy sprawdzić punkty sondy istnieje wszędzie tam, gdzie gracz oczekuje się przechodzić w scenie. Sondy punkty są umieszczane w siatce nawigacji przez aparat Akustyka projektu i nie można przenieść lub edytowane, więc upewnij się, obejmuje siatki nawigacji wszystkie lokalizacje możliwe odtwarzacza, sprawdzając punktów sondowania.

![Zrzut ekranu Akustyka sondy w Unreal w wersji zapoznawczej](media/unreal-probes-preview.png)

Zobacz [tworzenie rozwiązania](bake-resolution.md) więcej informacji na temat zdalnego vs poprawnie rozdzielczości.

## <a name="bake-your-level-using-azure-batch"></a>Tworzenie poziomu przy użyciu usługi Azure Batch

Można wprowadzić sceny z klastra obliczeniowego w chmurze przy użyciu usługi Azure Batch. Wtyczki projektu Akustyka Unreal łączy się bezpośrednio z usługi Azure Batch do utworzenia wystąpienia, zarządzanie i zatrzymywania klastra usługi Azure Batch dla każdego tworzenie. Na karcie Tworzenie wprowadź swoje poświadczenia platformy Azure, wybierz typ komputera klastra i rozmiar, a następnie kliknij przycisk Tworzenie.

### <a name="for-reference-parts-of-the-bake-tab"></a>Aby uzyskać informacje dotyczące: Elementy na karcie Tworzenie

![Zrzut ekranu tworzenie Akustyka karcie Unreal](media/unreal-bake-tab-details.png)

1. Tworzenie karty przycisku używane do tej strony.
2. Krótki opis, co można zrobić na tej stronie.
3. Pola, aby wprowadzić swoje poświadczenia platformy Azure, po utworzeniu konta platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Azure Batch](create-azure-account.md).
4. Uruchamianie portalu Azure, aby zarządzać subskrypcjami, monitorowania użycia i wyświetlania informacji dotyczących rozliczeń itd. 
5. Usługa Azure batch compute typ węzła na potrzeby obliczeń. Typ węzła musi być obsługiwany według lokalizacji centrum danych platformy Azure. Jeśli nie masz pewności, pozostaw **Standard_F8s_v2**.
6. Liczba węzłów na potrzeby tego obliczenia. Liczba, wprowadź w tym miejscu ma wpływ na czas wymagany do ukończenia tworzenie i jest ograniczone przez przydziału rdzeni usługi Azure Batch. Alokacja domyślna tylko umożliwia węzłów dwóch 8 rdzeni lub jednego 16 rdzeni węzła, ale można rozszerzyć. Aby uzyskać więcej informacji na temat ograniczeń alokacji core, zobacz [Tworzenie konta usługi Azure Batch](create-azure-account.md).
7. Zaznacz to pole wyboru, aby skonfigurować pulę obliczeniowych do użycia [węzły o niskim priorytecie](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Węzły obliczeniowe o niskim priorytecie mają znacznie niższe koszty, ale mogą być niedostępne lub mogą być przerywane, w dowolnym momencie.
8. Ilość czasu oczekiwano podejmowane dla zadania do uruchamiania w chmurze. Nie dotyczy to czas uruchamiania węzła. Gdy zadanie zacznie działać, to o tym, ile powinna być, aby wrócić wyniki. Należy pamiętać, że jest to tylko oszacowanie.
9. Całkowita ilość czasu obliczeniowego potrzebnych do uruchomienia symulacje. Jest to suma czasu obliczeń węzeł, który będzie używany na platformie Azure. Zobacz [koszt tworzenie Szacowanie](#Estimating-bake-cost) poniżej Aby uzyskać więcej informacji na temat korzystania z tej wartości.
10. Kliknij przycisk Tworzenie, aby przesłać tworzenie do chmury. Gdy zadanie jest uruchomione, spowoduje to pokazanie **anulować zadanie** zamiast tego. Czy istnieją błędy na tej karcie, czy przepływ pracy na **sondy** karta nie zostanie zakończone, ten przycisk będzie wyłączony.
11. Liczba sondy sceny obliczone na **sondy** kartę. Liczbę sond określa liczbę symulacje, które należy uruchomić w chmurze. Nie można określić więcej węzłów niż jest sondy.
12. Ten komunikat informujący o bieżący stan zadania, lub jeśli występują błędy na tej karcie, co te błędy.

Można zawsze uzyskać pełne informacje na temat aktywnych zadań, pule obliczeniowe i Magazyn w [witryny Azure portal](https://portal.azure.com).

Gdy zadanie jest uruchomione **tworzenie** przycisku zmienia się na **anulować zadanie**. Użyj tego przycisku można anulować zadania w toku. Trwa anulowanie zadania nie można cofnąć, żadne wyniki nie będą dostępne, i możesz nadal jest naliczana raz obliczeniowych platformy Azure, używane przed anulowaniem.

Po rozpoczęciu tworzenie, możesz zamknąć Unreal. W zależności od projektu, typ węzła i liczby węzłów tworzenie chmury może potrwać kilka godzin. Tworzenie stanu zadania zostaną zaktualizowane, gdy ponowne załadowanie projektu i Otwórz okno Akustyka. Jeśli zadanie zostało ukończone, plik wyjściowy zostanie pobrany.

Poświadczenia platformy Azure są bezpiecznie przechowywane na komputerze lokalnym i skojarzone z projektem unreal Engine. Służą one wyłącznie w celu nawiązania bezpiecznego połączenia z platformą Azure.

### <a name="Estimating-bake-cost"></a> Trwa szacowanie kosztu tworzenie platformy Azure

Aby oszacować, jaki będzie koszt danego tworzenie, wykonaj wartości wyświetlane dla **szacowany koszt obliczeń**, który jest czasem trwania i wielokrotnie kosztów, przez co godzinę w lokalnej walucie z **typ węzła maszyny Wirtualnej** wybrane. Wynik nie będzie również obejmował czas węzła potrzebne do uruchomienia w węzłach i uruchamiania. Na przykład w przypadku wybrania **Standard_F8s_v2** typu węzła, który ma koszt 0,40 USD/godz., a szacowany koszt obliczeń jest 3 godzin i 57 minut, szacowany koszt będzie $0,40 * ~ 4 godziny = ~ 1,60 USD. Rzeczywisty koszt będzie prawdopodobnie nieco wyższy ze względu na dodatkowy czas, aby uzyskać dostęp do węzłów pracę. Można znaleźć węzła co godzinę, koszt na [cennik usługi Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) strony (wybierz opcję "zoptymalizowane pod kątem obliczeń" lub "obliczenia o wysokiej wydajności" dla kategorii).

### <a name="reviewing-the-bake-results"></a>Sprawdzanie wyników tworzenie

Po zakończeniu tworzenie, sprawdź, czy punkty voxels i badania znajdują się w ich oczekiwanych lokalizacjach za pomocą wtyczki środowiska uruchomieniowego.

## <a name="Data-Files"></a>Pliki danych

Istnieją cztery pliki danych utworzonych przez ten dodatek w różnych punktach. Tylko jeden z nich jest potrzebna w czasie wykonywania i znajduje się w folderze zawartości/Akustyka projektu, który jest automatycznie dodawany do ścieżki pakowania projektu. Pozostałe trzy znajdują się w folderze Akustyka dane i nie są w pakiecie.

* **[Project]/Config/ProjectAcoustics.cfg**: Ten plik przechowuje dane, które możesz wprowadzić w polach w Interfejsie użytkownika Akustyka trybu. Nie można zmienić lokalizację i nazwę tego pliku. Istnieją inne wartości przechowywane w tym pliku, które wpływają na tworzenie, ale są dla użytkowników zaawansowanych i nie powinna być zmieniana.
* **[Project]/Content/Acoustics/[LevelName]\_AcousticsData.ace**: Ten plik jest jest tworzony podczas symulacji tworzenie i zawiera dane wyszukiwania używany przez środowisko uruchomieniowe do renderowania Akustyka swoje sceny. Lokalizację i nazwę tego pliku można zmienić przy użyciu pól na **sondy** kartę.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData.vox**: Ten plik przechowuje geometrii Akustyka voxelized i właściwości materiału. Obliczane przy użyciu **Calculate** znajdujący się na **sondy** kartę. Lokalizację i nazwę tego pliku można zmienić przy użyciu pól na **sondy** kartę.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData\_config.xml**: Ten plik przechowuje obliczane przy użyciu parametrów **Calculate** znajdujący się na **sondy** kartę. Lokalizację i nazwę tego pliku można zmienić przy użyciu pól na **sondy** kartę.

Należy zadbać, aby nie usuwać plików *.ace pobrany z platformy Azure. Ten plik nie jest możliwe do odzyskania, z wyjątkiem przez rebaking sceny.

## <a name="next-steps"></a>Kolejne kroki
* Zapoznaj się z [projektowania formanty Unreal](unreal-workflow.md)
* Zapoznaj się z [koncepcji projektów Akustyka projektu](design-process.md)

