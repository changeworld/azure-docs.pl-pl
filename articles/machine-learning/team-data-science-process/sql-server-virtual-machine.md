---
title: Eksploruj dane na maszynie wirtualnej programu SQL Server — proces nauki o danych zespołowych
description: Eksploruj + przetwarzaj dane i generuj funkcje przy użyciu języka Python lub SQL na maszynie wirtualnej programu SQL Server na platformie Azure.
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
ms.openlocfilehash: d3eb4d2faf58d1861fda9d04437f9f9530c77672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718484"
---
# <a name="process-data-in-sql-server-virtual-machine-on-azure"></a><a name="heading"></a>Przetwarzanie danych maszyny wirtualnej programu SQL Server na platformie Azure
W tym dokumencie opisano sposób eksplorowania danych i generowania funkcji danych przechowywanych na maszynie wirtualnej programu SQL Server na platformie Azure. Ten cel może być zakończona przez wrangling danych przy użyciu języka SQL lub przy użyciu języka programowania, takich jak Python.

> [!NOTE]
> Przykładowe instrukcje SQL w tym dokumencie zakładają, że dane są w programie SQL Server. Jeśli tak nie jest, zapoznaj się z mapą procesu nauki o danych w chmurze, aby dowiedzieć się, jak przenieść dane do programu SQL Server.
> 
> 

## <a name="using-sql"></a><a name="SQL"></a>Korzystanie z języka SQL
Opisano następujące zadania wrangling danych w tej sekcji przy użyciu języka SQL:

1. [Eksploracja danych](#sql-dataexploration)
2. [Generowanie funkcji](#sql-featuregen)

### <a name="data-exploration"></a><a name="sql-dataexploration"></a>Eksploracja danych
Oto kilka przykładowych skryptów SQL, które mogą służyć do eksplorowania magazynów danych w programie SQL Server.

> [!NOTE]
> Na przykład praktyczny można użyć [zestawu danych taksówki NYC](https://www.andresmh.com/nyctaxitrips/) i odwołać się do ipnb o nazwie NYC Data [wrangling przy użyciu notesu IPython i programu SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) dla end-to-end przewodnika.
> 
> 

1. Uzyskaj liczbę obserwacji dziennie
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Pobierz poziomy w kolumnie kategorycznej
   
    `select  distinct <column_name> from <databasename>`
3. Uzyskaj liczbę poziomów w połączeniu z dwiema kolumnami kategorycznymi 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Pobierz rozkład kolumn numerycznych
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="feature-generation"></a><a name="sql-featuregen"></a>Generowanie funkcji
W tej sekcji opisano sposoby generowania funkcji przy użyciu języka SQL:  

1. [Generowanie funkcji oparte na zliczanie](#sql-countfeature)
2. [Generowanie funkcji Binning](#sql-binningfeature)
3. [Wdrażanie operacji z jednej kolumny](#sql-featurerollout)

> [!NOTE]
> Po wygenerowaniu dodatkowych funkcji można dodać je jako kolumny do istniejącej tabeli lub utworzyć nową tabelę z dodatkowymi funkcjami i kluczem podstawowym, które można połączyć z oryginalną tabelą. 
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Generowanie funkcji oparte na zliczanie
Poniższe przykłady pokazują dwa sposoby generowania funkcji zliczania. Pierwsza metoda używa sumy warunkowej, a druga metoda używa klauzuli "gdzie". Wyniki te mogą być następnie połączone z oryginalną tabelą (przy użyciu kolumn klucza podstawowego), aby mieć funkcje zliczania obok oryginalnych danych.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Generowanie funkcji Binning
W poniższym przykładzie pokazano, jak wygenerować binned operacji przez binning (przy użyciu pięciu pojemników) kolumny numerycznej, która może służyć jako obiekt zamiast:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>Wdrażanie operacji z jednej kolumny
W tej sekcji pokazujemy, jak wdrożyć pojedynczą kolumnę w tabeli, aby wygenerować dodatkowe funkcje. W przykładzie przyjęto założenie, że w tabeli znajduje się kolumna szerokości lub długości geograficznej, z której próbujesz wygenerować operacje.

Oto krótki podkład na szerokość / długość geograficzna dane lokalizacji (zasób z stackoverflow [Jak zmierzyć dokładność szerokości i długości geograficznej?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Te wskazówki są przydatne do zrozumienia przed połączeniem lokalizacji jako jednej lub więcej funkcji:

* Znak mówi nam, czy jesteśmy na północ czy południe, wschód lub zachód na świecie.
* Nonzero setki cyfr mówi nam, że używamy długości geograficznej, a nie szerokości geograficznej!
* Cyfra kilkudziesięciu daje pozycję do około 1000 kilometrów. Daje nam przydatne informacje o tym, na jakim kontynencie lub oceanie się znajdujemy.
* Cyfra jednostek (jeden stopień dziesiętny) daje pozycję do 111 kilometrów (60 mil morskich, około 69 mil). Może ci powiedzieć, w jakim stanie, kraju lub regionie się znajdujesz.
* Pierwsze miejsce po przecinku jest warte do 11,1 km: może odróżnić położenie jednego dużego miasta od sąsiedniego dużego miasta.
* Drugie miejsce po przecinku jest warte do 1,1 km: może oddzielić jedną wioskę od następnej.
* Trzecie miejsce po przecinku jest warte do 110 m: może zidentyfikować duże pole rolnicze lub kampus instytucjonalny.
* Czwarte miejsce po przecinku jest warte do 11 m: może zidentyfikować działkę. Jest to porównywalne z typową dokładnością nieskorygowanego urządzenia GPS bez zakłóceń.
* Piąte miejsce po przecinku jest warte do 1,1 m: odróżnia drzewa od siebie. Dokładność do tego poziomu z komercyjnych jednostek GPS można osiągnąć tylko z korektą różnicową.
* Szóste miejsce po przecinku jest warte do 0,11 m: można go użyć do szczegółowego układania konstrukcji, do projektowania krajobrazów, budowy dróg. Powinien być wystarczająco dobry do śledzenia ruchów lodowców i rzek. Można to osiągnąć poprzez podjęcie żmudnych środków z GPS, takich jak różnie skorygowane GPS.

Informacje o lokalizacji można uzyskać w następujący sposób, oddzielając informacje o regionie, lokalizacji i mieście. Można również wywołać punkt końcowy REST, takich jak Bing Maps API dostępne w [Znajdź lokalizację według punktu,](https://msdn.microsoft.com/library/ff701710.aspx) aby uzyskać informacje o regionie/dzielnicy.

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
> Rekordy można programowo wstawiać przy użyciu wybranego języka. Może być konieczne wstawienie danych w fragmentach w celu poprawy wydajności zapisu (na przykład, jak to zrobić za pomocą pyodbc, zobacz [przykład HelloWorld, aby uzyskać dostęp do programu SQLServer za pomocą języka Python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Inną alternatywą jest wstawienie danych do bazy danych za pomocą [narzędzia BCP](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Łączenie się z usługą Azure Machine Learning
Nowo wygenerowana funkcja może zostać dodana jako kolumna do istniejącej tabeli lub przechowywana w nowej tabeli i połączona z oryginalną tabelą do uczenia maszynowego. Funkcje mogą być generowane lub dostępne, jeśli zostały już utworzone, przy użyciu modułu [Importuj dane][import-data] w usłudze Azure Machine Learning, jak pokazano poniżej:

![czytniki azureml][1] 

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Używanie języka programowania, takiego jak Python
Korzystanie z języka Python do eksplorowania danych i generowania funkcji, gdy dane znajdują się w programie SQL Server, jest podobne do przetwarzania danych w środowisku blob platformy Azure przy użyciu języka Python, zgodnie z dokumentami w [środowisku przetwarzania obiektów blob platformy Azure w środowisku do nauki o danych.](data-blob.md) Załaduj dane z bazy danych do ramki danych pandas w celu zwiększenia przetwarzania. Dokumentujemy proces łączenia się z bazą danych i ładowania danych do ramki danych w tej sekcji.

Następujący format ciągu połączenia może służyć do łączenia się z bazą danych programu SQL Server z języka Python przy użyciu pyodbc (zastąp nazwę serwera, nazwę dbname, nazwę użytkownika i hasło określonymi wartościami):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Biblioteka Pandas](https://pandas.pydata.org/) w Pythonie zawiera bogaty zestaw struktur danych i narzędzi do analizy danych do manipulowania danymi podczas programowania języka Python. Poniższy kod odczytuje wyniki zwrócone z bazy danych programu SQL Server do ramki danych Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Teraz możesz pracować z ramką danych Pandas zgodnie z artykułem [Przetwarzanie danych obiektów blob platformy Azure w środowisku nauki o danych.](data-blob.md)

## <a name="azure-data-science-in-action-example"></a>Przykład nauki o danych platformy Azure w działaniu
Aby zapoznać się z kompleksowym przykładem przewodnika procesu nauki o danych platformy Azure przy użyciu publicznego zestawu danych, zobacz [Proces nauki o danych platformy Azure w działaniu.](sql-walkthrough.md)

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

