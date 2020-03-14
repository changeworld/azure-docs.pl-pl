---
title: Platformy i narzędzia dla projektów do nauki o danych — danych zespołu dla celów naukowych
description: Wyszczególniono i omówiono zasoby danych i analiz, które muszą być dostępne dla przedsiębiorstw standaryzacji na procesie nauki o danych zespołu.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e3297319c67ad2b7c94371356cde49113c7ef737
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251611"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Platformy i narzędzia dla projektów do nauki o danych

Firma Microsoft zapewnia pełen zakres zasobów analitycznych zarówno dla platformy w chmurze, jak i lokalnych. Mogą one wdrażane, umożliwiają wykonywanie swoje projekty do nauki o danych, wydajność i skalowalność. Wskazówki dla zespołów implementujących projekty analizy danych w sposób do śledzenia, kontroli wersji i współpracy są udostępniane przez [proces nauki o danych zespołowych](overview.md) (przetwarzania TDSP).  Aby zapoznać się z konspektem ról pracowników i skojarzonych z nimi zadań, które są obsługiwane przez zespół ds. analizy danych w tym procesie, zobacz temat [role i zadania w procesie nauki danych zespołu](roles-tasks.md).

Zasoby analityczne dostępne dla zespołów analizy danych korzystających z przetwarzania TDSP obejmują:

- Maszyny wirtualne do nauki o danych (Windows i Linux CentOS)
- Klastry HDInsight Spark
- Synapse Analytics
- Azure Data Lake
- Klastry HDInsight Hive
- Azure File Storage
- SQL Server 2019 R i usługi Python
- Azure Databricks

W tym dokumencie firma Microsoft Zwięźle opisz zasoby i udostępniają linki do samouczki i przewodniki zespoły przetwarzania TDSP zostały opublikowane. Mogą one pomóc w Dowiedz się, jak używać ich krok po kroku i Rozpocznij korzystanie z nich do kompilowania inteligentnych aplikacji. Więcej informacji na temat tych zasobów jest dostępna na stronach produktów. 

## <a name="data-science-virtual-machine-dsvm"></a>Maszyna wirtualna do nauki o danych (DSVM)

Maszyna wirtualna do nauki o danych oferowana zarówno Windows, jak i Linux przez firmę Microsoft i zawiera popularne narzędzia do analizy danych modelowania i opracowywania działań. Zawiera ona narzędzia takie jak:

- Microsoft R Server Developer Edition 
- Anaconda Python dystrybucji
- Notesy Jupyter notebook dla języka Python i R 
- Visual Studio Community Edition, za pomocą języka Python i narzędzia języka R na Windows / Eclipse w systemie Linux
- Power BI desktop dla Windows
- SQL Server 2016 Developer Edition w systemie Windows / Postgres w systemie Linux

Obejmuje to również **Narzędzia ml i AI** , takie jak xgboost, Mxnet i Vowpal Wabbit.

Obecnie DSVM jest dostępna w systemach operacyjnych **Windows** i **Linux CentOS** . Wybierz rozmiar maszyny wirtualnej DSVM (liczba rdzeni procesora CPU) i ilość pamięci odpowiednio do potrzeb projektów do nauki o danych, które planujesz wykonać na nim. 

