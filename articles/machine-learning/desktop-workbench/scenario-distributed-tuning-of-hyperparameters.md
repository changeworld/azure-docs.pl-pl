---
title: Rozproszone dostrajanie z Hyperparameters przy użyciu usługi Azure Machine Learning Workbench | Dokumentacja firmy Microsoft
description: W tym scenariuszu pokazano, jak to zrobić rozproszonej dostrojenie hyperparameters przy użyciu usługi Azure Machine Learning Workbench
services: machine-learning
author: pechyony
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.author: dmpechyo
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.date: 09/20/2017
ms.openlocfilehash: 70baa1160e9f521ac5533eaa15d4a9ab57433a49
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="distributed-tuning-of-hyperparameters-using-azure-machine-learning-workbench"></a>Rozproszone dostrojenie hyperparameters przy użyciu usługi Azure Machine Learning Workbench

W tym scenariuszu pokazano, jak używać usługi Azure Machine Learning Workbench do skalowania w poziomie dostrojenie hyperparameters algorytmów uczenia maszynowego, które implementują scikit — Dowiedz się interfejsu API. Zostanie przedstawiony sposób Konfigurowanie i używanie zdalnego kontenera Docker i klastra Spark dostrojenia hyperparameters jako wykonywania wewnętrznej bazy danych.

## <a name="link-of-the-gallery-github-repository"></a>Łącze repozytorium GitHub galerii
Poniżej znajduje się łącze do publicznego repozytorium GitHub: 

[https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)

## <a name="use-case-overview"></a>Omówienie przypadków użycia

Wiele algorytmów uczenia maszynowego ma co najmniej jeden pokrętła o nazwie hyperparameters. Te pokrętła Zezwalaj dostrajanie algorytmów w celu optymalizacji ich wydajności za pośrednictwem przyszłych danych mierzona w oparciu metryki określone przez użytkownika (na przykład dokładność AUC, RMSE). Naukowca danych musi podać wartości hyperparameters podczas konstruowania modelu za pośrednictwem dane szkoleniowe i dopiero po kilkukrotnym przyszłych testów. Jak oparte na może danych szkoleniowych znane skonfigurowanie wartości hyperparameters tak, aby modelu dobrą wydajność nad danymi nieznany testu? 
    
Popularne metoda dostrajania hyperparameters *wyszukiwania siatki* połączeniu z *krzyżowego sprawdzania poprawności*. Krzyżowe sprawdzanie poprawności to technika, który ocenia, jak model uczenia na zestaw szkoleniowy prognozuje wyznaczonych dla zestawu testów. Ta technika, możemy najpierw podzielić zestawu danych na złożeń K i następnie uczenia razy K algorytmu w działaniu okrężnym. Firma Microsoft to zrobić na wszystkich, ale jeden złożeń o nazwie "przechowywane poza złożenia". Średnia wartość metryki modeli K możemy obliczeniowego za pośrednictwem K przechowywane poza złożeń. Wartość średnia, nazywany *oszacowania wydajności krzyżowego sprawdzania poprawności*, zależy od wartości hyperparameters używany podczas tworzenia modeli K. Dostrajanie hyperparameters, możemy wyszukiwać za pośrednictwem obszaru candidate hyperparameter wartości, aby dowiedzieć się, że te, które optymalizacji wydajności krzyżowego sprawdzania poprawności oszacować. Wyszukiwanie siatki jest typowe techniki wyszukiwania. W wyszukiwaniu siatki miejsce candidate wartości wielu hyperparameters jest iloczyn wektorowy zestawów wartości candidate poszczególnych hyperparameters. 

Przy użyciu krzyżowego sprawdzania poprawności wyszukiwania siatki może być czasochłonne. Jeśli algorytm ma pięć hyperparameters z pięciu wartości kandydujących, używamy złożeń K = 5. Następnie wykonywania siatki wyszukiwanie według szkolenia 5<sup>6</sup>= 15625 modeli. Na szczęście siatki wyszukiwania przy użyciu krzyżowego sprawdzania poprawności jest embarrassingly równoległych procedury i może być uczony te modele równolegle.

