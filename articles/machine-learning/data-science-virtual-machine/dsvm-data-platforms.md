---
title: Data Platform dla danych nauki maszyny wirtualnej - Azure | Dokumentacja firmy Microsoft
description: Data Platform dla maszyny wirtualnej analizy danych.
keywords: narzędzia do analizy danych, maszyny wirtualnej analizy danych, narzędzia do analizy danych, nauki danych systemu linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: b3f340006801287383c2afb2924706affbd77a51
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="data-platforms"></a>Platformy danych

Maszyna wirtualna nauki danych (DSVM) umożliwia tworzenie użytkownika analytics przed różnorodnym platformy danych. Oprócz interfejsów dla platform zdalnych danych DSVM zawiera lokalne wystąpienie szybkie opracowywanie i tworzenie prototypów. 

Poniżej zostały wymienione narzędzia platformy danych obsługiwane w DSVM. 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| Co to jest?   | Wystąpienia lokalne relacyjnej bazy danych      |
| DSVM obsługiwane wersje      | Windows      |
| Typowe zastosowania      | Szybkie opracowywanie lokalnie z mniejszym zestawu danych <br/> Uruchom R w bazie danych   |
| Łącza do próbek      |    Próbki tego zestawu danych z nowego Jorku jest ładowany do bazy danych SQL `nyctaxi`. <br/> Przykładowe Jupyter przedstawiający Microsoft R i analiza w bazie danych można znaleźć w folderze:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Pokrewne narzędzia na DSVM       | SQL Server Management Studio <br/> Sterowniki ODBC/JDBC<br/> pyodbc, RODBC<br />Apache Przechodzenie do szczegółów      |

> [!NOTE]
> Edycja dewelopera programu SQL Server 2016 służy tylko do prac deweloperskich i celów testowych. Potrzebujesz licencji lub jednej z maszyn wirtualnych serwera SQL go uruchomić w środowisku produkcyjnym. 


### <a name="setup"></a>Konfiguracja

Serwer bazy danych jest już wstępnie skonfigurowany i usług systemu Windows związanych z programem SQL Server (takie jak `SQL Server (MSSQLSERVER)`) są ustawione na uruchamianie automatyczne. Tylko ręcznego kroku uruchamiana jest umożliwienie analytics w bazie danych przy użyciu R. firmy Microsoft Można to zrobić, uruchamiając następujące polecenia jeden raz akcję w programu SQL Server Management Studio (SSMS) po zalogowaniu jako administrator komputera Open "Nowe zapytanie" w programie SSMS, upewnij się, wybranej bazy danych `master` , a następnie uruchom: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
Uruchomienie programu SQL Server Management Studio, można wyszukać "Programu SQL Server Management Studio" na liście programu, lub Użyj wyszukiwania systemu Windows znajdź i uruchom je. Po wyświetleniu monitu o poświadczenia, wybierz opcję "Uwierzytelnianie systemu Windows" i użyj nazwy komputera lub ```localhost``` w polu Nazwa serwera SQL. 

### <a name="how-to-use--run-it"></a>Jak używać / uruchom go?  

Serwer bazy danych z domyślnego wystąpienia bazy danych działa automatycznie domyślnie. Narzędzia, takie jak SQL Server Management Studio na maszynie Wirtualnej umożliwia dostęp do programu SQL Server bazy danych lokalnie. Administratorzy lokalni konto ma uprawnienia administratora w bazie danych. 

DSVM dołączany sterowniki ODBC — sterowniki i JDBC, aby komunikował się z programu SQL Server, w przypadku baz danych Azure SQL i magazyn danych SQL Azure z aplikacji napisanych w wielu językach, łącznie z języka Python, R. 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Jak jest on skonfigurowany / zainstalowanym DSVM? 

