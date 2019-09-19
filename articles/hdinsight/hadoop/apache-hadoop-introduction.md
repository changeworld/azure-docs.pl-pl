---
title: Co to jest stos technologii Apache Hadoop? — Azure HDInsight
description: Wprowadzenie do usługi HDInsight oraz stos i składniki technologii Apache Hadoop.
keywords: azure hadoop, hadoop azure, wprowadzenie do hadoop, wprowadzenie do usługi hadoop, stos technologii hadoop, wstęp do hadoop, wprowadzenie do platformy hadoop, co to jest klaster hadoop, co to są klastry hadoop, do czego służy usługa hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: overview
ms.date: 08/15/2019
ms.openlocfilehash: 193b8f59a805de4303bfc38d397a821cc068dcc0
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088200"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Co to jest Apache Hadoop w usłudze Azure HDInsight?

[Apache Hadoop](https://hadoop.apache.org/) to oryginalna struktura typu „open source” do przetwarzania rozproszonego i analizy zestawów danych big data w klastrach. Ekosystem usługi Hadoop obejmuje powiązane oprogramowanie i narzędzia, w tym Apache Hive, Apache HBase, Spark, Kafka i wiele innych.

Azure HDInsight to w pełni zarządzana usługa analizy typu open source w chmurze dla przedsiębiorstw. Typ klastra Apache Hadoop w usłudze Azure HDInsight umożliwia korzystanie z systemu HDFS, zarządzanie zasobami PRZĘDZy oraz prosty model programowania MapReduce do równoległego przetwarzania i analizowania danych wsadowych.

Aby wyświetlić dostępne składniki stosu technologii Hadoop w usłudze HDInsight, zobacz [składniki i wersje dostępne w usłudze HDInsight](../hdinsight-component-versioning.md). Aby dowiedzieć się więcej o platformie Hadoop w usłudze HDInsight, zobacz [stronę z opisem funkcji platformy Azure w usłudze HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a id="whatis"></a>Co to jest MapReduce

Apache Hadoop MapReduce jest platformą oprogramowania do pisania zadań, które przetwarzają ogromną ilość danych. Dane wejściowe są dzielone na niezależne fragmenty. Każdy fragment jest przetwarzany równolegle między węzłami w klastrze. Zadanie MapReduce składa się z dwóch funkcji:

* **Maper**: Wykorzystuje dane wejściowe, analizuje je (zazwyczaj przy użyciu operacji filtrowania i sortowania) i emituje krotki (pary klucz-wartość)

* **Zmniejszenie**: Wykorzystuje krotki emitowane przez mapowanie i wykonuje operację podsumowującą, która tworzy mniejszy wynik połączony z danych mapowania

Przykład podstawowego zadania MapReduce zliczania wyrazów przedstawiono na poniższym diagramie:   

 ![HDI.WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

Dane wyjściowe tego zadania to liczba, ile razy każdy wyraz pojawił się w tekście.

* Maper pobiera każdy wiersz z tekstu wejściowego jako dane wejściowe i dzieli go na słowa. Emituje parę klucz/wartość za każdym razem, gdy wyraz wypada wyraz, po którym następuje 1. Dane wyjściowe są sortowane przed wysłaniem go do programu do redukcji.
* Zmniejszenie sumuje te pojedyncze zliczenia dla każdego wyrazu i emituje pojedynczą parę klucz/wartość zawierającą wyraz, a następnie sumę jego wystąpień.

MapReduce można zaimplementować w różnych językach. Język Java jest najpopularniejszą implementacją i jest używany do celów demonstracyjnych w tym dokumencie.

## <a name="development-languages"></a>Języki programistyczne

Języki lub struktury oparte na języku Java i wirtualna maszyna Java mogą być uruchamiane bezpośrednio jako zadanie MapReduce. Przykładem używanym w tym dokumencie jest aplikacja Java MapReduce. W językach innych niż Java, takich C#jak Python, lub autonomicznych plikach wykonywalnych, należy używać **przesyłania strumieniowego Hadoop**.

Przesyłanie strumieniowe w usłudze Hadoop komunikuje się z mapowaniem i zmniejszeniem za pośrednictwem STDIN i STDOUT. Mapowanie i zmniejszenie danych są odczytywane wierszowo z STDIN i zapisywane dane wyjściowe do STDOUT. Każdy wiersz odczytywany lub emitowany przez mapowanie i zmniejszenie musi być w formacie pary klucz/wartość, rozdzielony znakiem tabulacji:

    [key]/t[value]

Aby uzyskać więcej informacji, zobacz Usługa [przesyłania strumieniowego Hadoop](https://hadoop.apache.org/docs/r1.2.1/streaming.html).

Przykłady korzystania z przesyłania strumieniowego Hadoop za pomocą usługi HDInsight można znaleźć w następującym dokumencie:

* [Opracowywanie C# zadań MapReduce](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie klastra Apache Hadoop w usłudze HDInsight](apache-hadoop-linux-create-cluster-get-started-portal.md)
