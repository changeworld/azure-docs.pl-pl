---
title: Sprzężenia w obszarze hive Apache prowadzi do outOfMemory błąd - Azure HDInsight
description: Radzenie sobie z błędami OutOfMemory "Limit narzutów GC przekroczony błąd"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ab334dfb15044fd0734a107c12003ca2c1f86906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895187"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Scenariusz: Sprzężenie w obszarze hive Apache prowadzi do błędu OutOfMemory w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas korzystania ze składników zapytania interaktywnego w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Domyślnym zachowaniem sprzężeń Apache Hive jest załadowanie całej zawartości tabeli do pamięci, dzięki czemu sprzężenie można wykonać bez konieczności wykonywania kroku Map/Reduce. Jeśli tabela Hive jest zbyt duży, aby zmieścić się w pamięci, kwerenda może zakończyć się niepowodzeniem.

## <a name="cause"></a>Przyczyna

Podczas uruchamiania sprzężeń w gałęzi o wystarczającym rozmiarze następuje następujący błąd:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Rozwiązanie

Uniemożliwić gałęzi ładowania tabel do pamięci na sprzężenia (zamiast wykonywania map/reduce krok) przez ustawienie następującej wartości konfiguracji gałęzi:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Następne kroki

Jeśli ustawienie tej wartości nie rozwiązało problemu, odwiedź jedną z następujących opcji...

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