Aby uzyskać więcej informacji na temat wersji systemu Windows DSVM, zobacz [Microsoft Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) w witrynie Azure Marketplace. W przypadku wersji systemu Linux DSVM należy zapoznać się z tematem [linux Data Science Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Aby dowiedzieć się, jak wykonywać niektóre typowe zadania analizy danych na DSVM wydajnie, zobacz [10 rzeczy, które możesz wykonać na maszynie wirtualnej do analizy danych](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Klastry usługi Azure HDInsight Spark

Apache Spark to równoległego typu open source platforma, która obsługuje przetwarzanie w pamięci w celu zwiększania wydajności aplikacji do analizy danych big data przetwarzania. Aparat przetwarzania Spark zaprojektowano pod kątem szybkości, łatwości użycia i zaawansowanych możliwości analitycznych. Możliwości w pamięci platforma Spark, że to dobry wybór w przypadku algorytmów iteracyjnych używanych w usłudze machine learning i obliczeń wykresu. Platforma Spark jest również zgodna z usługą Azure Blob storage (WASB) istniejących danych przechowywanych na platformie Azure pozwala na łatwe przetwarzanie przy użyciu platformy Spark.

Tworząc klaster Spark w usłudze HDInsight, tworzysz zasoby obliczeniowe systemu Azure z zainstalowaną i skonfigurowaną platformą Spark. Tworzenie klastra Spark w HDInsight trwa około 10 minut. Store dane, które mają być przetwarzane w usłudze Azure Blob storage. Aby uzyskać informacje na temat korzystania z usługi Azure Blob Storage z klastrem, zobacz [Korzystanie z usługi Azure Blob Storage zgodnej z systemem plików HDFS w usłudze HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

Przetwarzanie TDSP zespołu firmy Microsoft został opublikowany dwóch end-to-end — wskazówki dotyczące sposobu używania klastrów usługi Azure HDInsight Spark do tworzenia rozwiązań do nauki o danych, co przy użyciu języka Python i innych Scala. Aby uzyskać więcej informacji na temat **klastrów**usługi Azure HDInsight Spark, zobacz [Omówienie: Apache Spark w usłudze HDInsight w systemie Linux](../../hdinsight/spark/apache-spark-overview.md). Aby dowiedzieć się, jak utworzyć rozwiązanie do nauki o danych przy użyciu języka **Python** w klastrze Azure HDInsight Spark, zobacz [Omówienie analizy danych przy użyciu platformy Spark w usłudze Azure HDInsight](spark-overview.md). Aby dowiedzieć się, jak utworzyć rozwiązanie do nauki o danych przy użyciu usługi **Scala** w klastrze Azure HDInsight Spark, zobacz [naukę danych przy użyciu Scala i platformy Spark na platformie Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Usługa Azure SQL Data Warehouse umożliwia skalowanie zasobów obliczeniowych, łatwe i w ciągu kilku sekund, bez konieczności nadmiernej aprowizacji lub uiszczania nadmiernych opłat. Zapewnia także wyjątkową możliwość wstrzymania wykorzystanie zasobów obliczeniowych, dzięki czemu masz swobodę w lepszym zarządzaniu kosztami chmury. Możliwość wdrażania zasobów obliczeniowych skalowalne umożliwia wprowadzanie wszystkich danych do usługi Azure SQL Data Warehouse. Koszty magazynowania są minimalne, i mogą wykonywać obliczenia tylko na części zestawów danych, które mają być analizowane. 

Aby uzyskać więcej informacji na temat Azure SQL Data Warehouse, zobacz witrynę sieci Web [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) . Aby dowiedzieć się, jak utworzyć kompleksowe rozwiązania do analizy zaawansowanej przy użyciu SQL Data Warehouse, zobacz proces uczenie [danych zespołu w działaniu: korzystanie z SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake jest jako repozytorium w całym przedsiębiorstwie dla każdego typu danych zbieranych w jednej lokalizacji, przed wszelkimi formalnymi wymaganiami lub nakładanymi schematami. Ta elastyczność umożliwia wszystkich typów danych, które mają być przechowywane w usłudze data lake, niezależnie od jego rozmiaru i struktury lub szybkość ich przetwarzania. Organizacje mogą następnie korzystanie z usługi Hadoop lub zaawansowanych analiz, aby znaleźć wzorce w tych danych, jeziora. Data Lake może również służyć jako repozytorium w celu przygotowania danych tańszych przed curating dane i przenosić je do magazynu danych.

Aby uzyskać więcej informacji na temat Azure Data Lake, zobacz [wprowadzenie Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Aby dowiedzieć się, jak utworzyć skalowalne, kompleksowe rozwiązanie do nauki o danych Azure Data Lake, zobacz [skalowalne analizy danych w Azure Data Lake: kompleksowy przewodnik](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Klastry usługi Azure HDInsight Hive (Hadoop)

Apache Hive jest platformy Hadoop, która umożliwia podsumowanie danych, zapytań i analizy danych przy użyciu HiveQL, język zapytań, podobnych do bazy danych SQL w systemie magazynu danych. Gałąź można interaktywną eksplorację danych, lub do tworzenia zadań przetwarzania wsadowego wielokrotnego użytku.

Gałąź pozwala na strukturę projektu w dużej mierze pozbawionych struktury danych. Po zdefiniowaniu struktury, można użyć gałęzi kwerendy te dane w klastrze platformy Hadoop bez konieczności użycia lub nawet wiedzieć, Java lub MapReduce. HiveQL (język zapytań programu Hive) umożliwia pisanie zapytań przy użyciu instrukcji, które są podobne do T-SQL.

Dla analityków danych Hive można uruchomić Python User-Defined funkcjom definiowanym w zapytań programu Hive do przetwarzania rekordów. Tę możliwość, które znacznie rozszerza możliwości zapytań programu Hive w analizie danych. W szczególności umożliwia analitykom danych związanych z technicznego opracowywania funkcji skalowalne w językach, które są prawie zapoznać się z: HiveQL podobnego do SQL i Python. 

Aby uzyskać więcej informacji na temat klastrów Hive usługi Azure HDInsight, zobacz [Używanie technologii Hive i HiveQL z usługą Hadoop w usłudze HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Aby dowiedzieć się, jak utworzyć skalowalne, kompleksowe rozwiązanie do nauki o danych za pomocą klastrów Hive usługi Azure HDInsight, zobacz [proces analizy danych zespołu w akcji: korzystanie z klastrów usługi HDInsight Hadoop](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Usługa Azure File Storage to usługa, która oferuje udziały plików w chmurze przy użyciu standardowego protokołu bloku komunikatów serwera (SMB). Obsługiwane są wersje 2.1 i 3.0 protokołu SMB. W usłudze Magazyn plików Azure można migrować starsze aplikacje korzystające z udziałów plików na platformę Azure szybko i bez kosztownych modyfikacji oprogramowania. Aplikacje uruchomione na maszynach wirtualnych lub w ramach usług w chmurze platformy Azure, a także na klientach lokalnych mogą instalować udziały plików w chmurze tak samo jak aplikacja na komputerze instalująca typowy udział SMB. Dowolna liczba składników aplikacji może następnie równocześnie zainstalować udział Magazynu plików i uzyskiwać do niego dostęp.

Szczególnie przydatne w przypadku projektów do nauki o danych jest możliwość tworzenia magazynu plików platformy Azure jako miejsce, aby udostępnić dane projektu z członkami swojego zespołu projektu. Następnie każdy z nich ma dostęp do tej samej kopii danych w usłudze Azure file storage. Aby udostępnić zestawy funkcji wygenerowanych podczas realizacji projektu mogą również wykorzystać ten magazyn plików. Jeśli projekt jest zaangażowanie klientów, klientów można utworzyć usługi Azure file storage w ramach własnej subskrypcji platformy Azure, aby udostępnić dane projektu i funkcji z Tobą. W ten sposób klient ma pełną kontrolę nad zasobami danych projektu. Aby uzyskać więcej informacji na temat usługi Azure File Storage, zobacz [Rozpoczynanie pracy z usługą Azure File Storage w systemie Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) oraz [jak korzystać z usługi Azure File Storage z systemem Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2019-r-and-python-services"></a>SQL Server 2019 R i usługi Python

Usługa R Services (in-Database) udostępnia platformę do tworzenia i wdrażania inteligentnych aplikacji, które mogą odkrywać nowe informacje. Rozbudowane i zaawansowane języka R, łącznie z wielu pakietów, dostarczone przez społeczność pasjonatów języka R, można użyć do tworzenia modeli i generować prognozy na podstawie danych programu SQL Server. Ponieważ usługi R (in-Database) integrują język R z SQL Server, analiza jest bliska danych, co eliminuje koszty i zagrożenia bezpieczeństwa związane z przeniesieniem danych.

Usługa r Services (in-Database) obsługuje język R typu open source z kompleksowym zestawem SQL Server narzędzi i technologii. Oferują doskonałą wydajność, bezpieczeństwa, niezawodności i możliwości zarządzania. Można wdrożyć rozwiązania R przy użyciu narzędzia wygodne i znane. Twoje aplikacje produkcyjne można wywołać środowiskiem uruchomieniowym języka r. i pobrać prognozy i wizualizacji przy użyciu języka Transact-SQL. Aby zwiększyć skalę i wydajność swoich rozwiązań języka R możesz także użyć biblioteki programu ScaleR. Aby uzyskać więcej informacji, zobacz [SQL Server R Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

Przetwarzanie TDSP zespół firmy Microsoft został opublikowany dwa przewodniki end-to-end, które pokazują, jak tworzyć rozwiązania do analizy danych w SQL Server 2016 R Services: jeden dla programistów języka R i jeden dla deweloperów programu SQL. Dla **programistów języka R**, zobacz [Przewodnik po kompleksowej](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)analizie danych. W przypadku **deweloperów SQL**zobacz [w temacie Advanced Analytics Database for SQL Developers (samouczek)](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix"></a>Dodatek: narzędzia do konfigurowania projektów do nauki o danych

### <a name="install-git-credential-manager-on-windows"></a>Zainstaluj program Git Credential Manager na Windows

Jeśli korzystasz z usługi przetwarzania TDSP w **systemie Windows**, musisz zainstalować program **git Credential Manager (GCM)** , aby komunikować się z repozytoriami git. Aby zainstalować GCM, należy najpierw zainstalować **Chocolaty**. Aby zainstalować Chocolaty i GCM, uruchom następujące polecenia w programie Windows PowerShell jako **administrator**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Zainstaluj program Git na maszynach z systemem Linux (CentOS)

Uruchom poniższe polecenie powłoki bash, aby zainstalować program Git na maszynach z systemem Linux (CentOS):

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Generuj klucz publiczny SSH na maszynach z systemem Linux (CentOS)

Jeśli używasz maszyny z systemem Linux (CentOS) do uruchamiania poleceń usługi git, należy dodać publiczny klucz SSH maszyny do usługi Azure DevOps, aby ta maszyna jest rozpoznawany przez usługom DevOps platformy Azure. Po pierwsze należy wygenerować publiczny klucz SSH i Dodaj klucz do kluczy publicznych SSH na stronie Ustawienia zabezpieczeń usługom DevOps platformy Azure. 

1. Aby wygenerować klucz SSH, uruchom dwa poniższe polecenia: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Polecenia, aby wygenerować klucz SSH](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Skopiuj cały klucz SSH, w tym *SSH-RSA*. 
1. Zaloguj się do usługi Azure DevOps. 
1. Kliknij **< nazwę\>** w prawym górnym rogu strony, a następnie kliknij pozycję **zabezpieczenia**. 
    
   ![Kliknij swoją nazwę użytkownika, a następnie kliknij przycisk zabezpieczeń](./media/platforms-and-tools/resources-2-user-setting.png)

1. Kliknij pozycję **klucze publiczne SSH**, a następnie kliknij pozycję **+ Dodaj**. 

   ![Kliknij przycisk kluczy publicznych SSH, a następnie kliknij pozycję + Dodaj](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Wklej klucz SSH skopiowany do pola tekstowego i Zapisz.


## <a name="next-steps"></a>Następne kroki

Dostępne są również pełne przewodniki pokazujące wszystkie kroki procesu dla **konkretnych scenariuszy** . Są one wyświetlane i połączone z opisami miniatur w temacie [przykładowe](walkthroughs.md) wskazówki. One ilustrują sposób łączenia chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 

Aby zapoznać się z przykładami, które pokazują, jak wykonać kroki w procesie nauki danych zespołu przy użyciu Azure Machine Learning Studio (klasyczny), zobacz ścieżkę [usługi Azure ml](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) Learning.
