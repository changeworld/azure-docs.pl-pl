---
title: Poznaj system Linux
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się, jak wykonać kilka typowych zadań związanych z nauką o danych przy użyciu Data Science Virtual Machine systemu Linux.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 07/16/2018
ms.openlocfilehash: f9d4b933bc9c6e11dde8168d9797a1b6196e6f47
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170687"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Nauka danych przy użyciu Data Science Virtual Machine systemu Linux na platformie Azure

W tym instruktażu pokazano, jak wykonać kilka typowych zadań naukowych dotyczących danych przy użyciu Data Science Virtual Machine systemu Linux (DSVM). Linux DSVM to obraz maszyny wirtualnej dostępny na platformie Azure, który jest preinstalowany z kolekcją narzędzi często używanych do analizy danych i uczenia maszynowego. Najważniejsze składniki oprogramowania są objęte obsługą [administracyjną Data Science Virtual Machine systemu Linux](linux-dsvm-intro.md). Obraz DSVM ułatwia rozpoczęcie pracy z analizą danych w kilka minut, bez konieczności instalowania i konfigurowania poszczególnych narzędzi osobno. Można łatwo skalować w górę DSVM, jeśli jest to konieczne, i można go zatrzymać, gdy nie jest używany. Zasób DSVM jest elastyczny i ekonomiczny.

Zadania analizy danych przedstawione w tym instruktażu są zgodne z krokami opisanymi w temacie [co to jest proces nauki o danych zespołowych?](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) Proces nauki danych zespołu to systematyczne podejście do nauki dotyczącej danych, które ułatwiają zespołom analityków danych wydajną współpracę nad cyklem tworzenia inteligentnych aplikacji. W procesie nauki o danych udostępnia także platforma iteracyjne do nauki o danych, który może występować przez osobę.

W tym instruktażu analizujemy zestaw danych [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) . Spambase to zbiór wiadomości e-mail oznaczonych jako spam lub Ham (bez spamu). Spambase zawiera również pewne dane statystyczne dotyczące zawartości wiadomości e-mail. Więcej informacji na temat statystyk znajduje się w dalszej części przewodnika.

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem DSVM systemu Linux należy spełnić następujące wymagania wstępne:

