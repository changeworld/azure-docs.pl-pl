---
title: Tworzenie Akustyka z Akustyka projektu
titlesuffix: Azure Cognitive Services
description: W tym dokumencie opisano proces przesyłania tworzenie Akustyka przy użyciu rozszerzenia edytora aparatu Unity.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: c37e050cd762cb173d64f78b5267e4ad252d17a9
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902250"
---
# <a name="bake-acoustics"></a>Tworzenie Akustyka

W tym dokumencie opisano proces przesyłania tworzenie Akustyka przy użyciu rozszerzenia edytora aparatu Unity. Aby uzyskać więcej ogólnych informacji na Akustyka, zobacz [co to jest Akustyka](what-is-acoustics.md). Przewodnik Szybki Start, zobacz [wprowadzenie](getting-started.md).

## <a name="import-the-plugin"></a>Importuj wtyczki

Importowanie pakietu wtyczki Akustyka do projektu. Następnie otwórz Akustyka interfejsu użytkownika, wybierając **okna > Akustyka** menu aparatu Unity:

![Otwórz Akustyka okna](media/WindowAcoustics.png)

## <a name="principles"></a>Zasady

Okno narzędzia Akustyka zbiera informacje Akustyka aparat musi obliczyć Akustyka do sceny. Istnieje pięć kroków do tworzenie czynności:

1. Tworzenie lub oznaczyć swoje player nawigacji siatki
2. Oznacz Akustyka geometrii
3. Przypisywanie właściwości akustyczny materiałów do geometrii
4. Umieszczanie sondy (wersja zapoznawcza)
5. Tworzenie

Po zakończeniu tworzenie zobacz [Omówienie procesu projektowania dla Akustyka](design-process.md) kroki opcjonalne po Tworzenie projektu.

## <a name="create-or-mark-a-navigation-mesh"></a>Tworzenie lub znak nawigacyjne siatki

Siatka nawigacji służy umieścić punkty sondy symulacji. Można użyć mechanizmu Unity [nawigacyjne siatki w przepływie pracy](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html), lub można określić własne nawigacyjne siatki. Oczek nawigacji utworzonych za pomocą mechanizmu Unity przepływu pracy będą pobierane przez system Akustyka. Aby użyć własnego siatek, oznacz je z **obiektów** kartę zgodnie z opisem w następnym kroku.

## <a name="objects-tab"></a>Karta obiekty

