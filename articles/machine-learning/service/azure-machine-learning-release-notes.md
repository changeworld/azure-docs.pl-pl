---
title: Co nowego w wersji?
titleSuffix: Azure Machine Learning service
description: Dowiedz się więcej o najnowszych aktualizacjach usługi Azure Machine Learning i uczenia maszynowego i zestawy SDK Python przeznaczonego do przygotowania danych.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
author: hning86
ms.author: haining
ms.reviewer: j-martens
ms.date: 12/20/2018
ms.custom: seodec18
ms.openlocfilehash: 8a67a20beff306cd23b08e1d651ab5dc4c779fd2
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742766"
---
# <a name="azure-machine-learning-service-release-notes"></a>Informacje o wersji usługi Azure Machine Learning

Ten artykuł zawiera informacje o wersji usługi Azure Machine Learning. 

## <a name="2018-12-20"></a>2018-12-20: 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v1.0.6

+ **Dokumentacja zestawu SDK**: https://aka.ms/aml-sdk

+ **Poprawki błędów**: Ta wersja zawiera przede wszystkim poprawki drobnych błędów

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Zestaw SDK v1.0.4 przeznaczonego do przygotowania danych usługi Azure Machine Learning

+ **Dokumentacja zestawu SDK**: https://aka.ms/data-prep-sdk

+ **Nowe funkcje**
  + `to_bool` Teraz dzięki funkcji niezgodne wartości są konwertowane na wartości błędów. Jest to nowe zachowanie niezgodność dla `to_bool` i `set_column_types`, podczas gdy poprzednie zachowanie domyślne można przekonwertować niezgodne wartości FALSE.
  + Podczas wywoływania `to_pandas_dataframe`, istnieje nowa opcja do interpretacji null/Brak wartości w kolumnach liczbowych jako NaN.
  + Dodano możliwość sprawdzenia, zwracany typ niektóre wyrażenia w celu zapewnienia spójności typu i wczesna Porażka.
  + Teraz można wywołać `parse_json` przeanalizować wartości w kolumnie jako obiekty JSON i rozszerzać je na wiele kolumn.

+ **Poprawki błędów**
  + Naprawiono usterkę, która uległa awarii `set_column_types` w języku Python 3.5.2.
  + Naprawiono usterkę, która uległa awarii podczas nawiązywania połączenia z magazynem danych przy użyciu obrazu AML.

