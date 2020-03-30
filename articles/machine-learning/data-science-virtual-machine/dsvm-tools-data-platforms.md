---
title: Obsługiwane platformy danych
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się więcej o obsługiwanych platformach danych i narzędziach dla maszyny wirtualnej do nauki o danych platformy Azure.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: cd787881957d78f179107e46b2650de4618c7724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282328"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Platformy danych obsługiwane na maszynie wirtualnej do nauki o danych

Dzięki maszynie wirtualnej do nauki o danych (DSVM) można tworzyć analizy na wielu platformach danych. Oprócz interfejsów do zdalnych platform danych DSVM zapewnia lokalne wystąpienie szybkiego rozwoju i prototypowania.

Następujące narzędzia platformy danych są obsługiwane w dsvm.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| | |
| ------------- | ------------- |
| co to jest?   | Wystąpienie lokalnej relacyjnej bazy danych      |
| Obsługiwane wersje DSVM      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| Typowe zastosowania      | Szybki rozwój lokalnie z mniejszym zestawem danych <br/> Uruchamianie w bazie danych R   |
| Odnośniki do próbek      |    Mała próbka zestawu danych w Nowym Jorku jest ładowana do bazy danych SQL:<br/>  `nyctaxi` <br/> Przykład jupyter pokazujący serwer uczenia maszynowego firmy Microsoft i analizę w bazie danych można znaleźć na stronie:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Powiązane narzędzia w systemie DSVM       | SQL Server Management Studio <br/> Sterowniki ODBC/JDBC<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> SQL Server Developer Edition może służyć tylko do celów deweloperskich i testowych. Aby uruchomić ją w wersji produkcyjnej, potrzebna jest licencja lub jedna z maszyn wirtualnych programu SQL Server.


### <a name="setup"></a>Konfiguracja

Serwer bazy danych jest już wstępnie skonfigurowany, a usługi `SQL Server (MSSQLSERVER)`systemu Windows związane z programem SQL Server (np. ) są uruchamiane automatycznie. Jedynym krokiem ręcznym jest włączenie analizy w bazie danych przy użyciu programu Microsoft Machine Learning Server. Analizę można włączyć, uruchamiając następujące polecenie jako jednorazową akcję w programie SQL Server Management Studio (SSMS). Uruchom to polecenie po zalogowaniu się jako administrator komputera, otwórz nową kwerendę w `master`SSMS i upewnij się, że wybrana baza danych to:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Aby uruchomić program SQL Server Management Studio, można wyszukać "SQL Server Management Studio" na liście programów lub użyć usługi Windows Search, aby go znaleźć i uruchomić. Po wyświetleniu monitu o podanie poświadczeń wybierz **pozycję Uwierzytelnianie systemu Windows** i użyj nazwy komputera lub ```localhost``` w polu Nazwa programu SQL **Server.**

### <a name="how-to-use-and-run-it"></a>Jak go używać i uruchamiać

Domyślnie serwer bazy danych z domyślnym wystąpieniem bazy danych jest uruchamiany automatycznie. Za pomocą narzędzi, takich jak SQL Server Management Studio na maszynie Wirtualnej, można uzyskać dostęp do bazy danych programu SQL Server lokalnie. Konta administratora lokalnego mają dostęp administratora w bazie danych.

Ponadto dsvm jest wyposażony w sterowniki ODBC i JDBC do rozmowy z sql server, baz danych SQL Azure i usługi Azure SQL Data Warehouse z aplikacji napisanych w wielu językach, w tym Python i Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Jak jest skonfigurowany i zainstalowany na DSVM? 

 Program SQL Server jest instalowany w standardowy sposób. Można go znaleźć `C:\Program Files\Microsoft SQL Server`w . Wystąpienie serwera uczenia maszynowego w `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`bazie danych znajduje się pod adresem . DSVM ma również oddzielne autonomiczne wystąpienie serwera uczenia `C:\Program Files\Microsoft\R Server\R_SERVER`maszynowego, które jest zainstalowane w . Te dwa wystąpienia serwera uczenia maszynowego nie współużytkuje bibliotek.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (samodzielny)

