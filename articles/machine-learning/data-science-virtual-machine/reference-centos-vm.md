---
title: 'Dokumentacja: CentOS DSVM'
description: Szczegóły dotyczące narzędzi uwzględnionych w CentOS Data Science Virtual Machine
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 0f71a8af2f7d2cfbfe43c0cfcc84cc7c08109c32
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493709"
---
# <a name="reference-centos-linux-data-science-virtual-machine"></a>Reference: CentOS (Linux) Data Science Virtual Machine

System Linux Data Science Virtual Machine (DSVM) to maszyna wirtualna platformy Azure oparta na CentOS. W systemie Linux DSVM jest dostępna kolekcja preinstalowanych narzędzi, których można użyć do analizy danych i uczenia maszynowego. 

Najważniejsze składniki oprogramowania zawarte w DSVM systemu Linux to:

* System operacyjny dystrybucji CentOS systemu Linux.
* Microsoft Machine Learning Server.
* Anaconda w języku Python (wersje 3,5 i 2,7), w tym popularne biblioteki analizy danych.
* JuliaPro, zanadzorowana dystrybucja języka Julia i popularnych bibliotek naukowych i analitycznych analiz danych.
* Wystąpienie autonomiczne Spark i Jednowęzłowe rozwiązanie Hadoop (HDFS, PRZĘDZe).
* JupyterHub, wieloużytkownikowy serwer notesu Jupyter obsługujący jądra języka R, Python, PySpark i Julia.
* Eksplorator usługi Azure Storage.
* Interfejs wiersza polecenia platformy Azure służący do zarządzania zasobami platformy Azure.
* Baza danych PostgresSQL.
* Narzędzia uczenia maszynowego:
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK) — zestaw narzędzi do uczenia głębokiego firmy Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit), szybki system uczenia maszynowego, który obsługuje techniki takie jak online, Hashing, allreduce, redukcyjny, learning2search, Active i Interactive Learning.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/), narzędzie, które zapewnia szybką i precyzyjną implementację podwyższania poziomu drzewa.
  * [Rattle](https://togaware.com/rattle/), narzędzie, które ułatwia rozpoczęcie pracy z analizami danych i uczeniem maszynowym w języku R. Rattle oferuje zarówno eksplorację, jak i modelowanie danych oparte na graficznym interfejsie użytkownika przy użyciu automatycznego generowania kodu języka R.
* Zestaw Azure SDK w językach Java, Python, Node. js, Ruby i PHP.
* Biblioteki w językach R i Python do użycia w Azure Machine Learning i innych usługach platformy Azure.
* Narzędzia i edytory programistyczne (RStudio, platformy PyCharm itd, IntelliJ, Emacs:, regedit, VI).

Nauka danych obejmuje iterację sekwencji zadań:

1. Znajdowanie, ładowanie i wstępne przetwarzanie danych.
1. Kompiluj i Testuj modele.
1. Wdróż modele do użycia w inteligentnych aplikacjach.

Naukowcy danych używają różnych narzędzi do wykonania tych zadań. Może to zająć dużo czasu, aby znaleźć prawidłowe wersje oprogramowania, a następnie pobrać, skompilować i zainstalować oprogramowanie.

DSVM systemu Linux może znacznie uprościć ten ciężar. Użyj DSVM systemu Linux, aby szybko rozpocząć projekt analizy. DSVM systemu Linux ułatwia wykonywanie zadań w różnych językach, w tym w języku R, Python, SQL, Java i C++. Funkcja przezaćmienia oferuje łatwe w użyciu środowisko IDE do tworzenia i testowania kodu. Zestaw Azure SDK, zawarty w DSVM, ułatwia tworzenie aplikacji przy użyciu różnych usług w systemie Linux dla platformy chmury firmy Microsoft. Inne języki są wstępnie zainstalowane, w tym Ruby, Perl, PHP i Node. js.

Nie ma opłat za oprogramowanie dla obrazu DSVM. Płacisz tylko za opłaty za użycie sprzętu platformy Azure, które są oceniane na podstawie rozmiaru maszyny wirtualnej, którą zainicjujesz przy użyciu obrazu DSVM. Aby uzyskać więcej informacji na temat opłat za obliczenia, zapoznaj się z [listą Data Science Virtual Machine dla systemu Linux (CentOS)](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) w witrynie Azure Marketplace.


## <a name="machine-learning-server"></a>Machine Learning Server

Język R jest jednym z najpopularniejszych języków analizy danych i uczenia maszynowego. Jeśli chcesz użyć języka R do analizy, DSVM ma Machine Learning Server z biblioteką jądra Microsoft R Open i Math. Biblioteka jądra matematycznego optymalizuje Typowe operacje matematyczne w algorytmach analitycznych. Otwarty język r jest w pełni zgodny z CRAN R. Wszystkie biblioteki języka R opublikowane w CRAN można instalować w języku R Open. 

Za pomocą Machine Learning Server można skalować i operacjonalizować modele języka R do usług sieci Web. Można edytować programy R w jednym z edytorów domyślnych, takich jak RStudio, VI lub Emacs:. Edytor Emacs: jest preinstalowany na DSVM. Pakiet Emacs: ESS (Emacs: mówiący o statystyce) upraszcza pracę z plikami języka R w edytorze emacs:.

Aby otworzyć konsolę języka R, w powłoce wpisz **R**. To polecenie umożliwia przejście do interaktywnego środowiska. Aby opracować program R, zazwyczaj używany jest Edytor, taki jak Emacs: lub VI, a następnie uruchamianie skryptów w języku R. RStudio oferuje pełny graficzny interfejs IDE służący do tworzenia programów języka R.

Skrypt języka R, którego można użyć do zainstalowania [20 najważniejszych pakietów R](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) , znajduje się w DSVM. Ten skrypt można uruchomić, gdy jesteś w interfejsie Interactive języka R. Jak wspomniano wcześniej, aby otworzyć ten interfejs, w powłoce wpisz **R**.  

## <a name="python"></a>Python

Anaconda Python jest instalowany ze środowiskami Python 3,5 i 2,7. Środowisko 2,7 jest nazywane _korzeniem_ , a środowisko 3,5 jest nazywane _py35_. Ta dystrybucja zawiera podstawowy język Python wraz z około 300 z najpopularniejszych pakietów obliczeń matematycznych, inżynieryjnych i analitycznych.

Środowisko py35 jest ustawieniem domyślnym. Aby aktywować środowisko główne (2,7), użyj tego polecenia:

```bash
source activate root
```

Aby ponownie aktywować środowisko py35, użyj tego polecenia:

```bash
source activate py35
```

Aby wywołać interaktywną sesję języka Python, w powłoce wprowadź **Python**. 

Zainstaluj dodatkowe biblioteki języka Python za pomocą Conda lub PIP. W przypadku programu PIP najpierw Aktywuj poprawne środowisko, jeśli nie chcesz go domyślnie:

```bash
source activate root
pip install <package>
```

Lub Określ pełną ścieżkę do PIP:

```bash
/anaconda/bin/pip install <package>
```

W przypadku Conda należy zawsze określić nazwę środowiska (py35 lub root):

```bash
conda install <package> -n py35
```

Jeśli korzystasz z interfejsu graficznego lub chcesz skonfigurować przekazywanie X11, możesz wprowadzić **platformy PyCharm itd** , aby otworzyć środowisko IDE platformy PyCharm itd Python. Można użyć domyślnych edytorów tekstu. Ponadto można użyć Spyder, środowiska IDE języka Python, które jest powiązane z dystrybucją języka Python Anaconda. Spyder potrzebuje graficznego pulpitu lub X11ego przesyłania dalej. Pulpit graficzny ma skrót do Spyder.

## <a name="jupyter-notebook"></a>Jupyter Notebook

Dystrybucja Anaconda zawiera również Jupyter Notebook środowisko do udostępniania kodu i analizy. Uzyskaj dostęp do Jupyter Notebook za pomocą JupyterHub. Zaloguj się przy użyciu nazwy użytkownika i hasła lokalnego systemu Linux.

Serwer Jupyter Notebook jest wstępnie skonfigurowany przy użyciu jądra Python 2, Python 3 i języka R. Aby otworzyć przeglądarkę i uzyskać dostęp do serwera Jupyter Notebook, użyj ikony **Jupyter Notebook** pulpicie. Jeśli uzyskujesz dostęp do DSVM za pośrednictwem protokołu SSH lub klienta X2Go, możesz również uzyskać dostęp do serwera Jupyter Notebook na https:\//localhost: 8000/.

> [!NOTE]
> Kontynuuj, jeśli otrzymasz ostrzeżenia dotyczące certyfikatów.

Można uzyskać dostęp do serwera notesu Jupyter z dowolnego hosta. Wprowadź **https:\//\<DSVM nazwę DNS lub adres IP\>: 8000/** .

> [!NOTE]
> Port 8000 jest domyślnie otwarty w zaporze po zainicjowaniu obsługi administracyjnej DSVM. 

Firma Microsoft udostępnia spakowane przykładowe notesy, jeden w języku Python i jeden w języku R. Po uwierzytelnieniu do Jupyter Notebook przy użyciu lokalnej nazwy użytkownika i hasła w systemie Linux można zobaczyć link do przykładów na stronie głównej Jupyter Notebook. Aby utworzyć nowy Notes, wybierz pozycję **Nowy**, a następnie wybierz jądro języka, którego chcesz użyć. Jeśli przycisk **Nowy** nie jest widoczny, wybierz ikonę **Jupyter** w lewym górnym rogu, aby przejść do strony głównej serwera notesu.

## <a name="spark-standalone"></a>Platforma Spark — autonomiczna 

Wystąpienie trybu autonomicznego platformy Spark jest preinstalowane na DSVM systemu Linux, aby ułatwić tworzenie aplikacji platformy Spark lokalnie przed przetestowaniem i wdrażaniem ich w dużych klastrach. 

Można uruchamiać programy PySpark za pomocą jądra Jupyter. Po otwarciu Jupyter wybierz przycisk **Nowy** i powinna zostać wyświetlona lista dostępnych jądra. **Spark-Python** to jądro PySpark, które umożliwia tworzenie aplikacji platformy Spark przy użyciu języka Python. Do skompilowania programu Spark można także użyć środowiska IDE języka Python, takiego jak platformy PyCharm itd lub Spyder. 

W tym wystąpieniu autonomicznym stos Spark działa w programie wywołującym klienta. Ta funkcja przyspiesza i ułatwia rozwiązywanie problemów w porównaniu z tworzeniem w klastrze Spark.

Jupyter udostępnia przykładowy Notes PySpark. Można go znaleźć w katalogu SparkML w katalogu głównym Jupyter ($HOME/notebooks/SparkML/pySpark). 

W przypadku programowania w języku R dla platformy Spark można użyć Machine Learning Server, Spark lub sparklyr. 

Przed uruchomieniem w kontekście platformy Spark w Machine Learning Server należy wykonać jednorazowy krok konfiguracji w celu włączenia lokalnego jednego węzła usługi Hadoop HDFS i wystąpienia nici. Domyślnie usługi Hadoop są zainstalowane, ale wyłączone na DSVM. Aby włączyć usługi Hadoop, uruchom następujące polecenia jako główne po raz pierwszy:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Usługi związane z usługą Hadoop można zatrzymać, gdy nie są potrzebne, uruchamiając `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn`.

Katalog/dsvm/samples/MRS zawiera przykład demonstrujący sposób tworzenia i testowania Machine Learning Server w zdalnym kontekście Spark (autonomiczne wystąpienie platformy Spark w DSVM).

## <a name="ides-and-editors"></a>Środowisk IDE i redaktorzy

Można wybrać spośród kilku edytorów kodu, w tym VI/VIM, Emacs:, regedit, platformy PyCharm itd, RStudio, zaćmienie, lateks i IntelliJ. 

* Języki regedit, zaćmienie, IntelliJ, R Studio i platformy PyCharm itd są edytorami graficznymi. Aby móc z nich korzystać, musisz się zalogować na pulpicie graficznym. Można je otworzyć przy użyciu skrótów menu programu Desktop i aplikacji.

* Vim i Emacs: są edytorami tekstowymi. W Emacs:, pakiet dodatku ESS udostępnia pracę z językiem R w edytorze emacs:. Więcej informacji można znaleźć w [witrynie sieci Web ESS](https://ess.r-project.org/).

* Zaćmienie to rozszerzalne środowisko IDE, które obsługuje wiele języków. Zaćmienie IDE dla deweloperów języka Java jest wersją zainstalowaną na DSVM. Wtyczki można zainstalować dla kilku popularnych języków, aby zwiększyć środowisko. 

  Wtyczka Azure Toolkit for Eclipse jest instalowana z funkcją przezaćmienia na DSVM. Za pomocą Azure Toolkit for Eclipse można tworzyć, opracowywać, testować i wdrażać aplikacje platformy Azure przy użyciu środowiska programistycznego zaćmienie, które obsługuje języki takie jak Java.

  Zestaw Azure SDK dla języka Java jest również instalowany z Azure Toolkit for Eclipse na DSVM. Zestaw Azure SDK dla języka Java zapewnia dostęp do różnych usług platformy Azure z wnętrza środowiska Java. 
  
  Aby uzyskać więcej informacji, zobacz [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

* Wartość lateks jest instalowana za pomocą pakietu TeXLive wraz z pakietem dodatku Emacs: o nazwie [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Ten pakiet upraszcza tworzenie dokumentów lateks w Emacs:. 

## <a name="databases"></a>Bazy danych

DSVM systemu Linux zapewnia dostęp do kilku narzędzi wiersza polecenia i bazy danych.

### <a name="postgressql"></a>PostgresSQL

Baza danych open source PostgresSQL jest dostępna w DSVM, z usługami uruchomionymi i initdb zakończonymi pomyślnie. Należy utworzyć bazy danych i użytkowników. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją PostgresSQL](https://www.postgresql.org/docs/).  

### <a name="squirrel-sql"></a>SQuirreL SQL

SQuirreL SQL to graficzny klient SQL, który może łączyć się z różnymi bazami danych (w tym SQL Server, PostgresSQL i MySQL) i uruchamiać zapytania SQL. Program SQuirreL SQL można uruchomić z graficznej sesji pulpitu (na przykład za pośrednictwem klienta X2Go) przy użyciu ikony pulpitu. Można też uruchomić klienta programu za pomocą następującego polecenia w powłoce:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Przed pierwszym użyciem Skonfiguruj sterowniki i aliasy baz danych. Sterowniki JDBC znajdują się w/usr/share/Java/jdbcdrivers.

Aby uzyskać więcej informacji, zobacz [Squirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-sql-server"></a>Narzędzia wiersza polecenia do uzyskiwania dostępu SQL Server

Pakiet sterowników ODBC dla SQL Server również zawiera dwa narzędzia wiersza polecenia:

* **BCP**: Narzędzie bcp zbiorczo kopiuje dane między wystąpieniem SQL Server i plikiem danych w formacie określonym przez użytkownika. Narzędzia bcp można użyć do zaimportowania dużej liczby nowych wierszy do tabel SQL Server lub do eksportowania danych z tabel do plików danych. Aby zaimportować dane do tabeli, należy użyć pliku formatu utworzonego dla tej tabeli. Lub należy zrozumieć strukturę tabeli i typy danych, które są prawidłowe dla jego kolumn.

  Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia za pomocą narzędzia bcp](https://msdn.microsoft.com/library/hh568446.aspx).

* **sqlcmd**: można użyć narzędzia sqlcmd do wprowadzania instrukcji języka Transact-SQL, procedur systemowych i plików skryptów w wierszu polecenia. Narzędzie sqlcmd używa ODBC do wykonywania partii Transact-SQL.

  Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia przy użyciu narzędzia sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Istnieją pewne różnice w tym narzędziu między platformami Linux i Windows. Zapoznaj się z dokumentacją, aby uzyskać szczegółowe informacje.

### <a name="database-access-libraries"></a>Biblioteki dostępu do bazy danych

Biblioteki dostępu do baz danych są dostępne w językach R i Python:

* W języku R można użyć pakietu RODBC lub pakietu dplyr do wykonywania zapytań lub uruchamiania instrukcji SQL na serwerze bazy danych.
* W języku Python Biblioteka moduł pyodbc zapewnia dostęp do bazy danych za pomocą ODBC jako warstwy źródłowej.

## <a name="azure-tools"></a>Narzędzia platformy Azure

Następujące narzędzia platformy Azure są zainstalowane na DSVM:

* Interfejs wiersza polecenia **platformy Azure**: za pomocą poleceń powłoki można tworzyć zasoby platformy Azure i zarządzać nimi za pomocą polecenia. Aby otworzyć narzędzia platformy Azure, wprowadź **Pomoc platformy Azure**. Aby uzyskać więcej informacji, zobacz [stronę dokumentacji interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Eksplorator usługi Azure Storage**: Eksplorator usługi Azure Storage to graficzne narzędzie umożliwiające przeglądanie obiektów przechowywanych na koncie usługi Azure Storage oraz przekazywanie i pobieranie danych do i z obiektów blob platformy Azure. Dostęp do Eksplorator usługi Storage można uzyskać za pomocą ikony skrótu pulpitu. Możesz również otworzyć ją z poziomu monitu powłoki, wprowadzając **StorageExplorer**. Musisz zalogować się z klienta X2Go lub skonfigurować przekazywanie X11.
* **Biblioteki platformy Azure**: następujące biblioteki są PREINSTALOWANE na DSVM:
  
  * **Python**: biblioteki związane z platformą Azure w języku Python to *Azure* *, Azure,* *pydocumentdb*i *moduł pyodbc*. Za pomocą pierwszych trzech bibliotek można uzyskać dostęp do usług Azure Storage, Azure Machine Learning i Azure Cosmos DB (baza danych NoSQL na platformie Azure). Czwarta biblioteka, moduł pyodbc (wraz ze sterownikiem Microsoft ODBC dla SQL Server) umożliwia dostęp do SQL Server, Azure SQL Database i Azure SQL Data Warehouse z języka Python przy użyciu interfejsu ODBC. Wprowadź **listę PIP** , aby wyświetlić wszystkie wymienione biblioteki. Upewnij się, że uruchamiasz to polecenie w środowiskach Python 2,7 i 3,5.
  * **R**: biblioteki związane z platformą Azure w języku R to Azure i RODBC.
  * **Java**: listę bibliotek Java platformy Azure można znaleźć w katalogu/DSVM/SDK/AZURESDKJAVA na dsvm. Biblioteki kluczy to usługi Azure Storage i interfejsy API zarządzania, Azure Cosmos DB i sterowniki JDBC dla SQL Server.  

Dostęp do [Azure Portal](https://portal.azure.com) można uzyskać za pomocą wstępnie zainstalowanej przeglądarki Firefox. W Azure Portal można tworzyć i monitorować zasoby platformy Azure oraz zarządzać nimi.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning to w pełni zarządzana usługa w chmurze, która służy do kompilowania, wdrażania i udostępniania rozwiązań do analizy predykcyjnej. Możesz tworzyć eksperymenty i modele z Azure Machine Learning Studio (klasyczne). Aby uzyskać dostęp do Azure Machine Learning z przeglądarki sieci Web na DSVM, przejdź do [Microsoft Azure Machine Learning](https://studio.azureml.net).

Po zalogowaniu się do Azure Machine Learning Studio (klasyczny) można użyć kanwy eksperymentowania, aby utworzyć przepływ logiczny dla algorytmów uczenia maszynowego. Masz również dostęp do Jupyter Notebook hostowanego w Azure Machine Learning. Notes może bezproblemowo współpracować z eksperymentami w Azure Machine Learning Studio (klasyczny). 

Operacjonalizować modele uczenia maszynowego, które tworzysz, umieszczając je w interfejsie usługi sieci Web. Modele uczenia maszynowego operacjonalizowania umożliwiają klientom w dowolnym języku wywoływanie prognoz z tych modeli. Aby uzyskać więcej informacji, zobacz [dokumentację Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Możesz również kompilować modele w języku R lub Python w DSVM, a następnie wdrożyć je w środowisku produkcyjnym na Azure Machine Learning. Firma Microsoft zainstalowała biblioteki w języku R (**Azure**) i Python (**Azure**) w celu obsługi tej funkcji.

Aby uzyskać informacje dotyczące sposobu wdrażania modeli w języku R i Python do Azure Machine Learning, zobacz [dziesięć rzeczy, które możesz wykonać na Data Science Virtual Machine](vm-do-ten-things.md).

> [!NOTE]
> Instrukcje zawarte w [dziesięciu czynnościach, które można wykonać na Data Science Virtual Machine](vm-do-ten-things.md) zostały wprowadzone dla systemu Windows w wersji DSVM. Jednak informacje o wdrażaniu modeli do Azure Machine Learning dotyczą również systemu Linux DSVM.

## <a name="machine-learning-tools"></a>Narzędzia uczenia maszynowego

DSVM zawiera kilka narzędzi i algorytmów uczenia maszynowego, które są wstępnie skompilowane i wstępnie zainstalowane lokalnie. Należą do nich:

* **Microsoft Cognitive Toolkit**: zestaw narzędzi do uczenia głębokiego.
* **Vowpal Wabbit**: Krótki algorytm uczenia w trybie online.
* **XGBoost**: narzędzie, które zapewnia zoptymalizowane, podwyższające algorytmy drzewa.
* **Python**: Anaconda Python jest powiązany z algorytmami uczenia maszynowego z bibliotekami, takimi jak Scikit — uczenie się. Inne biblioteki można zainstalować za pomocą polecenia `pip install`.
* **R**: Zaawansowana biblioteka funkcji uczenia maszynowego jest dostępna dla języka R. wstępnie zainstalowane biblioteki to LM, GLM, randomForest i rpart. Możesz zainstalować inne biblioteki, uruchamiając `install.packages(<lib name>)`.

Microsoft Cognitive Toolkit, Vowpal Wabbit i XGBoost zostały omówione bardziej szczegółowo w następnych sekcjach.

### <a name="microsoft-cognitive-toolkit"></a>Zestaw narzędzi usług Microsoft Cognitive

Microsoft Cognitive Toolkit to zestaw narzędzi do uczenia głębokiego "open source". Jest to narzędzie wiersza polecenia (CNTK) i znajduje się już w ścieżce.

Aby uruchomić podstawowy przykład, uruchom następujące polecenia w powłoce:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Aby uzyskać więcej informacji, zobacz [REPOZYTORIUM CNTK GitHub](https://github.com/Microsoft/CNTK) i witrynę [typu wiki CNTK](https://github.com/Microsoft/CNTK/wiki).

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit to system uczenia maszynowego, który używa technik takich jak online, Hashing, allreduce, redukcyjny, learning2search, Active i Interactive Learning.

Aby uruchomić narzędzie w podstawowym przykładzie, uruchom następujące polecenia:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Katalog demonstracyjny Vowpal Wabbit zawiera inne, większe pokazy. Aby uzyskać więcej informacji na temat Vowpal Wabbit, zapoznaj się z [repozytorium usługi GitHub Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) i witryną [typu wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki)Vowpal Wabbit.

### <a name="xgboost"></a>XGBoost

Biblioteka XGBoost jest zaprojektowana i zoptymalizowana pod kątem podwyższania poziomu (drzewa). Celem biblioteki XGBoost jest wypchnięcie ograniczeń obliczeniowych maszyn do skrajnych wymagań w celu zapewnienia skalowalności, przenośnego i dokładnego wzrostu drzewa na dużą skalę.

XGBoost jest udostępniana jako wiersz polecenia i jako biblioteka języka R.

Aby użyć biblioteki XGBoost w języku R, uruchom interaktywną sesję języka R (w powłoce, wprowadź **R**), a następnie załaduj bibliotekę.

Oto prosty przykład, który można uruchomić w wierszu polecenia R:

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

Plik. model jest zapisywana w określonym katalogu. Aby uzyskać informacje na temat tego przykładu demonstracyjnego w witrynie GitHub, zobacz sekcję [Klasyfikacja danych binarnych](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Aby uzyskać więcej informacji na temat XGBoost, zobacz [dokumentację XGBoost](https://xgboost.readthedocs.org/en/latest/) i [repozytorium XGBoost](https://github.com/dmlc/xgboost)w witrynie GitHub.

### <a name="rattle"></a>Rattle

Rattle (*R* *Nalityczny* *t*OOL *t*o *L*zdobyć *E*asily) korzysta z eksplorowania i modelowania danych opartych na graficznym interfejsie użytkownika. Rattle:
- Prezentuje statystyczne i wizualne podsumowania danych.
- Przekształca dane, które można łatwo modelować.
- Kompiluje zarówno nienadzorowane, jak i nadzorowane modele na podstawie danych.
- Przedstawia wydajność modeli graficznie.
- Ocenia nowe zestawy danych.
- Generuje kod R.
- Replikuje operacje w interfejsie użytkownika, które można uruchomić bezpośrednio w języku R lub użyć jako punktu wyjścia do dalszej analizy.

Do uruchomienia Rattle należy zalogować się do sesji pulpitu graficznego. W terminalu wprowadź **r** , aby otworzyć środowisko języka r. W wierszu polecenia języka R wprowadź następujące polecenia:

```R
library(rattle)
rattle()
```

Zostanie otwarty interfejs graficzny, który ma zestaw kart. Poniższe kroki szybkiego startu w programie Rattle umożliwiają użycie przykładowego zestawu danych pogody i skompilowanie modelu. W niektórych krokach zostanie wyświetlony monit o automatyczne zainstalowanie i załadowanie niektórych wymaganych pakietów języka R, które nie znajdują się jeszcze w systemie.

> [!NOTE]
> Jeśli nie masz uprawnień do zainstalowania pakietu w katalogu systemowym (domyślnie), w oknie konsoli języka R może zostać wyświetlony monit o zainstalowanie pakietów w bibliotece osobistej. Jeśli zobaczysz te polecenia, wprowadź **y**.

1. Wybierz pozycję **Wykonaj**.
1. Zostanie wyświetlone okno dialogowe z prośbą o załadowanie przykładowego zestawu danych pogody. Wybierz pozycję **tak** , aby załadować przykład.
1. Wybierz kartę **model** .
1. Wybierz pozycję **Execute (wykonaj** ), aby skompilować drzewo decyzyjne.
1. Wybierz pozycję **Rysuj** , aby wyświetlić drzewo decyzyjne.
1. Wybierz opcję **Las** , a następnie wybierz polecenie **Execute (wykonaj** ), aby skompilować losowo Las.
1. Wybierz kartę **szacowanie** .
1. Wybierz opcję **ryzykowne** , a następnie wybierz polecenie **Execute (wykonaj** ), aby wyświetlić wykresy wydajności dwa **ryzyka (skumulowane)** .
1. Wybierz kartę **Dziennik** , aby wyświetlić wygenerowany kod R dla powyższych operacji. (Z powodu błędu w bieżącej wersji Rattle należy wstawić znak **#** przed **eksportem tego dziennika** do tekstu dziennika).
1. Wybierz przycisk **Eksportuj** , aby zapisać plik skryptu języka R o nazwie *weather_script. R* do folderu macierzystego.

Możesz wyjść z Rattle i R. Teraz można zmodyfikować wygenerowany skrypt języka R. Można też użyć skryptu w taki sposób, aby był on uruchamiany w dowolnym momencie, aby powtórzyć wszystko, co zostało zrobione w interfejsie użytkownika Rattle. Szczególnie w przypadku początkujących w języku R, jest to sposób, aby szybko przeprowadzić analizę i uczenie maszynowe w prostym interfejsie graficznym, podczas gdy automatycznie generuje kod w języku R w celu zmodyfikowania lub uczenia się.

## <a name="next-steps"></a>Następne kroki

Masz dodatkowe pytania? Rozważ utworzenie [biletu pomocy technicznej](https://azure.microsoft.com/support/create-ticket/).