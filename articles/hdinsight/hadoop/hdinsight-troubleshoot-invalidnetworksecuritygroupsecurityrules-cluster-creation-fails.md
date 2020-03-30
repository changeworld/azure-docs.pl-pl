---
title: Błąd InvalidNetworkSecurityGroupSecurityRules — usługa Azure HDInsight
description: Tworzenie klastra kończy się niepowodzeniem z regułami errorcode InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: a73e1e9f7a9c017dd29b627a24c25ae2e064c0a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894145"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Scenariusz: InvalidNetworkSecurityGroupSecurityRules — tworzenie klastra kończy się niepowodzeniem w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Zostanie wyświetlony `InvalidNetworkSecurityGroupSecurityRules` kod błędu o opisie podobnym do "Reguły zabezpieczeń w sieciowej grupie zabezpieczeń skonfigurowane w podsieci nie zezwalają na wymaganą łączność przychodzącą i/lub wychodzącą".

## <a name="cause"></a>Przyczyna

Prawdopodobnie problem z regułami [grupy zabezpieczeń sieci przychodzącej](../../virtual-network/virtual-network-vnet-plan-design-arm.md) skonfigurowanym dla klastra.

## <a name="resolution"></a>Rozwiązanie

Przejdź do witryny Azure portal i zidentyfikować sieciowej grupy sieciowej, która jest skojarzona z podsiecią, w której jest wdrażany klaster. W sekcji **Przychodzące reguły zabezpieczeń** upewnij się, że reguły zezwalają na dostęp przychodzący do portu 443 dla adresów IP wymienionych [w tym miejscu](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip).

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
