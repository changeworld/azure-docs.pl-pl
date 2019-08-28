---
title: Wprowadzenie do usługi Azure Data Science Virtual Machine dla systemów Linux i Windows | Microsoft Docs
description: Kluczowe składniki i scenariusze analizy dla maszyn wirtualnych do nauki o danych z systemami Windows i Linux.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/22/2019
ms.author: vijetaj
ms.openlocfilehash: 22192d67a946f9f466848b65b9cd763b16e6c5f0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099384"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Co to jest Data Science Virtual Machine platformy Azure dla systemów Linux i Windows?

Data Science Virtual Machine (DSVM) to dostosowany obraz maszyny wirtualnej na platformie Azure Cloud Platform zbudowany specjalnie na potrzeby analizy danych. Zawiera ona wiele wstępnie zainstalowanych i skonfigurowanych, popularnych narzędzi do analizy danych, które pozwalają szybko rozpocząć tworzenie inteligentnych, zaawansowanych aplikacji analitycznych. 

Konfiguracje narzędzi są rygorystycznie testowane przez analityków danych i deweloperów w firmie Microsoft oraz szerszej społeczności naukowej o danych. Ten test zapewnia stabilność i ogólną żywotność.

DSVM jest dostępny w:
+ Windows Server 2016, Windows Server 2012
+ Ubuntu 16,04 LTS i CentOS 7,4

> [!NOTE]
> Wszystkie narzędzia maszyny wirtualnej do uczenia głębokiego zostały złożone w Data Science Virtual Machine. 


## <a name="what-can-i-do-with-the-dsvm"></a>Co mogę zrobić z DSVM?
Celem Data Science Virtual Machine jest zapewnienie pracownikom danych wszystkich poziomów umiejętności i między branżami z bezpłatnym, wstępnie skonfigurowanym i w pełni zintegrowanym środowiskiem nauki o danych. Zamiast samodzielnie wdrażać porównywalny obszar roboczy, możesz udostępnić DSVM. Ten wybór może zaoszczędzić dni lub nawet _tygodnie_ w procesach instalacji, konfiguracji i zarządzania pakietami. Po przydzieleniu maszyny wirtualnej do nauki o danych można natychmiast rozpocząć pracę nad projektem nauki o danych.

DSVM jest zaprojektowana i skonfigurowana do pracy z szeroką gamę scenariuszy użycia. Środowisko można skalować w górę lub w dół w miarę zmiany wymagań projektu. Możesz również użyć preferowanego języka do programowania zadań analizy danych i instalowania innych narzędzi, aby dostosować system do własnych potrzeb.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Wstępnie skonfigurowany pulpit analityczny w chmurze
DSVM zawiera konfigurację linii bazowej dla zespołów do nauki o danych, które chcą zamienić swoje pulpity lokalne na zarządzany pulpit w chmurze. Ten punkt odniesienia pozwala zagwarantować, że wszyscy naukowcy zajmujący się danymi w zespole mają spójną konfigurację umożliwiającą weryfikowanie eksperymentów i promowanie współpracy. Obniża ona również koszty, zmniejszając obciążenie administratora systemu. Zmniejszenie obciążenia pozwala zaoszczędzić czas wymagany do oszacowania, zainstalowania i konserwacji pakietów oprogramowania na potrzeby zaawansowanej analizy.

### <a name="data-science-training-and-education"></a>Szkolenia i edukacja w zakresie nauki o danych
Instruktorzy i nauczyciele, którzy uczą klasy nauki o danych, zwykle udostępniają obraz maszyny wirtualnej. Obraz ten gwarantuje, że uczniowie mają spójną konfigurację oraz że próbki przewidywalnie działają. 

