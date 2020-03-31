---
title: Dzienniki gałęzi Apache zapełniające miejsce na dysku — Usługa Azure HDInsight
description: Dzienniki gałęzi Apache zapełniają miejsce na dysku w węzłach head w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943970"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scenariusz: Dzienniki gałęzi Apache zapełniają miejsce na dysku węzłów head w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwych rozwiązań problemów związanych z za mało miejsca na dysku w węzłach head w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

W klastrze Apache Hive/LLAP niechciane dzienniki zajmują całe miejsce na dysku w węzłach głównego. Dzięki czemu można było zaobserwować następujące problemy.

1. Dostęp SSH kończy się niepowodzeniem z powodu braku miejsca w węźle głównym.
2. Ambari daje *BŁĄD HTTP: 503 Usługa niedostępna*.

Dzienniki `ambari-agent` pokaże następujące, gdy występuje problem.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Przyczyna

W zaawansowanych konfiguracjach Hive-log4j pominięto parametr *log4j.appender.RFA.MaxBackupIndex.* Powoduje niekończące się generowanie plików dziennika.

## <a name="resolution"></a>Rozwiązanie

1. Przejdź do podsumowania komponentu Hive w `Configs` portalu Ambari i kliknij kartę.

2. Przejdź do `Advanced hive-log4j` sekcji w obszarze Ustawienia zaawansowane.

3. Ustaw `log4j.appender.RFA` parametr jako RollingFileAppender. 

4. Zestaw `log4j.appender.RFA.MaxFileSize` `log4j.appender.RFA.MaxBackupIndex` i w następujący sposób.

```
log4jhive.log.maxfilesize=1024MB
log4jhive.log.maxbackupindex=10

log4j.appender.RFA=org.apache.log4j.RollingFileAppender
log4j.appender.RFA.File=${hive.log.dir}/${hive.log.file}
log4j.appender.RFA.MaxFileSize=${log4jhive.log.maxfilesize}
log4j.appender.RFA.MaxBackupIndex=${log4jhive.log.maxbackupindex}
log4j.appender.RFA.layout=org.apache.log4j.PatternLayout
log4j.appender.RFA.layout.ConversionPattern=%d{ISO8601} %-5p [%t] %c{2}: %m%n
```
5. Ustaw `hive.root.logger` `INFO,RFA` w następujący sposób. Domyślnym ustawieniem jest DEBUG, co sprawia, że dzienniki stają się bardzo duże.

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. Zapisz configs i uruchom ponownie wymagane składniki.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
