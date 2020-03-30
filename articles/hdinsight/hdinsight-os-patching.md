---
title: Konfigurowanie harmonogramu poprawek systemu operacyjnego dla klastrów usługi Azure HDInsight
description: Dowiedz się, jak skonfigurować harmonogram poprawek systemu operacyjnego dla klastrów HDInsight opartych na systemie Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: f8e694f658d6e9de04c92001214ecd5c32ff7753
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206864"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Konfigurowanie harmonogramu poprawek systemu operacyjnego dla klastrów HDInsight opartych na systemie Linux

> [!IMPORTANT]
> Obrazy Ubuntu stają się dostępne dla nowego tworzenia klastra usługi Azure HDInsight w ciągu trzech miesięcy od opublikowania. Od stycznia 2019 r. uruchamianie klastrów nie jest automatycznie poprawiane. Klienci muszą używać akcji skryptu lub innych mechanizmów, aby załatać uruchomiony klaster. Nowo utworzone klastry zawsze będą miały najnowsze dostępne aktualizacje, w tym najnowsze poprawki zabezpieczeń.

Usługa HDInsight zapewnia obsługę typowych zadań w klastrze, takich jak instalowanie poprawek systemu operacyjnego, aktualizacji zabezpieczeń i ponowne uruchamianie węzłów. Te zadania są wykonywane przy użyciu następujących dwóch skryptów, które można uruchomić jako [akcje skryptu](hdinsight-hadoop-customize-cluster-linux.md)i skonfigurować z parametrami:

- `schedule-reboots.sh`- Wykonaj natychmiastowe ponowne uruchomienie lub zaplanuj ponowne uruchomienie w węzłach klastra.
- `install-updates-schedule-reboots.sh`- Zainstaluj wszystkie aktualizacje, tylko jądro + aktualizacje zabezpieczeń lub tylko aktualizacje jądra.

> [!NOTE]  
> Akcje skryptu nie będą automatycznie stosowane do aktualizacji dla wszystkich przyszłych cykli aktualizacji. Uruchom skrypty za każdym razem, gdy nowe aktualizacje muszą być stosowane do zainstalowania aktualizacji, a następnie uruchom ponownie maszynę wirtualną.

## <a name="preparation"></a>Przygotowywanie

Łatka w reprezentatywnym środowisku nieprodukcyjnym przed wdrożeniem w środowisku produkcyjnym. Opracowanie planu, aby odpowiednio przetestować system przed rzeczywistym poprawki.

Od czasu do czasu, z sesji ssh z klastra, może pojawić się komunikat, że uaktualnienie jest dostępne. Wiadomość może wyglądać mniej więcej tak:

```
New release '18.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade it
```

Łatanie jest opcjonalne i według własnego uznania.

## <a name="restart-nodes"></a>Ponowne uruchamianie węzłów
  
Harmonogram [skryptów ponownie uruchamia](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh)się ponownie, ustawia typ ponownego uruchomienia, który zostanie wykonany na komputerach w klastrze. Podczas przesyłania akcji skryptu należy ustawić ją tak, aby była stosowana we wszystkich trzech typach węzłów: węźle głównym, węźle procesu roboczego i zookeeperze. Jeśli skrypt nie jest stosowany do typu węzła, maszyny wirtualne dla tego typu węzła nie zostaną zaktualizowane lub ponownie uruchomione.

Akceptuje `schedule-reboots script` jeden parametr numeryczny:

| Parametr | Dopuszczalne wartości | Definicja |
| --- | --- | --- |
| Typ ponownego uruchomienia do wykonania | 1 lub 2 | Wartość 1 umożliwia ponowne uruchomienie harmonogramu (zaplanowane w ciągu 12-24 godzin). Wartość 2 umożliwia natychmiastowe ponowne uruchomienie (w 5 minut). Jeśli nie podano żadnego parametru, wartość domyślna to 1. |  

## <a name="install-updates-and-restart-nodes"></a>Instalowanie aktualizacji i ponowne uruchamianie węzłów

Skrypt [install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) udostępnia opcje instalowania różnych typów aktualizacji i ponownego uruchamiania maszyny Wirtualnej.

Skrypt `install-updates-schedule-reboots` akceptuje dwa parametry liczbowe, zgodnie z opisem w poniższej tabeli:

| Parametr | Dopuszczalne wartości | Definicja |
| --- | --- | --- |
| Typ aktualizacji do zainstalowania | 0, 1 lub 2 | Wartość 0 instaluje tylko aktualizacje jądra. Wartość 1 instaluje wszystkie aktualizacje, a 2 instaluje tylko jądro + aktualizacje zabezpieczeń. Jeśli nie podano żadnego parametru, wartość domyślna to 0. |
| Typ ponownego uruchomienia do wykonania | 0, 1 lub 2 | Wartość 0 wyłącza ponowne uruchomienie. Wartość 1 umożliwia ponowne uruchomienie harmonogramu, a 2 umożliwia natychmiastowe ponowne uruchomienie. Jeśli nie podano żadnego parametru, wartość domyślna to 0. Użytkownik musi zmienić parametr wejściowy 1 na parametr wejściowy 2. |

> [!NOTE]
> Skrypt należy oznaczyć jako utrwalony po zastosowaniu go do istniejącego klastra. W przeciwnym razie wszystkie nowe węzły utworzone za pomocą operacji skalowania użyją domyślnego harmonogramu poprawek. Jeśli zastosujesz skrypt jako część procesu tworzenia klastra, jest on utrwalony automatycznie.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe kroki dotyczące używania akcji skryptów, zobacz następujące sekcje w [sekcji Dostosowywanie klastrów HDInsight opartych na systemie Linux przy użyciu akcji skryptu:](hdinsight-hadoop-customize-cluster-linux.md)

- [Używanie akcji skryptu podczas tworzenia klastra](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [Stosowanie akcji skryptu do uruchomionego klastra](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
