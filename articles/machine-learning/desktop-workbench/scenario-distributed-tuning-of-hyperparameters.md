---
title: Rozproszone Dostosowywanie z Hiperparametrów przy użyciu usługi Azure Machine Learning Workbench | Dokumentacja firmy Microsoft
description: W tym scenariuszu pokazano, jak to zrobić, rozproszone Dostosowywanie hiperparametrów, za pomocą usługi Azure Machine Learning Workbench
services: machine-learning
author: pechyony
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.author: dmpechyo
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.date: 09/20/2017
ROBOTS: NOINDEX
ms.openlocfilehash: f74889cdf727bc132723d16df295849769001ce9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951971"
---
# <a name="distributed-tuning-of-hyperparameters-using-azure-machine-learning-workbench"></a>Rozproszone Dostosowywanie hiperparametrów przy użyciu usługi Azure Machine Learning Workbench

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



W tym scenariuszu pokazano, jak używać usługi Azure Machine Learning Workbench do skalowania w poziomie Dostosowywanie hiperparametrów algorytmów uczenia maszynowego, które implementują scikit-Dowiedz się, interfejsu API. Przedstawiono sposób konfigurowania i używania zdalnym kontenerze platformy Docker i klaster Spark jako zaplecza wykonywania dostrojenia hiperparametrów.

## <a name="link-of-the-gallery-github-repository"></a>Link repozytorium GitHub galerii
Oto link do publicznego repozytorium GitHub: 

[https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)

## <a name="use-case-overview"></a>Omówienie przypadków użycia

Wiele algorytmów uczenia maszynowego ma co najmniej jeden pokrętła, o nazwie hiperparametrów. Te pokrętła umożliwia dostrajanie algorytmów w celu optymalizowania wydajności za pośrednictwem przyszłych danych, mierzona w oparciu o metryki określonych przez użytkownika (na przykład, dokładności, AUC, RMSE). Analityk danych musi podać wartości hiperparametrów, podczas tworzenia modelu danych szkoleniowych i przed wyświetlaniem danych przyszłych testów. Jak zależnie od znanych szkolenia mogą danych skonfigurujemy wartości hiperparametrów tak, aby model nie ma dobrej wydajności danych nieznany testu? 
    
To popularne technika Dostosowywanie hiperparametrów *wyszukiwania siatki* w połączeniu z *krzyżowego sprawdzania poprawności*. Krzyżowa Weryfikacja to technika, która ocenia, jak model jest uczony na zestaw szkoleniowy przewiduje zestawu testów. Korzystając z tej techniki, firma Microsoft najpierw podzielić zestawu danych na złożeń K i następnie uczenie algorytmu czasu K w działaniu okrężnym. Możemy to zrobić na wszystkich, ale jeden z złożeń o nazwie "zwijania przechowywane poza". Firma Microsoft obliczeń średnia wartość metryki K modele za pośrednictwem K przechowywane poza złożeń. Ta wartość średnią, o nazwie *szacowania wydajności zweryfikowane dla wielu*, zależy od wartości hiperparametrów użytą podczas tworzenia modeli K. Podczas dostosowywania hiperparametrów, firma Microsoft Przeszukaj miejsce Release candidate hiperparametrycznego wartości, aby znaleźć oszacować te, które zoptymalizować wydajność krzyżowego sprawdzania poprawności. Wyszukiwanie siatki jest typową techniką wyszukiwania. W wyszukiwaniu siatki miejsce Release candidate wartości wielu hiperparametrów jest iloczyn wektorowy zestawy wartości Release candidate poszczególnych hiperparametrów. 

Wyszukiwanie w siatce, używając krzyżowego sprawdzania poprawności może być czasochłonne. Jeśli algorytm ma pięć hiperparametrów z pięciu wartości Release candidate, używamy złożeń KB = 5. Następnie zakończymy siatki wyszukiwanie według szkolenia 5<sup>6</sup>= 15625 modeli. Na szczęście siatki wyszukiwania przy użyciu krzyżowego sprawdzania poprawności jest zaskakująco równoległymi procedury i te modele mogą odbywać się równolegle.

