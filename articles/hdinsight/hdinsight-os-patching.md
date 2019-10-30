---
title: Konfigurowanie harmonogramu poprawek systemu operacyjnego dla klastrów usługi Azure HDInsight
description: Dowiedz się, jak skonfigurować harmonogram stosowania poprawek systemu operacyjnego dla klastrów usługi HDInsight opartych na systemie Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: d0a490fd3b23c96923af10db3c1f9ee9ea0dfad5
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044879"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Konfigurowanie harmonogramu poprawek systemu operacyjnego dla klastrów usługi HDInsight opartych na systemie Linux 

> [!IMPORTANT]
> Obrazy Ubuntu są dostępne dla nowego klastra usługi Azure HDInsight w ciągu trzech miesięcy od opublikowania. Począwszy od stycznia 2019, uruchomione klastry nie są stosowane do autopoprawek. Aby zastosować poprawki do działającego klastra, klienci muszą używać akcji skryptu lub innych mechanizmów. Nowo utworzone klastry będą zawsze miały najnowsze dostępne aktualizacje, w tym najnowsze poprawki zabezpieczeń.

Czasami należy ponownie uruchomić maszyny wirtualne w klastrze usługi HDInsight w celu zainstalowania ważnych poprawek zabezpieczeń.

Za pomocą akcji skryptu opisanych w tym artykule można zmodyfikować harmonogram stosowania poprawek systemu operacyjnego w następujący sposób:

1. Zainstaluj wszystkie aktualizacje lub Zainstaluj tylko aktualizacje jądra + zabezpieczenia lub aktualizacje jądra.
2. Wykonaj natychmiastowe ponowne uruchomienie lub Zaplanuj ponowne uruchomienie maszyny wirtualnej.

> [!NOTE]  
> Akcje skryptu opisane w tym artykule będą działały tylko z klastrami usługi HDInsight opartymi na systemie Linux utworzonymi po 1 sierpnia 2016. Poprawki zaczynają obowiązywać dopiero po ponownym uruchomieniu maszyn wirtualnych.
> Akcje skryptu nie będą automatycznie stosowały aktualizacji dla wszystkich przyszłych cykli aktualizacji. Uruchom skrypty za każdym razem, gdy trzeba zastosować nowe aktualizacje, aby zainstalować aktualizacje, a następnie uruchom ponownie maszynę wirtualną.

## <a name="add-information-to-the-script"></a>Dodawanie informacji do skryptu

Przy użyciu skryptu wymagane są następujące informacje:

- Lokalizacja skryptu Install-Updates-Schedule-reboot: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   Usługa HDInsight używa tego identyfikatora URI do znajdowania i uruchamiania skryptu na wszystkich maszynach wirtualnych w klastrze. Ten skrypt zawiera opcje instalowania aktualizacji i ponownego uruchamiania maszyny wirtualnej.
  
- Lokalizacja skryptu harmonogram-ponowny rozruch: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   Usługa HDInsight używa tego identyfikatora URI do znajdowania i uruchamiania skryptu na wszystkich maszynach wirtualnych w klastrze. Ten skrypt uruchamia ponownie maszynę wirtualną.
  
- Typy węzłów klastra, do których zastosowano skrypt, to węzła głównego, workernode i dozorcy. Zastosuj skrypt do wszystkich typów węzłów w klastrze. Jeśli skrypt nie zostanie zastosowany do typu węzła, maszyny wirtualne dla tego typu węzła nie zostaną zaktualizowane ani uruchomione ponownie.

- Skrypt install-Updates-Schedule-reboot przyjmuje dwa parametry liczbowe:

    | Parametr | Definicja |
    | --- | --- |
    | Zainstaluj tylko aktualizacje jądra/Zainstaluj wszystkie aktualizacje/Zainstaluj tylko aktualizacje jądra +|0, 1 lub 2. Wartość 0 powoduje zainstalowanie tylko aktualizacji jądra. Wartość 1 powoduje zainstalowanie wszystkich aktualizacji, a 2 instaluje tylko aktualizacje jądra i zabezpieczeń. Jeśli parametr nie zostanie podany, wartość domyślna to 0. |
    | Bez ponownego uruchamiania/Włącz planowanie ponownego uruchamiania/Włącz natychmiastowe ponowne uruchomienie |0, 1 lub 2. Wartość 0 powoduje wyłączenie ponownego uruchomienia. Wartość 1 włącza ponowne uruchomienie harmonogramu, a 2 włącza natychmiastowe ponowne uruchomienie. Jeśli parametr nie zostanie podany, wartość domyślna to 0. Użytkownik musi zmienić parametr wejściowy 1 na parametr wejściowy 2. |
   
 - Skrypt harmonogram-ponowny rozruch akceptuje jeden parametr liczbowy:

    | Parametr | Definicja |
    | --- | --- |
    | Włącz zaplanowane ponowne uruchomienie/włączenie natychmiastowego ponownego uruchomienia |1 lub 2. Wartość 1 włącza zaplanowanie ponownego uruchomienia (zaplanowane w 12-24 godzinach). Wartość 2 włącza natychmiastowe ponowne uruchomienie (w ciągu 5 minut). Jeśli parametr nie zostanie określony, wartość domyślna to 1. |  

> [!NOTE]
> Skrypt należy oznaczyć jako utrwalony po zastosowaniu go w istniejącym klastrze. W przeciwnym razie wszystkie nowe węzły utworzone za pomocą operacji skalowania będą używały domyślnego harmonogramu poprawek. Jeśli zastosujesz skrypt w ramach procesu tworzenia klastra, zostanie on utrwalony automatycznie.


## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z konkretnymi krokami dotyczącymi używania akcji skryptu, zobacz następujące sekcje w temacie [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md):

* [Używanie akcji skryptu podczas tworzenia klastra](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Zastosuj akcję skryptu do działającego klastra](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
