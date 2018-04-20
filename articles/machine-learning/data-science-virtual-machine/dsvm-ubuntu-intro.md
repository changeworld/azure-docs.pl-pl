---
title: Aprowizowanie maszyny wirtualnej systemu Linux (Ubuntu) danych nauki na platformie Azure | Dokumentacja firmy Microsoft
description: Konfigurowanie i tworzenie danych nauki maszyny wirtualnej dla systemu Linux (Ubuntu) na platformie Azure analytics i uczenia maszynowego.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: bradsev
ms.openlocfilehash: 4715384a0c6eb24a6a4208ca387b8c4a9871d5c7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="provision-the-data-science-virtual-machine-for-linux-ubuntu"></a>Aprowizowanie maszyny wirtualnej nauki danych dla systemu Linux (Ubuntu)

Maszyna wirtualna nauki danych dla systemu Linux jest obraz maszyny wirtualnej z systemem Ubuntu, który ułatwia szybkie wprowadzenie do uczenia maszynowego, w tym głębokie learning na platformie Azure. Narzędzia głębokie learning obejmują:

  * [Caffe](http://caffe.berkeleyvision.org/): platforma głębokie learning skompilowany dla szybkości, expressivity i Modułowość
  * [Caffe2](https://github.com/caffe2/caffe2): wersja wieloplatformowych Caffe
  * [Microsoft kognitywnych Toolkit](https://github.com/Microsoft/CNTK): bezpośrednie uczenia toolkit oprogramowania Microsoft Research
  * [H2O](https://www.h2o.ai/): platformy danych big data open source i graficznego interfejsu użytkownika
  * [Keras](https://keras.io/): wysokiego poziomu sieci neuronowej interfejsu API języka Python dla Theano i TensorFlow
  * [MXNet](http://mxnet.io/): Biblioteka elastyczne, wydajne learning głębokie o wiele powiązań języka
  * [CYFR NVIDIA](https://developer.nvidia.com/digits): graficznego systemu, które upraszcza typowe zadania uczenia głębokiego
  * [PyTorch](http://pytorch.org/): wysokiego poziomu biblioteka języka Python z obsługą dynamicznej sieci
  * [TensorFlow](https://www.tensorflow.org/): Biblioteka open source dla analizy maszyny z Google
  * [Theano](http://deeplearning.net/software/theano/): biblioteka języka Python A definiowanie, optymalizacja i efektywnie obliczenia matematyczne wyrażeń zawierających tablice wielowymiarowe
  * [Latarka](http://torch.ch/): naukowych framework przetwarzania danych z szeroką obsługę algorytmów uczenia maszynowego
  * CUDA, cuDNN i sterownik NVIDIA
  * Wiele przykładowych notesów Jupyter

Wersje GPU są wszystkie biblioteki, chociaż są one również uruchomić na Procesorze.

Maszyna wirtualna nauki danych dla systemu Linux zawiera również popularne narzędzia dla danych nauki i rozwoju działania, w tym:

* Microsoft R Server Developer Edition po otwarciu R firmy Microsoft
* Dystrybucję oprogramowania Python anaconda (wersji 2.7 i 3.5), łącznie z biblioteki analiz danych popularnych
* JuliaPro - wyselekcjonowanych dystrybucji języka Julia z popularnych bibliotek analizy danych i naukowe
* Wystąpienia autonomicznego Spark i jednego węzła Hadoop (HDFS, Yarn)
* JupyterHub — Obsługa R, Python, PySpark, Julia jądra wielodostępnym serwera notesu Jupyter
* Eksplorator usługi Azure Storage
* Azure interfejs wiersza polecenia (CLI) do zarządzania zasobami platformy Azure
* Machine learning narzędzia
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): szybki komputer uczenia systemu obsługującego technik, takich jak online, wyznaczania wartości skrótu, allreduce, redukcji, learning2search, aktywna i uczenie się interakcyjne
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): narzędzie, zapewniając szybkie i dokładne boosted drzewa wykonania
  * [Rattle](http://rattle.togaware.com/): graficzne narzędzie, które umożliwia wprowadzenie do analizy danych i machine learning w łatwy R
  * [LightGBM](https://github.com/Microsoft/LightGBM): zwiększania framework gradientu szybkie, rozproszonych, wysokiej wydajności
* Zestaw Azure SDK w języku Java, Python, node.js, Ruby, PHP
* Biblioteki języka R i Python dla programu uczenie maszynowe Azure i innych usług Azure
* Narzędzia deweloperskie i edytory (programu RStudio, PyCharm, IntelliJ, Emacs, vim)


Podczas analizy danych obejmuje iteracja w sekwencji zadań:

1. Znajdowanie, ładowania i wstępne przetworzenie danych
2. Tworzenie i testowanie modeli
3. Wdrażanie modeli do wykorzystania w aplikacji inteligentnego

Analityków danych za pomocą różnych narzędzi do wykonania tych zadań. Może być bardzo czasochłonne znalezienia odpowiedniej wersji oprogramowania, a następnie pobrać, skompiluj i zainstalować te wersje.

Maszyny wirtualnej nauki danych dla systemu Linux może znacznie ułatwić to obciążenie. Użyj, aby szybko rozpocząć projektu analizy. Pozwala na pracę nad zadaniami w różnych językach, łącznie z R, Python, SQL, Java i C++. Zestaw SDK usługi Azure uwzględnione w maszynie Wirtualnej umożliwia tworzenie aplikacji przy użyciu różnych usług w systemie Linux dla platformy firmy Microsoft w chmurze. Ponadto użytkownik ma dostęp do innych języków, takie jak Ruby, Perl, PHP i node.js również wstępnie zainstalowane.

Nie ma żadnych opłat oprogramowania dla tego obrazu maszyny Wirtualnej do analizy danych. Płaci się tylko opłaty użycia Azure sprzętu, które są oceniane na podstawie rozmiaru maszyny wirtualnej, które należy udostępnić. Więcej informacji na temat opłat obliczeniowe można znaleźć w [strony listę maszyny Wirtualnej w portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Inne wersje danych maszyny wirtualnej nauki
A [CentOS](linux-dsvm-intro.md) obrazu jest również dostępna, wykonywanie wielu z tych samych narzędzi jako obraz Ubuntu. A [Windows](provision-vm.md) obraz jest również dostępny.

## <a name="prerequisites"></a>Wymagania wstępne
Przed utworzeniem maszyny wirtualnej nauki danych dla systemu Linux, musi mieć subskrypcję platformy Azure. Aby go uzyskać, zobacz [Azure Pobierz bezpłatną wersję próbną](https://azure.microsoft.com/free/).

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Tworzenie maszyny wirtualnej nauki danych dla systemu Linux
Poniżej przedstawiono kroki, aby utworzyć wystąpienie maszyny wirtualnej nauki danych dla systemu Linux:

1. Przejdź do wyświetlania na maszynie wirtualnej [portalu Azure](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu).
2. Kliknij przycisk **Utwórz** (u dołu) można wyświetlić kreatora.![ Konfigurowanie danych nauki vm](./media/dsvm-ubuntu-intro/configure-data-science-virtual-machine.png)
3. Dane wejściowe dla poszczególne kroki w Kreatorze (wyliczyć po prawej stronie powyższej ilustracji) używany do tworzenia maszyny wirtualnej nauki danych firmy Microsoft można znaleźć w poniższych sekcjach. Poniżej przedstawiono wejść potrzebne do skonfigurowania każdego z następujących czynności:
   
   a. **Podstawy**:
   
   * **Nazwa**: Nazwa serwera nauki danych tworzona.
   * **Nazwa użytkownika**: pierwsze konto logowania identyfikatora.
   * **Hasło**: pierwszy hasło do konta (zamiast hasła można użyć klucza publicznego SSH).
   * **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz jedną, na którym maszyna ma być utworzony i są rozliczane. Musi mieć uprawnienia do tworzenia zasobów dla tej subskrypcji.
   * **Grupy zasobów**: można utworzyć nowej lub istniejącej grupy.
   * **Lokalizacja**: wybierz centrum danych, która jest najbardziej odpowiednia. Zazwyczaj jest centrum danych, która zawiera większość danych lub zbliżony do fizycznej lokalizacji najszybszy dostęp do sieci.
   
   b. **Rozmiar**:
   
   * Wybierz jeden z typów serwerów, które spełnia Twoje wymagania funkcjonalne i ograniczenia kosztów. Wybierz **Wyświetl wszystko** Aby wyświetlić więcej opcji dotyczących rozmiarów maszyn wirtualnych. Wybierz maszynę Wirtualną NC klasy szkolenia procesora GPU.
   
   c. **Ustawienia**:
   
   * **Typ dysku**: Wybierz **Premium** również dysków półprzewodnikowych (SSD). W przeciwnym razie wybierz **standardowe**. Maszyny wirtualne procesora GPU wymaga dysków w warstwie standardowa.
   * **Konto magazynu**: Utwórz nowe konto magazynu Azure w ramach subskrypcji lub użyć istniejącego w tej samej lokalizacji, która została wybrana na **podstawy** kroku kreatora.
   * **Inne parametry**: W większości przypadków, po prostu użyć wartości domyślnych. Aby uwzględnić wartości innych niż domyślne, umieść kursor nad informacyjną łącze, aby uzyskać pomoc dotyczącą określonych pól.
   
   d. **Podsumowanie**:
   
   * Sprawdź, czy wszystkie wprowadzone informacje jest poprawna.
   
   e. **Kup**:
   
   * Aby uruchomić udostępnianie, kliknij przycisk **kupić**. Łącze znajduje się na warunki transakcji. Maszyna wirtualna nie ma żadnych dodatkowych kosztów poza obliczeniowe dla rozmiaru serwera w **rozmiar** kroku.

Udostępnianie powinno zająć około 5 – 10 minut. Stan inicjowania obsługi administracyjnej jest wyświetlana w portalu Azure.

## <a name="how-to-access-the-data-science-virtual-machine-for-linux"></a>Jak dostęp do maszyny wirtualnej nauki danych dla systemu Linux

Aby dostęp do DSVM Ubuntu za pomocą trzech metod:
1. SSH przez sesje usług terminalowych
2. X2Go graficzny sesji
3. JupyterHub i JupyterLab notesów Jupyter

### <a name="ssh"></a>Protokół SSH

Po utworzeniu maszyny Wirtualnej można logowania się do go przy użyciu protokołu SSH. Korzystać z poświadczeń konta, które zostały utworzone w **podstawy** sekcji Krok 3 dla interfejsu powłoki tekstu. W systemie Windows, możesz pobrać narzędzie klienta SSH, takie jak [Putty](http://www.putty.org). Jeśli wolisz pulpitu graficznego (X Windows System), można użyć X11 przekazywania na Putty lub instalowanie klienta X2Go.

> [!NOTE]
> Klient X2Go działa lepiej niż X11 przekazywania podczas testowania. Zalecamy używanie klienta X2Go dla graficzny interfejs użytkownika.
> 
> 

### <a name="x2go"></a>X2Go
Maszyny Wirtualnej systemu Linux jest już zainicjowane z serwerem X2Go i gotowa do akceptowania połączeń klienta. Nawiązać pulpitu graficznego maszyny Wirtualnej systemu Linux, wykonaj następującą procedurę na komputerze klienckim:

1. Pobierz i zainstaluj klienta X2Go dla danej platformy klienta z [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Uruchom klienta X2Go, a następnie wybierz **nowej sesji**. Otwiera okno konfiguracji z wieloma kartami. Wprowadź następujące parametry konfiguracji:
   * **Karta sesji**:
     * **Host**: Nazwa hosta lub adres IP maszyny Wirtualnej systemu Linux danych nauki.
     * **Logowania**: nazwa użytkownika na Maszynie wirtualnej systemu Linux.
     * **SSH Port**: pozostaw wartość domyślna 22.
     * **Typ sesji**: Zmień wartość na XFCE. Maszyny Wirtualnej systemu Linux aktualnie obsługuje tylko XFCE pulpitu.
   * **Karta Media**: można wyłączyć obsługi dźwięku i klienta drukowania, jeśli nie ma potrzeby korzystania z nich.
   * **Foldery udostępnione**: katalogi z z komputerów klienckich zainstalowane na Maszynie wirtualnej systemu Linux, należy dodać katalogi komputera klienta, które chcesz udostępnić Maszynie wirtualnej na tej karcie.

Po zalogowaniu się z maszyną Wirtualną przy użyciu klienta SSH lub XFCE graficznego pulpitu za pomocą klienta X2Go, można przystąpić do uruchomienia przy użyciu narzędzia, które są zainstalowane i skonfigurowane na maszynie Wirtualnej. Na XFCE Zobacz aplikacje menu skrótów i ikony pulpitu dla wielu narzędzi.

### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub i JupyterLab

Uruchamia Ubuntu DSVM [JupyterHub](https://github.com/jupyterhub/jupyterhub), serwer Jupyter przez wielu użytkowników. Aby połączyć, należy przejść do https://your-vm-ip:8000 na komputerze przenośnym lub pulpit, wprowadź nazwę użytkownika i hasło używane do tworzenia maszyny Wirtualnej i zaloguj się. Wiele przykładowych notesów dostępnych Przeglądaj i wypróbować usługę.

JupyterLab, generacji notesów Jupyter i JupyterHub, jest również dostępna. Do niego dostęp, zaloguj się do JupyterHub, a następnie przejdź do adresu URL https://your-vm-ip:8000/lab. JupyterLab można ustawić jako domyślny serwer notesu, dodając ten wiersz do /etc/jupyterhub/jupyterhub_config.py:

    c.Spawner.default_url = '/lab'

## <a name="tools-installed-on-the-data-science-virtual-machine-for-linux"></a>Narzędzia są zainstalowane na maszynie wirtualnej nauki danych dla systemu Linux
### <a name="deep-learning-libraries"></a>Głębokie Learning bibliotek

#### <a name="cntk"></a>CNTK
Kognitywnych zestaw narzędzi firmy Microsoft jest typu open source, głębokie uczenia zestawu narzędzi. Python powiązań są dostępne w środowiskach Conda głównego i py35. Ma ona również narzędzia wiersza polecenia (cntk), który już znajduje się w ŚCIEŻCE.

Przykładowe Python notesów są dostępne w JupyterHub. Aby uruchomić podstawowy przykład w wierszu polecenia, wykonaj następujące polecenia w powłoce:

    cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
    cntk configFile=lr_bs.cntk makeMode=false command=Train

Aby uzyskać więcej informacji, zobacz sekcję CNTK [GitHub](https://github.com/Microsoft/CNTK)i [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

#### <a name="caffe"></a>Caffe
Caffe to platforma głębokie learning Centrum uczenia i Berkeley wizji. Jest ona dostępna w /opt/caffe. Przykłady można znaleźć w /opt/caffe/examples.

#### <a name="caffe2"></a>Caffe2
Caffe2 to platforma głębokie uczenia z usługi Facebook, który jest oparty na Caffe. Jest ona dostępna w języku Python 2.7 w środowisku Conda głównego. Aby aktywować go, uruchom następujące polecenie z poziomu powłoki:

    source /anaconda/bin/activate root

Niektóre komputery przenośne przykładzie są dostępne w JupyterHub.

#### <a name="h2o"></a>H2O
H2O jest uczenie maszynowe szybkiego, w pamięci, rozproszone i platformy analizy predykcyjnej. Pakiet języka Python jest instalowane w środowiskach Anaconda główny i py35. Pakiet języka R jest również instalowany. Aby uruchomić H2O z wiersza polecenia, `java -jar /dsvm/tools/h2o/current/h2o.jar`; istnieją różne [opcje wiersza polecenia](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) , który chcesz skonfigurować. Interfejs sieci Web przepływu mogą uzyskiwać przechodząc do http://localhost:54321 rozpocząć pracę. Przykładowe notesów są także dostępne w JupyterHub.

#### <a name="keras"></a>Keras
Keras jest ogólny sieci neuronowej interfejsu API środowiska Python, którą można uruchomić na początku TensorFlow lub Theano. Jest ona dostępna w środowiskach Python głównego i py35. 

#### <a name="mxnet"></a>MXNet
MXNet to platforma głębokie nauki przeznaczony dla wydajność i elastyczność. Ma ona powiązania R i Python włączone DSVM. Przykładowe notesów znajdują się w JupyterHub, a przykładowy kod jest dostępny w /dsvm/samples/mxnet.

#### <a name="nvidia-digits"></a>NVIDIA CYFR
NVIDIA głębokie uczenia GPU szkolenia System, znany jako CYFR, jest systemu, aby uprościć typowe zadania uczenia głębokie takich jak zarządzanie danych, projektowanie i szkolenia sieci neuronowe w systemach procesora GPU i monitorowania wydajności w czasie rzeczywistym z wizualizacji zaawansowane. 

CYFR jest dostępna jako usługa o nazwie cyfr. Uruchom usługę i przejdź do http://localhost:5000 rozpocząć pracę.

Jako moduł Python w środowisku głównego Conda instalowany jest także CYFR.

#### <a name="tensorflow"></a>TensorFlow
TensorFlow to biblioteka głębokie learning firmy Google. Jest to biblioteka oprogramowania typu open source do wartości liczbowych obliczeń przy użyciu wykresów przepływu danych. TensorFlow jest dostępna w środowisku Python py35, a niektóre przykładowe notesów znajdują się w JupyterHub.

#### <a name="theano"></a>Theano
Theano to biblioteka języka Python dla wydajne obliczenia wartości liczbowych. Jest ona dostępna w środowiskach Python głównego i py35. 

#### <a name="torch"></a>Torch
Latarka to naukowych platforma przetwarzania danych z szeroką obsługę algorytmów uczenia maszynowego. Jest on dostępny w /dsvm/tools/torch, a sesja interaktywna th i Menedżer pakietów luarocks są dostępne w wierszu polecenia. Przykłady są dostępne w /dsvm/samples/torch.

PyTorch jest również dostępna w środowisku Anaconda głównego. Przykłady znajdują się w /dsvm/samples/pytorch.

### <a name="microsoft-r-server"></a>Microsoft R Server
R jest jednym z najbardziej popularnych języków do analizowania danych i uczenia maszynowego. Jeśli chcesz użyć R do analizy sieci, czy maszyna wirtualna ma Microsoft R Server (PANI) Microsoft Open R (MRO) i biblioteki jądra matematyczne (MKL). MKL optymalizuje operacji matematycznych typowe w przypadku algorytmów analitycznych. MRO wynosi 100% zgodny z sieci CRAN-R, a wszelkie bibliotek R opublikowane w sieci CRAN można zainstalować na MRO. PANI umożliwia skalowanie i operationalization R modeli do usług sieci web. Można edytować w jednym z edytory domyślne, takie jak programu RStudio, vi lub Emacs programy R. Można jednak za pomocą edytora Emacs został wstępnie zainstalowany. Emacs: pakiet dostępu (Statystyka mówi Emacs) upraszcza pracy z plikami R w edytorze emacs:.

Do uruchamiania R konsoli, wystarczy wpisać **R** w powłoce. Powoduje to przejście do interaktywnego środowiska. Aby opracować R program, zwykle za pomocą edytora, takich jak Emacs lub vi, a następnie uruchom skrypty w R. Z programu RStudio masz pełne graficznego środowiska IDE umożliwiające tworzenie programu R.

Jest także skrypt języka R do zainstalowania [pakietów języka R z góry 20](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) Jeśli chcesz. Ten skrypt może działać po w interfejsie interakcyjne R, który można wprowadzić (jak wspomniano), wpisując **R** w powłoce.  

### <a name="python"></a>Python
Anaconda Python jest instalowany z Python 2.7 i środowisk 3.5. 2.7 środowisko jest nazywany _głównego_, i nosi nazwę wersji 3.5 środowiska _py35_. Rozkład ten zawiera podstawowe języka Python oraz około 300 najbardziej popularnych pakietów analytics matematyczne, inżynieria i danych. 

W środowisku py35 jest ustawieniem domyślnym. Aby aktywować środowiska głównego (2.7):

    source activate root

Aby ponownie aktywować py35 środowiska:

    source activate py35

Aby wywołać sesja interaktywna Python, wystarczy wpisać **python** w powłoce. 

Instalowanie dodatkowych bibliotek języka Python za pomocą ```conda``` lub ````pip```` . Dla narzędzia pip należy najpierw aktywować poprawne środowisko, jeśli nie chcesz, aby wartość domyślna:

    source activate root
    pip install <package>

Lub określ pełną ścieżkę do narzędzia pip:

    /anaconda/bin/pip install <package>
    
Dla conda, należy zawsze podać nazwę środowiska (_py35_ lub _głównego_):

    conda install <package> -n py35

Jeśli w graficznym interfejsie lub mieć X11 przekazywania zestawu w górę, można wpisać **pycharm** można uruchomić PyCharm IDE języka Python. Możesz użyć domyślnego edytory tekstów. Ponadto można użyć Spyder, IDE języka Python, który jest powiązany z dystrybucji Anaconda Python. Spyder wymaga graficznego pulpitu lub X11 przekazywania. Skrót do Spyder znajduje się w graficznym desktop.s

### <a name="jupyter-notebook"></a>Notesu Jupyter
Rozkład Anaconda zawiera również notesu Jupyter, środowisko, aby udostępnić kodu i analizy. Notesu Jupyter jest dostępny za pośrednictwem JupyterHub. Zaloguj się przy użyciu lokalnego nazwę użytkownika systemu Linux i hasło.

Wstępnie skonfigurowano serwer notesu Jupyter z języka Python, 2, Python 3 i jądra R. Brak ikony pulpitu o nazwie "Notesu Jupyter" można uruchomić przeglądarki do uzyskiwania dostępu do serwera notesu. Jeśli na maszynie Wirtualnej za pośrednictwem protokołu SSH lub X2Go klienta, możesz również odwiedzić [ https://localhost:8000/ ](https://localhost:8000/) dostęp do serwera notesu Jupyter.

> [!NOTE]
> Kontynuuj, jeśli możesz uzyskać wyświetlania ostrzeżeń dotyczących certyfikatów.
> 
> 

Można uzyskać dostępu do serwera notesu Jupyter z dowolnym hostem. Po prostu wpisz *https://\<nazwę DNS maszyny Wirtualnej lub adres IP\>: 8000 /*

> [!NOTE]
> Port 8000 jest otwarty na zaporze domyślnie po zainicjowaniu obsługi maszyny Wirtualnej.
> 
> 

Firma Microsoft ma spakowane próbki notesów — jeden w języku Python i jeden w języku R. Na stronie głównej notesu można zobaczyć łącza do próbek, po notesu Jupyter uwierzytelniania przy użyciu lokalnego nazwę użytkownika systemu Linux i hasło. Można utworzyć nowy notes, wybierając **nowy**, a następnie jądra odpowiedni język. Jeśli nie widzisz **nowy** , kliknij **Jupyter** ikonę na lewego górnego można przejść do strony głównej serwera notesu.

### <a name="apache-spark-standalone"></a>Apache Spark autonomiczny 
Autonomicznego wystąpienia programu Apache Spark jest preinstalowany na DSVM Linux ułatwiające tworzenie aplikacji Spark lokalnie najpierw przed testowanie i wdrażanie w dużych klastrach. Można uruchamiać programy PySpark za pośrednictwem jądra Jupyter. Po otwarciu Jupyter kliknij **nowy** przycisk i powinna zostać wyświetlona lista dostępnych jądra. "Python — Spark" jest jądra PySpark, która umożliwia tworzenie aplikacji przy użyciu języka Python Spark. IDE języka Python, takie jak PyCharm lub Spyder umożliwia również tworzenie możesz programu Spark. Ponieważ to jest wystąpienia autonomicznego, stosu Spark jest uruchamiany w ramach programu klienta wywołującego. Dzięki temu szybsze i łatwiejsze Rozwiązywanie problemów w porównaniu do tworzenia w klastrze Spark. 

Przykładowy notes PySpark znajduje się na Jupyter, który znajduje się w katalogu "SparkML" w katalogu macierzystego Jupyter ($ głównej/notesów/SparkML/pySpark). 

Jeśli w R są programowania dla platformy Spark, można użyć Microsoft R Server SparkR lub sparklyr. 

Przed uruchomieniem w kontekście Spark Microsoft R Server, należy wykonać jeden raz, Instalator krok do włączenia lokalnego węzła pojedynczego wystąpienia systemu plików HDFS Hadoop i Yarn. Domyślnie usługi Hadoop są zainstalowane, ale na DSVM wyłączony. Aby go włączyć, należy uruchomić następujące polecenia jako główny po raz pierwszy:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Można zatrzymać usługi Hadoop powiązane usługi, gdy nie są potrzebne, uruchamiając ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```` próbkę pokazująca, jak umożliwiające opracowanie i przetestowanie PANI w zdalnym kontekście Spark (czyli wystąpienia Spark autonomicznego na DSVM) są dostępne w `/dsvm/samples/MRS` katalogu. 

### <a name="ides-and-editors"></a>IDEs i Redaktorzy
Należy wybrać kilka edytory kodu. W tym vi/VIM, Emacs PyCharm, programu RStudio i IntelliJ. IntelliJ, programu RStudio PyCharm są edytory graficzne i konieczne jest zalogowany do graficznego pulpitu z nich korzystać. Edytory te mają pulpitu i aplikacji menu skrótów, można je uruchomić.

**VIM** i **Emacs** są edytory oparte na tekst. Na Emacs możemy został zainstalowany pakiet dodatku o nazwie Emacs mówi statystyki (dostępu), który ułatwia w edytorze emacs: Praca z R. Więcej informacji można znaleźć w folderze [dostępu](http://ess.r-project.org/).

**Lateksu** jest instalowane za pomocą pakietu texlive wraz z dodatku Emacs [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) pakiet, który upraszcza tworzenie dokumentów lateksu w emacs:.  

### <a name="databases"></a>Bazy danych

#### <a name="graphical-sql-client"></a>Graficzny klienta SQL
**SQuirrel SQL**, dostarczono graficznego klienta SQL, do łączenia z do różnych baz danych (takich jak Microsoft SQL Server oraz MySQL) i uruchamiać zapytania SQL. Można to wykonać, z sesji pulpitu graficznym (na przykład za pomocą klienta X2Go). Aby wywołać SQuirrel SQL, możesz uruchomić go z ikony na pulpicie lub uruchom następujące polecenie w powłoce.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

Przed pierwszym użyciem skonfigurować sterowniki i aliasów bazy danych. Sterownik JDBC znajdują się na:

*/usr/share/Java/jdbcdrivers*

Aby uzyskać więcej informacji, zobacz [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Narzędzia wiersza polecenia do uzyskiwania dostępu do programu Microsoft SQL Server
Pakiet sterownika ODBC dla programu SQL Server zawiera również dwa narzędzia wiersza polecenia:

**Narzędzie BCP**: zbiorczego narzędzie bcp kopiuje dane między wystąpienia programu Microsoft SQL Server i plik danych w formacie określone przez użytkownika. Narzędzie bcp może służyć do importowania dużej liczby nowych wierszy do tabel programu SQL Server lub do eksportowania danych z tabel do plików danych. Do importowania danych do tabeli, należy użyć pliku formatu utworzonego dla tej tabeli, lub należy poznać strukturę tabeli i typy danych, które są prawidłowe dla kolumn.

Aby uzyskać więcej informacji, zobacz [połączenie za pomocą narzędzia bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**narzędzia sqlcmd**: Wprowadź instrukcji języka Transact-SQL z narzędzia sqlcmd, a także systemowych procedur i pliki w wierszu polecenia skryptu. Narzędzie to wykorzystuje ODBC można wykonać partii języka Transact-SQL.

Aby uzyskać więcej informacji, zobacz [połączenie przy użyciu narzędzia sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

> [!NOTE]
> Istnieją pewne różnice w to narzędzie między platformami systemu Linux i Windows. Zobacz dokumentację, aby uzyskać szczegółowe informacje.
> 
> 

#### <a name="database-access-libraries"></a>Biblioteki dostępu do bazy danych
Brak dostępnych bibliotek R i Python baz danych programu access.

* W R **RODBC** pakietu lub **dplyr** pakietu służy do zapytań lub wykonywania instrukcji SQL na serwerze bazy danych.
* W języku Python **pyodbc** Biblioteka zapewnia dostęp do bazy danych z ODBC jako odpowiedniej warstwy.  

### <a name="azure-tools"></a>Narzędzia platformy Azure
Następujące narzędzia Azure są zainstalowane na maszynie Wirtualnej:

* **Interfejs wiersza polecenia platformy Azure**: The Azure CLI służy do tworzenia i zarządzania zasobami Azure za pomocą powłoki poleceń. Aby wywołać narzędzi platformy Azure, po prostu wpisz **azure pomocy**. Aby uzyskać więcej informacji, zobacz [stronę dokumentacji interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Eksplorator magazynu Microsoft Azure**: Eksploratora usługi Microsoft Azure Storage jest graficzne narzędzie, które służy do przeglądania obiektów, które są przechowywane na koncie magazynu Azure oraz do przekazywania i pobierania danych do i z obiekty BLOB platformy Azure. Dostępne Eksploratora usługi Storage z ikonę skrótu na pulpicie. Można wywołać z poziomu wiersza powłoki, wpisując **StorageExplorer**. Musisz być zalogowany na kliencie X2Go lub ma X11 przekazywania zestawu w górę.
* **Biblioteki Azure**: poniżej przedstawiono niektóre wstępnie zainstalowane bibliotek.
  
  * **Python**: Azure związane z biblioteki w języku Python, które są zainstalowane są **azure**, **uczenie maszynowe Azure**, **pydocumentdb —**, i **pyodbc**. Pierwsze trzy bibliotek umożliwia dostęp do usług Azure storage, uczenie maszynowe Azure i bazy danych Azure rozwiązania Cosmos (danych nosql opartej na platformie Azure). Czwarty biblioteki pyodbc (wraz z sterownik ODBC firmy Microsoft dla programu SQL Server), umożliwia dostęp do programu SQL Server, bazy danych SQL Azure i usługi Azure SQL Data Warehouse w języku Python za pomocą interfejsu ODBC. Wprowadź **listy pip** aby zobaczyć wszystkie wymienione biblioteki. Pamiętaj uruchomić to polecenie w środowiskach 3.5 i Python 2.7.
  * **R**: związanych z programu Azure biblioteki w R, które są zainstalowane są **uczenie maszynowe Azure** i **RODBC**.
  * **Java**: lista bibliotek Azure Java można znaleźć w katalogu **/dsvm/sdk/AzureSDKJava** na maszynie Wirtualnej. Biblioteki klucza to Azure sterowniki magazynów i zarządzania interfejsów API, bazy danych Azure rozwiązania Cosmos i JDBC dla programu SQL Server.  

Dostęp można uzyskać [portalu Azure](https://portal.azure.com) z wstępnie zainstalowane przeglądarki Firefox. W portalu Azure można utworzyć, zarządzanie i monitorowanie zasobów platformy Azure.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Usługa Azure Machine Learning to usługa w chmurze pełni zarządzana, która umożliwia tworzenie, wdrażanie i udostępniać rozwiązania analizy predykcyjnej. Możesz utworzyć modele i eksperymenty Azure Machine Learning Studio. Jest dostępny z przeglądarki sieci web na maszynie wirtualnej nauki danych odwiedzając [Microsoft Azure Machine Learning](https://studio.azureml.net).

Po zalogowaniu się w usłudze Azure Machine Learning Studio, masz dostęp do kanwy eksperymenty, gdzie można utworzyć przepływ logiczny dla algorytmów uczenia maszynowego. Możesz również dostępu do hostowanych w usłudze Azure Machine Learning notesu Jupyter i może bezproblemowo współpracować z eksperymentów w usłudze Machine Learning Studio. Operacjonalizuj modele utworzonych przez zawijania je w interfejsie usługi sieci web uczenia maszynowego. Dzięki temu klienci, w dowolnym języku do wywołania predykcje uzyskiwane z modeli uczenia maszynowego. Aby uzyskać więcej informacji, zobacz [dokumentacji uczenia maszynowego](https://azure.microsoft.com/documentation/services/machine-learning/).

Można również tworzenie modeli w języku R lub Python na maszynie Wirtualnej, a następnie wdrożyć go w środowisku produkcyjnym w usłudze Azure Machine Learning. Firma Microsoft zainstalowano bibliotek w R (**uczenie maszynowe Azure**) i języka Python (**uczenie maszynowe Azure**) Aby włączyć tę funkcję.

Aby uzyskać informacje na temat wdrażania modeli w R i Python w usłudze Azure Machine Learning, zobacz [10 sposobów na nauki danych maszyny wirtualnej](vm-do-ten-things.md) (w szczególności, w sekcji "tworzenie modeli przy użyciu R lub Python i Operacjonalizacji je przy użyciu usługi Azure Machine Learning").

> [!NOTE]
> Instrukcje te zostały napisane dla wersji systemu Windows maszyny wirtualnej analizy danych. Jednak informacje o wdrażaniu modeli do usługi Azure Machine Learning istnieje mające zastosowanie do maszyny Wirtualnej systemu Linux.
> 
> 

### <a name="machine-learning-tools"></a>Machine learning narzędzia
Maszyna wirtualna ma kilka machine learning narzędzia i algorytmy, które zostały wstępnie skompilowany i wstępnie zainstalowane lokalnie. Należą do nich:

* **Vowpal Wabbit**: Algorytm uczenia fast online.
* **xgboost**: narzędzie, które zapewnia zoptymalizowane, boosted drzewa algorytmów.
* **Rattle**: na podstawie R graficzne narzędzie do łatwego Eksplorowanie danych oraz modelowania.
* **Python**: Anaconda Python jest powiązane z algorytmów uczenia maszynowego z bibliotekami, takich jak Scikit Dowiedz się więcej. Inne biblioteki można zainstalować za pomocą `pip install` polecenia.
* **LightGBM**: gradientu szybkie, rozproszonych, wysokiej wydajności, zwiększania framework oparte na algorytmy drzewa decyzyjnego.
* **R**: Biblioteka sformatowanego machine learning funkcji jest dostępna dla R. Niektóre z bibliotek, które są wstępnie zainstalowane są lm, glm, randomForest, rpart. Inne biblioteki można zainstalować, uruchamiając:
  
        install.packages(<lib name>)

Poniżej przedstawiono dodatkowe informacje dotyczące narzędzia pierwsze trzy uczenia maszynowego na liście.

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit
Vowpal Wabbit jest system, który używa technik, takich jak online, wyznaczania wartości skrótu, allreduce, redukcji, learning2search, aktywne, uczenia maszynowego i uczenie się interakcyjne.

Aby uruchomić narzędzie, na przykład podstawowa, wykonaj następujące czynności:

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

Istnieją inne, większy pokazy w tym katalogu. Aby uzyskać więcej informacji dotyczących VW, zobacz [tej sekcji GitHub](https://github.com/JohnLangford/vowpal_wabbit)i [wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost
Jest to biblioteka, który został zaprojektowany i zoptymalizowany pod kątem algorytmów boosted (drzewo). Celem tej biblioteki jest do dystrybuowania limitów obliczeń maszyny do zewnętrznych potrzebnych do udostępniania drzewa na dużą skalę, zwiększanie wyniku, który jest skalowalna, przenośne i dokładne.

Jest podana jako wiersz polecenia, a także biblioteki R.

Aby użyć tej biblioteki w R, można uruchomić sesji interaktywnej R (przez wpisanie po prostu **R** w powłoce) i załadować biblioteki.

Poniżej przedstawiono prosty przykład, który można uruchomić w wierszu R:

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

Aby uruchomić wiersz polecenia xgboost, Oto do wykonania w powłoce poleceń:

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


.Model — plik jest zapisywany do podanego katalogu. Można znaleźć informacje o tym przykładzie pokaz [w serwisie GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Aby uzyskać więcej informacji o xgboost, zobacz [stronę dokumentacji xgboost](https://xgboost.readthedocs.org/en/latest/), a jego [repozytorium GitHub](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Rattle
Rattle ( **R** **A**nalytical **T**pismo odręczne — narzędzie **T**o **L**zdobyć **E**asily) korzysta z Graficznym interfejsem użytkownika Eksplorowanie danych oraz modelowania. Stanowi statystyczne i wizualne podsumowania danych, transformacji danych, które mogą być łatwo modelowane, tworzy zarówno nienadzorowanych i nadzorowanym modeli z danych, przedstawia wydajność modeli graficznie, i ustawia wyniki nowych danych. Generuje kod języka R, replikowanie operacje w interfejsie użytkownika, które można uruchomić bezpośrednio w R lub używany jako punkt początkowy do dalszej analizy.

Aby uruchomić Rattle, należy w graficznym logowania w sesji pulpitu. Na terminalu, wpisz ```R``` wprowadzenia środowiska R. W wierszu polecenia języka R wprowadź następujące polecenia:

    library(rattle)
    rattle()

Teraz interfejsu graficznego otwartej z zestawu kart. Poniżej przedstawiono kroki szybki start w Rattle potrzebne do Użyj zestawu danych przykładowych pogodą i tworzenia modelu. W niektórych czynności monit do automatycznego zainstalowania i załadować niektórych wymaganych pakietów języka R, które nie są już w systemie.

> [!NOTE]
> Jeśli nie masz dostępu do zainstalowania pakietu w katalogu systemowym (ustawienie domyślne), może zostanie wyświetlony monit na R okna konsoli można zainstalować pakietów do biblioteki osobistych. Odpowiedzi *y* Jeśli wyświetlane monity.
> 
> 

1. Kliknij przycisk **Execute** (Wykonaj).
2. Okno dialogowe wyskakującej, pytaniem, jeśli chcesz użyć zestawu danych pogody przykład. Kliknij przycisk **tak** załadować w przykładzie.
3. Kliknij przycisk **modelu** kartę.
4. Kliknij przycisk **Execute** do tworzenia drzewa decyzyjnego.
5. Kliknij przycisk **rysowania** do wyświetlenia drzewa decyzyjnego.
6. Kliknij przycisk **lasu** przycisk radiowy, a następnie kliknij przycisk **Execute** do tworzenia losowe lasu.
7. Kliknij przycisk **Evaluate** kartę.
8. Kliknij przycisk **ryzyka** przycisk radiowy, a następnie kliknij przycisk **Execute** do wyświetlenia dwa wykresy wydajności ryzyka (skumulowany).
9. Kliknij przycisk **dziennika** kartę, aby wyświetlić kod Generuj R poprzedniej operacji.
   (Z powodu błędów w bieżącej wersji Rattle, należy wstawić *#* znak przed *wyeksportować ten dziennik...*  w tekście dziennika.)
10. Kliknij przycisk **wyeksportować** przycisk, aby zapisać plik skryptu języka R o nazwie *weather_script. R* do folderu macierzystego.

Można zamknąć Rattle i R. Teraz możesz zmodyfikować wygenerowany skrypt języka R lub go użyć, ponieważ jest go w każdej chwili uruchomić ponownie wszystkie czynności, które zostało zrobione w Interfejsie użytkownika Rattle. Szczególnie dla początkujących użytkowników w R to prosty sposób na szybkie czy analizy i komputera learning prostego interfejsu graficznego, podczas automatycznego generowania kodu w R, aby zmodyfikować i/lub Dowiedz się więcej.

## <a name="next-steps"></a>Kolejne kroki
Oto, jak można kontynuować swoją uczenie i eksploracja:

* [Nauki danych na dane nauki maszyny wirtualnej dla systemu Linux](linux-dsvm-walkthrough.md) przewodniku zademonstrowano, jak wykonać kilka typowych czynności nauki danych z danych nauki maszyny Wirtualnej systemu Linux udostępnione w tym miejscu. 
* Poznaj różne narzędzia analizy danych na nauki danych maszyny Wirtualnej za wypróbowanie narzędzi opisanych w tym artykule. Można również uruchomić *dsvm więcej info* na powłoki wstęp i wskaźniki do informacji na temat narzędzi zainstalowanych na maszynie Wirtualnej na maszynie wirtualnej.  
* Informacje o sposobie tworzenia rozwiązań analitycznych end-to-end systematycznie przy użyciu [proces nauki danych zespołu](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Odwiedź stronę [Cortana Analytics Gallery](http://gallery.cortanaanalytics.com) maszyny uczenie i danych analizy próbek korzystających z pakietu Cortana Analytics.

