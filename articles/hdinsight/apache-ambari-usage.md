---
title: Użycie Apache Ambari w usłudze Azure HDInsight
description: Omówienie sposobu stosowania apache Ambari w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 466c170985715be52a90d579c19ca23aefefe2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77067398"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Użycie Apache Ambari w usłudze Azure HDInsight

Usługa HDInsight używa apache Ambari do wdrażania klastra i zarządzania nimi. Agenci Ambari są uruchamiane na każdym węźle (węzła roboczego, zookeeper i edgenode, jeśli istnieje). Serwer Ambari działa tylko na headnode (hn0 lub hn1). Tylko jedno wystąpienie serwera Ambari jest uruchamiane w tym czasie. Jest to kontrolowane przez kontroler trybu failover HDInsight. Gdy jeden z headnodes jest w dół do ponownego uruchomienia lub konserwacji, drugi headnode stanie się aktywny i serwer Ambari na drugim headnode zostanie uruchomiony.

Cała konfiguracja klastra powinna być wykonywana za pośrednictwem [interfejsu użytkownika Ambari](./hdinsight-hadoop-manage-ambari.md), wszelkie zmiany lokalne zostaną zastąpione po ponownym uruchomieniu węzła.

## <a name="failover-controller-services"></a>Usługi kontrolera trybu failover

Kontroler trybu failover hdinsight jest również odpowiedzialny za aktualizowanie adresu IP hosta headnode, który wskazuje bieżący aktywny węzeł główny. Wszystkie agentów Ambari są skonfigurowane do raportowania jego stanu i pulsu do headnode hosta. Kontroler trybu failover to zestaw usług uruchomionych w każdym węźle w klastrze, jeśli nie są uruchomione, funkcja awaryjna headnode może nie działać poprawnie i podczas próby uzyskania dostępu do serwera Ambari zostanie zainstalowany protokół HTTP 502.

Aby sprawdzić, który węzeł główny jest aktywny, jednym ze sposobów jest ssh `ping headnodehost` do jednego z węzłów w klastrze, a następnie uruchomić i porównać ADRES IP z dwoma headnodes.

Jeśli usługi kontrolera trybu failover nie są uruchomione, headnode pracy awaryjnej może nie upaść poprawnie, co może skończyć się nie uruchomiony serwer Ambari. Aby sprawdzić, czy usługi kontrolera trybu failover są uruchomione, wykonaj:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Dzienniki

Na aktywnym headnode możesz sprawdzić logi serwera Ambari na stronie:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

W dowolnym węźle w klastrze można sprawdzić dzienniki agenta Ambari pod adresem:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Sekwencje uruchamiania usługi

Jest to sekwencja uruchamiania usługi w czasie rozruchu:

1. Hdinsight-agent uruchamia usługi kontrolera trybu failover.
1. Usługi kontrolera trybu failover uruchamiają agenta Ambari na każdym węźle i serwer Ambari na aktywnym headnode.

## <a name="ambari-database"></a>Baza danych Ambari

USŁUGA HDInsight tworzy usługę SQL Azure Database pod maską, aby służyć jako baza danych dla serwera Ambari. Domyślną [warstwą usług jest S0](../sql-database/sql-database-elastic-pool-scale.md).

Dla każdego klastra z liczbą węzłów procesu roboczego większą niż 16 podczas tworzenia klastra, S2 jest warstwą usług bazy danych.

## <a name="takeaway-points"></a>Punkty na wynos

Nigdy nie uruchamiaj ręcznie/nie uruchamiaj usług ambari-server lub ambari-agent, chyba że próbujesz ponownie uruchomić usługę, aby obejść problem. Aby wymusić tryb failover, można ponownie uruchomić aktywny plik headnode.

Nigdy nie modyfikuj ręcznie żadnych plików konfiguracyjnych w dowolnym węźle klastra, pozwól interfejsowi użytkownika Ambari wykonać zadanie za Ciebie.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie klastrami HDInsight przy użyciu internetowego interfejsu użytkownika systemu Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Zarządzanie klastrami HDInsight przy użyciu interfejsu API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md)

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
