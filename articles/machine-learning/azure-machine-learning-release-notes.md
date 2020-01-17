---
title: Co nowego w wersji?
titleSuffix: Azure Machine Learning
description: Poznaj najnowsze aktualizacje Azure Machine Learning oraz zestawy SDK języka Python dotyczące uczenia maszynowego i przygotowywania danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 0e613dd1c47a9942d95bd668f1e9f85e42649b5c
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122376"
---
# <a name="azure-machine-learning-release-notes"></a>Informacje o wersji Azure Machine Learning

W tym artykule dowiesz się więcej na temat wydań Azure Machine Learning.  Aby uzyskać pełną zawartość referencyjną SDK, odwiedź stronę referencyjną [**głównego zestawu sdk Azure Machine Learning dla języka Python**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) .

Zobacz [listę znanych problemów](resource-known-issues.md) informacje na temat znanych błędów i rozwiązania problemu.

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.83

+ **Nowe funkcje**
  + Zestaw danych: Dodaj dwie opcje `on_error` i `out_of_range_datetime`, aby `to_pandas_dataframe` się nie powieść, gdy dane zawierają wartości błędów zamiast wypełniania przy użyciu `None`.

+ **Poprawki i ulepszenia błędów**
  + **Azure automl — środowisko uruchomieniowe**
    + Naprawiono regresję, która spowodowała wygenerowanie TypeError podczas uruchamiania AutoML w wersjach języka Python poniżej 3.5.4.
  + **azureml-core**
    + Naprawiono usterkę w `datastore.upload_files`, w której nie można było użyć ścieżki względnej, która nie została rozpoczęta z `./`.
    + Dodano komunikaty o zaniechaniu dla wszystkich klas obrazu codepaths
    + Naprawiono budowę adresu URL Zarządzanie modelami dla regionu Mooncake.
    + Rozwiązano problem polegający na tym, że modele używające source_dir nie mogły zostać spakowane dla Azure Functions.    
    + Dodano opcję do [środowiska. build_local ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) w celu wypchnięcia obrazu do rejestru kontenerów obszaru roboczego platformy Azure
    + Zaktualizowano zestaw SDK w taki sposób, aby korzystał z nowej biblioteki tokenów na platformie Azure Synapse w sposób zgodny ze sobą.
  + **Azure — interpretowanie**
    + Naprawiono usterkę, która nie została zwrócona w przypadku braku wyjaśnień dostępnych do pobrania. Teraz zgłasza wyjątek, zgodne zachowanie w innym miejscu.
  + **azureml-pipeline-steps**
    + Niedozwolone przekazywanie `DatasetConsumptionConfig`s do `Estimator``inputs`, gdy `Estimator` zostanie użyta w `EstimatorStep`.
  + **Azure-SDK**
    + Dodano klienta AutoML do pakietu Azure-SDK, co umożliwia przesłanie zdalnych przebiegów AutoML bez instalowania pełnego pakietu AutoML.
  + **Azure-pociąg-automl-Client**
    + Skorygowane wyrównanie danych wyjściowych z konsoli dla uruchomień automl
    + Rozwiązano problem polegający na tym, że w zdalnej amlcompute można zainstalować niepoprawną wersję programu Pandas.


## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.81

+ **Poprawki i ulepszenia błędów**
  + **Azure-contrib — interpretacja**
    + Odłóż zależność kształtu do interpretowania przez społeczność od platformy Azure — Interpretuj
  + **azureml-core**
    + Element docelowy obliczeń można teraz określić jako parametr do odpowiednich obiektów konfiguracji wdrożenia. Jest to w odróżnieniu od nazwy elementu docelowego obliczeń, który ma zostać wdrożony, a nie obiektu zestawu SDK.
    + Dodano informacje CreatedBy do modelu i obiektów usługi. Dostęp do niego można uzyskać za poorednictwem <var>. created_by
    + Naprawiono ContainerImage. Run (), który nieprawidłowo konfiguruje port HTTP kontenera Docker.
    + Ustaw `azureml-dataprep` jako opcjonalne dla `az ml dataset register` polecenia CLI
  + **azureml-dataprep**
    + Naprawiono usterkę polegającą na tym, że TabularDataset. to_pandas_dataframe niepoprawnie powraca do alternatywnego czytnika i drukuje ostrzeżenie.
  + **azureml-explain-model**
    + Odłóż zależność kształtu do interpretowania przez społeczność od platformy Azure — Interpretuj
  + **azureml-pipeline-core**
    + Dodano nowy krok potoku `NotebookRunnerStep`, aby uruchomić lokalny Notes jako krok w potoku.
    + Usunięto przestarzałe funkcje get_all dla PublishedPipelines, harmonogramów i PipelineEndpoints
  + **Azure-pociąg-automl-Client**
    + Rozpoczęto przestarzałość data_script jako dane wejściowe do AutoML.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.79

+ **Poprawki i ulepszenia błędów**
  + **Azure-automl-Core**
    + Usunięto featurizationConfig do zarejestrowania
      + Zaktualizowano rejestrowanie w celu zarejestrowania tylko "Auto"/"off"/"dostosowany".
  + **Azure automl — środowisko uruchomieniowe**
    + Dodano obsługę Pandas. Seria i Pandas. Kategorii do wykrywania typu danych kolumny. Poprzednio obsługiwane tylko numpy. ndarray
      + Dodano powiązane zmiany kodu w celu poprawnego obsłużenia kategorii dtype.
    + Ulepszono interfejs funkcji prognozowania: parametr y_pred został wykonany jako opcjonalny. -Docstrings zostały ulepszone.
  + **Azure contrib — zestaw danych**
    + Naprawiono usterkę, w której nie można zainstalować zestawów danych z etykietami.
  + **azureml-core**
    + Poprawka usterki dla `Environment.from_existing_conda_environment(name, conda_environment_name)`. Użytkownik może utworzyć wystąpienie środowiska, które jest dokładną repliką środowiska lokalnego
    + Zmieniono metody zestawów danych powiązane z serią czasową, aby `include_boundary=True` domyślnie.
  + **Azure-pociąg-automl-Client**
    + Rozwiązano problem polegający na tym, że wyniki walidacji nie są drukowane, gdy dla opcji Pokaż dane wyjściowe ustawiono wartość FAŁSZ.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.76

+ **Fundamentalne zmiany**
  + Problemy z aktualizacją uczenia maszynowego — AutoML
    + Uaktualnianie do usługi Azure-uczenie-automl > = 1.0.76 z usługi Azure-pociąg-automl < 1.0.76 może spowodować niepowodzenie instalacji częściowych. Aby rozwiązać ten problem, można uruchomić skrypt Instalatora znajdujący się w https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd. Lub jeśli używasz narzędzia PIP bezpośrednio, możesz:
      + "Instalacja PIP--upgrade Azure-pociąg-automl"
      + "Narzędzie pip install--ignore-Install-automl-Client"
    + lub można odinstalować starą wersję przed uaktualnieniem
      + "PIP Uninstall-pociąg-automl"
      + "pip install Azure-pociąg-automl"

+ **Poprawki i ulepszenia błędów**
  + **Azure automl — środowisko uruchomieniowe**
    + Funkcja AutoML będzie teraz uwzględniać zarówno klasy prawdziwe, jak i fałszywe w przypadku obliczania średniej metryk skalarnych dla zadań klasyfikacji binarnej.
    + Przeniesiono kod uczenia maszynowego i szkoleniowego w usłudze Azure AutoML-Core do nowego pakietu Azure-AutoML-Runtime.
  + **Azure contrib — zestaw danych**
    + Podczas wywoływania `to_pandas_dataframe` na oznaczonym zestawie danych z opcją pobierania można teraz określić, czy zastąpić istniejące pliki, czy nie.
    + Podczas wywoływania `keep_columns` lub `drop_columns`, w wyniku których zostanie porzucona szeregów czasowych, etykieta lub kolumna obrazu, odpowiednie możliwości zostaną również porzucone dla zestawu danych.
    + Rozwiązano problem z programem ładującym pytorch na potrzeby zadania wykrywania obiektu.
  + **Azure-contrib — interpretacja**
    + Usunięto widżet pulpitu nawigacyjnego wyjaśnienie z platformy Azure-contrib-interpreter, zmieniony pakiet, aby odwołać się do nowego elementu w interpret_community
    + Zaktualizowana wersja interpretera — społeczność 0.2.0
  + **azureml-core**
    + Zwiększ wydajność `workspace.datasets`.
    + Dodano możliwość rejestrowania Azure SQL Database datastore przy użyciu nazwy użytkownika i uwierzytelniania hasła
    + Poprawka do ładowania RunConfigurations ze ścieżek względnych.
    + Podczas wywoływania `keep_columns` lub `drop_columns`, w wyniku których zostanie usunięta kolumna szeregów czasowych, odpowiednie możliwości zostaną również porzucone dla zestawu danych.
  + **Azure — interpretowanie**
    + Zaktualizowana wersja interpretera — społeczność 0.2.0
  + **azureml-pipeline-steps**
    + Udokumentowane obsługiwane wartości `runconfig_pipeline_params` dla kroków potoku usługi Azure Machine Learning.
  + **azureml-pipeline-core**
    + Dodano opcję interfejsu wiersza polecenia w celu pobrania danych wyjściowych w formacie JSON dla poleceń potoku.
  + **azureml-train-automl**
    + Podziel usługę Azure AutoML na dwa pakiety, pakiet klienta Azure-AutoML-Client oraz pakiet szkoleniowy "Uczenie maszynowe-AutoML — środowisko uruchomieniowe"
  + **Azure-pociąg-automl-Client**
    + Dodano klienta zubożonego do przesyłania eksperymentów AutoML bez konieczności lokalnego instalowania żadnych zależności usługi Machine Learning.
    + Stałe rejestrowanie automatycznie wykrytych spowolnienia, stopniowe rozmiary okien i maksymalne Horizons w zdalnych uruchomieniach.
  + **Azure-uczenie-automl — środowisko uruchomieniowe**
    + Dodano nowy pakiet AutoML w celu wyodrębnienia składników uczenia maszynowego i środowiska uruchomieniowego z klienta programu.
  + **Azure-contrib-pociąg-RL**
    + Dodano obsługę uczenia wzmacniania w zestawie SDK.
    + Dodano obsługę AmlWindowsCompute w zestawie RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.74

  + **Funkcje w wersji zapoznawczej**
    + **Azure contrib — zestaw danych**
      + Po zaimportowaniu programu Azure-contrib-DataSet można wywołać `Dataset.Labeled.from_json_lines` zamiast `._Labeled`, aby utworzyć zestaw danych z etykietą.
      + Podczas wywoływania `to_pandas_dataframe` na oznaczonym zestawie danych z opcją pobierania można teraz określić, czy zastąpić istniejące pliki, czy nie.
      + Podczas wywoływania `keep_columns` lub `drop_columns`, które powoduje porzucanie szeregów czasowych, etykiet lub kolumn obrazu, odpowiednie możliwości zostaną również porzucone dla zestawu danych.
      + Rozwiązano problemy z modułem ładującym PyTorch podczas wywoływania `dataset.to_torchvision()`.

+ **Poprawki i ulepszenia błędów**
  + **Azure — interfejs wiersza polecenia**
    + Dodano profilowanie modelu do interfejsu wiersza polecenia w wersji zapoznawczej.
    + Naprawia istotną zmianę w usłudze Azure Storage powodującą awarię interfejsu wiersza polecenia platformy Azure.
    + Dodano typ Load Balancer do MLC dla typów AKS
  + **Azure-automl-Core**
    + Rozwiązano problem z wykrywaniem maksymalnego poziomu w szeregach czasowych, mających brakujące wartości i wiele ziaren.
    + Rozwiązano problem z błędami podczas generowania podziałów wzajemnego sprawdzania poprawności.
    + Zastąp tę sekcję komunikatem w formacie promocji, aby pojawić się w informacjach o wersji:-Ulepszona obsługa krótkich ziaren w zestawach danych prognozowanie.
    + Rozwiązano problem związany z maską niektórych informacji o użytkowniku podczas rejestrowania. -Ulepszone rejestrowanie błędów podczas prognozowania przebiegów.
    + Dodawanie psutil jako zależności Conda do automatycznie generowanego pliku wdrożenia YML.
  + **Azure-contrib-Mir**
    + Naprawia istotną zmianę w usłudze Azure Storage powodującą awarię interfejsu wiersza polecenia platformy Azure.
  + **azureml-core**
    + Naprawia usterkę, która spowodowała, że modele wdrożone w Azure Functions w celu utworzenia rozwiązania 500S.
    + Rozwiązano problem polegający na tym, że plik amlignore nie został zastosowany do migawek.
    + Dodano nowy interfejs API amlcompute. get_active_runs, który zwraca generator dla uruchomionych i umieszczonych w kolejce przebiegów w danym amlcompute.
    + Dodano Load Balancer typ do MLC dla typów AKS.
    + Dodano append_prefix parametru bool do download_files w run.py i download_artifacts_from_prefix w artifacts_client. Ta flaga służy do selektywnego spłaszczania ścieżki źródła, więc do output_directory dodawany jest tylko nazwa pliku lub folderu.
    + Rozwiąż problem z deserializacji `run_config.yml` przy użyciu zestawu danych.
    + Podczas wywoływania `keep_columns` lub `drop_columns`, w wyniku których powstaje kolumna szeregów czasowych, odpowiednie możliwości zostaną porzucone również dla zestawu danych.
  + **Azure — interpretowanie**
    + Zaktualizowano interpreter — wersja społecznościowa do 0.1.0.3
  + **azureml-train-automl**
    + Rozwiązano problem polegający na tym, że automl_step mogą nie drukować problemów z walidacją.
    + Naprawiono pomyślne register_model, nawet jeśli w środowisku modelu brakuje lokalnie zależności.
    + Rozwiązano problem polegający na tym, że niektóre zdalne uruchomienia nie zostały włączone.
    + Dodanie rejestrowania wyjątku powodującego przedwczesne uruchomienie lokalnego uruchomienia.
  + **azureml-train-core**
    + Należy wziąć pod uwagę resume_from uruchomienia w obliczu zautomatyzowanego przebiegu strojenia najlepszych parametrów.
  + **azureml-pipeline-core**
    + Obsługa stałych parametrów w konstrukcji argumentu potoku.
    + Dodano parametr YAML potoku i typ kroku.
    + Nowy format YAML dla kroku potoku i dodano ostrzeżenie o zaniechaniu dla starego formatu.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Środowisko sieci Web

