---
title: What's new in Azure Machine Learning
description: Ten dokument zawiera szczegóły dotyczące aktualizacji do usługi Azure Machine Learning.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.service: machine-learning
ms.workload: data-services
ms.topic: reference
ms.date: 03/28/2018
ms.openlocfilehash: ac08baa6f478926a2c8dadd366049e9506272366
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="whats-new-in-azure-machine-learning"></a>Co nowego w usłudze Azure Machine Learning

W tym artykule, Dowiedz się więcej o nowe funkcje i znane problemy dotyczące [Azure Machine Learning usług](overview-what-is-azure-ml.md). 

## <a name="2018-01-sprint-3"></a>2018-01 (przebiegu 3) 
**Numer wersji**: 0.1.1712.18263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([znaleźć wersji](known-issues-and-troubleshooting-guide.md))

Poniżej przedstawiono aktualizacje i usprawnienia w tym przebiegu. Wiele z tych aktualizacji są wykonywane jako bezpośrednio w wyniku opinie użytkowników. 


Poniżej znajduje się lista szczegółowe aktualizacji w obszarze każdego składnika uczenie maszynowe Azure w tym przebiegu.

- Aktualizacje stosu uwierzytelniania wymusza logowania i konta wybór przy uruchamianiu

### <a name="workbench"></a>Workbench
- Możliwość instalacji/dezinstalacji aplikacji z apletu Dodaj/Usuń programy
- Aktualizacje stosu uwierzytelniania wymusza wyboru logowania i konto podczas rozruchu
- Udoskonalone środowisko pojedynczy znak na rejestracji jednokrotnej (SSO) w systemie Windows
- Użytkownicy, którzy należą do wielu dzierżawców z innymi poświadczeniami teraz będą mogli zalogować się do środowiska roboczego

### <a name="ui"></a>UI
- Ogólne poprawki i poprawki

### <a name="notebooks"></a>Komputery przenośne
- Ogólne poprawki i poprawki

### <a name="data-preparation"></a>Przygotowywanie danych 
- Ulepszone sugestie automatycznie podczas wykonywania przekształcenia według przykładu
- Ulepszone algorytm inspektora częstotliwość wzorca
- Umożliwia wysyłanie przykładowych danych i opinie podczas wykonywania przekształcenia przez przykład ![obrazu wysyłania link opinii na pochodzi przekształcenia kolumny](media/azure-machine-learning-release-notes/SendFeedbackFromDeriveColumn.png)
- Ulepszenia środowiska uruchomieniowego Spark
- Scala zastąpił Pyspark
- Stałe brakiem zamknięcia czas inspektora serii danych nie dotyczy 
- Stały czas zawieszenie wykonywania przygotowanie bazy danych dla HDI

### <a name="model-management-cli-updates"></a>Aktualizuje model administracyjny interfejsu wiersza polecenia 
  - Posiadania subskrypcji nie jest już wymagane do obsługi zasobów. Współautor dostępu do grupy zasobów będą wystarczające, aby skonfigurować środowisko wdrażania.
  - Włączone lokalnego środowiska instalacji bezpłatnej subskrypcji 

## <a name="2017-12-sprint-2-qfe"></a>2017-12 (wypalenie poprawka 2) 
**Numer wersji**: 0.1.1711.15323 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([znaleźć wersji](known-issues-and-troubleshooting-guide.md))

To jest wersja QFE (Quick Fix Engineering), wersji pomocniczej. On rozwiązuje kilka problemów telemetrii i pomaga zespołowi produktu w celu lepszego zrozumienia sposobu używania produktu. Wiedza może przejść do przyszłych wysiłki mające na celu poprawę środowiska produktu. 

Ponadto istnieją dwie ważne aktualizacje:

- Rozwiązane usterki w przygotowanie bazy danych, który uniemożliwił inspektora serii czas wyświetlania w pakietach przygotowywania danych.
- W narzędziu wiersza polecenia nie trzeba być właścicielem subskrypcji platformy Azure do udostępnienia klastrów usługi Machine Learning obliczeniowe ACS. 

## <a name="2017-12-sprint-2"></a>2017-12 (przebiegu 2)
**Numer wersji**: 0.1.1711.15263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([znaleźć wersji](known-issues-and-troubleshooting-guide.md))


