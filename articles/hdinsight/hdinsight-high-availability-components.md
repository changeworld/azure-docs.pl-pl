---
title: Składniki wysokiej dostępności w usłudze Azure HDInsight
description: Omówienie różnych składników wysokiej dostępności używanych przez klastry HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 38fb45fd339b5e2c7cab6f66a1ed6c0df73fb29e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74069628"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Usługi wysokiej dostępności obsługiwane przez usługę Azure HDInsight

 Aby zapewnić optymalny poziom dostępności składników analitycznych, firma HDInsight została opracowana z unikalną architekturą zapewniającą wysoką dostępność krytycznych usług.In, for provide you with optimal levels of availability for your analytics components, HDInsight was developed with a unique architecture for ensuring high availability (HA) of critical services. Niektóre składniki tej architektury zostały opracowane przez firmę Microsoft w celu zapewnienia automatycznego trybu failover. Inne składniki są standardowe składniki Apache, które są wdrażane do obsługi określonych usług. W tym artykule opisano architekturę modelu usługi wysokiej jakości w usłudze HDInsight, jak usługa HDInsight obsługuje tryb failover dla usług wysokiej jakości oraz najlepsze rozwiązania w celu odzyskania danych po przerwach w działaniu innych usług.

## <a name="high-availability-infrastructure"></a>Infrastruktura o wysokiej dostępności

Usługa HDInsight zapewnia dostosowaną infrastrukturę, aby zapewnić wysoką dostępność czterech podstawowych usług dzięki funkcjom automatycznego trybu failover:

- Serwer Apache Ambari
- Serwer osi czasu aplikacji dla Apache YARN
- Serwer historii zadań dla Hadoop MapReduce
- Apache Livy

Infrastruktura ta składa się z wielu usług i składników oprogramowania, z których niektóre są zaprojektowane przez firmę Microsoft. Następujące składniki są unikatowe dla platformy HDInsight:

- Podrzędny kontroler pracy awaryjnej
- Główny kontroler trybu failover
- Usługa wysokiej dostępności urządzenia Slave
- Master usługa wysokiej dostępności

![infrastruktura o wysokiej dostępności](./media/hdinsight-high-availability-components/high-availability-architecture.png)

Istnieją również inne usługi o wysokiej dostępności, które są obsługiwane przez składniki niezawodności apache open source. Te składniki są również obecne w klastrach HDInsight:

- Nazwa systemu plików Hadoop (HDFS)Node
- Menedżer zasobów przędzy
- Wzorzec bazy HBase

W poniższych sekcjach zostaną bardziej szczegółowe informacje na temat współpracy tych usług.

## <a name="hdinsight-high-availability-services"></a>Usługi wysokiej dostępności HDInsight

Firma Microsoft zapewnia obsługę czterech usług Apache w poniższej tabeli w klastrach HDInsight. Aby odróżnić je od usług o wysokiej dostępności obsługiwanych przez składniki z Apache, są one nazywane *usługami HDInsight HA*.

| Usługa | Węzły klastra | Typy klastrów | Przeznaczenie |
|---|---|---|---|
| Serwer Apache Ambari| Aktywny headnode | Wszystkie | Monitoruje klaster i zarządza nim.|
| Serwer osi czasu aplikacji dla Apache YARN | Aktywny headnode | Wszystkie z wyjątkiem Kafki | Utrzymuje informacje debugowania o zadaniach YARN uruchomionych w klastrze.|
| Serwer historii zadań dla Hadoop MapReduce | Aktywny headnode | Wszystkie z wyjątkiem Kafki | Utrzymuje debugowanie danych dla mapReduce zadań.|
| Apache Livy | Aktywny headnode | platforma Spark | Umożliwia łatwą interakcję z klastrem Spark za pomocą interfejsu REST |

>[!Note]
> Klastry pakietu zabezpieczeń HDInsight Enterprise Security Package (ESP) zapewniają obecnie tylko wysoką dostępność serwera Ambari.

### <a name="architecture"></a>Architektura

Każdy klaster HDInsight ma dwa węzły głowy w trybach aktywnych i czuwania, odpowiednio. Usługi HDInsight HA działają tylko na headnodes. Usługi te powinny być zawsze uruchomione na aktywnym pliku headnode i zatrzymane i umieszczone w trybie konserwacji na headnode w trybie czuwania.

