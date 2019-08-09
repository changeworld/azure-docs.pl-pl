---
title: Problemy z łącznością Apache Phoenix w usłudze Azure HDInsight
description: Problemy z łącznością Apache Phoenix w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/07/2019
ms.openlocfilehash: 641d622377bad7a1239efd526b93c6f0f0c08d4a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68887042"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Scenariusz: Problemy z łącznością Apache Phoenix w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Nie można nawiązać połączenia z usługą Apache HBase z Apache Phoenix. Przyczyny mogą się różnić.

## <a name="cause-incorrect-ip"></a>Przyczyna: Nieprawidłowy adres IP

Nieprawidłowy adres IP aktywnego węzła dozorcy.

### <a name="resolution"></a>Rozwiązanie

Adres IP aktywnego węzła dozorcy można zidentyfikować z poziomu interfejsu użytkownika Ambari, wykonując linki do **> Szybkie linki — > ZK***  **(Active) — > Informacje o dozorcy**. Poprawne w razie konieczności.

---

## <a name="cause-systemcatalog-table-offline"></a>Przyczyna: Systemami. Tabela wykazu w trybie offline

Podczas uruchamiania poleceń, takich `!tables`jak, pojawia się komunikat o błędzie podobny do:

```
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Podczas uruchamiania poleceń, takich `count 'SYSTEM.CATALOG'`jak, pojawia się komunikat o błędzie podobny do:

```
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Rozwiązanie

Uruchom ponownie usługę serwera hmaster na wszystkich węzłach dozorcy z poziomu interfejsu użytkownika Ambari.

1. Przejdź do **HBase — > aktywne łącze HBase Master** w sekcji Podsumowanie HBase.

1. W sekcji **składniki** Uruchom ponownie usługę HBase Master.

1. Powtórz powyższe kroki dla pozostałych usług **HBase Master w stanie wstrzymania** .

Zastabilizacja i zakończenie odzyskiwania może potrwać do 5 HBase Master minut. `SYSTEM.CATALOG` Gdy tabela zostanie przywrócona do normalnego, problem z łącznością Apache Phoenix powinien zostać rozwiązany automatycznie.

## <a name="next-steps"></a>Kolejne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) z programem — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
