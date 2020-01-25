---
title: Tworzenie funkcji w programie SQL Server przy użyciu programu SQL i Python — zespołu danych dla celów naukowych
description: Generowanie funkcji dla danych przechowywanych na maszynę Wirtualną programu SQL Server na platformie Azure przy użyciu programu SQL i Python — część procesu do nauki o danych zespołu.
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
ms.openlocfilehash: 58fa98005d7d89e84404d99cf4f55e456fd91f21
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721748"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Tworzenie funkcji dla danych w programie SQL Server przy użyciu języka SQL i Python
W tym dokumencie przedstawiono sposób generowania funkcji dla danych przechowywanych w maszynę Wirtualną programu SQL Server na platformie Azure, ułatwiającą algorytmy wydajniej informacje z danych. Można użyć bazy danych SQL lub języka programowania, takich jak Python, aby wykonać to zadanie. Oba podejścia są przedstawione w tym artykule.

To zadanie jest to krok w [Team Data Science naukowych](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

> [!NOTE]
> Na przykład praktycznych, można zapoznać się [zestawu danych taksówek NYC](https://www.andresmh.com/nyctaxitrips/) i odnoszą się do IPNB pod tytułem [danych NYC inteligencji przy użyciu IPython Notebook i programu SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) dla przewodnika end-to-end.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz:

* Utworzone konto magazynu platformy Azure. Aby uzyskać instrukcje, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-account-create.md)
* Przechowywane dane w programie SQL Server. Jeśli nie masz, zobacz [przenoszenie danych do usługi Azure SQL Database dla usługi Azure Machine Learning](move-sql-azure.md) instrukcje dotyczące przenoszenia danych istnieje.

## <a name="sql-featuregen"></a>Generowanie funkcji przy użyciu języka SQL
W tej sekcji opisano sposób generowania funkcji przy użyciu języka SQL:  

* [Liczba na podstawie funkcji generowania](#sql-countfeature)
* [Kwantowanie generacji funkcji](#sql-binningfeature)
* [Wdrażanie funkcji z jednej kolumny](#sql-featurerollout)

> [!NOTE]
> Po wygenerowaniu dodatkowe funkcje, możesz dodać je jako kolumny do istniejącej tabeli lub Utwórz nową tabelę z dodatkowych funkcji i klucz podstawowy, który może być łączone z oryginalnej tabeli.
> 
> 

### <a name="sql-countfeature"></a>Na podstawie liczby funkcji generowania
Niniejszy dokument przedstawia dwa sposoby generowania funkcji count. Pierwsza metoda używa sum warunkowych, a druga metoda używa klauzula "where". Te nowe funkcje mogą następnie być sprzężone z oryginalną tabelą (przy użyciu kolumn klucza podstawowego) w celu uzyskania funkcji count wraz z oryginalnymi danymi.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>Kwantowanie generacji funkcji
Poniższy przykład pokazuje, jak można wygenerować kwanty funkcji przez proces pakowania (pojemniki pięć) kolumny liczbowe, która może służyć jako funkcja zamiast tego:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Wdrażanie funkcji z jednej kolumny
W tej sekcji pokażemy, jak wdrażać pojedynczą kolumnę w tabeli, aby wygenerować dodatkowe funkcje. W przykładzie założono, że w tabeli, z którego chcesz wygenerować funkcji znajduje się kolumna szerokości i długości geograficznej.

Poniżej przedstawiono krótki Podręcznik szerokości/długości geograficznej lokalizacji danych (z zasobami w witrynie stackoverflow `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Poniżej przedstawiono niektóre kwestie przydatne informacje o lokalizacji danych przed utworzeniem funkcji z pola:

* Znak wskazuje, czy możemy się północ lub południe, wschód lub zachód na całym świecie.
* Wartość różną od zera setki cyfra wskazuje długość geograficzna, szerokość nie jest on używany.
* Dziesiątki cyfrę daje możliwość około 1000 kilometrów. Daje ona przydatnych informacji o jakie kontynent lub ocean jesteśmy na.
* Cyfra jednostki (jeden stopień dziesiętna) zapewnia pozycji do 111 kilometrów (60 mil, około 69 mil). Oznacza to, że jest to bardzo duży stan lub kraj/region, w którym się znajdują.
* Pierwsze miejsce dziesiętne, warto maksymalnie 11.1 km: można odróżnić, pozycja jeden duży kolumny city z sąsiednich Miasto dużych.
* Drugie miejsce dziesiętne, warto maksymalnie 1.1 km: je oddzielić wieś jednego z następnej.
* Warto do 110 m: można zidentyfikować, duże pole rolnicze lub campus instytucjonalnych jest trzeciego miejsca dziesiętnego.
* Czwarte miejsce dziesiętne warto m: do 11, że może ono ustalić działka ziemi. Jest ona porównywalna ze typowe dokładność Niepoprawione jednostki GPS bez zakłóceń.
* Piąty miejsca dziesiętnego jest warte maksymalnie 1.1 m: go odróżnia drzew od siebie nawzajem. Dokładność do tego poziomu przy użyciu komercyjnych jednostek GPS może odbyć się tylko z różnicowego korekty.
* Szóste miejsce dziesiętne jest równe 0,11 m: można użyć tego poziomu, aby uzyskać szczegółowe informacje o strukturach, na potrzeby projektowania Landscapes, tworzenia dróg. Powinna ona ponad wystarczająco dobre do śledzenia przemieszczania glaciers i rzek. Ten cel można osiągnąć, pobierając miary painstaking z GPS, takie jak rozwiązane z różnicą GPS.

Informacje o lokalizacji może być neural, oddzielając region, lokalizacji i miejscowości. Można również wywołać punkt końcowy REST, taki jak interfejs API usługi mapy Bing (zobacz `https://msdn.microsoft.com/library/ff701710.aspx`, aby uzyskać informacje o regionie/okręgu).

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

Te funkcje oparte na lokalizacji dalsze można wygenerować liczba dodatkowych funkcji, zgodnie z wcześniejszym opisem.

> [!TIP]
> Programowe można wstawić rekordów przy użyciu wybranego języka. Może być konieczne wstawić dane we fragmentach, aby zwiększyć wydajność zapisu. [Poniżej przedstawiono przykładowy sposób to zrobić za pomocą moduł pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Innym sposobem jest, aby wstawić dane do bazy danych przy użyciu [narzędzia BCP](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="sql-aml"></a>Nawiązywanie połączenia z usługi Azure Machine Learning
Funkcja nowo wygenerowane można dodane jako kolumny do istniejącej tabeli lub przechowywane w nowej tabeli i łączone z oryginalnej tabeli dla usługi machine learning. Funkcje, które mogą być generowane lub dostęp, jeśli już utworzone za pomocą [importu danych](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) modułu usługi Azure ml, jak pokazano poniżej:

![Usługa Azure ML czytelnicy](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Przy użyciu języka programowania, takich jak Python
Aby wygenerować funkcje, gdy dane znajdują się w programie SQL Server przy użyciu języka Python jest podobny do przetwarzania danych w usłudze Azure blob przy użyciu języka Python. Dla porównania, zobacz [danych obiektów Blob platformy Azure proces w danym środowisku do nauki o danych](data-blob.md). Załaduj dane z bazy danych do pandas ramki danych, aby go przetworzyć. W tej sekcji opisano proces łączenia z bazą danych i ładowania danych do ramki danych.

Następujący format parametrów połączenia może służyć do łączenia z bazą danych programu SQL Server za pomocą języka Python za pomocą moduł pyodbc (Zastąp servername, dbname, nazwę użytkownika i hasła o określonej wartości):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Biblioteki Pandas](https://pandas.pydata.org/) w języku Python zawiera bogaty zestaw struktur danych i narzędzia do analizy danych do manipulowania danymi programowania Python. Poniższy kod odczytuje wyniki zwracane z bazy danych programu SQL Server do ramki danych Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Teraz możesz pracować z biblioteki Pandas ramki danych zgodnie z opisem w tematach [Tworzenie funkcji dla danych magazynu obiektów blob platformy Azure za pomocą usługi Panda](create-features-blob.md).

