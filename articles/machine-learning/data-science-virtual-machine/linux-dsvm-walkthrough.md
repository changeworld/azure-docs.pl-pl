---
title: Poznaj Linuksa
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się, jak wykonać kilka typowych zadań nauki o danych przy użyciu maszyny wirtualnej do nauki o danych systemu Linux.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 07/16/2018
ms.openlocfilehash: 1d15d53816d916bd28841aae39255685524faa2d
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477870"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Analiza danych za pomocą maszyny wirtualnej do nauki o danych w systemie Linux na platformie Azure

W tym przewodniku pokazano, jak wykonać kilka typowych zadań nauki o danych przy użyciu maszyny wirtualnej do nauki o danych systemu Linux (DSVM). Linux DSVM to obraz maszyny wirtualnej dostępny na platformie Azure, który jest preinstalowany z kolekcją narzędzi powszechnie używanych do analizy danych i uczenia maszynowego. Kluczowe składniki oprogramowania są wyszczególnione w [uidrękaniu maszyny wirtualnej do nauki o danych linuksa](linux-dsvm-intro.md). Obraz DSVM ułatwia rozpoczęcie nauki o danych w ciągu kilku minut, bez konieczności instalowania i konfigurowania każdego z narzędzi indywidualnie. W razie potrzeby można łatwo skalować dsvm i można go zatrzymać, gdy nie jest używany. Zasób DSVM jest zarówno elastyczny, jak i ekonomiczny.

Zadania nauki o danych zademonstrowane w tym przewodniku wykonaj kroki opisane w [Co to jest proces nauki o danych zespołu?](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) Proces nauki o danych zespołu to systematyczne podejście do nauki o danych, które pomaga zespołom analityków danych skutecznie współpracować nad cyklem życia tworzenia inteligentnych aplikacji. Proces nauki o danych zapewnia również iteracyjny framework dla nauki o danych, które mogą być następuje przez osobę.

