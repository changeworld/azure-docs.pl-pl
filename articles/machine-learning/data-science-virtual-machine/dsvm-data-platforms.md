---
title: Obsługiwane platformy danych
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się więcej na temat obsługiwanych platform i narzędzi danych dla Data Science Virtual Machine platformy Azure.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/3/2019
ms.openlocfilehash: df112889fd7cd8ad1574147072b6e13137945462
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947537"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Platformy danych obsługiwane w Data Science Virtual Machine

Za pomocą Data Science Virtual Machine (DSVM) można skompilować analizę na szeroką gamę platform danych. Oprócz interfejsów do zdalnych platform danych, DSVM zapewnia lokalne wystąpienie do szybkiego projektowania i tworzenia prototypów.

Na DSVM są obsługiwane następujące narzędzia platformy danych.

## <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition

| | |
| ------------- | ------------- |
| Co to jest?   | Lokalne wystąpienie relacyjnej bazy danych      |
| Obsługiwane wersje DSVM      | Windows      |
| Typowe zastosowania      | Szybkie programowanie lokalnie z mniejszym zestawem danych <br/> Uruchom w bazie danych R   |
| Linki do przykładów      |    Mały przykład zestawu danych w Nowym Jorku jest ładowany do bazy danych SQL:<br/>  `nyctaxi` <br/> Przykład Jupyter z informacjami o Microsoft Machine Learning Server i analizie w bazie danych można znaleźć pod adresem:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Narzędzia pokrewne na DSVM       | SQL Server Management Studio <br/> Sterowniki ODBC/JDBC<br/> Moduł pyodbc, RODBC<br />Funkcja drążenia Apache      |

> [!NOTE]
> SQL Server 2016 Developer Edition może być używana tylko do celów deweloperskich i testowych. Potrzebujesz licencji lub jednej z SQL Server maszyn wirtualnych, aby uruchomić ją w środowisku produkcyjnym.


### <a name="setup"></a>Konfiguracja

Serwer bazy danych jest już wstępnie skonfigurowany i usługi systemu Windows powiązane z SQL Server (na przykład `SQL Server (MSSQLSERVER)`) są ustawione do automatycznego uruchamiania. Jedyny krok ręczny obejmuje włączenie analizy w bazie danych przy użyciu Microsoft Machine Learning Server. Można to zrobić, uruchamiając następujące polecenie jako jednorazową akcję w SQL Server Management Studio (SSMS). Uruchom to polecenie po zalogowaniu się jako administrator komputera, Otwórz nowe zapytanie w programie SSMS i upewnij się, że wybrana baza danych ma `master`:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Aby uruchomić SQL Server Management Studio, można wyszukać ciąg "SQL Server Management Studio" na liście programów lub użyć usługi Windows Search w celu znalezienia i uruchomienia. Po wyświetleniu monitu o poświadczenia wybierz pozycję **uwierzytelnianie systemu Windows** , a w polu **nazwa SQL Server** użyj nazwy maszyny lub ```localhost```.

### <a name="how-to-use-and-run-it"></a>Jak używać i uruchamiać

Domyślnie serwer bazy danych z domyślnym wystąpieniem bazy danych jest uruchamiany automatycznie. Aby uzyskać dostęp do bazy danych SQL Server lokalnie, można użyć narzędzi takich jak SQL Server Management Studio na maszynie wirtualnej. Konta administratorów lokalnych mają dostęp administratora do bazy danych programu.

