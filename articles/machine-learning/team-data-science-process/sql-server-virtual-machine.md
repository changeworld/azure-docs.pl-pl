---
title: Eksplorowanie danych na maszynie wirtualnej programu SQL Server — zespołu danych dla celów naukowych
description: Eksplorowanie danych i wygenerować funkcje na maszynie wirtualnej programu SQL Server na platformie Azure
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/23/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6adc5dfa740d440e78bf2f276447c4585503d7c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606470"
---
# <a name="heading"></a>Przetwarzanie danych w maszyną wirtualną programu SQL Server na platformie Azure
W tym dokumencie opisano, jak eksplorować dane i generować funkcji dla danych przechowywanych na maszynę Wirtualną programu SQL Server na platformie Azure. Można to zrobić za dane inteligencji przy użyciu języka SQL lub przy użyciu języka programowania, takich jak Python.

> [!NOTE]
> Przykładowe instrukcje SQL, w tym dokumencie przyjęto założenie, że dane są w programie SQL Server. Jeśli nie, można znaleźć mapy procesu do nauki o danych chmury dowiesz się, jak przenieść dane do programu SQL Server.
> 
> 

## <a name="SQL"></a>Przy użyciu języka SQL
Opisano następujące zadania wrangling danych w tej sekcji przy użyciu języka SQL:

1. [Eksploracja danych](#sql-dataexploration)
2. [Generowanie funkcji](#sql-featuregen)

### <a name="sql-dataexploration"></a>Eksploracja danych
Poniżej przedstawiono kilka przykładowe skrypty SQL, których można użyć, aby zapoznać się z magazynami danych w programie SQL Server.

> [!NOTE]
> Na przykład praktycznych, możesz użyć [zestawu danych taksówek NYC](https://www.andresmh.com/nyctaxitrips/) i odnoszą się do IPNB pod tytułem [danych NYC inteligencji przy użyciu IPython Notebook i programu SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) dla przewodnika end-to-end.
> 
> 

1. Zliczanie uwagi na dzień
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Pobierz poziomy w kolumnie podzielonych na kategorie
   
    `select  distinct <column_name> from <databasename>`
3. Pobierz liczbę poziomów w połączeniu z dwóch kolumn podzielonych na kategorie 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Rozpoczynanie dystrybucji dla kolumny liczbowe
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="sql-featuregen"></a>Generowanie funkcji
W tej sekcji opisano sposób generowania funkcji przy użyciu języka SQL:  

1. [Liczba na podstawie funkcji generowania](#sql-countfeature)
2. [Kwantowanie generacji funkcji](#sql-binningfeature)
3. [Wdrażanie funkcji z jednej kolumny](#sql-featurerollout)

> [!NOTE]
> Po wygenerowaniu dodatkowe funkcje, możesz dodać je jako kolumny do istniejącej tabeli lub Utwórz nową tabelę z dodatkowych funkcji i klucz podstawowy, który może być łączone z oryginalnej tabeli. 
> 
> 

### <a name="sql-countfeature"></a>Liczba na podstawie funkcji generowania
W poniższych przykładach pokazano dwa sposoby generowania funkcji count. Pierwsza metoda używa sum warunkowych, a druga metoda używa klauzula "where". Te mogą następnie zostać sprzężone z oryginalnej tabeli (przy użyciu kolumny klucza podstawowego) z mają funkcje count, wraz z oryginalnymi danymi.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>Kwantowanie generacji funkcji
Poniższy przykład pokazuje, jak można wygenerować kwanty funkcji przez proces pakowania (pojemniki pięć) kolumny liczbowe, która może służyć jako funkcja zamiast tego:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Wdrażanie funkcji z jednej kolumny
W tej sekcji pokażemy, jak wdrażać pojedynczą kolumnę w tabeli, aby wygenerować dodatkowe funkcje. W przykładzie założono, że w tabeli, z którego chcesz wygenerować funkcji znajduje się kolumna szerokości i długości geograficznej.

Poniżej przedstawiono krótki Podręcznik szerokości/długości geograficznej lokalizacji danych (z zasobami w witrynie stackoverflow [sposób mierzenia dokładności szerokości i długości geograficznej?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Jest to przydatne do zrozumienia przed featurizing pole lokalizacji:

* Znak informuje NAS, czy możemy się północ lub południe, wschód lub zachodnie na całym świecie.
* Wartość różną od zera setki cyfrę informuje NAS, że firma Microsoft korzysta długość geograficzna, szerokość nie!
* Dziesiątki cyfrę daje możliwość około 1000 kilometrów. Daje nam przydatnych informacji o jakie kontynent lub ocean jesteśmy na.
* Cyfra jednostki (jeden stopień dziesiętna) zapewnia pozycji do 111 kilometrów (60 mil, około 69 mil). Można powiedzieć nam około jakie dużych stanu lub kraju/regionu, w których jesteśmy w stanie.
* Pierwsze miejsce dziesiętne, warto maksymalnie 11.1 km: można odróżnić, pozycja jeden duży kolumny city z sąsiednich Miasto dużych.
* Drugie miejsce dziesiętne, warto maksymalnie 1.1 km: je oddzielić wieś jednego z następnej.
* Warto do 110 m: można zidentyfikować, duże pole rolnicze lub campus instytucjonalnych jest trzeciego miejsca dziesiętnego.
* Czwarte miejsce dziesiętne warto m: do 11, że może ono ustalić działka ziemi. Jest ona porównywalna ze typowe dokładność Niepoprawione jednostki GPS bez zakłóceń.
* Piąty miejsca dziesiętnego jest warte maksymalnie 1.1 m: go odróżnia drzew od siebie nawzajem. Dokładność do tego poziomu przy użyciu komercyjnych jednostek GPS może odbyć się tylko z różnicowego korekty.
* Szósty miejsce dziesiętne warto m: maksymalnie uwzględnieniu wartości 0,11, których można użyć tego do układania struktury szczegóły dotyczące projektowania krajobrazów, tworzenie drogach. Powinna ona ponad wystarczająco dobre do śledzenia przemieszczania glaciers i rzek. Można to osiągnąć, wykonując painstaking miar z GPS, takich jak GPS differentially poprawiony.

Informacje o lokalizacji można neural następująco, oddzielanie region, lokalizacji i miejscowości. Należy pamiętać, że można również wywołać punkt końcowy REST, np. interfejsu API map Bing dostępne pod adresem [znaleźć lokalizacji punktowi](https://msdn.microsoft.com/library/ff701710.aspx) można pobrać informacji o regionie/region.

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
> Programowe można wstawić rekordów przy użyciu wybranego języka. Musisz wstawić dane we fragmentach, aby zwiększyć wydajność zapisu (na przykład jak to zrobić za pomocą moduł pyodbc zobacz [HelloWorld przykładu, aby uzyskać dostęp SQLServer za pomocą języka python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Innym sposobem jest, aby wstawić dane do bazy danych przy użyciu [narzędzie BCP](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="sql-aml"></a>Nawiązywanie połączenia z usługi Azure Machine Learning
Funkcja nowo wygenerowane można dodane jako kolumny do istniejącej tabeli lub przechowywane w nowej tabeli i łączone z oryginalnej tabeli dla usługi machine learning. Funkcje, które mogą być generowane lub dostęp, jeśli już utworzone za pomocą [importu danych] [ import-data] modułu w usłudze Azure Machine Learning, jak pokazano poniżej:

![Czytelnicy usługi Azure ml][1] 

## <a name="python"></a>Przy użyciu języka programowania, takich jak Python
Przy użyciu języka Python, aby eksplorować dane i wygenerować funkcje, gdy dane znajdują się w programie SQL Server jest podobny do przetwarzania danych w usłudze Azure blob przy użyciu języka Python, zgodnie z opisem w [danych obiektów Blob platformy Azure proces w danym środowisku do nauki o danych](data-blob.md). Dane powinna być załadowana z bazy danych do ramki danych pandas i następnie mogą być dalej przetwarzane. Udokumentowaliśmy proces łączenia z bazą danych i ładowania danych do ramki danych w tej sekcji.

Następujący format parametrów połączenia może służyć do łączenia z bazą danych programu SQL Server za pomocą języka Python za pomocą moduł pyodbc (Zastąp servername, dbname, nazwę użytkownika i hasła o określonej wartości):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Biblioteki Pandas](https://pandas.pydata.org/) w języku Python zawiera bogaty zestaw struktur danych i narzędzia do analizy danych do manipulowania danymi programowania Python. Poniższy kod odczytuje wyniki zwracane z bazy danych programu SQL Server do ramki danych Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Teraz możesz pracować z biblioteki Pandas ramki danych zgodnie z opisem w artykule [danych obiektów Blob platformy Azure proces w danym środowisku do nauki o danych](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Do nauki o danych platformy Azure, w przykładzie akcji
Na przykład wskazówki end-to-end procesu do nauki o danych platformy Azure przy użyciu publicznego zestawu danych, zobacz [Azure danych dla celów naukowych w działaniu](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