## <a name="prerequisites"></a>Wymagania wstępne

* [Konta Azure](https://azure.microsoft.com/free/) (bezpłatnych wersji próbnych są dostępne).
* Zainstalowana kopia programu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) następujące [zainstalować i utworzyć szybkiego startu](../service/quickstart-installation.md) zainstalować Workbench i tworzenia kont.
* W tym scenariuszu przyjęto założenie, że Workbench uczenia Maszynowego Azure są uruchomione na systemu Windows 10 lub MacOS z aparatem platformy Docker zainstalowane lokalnie. 
* Do uruchomienia w scenariuszu z kontenerem Docker zdalnego, należy udostępnić Ubuntu danych nauki maszyny wirtualnej (DSVM), postępując [instrukcje](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Zaleca się używania maszyny wirtualnej z co najmniej 8 rdzeni i 28 Gb pamięci. D4 wystąpień maszyn wirtualnych ma takie wydajności. 
* Aby uruchomić ten scenariusz z klastrem Spark, wykonując te obsługi administracyjnej klastra Spark w usłudze HDInsight [instrukcje](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters). Zalecamy posiadanie zarówno nagłówka i proces roboczy węzłów klastra przy użyciu następującej konfiguracji:
    - czterech węzłów procesu roboczego
    - osiem rdzeni
    - 28 Gb pamięci  
      
  D4 wystąpień maszyn wirtualnych ma takie wydajności. 

     **Rozwiązywanie problemów z**: subskrypcji Your Azure może być limit przydziału liczby rdzeni, które mogą być używane. Azure portal pozwala na tworzenie klastra z łączną liczbą przekracza limit przydziału rdzeni. Aby znaleźć przydziału, przejdź w portalu Azure w sekcji subskrypcji, kliknij przycisk z subskrypcją użytą do wdrażania klastra, a następnie kliknij polecenie **użycia + przydziały**. Zazwyczaj przydziały są definiowane dla każdego regionu systemu Azure i są dostępne do wdrożenia klastra Spark w regionie, w którym masz wystarczająco dużo wolnego rdzeni. 

* Tworzenie konta magazynu Azure używanego do przechowywania zestawu danych. Postępuj zgodnie z [instrukcje](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) można utworzyć konta magazynu.

## <a name="data-description"></a>Opis elementu danych

Używamy [TalkingData dataset](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data). Ten zestaw danych nie ma zdarzenia z aplikacji w telefonów komórkowych. Celem jest przewidywanie kategorii płci i wiek użytkownika telefonu komórkowego danego typu telefonu i zdarzenia, które ostatnio wygenerowanym przez użytkownika.  

## <a name="scenario-structure"></a>Scenariusz — struktura
Ten scenariusz ma wiele folderów w repozytorium GitHub. Kod i pliki konfiguracyjne znajdują się w **kod** folder, cała dokumentacja znajduje się w **Docs** folder i wszystkie obrazy są **obrazów** folderu. Folder główny zawiera plik README, który zawiera krótkie podsumowanie tego scenariusza.

### <a name="getting-started"></a>Wprowadzenie
Kliknij ikonę usługi Azure Machine Learning Workbench uruchomić Azure Machine Learning Workbench i utworzyć projekt z szablonu "Rozproszonych dostrajanie z Hyperparameters". Możesz znaleźć szczegółowe instrukcje dotyczące sposobu tworzenia nowego projektu w [zainstalować i utworzyć szybkiego startu](quickstart-installation.md).   

### <a name="configuration-of-execution-environments"></a>Konfiguracja środowiska wykonania
Zostanie przedstawiony sposób uruchamiania naszego kodu w kontenerze Docker zdalnego i w klastrze Spark. Możemy zaczynać opis ustawień, które są wspólne dla obu środowiskach. 

Używamy [scikit — Dowiedz się](https://anaconda.org/conda-forge/scikit-learn), [xgboost](https://anaconda.org/conda-forge/xgboost), i [magazyn azure](https://pypi.python.org/pypi/azure-storage) pakiety, które nie znajdują się w domyślnym kontenerze Docker z usługi Azure Machine Learning Workbench. Magazyn Azure pakiet wymaga instalacji [kryptografii](https://pypi.python.org/pypi/cryptography) i [azure](https://pypi.python.org/pypi/azure) pakietów. Aby zainstalować te pakiety w kontenerze Docker i w węzłach klastra Spark, możemy zmodyfikować plik conda_dependencies.yml:

    name: project_environment
    channels:
      - conda-forge
    dependencies:
      - python=3.5.2
      - scikit-learn
      - xgboost
      - pip:
        - cryptography
        - azure
        - azure-storage

Zmodyfikowane conda\_dependencies.yml plik jest przechowywany w katalogu aml_config samouczka. 

W następnych krokach możemy nawiązać środowiska wykonawczego konto platformy Azure. Kliknij Menu Plik z lewym górnym rogu AML Workbench. I wybierz polecenie "Otwórz okno wiersza polecenia". Następnie uruchom w interfejsu wiersza polecenia

    az login

Pojawi się komunikat

    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code <code> to authenticate.

Przejdź do tej strony sieci web, wprowadź kod i zaloguj się do konta platformy Azure. Po wykonaniu tego kroku uruchom w interfejsu wiersza polecenia

    az account list -o table

i Znajdź konta AML Workbench roboczy ma identyfikator subskrypcji platformy Azure. Na koniec uruchom w interfejsu wiersza polecenia

    az account set -s <subscription ID>

Aby nawiązać połączenie z subskrypcją platformy Azure.

W dwóch następnych sekcjach zostanie przedstawiony sposób ukończenia konfiguracji zdalnego docker i klastra Spark.

#### <a name="configuration-of-remote-docker-container"></a>Konfiguracja zdalnego kontenera Docker

 Aby skonfigurować zdalne kontenera Docker, uruchom w interfejsu wiersza polecenia

    az ml computetarget attach remotedocker --name dsvm --address <IP address> --username <username> --password <password> 

z adresu IP adres, nazwę użytkownika i hasło w DSVM. Adres IP DSVM można znaleźć w sekcji Przegląd strony DSVM w portalu Azure:

![IP MASZYNY WIRTUALNEJ](media/scenario-distributed-tuning-of-hyperparameters/vm_ip.png)

#### <a name="configuration-of-spark-cluster"></a>Konfiguracja klastra Spark

Aby skonfigurować środowisko Spark, uruchom w interfejsu wiersza polecenia

    az ml computetarget attach cluster --name spark --address <cluster name>-ssh.azurehdinsight.net  --username <username> --password <password> 

z nazwą klastra, nazwa użytkownika SSH klastra i hasło. Wartość domyślna nazwa użytkownika SSH jest `sshuser`, chyba że zostanie zmienione podczas inicjowania obsługi klastra. Nazwa klastra można znaleźć w sekcji właściwości strony klastra w portalu Azure:

![Nazwa klastra](media/scenario-distributed-tuning-of-hyperparameters/cluster_name.png)

Używamy spark sklearn pakietu na środowiska wykonawczego dla rozproszonych dostrojenie hyperparameters Spark. Firma Microsoft zmodyfikowany plik spark_dependencies.yml, aby zainstalować ten pakiet, gdy jest używana platforma Spark środowiska wykonawczego:

    configuration: 
      #"spark.driver.cores": "8"
      #"spark.driver.memory": "5200m"
      #"spark.executor.instances": "128"
      #"spark.executor.memory": "5200m"  
      #"spark.executor.cores": "2"
  
    repositories:
      - "https://mmlspark.azureedge.net/maven"
      - "https://spark-packages.org/packages"
    packages:
      - group: "com.microsoft.ml.spark"
        artifact: "mmlspark_2.11"
        version: "0.7.91"
      - group: "databricks"
        artifact: "spark-sklearn"
        version: "0.2.0"

Zmodyfikowane spark\_dependencies.yml plik jest przechowywany w katalogu aml_config samouczka. 

### <a name="data-ingestion"></a>Wprowadzanie danych
Kod w tym scenariuszu przyjęto założenie, że dane są przechowywane w magazynie obiektów blob platformy Azure. Firma Microsoft początkowo przedstawiają sposób pobierania danych z lokacji Kaggle do komputera i przekaż go do magazynu obiektów blob. Następnie zostanie przedstawiony sposób odczytać danych z magazynu obiektów blob. 

Aby pobrać dane z Kaggle, przejdź do [strony zestawu danych](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data) i kliknij przycisk Pobierz. Użytkownik jest proszony o zalogować się do Kaggle. Po zalogowaniu nastąpi przekierowanie do strony zestawu danych. Następnie pobierają pliki najpierw siedmiu w lewej kolumnie, zaznaczając je i klikając przycisk Pobierz. Całkowity rozmiar pobieranych plików jest 289 Mb. Aby przekazać pliki do magazynu obiektów blob, utworzyć kontener magazynu obiektów blob "zestawu danych" na koncie magazynu. Możesz to zrobić, przechodząc do strony systemu Azure, Twoje konto magazynu, klikając pozycję obiekty BLOB, a następnie klikając pozycję + kontenera. Wprowadź "zestawu danych" jako nazwy, a następnie kliknij przycisk OK. Poniższe zrzuty ekranu przedstawiają następujące kroki:

![Otwórz blob](media/scenario-distributed-tuning-of-hyperparameters/open_blob.png)
![Otwórz kontenera](media/scenario-distributed-tuning-of-hyperparameters/open_container.png)

Po wykonaniu tej Wybierz kontener zestawu danych z listy i kliknij przycisk przesyłania. Azure portal umożliwia przekazywania wielu plików jednocześnie. W sekcji "Przekazania obiektu blob" kliknij przycisk folderu, wybierz wszystkie pliki z zestawu danych, kliknij przycisk Otwórz, a następnie kliknij przekazywania. Poniższy zrzut ekranu przedstawia następujące kroki:

![Przekazywanie obiektu blob](media/scenario-distributed-tuning-of-hyperparameters/upload_blob.png) 

Przekazywanie plików może zająć kilka minut, w zależności od połączenia internetowego. 

W naszym kodzie używamy [zestawu SDK usługi Magazyn Azure](https://azure-storage.readthedocs.io/en/latest/) pobierania zestawu danych z magazynu obiektów blob do aktualnego środowiska wykonawczego. Pobieranie jest wykonywane w obciążenia\_funkcja funkcji data() z pliku load_data.py. Aby użyć tego kodu, należy zastąpić < nazwa_konta > i < ACCOUNT_KEY > o nazwie i klucz podstawowy konta magazynu, który jest hostem zestawu danych. Widać nazwy konta w lewym górnym rogu strony systemu Azure dla konta magazynu. Można uzyskać konta typu klucza, wybierz klucze dostępu Azure stronie magazynu konta, (Zobacz pierwszy zrzut ekranu w sekcji wprowadzanie danych), a następnie skopiuj długi ciąg w pierwszym wierszu kolumny klucza:
 
![Klucz dostępu](media/scenario-distributed-tuning-of-hyperparameters/access_key.png)

Poniższy kod z funkcji load_data() pobiera pojedynczy plik:

    from azure.storage.blob import BlockBlobService

    # Define storage parameters 
    ACCOUNT_NAME = "<ACCOUNT_NAME>"
    ACCOUNT_KEY = "<ACCOUNT_KEY>"
    CONTAINER_NAME = "dataset"

    # Define blob service     
    my_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # Load blob
    my_service.get_blob_to_path(CONTAINER_NAME, 'app_events.csv.zip', 'app_events.csv.zip')

Zwróć uwagę, że nie trzeba ręcznie uruchom plik load_data.py. Jest ona wywoływana z innych plików na dalszym etapie.

### <a name="feature-engineering"></a>Inżynieria funkcji
Kod dla przetwarzania wszystkich funkcji znajduje się w funkcji\_engineering.py pliku. Nie trzeba ręcznie uruchom plik feature_engineering.py. Później będzie ona wywoływana z innych plików.

Utworzymy wiele zestawów funkcji:
* Kodowanie hot jeden marki i modelu telefonu komórkowego (jeden\_gorących\_brand_model funkcja)
* Część zdarzenia generowane przez użytkownika w każdego dnia tygodnia (dzień tygodnia\_hour_features funkcja)
* Część zdarzenia generowane przez użytkownika w ciągu każdej godziny (dzień tygodnia\_hour_features funkcja)
* Część zdarzenia generowane przez użytkownika w każdej kombinacji dzień tygodnia i godzinę (dzień tygodnia\_hour_features funkcja)
* Część zdarzenia generowane przez użytkownika w każdej aplikacji (co\_gorących\_app_labels funkcja)
* Część zdarzenia generowane przez użytkownika w każdej etykiety aplikacji (co\_gorących\_app_labels funkcja)
* Część zdarzenia generowane przez użytkownika w każdej kategorii aplikacji (tekst\_category_features funkcja)
* Wskaźnik funkcji dla kategorii aplikacji, które były używane do wygenerowanych zdarzeń (jeden\_hot_category funkcja)

Te funkcje zostały inspirowana przez jądro Kaggle [model liniowy na aplikacje i etykiety](https://www.kaggle.com/dvasyukova/a-linear-model-on-apps-and-labels).

Obliczenia tych funkcji wymaga dużo pamięci. Początkowo próby obliczeniowe funkcji w środowisku lokalnym z 16 GB pamięci RAM. Firma Microsoft było obliczeniowe pierwsze cztery zestawy funkcji, ale Odebrano błąd "Brak pamięci" podczas obliczania piątej zestaw funkcji. Obliczenia zestawy funkcji pierwsze cztery znajduje się w pliku singleVMsmall.py i mogą być wykonywane w środowisku lokalnym, uruchamiając 

     az ml experiment submit -c local .\singleVMsmall.py   

w oknie interfejsu wiersza polecenia.

Ponieważ lokalne środowisko jest za mały dla przetwarzania się, że wszystkie zestawy funkcji, możemy Przełącz się do DSVM zdalnego, z większą pamięcią. Wykonanie wewnątrz DSVM odbywa się w kontenerze Docker, który jest zarządzany przez AML Workbench. Za pomocą tego DSVM możemy obliczeniowe wszystkich funkcji i modeli uczenia i dostrajania hyperparameters (zobacz następną sekcję). Plik singleVM.py ma obliczania funkcji pełne i modelowanie kodu. W następnej sekcji zostanie zostanie przedstawiony sposób uruchamiania singleVM.py w DSVM zdalnego. 

### <a name="tuning-hyperparameters-using-remote-dsvm"></a>Dostrajanie hyperparameters przy użyciu zdalnego DSVM
Używamy [xgboost](https://anaconda.org/conda-forge/xgboost) zwiększania gradientu drzewa wykonania [1]. Korzystamy również [scikit — Dowiedz się](http://scikit-learn.org/) pakietu, aby dostroić hyperparameters xgboost. Mimo że xgboost nie jest częścią scikit — Dowiedz się pakiet, implementuje scikit — Dowiedz się interfejsu API i dlatego mogą być używane razem z hyperparameter dostrajania funkcji scikit — Dowiedz się więcej. 

Xgboost ma osiem hyperparameters, opisane [tutaj](https://github.com/dmlc/xgboost/blob/master/doc/parameter.md):
* n_estimators
* max_depth
* reg_alpha
* reg_lambda
* colsample\_by_tree
* learning_rate
* colsample\_by_level
* podpróbki
* Cel  
 
Początkowo możemy użyć zdalnego DSVM i dostrajania hyperparameters z małych siatki candidate wartości:

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1],'learning_rate': [0.1], 'colsample_bylevel': [0.1,], 'subsample': [0.5]}]  

Ta siatka ma cztery kombinacje wartości hyperparameters. Używamy 5-fold krzyżowego sprawdzania poprawności co 4 x 5 = 20 działa z xgboost. Do pomiaru wydajności modeli, używamy pomiar utraty ujemna dziennika. Poniższy kod umożliwia znalezienie wartości hyperparameters z siatki, które zmaksymalizować utraty krzyżowego sprawdzania poprawności ujemna dziennika. Kod używa także te wartości do nauczenia modelu końcowego za pośrednictwem zestawu szkoleniowego pełnej:

    clf = XGBClassifier(seed=0)
    metric = 'neg_log_loss'
    
    clf_cv = GridSearchCV(clf, tuned_parameters, scoring=metric, cv=5, n_jobs=8)
    model = clf_cv.fit(X_train,y_train)

Po utworzeniu modelu, możemy zapisać wyniki dostrajanie hyperparameter. Używamy rejestrowania interfejsu API AML Workbench można zapisać wartości najlepsze hyperparameters i odpowiednie krzyżowego sprawdzania poprawności oszacowanie utraty ujemna dziennika:

    from azureml.logging import get_azureml_logger

    # initialize logger
    run_logger = get_azureml_logger()

    ...

    run_logger.log(metric, float(clf_cv.best_score_))

    for key in clf_cv.best_params_.keys():
        run_logger.log(key, clf_cv.best_params_[key]) 

Możemy także utworzyć plik sweeping_results.txt z dziennika krzyżowego sprawdzania poprawności, ujemna strat wszystkich kombinacji wartości hyperparameter w siatce.

    if not path.exists('./outputs'):
        makedirs('./outputs')
    outfile = open('./outputs/sweeping_results.txt','w')

    print("metric = ", metric, file=outfile)
    for i in range(len(model.grid_scores_)):
        print(model.grid_scores_[i], file=outfile)
    outfile.close()

Ten plik jest przechowywany w specjalny. / danych wyjściowych katalogu. Później zostanie przedstawiony sposób go pobrać.  

 Przed uruchomieniem singleVM.py w zdalnym DSVM po raz pierwszy, utworzymy uruchamiając istnieje kontener Docker 

    az ml experiment prepare -c dsvm

Interfejs wiersza polecenia systemu Windows. Tworzenie Docker kontener może zająć kilka minut. Następnie przeprowadzana w DSVM singleVM.py:

    az ml experiment submit -c dsvm .\singleVM.py

To polecenie zakończy się w 1 godzina 38 minut po DSVM ma 8 rdzeni i 28 Gb pamięci. W oknie Uruchamianie historii AML Workbench można wyświetlić zarejestrowane wartości:

![Historia uruchomień](media/scenario-distributed-tuning-of-hyperparameters/run_history.png)

Domyślnie okno Uruchom historii zawiera wartości i wykresy pierwszego zarejestrowane wartości 1 i 2. Aby zapoznać się z pełną listą wybrane wartości hyperparameters, kliknij ikonę ustawienia oznaczonej jako czerwone kółko na poprzednim zrzucie ekranu. Następnie wybierz hyperparameters ma być wyświetlany w tabeli. Ponadto wybierz wykresy, które są wyświetlane w górnej części okna Uruchom historii, kliknij ikonę ustawienia oznaczonej jako niebieskie koło i wybierz wykresów z listy. 

Wybrane wartości hyperparameters można zbadać w taki sposób, w oknie właściwości Uruchom: 

![Uruchomienie właściwości](media/scenario-distributed-tuning-of-hyperparameters/run_properties.png)

W prawym górnym rogu okna właściwości Uruchom jest sekcji plików wyjściowych z listą wszystkich plików, które zostały utworzone w ". \output" folderu. zmiennymi\_results.txt można pobrać z tego miejsca, wybierając ją i klikając przycisk Pobierz. sweeping_results.txt powinny mieć następujące dane wyjściowe:

    metric =  neg_log_loss
    mean: -2.29096, std: 0.03748, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28712, std: 0.03822, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28706, std: 0.03863, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}
    mean: -2.28530, std: 0.03927, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}

### <a name="tuning-hyperparameters-using-spark-cluster"></a>Dostrajanie hyperparameters przy użyciu klastra Spark
Używamy klastra Spark do skalowania w poziomie dostrajanie hyperparameters i użyj siatki większy. Jest naszej nowej siatki

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0.1], 'colsample_bylevel': [0.01, 0.1], 'subsample': [0.5, 0.7]}]

