---
title: Skonfiguruj harmonogram dla klastrów HDInsight opartych na systemie Linux — Azure stosowania poprawek systemu operacyjnego
description: Dowiedz się, jak skonfigurować harmonogram dla klastrów HDInsight opartych na systemie Linux stosowania poprawek systemu operacyjnego.
services: hdinsight
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2017
ms.openlocfilehash: 5d8fae7412bece631e6961fb92b7d87e308cc857
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599218"
---
# <a name="os-patching-for-hdinsight"></a>Stosowanie poprawek dla HDInsight systemu operacyjnego 
Jako zarządzana usługa Hadoop HDInsight zajmuje się stosowanie poprawek systemu operacyjnego podstawowych maszyn wirtualnych, które korzystają z klastrów HDInsight. Począwszy od 1 sierpnia 2016 r. dokonaliśmy zmiany zasad stosowania poprawek systemu operacyjnego gościa w przypadku klastrów HDInsight opartych na systemie Linux (w wersji 3.4 lub nowszą). Celem nowych zasad jest znacznie zmniejszyć liczbę ponownego uruchomienia ze względu na stosowanie poprawek. Nowe zasady będą w dalszym poprawki maszyn wirtualnych (VM) w klastrach systemu Linux w każdy poniedziałek lub czwartek, począwszy od 12: 00 UTC w sposób rozłożone między węzłami w dowolnym danym klastrze. Jednak wszelkie danej maszyny Wirtualnej będzie tylko ponowny rozruch co najwyżej raz na 30 dni z powodu stosowania poprawek systemu operacyjnego gościa. Ponadto to pierwszy ponowny rozruch dla nowo utworzonego klastra nie nastąpi szybciej niż 30 dni od daty utworzenia klastra. Poprawki będą obowiązywać po wykonywany jest ponowny rozruch maszyn wirtualnych.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Jak skonfigurować harmonogram dla klastrów HDInsight opartych na systemie Linux stosowania poprawek systemu operacyjnego
Maszyny wirtualne w klastrze usługi HDInsight, należy wykonać ponowny rozruch od czasu do czasu, dzięki czemu można zainstalować poprawki zabezpieczeń ważne. Począwszy od 1 sierpnia 2016 r. nowe klastry HDInsight opartych na systemie Linux (w wersji 3.4 lub nowszego) wykonywany jest ponowny rozruch przy użyciu następującego harmonogramu:

1. Maszyny wirtualnej w klastrze można tylko ponownie uruchomić dla poprawki co najwyżej jeden raz w ciągu 30-dniowego okresu.
2. Ponowne uruchomienie komputera występuje, począwszy od stawki 0: 00 czasu UTC.
3. Proces ponownego rozruchu jest rozłożona na maszynach wirtualnych w klastrze, więc klastra jest nadal dostępny w procesie ponownego uruchomienia.
4. To pierwszy ponowny rozruch dla nowo utworzonego klastra nie nastąpi szybciej niż 30 dni od daty utworzenia klastra.

Za pomocą akcji skryptu, opisane w tym artykule, można zmodyfikować systemu operacyjnego, harmonogram stosowania poprawek w następujący sposób:
1. Włączanie lub wyłączanie automatycznego ponownego uruchamiania
2. Ustaw częstotliwość (w dniach między ponownymi uruchomieniami) wykonuje ponowny rozruch
3. Ustawiony dzień tygodnia, gdy odbywa się ponowne uruchomienie komputera

> [!NOTE]
> Ta akcja skryptu będzie działać tylko z klastrami HDInsight opartych na systemie Linux, utworzonych po 1 sierpnia 2016 r. Poprawki będą obowiązywać tylko wtedy, gdy wykonywany jest ponowny rozruch maszyn wirtualnych. 
>

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
> Ten skrypt należy oznaczyć jako utrwalone po zastosowaniu wbudowanej w istniejącym klastrze. W przeciwnym razie wszelkie nowe węzły utworzone za pośrednictwem operacji skalowania użyje domyślnej harmonogram stosowania poprawek.
Jeśli zastosujesz skrypt jako część procesu tworzenia klastra są utrwalane automatycznie.
>

## <a name="next-steps"></a>Kolejne kroki

Aby poznać konkretne kroki dotyczące za pomocą akcji skryptu, zobacz następujące sekcje w [HDInsight opartych na Linuz Dostosowywanie klastrów za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md):

* [Za pomocą akcji skryptu, podczas tworzenia klastra](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Stosowanie akcji skryptu do działającego klastra](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
