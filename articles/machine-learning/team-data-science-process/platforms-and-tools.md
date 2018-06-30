---
title: Projekty - Azure zespołowe platform i narzędzia do analizy danych | Dokumentacja firmy Microsoft
description: Itemizes i omówiono dostępnych dla przedsiębiorstw standaryzacji na proces nauki danych zespołu zasobów danych i ich analiza.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: deguhath
ms.openlocfilehash: f447378816f137517a3d542a27841d0cab351473
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112684"
---
# <a name="platforms-and-tools-for-data-science-team-projects"></a>Projekty zespołowe platform i narzędzia do analizy danych

Firma Microsoft udostępnia pełną gamę danych i ich analiza usług i zasobów dla platform zarówno chmurze lub lokalnie. Wdrożeniem dokonanie wykonywania projektów analizy danych, wydajność i skalowalność. Pod kontrolą wersji wskazówki dotyczące wykonania projektów analizy danych w trackable zespołów, i sposób współpracy są dostarczane przez [proces nauki danych zespołu](overview.md) (TDSP).  Aby schematem ról pracowników i ich skojarzonych zadań, które są obsługiwane przez zespół nauki danych standaryzacji w tym procesie, zobacz [proces nauki danych zespołu ról i zadań](roles-tasks.md).

Usługi danych i ich analiza, które muszą być dostępne dla zespołów nauki danych przy użyciu TDSP obejmują:

- Maszyny wirtualne nauki danych (z systemem Windows i Linux CentOS)
- Klastry Spark w usłudze HDInsight
- SQL Data Warehouse
- Azure Data Lake
- Klastry HDInsight Hive
- Azure File Storage
- SQL Server 2016 R Services

W tym dokumencie możemy krótko opisano zasobów i udostępniają linki do samouczki i wskazówki dotyczące zespołów TDSP zostały opublikowane. Ułatwiają one informacje o sposobie korzystania z nich krok po kroku i Rozpocznij korzystanie z nich do tworzenia aplikacji inteligentnego. Więcej informacji na temat tych zasobów jest dostępna na swoich stronach produktu. 

## <a name="data-science-virtual-machine-dsvm"></a>Maszyna wirtualna nauki danych (DSVM)

Maszyna wirtualna nauki danych są oferowane w systemach Windows i Linux przez firmę Microsoft, zawiera popularne narzędzia dla działań modelowania i rozwoju nauki danych. Zawiera on narzędzia takie jak:

- Microsoft R Server Developer Edition 
- Dystrybucję oprogramowania Python anaconda
- Notesów Jupyter dla języka Python i R 
- Visual Studio Community Edition z języka Python oraz narzędzia R w systemie Windows / Zaćmienie-w systemie Linux
- Power BI desktop dla systemu Windows
- SQL Server 2016 Developer Edition w systemie Windows / Postgres w systemie Linux

Zawiera także **ML i AI narzędzia** , takich jak CNTK (Otwórz Learning głębokie źródło zestawu narzędzi firmy Microsoft), xgboost, mxnet i Vowpal Wabbit.

Aktualnie jest dostępna w DSVM **Windows** i **Linux CentOS** systemów operacyjnych. Wybierz rozmiar Twojej DSVM (liczba rdzeni procesora CPU) i ilość pamięci na podstawie potrzeb projektów analizy danych, które zamierzasz wykonać na nim. 

