---
title: Obsługiwane platformy danych
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się więcej na temat obsługiwanych platform i narzędzi danych dla Data Science Virtual Machine platformy Azure.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: bfae8147c348c76fa0e406fec283144ebc26e86b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270110"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Obsługiwane na maszynie wirtualnej do nauki o danych platformy danych

Za pomocą Data Science Virtual Machine (DSVM) można skompilować analizę na szeroką gamę platform danych. Oprócz interfejsów platformy danych zdalnych maszyny DSVM udostępnia lokalne wystąpienie szybkie opracowywanie i tworzenia prototypów.

Na DSVM są obsługiwane następujące narzędzia platformy danych.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| | |
| ------------- | ------------- |
| Co to jest?   | Wystąpienie lokalne relacyjnej bazy danych      |
| Obsługiwane wersje DSVM      | Windows: SQL Server 2017, Windows 2019 (wersja zapoznawcza): SQL Server 2019      |
| Typowe zastosowania      | Szybkie opracowywanie lokalnie przy użyciu mniejszy zestaw danych <br/> Uruchamiaj język R w bazie danych   |
| Zawiera linki do przykładów      |    Mały przykład zestawu danych w Nowym Jorku jest ładowany do bazy danych SQL:<br/>  `nyctaxi` <br/> Przykład Jupyter z informacjami o Microsoft Machine Learning Server i analizie w bazie danych można znaleźć pod adresem:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Narzędzia pokrewne na DSVM       | SQL Server Management Studio <br/> Sterowniki ODBC/JDBC<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> Wersji SQL Server Developer można używać tylko w celach deweloperskich i testowych. Potrzebujesz licencji lub jednej z maszyn wirtualnych serwera SQL, aby go uruchomić w środowisku produkcyjnym.


### <a name="setup"></a>Konfigurowanie

Serwer bazy danych jest już wstępnie skonfigurowany i usługi systemu Windows powiązane z SQL Server (takie jak `SQL Server (MSSQLSERVER)`) są skonfigurowane do automatycznego uruchamiania. Jedyny krok ręczny obejmuje włączenie analizy w bazie danych przy użyciu Microsoft Machine Learning Server. Możesz włączyć funkcję analizy, uruchamiając następujące polecenie jako jednorazową akcję w SQL Server Management Studio (SSMS). Uruchom to polecenie po zalogowaniu się jako administrator komputera, Otwórz nowe zapytanie w programie SSMS i upewnij się, że wybrana baza danych jest `master`:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Aby uruchomić SQL Server Management Studio, można wyszukać ciąg "SQL Server Management Studio" na liście programów lub użyć usługi Windows Search w celu znalezienia i uruchomienia. Po wyświetleniu monitu o poświadczenia wybierz pozycję **uwierzytelnianie systemu Windows** i użyj nazwy maszyny lub ```localhost``` w polu **nazwa SQL Server** .

### <a name="how-to-use-and-run-it"></a>Jak używać i uruchamiać

Domyślnie serwer bazy danych z domyślnym wystąpieniem bazy danych jest uruchamiany automatycznie. Dostęp do programu SQL Server bazy danych lokalnie, można użyć narzędzi, takich jak SQL Server Management Studio na maszynie Wirtualnej. Konta administratorów lokalnych mają dostęp administratora do bazy danych programu.

