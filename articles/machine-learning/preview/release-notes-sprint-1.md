---
title: Azure ML Workbench informacje o wersji dla przebiegu 1 listopada 2017 r.
description: "Ten dokument zawiera szczegóły aktualizacje dla wersji przebiegu 1 uczenie Maszynowe Azure"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/06/2017
ms.openlocfilehash: a4945c77be5763ffeda328184149f712572937c0
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2017
---
# <a name="sprint-1---november-2017"></a>Przebieg 1 — listopad 2017 

**Numer wersji: 0.1.1710.31013**

Witamy w drugiej aktualizacji Azure Machine Learning Workbench. W dalszym ciągu poprawiają dotyczących zabezpieczeń, stabilność i łatwości konserwacji w aplikacji workbench, interfejsu wiersza polecenia i warstwy usług zaplecza. Dziękujemy za przesłanie uśmiechy i frowns. Duża liczba poniżej aktualizacje są wprowadzane jako bezpośrednie wyniki swoją opinię. Pamiętaj o ich wejścia!

## <a name="notable-new-features"></a>Ważne funkcje
- Uczenie Maszynowe Azure jest obecnie dostępna w dwóch nowych regionów platformy Azure: **Europa** i **Azja południowo-wschodnia**. Dołączenia poprzedniej regionów **wschodnie stany USA 2**, **zachodnie środkowe stany**, i **Australia Wschodnia**, przywracanie łączna liczba wdrożonych regionów pięć.
- Firma Microsoft włączona Składnia kodu Python wyróżnianie w aplikacji Workbench, aby ułatwić odczyt i Edycja kodu źródłowego języka Python. 
- Można teraz uruchomić Twoje ulubione IDE bezpośrednio z pliku, a nie z całego projektu.  Otwarcie pliku w Workbench, a następnie klikając pozycję "Edytuj" uruchamia środowiskiem IDE (obecnie kodzie VS i PyCharm są obsługiwane) z projektami i bieżącego pliku.  Można także kliknąć strzałkę obok przycisku Edytuj, aby edytować plik w edytorze tekstu Workbench.  Pliki są tylko do odczytu do momentu kliknięcia edycji zapobiec przypadkowym zmianom.
- Popularne biblioteki kreślenia `matplotlib` wersji 2.1.0 teraz jest dostarczany z aplikacją Workbench.
- Wersja platformy .NET Core możemy uaktualniona do wersji 2.0 dla aparatu przygotowanie bazy danych. Wymagania dla biblioteki openssl brew install podczas instalacji aplikacji na macOS to usunięte. Paves również sposób ciekawsze danych przygotowywania funkcje znaleziona w najbliższej przyszłości. 
- Firma Microsoft włączono stronę główną określonej wersji aplikacji, aby uzyskać dokładniejsze informacje o wersji i zaktualizować monitów, w zależności od bieżącej wersji aplikacji.
- Jeśli nazwa użytkownika lokalnego ma miejsce w nim, aplikację można teraz pomyślnie zainstalować. 

## <a name="detailed-updates"></a>Szczegółowe aktualizacji
Poniżej znajduje się lista szczegółowe aktualizacji w obszarze każdego składnika uczenie maszynowe Azure w tym przebiegu.

### <a name="installer"></a>Instalator
- Instalator aplikacji czyści teraz utworzona przez starszą wersję aplikacji katalogu instalacyjnego.
- Stałe usterkę, która prowadzi Instalator gromadzą w skali 100% na macOS Sierra wysoki.
- Jest teraz bezpośredniego łącza do katalogu Instalatora dla użytkownika przejrzeć dzienniki Instalatora, w przypadku, gdy instalacja nie powiedzie się.
- Zainstaluj teraz działa w przypadku użytkowników, którzy mają miejsce w nazwie użytkownika.

### <a name="workbench-authentication"></a>Workbench uwierzytelniania
- Obsługa uwierzytelniania w Menedżerze serwera Proxy.
- Rejestrowanie w teraz zakończy się pomyślnie, jeśli użytkownik znajduje się za zaporą. 
- Jeśli użytkownik ma eksperymenty kont w wielu regionach platformy Azure i jeden region stanie się niedostępna, zawiesza się już aplikacji.
- Podczas uwierzytelniania nie zostanie zakończony i okno dialogowe uwierzytelnianie jest nadal widoczny, aplikacji już nie próbuje załadować obszaru roboczego z lokalnej pamięci podręcznej.