W tym instruktażu analizujemy zestaw danych [bazy spamu.](https://archive.ics.uci.edu/ml/datasets/spambase) Spambase to zestaw wiadomości e-mail oznaczonych spamem lub szynką (nie spamem). Spambase zawiera również pewne statystyki dotyczące zawartości wiadomości e-mail. Mówimy o statystykach w dalszej części przewodnika.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było używać systemu DSVM systemu Linux, należy mieć następujące wymagania wstępne:

* **Subskrypcja platformy Azure**. Aby uzyskać subskrypcję platformy Azure, zobacz [Tworzenie bezpłatnego konta platformy Azure już dziś.](https://azure.microsoft.com/free/)
* [**Linux Data Science Virtual Machine**](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu). Aby uzyskać informacje dotyczące inicjowania obsługi administracyjnej maszyny wirtualnej, zobacz [Aprowizowanie maszyny wirtualnej do nauki o danych systemu Linux](linux-dsvm-intro.md).
* [**X2Go**](https://wiki.x2go.org/doku.php) zainstalowany na komputerze z otwartą sesją XFCE. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie klienta X2Go](linux-dsvm-intro.md#x2go).
* Aby uzyskać płynniejsze przewijanie, w przeglądarce internetowej DSVM firefox, przełącz flagę `gfx.xrender.enabled` w `about:config`. [Dowiedz się więcej](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Należy również `mousewheel.enable_pixel_scrolling` `False`rozważyć ustawienie na . [Dowiedz się więcej](https://support.mozilla.org/questions/981140).
* **Konto usługi Azure Machine Learning**. Jeśli jeszcze go nie masz, zarejestruj się, aby założyć nowe konto na [stronie głównej usługi Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning//).

## <a name="download-the-spambase-dataset"></a>Pobierz zestaw danych spambase

Zestaw danych [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) to stosunkowo mały zestaw danych zawierający 4601 przykładów. Zestaw danych jest wygodny rozmiar do wykazania niektórych kluczowych funkcji DSVM, ponieważ utrzymuje skromne wymagania zasobów.

> [!NOTE]
> Ten instruktaż został utworzony przy użyciu D2 v2-rozmiar Linux DSVM (CentOS Edition). Ten rozmiar można użyć do wykonania procedur, które zostały zademonstrowane w tym instruktażu.

Jeśli potrzebujesz więcej miejsca, możesz utworzyć dodatkowe dyski i dołączyć je do dsvm. Dyski używają trwałego magazynu platformy Azure, więc ich dane są zachowywane, nawet jeśli serwer jest ponownie aprowiowany z powodu zmiany rozmiaru lub jest zamknięty. Aby dodać dysk i dołączyć go do maszyny DSVM, wykonaj czynności opisane w sekcji [Dodawanie dysku do maszyny Wirtualnej systemu Linux](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Kroki dodawania dysku użyj interfejsu wiersza polecenia platformy Azure, który jest już zainstalowany na dsvm. Kroki można wykonać całkowicie z samego DSVM. Inną opcją zwiększenia pamięci masowej jest użycie [usługi Azure Files](../../storage/files/storage-how-to-use-files-linux.md).

Aby pobrać dane, otwórz okno terminala, a następnie uruchom to polecenie:

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Pobrany plik nie ma wiersza nagłówka. Utwórzmy inny plik, który ma nagłówek. Uruchom to polecenie, aby utworzyć plik z odpowiednimi nagłówkami:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Następnie połącz dwa pliki razem:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Zestaw danych ma kilka typów statystyk dla każdej wiadomości e-mail:

* Kolumny takie jak **słowo\_freq\_WORD** wskazują procent słów w wiadomości e-mail, które pasują do *WORD*. Na przykład, jeśli **\_słowo freq\_make** wynosi **1**, to 1% wszystkich słów w wiadomości e-mail zostały *zrobić*.
* Kolumny takie jak **char\_freq\_CHAR** wskazują procent wszystkich znaków w wiadomości e-mail, które są *CHAR*.
* **długość\_\_przebiegu\_wielkim jest** najdłuższą długością sekwencji wielkich liter.
* **średnia\_\_długości\_przebiegu kapitałowego** jest średnią długością wszystkich sekwencji wielkich liter.
* **suma\_\_długości\_przebiegu wielkich liter** jest całkowitą długością wszystkich sekwencji wielkich liter.
* **spam** wskazuje, czy wiadomość e-mail została uznana za spam, czy nie (1 = spam, 0 = nie spam).

## <a name="explore-the-dataset-by-using-r-open"></a>Eksplorowanie zestawu danych przy użyciu funkcji R Open

Przeanalizujmy dane i wykonaj kilka podstawowych uczenia maszynowego przy użyciu języka R. DSVM jest dostarczany z preinstalowanym systemem [Microsoft R Open.](https://mran.revolutionanalytics.com/open/) Biblioteki matematyczne wielowątkowe w preinstalowanej wersji języka R oferują lepszą wydajność niż wersje jednowątkowe. R Open zapewnia również odtwarzalność za pomocą migawki repozytorium pakietów CRAN.

Aby uzyskać kopie przykładów kodu, które są używane w tym instruktażu, użyj git do klonowania repozytorium Azure-Machine-Learning-Data-Science. Git jest preinstalowany w systemie DSVM. W wierszu polecenia git uruchom:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Otwórz okno terminala i rozpocznij nową sesję języka R w konsoli interaktywnej języka R. Można również użyć RStudio, który jest preinstalowany na DSVM.

Aby zaimportować dane i skonfigurować środowisko:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Aby wyświetlić statystyki podsumowania dotyczące każdej kolumny:

    summary(data)

Aby uzyskać inny widok danych:

    str(data)

W tym widoku przedstawiono typ każdej zmiennej i kilka pierwszych wartości w zestawie danych.

Kolumna **spamu** została odczytana jako liczba całkowita, ale w rzeczywistości jest to zmienna kategoryczna (lub czynnik). Aby ustawić jego typ:

    data$spam <- as.factor(data$spam)

Aby wykonać analizę odkrywcze, użyj pakietu [ggplot2,](https://ggplot2.tidyverse.org/) popularnej biblioteki wykresów dla R, która jest preinstalowana w dsvm. Na podstawie danych podsumowujących wyświetlanych wcześniej, mamy statystyki podsumowania częstotliwości znaku wykrzyknika. Wykreślijmy te częstotliwości w tym miejscu, uruchamiając następujące polecenia:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Ponieważ pasek zerowy pochyla wykres, wyeliminujmy ją:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Istnieje gęstość nietrywialna powyżej 1, która wygląda interesująco. Spójrzmy tylko na te dane:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Następnie podziel go przez spam kontra szynkę:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Te przykłady powinny pomóc w nakreślić podobne wykresy i eksplorować dane w innych kolumnach.

## <a name="train-and-test-a-machine-learning-model"></a>Szkolenie i testowanie modelu uczenia maszynowego

Przeszkolijmy kilka modeli uczenia maszynowego, aby sklasyfikować wiadomości e-mail w zestawie danych jako zawierające spam lub szynkę. W tej sekcji szkolimy model drzewa decyzyjnego i losowy model lasu. Następnie testujemy dokładność prognoz.

> [!NOTE]
> Pakiet *rpart* (cykliczne partycjonowanie i drzewa regresji) używany w poniższym kodzie jest już zainstalowany w systemie DSVM.

Najpierw podzielmy zestaw danych na zestawy szkoleniowe i zestawy testów:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Następnie utwórz drzewo decyzyjne, aby sklasyfikować wiadomości e-mail:

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Oto wynik:

![Diagram tworzonego drzewa decyzyjnego](./media/linux-dsvm-walkthrough/decision-tree.png)

Aby określić, jak dobrze działa na zestawie szkoleniowym, użyj następującego kodu:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Aby określić, jak dobrze działa na zestawie testowym:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Wypróbujmy również losowy model lasu. Losowe lasy trenują wiele drzew decyzyjnych i wyprowadzają klasę, która jest trybem klasyfikacji ze wszystkich poszczególnych drzew decyzyjnych. Zapewniają one bardziej zaawansowane podejście uczenia maszynowego, ponieważ korygują tendencję modelu drzewa decyzyjnego do overfit zestawu danych szkolenia.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio-classic"></a>Wdrażanie modelu w usłudze Azure Machine Learning Studio (klasyczny)

[Usługa Azure Machine Learning Studio (klasyczna)](https://studio.azureml.net/) to usługa w chmurze, która ułatwia tworzenie i wdrażanie modeli analizy predykcyjnej. Ładną cechą usługi Azure Machine Learning Studio (klasyczny) jest jego możliwość publikowania dowolnej funkcji Języka R jako usługi sieci web. Pakiet Usługi Azure Machine Learning Studio (klasyczny) pakiet języka R ułatwia wdrażanie bezpośrednio z sesji języka R na modelu DSVM.

Aby wdrożyć kod drzewa decyzyjnego z poprzedniej sekcji, zaloguj się do usługi Azure Machine Learning Studio (klasyczny). Do zalogowania się potrzebny jest identyfikator obszaru roboczego i token autoryzacji. Aby znaleźć te wartości i zainicjować zmienne usługi Azure Machine Learning za ich pomocą, wykonaj następujące kroki:

1. W menu po lewej stronie wybierz pozycję **Ustawienia**. Zanotuj wartość **identyfikatora workspace**.

   ![Identyfikator obszaru roboczego usługi Azure Machine Learning Studio (klasyczny)](./media/linux-dsvm-walkthrough/workspace-id.png)

1. Wybierz kartę **Tokeny autoryzacji.** Zanotuj wartość **tokenu autoryzacji podstawowej**.

   ![Token autoryzacji podstawowej usługi Azure Machine Learning Studio (klasyczny)](./media/linux-dsvm-walkthrough/workspace-token.png)
1. Załaduj pakiet **AzureML,** a następnie ustaw wartości zmiennych za pomocą identyfikatora tokenu i obszaru roboczego w sesji R na serwerze DSVM:

        if(!require("devtools")) install.packages("devtools")
        devtools::install_github("RevolutionAnalytics/AzureML")
        if(!require("AzureML")) install.packages("AzureML")
        require(AzureML)
        wsAuth = "<authorization-token>"
        wsID = "<workspace-id>"

1. Uprościjmy model, aby ta demonstracja była łatwiejsza do zaimplementowania. Wybierz trzy zmienne w drzewie decyzyjnym najbliżej katalogu głównego i skompiluj nowe drzewo przy użyciu tylko tych trzech zmiennych:

        colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
        smallTrainSet <- trainSet[, colNames]
        smallTestSet <- testSet[, colNames]
        model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

1. Potrzebujemy funkcji przewidywania, która przyjmuje funkcje jako dane wejściowe i zwraca przewidywane wartości:

        predictSpam <- function(newdata) {
        predictDF <- predict(model.rpart, newdata = newdata)
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
        }

1. Utwórz plik settings.json dla tego obszaru roboczego:

        vim ~/.azureml/settings.json

1. Upewnij się, że w pliku settings.json znajduje się następująca zawartość:

         {"workspace":{
           "id": "<workspace-id>",
           "authorization_token": "<authorization-token>",
           "api_endpoint": "https://studioapi.azureml.net",
           "management_endpoint": "https://management.azureml.net"
         }


1. Publikowanie **predictSpam** funkcji do usługi AzureML przy użyciu **funkcji publishWebService:**

        ws <- workspace()
        spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)

1. Ta funkcja przyjmuje **predictSpam** funkcji, tworzy usługę sieci web o nazwie **spamWebService,** który ma zdefiniowane dane wejściowe i wyjściowe, a następnie zwraca informacje o nowym punkcie końcowym.

    To polecenie służy do wyświetlania szczegółów najnowszej opublikowanej usługi sieci web, w tym jej punktu końcowego interfejsu API i kluczy dostępu:

        s<-tail(services(ws, name = "spamWebService"), 1)
        ep <- endpoints(ws,s)
        ep

1. Aby wypróbować go w pierwszych 10 wierszach zestawu testowego:

        consume(ep, smallTestSet[1:10, ])

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Samouczki i instruktaże dotyczące uczenia głębokiego

Oprócz przykładów opartych na platformie znajduje się również zestaw kompleksowych instruktajów. Te wskazówki ułatwiają szybkie rozpoczęcie tworzenia aplikacji uczenia głębokiego w domenach, takich jak rozumienie obrazu i tekstu/języka.

- [Uruchamianie sieci neuronowych w różnych ramach: kompleksowy](https://github.com/ilkarman/DeepLearningFrameworks)przewodnik, który pokazuje, jak migrować kod z jednej struktury do drugiej. Pokazano również, jak porównać wydajność modelu i środowiska wykonawczego w ramach. 

- [Przewodnik do tworzenia kompleksowego rozwiązania do wykrywania produktów w obrazach:](https://github.com/Azure/cortana-intelligence-product-detection-from-images)Wykrywanie obrazów to technika, która może zlokalizować i sklasyfikować obiekty w obrazach. Technologia ta może przynieść ogromne korzyści w wielu rzeczywistych dziedzinach biznesu. Na przykład sprzedawcy detaliczni mogą użyć tej techniki, aby określić, który produkt klient podniósł z półki. Te informacje z kolei pomagają sklepom zarządzać zapasami produktów. 

- [Głębokie uczenie dźwięku:](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/)Ten samouczek pokazuje, jak trenować model uczenia głębokiego do wykrywania zdarzeń audio w [zestawie danych dźwiękowych.](https://urbansounddataset.weebly.com/) Samouczek zawiera omówienie sposobu pracy z danymi audio.

- [Klasyfikacja dokumentów tekstowych:](https://github.com/anargyri/lstm_han)W tym instruktażu pokazano, jak budować i trenować dwie różne architektury sieci neuronowych: hierarchiczną sieć uwagi i pamięć długoterminową (LSTM). Te sieci neuronowe używają interfejsu API Keras do uczenia głębokiego do klasyfikowania dokumentów tekstowych. Keras jest frontonem trzech najpopularniejszych struktur uczenia głębokiego: Microsoft Cognitive Toolkit, TensorFlow i Theano.

## <a name="other-tools"></a>Inne narzędzia

Pozostałe sekcje pokazują, jak korzystać z niektórych narzędzi zainstalowanych w systemie Linux DSVM. Omawiamy następujące narzędzia:

* XGBoost
* Python
* JupyterHub (Polski)
* Rattle
* PostgreSQL i SQuirreL SQL
* Magazyn danych programu SQL Server

### <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) zapewnia szybką i dokładną implementację drzewa.

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

XGBoost może również wywoływać z Pythona lub wiersza polecenia.

### <a name="python"></a>Python

W przypadku tworzenia języka Python dystrybucje Języka Python 3.5 i 2.7 są zainstalowane na serwerze DSVM.

> [!NOTE]
> Dystrybucja Anaconda obejmuje [Conda](https://conda.pydata.org/docs/index.html). Conda służy do tworzenia niestandardowych środowisk Języka Python, które mają różne wersje lub pakiety zainstalowane w nich.

Przeczytajmy w niektórych spambase dataset i sklasyfikować wiadomości e-mail z obsługą maszyn wektorowych w Scikit-learn:

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Aby przewidzieć:

    clf.predict(X.ix[0:20, :])

Aby zademonstrować, jak opublikować punkt końcowy usługi Azure Machine Learning, zróbmy bardziej podstawowy model. Użyjemy trzech zmiennych, które użyliśmy podczas wcześniejszego opublikowania modelu R:

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Aby opublikować model w usłudze Azure Machine Learning:

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
> Ta opcja jest dostępna tylko dla Języka Python 2.7. Nie jest jeszcze obsługiwany w Pythonie 3.5. Aby uruchomić, należy użyć **/anaconda/bin/python2.7**.

### <a name="jupyterhub"></a>JupyterHub (Polski)

Dystrybucja Anaconda w DSVM jest wyposażona w notebook Jupyter, środowisko międzyplatformowe do udostępniania kodu i analizy Języka Python, R lub Julia. Do notesu Jupyter jest dostępny za pośrednictwem usługi JupyterHub. Logujesz się przy użyciu lokalnej nazwy użytkownika i\<hasła systemu Linux pod\>https:// nazwą DNS DSVM lub adresem IP:8000/. Wszystkie pliki konfiguracyjne dla JupyterHub znajdują się w /etc/jupyterhub.

> [!NOTE]
> Aby użyć Menedżera pakietów `pip` języka Python (za pomocą polecenia) z notesu Jupytera w bieżącym jądrze, użyj tego polecenia w komórce kodu:
>
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Aby użyć instalatora Conda `conda` (za pomocą polecenia) z notesu Jupytera w bieżącym jądrze, użyj tego polecenia w komórce kodu:
>
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Kilka przykładowych notesów jest już zainstalowanych w systemie DSVM:

* Przykładowe notesy języka Python:
  * [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [Usługa IrisClassifierPyMLWeb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* Przykładowy notes R:
  * [IntroTutorialinr ( IntroTutorialinr )](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> Język Julia jest również dostępny z wiersza polecenia na Linux DSVM.

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) *(R* *A*nalytical *T*ool *T*o *L*earn *E*asily) jest graficznym narzędziem R do eksploracji danych. Rattle posiada intuicyjny interfejs, który ułatwia ładowanie, eksplorowanie i przekształcanie danych oraz tworzenie i ocenę modeli. [Grzechotka: Interfejs gui wyszukiwanie danych dla R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) zawiera instruktaż, który pokazuje funkcje Rattle.Rattle: A Data Mining GUI for R provides a walkthrough that demonstrates Rattle's features.

Zainstaluj i uruchom rattle, uruchamiając następujące polecenia:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Nie musisz instalować grzechotki na dsvm. Jednak po otwarciu programu Rattle może zostać wyświetlony monit o zainstalowanie dodatkowych pakietów.

Rattle używa interfejsu opartego na tabulatorach. Większość kart odpowiada krokom w [procesie nauki o danych zespołu,](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)takich jak ładowanie danych lub eksplorowanie danych. Proces nauki o danych przepływa od lewej do prawej przez karty. Ostatnia karta zawiera dziennik poleceń R, które zostały uruchomione przez Rattle.

Aby załadować i skonfigurować zestaw danych:

1. Aby załadować plik, wybierz kartę **Dane.**
1. Wybierz selektor obok **pozycji Nazwa pliku**, a następnie wybierz **opcję spambaseHeaders.data**.
1. Aby załadować plik. wybierz **opcję Wykonaj**. Powinno zostać wyświetlone podsumowanie każdej kolumny, w tym jej typ zidentyfikowanych danych; niezależnie od tego, czy jest to dane wejściowe, cel lub inny typ zmiennej; i liczbę unikatowych wartości.
1. Rattle poprawnie zidentyfikował kolumnę **spamu** jako cel. Wybierz kolumnę **spamu,** a następnie ustaw **typ danych docelowych** na **Kategoryczny**.

Aby eksplorować dane:

1. Wybierz kartę **Eksploruj.**
1. Aby wyświetlić informacje o typach zmiennych i niektórych statystykach podsumowania, wybierz opcję **Podsumowanie** > **wykonania**.
1. Aby wyświetlić inne typy statystyk dotyczących każdej zmiennej, wybierz inne opcje, takie jak **Opis lub** **Podstawy**.

Kartę **Eksploruj** można również użyć do generowania wnikliwych wykresów. Aby wykreślić histogram danych:

1. Wybierz **pozycję Dystrybucje**.
1. W **przypadku word_freq_remove** i **word_freq_you**wybierz **Histogram**.
1. Wybierz pozycję **Wykonaj**. Oba wykresy gęstości powinny być widoczne w jednym oknie wykresu, gdzie jest jasne, że _słowo_ pojawia się znacznie częściej w wiadomościach e-mail niż _usuwanie_.

Interesujące są również działki **korelacji.** Aby utworzyć wykres, należy:

1. W obszarze **Typ**wybierz **opcję Korelacja**.
1. Wybierz pozycję **Wykonaj**.
1. Rattle ostrzega, że zaleca maksymalnie 40 zmiennych. Wybierz **opcję Tak,** aby wyświetlić wykres.

Istnieje kilka ciekawych korelacji, które pojawiają się: _technologia_ jest silnie skorelowana z _HP_ i _laboratoriów_, na przykład. Jest również silnie skorelowane z _650,_ ponieważ kod kierunkowy dawców zestawu danych wynosi 650.

Wartości liczbowe dla korelacji między wyrazami są dostępne w **eksploruj** okna. Warto zauważyć, na przykład, że _technologia_ jest negatywnie skorelowana z _twoimi_ i _pieniędzmi._

Grzechotka może przekształcić zestaw danych do obsługi niektórych typowych problemów. Na przykład można przeskalować funkcje, przypisywać brakujące wartości, obsługiwać wartości odstające i usuwać zmienne lub obserwacje, które mają brakujące dane. Grzechotka może również identyfikować reguły skojarzenia między obserwacjami i zmiennymi. Te karty nie są omówione w tym instruktażu wprowadzającym.

Grzechotka może również uruchamiać analizę klastra. Wykluczmy niektóre funkcje, aby dane wyjściowe były łatwiejsze do odczytania. Na karcie **Dane** wybierz pozycję **Ignoruj** obok każdej ze zmiennych z wyjątkiem tych 10 elementów:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

Powrót do karty **Klaster.** Wybierz **KMeans**, a następnie ustaw **liczbę klastrów** na **4**. Wybierz pozycję **Wykonaj**. Wyniki są wyświetlane w oknie wyjściowym. Jeden klaster ma wysoką częstotliwość _george_ i _hp_, i jest prawdopodobnie uzasadnione e-mail biznesowych.

Aby utworzyć podstawowy model uczenia maszynowego drzewa decyzyjnego:

1. Wybierz kartę **Model,**
1. Dla **typu**wybierz **opcję Drzewo**.
1. Wybierz **polecenie Wykonaj,** aby wyświetlić drzewo w formie tekstowej w oknie wyjściowym.
1. Wybierz przycisk **Rysowanie,** aby wyświetlić wersję graficzną. Drzewo decyzyjne wygląda podobnie do drzewa, które uzyskaliśmy wcześniej za pomocą rpart.

Przydatną cechą Rattle jest jego zdolność do uruchamiania kilku metod uczenia maszynowego i szybkiego ich oceny. Oto kroki:

1. W obszarze **Typ**wybierz pozycję **Wszystkie**.
1. Wybierz pozycję **Wykonaj**.
1. Po zakończeniu pracy rattle można wybrać dowolną wartość **typu, taką** jak **SVM,** i wyświetlić wyniki.
1. Można również porównać wydajność modeli na zestaw sprawdzania poprawności przy użyciu **tabeli Oceń** kartę. Na przykład wybór **Macierz błędów** pokazuje macierz pomyłek, ogólny błąd i uśredniona wartość błędu klasy dla każdego modelu w zestawie sprawdzania poprawności. Można również wykreślić krzywe ROC, uruchomić analizę czułości i wykonać inne typy ocen modelu.

Po zakończeniu tworzenia modeli wybierz kartę **Log,** aby wyświetlić kod R, który został uruchomiony przez Rattle podczas sesji. Można wybrać przycisk **Eksportuj,** aby go zapisać.

> [!NOTE]
> Bieżąca wersja Rattle zawiera błąd. Aby zmodyfikować skrypt lub użyć go do powtórzenia **#** kroków później, należy wstawić znak przed *Eksportuj ten dziennik ...* w tekście dziennika.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL i SQuirreL SQL

DSVM jest wyposażony w postgreSql zainstalowany. PostgreSQL to wyrafinowana, relacyjnej bazy danych typu open source. W tej sekcji pokazano, jak załadować zestaw danych bazy spamu do PostgreSQL, a następnie zbadać go.

Przed załadowaniem danych należy zezwolić na uwierzytelnianie hasłem od hosta lokalnego. W wierszu polecenia uruchom następujące polecenie:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

W dolnej części pliku konfiguracyjnego znajduje się kilka wierszy, które wyszczególniają dozwolone połączenia:

    # "local" is only for Unix domain socket connections:
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Zmień linię **połączeń lokalnych IPv4,** aby używać **md5** zamiast **ident**, abyśmy mogli się zalogować za pomocą nazwy użytkownika i hasła:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Następnie uruchom ponownie usługę PostgreSQL:

    sudo systemctl restart postgresql

Aby uruchomić *psql* (interaktywny terminal dla PostgreSQL) jako wbudowanego użytkownika postgres, uruchom to polecenie:

    sudo -u postgres psql

Utwórz nowe konto użytkownika przy użyciu nazwy użytkownika konta Linux użytego do zalogowania się. Utwórz hasło:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Zaloguj się do psql:

    psql

Zaimportuj dane do nowej bazy danych:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Teraz eksplorujmy dane i uruchamiajmy niektóre zapytania za pomocą SQuirreL SQL, graficznego narzędzia, którego można używać do interakcji z bazami danych za pośrednictwem sterownika JDBC.

Aby rozpocząć, w menu **Aplikacje** otwórz SQuirreL SQL. Aby skonfigurować sterownik:

1. Wybierz pozycję**Sterowniki widoku** **systemu Windows** > .
1. Kliknij prawym przyciskiem myszy **postgreSQl** i wybierz polecenie **Modyfikuj sterownik**.
1. Wybierz **opcję Dodaj dodatkową ścieżkę klasy** > **Add**.
1. W polu **Nazwa pliku**wpisz **/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar**.
1. Wybierz pozycję **Otwórz**.
1. Wybierz **pozycję Lista sterowników**. W obszarze **Nazwa klasy**wybierz **pozycję org.postgresql.Driver**, a następnie wybierz przycisk **OK**.

Aby skonfigurować połączenie z serwerem lokalnym:

1. Wybierz**pozycję Aliasy widoku** **systemu Windows.** > 
1. Wybierz **+** przycisk, aby utworzyć nowy alias. Aby uzyskać nową nazwę **aliasu, wprowadź bazę danych spamu**. 
1. W obszarze **Sterownik**wybierz **pozycję PostgreSQL**.
1. Ustaw adres URL na **jdbc:postgresql://localhost/spam**.
1. Wprowadź nazwę użytkownika i hasło.
1. Kliknij przycisk **OK**.
1. Aby otworzyć okno **Połączenie,** kliknij dwukrotnie alias **bazy danych spamu.**
1. Wybierz przycisk **Połącz**.

Aby uruchomić niektóre zapytania:

1. Wybierz kartę **SQL.**
1. W polu kwerendy u góry karty **SQL** wprowadź zapytanie podstawowe, takie jak `SELECT * from data;`.
1. Naciśnij klawisze Ctrl+Enter, aby uruchomić kwerendę. Domyślnie SQuirreL SQL zwraca pierwsze 100 wierszy z kwerendy.

Istnieje wiele innych zapytań, które można uruchomić w celu eksplorowania tych danych. Na przykład, jak częstotliwość tego słowa *różnią się* między spamem a szynką?

    SELECT avg(word_freq_make), spam from data group by spam;

Lub, jakie są cechy e-mail, które często zawierają *3d?*

    SELECT * from data order by word_freq_3d desc;

Większość e-maili, które mają wysoką częstość *występowania 3D,* najwyraźniej to spam. Te informacje mogą być przydatne do tworzenia modelu predykcyjnego do klasyfikowania wiadomości e-mail.

Jeśli chcesz wykonać uczenie maszynowe przy użyciu danych przechowywanych w bazie danych PostgreSQL, rozważ użycie [madlib](https://madlib.incubator.apache.org/).

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

Usługa Azure SQL Data Warehouse to oparta na chmurze, skalowawska baza danych, która może przetwarzać ogromne ilości danych, zarówno relacyjnych, jak i nierelacyjnych. Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure SQL Data Warehouse?](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Aby połączyć się z magazynem danych i utworzyć tabelę, uruchom następujące polecenie z wiersza polecenia:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

W wierszu sqlcmd uruchom to polecenie:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Skopiuj dane przy użyciu bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Pobrany plik zawiera zakończenia wierszy w stylu systemu Windows. Narzędzie bcp oczekuje zakończeń linii w stylu Uniksa. Użyj flagi -r, aby poinformować bcp.

Następnie kwerenda przy użyciu sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

Można również kwerendy przy użyciu SQuirreL SQL. Wykonaj kroki podobne do PostgreSQL przy użyciu sterownika JDBC programu SQL Server. Sterownik JDBC znajduje się w folderze /usr/share/java/jdbcdrivers/sqljdbc42.jar.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać omówienie artykułów, które przechadzają się po zadaniach, które obejmują proces nauki o danych na platformie Azure, zobacz [Proces nauki o danych zespołu.](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)

Aby uzyskać opis end-to-end wskazówki, które pokazują kroki w procesie nauki o danych zespołu dla określonych scenariuszy, zobacz [Team Data Science Process wskazówki.](../team-data-science-process/walkthroughs.md) W przewodnikach pokazano również, jak połączyć narzędzia i usługi w chmurze i lokalnie w przepływ pracy lub potok, aby utworzyć inteligentną aplikację.
