---
title: Nie można usunąć sieci wirtualnej na platformie Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązać problem polegający na tym, że nie można usunąć sieci wirtualnej na platformie Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 2d427a8b40fcb537801ce76aae6bc32fcda3a307
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71056921"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Rozwiązywanie problemów: nie można usunąć sieci wirtualnej na platformie Azure

Podczas próby usunięcia sieci wirtualnej na platformie Microsoft Azure mogą pojawić się błędy. Ten artykuł zawiera kroki rozwiązywania problemów ułatwiające rozwiązanie tego problemu. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Wskazówki dotyczące rozwiązywania problemów 

1. [Sprawdź, czy brama sieci wirtualnej jest uruchomiona w sieci wirtualnej](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Sprawdź, czy brama aplikacji jest uruchomiona w sieci wirtualnej](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Sprawdź, czy usługa domenowa Active Directory azure jest włączona w sieci wirtualnej](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Sprawdź, czy sieć wirtualna jest połączona z innym zasobem](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Sprawdź, czy maszyna wirtualna jest nadal uruchomiona w sieci wirtualnej](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Sprawdź, czy sieć wirtualna utknęła w migracji](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Sprawdzanie, czy brama sieci wirtualnej jest uruchomiona w sieci wirtualnej

Aby usunąć sieć wirtualną, należy najpierw usunąć bramę sieci wirtualnej.

W przypadku klasycznych sieci wirtualnych przejdź do strony **Przegląd** klasycznej sieci wirtualnej w witrynie Azure portal. W sekcji **Połączenia sieci VPN,** jeśli brama jest uruchomiona w sieci wirtualnej, zostanie wyświetlony adres IP bramy. 

![Sprawdzanie, czy brama jest uruchomiona](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

W przypadku sieci wirtualnych przejdź do strony **Przegląd** sieci wirtualnej. Sprawdź, **czy podłączone urządzenia** nie mają bramy sieci wirtualnej.

![Sprawdź podłączone urządzenie](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Zanim usuniesz bramę, najpierw usuń wszystkie obiekty **Połączenia** w bramie. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Sprawdzanie, czy brama aplikacji jest uruchomiona w sieci wirtualnej

Przejdź do strony **Przegląd** sieci wirtualnej. Sprawdź, czy masz dostęp do bramy **aplikacji.**

![Sprawdź podłączone urządzenie](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Jeśli istnieje brama aplikacji, należy ją usunąć, zanim będzie można usunąć sieć wirtualną.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Sprawdzanie, czy usługa domenowa Active Directory platformy Azure jest włączona w sieci wirtualnej

Jeśli usługa domenowa Active Directory jest włączona i połączona z siecią wirtualną, nie można usunąć tej sieci wirtualnej. 

![Sprawdź podłączone urządzenie](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Aby wyłączyć usługę, zobacz [Wyłączanie usług domenowych Active Directory platformy Azure przy użyciu witryny Azure portal](../active-directory-domain-services/delete-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Sprawdzanie, czy sieć wirtualna jest połączona z innym zasobem

Sprawdź, czy łącza obwodów, połączenia i komunikacja równorzędna sieci wirtualnej. Każda z nich może spowodować niepowodzenie usuwania sieci wirtualnej. 

Zalecana kolejność usuwania jest następująca:

1. Połączenia bramy
2. Bramy
3. Ips
4. Komunikacja równorzędna sieci wirtualnej
5. Środowisko usługi aplikacji (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Sprawdzanie, czy maszyna wirtualna jest nadal uruchomiona w sieci wirtualnej

Upewnij się, że żadna maszyna wirtualna nie znajduje się w sieci wirtualnej.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Sprawdzanie, czy sieć wirtualna utknęła w migracji

Jeśli sieć wirtualna utknęła w stanie migracji, nie można jej usunąć. Uruchom następujące polecenie, aby przerwać migrację, a następnie usuń sieć wirtualną.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Następne kroki

- [Wirtualna sieć platformy Azure](virtual-networks-overview.md)
- [Często zadawane pytania dotyczące sieci wirtualnych platformy Azure](virtual-networks-faq.md)