Aby uzyskać więcej informacji na DSVM wersji systemu Windows, zobacz [maszyny wirtualnej nauki danych Microsoft](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) w witrynie Azure marketplace. Dla wersji systemu Linux DSVM, zobacz [maszyny wirtualnej systemu Linux danych nauki](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Aby dowiedzieć się, jak wykonać niektóre typowe zadania nauki danych na DSVM wydajnie, zobacz [10 sposobów na nauki danych maszyny wirtualnej](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Azure HDInsight Spark klastrów

Platforma Apache Spark jest równoległe open source przetwarzania framework, która obsługuje przetwarzanie w pamięci w celu zwiększania wydajności aplikacji do analizy danych big data. Aparat przetwarzania Spark zaprojektowano pod kątem szybkości, łatwości użycia i zaawansowanych możliwości analitycznych. Możliwości obliczeniowych w pamięci platforma Spark stanowić dobrym rozwiązaniem dla algorytmów iteracyjnych używanych w uczeniu maszynowym i obliczeniach wykresu. Platforma Spark jest również zgodna z magazynu obiektów Blob platformy Azure (WASB), więc łatwo można przetwarzać istniejących danych przechowywanych na platformie Azure przy użyciu platformy Spark.

Tworząc klaster Spark w usłudze HDInsight, tworzysz zasoby obliczeniowe systemu Azure z zainstalowaną i skonfigurowaną platformą Spark. Tworzenie klastra Spark w usłudze HDInsight trwa około 10 minut. Przechowywanie danych mają być przetwarzane w magazynie obiektów Blob Azure. Informacje o używaniu magazynu obiektów Blob Azure z klastra, zobacz [magazynu obiektów Blob platformy Azure zgodnego systemem plików HDFS Użyj z platformą Hadoop w usłudze HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

TDSP zespołu firmy Microsoft został opublikowany dwóch end-to-end wskazówki dotyczące sposobu używania platformy Spark w usłudze Azure Hdinsight do tworzenia rozwiązań analizy danych, co przy użyciu języka Python i innych Scala. Aby uzyskać więcej informacji w usłudze Azure HDInsight **klastry Spark**, zobacz [Przegląd: platforma Apache Spark w usłudze HDInsight w systemie Linux](../../hdinsight/spark/apache-spark-overview.md). Aby dowiedzieć się, jak tworzyć danych nauki rozwiązania przy użyciu **Python** w klastrze Spark Azure HDInsight, zobacz [przegląd danych nauki używania platformy Spark w usłudze Azure HDInsight](spark-overview.md). Aby dowiedzieć się, jak tworzyć danych nauki rozwiązania przy użyciu **Scala** w klastrze Spark Azure HDInsight, zobacz [nauki danych przy użyciu języka Scala i Spark na platformie Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL Data Warehouse umożliwia łatwe i w sekundach, skalowanie zasoby obliczeniowe bez przerostu lub nadmiernie płatności. Zapewnia także unikatowy opcję, aby wstrzymać użycia zasobów obliczeniowych dzięki funkcjom, aby lepiej zarządzać koszty chmury. Możliwość wdrażania zasobów obliczeniowych skalowalne umożliwia można wyświetlić wszystkich danych do usługi Azure SQL Data Warehouse. Są minimalne kosztów magazynowania i obliczeń można uruchomić tylko na części zestawów danych, które mają być analizowane. 

Aby uzyskać więcej informacji dotyczących usługi Azure SQL Data Warehouse, zobacz [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) witryny sieci Web. Aby dowiedzieć się, jak tworzyć end-to-end zaawansowane metody analizy rozwiązania z usługą Magazyn danych SQL, zobacz [zespołu danych nauki procesu w działaniu: przy użyciu usługi SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Usługi Azure data lake jest jako repozytorium dla przedsiębiorstw każdego typu danych zebranych w jednym miejscu, przed posiadanie zapotrzebowania i schematu są narzucone. Tego rodzaju elastyczności umożliwia każdego typu danych przechowywanych w usłudze data lake, niezależnie od jego rozmiaru i struktury lub tempa jest pozyskanych. Organizacje mogą następnie używać Hadoop lub zaawansowana analityka w celu znalezienia wzorce w danych tych jeziora. Jeziora danych może również służyć jako repozytorium w celu przygotowania tanie danych przed mogą danych i przeniesienie go do magazynu danych.

Aby uzyskać więcej informacji dotyczących usługi Azure Data Lake, zobacz [Introducing Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Aby dowiedzieć się, jak zbudować rozwiązanie do nauki skalowalne end-to-end danych z usługi Azure Data Lake, zobacz [skalowalne nauki danych w usłudze Azure Data Lake: wskazówki end-to-end](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Klastry Azure HDInsight Hive (Hadoop)

Apache Hive jest Hadoop, dzięki czemu dane podsumowania, zapytania i analizy danych za pomocą HiveQL, podobnie jak SQL język kwerendy w systemie magazynu danych. Gałąź może służyć do interaktywnego eksplorowania danych lub utworzyć zadania przetwarzania wsadowego wielokrotnego użytku.

Gałąź umożliwia struktury projektu na danych niestrukturalnych w dużej mierze. Po zdefiniowaniu struktury umożliwia Hive zapytań bez konieczności użycia, lub nawet nie wie, Java lub MapReduce dane w klastra usługi Hadoop. HiveQL (język zapytań Hive) umożliwia pisanie zapytań dotyczących instrukcji, które są podobne do T-SQL.

Dla analityków danych Hive można uruchomić Python User-Defined funkcjom definiowanym gałęzi w zapytaniach w celu przetwarzania rekordów. Ta możliwość znacznie rozszerza możliwości zapytań programu Hive analizę danych. W szczególności umożliwia analityków danych przeprowadzenie engineering funkcji skalowalne w językach, które najczęściej są zaznajomieni z programem: HiveQL przypominającego SQL i Python. 

Aby uzyskać więcej informacji dotyczących klastrów Hive HDInsight Azure, zobacz [używanie Hive i HiveQL z usługą Hadoop w usłudze HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Aby dowiedzieć się, jak zbudować rozwiązanie nauki skalowalne danych na trasie o Azure Hive w usłudze Hdinsight, zobacz [zespołu danych nauki procesu w działaniu: z użyciem klastrów usługi HDInsight Hadoop](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Magazyn plików Azure to usługa, która oferuje udziały plików w chmurze przy użyciu standardowego protokołu bloku komunikatów serwera (SMB). Obsługiwane są wersje 2.1 i 3.0 protokołu SMB. W usłudze Magazyn plików Azure można migrować starsze aplikacje korzystające z udziałów plików na platformę Azure szybko i bez kosztownych modyfikacji oprogramowania. Aplikacje uruchomione na maszynach wirtualnych lub w ramach usług w chmurze platformy Azure, a także na klientach lokalnych mogą instalować udziały plików w chmurze tak samo jak aplikacja na komputerze instalująca typowy udział SMB. Dowolna liczba składników aplikacji może następnie równocześnie zainstalować udział Magazynu plików i uzyskiwać do niego dostęp.

Szczególnie przydatna w przypadku projektów analizy danych jest możliwość tworzenia magazyn plików Azure na potrzeby udostępniania danych projektu członkowie zespołu projektu. Następnie każdego z nich ma dostęp do tej samej kopii danych w usłudze magazyn plików Azure. Ten magazyn plików mogą również wykorzystać do udostępniania funkcji zestawów wygenerowanych podczas realizacji projektu. Jeśli projekt jest zaangażowania klientów, klientów można utworzyć magazyn plików Azure w ramach własnej subskrypcji Azure o udostępnienie danych projektu i funkcje. W ten sposób klient ma pełną kontrolę nad zasobów danych projektu. Aby uzyskać więcej informacji na magazyn plików Azure, zobacz [Rozpoczynanie pracy z magazynem plików Azure w systemie Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) i [jak używać magazynu plików Azure z systemem Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2016-r-services"></a>SQL Server 2016 R Services

R usługi (w database) udostępniają platformę do projektowania i wdrażania aplikacji inteligentnego, które można ujawnić nowych danych. Języka R bogaty i zaawansowane, w tym wiele pakietów udostępniane przez społeczność R służy do tworzenia modeli i generować prognoz na podstawie danych programu SQL Server. R usług (w database) języka R są zintegrowane z programem SQL Server, analytics są przechowywane w pobliżu danych, co eliminuje koszty i zagrożenia bezpieczeństwa związane z przenoszenia danych.

R usługi (w database) obsługują język R typu open source z użyciem kompleksowego zestawu narzędzi programu SQL Server i technologii. Oferują one wyższego poziomu wydajności, zabezpieczeń, niezawodności i możliwości zarządzania. Można wdrożyć rozwiązania R przy użyciu wygodne i znanych narzędzi. Aplikacji produkcyjnych można wywołać środowiska uruchomieniowego języka R i pobrać prognoz i elementy wizualne przy użyciu języka Transact-SQL. Możesz również użyć bibliotek ScaleR poprawiające skalowalność i wydajność rozwiązań R. Aby uzyskać więcej informacji, zobacz [usług SQL Server R](https://msdn.microsoft.com/library/mt604845.aspx)

Zespół TDSP firmy Microsoft został opublikowany dwóch wskazówki end-to-end, których przedstawiono sposób tworzenia rozwiązań analizy danych w programie SQL Server 2016 R Services: jeden dla programistów języka R i jeden dla deweloperów SQL. Dla **programistów R**, zobacz [wskazówki End-to-End nauki danych](https://msdn.microsoft.com/library/mt612857.aspx). Dla **deweloperzy SQL**, zobacz [w bazie danych zaawansowane analizy dla deweloperów SQL (samouczek)](https://msdn.microsoft.com/library/mt683480.aspx).


## <a name="appendix"></a>Dodatek: Narzędzia do ustawiania projektów analizy danych

### <a name="install-git-credential-manager-on-windows"></a>Zainstaluj Menedżera poświadczeń Git w systemie Windows

Jeśli wykonujesz TDSP **Windows**, musisz zainstalować **Git Menedżera poświadczeń (GCM)** do komunikowania się z repozytoriów narzędzia Git. Aby zainstalować usługi GCM, należy najpierw zainstalować **Chocolaty**. Aby zainstalować Chocolaty i GCM, uruchom następujące polecenia w programie Windows PowerShell jako **administratora**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Zainstaluj usługę Git na komputerach z systemem Linux (CentOS)

Uruchom następujące polecenie bash, aby zainstalować Git na maszynach z systemem Linux (CentOS):

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Generowanie klucza publicznego SSH na komputerach z systemem Linux (CentOS)

Jeśli używasz maszyny z systemem Linux (CentOS) do uruchamiania polecenia git, należy dodać klucz publiczny SSH na komputerze z serwerem programu VSTS tak, aby ten komputer jest rozpoznawany przez serwer programu VSTS. Najpierw należy wygenerować klucz publiczny SSH, a następnie dodaj klucz do kluczy publicznych SSH na stronie Ustawienia zabezpieczeń usługi VSTS. 

- Aby wygenerować klucz SSH, uruchom następujące dwa polecenia: 

        ssh-keygen
        cat .ssh/id_rsa.pub

![](./media/platforms-and-tools/resources-1-generate_ssh.png)

- Skopiuj cały ssh w tym klucza *ssh-rsa*. 
- Zaloguj się do serwera programu VSTS. 
- Kliknij przycisk **< imię i nazwisko\>**  w prawym górnym rogu strony i kliknij przycisk **zabezpieczeń**. 
    
    ![](./media/platforms-and-tools/resources-2-user-setting.png)

- Kliknij przycisk **klucze publiczne SSH**i kliknij przycisk **+ Dodaj**. 

    ![](./media/platforms-and-tools/resources-3-add-ssh.png)

- Wklej ssh klucza skopiowany w polu tekstowym, a następnie zapisz.


## <a name="next-steps"></a>Kolejne kroki

Pełne end-to-end wskazówki, które pokazują wszystkie kroki procesu **określonych scenariuszy** podawane są również. Wymieniono i połączone z opisami miniatur w [wskazówki przykład](walkthroughs.md) tematu. Pokazują one sposób łączenia chmury, narzędzia lokalnych i usług w przepływie pracy lub potoku, aby utworzyć aplikację inteligentnego. 

Przykłady wykonywania czynności w procesie nauki zespołu danych korzystających z usługi Azure Machine Learning Studio można znaleźć [z ML Azure](http://aka.ms/datascienceprocess) ścieżkę szkoleniową.