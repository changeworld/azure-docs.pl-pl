---
title: Co to jest Data Science Virtual Machine platformy Azure
description: Najważniejsze scenariusze analizy i składniki dla Virtual Machines nauki o danych w systemie Windows i Linux.
keywords: narzędzia do nauki dotyczące danych, maszyna wirtualna do nauki o danych, narzędzia do nauki o danych, nauka danych systemu Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 02/22/2019
ms.openlocfilehash: f40ad01d74df26e5a6d90c348a706e8a3c666f2f
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71675073"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Co to jest Data Science Virtual Machine platformy Azure dla systemów Linux i Windows?

Data Science Virtual Machine (DSVM) to dostosowany obraz maszyny wirtualnej na platformie Azure Cloud Platform zbudowany specjalnie na potrzeby analizy danych. Ma wiele popularnych narzędzi do nauki o danych i innych, wstępnie zainstalowanych i wstępnie skonfigurowanych, aby szybko rozpocząć tworzenie inteligentnych aplikacji na potrzeby zaawansowanej analizy. 

Konfiguracje narzędzi są rygorystycznie testowane przez analityków danych i deweloperów w firmie Microsoft oraz szerszej społeczności naukowej o danych. Ten test zapewnia stabilność i ogólną żywotność.

DSVM jest dostępny w:
+ Windows Server 2016, Windows Server 2012
+ Ubuntu 16,04 LTS i CentOS 7,4

> [!NOTE]
> Wszystkie narzędzia maszyny wirtualnej do uczenia głębokiego zostały złożone w Data Science Virtual Machine. 


## <a name="what-can-i-do-with-the-dsvm"></a>Co mogę zrobić z DSVM?
Celem Data Science Virtual Machine jest zapewnienie pracownikom danych wszystkich poziomów umiejętności i między branżami z bezpłatnym, wstępnie skonfigurowanym i w pełni zintegrowanym środowiskiem nauki o danych. Zamiast samodzielnie wdrażać porównywalny obszar roboczy, możesz udostępnić DSVM. Ten wybór może zaoszczędzić dni lub nawet _tygodnie_ w procesach instalacji, konfiguracji i zarządzania pakietami. Po przydzieleniu DSVM można od razu rozpocząć pracę nad projektem analizy danych.

DSVM jest zaprojektowana i skonfigurowana do pracy z szeroką gamę scenariuszy użycia. Środowisko można skalować w górę lub w dół w miarę zmiany wymagań projektu. Możesz również użyć preferowanego języka do programowania zadań analizy danych i instalowania innych narzędzi, aby dostosować system do własnych potrzeb.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Wstępnie skonfigurowany pulpit analityczny w chmurze
DSVM zawiera konfigurację linii bazowej dla zespołów do nauki o danych, które chcą zamienić swoje pulpity lokalne na zarządzany pulpit w chmurze. Ta linia bazowa zapewnia, że wszyscy analitykowie danych w zespole mają spójną konfigurację, z którą można weryfikować eksperymenty i wspierać współpracę. Obniża ona również koszty, zmniejszając obciążenie administratora systemu. Zmniejszenie obciążenia pozwala zaoszczędzić czas wymagany do oszacowania, zainstalowania i konserwacji pakietów oprogramowania na potrzeby zaawansowanej analizy.

### <a name="data-science-training-and-education"></a>Szkolenia i edukacja w zakresie analizy danych
Instruktorzy i nauczyciele, którzy uczą klasy nauki o danych, zwykle udostępniają obraz maszyny wirtualnej. Obraz ten gwarantuje, że uczniowie mają spójną konfigurację oraz że próbki przewidywalnie działają. 

DSVM tworzy środowisko na żądanie z spójną konfiguracją, która ułatwia problemy z obsługą i niezgodnością. Przypadki, w których te środowiska muszą być tworzone często, szczególnie w przypadku krótszych klas szkoleniowych.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Elastyczna pojemność na żądanie dla projektów o dużej skali
Imprezy rozpoczynająa i konkursy danych na dużą skalę, dzięki czemu można przeskalować wydajność sprzętu, zazwyczaj przez krótki czas trwania. DSVM może ułatwić replikację środowiska nauki o danych na żądanie, na skalowalnych w poziomie serwerach, które umożliwiają eksperymenty, w których można uruchamiać zasoby obliczeniowe o wysokiej jakości.

### <a name="custom-compute-power-for-azure-notebooks"></a>Niestandardowa moc obliczeniowa dla Azure Notebooks
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) to bezpłatna usługa hostowana, która umożliwia tworzenie, uruchamianie i udostępnianie notesów Jupyter w chmurze bez konieczności instalacji. Bezpłatna warstwa usługi jest ograniczona do 4 GB pamięci i 1 GB danych. 

