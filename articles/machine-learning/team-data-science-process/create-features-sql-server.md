---
title: Tworzenie funkcji dla danych w programie SQL Server przy użyciu programu SQL i Python | Dokumentacja firmy Microsoft
description: Przetwarzania danych z usługi SQL Azure
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: ''
ms.assetid: bf1f4a6c-7711-4456-beb7-35fdccd46a44
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: deguhath
ms.openlocfilehash: 432f7b9bb27c1dee396677c54edf48d9fdb027a0
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836338"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Tworzenie funkcji dla danych w programie SQL Server przy użyciu języka SQL i Python
Ten dokument przedstawia sposób generowania funkcje dla danych przechowywanych w maszyny Wirtualnej serwera SQL na platformie Azure, które pomagają algorytmów wydajniej informacje z danych. Do wykonania tego zadania można użyć SQL lub języka programowania, takich jak Python. Poniżej przedstawiono w obu podejść.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

To **menu** linki do tematów opisujących sposób tworzenia funkcji dla danych w różnych środowiskach. To zadanie jest krokiem w [zespołu danych nauki procesu (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

> [!NOTE]
> Na przykład praktyczne, należy zapoznać się [dataset taksówki NYC](http://www.andresmh.com/nyctaxitrips/) i zapoznaj się z IPNB zatytułowany [wrangling NYC danych za pomocą notesu IPython i SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) dla przewodnika end-to-end.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że masz:

* Utworzone konto magazynu platformy Azure. Aby uzyskać instrukcje, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Przechowywane dane w programie SQL Server. Jeśli nie masz, zobacz [przenieść dane do bazy danych SQL Azure dla usługi Azure Machine Learning](move-sql-azure.md) instrukcje dotyczące przenoszenia danych istnieje.

## <a name="sql-featuregen"></a>Funkcja generowania z SQL
W tej sekcji opisano sposób generowania funkcji za pomocą programu SQL:  

1. [Funkcja generowania na podstawie liczby](#sql-countfeature)
2. [Funkcja generowania binning](#sql-binningfeature)
3. [Wprowadza funkcji z jedną kolumną](#sql-featurerollout)

> [!NOTE]
> Po wygenerowaniu dodatkowe funkcje, możesz dodać je jako kolumny do istniejącej tabeli lub Utwórz nową tabelę z dodatkowych funkcji i klucz podstawowy, który może być połączony z oryginalnej tabeli.
> 
> 

### <a name="sql-countfeature"></a>Na podstawie liczby funkcji generowania
W tym dokumencie przedstawiono dwa sposoby generowania funkcji count. Pierwsza metoda korzysta sum warunkowych, a druga metoda klauzula "where". Te można następnie być połączony z oryginalnej tabeli (przy użyciu kolumn klucza podstawowego) ma funkcje liczby równolegle z oryginalnych danych.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>Funkcja generowania binning
Poniższy przykład przedstawia sposób generowanie funkcji binned przez binning (przy użyciu 5 bins) numeryczny kolumny, która może służyć jako funkcja zamiast tego:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Wprowadza funkcji z jedną kolumną
W tej sekcji pokażemy, jak korzystać z jednej kolumny w tabeli, aby wygenerować dodatkowe funkcje. W przykładzie założono, że w tabeli, z którego chcesz wygenerować funkcji jest kolumną szerokości geograficznej lub długość geograficzną.

Oto krótkie Elementarz na współrzędne/geograficznej lokalizacji danych (planować z stackoverflow `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Oto niektóre przydatne wszystkim pamiętać o lokalizacji danych przed utworzeniem funkcji z pola:

* Znak wskazuje, czy możemy się północ lub południe, wschód lub zachód na całym świecie.
* Niezerowe setki cyfra wskazuje geograficzne, zakres nie jest używany.
* Dziesiątki cyfrę daje możliwość około 1000 kilometrach. Udostępnia przydatne informacje na temat jakiego kontynencie lub Oceanu jesteśmy w.
* Cyfra jednostki (jeden stopień decimal) zapewnia pozycji do 111 kilometrach (60 mil, około 69 mil). Wskazuje on, około, jakie dużych stanu lub kraju w.
* Pierwszy przecinka warto do 11.1 km: można odróżnić pozycja dużych miast z sąsiednich Miasto duże.
* Drugi przecinka warto do 1.1 km: go oddzielić wieś jednego z następnej.
* Trzecia cyfra dziesiętna jest wartości do 110 m: zidentyfikuje dużych rolnych pola lub instytucjonalnych firmy.
* Warto m: do 11 można zidentyfikować zbiorczym ziemi jest czwarty miejsc dziesiętnych. Nie jest porównywalny typowe dokładność Niepoprawione jednostki GPS bez zakłóceń.
* Warto do 1.1 m: go odróżnia drzewa od siebie nawzajem jest piąty przecinka. Dokładność na ten poziom z komercyjnego jednostki GPS tylko może zostać osiągnięty przy różnicowej korekty.
* Warto do uwzględnieniu wartości 0,11 m: możesz użyć tej funkcji do układania struktury szczegółowo projektowania krajobrazów, tworzenie drogi jest szóstego miejsc dziesiętnych. Powinna ona więcej niż wystarczy do śledzenia przemieszczania glaciers i rzek. Można to osiągnąć, wykonując painstaking środków GPS, takich jak differentially poprawiony GPS.

Informacje o lokalizacji może być featurized oddzielając regionu, lokalizacji i Miasto informacji. Należy pamiętać, że raz także wywołać punkt końcowy REST, np. interfejsu API map Bing dostępne pod adresem `https://msdn.microsoft.com/library/ff701710.aspx` można pobrać informacji o regionie/okręgu.

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

Te funkcje na podstawie lokalizacji można dodatkowo do generowania licznika dodatkowe funkcje, zgodnie z wcześniejszym opisem.

> [!TIP]
> Można programowane Wstawianie rekordów przy użyciu języka wybór. Konieczne może być wstawić fragmentów, aby zwiększyć wydajność zapisu danych. [Oto przykład tego, jak to zrobić przy użyciu pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Alternatywą jest do wstawiania danych w bazie danych przy użyciu [narzędzia BCP](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="sql-aml"></a>Nawiązywanie połączenia z usługi Azure Machine Learning
Funkcja wygenerowanym możliwość dodania jako kolumny do istniejącej tabeli lub przechowywane w nowej tabeli i połączony z oryginalnej tabeli do uczenia maszynowego. Funkcje mogą być generowane lub dostęp, jeśli już utworzone za pomocą [i zaimportuj dane](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) modułu w uczenie Maszynowe Azure, jak pokazano poniżej:

![Czytniki uczenia Maszynowego Azure](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Przy użyciu języka programowania, takich jak Python
Generowanie funkcji, jeśli dane są w programie SQL Server przy użyciu Python jest podobny do przetwarzania danych obiektów blob platformy Azure przy użyciu języka Python. Aby uzyskać porównanie, zobacz [danych obiektów Blob platformy Azure procesu w danym środowisku nauki danych](data-blob.md). Ładowanie danych z bazy danych do ramki danych pandas go przetworzyć. W tej sekcji opisano proces łączenia z bazą danych i ładowania danych do ramki danych.

Następującego formatu ciągu połączenia może służyć do połączenia z bazą danych programu SQL Server w języku Python za pomocą pyodbc (Zastąp servername, dbname nazwy użytkownika i hasła o określonej wartości):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Biblioteki Pandas](http://pandas.pydata.org/) w języku Python zawiera bogaty zestaw struktur danych i narzędzia do analizy danych do manipulowania danymi programowania Python. Poniższy kod odczytuje wyniki zwracane z bazy danych programu SQL Server do ramki danych Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Teraz możesz pracować z Pandas ramki danych objętych w tematach [Tworzenie funkcji dla danych magazynu obiektów blob platformy Azure przy użyciu Panda](create-features-blob.md).

