---
title: Data Platform dla maszyny wirtualnej analizy danych — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o platformach danych i narzędzia obsługiwanych na maszynie wirtualnej do nauki o danych.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: a0fe4ee33769a6d8cf14f60dcc0c4615bb00c5e8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565302"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Obsługiwane na maszynie wirtualnej do nauki o danych platformy danych

Maszyna wirtualna do nauki o danych (DSVM) umożliwia tworzenie analizy przed szeroką gamę platform danych. Oprócz interfejsów platformy danych zdalnych maszyny DSVM udostępnia lokalne wystąpienie szybkie opracowywanie i tworzenia prototypów. 

Poniżej przedstawiono narzędziami platformy danych, które są obsługiwane na maszyny DSVM. 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| Co to jest?   | Wystąpienie lokalne relacyjnej bazy danych      |
| Wersje maszyny DSVM obsługiwane      | Windows      |
| Typowe zastosowania      | Szybkie opracowywanie lokalnie przy użyciu mniejszy zestaw danych <br/> Uruchamiaj język R w bazie danych   |
| Zawiera linki do przykładów      |    Małą próbkę New York City zestaw danych jest ładowany do bazy danych SQL `nyctaxi`. <br/> Przykładowe Jupyter przedstawiający Microsoft R i analiza w bazie danych można znaleźć w folderze:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Pokrewne narzędzia na maszyny DSVM       | SQL Server Management Studio <br/> Sterowniki ODBC/JDBC<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> SQL Server 2016 developer edition należy używać tylko do tworzenia i testowania. Potrzebujesz licencji lub jednej z maszyn wirtualnych serwera SQL, aby go uruchomić w środowisku produkcyjnym. 


### <a name="setup"></a>Konfigurowanie

Serwer bazy danych już jest wstępnie skonfigurowana i usługi Windows powiązane z programu SQL Server (takich jak `SQL Server (MSSQLSERVER)`) są ustawione do automatycznego uruchamiania. Tylko ręcznie krok, aby uruchomić jest umożliwienie analytics w bazie danych przy użyciu Microsoft R. Można to zrobić, uruchamiając następujące polecenie, jeden raz akcję w SQL Server Management Studio (SSMS) po zalogowaniu jako administrator komputera Open "Nowe zapytanie" w programie SSMS, upewnij się, wybranej bazy danych `master` , a następnie uruchom: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
Aby można było uruchomić program SQL Server Management Studio, możesz wyszukać "SQL Server Management Studio" na liście programu lub użyj Windows Search, aby znaleźć i uruchomić go. Po wyświetleniu monitu o poświadczenia, wybierz opcję "Uwierzytelnianie Windows" i użyć nazwy komputera lub ```localhost``` w polu Nazwa serwera SQL. 

### <a name="how-to-use--run-it"></a>Jak używać / ją uruchomić?  

Serwer bazy danych z domyślnym wystąpieniem bazy danych działa automatycznie domyślnie. Dostęp do programu SQL Server bazy danych lokalnie, można użyć narzędzi, takich jak SQL Server Management Studio na maszynie Wirtualnej. Administratorzy lokalni konto ma dostęp administratora w bazie danych. 

NAUKI zawiera także sterowniki ODBC — sterowniki i sterownika JDBC na komunikowanie się z programem SQL Server, w przypadku baz danych Azure SQL i Azure SQL Data Warehouse z aplikacji napisanych w wielu językach, w tym języków Python i R. 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM? 

