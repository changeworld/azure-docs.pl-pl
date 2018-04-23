---
title: Samouczek udostępniania roweru — przygotowanie zaawansowanych danych za pomocą usługi Azure Machine Learning Workbench
description: W tym samouczku wykonasz zadanie całościowego przygotowania danych przy użyciu usługi Azure Machine Learning Workbench
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/21/2017
ms.openlocfilehash: 77729ad52f101f0d57fe24115525795c6b4f2db2
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-use-azure-machine-learning-workbench-for-advanced-data-preparation-bike-share-data"></a>Samouczek: przygotowanie zaawansowanych danych za pomocą usługi Azure Machine Learning Workbench (dane udostępniania roweru)
Usługa Azure Machine Learning (wersja zapoznawcza) stanowi zintegrowane, kompleksowe rozwiązanie do nauki o danych i do zaawansowanych analiz przeznaczone dla profesjonalnych analityków. Będą oni korzystać z tego rozwiązania w celu przygotowywania danych, opracowywania eksperymentów i wdrażania modeli na skalę chmury.

W tym samouczku użyjesz usługi Machine Learning (wersja zapoznawcza), aby dowiedzieć się, jak:
> [!div class="checklist"]
> * Interaktywnie przygotować dane za pomocą narzędzia do przygotowywania danych usługi Machine Learning.
> * Importować, przekształcać i tworzyć zestawy danych testowych.
> * Tworzyć pakiet przygotowywania danych.
> * Uruchamiać pakiet przygotowywania danych przy użyciu języka Python.
> * Generować zestaw danych szkoleniowych przez ponowne użycie pakietu przygotowywania danych do dodatkowych plików wejściowych.
> * wykonywać skrypty w oknie lokalnego interfejsu wiersza polecenia platformy Azure,
> * wykonywać skrypty w środowisku usługi Azure HDInsight w chmurze.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Lokalna instalacja usługi Azure Machine Learning Workbench. Aby uzyskać więcej informacji, postępuj zgodnie z [instrukcjami szybkiego startu instalacji](../service/quickstart-installation.md).
* Jeśli nie masz zainstalowanego interfejsu wiersza polecenia platformy Azure, postępuj zgodnie z instrukcjami, aby [zainstalować najnowszą wersję interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Klaster HDInsights Spark](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal) utworzony na platformie Azure.
* Konto usługi Azure Storage.
* Znajomość tworzenia nowego projektu w usłudze Workbench.
* Chociaż nie jest to wymagane, warto zainstalować [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/), aby można było przekazywać, pobierać i wyświetlać obiekty blob na koncie magazynu.

