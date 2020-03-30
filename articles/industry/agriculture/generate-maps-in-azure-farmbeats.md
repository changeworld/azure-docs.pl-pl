---
title: Generowanie map
description: W tym artykule opisano sposób generowania map w usłudze Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 92228c691c323bc0b9621dfc7413d86c5c2669e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271774"
---
# <a name="generate-maps"></a>Generowanie map

Za pomocą usługi Azure FarmBeats, można wygenerować następujące mapy przy użyciu obrazów satelitarnych i danych wejściowych czujnika. Mapy ułatwią sprawdzenie położenia geograficznego farmy i określenie odpowiedniej lokalizacji dla twoich urządzeń.

  - **Mapa umieszczania czujników**: Zawiera zalecenia dotyczące liczby czujników do użycia i miejsca umieszczenia ich w gospodarstwie.
  - **Mapa indeksów satelitarnych**: Pokazuje wskaźnik roślinności i wskaźnik wody dla gospodarstwa.
  - **Mapa ciepła wilgotności gleby**: Pokazuje rozkład wilgotności gleby poprzez łączenie danych satelitarnych i danych z czujników.

## <a name="sensor-placement-map"></a>Mapa rozmieszczenia czujników

Mapa umieszczenia czujnika FarmBeats pomaga w rozmieszczeniu czujników wilgotności gleby. Dane wyjściowe mapy składają się z listy współrzędnych do wdrożenia czujnika. Wejścia z tych czujników są używane wraz ze zdjęciami satelitarnymi do generowania mapy cieplnej wilgotności gleby.

Ta mapa jest uzyskiwane przez segmentacji czaszy, jak widać w wielu terminach w ciągu roku. Nawet goła gleba i budynki są częścią baldachimu. Można usunąć czujniki, które nie są wymagane w lokalizacji. Ta mapa jest dla wskazówek, i można zmienić położenie i numery nieco na podstawie wiedzy niestandardowej. Dodanie czujników nie cofnie wyników mapy cieplnej wilgotności gleby, ale istnieje możliwość pogorszenia dokładności mapy cieplnej, jeśli liczba czujników zostanie zmniejszona.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed podjęciem próby wygenerowania mapy umieszczania czujników spełnij następujące wymagania wstępne:

- Wielkość gospodarstwa musi wynosić więcej niż jeden akr.
- Liczba scen Sentinel bez chmury musi być większa niż sześć dla wybranego zakresu dat.
- Co najmniej sześć bezchmurnych scen Sentinel musi mieć znormalizowany wskaźnik roślinności różnicowej (NDVI) większy lub równy 0,3.

    > [!NOTE]
    > Sentinel zezwala tylko na dwa równoczesne wątki na użytkownika. W rezultacie zadania są umieszczane w kolejce i może potrwać dłużej.

### <a name="dependencies-on-sentinel"></a>Zależności od Sentinel

Następujące zależności odnoszą się do sentinel:

- Polegamy na wydajności Sentinel do pobierania zdjęć satelitarnych. Sprawdź stan wydajności sentinel i [działania](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)konserwacyjne .
- Sentinel zezwala tylko na dwa [równoczesne pliki do pobrania wątków](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) na użytkownika.
- Na generowanie mapy precyzyjnej ma wpływ [zasięg Sentinel i częstotliwość ponownego odwiedzania.]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)

## <a name="create-a-sensor-placement-map"></a>Tworzenie mapy umieszczania czujników
W tej sekcji opisano procedury tworzenia map umieszczania czujników.

> [!NOTE]
> Mapę umieszczenia czujników można zainicjować na stronie **Mapy** lub z menu rozwijanego **Generowanie map dokładności** na stronie Szczegóły **farmy.**

Wykonaj następujące kroki.

