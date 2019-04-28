---
title: Nie można usunąć sieci wirtualnej na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać ten problem, w której nie można usunąć sieci wirtualnej na platformie Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 4bd143b37e4403d039108b4349b27604b6503e0e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123029"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Rozwiązywanie problemów: Nie można usunąć sieci wirtualnej na platformie Azure

Mogą występować błędy, podczas próby usunięcia sieci wirtualnej w systemie Microsoft Azure. Ten artykuł zawiera kroki rozwiązywania problemów, aby rozwiązać ten problem. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Wskazówki dotyczące rozwiązywania problemów 

1. [Sprawdź, czy brama sieci wirtualnej jest uruchomiony w sieci wirtualnej](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Sprawdź, czy bramę aplikacji działa w sieci wirtualnej](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Sprawdź, czy usługi domeny Azure Active Directory jest włączone w sieci wirtualnej](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Sprawdź, czy sieci wirtualnej jest podłączony do innego zasobu](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Sprawdź, czy maszyna wirtualna nadal działa w sieci wirtualnej](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Sprawdź, czy sieć wirtualna utkwiła w automatycznej migracji](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Sprawdź, czy brama sieci wirtualnej jest uruchomiony w sieci wirtualnej

Aby usunąć sieci wirtualnej, należy najpierw usunąć bramę sieci wirtualnej.

Dla klasycznych sieci wirtualnych, przejdź do **Przegląd** strony klasycznej sieci wirtualnej w witrynie Azure portal. W **połączeń sieci VPN** sekcji, jeśli brama jest uruchomiona w sieci wirtualnej, zostanie wyświetlony adres IP bramy. 

![Sprawdź, czy brama jest uruchomiona](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

Dla sieci wirtualnych, przejdź do **Przegląd** strony sieć wirtualna. Sprawdź **połączone urządzenia** dla bramy sieci wirtualnej.

![Sprawdź podłączone urządzenie](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Przed usunięciem bramy należy najpierw usunąć dowolne **połączenia** obiektów w bramie. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Sprawdź, czy bramę aplikacji działa w sieci wirtualnej

Przejdź do **Przegląd** strony sieć wirtualna. Sprawdź **połączone urządzenia** dla usługi application gateway.

![Sprawdź podłączone urządzenie](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

W przypadku bramy aplikacji, należy usunąć przed usunięciem sieci wirtualnej.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Sprawdź, czy usługi domeny Azure Active Directory jest włączone w sieci wirtualnej

Jeśli usług domenowych w usłudze Active Directory jest włączona i połączona z siecią wirtualną, nie można usunąć tej sieci wirtualnej. 

![Sprawdź podłączone urządzenie](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Aby wyłączyć usługę, zobacz [wyłączyć usługi Azure Active Directory Domain Services w witrynie Azure portal](../active-directory-domain-services/active-directory-ds-disable-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Sprawdź, czy sieci wirtualnej jest podłączony do innych zasobów

Sprawdź, czy linków obwodu, połączeń i komunikacja równorzędna sieci wirtualnych. Dowolny z nich może spowodować usuwania sieci wirtualnej nie powiedzie się. 

Kolejność usuwania zalecana jest następująca:

1. połączenia bramy
2. Bramy
3. Adresy IP
4. Komunikacja równorzędna sieci wirtualnych
5. Środowisko App Service Environment (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Sprawdź, czy maszyna wirtualna nadal działa w sieci wirtualnej

Upewnij się, że żadna maszyna wirtualna będzie w sieci wirtualnej.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Sprawdź, czy sieć wirtualna utkwiła w automatycznej migracji

Jeśli sieć wirtualna utkwiła w automatycznej migracji stanu, nie można usunąć. Uruchom następujące polecenie, aby przerwać migracji, a następnie usuń sieć wirtualną.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Kolejne kroki

- [Azure Virtual Network](virtual-networks-overview.md)
- [Często zadawane pytania dotyczące sieci wirtualnych platformy Azure](virtual-networks-faq.md)