+ **Aktualizacje**
  * [Przykład notesów](https://aka.ms/aml-data-prep-notebooks) w celu uzyskania pracy — samouczki, analiz przypadków i przewodniki z instrukcjami.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Ogólna dostępność

Usługa Azure Machine Learning jest teraz ogólnie dostępna.

### <a name="azure-machine-learning-compute"></a>Usługi Azure Machine Learning obliczeń
W tej wersji, Pragniemy zaprezentować nowe środowisko obliczeniowe zarządzane za pośrednictwem [obliczeniowego usługi Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute). Ten cel obliczenia zastępuje usługi Azure Batch AI zasoby obliczeniowe na potrzeby usługi Azure Machine Learning. 

Ten element docelowy obliczeniowych:
+ Służy do modelu wnioskowania szkolenie i usługi batch
+ Jest jednym — do wielu - node obliczeń
+ Obsługuje zarządzanie klastrowania i planowania zadań dla użytkownika
+ Skalowania domyślnie
+ Obsługa zasobów procesora CPU i procesora GPU 
+ Umożliwia korzystanie z maszyn wirtualnych o niskim priorytecie w obniżonej cenie

Usługa Azure obliczeniowego usługi Machine Learning można tworzyć w języku Python, za pomocą witryny Azure portal lub interfejsu wiersza polecenia. Należy utworzyć w regionie Twojego obszaru roboczego i nie można dołączyć z innym obszarem roboczym. Ten cel obliczenia używa kontenera platformy Docker na potrzeby przebieg, a pakiety zależności replikowanie tym samym środowisku na wszystkie węzły.

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
  * Użyj najnowszej AML Uruchom tokenu podczas odczytywanie z oraz zapisywanie do magazynów danych na zdalnych przebiegów. Wcześniej Jeśli tokenu Uruchom AML zostanie zaktualizowany w języku Python, przygotowywanie danych środowiska uruchomieniowego nie zostanie zaktualizowany przy użyciu zaktualizowanych AML Uruchom tokenu.
  * Dodatkowe przejrzyste komunikaty o błędach
  * to_spark_dataframe() już ulegnie awarii, jeśli korzysta z platformy Spark `Kryo` serializacji
  * Liczba wartości Inspector umożliwia teraz wyświetlanie więcej niż 1000 unikatowe wartości
  * Losowe podziału nie jest już nie powiedzie się jeśli oryginalny przepływu danych nie ma nazwy  

+ **Więcej informacji**
  * [Zestaw SDK przygotowywania danych usługi Azure Machine Learning](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Dokumenty i notesów
+ Potokach uczenia Maszynowego
  + Nowe i zaktualizowane notesów dla klientów zaczynających się od potoki, określania zakresu usługi batch i styl transferu przykłady: https://aka.ms/aml-pipeline-notebooks
  + Dowiedz się, jak [Tworzenie pierwszego potoku](how-to-create-your-first-pipeline.md)
  + Dowiedz się, jak [Uruchom prognozy usługi batch przy użyciu potoków](how-to-run-batch-predictions.md)
+ Cel obliczenia w usłudze Azure Machine Learning
  + [Przykładowe notesów] (https://aka.ms/aml-notebooks) są teraz aktualizowane do użycia nowe obliczenie zarządzanych.
  + [Dowiedz się więcej o tym obliczeń](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Witryna Azure portal: nowe funkcje
+ Tworzenie i zarządzanie nimi [obliczeniowego usługi Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) typów w portalu.
+ Monitoruj użycie przydziału i [zażądać przydziału](how-to-manage-quotas.md) Azure obliczeniowego usługi Machine Learning.
+ Wyświetl stan klastra obliczeniowego usługi Azure Machine Learning w czasie rzeczywistym.
+ Tworzenie obliczeniowego usługi Azure Machine Learning i usługi Azure Kubernetes Service została dodana obsługa sieci wirtualnej.
+ Należy ponownie uruchomić potoków opublikowanych przy użyciu istniejących parametrów.
+ Nowe [zautomatyzowane machine learning wykresy](how-to-track-experiments.md#auto) modeli klasyfikacji (przyrostu zyski, odwzorowania, funkcja znaczenie na wykresie słupkowym: explainability modelu) i modele regresji (reszty i funkcja znaczenie wykresu za pomocą modelu explainability). 
+ Potoki mogą być wyświetlane w witrynie Azure portal




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v0.1.80

+ **Fundamentalne zmiany** 
  * *azureml.Train.widgets* przestrzeń nazw została przeniesiona do *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* następujące klasy — na podstawie której zaniechane *azureml.core.compute.BatchAICompute* i *azureml.core.compute.DSVMCompute*. Ostatnie klasy zostaną usunięte w następnych wersjach. Klasa AmlCompute ma teraz łatwiej definicję po prostu potrzebuje vm_size i max_nodes i automatycznie skaluje klaster z zakresu od 0 do max_nodes po przesłaniu zadania. [Przykładowe notebooki] (https://github.com/Azure/MachineLearningNotebooks/tree/master/training) zostały zaktualizowane przy użyciu tych informacji i powinny zawierać przykłady użycia. Mamy nadzieję, że możesz np. to uproszczenie i wiele więcej fascynujących funkcji są dostępne w nowszej wersji!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Zestaw SDK v0.5.1 przeznaczonego do przygotowania danych usługi Azure Machine Learning 

Więcej informacji na temat zestawu SDK usługi Data Prep, czytając [odwołania docs](https://aka.ms/data-prep-sdk).
+ **Nowe funkcje**
   * Utworzony nowy interfejs wiersza polecenia przygotowania danych do wykonywania pakietów przygotowania danych i wyświetlić profil danych dla zestawu danych i przepływu danych
   * Przeprojektowana SetColumnType interfejsu API w celu zwiększenia użyteczności
   * Zmieniono nazwę smart_read_file do auto_read_file
   * Udostępnia teraz przesunięcia czasowego i kurtoza profilu danych
   * Próbkowanie z włączonym próbkowaniem stratyfikowana
   * Może odczytywać dane z plików zip, które zawierają pliki CSV
   * Można podzielić zestawów danych row-wise za pomocą losowych podziału (na przykład zestawy testów train)
   * Można uzyskać wszystkie kolumny typów danych z przepływu danych lub profil danych przez wywołanie metody `.dtypes`
   * Można pobrać liczbę wierszy z przepływu danych lub profil danych przez wywołanie metody `.row_count`

+ **Poprawki błędów**
   * Stałą Długa podwójna konwersja 
   * Naprawiono assert po dowolnej Dodaj kolumnę 
   * Rozwiązano problem z FuzzyGrouping, gdzie nie może nie wykryć grup w niektórych przypadkach
   * Funkcja stałej sortowania przestrzegają kolejność sortowania wielokolumnowych
   * Naprawiono i/lub wyrażenia są podobne do jak `pandas` je obsługuje
   * Naprawiono odczytu ze ścieżki dbfs
   * Wprowadzone bardziej zrozumiałe komunikaty o błędach 
   * Teraz już nie kończy się niepowodzeniem podczas odczytywania na zdalnym obliczeniowych elementów docelowych przy użyciu tokenu AML
   * Teraz już nie kończy się niepowodzeniem na DSVM systemu Linux
   * Teraz już ulega awarii podczas wartości innych niż ciąg znajdują się w ciągu predykatów
   * Teraz obsługuje błędy potwierdzenia, gdy przepływ danych powinna zakończyć się niepowodzeniem poprawnie
   * Obsługuje teraz lokalizacje przechowywania dbutils zainstalowanego w usłudze Azure Databricks

## <a name="2018-11-05"></a>05-11-2018 r.

### <a name="azure-portal"></a>Azure Portal 
Witryny Azure portal dla usługi Azure Machine Learning ma następujące aktualizacje:
  * Nowy **potoki** kartę opublikowanych potoków.
  * Dodano obsługę dołączania do istniejącego klastra HDInsight, jako cel obliczenia.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v0.1.74

+ **Fundamentalne zmiany** 
  * * Workspace.compute_targets, magazynów danych, eksperymentów, obrazy, modele i *usług sieci Web* właściwości zamiast metod. Na przykład Zastąp ciąg *Workspace.compute_targets()* z *Workspace.compute_targets*.
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
  * 64-bitowej nieoznaczonej liczby całkowitej przepełnienia są teraz obsługiwane poprawnie w systemie Linux
  * Stały tekst Nieprawidłowa etykieta plikami ze zwykłym tekstem w smart_read
  * Typ kolumny ciągu teraz zostaną wyświetlone w widoku metryk
  * Liczba typów jest rozwiązano problem, aby pokazać ValueKinds mapowany do jednego typu pola, a nie pojedyncze pliki
  * Write_to_csv już nie kończy się niepowodzeniem podczas ścieżka jest podana jako ciąg
  * Korzystając z Zastąp, pozostawiając "wyszukiwanie" puste nie są już nie powiedzie się 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Usługi Azure Machine Learning zestawu SDK dla języka Python v0.1.68

+ **Nowe funkcje**
  * Obsługa wielu dzierżaw, podczas tworzenia nowego obszaru roboczego.

+ **Usterki usunięte**
  * Nie trzeba przypiąć wersji biblioteki pynacl podczas wdrażania usługi sieci web.

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
  * Zadania teraz raport prawidłowy stan, nawet wtedy, gdy ich zakończyć pracę z kodem błędu niż 0. 
  * Sprawdzanie poprawności atrybutów RunConfig w zestawie SDK. 
  * Obiekt HyperDrive Uruchom obsługuje anulowanie podobne do regularnego wykonywania: nie trzeba przekazywać żadnych parametrów. 
  * Widżet ulepszenia dotyczące utrzymania stan z listy rozwijanej wartości dla rozproszonej przebiegów oraz uruchomień HyperDrive. 
  * Narzędzia TensorBoard i innych dziennik, który obsługuje pliki Naprawiono parametr serwera. 
  * Obsługa MPI Intel(R) po stronie usługi. 
  * Poprawka do szczegółowego dostosowywania parametrów dla rozproszonego wykonywania awarii podczas sprawdzania poprawności w BatchAI. 
  * Kontekst Manager identyfikuje teraz podstawowe wystąpienie. 

+ **Środowisku witryny Azure portal**
  * log_table() i log_row() są obsługiwane w szczegóły przebiegu. 
  * Automatycznie tworzyć wykresy, tabele i wiersze, 1, 2 lub 3 kolumny liczbowe i opcjonalna kolumna podzielonych na kategorie.

+ **Automatyczne Machine Learning**
  * Ulepszona obsługa błędów i dokumentacji 
  * Naprawiono pobieranie właściwości uruchamiania problemy z wydajnością. 
  * Naprawiono kontynuować wykonywania problem. 
  * Rozwiązano ensembling problemy iteracji.
  * Naprawiono szkolenia zwisa błędów w systemie MAC OS.
  * Próbkowanie — makro średni żądania Ściągnięcia/WIELOKLASOWA krzywa w scenariuszu niestandardowego sprawdzania poprawności.
  * Usunięty indeks dodatkowej logiki.
  * Usunąć filtr z get_output interfejsu API.

+ **Potoki**
  * Dodaje metody Pipeline.publish() publikowanie potoku bezpośrednio, bez konieczności wykonywania są uruchamiane jako pierwsze.   
  * Dodaje metody PipelineRun.get_pipeline_runs() można pobrać uruchomień potoków, które zostały wygenerowane z opublikowanych potoku.

+ **Project Brainwave**
  * Zaktualizowano obsługę na układów FPGA nowe modele sztucznej Inteligencji.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Zestaw SDK v0.2.0 przeznaczonego do przygotowania danych usługi Azure Machine Learning
[Wersja 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) obejmuje następujące funkcje i poprawki:

+ **Nowe funkcje**
  * Obsługa hot jeden kodowania
  * Obsługa przekształcania kwantyl
   
+ **Usterka naprawiona:**
  * Działa z dowolną wersją Tornado nie ma potrzeby obniżyć wersji Tornado
  * Liczba wartości dla wszystkich wartości nie tylko trzy pierwsze

## <a name="2018-09-public-preview-refresh"></a>2018-09 (w wyniku odświeżenia publiczna wersja zapoznawcza)

Nowy, odświeżana wersji usługi Azure Machine Learning: Dowiedz się więcej na temat tej wersji: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/

## <a name="older-notes-sept-2017---jun-2018"></a>Informacje o starszych: Września 2017 — cze 2018 r.
### <a name="2018-05-sprint-5"></a>2018-05 (sprint 5)

W tej wersji usługi Azure Machine Learning możesz wykonywać następujące czynności:
+ Cechowanie obrazów przy użyciu wersji wykonywanie kwantyzowanych 50 siecią ResNet szkolenie klasyfikatora na podstawie tych funkcji i [wdrażanie modelu na FPGA na platformie Azure](../service/how-to-deploy-fpga-web-service.md) dla wnioskowania bardzo niskimi opóźnieniami.

+ Szybkie tworzenie i wdrażanie bardzo dokładnych machine learning i modeli uczenia głębokiego przy użyciu [niestandardowego uczenia pakiety usługi Azure Machine](../desktop-workbench/reference-python-package-overview.md)

### <a name="2018-03-sprint-4"></a>2018-03 (sprint 4)
**Numer wersji**: 0.1.1801.24353 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([Znajdź swoją wersję](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Wiele następujące aktualizacje są wprowadzane jako bezpośredni wynik opinii. Postanowieniem!

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
    - Punkty wejścia używane mogą być zmieniane.
    - Filtry najwyższego poziomu, które mogą być dodawane lub zmodyfikować ![Dodaj filtry](media/azure-machine-learning-release-notes/add-filters.jpg)
    - Wykresy i statystyki mogą być dodane lub zmodyfikowane (i przeciągnij i upuść nieco inaczej rozmieszczone).
    ![Tworzenie nowych wykresów](media/azure-machine-learning-release-notes/configure-charts.png)

  - CRUD dla historii uruchamiania raportów
  - Przenieść wszystkie istniejące widoku listy historii uruchamiania plików konfiguracji do raportu po stronie serwera, który zachowuje się jak potoków w przebiegów z punktów wejścia wybrane.

- Karta środowiska
  - Możesz łatwo dodawać nowemu celowi obliczenia i uruchamiać pliki konfiguracji do projektu ![nowy cel obliczenia](media/azure-machine-learning-release-notes/add-new-environments.png)
  - Aktualizowanie plików konfiguracji przy użyciu prostego, oparte na formularzach środowiska użytkownika i zarządzanie nimi
  - Nowy przycisk do przygotowania środowiska do wykonania

- Ulepszenia wydajności do listy plików, na pasku bocznym

#### <a name="data-preparation"></a>Przygotowywanie danych 
- Usługa Azure Machine Learning Workbench umożliwia teraz mieć możliwość wyszukiwania przy użyciu znanych kolumny nazwy kolumny.


#### <a name="experimentation"></a>Eksperymentowanie
- Usługa Azure Machine Learning Workbench obsługuje teraz uruchamianie skryptów natywnie we własnym środowisku python lub pyspark. Dla tej funkcji użytkownik tworzy i zarządza własnym środowisku na zdalnej maszynie Wirtualnej i użyj usługi Azure Machine Learning Workbench, aby uruchomić te skrypty w tym miejscu docelowym. Zobacz [konfigurowania usługi eksperymentowanie w usłudze Azure Machine Learning](../desktop-workbench/experimentation-service-configuration.md) 

#### <a name="model-management"></a>Zarządzanie modelami
- Obsługa dostosowywania wdrożone kontenery: umożliwia dostosowanie obrazu kontenera, umożliwiając instalacji zewnętrznych bibliotekach przy użyciu polecenia apt-get, itp. Nie jest już ograniczona do bibliotek do zainstalowania narzędzia pip. Zobacz [dokumentacji](../desktop-workbench/model-management-custom-container.md) Aby uzyskać więcej informacji.
  - Użyj `--docker-file myDockerStepsFilename` flagę i nazwa pliku z manifestu obrazu i poleceń do tworzenia usługi.
  - Obraz podstawowy jest Ubuntu i nie może być modyfikowany.
  - Przykładowe polecenie: 
  
    ```shell
    $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
    ```

### <a name="2018-01-sprint-3"></a>2018-01 (sprint 3) 
**Numer wersji**: 0.1.1712.18263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([Znajdź swoją wersję](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Poniżej przedstawiono aktualizacji i ulepszeń, w tym sprincie. Większość tych aktualizacji są wprowadzane jako bezpośredni wynik opinii użytkowników. 

### <a name="2017-12-sprint-2"></a>2017-12 (sprint 2)
**Numer wersji**: 0.1.1711.15263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([Znajdź swoją wersję](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Ta wersja jest trzeci aktualizacji usługi Azure Machine Learning. Ta aktualizacja zawiera ulepszenia w aplikacji workbench, interfejsu wiersza polecenia (CLI) i usług zaplecza. Dziękujemy wysyłania radosną twarz i frowns. Wiele następujące aktualizacje są wprowadzane jako bezpośredni wynik opinii. 

### <a name="2017-11-sprint-1"></a>2017-11 (sprint 1) 
**Numer wersji**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([Znajdź swoją wersję](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

W tej wersji wprowadziliśmy ulepszenia dotyczące zabezpieczeń, stabilności i łatwość konserwacji w aplikacji workbench, interfejsu wiersza polecenia i warstwy usług zaplecza. 

### <a name="2017-10-sprint-0"></a>2017-10 (sprint 0) 
**Numer wersji**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([Znajdź swoją wersję](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Ta wersja jest pierwszą aktualizacją Azure Machine Learning Workbench, zgodnie z naszym początkowej publicznej wersji zapoznawczej na konferencji Microsoft Ignite 2017. Główne aktualizacje wprowadzono w tej wersji są niezawodności i stabilizacji poprawki. 

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z omówieniem usługi [Azure Machine Learning](../service/overview-what-is-azure-ml.md).
