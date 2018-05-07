---
title: Eksplorowanie danych w maszynie wirtualnej serwera SQL na platformie Azure | Dokumentacja firmy Microsoft
description: Jak eksplorować dane przechowywane na maszynie Wirtualnej z programu SQL Server na platformie Azure.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ccbb3085-af9e-4ec2-9df2-15dcab261d05
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: 68c8386917599afcf2819ff97453de77dea90215
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Eksplorowanie danych maszyny wirtualnej programu SQL Server na platformie Azure
W tym dokumencie opisano sposób eksplorować dane przechowywane na maszynie Wirtualnej z programu SQL Server na platformie Azure. Można to zrobić przy użyciu języka programowania, takich jak Python lub wrangling danych przy użyciu programu SQL.

Następujące **menu** linki do tematów opisujących sposób użycia narzędzia, aby eksplorować dane w różnych środowiskach magazynu. To zadanie jest krokiem w procesie Analytics Cortana (CAP).

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

> [!NOTE]
> Instrukcje SQL próbki w tym dokumencie założono, że dane są w programie SQL Server. Jeśli nie, zajrzyj do mapy procesu chmury danych nauki, aby dowiedzieć się, jak przenieść dane do programu SQL Server.
> 
> 

## <a name="sql-dataexploration"></a>Eksplorowanie danych SQL za pomocą skryptów SQL
Poniżej przedstawiono kilka przykładowe skrypty SQL, które mogą służyć do eksplorowania magazyny danych w programie SQL Server.

1. Zliczanie uwagi na dzień
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Pobierz poziomy w kolumnie podzielone na kategorie
   
    `select  distinct <column_name> from <databasename>`
3. Pobierz liczbę poziomów w połączeniu z dwóch kolumn podzielone na kategorie 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Pobierz dystrybucji dla kolumn wartości liczbowych
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Na przykład praktyczne, można użyć [dataset taksówki NYC](http://www.andresmh.com/nyctaxitrips/) i zapoznaj się z IPNB zatytułowany [wrangling NYC danych za pomocą notesu IPython i SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) dla przewodnika end-to-end.
> 
> 

## <a name="python"></a>Eksplorowanie danych SQL za pomocą języka Python
Przy użyciu języka Python do Eksplorowanie danych oraz do generowania funkcji, jeśli dane są w programie SQL Server jest podobny do przetwarzania danych obiektów blob platformy Azure przy użyciu języka Python, zgodnie z opisem w [danych obiektów Blob platformy Azure procesu w danym środowisku nauki danych](data-blob.md). Dane powinna być załadowana z bazy danych do pandas DataFrame, a następnie mogą być dalej przetwarzane. Proces łączenia z bazą danych i ładowania danych do DataFrame w tej sekcji możemy dokumentu.

Następującego formatu ciągu połączenia może służyć do połączenia z bazą danych programu SQL Server w języku Python za pomocą pyodbc (Zastąp servername, dbname nazwy użytkownika i hasła o określonej wartości):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Biblioteki Pandas](http://pandas.pydata.org/) w języku Python zawiera bogaty zestaw struktur danych i narzędzia do analizy danych do manipulowania danymi programowania Python. Poniższy kod odczytuje wyniki zwracane z bazy danych programu SQL Server do ramki danych Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Teraz możesz pracować z Pandas DataFrame, jak to opisano w temacie [danych obiektów Blob platformy Azure procesu w danym środowisku nauki danych](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Proces nauki danych zespołu w przykładzie akcji
Aby proces Analytics Cortana, przy użyciu publicznego zestawu danych, na przykład na trasie wskazówki, zobacz [zespołu danych nauki procesu w działaniu: przy użyciu programu SQL Server](sql-walkthrough.md).

