---
title: Sprzężenia w Apache Hive prowadzi do błędu OutOfMemory w usłudze Azure HDInsight
description: Postępowanie z błędami OutOfMemory "Przekroczono limit narzutów GC"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 45b19bdfc12313974252c81c2597645b1ceb2018
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668848"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Scenariusz: Sprzężenia w Apache Hive prowadzi do błędu OutOfMemory w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania interakcyjnych składników zapytań w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Domyślne zachowanie Apache Hive sprzężeń polega na załadowaniu całej zawartości tabeli do pamięci, aby można było wykonać sprzężenie bez konieczności wykonywania kroków mapy/zmniejszenia. Jeśli tabela programu Hive jest zbyt duża, aby zmieścić ją w pamięci, zapytanie może zakończyć się niepowodzeniem.

## <a name="cause"></a>Przyczyna

Po uruchomieniu sprzężeń w gałęzi o wystarczającym rozmiarze wystąpi następujący błąd:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Rozwiązanie

Nie Zezwalaj na załadowanie tabel do pamięci przy użyciu sprzężeń (zamiast przeprowadzenia mapy/kroku zmniejszenia) przez ustawienie następujących wartości konfiguracji programu Hive:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli ustawienie tej wartości nie rozwiązało problemu, odwiedź jedną z następujących czynności...

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
