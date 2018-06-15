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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: d3ffcd9981ff0c57f6d625efcede3e930acd17b5
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072113"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Rozwiązywanie problemów: Nie można usunąć sieci wirtualnej na platformie Azure

Błędy może pojawić się podczas próby usunięcia sieci wirtualnej na platformie Microsoft Azure. Ten artykuł zawiera kroki rozwiązywania problemów, aby rozwiązać ten problem. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Wskazówki dotyczące rozwiązywania problemów 

1. [Sprawdź, czy brama sieci wirtualnej jest uruchomiony w sieci wirtualnej](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Sprawdź, czy bramę aplikacji jest uruchomiony w sieci wirtualnej](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Sprawdź, czy usługi Azure Active Directory domeny jest włączone w sieci wirtualnej](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Sprawdź, czy sieci wirtualnej jest podłączona do innego zasobu](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Sprawdź, czy maszyna wirtualna nadal działa w sieci wirtualnej](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Sprawdź, czy sieć wirtualna utkwiła w automatycznej migracji](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Sprawdź, czy brama sieci wirtualnej jest uruchomiony w sieci wirtualnej

Aby usunąć sieci wirtualnej, należy najpierw usunąć bramę sieci wirtualnej.

Aby klasycznych sieci wirtualnych, przejdź do tematu **omówienie** strony klasycznej sieci wirtualnej w portalu Azure. W **połączeń sieci VPN** sekcji, jeśli brama jest uruchomiona w sieci wirtualnej, zostanie wyświetlony adres IP bramy. 

![Sprawdź, czy brama jest uruchomiona](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

Dla sieci wirtualnych, przejdź do **omówienie** strony sieci wirtualnej. Sprawdź **urządzeń podłączonych** dla bramy sieci wirtualnej.

![Sprawdź podłączonym urządzeniu](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Zanim będzie można usunąć bramy, najpierw usuń wszystkie **połączenia** obiektów w bramie. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Sprawdź, czy w sieci wirtualnej jest uruchomiona bramy aplikacji

Przejdź do **omówienie** strony sieci wirtualnej. Sprawdź **urządzeń podłączonych** bramy aplikacji.

![Sprawdź podłączonym urządzeniu](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

W przypadku bramy aplikacji, należy go usunąć przed usunięciem sieci wirtualnej.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Sprawdź, czy usługi Azure Active Directory domeny jest włączone w sieci wirtualnej

Jeśli usług domenowych w usłudze Active Directory jest włączony i podłączony do sieci wirtualnej, nie można usunąć tej sieci wirtualnej. 

![Sprawdź podłączonym urządzeniu](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Aby wyłączyć usługę, zobacz [wyłączyć usługi Azure Active Directory Domain Services przy użyciu portalu Azure](../active-directory-domain-services/active-directory-ds-disable-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Sprawdź, czy sieci wirtualnej jest podłączona do innego zasobu

Sprawdź, czy łącza obwód, połączeń i komunikacji równorzędnych sieci wirtualnych. Żadnego z tych adresów może spowodować niepowodzenie usunięcie sieci wirtualnej. 

Kolejność usuwania zalecana jest następująca:

1. Połączenia bramy
2. Bramy
3. Adresy IP
4. Komunikacji równorzędnych sieci wirtualnych
5. Środowisko usługi aplikacji (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Sprawdź, czy maszyna wirtualna nadal działa w sieci wirtualnej

Upewnij się, że żadna maszyna wirtualna będzie w sieci wirtualnej.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Sprawdź, czy utkwiła w automatycznej migracji sieci wirtualnej

Jeśli sieć wirtualna jest zablokowana w stanie migracji, nie można usunąć. Uruchom następujące polecenie, aby przerwać migracji, a następnie usunąć sieci wirtualnej.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Kolejne kroki

- [Azure Virtual Network](virtual-networks-overview.md)
- [Często zadawane pytania dotyczące sieci wirtualnych platformy Azure](virtual-networks-faq.md)
