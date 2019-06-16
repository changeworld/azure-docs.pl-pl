---
title: Platforma Apache Kafka zwiększenie skalowalności — Azure HDInsight
description: Dowiedz się, jak skonfigurować zarządzane dyski klastra Apache Kafka w usłudze Azure HDInsight w celu zwiększenia skalowalności.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: a3c4f78e9d3b46b66cb2a893c4eed39f865172dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64711723"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Konfigurowanie magazynu i skalowalności klastra Apache Kafka w usłudze HDInsight

Dowiedz się, jak skonfigurować liczbę zarządzanych dysków używanych przez [platformy Apache Kafka](https://kafka.apache.org/) na HDInsight.

Platforma Kafka w usłudze HDInsight używa dysku lokalnego maszyn wirtualnych w klastrze usługi HDInsight. Ze względu na duże obciążenie we/wy platformy Kafka funkcja [Dyski zarządzane platformy Azure](../../virtual-machines/windows/managed-disks-overview.md) jest używana do zapewnienia wysokiej przepływności i zwiększenia miejsca do magazynowania w każdym węźle. Jeśli platforma Kafka korzysta z tradycyjnych wirtualnych dysków twardych (VHD), rozmiar każdego węzła nie przekracza 1 TB. W przypadku dysków zarządzanych można użyć wielu dysków, aby osiągnąć 16 TB pamięci dla każdego węzła w klastrze.

Poniższy diagram przedstawia porównanie platformy Kafka w usłudze HDInsight przed użyciem dysków zarządzanych i platformy Kafka w usłudze HDInsight z dyskami zarządzanymi:

![Diagram przedstawiający porównanie platformy Kafka w usłudze HDInsight z użyciem jednego wirtualnego dysku twardego na maszynę wirtualną oraz z użyciem wielu dysków zarządzanych na maszynę wirtualną](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Konfigurowanie dysków zarządzanych: Azure Portal

1. Aby zapoznać się z typowymi czynnościami tworzenia klastra przy użyciu witryny, wykonaj kroki opisane w temacie [Tworzenie klastra usługi HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md). Nie wykonuj procesu tworzenia w witrynie.

2. W sekcji __Rozmiar klastra__ określ liczbę dysków w polu __Liczba dysków na węzeł procesu roboczego__.

    > [!NOTE]  
    > Można wybrać typ dysku zarządzanego __Standardowy__ (HDD) lub __Premium__ (SSD). Dyski w warstwie Premium są używane przez maszyny wirtualne serii DS i GS. Wszystkie pozostałe typy maszyn wirtualnych korzystają z dysków standardowych.

    ![Obraz sekcji rozmiaru klastra z wyróżnionymi dyskami dla każdego węzła procesu roboczego](./media/apache-kafka-scalability/set-managed-disks-portal.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Konfigurowanie dysków zarządzanych: Szablon usługi Resource Manager

Aby kontrolować liczbę dysków używanych przez węzły procesu roboczego w klastrze Kafka, użyj następującej sekcji szablonu:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Można znaleźć kompletny szablon, który demonstruje, jak skonfigurować zarządzane dyski w [ https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json ](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat pracy z platformą Apache Kafka w HDInsight zobacz następujące dokumenty:

* [Tworzenie repliki platformy Kafka Apache na HDInsight przy użyciu narzędzia MirrorMaker](apache-kafka-mirroring.md)
* [Używanie systemu Apache Storm z platformą Apache Kafka w HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Korzystanie z platformy Apache Spark z platformą Apache Kafka w HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Łączenie do platformy Apache Kafka za pośrednictwem sieci wirtualnej platformy Azure](apache-kafka-connect-vpn-gateway.md)

* [HDInsight blog na dyskach zarządzanych przy użyciu platformy Apache Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
