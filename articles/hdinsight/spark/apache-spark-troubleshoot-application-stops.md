---
title: Aplikacja Apache Spark Streaming zatrzymuje się po 24 dniach w usłudze Azure HDInsight
description: Aplikacja Apache Spark Streaming zatrzymuje się po wykonaniu przez 24 dni i nie ma żadnych błędów w plikach dziennika.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: ff410ea1b6c54d2f58babeb20c68fe95033e9728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894434"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Scenariusz: Aplikacja Apache Spark Streaming zatrzymuje się po wykonaniu przez 24 dni w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas korzystania ze składników Platformy Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Aplikacja Apache Spark Streaming zatrzymuje się po wykonaniu przez 24 dni i nie ma żadnych błędów w plikach dziennika.

## <a name="cause"></a>Przyczyna

Wartość `livy.server.session.timeout` określa, jak długo Apache Livy powinien czekać na zakończenie sesji. Gdy długość sesji osiągnie `session.timeout` wartość, sesja Livy i aplikacja są automatycznie zabijane.

## <a name="resolution"></a>Rozwiązanie

W przypadku długotrwałych zadań `livy.server.session.timeout` należy zwiększyć wartość korzystania z interfejsu użytkownika Ambari. Dostęp do konfiguracji Livy można uzyskać za pomocą `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`interfejsu użytkownika Ambari za pomocą adresu URL .

Zamień `<yourclustername>` na nazwę klastra HDInsight, jak pokazano w portalu.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
