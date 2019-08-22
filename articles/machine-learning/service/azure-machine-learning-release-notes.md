---
title: Co nowego w wersji?
titleSuffix: Azure Machine Learning service
description: Dowiedz się więcej na temat najnowszych aktualizacji usługi Azure Machine Learning Service oraz zestawów SDK języka Python dotyczących uczenia maszynowego i danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: e169d7734776f9319685d97f941b3fc11eec8298
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656246"
---
# <a name="azure-machine-learning-service-release-notes"></a>Informacje o wersji usługi Azure Machine Learning

Ten artykuł zawiera informacje o wersji usługi Azure Machine Learning.  Aby uzyskać pełną zawartość referencyjną SDK, odwiedź stronę referencyjną [**głównego zestawu sdk Azure Machine Learning dla języka Python**](https://aka.ms/aml-sdk) . 

Zobacz [listę znanych problemów](resource-known-issues.md) informacje na temat znanych błędów i rozwiązania problemu.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.57
+ **Nowe funkcje**
  + Włączone `TabularDataset` do użycia przez AutomatedML. Aby dowiedzieć się `TabularDataset`więcej na temat https://aka.ms/azureml/howto/createdatasets, odwiedź stronę.
  
+ **Poprawki i ulepszenia błędów**
  + **automl-Client-Core-nativeclient**
    + Naprawiono błąd, zgłoszony, gdy szkolenia i/lub etykiety sprawdzania poprawności (y i y_valid) są podane w formie ramki danych Pandas, ale nie jako tablicy numpy.
    + Zaktualizowany interfejs do utworzenia, `RawDataContext` aby wymagał tylko danych `AutoMLBaseSettings` i obiektu.
    +  Zezwól użytkownikom AutoML na porzucanie serii szkoleniowych, które nie są wystarczająco długie podczas prognozowania. -Zezwalaj użytkownikom AutoML na upuszczanie ziaren z zestawu testów, który nie istnieje w zestawie szkoleniowym podczas prognozowania.
  + **Azure — interfejs wiersza polecenia**
    + Teraz można zaktualizować certyfikat protokołu SSL dla punktu końcowego oceniania wdrożonego w klastrze AKS zarówno dla wygenerowanego przez firmę Microsoft, jak i dla certyfikatu klienta.
  + **Azure-automl-Core**
    + Rozwiązano problem w AutoML, w którym wiersze z brakującymi etykietami nie zostały prawidłowo usunięte.
    + Ulepszono rejestrowanie błędów w AutoML; Pełne komunikaty o błędach będą teraz zawsze zapisywane w pliku dziennika.
    + AutoML zaktualizował Przypinanie pakietu do dołączenia `azureml-defaults`, `azureml-dataprep` `azureml-explain-model`, i. AutoML nie będzie już ostrzegał o niezgodności pakietów (z wyjątkiem `azureml-train-automl` pakietu).
    + Rozwiązano problem w szeregów czasowych, w którym podziały OKS mają nierówne rozmiary powodujące niepowodzenie obliczeń w pojemniku.
    + W przypadku korzystania z iteracji zestawu danych dla typu uczenia krzyżowego, jeśli zakończył się problemami z pobraniem modeli przeszkolonych na całym zestawie, wystąpił niespójność między modelami wag i modelami, które zostały wprowadzone do głosu Zestawy.
    + Naprawiono błąd, zgłoszony, gdy szkolenia i/lub etykiety sprawdzania poprawności (y i y_valid) są podane w formie ramki danych Pandas, ale nie jako tablicy numpy.
    + Rozwiązano problem z zadaniami prognozowania, gdy żaden z nich nie został napotkany w kolumnach logicznych tabel wejściowych.
    + Zezwól użytkownikom AutoML na porzucanie serii szkoleniowych, które nie są wystarczająco długie podczas prognozowania. -Zezwalaj użytkownikom AutoML na upuszczanie ziaren z zestawu testów, który nie istnieje w zestawie szkoleniowym podczas prognozowania.
  + **azureml-core**
    + Rozwiązano problem z porządkowaniem parametrów blob_cache_timeout.
    + Dodano zewnętrzne Typy wyjątków dopasowania i transformacji do błędów systemowych.
    + Dodano obsługę Key Vault wpisów tajnych dla zdalnych uruchomień. Dodaj magazyn usługi Azure. Core. classing, aby dodać, pobrać i wyświetlić wpisy tajne z magazynu kluczy skojarzonego z Twoim obszarem roboczym. Obsługiwane są następujące operacje:
      + Azure. Core. Workspace. Workspace. Get _default_keyvault ()
      + Azure. Core., Magazyn kluczy. _secret (nazwa, wartość)
      + Azure. Core. kluczy. Magazyn kluczy. Set _secrets (secrets_dict)
      + Azure. Core., Magazyn kluczy. Get _secret (nazwa)
      + Azure. Core. w magazynie kluczy. _secrets. get (secrets_list)
      + Azure. Core., Magazyn kluczy. list_secrets ()
    + Dodatkowe metody uzyskiwania domyślnego magazynu kluczy i uzyskiwania wpisów tajnych podczas zdalnego uruchomienia:
      + Azure. Core. Workspace. Workspace. Get _default_keyvault ()
      + Azure. Core. Run. Run. Get _secret (nazwa)
      + Azure. Core. Run. Run. Get _secrets (secrets_list)
    + Dodano dodatkowe parametry przesłonięcia w celu przesłania polecenia CLI.
    + Zwiększ niezawodność wywołań interfejsu API, aby rozszerzać ponowną próbę na typowe wyjątki biblioteki żądań.
    + Dodano obsługę przesyłania przebiegów z przesłanego przebiegu.
    + Rozwiązano problem związany z wygaśnięciem tokenu sygnatury dostępu współdzielonego w FileWatcher, który spowodował zatrzymanie przekazywania plików po wygaśnięciu tokenu początkowego.
    + Obsługiwane Importowanie plików CSV/TSV protokołu HTTP w zestawie danych Python SDK.
    + Zaniechano metody Workspace. Setup (). Komunikat z ostrzeżeniem pokazywany użytkownikom sugeruje użycie polecenia Create () lub Get ()/from_config ().
    + Dodano środowisko. Add _private_pip_wheel (), które umożliwia przekazywanie prywatnych niestandardowych pakietów języka Python (. WHL) do obszaru roboczego i bezpieczne używanie ich do kompilowania/zmaterializowania środowiska.
    + Teraz można zaktualizować certyfikat protokołu SSL dla punktu końcowego oceniania wdrożonego w klastrze AKS zarówno dla wygenerowanego przez firmę Microsoft, jak i dla certyfikatu klienta.
  + **azureml-explain-model**
    + Dodano parametr służący do dodawania identyfikatora modelu do wyjaśnień podczas przekazywania.
    + Dodano `is_raw` znakowanie do wyjaśnień w pamięci i przekazywanie.
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
    + Teraz AutoMLStep przechodzi przez konfigurację automl do zaplecza, aby uniknąć problemów z zmianami lub dodatkami nowych parametrów konfiguracji.
  + **azureml-train-core**
    + Dodano obsługę Torch 1,2 w PyTorch szacowania.
  + **Azure — widżety**
    + Udoskonalone wykresy macierzowe z błędami na potrzeby szkoleń klasyfikacji.

### <a name="azure-portal"></a>Azure Portal
+ **Funkcja wersji zapoznawczej**
  + Przesyłanie strumieniowe plików dziennika i danych wyjściowych jest teraz dostępne dla stron szczegółów uruchamiania. Po włączeniu przełącznika podglądu pliki będą przesyłać strumieniowo aktualizacje w czasie rzeczywistym.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.55

+ **Nowe funkcje**
  + Uwierzytelnianie oparte na tokenach jest teraz obsługiwane na potrzeby wywołań punktu końcowego oceniania wdrożonego w AKS. Będziemy nadal obsługiwać bieżące uwierzytelnianie oparte na kluczach, a użytkownicy mogą korzystać z jednego z tych mechanizmów uwierzytelniania jednocześnie.
  + Możliwość zarejestrowania magazynu obiektów BLOB znajdującego się za siecią wirtualną (VNet) jako magazynem danych.
  
+ **Poprawki i ulepszenia błędów**
  + **Azure-automl-Core**
    + Naprawia usterkę, w której rozmiar walidacji podziałów OKS jest mały i powoduje nieprawidłowe wykresy przewidywane a rzeczywiste dla regresji i prognozowania.
    + Rejestrowanie zadań prognozowania w trakcie zdalnego działania zostało ulepszone, teraz użytkownik jest dostarczany z kompleksowym komunikatem o błędzie, Jeśli uruchomienie nie powiodło się.
    + Rozwiązano błędy elementu szeregów czasowych, jeśli flaga preprocesora ma wartość true.
    + Niektóre komunikaty o błędach sprawdzania poprawności danych prognozowania są bardziej funkcjonalne.
    + Zmniejszone użycie pamięci przez AutoML jest uruchamiane przez porzucanie i/lub opóźnione ładowanie zestawów danych, w szczególności między zduplikowanymi procesami
  + **Azure-contrib-opis-model**
    + Dodano flagę model_task do objaśnień, aby umożliwić użytkownikowi przesłonięcie domyślnej automatycznej logiki wnioskowania dla typu modelu
    + Zmiany widżetu: Jest automatycznie instalowany przy użyciu contrib, nie więcej nbextension instalacji/włączania
    + Zmiany pulpitu nawigacyjnego:-Box i Violins poza wykresem beeswarm na stronie podsumowania — znacznie szybsze ponowne renderowanie wykresu beeswarm na podstawie zmiany suwaka "góra-k" — w tym celu Wyjaśnij, jak najwyższe-k jest obliczane i przydatne dostosowywalne komunikaty w miejscu wykresów, gdy nie podano danych
  + **azureml-core**
    + Dodano metodę model. Package () w celu utworzenia obrazów platformy Docker i wieloetapowe dockerfile, które hermetyzują modele i ich zależności.
    + Zaktualizowano lokalne usługi WebService, aby akceptować InferenceConfigs zawierające obiekty środowiska.
    + Naprawiono model. Register () podczas tworzenia nieprawidłowych modeli, gdy "." (dla bieżącego katalogu) jest przenoszona jako parametr model_path.
    + Dodaj Run. submit_child, funkcja powoduje odbicie eksperymentu. Prześlij podczas określania uruchomienia jako elementu nadrzędnego przesłanego przebiegu podrzędnego.
    + Obsługa opcji konfiguracji z metody model. Register w programie Run. register_model.
    + Możliwość uruchamiania zadań JAR w istniejącym klastrze.
    + Teraz obsługiwane są parametry instance_pool_id i cluster_log_dbfs_path.
    + Dodano obsługę używania obiektu środowiska podczas wdrażania modelu w usłudze sieci Web. Obiekt środowiska można teraz dostarczyć jako część obiektu InferenceConfig.
    + Dodawanie mapowania appinsifht dla nowych regionów — środkowe i zachodnie-northcentralus
    + Dodano dokumentację dla wszystkich atrybutów we wszystkich klasach magazynu danych.
    + Dodano parametr blob_cache_timeout do `Datastore.register_azure_blob_container`.
    + Dodano metody save_to_directory i load_from_directory do programu Azure. Core. Environment. Environment.
    + Dodano polecenia "AZ ml Download Environment" i "polecenie" AZ ml Environment Register "do interfejsu CLI.
    + Dodano środowisko. Dodaj metodę _private_pip_wheel.
  + **azureml-explain-model**
    + Dodano śledzenie zestawu danych do wyjaśnień za pomocą usługi DataSet (wersja zapoznawcza).
    + Zmniejszono domyślny rozmiar wsadu podczas przesyłania globalnych wyjaśnień z 10 000 do 100.
    + Dodano flagę model_task do objaśnień, aby umożliwić użytkownikowi przesłonięcie domyślnej automatycznej logiki wnioskowania dla typu modelu.
  + **azureml-mlflow**
    + Naprawiono usterkę w mlflow. Azure. build_image, gdzie zagnieżdżone katalogi są ignorowane.
  + **azureml-pipeline-steps**
    + Dodano możliwość uruchamiania zadań JAR w istniejącym klastrze Azure Databricks.
    + Dodano obsługę parametrów instance_pool_id i cluster_log_dbfs_path dla kroku DatabricksStep.
    + Dodano obsługę parametrów potoku w kroku DatabricksStep.
  + **azureml-train-automl**
    + Dodano docstrings dla plików związanych z kompletem.
    + Zaktualizowane dokumenty do bardziej odpowiedniego języka dla `max_cores_per_iteration` i`max_concurrent_iterations`
    + Rejestrowanie zadań prognozowania w trakcie zdalnego działania zostało ulepszone, teraz użytkownik jest dostarczany z kompleksowym komunikatem o błędzie, Jeśli uruchomienie nie powiodło się.
    + Usunięto get_data z notesu automlstep potoku.
    + Uruchomiono obsługę w automlstep.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning zestawu SDK 1.1.10 przygotowywania danych

+ **Nowe funkcje**
  + Teraz możesz poprosić o wykonanie określonych inspektorów (np. histogramu, wykresu punktowego itp.) w określonych kolumnach.
  + Dodano argument zrównoleglanie do `append_columns`elementu. Jeśli wartość jest równa true, dane zostaną załadowane do pamięci, ale wykonywanie zostanie uruchomione równolegle; w przypadku wartości false wykonywanie będzie przesyłane strumieniowo, ale pojedyncze wątki.

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
    + Dodawanie obsługi Nimbus ML & szacowania potoki, które mają być używane w AutoML szacowania.
    + Naprawianie usterki w procedurze wyboru kompletu, która niepotrzebnie zwiększa wynikowy element, nawet jeśli wyniki pozostawały stałe.
    + Włącz ponowne użycie niektórych featurizations w ramach podziałów OKS na potrzeby prognozowania zadań. Przyspiesza to Uruchamianie Instalatora przez przekroczenie współczynnika n_cross_validations dla kosztownych featurizations, takich jak spowolnienia i kroczące okna.
    + Rozwiązywanie problemu, jeśli czas jest poza Pandas obsługiwanym zakresem czasu. Teraz możemy podnieść wartość DataException, jeśli czas jest krótszy niż PD. Sygnatura czasowa. minimalna lub większa od PD. Timestamp. Max
    + Prognozowanie umożliwia teraz różne częstotliwości w zestawach uczenia i testu, jeśli można je wyrównać. Na przykład "kwartalne rozpoczynające się w styczniu" i "co kwartał zaczynają się w październiku" można wyrównać.
    + Właściwość "Parameters" została dodana do TimeSeriesTransformer.
    + Usuń stare klasy wyjątków.
    + W przypadku `target_lags` zadań prognozowania parametr przyjmuje teraz jedną wartość całkowitą lub listę liczb całkowitych. Jeśli podano liczbę całkowitą, zostanie utworzone tylko jedno opóźnienie. W przypadku podanej listy zostaną wykonane unikatowe wartości spowolnienia. target_lags = [1, 2, 2, 4] utworzy spowolnienia jednego, 2 i 4 okresów.
    + Usuń usterkę dotyczącą utraty typów kolumn po przekształceniu (połączona usterka);
    + W `model.forecast(X, y_query)`, zezwól y_query na typ obiektu zawierający brak (s) na początku (#459519).
    + Dodaj oczekiwane wartości do danych wyjściowych automl
  + **Azure — contrib — datadryf**
    +  Ulepszenia przykładowego notesu, w tym przełączanie do platformy Uczenie maszynowe opendatasets zamiast platformy Azure-contrib-opendatasets i wydajności podczas wzbogacania danych
  + **Azure-contrib-opis-model**
    + Stałe przekształcenia — argument dla objaśniania WAPNa dla wagi nieprzetworzonej funkcji w usłudze Azure contrib-Wyjaśnij pakiet modelu
    + dodano segmentacji do wyjaśnień obrazu w wyjaśnieniu obrazu dla pakietu Azure-contrib-Wyjaśnij
    + Dodaj obsługę rozrzedzenia scipy dla LimeExplainer
    + Dodaj batch_size do programu wyjaśniającego, gdy include_local = false w celu przesyłania strumieniowego globalnych wyjaśnień w partiach, aby zwiększyć czas wykonywania DecisionTreeExplainableModel
  + **Azure-contrib-featureengineering**
    + Poprawka do wywoływania set_featurizer_timeseries_params (): DICT zmiana typu wartości i sprawdzanie pustego notesu dla szeregów czasowych featurized
    + Zaktualizuj zależność NimbusML do wersji 1.2.0 (aktualna Najnowsza wersja).
  + **azureml-core**
    + Dodano możliwość dołączania magazynów danych DBFS w interfejsie wiersza polecenia Azure 
    + Rozwiązano problem z przekazaniem magazynu danych, w którym zostanie utworzony `target_path` pusty folder, jeśli został uruchomiony z`/`
    + Rozwiązano problem DeepCopy w ServicePrincipalAuthentication.
    + Do interfejsu wiersza polecenia dodano poleceń "AZ ml Environment show" i "AZ ml Environment list".
    + Środowiska obsługują teraz Określanie base_dockerfile jako alternatywy dla już skompilowanego base_image.
    + Nieużywane ustawienie RunConfiguration auto_prepare_environment zostało oznaczone jako przestarzałe.
    + Opis modelu można teraz zaktualizować po rejestracji
    + Poprawka Usuwanie modelu i obrazu teraz zawiera więcej informacji na temat pobierania obiektów nadrzędnych, które są zależne od nich, jeśli usuwanie nie powiedzie się z powodu zależności między strumieniami.
    + Rozwiązano problem polegający na tym, że wydrukowany pusty czas trwania wdrożeń występujących podczas tworzenia obszaru roboczego dla niektórych środowisk.
    + Ulepszone błędy tworzenia obszaru roboczego. Użytkownicy nie zobaczą "nie można utworzyć obszaru roboczego. Nie można znaleźć... " jako komunikat i zamiast tego pojawia się rzeczywisty błąd tworzenia.
    + Dodano obsługę uwierzytelniania tokenów w AKS WebServices. 
    + Dodaj `get_token()` metodę do `Webservice` obiektów.
    + Dodano obsługę interfejsu wiersza polecenia do zarządzania zestawami danych uczenia maszynowego.
    + `Datastore.register_azure_blob_container`teraz opcjonalnie przyjmuje `blob_cache_timeout` wartość (w sekundach), która umożliwia skonfigurowanie parametrów instalacji blobfuse w celu włączenia wygasania pamięci podręcznej dla tego magazynu danych. Wartość domyślna nie jest przekroczeniem limitu czasu, czyli gdy obiekt BLOB jest odczytywany, pozostanie w lokalnej pamięci podręcznej do momentu zakończenia zadania. Większość zadań preferuje to ustawienie, ale niektóre zadania muszą odczytywać więcej danych z dużego zestawu danych, niż mieści się na ich węzłach. W przypadku tych zadań dostrajanie tego parametru pomoże się pomyślnie. Weź pod uwagę, gdy dostrajasz ten parametr: ustawienie zbyt małej wartości może spowodować spadek wydajności, ponieważ dane używane w epokach mogły wygasnąć przed ponownym użyciem. Oznacza to, że wszystkie operacje odczytu będą wykonywane z magazynu obiektów BLOB (tj. sieci), a nie lokalnej pamięci podręcznej, co negatywnie wpływa na czasy uczenia się.
    + Opis modelu można teraz prawidłowo zaktualizować po rejestracji
    + Usuwanie modelu i obrazu zapewnia teraz więcej informacji na temat obiektów nadrzędnych, które są od nich zależne, co powoduje niepowodzenie usuwania
    + Zwiększ wykorzystanie zasobów przez zdalne uruchomienia za pomocą usługi Azure. mlflow.
  + **azureml-explain-model**
    + Stałe przekształcenia — argument dla objaśniania WAPNa dla wagi nieprzetworzonej funkcji w usłudze Azure contrib-Wyjaśnij pakiet modelu
    + Dodaj obsługę rozrzedzenia scipy dla LimeExplainer
    + dodano otokę z objaśnieniem liniowym kształtu, a także inny poziom do tabelarycznego objaśnienia w celu wyjaśnienia modeli liniowych
    + Aby uzyskać wyjaśnienie śladu w bibliotece modeli wyjaśnień, Naprawiono błąd podczas include_local = false dla rozrzedzonych danych wejściowych
    + Dodaj oczekiwane wartości do danych wyjściowych automl
    + stała ważność funkcji permutacji podczas dostarczania argumentu Transformations w celu uzyskania ważności funkcji surowej
    + Dodaj batch_size do programu wyjaśniającego, gdy include_local = false w celu przesyłania strumieniowego globalnych wyjaśnień w partiach, aby zwiększyć czas wykonywania DecisionTreeExplainableModel
    + Aby zapoznać się z biblioteką wyjaśniającą model, należy naprawić blackboxe, gdzie dane wejściowe Pandas Dataframe są wymagane do przewidywania
    + Naprawiono usterkę, gdy `explanation.expected_values` czasami zwróci wartość zmiennoprzecinkową zamiast listy z elementem zmiennoprzecinkowym.
  + **azureml-mlflow**
    + Poprawa wydajności mlflow. Set _experiment (experiment_name)
    + Naprawianie usterki w korzystaniu z InteractiveLoginAuthentication dla mlflow tracking_uri
    + Zwiększ wykorzystanie zasobów przez zdalne uruchomienia za pomocą usługi Azure. mlflow.
    + Ulepszanie dokumentacji pakietu Azure-mlflow
    + Błąd poprawki, gdzie mlflow. log _artifacts ("my_dir") zapisze artefakty w obszarze "my_dir/< artefakty >" zamiast "< ścieżek artefaktów >"
  + **azureml-opendatasets**
    + Przypnij pyarrow z opendatasets do starych wersji (< 0.14.0) z powodu problemu z pamięcią nowo wprowadzonego w tym miejscu.
    +  Przenieś usługę Azure-contrib-opendatasets do programu Azure opendatasets. -Zezwalaj na rejestrację otwartych klas DataSet w obszarze roboczym AML i bezproblemowo wykorzystują możliwości zestawu danych AML. — Znacznie Zwiększ wydajność wzbogacania NoaaIsdWeather w wersji innej niż SPARK.
  + **azureml-pipeline-steps**
    + Magazyn danych DBFS jest teraz obsługiwany w przypadku wejść i wyjść w DatabricksStep.
    + Zaktualizowana dokumentacja Azure Batch kroku w odniesieniu do danych wejściowych/wyjściowych.
    + W programie AzureBatchStep zmieniono wartość domyślną *delete_batch_job_after_finish* na *true*.
  + **Azure-Telemetria**
    +  Przenieś usługę Azure-contrib-opendatasets do programu Azure opendatasets. -Zezwalaj na rejestrację otwartych klas DataSet w obszarze roboczym AML i bezproblemowo wykorzystują możliwości zestawu danych AML. — Znacznie Zwiększ wydajność wzbogacania NoaaIsdWeather w wersji innej niż SPARK.
  + **azureml-train-automl**
    + Zaktualizowana dokumentacja w witrynie get_output w celu odzwierciedlenia rzeczywistego typu zwracanego i podania dodatkowych informacji na temat pobierania właściwości klucza.
    + Zaktualizuj zależność NimbusML do wersji 1.2.0 (aktualna Najnowsza wersja).
    + Dodaj oczekiwane wartości do danych wyjściowych automl
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
    + Ten nowy pakiet umożliwia zarejestrowanie otwartych zestawów danych jako zestawu obiektów w obszarze roboczym AML i wykorzystanie funkcji oferowanych przez zestaw danych.
    + Obejmuje ona również istniejące funkcje, takie jak zużywanie otwartych zestawów danych jako Pandas/SPARK dataframes, a także przyłączania do lokalizacji dla niektórych elementów DataSet, takich jak pogoda.

+ **Funkcje w wersji zapoznawczej**
    + HyperDriveConfig może teraz akceptować obiekt potoku jako parametr obsługujący dostrajanie parametrów przy użyciu potoku.

+ **Poprawki i ulepszenia błędów**
  + **azureml-train-automl**
    + Rozwiązano błąd dotyczący utraty typów kolumn po transformacji.
    + Rozwiązano błąd, aby zezwolić y_query na to, że typ obiektu nie zawiera żadnego z nich na początku. 
    + Rozwiązano problem związany z procedurą wyboru kompletną, która niepotrzebnie zwiększa wynikowe zestawy, nawet jeśli wyniki pozostawały stałe.
    + Rozwiązano problem z ustawieniami whitelist_models i blacklist_models w AutoMLStep.
    + Rozwiązano problem uniemożliwiający użycie przetwarzania wstępnego, gdy AutoML mógłby zostać użyty w kontekście potoków usługi Azure ML.
  + **azureml-opendatasets**
    + Przeniesiono usługę Azure-contrib-opendatasets do programu Azure opendatasets.
    + Dozwolone są otwarte klasy DataSet do zarejestrowania w obszarze roboczym AML i bezproblemowo wykorzystują możliwości zestawu danych AML.
    + Znacznie Ulepszono NoaaIsdWeather wzbogacanie wydajności w wersji innej niż SPARK.
  + **azureml-explain-model**
    + Zaktualizowana dokumentacja online dla obiektów interpretera.
    + Dodano batch_size do programu wyjaśniającego, gdy include_local = false do przesyłania strumieniowego globalnych wyjaśnień w partiach, aby zwiększyć czas wykonywania DecisionTreeExplainableModel.
    + Rozwiązano problem polegający `explanation.expected_values` na tym, że czasami zwracają wartość zmiennoprzecinkową, a nie listę z zmiennoprzecinkową.
    + Dodano oczekiwane wartości do automl danych wyjściowych w celu uzyskania wyjaśnień w bibliotece modeli wyjaśnień.
    + Stała ważność funkcji permutacji, gdy podano argument Transformations w celu uzyskania ważności funkcji RAW.
    + Dodano batch_size do programu wyjaśniającego, gdy include_local = false do przesyłania strumieniowego globalnych wyjaśnień w partiach, aby zwiększyć czas wykonywania DecisionTreeExplainableModel dla biblioteki modelowania.
  + **azureml-core**
    + Dodano możliwość dołączania magazynów danych DBFS w interfejsie wiersza polecenia Azure.
    + Rozwiązano problem związany z przekazywaniem magazynu danych, w którym jest tworzony `target_path` pusty folder `/`, jeśli został uruchomiony z.
    + Włączono porównanie dwóch zestawów danych.
    + Usuwanie modelu i obrazu teraz zawiera więcej informacji na temat pobierania obiektów nadrzędnych, które są zależne od nich, jeśli usuwanie nie powiedzie się z powodu zależności między strumieniami.
    + Zaniechano nieużywane ustawienie RunConfiguration w auto_prepare_environment.
  + **azureml-mlflow**
    + Ulepszone wykorzystanie zasobów przez zdalne uruchomienia korzystające z platformy Azure. mlflow.
    + Ulepszona Dokumentacja pakietu Azure-mlflow.
    + Rozwiązano problem polegający na tym, że mlflow. log _artifacts ("my_dir") zapisze artefakty w ścieżce "my_dir/artefaktów" zamiast "artefaktów".
  + **azureml-pipeline-core**
    + Parametr hash_paths dla wszystkich kroków potoku jest przestarzały i zostanie usunięty w przyszłości. Domyślnie zawartość pliku katalog_źródłowy jest skrótem (z wyjątkiem plików wymienionych w pliku. amlignore lub. gitignore)
    + Kontynuuj ulepszanie modułów i ModuleStep, aby obsługiwały specyficzne dla typów obliczeń moduły, w przygotowaniu do integracji RunConfiguration i dalszych zmian w celu odblokowania ich użycia w potokach.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Ulepszona dokumentacja w odniesieniu do danych wejściowych/wyjściowych.
    + AzureBatchStep: Zmieniono wartość domyślną delete_batch_job_after_finish na true.
  + **azureml-train-core**
    + Ciągi są teraz akceptowane jako element docelowy obliczeń dla zautomatyzowanego dostrajania parametrów.
    + Zaniechano nieużywane ustawienie RunConfiguration w auto_prepare_environment.
    + Przestarzałe parametry `conda_dependencies_file_path` i `pip_requirements_file_path` `conda_dependencies_file` odpowiednio`pip_requirements_file` .
  + **azureml-opendatasets**
    + Znacznie Zwiększ wydajność wzbogacania NoaaIsdWeather w wersji innej niż SPARK.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning zestawu SDK 1.1.8 przygotowywania danych

+ **Nowe funkcje**
 + Obiekty przepływu danych można teraz powtarzać, generując sekwencję rekordów. Zapoznaj się `Dataflow.to_record_iterator`z dokumentacją.

+ **Poprawki i ulepszenia błędów**
 + Zwiększona niezawodność zestawu preprodukcyjny SDK.
 + Ulepszona obsługa Pandas dataframes z indeksami kolumn niebędącymi ciągami.
 + Ulepszona wydajność `to_pandas_dataframe` zestawów danych.
 + Naprawiono usterkę polegającą na tym, że wykonanie zestawów danych w środowisku wielowęzłowym nie powiodło się.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning zestawu SDK 1.1.7 przygotowywania danych

Przywrócono zmianę, która zwiększyła wydajność, ponieważ powodowała to problemy dla niektórych klientów korzystających Azure Databricks. Jeśli wystąpił problem dotyczący Azure Databricks, można uaktualnić do wersji 1.1.7 przy użyciu jednej z poniższych metod:
1. Uruchom ten skrypt, aby uaktualnić:`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
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
  + Model. Register obsługuje teraz rejestrowanie wielu pojedynczych plików jako jednego modelu przy `child_paths` użyciu parametru.
  
+ **Funkcje w wersji zapoznawczej**
    + Narzędzia do oceniania ocen mogą teraz opcjonalnie zapisywać informacje Conda i PIP w celu uzyskania bardziej niezawodnej serializacji i deserializacji.
    + Poprawka usterki dla selektora funkcji automatycznej.
    + Zaktualizowano mlflow. Azure. build_image do nowego interfejsu API, poprawki poprawek uwidocznione przez nową implementację.

+ **Poprawki i ulepszenia błędów**
  + Usunięto zależność paramiko od rdzenia platformy Azure. Dodano ostrzeżenia o zaniechaniu dla starszych metod dołączania obiektów docelowych obliczeń.
  + Zwiększ wydajność działania Run. create_children
  + W wyjaśnieniu z klasyfikatorem binarnym należy poprawić kolejność prawdopodobieństwa, gdy w celu skalowania wartości kształtu jest używane prawdopodobieństwo dla nauczycieli
  + Ulepszona obsługa błędów i komunikat dotyczący zautomatyzowanej uczenia maszynowego. 
  + Rozwiązano problem z limitem czasu iteracji dla automatycznego uczenia maszynowego.
  + Ulepszona wydajność transformacji szeregów czasowych na potrzeby automatycznego uczenia maszynowego.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning zestawu SDK 1.1.6 przygotowywania danych

+ **Nowe funkcje**
  + Dodano funkcje podsumowania dla najważniejszych wartości`SummaryFunction.TOPVALUES`() i dolnych`SummaryFunction.BOTTOMVALUES`wartości ().

+ **Poprawki i ulepszenia błędów**
  + Znacznie Ulepszono wydajność programu `read_pandas_dataframe`.
  + Naprawiono usterkę, `get_profile()` która spowodowałaby, że przepływu danych wskazujące na błędy plików binarnych.
  + Uwidocznione `set_diagnostics_collection()` , aby umożliwić programowe Włączanie/wyłączanie zbierania danych telemetrycznych.
  + Zmieniono zachowanie `get_profile()`. Wartości NaN są teraz ignorowane dla opcji min, średnia, std i sum, które są wyrównane z zachowaniem Pandas.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.43

+ **Nowe funkcje**
  + Azure Machine Learning teraz zapewnia obsługę pierwszej klasy dla popularnego środowiska uczenia maszynowego i analizy danych Scikit — uczenie się. Korzystając z szacowania, użytkownicy mogą łatwo uczeniować i wdrażać modele Scikit-uczyć. [ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)
    + Dowiedz się, jak [uruchomić dostrajanie parametrów za pomocą Scikit — Dowiedz się](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb), jak korzystać z usługi.
  + Dodano obsługę tworzenia ModuleStep w potokach oraz klasy modułów i ModuleVersion w celu zarządzania jednostkami obliczeniowymi wielokrotnego użytku.
  + Usługi WebServices ACI obsługują teraz trwałe scoring_uri za pomocą aktualizacji. Scoring_uri zmieni się z adresu IP na nazwę FQDN. Etykieta nazwy DNS dla nazwy FQDN można skonfigurować przez ustawienie dns_name_label w deploy_configuration. 
  + Automatyczne Uczenie maszynowe nowe funkcje:
    + STL featurized na potrzeby prognozowania
    + KMeans klastrowanie jest włączone do czyszczenia funkcji
  + AmlCompute zatwierdzeń przydziałów stało się szybsze. Teraz zautomatyzowany proces zatwierdzania żądań limitu przydziału w ramach progu. Aby uzyskać więcej informacji o działaniu przydziałów, Dowiedz się, [jak zarządzać](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas)przydziałami.

+ **Funkcje w wersji zapoznawczej**
    + Integracja z usługą [MLflow](https://mlflow.org) 1.0.0 Tracking za pomocą pakietu Azure-MLflow ([przykładowe notesy](https://aka.ms/azureml-mlflow-examples)).
    + Prześlij Notes Jupyter jako przebieg. [Dokumentacja interfejsu API](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Publiczna wersja zapoznawcza narzędzia do [wykrywania dryfów danych](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift?view=azure-ml-py) za poorednictwem pakietu Azure-contrib-datadryfing ([przykładowe notesy](https://aka.ms/azureml-datadrift-example)). Dryfowanie danych jest jednym z najważniejszych powodów, w których dokładność modelu ulega obniżeniu w miarę upływu czasu. Zdarza się to, gdy dane dostarczane do modelu w środowisku produkcyjnym różnią się od danych, na których jest szkolony model. Narzędzie AML dryfowania danych ułatwia Klientowi monitorowanie dryfowania danych i wysyłanie alertów w przypadku wykrycia dryfu. 

+ **Fundamentalne zmiany**

+ **Poprawki i ulepszenia błędów**
  + RunConfiguration Załaduj i Zapisz obsługuje określenie pełnej ścieżki pliku z pełnymi kopiami zapasowymi dla poprzedniego zachowania.
  + Dodano buforowanie w ServicePrincipalAuthentication, domyślnie wyłączone.
  + Włącz rejestrowanie wielu wykresów w tej samej nazwie metryki.
  + Klasa modelu jest teraz prawidłowo przewoźny z platformy Azure. Core`from azureml.core import Model`().
  + W przypadku `hash_path` etapów potoku parametr jest obecnie przestarzały. Nowe zachowanie polega na wykonaniu skrótu wartości katalog_źródłowy, z wyjątkiem plików wymienionych w. amlignore lub. gitignore.
  + W pakietach potokowych `get_all` różne `get_all_*` i metody były `list` przestarzałe na rzecz i `list_*`, odpowiednio.
  + Azure. Core. Get _run nie wymaga już, aby klasy zostały zaimportowane przed zwróceniem oryginalnego typu uruchomienia.
  + Rozwiązano problem polegający na tym, że niektóre wywołania aktualizacji usługi WebService nie powodowały aktualizacji.
  + Limit czasu oceniania w usłudze AKS WebService musi należeć do zakresu od 5 ms do 300000ms. Maksymalna dozwolona scoring_timeout_ms dla żądań oceniania była niedostępna od 1 do 5 minut.
  + Obiekty LocalWebservice mają `scoring_uri` teraz właściwości `swagger_uri` i.
  + Przeniesiono Tworzenie katalogów wyjściowych i przekazywanie katalogów danych wyjściowych z procesu użytkownika. Włączono zestaw SDK historii uruchamiania do uruchomienia w każdym procesie użytkownika. Powinno to rozwiązać pewne problemy z synchronizacją napotkane przez rozproszone przebiegi szkoleniowe.
  + Nazwa dziennika usługi Azure zapisywana na podstawie nazwy procesu użytkownika będzie teraz zawierać nazwę procesu (dotyczy tylko szkoleń rozproszonych) i identyfikatora PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning zestawu SDK 1.1.5 przygotowywania danych

+ **Poprawki i ulepszenia błędów**
  + W przypadku interpretowanych wartości datetime o 2-cyfrowym formacie roku Zakres prawidłowych lat został zaktualizowany tak, aby pasował do systemu Windows. Zakres został zmieniony z 1930-2029 na 1950-2049.
  + Podczas odczytywania w pliku i ustawieniu `handleQuotedLineBreaks=True` `\r` program będzie traktowany jako nowy wiersz.
  + Rozwiązano błąd, który `read_pandas_dataframe` spowodował niepowodzenie w niektórych przypadkach.
  + Ulepszona wydajność `get_profile`programu.
  + Ulepszone komunikaty o błędach.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Zestaw SDK pakietu przygotowywania danych Azure Machine Learning

+ **Nowe funkcje**
  + Teraz można używać następujących funkcji języka wyrażeń do wyodrębniania i analizowania wartości DateTime w nowych kolumnach.
    + `RegEx.extract_record()`wyodrębnia elementy DateTime do nowej kolumny.
    + `create_datetime()`tworzy obiekty DateTime z oddzielnych elementów DateTime.
  + Po wywołaniu `get_profile()`, można teraz zobaczyć, że kolumny quantile są oznaczone jako (EST.), aby jasno wskazać, że wartości są przybliżami.
  + W przypadku odczytywania z usługi Azure Blob Storage można teraz używać * * obsługi symboli wieloznacznych.
    + tj.`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Poprawki błędów**
  + Naprawiono usterkę związaną z odczytywaniem pliku Parquet ze źródła zdalnego (obiekt blob platformy Azure).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.39
+ **Wprowadzane**
  + Opcja uruchamiania konfiguracji auto_prepare_environment jest przestarzała, a funkcja autoprzygotowywania stanie się wartością domyślną.

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

### <a name="azure-portal"></a>Azure Portal

W Azure Portal można teraz:
+ Tworzenie i uruchamianie zautomatyzowanych eksperymentów ML 
+ Utwórz maszynę wirtualną notesu, aby wypróbować przykładowe notesy Jupyter lub własne.
+ Zupełnie nowa sekcja tworzenia (wersja zapoznawcza) w obszarze roboczym usługi Machine Learning, która obejmuje automatyczne Machine Learning, interfejs wizualny i hostowane maszyny wirtualne notesu
    + Automatycznie twórz model przy użyciu automatycznego uczenia maszynowego 
    + Używanie interfejsu wizualizacji przeciągnij i upuść do uruchamiania eksperymentów
    + Tworzenie maszyny wirtualnej notesu do eksplorowania danych, tworzenia modeli i wdrażania usług.
+ Aktualizowanie wykresu i metryk na żywo na stronach uruchamiania raportów i uruchamiania szczegółów
+ Zaktualizowano Podgląd plików dla dzienników, danych wyjściowych i migawek na stronach szczegółów uruchamiania.
+ Nowe i ulepszone środowisko tworzenia raportów na karcie eksperymenty. 
+ Dodano możliwość pobrania pliku config. JSON z strony przegląd obszaru roboczego usługi Azure Machine Learning.
+ Obsługa Machine Learning tworzenia obszaru roboczego usługi z poziomu obszaru roboczego Azure Databricks 

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
+ [Szybko przechodzenie do wstępnie skonfigurowanej maszyny wirtualnej](tutorial-1st-experiment-sdk-setup.md) notesu, która ma najnowszą wersję zestawu SDK Azure Machine Learning i powiązane pakiety.
+ Dostęp jest zabezpieczony za pośrednictwem sprawdzonych technologii, takich jak HTTPS, Azure Active Directory uwierzytelniania i autoryzacji.
+ Niezawodny magazyn w chmurze dla notesów i kodu na koncie Obszar roboczy usługi Azure Machine Learning BLOB Storage. Możesz bezpiecznie usunąć maszynę wirtualną notesu bez utraty pracy.
+ Wstępnie zainstalowane przykładowe notesy umożliwiające eksplorowanie i eksperymentowanie z funkcjami usługi Azure Machine Learning Service.
+ Pełne możliwości dostosowywania maszyn wirtualnych platformy Azure, wszystkie typy maszyn wirtualnych, wszystkie pakiety i sterowniki. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning zestawu SDK dla języka Python v 1.0.33.

+ Usługa Azure ML Modele z przyspieszaniem sprzętowym w systemie [FPGA](concept-accelerate-with-fpgas.md) jest ogólnie dostępna.
  + Teraz można [używać pakietu Azure-akceleracja-models](how-to-deploy-fpga-web-service.md) do:
    + Uczenie wag obsługiwanej sieci głębokiej neuronowych (ResNet 50, ResNet 152, DenseNet-121, VGG-16 i SSD-VGG)
    + Korzystanie z uczenia transferowego z obsługiwanym DNN
    + Zarejestruj model przy użyciu usługi Zarządzanie modelami i konteneryzowanie model
    + Wdrażanie modelu na maszynie wirtualnej platformy Azure przy użyciu FPGA w klastrze usługi Azure Kubernetes Service (AKS)
  + Wdrażanie kontenera na urządzeniu z serwerem [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)
  + Poprowadź ocenę danych za pomocą punktu końcowego gRPC [](https://github.com/Azure-Samples/aml-hardware-accelerated-models) z tym przykładem

### <a name="automated-machine-learning"></a>Zautomatyzowane uczenie maszynowe

+ Czyszczenie funkcji umożliwiające dynamiczne dodawanie featurizers na potrzeby optymalizacji wydajności. New featurizers: osadzanie pracy, Waga dowodów, kodowanie docelowe, kodowanie tekstu docelowego, odległość klastra
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

[`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) Wprowadzono dodanie nowej wersji opublikowanego potoku przy zachowaniu tego samego punktu końcowego.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning zestaw SDK przygotowania do przygotowywania danych

Uwaga: Zestaw SDK języka Python dla przygotowywania danych `numpy` nie `pandas` będzie już instalowany i pakietów. Zobacz [zaktualizowane instrukcje dotyczące instalacji](https://aka.ms/aml-data-prep-installation).

+ **Nowe funkcje**
  + Teraz można użyć przekształcenia Pivot.
    + Przewodnik: [Notes przestawny](https://aka.ms/aml-data-prep-pivot-nb)
  + Teraz można używać wyrażeń regularnych w funkcjach natywnych.
    + Przykłady:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Teraz `to_upper`można używać  funkcji i `to_lower`wjęzykuwyrażeń  .
  + Teraz można zobaczyć liczbę unikatowych wartości każdej kolumny w profilu danych.
  + W przypadku niektórych często używanych kroków czytelnika można teraz przekazać `infer_column_types` argument. Jeśli jest ustawiona na `True`, przygotowanie danych podejmie próbę wykrycia i automatycznego przekonwertowania typów kolumn.
    + `inference_arguments`jest obecnie przestarzałe.
  + Teraz można wywołać metodę `Dataflow.shape`.

+ **Poprawki i ulepszenia błędów**
  + `keep_columns` teraz akceptujemy dodatkowy opcjonalny argument `validate_column_exists`, który sprawdza, czy `keep_columns` wynik będzie zawierać dowolne kolumny.
  + Wszystkie kroki czytnika (które odczytają plik) teraz akceptują dodatkowy opcjonalny argument `verify_exists`.
  + Ulepszona wydajność odczytywania z Pandas Dataframe i pobierania profilów danych.
  + Rozwiązano problem polegający na tym, że cięcie pojedynczego kroku z przepływu danych nie powiodło się z pojedynczym indeksem.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + Teraz można ponownie przesłać istniejący skrypt uruchomiony w istniejącym zdalnym klastrze obliczeniowym. 
  + Teraz można uruchomić opublikowany potok z nowymi parametrami na karcie potoki. 
  + Szczegóły uruchamiania obsługują teraz nową przeglądarkę plików migawek. Możesz wyświetlić migawkę katalogu podczas przesyłania określonego uruchomienia. Możesz również pobrać Notes, który został przesłany w celu uruchomienia uruchomienia.
  + Teraz można anulować uruchomienia nadrzędne z Azure Portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.23

+ **Nowe funkcje**
  + Zestaw SDK Azure Machine Learning obsługuje teraz środowisko Python 3,7.
  + Azure Machine Learning DNN szacowania teraz zapewnia wbudowaną obsługę wielowersji. Na przykład `TensorFlow`  szacowania teraz akceptuje `framework_version` parametr, a użytkownicy mogą określić wersję "1,10" lub "1,12". Aby zapoznać się z listą wersji obsługiwanych przez bieżącą wersję zestawu SDK, `get_supported_versions()` należy wywołać żądaną klasę struktury (na `TensorFlow.get_supported_versions()`przykład).
  Listę wersji obsługiwanych przez najnowszą wersję zestawu SDK można znaleźć w [dokumentacji DNN szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning przygotowanie zestawu danych SDK v 1.1.1

+ **Nowe funkcje**
  + Za pomocą read_ * transformacje można odczytać wiele źródeł danych/danych źródłowych/DataReference.
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
  + Metoda *Azure. Core. Run. create_children* umożliwia tworzenie wielu obiektów podrzędnych przy użyciu jednego wywołania z niskim opóźnieniem.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning zestawu SDK 1.1.0 przygotowywania danych

+ **Fundamentalne zmiany**
  + Koncepcja pakietu przygotowywania danych jest przestarzała i nie jest już obsługiwana. Zamiast utrwalać wiele przepływów elementów w jednym pakiecie, można utrwalać przepływy elementów pojedynczo.
    + Przewodnik: [Otwieranie i zapisywanie notesu dataflows](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Nowe funkcje**
  + Przygotowanie danych może teraz rozpoznać kolumny, które pasują do określonego typu semantycznego i odpowiednio podzielić. Obecnie obsługiwane STypes obejmują: adres e-mail, współrzędne geograficzne (Szerokość geograficzna &), adresy IPv4 i IPv6, numer telefonu USA i kod pocztowy US.
    + Przewodnik: [Notes typów semantycznych](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Przygotowanie danych obsługuje teraz następujące operacje, aby wygenerować kolumnę wynikową z dwóch kolumn liczbowych: Odejmij, pomnóż, Podziel i modulo.
  + Możesz wywołać `verify_has_data()` przepływu danych, aby sprawdzić, czy przepływu danych będzie generować rekordy, jeśli są wykonywane.

+ **Poprawki i ulepszenia błędów**
  + Teraz możesz określić liczbę pojemników, które mają być używane w histogramie dla profilów kolumn liczbowych.
  + `read_pandas_dataframe` Przekształcanie wymaga teraz, aby ramka danych zawierała nazwy kolumn lub ciągów z typem bajtów.
  + Naprawiono usterkę `fill_nulls` w przekształceniu, gdzie wartości nie zostały prawidłowo wypełnione, jeśli brakuje kolumny.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.18

 + **Wprowadzane**
   + Pakiet Azure-tensorboard zastępuje usługę Azure-contrib-tensorboard.
   + W tej wersji można skonfigurować konto użytkownika w zarządzanym klastrze obliczeniowym (amlcompute) podczas jego tworzenia. Można to zrobić, przekazując te właściwości w konfiguracji aprowizacji. Więcej szczegółów można znaleźć w [dokumentacji referencyjnej zestawu SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-).

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

  + Azure Machine Learning teraz zapewnia obsługę pierwszej klasy dla popularnego łańcucha DNN Framework. Korzystanie [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) z klas użytkownicy mogą łatwo przeszkolić i wdrożyć modele łańcucha.
    + Dowiedz się, jak [uruchomić szkolenie rozproszone z ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Dowiedz się, jak [uruchomić dostrajanie parametrów za pomocą łańcucha](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning potoki dodaliśmy możliwość wyzwalania uruchomienia potoku na podstawie modyfikacji magazynu danych. [Notes harmonogramu](https://aka.ms/pl-schedule) potoku został zaktualizowany w celu pokazania tej funkcji.

+ **Poprawki i ulepszenia błędów**
  + Dodaliśmy obsługę potoków Azure Machine Learning, aby ustawić właściwość source_directory_data_store na żądany magazyn danych (np. Magazyn obiektów BLOB) w [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) , które są dostępne dla [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Zgodnie z domyślnymi krokami usługa Azure File Store jest używana jako zapasowy magazyn danych, co może powodować problemy z ograniczaniem wydajności w przypadku wykonywania dużej liczby kroków współbieżnie.

### <a name="azure-portal"></a>Azure Portal

+ **Nowe funkcje**
  + Nowe środowisko edytora tabeli przeciągnij i upuść dla raportów. Użytkownicy mogą przeciągać kolumnę od samego do obszaru tabeli, w którym zostanie wyświetlona wersja zapoznawcza tabeli. Kolumny można zmieniać.
  + Nowa przeglądarka plików dzienników
  + Linki do przebiegów eksperymentów, obliczenia, modele, obrazy i wdrożenia z poziomu karty działania

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning zestawu SDK 1.0.15 przygotowywania danych

+ **Nowe funkcje**
  + Przygotowanie danych obsługuje teraz zapisywanie strumieni plików z przepływu danych. Zapewnia również możliwość manipulowania nazwami strumieni plików w celu tworzenia nowych nazw plików.
    + Przewodnik: [Praca z notesem strumieni plików](https://aka.ms/aml-data-prep-file-stream-nb)

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
  + `PublishedPipeline.get_published_pipeline` Przestarzałe`PublishedPipeline.get`.
  + `Schedule.get_schedule` Przestarzałe`Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning zestawu SDK 1.0.12 przygotowywania danych

+ **Nowe funkcje**
  + Przygotowanie danych obsługuje teraz odczytywanie z usługi Azure SQL Database przy użyciu magazynu danych.
 
+ **Wprowadzane**
  + Ulepszona wydajność pamięci niektórych operacji na dużych danych.
  + `read_pandas_dataframe()``temp_folder` teraz należy określić.
  + Właściwość na `ColumnProfile` jest przestarzała — Użyj `column_name` zamiast niej. `name`

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.10

+ **Zmiany**: 
  + Zestaw Azure ML SDK nie ma już pakietów interfejsu wiersza polecenia platformy Azure jako zależności. W szczególnych przypadkach platformy Azure-CLI-Core i Azure-CLI-profile zostały usunięte z platformy Azure Core. Są to zmiany wpływające na użytkowników:
    + Jeśli wykonujesz polecenie "AZ login", a następnie korzystasz z zestawu Azure-SDK, zestaw SDK będzie wykonywał dziennik przeglądarki lub kodu urządzenia jeszcze raz. Nie będzie on używać żadnego stanu poświadczeń utworzonego przez użytkownika AZ login.
    + W przypadku uwierzytelniania interfejsu wiersza polecenia platformy Azure, na przykład przy użyciu polecenia "AZ login", należy użyć klasy _Azure. Core. Authentication. AzureCliAuthentication_ . W przypadku uwierzytelniania interfejsu wiersza polecenia platformy Azure do narzędzia _PIP Zainstaluj interfejs wiersza polecenia platformy Azure_ w środowisku języka Python, w którym zainstalowano zestaw Azure-SDK.
    + Jeśli wykonujesz polecenie "AZ login" przy użyciu nazwy głównej usługi dla automatyzacji, zalecamy użycie klasy " _Azure. Core. Authentication. ServicePrincipalAuthentication_ ", ponieważ usługa Azure SDK nie będzie używać stanu poświadczeń utworzonego przez interfejs wiersza polecenia platformy Azure. 

+ **Poprawki błędów**: Ta wersja zawiera głównie poprawki błędów pomocniczych

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
    - Wykres punktowy
    - Wykres bąbelkowy
+ Portal będzie teraz dynamicznie generował raporty dotyczące eksperymentów. Gdy użytkownik przesyła do eksperymentu przebieg, raport zostanie automatycznie wygenerowany z zarejestrowanymi metrykami i wykresami, aby umożliwić porównanie różnych przebiegów. 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.8

+ **Poprawki błędów**: Ta wersja zawiera głównie poprawki błędów pomocniczych

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning zestawu SDK 1.0.7 przygotowywania danych

+ **Nowe funkcje**
  + Udoskonalenia magazynu danych (udokumentowane w przewodniku w [usłudze Magazyn](https://aka.ms/aml-data-prep-datastore-nb)danych)
    + Dodano możliwość odczytu i zapisu do udziału plików platformy Azure oraz ADLS magazynów danych w celu skalowania w górę.
    + W przypadku korzystania z magazynów danych program przygotowywania w ramach programu obsługuje teraz używanie uwierzytelniania nazwy głównej usługi zamiast interakcyjnego uwierzytelniania.
    + Dodano obsługę adresów URL wasb i wasbs.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning zestawu SDK 1.0.6 przygotowywania danych

+ **Poprawki błędów**
  + Rozwiązano problem z odczytem z publicznego, czytelnego kontenera obiektów blob platformy Azure na platformie Spark

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.6
+ **Poprawki błędów**: Ta wersja zawiera głównie poprawki błędów pomocniczych

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning zestawu SDK 1.0.4 przygotowywania danych

+ **Nowe funkcje**
  + `to_bool`funkcja umożliwia teraz konwertowanie niezgodnych wartości na wartości błędów. Jest to nowe domyślne zachowanie niezgodności dla `to_bool` i `set_column_types`, natomiast poprzednie zachowanie domyślne polega na przekonwertowaniu niezgodnych wartości na wartość false.
  + W przypadku `to_pandas_dataframe`wywołania, istnieje nowa opcja interpretacji wartości null/brakująca w kolumnach liczbowych jako NaN.
  + Dodano możliwość sprawdzenia zwracanego typu niektórych wyrażeń w celu zapewnienia spójności typów i wczesnego powrotu po awarii.
  + Teraz można wywołać metody `parse_json` analizy wartości w kolumnie jako obiekty JSON i rozwinąć je do wielu kolumn.

+ **Poprawki błędów**
  + Naprawiono usterkę, `set_column_types` która uległa awarii w języku Python 3.5.2.
  + Naprawiono usterkę, która uległa awarii podczas nawiązywania połączenia z magazynem danych przy użyciu obrazu AML.

+ **Aktualizacje**
  * [Przykładowe notesy](https://aka.ms/aml-data-prep-notebooks) dla samouczków rozpoczynających pracę, analizy przypadków i przewodniki.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Ogólna dostępność

Usługa Azure Machine Learning jest teraz ogólnie dostępna.

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
  * Użyj najnowszej AML Uruchom tokenu podczas odczytywanie z oraz zapisywanie do magazynów danych na zdalnych przebiegów. Wcześniej Jeśli tokenu Uruchom AML zostanie zaktualizowany w języku Python, przygotowywanie danych środowiska uruchomieniowego nie zostanie zaktualizowany przy użyciu zaktualizowanych AML Uruchom tokenu.
  * Dodatkowe przejrzyste komunikaty o błędach
  * to_spark_dataframe () nie będzie już ulegać awarii, `Kryo` gdy platforma Spark używa serializacji
  * Liczba wartości Inspector umożliwia teraz wyświetlanie więcej niż 1000 unikatowe wartości
  * Losowe podziału nie jest już nie powiedzie się jeśli oryginalny przepływu danych nie ma nazwy  

+ **Więcej informacji**
  * [Zestaw SDK przygotowywania danych usługi Azure Machine Learning](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Dokumenty i notesów
+ Potokach uczenia Maszynowego
  + Nowe i zaktualizowane notesów dla klientów zaczynających się od potoki, określania zakresu usługi batch i styl transferu przykłady: https://aka.ms/aml-pipeline-notebooks
  + Dowiedz się, jak [Tworzenie pierwszego potoku](how-to-create-your-first-pipeline.md)
  + Dowiedz się, jak [Uruchom prognozy usługi batch przy użyciu potoków](how-to-run-batch-predictions.md)
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
   * Można uzyskać wszystkie typy danych kolumn z przepływu danych lub profilu danych przez wywołanie`.dtypes`
   * Może pobrać liczbę wierszy z przepływu danych lub profilu danych przez wywołanie`.row_count`

+ **Poprawki błędów**
   * Stałą Długa podwójna konwersja 
   * Naprawiono assert po dowolnej Dodaj kolumnę 
   * Rozwiązano problem z FuzzyGrouping, gdzie nie może nie wykryć grup w niektórych przypadkach
   * Funkcja stałej sortowania przestrzegają kolejność sortowania wielokolumnowych
   * Wyrażenia stałe i/lub, które są podobne do `pandas` sposobu ich obsługi
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
  * Rozwiązano ensembling problemy iteracji.
  * Naprawiono szkolenia zwisa błędów w systemie MAC OS.
  * Próbkowanie — makro średni żądania Ściągnięcia/WIELOKLASOWA krzywa w scenariuszu niestandardowego sprawdzania poprawności.
  * Usunięty indeks dodatkowej logiki.
  * Usunąć filtr z get_output interfejsu API.

+ **Potoki**
  * Dodaje metody Pipeline.publish() publikowanie potoku bezpośrednio, bez konieczności wykonywania są uruchamiane jako pierwsze.   
  * Dodano metodę PipelineRun. Get _pipeline_runs (), aby pobrać uruchomienia potoku, które zostały wygenerowane z opublikowanego potoku.

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

Nowa odświeżona wersja Azure Machine Learning: Przeczytaj więcej na temat tej wersji: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z omówieniem [usługi Azure Machine Learning](../service/overview-what-is-azure-ml.md).
