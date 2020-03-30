---
title: Błąd procesora cpu programu Watchdog BUG z klastra Usługi Azure HDInsight
description: Watchdog BUG soft lockup CPU pojawia się w syslogach jądra z klastra Usługi Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894106"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Scenariusz: "watchdog: BUG: soft lockup - CPU" błąd z klastra usługi Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Syslogs jądra zawierają komunikat `watchdog: BUG: soft lockup - CPU`o błędzie: .

## <a name="cause"></a>Przyczyna

[Błąd](https://bugzilla.kernel.org/show_bug.cgi?id=199437) w jądrze Linuksa powoduje miękkie blokady procesora.

## <a name="resolution"></a>Rozwiązanie

Zastosuj poprawkę jądra. Poniższy skrypt uaktualnia jądro Linuksa i ponownie uruchamia maszyny w różnym czasie przez 24 godziny. Wykonaj akcję skryptu w dwóch partiach. Pierwsza partia znajduje się na wszystkich węzłach z wyjątkiem węzła głównego. Druga partia znajduje się w węźle głównym. Nie należy uruchamiać w węźle głównym i innych węzłów w tym samym czasie.

1. Przejdź do klastra USŁUGI HDInsight z witryny Azure portal.

1. Przejdź do akcji skryptu.

1. Wybierz **pozycję Prześlij nowy** i wprowadź dane wejściowe w następujący sposób

    | Właściwość | Wartość |
    | --- | --- |
    | Typ skryptu | -Niestandardowe |
    | Nazwa |Naprawiono problem z blokadą miękką jądra |
    | Identyfikator URI skryptu bash |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Typy węzłów |Pracownik, Zookeeper |
    | Parametry |Nie dotyczy |

    Wybierz **opcję Utrwalić tę akcję skryptu ...** jeśli chcesz wykonać skrypt po dodaniu nowych węzłów.

1. Wybierz **pozycję Utwórz**.

1. Poczekaj na wykonanie zakończyć się pomyślnie.

1. Wykonaj akcję skryptu w węźle głównym, wykonując te same kroki co krok 3, ale tym razem z typami węzłów: head.

1. Poczekaj na wykonanie zakończyć się pomyślnie.

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
