---
title: Apache Spark przesyłania strumieniowego aplikacji przestanie przetwarzać dane po 24 dniach bez znanego błędu w dziennikach w usłudze Azure HDInsight
description: Aplikacja przesyłania strumieniowego Apache Spark przestanie działać po upływie 24 dni i nie ma żadnych błędów w plikach dziennika.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 002c45c514b5d8207a1aa70ec8e1c749677a239a
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620885"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Scenariusz: Apache Spark przetrzymywanie aplikacji przesyłania strumieniowego po 24 dniach w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Aplikacja przesyłania strumieniowego Apache Spark przestanie działać po upływie 24 dni i nie ma żadnych błędów w plikach dziennika.

## <a name="cause"></a>Przyczyna

Wartość `livy.server.session.timeout` określa, jak długo Apache usługi Livy powinien czekać na zakończenie sesji. Gdy długość sesji osiągnie `session.timeout` wartość, sesja usługi Livy i aplikacja są automatycznie zabijane.

## <a name="resolution"></a>Rozwiązanie

W przypadku długotrwałych zadań Zwiększ wartość `livy.server.session.timeout` przy użyciu interfejsu użytkownika Ambari. Możesz uzyskać dostęp do konfiguracji usługi Livy z interfejsu użytkownika Ambari przy użyciu adresu `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`URL.

Zamień `<yourclustername>` na nazwę klastra usługi HDInsight, jak pokazano w portalu.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
