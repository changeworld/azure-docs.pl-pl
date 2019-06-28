---
title: Skonfiguruj harmonogram dla klastrów HDInsight opartych na systemie Linux — Azure stosowania poprawek systemu operacyjnego
description: Dowiedz się, jak skonfigurować harmonogram dla klastrów HDInsight opartych na systemie Linux stosowania poprawek systemu operacyjnego.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: 3fad8869a31688e9e2413abb350eccf1f871f7dd
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330701"
---
# <a name="os-patching-for-hdinsight"></a>Stosowanie poprawek dla HDInsight systemu operacyjnego 

> [!IMPORTANT]
> Obrazy Ubuntu stają się dostępne do tworzenia nowego klastra HDInsight w ciągu trzech miesięcy od dnia publikacji. Począwszy od stycznia 2019 r, są uruchomione klastrów **nie** poprawiono automatycznie. Klienci, należy użyć akcji skryptu lub innych mechanizmów zastosowania poprawki względem działającego klastra. Nowo utworzony klastry mają zawsze najnowsze dostępne aktualizacje, w tym najnowsze poprawki zabezpieczeń.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Jak skonfigurować harmonogram dla klastrów HDInsight opartych na systemie Linux stosowania poprawek systemu operacyjnego
Maszyny wirtualne w klastrze usługi HDInsight, należy wykonać ponowny rozruch od czasu do czasu, dzięki czemu można zainstalować poprawki zabezpieczeń ważne. 

Za pomocą akcji skryptu, opisane w tym artykule, można zmodyfikować systemu operacyjnego, harmonogram stosowania poprawek w następujący sposób:
1. Zainstaluj wszystkie aktualizacje lub jądra instalacji i aktualizacji zabezpieczeń czy instalacja aktualizacji jądra tylko.
2. Natychmiastowe ponowne uruchomienie komputera lub harmonogram ponownego uruchomienia na maszynie Wirtualnej.

> [!NOTE]  
> Te akcje skryptu działa tylko z klastrami HDInsight opartych na systemie Linux, utworzonych po 1 sierpnia 2016 r. Poprawki będą obowiązywać tylko wtedy, gdy wykonywany jest ponowny rozruch maszyn wirtualnych. Skrypty te nie będą automatycznie stosowane aktualizacje dla wszystkich przyszłych aktualizacji cykli. Uruchamianie skryptów, które każdy czas nowe aktualizacje, które należy zastosować w celu zainstalowania aktualizacji, a następnie uruchom ponownie maszynę Wirtualną.

## <a name="how-to-use-the-script"></a>Jak użyć skryptu 

Za pomocą ten skrypt wymaga następujących informacji:
1. Lokalizację skryptu install aktualizacje — harmonogram — ponowny rozruch: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight używa tego identyfikatora URI do znajdowania i uruchamiania skryptu na wszystkich maszynach wirtualnych w klastrze. Ten skrypt zawiera opcje, aby zainstalować aktualizacje i uruchom ponownie maszynę Wirtualną.
  
2. Ponowne uruchamianie harmonogramu lokalizację skryptu: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight używa tego identyfikatora URI do znajdowania i uruchamiania skryptu na wszystkich maszynach wirtualnych w klastrze. Ten skrypt wykonuje ponowny rozruch maszyny Wirtualnej.
  
3. Typy węzłów klastra, które skryptu są stosowane do: węzeł główny, workernode, zookeeper. Ten skrypt należy zastosować do wszystkich typów węzłów w klastrze. Jeśli nie ma zastosowania do typu węzła, następnie maszyn wirtualnych, dla tego typu węzła będzie nie zostać zaktualizowane lub ponownie uruchomiony.

4. Parametr: Skrypt instalacji — aktualizacje — harmonogram — ponowne uruchamianie akceptuje dwóch parametrów liczbowych:

    | Parametr | Definicja |
    | --- | --- |
    | Zainstaluj tylko aktualizacji jądra / instalowanie wszystkich instalacji/aktualizacji jądra + security tylko aktualizacje |0, 1 lub 2. Wartość 0 instaluje tylko aktualizacje jądra podczas 1 instaluje wszystkie aktualizacje i jądra instaluje 2 + security, tylko aktualizacji. Jeśli parametr nie zostanie podany, wartość domyślna to 0. |
    | Nie ponownego uruchomienia/Włącz harmonogram ponowny rozruch/Włącz natychmiastowego ponownego uruchomienia |0, 1 lub 2. Wartość 0 wyłącza ponowne uruchomienie komputera, gdy 1 włącza harmonogram ponownego uruchomienia i 2 umożliwia natychmiastowe ponowne uruchomienie komputera. Jeśli parametr nie zostanie podany, wartość domyślna to 0. Użytkownik, musisz podać parametr 1 do 2 parametr wejściowy. |
   
 5. Parametr: Skrypt uruchamiany ponownie harmonogram akceptuje jednego parametru liczbowego:

    | Parametr | Definicja |
    | --- | --- |
    | Włącz harmonogram ponownego uruchomienia/Włącz natychmiastowego ponownego uruchomienia |1 lub 2. Wartość 1 włącza harmonogram ponowne uruchomienie (ponowny rozruch jest zaplanowane w ciągu 12 – 24 godzin) podczas 2 umożliwia natychmiastowe ponowne uruchomienie (w ciągu 5 minut). Jeśli parametr nie zostanie podany, wartość domyślna to 1. |  

> [!NOTE] 
> Należy oznaczyć skrypt jako utrwalone po zastosowaniu wbudowanej w istniejącym klastrze. W przeciwnym razie wszelkie nowe węzły utworzone za pośrednictwem operacji skalowania użyje domyślnej harmonogram stosowania poprawek.  Jeśli zastosujesz skrypt jako część procesu tworzenia klastra są utrwalane automatycznie.


## <a name="next-steps"></a>Kolejne kroki

Aby poznać konkretne kroki dotyczące za pomocą akcji skryptu, zobacz następujące sekcje w [HDInsight opartych na systemie Linux z Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md):

* [Za pomocą akcji skryptu, podczas tworzenia klastra](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Stosowanie akcji skryptu do działającego klastra](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