| | |
| ------------- | ------------- |
| co to jest?   | Autonomiczne (pojedynczy węzeł w procesie) wystąpienie popularnej platformy Apache Spark; system szybkiego, wielkoskalowego przetwarzania danych i uczenia maszynowego     |
| Obsługiwane wersje DSVM      | Linux     |
| Typowe zastosowania      | * Szybki rozwój aplikacji Spark/PySpark lokalnie z mniejszym zestawem danych i późniejszym wdrożeniem w dużych klastrach Platformy Spark, takich jak Azure HDInsight<br/> * Test microsoft machine learning server spark kontekście <br />* Użyj SparkML lub biblioteki [MMLSpark](https://github.com/Azure/mmlspark) open source firmy Microsoft do tworzenia aplikacji ML |
| Odnośniki do próbek      |    Próbka wypytera: <br />&nbsp;&nbsp;* ~/notebooki/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooki/MMLSpark <br /> Serwer uczenia maszynowego firmy Microsoft (kontekst platformy Spark): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Powiązane narzędzia w systemie DSVM       | PySpark, Scala<br/>Jupyter (Jądra Iskry/PySpark)<br/>Serwer uczenia maszynowego firmy Microsoft, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Korzystanie
Zadania platformy Spark można przesłać w `spark-submit` `pyspark` wierszu polecenia, uruchamiając polecenie lub polecenie. Notes Jupyter można również utworzyć, tworząc nowy notes z jądrem Spark.

Platformy Spark from R można używać przy użyciu bibliotek, takich jak SparkR, Sparklyr i Microsoft Machine Learning Server, które są dostępne na dsvm. Zobacz wskaźniki do próbek w powyższej tabeli.

### <a name="setup"></a>Konfiguracja
Przed uruchomieniem w kontekście platformy Spark w programie Microsoft Machine Learning Server w wersji Ubuntu Linux DSVM należy wykonać krok konfiguracji jednorazowej, aby włączyć lokalny pojedynczy węzeł Usługi HDFS i wystąpienie Yarn. Domyślnie usługi Hadoop są zainstalowane, ale wyłączone w systemie DSVM. Aby je włączyć, uruchom następujące polecenia jako root po raz pierwszy:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Możesz zatrzymać usługi związane z Hadoop, gdy nie ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```są już potrzebne, uruchamiając program .

Przykład, który pokazuje, jak rozwijać i testować MRS w zdalnym kontekście platformy Spark (który jest `/dsvm/samples/MRS` autonomicznym wystąpieniem platformy Spark w dsvm) jest dostępna w katalogu.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Jak jest skonfigurowany i zainstalowany na DSVM? 
|Platforma|Lokalizacja instalacji ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X-bin-hadoopX.X|


Biblioteki uzyskujące dostęp do danych z magazynu obiektów Blob platformy Azure lub usługi Azure Data Lake Storage przy użyciu bibliotek uczenia maszynowego programu Microsoft MMLSpark są preinstalowane w $SPARK_HOME/jars. Te jary są ładowane automatycznie po uruchomieniu platformy Spark. Domyślnie program Spark używa danych na dysku lokalnym. 

W przypadku wystąpienia platformy Spark w systemie DSVM, aby uzyskać dostęp do danych przechowywanych w magazynie obiektów Blob lub usłudze Azure Data Lake Storage, należy utworzyć i skonfigurować `core-site.xml` plik na podstawie szablonu znajdującego się w $SPARK_HOME/conf/core-site.xml.template. Musisz również mieć odpowiednie poświadczenia, aby uzyskać dostęp do magazynu obiektów Blob i usługi Azure Data Lake Storage. (Należy zauważyć, że pliki szablonów używają symboli zastępczych dla magazynu obiektów Blob i konfiguracji usługi Azure Data Lake Storage).

Aby uzyskać bardziej szczegółowe informacje na temat tworzenia poświadczeń usługi Usługi Azure Data Lake Storage, zobacz [Uwierzytelnianie za pomocą usługi Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Po wprowadzeniu poświadczeń magazynu obiektów Blob lub usługi Azure Data Lake Storage w pliku core-site.xml można odwoływać się do danych przechowywanych w tych źródłach za pomocą prefiksu identyfikatora URI wasb:// lub adl://.