Ponadto DSVM zawiera sterowniki ODBC i JDBC, aby komunikować się z SQL Server, bazami danych SQL Azure i Azure SQL Data Warehouse z aplikacji utworzonych w wielu językach, w tym Python i Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Jak została skonfigurowana i zainstalowana na DSVM? 

 SQL Server jest zainstalowany w standardowy sposób. Można go znaleźć w `C:\Program Files\Microsoft SQL Server`. Wystąpienie Machine Learning Server w bazie danych znajduje się w `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. DSVM ma również oddzielne autonomiczne wystąpienie Machine Learning Server, które jest instalowane w `C:\Program Files\Microsoft\R Server\R_SERVER`. Te dwa wystąpienia Machine Learning Server nie współdzielą bibliotek.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (autonomiczny)

| | |
| ------------- | ------------- |
| Co to jest?   | Autonomiczne (Jednowęzłowe wystąpienie w procesie) popularnej platformy Apache Spark; System szybkiego i uczenia maszynowego na dużą skalę     |
| Obsługiwane wersje DSVM      | Linux     |
| Typowe zastosowania      | * Szybkie opracowywanie aplikacji platformy Spark/PySpark lokalnie z mniejszym zestawem danych i późniejszym wdrożeniem w dużych klastrach Spark, takich jak usługa Azure HDInsight<br/> * Test Microsoft Machine Learning Server — kontekst platformy Spark <br />* Użyj SparkML lub biblioteki [MMLSpark](https://github.com/Azure/mmlspark) Open Source firmy Microsoft do kompilowania aplikacji ml |
| Zawiera linki do przykładów      |    Przykład programu Jupyter. <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft Machine Learning Server (kontekst Spark):/dsvm/samples/MRS/MRSSparkContextSample.R |
| Narzędzia pokrewne na DSVM       | PySpark, Scala<br/>Jupyter (Spark/PySpark jądra)<br/>Microsoft Machine Learning Server, Spark, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Jak z niej korzystać
Zadania platformy Spark można przesłać w wierszu polecenia, uruchamiając polecenie `spark-submit` lub `pyspark`. Można również utworzyć notesu programu Jupyter, tworząc nowy notes o jądrze platformy Spark.

Platformy Spark można użyć z języka R, korzystając z bibliotek takich jak Spark, Sparklyr i Microsoft Machine Learning Server, które są dostępne w DSVM. Zobacz łącza do przykładów w powyższej tabeli.

### <a name="setup"></a>Konfigurowanie
Przed uruchomieniem w kontekście platformy Spark w Microsoft Machine Learning Server na Ubuntu Linux DSVM Edition należy wykonać jednorazowy krok konfiguracji, aby włączyć lokalny pojedynczy węzeł Hadoop HDFS i wystąpienie przędzenia. Domyślnie usługi Hadoop jest zainstalowany, ale wyłączone na maszyny DSVM. Aby je włączyć, uruchom następujące polecenia jako główne po raz pierwszy:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Usługi związane z usługą Hadoop można zatrzymać, gdy nie są już potrzebne, uruchamiając ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```.

Przykład, który pokazuje, jak opracowywać i testować w zdalnym kontekście Spark (który jest autonomicznym wystąpieniem platformy Spark na DSVM), jest udostępniany i dostępny w katalogu `/dsvm/samples/MRS`.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Jak została skonfigurowana i zainstalowana na DSVM? 
|Platforma|Lokalizacja_instalacji ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Biblioteki umożliwiające dostęp do danych z usługi Azure Blob Storage lub Azure Data Lake Storage przy użyciu bibliotek uczenia maszynowego programu Microsoft MMLSpark są preinstalowane w $SPARK _HOME/Jars. Tych plikach JAR są ładowane automatycznie podczas uruchamiania aparatu Spark. Domyślnie platforma Spark używa danych na dysku lokalnym. 

Aby wystąpienie platformy Spark na DSVM uzyskać dostęp do danych przechowywanych w usłudze BLOB Storage lub Azure Data Lake Storage, należy utworzyć i skonfigurować plik `core-site.xml` na podstawie szablonu znalezionego w $SPARK _HOME/conf/Core-site.XML.Template. Musisz również mieć odpowiednie poświadczenia, aby uzyskać dostęp do magazynu obiektów blob i Azure Data Lake Storage. (Należy pamiętać, że pliki szablonów używają symboli zastępczych dla usługi BLOB Storage i konfiguracji Azure Data Lake Storage).

Aby uzyskać szczegółowe informacje na temat tworzenia poświadczeń usługi Azure Data Lake Storage, zobacz [uwierzytelnianie przy użyciu Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Po wprowadzeniu poświadczeń dla magazynu obiektów blob lub Azure Data Lake Storage w pliku pliku Core-site. XML można odwoływać się do danych przechowywanych w tych źródłach za pomocą prefiksu URI wasb://lub adl://.