### <a name="workbench-app"></a>Workbench aplikacji
- Wyróżnianie składni kodu Python jest włączona w edytorze tekstu.
- Przycisk Edytuj w edytorze tekstu służy do edytowania pliku w IDE (kodzie VS i PyCharm są obsługiwane) lub w edytorze tekstów wbudowanych.
- Edytor tekstu jest w trybie tylko do odczytu domyślnie. 
- Zapisać zmiany teraz stanu wizualnego przycisk wyłączona po bieżący plik jest zapisany i dlatego nie jest już zanieczyszczone.
- Zapisuje Workbench _wszystkie_ niezapisane pliki po zainicjowaniu przebiegu.
- Workbench pamięta, że ostatni używana obszaru roboczego na komputerze lokalnym, dlatego zostanie otwarty automatycznie.
- Uruchom teraz jest dozwolone tylko jedno wystąpienie Workbench. Wcześniej wielu wystąpień może zostać uruchomiona które spowodowały problemy podczas działania na tym samym projekcie.
- Zmieniono nazwę pliku menu "Otwieranie projektu..." do "Dodaj istniejący Folder jako projekt..." 
- Karta przełączania teraz jest znacznie szybsze.
- Łącza pomocy są dodawane do okna dialogowego Konfigurowanie IDE.
- Formularz opinii o pamięta teraz podany czas ostatniego adres e-mail.
- Uśmiechy i frowns obszaru tekstu formularza jest teraz większe, więc można Prześlij nam opinię więcej! 
- `--owner` Przełącznika tekst pomocy w `az ml workspace create` jest usuwana.
- Dodaliśmy "Informacje o" okno dialogowe, aby pomóc użytkownikowi łatwo Wyświetl i skopiuj numer wersji aplikacji.
- Element menu "Sugeruj funkcji" zostanie dodany do menu Pomoc.
- Nazwa konta eksperymenty jest teraz widoczne w aplikacji na pasku tytułu, poprzedzających "Azure Machine Learning Workbench" Nazwa aplikacji.
- Głównej wersji aplikacji jest wyświetlany teraz na podstawie wersji aplikacji wykryto.

### <a name="data-preparation"></a>Przygotowywanie danych 
- Zewnętrznej witryny sieci web nie mogą być ładowane z Inspektora mapę, aby uniknąć potencjalnych problemów z zabezpieczeniami.
- Inspektorzy histogramu i liczba wartości ma teraz opcję, aby wyświetlić wykres w skali logarytmicznej.
- Podczas obliczania jest wykonywana, pasek jakości danych pojawi się na innym kolorze sygnalizują stan "Obliczanie".
- Kolumna metryki pokazują teraz statystyki dla kolumn wartości kategorii.
- Ostatni znak w nazwie źródła danych nie jest już obcięty.
- Pakiet przygotowanie bazy danych teraz pozostaje otwarty podczas przełączania kart, co zyski zauważalna zmiana wydajności.
- W źródle danych, podczas przełączania się między widokiem danych i metryk już zmienia kolejność kolumn teraz.
- Otwieranie nieprawidłową `.dprep` lub `.dsource` plik już nie powoduje, że Workbench awarii.
- Pakiet przygotowanie bazy danych można teraz używa ścieżka względna dla danych wyjściowych w _zapisu do pliku CSV_ transformacji.
- _Zachowaj kolumny_ przekształcenia teraz zezwala użytkownikowi na dodawanie dodatkowych kolumn podczas edycji.
- _Zastąp to_ menu teraz faktycznie uruchamia _zastąpić wartość_ okno dialogowe.
- _Zastąp wartość_ przekształcenie teraz funkcje zgodnie z oczekiwaniami zamiast zgłaszania błędu.
- Podczas odwoływania się do plików danych poza folderem projektu, dzięki czemu można uruchomić pakiet w kontekście lokalnego ścieżka bezwzględna do pliku danych pakietu przygotowanie bazy danych obecnie korzysta ścieżkę bezwzględną.
- _Pełny plik_ jako strategii próbkowania jest teraz obsługiwana podczas korzystania z usługi Azure blob jako źródła danych.
- Wygenerowany kod języka Python (za pomocą pakietu przygotowanie bazy danych) teraz prowadzi zarówno CR i LF, dzięki czemu przyjazną w systemie Windows.
- _Wybierz metryki_ listy rozwijanej teraz ukrywa właściwości podczas przełączania do widoku danych.
- Workbench może teraz parquet proces pliki nawet wtedy, gdy używane są środowisko uruchomieniowe języka Python. Wcześniej tylko Spark można podczas przetwarzania parquet plików. 
- Filtrowanie wartości w kolumnie z _data_ — typ danych nie jest już powoduje aparatu przygotowanie bazy danych do awarii.
- Widoku metryki uwzględnia teraz próbkowania strategii aktualizacji.
- Teraz próbkowania zadań zdalnego działa prawidłowo.

