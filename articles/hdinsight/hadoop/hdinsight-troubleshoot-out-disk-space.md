---
title: W węźle klastra zabraknie miejsca na dysku w usłudze Azure HDInsight
description: Rozwiązywanie problemów z przestrzenią dyskową węzła klastra Apache Hadoop w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: fbfd82473b68f5032d19834ac809191d498a5a67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894126"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Scenariusz: w węźle klastra zabraknie miejsca na dysku w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Zadanie może zakończyć się niepowodzeniem z komunikatem o błędzie podobnym do:`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Lub może pojawić się alert Apache `local-dirs usable space is below configured utilization percentage`Ambari podobne do: .

## <a name="cause"></a>Przyczyna

Pamięć podręczna aplikacji Apache Yarn mogła zużyła całe dostępne miejsce na dysku. Aplikacja Spark jest prawdopodobnie uruchomiony nieefektywnie.

## <a name="resolution"></a>Rozwiązanie

1. Użyj interfejsu użytkownika Ambari, aby określić, w którym węźle zabraknie miejsca na dysku.

1. Określ, który folder w węźle niepokojące przyczynia się do większości miejsca na dysku. SSH do węzła, `df` a następnie uruchom do listy użycia dysku dla wszystkich instalacji. Zwykle jest `/mnt` to dysk tymczasowy używany przez OSS. Można wprowadzić folder, a `sudo du -hs` następnie wpisać, aby wyświetlić podsumowane rozmiary plików w folderze. Jeśli widzisz folder podobny `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`do , oznacza to, że aplikacja jest nadal uruchomiona. Może to być spowodowane trwałością RDD lub pośrednimi plikami losowymi.

1. Aby złagodzić problem, zabić aplikację, która zwolni miejsce na dysku używane przez tę aplikację.

1. Aby ostatecznie rozwiązać ten problem, zoptymalizuj aplikację.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
