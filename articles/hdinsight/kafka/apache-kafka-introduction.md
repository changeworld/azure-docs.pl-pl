---
title: Wprowadzenie do platformy Apache Kafka w usłudze HDInsight Azure | Microsoft Docs
description: 'Uzyskaj informacje o platformie Apache Kafka w usłudze HDInsight: artykuł zawiera opis platformy, jej działania oraz informacje o tym, gdzie można znaleźć przykłady i informacje ułatwiające rozpoczęcie pracy.'
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 4a4f2c6734de211cd20ee4b9f6815bdefefb25bc
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="introducing-apache-kafka-on-hdinsight"></a>Wprowadzenie do platformy Apache Kafka w usłudze HDInsight

[Apache Kafka](https://kafka.apache.org) to rozproszona platforma przesyłania strumieniowego typu „open source”, która umożliwia tworzenie aplikacji i potoków danych przesyłania strumieniowego w czasie rzeczywistym. Ponadto platforma Kafka oferuje funkcję brokera komunikatów, która działa podobnie do kolejki komunikatów. Pozwala ona publikować i subskrybować w nazwanych strumieniach danych. Platforma Kafka w usłudze HDInsight to zarządzana wysoce skalowalna usługa o wysokiej dostępności, która działa w chmurze platformy Microsoft Azure.

## <a name="why-use-kafka-on-hdinsight"></a>Dlaczego warto używać platformy Kafka w usłudze HDInsight?

Platforma Kafka w usłudze HDInsight oferuje następujące funkcje:

* __Umowa dotycząca poziomu usług (SLA) gwarantująca czas pracy platformy Kafka na poziomie 99,9%__: aby uzyskać więcej informacji, zobacz dokument [HDInsight — umowa SLA](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* __Odporność na uszkodzenia__: Platformę Kafka zaprojektowano pod kątem jednowymiarowej perspektywy regału sprzętowego, co sprawdza się w niektórych środowiskach. Jednak w takich środowiskach jak platforma Azure regał sprzętowy jest reprezentowany w dwóch wymiarach — domenach aktualizacji i domenach błędów. Firma Microsoft udostępnia narzędzia, za pomocą których można ponownie zrównoważyć partycje i repliki platformy Kafka między domenami aktualizacji i błędów. 

    Aby uzyskać więcej informacji, zobacz [High availability with Kafka on HDInsight](apache-kafka-high-availability.md) (Wysoka dostępność na platformie Kafka w usłudze HDInsight).

* **Integracja z usługą Azure Managed Disks**: usługa Managed Disks zapewnia wyższą skalowalność i przepływność dysków używanych przez platformę Kafka w usłudze HDInsight (do 16 TB na węzeł w klastrze).

    Aby uzyskać informacje dotyczące konfigurowania usługi Managed Disks na platformie Kafka w usłudze HDInsight, zobacz artykuł [Increase scalability of Kafka on HDInsight](apache-kafka-scalability.md) (Zwiększanie skalowalności platformy Kafka w usłudze HDInsight).

    Aby uzyskać więcej informacji o usłudze Managed Disks, zobacz artykuł [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* **Alerty, monitorowanie i konserwacja predykcyjna**: do monitorowania platformy Kafka w usłudze HDInsight może służyć usługa Azure Log Analytics. Usługa Log Analytics wydobywa informacje dotyczące maszyn wirtualnych, takie jak metryki dysków i kart sieciowych oraz metryki JMX z platformy Kafka.

    Aby uzyskać więcej informacji, zobacz [Analyze logs for Kafka on HDInsight](apache-kafka-log-analytics-operations-management.md) (Analizowanie dzienników na potrzeby platformy Kafka w usłudze HDInsight).

* **Replikowanie danych platformy Kafka**: platforma Kafka udostępnia narzędzie MirrorMaker, które replikuje dane między klastrami Kafka.

    Aby uzyskać informacje na temat używania narzędzia MirrorMaker, zobacz [Replicate Kafka topics with Kafka on HDInsight](apache-kafka-mirroring.md) (Replikowanie tematów platformy Kafka na platformie Kafka w usłudze HDInsight).

* **Skalowanie klastra**: usługa HDInsight umożliwia zmianę liczby węzłów procesu roboczego (które hostują brokera platformy Kafka) po utworzeniu klastra. Skaluj klaster w górę w miarę zwiększania obciążeń lub skaluj w dół w celu zmniejszenia kosztów. Skalowanie może być przeprowadzane w witrynie Azure Portal, w programie Azure PowerShell i w innych interfejsach zarządzania platformy Azure. W przypadku platformy Kafka po wykonaniu operacji skalowania należy przeprowadzić ponowne równoważenie replik partycji. Ponowne równoważenie partycji na platformie Kafka umożliwia skorzystanie z nowej liczby węzłów procesu roboczego.

    Aby uzyskać więcej informacji, zobacz [High availability with Kafka on HDInsight](apache-kafka-high-availability.md) (Wysoka dostępność na platformie Kafka w usłudze HDInsight).

* **Wzorzec przesyłania komunikatów dotyczących publikowania i subskrybowania**: platforma Kafka udostępnia interfejs API producenta do publikowania rekordów w temacie platformy Kafka. Interfejs API klienta jest używany podczas subskrybowania tematu.

    Aby uzyskać więcej informacji, zobacz [Start with Kafka on HDInsight](apache-kafka-get-started.md) (Rozpoczynanie pracy z platformą Kafka w usłudze HDInsight).

* **Przetwarzanie strumienia**: platforma Kafka jest często używana z systemem Apache Storm lub platformą Spark na potrzeby przetwarzania strumienia w czasie rzeczywistym. Na platformie Kafka 0.10.0.0 (usługa HDInsight w wersji 3.5 i 3.6) wprowadzono interfejs API przesyłania strumieniowego, który umożliwia tworzenie rozwiązań do transmisji strumieniowej bez konieczności stosowania rozwiązań Storm i Spark.

    Aby uzyskać więcej informacji, zobacz [Start with Kafka on HDInsight](apache-kafka-get-started.md) (Rozpoczynanie pracy z platformą Kafka w usłudze HDInsight).

* **Skalowanie w poziomie**: platforma Kafka dzieli strumienie na partycje w różnych węzłach klastra usługi HDInsight. Procesy klientów mogą być kojarzone z poszczególnymi partycjami, aby umożliwić równoważenie obciążenia podczas korzystania z rekordów.

    Aby uzyskać więcej informacji, zobacz [Start with Kafka on HDInsight](apache-kafka-get-started.md) (Rozpoczynanie pracy z platformą Kafka w usłudze HDInsight).

* **Dostarczanie w określonej kolejności**: rekordy na każdej partycji są przechowywane w strumieniu w tej samej kolejności, w której zostały odebrane. Skojarzenie jednego procesu klienta z jedną partycją pozwala zagwarantować, że rekordy są przetwarzane we właściwej kolejności.

    Aby uzyskać więcej informacji, zobacz [Start with Kafka on HDInsight](apache-kafka-get-started.md) (Rozpoczynanie pracy z platformą Kafka w usłudze HDInsight).

## <a name="use-cases"></a>Przypadki zastosowań

* **Komunikaty**: platforma Kafka obsługuje wzorzec przesyłania komunikatów dotyczących publikowania i subskrybowania, dlatego jest często używana jako broker komunikatów.

* **Śledzenie działań**: platforma Kafka rejestruje rekordy w określonej kolejności, dlatego może służyć do śledzenia i ponownego tworzenia działań. Mogą to być na przykład działania użytkownika w witrynie sieci Web lub aplikacji.

* **Agregacja**: przetwarzanie strumienia pozwala agregować informacje z różnych strumieni w celu łączenia i centralizowania informacji w formie danych operacyjnych.

* **Przekształcanie**: przetwarzanie strumienia umożliwia łączenie i urozmaicanie danych z wielu tematów wejściowych w formie tematów wyjściowych.

## <a name="architecture"></a>Architektura

![Konfiguracja klastra Kafka](./media/apache-kafka-introduction/kafka-cluster.png)

Ten diagram przedstawia typową konfigurację platformy Kafka korzystającą z grup konsumentów, partycjonowania i replikacji w celu zapewnienia równoległego odczytu zdarzeń przy zachowaniu odporności na uszkodzenia. Usługę Apache ZooKeeper, która zarządza stanem klastra platformy Kafka, zaprojektowano pod kątem obsługi jednoczesnych, odpornych transakcji o małych opóźnieniach. Platforma Kafka przechowuje rekordy w *tematach*. Rekordy są tworzone przez *producentów* i używane przez *odbiorców*. Producenci pobierają rekordy z *brokerów* platformy Kafka. Każdy węzeł procesu roboczego w klastrze usługi HDInsight jest brokerem platformy Kafka. Dla każdego użytkownika jest tworzona jedna partycja, co umożliwia równoległe przetwarzanie danych przesyłanych strumieniowo. Dzięki replikacji zapewniono dystrybucję partycji na węzłach, co gwarantuje ochronę przed awariami węzła (brokera). Partycja wiodąca jest oznaczona symbolem *(L)*. Ruch producenta jest kierowany do partycji wiodącej w każdym węźle przy użyciu stanu zarządzanego przez usługę ZooKeeper.

Każdy broker platformy Kafka korzysta z usługi Azure Managed Disks. Liczba dysków jest definiowana przez użytkownika. Mogą one zapewnić maksymalnie 16 TB magazynu na brokera.

> [!IMPORTANT]
> Platforma Kafka nie uwzględnia sprzętu podstawowego (montowanego w stelażu) w centrum danych platformy Azure. Aby zapewnić odpowiednie równoważenie partycji w sprzęcie podstawowym, zobacz dokument [Configure high availability of data (Kafka)](apache-kafka-high-availability.md) (Konfigurowanie wysokiej dostępności danych [platforma Kafka]).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak korzystać z platformy Apache Kafka w usłudze HDInsight, użyj następujących linków:

* [Wprowadzenie do platformy Kafka w usłudze HDInsight](apache-kafka-get-started.md)

* [Tworzenie repliki platformy Kafka w usłudze HDInsight przy użyciu narzędzia MirrorMaker](apache-kafka-mirroring.md)

* [Używanie systemu Apache Storm z platformą Kafka w usłudze HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Używanie platformy Apache Spark z platformą Kafka w usłudze HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Nawiązywanie połączenia z platformą Kafka za pośrednictwem sieci wirtualnej platformy Azure](apache-kafka-connect-vpn-gateway.md)