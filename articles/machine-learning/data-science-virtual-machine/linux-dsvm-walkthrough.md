---
title: Połączenie analiz danych z danych nauki maszyny wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Jak wykonać kilka typowych zadań nauki danych z maszyny Wirtualnej systemu Linux danych nauki.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 055d8b1c9884c9525ba15ea9508ab00a5f48a048
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Połączenie analiz danych z danych nauki maszyny wirtualnej systemu Linux na platformie Azure
W tym przewodniku przedstawiono sposób wykonywania kilka typowych zadań nauki danych z maszyny Wirtualnej systemu Linux danych nauki. Linux danych nauki maszyny wirtualnej (DSVM) jest obrazu maszyny wirtualnej na platformie Azure, który jest wstępnie zainstalowane z kolekcją narzędzi powszechnie używany do analizy danych i uczenia maszynowego. Składniki oprogramowania są wyszczególnione w [Aprowizowanie maszyny wirtualnej systemu Linux danych nauki](linux-dsvm-intro.md) tematu. Obraz maszyny Wirtualnej ułatwia rozpoczęcie pracy podczas nauki danych (w minutach), bez konieczności instalowania i konfigurowania poszczególnych narzędzi pojedynczo. Można łatwo skalowanie w górę maszynę Wirtualną, jeśli to konieczne i zatrzymaj ją nieużywane. Dlatego ten zasób jest zarówno elastyczne i ekonomiczne.