Strona docelowa współpracy obszaru roboczego w [https://ml.azure.com](https://ml.azure.com) została ulepszona i oznaczana jako Azure Machine Learning Studio (wersja zapoznawcza).

Z poziomu programu Studio można nauczyć, testować i wdrażać Azure Machine Learning zasoby, takie jak zestawy danych, potoki, modele, punkty końcowe itd., oraz zarządzać nimi.

Uzyskaj dostęp do następujących narzędzi autorskich opartych na sieci Web z programu Studio:

| Narzędzie oparte na sieci Web | Opis | Edycja |
|-|-|-|
| Maszyna wirtualna w notesie (wersja zapoznawcza) | W pełni zarządzana stacja robocza oparta na chmurze | Podstawowa & Enterprise |
| [Automatyczne Uczenie maszynowe](tutorial-first-experiment-automated-ml.md) (wersja zapoznawcza) | Brak środowiska kodu do automatyzacji tworzenia modelu uczenia maszynowego | Enterprise |
| [Projektant](concept-designer.md) (wersja zapoznawcza) | Narzędzie do modelowania i upuszczania maszynowego, znane wcześniej jako projektant | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Udoskonalenia projektanta Azure Machine Learning

+ Dawniej znany jako interfejs wizualizacji 
+   11 nowe [moduły](algorithm-module-reference/module-reference.md) , w tym zalecenia, klasyfikatory i narzędzia szkoleniowe, w tym Inżynieria funkcji, wzajemne sprawdzanie poprawności i Przekształcanie danych.

### <a name="r-sdk"></a>Zestaw SDK języka R 
 
Analitycy danych i deweloperzy AI używają [zestawu SDK Azure Machine Learning dla języka R](tutorial-1st-r-experiment.md) do kompilowania i uruchamiania przepływów pracy uczenia maszynowego przy użyciu Azure Machine Learning.

Zestaw Azure Machine Learning SDK dla języka R używa pakietu `reticulate` do powiązania z zestawem Python SDK. Dzięki powiązaniu bezpośrednio z językiem Python zestaw SDK dla języka R umożliwia dostęp do podstawowych obiektów i metod zaimplementowanych w zestawie Python SDK z dowolnego wybranego środowiska języka R.

Główne możliwości zestawu SDK obejmują:

+   Zarządzanie zasobami w chmurze na potrzeby monitorowania, rejestrowania i organizowania eksperymentów uczenia maszynowego.
+   Uczenie modeli przy użyciu zasobów w chmurze, w tym szkoleń modelu procesora GPU.
+   Wdróż modele jako usługi WebService na Azure Container Instances (ACI) i Azure Kubernetes Service (AKS).

Zapoznaj się z [witryną internetową pakietu](https://azure.github.io/azureml-sdk-for-r) , aby uzyskać pełną dokumentację.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning integrację z usługą Event Grid 

Azure Machine Learning jest teraz dostawcą zasobów dla Event Grid, można skonfigurować zdarzenia uczenia maszynowego za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure. Użytkownicy mogą tworzyć zdarzenia dotyczące ukończenia przebiegu, rejestracji modelu, wdrożenia modelu i dryfowania danych. Te zdarzenia mogą być kierowane do programów obsługi zdarzeń obsługiwanych przez Event Grid do użycia. Więcej informacji można znaleźć w temacie [schemat](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning)zdarzeń usługi Machine Learning, [koncepcje](https://docs.microsoft.com/azure/machine-learning/concept-event-grid-integration) i artykuły dotyczące [samouczka](https://docs.microsoft.com/azure/machine-learning/how-to-use-event-grid) .

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.72

+ **Nowe funkcje**
  + Dodano monitory zestawu danych za pośrednictwem pakietu [**Azure-datadryfing**](https://docs.microsoft.com/python/api/azureml-datadrift) , co pozwala na monitorowanie zestawów danych szeregów czasowych na potrzeby dryfowania lub innych zmian statystycznych w czasie. Alerty i zdarzenia mogą być wyzwalane w przypadku wykrycia dryfu lub spełnienia innych warunków dotyczących danych. Zapoznaj się z [naszą dokumentacją](https://aka.ms/datadrift) , aby uzyskać szczegółowe informacje.
  + Ogłaszamy dwie nowe wersje (nazywane także zamiennie jednostkami SKU) w Azure Machine Learning. W tej wersji możesz teraz utworzyć obszar roboczy Azure Machine Learning podstawowy lub Enterprise. Wszystkie istniejące obszary robocze zostaną domyślnie przeszukane w wersji podstawowa. Możesz też przejść do Azure Portal lub do programu Studio, aby uaktualnić obszar roboczy w dowolnym momencie. Możesz utworzyć obszar roboczy podstawowy lub Enterprise na podstawie Azure Portal. Zapoznaj się z [naszą dokumentacją](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) , aby dowiedzieć się więcej. W zestawie SDK wersja obszaru roboczego można określić przy użyciu właściwości "SKU" obiektu obszaru roboczego.
  + Wprowadzono również usprawnienia Azure Machine Learning obliczeń — teraz można wyświetlać metryki dla klastrów (takie jak łączna liczba węzłów, uruchomione węzły, łączny przydział rdzeni) w Azure Monitor, oprócz wyświetlania dzienników diagnostycznych na potrzeby debugowania. Ponadto można również wyświetlać uruchomione lub kolejkowane uruchomienia w klastrze oraz szczegóły, takie jak adresy IP różnych węzłów w klastrze. Można je wyświetlić w portalu lub za pomocą odpowiednich funkcji w zestawie SDK lub interfejsie wiersza polecenia.

  + **Funkcje w wersji zapoznawczej**
    + Firma Microsoft udostępnia w wersji zapoznawczej obsługę szyfrowania dysków lokalnego dysku SSD w programie Azure Machine Learning COMPUTE. Zgłoś bilet pomocy technicznej, aby uzyskać listy dozwolonych subskrypcji do korzystania z tej funkcji.
    + Publiczna wersja zapoznawcza Azure Machine Learning wnioskowania partii. Azure Machine Learning wnioskowanie wsadowe odwołuje się do dużych zadań wnioskowania, które nie są zależne od czasu. Wnioskowanie wsadowe zapewnia ekonomiczne skalowanie obliczeniowe z zastosowaniem niezrównanej przepływności dla aplikacji asynchronicznych. Jest zoptymalizowany pod kątem wysokiej przepływności, pożaru i zapomnienia w przypadku dużych kolekcji danych.
    + [**Azure contrib — zestaw danych**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + Włączone funkcje dla oznaczonego zestawu danych
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Poprawki i ulepszenia błędów**
  + **Azure — interfejs wiersza polecenia**
    + Interfejs wiersza polecenia obsługuje teraz pakowanie modeli.
    + Dodano interfejs wiersza polecenia zestawu danych. Aby uzyskać więcej informacji: `az ml dataset --help`
    + Dodano obsługę wdrażania i pakowania obsługiwanych modeli (ONNX, scikit — uczenie i TensorFlow) bez wystąpienia InferenceConfig.
    + Dodano flagę zastępowania dla wdrożenia usługi (ACI i AKS) w zestawie SDK i interfejsie wiersza polecenia. Jeśli ta wartość jest określona, zastąpi istniejącą usługę, jeśli usługa o nazwie już istnieje. Jeśli usługa nie istnieje, program utworzy nową usługę.
    + Modele można zarejestrować przy użyciu dwóch nowych platform, Onnx i Tensorflow. — Rejestracja modelu akceptuje przykładowe dane wejściowe, przykładowe dane wyjściowe i konfigurację zasobów dla modelu.
  + **Azure-automl-Core**
    + Szkolenie iteracji zostanie uruchomione w procesie podrzędnym tylko wtedy, gdy są ustawiane ograniczenia środowiska uruchomieniowego.
    + Dodano Guardrail do prognozowania zadań, aby sprawdzić, czy określony max_horizon spowoduje problem z pamięcią na danym komputerze. W takim przypadku zostanie wyświetlony komunikat Guardrail.
    + Dodano obsługę złożonych częstotliwości, takich jak 2 lata i 1 miesiąc. -Dodano zrozumiały komunikat o błędzie, jeśli nie można ustalić częstotliwości.
    + Dodaj usługę Azure-default do automatycznie generowanej ENV Conda, aby rozwiązać błąd wdrażania modelu
    + Zezwalaj na konwertowanie pośrednich danych w potoku Azure Machine Learning, które mają być konwertowane do tabelarycznego zestawu danych i używane w `AutoMLStep`.
    + Zaimplementowana aktualizacja celu kolumny na potrzeby przesyłania strumieniowego.
    + Zaimplementowana aktualizacja parametrów transformatora dla programu Streamer i HashOneHotEncoder na potrzeby przesyłania strumieniowego.
    + Dodano bieżący rozmiar danych i minimalny wymagany rozmiar danych do komunikatów o błędach walidacji.
    + Zaktualizowano minimalny wymagany rozmiar danych na potrzeby weryfikacji krzyżowej w celu zagwarantowania co najmniej dwóch próbek w każdym złożeniu walidacji.
  + **Azure-Common — interfejs wiersza polecenia**
    + Interfejs wiersza polecenia obsługuje teraz pakowanie modeli.
    + Modele można zarejestrować przy użyciu dwóch nowych platform, Onnx i Tensorflow.
    + Rejestracja modelu akceptuje przykładowe dane wejściowe, przykładowe dane wyjściowe i konfigurację zasobów dla modelu.
  + **Azure-contrib-gbdt**
    + Naprawiono kanał wydania dla notesu
    + Dodano ostrzeżenie dla nieobsługiwanego elementu docelowego obliczeń AmlCompute
    + Dodano LightGMB szacowania do pakietu Azure-contrib-gbdt
  + [**Azure — rdzeń**](https://docs.microsoft.com/python/api/azureml-core)
    + Interfejs wiersza polecenia obsługuje teraz pakowanie modeli.
    + Dodaj ostrzeżenie o zaniechaniu dla przestarzałych interfejsów API zestawu danych. Zobacz informacje o zmianie interfejsu API zestawu danych w https://aka.ms/tabular-dataset.
    + Zmień [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) , aby zwracała nazwę i wersję rejestracji, jeśli zestaw danych jest zarejestrowany.
    + Usuń usterkę, która ScriptRunConfig z zestawem danych jako argumentem, nie można wielokrotnie użyć do przesłania uruchomienia eksperymentu.
    + Zestawy danych pobrane podczas przebiegu będą śledzone i mogą być widoczne na stronie Szczegóły uruchamiania lub przez wywołanie [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) po zakończeniu przebiegu.
    + Zezwalaj na konwertowanie pośrednich danych w potoku Azure Machine Learning, które mają być konwertowane do tabelarycznego zestawu danych i używane w [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep).
    + Dodano obsługę wdrażania i pakowania obsługiwanych modeli (ONNX, scikit — uczenie i TensorFlow) bez wystąpienia InferenceConfig.
    + Dodano flagę zastępowania dla wdrożenia usługi (ACI i AKS) w zestawie SDK i interfejsie wiersza polecenia. Jeśli ta wartość jest określona, zastąpi istniejącą usługę, jeśli usługa o nazwie już istnieje. Jeśli usługa nie istnieje, program utworzy nową usługę.
    +  Modele można zarejestrować przy użyciu dwóch nowych platform, Onnx i Tensorflow. Rejestracja modelu akceptuje przykładowe dane wejściowe, przykładowe dane wyjściowe i konfigurację zasobów dla modelu.
    + Dodano nowy magazyn danych dla Azure Database for MySQL. Dodano przykład używania Azure Database for MySQL w DataTransferStep w potokach Azure Machine Learning.
    + Dodano funkcje umożliwiające dodawanie i usuwanie tagów z eksperymentów dodanych do funkcji usuwania tagów z przebiegów
    + Dodano flagę zastępowania dla wdrożenia usługi (ACI i AKS) w zestawie SDK i interfejsie wiersza polecenia. Jeśli ta wartość jest określona, zastąpi istniejącą usługę, jeśli usługa o nazwie już istnieje. Jeśli usługa nie istnieje, program utworzy nową usługę.
  + [**Azure — datadryfowanie**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Przeniesiono z `azureml-contrib-datadrift` do `azureml-datadrift`
    + Dodano obsługę monitorowania zestawów danych szeregów czasowych dla dryfowania i innych środków statystycznych
    + Nowe metody `create_from_model()` i `create_from_dataset()` do klasy [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) . Metoda `create()` będzie przestarzała.
    + Korekty wizualizacji w języku Python i interfejsie użytkownika w programie Azure Machine Learning Studio.
    + Obsługuj cotygodniowe i comiesięczne planowanie monitorów, a także codziennie dla monitorów zestawu danych.
    + Obsługa wypełniania metryk monitora danych w celu analizowania danych historycznych dla monitorów zestawu danych.
    + Różne poprawki błędów
  + [**Azure — potok — rdzeń**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + Uczenie maszynowe nie jest już potrzebne do przesłania Azure Machine Learning uruchomienia potoku z pliku `yaml` potoku.
  + [**Uczenie maszynowe — automl**](/python/api/azureml-train-automl-runtime/)
    + Dodaj usługę Azure-default do automatycznie generowanej ENV Conda, aby rozwiązać błąd wdrażania modelu
    + AutoML szkolenie zdalne obejmuje teraz usługę Azure-defaults, która umożliwia ponowne użycie ENV na potrzeby wnioskowania.
  + **azureml-train-core**
    + Dodano obsługę PyTorch 1,3 w programie [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) szacowania

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Interfejs wizualny (wersja zapoznawcza)

+ Azure Machine Learning graficzny interfejs (wersja zapoznawcza) został przestawiony do uruchomienia na [potokach Azure Machine Learning](concept-ml-pipelines.md). Potoki (wcześniej znane jako eksperymenty) utworzone w interfejsie wizualizacji są teraz w pełni zintegrowane z podstawowymi Azure Machine Learning środowiska.
  + Ujednolicone środowisko zarządzania z zasobami zestawu SDK
  + Przechowywanie wersji i śledzenie dla modeli interfejsu wizualizacji, potoków i punktów końcowych
  + Przeprojektowany interfejs użytkownika
  + Dodano wdrożenie wnioskowania o partii
  + Dodano obsługę usługi Azure Kubernetes Service (AKS) dla celów obliczeniowych wnioskowania
  + Nowy przepływ pracy tworzenia potoku w języku Python
  + Nowa [Strona](https://ml.azure.com) docelowa dla narzędzi do tworzenia wizualnego

+ **Nowe moduły**
  + Zastosuj operacje matematyczne
  + Zastosuj transformację SQL
  + Przytnij wartości
  + Podsumowywanie danych
  + Importuj z usługi SQL Database

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.69

+ **Poprawki i ulepszenia błędów**
  + **Azure-automl-Core**
    + Ograniczanie objaśnień modelu do najlepszego przebiegu zamiast obliczania wyjaśnień dla każdego przebiegu. Zmiana tego zachowania na Local, Remote i ADB.
    + Dodano obsługę wyjaśnień modelu na żądanie dla interfejsu użytkownika
    + Dodano psutil jako zależność `automl` i uwzględnione psutil jako zależność Conda w amlcompute.
    + Rozwiązano problem związany z algorytmem heurystycznego spowolnienia i oknem kroczącym dla danych prognozowania, który określa niektóre serie mogące spowodować błędy algebry liniowe
      + Dodano drukowanie dla parametrów z algorytmem heurystycznym określonych w przebiegach prognozowania.
  + **Azure — contrib — datadryf**
    + Dodano ochronę podczas tworzenia metryk danych wyjściowych, jeśli w pierwszej sekcji nie ma dryfu poziomu zestawu danych.
  + **Azure-contrib — interpretacja**
    + Nazwa pakietu "Azure-contrib-wyjaśnij" została zmieniona na "Azure-contrib-Interpretuj"
  + **azureml-core**
    + Dodano interfejs API służący do wyrejestrowywania zestawów danych. `dataset.unregister_all_versions()`
    + Nazwa pakietu "Azure-contrib-wyjaśnij" została zmieniona na "Azure-contrib-interpretacja".
  + **[Azure — rdzeń](https://docs.microsoft.com/python/api/azureml-core)**
    + Dodano interfejs API służący do wyrejestrowywania zestawów danych. zestawu. [unregister_all_versions ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Dodano interfejs API zestawu danych w celu sprawdzenia czasu zmiany danych. `dataset.data_changed_time`.
    + Możliwość użycia `FileDataset` i `TabularDataset` jako danych wejściowych `PythonScriptStep`, `EstimatorStep`i `HyperDriveStep` w potoku Azure Machine Learning
    + Ulepszona wydajność `FileDataset.mount` dla folderów zawierających dużą liczbę plików
    + Możliwość korzystania z [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) i [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) jako danych wejściowych dla [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)i [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) w potoku Azure Machine Learning.
    + Wydajność FileDataset. Ulepszono [instalację ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none-) w przypadku folderów zawierających dużą liczbę plików
    + Dodano adres URL do znanych zaleceń dotyczących błędów w szczegółach uruchomienia.
    + Naprawiono usterkę w programie Run. get_metrics, w której żądania mogą zakończyć się niepowodzeniem, Jeśli uruchomienie miało zbyt wiele elementów podrzędnych
    + Naprawiono usterkę w programie [Run. get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) , w której żądania mogą zakończyć się niepowodzeniem, Jeśli uruchomienie miało zbyt wiele elementów podrzędnych
    + Dodano obsługę uwierzytelniania w klastrze Arcadia.
    + Utworzenie obiektu eksperymentu powoduje pobranie lub utworzenie eksperymentu w obszarze roboczym Azure Machine Learning na potrzeby śledzenia historii uruchamiania. Identyfikator eksperymentu i czas archiwizowany są wypełniane w obiekcie eksperymentu podczas tworzenia. Przykład: eksperyment = eksperyment (obszar roboczy, "nowy eksperyment") experiment_id = experiment.id Archive () i reactivate () są funkcjami, które mogą być wywoływane w ramach eksperymentu w celu ukrycia i przywrócenia eksperymentu z wyświetlania w środowisku użytkownika lub zwrócenia domyślnego w wywołaniu Aby wyświetlić listę eksperymentów. Jeśli zostanie utworzony nowy eksperyment o takiej samej nazwie jak zarchiwizowany eksperyment, można zmienić nazwę zarchiwizowanego eksperymentu podczas ponownej aktywacji, przekazując nową nazwę. Może istnieć tylko jeden aktywny eksperyment o danej nazwie. Przykład: experiment1 = eksperyment (obszar roboczy, "aktywny eksperyment") experiment1. archiwalny () # Utwórz nowy aktywny eksperyment o takiej samej nazwie jak zarchiwizowany. experiment2. = Eksperyment (obszar roboczy, "aktywny eksperyment") experiment1. reactivate (new_name = "poprzednio aktywny eksperyment") lista metod statycznych () na Eksperymentie może przyjmować filtr nazw i filtr widoku. Wartości ViewType to "ACTIVE_ONLY", "ARCHIVED_ONLY" i "ALL" przykład: archived_experiments = eksperyment. list (Workspace, view_type = "ARCHIVED_ONLY") all_first_experiments = eksperyment. list (Workspace, Name = "pierwszy eksperyment", view_type = "wszystkie")
    + Obsługa użycia środowiska do wdrażania modeli i aktualizacji usługi
  + **Azure — datadryfowanie**
    + Atrybut show klasy DataDriftDector nie będzie obsługiwał opcjonalnego argumentu "with_details". Atrybut show będzie zawierał tylko współczynnik dryfu danych i udział danych w kolumnach funkcji.
    + DataDriftDetector zmiany zachowania atrybutu "get_output":
      + Start_time parametru wejściowego, end_time są opcjonalne zamiast obowiązkowe;
      + Dane wejściowe określonego start_time i/lub end_time z określonym run_id w tym samym wywołaniu spowodują wyjątek błędu wartości, ponieważ wykluczają się wzajemnie.
      + Według danych wejściowych start_time i/lub end_time zostaną zwrócone tylko wyniki zaplanowanego uruchomienia.
      + Parametr "daily_latest_only" jest przestarzały.
    + Obsługa pobierania danych wyjściowych z dryfem opartym na DataSet.
  + **azureml-explain-model**
    + Zmienia nazwę pakietu usługi Uczenie maszynowe-Wyjaśnij, aby obtłumaczyć usługę Azure, zachowując stary pakiet pod kątem zgodności z poprzednimi wersjami
    + Naprawiono `automl` usterkę z nieprzetworzonymi wyjaśnieniami, zamiast regresji domyślnie przy pobieraniu z ExplanationClient
    + Dodaj obsługę `ScoringExplainer`, które mają być tworzone bezpośrednio przy użyciu `MimicWrapper`
  + **azureml-pipeline-core**
    + Zwiększona wydajność tworzenia dużych potoków
  + **azureml-train-core**
    + Dodano obsługę TensorFlow 2,0 w TensorFlow szacowania
  + **azureml-train-automl**
    + Utworzenie obiektu [eksperymentu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) powoduje pobranie lub utworzenie eksperymentu w obszarze roboczym Azure Machine Learning na potrzeby śledzenia historii uruchamiania. Identyfikator eksperymentu i czas archiwizowany są wypełniane w obiekcie eksperymentu podczas tworzenia. Przykład:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        Funkcja [Archive ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) i [reactivate ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) to funkcje, które mogą być wywoływane w ramach eksperymentu w celu ukrycia i przywrócenia eksperymentu z wyświetlania w środowisku użytkownika lub zwrócenia domyślnie w wywołaniu eksperymentów z listą. Jeśli zostanie utworzony nowy eksperyment o takiej samej nazwie jak zarchiwizowany eksperyment, można zmienić nazwę zarchiwizowanego eksperymentu podczas ponownej aktywacji, przekazując nową nazwę. Może istnieć tylko jeden aktywny eksperyment o danej nazwie. Przykład:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        Lista metod statycznych [()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) na eksperymentie może przyjmować filtr nazw i filtru widoku. Wartości ViewType są "ACTIVE_ONLY", "ARCHIVED_ONLY" i "ALL". Przykład:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Obsługa użycia środowiska do wdrażania modeli i aktualizacji usługi.
  + **[Azure — datadryfowanie](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + Atrybut show klasy [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) nie będzie obsługiwał opcjonalnego argumentu "with_details". Atrybut show będzie zawierał tylko współczynnik dryfu danych i udział danych w kolumnach funkcji.
    + DataDriftDetector funkcja [get_output]https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) zmiany zachowania:
      + Start_time parametru wejściowego, end_time są opcjonalne zamiast obowiązkowe;
      + Wejście start_time i/lub end_time z określonym run_id w tym samym wywołaniu spowoduje wyjątek błędu wartości, ponieważ wykluczają się wzajemnie.
      + Według danych wejściowych start_time i/lub end_time zostaną zwrócone tylko wyniki zaplanowanego uruchomienia.
      + Parametr "daily_latest_only" jest przestarzały.
    + Obsługa pobierania danych wyjściowych z dryfem opartym na DataSet.
  + **[Uczenie maszynowe — wyjaśnienie modelu](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Zmienia nazwę pakietu usługi Uczenie maszynowe-Wyjaśnij, aby obtłumaczyć usługę Azure i zachować stary pakiet na potrzeby zgodności z poprzednimi wersjami.
    + stała usterka AutoML z nieprzetworzonymi wyjaśnieniami, które zostały ustawione na zadanie klasyfikacji zamiast regresji domyślnie przy pobieraniu z ExplanationClient.
    + Dodawanie obsługi [ScoringExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.scoring.scoring_explainer.scoringexplainer) do tworzenia bezpośrednio przy użyciu [MimicWrapper](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic_wrapper.mimicwrapper)
  + **[Azure — potok — rdzeń](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Ulepszona wydajność tworzenia dużych potoków.
  + **[Azure — uczenie rdzeniowe](https://docs.microsoft.com/python/api/azureml-train-core)**
    + Dodano obsługę TensorFlow 2,0 w [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) szacowania.
  + **[Uczenie maszynowe — automl](/python/api/azureml-train-automl-runtime/)**
    + Uruchomienie przebiegu nadrzędnego nie powiedzie się, jeśli iteracja instalacji nie powiodła się, ponieważ organizacja już ją obsłuży.
    + Dodano obsługę funkcji Local-Docker i lokalnego Conda na potrzeby eksperymentów AutoML
    + Dodano obsługę funkcji Local-Docker i lokalnego Conda na potrzeby eksperymentów AutoML.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nowe środowisko sieci Web (wersja zapoznawcza) dla Azure Machine Learning obszarów roboczych

Karta eksperymenty w [nowym portalu obszaru roboczego](https://ml.azure.com) została zaktualizowana, aby analityki danych mogli monitorować eksperymenty w bardziej wydajny sposób. Można zapoznać się z następującymi funkcjami:
+ Zaeksperymentowanie metadanych w celu łatwego filtrowania i sortowania listy eksperymentów
+ Uproszczone i wydajne strony szczegółów eksperymentów, które umożliwiają wizualizowanie i porównywanie przebiegów
+ Nowy projekt do uruchamiania stron szczegółów, aby zrozumieć i monitorować przebiegi szkoleniowe

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.65

  + **Nowe funkcje**
    + Dodano środowiska nadzorowane. Te środowiska zostały wstępnie skonfigurowane z bibliotekami dla typowych zadań uczenia maszynowego i zostały wstępnie skompilowane i zbuforowane jako obrazy platformy Docker w celu przyspieszenia wykonania. Są one domyślnie wyświetlane na liście środowisk obszaru roboczego z prefiksem "Azure".
    + Dodano środowiska nadzorowane. Te środowiska zostały wstępnie skonfigurowane z bibliotekami dla typowych zadań uczenia maszynowego i zostały wstępnie skompilowane i zbuforowane jako obrazy platformy Docker w celu przyspieszenia wykonania. Są one domyślnie wyświetlane na liście środowisk [obszaru roboczego](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)z prefiksem "Azure".

  + **azureml-train-automl**
  + **[Uczenie maszynowe — automl](/python/api/azureml-train-automl-runtime/)**
    + Dodano obsługę konwersji ONNX dla ADB i HDI

+ **Funkcje w wersji zapoznawczej**
  + **azureml-train-automl**
  + **[Uczenie maszynowe — automl](/python/api/azureml-train-automl-runtime/)**
    + Obsługiwane BERT i BiLSTM jako featurized tekstu (tylko wersja zapoznawcza)
    + Obsługiwane dostosowanie cechowania dla parametrów przeznaczenie kolumny i transformator (tylko wersja zapoznawcza)
    + Obsługiwane nieprzetworzone wyjaśnienia, gdy użytkownik włącza wyjaśnienie modelu podczas uczenia (tylko wersja zapoznawcza)
    + Dodano PROPHET do prognozowania `timeseries` jako potoku do uczenia (tylko wersja zapoznawcza)

  + **Azure — contrib — datadryf**
    + Pakiety przechowywane z usługi Azure contrib-datadryfing do platformy Azure — datadryf; Pakiet `contrib` zostanie usunięty w przyszłej wersji

+ **Poprawki i ulepszenia błędów**
  + **Azure-automl-Core**
    + Wprowadzono FeaturizationConfig do AutoMLConfig i AutoMLBaseSettings
    + Wprowadzono FeaturizationConfig do [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) i AutoMLBaseSettings
      + Przesłoń cel kolumny dla cechowania z podaną kolumną i typem funkcji
      + Przesłoń parametry transformatora
    + Dodano komunikat o zaniechaniu dla explain_model () i retrieve_model_explanations ()
    + Dodano Prophet jako potok do uczenia (tylko wersja zapoznawcza)
    + Dodano komunikat o zaniechaniu dla explain_model () i retrieve_model_explanations ().
    + Dodano Prophet jako potok do uczenia (tylko wersja zapoznawcza).
    + Dodano obsługę automatycznego wykrywania spowolnienia docelowego, rozmiaru okna stopniowego i maksymalnego zakresu. Jeśli jedna z target_lags, target_rolling_window_size lub max_horizon jest ustawiona na wartość "Auto", heurystyka zostanie zastosowana do oszacowania wartości odpowiadającego parametru na podstawie danych szkoleniowych.
    + Stałe prognozowanie w przypadku, gdy zestaw danych zawiera jedną kolumnę ziarna, ten ziarno jest typu liczbowego i istnieje przerwy między zestawem pociągiem i testem
    + Rozwiązano komunikat o błędzie dotyczący zduplikowanego indeksu w trakcie wykonywania zadań prognozowania
    + Stałe prognozowanie w przypadku, gdy zestaw danych zawiera jedną kolumnę ziarna, ten ziarno jest typu liczbowego i istnieje przerwy między zestawem pociągiem i testowym.
    + Rozwiązano komunikat o błędzie dotyczący zduplikowanego indeksu w przebiegu zdalnym podczas prognozowania zadań.
    + Dodano Guardrail, aby sprawdzić, czy zestaw danych jest niezrównoważony. Jeśli tak jest, w konsoli zostanie zapisany komunikat Guardrail.
  + **azureml-core**
    + Dodano możliwość pobierania adresu URL sygnatury dostępu współdzielonego do modelu w magazynie za pomocą obiektu model. Np.: model. get_sas_url ()
    + Wprowadź `run.get_details()['datasets']`, aby uzyskać zestawy danych skojarzone z przesłanym przebiegiem
    + Dodaj `Dataset.Tabular.from_json_lines_files` interfejsu API, aby utworzyć TabularDataset z plików wierszy JSON. Aby dowiedzieć się więcej o tych danych tabelarycznych w plikach wierszy JSON w TabularDataset, odwiedź stronę https://aka.ms/azureml-data w celu uzyskania dokumentacji.
    + Dodano dodatkowe pola rozmiaru maszyny wirtualnej (dysk systemu operacyjnego, liczba procesorów GPU) do funkcji supported_vmsizes ()
    + Dodano dodatkowe pola do funkcji list_nodes (), aby pokazać przebieg, prywatny i publiczny adres IP, port itd.
    + Możliwość określenia nowego pola podczas aprowizacji klastra — remotelogin_port_public_access, które można ustawić jako włączone lub wyłączone w zależności od tego, czy port SSH ma pozostać otwarty, czy zamknięty w momencie tworzenia klastra. Jeśli nie określisz go, usługa będzie inteligentnie otwierać lub zamykać port w zależności od tego, czy klaster jest wdrażany w sieci wirtualnej.
  + **azureml-explain-model**
  + **[Azure — rdzeń](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Dodano możliwość pobierania adresu URL sygnatury dostępu współdzielonego do modelu w magazynie za pomocą obiektu model. Np.: model. [get_sas_url ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Wprowadź przebieg. [get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)["zestawy danych"], aby pobrać zestawy danych skojarzone z przesłanym przebiegiem
    + Dodaj `Dataset.Tabular`interfejsu API. [from_json_lines_files ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) w celu utworzenia TabularDataset z plików wierszy JSON. Aby dowiedzieć się więcej o tych danych tabelarycznych w plikach wierszy JSON w TabularDataset, odwiedź stronę https://aka.ms/azureml-data w celu uzyskania dokumentacji.
    + Dodano dodatkowe pola rozmiaru maszyny wirtualnej (dysk systemu operacyjnego, liczba procesorów GPU) do funkcji [supported_vmsizes ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Dodano dodatkowe pola do funkcji [list_nodes ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) , aby pokazać przebieg, prywatny i publiczny adres IP, port itd.
    + Możliwość określenia nowego pola podczas [aprowizacji](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) klastra `--remotelogin_port_public_access`, które można ustawić jako włączone lub wyłączone w zależności od tego, czy port SSH ma pozostać otwarty, czy zamknięty w momencie tworzenia klastra. Jeśli nie określisz go, usługa będzie inteligentnie otwierać lub zamykać port w zależności od tego, czy klaster jest wdrażany w sieci wirtualnej.
  + **[Uczenie maszynowe — wyjaśnienie modelu](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Ulepszona dokumentacja dotycząca wyników wyjaśnień w scenariuszu klasyfikacji.
    + Dodano możliwość przekazania przewidywanych wartości y w wyjaśnieniu dla przykładowych ocen. Odblokowuje więcej przydatnych wizualizacji.
    + Dodano Właściwość wyjaśniającej do MimicWrapper, aby umożliwić pobieranie bazowego MimicExplaineru.
  + **azureml-pipeline-core**
    + Dodano Notes do opisywania modułów, ModuleVersion i ModuleStep
  + **azureml-pipeline-steps**
    + Dodano RScriptStep do obsługi skryptu języka R za pośrednictwem potoku AML.
    + Stałe analizowanie parametrów metadanych w AzureBatchStep, co spowodowało, że komunikat o błędzie "przypisanie dla identyfikatora subskrypcji parametru nie jest określony".
  + **azureml-train-automl**
    + Obsługiwane training_data, validation_data, label_column_name weight_column_name jako format danych wejściowych
    + Dodano komunikat o zaniechaniu dla explain_model () i retrieve_model_explanations ()
  + **[Azure — potok — rdzeń](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Dodano [Notes](https://aka.ms/pl-modulestep) do opisywania [modułów](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)), [ModuleVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) i [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[Azure — potok — kroki](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + Dodano [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) do obsługi skryptu języka R za pośrednictwem potoku AML.
    + Nastąpiło analizowanie stałych parametrów metadanych w [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) , co spowodowało, że komunikat o błędzie "przypisanie dla identyfikatora subskrypcji parametru nie jest określony".
  + **[Uczenie maszynowe — automl](/python/api/azureml-train-automl-runtime/)**
    + Obsługiwane training_data, validation_data, label_column_name weight_column_name jako format danych wejściowych.
    + Dodano komunikat o zaniechaniu dla [explain_model ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) i [retrieve_model_explanations ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.62

+ **Nowe funkcje**
  + Wprowadza `timeseries` cechy w TabularDataset. Ta cecha umożliwia łatwe filtrowanie znaczników czasu dla danych TabularDataset, takich jak pobieranie wszystkich danych między zakresem czasu a najnowszymi danymi. Aby dowiedzieć się więcej na temat `timeseries` cech TabularDataset, odwiedź https://aka.ms/azureml-data do dokumentacji lub https://aka.ms/azureml-tsd-notebook przykładowego notesu.
  + Włączono szkolenia z TabularDataset i FileDataset. Odwiedź witrynę https://aka.ms/dataset-tutorial, aby zapoznać się z przykładowym notesem.

  + **azureml-train-core**
    + Dodano obsługę `Nccl` i `Gloo` w PyTorch szacowania

+ **Poprawki i ulepszenia błędów**
  + **Azure-automl-Core**
    + Zaniechano ustawienia AutoML "lag_length" i LaggingTransformer.
    + Naprawiono poprawną weryfikację danych wejściowych, jeśli są one określone w formacie przepływu danych
    + Zmodyfikowano fit_pipeline. PR, aby wygenerować kod JSON grafu i przekazać go do artefaktów.
    + Renderowanie grafu w `userrun` przy użyciu `Cytoscape`.
  + **azureml-core**
    + Oglądany obsługę wyjątków w kodzie ADB i Wprowadzaj zmiany zgodnie z nową obsługą błędów
    + Dodano automatyczne uwierzytelnianie MSI dla maszyn wirtualnych notesu.
    + Naprawia usterkę, w przypadku której można przekazać uszkodzone lub puste modele z powodu nieudanych ponownych prób.
    + Naprawiono usterkę, w której nazwa `DataReference` zmienia się podczas zmiany trybu `DataReference` (np. podczas wywoływania `as_upload`, `as_download`lub `as_mount`).
    + Wprowadź `mount_point` i `target_path` opcjonalne dla `FileDataset.mount` i `FileDataset.download`.
    + Wyjątek, że nie można odnaleźć kolumny sygnatur czasowych, zostanie zgłoszony, jeśli interfejs API związany z połączeniami szeregowymi jest wywoływany bez określonej kolumny sygnatury czasowej lub że przypisane kolumny sygnatur czasowych zostaną usunięte.
    + Kolumny szeregów czasowych powinny być przypisane do kolumny, której typem jest Date, w przeciwnym razie oczekiwany jest wyjątek
    + Kolumny szeregów czasowych przypisujące interfejs API "with_timestamp_columns" może przyjmować nierówną wartość o wartości none/Gruba kolumna znaczników czasu, co spowoduje wyczyszczenie wcześniej przypisanych kolumn sygnatur czasowych.
    + Wyjątek zostanie wygenerowany, gdy kolumna "Gruba ziarnista" lub "Szczegółowa sygnatura czasowa" zostanie porzucona z oznaczeniem użytkownika, który może zostać wykonany po wykluczeniu kolumny sygnatur czasowych na liście upuszczania lub wywołaniu with_time_stamp bez wartości none do sygnatury czasowej zwolnienia Columns
    + Wyjątek zostanie wygenerowany, gdy kolumna "grube" lub "Szczegółowa sygnatura czasowa" nie zostanie uwzględniona w liście Zachowaj kolumny z oznaczeniem dla użytkownika, który będzie można wykonać po uwzględnieniu kolumny sygnatury czasowej w Zachowaj listę kolumn lub wywoływać with_time_stamp bez wartość, aby zwolnić kolumny sygnatur czasowych.
    + Dodano rejestrowanie dla rozmiaru zarejestrowanego modelu.
  + **azureml-explain-model**
    + Naprawiono ostrzeżenie w konsoli, gdy pakiet języka Python "pakowanie" nie jest zainstalowany: "Używanie starszej niż obsługiwanej wersji programu lightgbm, Uaktualnij do wersji nowszej niż 2.2.1"
    + Stałe wyjaśnienie modelu pobierania z fragmentowania dla globalnych wyjaśnień z wieloma funkcjami
    + Brak przykładów inicjalizacji w wyjaśnieniu danych wyjściowych dla nierozwiązanego śladu
    + Naprawiono niezmienny błąd na właściwościach ustawionych podczas przekazywania z wyjaśnieniem klienta przy użyciu dwóch różnych typów modeli
    + Dodano get_raw PARAM do programu do oceny objaśnień. Wyjaśnij (), dzięki czemu jeden z objaśnień oceniających może zwrócić zarówno wartości przetworzone, jak i pierwotne.
  + **azureml-train-automl**
    + Wprowadzono publiczne interfejsy API z AutoML, aby obsłużyć wyjaśnienia z `automl` Wyjaśnij zestaw SDK — nowszy sposób obsługi AutoMLych wyjaśnień, odsprzęgając AutoML cechowania i wyjaśnij zintegrowane z zestawem SDK kompleksowe rozwiązanie z wyjaśnieniami dotyczącymi platformy Azure dla modeli AutoML.
    + Usuwanie z usługi Azure Defaults z zdalnych środowisk szkoleniowych.
    + Zmieniono domyślną lokalizację magazynu pamięci podręcznej z plik filecachestore na jeden, aby AzureFileCacheStore jeden dla AutoML na Azure Databricks ścieżce kodu.
    + Naprawiono poprawną weryfikację danych wejściowych, jeśli są one określone w formacie przepływu danych
  + **azureml-train-core**
    + Przywrócono source_directory_data_store przestarzałe.
    + Dodano możliwość przesłonięcia wersji pakietu z zainstalowanym usługą Azure.
    + Dodano obsługę pliku dockerfile w parametrze `environment_definition` w szacowania.
    + Uproszczone dystrybuowanie parametrów szkoleniowych w szacowania.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nowe środowisko sieci Web (wersja zapoznawcza) dla Azure Machine Learning obszarów roboczych
Nowe środowisko internetowe umożliwia analitykom danych i inżynierom danych ukończenie kompleksowych cykli życia uczenia maszynowego z przygotowywanie i wizualizowania danych w celu uczenia i wdrażania modeli w jednej lokalizacji.

![Interfejs użytkownika obszaru roboczego Azure Machine Learning (wersja zapoznawcza)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Najważniejsze funkcje:**

Korzystając z tego nowego interfejsu Azure Machine Learning, możesz teraz:
+ Zarządzanie notesami lub łączenie się z usługą Jupyter
+ [Uruchamianie zautomatyzowanych eksperymentów ML](tutorial-first-experiment-automated-ml.md)
+ [Tworzenie zestawów danych z lokalnych plików, magazynów elementów, & plików sieci Web](how-to-create-register-datasets.md)
+ Eksplorowanie & Przygotowanie zestawów danych do tworzenia modelu
+ Monitoruj dryfowanie danych dla modeli
+ Wyświetlanie ostatnich zasobów z pulpitu nawigacyjnego

W czasie tej wersji obsługiwane są następujące przeglądarki: Chrome, Firefox, Safari i Microsoft Edge Preview.

**Znane problemy:**

1. Odśwież przeglądarkę, Jeśli zobaczysz komunikat "coś poszło źle! Wystąpił błąd podczas ładowania plików fragmentu, gdy wdrożenie jest w toku.

1. Nie można usunąć pliku ani zmienić jego nazwy w notesach i plikach. W publicznej wersji zapoznawczej można użyć interfejsu użytkownika Jupyter lub terminalu w maszynie wirtualnej notesu do wykonywania operacji aktualizacji plików. Ponieważ jest to zainstalowany system plików NFS, wszystkie zmiany wprowadzone na maszynie wirtualnej notesu są natychmiast odzwierciedlane w obszarze roboczym Notes.

1. Aby SSH do maszyny wirtualnej notesu:
   1. Znajdź klucze SSH, które zostały utworzone podczas konfiguracji maszyny wirtualnej. Lub Znajdź klucze w obszarze roboczym Azure Machine Learning > Otwórz kartę obliczeniową > Znajdź maszynę wirtualną Notes na liście > Otwórz jej właściwości: Skopiuj klucze z okna dialogowego.
   1. Zaimportuj te publiczne i prywatne klucze SSH do komputera lokalnego.
   1. Używaj ich do używania protokołu SSH w notesie maszyn wirtualnych.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.60

+ **Nowe funkcje**
  + Wprowadzono FileDataset, która odwołuje się do jednego lub wielu plików w magazynach danych lub publicznych adresach URL. Pliki mogą być w dowolnym formacie. FileDataset umożliwia pobieranie lub Instalowanie plików na potrzeby obliczeń. Aby dowiedzieć się więcej na temat FileDataset, odwiedź stronę https://aka.ms/file-dataset.
  + Dodano obsługę YAML potoku dla kroku PythonScript, krok adla, krok, etap, DataTransferStep i AzureBatch kroku

+ **Poprawki i ulepszenia błędów**
  + **Azure-automl-Core**
    + AutoArima jest teraz pożądanym potokiem tylko dla wersji zapoznawczej.
    + Ulepszone raportowanie błędów w przypadku prognozowania.
    + Ulepszone rejestrowanie przy użyciu wyjątków niestandardowych zamiast ogólnych w zadaniach prognozowania.
    + Usunięto sprawdzenie max_concurrent_iterations, aby było mniejsze niż łączna liczba iteracji.
    + Modele AutoML teraz zwracają AutoMLExceptions
    + Ta wersja zwiększa wydajność wykonywania zautomatyzowanych uruchomień lokalnych w usłudze Machine Learning.
  + **azureml-core**
    + Wprowadź zestaw danych. get_all (obszar roboczy), który zwraca słownik `TabularDataset` i `FileDataset` obiektów objętych nazwą rejestracji.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Wprowadź `parition_format` jako argument do `Dataset.Tabular.from_delimited_files` i `Dataset.Tabular.from_parquet.files`. Informacje o partycji każdej ścieżki danych zostaną wyodrębnione do kolumn w oparciu o określony format. "{column_name}" tworzy kolumnę String, a "{column_name: RRRR/MM/DD/HH/mm/SS}" tworzy kolumnę datetime, gdzie "RRRR", "MM", "DD", "gg", "mm" i "SS" są używane do wyodrębniania wartości typu Year, month, Day, Hour, minute i Second. Partition_format powinna rozpoczynać się od pozycji pierwszego klucza partycji aż do końca ścieżki pliku. Na przykład dana ścieżka ". /USA/2019/01/01/data.csv "gdzie partycja jest według kraju i godziny, partition_format ="/{Country}/{PartitionDate: RRRR/MM/DD}/Data. csv "tworzy kolumnę ciągu" Country "z wartością" USA "i kolumną DateTime" PartitionDate "o wartości" 2019-01-01 ".
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Wprowadź `partition_format` jako argument do `Dataset.Tabular.from_delimited_files` i `Dataset.Tabular.from_parquet.files`. Informacje o partycji każdej ścieżki danych zostaną wyodrębnione do kolumn w oparciu o określony format. "{column_name}" tworzy kolumnę String, a "{column_name: RRRR/MM/DD/HH/mm/SS}" tworzy kolumnę datetime, gdzie "RRRR", "MM", "DD", "gg", "mm" i "SS" są używane do wyodrębniania wartości typu Year, month, Day, Hour, minute i Second. Partition_format powinna rozpoczynać się od pozycji pierwszego klucza partycji aż do końca ścieżki pliku. Na przykład dana ścieżka ". /USA/2019/01/01/data.csv "gdzie partycja jest według kraju i godziny, partition_format ="/{Country}/{PartitionDate: RRRR/MM/DD}/Data. csv "tworzy kolumnę ciągu" Country "z wartością" USA "i kolumną DateTime" PartitionDate "o wartości" 2019-01-01 ".
    + Metody `to_csv_files` i `to_parquet_files` zostały dodane do `TabularDataset`. Te metody umożliwiają konwersję między `TabularDataset` i `FileDataset`, konwertując dane do plików o określonym formacie.
    + Podczas zapisywania pliku dockerfile wygenerowanego przez model () automatycznie loguj się do podstawowego rejestru obrazu.
    + "gpu_support" nie jest już konieczne; AML teraz automatycznie wykrywa i używa rozszerzenia platformy Docker firmy NVIDIA, gdy jest dostępne. Zostanie usunięta w przyszłej wersji.
    + Dodano obsługę tworzenia, aktualizowania i używania PipelineDrafts.
    + Ta wersja zwiększa wydajność wykonywania zautomatyzowanych uruchomień lokalnych w usłudze Machine Learning.
    + Użytkownicy mogą badać metryki z historii uruchamiania według nazwy.
    + Ulepszone rejestrowanie przy użyciu wyjątków niestandardowych zamiast ogólnych w zadaniach prognozowania.
  + **azureml-explain-model**
    + Dodano parametr feature_maps do nowego MimicWrapper, dzięki czemu użytkownicy mogą uzyskiwać nieprzetworzone wyjaśnienia funkcji.
    + Przekazywanie zestawu danych jest teraz domyślnie wyłączone w celu przekazania wyjaśnień i można je ponownie włączyć przy użyciu upload_datasets = true
    + Dodano parametry filtrowania "is_law" do listy wyjaśnień i funkcji pobierania.
    + Dodaje metodę `get_raw_explanation(feature_maps)` do globalnych i lokalnych obiektów wyjaśnień.
    + Dodano Sprawdzanie wersji do lightgbm z wydrukowanym ostrzeżeniem, jeśli poniżej obsługiwanej wersji
    + Zoptymalizowane użycie pamięci podczas tworzenia wsadowych objaśnień
    + Modele AutoML teraz zwracają AutoMLExceptions
  + **azureml-pipeline-core**
    + Dodano obsługę tworzenia, aktualizowania i używania PipelineDrafts — może służyć do obsługi definicji potoku mutable i używania ich interaktywnie do uruchamiania
  + **azureml-train-automl**
    + Utworzono funkcję służącą do instalowania określonych wersji procesora GPU pytorch v 1.1.0, :::no-loc text="cuda"::: Toolkit 9,0, pytorch-transformatorów, które są wymagane do włączenia BERT/XLNet w środowisku uruchomieniowym języka Python.
  + **azureml-train-core**
    + Wczesna awaria niektórych z parametrów w zestawie SDK, a nie po stronie serwera.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning zestawu SDK 1.1.14 przygotowywania danych
+ **Poprawki i ulepszenia błędów**
  + Włączono funkcję zapisu do ADLS/ADLSGen2 przy użyciu nieprzetworzonej ścieżki i poświadczeń.
  + Naprawiono usterkę, która spowodowała `include_path=True`, aby nie działała `read_parquet`.
  + Rozwiązano błąd `to_pandas_dataframe()` spowodowany przez wyjątek "Nieprawidłowa wartość właściwości: hostSecret".
  + Naprawiono usterkę polegającą na tym, że nie można odczytać plików na DBFS w trybie Spark.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.57
+ **Nowe funkcje**
  + Włączone `TabularDataset`, które mają być używane przez AutomatedML. Aby dowiedzieć się więcej na temat `TabularDataset`, odwiedź stronę https://aka.ms/azureml/howto/createdatasets.

+ **Poprawki i ulepszenia błędów**
  + **automl-Client-Core-nativeclient**
    + Rozwiązano błąd wywoływany, gdy szkolenia i/lub etykiety sprawdzania poprawności (y i y_valid) są podane w postaci Pandas Dataframe, ale nie jako tablica numpy.
    + Zaktualizowany interfejs do tworzenia `RawDataContext` wymaga tylko danych i `AutoMLBaseSettings` obiektu.
    +  Zezwól użytkownikom AutoML na porzucanie serii szkoleniowych, które nie są wystarczająco długie podczas prognozowania. -Zezwól użytkownikom AutoML na porzucanie ziaren z zestawu testów, który nie istnieje w zestawie szkoleniowym podczas prognozowania.
  + **Azure — interfejs wiersza polecenia**
    + Teraz można zaktualizować certyfikat protokołu SSL dla punktu końcowego oceniania wdrożonego w klastrze AKS zarówno dla wygenerowanego przez firmę Microsoft, jak i dla certyfikatu klienta.
  + **Azure-automl-Core**
    + Rozwiązano problem w AutoML, w którym wiersze z brakującymi etykietami nie zostały prawidłowo usunięte.
    + Ulepszono rejestrowanie błędów w AutoML; Pełne komunikaty o błędach będą teraz zawsze zapisywane w pliku dziennika.
    + AutoML zaktualizował Przypinanie pakietu w celu uwzględnienia `azureml-defaults`, `azureml-explain-model`i `azureml-dataprep`. Program AutoML nie będzie już ostrzegał o niezgodności pakietów (z wyjątkiem pakietu `azureml-train-automl`).
    + Rozwiązano problem w `timeseries`, gdzie podziały OKS mają nierówne rozmiary powodujące niepowodzenie obliczeń w pojemniku.
    + W przypadku korzystania z iteracji zestawu danych dla typu uczenia krzyżowego, jeśli zakończył się problemami z pobraniem modeli przeszkolonych na całym zestawie, wystąpił niespójność między modelami wag i modelami, które zostały wprowadzone do głosu Zestawy.
    + Rozwiązano błąd wywoływany, gdy szkolenia i/lub etykiety sprawdzania poprawności (y i y_valid) są podane w postaci Pandas Dataframe, ale nie jako tablica numpy.
    + Rozwiązano problem z zadaniami prognozowania, gdy żaden z nich nie został napotkany w kolumnach logicznych tabel wejściowych.
    + Zezwól użytkownikom AutoML na porzucanie serii szkoleniowych, które nie są wystarczająco długie podczas prognozowania. -Zezwól użytkownikom AutoML na porzucanie ziaren z zestawu testów, który nie istnieje w zestawie szkoleniowym podczas prognozowania.
  + **azureml-core**
    + Rozwiązano problem z kolejnością parametrów blob_cache_timeout.
    + Dodano zewnętrzne Typy wyjątków dopasowania i transformacji do błędów systemowych.
    + Dodano obsługę Key Vault wpisów tajnych dla zdalnych uruchomień. Dodaj magazyn usługi Azure. Core. kluczy, aby dodać, pobrać i wyświetlić wpisy tajne z magazynu kluczy skojarzonego z Twoim obszarem roboczym. Obsługiwane są następujące operacje:
      + Azure. Core. Workspace. Workspace. get_default_keyvault ()
      + Azure. Core., Magazyn kluczy. set_secret (nazwa, wartość)
      + Azure. Core., Magazyn kluczy. set_secrets (secrets_dict)
      + Azure. Core. z magazynu kluczy. Magazyn kluczy. get_secret (nazwa)
      + Azure. Core., Magazyn kluczy. get_secrets (secrets_list)
      + Azure. Core., Magazyn kluczy. list_secrets ()
    + Dodatkowe metody uzyskiwania domyślnego magazynu kluczy i uzyskiwania wpisów tajnych podczas zdalnego uruchomienia:
      + Azure. Core. Workspace. Workspace. get_default_keyvault ()
      + Azure. Core. Run. Run. get_secret (nazwa)
      + Azure. Core. Run. Run. get_secrets (secrets_list)
    + Dodano dodatkowe parametry przesłonięcia w celu przesłania polecenia CLI.
    + Zwiększ niezawodność wywołań interfejsu API, aby rozszerzać ponowną próbę na typowe wyjątki biblioteki żądań.
    + Dodano obsługę przesyłania przebiegów z przesłanego przebiegu.
    + Rozwiązano problem związany z wygaśnięciem tokenu sygnatury dostępu współdzielonego w FileWatcher, co spowodowało zatrzymanie przekazywania plików po wygaśnięciu tokenu początkowego.
    + Obsługiwane Importowanie plików CSV/TSV protokołu HTTP w zestawie danych Python SDK.
    + Zaniechano metody Workspace. Setup (). Komunikat ostrzegawczy pokazywany użytkownikom sugeruje użycie polecenia Create () lub Get ()/from_config ().
    + Dodano środowisko. add_private_pip_wheel (), które umożliwia przekazywanie prywatnych niestandardowych pakietów języka Python `whl`do obszaru roboczego i bezpieczne ich używanie do kompilowania/zmaterializowania środowiska.
    + Teraz można zaktualizować certyfikat protokołu SSL dla punktu końcowego oceniania wdrożonego w klastrze AKS zarówno dla wygenerowanego przez firmę Microsoft, jak i dla certyfikatu klienta.
  + **azureml-explain-model**
    + Dodano parametr służący do dodawania identyfikatora modelu do wyjaśnień podczas przekazywania.
    + Dodano `is_raw` tagowania do wyjaśnień w pamięci i przekazywania.
    + Dodano obsługę pytorch i testy dla pakietu Azure-Wyjaśnij model.
  + **azureml-opendatasets**
    + Obsługa wykrywania i rejestrowania środowiska testowego.
    + Dodano klasy w celu uzyskania wypełniania do USA według powiatów i zip.
  + **azureml-pipeline-core**
    + Dodano Właściwość Label do definicji portów wejściowych i wyjściowych.
  + **Azure-Telemetria**
    + Naprawiono niepoprawną konfigurację telemetrii.
  + **azureml-train-automl**
    + Naprawiono usterkę dotyczącą niepowodzenia instalacji, błąd nie był rejestrowany w polu "błędy" dla uruchomienia Instalatora i dlatego nie został on zapisany w nadrzędnym przebiegu "Errors".
    + Rozwiązano problem w AutoML, w którym wiersze z brakującymi etykietami nie zostały prawidłowo usunięte.
    + Zezwól użytkownikom AutoML na porzucanie serii szkoleniowych, które nie są wystarczająco długie podczas prognozowania.
    + Zezwalaj użytkownikom AutoML na upuszczanie ziaren z zestawu testów, który nie istnieje w zestawie szkoleniowym podczas prognozowania.
    + Teraz AutoMLStep `automl` konfigurację do zaplecza, aby uniknąć problemów ze zmianami lub dodatkami nowych parametrów konfiguracji.
    + AutoML Data Guardrail jest teraz w publicznej wersji zapoznawczej. Użytkownik zobaczy raport Guardrail danych (dla zadań klasyfikacji/regresji) po szkoleniu, a także będzie mógł uzyskać do niego dostęp za pomocą interfejsu API zestawu SDK.
  + **azureml-train-core**
    + Dodano obsługę Torch 1,2 w PyTorch szacowania.
  + **Azure — widżety**
    + Udoskonalone wykresy macierzowe z błędami na potrzeby szkoleń klasyfikacji.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning zestawu SDK 1.1.12 przygotowywania danych
+ **Nowe funkcje**
  + Listy ciągów mogą teraz być przesyłane jako dane wejściowe do metod `read_*`.

+ **Poprawki i ulepszenia błędów**
  + Wydajność `read_parquet` została znacznie ulepszona podczas pracy w usłudze Spark.
  + Rozwiązano problem polegający na tym, że `column_type_builder` nie powiodło się w przypadku pojedynczej kolumny z niejednoznacznymi formatami daty.

### <a name="azure-portal"></a>Portal Azure
+ **Funkcja wersji zapoznawczej**
  + Przesyłanie strumieniowe plików dziennika i danych wyjściowych jest teraz dostępne dla stron szczegółów uruchamiania. Po włączeniu przełącznika podglądu pliki będą przesyłać strumieniowo aktualizacje w czasie rzeczywistym.
  + Możliwość ustawiania limitu przydziału na poziomie obszaru roboczego jest publikowana w wersji zapoznawczej. Przydziały AmlCompute są przydzielane na poziomie subskrypcji, ale teraz można rozpowszechnić ten przydział między obszarami roboczymi i przydzielić go do sprawiedliwego udostępniania i zarządzania. Wystarczy kliknąć blok **użycie i limity przydziału** na lewym pasku nawigacyjnym obszaru roboczego i wybrać kartę **Konfiguruj przydziały** . Zwróć uwagę na to, że administrator subskrypcji może ustawić przydziały na poziomie obszaru roboczego, ponieważ jest to operacja między obszarami roboczymi.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.55

+ **Nowe funkcje**
  + Uwierzytelnianie oparte na tokenach jest teraz obsługiwane na potrzeby wywołań punktu końcowego oceniania wdrożonego w AKS. Będziemy nadal obsługiwać bieżące uwierzytelnianie oparte na kluczach, a użytkownicy mogą korzystać z jednego z tych mechanizmów uwierzytelniania jednocześnie.
  + Możliwość zarejestrowania magazynu obiektów BLOB znajdującego się za siecią wirtualną (VNet) jako magazynem danych.

+ **Poprawki i ulepszenia błędów**
  + **Azure-automl-Core**
    + Naprawia usterkę, w której rozmiar walidacji podziałów OKS jest mały i powoduje nieprawidłowe wykresy przewidywane a rzeczywiste dla regresji i prognozowania.
    + Rejestrowanie zadań prognozowania w trakcie zdalnego działania zostało ulepszone, teraz użytkownik jest dostarczany z kompleksowym komunikatem o błędzie, Jeśli uruchomienie nie powiodło się.
    + Rozwiązano błędy `Timeseries`, jeśli flaga preprocesora ma wartość true.
    + Niektóre komunikaty o błędach sprawdzania poprawności danych prognozowania są bardziej funkcjonalne.
    + Zmniejszone użycie pamięci przez AutoML jest uruchamiane przez porzucanie i/lub opóźnione ładowanie zestawów danych, w szczególności między zduplikowanymi procesami
  + **Azure-contrib-opis-model**
    + Dodano flagę model_task do objaśnień, aby umożliwić użytkownikowi przesłonięcie domyślnej automatycznej logiki wnioskowania dla typu modelu
    + Zmiany widżetu: program jest automatycznie instalowany przy użyciu `contrib`, nie ma więcej `nbextension` instalacji/włączania — wyjaśnienie z tylko globalną ważnością funkcji (np. Permutative)
    + Zmiany pulpitu nawigacyjnego:-Box i Violins (oprócz `beeswarm` kreolenia na stronie podsumowania — znacznie szybsze ponowne renderowanie `beeswarm` na liście suwaka "Top-k" — pomoc wyjaśniająca, jak najwyższe-k jest obliczane i przydatne dostosowywalne komunikaty zamiast wykresów, gdy dane nie zostały dostarczone
  + **azureml-core**
    + Dodano metodę model. Package () w celu utworzenia obrazów platformy Docker i wieloetapowe dockerfile, które hermetyzują modele i ich zależności.
    + Zaktualizowano lokalne usługi WebService, aby akceptować InferenceConfigs zawierające obiekty środowiska.
    + Naprawiono model. Register () podczas tworzenia nieprawidłowych modeli, gdy "." (dla bieżącego katalogu) jest przenoszona jako parametr model_path.
    + Dodawanie przebiegu. submit_child, funkcje odzwierciedlają eksperyment. Prześlij podczas określania przebiegu jako elementu nadrzędnego przesłanego przebiegu podrzędnego.
    + Obsługa opcji konfiguracji z metody model. Register w programie Run. register_model.
    + Możliwość uruchamiania zadań JAR w istniejącym klastrze.
    + Teraz obsługiwane instance_pool_id i cluster_log_dbfs_path parametrów.
    + Dodano obsługę używania obiektu środowiska podczas wdrażania modelu w usłudze sieci Web. Obiekt środowiska można teraz dostarczyć jako część obiektu InferenceConfig.
    + Dodawanie mapowania appinsifht dla nowych regionów — środkowe i zachodnie-northcentralus
    + Dodano dokumentację dla wszystkich atrybutów we wszystkich klasach magazynu danych.
    + Dodano blob_cache_timeout parametru do `Datastore.register_azure_blob_container`.
    + Dodano metody save_to_directory i load_from_directory do programu Azure. Core. Environment. Environment.
    + Dodano polecenia "AZ ml Download Environment" i "polecenie" AZ ml Environment Register "do interfejsu CLI.
    + Dodano metodę Environment. add_private_pip_wheel.
  + **azureml-explain-model**
    + Dodano śledzenie zestawu danych do wyjaśnień za pomocą usługi DataSet (wersja zapoznawcza).
    + Zmniejszono domyślny rozmiar wsadu podczas przesyłania globalnych wyjaśnień z 10 000 do 100.
    + Dodano flagę model_task do objaśnień, aby umożliwić użytkownikowi przesłonięcie domyślnej automatycznej logiki wnioskowania dla typu modelu.
  + **azureml-mlflow**
    + Naprawiono usterkę w mlflow. Azure. build_image, gdzie zagnieżdżone katalogi są ignorowane.
  + **azureml-pipeline-steps**
    + Dodano możliwość uruchamiania zadań JAR w istniejącym klastrze Azure Databricks.
    + Dodano instance_pool_id obsługi i cluster_log_dbfs_path parametrów dla kroku DatabricksStep.
    + Dodano obsługę parametrów potoku w kroku DatabricksStep.
  + **azureml-train-automl**
    + Dodano `docstrings` dla plików związanych z kompletem.
    + Zaktualizowane dokumenty do bardziej odpowiedniego języka dla `max_cores_per_iteration` i `max_concurrent_iterations`
    + Rejestrowanie zadań prognozowania w trakcie zdalnego działania zostało ulepszone, teraz użytkownik jest dostarczany z kompleksowym komunikatem o błędzie, Jeśli uruchomienie nie powiodło się.
    + Usunięto get_data z notesu `automlstep` potoku.
    + Rozpoczęto obsługę `dataprep` w `automlstep`.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning zestawu SDK 1.1.10 przygotowywania danych

+ **Nowe funkcje**
  + Teraz możesz poprosić o wykonanie określonych inspektorów (np. histogramu, wykresu punktowego itp.) w określonych kolumnach.
  + Dodano argument zrównoleglanie do `append_columns`. Jeśli wartość jest równa true, dane zostaną załadowane do pamięci, ale wykonywanie zostanie uruchomione równolegle; w przypadku wartości false wykonywanie będzie przesyłane strumieniowo, ale pojedyncze wątki.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.53

+ **Nowe funkcje**
  + Zautomatyzowane Machine Learning teraz obsługuje modele szkoleń ONNX na zdalnym miejscu docelowym obliczeń
  + Azure Machine Learning teraz zapewnia możliwość wznowienia szkolenia z poprzedniego przebiegu, punktów kontrolnych lub plików modeli.
    + Dowiedz się [, jak za pomocą usługi szacowania wznowić szkolenia z poprzedniego przebiegu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Poprawki i ulepszenia błędów**
  + **automl-Client-Core-nativeclient**
    + Usuń usterkę dotyczącą typów kolumn utracie po przekształceniu (połączona usterka);
    + Zezwalaj, aby y_query być typem obiektu zawierającym brak (s) na początku (#459519).
  + **Azure — interfejs wiersza polecenia**
    + Polecenia CLI "model Deploy" i "Aktualizacja usługi" teraz akceptują parametry, pliki konfiguracji lub kombinację dwóch. Parametry mają pierwszeństwo przed atrybutami w plikach.
    + Opis modelu można teraz zaktualizować po rejestracji
  + **Azure-automl-Core**
    + Zaktualizuj zależność NimbusML do wersji 1.2.0 (aktualna Najnowsza wersja).
    + Dodawanie obsługi potoków NimbusML szacowania &, które mają być używane w AutoML szacowania.
    + Naprawianie usterki w procedurze wyboru kompletu, która niepotrzebnie zwiększa wynikowy element, nawet jeśli wyniki pozostawały stałe.
    + Włącz ponowne użycie niektórych featurizations w ramach podziałów OKS na potrzeby prognozowania zadań. Przyspiesza to Uruchamianie Instalatora przez przekroczenie współczynnika n_cross_validations dla kosztownych featurizations, takich jak spowolnienia i stopniowające okna.
    + Rozwiązywanie problemu, jeśli czas jest poza Pandas obsługiwanym zakresem czasu. Teraz możemy podnieść wartość DataException, jeśli czas jest krótszy niż PD. Sygnatura czasowa. minimalna lub większa od PD. Timestamp. Max
    + Prognozowanie umożliwia teraz różne częstotliwości w zestawach uczenia i testu, jeśli można je wyrównać. Na przykład "kwartalne rozpoczynające się w styczniu" i "co kwartał zaczynają się w październiku" można wyrównać.
    + Właściwość "Parameters" została dodana do TimeSeriesTransformer.
    + Usuń stare klasy wyjątków.
    + W przypadku zadań prognozowania parametr `target_lags` akceptuje teraz jedną liczbę całkowitą lub listę liczb całkowitych. Jeśli podano liczbę całkowitą, zostanie utworzone tylko jedno opóźnienie. W przypadku podanej listy zostaną wykonane unikatowe wartości spowolnienia. target_lags = [1, 2, 2, 4] utworzy spowolnienia jednego, 2 i 4 okresów.
    + Usuń usterkę dotyczącą utraty typów kolumn po przekształceniu (połączona usterka);
    + W `model.forecast(X, y_query)`Zezwól y_query na typ obiektu zawierający brak (s) na początku (#459519).
    + Dodaj oczekiwane wartości do `automl` danych wyjściowych
  + **Azure — contrib — datadryf**
    +  Ulepszenia przykładowego notesu, w tym przełączanie do platformy Uczenie maszynowe opendatasets zamiast platformy Azure-contrib-opendatasets i wydajności podczas wzbogacania danych
  + **Azure-contrib-opis-model**
    + Stałe przekształcenia — argument dla objaśniania WAPNa dla wagi nieprzetworzonej funkcji w usłudze Azure contrib-Wyjaśnij pakiet modelu
    + Dodano segmentacji do wyjaśnień obrazu w opisie obrazu dla pakietu Azure-contrib-Wyjaśnij-model
    + Dodaj obsługę rozrzedzenia scipy dla LimeExplainer
    + Dodano `batch_size` do programu wyjaśniającego, gdy `include_local=False`do przesyłania strumieniowego globalnych wyjaśnień w partiach w celu poprawienia czasu wykonywania DecisionTreeExplainableModel
  + **Azure-contrib-featureengineering**
    + Poprawka do wywoływania set_featurizer_timeseries_params (): DICT zmiana typu wartości i sprawdzanie wartości null — Dodawanie notesu dla `timeseries` featurized
    + Zaktualizuj zależność NimbusML do wersji 1.2.0 (aktualna Najnowsza wersja).
  + **azureml-core**
    + Dodano możliwość dołączania magazynów danych DBFS w interfejsie wiersza polecenia Azure
    + Rozwiązano problem z przekazaniem magazynu danych, w którym tworzony jest pusty folder, jeśli `target_path` rozpoczyna się z `/`
    + Rozwiązano problem `deepcopy` w ServicePrincipalAuthentication.
    + Do interfejsu wiersza polecenia dodano poleceń "AZ ml Environment show" i "AZ ml Environment list".
    + Środowiska obsługują teraz Określanie base_dockerfile jako alternatywy dla już skompilowanego base_image.
    + Nieużywane ustawienie RunConfiguration auto_prepare_environment zostało oznaczone jako przestarzałe.
    + Opis modelu można teraz zaktualizować po rejestracji
    + Poprawka: model i usuwanie obrazu teraz zawiera więcej informacji na temat pobierania obiektów nadrzędnych, które są zależne od nich, jeśli usuwanie nie powiedzie się z powodu zależności między strumieniami.
    + Rozwiązano problem polegający na tym, że wydrukowany pusty czas trwania wdrożeń występujących podczas tworzenia obszaru roboczego dla niektórych środowisk.
    + Ulepszone błędy tworzenia obszaru roboczego. Użytkownicy nie zobaczą "nie można utworzyć obszaru roboczego. Nie można znaleźć... " jako komunikat i zamiast tego pojawia się rzeczywisty błąd tworzenia.
    + Dodano obsługę uwierzytelniania tokenów w AKS WebServices.
    + Dodaj metodę `get_token()` do `Webservice` obiektów.
    + Dodano obsługę interfejsu wiersza polecenia do zarządzania zestawami danych uczenia maszynowego.
    + `Datastore.register_azure_blob_container` teraz opcjonalnie przyjmuje wartość `blob_cache_timeout` (w sekundach), która umożliwia skonfigurowanie parametrów instalacji blobfuse w celu włączenia wygasania pamięci podręcznej dla tego magazynu danych. Wartość domyślna nie jest przekroczeniem limitu czasu, czyli gdy obiekt BLOB jest odczytywany, pozostanie w lokalnej pamięci podręcznej do momentu zakończenia zadania. Większość zadań preferuje to ustawienie, ale niektóre zadania muszą odczytywać więcej danych z dużego zestawu danych, niż mieści się na ich węzłach. W przypadku tych zadań dostrajanie tego parametru pomoże się pomyślnie. Weź pod uwagę, gdy dostrajasz ten parametr: ustawienie zbyt małej wartości może spowodować spadek wydajności, ponieważ dane używane w epokach mogły wygasnąć przed ponownym użyciem. Oznacza to, że wszystkie operacje odczytu będą wykonywane z magazynu obiektów BLOB (tj. sieci), a nie lokalnej pamięci podręcznej, co negatywnie wpływa na czasy uczenia się.
    + Opis modelu można teraz prawidłowo zaktualizować po rejestracji
    + Usuwanie modelu i obrazu zapewnia teraz więcej informacji na temat obiektów nadrzędnych, które są od nich zależne, co powoduje niepowodzenie usuwania
    + Zwiększ wykorzystanie zasobów przez zdalne uruchomienia za pomocą usługi Azure. mlflow.
  + **azureml-explain-model**
    + Stałe przekształcenia — argument dla objaśniania WAPNa dla wagi nieprzetworzonej funkcji w usłudze Azure contrib-Wyjaśnij pakiet modelu
    + Dodaj obsługę rozrzedzenia scipy dla LimeExplainer
    + dodano otokę objaśnienia liniowego kształtu, a także inny poziom do tabelarycznego objaśnienia w celu wyjaśnienia modeli liniowych
    + Aby uzyskać wyjaśnienie śladu w bibliotece modeli wyjaśnień, Naprawiono błąd podczas include_local = false dla rozrzedzonych danych wejściowych
    + Dodaj oczekiwane wartości do `automl` danych wyjściowych
    + stała ważność funkcji permutacji podczas dostarczania argumentu Transformations w celu uzyskania ważności funkcji surowej
    + dodano `batch_size` do programu wyjaśniającego, gdy `include_local=False`do przesyłania strumieniowego globalnych wyjaśnień w partiach w celu poprawienia czasu wykonywania DecisionTreeExplainableModel
    + Aby zapoznać się z biblioteką wyjaśniającą model, należy naprawić blackboxe, gdzie dane wejściowe Pandas Dataframe są wymagane do przewidywania
    + Naprawiono usterkę, w której `explanation.expected_values` czasami zwracają wartość zmiennoprzecinkową, a nie listę z zmiennoprzecinkową.
  + **azureml-mlflow**
    + Poprawa wydajności mlflow. set_experiment (experiment_name)
    + Naprawianie usterki w korzystaniu z InteractiveLoginAuthentication dla mlflow tracking_uri
    + Zwiększ wykorzystanie zasobów przez zdalne uruchomienia za pomocą usługi Azure. mlflow.
    + Ulepszanie dokumentacji pakietu Azure-mlflow
    + Błąd poprawki, gdzie mlflow. log_artifacts ("my_dir") zapisze artefakty w obszarze "my_dir/< artefaktów >" zamiast "< ścieżek artefaktów >"
  + **azureml-opendatasets**
    + Przypnij `pyarrow` `opendatasets` do starych wersji (< 0.14.0) z powodu problemu z pamięcią nowo wprowadzonego w tym miejscu.
    + Przenieś usługę Azure-contrib-opendatasets do programu Azure opendatasets.
    + Zezwalaj na rejestrację otwartych klas DataSet w obszarze roboczym Azure Machine Learning i bezproblemowo korzystaj z możliwości zestawu danych AML.
    + Znacznie Zwiększ wydajność wzbogacania NoaaIsdWeather w wersji innej niż SPARK.
  + **azureml-pipeline-steps**
    + Magazyn danych DBFS jest teraz obsługiwany w przypadku wejść i wyjść w DatabricksStep.
    + Zaktualizowana dokumentacja Azure Batch kroku w odniesieniu do danych wejściowych/wyjściowych.
    + W AzureBatchStep, zmieniono wartość domyślną *delete_batch_job_after_finish* na *true*.
  + **Azure-Telemetria**
    +  Przenieś usługę Azure-contrib-opendatasets do programu Azure opendatasets.
    + Zezwalaj na rejestrację otwartych klas DataSet w obszarze roboczym Azure Machine Learning i bezproblemowo korzystaj z możliwości zestawu danych AML.
    + Znacznie Zwiększ wydajność wzbogacania NoaaIsdWeather w wersji innej niż SPARK.
  + **azureml-train-automl**
    + Zaktualizowana dokumentacja dotycząca get_output odzwierciedla faktyczny zwracany typ i udostępnia dodatkowe uwagi dotyczące pobierania właściwości klucza.
    + Zaktualizuj zależność NimbusML do wersji 1.2.0 (aktualna Najnowsza wersja).
    + Dodaj oczekiwane wartości do `automl` danych wyjściowych
  + **azureml-train-core**
    + Ciągi są teraz akceptowane jako element docelowy obliczeń dla zautomatyzowanego dostrajania parametrów
    + Nieużywane ustawienie RunConfiguration auto_prepare_environment zostało oznaczone jako przestarzałe.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning zestawu SDK 1.1.9 przygotowywania danych

+ **Nowe funkcje**
  + Dodano obsługę odczytywania pliku bezpośrednio z adresu URL http lub https.

+ **Poprawki i ulepszenia błędów**
  + Ulepszony komunikat o błędzie podczas próby odczytania zestawu danych Parquet ze źródła zdalnego (co nie jest obecnie obsługiwane).
  + Rozwiązano błąd podczas zapisu w formacie pliku Parquet w ADLS Gen 2 i Zaktualizowano nazwę kontenera ADLS generacji 2 w ścieżce.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Interfejs wizualizacji
+ **Funkcje w wersji zapoznawczej**
  + Dodano moduł "Wykonaj skrypt języka R" w interfejsie wizualizacji.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.48

+ **Nowe funkcje**
  + **azureml-opendatasets**
    + **Azure-contrib-opendatasets** jest teraz dostępny jako **Azure-opendatasets**. Stary pakiet może nadal korzystać z programu, ale zalecamy użycie usługi **Azure-opendatasets** do przodu w celu uzyskania bogatszych możliwości i ulepszeń.
    + Ten nowy pakiet umożliwia zarejestrowanie otwartych zestawów danych jako zestaw DataSet w obszarze roboczym Azure Machine Learning i wykorzystanie funkcji oferowanych przez zestaw danych.
    + Obejmuje ona również istniejące funkcje, takie jak zużywanie otwartych zestawów danych jako Pandas/SPARK dataframes, a także przyłączania do lokalizacji dla niektórych elementów DataSet, takich jak pogoda.

+ **Funkcje w wersji zapoznawczej**
    + HyperDriveConfig może teraz akceptować obiekt potoku jako parametr obsługujący dostrajanie parametrów przy użyciu potoku.

+ **Poprawki i ulepszenia błędów**
  + **azureml-train-automl**
    + Rozwiązano błąd dotyczący utraty typów kolumn po transformacji.
    + Rozwiązano błąd zezwalający y_query być typem obiektu zawierającym brak (s) na początku.
    + Rozwiązano problem związany z procedurą wyboru kompletną, która niepotrzebnie zwiększa wynikowe zestawy, nawet jeśli wyniki pozostawały stałe.
    + Rozwiązano problem z ustawieniami whitelist_models i blacklist_models w AutoMLStep.
    + Rozwiązano problem uniemożliwiający użycie przetwarzania wstępnego, gdy AutoML mógłby zostać użyty w kontekście potoków usługi Azure ML.
  + **azureml-opendatasets**
    + Przeniesiono usługę Azure-contrib-opendatasets do programu Azure opendatasets.
    + Dozwolone są otwarte klasy zestawu danych do zarejestrowania w obszarze roboczym Azure Machine Learning i bezproblemowo wykorzystują możliwości zestawu danych AML.
    + Znacznie Ulepszono NoaaIsdWeather wzbogacanie wydajności w wersji innej niż SPARK.
  + **azureml-explain-model**
    + Zaktualizowana dokumentacja online dla obiektów interpretera.
    + Dodano `batch_size` do programu wyjaśniającego, gdy `include_local=False`do przesyłania strumieniowego globalnych wyjaśnień w partiach w celu poprawienia czasu wykonywania DecisionTreeExplainableModel dla biblioteki modelowania.
    + Rozwiązano problem polegający na tym, że `explanation.expected_values` czasami zwracają wartość zmiennoprzecinkową, a nie listę z zmiennoprzecinkową.
    + Dodano oczekiwane wartości do `automl` danych wyjściowych dla objaśnień w bibliotece modeli wyjaśnień.
    + Stała ważność funkcji permutacji, gdy podano argument Transformations w celu uzyskania ważności funkcji RAW.
  + **azureml-core**
    + Dodano możliwość dołączania magazynów danych DBFS w interfejsie wiersza polecenia Azure.
    + Rozwiązano problem związany z przekazywaniem magazynu danych, w którym tworzony jest pusty folder, jeśli `target_path` rozpoczyna się z `/`.
    + Włączono porównanie dwóch zestawów danych.
    + Usuwanie modelu i obrazu teraz zawiera więcej informacji na temat pobierania obiektów nadrzędnych, które są zależne od nich, jeśli usuwanie nie powiedzie się z powodu zależności między strumieniami.
    + Przestarzałe ustawienie RunConfiguration nie jest używane w auto_prepare_environment.
  + **azureml-mlflow**
    + Ulepszone wykorzystanie zasobów przez zdalne uruchomienia korzystające z platformy Azure. mlflow.
    + Ulepszona Dokumentacja pakietu Azure-mlflow.
    + Rozwiązano problem polegający na tym, że mlflow. log_artifacts ("my_dir") zapisze artefakty w obszarze "my_dir/artifact-Paths" zamiast "ze ścieżki artefaktów".
  + **azureml-pipeline-core**
    + Parametr hash_paths dla wszystkich kroków potoku jest przestarzały i zostanie usunięty w przyszłości. Domyślnie zawartość source_directory jest skrótem (z wyjątkiem plików wymienionych w pliku. amlignore lub. gitignore)
    + Kontynuuj ulepszanie modułów i ModuleStep, aby obsługiwały specyficzne dla typów obliczeń moduły, aby przygotować się do integracji RunConfiguration i innych zmian w celu odblokowania użycia modułu specyficznego dla typu obliczeń w potokach.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Ulepszona dokumentacja w odniesieniu do danych wejściowych/wyjściowych.
    + AzureBatchStep: Zmieniono wartość domyślną delete_batch_job_after_finish na true.
  + **azureml-train-core**
    + Ciągi są teraz akceptowane jako element docelowy obliczeń dla zautomatyzowanego dostrajania parametrów.
    + Przestarzałe ustawienie RunConfiguration nie jest używane w auto_prepare_environment.
    + Przestarzałe parametry `conda_dependencies_file_path` i `pip_requirements_file_path` na korzyść odpowiednio `conda_dependencies_file` i `pip_requirements_file`.
  + **azureml-opendatasets**
    + Znacznie Zwiększ wydajność wzbogacania NoaaIsdWeather w wersji innej niż SPARK.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning zestawu SDK 1.1.8 przygotowywania danych

+ **Nowe funkcje**
 + Obiekty przepływu danych można teraz powtarzać, generując sekwencję rekordów. Zapoznaj się z dokumentacją dotyczącą `Dataflow.to_record_iterator`.
  + Obiekty przepływu danych można teraz powtarzać, generując sekwencję rekordów. Zapoznaj się z dokumentacją dotyczącą `Dataflow.to_record_iterator`.

+ **Poprawki i ulepszenia błędów**
 + Zwiększona niezawodność zestawu preprodukcyjny SDK.
 + Ulepszona obsługa Pandas dataframes z indeksami kolumn niebędącymi ciągami.
 + Ulepszona wydajność `to_pandas_dataframe` w zestawach danych.
 + Naprawiono usterkę polegającą na tym, że wykonanie zestawów danych w środowisku wielowęzłowym nie powiodło się.
  + Zwiększona niezawodność zestawu preprodukcyjny SDK.
  + Ulepszona obsługa Pandas dataframes z indeksami kolumn niebędącymi ciągami.
  + Ulepszona wydajność `to_pandas_dataframe` w zestawach danych.
  + Naprawiono usterkę polegającą na tym, że wykonanie zestawów danych w środowisku wielowęzłowym nie powiodło się.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning zestawu SDK 1.1.7 przygotowywania danych

Przywrócono zmianę, która zwiększyła wydajność, ponieważ powodowała to problemy dla niektórych klientów korzystających Azure Databricks. Jeśli wystąpił problem dotyczący Azure Databricks, można uaktualnić do wersji 1.1.7 przy użyciu jednej z poniższych metod:
1. Uruchom ten skrypt, aby uaktualnić: `%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Utwórz ponownie klaster, w którym zostanie zainstalowana najnowsza wersja zestawu SDK przygotowywania danych.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.45

+ **Nowe funkcje**
  + Dodaj zastępczy Model drzewa decyzyjnego do programu wyjaśniającego w pakiecie Azure-Wyjaśnij pakiet
  + Możliwość określenia wersji CUDA, która ma zostać zainstalowana na obrazach Inferencing. Obsługa CUDA 9,0, 9,1 i 10,0.
  + Informacje o obrazach podstawowych szkoleń dotyczących usługi Azure ML są teraz dostępne w [usłudze Azure ml Containers Repository](https://github.com/Azure/AzureML-Containers) i [DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + Dodano obsługę interfejsu wiersza polecenia dla harmonogramu potoku. Uruchom "AZ ml Pipeline-h", aby dowiedzieć się więcej
  + Dodano niestandardowy parametr przestrzeni nazw Kubernetes do AKS konfiguracji wdrożenia sieci Web i interfejsu wiersza polecenia.
  + Przestarzały parametr hash_paths dla wszystkich kroków potoku
  + Model. Register obsługuje teraz rejestrowanie wielu pojedynczych plików jako jednego modelu z użyciem parametru `child_paths`.

+ **Funkcje w wersji zapoznawczej**
    + Narzędzia do oceniania ocen mogą teraz opcjonalnie zapisywać informacje Conda i PIP w celu uzyskania bardziej niezawodnej serializacji i deserializacji.
    + Poprawka usterki dla selektora funkcji automatycznej.
    + Zaktualizowano mlflow. Azure. build_image do nowego interfejsu API, poprawki poprawek uwidocznione przez nową implementację.

+ **Poprawki i ulepszenia błędów**
  + Usunięto zależność `paramiko` od rdzenia platformy Azure. Dodano ostrzeżenia o zaniechaniu dla starszych metod dołączania obiektów docelowych obliczeń.
  + Zwiększ wydajność działania. create_children
  + W wyjaśnieniu z klasyfikatorem binarnym należy poprawić kolejność prawdopodobieństwa, gdy w celu skalowania wartości kształtów jest używane prawdopodobieństwo dla nauczycieli.
  + Ulepszona obsługa błędów i komunikat dotyczący zautomatyzowanej uczenia maszynowego.
  + Rozwiązano problem z limitem czasu iteracji dla automatycznego uczenia maszynowego.
  + Ulepszona wydajność transformacji szeregów czasowych na potrzeby automatycznego uczenia maszynowego.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning zestawu SDK 1.1.6 przygotowywania danych

+ **Nowe funkcje**
  + Dodano funkcje podsumowania dla pierwszych wartości (`SummaryFunction.TOPVALUES`) i dolnych wartości (`SummaryFunction.BOTTOMVALUES`).

+ **Poprawki i ulepszenia błędów**
  + Znacznie Ulepszono wydajność `read_pandas_dataframe`.
  + Naprawiono usterkę powodującą, że `get_profile()` na przepływu danych wskazujące na błędy plików binarnych.
  + Uwidocznione `set_diagnostics_collection()` umożliwiające programowe Włączanie/wyłączanie zbierania danych telemetrycznych.
  + Zmieniono zachowanie `get_profile()`. Wartości NaN są teraz ignorowane dla opcji min, średnia, std i sum, które są wyrównane z zachowaniem Pandas.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.43

+ **Nowe funkcje**
  + Azure Machine Learning teraz zapewnia obsługę pierwszej klasy dla popularnego środowiska uczenia maszynowego i analizy danych Scikit — uczenie się. Za pomocą [`SKLearn` szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)użytkownicy mogą łatwo uczeniować i wdrażać modele Scikit-uczyć.
    + Dowiedz się, jak [uruchomić dostrajanie parametrów za pomocą Scikit — Dowiedz się](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb), jak korzystać z usługi.
  + Dodano obsługę tworzenia ModuleStep w potokach oraz klasy modułów i ModuleVersion w celu zarządzania jednostkami obliczeniowymi wielokrotnego użytku.
  + Usługi WebServices ACI obsługują teraz trwałe scoring_uri za pomocą aktualizacji. Scoring_uri zmieni się z adresu IP na nazwę FQDN. Etykieta nazwy DNS dla nazwy FQDN można skonfigurować przez ustawienie dns_name_label w deploy_configuration.
  + Automatyczne Uczenie maszynowe nowe funkcje:
    + STL featurized na potrzeby prognozowania
    + KMeans klastrowanie jest włączone do czyszczenia funkcji
  + AmlCompute zatwierdzeń przydziałów stało się szybsze. Teraz zautomatyzowany proces zatwierdzania żądań limitu przydziału w ramach progu. Aby uzyskać więcej informacji o działaniu przydziałów, Dowiedz się, [jak zarządzać przydziałami](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas).

+ **Funkcje w wersji zapoznawczej**
    + Integracja z usługą [MLflow](https://mlflow.org) 1.0.0 Tracking za pomocą pakietu Azure-MLflow ([przykładowe notesy](https://aka.ms/azureml-mlflow-examples)).
    + Prześlij Notes Jupyter jako przebieg. [Dokumentacja interfejsu API](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Publiczna wersja zapoznawcza narzędzia do [wykrywania dryfów danych](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) za poorednictwem pakietu Azure-contrib-datadryfing ([przykładowe notesy](https://aka.ms/azureml-datadrift-example)). Dryfowanie danych jest jednym z najważniejszych powodów, w których dokładność modelu ulega obniżeniu w miarę upływu czasu. Zdarza się to, gdy dane dostarczane do modelu w środowisku produkcyjnym różnią się od danych, na których jest szkolony model. Narzędzie AML dryfowania danych ułatwia Klientowi monitorowanie dryfowania danych i wysyłanie alertów w przypadku wykrycia dryfu.

+ **Fundamentalne zmiany**

+ **Poprawki i ulepszenia błędów**
  + RunConfiguration Załaduj i Zapisz obsługuje określenie pełnej ścieżki pliku z pełnymi kopiami zapasowymi dla poprzedniego zachowania.
  + Dodano buforowanie w ServicePrincipalAuthentication, domyślnie wyłączone.
  + Włącz rejestrowanie wielu wykresów w tej samej nazwie metryki.
  + Klasa modelu jest teraz prawidłowo przewoźny z platformy Azure. Core (`from azureml.core import Model`).
  + W przypadku etapów potoku `hash_path` parametr jest obecnie przestarzały. Nowe zachowanie polega na zakończeniu mieszania source_directory, z wyjątkiem plików wymienionych w pliku. amlignore lub. gitignore.
  + W pakietach potoku różne metody `get_all` i `get_all_*` zostały zaniechane na rzecz odpowiednio `list` i `list_*`.
  + Azure. Core. get_run nie wymaga już importowania klas przed zwróceniem oryginalnego typu uruchomienia.
  + Rozwiązano problem polegający na tym, że niektóre wywołania aktualizacji usługi WebService nie powodowały aktualizacji.
  + Limit czasu oceniania w usłudze AKS WebService musi należeć do zakresu od 5 ms do 300000ms. Maksymalna dozwolona scoring_timeout_ms żądań oceniania była niedostępna od 1 do 5 minut.
  + Obiekty LocalWebservice mają teraz `scoring_uri` i `swagger_uri` właściwości.
  + Przeniesiono Tworzenie katalogów wyjściowych i przekazywanie katalogów danych wyjściowych z procesu użytkownika. Włączono zestaw SDK historii uruchamiania do uruchomienia w każdym procesie użytkownika. Powinno to rozwiązać pewne problemy z synchronizacją napotkane przez rozproszone przebiegi szkoleniowe.
  + Nazwa dziennika usługi Azure zapisywana na podstawie nazwy procesu użytkownika będzie teraz zawierać nazwę procesu (dotyczy tylko szkoleń rozproszonych) i identyfikatora PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning zestawu SDK 1.1.5 przygotowywania danych

+ **Poprawki i ulepszenia błędów**
  + W przypadku interpretowanych wartości datetime o 2-cyfrowym formacie roku Zakres prawidłowych lat został zaktualizowany tak, aby pasował do systemu Windows. Zakres został zmieniony z 1930-2029 na 1950-2049.
  + Podczas odczytywania pliku i ustawienia `handleQuotedLineBreaks=True`, `\r` będzie traktowany jako nowy wiersz.
  + Naprawiono usterkę, która spowodowała niepowodzenie w niektórych przypadkach `read_pandas_dataframe`.
  + Ulepszona wydajność `get_profile`.
  + Ulepszone komunikaty o błędach.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Zestaw SDK pakietu przygotowywania danych Azure Machine Learning

+ **Nowe funkcje**
  + Teraz można używać następujących funkcji języka wyrażeń do wyodrębniania i analizowania wartości DateTime w nowych kolumnach.
    + `RegEx.extract_record()` wyodrębnia elementów DateTime do nowej kolumny.
    + `create_datetime()` tworzy obiekty DateTime z oddzielnych elementów DateTime.
  + Podczas wywoływania `get_profile()`teraz można zobaczyć, że kolumny quantile są oznaczone jako (EST.), aby jasno wskazać, że wartości są przybliżami.
  + W przypadku odczytywania z usługi Azure Blob Storage można teraz używać * * obsługi symboli wieloznacznych.
    + np. `dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Poprawki błędów**
  + Naprawiono usterkę związaną z odczytywaniem pliku Parquet ze źródła zdalnego (obiekt blob platformy Azure).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.39
+ **Wprowadzane**
  + Opcja uruchamiania auto_prepare_environment konfiguracji jest przestarzała, a funkcja autoprzygotowywania stanie się wartością domyślną.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning zestawu SDK 1.1.3 przygotowywania danych

+ **Nowe funkcje**
  + Dodano obsługę odczytu z bazy danych PostgresSQL, wywołując read_postgresql lub korzystając ze sklepu datastore.
    + Zobacz przykłady w przewodnikach:
      + [Notes pozyskiwania danych](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Notes magazynu danych](https://aka.ms/aml-data-prep-datastore-nb)

+ **Poprawki i ulepszenia błędów**
  + Rozwiązano problemy z konwersją typu kolumny:
  + Teraz prawidłowo konwertuje wartość logiczną lub kolumnę liczbową na kolumnę logiczną.
  + Teraz nie kończy się niepowodzeniem podczas próby ustawienia kolumny daty na typ daty.
  + Ulepszono typy Jointype i towarzyszącą dokumentację referencyjną. Podczas sprzęgania dwóch przepływów elementów można teraz określić jeden z następujących typów sprzężenia:
    + NONE, MATCH, INNER, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULL.
  + Ulepszono inferencing typu danych, aby rozpoznawać więcej formatów daty.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Portal Azure

W Azure Portal można teraz:
+ Tworzenie i uruchamianie zautomatyzowanych eksperymentów ML
+ Utwórz maszynę wirtualną notesu, aby wypróbować przykładowe notesy Jupyter lub własne.
+ Zupełnie nowa sekcja tworzenia (wersja zapoznawcza) w obszarze roboczym Azure Machine Learning, która obejmuje automatyczne Machine Learning, interfejs wizualizacji i hostowane maszyny wirtualne notesy
    + Automatycznie twórz model przy użyciu automatycznego uczenia maszynowego
    + Używanie interfejsu wizualizacji przeciągnij i upuść do uruchamiania eksperymentów
    + Tworzenie maszyny wirtualnej notesu do eksplorowania danych, tworzenia modeli i wdrażania usług.
+ Aktualizowanie wykresu i metryk na żywo na stronach uruchamiania raportów i uruchamiania szczegółów
+ Zaktualizowano Podgląd plików dla dzienników, danych wyjściowych i migawek na stronach szczegółów uruchamiania.
+ Nowe i ulepszone środowisko tworzenia raportów na karcie eksperymenty.
+ Dodano możliwość pobrania pliku config. JSON z strony przegląd obszaru roboczego Azure Machine Learning.
+ Obsługa Azure Machine Learning tworzenia obszarów roboczych w obszarze roboczym Azure Databricks.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.33
+ **Nowe funkcje**
  + W _obszarze roboczym. Create_ Metoda akceptuje teraz domyślne konfiguracje klastrów dla klastrów procesora i GPU.
  + Jeśli Tworzenie obszaru roboczego nie powiedzie się, zasoby zależne są czyszczone.
  + Domyślna jednostka SKU Azure Container Registry została przełączona do warstwy Podstawowa.
  + Azure Container Registry jest tworzony opóźnieniem, gdy jest wymagany do tworzenia i uruchamiania obrazu.
  + Obsługa środowisk w przypadku przebiegów szkoleniowych.

### <a name="notebook-virtual-machine"></a>Maszyna wirtualna notesu 

Użyj maszyny wirtualnej z notesem jako bezpiecznego, gotowego do użycia w przedsiębiorstwie środowiska hostingu dla notesów Jupyter, w których możesz programować eksperymenty uczenia maszynowego, wdrożyć modele jako punkty końcowe sieci Web i wykonać wszystkie inne operacje obsługiwane przez zestaw SDK Azure Machine Learning przy użyciu języka Python. Oferuje kilka możliwości:
+ [Szybko Skonfiguruj wstępnie skonfigurowaną maszynę wirtualną](tutorial-1st-experiment-sdk-setup.md) , która ma najnowszą wersję zestawu SDK Azure Machine Learning i powiązane pakiety.
+ Dostęp jest zabezpieczony za pośrednictwem sprawdzonych technologii, takich jak HTTPS, Azure Active Directory uwierzytelniania i autoryzacji.
+ Niezawodny magazyn w chmurze dla notesów i kodu na koncie Obszar roboczy usługi Azure Machine Learning BLOB Storage. Możesz bezpiecznie usunąć maszynę wirtualną notesu bez utraty pracy.
+ Wstępnie zainstalowane przykładowe notesy umożliwiające eksplorowanie i eksperymentowanie z Azure Machine Learning funkcjami.
+ Pełne możliwości dostosowywania maszyn wirtualnych platformy Azure, wszystkie typy maszyn wirtualnych, wszystkie pakiety i sterowniki. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning zestawu SDK dla języka Python v 1.0.33.

+ Usługa Azure ML Modele z przyspieszaniem sprzętowym w systemie [FPGA](how-to-deploy-fpga-web-service.md) jest ogólnie dostępna.
  + Teraz można [używać pakietu Azure-akceleracja-models](how-to-deploy-fpga-web-service.md) do:
    + Uczenie wag obsługiwanej sieci głębokiej neuronowych (ResNet 50, ResNet 152, DenseNet-121, VGG-16 i SSD-VGG)
    + Korzystanie z uczenia transferowego z obsługiwanym DNN
    + Zarejestruj model przy użyciu usługi Zarządzanie modelami i konteneryzowanie model
    + Wdrażanie modelu na maszynie wirtualnej platformy Azure przy użyciu FPGA w klastrze usługi Azure Kubernetes Service (AKS)
  + Wdrażanie kontenera na urządzeniu z serwerem [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)
  + Poprowadź ocenę danych za pomocą punktu końcowego gRPC z tym [przykładem](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Zautomatyzowane uczenie maszynowe

+ Czyszczenie funkcji umożliwiające dynamiczne dodawanie :::no-loc text="featurizers"::: na potrzeby optymalizacji wydajności. Nowe :::no-loc text="featurizers":::: osadzanie pracy, Waga dowodów, kodowanie docelowe, kodowanie tekstu docelowego, odległość klastra
+ Inteligentna CV do obsługi pociągów i prawidłowych podziałów w ramach zautomatyzowanej ML
+ Niektóre zmiany optymalizacji pamięci i zwiększenie wydajności środowiska uruchomieniowego
+ Poprawa wydajności w wyjaśnieniu modelu
+ Konwersja modelu ONNX na potrzeby lokalnego uruchomienia
+ Dodano obsługę podpróbkowania
+ Inteligentne zatrzymywanie, gdy nie zdefiniowano kryteriów wyjścia
+ Skumulowane zestawy

+ Prognozowanie szeregów czasowych
  + Nowa funkcja prognozowania przewidywania
  + Teraz można korzystać z krzyżowego sprawdzania poprawności w danych szeregów czasowych
  + Dodano nowe funkcje w celu skonfigurowania spowolnienia szeregów czasowych
  + Dodano nowe funkcje do obsługi funkcji agregowania okna
  + Nowe wykrywanie świąt i featurized, gdy kod kraju jest zdefiniowany w ustawieniach eksperymentu

+ Azure Databricks
  + Włączono prognozowanie szeregów czasowych i explainabilty model/możliwości interpretacji
  + Możesz teraz anulować i wznowić (kontynuować) zautomatyzowane eksperymenty ML
  + Dodano obsługę przetwarzania wielordzeniowego

### <a name="mlops"></a>MLOps
+ **Debugowanie lokalnego & wdrożenia dla kontenerów oceniania**<br/> Teraz można wdrożyć model ML lokalnie i szybko wykonać iterację w pliku oceniania i zależnościach, aby upewnić się, że działają one zgodnie z oczekiwaniami.

+ **Wprowadzono InferenceConfig & model. deploy ()**<br/> Wdrożenie modelu obsługuje teraz Określanie folderu źródłowego ze skryptem wejścia, tak samo jak RunConfig.  Ponadto wdrożenie modelu zostało uproszczone do jednego polecenia.

+ **Śledzenie odwołań git**<br/> Klienci zażądali podstawowych możliwości integracji usługi git przez pewien czas, ponieważ ułatwiają one przechowywanie kompleksowego dziennika inspekcji. Zaimplementowano śledzenie dla głównych jednostek w usłudze Azure ML dla metadanych związanych z git (repozytorium, zatwierdzanie, czyszczenie stanu). Te informacje będą zbierane automatycznie przez zestaw SDK i interfejs wiersza polecenia.

+ **Profilowanie modelu & weryfikacja usługi**<br/> Klienci często omawiają trudności związane z prawidłowym rozmiarem obliczeń związanych z ich usługą wnioskowania. Dzięki naszej usłudze profilowania modelu klient może dostarczyć przykładowe dane wejściowe i profilować w 16 różnych konfiguracjach procesora/pamięci, aby określić optymalną zmianę rozmiarów wdrożenia.

+ **Przenoszenie własnego obrazu podstawowego do wnioskowania**<br/> Kolejną powszechną skargą była trudność w przeniesieniu się z eksperymentów w celu przeprowadzenia ponownego udostępniania. Dzięki naszej nowej funkcji udostępniania obrazów podstawowych można teraz ponownie wykorzystać podstawowe obrazy eksperymentów, zależności i wszystkie w przypadku wnioskowania. Powinno to przyspieszyć wdrożenia i zmniejszyć przerwy od wewnętrznego do pętli zewnętrznej.

+ **Ulepszone środowisko generowania schematu struktury Swagger**<br/> Nasza Poprzednia metoda generowania struktury Swagger była podatna na błędy i nie można jej zautomatyzować. Mamy nowy wbudowany sposób generowania schematów struktury Swagger z dowolnej funkcji języka Python za pośrednictwem dekoratory. Mamy ten kod, a nasz protokół generowania schematu nie jest połączony z platformą Azure ML.

+ **Azure ML CLI jest ogólnie dostępna (GA)**<br/> Modele można teraz wdrażać za pomocą jednego polecenia CLI. Mamy często zadawane Opinie klientów, że żaden z nich nie wdraża modelu ML z notesu Jupyter. [**Dokumentacja referencyjna interfejsu wiersza polecenia**](https://aka.ms/azmlcli) została zaktualizowana.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning zestawu SDK dla języka Python v 1.0.30.

[`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) nastąpiło dodanie nowej wersji opublikowanego potoku przy zachowaniu tego samego punktu końcowego.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning zestaw SDK przygotowania do przygotowywania danych

Uwaga: zestaw SDK języka Python dla przygotowywania danych nie będzie już instalował `numpy` i `pandas` pakietów. Zobacz [zaktualizowane instrukcje dotyczące instalacji](https://aka.ms/aml-data-prep-installation).

+ **Nowe funkcje**
  + Teraz można użyć przekształcenia Pivot.
    + Przewodnik: [Notebook Pivot](https://aka.ms/aml-data-prep-pivot-nb)
  + Teraz można używać wyrażeń regularnych w funkcjach natywnych.
    + Przykłady:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Teraz można używać  `to_upper`i `to_lower`funkcji  w języku wyrażeń.
  + Teraz można zobaczyć liczbę unikatowych wartości każdej kolumny w profilu danych.
  + W przypadku niektórych często używanych kroków czytelnika można teraz przekazać argument `infer_column_types`. Jeśli jest ustawiona na `True`, przygotowanie danych podejmie próbę wykrycia i automatycznego przekonwertowania typów kolumn.
    + `inference_arguments` jest obecnie przestarzałe.
  + Teraz można wywołać `Dataflow.shape`.

+ **Poprawki i ulepszenia błędów**
  + `keep_columns` teraz akceptuje dodatkowy opcjonalny argument `validate_column_exists`, który sprawdza, czy wynik `keep_columns` będzie zawierać wszystkie kolumny.
  + Wszystkie kroki czytnika (które odczytają plik) teraz akceptują dodatkowy opcjonalny argument `verify_exists`.
  + Ulepszona wydajność odczytywania z Pandas Dataframe i pobierania profilów danych.
  + Rozwiązano problem polegający na tym, że cięcie pojedynczego kroku z przepływu danych nie powiodło się z pojedynczym indeksem.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Portal Azure
  + Teraz można ponownie przesłać istniejący skrypt uruchomiony w istniejącym zdalnym klastrze obliczeniowym.
  + Teraz można uruchomić opublikowany potok z nowymi parametrami na karcie potoki.
  + Szczegóły uruchamiania obsługują teraz nową przeglądarkę plików migawek. Możesz wyświetlić migawkę katalogu podczas przesyłania określonego uruchomienia. Możesz również pobrać Notes, który został przesłany w celu uruchomienia uruchomienia.
  + Teraz można anulować uruchomienia nadrzędne z Azure Portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.23

+ **Nowe funkcje**
  + Zestaw SDK Azure Machine Learning obsługuje teraz środowisko Python 3,7.
  + Azure Machine Learning DNN szacowania teraz zapewnia wbudowaną obsługę wielowersji. Na przykład `TensorFlow` szacowania teraz akceptuje `framework_version` parametr, a użytkownicy mogą określić wersję "1,10" lub "1,12". Aby uzyskać listę wersji obsługiwanych przez bieżącą wersję zestawu SDK, wywołaj `get_supported_versions()` w żądanej klasie Framework (na przykład `TensorFlow.get_supported_versions()`).
  Listę wersji obsługiwanych przez najnowszą wersję zestawu SDK można znaleźć w [dokumentacji DNN szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning przygotowanie zestawu danych SDK v 1.1.1

+ **Nowe funkcje**
  + Możesz odczytywać wiele źródeł danych/źródła danych/DataReference, używając read_ * transformacje.
  + Aby utworzyć nową kolumnę, można wykonać następujące operacje: dzielenie, podłoga, modulo, moc, długość.
  + Przygotowanie danych jest teraz częścią pakietu diagnostyki usługi Azure ML i domyślnie będzie rejestrować informacje diagnostyczne.
    + Aby wyłączyć tę funkcję, ustaw dla tej zmiennej środowiskowej wartość true: DISABLE_DPREP_LOGGER

+ **Poprawki i ulepszenia błędów**
  + Ulepszona Dokumentacja kodu dla często używanych klas i funkcji.
  + Naprawiono usterkę w auto_read_file, która nie mogła odczytać plików programu Excel.
  + Dodano opcję zastępowania folderu w read_pandas_dataframe.
  + Ulepszona wydajność instalacji zależności dotnetcore2 oraz dodano obsługę Fedora 27/28 i Ubuntu 1804.
  + Ulepszono wydajność odczytywania z obiektów blob platformy Azure.
  + Wykrywanie typu kolumny obsługuje teraz kolumny typu Long.
  + Naprawiono usterkę, w której niektóre wartości dat były wyświetlane jako sygnatury czasowe zamiast obiektów DateTime języka Python.
  + Naprawiono usterkę, w której niektóre liczby typów były wyświetlane jako podwaja zamiast liczb całkowitych.


## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.21

+ **Nowe funkcje**
  + Metoda *Azure. Core. Run. create_children* umożliwia tworzenie wielu elementów podrzędnych w przypadku małych opóźnień z pojedynczym wywołaniem.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning zestawu SDK 1.1.0 przygotowywania danych

+ **Fundamentalne zmiany**
  + Koncepcja pakietu przygotowywania danych jest przestarzała i nie jest już obsługiwana. Zamiast utrwalać wiele przepływów elementów w jednym pakiecie, można utrwalać przepływy elementów pojedynczo.
    + Przewodnik: [otwieranie i zapisywanie notesu dataflows](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Nowe funkcje**
  + Przygotowanie danych może teraz rozpoznać kolumny, które pasują do określonego typu semantycznego i odpowiednio podzielić. Obecnie obsługiwane STypes obejmują: adres e-mail, współrzędne geograficzne (Szerokość geograficzna &), adresy IPv4 i IPv6, numer telefonu USA i kod pocztowy US.
    + Przewodnik: [typy semantyczne — Notes](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Przygotowanie danych obsługuje teraz następujące operacje, aby wygenerować kolumnę wynikową z dwóch kolumn liczbowych: Odejmij, pomnóż, Podziel i modulo.
  + Można wywołać `verify_has_data()` w przepływu danych, aby sprawdzić, czy przepływu danych będzie generować rekordy, jeśli są wykonywane.

+ **Poprawki i ulepszenia błędów**
  + Teraz możesz określić liczbę pojemników, które mają być używane w histogramie dla profilów kolumn liczbowych.
  + Przekształcanie `read_pandas_dataframe` teraz wymaga, aby ramka danych zawierała nazwy kolumn lub ciągów z typem bajtów.
  + Naprawiono usterkę w przekształceniu `fill_nulls`, gdzie wartości nie zostały prawidłowo wypełnione, jeśli brakuje kolumny.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.18

 + **Wprowadzane**
   + Pakiet Azure-tensorboard zastępuje usługę Azure-contrib-tensorboard.
   + W tej wersji można skonfigurować konto użytkownika w zarządzanym klastrze obliczeniowym (amlcompute) podczas jego tworzenia. Można to zrobić, przekazując te właściwości w konfiguracji aprowizacji. Więcej szczegółów można znaleźć w [dokumentacji referencyjnej zestawu SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning zestawu SDK 1.0.17 przygotowywania danych

+ **Nowe funkcje**
  + Program obsługuje teraz Dodawanie dwóch kolumn liczbowych w celu wygenerowania wynikowej kolumny przy użyciu języka wyrażeń.

+ **Poprawki i ulepszenia błędów**
  + Ulepszono sprawdzanie dokumentacji i parametrów dla random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning zestawu SDK 1.0.16 przygotowywania danych

+ **Poprawka błędu**
  + Rozwiązano problem z uwierzytelnianiem jednostki usługi, który został spowodowany przez zmianę interfejsu API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.17

+ **Nowe funkcje**

  + Azure Machine Learning teraz zapewnia obsługę pierwszej klasy dla popularnego łańcucha DNN Framework. Za pomocą [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) klasy użytkownicy mogą łatwo uczeni i wdrażać modele łańcucha.
    + Dowiedz się, jak [uruchomić szkolenie rozproszone z ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Dowiedz się, jak [uruchomić dostrajanie parametrów za pomocą łańcucha](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Potoki Azure Machine Learning dodane zdolność do wyzwalania uruchomienia potoku na podstawie modyfikacji magazynu danych. [Notes harmonogramu](https://aka.ms/pl-schedule) potoku został zaktualizowany w celu pokazania tej funkcji.

+ **Poprawki i ulepszenia błędów**
  + Dodaliśmy obsługę w potokach Azure Machine Learning, aby ustawić właściwość source_directory_data_store na żądany magazyn danych (np. Magazyn obiektów BLOB) w witrynie [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) , które są dostarczane do [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Zgodnie z domyślnymi krokami usługa Azure File Store jest używana jako zapasowy magazyn danych, co może powodować problemy z ograniczaniem wydajności w przypadku wykonywania dużej liczby kroków współbieżnie.

### <a name="azure-portal"></a>Portal Azure

+ **Nowe funkcje**
  + Nowe środowisko edytora tabeli przeciągnij i upuść dla raportów. Użytkownicy mogą przeciągać kolumnę od samego do obszaru tabeli, w którym zostanie wyświetlona wersja zapoznawcza tabeli. Kolumny można zmieniać.
  + Nowa przeglądarka plików dzienników
  + Linki do przebiegów eksperymentów, obliczenia, modele, obrazy i wdrożenia z poziomu karty działania

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning zestawu SDK 1.0.15 przygotowywania danych

+ **Nowe funkcje**
  + Przygotowanie danych obsługuje teraz zapisywanie strumieni plików z przepływu danych. Zapewnia również możliwość manipulowania nazwami strumieni plików w celu tworzenia nowych nazw plików.
    + Przewodnik: [Praca ze strumieniem plików — Notes](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Poprawki i ulepszenia błędów**
  + Ulepszona wydajność t-Digest w dużych zestawach danych.
  + Przygotowanie danych obsługuje teraz odczytywanie danych ze ścieżki datapath.
  + Jedno kodowanie gorące teraz działa w przypadku kolumn logicznych i liczbowych.
  + Inne poprawki błędów.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.15

+ **Nowe funkcje**
  + Azure Machine Learning dodane potoki: AzureBatchStep ([Notes](https://aka.ms/pl-azbatch)), HyperDriveStep (Notes) oraz funkcja planowania na podstawie czasu ([Notes](https://aka.ms/pl-schedule)).
  +  DataTranferStep zaktualizowane do pracy z usługą Azure SQL Server i usługą Azure Database for PostgreSQL ([Notes](https://aka.ms/pl-data-trans)).

+ **Wprowadzane**
  + Przestarzałe `PublishedPipeline.get_published_pipeline` na rzecz `PublishedPipeline.get`.
  + Przestarzałe `Schedule.get_schedule` na rzecz `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning zestawu SDK 1.0.12 przygotowywania danych

+ **Nowe funkcje**
  + Przygotowanie danych obsługuje teraz odczytywanie z usługi Azure SQL Database przy użyciu magazynu danych.

+ **Wprowadzane**
  + Ulepszona wydajność pamięci niektórych operacji na dużych danych.
  + `read_pandas_dataframe()` teraz wymagane jest określenie `temp_folder`.
  + Właściwość `name` na `ColumnProfile` była przestarzała — zamiast tego użyj `column_name`.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.10

+ **Zmiany**:
  + Zestaw Azure ML SDK nie ma już pakietów interfejsu wiersza polecenia platformy Azure jako zależności. W szczególnych przypadkach platformy Azure-CLI-Core i Azure-CLI-profile zostały usunięte z platformy Azure Core. Są to zmiany wpływające na użytkowników:
    + Jeśli wykonujesz polecenie "AZ login", a następnie korzystasz z zestawu Azure-SDK, zestaw SDK będzie wykonywał dziennik przeglądarki lub kodu urządzenia jeszcze raz. Nie będzie on używać żadnego stanu poświadczeń utworzonego przez użytkownika AZ login.
    + W przypadku uwierzytelniania interfejsu wiersza polecenia platformy Azure, na przykład przy użyciu polecenia "AZ login", należy użyć klasy _Azure. Core. Authentication. AzureCliAuthentication_ . W przypadku uwierzytelniania interfejsu wiersza polecenia platformy Azure do narzędzia _PIP Zainstaluj interfejs wiersza polecenia platformy Azure_ w środowisku języka Python, w którym zainstalowano zestaw Azure-SDK.
    + Jeśli wykonujesz polecenie "AZ login" przy użyciu nazwy głównej usługi dla automatyzacji, zalecamy użycie klasy " _Azure. Core. Authentication. ServicePrincipalAuthentication_ ", ponieważ usługa Azure SDK nie będzie używać stanu poświadczeń utworzonego przez interfejs wiersza polecenia platformy Azure.

+ **Poprawki błędów**: Ta wersja zawiera większość poprawek błędów pomocniczych

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning zestawu SDK 1.0.8 przygotowywania danych

+ **Poprawki błędów**
  + Ulepszona wydajność uzyskiwania profilów danych.
  + Rozwiązano drobne usterki związane z raportowaniem błędów.

### <a name="azure-portal-new-features"></a>Witryna Azure portal: nowe funkcje
+ Nowe środowisko przeciągania i upuszczania wykresów w raportach. Użytkownicy mogą przeciągać kolumnę lub atrybut z obszaru wykresu, w którym system automatycznie wybierze odpowiedni typ wykresu dla użytkownika na podstawie typu danych. Użytkownicy mogą zmienić typ wykresu na inne odpowiednie typy lub dodać dodatkowe atrybuty.

    Obsługiwane typy wykresów:
    - Wykres liniowy
    - Histogram
    - Skumulowany wykres słupkowy
    - Skrzynka Box
    - Punktowy
    - Wykres bąbelkowy
+ Portal będzie teraz dynamicznie generował raporty dotyczące eksperymentów. Gdy użytkownik przesyła do eksperymentu przebieg, raport zostanie automatycznie wygenerowany z zarejestrowanymi metrykami i wykresami, aby umożliwić porównanie różnych przebiegów.

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.8

+ **Poprawki błędów**: Ta wersja zawiera większość poprawek błędów pomocniczych

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning zestawu SDK 1.0.7 przygotowywania danych

+ **Nowe funkcje**
  + Udoskonalenia magazynu danych (udokumentowane w [przewodniku w usłudze Magazyn](https://aka.ms/aml-data-prep-datastore-nb)danych)
    + Dodano możliwość odczytu i zapisu do udziału plików platformy Azure oraz ADLS magazynów danych w celu skalowania w górę.
    + W przypadku korzystania z magazynów danych program przygotowywania w ramach programu obsługuje teraz używanie uwierzytelniania nazwy głównej usługi zamiast interakcyjnego uwierzytelniania.
    + Dodano obsługę adresów URL wasb i wasbs.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning zestawu SDK 1.0.6 przygotowywania danych

+ **Poprawki błędów**
  + Rozwiązano problem z odczytem z publicznego, czytelnego kontenera obiektów blob platformy Azure na platformie Spark

## <a name="2018-12-20"></a>2018-12-20

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.6
+ **Poprawki błędów**: Ta wersja zawiera większość poprawek błędów pomocniczych

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning zestawu SDK 1.0.4 przygotowywania danych

+ **Nowe funkcje**
  + Funkcja `to_bool` umożliwia teraz konwertowanie niezgodnych wartości na wartości błędów. Jest to nowe domyślne zachowanie niezgodności dla `to_bool` i `set_column_types`, podczas gdy poprzednie domyślne zachowanie miało na celu przekonwertowanie niezgodnych wartości na wartość false.
  + Podczas wywoływania `to_pandas_dataframe`jest dostępna nowa opcja interpretacji wartości null/brakująca w kolumnach liczbowych jako NaN.
  + Dodano możliwość sprawdzenia zwracanego typu niektórych wyrażeń w celu zapewnienia spójności typów i wczesnego powrotu po awarii.
  + Teraz możesz wywoływać `parse_json`, aby analizować wartości w kolumnie jako obiekty JSON i rozszerzać je do wielu kolumn.

+ **Poprawki błędów**
  + Naprawiono usterkę, która uległa awarii `set_column_types` w języku Python 3.5.2.
  + Naprawiono usterkę, która uległa awarii podczas nawiązywania połączenia z magazynem danych przy użyciu obrazu AML.

+ **Aktualizacje**
  * [Przykładowe notesy](https://aka.ms/aml-data-prep-notebooks) dla samouczków rozpoczynających pracę, analizy przypadków i przewodniki.

## <a name="2018-12-04-general-availability"></a>2018--12 04: ogólna dostępność

Azure Machine Learning jest teraz ogólnie dostępna.

### <a name="azure-machine-learning-compute"></a>Usługi Azure Machine Learning obliczeń
W tej wersji ogłaszamy nowe zarządzane środowisko obliczeniowe za pomocą [obliczeń Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute). Ten obiekt docelowy obliczeń zastępuje Azure Batch AI obliczeń dla Azure Machine Learning.

Ten obiekt docelowy obliczeń:
+ Służy do szkolenia modelu i wnioskowania partii/oceny
+ Jest obliczeniami jednowęzłowymi
+ Czy Zarządzanie klastrem i planowanie zadań dla użytkownika
+ Domyślnie Skalowanie automatyczne
+ Obsługa zasobów procesora CPU i procesora GPU
+ Umożliwia korzystanie z maszyn wirtualnych o niskim priorytecie w celu obniżenia kosztów

Usługa Azure obliczeniowego usługi Machine Learning można tworzyć w języku Python, za pomocą witryny Azure portal lub interfejsu wiersza polecenia. Należy utworzyć w regionie Twojego obszaru roboczego i nie można dołączyć z innym obszarem roboczym. Ten obiekt docelowy obliczeń używa kontenera Docker dla przebiegu i pakuje zależności, aby replikować to samo środowisko we wszystkich węzłach.

> [!Warning]
> Zalecamy utworzenie nowego obszaru roboczego, aby użyć obliczeniowego usługi Azure Machine Learning. Brak zdalnego prawdopodobieństwo, że w trakcie tworzenia obliczeniowego usługi Azure Machine Learning z istniejącym obszarem roboczym użytkownicy będą widzieć błąd. Istniejących zasobów obliczeniowych, w obszarze roboczym będą nadal działać bez zmian.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v1.0.2
+ **Fundamentalne zmiany**
  + W tej wersji zostaną usunięte obsługę tworzenia maszyny Wirtualnej z usługi Azure Machine Learning. Nadal możesz dołączyć istniejącej chmury maszyny Wirtualnej lub zdalnej na serwerze lokalnym.
  + Obsługa BatchAI, które powinny być wspierane za pośrednictwem obliczeniowego usługi Azure Machine Learning teraz również zostaną usunięte.

+ **Nowy**
  + Dla potoków uczenia maszynowego:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **zaktualizowane**
  + Dla potoków uczenia maszynowego:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) teraz akceptuje runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) teraz kopiuje do i z źródła danych SQL
    + Planowanie funkcji zestawu SDK do tworzenia i aktualizowania harmonogramy uruchamiania potoków opublikowane

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Zestaw SDK v0.5.2 przeznaczonego do przygotowania danych usługi Azure Machine Learning
+ **Fundamentalne zmiany**
  * `SummaryFunction.N` Nazwa została zmieniona na `SummaryFunction.Count`.

+ **Poprawki błędów**
  * Użyj najnowszego tokenu uruchamiania AML podczas odczytywania i zapisywania do magazynów danych na zdalnych uruchomieniach. Wcześniej Jeśli tokenu Uruchom AML zostanie zaktualizowany w języku Python, przygotowywanie danych środowiska uruchomieniowego nie zostanie zaktualizowany przy użyciu zaktualizowanych AML Uruchom tokenu.
  * Dodatkowe przejrzyste komunikaty o błędach
  * to_spark_dataframe () nie będzie już ulegać awarii, gdy platforma Spark używa serializacji `Kryo`
  * Liczba wartości Inspector umożliwia teraz wyświetlanie więcej niż 1000 unikatowe wartości
  * Losowe podziału nie jest już nie powiedzie się jeśli oryginalny przepływu danych nie ma nazwy

+ **Więcej informacji**
  * [Zestaw SDK przygotowywania danych usługi Azure Machine Learning](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Dokumenty i notesów
+ Potokach uczenia Maszynowego
  + Nowe i zaktualizowane notesów dla klientów zaczynających się od potoki, określania zakresu usługi batch i styl transferu przykłady: https://aka.ms/aml-pipeline-notebooks
  + Dowiedz się, jak [Tworzenie pierwszego potoku](how-to-create-your-first-pipeline.md)
  + Dowiedz się, jak [Uruchom prognozy usługi batch przy użyciu potoków](how-to-use-parallel-run-step.md)
+ Azure Machine Learning cel obliczeń
  + [Przykładowe notesy](https://aka.ms/aml-notebooks) zostały zaktualizowane w celu korzystania z nowych zarządzanych obliczeń.
  + [Dowiedz się więcej o tym obliczeń](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Witryna Azure portal: nowe funkcje
+ Tworzenie i zarządzanie nimi [obliczeniowego usługi Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) typów w portalu.
+ Monitoruj użycie przydziału i [zażądać przydziału](how-to-manage-quotas.md) Azure obliczeniowego usługi Machine Learning.
+ Wyświetl Azure Machine Learning stanu klastra obliczeniowego w czasie rzeczywistym.
+ Tworzenie obliczeniowego usługi Azure Machine Learning i usługi Azure Kubernetes Service została dodana obsługa sieci wirtualnej.
+ Uruchom ponownie opublikowane potoki z istniejącymi parametrami.
+ Nowe [zautomatyzowane machine learning wykresy](how-to-understand-automated-ml.md) modeli klasyfikacji (przyrostu zyski, odwzorowania, funkcja znaczenie na wykresie słupkowym: explainability modelu) i modele regresji (reszty i funkcja znaczenie wykresu za pomocą modelu explainability).
+ Potoki mogą być wyświetlane w witrynie Azure portal




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v0.1.80

+ **Fundamentalne zmiany**
  * *azureml.Train.widgets* przestrzeń nazw została przeniesiona do *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* następujące klasy — na podstawie której zaniechane *azureml.core.compute.BatchAICompute* i *azureml.core.compute.DSVMCompute*. Ostatnie klasy zostaną usunięte w następnych wersjach. Klasa AmlCompute ma teraz łatwiej definicję po prostu potrzebuje vm_size i max_nodes i automatycznie skaluje klaster z zakresu od 0 do max_nodes po przesłaniu zadania. Nasze [przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) zostały zaktualizowane o te informacje i powinny być podane przykłady użycia. Mamy nadzieję, że możesz np. to uproszczenie i wiele więcej fascynujących funkcji są dostępne w nowszej wersji!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Zestaw SDK v0.5.1 przeznaczonego do przygotowania danych usługi Azure Machine Learning

Więcej informacji na temat zestawu SDK usługi Data Prep, czytając [odwołania docs](https://aka.ms/data-prep-sdk).
+ **Nowe funkcje**
   * Utworzony nowy interfejs wiersza polecenia przygotowania danych do wykonywania pakietów przygotowania danych i wyświetlić profil danych dla zestawu danych i przepływu danych
   * Przeprojektowana SetColumnType interfejsu API w celu zwiększenia użyteczności
   * Zmieniono nazwę smart_read_file do auto_read_file
   * Udostępnia teraz przesunięcia czasowego i kurtoza profilu danych
   * Próbkowanie z włączonym próbkowaniem stratyfikowana
   * Może odczytywać dane z plików zip, które zawierają pliki CSV
   * Może dzielić zestawy danych z losowym podziałem (na przykład do zestawów testów testowych)
   * Można uzyskać wszystkie typy danych kolumn z przepływu danych lub profilu danych przez wywołanie `.dtypes`
   * Może pobrać liczbę wierszy z przepływu danych lub profilu danych przez wywołanie `.row_count`

+ **Poprawki błędów**
   * Stałą Długa podwójna konwersja
   * Naprawiono assert po dowolnej Dodaj kolumnę
   * Rozwiązano problem z FuzzyGrouping, gdzie nie może nie wykryć grup w niektórych przypadkach
   * Funkcja stałej sortowania przestrzegają kolejność sortowania wielokolumnowych
   * Stałe i/lub wyrażenia, które mają być podobne do sposobu, w jaki `pandas` je obsłużyć
   * Naprawiono odczytu ze ścieżki dbfs
   * Wprowadzone bardziej zrozumiałe komunikaty o błędach
   * Teraz nie kończy się już w przypadku odczytywania na zdalnym miejscu docelowym obliczeń przy użyciu tokenu AML
   * Teraz już nie kończy się niepowodzeniem na DSVM systemu Linux
   * Teraz już ulega awarii podczas wartości innych niż ciąg znajdują się w ciągu predykatów
   * Teraz obsługuje błędy potwierdzenia, gdy przepływ danych powinna zakończyć się niepowodzeniem poprawnie
   * Obsługuje teraz lokalizacje przechowywania dbutils zainstalowanego w usłudze Azure Databricks

## <a name="2018-11-05"></a>05-11-2018 r.

### <a name="azure-portal"></a>Portal Azure
Azure Portal dla Azure Machine Learning mają następujące aktualizacje:
  * Nowy **potoki** kartę opublikowanych potoków.
  * Dodano obsługę dołączania do istniejącego klastra HDInsight, jako cel obliczenia.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v0.1.74

+ **Fundamentalne zmiany**
  * \* Workspace. compute_targets, magazyny danych, eksperymenty, obrazy, modele i *usługi WebService* są właściwościami, a nie metodami. Na przykład Zastąp ciąg *Workspace.compute_targets()* z *Workspace.compute_targets*.
  * *Run.get_context* traktuje jako przestarzałą *Run.get_submitted_run*. Druga metoda zostaną usunięte w następnych wersjach.
  * *PipelineData* klasy teraz oczekuje obiektu magazynu danych jako parametr zamiast datastore_name. Podobnie *potoku* akceptuje default_datastore zamiast default_datastore_name.

+ **Nowe funkcje**
  * Potoków uczenia maszynowego Azure [przykładowy notes](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) używa teraz kroki MPI.
  * Element widget RunDetails dla notesów programu Jupyter jest aktualizowana w celu wyświetlenia wizualizacja potoku.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Zestaw SDK v0.4.0 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Nowe funkcje**
  * Liczba typów dodawane do profilu danych
  * Liczba wartości i histogramu jest teraz dostępna
  * Więcej percentyle w profilu danych
  * Mediana jest dostępna w podsumowanie
  * Python 3.7 jest teraz obsługiwane.
  * Po zapisaniu przepływu danych, który zawiera magazynów danych na pakiet przygotowania danych jako część pakietu przygotowania danych zostaną utrwalone dane magazynu danych
  * Zapisywanie do magazynu danych jest teraz obsługiwane.

+ **Naprawiono usterkę**
  * 64-bitowe przepełnienia liczby całkowitej bez znaku są teraz obsługiwane prawidłowo w systemie Linux
  * Stały tekst Nieprawidłowa etykieta plikami ze zwykłym tekstem w smart_read
  * Typ kolumny ciągu teraz zostaną wyświetlone w widoku metryk
  * Liczba typów jest rozwiązano problem, aby pokazać ValueKinds mapowany do jednego typu pola, a nie pojedyncze pliki
  * Write_to_csv już nie kończy się niepowodzeniem podczas ścieżka jest podana jako ciąg
  * Korzystając z Zastąp, pozostawiając "wyszukiwanie" puste nie są już nie powiedzie się

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v0.1.68

+ **Nowe funkcje**
  * Obsługa wielu dzierżawców podczas tworzenia nowego obszaru roboczego.

+ **Usterki usunięte**
  * Nie trzeba już przypinać wersji biblioteki pynacl podczas wdrażania usługi sieci Web.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Zestaw SDK v0.3.0 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Nowe funkcje**
  * Dodano transform_partition_with_file(script_path) metody, która umożliwia użytkownikom na przekazywanie, ścieżkę pliku Python do wykonywania

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v0.1.65
[Wersja 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) zawiera nowe funkcje, więcej dokumentacji, poprawki i [przykładowy notesów](https://aka.ms/aml-notebooks).

Zobacz [listę znanych problemów](resource-known-issues.md) informacje na temat znanych błędów i rozwiązania problemu.

+ **Fundamentalne zmiany**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets Workspace.images Workspace.web_services słownika zwrotu, poprzednio zwróconej liście. Zobacz [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) dokumentacji interfejsu API.

  * Automatyczne usługi Machine Learning usunąć błąd znormalizowane średniej kwadratowej z podstawowe metryki.

+ **HyperDrive**
  * Poprawki błędów różnych HyperDrive Bayesowskie, ulepszenia wydajności dotyczące pobrania wywołania metryki.
  * Uaktualnienie Tensorflow 1.10 1.9
  * Optymalizacja obrazów platformy docker do zimnego.
  * Zadania będą teraz zgłaszać prawidłowy stan nawet wtedy, gdy zakończyły się kodem błędu innym niż 0.
  * Sprawdzanie poprawności atrybutów RunConfig w zestawie SDK.
  * Obiekt HyperDrive Uruchom obsługuje anulowanie podobne do regularnego wykonywania: nie trzeba przekazywać żadnych parametrów.
  * Widżet ulepszenia dotyczące utrzymania stan z listy rozwijanej wartości dla rozproszonej przebiegów oraz uruchomień HyperDrive.
  * Narzędzia TensorBoard i innych dziennik, który obsługuje pliki Naprawiono parametr serwera.
  * Obsługa MPI Intel(R) po stronie usługi.
  * Poprawka do szczegółowego dostosowywania parametrów dla rozproszonego wykonywania awarii podczas sprawdzania poprawności w BatchAI.
  * Kontekst Manager identyfikuje teraz podstawowe wystąpienie.

+ **Środowisku witryny Azure portal**
  * log_table() i log_row() są obsługiwane w szczegóły przebiegu.
  * Automatycznie twórz wykresy dla tabel i wierszy z 1, 2 lub 3 kolumnami numerycznymi i opcjonalną kolumną kategorii.

+ **Automatyczne Machine Learning**
  * Ulepszona obsługa błędów i dokumentacji
  * Naprawiono pobieranie właściwości uruchamiania problemy z wydajnością.
  * Naprawiono kontynuować wykonywania problem.
  * Rozwiązano problemy z iteracją :::no-loc text="ensembling":::.
  * Naprawiono szkolenia zwisa błędów w systemie MAC OS.
  * Próbkowanie — makro średni żądania Ściągnięcia/WIELOKLASOWA krzywa w scenariuszu niestandardowego sprawdzania poprawności.
  * Usunięty indeks dodatkowej logiki.
  * Usunąć filtr z get_output interfejsu API.

+ **Potoki**
  * Dodaje metody Pipeline.publish() publikowanie potoku bezpośrednio, bez konieczności wykonywania są uruchamiane jako pierwsze.
  * Dodano metodę PipelineRun. get_pipeline_runs () w celu pobrania uruchomień potoków, które zostały wygenerowane z opublikowanego potoku.

+ **Project Brainwave**
  * Zaktualizowano obsługę na układów FPGA nowe modele sztucznej Inteligencji.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Zestaw SDK v0.2.0 przeznaczonego do przygotowania danych usługi Azure Machine Learning
[Wersja 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) zawiera następujące funkcje i poprawki błędów:

+ **Nowe funkcje**
  * Obsługa hot jeden kodowania
  * Obsługa przekształcania kwantyl

+ **Usterka naprawiona:**
  * Działa z dowolną wersją Tornado nie ma potrzeby obniżyć wersji Tornado
  * Liczba wartości dla wszystkich wartości nie tylko trzy pierwsze

## <a name="2018-09-public-preview-refresh"></a>2018-09 (w wyniku odświeżenia publiczna wersja zapoznawcza)

Nowe, odświeżone wydanie Azure Machine Learning: Przeczytaj więcej na temat tej wersji: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z omówieniem usługi [Azure Machine Learning](overview-what-is-azure-ml.md).
