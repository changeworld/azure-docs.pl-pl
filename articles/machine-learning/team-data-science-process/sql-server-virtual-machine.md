---
title: Eksplorowanie danych maszyny wirtualnej programu SQL Server na platformie Azure | Dokumentacja firmy Microsoft
description: Eksploruj dane i generowania funkcje w maszyny wirtualnej programu SQL Server na platformie Azure
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: ''
ms.assetid: 3949fb2c-ffab-49fb-908d-27d5e42f743b
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: deguhath
ms.openlocfilehash: 39bdbce4ada225c0fa7df8559f68b591ccfc68b7
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838660"
---
# <a name="heading"></a>Dane procesu w maszynie wirtualnej serwera SQL na platformie Azure
W tym dokumencie opisano sposób eksplorować dane i generowanie funkcji danych przechowywanych na maszynie Wirtualnej z programu SQL Server na platformie Azure. Można to zrobić przy użyciu języka programowania, takich jak Python lub wrangling danych przy użyciu programu SQL.

> [!NOTE]
> Instrukcje SQL próbki w tym dokumencie założono, że dane są w programie SQL Server. Jeśli nie, zajrzyj do mapy procesu chmury danych nauki, aby dowiedzieć się, jak przenieść dane do programu SQL Server.
> 
> 

## <a name="SQL"></a>Przy użyciu programu SQL
Opisano następujące zadania wrangling danych w tej sekcji przy użyciu programu SQL:

