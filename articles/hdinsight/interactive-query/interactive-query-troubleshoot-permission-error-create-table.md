---
title: Odmowa uprawnienia błędu z tabelą gałąź apache w usłudze Azure HDInsight
description: Odmowa uprawnień błąd podczas próby utworzenia tabeli hive Apache w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8ebad9300c126193ddb96d5f07057b9a825dbfcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895141"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Scenariusz: Odmowa uprawnienia błąd podczas próby utworzenia tabeli gałęzi Apache w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas korzystania ze składników zapytania interaktywnego w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas próby utworzenia tabeli zostanie wyświetlony następujący błąd:

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

Podobny komunikat o błędzie zostanie wyświetlony po uruchomieniu następującego polecenia magazynu HDFS:

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Przyczyna

Możliwość tworzenia tabeli w obszarze Gałęzi Apache zależy od uprawnień zastosowanych do konta magazynu klastra. Jeśli uprawnienia konta magazynu klastra są nieprawidłowe, nie będzie można utworzyć tabel. Oznacza to, że może mieć poprawne zasady Ranger do tworzenia tabeli i nadal zobacz "Odmowa uprawnień" błędy.

## <a name="resolution"></a>Rozwiązanie

Jest to spowodowane brakiem wystarczających uprawnień do używanego kontenera magazynu. Użytkownik tworzący tabelę hive wymaga odczytu, zapisu i wykonywania uprawnień względem kontenera. Aby uzyskać więcej informacji, zobacz [Najważniejsze wskazówki dotyczące autoryzacji hive przy użyciu Apache Ranger w HDP 2.2](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
