---
title: Dowiedz się, jak używać maszyny wirtualnej do nauki o danych systemu Linux
titleSuffix: Azure
description: Jak przeprowadzić kilka typowych zadań do nauki o danych z maszyny Wirtualnej do nauki o danych systemu Linux.
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
editor: cgronlun
ms.custom: seodec18
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: vijetaj
ms.openlocfilehash: 8100108adc7115921948d3e8f10e415bc2705fbd
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051652"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Do nauki o danych z systemem Linux maszyny wirtualnej analizy danych na platformie Azure
W tym instruktażu przedstawiono sposób wykonywania kilku typowych zadań do nauki o danych z maszyna wirtualna do nauki o danych systemu Linux. Linux Data Science Virtual Machine (dsvm dystrybucji) jest obraz maszyny wirtualnej dostępne na platformie Azure, który jest wstępnie instalowane ze zbiorem narzędzi często używane do analizy danych i uczenia maszynowego. Składniki oprogramowania są wymienione w [Aprowizowanie maszyny wirtualnej do nauki o danych Linux](linux-dsvm-intro.md) tematu. Obraz maszyny Wirtualnej ułatwia rozpoczęcie pracy, nauki o danych w ciągu kilku minut, bez konieczności instalowania i konfigurowania poszczególnych narzędzi indywidualnie. Możesz łatwo skalować w górę maszyny Wirtualnej, w razie potrzeby i zatrzymaj ją, gdy użycie. Więc ten zasób jest elastyczne i ekonomiczne.

Zadania do nauki o danych, które są przedstawione w tym przewodniku wykonaj czynności opisane w temacie [zespołu danych dla celów naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview). Ten proces obejmuje to systematyczne podejście do nauki o danych, który umożliwia zespołom naukowców skutecznie współpracować z cyklem tworzenia inteligentnych aplikacji. W procesie nauki o danych udostępnia także platforma iteracyjne do nauki o danych, który może występować przez osobę.

Analizujemy [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) zestawu danych, w tym przewodniku. Jest to zestaw wiadomości e-mail, które są oznaczone jako wiadomości-śmieci lub które pichcisz (tzn. nie są one spam), a także statystykami dotyczącymi zawartość wiadomości e-mail. Statystyki uwzględnione są omówione w następnej, ale jedną sekcję.

## <a name="prerequisites"></a>Wymagania wstępne
Przed użyciem systemu Linux maszyny wirtualnej do nauki o danych, musisz mieć następujące czynności:

* **Subskrypcji platformy Azure**. Jeśli nie masz już jeden, zobacz [Utwórz bezpłatne konto platformy Azure już dziś](https://azure.microsoft.com/free/).
* A [ **Linux maszyny Wirtualnej do analizy danych**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Aby uzyskać informacje o inicjowaniu obsługi tej maszyny Wirtualnej, zobacz [Aprowizowanie maszyny wirtualnej do nauki o danych Linux](linux-dsvm-intro.md).
* [X2Go](https://wiki.x2go.org/doku.php) zainstalowana na danym komputerze i otworzyć sesję XFCE. Aby uzyskać informacje na temat instalowania i konfigurowania **klienta programu x2go**, zobacz [Instalowanie i Konfigurowanie klienta x2go](linux-dsvm-intro.md#install-and-configure-the-x2go-client).
* Dla płynne przewijanie, Przełącz flagę gfx.xrender.enabled o: config w przeglądarce FireFox maszyn wirtualnych. [Zobacz więcej informacji znajdziesz tutaj. ](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Należy również rozważyć przełączanie *mousewheel.enable_pixel_scrolling* na wartość False. [Instrukcje w tym miejscu.](https://support.mozilla.org/questions/981140)
* **Konta usługi Azure ml**. Jeśli nie masz jeszcze jeden Załóż nowe na [strony głównej usługi Azure ml](https://studio.azureml.net/). Brak warstwę bezpłatnego użycia, aby pomóc Ci rozpocząć pracę.

## <a name="download-the-spambase-dataset"></a>Pobierz zestaw danych spambase
[Spambase](https://archive.ics.uci.edu/ml/datasets/spambase) zestaw danych jest stosunkowo mały zestaw danych zawiera tylko 4601 przykłady. Jest to wygodne rozmiar do użycia podczas wykazania, że niektóre najważniejsze funkcje maszyna wirtualna do nauki o danych, ponieważ utrzymuje wymagania dotyczące zasobów niewielkie.

> [!NOTE]
> W tym instruktażu utworzono na D2 v2 wielkości Linux maszyny wirtualnej analizy danych (CentOS wydanie). Ten rozmiar maszyny wirtualnej DSVM jest przystosowana do obsługi w procedurach przedstawionych w tym przewodniku.
>
>

Jeśli potrzebujesz więcej miejsca w magazynie, możesz utworzyć dodatkowe dyski i dołącz je do maszyny Wirtualnej. Te dyski Użyj trwałego magazynu platformy Azure, dzięki czemu ich dane są zachowywane, nawet wtedy, gdy serwer jest aprowizowany ponownie z powodu zmiany rozmiaru lub jest wyłączony. Aby dodać dysk i dołączyć go do maszyny Wirtualnej, postępuj zgodnie z instrukcjami [dodać dysk do maszyny Wirtualnej z systemem Linux](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Kroki przy użyciu interfejsu wiersza polecenia platformy Azure (Azure CLI), który jest już zainstalowany na maszyny DSVM. Dlatego te procedury może odbywać się wyłącznie z samą maszynę Wirtualną. Innym rozwiązaniem w celu zwiększenia magazynu jest użycie [usługi Azure files](../../storage/files/storage-how-to-use-files-linux.md).

Aby pobrać dane, Otwórz okno terminalu i uruchom następujące polecenie:

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Pobrany plik ma wiersz nagłówka, więc utworzyć inny plik, który ma nagłówka. Uruchom następujące polecenie, aby utworzyć plik, odpowiednie nagłówki:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Następnie złączyć dwa pliki z poleceniem:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Zestaw danych zawiera kilka typów statystyk w każdej wiadomości e-mail:

* Kolumn, takich jak ***word\_Częst\_WORD*** wskazać wartość procentowa wyrazy w wiadomości e-mail, które odpowiadają *WORD*. Na przykład jeśli *word\_Częst\_upewnij* wynosi 1, a następnie 1% wszystkie wyrazy w wiadomości e-mail zostały *wprowadzić*.
* Kolumny, takie jak ***char\_Częst\_CHAR*** wskazać wartość procentowa wszystkie znaki w wiadomości e-mail, które były *CHAR*.
* ***wielkie\_Uruchom\_długość\_najdłuższy*** jest najdłuższy okres sekwencję wielkie litery.
* ***wielkie\_Uruchom\_długość\_średni*** jest średnia długość wszystkie sekwencje wielkie litery.
* ***wielkie\_Uruchom\_długość\_całkowita*** jest łączna długość wszystkich sekwencji wielkie litery.
* ***spam*** wskazuje, czy wiadomość e-mail została uznana za spam, czy nie (1 = wiadomości-śmieci, 0 = nie spamu).

## <a name="explore-the-dataset-with-microsoft-r-open"></a>Zapoznaj się z zestawu danych za pomocą programu Microsoft R Open
Umożliwia sprawdzanie danych i wykonaj niektóre podstawowe uczenie maszynowe przy użyciu języka R. Maszyna wirtualna do nauki o danych, który jest dostarczany z [Microsoft R Open](https://mran.revolutionanalytics.com/open/) wstępnie zainstalowane. Wielowątkowe biblioteki matematyczne w tej wersji programu R oferują lepszą wydajność niż różne wersje apartamentem. Microsoft R Open także odtwarzaniem przy użyciu migawki repozytorium pakietów CRAN.

Aby pobrać kopię przykłady kodu, używana w tym przewodniku, sklonuj **Azure-maszyny-Learning — nauka o danych** repozytorium przy użyciu narzędzia git, który jest wstępnie zainstalowanych na maszynie Wirtualnej. W wierszu polecenia usługi git Uruchom polecenie:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Otwórz okno terminala i rozpocznij nową sesję R dzięki interakcyjnej konsoli R lub użyć programu RStudio wstępnie zainstalowane na maszynie.


Aby importować dane i skonfigurować środowisko, uruchom polecenie:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Aby wyświetlić podsumowanie statystyk dotyczących poszczególnych kolumn:

    summary(data)

Aby uzyskać inny widok danych:

    str(data)

To pokazuje typ każda zmienna i pierwsze kilka wartości w zestawie danych.

*Spamu* kolumna została odczytana jako liczba całkowita, ale jest faktycznie kategorii zmiennej (lub Authentication). Aby ustawić jej typ:

    data$spam <- as.factor(data$spam)

Do wykonania niektórych analizę należy użyć [ggplot2](https://ggplot2.tidyverse.org/) pakietów popularnych wydarzeniom biblioteki dla języka R, która jest już zainstalowana na maszynie Wirtualnej. Uwaga: dane podsumowania wyświetlane wcześniej, że mamy statystyki podsumowujące częstotliwość znak wykrzyknika. Spróbujmy wykreślania tych częstotliwości, w tym miejscu przy użyciu następujących poleceń:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Ponieważ zero pasek pochylenie wykres pozbądźmy go:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Brak nietrywialnymi gęstość powyżej 1, która wygląda ciekawie. Przyjrzyjmy się tylko te dane:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Następnie podzielić ją, przez które pichcisz vs spamu:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Te przykłady powinny umożliwiać wykonywanie podobne wykresy pozostałe kolumny do eksplorowania danych zawartych w nich.

## <a name="train-and-test-an-ml-model"></a>Uczenie i testowanie modeli uczenia Maszynowego
Teraz załóżmy szkolenie kilka klasyfikowanie wiadomości e-mail w zestawie danych, jako zawierającego zakres lub które pichcisz modele uczenia maszynowego. Firma Microsoft szkolenie modelu drzewa decyzyjnego i model lasu losowych w tej sekcji, a następnie przetestować ich dokładności prognozy ich.

> [!NOTE]
> Używany w poniższym kodzie pakiet rpart (partycjonowanie cyklicznego i drzew regresji) jest już zainstalowana na maszynie Wirtualnej do nauki o danych.
>
>

Pierwszy, możemy podzielić zestawu danych na zestawy szkoleniowe i testowe:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

A następnie utwórz drzewa decyzyjnego do klasyfikowania wiadomości e-mail.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Poniżej przedstawiono wyniki:

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

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

Spróbujmy również model lasu losowych. Losowe lasach wiele algorytmów uczenia i wyjściowych klasę, która jest tryb klasyfikacje ze wszystkich poszczególnych algorytmów. Zapewniają one bardziej wydajne usługi machine learning podejście, ponieważ skorygowanie tendencje modelu drzewa decyzyjnego do overfit zestaw danych szkoleniowych.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio"></a>Wdrażanie modelu w programie Azure Machine Learning Studio
[Azure Machine Learning Studio](https://studio.azureml.net/) to usługa w chmurze, która ułatwia tworzenie i wdrażanie modeli analizy predykcyjnej. Jedną z świetnych funkcji programu Azure Machine Learning Studio jest możliwość publikowania dowolnej funkcji języka R jako usługi sieci Web. Pakiet Azure Machine Learning Studio R sprawia, że wdrożenie jest łatwe do wykonania bezpośrednio z naszej sesji języka R na DSVM.

Aby wdrożyć kod drzewa decyzyjnego z poprzedniej sekcji, musisz zalogować się do usługi Azure Machine Learning Studio. Potrzebujesz Identyfikatora obszaru roboczego i token autoryzacji, aby zalogować się. Aby znaleźć te wartości i zainicjować zmienne Azure Machine Learning z nimi:

Wybierz **ustawienia** w menu po lewej stronie. Uwaga swoje **identyfikator obszaru roboczego**. ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

Wybierz **tokenach autoryzacji** obciążenie menu i notatki usługi **tokenu autoryzacji podstawowej**.![ 3](./media/linux-dsvm-walkthrough/workspace-token.png)

Obciążenia **usługi Azure ml** pakietu, a następnie ustaw wartości zmiennych z Twoim Identyfikatorem token i obszar roboczy w sesji języka r., na maszyny DSVM:

    if(!require("AzureML")) install.packages("AzureML")
    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Teraz można uproszczenie model Aby wykonywać ułatwia zaimplementowanie tej prezentacji. Wybierz trzy zmienne w najbardziej zbliżony do katalogu głównego drzewa decyzyjnego i tworzyć nowe drzewo przy użyciu tylko tych trzech zmiennych:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Potrzebujemy funkcji prognozowania, który przyjmuje funkcji jako dane wejściowe i zwraca przewidywane wartości:

    predictSpam <- function(newdata) {
      predictDF <- predict(model.rpart, newdata = newdata)
      return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }


Publikowanie funkcji predictSpam do korzystania z usługi Azure ml **publishWebService** funkcji:

    spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)


Ta funkcja przyjmuje **predictSpam** funkcji, tworzy usługę sieci web o nazwie **spamWebService** z zdefiniowane dane wejściowe i wyjściowe i zwraca informacje na temat nowego punktu końcowego.

Wyświetl szczegółowe informacje o najnowszej opublikowanej usługi sieci web, łącznie z jej punkt końcowy interfejsu API i uzyskać dostęp do kluczy przy użyciu polecenia:

    s<-tail(services(ws, name = "spamWebService"), 1)
    ep <- endpoints(ws,s)
    ep

Aby wypróbować tę funkcję w pierwszym ustawić 10 wierszy testu:

    consume(ep, smallTestSet[1:10, ])

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Głębokiego uczenia, samouczki i przewodniki

Oprócz przykładów oparte na strukturze zbiór — wszechstronne wskazówki udostępniane są również że zweryfikowany w DLVM. Przewodniki te pomagają szybko Rozpocznij korzystanie z usługi opracowywania aplikacji uczenia głębokiego w domenach, takich jak obraz i tekst/language understanding. Więcej samouczków end-to-end w różnych domenach i technologii, będą w dalszym ciągu można dodać.   


- [Uruchamianie sieci neuronowych w różnych strukturach](https://github.com/ilkarman/DeepLearningFrameworks): Kompleksowy przewodnik pokazujący sposób migrowania kodu z jednej struktury do innej. Ilustruje też sposób porównywania modelu i uruchamianie wydajności w czasie dla platform. 

- [Przewodnik dotyczący tworzenia kompleksowych rozwiązań do wykrywania produktów w obrazach](https://github.com/Azure/cortana-intelligence-product-detection-from-images): Wykrywanie obrazów to technika, która umożliwia lokalizowanie i klasyfikowanie obiektów w obrazach. Ta technologia może potencjalnie doprowadzić ogromne korzyści w wielu dziedzinach biznesowych realnym. Na przykład sprzedawcy detaliczni, można użyć tej techniki ustalenie produktu, który klientów została pobrana z półki. Informacje te pomagają z kolei magazyny Zarządzanie spis produktów. 

- [Uczenie głębokie dla audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/) ten samouczek pokazuje sposób trenowania modelu uczenia głębokiego, wykrywania zdarzeń audio na [dataset miejskich dźwięki](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html) i omówiono sposób pracy z danymi audio.

- [Klasyfikacja dokumentów tekstowych](https://github.com/anargyri/lstm_han): W tym instruktażu przedstawiono sposób tworzenia i uczenia dwóch różnych architektur sieci neuronowych: Sieć hierarchiczna zwracająca uwagę i długi czas pamięci (LSTM). Te sieci neuronowych na użytek interfejsu API biblioteki Keras głębokie uczenie do klasyfikowania dokumentów tekstowych. Keras to przód do trzech najpopularniejszych platform uczenia głębokiego: Microsoft Cognitive Toolkit, TensorFlow i Theano.

## <a name="use-other-tools-available"></a>Za pomocą innych narzędzi, które są dostępne
Pozostałe sekcje pokazują, jak korzystać z niektórych narzędzi zainstalowanych na maszynie Wirtualnej do nauki o danych systemu Linux. Oto lista narzędzi omówiono:

* XGBoost
* Python
* Jupyterhub
* Rattle
* PostgreSQL & Squirrel SQL
* SQL Server Data Warehouse

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) to narzędzie, które umożliwia szybkie i dokładne wzmocnione drzewo implementację.

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

XGBoost można również wywołać z języka python lub wiersza polecenia.

## <a name="python"></a>Python
Do tworzenia aplikacji przy użyciu języka Python dystrybucje Anaconda Python 2.7 i 3.5 zostały zainstalowane w maszyny DSVM.

> [!NOTE]
> Obejmuje dystrybucji pakietu Anaconda [Conda](https://conda.pydata.org/docs/index.html), który może służyć do tworzenia niestandardowego środowiska dla języka Python, które mają różne wersje i/lub pakietów zainstalowanych w nich.
>
>

Spróbujmy odczytać w niektórych spambase zestawu danych i klasyfikowanie wiadomości e-mail z maszynami wektor pomocy technicznej w scikit-Dowiedz się więcej:

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Jak tworzyć prognozy:

    clf.predict(X.ix[0:20, :])

Aby pokazać sposób publikowania punktu końcowego usługi Azure ml, upewnijmy się prostsze modelu trzech zmiennych ile My mieliśmy po opublikowaniu modelu R wcześniej.

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Aby opublikować model uczenia maszynowego Azure:

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
> To jest dostępna tylko dla języka python 2.7 i nie jest jeszcze obsługiwana na 3.5. Uruchom z **/anaconda/bin/python2.7**.
>
>

## <a name="jupyterhub"></a>Jupyterhub
Dystrybucja pakietu Anaconda w maszyny DSVM jest powiązana z notesu programu Jupyter, środowisko dla wielu platform, udostępniać kod języka Python, R lub Julia i analizy. Notes Jupyter jest dostępna za pośrednictwem JupyterHub. Zaloguj się przy użyciu lokalnej nazwy użytkownika systemu Linux i hasło przy ***https://\<nazwę DNS maszyny Wirtualnej lub adres IP\>: 8000 /***. Wszystkie pliki konfiguracji JupyterHub, znajdują się w katalogu **/etc/jupyterhub**.

> [!NOTE]
> Aby użyć Menedżera pakietów języka Python (za pośrednictwem `pip` polecenie) z notesu Jupyter w jądrze bieżącego następującego polecenia mogą być używane w komórce kodu, na przykład:
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> 
> 
> [!NOTE]
> Aby użyć Instalatora Conda (za pośrednictwem `conda` polecenie) z notesu Jupyter w jądrze bieżącego następującego polecenia mogą być używane w komórce kodu, na przykład:
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Kilka notesów próbki są już zainstalowane na maszynie Wirtualnej:

* Zobacz [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) notesu Python próbki.
* Zobacz [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) przykład **R** notesu.
* Zobacz [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) inny przykład **Python** notesu.

> [!NOTE]
> Języka Julia jest również dostępna z poziomu wiersza polecenia na maszynie Wirtualnej do nauki o danych systemu Linux.
>
>

## <a name="rattle"></a>Rattle
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (R analitycznych narzędzie do Dowiedz się, łatwo) jest graficznego narzędzia języka R do wyszukiwania danych. Posiada intuicyjny interfejs, który można łatwo załadować, eksplorować, przekształcania danych i tworzyć i oceniać modele.  Rattle artykułu [: Graficzny interfejs użytkownika wyszukiwania danych dla](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) języka R zawiera Instruktaż pokazujący jego funkcje.

Zainstaluj i uruchom Rattle za pomocą następujących poleceń:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Instalacja nie jest wymagane na maszyny DSVM. Ale Rattle może wyświetlić monit o zainstalowanie dodatkowych pakietów, podczas ładowania.
>
>

Rattle korzysta z interfejsu opartego na karcie. Większość kart odpowiadają procedury opisanej w [danych dla celów naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), takich jak ładowanie danych lub jego Eksplorowanie. Proces analizy danych jest pisany od lewej do prawej, za pośrednictwem karty. Ale ostatnią kartę zawiera dziennik poleceń języka R uruchamiane przez Rattle.

Aby załadować i skonfigurować zestaw danych:

* Aby załadować plik, wybierz pozycję **danych** karcie następnie
* Wybierz selektor **Filename** i wybierz polecenie **spambaseHeaders.data**.
* Aby załadować plik. Wybierz **Execute** górny wiersz przycisków. Podsumowanie każdej kolumny, w tym jego typu danych wskazany, czy dane wejściowe, obiektu docelowego lub inny typ zmiennej i liczbie unikatowych wartości powinny być widoczne.
* Poprawnie zidentyfikował rattle **spamu** kolumny jako element docelowy. Wybierz kolumnę wiadomości-śmieci, a następnie ustaw **docelowy typ danych** do **Categoric**.

Aby eksplorować dane:

* Wybierz **Eksploruj** kartę.
* Kliknij przycisk **podsumowania**, następnie **Execute**, aby wyświetlić informacje dotyczące typów zmiennych i niektóre statystyki podsumowujące.
* Aby wyświetlić inne rodzaje statystyki dotyczące każdej zmiennej, wybierz inne opcje, takie jak **opis** lub **podstawy**.

**Eksploruj** karta również służy do generowania wielu powierzchniach bezpośrednio. Do wykreślenia histogram danych:

* Wybierz **dystrybucje**.
* Sprawdź **Histogram** dla **word_freq_remove** i **word_freq_you**.
* Wybierz pozycję **Wykonaj**. Powinny być widoczne oba wykresy gęstość w oknie pojedynczy wykres, jeżeli jest jasne, że słowa "you" pojawia się znacznie częściej w wiadomościach e-mail niż "Usuń".

Krzywe korelacji są również interesujące. Aby go utworzyć:

* Wybierz **korelacji** jako **typu**, następnie
* Wybierz pozycję **Wykonaj**.
* Rattle wyświetli ostrzeżenie, że zalecane maksymalnie 40 zmiennych. Wybierz **tak** Aby wyświetlić wykres.

Istnieją pewne interesujące korelacje, które pojawiają się: "Technologia", jest ściśle związana "Http" i "labs", na przykład. Jest ona również zdecydowanie skorelowanych z "650", ponieważ numer kierunkowy ofiarodawców zestaw danych jest 650.

W oknie Eksploruj dostępnych wartości liczbowych dla korelacji między wyrazami. Warto uwagi, na przykład, że "Technologia" negatywny są skorelowane z "Twoje" i "pieniądze".

Rattle można przekształcać zestawu danych do obsługi niektórych typowych problemów. Na przykład umożliwia ponowne skalowanie funkcje, przypisują brakujące wartości, obsługi wartości odstających i usuwanie zmiennych lub uwagi z brakujących danych. Rattle można również zidentyfikować reguły skojarzenia między uwag i/lub zmienne. Te karty wykraczają poza zakres tego instruktażu wprowadzających.

Rattle można również wykonywać analizy klastra. Umożliwia wykluczenie niektórych funkcji, aby ułatwić dane wyjściowe. Na **danych** kartę, wybrać **Ignoruj** obok każdego zmiennych, z wyjątkiem tych 10 najważniejszych elementów:

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

Następnie wróć do **klastra** kartę, wybrać **KMeans**i ustaw *liczbę klastrów* do 4. Następnie **wykonania**. Wyniki są wyświetlane w oknie danych wyjściowych. Jeden klaster ma wysokich częstotliwości "george" i "http" i prawdopodobnie jest uzasadnione służbowy adres e-mail.

Aby utworzyć model uczenia maszynowego drzewa prostych decyzji:

* Wybierz **modelu** karty
* Wybierz **drzewa** jako **typu**.
* Wybierz **Execute** do wyświetlenia w drzewie w postaci tekstu w oknie danych wyjściowych.
* Wybierz **Rysowanie** przycisk, aby wyświetlić graficzną wersję. Bardzo przypomina drzewa firma uzyskany wcześniej przy użyciu *rpart*.

Jedna z funkcji nieuprzywilejowany Rattle polega na ich szybkie oceny i uruchomić kilka metod learning maszyny. Poniżej przedstawiono procedurę:

* Wybierz **wszystkich** dla **typu**.
* Wybierz pozycję **Wykonaj**.
* Po zakończeniu kliknąć każdej pojedynczej **typu**, takiej jak **SVM**i wyświetlić wyniki.
* Można również porównać wydajność modele na weryfikacji, można ustawić przy użyciu **Evaluate** kartę. Na przykład **macierzy błąd** wybór dowiesz się, macierz pomyłek, ogólny błąd i błąd uśrednionej klasy dla każdego modelu w zestawie sprawdzania poprawności.
* Można również wykreślania krzywych ROC, wykonywać analizy ważność i wykonywanie innych typów oceny modelu.

Po zakończeniu procesu tworzenia modeli wybierz **dziennika** kartę, aby wyświetlić kod R, uruchamiając Rattle podczas sesji. Możesz wybrać **wyeksportować** przycisk, aby go zapisać.

> [!NOTE]
> W bieżącej wersji Rattle znajduje się błąd. Aby zmodyfikować skrypt lub użyć go do powtórzenia kroków później, należy wstawić znak # przed poleceniem *Eksportuj ten dziennik...* w tekście dziennika.
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL & Squirrel SQL
Maszyny DSVM jest powiązana z zainstalowana baza danych PostgreSQL. PostgreSQL jest zaawansowane, typu open-source relacyjnej bazy danych. W tej sekcji przedstawiono sposób ładowania naszego zestawu danych spamu PostgreSQL i następnie wykonuje zapytania.

Przed załadowaniem danych, należy zezwolić na uwierzytelnianie hasłem z hosta. W wierszu polecenia:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

W dolnej części pliku konfiguracji są kilka wierszy, które szczegółowo dozwolone połączenia:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Zmień wiersz "IPv4 połączenia lokalnego", aby użyć md5 zamiast ident, dzięki czemu możemy zalogować się przy użyciu nazwy użytkownika i hasła:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

I ponownie uruchom usługę postgres:

    sudo systemctl restart postgresql

Aby uruchomić narzędzie psql, terminalu interactive database for PostgreSQL, jako użytkownik postgres wbudowanych uruchom następujące polecenie w wierszu:

    sudo -u postgres psql

Utwórz nowe konto użytkownika przy użyciu tej samej nazwy użytkownika, jako konto aktualnie zalogowany jako i przypisz do niego hasło w systemie Linux:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Następnie zaloguj się do narzędzia psql jako użytkownika:

    psql

I zaimportuj dane do nowej bazy danych:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Teraz sklonujemy Eksplorowanie danych i uruchomisz kilka zapytań przy użyciu **Squirrel SQL**, graficznego narzędzia, która umożliwia interakcję z bazami danych za pośrednictwem sterownika JDBC.

Aby rozpocząć, uruchom Squirrel SQL z menu aplikacji. Aby skonfigurować sterownika:

* Wybierz **Windows**, następnie **przeglądanie sterowników**.
* Kliknij prawym przyciskiem myszy **PostgreSQL** i wybierz **modyfikowania sterowników**.
* Wybierz **bardzo klasy ścieżki**, następnie **Dodaj**.
* Wprowadź ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** dla **nazwa pliku** i
* Wybierz pozycję **Otwórz**.
* Wybierz z listy sterowniki, a następnie wybierz **org.postgresql.Driver** w **Nazwa klasy**i wybierz **OK**.

Aby skonfigurować połączenie z serwerem lokalnym:

* Wybierz **Windows**, następnie **wyświetlić aliasy.**
* Wybierz **+** , aby wprowadzić nowy alias.
* Nadaj mu nazwę *bazy danych spamu*, wybierz **PostgreSQL** w **sterownika** listy rozwijanej.
* Ustaw adres URL *jdbc:postgresql://localhost/spam*.
* Wprowadź swoje *username* i *hasło*.
* Kliknij przycisk **OK**.
* Aby otworzyć **połączenia** okna, kliknij dwukrotnie ***spamu bazy danych*** aliasu.
* Wybierz przycisk **Połącz**.

Aby uruchomić kilka zapytań:

* Wybierz **SQL** kartę.
* Wprowadź prostego zapytania, na przykład `SELECT * from data;` w polu tekstowym zapytanie w górnej części karty SQL.
* Naciśnij klawisz **Wprowadź Ctrl** do jej uruchomienia. Domyślnie Squirrel SQL zwraca pierwsze 100 wierszy z zapytania.

Istnieje wiele więcej zapytań, że można uruchomić, aby zapoznać się z tych danych. Na przykład, jak działa częstotliwość wyraz *wprowadzić* różnią się w wiadomości-śmieci, które pichcisz?

    SELECT avg(word_freq_make), spam from data group by spam;

Lub co to są właściwości wiadomości e-mail, które często zawierają *3d*?

    SELECT * from data order by word_freq_3d desc;

Większość wiadomości e-mail, które mają wysokie wystąpienie *3d* są najwyraźniej wysyłania spamu, dzięki czemu może to być przydatne do tworzenia modelu predykcyjnego klasyfikowanie wiadomości e-mail.

Jeśli chcesz wykonać uczenia maszynowego z danymi przechowywanymi w bazie danych programu PostgreSQL, należy rozważyć użycie [MADlib](https://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>SQL Server Data Warehouse
Azure SQL Data Warehouse to oparta na chmurze i skalowalna w poziomie baza danych, która może przetwarzać ogromne ilości danych relacyjnych i nierelacyjnych. Aby uzyskać więcej informacji, zobacz [co to jest Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Aby połączyć się z magazynem danych i utworzenia tabeli, uruchom następujące polecenie w wierszu polecenia:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Następnie w wierszu sqlcmd:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Kopiowanie danych za pomocą narzędzia bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Końce wierszy w pobranym pliku są Windows style, ale bcp oczekuje stylu systemu UNIX, dlatego musimy sprawdzić bcp z flagą - r.
>
>

I zapytania przy użyciu narzędzia sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

Można także badać, za pomocą Squirrel SQL. Wykonać podobne kroki dla PostgreSQL, za pomocą MSSQL serwera sterownik JDBC firmy Microsoft, który można znaleźć w ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać omówienie tematów, które przeprowadzą Cię przez zadania, wchodzące w skład procesu do nauki o danych na platformie Azure, zobacz [zespołu danych dla celów naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview).

Aby uzyskać opis innych instruktaży end-to-end, które przedstawiają kroki w procesie nauki o danych zespołu dla konkretnych scenariuszy, zobacz [wskazówki dotyczące procesu do nauki o danych zespołu](../team-data-science-process/walkthroughs.md). Przewodniki pokazują również sposób łączenia w chmurze i lokalnych narzędzi i usług w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji.