* **Subskrypcja platformy Azure**. Aby uzyskać subskrypcję platformy Azure, zobacz artykuł [Tworzenie bezpłatnego konta platformy Azure już dzisiaj](https://azure.microsoft.com/free/).
* [**Data Science Virtual Machine systemu Linux**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Informacje o aprowizacji maszyny wirtualnej można znaleźć w temacie [Inicjowanie obsługi Data Science Virtual Machine systemu Linux](linux-dsvm-intro.md).
* [**X2go**](https://wiki.x2go.org/doku.php) zainstalowane na komputerze z otwartą sesją pulpit Xfce. Aby uzyskać więcej informacji, zobacz [Instalowanie i Konfigurowanie klienta x2go](linux-dsvm-intro.md#x2go).
* Aby usprawnić przewijanie w przeglądarce sieci Web w programie DSVM, przełącz `gfx.xrender.enabled` flagę w. `about:config` [Dowiedz się więcej](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Należy również rozważyć `mousewheel.enable_pixel_scrolling` ustawienie `False`. [Dowiedz się więcej](https://support.mozilla.org/questions/981140).
* **Konto Azure Machine Learning**. Jeśli jeszcze tego nie zrobiono, Utwórz nowe konto na [stronie głównej Azure Machine Learning](https://studio.azureml.net/). Możesz bezpłatnie wypróbować usługę, aby pomóc Ci rozpocząć pracę.

## <a name="download-the-spambase-dataset"></a>Pobierz zestaw danych spambase

[Spambase](https://archive.ics.uci.edu/ml/datasets/spambase) DataSet to stosunkowo mały zestaw danych, który zawiera 4 601 przykładów. Zestaw danych jest wygodnym rozmiarem do prezentowania niektórych kluczowych funkcji DSVM, ponieważ utrzymuje wymagania dotyczące zasobów.

> [!NOTE]
> Ten Instruktaż został utworzony przy użyciu D2 v2-size Linux DSVM (CentOS Edition). Aby wykonać procedury, które są przedstawione w tym instruktażu, można użyć DSVM tego rozmiaru.

Jeśli potrzebujesz więcej miejsca do magazynowania, możesz utworzyć dodatkowe dyski i dołączyć je do DSVM. Na dyskach jest używany trwały Magazyn Azure, więc ich dane są zachowywane nawet wtedy, gdy serwer jest ponownie inicjowany z powodu zmiany rozmiarów lub wyłączania. Aby dodać dysk i dołączyć go do DSVM, wykonaj kroki opisane w temacie [Dodawanie dysku do maszyny wirtualnej z systemem Linux](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Procedura dodawania dysku przy użyciu interfejsu wiersza polecenia platformy Azure, który jest już zainstalowany w DSVM. Możesz wykonać czynności całkowicie z DSVM samego siebie. Kolejną opcją zwiększenia magazynu jest użycie [Azure Files](../../storage/files/storage-how-to-use-files-linux.md).

Aby pobrać dane, Otwórz okno terminalu, a następnie uruchom następujące polecenie:

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Pobrany plik nie ma wiersza nagłówka. Utwórzmy kolejny plik, który ma nagłówek. Uruchom następujące polecenie, aby utworzyć plik, odpowiednie nagłówki:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Następnie połącz dwa pliki razem:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Zestaw danych zawiera kilka typów statystyk dla każdej wiadomości e-mail:

* Kolumny, **takie\_jak\_Word FREQ *Word*,** wskazują procent słów w wiadomości e-mail pasującej do *wyrazu*. Jeśli na przykład **słowo\_FREQ\_** ma wartość **1**, *nastąpiło*1% wszystkich wyrazów w wiadomości e-mail.
* Kolumny, **takie\_jak\_char FREQ *char*,** wskazują procent wszystkich znaków w wiadomości e-mail, która jest *znakiem*.
* **wielkie\_Uruchom\_długość\_najdłuższy** jest najdłuższy okres sekwencję wielkie litery.
* **wielkie\_Uruchom\_długość\_średni** jest średnia długość wszystkie sekwencje wielkie litery.
* **wielkie\_Uruchom\_długość\_całkowita** jest łączna długość wszystkich sekwencji wielkie litery.
* **spam** wskazuje, czy wiadomość e-mail została uznana za spam, czy nie (1 = wiadomości-śmieci, 0 = nie spamu).

## <a name="explore-the-dataset-by-using-r-open"></a>Eksplorowanie zestawu danych przy użyciu języka R Open

Przeanalizujmy dane i wykonują podstawowe Uczenie maszynowe przy użyciu języka R. DSVM jest dostarczany ze wstępnie zainstalowanym [programem Microsoft R Open](https://mran.revolutionanalytics.com/open/) . Wielowątkowe biblioteki matematyczne w wstępnie zainstalowanej wersji języka R oferują lepszą wydajność niż wersje jednowątkowe. Otwarty język R umożliwia także odtwarzalność za pomocą migawki repozytorium pakietu CRAN.

Aby uzyskać kopie przykładów kodu, które są używane w tym instruktażu, użyj narzędzia Git, aby sklonować repozytorium Azure-Machine-Learning-Data-nauka. Narzędzie git jest preinstalowane na DSVM. W wierszu polecenia usługi git Uruchom polecenie:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Otwórz okno terminalu i uruchom nową sesję języka R w konsoli języka R Interactive. Można również użyć RStudio, który jest preinstalowany na DSVM.

Aby zaimportować dane i skonfigurować środowisko:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Aby wyświetlić podsumowanie statystyk dotyczących poszczególnych kolumn:

    summary(data)

Aby uzyskać inny widok danych:

    str(data)

Ten widok przedstawia typ każdej zmiennej i kilka pierwszych wartości w zestawie danych.

**Spamu** kolumna została odczytana jako liczba całkowita, ale jest faktycznie kategorii zmiennej (lub Authentication). Aby ustawić jej typ:

    data$spam <- as.factor(data$spam)

Aby przeprowadzić analizę naukową, Użyj pakietu [ggplot2](https://ggplot2.tidyverse.org/) , popularnej biblioteki grafów dla języka R, który jest wstępnie zainstalowany w DSVM. Na podstawie danych podsumowania wyświetlanych wcześniej podsumowanie zawiera podsumowanie dotyczące częstotliwości znaku wykrzyknika. Tutaj Umieść te częstotliwości, uruchamiając następujące polecenia:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Ponieważ pasek zero jest skośny, można go wyeliminować:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Istnieje nieprosta gęstość powyżej 1, która wygląda interesująca. Przyjrzyjmy się tylko tym danym:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Następnie podzielenie go przez spam a Ham:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Te przykłady powinny ułatwić tworzenie podobnych wykresów i Eksplorowanie danych w innych kolumnach.

## <a name="train-and-test-a-machine-learning-model"></a>Uczenie i testowanie modelu uczenia maszynowego

Przekażmy kilka modeli uczenia maszynowego, aby sklasyfikować wiadomości e-mail w zestawie danych jako zawierające spam lub Ham. W tej sekcji pouczymy Model drzewa decyzyjnego i losowy model lasów. Następnie przetestujemy dokładność prognoz.

> [!NOTE]
> Pakiet *rpart* (drzewa partycjonowania i regresji) użyty w poniższym kodzie jest już zainstalowany na DSVM.

Najpierw Podziel zestaw danych na zestawy szkoleniowe i zestawy testów:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Następnie utwórz drzewo decyzyjne służące do klasyfikowania wiadomości e-mail:

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Poniżej przedstawiono wyniki:

![Diagram tworzonego drzewa decyzyjnego](./media/linux-dsvm-walkthrough/decision-tree.png)

Aby określić, jak sprawdzi się na zestaw szkoleniowy, użyj następującego kodu:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Aby określić, jak sprawdzi się w zestawie testów:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Spróbujmy również model lasu losowych. Lasy losowe przeszkolą wiele drzew decyzyjnych i wyprowadzają klasę, która jest trybem klasyfikacji ze wszystkich poszczególnych drzew decyzyjnych. Zapewniają one bardziej zaawansowane podejście do uczenia maszynowego, ponieważ są one poprawne dla tendencji modelu drzewa decyzyjnego, aby overfit zestaw danych szkoleniowych.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio"></a>Wdróż model do Azure Machine Learning Studio

[Azure Machine Learning Studio](https://studio.azureml.net/) to usługa w chmurze, która ułatwia tworzenie i wdrażanie modeli analizy predykcyjnej. Świetną funkcją Azure Machine Learning Studio jest możliwość publikowania dowolnej funkcji języka R jako usługi sieci Web. Pakiet Azure Machine Learning Studio R sprawia, że wdrożenie jest łatwe, bezpośrednio z sesji języka R na DSVM.

Aby wdrożyć kod drzewa decyzyjnego z poprzedniej sekcji, zaloguj się do Azure Machine Learning Studio. Potrzebujesz Identyfikatora obszaru roboczego i token autoryzacji, aby zalogować się. Aby znaleźć te wartości i zainicjować dla nich zmienne Azure Machine Learning, wykonaj następujące kroki:

1. W menu po lewej stronie wybierz pozycję **Ustawienia**. Zwróć uwagę na wartość **identyfikatora obszaru roboczego**.

   ![Identyfikator obszaru roboczego Azure Machine Learning Studio](./media/linux-dsvm-walkthrough/workspace-id.png)

1. Wybierz kartę **tokeny autoryzacji** . Zwróć uwagę na wartość **podstawowego tokenu autoryzacji**.

   ![Podstawowy Token autoryzacji Azure Machine Learning Studio](./media/linux-dsvm-walkthrough/workspace-token.png)
1. Załaduj pakiet platformy **Azure** , a następnie ustaw wartości zmiennych przy użyciu tokenu i identyfikatora obszaru roboczego w sesji języka R na DSVM:

        if(!require("AzureML")) install.packages("AzureML")
        require(AzureML)
        wsAuth = "<authorization-token>"
        wsID = "<workspace-id>"

1. Teraz można uproszczenie model Aby wykonywać ułatwia zaimplementowanie tej prezentacji. Wybierz trzy zmienne w drzewie decyzyjnym znajdującym się najbliżej katalogu głównego i skompiluj nowe drzewo przy użyciu tylko tych trzech zmiennych:

        colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
        smallTrainSet <- trainSet[, colNames]
        smallTestSet <- testSet[, colNames]
        model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

1. Potrzebujemy funkcji prognozowania, który przyjmuje funkcji jako dane wejściowe i zwraca przewidywane wartości:

        predictSpam <- function(newdata) {
        predictDF <- predict(model.rpart, newdata = newdata)
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
        }


1. Opublikuj funkcję **predictSpam** na stronie Azure przy użyciu funkcji **publishWebService** :

        spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)

1. Ta funkcja przyjmuje funkcję **predictSpam** , tworzy usługę sieci Web o nazwie **spamWebService** , która ma zdefiniowane dane wejściowe i wyjściowe, a następnie zwraca informacje o nowym punkcie końcowym.

    To polecenie służy do wyświetlania szczegółów najnowszej opublikowanej usługi sieci Web, w tym jej punktu końcowego interfejsu API i kluczy dostępu:

        s<-tail(services(ws, name = "spamWebService"), 1)
        ep <- endpoints(ws,s)
        ep

1. Aby wypróbować tę funkcję w pierwszym ustawić 10 wierszy testu:

        consume(ep, smallTestSet[1:10, ])

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Samouczki i przewodniki dotyczące uczenia głębokiego

Oprócz przykładów opartych na architekturze udostępnia się również zestaw kompleksowych przewodników. Przewodniki te pomagają szybko Rozpocznij korzystanie z usługi opracowywania aplikacji uczenia głębokiego w domenach, takich jak obraz i tekst/language understanding.

- [Uruchamianie sieci neuronowych w różnych strukturach](https://github.com/ilkarman/DeepLearningFrameworks): Obszerny przewodnik pokazujący, jak migrować kod z jednej struktury do innej. Pokazano również, jak porównać model i wydajność środowiska uruchomieniowego w różnych strukturach. 

- [Przewodnik dotyczący tworzenia kompleksowych rozwiązań do wykrywania produktów w obrazach](https://github.com/Azure/cortana-intelligence-product-detection-from-images): Wykrywanie obrazów to technika, która umożliwia lokalizowanie i klasyfikowanie obiektów w obrazach. Technologia ta pozwala uzyskać ogromne korzyści w wielu domenach biznesowych w czasie rzeczywistym. Na przykład sprzedawcy detaliczni, można użyć tej techniki ustalenie produktu, który klientów została pobrana z półki. Informacje te pomagają z kolei magazyny Zarządzanie spis produktów. 

- [Głębokie uczenie audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): W tym samouczku przedstawiono sposób uczenia modelu uczenia głębokiego na potrzeby wykrywania zdarzeń audio w [zestawie danych dotyczących dźwięków miejskich](https://urbansounddataset.weebly.com/). Samouczek zawiera omówienie sposobu pracy z danymi audio.

- [Klasyfikacja dokumentów tekstowych](https://github.com/anargyri/lstm_han): W tym instruktażu przedstawiono sposób tworzenia i uczenia dwóch różnych architektur sieci neuronowych: Hierarchiczna sieć zwracająca uwagę i długotrwała pamięć krótkoterminowa (LSTM). Te sieci neuronowych na użytek interfejsu API biblioteki Keras głębokie uczenie do klasyfikowania dokumentów tekstowych. Keras to przód do trzech najpopularniejszych platform uczenia głębokiego: Microsoft Cognitive Toolkit, TensorFlow i Theano.

## <a name="other-tools"></a>Inne narzędzia

Pozostałe sekcje pokazują, jak używać niektórych narzędzi zainstalowanych w systemie Linux DSVM. Omawiamy następujące narzędzia:

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL i SQuirreL SQL
* SQL Server Data Warehouse

### <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) zapewnia szybką i dokładną implementację podwyższania poziomu drzewa.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

XGBoost może również wywołać z języka Python lub wiersza polecenia.

### <a name="python"></a>Python

W przypadku programowania w języku Python dystrybucje Anaconda języka Python 3,5 i 2,7 są zainstalowane na DSVM.

> [!NOTE]
> Anaconda dystrybucji obejmuje [Conda](https://conda.pydata.org/docs/index.html). Za pomocą Conda można tworzyć niestandardowe środowiska języka Python, w których zainstalowano różne wersje lub pakiety.

Przeczytajmy część zestawu danych spambase i Klasyfikowanie wiadomości e-mail przy użyciu maszyn wektorów pomocy technicznej w Scikit — uczenie się:

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Jak tworzyć prognozy:

    clf.predict(X.ix[0:20, :])

Aby dowiedzieć się, jak opublikować punkt końcowy Azure Machine Learning, Utwórzmy bardziej podstawowy model. Będziemy używać trzech zmiennych, które zostały użyte po opublikowaniu modelu R wcześniej:

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Aby opublikować model do Azure Machine Learning:

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)


> [!NOTE]
> Ta opcja jest dostępna tylko dla języka Python 2,7. Nie jest jeszcze obsługiwane w języku Python 3,5. Aby uruchomić, użyj **/Anaconda/bin/python2.7**.

### <a name="jupyterhub"></a>JupyterHub

Rozkład Anaconda w DSVM zawiera Jupyter Notebook, czyli środowisko Międzyplatformowe do udostępniania kodu Python, R lub Julia i analizy. Dostęp do Jupyter Notebook można uzyskać za pomocą JupyterHub. Użytkownik loguje się przy użyciu nazwy użytkownika i hasła lokalnego systemu Linux pod\<nazwą DNS https://DSVM lub adresem\>IP: 8000/. Wszystkie pliki konfiguracji dla JupyterHub znajdują się w/etc/jupyterhub.

> [!NOTE]
> Aby użyć Menedżera pakietów języka Python (za pośrednictwem `pip` polecenia) z Jupyter notebook w bieżącym jądrze, użyj tego polecenia w komórce kodu:
>
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Aby użyć Instalatora Conda (za pośrednictwem `conda` polecenia) z Jupyter notebook w bieżącym jądrze, użyj tego polecenia w komórce kodu:
>
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Kilka przykładowych notesów jest już zainstalowanych na DSVM:

* Przykładowe notesy w języku Python:
  * [IntroToJupyterPython. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* Przykładowy Notes języka R:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> Język Julia jest również dostępny w wierszu polecenia w systemie Linux DSVM.

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *A*nalityczny *t*OOL *t*o *L*zdobyć *E*asily) jest graficznym narzędziem języka r do wyszukiwania danych. Rattle ma intuicyjny interfejs, który ułatwia ładowanie, eksplorowanie i Przekształcanie danych oraz tworzenie i ocenę modeli. [Rattle: Graficzny interfejs użytkownika wyszukiwania danych dla](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) języka R zawiera Instruktaż pokazujący funkcje Rattle.

Zainstaluj i uruchom program Rattle, uruchamiając następujące polecenia:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Nie musisz instalować Rattle na DSVM. Jednak po otwarciu Rattle może zostać wyświetlony monit o zainstalowanie dodatkowych pakietów.

Rattle korzysta z interfejsu opartego na karcie. Większość kart odpowiada krokom w [zespołowym procesie nauki danych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), takim jak ładowanie danych lub Eksplorowanie danych. Proces analizy danych jest pisany od lewej do prawej, za pośrednictwem karty. Ostatnia karta zawiera dziennik poleceń języka R, które zostały uruchomione przez Rattle.

Aby załadować i skonfigurować zestaw danych:

1. Aby załadować plik, wybierz kartę **dane** .
1. Wybierz selektor obok **nazwy pliku**, a następnie wybierz pozycję **spambaseHeaders. Data**.
1. Aby załadować plik. Wybierz pozycję **Wykonaj**. Powinno zostać wyświetlone podsumowanie każdej kolumny, w tym określony typ danych. bez względu na to, czy jest to wejście, obiekt docelowy czy inny typ zmiennej; oraz liczbę unikatowych wartości.
1. Poprawnie zidentyfikował rattle **spamu** kolumny jako element docelowy. Wybierz kolumnę **spam** , a następnie ustaw **docelowy typ danych** na **categoric**.

Aby eksplorować dane:

1. Wybierz **Eksploruj** kartę.
1. Aby wyświetlić informacje o typach zmiennych i niektórych statystykach podsumowujących, wybierz opcję**wykonywanie** **podsumowania** > .
1. Aby wyświetlić inne typy statystyk dla każdej zmiennej, wybierz inne opcje, takie jak **opisywanie** lub **podstawy**.

Karta **Eksplorowanie** umożliwia również generowanie szczegółowych wykresów. Do wykreślenia histogram danych:

1. Wybierz **dystrybucje**.
1. W przypadku **word_freq_remove** i **Word_freq_you**wybierz pozycję **histogram**.
1. Wybierz pozycję **Wykonaj**. W jednym oknie wykresu powinny być widoczne oba wykresy gęstości, w _których jest jasne, że słowo pojawia_ się częściej _niż w_wiadomościach e-mail.

Wykresy **korelacji** są również interesujące. Aby utworzyć wykres:

1. W obszarze **Typ**wybierz pozycję **korelacja**.
1. Wybierz pozycję **Wykonaj**.
1. Rattle wyświetli ostrzeżenie, że zalecane maksymalnie 40 zmiennych. Wybierz **tak** Aby wyświetlić wykres.

Istnieje kilka interesujących korelacji: _technologia_ jest silnie skorelowana z _HP_ i _Labs_, na przykład. Jest również silnie skorelowane z _650_ , ponieważ kod obszaru dawców zestawu danych to 650.

Wartości liczbowe korelacji między wyrazami są dostępne w oknie **Eksplorowanie** . Warto zwrócić uwagę na przykład, że _technologia_ jest niekorzystnie _skorelowane z i_ _pieniędzy_.

Rattle można przekształcać zestawu danych do obsługi niektórych typowych problemów. Na przykład może zmieniać skalę funkcji, określać brakujące wartości, obsługiwać elementy odstające i usuwać zmienne lub obserwacje z brakującymi danymi. Rattle może również identyfikować reguły kojarzenia między obserwacjami a zmiennymi. Te karty nie zostały omówione w tym instruktażu wprowadzającym.

Rattle może również uruchamiać analizę klastra. Umożliwia wykluczenie niektórych funkcji, aby ułatwić dane wyjściowe. Na karcie **dane** wybierz opcję **Ignoruj** obok każdej ze zmiennych, z wyjątkiem tych 10 elementów:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* wiadomości-śmieci

Wróć do karty **klaster** . Wybierz pozycję **KMeans**, a następnie ustaw **liczbę klastrów** na **4**. Wybierz pozycję **Wykonaj**. Wyniki są wyświetlane w oknie danych wyjściowych. Jeden klaster ma wysoką częstotliwość _George_ i _HP_i prawdopodobnie jest uzasadnioną służbową pocztą e-mail.

Aby utworzyć podstawowy model uczenia maszynowego w strukturze:

1. Wybierz **modelu** karty
1. W polu **Typ**wybierz pozycję **drzewo**.
1. Wybierz **Execute** do wyświetlenia w drzewie w postaci tekstu w oknie danych wyjściowych.
1. Wybierz **Rysowanie** przycisk, aby wyświetlić graficzną wersję. Drzewo decyzyjne wygląda podobnie do drzewa uzyskanego wcześniej przy użyciu rpart.

Przydatna funkcja Rattle może uruchamiać kilka metod uczenia maszynowego i szybko je oceniać. Oto kroki:

1. Dla opcji **Typ**zaznacz **wszystko**.
1. Wybierz pozycję **Wykonaj**.
1. Po zakończeniu działania Rattle można wybrać dowolną wartość **typu** , na przykład **SVM**, i wyświetlić wyniki.
1. Możesz również porównać wydajność modeli w zestawie walidacji, korzystając z karty **szacowanie** . Na przykład **macierzy błąd** wybór dowiesz się, macierz pomyłek, ogólny błąd i błąd uśrednionej klasy dla każdego modelu w zestawie sprawdzania poprawności. Można również wykreślić krzywe ROC, uruchamiać analizę czułości i wykonywać inne typy ocen modeli.

Po zakończeniu tworzenia modeli wybierz kartę **Dziennik** , aby wyświetlić kod języka R, który został uruchomiony przez Rattle podczas sesji. Możesz wybrać **wyeksportować** przycisk, aby go zapisać.

> [!NOTE]
> Bieżąca wersja programu Rattle zawiera usterkę. Aby zmodyfikować skrypt lub użyć go w celu późniejszego powtórzenia kroków, należy wstawić **#** znak przed poleceniem *Eksportuj ten dziennik...* w tekście dziennika.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL i SQuirreL SQL

Maszyny DSVM jest powiązana z zainstalowana baza danych PostgreSQL. PostgreSQL jest zaawansowane, typu open-source relacyjnej bazy danych. W tej sekcji przedstawiono sposób ładowania zestawu danych spambase do PostgreSQL, a następnie wykonywania zapytania.

Przed załadowaniem danych należy zezwolić na uwierzytelnianie hasła z hosta lokalnego. W wierszu polecenia uruchom następujące polecenie:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

W dolnej części pliku konfiguracji są kilka wierszy, które szczegółowo dozwolone połączenia:

    # "local" is only for Unix domain socket connections:
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Zmień linię **połączenia lokalnego IPv4** , aby używać **algorytmu MD5** zamiast **ident**, więc można zalogować się przy użyciu nazwy użytkownika i hasła:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Następnie uruchom ponownie usługę PostgreSQL:

    sudo systemctl restart postgresql

Aby uruchomić *PSQL* (interaktywny Terminal dla PostgreSQL) jako wbudowanego użytkownika Postgres, uruchom następujące polecenie:

    sudo -u postgres psql

Utwórz nowe konto użytkownika przy użyciu nazwy użytkownika konta systemu Linux użytego do zalogowania się. Utwórz hasło:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Zaloguj się do PSQL:

    psql

Zaimportuj dane do nowej bazy danych:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Teraz eksplorujmy dane i uruchamiasz niektóre zapytania za pomocą SQuirreL SQL, graficznego narzędzia, którego możesz użyć do współdziałania z bazami danych za pośrednictwem sterownika JDBC.

Aby rozpocząć, w menu **aplikacje** Otwórz SQuirreL SQL. Aby skonfigurować sterownika:

1. Wybierz pozycję**sterowniki widoku** **systemu Windows** > .
1. Kliknij prawym przyciskiem myszy pozycję **PostgreSQL** i wybierz pozycję **Modyfikuj sterownik**.
1. Wybierz pozycję **dodatkowa ścieżka** > klasy**Dodaj**.
1. W obszarze **Nazwa pliku**wprowadź **/usr/share/Java/jdbcdrivers/PostgreSQL-9.4.1208.jre6.jar**.
1. Wybierz pozycję **Otwórz**.
1. Wybierz pozycję **Wyświetl sterowniki**. W obszarze **Nazwa klasy**wybierz pozycję **org. PostgreSQL. Driver**, a następnie wybierz przycisk **OK**.

Aby skonfigurować połączenie z serwerem lokalnym:

1. Wybierz pozycję **Windows** > **View aliasy.**
1. Wybierz przycisk **+** , aby utworzyć nowy alias. Dla nowej nazwy aliasu wprowadź **bazę danych spamu**. 
1. W obszarze **Sterownik**wybierz pozycję **PostgreSQL**.
1. Ustaw adres URL **jdbc:postgresql://localhost/spam**.
1. Wprowadź nazwę użytkownika i hasło.
1. Kliknij przycisk **OK**.
1. Aby otworzyć **połączenia** okna, kliknij dwukrotnie **spamu bazy danych** aliasu.
1. Wybierz przycisk **Połącz**.

Aby uruchomić kilka zapytań:

1. Wybierz **SQL** kartę.
1. W polu zapytania w górnej części karty **SQL** wprowadź zapytanie podstawowe, takie jak `SELECT * from data;`.
1. Naciśnij klawisze CTRL + ENTER, aby uruchomić zapytanie. Domyślnie SQuirreL SQL zwraca pierwsze 100 wierszy z zapytania.

Istnieje wiele zapytań, które można uruchomić, aby eksplorować te dane. Na przykład, jak działa częstotliwość wyraz *wprowadzić* różnią się w wiadomości-śmieci, które pichcisz?

    SELECT avg(word_freq_make), spam from data group by spam;

Lub jakie są cechy poczty e-mail, które często zawierają *3D*?

    SELECT * from data order by word_freq_3d desc;

Większość wiadomości e-mail, które mają duże wystąpienie *3W* , jest prawdopodobnie spamem. Te informacje mogą być przydatne podczas tworzenia modelu predykcyjnego do klasyfikowania wiadomości e-mail.

Jeśli chcesz przeprowadzić Uczenie maszynowe przy użyciu danych przechowywanych w bazie danych PostgreSQL, rozważ użycie [MADlib](https://madlib.incubator.apache.org/).

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

Azure SQL Data Warehouse to oparta na chmurze baza danych skalowalna w poziomie, która może przetwarzać ogromne ilości danych, zarówno relacyjnych, jak i nierelacyjnych. Aby uzyskać więcej informacji, zobacz [co to jest Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Aby połączyć się z magazynem danych i utworzenia tabeli, uruchom następujące polecenie w wierszu polecenia:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

W wierszu polecenia sqlcmd Uruchom następujące polecenie:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Skopiuj dane przy użyciu narzędzia bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Pobrany plik zawiera końce linii stylu systemu Windows. Narzędzie bcp oczekuje końców linii w stylu systemu UNIX. Użyj flagi-r, aby powiedzieć bcp.

Następnie wykonaj zapytanie przy użyciu narzędzia sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

Możesz również wysyłać zapytania przy użyciu języka SQL SQuirreL. Wykonaj czynności podobne do PostgreSQL przy użyciu sterownika JDBC SQL Server. Sterownik JDBC znajduje się w folderze/usr/share/Java/jdbcdrivers/sqljdbc42.jar.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem artykułów, które przeprowadzą Cię przez zadania wchodzące w skład procesu analizy danych na platformie Azure, zobacz [zespół ds. analizy danych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview).

Aby zapoznać się z opisem kompleksowych przewodników, które przedstawiają kroki procesu nauka danych zespołu dla konkretnych scenariuszy, zobacz [przewodniki dotyczące procesów naukowych](../team-data-science-process/walkthroughs.md)dotyczących analiz danych. Przewodniki pokazują również sposób łączenia w chmurze i lokalnych narzędzi i usług w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji.