Serwer SQL jest zainstalowany w sposób standardowy. Można je znaleźć w `C:\Program Files\Microsoft SQL Server`. Wystąpienie bazy danych w R znajduje się na `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. DSVM ma również oddzielne autonomicznego wystąpienia R Server zainstalowany w `C:\Program Files\Microsoft\R Server\R_SERVER`. Wystąpienia te dwa R nie udostępniaj biblioteki.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (autonomiczna)

| | |
| ------------- | ------------- |
| Co to jest?   | Wystąpienia autonomicznego (jednego węzła w procesie) popularnych platformy Apache Spark, system dużych szybkiego przetwarzania danych i uczenia maszynowego     |
| DSVM obsługiwane wersje      | Linux <br /> Systemu Windows (eksperymentalne)      |
| Typowe zastosowania      | * Szybkiego opracowywania aplikacji Spark/PySpark lokalnie z mniejszym zestawu danych lub nowszym należy ją wdrożyć na dużych klastry Spark, takich jak Azure HDInsight<br/> * Testów Microsoft R Server Spark kontekstu <br />* Użyj typu open source SparkML lub firmy Microsoft [MMLSpark](https://github.com/Azure/mmlspark) biblioteki, aby tworzyć aplikacje ML  |
| Łącza do próbek      |    Przykład Jupyter: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Serwer R firmy Microsoft (Spark kontekst): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Pokrewne narzędzia na DSVM       | PySpark, Scala<br/>Jupyter (jądra Spark/PySpark)<br/>Microsoft R Server, SparkR, Sparklyr <br />Apache Przechodzenie do szczegółów      |

### <a name="how-to-use-it"></a>Jak z niego korzystać
Możesz uruchamiać Spark, przesyłania zadań Spark w wierszu polecenia z `spark-submit` lub `pyspark` poleceń. Tworząc nowy notes o jądrze Spark można również utworzyć notesu Jupyter. 

Możesz użyć Spark z przy użyciu bibliotek jak SparkR, Sparklyr lub Microsoft R Server, które są dostępne na DSVM R. Zobacz wskaźniki do próbek w powyższej tabeli. 

> [!NOTE]
> Programem Microsoft R Server w kontekście Spark DSVM jest obsługiwana tylko w wersji Ubuntu Linux DSVM. 



### <a name="setup"></a>Konfiguracja
Przed uruchomieniem w kontekście Spark Microsoft R Server w wersji Ubuntu Linux DSVM, należy wykonać jeden raz, Instalator krok do włączenia lokalnego węzła pojedynczego wystąpienia systemu plików HDFS Hadoop i Yarn. Domyślnie usługi Hadoop są zainstalowane, ale na DSVM wyłączony. Aby go włączyć, należy uruchomić następujące polecenia jako główny po raz pierwszy:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Można zatrzymać usługi Hadoop usług związanych z, jeśli nie muszą, uruchamiając ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```` próbkę pokazująca, jak umożliwiające opracowanie i przetestowanie PANI w zdalnym kontekście Spark (czyli wystąpienia Spark autonomicznego na DSVM) są dostępne w `/dsvm/samples/MRS` katalog. 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Jak jest on skonfigurowany / zainstalowanym DSVM? 
|Platforma|Zainstaluj lokalizacji ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Biblioteki, aby uzyskać dostęp do danych z obiektów Blob platformy Azure lub usługi Azure Data Lake magazyn (ADLS) i korzystanie z bibliotek usługi machine learning MMLSpark firmy Microsoft są wstępnie zainstalowane w SPARK_HOME $/ słoików. Te słoików są ładowane automatycznie podczas uruchamiania Spark. Domyślnie Spark używa danych na dysku lokalnym. Aby wystąpienia Spark na DSVM uzyskują dostęp do danych przechowywanych na obiektów blob platformy Azure lub ADLS należy utworzyć skonfigurować `core-site.xml` plik na podstawie szablonu w $SPARK_HOME/conf/core-site.xml.template (gdzie symboli zastępczych dla obiekt Blob i ADLS konfiguracje) za pomocą odpowiednich poświadczeń do obiektów blob platformy Azure i usługi Azure Data Lake Storage. Możesz znaleźć szczegółowe kroki dotyczące tworzenia poświadczeń usługi ADLS [tutaj](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory#create-an-active-directory-application). Po wprowadzeniu poświadczeń dla obiektów blob platformy Azure lub ADLS w pliku core-site.xml można odwoływać się do danych przechowywanych w tych źródeł z Prefiks URI wasb: / / lub adl: / /. 

