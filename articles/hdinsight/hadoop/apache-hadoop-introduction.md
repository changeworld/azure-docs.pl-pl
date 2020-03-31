---
title: Co to są Apache Hadoop i MapReduce — Azure HDInsight
description: Wprowadzenie do HDInsight i stosu technologii Apache Hadoop i komponentów.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 7e8dd69b7c58e090c30ea1aa59feddab610dd3c5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78244885"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Co to jest Apache Hadoop w usłudze Azure HDInsight?

[Apache Hadoop](https://hadoop.apache.org/) to oryginalna struktura typu „open source” do przetwarzania rozproszonego i analizy zestawów danych big data w klastrach. Ekosystem Hadoop obejmuje powiązane oprogramowanie i narzędzia, w tym Apache Hive, Apache HBase, Spark, Kafka i wiele innych.

Usługa Azure HDInsight to w pełni zarządzana usługa analityczna typu open source o pełnym spektrum w chmurze dla przedsiębiorstw. Typ klastra Apache Hadoop w usłudze Azure HDInsight umożliwia równoległe przetwarzanie i analizowanie danych wsadowych za pomocą usługi HDFS, zarządzania zasobami YARN oraz prostego modelu programowania MapReduce.

Aby wyświetlić dostępne składniki stosu technologii Hadoop w usłudze HDInsight, zobacz [Components and versions available with HDInsight](../hdinsight-component-versioning.md) (Składniki i wersje dostępne w usłudze HDInsight). Aby dowiedzieć się więcej o platformie Hadoop w usłudze HDInsight, zobacz [stronę z opisem funkcji platformy Azure w usłudze HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-mapreduce"></a>Co to jest MapReduce

Apache Hadoop MapReduce to struktura oprogramowania do pisania zadań, które przetwarzają ogromne ilości danych. Dane wejściowe są dzielone na niezależne fragmenty. Każdy fragment jest przetwarzany równolegle między węzłami w klastrze. Zadanie MapReduce składa się z dwóch funkcji:

* **Mapowanie**: Zużywa dane wejściowe, analizuje je (zwykle z operacji filtrowania i sortowania) i emituje krotek (pary klucz-wartość)

* **Reduktor:** Zużywa krotki emitowane przez mapera i wykonuje operację podsumowania, która tworzy mniejszy, połączony wynik z danych mapera

Przykład zadania MapReduce jest zilustrowany na poniższym diagramie:

 ![Hdi. Program WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

Dane wyjściowe tego zadania to liczba razy, ile wystąpiło każde słowo w tekście.

* Mapera przyjmuje każdy wiersz z tekstu wejściowego jako dane wejściowe i dzieli go na słowa. Emituje parę klucz/wartość za każdym razem, gdy po słowie pojawia się wyraz, po którym następuje 1. Dane wyjściowe są sortowane przed wysłaniem go do reduktora.
* Reduktor sumuje te indywidualne liczby dla każdego wyrazu i emituje pojedynczą parę klucz/wartość, która zawiera wyraz, po którym następuje suma jego wystąpień.

MapReduce mogą być realizowane w różnych językach. Java jest najczęstszą implementacją i jest używana do celów demonstracyjnych w tym dokumencie.

## <a name="development-languages"></a>Języki programowania

Języki lub struktury, które są oparte na Java i Java Virtual Machine można uruchamiać bezpośrednio jako zadanie MapReduce. Przykładem użytym w tym dokumencie jest aplikacja Java MapReduce. Języki inne niż Java, takie jak C#, Python lub autonomiczne pliki wykonywalne, muszą używać **przesyłania strumieniowego Hadoop.**

Przesyłanie strumieniowe Hadoop komunikuje się z maperem i reduktorem przez STDIN i STDOUT. Maper i reduktor odczytywać dane linii w czasie z STDIN i zapisać dane wyjściowe do STDOUT. Każdy wiersz odczytany lub emitowany przez mapera i reduktor musi być w formacie pary klucz/wartość, rozdzielonych znakiem karty:

    [key]/t[value]

Aby uzyskać więcej informacji, zobacz [Hadoop Streaming](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html).

Przykłady użycia usługi Przesyłania strumieniowego Hadoop z hdinsight można znaleźć w następującym dokumencie:

* [Tworzenie zadań Map# W języku C#](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie klastra Apache Hadoop w usłudze HDInsight](apache-hadoop-linux-create-cluster-get-started-portal.md)