## <a name="prerequisites"></a>Wymagania wstępne

* [Konta platformy Azure](https://azure.microsoft.com/free/) (bezpłatne wersje próbne są dostępne).
* Zainstalowana kopia programu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) następujące [Instalowanie i Tworzenie szybkiego startu](quickstart-installation.md) do zainstalowania aplikacji Workbench, a następnie utwórz konta.
* W tym scenariuszu przyjęto założenie, że działasz aplikacji Azure ML Workbench w systemie Windows 10 lub MacOS przy użyciu aparatu platformy Docker zainstalowany lokalnie. 
* Można uruchomić scenariusza z zdalnym kontenerze platformy Docker, aprowizować Ubuntu danych do analizy maszyny wirtualnej (DSVM), postępując zgodnie z [instrukcje](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Firma Microsoft zaleca używanie maszyny wirtualnej z co najmniej 8 rdzeni i 28 Gb pamięci. D4 wystąpień maszyn wirtualnych mają takie pojemności. 
* Aby uruchomić ten scenariusz przy użyciu klastra Spark, inicjowania obsługi klastra HDInsight Spark wykonując te [instrukcje](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters). Zaleca się posiadanie klastra przy użyciu następującej konfiguracji w węzłach zarówno nagłówka, jak i procesu roboczego:
    - cztery węzły procesu roboczego
    - osiem rdzeni
    - 28 Gb pamięci  
      
  D4 wystąpień maszyn wirtualnych mają takie pojemności. 

     **Rozwiązywanie problemów z**: Twoja subskrypcja platformy Azure może mieć limit przydziału liczby rdzeni, których można użyć. Witryna Azure portal nie zezwala na tworzenie klastra z łączną liczbą rdzeni przekroczenie limitu przydziału. Aby odnaleźć limitu przydziału, przejdź w witrynie Azure portal w sekcji subskrypcji, kliknij subskrypcję, używany do wdrażania klastra, a następnie kliknij polecenie **użycie i przydziały**. Zazwyczaj przydziały są definiowane na region platformy Azure i można wybrać wdrożyć klaster Spark w regionie, w którym masz wystarczającej liczby wolnych rdzeni. 

* Utwórz konto usługi Azure storage używanego do przechowywania zestawu danych. Postępuj zgodnie z [instrukcje](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) można utworzyć konta magazynu.

## <a name="data-description"></a>Opis danych

Używamy [TalkingData dataset](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data). Ten zestaw danych zawiera zdarzenia z aplikacji w telefony komórkowe. Celem jest przewidzieć płeć i grupa wiekowa telefonu komórkowego użytkownika dla danego typu telefonu i zdarzenia, które ostatnio wygenerowanym przez użytkownika.  

## <a name="scenario-structure"></a>Struktura scenariusza
Ten scenariusz ma wiele folderów w repozytorium GitHub. Kod i pliki konfiguracyjne znajdują się w **kodu** folder, całą dokumentację znajduje się w **Docs** folder i wszystkie obrazy są **obrazów** folderu. Folder główny zawiera plik README, który zawiera krótkie podsumowanie tego scenariusza.

### <a name="getting-started"></a>Wprowadzenie
Kliknij ikonę aplikacji Azure Machine Learning Workbench do uruchomienia aplikacji Azure Machine Learning Workbench i Utwórz projekt z szablonu "Rozproszone Dostosowywanie z Hiperparametrów". Można znaleźć szczegółowe instrukcje dotyczące sposobu tworzenia nowego projektu w [Instalowanie i Tworzenie szybkiego startu](quickstart-installation.md).   

### <a name="configuration-of-execution-environments"></a>Konfiguracja środowiska wykonawcze
Pokazujemy, jak uruchomić naszego kodu w zdalnym kontenerze platformy Docker i w klastrze Spark. Rozpoczniemy pracę opis ustawień, które są wspólne dla obu środowiskach. 

Używamy [scikit-informacje](https://anaconda.org/conda-forge/scikit-learn), [xgboost](https://anaconda.org/conda-forge/xgboost), i [usługi azure storage](https://pypi.python.org/pypi/azure-storage) pakiety, które nie znajdują się w domyślnym kontenerze platformy Docker z usługi Azure Machine Learning Workbench. Pakiet Azure-storage wymaga instalacji [kryptografii](https://pypi.python.org/pypi/cryptography) i [azure](https://pypi.python.org/pypi/azure) pakietów. Aby zainstalować te pakiety w kontenerze platformy Docker i w węzłach klastra Spark, możemy zmodyfikować plik conda_dependencies.yml:

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

Zmodyfikowane conda\_dependencies.yml plik jest przechowywany w katalogu aml_config części samouczka. 

W następnych krokach łączymy środowiska wykonawczego na koncie platformy Azure. Kliknij Menu Plik w lewym górnym rogu aplikacji AML Workbench. I wybierz polecenie "Otwórz wiersz polecenia". Następnie uruchom w interfejsie wiersza polecenia

    az login

Zostanie wyświetlony komunikat

    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code <code> to authenticate.

Przejdź do tej strony sieci web, wprowadź kod, a następnie zaloguj się do konta platformy Azure. Po wykonaniu tego kroku uruchom w interfejsie wiersza polecenia

    az account list -o table

i znaleźć Identyfikatora subskrypcji platformy Azure, który ma konto usługi AML aplikacji Workbench w obszarze roboczym. Na koniec uruchom aplikację w interfejsie wiersza polecenia

    az account set -s <subscription ID>

Aby nawiązać połączenie z subskrypcją platformy Azure.

W dwóch następnych sekcjach przedstawiono, jak przeprowadzić konfigurację zdalnego platformy docker i klaster Spark.

#### <a name="configuration-of-remote-docker-container"></a>Konfiguracja w zdalnym kontenerze platformy Docker

 Aby skonfigurować zdalnym kontenerze platformy Docker, uruchom w interfejsie wiersza polecenia

    az ml computetarget attach remotedocker --name dsvm --address <IP address> --username <username> --password <password> 

za pomocą adresu IP adres, nazwę użytkownika i hasło w nauki. Adres IP maszyny wirtualnej DSVM można znaleźć w sekcji Przegląd DSVM strony w witrynie Azure portal:

![Adres IP maszyny wirtualnej](media/scenario-distributed-tuning-of-hyperparameters/vm_ip.png)

#### <a name="configuration-of-spark-cluster"></a>Konfiguracja klastra Spark

Aby skonfigurować środowisko Spark, uruchom w interfejsie wiersza polecenia

    az ml computetarget attach cluster --name spark --address <cluster name>-ssh.azurehdinsight.net  --username <username> --password <password> 

nazwą klastra, nazwa użytkownika SSH klastra i hasło. Wartość domyślna nazwa użytkownika SSH jest `sshuser`, chyba że został zmieniony podczas inicjowania obsługi klastra. Nazwa klastra można znaleźć w sekcji właściwości strony klastra w witrynie Azure portal:

![Nazwa klastra](media/scenario-distributed-tuning-of-hyperparameters/cluster_name.png)

Używamy pakietu skryptu sklearn spark mieć Spark jako środowisko wykonawcze dla rozproszone Dostosowywanie hiperparametrów. Zmodyfikowany plik spark_dependencies.yml, aby zainstalować ten pakiet, gdy jest używane środowisko wykonawcze Spark:

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

Zmodyfikowane spark\_dependencies.yml plik jest przechowywany w katalogu aml_config części samouczka. 

### <a name="data-ingestion"></a>Wprowadzanie danych
Kod w tym scenariuszu przyjęto założenie, że dane są przechowywane w usłudze Azure blob storage. Początkowo przedstawiono, jak pobrać dane z witryny Kaggle do komputera i przekaż go do magazynu obiektów blob. Następnie przedstawiono, jak odczytywać dane z magazynu obiektów blob. 

Aby pobrać dane z Kaggle, przejdź do [strona zestawu danych](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data) i kliknij przycisk Pobierz. Użytkownik jest proszony o Zaloguj się do Kaggle. Po zalogowaniu nastąpi przekierowanie do strony zestawu danych. Następnie Pobierz pierwszych siedem plików znajdujących się w lewej kolumnie, wybierając je i klikając przycisk Pobierz. Całkowity rozmiar pobieranych plików to 289 Mb. Aby przekazać te pliki do magazynu obiektów blob, Tworzenie kontenera magazynu obiektów blob "zestawu danych" na koncie magazynu. Możesz tworzyć, przechodząc do strony platformy Azure z Twojego konta magazynu, klikając pozycję obiekty BLOB, a następnie klikając pozycję + kontener. Wprowadź nazwę "zestaw danych", a następnie kliknij przycisk OK. Poniższe zrzuty ekranu pokazują następujące kroki:

![Otwórz obiekt blob](media/scenario-distributed-tuning-of-hyperparameters/open_blob.png)
![Open container](media/scenario-distributed-tuning-of-hyperparameters/open_container.png)

Po tym z listy Wybierz kontener dla zestawu danych i kliknij przycisk Przekaż. Witryna Azure portal można przekazać wiele plików jednocześnie. W sekcji "Przekazywanie obiektu blob" kliknij przycisk folder, zaznacz wszystkie pliki z zestawu danych, kliknij przycisk Otwórz, a następnie kliknij przekazywania. Poniższy zrzut ekranu przedstawia następujące kroki:

![Przekazywanie obiektu blob](media/scenario-distributed-tuning-of-hyperparameters/upload_blob.png) 

Przekazywanie plików trwa kilka minut, w zależności od połączenia internetowego. 

W naszym kodzie użyto [zestawu SDK usługi Azure Storage](https://docs.microsoft.com/python/azure/) pobierania zestawu danych z magazynu obiektów blob do aktualnego środowiska wykonawczego. Pliki do pobrania jest wykonywane w obciążenia\_funkcji data() z pliku load_data.py. Aby użyć tego kodu, musisz zastąpić < ACCOUNT_NAME > i < ACCOUNT_KEY > przy użyciu nazwy i klucza podstawowego konta magazynu, który jest hostem zestawu danych. Widać nazwę konta w lewym górnym rogu strony Azure konta magazynu. Można uzyskać konta klucza, wybierz pozycję klucze dostępu stronie w witrynie Azure Storage account (Zobacz pierwszy zrzut ekranu w sekcji pozyskiwanie danych) i następnie skopiuj długi ciąg w pierwszym wierszu kolumny klucza:
 
![Klucz dostępu](media/scenario-distributed-tuning-of-hyperparameters/access_key.png)

Poniższy kod w funkcji load_data() do pobrania jednego pliku:

    from azure.storage.blob import BlockBlobService

    # Define storage parameters 
    ACCOUNT_NAME = "<ACCOUNT_NAME>"
    ACCOUNT_KEY = "<ACCOUNT_KEY>"
    CONTAINER_NAME = "dataset"

    # Define blob service     
    my_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # Load blob
    my_service.get_blob_to_path(CONTAINER_NAME, 'app_events.csv.zip', 'app_events.csv.zip')

Należy zauważyć, że nie trzeba ręcznie uruchomić load_data.py pliku. Jest ona wywoływana z innych plików później.

### <a name="feature-engineering"></a>Inżynieria funkcji
Kod do przetwarzania wszystkich funkcji znajduje się w funkcji\_engineering.py pliku. Nie trzeba ręcznie uruchomić feature_engineering.py pliku. Później będzie ona wywoływana z innych plików.

Możemy utworzyć wiele zestawów funkcji:
* Kodowanie hot jeden marki i modelu telefonu komórkowego (jeden\_gorąca\_brand_model funkcji)
* Ułamek zdarzeń generowanych przez użytkownika w każdy dzień tygodnia (dzień tygodnia\_hour_features funkcji)
* Ułamek zdarzeń generowanych przez użytkownika w ciągu każdej godziny (dzień tygodnia\_hour_features funkcji)
* Ułamek zdarzeń generowanych przez użytkownika w każdej kombinacji dzień tygodnia i godzinę (dzień tygodnia\_hour_features funkcji)
* Ułamek zdarzeń generowanych przez użytkownika w każdej aplikacji (co\_gorąca\_app_labels funkcji)
* Ułamek zdarzeń generowanych przez użytkownika w każdej etykiety aplikacji (co\_gorąca\_app_labels funkcji)
* Ułamek zdarzeń generowanych przez użytkownika w każdej kategorii aplikacji (tekst\_category_features funkcji)
* Wskaźnik funkcji kategorie aplikacji, które były używane do wygenerowanych zdarzeń (jeden\_hot_category funkcji)

Te funkcje zostały ZAINSPIROWANE przez jądro Kaggle [modelu liniowego na aplikacje i etykiety](https://www.kaggle.com/dvasyukova/a-linear-model-on-apps-and-labels).

Obliczenia z tych funkcji wymaga znacznej ilości pamięci. Początkowo próby obliczenia funkcji w środowisku lokalnym za pomocą 16 GB pamięci RAM. Firma Microsoft udało się obliczyć pierwsze cztery zestawy funkcji, ale wystąpił błąd podczas "za mało pamięci" podczas obliczania piąty zestawu funkcji. Obliczenie zestawy funkcji pierwsze cztery znajduje się w pliku singleVMsmall.py i mogą być wykonywane w środowisku lokalnym, uruchamiając 

     az ml experiment submit -c local .\singleVMsmall.py   

w oknie interfejsu wiersza polecenia.

Ponieważ środowisko lokalne jest zbyt mała do przetwarzania danych, że wszystkie zestawy funkcji, możemy przełączyć się do zdalnego maszyny wirtualnej DSVM, który ma więcej pamięci. Wykonanie wewnątrz maszyny wirtualnej DSVM odbywa się w kontenerze platformy Docker, który jest zarządzany przez aplikację Workbench AML. Za pomocą tej maszyny wirtualnej DSVM jesteśmy w stanie do obliczenia wszystkich funkcji oraz szkolenia modeli i dostosowywanie hiperparametrów (zobacz następną sekcję). Plik singleVM.py ma pełną funkcji obliczeń i modelowanie kodu. W następnej sekcji firma Microsoft pokazują, jak uruchomić singleVM.py w zdalnej maszyny DSVM. 

### <a name="tuning-hyperparameters-using-remote-dsvm"></a>Dostosowywanie hiperparametrów przy użyciu zdalnego maszyny wirtualnej DSVM
Używamy [xgboost](https://anaconda.org/conda-forge/xgboost) zwiększania wyniku gradientu drzewa wykonania [1]. Możemy również użyć [scikit-Dowiedz się,](http://scikit-learn.org/) pakietu można dostrajanie hiperparametrów xgboost. Chociaż xgboost nie jest częścią scikit-informacje pakietu, implementuje scikit — Dowiedz się, interfejsu API i dlatego może być używane razem z hiperparametrycznego dostrajania funkcji scikit — Dowiedz się więcej. 

Xgboost ma osiem hiperparametrów, opisano [tutaj](https://github.com/dmlc/xgboost/blob/master/doc/parameter.md):
* n_estimators
* max_depth
* reg_alpha
* reg_lambda
* colsample\_by_tree
* learning_rate
* colsample\_by_level
* podpróbka
* Cel  
 
Początkowo są używane zdalnego maszyny wirtualnej DSVM i dostosowywanie hiperparametrów z Mała kratka Release candidate wartości:

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1],'learning_rate': [0.1], 'colsample_bylevel': [0.1,], 'subsample': [0.5]}]  

Ta siatka zawiera cztery kombinacje wartości hiperparametrów. Używamy 5-fold krzyżowego sprawdzania poprawności skutkuje 4 x 5 = 20 uruchomienia xgboost. Aby zmierzyć wydajność modeli, używamy pomiar utraty ujemna dziennika. Poniższy kod znajduje wartości hiperparametrów z siatki, która zmaksymalizować utraty zweryfikowane dla wielu ujemna dziennika. Kod używa również te wartości do nauczenia modelu końcowego za pośrednictwem zestawu szkoleniowego pełna:

    clf = XGBClassifier(seed=0)
    metric = 'neg_log_loss'
    
    clf_cv = GridSearchCV(clf, tuned_parameters, scoring=metric, cv=5, n_jobs=8)
    model = clf_cv.fit(X_train,y_train)

Po utworzeniu modelu, oszczędzamy wyniki strojenia hiperparametrycznego. Używamy rejestrowanie interfejsu API AML Workbench można zapisać wartości najlepsze hiperparametrów i odpowiednie zweryfikowane dla wielu oszacowanie utraty ujemna dziennika:

    from azureml.logging import get_azureml_logger

    # initialize logger
    run_logger = get_azureml_logger()

    ...

    run_logger.log(metric, float(clf_cv.best_score_))

    for key in clf_cv.best_params_.keys():
        run_logger.log(key, clf_cv.best_params_[key]) 

Musimy również utworzyć plik sweeping_results.txt z dziennika zweryfikowane dla wielu, ujemnych strat wszystkie kombinacje wartości hiperparametrycznego w siatce.

    if not path.exists('./outputs'):
        makedirs('./outputs')
    outfile = open('./outputs/sweeping_results.txt','w')

    print("metric = ", metric, file=outfile)
    for i in range(len(model.grid_scores_)):
        print(model.grid_scores_[i], file=outfile)
    outfile.close()

Ten plik jest przechowywany w specjalnej. / wyjścia katalogu. Później pokazujemy, jak ją pobrać.  

 Przed uruchomieniem singleVM.py w zdalnej maszyny wirtualnej DSVM po raz pierwszy, utworzymy kontener platformy Docker, uruchamiając 

    az ml experiment prepare -c dsvm

Interfejs wiersza polecenia systemu Windows. Tworzenie z narzędziem trwa kilka minut. Po tym singleVM.py przeprowadzane w nauki:

    az ml experiment submit -c dsvm .\singleVM.py

To polecenie zakończy się 1 godzina 38 minut podczas nauki ma 8 rdzeni i 28 Gb pamięci. Zarejestrowane wartości mogą być wyświetlane w oknie historii uruchamiania AML Workbench:

![Historia uruchamiania](media/scenario-distributed-tuning-of-hyperparameters/run_history.png)

Domyślnie okno Historia uruchamiania pokazuje wartości i wykresy pierwszy 1 – 2 zarejestrowanych wartości. Aby wyświetlić pełną listę wybrane wartości hiperparametrów, kliknij ikonę ustawień oznaczone czerwone kółko na poprzednim zrzucie ekranu. Następnie wybierz hiperparametrów, które mają być wyświetlane w tabeli. Ponadto aby wybrać wykresów, które są wyświetlane w górnej części okna historia uruchamiania, kliknij ikonę ustawienia oznaczone jasnoniebieski okrąg i wybierz wykresy z listy. 

Wybrane wartości hiperparametrów można zbadać w taki sposób, w oknie właściwości uruchomienia: 

![właściwości wykonywania](media/scenario-distributed-tuning-of-hyperparameters/run_properties.png)

W prawym górnym rogu okna właściwości przebiegu jest sekcji plików wyjściowych z listą wszystkich plików, które zostały utworzone w ". \output" folder. sprawdzaniu\_results.txt można pobrać stamtąd, wybierając ją i klikając przycisk Pobierz. sweeping_results.txt powinny mieć następujące dane wyjściowe:

    metric =  neg_log_loss
    mean: -2.29096, std: 0.03748, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28712, std: 0.03822, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28706, std: 0.03863, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}
    mean: -2.28530, std: 0.03927, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}

### <a name="tuning-hyperparameters-using-spark-cluster"></a>Dostosowywanie hiperparametrów przy użyciu klastra Spark
Klaster Spark są używane do skalowania w poziomie Dostosowywanie hiperparametrów i użyj siatki większe. Nasz nowy siatki

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0.1], 'colsample_bylevel': [0.01, 0.1], 'subsample': [0.5, 0.7]}]

