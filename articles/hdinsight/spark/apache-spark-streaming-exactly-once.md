---
title: Utwórz zadania przesyłania strumieniowego Spark dokładnie — raz zdarzenia przetwarzania - Azure HDInsight | Dokumentacja firmy Microsoft
description: Jak można skonfigurować przesyłania strumieniowego Spark, aby przetworzyć zdarzenie raz i tylko raz.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ramoha
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: ramoha
ms.openlocfilehash: 48b1d3811f3a8f6190c58e9646ab0d820859fc21
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164866"
---
# <a name="create-spark-streaming-jobs-with-exactly-once-event-processing"></a>Utwórz zadania przesyłania strumieniowego Spark dokładnie — raz zdarzenia przetwarzania

Przesyłanie strumieniowe przetwarzanie aplikacji wykonać różne podejścia, sposobu ich obsługi ponownego przetwarzania komunikatów po awarii niektórych w systemie:

* Co najmniej raz: każdy komunikat jest gwarantowana do przetwarzania, ale mogą uzyskać przetwarzane więcej niż raz.
* Co najwyżej raz: każdy komunikat może lub nie mogą być przetwarzane. Jeśli komunikat jest przetwarzany, jego jest przetwarzany tylko raz.
* Dokładnie raz: każdy komunikat jest gwarantowana na przetworzenie raz i tylko raz.

W tym artykule przedstawiono sposób konfigurowania do osiągnięcia dokładnie przesyłania strumieniowego Spark — po zakończeniu przetwarzania.

## <a name="exactly-once-semantics-with-spark-streaming"></a>Dokładnie — raz semantyki z przesyłania strumieniowego Spark

Najpierw należy wziąć pod uwagę jak wszystkich punktów awarii systemu, uruchom ponownie po problemu i jak można uniknąć utraty danych. Aplikacja usługi przesyłania strumieniowego Spark ma:

* Źródło danych wejściowych
* Jeden lub więcej procesów odbiornika, które pobierają dane ze źródła danych wejściowych
* Zadania, które przetwarzają dane
* Ujście danych wyjściowych
* Proces sterownik, który zarządza długotrwałe zadania

Dokładnie — po semantyki wymagać, aby nie zostały utracone w dowolnym momencie żadne dane, przetwarzanie wiadomości jest ponownego uruchamiania, niezależnie od tego, gdzie występuje błąd.

### <a name="replayable-sources"></a>Przechwytywaniem źródeł

Źródło aplikacji przesyłania strumieniowego Spark jest odczytywania zdarzeń z musi być *przechwytywaniem*. Oznacza to, że w przypadku, gdy wiadomość została pobrana, ale następnie system nie powiodła się, zanim komunikat może być utrwalona lub przetworzone, źródło musi zapewniać tę samą wiadomość ponownie.

Na platformie Azure zarówno usługi Azure Event Hubs, jak i Kafka w usłudze HDInsight zapewnia przechwytywaniem źródeł. Innym przykładem przechwytywaniem źródła jest systemem odpornej na uszkodzenia plików, takich jak system plików HDFS, obiektach blob magazynu Azure, lub Azure Data Lake Store, w którym wszystkie dane są przechowywane w nieskończoność i w dowolnym momencie możesz ponownie może odczytywać dane w całości.

### <a name="reliable-receivers"></a>Odbiorniki niezawodnej

W przesyłania strumieniowego Spark, źródeł, takich jak usługi Event Hubs i Kafka *niezawodnej odbiorcy*, gdzie każdy odbiorca przechowuje informacje o postępie odczytywanie źródła. Odbiornik niezawodnej utrwala swój stan do magazynu w odpornej na uszkodzenia, dozorcy albo w przesyłania strumieniowego Spark zapisywane w systemie plików HDFS punkty kontrolne. W przypadku takich odbiornika awarii i jest nowszy ponownie uruchomione, jego mogą odbierać miejsca, w którym.

### <a name="use-the-write-ahead-log"></a>Korzystanie z wyprzedzeniem zapisu dziennika

Przesyłanie strumieniowe Spark obsługuje korzystanie z wyprzedzeniem zapisu dziennika, gdzie każdego otrzymane zdarzenie jest najpierw są zapisywane w katalogu punktu kontrolnego platforma Spark w odpornej na uszkodzenia magazynu, a następnie przechowywane w odpornej rozproszonych zestawu danych (RDD). Na platformie Azure odpornej na uszkodzenia magazynu jest system plików HDFS obsługiwanej przez usługi Azure Storage lub usługi Azure Data Lake Store. W aplikacji przesyłania strumieniowego Spark zapisu z wyprzedzeniem dziennika jest włączona dla wszystkich odbiorników przez ustawienie `spark.streaming.receiver.writeAheadLog.enable` ustawienia konfiguracji do `true`. Dziennik zapisu z wyprzedzeniem zapewnia odporność na uszkodzenia niepowodzeń sterownika i modułów.

