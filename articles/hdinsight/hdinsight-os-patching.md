---
title: Skonfiguruj harmonogram dla klastrów HDInsight opartych na systemie Linux — Azure stosowania poprawek systemu operacyjnego
description: Dowiedz się, jak skonfigurować harmonogram dla klastrów HDInsight opartych na systemie Linux stosowania poprawek systemu operacyjnego.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: efe74618b269000749f7ba6c24d35903e540dcfb
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657056"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Skonfiguruj harmonogram dla klastrów HDInsight opartych na systemie Linux stosowania poprawek systemu operacyjnego 

> [!IMPORTANT]
> Obrazy Ubuntu stają się dostępne dla tworzenia nowego klastra Azure HDInsight w ciągu trzech miesięcy od dnia publikacji. Począwszy od stycznia 2019 r uruchamianie klastrów nie są poprawiono automatycznie. Klienci, należy użyć akcji skryptu lub innych mechanizmów zastosowania poprawki względem działającego klastra. Nowo utworzony klastry mają zawsze najnowsze dostępne aktualizacje, w tym najnowsze poprawki zabezpieczeń.

Od czasu do czasu należy ponownie uruchomić maszyny wirtualne (VM) w klastrze usługi HDInsight w taki sposób, aby zainstalować poprawki zabezpieczeń ważne.

Za pomocą akcji skryptu, opisane w tym artykule, można zmodyfikować systemu operacyjnego, harmonogram stosowania poprawek w następujący sposób:

1. Zainstaluj wszystkie aktualizacje, lub zainstaluj tylko jądra i aktualizacji zabezpieczeń lub aktualizacji jądra.
2. Czy natychmiastowe ponowne uruchamianie lub Planowanie ponownego uruchomienia na maszynie Wirtualnej.

> [!NOTE]  
> Akcje skryptu opisanych w tym artykule będzie działać wyłącznie przy użyciu klastrów HDInsight opartych na systemie Linux, utworzonych po 1 sierpnia 2016 r. Poprawki zaczynają obowiązywać dopiero po ponownym uruchamianiem maszyn wirtualnych.
> Akcje skryptu nie będą automatycznie stosowane aktualizacje dla wszystkich cykli przyszłej aktualizacji. Uruchom skrypty za każdym razem, gdy nowe aktualizacje, które należy zastosować do zainstalowania aktualizacji, a następnie uruchom ponownie maszynę Wirtualną.

## <a name="add-information-to-the-script"></a>Dodawanie informacji do skryptu

Za pomocą skryptu wymaga następujących informacji:

- Lokalizację skryptu install aktualizacje — harmonogram — ponowny rozruch: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight używa tego identyfikatora URI do znajdowania i uruchamiania skryptu na wszystkich maszynach wirtualnych w klastrze. Ten skrypt zawiera opcje do zainstalowania aktualizacji i ponownego uruchomienia maszyny Wirtualnej.
  
- Ponowne uruchamianie harmonogramu lokalizację skryptu: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight używa tego identyfikatora URI do znajdowania i uruchamiania skryptu na wszystkich maszynach wirtualnych w klastrze. Ten skrypt powoduje ponowne uruchomienie maszyny Wirtualnej.
  
- Typy węzłów klastra, które skryptu są stosowane do to węzeł główny, workernode i zookeeper. Zastosuj skrypt do wszystkich typów węzłów w klastrze. Jeśli skrypt nie jest stosowany do typu węzła, maszyn wirtualnych dla tego typu węzła nie będą aktualizowane lub ponownego uruchomienia.

- Skrypt instalacji — aktualizacje — harmonogram — ponowne uruchamianie akceptuje dwóch parametrów liczbowych:

    | Parametr | Definicja |
    | --- | --- |
    | Zainstaluj tylko aktualizacji jądra / instalowanie wszystkich instalacji/aktualizacji jądra + security tylko aktualizacje|0, 1 lub 2. Wartość 0 zainstaluje tylko aktualizacje jądra. Wartość 1 instaluje wszystkie aktualizacje i 2 instaluje tylko jądra + aktualizacje zabezpieczeń. Jeśli parametr nie zostanie podany, wartość domyślna to 0. |
    | Nie ponownego uruchomienia/Włącz harmonogram ponowny rozruch/Włącz natychmiastowego ponownego uruchomienia |0, 1 lub 2. Wartość 0 wyłącza ponowne uruchomienie. Wartość 1 umożliwia ponowne uruchomienie harmonogramu, a 2 umożliwia natychmiastowe ponowne uruchomienie. Jeśli parametr nie zostanie podany, wartość domyślna to 0. Użytkownik musi zmienić parametr wejściowy 1 do 2 parametr wejściowy. |
   
 - Skrypt uruchamiany ponownie harmonogram akceptuje jednego parametru liczbowego:

    | Parametr | Definicja |
    | --- | --- |
    | Włącz harmonogram ponownego uruchomienia/Włącz natychmiastowego ponownego uruchomienia |1 lub 2. Wartość 1 umożliwia ponowne uruchomienie harmonogramu (zaplanowane 12 – 24 godzin). Wartość 2 umożliwia natychmiastowe ponowne uruchomienie (w ciągu 5 minut). Jeśli parametr nie zostanie podany, wartość domyślna to 1. |  

> [!NOTE]
> Należy oznaczyć skrypt jako utrwalone po zastosowaniu do istniejącego klastra. W przeciwnym razie wszelkie nowe węzły utworzone za pośrednictwem operacji skalowania użyje domyślnej harmonogram stosowania poprawek. Jeśli zastosujesz skrypt jako część procesu tworzenia klastra zawiera utrwalone automatycznie.


## <a name="next-steps"></a>Kolejne kroki

Aby poznać konkretne kroki dotyczące za pomocą akcji skryptu, zobacz następujące sekcje w [HDInsight opartych na systemie Linux z Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md):

* [Za pomocą akcji skryptu, podczas tworzenia klastra](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Stosowanie akcji skryptu do działającego klastra](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