## <a name="data-acquisition"></a>Pozyskiwanie danych
Ten samouczek używa [zestawu danych Boston Hubway](https://s3.amazonaws.com/hubway-data/index.html) i danych o pogodzie w Bostonie z [NOAA](http://www.noaa.gov/).

1. Pobierz pliki danych z następujących linków do swojego lokalnego środowiska deweloperskiego:

   * [Dane o pogodzie w Bostonie](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv)

   * Dane podróży Hubway z witryny internetowej firmy Hubway:

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. Po pobraniu rozpakuj każdy plik zip.

## <a name="upload-data-files-to-azure-blob-storage"></a>Przekazywanie plików danych do magazynu Azure Blob Storage
Magazyn Azure Blob Storage może służyć do hostowania plików danych.

1. Skorzystaj z konta magazynu używanego dla Twojego klastra usługi HDInsight.

    ![Konto magazynu klastra usługi HDInsight](media/tutorial-bikeshare-dataprep/hdinsightstorageaccount.png)

2. Utwórz nowy kontener o nazwie **data-files** do przechowywania plików danych **BikeShare**.

3. Przekaż pliki danych. Przekaż element `BostonWeather.csv` do folderu o nazwie `weather`. Przekaż pliki danych podróży do folderu o nazwie `tripdata`.

    ![Przekazywanie plików danych](media/tutorial-bikeshare-dataprep/azurestoragedatafile.png)

> [!TIP]
> Możesz również użyć Eksploratora usługi Storage do przekazania obiektów blob. To narzędzie pozwala również na wyświetlenie zawartości plików wygenerowanych w tym samouczku.

## <a name="learn-about-the-datasets"></a>Dowiedz się więcej o zestawach danych
1. Plik __pogody w Bostonie__ zawiera następujące pola związane z pogodą, zgłaszane co godzinę:

   * **DATE**

   * **REPORTTPYE**

   * **HOURLYDRYBULBTEMPF**

   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

2. Dane __hubway__ są zorganizowane w pliki według roku i miesiąca. Na przykład plik o nazwie `201501-hubway-tripdata.zip` zawiera plik csv, w którym znajdują się dane za styczeń 2015 r. Dane zawierają następujące pola, przy czym każdy wiersz reprezentuje podróż rowerową:

   * **Czas trwania podróży (w sekundach)**

   * **Data i godzina rozpoczęcia**

   * **Data i godzina zakończenia**

   * **Nazwa i identyfikator stacji rozpoczęcia**

   * **Nazwa i identyfikator stacji zakończenia**

   * **Identyfikator roweru**

   * **Typ użytkownika (Zwykły = użytkownik mający dostęp 24-godzinny lub 72-godzinny; Członek = członkostwo roczne lub miesięczne)**

   * **Kod pocztowy (jeśli użytkownik jest członkiem)**

   * **Płeć (zgłaszana przez samego członka)**

## <a name="create-a-new-project"></a>Tworzenie nowego projektu
1. Uruchom usługę **Machine Learning Workbench** z menu Start lub modułu uruchamiającego.

2. Utwórz nowy projekt usługi Machine Learning. Wybierz przycisk **+** na stronie **Projekty** lub wybierz kolejno pozycje **Plik** > **Nowy**.

   * Użyj szablonu **Udostępnianie roweru**.

   * Nazwij swój projekt **BikeShare**. 

## <a id="newdatasource"></a>Tworzenie nowego źródła danych

1. Utwórz nowe źródło danych. Wybierz przycisk **Dane** (ikona cylindra) na pasku narzędzi po lewej stronie, aby wyświetlić widok **Dane**.

   ![Karta widoku Dane](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. Dodaj źródło danych. Wybierz ikonę **+**, a następnie wybierz pozycję **Dodaj źródło danych**.

   ![Opcja Dodaj źródło danych](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>Dodawanie danych o pogodzie

1. **Magazyn danych**: wybierz magazyn danych, który zawiera dane. Ponieważ używasz plików, wybierz pozycję **Pliki/katalog**. Kliknij przycisk **Dalej**, aby kontynuować.

   ![Wpis Pliki/katalog](media/tutorial-bikeshare-dataprep/datasources.png)

2. **Wybór pliku**: dodaj dane o pogodzie. Wyszukaj i wybierz plik `BostonWeather.csv` przekazany wcześniej do usługi Blob Storage. Wybierz opcję **Dalej**.

   ![Wybieranie pliku z wybranym plikiem BostonWeather.csv](media/tutorial-bikeshare-dataprep/azureblobpickweatherdatafile.png)

3. **Szczegóły plików**: sprawdź schemat pliku, który został wykryty. Usługa Machine Learning Workbench analizuje dane w pliku i rozpoznaje schemat, który ma być używany.

   ![Weryfikowanie szczegółów pliku](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > W niektórych przypadkach usługa Workbench może nie wykryć poprawnego schematu. Zawsze sprawdzaj, czy parametry zestawu danych są poprawne. W przypadku danych o pogodzie sprawdź, czy są one ustawione na następujące wartości:
   >
   > * __Typ pliku__: plik ograniczany (csv, tsv, txt itd.)
   > * __Separator__: przecinek [,]
   > * __Znak wiersza komentarza__: nie jest ustawiona żadna wartość.
   > * __Tryb pomijania wierszy__: nie pomijaj
   > * __Kodowanie pliku__: utf-8
   > * __Tryb promowania nagłówków__: użyj nagłówków z pierwszego pliku

   Podgląd danych powinien wyświetlać następujące kolumny:

   * **Ścieżka**

   * **DATE**

   * **REPORTTYPE**

   * **HOURLYDRYBULBTEMPF**
   
   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

   Aby kontynuować, kliknij przycisk **Dalej**.

4. **Typy danych**: sprawdź typy danych, które są wykrywane automatycznie. Usługa Machine Learning Workbench analizuje dane w pliku i rozpoznaje typy danych, które mają być używane.

   a. W przypadku tych danych zmień **TYP DANYCH** dla wszystkich kolumn na **Ciąg**.

   > [!NOTE]
   > Wartość Ciąg służy do wyróżniania możliwości usługi Workbench w dalszej części tego samouczka. 

   ![Przeglądanie typów danych](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   b. Aby kontynuować, kliknij przycisk __Dalej__. 

5. **Próbkowanie**: aby utworzyć schemat próbkowania, wybierz pozycję **Edytuj**. Wybierz nowy wiersz __Top 10000__, który został dodany, a następnie wybierz pozycję __Edytuj__. Ustaw pozycję __Strategia próbki__ na **Cały plik**, a następnie wybierz pozycję **Zastosuj**.

   ![Dodawanie nowej strategii próbkowania](media/tutorial-bikeshare-dataprep/weatherdatasamplingfullfile.png)

   Aby użyć strategii __Cały plik__, wybierz wpis __Cały plik__, a następnie wybierz pozycję __Ustaw jako aktywny__. Obok pozycji __Cały plik__ zostanie wyświetlona gwiazdka, aby wskazać, że to jest aktywna strategia.

   ![Ustawianie strategii Cały plik jako aktywnej](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   Aby kontynuować, kliknij przycisk **Dalej**.

6. **Kolumna ścieżki**: użyj sekcji __Kolumna ścieżki__ do włączenia pełnej ścieżki pliku jako kolumny do importowanych danych. Wybierz pozycję __Nie dołączaj kolumny ścieżki__.

   > [!TIP]
   > Dołączenie ścieżki jako kolumny jest przydatne, jeśli importujesz folder z wieloma plikami o różnych nazwach. Jest to również przydatne, jeśli nazwy plików zawierają informacje, które chcesz wyodrębnić później.

   ![Kolumna ścieżki ustawiona tak, aby nie była uwzględniana](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **Zakończ**: aby zakończyć tworzenie źródła danych, wybierz pozycję **Zakończ**.

    Zostanie otwarta nowa karta źródła danych o nazwie __BostonWeather__. Próbka danych jest wyświetlana w widoku siatki. Przykład jest oparty na określonym poprzednio aktywnym schemacie próbkowania.

    Zwróć uwagę na okienko **Kroki** po prawej stronie ekranu, które zawiera poszczególne akcje wykonane podczas tworzenia tego źródła danych.

   ![Wyświetlanie źródła danych, przykładu i kroków](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>Wyświetlanie metryk źródła danych

Wybierz pozycję __Metryki__ w lewym górnym rogu karty widoku siatki. Ten widok przedstawia rozkład i inne zagregowane dane statystyczne próbki danych.

![Wyświetlanie metryk](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> Aby skonfigurować widoczność statystyk, użyj listy rozwijanej **Wybierz metrykę**. Wybierz metrykę i usuń jej zaznaczenie, aby zmienić widok siatki.

Aby wrócić do widoku __Dane__, wybierz pozycję __Dane__ w lewym górnym rogu strony.

## <a name="add-a-data-source-to-the-data-preparation-package"></a>Dodawanie źródła danych do pakietu przygotowywania danych

1. Wybierz pozycję __Przygotuj__, aby rozpocząć przygotowywanie danych. 

2. Po wyświetleniu monitu wprowadź nazwę pakietu przygotowywania danych, taką jak **BikeShare Data Prep**. 

3. Kliknij przycisk __OK__, aby kontynuować.

   ![Okno dialogowe Przygotowywanie](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. Nowy pakiet o nazwie **BikeShare Data Prep** zostanie wyświetlony w sekcji __Przygotowywanie danych__ karty __Dane__. 

   Aby wyświetlić pakiet, wybierz ten wpis. 

5. Wybierz przycisk **>>**, aby rozwinąć obszar __Przepływy danych__ i wyświetlić dane zawarte w pakiecie. W tym przykładzie __BostonWeather__ jest jedynym przepływem danych.

   > [!IMPORTANT]
   > Pakiet może zawierać wiele przepływów danych.

   ![Przepływy danych w pakiecie](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>Filtrowanie danych według wartości
1. Aby odfiltrować dane, kliknij prawym przyciskiem myszy komórkę z określoną wartością lub wybierz pozycję __Filtr__. Następnie wybierz typ filtru.

2. W tym samouczku wybierz komórkę zawierającą wartość `FM-15`. Następnie ustaw filtr na wartość **równa się**.  Teraz dane są filtrowane tak, aby zwracać tylko wiersze, w których __REPORTTYPE__ ma wartość `FM-15`.

   ![Okno dialogowe filtrowania](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM-15 jest typem raportu pogody METAR (Meteorological Terminal Aviation Routine Weather Report). Raporty FM-15 są empirycznie uważane za najbardziej kompletne i z małą liczbą brakujących danych.

## <a name="remove-a-column"></a>Usuwanie kolumny

Nie potrzebujesz już kolumny __REPORTTYPE__. Kliknij prawym przyciskiem myszy nagłówek kolumny, a następnie wybierz pozycję **Usuń kolumnę**.

   ![Opcja Usuń kolumnę](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>Zmienianie typów danych i usuwanie błędów
1. Naciśnij klawisz Ctrl (polecenie ⌘ dla komputerów Mac) podczas wybierania nagłówków kolumn, aby wybrać wiele kolumn jednocześnie. Użyj tej techniki, aby wybrać następujące nagłówki kolumn:

   * **HOURLYDRYBULBTEMPF**

   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

2. Kliknij prawym przyciskiem myszy jeden z wybranych nagłówków kolumny, a następnie wybierz pozycję **Konwertuj typ pola na liczbowy**. Ta opcja przeprowadza konwersję typu danych dla kolumn na liczbowy.

   ![Konwertowanie wielu kolumn na liczbowe](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. Odfiltruj wartości błędów. Niektóre kolumny mają problemy z konwersją typu danych. Ten problem jest wskazywany przez kolor czerwony na __pasku jakości danych__ dla kolumny.

   Aby usunąć wiersze, które mają błędy, kliknij prawym przyciskiem myszy nagłówek kolumny **HOURLYDRYBULBTEMPF**. Wybierz pozycję **Filtruj kolumnę**. Użyj domyślnej pozycji **Chcę** jako **Zachowaj wiersze**. Zmień listę rozwijaną **Warunki**, aby wybrać pozycję **nie jest błędem**. Kliknij przycisk **OK**, aby zastosować filtr.

   ![Wartości błędów filtrów](media/tutorial-bikeshare-dataprep/filtererrorvalues.png)

4. Aby usunąć pozostałe wiersze błędów w innych kolumnach, powtórz ten proces filtrowania dla kolumn **HOURLYRelativeHumidity** i **HOURLYWindSpeed**.

## <a name="use-by-example-transformations"></a>Użycie dla przykładowych przekształceń

Aby użyć danych w prognozie dla dwugodzinnych bloków czasu, musisz obliczyć średnie warunki pogodowe dla okresów dwugodzinnych. Użyj następujących akcji:

* Podziel kolumnę **DATE** na oddzielne kolumny **Data** i **Godzina**. Szczegółowe instrukcje zostały podane w poniższej sekcji.

* Utwórz kolumnę pochodną **Hour_Range** z kolumny **Godzina**. Szczegółowe instrukcje zostały podane w poniższej sekcji.

* Utwórz kolumnę pochodną **Data\_Godzina\_Zakres** z kolumn **DATE** i **Hour_Range**. Szczegółowe instrukcje zostały podane w poniższej sekcji.

### <a name="split-column-by-example"></a>Dzielenie kolumn według przykładu

1. Podziel kolumnę **DATE** na oddzielne kolumny **Data** i **Godzina**. Kliknij prawym przyciskiem myszy nagłówek kolumny **DATE** i wybierz pozycję **Podziel kolumnę według przykładu**.

   ![Wpis Podziel kolumnę według przykładu](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. Usługa Machine Learning Workbench identyfikuje znaczący ogranicznik i automatycznie tworzy dwie kolumny, dzieląc dane na wartości daty i godziny. 

3. Kliknij przycisk __OK__, aby zaakceptować wyniki operacji podziału.

   ![Dzielenie kolumn DATE_1 i DATE_2](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>Tworzenie kolumn pochodnych według przykładu

1. Aby uzyskać pochodny zakres dwugodzinny, kliknij prawym przyciskiem myszy nagłówek kolumny __DATE\_2__ i wybierz pozycję **Utwórz kolumnę pochodną według przykładu**.

   ![Wpis Utwórz kolumnę pochodną według przykładu](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   Nowa pusta kolumna zostanie dodana z wartościami null.

2. Wybierz pierwszą pustą komórkę w nowej kolumnie. Aby podać przykładowy żądany zakres czasu, wpisz **0:00–2:00** w nowej kolumnie, a następnie naciśnij klawisz Enter.

   ![Nowa kolumna z wartością 0:00–2:00](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Usługa Machine Learning Workbench syntetyzuje program na podstawie podanych przez Ciebie przykładów i stosuje ten sam program do pozostałych wierszy. Wszystkie pozostałe wiersze są wypełniane automatycznie na podstawie podanego przykładu. Usługa Workbench również analizuje dane i próbuje zidentyfikować przypadki brzegowe. 

   > [!IMPORTANT]
   > Identyfikacja przypadków brzegowych może nie działać na komputerze Mac w bieżącej wersji usługi Workbench. Pomiń kolejne kroki 3 i 4 na komputerach Mac. W zamian wybierz przycisk __OK__ po wypełnieniu wszystkich wierszy wartościami pochodnymi.
   
3. Tekst **Analizowanie danych** nad siatką wskazuje, że usługa Workbench próbuje wykryć przypadki brzegowe. Po zakończeniu stan zmieni się na **Przejrzyj następny sugerowany wiersz** lub **Brak sugestii**. W tym przykładzie jest zwracany stan **Przejrzyj następny sugerowany wiersz**.

4. Aby wyświetlić sugerowane zmiany, wybierz pozycję **Przejrzyj następny sugerowany wiersz**. Komórka, którą należy przejrzeć i poprawić (w razie potrzeby), jest wyróżniona na ekranie.

   ![Przeglądanie następnego sugerowanego wiersza](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    Wybierz __OK__, aby zaakceptować transformację.
 
5. Nastąpi powrót do widoku siatki danych __BostonWeather__. Siatka zawiera teraz trzy kolumny dodane wcześniej.

   ![Widok siatki z dodanymi wierszami](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   > Wszystkie wprowadzone zmiany zostaną zachowane w okienku **Kroki**. Przejdź do kroku utworzonego w okienku **Kroki**, wybierz strzałkę w dół i wybierz pozycję **Edytuj**. Zostanie wyświetlone zaawansowane okno dla **Tworzenia kolumn pochodnych według przykładu**. Wszystkie przykłady są zachowywane w tym miejscu. Przykłady możesz również dodać ręcznie, klikając dwukrotnie wiersz w poniższej siatce. Wybierz pozycję **Anuluj**, aby powrócić do głównej siatki bez stosowania zmian. Dostęp do tego widoku możesz też uzyskać, wybierając pozycję **Tryb zaawansowany** podczas wykonywania przekształcenia **Utwórz kolumnę pochodną według przykładu**.

6. Aby zmienić nazwę kolumny, dwukrotnie kliknij nagłówek kolumny i wpisz **zakres godzin**. Naciśnij klawisz Enter, aby zapisać zmiany.

   ![Zmienianie nazwy kolumny](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. Aby utworzyć pochodny zakres dat i godzin, wybierz wiele kolumn **Data\_1** i **Zakres godzin**, kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Utwórz kolumnę pochodną według przykładu**.

   ![Tworzenie kolumny pochodnej według przykładu](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   Wpisz ciąg **01 stycznia 2015 0:00–2:00** jako przykład z pierwszego wiersza i wybierz klawisz Enter.

   Usługa Workbench określa przekształcenie na podstawie podanego przykładu. W tym przykładzie wynikiem jest zmiana formatu daty i połączenie z oknem dwóch godzin.

   ![Przykład: 01 stycznia 2015 0:00–2:00](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)

   > [!IMPORTANT]
   > Na komputerze Mac wykonaj poniższy krok zamiast kroku 8:
   >
   > * Przejdź do pierwszej komórki zawierającej ciąg **01 lutego 2015 0:00–2:00**. Powinien to być wiersz 15. Popraw wartość na **02 stycznia 2015 0:00–2:00** i wybierz klawisz Enter. 
   

8. Poczekaj, aż stan zmieni się z **Analizowanie danych** na **Przejrzyj następny sugerowany wiersz**. Ta zmiana może potrwać kilka sekund. Wybierz link stanu, aby przejść do sugerowanego wiersza. 

   ![Sugerowany wiersz do przejrzenia](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   Wiersz zawiera wartość null, ponieważ wartość daty źródłowej może mieć format dd/mm/rrrr lub mm/dd/rrrr. Wpisz poprawną wartość **13 stycznia 2015 2:00–4:00** i wybierz klawisz Enter. Usługa Workbench używa dwóch przykładów do ulepszenia tworzenia elementów pochodnych dla pozostałych wierszy.

   ![Poprawnie sformatowane dane](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. Wybierz **OK**, aby zaakceptować przekształcenie.

   ![Ukończona siatka przekształcenia](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > Aby użyć **trybu zaawansowanego** dla pozycji **Utwórz kolumnę pochodną według przykładu** w tym kroku, wybierz strzałkę w dół w okienku **Kroki**. W siatce danych obok kolumn **DATE\_1** i **Zakres godzin** znajdują się pola wyboru. Usuń zaznaczenie pola wyboru obok kolumny **Zakres godzin**, aby zobaczyć, jak zmienią się dane wyjściowe. W przypadku braku kolumny **Zakres godzin** jako danych wejściowych zakres **0:00–2:00** jest traktowany jako stała i jest dołączany do wartości pochodnych. Wybierz pozycję **Anuluj**, aby powrócić do głównej siatki bez stosowania zmian.
   ![Tryb zaawansowany](media/tutorial-bikeshare-dataprep/derivedcolumnadvancededitdeselectcolumn.png)

10. Aby zmienić nazwę kolumny, kliknij dwukrotnie nagłówek. Zmień nazwę na **Zakres daty godziny**, a następnie naciśnij klawisz Enter.

11. Dokonaj wielokrotnego wyboru kolumn **DATE**, **DATE\_1**, **DATE\_2** i **Zakres godzin**. Kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Usuń kolumnę**.

## <a name="summarize-data-mean"></a>Podsumowanie danych (średnia)

Następnym krokiem jest podsumowanie warunków pogodowych, obliczając średnią wartości pogrupowanych według zakresu godzin.

1. Wybierz kolumnę **Zakres daty godziny**, a następnie z menu **Przekształcenia** wybierz pozycję **Podsumowanie**.

    ![Menu Przekształcenia](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. Aby podsumować dane, przeciągnij kolumny z siatki w dolnej części strony do okienka po lewej i prawej stronie u góry. Okienko po lewej stronie zawiera tekst **Przeciągnij kolumny tutaj, aby zgrupować dane**. Okienko po prawej stronie zawiera tekst **Przeciągnij kolumny tutaj, aby podsumować dane**. 

    a. Przeciągnij kolumnę **Zakres daty godziny** z siatki w dolnej części do okienka po lewej stronie. Przeciągnij kolumny **HOURLYDRYBULBTEMPF**, **HOURLYRelativeHumidity** i **HOURLYWindSpeed** do prawego okienka. 

    b. W okienku po prawej stronie wybierz pozycję **Średnia** jako miarę **Agregacji** dla każdej kolumny. Wybierz przycisk **OK**, aby zakończyć podsumowanie.

    ![Ekran podsumowywania danych](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-by-using-script"></a>Przekształcanie przepływu danych przy użyciu skryptu

Zmienianie danych w kolumnach liczbowych na dane z zakresu od 0 do 1 umożliwia uzyskanie szybkiej zbieżności w niektórych modelach. Obecnie nie ma wbudowanego przekształcenia, które może w sposób standardowy przeprowadzić takie przekształcenie. Użyj skryptu języka Python w celu wykonania tej operacji.

1. Z menu **Przekształcenie** wybierz pozycję **Przekształć przepływ danych (skrypt)**.

2. Wprowadź następujący kod w wyświetlonym polu tekstowym. Jeśli użyto nazw kolumn, kod powinien działać bez żadnych modyfikacji. Piszesz prostą logikę normalizacji minimum-maksimum w języku Python.

    > [!WARNING]
    > Skrypt oczekuje nazw kolumn użytych wcześniej w tym samouczku. Jeśli masz inne nazwy kolumn, musisz zmienić nazwy w skrypcie.

   ```python
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   minVal = min(df["HOURLYDRYBULBTEMPF_Mean"])
   df["HOURLYDRYBULBTEMPF_Mean"] = (df["HOURLYDRYBULBTEMPF_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYDRYBULBTEMPF_Mean":"N_DryBulbTemp"},inplace=True)

   maxVal = max(df["HOURLYRelativeHumidity_Mean"])
   minVal = min(df["HOURLYRelativeHumidity_Mean"])
   df["HOURLYRelativeHumidity_Mean"] = (df["HOURLYRelativeHumidity_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYRelativeHumidity_Mean":"N_RelativeHumidity"},inplace=True)

   maxVal = max(df["HOURLYWindSpeed_Mean"])
   minVal = min(df["HOURLYWindSpeed_Mean"])
   df["HOURLYWindSpeed_Mean"] = (df["HOURLYWindSpeed_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYWindSpeed_Mean":"N_WindSpeed"},inplace=True)

   df
   ```

    > [!TIP]
    > Skrypt w języku Python musi zwracać `df` na końcu. Ta wartość jest używana do zapełniania siatki.
    
   ![Okno dialogowe Przekształcanie przepływu danych (skrypt)](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. Wybierz przycisk __OK__, aby użyć skryptu. Kolumny liczbowe w siatce zawierają teraz wartości z zakresu od 0 do 1.

    ![Siatka zawierająca wartości od 0 do 1](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

Zakończono przygotowywanie danych o pogodzie. Następnie przygotuj dane podróży.

## <a name="load-trip-data"></a>Ładowanie danych podróży

1. Aby zaimportować plik `201701-hubway-tripdata.csv`, wykonaj kroki opisane w sekcji [Tworzenie nowego źródła danych](#newdatasource). W trakcie procesu importowania użyj następujących opcji:

    * __Wybieranie pliku__: wybierz pozycję **Azure Blob** podczas wyszukiwania pliku.

    * __Schemat próbkowania__: wybierz schemat próbkowania **Cały plik** i uaktywnij próbkę.

    * __Typ danych__: zaakceptuj wartości domyślne.

2. Po zaimportowaniu danych wybierz pozycję __Przygotuj__, aby rozpocząć przygotowywanie danych. Wybierz istniejący pakiet **BikeShare Data Prep.dprep**, a następnie wybierz przycisk __OK__.

    Ten proces powoduje dodanie **Przepływu danych** do istniejącego pliku **Przygotowywanie danych** zamiast tworzenia nowego.

    ![Wybieranie istniejącego pakietu](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. Po załadowaniu siatki rozwiń węzeł __DATAFLOWS__. Istnieją teraz dwa przepływy danych: **BostonWeather** i **201701-hubway-tripdata**. Wybierz wpis **201701-hubway-tripdata**.

    ![Wpis 201701-hubway-tripdata](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-the-map-inspector"></a>Używanie inspektora mapy

Podczas przygotowywania danych są dostępne użyteczne wizualizacje, zwane inspektorami, obsługujące ciągi oraz dane liczbowe i geograficzne. Pomagają one lepiej zrozumieć dane i zidentyfikować elementy odstające. Wykonaj poniższe kroki, aby użyć inspektora mapy.

1. Wybierz wiele kolumn **szerokość geograficzna stacji początkowej** i **długość geograficzna stacji początkowej**. Kliknij prawym przyciskiem myszy jedną z kolumn, a następnie wybierz pozycję **Mapa**.

    > [!TIP]
    > Aby włączyć wybór wielokrotny, przytrzymaj klawisz Ctrl (polecenie ⌘ dla komputerów Mac) i wybierz nagłówek każdej kolumny.

    ![Wizualizacja mapy](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. Aby zmaksymalizować wizualizację mapy, wybierz ikonę **Maksymalizuj**. Aby dopasować mapę do okna, wybierz ikonę **E** w lewym górnym rogu wizualizacji.

    ![Zmaksymalizowany obraz](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. Wybierz przycisk **Minimalizuj**, aby powrócić do widoku siatki.

## <a name="use-the-column-statistics-inspector"></a>Używanie inspektora statystyk kolumny

Aby użyć inspektora statystyk kolumny, kliknij prawym przyciskiem myszy kolumnę __tripduration__ i wybierz pozycję __Statystyki kolumny__.

![Wpis statystyk kolumny](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

Ten proces powoduje dodanie nowej wizualizacji zatytułowanej __Statystyki tripduration__ w okienku __INSPEKTORZY__.

![Inspektor statystyk tripduration](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> Wartość maksymalna czasu trwania podróży wynosi 961 814 minut, czyli około 2 lata. Wydaje się, że w zestawie danych istnieją pewne elementy odstające.

## <a name="use-the-histogram-inspector"></a>Używanie inspektora histogramu

Aby spróbować zidentyfikować elementy odstające, kliknij prawym przyciskiem myszy kolumnę __tripduration__ i wybierz pozycję __Histogram__.

![Inspektor histogramu](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

Histogram nie jest pomocny, ponieważ wartości odstające zniekształcają wykres.

## <a name="add-a-column-by-using-script"></a>Dodawanie kolumny przy użyciu skryptu

1. Kliknij prawym przyciskiem myszy kolumnę **tripduration** i wybierz pozycję **Dodaj kolumnę (skrypt)**.

    ![Menu Dodaj kolumnę (skrypt)](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. W oknie dialogowym __Dodawanie kolumny (skrypt)__ użyj następujących wartości:

    * __Nowa nazwa kolumny__: logtripduration

    * __Wstaw tę nową kolumnę po__: tripduration

    * __Kod nowej kolumny__: `math.log(row.tripduration)`

    * __Typ bloku kodu__: wyrażenie

   ![Okno dialogowe Dodawanie kolumny (skrypt)](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. Wybierz przycisk __OK__, aby dodać kolumnę **logtripduration**.

4. Kliknij kolumnę prawym przyciskiem myszy i wybierz pozycję **Histogram**.

    ![Histogram kolumny logtripduration](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

    Wizualnie ten histogram jest podobny do rozkładu normalnego z nietypowym zakończeniem.

## <a name="use-an-advanced-filter"></a>Używanie filtru zaawansowanego

Użycie filtru do danych aktualizuje inspektorów dla nowej dystrybucji. 

1. Kliknij prawym przyciskiem myszy kolumnę **logtripduration** i wybierz pozycję **Filtruj kolumnę**. 

2. W oknie dialogowym __Edytowanie__ użyj następujących wartości:

    * __Filtruj tę kolumnę liczbową__: logtripduration

    * __Chcę__: zachowaj wiersze

    * __Gdy__: dowolny z poniższych warunków ma wartość True (logiczne OR)

    * __Jeśli ta kolumna__: mniejsza niż

    * __Wartość__: 9

    ![Opcje filtru](media/tutorial-bikeshare-dataprep/loftripfilter.png)

3. Kliknij przycisk __OK__, aby zastosować filtr.

    ![Zaktualizowane histogramy po zastosowaniu filtru](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>Efekt obwódki

1. Maksymalizuj histogram kolumny **logtripduration**. Niebieski histogram jest nałożony na szary histogram. Ten ekran jest nazywany **efektem obwódki**:

    * Szary histogram reprezentuje dystrybucję przed operacją (w tym przypadku operacją filtrowania).

    * Niebieski histogram reprezentuje histogram po wykonaniu operacji. 

   Obwódka ułatwia wizualizację skutków operacji na danych.

   ![Efekt obwódki](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > Niebieski histogram wygląda na krótszy w porównaniu do poprzedniego. Różnica ta jest spowodowana automatyczną ponowną obsługą zasobników danych w nowym zakresie.

2. Aby usunąć obwódki, zaznacz pozycję __Edytuj__ i wyczyść pole wyboru __Pokaż obwódkę__.

    ![Opcje histogramu](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. Wybierz przycisk **OK**, aby wyłączyć obwódkę. Następnie zminimalizuj histogram.

### <a name="remove-columns"></a>Usuwanie kolumn

W danych podróży każdy wiersz reprezentuje zdarzenie pobrania roweru. W tym samouczku będą potrzebne tylko kolumny **starttime** i **identyfikator stacji rozpoczęcia**. Aby usunąć inne kolumny, zastosuj wybór wielokrotny do tych dwóch kolumn, kliknij prawym przyciskiem myszy nagłówek kolumny, a następnie wybierz pozycję **Zachowaj kolumnę**. Inne kolumny zostaną usunięte.

![Opcja Zachowaj kolumnę](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>Podsumowanie danych (licznik)

Aby podsumować zapotrzebowanie na rower dla okresu dwóch godzin, użyj kolumn pochodnych.

1. Kliknij prawym przyciskiem myszy kolumnę **starttime** i wybierz pozycję **Utwórz kolumnę pochodną według przykładu**.

    ![Opcja Utwórz kolumnę pochodną według przykładu](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. Na przykład wprowadź wartość **01 stycznia 2017 0: 00–2:00** dla pierwszego wiersza.

    > [!IMPORTANT]
    > W poprzednim przykładzie kolumn pochodnych użyto wielu kroków, aby uzyskać kolumnę zawierającą okres daty i godziny. W tym przykładzie możesz zobaczyć, że ta operacja może zostać wykonana jako pojedynczy krok, zapewniając przykład końcowych danych wyjściowych.

    > [!NOTE]
    > Możesz podać przykład dla dowolnego wiersza. W tym przykładzie wartość **01 stycznia 2017 0:00–2:00** jest prawidłowa dla pierwszego wiersza danych.

    ![Przykładowe dane](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)

   > [!IMPORTANT]
   > Na komputerze Mac wykonaj poniższe czynności zamiast kroku 3:
   >
   > * Przejdź do pierwszej komórki zawierającej ciąg **01 stycznia 2017 1:00–2:00**. Powinien to być wiersz 14. Popraw wartość na **01 stycznia 2017 0:00–2:00** i wybierz klawisz Enter. 

3. Poczekaj, aż aplikacja obliczy wartości dla wszystkich wierszy. Proces może potrwać kilka sekund. Po zakończeniu analizy użyj linku __Przejrzyj następny sugerowany wiersz__ w celu sprawdzenia danych.

   ![Zakończona analiza z linkiem do przeglądu](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    Upewnij się, że obliczone wartości są poprawne. Jeśli nie, zaktualizuj wartość za pomocą oczekiwanej wartości i wybierz klawisz Enter. Następnie poczekaj na zakończenie analizy. Zakończenie procesu **Przejrzyj następny sugerowany wiersz** nastąpi po wyświetleniu komunikatu **Brak sugestii**. Komunikat **Brak sugestii** oznacza, że aplikacja sprawdziła przypadki brzegowe i syntetyzowany program jest satysfakcjonujący. To jest najlepsze rozwiązanie do wykonania kontroli wizualnej przekształconych danych przed zaakceptowaniem przekształcenia. 

4. Wybierz **OK**, aby zaakceptować przekształcenie. Zmień nazwę nowo utworzonej kolumny na **Zakres daty godziny**.

    ![Kolumna ze zmienioną nazwą](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. Kliknij prawym przyciskiem myszy nagłówek kolumny **starttime**, a następnie wybierz pozycję **Usuń kolumnę**.

6. Aby podsumować dane, z menu __Przekształcenie__ wybierz pozycję __Podsumuj__. Aby utworzyć transformację, wykonaj następujące czynności:

    * Przeciągnij kolumny __Zakres daty godziny__ i __identyfikator stacji początkowej__ do okienka **Grupuj według** po lewej stronie.

    * Przeciągnij kolumnę __identyfikator stacji początkowej__ do okienka **podsumowanie danych** po prawej stronie.

   ![Opcje podsumowania](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. Wybierz przycisk **OK**, aby zaakceptować wynik podsumowania.

## <a name="join-dataflows"></a>Dołączanie przepływów danych

Aby dołączyć dane pogody do danych podróży, wykonaj następujące kroki:

1. Z menu __Przekształcenie__ wybierz pozycję __Dołącz__.

2. __Tabele__: wybierz tabelę **BostonWeather** jako przepływ danych **po lewej stronie** i tabelę **201701-hubway-tripdata** jako przepływ danych **po prawej stronie**. Aby kontynuować, kliknij przycisk **Dalej**.

    ![Opcje wybrane w obszarze Tabele](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __Kluczowe kolumny__: wybierz kolumnę **Zakres daty godziny** w obu tabelach, a następnie wybierz przycisk __Dalej__.

    ![Opcje wybrane w obszarze Kluczowe kolumny](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __Typ przyłączenia__: wybierz __Pasujące wiersze__ jako typ przyłączenia, a następnie wybierz pozycję __Zakończ__.

    ![Typ przyłączenia pasujących wierszy](media/tutorial-bikeshare-dataprep/joinscreen.png)

    Ten proces tworzy nowy przepływ danych o nazwie __Przyłącz wynik__.

## <a name="create-additional-features"></a>Tworzenie dodatkowych funkcji

1. Aby utworzyć kolumnę zawierającą dzień tygodnia, kliknij prawym przyciskiem myszy kolumnę **Zakres daty godziny** i wybierz pozycję **Utwórz kolumnę pochodną według przykładu**. Użyj wartości __Nd__ dla daty, który wystąpiła w niedzielę. Przykład: **01 stycznia 2017 0:00–2:00**. Naciśnij klawisz Enter, a następnie wybierz przycisk **OK**. Zmień nazwę tej kolumny na __Dni tygodnia__.

    ![Tworzenie nowej kolumny dnia tygodnia](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. Aby utworzyć kolumnę zawierającą okres dla wiersza, kliknij prawym przyciskiem myszy kolumnę **Zakres daty godziny** i wybierz pozycję **Utwórz kolumnę pochodną według przykładu**. Użyj wartości **0:00–2:00** dla wiersza zawierającego ciąg **01 stycznia 2017 0:00–2:00**. Naciśnij klawisz Enter, a następnie wybierz przycisk **OK**. Zmień nazwę tej kolumny na **Okres**.

    ![Kolumna okresu](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. Aby usunąć kolumny **Zakres daty godziny** i **Zakres r_daty godziny**, naciśnij klawisz Ctrl (polecenie ⌘ dla komputerów Mac) i zaznacz nagłówek każdej kolumny. Kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Usuń kolumnę**.

## <a name="read-data-from-python"></a>Odczytywanie danych z języka Python

Możesz uruchomić pakiet przygotowywania danych z języka Python lub PySpark i pobrać wynik w postaci **ramki danych**.

Aby wygenerować przykładowy skrypt w języku Python, kliknij prawym przyciskiem myszy pakiet __BikeShare Data Prep__ i wybierz pozycję __Generuj plik kodu dostępu do danych__. Przykładowy plik w języku Python jest tworzony w Twoim **folderze projektu**, a także jest ładowany na karcie w usłudze Workbench. Poniższy skrypt języka Python jest przykładem generowanego kodu:

```python
# Use the Azure Machine Learning data preparation package
from azureml.dataprep import package

# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# This call will load the referenced package and return a DataFrame.
# If run in a PySpark environment, this call returns a
# Spark DataFrame. If not, it will return a Pandas DataFrame.
df = package.run('BikeShare Data Prep.dprep', dataflow_idx=0)

# Remove this line and add code that uses the DataFrame
df.head(10)
```

W tym samouczku nazwa pliku to `BikeShare Data Prep.py`. Ten plik jest używany w dalszej części tego samouczka.

## <a name="save-test-data-as-a-csv-file"></a>Zapisywanie danych testowych jako pliku CSV

Aby zapisać przepływ danych **Dołącz wynik** do pliku CSV, musisz zmienić skrypt `BikeShare Data Prep.py`. 

1. Otwórz projekt do edycji w programie Visual Studio Code.

    ![Otwieranie projektu w programie Visual Studio Code](media/tutorial-bikeshare-dataprep/openprojectinvscode.png)

2. Zaktualizuj skrypt języka Python w pliku `BikeShare Data Prep.py`, używając następującego kodu:

    ```python
    import pyspark

    from azureml.dataprep.package import run
    from pyspark.sql.functions import *

    # start Spark session
    spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

    # dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
    df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
    df.show(n=10)
    row_count_first = df.count()

    # Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/testata'
    # 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
    blobfolder = 'Your Azure Storage blob path'

    df.write.csv(blobfolder, mode='overwrite') 

    # retrieve csv file parts into one data frame
    csvfiles = "<Your Azure Storage blob path>/*.csv"
    df = spark.read.option("header", "false").csv(csvfiles)
    row_count_result = df.count()
    print(row_count_result)
    if (row_count_first == row_count_result):
        print('counts match')
    else:
        print('counts do not match')
    print('done')
    ```

3. Zastąp element `Your Azure Storage blob path` ścieżką do pliku wyjściowego do utworzenia. Zastąp w przypadku zmiennych `blobfolder` i `csvfiles`.

## <a name="create-an-hdinsight-run-configuration"></a>Tworzenie konfiguracji uruchomienia usługi HDInsight

1. W aplikacji Machine Learning Workbench otwórz okno wiersza polecenia, wybierz menu **Plik**, a następnie wybierz polecenie **Otwórz wiersz polecenia**. Wiersz polecenia uruchomi się w folderze projektu z monitem `C:\Projects\BikeShare>`.

    ![Otwieranie wiersza polecenia](media/tutorial-bikeshare-dataprep/opencommandprompt.png)

   >[!IMPORTANT]
   >Musisz użyć okna wiersza polecenia (otwartego z aplikacji Workbench), aby wykonać poniższe kroki.

2. Użyj wiersza polecenia, aby zalogować się na platformie Azure. 

   Podczas uwierzytelniania w zasobach platformy Azure aplikacja Workbench i interfejs wiersza polecenia używają osobnych pamięci podręcznych poświadczeń. Wystarczy wykonać tę czynność jeden raz i poświadczenia zostaną zapamiętane do czasu wygaśnięcia tokenu z pamięci podręcznej. Polecenie `az account list` zwraca listę subskrypcji dostępnych na potrzeby logowania. Jeśli istnieje więcej niż jedna subskrypcja, użyj wartości identyfikatora z odpowiedniej subskrypcji. Ustaw subskrypcję jako domyślnie używane konto przy użyciu polecenia `az account set -s`, a następnie podaj wartość identyfikatora subskrypcji. Potwierdź ustawienia za pomocą polecenia `show` konta.

   ```azurecli
   REM login by using the aka.ms/devicelogin site
   az login
   
   REM lists all Azure subscriptions you have access to 
   az account list -o table
   
   REM sets the current Azure subscription to the one you want to use
   az account set -s <subscriptionId>
   
   REM verifies that your current subscription is set correctly
   az account show
   ```

3. Utwórz konfigurację uruchomieniową usługi HDInsight. Potrzebna jest nazwa klastra i hasło `sshuser`.

    ```azurecli
    az ml computetarget attach cluster --name hdinsight --address <yourclustername>.azurehdinsight.net --username sshuser --password <your password>
    az ml experiment prepare -c hdinsight
    ```
> [!NOTE]
> Po utworzeniu pustego projektu domyślne konfiguracje uruchomieniowe to **local** i **docker**. Spowoduje to utworzenie nowej konfiguracji uruchomieniowej dostępnej w usłudze Workbench podczas uruchamiania skryptów. 

## <a name="run-in-an-hdinsight-cluster"></a>Uruchamianie w klastrze usługi HDInsight

Wróć do aplikacji Machine Learning Workbench, aby uruchomić skrypt w klastrze usługi HDInsight.

1. Wróć do ekranu głównego projektu, wybierając ikonę **Ekran główny** po lewej stronie.

2. Wybierz pozycję **hdinsight** z listy rozwijanej, aby uruchomić skrypt w klastrze usługi HDInsight.

3. Wybierz pozycję **Uruchom**. Skrypt jest przesyłany w postaci zadania. Stan zadania zmieni się na __Ukończono__ po zapisaniu pliku w określonej lokalizacji w kontenerze magazynu.

    ![Uruchamianie skryptu w usłudze HDInsight](media/tutorial-bikeshare-dataprep/hdinsightrunscript.png)


## <a name="substitute-data-sources"></a>Zastępowanie źródeł danych

W poprzednich krokach do przygotowywania danych testowych były używane źródła danych `201701-hubway-tripdata.csv` i `BostonWeather.csv`. Aby użyć pakietu z innymi plikami danych podróży, wykonaj następujące czynności:

1. Utwórz nowe źródło danych, korzystając z opisanych wcześniej kroków, z następującymi zmianami procesu:

    * __Wybór pliku__: po wybraniu pliku dokonaj wielokrotnego wyboru sześciu pozostałych plików CSV tripdata.

    ![Ładowanie sześciu pozostałych plików](media/tutorial-bikeshare-dataprep/browseazurestoragefortripdatafiles.png)

    > [!NOTE]
     > Wpis __+5__ wskazuje, że istnieje pięć dodatkowych plików innych niż ten, który jest wymieniony.

    * __Szczegóły pliku__: ustaw __Tryb promocji nagłówków__ na **Wszystkie pliki mają ten sam nagłówek**. Ta wartość wskazuje, że każdy plik zawiera ten sam nagłówek.

    ![Wybieranie szczegółów pliku](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   Zapisz nazwę tego źródła danych, ponieważ będzie ona używana w dalszych krokach.

2. Wybierz ikonę folderu, aby wyświetlić pliki w swoim projekcie. Rozwiń katalog __aml\_config__, a następnie wybierz plik `hdinsight.runconfig`.

    ![Lokalizacja pliku hdinsight.runconfig](media/tutorial-bikeshare-dataprep/hdinsightsubstitutedatasources.png) 

3. Wybierz przycisk **Edytuj**, aby otworzyć plik w programie Visual Studio Code.

4. Dodaj następujące wiersze na końcu pliku `hdinsight.runconfig`, a następnie wybierz ikonę dysku, aby zapisać plik.

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    Ta zmiana zastępuje oryginalne źródło danych takim, które zawiera sześć plików danych podróży.

## <a name="save-training-data-as-a-csv-file"></a>Zapisywanie danych szkoleniowych jako pliku CSV

1. Przejdź do wcześniej edytowanego pliku Python `BikeShare Data Prep.py`. Podaj inną ścieżkę pliku w celu zapisania danych szkoleniowych.

    ```python
    import pyspark

    from azureml.dataprep.package import run
    from pyspark.sql.functions import *

    # start Spark session
    spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

    # dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
    df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
    df.show(n=10)
    row_count_first = df.count()

    # Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/traindata'
    # 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
    blobfolder = 'Your Azure Storage blob path'

    df.write.csv(blobfolder, mode='overwrite') 

    # retrieve csv file parts into one data frame
    csvfiles = "<Your Azure Storage blob path>/*.csv"
    df = spark.read.option("header", "false").csv(csvfiles)
    row_count_result = df.count()
    print(row_count_result)
    if (row_count_first == row_count_result):
        print('counts match')
    else:
        print('counts do not match')
    print('done')
    ```

2. Użyj folderu o nazwie `traindata` na potrzeby wyjściowych danych szkoleniowych.

3. Aby przesłać nowe zadanie, wybierz pozycję **Uruchom**. Upewnij się, że wybrano pozycję **hdinsight**. Zadanie jest przesyłane z nową konfiguracją. Danymi wyjściowymi tego zadania są dane szkoleniowe. Te dane są tworzone przy użyciu wykonanych wcześniej kroków przygotowywania danych. Wykonanie zadania może potrwać kilka minut.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki
Samouczek przygotowywania danych udostępniania roweru został ukończony. W tym samouczku użyto usługi Machine Learning (wersja zapoznawcza), aby dowiedzieć się, jak:
> [!div class="checklist"]
> * Interaktywnie przygotować dane za pomocą narzędzia do przygotowywania danych usługi Machine Learning.
> * Importować, przekształcać i tworzyć zestawy danych testowych.
> * Tworzyć pakiet przygotowywania danych.
> * Uruchamiać pakiet przygotowywania danych przy użyciu języka Python.
> * Generować zestaw danych szkoleniowych przez ponowne użycie pakietu przygotowywania danych do dodatkowych plików wejściowych.

Następnie dowiedz się więcej o przygotowywaniu danych:
> [!div class="nextstepaction"]
> [Data Preparation User Guide](data-prep-user-guide.md) (Podręcznik użytkownika przygotowywania danych)
