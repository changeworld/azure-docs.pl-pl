---
title: 'Samouczek: Tworzenie modelu uczenia maszynowego za pomocą języka R'
titleSuffix: Azure Machine Learning
description: W tym samouczku użyjesz zestawu SDK usługi Azure Machine Learning do utworzenia modelu regresji logistycznej, który przewiduje prawdopodobieństwo śmierci w wypadku samochodowym.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 02/07/2020
ms.openlocfilehash: 5b1c6561519bc25c2b7ac77f0a25eff89413a07a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256488"
---
# <a name="tutorial-use-r-to-create-a-machine-learning-model"></a>Samouczek: Tworzenie modelu uczenia maszynowego za pomocą języka R
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym samouczku użyjesz zestawu SDK usługi Azure Machine Learning do utworzenia modelu regresji logistycznej, który przewiduje prawdopodobieństwo śmierci w wypadku samochodowym. Zobaczysz, jak zasoby chmury usługi Azure Machine Learning współpracują z usługą R, aby zapewnić skalowalne środowisko do szkolenia i wdrażania modelu.  

Ten samouczek obejmuje wykonanie następujących zadań:
> [!div class="checklist"]
> * Tworzenie obszaru roboczego usługi Azure Machine Learning
> * Klonowanie folderu notesu z plikami niezbędnymi do uruchomienia tego samouczka w obszarze roboczym
> * Otwieranie rstudio z obszaru roboczego
> * Załaduj dane i przygotuj się do treningu
> * Przesyłanie danych do magazynu danych, aby były dostępne do zdalnego szkolenia
> * Tworzenie zasobu obliczeniowego w celu zdalnego szkolenia modelu
> * Trenuj `caret` model, aby przewidzieć prawdopodobieństwo śmierci
> * Wdrażanie punktu końcowego prognozowania
> * Przetestuj model z R

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.


## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar roboczy usługi Azure Machine Learning to podstawowy zasób w chmurze używany do eksperymentowania, uczenia i wdrażania modeli uczenia maszynowego. Wiąże subskrypcję platformy Azure i grupę zasobów z łatwo zużywanym obiektem w usłudze. 

Tworzenie obszaru roboczego za pośrednictwem witryny Azure portal, konsoli opartej na sieci Web do zarządzania zasobami platformy Azure. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Zanotuj **swój obszar roboczy** i **subskrypcję**. Będą one potrzebne, aby zapewnić utworzenie eksperymentu we właściwym miejscu. 


## <a name="clone-a-notebook-folder"></a><a name="azure"></a>Klonowanie folderu notesu

