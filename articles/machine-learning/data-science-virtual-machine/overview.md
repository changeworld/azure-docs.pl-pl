---
title: Wprowadzenie do maszyny wirtualnej nauki danych platformy Azure dla systemów Linux i Windows | Dokumentacja firmy Microsoft
description: Scenariusze analizy klucza i składników systemu Windows i Linux danych nauki maszyny wirtualne.
keywords: narzędzia do analizy danych, maszyny wirtualnej analizy danych, narzędzia do analizy danych, nauki danych systemu linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: gokuma
ms.openlocfilehash: f62f6c4b2679457e8aaddb81e8a37622ca878ffc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="introduction-to-azure-data-science-virtual-machine-for-linux-and-windows"></a>Wprowadzenie do maszyny wirtualnej nauki danych platformy Azure dla systemów Linux i Windows

Maszyna wirtualna nauki danych (DSVM) jest dostosowany obraz maszyny Wirtualnej w chmurze Azure firmy Microsoft, opracowane pod kątem wykonywania analizy danych. Zawiera ona wiele wstępnie zainstalowanych i skonfigurowanych, popularnych narzędzi do analizy danych, które pozwalają szybko rozpocząć tworzenie inteligentnych, zaawansowanych aplikacji analitycznych. Jest dostępna w systemach Windows Server i Linux. Oferujemy wersje maszyny wirtualnej dla systemów Windows Server 2016 i Windows Server 2012. Firma Microsoft oferuje systemu Linux DSVM Ubuntu 16.04 LTS i CentOS 7.4.

W tym temacie opisano, co można zrobić z maszyną Wirtualną analizy danych, opisano niektóre z kluczowych scenariuszy dla przy użyciu maszyny Wirtualnej itemizes najważniejsze funkcje dostępne w wersjach systemu Windows i Linux i instrukcje dotyczące sposobu rozpocząć korzystanie z nich.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>Co można zrobić z maszyny wirtualnej nauki danych?
Celem maszyny wirtualnej nauki danych jest zapewnienie specjalistów danych na wszystkich poziomach umiejętności oraz role ze środowiskiem nauki tarcia dane. Ta maszyna wirtualna jest zapisywany wiele czasu, który poświęcić Jeśli samodzielnie miał wdrażana środowisku można porównywać pod względem. Zamiast tego uruchomić projektu nauki danych bezpośrednio w nowo utworzone wystąpienie maszyny Wirtualnej. 

Maszyna wirtualna nauki danych został zaprojektowany i skonfigurowane do pracy z szeroką gamę scenariusze użycia. Środowiska można skalować w górę lub w dół zgodnie z projektu zmieniających się potrzeb. Mogą używać preferowany język do zadania analizy danych program. Można zainstalować inne narzędzia i Dostosuj system na potrzeby użytkowników.

## <a name="key-scenarios"></a>Najważniejsze scenariusze
W tej części podano niektóre najważniejsze scenariusze, dla których można wdrożyć maszyny Wirtualnej analizy danych.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Pulpit analytics wstępnie skonfigurowane w chmurze
Maszyna wirtualna nauki danych udostępnia konfiguracji bazowej dla zespołów nauki danych chcesz wymienić lokalnych komputerów stacjonarnych z pulpitem zarządzanej chmury. Ta linia bazowa zapewnia analityków danych w zespole spójne ustawienia umożliwiające Sprawdź eksperymentów, a następnie Podwyższ współpracy. Zmniejszenie obciążenia sysadmin i zapisując na czas potrzebny na ocenę, zainstalowanie i obsługa różnych pakietów oprogramowania wymagane do wykonania zaawansowana analityka są również obniża koszty.  