1. Na stronie głównej przejdź do **mapy** z lewego menu nawigacji.
2. Wybierz **polecenie Utwórz mapy**i wybierz pozycję Położenie **czujnika** z menu rozwijanego.

    ![Wybieranie rozmieszczenia czujników](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Po **wybraniu opcji Położenie czujnika**zostanie wyświetle się okno **Położenie czujnika.**

    ![Okno rozmieszczenia czujników](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Wybierz farmę z menu rozwijanego **Farm.**
   Aby wyszukać i wybrać farmę, możesz przewinąć listę rozwijaną lub wprowadzić nazwę farmy w polu tekstowym.
5. Aby wygenerować mapę na ostatni rok, wybierz **polecane**.
6. Aby wygenerować mapę niestandardowego zakresu dat, wybierz opcję **Wybierz zakres dat**. Wprowadź datę rozpoczęcia i zakończenia, dla której chcesz wygenerować mapę rozmieszczenia czujników.
7. Wybierz **pozycję Generuj mapy**.
 Zostanie wyświetlony komunikat potwierdzający ze szczegółami zadania.

  Aby uzyskać informacje o stanie zadania, zobacz sekcję **Wyświetlanie zadań**. Jeśli stan zadania jest wyświetlany *nie powiódł się,* w etykietce narzędzia stanu Niepowodzenie pojawi się *szczegółowy* komunikat o błędzie. W takim przypadku powtórz poprzednie kroki i spróbuj ponownie.

  Jeśli problem będzie się powtarzał, zobacz sekcję [Rozwiązywanie problemów](troubleshoot-azure-farmbeats.md) lub skontaktuj się z [forum Azure FarmBeats, aby uzyskać pomoc techniczną](https://aka.ms/FarmBeatsMSDN) z odpowiednimi dziennikami.

### <a name="view-and-download-a-sensor-placement-map"></a>Wyświetlanie i pobieranie mapy umieszczania czujników

Wykonaj następujące kroki.

1. Na stronie głównej przejdź do **mapy** z lewego menu nawigacji.

    ![Wybierz mapy z lewego menu nawigacyjnego](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Wybierz **filtr** z lewej strony nawigacji w oknie.
  W oknie **Filtr** są wyświetlane kryteria wyszukiwania.

    ![Okno filtru](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Z menu rozwijanego **wybierz pozycję Typ,** **Data**i **Nazwa.** Następnie wybierz pozycję **Zastosuj,** aby wyszukać mapę, którą chcesz wyświetlić.
  Data utworzenia zadania jest wyświetlana w formacie type_farmname_YYYY-MM-DD.
4. Przewiń listę map dostępnych za pomocą pasków nawigacyjnych na końcu strony.
5. Wybierz mapę, którą chcesz wyświetlić. W wyskakującym oknie zostanie wyświetlony podgląd wybranej mapy.
6. Wybierz **opcję Pobierz**i pobierz plik GeoJSON współrzędnych czujnika.

    ![Podgląd mapy umieszczenia czujnika](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Mapa indeksów satelitarnych

W poniższych sekcjach wyjaśniono procedury związane z tworzeniem i przeglądaniem mapy indeksów satelitarnych.

> [!NOTE]
> Mapę indeksów satelitarnych można zainicjować na stronie **Mapy** lub z menu rozwijanego **Generowanie map precyzyjnych** na stronie **Szczegóły farmy.**

FarmBeats zapewnia możliwość generowania map NDVI, Enhanced Vegetation Index (EVI) i Normalized Difference Water Index (NDWI) dla gospodarstw rolnych. Wskaźniki te pomagają określić, w jaki sposób uprawa obecnie rośnie lub rosła w przeszłości, a reprezentatywne poziomy wody w ziemi.


> [!NOTE]
> Obraz wartowniy jest wymagany dla dni, dla których mapa jest generowana.


## <a name="create-a-satellite-indices-map"></a>Tworzenie mapy indeksów satelitarnych

Wykonaj następujące kroki.

1. Na stronie głównej przejdź do **mapy** z lewego menu nawigacji.
2. Wybierz **polecenie Utwórz mapy**i wybierz z menu rozwijanego pozycję **Indeksy satelitarne.**

    ![Wybierz indeksy satelitarne z menu rozwijanego](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Po **wybraniu indeksów satelitarnych**pojawi się okno **Indeksy satelitarne.**

    ![Okno Indeksy satelitarne](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Wybierz farmę z menu rozwijanego.
   Aby wyszukać i wybrać farmę, możesz przewinąć listę rozwijaną lub wprowadzić nazwę farmy.   
5. Aby wygenerować mapę na ostatni tydzień, wybierz opcję **W tym tygodniu**.
6. Aby wygenerować mapę niestandardowego zakresu dat, wybierz opcję **Wybierz zakres dat**. Wprowadź datę rozpoczęcia i zakończenia, dla której chcesz wygenerować mapę indeksów satelitarnych.
7. Wybierz **pozycję Generuj mapy**.
    Zostanie wyświetlony komunikat potwierdzający ze szczegółami zadania.

    ![Komunikat potwierdzenia mapy indeksów satelitarnych](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Aby uzyskać informacje o stanie zadania, zobacz sekcję **Wyświetlanie zadań**. Jeśli stan zadania jest wyświetlany *nie powiódł się,* w etykietce narzędzia stanu Niepowodzenie pojawi się *szczegółowy* komunikat o błędzie. W takim przypadku powtórz poprzednie kroki i spróbuj ponownie.

    Jeśli problem będzie się powtarzał, zobacz sekcję [Rozwiązywanie problemów](troubleshoot-azure-farmbeats.md) lub skontaktuj się z [forum Azure FarmBeats, aby uzyskać pomoc techniczną](https://aka.ms/FarmBeatsMSDN) z odpowiednimi dziennikami.

### <a name="view-and-download-a-map"></a>Wyświetlanie i pobieranie mapy

Wykonaj następujące kroki.

1. Na stronie głównej przejdź do **mapy** z lewego menu nawigacji.

    ![Wybierz mapy](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Wybierz **filtr** z lewej strony nawigacji w oknie. W oknie **Filtr** są wyświetlane kryteria wyszukiwania.

    ![W oknie filtru są wyświetlane kryteria wyszukiwania](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Z menu rozwijanego **wybierz pozycję Typ,** **Data**i **Nazwa.** Następnie wybierz pozycję **Zastosuj,** aby wyszukać mapę, którą chcesz wyświetlić.
  Data utworzenia zadania jest wyświetlana w formacie type_farmname_YYYY-MM-DD.

4. Przewiń listę map dostępnych za pomocą pasków nawigacyjnych na końcu strony.
5. Dla każdej kombinacji **nazwy gospodarstwa** i **daty**dostępne są następujące trzy mapy:
    - Ndvi (ndvi)
    - Evi
    - Ndwi ( NDWI )
6. Wybierz mapę, którą chcesz wyświetlić. W wyskakującym oknie zostanie wyświetlony podgląd wybranej mapy.
7. Wybierz **polecenie Pobierz** z menu rozwijanego, aby wybrać format pobierania. Mapa zostanie pobrana i zapisana w folderze lokalnym na komputerze.

    ![Podgląd mapy wybranych indeksów satelitarnych](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>Mapa cieplna wilgotności gleby

Wilgotność gleby to woda, która znajduje się w przestrzeniach między cząstkami gleby.Mapa cieplna Wilgotność gleby pomaga zrozumieć dane dotyczące wilgotności gleby na dowolnej głębokości, w wysokiej rozdzielczości w gospodarstwie. Aby uzyskać dokładną i użyteczną mapę cieplną gleby, wymagane jest jednolite wdrożenie czujników. Wszystkie czujniki muszą pochodzić od tego samego dostawcy. Różni dostawcy mają różnice w sposobie pomiaru wilgotności gleby wraz z różnicami w kalibracji. Mapa cieplna jest generowana dla określonej głębokości przy użyciu czujników wdrożonych na tej głębokości.

### <a name="before-you-begin"></a>Przed rozpoczęciem

Przed podjęciem próby wygenerowania mapy cieplnej wilgotność gleby należy spełnić następujące wymagania wstępne:

- Należy wdrożyć co najmniej trzy czujniki wilgotności gleby. Nie próbuj tworzyć mapy cieplnej wilgotności gleby, zanim czujniki zostaną wdrożone i skojarzone z gospodarstwem.
- Na generowanie mapy cieplnej wilgotności gleby ma wpływ pokrycie ścieżki, zachmurzenie i cień chmur. Co najmniej jedna bezchmurna scena Sentinel musi być dostępna przez ostatnie 120 dni, od dnia, w którym zażądano mapy cieplnej wilgotność gleby.
- Co najmniej połowa czujników wdrożonych w farmie musi być w trybie online i przesyłać dane strumieniowe do datahub.
- Mapa cieplna musi być generowana przy użyciu czujników od tego samego dostawcy.


## <a name="create-a-soil-moisture-heatmap"></a>Tworzenie mapy cieplnej wilgotności gleby

Wykonaj następujące kroki.

1. Na stronie głównej przejdź do **mapy** z lewego menu nawigacyjnego, aby wyświetlić stronę **Mapy.**
2. Wybierz **polecenie Utwórz mapy**i wybierz z menu rozwijanego opcję **Wilgoć gleby.**

    ![Z rozwijanego menu wybierz opcję Wilgotność gleby](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Po wybraniu opcji **Wilgotność gleby**pojawi się okno **Wilgotność gleby.**

    ![Okno wilgotności gleby](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Wybierz farmę z menu rozwijanego **Farm.**
   Aby wyszukać i wybrać farmę, możesz przewinąć z listy rozwijanej lub wprowadzić nazwę farmy w menu rozwijanym **Wybierz farmę.**
5. Z menu rozwijanego **Wybierz czujnik wilgotności gleby** wybierz pomiar (głębokość) czujnika wilgotności gleby, dla którego chcesz wygenerować mapę.
Aby znaleźć czujnik, przejdź do **sensora i**wybierz dowolny czujnik wilgotności gleby. Następnie w sekcji **Właściwości czujnika** użyj wartości w **nazwie miary**.
6. Aby wygenerować mapę na **dzień dzisiejszy** lub **ten tydzień,** wybierz jedną z opcji.
7. Aby wygenerować mapę niestandardowego zakresu dat, wybierz opcję **Wybierz zakres dat**. Wprowadź datę rozpoczęcia i zakończenia, dla której chcesz wygenerować mapę cieplną Wilgotność gleby.
8. Wybierz **pozycję Generuj mapy**.
 Zostanie wyświetlony komunikat potwierdzający ze szczegółami zadania.

   ![Komunikat potwierdzenia mapy wilgotności gleby](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Aby uzyskać informacje o stanie zadania, zobacz sekcję **Wyświetlanie zadań**. Jeśli stan zadania jest wyświetlany *nie powiódł się,* w etykietce narzędzia stanu Niepowodzenie pojawi się *szczegółowy* komunikat o błędzie. W takim przypadku powtórz poprzednie kroki i spróbuj ponownie.

    Jeśli problem będzie się powtarzał, zobacz sekcję [Rozwiązywanie problemów](troubleshoot-azure-farmbeats.md) lub skontaktuj się z [forum Azure FarmBeats, aby uzyskać pomoc techniczną](https://aka.ms/FarmBeatsMSDN) z odpowiednimi dziennikami.

### <a name="view-and-download-a-map"></a>Wyświetlanie i pobieranie mapy

Wykonaj następujące kroki.

1. Na stronie głównej przejdź do **mapy** z lewego menu nawigacji.

    ![Przejdź do map](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Wybierz **filtr** z lewej strony nawigacji w oknie. W oknie **Filtr** jest wyświetlany sposób, w którym można wyszukiwać mapy.

    ![Wybierz filtr z lewej strony nawigacji](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Z menu rozwijanego **wybierz pozycję Typ,** **Data**i **Nazwa.** Następnie wybierz pozycję **Zastosuj,** aby wyszukać mapę, którą chcesz wyświetlić. Data utworzenia zadania jest wyświetlana w formacie type_farmname_YYYY-MM-DD.
4. Wybierz ikonę **Sortuj** obok nagłówków tabeli, aby sortować według **farmy,** **daty**, **utworzonego w**dniu , **identyfikatora zadania**i **typu zadania**.
5. Przewiń listę dostępnych map za pomocą przycisków nawigacyjnych na końcu strony.
6. Wybierz mapę, którą chcesz wyświetlić. W wyskakującym oknie zostanie wyświetlony podgląd wybranej mapy.
7. Wybierz **polecenie Pobierz** z menu rozwijanego, aby wybrać format pobierania. Mapa zostanie pobrana i przechowywana w określonym folderze.

    ![Podgląd mapy cieplnej wilgotności gleby](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