Otwórz **obiektów** karcie **Akustyka** okna. Ta karta służy do oznaczania obiektów w do sceny, po prostu dodaje **AcousticsGeometry** lub **AcousticsNavigation** składników do obiektu. Można również użyć [standardowa przepływu pracy składnika Unity](https://docs.unity3d.com/Manual/UsingComponents.html) zaznaczyć lub odznaczyć obiektów.

Wybierz jeden lub więcej obiektów w oknie lub hierarchię sceny i następnie zaznacz lub usuń zaznaczenie, ich **AcousticsGeometry** lub **AcousticsNavigation** zgodnie z poniższym opisem. Jeśli nic nie jest zaznaczone, można oznaczyć lub odznaczyć wszystkie elementy w scenie naraz.

Może być oznaczony tylko renderowania siatki i terenach. Inne typy obiektów, zostaną zignorowane. Zaznacz pola wyboru będą zaznacz lub usuń zaznaczenie wszystkich obiektów dotkniętych wadami.

Jeśli masz niczego nie wybrano w do sceny, będzie to wyglądać jak na poniższym obrazie:

![Obiekty karcie Brak zaznaczenia](media/ObjectsTabNoSelectionDetail.png)

Jeśli masz coś, co jest wybrane w oknie sceny lub hierarchię, będzie to wyglądać jak na poniższym obrazie:

![Obiekty karcie Brak zaznaczenia](media/ObjectsTabWithSelectionDetail.png)

### <a name="objects-tab-parts"></a>Części karty obiektów

Części strony karty są:

1. Przyciski wyboru karty (**obiektów** wybraną kartą). Użyj tych przycisków, aby zapoznać się z kroków różne działania tworzenie Akustyka od lewej do prawej.
2. Krótki opis co należy zrobić, korzystając z tej strony.
3. Filtry dostępne w oknie hierarchii. Umożliwia filtrowanie okno hierarchii obiektów określonego typu, dzięki czemu łatwiej można oznaczyć. Jeśli nie został jeszcze oznaczony nastąpią żadne Akustyka, wybierając opcje ostatnich dwóch opisano nothing. Jednakże mogą być użyteczne do znajdowania obiektów oznaczonych, gdy zostało to zrobione.
4. Gdy żadne obiekty nie są wybrane, w tej sekcji przedstawia stan wszystkich obiektów w scenie:
    * Łącznie — całkowita liczba aktywnych, nieukrytą obiektów w scenie.
    * Zignorowane — liczba obiektów, które nie są renderowania siatki lub terenach.
    * Siatka — liczba siatki renderowania obiektów w scenie
    * Terenu — liczba terenu obiektów w scenie
    * Geometria — liczba siatki lub terenu obiektów w scenie, oznaczone jako "Akustyka geometrii"
    * Nawigacja — liczba siatki lub terenu obiektów w scenie, oznaczone jako "Akustyka Navigation". Ta liczba nie obejmuje NavMesh mechanizmu Unity.
5. Zawiera całkowitą liczbę "zapytania — możliwe" obiektów w scenie, która jest tylko renderowania siatki i terenach. Pokazuje pola wyboru, można użyć do oznaczania (Dodaj odpowiedni składnik do) te obiekty geometrii lub nawigację dla Akustyka
6. Jeśli nic nie jest zaznaczone, ta notatka przypomina o tym, aby wybrać obiekty do znakowania, w razie potrzeby. Możesz również sprawdzić jeden lub oba pola wyboru, aby oznaczyć wszystkie obiekty w scenie bez zaznaczania żadnych.
7. Po wybraniu obiektów w tej sekcji przedstawiono stan wybranych obiektów.
8. Pokazuje całkowitą liczbę "zapytania — możliwe" wybrane obiekty. Zaznaczenie lub usunięcie zaznaczenia pola wyboru będą zaznacz lub usuń zaznaczenie tylko wybrane obiekty.

Jeśli niektóre obiekty są oznaczone, a niektóre nie są odpowiednie pole wyboru wyświetli wartość "mieszany":

![Mieszane wartości pola wyboru](media/MixedObjectSelectionDetail.png)

Kliknięcie pola wyboru spowoduje to wymuszenie wszystkie obiekty był oznaczony jako, a następnie ponownie klikając spowoduje usunięcie zaznaczenia wszystkich obiektów.

Obiekty mogą być zaznaczone dla geometrii i nawigacji.

### <a name="guidelines-for-marking-objects"></a>Wytyczne dotyczące oznaczania obiektów

Pamiętaj oznaczyć wszystkie obiekty jako **geometrii Akustyka** one powinien occlude, odzwierciedlają, czy wchłonąć dźwięku. Geometria Akustyka może zawierać np podstaw, ściany, dachy, systemu windows i szkła okna, kilimy i meble dużych. Ta opcja jest akceptowalne obejmujący mniejszych obiekty, takie jak lamp, elementy dekoracyjne, oprawami świetlnymi, jak nie zwiększają one znacznie Tworzenie kosztów. Należy nie przegap główne elementy, takie jak ziemi czy górnej granicy. Ponadto nie uwzględniają rzeczy, które nie powinny mieć wpływ na Akustyka, takie jak kolizja siatki.

Przekształcanie obiektu w czasie obliczania sondy (za pośrednictwem **sondy** karcie poniżej) zostanie rozwiązany w wynikach tworzenie. Przenoszenie wszystkich zaznaczonych obiektów w scenie będzie wymagać ponownego wykonywania obliczeń sondowania i rebaking sceny.

## <a name="materials-tab"></a>Karta materiały

Gdy obiekty są oznaczone, kliknij przycisk **materiałów** przycisk, aby przejść do karty materiałów.

### <a name="parts-of-the-materials-tab"></a>Elementy na karcie materiały

![Karta materiałów — szczegóły](media/MaterialsTabDetail.png)

1. **Materiałów** kartę przycisku używane do tej strony.
2. Krótki opis co należy zrobić, korzystając z tej strony.
3. Po zaznaczeniu tej opcji tylko użytych przez obiekty oznaczone jako **geometrii Akustyka** zostaną wyświetlone. W przeciwnym razie zostaną wyświetlone wszystkie materiały, używany w scenie.
4. Użyj tych opcji, aby zmienić kolejność menu rozwijanego, która jest wyświetlana, gdy przycisk listy rozwijanej w kolumnie Akustyka poniżej (6). **Nazwa** sortuje akustyczny materiałów według nazwy. "Absorptivity" posortowane w kolejności absorptivity od niskiego na wysoki.
5. Lista użytych w scenie, sortowana alfabetycznie. Jeśli **Pokaż tylko zaznaczone** pole wyboru jest zaznaczone (3), tylko użytych przez obiekty oznaczone jako **geometrii Akustyka** są wyświetlane. Klikając tutaj materiał zaznaczyć wszystkie obiekty w scenie, używanego przez tego materiału.
6. Zawiera materiały akustyczny powierzonych materiału w scenie. Kliknij listę rozwijaną, aby ponownie przypisać materiał sceny do innego materiału akustyczny. Możesz zmienić kolejność sortowania menu wyświetlane po kliknięciu elementu w tym miejscu przy użyciu **Akustyka Sortuj według:** opcje (# 4).
7. Pokazuje akustyczny współczynnik materiał wybraną w poprzednim kolumnę. Wartość zero oznacza, że dokładnie odzwierciedlają (nie absorpcji) podczas wartość 1 oznacza, że doskonale pochłaniającym (bez odbicia). Nie można zmienić współczynnik absorpcji, chyba że wybrany materiał "Niestandardowe".
8. Materiał przypisane na "Niestandardowe", suwak już jest wyłączona i można wybrać współczynnik absorpcji za pomocą suwaka lub wpisując wartość. Aby uzyskać więcej informacji na temat właściwości materiału, zobacz [Omówienie procesu projektowania dla Akustyka](design-process.md).

### <a name="guidelines-for-assigning-materials-or-absorption-values"></a>Wskazówki dotyczące przypisywania materiałów (lub wartości pochłaniania)

Na tej karcie próbuje odgadnąć, co to jest absorpcji materiałach na podstawie nazwy. Na przykład jeśli Twoja nazwa materiału w scenie jest LivingRoom_WoodTable, początkowej materiał akustyczny przypisane do niego będzie "drewna". Materiały, w której nie można ustalić znanych materiał są przypisywane materiału "Domyślna", który ma absorpcji, podobnie jak konkretny.

Można ponownie przypisać materiały akustycznego każdego materiału sceny. Na przykład dźwięki pokoju zbyt reverberant, zmiana akustyczny materiał ściany, piętro lub limitu na coś absorptivity wyższy. Akustyczny przydziału materiału ma zastosowanie do wszystkich obiektów używających materiału sceny.

## <a name="probes-tab"></a>Karta sondy

Po przypisaniu materiałów, przełącz się do **sondy** kartę.

### <a name="parts-of-the-probes-tab"></a>Elementy na karcie sondy

![Karta sondy — szczegóły](media/ProbesTabDetail.png)

1. **Sondy** przycisk karta umożliwia wyświetlenie na tej stronie
2. Krótki opis co należy zrobić, korzystając z tej strony
3. Użyj tych, aby wybrać rozdzielczość symulacji zgrubnym lub dobrym rozwiązaniem. Duże jest szybsze, ale ma pewne wady i zalety. Zobacz ["grubych vs szczegółowe rozwiązania"](#Coarse-vs-Fine-Resolution) poniżej szczegółowe informacje.
4. Wybierz lokalizację, w których można umieścić pliki danych Akustyka za pomocą tego pola. Kliknij przycisk "...", aby użyć selektora folderów. Wartość domyślna to **zasobów/AcousticsData**. **Edytora** podfolder zostanie również utworzony w tej lokalizacji. Aby uzyskać więcej informacji na temat plików danych, zobacz ["Data Files"](#Data-Files) poniżej.
5. Pliki danych dla tego sceny będzie miała przy użyciu prefiksu podane w tym miejscu. Wartość domyślna to "Acoustics_ [Nazwa sceny]".
6. Po sondy zostały obliczone z kontrolki powyżej zostanie wyłączona. Kliknij przycisk **wyczyść** przycisk, aby wymazać obliczeń i włączyć formanty, tak, aby ponownie obliczyć przy użyciu nowych ustawień.
7. Kliknij przycisk **obliczania...**  znajdujący się voxelize sceny i obliczyć lokalizacji punktu sondowania. Odbywa się lokalnie na urządzeniu i należy wykonać przed sposób tworzenie.

W tej wersji projektu Akustyka sondy nie może znajdować się ręcznie i muszą znajdować się za pomocą zautomatyzowanego procesu w **sondy** kartę.

### <a name="what-the-calculate-button-calculates"></a>Oblicza przycisku "Oblicz..."

**Obliczania...**  przycisk ma wszystkie dane zostały podane w do tej pory (geometrii, nawigacja, materiałów i ustawienie duże/szczegółowe) i przechodzi przez kilka kroków:

1. On przyjmuje geometrii z siatek sceny i oblicza voxel woluminu. Voxel wolumin jest 3-wymiarowej, która obejmuje całą sceny, składa się z małych trzeciego stopnia "voxels". Rozmiar voxels jest określana przez częstotliwość symulacji, która została ustawiona przez **rozpoznawania symulacji** ustawienie. Każdy voxel jest oznaczony jako opcję "Otwórz udziału użytkownika" lub zawierających geometrii sceny. Jeśli voxel zawiera geometrii voxel zostanie oznaczony za pomocą współczynnik przypisanych do tej geometrii materiałów.
2. Następnie używa danych nawigacji do obliczania pod względem akustycznym interesujące lokalizacji, gdzie gracz. Próbuje znaleźć zestaw rozsądnie małe rozmiary z tych lokalizacji, który zawiera mniejszych obszarów, takich jak drzwi i korytarzach, a następnie do pomieszczenia, aby otworzyć miejsca do magazynowania. Dla małych scen zazwyczaj jest to mniej niż 100 lokalizacji podczas dużych sceny może mieć maksymalnie tysiąc.
3. Dla każdej lokalizacji końcowej odbiornik, który oblicza określa liczbę parametrów, takich jak jak "otwarte" to miejsce, rozmiar miejsca, w którym się itp.
4. Wyniki te obliczenia są przechowywane w plikach w miejscu określonym przez użytkownika (zobacz ["Data Files"](#Data-Files) poniżej)

W zależności od rozmiaru sceny i szybkość maszyny tych obliczeń może potrwać kilka minut.

Po zakończeniu tych obliczeń możesz wyświetlić podgląd danych voxel i lokalizacji punktu sondowania, aby mieć pewność, że tworzenie prześle Ci dobre wyniki. Elementy, takie jak siatki zły nawigacji lub geometrii Brak dodatkowych zazwyczaj będzie szybko widoczne w wersji zapoznawczej, więc można ją poprawić.

### <a name="scene-rename"></a>Zmień nazwę sceny

Nazwa sceny jest używana do sceny łączenie się z plikami przechowywania położenie punktu sondowania i voxelization. Jeśli sceny zostanie zmieniona po sondy punkty są obliczane, materiału przypisania i położenia danych zostaną utracone i wymaga ponownego uruchomienia.

## <a name="debug-display-through-gizmos"></a>Debugowanie wyświetlana przez elementy Gizmo

Domyślnie zarówno **sondy** i **Voxels** gizmo są włączone. Te opisano voxels i sondy punktu lokalizacje, które zostały obliczone. One można włączyć lub wyłączyć za pomocą menu Gizmo:

![Elementy gizmo Menu](media/GizmosMenu.png)

### <a name="voxels"></a>Voxels

Voxels są wyświetlane w oknie sceny jako zielony modułów wokół geometrii uczestniczących w programie. Voxels, które zawierają tylko air nie są wyświetlane. Występuje duże zielone pole wokół sceny całego, który oznacza wolumin voxel pełną, który będzie używany w symulacji.
Poruszanie się sceny i sprawdź, czy ma wszystko, co powinno być geometrii voxels. Aparat sceny musi można w ciągu około 5 liczniki obiektu dla voxels do wyświetlenia.

Możesz porównać voxels utworzone przy użyciu rozpoznawania poprawnie vs Zgrubnym rozwiązania, pojawi się zgrubnym voxels dwukrotnie są tak duże.

![Voxel (wersja zapoznawcza)](media/VoxelCubesPreview.png)

### <a name="probe-points"></a>Punkty sondy

Sonda punkty oznaczają to samo z lokalizacjami możliwe player (odbiornika). Podczas ustalania tworzenie, Akustyka dla źródła dźwięku w dowolnym miejscu sceny jest obliczana dla każdego z tych punktów sondowania. Jeśli gracz nie jest bezpośrednio w miejscu punktu sondowania, dane z punktów sondy najbardziej zbliżona odtwarzacz są używane do interpolacji parametrów w tej lokalizacji.

W związku z tym jest istotne dla zapewnienia dowolnym istnieją punkty sondy odtwarzacz oczekuje się, że są przesyłane w scenie i odpowiednio reprezentowane małych obszarów i drzwi. Sonda punktów umieszczone przez aparat projektu Akustyka interpretacji geometrii swoje sceny w oparciu i nie można przenosić ani edytować w tej wersji zapoznawczej projektanta. Użyj ich do sprawdzenia poprawności swojej znakowania geometry, danych wskazówka nawigacji.

![Sondy (wersja zapoznawcza)](media/ProbesPreview.png)

### <a name="Coarse-vs-Fine-Resolution"></a>Rozpoznawanie poprawnie zdalnego programu vs

Jedyną różnicą między ustawień rozpoznawania duże i szczegółowe to częstotliwość, w którym odbywa się symulacji. Szczegółowe korzysta z częstotliwością dwukrotnie możliwie duże.
A to może wydawać się prosty, ma wiele skutki akustyczny symulacji:

* Długość fali duże jest dwa razy, jak długo jak prawidłowo i w związku z tym voxels dwukrotnie są tak duże.
* Rozmiar voxel, dzięki czemu duże, tworzenie około 16 razy szybciej niż tworzenie dobrym rozwiązaniem jest bezpośrednio związana czasu symulacji.
* Nie może być symulowana portali (na przykład drzwi lub systemu windows) mniejszy niż rozmiar voxel. Gruba ustawienie może spowodować, że niektóre z tych portali mniejszych, aby nie być symulowana; w związku z tym nie przekaże dźwięku za pomocą w czasie wykonywania. Aby zobaczyć, jeśli to się dzieje, wyświetlając voxels.
* Mniejszą częstotliwością symulacji powoduje mniej diffraction wokół rogi i krawędzie.
* Dźwięk źródła nie może znajdować się wewnątrz "wypełnione" voxels będącego voxels, który zawiera geometrii — skutkuje to Brak dźwięku. Jest trudne do zlokalizowania źródeł dźwięku, aby nie były w większych voxels z duże niż ustawienie dobrym rozwiązaniem.
* Większe voxels będzie mającym więcej do portali, jak pokazano poniżej. Pierwszy obraz został utworzony przy użyciu duże, podczas gdy druga jest tego samego pola, za pomocą cienkiej rozpoznawania. Wskazane przez czerwony oznaczenia, jest znacznie mniej nieautoryzowanego dostępu do pola przy użyciu ustawień w dobrym stanie. Niebieska linia jest bramą, zgodnie z definicją geometrii, podczas gdy czerwona linia jest skuteczne portal akustyczne, zależy od rozmiaru voxel. Jak ta włamań odgrywa w danej sytuacji zależy od całkowicie jak voxels wiersz w górę przy użyciu geometrii portalu, który zależy od rozmiaru i lokalizacje obiektów w scenie.

![Gruba drzwi](media/CoarseVoxelDoorway.png)

![Dobrym rozwiązaniem drzwi](media/FineVoxelDoorway.png)

## <a name="bake-tab"></a>Tworzenie karty

Po zakończeniu weryfikacji danych (wersja zapoznawcza), użyj **tworzenie** kartę, aby przesłać Tworzenie usługi w chmurze.

### <a name="parts-of-the-bake-tab"></a>Elementy na karcie Tworzenie

![Tworzenie Karta Szczegóły](media/BakeTabDetails.png)

1. Tworzenie karty przycisku używane do tej strony.
2. Krótki opis, co można zrobić na tej stronie.
3. Pola, aby wprowadzić swoje poświadczenia platformy Azure, po utworzeniu konta platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Azure Batch](create-azure-account.md).
4. Tag obrazu platformy docker dla zestawu narzędzi Akustyka.
5. Uruchamianie portalu Azure, aby zarządzać subskrypcjami, monitorowania użycia i wyświetlania informacji dotyczących rozliczeń itd. 
6. Usługa Azure batch compute typ węzła na potrzeby obliczeń. Typ węzła musi być obsługiwany według lokalizacji centrum danych platformy Azure. Jeśli nie masz pewności, pozostaw **Standard_F8s_v2**.
7. Liczba węzłów na potrzeby tego obliczenia. Liczba, wprowadź w tym miejscu ma wpływ na czas wymagany do ukończenia tworzenie i jest ograniczone przez przydziału rdzeni usługi Azure Batch. Alokacja domyślna tylko umożliwia węzłów dwóch 8 rdzeni lub jednego 16 rdzeni węzła, ale można rozszerzyć. Aby uzyskać więcej informacji na temat ograniczeń alokacji core, zobacz [Tworzenie konta usługi Azure Batch](create-azure-account.md).
8. Zaznacz to pole wyboru, aby skonfigurować pulę obliczeniowych do użycia [węzły o niskim priorytecie](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Węzły obliczeniowe o niskim priorytecie mają znacznie niższe koszty, ale mogą być niedostępne lub mogą być przerywane, w dowolnym momencie.
9. Liczba sondy sceny obliczone na **sondy** kartę. Liczbę sond określa liczbę symulacje, które należy uruchomić w chmurze. Nie można określić więcej węzłów niż jest sondy.
10. Ilość czasu oczekiwano podejmowane dla zadania do uruchamiania w chmurze. Nie dotyczy to czas uruchamiania węzła. Gdy zadanie zacznie działać, to o tym, ile powinna być, aby wrócić wyniki. Należy pamiętać, że jest to tylko oszacowanie.
11. Całkowita ilość czasu obliczeniowego potrzebnych do uruchomienia symulacje. Jest to suma czasu obliczeń węzeł, który będzie używany na platformie Azure. Zobacz [koszt tworzenie Szacowanie](#Estimating-bake-cost) poniżej Aby uzyskać więcej informacji na temat korzystania z tej wartości.
12. Ten komunikat informuje, gdzie będzie można zapisać wyników tworzenie po ukończeniu zadania.
13. (Tylko w trybie Zaawansowane) Jeśli zaistnieje należy wymusić Unity zapomnieć o tworzenie przesłane (np. pobrano wyniki za pomocą innego komputera), kliknij przycisk **Wyczyść stan** przycisk zapomnieć o zadaniu, który został przesłany. Należy pamiętać, że oznacza to plik wyników, gdy wszystko będzie gotowe, będzie **nie** pobrany, i **to nie jest taka sama jak anulowanie zadania**. Zadanie, jeśli uruchomione, będzie kontynuował pracę w chmurze.
14. Kliknij przycisk Tworzenie, aby przesłać tworzenie do chmury. Gdy zadanie jest uruchomione, spowoduje to pokazanie **anulować zadanie** zamiast tego.
15. Przygotowuje przetwarzanie symulacji Akustyka na komputerach lokalnych. Zobacz [Tworzenie lokalnego](#Local-bake) Aby uzyskać więcej informacji.  
16. Ten obszar zawiera stan tworzenie. Po zakończeniu powinien być wyświetlony **pobrane**.

Można zawsze uzyskać pełne informacje na temat aktywnych zadań, pule obliczeniowe i Magazyn w [witryny Azure Portal](https://portal.azure.com).

Gdy zadanie jest uruchomione **tworzenie** przycisku zmienia się na **anulować zadanie**. Użyj tego przycisku można anulować zadania w toku. Użytkownik jest proszony o potwierdzenie przed anulowaniem zadania. Trwa anulowanie zadania nie można cofnąć, żadne wyniki nie będą dostępne i możesz nadal jest naliczana dla dowolnego wykorzystany czas obliczeń platformy Azure.

Po rozpoczęciu tworzenie, możesz zamknąć aparatu Unity. W zależności od projektu, typ węzła i liczby węzłów tworzenie chmury może potrwać kilka godzin. Tworzenie stanu zadania zostaną zaktualizowane, gdy ponowne załadowanie projektu i Otwórz okno Akustyka. Jeśli zadanie zostało ukończone, plik wyjściowy zostanie pobrany.

Poświadczenia platformy Azure są bezpiecznie przechowywane na komputerze lokalnym i skojarzone z programu Unity editor. Służą one wyłącznie w celu nawiązania bezpiecznego połączenia z platformą Azure.

### <a name="Estimating-bake-cost"></a> Trwa szacowanie kosztu tworzenie platformy Azure

Aby oszacować, jaki będzie koszt danego tworzenie, wykonaj wartości wyświetlane dla **szacowany koszt obliczeń**, który jest czasem trwania i wielokrotnie kosztów, przez co godzinę w lokalnej walucie z **typ węzła maszyny Wirtualnej** wybrane. Wynik nie będzie również obejmował czas węzła potrzebne do uruchomienia w węzłach i uruchamiania. Na przykład w przypadku wybrania **Standard_F8s_v2** typu węzła, który ma koszt 0,40 USD/godz., a szacowany koszt obliczeń jest 3 godzin i 57 minut, szacowany koszt będzie $0,40 * ~ 4 godziny = ~ 1,60 USD. Rzeczywisty koszt będzie prawdopodobnie nieco wyższy ze względu na dodatkowy czas, aby uzyskać dostęp do węzłów pracę. Można znaleźć węzła co godzinę, koszt na [cennik usługi Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) strony (wybierz opcję "zoptymalizowane pod kątem obliczeń" lub "obliczenia o wysokiej wydajności" dla kategorii).

### <a name="reviewing-the-bake-results"></a>Sprawdzanie wyników tworzenie

Po zakończeniu tworzenie, sprawdź, czy punkty voxels i badania znajdują się w ich oczekiwanych lokalizacjach za pomocą wtyczki środowiska uruchomieniowego. Więcej informacji znajduje się w [Omówienie procesu projektowania dla Akustyka](design-process.md).

## <a name="Local-bake"></a>Tworzenie lokalnego
Tworzenie lokalnej działa symulacji Akustyka komputera zamiast przeniesieniu ich do klastra obliczeniowego usługi Azure Batch. Może to być to dobry wybór do eksperymentowania z Akustyka bez subskrypcji platformy Azure. Pamiętaj, że Symulacja Akustyka praktyce najbardziej wymagających może zająć dużo czasu w zależności od rozmiaru sceny, symulacja konfiguracji i raw mocy obliczeniowej maszyny przetwarzania.

### <a name="minimum-hardware-requirements"></a>Minimalne wymagania sprzętowe
64-bitowy procesor Intel z co najmniej 8 rdzeni i 32 GB pamięci RAM lub nowszej.

Na przykład na komputerze przy użyciu Intel Xeon E5-1660 @ 3 GHz oraz 32 GB pamięci RAM — 8 rdzeni
* Niewielkiej sceny przy użyciu 100 sondy zajmuje OK. 2 dla zdalnego tworzenie i około 32 godziny na tworzenie rozwiązania dobrym rozwiązaniem.
* Większe sceny za pomocą sondy 1000 może potrwać maksymalnie ~ 20 godzin zgrubnym rozwiązanie problemu i ~ 21 dni tworzenie rozwiązania dobrym rozwiązaniem.

### <a name="setup-docker"></a>Konfigurowanie platformy Docker
Instalowanie i konfigurowanie platformy Docker na komputerze, który będzie przetwarzał Symulacja-
1. Zainstaluj [zestawu narzędzi platformy Docker](https://www.docker.com/products/docker-desktop).
2. Uruchom ustawień platformy Docker, przejdź do opcji "Zaawansowany", a następnie konfiguruje zasoby, aby w przynajmniej 8 GB pamięci RAM. Więcej procesorów można przydzielić do platformy Docker, tym szybsze tworzenie zostanie ukończone. ![Przykład ustawień platformy Docker](media/DockerSettings.png)
3. Przejdź do "Udostępnione dyski" i Włącz udostępnianie dla dysku używana na potrzeby przetwarzania.![DockerDriveSharing](media/DockerSharedDrives.png)

### <a name="run-local-bake"></a>Tworzenie lokalnego uruchomienia
1. Kliknij przycisk "Przygotowanie lokalnego tworzenie" na karcie Tworzenie, a następnie wybierz folder, w którym zostaną zapisane pliki wejściowe i wykonywania skryptów. Następnie można uruchomić tworzenie na dowolnym komputerze, tak długo, jak spełnia minimalne wymagania sprzętowe i ma platforma Docker jest zainstalowana przez skopiowanie folderu do tej maszyny.
2. Uruchomienie symulacji za pomocą skryptu "runlocalbake.bat". Ten skrypt spowoduje pobranie obrazu platformy Docker Akustyka projektu z zestawem narzędzi niezbędnych do symulacji przetwarzania i rozpocząć symulację. 
3. Po zakończeniu symulacji skopiuj wynikowy plik .ace, wróć do projektu środowiska Unity. Aby upewnić się, że Unity rozpoznaje to jako plik binarny, Dołącz ".bytes" z rozszerzeniem pliku (na przykład "Scene1.ace.bytes"). Szczegółowe dzienniki symulacji są przechowywane w "AcousticsLog.txt." Jeśli napotkasz problemy, należy udostępnić ten plik w celu ułatwienia diagnozy.

## <a name="Data-Files"></a>Pliki danych

Istnieją cztery pliki danych utworzonych przez ten dodatek w różnych punktach. Wymagane jest tylko jeden z nich w czasie wykonywania, w związku z tym pozostałych trzech znajdują się wewnątrz foldery o nazwie "Editor", dzięki czemu nie będzie można skompilować do projektu.

* **Zasobów/Edytor / [SceneName]\_AcousticsParameters.asset**: ten plik przechowuje dane, możesz wprowadzić w polach w Interfejsie użytkownika Akustyka. Nie można zmienić lokalizację i nazwę tego pliku. Istnieją inne wartości przechowywane w tym pliku, które wpływają na tworzenie, ale są dla użytkowników zaawansowanych i nie powinna być zmieniana.
* **Zasobów/AcousticsData/Akustyka\_.ace.bytes [SceneName]**: ten plik jest jest tworzony podczas symulacji tworzenie i zawiera dane wyszukiwania używany przez środowisko uruchomieniowe do renderowania Akustyka swoje sceny. Lokalizację i nazwę tego pliku można zmienić przy użyciu pól na **sondy** kartę.
* **Assets/AcousticsData/Editor/Acoustics_[SceneName].vox**: ten plik przechowuje geometrii Akustyka voxelized i właściwości materiału. Obliczane przy użyciu **Calculate...**  przycisku na karcie sondy. Lokalizację i nazwę tego pliku można zmienić przy użyciu pól na **sondy** kartę.
* **Zasobów/AcousticsData/Edytor/Akustyka\_[SceneName]\_pliku config.xml**: ten plik przechowuje obliczane przy użyciu parametrów **Calculate...**  znajdujący się na **sondy** kartę. Lokalizację i nazwę tego pliku można zmienić przy użyciu pól na **sondy** kartę.

Uważaj, aby usunąć *. plik ace.bytes pobrany z tworzenie. Ten plik nie jest możliwe do odzyskania, z wyjątkiem przez rebaking sceny.

## <a name="next-steps"></a>Kolejne kroki
* Zastosuj tworzenie wyniki do źródła dźwięku w [Omówienie procesu projektowania dla Akustyka](design-process.md).