Aby zwolnić wszystkie limity, można dołączyć projekt notesów do DSVM lub innej maszyny wirtualnej działającej na serwerze Jupyter. Jeśli zalogujesz się do Azure Notebooks przy użyciu konta za pomocą Azure Active Directory (na przykład konta firmowego), notesy automatycznie pokazują DSVMs w każdej subskrypcji skojarzonej z tym kontem. Możesz [dołączyć DSVM do Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) , aby rozwinąć dostępną moc obliczeniową.

### <a name="short-term-experimentation-and-evaluation"></a>Krótkoterminowe eksperymenty i Ocena
Możesz użyć DSVM, aby oszacować lub poznać podobne do nich narzędzia, przy minimalnym wykorzystaniu konfiguracji:

- Microsoft Machine Learning Server
- Serwer SQL
- Narzędzia programu Visual Studio
- Jupyter
- Narzędzia uczenia głębokiego i uczenia maszynowego
- Nowe narzędzia popularne w społeczności 

Ponieważ można szybko skonfigurować DSVM, można zastosować go w innych krótkoterminowych scenariuszach użytkowania. Te scenariusze obejmują replikację opublikowanych eksperymentów, wykonywanie pokazów oraz następujące przewodniki dotyczące sesji online i samouczków konferencyjnych.

### <a name="deep-learning-with-gpus"></a>Uczenie głębokie za pomocą procesorów GPU
W DSVM modele szkoleniowe mogą korzystać z algorytmów uczenia głębokiego na sprzęcie opartym na procesorach GPU. Korzystając z możliwości skalowania maszyn wirtualnych platformy Azure, DSVM ułatwia korzystanie z sprzętu opartego na procesorze GPU w chmurze zgodnie z potrzebami. Możesz przełączyć się na maszynę wirtualną opartą na procesorze GPU podczas uczenia dużych modeli lub gdy potrzebujesz obliczeń o dużej szybkości przy zachowaniu tego samego dysku systemu operacyjnego. Można wybrać dowolną jednostkę SKU maszyn wirtualnych z serii N z funkcją DSVM. Zanotuj bezpłatne konta platformy Azure nie obsługują jednostek SKU maszyn wirtualnych obsługujących procesor GPU.

Wydanie systemu Windows Server 2016 DSVM jest wstępnie zainstalowane ze sterownikami procesora GPU, platformami i wersjami procesora GPU dla platform edukacyjnych. W wersji systemu Linux uczenie głębokie na procesorach GPU jest włączone zarówno w CentOS, jak i Ubuntu DSVMs. 

Możesz również wdrożyć Ubuntu, CentOS lub Windows 2016 wydania DSVM na maszynie wirtualnej platformy Azure, która nie jest oparta na procesorach GPU. W takim przypadku wszystkie platformy uczenia głębokiego zostaną przywrócone do trybu procesora CPU.
 
