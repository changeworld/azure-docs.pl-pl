---
title: Przykładowe dane w programie SQL Server na platformie Azure — proces nauki o danych zespołu
description: Przykładowe dane przechowywane w programie SQL Server na platformie Azure przy użyciu języka programowania SQL lub języka języka python, a następnie przenieść go do usługi Azure Machine Learning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 71a2ec9dc4d644fb8739db3817e2cd1d09913da7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717646"
---
# <a name="sample-data-in-sql-server-on-azure"></a><a name="heading"></a>Przykładowe dane w programie SQL Server na platformie Azure

W tym artykule pokazano, jak przykładać danych przechowywanych w programie SQL Server na platformie Azure przy użyciu języka programowania SQL lub języka programowania Języka Python. Pokazano również, jak przenieść przykładowe dane do usługi Azure Machine Learning, zapisując je w pliku, przekazując je do obiektu blob platformy Azure, a następnie odczytując je w usłudze Azure Machine Learning Studio.

Próbkowanie języka Python używa [biblioteki Pyodbc](https://code.google.com/p/pyodbc/) ODBC do łączenia się z programem SQL Server na platformie Azure i [biblioteki Pandas](https://pandas.pydata.org/) w celu pobrania próbkowania.

> [!NOTE]
> Przykładowy kod SQL w tym dokumencie zakłada, że dane są w programie SQL Server na platformie Azure. Jeśli tak nie jest, zapoznaj się [z artykule Przenoszenie danych do programu SQL Server na platformie Azure,](move-sql-server-virtual-machine.md) aby uzyskać instrukcje dotyczące przenoszenia danych do programu SQL Server na platformie Azure.
> 
> 

**Dlaczego warto przykładać swoje dane?**
Jeśli zestaw danych, który planujesz analizować, jest duży, zwykle warto pobrać próbkę w dół danych, aby zmniejszyć je do mniejszego, ale reprezentatywnego i łatwiejszego w zarządzaniu rozmiaru. Próbkowanie ułatwia zrozumienie danych, eksplorację i inżynierię funkcji. Jego rolą w [procesie nauki o danych zespołu (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) jest umożliwienie szybkiego prototypowania funkcji przetwarzania danych i modeli uczenia maszynowego.

To zadanie próbkowania jest krokiem w [procesie nauki o danych zespołu (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

## <a name="using-sql"></a><a name="SQL"></a>Korzystanie z języka SQL
W tej sekcji opisano kilka metod przy użyciu języka SQL do wykonywania prostego losowego próbkowania danych w bazie danych. Wybierz metodę na podstawie rozmiaru danych i ich dystrybucji.

Następujące dwa elementy pokazują, `newid` jak używać w programie SQL Server do wykonywania próbkowania. Wybrana metoda zależy od losowej, jak ma być próbka (pk_id w poniższym przykładowym kodzie jest zakładana jako automatycznie generowany klucz podstawowy).

1. Mniej surowa próbka losowa
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Bardziej losowa próbka 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Tablesample może służyć do pobierania próbek danych, jak również. Ta opcja może być lepszym rozwiązaniem, jeśli rozmiar danych jest duży (przy założeniu, że dane na różnych stronach nie są skorelowane) i aby kwerenda została ukończona w rozsądnym czasie.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> Funkcje z tych próbkowania można eksplorować i generować, przechowując je w nowej tabeli
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Łączenie się z usługą Azure Machine Learning
Możesz bezpośrednio użyć przykładowych zapytań powyżej w module [importu usługi][import-data] Azure Machine Learning, aby pobrać próbkę w dół danych w locie i przenieść je do eksperymentu usługi Azure Machine Learning. Zrzut ekranu przedstawiający użycie modułu czytnika do odczytu próbkowania danych jest pokazany tutaj:

![czytnik sql][1]

## <a name="using-the-python-programming-language"></a><a name="python"></a>Korzystanie z języka programowania Języka Python
W tej sekcji pokazano przy użyciu [biblioteki pyodbc](https://code.google.com/p/pyodbc/) do ustanowienia ODBC połączyć się z bazy danych serwera SQL w języku Python. Parametry połączenia bazy danych są następujące: (zastąp nazwę serwera, nazwę użytkownika i hasło konfiguracją):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Biblioteka [Pandas](https://pandas.pydata.org/) w Pythonie zawiera bogaty zestaw struktur danych i narzędzi do analizy danych do manipulowania danymi podczas programowania języka Python. Poniższy kod odczytuje próbkę 0,1% danych z tabeli w bazie danych SQL azure do danych pandas:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)

Teraz można pracować z próbkowania danych w ramce danych Pandas. 

### <a name="connecting-to-azure-machine-learning"></a><a name="python-aml"></a>Łączenie się z usługą Azure Machine Learning
Poniższy przykładowy kod służy do zapisywania danych próbkowanych w dół do pliku i przekazywania go do obiektu blob platformy Azure. Dane w obiekcie blob można bezpośrednio odczytać w eksperymentalnym usłudze Azure Machine Learning przy użyciu modułu [Importuj dane.][import-data] Kroki tego procesu są następujące: 

1. Zapisywanie ramki danych pand do pliku lokalnego
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Przekazywanie pliku lokalnego do obiektu blob platformy Azure
   
        from azure.storage import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. Odczytuj dane z obiektu blob platformy Azure przy użyciu [modułu importu usługi][import-data] Azure Machine Learning, jak pokazano na poniższym ekranie:

![obiekt blob czytnika][2]

## <a name="the-team-data-science-process-in-action-example"></a>Przykład procesu nauki o danych zespołu w akcji
Aby przejść przez przykład procesu nauki o danych zespołu przy użyciu publicznego zestawu danych, zobacz [Proces nauki o danych zespołu w akcji: przy użyciu programu SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
