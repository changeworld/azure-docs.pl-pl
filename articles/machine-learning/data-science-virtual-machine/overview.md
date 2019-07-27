---
title: Wprowadzenie do maszyny wirtualnej do nauki o danych platformy Azure dla systemów Linux i Windows | Microsoft Docs
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
ms.openlocfilehash: 5816f53115f3ec54cbd9784894a5262b68dd6e95
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565173"
---
# <a name="what-is-azure-data-science-virtual-machine-for-linux-and-windows"></a>Co to jest platforma Azure Data Science Virtual Machine dla systemów Linux i Windows?

Maszyna wirtualna do nauki o danych to dostosowany obraz maszyny wirtualnej znajdujący się w chmurze Microsoft Azure, przeznaczony do nauki o danych. Zawiera ona wiele wstępnie zainstalowanych i skonfigurowanych, popularnych narzędzi do analizy danych, które pozwalają szybko rozpocząć tworzenie inteligentnych, zaawansowanych aplikacji analitycznych. Jest dostępna w systemach Windows Server i Linux. Oferujemy wersje maszyny wirtualnej dla systemów Windows Server 2016 i Windows Server 2012. Oferujemy maszynę wirtualną do nauki o danych dla systemu Linux w wersjach Ubuntu 16.04 LTS i CentOS 7.4.

W tym artykule omówiono czynności, które można wykonać za pomocą Data Science VM. Omówiono w nim kilka kluczowych scenariuszy dotyczących używania maszyny wirtualnej i wyszczególniono najważniejsze funkcje dostępne w wersjach systemów Windows i Linux. Artykuł zawiera również instrukcje dotyczące rozpoczynania korzystania z nich.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>Jakie są możliwości maszyny wirtualnej do nauki o danych?
Maszyna wirtualna Data Science Virtual Machine ma oferować osobom zawodowo zajmującym się danymi na wszystkich poziomach umiejętności i we wszystkich branżach bezproblemowe, wstępnie skonfigurowane oraz w pełni zintegrowane środowisko nauki o danych. Zamiast samodzielnego wdrażania porównywalnego obszaru roboczego można aprowizować maszynę wirtualną do nauki o danych, aby zaoszczędzić dni lub nawet _tygodnie_ na procesach instalacji, konfiguracji i zarządzania pakietami. Po przydzieleniu maszyny wirtualnej do nauki o danych można natychmiast rozpocząć pracę nad projektem nauki o danych.

Maszyna wirtualna do nauki o danych została zaprojektowana i skonfigurowana do obsługi szerokiej gamy scenariuszy użycia. Środowisko można skalować w górę lub w dół w miarę zmiany wymagań projektu. Możesz również użyć preferowanego języka do programowania zadań analizy danych i instalowania innych narzędzi, aby dostosować system do swoich potrzeb.

## <a name="key-scenarios"></a>Kluczowe scenariusze
W tej sekcji przedstawiono niektóre najważniejsze scenariusze, w których można wdrożyć maszynę wirtualną do nauki o danych.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Wstępnie skonfigurowany pulpit analityczny w chmurze
Maszyna wirtualna do nauki o danych udostępnia konfigurację punktu odniesienia dla zespołów zajmujących się nauką o danych, które chcą zastąpić pulpity lokalne zarządzanym pulpitem w chmurze. Ten punkt odniesienia pozwala zagwarantować, że wszyscy naukowcy zajmujący się danymi w zespole mają spójną konfigurację umożliwiającą weryfikowanie eksperymentów i promowanie współpracy. Obniża ona również koszty, zmniejszając obciążenie administratora systemu. Zmniejszenie obciążenia pozwala zaoszczędzić czas wymagany do oszacowania, zainstalowania i konserwacji różnych pakietów oprogramowania, które są konieczne do przeprowadzenia zaawansowanej analizy.

