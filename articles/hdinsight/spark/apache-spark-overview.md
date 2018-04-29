---
title: Wprowadzenie do platformy Spark w usłudze Azure HDInsight | Microsoft Docs
description: Ten artykuł przedstawia wprowadzenie do platformy Spark w usłudze HDInsight i różne scenariusze korzystania z klastra Spark w usłudze HDInsight.
keywords: co to jest apache spark,klaster spark,wprowadzenie do platformy spark,platforma spark w usłudze hdinsight
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 82334b9e-4629-4005-8147-19f875c8774e
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 12/13/2017
ms.author: nitinme
ms.openlocfilehash: 783cbd673231694b09bd27942799c1fd92e09a46
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="introduction-to-spark-on-hdinsight"></a>Wprowadzenie do platformy Spark w usłudze HDInsight

Ten artykuł przedstawia wprowadzenie do platformy Spark w usłudze HDInsight. <a href="http://spark.apache.org/" target="_blank">Apache Spark</a> to platforma przetwarzania równoległego typu open source, która obsługuje przetwarzanie w pamięci w celu zwiększania wydajności aplikacji do analizy danych big data. Klaster Spark w usłudze HDInsight jest zgodny z usługami Azure Storage (WASB) i Azure Data Lake Store. Dzięki temu istniejące dane przechowywane na platformie Azure można łatwo przetwarzać za pomocą klastra Spark.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Tworząc klaster Spark w usłudze HDInsight, tworzysz zasoby obliczeniowe platformy Azure z zainstalowaną i skonfigurowaną platformą Spark. Utworzenie klastra Spark w usłudze HDInsight trwa tylko około 10 minut. Dane, które mają być przetwarzane, są przechowywane w usłudze Azure Storage lub Azure Data Lake Store. Zobacz temat [Korzystanie z usługi Azure Storage z usługą HDInsight](../hdinsight-hadoop-use-blob-storage.md).

