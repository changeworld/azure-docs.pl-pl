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
ms.openlocfilehash: a97a03f7ef20ae56cec04341fe76b79ee657547b
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748476"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Konfigurowanie harmonogramu poprawek systemu operacyjnego dla klastrów usługi HDInsight opartych na systemie Linux

> [!IMPORTANT]
> Obrazy Ubuntu są dostępne dla nowego klastra usługi Azure HDInsight w ciągu trzech miesięcy od opublikowania. Począwszy od stycznia 2019, uruchomione klastry nie są stosowane do autopoprawek. Aby zastosować poprawki do działającego klastra, klienci muszą używać akcji skryptu lub innych mechanizmów. Nowo utworzone klastry będą zawsze miały najnowsze dostępne aktualizacje, w tym najnowsze poprawki zabezpieczeń.

Usługa HDInsight zapewnia obsługę typowych zadań w klastrze, takich jak instalowanie poprawek systemu operacyjnego, aktualizacji zabezpieczeń i węzłów ponownego uruchamiania. Te zadania są wykonywane przy użyciu następujących dwóch skryptów, które mogą być uruchamiane jako [Akcje skryptu](hdinsight-hadoop-customize-cluster-linux.md)i skonfigurowane z parametrami:

- `schedule-reboots.sh` — wykonaj natychmiastowe ponowne uruchomienie lub Zaplanuj ponowne uruchomienie w węzłach klastra.
- `install-updates-schedule-reboots.sh` — Zainstaluj wszystkie aktualizacje, tylko aktualizacje jądra + zabezpieczenia lub tylko aktualizacje jądra.

> [!NOTE]  
> Akcje skryptu nie będą automatycznie stosowały aktualizacji dla wszystkich przyszłych cykli aktualizacji. Uruchom skrypty za każdym razem, gdy trzeba zastosować nowe aktualizacje, aby zainstalować aktualizacje, a następnie uruchom ponownie maszynę wirtualną.

## <a name="restart-nodes"></a>Uruchom ponownie węzły
  
Harmonogram skryptów [—](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh)ponowny rozruch, ustawia typ ponownego uruchomienia, który będzie wykonywany na maszynach w klastrze. Podczas przesyłania akcji skryptu ustaw jej wartość na wszystkie trzy typy węzłów: węzeł główny, węzeł procesu roboczego i dozorcy. Jeśli skrypt nie zostanie zastosowany do typu węzła, maszyny wirtualne dla tego typu węzła nie zostaną zaktualizowane ani uruchomione ponownie.

`schedule-reboots script` akceptuje jeden parametr liczbowy:

| Parametr | Akceptowane wartości | Definicja |
| --- | --- | --- |
| Typ ponownego uruchomienia do wykonania | 1 lub 2 | Wartość 1 włącza zaplanowanie ponownego uruchomienia (zaplanowane w 12-24 godzinach). Wartość 2 włącza natychmiastowe ponowne uruchomienie (w ciągu 5 minut). Jeśli parametr nie zostanie określony, wartość domyślna to 1. |  

## <a name="install-updates-and-restart-nodes"></a>Instalowanie aktualizacji i ponowne uruchamianie węzłów

Skrypt [Install-Updates-Schedule-Reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) zawiera opcje instalowania różnych typów aktualizacji i ponownego uruchamiania maszyny wirtualnej.

Skrypt `install-updates-schedule-reboots` akceptuje dwa parametry liczbowe, zgodnie z opisem w poniższej tabeli:

| Parametr | Akceptowane wartości | Definicja |
| --- | --- | --- |
| Typ aktualizacji do zainstalowania | 0, 1 lub 2 | Wartość 0 powoduje zainstalowanie tylko aktualizacji jądra. Wartość 1 powoduje zainstalowanie wszystkich aktualizacji, a 2 instaluje tylko aktualizacje jądra i zabezpieczeń. Jeśli parametr nie zostanie podany, wartość domyślna to 0. |
| Typ ponownego uruchomienia do wykonania | 0, 1 lub 2 | Wartość 0 powoduje wyłączenie ponownego uruchomienia. Wartość 1 włącza ponowne uruchomienie harmonogramu, a 2 włącza natychmiastowe ponowne uruchomienie. Jeśli parametr nie zostanie podany, wartość domyślna to 0. Użytkownik musi zmienić parametr wejściowy 1 na parametr wejściowy 2. |

> [!NOTE]
> Skrypt należy oznaczyć jako utrwalony po zastosowaniu go w istniejącym klastrze. W przeciwnym razie wszystkie nowe węzły utworzone za pomocą operacji skalowania będą używały domyślnego harmonogramu poprawek. Jeśli zastosujesz skrypt w ramach procesu tworzenia klastra, zostanie on utrwalony automatycznie.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z konkretnymi krokami dotyczącymi używania akcji skryptu, zobacz następujące sekcje w temacie [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md):

* [Używanie akcji skryptu podczas tworzenia klastra](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Zastosuj akcję skryptu do działającego klastra](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
