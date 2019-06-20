---
title: Co to jest Apache HBase w usłudze Azure HDInsight?
description: Wprowadzenie do bazy danych Apache HBase w usłudze HDInsight — bazy danych NoSQL opartej na platformie Hadoop. Dowiedz się więcej o przypadkach użycia i porównaj bazę danych HBase z innymi klastrami Hadoop.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: overview
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: e48a0c69dc04325c3f3c2ff7b73a26c6366816c9
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137455"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Co to jest Apache HBase w usłudze Azure HDInsight

[Apache HBase](https://hbase.apache.org/) jest baza danych NoSQL typu open source, która jest oparta na [Apache Hadoop](https://hadoop.apache.org/) i modelowanych po [bazie danych Google BigTable](https://cloud.google.com/bigtable/). Baza danych HBase zapewnia dostęp losowy i wysoki poziom spójności w przypadku dużych ilości danych z częściową strukturą i bez struktury w bezschematowej bazie danych zorganizowanej według rodzin kolumn.

Z perspektywy użytkownika bazy danych HBase jest podobne do bazy danych. Dane są przechowywane w wierszy i kolumn w tabeli, a dane w wierszu są pogrupowane według rodziny kolumn. HBase jest bezschematową bazą danych, co oznacza, że ani kolumny, ani typy danych w nich przechowywanych nie muszą być zdefiniowane przed użyciem. Kod typu open source zapewnia skalowanie liniowe, umożliwiając obsługę petabajtów danych na tysiącach węzłów. Baza może wykorzystywać nadmiarowość danych, przetwarzanie wsadowe i inne funkcje, które są dostarczane przez aplikacje rozproszone w ekosystemie Hadoop.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Jak Apache HBase jest zaimplementowana w usłudze Azure HDInsight?

Baza danych HBase w usłudze HDInsight jest oferowana jako zarządzany klaster zintegrowany ze środowiskiem Azure. Klastry są skonfigurowane do przechowywania danych bezpośrednio w [usługi Azure Storage](./../hdinsight-hadoop-use-blob-storage.md) który zapewnia małe opóźnienia i zwiększoną elastyczność w zakresie wydajności i kosztów. Pozwala to klientom tworzyć interaktywne witryny sieci Web pracujące z dużymi zestawami danych, opracowywać usługi przechowywania danych czujników i danych telemetrycznych z milionów punktów końcowych oraz analizować te dane przy użyciu zadań platformy Hadoop. HBase i Hadoop są dobrymi punktami startowymi dla projektów obejmujących dane big data w środowisku Azure. W szczególności mogą one umożliwiać pracę w czasie rzeczywistym aplikacji obsługujących duże zestawy danych.

Implementacja usługi HDInsight wykorzystuje skalowalność architektury HBase, aby zapewnić automatyczne dzielenie tabel na fragmenty, wysoki poziom spójności operacji odczytu i zapisu oraz automatyzację pracy awaryjnej. Wydajność jest zwiększona dzięki buforowaniu w pamięci operacji odczytu i przesyłaniu strumieniowemu o wysokiej przepustowości obejmującemu operacje zapisu. Klaster bazy danych HBase można utworzyć w sieci wirtualnej. Aby uzyskać szczegółowe informacje, zobacz temat [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Tworzenie klastrów usługi HDInsight w usłudze Azure Virtual Network).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>W jaki sposób zarządzane są dane w bazie danych HBase w usłudze HDInsight?
W bazie danych HBase można zarządzać danymi za pomocą poleceń `create`, `get`, `put` i `scan` z poziomu powłoki HBase. Dane są zapisywane w bazie danych przy użyciu polecenia `put` i odczytywane przy użyciu polecenia `get`. Polecenie `scan` jest używane do uzyskiwania danych z wielu wierszy w tabeli. Danymi można również zarządzać przy użyciu interfejsu API w języku C# bazy danych HBase, który udostępnia bibliotekę klienta ponad interfejsem API REST HBase. Bazy danych HBase, również może być odpytywany za pomocą [Apache Hive](https://hive.apache.org/). Aby zapoznać się z wprowadzeniem do tych modeli programowania, zobacz [rozpoczęcie korzystania z bazy danych Apache HBase z użyciem usługi Apache Hadoop w HDInsight](./apache-hbase-tutorial-get-started-linux.md). Koprocesory są również dostępne, co umożliwia przetwarzanie danych w węzłach hostujących bazę danych.

> [!NOTE]  
> Platforma Thrift nie jest obsługiwana przez bazę danych HBase w usłudze HDInsight.

## <a name="scenarios-use-cases-for-apache-hbase"></a>Scenariusze: Zastosowań bazy danych Apache HBase
Przypadek użycia, które BigTable (a przy użyciu rozszerzenia, bazy danych HBase) został utworzony na podstawie wyszukiwania w Internecie. Aparaty wyszukiwania tworzą indeksy, które mapują terminy na zawierające je strony sieci Web. Istnieje jednak wiele innych przypadków użycia, w których baza danych HBase jest przydatna — niektóre z nich są wymienione w tej sekcji.

* Magazyn wartości klucza
  
    Baza danych HBase może być używana jako magazyn wartości klucza i nadaje się do zarządzania systemami obsługi wiadomości. Facebook używa bazy danych HBase na potrzeby systemu obsługi wiadomości, gdyż takie rozwiązanie idealnie nadaje się do przechowywania wiadomości i zarządzania komunikacją internetową. Usługa WebTable używa bazy danych HBase do wyszukiwania tabel wyodrębnianych ze stron sieci Web oraz zarządzania nimi.
* Dane czujników
  
    Baza danych HBase może służyć do przechwytywania danych gromadzonych przyrostowo z różnych źródeł. Dotyczy to analiz społecznościowych, szeregów czasowych, aktualizowania interaktywnych pulpitów nawigacyjnych przy użyciu trendów i liczników oraz zarządzania systemami dzienników inspekcji. Przykładami mogą być terminale transakcyjne Bloomberg oraz baza danych OpenTSDB (Open Time Series Database), która przechowuje i udostępnia metryki dotyczące kondycji systemów serwerowych.
* Zapytania w czasie rzeczywistym
  
    [Apache Phoenix](https://phoenix.apache.org/) jest aparatem zapytań SQL dla bazy danych Apache HBase. Jest on dostępny jako sterownik JDBC i umożliwia wykonywanie zapytań i zarządzanie tabelami HBase przy użyciu języka SQL.
* HBase jako platforma
  
    Aplikacje mogą działać w bazie danych HBase, wykorzystując ją jako magazyn danych. Przykłady obejmują Phoenix, [OpenTSDB](http://opentsdb.net/), Kiji i Titan. Aplikacje można również integrować z bazą danych HBase. Przykłady obejmują [Apache Hive](https://hive.apache.org/), [Apache Pig](https://pig.apache.org/), [Solr](https://lucene.apache.org/solr/), [Apache Storm](https://storm.apache.org/), [Apache Flume](https://flume.apache.org/), [ Apache Impala](https://impala.apache.org/), [platformy Apache Spark](https://spark.apache.org/) , [Ganglia](http://ganglia.info/), i [Apache Drill](https://drill.apache.org/).

## <a name="next-steps"></a>Kolejne kroki

* [Rozpoczynanie pracy z usługą Apache Hadoop w HDInsight przy użyciu bazy danych Apache HBase](./apache-hbase-tutorial-get-started-linux.md)
* [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Tworzenie klastrów usługi HDInsight w usłudze Azure Virtual Network)
* [Konfigurowanie replikacji bazy danych Apache HBase w HDInsight](apache-hbase-replication.md)
