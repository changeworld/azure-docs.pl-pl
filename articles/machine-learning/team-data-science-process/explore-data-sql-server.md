---
title: Eksploruj dane na maszynie wirtualnej programu SQL Server — proces nauki o danych zespołu
description: Jak eksplorować dane, które są przechowywane w maszynie Wirtualnej programu SQL Server na platformie Azure przy użyciu języka SQL lub języka programowania, takiego jak Python.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720099"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Eksplorowanie danych maszyny wirtualnej programu SQL Server na platformie Azure

W tym artykule opisano, jak eksplorować dane, które są przechowywane w maszynie wirtualnej programu SQL Server na platformie Azure. Użyj SQL lub Python do zbadania danych.

To zadanie jest krokiem w [procesie nauki o danych zespołu.](overview.md)

> [!NOTE]
> Przykładowe instrukcje SQL w tym dokumencie zakładają, że dane są w programie SQL Server. Jeśli tak nie jest, zapoznaj się z mapą procesu nauki o danych w chmurze, aby dowiedzieć się, jak przenieść dane do programu SQL Server.
> 
> 

## <a name="explore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>Eksplorowanie danych SQL za pomocą skryptów SQL
Oto kilka przykładowych skryptów SQL, które mogą służyć do eksplorowania magazynów danych w programie SQL Server.

1. Uzyskaj liczbę obserwacji dziennie
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Pobierz poziomy w kolumnie kategorycznej
   
    `select  distinct <column_name> from <databasename>`
3. Uzyskaj liczbę poziomów w połączeniu z dwiema kolumnami kategorycznymi 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Pobierz rozkład kolumn numerycznych
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Na przykład praktyczny można użyć [zestawu danych taksówki NYC](https://www.andresmh.com/nyctaxitrips/) i odwołać się do ipnb o nazwie NYC Data [wrangling przy użyciu notesu IPython i programu SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) dla end-to-end przewodnika.
> 
> 

## <a name="explore-sql-data-with-python"></a><a name="python"></a>Eksplorowanie danych SQL za pomocą języka Python
Korzystanie z języka Python do eksplorowania danych i generowania funkcji, gdy dane znajdują się w programie SQL Server, jest podobne do przetwarzania danych w środowisku blob platformy Azure przy użyciu języka Python, zgodnie z dokumentami w [środowisku przetwarzania obiektów blob platformy Azure w środowisku do nauki o danych.](data-blob.md) Załaduj dane z bazy danych do pandas DataFrame, a następnie mogą być przetwarzane dalej. Dokumentujemy proces łączenia się z bazą danych i ładowania danych do DataFrame w tej sekcji.

Następujący format ciągu połączenia może służyć do łączenia się z bazą danych programu SQL Server z języka Python przy użyciu pyodbc (zastąp nazwę serwera, nazwę dbname, nazwę użytkownika i hasło określonymi wartościami):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Biblioteka Pandas](https://pandas.pydata.org/) w Pythonie zawiera bogaty zestaw struktur danych i narzędzi do analizy danych do manipulowania danymi podczas programowania języka Python. Poniższy kod odczytuje wyniki zwrócone z bazy danych programu SQL Server do ramki danych Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Teraz możesz pracować z Pandas DataFrame zgodnie z tematem [Przetwarzanie danych obiektów blob platformy Azure w środowisku nauki o danych.](data-blob.md)

## <a name="the-team-data-science-process-in-action-example"></a>Przykład procesu nauki o danych zespołu w przykładzie akcji
Aby zapoznać się z kompleksowym przykładem szczegółowego procesu analizy Cortany przy użyciu publicznego zestawu danych, zobacz [Proces nauki o danych zespołu w akcji: przy użyciu programu SQL Server](sql-walkthrough.md).

