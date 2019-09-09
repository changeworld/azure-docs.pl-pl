---
title: Usługa Apache Ambari tez umożliwia spowolnienie ładowania w usłudze Azure HDInsight
description: Widok tez Apache Ambari może ładować się wolno lub nie ładować w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 4a0f6706781b0234942c473187474d0ab66e3cd4
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811564"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Scenariusz: Usługa Apache Ambari tez umożliwia spowolnienie ładowania w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania interakcyjnych składników zapytań w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Widok tez Apache Ambari może ładować się wolno lub nie można go załadować. Podczas ładowania widoku Ambari tez mogą być widoczne procesy na węzłów głównych, które nie odpowiadają.

## <a name="cause"></a>Przyczyna

Dostęp do interfejsów API przędzy ATS może czasami mieć niską wydajność w przypadku klastrów utworzonych przed OCT 2017, gdy w klastrze jest uruchomionych wiele zadań Hive.

## <a name="resolution"></a>Rozwiązanie

Jest to problem, który został rozwiązany w październiku 2017. Ponowne utworzenie klastra spowoduje, że ten problem nie zostanie uruchomiony ponownie.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