### <a name="data-science-training-and-education"></a>Szkolenia i edukacja w zakresie nauki o danych
Instruktorzy i wykładowcy dla przedsiębiorstw, które uczyją klasy do nauki o danych, zwykle udostępniają obraz maszyny wirtualnej. Zapewniają one obraz, aby upewnić się, że ich studenci mają spójną konfigurację oraz że próbki przestają działać w sposób predykcyjny. Maszyna wirtualna do nauki o danych tworzy środowisko na żądanie ze spójną konfiguracją, która ułatwia radzenie sobie z problemami z obsługą i niezgodnością. To rozwiązanie oferuje znaczące korzyści w sytuacjach, w których jest konieczne częste tworzenie takich środowisk (zwłaszcza w przypadku krótszych szkoleń).

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Elastyczna wydajność na żądanie dla projektów w dużej skali
Maratony deweloperów i konkursy w zakresie nauki o danych lub modelowanie i eksplorowanie danych na dużą skalę wymagają wydajności sprzętu skalowanej w poziomie, zwykle przez krótki czas. Data Science VM może ułatwić replikację środowiska nauki o danych na żądanie, na serwerach skalowalnych w poziomie, które umożliwiają eksperymenty, które mają być uruchamiane z wysoką szybkością zasobów obliczeniowych.

### <a name="custom-compute-power-for-azure-notebooks"></a>Niestandardowa moc obliczeniowa dla Azure Notebooks

