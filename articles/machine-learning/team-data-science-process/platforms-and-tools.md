---
title: Platformy i narzędzia dla projektów do nauki o danych — danych zespołu dla celów naukowych
description: Wyszczególniono i omówiono zasoby danych i analiz, które muszą być dostępne dla przedsiębiorstw standaryzacji na procesie nauki o danych zespołu.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 31c6c1258b6d20eb2d2802cf23244cb59df682c0
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281271"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Platformy i narzędzia dla projektów do nauki o danych

Firma Microsoft udostępnia pełnego zakresu danych i analiz usług i zasobów dla platform zarówno w chmurze, jak i lokalnie. Mogą one wdrażane, umożliwiają wykonywanie swoje projekty do nauki o danych, wydajność i skalowalność. Wskazówki dla zespołów, implementowanie projekty do nauki o danych na słupkowych, wersja kontrolowane i sposób współpracy są dostarczane przez [zespołu danych dla celów naukowych](overview.md) (TDSP).  Konspekt ról pracowników i ich skojarzonych zadań, które są obsługiwane przez zespół do nauki o danych standaryzacji na temat tego procesu można zobaczyć [zespołu danych dla celów naukowych role i zadania](roles-tasks.md).

Usługi danych i analiz dostępnych dla zespołów do nauki o danych przy użyciu przetwarzania TDSP obejmują:

- Maszyny wirtualne do nauki o danych (Windows i Linux CentOS)
- Klastry HDInsight Spark
- SQL Data Warehouse
- Azure Data Lake
- Klastry HDInsight Hive
- Azure File Storage
- SQL Server 2016 R Services

W tym dokumencie firma Microsoft Zwięźle opisz zasoby i udostępniają linki do samouczki i przewodniki zespoły przetwarzania TDSP zostały opublikowane. Mogą one pomóc w Dowiedz się, jak używać ich krok po kroku i Rozpocznij korzystanie z nich do kompilowania inteligentnych aplikacji. Więcej informacji na temat tych zasobów jest dostępna na stronach produktów. 

## <a name="data-science-virtual-machine-dsvm"></a>Maszyna wirtualna do nauki o danych (DSVM)

Maszyna wirtualna do nauki o danych oferowana zarówno Windows, jak i Linux przez firmę Microsoft i zawiera popularne narzędzia do analizy danych modelowania i opracowywania działań. Zawiera ona narzędzia takie jak:

- Microsoft R Server Developer Edition 
- Anaconda Python dystrybucji
- Notesy Jupyter notebook dla języka Python i R 
- Visual Studio Community Edition, za pomocą języka Python i narzędzia języka R na Windows / Eclipse w systemie Linux
- Power BI desktop dla Windows
- SQL Server 2016 Developer Edition w systemie Windows / Postgres w systemie Linux

Obejmuje również **narzędzia do uczenia Maszynowego i sztucznej Inteligencji** , takich jak xgboost, mxnet i Vowpal Wabbit.

Obecnie jest dostępna w DSVM **Windows** i **Linux CentOS** systemów operacyjnych. Wybierz rozmiar maszyny wirtualnej DSVM (liczba rdzeni procesora CPU) i ilość pamięci odpowiednio do potrzeb projektów do nauki o danych, które planujesz wykonać na nim. 

