---
title: Rozwiązywanie problemów z połączeniami — witryna Azure portal
titleSuffix: Azure Network Watcher
description: Dowiedz się, jak korzystać z możliwości rozwiązywania problemów z połączeniem usługi Azure Network Watcher przy użyciu witryny Azure portal.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: damendo
ms.openlocfilehash: e405a91b1ea541b4ed3328fdb3bf80ca82731c93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283240"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Rozwiązywanie problemów z połączeniami za pomocą usługi Azure Network Watcher przy użyciu portalu Azure

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [Powershell](network-watcher-connectivity-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-connectivity-cli.md)
> - [Interfejs API REST platformy Azure](network-watcher-connectivity-rest.md)

Dowiedz się, jak użyć rozwiązywania problemów z połączeniem, aby sprawdzić, czy można ustanowić bezpośrednie połączenie TCP z maszyny wirtualnej do danego punktu końcowego.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz następujące zasoby:

* Wystąpienie Obserwatora sieciowego w regionie, w którego chcesz rozwiązać problem z połączeniem.
* Maszyny wirtualne do rozwiązywania problemów z połączeniami.

> [!IMPORTANT]
> Rozwiązywanie problemów z połączeniem wymaga, `AzureNetworkWatcherExtension` aby maszyna wirtualna, z której można rozwiązać problem, ma zainstalowane rozszerzenie maszyny Wirtualnej. Aby zainstalować rozszerzenie na maszynie Wirtualnej systemu Windows odwiedź [rozszerzenie maszyny wirtualnej usługi Azure Network Watcher Agent dla systemu Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i maszyny wirtualnej z systemem Linux, odwiedź rozszerzenie maszyny [wirtualnej usługi Azure Network Watcher Agent dla systemu Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Rozszerzenie nie jest wymagane w docelowym punkcie końcowym.

## <a name="check-connectivity-to-a-virtual-machine"></a>Sprawdzanie łączności z maszyną wirtualną

W tym przykładzie sprawdza łączność z docelową maszyną wirtualną za porcie 80.

Przejdź do obserwatora sieciowego i kliknij pozycję **Rozwiązywanie problemów z połączeniem**. Wybierz maszynę wirtualną, z na podstawie tej aby sprawdzić łączność. W sekcji **Miejsce docelowe** wybierz **pozycję Wybierz maszynę wirtualną** i wybierz właściwą maszynę wirtualną i port do przetestowania.

Po kliknięciu przycisku **Sprawdź**łączność między maszynami wirtualnymi na określonym porcie jest sprawdzana. W tym przykładzie docelowa maszyna wirtualna jest nieosiągalna, wyświetlana jest lista przeskoków.

![Sprawdzanie wyników łączności dla maszyny wirtualnej][1]

## <a name="check-remote-endpoint-connectivity"></a>Sprawdzanie łączności zdalnego punktu końcowego

Aby sprawdzić łączność i opóźnienie ze zdalnym punktem końcowym, wybierz przycisk opcji **Określ ręcznie** w sekcji **Miejsce docelowe,** wprowadź adres URL i port, a następnie kliknij przycisk **Sprawdź**.  Jest to używane dla zdalnych punktów końcowych, takich jak witryny sieci Web i punkty końcowe magazynu.

![Sprawdzanie wyników łączności dla witryny sieci Web][2]

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zautomatyzować przechwytywanie pakietów za pomocą alertów maszyn wirtualnych, przeglądając [Tworzenie wyzwalanego alertu przechwytywania pakietów](network-watcher-alert-triggered-packet-capture.md)

Sprawdzanie, czy określony ruch jest dozwolony w maszynie wirtualnej lub poza niej, odwiedzając weryfikację [przepływu adresu IP](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png