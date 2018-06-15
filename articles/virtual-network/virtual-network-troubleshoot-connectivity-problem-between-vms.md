---
title: Rozwiązywanie problemów z łącznością między maszynami wirtualnymi Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z łącznością między maszynami wirtualnymi Azure.
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
ms.openlocfilehash: 6decb0e9188db00608be35d9ba4e84df92ceb671
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072338"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Rozwiązywanie problemów z łącznością między maszynami wirtualnymi Azure

Mogą wystąpić problemy z łącznością między maszynami wirtualnymi Azure (maszyny wirtualne). Ten artykuł zawiera kroki rozwiązywania problemów, aby rozwiązać ten problem. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Objaw

Jednej maszyny Wirtualnej platformy Azure nie może połączyć się innej maszyny Wirtualnej platformy Azure.

## <a name="troubleshooting-guidance"></a>Wskazówki dotyczące rozwiązywania problemów 

1. [Sprawdź, czy karta sieciowa jest błędnie skonfigurowane](#step-1-check-whether-nic-is-misconfigured)
2. [Sprawdź, czy ruch sieciowy jest zablokowany przez NSG lub przez](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Sprawdź, czy ruch sieciowy jest blokowany przez zaporę maszyny Wirtualnej](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Sprawdź, czy maszyna wirtualna aplikacji lub usługi nasłuchuje na porcie](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Sprawdź, czy przyczyną problemu jest SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Sprawdź, czy ruch jest blokowany przez listy kontroli dostępu dla klasycznego maszyny Wirtualnej](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Sprawdź, czy punkt końcowy jest tworzony dla klasycznego maszyny Wirtualnej](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Spróbuj nawiązać połączenia z udziału sieciowego maszyny Wirtualnej](#step-8-try-to-connect-to-a-vm-network-share)
9. [Sprawdź łączność sieci wirtualnej między](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Wykonaj następujące kroki, aby rozwiązać problem. Po wykonaniu każdego kroku sprawdź, czy problem został rozwiązany. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Krok 1: Sprawdź, czy karta sieciowa jest błędnie skonfigurowane

Postępuj zgodnie z instrukcjami [sposób resetowania interfejsu sieciowego dla maszyny Wirtualnej systemu Windows Azure](../virtual-machines/windows/reset-network-interface.md). 

Jeśli ten problem występuje po zmodyfikowaniu interfejsu sieciowego (NIC), wykonaj następujące kroki:

**Maszyny wirtualne z wieloma kartami Sieciowymi**

1. Dodaj kartę sieciową.
2. Rozwiąż problemy w zły karty Sieciowej lub usunąć zły karty sieciowej.  Następnie ponownie Dodaj kartę Sieciową.

Aby uzyskać więcej informacji, zobacz [interfejsów sieciowych, aby dodać lub usunąć z maszyny wirtualnej](virtual-network-network-interface-vm.md).

**Wirtualna karta sieciowa na jednym** 

- [Wdrożenie maszyny Wirtualnej z systemem Windows](../virtual-machines/windows/redeploy-to-new-node.md)
- [Wdrożenie maszyny Wirtualnej systemu Linux](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Krok 2: Sprawdź, czy ruch sieciowy jest zablokowany przez NSG lub przez

Użyj [Sprawdź przepływ IP obserwatora sieciowego](../network-watcher/network-watcher-ip-flow-verify-overview.md) i [rejestrowania przepływu NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) ustalenie, czy istnieje grupa zabezpieczeń sieci (NSG) lub zdefiniowane przez użytkownika tras przez uniemożliwiać przepływ ruchu.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Krok 3: Sprawdź, czy ruch sieciowy jest blokowany przez zaporę maszyny Wirtualnej

Wyłącz zaporę, a następnie sprawdź wynik. Jeśli ten problem zostanie rozwiązany, sprawdź ustawienia zapory, a następnie ponownie włącz zaporę.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Krok 4: Sprawdź, czy maszyna wirtualna aplikacji lub usługi nasłuchuje na porcie

Aby sprawdzić, czy maszyna wirtualna aplikacji lub usługi nasłuchuje na porcie, można użyć jednej z następujących metod.

- Uruchom następujące polecenia, aby sprawdzić, czy serwer nasłuchuje na tym porcie.

**Maszyna wirtualna z systemem Windows**

    netstat –ano

**Maszyna wirtualna z systemem Linux**

    netstat -l

- Uruchom **telnet** na maszynie wirtualnej, aby przetestować portu. Jeśli test ma wynik negatywny, aplikacja lub usługa nie skonfigurowano nasłuchiwanie na tym porcie.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Krok 5: Sprawdź, czy przyczyną problemu jest SNAT

W niektórych scenariuszach maszyna wirtualna znajduje się za rozwiązania równoważenia obciążenia, które ma zależności zasobów poza platformą Azure. W tych scenariuszach występują sporadyczne problemy z połączeniem, problem może być spowodowane [wyczerpania portu SNAT](../load-balancer/load-balancer-outbound-connections.md). Aby rozwiązać ten problem, Utwórz dla każdej maszyny Wirtualnej związanej z modułem równoważenia obciążenia i zabezpieczyć za pomocą NSG lub listy ACL adresu VIP (lub ILPIP klasycznego). 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Krok 6: Sprawdź, czy ruch jest blokowany przez listy kontroli dostępu dla klasycznego maszyny Wirtualnej

Listy kontroli dostępu (ACL) zapewnia możliwość selektywnego akceptowanie lub odrzucanie ruchu dla punktu końcowego maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Zarządzanie listy ACL punktu końcowego](../virtual-machines/windows/classic/setup-endpoints.md#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Krok 7: Sprawdź, czy punkt końcowy jest tworzony dla klasycznego maszyny Wirtualnej

Wszystkie maszyny wirtualne utworzone na platformie Azure przy użyciu klasycznego modelu wdrażania można automatycznie komunikują się za pośrednictwem kanału między maszynami wirtualnymi w tej samej usługi w chmurze lub sieci wirtualnej sieci prywatnej. Jednak komputery w innych sieciach wirtualnych wymagają punktów końcowych, aby skierować ruch sieciowy przychodzący do maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [sposobu konfigurowania punktów końcowych](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Krok 8: Spróbuj połączyć się z udziału sieciowego maszyny Wirtualnej

Jeśli nie można połączyć z udziału sieciowego maszyny Wirtualnej, problem może być spowodowane niedostępny kart sieciowych w maszynie Wirtualnej. Aby usunąć niedostępny kart sieciowych, zobacz [sposób usuwania niedostępny kart sieciowych](../virtual-machines/windows/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Krok 9: Wyboru między-Vnet łączności

Użyj [Sprawdź przepływ IP obserwatora sieciowego](../network-watcher/network-watcher-ip-flow-verify-overview.md) i [rejestrowania przepływu NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) ustalenie, czy istnieje grupa NSG lub przez uniemożliwiać przepływ ruchu. Można również sprawdzić konfigurację sieci wirtualnej między [tutaj](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybkie rozwiązanie problemu.