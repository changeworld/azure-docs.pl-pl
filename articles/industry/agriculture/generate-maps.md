---
title: Generuj mapy
description: Opisuje sposób generowania map w FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 6cdebb8f6abea2b50dd8aad58ccc9fb5e680cba9
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73798481"
---
# <a name="generate-maps"></a>Generuj mapy

Za pomocą usługi Azure FarmBeats można generować następujące mapy przy użyciu obrazów satelitarnych i danych wejściowych czujników. Mapy pomagają w wyświetlaniu lokalizacji geograficznej farmy i identyfikacji odpowiedniej lokalizacji urządzeń.

  -  **Mapa położenia czujnika** — zawiera zalecenia dotyczące liczby czujników do użycia i miejsca umieszczenia ich w farmie.
  - **Mapa indeksów satelitarnych** — pokazuje indeks roślinności i indeks wody dla farmy.
  - **Mapa wilgotności gleby** — pokazuje rozkład wilgotności gleby przez odmowę danych satelitarnych i danych czujników.

## <a name="sensor-placement-maps"></a>Mapy umieszczania czujnika

Mapa położenia czujnika FarmBeatsa pomaga w rozmieszczeniu czujników wilgoci. Dane wyjściowe mapy składają się z listy współrzędnych wdrożenia czujnika. Dane wejściowe z tych czujników są używane razem z obrazami satelitarnymi w celu wygenerowania wilgotności gleby mapę cieplną.  

Ta mapa jest tworzona przez segmentację korony, która jest widoczna w wielu datach w roku, nawet bez systemu operacyjnego, a budynki są częścią korony. Można usunąć czujniki, które nie są wymagane w lokalizacji. Ta mapa jest zgodna ze wskazówkami i można zmienić pozycje i liczby nieco w zależności od niestandardowej wiedzy (dodanie czujników nie spowoduje odłożenia wyników mapy cieplnej wilgotności gleby, ale istnieje możliwość pogorszenia dokładności mapy cieplnej w przypadku zmniejszenia numeru czujnika).  

## <a name="before-you-begin"></a>Przed rozpoczęciem  

Przed podjęciem próby wygenerowania mapy umieszczenia czujnika wykonaj następujące czynności:

- Rozmiar farmy musi być większy niż jeden Acre.
- Liczba scen ze wskaźnikami wolnych od chmury musi być większa niż sześć dla wybranego zakresu dat.  
- Co najmniej sześć scen wskaźnikowych bez chmury musi mieć NDVI > = 0,3.

    > [!NOTE]
    > Wskaźnik kontrolny umożliwia tylko dwa współbieżne wątki na użytkownika. W związku z tym zadania będą umieszczane w kolejce i może trwać dłużej.

### <a name="dependencies-on-sentinel"></a>Zależności dotyczące wskaźnikowego  

Poniżej znajdują się zależności wskaźnikowe:

- W przypadku pobierania obrazów satelitarnych zależą od wydajności wskaźnikowej. Sprawdź stan wydajności wskaźnikowej i [działania](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)konserwacyjne.
- Wskaźnik kontrolny umożliwia tylko dwa współbieżnie [pobierane wątki](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) na użytkownika.
- Na generowanie mapowania dokładności będzie miało wpływ [pokrycie wskaźnikiem i częstotliwość ponownego odwiedzania]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage).

## <a name="create-sensor-placement-map"></a>Utwórz mapę położenia czujnika
Sekcja zawiera szczegółowe informacje na temat procedur tworzenia map umieszczania czujnika.

> [!NOTE]
> Położenie czujnika można zainicjować ze strony **Maps** lub z menu rozwijanego **Generuj mapy dokładności** na stronie **szczegółów farmy** .

Wykonaj następujące czynności:

