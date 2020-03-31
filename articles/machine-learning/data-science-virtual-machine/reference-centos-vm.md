---
title: 'Odwołanie: Maszyna wirtualna do nauki o danych CentOS'
titleSuffix: Azure Data Science Virtual Machine
description: Szczegółowe informacje na temat narzędzi zawartych w maszynie wirtualnej do nauki o danych CentOS
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: db49a9f5e0e6675d93cb58d6af9c92fac21e8b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525842"
---
# <a name="reference-centos-linux-data-science-virtual-machine"></a>Referencje: Maszyna wirtualna do nauki o danych CentOS (Linux)

Maszyna wirtualna do nauki o danych systemu Linux (DSVM) to maszyna wirtualna platformy Azure oparta na platformie CentOS. Linux DSVM jest wyposażony w kolekcję wstępnie zainstalowanych narzędzi, które można użyć do analizy danych i uczenia maszynowego. 

Kluczowe składniki oprogramowania zawarte w systemie DSVM systemu Linux to:

* System operacyjny dystrybucji Linux CentOS.
* Serwer uczenia maszynowego firmy Microsoft.
* Dystrybucja Języka Python Anaconda (wersje 3.5 i 2.7), w tym popularne biblioteki analizy danych.
* JuliaPro, wyselekcjonowana dystrybucja języka Julia oraz popularnych bibliotek naukowych i analitycznych.
* Spark Autonomiczne wystąpienie i jednowęzłowy Hadoop (HDFS, YARN).
* JupyterHub, serwer notebooka Jupyter wielodostępnego, który obsługuje jądra R, Python, PySpark i Julia.
* Eksplorator usługi Azure Storage.
* Interfejs wiersza polecenia platformy Azure, interfejs wiersza polecenia platformy Azure do zarządzania zasobami platformy Azure.
* Baza danych PostgresSQL.
* Narzędzia uczenia maszynowego:
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK), zestaw narzędzi do głębokiego uczenia się firmy Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit), szybki system uczenia maszynowego, który obsługuje techniki takie jak online, haszowanie, allreduce, redukcje, learning2search, aktywne i interaktywne uczenie się.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/), narzędzie, które zapewnia szybką i dokładną implementację wzmocnionego drzewa.
  * [Rattle](https://togaware.com/rattle/), narzędzie, które sprawia, że rozpoczęcie pracy z analizą danych i uczeniem maszynowym w R jest łatwe. Rattle oferuje zarówno eksplorację danych opartych na interfejsie GUI, jak i modelowanie przy użyciu automatycznego generowania kodu R.
* Zestaw Azure SDK w językach Java, Python, Node.js, Ruby i PHP.
* Biblioteki w językach R i Python do użycia w usłudze Azure Machine Learning i innych usługach platformy Azure.
* Narzędzia programistyczne i edytory (RStudio, PyCharm, IntelliJ, Emacs, gedit, vi).

Data science obejmuje iteracji sekwencji zadań:

1. Znajdowanie, ładowanie i wstępne przetwarzanie danych.
1. Tworzenie i testowanie modeli.
1. Wdrażanie modeli do użycia w inteligentnych aplikacjach.

Analitycy danych używają różnych narzędzi do wykonywania tych zadań. Znalezienie odpowiednich wersji oprogramowania może być czasochłonne, a następnie pobranie, skompilowanie i zainstalowanie oprogramowania.

Linux DSVM może znacznie zmniejszyć to obciążenie. Użyj systemu Linux DSVM, aby szybko rozpocząć projekt analityczny. Linux DSVM pomaga pracować nad zadaniami w różnych językach, w tym R, Python, SQL, Java i C++. Eclipse zapewnia łatwy w użyciu IDE do tworzenia i testowania kodu. Zestaw Azure SDK, zawarty w systemie DSVM, ułatwia tworzenie aplikacji przy użyciu różnych usług w systemie Linux dla platformy chmurowej firmy Microsoft. Inne języki są preinstalowane, w tym Ruby, Perl, PHP i Node.js.

Za obraz maszyny DSVM nie są naliczane opłaty za oprogramowanie. Płacisz tylko opłaty za użycie sprzętu platformy Azure, które są oceniane na podstawie rozmiaru maszyny wirtualnej, którą aprowicasz za pomocą obrazu DSVM. Aby uzyskać więcej informacji na temat opłat obliczeniowych, zobacz [maszynę wirtualną do nauki o danych dla systemu Linux (CentOS)](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) w witrynie Azure Marketplace.


## <a name="machine-learning-server"></a>Serwer uczenia maszynowego

R jest jednym z najbardziej popularnych języków do analizy danych i uczenia maszynowego. Jeśli chcesz używać języka R do analizy, dsvm ma serwer uczenia maszynowego z Microsoft R Open i math biblioteki jądra. Math Kernel Library optymalizuje typowe operacje matematyczne w algorytmach analitycznych. R Open jest w pełni kompatybilny z CRAN R. Każda z bibliotek języka R opublikowanych w CRAN może być zainstalowana na R Open. 

Za pomocą serwera uczenia maszynowego można skalować i operacjonalizacji modeli języka R w usługach sieci web. Programy R można edytować w jednym z domyślnych edytorów, takich jak RStudio, vi lub Emacs. Edytor Emacs jest preinstalowany w systemie DSVM. Pakiet Emacs ESS (Emacs Speaks Statistics) upraszcza pracę z plikami R w edytorze Emacs.

Aby otworzyć konsolę R, w powłoce wprowadź **klawisz R**. To polecenie przenosi cię do środowiska interaktywnego. Aby opracować program R, zazwyczaj używasz edytora, takiego jak Emacs lub vi, a następnie uruchamiasz skrypty w R. RStudio oferuje pełny graficzny IDE do tworzenia programu R.

Skrypt języka R, którego można użyć do zainstalowania [najlepszych pakietów 20 R,](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) znajduje się w systemie DSVM. Ten skrypt można uruchomić, gdy znajdujesz się w interfejsie interaktywnym języka R. Jak wspomniano wcześniej, aby otworzyć ten interfejs, w powłoce wprowadź **R**.  

## <a name="python"></a>Python

Anaconda Python jest instalowany w środowiskach Python 3.5 i 2.7. Środowisko 2.7 nazywa się _rootem,_ a środowisko 3.5 nazywa się _py35_. Ta dystrybucja zawiera podstawowy Python wraz z około 300 najpopularniejszych pakietów matematycznych, inżynieryjnych i analizy danych.

Środowisko py35 jest ustawieniem domyślnym. Aby aktywować środowisko główne (2.7), użyj tego polecenia:

```bash
source activate root
```

Aby ponownie aktywować środowisko py35, użyj tego polecenia:

```bash
source activate py35
```

Aby wywołać interakcyjną sesję języka Python, w powłoce wprowadź **python**. 

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

## <a name="jupyter-notebook"></a>Jupyter Notebook

Dystrybucja Anaconda jest również wyposażona w notebook Jupyter, środowisko do udostępniania kodu i analizy. Dostęp do notesu Jupyter za pośrednictwem usługi JupyterHub. Logujesz się przy użyciu lokalnej nazwy użytkownika i hasła systemu Linux.

Serwer notebooka Jupyter jest wstępnie skonfigurowany z jądrami Python 2, Python 3 i R. Użyj ikony **pulpitu Jupyter Notebook,** aby otworzyć przeglądarkę i uzyskać dostęp do serwera notebooków Jupyter. Jeśli uzyskujesz dostęp do DSVM za pośrednictwem protokołu SSH lub klienta X2Go,\/możesz również uzyskać dostęp do serwera notebooka Jupyter pod adresem https: /localhost:8000/.

> [!NOTE]
> Kontynuuj, jeśli otrzymasz ostrzeżenia o certyfikatach.

Dostęp do serwera notebooka Jupyter można uzyskać z dowolnego hosta. Wprowadź **https:\//\<Nazwa DNS DSVM lub adres\>IP :8000/**.

> [!NOTE]
> Port 8000 jest domyślnie otwierany w zaporze po zainicjowaniu obsługi administracyjnej dsvm. 

Microsoft ma spakowane przykładowe notesy, jeden w Pythonie i jeden w języku R. Link do przykładów można zobaczyć na stronie głównej notebooka Jupyter po uwierzytelnienia w notesie Jupyter przy użyciu lokalnej nazwy użytkownika i hasła systemu Linux. Aby utworzyć nowy notes, wybierz pozycję **Nowy**, a następnie wybierz jądro języka, którego chcesz użyć. Jeśli nie widzisz przycisku **Nowy,** wybierz ikonę **Jupyter** w lewym górnym rogu, aby przejść do strony głównej serwera notesu.

## <a name="spark-standalone"></a>Spark Standalone 

Wystąpienie trybu autonomicznego platformy Spark jest preinstalowane w systemie Linux DSVM, aby ułatwić lokalne tworzenie aplikacji Platformy Spark przed przetestowaniem i wdrożeniem ich w dużych klastrach. 

Programy PySpark można uruchamiać za pośrednictwem jądra Jupyter. Po otwarciu Jupyter wybierz przycisk **Nowy,** a zobaczysz listę dostępnych jąder. **Spark — Python** to jądro PySpark, które umożliwia tworzenie aplikacji Platformy Spark przy użyciu języka Python. Można również użyć Ide Python jak PyCharm lub Spyder do tworzenia programu Spark. 

W tym autonomicznym wystąpieniu stos Platformy Spark jest uruchamiany w programie klienta wywołującego. Ta funkcja sprawia, że szybsze i łatwiejsze rozwiązywanie problemów w porównaniu do tworzenia w klastrze Platformy Spark.

Jupyter zapewnia przykładowy notes PySpark. Można go znaleźć w katalogu SparkML w katalogu macierzystym Jupyter ($HOME/notebooks/SparkML/pySpark). 

Jeśli programujesz w językach R dla platformy Spark, możesz użyć serwera uczenia maszynowego, platformy SparkR lub sparklyra. 

Przed uruchomieniem w kontekście platformy Spark w serwerze uczenia maszynowego należy wykonać krok konfiguracji jednorazowej, aby włączyć lokalne wystąpienie systemu HDFS i YARN z jednym węzłem. Domyślnie usługi Hadoop są zainstalowane, ale wyłączone w systemie DSVM. Aby włączyć usługi Hadoop, uruchom następujące polecenia jako root po raz pierwszy:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Możesz zatrzymać usługi związane z Hadoop, gdy nie `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn`potrzebujesz ich, uruchamiając .

Katalog /dsvm/samples/MRS zawiera przykład, który pokazuje, jak tworzyć i testować serwer uczenia maszynowego w zdalnym kontekście platformy Spark (autonomiczne wystąpienie platformy Spark w systemie DSVM).

## <a name="ides-and-editors"></a>IDE i redaktorzy

Do wyboru jest kilka edytorów kodu, w tym vi/VIM, Emacs, gedit, PyCharm, RStudio, Eclipse, LaTeX i IntelliJ. 

* gedit, Eclipse, IntelliJ, R Studio i PyCharm są edytorami graficznymi. Aby z nich korzystać, musisz zalogować się na pulpicie graficznym. Można je otworzyć za pomocą pulpitu i skrótów menu aplikacji.

* Vim i Emacs są edytorami tekstowymi. W emacs, pakiet dodatku ESS ułatwia pracę z R w edytorze Emacs łatwiejsze. Więcej informacji można znaleźć na [stronie internetowej ESS](https://ess.r-project.org/).

* Eclipse to otwarty, rozszerzalny IDE, który obsługuje wiele języków. Eclipse IDE for Java Developers to wersja zainstalowana na DSVM. Wtyczki dla kilku popularnych języków można zainstalować, aby rozszerzyć środowisko. 

  Wtyczka Azure Toolkit for Eclipse jest zainstalowana z programem Eclipse na serwerze DSVM. Za pomocą narzędzia Azure Toolkit for Eclipse można tworzyć, tworzyć, testować i wdrażać aplikacje platformy Azure przy użyciu środowiska deweloperskiego Eclipse obsługującego języki takie jak Java.

  Zestaw Azure SDK dla języka Java jest również zainstalowany z zestawem narzędzi Azure toolkit dla programu Eclipse na serwerze DSVM. Zestaw Azure SDK dla języka Java zapewnia dostęp do różnych usług platformy Azure z wewnątrz środowiska Java. 
  
  Aby uzyskać więcej informacji, zobacz temat [Zestaw narzędzi Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

* LaTeX jest instalowany przez pakiet texlive, wraz z pakietem emacs o nazwie [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Ten pakiet upraszcza tworzenie dokumentów LaTeX w Emacs. 

## <a name="databases"></a>Bazy danych

Linux DSVM daje dostęp do kilku narzędzi bazy danych i wiersza polecenia.

### <a name="postgressql"></a>PostgreSQL

Baza danych typu open source PostgresSQL jest dostępna w systemie DSVM, z uruchomionymi usługami i ukończonymi initdb. Należy utworzyć bazy danych i użytkowników. Aby uzyskać więcej informacji, zobacz [dokumentację PostgresSQL](https://www.postgresql.org/docs/).  

### <a name="squirrel-sql"></a>SQuirreL SQL

SQuirreL SQL to graficzny klient SQL, który może łączyć się z różnymi bazami danych (w tym SQL Server, PostgresSQL i MySQL) i uruchamiać kwerendy SQL. Można uruchomić SQuirreL SQL z sesji graficznej pulpitu (za pośrednictwem klienta X2Go, na przykład) za pomocą ikony pulpitu. Lub można uruchomić klienta za pomocą następującego polecenia w powłoce:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Przed pierwszym użyciem skonfiguruj sterowniki i aliasy bazy danych. Sterowniki JDBC znajdują się na /usr/share/java/jdbcdrivers.

Aby uzyskać więcej informacji, zobacz [SQuirreL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-sql-server"></a>Narzędzia wiersza polecenia umożliwiające dostęp do programu SQL Server

Pakiet sterowników ODBC dla programu SQL Server jest również wyposażony w dwa narzędzia wiersza polecenia:

* **bcp**: Narzędzie bcp zbiorczo kopiuje dane między wystąpieniem programu SQL Server a plikiem danych w formacie określonym przez użytkownika. Za pomocą narzędzia bcp można importować dużą liczbę nowych wierszy do tabel programu SQL Server lub eksportować dane z tabel do plików danych. Aby zaimportować dane do tabeli, należy użyć pliku formatu utworzonego dla tej tabeli. Lub należy zrozumieć strukturę tabeli i typy danych, które są prawidłowe dla jego kolumn.

  Aby uzyskać więcej informacji, zobacz [Łączenie z bcp](https://msdn.microsoft.com/library/hh568446.aspx).

* **sqlcmd**: W wierszu polecenia można użyć narzędzia sqlcmd, aby wprowadzić instrukcje Transact-SQL, procedury systemowe i pliki skryptów. Narzędzie sqlcmd używa odbc do wykonywania partii Transact-SQL.

  Aby uzyskać więcej informacji, zobacz [Łączenie z sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Istnieją pewne różnice w tym narzędziu między platformami Linux i Windows. Szczegółowe informacje można znaleźć w dokumentacji.

### <a name="database-access-libraries"></a>Biblioteki dostępu do bazy danych

Biblioteki dostępu do bazy danych są dostępne w językach R i Python:

* W języku R można użyć pakietu RODBC lub pakietu dplyr do wykonywania zapytań lub uruchamiania instrukcji SQL na serwerze bazy danych.
* W pythonie biblioteka pyodbc zapewnia dostęp do bazy danych z ODBC jako warstwy podstawowej.

## <a name="azure-tools"></a>Narzędzia platformy Azure

Następujące narzędzia platformy Azure są instalowane na serwerze DSVM:

* **Interfejs wiersza polecenia platformy Azure:** Interfejs wiersza polecenia na platformie Azure umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą poleceń powłoki. Aby otworzyć narzędzia platformy Azure, wprowadź **pomoc platformy Azure**. Aby uzyskać więcej informacji, zobacz [stronę dokumentacji interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Eksplorator usługi Azure Storage:** Eksplorator usługi Azure Storage to narzędzie graficzne, za pomocą którego można przeglądać obiekty przechowywane na koncie usługi Azure storage oraz przekazywać i pobierać dane do i z obiektów blob platformy Azure. Dostęp do Eksploratora magazynu można uzyskać za pomocą ikony skrótów pulpitu. Można go również otworzyć z monitu powłoki, wprowadzając **storageExplorer**. Musisz być zalogowany z klienta X2Go lub skonfigurować przekazywanie X11.
* **Biblioteki platformy Azure:** Następujące biblioteki są preinstalowane w systemie DSVM:
  
  * **Python**: Biblioteki związane z platformą Azure w *pythonie*to azure , *azureml*, *pydocumentdb*i *pyodbc*. Pierwsze trzy biblioteki umożliwiają dostęp do usług magazynu platformy Azure, usługi Azure Machine Learning i usługi Azure Cosmos DB (bazy danych NoSQL na platformie Azure). Czwarta biblioteka pyodbc (wraz ze sterownikiem Microsoft ODBC dla programu SQL Server) umożliwia dostęp do programu SQL Server, bazy danych SQL Azure i usługi Azure SQL Data Warehouse z języka Python przy użyciu interfejsu ODBC. Wprowadź **listę pipsów,** aby wyświetlić wszystkie wymienione biblioteki. Pamiętaj, aby uruchomić to polecenie w środowiskach Python 2.7 i 3.5.
  * **R**: Biblioteki związane z platformą Azure w języku R to AzureML i RODBC.
  * **Java**: Listę bibliotek Java platformy Azure można znaleźć w katalogu /dsvm/sdk/AzureSDKJava na DSVM. Kluczowe biblioteki to interfejsy API magazynu i zarządzania platformy Azure, usługi Azure Cosmos DB i sterowniki JDBC dla programu SQL Server.  

Dostęp do [portalu Azure](https://portal.azure.com) można uzyskać za pomocą preinstalowanej przeglądarki Firefox. W witrynie Azure portal można tworzyć zasoby platformy Azure, zarządzać nimi i monitorować je.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Usługa Azure Machine Learning to w pełni zarządzana usługa w chmurze, której można używać do tworzenia, wdrażania i udostępniania rozwiązań analizy predykcyjnej. Tworzenie eksperymentów i modeli z usługi Azure Machine Learning Studio (klasyczny). Aby uzyskać dostęp do usługi Azure Machine Learning z przeglądarki sieci Web w systemie DSVM, przejdź do [usługi Microsoft Azure Machine Learning](https://studio.azureml.net).

Po zalogowaniu się do usługi Azure Machine Learning Studio (klasyczne), można użyć kanwy eksperymentów do tworzenia przepływu logicznego dla algorytmów uczenia maszynowego. Masz również dostęp do notesu Jupyter, który jest hostowany w usłudze Azure Machine Learning. Notes może bezproblemowo współpracować z eksperymentami w usłudze Azure Machine Learning Studio (klasyczny). 

Operacjonalizacja modeli uczenia maszynowego, które tworzysz, zapakowanie ich w interfejsie usługi sieci web. Operacjonalizacja modeli uczenia maszynowego umożliwia klientom napisanym w dowolnym języku wywoływanie prognoz z tych modeli. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Można również skompilować modele w języku R lub Python na DSVM, a następnie wdrożyć je w produkcji na usłudze Azure Machine Learning. Firma Microsoft zainstalowała biblioteki w językach R **(AzureML)** i Pythonie **(azureml)** w celu obsługi tej funkcji.

Aby uzyskać informacje dotyczące wdrażania modeli w językach R i Python w usłudze Azure Machine Learning, zobacz [Dziesięć czynności, które można wykonać na maszynie wirtualnej nauki o danych.](vm-do-ten-things.md)

> [!NOTE]
> Instrukcje w [Ten rzeczy, które można wykonać na maszynie wirtualnej nauki o danych](vm-do-ten-things.md) zostały napisane dla wersji systemu Windows DSVM. Jednak informacje dotyczące wdrażania modeli w usłudze Azure Machine Learning dotyczą również systemu DSVM systemu Linux.

## <a name="machine-learning-tools"></a>Narzędzia uczenia maszynowego

DSVM jest wyposażony w kilka narzędzi uczenia maszynowego i algorytmów, które są wstępnie kompilowane i preinstalowane lokalnie. Należą do nich:

* **Microsoft Cognitive Toolkit**: zestaw narzędzi do głębokiego uczenia się.
* **Vowpal Wabbit**: Szybki algorytm uczenia się online.
* **XGBoost**: Narzędzie, które zapewnia zoptymalizowane, wzmocnione algorytmy drzewa.
* **Python**: Anaconda Python jest dostarczany w pakiecie z algorytmami uczenia maszynowego z bibliotekami, takimi jak Scikit-learn. Za pomocą polecenia można zainstalować `pip install` inne biblioteki.
* **R**: Dla R. Preinstalowane biblioteki są dostępne w bogatej bibliotece funkcji uczenia maszynowego, w tym lm, glm, randomForest i rpart. Inne biblioteki można zainstalować, uruchamiając program `install.packages(<lib name>)`.

Microsoft Cognitive Toolkit, Vowpal Wabbit i XGBoost zostały omówione bardziej szczegółowo w następnych sekcjach.

### <a name="microsoft-cognitive-toolkit"></a>Zestaw narzędzi usług Microsoft Cognitive

Microsoft Cognitive Toolkit to zestaw narzędzi do głębokiego uczenia się typu open source. Jest to narzędzie wiersza polecenia (CNTK) i jest już w ścieżce.

Aby uruchomić próbkę podstawową, uruchom następujące polecenia w powłoce:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Aby uzyskać więcej informacji, zobacz [repozytorium GItHub CNTK](https://github.com/Microsoft/CNTK) i [wiki CNTK](https://github.com/Microsoft/CNTK/wiki).

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit to system uczenia maszynowego, który wykorzystuje techniki takie jak online, haszowanie, allreduce, redukcje, learning2search, aktywne i interaktywne uczenie.

Aby uruchomić narzędzie w przykładzie podstawowym, uruchom następujące polecenia:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Katalog demonstracyjny Vowpal Wabbit zawiera inne, większe wersje demonstracyjne. Aby uzyskać więcej informacji na temat Vowpal Wabbit, zobacz [repozytorium GitHub Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) i [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>XGBoost

Biblioteka XGBoost została zaprojektowana i zoptymalizowana pod kątem algorytmów promowanych (drzewa). Celem biblioteki XGBoost jest przesunięcie limitów obliczeniowych maszyn do skrajności niezbędnych do zapewnienia zwiększania drzew na dużą skalę, które jest skalowalne, przenośne i dokładne.

XGBoost jest dostarczany jako wiersz polecenia i jako biblioteka R.

Aby użyć biblioteki XGBoost w R, rozpocznij interaktywną sesję języka R (w powłoce wprowadź **klawisz R),** a następnie załaduj bibliotekę.

Oto prosty przykład, który można uruchomić w wierszu R:

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

Aby uruchomić wiersz polecenia XGBoost, uruchom następujące polecenia w powłoce:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Plik .model jest zapisywany w określonym katalogu. Aby uzyskać informacje na temat tego przykładu demonstracyjnego w usłudze GitHub, zobacz [Klasyfikacja binarna](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Aby uzyskać więcej informacji na temat XGBoost, zobacz [dokumentację XGBoost](https://xgboost.readthedocs.org/en/latest/) i [repozytorium XGBoost GitHub](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Rattle *(R* *A*nalytical *T*ool *T*o *L*earn *E*asily) wykorzystuje eksplorację i modelowanie danych oparte na GUI. Grzechotka:
- Przedstawia statystyczne i wizualne podsumowania danych.
- Przekształca dane, które można łatwo modelować.
- Tworzy modele bez nadzoru i nadzorowane na podstawie danych.
- Przedstawia wydajność modeli graficznie.
- Zdobywa nowe zestawy danych.
- Generuje kod R.
- Replikuje operacje w interfejsie użytkownika, które można uruchomić bezpośrednio w R lub używane jako punkt wyjścia do większej analizy.

Aby uruchomić aplikację Rattle, musisz zalogować się do sesji graficznej pulpitu. W terminalu wprowadź **R,** aby otworzyć środowisko R. W wierszu R wprowadź następujące polecenia:

```R
library(rattle)
rattle()
```

Zostanie otwarty interfejs graficzny z zestawem kart. Skorzystaj z następujących kroków szybkiego startu w rattle, aby użyć przykładowego zestawu danych pogodowych i utworzyć model. W niektórych krokach zostanie wyświetlony monit o automatyczne zainstalowanie i załadowanie niektórych wymaganych pakietów R, które nie są jeszcze w systemie.

> [!NOTE]
> Jeśli nie masz uprawnień do instalowania pakietu w katalogu systemowym (domyślnie), w oknie konsoli R może zostać wyświetlony monit o zainstalowanie pakietów w bibliotece osobistej. Jeśli widzisz te monity, wprowadź **y**.

1. Wybierz pozycję **Wykonaj**.
1. Okno dialogowe z monitem o załadowanie przykładowego zestawu danych pogodowych. Wybierz **opcję Tak,** aby załadować przykład.
1. Wybierz kartę **Model.**
1. Wybierz **opcję Wykonaj,** aby utworzyć drzewo decyzyjne.
1. Wybierz **opcję Rysuj,** aby wyświetlić drzewo decyzyjne.
1. Wybierz opcję **Las,** a następnie wybierz pozycję **Wykonaj,** aby utworzyć losowy las.
1. Wybierz kartę **Oceń.**
1. Wybierz opcję **Ryzyko,** a następnie wybierz pozycję **Wykonaj,** aby wyświetlić dwa wykresy wydajności **Ryzyko (skumulowane).**
1. Wybierz **log** kartę, aby wyświetlić wygenerowany kod R dla poprzednich operacji. (Z powodu błędu w bieżącej wersji Rattle, **#** należy wstawić znak przed **Eksportuj ten dziennik** w tekście dziennika.)
1. Wybierz przycisk **Eksportuj,** aby zapisać plik skryptu R o nazwie *weather_script. R* do folderu macierzystego.

Możesz wyjść Rattle i R. Teraz możesz zmodyfikować wygenerowany skrypt R. Lub użyj skryptu, jak to jest, i uruchom go w dowolnym momencie, aby powtórzyć wszystko, co zostało zrobione w rattle interfejsu użytkownika. Specjalnie dla początkujących w R, jest to sposób, aby szybko zrobić analizy i uczenia maszynowego w prosty interfejs graficzny, podczas gdy automatycznie generowania kodu w R do modyfikowania lub do nauki.

## <a name="next-steps"></a>Następne kroki

Masz dodatkowe pytania? Rozważ utworzenie [biletu pomocy technicznej](https://azure.microsoft.com/support/create-ticket/).