Ta siatka ma 16 kombinacje wartości hyperparameters. Ponieważ używamy 5-fold krzyżowego sprawdzania poprawności przeprowadzana xgboost 16 x 5 = 80 razy.

scikit — Dowiedz się pakiet nie ma natywnej obsługi programu Dostrajanie hyperparameters przy użyciu klastra Spark. Na szczęście [spark sklearn](https://spark-packages.org/package/databricks/spark-sklearn) pakietu z Databricks wypełnia luki. Ten pakiet zawiera funkcję GridSearchCV, która ma prawie tego samego interfejsu API funkcji GridSearchCV w scikit — Dowiedz się więcej. Użyj spark sklearn i dostrajania hyperparameters przy użyciu Spark, należy utworzyć w kontekście Spark

    from pyspark import SparkContext
    sc = SparkContext.getOrCreate()

Następnie możemy zastąpić 

    from sklearn.model_selection import GridSearchCV

z  

    from spark_sklearn import GridSearchCV

Ponadto możemy Zastąp wywołanie GridSearchCV z scikit — Dowiedz się, jak jeden z spark sklearn:

    clf_cv = GridSearchCV(sc = sc, param_grid = tuned_parameters, estimator = clf, scoring=metric, cv=5)

Końcowe kod dostrajanie hyperparameters przy użyciu platformy Spark w dystrybucji\_sweep.py pliku. Różnica między singleVM.py i distributed_sweep.py jest definicji siatki i cztery wiersze kodu. Zwróć uwagę, że z powodu AML Workbench usług, kod rejestrowania nie ulega zmianie, gdy zmiana środowiska wykonawczego z DSVM zdalnego do klastra Spark.

Przed uruchomieniem distributed_sweep.py w klastrze Spark po raz pierwszy, należy zainstalować pakiety języka Python istnieje. Można to osiągnąć, uruchamiając 

    az ml experiment prepare -c spark

Interfejs wiersza polecenia systemu Windows. Ta instalacja może zająć kilka minut. Następnie przeprowadzana distributed_sweep.py w klastrze Spark:

    az ml experiment submit -c spark .\distributed_sweep.py

To polecenie zakończy się w 1 godzina 6 minut po klastra Spark ma 6 węzłów procesu roboczego z 28 Gb pamięci. Dostrajanie hyperparameter wyniki są dostępne w konsoli usługi Azure Machine Learning Workbench taki sam sposób jak zdalne wykonywanie DSVM. (to znaczy dzienniki, najlepsze wartości hyperparameters i pliku sweeping_results.txt)

### <a name="architecture-diagram"></a>Diagram architektury

Na poniższym diagramie przedstawiono ogólny przepływ pracy: ![architektury](media/scenario-distributed-tuning-of-hyperparameters/architecture.png) 

## <a name="conclusion"></a>Podsumowanie 

W tym scenariuszu firma Microsoft pokazano, jak używać usługi Azure Machine Learning Workbench do dostrojenie hyperparameters w zdalnym maszyn wirtualnych i klastry Spark. Widzieliśmy się, że usługi Azure Machine Learning Workbench udostępnia narzędzia, aby ułatwić konfigurację środowiska wykonywania. Umożliwia także łatwe przełączenie między nimi. 

## <a name="references"></a>Dokumentacja

[1] i C. Guestrin T. Chen. [XGBoost: Skalowalne drzewa zwiększania systemu](https://arxiv.org/abs/1603.02754). KDD 2016.