![Platforma Spark: ujednolicona struktura](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="spark-vs-traditional-mapreduce"></a>Platforma Spark a tradycyjna technologia MapReduce

Co sprawia, że platforma Spark działa szybko? Na czym polegają różnice między architekturą platformy Apache Spark a tradycyjną technologią MapReduce, które zapewniają lepszą wydajność udostępniania danych?

![Tradycyjna technologia MapReduce a platforma Spark](./media/apache-spark-overview/mapreduce-vs-spark.png)

Platforma Spark udostępnia typom pierwotnym możliwość używania klastrów obliczeniowych korzystających z funkcji przetwarzania w pamięci. W ramach zadania Spark dane można załadować do pamięci podręcznej i wielokrotnie wykonywać zapytania na tych danych, co działa znacznie szybciej niż w systemach opartych na operacjach dyskowych. Platforma Spark obsługuje również integrację z językiem programowania Scala, co pozwala manipulować rozproszonymi zestawami danych jak kolekcjami lokalnymi. Nie ma potrzeby, aby wszystkie elementy były obejmowane strukturami operacji mapowania i redukcji.

Udostępnianie danych między operacjami platformy Spark przebiega szybciej, ponieważ dane znajdują się w pamięci. Z kolei w usłudze Hadoop dane są udostępniane za pomocą systemu plików HDFS, co wydłuża czas ich przetwarzania.

## <a name="what-is-apache-spark-on-azure-hdinsight"></a>Co to jest klaster Apache Spark w usłudze Azure HDInsight?
Klastry Spark w usłudze HDInsight oferują w pełni zarządzaną usługę Spark. Poniżej przedstawiono korzyści związane z utworzeniem klastra Spark w usłudze HDInsight.

| Cecha | Opis |
| --- | --- |
| Łatwość tworzenia klastrów Spark |Nowy klaster Spark w usłudze HDInsight można utworzyć w kilka minut przy użyciu witryny Azure Portal, programu Azure PowerShell lub zestawu .NET SDK usługi HDInsight. Zobacz temat [Wprowadzenie do klastra Spark w usłudze HDInsight](apache-spark-jupyter-spark-sql.md) |
| Łatwość obsługi |Klaster Spark w usłudze HDInsight zawiera notesy Jupyter i Zeppelin. Można ich używać do interakcyjnego przetwarzania danych i wizualizacji.|
| Interfejsy API REST |Klastry Spark w usłudze HDInsight obejmują [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), czyli serwer zadań Spark oparty na interfejsie API REST do zdalnego przesyłania i monitorowania zadań. |
| Obsługa usługi Azure Data Lake Store | Klaster Spark w usłudze HDInsight można skonfigurować do korzystania z usługi Azure Data Lake Store jako magazynu dodatkowego, a także jako magazynu podstawowego (wyłącznie przy użyciu klastrów HDInsight 3.5). Aby uzyskać więcej informacji o usłudze Data Lake — magazyn, zobacz temat [Przegląd usługi Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md). |
| Integracja z usługami Azure |Klaster Spark w usłudze HDInsight zawiera łącznik usługi Azure Event Hubs. Klienci mogą tworzyć aplikacje do przesyłania strumieniowego przy użyciu usługi Event Hubs w uzupełnieniu do oprogramowania [Kafka](http://kafka.apache.org/), które jest już dostępne w ramach platformy Spark. |
| Obsługa platformy R Server | Możesz skonfigurować platformę R Server w klastrze Spark w usłudze HDInsight, aby uruchamiać rozproszone obliczenia R z szybkością zapewnianą przez klaster Spark. Aby uzyskać więcej informacji, zobacz temat [Rozpoczęcie pracy z platformą R Server w usłudze HDInsight](../r-server/r-server-get-started.md). |
| Integracja ze zintegrowanymi środowiskami projektowymi innych firm | Usługa HDInsight zapewnia wtyczki dla takich zintegrowanych środowisk projektowych, jak IntelliJ IDEA i Eclipse, które umożliwiają tworzenie i przesyłanie aplikacji do klastra Spark w usłudze HDInsight. Aby uzyskać więcej informacji, zobacz tematy [Korzystanie z zestawu narzędzi platformy Azure dla środowiska IntelliJ IDEA](apache-spark-intellij-tool-plugin.md) i [Korzystanie z zestawu narzędzi platformy Azure dla środowiska Eclipse](apache-spark-eclipse-tool-plugin.md).|
| Zapytania jednoczesne |Klastry Spark w usłudze HDInsight obsługują zapytania jednoczesne. Dzięki temu wielu zapytań od jednego użytkownika lub wiele zapytań od różnych użytkowników i aplikacji może współdzielić te same zasoby klastra. |
| Buforowanie na dyskach SSD |Istnieje możliwość buforowania danych w pamięci lub na dyskach SSD podłączonych do węzłów klastra. Buforowanie w pamięci zapewnia najlepszą wydajność zapytań, ale może być kosztowne. Buforowanie na dyskach SSD stanowi doskonałe rozwiązanie umożliwiające poprawę wydajności zapytań bez konieczności tworzenia klastra o rozmiarze obejmującym cały zestaw danych w pamięci. |
| Integracja z narzędziami do analizy biznesowej |Klastry Spark w usłudze HDInsight zawierają łączniki dla narzędzi do analizy biznesowej danych, takich jak [Power BI](http://www.powerbi.com/) i [Tableau](http://www.tableau.com/products/desktop). |
| Wstępnie załadowane biblioteki Anaconda |Klastry Spark w usłudze HDInsight są dostarczane z wstępnie zainstalowanymi bibliotekami Anaconda. Platforma [Anaconda](http://docs.continuum.io/anaconda/) dostarcza prawie 200 bibliotek do uczenia maszynowego, analizy danych, wizualizacji itp. |
| Skalowalność |Chociaż można określić liczbę węzłów w klastrze podczas tworzenia, przydatna może okazać się możliwość zwiększania i zmniejszania klastra w celu dopasowania go do obciążeń. Wszystkie klastry usługi HDInsight umożliwiają zmianę liczby węzłów w klastrze. Ponadto klastry Spark można porzucić bez utraty danych, ponieważ wszystkie dane są przechowywane w usłudze Azure Storage lub Data Lake Store. |
| Całodobowa pomoc techniczna |Oferta klastrów Spark w usłudze HDInsight obejmuje całodobową pomoc techniczną dla przedsiębiorstw oraz umowę SLA gwarantującą 99,9% czasu działania. |

## <a name="spark-cluster-architecture"></a>Architektura klastra Spark

Oto Spark architektury klastra i jak to działa:

![Architektura klastra Spark](./media/apache-spark-overview/spark-architecture.png)

Węzeł główny zawiera usługę nadrzędną platformy Spark, która zarządza liczbą aplikacji. Aplikacje te są mapowane na sterownik Spark. Zarządzanie poszczególnymi aplikacjami przez usługę nadrzędną platformy Spark odbywa się na różne sposoby. Architekturę Spark można wdrożyć na bazie rozwiązania Mesos lub Yarn albo menedżera klastra Spark, który umożliwia przydzielenie zasobów węzła procesu roboczego do aplikacji. W usłudze HDInsight platforma Spark korzysta z menedżera klastra YARN. Zasoby klastra są zarządzane przez usługę nadrzędną platformy Spark w usłudze HDInsight. Dzięki temu usługa nadrzędna platformy Spark ma informacje dotyczące zajętych i dostępnych zasobów (na przykład pamięci) w węźle procesu roboczego.

Sterownik obsługuje funkcję main użytkownika i wykonuje różne operacje równoległe w węzłach procesu roboczego. Następnie pobiera wyniki operacji. Węzły procesu roboczego odczytują i zapisują dane, korzystając z rozproszonego systemu plików Hadoop (HDFS). Węzły procesu roboczego przesyłają również przekształcone dane do pamięci podręcznej w postaci odpornych rozproszonych zestawów danych (Resilient Distributed Datasets, RDDs).

Po utworzeniu aplikacji w usłudze nadrzędnej platformy Spark przydzielane są zasoby do aplikacji i powstaje środowisko wykonawcze o nazwie „sterownik Spark”. Sterownik Spark tworzy także kontekst aparatu Spark i zaczyna tworzyć odporne rozproszone zestawy danych. Metadane tych zestawów danych są przechowywane na sterowniku Spark.

Sterownik Spark łączy się z usługą nadrzędną platformy Spark i konwertuje aplikację na graf skierowany (DAG) zawierający poszczególne zadania, które są uruchamiane w module wykonywania w węzłach procesu roboczego. Poszczególne aplikacje uzyskują własne procesy wykonawcze, które istnieją przez cały czas działania aplikacji i pozwalają uruchamiać zadania w wielu wątkach.

## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>Jakie są przypadki użycia platformy Spark w usłudze HDInsight?
Klastry Spark w usłudze HDInsight umożliwiają realizację następujących głównych scenariuszy:

### <a name="interactive-data-analysis-and-bi"></a>Interakcyjna analiza danych i analiza biznesowa
[Zobacz samouczek](apache-spark-use-bi-tools.md)

Klaster Apache Spark w usłudze HDInsight przechowuje dane w usłudze Azure Storage lub Azure Data Lake Store. Eksperci biznesowi i osoby podejmujące kluczowe decyzje mogą analizować i tworzyć raporty na podstawie danych oraz korzystać z usługi Microsoft Power BI w celu sporządzania interakcyjnych raportów na podstawie analizowanych danych. Analitycy mogą rozpocząć pracę od danych o częściowej strukturze lub bez struktury w magazynie klastra, zdefiniować schemat danych za pomocą notesów, a następnie skompilować modele danych przy użyciu usługi Microsoft Power BI. Klastry Spark w usłudze HDInsight obsługują również wiele narzędzi do analizy biznesowej innych firm, takich jak Tableau, dzięki czemu platforma Spark jest idealnym wyborem dla analityków danych, ekspertów biznesowych i osób podejmujących kluczowe decyzje.

### <a name="spark-machine-learning"></a>Spark Machine Learning
[Zobacz samouczek: przewidywanie temperatur w budynkach z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)

[Zobacz samouczek: przewidywanie wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)

Platforma Apache Spark jest dostarczana z biblioteką [MLlib](http://spark.apache.org/mllib/) do uczenia maszynowego opartą na platformie Spark, której można używać z klastra Spark w usłudze HDInsight. Klaster Spark w usłudze HDInsight obejmuje również platformę Anaconda — dystrybucję oprogramowania Python z szeregiem różnych pakietów do uczenia maszynowego. W połączeniu z wbudowaną obsługą notesów Jupyter i Zeppelin platforma zapewnia najwyższej jakości środowisko do tworzenia aplikacji do uczenia maszynowego.

### <a name="spark-streaming-and-real-time-data-analysis"></a>Przesyłanie strumieniowe i analiza danych w czasie rzeczywistym na platformie Spark

Klastry Spark w usłudze HDInsight zapewniają szeroką obsługę tworzenia rozwiązań do analizy w czasie rzeczywistym. Platforma Spark jest już wyposażona w łączniki do przyjmowania danych z wielu źródeł, takich jak Kafka, Flume, Twitter, ZeroMQ lub gniazda TCP, a ponadto platforma Spark w usłudze HDInsight oferuje wysokiej klasy obsługę pobierania danych z usługi Azure Event Hubs. Event Hubs to najczęściej używana usługa kolejkowania na platformie Azure. Wbudowana obsługa centrum zdarzeń sprawia, że klastry Spark w usłudze HDInsight stanowią idealną platformę do tworzenia potoku analizy w czasie rzeczywistym.

## <a name="next-steps"></a>Jakie składniki wchodzą w skład klastra Spark?
Klastry Spark w usłudze HDInsight obejmują następujące składniki, które są domyślnie dostępne w klastrach.

* [Spark Core](https://spark.apache.org/docs/1.5.1/). Obejmuje takie składniki, jak Spark Core, Spark SQL, interfejsy API przesyłania strumieniowego Spark, GraphX oraz MLlib.
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Notes Jupyter](https://jupyter.org)
* [Notes Zeppelin](http://zeppelin-project.org/)

Klastry Spark w usłudze HDInsight obejmują też [sterownik ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) zapewniający łączność z klastrami Spark w usłudze HDInsight z poziomu narzędzi do analizy biznesowej, takich jak Microsoft Power BI i Tableau.

## <a name="where-do-i-start"></a>Od czego zacząć?
Rozpocznij od utworzenia klastra Spark w usłudze HDInsight. Zobacz temat [Wprowadzenie: tworzenie klastra Apache Spark w usłudze HDInsight i uruchamianie interakcyjnych zapytań Spark SQL](apache-spark-jupyter-spark-sql.md). 

## <a name="next-steps"></a>Następne kroki
### <a name="scenarios"></a>Scenariusze
* [Platforma Spark i analiza biznesowa: interakcyjna analiza danych na platformie Spark w usłudze HDInsight z użyciem narzędzi do analizy biznesowej](apache-spark-use-bi-tools.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web na platformie Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Spark przy użyciu programu Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Zdalne debugowanie aplikacji Spark przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Zeppelin w klastrze Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Spark w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
* [Znane problemy dotyczące platformy Apache Spark w usłudze Azure HDInsight](apache-spark-known-issues.md).