### <a name="job-execution"></a>Wykonanie zadania
- Argument jest teraz zawarta w rekordzie Historia uruchomień.
- Zadania rozpoczęte CLI teraz zostaną wyświetlone w panelu zadania Historia Uruchom automatycznie.
- Panel zadania zawiera teraz zadań utworzonych przez gościa użytkowników dodanych do dzierżawę usługi AAD.
- Anuluj zadanie panelu i akcje usuwania są bardziej stabilne.
- Po kliknięciu przycisk Uruchom, komunikat o błędzie zostanie wywołany teraz, jeśli pliki konfiguracji mają zły format.
- Trwa przerywanie działania aplikacji już nie zakłóca zadania rozpoczęte w interfejsu wiersza polecenia.
- Zadania rozpoczęte CLI teraz w dalszym ciągu Podziel się poza standardowe nawet po godzinie wykonania.
- Lepsze komunikaty o błędach są wyświetlane, gdy pakiet przygotowanie bazy danych uruchom nie powiedzie się w języku Python/PySpark.
- `az ml experiment clean`teraz czyści obrazy usługi Docker również zdalnej maszynie wirtualnej.
- `az ml experiment clean`teraz działa poprawnie dla lokalnych docelowego na macOS.
- Komunikaty o błędach po uruchomieniu przeznaczonych dla lokalnego lub zdalnego Docker oczyszczeniem w górę i bardziej czytelne.
- Lepsze komunikat o błędzie jest wyświetlane, gdy nazwa węzła głównego klastra usługi HDInsight nie jest prawidłowo sformatowana, gdy dołączony jako obiekt docelowy wykonywania.
- Lepsze komunikat o błędzie jest wyświetlany, jeśli klucz tajny nie znajdują się w usłudze poświadczeń. 
- Biblioteka MMLSpark jest uaktualniany do obsługi Apache Spark 2.2.
- MMLSpark zawierają teraz podmiotu kodowania transformacji (kodowanie siatki) medyczne dokumentów.
- `matplotlib`wersja 2.1.0 jest obecnie dostarczana pole poza z Workbench.

### <a name="jupyter-notebook"></a>Jupyter Notebook
- W widoku notesów wyszukiwania nazwy notesu teraz działa prawidłowo.
- Można teraz usunąć notesu w widoku notesów.
- Nowe magic `%upload_artifact` jest dodanie do przekazywania plików utworzony w środowisku wykonywania notesu do magazynu danych wykonywania historii.
- Błędy jądra są teraz udostępniane w stan zadania notesu ułatwiają debugowanie.
- Serwer Jupyter teraz prawidłowo zamknięty, kiedy użytkownik zaloguje się poza aplikacją.

### <a name="azure-portal"></a>Azure Portal
- Konto eksperymentowania oraz konta zarządzania modelu teraz mogą być tworzone w dwóch nowych regionów platformy Azure: zachodnie Europie i Azji Południowo-Wschodnia.
- Plan DevTest konto zarządzania modelu teraz jest dostępna tylko po pierwsza z nich mogą być tworzone w ramach subskrypcji. 
- Link pomoc w portalu Azure zostaną zmienione na wskaż stronę dokumentacji poprawne.
- Pole opisu jest usuwany z strony szczegółów Docker obrazu, ponieważ nie ma zastosowania.
- Szczegółowe informacje w tym ustawienia AppInsights i automatyczne skalowanie są dodawane do strony szczegółów usługi sieci web.
- Strony Zarządzanie modelu renderuje teraz, nawet jeśli pliki cookie innych firm są wyłączone w przeglądarce. 

### <a name="operationalization"></a>Operationalization
- Usługa sieci Web "wynik" w nazwie już nie kończy się niepowodzeniem.
- Użytkownik może teraz utworzyć środowiska wdrażania właśnie współautora dostęp do grupy zasobów platformy Azure lub subskrypcji. Właściciel dostęp do całej subskrypcji nie jest już potrzebne.
- Operationalization CLI teraz ma kartę automatycznego uzupełniania w systemie Linux.
- Usługa konstrukcji obrazów obsługuje teraz tworzenia obrazów urządzeń/usługi Azure IoT.

### <a name="sample-projects"></a>Przykładowych projektach
- [_Klasyfikowanie Iris_ ](./tutorial-classifying-iris-part-1.md) przykładowy projekt:
    - `iris_pyspark.py`została zmieniona na `iris_spark.py`.
    - `iris_score.py`została zmieniona na `score_iris.py`.
    - `iris.dprep`i `iris.dsource` są aktualizowane w celu uwzględnienia najnowszych aktualizacji aparatu przygotowanie bazy danych.
    - `iris.ipynb`Notesu wprowadza się do pracy w klastrze usługi HDInsight.
    - Historia uruchomień jest włączona w programie `iris.ipynb` komórki notesu.
- [_Zaawansowane przygotowanie bazy danych przy użyciu danych udziału roweru_ ](./tutorial-bikeshare-dataprep.md) przykładowy projekt "Obsługi błędu wartość" krok stałej.
- [_MMLSpark na dane spisu dla dorosłych_ ](https://github.com/Azure/MachineLearningSamples-mmlspark) przykładowy projekt `docker.runconfig` format zaktualizowany z JSON do yaml programu.
- [_Rozproszone dostrajanie Hyperparameter_ ](./scenario-distributed-tuning-of-hyperparameters.md) przykładowy projekt`docker.runconfig` format zaktualizowany z JSON do yaml programu.
- Nowy projekt przykładowy [ _klasyfikacji obrazu przy użyciu CNTK_](./scenario-image-classification-using-cntk.md).
