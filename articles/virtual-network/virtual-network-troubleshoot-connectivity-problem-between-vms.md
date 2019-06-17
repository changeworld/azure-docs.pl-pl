---
title: Rozwiązywanie problemów z łącznością między maszynami wirtualnymi platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z łącznością między maszynami wirtualnymi platformy Azure.
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
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: fc3d6ab1d7fdf05963d9ecd350deccd940a95b87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61036392"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Rozwiązywanie problemów z łącznością między maszynami wirtualnymi platformy Azure

Mogą wystąpić problemy z łącznością między maszynami wirtualnymi platformy Azure (maszyny wirtualne). Ten artykuł zawiera kroki rozwiązywania problemów, aby rozwiązać ten problem. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Objaw

Jednej maszyny Wirtualnej platformy Azure nie można połączyć z inną maszyną wirtualną platformy Azure.

## <a name="troubleshooting-guidance"></a>Wskazówki dotyczące rozwiązywania problemów 

1. [Sprawdź, czy karta sieciowa jest błędnie skonfigurowane](#step-1-check-whether-nic-is-misconfigured)
2. [Sprawdź, czy ruch sieciowy jest blokowany przez sieciową grupę zabezpieczeń lub Routing zdefiniowany przez użytkownika](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Sprawdź, czy ruch sieciowy jest blokowany przez zapory maszyny Wirtualnej](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Sprawdź, czy maszyna wirtualna aplikacji lub usługi nasłuchuje na porcie](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Sprawdź, czy problem jest spowodowany SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Sprawdź, czy ruch jest blokowany przez listy ACL dla klasycznej maszyny Wirtualnej](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Sprawdź, czy punkt końcowy jest tworzony dla klasycznej maszyny Wirtualnej](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Spróbuj połączyć się z udziałem sieciowym maszyny Wirtualnej](#step-8-try-to-connect-to-a-vm-network-share)
9. [Sprawdź łączność między sieciami wirtualnymi](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Wykonaj następujące kroki, aby rozwiązać problem. Po wykonaniu każdego kroku, sprawdź, czy problem został rozwiązany. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Krok 1: Sprawdź, czy karta sieciowa jest błędnie skonfigurowane

Postępuj zgodnie z instrukcjami w [jak zresetować interfejsu sieciowego maszyny wirtualnej platformy Azure Windows](../virtual-machines/windows/reset-network-interface.md). 

Jeśli ten problem występuje po zmodyfikowaniu interfejsu sieciowego (NIC), wykonaj następujące czynności:

**Maszyny wirtualne z wieloma kartami**

1. Dodawanie karty sieciowej.
2. Napraw problemy w zły karty Sieciowej lub usunąć zły karty sieciowej.  Następnie ponownie Dodaj kartę Sieciową.

Aby uzyskać więcej informacji, zobacz [interfejsów sieciowych, aby dodać lub usunąć z maszyn wirtualnych](virtual-network-network-interface-vm.md).

**Maszyna wirtualna karta sieciowa na jednym** 

- [Ponowne wdrażanie maszyny Wirtualnej Windows](../virtual-machines/windows/redeploy-to-new-node.md)
- [Ponowne wdrażanie maszyny Wirtualnej systemu Linux](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Krok 2: Sprawdź, czy ruch sieciowy jest blokowany przez sieciową grupę zabezpieczeń lub Routing zdefiniowany przez użytkownika

Użyj [Weryfikacja przepływu dla adresu IP sieci obserwatora](../network-watcher/network-watcher-ip-flow-verify-overview.md) i [rejestrowanie przepływu sieciowych grup zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-overview.md) do określenia, czy istnieje grupa zabezpieczeń sieci (NSG) lub zdefiniowane przez użytkownika trasy Nauczone uniemożliwiać przepływu ruchu.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Krok 3: Sprawdź, czy ruch sieciowy jest blokowany przez zapory maszyny Wirtualnej

Wyłącz zaporę, a następnie przetestować wynik. Jeśli ten problem zostanie rozwiązany, sprawdź ustawienia zapory, a następnie ponownie włącz zaporę.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Krok 4: Sprawdź, czy maszyna wirtualna aplikacji lub usługi nasłuchuje na porcie

Można użyć jednej z następujących metod, aby sprawdzić, czy maszyna wirtualna aplikacji lub usługi nasłuchuje na porcie.

- Uruchom następujące polecenia, aby sprawdzić, czy serwer nasłuchuje na tym porcie.

**Maszyna wirtualna z systemem Windows**

    netstat –ano

**Maszyna wirtualna z systemem Linux**

    netstat -l

- Uruchom **telnet** polecenia na maszynie wirtualnej, aby przetestować port. Jeśli test zakończy się niepowodzeniem, aplikacja lub usługa nie skonfigurowano do nasłuchiwania na tym porcie.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Krok 5. Sprawdź, czy problem jest spowodowany SNAT

W niektórych scenariuszach maszyny Wirtualnej jest umieszczany za rozwiązanie do równoważenia obciążenia, które ma zależność od zasobów spoza platformy Azure. W tych scenariuszach występują sporadyczne problemy z połączeniem, problem może być spowodowane [wyczerpanie portów SNAT](../load-balancer/load-balancer-outbound-connections.md). Aby rozwiązać ten problem, Utwórz adres VIP (lub ILPIP model klasyczny) dla każdej maszyny Wirtualnej, który jest modułem równoważenia obciążenia i zabezpieczanie przy użyciu sieciowej grupy zabezpieczeń lub listy ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Krok 6: Sprawdź, czy ruch jest blokowany przez listy ACL dla klasycznej maszyny Wirtualnej

Listy kontroli dostępu (ACL) zapewnia możliwość selektywnego akceptowanie lub odrzucanie ruchu dla punktu końcowego maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [listy ACL punktu końcowego zarządzania](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Krok 7: Sprawdź, czy punkt końcowy jest tworzony dla klasycznej maszyny Wirtualnej

Wszystkie maszyny wirtualne, które tworzysz na platformie Azure przy użyciu klasycznego modelu wdrażania automatycznie mogą komunikować się za pośrednictwem kanału sieci prywatnej z innymi maszynami wirtualnymi w tej samej usługi w chmurze lub sieci wirtualnej. Jednak komputery w innych sieciach wirtualnych wymagają punktów końcowych do kierowania ruchu sieciowego przychodzącego do maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [jak konfigurować punkty końcowe](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Krok 8: Spróbuj połączyć się z udziałem sieciowym maszyny Wirtualnej

Jeśli nie możesz połączyć do udziału sieciowego maszyny Wirtualnej, może być przyczyną problemu niedostępne karty sieciowe w maszynie Wirtualnej. Aby usunąć niedostępne karty sieciowe, zobacz [sposób usuwania niedostępne karty sieciowe](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Krok 9: Sprawdź łączność między sieciami wirtualnymi

Użyj [Weryfikacja przepływu dla adresu IP sieci obserwatora](../network-watcher/network-watcher-ip-flow-verify-overview.md) i [rejestrowanie przepływu sieciowych grup zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-overview.md) do określenia, czy istnieje sieciowa grupa zabezpieczeń lub Routing zdefiniowany przez użytkownika, uniemożliwiać przepływu ruchu. Można również sprawdzić konfigurację między sieciami wirtualnymi [tutaj](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.