Aby uzyskać więcej informacji na temat wersji Windows wersje maszyny wirtualnej, zobacz [maszyny wirtualnej do nauki o danych Microsoft](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) w witrynie Azure marketplace. Dla wersji systemu Linux maszyny DSVM, zobacz [maszyny wirtualnej do nauki o danych dla systemu Linux](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Aby dowiedzieć się, jak wykonywać niektóre typowe zadania wydobywania danych na maszyny DSVM wydajnie, zobacz [dziesięć rzeczy, które można wykonać na maszynie wirtualnej analizy danych](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Klastry usługi Azure HDInsight Spark

Apache Spark to równoległego typu open source platforma, która obsługuje przetwarzanie w pamięci w celu zwiększania wydajności aplikacji do analizy danych big data przetwarzania. Aparat przetwarzania Spark zaprojektowano pod kątem szybkości, łatwości użycia i zaawansowanych możliwości analitycznych. Możliwości w pamięci platforma Spark, że to dobry wybór w przypadku algorytmów iteracyjnych używanych w usłudze machine learning i obliczeń wykresu. Platforma Spark jest również zgodna z usługą Azure Blob storage (WASB) istniejących danych przechowywanych na platformie Azure pozwala na łatwe przetwarzanie przy użyciu platformy Spark.

Tworząc klaster Spark w usłudze HDInsight, tworzysz zasoby obliczeniowe systemu Azure z zainstalowaną i skonfigurowaną platformą Spark. Tworzenie klastra Spark w HDInsight trwa około 10 minut. Store dane, które mają być przetwarzane w usłudze Azure Blob storage. Aby uzyskać informacje na temat korzystania z usługi Azure Blob Storage z klastrem, zobacz [używanie zgodnego systemem plików HDFS usługi Azure Blob storage z usługą Hadoop w HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

Przetwarzanie TDSP zespołu firmy Microsoft został opublikowany dwóch end-to-end — wskazówki dotyczące sposobu używania klastrów usługi Azure HDInsight Spark do tworzenia rozwiązań do nauki o danych, co przy użyciu języka Python i innych Scala. Aby uzyskać więcej informacji na temat usługi Azure HDInsight **klastry Spark**, zobacz [omówienie: Platforma Apache Spark w systemie HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Aby dowiedzieć się, jak tworzyć, używając rozwiązania do nauki o danych **Python** klastrze Azure HDInsight Spark, zobacz [Omówienie programu do nauki o danych przy użyciu platformy Spark w usłudze Azure HDInsight](spark-overview.md). Aby dowiedzieć się, jak tworzyć, używając rozwiązania do nauki o danych **Scala** klastrze Azure HDInsight Spark, zobacz [do nauki o danych przy użyciu języka Scala i aparatu Spark na platformie Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Usługa Azure SQL Data Warehouse umożliwia skalowanie zasobów obliczeniowych, łatwe i w ciągu kilku sekund, bez konieczności nadmiernej aprowizacji lub uiszczania nadmiernych opłat. Zapewnia także wyjątkową możliwość wstrzymania wykorzystanie zasobów obliczeniowych, dzięki czemu masz swobodę w lepszym zarządzaniu kosztami chmury. Możliwość wdrażania zasobów obliczeniowych skalowalne umożliwia wprowadzanie wszystkich danych do usługi Azure SQL Data Warehouse. Koszty magazynowania są minimalne, i mogą wykonywać obliczenia tylko na części zestawów danych, które mają być analizowane. 

Aby uzyskać więcej informacji na temat usługi Azure SQL Data Warehouse, zobacz [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) witryny sieci Web. Aby dowiedzieć się, jak tworzyć end-to-end zaawansowanych rozwiązań analizy usługa SQL Data Warehouse, zobacz [zespołu danych dla celów naukowych w działaniu: Korzystanie z programu SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Usługi Azure data lake jest jako repozytorium całego przedsiębiorstwa każdego typu danych zebranych w jednej lokalizacji, przed wszelkie wymagania formalne lub schematu, które są narzucone. Ta elastyczność umożliwia wszystkich typów danych, które mają być przechowywane w usłudze data lake, niezależnie od jego rozmiaru i struktury lub szybkość ich przetwarzania. Organizacje mogą następnie korzystanie z usługi Hadoop lub zaawansowanych analiz, aby znaleźć wzorce w tych danych, jeziora. Data Lake może również służyć jako repozytorium w celu przygotowania danych tańszych przed curating dane i przenosić je do magazynu danych.

Aby uzyskać więcej informacji na temat usługi Azure Data Lake, zobacz [Introducing Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Aby dowiedzieć się, jak tworzyć rozwiązania do analizy skalowalnych danych end-to-end z usługą Azure Data Lake, zobacz [skalowalna analiza danych w usłudze Azure Data Lake: Przewodnik end-to-end](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Klastry usługi Azure HDInsight Hive (Hadoop)

Apache Hive jest platformy Hadoop, która umożliwia podsumowanie danych, zapytań i analizy danych przy użyciu HiveQL, język zapytań, podobnych do bazy danych SQL w systemie magazynu danych. Gałąź można interaktywną eksplorację danych, lub do tworzenia zadań przetwarzania wsadowego wielokrotnego użytku.

Gałąź pozwala na strukturę projektu w dużej mierze pozbawionych struktury danych. Po zdefiniowaniu struktury, można użyć gałęzi kwerendy te dane w klastrze platformy Hadoop bez konieczności użycia lub nawet wiedzieć, Java lub MapReduce. HiveQL (język zapytań programu Hive) umożliwia pisanie zapytań przy użyciu instrukcji, które są podobne do T-SQL.

Dla analityków danych Hive można uruchomić Python User-Defined funkcjom definiowanym w zapytań programu Hive do przetwarzania rekordów. Tę możliwość, które znacznie rozszerza możliwości zapytań programu Hive w analizie danych. W szczególności umożliwia analitykom danych związanych z technicznego opracowywania funkcji skalowalne w językach, które są prawie zapoznać się z: HiveQL podobnego do SQL i Python. 

Aby uzyskać więcej informacji na temat usługi Azure HDInsight Hive klastrów, zobacz [używanie Hive i HiveQL z usługą Hadoop w HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Aby dowiedzieć się, jak tworzyć skalowalne danych end-to-end rozwiązania analizy przy użyciu usługi Azure HDInsight Hive klastrów, zobacz [zespołu danych dla celów naukowych w działaniu: przy użyciu klastrów usługi HDInsight Hadoop](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Usługa Azure File Storage to usługa, która oferuje udziały plików w chmurze przy użyciu standardowego protokołu bloku komunikatów serwera (SMB). Obsługiwane są wersje 2.1 i 3.0 protokołu SMB. W usłudze Magazyn plików Azure można migrować starsze aplikacje korzystające z udziałów plików na platformę Azure szybko i bez kosztownych modyfikacji oprogramowania. Aplikacje uruchomione na maszynach wirtualnych lub w ramach usług w chmurze platformy Azure, a także na klientach lokalnych mogą instalować udziały plików w chmurze tak samo jak aplikacja na komputerze instalująca typowy udział SMB. Dowolna liczba składników aplikacji może następnie równocześnie zainstalować udział Magazynu plików i uzyskiwać do niego dostęp.

Szczególnie przydatne w przypadku projektów do nauki o danych jest możliwość tworzenia magazynu plików platformy Azure jako miejsce, aby udostępnić dane projektu z członkami swojego zespołu projektu. Następnie każdy z nich ma dostęp do tej samej kopii danych w usłudze Azure file storage. Aby udostępnić zestawy funkcji wygenerowanych podczas realizacji projektu mogą również wykorzystać ten magazyn plików. Jeśli projekt jest zaangażowanie klientów, klientów można utworzyć usługi Azure file storage w ramach własnej subskrypcji platformy Azure, aby udostępnić dane projektu i funkcji z Tobą. W ten sposób klient ma pełną kontrolę nad zasobami danych projektu. Aby uzyskać więcej informacji na temat usługi Azure File Storage, zobacz [Rozpoczynanie pracy z usługą Azure File storage w Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) i [jak używać usługi Azure File Storage z systemem Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2016-r-services"></a>SQL Server 2016 R Services

Usługi R Services (In-database) zapewnia platformę do tworzenia i wdrażania inteligentne aplikacje, które można Odkrywaj nowe informacje. Rozbudowane i zaawansowane języka R, łącznie z wielu pakietów, dostarczone przez społeczność pasjonatów języka R, można użyć do tworzenia modeli i generować prognozy na podstawie danych programu SQL Server. Usługi R Services (In-database) języka R są zintegrowane z programem SQL Server, analizy są przechowywane w pobliżu danych, co pozwala wyeliminować koszty i zagrożenia bezpieczeństwa związane z przenoszeniem danych.

Usługi R Services (In-database) obsługuje języka R typu open source dzięki kompleksowemu zestawowi narzędzi programu SQL Server i technologii. Oferują doskonałą wydajność, bezpieczeństwa, niezawodności i możliwości zarządzania. Można wdrożyć rozwiązania R przy użyciu narzędzia wygodne i znane. Twoje aplikacje produkcyjne można wywołać środowiskiem uruchomieniowym języka r. i pobrać prognozy i wizualizacji przy użyciu języka Transact-SQL. Aby zwiększyć skalę i wydajność swoich rozwiązań języka R możesz także użyć biblioteki programu ScaleR. Aby uzyskać więcej informacji, zobacz [SQL Server R Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

Przetwarzanie TDSP zespół firmy Microsoft został opublikowany dwa przewodniki end-to-end, które pokazują, jak tworzyć rozwiązania do analizy danych w SQL Server 2016 R Services: jeden dla programistów języka R i jeden dla deweloperów programu SQL. Aby uzyskać **programistom języka R**, zobacz [wskazówki End-to-End do nauki o danych](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). Aby uzyskać **deweloperów SQL**, zobacz [Advanced Analytics w bazie danych dla deweloperów programu SQL (samouczek)](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix"></a>Dodatek: Narzędzia umożliwiające konfigurowanie projektów do nauki o danych

### <a name="install-git-credential-manager-on-windows"></a>Zainstaluj program Git Credential Manager na Windows

Jeśli korzystasz z przetwarzania TDSP **Windows**, musisz zainstalować **Git Credential Manager (GCM)** do komunikowania się z repozytoriami Git. Aby zainstalować usługi GCM, należy najpierw zainstalować **Chocolaty**. Aby zainstalować Chocolaty i GCM, uruchom następujące polecenia w programie Windows PowerShell jako **administratora**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Zainstaluj program Git na maszynach z systemem Linux (CentOS)

Uruchom poniższe polecenie powłoki bash, aby zainstalować program Git na maszynach z systemem Linux (CentOS):

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Generuj klucz publiczny SSH na maszynach z systemem Linux (CentOS)

Jeśli używasz maszyny z systemem Linux (CentOS) do uruchamiania poleceń usługi git, należy dodać publiczny klucz SSH maszyny do usługi Azure DevOps, aby ta maszyna jest rozpoznawany przez usługom DevOps platformy Azure. Po pierwsze należy wygenerować publiczny klucz SSH i Dodaj klucz do kluczy publicznych SSH na stronie Ustawienia zabezpieczeń usługom DevOps platformy Azure. 

- Aby wygenerować klucz SSH, uruchom dwa poniższe polecenia: 

        ssh-keygen
        cat .ssh/id_rsa.pub

![Polecenia, aby wygenerować klucz SSH](./media/platforms-and-tools/resources-1-generate_ssh.png)

- Skopiuj cały ssh w tym klucza *ssh-rsa*. 
- Zaloguj się do usługi Azure DevOps. 
- Kliknij przycisk **< imię i nazwisko\>**  w prawym górnym rogu strony i kliknij przycisk **zabezpieczeń**. 
    
    ![Kliknij swoją nazwę użytkownika, a następnie kliknij przycisk zabezpieczeń](./media/platforms-and-tools/resources-2-user-setting.png)

- Kliknij przycisk **kluczy publicznych SSH**i kliknij przycisk **+ Dodaj**. 

    ![Kliknij przycisk kluczy publicznych SSH, a następnie kliknij pozycję + Dodaj](./media/platforms-and-tools/resources-3-add-ssh.png)

- Wklej ssh key właśnie został skopiowany do pola tekstowego, a następnie zapisz.


## <a name="next-steps"></a>Kolejne kroki

Pełne przewodniki end-to-end, które przedstawiają wszystkie kroki procesu **konkretnych scenariuszy** znajdują się także. Wymieniono i połączone z opisami miniatur w [przykładowe przewodniki](walkthroughs.md) tematu. One ilustrują sposób łączenia chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 

Przykłady wykonywania czynności w procesie nauki o danych zespołu, korzystających z usługi Azure Machine Learning Studio, zobacz [za pomocą usługi Azure ML](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) ścieżka szkoleniowa dla usługi.
