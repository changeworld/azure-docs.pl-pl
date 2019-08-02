---
title: Błąd obszaru sterty Java podczas próby otwarcia serwera historii Apache Spark w usłudze Azure HDInsight
description: Nie można uruchomić serwera Apache usługi Livy przy użyciu języka Java. lang. OutOfMemoryError w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 5c2ae90bdca8512802c845a5ac58c3c4aeedd5b7
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667801"
---
# <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server-in-azure-hdinsight"></a>Scenariusz: Błąd obszaru sterty Java podczas próby otwarcia serwera historii Apache Spark w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas otwierania zdarzeń na serwerze historii platformy Spark pojawia się następujący błąd:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

## <a name="cause"></a>Przyczyna

Ten problem często jest spowodowany brakiem zasobów podczas otwierania dużych plików zdarzeń Spark. Rozmiar sterty Spark jest domyślnie ustawiony na 1 GB, ale duże pliki zdarzeń platformy Spark mogą wymagać więcej niż tego.

Jeśli chcesz sprawdzić rozmiar plików, które próbujesz załadować, możesz wykonać następujące polecenia:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

## <a name="resolution"></a>Rozwiązanie

Pamięć serwera historii platformy Spark można zwiększyć, edytując `SPARK_DAEMON_MEMORY` właściwość w konfiguracji platformy Spark i uruchamiając ponownie wszystkie usługi.

Można to zrobić z poziomu interfejsu użytkownika przeglądarki Ambari, wybierając sekcję Spark2/config/Advanced Spark2-ENV.

![Advanced spark2 — sekcja ENV](./media/apache-spark-ts-outofmemory-heap-space/image01.png)

Dodaj następującą właściwość, aby zmienić pamięć serwera historii platformy Spark z 1g na 4G: `SPARK_DAEMON_MEMORY=4g`.

![Właściwość platformy Spark](./media/apache-spark-ts-outofmemory-heap-space/image02.png)

Upewnij się, że wszystkie usługi, których to dotyczy, zostały uruchomione ponownie z Ambari.

## <a name="next-steps"></a>Kolejne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
