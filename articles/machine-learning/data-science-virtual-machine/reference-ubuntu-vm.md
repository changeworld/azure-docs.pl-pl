---
title: 'Odwołanie: Maszyna wirtualna nauki o danych Ubuntu'
titleSuffix: Azure Data Science Virtual Machine
description: Szczegółowe informacje na temat narzędzi zawartych w maszynie wirtualnej Ubuntu Data Science
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 830668a78929a5e6a7e131ade5c62b81e6d725c3
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631365"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Referencje: Maszyna wirtualna do nauki o danych Ubuntu (Linux)

Poniżej znajduje się lista dostępnych narzędzi na maszynie wirtualnej Ubuntu Data Science. 

## <a name="deep-learning-libraries"></a>Biblioteki uczenia głębokiego

### <a name="cntk"></a>CNTK

Microsoft Cognitive Toolkit to zestaw narzędzi do głębokiego uczenia się typu open source. Powiązania języka Python są dostępne w środowiskach główny i Conda py35. Posiada również narzędzie wiersza polecenia (CNTK), które jest już w ścieżce.

Przykładowe notesy języka Python są dostępne w udkw. Aby uruchomić próbkę podstawową w wierszu polecenia, uruchom następujące polecenia w powłoce:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Aby uzyskać więcej informacji, zobacz sekcję CNTK [gitHub](https://github.com/Microsoft/CNTK) i [wiki CNTK](https://github.com/Microsoft/CNTK/wiki).

### <a name="caffe"></a>Caffe

Caffe to struktura głębokiego uczenia się z Berkeley Vision and Learning Center. Jest on dostępny w /opt/caffe. Przykłady można znaleźć w /opt/caffe/examples.

### <a name="caffe2"></a>Caffe2

Caffe2 to struktura głębokiego uczenia się z Facebooka, która jest zbudowana na Caffe. Jest on dostępny w Pythonie 2.7 w środowisku głównym Conda. Aby go aktywować, uruchom następujące polecenie z powłoki:

```bash
source /anaconda/bin/activate root
```

Niektóre przykładowe notesy są dostępne w udkw.

### <a name="h2o"></a>H2o

H2O to szybka, w pamięci, rozproszona platforma uczenia maszynowego i analizy predykcyjnej. Pakiet Języka Python jest zainstalowany zarówno w środowiskach głównego, jak i py35 Anaconda. Zainstalowany jest również pakiet R. 

Aby otworzyć H2O z wiersza polecenia, uruchom polecenie `java -jar /dsvm/tools/h2o/current/h2o.jar`. Istnieją różne [opcje wiersza polecenia,](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) które można skonfigurować. Dostęp do interfejsu użytkownika sieci Web http://localhost:54321 flow można uzyskać, przeglądając, aby rozpocząć. Przykładowe notesy są również dostępne w udkw.

### <a name="keras"></a>Keras

Keras jest interfejsem API sieci neuronowych wysokiego poziomu w języku Python. Może działać na szczycie TensorFlow, Microsoft Cognitive Toolkit lub Theano. Jest on dostępny w środowiskach głównym i py35 Python.

### <a name="mxnet"></a>MXNet

MXNet to struktura uczenia głębokiego zaprojektowana zarówno z myślą o wydajności, jak i elastyczności. Ma powiązania R i Python zawarte w DSVM. Przykładowe notesy są zawarte w JupyterHub, a przykładowy kod jest dostępny w /dsvm/samples/mxnet.

### <a name="nvidia-digits"></a>CYFRY NVIDII

Nvidia Deep Learning GPU Training System, znany jako DIGITS, to system upraszczać typowe zadania głębokiego uczenia. Zadania te obejmują zarządzanie danymi, projektowanie i szkolenie sieci neuronowych w systemach GPU oraz monitorowanie wydajności w czasie rzeczywistym dzięki zaawansowanej wizualizacji.

DIGITS jest dostępny jako usługa o nazwie *cyfry*. Uruchom usługę i przejdź http://localhost:5000 do, aby rozpocząć.

DIGITS jest również zainstalowany jako moduł Pythona w środowisku głównym Conda.

### <a name="tensorflow"></a>TensorFlow

TensorFlow to biblioteka głębokiego uczenia się Google. Jest to biblioteka oprogramowania typu open source do obliczeń numerycznych przy użyciu wykresów przepływu danych. TensorFlow jest dostępny w środowisku py35 Python, a niektóre przykładowe notesy są zawarte w JupyterHub.

### <a name="theano"></a>Theano

Theano to biblioteka Pythona do efektywnego obliczania liczbowego. Jest on dostępny w środowiskach głównym i py35 Python. 

### <a name="torch"></a>Torch

Torch to naukowa struktura obliczeniowa z szerokim wsparciem dla algorytmów uczenia maszynowego. Jest on dostępny w /dsvm/tools/torch, a sesja interaktywna **th** i menedżer pakietów LuaRocks są dostępne w wierszu polecenia. Przykłady są dostępne w /dsvm/samples/torch.

PyTorch jest również dostępny w głównym środowisku Anaconda. Przykłady są w /dsvm/samples/pytorch.

## <a name="microsoft-machine-learning-server"></a>Serwer Microsoft Machine Learning

R jest jednym z najbardziej popularnych języków do analizy danych i uczenia maszynowego. Jeśli chcesz używać języka R do analizy, maszyna wirtualna ma serwer Microsoft Machine Learning Server z programem Microsoft R Open i biblioteką jądra matematycznego. Math Kernel Library optymalizuje operacje matematyczne wspólne w algorytmach analitycznych. Microsoft R Open jest w 100 procentach zgodny z cran r, a każda z bibliotek języka R opublikowanych w CRAN może być zainstalowana w programie Microsoft R Open. 

Usługa Machine Learning Server umożliwia skalowanie i operacjonalizację modeli języka R w usługach sieci web. Programy R można edytować w jednym z domyślnych edytorów, takich jak RStudio, vi lub Emacs. Jeśli wolisz używać edytora Emacs, został on wstępnie zainstalowany. Pakiet Emacs ESS (Emacs Speaks Statistics) upraszcza pracę z plikami R w edytorze Emacs.

Aby otworzyć konsolę R, należy wprowadzić **R** w powłoce. To polecenie przenosi cię do środowiska interaktywnego. Aby opracować program R, zazwyczaj używasz edytora, takiego jak Emacs lub vi, a następnie uruchamiasz skrypty w ramach języka R. Z RStudio, masz pełną graficzną IDE do opracowania programu R.

Istnieje również skrypt R, aby zainstalować [pakiety Top 20 R,](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) jeśli chcesz. Ten skrypt można uruchomić po przejściu do interfejsu interaktywnego języka R. Jak wspomniano wcześniej, można otworzyć ten interfejs, wprowadzając **R** w powłoce.  

## <a name="python"></a>Python

Anaconda Python jest zainstalowany w środowiskach Python 2.7 i 3.5. Środowisko 2.7 nazywa się _rootem,_ a środowisko 3.5 nazywa się _py35._ Ta dystrybucja zawiera podstawowy Python wraz z około 300 najpopularniejszych pakietów matematycznych, inżynieryjnych i analizy danych.

Środowisko py35 jest ustawieniem domyślnym. Aby aktywować środowisko główne (2.7), użyj tego polecenia:

```bash
source activate root
```

Aby ponownie aktywować środowisko py35, użyj tego polecenia:

```bash
source activate py35
```

Aby wywołać sesję interaktywną języka Python, wprowadź **python** w powłoce. 

Zainstaluj dodatkowe biblioteki Języka Python przy użyciu Conda lub pip. W przypadku pip, najpierw aktywuj odpowiednie środowisko, jeśli nie chcesz domyślnie:

```bash
source activate root
pip install <package>
```

Lub określ pełną ścieżkę do pip:

```bash
/anaconda/bin/pip install <package>
```

W przypadku firmy Conda należy zawsze określić nazwę środowiska (py35 lub katalog główny):

```bash
conda install <package> -n py35
```

Jeśli korzystasz z interfejsu graficznego lub masz skonfigurowaną przekierowywanie X11, możesz wprowadzić **pycharm,** aby otworzyć IDE PyCharm Python. Można użyć domyślnych edytorów tekstu. Ponadto można użyć Spyder, Ide Python, który jest dołączony do dystrybucji Języka Python Anaconda. Spyder potrzebuje pulpitu graficznego lub X11 przekazywania. Pulpit graficzny ma skrót do Spyder.

## <a name="jupyter-notebook"></a>Notes Jupyter

Dystrybucja Anaconda jest również wyposażona w notebook Jupyter, środowisko do udostępniania kodu i analizy. Notes Jupyter jest dostępny za pośrednictwem JupyterHub. Logujesz się przy użyciu lokalnej nazwy użytkownika i hasła systemu Linux.

Serwer notebooka Jupyter został wstępnie skonfigurowany z jądrami Python 2, Python 3 i R. Użyj ikony **pulpitu Usługi Jupyter Notebook,** aby otworzyć przeglądarkę i uzyskać dostęp do serwera notebooków. Jeśli korzystasz z maszyny Wirtualnej za pośrednictwem protokołu SSH lub klienta X2Go, [https://localhost:8000/](https://localhost:8000/)możesz również uzyskać dostęp do serwera notebooków Jupyter pod adresem .

> [!NOTE]
> Kontynuuj, jeśli otrzymasz ostrzeżenia o certyfikatach.

Dostęp do serwera notebooka Jupyter można uzyskać z dowolnego hosta. Wprowadź **https:// nazwę\<DNS maszyny Wirtualnej\>lub adres IP :8000/**.

> [!NOTE]
> Port 8000 jest domyślnie otwierany w zaporze podczas inicjowania obsługi administracyjnej maszyny Wirtualnej. 

Mamy spakowane przykładowe notesy - jeden w Pythonie i jeden w języku R. Link do przykładów można zobaczyć na stronie głównej notesu po uwierzytelnienia w notesie Jupyter przy użyciu lokalnej nazwy użytkownika i hasła systemu Linux. Nowy notes można utworzyć, wybierając pozycję **Nowy**, a następnie wybierając odpowiednie jądro języka. Jeśli nie widzisz przycisku **Nowy,** wybierz ikonę **Jupyter** w lewym górnym rogu, aby przejść do strony głównej serwera notesu.

## <a name="apache-spark-standalone"></a>Apache Spark samodzielny

Autonomiczne wystąpienie platformy Apache Spark jest preinstalowane w systemie Linux DSVM, aby ułatwić lokalne tworzenie aplikacji Platformy Spark przed przetestowaniem i wdrożeniem ich w dużych klastrach. 

Programy PySpark można uruchamiać za pośrednictwem jądra Jupyter. Po otwarciu Jupyter wybierz przycisk **Nowy,** a zobaczysz listę dostępnych jąder. **Spark — Python** to jądro PySpark, które umożliwia tworzenie aplikacji Platformy Spark przy użyciu języka Python. Można również użyć Ide Python jak PyCharm lub Spyder do tworzenia programu Spark. 

W tym autonomicznym wystąpieniu stos Platformy Spark jest uruchamiany w ramach programu klienta wywołującego. Ta funkcja sprawia, że szybsze i łatwiejsze rozwiązywanie problemów w porównaniu do tworzenia w klastrze Platformy Spark.

Jupyter zapewnia przykładowy notes PySpark. Można go znaleźć w katalogu SparkML w katalogu macierzystym Jupyter ($HOME/notebooks/SparkML/pySpark). 

Jeśli programujesz w programie R dla platformy Spark, możesz użyć programu Microsoft Machine Learning Server, SparkR lub sparklyr. 

Przed uruchomieniem w kontekście platformy Spark w programie Microsoft Machine Learning Server należy wykonać krok konfiguracji jednorazowej, aby włączyć lokalne wystąpienie systemu HDFS i Yarn z jednym węzłem. Domyślnie usługi Hadoop są zainstalowane, ale wyłączone w systemie DSVM. Aby go włączyć, należy uruchomić następujące polecenia jako root po raz pierwszy:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Możesz zatrzymać usługi związane z Hadoop, gdy nie ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```potrzebujesz ich, uruchamiając .

Katalog /dsvm/samples/MRS zawiera przykład, który pokazuje, jak tworzyć i testować serwer uczenia maszynowego firmy Microsoft w zdalnym kontekście platformy Spark (autonomiczne wystąpienie platformy Spark w systemie DSVM).

## <a name="ides-and-editors"></a>IDE i redaktorzy

Masz do wyboru kilka edytorów kodu, w tym vi/Vim, Emacs, PyCharm, RStudio i IntelliJ. 

PyCharm, RStudio i IntelliJ są edytorami graficznymi. Aby z nich korzystać, musisz zalogować się na pulpicie graficznym. Można je otworzyć za pomocą pulpitu i skrótów menu aplikacji.

Vim i Emacs są edytorami tekstowymi. W emacs, pakiet dodatku ESS ułatwia pracę z R w edytorze Emacs. Więcej informacji można znaleźć na [stronie internetowej ESS](https://ess.r-project.org/).

LaTex jest instalowany przez pakiet texlive, wraz z pakietem emacs o nazwie [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Ten pakiet upraszcza tworzenie dokumentów LaTex w Emacs.  

## <a name="databases"></a>Bazy danych

### <a name="graphical-sql-client"></a>Graficzny klient SQL

SQuirrel SQL, graficzny klient SQL, może łączyć się z różnymi bazami danych (takimi jak Microsoft SQL Server i MySQL) i uruchamiać zapytania SQL. Można uruchomić SQuirrel SQL z sesji graficznej pulpitu (za pośrednictwem klienta X2Go, na przykład) za pomocą ikony pulpitu. Lub można uruchomić klienta za pomocą następującego polecenia w powłoce:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Przed pierwszym użyciem skonfiguruj sterowniki i aliasy bazy danych. Sterowniki JDBC znajdują się na /usr/share/java/jdbcdrivers.

Aby uzyskać więcej informacji, zobacz [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Narzędzia wiersza polecenia umożliwiające dostęp do programu Microsoft SQL Server

Pakiet sterowników ODBC dla programu SQL Server jest również wyposażony w dwa narzędzia wiersza polecenia:

- **bcp**: Narzędzie bcp zbiorczo kopiuje dane między wystąpieniem programu Microsoft SQL Server a plikiem danych w formacie określonym przez użytkownika. Za pomocą narzędzia bcp można importować dużą liczbę nowych wierszy do tabel programu SQL Server lub eksportować dane z tabel do plików danych. Aby zaimportować dane do tabeli, należy użyć pliku formatu utworzonego dla tej tabeli. Lub należy zrozumieć strukturę tabeli i typy danych, które są prawidłowe dla jego kolumn.

  Aby uzyskać więcej informacji, zobacz [Łączenie z bcp](https://msdn.microsoft.com/library/hh568446.aspx).

- **sqlcmd**: Instrukcje Transact-SQL można wprowadzić za pomocą narzędzia sqlcmd. W wierszu polecenia można również wprowadzić procedury systemowe i pliki skryptów. To narzędzie używa ODBC do uruchamiania partii Transact-SQL.

  Aby uzyskać więcej informacji, zobacz [Łączenie się z sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Istnieją pewne różnice w tym narzędziu między platformami Linux i Windows. Szczegółowe informacje można znaleźć w dokumentacji.

### <a name="database-access-libraries"></a>Biblioteki dostępu do bazy danych

Biblioteki są dostępne w językach R i Pythonie w celu uzyskania dostępu do bazy danych:

* W języku R można użyć pakietu RODBC lub pakietu dplyr do wykonywania zapytań lub uruchamiania instrukcji SQL na serwerze bazy danych.
* W pythonie biblioteka pyodbc zapewnia dostęp do bazy danych z ODBC jako warstwy podstawowej.  

## <a name="azure-tools"></a>Narzędzia platformy Azure

Następujące narzędzia platformy Azure są instalowane na maszynie wirtualnej:

* **Interfejs wiersza polecenia platformy Azure:** Interfejs wiersza polecenia na platformie Azure umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą poleceń powłoki. Aby otworzyć narzędzia platformy Azure, wprowadź **pomoc platformy Azure**. Aby uzyskać więcej informacji, zobacz [stronę dokumentacji interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Eksplorator usługi Azure Storage:** Eksplorator usługi Azure Storage to narzędzie graficzne, za pomocą którego można przeglądać obiekty przechowywane na koncie usługi Azure storage oraz przekazywać i pobierać dane do i z obiektów blob platformy Azure. Dostęp do Eksploratora magazynu można uzyskać za pomocą ikony skrótów pulpitu. Można go również otworzyć z monitu powłoki, wprowadzając **storageExplorer**. Musisz być zalogowany z klienta X2Go lub skonfigurować przekazywanie X11.
* **Biblioteki platformy Azure:** Oto niektóre z wstępnie zainstalowanych bibliotek.
  
  * **Python**: Biblioteki związane z platformą Azure w *pythonie*to azure , *azureml*, *pydocumentdb*i *pyodbc*. Pierwsze trzy biblioteki umożliwiają dostęp do usług magazynu platformy Azure, usługi Azure Machine Learning i usługi Azure Cosmos DB (bazy danych NoSQL na platformie Azure). Czwarta biblioteka pyodbc (wraz ze sterownikiem Microsoft ODBC dla programu SQL Server) umożliwia dostęp do programu SQL Server, bazy danych SQL Azure i usługi Azure SQL Data Warehouse z języka Python przy użyciu interfejsu ODBC. Wprowadź **listę pipsów,** aby wyświetlić wszystkie wymienione biblioteki. Pamiętaj, aby uruchomić to polecenie w środowiskach Python 2.7 i 3.5.
  * **R**: Biblioteki związane z platformą Azure w języku R to AzureML i RODBC.
  * **Java**: Listę bibliotek Java platformy Azure można znaleźć w katalogu /dsvm/sdk/AzureSDKJava na maszynie wirtualnej. Kluczowe biblioteki to interfejsy API magazynu i zarządzania platformy Azure, usługi Azure Cosmos DB i sterowniki JDBC dla programu SQL Server.  

Dostęp do [portalu Azure](https://portal.azure.com) można uzyskać za pomocą wstępnie zainstalowanej przeglądarki Firefox. W witrynie Azure portal można tworzyć zasoby platformy Azure, zarządzać nimi i monitorować je.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Usługa Azure Machine Learning to w pełni zarządzana usługa w chmurze, która umożliwia tworzenie, wdrażanie i udostępnianie rozwiązań analizy predykcyjnej. Eksperymenty i modele można tworzyć w usłudze Azure Machine Learning studio (wersja zapoznawcza). Dostęp do niego można uzyskać za pomocą przeglądarki sieci Web na maszynie wirtualnej do nauki o danych, odwiedzając usługę [Microsoft Azure Machine Learning](https://ml.azure.com).

Po zalogowaniu się do studia usługi Azure Machine Learning można użyć kanwy eksperymentów, aby utworzyć przepływ logiczny dla algorytmów uczenia maszynowego. Masz również dostęp do notesu Jupyter, który jest hostowany w usłudze Azure Machine Learning i można bezproblemowo pracować z eksperymentów w usłudze Azure Machine Learning studio. 

Operacjonalizacja modeli uczenia maszynowego, które zostały utworzone przez zawijanie ich w interfejsie usługi sieci web. Operacjonalizacja modeli uczenia maszynowego umożliwia klientom napisanym w dowolnym języku wywoływanie prognoz z tych modeli. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Można również skompilować modele w języku R lub Python na maszynie Wirtualnej, a następnie wdrożyć je w produkcji na usłudze Azure Machine Learning. Mamy zainstalowane biblioteki w języku R **(AzureML)** i Python **(azureml),** aby włączyć tę funkcję.

Aby uzyskać informacje na temat wdrażania modeli w językach R i Python w usłudze Azure Machine Learning, zobacz [Dziesięć czynności, które można wykonać na maszynie wirtualnej nauki o danych.](vm-do-ten-things.md)

> [!NOTE]
> Te instrukcje zostały napisane dla wersji systemu Windows maszyny wirtualnej nauki o danych. Ale informacje podane tam na temat wdrażania modeli do usługi Azure Machine Learning ma zastosowanie do maszyny wirtualnej z systemem Linux.

## <a name="machine-learning-tools"></a>Narzędzia uczenia maszynowego

Maszyna wirtualna jest wyposażona w narzędzia uczenia maszynowego i algorytmy, które zostały wstępnie skompilowane i wstępnie zainstalowane lokalnie. Należą do nich:

* **Vowpal Wabbit**: Szybki algorytm uczenia się online.
* **xgboost**: Narzędzie, które zapewnia zoptymalizowane, wzmocnione algorytmy drzewa.
* **Grzechotka:** Narzędzie graficzne oparte na R do łatwej eksploracji i modelowania danych.
* **Python**: Anaconda Python jest dostarczany w pakiecie z algorytmami uczenia maszynowego z bibliotekami, takimi jak Scikit-learn. Za pomocą polecenia można zainstalować `pip install` inne biblioteki.
* **LightGBM**: szybka, rozproszona, wysokowydajna struktura zwiększania gradientów oparta na algorytmach drzewa decyzyjnego.
* **R**: Dla R. Fabrycznie zainstalowane biblioteki są dostępne dla bibliotek uczenia maszynowego lm, glm, randomForest i rpart. Inne biblioteki można zainstalować, uruchamiając to polecenie:
  
        install.packages(<lib name>)

Oto kilka dodatkowych informacji na temat pierwszych trzech narzędzi uczenia maszynowego na liście.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit to system uczenia maszynowego, który wykorzystuje techniki, takie jak online, hashing, allreduce, reductions, learning2search, active i interactive learning.

Aby uruchomić narzędzie w przykładzie podstawowym, użyj następujących poleceń:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Istnieją inne, większe dema w tym katalogu. Aby uzyskać więcej informacji na temat Vowpal Wabbit, zobacz [tę sekcję GitHub](https://github.com/JohnLangford/vowpal_wabbit) i [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>xgboost

Biblioteka xgboost została zaprojektowana i zoptymalizowana pod kątem algorytmów wzmocnionych (drzewa). Celem tej biblioteki jest przesunięcie limitów obliczeniowych maszyn do skrajności niezbędnych do zapewnienia zwiększania drzewa na dużą skalę, które jest skalowalne, przenośne i dokładne.

Jest on dostarczany jako wiersz polecenia i biblioteki R. Aby użyć tej biblioteki w językach R, można rozpocząć interaktywną sesję języka R (wprowadzając **R** w powłoce) i załadować bibliotekę.

Oto prosty przykład, który można uruchomić w proszce R:

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

Aby uruchomić wiersz polecenia xgboost, oto polecenia do uruchomienia w powłoce:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Plik .model jest zapisywany w określonym katalogu. Informacje na temat tego przykładu demo można znaleźć [na GitHubie.](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification)

Aby uzyskać więcej informacji na temat xgboost, zobacz [stronę dokumentacji xgboost](https://xgboost.readthedocs.org/en/latest/) i jego [repozytorium GitHub](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Rattle **(R** **A**nalytical **T**ool **T**o **L**zarobić **E**asily) wykorzystuje gui oparte na eksploracji danych i modelowania. Przedstawia statystyczne i wizualne podsumowania danych, przekształca dane, które mogą być łatwo modelowane, buduje zarówno nienadzorowane, jak i nadzorowane modele z danych, przedstawia wydajność modeli graficznie i ocenia nowe zestawy danych. Generuje również kod R, replikując operacje w interfejsie użytkownika, które mogą być uruchamiane bezpośrednio w R lub używane jako punkt wyjścia do dalszej analizy.

Aby uruchomić program Rattle, musisz być w sesji logowania na pulpicie graficznym. Na terminalu wprowadź **R,** aby otworzyć środowisko R. W wierszu R wprowadź następujące polecenia:

```R
library(rattle)
rattle()
```

Teraz otworzy się interfejs graficzny z zestawem kart. Skorzystaj z następujących kroków szybkiego startu w rattle, aby użyć przykładowego zestawu danych pogodowych i utworzyć model. W niektórych krokach zostanie wyświetlony monit o automatyczne zainstalowanie i załadowanie niektórych wymaganych pakietów R, które nie są jeszcze w systemie.

> [!NOTE]
> Jeśli nie masz dostępu do zainstalowania pakietu w katalogu systemowym (domyślnie), w oknie konsoli R może zostać wyświetlony monit o zainstalowanie pakietów w bibliotece osobistej. Odpowiedź **y,** jeśli widzisz te monity.

1. Wybierz pozycję **Wykonaj**.
1. Zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz użyć przykładowego zestawu danych pogodowych. Wybierz **opcję Tak,** aby załadować przykład.
1. Wybierz kartę **Model.**
1. Wybierz **opcję Wykonaj,** aby utworzyć drzewo decyzyjne.
1. Wybierz **opcję Rysuj,** aby wyświetlić drzewo decyzyjne.
1. Wybierz opcję **Las** i wybierz pozycję **Wykonaj,** aby zbudować losowy las.
1. Wybierz kartę **Oceń.**
1. Wybierz opcję **Ryzyko** i wybierz pozycję **Wykonaj,** aby wyświetlić dwa wykresy wydajności **Ryzyko (skumulowane).**
1. Wybierz **log** kartę, aby wyświetlić wygenerowany kod R dla poprzednich operacji.
   (Z powodu błędu w bieżącej wersji Rattle, **#** należy wstawić znak przed **Eksportuj ten dziennik** w tekście dziennika.)
1. Wybierz przycisk **Eksportuj,** aby zapisać plik skryptu R o nazwie *weather_script. R* do folderu macierzystego.

Możesz wyjść Rattle i R. Teraz możesz zmodyfikować wygenerowany skrypt R. Lub użyj skryptu, jak to jest, i uruchom go w dowolnym momencie, aby powtórzyć wszystko, co zostało zrobione w rattle interfejsu użytkownika. Specjalnie dla początkujących w R, jest to sposób, aby szybko zrobić analizy i uczenia maszynowego w prosty interfejs graficzny, podczas gdy automatycznie generowania kodu w R do modyfikowania lub uczenia się.

## <a name="next-steps"></a>Następne kroki

Masz dodatkowe pytania? Rozważ utworzenie [biletu pomocy technicznej](https://azure.microsoft.com/support/create-ticket/).