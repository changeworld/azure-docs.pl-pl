---
title: Zarządzanie miejscem na dysku w usłudze Azure HDInsight
description: Rozwiązywanie problemów z instrukcjami i możliwymi rozwiązaniami problemów podczas interakcji z klastrami usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473014"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Zarządzanie miejscem na dysku w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="hive-log-configurations"></a>Konfiguracje dziennika gałęzi

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra.

1. Przejdź do **sekcji Hive** > **Configs** > **Advanced** > Advanced**hive-log4j**. Przejrzyj następujące ustawienia:

    * `hive.root.logger=DEBUG,RFA`. Jest to wartość domyślna, zmodyfikuj [poziom dziennika,](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) aby `INFO` wydrukować mniej wpisów dzienników.

    * `log4jhive.log.maxfilesize=1024MB`. Jest to wartość domyślna, zmodyfikować zgodnie z potrzebami.

    * `log4jhive.log.maxbackupindex=10`. Jest to wartość domyślna, zmodyfikować zgodnie z potrzebami. Jeśli parametr został pominięty, wygenerowane pliki dziennika będą nieskończone.

## <a name="yarn-log-configurations"></a>Konfiguracje dziennika przędzy

Przejrzyj następujące konfiguracje:

* Apache Ambari

    1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra.

    1. Przejdź do Menedżera**zaawansowanych** > **zasobów** **Hive** > **Configs** > . Upewnij się, że **opcja Włącz agregację dzienników** jest zaznaczona. Jeśli opcja jest wyłączona, węzły nazw będą przechowywać dzienniki lokalnie i nie agregują ich w magazynie zdalnym po zakończeniu lub zakończeniu aplikacji.

* Upewnij się, że rozmiar klastra jest odpowiedni dla obciążenia. Obciążenie mogło ulec zmianie niedawno lub może być zmieniony format klastra. [Skalowanie](../hdinsight-scaling-best-practices.md) w górę klastra, aby dopasować się do wyższego obciążenia.

* `/mnt/resource`mogą być wypełnione osieroconymi plikami (tak jak w przypadku ponownego uruchomienia Menedżera zasobów). W razie potrzeby `/mnt/resource/hadoop/yarn/log` należy `/mnt/resource/hadoop/yarn/local`ręcznie wyczyścić i .

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