Ponadto DSVM zawiera sterowniki ODBC i JDBC, aby komunikować się z SQL Server, bazami danych SQL Azure i Azure SQL Data Warehouse z aplikacji utworzonych w wielu językach, w tym Python i Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Jak została skonfigurowana i zainstalowana na DSVM? 

 SQL Server jest zainstalowany w standardowy sposób. Można go znaleźć pod adresem `C:\Program Files\Microsoft SQL Server`. Wystąpienie Machine Learning Server w bazie danych znajduje się w `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. DSVM ma również oddzielne autonomiczne wystąpienie Machine Learning Server, które jest instalowane w `C:\Program Files\Microsoft\R Server\R_SERVER`. Te dwa wystąpienia Machine Learning Server nie współdzielą bibliotek.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2. x (autonomiczne)

| | |
| ------------- | ------------- |
| Co to jest?   | Autonomiczne (Jednowęzłowe wystąpienie w procesie) popularnej platformy Apache Spark; System szybkiego i uczenia maszynowego na dużą skalę     |
| Obsługiwane wersje DSVM      | Linux <br /> Windows (wersja eksperymentalna)      |
| Typowe zastosowania      | * Szybkie opracowywanie aplikacji platformy Spark/PySpark lokalnie z mniejszym zestawem danych i późniejszym wdrożeniem w dużych klastrach Spark, takich jak usługa Azure HDInsight<br/> * Test Microsoft Machine Learning Server — kontekst platformy Spark <br />* Użyj SparkML lub biblioteki [MMLSpark](https://github.com/Azure/mmlspark) Open Source firmy Microsoft do kompilowania aplikacji ml |
| Linki do przykładów      |    Przykład Jupyter: <br />&nbsp; @ no__t-1 * ~/notebooks/SparkML/pySpark <br /> &nbsp; @ no__t-1 * ~/notebooks/MMLSpark <br /> Microsoft Machine Learning Server (kontekst Spark):/dsvm/samples/MRS/MRSSparkContextSample.R |
| Narzędzia pokrewne na DSVM       | PySpark, Scala<br/>Jupyter (jądra Spark/PySpark)<br/>Microsoft Machine Learning Server, Spark, Sparklyr <br />Funkcja drążenia Apache      |

### <a name="how-to-use-it"></a>Jak z niej korzystać
Zadania platformy Spark można przesłać w wierszu polecenia, uruchamiając polecenie `spark-submit` lub `pyspark`. Możesz również utworzyć Notes Jupyter, tworząc nowy Notes z jądrem platformy Spark.

Platformy Spark można użyć z języka R, korzystając z bibliotek takich jak Spark, Sparklyr i Microsoft Machine Learning Server, które są dostępne w DSVM. Zobacz wskaźniki do przykładów w powyższej tabeli.

> [!NOTE]
> Uruchamianie Microsoft Machine Learning Server w kontekście platformy Spark DSVM jest obsługiwane tylko w wersji Ubuntu Linux DSVM.



### <a name="setup"></a>Konfiguracja
Przed uruchomieniem w kontekście platformy Spark w Microsoft Machine Learning Server na Ubuntu Linux DSVM Edition należy wykonać jednorazowy krok konfiguracji, aby włączyć lokalny pojedynczy węzeł Hadoop HDFS i wystąpienie przędzenia. Domyślnie usługi Hadoop są zainstalowane, ale wyłączone na DSVM. Aby je włączyć, uruchom następujące polecenia jako główne po raz pierwszy:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Usługi związane z usługą Hadoop można zatrzymać, gdy nie są już potrzebne, uruchamiając ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```.

Przykład demonstrujący, jak tworzyć i testować większość w zdalnym kontekście Spark (czyli autonomicznym wystąpieniu platformy Spark na DSVM), które są dostępne i są obsługiwane w katalogu `/dsvm/samples/MRS`.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Jak została skonfigurowana i zainstalowana na DSVM? 
|Platforma|Lokalizacja instalacji ($SPARK _HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Biblioteki umożliwiające dostęp do danych z usługi Azure Blob Storage lub Azure Data Lake Storage przy użyciu bibliotek uczenia maszynowego programu Microsoft MMLSpark są preinstalowane w $SPARK _HOME/Jars. Te JARs są automatycznie ładowane podczas uruchamiania platformy Spark. Domyślnie platforma Spark używa danych na dysku lokalnym. 

Aby wystąpienie platformy Spark na DSVM uzyskać dostęp do danych przechowywanych w usłudze BLOB Storage lub Azure Data Lake Storage, należy utworzyć i skonfigurować plik `core-site.xml` na podstawie szablonu dostępnego w $SPARK _HOME/conf/pliku Core-site. XML. Template. Musisz również mieć odpowiednie poświadczenia, aby uzyskać dostęp do magazynu obiektów blob i Azure Data Lake Storage. (Należy pamiętać, że pliki szablonów używają symboli zastępczych dla usługi BLOB Storage i konfiguracji Azure Data Lake Storage).

Aby uzyskać szczegółowe informacje na temat tworzenia poświadczeń usługi Azure Data Lake Storage, zobacz [uwierzytelnianie przy użyciu Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Po wprowadzeniu poświadczeń dla magazynu obiektów blob lub Azure Data Lake Storage w pliku pliku Core-site. XML można odwoływać się do danych przechowywanych w tych źródłach za pomocą prefiksu URI wasb://lub adl://.

