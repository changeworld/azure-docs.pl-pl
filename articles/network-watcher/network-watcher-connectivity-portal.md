---
title: Rozwiązywanie problemów z połączeniami z obserwatora sieciowego Azure - Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z połączenia Rozwiązywanie problemów z możliwości obserwatora sieciowego Azure przy użyciu portalu Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: jdial
ms.openlocfilehash: 55c8e6763b0c3d8bd3884a44695c25e62b8d9900
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Rozwiązywanie problemów z połączeniami z obserwatora sieciowego Azure przy użyciu portalu Azure

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [Program PowerShell](network-watcher-connectivity-powershell.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-connectivity-cli.md)
> - [Interfejs API Azure REST](network-watcher-connectivity-rest.md)

Dowiedz się, jak używać połączenia Rozwiązywanie problemów, aby sprawdzić, czy można nawiązać bezpośrednie połączenie TCP z maszyny wirtualnej do danego punktu końcowego.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz następujące zasoby:

* Wystąpienie obserwatora sieciowego w regionie rozwiązywania problemów z połączeniem.
* Rozwiązywanie problemów z połączeniami z maszyn wirtualnych.

> [!IMPORTANT]
> Rozwiązywanie problemów z połączenia wymaga, aby rozwiązać z maszyny Wirtualnej miało `AzureNetworkWatcherExtension` zainstalowane rozszerzenia maszyny Wirtualnej. Instalowanie rozszerzenia na maszynie Wirtualnej systemu Windows można znaleźć [rozszerzenie maszyny wirtualnej Azure sieci obserwatorów agenta dla systemu Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i dla maszyny Wirtualnej systemu Linux, odwiedź [rozszerzenie maszyny wirtualnej Azure sieci obserwatorów agenta dla systemu Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Rozszerzenie nie jest wymagany dla docelowego punktu końcowego.

## <a name="check-connectivity-to-a-virtual-machine"></a>Sprawdź połączenie z maszyną wirtualną

W tym przykładzie służy do sprawdzania łączności do docelowej maszyny wirtualnej za pośrednictwem portu 80.

Przejdź do Twojej obserwatora sieciowego i kliknij **Rozwiązywanie problemów z połączenia**. Wybierz maszynę wirtualną, aby sprawdzić łączność z. W **docelowego** wybierz **wybierz maszynę wirtualną** oraz wybrać poprawne maszyny wirtualnej i portu do testowania.

Po kliknięciu **Sprawdź**, łączności między maszynami wirtualnymi na określony port jest zaznaczony. W tym przykładzie docelowej maszyny Wirtualnej jest nieosiągalny, lista przeskoków są wyświetlane.

![Wyniki sprawdzania łączności dla maszyny wirtualnej][1]

## <a name="check-remote-endpoint-connectivity"></a>Sprawdź łączność zdalny punkt końcowy

Aby sprawdzić łączność i czas oczekiwania do zdalnego punktu końcowego, wybierz **ręcznie określ** przycisku radiowego w **docelowego** sekcji należy wprowadzić adres url i numer portu i kliknij przycisk **Sprawdź**.  Służy to do zdalnego punktów końcowych, takie jak punktów końcowych witryn sieci Web i magazynu.

![Wyniki sprawdzania łączności dla witryny sieci web][2]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak można zautomatyzować przechwytywania pakietów z alertami maszyny wirtualnej, wyświetlając [utworzyć przechwytywania alertów wyzwalanych pakietów](network-watcher-alert-triggered-packet-capture.md)

Znajdź, jeśli niektórych ruch jest dozwolony w lub z maszyny Wirtualnej, odwiedzając [Sprawdź przepływ Sprawdź IP](network-watcher-check-ip-flow-verify-portal.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png