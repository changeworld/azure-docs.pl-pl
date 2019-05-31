---
title: Tworzenie maszyny wirtualnej do nauki o danych Ubuntu Linux
titleSuffix: Azure
description: Skonfiguruj i Utwórz Data Science maszyny wirtualnej dla systemu Linux (Ubuntu) na platformie Azure w celu analizy i uczenia maszynowego.
services: machine-learning
documentationcenter: ''
author: gopitk
ms.author: gokuma
manager: cgronlun
ms.custom: seodec18
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: 5a9fdebc8db0c2a1acc20a894f80cfcc87fb89d5
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236492"
---
# <a name="provision-the-data-science-virtual-machine-for-linux-ubuntu"></a>Aprowizowanie maszyny wirtualnej do nauki o danych dla systemu Linux (Ubuntu)

Maszyna wirtualna do nauki o danych dla systemu Linux jest obraz maszyny wirtualnej z systemem Ubuntu, który ułatwia rozpoczynanie pracy z usługą machine learning, w tym głębokiego uczenia na platformie Azure. Narzędzia uczenia głębokiego obejmują:

* [Caffe](https://caffe.berkeleyvision.org/): Stworzona z myślą o szybkości, expressivity i Modułowość platforma do uczenia głębokiego
* [Caffe2](https://github.com/caffe2/caffe2): Dla wielu platform wersję Caffe
* [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK): Zestaw narzędzi do oprogramowania przez firmę Microsoft Research do uczenia głębokiego
* [H2O](https://www.h2o.ai/): Platformę danych big data typu open-source oraz graficznego interfejsu użytkownika
* [Biblioteki Keras](https://keras.io/): Ogólne sieci neuronowej interfejsu API w języku Python, TensorFlow, Cognitive Toolkit firmy Microsoft i Theano
* [MXNet](https://mxnet.io/): Biblioteka elastyczne i wydajne uczenia głębokiego, wiele powiązań języka
* [PROCESORY GPU NVIDIA CYFR](https://developer.nvidia.com/digits): Graficzny system, który upraszcza typowe zadania uczenia głębokiego
* [PyTorch](https://pytorch.org/): Bibliotekę języka Python wysokiego poziomu o obsługę dynamicznej sieci
* [TensorFlow](https://www.tensorflow.org/): Biblioteki typu open source dla analizy maszynowej od firmy Google
* [Theano](http://deeplearning.net/software/theano/): Biblioteka języka Python do definiowania, optymalizowanie i efektywnie oceny wyrażenia matematyczne, obejmujące Wielowymiarowe tablice
* [Latarka](http://torch.ch/): Naukowa struktura obliczeniowa dzięki szerokiej obsłudze algorytmów uczenia maszynowego
* CUDA, cuDNN i sterownik NVIDIA
* Wiele przykładowych aplikacji Jupyter notebooks

Wszystkie biblioteki są wersje procesora GPU, chociaż mogą również uruchamiać na Procesorze.

Maszyna wirtualna do nauki o danych dla systemu Linux zawiera także popularne narzędzia do danych do analizy i rozwoju działalności, w tym:

* Microsoft R Server Developer Edition, za pomocą oprogramowania Microsoft R Open
* Anaconda Python dystrybucji (wersja 2.7 i 3.5), łącznie z bibliotekami analiz danych popularnych
* JuliaPro - wyselekcjonowanych dystrybucja języka Julia z popularnymi bibliotekami analizy danych i naukowe
* Wystąpienie platformy Spark autonomiczne i jednego węzła usługi Hadoop (HDFS, Yarn)
* JupyterHub — Obsługa języka R, Python, PySpark, Julia jądra wielodostępnym serwer notesu Jupyter
* Eksplorator usługi Azure Storage
* Usługa Azure interfejs wiersza polecenia (CLI) do zarządzania zasobami platformy Azure
* Machine learning narzędzia
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): Nauka system uczenia maszynowego szybkie, obsługa technik, takich jak online, wyznaczanie wartości skrótu, allreduce, redukcji, learning2search, są aktywne i interaktywne
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): To narzędzie, zapewniając szybkie i dokładne wzmocnionego drzewa wykonania
  * [Rattle](https://togaware.com/rattle/): Graficzne narzędzie, które ułatwia rozpoczęcie korzystania z analizy danych i uczenia maszynowego w R łatwo
  * [LightGBM](https://github.com/Microsoft/LightGBM): Ulepszanie framework gradientu, szybka i rozproszonej o wysokiej wydajności
* Zestaw Azure SDK w języku Java, Python, node.js, Ruby, PHP
* Bibliotek języka R i Python do użycia w usłudze Azure Machine Learning i innymi usługami platformy Azure
* Narzędzia programistyczne i Redaktorzy (RStudio, platformy PyCharm, IntelliJ, Emacs, vim)

Nauki o danych obejmuje iteracja w sekwencji zadań:

1. Znajdowanie, ładowanie i przetwarzanie wstępne danych
1. Tworzenie i testowanie modeli
1. Wdrażanie modeli do użycia w inteligentnych aplikacjach

Analitycy danych za pomocą różnych narzędzi w celu wykonania tych zadań. Może być bardzo czasochłonne znajdziesz odpowiednie wersje oprogramowania, a następnie pobrać, skompilować i instalowania tych wersji.

Maszyna wirtualna do nauki o danych dla systemu Linux może znacznie ułatwić ten ciężar. Użyj go w szybkim stworzeniu projektu analizy. Pozwala ona do pracy nad zadaniami w różnych językach, w tym języka R, Python, SQL, Java i C++. Uwzględnione na maszynie wirtualnej i zestawu Azure SDK umożliwia tworzenie aplikacji przy użyciu różnych usług w systemie Linux na platformie Microsoft cloud. Ponadto masz dostęp do innych językach, takich jak Ruby, Perl, PHP i node.js, które są również wstępnie zainstalowane.

Opłaty nie będą oprogramowania dla tego obrazu maszyny Wirtualnej do nauki o danych. Opłaty są naliczane tylko sprzętowych platformy Azure opłaty za użycie, które są oceniane na podstawie rozmiaru maszyny wirtualnej, która aprowizowania. Szczegółowe informacje na temat opłat obliczeniowych można znaleźć na [strony maszyny Wirtualnej w portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Inne wersje maszyna wirtualna do nauki o danych

A [CentOS](linux-dsvm-intro.md) obrazu jest także możliwe przy użyciu wielu z tych samych narzędzi, jak obraz systemu Ubuntu. A [Windows](provision-vm.md) obrazu jest także dostępna.

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem maszyny wirtualnej do nauki o danych dla systemu Linux, musi mieć subskrypcję platformy Azure. Aby uzyskać subskrypcję, zobacz [uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/free/).

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Tworzenie maszyny wirtualnej do nauki o danych dla systemu Linux

Poniżej przedstawiono kroki, aby utworzyć wystąpienie maszyny wirtualnej do nauki o danych dla systemu Linux:

1. Przejdź do maszyny wirtualnej, w witrynie [witryny Azure portal](https://portal.azure.com/#create/microsoft-dsvm.linux-data-science-vm-ubuntulinuxdsvmubuntu). Może być wyświetlony monit do logowania się do konta platformy Azure, jeśli użytkownik są nie już zalogowany. 
1. Kliknij przycisk **Utwórz** (u dołu), aby wywołać Kreatora.![ Konfigurowanie — — — maszyna wirtualna analizy danych](./media/dsvm-ubuntu-intro/configure-data-science-virtual-machine.png)
1. Dane wejściowe dla każdej z czynności w Kreatorze (wyliczany na poprzednim rysunku po prawej stronie) użyty do utworzenia maszyny wirtualnej do nauki o danych firmy Microsoft można znaleźć w poniższych sekcjach. Poniżej przedstawiono dane wejściowe wymagane do skonfigurowania każdego z następujących czynności:

   a. **Podstawy**:

   * **Nazwa**: Nazwa tworzonej serwera do nauki o danych.
   * **Typ dysku maszyny Wirtualnej**: Wybierz **dysku Premium SSD** Jeśli wolisz dysków półprzewodnikowych (SSD). W przeciwnym razie wybierz **standardowych dysków Twardych**. 
   * **Nazwa użytkownika**: Pierwsze konto logowania identyfikatora.
   * **Hasło**: Pierwsze hasło do konta (zamiast hasła można użyć klucza publicznego SSH).
   * **Subskrypcja**: Jeśli masz więcej niż jedną subskrypcję, wybierz ten, na którym maszyna ma zostać utworzona i rozliczane. Musisz mieć uprawnienia do tworzenia zasobów dla tej subskrypcji.
   * **Grupa zasobów**: Możesz utworzyć nową lub użyj istniejącej grupy.
   * **Lokalizacja**: Wybierz centrum danych, które jest najbardziej odpowiednie. Zazwyczaj jest centrum danych, który zawiera większość danych lub znajduje się najbliżej Twojej lokalizacji fizycznej najszybszy dostęp do sieci.

   b. **Rozmiar**:

   * Wybierz jeden z typów serwerów, które spełnia Twoje wymagania funkcjonalne i ograniczenia kosztów. Wybierz kontroler sieci lub klasa ND maszyny Wirtualnej na potrzeby wystąpienia maszyny Wirtualnej opartej na procesorze GPU. [Dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/) strona zawiera listę regionów za pomocą procesorów GPU.

   c. **Ustawienia**:

   * W większości przypadków można po prostu użyć wartości domyślne. Aby uwzględnić wartości innych niż domyślne, umieść kursor informacyjny łącze, aby uzyskać pomoc dotyczącą określonych pól.

   d. **Podsumowanie**:

   * Sprawdź, czy wszystkie wprowadzone informacje jest poprawna. Link znajduje się na warunki użytkowania. Maszyna wirtualna nie ma żadnych dodatkowych kosztów poza obliczenia rozmiaru serwera wybranej w ramach **rozmiar** kroku. Aby rozpocząć, aprowizacja, kliknij przycisk **Utwórz**. 

Aprowizacja powinno zająć około 5 minut. Stan aprowizacji jest wyświetlany w witrynie Azure portal.

## <a name="how-to-access-the-data-science-virtual-machine-for-linux"></a>Jak uzyskiwać dostęp do maszyny wirtualnej do nauki o danych dla systemu Linux

Aby uzyskać dostęp maszyny wirtualnej DSVM Ubuntu za pomocą trzech metod:

1. SSH przez sesje usług terminalowych
1. X2Go graficzny sesji
1. JupyterHub i JupyterLab dla notesów programu Jupyter

Maszyna wirtualna do nauki o danych można także dołączyć do notesów usługi Azure do uruchamiania aplikacji Jupyter notebooks na maszynie Wirtualnej i obejścia ograniczeń w warstwie bezpłatnej usługi. Aby uzyskać więcej informacji, zobacz [zarządzanie i konfigurować projekty notesów — warstwa wystąpień obliczeniowych](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

### <a name="ssh"></a>Protokół SSH

Po utworzeniu maszyny Wirtualnej możesz można Zaloguj się do niej przy użyciu protokołu SSH. Użyj poświadczeń konta, które zostały utworzone w **podstawy** sekcji Krok 3 dla interfejsu powłoki tekstu. W systemie Windows możesz pobrać narzędzia klienta SSH, takie jak program [Putty](https://www.putty.org). Jeśli wolisz graficzny desktop (X w systemie Windows), możesz użyć X11 przekazywania w programie Putty lub zainstalować klienta X2Go.

> [!NOTE]
> Klient X2Go spisywała się lepiej niż X11 przekazywania do testowania. Zaleca się przy użyciu klienta X2Go dla interfejsu graficznego pulpitu.

### <a name="x2go"></a>X2Go

Maszyny Wirtualnej systemu Linux jest już zainicjowana przy użyciu serwera X2Go i gotowy do akceptowania połączeń klienta. Aby połączyć pulpit graficzny maszyny Wirtualnej systemu Linux, należy wykonać poniższą procedurę na komputerze klienckim:

1. Pobieranie i instalowanie klienta X2Go dla danej platformy klienta [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Uruchom klienta X2Go, a następnie wybierz pozycję **nowej sesji**. Otwiera okno konfiguracji z wieloma kartami. Wprowadź następujące parametry konfiguracji:
   * **Karta sesji**:
     * **Host**: Nazwa hosta lub adres IP maszyny wirtualnej do nauki o danych systemu Linux.
     * **Zaloguj się**: Nazwa użytkownika na maszynie Wirtualnej systemu Linux.
     * **SSH portu**: Pozostaw to pole o 22, wartością domyślną.
     * **Typ sesji**: Zmień wartość na XFCE. Obecnie maszyny Wirtualnej systemu Linux obsługuje tylko pulpit xfce.
   * **Karta Media**: Można wyłączyć dźwięk pomocy technicznej i klienta, drukowanie, jeśli nie potrzebujesz umożliwić ich używanie.
   * **Foldery udostępnione**: Katalogi z maszyn klienta zainstalowanego na maszynie Wirtualnej systemu Linux, dodać katalogi maszyny klienta, które chcesz się podzielić z maszyną Wirtualną na tej karcie.

Po zalogowaniu do maszyny Wirtualnej przy użyciu klienta SSH lub graficznego pulpit xfce za pomocą klienta X2Go, jesteś gotowy rozpocząć korzystanie z narzędzia, które są zainstalowane i skonfigurowane na maszynie Wirtualnej. Na XFCE widać ikony pulpitu i skróty w menu aplikacji dla wielu narzędzi.

### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub i JupyterLab

Uruchamia Ubuntu DSVM [JupyterHub](https://github.com/jupyterhub/jupyterhub), serwer programu Jupyter wielu użytkowników. Aby nawiązać połączenie, przejdź do protokołu https:\// z-maszyny wirtualnej — ip:8000 na komputerze przenośnym lub pulpit, wprowadź nazwę użytkownika i hasło, których użyto do utworzenia maszyny Wirtualnej, a następnie zaloguj się. Wiele notesów próbki są dostępne do przeglądania i wypróbować.

JupyterLab, następna generacja notesów Jupyter i JupyterHub, jest również dostępna. Aby uzyskać do niego dostęp, zaloguj się do JupyterHub, a następnie przejdź do adresu URL https:\// z-maszyny wirtualnej — ip:8000/użytkownik/your-username/laboratorium. JupyterLab można ustawić jako domyślny serwer notesu, dodając ten wiersz, aby */etc/jupyterhub/jupyterhub_config.py*:

```python
c.Spawner.default_url = '/lab'
```

## <a name="tools-installed-on-the-data-science-virtual-machine-for-linux"></a>Narzędzia są zainstalowane na maszynie wirtualnej do nauki o danych dla systemu Linux

### <a name="deep-learning-libraries"></a>Biblioteki uczenia głębokiego

#### <a name="cntk"></a>CNTK

Microsoft Cognitive Toolkit to open source, szczegółowe, zestaw narzędzi do uczenia. Powiązania Python są dostępne w środowisk Conda główny i py35. Ma również narzędzia wiersza polecenia (cntk), który już znajduje się w ŚCIEŻCE.

Notesy Python próbki są dostępne w JupyterHub. Aby uruchomić podstawowy przykład w wierszu polecenia, uruchom następujące polecenia w powłoce:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Aby uzyskać więcej informacji, zobacz sekcję CNTK [GitHub](https://github.com/Microsoft/CNTK)i [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

#### <a name="caffe"></a>Caffe

Caffe to platforma do uczenia głębokiego z Centrum uczenia i przetwarzania Berkeley. Jest ona dostępna w /opt/caffe. Przykłady można znaleźć w /opt/caffe/examples.

#### <a name="caffe2"></a>Caffe2

Caffe2 to platforma do uczenia głębokiego z usługi Facebook, która jest oparta na Caffe. Jest ona dostępna w języku Python 2.7 w środowisku głównym Conda. Aby go uaktywnić, uruchom następujące polecenie z poziomu powłoki:

```bash
source /anaconda/bin/activate root
```

Notesy niektóre przykładowe są dostępne w JupyterHub.

#### <a name="h2o"></a>H2O

H2O to uczenie maszynowe szybkie w pamięci, rozproszonego i analizy predykcyjnej platformy. Pakiet języka Python jest zainstalowany w środowiskach Anaconda głównego i py35. Pakiet języka R jest również instalowany. Aby uruchomić H2O z wiersza polecenia, uruchom `java -jar /dsvm/tools/h2o/current/h2o.jar`; istnieją różne [opcje wiersza polecenia](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) , który chcesz skonfigurować. Interfejs użytkownika sieci Web Flow jest możliwy, przechodząc do http://localhost:54321 na rozpoczęcie pracy. Notesy próbki są również dostępne w JupyterHub.

#### <a name="keras"></a>Biblioteki Keras

Biblioteki Keras jest ogólny sieci neuronowej interfejsu API w języku Python, który można uruchomić na górze, TensorFlow, Cognitive Toolkit firmy Microsoft lub Theano. Jest ona dostępna w środowiskach Python głównego i py35.

#### <a name="mxnet"></a>MXNet

MXNet to platforma uczenia głębokiego, przeznaczony dla środowisk wydajność i elastyczność. Ma ona powiązania języków R i Python dołączonym maszyny DSVM. Notesy próbki są objęte JupyterHub i przykładowy kod jest dostępny w /dsvm/samples/mxnet.

#### <a name="nvidia-digits"></a>PROCESORY GPU NVIDIA CYFR

Procesory GPU NVIDIA głębokiego uczenia procesora GPU szkolenia systemu, nazywany CYFR, to system można uproszczenie typowe zadania uczenia głębokiego, takich jak zarządzanie danymi, projektowania i programowania sieci neuronowych szkolenia w systemach procesora GPU i monitorowanie wydajności w czasie rzeczywistym za pomocą zaawansowanych wizualizacji.

CYFRY jest dostępna jako usługa o nazwie cyfr. Uruchom usługę, a następnie przejdź do http://localhost:5000 na rozpoczęcie pracy.

CYFRY jest również instalowany jako moduł języka Python w środowisku głównym Conda.

#### <a name="tensorflow"></a>TensorFlow

TensorFlow to biblioteka uczenia głębokiego firmy Google. To biblioteka oprogramowania typu open source umożliwiająca wykonywanie obliczeń numerycznych przy użyciu wykresów przepływu danych. TensorFlow jest dostępna w środowisku Python py35, a niektóre notesów próbki są objęte JupyterHub.

#### <a name="theano"></a>Theano

Theano to biblioteka języka Python dla wydajne wykonywanie obliczeń numerycznych. Jest ona dostępna w środowiskach Python głównego i py35. 

#### <a name="torch"></a>Torch

Latarka jest naukowa struktura obliczeniowa dzięki szerokiej obsłudze algorytmów uczenia maszynowego. Jest on dostępny w /dsvm/tools/torch, a Ty interaktywnej sesji i luarocks Menedżera pakietów są dostępne w wierszu polecenia. Przykłady są dostępne w /dsvm/samples/torch.

PyTorch jest również dostępna w środowisku Anaconda głównego. Przykłady znajdują się w /dsvm/samples/pytorch.

### <a name="microsoft-r-server"></a>Microsoft R Server

R jest jednym z najbardziej popularnych języków do analizowania danych i uczenia maszynowego. Jeśli chcesz używać języka R do analizy, maszyna wirtualna ma Microsoft R Server (PANI) za pomocą programu Microsoft R Open (MRO) i matematyczne biblioteki jądra (MKL). MKL optymalizuje operacje matematyczne, często używany w algorytmów analitycznych. MRO wynosi 100% zgodny z sieci CRAN-R, a żadnego z bibliotek języka R, opublikowane w usłudze CRAN można zainstalować na MRO. PANI umożliwia skalowanie i operacjonalizacji modeli języka R do usług sieci web. Możesz edytować programach języka R w jednym z edytorów domyślne, takich jak program RStudio, vi lub Emacs. Jeśli wolisz, za pomocą edytora Emacs, zostało wstępnie zainstalowane. Emacs: pakiet TĘPU (Statystyka mówi Emacs) upraszcza pracę z plikami języka R w edytorze Emacs.

Aby uruchomić R konsoli, po prostu wpisz **R** w powłoce. To polecenie umożliwia przejście do środowiska interaktywnego. Aby opracować programu R, zazwyczaj używają edytorem takim jak Emacs lub vi, a następnie uruchom skrypty w języku R. Za pomocą programu RStudio masz pełną graficznego środowiska IDE do tworzenia programu R.

Jest także skrypt języka R do zainstalowania [pakietów języka R z pierwszych 20](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) chcącym. Ten skrypt można uruchomić po R interaktywny interfejs, który można wprowadzić (jak wspomniano), wpisując **R** w powłoce.  

### <a name="python"></a>Python

Anaconda Python jest zainstalowany za pomocą języka Python 2.7 i 3.5 środowiska. 2.7 środowisko jest nazywany _głównego_, i nosi nazwę wersji 3.5 środowiska _py35_. Rozkład ten zawiera podstawowy Python wraz z około 300 najpopularniejszych matematycznych, inżynieria i danych pakietów do analizy.

Środowisko py35 jest ustawieniem domyślnym. Aby aktywować środowisko główny (w wersji 2.7):

```bash
source activate root
```

Aby ponownie aktywować środowisko py35:

```bash
source activate py35
```

Aby wywołać interaktywnych sesji języka Python, po prostu wpisz **python** w powłoce. 

Zainstaluj dodatkowe biblioteki języka Python za pomocą ```conda``` lub ```pip``` . Dla narzędzia pip należy najpierw aktywować odpowiednie środowisko, jeśli nie chcesz, wartość domyślna:

```bash
source activate root
pip install <package>
```

Lub wprowadź pełną ścieżkę do narzędzia pip:

```bash
/anaconda/bin/pip install <package>
```

Dla środowiska conda, narzędzia należy zawsze określić nazwę środowiska (_py35_ lub _głównego_):

```bash
conda install <package> -n py35
```

Jeśli masz na interfejsu graficznego lub X11 przekazywania zestawu w górę, należy wpisać **platformy pycharm** można uruchomić środowisko IDE języka Python platformy PyCharm. Możesz użyć domyślnego edytorów tekstu. Ponadto można użyć Spyder, środowisko IDE języka Python, który jest powiązany z dystrybucji Anaconda Python. Spyder musi mieć graficzny pulpitu lub X11 przekazywania. Skrót do Spyder znajduje się w pulpicie graficznego.

### <a name="jupyter-notebook"></a>Notes Jupyter

Dystrybucja pakietu Anaconda również jest dostarczany z notesu programu Jupyter, środowisko, aby udostępniać kod i analizy. Notes Jupyter jest dostępna za pośrednictwem JupyterHub. Zaloguj się przy użyciu lokalnej nazwy użytkownika systemu Linux i hasło.

Serwer notesu Jupyter został wstępnie skonfigurowany przy użyciu języka Python 2, 3 języka Python i R jądra. Brak ikony pulpitu, o nazwie "Notesu programu Jupyter", aby uruchomić przeglądarkę, aby uzyskać dostęp do serwera notesu. Jeśli na maszynie Wirtualnej przy użyciu klienta SSH lub X2Go, możesz również odwiedzić [ https://localhost:8000/ ](https://localhost:8000/) dostęp do serwera notesu programu Jupyter.

> [!NOTE]
> Kontynuuj, jeśli otrzymasz ostrzeżeń dotyczących certyfikatów.

Serwer notesu Jupyter dostęp z dowolnego hosta. Po prostu wpisz *https://\<nazwę DNS maszyny Wirtualnej lub adres IP\>: 8000 /*

> [!NOTE]
> Port 8000 jest otwarty na zaporze domyślnie, gdy maszyna wirtualna jest aprowizowana. 

Firma Microsoft spakowaniu notesów przykładowe — jeden w języku Python i jeden w języku R. Można zobaczyć łącza do przykładów na stronie głównej Notes, po notesu programu Jupyter uwierzytelniania przy użyciu lokalnej nazwy użytkownika systemu Linux i hasło. Można utworzyć nowy notes, wybierając **New**, a następnie jądra odpowiedni język. Jeśli nie widzisz **New** przycisku, kliknij przycisk **Jupyter** ikony w lewym górnym rogu, aby przejść do strony głównej serwera notesu.

### <a name="apache-spark-standalone"></a>Autonomiczny platformy Apache Spark

Wystąpienia autonomicznego platformy Apache Spark jest preinstalowany na DSVM systemu Linux, które pomogą Ci tworzyć aplikacje Spark lokalnie najpierw przed testowanie i wdrażanie w dużych klastrach. Możesz uruchomić programy PySpark za pośrednictwem jądra programu Jupyter. Po otwarciu programu Jupyter, kliknij przycisk **New** przycisku i powinien zostać wyświetlony listę dostępnych jądra. "Platforma Spark — Python" jest jądra PySpark, która pozwala tworzyć aplikacje przy użyciu języka Python platformy Spark. Środowisko IDE języka Python, takich jak platformy PyCharm lub Spyder służy również do tworzenia programu na platformę Spark. W tym wystąpieniu autonomicznym stosu Spark jest uruchamiany w ramach program wywołujący klienta, co pozwala na szybsze i prostsze do rozwiązywania problemów w porównaniu do tworzenia w klastrze Spark.

Przykładowy notes PySpark znajduje się na Jupyter, który znajduje się w katalogu "SparkML" w głównym katalogu Jupyter ($ głównej/notesów/SparkML/pySpark). 

Jeśli programujesz w języku R dla platformy Spark, można użyć oprogramowania Microsoft R Server, SparkR lub sparklyr. 

Przed uruchomieniem w kontekście platformy Spark w programie Microsoft R Server, należy ją wykonać jeden raz, Instalator kroku umożliwiające pojedynczego węzła lokalnego wystąpienia HDFS, Hadoop i Yarn. Domyślnie usługi Hadoop jest zainstalowany, ale wyłączone na maszyny DSVM. Aby ją włączyć, należy uruchom następujące polecenia jako głównego po raz pierwszy:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Hadoop można zatrzymać związane z usługami, gdy nie są potrzebne, uruchamiając ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```

Przykład ukazujące sposób tworzenia i testowania PANI w zdalnym kontekstu aparatu Spark (czyli wystąpieniu platformy Spark autonomiczne maszyny DSVM) jest dostarczany i dostępne w */dsvm/samples/MRS* katalogu.

### <a name="ides-and-editors"></a>Środowiska IDE i edytorów

Masz do wyboru kilka edytorów kodów, m.in. vi/VIM Emacs, platformy PyCharm, RStudio i IntelliJ. IntelliJ, RStudio platformy PyCharm są edytory graficzne i musisz być zarejestrowany w programie desktop graficznego z nich korzystać. Te edytorów, aplikacji i pulpitu menu skrótów do ich uruchamiania.

**VIM** i **Emacs** są edytory oparte na tekście. Na Emacs możemy zainstalować pakietu dodatku o nazwie Emacs mówi statystyki (dostępu), która ułatwia pracę przy użyciu języka R w edytorze Emacs. Więcej informacji znajduje się w temacie [TĘPU](https://ess.r-project.org/).

**LaTex** instaluje się za pośrednictwem pakietu texlive wraz z dodatku Emacs [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) pakiet, który upraszcza tworzenie dokumentów w ramach Emacs LaTex.  

### <a name="databases"></a>Bazy danych

#### <a name="graphical-sql-client"></a>Graficzny Klient SQL

**SQuirrel SQL**, podano graficzny Klient SQL, połączyć się z różnych baz danych (takich jak Microsoft SQL Server i MySQL) i uruchamiać zapytania SQL. Możesz uruchomić SQuirrel SQL z sesji pulpitu graficznym (na przykład przy użyciu klienta X2Go) przy użyciu ikony pulpitu lub za pomocą następującego polecenia w powłoce:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Przed pierwszym użyciem należy skonfigurować sterowniki i aliasy bazy danych. Sterowników JDBC znajdują się na:

*/usr/share/Java/jdbcdrivers*

Aby uzyskać więcej informacji, zobacz [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Narzędzia wiersza polecenia do uzyskiwania dostępu do programu Microsoft SQL Server

Pakiet sterownika ODBC dla programu SQL Server dołączono również dwa narzędzia wiersza polecenia:

**Narzędzie BCP**: Zbiorcze narzędzie bcp kopiuje dane między wystąpienia programu Microsoft SQL Server i plik danych w formacie określonych przez użytkownika. Narzędzie bcp służy do importowania dużej liczby nowych wierszy do tabel programu SQL Server lub do wyeksportowania danych z tabel do plików danych. Aby zaimportować dane do tabeli, musisz użyć pliku formatu utworzonego dla tej tabeli lub poznać strukturę tabeli i typy danych, które są prawidłowe dla kolumn.

Aby uzyskać więcej informacji, zobacz [połączenie za pomocą narzędzia bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**sqlcmd**: Można użyć instrukcji języka Transact-SQL przy użyciu narzędzia sqlcmd, a także procedury systemu i pliki skryptów, w tym celu w wierszu polecenia. To narzędzie używa ODBC w celu wykonania partii języka Transact-SQL.

Aby uzyskać więcej informacji, zobacz [połączenie przy użyciu narzędzia sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

> [!NOTE]
> Istnieją pewne różnice w to narzędzie między platformami systemów Linux i Windows. Zobacz dokumentację, aby uzyskać szczegółowe informacje.

#### <a name="database-access-libraries"></a>Biblioteki dostępu do bazy danych

Brak dostępnych bibliotek języka R i Python do baz danych programu access.

* W języku R **RODBC** pakietu lub **dplyr** pakiet umożliwia zapytań lub wykonywania instrukcji SQL na serwerze bazy danych.
* W języku Python **moduł pyodbc** Biblioteka zapewnia dostęp do bazy danych ODBC jako odpowiedniej warstwy.  

### <a name="azure-tools"></a>Narzędzia platformy Azure

Następujące narzędzia platformy Azure są zainstalowane na maszynie Wirtualnej:

* **Interfejs wiersza polecenia platformy Azure**: Interfejs wiersza polecenia platformy Azure umożliwia tworzenie i zarządzanie zasobami platformy Azure przy użyciu powłoki poleceń. Aby wywołać narzędzia platformy Azure, po prostu wpisz **pomoc platformy azure**. Aby uzyskać więcej informacji, zobacz [stronę z dokumentacją dotyczącą interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Eksplorator usługi Microsoft Azure Storage**: Microsoft Azure Storage Explorer to graficzny narzędzie, które umożliwia przeglądanie obiektów, które są przechowywane na koncie magazynu platformy Azure i przekazywanie i pobieranie danych do i z obiektów blob platformy Azure. Można uzyskać dostęp do Eksploratora usługi Storage z ikony skrótu na pulpicie. Wywołania z poziomu wiersza powłoki, wpisując **StorageExplorer**. Zalogować się do komputera z klienta X2Go lub mieć X11 przekazywania zestawu w górę.
* **Biblioteki Azure**: Poniżej przedstawiono niektóre z wstępnie zainstalowanymi bibliotekami.
  
  * **Python**: Biblioteki dotyczących platformy Azure w języku Python, które są zainstalowane są **azure**, **usługi Azure ml**, **pydocumentdb**, i **moduł pyodbc**. Pierwsze trzy bibliotek umożliwia dostęp do usług Azure storage, Azure Machine Learning i Azure Cosmos DB (bazę danych NoSQL na platformie Azure). Czwarty biblioteka, moduł pyodbc (wraz z sterownik Microsoft ODBC dla programu SQL Server), umożliwia dostęp do programu SQL Server, usługi Azure SQL Database i Azure SQL Data Warehouse za pomocą języka Python przy użyciu interfejsu ODBC. Wprowadź **listy pip** aby zobaczyć wszystkie wymienione biblioteki. Pamiętaj uruchomić to polecenie zarówno język Python 2.7 i 3.5 środowiska.
  * **R**: Dotyczących platformy Azure biblioteki w języku R, które są zainstalowane są **usługi Azure ml** i **RODBC**.
  * **Java**: Na liście bibliotek Java usługi Azure można znaleźć w katalogu **/dsvm/sdk/AzureSDKJava** na maszynie Wirtualnej. Biblioteki klucza to Azure sterowniki magazynów i zarządzania interfejsami API usługi Azure Cosmos DB i JDBC dla programu SQL Server.  

Możesz uzyskać dostęp [witryny Azure portal](https://portal.azure.com) z wstępnie zainstalowanymi przeglądarki Firefox. W witrynie Azure portal można utworzyć, zarządzanie i monitorowanie zasobów platformy Azure.

### <a name="azure-machine-learning"></a>Azure Machine Learning

Usługa Azure Machine Learning to w pełni zarządzana usługa w chmurze umożliwiająca tworzenie, wdrażanie i udostępnianie rozwiązań do analizy predykcyjnej. Możesz tworzyć swoje eksperymentów i modeli z usługi Azure Machine Learning Studio. Jest dostępny w przeglądarce sieci web na maszynie wirtualnej do nauki o danych, odwiedzając [Microsoft Azure Machine Learning](https://studio.azureml.net).

Po zalogowaniu się do usługi Azure Machine Learning Studio masz dostęp do obszaru roboczego eksperymentowanie w usłudze którym utworzysz przepływ logiczny dla algorytmów uczenia maszynowego. Możesz również dostęp do notesu programu Jupyter, hostowanych w usłudze Azure Machine Learning i może bezproblemowo współpracować z eksperymentów w usłudze Machine Learning Studio. Operacjonalizowanie modeli, utworzonych przez opakowywanie je w interfejsie usługi sieci web uczenia maszynowego. Operacjonalizacji modeli uczenia maszynowego umożliwia klientom napisane w dowolnym języku wywołać za pomocą tych modeli. Aby uzyskać więcej informacji, zobacz [dokumentacja usługi Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Można również twórz modele w języku R lub Python na maszynie Wirtualnej, a następnie wdrożysz go w środowisku produkcyjnym w usłudze Azure Machine Learning. Firma Microsoft zainstalowano bibliotek w języku R (**usługi Azure ml**) i języka Python (**usługi Azure ml**) Aby włączyć tę funkcję.

Aby uzyskać informacje na temat sposobu wdrażania modeli języków R i Python w usłudze Azure Machine Learning, zobacz [dziesięć rzeczy, które można wykonać na maszynie wirtualnej analizy danych](vm-do-ten-things.md) (w szczególności i sekcji "tworzenie modeli przy użyciu języka R lub Python i Obsługuj ich operacje przy użyciu Usługa Azure Machine Learning").

> [!NOTE]
> Te instrukcje zostały napisane dla wersji Windows maszyna wirtualna do nauki o danych. Jednak informacje o wdrażaniu modeli usługi Azure Machine Learning ma zastosowanie do maszyny Wirtualnej systemu Linux.

### <a name="machine-learning-tools"></a>Machine learning narzędzia

Maszyna wirtualna ma kilka narzędzi i algorytmów, które zostały wstępnie skompilowany wstępnie zainstalowane lokalnie uczenia maszynowego. Należą do nich:

* **Vowpal Wabbit**: Algorytm uczenia szybko online.
* **xgboost**: Narzędzia, która zapewnia zoptymalizowane algorytmy wzmocnionego drzewa.
* **Rattle**: Na podstawie języka R graficzne narzędzie do łatwej eksploracji danych i modelowania.
* **Python**: Anaconda Python jest dostarczany w połączeniu z usługą algorytmów uczenia maszynowego przy użyciu bibliotek, takich jak Scikit-learn. Inne biblioteki można zainstalować za pomocą `pip install` polecenia.
* **LightGBM**: Gradientem, szybka i rozproszonej o wysokiej wydajności, ulepszanie architektury opartej na algorytmy drzewa decyzyjnego.
* **R**: Bogata biblioteka machine learning funkcji jest dostępna dla języka R. Niektóre z bibliotek, które są wstępnie zainstalowane są lm glm, randomForest, rpart. Inne biblioteki można zainstalować przez uruchomienie:
  
        install.packages(<lib name>)

Poniżej przedstawiono dodatkowe informacje o narzędziach pierwsze trzy usługi machine learning na liście.

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit jest system, który korzysta z technik, takich jak online, wyznaczania wartości skrótu, allreduce, redukcji, learning2search, są aktywne, uczenia maszynowego i szkolenia interaktywne.

Aby uruchomić narzędzie, na przykład podstawowy, użyj następujących poleceń:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Istnieją inne, większy pokazy, w tym katalogu. Aby uzyskać więcej informacji na temat VW, zobacz [tej sekcji GitHub](https://github.com/JohnLangford/vowpal_wabbit)i [wiki Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost

Jest to biblioteka, który został zaprojektowany i zoptymalizowany pod kątem algorytmy wzmocnione (drzewo). Celem tej biblioteki jest wypychania limity obliczeń maszyn do skrajnymi poziomami niezbędnej do zapewnienia drzewa na dużą skalę, zwiększania wyniku, który jest skalowalny, przenośne i dokładne.

Jest ona udostępniana jako wiersz polecenia, a także do biblioteki języka R.

Aby użyć tej biblioteki w języku R, można uruchomić interaktywnych sesji języka R (po prostu wpisując **R** w powłoce) i załadować biblioteki.

Poniżej przedstawiono prosty przykład, które można uruchomić w wierszu polecenia języka R:

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

Aby uruchomić wiersz polecenia xgboost, poniżej przedstawiono polecenia do wykonania w powłoce:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

.Model — plik jest zapisywany do podanego katalogu. Można znaleźć informacje o tym przykładzie pokaz [w serwisie GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Aby uzyskać więcej informacji na temat xgboost zobacz [stronę z dokumentacją dotyczącą xgboost](https://xgboost.readthedocs.org/en/latest/), a jego [repozytorium GitHub](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Rattle

Rattle ( **R** **A**nalityczny **T**pismo odręczne — narzędzie **T**o **L**zdobyć **E** asily) używa Eksplorowanie i modelowanie danych z Graficznym interfejsem użytkownika. Przedstawia informacje o statystycznych i wizualne podsumowania danych, dane przekształceń, które mogą być łatwo modelowane, kompilacje nadzorowanych i nienadzorowanych modeli z danych, przedstawia w formie graficznej, informacje o wydajności modeli i zestawy danych nowej oceny. Generuje kod R, replikowanie operacji w interfejsie użytkownika, które można uruchomić bezpośrednio w języku R lub wykorzystywane jako punktu wyjścia do dalszej analizy.

Aby uruchomić Rattle, musisz być w graficznym logowania w sesji pulpitu. Na terminalu wpisz ```R``` wprowadzenia środowisko R. W wierszu polecenia języka R, wpisz następujące polecenia:

```R
library(rattle)
rattle()
```

Teraz interfejs graficzny otwiera z zestawu kart. Poniżej przedstawiono kroki przewodnika Szybki Start w Rattle potrzebne do użycia Przykładowy zestaw danych o pogodzie i utworzyć model. W niektórych czynności monit automatycznie zainstaluje i załaduje niektóre wymagane pakiety języka R, które nie są już w systemie.

> [!NOTE]
> Jeśli nie masz dostępu do zainstalowania pakietu w katalogu systemowym (ustawienie domyślne), może być wyświetlony monit na okna konsoli języka R, aby zainstalować pakiety do osobistą bibliotekę dokumentacji. Odpowiedź *y* Jeśli widzisz te monity.

1. Kliknij przycisk **Execute** (Wykonaj).
1. Pojawi się okno dialogowe, pytaniem, jeśli chcesz użyć Przykładowy zestaw danych o pogodzie. Kliknij przycisk **tak** załadować w przykładzie.
1. Kliknij przycisk **modelu** kartę.
1. Kliknij przycisk **Execute** do tworzenia drzewa decyzyjnego.
1. Kliknij przycisk **Rysowanie** do wyświetlenia drzewa decyzyjnego.
1. Kliknij przycisk **lasu** przycisk radiowy, a następnie kliknij przycisk **Execute** tworzenie losowego lasu.
1. Kliknij przycisk **Evaluate** kartę.
1. Kliknij przycisk **ryzyka** przycisk radiowy, a następnie kliknij przycisk **Execute** do wyświetlenia dwa wykresy wydajności o podwyższonym ryzyku (skumulowany).
1. Kliknij przycisk **dziennika** kartę, aby wyświetlić kod Generuj R poprzedniej operacji.
   (Ze względu na usterkę w bieżącej wersji Rattle należy wstawić *#* znak przed *eksportowanie tego dziennika...*  w tekście dziennika.)
1. Kliknij przycisk **wyeksportować** przycisk, aby zapisać plik skryptu języka R, o nazwie *weather_script. R* do folderu głównego.

Możesz wyjść Rattle i języka R. Teraz można zmodyfikować wygenerowany skrypt języka R lub używany tak, jak można uruchamiać go w dowolnym momencie wszystko, co zostało zrobione w Interfejsie użytkownika Rattle powtórzeń. Szczególnie dla początkujących w języku R jest to prosty sposób szybko wykonywać analizę danych i uczenia maszynowego w prosty interfejs graficzny podczas automatycznego generowania kodu w języku R, modyfikować i/lub Dowiedz się więcej.

## <a name="next-steps"></a>Kolejne kroki

Poniżej przedstawiono, jak można kontynuować Twoją naukę oraz eksploracji:

* [Do nauki o danych na maszyny wirtualnej analizy danych dla systemu Linux](linux-dsvm-walkthrough.md) instruktażu przedstawiono sposób wykonywania kilku typowych zadań do nauki o danych z systemem Linux maszyna wirtualna do nauki o danych aprowizowane w tym miejscu. 
* Eksplorowanie różnych narzędzi do analizy danych na maszyny Wirtualnej do analizy danych, korzystając z narzędzi opisanych w tym artykule. Można również uruchomić *dsvm więcej informacji o* Shell na maszynie wirtualnej wstęp i łącza do dodatkowych informacji na temat narzędzi zainstalowanych na maszynie Wirtualnej.  
* Dowiedz się, jak tworzyć rozwiązania analityczne end-to-end systematycznie przy użyciu [zespołu danych dla celów naukowych](https://aka.ms/tdsp).
* Odwiedź stronę [galerii Azure AI](https://gallery.azure.ai/) maszyny nauki i dane analizy przykładów, które korzystają z usług Azure AI.
