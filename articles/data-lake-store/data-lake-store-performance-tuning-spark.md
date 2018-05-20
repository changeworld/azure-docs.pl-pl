---
title: Dostosowywanie wskazówki dotyczące wydajności Spark usługi Azure Data Lake Store | Dokumentacja firmy Microsoft
description: Dostosowywanie wskazówki dotyczące wydajności Spark usługi Azure Data Lake Store
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
ms.openlocfilehash: a807bea13063d2a0b3c1c71ddb6c98aa2d2568d3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-store"></a>Wskazówki dotyczące platforma Spark w usłudze HDInsight i usługi Azure Data Lake Store dostrajania wydajności

Gdy dostrajania wydajności na Spark, należy wziąć pod uwagę liczbę aplikacje działające w klastrze.  Domyślnie program można uruchomić 4 aplikacji równocześnie na klaster HDI (Uwaga: domyślne ustawienie to może ulec zmianie).  Użytkownik chce korzystać z aplikacji mniej może zastępują ustawienia domyślne i użyć jednego klastra dla tych aplikacji.  

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Store**. Aby uzyskać instrukcje na temat go utworzyć, zobacz [wprowadzenie do usługi Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Klaster HDInsight Azure** z dostępem do konta usługi Data Lake Store. Zobacz [tworzenia klastra usługi HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Upewnij się, że włączenie pulpitu zdalnego dla klastra.
* **Uruchomiona klastra Spark w usłudze Azure Data Lake Store**.  Aby uzyskać więcej informacji, zobacz [klastra Spark w usłudze HDInsight używany do analizowania danych w usłudze Data Lake Store](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Wytyczne dotyczące ADLS dostrajania wydajności**.  Pojęcia dotyczące ogólnej wydajności, aby zapoznać [Data Lake magazynu dostrajanie wytyczne dotyczące wydajności](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance) 

## <a name="parameters"></a>Parametry

Podczas uruchamiania zadań Spark, poniżej przedstawiono najważniejsze ustawienia, które można przedstawić w celu zwiększenia wydajności na ADLS:

* **Liczba modułów** — liczba równoczesnych zadań, które mogą być wykonywane.

* **Moduł wykonujący pamięci** — ilość pamięci przydzielona do każdego Moduł wykonujący.

* **Moduł wykonujący rdzeni** — liczba rdzeni przydzielone do każdego Moduł wykonujący.                     

**Liczba modułów** modułów Num ustawi maksymalna liczba zadań, które można uruchomić równolegle.  Rzeczywista liczba zadań, które można uruchomić równolegle jest ograniczone przez pamięć i zasoby Procesora dostępne w klastrze.

**Moduł wykonujący pamięci** to ilość pamięci przydzielona do każdego Moduł wykonujący.  Z pamięcią potrzebną dla każdego modułu wykonującego jest zależny od zadania.  W przypadku złożonych operacji pamięć musi być wyższy.  Proste operacje, takie jak odczytu i zapisu wymagania dotyczące pamięci będzie niższa.  Ilość pamięci dla każdego modułu wykonującego można wyświetlić w narzędzia Ambari.  W Ambari przejdź do Spark i wyświetl kartę Configs.  

**Moduł wykonujący rdzeni** to ustawia liczbę rdzeni na program, który określa liczbę równoległych wątków, które mogą być uruchamiane na moduł wykonujący.  Na przykład jeśli moduł wykonujący rdzeni = 2, a następnie każdy moduł wykonujący można uruchamiać zadania równoległych 2 wykonujący.  Moduł wykonujący rdzenie potrzebne będzie zależało od zadania.  Duże zadania We/Wy nie wymaga dużej ilości pamięci poszczególnych zadań, dzięki czemu można każdy moduł wykonujący może obsługiwać więcej zadań równoległych.

Domyślnie dwa rdzenie YARN wirtualnych są definiowane dla każdego rdzeni fizycznych uruchomionej w usłudze HDInsight Spark.  Liczba ta zapewnia kompromis concurrecy oraz ilości wiele wątków do przełączania kontekstu.  

## <a name="guidance"></a>Wskazówki

Podczas uruchamiania obciążeń analizy dużych do pracy z danymi w usłudze Data Lake Store Spark, zaleca się, że używasz najnowszej wersji usługi HDInsight można uzyskać najlepszą wydajność dzięki usłudze Data Lake Store. Gdy zadanie jest bardziej intensywne operacje We/Wy, niektórych parametrów można skonfigurować w celu zwiększenia wydajności.  Azure Data Lake Store jest platformy wysokiej skalowalności magazynu, który może obsługiwać wysokiej przepływności.  Jeśli zadanie zawiera głównie do odczytu lub zapisu, następnie zwiększenie współbieżności dla operacji We/Wy do i z usługi Azure Data Lake Store może zwiększyć wydajność.

Istnieje kilka sposobów ogólne, aby zwiększyć współbieżność zadań intensywne operacje We/Wy.

**Krok 1: Określanie, jak wiele aplikacji uruchomionych w klastrze** — należy wiedzieć, jak wiele aplikacji są uruchomione w klastrze, w tym bieżący.  Wartości domyślne dla każdej platformy Spark ustawienia obowiązuje założenie, które istnieją 4 równoczesne działanie aplikacji.  W związku z tym będzie mieć tylko 25% klastra, które są dostępne dla każdej aplikacji.  Aby uzyskać lepszą wydajność, można zastąpić wartości domyślne, zmieniając liczba modułów.  

**Krok 2: Ustawić modułu wykonującego pamięci** — najpierw ustawić jest pamięci Moduł wykonujący.  Pamięć będzie zależna od zadanie, które zamierzasz uruchomić.  Można zwiększyć współbieżność przydzielając mniejszą ilość pamięci na moduł wykonujący.  Jeśli widzisz poza wyjątkami pamięci podczas uruchamiania zadania, należy zwiększyć wartość tego parametru.  Jeden alternatywą jest uzyskanie większej ilości pamięci za pomocą klastra, który ma większych ilości pamięci lub zwiększyć rozmiar klastra.  Więcej pamięci umożliwi więcej modułów, które mają być używane, co oznacza, że więcej współbieżności.

**Krok 3: Ustawianie rdzeni Moduł wykonujący** — WE dla/Wy intensywnych obciążeń, które nie mają złożonych operacji, zaleca się rozpoczynać się od dużej liczby rdzeni Moduł wykonujący zwiększyć liczbę równoległych zadań na moduł wykonujący.  Ustawienie modułu wykonującego rdzenie 4 stanowi dobry początek.   

    executor-cores = 4
Zwiększenie liczby rdzeni Moduł wykonujący zapewni więcej równoległości, możesz eksperymentować z różnych rdzeni Moduł wykonujący.  Dla zadania, których bardziej złożonych operacji należy zmniejszyć liczbę rdzeni na moduł wykonujący.  Jeśli moduł wykonujący rdzeni jest wyższa niż 4, następnie wyrzucanie elementów bezużytecznych mogą się nieefektywne i obniżyć wydajność.

**Krok 4: Określanie ilości pamięci YARN w klastrze** — te informacje są dostępne w narzędzia Ambari.  Przejdź do YARN, a następnie Wyświetl kartę Configs.  Pamięć YARN jest wyświetlany w tym oknie.  
Uwaga: podczas pracy w oknie można również sprawdzić rozmiar domyślny kontener YARN.  Rozmiar kontenera YARN jest taka sama jak ilość pamięci na parametru Moduł wykonujący.

    Total YARN memory = nodes * YARN memory per node
**Krok 5: Oblicz num modułów**

**Oblicz ograniczenie pamięci** — parametr num modułów jest ograniczony przez pamięci lub procesora CPU.  Ograniczenie pamięci jest określany przez ilość dostępnej pamięci YARN dla aplikacji.  Należy podjąć YARN pamięć i przez moduł wykonujący pamięci.  Ograniczenie należy cofnąć skalowana dla wielu aplikacji, możemy podzielić przez kod aplikacji.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**Obliczanie Procesora ograniczenia** — ograniczenie Procesora jest obliczany jako wartość całkowita liczba rdzeni wirtualnego rozdzielonych liczba rdzeni w moduł wykonujący.  Istnieją 2 rdzenie wirtualnych dla poszczególnych rdzeni fizycznych.  Podobnie jak ograniczenie pamięci, mamy dzielenie według liczby aplikacji.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Ustaw modułów num** — parametr num modułów jest określana przez pobranie minimum ograniczenia pamięci i ograniczenia procesora CPU. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Ustawienie większej liczby modułów num nie zawsze poprawia wydajność.  Należy rozważyć dodanie więcej modułów spowoduje dodanie czy bardzo ogólnych, dla każdego dodatkowego Moduł wykonujący, które potencjalnie może zmniejszyć wydajność.  Liczba modułów jest ograniczone przez zasoby klastra.    

## <a name="example-calculation"></a>Przykład obliczeń

Załóżmy, że masz obecnie klaster składa się z 8 węzłów D4v2 uruchomionym 2 aplikacji, w tym zamierzasz uruchomić.  

**Krok 1: Określanie, jak wiele aplikacji uruchomionych w klastrze** — wiesz, że masz 2 aplikacji w klastrze, w tym zamierzasz uruchomić.  

**Krok 2: Ustawić modułu wykonującego pamięci** — na przykład możemy ustalić, czy 6 GB pamięci Moduł wykonujący są wystarczające dla zadania intensywne operacje We/Wy.  

    executor-memory = 6GB
**Krok 3: Ustawianie rdzeni Moduł wykonujący** — ponieważ jest to zadanie intensywne operacje We/Wy, możemy ustawić liczbę rdzeni dla każdego modułu wykonującego 4.  Ustawienie rdzeni w jednym Moduł wykonujący większych niż 4 może spowodować problemy z kolekcji pamięci.  

    executor-cores = 4
**Krok 4: Określanie ilości pamięci YARN w klastrze** — firma Microsoft, przejdź do narzędzia Ambari, aby dowiedzieć się, że każdy D4v2 ma 25 GB pamięci YARN.  Ponieważ 8 węzłów, dostępnej pamięci YARN jest mnożona przez 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Krok 5: Oblicz modułów num** — parametr num modułów jest określana przez pobranie minimalnym ograniczeniu pamięci i ograniczenie Procesora rozdzielonych liczba aplikacji uruchomionych na Spark.    

**Oblicz ograniczenie pamięci** — ograniczenie pamięci jest obliczany jako wartość całkowita pamięć YARN podzielona przez ilość pamięci na moduł wykonujący.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**Obliczanie Procesora ograniczenia** — ograniczenie Procesora jest obliczany jako rdzenie całkowita yarn rozdzielonych liczba rdzeni w moduł wykonujący.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Zestaw num modułów**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