Ta siatka ma 16 kombinacje wartości hiperparametrów. Ponieważ używamy 5-fold krzyżowego sprawdzania poprawności, przeprowadzamy xgboost 16 x 5 = 80 godzin.

scikit-Dowiedz się, pakiet nie ma natywnej obsługi na dostosowywanie hiperparametrów przy użyciu klastra Spark. Na szczęście [skryptu sklearn spark](https://spark-packages.org/package/databricks/spark-sklearn) pakietu z usługi Databricks wypełnia luki. Ten pakiet zawiera funkcję GridSearchCV, która ma prawie tego samego interfejsu API funkcji GridSearchCV scikit-Dowiedz się więcej. Użyj skryptu sklearn platformy spark i dostosowywanie hiperparametrów przy użyciu platformy Spark, musimy utworzyć kontekstu aparatu Spark

    from pyspark import SparkContext
    sc = SparkContext.getOrCreate()

Firma Microsoft Zastąp 

    from sklearn.model_selection import GridSearchCV

z  

    from spark_sklearn import GridSearchCV

Ponadto firma Microsoft Zastąp wywołanie GridSearchCV z scikit-Dowiedz się, jak jeden z skryptu sklearn spark:

    clf_cv = GridSearchCV(sc = sc, param_grid = tuned_parameters, estimator = clf, scoring=metric, cv=5)

Końcowe kod Dostosowywanie hiperparametrów przy użyciu platformy Spark w dystrybucji\_sweep.py pliku. Różnica między singleVM.py i distributed_sweep.py jest w definicji siatki i cztery dodatkowe wiersze kodu. Należy zauważyć, że ze względu na usługi AML Workbench rejestrowania kodu nie zmienia się podczas zmiany środowiska wykonawczego ze zdalnej maszyny wirtualnej DSVM do klastra Spark.

Przed uruchomieniem distributed_sweep.py w klastrze Spark po raz pierwszy, należy zainstalować pakiety języka Python, brak. Można to osiągnąć, uruchamiając 

    az ml experiment prepare -c spark

Interfejs wiersza polecenia systemu Windows. Ta instalacja trwa kilka minut. Po tym distributed_sweep.py przeprowadzane w klastrze Spark:

    az ml experiment submit -c spark .\distributed_sweep.py

To polecenie zakończy się 1 godzina 6 minut po klaster platformy Spark ma 6 węzłów procesu roboczego, 28 GB pamięci. Wyniki strojenia hiperparametrycznego są dostępne w usłudze Azure Machine Learning Workbench taki sam sposób jak zdalne wykonywanie nauki. (to znaczy dzienników, najlepsze wartości hiperparametrów i pliku sweeping_results.txt)

### <a name="architecture-diagram"></a>Diagram architektury

Na poniższym diagramie przedstawiono ogólny przepływ pracy: ![architektury](media/scenario-distributed-tuning-of-hyperparameters/architecture.png) 

## <a name="conclusion"></a>Podsumowanie 

W tym scenariuszu pokazaliśmy, jak wykonać Dostosowywanie hiperparametrów w zdalnych maszyn wirtualnych i klastry Spark za pomocą usługi Azure Machine Learning Workbench. Widzieliśmy czy Azure Machine Learning Workbench udostępnia narzędzia, aby ułatwić konfigurację środowiska wykonawcze. Umożliwia również łatwe przełączanie się między nimi. 

## <a name="references"></a>Dokumentacja

[1] i C. Guestrin T. Chen. [XGBoost: Skalowalne drzewa zwiększania wyniku System](https://arxiv.org/abs/1603.02754). KDD 2016.