Trzeci aktualizacja usługi Azure Machine Learning — Zapraszamy! Ta aktualizacja zawiera ulepszenia w aplikacji workbench, interfejsu wiersza polecenia (CLI) i usługi zaplecza. Dziękujemy wysyłania uśmiechy i frowns. Wiele z następujących aktualizacji są wykonywane jako bezpośrednie wyniki opinii użytkowników. 

**Ważne funkcje**
- [Obsługa programu SQL Server i bazy danych SQL Azure jako źródła danych](data-prep-appendix2-supported-data-sources.md#types) 
- [Głębokie uczenie na Spark z obsługą procesora GPU za pomocą MMLSpark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Wszystkie kontenery AML są zgodne z urządzenia Azure IoT brzegowe po wdrożeniu (żadne dodatkowe czynności wymagane)](http://aka.ms/aml-iot-edge-blog)
- Zarejestrowanego modelu listy i szczegółów widoki dostępne portalu Azure
- Uzyskiwanie dostępu do celów obliczeń przy użyciu uwierzytelniania opartego na kluczach SSH oprócz dostępu opartego na nazwę użytkownika/hasło. 
- Nowy wzorzec inspektora częstotliwość danych przygotowywanie środowiska. 

**Szczegóły aktualizacji** poniżej znajduje się lista szczegółowe aktualizacji w obszarze każdego składnika uczenie maszynowe Azure w tym przebiegu.

### <a name="installer"></a>Instalator
- Instalator może self aktualizacji, który usterek poprawki i nowe funkcje mogą być obsługiwane bez konieczności ponownie ją zainstaluj użytkownika

### <a name="workbench-authentication"></a>Workbench uwierzytelniania
- Wiele poprawek do uwierzytelniania systemu. Prosimy o kontakt Jeśli nadal występują problemy dotyczące logowania.
- Zmiany interfejsu użytkownika, które ułatwiają znajdowanie ustawienia Menedżera serwera Proxy.

### <a name="workbench"></a>Workbench
- Widok tylko do odczytu pliku ma teraz światła niebieskie tło
- Przycisk Edytuj przeniesiony z prawej strony, aby był bardziej wykrywalny.
- formaty plików "ipynb", "dprep" i "dsource" teraz mogą być renderowane w formacie tekstowym pierwotnych
- Workbench ma teraz nowe środowisko edytowania, który przeprowadzi użytkowników do edycji skryptów i użyj Workbench tylko do typów plików, które mają bogate środowisko edycji (na przykład komputery przenośne źródeł danych, pakiety przygotowywania danych) Edytuj przy użyciu zewnętrznego IDEs
- Podczas ładowania listy obszarów roboczych i projekty, do których użytkownik ma dostęp jest znacznie szybsze teraz

### <a name="data-preparation"></a>Przygotowywanie danych 
- Wzorzec Inspektor częstotliwość przeszukiwarki ciągu w kolumnie. Można również filtrować dane przy użyciu tych wzorców. To pokazuje widok podobny do Inspektora wartość liczby. Różnica polega na tym, że wzorzec częstotliwość zawiera informacje o liczbie unikatowych wzorców danych, a nie liczby unikatowych danych. Można również filtrować przychodzący lub wychodzący wszystkie wiersze, które pasują do wzorca niektórych.

![Obraz inspektora częstotliwość wzorzec na numer produktu](media/azure-machine-learning-release-notes/pattern-inspector-product-number.png)

- Ulepszenia wydajności podczas rekomendowania przypadków krawędzi, aby przejrzeć w transformacji "pochodzi kolumny przykładzie"

- [Obsługa programu SQL Server i bazy danych SQL Azure jako źródła danych](data-prep-appendix2-supported-data-sources.md#types) 

![Obraz tworzenia nowego źródła danych serwera SQL](media/azure-machine-learning-release-notes/sql-server-data-source.png)

- Włączone widok "w skrócie" liczby wierszy i kolumn

![Obraz wiersza liczba kolumn w prolongaty](media/azure-machine-learning-release-notes/row-col-count.png)

- Przygotowanie bazy danych jest włączona we wszystkich kontekstach obliczeń
- Źródła danych, które używają bazy danych programu SQL Server są włączone we wszystkich kontekstach obliczeń
- Dane przygotowywanie siatki, które kolumny można filtrować według typu danych
- Rozwiązany problem z konwertowaniem wiele kolumn do daty
- Rozwiązany problem ten użytkownik może zaznaczenie kolumny wyjściowej jako źródło w pochodzi kolumny według przykładu użytkownik zmienił nazwy kolumny wyjściowej w trybie zaawansowanym.

### <a name="job-execution"></a>Wykonanie zadania
Teraz można tworzyć i dostęp do klastra lub remotedocker cel obliczeń typu przy użyciu uwierzytelniania opartego na kluczach SSH wykonaj następujące czynności:
- Dołączanie elementu docelowego obliczeń przy użyciu następującego polecenia w interfejsu wiersza polecenia

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
>[!NOTE]
>Opcja -k (lub--użyj — uczenie maszynowe Azure-— klucz ssh), w poleceniu określa generowanie i używanie klucza SSH.

- Azure ML Workbench wygenerowania klucza publicznego i wyjściowy, który w konsoli. Zaloguj się do obiektu docelowego obliczeń przy użyciu tej samej nazwy użytkownika i Dołącz plik ~/.ssh/authorized_keys kluczem publicznym.

- Można przygotować ten element docelowy obliczeń i użyć jej do wykonania i Workbench uczenia Maszynowego Azure będzie używać tego klucza do uwierzytelniania.  

Aby uzyskać więcej informacji na temat tworzenia elementów docelowych obliczeń, zobacz [Konfigurowanie Azure Machine Learning eksperymenty usługi](experimentation-service-configuration.md)

### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools for AI
- Dodano obsługę [Visual Studio Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017). 

### <a name="command-line-interface-cli"></a>Interfejs wiersza polecenia (CLI)
- Dodaje `az ml datasource create` polecenie umożliwia utworzenie pliku źródła danych z wiersza polecenia

### <a name="model-management-and-operationalization"></a>Zarządzanie modelami i Operationalization
- [Wszystkie kontenery AML są zgodne z urządzenia Azure IoT brzegowe podczas operationalized (żadne dodatkowe czynności wymagane)](http://aka.ms/aml-iot-edge-blog) 
- Ulepszenia komunikatów o błędach w o16n interfejsu wiersza polecenia
- Poprawki błędów w portalu zarządzania modelu środowiska użytkownika  
- Wielkość liter spójne litery model zarządzania atrybutów na stronie szczegółów
- Ocenianie limitu czasu połączenia w czasie rzeczywistym ustawioną 60 sekund
- Zarejestrowanego modelu listy i szczegółów widoków dostępne w portalu Azure

![Szczegóły modelu w portalu](media/azure-machine-learning-release-notes/model-list.jpg)

![Omówienie modelu w portalu](media/azure-machine-learning-release-notes/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- Bezpośrednie Learning na Spark z [Obsługa procesora GPU](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Obsługa szablonów usługi Resource Manager łatwe zasobu wdrożenia
- Obsługa ekosystem SparklyR
- [Integracja AZTK](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>Przykładowych projektach
- [Iris](https://github.com/Azure/MachineLearningSamples-Iris) i [MMLSpark](https://github.com/Azure/mmlspark) przykłady zaktualizowane o nową wersję zestawu SDK usługi Azure ML

### <a name="breaking-changes"></a>Fundamentalne zmiany
- Poziom jest podwyższany `--type` przełącznika w `az ml computetarget attach` do podpolecenia. 

    - `az ml computetarget attach --type remotedocker` jest teraz `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` jest teraz `az ml computetarget attach cluster`

## <a name="2017-11-sprint-1"></a>2017-11 (przebiegu 1) 
**Numer wersji**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([znaleźć wersji](known-issues-and-troubleshooting-guide.md))

W tej wersji poprawiono dotyczących zabezpieczeń, stabilność i łatwości w aplikacji workbench, interfejsu wiersza polecenia i warstwy usług zaplecza. Dziękujemy za przesłanie uśmiechy i frowns. Duża liczba poniżej aktualizacje są wprowadzane jako bezpośrednie wyniki swoją opinię. Przechowywanie ich wejścia!

### <a name="notable-new-features"></a>Ważne funkcje
- Uczenie Maszynowe Azure jest obecnie dostępna w dwóch nowych regionów platformy Azure: **Europa** i **Azja południowo-wschodnia**. Dołączenia poprzedniej regionów **wschodnie stany USA 2**, **zachodnie środkowe stany**, i **Australia Wschodnia**, przywracanie łączna liczba wdrożonych regionów pięć.
- Firma Microsoft włączona Składnia kodu Python wyróżnianie w aplikacji Workbench, aby ułatwić odczyt i Edycja kodu źródłowego języka Python. 
- Można teraz uruchomić Twoje ulubione IDE bezpośrednio z pliku, a nie z całego projektu.  Otwarcie pliku w Workbench, a następnie klikając pozycję "Edytuj" uruchamia środowiskiem IDE (obecnie kodzie VS i PyCharm są obsługiwane) z projektami i bieżącego pliku.  Można także kliknąć strzałkę obok przycisku Edytuj, aby edytować plik w edytorze tekstu Workbench.  Pliki są tylko do odczytu do momentu kliknięcia edycji zapobiec przypadkowym zmianom.
- Popularne biblioteki kreślenia `matplotlib` wersji 2.1.0 teraz jest dostarczany z aplikacją Workbench.
- Wersja platformy .NET Core możemy uaktualniona do wersji 2.0 dla aparatu przygotowanie bazy danych. Wymagania dla biblioteki openssl brew install podczas instalacji aplikacji na macOS to usunięte. Paves również sposób ciekawsze danych przygotowywania funkcje znaleziona w najbliższej przyszłości. 
- Firma Microsoft włączono stronę główną określonej wersji aplikacji, aby uzyskać dokładniejsze informacje o wersji i zaktualizować monitów, w zależności od bieżącej wersji aplikacji.
- Jeśli nazwa użytkownika lokalnego ma miejsce w nim, aplikację można teraz pomyślnie zainstalować. 

### <a name="detailed-updates"></a>Szczegółowe aktualizacji
Poniżej znajduje się lista szczegółowe aktualizacji w obszarze każdego składnika uczenie maszynowe Azure w tym przebiegu.

#### <a name="installer"></a>Instalator
- Instalator aplikacji czyści teraz utworzona przez starszą wersję aplikacji katalogu instalacyjnego.
- Stałe usterkę, która prowadzi Instalator gromadzą w skali 100% na macOS Sierra wysoki.
- Jest teraz bezpośredniego łącza do katalogu Instalatora dla użytkownika przejrzeć dzienniki Instalatora, w przypadku, gdy instalacja nie powiedzie się.
- Zainstaluj teraz działa w przypadku użytkowników, którzy mają miejsce w nazwie użytkownika.

#### <a name="workbench-authentication"></a>Workbench uwierzytelniania
- Obsługa uwierzytelniania w Menedżerze serwera Proxy.
- Rejestrowanie w teraz zakończy się pomyślnie, jeśli użytkownik znajduje się za zaporą. 
- Jeśli użytkownik ma eksperymenty kont w wielu regionach platformy Azure i jeden region stanie się niedostępna, zawiesza się już aplikacji.
- Podczas uwierzytelniania nie zostanie zakończony i okno dialogowe uwierzytelnianie jest nadal widoczny, aplikacji już nie próbuje załadować obszaru roboczego z lokalnej pamięci podręcznej.

#### <a name="workbench-app"></a>Workbench aplikacji
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

#### <a name="data-preparation"></a>Przygotowywanie danych 
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

#### <a name="job-execution"></a>Wykonanie zadania
- Argument jest teraz zawarta w rekordzie Historia uruchomień.
- Zadania rozpoczęte CLI teraz zostaną wyświetlone w panelu zadania Historia Uruchom automatycznie.
- Panel zadania zawiera teraz zadań utworzonych przez gościa użytkowników dodanych do dzierżawy usługi Azure AD.
- Anuluj zadanie panelu i akcje usuwania są bardziej stabilne.
- Po kliknięciu przycisk Uruchom, komunikat o błędzie zostanie wywołany teraz, jeśli pliki konfiguracji mają zły format.
- Trwa przerywanie działania aplikacji już nie zakłóca zadania rozpoczęte w interfejsu wiersza polecenia.
- Zadania rozpoczęte CLI teraz w dalszym ciągu Podziel się poza standardowe nawet po godzinie wykonania.
- Lepsze komunikaty o błędach są wyświetlane, gdy pakiet przygotowanie bazy danych uruchom nie powiedzie się w języku Python/PySpark.
- `az ml experiment clean` teraz czyści obrazy usługi Docker również zdalnej maszynie wirtualnej.
- `az ml experiment clean` teraz działa poprawnie dla lokalnych docelowego na macOS.
- Komunikaty o błędach po uruchomieniu przeznaczonych dla lokalnego lub zdalnego Docker oczyszczeniem w górę i bardziej czytelne.
- Lepsze komunikat o błędzie jest wyświetlane, gdy nazwa węzła głównego klastra usługi HDInsight nie jest prawidłowo sformatowana, gdy dołączony jako obiekt docelowy wykonywania.
- Lepsze komunikat o błędzie jest wyświetlany, jeśli klucz tajny nie znajdują się w usłudze poświadczeń. 
- Biblioteka MMLSpark jest uaktualniany do obsługi Apache Spark 2.2.
- MMLSpark zawierają teraz podmiotu kodowania transformacji (kodowanie siatki) medyczne dokumentów.
- `matplotlib` wersja 2.1.0 jest obecnie dostarczana pole poza z Workbench.

#### <a name="jupyter-notebook"></a>Jupyter Notebook
- W widoku notesów wyszukiwania nazwy notesu teraz działa prawidłowo.
- Można teraz usunąć notesu w widoku notesów.
- Nowe magic `%upload_artifact` jest dodanie do przekazywania plików utworzony w środowisku wykonywania notesu do magazynu danych wykonywania historii.
- Błędy jądra są teraz udostępniane w stan zadania notesu ułatwiają debugowanie.
- Serwer Jupyter teraz prawidłowo zamknięty, kiedy użytkownik zaloguje się poza aplikacją.

#### <a name="azure-portal"></a>Azure Portal
- Konto eksperymentowania oraz konta zarządzania modelu teraz mogą być tworzone w dwóch nowych regionów platformy Azure: zachodnie Europie i Azji Południowo-Wschodnia.
- Plan DevTest konto zarządzania modelu teraz jest dostępna tylko po pierwsza z nich mogą być tworzone w ramach subskrypcji. 
- Link pomoc w portalu Azure zostaną zmienione na wskaż stronę dokumentacji poprawne.
- Pole opisu jest usuwany z strony szczegółów Docker obrazu, ponieważ nie ma zastosowania.
- Szczegółowe informacje w tym ustawienia AppInsights i automatyczne skalowanie są dodawane do strony szczegółów usługi sieci web.
- Strony Zarządzanie modelu renderuje teraz, nawet jeśli pliki cookie innych firm są wyłączone w przeglądarce. 

#### <a name="operationalization"></a>Operationalization
- Usługa sieci Web "wynik" w nazwie już nie kończy się niepowodzeniem.
- Użytkownik może teraz utworzyć środowiska wdrażania właśnie współautora dostęp do grupy zasobów platformy Azure lub subskrypcji. Właściciel dostęp do całej subskrypcji nie jest już potrzebne.
- Operationalization CLI teraz ma kartę automatycznego uzupełniania w systemie Linux.
- Usługa konstrukcji obrazów obsługuje teraz tworzenia obrazów urządzeń/usługi Azure IoT.

#### <a name="sample-projects"></a>Przykładowych projektach
- [_Klasyfikowanie Iris_ ](./tutorial-classifying-iris-part-1.md) przykładowy projekt:
    - `iris_pyspark.py` została zmieniona na `iris_spark.py`.
    - `iris_score.py` została zmieniona na `score_iris.py`.
    - `iris.dprep` i `iris.dsource` są aktualizowane w celu uwzględnienia najnowszych aktualizacji aparatu przygotowanie bazy danych.
    - `iris.ipynb` Notesu wprowadza się do pracy w klastrze usługi HDInsight.
    - Historia uruchomień jest włączona w programie `iris.ipynb` komórki notesu.
- [_Zaawansowane przygotowanie bazy danych przy użyciu danych udziału roweru_ ](./tutorial-bikeshare-dataprep.md) przykładowy projekt "Obsługi błędu wartość" krok stałej.
- [_MMLSpark na dane spisu dla dorosłych_ ](https://github.com/Azure/MachineLearningSamples-mmlspark) przykładowy projekt `docker.runconfig` format zaktualizowany z JSON do yaml programu.
- [_Rozproszone dostrajanie Hyperparameter_ ](./scenario-distributed-tuning-of-hyperparameters.md) przykładowy projekt`docker.runconfig` format zaktualizowany z JSON do yaml programu.
- Nowy projekt przykładowy [ _klasyfikacji obrazu przy użyciu CNTK_](./scenario-image-classification-using-cntk.md).


## <a name="2017-10-sprint-0"></a>2017 10 (przebiegu 0) 
**Numer wersji**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([znaleźć wersji](known-issues-and-troubleshooting-guide.md))

Witamy w pierwszej aktualizacji Azure Machine Learning środowiska roboczego po naszej początkowej publicznej wersji zapoznawczej na konferencji Ignite firmy Microsoft w 2017 r. Główne aktualizacje w tej wersji są niezawodność i stabilizacji poprawki.  Krytyczne problemy, które rozwiązaliśmy między innymi:

### <a name="new-features"></a>Nowe funkcje
- System macOS wysokiej Sierra obecnie jest obsługiwany.

### <a name="bug-fixes"></a>Poprawki błędów
#### <a name="workbench-experience"></a>Środowisko robocze
- Przeciąganie i upuszczanie plików do narzędzia Workbench powoduje, że Workbench awarię.
- Okno terminalu w kodzie VS skonfigurowane jako IDE Workbench nie rozpoznaje _az ml_ poleceń.

#### <a name="workbench-authentication"></a>Workbench uwierzytelniania
Wprowadziliśmy wiele aktualizacje do poprawy różnych uwierzytelniania logowania i problemach raportowanych.
- Okno uwierzytelniania zachowuje wyświetlanie w górę, zwłaszcza w przypadku połączenia internetowego nie jest stabilna.
- Problemy z większą niezawodność wokół wygaśnięcia tokenu uwierzytelniania.
- W niektórych przypadkach okno uwierzytelniania występuje dwa razy.
- Okno główne Workbench nadal wyświetla komunikat "uwierzytelnianie" po zakończeniu procesu uwierzytelniania i wyświetlonym oknie dialogowym już odrzucono.
- W przypadku połączenia internetowego, dialog uwierzytelniania pojawia się pusty ekran.

#### <a name="data-preparation"></a>Przygotowywanie danych 
- Jeśli określona wartość jest filtrowany, błędy i brakujące wartości są również odfiltrowane.
- Zmiana strategii próbkowania usuwa kolejnych istniejące operacje sprzężenia.
- Zastąpienie wartości Brak przekształcenie nie uwzględnia NaN.
- Wnioskowanie o typie data zgłasza wyjątek w przypadku wartości null.

#### <a name="job-execution"></a>Wykonanie zadania
- Nie ma wyczyść błędu podczas wykonywania zadania nie powiedzie się załadować folderu projektu, ponieważ został przekroczony limit rozmiaru.
- Jeśli skrypt w języku Python użytkownika zmieni się katalog roboczy, pliki zapisywane do folderów dane wyjściowe nie są śledzone. 
- Jeśli aktywną subskrypcją platformy Azure jest inna niż ten, który należy do bieżącego projektu, przesyłanie zadań powoduje błąd 403.
- Gdy Docker nie jest obecny, żaden komunikat wyczyść błąd jest zwracany, gdy użytkownik próbuje użyć Docker jako obiekt docelowy wykonywania.
- plik .runconfig nie jest zapisywany automatycznie, gdy użytkownik kliknie _Uruchom_ przycisku.

#### <a name="jupyter-notebook"></a>Jupyter Notebook
- Nie można uruchomić serwera notesu, jeśli użytkownik korzysta z określonymi typami logowania.
- Komunikaty o błędach notesu serwera nie powierzchni w dziennikach widoczny dla użytkownika.

#### <a name="azure-portal"></a>Azure Portal
- Zaznaczenie ciemnym motywem portalu Azure powoduje, że blok zarządzania modelu, który będzie wyświetlany jako czarne pole.

#### <a name="operationalization"></a>Operationalization
- Ponowne wykorzystywanie manifestu do aktualizowania usługi sieci web powoduje, że nowy obraz Docker wbudowane losowe nazwą.
- Dzienniki usługi sieci Web nie można pobrać z Kubernetes klastra.
- Błąd komunikat o błędzie jest drukowany, gdy użytkownik próbuje utworzyć konto zarządzania modelu lub konto obliczeniowe ML i napotkał problemy z uprawnieniami.

## <a name="next-steps"></a>Kolejne kroki

Zapoznanie się z omówieniem dla [usługi Azure Machine Learning](overview-what-is-azure-ml.md).