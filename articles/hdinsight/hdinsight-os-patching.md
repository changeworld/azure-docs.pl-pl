---
title: Skonfiguruj harmonogram dla klastrów HDInsight opartych na systemie Linux — Azure stosowania poprawek systemu operacyjnego
description: Dowiedz się, jak skonfigurować harmonogram dla klastrów HDInsight opartych na systemie Linux stosowania poprawek systemu operacyjnego.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: a887d6c69b9fa80f3144434e72a097e80d123a1b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64722301"
---
# <a name="os-patching-for-hdinsight"></a>Stosowanie poprawek dla HDInsight systemu operacyjnego 

> [!IMPORTANT]
> Obrazy Ubuntu stają się dostępne dla nowego Tworzenie klastra HDInsight w ciągu 3 miesięcy opublikować. Począwszy od stycznia 2019 r, są uruchomione klastrów **nie** poprawiono automatycznie. Klienci, należy użyć akcji skryptu lub innych mechanizmów zastosowania poprawki względem działającego klastra. Nowo utworzony klastry mają zawsze najnowsze dostępne aktualizacje, w tym najnowsze poprawki zabezpieczeń.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Jak skonfigurować harmonogram dla klastrów HDInsight opartych na systemie Linux stosowania poprawek systemu operacyjnego
Maszyny wirtualne w klastrze usługi HDInsight, należy wykonać ponowny rozruch od czasu do czasu, dzięki czemu można zainstalować poprawki zabezpieczeń ważne. 

Za pomocą akcji skryptu, opisane w tym artykule, można zmodyfikować systemu operacyjnego, harmonogram stosowania poprawek w następujący sposób:
1. Włączanie lub wyłączanie automatycznego ponownego uruchamiania
2. Ustaw częstotliwość (w dniach między ponownymi uruchomieniami) wykonuje ponowny rozruch
3. Ustawiony dzień tygodnia, gdy odbywa się ponowne uruchomienie komputera

> [!NOTE]  
> Ta akcja skryptu będzie działać tylko z klastrami HDInsight opartych na systemie Linux, utworzonych po 1 sierpnia 2016 r. Poprawki będą obowiązywać tylko wtedy, gdy wykonywany jest ponowny rozruch maszyn wirtualnych. 

## <a name="how-to-use-the-script"></a>Jak użyć skryptu 

Gdy za pomocą ten skrypt wymaga następujących informacji:
1. Lokalizacja skryptu: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv01/os-patching-reboot-config.sh.  HDInsight używa tego identyfikatora URI do znajdowania i uruchamiania skryptu na wszystkich maszynach wirtualnych w klastrze.
  
2. Typy węzłów klastra, które skryptu są stosowane do: węzeł główny, workernode, zookeeper. Ten skrypt należy zastosować do wszystkich typów węzłów w klastrze. Jeśli nie ma zastosowania do typu węzła, maszyn wirtualnych, dla tego typu węzła będą nadal używać poprzedniej harmonogram stosowania poprawek.


3.  Parametr: Ten skrypt akceptuje trzech parametrów liczbowych:

    | Parametr | Definicja |
    | --- | --- |
    | Włączanie/wyłączanie automatycznego ponownego uruchamiania |0 lub 1. Wartość 0 powoduje wyłączenie automatycznego ponownego uruchamiania, a 1 umożliwia automatycznego ponownego uruchamiania. |
    | Częstotliwość |7 – 90 (włącznie). Liczba dni oczekiwania przed ponownym rozruchem maszyny wirtualne pod kątem poprawek, które wymagają ponownego uruchomienia. |
    | Dzień tygodnia |1 do 7 (włącznie). Wartość 1 oznacza ponowne uruchomienie powinno nastąpić w poniedziałek i 7 wskazuje Sunday.For przykład za pomocą parametrów od 2 do 60 1 powoduje automatyczne ponowne uruchomienie co 60 dni (maksymalnie) na wtorek. |
    | Trwałość |Podczas stosowania akcji skryptu do istniejącego klastra, możesz oznaczyć skrypt jako utrwalone. Utrwalonych skryptów są stosowane, gdy nowy workernodes zostaną dodane do klastra za pośrednictwem operacji skalowania. |

> [!NOTE]  
> Ten skrypt należy oznaczyć jako utrwalone po zastosowaniu wbudowanej w istniejącym klastrze. W przeciwnym razie wszelkie nowe węzły utworzone za pośrednictwem operacji skalowania użyje domyślnej harmonogram stosowania poprawek.  Jeśli zastosujesz skrypt jako część procesu tworzenia klastra są utrwalane automatycznie.

## <a name="next-steps"></a>Kolejne kroki

Aby poznać konkretne kroki dotyczące za pomocą akcji skryptu, zobacz następujące sekcje w [HDInsight opartych na systemie Linux z Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md):

* [Za pomocą akcji skryptu, podczas tworzenia klastra](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Stosowanie akcji skryptu do działającego klastra](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