1. [Eksploracja danych](#sql-dataexploration)
2. [Funkcja generowania](#sql-featuregen)

### <a name="sql-dataexploration"></a>Eksploracja danych
Poniżej przedstawiono kilka przykładowe skrypty SQL, które mogą służyć do eksplorowania magazyny danych w programie SQL Server.

> [!NOTE]
> Na przykład praktyczne, można użyć [dataset taksówki NYC](http://www.andresmh.com/nyctaxitrips/) i zapoznaj się z IPNB zatytułowany [wrangling NYC danych za pomocą notesu IPython i SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) dla przewodnika end-to-end.
> 
> 

1. Zliczanie uwagi na dzień
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Pobierz poziomy w kolumnie podzielone na kategorie
   
    `select  distinct <column_name> from <databasename>`
3. Pobierz liczbę poziomów w połączeniu z dwóch kolumn podzielone na kategorie 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Pobierz dystrybucji dla kolumn wartości liczbowych
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="sql-featuregen"></a>Funkcja generowania
W tej sekcji opisano sposób generowania funkcji za pomocą programu SQL:  

1. [Funkcja generowania na podstawie liczby](#sql-countfeature)
2. [Funkcja generowania binning](#sql-binningfeature)
3. [Wprowadza funkcji z jedną kolumną](#sql-featurerollout)

> [!NOTE]
> Po wygenerowaniu dodatkowe funkcje, możesz dodać je jako kolumny do istniejącej tabeli lub Utwórz nową tabelę z dodatkowych funkcji i klucz podstawowy, który może być połączony z oryginalnej tabeli. 
> 
> 

### <a name="sql-countfeature"></a>Funkcja generowania na podstawie liczby
W poniższych przykładach pokazano dwa sposoby generowania funkcji count. Pierwsza metoda korzysta sum warunkowych, a druga metoda klauzula "where". Te można następnie być połączony z oryginalnej tabeli (przy użyciu kolumn klucza podstawowego) ma funkcje liczby równolegle z oryginalnych danych.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>Funkcja generowania binning
Poniższy przykład przedstawia wygenerować funkcji binned przez binning (za pomocą pięciu bins) kolumny liczbowe, która może służyć jako funkcja zamiast tego:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Wprowadza funkcji z jedną kolumną
W tej sekcji pokażemy, jak korzystać z jednej kolumny w tabeli, aby wygenerować dodatkowe funkcje. W przykładzie założono, że w tabeli, z którego chcesz wygenerować funkcji jest kolumną szerokości geograficznej lub długość geograficzną.

Oto krótkie Elementarz na współrzędne/geograficznej lokalizacji danych (planować z stackoverflow [sposób mierzenia dokładność współrzędne geograficzne?](http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Jest to przydatne zrozumieć przed featurizing pole lokalizacji:

* Znak informuje NAS czy możemy się północ lub Południowa, wschód lub zachód na całym świecie.
* Niezerowe setki cyfrę informuje, nam używamy długości, nie szerokości geograficznej!
* Dziesiątki cyfrę daje możliwość około 1000 kilometrach. Udostępnia nam przydatnych informacji o jakie kontynencie lub Oceanu jesteśmy w.
* Cyfra jednostki (jeden stopień decimal) zapewnia pozycji do 111 kilometrach (60 mil, około 69 mil). Go nam powiedzieć około jakim stanie dużych lub kraju, w którym możemy w.
* Pierwszy przecinka warto do 11.1 km: można odróżnić pozycja dużych miast z sąsiednich Miasto duże.
* Drugi przecinka warto do 1.1 km: go oddzielić wieś jednego z następnej.
* Trzecia cyfra dziesiętna jest wartości do 110 m: zidentyfikuje dużych rolnych pola lub instytucjonalnych firmy.
* Warto m: do 11 można zidentyfikować zbiorczym ziemi jest czwarty miejsc dziesiętnych. Nie jest porównywalny typowe dokładność Niepoprawione jednostki GPS bez zakłóceń.
* Warto do 1.1 m: go odróżnia drzewa od siebie nawzajem jest piąty przecinka. Dokładność na ten poziom z komercyjnego jednostki GPS tylko może zostać osiągnięty przy różnicowej korekty.
* Warto do uwzględnieniu wartości 0,11 m: możesz użyć tej funkcji do układania struktury szczegółowo projektowania krajobrazów, tworzenie drogi jest szóstego miejsc dziesiętnych. Powinna ona więcej niż wystarczy do śledzenia przemieszczania glaciers i rzek. Można to osiągnąć, wykonując painstaking środków GPS, takich jak differentially poprawiony GPS.

Informacje o lokalizacji można featurized następujące oddzielanie regionu, lokalizacji i Miasto informacji. Należy pamiętać, można również wywołać punkt końcowy REST, np. interfejsu API map Bing dostępne pod adresem [znaleźć lokalizacji przez punkt](https://msdn.microsoft.com/library/ff701710.aspx) można pobrać informacji o regionie/okręgu.

    select 
        <location_columnname>
        ,round(<location_columnname>,0) as l1        
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

Te funkcje oparte na lokalizacji dalsze umożliwia generowanie liczby dodatkowych funkcji zgodnie z wcześniejszym opisem. 

> [!TIP]
> Można programowane Wstawianie rekordów przy użyciu języka wybór. Konieczne może być wstawić fragmentów, aby zwiększyć wydajność zapisu danych (na przykład jak to zrobić przy użyciu pyodbc zobacz [próby A HelloWorld do uzyskiwania dostępu do SQLServer python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Alternatywą jest do wstawiania danych w bazie danych przy użyciu [narzędzia BCP](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="sql-aml"></a>Nawiązywanie połączenia z usługi Azure Machine Learning
Funkcja wygenerowanym możliwość dodania jako kolumny do istniejącej tabeli lub przechowywane w nowej tabeli i połączony z oryginalnej tabeli do uczenia maszynowego. Funkcje mogą być generowane lub dostęp, jeśli już utworzone za pomocą [i zaimportuj dane] [ import-data] modułu w usłudze Azure Machine Learning, jak pokazano poniżej:

![czytniki uczenie maszynowe Azure][1] 

## <a name="python"></a>Przy użyciu języka programowania, takich jak Python
Przy użyciu języka Python do Eksplorowanie danych oraz do generowania funkcji, jeśli dane są w programie SQL Server jest podobny do przetwarzania danych obiektów blob platformy Azure przy użyciu języka Python, zgodnie z opisem w [danych obiektów Blob platformy Azure procesu w danym środowisku nauki danych](data-blob.md). Dane powinna być załadowana z bazy danych do ramki pandas danych, a następnie mogą być dalej przetwarzane. Proces łączenia z bazą danych i ładowania danych do ramki danych w tej sekcji możemy dokumentu.

Następującego formatu ciągu połączenia może służyć do połączenia z bazą danych programu SQL Server w języku Python za pomocą pyodbc (Zastąp servername, dbname nazwy użytkownika i hasła o określonej wartości):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Biblioteki Pandas](http://pandas.pydata.org/) w języku Python zawiera bogaty zestaw struktur danych i narzędzia do analizy danych do manipulowania danymi programowania Python. Poniższy kod odczytuje wyniki zwracane z bazy danych programu SQL Server do ramki danych Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Teraz możesz pracować z Pandas ramki danych, co opisano w artykule [danych obiektów Blob platformy Azure procesu w danym środowisku nauki danych](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Nauki danych Azure w przykładzie akcji
Na przykład wskazówki end-to-end procesu nauki danych Azure przy użyciu publicznego zestawu danych, zobacz [proces nauki danych Azure, w akcji](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

