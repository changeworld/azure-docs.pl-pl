---
title: Monitorowanie połączenia sieciowe z obserwatora sieciowego Azure - Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować łączność sieciową z obserwatora sieciowego Azure przy użyciu portalu Azure.
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
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: 242da9a3ce52d9c7d801215cde7b72b7f8fe9a91
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Połączenia sieciowe monitora obserwatora sieciowego Azure przy użyciu portalu Azure

Dowiedz się, jak monitorować łączność sieciową między maszyn wirtualnych Azure (VM) i adres IP przy użyciu monitora połączenia. Monitor połączenia zapewnia monitorowanie między źródłowego i docelowego adresu IP i portu. Monitor połączenia umożliwia realizację scenariuszy, takich jak monitorowanie łączności z maszyny Wirtualnej w sieci wirtualnej maszyny Wirtualnej działa program SQL server w tej samej lub innej sieci wirtualnej, za pośrednictwem portu 1433. Monitor połączenia udostępnia połączenia czas oczekiwania, jako metrykę Azure Monitor rejestrowane co 60 sekund. On również umożliwia topologii przeskoku przeskoku i identyfikuje problemy konfiguracji wpływające na połączenie.

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków w tym artykule, musi spełniać następujące wymagania wstępne:

* Wystąpienia obserwatora sieciowego w regionie, który chcesz monitorować połączenia. Jeśli nie masz jeszcze jeden, możesz utworzyć jedną, wykonując kroki opisane w [utworzenia wystąpienia obserwatora sieciowego Azure](network-watcher-create.md).
* Maszyny Wirtualnej, aby monitorować z. Aby dowiedzieć się, jak utworzyć Maszynę wirtualną, zobacz Tworzenie [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) lub [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) maszyny Wirtualnej.
* Ma `AzureNetworkWatcherExtension` zainstalowana na maszynie wirtualnej, który chcesz monitorować połączenia z. Aby zainstalować rozszerzenie na maszynie wirtualnej systemu Windows, zobacz [rozszerzenie maszyny wirtualnej Azure sieci obserwatorów agenta dla systemu Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i zainstalować rozszerzenie Zobacz maszyny Wirtualnej systemu Linux [rozszerzenie maszyny wirtualnej Azure sieci obserwatorów agenta Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Rozszerzenie nie jest wymagane na docelowy punkt końcowy, który chcesz monitorować.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się w [Portalu Azure](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Utwórz monitor połączenia

Poniższych kroków spowoduje włączenie monitorowania połączenia w docelowej lokalizacji maszyny Wirtualnej przez porty 80 i 1433:

1. Po lewej stronie portalu, wybierz **wszystkie usługi**.
2. Zacznij pisać *obserwatora sieciowego* w **filtru** pole. Gdy **obserwatora sieciowego** pojawia się w wynikach wyszukiwania, wybierz go.
3. W obszarze **monitorowanie**, wybierz pozycję **monitor połączenia**.
4. Wybierz pozycję **+ Dodaj**.
5. Wprowadź lub wybierz informacje dotyczące połączenia, chcesz monitorować, a następnie wybierz **Dodaj**. W tym przykładzie przedstawiono na rysunku poniżej połączenie monitorowane jest z *MultiTierApp0* maszyny Wirtualnej, aby *Database0* maszyny Wirtualnej za pośrednictwem portu 80:

    ![Dodawanie połączenia monitora](./media/connection-monitor/add-connection-monitor.png)

    Rozpoczyna monitorowanie. Monitor połączenia sondy co 60 sekund.
6. Wykonaj krok 5 ponownie, określając takie same źródłowych i docelowych maszyn wirtualnych i następujące wartości:
    
    |Ustawienie  |Wartość          |
    |---------|---------      |
    |Name (Nazwa)     | AppToDB(1433) |
    |Port     | 1433          |

## <a name="view-connection-monitoring"></a>Monitorowanie połączenia widoku

1. Wykonać kroki od 1 do 3 w [utworzyć monitor połączenia](#create-a-connection-monitor) Aby wyświetlić połączenia monitorowania.
2. Na poniższej ilustracji przedstawiono szczegóły *AppToDB(80)* połączenia. **Stan** jest dostępny. **Widoku wykresu** pokazuje **Średni czas obiegu** i **sondy nie powiodła się %**. Wykres przeskoku przeskoku informacje i pokazuje, że problemów wpływają na uzyskiwanie docelowego.

    ![Widok wykresu](./media/connection-monitor/view-graph.png)

3. Wyświetlanie *AppToDB(1433)* połączenia pokazano na poniższej ilustracji widać, że dla tego samego źródłowego i docelowego maszyn wirtualnych, stan jest nieosiągalny za pośrednictwem portu 1433. **Widoku siatki** w tym scenariuszu zawiera informacje przeskoku przeskoku i wpływu na uzyskiwanie problem. W takim przypadku reguły NSG blokuje cały ruch na porcie 1433 na drugi przeskok.

    ![Widok siatki](./media/connection-monitor/view-grid.png)

## <a name="next-steps"></a>Kolejne kroki

- Informacje o sposobie przechwytywania pakietów z alertami maszyny Wirtualnej przez automatyzację [tworzenie przechwytywania pakietów wyzwolony alert](network-watcher-alert-triggered-packet-capture.md).
- Ustalić, czy niektóre ruch jest dozwolony w lub z maszyny Wirtualnej za pomocą [Sprawdź przepływ IP](diagnose-vm-network-traffic-filtering-problem.md).