Program SQL Server jest zainstalowany w sposób standardowy. Znajduje się w temacie `C:\Program Files\Microsoft SQL Server`. Wystąpienie bazy danych w R znajduje się na `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. Maszyny DSVM ma również wystąpienia oprogramowania R Server oddzielne autonomicznego zainstalowane w `C:\Program Files\Microsoft\R Server\R_SERVER`. Te wystąpienia dwóch R nie należy udostępniać biblioteki.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (autonomiczny)

| | |
| ------------- | ------------- |
| Co to jest?   | Wystąpienia autonomicznego (jeden węzeł w procesie) popularne platformy Apache Spark, system szybko na dużą skalę przetwarzania danych i uczenia maszynowego     |
| Wersje maszyny DSVM obsługiwane      | Linux <br /> Windows (wersja eksperymentalna)      |
| Typowe zastosowania      | * Szybkiego opracowywania aplikacji Spark/PySpark lokalnie za pomocą mniejszy zestaw danych i później wdrożyć ją w dużych klastrach platformy Spark, takich jak Azure HDInsight<br/> * Testu kontekstu aparatu Spark usługi Microsoft R Server <br />* Użyj typu open source SparkML lub firmy Microsoft [MMLSpark](https://github.com/Azure/mmlspark) biblioteki do kompilowania aplikacji uczenia Maszynowego  |
| Zawiera linki do przykładów      |    Przykład programu Jupyter. <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft R Server (kontekstu aparatu Spark): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Pokrewne narzędzia na maszyny DSVM       | PySpark, Scala<br/>Jupyter (Spark/PySpark jądra)<br/>Microsoft R Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Jak z niej korzystać
Można uruchomić aparatu Spark za przesyłania zadań platformy Spark w wierszu polecenia za pomocą `spark-submit` lub `pyspark` poleceń. Można również utworzyć notesu programu Jupyter, tworząc nowy notes o jądrze platformy Spark. 

Możesz użyć platformy Spark z języka R przy użyciu bibliotek, takich jak SparkR, Sparklyr lub Microsoft R Server, które są dostępne na maszyny DSVM. Zobacz łącza do przykładów w powyższej tabeli. 

> [!NOTE]
> Uruchamianie oprogramowania Microsoft R Server w kontekście aparatu Spark wersje maszyny wirtualnej jest obsługiwana tylko w wersji Ubuntu Linux maszyny wirtualnej DSVM. 



### <a name="setup"></a>Konfigurowanie
Przed uruchomieniem w kontekście platformy Spark w programie Microsoft R Server w wersji Ubuntu Linux maszyny wirtualnej DSVM, należy ją wykonać jeden raz, Instalator kroku umożliwiające pojedynczego węzła lokalnego wystąpienia HDFS, Hadoop i Yarn. Domyślnie usługi Hadoop jest zainstalowany, ale wyłączone na maszyny DSVM. Aby ją włączyć, należy uruchom następujące polecenia jako głównego po raz pierwszy:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Hadoop można zatrzymać związane z usług, kiedy nie potrzebują, uruchamiając ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` próbkę ukazujące sposób tworzenia i testowania PANI w zdalnym kontekstu aparatu Spark (czyli wystąpieniu platformy Spark autonomiczne maszyny DSVM) są dostępne w `/dsvm/samples/MRS` katalog. 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Jak jest ona skonfigurowana / zainstalowanym maszyny DSVM? 
|Platforma|Lokalizacja_instalacji ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Biblioteki, aby uzyskać dostęp do danych z obiektów Blob platformy Azure lub usługi Azure Data Lake storage (ADLS) i za pomocą biblioteki MMLSpark uczenia maszynowego firmy Microsoft są wstępnie zainstalowane w plikach SPARK_HOME $/ JAR. Tych plikach JAR są ładowane automatycznie podczas uruchamiania aparatu Spark. Domyślnie platforma Spark korzysta z danych na dysku lokalnym. Aby wystąpienie platformy Spark na DSVM dostępu do danych przechowywanych w usłudze Azure blob lub Azure Data Lake Store należy utworzyć skonfigurować `core-site.xml` pliku na podstawie szablonu w $SPARK_HOME/conf/core-site.xml.template (tam, gdzie jest symboli zastępczych dla obiektów Blob i Azure Data Lake Store konfiguracje) przy użyciu odpowiednich poświadczeń do obiektów blob platformy Azure i usługi Azure Data Lake Storage. Możesz znaleźć bardziej szczegółowe instrukcje dotyczące tworzenia poświadczeń usługi Azure Data Lake store [tutaj](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Po wprowadzeniu poświadczeń dla obiektów blob platformy Azure lub usługi ADLS w pliku core-site.xml możesz odwoływać się do danych przechowywanych w tych źródłach z prefiksem identyfikatora URI wasb: / / lub systemu plików adl: / /. 

