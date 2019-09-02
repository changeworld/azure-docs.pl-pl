---
title: Linux Ubuntu — Szybki Start
titleSuffix: Azure Data Science Virtual Machine
description: Skonfiguruj i Utwórz Data Science maszyny wirtualnej dla systemu Linux (Ubuntu) na platformie Azure w celu analizy i uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 08/13/2019
ms.openlocfilehash: 9b323970e735a3e2a9d2cf418b787ed046c5574d
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70192084"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Szybki start: Konfigurowanie Data Science Virtual Machine dla systemu Linux (Ubuntu)

Data Science Virtual Machine (DSVM) dla systemu Linux to obraz maszyny wirtualnej oparty na Ubuntu, który ułatwia rozpoczęcie pracy z uczeniem maszynowym, w tym głębokie uczenie się na platformie Azure. Narzędzia uczenia głębokiego obejmują:

* [Caffe](https://caffe.berkeleyvision.org/): Platforma uczenia głębokiego skompilowana z myślą o szybkości, expressivity i modularności.
* [Caffe2](https://github.com/caffe2/caffe2): Międzyplatformowa wersja programu Caffe.
* [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK): Zestaw narzędzi do uczenia głębokiego firmy Microsoft Research.
* [H2O](https://www.h2o.ai/): Platforma do obsługi danych Big Data (Open Source) i graficzny interfejs użytkownika.
* [Keras](https://keras.io/): Interfejs API sieci neuronowych na wysokim poziomie w języku Python dla TensorFlow, Microsoft Cognitive Toolkit i Theano.
* [MXNet](https://mxnet.io/): Elastyczna, wydajna Biblioteka uczenia głębokiego z wieloma powiązaniami języka.
* [CYFRY NVIDIA](https://developer.nvidia.com/digits): System graficzny, który upraszcza typowe zadania uczenia głębokiego.
* [PyTorch](https://pytorch.org/): Biblioteka Python wysokiego poziomu z obsługą sieci dynamicznych.
* [TensorFlow](https://www.tensorflow.org/): Biblioteka open source dla usługi Machine Intelligence z usługi Google.
* [Theano](http://deeplearning.net/software/theano/): Biblioteka języka Python służąca do definiowania, optymalizowania i wydajnego oceniania wyrażeń matematycznych obejmujących tablice wielowymiarowe.
* [Torch](http://torch.ch/): Naukowa platforma obliczeniowa z szeroką obsługą algorytmów uczenia maszynowego.
* CUDA, cuDNN i sterownik NVIDIA.
* Wiele przykładowych notesów Jupyter.

Wszystkie biblioteki są wersje procesora GPU, chociaż mogą również uruchamiać na Procesorze.

Maszyna wirtualna do nauki o danych dla systemu Linux zawiera także popularne narzędzia do danych do analizy i rozwoju działalności, w tym:

* Microsoft Machine Learning Server z otwartym programem Microsoft R.
* Anaconda w języku Python (wersje 2,7 i 3,5), w tym popularne biblioteki analizy danych.
* JuliaProa, zanadzorowana dystrybucja języka Julia przy użyciu popularnych bibliotek naukowych i analitycznych danych.
* Autonomiczne wystąpienie platformy Spark i Jednowęzłowe rozwiązanie Hadoop (HDFS, przędza).
* JupyterHub, wieloużytkownikowy serwer notesu Jupyter obsługujący jądra języka R, Python, PySpark i Julia.
* Eksplorator usługi Azure Storage.
* Interfejs wiersza polecenia platformy Azure do zarządzania zasobami platformy Azure.
* Narzędzia uczenia maszynowego:
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): Szybki system uczenia maszynowego, który obsługuje takie techniki jak online, Hashing, allreduce, redukcyjny, learning2search, Active i Interactive uczenie się.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): Narzędzie, które umożliwia szybkie i dokładne wzmocnione drzewo implementację.
  * [Rattle](https://togaware.com/rattle/): Graficzne narzędzie, które ułatwia rozpoczęcie pracy z analizami danych i uczeniem maszynowym w języku R.
  * [LightGBM](https://github.com/Microsoft/LightGBM): Szybka, rozproszona platforma do zwiększania poziomu gradientu.
* Zestaw Azure SDK w językach Java, Python, Node. js, Ruby i PHP.
* Użyj bibliotek w języków R i Python, aby uzyskać w usłudze Azure Machine Learning i innymi usługami platformy Azure.
* Narzędzia i edytory programistyczne (RStudio, platformy PyCharm itd, IntelliJ, Emacs:, Vim).

Nauki o danych obejmuje iteracja w sekwencji zadań:

1. Znajdowanie, ładowanie i przetwarzanie wstępne danych
1. Tworzenie i testowanie modeli
1. Wdrażanie modeli do użycia w inteligentnych aplikacjach

Analitycy danych za pomocą różnych narzędzi w celu wykonania tych zadań. Może to być czasochłonne, aby znaleźć odpowiednie wersje oprogramowania, a następnie pobrać, skompilować i zainstalować te wersje.

Maszyna wirtualna do nauki o danych dla systemu Linux może znacznie ułatwić ten ciężar. Użyj go w szybkim stworzeniu projektu analizy. Pozwala ona do pracy nad zadaniami w różnych językach, w tym języka R, Python, SQL, Java i C++. Uwzględnione na maszynie wirtualnej i zestawu Azure SDK umożliwia tworzenie aplikacji przy użyciu różnych usług w systemie Linux na platformie Microsoft cloud. Ponadto masz dostęp do innych języków, takich jak Ruby, Perl, PHP i Node. js, które są również wstępnie zainstalowane.

Dla tego obrazu DSVM nie są naliczane opłaty za oprogramowanie. Opłaty są naliczane tylko sprzętowych platformy Azure opłaty za użycie, które są oceniane na podstawie rozmiaru maszyny wirtualnej, która aprowizowania. Aby uzyskać więcej informacji na temat opłat za obliczenia, zobacz [stronę listy maszyn wirtualnych w witrynie Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Inne wersje maszyna wirtualna do nauki o danych

A [CentOS](linux-dsvm-intro.md) obrazu jest także możliwe przy użyciu wielu z tych samych narzędzi, jak obraz systemu Ubuntu. Dostępny jest również obraz [systemu Windows](provision-vm.md) .

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem maszyny wirtualnej do nauki o danych dla systemu Linux, musi mieć subskrypcję platformy Azure. Możesz [skorzystać z bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/free/).

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Tworzenie maszyny wirtualnej do nauki o danych dla systemu Linux

Poniżej przedstawiono kroki, aby utworzyć wystąpienie maszyny wirtualnej do nauki o danych dla systemu Linux:

1. Przejdź do listy maszyn wirtualnych na [Azure Portal](https://portal.azure.com/#create/microsoft-dsvm.linux-data-science-vm-ubuntulinuxdsvmubuntu). Jeśli użytkownik nie jest jeszcze zalogowany, może zostać wyświetlony monit o zalogowanie się do konta platformy Azure. 
1. Wybierz pozycję **Utwórz** , aby wyświetlić Kreatora.
    ![Kreator tworzenia maszyny wirtualnej](./media/dsvm-ubuntu-intro/configure-data-science-virtual-machine.png)
1. Wprowadź następujące informacje, aby skonfigurować każdy krok kreatora:

    1. **Podstawy**:
    
       * **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz tę, w której zostanie utworzona i rozliczona. Musisz mieć uprawnienia do tworzenia zasobów dla tej subskrypcji.
       * **Grupa zasobów**: Możesz utworzyć nową lub użyj istniejącej grupy.
       * **Nazwa maszyny wirtualnej**: Wprowadź nazwę tworzonego serwera analizy danych.
       * **Region**: Wybierz najbardziej odpowiednie centrum danych. Zwykle jest to centrum danych, które ma większość Twoich dane, lub znajduje się najbliżej fizycznej lokalizacji, aby uzyskać najszybszy dostęp do sieci.
       * **Opcje dostępności**: Ustaw tę wartość, jeśli chcesz używać tej maszyny wirtualnej w zestawach dostępności lub strefach. W przeciwnym razie pozostaw wartość domyślną.
       * **Obraz**: Pozostaw wartość domyślną.
       * **Rozmiar**: Wybierz typ serwera, który spełnia wymagania funkcjonalne i ograniczenia dotyczące kosztów. Wybierz maszynę wirtualną z serii NC lub ND dla wystąpień maszyn wirtualnych opartych na procesorze GPU. 
       * **Nazwa użytkownika**: Wprowadź nazwę użytkownika administratora.
       * **Klucz publiczny SSH**: Wprowadź klucz publiczny RSA w formacie jednowierszowym. (Można użyć hasła zamiast klucza SSH).
    
    1. **Dyski**:
    
       * **Typ dysku systemu operacyjnego**: Wybierz **SSD w warstwie Premium** , jeśli wolisz dysk SSD (SSD). W przeciwnym razie wybierz **standardowych dysków Twardych**.
    
    1. W przypadku pozostałej części ustawień można użyć wartości domyślnych. Aby uwzględnić wartości inne niż domyślne, umieść kursor na linku informacyjnym, aby uzyskać pomoc. Po zakończeniu wybierz pozycję **Przegląd + Utwórz**.
    
    1. Po zakończeniu walidacji maszyny wirtualnej Sprawdź, czy wszystkie wprowadzone informacje są poprawne. Link kieruje Cię do warunków użytkowania. Na maszynie wirtualnej nie są naliczane dodatkowe opłaty wykraczające poza obliczenia dla rozmiaru serwera wybranego w danych wejściowych **rozmiaru** . Aby rozpocząć Inicjowanie obsługi administracyjnej, wybierz pozycję **Utwórz**.
    
    Aprowizacja powinno zająć około 5 minut. Stan jest wyświetlany w Azure Portal.

## <a name="how-to-access-the-data-science-virtual-machine-for-linux"></a>Jak uzyskiwać dostęp do maszyny wirtualnej do nauki o danych dla systemu Linux

Dostęp do Ubuntu DSVM można uzyskać przy użyciu trzech metod:

- SSH przez sesje usług terminalowych
- X2Go graficzny sesji
- JupyterHub i JupyterLab dla notesów programu Jupyter

Możesz również dołączyć Data Science Virtual Machine do Azure Notebooks do uruchamiania notesów Jupyter na maszynie wirtualnej i pomijania ograniczeń warstwy bezpłatnej usługi. Aby uzyskać więcej informacji, zobacz [Zarządzanie i Konfigurowanie projektów Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>Protokół SSH

Po utworzeniu maszyny Wirtualnej możesz można Zaloguj się do niej przy użyciu protokołu SSH. Użyj poświadczeń konta, które zostały utworzone w **podstawy** sekcji Krok 3 dla interfejsu powłoki tekstu. W systemie Windows można pobrać narzędzie klienta SSH, takie jak [](https://www.putty.org)wyglądająco. Jeśli wolisz korzystać z graficznego pulpitu (X systemu Windows), możesz użyć funkcji przekazywania dalej X11 lub zainstalować klienta X2Go.

> [!NOTE]
> Klient X2Go spisywała się lepiej niż X11 przekazywania do testowania. Zaleca się przy użyciu klienta X2Go dla interfejsu graficznego pulpitu.

### <a name="x2go"></a>X2Go

Maszyna wirtualna z systemem Linux została już zainicjowana z serwerem X2Go i gotowa do akceptowania połączeń klienckich. Aby połączyć pulpit graficzny maszyny Wirtualnej systemu Linux, należy wykonać poniższą procedurę na komputerze klienckim:

1. Pobieranie i instalowanie klienta X2Go dla danej platformy klienta [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Uruchom klienta X2Go, a następnie wybierz pozycję **nowej sesji**. Otwiera okno konfiguracji z wieloma kartami. Wprowadź następujące parametry konfiguracji:
   * **Karta sesji**:
     * **Host**: Wprowadź nazwę hosta lub adres IP Data Science Virtual Machine systemu Linux.
     * **Logowanie**: Wprowadź nazwę użytkownika na maszynie wirtualnej z systemem Linux.
     * **Port SSH**: Pozostaw wartość domyślną równą 22.
     * **Typ sesji**: Zmień wartość na **pulpit Xfce**. Obecnie maszyna wirtualna z systemem Linux obsługuje tylko pulpit pulpit Xfce.
   * **Karta multimedia**: Można wyłączyć obsługę dźwięku i drukowanie klienta, jeśli nie trzeba ich używać.
   * **Foldery udostępnione**: Jeśli chcesz, aby katalogi z komputerów klienckich były zainstalowane na maszynie wirtualnej z systemem Linux, Dodaj do tej karty katalogi komputerów klienckich, które mają być współużytkowane z maszyną wirtualną.

Po zalogowaniu się do maszyny wirtualnej za pomocą klienta SSH lub pulpit Xfce graficznego za pośrednictwem klienta X2Go możesz zacząć korzystać z narzędzi zainstalowanych i skonfigurowanych na maszynie wirtualnej. Na pulpit Xfce można zobaczyć skróty menu aplikacji i ikony pulpitu dla wielu narzędzi.

### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub i JupyterLab

Ubuntu DSVM uruchamia [JupyterHub](https://github.com/jupyterhub/jupyterhub), wieloużytkownikowego serwera Jupyter. Aby nawiązać połączenie, przejdź do\/protokołu https:/Your-VM-IP: 8000 na komputerze przenośnym lub pulpicie. Wprowadź nazwę użytkownika i hasło, które zostały użyte podczas tworzenia maszyny wirtualnej i zaloguj się. Wiele notesów próbki są dostępne do przeglądania i wypróbować.

JupyterLab, następna generacja notesów Jupyter i JupyterHub, jest również dostępna. Aby uzyskać do niego dostęp, zaloguj się do JupyterHub, a następnie przejdź do adresu URL\/https:/Your-VM-IP: 8000/user/nazwa_użytkownika/Lab. Możesz ustawić JupyterLab jako domyślny serwer notesu, dodając ten wiersz do `/etc/jupyterhub/jupyterhub_config.py`:

```python
c.Spawner.default_url = '/lab'
```

## <a name="tools-installed-on-the-data-science-virtual-machine-for-linux"></a>Narzędzia są zainstalowane na maszynie wirtualnej do nauki o danych dla systemu Linux

### <a name="deep-learning-libraries"></a>Biblioteki uczenia głębokiego

#### <a name="cntk"></a>CNTK

Microsoft Cognitive Toolkit to zestaw narzędzi do uczenia głębokiego "open source". Powiązania Python są dostępne w środowisk Conda główny i py35. Ma także narzędzie wiersza polecenia (CNTK), które już znajduje się w ścieżce.

Notesy Python próbki są dostępne w JupyterHub. Aby uruchomić podstawowy przykład w wierszu polecenia, uruchom następujące polecenia w powłoce:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Aby uzyskać więcej informacji, zobacz sekcję CNTK w witrynie [GitHub](https://github.com/Microsoft/CNTK) i [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

#### <a name="caffe"></a>Caffe

Caffe to platforma do uczenia głębokiego z Centrum uczenia i przetwarzania Berkeley. Jest on dostępny w/opt/Caffe. Przykłady można znaleźć w/opt/Caffe/examples.

#### <a name="caffe2"></a>Caffe2

Caffe2 to platforma do uczenia głębokiego z usługi Facebook, która jest oparta na Caffe. Jest on dostępny w języku Python 2,7 w środowisku głównym Conda. Aby go uaktywnić, uruchom następujące polecenie w powłoce:

```bash
source /anaconda/bin/activate root
```

Notesy niektóre przykładowe są dostępne w JupyterHub.

#### <a name="h2o"></a>H2O

H2O to uczenie maszynowe szybkie w pamięci, rozproszonego i analizy predykcyjnej platformy. Pakiet języka Python jest zainstalowany w środowiskach Anaconda głównego i py35. Pakiet języka R jest również instalowany. 

Aby otworzyć H2O z wiersza polecenia, uruchom `java -jar /dsvm/tools/h2o/current/h2o.jar`polecenie. Istnieją różne [Opcje wiersza polecenia](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) , które można skonfigurować. http://localhost:54321 Aby rozpocząć pracę, możesz uzyskać dostęp do interfejsu użytkownika sieci Web przepływu. Notesy próbki są również dostępne w JupyterHub.

#### <a name="keras"></a>Biblioteki Keras

Keras to interfejs API sieci neuronowych wysokiego poziomu w języku Python. Może działać w oparciu o TensorFlow, Microsoft Cognitive Toolkit lub Theano. Jest on dostępny w środowiskach głównych i py35 języka Python.

#### <a name="mxnet"></a>MXNet

MXNet to platforma uczenia głębokiego, przeznaczony dla środowisk wydajność i elastyczność. Ma ona powiązania języków R i Python dołączonym maszyny DSVM. Notesy próbki są objęte JupyterHub i przykładowy kod jest dostępny w /dsvm/samples/mxnet.

#### <a name="nvidia-digits"></a>PROCESORY GPU NVIDIA CYFR

System uczenia procesora GPU o głębokiej technologii NVIDIA, znany jako cyfry, to system upraszczający typowe zadania uczenia głębokiego. Te zadania obejmują zarządzanie danymi, projektowanie i uczenie sieci neuronowych w systemach GPU oraz monitorowanie wydajności w czasie rzeczywistym dzięki zaawansowanej wizualizacji.

CYFRY są dostępne jako usługa o nazwie *cyfry*. Uruchom usługę, a następnie przejdź do http://localhost:5000 na rozpoczęcie pracy.

CYFRY jest również instalowany jako moduł języka Python w środowisku głównym Conda.

#### <a name="tensorflow"></a>TensorFlow

TensorFlow to biblioteka uczenia głębokiego firmy Google. Jest to Biblioteka oprogramowania Open Source służąca do obliczania liczbowego przy użyciu wykresów przepływu danych. TensorFlow jest dostępna w środowisku Python py35, a niektóre notesów próbki są objęte JupyterHub.

#### <a name="theano"></a>Theano

Theano to biblioteka języka Python dla wydajne wykonywanie obliczeń numerycznych. Jest on dostępny w środowiskach głównych i py35 języka Python. 

#### <a name="torch"></a>Torch

Latarka jest naukowa struktura obliczeniowa dzięki szerokiej obsłudze algorytmów uczenia maszynowego. Jest on dostępny w/dsvm/Tools/Torch, a w wierszu polecenia dostępne są te interakcyjne sesje i Menedżer pakietów LuaRocks. Przykłady są dostępne w /dsvm/samples/torch.

PyTorch jest również dostępna w środowisku Anaconda głównego. Przykłady znajdują się w /dsvm/samples/pytorch.

### <a name="microsoft-machine-learning-server"></a>Serwer Microsoft Machine Learning

R jest jednym z najbardziej popularnych języków do analizowania danych i uczenia maszynowego. Jeśli chcesz użyć języka R do analizy, maszyna wirtualna ma Microsoft Machine Learning Server z biblioteką jądra Microsoft R Open i Math. Biblioteka jądra matematycznego optymalizuje operacje matematyczne wspólne w algorytmach analitycznych. Program Microsoft R Open ma 100 procent zgodny z CRAN R, a wszystkie biblioteki języka R opublikowane w CRAN można zainstalować na stronie Microsoft R Open. 

Machine Learning Server umożliwia skalowanie i operacjonalizacji modeli R w usługach sieci Web. Możesz edytować programach języka R w jednym z edytorów domyślne, takich jak program RStudio, vi lub Emacs. Jeśli wolisz, za pomocą edytora Emacs, zostało wstępnie zainstalowane. Pakiet Emacs: ESS (Emacs: mówiący o statystyce) upraszcza pracę z plikami języka R w edytorze emacs:.

Aby otworzyć konsolę języka R, należy wprowadzić wartość **R** w powłoce. To polecenie umożliwia przejście do interaktywnego środowiska. Aby opracować program R, zazwyczaj używany jest Edytor, taki jak Emacs: lub VI, a następnie uruchamianie skryptów w języku R. Dzięki RStudio masz pełny graficzny IDE do tworzenia programu R.

Istnieje również skrypt języka R umożliwiający zainstalowanie [najwyżej 20 pakietów r](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) , jeśli chcesz. Ten skrypt można uruchomić po zakończeniu pracy z interfejsem Interactive języka R. Jak wspomniano wcześniej, możesz otworzyć ten interfejs, wprowadzając polecenie **R** w powłoce.  

### <a name="python"></a>Python

Anaconda Python jest zainstalowany za pomocą języka Python 2.7 i 3.5 środowiska. 2\.7 środowisko jest nazywany _głównego_, i nosi nazwę wersji 3.5 środowiska _py35_. Rozkład ten zawiera podstawowy Python wraz z około 300 najpopularniejszych matematycznych, inżynieria i danych pakietów do analizy.

Środowisko py35 jest ustawieniem domyślnym. Aby aktywować środowisko główne (2,7), użyj tego polecenia:

```bash
source activate root
```

Aby ponownie aktywować środowisko py35, użyj tego polecenia:

```bash
source activate py35
```

Aby wywołać interaktywną sesję języka Python, wprowadź w powłoce język **Python** . 

Zainstaluj dodatkowe biblioteki języka Python za pomocą Conda lub PIP. W przypadku programu PIP najpierw Aktywuj poprawne środowisko, jeśli nie chcesz go domyślnie:

```bash
source activate root
pip install <package>
```

Lub wprowadź pełną ścieżkę do narzędzia pip:

```bash
/anaconda/bin/pip install <package>
```

W przypadku Conda należy zawsze określić nazwę środowiska (py35 lub root):

```bash
conda install <package> -n py35
```

Jeśli korzystasz z interfejsu graficznego lub chcesz skonfigurować przekazywanie X11, możesz wprowadzić **platformy PyCharm itd** , aby otworzyć środowisko IDE platformy PyCharm itd Python. Możesz użyć domyślnego edytorów tekstu. Ponadto można użyć Spyder, środowiska IDE języka Python, które jest powiązane z dystrybucją języka Python Anaconda. Spyder musi mieć graficzny pulpitu lub X11 przekazywania. Pulpit graficzny ma skrót do Spyder.

### <a name="jupyter-notebook"></a>Notes Jupyter

Dystrybucja pakietu Anaconda również jest dostarczany z notesu programu Jupyter, środowisko, aby udostępniać kod i analizy. Notes Jupyter jest dostępna za pośrednictwem JupyterHub. Zaloguj się przy użyciu nazwy użytkownika i hasła lokalnego systemu Linux.

Serwer notesu Jupyter został wstępnie skonfigurowany przy użyciu języka Python 2, 3 języka Python i R jądra. Użyj ikony **Jupyter Notebook** pulpitu, aby otworzyć przeglądarkę i uzyskać dostęp do serwera notesu. Jeśli korzystasz z maszyny wirtualnej za pośrednictwem protokołu SSH lub klienta X2Go, możesz również uzyskać dostęp do serwera notesu Jupyter w [https://localhost:8000/](https://localhost:8000/)systemie.

> [!NOTE]
> Kontynuuj, jeśli otrzymasz ostrzeżeń dotyczących certyfikatów.

Serwer notesu Jupyter dostęp z dowolnego hosta. Wprowadź **nazwę\<DNS lub adres\>IP https://maszyny wirtualnej: 8000/** .

> [!NOTE]
> Port 8000 jest otwarty na zaporze domyślnie, gdy maszyna wirtualna jest aprowizowana. 

Spakowane przykładowe notesy — jeden w języku Python i jeden w języku R. Po uwierzytelnieniu do notesu Jupyter za pomocą nazwy użytkownika i hasła lokalnego systemu Linux możesz zobaczyć link do przykładów na stronie głównej notesu. Nowy Notes można utworzyć, wybierając pozycję **Nowy**, a następnie wybierając odpowiednie jądro języka. Jeśli przycisk **Nowy** nie jest widoczny, wybierz ikonę **Jupyter** w lewym górnym rogu, aby przejść do strony głównej serwera notesu.

### <a name="apache-spark-standalone"></a>Autonomiczne Apache Spark

Autonomiczne wystąpienie Apache Spark jest preinstalowane na DSVM systemu Linux, aby ułatwić tworzenie aplikacji platformy Spark lokalnie przed przetestowaniem i wdrażaniem ich w dużych klastrach. 

Możesz uruchomić programy PySpark za pośrednictwem jądra programu Jupyter. Po otwarciu Jupyter wybierz przycisk **Nowy** i powinna zostać wyświetlona lista dostępnych jądra. **Spark-Python** to jądro PySpark, które umożliwia tworzenie aplikacji platformy Spark przy użyciu języka Python. Do skompilowania programu Spark można także użyć środowiska IDE języka Python, takiego jak platformy PyCharm itd lub Spyder. 

W tym wystąpieniu autonomicznym stos Spark działa w ramach wywołującego programu klienckiego. Ta funkcja przyspiesza i ułatwia rozwiązywanie problemów w porównaniu z tworzeniem w klastrze Spark.

Jupyter udostępnia przykładowy Notes PySpark. Można go znaleźć w katalogu SparkML w katalogu głównym Jupyter ($HOME/notebooks/SparkML/pySpark). 

W przypadku programowania w języku R dla platformy Spark można użyć Microsoft Machine Learning Server, Spark lub sparklyr. 

Przed uruchomieniem w kontekście platformy Spark w Microsoft Machine Learning Server należy wykonać jednorazowy krok konfiguracji w celu włączenia lokalnego jednego węzła usługi Hadoop HDFS i wystąpienia nici. Domyślnie usługi Hadoop jest zainstalowany, ale wyłączone na maszyny DSVM. Aby je włączyć, należy uruchomić następujące polecenia jako główne po raz pierwszy:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Usługi związane z usługą Hadoop można zatrzymać, gdy nie są potrzebne, uruchamiając ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```program.

Katalog/dsvm/samples/MRS zawiera przykład demonstrujący sposób tworzenia i testowania Microsoft Machine Learning Server w zdalnym kontekście Spark (autonomiczne wystąpienie platformy Spark w DSVM).

### <a name="ides-and-editors"></a>Środowiska IDE i edytorów

Można wybrać kilka edytorów kodu, w tym VI/VIM, Emacs:, platformy PyCharm itd, RStudio i IntelliJ. 

Platformy PyCharm itd, RStudio i IntelliJ są edytorami graficznymi. Aby móc z nich korzystać, musisz się zalogować na pulpicie graficznym. Można je otworzyć przy użyciu skrótów menu programu Desktop i aplikacji.

Vim i Emacs: są edytorami tekstowymi. W systemie Emacs: dodatek ESS udostępnia pracę z językiem R łatwiejszym w edytorze emacs:. Więcej informacji można znaleźć w [witrynie sieci Web ESS](https://ess.r-project.org/).

Wartość lateks jest instalowana za pomocą pakietu TeXLive wraz z pakietem dodatku Emacs: o nazwie [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Ten pakiet upraszcza tworzenie dokumentów lateks w Emacs:.  

### <a name="databases"></a>Bazy danych

#### <a name="graphical-sql-client"></a>Graficzny Klient SQL

SQuirrel SQL, graficzny klient SQL, może łączyć się z różnymi bazami danych (takimi jak Microsoft SQL Server i MySQL) i uruchamiać zapytania SQL. Program SQuirrel SQL można uruchomić z graficznej sesji pulpitu (na przykład za pośrednictwem klienta X2Go) przy użyciu ikony pulpitu. Można też uruchomić klienta programu za pomocą następującego polecenia w powłoce:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Przed pierwszym użyciem należy skonfigurować sterowniki i aliasy bazy danych. Sterowniki JDBC znajdują się w/usr/share/Java/jdbcdrivers.

Aby uzyskać więcej informacji, zobacz [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Narzędzia wiersza polecenia do uzyskiwania dostępu do programu Microsoft SQL Server

Pakiet sterownika ODBC dla programu SQL Server dołączono również dwa narzędzia wiersza polecenia:

- Narzędzie **BCP**: Narzędzie bcp zbiorczo kopiuje dane między wystąpieniem Microsoft SQL Server i plikiem danych w formacie określonym przez użytkownika. Narzędzia bcp można użyć do zaimportowania dużej liczby nowych wierszy do tabel SQL Server lub do eksportowania danych z tabel do plików danych. Aby zaimportować dane do tabeli, należy użyć pliku formatu utworzonego dla tej tabeli. Lub należy zrozumieć strukturę tabeli i typy danych, które są prawidłowe dla jego kolumn.

  Aby uzyskać więcej informacji, zobacz [połączenie za pomocą narzędzia bcp](https://msdn.microsoft.com/library/hh568446.aspx).

- **sqlcmd**: Instrukcje języka Transact-SQL można wprowadzać przy użyciu narzędzia sqlcmd. Możesz również wprowadzić procedury systemowe i pliki skryptów w wierszu polecenia. To narzędzie używa ODBC do uruchamiania partii Transact-SQL.

  Aby uzyskać więcej informacji, zobacz [połączenie przy użyciu narzędzia sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Istnieją pewne różnice w tym narzędziu między platformami Linux i Windows. Zobacz dokumentację, aby uzyskać szczegółowe informacje.

#### <a name="database-access-libraries"></a>Biblioteki dostępu do bazy danych

Biblioteki są dostępne w językach R i Python na potrzeby dostępu do bazy danych:

* W języku R można użyć pakietu RODBC lub pakietu dplyr do wykonywania zapytań lub uruchamiania instrukcji SQL na serwerze bazy danych.
* W języku Python Biblioteka moduł pyodbc zapewnia dostęp do bazy danych za pomocą ODBC jako warstwy źródłowej.  

### <a name="azure-tools"></a>Narzędzia platformy Azure

Następujące narzędzia platformy Azure są zainstalowane na maszynie Wirtualnej:

* **Interfejs wiersza polecenia platformy Azure**: Korzystając z interfejsu wiersza polecenia na platformie Azure, można tworzyć zasoby platformy Azure i zarządzać nimi za pomocą poleceń powłoki. Aby otworzyć narzędzia platformy Azure, wprowadź **Pomoc platformy Azure**. Aby uzyskać więcej informacji, zobacz [stronę z dokumentacją dotyczącą interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Eksplorator usługi Azure Storage**: Eksplorator usługi Azure Storage jest graficznym narzędziem, które służy do przeglądania obiektów przechowywanych na koncie usługi Azure Storage oraz przekazywania i pobierania danych do i z obiektów blob platformy Azure. Można uzyskać dostęp do Eksploratora usługi Storage z ikony skrótu na pulpicie. Możesz również otworzyć ją z poziomu monitu powłoki, wprowadzając **StorageExplorer**. Użytkownik musi być zalogowany z poziomu klienta usługi X2Go lub mieć skonfigurowane przekazywanie X11.
* **Biblioteki platformy Azure**: Poniżej przedstawiono niektóre z wstępnie zainstalowanych bibliotek.
  
  * **Python**: Biblioteki związane z platformą Azure w języku Python to *Azure*, Azure, *pydocumentdb*i *moduł pyodbc*. Pierwsze trzy bibliotek umożliwia dostęp do usług Azure storage, Azure Machine Learning i Azure Cosmos DB (bazę danych NoSQL na platformie Azure). Czwarty biblioteka, moduł pyodbc (wraz z sterownik Microsoft ODBC dla programu SQL Server), umożliwia dostęp do programu SQL Server, usługi Azure SQL Database i Azure SQL Data Warehouse za pomocą języka Python przy użyciu interfejsu ODBC. Wprowadź **listy pip** aby zobaczyć wszystkie wymienione biblioteki. Pamiętaj uruchomić to polecenie zarówno język Python 2.7 i 3.5 środowiska.
  * **R**: Biblioteki związane z platformą Azure w języku R to Azure i RODBC.
  * **Język Java**: Listę bibliotek Java platformy Azure można znaleźć w katalogu/dsvm/sdk/AzureSDKJava na maszynie wirtualnej. Biblioteki klucza to Azure sterowniki magazynów i zarządzania interfejsami API usługi Azure Cosmos DB i JDBC dla programu SQL Server.  

Możesz uzyskać dostęp [witryny Azure portal](https://portal.azure.com) z wstępnie zainstalowanymi przeglądarki Firefox. W witrynie Azure portal można utworzyć, zarządzanie i monitorowanie zasobów platformy Azure.

### <a name="azure-machine-learning"></a>Azure Machine Learning

Usługa Azure Machine Learning to w pełni zarządzana usługa w chmurze umożliwiająca tworzenie, wdrażanie i udostępnianie rozwiązań do analizy predykcyjnej. Możesz tworzyć swoje eksperymentów i modeli z usługi Azure Machine Learning Studio. Możesz uzyskać do niego dostęp z przeglądarki sieci Web na Data Science Virtual Machine, odwiedzając [Microsoft Azure Machine Learning](https://studio.azureml.net).

Po zalogowaniu się do Azure Machine Learning Studio możesz użyć kanwy eksperymentowania, aby utworzyć przepływ logiczny dla algorytmów uczenia maszynowego. Masz również dostęp do notesu Jupyter, który jest hostowany w Azure Machine Learning i może bezproblemowo współpracować z eksperymentami w Machine Learning Studio. 

Operacjonalizowanie modeli, utworzonych przez opakowywanie je w interfejsie usługi sieci web uczenia maszynowego. Modele uczenia maszynowego operacjonalizowania umożliwiają klientom w dowolnym języku wywoływanie prognoz z tych modeli. Aby uzyskać więcej informacji, zobacz [dokumentacja usługi Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Możesz również kompilować modele w języku R lub Python na maszynie wirtualnej, a następnie wdrożyć je w środowisku produkcyjnym na Azure Machine Learning. Firma Microsoft zainstalowano bibliotek w języku R (**usługi Azure ml**) i języka Python (**usługi Azure ml**) Aby włączyć tę funkcję.

Aby uzyskać informacje na temat sposobu wdrażania modeli w języku R i Python do Azure Machine Learning, zobacz [dziesięć rzeczy, które możesz wykonać na Data Science Virtual Machine](vm-do-ten-things.md).

> [!NOTE]
> Te instrukcje zostały opracowane dla Data Science Virtual Machine wersji systemu Windows. Jednak informacje o wdrażaniu modeli usługi Azure Machine Learning ma zastosowanie do maszyny Wirtualnej systemu Linux.

### <a name="machine-learning-tools"></a>Machine learning narzędzia

Maszyna wirtualna zawiera narzędzia i algorytmy uczenia maszynowego, które zostały wstępnie skompilowane i wstępnie zainstalowane lokalnie. Należą do nich:

* **Vowpal Wabbit**: Krótki algorytm uczenia w trybie online.
* **xgboost**: Narzędzie, które zapewnia zoptymalizowane, podwyższające algorytmy drzewa.
* **Rattle**: Narzędzie graficzne oparte na języku R ułatwiające eksplorowanie i modelowanie danych.
* **Python**: Środowisko Python Anaconda jest powiązane z algorytmami uczenia maszynowego z bibliotekami, takimi jak Scikit — uczenie się. Inne biblioteki można zainstalować za pomocą `pip install` polecenia.
* **LightGBM**: Szybka, rozproszona platforma do tworzenia gradientów o wysokiej wydajności oparta na algorytmach drzewa decyzyjnego.
* **R**: Zaawansowana biblioteka funkcji uczenia maszynowego jest dostępna dla języka R. wstępnie zainstalowane biblioteki to LM, GLM, randomForest i rpart. Aby zainstalować inne biblioteki, należy uruchomić następujące polecenie:
  
        install.packages(<lib name>)

Poniżej przedstawiono dodatkowe informacje o narzędziach pierwsze trzy usługi machine learning na liście.

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit jest system, który korzysta z technik, takich jak online, wyznaczania wartości skrótu, allreduce, redukcji, learning2search, są aktywne, uczenia maszynowego i szkolenia interaktywne.

Aby uruchomić narzędzie w podstawowym przykładzie, użyj następujących poleceń:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Istnieją inne, większy pokazy, w tym katalogu. Aby uzyskać więcej informacji na temat Vowpal Wabbit, zobacz [sekcję dotyczącą witryny GitHub](https://github.com/JohnLangford/vowpal_wabbit) i [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost

Biblioteka xgboost jest zaprojektowana i zoptymalizowana pod kątem podwyższania poziomu (drzewa). Celem tej biblioteki jest wypychania limity obliczeń maszyn do skrajnymi poziomami niezbędnej do zapewnienia drzewa na dużą skalę, zwiększania wyniku, który jest skalowalny, przenośne i dokładne.

Jest on dostarczany jako wiersz polecenia i biblioteka języka R. Aby użyć tej biblioteki w języku R, można uruchomić interaktywną sesję języka R (wprowadzając polecenie **R** w powłoce) i załadować bibliotekę.

Oto prosty przykład, który można uruchomić w wierszu polecenia języka R:

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

Aby uruchomić wiersz polecenia xgboost, poniżej przedstawiono polecenia do uruchomienia w powłoce:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Plik. model jest zapisywana w określonym katalogu. Informacje na temat tego przykładu demonstracyjnego można znaleźć [w witrynie GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Aby uzyskać więcej informacji na temat xgboost, zobacz [stronę dokumentacji xgboost](https://xgboost.readthedocs.org/en/latest/) i jej [repozytorium GitHub](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Rattle

Rattle ( **R** **A**nalityczny **T**pismo odręczne — narzędzie **T**o **L**zdobyć **E** asily) używa Eksplorowanie i modelowanie danych z Graficznym interfejsem użytkownika. Przedstawia informacje o statystycznych i wizualne podsumowania danych, dane przekształceń, które mogą być łatwo modelowane, kompilacje nadzorowanych i nienadzorowanych modeli z danych, przedstawia w formie graficznej, informacje o wydajności modeli i zestawy danych nowej oceny. Generuje kod R, replikowanie operacji w interfejsie użytkownika, które można uruchomić bezpośrednio w języku R lub wykorzystywane jako punktu wyjścia do dalszej analizy.

Aby uruchomić Rattle, musisz być w graficznym logowania w sesji pulpitu. Na terminalu wprowadź **r** , aby otworzyć środowisko języka r. W wierszu polecenia języka R, wpisz następujące polecenia:

```R
library(rattle)
rattle()
```

Teraz zostanie otwarty interfejs graficzny z zestawem kart. Poniższe kroki szybkiego startu w programie Rattle umożliwiają użycie przykładowego zestawu danych pogody i skompilowanie modelu. W niektórych krokach zostanie wyświetlony monit o automatyczne zainstalowanie i załadowanie niektórych wymaganych pakietów języka R, które nie znajdują się jeszcze w systemie.

> [!NOTE]
> Jeśli nie masz dostępu do instalacji pakietu w katalogu systemowym (domyślnie), w oknie konsoli języka R może zostać wyświetlony monit o zainstalowanie pakietów w bibliotece osobistej. Odpowiedź **y** Jeśli widzisz te monity.

1. Wybierz pozycję **Wykonaj**.
1. Zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz użyć przykładowego zestawu danych pogody. Wybierz pozycję **tak** , aby załadować przykład.
1. Wybierz kartę **model** .
1. Wybierz pozycję **Execute (wykonaj** ), aby skompilować drzewo decyzyjne.
1. Wybierz pozycję **Rysuj** , aby wyświetlić drzewo decyzyjne.
1. Wybierz opcję **Las** i wybierz opcję **Execute (wykonaj** ), aby skompilować losowo Las.
1. Wybierz kartę **szacowanie** .
1. Wybierz opcję **ryzyko** , a następnie wybierz pozycję **Wykonaj** , aby wyświetlić wykresy z dwoma **ryzykiem (skumulowane)** .
1. Wybierz kartę **Dziennik** , aby wyświetlić wygenerowany kod R dla powyższych operacji.
   (Z powodu błędu w bieżącej wersji programu Rattle należy wstawić **#** znak przed eksportem **tego dziennika** do tekstu dziennika).
1. Wybierz przycisk **Eksportuj** , aby zapisać plik skryptu języka R o nazwie *weather_script. R* do folderu macierzystego.

Możesz wyjść z Rattle i R. Teraz można zmodyfikować wygenerowany skrypt języka R. Można również użyć skryptu w ten sposób i uruchomić go w dowolnym momencie, aby powtórzyć wszystko, co zostało zrobione w interfejsie użytkownika Rattle. Szczególnie w przypadku początkujących w języku R, jest to sposób, aby szybko przeprowadzić analizę i uczenie maszynowe w prostym interfejsie graficznym, podczas gdy automatycznie generuje kod w języku R, aby zmodyfikować lub poznać.

## <a name="next-steps"></a>Następne kroki

Poniżej przedstawiono, jak można kontynuować Twoją naukę oraz eksploracji:

* Analiza [danych w Data Science Virtual Machine dla systemu Linux](linux-dsvm-walkthrough.md) zawiera informacje na temat wykonywania kilku typowych zadań związanych z nauką o danych przy użyciu DSVM systemu Linux. 
* Zapoznaj się z różnymi narzędziami do nauki o danych w DSVM, korzystając z narzędzi opisanych w tym artykule. Aby uzyskać więcej informacji `dsvm-more-info` na temat narzędzi zainstalowanych na maszynie wirtualnej, można również uruchomić na powłoce w obrębie maszyny wirtualnej.  
* Dowiedz się, jak tworzyć rozwiązania analityczne end-to-end systematycznie przy użyciu [zespołu danych dla celów naukowych](https://aka.ms/tdsp).
* Odwiedź stronę [galerii Azure AI](https://gallery.azure.ai/) maszyny nauki i dane analizy przykładów, które korzystają z usług Azure AI.
