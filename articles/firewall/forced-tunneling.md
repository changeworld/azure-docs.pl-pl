---
title: Tunelowanie wymuszone zaporą platformy Azure
description: Można skonfigurować wymuszone tunelowanie, aby kierować ruch związany z Internetem do dodatkowej zapory lub sieciowego urządzenia wirtualnego w celu dalszego przetwarzania.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/24/2020
ms.author: victorh
ms.openlocfilehash: e51f6de370a5340082f64a0ca15c61583f75962b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597284"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Tunelowanie wymuszone za pomocą zapory azure (wersja zapoznawcza)

Zaporę platformy Azure można skonfigurować tak, aby kierować cały ruch związany z Internetem do wyznaczonego następnego przeskoku, zamiast przechodzić bezpośrednio do Internetu. Na przykład może być lokalna zapora brzegowa lub inne wirtualne urządzenie sieciowe (NVA) do przetwarzania ruchu sieciowego, zanim zostanie przekazany do Internetu.

> [!IMPORTANT]
> Tunelowanie wymuszone za pomocą zapory azure jest obecnie w publicznej wersji zapoznawczej.
>
> Publiczna wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie należy korzystać z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Domyślnie wymuszone tunelowanie nie jest dozwolone w zaporze platformy Azure, aby upewnić się, że wszystkie jego wychodzące zależności platformy Azure są spełnione. Konfiguracje routsy zdefiniowanej przez użytkownika (UDR) w *sieci AzureFirewallSubnet,* które mają domyślną trasę, która nie będzie bezpośrednio do Internetu, są wyłączone.

## <a name="forced-tunneling-configuration"></a>Wymuszona konfiguracja tunelowania

Aby obsługiwać wymuszone tunelowanie, ruch zarządzania usługami jest oddzielony od ruchu klientów. Dodatkowa dedykowana podsieć o nazwie *AzureFirewallManagementSubnet* (minimalny rozmiar podsieci /26) jest wymagana z własnym skojarzonym publicznym adresem IP. Jedyną dozwoloną trasą w tej podsieci jest domyślna trasa do Internetu, a propagacja trasy BGP musi być wyłączona.

Jeśli masz domyślną trasę anonsowaną za pośrednictwem protokołu BGP, aby wymusić ruch do lokalnego, musisz utworzyć *AzureFirewallSubnet* i *AzureFirewallManagementSubnet* przed wdrożeniem zapory i mieć UDR z domyślną trasą do Internetu i **propagacji trasy bramy sieci wirtualnej** wyłączone.

W ramach tej konfiguracji *AzureFirewallSubnet* może teraz zawierać trasy do dowolnej zapory lokalnej lub urządzenia WUS do przetwarzania ruchu, zanim zostanie przekazany do Internetu. Można również opublikować te trasy za pośrednictwem protokołu BGP do *usługi AzureFirewallSubnet,* jeśli **propagacja trasy bramy sieci wirtualnej** jest włączona w tej podsieci.

Na przykład można utworzyć trasę domyślną w *usłudze AzureFirewallSubnet* z bramą sieci VPN jako następny przeskok, aby uzyskać dostęp do urządzenia lokalnego. Można też włączyć **propagację trasy bramy sieci wirtualnej,** aby uzyskać odpowiednie trasy do sieci lokalnej.

![Propagacja trasy bramy sieci wirtualnej](media/forced-tunneling/route-propagation.png)

Po skonfigurowaniu zapory platformy Azure do obsługi wymuszonego tunelowania nie można cofnąć konfiguracji. Jeśli usuniesz wszystkie inne konfiguracje IP na zaporze, konfiguracja ip zarządzania zostanie również usunięta, a zapora zostanie cofnięta. Nie można usunąć publicznego adresu IP przypisanego do konfiguracji adresów IP zarządzania, ale można przypisać inny publiczny adres IP.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Wdrażanie i konfigurowanie Zapory platformy Azure w sieci hybrydowej przy użyciu witryny Azure portal](tutorial-hybrid-portal.md)