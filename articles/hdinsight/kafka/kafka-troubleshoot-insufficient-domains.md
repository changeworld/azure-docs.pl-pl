---
title: Za mało domen błędów w błędzie regionu w usłudze Azure HDInsight
description: Tworzenie klastra nie powiodło się z powodu niewystarczającej liczby domen błędów w regionie usługi Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8be7e05ac85ce0b1b10edf18d3885a07e016b9ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895029"
---
# <a name="scenario-cluster-creation-failed-due-to-not-sufficient-fault-domains-in-region-in-azure-hdinsight"></a>Scenariusz: tworzenie klastra `not sufficient fault domains in region` nie powiodło się z powodu usługi Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Otrzymuj `not sufficient fault domains in region` komunikat o błędzie podobny do podczas próby utworzenia klastra Apache Kafka.

## <a name="cause"></a>Przyczyna

Domena błędów to logiczna grupa bazowego sprzętu w centrum danych platformy Azure. Wszystkie domeny błędów korzystają ze wspólnego źródła zasilania i przełącznika sieciowego. Maszyny wirtualne i dyski zarządzane, które implementują węzły w klastrze usługi HDInsight są rozdzielone między te domeny błędów. Taka architektura ogranicza wpływ potencjalnych awarii sprzętu fizycznego.

W każdym regionie świadczenia usługi Azure znajduje się określona liczba domen błędów. Lista domen i liczba domen błędów, które zawierają, można znaleźć w dokumentacji [dotyczącej zestawów dostępności](../../virtual-machines/windows/manage-availability.md).

W umiań HDInsight klastry platformy Kafka muszą być aprowizacji w regionie z co najmniej trzech domen błędów.

## <a name="resolution"></a>Rozwiązanie

Jeśli region, który chcesz utworzyć klaster nie ma wystarczających domen błędów, skontaktuj się z zespołem produktu, aby umożliwić inicjowanie obsługi administracyjnej klastra, nawet jeśli nie istnieją trzy domeny błędów.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
