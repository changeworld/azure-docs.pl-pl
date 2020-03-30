---
title: Problemy z łącznością Apache Phoenix w usłudze Azure HDInsight
description: Problemy z łącznością między Apache HBase i Apache Phoenix w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: b886f51bcb2bb7308c49c76563dcb70148bbc583
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887295"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Scenariusz: Problemy z łącznością Apache Phoenix w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Nie można połączyć się z Apache HBase z Apache Phoenix. Przyczyny mogą się różnić.

## <a name="cause-incorrect-ip"></a>Przyczyna: Nieprawidłowy adres IP

Niepoprawny adres IP aktywnego węzła Zookeeper.

### <a name="resolution"></a>Rozwiązanie

Adres IP aktywnego węzła Zookeeper można zidentyfikować za pomocą interfejsu użytkownika Ambari, klikając łącza do **HBase** > **Quick Links** > **ZK (Active)** > **Zookeeper Info**. W razie potrzeby popraw adres IP.

---

## <a name="cause-systemcatalog-table-offline"></a>Przyczyna: SYSTEM. Tabela CATALOG w trybie offline

Podczas uruchamiania poleceń, takich jak `!tables`, pojawia się komunikat o błędzie podobny do:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Podczas uruchamiania poleceń, takich jak `count 'SYSTEM.CATALOG'`, pojawia się komunikat o błędzie podobny do:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Rozwiązanie

W interfejsie apache Ambari wykonaj następujące czynności, aby ponownie uruchomić usługę HMaster we wszystkich węzłach ZooKeeper:

1. W sekcji **Podsumowanie** bazy HBase przejdź do **sekcji HBase** > **Active HBase Master**.

1. W sekcji **Składniki** uruchom ponownie usługę HBase Master.

1. Powtórz te kroki dla wszystkich pozostałych usług **Standby HBase Master.**

Może upłynąć do pięciu minut, aby usługa HBase Master ustabilizowała się i zakończyła odzyskiwanie. Po `SYSTEM.CATALOG` powrocie tabeli do normy problem z łącznością z Apache Phoenix powinien zostać rozwiązany automatycznie.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
