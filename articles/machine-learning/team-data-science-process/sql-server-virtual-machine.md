---
title: Eksplorowanie danych na maszynie wirtualnej programu SQL Server — zespołu danych dla celów naukowych
description: Eksploruj i Przetwarzaj dane oraz Generuj funkcje przy użyciu języka Python lub SQL w SQL Server maszynie wirtualnej na platformie Azure.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718484"
---
# <a name="heading"></a>Przetwarzanie danych w SQL Server maszynie wirtualnej na platformie Azure
W tym dokumencie opisano, jak eksplorować dane i generować funkcji dla danych przechowywanych na maszynę Wirtualną programu SQL Server na platformie Azure. Ten cel może być wykonany przez przetwarzanie danych przy użyciu programu SQL lub języka programowania, takiego jak Python.

> [!NOTE]
> Przykładowe instrukcje SQL, w tym dokumencie przyjęto założenie, że dane są w programie SQL Server. Jeśli nie, można znaleźć mapy procesu do nauki o danych chmury dowiesz się, jak przenieść dane do programu SQL Server.
> 
> 

## <a name="SQL"></a>Korzystanie z języka SQL
Opisano następujące zadania wrangling danych w tej sekcji przy użyciu języka SQL:

1. [Eksploracja danych](#sql-dataexploration)
2. [Generowanie funkcji](#sql-featuregen)

### <a name="sql-dataexploration"></a>Eksploracja danych
Poniżej przedstawiono kilka przykładowe skrypty SQL, których można użyć, aby zapoznać się z magazynami danych w programie SQL Server.

> [!NOTE]
> W przypadku praktycznego przykładu można użyć [zestawu danych NYC taksówki](https://www.andresmh.com/nyctaxitrips/) i odwołać się do IPNB [NYC danych przetwarzanie przy użyciu notesu IPython i SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) w celu uzyskania kompleksowego przewodnika.
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

1. [Generowanie funkcji na podstawie liczby](#sql-countfeature)
2. [Generowanie funkcji pakowania](#sql-binningfeature)
3. [Wdrażanie funkcji z pojedynczej kolumny](#sql-featurerollout)

> [!NOTE]
> Po wygenerowaniu dodatkowe funkcje, możesz dodać je jako kolumny do istniejącej tabeli lub Utwórz nową tabelę z dodatkowych funkcji i klucz podstawowy, który może być łączone z oryginalnej tabeli. 
> 
> 

### <a name="sql-countfeature"></a>Generowanie funkcji na podstawie liczby
W poniższych przykładach pokazano dwa sposoby generowania funkcji count. Pierwsza metoda używa sum warunkowych, a druga metoda używa klauzula "where". Te wyniki mogą następnie być sprzężone z oryginalną tabelą (przy użyciu kolumn klucza podstawowego) w celu uzyskania funkcji count wraz z oryginalnymi danymi.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>Generowanie funkcji pakowania
Poniższy przykład pokazuje, jak można wygenerować kwanty funkcji przez proces pakowania (pojemniki pięć) kolumny liczbowe, która może służyć jako funkcja zamiast tego:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Wdrażanie funkcji z pojedynczej kolumny
W tej sekcji pokażemy, jak wdrażać pojedynczą kolumnę w tabeli, aby wygenerować dodatkowe funkcje. W przykładzie założono, że w tabeli, z którego chcesz wygenerować funkcji znajduje się kolumna szerokości i długości geograficznej.

Poniżej znajduje się krótki początek danych lokalizacji Latitude/długości geograficznej (od StackOverflow [, jak zmierzyć dokładność długości i szerokości](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)geograficznej?). Wskazówki te są przydatne do zrozumienia przed dołączeniem lokalizacji jako jednej lub kilku funkcji:

* Znak informuje NAS, czy możemy się północ lub południe, wschód lub zachodnie na całym świecie.
* Wartość różną od zera setki cyfrę informuje NAS, że firma Microsoft korzysta długość geograficzna, szerokość nie!
* Dziesiątki cyfrę daje możliwość około 1000 kilometrów. Daje nam przydatnych informacji o jakie kontynent lub ocean jesteśmy na.
* Cyfra jednostki (jeden stopień dziesiętna) zapewnia pozycji do 111 kilometrów (60 mil, około 69 mil). Może on informować o stanie, kraju lub regionie, w którym się znajdujesz.
* Pierwsze miejsce dziesiętne, warto maksymalnie 11.1 km: można odróżnić, pozycja jeden duży kolumny city z sąsiednich Miasto dużych.
* Drugie miejsce dziesiętne, warto maksymalnie 1.1 km: je oddzielić wieś jednego z następnej.
* Warto do 110 m: można zidentyfikować, duże pole rolnicze lub campus instytucjonalnych jest trzeciego miejsca dziesiętnego.
* Czwarte miejsce dziesiętne warto m: do 11, że może ono ustalić działka ziemi. Jest ona porównywalna ze typowe dokładność Niepoprawione jednostki GPS bez zakłóceń.
* Piąty miejsca dziesiętnego jest warte maksymalnie 1.1 m: go odróżnia drzew od siebie nawzajem. Dokładność do tego poziomu przy użyciu komercyjnych jednostek GPS może odbyć się tylko z różnicowego korekty.
* Szósty miejsce dziesiętne warto m: maksymalnie uwzględnieniu wartości 0,11, których można użyć tego do układania struktury szczegóły dotyczące projektowania krajobrazów, tworzenie drogach. Powinna ona ponad wystarczająco dobre do śledzenia przemieszczania glaciers i rzek. Można to osiągnąć, wykonując painstaking miar z GPS, takich jak GPS differentially poprawiony.

Informacje o lokalizacji można neural następująco, oddzielanie region, lokalizacji i miejscowości. Możesz również wywołać punkt końcowy REST, taki jak interfejs API usługi mapy Bing dostępny w miejscu [wyszukiwania lokalizacji przez punkt](https://msdn.microsoft.com/library/ff701710.aspx) , aby uzyskać informacje o regionie/okręgu.

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
> Programowe można wstawić rekordów przy użyciu wybranego języka. Może być konieczne wstawienie danych w fragmentach, aby zwiększyć wydajność zapisu (Przykładowo, jak to zrobić za pomocą moduł pyodbc, zobacz [przykład HelloWorld, aby uzyskać dostęp do programu SqlServer przy użyciu języka Python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Kolejną alternatywą jest wstawianie danych w bazie danych przy użyciu [narzędzia bcp](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="sql-aml"></a>Nawiązywanie połączenia z Azure Machine Learning
Funkcja nowo wygenerowane można dodane jako kolumny do istniejącej tabeli lub przechowywane w nowej tabeli i łączone z oryginalnej tabeli dla usługi machine learning. Funkcje mogą być generowane lub dostępne, jeśli zostały już utworzone, przy użyciu modułu [Importuj dane][import-data] w Azure Machine Learning jak pokazano poniżej:

![Czytelnicy usługi Azure ml][1] 

## <a name="python"></a>Korzystanie z języka programowania, takiego jak Python
Używanie języka Python do eksplorowania danych i generowania funkcji, gdy dane są w SQL Server są podobne do przetwarzania danych w obiekcie blob platformy Azure przy użyciu języka Python zgodnie z opisem w temacie [przetwarzanie danych obiektów blob platformy Azure w środowisku nauki danych](data-blob.md). Załaduj dane z bazy danych do ramki danych Pandas w celu przetworzenia. Udokumentowaliśmy proces łączenia z bazą danych i ładowania danych do ramki danych w tej sekcji.

Następujący format parametrów połączenia może służyć do łączenia z bazą danych programu SQL Server za pomocą języka Python za pomocą moduł pyodbc (Zastąp servername, dbname, nazwę użytkownika i hasła o określonej wartości):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

[Biblioteka Pandas](https://pandas.pydata.org/) w języku Python oferuje bogaty zestaw struktur danych i narzędzi do analizy danych na potrzeby manipulowania danymi na potrzeby programowania w języku Python. Poniższy kod odczytuje wyniki zwracane z bazy danych programu SQL Server do ramki danych Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Teraz możesz współpracować z ramką danych Pandas, jak opisano w artykule [przetwarzanie danych obiektów blob platformy Azure w środowisku nauki danych](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Do nauki o danych platformy Azure, w przykładzie akcji
Aby zapoznać się z kompleksowym przykładem procesu nauki o danych platformy Azure przy użyciu publicznego zestawu danych, zobacz [proces analizy danych platformy Azure w działaniu](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

