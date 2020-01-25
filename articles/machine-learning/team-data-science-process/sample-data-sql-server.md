---
title: Przykładowe dane w programie SQL Server na platformie Azure — zespołu danych dla celów naukowych
description: Przykładowe dane przechowywane w programie SQL Server na platformie Azure przy użyciu bazy danych SQL lub języka programowania Python, a następnie przenieś go do usługi Azure Machine Learning.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717646"
---
# <a name="heading"></a>Przykładowe dane w programie SQL Server na platformie Azure

W tym artykule przedstawiono przykładowe dane przechowywane w programie SQL Server na platformie Azure przy użyciu SQL i języka programowania Python. Pokazano również, jak przenieść próbki danych do usługi Azure Machine Learning przez zapisanie go do pliku, przekazać go do obiektu blob platformy Azure i wczytaniem go do usługi Azure Machine Learning Studio.

Używa języka Python próbkowania [moduł pyodbc](https://code.google.com/p/pyodbc/) biblioteki ODBC, aby połączyć się z serwerem SQL na platformie Azure i [Pandas](https://pandas.pydata.org/) biblioteki w celu pobierania próbek.

> [!NOTE]
> Przykładowy kod SQL w tym dokumencie przyjęto założenie, że dane są w programie SQL Server na platformie Azure. Jeśli tak nie jest, zapoznaj się [przenoszenie danych do programu SQL Server na platformie Azure](move-sql-server-virtual-machine.md) artykuł, aby uzyskać instrukcje dotyczące sposobu przenoszenia danych do programu SQL Server na platformie Azure.
> 
> 

**Dlaczego przykładowe dane?**
Jeśli zestaw danych, która ma zostać analizowanie jest duża, zazwyczaj przyczyną jest dobrym pomysłem jest obniżenie częstotliwości próbkowania danych, aby zmniejszyć jego rozmiar mniejszy, ale reprezentatywny i łatwiejsze w zarządzaniu. Próbkowanie ułatwia zrozumienie, eksplorację i inżynierowanie danych. Jego rolę w [Team Data Science naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) jest umożliwienie szybkiego tworzenia prototypów funkcji do przetwarzania danych i modeli uczenia maszynowego.

To zadanie próbkowania jest krok [Team Data Science naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="SQL"></a>Przy użyciu języka SQL
W tej sekcji opisano kilka metod przy użyciu programu SQL, aby wykonać pobieranie próbek losowych względem danych w bazie danych. Wybierz metodę, w oparciu o rozmiar danych i jego dystrybucji.

Następujące dwa elementy pokazują, jak używać `newid` w programie SQL Server, aby wykonać pobieranie próbek. Wybór metody zależy od tego, jak losowo ma być pobierana próbka (pk_id w poniższym przykładowym kodzie jest założono, że jest automatycznie generowanym kluczem podstawowym).

1. Mniej rygorystyczne losowej próbki
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Więcej losowej próbki 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Tablesample może służyć do pobierania próbek danych, jak również. Ta opcja może być lepszym rozwiązaniem, jeśli rozmiar danych jest duży (przy założeniu, że dane na różnych stronach nie są skorelowane) i aby zapytanie zostało wykonane w rozsądnym czasie.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> Możesz eksplorować i generowania funkcji z tej próbki danych dzięki przechowywaniu go w nowej tabeli
> 
> 

### <a name="sql-aml"></a>Nawiązywanie połączenia z usługi Azure Machine Learning
Możesz bezpośrednio użyć przykładowych zapytań opisanych powyżej w module Azure Machine Learning [Import danych][import-data] , aby w pełni próbkować dane na bieżąco i przełączać je do eksperymentu Azure Machine Learning. Zrzut ekranu przedstawiający użycie modułu czytnika do odczytywania danych próbkowanych jest przedstawiony tutaj:

![Czytnik sql][1]

## <a name="python"></a>Przy użyciu języka programowania Python
W tej sekcji przedstawiono przy użyciu [biblioteki moduł pyodbc](https://code.google.com/p/pyodbc/) do nawiązania połączenia ODBC do bazy danych programu SQL server w języku Python. Parametry połączenia z bazą danych są następujące: (zastąp nazwę ServerName, dbname, username i Password swoją konfiguracją):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Pandas](https://pandas.pydata.org/) biblioteki w języku Python zawiera bogaty zestaw struktur danych i narzędzia do analizy danych do manipulowania danymi programowania Python. Poniższy kod odczytuje przykład 0,1% danych z tabeli w Azure SQL Database do danych Pandas:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)

Teraz możesz pracować z danymi próbkowane w ramce danych Pandas. 

### <a name="python-aml"></a>Nawiązywanie połączenia z usługi Azure Machine Learning
Następujący przykładowy kod umożliwia zapisanie danych próbkowana w dół do pliku i przekaż go do obiektu blob platformy Azure. Dane w obiekcie blob mogą być bezpośrednio odczytywane w Azure Machine Learning eksperymentu przy użyciu modułu [Importuj dane][import-data] . Dostępne są następujące czynności: 

1. Zapis pandas ramki danych do pliku lokalnego
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Przekaż plik lokalny do obiektów blob platformy Azure
   
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
3. Odczytaj dane z obiektu blob platformy Azure przy użyciu modułu Azure Machine Learning [Importuj dane][import-data] , jak pokazano na poniższym ekranie:

![czytnika obiektów blob][2]

## <a name="the-team-data-science-process-in-action-example"></a>Zespół danych dla celów naukowych w przykładzie akcji
Aby zapoznać się z przykładem procesu nauki o danych zespołowych przy użyciu publicznego zestawu danych, zobacz [proces analizy danych zespołu w akcji: używanie SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
