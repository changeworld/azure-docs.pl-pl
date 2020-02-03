---
title: Eksplorowanie danych na maszynie wirtualnej programu SQL Server — zespołu danych dla celów naukowych
description: Jak do eksplorowania danych, która jest przechowywana w maszynę Wirtualną programu SQL Server na platformie Azure przy użyciu bazy danych SQL lub języka programowania, takich jak Python.
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
ms.openlocfilehash: ae8c7c43ecbf9bc625e1e46be3e2c71c8d57b6f7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720099"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Eksplorowanie danych maszyny wirtualnej programu SQL Server na platformie Azure

W tym artykule opisano sposób eksplorowania danych, która jest przechowywana w maszynę Wirtualną programu SQL Server na platformie Azure. Użyj języka SQL lub Python, aby przeanalizować dane.

To zadanie jest krokiem w [procesie nauki danych zespołu](overview.md).

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
> W przypadku praktycznego przykładu można użyć [zestawu danych NYC taksówki](https://www.andresmh.com/nyctaxitrips/) i odwołać się do IPNB [NYC danych przetwarzanie przy użyciu notesu IPython i SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) w celu uzyskania kompleksowego przewodnika.
> 
> 

## <a name="python"></a>Eksplorowanie danych SQL przy użyciu języka Python
Używanie języka Python do eksplorowania danych i generowania funkcji, gdy dane są w SQL Server są podobne do przetwarzania danych w obiekcie blob platformy Azure przy użyciu języka Python, zgodnie z opisem w temacie [przetwarzanie danych obiektów blob platformy Azure w środowisku nauki danych](data-blob.md). Załaduj dane z bazy danych do Pandas Dataframe, a następnie można je przetworzyć w dalszej postaci. Udokumentowaliśmy proces łączenia z bazą danych i ładowania danych do elementów DataFrame w tej sekcji.

Następujący format parametrów połączenia może służyć do łączenia z bazą danych programu SQL Server za pomocą języka Python za pomocą moduł pyodbc (Zastąp servername, dbname, nazwę użytkownika i hasła o określonej wartości):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Biblioteka Pandas](https://pandas.pydata.org/) w języku Python oferuje bogaty zestaw struktur danych i narzędzi do analizy danych na potrzeby manipulowania danymi na potrzeby programowania w języku Python. Poniższy kod odczytuje wyniki zwracane z bazy danych programu SQL Server do ramki danych Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Teraz można korzystać z Pandas Dataframe, jak opisano w temacie [przetwarzanie danych obiektów blob platformy Azure w środowisku nauki danych](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Zespół danych dla celów naukowych w przykładzie akcji
Aby zapoznać się z kompleksowym przykładem procesu usługi Cortana Analytics przy użyciu publicznego zestawu danych, zobacz [proces nauka danych zespołu w akcji: używanie SQL Server](sql-walkthrough.md).

