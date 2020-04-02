---
title: Rozwiązywanie problemów dotyczących komunikacji równorzędnej sieci wirtualnych
description: Kroki ułatwiające rozwiązywanie większości problemów z komunikacją równorzędową w sieci wirtualnej.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 662619e101b45d1dd8b34ea97e31f214b254124a
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521883"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Rozwiązywanie problemów dotyczących komunikacji równorzędnej sieci wirtualnych

Ten przewodnik rozwiązywania problemów zawiera kroki ułatwiające rozwiązywanie większości problemów [z komunikacją równorzędową w sieci wirtualnej.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)

![Diagram komunikacji równorzędnej sieci wirtualnej](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Konfigurowanie komunikacji równorzędnej sieci wirtualnej między dwiema sieciami wirtualnymi

Czy sieci wirtualne są w tej samej subskrypcji lub w różnych subskrypcjach?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>Sieci wirtualne są w tej samej subskrypcji

Aby skonfigurować komunikację równorzędnych sieci wirtualnych dla sieci wirtualnych, które są w tej samej subskrypcji, należy użyć metod w następujących artykułach:

* Jeśli sieci wirtualne znajdują się w *tym samym regionie,* zobacz [Tworzenie komunikacji równorzędnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* Jeśli sieci wirtualne znajdują się w *różnych regionach,* zobacz [Komunikacja równorzędna sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 

> [!Note]
> Łączność nie działa w globalnej komunikacji równorzędnej sieci wirtualnej dla następujących zasobów: 
>
> * Maszyny wirtualne (maszyny wirtualne) za podstawową jednostką SKU modułu wewnętrznego równoważenia obciążenia (ILB)
> * Pamięć podręczna Redis (używa podstawowej jednostki SKU ILB)
> * Brama aplikacji (używa podstawowej jednostki SKU ILB)
> * Zestawy skalowania maszyny wirtualnej (używa podstawowej jednostki SKU równoważenia obciążenia)
> * Klastry sieci szkieletowej usług Azure (używa podstawowej jednostki SKU równoważenia obciążenia)
> * SQL Server Always On (używa podstawowej jednostki SKU równoważenia obciążenia)
> * Środowisko usługi Azure App Service dla usługi PowerApps (używa podstawowej jednostki SKU równoważenia obciążenia)
> * Usługa Azure API Management (używa podstawowych jednostek SKU równoważenia obciążenia)
> * Usługi domenowe usługi Active Directory platformy Azure (usługi Azure AD DS) (używa podstawowej jednostki SKU równoważenia obciążenia)

Aby uzyskać więcej informacji, zobacz [wymagania i ograniczenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) globalnej komunikacji równorzędnej.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>Sieci wirtualne znajdują się w różnych subskrypcjach lub dzierżawach usługi Active Directory

Aby skonfigurować komunikację równorzędną sieci wirtualnej dla sieci wirtualnych w różnych subskrypcjach lub dzierżawach usługi Active Directory, zobacz [Tworzenie komunikacji równorzędnej w różnych subskrypcjach interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)

> [!Note]
> Aby skonfigurować komunikację równorzędnych w sieci, w obu subskrypcjach muszą być uprawnienia **współautora sieci.** Aby uzyskać więcej informacji, zobacz [uprawnienia komunikacji równorzędnej](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Konfigurowanie komunikacji równorzędnej sieci wirtualnej za pomocą topologii z szprychą koncentratoru, która korzysta z zasobów lokalnych

![Diagram komunikacji równorzędnej sieci wirtualnej z wystąpieniem w środowisku lokalnym](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>W przypadku połączenia lokacja-lokacja lub połączenia usługi ExpressRoute

Wykonaj kroki opisane w: [Konfigurowanie przesyłania bramy sieci VPN do komunikacji równorzędnej w sieci wirtualnej](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="for-point-to-site-connections"></a>W przypadku połączeń typu punkt-lokacja

1. Wykonaj kroki opisane w: [Konfigurowanie przesyłania bramy sieci VPN do komunikacji równorzędnej w sieci wirtualnej](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. Po ustanowieniu lub zmianie komunikacji równorzędnej sieci wirtualnej pobierz i zainstaluj ponownie pakiet typu punkt-lokacja, aby klienci punktu do lokacji pobierali zaktualizowane trasy do sieci wirtualnej szprychy.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Konfigurowanie komunikacji równorzędnej sieci wirtualnej za pomocą sieci wirtualnej topologii z szprychami koncentratoru

![Diagram komunikacji równorzędnej sieci wirtualnej z szprychą sieci wirtualnej](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>Sieci wirtualne znajdują się w tym samym regionie


1. W sieci wirtualnej koncentratora skonfiguruj wirtualne urządzenie sieciowe (NVA).
1. W sieciach wirtualnych szprychy mają zdefiniowane przez użytkownika trasy z zastosowaniem następnego typu przeskoku "wirtualne urządzenie sieciowe".

Aby uzyskać więcej informacji, zobacz [Łączenie łańcucha usług](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Jeśli potrzebujesz pomocy w skonfigurowaniu urządzenia WUS, [skontaktuj się z dostawcą urządzenia WUS](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Aby uzyskać pomoc dotyczącą rozwiązywania problemów z konfiguracją i routingiem urządzeń NVA, zobacz [Problemy z urządzeniami wirtualnymi sieci na platformie Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### <a name="the-virtual-networks-are-in-different-regions"></a>Sieci wirtualne znajdują się w różnych regionach

Przesyłanie przez globalną sieć wirtualną komunikacji równorzędnej jest teraz obsługiwane. Łączność nie działa w globalnej sieci wirtualnej komunikacji równorzędnej dla następujących zasobów:

* Maszyny wirtualne za podstawową jednostką SKU ILB
* Pamięć podręczna Redis (używa podstawowej jednostki SKU ILB)
* Brama aplikacji (używa podstawowej jednostki SKU ILB)
* Zestawy skalowania (używa podstawowej jednostki SKU ILB)
* Klastry sieci szkieletowej usług (używa podstawowej jednostki SKU równoważenia obciążenia sieciowego)
* SQL Server Always On (używa podstawowej jednostki SKU równoważenia obciążenia)
* Środowisko usługi aplikacji (używa podstawowej jednostki SKU ILB)
* Zarządzanie interfejsami API (używa podstawowej jednostki SKU ILB)
* Usługi Azure AD DS (używa podstawowych jednostek SKU równoważenia obciążenia)

Aby dowiedzieć się więcej o globalnych wymaganiach i ograniczeniach komunikacji równorzędnej, zobacz [Komunikacja równorzędna w sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints).

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Rozwiązywanie problemów z łącznością między dwiema sieciami wirtualnymi równorzędnym

Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy za pomocą konta, które ma niezbędne [role i uprawnienia](virtual-network-manage-peering.md#permissions). Wybierz sieć wirtualną, wybierz pozycję **Komunikacja równorzędna**, a następnie sprawdź pole **Stan.** Jaki jest status?

### <a name="the-peering-status-is-connected"></a>Stan komunikacji równorzędnej to "Połączony"

Aby rozwiązać ten problem:

1. Sprawdź przepływy ruchu sieciowego:

   Użyj [rozwiązywania problemów z połączeniem](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) i [weryfikacji przepływu IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) ze źródłowej maszyny Wirtualnej do docelowej maszyny Wirtualnej, aby ustalić, czy istnieje grupa sieciowej lub UDR, która powoduje zakłócenia w przepływach ruchu.

   Jeśli używasz zapory lub urządzenia WUS: 
   1. Udokumentować parametry UDR, tak aby można było przywrócić je po zakończeniu tego kroku.
   2. Usuń UDR ze źródłowej podsieci maszyny Wirtualnej lub karty sieciowej, która wskazuje urządzenie WUS jako następny przeskok. Sprawdź łączność ze źródłową maszyną wirtualną bezpośrednio do miejsca docelowego, który jest pomijając urządzenia WUS. Jeśli ten krok nie działa, zobacz [narzędzie do rozwiązywania problemów z urządzeniami WUS](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

2. Weź ślad sieciowy: 
   1. Uruchom śledzenie sieci na docelowej maszynie wirtualnej. W systemie Windows można użyć programu **Netsh**. W przypadku systemu Linux należy użyć **protokołu TCPDump**.
   2. Uruchom **TcpPing** lub **PsPing** ze źródła do docelowego adresu IP.

      Jest to przykład polecenia **TcpPing:**`tcping64.exe -t <destination VM address> 3389`

   3. Po zakończeniu **TcpPing,** zatrzymać śledzenia sieci w miejscu docelowym.
   4. Jeśli pakiety pochodzą ze źródła, nie ma problemu z siecią. Sprawdź zarówno zaporę maszyny Wirtualnej, jak i aplikację nasłuchiwanie na tym porcie, aby zlokalizować problem z konfiguracją.

   > [!Note]
   > Nie można połączyć się z następującymi typami zasobów za pośrednictwem globalnej komunikacji równorzędnej sieci wirtualnej (sieci wirtualne w różnych regionach):
   >
   > * Maszyny wirtualne za podstawową jednostką SKU ILB
   > * Pamięć podręczna Redis (używa podstawowej jednostki SKU ILB)
   > * Brama aplikacji (używa podstawowej jednostki SKU ILB)
   > * Zestawy skalowania (używa podstawowej jednostki SKU ILB)
   > * Klastry sieci szkieletowej usług (używa podstawowej jednostki SKU równoważenia obciążenia sieciowego)
   > * SQL Server Always On (używa podstawowej jednostki SKU równoważenia obciążenia)
   > * Środowisko usługi aplikacji (używa podstawowej jednostki SKU ILB)
   > * Zarządzanie interfejsami API (używa podstawowej jednostki SKU ILB)
   > * Usługi Azure AD DS (używa podstawowych jednostek SKU równoważenia obciążenia)

Aby uzyskać więcej informacji, zobacz [wymagania i ograniczenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) globalnej komunikacji równorzędnej.

### <a name="the-peering-status-is-disconnected"></a>Stan komunikacji równorzędnej to "Rozłączony"

Aby rozwiązać ten problem, usuń komunikację równorzędną z obu sieci wirtualnych, a następnie ponownie je utworzyć.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Rozwiązywanie problemów z łącznością między siecią wirtualną szprychą koncentratoru a zasobem lokalnym

Czy twoja sieć korzysta z bramy NVA lub sieci VPN innej firmy?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Moja sieć korzysta z bramy urządzenia WUS lub sieci VPN innej firmy

Aby rozwiązać problemy z łącznością, które mają wpływ na bramę sieciowe lub bramę sieci VPN innej firmy, zobacz następujące artykuły:

* [Narzędzie do rozwiązywania problemów z systemem NVA](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Tworzenie łańcuchów usług](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>Moja sieć nie korzysta z bramy NVA ani bramy sieci VPN innej firmy

Czy sieć wirtualna centrum i sieć wirtualna szprychy mają bramę sieci VPN?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Zarówno sieć wirtualna koncentratora, jak i sieć wirtualna szprychy mają bramę sieci VPN

Korzystanie ze bramy zdalnej nie jest obsługiwane.

Jeśli sieć wirtualna szprychy ma już bramę sieci VPN, opcja **Użyj bramy zdalnej** nie jest obsługiwana w sieci wirtualnej szprychy. Wynika to z ograniczenia komunikacji równorzędnej sieci wirtualnej.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Zarówno sieć wirtualna koncentratora, jak i sieć wirtualna szprychy nie mają bramy sieci VPN

W przypadku połączeń lokacja-lokacja lub usługi Azure ExpressRoute sprawdź następujące główne przyczyny problemów z łącznością ze zdalną siecią wirtualną z lokalnego:

* W sieci wirtualnej, która ma bramę, sprawdź, czy jest zaznaczone pole wyboru **Zezwalaj na ruch przesyłany dalej.**
* W sieci wirtualnej, która nie ma bramy, sprawdź, czy jest zaznaczone pole wyboru **Użyj bramy zdalnej.**
* Niech administrator sieci sprawdzi urządzenia lokalne, aby sprawdzić, czy wszystkie mają dodaną przestrzeń adresową zdalnej sieci wirtualnej.

W przypadku połączeń typu punkt-lokacja:

* W sieci wirtualnej, która ma bramę, sprawdź, czy jest zaznaczone pole wyboru **Zezwalaj na ruch przesyłany dalej.**
* W sieci wirtualnej, która nie ma bramy, sprawdź, czy jest zaznaczone pole wyboru **Użyj bramy zdalnej.**
* Pobierz i zainstaluj ponownie pakiet klienta typu punkt-lokacja. Trasy sieci wirtualnej, które są nowo równorzędne, nie dodają automatycznie tras do klientów typu punkt-lokacja.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Rozwiązywanie problemów z łącznością sieciową z szprychami koncentratora między sieciami wirtualnymi szprychowymi w tym samym regionie

Sieć koncentratora musi zawierać urządzenie WUS. Skonfiguruj UDR w szprychach, które mają zestaw WUS ustawiony jako następny przeskok, i włącz **zezwalaj na przesyłanie dalej ruchu** w sieci wirtualnej koncentratora.

Aby uzyskać więcej informacji, zobacz [Łączenie sieci](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)usług i omówienie tych wymagań z wybranym dostawcą urządzenia [WUS.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Rozwiązywanie problemów z łącznością sieciową z szprychami koncentratora między sieciami wirtualnymi szprychowymi w różnych regionach

Przesyłanie przez globalną sieć wirtualną komunikacji równorzędnej jest teraz obsługiwane. Łączność nie działa w globalnej komunikacji równorzędnej sieci wirtualnej dla następujących zasobów:

* Maszyny wirtualne za podstawową jednostką SKU ILB
* Pamięć podręczna Redis (używa podstawowej jednostki SKU ILB)
* Brama aplikacji (używa podstawowej jednostki SKU ILB)
* Zestawy skalowania (używa podstawowej jednostki SKU ILB)
* Klastry sieci szkieletowej usług (używa podstawowej jednostki SKU równoważenia obciążenia sieciowego)
* SQL Server Always On (używa podstawowej jednostki SKU równoważenia obciążenia)
* Środowisko usługi aplikacji (używa podstawowej jednostki SKU ILB)
* Zarządzanie interfejsami API (używa podstawowej jednostki SKU ILB)
* Usługi Azure AD DS (używa podstawowych jednostek SKU równoważenia obciążenia)

Aby uzyskać więcej informacji, zobacz [wymagania i ograniczenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) globalnej komunikacji równorzędnej i różnych [topologii SIECI VPN](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/).

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Rozwiązywanie problemów z łącznością sieciową z szprychami koncentratora między aplikacją sieci web a siecią wirtualną szprychy

Aby rozwiązać ten problem:

1. Zaloguj się do Portalu Azure. 
1. W aplikacji sieci web wybierz pozycję **Sieć**, a następnie wybierz pozycję **Integracja sieci wirtualnej**.
1. Sprawdź, czy można wyświetlić zdalną sieć wirtualną. Ręczne wprowadzanie zdalnej przestrzeni adresowej sieci wirtualnej **(Synchronizacja sieci** i **Dodawanie tras).**

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Integracja aplikacji z siecią wirtualną platformy Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Routing sieci VPN typu punkt-lokacja — informacje](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Rozwiązywanie problemów z komunikatem o błędzie konfiguracji komunikacji równorzędnej sieci wirtualnej 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Bieżąca `<TENANT ID>` dzierżawa nie jest autoryzowana do uzyskiwania dostępu do połączonej subskrypcji

Aby rozwiązać ten problem, zobacz [Tworzenie komunikacji równorzędnej — azure cli](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

### <a name="not-connected"></a>Brak połączenia

Aby rozwiązać ten problem, usuń komunikację równorzędnej z obu sieci wirtualnych, a następnie ponownie je utworzyć.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Nie można równorzędnie sieci wirtualnej Databricks

Aby rozwiązać ten problem, skonfiguruj komunikację równorzędnej sieci wirtualnej w obszarze **Azure Databricks,** a następnie określ docelową sieć wirtualną przy użyciu **identyfikatora zasobu**. Aby uzyskać więcej informacji, zobacz [Równorzędne sieci wirtualnej Databricks do zdalnej sieci wirtualnej](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>W zdalnej sieci wirtualnej brakuje bramy

Ten problem występuje, gdy sieci wirtualne równorzędne `Use Remote Gateways`z różnych dzierżawców, a później chcesz skonfigurować . Ograniczeniem witryny Azure portal jest to, że nie można sprawdzić poprawność obecności bramy sieci wirtualnej w sieci wirtualnej innej dzierżawy.

Problem można rozwiązać na dwa sposoby:

 * Usuń komunikację równorzędną i aktywuj `Use Remote Gateways` tę opcję podczas tworzenia nowej komunikacji równorzędnej.
 * Użyj programu PowerShell lub CLI zamiast witryny `Use Remote Gateways`Azure Portal, aby włączyć program .

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z łącznością między maszynami wirtualnymi platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)
