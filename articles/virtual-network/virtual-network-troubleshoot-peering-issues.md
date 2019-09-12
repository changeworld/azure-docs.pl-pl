---
title: Rozwiązywanie problemów dotyczących komunikacji równorzędnej sieci wirtualnych
description: Kroki pomagające w rozwiązaniu większości problemów z siecią wirtualną za pomocą komunikacji równorzędnej.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: fc01a20a077a1c624ed490600db919fe3197556c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901765"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Rozwiązywanie problemów dotyczących komunikacji równorzędnej sieci wirtualnych

Ten przewodnik rozwiązywania problemów zawiera kroki ułatwiające rozwiązywanie typowych problemów z [siecią równorzędną sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) .

![IMAGE](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="scenario-1-configure-virtual-network-peering-between-two-virtual-networks"></a>Scenariusz 1: Konfigurowanie komunikacji równorzędnej sieci wirtualnej między dwiema sieciami wirtualnymi

Czy sieci wirtualne znajdują się w tej samej subskrypcji lub w różnych subskrypcjach?

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-subscription"></a>Typ połączenia 1: Sieci wirtualne znajdują się w tej samej subskrypcji

Aby skonfigurować komunikację równorzędną sieci wirtualnych dla sieci wirtualnych, które znajdują się w tej samej subskrypcji, należy skorzystać z metod opisanych w poniższych artykułach, zgodnie z potrzebami:

* Jeśli sieci wirtualne znajdują się w **tym samym regionie**, postępuj zgodnie z instrukcjami, aby [utworzyć komunikację równorzędną dla sieci wirtualnych w tej samej subskrypcji](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* Jeśli sieci wirtualne znajdują się w **różnych regionach**, postępuj zgodnie z instrukcjami, aby skonfigurować [globalne wirtualne sieci równorzędne](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).  

> [!Note]
> Łączność nie będzie przekroczyć globalnej komunikacji równorzędnej sieci wirtualnej dla następujących zasobów: 
>
> * Maszyny wirtualne za podstawową jednostką SKU ILB
> * Redis Cache (używa podstawowej jednostki SKU ILB)
> * Application Gateway (używa podstawowej jednostki SKU ILB)
> * Zestawy skalowania (używa podstawowej jednostki SKU ILB)
> * Klastry Service Fabric (używa podstawowej jednostki SKU ILB)
> * Zawsze włączone SQL (używa podstawowej jednostki SKU ILB)
> * Środowiska App Service (ASE) (używa podstawowej jednostki SKU ILB)
> * API Management (używa podstawowej jednostki SKU ILB)
> * Azure Active Directory Domain Service (dodaje) (używa podstawowej jednostki SKU ILB)

Aby uzyskać więcej informacji, zobacz [wymagania i ograniczenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) globalnej komunikacji równorzędnej.

### <a name="connection-type-2-the-virtual-networks-are-in-different-subscriptions-or-ad-tenants"></a>Typ połączenia 2: Sieci wirtualne znajdują się w różnych subskrypcjach lub dzierżawach usługi AD

Aby skonfigurować komunikację równorzędną sieci wirtualnych dla sieci wirtualnych w różnych subskrypcjach lub Active Directory dzierżawców, wykonaj kroki opisane w temacie [Tworzenie komunikacji równorzędnej w różnych subskrypcjach interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

> [!Note]
> Aby skonfigurować komunikację równorzędną sieci, musisz mieć uprawnienia **współautora sieci** w obu subskrypcjach. Aby uzyskać więcej informacji, zobacz [uprawnienia komunikacji równorzędnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering%23permissions).

## <a name="scenario-2-configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Scenariusz 2: Konfigurowanie komunikacji równorzędnej sieci wirtualnej z topologią gwiazdy, która korzysta z zasobów lokalnych

![IMAGE](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="connection-type-1-for-site-to-site-connection-or-expressroute-connection"></a>Typ połączenia 1: Dla połączenia lokacja-lokacja lub połączenia ExpressRoute

Wykonaj kroki opisane w temacie: [Skonfiguruj tranzyt bramy sieci VPN dla komunikacji równorzędnej sieci wirtualnej](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="connection-type-2-for-point-to-site-connections"></a>Typ połączenia 2: Dla połączeń punkt-lokacja

1. Wykonaj kroki opisane w temacie: [Skonfiguruj tranzyt bramy sieci VPN dla komunikacji równorzędnej sieci wirtualnej](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. Po ustanowieniu lub zmianie komunikacji równorzędnej sieci wirtualnej należy pobrać i zainstalować pakiet punkt-lokacja w celu uzyskania zaktualizowanych tras do sieci wirtualnej szprychy.

## <a name="scenario-3-configure-virtual-network-peering-with-hub-spoke-topology-for-azure-virtual-network"></a>Scenariusz 3: Konfigurowanie komunikacji równorzędnej sieci wirtualnej z topologią gwiazdy dla platformy Azure Virtual Network

![IMAGE](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-region"></a>Typ połączenia 1: Sieci wirtualne znajdują się w tym samym regionie

Należy skonfigurować wirtualne urządzenie sieciowe (urządzenie WUS) w sieci wirtualnej centrum i mieć trasy zdefiniowane przez użytkownika z następnym przeskokiem "sieciowe urządzenie wirtualne" stosowane w sieciach wirtualnych szprych. Aby uzyskać więcej informacji, zobacz Tworzenie [łańcucha usług](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Jeśli potrzebujesz pomocy przy konfigurowaniu urządzenie WUS, [skontaktuj się z dostawcą urządzenie WUS](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Aby uzyskać pomoc dotyczącą rozwiązywania problemów z konfiguracją i routingiem urządzenia urządzenie WUS, zobacz [problemy dotyczące sieciowego urządzenia wirtualnego na platformie Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### <a name="connection-type-2-the-virtual-networks-are-in-different-regions"></a>Typ połączenia 2: Sieci wirtualne znajdują się w różnych regionach

Przesyłanie za pośrednictwem globalnej komunikacji równorzędnej sieci wirtualnej jest teraz obsługiwane. Łączność nie działa w przypadku globalnej komunikacji równorzędnej sieci wirtualnej dla następujących zasobów:

* Maszyny wirtualne za podstawową jednostką SKU ILB
* Redis Cache (używa podstawowej jednostki SKU ILB)
* Application Gateway (używa podstawowej jednostki SKU ILB)
* Zestawy skalowania (używa podstawowej jednostki SKU ILB)
* Klastry Service Fabric (używa podstawowej jednostki SKU ILB)
* Zawsze włączone SQL (używa podstawowej jednostki SKU ILB)
* Środowiska App Service (ASE) (używa podstawowej jednostki SKU ILB)
* API Management (używa podstawowej jednostki SKU ILB)
* Azure Active Directory Domain Service (dodaje) (używa podstawowej jednostki SKU ILB)

Aby dowiedzieć się więcej o globalnych wymaganiach dotyczących komunikacji równorzędnej i ograniczeniach, zobacz [wirtualne sieci równorzędne](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints).

## <a name="scenario-4-i-have-a-connectivity-issue-between-two-peered-virtual-networks"></a>Scenariusz 4: Mam problem z łącznością między dwiema równorzędnymi sieciami wirtualnymi

Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta, które ma wymagane [role i uprawnienia](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions). Wybierz sieć wirtualną, wybierz pozycję **Komunikacja równorzędna**, a następnie sprawdź pole **stan** . Jaki jest stan?

### <a name="connection-type-1-the-peering-status-shows-connected"></a>Typ połączenia 1: Stan komunikacji równorzędnej pokazuje "Połączono"

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Sprawdź przepływy ruchu sieciowego:

   Użyj [rozwiązywania problemów z połączeniami](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) i [weryfikacji przepływu IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) ze źródłowej maszyny wirtualnej do docelowej maszyny wirtualnej, aby określić, czy istnieje sieciowej grupy zabezpieczeń lub UDR powodujące zakłócenia w przepływach ruchu.

   Jeśli używasz zapory lub urządzenia urządzenie WUS, wykonaj następujące kroki: 
   1. Udokumentować parametry UDR, aby można je było przywrócić po zakończeniu tego kroku.
   2. Usuń UDR ze źródłowej podsieci maszyny wirtualnej lub karty sieciowej, która wskazuje na urządzenie WUS w następnym przeskoku. Sprawdź łączność ze źródłowej maszyny wirtualnej bezpośrednio do lokalizacji docelowej, która pomija urządzenie WUS. Jeśli ten krok działa, zapoznaj się z pomocą [Narzędzia do rozwiązywania problemów urządzenie WUS](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

2. Wykonaj śledzenie sieci: 
   1. Rozpocznij śledzenie sieci na docelowej maszynie wirtualnej. Dla systemu Windows można użyć **narzędzia Netsh**. W przypadku systemu Linux Użyj **TCPDump**.
   2. Uruchom **TcpPing** lub **PsPing** ze źródła do docelowego adresu IP.

   * Jest to przykład polecenia **TcpPing** :`tcping64.exe -t <destination VM address> 3389`

   3. Po zakończeniu **TcpPing** Zatrzymaj śledzenie sieci w miejscu docelowym.
   4. Jeśli pakiety dotarły ze źródła, nie ma problemu z siecią. Aby zlokalizować problem z konfiguracją, należy przeanalizować zarówno zaporę maszyny wirtualnej, jak i aplikację nasłuchującą na tym porcie.

   > [!Note]
   > Nie można połączyć się z następującymi typami zasobów za pośrednictwem globalnej komunikacji równorzędnej sieci wirtualnej (sieci wirtualne w różnych regionach):
   >
   > * Maszyny wirtualne za podstawową jednostką SKU ILB
   > * Redis Cache (używa podstawowej jednostki SKU ILB)
   > * Application Gateway (używa podstawowej jednostki SKU ILB)
   > * Zestawy skalowania (używa podstawowej jednostki SKU ILB)
   > * Klastry Service Fabric (używa podstawowej jednostki SKU ILB)
   > * Zawsze włączone SQL (używa podstawowej jednostki SKU ILB)
   > * Środowiska App Service (ASE) (używa podstawowej jednostki SKU ILB)
   > * API Management (używa podstawowej jednostki SKU ILB)
   > * Azure Active Directory Domain Service (dodaje) (używa podstawowej jednostki SKU ILB)

Aby uzyskać więcej informacji, zobacz [wymagania i ograniczenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) globalnej komunikacji równorzędnej.

### <a name="connection-type-2-the-peering-status-shows-disconnected"></a>Typ połączenia 2: Stan komunikacji równorzędnej pokazuje "Rozłączono"

Należy usunąć komunikację równorzędną z obu sieci wirtualnych i utworzyć je ponownie.

## <a name="scenario-5-i-have-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-on-premises-resource"></a>Scenariusz 5: Mam problem z łącznością między siecią wirtualną gwiazdy i zasobem lokalnym

Czy używasz bramy urządzenie WUS lub sieci VPN innej firmy?

### <a name="connection-type-1-my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Typ połączenia 1: Moja sieć używa bramy urządzenie WUS lub sieci VPN innej firmy

Aby rozwiązać problemy z łącznością, które wpływają na usługę urządzenie WUS lub bramę sieci VPN innej firmy, zobacz następujące artykuły:

* [URZĄDZENIE WUS narzędzia do rozwiązywania problemów](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Tworzenie łańcucha usług](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="connection-type-2-my-network-does-not-a-third-party-nva-or-vpn-gateway"></a>Typ połączenia 2: Moja sieć nie jest bramą urządzenie WUS lub sieci VPN innej firmy

Czy sieci wirtualne Hub i szprychy mają bramę sieci VPN?

#### <a name="both-the-hub-and-spoke-virtual-networks-have-a-vpn-gateway"></a>Sieci wirtualne koncentratora i szprychy mają bramę sieci VPN

Korzystanie z bramy zdalnej nie jest obsługiwane.

Ze względu na ograniczenie sieci równorzędnej sieci wirtualnej **Używanie bramy zdalnej** nie jest obsługiwane w sieci wirtualnej szprychy, jeśli sieć wirtualna szprych ma już bramę sieci VPN.

#### <a name="both-the-hub-and-spoke-virtual-networks-dont-have-a-vpn-gateway"></a>Sieci wirtualne Hub i szprych nie mają bramy sieci VPN

W przypadku połączeń typu lokacja-lokacja lub ExpressRoute Sprawdź te główne przyczyny problemów z łącznością ze zdalną siecią wirtualną w środowisku lokalnym.

* Sprawdź, czy w sieci wirtualnej z bramą jest zaznaczone pole wyboru **Zezwalaj na ruch przesłany dalej** .
* Upewnij się, że pole wyboru **Użyj bramy zdalnej** jest zaznaczone w sieci wirtualnej, która nie ma bramy.
* Skontaktuj się z administratorem sieci, aby upewnić się, że wszystkie te urządzenia mają dodaną zdalną przestrzeń adresową sieci wirtualnej.

Dla połączeń punkt-lokacja:

* Sprawdź, czy w sieci wirtualnej z bramą jest zaznaczone pole wyboru **Zezwalaj na ruch przesłany dalej** .
* Upewnij się, że pole wyboru **Użyj bramy zdalnej** jest zaznaczone w sieci wirtualnej, która nie ma bramy.
* Pobierz i zainstaluj ponownie pakiet klienta punkt-lokacja. Trasy sieci wirtualnej, które są nowo połączone za pomocą komunikacji równorzędnej, nie są automatycznie dodawane do klientów punkt-lokacja.

## <a name="scenario-6-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Scenariusz 6: Mam problem z łącznością sieciową gwiazdy między sieciami wirtualnymi szprych w tym samym regionie

Musisz mieć urządzenie WUS w sieci centrów, skonfigurować UDR w szprychach, które mają ustawiony urządzenie WUS w następnym przeskoku, i włączyć opcję **Zezwalaj na ruch przesłany dalej** w sieci wirtualnej centrum.

Aby uzyskać więcej informacji, zobacz Tworzenie [łańcucha usług](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)i omawiaj wymagania przy użyciu wybranego przez siebie [dostawcy urządzenie WUS](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) .

## <a name="scenario-7-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Scenariusz 7: Mam problem z łącznością sieciową gwiazdy między sieciami wirtualnymi szprych w różnych regionach

Przesyłanie za pośrednictwem globalnej komunikacji równorzędnej sieci wirtualnej jest teraz obsługiwane. Łączność nie będzie przekroczyć globalnej komunikacji równorzędnej sieci wirtualnej dla następujących zasobów:

* Maszyny wirtualne za podstawową jednostką SKU ILB
* Redis Cache (używa podstawowej jednostki SKU ILB)
* Application Gateway (używa podstawowej jednostki SKU ILB)
* Zestawy skalowania (używa podstawowej jednostki SKU ILB)
* Klastry Service Fabric (używa podstawowej jednostki SKU ILB)
* Zawsze włączone SQL (używa podstawowej jednostki SKU ILB)
* Środowiska App Service (ASE) (używa podstawowej jednostki SKU ILB)
* API Management (używa podstawowej jednostki SKU ILB)
* Azure Active Directory Domain Service (dodaje) (używa podstawowej jednostki SKU ILB)

Aby uzyskać więcej informacji, zobacz [wymagania i ograniczenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) globalnej komunikacji równorzędnej i [różnych topologii sieci VPN](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/).

## <a name="scenario-8-i-have-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Scenariusz 8: Mam problem z łącznością sieciową gwiazdy między aplikacją sieci Web a siecią wirtualną szprychy

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Zaloguj się do Azure Portal. Przejdź do aplikacji sieci Web, wybierz pozycję Sieć, a następnie wybierz pozycję integracja z **siecią** **wirtualną**.
2. Sprawdź, czy jest wyświetlana zdalna Sieć wirtualna. Ręcznie wprowadź zdalną przestrzeń adresową sieci wirtualnej (**Synchronizacja sieci** i **Dodawanie tras**).

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Integrowanie aplikacji z usługą Azure Virtual Network](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Routing sieci VPN typu punkt-lokacja — informacje](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="scenario-9-i-receive-an-error-when-configuring-virtual-network-peering"></a>Scenariusz 9: Wystąpił błąd podczas konfigurowania komunikacji równorzędnej sieci wirtualnej

### <a name="error-1-current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Błąd 1: Bieżąca dzierżawa `<TENANT ID>` nie ma autoryzacji dostępu do połączonej subskrypcji

Aby rozwiązać ten problem, wykonaj kroki opisane w temacie [Tworzenie komunikacji równorzędnej — interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

### <a name="error-2-not-connected"></a>Błąd 2: Niepołączone

Należy usunąć komunikację równorzędną z obu sieci wirtualnych i utworzyć je ponownie.

### <a name="error-3-failed-to-peer-a-databricks-virtual-network"></a>Błąd 3: Nie można nawiązać komunikacji równorzędnej z siecią wirtualną datakostkami

Aby rozwiązać ten problem, należy skonfigurować komunikację równorzędną sieci wirtualnej z bloku **Azure Databricks** , a następnie określić docelową sieć wirtualną przy użyciu **identyfikatora zasobu**. Aby uzyskać więcej informacji, zobacz [równorzędną sieć wirtualną z siecią wirtualną do zdalnej sieci wirtualnej](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z łącznością między maszynami wirtualnymi platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)