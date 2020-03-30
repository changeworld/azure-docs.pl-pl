---
title: Rozwiązywanie problemów z łącznością między maszynami wirtualnymi platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązać problemy z łącznością między maszynami wirtualnymi platformy Azure.
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
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: ab3ae45081ecc481cb90af8961174e23c86e84b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71056817"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Rozwiązywanie problemów z łącznością między maszynami wirtualnymi platformy Azure

Mogą wystąpić problemy z łącznością między maszynami wirtualnymi platformy Azure (maszyny wirtualne). Ten artykuł zawiera kroki rozwiązywania problemów ułatwiające rozwiązanie tego problemu. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Objaw

Jedna maszyna wirtualna platformy Azure nie może połączyć się z inną maszyną wirtualną platformy Azure.

## <a name="troubleshooting-guidance"></a>Wskazówki dotyczące rozwiązywania problemów 

1. [Sprawdzanie, czy karta sieciowa jest nieprawidłowo skonfigurowana](#step-1-check-whether-nic-is-misconfigured)
2. [Sprawdzanie, czy ruch sieciowy jest blokowany przez nsg lub UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Sprawdzanie, czy ruch sieciowy jest blokowany przez zaporę maszyn wirtualnych](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Sprawdzanie, czy aplikacja lub usługa maszyny Wirtualnej nasłuchuje na porcie](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Sprawdź, czy problem jest spowodowany przez SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Sprawdzanie, czy ruch jest blokowany przez listy kontroli dostępu dla klasycznej maszyny Wirtualnej](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Sprawdzanie, czy punkt końcowy jest tworzony dla klasycznej maszyny Wirtualnej](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Spróbuj połączyć się z udziałem sieciowym maszyny Wirtualnej](#step-8-try-to-connect-to-a-vm-network-share)
9. [Sprawdź łączność Inter-Vnet](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Wykonaj następujące kroki, aby rozwiązać problem. Po wykonaniu każdego kroku sprawdź, czy problem został rozwiązany. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Krok 1: Sprawdź, czy karta sieciowa jest nieprawidłowo skonfigurowana

Wykonaj kroki opisane w [obszarze Jak zresetować interfejs sieciowy dla maszyny Wirtualnej systemu Windows platformy Azure](../virtual-machines/windows/reset-network-interface.md). 

Jeśli problem występuje po zmodyfikowaniu interfejsu sieciowego (NIC), wykonaj następujące kroki:

**Maszyny wirtualne z wieloma kartami sieciowymi**

1. Dodaj kartę sieciową.
2. Rozwiązać problemy w złej karty sieciowej lub usunąć złe KARTY SIECIOWE.  Następnie ponownie dodaj kartę sieciową.

Aby uzyskać więcej informacji, zobacz [Dodawanie interfejsów sieciowych do maszyn wirtualnych lub ich usuwanie](virtual-network-network-interface-vm.md).

**Maszyna wirtualna z jedną kartą sieciową** 

- [Ponowne wdrożenie maszyny Wirtualnej systemu Windows](../virtual-machines/windows/redeploy-to-new-node.md)
- [Ponowne wdrożenie maszyny Wirtualnej z systemem Linux](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Krok 2: Sprawdź, czy ruch sieciowy jest zablokowany przez nsg lub UDR

Użyj [weryfikacji przepływu IP obserwatora sieciowego](../network-watcher/network-watcher-ip-flow-verify-overview.md) i [rejestrowania przepływu sieciowej sieciowej,](../network-watcher/network-watcher-nsg-flow-logging-overview.md) aby ustalić, czy istnieje network security group (NSG) lub trasa zdefiniowana przez użytkownika (UDR), która zakłóca przepływ ruchu.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Krok 3: Sprawdzanie, czy ruch sieciowy jest blokowany przez zaporę maszyn wirtualnych

Wyłącz zaporę, a następnie przetestuj wynik. Jeśli problem został rozwiązany, sprawdź ustawienia zapory, a następnie ponownie włącz zaporę.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Krok 4: Sprawdź, czy aplikacja lub usługa maszyny Wirtualnej nasłuchuje na porcie

Można użyć jednej z następujących metod, aby sprawdzić, czy aplikacja maszyny Wirtualnej lub usługa nasłuchuje na porcie.

- Uruchom następujące polecenia, aby sprawdzić, czy serwer nasłuchuje na tym porcie.

**Maszyna wirtualna z systemem Windows**

    netstat –ano

**Maszyna wirtualna z systemem Linux**

    netstat -l

- Uruchom polecenie **telnet** na samej maszynie wirtualnej, aby przetestować port. Jeśli test zakończy się niepowodzeniem, aplikacja lub usługa nie jest skonfigurowana do nasłuchiwać na tym porcie.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Krok 5: Sprawdź, czy problem jest spowodowany przez SNAT

W niektórych scenariuszach maszyna wirtualna jest umieszczana za rozwiązaniem równoważenia obciążenia, które ma zależność od zasobów poza platformą Azure. W tych scenariuszach, jeśli występują sporadyczne problemy z połączeniem, problem może być spowodowany [wyczerpaniem portu SNAT](../load-balancer/load-balancer-outbound-connections.md). Aby rozwiązać ten problem, należy utworzyć adres VIP (lub ILPIP dla klasyków) dla każdej maszyny wirtualnej, która znajduje się za modułem równoważenia obciążenia i bezpieczne z NSG lub ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Krok 6: Sprawdź, czy ruch jest blokowany przez listy kontroli dostępu dla klasycznej maszyny Wirtualnej

Lista kontroli dostępu (ACL) zapewnia możliwość selektywnego zezwalania lub odrzucania ruchu dla punktu końcowego maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Zarządzanie acl w punkcie końcowym](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Krok 7: Sprawdź, czy punkt końcowy jest tworzony dla klasycznej maszyny Wirtualnej

Wszystkie maszyny wirtualne utworzone na platformie Azure przy użyciu klasycznego modelu wdrażania mogą automatycznie komunikować się za pośrednictwem kanału sieci prywatnej z innymi maszynami wirtualnymi w tej samej usłudze w chmurze lub w sieci wirtualnej. Jednak komputery w innych sieciach wirtualnych wymagają punktów końcowych do kierowania przychodzącego ruchu sieciowego do maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Jak skonfigurować punkty końcowe](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Krok 8: Spróbuj połączyć się z udziałem sieciowym maszyny Wirtualnej

Jeśli nie można połączyć się z udziałem sieciowym maszyny Wirtualnej, problem może być spowodowany przez niedostępne karty sieciowe w maszynie wirtualnej. Aby usunąć niedostępne karty sieciowe, zobacz [Jak usunąć niedostępne karty sieciowe](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Krok 9: Sprawdź łączność Inter-Vnet

Użyj [network watcher IP Flow Verify](../network-watcher/network-watcher-ip-flow-verify-overview.md) i [NSG Flow Logging,](../network-watcher/network-watcher-nsg-flow-logging-overview.md) aby ustalić, czy istnieje nsg lub UDR, który zakłóca przepływ ruchu. W tym miejscu można również sprawdzić [konfigurację](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections)inter-vnet.

### <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.