Dla procesów roboczych uruchomionych zadań przed dane zdarzenia każdy RDD jest zgodnie z definicją zarówno replikowane i rozproszone na wielu pracowników. Jeśli zadanie nie powiedzie się, ponieważ proces roboczy, uruchomienie jej awaria, zadanie zostanie ponownie uruchomiony na innego elementu roboczego z repliką dane zdarzenia, więc zdarzenia nie zostaną utracone.

### <a name="use-checkpoints-for-drivers"></a>Użyj punktów kontrolnych dla sterowników

Sterowniki zadania należy uruchamiać ponownie. Jeśli sterownik uruchamiania aplikacji przesyłania strumieniowego Spark ulegnie awarii, potrzebny w dół z nim wszystkie uruchomione odbiornikami, zadania i wszelkie RDDs przechowywania danych zdarzenia. W takim przypadku należy można było zapisać postęp zadania, aby można go ponownie później. Jest to osiągane przez tworzenie punktów kontrolnych skierowane acykliczne Graph (DAG) programu DStream okresowo do magazynu w odpornej na uszkodzenia. Metadane DAG obejmuje konfigurację używaną do tworzenia aplikacji przesyłania strumieniowego, operacje, które definiują aplikację i wszystkie instancje, które są umieszczone w kolejce, lecz nie została jeszcze ukończona. Te metadane umożliwia sterownik nie powiodło się z informacji punktu kontrolnego ponownego uruchomienia. Po ponownym uruchomieniu sterownika uruchomi nowy odbiorniki się odzyskać dane zdarzenia do RDDs z wyprzedzeniem zapisu dziennika.

Punkty kontrolne są włączone w przesyłania strumieniowego Spark w dwóch krokach. 

1. W obiekcie StreamingContext należy skonfigurować ścieżkę magazynu punkty kontrolne:

    Val ssc = nowe StreamingContext (spark, Seconds(1)) ssc.checkpoint("/path/to/checkpoints")

    W usłudze HDInsight należy zapisywać te punkty kontrolne do magazynu domyślnego dołączony do klastra, Magazyn Azure lub usługi Azure Data Lake Store.

2. Następnie należy określić interwał punktu kontrolnego (w sekundach) na DStream. W każdym interwale dane o stanie pochodzących z zdarzenia wejściowego jest utrwalana w magazynie. Stan utrwalony danych można ograniczyć obliczenia wymagane podczas odbudowywania stanu ze źródła zdarzenia.

    Wiersze Val = ssc.socketTextStream ("Nazwa hosta", 9999) lines.checkpoint(30) ssc.start() ssc.awaitTermination()

### <a name="use-idempotent-sinks"></a>Wychwytywanie idempotentności użycia

Zbiornika docelowego, do którego zadania zapisuje wyniki musi być w stanie obsłużyć sytuację, w którym jest on podawany takiego samego wyniku więcej niż raz. Obiekt sink musi mieć możliwość wykrywania takich duplikowania i je zignorować. *Idempotentności* zbiornika można wywoływać wielokrotnie z tymi samymi danymi żadne zmiany stanu.

Możesz utworzyć wychwytywanie idempotentności zaimplementowanie logikę, która najpierw sprawdza istnienie przychodzące wynik w magazynu danych. Jeśli wynik już istnieje, zapisu powinien zostać wyświetlony się pomyślnie z punktu widzenia zadania Spark, ale w rzeczywistości magazynie danych zignorowany zduplikowanych danych. Jeśli wynik nie istnieje, obiekt sink należy wstawić nowy wynik do jego magazynu. 

Na przykład można użyć procedury składowanej z bazy danych SQL Azure, która wstawia zdarzenia do tabeli. Tę procedurę składowaną najpierw wyszukiwana zdarzenia przez pola klucza i tylko wtedy, gdy nie pasujące zdarzenie znaleziono rekord wstawione do tabeli.

Innym przykładem jest za pomocą systemu plików podzielonym na partycje, takich jak obiekty BLOB magazynu Azure lub usługi Azure Data Lake store. W takim przypadku logiki sink nie trzeba Sprawdź, czy istnieje plik. Jeśli istnieje plik reprezentujący zdarzenia, jest po prostu zastępowany z tymi samymi danymi. W przeciwnym razie nowy plik jest tworzony w ścieżce obliczanej.

## <a name="next-steps"></a>Kolejne kroki

* [Platforma Spark przesyłania strumieniowego — omówienie](apache-spark-streaming-overview.md)
* [Tworzenie zadania przesyłania strumieniowego w iskrowym wysokiej dostępności w PRZĘDZY](apache-spark-streaming-high-availability.md)