Aby utrzymać prawidłowe stany usług wysokiej jakości i zapewnić szybką przerwę w pracy awaryjnej, HDInsight wykorzystuje Apache ZooKeeper, która jest usługą koordynacyjną dla aplikacji rozproszonych, do przeprowadzania aktywnych wyborów headnode. HDInsight udostępnia również kilka procesów Java w tle, które koordynują procedurę pracy awaryjnej dla usług HDInsight HA. Usługi te są następujące: główny kontroler pracy awaryjnej, kontroler trybu failover slave, *master-ha-service*i *slave-ha-service*.

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper to wysokowydajna usługa koordynacji dla aplikacji rozproszonych. W produkcji ZooKeeper zwykle działa w trybie replikacji, w którym zreplikowana grupa serwerów ZooKeeper tworzy kworum. Każdy klaster HDInsight ma trzy węzły ZooKeeper, które umożliwiają trzem serwerom ZooKeeper utworzenie kworum. HDInsight ma dwa kwora ZooKeeper działające równolegle ze sobą. Jedno kworum decyduje o aktywnym należeć do głowy w klastrze, na którym powinny być uruchamiane usługi HDInsight. Inne kworum jest używane do koordynowania usług wysokiej haw świadczonych przez Apache, jak opisano w kolejnych sekcjach.

### <a name="slave-failover-controller"></a>Podrzędny kontroler pracy awaryjnej

Kontroler trybu failover urządzenia podrzędnego działa w każdym węźle w klastrze HDInsight. Ten kontroler jest odpowiedzialny za uruchomienie agenta Ambari i *slave-ha-service* w każdym węźle. Okresowo wysyła zapytanie do pierwszego kworum ZooKeeper o aktywnym pliku headnode. Gdy aktywne i czuwania headnodes zmiany, slave kontroler pracy awaryjnej wykonuje następujące czynności:

1. Aktualizuje plik konfiguracji hosta.
1. Uruchamia ponownie agenta Ambari.

Usługa *slave-ha* jest odpowiedzialna za zatrzymanie usług HDInsight HA (z wyjątkiem serwera Ambari) w trybie headnode.

### <a name="master-failover-controller"></a>Główny kontroler trybu failover

Główny kontroler trybu failover działa na obu głównych plikach głównych. Oba główne kontrolery trybu failover komunikują się z pierwszym kworum ZooKeeper, aby wyznaczyć headnode, na który są uruchomione jako aktywny headnode.

Na przykład jeśli główny kontroler trybu failover na headnode 0 wygrywa wybory, zachodą następujące zmiany:

1. Headnode 0 staje się aktywny.
1. Główny kontroler trybu failover uruchamia serwer Ambari i *master-ha-service* na headnode 0.
1. Drugi główny kontroler trybu failover zatrzymuje serwer Ambari i *master-ha-service* na headnode 1.

Master-ha-service działa tylko na aktywnym headnode, zatrzymuje usługi HDInsight HA (z wyjątkiem serwera Ambari) na headnode w trybie gotowości i uruchamia je na aktywnym headnode.

### <a name="the-failover-process"></a>Proces pracy awaryjnej

![proces pracy awaryjnej](./media/hdinsight-high-availability-components/failover-steps.png)

Monitor kondycji działa na każdym headnode wraz z głównym kontrolerem pracy awaryjnej, aby wysłać powiadomienia hearbeat do kworum Zookeeper. W tym scenariuszu główny plik jest uważany za usługę wysokiej analizy. Monitor kondycji sprawdza, czy każda usługa wysokiej dostępności jest zdrowa i czy jest gotowa do udziału w wyborach przywódczych. Jeśli tak, to headnode będzie konkurować w wyborach. Jeśli nie, to zrezygnuje z wyborów, dopóki nie stanie się gotowy ponownie.

Jeśli headnode czuwania kiedykolwiek osiągnie przywództwo i staje się aktywny (na przykład w przypadku awarii z poprzedniego węzła aktywnego), jego główny kontroler trybu failover uruchomi wszystkie usługi HDInsight HA na nim. Główny kontroler trybu failover spowoduje również zatrzymanie tych usług na drugim głównym.

W przypadku awarii usługi HDInsight HA, takich jak awaria usługi lub nieprawidłowa kondycja, główny kontroler trybu failover powinien automatycznie ponownie uruchomić lub zatrzymać usługi zgodnie ze stanem headnode. Użytkownicy nie powinni ręcznie uruchamiać usług HDInsight WYSOKIEJ jakości w obu węzłach głównym. Zamiast tego należy zezwolić na automatyczne lub ręczne przewijanie w tryb failover, aby pomóc w odzyskaniu usługi.

### <a name="inadvertent-manual-intervention"></a>Nieumyślna interwencja ręczna

Usługi HDInsight HA powinny być uruchamiane tylko na aktywnym pliku headnode i zostaną automatycznie ponownie uruchomione w razie potrzeby. Ponieważ poszczególne usługi wysokiej klasy nie mają własnego monitora kondycji, nie można wyzwolić pracy awaryjnej na poziomie poszczególnych usług. Przewijenie awaryjne jest zapewnione na poziomie węzła, a nie na poziomie usługi.

### <a name="some-known-issues"></a>Niektóre znane problemy

