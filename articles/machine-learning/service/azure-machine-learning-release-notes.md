---
title: What's new in Azure Machine Learning
description: W tym dokumencie przedstawiono aktualizacji do usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
author: hning86
ms.author: haining
ms.reviewer: j-martens
ms.date: 03/28/2018
ms.openlocfilehash: be12a9ef0d6b520c741f95903d43f81727bc3b9b
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238014"
---
# <a name="azure-machine-learning-service-release-notes"></a>Informacje o wersji usługi Azure Machine Learning

Ten artykuł zawiera informacje o wersji usługi Azure Machine Learning. 

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v0.1.65
[Wersja 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) zawiera nowe funkcje, więcej dokumentacji, poprawki i [przykładowy notesów](https://aka.ms/aml-notebooks).

Zobacz [listę znanych problemów](resource-known-issues.md) informacje na temat znanych błędów i rozwiązania problemu.

#### <a name="breaking-changes"></a>Zmiany powodujące niezgodność
 * Workspace.experiments, Workspace.models, Workspace.compute_targets Workspace.images Workspace.web_services słownika zwrotu, poprzednio zwróconej liście. Zobacz [azureml.core.Workspace](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) dokumentacji interfejsu API.

 * Automatyczne usługi Machine Learning usunąć błąd znormalizowane średniej kwadratowej z podstawowe metryki.


#### <a name="hyperdrive"></a>HyperDrive
 * Poprawki błędów różnych HyperDrive Bayesowskie, ulepszenia wydajności dotyczące pobrania wywołania metryki. 
 * Uaktualnienie Tensorflow 1.10 1.9 
 * Optymalizacja obrazów platformy docker do zimnego. 
 * Zadania użytkownika teraz prawidłowy stan raportu nawet wtedy, gdy zamykania z powodu błędu kodu inne niż 0. 
 * Sprawdzanie poprawności atrybutów RunConfig w zestawie SDK. 
 * Obiekt HyperDrive Uruchom obsługuje anulowanie podobne do regularnego wykonywania: nie trzeba przekazywać żadnych parametrów. 
 * Widżet ulepszenia dotyczące utrzymania stan z listy rozwijanej wartości dla rozproszonej przebiegów oraz uruchomień HyperDrive. 
 * Narzędzia TensorBoard i innych dziennik, który obsługuje pliki Naprawiono parametr serwera. 
 * Obsługa MPI Intel(R) po stronie usługi. 
 * Poprawka do szczegółowego dostosowywania parametrów dla rozproszonego wykonywania awarii podczas sprawdzania poprawności w BatchAI. 
 * Kontekst Manager identyfikuje teraz podstawowe wystąpienie. 

#### <a name="azure-portal-experience"></a>Środowisku witryny Azure portal
 * log_table() i log_row() są obsługiwane w szczegóły przebiegu. 
 * Automatycznie twórz wykresy, tabele i wiersze 1,2 lub 3 kolumny liczbowe z opcjonalna kolumna podzielonych na kategorie.

#### <a name="automated-machine-learning"></a>Automatyczne Machine Learning
 * Ulepszona obsługa błędów i dokumentacji 
 * Naprawiono pobieranie właściwości uruchamiania problemy z wydajnością. 
 * Naprawiono kontynuować wykonywania problem. 
 * Rozwiązano ensembling problemy iteracji.
 * Naprawiono szkolenia zwisa błędów w systemie MAC OS.
 * Próbkowanie — makro średni żądania Ściągnięcia/WIELOKLASOWA krzywa w scenariuszu niestandardowego sprawdzania poprawności.
 * Usunięty indeks dodatkowej logiki.
 * Usunąć filtr z get_output interfejsu API.

#### <a name="pipelines"></a>Potoki
 * Dodaje metody Pipeline.publish() publikowanie potoku bezpośrednio, bez konieczności wykonywania są uruchamiane jako pierwsze.   
 * Dodano metoda PipelineRun.get_pipeline_runs() można pobrać potoku zostanie uruchomiona, które zostały wygenerowane z opublikowanych potoku.

#### <a name="project-brainwave"></a>Project Brainwave
 * Zaktualizowano obsługę na układów FPGA nowe modele sztucznej Inteligencji.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Zestaw SDK v0.2.0 przeznaczonego do przygotowania danych usługi Azure Machine Learning
[Wersja 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) obejmuje następujące funkcje i poprawki błędów:

**Nowe funkcje:** 
 * Obsługa hot jeden kodowania
 * Obsługa przekształcania kwantyl
   
**Usterka naprawiona:**
 * Działa z dowolną wersją Tornado nie ma potrzeby obniżyć wersji Tornado
 * Liczba wartości dla wszystkich wartości nie tylko trzy pierwsze

## <a name="2018-09-public-preview-refresh"></a>2018-09 (w wyniku odświeżenia publiczna wersja zapoznawcza)

Element nowych, całkowicie odświeżane wersji usługi Azure Machine Learning: Dowiedz się więcej o tej wersji: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/

## <a name="older-notes-sept-2017---jun-2018"></a>Informacje o starszych: września 2017 — cze 2018 r.
### <a name="2018-05-sprint-5"></a>2018-05 (sprint 5)

W tej wersji usługi Azure Machine Learning możesz wykonywać następujące czynności:
+ Cechowanie obrazów przy użyciu wersji wykonywanie kwantyzowanych 50 siecią ResNet szkolenie klasyfikatora na podstawie tych funkcji i [wdrażanie modelu na FPGA na platformie Azure](../service/how-to-deploy-fpga-web-service.md) dla wnioskowania bardzo niskimi opóźnieniami.

+ Szybkie tworzenie i wdrażanie bardzo dokładnych machine learning i modeli uczenia głębokiego przy użyciu [niestandardowego uczenia pakiety usługi Azure Machine](../desktop-workbench/reference-python-package-overview.md) dla następujących domen:
  + [Przetwarzanie obrazów](../desktop-workbench/how-to-build-deploy-image-classification-models.md)
  + [Analiza tekstu](../desktop-workbench/how-to-build-deploy-text-classification-models.md)
  + [Prognozowanie](../desktop-workbench/how-to-build-deploy-forecast-models.md)

### <a name="2018-03-sprint-4"></a>2018-03 (sprint 4)
**Numer wersji**: 0.1.1801.24353 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([Znajdź swoją wersję](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))


Wiele następujące aktualizacje są wprowadzane jako bezpośredni wynik opinii. Proszę postanowieniem!

**Istotne nowe funkcje i zmiany**

- Pomoc techniczna dotycząca działania skryptów w zdalnym maszyny wirtualne systemu Ubuntu natywnie w środowisku oprócz platformy docker na zdalnej na podstawie wykonywania.
- Nowe środowisko środowiska w aplikacji Workbench umożliwia tworzenie obliczeniowych elementów docelowych i uruchomionych konfiguracjach oprócz naszego środowiska na podstawie interfejsu wiersza polecenia.
![Karta środowiska](media/azure-machine-learning-release-notes/environment-page.png)
- Raporty można dostosowywać historii uruchamiania ![obraz nowe Uruchom raporty dotyczące historii](media/azure-machine-learning-release-notes/new-run-history-reports.png)

**Szczegółowe aktualizacji**

Poniżej przedstawiono listę szczegółowych aktualizacji w każdym obszarze składników usługi Azure Machine Learning, w tym sprincie.

#### <a name="workbench-ui"></a>Interfejs użytkownika w aplikacji Workbench
- Można dostosować raporty historii uruchamiania
  - Konfiguracja wykresu ulepszone raporty historii uruchamiania
    - Można zmienić używane punkty wejścia
    - Filtry najwyższego poziomu, które mogą być dodawane lub zmodyfikować ![Dodaj filtry](media/azure-machine-learning-release-notes/add-filters.jpg)
    - Wykresy i statystyki mogą być dodane lub zmodyfikowane (i przeciągnij i upuść nieco inaczej rozmieszczone).
    ![Tworzenie nowych wykresów](media/azure-machine-learning-release-notes/configure-charts.png)

  - CRUD dla historii uruchamiania raportów
  - Przenieść wszystkie istniejące widoku listy historii uruchamiania konfiguracje raportów po stronie serwera, który zachowuje się jak potoków w uruchamia z punktów wejścia wybrane.

- Karta środowiska
  - Możesz łatwo dodawać nowemu celowi obliczenia i uruchamiać pliki konfiguracji do projektu ![nowy cel obliczenia](media/azure-machine-learning-release-notes/add-new-environments.png)
  - Aktualizowanie plików konfiguracji przy użyciu prostego, oparte na formularzach środowiska użytkownika i zarządzanie nimi
  - Nowy przycisk do przygotowania środowiska do wykonania

- Ulepszenia wydajności do listy plików, na pasku bocznym

#### <a name="data-preparation"></a>Przygotowywanie danych 
- Usługa Azure Machine Learning Workbench umożliwia teraz mieć możliwość wyszukiwania przy użyciu znanych kolumny nazwy kolumny.


#### <a name="experimentation"></a>Eksperymentowanie
- Usługa Azure Machine Learning Workbench obsługuje teraz uruchamianie skryptów natywnie we własnym środowisku python lub pyspark. Dla tej funkcji użytkownik tworzy i zarządza własnym środowisku na zdalnej maszynie Wirtualnej i użyj usługi Azure Machine Learning Workbench, aby uruchomić te skrypty w tym miejscu docelowym. Zobacz [konfigurowania Azure usługi Machine Learning eksperymentowania](../desktop-workbench/experimentation-service-configuration.md) 

#### <a name="model-management"></a>Zarządzanie modelami
- Obsługa dostosowywania wdrożone kontenery: umożliwia dostosowanie obrazu kontenera, umożliwiając instalacji zewnętrznych bibliotekach przy użyciu polecenia apt-get, itp. Nie jest już ograniczona do bibliotek do zainstalowania narzędzia pip. Zobacz [dokumentacji](../desktop-workbench/model-management-custom-container.md) Aby uzyskać więcej informacji.
  - Użyj `--docker-file myDockerStepsFilename` flagę i nazwa pliku z manifestu obrazu i poleceń do tworzenia usługi.
  - Należy pamiętać, że obraz podstawowy jest Ubuntu i nie może być modyfikowany.
  - Przykładowe polecenie: 
  
      ```shell
      $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
      ```



### <a name="2018-01-sprint-3"></a>2018-01 (sprint 3) 
**Numer wersji**: 0.1.1712.18263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([Znajdź swoją wersję](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Poniżej przedstawiono aktualizacji i ulepszeń, w tym sprincie. Większość tych aktualizacji są wprowadzane jako bezpośredni wynik opinii użytkowników. 


Poniżej przedstawiono listę szczegółowych aktualizacji w każdym obszarze składników usługi Azure Machine Learning, w tym sprincie.

- Aktualizacje stosu uwierzytelniania wymusza wyboru logowania i konta przy uruchamianiu

#### <a name="workbench"></a>Workbench
- Możliwość instalowania/odinstalowywania aplikacji Dodaj/Usuń programy
- Aktualizacje stosu uwierzytelniania wymusza wyboru logowania i konta podczas rozruchu
- Ulepszone środowisko logowanie jednokrotne (SSO) w Windows
- Użytkownicy, którzy należą do wielu dzierżaw przy użyciu różnych poświadczeń teraz będzie można zalogować się do aplikacji Workbench

#### <a name="ui"></a>UI
- Ogólne ulepszenia i poprawki błędów

#### <a name="notebooks"></a>Notesów
- Ogólne ulepszenia i poprawki błędów

#### <a name="data-preparation"></a>Przygotowywanie danych 
- Ulepszone automatyczne podpowiedzi podczas wykonywania przekształcenia według przykładu
- Ulepszone algorytm Inspektor częstotliwość wzorzec
- Umożliwia wysyłanie przykładowych danych i opinie podczas wykonywania przekształcenia według przykładu ![obraz Wyślij link opinii na pochodzić Przekształć kolumny](media/azure-machine-learning-release-notes/SendFeedbackFromDeriveColumn.png)
- Ulepszenia środowiska uruchomieniowego platformy Spark
- Scala została zastąpiona Pyspark
- Naprawiono Brak możliwości zamknięcia Inspektor serii czasu danych nie ma zastosowania 
- Stały czas zawieszenie wykonywania przygotowania bazy danych dla usługi HDI

#### <a name="model-management-cli-updates"></a>Aktualizuje model zarządzania interfejsu wiersza polecenia 
  - Własności subskrypcji nie jest już wymagane do obsługi administracyjnej zasobów. Prawa dostępu współautora do grupy zasobów okażą się wystarczające skonfigurować środowisko wdrażania.
  - Włączone środowisko lokalne za darmo Konfigurowanie subskrypcji 

### <a name="2017-12-sprint-2-qfe"></a>2017-12 (przebieg 2 (QFE)) 
**Numer wersji**: 0.1.1711.15323 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([Znajdź swoją wersję](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Jest to wersja QFE (Quick Fix Engineering), wersję pomocniczą. Ona rozwiązuje kilka problemów telemetrii i pomaga zespół pracujący nad produktem do lepszego zrozumienia sposobu używania produktu. Baza wiedzy można przejść do przyszłych działań mających na celu podniesienia jakości obsługi produktu. 

Ponadto istnieją dwie ważne aktualizacje:

- Usunięto usterkę w przygotowywanie danych, który uniemożliwił Inspektor serii czasu wyświetlania w pakietów przygotowywania danych.
- W narzędziu wiersza polecenia już nie musisz być właścicielem subskrypcji platformy Azure, inicjować obsługę klastrów Machine Learning obliczenia ACS. 

### <a name="2017-12-sprint-2"></a>2017-12 (sprint 2)
**Numer wersji**: 0.1.1711.15263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([Znajdź swoją wersję](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Zapraszamy do zapoznania się z trzeciego aktualizacji usługi Azure Machine Learning. Ta aktualizacja zawiera ulepszenia w aplikacji workbench, interfejsu wiersza polecenia (CLI) i usług zaplecza. Dziękujemy wysyłania radosną twarz i frowns. Wiele następujące aktualizacje są wprowadzane jako bezpośredni wynik opinii. 

**Istotne nowe funkcje**
- [Obsługa programu SQL Server i bazą danych SQL Azure jako źródło danych](../desktop-workbench/data-prep-appendix2-supported-data-sources.md#types) 
- [Uczenia głębokiego na platformie Spark z obsługą procesorów GPU przy użyciu MMLSpark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Wszystkie kontenery AML są zgodne z usługą Azure IoT Edge po wdrożeniu (nie dodatkowe kroki konieczne)](http://aka.ms/aml-iot-edge-blog)
- Zarejestrowanego modelu listy i szczegółów widoki dostępne witryny Azure portal
- Uzyskiwanie dostępu do celów obliczeń przy użyciu uwierzytelniania opartego na kluczach SSH, oprócz dostępu w oparciu o nazwę użytkownika/hasła. 
- Nowe Inspektor częstotliwość wzorca danych przeznaczonego do przygotowania środowiska. 

**Szczegóły aktualizacji** poniżej przedstawiono listę szczegółowych aktualizacji w każdym obszarze składników usługi Azure Machine Learning, w tym sprincie.

#### <a name="installer"></a>Instalator
- Instalator może samodzielnej aktualizacji, więc usterek, poprawki i nowe funkcje mogą być obsługiwane bez konieczności jego ponowną instalację przez użytkownika

#### <a name="workbench-authentication"></a>Uwierzytelnianie aplikacji Workbench
- Wiele poprawek do systemu uwierzytelniania. Daj nam znać Jeśli nadal występują problemy podczas logowania.
- Zmiany interfejsu użytkownika, które ułatwiają znalezienie ustawień serwera Proxy menedżera.

#### <a name="workbench"></a>Workbench
- Widok tylko do odczytu pliku ma teraz światła niebieskie tło
- Przycisk Edytuj przeniesione z prawej strony, aby był bardziej wykrywalny.
- formatów plików "ipynb", "dprep" i "dsource" teraz mogą być renderowane w tekście nieprzetworzonym formatu
- Usługa workbench ma teraz nowe środowisko edycji, który prowadzi użytkowników kierunku za pomocą zewnętrznego środowiska IDE, aby edytować skrypty i użyj środowiska roboczego tylko po to, aby edytować typy plików, które mają bogate środowisko edytowania (na przykład notesów, źródła danych, pakietów przygotowywania danych)
- Ładowanie listy obszary robocze oraz projekty, do których użytkownik ma dostęp do jest znacznie szybsze teraz

#### <a name="data-preparation"></a>Przygotowywanie danych 
- Wzorzec Inspektor częstotliwość przeszukiwarki ciągu w kolumnie. Można również filtrować dane przy użyciu tych wzorców. To pokazuje widok podobne liczba wartości w Inspektorze. Różnica polega na tym, że częstotliwość wzorzec zawiera liczby unikatowe wzorce w danych, a nie liczby unikatowych danych. Można również filtrować wewnątrz lub na zewnątrz wszystkie wiersze, które są zgodne ze wzorcem niektórych.

![Obraz inspektora częstotliwość wzorca na numer produktu](media/azure-machine-learning-release-notes/pattern-inspector-product-number.png)

- Lepsza wydajność podczas rekomendowania przypadki brzegowe, aby zapoznać się w transformacji "Utwórz kolumnę pochodną według przykładu"

- [Obsługa programu SQL Server i bazą danych SQL Azure jako źródło danych](../desktop-workbench/data-prep-appendix2-supported-data-sources.md#types) 

![Obraz tworzenia nowego źródła danych serwera SQL](media/azure-machine-learning-release-notes/sql-server-data-source.png)

- Włączona "w mgnieniu oka" Widok liczby wierszy i kolumn

![Obraz wiersza liczba kolumn w prolongaty](media/azure-machine-learning-release-notes/row-col-count.png)

- Przygotowywanie danych jest włączona we wszystkich kontekstach obliczeń
- Źródła danych, które używają bazy danych programu SQL Server są włączone we wszystkich kontekstach obliczeń
- Siatki, które można filtrować według typu danych kolumny przeznaczonego do przygotowania danych
- Rozwiązano problem z konwertowaniem wiele kolumn do daty
- Rozwiązano problem polegający na ten użytkownik może wybrać kolumnę wyjściową jako źródło w pochodzić kolumny według przykładu jeśli użytkownik zmienił nazwy kolumny wyjściowej w trybie zaawansowanym.

#### <a name="job-execution"></a>Wykonanie zadania
Teraz można tworzyć i dostęp remotedocker lub w klastrze typu obliczeniowego elementu docelowego przy użyciu uwierzytelniania opartego na kluczach SSH wykonaj następujące czynności:
- Dołącz obliczeniowego elementu docelowego, używając następującego polecenia w interfejsie wiersza polecenia

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
>[!NOTE]
>-k (lub--użyj-usługi Azure ml ssh-key) — opcja polecenia określa generowania i użytkowania klucza SSH.

- Usługa Azure ML Workbench wygeneruje klucz publiczny i danych wyjściowych, w konsoli. Zaloguj się do obiektu docelowego obliczeniowych, przy użyciu tej samej nazwy użytkownika i Dołącz plik ~/.ssh/authorized_keys przy użyciu tego klucza publicznego.

- Można przygotować to obliczeniowych elementów docelowych i użyć go do wykonania i Azure ML Workbench użyje tego klucza uwierzytelniania.  

Aby uzyskać więcej informacji na temat tworzenia obliczeniowych elementów docelowych, zobacz [konfigurowania Azure usługi Machine Learning eksperymentowania](../desktop-workbench/experimentation-service-configuration.md)

#### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools for AI
- Dodano obsługę [Visual Studio Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017). 

#### <a name="command-line-interface-cli"></a>Interfejs wiersza polecenia (CLI)
- Dodano `az ml datasource create` polecenie umożliwia utworzenie pliku źródła danych z wiersza polecenia

#### <a name="model-management-and-operationalization"></a>Zarządzanie modelami i Operacjonalizacji
- [Wszystkie kontenery AML są zgodne z urządzeń usługi Azure IoT Edge, przygotowany do działania (nie dodatkowe kroki konieczne)](http://aka.ms/aml-iot-edge-blog) 
- Ulepszenia komunikatów o błędach w interfejsie wiersza polecenia o16n
- Poprawki błędów w portalu zarządzania w modelu środowiska użytkownika  
- Wielkość liter w wyrazie literę spójny model zarządzania atrybutów na stronie szczegółów
- Limit czasu wywołania oceniania w czasie rzeczywistym jest ustawiona na 60 sekund
- Zarejestrowanego modelu listy i szczegółów widoków dostępnych w witrynie Azure portal

![Szczegóły modelu w portalu](media/azure-machine-learning-release-notes/model-list.jpg)

![Omówienie modelu w portalu](media/azure-machine-learning-release-notes/model-overview-portal.jpg)

#### <a name="mmlspark"></a>MMLSpark
- Na platformie Spark za pomocą do uczenia głębokiego [wsparciem procesora GPU](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Obsługa szablonów usługi Resource Manager do wdrażania zasobów proste
- Obsługa ekosystem SparklyR
- [Integracja AZTK](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

#### <a name="sample-projects"></a>Przykładowe projekty
- [Iris](https://github.com/Azure/MachineLearningSamples-Iris) i [MMLSpark](https://github.com/Azure/mmlspark) przykłady aktualizowane przy użyciu nowej wersji zestawu SDK usługi Azure ML

#### <a name="breaking-changes"></a>Zmiany powodujące niezgodność
- Promowany `--type` przełącznika w `az ml computetarget attach` do podpolecenia. 

    - `az ml computetarget attach --type remotedocker` jest teraz `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` jest teraz `az ml computetarget attach cluster`

### <a name="2017-11-sprint-1"></a>2017-11 (sprint 1) 
**Numer wersji**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([Znajdź swoją wersję](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

W tej wersji wprowadziliśmy ulepszenia dotyczące zabezpieczeń, stabilności i łatwość konserwacji w aplikacji workbench, interfejsu wiersza polecenia i warstwy usług zaplecza. Dziękujemy za bardzo dużo radosną twarz i frowns. Wiele poniżej aktualizacje są wprowadzane jako bezpośredni wynik opinii. Postanowieniem!

#### <a name="notable-new-features"></a>Istotne nowe funkcje
- Uczenie Maszynowe systemu Azure jest teraz dostępna w dwóch nowych regionów systemu Azure: **Europa Zachodnia** i **Azja południowo-wschodnia**. Zostają dołączone poprzedniego regionów **wschodnie stany USA 2**, **zachodnio-środkowe stany USA**, i **Australia Wschodnia**, przeniesienie łączną liczbę wdrożonych regionów do pięciu.
- Umożliwiliśmy składni kodu Python wyróżniania w aplikacji Workbench, aby ułatwić odczytywanie i edytowanie kodu źródłowego języka Python. 
- Teraz możesz uruchamiać ulubionego środowiska IDE bezpośrednio z pliku, a nie z całego projektu.  Otwieranie pliku w aplikacji Workbench, a następnie klikając pozycję "Edytuj" powoduje uruchomienie zintegrowanego środowiska Projektowego (obecnie program VS Code i platformy PyCharm są obsługiwane) bieżącego pliku i projektu.  Można również kliknij strzałkę obok przycisk Edytuj, aby edytować plik w edytorze tekstów aplikacji Workbench.  Pliki są tylko do odczytu, dopóki nie klikniesz przycisku edycji, zapobieganie przypadkowym zmianom.
- Popularne bibliotekę wykreślania `matplotlib` wersja 2.1.0 jest teraz dostarczany z aplikacji Workbench.
- Firma Microsoft uaktualnienie wersji platformy .NET Core do aparatu przygotowywania danych w wersji 2.0. Usunąć to potrzebę openssl brew install podczas instalacji aplikacji w systemie macOS. On również drogę do wprowadzenia danych ciekawsze przygotowywania funkcji dostępna w niedalekiej przyszłości. 
- Udostępniliśmy stronę główną specyficzny dla wersji aplikacji, aby uzyskać dokładniejsze informacje o wersji i zaktualizuj monitów, w zależności od bieżącej wersji aplikacji.
- Jeśli Twoja nazwa użytkownika lokalnego ma miejsce w nim, aplikacja może teraz pomyślnie zainstalowana. 

#### <a name="detailed-updates"></a>Szczegółowe aktualizacji
Poniżej przedstawiono listę szczegółowych aktualizacji w każdym obszarze składników usługi Azure Machine Learning, w tym sprincie.

##### <a name="installer"></a>Instalator
- Instalator aplikacji teraz czyści katalogu instalacyjnego utworzone przez starszą wersję aplikacji.
- Naprawiono usterkę, która prowadzi gromadzą się na poziomie 100% na system macOS High Sierra Instalatora.
- Teraz jest bezpośredni link do katalogu Instalatora dla użytkownika przejrzeć dzienniki Instalatora, w przypadku, gdy instalacja nie powiedzie się.
- Zainstaluj teraz działa w przypadku użytkowników, którzy mają miejsce w ich nazw użytkowników.

##### <a name="workbench-authentication"></a>Uwierzytelnianie aplikacji Workbench
- Obsługa uwierzytelniania w Menedżerze serwera Proxy.
- Teraz logowanie zakończy się pomyślnie, jeśli użytkownik znajduje się za zaporą. 
- Jeśli użytkownik nie ma konta eksperymentowania w wielu regionach platformy Azure i regionami stanie się ona niedostępna, zawiesza się już aplikacji.
- Podczas uwierzytelniania nie zostanie przeprowadzona i okna dialogowego uwierzytelniania jest nadal widoczny, aplikacja nie jest już próbuje załadować obszar roboczy z lokalnej pamięci podręcznej.

##### <a name="workbench-app"></a>Aplikację Workbench
- Wyróżnianie składni kodu języka Python jest włączona w edytorze tekstów.
- Przycisk Edytuj w edytorze tekstów służy do edytowania pliku albo w środowisku IDE (program VS Code i platformy PyCharm są obsługiwane) lub w edytorze tekstów wbudowanych.
- Edytor tekstu jest w trybie tylko do odczytu domyślnie. 
- Przycisk stanu wizualnego teraz zmiany zapisać wyłączona po bieżący plik jest zapisany i dlatego nie jest już zanieczyszczeniu.
- Zapisuje Workbench _wszystkich_ niezapisane pliki po zainicjowaniu przebiegu.
- Środowisko robocze pamięta, że ostatnio używany obszar roboczy na komputerze lokalnym, więc zostanie automatycznie otwarta.
- Uruchom teraz jest dozwolone tylko jedno wystąpienie aplikacji Workbench. Wcześniej wiele wystąpień powinna zostać uruchomiona, których przyczyną problemów działających na tym samym projekcie.
- Zmieniono nazwę pliku menu "Otwórz projekt..." do "Dodaj istniejący Folder jako projekt..." 
- Karta przełączanie jest teraz znacznie szybciej.
- Linki pomocy są dodawane do okna dialogowego Konfigurowanie IDE.
- Formularz opinii pamięta teraz podany czas ostatniego adres e-mail.
- Radosną twarz i frowns obszar tekstu formularza jest teraz większe, dzięki czemu możesz wysłać nam więcej informacji zwrotnych. 
- `--owner` Przełącz tekst pomocy w `az ml workspace create` jest usuwana.
- Dodaliśmy "About" okno dialogowe, aby pomóc użytkownikowi łatwe wyświetlanie i kopiowanie numeru wersji aplikacji.
- Element menu "Zaproponuj funkcję" zostanie dodany do menu Pomoc.
- Nazwa konta eksperymentowania są teraz widoczne w aplikacji pasku tytułu, poprzedza nazwa aplikacji "Azure Machine Learning Workbench".
- Stronę główną specyficzny dla wersji aplikacji jest wyświetlany, opiera się teraz na wersję aplikacji, o których wykryto.

##### <a name="data-preparation"></a>Przygotowywanie danych 
- Zewnętrznej witryny sieci web nie będzie można ładować ze inspektora mapy, aby uniknąć potencjalnych problemów z zabezpieczeniami.
- Inspektorzy histogramie i liczba wartości ma teraz opcję, aby wyświetlić wykres w skali logarytmicznej.
- Gdy trwa obliczanie, pasku jakości danych pojawi się na inny kolor w celu sygnalizowania, że stan "Trwa obliczanie".
- Kolumna metryki pokazują teraz statystyki dla kolumn wartości podzielonych na kategorie.
- Ostatni znak w nazwie źródła danych nie jest już obcięty.
- Pakiet przygotowywania danych teraz pozostaje otwarty podczas przełączania kart, wynikowa zyski zauważalna zmiana wydajności.
- W źródle danych, podczas przełączania się między widokiem danych i metryk już zmiany kolejności kolumn teraz.
- Otwieranie nieprawidłową `.dprep` lub `.dsource` pliku nie powoduje już Workbench ulega awarii.
- Pakietu przygotowywania danych można teraz używa ścieżki względnej dla danych wyjściowych w _zapis do pliku CSV_ przekształcania.
- _Zachowaj kolumnę_ przekształcenie teraz umożliwia użytkownikowi dodawanie dodatkowych kolumn podczas edycji.
- _Zastąp to_ menu teraz faktycznie uruchamia _Zastąp wartość_ okno dialogowe.
- _Zastąp wartość_ Przekształć teraz funkcje, zgodnie z oczekiwaniami zamiast zgłaszania błędu.
- Pakiet przygotowywania danych używa ścieżki bezwzględnej teraz podczas odwoływania się do danych plików poza folderem projektu, dzięki czemu można uruchomić pakiet w lokalnym kontekście ścieżka bezwzględna do pliku danych.
- _Cały plik_ jako strategii próbkowania jest teraz obsługiwana w przypadku korzystania z usługi Azure blob jako źródła danych.
- Wygenerowany kod języka Python (od pakietu przygotowywania danych) teraz niesie ze sobą CR i LF, dzięki czemu przyjazna Windows.
- _Wybierz metryki_ listy rozwijanej teraz ukrywa właściwości podczas przełączania do widoku danych.
- Środowisko robocze mogą teraz pliki parquet procesu, nawet wtedy, gdy używane są środowiska uruchomieniowego Python. Wcześniej tylko Spark może służyć podczas przetwarzania plików parquet. 
- Filtrowanie wartości w kolumnie z _data_ typu danych nie powoduje już aparat przygotowywania danych ulega awarii.
- Wyświetl metryki uwzględnia teraz próbkowania strategii aktualizacji.
- Próbkowanie teraz zadania zdalne działa prawidłowo.

##### <a name="job-execution"></a>Wykonanie zadania
- Argument jest teraz zawarta w rekordu historii uruchamiania.
- Rozpoczęła się zadania interfejsu wiersza polecenia wyświetlane w panelu zadania historii przebiegu automatycznie.
- Panel zadania zawiera teraz zadań utworzonych przez użytkowników-gości dodana do dzierżawy usługi Azure AD.
- Anuluj panelu zadań, a operacje usuwania są bardziej stabilne.
- Po kliknięciu przycisku uruchomienia, komunikat o błędzie zostanie wywołany teraz, jeśli pliki konfiguracji znajdują się w zły format.
- Trwa przerywanie działania aplikacji nie jest już koliduje z zadaniami rozpoczęła się w interfejsie wiersza polecenia.
- Rozpoczęła się zadania interfejsu wiersza polecenia teraz w dalszym ciągu Podziel się poza standardowe, nawet po jednej godziny wykonywania.
- Lepsze komunikaty o błędach są wyświetlane, gdy pakiet przygotowywania danych, uruchamianie nie powiodło się w języku Python/PySpark.
- `az ml experiment clean` teraz czyści obrazów platformy Docker na zdalnej maszynie Wirtualnej również.
- `az ml experiment clean` teraz działa prawidłowo dla lokalnego obiektu docelowego w systemie macOS.
- Komunikaty o błędach, gdy przeznaczonych dla platformy Docker lokalny lub zdalny działa zostały wyczyszczone w górę i łatwiejsza do odczytania.
- Udoskonalony komunikat o błędzie jest wyświetlany, gdy nazwa węzła głównego klastra HDInsight nie jest poprawnie sformatowany w przypadku dołączonych jako element docelowy wykonywania.
- Udoskonalony komunikat o błędzie jest wyświetlany, gdy wpis tajny nie zostanie znaleziony w usłudze poświadczeń. 
- Biblioteka MMLSpark jest uaktualnienie do pomocy technicznej Apache Spark 2.2.
- MMLSpark obejmują teraz podmiotu kodowania transformacji (kodowanie siatki) dla dokumentów medyczne.
- `matplotlib` wersja 2.1.0 jest teraz dostarczany out-gotową przy użyciu aplikacji Workbench.

##### <a name="jupyter-notebook"></a>Jupyter Notebook
- W widoku notesów wyszukiwanie nazwy notesu teraz działa poprawnie.
- Możesz teraz usunąć Notes w widoku notesy.
- Nowe magic `%upload_artifact` jest dodawany do przekazywania plików utworzone w środowisku wykonawczym Notes w magazynie danych historii uruchamiania.
- Błędy jądra są teraz udostępniane w stan zadania notesu łatwiejsze debugowanie.
- Serwer Jupyter teraz poprawnie zamknięty podczas logowania użytkownika z aplikacji.

##### <a name="azure-portal"></a>Azure Portal
- Konta eksperymentowania oraz konta zarządzania modelami można teraz tworzyć tak w dwóch nowych regionów systemu Azure: Europa Zachodnia i Azja południowo-wschodnia.
- Plan usługi DevTest konta zarządzania modelu teraz jest dostępna tylko po to pierwszy z nich ma być utworzony w ramach subskrypcji. 
- Link pomoc w witrynie Azure portal jest aktualizowana, aby wskazywał na stronie dokumentacji poprawne.
- Pole opisu jest usuwany z strony szczegółów obrazu platformy Docker, ponieważ nie ma zastosowania.
- Szczegółowe informacje, łącznie z ustawieniami usługi Application Insights i automatycznego skalowania są dodawane do strony szczegółów usługi sieci web.
- Strony zarządzania modelu teraz renderuje, nawet jeśli pliki cookie innych firm są wyłączone w przeglądarce. 

##### <a name="operationalization"></a>Operacjonalizacja
- Usługa sieci Web za pomocą "wynik" w nazwie nie jest już nie powiedzie się.
- Użytkownik może teraz utworzyć środowisko wdrażania przy użyciu tylko prawa dostępu współautora do grupy zasobów platformy Azure lub subskrypcji. Dostęp właściciela do całej subskrypcji nie jest już potrzebny.
- Teraz operacjonalizacji interfejsu wiersza polecenia cieszy się karta automatycznego uzupełniania w systemie Linux.
- Usługa tworzenia obrazów obsługuje teraz kompilowanie obrazów dla urządzenia usługi Azure IoT.

##### <a name="sample-projects"></a>Przykładowe projekty
- [_Klasyfikowanie irysów_ ](../desktop-workbench/tutorial-classifying-iris-part-1.md) przykładowego projektu:
    - `iris_pyspark.py` została zmieniona na `iris_spark.py`.
    - `iris_score.py` została zmieniona na `score_iris.py`.
    - `iris.dprep` i `iris.dsource` są aktualizowane w celu odzwierciedlenia najnowszych aktualizacji aparatu przygotowywania danych.
    - `iris.ipynb` Notes wprowadza się do pracy w klastrze HDInsight.
    - Historia przebiegów jest włączona w programie `iris.ipynb` komórce w notesie.
- [_Zaawansowane przygotowywanie danych przy użyciu dane udostępniania roweru_ ](../desktop-workbench/tutorial-bikeshare-dataprep.md) przykładowy projekt krok "Obsługi błędów wartość" rozwiązane.
- [_MMLSpark w danych spisu treści dla dorosłych_ ](https://github.com/Azure/MachineLearningSamples-mmlspark) przykładowy projekt `docker.runconfig` format zaktualizowany z formatu JSON do kodu YAML.
- [_Rozproszone do strojenia Hiperparametrycznego_ ](../desktop-workbench/scenario-distributed-tuning-of-hyperparameters.md) przykładowy projekt`docker.runconfig` format zaktualizowany z formatu JSON do kodu YAML.
- Nowy projekt przykładowy [ _Klasyfikacja obrazów przy użyciu narzędzi CNTK_](../desktop-workbench/scenario-image-classification-using-cntk.md).


### <a name="2017-10-sprint-0"></a>2017-10 (sprint 0) 
**Numer wersji**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([Znajdź swoją wersję](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Witamy w pierwszej aktualizacji usługi Azure Machine Learning Workbench, zgodnie z naszym początkowej publicznej wersji zapoznawczej na konferencji Microsoft Ignite 2017. Główne aktualizacje wprowadzono w tej wersji są niezawodności i stabilizacji poprawki.  O istotnych kwestiach, które rozwiązaliśmy między innymi:

#### <a name="new-features"></a>Nowe funkcje
- System macOS High Sierra jest teraz obsługiwane.

#### <a name="bug-fixes"></a>Poprawki błędów
##### <a name="workbench-experience"></a>Środowisko aplikacji Workbench
- Przeciąganie i upuszczanie plików w aplikacji Workbench powoduje, że Workbench awarię.
- Okno terminalu w programie VS Code, skonfigurowany jako środowisko IDE dla aplikacji Workbench nie rozpoznaje _az ml_ poleceń.

##### <a name="workbench-authentication"></a>Uwierzytelnianie aplikacji Workbench
Wprowadziliśmy wiele aktualizacji poprawiających różne nazwy logowania i uwierzytelniania problemy zgłoszone.
- Okno uwierzytelniania przechowuje o wyświetlaniu w górę, szczególnie w przypadku, gdy połączenie z Internetem nie jest stabilna.
- Poprawiono niezawodność problemy dotyczące wygaśnięcia tokenu uwierzytelniania.
- W niektórych przypadkach uwierzytelniania zostanie wyświetlone okno dwa razy.
- Głównego okna aplikacji Workbench nadal wyświetla komunikat "uwierzytelniania" po zakończeniu procesu uwierzytelniania i podręcznym oknie dialogowym już odrzucono.
- W przypadku brak połączenia internetowego uwierzytelniania pojawi się okno dialogowe z pustego ekranu.

##### <a name="data-preparation"></a>Przygotowywanie danych 
- Jeśli określona wartość jest filtrowana, błędy i brakujące wartości są również odfiltrowane.
- Zmiana strategii próbkowania usuwa kolejnych istniejących operacji łączenia.
- Zastąpienie wartości Brak przekształcenie nie uwzględnia NaN.
- Wnioskowanie o typie data zgłasza wyjątek, gdy wartość null.

##### <a name="job-execution"></a>Wykonanie zadania
- Podczas wykonywania zadania nie powiedzie się Przekaż folder projektu, ponieważ przekroczył limit rozmiaru jest nie wyczyść komunikat o błędzie.
- Jeśli skrypt w języku Python użytkownika zmienia katalog roboczy, pliki zapisane w danych wyjściowych, foldery nie są śledzone. 
- Jeśli aktywna subskrypcja platformy Azure jest inny niż ten, którego należy bieżący projekt, przesłania zadania powoduje błąd 403.
- Jeśli nie występuje platformy Docker, nie wyczyść komunikat o błędzie jest zwracany, jeśli użytkownik próbuje użyć platformy Docker jako element docelowy wykonywania.
- plik .runconfig nie jest zapisywany automatycznie, gdy użytkownik kliknie _Uruchom_ przycisku.

##### <a name="jupyter-notebook"></a>Jupyter Notebook
- Jeśli użytkownik korzysta z niektórych typów logowania, nie można uruchomić serwera notesu.
- Komunikaty o błędach na serwer notesu nie Prezentuj w dziennikach widoczny dla użytkownika.

##### <a name="azure-portal"></a>Azure Portal
- Wybieranie ciemny motyw w witrynie Azure Portal powoduje, że blok Zarządzanie modelami w usłudze, aby wyświetlić jako czarne pole.

##### <a name="operationalization"></a>Operacjonalizacja
- Ponowne użycie manifest do aktualizowania usługi sieci web powoduje, że nowy obraz platformy Docker z wybraną losowo nazwą.
- Dzienniki usługi sieci Web nie można pobrać z klastrem Kubernetes.
- Mylący komunikat o błędzie zostanie wydrukowany, gdy użytkownik próbuje utworzyć konto Zarządzanie modelami w usłudze lub konto środowiska obliczeniowego usługi ML i napotyka problemy z uprawnieniami.

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z omówieniem usługi [Azure Machine Learning](../service/overview-what-is-azure-ml.md).