[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) to bezpłatna usługa hostowana, która umożliwia tworzenie, uruchamianie i udostępnianie notesów Jupyter w chmurze bez konieczności instalacji. Bezpłatna warstwa usług jest jednak ograniczona do 4 GB pamięci i 1 GB danych. Aby zwolnić wszystkie limity, można dołączyć projekt notesów do Data Science VM lub innej maszyny wirtualnej z uruchomionym serwerem Jupyter. Jeśli zalogujesz się do Azure Notebooks za pomocą konta przy użyciu Azure Active Directory (na przykład konta firmowego), notesy automatycznie pokazują maszyny wirtualne do nauki o danych w każdej subskrypcji skojarzonej z tym kontem. Aby uzyskać więcej informacji, zobacz [Konfigurowanie projektów i zarządzanie nimi — warstwa Obliczenia](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="short-term-experimentation-and-evaluation"></a>Krótkoterminowe eksperymenty i ocena
Maszyna wirtualna do nauki o danych umożliwia ocenę lub naukę narzędzi, takich jak Microsoft ML Server, SQL Server, narzędzia programu Visual Studio, Jupyter, zestawy narzędzi do uczenia głębokiego i maszynowego, oraz nowych narzędzi popularnych w społeczności przy minimalnym zakresie konfiguracji. Ponieważ Data Science VM można szybko skonfigurować, można go zastosować w innych krótkoterminowych scenariuszach użytkowania. Te scenariusze obejmują replikowanie opublikowanych eksperymentów, wykonywanie pokazów, a także wskazówki dotyczące sesji online i samouczków konferencyjnych.

### <a name="deep-learning"></a>Uczenie głębokie
Maszyna wirtualna do nauki o danych może być używana na potrzeby uczenia modeli za pomocą algorytmów uczenia głębokiego na sprzęcie opartym na procesorze GPU. Maszyna wirtualna Data Science Virtual Machine korzysta z możliwości skalowania chmury platformy Azure, aby ułatwić używanie sprzętu opartego na procesorze GPU w chmurze zgodnie z wymaganiami. Można przełączyć się do maszyny wirtualnej opartej na procesorze GPU w przypadku uczenia dużych modeli lub przeprowadzania szybkich obliczeń i zachować ten sam dysk systemu operacyjnego.  Maszyna wirtualna do nauki o danych dla systemu Windows Server 2016 jest oferowana z preinstalowanymi sterownikami procesora GPU, platformami i platformami uczenia głębokiego w wersjach dla procesorów GPU. W wersji dla systemu Linux uczenie głębokie procesora GPU jest włączone w przypadku maszyn wirtualnych do nauki o danych z systemami Ubuntu i CentOS. Można wdrożyć wersję programu Ubuntu, CentOS lub Windows 2016 dla Data Science VM na maszynę wirtualną platformy Azure bez procesora GPU. W takim przypadku wszystkie platformy uczenia głębokiego zostaną przywrócone do trybu procesora CPU.

## <a name="whats-included-in-the-data-science-vm"></a>Co oferuje maszyna wirtualna do nauki o danych?
Maszyna wirtualna do nauki o danych obejmuje wiele popularnych narzędzi do nauki o danych i uczenia głębokiego, które są już zainstalowane i skonfigurowane. Zawiera również narzędzia, które ułatwiają pracę z różnymi produktami platformy Azure przeznaczonymi do obsługi danych i analizy. Wśród nich można wymienić narzędzie Microsoft ML Server (R, Python), które umożliwia tworzenie modeli predykcyjnych, lub program SQL Server 2017 pozwalający eksplorować zestawy danych w dużej skali. Data Science VM obejmuje Host innych narzędzi z społeczności typu "open source" i firmy Microsoft, jak również przykładowy kod i notesy. W poniższej tabeli wyszczególniono i porównano główne składniki maszyny wirtualnej do nauki o danych w wersji dla systemów Windows i Linux.


| **Narzędzie**                                                           | **Wersja systemu Windows** | **Wersja systemu Linux** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) z preinstalowanymi popularnymi pakietami   |T                      | T             |
| Narzędzie [Microsoft ML Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition zawiera następujące składniki: <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) — równoległa platforma rozproszona o wysokiej wydajności (R i Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) — najnowocześniejsze algorytmy uczenia maszynowego firmy Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [Operacjonalizacja środowisk R i Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |T                      | T |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) Zaawansowane aktywowanie — Excel, Word i PowerPoint   |T                      |Nie              |
| [Anaconda Python](https://www.continuum.io/) 2.7 i 3.5 z preinstalowanymi popularnymi pakietami    |T                      |T              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) z preinstalowanymi popularnymi pakietami języka Julia                         |T                      |T              |
| Relacyjne bazy danych                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Narzędzia bazy danych                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * Sterowniki ODBC/JDBC| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (narzędzie do obsługi zapytań), <br /> * bcp, sqlcmd <br /> * Sterowniki ODBC/JDBC|
| Skalowalna analiza w bazie danych z usługami uczenia maszynowego programu SQL Server (R, Python) | T     |Nie              |
| **[Serwer notesu Jupyter](https://jupyter.org/) z następującymi jądrami:**                                  | T     | T |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | T | T |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python | T | T |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | T | T |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | T | T |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | Nie | Tak (tylko Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | Nie | T |
| JupyterHub (serwer notesów dla wielu użytkowników)| Nie | T |
| JupyterLab (serwer notesów dla wielu użytkowników) | Nie | Tak (tylko Ubuntu) |
| **Narzędzia programistyczne, środowisk IDE i edytory kodu**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2019 (wersja Community)](https://www.visualstudio.com/community/) z dodatkiem git, Azure HDInsight (Hadoop), Data Lake, SQL Server narzędzia danych, [Node. js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs)i [R Tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) | T | Nie |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (Julia IDE)](https://junolab.org/)| T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim i Emacs | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git i GitBash | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* .NET Framework | T | Nie |
| Power BI Desktop | T | Nie |
| Zestawy SDK umożliwiające dostęp do platformy Azure i pakietu usług Cortana Intelligence Suite | T | T |
| **Narzędzia do przenoszenia danych i zarządzania nimi** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Eksplorator usługi Azure Storage | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | T | Nie |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | T | Nie |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Sterownik obiektów blob systemu FUSE](https://github.com/Azure/azure-storage-fuse) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy (magazyn usługi Azure Data Lake)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | T | Nie |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Narzędzie do migracji danych DocDB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | T | Nie |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Brama zarządzania danymi firmy Microsoft](https://msdn.microsoft.com/library/dn879362.aspx): Przenoszenie danych między środowiskiem lokalnym i chmurą | T | Nie |
| &nbsp;&nbsp;&nbsp;&nbsp;* Narzędzia wiersza polecenia systemu Unix/Linux | T | T |
| [Apache Drill](https://drill.apache.org) do eksploracji danych | T | T |
| **Narzędzia uczenia maszynowego** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integracja z usługą [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](https://togaware.com/rattle/) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | Nie | Tak (tylko Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CatBoost](https://tech.yandex.com/catboost/) | Nie | Tak (tylko Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/) | Nie | Tak (tylko Ubuntu) |
| **Narzędzia uczenia głębokiego** <br>Wszystkie narzędzia współdziałają z procesorem GPU lub CPU |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | Tak (Windows 2016) | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Horovod](https://github.com/uber/horovod) | Nie | Tak (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](https://mxnet.io/) | Tak (Windows 2016) | T|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe i Caffe2](https://github.com/caffe2/caffe2) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Chainer](https://chainer.org/) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](https://pytorch.org/)| Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet Model Server](https://github.com/awslabs/mxnet-model-server) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow Serving](https://www.tensorflow.org/serving/) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorRT](https://developer.nvidia.com/tensorrt) | Nie | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Sterownik CUDA, cuDNN, NVIDIA](https://developer.nvidia.com/cuda-toolkit) | T | T |
| **Platforma danych big data (tylko środowisko deweloperskie i testowe)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* Lokalna autonomiczna platforma [Spark](https://spark.apache.org/) | T | T |
| &nbsp;&nbsp;&nbsp;&nbsp;* Lokalna usługa [Hadoop](https://hadoop.apache.org/) (HDFS, YARN) | Nie | T |

## <a name="get-started"></a>Wprowadzenie

### <a name="windows-data-science-vm"></a>Maszyna wirtualna do nauki o danych z systemem Windows
* Aby uzyskać więcej informacji na temat tworzenia i używania maszyny wirtualnej do nauki o danych z systemem Windows, zobacz [Provision the Windows Data Science Virtual Machine (Aprowizowanie maszyny wirtualnej do nauki o danych z systemem Windows)](provision-vm.md). Aby uzyskać więcej informacji na temat wykonywania różnych zadań wymaganych dla projektu nauki o danych na maszynie wirtualnej do nauki o danych z systemem Windows, zobacz [Ten things you can do on the Data Science Virtual Machine (Dziesięć rzeczy, które można zrobić na maszynie wirtualnej do nauki o danych)](vm-do-ten-things.md).

### <a name="linux-data-science-vm"></a>Maszyna wirtualna do nauki o danych z systemem Linux
* Aby uzyskać więcej informacji na temat tworzenia i używania maszyny wirtualnej do nauki o danych z systemem Ubuntu, zobacz [Provision the Data Science Virtual Machine for Linux (Ubuntu) (Aprowizowanie maszyny wirtualnej do nauki o danych dla systemu Linux — Ubuntu)](dsvm-ubuntu-intro.md). Aby uzyskać więcej informacji na temat tworzenia i używania maszyny wirtualnej do nauki o danych z systemem CentOS, zobacz [Provision a Linux CentOS Data Science Virtual Machine on Azure (Aprowizowanie maszyny wirtualnej do nauki o danych z systemem Linux CentOS na platformie Azure)](linux-dsvm-intro.md).
* Przewodnik z instrukcjami dotyczącymi typowych zadań w zakresie nauki o danych na maszynie wirtualnej z systemem Linux (CentOS i Ubuntu) jest dostępny na stronie [Data science on the Linux Data Science Virtual Machine (Nauka o danych na maszynie wirtualnej do nauki o danych z systemem Linux)](linux-dsvm-walkthrough.md).

## <a name="next-steps"></a>Następne kroki
[R developer's guide to Azure](/azure/architecture/data-guide/technology-choices/r-developers-guide) (Przewodnik po platformie Azure dla deweloperów języka R)
