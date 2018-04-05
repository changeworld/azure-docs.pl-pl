---
title: Zarządzanie dziennikami przepływu grupy zabezpieczeń sieci z obserwatora sieciowego Azure | Dokumentacja firmy Microsoft
description: Ta strona opisano sposób zarządzania dziennikami przepływu sieciowej grupy zabezpieczeń w obserwatora sieciowego Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: cb41781c5ac8fb759cecea01402c08dd716bf7d7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Zarządzanie dziennikami przepływu grupy zabezpieczeń sieci w portalu Azure

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [Program PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [Interfejs API REST](network-watcher-nsg-flow-logging-rest.md)

Dzienniki przepływu grupy zabezpieczeń sieci są funkcją obserwatora sieciowego, który umożliwia wyświetlenie informacji o przychodzące i wychodzące ruchu IP za pośrednictwem grupy zabezpieczeń sieci. Te dzienniki przepływu są zapisywane w formacie JSON i zawierają istotne informacje, w tym: 

- Wychodzące i przychodzące przepływem na podstawie reguł.
- Karta sieciowa, która dotyczy przepływu.
- 5-elementowej informacje o przepływie (źródłowego i docelowego adresu IP, portu źródłowego i docelowego, protokół).
- Informacja, czy dozwolony lub odrzucany ruchu.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym artykule, trzeba już mieć następujące zasoby:

- Istniejące obserwatora sieciowego. Aby utworzyć obserwatora sieciowego, zobacz [utworzyć wystąpienia obserwatora sieciowego](network-watcher-create.md).
- Istniejącej grupy zasobów z prawidłową maszyną wirtualną. Jeśli nie masz maszyny wirtualnej, zobacz Tworzenie [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) lub [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) maszyny wirtualnej.

## <a name="register-insights-provider"></a>Zarejestruj dostawcę usługi Insights

Przepływ rejestrowanie działało poprawnie **elemencie Microsoft.Insights** dostawcy musi być zarejestrowana. Aby zarejestrować dostawcę, należy wykonać następujące czynności: 

1. Przejdź do **subskrypcje**, a następnie wybierz subskrypcję, dla której chcesz włączyć dzienniki przepływu. 
2. Na **subskrypcji** bloku, wybierz opcję **dostawców zasobów**. 
3. Spójrz na listę dostawców i upewnij się, że **elemencie microsoft.insights** dostawca został zarejestrowany. Jeśli nie, następnie wybierz **zarejestrować**.

![Widok dostawców][providers]

## <a name="enable-flow-logs"></a>Włączanie dzienników przepływu

Następujące kroki umożliwiają włączenie przepływu dzienniki w lokacji sieciowej grupy zabezpieczeń.

### <a name="step-1"></a>Krok 1

Przejdź do wystąpienia obserwatora sieciowego, a następnie wybierz **przepływ NSG rejestruje**.

![Przegląd dzienników przepływu][1]

### <a name="step-2"></a>Krok 2

Wybierz grupy zabezpieczeń sieci z listy.

![Przegląd dzienników przepływu][2]

### <a name="step-3"></a>Krok 3 

Na **ustawień dzienników przepływu** bloku, Ustaw stan na **na**, a następnie skonfiguruj konto magazynu. Wybierz istniejące konto magazynu, który ma **wszystkich sieci** (ustawienie domyślne) wybrany w obszarze **zapory i sieci wirtualne**w obszarze **ustawienia** dla konta magazynu. Po wybraniu konta magazynu, wybierz **OK**, a następnie wybierz **zapisać**.

![Przegląd dzienników przepływu][3]

## <a name="download-flow-logs"></a>Pobierz dzienniki przepływu

Przepływ dzienniki są zapisywane na koncie magazynu. Pobierz dzienniki przepływu, aby je wyświetlić.

### <a name="step-1"></a>Krok 1

Aby pobrać dzienniki przepływu, wybierz **dzienniki przepływu można pobrać z kont magazynu skonfigurowanych**. Ten krok przejście do widoku konto magazynu, na którym można wybrać dzienniki, które można pobrać.

![Ustawienia dzienników przepływów][4]

### <a name="step-2"></a>Krok 2

Przejdź do konta magazynu poprawne. Następnie wybierz **kontenery** > **insights — dziennik networksecuritygroupflowevent**.

![Ustawienia dzienników przepływów][5]

### <a name="step-3"></a>Krok 3

Przejdź do lokalizacji pliku dziennika przepływu, zaznacz go, a następnie wybierz **Pobierz**.

![Ustawienia dzienników przepływów][6]

Informacje o strukturze dziennika, odwiedź stronę [Omówienie dziennika przepływu grupy zabezpieczeń sieci](network-watcher-nsg-flow-logging-overview.md).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [wizualizacji dzienników przepływu grupy NSG z usługą Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
[providers]: ./media/network-watcher-nsg-flow-logging-portal/providers.png
