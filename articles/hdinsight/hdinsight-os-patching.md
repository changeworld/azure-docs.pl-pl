---
title: Skonfiguruj harmonogram dla klastrów HDInsight opartych na systemie Linux — Azure stosowania poprawek systemu operacyjnego
description: Dowiedz się, jak skonfigurować harmonogram dla klastrów HDInsight opartych na systemie Linux stosowania poprawek systemu operacyjnego.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: 5b8ed75863087e077d483c792ac4134a0c3e1eb0
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203636"
---
# <a name="os-patching-for-hdinsight"></a>Stosowanie poprawek dla HDInsight systemu operacyjnego 

> [!IMPORTANT]
> Obrazy Ubuntu stają się dostępne dla nowego Tworzenie klastra HDInsight w ciągu 3 miesięcy opublikować. Począwszy od stycznia 2019 r, są uruchomione klastrów **nie** poprawiono automatycznie. Klienci, należy użyć akcji skryptu lub innych mechanizmów zastosowania poprawki względem działającego klastra. Nowo utworzony klastry mają zawsze najnowsze dostępne aktualizacje, w tym najnowsze poprawki zabezpieczeń.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Jak skonfigurować harmonogram dla klastrów HDInsight opartych na systemie Linux stosowania poprawek systemu operacyjnego
Maszyny wirtualne w klastrze usługi HDInsight, należy wykonać ponowny rozruch od czasu do czasu, dzięki czemu można zainstalować poprawki zabezpieczeń ważne. 

Za pomocą akcji skryptu, opisane w tym artykule, można zmodyfikować systemu operacyjnego, harmonogram stosowania poprawek w następujący sposób:
1. Zainstalować pełne aktualizacje systemu operacyjnego lub aktualizacji zabezpieczeń
2. Uruchom ponownie maszynę Wirtualną

> [!NOTE]  
> Ta akcja skryptu będzie działać tylko z klastrami HDInsight opartych na systemie Linux, utworzonych po 1 sierpnia 2016 r. Poprawki będą obowiązywać tylko wtedy, gdy wykonywany jest ponowny rozruch maszyn wirtualnych. Ten skrypt nie będą automatycznie stosowane aktualizacje dla wszystkich cykli przyszłej aktualizacji. Uruchom skrypt, który każdego czasu nowe aktualizacje, które należy zastosować w celu zainstalowania aktualizacji, a następnie uruchom ponownie maszynę Wirtualną.

## <a name="how-to-use-the-script"></a>Jak użyć skryptu 

Gdy za pomocą ten skrypt wymaga następujących informacji:
1. Lokalizacja skryptu: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/os-patching-reboot-config.sh.  HDInsight używa tego identyfikatora URI do znajdowania i uruchamiania skryptu na wszystkich maszynach wirtualnych w klastrze.
  
2. Typy węzłów klastra, które skryptu są stosowane do: węzeł główny, workernode, zookeeper. Ten skrypt należy zastosować do wszystkich typów węzłów w klastrze. Jeśli nie ma zastosowania do typu węzła, maszyny wirtualne, dla tego typu węzła nie można zaktualizować.


3.  Parametr: Ten skrypt akceptuje jednego parametru liczbowego:

    | Parametr | Definicja |
    | --- | --- |
    | Instalacji pełnej wersji systemu operacyjnego/instalacja aktualizacji tylko aktualizacje zabezpieczeń |0 lub 1. Wartość 0 instaluje aktualizacje zabezpieczeń, tylko wtedy, gdy 1 instaluje aktualizacje pełnego systemu operacyjnego. Jeśli parametr nie zostanie podany, wartość domyślna to 0. |

> [!NOTE]  
> Ten skrypt należy oznaczyć jako utrwalone po zastosowaniu wbudowanej w istniejącym klastrze. W przeciwnym razie wszelkie nowe węzły utworzone za pośrednictwem operacji skalowania użyje domyślnej harmonogram stosowania poprawek.  Jeśli zastosujesz skrypt jako część procesu tworzenia klastra są utrwalane automatycznie.

## <a name="next-steps"></a>Kolejne kroki

Aby poznać konkretne kroki dotyczące za pomocą akcji skryptu, zobacz następujące sekcje w [HDInsight opartych na systemie Linux z Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md):

* [Za pomocą akcji skryptu, podczas tworzenia klastra](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Stosowanie akcji skryptu do działającego klastra](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
