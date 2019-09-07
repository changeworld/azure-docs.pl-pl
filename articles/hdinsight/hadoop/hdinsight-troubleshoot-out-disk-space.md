---
title: Za mało miejsca na dysku w węźle klastra w usłudze Azure HDInsight
description: Rozwiązywanie problemów dotyczących miejsca na dysku węzła klastra Apache Hadoop w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/05/2019
ms.openlocfilehash: 139fb0e77f8f6960e7b13899f9586dd78bf46a92
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735847"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Scenariusz: Za mało miejsca na dysku w węźle klastra w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Zadanie może zakończyć się niepowodzeniem z komunikatem o błędzie podobnym do:`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Możesz też otrzymywać alerty Apache Ambari podobne do: `local-dirs usable space is below configured utilization percentage`.

## <a name="cause"></a>Przyczyna

Pamięć podręczna aplikacji Apache przędza mogła korzystać z całego dostępnego miejsca na dysku. Aplikacja Spark prawdopodobnie działa wydajnie.

## <a name="resolution"></a>Rozwiązanie

1. Użyj interfejsu użytkownika Ambari, aby określić, który węzeł kończy miejsce na dysku.

1. Ustal, który folder w węźle niepokojące przyczynia się do większości miejsca na dysku. Najpierw Użyj protokołu SSH do węzła, a `df` następnie uruchom polecenie, aby wyświetlić listę użycia dysku dla wszystkich instalacji. `/mnt` Zwykle jest to dysk tymczasowy używany przez OSS. Możesz wprowadzić do folderu, a następnie wpisz `sudo du -hs` , aby wyświetlić podsumowywane rozmiary plików w folderze. Jeśli zobaczysz folder podobny do `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`, oznacza to, że aplikacja nadal działa. Przyczyną może być RDD trwałość lub pośrednie losowe pliki.

1. Aby wyeliminować problem, Kasuj aplikację, która zwolni miejsce na dysku używane przez tę aplikację.

1. Aby ostatecznie rozwiązać ten problem, Zoptymalizuj aplikację.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
