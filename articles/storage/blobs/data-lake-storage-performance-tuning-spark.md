---
title: Usługa Azure Data Lake Storage Gen2 wydajności aparatu Spark wytyczne dotyczące dostosowywania | Dokumentacja firmy Microsoft
description: Usługa Azure Data Lake Storage Gen2 wydajności aparatu Spark wytyczne dotyczące dostosowywania
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: feefcf4f9f4448ab2b36c415cb745fd98277eb28
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939336"
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Wskazówki dotyczące platformy Spark w HDInsight i Azure Data Lake Storage Gen2 dostrajania wydajności

Podczas dostosowywania wydajności na platformie Spark, należy wziąć pod uwagę liczba aplikacji uruchomionych w klastrze.  Domyślnie program można uruchomić 4 aplikacji jednocześnie w klastrze usługi HDI (Uwaga: domyślne ustawienie to może ulec zmianie).  Użytkownik chce korzystać z mniejszej liczby aplikacji, dzięki czemu mogą zastąpić ustawienia domyślne i użycia klastra dla tych aplikacji.  

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Storage Gen2**. Aby uzyskać instrukcje na temat jej tworzenia, zobacz [Szybki Start: Tworzenie konta magazynu Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
* **Klaster usługi Azure HDInsight** dzięki dostępowi do konta Data Lake Storage Gen2. Zobacz [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Upewnij się, że włączenie pulpitu zdalnego dla klastra.
* **Systemem klastra Spark Data Lake Storage Gen2**.  Aby uzyskać więcej informacji, zobacz [klastra HDInsight korzystanie z platformy Spark do analizy danych Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Wytyczne dotyczące Data Lake Storage Gen2 dostrajania wydajności**.  Pojęcia związane z ogólnej wydajności, zobacz [Data Lake Storage Gen2 dostrajania wskazówki dotyczące wydajności](data-lake-storage-performance-tuning-guidance.md) 

## <a name="parameters"></a>Parametry

Uruchamiając zadania Spark, poniżej przedstawiono najważniejsze ustawienia, które mogą być dostosowane do zwiększenia wydajności na Data Lake Storage Gen2:

* **Liczba programów przetwarzających** — liczbę współbieżnych zadań, które mogą być wykonywane.

* **Wykonawca pamięci** — ilość pamięci przydzielona do każdego wykonawcy.

* **Rdzenie wykonawca** — liczby rdzeni, przydzielone do każdego wykonawcy.                     

**Liczba programów przetwarzających** liczba programów przetwarzających ustawi maksymalną liczbę zadań, które mogą działać równolegle.  Rzeczywista liczba zadań, które mogą działać równolegle jest ograniczone przez pamięci i zasobów procesora CPU jest dostępne w klastrze.

**Wykonawca pamięci** to ilość pamięci przydzielonej do każdej funkcji wykonawczej.  Pamięć wymagane dla każdego wykonawcy jest zależna od zadania.  W przypadku złożonych operacji pamięci musi być wyższa.  W przypadku prostych operacji takich jak Odczyt i zapis wymagania dotyczące pamięci będzie niższa.  Ilość pamięci dla każdego wykonawcy mogą być wyświetlane w Ambari.  W Ambari przejdź do platformy Spark, a następnie Wyświetl kartę konfiguracje.  

**Rdzenie wykonawca** to ustawia liczbę wykorzystane w ramach przetwarzania, który określa liczby wątków równoległych, które mogą być uruchamiane na wykonawca rdzeni.  Na przykład jeśli program wykonujący rdzeni = 2, następnie każdy program wykonujący uruchomić 2 zadań równoległych w wykonawcę.  Rdzenie wykonawca potrzebne będzie zależny od zadania.  Zadań samochodów We/Wy nie wymagają dużej ilości pamięci dla zadań, dzięki czemu każdy wykonawca może obsługiwać więcej zadań równoległych.

Domyślnie dwa rdzenie wirtualne YARN są definiowane na każdy rdzeń fizyczny uruchamianego na HDInsight Spark.  Liczba ta oferuje dobry kompromis między współbieżność i ilości z wielu wątków do przełączania kontekstu.  

## <a name="guidance"></a>Wskazówki

Podczas uruchamiania aparatu Spark obciążeń wynikających z analizy do pracy z danymi w Data Lake Storage Gen2, zaleca się używać najnowszej wersji HDInsight w celu uzyskania najlepszej wydajności z Data Lake Storage Gen2. Gdy zadanie jest bardziej intensywne operacje We/Wy, niektóre parametry mogą być skonfigurowane do zwiększenia wydajności.  Data Lake Storage Gen2 to platforma magazyn o wysokim stopniu skalowalności, która może obsłużyć dużą przepływnością.  Jeśli zadanie składa się głównie z odczytu lub zapisu, następnie zwiększenia współbieżności dla operacji We/Wy do i z Data Lake Storage Gen2 może zwiększyć wydajność.

Istnieje kilka sposobów ogólne w celu zwiększenia współbieżności zadań intensywnie korzystających z operacji We/Wy.

**Krok 1. Określić, jak wiele aplikacji jest uruchomionych w klastrze** — należy wiedzieć, jak wiele aplikacji jest uruchomionych w klastrze, w tym bieżący.  Wartości domyślne dla każdej platformy Spark, ustawienia obowiązuje założenie, które są 4 aplikacji uruchomionych jednocześnie.  W związku z tym będzie mieć tylko 25% klastra, które są dostępne dla każdej aplikacji.  Aby uzyskać lepszą wydajność, można zastąpić ustawienia domyślne, zmieniając liczbie funkcji wykonawczych.  

**Krok 2. Ustaw program wykonujący pamięci** — pierwszą rzeczą, aby ustawić jest pamięci wykonawcy.  Pamięć będzie zależny od zadania, które zamierzasz uruchomić.  Możesz zwiększyć współbieżności, przydzielając mniejszą ilość pamięci na wykonawcy.  Jeśli widzisz poza wyjątki pamięci podczas uruchamiania zadania, należy zwiększyć wartość tego parametru.  Jeden alternatywa to uzyskanie większej ilości pamięci za pomocą klastra, który ma większej ilości pamięci lub zwiększenie rozmiaru klastra.  Większa ilość pamięci umożliwi więcej executors ma być używany, co oznacza, że uzyskać większą współbieżność.

**Krok 3. Ustaw program wykonujący rdzeni** — dla obciążeń wejścia/wyjścia o znacznym wykorzystaniu, które nie mają złożonych operacji, warto rozpocząć z dużą liczbą rdzeni wykonawcy, aby zwiększyć liczbę zadań równoległych na wykonawcy.  Ustawienie 4 rdzenie wykonawca stanowi dobry początek.   

    executor-cores = 4
Zwiększenie liczby rdzeni wykonawca zapewni więcej równoległości, dzięki czemu możesz eksperymentować z różnych rdzeni wykonawcy.  Dla zadań, które mają bardziej złożonych operacji należy zmniejszyć liczbę rdzeni na wykonawcy.  Jeśli wykonawca rdzeni jest większa niż 4, następnie wyrzucania elementów bezużytecznych może być obsługiwane mało wydajnie i obniżyć wydajność.

**Krok 4. Określić ilość pamięci usługi YARN w klastrze** — te informacje są dostępne w Ambari.  Przejdź do usługi YARN i wyświetlić kartę konfiguracje.  W tym oknie wyświetlane jest pamięci usługi YARN.  
Należy pamiętać podczas pracy w tym oknie można również wyświetlić domyślny rozmiar kontenera YARN.  Rozmiar kontenera YARN jest taka sama jak ilość pamięci na parametr funkcji wykonawczej.

    Total YARN memory = nodes * YARN memory per node
**Krok 5. Oblicz liczba programów przetwarzających**

**Oblicz ograniczenia pamięci** — parametr liczba programów przetwarzających jest ograniczony przez pamięci lub procesora CPU.  Ograniczenie pamięci jest określany przez ilość dostępnej pamięci usługi YARN dla aplikacji.  Należy wykonać całkowity rozmiar pamięci usługi YARN i przez program wykonujący pamięci.  Ograniczenie musi być cofnąć skalowanych liczby aplikacji, więc możemy dzielnikiem liczby aplikacji.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**Oblicz ograniczenie użycia Procesora** — ograniczenie procesora CPU jest obliczany jako łączna liczba rdzeni wirtualnych dzielona przez liczbę rdzeni na wykonawcy.  Istnieją 2 rdzeni wirtualnych na każdy rdzeń fizyczny.  Podobnie jak ograniczenia pamięci, mamy dzielnikiem liczby aplikacji.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Ustaw liczba programów przetwarzających** — parametr liczba programów przetwarzających jest określana przez pobranie wartości minimalnej ograniczenia pamięci i ograniczenia procesora CPU. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Ustawienie większej liczby liczba programów przetwarzających nie zawsze poprawia wydajność.  Należy rozważyć, czy dodanie więcej executors spowoduje dodanie dodatkowych obciążenie dla każdego dodatkowego przetwarzania, które potencjalnie mogą obniżyć wydajność.  Liczba programów przetwarzających jest ograniczone przez zasoby klastra.    

## <a name="example-calculation"></a>Przykład obliczania

Załóżmy, że masz już klaster składa się z 8 węzłów D4v2 uruchomionym 2 w tym co ma do uruchamiania aplikacji.  

**Krok 1. Określić, jak wiele aplikacji jest uruchomionych w klastrze** — wiesz, że masz 2 aplikacji w klastrze, w tym alert zamierzasz uruchomić.  

**Krok 2. Ustaw program wykonujący pamięci** — w tym przykładzie określamy, czy 6 GB pamięci funkcji wykonawczej są wystarczające dla zadania intensywnie korzystających z operacji We/Wy.  

    executor-memory = 6GB
**Krok 3. Ustaw program wykonujący rdzeni** — ponieważ jest to zadanie intensywnie korzystających z operacji We/Wy, możemy ustawić liczbę rdzeni dla każdego wykonawcy do 4.  Ustawianie rdzeni wykonawca większych niż 4 może spowodować problemy z kolekcji wyrzucania elementów.  

    executor-cores = 4
**Krok 4. Określić ilość pamięci usługi YARN w klastrze** — firma Microsoft, przejdź do narzędzia Ambari, aby dowiedzieć się, że każdy D4v2 ma 25 GB pamięci usługi YARN.  Ponieważ 8 węzłów, dostępnej pamięci usługi YARN jest mnożony przez 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Krok 5. Oblicz liczba programów przetwarzających** — parametr liczba programów przetwarzających jest określana przez wykonanie co najmniej ograniczenia pamięci i ograniczenie użycia Procesora podzielona przez liczba aplikacji uruchomionych na platformie Spark.    

**Oblicz ograniczenia pamięci** — ograniczenie pamięci jest obliczany jako podzielona przez ilość pamięci na wykonawca całkowitej ilości pamięci usługi YARN.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**Oblicz ograniczenie użycia Procesora** — ograniczenie procesora CPU jest obliczany jako rdzeni całkowita yarn dzielona przez liczbę rdzeni na wykonawcy.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Liczba set-programów przetwarzających**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