DSVM tworzy środowisko na żądanie z spójną konfiguracją, która ułatwia problemy z obsługą i niezgodnością. To rozwiązanie oferuje znaczące korzyści w sytuacjach, w których jest konieczne częste tworzenie takich środowisk (zwłaszcza w przypadku krótszych szkoleń).

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Elastyczna wydajność na żądanie dla projektów w dużej skali
Imprezy rozpoczynająa i konkursy danych na dużą skalę, dzięki czemu można przeskalować wydajność sprzętu, zazwyczaj przez krótki czas trwania. DSVM może ułatwić replikację środowiska nauki o danych na żądanie, na skalowalnych w poziomie serwerach, które umożliwiają eksperymenty, w których można uruchamiać zasoby obliczeniowe o wysokiej jakości.

### <a name="custom-compute-power-for-azure-notebooks"></a>Niestandardowa moc obliczeniowa dla Azure Notebooks
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) to bezpłatna usługa hostowana, która umożliwia tworzenie, uruchamianie i udostępnianie notesów Jupyter w chmurze bez konieczności instalacji. Bezpłatna warstwa usługi jest ograniczona do 4 GB pamięci i 1 GB danych. 

Aby zwolnić wszystkie limity, można dołączyć projekt notesów do DSVM lub innej maszyny wirtualnej działającej na serwerze Jupyter. Jeśli zalogujesz się do Azure Notebooks przy użyciu konta za pomocą Azure Active Directory (na przykład konta firmowego), notesy automatycznie pokazują DSVMs w każdej subskrypcji skojarzonej z tym kontem. Możesz [dołączyć DSVM do Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) , aby rozwinąć dostępną moc obliczeniową.

### <a name="short-term-experimentation-and-evaluation"></a>Krótkoterminowe eksperymenty i ocena
Możesz użyć DSVM, aby oszacować lub poznać podobne do nich narzędzia, przy minimalnym wykorzystaniu konfiguracji:

- Serwer Microsoft Machine Learning
- SQL Server
- Narzędzia programu Visual Studio
- Jupyter
- Narzędzia uczenia głębokiego i uczenia maszynowego
- Nowe narzędzia popularne w społeczności 

Ponieważ można szybko skonfigurować DSVM, można zastosować go w innych krótkoterminowych scenariuszach użytkowania. Te scenariusze obejmują replikację opublikowanych eksperymentów, wykonywanie pokazów oraz następujące przewodniki dotyczące sesji online i samouczków konferencyjnych.

### <a name="deep-learning"></a>Uczenie głębokie
W DSVM modele szkoleniowe mogą korzystać z algorytmów uczenia głębokiego na sprzęcie opartym na procesorach GPU. Korzystając z możliwości skalowania maszyn wirtualnych platformy Azure, DSVM ułatwia korzystanie z sprzętu opartego na procesorze GPU w chmurze zgodnie z potrzebami. Możesz przełączyć się na maszynę wirtualną opartą na procesorze GPU podczas uczenia dużych modeli lub gdy potrzebujesz obliczeń o dużej szybkości przy zachowaniu tego samego dysku systemu operacyjnego.  

Wydanie systemu Windows Server 2016 DSVM jest wstępnie zainstalowane ze sterownikami procesora GPU, platformami i wersjami procesora GPU dla platform edukacyjnych. W wersji systemu Linux uczenie głębokie na procesorach GPU jest włączone zarówno w CentOS, jak i Ubuntu DSVMs. 

Możesz również wdrożyć Ubuntu, CentOS lub Windows 2016 wydania DSVM na maszynie wirtualnej platformy Azure, która nie jest oparta na procesorach GPU. W takim przypadku wszystkie platformy uczenia głębokiego zostaną przywrócone do trybu procesora CPU.
 
