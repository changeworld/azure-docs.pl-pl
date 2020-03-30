---
title: Powiązany procesor w klastrze Apache HBase — Usługa Azure HDInsight
description: Rozwiązywanie problemów z powiązanym procesorem CPU na serwerze regionu w klastrze Apache HBase w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 16c994029e91d743f1c2a7e2eab51eb86fc378e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887312"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Scenariusz: Powiązany procesor CPU na serwerze regionu w klastrze Apache HBase w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Proces serwera regionu Apache HBase rozpoczyna zajmowanie blisko 200% procesora CPU, co powoduje, że alerty są uruchamiane w procesie i klastrze HBase Master, aby nie działać z pełną wydajnością.

## <a name="cause"></a>Przyczyna

Jeśli korzystasz z klastra HBase w wersji 3.4, być może zostałeś trafiony przez potencjalny błąd spowodowany uaktualnieniem jdk do wersji 1.7.0_151. Objawem widzimy jest proces serwera regionu rozpoczyna się zajmuje blisko 200% CPU (aby sprawdzić to uruchomić `top` polecenie; jeśli istnieje proces zajmujący blisko 200% CPU uzyskać jego pid i potwierdzić, że jest proces serwera regionu przez uruchomienie `ps -aux | grep` ).

## <a name="resolution"></a>Rozwiązanie

1. Zainstaluj jdk 1.8 na wszystkich węzłach klastra, jak poniżej:

    * Uruchom akcję `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`skryptu . Pamiętaj, aby wybrać opcję uruchamiania na wszystkich węzłach.

    * Alternatywnie można zalogować się do każdego węzła i uruchomić polecenie `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`.

1. Przejdź do interfejsu użytkownika `https://<clusterdnsname>.azurehdinsight.net`Ambari - .

1. Przejdź do **pozycji HBase->Configs->Advanced->Advanced** `hbase-env configs` i zmień `JAVA_HOME` zmienną na `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`. Zapisz zmianę konfiguracji.

1. [Opcjonalnie, ale zalecane] [Opróżnij wszystkie tabele w klastrze](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

1. Z interfejsu użytkownika Ambari ponownie uruchom ponownie wszystkie usługi HBase, które wymagają ponownego uruchomienia.

1. W zależności od danych w klastrze może upłynąć kilka minut do godziny, aby klaster osiągnął stabilny stan. Sposób potwierdzenia, że klaster osiągnie stabilny stan, polega na sprawdzeniu interfejsu użytkownika HMaster (wszystkie serwery regionu powinny być aktywne) z ambari (odświeżanie) lub z diody sterującej uruchomą powłokę HBase, a następnie uruchom polecenie stanu.

Aby sprawdzić, czy uaktualnienie zakończyło się pomyślnie, sprawdź, czy odpowiednie procesy HBase są uruchamiane przy użyciu odpowiedniej wersji java — na przykład dla serwera regionu sprawdź jako:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
