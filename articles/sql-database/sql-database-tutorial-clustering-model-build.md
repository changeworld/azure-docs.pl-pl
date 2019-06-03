---
title: 'Samouczek: Tworzenie model klastrowania w języku R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W drugiej części tej serii samouczków trzyczęściowej serii utworzysz model K-średnich do wykonania, klastrowania w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 05/17/2019
ms.openlocfilehash: 12738b63be92420c5f3afea6c133522cbd97f849
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419762"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Samouczek: Tworzenie model klastrowania w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)

W drugiej części tej serii samouczków trzyczęściowej serii utworzysz model K-średnich do wykonania, klastrowania w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza).

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Zdefiniuj liczbę klastrów dla algorytmu K-średnich
> * Wykonaj klastrowania
> * Analizuj wyniki

W [pierwszej części](sql-database-tutorial-clustering-model-prepare-data.md), wiesz, jak przygotować dane z usługi Azure SQL database do wykonania, klastrowania w języku R.

W [trzyczęściowej](sql-database-tutorial-clustering-model-deploy.md), dowiesz się, jak utworzyć procedurę przechowywaną w usługi Azure SQL database, które może wykonywać, klastrowanie, na podstawie nowych danych.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Drugiej części tego samouczka przyjęto założenie, zostały wykonane [ **pierwszej części** ](sql-database-tutorial-clustering-model-prepare-data.md) i jego wymagania wstępne.

## <a name="define-the-number-of-clusters"></a>Zdefiniuj liczbę klastrów

W klastrze Twoich danych klienta, użyjesz **K-średnich** algorytmu klastrowania, co najprostszy i dobrze znane sposoby grupowania danych.
Możesz dowiedzieć się więcej o K-średnich w [kompletny przewodnik po algorytmu klastrowania K-średnich](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

Algorytm akceptuje dwóch danych wejściowych: Dane i wstępnie zdefiniowany numer "*k*" reprezentujący liczbę klastrów do wygenerowania.
Dane wyjściowe są *k* klastrami przy użyciu danych wejściowych na partycje, między klastrami.

Aby określić liczbę klastrów dla algorytmu do użycia, należy użyć wykres w obrębie grupy suma kwadratów według liczby klastrów wyodrębnione. Odpowiednią liczbę klastrów do użycia znajduje się na odcinek łącznika lub "kątowa" powierzchni.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Objaśnienie wykresu](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

Oparty na wykresie, wygląda *k = 4* byłaby odpowiednią wartością do wypróbowania. Czy *k* wartość opcji pozwala grupować klientów na cztery klastry.

## <a name="perform-clustering"></a>Wykonaj klastrowania

W poniższym skrypcie języka R, użyjemy funkcji **rxKmeans**, czyli funkcja K-średnich pakietu RevoScaleR.

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>Analizuj wyniki

Teraz, że zostały wykonane przy użyciu K-średnich klastrowania, następnym krokiem jest analiza wynik i sprawdź, czy możesz znaleźć wszystkie informacje umożliwiające działanie.

**Clust** obiekt zawiera wyniki z klastrowania K-średnich.

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio + 
    frequency, data = customer_returns, outFile = return_cluster, 
    outColName = "cluster", extraVarsToWrite = c("customer"), 
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0 
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

Oznacza czterech klastrów podany przy użyciu zmiennych zdefiniowanych w [pierwszej części](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers):

* *orderRatio* = stosunek zwrotu zamówienia (całkowita liczba zamówień, częściowo lub całkowicie zwrócony, a łączna liczba zamówień)
* *itemsRatio* = stosunek zwracanego elementu (całkowita liczba elementów zwróconych w porównaniu z liczbą elementów zakupu)
* *monetaryRatio* = stosunek kwota zwrotu (łączną kwotę pieniężną elementów zwróconych w porównaniu z kwota zakupu)
* *częstotliwość* = zwracany częstotliwości

Wyszukiwanie danych przy użyciu K-średnich często wymaga dalszych analiz wyniki i dalsze czynności, aby lepiej zrozumieć każdy klaster, ale oferuje ona pewne dobre potencjalnych klientów.
Poniżej przedstawiono kilka sposobów może interpretować te wyniki:

* Klaster 1 (największa klastra) jest prawdopodobnie grupy odbiorców, które nie są aktywne (wszystkie wartości są zero).
* Klaster 3 wydaje się być grupy, która detaliczny wyróżnia się pod względem zwracanego zachowanie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

***Jeśli nie zamierzasz kontynuować z tego samouczka***, Usuń tpcxbb_1gb bazę danych z serwera usługi Azure SQL Database.

W witrynie Azure portal wykonaj następujące kroki:

1. Wybierz z menu po lewej stronie w witrynie Azure portal, **wszystkie zasoby** lub **baz danych SQL**.
1. W **Filtruj według nazwy...**  wprowadź **tpcxbb_1gb**i wybierz swoją subskrypcję.
1. Wybierz swoje **tpcxbb_1gb** bazy danych.
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W drugiej części tej serii samouczków możesz wykonać następujące czynności:

* Zdefiniuj liczbę klastrów dla algorytmu K-średnich
* Wykonaj klastrowania
* Analizuj wyniki

Aby wdrożyć model usługi machine learning, który został utworzony, postępuj zgodnie z trzeciej części tej serii samouczków:

> [!div class="nextstepaction"]
> [Samouczek: Wdróż model klastrowania w języku R z SQL bazy danych usług Azure Machine Learning (wersja zapoznawcza)](sql-database-tutorial-clustering-model-deploy.md)