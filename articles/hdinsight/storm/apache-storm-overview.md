---
title: Co to jest Apache Storm — Azure HDInsight
description: System Apache Storm służy do przetwarzania strumieni danych w czasie rzeczywistym. Usługa Azure HDInsight umożliwia łatwe tworzenie klastrów Storm w chmurze Azure. Przy użyciu programu Visual Studio można tworzyć rozwiązania Storm przy użyciu języka C#, a następnie wdrażać je do klastrów usługi HDInsight Storm.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: przypadki użycia systemu apache storm,klaster storm,co to jest apache storm
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: overview
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 00c536120ed9507d5ecc6fe930429d12514945b3
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483817"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Co to jest Apache Storm w usłudze Azure HDInsight?

[Apache Storm](https://storm.apache.org/) to rozproszony, odporny na uszkodzenia system obliczeniowy typu open source. Można używać systemu Storm można przetwarzać strumienie danych w czasie rzeczywistym za pomocą [Apache Hadoop](https://hadoop.apache.org/). Rozwiązanie Storm oferuje również gwarantowane przetwarzanie danych z możliwością powtarzania danych, które nie zostały pomyślnie przetworzone po raz pierwszy.

## <a name="why-use-apache-storm-on-hdinsight"></a>Dlaczego warto używać systemu Storm Apache na HDInsight?

System Storm w usłudze HDInsight oferuje następujące funkcje:

* __99% umowy poziomu usług (SLA) gwarantująca czas pracy systemu Storm na__: Aby uzyskać więcej informacji, zobacz dokument [HDInsight — umowa SLA](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Umożliwia łatwe dostosowywanie klastrów Storm dzięki uruchamianiu w nich skryptów podczas procesu tworzenia klastra lub po jego ukończeniu. Aby uzyskać więcej informacji, zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md).

* **Tworzenie rozwiązań w wielu językach**: Składniki systemu Storm można pisać w wybranym języku, takim jak Java, C# i Python.

    * Integruje program Visual Studio z usługą HDInsight na potrzeby tworzenia i monitorowania topologii języka C# oraz zarządzania nimi. Aby uzyskać więcej informacji, zobacz [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md) (Tworzenie topologii języka C# przy użyciu narzędzi HDInsight Tools dla programu Visual Studio).

    * Obsługuje interfejs języka Java Trident. Umożliwia on tworzenie topologii Storm obsługujących dokładnie jednokrotne przetwarzanie komunikatów, transakcyjną trwałość magazynu danych i zestaw typowych operacji analizy strumienia.

* **Dynamiczne skalowanie**: Można dodawać lub usuwać węzły procesu roboczego bez wpływu na działające topologie Storm.

    * Aby skorzystać z nowych węzłów dodanych za pośrednictwem operacji skalowania, musisz dezaktywować i ponownie aktywować działające topologie.

* **Tworzenie potoków przesyłania strumieniowego przy użyciu wielu usług platformy Azure**: System STORM w HDInsight integruje się z innymi usługami platformy Azure, takich jak Event Hubs, SQL Database, usługi Azure Storage i usługi Azure Data Lake Storage.

    Z przykładowym rozwiązaniem, która integruje się z usługami platformy Azure, zobacz [przetwarzania zdarzeń pochodzących z usługi Event Hubs przy użyciu platformy Apache Storm w HDInsight](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

Lista firm, które używają systemu Apache Storm w rozwiązaniach analitycznych działających w czasie rzeczywistym, jest dostępna na stronie [Companies Using Apache Storm](https://storm.apache.org/documentation/Powered-By.html) (Firmy korzystające z systemu Apache Storm).

Aby rozpocząć korzystanie z systemu Storm, zobacz [tworzenie i monitorowanie topologii Apache Storm w usłudze Azure HDInsight](apache-storm-quickstart.md).

## <a name="how-does-apache-storm-work"></a>Jak działa systemu Apache Storm

System STORM uruchamia topologie zamiast [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) zadania, które mogą być zaznajomiony z. Topologie systemu Storm obejmują wiele składników rozmieszczonych w skierowanym grafie acyklicznym (DAG). Dane przepływają między składnikami tego grafu. Każdy składnik używa przynajmniej jednego strumienia danych i może opcjonalnie emitować przynajmniej jeden strumień. Na poniższym diagramie przedstawiono sposób przepływu danych między składnikami w podstawowej topologii zliczania wyrazów:

![Przykładowy układ składników w topologii Storm](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* Składniki typu spout wprowadzają dane do topologii. Wysyłają one co najmniej jeden strumień danych do topologii.

* Składniki typu bolt wykorzystują strumienie emitowane przez elementy spout lub inne elementy bolt. Składniki typu bolt mogą opcjonalnie emitować strumienie do topologii. Odpowiadają również za zapisywanie danych w usługach zewnętrznych lub magazynie — takim jak system plików HDFS, platforma Kafka lub usługa HBase.

## <a name="reliability"></a>Niezawodność

System Apache Storm gwarantuje, że każdy przychodzący komunikat jest zawsze w pełni przetwarzany — nawet wtedy, gdy analiza danych jest rozłożona na setki węzłów.

Węzeł Nimbus oferuje funkcje podobne do Apache Hadoop JobTracker i przypisuje zadania do innych węzłów w klastrze za pośrednictwem [Apache ZooKeeper](https://zookeeper.apache.org/). Węzły dozorcy zapewniają koordynację klastra i ułatwiają komunikację między węzłem Nimbus i procesem nadzorczym procesów roboczych. Jeśli jeden węzeł przetwarzania przestanie działać, zostanie zawiadomiony węzeł Nimbus, który przypisze zadanie i związane z nim dane do innego węzła.

W domyślnej konfiguracji klastrów Apache Storm występuje tylko jeden węzeł Nimbus. System Storm w usłudze HDInsight obejmuje dwa węzły Nimbus. W przypadku awarii węzła podstawowego klaster Storm przechodzi do węzła pomocniczego, a węzeł podstawowy jest przywracany. Na poniższym diagramie przedstawiono konfigurację przepływu zadań systemu Storm w usłudze HDInsight:

![Schemat węzłów Nimbus, dozorcy i nadzorcy](./media/apache-storm-overview/nimbus.png)

## <a name="ease-of-creation"></a>Łatwość tworzenia

Nowy klaster Storm można utworzyć w usłudze HDInsight w ciągu kilku minut. Aby uzyskać więcej informacji na temat tworzenia klastra Storm, zobacz [tworzenie technologii Apache Hadoop clusters, przy użyciu witryny Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="ease-of-use"></a>Łatwość obsługi

* __Secure Shell (SSH) łączności__: Można uzyskać dostęp do węzłów głównych klastra Storm za pośrednictwem Internetu, przy użyciu protokołu SSH. Polecenia można uruchamiać bezpośrednio w klastrze przy użyciu protokołu SSH.

  Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* __Łączność w sieci Web__: Wszystkie klastry HDInsight udostępniają interfejs webowy Ambari. Pozwala on łatwo monitorować i konfigurować usługi oraz zarządzać nimi w klastrze. Klastry Storm udostępniają też interfejs Storm. Interfejs ten pozwala na monitorowanie działających topologii systemu Storm oraz zarządzanie nimi z poziomu przeglądarki przy użyciu interfejsu użytkownika Storm.

  Aby uzyskać więcej informacji, zobacz [Zarządzanie HDInsight przy użyciu Interfejsu sieci Web Apache Ambari](../hdinsight-hadoop-manage-ambari.md) i [monitorowanie i zarządzanie przy użyciu interfejsu użytkownika platformy Storm Apache](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui) dokumentów.

* __Program Azure PowerShell i na platformie Azure klasyczny interfejs wiersza polecenia__: Program PowerShell i klasyczny interfejs wiersza polecenia, które są zarówno udostępniają narzędzia wiersza polecenia, których można w systemie klienta do pracy z HDInsight i innymi usługami platformy Azure.

* __Integracja z programem Visual Studio__: Usługa Azure Data Lake Tools for Visual Studio obejmują szablony projektów umożliwiające tworzenie C# Storm topologies przy użyciu platformy SCP.NET. Narzędzia Data Lake Tools oferują również umożliwiające wdrażanie i monitorowanie rozwiązań systemu Storm w usłudze HDInsight oraz zarządzanie nimi.

  Aby uzyskać więcej informacji, zobacz [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md) (Tworzenie topologii języka C# przy użyciu narzędzi HDInsight Tools dla programu Visual Studio).

## <a name="integration-with-other-azure-services"></a>Integracja z innymi usługami platformy Azure

* __Azure Data Lake Storage__: Na przykład korzystania z usługi Data Lake Storage w połączeniu z klastrem Storm zobacz [użycia Azure magazyn usługi Data Lake za pomocą Storm Apache na HDInsight](apache-storm-write-data-lake-store.md).

* __Usługa Event Hubs__: Przykład użycia usługi Event Hubs w klastrze Storm zobacz następujące przykłady:

    * [Przetwarzanie zdarzeń z usługi Azure Event Hubs przy użyciu platformy Apache Storm w HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/)

    * [Przetwarzania zdarzeń pochodzących z usługi Azure Event Hubs przy użyciu platformy Apache Storm w HDInsight (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __Baza danych SQL__, __usługi Cosmos DB__, __usługi Event Hubs__, i __HBase__: Przykłady szablonów są dostępne w narzędziach Data Lake Tools for Visual Studio. Aby uzyskać więcej informacji, zobacz [programowanie C# topologii STORM Apache na HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="support"></a>Pomoc techniczna

System Storm w usłudze HDInsight jest dostarczany z pełną, stale dostępną pomocą techniczną na poziomie korporacyjnym. System Storm w usłudze HDInsight gwarantuje również dostępność na poziomie 99,9 procent zgodnie z umową SLA. Firma Microsoft gwarantuje więc, że klaster Storm utrzymuje łączność zewnętrzną przez nie mniej niż 99,9% czasu.

Aby uzyskać więcej informacji, zobacz [Pomoc techniczna platformy Azure](https://azure.microsoft.com/support/options/).

## <a name="apache-storm-use-cases"></a>Przypadki użycia systemu Apache Storm

Poniżej przedstawiono kilka typowych scenariuszy, w których można skorzystać z systemu Storm w usłudze HDInsight:

* Internet rzeczy (IoT)
* Wykrywanie oszustw
* Analityka społecznościowa
* Wyodrębnianie, transformacja, ładowanie (ETL)
* Monitorowanie sieci
* Wyszukiwanie
* Marketing na urządzeniach przenośnych

Aby uzyskać informacje o praktycznych scenariuszach, zobacz [jak firmy korzystają z systemu Apache Storm](https://storm.apache.org/documentation/Powered-By.html) dokumentu.

## <a name="development"></a>Opracowywanie zawartości

Korzystając z narzędzi Data Lake Tools for Visual Studio programiści .NET mogą projektować i implementować topologie w języku C#. Można również tworzyć hybrydowe topologie, wykorzystujące składniki Java i C#.

Aby uzyskać więcej informacji na ten temat, zobacz [Develop C# topologies for Apache Storm on HDInsight using Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md) (Tworzenie topologii C# dla Apache Storm w usłudze HDInsight przy użyciu programu Visual Studio).

Możesz również tworzyć rozwiązania w języku Java przy użyciu wybranego środowiska IDE. Aby uzyskać więcej informacji, zobacz [opracowywanie topologii języka Java dla usługi Storm Apache na HDInsight](apache-storm-develop-java-topology.md).

Składniki systemu Storm można również opracowywać w języku Python. Aby uzyskać więcej informacji, zobacz [topologii usługi Storm opracowywanie Apache na HDInsight przy użyciu języka Python](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Typowe wzorce programowania

### <a name="guaranteed-message-processing"></a>Gwarantowane przetwarzanie komunikatów

System Apache Storm zapewnia różne poziomy gwarantowanego przetwarzania komunikatów. Na przykład podstawowa aplikacja Storm może zagwarantować przetwarzanie co najmniej jednokrotne, a [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) może zagwarantować przetwarzanie dokładnie jednokrotne.

Aby uzyskać więcej informacji, zobacz [Guarantees on data processing](https://storm.apache.org/about/guarantees-data-processing.html) (Gwarancje przetwarzania danych) w serwisie apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Wzorzec odczytywanie krotki wejściowej, emitowanie zero lub więcej krotek i następnie potwierdzenie krotki wejściowej natychmiast po zakończeniu metody execute jest wspólne. System Storm udostępnia interfejs [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html) w celu automatyzacji tego wzorca.

### <a name="joins"></a>Sprzężenia

Sposób łączenia strumieni danych różni się między aplikacjami. Na przykład można łączyć poszczególne krotki z wielu strumieni w jeden nowy strumień lub łączyć tylko partie krotek w określonym oknie. W obu przypadkach łączenie można przeprowadzić za pomocą metody [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Grupowanie pól polega na określeniu, jak krotki są kierowane do składników bolt.

W poniższym przykładzie w języku Java metoda fieldsGrouping służy do kierowana krotek pochodzących ze składników „1”, „2” i „3” do elementu bolt o nazwie MyJoiner:

```java
builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));
```

### <a name="batches"></a>Partie

System Apache Storm ma wewnętrzny mechanizm zegarowy nazywany „krotką znacznikową”. Można określić częstotliwość emitowania krotki znacznikowej w topologii.

Aby zapoznać się z przykładem korzystania z krotki znacznikowej z poziomu składnika języka C#, zobacz [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Pamięci podręczne

Buforowanie w pamięci jest często używane jako mechanizm przyspieszania przetwarzania, ponieważ utrzymuje często używane zasoby w pamięci. Ponieważ topologia jest rozpowszechniana na wiele węzłów i wiele procesów w każdym węźle, należy rozważyć użycie metody [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Użyj metody `fieldsGrouping`, aby zagwarantować, że krotki z polami używanymi do przeszukiwania pamięci podręcznej są zawsze kierowane do tego samego procesu. Funkcja grupowania pozwala uniknąć duplikowania wpisów pamięci podręcznej między procesami.

### <a name="stream-top-n"></a>Strumień „pierwszych N”

Jeśli topologia zależy od obliczenia wartości „pierwszych N”, oblicz wartość pierwszych N równolegle. Następnie należy scalić dane wyjściowe z tych obliczeń w obrębie wartości globalnej. Tę operację można wykonać przy użyciu metody [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-), aby przeprowadzić kierowanie według pola na potrzeby przetwarzania równoległego. Następnie można kierować do składnika bolt, który określa globalnie największą wartość N.

Aby zapoznać się z przykładem obliczania wartości pierwszych N, zobacz przykład [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Rejestrowanie

System STORM używa [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) do rejestrowania informacji. Domyślnie rejestrowana jest duża ilość danych i sortowanie informacji może być trudne. W topologii systemu Storm można uwzględnić plik konfiguracji rejestrowania, aby sterować zachowaniem rejestrowania.

Przykładową topologię pokazującą metodę konfigurowania logowania można znaleźć w przykładzie [aplikacji WordCount opartej na języku Java](apache-storm-develop-java-topology.md) dla systemu Storm w usłudze HDInsight.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat rozwiązań analitycznych w czasie rzeczywistym przy użyciu platformy Apache Storm w HDInsight:

* [Tworzenie i monitorowanie topologii Apache Storm w usłudze Azure HDInsight](apache-storm-quickstart.md)
* [Przykładowe topologie dla systemu Apache Storm w usłudze HDInsight](apache-storm-example-topology.md)