### <a name="data-science-training-and-education"></a>Szkolenia i edukacja w zakresie analizy danych
Instruktorów przedsiębiorstwa i instytucjom klasy zazwyczaj zawierają obraz maszyny wirtualnej, aby zapewnić spójne ustawienia ich studentów i przewidywalnego pracy przykłady który uczy analizy danych. Maszyna wirtualna nauki danych tworzy środowisko na żądanie spójne Instalatora, która ułatwia wyzwania niezgodności i pomocy technicznej. Przypadki, w której tych środowisk muszą zostać skompilowane często, zwłaszcza w przypadku krótszą klasy szkolenia, korzyści znacznie.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Elastyczne pojemności na żądanie dla dużych projektów
Dane nauki hackathons/konkursami lub modelowania danych na dużą skalę i eksploracja wymagają skalowana w poziomie wydajności sprzętu i zwykle przez krótki czas trwania. Maszyna wirtualna nauki danych może pomóc w replikowanie danych środowiska nauki szybko na żądanie, na serwerach skalowanej, umożliwiających eksperymenty wymagające silnych zasobów obliczeniowych do uruchomienia.

### <a name="short-term-experimentation-and-evaluation"></a>Eksperymenty krótkoterminowej i ocena
Maszyna wirtualna nauki danych może być używane do oceny lub Dowiedz się, narzędzi, takich jak Microsoft ML Server, SQL Server, Visual Studio tools, Jupyter głębokość uczenia / ML zestaw narzędzi i nowe narzędzia popularnych w społeczności z minimalnym Instalatora nakładu pracy. Ponieważ maszyna wirtualna nauki danych można skonfigurować szybko, mogą być stosowane w innych krótkoterminowej scenariusze użycia, takie jak replikowanie opublikowanych eksperymentów, pokazy, następujące wskazówki w sesji online lub samouczki konferencji wykonywania.

### <a name="deep-learning"></a>Głębokie learning
Nauki danych maszyny Wirtualnej może służyć do szkolenia modelu przy użyciu algorytmów uczenia głębokie na procesora GPU (jednostki przetwarzania grafiki) oparte na sprzęcie. Przy użyciu maszyn wirtualnych, skalowanie możliwości chmury Azure, DSVM pomaga oparte na procesorze Graficznym sprzęt jest używany w chmurze, zgodnie z harmonogramem potrzeby. Jeden przełączyć się do maszyny Wirtualnej na podstawie procesora GPU podczas dużych modeli uczenia lub wymagają obliczenia szybkich przy zachowaniu tego samego dysku systemu operacyjnego.  Wersji systemu Windows Server 2016 DSVM jest wstępnie zainstalowane z wersji sterowników procesora GPU, struktur i wersje GPU głębokie uczenia struktury. W systemie Linux bezpośrednich learning na procesor GPU jest włączona na Ubuntu DSVMs i CentOS. Ubuntu, CentOS lub Windows 2016 wersji wirtualna nauki danych można wdrożyć do maszyny wirtualnej platformy Azure z systemem innym niż oparte na procesorze Graficznym w takim przypadku wszystkich platform głębokie learning powróci do trybu procesora CPU. 

## <a name="whats-included-in-the-data-science-vm"></a>Zawartość w maszynie Wirtualnej nauki danych?
Maszyna wirtualna nauki danych ma wiele popularnych danych nauki i bezpośrednie narzędzie już zainstalowany i skonfigurowany. Zawiera również narzędzia, które ułatwiają pracę z różnych Azure produktów danych i ich analiza. Można eksplorować i tworzenie modeli predykcyjnych w dużych zestawów danych przy użyciu Server ML firmy Microsoft (R, Python) lub 2017 serwera SQL. Host innych narzędzi typu open source społeczność i firmy Microsoft są uwzględnione, a także przykładowy kod i notesów. W poniższej tabeli itemizes i porównuje główne składniki dostępna w wersjach systemu Windows i Linux maszyny wirtualnej analizy danych.