[Dowiedz się więcej o dostępnych strukturach głębokiego uczenia i AI](dsvm-deep-learning-ai-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Co zawiera DSVM?
Data Science Virtual Machine ma wiele popularnych narzędzi do nauki o danych i uczenia głębokiego, które są już zainstalowane i skonfigurowane. Zawiera również narzędzia, które ułatwiają współpracę z różnymi produktami danych i analizami platformy Azure. Te produkty obejmują Microsoft Machine Learning Server (R, Python) do kompilowania modeli predykcyjnych oraz SQL Server 2017 na potrzeby eksploracji zestawów danych na dużą skalę. DSVM obejmuje inne narzędzia z społeczności "open source" i firmy Microsoft wraz z [przykładowym kodem i notesem](dsvm-samples-and-walkthroughs.md). 

Oto lista narzędzi i platform:
+ [Obsługiwane języki programowania](dsvm-languages.md)

+ [Obsługiwane platformy danych](dsvm-data-platforms.md)

+ [Narzędzia programistyczne i środowisk IDE](dsvm-tools-development.md)

+ [Uczenie głębokie i platformy AI](dsvm-deep-learning-ai-frameworks.md)

+ [Uczenie maszynowe i narzędzia do nauki o danych](dsvm-ml-data-science-tools.md)

+ [Narzędzia do pozyskiwania danych](dsvm-tools-ingestion.md)

+ [Narzędzia do eksploracji i wizualizacji danych](dsvm-tools-explore-and-visualize.md)

W poniższej tabeli wyszczególniono i porównano główne składniki zawarte w wersjach systemu Windows i Linux Data Science Virtual Machine.

| **Narzędziem**                                                           | **Wersja systemu Windows** | **Wersja systemu Linux** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) ze wstępnie zainstalowanymi popularnymi pakietami   |T                      | T             |
| [Microsoft Machine Learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Wersja Developer zawiera: <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [kolekcję funkcji revoscaler/biblioteki revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) Parallel i Distributed High-Performance Platform (R i Python)<br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), nowe, najnowocześniejsze algorytmy uczenia maszynowego firmy Microsoft <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [R i Python operacjonalizacji](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |T                      | T |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus z aktywację udostępnioną: Excel, Word i PowerPoint   |T                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2,7 i 3,5 z preinstalowanymi popularnymi pakietami    |T                      |T              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) ze popularnymi pakietami dla wstępnie zainstalowanego języka Julia                         |T                      |T              |
| Relacyjne bazy danych                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Wersja Developer| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Narzędzia bazy danych                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [BCP, SQLCMD](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  Sterowniki ODBC/JDBC|  [Squirrel SQL](http://squirrel-sql.sourceforge.net/) (narzędzie do wykonywania zapytań), <br />  BCP, SQLCMD <br />  Sterowniki ODBC/JDBC|
| Skalowalna analiza w bazie danych za pomocą usługi SQL Server Machine Learning (R, Python) | T     |N              |
| [Serwer Jupyter Notebook](https://jupyter.org/) z następującymi jądrami:                                  | T     | T |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 R | T | T |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Python | T | T |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Julia | T | T |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 PySpark | T | T |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | T (tylko Ubuntu) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Spark     | N | T |
| JupyterHub (serwer notesu wieloużytkownikom)| N | T |
| JupyterLab (serwer notesu wieloużytkownikom) | N | T (tylko Ubuntu) |
| Narzędzia programistyczne, środowisk IDE i edytory kodu:| | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio 2019 (wersja Community)](https://www.visualstudio.com/community/) z dodatkiem git, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node. js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs)i [R Tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) | T | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio Code](https://code.visualstudio.com/) | T | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | T | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [platformy PyCharm itd Community Edition](https://www.jetbrains.com/pycharm/) | N | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Atom](https://atom.io/) | N | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Juno (Julia IDE)](https://junolab.org/)| T | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 vim i Emacs: | T | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 git i git bash | T | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 OpenJDK | T | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 .NET Framework | T | N |
| Power BI Desktop | T | N |
| Zestawy SDK umożliwiające dostęp do platformy Azure i Cortana Intelligence Suite usług | T | T |
| Narzędzia do przenoszenia i zarządzania danymi: | | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Eksplorator usługi Azure Storage | T | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure) | T | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Azure PowerShell | T | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | T | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [BLOB](https://github.com/Azure/azure-storage-fuse) | N | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | T | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure Cosmos DB narzędzia do migracji danych](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | T | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Microsoft zarządzanie danymi Gateway](https://msdn.microsoft.com/library/dn879362.aspx): przenoszenie danych między środowiskiem lokalnym a chmurą | T | N |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 narzędzia wiersza polecenia systemu UNIX/Linux | T | T |
| [Przechodzenie do szczegółów platformy Apache](https://drill.apache.org) na potrzeby eksploracji danych | T | T |
| Narzędzia uczenia maszynowego: |||
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 integrację z [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | T | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [XGBoost](https://github.com/dmlc/xgboost) | T | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | T | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | T | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Rattle](https://togaware.com/rattle/) | T | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [LightGBM](https://github.com/Microsoft/LightGBM) | N | T (tylko Ubuntu) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CatBoost](https://tech.yandex.com/catboost/) | N | T (tylko Ubuntu) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [H2O](https://www.h2o.ai/h2o/), [musujące wody](https://www.h2o.ai/sparkling-water/) | N | T (tylko Ubuntu) |
| Narzędzia uczenia głębokiego, które działają na procesorze GPU lub procesorze CPU: |  |  |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | T | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow](https://www.tensorflow.org/) | T (Windows 2016) | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Horovod](https://github.com/uber/horovod) | N | T (Ubuntu) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MXNet](https://mxnet.io/) | T (Windows 2016) | T|
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Caffe i Caffe2](https://github.com/caffe2/caffe2) | N | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t [-3](https://chainer.org/) | N | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Torch](http://torch.ch/) | N | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Theano](https://github.com/Theano/Theano) | N | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Keras](https://keras.io/)| N | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [PyTorch](https://pytorch.org/)| N | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [NVIDIA cyfr](https://github.com/NVIDIA/DIGITS) | N | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MXNet model Server](https://github.com/awslabs/mxnet-model-server) | N | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow obsługujący](https://www.tensorflow.org/serving/) | N | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorRT](https://developer.nvidia.com/tensorrt) | N | T |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [cuda, cuDNN, NVIDIA Driver](https://developer.nvidia.com/cuda-toolkit) | T | T |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej z następujących artykułów:

+ Systemy
  + [Konfigurowanie DSVM systemu Windows](provision-vm.md)
  + [Dziesięć rzeczy, które można wykonać na DSVM systemu Windows](vm-do-ten-things.md)

+ System
  + [Konfigurowanie systemu Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Konfigurowanie systemu Linux DSVM (CentOS)](linux-dsvm-intro.md)
  + [Analiza danych w systemie Linux DSVM](linux-dsvm-walkthrough.md)