- Podczas ręcznego uruchamiania usługi wysokiej dostawcy usług w trybie czuwania nie zostanie ona zatrzymana, dopóki nie nastąpi następna praca awaryjna. Gdy usługi wysokiej dostępności są uruchomione na obu headnodes, niektóre potencjalne problemy obejmują: Ambari UI jest niedostępny, Ambari rzuca błędy, YARN, Spark i Oozie zadania mogą utknąć.

- Po zatrzymaniu usługi wysokiej klasy na aktywnym nalocie nie zostanie ona ponownie uruchomiona, dopóki nie nastąpi następna praca awaryjna lub główny kontroler trybu failover/master-ha-service zostanie ponownie uruchomiony. Gdy jedna lub więcej usług wysokiej dostępności zatrzymuje się na aktywnym pliku headnode, zwłaszcza gdy serwer Ambari zatrzymuje się, interfejs użytkownika Ambari jest niedostępny, inne potencjalne problemy obejmują awarie zadań YARN, Spark i Oozie.

## <a name="apache-high-availability-services"></a>Apache usługi o wysokiej dostępności

Apache zapewnia wysoką dostępność dla plików HDFS NameNode, YARN ResourceManager i HBase Master, które są również dostępne w klastrach HDInsight. W przeciwieństwie do usług HDInsight HA są one obsługiwane w klastrach ESP. Usługi Apache HA komunikują się z drugim kworum ZooKeeper (opisane w powyższej sekcji), aby wybrać stany aktywne/rezerwowe i przeprowadzić automatyczne tryb failover. W poniższych sekcjach szczegółowo opisano, jak działają te usługi.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Nazwa rozproszonego systemu plików Hadoop (HDFS)Node

Klastry HDInsight oparte na apache Hadoop 2.0 lub nowszym zapewniają wysoką dostępność NameNode. Istnieją dwa NameNodes uruchomione na headnodes, które są skonfigurowane do automatycznego trybu failover. NameNodes używać *ZKFailoverController* do komunikowania się z Zookeeper do wyboru stanu aktywny/w trybie gotowości. *Kontroler ZKFailoverController* działa na obu headnodes i działa w taki sam sposób, jak główny kontroler trybu failover powyżej.

Drugie kworum Zookeeper jest niezależne od pierwszego kworum, więc aktywne NameNode nie może działać na aktywnym headnode. Gdy aktywne NameNode jest martwy lub niezdrowe, nazwa gotowościCzyzwyć wybory i staje się aktywny.

### <a name="yarn-resourcemanager"></a>Menedżer zasobów przędzy

Klastry HDInsight oparte na apache Hadoop 2.4 lub nowszym obsługują wysoką dostępność YARN ResourceManager. Istnieją dwa ResourceManagers, rm1 i rm2, uruchomione na headnode 0 i headnode 1, odpowiednio. Podobnie jak NameNode, YARN ResourceManager jest również skonfigurowany do automatycznego trybu failover. Inny ResourceManager jest automatycznie wybierany jako aktywny, gdy bieżący aktywny ResourceManager ustępuje lub nie odpowiada.

YARN ResourceManager używa wbudowanego *programu ActiveStandbyElector* jako detektora awarii i elektoratu lidera. W przeciwieństwie do nazwy HDFS, YARN ResourceManager nie potrzebuje oddzielnego demona ZKFC. Aktywny ResourceManager zapisuje swoje stany w Apache Zookeeper.

Wysoka dostępność menedżera zasobów YARN jest niezależna od NameNode i innych usług WYSOKIEJ DOSTĘPNOŚCI HDInsight. Aktywny ResourceManager nie może działać na aktywnym headnode lub headnode, gdzie aktywny NameNode jest uruchomiony. Aby uzyskać więcej informacji na temat wysokiej dostępności menedżera zasobów YARN, zobacz [Wysoka dostępność menedżera zasobów](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html).

### <a name="hbase-master"></a>Wzorzec bazy HBase

Klastry HDInsight HBase obsługują wysoką dostępność usługi HBase Master. W przeciwieństwie do innych usług wysokiej bazy danych, które są uruchamiane na headnodes, HBase Masters działają na trzech węzłach Zookeeper, gdzie jeden z nich jest aktywnym wzorcem, a pozostałe dwa są w trybie gotowości. Podobnie jak NameNode, HBase Master współrzędne z Apache Zookeeper dla wyboru lidera i wykonuje automatyczne tryb failover, gdy bieżący aktywny wzorzec ma problemy. W każdej chwili istnieje tylko jeden aktywny wzorzec HBase.

## <a name="next-steps"></a>Następne kroki

- [Dostępność i niezawodność klastrów Apache Hadoop w systemie HDInsight](hdinsight-high-availability-linux.md)
- [Architektura sieci wirtualnej usługi Azure HDInsight](hdinsight-virtual-network-architecture.md)