W tym przykładzie użyto serwera notesu w chmurze w obszarze roboczym dla środowiska wolnego od instalacji i wstępnie skonfigurowanego środowiska. Użyj [własnego środowiska,](https://azure.github.io/azureml-sdk-for-r/articles/installation.html) jeśli wolisz mieć kontrolę nad środowiskiem, pakietami i zależnościami.

Wykonaj następującą konfigurację eksperymentu i uruchom kroki w usłudze Azure Machine Learning studio, skonsolidowanym interfejsie, który zawiera narzędzia uczenia maszynowego do wykonywania scenariuszy do nauki o danych dla praktyków nauki o danych na wszystkich poziomach umiejętności.

1. Zaloguj się do [studia usługi Azure Machine Learning](https://ml.azure.com/).

1. Wybierz subskrypcję i utworzony obszar roboczy.

1. Po lewej stronie **wybierz pozycję Notesy.**

1. Otwórz folder **Przykłady.**

1. Otwórz folder **R.**

1. Otwórz folder z numerem wersji.  Ta liczba reprezentuje bieżącą wersję zestawu R SDK.

1. Wybierz **"..."** po prawej stronie folderu **winiety,** a następnie wybierz pozycję **Klonuj**.

    ![Folder Klonowanie](media/tutorial-1st-r-experiment/clone-folder.png)

1. Zostanie wyświetlona lista folderów przedstawiająca każdego użytkownika, który uzyskuje dostęp do obszaru roboczego.  Wybierz folder, aby sklonować tam folder **winiet.**

## <a name="a-nameopenopen-rstudio"></a><a name="open">Otwórz RStudio

Użyj RStudio na wystąpieniu obliczeniowym lub maszynie wirtualnej notesu, aby uruchomić ten samouczek.  

1. Wybierz **pozycję Oblicz** po lewej stronie.

1. Dodaj zasób obliczeniowy, jeśli jeszcze go nie istnieje.

1. Po uruchomieniu obliczeń użyj łącza **RStudio,** aby otworzyć RStudio.

1. W RStudio, twój folder *winiety* jest kilka poziomów w dół od *użytkowników* w sekcji **Pliki** w prawym dolnym prawa.  W *obszarze winiety*wybierz folder *train-and-deploy-to-aci, aby* znaleźć pliki potrzebne w tym samouczku.

> [!Important]
> Pozostała część tego artykułu zawiera tę samą zawartość, jak w *train-and-deploy-to-aci. Rmd.* Jeśli masz doświadczenie z RMarkdown, nie krępuj się używać kodu z tego pliku.  Możesz też skopiować/wkleić fragmenty kodu stamtąd lub z tego artykułu do skryptu R lub wiersza polecenia.  


## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego
Konfiguracja prac rozwojowych w tym samouczku zawiera następujące akcje:

* Instalowanie wymaganych pakietów
* Łączenie się z obszarem roboczym, aby wystąpienie obliczeniowe mogły komunikować się z zasobami zdalnymi
* Tworzenie eksperymentu w celu śledzenia przebiegów
* Tworzenie zdalnego celu obliczeniowego do użycia w szkoleniu

### <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

 * Zainstaluj najnowszą wersję z CRAN.

    ```R
    # install the latest version from CRAN
    install.packages("azuremlsdk")
    azuremlsdk::install_azureml(envname = 'r-reticulate')
    ```
    
* Lub zainstaluj wersję deweloperską z GitHub.

    ```R
    # or install the development version from GitHub
    remotes::install_github('https://github.com/Azure/azureml-sdk-for-r')
    azuremlsdk::install_azureml(envname = 'r-reticulate')
    ```

Teraz śmiało i zaimportować pakiet **azuremlsdk.**

```R
library(azuremlsdk)
```

Skrypty szkoleniowe i`accidents.R` `accident_predict.R`oceniające ( i ) mają pewne dodatkowe zależności. Jeśli planujesz uruchamianie tych skryptów lokalnie, upewnij się, że masz te wymagane pakiety, jak również.

### <a name="load-your-workspace"></a>Załaduj swój obszar roboczy
Tworzenie wystąpienia obiektu obszaru roboczego z istniejącego obszaru roboczego. Poniższy kod spowoduje załadowanie szczegółów obszaru roboczego z pliku **config.json.** Można również pobrać obszar roboczy za pomocą programu [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html).

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Tworzenie eksperymentu
Eksperyment usługi Azure ML śledzi grupowanie przebiegów, zazwyczaj z tego samego skryptu szkoleniowego. Utwórz eksperyment, aby śledzić przebiegi do szkolenia modelu karetki na danych wypadków.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>Tworzenie celu obliczeniowego
Za pomocą usługi zarządzanej Azure Machine Learning Compute (AmlCompute) analitycy danych mogą szkolić modele uczenia maszynowego w klastrach maszyn wirtualnych platformy Azure. Przykłady obejmują maszyny wirtualne z obsługą procesorów GPU. W tym samouczku utworzysz klaster AmlCompute z jednym węzłem jako środowisko szkoleniowe. Poniższy kod tworzy klaster obliczeniowy, jeśli jeszcze nie istnieje w obszarze roboczym.

Może być konieczne odczekanie kilku minut na aprowizacji klastra obliczeniowego, jeśli jeszcze nie istnieje.

```R
cluster_name <- "rcluster"
compute_target <- get_compute(ws, cluster_name = cluster_name)
if (is.null(compute_target)) {
  vm_size <- "STANDARD_D2_V2" 
  compute_target <- create_aml_compute(workspace = ws,
                                       cluster_name = cluster_name,
                                       vm_size = vm_size,
                                       max_nodes = 1)
}

wait_for_provisioning_completion(compute_target)
```

## <a name="prepare-data-for-training"></a>Przygotowywanie danych do szkolenia
Ten poradnik wykorzystuje dane z US [National Highway Traffic Safety Administration](https://cdan.nhtsa.gov/tsftables/tsfar.htm) (dzięki Mary [C. Meyer i Tremika Finney](https://www.stat.colostate.edu/~meyer/airbags.htm)).
Ten zestaw danych zawiera dane z ponad 25 000 wypadków samochodowych w Stanach Zjednoczonych, ze zmiennymi, których można użyć do przewidzenia prawdopodobieństwa śmierci. Najpierw zaimportuj dane do R i `accidents` przekształć `Rdata` je w nową ramkę danych do analizy i wyeksportuj do pliku.

```R
nassCDS <- read.csv("nassCDS.csv", 
                     colClasses=c("factor","numeric","factor",
                                  "factor","factor","numeric",
                                  "factor","numeric","numeric",
                                  "numeric","character","character",
                                  "numeric","numeric","character"))
accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)
accidents$dvcat <- ordered(accidents$dvcat, 
                          levels=c("1-9km/h","10-24","25-39","40-54","55+"))

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>Przekazywanie danych do magazynu danych
Przekaż dane do chmury, aby były dostępne w środowisku szkolenia zdalnego. Każdy obszar roboczy usługi Azure Machine Learning jest dostarczany z domyślnym magazynem danych, który przechowuje informacje o połączeniu z kontenerem obiektów blob platformy Azure, który jest aprowiada na koncie magazynu dołączonym do obszaru roboczego. Poniższy kod przekaże dane dotyczące wypadków utworzone powyżej do tego magazynu danych.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>Szkolenie modelu

W tym samouczku dopasuj model regresji logistycznej do przekazanych danych przy użyciu zdalnego klastra obliczeniowego. Aby przesłać pracę, musisz:

* Przygotowanie skryptu szkolenia
* Tworzenie narzędzia do szacowania
* Przesyłanie zadania

### <a name="prepare-the-training-script"></a>Przygotowanie skryptu szkolenia
Skrypt szkolenia `accidents.R` o nazwie został dostarczony dla Ciebie w tym samym katalogu, co ten samouczek. Zwróć uwagę na następujące szczegóły **wewnątrz skryptu szkoleniowego,** które zostały wykonane w celu wykorzystania usługi Azure Machine Learning do szkolenia:

* Skrypt szkolenia przyjmuje `-d` argument, aby znaleźć katalog, który zawiera dane szkoleniowe. Podczas definiowania i przesyłania zadania później, należy wskazać do magazynu danych dla tego argumentu. Usługa Azure ML zainstaluje folder magazynu w klastrze zdalnym dla zadania szkoleniowego.
* Skrypt szkolenia rejestruje ostateczną dokładność jako metrykę do rekordu `log_metric_to_run()`uruchomienia w usłudze Azure ML przy użyciu . Zestaw SDK usługi Azure ML udostępnia zestaw interfejsów API rejestrowania do rejestrowania różnych metryk podczas przebiegów szkoleniowych. Te metryki są rejestrowane i utrwalone w rekordzie przebiegu eksperymentu. Dane są następnie dostępne w dowolnym momencie lub wyświetlane na stronie szczegółów uruchamiania w [studio](https://ml.azure.com). Zobacz [odwołanie do](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation) pełnego zestawu metod `log_*()`rejestrowania .
* Skrypt szkoleniowy zapisuje model w katalogu o nazwie **dane wyjściowe**. Folder `./outputs` otrzymuje specjalne traktowanie przez usługę Azure ML. Podczas szkolenia pliki zapisane `./outputs` do są automatycznie przekazywane do rekordu uruchomienia przez usługę Azure ML i utrwalone jako artefakty. Zapisując przeszkolony model `./outputs`do programu , będziesz mieć dostęp do pliku modelu i pobierać go nawet po zakończeniu biegu i nie masz już dostępu do zdalnego środowiska szkoleniowego.

### <a name="create-an-estimator"></a>Tworzenie narzędzia do szacowania

Estymator usługi Azure ML hermetyzuje informacje o konfiguracji uruchamiania potrzebne do wykonywania skryptu szkoleniowego w celu obliczeniowym. Przebiegi usługi Azure ML są uruchamiane jako zadania konteneryzowane w określonym celu obliczeniowym. Domyślnie obraz platformy Docker utworzony dla zadania szkoleniowego będzie zawierać R, zestaw SDK usługi Azure ML i zestaw często używanych pakietów R. Zobacz pełną listę pakietów domyślnych zawartych tutaj.

Aby utworzyć estymator, należy zdefiniować:

* Katalog zawierający skrypty potrzebne do`source_directory`szkolenia ( ). Wszystkie pliki w tym katalogu są przekazywane do węzłów klastra do wykonania. Katalog musi zawierać skrypt szkolenia i wszelkie dodatkowe skrypty wymagane.
* Skrypt szkolenia, który zostanie`entry_script`wykonany ( ).
* Cel obliczeniowy`compute_target`( ), w tym przypadku klaster AmlCompute utworzony wcześniej.
* Parametry wymagane ze skryptu`script_params`szkoleniowego ( ). Usługa Azure ML uruchomi skrypt szkoleniowy jako `Rscript`skrypt wiersza polecenia z programem . W tym samouczku można określić jeden argument do skryptu, punkt `ds$path(target_path)`montażowy katalogu danych, które można uzyskać dostęp za pomocą programu .
* Wszelkie zależności środowiska wymagane do szkolenia. Domyślny obraz platformy Docker utworzony do`caret`szkolenia `e1071`zawiera `optparse`już trzy pakiety ( , i ) potrzebne w skrypcie szkolenia.  Nie trzeba więc podawać dodatkowych informacji. Jeśli używasz pakietów Języka R, które nie są domyślnie `cran_packages` uwzględniane, użyj parametru estymatora, aby dodać dodatkowe pakiety CRAN. Zobacz [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) punkt odniesienia dla pełnego zestawu konfigurowalnych opcji.

```R
est <- estimator(source_directory = ".",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>Przesyłanie zadania w klastrze zdalnym

Na koniec prześlij zadanie do uruchomienia w klastrze. `submit_experiment()`zwraca Run obiektu, który następnie używa się do interfejsu z run. Łącznie pierwszy przebieg trwa **około 10 minut**. Ale w przypadku późniejszych uruchomień ten sam obraz platformy Docker jest ponownie odtwarzany, o ile zależności skryptu nie zmieniają się.  W takim przypadku obraz jest buforowany, a czas uruchamiania kontenera jest znacznie szybszy.

```R
run <- submit_experiment(exp, est)
```

Szczegóły przebiegu można wyświetlić w przeglądarce RStudio Viewer. Kliknięcie łącza "Widok sieci Web" pod warunkiem spowoduje, że do usługi Azure Machine Learning studio, gdzie można monitorować przebieg w interfejsie użytkownika.

```R
view_run_details(run)
```

Szkolenie modelu odbywa się w tle. Poczekaj na zakończenie uczenia modelu przed uruchomieniem dalszego kodu.

```R
wait_for_run_completion(run, show_output = TRUE)
```

Ty — i współpracownicy z dostępem do obszaru roboczego — możesz przesyłać wiele eksperymentów równolegle, a usługa Azure ML podejmie planowanie zadań w klastrze obliczeniowym. Można nawet skonfigurować klaster do automatycznego skalowania do wielu węzłów i skalowania wstecz, gdy w kolejce nie ma więcej zadań obliczeniowych. Ta konfiguracja jest opłacalnym sposobem udostępniania zasobów obliczeniowych przez zespoły.

## <a name="retrieve-training-results"></a>Pobieranie wyników treningów
Po zakończeniu szkolenia modelu można uzyskać dostęp do artefaktów zadania, które zostały utrwalone do rekordu przebiegu, w tym wszelkie metryki rejestrowane i końcowego modelu przeszkolonego.

### <a name="get-the-logged-metrics"></a>Pobierz zarejestrowane dane
W skrypcie `accidents.R`szkolenia , rejestrowane metryki z modelu: dokładność prognoz w danych szkoleniowych. Możesz zobaczyć metryki w [studio](https://ml.azure.com)lub wyodrębnić je do sesji lokalnej jako listę R w następujący sposób:

```R
metrics <- get_run_metrics(run)
metrics
```

Jeśli przeprowadzono wiele eksperymentów (na przykład przy użyciu różnych zmiennych, algorytmów lub hiperparamerów), możesz użyć metryk z każdego uruchomienia, aby porównać i wybrać model, którego użyjesz w produkcji.

### <a name="get-the-trained-model"></a>Uzyskaj wyszkolony model
Można pobrać przeszkolony model i spojrzeć na wyniki w lokalnej sesji R. Poniższy kod pobierze zawartość `./outputs` katalogu, który zawiera plik modelu.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

Widzisz kilka czynników, które przyczyniają się do zwiększenia szacowanego prawdopodobieństwa śmierci:

* większa prędkość uderzenia 
* mężczyzna kierowca
* starszy pasażer
* Pasażerów

Widzisz mniejsze prawdopodobieństwo śmierci z:

* obecność poduszek powietrznych
* obecność pasów bezpieczeństwa
* zderzenie czołowe 

Rok produkcji pojazdu nie ma znaczącego wpływu.

Tego modelu można użyć do tworzenia nowych prognoz:

```R
newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=16,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

## predicted probability of death for these variables, as a percentage
as.numeric(predict(accident_model,newdata, type="response")*100)
```

## <a name="deploy-as-a-web-service"></a>Wdrażanie w postaci usługi internetowej

Dzięki modelowi możesz przewidzieć niebezpieczeństwo śmierci po kolizji. Użyj usługi Azure ML, aby wdrożyć model jako usługę przewidywania. W tym samouczku wdrożysz usługę sieci web w [instancjach kontenera platformy Azure](https://docs.microsoft.com/azure/container-instances/) (ACI).

### <a name="register-the-model"></a>Rejestrowanie modelu

Najpierw zarejestruj model pobrany do obszaru [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html)roboczego za pomocą programu . Zarejestrowany model może być dowolną kolekcją plików, ale w tym przypadku wystarczy obiekt modelu R. Usługa Azure ML użyje zarejestrowanego modelu do wdrożenia.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>Definiowanie zależności wnioskowania
Aby utworzyć usługę sieci web dla modelu, należy najpierw`entry_script`utworzyć skrypt oceniania ( ), skrypt R, który będzie przyjmować jako wartości zmiennych wejściowych (w formacie JSON) i wyprowadzać prognozowanie z modelu. W tym samouczku użyj `accident_predict.R`podanego pliku oceniania . Skrypt oceniania musi `init()` zawierać metodę, która ładuje model i zwraca funkcję, która używa modelu do przewidywania na podstawie danych wejściowych. Więcej informacji można znaleźć w [dokumentacji.](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details)

Następnie zdefiniuj **środowisko** usługi Azure ML dla zależności pakietu skryptu. W środowisku należy określić pakiety języka R (z CRAN lub gdzie indziej), które są potrzebne do uruchomienia skryptu. Można również podać wartości zmiennych środowiskowych, które skrypt może odwoływać się do modyfikowania jego zachowanie. Domyślnie usługa Azure ML będzie tworzyć ten sam domyślny obraz platformy Docker używany z estymatorem do szkolenia. Ponieważ samouczek nie ma specjalnych wymagań, utwórz środowisko bez specjalnych atrybutów.

```R
r_env <- r_environment(name = "basic_env")
```

Jeśli chcesz użyć własnego obrazu platformy Docker do `custom_docker_image` wdrożenia zamiast tego, należy określić parametr. Zobacz [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) odwołanie do pełnego zestawu konfigurowalnych opcji definiowania środowiska.

Teraz masz wszystko, czego potrzebujesz, aby utworzyć **konfigur wnioskowania** do hermetyzacji skryptu oceniającego i zależności środowiska.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>Wdrażanie w usłudze ACI
W tym samouczku wdrożysz usługę w usłudze ACI. Ten kod przepisuje pojedynczy kontener do odpowiadania na przychodzące żądania, który jest odpowiedni do testowania i lekkich obciążeń. Zobacz [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html) dodatkowe opcje konfigurowalne. (W przypadku wdrożeń na skalę produkcyjną można również [wdrożyć w usłudze Azure Kubernetes.)](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks/deploy-to-aks.html)

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

Teraz wdrażasz model jako usługę sieci web. Wdrożenie **może potrwać kilka minut**. 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>Testowanie wdrożonego modelu

Teraz, gdy model jest wdrożony jako usługa, można [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html)przetestować usługę z języka R przy użyciu .  Podaj nowy zestaw danych do przewidzenia, przekonwertuj je na JSON i wyślij do usługi.

```R
library(jsonlite)

newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=22,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

prob <- invoke_webservice(aci_service, toJSON(newdata))
prob
```

Można również uzyskać punkt końcowy HTTP usługi sieci web, który akceptuje wywołania klienta REST. Ten punkt końcowy można udostępnić każdemu, kto chce przetestować usługę internetową lub zintegrować ją z aplikacją.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Usuń zasoby, gdy nie są już potrzebne. Nie usuwaj żadnych zasobów, które chcesz nadal używać. 

Usuń usługę sieci web:
```R
delete_webservice(aci_service)
```

Usuń zarejestrowany model:
```R
delete_model(model)
```

Usuń klaster obliczeniowy:
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>Usuń wszystko

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* Po zakończeniu pierwszego eksperymentu usługi Azure Machine Learning w językach R dowiedz się więcej o [sdk usługi Azure Machine Learning dla języka R.](https://azure.github.io/azureml-sdk-for-r/index.html)

* Dowiedz się więcej o usłudze Azure Machine Learning z języka R na podstawie przykładów w innych *folderach winiet.*
