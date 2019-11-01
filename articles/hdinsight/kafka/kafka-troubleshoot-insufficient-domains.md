---
title: W usłudze Azure HDInsight nie ma wystarczającej liczby domen błędów w regionie
description: Tworzenie klastra nie powiodło się z powodu niewystarczających domen błędów w regionie w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: defb0666a6537d62b22dca301f69a5163e887d3f
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241907"
---
# <a name="scenario-cluster-creation-failed-due-to-not-sufficient-fault-domains-in-region-in-azure-hdinsight"></a>Scenariusz: Tworzenie klastra nie powiodło się z powodu `not sufficient fault domains in region` w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas próby utworzenia klastra Apache Kafka zostanie wyświetlony komunikat o błędzie podobny do `not sufficient fault domains in region`.

## <a name="cause"></a>Przyczyna

Domena błędów to logiczna grupa bazowego sprzętu w centrum danych platformy Azure. Wszystkie domeny błędów korzystają ze wspólnego źródła zasilania i przełącznika sieciowego. Maszyny wirtualne i dyski zarządzane, które implementują węzły w klastrze usługi HDInsight są rozdzielone między te domeny błędów. Taka architektura ogranicza wpływ potencjalnych awarii sprzętu fizycznego.

W każdym regionie świadczenia usługi Azure znajduje się określona liczba domen błędów. Aby uzyskać listę domen i liczbę zawartych w nich domen błędów, zapoznaj się z dokumentacją dotyczącą [zestawów dostępności](../../virtual-machines/windows/manage-availability.md).

W usłudze HDInsight Klastry Kafka są wymagane do aprowizacji w regionie z co najmniej trzema domenami błędów.

## <a name="resolution"></a>Rozdzielczość

Jeśli region, w którym chcesz utworzyć klaster, nie ma wystarczających domen błędów, skontaktuj się z zespołem produktu, aby zezwolić na udostępnianie klastra, nawet jeśli nie ma trzech domen błędów.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure w celu poprawy obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
