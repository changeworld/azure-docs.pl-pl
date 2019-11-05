---
title: Sprzężenia w Apache Hive prowadzi do błędu OutOfMemory — Azure HDInsight
description: Postępowanie z błędami OutOfMemory "Przekroczono limit narzutów GC"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 182ca8243b2e6050a72c22f52b9fcd0d2cef37c5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494226"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Scenariusz: sprzężenia w Apache Hive prowadzi do błędu OutOfMemory w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania interakcyjnych składników zapytań w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Domyślne zachowanie Apache Hive sprzężeń polega na załadowaniu całej zawartości tabeli do pamięci, aby można było wykonać sprzężenie bez konieczności wykonywania kroków mapy/zmniejszenia. Jeśli tabela programu Hive jest zbyt duża, aby zmieścić ją w pamięci, zapytanie może zakończyć się niepowodzeniem.

## <a name="cause"></a>Przyczyna

Po uruchomieniu sprzężeń w gałęzi o wystarczającym rozmiarze wystąpi następujący błąd:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Rozdzielczość

Nie Zezwalaj na załadowanie tabel do pamięci przy użyciu sprzężeń (zamiast przeprowadzenia mapy/kroku zmniejszenia) przez ustawienie następujących wartości konfiguracji programu Hive:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Następne kroki

Jeśli ustawienie tej wartości nie rozwiązało problemu, odwiedź jedną z następujących czynności...

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalne Microsoft Azure konto, aby usprawnić obsługę klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [Jak utworzyć żądanie obsługi na platformie Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy technicznej dotyczącej zarządzania subskrypcjami i rozliczeniami jest oferowany w ramach subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona w ramach jednego z [planów pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/).
