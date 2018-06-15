---
title: Konfigurowanie systemu operacyjnego poprawki harmonogram dla opartych na systemie Linux klastrów usługi HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować harmonogram dla klastrów usługi HDInsight opartych na systemie Linux poprawki systemu operacyjnego.
services: hdinsight
documentationcenter: ''
author: bprakash
manager: asadk
editor: bprakash
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: bhanupr
ms.openlocfilehash: 42771b9ff0f177b6b31f626d1dd2d07046a53965
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31409759"
---
# <a name="os-patching-for-hdinsight"></a>Poprawki dla usługi HDInsight systemu operacyjnego 
Jako zarządzanej usługi Hadoop HDInsight zapewnia obsługę stosowanie poprawek systemu operacyjnego źródłowej maszyn wirtualnych używanych przez klastry usługi HDInsight. Począwszy od 1 sierpnia 2016 zmieniono zasady stosowania poprawek systemu operacyjnego gościa opartych na systemie Linux klastrów usługi HDInsight (w wersji 3.4 lub nowszej). Celem nowych zasad jest znacznie zmniejszyć liczbę ponownych uruchomień komputera z powodu stosowania poprawek. Nowe zasady będzie poprawki maszynach wirtualnych (VM) w systemie Linux klastrów każdego poniedziałek i czwartek, zaczynając od 00: 00 czasu UTC, w sposób rozłożone w węzłach żadnego danego klastra. Jednak żadnej danej maszyny Wirtualnej zostanie uruchomiony tylko co najwyżej raz na 30 dni z powodu stosowania poprawek systemu operacyjnego gościa. Ponadto ponownym dla nowo utworzony klaster nie nastąpi szybciej niż 30 dni od daty utworzenia klastra. Poprawki zostaną zastosowane po maszyn wirtualnych są ponownie uruchamiane.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Jak skonfigurować system operacyjny poprawki harmonogram dla klastrów usługi HDInsight opartych na systemie Linux
Maszyny wirtualne w klastrze HDInsight wymagać ponownego uruchomienia od czasu do czasu, dzięki czemu można zainstalować poprawki zabezpieczeń ważne. Począwszy od 1 sierpnia 2016 nowych klastrów usługi HDInsight opartej na systemie Linux (w wersji 3.4 lub nowszego) są ponownie uruchamiane przy użyciu następującego harmonogramu:

1. Maszyny wirtualnej w klastrze można tylko ponownego uruchomienia dla poprawek co najwyżej jeden raz w ciągu 30-dniowego okresu.
2. Występuje ponownego uruchamiania, zaczynając od 00: 00 czasu UTC.
3. Proces ponownego rozruchu jest rozłożona między maszynami wirtualnymi w klastrze, więc klastra jest nadal dostępny podczas procesu ponownego uruchomienia.
4. Pierwszy ponowny rozruch dla nowo utworzony klaster nie nastąpi szybciej niż 30 dni od daty utworzenia klastra.

Za pomocą akcji skryptu opisane w tym artykule, można zmodyfikować OS poprawki harmonogramu w następujący sposób:
1. Włącz lub wyłącz automatyczne ponowne uruchomienie
2. Ustaw częstotliwość ponownego uruchamiania (w dniach od ponownego uruchomienia)
3. Ustawiony dzień tygodnia, gdy wystąpi ponowny rozruch

> [!NOTE]
> Ta akcja skryptu działa tylko z klastrami HDInsight opartych na systemie Linux utworzone po 1 sierpnia 2016. Poprawki zostaną zastosowane tylko wtedy, gdy maszyny wirtualne są ponownie uruchamiane. 
>

## <a name="how-to-use-the-script"></a>Jak używać skryptu 

Jeśli za pomocą tego skryptu wymaga następujących informacji:
1. Lokalizacja skryptu: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv01/os-patching-reboot-config.sh.  HDInsight używa tego identyfikatora URI do znalezienia i uruchom skrypt na wszystkich maszynach wirtualnych w klastrze.
  
2. Typy węzłów klastra, zastosowane do skryptu: headnode, workernode, dozorcy. Ten skrypt należy zastosować do wszystkich typów węzła w klastrze. Jeśli nie ma zastosowania do typu węzła, następnie maszyn wirtualnych dla tego typu węzła będzie używać poprzedniej harmonogram stosowania poprawek.


3.  Parametr: Ten skrypt akceptuje trzy parametry liczbowe:

    | Parametr | Definicja |
    | --- | --- |
    | Włączanie/wyłączanie automatycznego ponownego uruchamiania |0 lub 1. Wartość 0 wyłącza automatyczne ponowne uruchomienie, gdy 1 umożliwia automatyczne ponowne uruchomienie. |
    | Częstotliwość |7 do 90 (włącznie). Liczba dni, aby zaczekać na ponowne uruchomienie maszyn wirtualnych do poprawki, które wymagają ponownego uruchomienia komputera. |
    | Dzień tygodnia |1-7 (włącznie). Wartość 1 oznacza ponownego uruchamiania powinny występować w poniedziałek i 7 wskazuje przykładzie Sunday.For przy użyciu parametrów 2 60 1 powoduje automatyczne ponowne uruchomienie co 60 dni (maksymalnie) wtorek. |
    | Trwałość |Podczas stosowania akcji skryptu do istniejącego klastra, można oznaczyć skrypt jako utrwalone. Utrwalonych skryptów są stosowane, gdy nowy workernodes zostaną dodane do klastra za pomocą operacji skalowania. |

> [!NOTE]
> Ten skrypt musi zostać oznaczone jako zachowywane w przypadku zastosowania do istniejącego klastra. W przeciwnym razie wszelkie nowe węzły został utworzony za pomocą operacji skalowania użyje domyślnego harmonogramu poprawki.
Jeśli stosujesz skrypt jako część procesu tworzenia klastra jest trwały automatycznie.
>

## <a name="next-steps"></a>Kolejne kroki

Do wykonania określonych kroków przy użyciu akcji skryptu, zobacz następujące sekcje w [klastrów usługi HDInsight opartej na Linuz dostosować za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md):

* [Za pomocą akcji skryptu, podczas tworzenia klastra](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Zastosowanie akcji skryptu do uruchomionej klastra](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
