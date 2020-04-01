---
title: Platformy i narzędzia do projektów do nauki o danych - Team Data Science Process
description: Itemizes i omówiono dane i zasoby analityczne dostępne dla przedsiębiorstw standaryzujące w procesie nauki o danych zespołu.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d26dc55cfd0f2ef0bb78e4153fffe3f1cb910b73
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474871"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Platformy i narzędzia do projektów do nauki o danych

Firma Microsoft udostępnia pełne spektrum zasobów analitycznych zarówno dla platformy chmurowej, jak i lokalnej. Można je wdrożyć, aby wykonywanie projektów nauki o danych było wydajne i skalowalne. Wskazówki dla zespołów wdrażających projekty do nauki o danych w sposób śledzony, kontrolowany w wersji i współpracy są dostarczane przez [proces nauki o danych zespołu](overview.md) (TDSP).  Aby uzyskać zarys ról personelu i skojarzonych z nimi zadań obsługiwanych przez zespół analityki danych standaryzujący ten proces, zobacz [Role i zadania procesu nauki o danych zespołowych](roles-tasks.md).

Zasoby analityczne dostępne dla zespołów do nauki o danych przy użyciu TDSP obejmują:

- Maszyny wirtualne do nauki o danych (zarówno Windows, jak i Linux CentOS)
- Klastry iskrowe hdinsight
- Synapse Analytics
- Azure Data Lake
- Klastry gałęzi HDInsight
- Azure File Storage
- Usługi SQL Server 2019 R i Python
- Azure Databricks

W tym dokumencie pokrótce opisano zasoby i udostępniamy łącza do samouczków i przewodników, które zespoły TDSP zostały opublikowane. Mogą one pomóc ci nauczyć się, jak z nich korzystać krok po kroku i rozpocząć korzystanie z nich do tworzenia inteligentnych aplikacji. Więcej informacji na temat tych zasobów można znaleźć na stronach produktów. 

## <a name="data-science-virtual-machine-dsvm"></a>Maszyna wirtualna do nauki o danych (DSVM)

Maszyna wirtualna do nauki o danych oferowana zarówno w systemie Windows, jak i linux przez firmę Microsoft zawiera popularne narzędzia do modelowania i tworzenia danych. Zawiera narzędzia takie jak:

- Microsoft R Server Developer Edition 
- Dystrybucja Anaconda Python
- Zeszyty Jupyter dla Pythona i R 
- Visual Studio Community Edition z python i R Tools w systemie Windows / Eclipse na Linuksie
- Pulpit usługi Power BI dla systemu Windows
- SQL Server 2016 Developer Edition w systemie Windows / Postgres w systemie Linux

Zawiera również **narzędzia ML i AI,** takie jak xgboost, mxnet i Vowpal Wabbit.

Obecnie DSVM jest dostępny w systemach operacyjnych **Windows** i **Linux CentOS.** Wybierz rozmiar dsvm (liczba rdzeni procesora CPU i ilość pamięci) w oparciu o potrzeby projektów nauki o danych, które planujesz wykonać na nim. 

Aby uzyskać więcej informacji na temat wersji systemu Windows DSVM, zobacz [Microsoft Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) w portalu Azure Marketplace. Zobacz też: Linux data science [Virtual Machine](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu).

