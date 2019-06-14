---
title: Eksplorowanie danych na maszynie wirtualnej programu SQL Server — zespołu danych dla celów naukowych
description: Jak do eksplorowania danych, która jest przechowywana w maszynę Wirtualną programu SQL Server na platformie Azure przy użyciu bazy danych SQL lub języka programowania, takich jak Python.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: be75490e4e86956337ce38133df6095790b3a374
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60303659"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Eksplorowanie danych maszyny wirtualnej programu SQL Server na platformie Azure

W tym artykule opisano sposób eksplorowania danych, która jest przechowywana w maszynę Wirtualną programu SQL Server na platformie Azure. Można to zrobić za dane inteligencji przy użyciu języka SQL lub przy użyciu języka programowania, takich jak Python.

To zadanie jest to krok w [zespołu danych dla celów naukowych](overview.md).

> [!NOTE]
> Przykładowe instrukcje SQL, w tym dokumencie przyjęto założenie, że dane są w programie SQL Server. Jeśli nie, można znaleźć mapy procesu do nauki o danych chmury dowiesz się, jak przenieść dane do programu SQL Server.
> 
> 

## <a name="sql-dataexploration"></a>Eksplorowanie danych SQL za pomocą skryptów SQL
Poniżej przedstawiono kilka przykładowe skrypty SQL, których można użyć, aby zapoznać się z magazynami danych w programie SQL Server.

1. Zliczanie uwagi na dzień
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Pobierz poziomy w kolumnie podzielonych na kategorie
   
    `select  distinct <column_name> from <databasename>`
3. Pobierz liczbę poziomów w połączeniu z dwóch kolumn podzielonych na kategorie 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Rozpoczynanie dystrybucji dla kolumny liczbowe
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Na przykład praktycznych, możesz użyć [zestawu danych taksówek NYC](https://www.andresmh.com/nyctaxitrips/) i odnoszą się do IPNB pod tytułem [danych NYC inteligencji przy użyciu IPython Notebook i programu SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) dla przewodnika end-to-end.
> 
> 

## <a name="python"></a>Eksplorowanie danych SQL za pomocą języka Python
Przy użyciu języka Python, aby eksplorować dane i wygenerować funkcje, gdy dane znajdują się w programie SQL Server jest podobny do przetwarzania danych w usłudze Azure blob przy użyciu języka Python, zgodnie z opisem w [danych obiektów Blob platformy Azure proces w danym środowisku do nauki o danych](data-blob.md). Dane powinna być załadowana z bazy danych do pandas DataFrame i następnie mogą być dalej przetwarzane. Udokumentowaliśmy proces łączenia z bazą danych i ładowania danych do elementów DataFrame w tej sekcji.

Następujący format parametrów połączenia może służyć do łączenia z bazą danych programu SQL Server za pomocą języka Python za pomocą moduł pyodbc (Zastąp servername, dbname, nazwę użytkownika i hasła o określonej wartości):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Biblioteki Pandas](https://pandas.pydata.org/) w języku Python zawiera bogaty zestaw struktur danych i narzędzia do analizy danych do manipulowania danymi programowania Python. Poniższy kod odczytuje wyniki zwracane z bazy danych programu SQL Server do ramki danych Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Teraz możesz pracować z biblioteki Pandas DataFrame zgodnie z opisem w temacie [danych obiektów Blob platformy Azure proces w danym środowisku do nauki o danych](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Zespół danych dla celów naukowych w przykładzie akcji
Aby procesie Cortana Analytics, za pomocą publicznego zestawu danych, na przykład end-to-end wskazówki, zobacz [zespołu danych dla celów naukowych w działaniu: przy użyciu programu SQL Server](sql-walkthrough.md).