Zadania nauki danych zostało to pokazane w tym przewodniku wykonaj czynności opisane w temacie [proces nauki danych zespołu](https://azure.microsoft.com/documentation/learning-paths/data-science-process/). Ten proces obejmuje systematyczne podejście do analizy danych, umożliwiający zespoły analityków danych, efektywnie współpracować z cyklem tworzenia aplikacji inteligentnego. Proces nauki danych umożliwia również iteracyjne framework analizy danych, które można wykonać przez osobę.

Możemy przeanalizować [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) zestawu danych w ramach tego przewodnika. Jest to zestaw wiadomości e-mail, które są oznaczone jako wiadomości-śmieci lub pichcisz (tzn. nie są one spamu), a także zawiera statystykami dotyczącymi zawartości wiadomości e-mail. Statystyki uwzględnione opisano w następnych, ale jedną sekcję.

## <a name="prerequisites"></a>Wymagania wstępne
Przed użyciem maszyny wirtualnej systemu Linux danych nauki, musi mieć następujące czynności:

* **Subskrypcji platformy Azure**. Jeśli nie masz już jeden, zobacz [utworzyć bezpłatne konto platformy Azure obecnie](https://azure.microsoft.com/free/).
* A [ **nauki danych Linux VM**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Aby uzyskać informacje o inicjowaniu obsługi tej maszyny Wirtualnej, zobacz [Aprowizowanie maszyny wirtualnej systemu Linux danych nauki](linux-dsvm-intro.md).
* [X2Go](http://wiki.x2go.org/doku.php) zainstalowany na tym komputerze i otworzyć sesję XFCE. Aby uzyskać informacje o instalowaniu i konfigurowaniu **klienta X2Go**, zobacz [Instalowanie i konfigurowanie klienta X2Go](linux-dsvm-intro.md#installing-and-configuring-x2go-client).
* Dla płynne przewijania, Włącz flagę gfx.xrender.enabled o: config przeglądarki FireFox maszyn wirtualnych. [Zobacz więcej tutaj. ](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Należy również rozważyć przełączanie *mousewheel.enable_pixel_scrolling* na wartość False. [Instrukcje w tym miejscu.](https://support.mozilla.org/en-US/questions/981140)
* **Konta uczenie maszynowe Azure**. Jeśli nie masz jeszcze jeden Załóż nowe hasło w [strony głównej uczenie maszynowe Azure](https://studio.azureml.net/). Brak warstwę bezpłatna użycia, aby pomóc Ci rozpocząć.

## <a name="download-the-spambase-dataset"></a>Pobierz zestaw danych spambase
[Spambase](https://archive.ics.uci.edu/ml/datasets/spambase) zestaw danych jest stosunkowo mały zestaw danych, który zawiera tylko 4601 przykłady. Jest to wygodny rozmiar czcionki do użycia wykazać, że niektóre z kluczowych funkcji usługi wirtualna nauki danych, ponieważ utrzymuje wymagania dotyczące zasobów niewielkie.

> [!NOTE]
> Ten przewodnik został utworzony na D2 rozmiar v2 danych nauki maszyny wirtualnej systemu Linux. Ten rozmiar DSVM jest zdolny do obsługi procedury przedstawione w tym przewodniku.
>
>

Jeśli potrzebujesz więcej miejsca w magazynie, można utworzyć dodatkowe dyski i dołącz je do maszyny Wirtualnej. Te dyski Użyj trwałego magazynu Azure, więc ich dane zostaną zachowane, nawet wtedy, gdy serwer jest ponownie udostępnić z powodu zmiany rozmiaru lub jest wyłączony. Aby dodać dysk, a następnie dołącz je do maszyny Wirtualnej, postępuj zgodnie z instrukcjami [dodać dysk do maszyny Wirtualnej systemu Linux](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Kroki przy użyciu interfejsu wiersza polecenia platformy Azure (Azure CLI), który jest już zainstalowany na DSVM. Dlatego te procedury może odbywać się wyłącznie z samej maszyny Wirtualnej. Inną opcję, aby zwiększyć ilość miejsca jest użycie [pliki Azure](../../storage/files/storage-how-to-use-files-linux.md).

Aby pobrać dane, Otwórz okno terminalu i uruchom to polecenie:

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Pobrany plik nie ma wiersz nagłówka, więc warto utworzyć innego pliku, który ma nagłówka. Uruchom to polecenie, aby utworzyć plik z odpowiednią nagłówki:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Następnie połącz dwa pliki z poleceniem:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Element dataset zawiera kilka typów statystyk w każdej wiadomości e-mail:

* Kolumn, takich jak ***word\_Częst\_WORD*** wskazuje procent wyrazy w wiadomości e-mail, które odpowiadają *WORD*. Na przykład jeśli *word\_Częst\_upewnij* ma wartość 1, a następnie 1% wszystkich wyrazów w wiadomości e-mail zostały *upewnij*.
* Kolumn, takich jak ***char\_Częst\_CHAR*** wskazuje procent wszystkich znaków w wiadomości e-mail, które były *CHAR*.
* ***kapitału\_Uruchom\_długość\_najdłuższym*** najdłuższym długość sekwencji wielkimi literami.
* ***kapitału\_Uruchom\_długość\_średni*** jest średnią długość wszystkie sekwencje wielkimi literami.
* ***kapitału\_Uruchom\_długość\_całkowita*** łączna długość wszystkich sekwencji wielkimi literami.
* ***spamu*** wskazuje, czy wiadomość e-mail została uznana za spam, lub nie (1 = wiadomości-śmieci, 0 = nie spamu).

## <a name="explore-the-dataset-with-microsoft-r-open"></a>Eksploruj z Microsoft R Otwórz zestaw danych
Umożliwia sprawdzanie danych i czy niektóre podstawowe maszyny uczenie przy użyciu R. Maszyna wirtualna nauki danych jest dostarczany z [Microsoft R Otwórz](https://mran.revolutionanalytics.com/open/) wstępnie zainstalowane. Biblioteki matematyczne wielowątkowe w tej wersji języka R oferuje lepszą wydajność niż w różnych wersjach jednowątkowego. Otwórz R firmy Microsoft są także powtarzalności przy użyciu migawek sieci CRAN repozytorium pakietów.

Aby uzyskać kopię przykłady kodu, używany w tym przewodniku, klonowanie **Azure-maszyny-Learning-danych-nauki** repozytorium przy użyciu narzędzia git, który jest wstępnie zainstalowane na maszynie Wirtualnej. Z poziomu wiersza polecenia git Uruchom polecenie:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Otwórz okno terminala i rozpocznij nową sesję R z konsolą interakcyjne R.

> [!NOTE]
> Umożliwia także programu RStudio dla poniższych procedur. Do zainstalowania programu RStudio, wykonanie tego polecenia w terminalu: `./Desktop/DSVM\ tools/installRStudio.sh`
>
>

Aby zaimportować dane i konfigurowanie środowiska, uruchom polecenie:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Aby wyświetlić podsumowanie statystyk dotyczących poszczególnych kolumn:

    summary(data)

Aby uzyskać inny widok danych:

    str(data)

To pokazuje typ każdej zmiennej i pierwszy kilka wartości w zestawie danych.

*Spamu* kolumna została odczytana jako liczba całkowita, ale jest rzeczywiście kategorii zmiennej (lub współczynnik). Aby ustawić typ:

    data$spam <- as.factor(data$spam)

W tym poznawcze analizy, użyj [ggplot2](http://ggplot2.org/) pakietu popularnych biblioteki graficznych dla języka R, która jest już zainstalowana na maszynie Wirtualnej. Uwaga: z danych podsumowania wyświetlane wcześniej, będziemy mieć podsumowania statystyk od częstotliwości znakiem wykrzyknika. Załóżmy wykreślenia tych częstotliwości tutaj za pomocą następujących poleceń:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Ponieważ zero pasek pochylenie powierzchni umożliwia pozbyć się go:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Brak nieuproszczony gęstości powyżej 1, która wygląda interesujące. Przyjrzyjmy się tylko te dane:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Następnie podziel go przez pichcisz vs spamu:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Te przykłady, należy włączyć można utworzyć podobne powierzchni innych kolumn, aby eksplorować dane zawarte w nich.

## <a name="train-and-test-an-ml-model"></a>Nauczenia i przetestowania modelu usługi uczenie Maszynowe
Teraz załóżmy uczenia kilka modeli uczenia maszyny do klasyfikowania wiadomości e-mail w zestawie danych jako zawierający zakres lub pichcisz. Firma Microsoft train model drzewa decyzyjnego i model lasu losowe w tej sekcji, a następnie sprawdź ich dokładność ich prognoz.

> [!NOTE]
> Używany w poniższym kodzie pakiet rpart (cykliczne partycjonowania i drzew regresji) jest już zainstalowana na maszynie Wirtualnej analizy danych.
>
>

Pierwsza strona, możemy podzielić zestawu danych zestawy szkoleniowe i testu:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

A następnie utworzyć drzewo decyzyjne do klasyfikowania wiadomości e-mail.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

W tym miejscu jest wynikiem:

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

Aby określić, jak również wykonuje na zestaw szkoleniowy, należy użyć poniższego kodu:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Aby określić, jak również wykonuje w zestawie testów:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Spróbujmy również modelu losowe lasu. Losowe lasów wiele algorytmów uczenia i wyjściowych klasę, która jest tryb klasyfikacje ze wszystkich poszczególnych algorytmów. Udostępniają one bardziej wydajny komputer uczenia podejście, jak Usuń tendencję modelu drzewa decyzyjnego overfit zestawu danych szkoleniowych.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Wdróż model do uczenia Maszynowego Azure
[Azure Machine Learning Studio](https://studio.azureml.net/) (uczenie maszynowe Azure) to usługa w chmurze, która ułatwia tworzenie i wdrażanie modeli analizy predykcyjnej. Jedną z funkcji nieuprzywilejowany uczenie maszynowe Azure jest możliwość publikowania dowolnej funkcji R jako usługę sieci web. Pakiet R uczenie maszynowe Azure ułatwia wdrożenia bezpośrednio z naszych sesji R na DSVM.

Aby wdrożyć kod drzewa decyzyjnego z poprzedniej sekcji, musisz zalogować się do usługi Azure Machine Learning Studio. Należy swój identyfikator obszaru roboczego i token autoryzacji do logowania. Aby znaleźć te wartości i zainicjować zmienne uczenie maszynowe Azure z nimi:

Wybierz **ustawienia** w menu po lewej stronie. Uwaga użytkownika **identyfikator obszaru roboczego**. ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

Wybierz **tokeny autoryzacji** z menu narzutów i notatki z **podstawowego tokenu autoryzacji**.![ 3](./media/linux-dsvm-walkthrough/workspace-token.png)

Obciążenia **uczenie maszynowe Azure** pakietu, a następnie ustaw wartości zmiennych za pomocą Identyfikatora tokenu i obszar roboczy w sesji R w DSVM:

    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Ta funkcja pozwala uprościć modelu ułatwia wdrożenie tej prezentacji. Wybierz trzy zmienne w najbliższej do katalogu głównego drzewa decyzyjnego i utworzyć nowe drzewo przy użyciu tylko tych trzech zmiennych:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Potrzebujemy funkcji prognozowania, który przyjmuje jako dane wejściowe funkcji i zwraca przewidywane wartości:

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

Publikowanie uczenie maszynowe Azure przy użyciu funkcji predictSpam **publishWebService** funkcji:

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

Ta funkcja przyjmuje **predictSpam** funkcji, tworzy usługi sieci web o nazwie **spamWebService** z zdefiniowane wejściach i wyjściach i zwraca informacje o nowy punkt końcowy.

Wyświetl szczegóły usługi opublikowana w sieci web, w tym punkcie końcowym interfejsu API i uzyskać dostęp do kluczy przy użyciu polecenia:

    spamWebService[[2]]

Wypróbowanie pierwszego Ustaw 10 wierszy testu:

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## <a name="use-other-tools-available"></a>Użyj innych dostępnych narzędzi
Pozostałe sekcje pokazują, jak korzystać z niektórych narzędzi zainstalowanych na Maszynie wirtualnej systemu Linux danych nauki. Poniżej przedstawiono listę narzędzi omówione:

* XGBoost
* Python
* Jupyterhub
* Rattle
* PostgreSQL & Squirrel SQL
* Magazyn danych serwera SQL Server

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) to narzędzie, które udostępnia implementację szybkich i dokładnych boosted drzewa.

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
Do tworzenia aplikacji przy użyciu języka Python zostały zainstalowane w DSVM dystrybucje Anaconda Python 2.7 i 3.5.

> [!NOTE]
> Obejmuje dystrybucji Anaconda [Condas](http://conda.pydata.org/docs/index.html), które mogą służyć do tworzenia niestandardowego środowiska dla języka Python, które mają różne wersje i/lub pakietów zainstalowanych w nich.
>
>

Umożliwia odczytu w niektórych spambase zestawu danych i ich klasyfikację wiadomości e-mail pomocy technicznej wektor maszyn w scikit — Dowiedz się więcej:

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Do tworzenia prognoz:

    clf.predict(X.ix[0:20, :])

Aby pokazać, jak opublikować punktu końcowego uczenie maszynowe Azure, upewnijmy prostsze modelu trzy zmienne jak robiliśmy po opublikowaniu możemy modelu R wcześniej.

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Aby opublikować model uczenie maszynowe Azure:

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
> To jest dostępne tylko dla języka python 2.7 i nie jest jeszcze obsługiwana w 3.5. Uruchom z **/anaconda/bin/python2.7**.
>
>

## <a name="jupyterhub"></a>Jupyterhub
Rozkład Anaconda w DSVM jest dostarczany z notesu Jupyter środowisku wieloplatformowych udostępniać kod języka Python, R lub Julia i analizy. Notesu Jupyter jest dostępny za pośrednictwem JupyterHub. Zaloguj się przy użyciu lokalnego nazwę użytkownika systemu Linux i hasło na ***https://\<nazwę DNS maszyny Wirtualnej lub adres IP\>: 8000 /***. Wszystkie pliki konfiguracji dla JupyterHub znajdują się w katalogu **/etc/jupyterhub**.

Kilka przykładowych notesów są już zainstalowane na maszynie Wirtualnej:

* Zobacz [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) notesu Python próbki.
* Zobacz [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) przykładowe **R** notesu.
* Zobacz [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) dla innej próbki **Python** notesu.

> [!NOTE]
> Język Julia jest również dostępna w wierszu polecenia na Maszynie wirtualnej systemu Linux danych nauki.
>
>

## <a name="rattle"></a>Rattle
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (R analitycznych narzędzia do Dowiedz się łatwo) jest narzędziem graficznym R do wyszukiwania danych. Ma ona intuicyjny interfejs, który ułatwia obciążenia, Eksploruj i przekształcania danych i tworzenie i oceny modeli.  Artykuł [Rattle: A danych wyszukiwania graficznego interfejsu użytkownika dla R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) zawiera wskazówki, który demonstruje jego funkcje.

Zainstaluj i uruchom Rattle za pomocą następujących poleceń:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Instalacja nie jest wymagana w DSVM. Ale Rattle mogą wyświetlić monit o zainstalowanie dodatkowych pakietów podczas jego ładowania.
>
>

Rattle używa interfejsu oparte na karcie. Większość kart odpowiadają kroki w [proces nauki danych](https://azure.microsoft.com/documentation/learning-paths/data-science-process/), takie jak ładowanie danych lub jej eksplorację. Proces nauki danych pisany od lewej do prawej za pośrednictwem karty. Ale ostatniego karta zawiera dziennik poleceń R, uruchamiając Rattle.

Aby załadować i skonfigurować zestaw danych:

* Aby załadować plik, wybierz **danych** karcie następnie
* Obok pozycji wybierz selektor **Filename** i wybierz polecenie **spambaseHeaders.data**.
* Aby załadować plik. Wybierz **Execute** w górnym wierszu przycisków. Powinny pojawić się podsumowanie każdej kolumny, w tym jego typu danych zidentyfikowanych, czy dane wejściowe, cel lub innego typu zmienną i liczbę unikatowych wartości.
* Poprawnie identyfikuje rattle **spamu** kolumny jako element docelowy. Wybierz kolumnę wiadomości-śmieci, a następnie ustaw **docelowy typ danych** do **Categoric**.

Aby eksplorować dane:

* Wybierz **Eksploruj** kartę.
* Kliknij przycisk **podsumowania**, następnie **Execute**, aby wyświetlić niektóre informacje na temat typów zmiennych i niektórych podsumowania statystyk.
* Aby wyświetlić inne typy danych statystycznych dotyczących każdej zmiennej, wybierz inne opcje, takie jak **Opisz** lub **podstawy**.

**Eksploruj** karta umożliwia także wygenerować wiele interesującego powierzchni. Do wykreślenia histogram danych:

* Wybierz **dystrybucje**.
* Sprawdź **Histogram** dla **word_freq_remove** i **word_freq_you**.
* Wybierz **wykonania**. Powinny pojawić się zarówno powierzchni gęstość w oknie pojedynczego wykresu, jeżeli jest jasne, czy słowo "musisz" pojawia się częściej w wiadomościach e-mail niż "Usuń".

Krzywe korelacji są również interesujące. Aby go utworzyć:

* Wybierz **korelacji** jako **typu**, następnie
* Wybierz **wykonania**.
* Rattle wyświetli ostrzeżenie, że zaleca maksymalnie 40 zmiennych. Wybierz **tak** do wyświetlania na powierzchni.

Istnieją pewne interesujące korelacji, które znaleziona: "technologii" jest silnie korelowane "HP" i "laboratoria", np. Ponieważ numer kierunkowy dawcy zestawu danych jest 650 go są również silnie korelowane "650".

W oknie Eksploruj dostępnych wartości liczbowych dla korelacji między wyrazami. Jest interesujące Uwaga, na przykład, że "technologii" negatywnie są skorelowane z "UR" i "pieniędzy".

Rattle można przekształcać zestawu danych do obsługi niektórych typowych problemów. Na przykład umożliwia ponowne skalowanie funkcji, przypisują brakujące wartości obsługi wartości odstających i Usuń zmienne lub uwagi z brakującymi danymi. Rattle mogą też wskazać reguły skojarzenia między uwag i/lub zmienne. Te karty wykraczają poza zakres tego przewodnika wstępne.

Rattle można również wykonywać analizy klastra. Umożliwia wykluczenie niektórych funkcji, aby ułatwić dane wyjściowe. Na **danych** , wybierz pozycję **Ignoruj** obok każdego zmiennych z wyjątkiem tych pozycji 10:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spamu

Następnie przejdź wstecz do **klastra** , wybierz pozycję **KMeans**i ustaw *liczby klastrów* do 4. Następnie **wykonania**. Wyniki są wyświetlane w oknie danych wyjściowych. Jeden klaster ma wysoką częstotliwość "george" i "hp" i prawdopodobnie jest uzasadnione służbowy adres e-mail.

Do tworzenia modelu uczenia maszynowego drzewa proste decyzji:

* Wybierz **modelu** karcie
* Wybierz **drzewa** jako **typu**.
* Wybierz **Execute** do wyświetlenia w drzewie w postaci tekstu w oknie danych wyjściowych.
* Wybierz **rysowania** przycisk, aby wyświetlić graficzną wersję. Wygląda ona podobna do drzewa, możemy uzyskany wcześniej przy użyciu *rpart*.

Jedną z funkcji nieuprzywilejowany Rattle jest możliwość Uruchom kilka metod learning maszyny i szybko ich oceny. Poniżej przedstawiono procedurę:

* Wybierz **wszystkie** dla **typu**.
* Wybierz **wykonania**.
* Po zakończeniu kliknięcie dowolnego pojedynczego **typu**, takiej jak **SVM**i wyświetlić wyniki.
* Można także porównać wydajności modeli weryfikacji ustawić za pomocą **Evaluate** kartę. Na przykład **macierzy błąd** zaznaczenie zawiera macierz pomyłek, ogólny błąd i błąd uśrednionej klasy dla każdego modelu zestawu weryfikacji.
* Można również wykreślenia krzywych ROC, analiza czułości i czy innych typów oceny modelu.

Po zakończeniu budowania modeli wybierz **dziennika** kartę, aby wyświetlić kod języka R, uruchamiając Rattle podczas sesji. Możesz wybrać **wyeksportować** przycisk, aby zapisać go.

> [!NOTE]
> Jest to błąd w bieżącej wersji Rattle. Aby zmodyfikować skrypt lub używać jej później ponownie wszystkie czynności, należy wstawić przed znak # * wyeksportować ten dziennik... * w tekście dziennika.
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL & Squirrel SQL
DSVM jest dostarczany z PostgreSQL zainstalowane. PostgreSQL jest złożone, open source relacyjnej bazy danych. W tej sekcji przedstawiono sposób załadować naszym zestawie danych spamu do PostgreSQL, a następnie wykonasz zapytania.

Przed załadowaniem danych, musisz zezwolić uwierzytelniania hasła z hosta. W wierszu polecenia:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

W dolnej części pliku konfiguracji są kilka wierszy, które szczegółowo dozwolone połączenia:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Zmień wiersz "Lokalnych połączeń IPv4" do użycia zamiast ident, md5, aby firma Microsoft może logować się przy użyciu nazwy użytkownika i hasła:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

I uruchom ponownie usługę postgres:

    sudo systemctl restart postgresql

Aby uruchomić psql, terminalu interactive PostgreSQL jako użytkownik postgres wbudowanych, uruchom następujące polecenie z wiersza:

    sudo -u postgres psql

Utwórz nowe konto użytkownika przy użyciu tej samej nazwy użytkownika, jako konto systemu Linux jest obecnie zalogowany jako i nadaj mu hasło:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Następnie zaloguj się do psql jako użytkownika:

    psql

I zaimportuj dane do nowej bazy danych:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Teraz załóżmy danych i uruchom kilka zapytań przy użyciu **Squirrel SQL**, graficzne narzędzie, które umożliwia interakcję z bazami danych za pośrednictwem sterownik JDBC.

Aby rozpocząć, uruchom program Squirrel SQL z menu aplikacji. Aby skonfigurować sterownika:

* Wybierz **Windows**, następnie **Wyświetl sterowniki**.
* Kliknij prawym przyciskiem myszy **PostgreSQL** i wybierz **zmodyfikować sterownika**.
* Wybierz **bardzo klasy ścieżki**, następnie **dodać**.
* Wprowadź ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** dla **nazwa pliku** i
* Wybierz **Otwórz**.
* Wybierz z listy sterowniki, a następnie wybierz **org.postgresql.Driver** w **Nazwa klasy**i wybierz **OK**.

Aby skonfigurować połączenie z serwerem lokalnym:

* Wybierz **Windows**, następnie **wyświetlić aliasy.**
* Wybierz **+** przycisk, aby nowy alias.
* Nadaj mu nazwę *bazy danych spamu*, wybierz **PostgreSQL** w **sterownika** listy rozwijanej.
* Ustaw adres URL *jdbc:postgresql://localhost/spam*.
* Wprowadź użytkownika *username* i *hasło*.
* Kliknij przycisk **OK**.
* Aby otworzyć **połączenia** okna, kliknij dwukrotnie ***bazy danych spamu*** alias.
* Wybierz przycisk **Połącz**.

Aby uruchomić niektóre kwerendy:

* Wybierz **SQL** kartę.
* Wprowadź na przykład prostego zapytania `SELECT * from data;` w polu tekstowym kwerendy w górnej części karcie SQL.
* Naciśnij klawisz **Wprowadź Ctrl** go uruchomić. Domyślnie Squirrel SQL zwraca pierwszych 100 wierszy z zapytania.

Istnieje wiele więcej kwerend się, że można uruchomić, aby eksplorować dane. Na przykład, jak częstotliwość Word *upewnij* różnią się spamem i pichcisz?

    SELECT avg(word_freq_make), spam from data group by spam;

Lub co to są właściwości wiadomości e-mail, które często zawierają *3d*?

    SELECT * from data order by word_freq_3d desc;

Większość wiadomości e-mail, które mają wysokie wystąpienie *3d* są najwyraźniej wysyłania spamu, dlatego może być przydatne do tworzenia modelu predykcyjnego można klasyfikować wiadomości e-mail.

Jeśli chcesz wykonać uczenia maszynowego dane przechowywane w bazie danych programu PostgreSQL, rozważ użycie [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>Magazyn danych serwera SQL Server
Azure SQL Data Warehouse to oparta na chmurze i skalowalna w poziomie baza danych, która może przetwarzać ogromne ilości danych relacyjnych i nierelacyjnych. Aby uzyskać więcej informacji, zobacz [co to jest Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Aby połączyć się z magazynem danych i utworzyć tabelę, uruchom następujące polecenie w wierszu polecenia:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Następnie w wierszu sqlcmd:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Kopiowanie danych za pomocą narzędzia bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Zakończenia wierszy w pobranym pliku są styl systemu Windows, ale bcp oczekuje typu UNIX, więc musimy sprawdzić bcp, który z flagą - r.
>
>

I zapytania przy użyciu narzędzia sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

Można także zbadać z Squirrel SQL. Należy wykonać podobne kroki w przypadku PostgreSQL sterownik JDBC serwera MSSQL firmy Microsoft, za pomocą której można znaleźć w ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Kolejne kroki
Omówienie tematów, które umożliwia przeprowadzenie zadań wchodzące w skład procesu nauki danych na platformie Azure, zobacz [proces nauki danych zespołu](http://aka.ms/datascienceprocess).

Opis innych end-to-end wskazówki, które pokazują czynności w procesie nauki zespołu danych dla konkretnych scenariuszy, zobacz [wskazówki dotyczące procesu nauki danych zespołu](../team-data-science-process/walkthroughs.md). Wskazówki dotyczące przedstawiono również sposób łączenia chmurze i lokalnych narzędzi i usług w przepływie pracy lub potoku, aby utworzyć aplikację inteligentnego.