| **Narzędzie**                                                           | **Wersja systemu Windows** | **Wersja systemu Linux** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Otwórz R Microsoft](https://mran.microsoft.com/open/) z pakietami popularnych preinstalowanym   |Tak                      | Tak             |
| [Server ML firmy Microsoft (R, Python)](https://docs.microsoft.com/machine-learning-server/) zawiera Developer Edition, <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) równoległe i rozproszone framework wysokiej wydajności (R i Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) -nowych algorytmów uczenia Maszynowego stanu grafiki firmy Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R i Python Operationalization](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |Tak                      | Tak |
| [Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) wykraczającego Plus z udostępnionego aktywacji - programu Excel, Word i PowerPoint   |Tak                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2.7, 3.5 z pakietami popularnych preinstalowanym    |Tak                      |Tak              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) z popularnych pakiety języka Julia preinstalowanym                         |Tak                      |Tak              |
| Relacyjne bazy danych                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/)(tylko CentOS) |
| Narzędzia bazy danych                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * Sterowniki ODBC/JDBC| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (podczas badania narzędzie) <br /> * bcp, sqlcmd <br /> * Sterowniki ODBC/JDBC|
| Skalowalna analityka w bazie danych z usługami SQL Server ML (R, Python) | Tak     |N              |
| **[Serwer notesu Jupyter](http://jupyter.org/) z następujących jądra**                                  | Tak     | Tak |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | Tak | Tak |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python 2.7 & 3.5 | Tak | Tak |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | Tak | Tak |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | Tak | Tak |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | T (tylko Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | Tak |
| JupyterHub (serwer notesów przez wielu użytkowników)| N | Tak |
| **Narzędzia deweloperskie, edytory IDEs i kod**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) > Wtyczki Git, Azure HDInsight (Hadoop), usługi Data Lake tools danych programu SQL Server [Node.js](https://github.com/Microsoft/nodejstools), [Python](http://aka.ms/ptvs), i [R Tools dla Visual Studio (RTVS)](http://microsoft.github.io/RTVS-docs/) | Tak | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | Tak | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Programu RStudio pulpitu](https://www.rstudio.com/products/rstudio/#Desktop) | Tak | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm](https://www.jetbrains.com/pycharm/) | N | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (Julia IDE)](http://junolab.org/)| Tak | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim i emacs: | Tak | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git i GitBash | Tak | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | Tak | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* .Net Framework | Tak | N |
| Usługa Power BI Desktop | Tak | N |
| Zestaw SDK dostępu do platformy Azure i pakietu Cortana Intelligence Suite usług | Tak | Tak |
| **Przenoszenie danych i narzędzia do zarządzania** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Eksplorator usługi azure Storage | Tak | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure) | Tak | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | Tak | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Tak | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Sterownik BEZPIECZNIK obiektów blob](https://github.com/Azure/azure-storage-fuse) | N | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy(Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Tak | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Narzędzie migracji danych o nazwie DocDB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Tak | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Brama zarządzania danymi firmy Microsoft](https://msdn.microsoft.com/library/dn879362.aspx): przenoszenie danych między lokalnego programu i w chmurze | Tak | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Narzędzia wiersza polecenia systemu Unix/Linux | Tak | Tak |
| [Przechodzenie do szczegółów Apache](http://drill.apache.org) dla Eksploracja danych | Tak | Tak |
| **Narzędzia uczenia maszynowego** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integracji z [uczenie maszynowe Azure](https://azure.microsoft.com/services/machine-learning/) (R, Python) | Tak | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | Tak | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Tak | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](http://www.cs.waikato.ac.nz/ml/weka/) | Tak | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](http://rattle.togaware.com/) | Tak | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | T (tylko Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/) | N | T (tylko Ubuntu) |
| **Oparte na procesorze Graficznym bezpośrednich narzędzie** |Wersja systemu Windows Server 2016  | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Kognitywnych zestaw narzędzi firmy Microsoft (znanego wcześniej jako CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) | Tak | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | Tak | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](http://mxnet.io/) | Tak | Tak|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe & Caffe2](https://github.com/caffe2/caffe2) | N | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Latarka](http://torch.ch/) | N | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](http://pytorch.org/)| N | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia cyfr](https://github.com/NVIDIA/DIGITS) | N | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Serwer modelu MXNet](https://github.com/awslabs/mxnet-model-server) | N | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Obsługa TensorFlow](https://www.tensorflow.org/serving/) | N | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA, CUDNN, Nvidia Driver](https://developer.nvidia.com/cuda-toolkit) | Tak | Tak |
| **Platformy danych big Data (tylko Devtest)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* Lokalnego [Spark](http://spark.apache.org/) autonomiczny | N | Tak |
| &nbsp;&nbsp;&nbsp;&nbsp;* Lokalnego [Hadoop](http://hadoop.apache.org/) (HDFS, YARN) | N | Tak |



## <a name="get-started-with-the-windows-data-science-vm"></a>Rozpoczynanie pracy z maszyny Wirtualnej nauki danych systemu Windows
* Utwórz wystąpienie żądanej wersji systemu Windows DSVM, przechodząc do
  * [Windows Server 2016 na podstawie DSVM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.windows-data-science-vm)
  
  lub 
  * [Windows Server 2012 na podstawie DSVM](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) 
* Kliknij przycisk **UZYSKAĆ IT** przycisku.
* Zaloguj się do maszyny Wirtualnej z pulpitu zdalnego przy użyciu poświadczeń określonych podczas tworzenia maszyny Wirtualnej.
* Aby wykryć i uruchom dostępne narzędzia, kliknij przycisk **Start** menu.

## <a name="get-started-with-the-linux-data-science-vm"></a>Rozpoczynanie pracy z maszyny Wirtualnej systemu Linux danych nauki
* Utwórz wystąpienie żądanej wersji systemu Linux DSVM, przechodząc do 
  * [Ubuntu na podstawie DSVM](http://aka.ms/dsvm/ubuntu)

  lub

  * [CentOS na podstawie DSVM](http://aka.ms/dsvm/centos)

  
* Kliknij przycisk **Pobierz teraz** przycisku.
* Zaloguj się do maszyny Wirtualnej z klienta SSH, takiego jak Putty lub polecenia SSH, używając poświadczeń określonych, podczas tworzenia maszyny Wirtualnej.
* W wierszu polecenia powłoki wprowadź informacje o innych dsvm.
* Graficzny komputerów stacjonarnych, Pobierz klienta X2Go dla danej platformy klienta [tutaj](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) i postępuj zgodnie z instrukcjami w dokumencie maszyny Wirtualnej systemu Linux danych nauki [Aprowizowanie maszyny wirtualnej systemu Linux danych nauki](linux-dsvm-intro.md#installing-and-configuring-x2go-client).

## <a name="next-steps"></a>Kolejne kroki
### <a name="for-the-windows-data-science-vm"></a>Dla systemu Windows nauki danych maszyny Wirtualnej
* Aby uzyskać więcej informacji na temat sposobu uruchamianie określonych narzędzi dostępnych w wersji systemu Windows, zobacz [Aprowizowanie maszyny wirtualnej programu Microsoft Data nauki](provision-vm.md) i
* Aby uzyskać więcej informacji na temat sposobu wykonywania różnych zadań wymagane dla projektu nauki danych na Maszynie wirtualnej systemu Windows, zobacz [10 sposobów na nauki danych maszyny wirtualnej](vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>Dla danych Linux nauki maszyny Wirtualnej
* Aby uzyskać więcej informacji na temat sposobu uruchamianie określonych narzędzi dostępnych w wersji systemu Linux, zobacz [Aprowizowanie maszyny wirtualnej systemu Linux danych nauki](linux-dsvm-intro.md).
* Aby uzyskać wskazówki, które pokazuje, jak wykonać kilka typowych zadań nauki danych z maszyny Wirtualnej systemu Linux, zobacz [nauki danych na dane nauki maszyny wirtualnej systemu Linux](linux-dsvm-walkthrough.md).

