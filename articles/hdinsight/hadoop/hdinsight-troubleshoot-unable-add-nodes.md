---
title: Nie można dodać węzłów do klastra usługi Azure HDInsight
description: Rozwiązywanie problemów, dlaczego nie można dodać węzłów do klastra Apache Hadoop w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 97d7f34fff324a9959292460e534c15110c3e532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894096"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scenariusz: Nie można dodać węzłów do klastra usługi Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Nie można dodać węzłów do klastra usługi Azure HDInsight.

## <a name="cause"></a>Przyczyna

Przyczyny mogą się różnić.

## <a name="resolution"></a>Rozwiązanie

Korzystając z funkcji [Rozmiar klastra,](../hdinsight-scaling-best-practices.md) oblicz liczbę dodatkowych rdzeni potrzebnych dla klastra. Zależy to od łącznej liczby rdzeni w nowych węzłach procesów roboczych. Następnie spróbuj wykonać co najmniej jeden z następujących kroków:

* Sprawdź, czy są dostępne rdzenie w lokalizacji klastra.

* Sprawdź liczbę dostępnych rdzeni w innych lokalizacjach. Rozważ ponowne utworzenie klastra w innej lokalizacji z odpowiednią liczbą dostępnych rdzeni.

* Aby zwiększyć limit przydziału rdzeni dla określonej lokalizacji, wyślij bilet pomocy technicznej dotyczący zwiększenia limitu przydziału rdzeni w usłudze HDInsight.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