1. Na stronie głównej przejdź do pozycji **mapy** w menu nawigacji po lewej stronie.
2. Wybierz pozycję **Utwórz mapy** i wybierz pozycję **położenie czujnika** z menu rozwijanego.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Wybierz pozycję **położenie czujnika**.
  Zostanie wyświetlone okno umieszczania czujnika.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Wybierz farmę z menu rozwijanego **farmy** .  
   Aby wyszukać i wybrać farmę, można albo przewinąć listę rozwijaną, albo wpisać nazwę farmy w polu tekstowym.
5. Jeśli chcesz wygenerować mapę dla ostatniego roku, wybierz opcję **zalecana** opcja.
6. Jeśli chcesz wygenerować mapę dla niestandardowego zakresu dat, wybierz pozycję **Wybierz zakres dat**i podaj datę początkową i końcową, dla której chcesz wygenerować mapę umieszczania czujnika.
7. Wybierz pozycję **Generuj mapy**.
 Zostanie wyświetlony komunikat z potwierdzeniem zawierającym szczegóły zadania.

  Informacje o stanie zadania znajdują się w sekcji **Wyświetlanie zadań**. Jeśli stan zadania *nie powiodło się*, w etykietce narzędzia stanu *Niepowodzenie* zostanie wyświetlony szczegółowy komunikat o błędzie. W takim przypadku Powtórz powyższe kroki i spróbuj ponownie.

  Jeśli problem będzie się powtarzać, zobacz sekcję [Rozwiązywanie problemów](troubleshoot-project-farmbeats.md) lub skontaktuj się z [Forum usługi Azure FarmBeats, aby uzyskać pomoc techniczną](https://aka.ms/FarmBeatsMSDN) dotyczącą odpowiednich dzienników.

### <a name="view-and-download-sensor-placement-map"></a>Wyświetlanie i pobieranie mapy umieszczenia czujnika

Wykonaj następujące czynności:

1. Na stronie głównej przejdź do pozycji **mapy** w menu nawigacji po lewej stronie.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Wybierz opcję **Filtr** w lewym okienku nawigacji okna.
  W oknie **filtru** są wyświetlane kryteria wyszukiwania.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Z menu rozwijanego wybierz **Typ**, **datę** i **nazwę** , a następnie wybierz pozycję **Zastosuj** do wyszukiwania mapy, którą chcesz wyświetlić.
  Data utworzenia zadania jest pokazana w formacie type_farmname_YYYY-MM-DD.
4. Przewiń listę dostępnych map przy użyciu pasków nawigacyjnych znajdujących się na końcu strony.
5. Wybierz mapę, którą chcesz wyświetlić. W oknie podręcznym zostanie wyświetlona wersja zapoznawcza wybranej mapy.
6. Wybierz pozycję **Pobierz**, a następnie Pobierz plik GEOJSON współrzędnych czujnika.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Mapa indeksów satelitarnych

W poniższych sekcjach opisano procedury dotyczące tworzenia i wyświetlania mapy indeksów satelitarnych.

> [!NOTE]
> Możesz inicjować mapowanie indeksów satelitarnych ze strony **Maps** lub z menu rozwijanego **Generuj mapy dokładności** na stronie **szczegółów farmy** .

FarmBeats umożliwia generowanie znormalizowanych indeksów (NDVI), ulepszonego indeksu wegetacyjnego (EVI) i znormalizowanych różnic w indeksie wodnych (NDWI) dla Farm. Te indeksy pomagają określić, w jaki sposób uprawa jest obecnie zwiększana, lub w przeszłości, a reprezentatywne poziomy wody w ziemi.


> [!NOTE]
> Obraz kontrolny jest wymagany w dniach, dla których jest generowana mapa.


## <a name="create-satellite-indices-map"></a>Utwórz mapę indeksów satelitarnych

Wykonaj następujące czynności:

1. Na stronie głównej przejdź do pozycji **mapy** w menu nawigacji po lewej stronie.
2. Wybierz pozycję **Utwórz mapy** i wybierz z menu rozwijanego opcję **indeksy satelitarne** .

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Wybierz opcję **indeksy satelitarne**.
  Zostanie wyświetlone okno indeksy satelitarne.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Wybierz farmę z menu rozwijanego.  
   Aby wyszukać i wybrać farmę, można albo przewinąć listę rozwijaną, albo wpisać nazwę farmy.   
5. Jeśli chcesz wygenerować mapę dla ostatniego tygodnia, wybierz opcję **ten tydzień** .
6. Jeśli chcesz wygenerować mapę dla niestandardowego zakresu dat, wybierz pozycję **Wybierz zakres dat**i podaj datę początkową i końcową, dla której chcesz wygenerować mapę indeksów satelitarnych.
7. Wybierz pozycję **Generuj mapy**.
    Zostanie wyświetlony komunikat z potwierdzeniem zawierającym szczegóły zadania.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Informacje o stanie zadania znajdują się w sekcji **Wyświetlanie zadań**. Jeśli stan zadania *nie powiodło się*, w etykietce narzędzia stanu *Niepowodzenie* zostanie wyświetlony szczegółowy komunikat o błędzie. W takim przypadku Powtórz powyższe kroki i spróbuj ponownie.

    Jeśli problem będzie się powtarzać, zobacz sekcję [Rozwiązywanie problemów](troubleshoot-project-farmbeats.md) lub skontaktuj się z [Forum usługi Azure FarmBeats, aby uzyskać pomoc techniczną](https://aka.ms/FarmBeatsMSDN) dotyczącą odpowiednich dzienników.

### <a name="view-and-download-map"></a>Wyświetlanie i pobieranie mapy

Wykonaj następujące czynności:

1. Na stronie głównej przejdź do pozycji **mapy** w menu nawigacji po lewej stronie.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Wybierz pozycję **Filtruj** w lewym okienku nawigacji okna, w oknie **Filtr** wyświetlane są kryteria wyszukiwania.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Z menu rozwijanego wybierz **Typ**, **datę** i **nazwę** , a następnie wybierz pozycję **Zastosuj** do wyszukiwania mapy, którą chcesz wyświetlić.
  Data utworzenia zadania jest pokazana w formacie type_farmname_YYYY-MM-DD.

4. Przewiń listę dostępnych map przy użyciu pasków nawigacyjnych znajdujących się na końcu strony.
5. Dla każdej kombinacji nazwy i **daty** **farmy** dostępne są następujące trzy mapy:
    - NDVI
    - EVI
    - NDWI
6. Wybierz mapę, którą chcesz wyświetlić. W oknie podręcznym zostanie wyświetlona wersja zapoznawcza wybranej mapy.
7. Wybierz pozycję **Pobierz** menu rozwijane, aby wybrać format pobierania, a mapa zostanie pobrana i zapisana w folderze lokalnym na komputerze.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="get-soil-moisture-heatmap"></a>Pobierz wilgotność gleby mapę cieplną

Wilgotność gleby jest wodą, która znajduje się w odstępach między cząstkami glebowymi. Wilgotność gleby mapę cieplną pomaga zrozumieć dane wilgoci w glebie na dowolnej głębokości, z wysoką rozdzielczością w obrębie Farm. Aby wygenerować dokładną i przydatną wilgotność gleby mapę cieplną, wymagana jest jednolite wdrożenie czujników, co powoduje, że wszystkie czujniki są z tego samego dostawcy. Różni dostawcy będą mieć różnice w sposobie mierzenia wilgoci w glebie wraz z różnicami w kalibracji. Mapę cieplną jest generowany z określoną głębokością przy użyciu czujników wdrożonych na tej głębokości.

### <a name="before-you-begin"></a>Przed rozpoczęciem  

Przed podjęciem próby wygenerowania wilgotności gleby mapę cieplną upewnij się, że:

- Należy wdrożyć co najmniej trzy czujniki wilgoci w glebie. Firma Microsoft zaleca, aby nie próbować utworzyć mapy wilgotności gleby przed wdrożeniem czujników i skojarzeniem ich z farmą.  
- Co najmniej jedna z chmurowych wolnych scen musi być dostępna dla ostatnich 120 dni, od dnia, dla którego zażądano mapy wilgoci w glebie.
- Co najmniej połowa czujników wdrożonych w farmie musi być w trybie online i zawierać dane przesyłane strumieniowo do centrum danych.
- Mapę cieplną powinny być generowane przy użyciu miar czujników z tego samego dostawcy.


## <a name="create-soil-moisture-heatmap"></a>Tworzenie mapę cieplną wilgoci

Wykonaj następujące czynności:

1. Na stronie głównej przejdź do pozycji **mapy** w menu nawigacji po lewej stronie, aby wyświetlić stronę mapy.
2. Wybierz pozycję **Utwórz mapy** i wybierz **wilgotność gleby** z menu rozwijanego.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Wybierz **wilgotność gleby**.
    Zostanie wyświetlone okno wilgotność gleby.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Wybierz farmę z menu rozwijanego **farmy** .  
   Aby wyszukać i wybrać farmę, możesz wykonać przewijanie z listy rozwijanej lub wpisać nazwę farmy w menu rozwijanym Wybieranie farmy.
5. W menu rozwijanym **Wybierz pomiar czujnika gleby** , Wybierz miarę czujnika wilgoci gleby (głębokość), dla którego chcesz wygenerować mapę.
Aby znaleźć miarę czujnika, przejdź do **czujników**, wybierz dowolny czujnik wilgoci glebowej. Następnie w sekcji **Właściwości czujnika** Użyj wartości w odniesieniu do **nazwy miary**
6. Jeśli chcesz wygenerować w **dniu dzisiejszym** lub w **tym tygodniu**, wybierz jedną z opcji.
7. Jeśli chcesz wygenerować dla niestandardowego zakresu dat, wybierz pozycję **Wybierz zakres dat**i podaj datę początkową i końcową, dla której chcesz wygenerować mapę wilgoci gleby.
8. Wybierz pozycję **Generuj mapy**.
 Zostanie wyświetlony komunikat z potwierdzeniem zawierającym szczegóły zadania.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Informacje o stanie zadania znajdują się w sekcji **Wyświetlanie zadań**. Jeśli stan zadania *nie powiodło się*, w etykietce narzędzia stanu *Niepowodzenie* zostanie wyświetlony szczegółowy komunikat o błędzie. W takim przypadku Powtórz powyższe kroki i spróbuj ponownie.

    Jeśli problem będzie się powtarzać, zobacz sekcję [Rozwiązywanie problemów](troubleshoot-project-farmbeats.md) lub skontaktuj się z [Forum usługi Azure FarmBeats, aby uzyskać pomoc techniczną](https://aka.ms/FarmBeatsMSDN) dotyczącą odpowiednich dzienników.

### <a name="view-and-download-map"></a>Wyświetlanie i pobieranie mapy

Wykonaj następujące czynności:

1. Na stronie głównej przejdź do pozycji **mapy** w menu nawigacji po lewej stronie.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Wybierz pozycję **Filtruj** w **lewym okienku nawigacji okna, w którym** można wyszukiwać mapy.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Z menu rozwijanego wybierz **Typ**, **datę** i **nazwę** , a następnie wybierz pozycję **Zastosuj** do wyszukiwania mapy, którą chcesz wyświetlić. Data utworzenia zadania jest pokazana w formacie type_farmname_YYYY-MM-DD.
4. Wybierz ikonę **sortowania** obok nagłówka tabeli, aby posortować dane zgodnie z farmą, datą, Data utworzenia, identyfikatorem zadania i typem zadania.
5. Przewiń listę dostępnych map przy użyciu przycisków nawigacji na końcu strony.
6. Wybierz mapę, którą chcesz wyświetlić. W oknie podręcznym zostanie wyświetlona wersja zapoznawcza wybranej mapy.
7. Wybierz pozycję **Pobierz** menu rozwijane, aby wybrać format pobierania, a mapa zostanie pobrana i zapisana w folderze lokalnym Twojego komputera.

    ![Farmy projektów](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
