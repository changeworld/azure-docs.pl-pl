---
title: Architektura sieci wirtualnej usługi Azure HDInsight
description: Dowiedz się więcej z dostępnych zasobów, podczas tworzenia klastra usługi HDInsight w usłudze Azure Virtual Network.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 41420497bffd0abdc598e4c86b2dbda1466b2ce1
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252849"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Architektura sieci wirtualnej usługi Azure HDInsight

W tym artykule opisano zasoby, które znajdują się w przypadku wdrażania klastra usługi HDInsight w niestandardowych sieci wirtualnej platformy Azure. Ta informacja pomoże łączenie z zasobami lokalnymi, do klastra usługi HDInsight na platformie Azure. Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [co to jest Azure Virtual Network?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Typy zasobów w klastrach usługi Azure HDInsight

Usługa Azure HDInsight clusters mają różne typy maszyn wirtualnych, albo przez węzły. Każdy typ węzła odgrywa rolę w działania systemu. W poniższej tabeli podsumowano te typy węzłów i ich ról w klastrze.

| Type | Opis |
| --- | --- |
| Węzeł główny |  W przypadku wszystkich typów klastrów z wyjątkiem systemu Apache Storm węzły główne hostują procesów, które Zarządzanie wykonywaniem aplikacji rozproszonej. Węzeł główny jest również węzeł, który można nawiąż połączenie i wykonywania aplikacji, które następnie są koordynowany uruchamianej w zasobów klastra. Liczba węzłów głównych jest ustalony na dwa wszystkie typy klastrów. |
| Węzeł usługi zooKeeper | Dozorcy służy do koordynowania zadań między węzły, które wykonują przetwarzania danych. Ponadto jest wybór lidera węzła głównego i śledzi informacje o który węzła głównego jest uruchomioną określoną usługą wzorca. Liczba węzłów dozorcy jest ustalony na trzy. |
| Węzeł procesu roboczego | Reprezentuje węzły, które obsługują funkcję przetwarzania danych. Węzłów procesu roboczego może być dodane lub usunięte z klastra na skalowanie możliwości obliczeniowych i zarządzania kosztami. |
| Węzłem krawędzi oprogramowania R Server | Węzłem krawędzi oprogramowania R Server reprezentuje węzeł, w którym można nawiąż połączenie i wykonywania aplikacji, które następnie są koordynowany uruchamianej w zasobów klastra. Węzeł krawędzi nie uczestniczy w analizy danych w klastrze. Ten węzeł obsługuje również R Studio Server, dzięki któremu można uruchomić aplikację języka R za pomocą przeglądarki. |
| Węzeł regionu | Typ klastra HBase uruchamiane serwera regionalnego węzła region (nazywane również węzeł danych). Serwery regionów obsługi i zarządzanie nimi część danych zarządzanych przez bazę danych HBase. Węzły regionu można dodane lub usunięte z klastra na skalowanie możliwości obliczeniowych i zarządzania kosztami.|
| Węzeł nimbus | Dla typu klastra Storm węzeł Nimbus oferuje funkcje podobne do węzła głównego. Węzeł Nimbus przypisuje zadania do innych węzłów w klastrze za pośrednictwem dozorcy, która koordynuje uruchomionych topologii systemu Storm. |
| Nadzorca węzła | Dla typu klastra Storm węzeł nadzorca wykonuje instrukcji udostępnionych przez węzeł Nimbus do wykonywania żądanego przetwarzania. |

## <a name="basic-virtual-network-resources"></a>Zasoby podstawowe sieci wirtualnej

Na poniższym diagramie przedstawiono umieszczania HDInsight węzłów i zasobów sieciowych na platformie Azure.

![Diagram jednostek HDInsight utworzone w sieci Wirtualnej platformy Azure niestandardowe](./media/hdinsight-virtual-network-architecture/vnet-diagram.png)

Domyślne zasoby wtedy, gdy HDInsight jest wdrażana w sieci wirtualnej platformy Azure obejmują typy węzłów klastra, które są wymienione w powyższej tabeli, a także urządzeń sieciowych, które obsługują komunikację między siecią wirtualną, jak i spoza sieci.

Poniższa tabela zawiera podsumowanie węzłów klastra dziewięć, tworzonych podczas HDInsight zostaje wdrożony do niestandardowa sieć wirtualna platformy Azure.

| Typ zasobu | Liczba | Szczegóły |
| --- | --- | --- |
|Węzeł główny | dwa |    |
|Węzeł usługi ZooKeeper | trzy | |
|Węzeł procesu roboczego | dwa | Ta liczba może się różnić w zależności od konfiguracji klastra i skalowanie. Dla platformy Apache Kafka, wymagany jest co najmniej trzy węzły procesu roboczego.  |
|Węzeł bramy | dwa | Węzły bramy są maszyn wirtualnych platformy Azure, które są tworzone na platformie Azure, ale nie są widoczne w ramach subskrypcji. Jeśli musisz ponownie uruchomić te węzły, skontaktuj się z działem pomocy technicznej. |

Następujące zasoby sieci obecne są tworzone automatycznie w sieci wirtualnej używane z HDInsight:

| Zasób sieci | Liczba | Szczegóły |
| --- | --- | --- |
|Moduł równoważenia obciążenia | trzy | |
|Interfejsy sieciowe | dziewięć | Ta wartość jest oparty na klastrze normalne, gdzie każdy węzeł ma własny interfejs sieciowy. Dziewięć interfejsy są dwa węzły główne, trzy węzły dozorcy, dwa węzły procesu roboczego i dwa węzły bramy wymienionych w powyższej tabeli. |
|Publiczne adresy IP | dwa |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Punkty końcowe dotyczące łączenia usługi HDInsight

Aby uzyskać dostęp klastra HDInsight na trzy sposoby:

- Punkt końcowy HTTPS poza siecią wirtualną w `CLUSTERNAME.azurehdinsight.net`.
- Punkt końcowy SSH w celu bezpośredniego połączenia z węzłem głównym w `CLUSTERNAME-ssh.azurehdinsight.net`.
- Punkt końcowy HTTPS w sieci wirtualnej `CLUSTERNAME-int.azurehdinsight.net`. Zwróć uwagę "-int" w tym adresie URL. Ten punkt końcowy zostanie rozpoznana prywatny adres IP w tej sieci wirtualnej i nie jest dostępne z publicznej sieci internet.

Każda z tych trzech punktów końcowych są przypisywane modułu równoważenia obciążenia.

Publiczne adresy IP podano też dwa punkty końcowe, które umożliwiają połączenie z spoza sieci wirtualnej.

1. Jeden publiczny adres IP jest przypisany do modułu równoważenia obciążenia dla w pełni kwalifikowana nazwa domeny (FQDN) do użycia podczas łączenia się z klastrem z Internetu `CLUSTERNAME.azurehdinsight.net`.
1. Drugi publiczny adres IP jest używany dla nazwy domeny tylko SSH `CLUSTERNAME-ssh.azurehdinsight.net`.

## <a name="next-steps"></a>Kolejne kroki

* [Zabezpieczanie ruchu przychodzącego do klastrów HDInsight w sieci wirtualnej przy użyciu prywatnych punktów końcowych](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
