---
title: Tworzenie funkcji w programie SQL Server przy użyciu języka SQL i Python — proces nauki o danych zespołowych
description: Generowanie funkcji dla danych przechowywanych w maszynie Wirtualnej programu SQL Server na platformie Azure przy użyciu języka SQL i Pythona — część procesu nauki o danych zespołu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721748"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Tworzenie funkcji dla danych w programie SQL Server przy użyciu języka SQL i Python
W tym dokumencie pokazano, jak wygenerować funkcje danych przechowywanych na maszynie Wirtualnej programu SQL Server na platformie Azure, które pomagają algorytmom efektywniej uczyć się na podstawie danych. Aby wykonać to zadanie, można użyć języka SQL lub języka programowania, takiego jak Python. Oba podejścia są tutaj przedstawione.

To zadanie jest krokiem w [procesie nauki o danych zespołu (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

> [!NOTE]
> Na przykład praktyczny można zapoznać się z [zestawem danych taksówki NYC](https://www.andresmh.com/nyctaxitrips/) i zapoznać się z ipnb o nazwie [NYC Data wrangling przy użyciu notesu IPython i programu SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) dla end-to-end.
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz:

* Utworzono konto magazynu platformy Azure. Jeśli potrzebujesz instrukcji, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-account-create.md)
* Przechowywane dane w programie SQL Server. Jeśli nie, zobacz [Przenoszenie danych do usługi Azure SQL Database dla usługi Azure Machine Learning,](move-sql-azure.md) aby uzyskać instrukcje dotyczące przenoszenia danych w tym miejscu.

## <a name="feature-generation-with-sql"></a><a name="sql-featuregen"></a>Generowanie funkcji za pomocą języka SQL
W tej sekcji opisano sposoby generowania funkcji przy użyciu języka SQL:  

* [Generowanie funkcji oparte na zliczanie](#sql-countfeature)
* [Generowanie funkcji Binning](#sql-binningfeature)
* [Wdrażanie operacji z jednej kolumny](#sql-featurerollout)

> [!NOTE]
> Po wygenerowaniu dodatkowych funkcji można dodać je jako kolumny do istniejącej tabeli lub utworzyć nową tabelę z dodatkowymi funkcjami i kluczem podstawowym, które można połączyć z oryginalną tabelą.
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Generowanie funkcji oparte na liczbie
W tym dokumencie przedstawiono dwa sposoby generowania funkcji zliczania. Pierwsza metoda używa sumy warunkowej, a druga metoda używa klauzuli "gdzie". Te nowe funkcje można następnie połączyć z oryginalną tabelą (przy użyciu kolumn klucza podstawowego), aby mieć funkcje zliczania obok oryginalnych danych.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Generowanie funkcji Binning
W poniższym przykładzie pokazano, jak wygenerować binned operacji przez binning (przy użyciu pięciu pojemników) kolumny numerycznej, która może służyć jako obiekt zamiast:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>Wdrażanie operacji z jednej kolumny
W tej sekcji pokazujemy, jak wdrożyć pojedynczą kolumnę w tabeli, aby wygenerować dodatkowe funkcje. W przykładzie przyjęto założenie, że w tabeli znajduje się kolumna szerokości lub długości geograficznej, z której próbujesz wygenerować operacje.

Oto krótki podkład na szerokość /długość geograficzna dane lokalizacji (zasób z stackoverflow `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Oto kilka przydatnych rzeczy, które należy zrozumieć na temat danych o lokalizacji przed utworzeniem funkcji z pola:

* Znak wskazuje, czy jesteśmy na północy lub południu, wschodzie lub zachodzie na świecie.
* Nonzero hundreds cyfra wskazuje długość geograficzną, nie szerokość geograficzna jest używany.
* Cyfra kilkudziesięciu daje pozycję do około 1000 kilometrów. Dostarcza przydatnych informacji o tym, na jakim kontynencie lub oceanie się znajdujemy.
* Cyfra jednostek (jeden stopień dziesiętny) daje pozycję do 111 kilometrów (60 mil morskich, około 69 mil). Wskazuje mniej więcej, w jakim dużym stanie lub kraju /regionie jesteśmy.
* Pierwsze miejsce po przecinku jest warte do 11,1 km: może odróżnić położenie jednego dużego miasta od sąsiedniego dużego miasta.
* Drugie miejsce po przecinku jest warte do 1,1 km: może oddzielić jedną wioskę od następnej.
* Trzecie miejsce po przecinku jest warte do 110 m: może zidentyfikować duże pole rolnicze lub kampus instytucjonalny.
* Czwarte miejsce po przecinku jest warte do 11 m: może zidentyfikować działkę. Jest to porównywalne z typową dokładnością nieskorygowanego urządzenia GPS bez zakłóceń.
* Piąte miejsce po przecinku jest warte do 1,1 m: odróżnia drzewa od siebie. Dokładność do tego poziomu z komercyjnych jednostek GPS można osiągnąć tylko z korektą różnicową.
* Szóste miejsce po przecinku jest warte do 0,11 m: można użyć tego poziomu do szczegółowego układania konstrukcji, do projektowania krajobrazów, budowy dróg. Powinien być wystarczająco dobry do śledzenia ruchów lodowców i rzek. Cel ten można osiągnąć poprzez podjęcie żmudnych środków z GPS, takich jak różnie skorygowane GPS.

Informacje o lokalizacji można uzyskać, oddzielając informacje o regionie, lokalizacji i mieście. Raz można również wywołać punkt końcowy REST, takich `https://msdn.microsoft.com/library/ff701710.aspx` jak Bing Maps interfejsu API (zobacz, aby uzyskać informacje o regionie/okręgu).

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

Te funkcje oparte na lokalizacji mogą być dodatkowo używane do generowania dodatkowych funkcji zliczania, jak opisano wcześniej.

> [!TIP]
> Rekordy można programowo wstawiać przy użyciu wybranego języka. Może być konieczne wstawienie danych w fragmentach, aby poprawić wydajność zapisu. [Oto przykład jak to zrobić za pomocą pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Inną alternatywą jest wstawienie danych do bazy danych za pomocą [narzędzia BCP](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Łączenie się z usługą Azure Machine Learning
Nowo wygenerowana funkcja może zostać dodana jako kolumna do istniejącej tabeli lub przechowywana w nowej tabeli i połączona z oryginalną tabelą do uczenia maszynowego. Funkcje mogą być generowane lub dostępne, jeśli zostały już utworzone, przy użyciu modułu [Importuj dane](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) w usłudze Azure ML, jak pokazano poniżej:

![Czytniki usługi Azure ML](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Używanie języka programowania, takiego jak Python
Za pomocą języka Python do generowania funkcji, gdy dane są w programie SQL Server jest podobny do przetwarzania danych w obiekcie blob platformy Azure przy użyciu języka Python. Dla porównania zobacz [przetwarzanie danych obiektów blob platformy Azure w środowisku nauki o danych.](data-blob.md) Załaduj dane z bazy danych do ramki danych pandas, aby przetworzyć ją dalej. Proces łączenia się z bazą danych i ładowania danych do ramki danych jest udokumentowany w tej sekcji.

Następujący format ciągu połączenia może służyć do łączenia się z bazą danych programu SQL Server z języka Python przy użyciu pyodbc (zastąp nazwę serwera, nazwę dbname, nazwę użytkownika i hasło określonymi wartościami):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Biblioteka Pandas](https://pandas.pydata.org/) w Pythonie zawiera bogaty zestaw struktur danych i narzędzi do analizy danych do manipulowania danymi podczas programowania języka Python. Poniższy kod odczytuje wyniki zwrócone z bazy danych programu SQL Server do ramki danych Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Teraz możesz pracować z ramką danych Pandas, jak omówione w tematach [Tworzenie funkcji dla danych magazynu obiektów blob platformy Azure przy użyciu Panda](create-features-blob.md).