Aby dowiedzieć się, jak wydajnie wykonywać niektóre typowe zadania do nauki o danych na maszynie DSVM, zobacz [10 czynności, które można wykonać na maszynie wirtualnej do nauki o danych](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Klastry platformy Azure HDInsight Spark

Apache Spark to platforma przetwarzania równoległego typu open source, która obsługuje przetwarzanie w pamięci w celu zwiększania wydajności aplikacji do analizy danych big data. Silnik przetwarzania Spark został stworzony z myślą o szybkości, łatwości obsługi i zaawansowanej analizie. Możliwości obliczeń w pamięci firmy Spark sprawiają, że jest to dobry wybór dla algorytmów iteracyjnych w uczeniu maszynowym i obliczeniach wykresów. Platforma Spark jest również zgodna z usługą Azure Blob Storage (WASB), dzięki czemu istniejące dane przechowywane na platformie Azure mogą być łatwo przetwarzane przy użyciu platformy Spark.

Tworząc klaster Spark w usłudze HDInsight, tworzysz zasoby obliczeniowe systemu Azure z zainstalowaną i skonfigurowaną platformą Spark. Utworzenie klastra platformy Spark w ujrzeniu w umiań jest możliwe po około 10 minutach. Przechowywanie danych do przetworzenia w magazynie obiektów Blob platformy Azure. Aby uzyskać informacje na temat korzystania z usługi Azure Blob Storage z klastrem, zobacz [Używanie magazynu obiektów blob platformy Azure zgodnym z systemem HDFS z usługą Hadoop w usłudze HDInsight.](../../hdinsight/hdinsight-hadoop-use-blob-storage.md)

Zespół TDSP firmy Microsoft opublikował dwa kompleksowe wskazówki dotyczące używania klastrów platformy Azure HDInsight Spark do tworzenia rozwiązań do nauki o danych, jeden przy użyciu języka Python, a drugi Scala. Aby uzyskać więcej informacji na temat **klastrów platformy**Azure HDInsight Spark , zobacz [Omówienie: Apache Spark w systemie HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Aby dowiedzieć się, jak utworzyć rozwiązanie do nauki o danych przy użyciu **języka Python** w klastrze platformy Azure HDInsight Spark, zobacz [Omówienie nauki o danych przy użyciu platformy Spark w usłudze Azure HDInsight.](spark-overview.md) Aby dowiedzieć się, jak utworzyć rozwiązanie do nauki o danych przy użyciu **scali** na platformie Azure HDInsight Spark Cluster, zobacz [data science przy użyciu scala i spark na platformie Azure.](scala-walkthrough.md) 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Usługa Azure SQL Data Warehouse umożliwia łatwe i wyskakujące zasoby obliczeniowe w ciągu kilku sekund bez nadmiernej aprowizacji lub nadmiernego płacenia. Oferuje również unikalną opcję wstrzymania korzystania z zasobów obliczeniowych, co daje swobodę lepszego zarządzania kosztami chmury. Możliwość wdrażania skalowalnych zasobów obliczeniowych umożliwia wprowadzenie wszystkich danych do usługi Azure SQL Data Warehouse. Koszty magazynowania są minimalne i można uruchamiać obliczenia tylko na części zestawów danych, które mają być analizowane. 

Aby uzyskać więcej informacji na temat usługi Azure SQL Data Warehouse, zobacz witrynę sieci Web [usługi SQL Data Warehouse.](https://azure.microsoft.com/services/sql-data-warehouse) Aby dowiedzieć się, jak tworzyć kompleksowe zaawansowane rozwiązania analityczne za pomocą usługi SQL Data Warehouse, zobacz [Proces nauki o danych zespołu w akcji: korzystanie z usługi SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Usługa Azure Data Lake jest repozytorium dla całego przedsiębiorstwa każdego typu danych zebranych w jednej lokalizacji, przed nałożeniem jakichkolwiek wymagań formalnych lub schematu. Ta elastyczność umożliwia przechowywanie każdego typu danych w jeziorze danych, niezależnie od ich rozmiaru lub struktury lub szybkości ich pozyskiwania. Organizacje mogą następnie używać hadoop lub zaawansowanej analizy, aby znaleźć wzorce w tych jeziorach danych. Jeziora danych mogą również służyć jako repozytorium do przygotowania danych o niższych kosztach przed nadzorowaniem danych i przenoszeniem ich do magazynu danych.

Aby uzyskać więcej informacji na temat usługi Azure Data Lake, zobacz [Wprowadzenie usługi Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Aby dowiedzieć się, jak utworzyć skalowalne kompleksowe rozwiązanie do nauki o danych za pomocą usługi Azure Data Lake, zobacz [Scalable Data Science w usłudze Azure Data Lake: Kompleksowe wskazówki](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Klastry gałęzi usługi Azure HDInsight (Hadoop)

Apache Hive to system magazynu danych dla Hadoop, który umożliwia podsumowanie danych, wykonywanie zapytań i analizę danych przy użyciu HiveQL, języka zapytań podobnego do SQL. Ule może służyć do interaktywnego eksplorowania danych lub do tworzenia zadań przetwarzania wsadowego wielokrotnego użycia.

Gałąź umożliwia projekt struktury na danych w dużej mierze nieustrukturyzowane. Po zdefiniowaniu struktury można użyć gałęzi do wykonywania zapytań o te dane w klastrze Hadoop bez konieczności używania, a nawet wiedzieć, Java lub MapReduce. HiveQL (język zapytania hive) umożliwia pisanie zapytań z instrukcjami, które są podobne do T-SQL.

Dla analityków danych hive można uruchomić funkcje zdefiniowane przez użytkownika języka Python (UDFs) w zapytaniach hive do przetwarzania rekordów. Ta możliwość znacznie rozszerza możliwości zapytań hive w analizie danych. W szczególności umożliwia analitykom danych przeprowadzenie skalowalnej inżynierii funkcji w językach, które są najczęściej znane: hiveql podobne do języka SQL i Python. 

Aby uzyskać więcej informacji na temat klastrów hive usługi Azure HDInsight, zobacz [Używanie gałęzi i hiveQL z hadoopem w programie HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Aby dowiedzieć się, jak utworzyć skalowalne kompleksowe rozwiązanie do nauki o danych za pomocą klastrów hive usługi Azure HDInsight, zobacz [Proces nauki o danych zespołu w działaniu: przy użyciu klastrów hdinsight Hadoop.](hive-walkthrough.md)


## <a name="azure-file-storage"></a>Azure File Storage 

Usługa Azure File Storage to usługa, która oferuje udziały plików w chmurze przy użyciu standardowego protokołu bloku komunikatów serwera (SMB). Obsługiwane są wersje 2.1 i 3.0 protokołu SMB. W usłudze Magazyn plików Azure można migrować starsze aplikacje korzystające z udziałów plików na platformę Azure szybko i bez kosztownych modyfikacji oprogramowania. Aplikacje uruchomione na maszynach wirtualnych lub w ramach usług w chmurze platformy Azure, a także na klientach lokalnych mogą instalować udziały plików w chmurze tak samo jak aplikacja na komputerze instalująca typowy udział SMB. Dowolna liczba składników aplikacji może następnie równocześnie zainstalować udział Magazynu plików i uzyskiwać do niego dostęp.

Szczególnie przydatne w przypadku projektów do nauki o danych jest możliwość tworzenia magazynu plików platformy Azure jako miejsce do udostępniania danych projektu członkom zespołu projektu. Każdy z nich ma następnie dostęp do tej samej kopii danych w magazynie plików platformy Azure. Mogą również używać tego magazynu plików do udostępniania zestawów funkcji generowanych podczas wykonywania projektu. Jeśli projekt jest zaangażowanie klienta, klienci mogą utworzyć magazyn plików platformy Azure w ramach własnej subskrypcji platformy Azure do udostępniania danych projektu i funkcji z Tobą. W ten sposób klient ma pełną kontrolę nad zasobami danych projektu. Aby uzyskać więcej informacji na temat usługi Azure File Storage, zobacz [Wprowadzenie do usługi Azure File Storage w systemie Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) i Jak [korzystać z usługi Azure File Storage z systemem Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2019-r-and-python-services"></a>Usługi SQL Server 2019 R i Python

Usługi R (w bazie danych) zapewnia platformę do tworzenia i wdrażania inteligentnych aplikacji, które mogą odkryć nowe informacje. Można użyć bogatego i zaawansowanego języka języka języka Języka Języka R, w tym wielu pakietów dostarczonych przez społeczność języka R, do tworzenia modeli i generowania prognoz na podstawie danych programu SQL Server. Ponieważ usługi R (w bazie danych) integruje język Języka Języka R z programem SQL Server, analizy są trzymane blisko danych, co eliminuje koszty i zagrożenia bezpieczeństwa związane z przenoszeniem danych.

Usługi R (w bazie danych) obsługuje język języka języka R typu open source za pomocą kompleksowego zestawu narzędzi i technologii programu SQL Server. Oferują one doskonałą wydajność, bezpieczeństwo, niezawodność i łatwość zarządzania. Rozwiązania języka R można wdrażać przy użyciu wygodnych i znanych narzędzi. Aplikacje produkcyjne można wywołać środowisko uruchomieniowe języka R i pobrać prognoz i wizualizacji przy użyciu Transact-SQL. Biblioteki ScaleR są również używane do zwiększania skali i wydajności rozwiązań języka R. Aby uzyskać więcej informacji, zobacz [SQL Server R Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

Zespół TDSP firmy Microsoft opublikował dwa kompleksowe wskazówki, które pokazują, jak tworzyć rozwiązania do nauki o danych w usługach JĘZYKA SQL Server 2016 R: jeden dla programistów języka R i jeden dla deweloperów SQL. W przypadku **programistów R**zobacz [Przewodnik do nauki o danych end-to-end](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). W przypadku **programów SQL deweloperzy**zobacz [Zaawansowana analiza bazy danych dla deweloperów SQL (samouczek)](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix-tools-to-set-up-data-science-projects"></a><a name="appendix"></a>Dodatek: Narzędzia do konfigurowania projektów do nauki o danych

### <a name="install-git-credential-manager-on-windows"></a>Instalowanie Menedżera poświadczeń Git w systemie Windows

Jeśli korzystasz z TDSP w systemie **Windows,** musisz zainstalować **Menedżera poświadczeń Git (GCM)** do komunikowania się z repozytoriami Git. Aby zainstalować GCM, najpierw trzeba zainstalować **Chocolaty**. Aby zainstalować Chocolaty i GCM, uruchom następujące polecenia w programie Windows PowerShell jako **administrator:**  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Instalowanie git na komputerach Linux (CentOS)

Uruchom następujące polecenie bash, aby zainstalować git na komputerach z systemem Linux (CentOS):

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Generowanie publicznego klucza SSH na komputerach z systemem Linux (CentOS)

Jeśli używasz komputerów z systemem Linux (CentOS) do uruchamiania poleceń git, musisz dodać publiczny klucz SSH komputera do usług Azure DevOps, aby ten komputer został rozpoznany przez usługi Azure DevOps. Najpierw musisz wygenerować publiczny klucz SSH i dodać klucz do kluczy publicznych SSH na stronie ustawienia zabezpieczeń usługi Azure DevOps Services. 

1. Aby wygenerować klucz SSH, uruchom następujące dwa polecenia: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Polecenia do generowania klucza SSH](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Skopiuj cały klucz ssh, w tym *ssh-rsa*. 
1. Zaloguj się do usług DevOps platformy Azure. 
1. Kliknij **<Twoje imię\> i nazwisko** w prawym górnym rogu strony i kliknij przycisk **Zabezpieczenia**. 
    
   ![Kliknij swoje imię i nazwisko, a następnie kliknij przycisk zabezpieczenia](./media/platforms-and-tools/resources-2-user-setting.png)

1. Kliknij pozycję **Klucze publiczne SSH**i kliknij przycisk **+Dodaj**. 

   ![Kliknij pozycję Klucze publiczne SSH, a następnie kliknij przycisk +Dodaj](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Wklej klucz ssh skopiowany do pola tekstowego i zapisz.


## <a name="next-steps"></a>Następne kroki

Dostępne są również pełne wskazówki end-to-end, które pokazują wszystkie kroki w procesie dla **określonych scenariuszy.** Są one wyświetlane i połączone z opisami miniatur w [temacie Przykładowe wskazówki.](walkthroughs.md) Ilustrują one sposób łączenia narzędzi i usług w chmurze, narzędzia lokalne i usługi w przepływ pracy lub potok, aby utworzyć inteligentną aplikację. 

Przykłady, które pokazują, jak wykonać kroki w procesie nauki o danych zespołu przy użyciu usługi Azure Machine Learning Studio (klasyczny), zobacz ścieżkę uczenia azure [ml.](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)
