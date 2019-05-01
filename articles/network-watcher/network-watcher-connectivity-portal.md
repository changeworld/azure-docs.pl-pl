---
title: Rozwiązywanie problemów z połączeniami w usłudze Azure Network Watcher — witryna Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z połączenia Rozwiązywanie problemów z możliwości usługi Azure Network Watcher w witrynie Azure portal.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: kumud
ms.openlocfilehash: 783bcd0cdc97328f16c4a0defa18daa46a065842
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702001"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Rozwiązywanie problemów z połączeniami w usłudze Azure Network Watcher w witrynie Azure portal

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [Program PowerShell](network-watcher-connectivity-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-connectivity-cli.md)
> - [Interfejs API REST platformy Azure](network-watcher-connectivity-rest.md)

Dowiedz się, jak używać połączenia Rozwiązywanie problemów, aby sprawdzić, czy można nawiązać bezpośrednie połączenie TCP z maszyny wirtualnej do danego punktu końcowego.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz następujące zasoby:

* Wystąpienie usługi Network Watcher w regionie, do których użytkownik chce Rozwiązywanie problemów z połączeniem.
* Rozwiązywanie problemów z połączeniami z maszyn wirtualnych.

> [!IMPORTANT]
> Rozwiązywanie problemów z połączeniami wymaga, że maszyna wirtualna, rozwiązywanie problemów z z ma `AzureNetworkWatcherExtension` zainstalowane rozszerzenie maszyny Wirtualnej. Instalowanie rozszerzenia na maszynie Wirtualnej Windows można znaleźć [Agent usługi Azure Network Watcher rozszerzenie maszyny wirtualnej dla Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i maszyny Wirtualnej systemu Linux można znaleźć pod adresem [Agent usługi Azure Network Watcher rozszerzenie maszyny wirtualnej dla systemu Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Rozszerzenie nie jest wymagany dla docelowego punktu końcowego.

## <a name="check-connectivity-to-a-virtual-machine"></a>Sprawdź łączność z maszyną wirtualną

W tym przykładzie służy do sprawdzania łączności do docelowej maszyny wirtualnej za pośrednictwem portu 80.

Przejdź do usługi Network Watcher i kliknij **Rozwiązywanie problemów z połączeniami**. Wybierz maszynę wirtualną, aby sprawdzić łączność z. W **docelowy** wybierz sekcję **wybierz maszynę wirtualną** i wybierz odpowiednią maszynę wirtualną oraz port do testowania.

Po kliknięciu **Sprawdź**, łączność między maszynami wirtualnymi na podany port jest zaznaczone. W tym przykładzie docelowej maszyny Wirtualnej jest niedostępny, przedstawiono listę przeskoków.

![Sprawdzanie łączności wyników dla maszyny wirtualnej][1]

## <a name="check-remote-endpoint-connectivity"></a>Sprawdź łączność zdalnego punktu końcowego

Aby sprawdzić łączność i opóźnienia w zdalnym punkcie końcowym, wybierz opcję **ręcznie określić** przycisku radiowego w **docelowy** sekcji, wprowadź adres url i numer portu i kliknij przycisk **Sprawdź**.  Służy to do zdalnego punktów końcowych, takich jak punkty końcowe usług websites i storage.

![Sprawdzanie łączności wyników dla witryny sieci web][2]

## <a name="next-steps"></a>Kolejne kroki

Informacje o automatyzowaniu przechwytywania pakietów przy użyciu alertów maszyny wirtualnej, wyświetlając [tworzenie przechwytywania pakietów wyzwolonych alertów](network-watcher-alert-triggered-packet-capture.md)

Dowiedz się, czy niektóre jest dozwolony ruch do lub z maszyny Wirtualnej, odwiedzając [weryfikowanie przepływu protokołu IP z Sprawdź](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png