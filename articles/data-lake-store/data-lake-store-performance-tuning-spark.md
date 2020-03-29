---
title: Wskazówki dotyczące dostrajania wydajności usługi Azure Data Lake Storage Gen1 Spark | Dokumenty firmy Microsoft
description: Wskazówki dotyczące dostrajania wydajności usługi Azure Data Lake Storage Gen1 Spark
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: dc92e7d2fcc911aeb6d92b91dd2d430af3c502ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61436515"
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Wskazówki dotyczące dostrajania wydajności dla platformy Spark w usłudze HDInsight i usłudze Azure Data Lake Storage Gen1

Podczas dostrajania wydajności w programie Spark należy wziąć pod uwagę liczbę aplikacji, które będą uruchomione w klastrze.  Domyślnie można uruchomić 4 aplikacje jednocześnie w klastrze HDI (Uwaga: ustawienie domyślne może ulec zmianie).  Możesz zdecydować się na użycie mniejszej liczby aplikacji, aby zastąpić ustawienia domyślne i użyć większej liczby klastrów dla tych aplikacji.  

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące tworzenia jednego z nich, zobacz Wprowadzenie do [usługi Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Klaster usługi Azure HDInsight** z dostępem do konta Usługi Data Lake Storage Gen1. Zobacz [Tworzenie klastra HDInsight z pamięcią Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Upewnij się, że włączysz pulpit zdalny dla klastra.
* **Uruchamianie klastra Platformy Spark w pamięci masowej data lake gen1**.  Aby uzyskać więcej informacji, zobacz Analizowanie danych w u źródła [danych Gen1 za pomocą klastra hdinsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Wskazówki dotyczące dostrajania wydajności w programie Data Lake Storage Gen1**.  Aby zapoznać się z ogólnymi pojęciami dotyczącymi wydajności, zobacz [Wskazówki dotyczące dostrajania wydajności magazynu danych Lake Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance) 

## <a name="parameters"></a>Parametry

Podczas uruchamiania zadań platformy Spark oto najważniejsze ustawienia, które można dostroić, aby zwiększyć wydajność w programie Data Lake Storage Gen1:

* **Num-executors** — liczba równoczesnych zadań, które mogą być wykonywane.

* **Executor-memory** — ilość pamięci przydzielonej do każdego wykonawcy.

* **Executor-cores** - Liczba rdzeni przydzielonych do każdego wykonawcy.                     

**Wykonawcy num** Num-executors ustawi maksymalną liczbę zadań, które można uruchomić równolegle.  Rzeczywista liczba zadań, które można uruchomić równolegle jest ograniczona przez zasoby pamięci i procesora CPU dostępne w klastrze.

**Wykonawca-pamięć** Jest to ilość pamięci, która jest przydzielana do każdego wykonawcy.  Pamięć wymagana dla każdego wykonawcy jest zależna od zadania.  W przypadku złożonych operacji pamięć musi być wyższa.  W przypadku prostych operacji, takich jak odczyt i zapis, wymagania dotyczące pamięci będą niższe.  Ilość pamięci dla każdego wykonawcy można wyświetlić w Ambari.  W Ambari przejdź do spark i wyświetl kartę Konfiguracje.  

**Rdzenie executor** To ustawia ilość rdzeni używanych na executor, który określa liczbę równoległych wątków, które mogą być uruchamiane na wykonawcę.  Na przykład jeśli executor-cores = 2, a następnie każdy wykonawca można uruchomić 2 równoległe zadania w wykonawcy.  Rdzenie wykonawcy potrzebne będą zależne od zadania.  Ciężkie zadania we/wy nie wymagają dużej ilości pamięci na zadanie, więc każdy wykonawca może obsługiwać więcej równoległych zadań.

Domyślnie dwa wirtualne rdzenie YARN są definiowane dla każdego rdzenia fizycznego podczas uruchamiania programu Spark w programie HDInsight.  Liczba ta zapewnia dobrą równowagę współbieżności i ilość przełączania kontekstu z wielu wątków.  

## <a name="guidance"></a>Wskazówki

Podczas uruchamiania obciążeń analitycznych platformy Spark do pracy z danymi w urozwoju danych Lake Storage Gen1 zaleca się użycie najnowszej wersji usługi HDInsight, aby uzyskać najlepszą wydajność z pamięcią masową Data Lake Gen1. Gdy zadanie jest bardziej intensywne we/wy, niektóre parametry można skonfigurować w celu zwiększenia wydajności.  Data Lake Storage Gen1 to wysoce skalowalna platforma pamięci masowej, która może obsługiwać wysoką przepustowość.  Jeśli zadanie składa się głównie z odczytu lub zapisu, zwiększenie współbieżności dla we/wy do i z usługi Data Lake Storage Gen1 może zwiększyć wydajność.

Istnieje kilka ogólnych sposobów zwiększenia współbieżności dla zadań intensywnie korzystających z we/wy.

**Krok 1: Określ, ile aplikacji jest uruchomionych w klastrze** — powinieneś wiedzieć, ile aplikacji jest uruchomionych w klastrze, w tym bieżąca.  Wartości domyślne dla każdego ustawienia platformy Spark zakłada, że istnieją 4 aplikacje uruchomione jednocześnie.  W związku z tym będzie mieć tylko 25% klastra dostępne dla każdej aplikacji.  Aby uzyskać lepszą wydajność, można zastąpić wartości domyślne, zmieniając liczbę wykonawców.  

**Krok 2: Ustaw executor-memory** - pierwszą rzeczą, którą należy ustawić, jest wykonawca-pamięć.  Pamięć będzie zależna od zadania, które zamierzasz uruchomić.  Można zwiększyć współbieżność, przydzielając mniej pamięci na wykonawcę.  Jeśli widzisz wyjątki braku pamięci po uruchomieniu zadania, należy zwiększyć wartość tego parametru.  Jedną z alternatyw jest uzyskanie większej ilości pamięci przy użyciu klastra, który ma większe ilości pamięci lub zwiększenie rozmiaru klastra.  Więcej pamięci umożliwi użycie większej liczby executors, co oznacza większą współbieżność.

**Krok 3: Ustaw rdzenie executor** — dla obciążeń intensywnie we/wy, które nie mają złożonych operacji, dobrze jest zacząć od dużej liczby rdzeni executor, aby zwiększyć liczbę równoległych zadań na wykonawcę.  Ustawienie executor-cores do 4 jest dobry początek.   

    executor-cores = 4
Zwiększenie liczby rdzeni executor daje więcej równoległości, dzięki czemu można eksperymentować z różnych rdzeni executor.  W przypadku zadań, które mają bardziej złożone operacje, należy zmniejszyć liczbę rdzeni na wykonawcę.  Jeśli executor-cores jest ustawiony wyższy niż 4, następnie wyrzucania elementów bezużytecznych może stać się nieefektywne i obniżyć wydajność.

**Krok 4: Określ ilość pamięci YARN w klastrze** — te informacje są dostępne w Ambari.  Przejdź do pozycji YARN i wyświetl kartę Konfiguracje.  Pamięć YARN jest wyświetlana w tym oknie.  
Uwaga, gdy jesteś w oknie, można również zobaczyć domyślny rozmiar kontenera YARN.  Rozmiar kontenera YARN jest taki sam jak pamięć na parametr executor.

    Total YARN memory = nodes * YARN memory per node
**Krok 5: Obliczanie num-executors**

**Oblicz ograniczenie pamięci** — parametr num-executors jest ograniczony przez pamięć lub procesor CPU.  Ograniczenie pamięci zależy od ilości dostępnej pamięci YARN dla aplikacji.  Należy wziąć całkowitą pamięć YARN i podzielić, że przez executor-memory.  Ograniczenie musi zostać zdkalowane dla liczby aplikacji, aby śmy dzielili przez liczbę aplikacji.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**Oblicz ograniczenie procesora —** ograniczenie procesora jest obliczane jako całkowita liczba rdzeni wirtualnych podzielona przez liczbę rdzeni na wykonawcę.  Istnieją 2 rdzenie wirtualne dla każdego rdzenia fizycznego.  Podobnie jak w ograniczeniu pamięci, mamy podzielić przez liczbę aplikacji.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Ustaw num-executors** — parametr num-executors jest określany przez przyjęcie minimalnego ograniczenia pamięci i ograniczenia procesora CPU. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Ustawienie większej liczby num-executors nie musi zwiększyć wydajność.  Należy wziąć pod uwagę, że dodanie więcej executors doda dodatkowe obciążenie dla każdego dodatkowego wykonawcy, co może potencjalnie obniżyć wydajność.  Num-executors jest ograniczony przez zasoby klastra.    

## <a name="example-calculation"></a>Przykładowe obliczenie

Załóżmy, że masz obecnie klaster składający się z 8 węzłów D4v2, w których są uruchomione 2 aplikacje, w tym ten, który zamierzasz uruchomić.  

**Krok 1: Określ, ile aplikacji jest uruchomionych w klastrze** — wiesz, że masz w klastrze 2 aplikacje, w tym te, które zamierzasz uruchomić.  

**Krok 2: Ustaw executor-memory** — w tym przykładzie ustalimy, że 6GB pamięci egzekutora będzie wystarczające dla zadania intensywnego we/wy.  

    executor-memory = 6GB
**Krok 3: Ustaw rdzenie executor** - Ponieważ jest to zadanie intensywnie we/wy, możemy ustawić liczbę rdzeni dla każdego wykonawcy na 4.  Ustawienie rdzeni na wykonawcę na większe niż 4 może spowodować problemy z wyrzucaniem elementów bezużytecznych.  

    executor-cores = 4
**Krok 4: Określ ilość pamięci YARN w klastrze** — przechodzimy do Ambari, aby dowiedzieć się, że każdy D4v2 ma 25 GB pamięci YARN.  Ponieważ istnieje 8 węzłów, dostępna pamięć YARN jest mnożona przez 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Krok 5: Oblicz num-executors** — parametr num-executors jest określany przez podjęcie minimum ograniczenia pamięci i ograniczenia procesora CPU podzielonego przez # aplikacji uruchomionych na spark.    

**Oblicz ograniczenie pamięci** — ograniczenie pamięci jest obliczane jako całkowita pamięć YARN podzielona przez pamięć na wykonawcę.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**Obliczanie ograniczenia procesora** — ograniczenie procesora jest obliczane jako całkowita liczba rdzeni przędzy podzielona przez liczbę rdzeni na wykonawcę.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Ustawianie wykonawców num**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

