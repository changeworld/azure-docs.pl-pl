---
title: Samouczek tworzenie Unity Akustyka projektu
titlesuffix: Azure Cognitive Services
description: W tym samouczku opisano Akustyka pieczenie przy użyciu Akustyka projektu na platformie Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 2f0fcdcdf781c86179b67eeef0223d46da0fc65b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917004"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Samouczek tworzenie Unity Akustyka projektu
W tym samouczku opisano Akustyka pieczenie przy użyciu Akustyka projektu na platformie Unity.

Wymagania dotyczące oprogramowania:
* [Unity 2018.2 +](https://unity3d.com) dla Windows
* [Wtyczka Akustyka projektu zintegrowane w swoim projekcie aparatu Unity](unity-integration.md) lub [Unity Akustyka projektu przykładowej zawartości](unity-quickstart.md)
* Opcjonalnie: [Konta usługi Azure Batch](create-azure-account.md) aby przyspieszyć tworzony przy użyciu chmury obliczeniowej

## <a name="open-the-project-acoustics-bake-window"></a>Otwórz okno Akustyka projektu tworzenie
Wybierz **okna > Akustyka** menu aparatu Unity:

![Zrzut ekranu programu Unity editor z podświetloną opcją menu okna Akustyka](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Tworzenie siatki nawigacji
Akustyka projekt używa siatki nawigacji można umieścić punkty sondy odbiornika symulacji. Można użyć mechanizmu Unity [nawigacyjne siatki w przepływie pracy](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html), lub użyj innego pakietu modelowanie 3D projektować własne siatki. 

## <a name="mark-acoustic-scene-objects"></a>Oznacz obiekty sceny akustycznych
Projekt Akustyka opiera się na dwa rodzaje obiekty sceny symulacji: obiekty, które będą odzwierciedlać i occlude dźwięk w symulacji i siatki nawigacji odtwarzacza, ograniczające odbiornika sondy punktów w symulacji. Oba typy obiektów są oznaczane za pomocą **obiektów** kartę. 

Ponieważ oznaczania obiektów po prostu dodaje **AcousticsGeometry** lub **AcousticsNavigation** składników do obiektu, można również użyć [standardowa Unity workflow składnika](https://docs.unity3d.com/Manual/UsingComponents.html)zaznaczyć lub odznaczyć obiektów. Może być oznaczony tylko renderowania siatki i terenach. Inne typy obiektów, zostaną zignorowane. Zaznacz pola wyboru będą zaznacz lub usuń zaznaczenie wszystkich obiektów dotkniętych wadami.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Oznacz akustyczny geometrii zamknięcia i odbicie
Otwórz **obiektów** karcie **Akustyka** okna. Oznacz wszystkie obiekty jako **geometrii Akustyka** one powinien occlude, odzwierciedlają, czy wchłonąć dźwięku. Geometria Akustyka może zawierać np podstaw, ściany, dachy, systemu windows i szkła okna, kilimy i meble dużych. Dla tych obiektów, można użyć dowolnego dowolnego poziomu złożoności. Ponieważ sceny voxelized przed symulacji, bardzo szczegółowe siatki, takie jak drzewa z wielu małych liści, nie jest droższy wprowadzić niż uproszczone obiekty.

Nie dołączaj rzeczy, które nie powinien wpływać na Akustyka, takich jak siatek kolizji niewidoczne.

Przekształcanie obiektu w czasie obliczania sondy (za pośrednictwem **sondy** karcie poniżej) zostanie rozwiązany w wynikach tworzenie. Przenoszenie wszystkich zaznaczonych obiektów w scenie będzie wymagać ponownego wykonywania obliczeń sondowania i rebaking sceny.

### <a name="mark-the-navigation-mesh"></a>Znak nawigacyjne siatki
Oczek nawigacji utworzonych za pomocą mechanizmu Unity przepływu pracy będą pobierane przez system Akustyka. Aby użyć własnego siatek, oznacz je z **obiektów** kartę.

### <a name="for-reference-the-objects-tab-parts"></a>Aby uzyskać informacje dotyczące: Części karty obiektów
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

Jeśli masz niczego nie wybrano w sceny kartę obiekty będzie wyglądać jak na poniższym obrazie:

![Zrzut ekranu Akustyka obiektów karty z Brak zaznaczenia](media/objects-tab-no-selection-detail.png)

Jeśli masz coś, co jest wybrane w oknie sceny lub hierarchię, będzie to wyglądać jak na poniższym obrazie:

![Zrzut ekranu z Akustyka kartę obiekty z wyborem pokazano](media/objects-tab-selection-detail.png)

Jeśli niektóre obiekty są oznaczone, a niektóre nie są odpowiednie pole wyboru wyświetli wartość "mieszany":

![Zrzut ekranu Akustyka obiektów karty z wyróżnioną ikoną Wybór mieszany](media/mixed-object-selection-detail.png)

Kliknięcie pola wyboru spowoduje to wymuszenie wszystkie obiekty był oznaczony jako, a następnie ponownie klikając spowoduje usunięcie zaznaczenia wszystkich obiektów.

Obiekty mogą być zaznaczone dla geometrii i nawigacji.

## <a name="select-acoustic-materials"></a>Wybierz materiały akustycznych
Gdy obiekty są oznaczone, kliknij przycisk **materiałów** przycisk i przypisz akustyczny materiałów. System materiałów Akustyka projektu jest powiązane z systemem visual materiałów aparatu Unity: dla dwóch obiektów mieć osobne akustyczny materiałów, musi mieć osobne materiałów visual.

Akustyczny materiałów kontroli ilości energii dźwięku odzwierciedlone powrót po awarii z każdej powierzchni. Domyślny materiał akustyczny ma absorpcji, podobnie jak konkretny. Projekt Akustyka sugeruje materiałów, na podstawie nazwy materiału visual. Materiał akustyczny "Custom" można przypisać do materiału umożliwiające absorpcji współczynnik suwaka.

Czas reverberation danego materiału w pomieszczeniu odwrotnie jest powiązana z jego współczynnik, materiałami większość posiada absorpcji wartości z zakresu od 0,01 do 0,20. Materiały z współczynniki absorpcji poza tym zakresem są bardzo pochłaniający.

![Wykres przedstawiający korelacja ujemna reverberation czasu współczynnik](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Aby uzyskać informacje dotyczące: Elementy na karcie materiały
![Zrzut ekranu Akustyka materiałów kartę na platformie Unity](media/materials-tab-detail.png)

1. **Materiałów** kartę przycisku używane do tej strony.
2. Krótki opis co należy zrobić, korzystając z tej strony.
3. Po zaznaczeniu tej opcji tylko użytych przez obiekty oznaczone jako **geometrii Akustyka** zostaną wyświetlone. W przeciwnym razie zostaną wyświetlone wszystkie materiały, używany w scenie.
4. Użyj tych opcji, aby zmienić kolejność menu rozwijanego, która jest wyświetlana, gdy przycisk listy rozwijanej w kolumnie Akustyka poniżej (6). **Nazwa** sortuje akustyczny materiałów według nazwy. "Absorptivity" posortowane w kolejności absorptivity od niskiego na wysoki.
5. Lista użytych w scenie, sortowana alfabetycznie. Jeśli **Pokaż tylko zaznaczone** pole wyboru jest zaznaczone (3), tylko użytych przez obiekty oznaczone jako **geometrii Akustyka** są wyświetlane. Klikając tutaj materiał zaznaczyć wszystkie obiekty w scenie, używanego przez tego materiału.
6. Zawiera materiały akustyczny powierzonych materiału w scenie. Kliknij listę rozwijaną, aby ponownie przypisać materiał sceny do innego materiału akustyczny. Możesz zmienić kolejność sortowania menu wyświetlane po kliknięciu elementu w tym miejscu przy użyciu **Akustyka Sortuj według:** opcje (# 4).
7. Pokazuje akustyczny współczynnik materiał wybraną w poprzednim kolumnę. Wartość zero oznacza, że dokładnie odzwierciedlają (nie absorpcji) podczas wartość 1 oznacza, że doskonale pochłaniającym (bez odbicia). Nie można zmienić współczynnik absorpcji, chyba że wybrany materiał "Niestandardowe".
8. Materiał przypisane na "Niestandardowe", suwak już jest wyłączona i można wybrać współczynnik absorpcji za pomocą suwaka lub wpisując wartość.

## <a name="calculate-and-review-listener-probe-locations"></a>Obliczanie i przejrzyj lokalizacji funkcji badania odbiornika
Po przypisaniu materiałów, przełącz się do **sondy** kartę, a następnie kliknij przycisk **Calculate** umieścić punkty sondy odbiornika symulacji.

### <a name="what-the-calculate-button-calculates"></a>Oblicza przycisku "Oblicz..."
**Obliczania...**  przycisk używa Twojego geometrii wybranego akustyczny sceny przygotować sceny symulacji:

1. On przyjmuje geometrii z siatek sceny i oblicza voxel woluminu. Voxel wolumin jest 3-wymiarowej, która obejmuje całą sceny, składa się z małych trzeciego stopnia "voxels". Rozmiar voxels jest określana przez częstotliwość symulacji, która została ustawiona przez **rozpoznawania symulacji** ustawienie. Każdy voxel jest oznaczony jako opcję "Otwórz udziału użytkownika" lub zawierających geometrii sceny. Jeśli voxel zawiera geometrii voxel zostanie oznaczony za pomocą współczynnik przypisanych do tej geometrii materiałów.
2. Aby umieścić punkty sondy odbiornika używa mesh(es) nawigacji. Algorytm równoważy konkurencyjnych wątpliwości przestrzenne pokrycia i symulacje inżynierskie czasu i rozmiaru pliku, przy jednoczesnym zapewnieniu, które zawęzić korytarzach i małych miejsca do magazynowania stały pewien stopień pokrycia. Typowe sondy punktu liczby z zakresu od 100 dla małych scen do kilku tysięcy dla dużych scen.

W zależności od rozmiaru sceny i szybkość maszyny tych obliczeń może potrwać kilka minut.

### <a name="review-voxel-and-probe-placement"></a>Przegląd voxel i badania umieszczania
Wyświetl podgląd danych voxel i lokalizacje punktu sondowania, aby upewnić się, że wszystko jest gotowe do tworzenie sceny. Niekompletne nawigacyjne siatki lub brakujące lub bardzo akustyczny geometrii będzie zazwyczaj szybko widoczne w wersji zapoznawczej. Umieszczanie Voxel i badania można włączyć lub wyłączyć za pomocą menu Gizmo:

![Zrzut ekranu Gizmo menu na platformie Unity](media/gizmos-menu.png)

Voxels zawierający akustyczny geometrii są wyświetlane jako zielony modułów. Eksplorować Scena i sprawdź, czy ma wszystko, co powinno być geometrii voxels. Aparat sceny musi można w ciągu około 5 liczniki obiektu dla voxels do wyświetlenia.

Możesz porównać voxels utworzone przy użyciu rozpoznawania poprawnie vs zgrubnym rozwiązania, pojawi się zgrubnym voxels dwukrotnie są tak duże.

![Zrzut ekranu przedstawiający zgrubnym voxels (wersja zapoznawcza) w programie Unity editor](media/voxel-cubes-preview.png)

Symulacja wyniki są interpolowane między odbiornika sondy punktu w czasie wykonywania. Sprawdź istnieją sondy punkty niemal dowolnym miejscu odtwarzacz oczekuje się przechodzić w scenie.

![Zrzut ekranu przedstawiający sondy (wersja zapoznawcza) w programie Unity editor](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Zadbać o zmienia nazwę sceny
Nazwa sceny jest używana do sceny łączenie się z plikami przechowywania położenie punktu sondowania i voxelization. Jeśli sceny zostanie zmieniona po sondy punkty są obliczane, materiału przypisania i położenia danych zostaną utracone i wymaga ponownego uruchomienia.

### <a name="for-reference-parts-of-the-probes-tab"></a>Aby uzyskać informacje dotyczące: Elementy na karcie sondy
![Zrzut ekranu sondy Akustyka kartę na platformie Unity](media/probes-tab-detail.png)

1. **Sondy** przycisk karta umożliwia wyświetlenie na tej stronie
2. Krótki opis co należy zrobić, korzystając z tej strony
3. Użyj tych, aby wybrać rozdzielczość symulacji zgrubnym lub dobrym rozwiązaniem. Duże jest szybsze, ale ma pewne wady i zalety. Zobacz [tworzenie rozwiązania](bake-resolution.md) poniżej szczegółowe informacje.
4. Wybierz lokalizację, w których można umieścić pliki danych Akustyka za pomocą tego pola. Kliknij przycisk "...", aby użyć selektora folderów. Wartość domyślna to **zasobów/AcousticsData**. **Edytora** podfolder zostanie również utworzony w tej lokalizacji. Aby uzyskać więcej informacji na temat plików danych, zobacz [pliki danych](#Data-Files) poniżej.
5. Pliki danych dla tego sceny będzie miała przy użyciu prefiksu podane w tym miejscu. Wartość domyślna to "Acoustics_ [Nazwa sceny]".
6. Po sondy zostały obliczone z kontrolki powyżej zostanie wyłączona. Kliknij przycisk **wyczyść** przycisk, aby wymazać obliczeń i włączyć formanty, tak, aby ponownie obliczyć przy użyciu nowych ustawień.
7. Kliknij przycisk **obliczania...**  znajdujący się voxelize sceny i obliczyć lokalizacji punktu sondowania. Odbywa się lokalnie na urządzeniu i należy wykonać przed sposób tworzenie.

W tej wersji projektu Akustyka sondy nie może znajdować się ręcznie i muszą znajdować się za pomocą zautomatyzowanego procesu w **sondy** kartę.

Zobacz [tworzenie rozwiązania](bake-resolution.md) więcej informacji na temat zdalnego vs poprawnie rozdzielczości.

## <a name="bake-your-scene-using-azure-batch"></a>Tworzenie sceny za pomocą usługi Azure Batch
Można wprowadzić sceny z klastra obliczeniowego w chmurze przy użyciu usługi Azure Batch. Wtyczka Unity Akustyka projekt łączy się bezpośrednio z usługi Azure Batch do utworzenia wystąpienia, zarządzanie i zatrzymywania klastra usługi Azure Batch dla każdego tworzenie. Na **tworzenie** kartę, wprowadź swoje poświadczenia platformy Azure, wybierz typ komputera klastra i rozmiar i kliknij przycisk **tworzenie**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Aby uzyskać informacje dotyczące: Elementy na karcie Tworzenie
![Zrzut ekranu tworzenie Akustyka kartę na platformie Unity](media/bake-tab-details.png)

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
14. Kliknij przycisk **tworzenie** przycisk, aby przesłać tworzenie do chmury. Gdy zadanie jest uruchomione, spowoduje to pokazanie **anulować zadanie** zamiast tego.
15. Przygotowanie do przetwarzania [Akustyka symulacji na komputerze PC](#Local-bake).
16. Ten obszar zawiera stan tworzenie. Po zakończeniu powinien być wyświetlony **pobrane**.

Można zawsze uzyskać pełne informacje na temat aktywnych zadań, pule obliczeniowe i Magazyn w [witryny Azure portal](https://portal.azure.com).

Gdy zadanie jest uruchomione **tworzenie** przycisku zmienia się na **anulować zadanie**. Użyj tego przycisku można anulować zadania w toku. Użytkownik jest proszony o potwierdzenie przed anulowaniem zadania. Trwa anulowanie zadania nie można cofnąć, żadne wyniki nie będą dostępne i możesz nadal jest naliczana dla dowolnego wykorzystany czas obliczeń platformy Azure.

Po rozpoczęciu tworzenie, możesz zamknąć aparatu Unity. W zależności od projektu, typ węzła i liczby węzłów tworzenie chmury może potrwać kilka godzin. Tworzenie stanu zadania zostaną zaktualizowane, gdy ponowne załadowanie projektu i Otwórz okno Akustyka. Jeśli zadanie zostało ukończone, plik wyjściowy zostanie pobrany.

Poświadczenia platformy Azure są bezpiecznie przechowywane na komputerze lokalnym i skojarzone z programu Unity editor. Służą one wyłącznie w celu nawiązania bezpiecznego połączenia z platformą Azure.

### <a name="Estimating-bake-cost"></a> Trwa szacowanie kosztu tworzenie platformy Azure

Aby oszacować, jaki będzie koszt danego tworzenie, wykonaj wartości wyświetlane dla **szacowany koszt obliczeń**, który jest czasem trwania i wielokrotnie kosztów, przez co godzinę w lokalnej walucie z **typ węzła maszyny Wirtualnej** wybrane. Wynik nie będzie również obejmował czas węzła potrzebne do uruchomienia w węzłach i uruchamiania. Na przykład w przypadku wybrania **Standard_F8s_v2** typu węzła, który ma koszt 0,40 USD/godz., a szacowany koszt obliczeń jest 3 godzin i 57 minut, szacowany koszt będzie $0,40 * ~ 4 godziny = ~ 1,60 USD. Rzeczywisty koszt będzie prawdopodobnie nieco wyższy ze względu na dodatkowy czas, aby uzyskać dostęp do węzłów pracę. Można znaleźć węzła co godzinę, koszt na [cennik usługi Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) strony (wybierz opcję "zoptymalizowane pod kątem obliczeń" lub "obliczenia o wysokiej wydajności" dla kategorii).

## <a name="Local-bake"></a> Tworzenie sceny na komputerze z systemem
Na komputerze z własnych, można wprowadzić sceny. Może to być przydatne do eksperymentowania z Akustyka przy użyciu małych sceny przed utworzeniem konta usługi Azure Batch. Uwaga symulacji Akustyka może zająć dużo czasu w zależności od rozmiaru sceny.

### <a name="minimum-hardware-requirements"></a>Minimalne wymagania sprzętowe
* Procesor x86 64 z co najmniej 8 rdzeni i 32 GB pamięci RAM

Na przykład podczas testów na komputerze o architekturze Intel Xeon E5-1660 8-rdzeniowe @ 3 GHz i 32 GB pamięci RAM —
* Niewielkiej sceny przy użyciu 100 sond może potrwać około 2 godziny zgrubnym Tworzenie lub 32 godziny na tworzenie dobrym rozwiązaniem.
* Średnie sceny z 1000 sond może potrwać około 20 godzin zgrubnym Tworzenie lub 21 dni tworzenie dobrym rozwiązaniem.

### <a name="setup-docker"></a>Setup Docker
Instalowanie i konfigurowanie platformy Docker na komputerze, który będzie przetwarzał Symulacja-
1. Zainstaluj [zestawu narzędzi platformy Docker](https://www.docker.com/products/docker-desktop).
2. Uruchom ustawień platformy Docker, przejdź do opcji "Zaawansowane", a następnie konfiguruje zasoby, które mają co najmniej 8GB pamięci RAM. Więcej procesorów można przydzielić do platformy Docker, tym szybsze tworzenie zostanie ukończone. ![Zrzut ekranu przedstawiający przykładowy ustawień platformy Docker](media/docker-settings.png)
3. Przejdź do "Udostępnione dyski" i Włącz udostępnianie dla dysku używana na potrzeby przetwarzania.![Zrzut ekranu z opcje platformy Docker udostępnionego dysku](media/docker-shared-drives.png)

### <a name="run-local-bake"></a>Tworzenie lokalnego uruchomienia
1. Kliknij przycisk "Przygotowanie lokalnego tworzenie" **tworzenie** karcie, a następnie wybierz folder, w którym zostaną zapisane pliki wejściowe i wykonywania skryptów. Następnie można uruchomić tworzenie na dowolnym komputerze, tak długo, jak spełnia minimalne wymagania sprzętowe i ma platforma Docker jest zainstalowana przez skopiowanie folderu do tej maszyny.
2. Uruchomienie symulacji za pomocą skryptu "runlocalbake.bat". Ten skrypt spowoduje pobranie obrazu platformy Docker Akustyka projektu z zestawem narzędzi niezbędnych do symulacji przetwarzania i rozpocząć symulację. 
3. Po zakończeniu symulacji skopiuj wynikowy plik .ace, wróć do projektu środowiska Unity. Aby upewnić się, że Unity rozpoznaje to jako plik binarny, Dołącz ".bytes" z rozszerzeniem pliku (na przykład "Scene1.ace.bytes"). Szczegółowe dzienniki symulacji są przechowywane w "AcousticsLog.txt." Jeśli napotkasz problemy, należy udostępnić ten plik w celu ułatwienia diagnozy.

## <a name="Data-Files"></a> Pliki dodane przez proces tworzenie

Istnieją cztery pliki danych tworzone podczas procesu tworzenie. Jedna zawiera wyniki symulacji i jest dostarczany z tytułu. Pozostałe przechowywać dane dotyczące przez Edytor platformy Unity.

Wynik symulacji:
* **Zasobów/AcousticsData/Akustyka\_.ace.bytes [SceneName]**: Jest tabeli odnośników czasu wykonywania, zawiera wyniki symulacji i voxelized elementy akustyczny sceny. Lokalizację i nazwę tego pliku można zmienić przy użyciu pól na **sondy** kartę.

Należy zadbać, nie, aby usunąć plik wyniki symulacji. Nie jest możliwe do odzyskania, z wyjątkiem przez rebaking sceny.

Edytor plików danych:
* **Zasobów/Edytor / [SceneName]\_AcousticsParameters.asset**: Ten plik przechowuje dane, które możesz wprowadzić w polach w Interfejsie użytkownika Akustyka. Nie można zmienić lokalizację i nazwę tego pliku.
* **Assets/AcousticsData/Editor/Acoustics_[SceneName].vox**: Ten plik przechowuje geometrii Akustyka voxelized i właściwości materiału, które są obliczane przy użyciu **Calculate...**  przycisku na karcie sondy. Lokalizację i nazwę tego pliku można zmienić przy użyciu pól na **sondy** kartę.
* **Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml**: Ten plik przechowuje parametry symulacji obliczane przy użyciu **Calculate...**  znajdujący się na **sondy** kartę. Lokalizację i nazwę tego pliku można zmienić przy użyciu pól na **sondy** kartę.

## <a name="set-up-the-acoustics-lookup-table"></a>Konfigurowanie Akustyka tabeli odnośników
Przeciąganie i upuszczanie **Akustyka projektu** prefab z panelu Projekt do sceny:

![Zrzut ekranu Akustyka prefab na platformie Unity](media/acoustics-prefab.png)

Kliknij pozycję **ProjectAcoustics** obiektu gry, a następnie przejść do jego panelu Inspektor. Określ lokalizację zawartości tworzenie wyników ("". ACE pliku w **zasobów/AcousticsData**) przez przeciąganie i upuszczanie go w skrypcie Menedżera Akustyka lub przez kliknięcie przycisku kółko obok pola tekstowego.

![Zrzut ekranu Menedżera Akustyka prefab na platformie Unity](media/acoustics-manager.png)  

## <a name="next-steps"></a>Kolejne kroki
* Zapoznaj się z [projektowania formanty dla aparatu Unity](unity-workflow.md)
* Zapoznaj się z [koncepcji projektów Akustyka projektu](design-process.md)