[Dowiedz się więcej o dostępnych strukturach głębokiego uczenia i AI](dsvm-deep-learning-ai-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Co zawiera DSVM?
Maszyna wirtualna do nauki o danych obejmuje wiele popularnych narzędzi do nauki o danych i uczenia głębokiego, które są już zainstalowane i skonfigurowane. Ponadto zawiera ona narzędzia, które ułatwiają pracę z różnymi produktami platformy Azure do obsługi danych i analizy. Te produkty obejmują Microsoft Machine Learning Server (R, Python) do kompilowania modeli predykcyjnych oraz SQL Server 2017 na potrzeby eksploracji zestawów danych na dużą skalę. DSVM obejmuje inne narzędzia z społeczności "open source" i firmy Microsoft wraz z przykładowym [kodem i notesem](dsvm-samples-and-walkthroughs.md). 

Oto lista narzędzi i platform:
+ [Obsługiwane języki programowania](dsvm-languages.md)

+ [Obsługiwane platformy danych](dsvm-data-platforms.md)

+ [Narzędzia programistyczne i środowisk IDE](dsvm-tools-development.md)

+ [Uczenie głębokie i platformy AI](dsvm-deep-learning-ai-frameworks.md)

+ [Uczenie maszynowe i narzędzia do nauki o danych](dsvm-ml-data-science-tools.md)

+ [Narzędzia do pozyskiwania danych](dsvm-tools-ingestion.md)

+ [Narzędzia do eksploracji i wizualizacji danych](dsvm-tools-explore-and-visualize.md)

W poniższej tabeli wyszczególniono i porównano główne składniki maszyny wirtualnej do nauki o danych w wersji dla systemów Windows i Linux.

| **Narzędzie**                                                           | **Wersja systemu Windows** | **Wersja systemu Linux** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) z preinstalowanymi popularnymi pakietami   |T                      | T             |
| [Microsoft Machine Learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Wersja Developer zawiera: <br />  &nbsp;&nbsp;&nbsp;&nbsp;[Kolekcję funkcji revoscaler/biblioteki revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) Parallel i Distributed High-Performance Platform (R i Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;[MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), nowe najnowocześniejsze algorytmy uczenia maszynowego firmy Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp;[R i Python operacjonalizacji](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |T                      | T |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus z aktywację udostępnioną: Excel, Word i PowerPoint   |T                      |Nie              |
| [Anaconda Python](https://www.continuum.io/) 2,7 i 3,5 z preinstalowanymi popularnymi pakietami    |T                      |T              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) z preinstalowanymi popularnymi pakietami języka Julia                         |T                      |T              |
| Relacyjne bazy danych                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Narzędzia bazy danych                                                       |  SQL Server Management Studio <br/> Usługi integracji programu SQL Server<br/> [BCP, SQLCMD](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  Sterowniki ODBC/JDBC|  [Squirrel SQL](http://squirrel-sql.sourceforge.net/) (narzędzie do wykonywania zapytań), <br />  BCP, SQLCMD <br />  Sterowniki ODBC/JDBC|
| Skalowalna analiza w bazie danych za pomocą usługi SQL Server Machine Learning (R, Python) | T     |Nie              |
| [Serwer Jupyter Notebook](https://jupyter.org/) z następującymi jądrami:                                  | T     | T |
|     &nbsp;&nbsp;&nbsp;&nbsp;® | T | T |
|     &nbsp;&nbsp;&nbsp;&nbsp;Python | T | T |
|     &nbsp;&nbsp;&nbsp;&nbsp;Julia | T | T |
|     &nbsp;&nbsp;&nbsp;&nbsp;PySpark | T | T |
|     &nbsp;&nbsp;&nbsp;&nbsp;[Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | Nie | Tak (tylko Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;SparkR     | Nie | T |
| JupyterHub (serwer notesu wieloużytkownikom)| Nie | T |
| JupyterLab (serwer notesu wieloużytkownikom) | Nie | Tak (tylko Ubuntu) |
| Narzędzia programistyczne, środowisk IDE i edytory kodu:| | |
| &nbsp;&nbsp;&nbsp;&nbsp;[Visual Studio 2019 (wersja Community)](https://www.visualstudio.com/community/) z dodatkiem git, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server narzędzia danych, [Node. js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs)i [R Tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) | T | Nie |
| &nbsp;&nbsp;&nbsp;&nbsp;[Visual Studio Code](https://code.visualstudio.com/) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[RStudio](https://www.rstudio.com/products/rstudio/#Desktop) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[Serwer RStudio](https://www.rstudio.com/products/rstudio/#Server) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[Platformy PyCharm itd Community Edition](https://www.jetbrains.com/pycharm/) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[Atom](https://atom.io/) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[Juno (Julia IDE)](https://junolab.org/)| T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;Vim i Emacs: | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;Git i git bash | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;OpenJDK | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;.NET Framework | T | Nie |
| Power BI Desktop | T | Nie |
| Zestawy SDK umożliwiające dostęp do platformy Azure i pakietu usług Cortana Intelligence Suite | T | T |
| Narzędzia do przenoszenia i zarządzania danymi: | | |
| &nbsp;&nbsp;&nbsp;&nbsp;Eksplorator usługi Azure Storage | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;Azure PowerShell | T | Nie |
| &nbsp;&nbsp;&nbsp;&nbsp;[AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | T | Nie |
| &nbsp;&nbsp;&nbsp;&nbsp;[Sterownik bezpiecznika obiektu BLOB](https://github.com/Azure/azure-storage-fuse) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | T | Nie |
| &nbsp;&nbsp;&nbsp;&nbsp;[Narzędzie do migracji danych Azure Cosmos DB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | T | Nie |
| &nbsp;&nbsp;&nbsp;&nbsp;[Microsoft zarządzanie danymi Gateway](https://msdn.microsoft.com/library/dn879362.aspx): przenoszenie danych między środowiskiem lokalnym a chmurą | T | Nie |
| &nbsp;&nbsp;&nbsp;&nbsp;Narzędzia wiersza polecenia systemu UNIX/Linux | T | T |
| [Przechodzenie do szczegółów platformy Apache](https://drill.apache.org) na potrzeby eksploracji danych | T | T |
| Narzędzia uczenia maszynowego: |||
| &nbsp;&nbsp;&nbsp;&nbsp;Integracja z [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[XGBoost](https://github.com/dmlc/xgboost) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[Weka](https://www.cs.waikato.ac.nz/ml/weka/) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[Rattle](https://togaware.com/rattle/) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[LightGBM](https://github.com/Microsoft/LightGBM) | Nie | Tak (tylko Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;[CatBoost](https://tech.yandex.com/catboost/) | Nie | Tak (tylko Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;[H2O](https://www.h2o.ai/h2o/), [wody musujące](https://www.h2o.ai/sparkling-water/) | Nie | Tak (tylko Ubuntu) |
| Narzędzia uczenia głębokiego, które działają na procesorze GPU lub procesorze CPU: |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[TensorFlow](https://www.tensorflow.org/) | Tak (Windows 2016) | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[Horovod](https://github.com/uber/horovod) | Nie | Tak (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;[MXNet](https://mxnet.io/) | Tak (Windows 2016) | T|
| &nbsp;&nbsp;&nbsp;&nbsp;[Caffe i Caffe2](https://github.com/caffe2/caffe2) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;Moduł [łańcucha](https://chainer.org/) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[Torch](http://torch.ch/) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[Theano](https://github.com/Theano/Theano) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[Keras](https://keras.io/)| Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[PyTorch](https://pytorch.org/)| Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[Cyfry NVIDIA](https://github.com/NVIDIA/DIGITS) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[Serwer modelu MXNet](https://github.com/awslabs/mxnet-model-server) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[TensorFlow obsługujące](https://www.tensorflow.org/serving/) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[TensorRT](https://developer.nvidia.com/tensorrt) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;[Cuda, cuDNN, sterownik NVIDIA](https://developer.nvidia.com/cuda-toolkit) | T | T |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej z następujących artykułów:

+ W systemie Windows:
  + [Konfigurowanie maszyny DSVM z systemem Windows](provision-vm.md)
  + [Dziesięć rzeczy, które można wykonać na DSVM systemu Windows](vm-do-ten-things.md)

+ W systemie Linux:
  + [Konfigurowanie systemu Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Konfigurowanie systemu Linux DSVM (CentOS)](linux-dsvm-intro.md)
  + [Analiza danych w systemie Linux DSVM](linux-dsvm-walkthrough.md)
