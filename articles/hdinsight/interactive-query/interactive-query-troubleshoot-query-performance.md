---
title: Niska wydajność zapytań apache hive LLAP w usłudze Azure HDInsight
description: Zapytania w apache hive LLAP są wykonywane wolniej niż oczekiwano w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8bd20849b15f8c8d5a14653f702f78c6404d82e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895121"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Scenariusz: Niska wydajność zapytań apache hive LLAP w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas korzystania ze składników zapytania interaktywnego w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Domyślne konfiguracje klastra nie są wystarczająco dostrojone dla obciążenia. Kwerendy w powiecie ulu llap są wykonywane wolniej niż oczekiwano.

## <a name="cause"></a>Przyczyna

Może się to zdarzyć z różnych powodów.

## <a name="resolution"></a>Rozwiązanie

LLAP jest zoptymalizowany pod kątem zapytań, które obejmują sprzężenia i agregacje. Zapytania, takie jak następujące, nie działają dobrze w klastrze gałęzi interaktywnej:

```
select * from table where column = "columnvalue"
```

Aby zwiększyć wydajność zapytań punktowych w u gałęzi LLAP, ustaw następujące konfiguracje:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

Można również zwiększyć użycie pamięci podręcznej LLAP, aby zwiększyć wydajność dzięki następującej zmianie konfiguracji:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
