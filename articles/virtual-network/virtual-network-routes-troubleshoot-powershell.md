---
title: Rozwiązywanie problemów z tras - PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z trasy w modelu wdrażania usługi Azure Resource Manager przy użyciu programu Azure PowerShell.
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: bf7dc5e7-9399-460e-8e0d-8992dbed98a6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 4ef1387e3c8573a2bfa64c166f08bf47723eca62
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="troubleshoot-routes-using-azure-powershell"></a>Rozwiązywanie problemów z tras za pomocą programu Azure PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-routes-troubleshoot-portal.md)
> * [Program PowerShell](virtual-network-routes-troubleshoot-powershell.md)
> 
> 

Jeśli występują problemy z połączeniem sieciowym do lub z sieci maszyny wirtualnej Azure (VM), tras może mieć wpływ na Twoje ruch maszyny Wirtualnej. Ten artykuł zawiera omówienie funkcji diagnostyki dla tras do dalszego rozwiązywania.

Tabele tras są skojarzone z podsieciami i obowiązują na wszystkich interfejsach sieciowych (NIC) w tej podsieci. Następujące typy tras może odnosić się do każdego interfejsu sieciowego:

* **Trasy systemowe:** Domyślnie każda podsieć utworzona w sieci wirtualnej platformy Azure (VNet) ma tabel tras systemu, które umożliwiają lokalnej sieci wirtualnej ruch, ruch lokalnej za pośrednictwem bramy sieci VPN i ruchu internetowego. Istnieją również tras systemowych dla połączyć za pomocą sieci wirtualnych.
* **Trasy protokołu BGP:** propagowane do interfejsów sieciowych za pośrednictwem usługi ExpressRoute lub połączeń VPN lokacja lokacja. Dowiedz się więcej o routingu BGP, odczytując [protokołu BGP z bramy sieci VPN](../vpn-gateway/vpn-gateway-bgp-overview.md) i [omówienie ExpressRoute](../expressroute/expressroute-introduction.md) artykułów.
* **Trasy zdefiniowane przez użytkownika (przez):** korzystając z wirtualnych urządzeń sieciowych lub są wymuszone tunelowanie ruch do sieci lokalnej za pośrednictwem sieci VPN lokacja lokacja, może być zdefiniowana przez użytkownika tras (Udr) skojarzone z tabeli tras podsieci. Jeśli nie masz doświadczenia w obsłudze Udr, przeczytaj [trasy zdefiniowane przez użytkownika](virtual-networks-udr-overview.md#user-defined) artykułu.

Przy użyciu różnych tras, które można zastosować do interfejsu sieciowego może być trudne do ustalenia, obowiązują agregacji trasy. Do rozwiązywania problemów łączności sieciowej maszyny Wirtualnej, można wyświetlić wszystkie skuteczne trasy dla interfejsu sieciowego w modelu wdrażania usługi Azure Resource Manager.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>Rozwiązywanie problemów z przepływem ruchu maszyny Wirtualnej za pomocą skuteczne tras
W tym artykule używa następujący scenariusz jako przykład ilustrujący sposób rozwiązywania skuteczne trasy dla interfejsu sieciowego:

Maszyna wirtualna (*VM1*) podłączone do sieci wirtualnej (*VNet1*, prefiks: 10.9.0.0/16) nie może połączyć się VM(VM3) w nowo peered sieci wirtualnej (*VNet3*, prefiks 10.10.0.0/16). Brak Udr lub BGP kieruje stosowane do interfejsu sieciowego VM1 NIC1 podłączony do maszyny Wirtualnej, są stosowane tylko tras systemowych.

W tym artykule opisano sposób ustalić przyczynę niepowodzenia połączenia przy użyciu możliwości wprowadzenia trasy w modelu wdrażania zarządzania zasobami Azure.
Podczas w przykładzie użyto tylko tras systemowych, te same kroki może służyć do określenia błędów połączenia przychodzącego i wychodzącego przez dowolnego typu trasy.

> [!NOTE]
> Jeśli maszyna wirtualna ma więcej niż jedną kartę Sieciową podłączoną, sprawdź skuteczne trasy dla każdej z kart sieciowych do diagnozowania problemów z łącznością sieciową z maszyny Wirtualnej i.
> 
> 

### <a name="view-effective-routes-for-a-virtual-machine"></a>Widok skuteczne trasy dla maszyny wirtualnej
Aby wyświetlić trasy agregacji, które są stosowane do maszyny Wirtualnej, wykonaj następujące kroki:

### <a name="view-effective-routes-for-a-network-interface"></a>Widok skuteczne trasy dla interfejsu sieciowego
Aby wyświetlić trasy agregacji, które są stosowane do interfejsu sieciowego, wykonaj następujące kroki:

1. Uruchom sesję programu PowerShell systemu Azure i logowania do platformy Azure. Jeśli nie masz doświadczenia w obsłudze programu Azure PowerShell, przeczytaj [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) artykułu. Konto musi mieć przypisaną *Microsoft.Network/networkInterfaces/effectiveRouteTable/action* operacji interfejsu sieciowego. Aby dowiedzieć się, jak przypisać operacje do kont, zobacz [Tworzenie niestandardowych ról dla kontroli dostępu](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Poniższe polecenie zwraca wszystkie trasy stosowany do karty sieciowej o nazwie *VM1 NIC1* w grupie zasobów *RG1*.
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > Jeśli nie znasz nazwę karty sieciowej, wpisz następujące polecenie, aby pobrać wszystkie interfejsy sieciowe group.* zasobu nazwy
   > 
   > 
   
       Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name
   
   Następujące dane wyjściowe wygląda podobnie do danych wyjściowych dla każdej trasy stosowana do podsieci, którą jest połączona karta sieciowa:
   
       Name :
       State : Active
       AddressPrefix : {10.9.0.0/16}
       NextHopType : VNetLocal
       NextHopIpAddress : {}
   
       Name :
       State : Active
       AddressPrefix : {0.0.0.0/16}
       NextHopType : Internet
       NextHopIpAddress : {}
   
   Zwróć uwagę następujące opcje w danych wyjściowych:
   
   * **Nazwa**: Nazwa trasy skuteczne może być pusty, chyba że jawnie określone dla tras zdefiniowanych przez użytkownika. 
   * **Stan**: wskazuje stan skuteczne trasy. Możliwe wartości to "Active" lub "Nieprawidłowe"
   * **AddressPrefixes**: Określa prefiks adresu skuteczne trasy w notacji CIDR. 
   * **Typ następnego przeskoku**: wskazuje następnego skoku dla danej trasy. Możliwe wartości to *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*, lub *Null*. Wartość *Null* dla **Typ następnego przeskoku** przez może oznaczać nieprawidłową trasy. Na przykład jeśli **Typ następnego przeskoku** jest *VirtualAppliance* i urządzenie wirtualne sieci maszyny Wirtualnej nie jest w stanie zainicjowano obsługę administracyjną uruchomiona. Jeśli **Typ następnego przeskoku** jest *właściwość VPNGateway* i nie ma żadnych bramy elastycznie/uruchomiony w danej sieci wirtualnej, trasa mogą być nieprawidłowe.
   * **Adres IP następnego przeskoku**: Określa adres IP następnego przeskoku skuteczne trasy.
   
   Poniższe polecenie zwraca trasy w łatwiejsze wyświetlić tabelę:
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table
   
   Następujące dane wyjściowe są niektóre z danych wyjściowych dla scenariusza opisanego wcześniej:
   
       Name State AddressPrefix NextHopType NextHopIpAddress
       ---- ----- ------------- ----------- ----------------
       Active {10.9.0.0/16} VnetLocal {}
       Active {0.0.0.0/0} Internet {}
3. Brak trasy do *WestUS VNet3* sieciami wirtualnymi (prefiks 10.10.0.0/16)** z *WestUS VNet1* (prefiksu 10.9.0.0/16) w danych wyjściowych z poprzedniego kroku. Jak pokazano na poniższej ilustracji, równorzędna sieci wirtualnej Połącz z *WestUS VNet3* sieci wirtualnej jest w *Rozłączono* stanu.
   
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)
   
    Łącze dwukierunkowe dla komunikacji równorzędnej jest uszkodzona, który wyjaśnia dlaczego VM1 nie może połączyć się VM3 w *WestUS VNet3* sieci wirtualnej. Konfiguracja dwukierunkowe sieci wirtualnej komunikacji równorzędnej łącze ponownie *WestUS VNet1* i *WestUS VNet3* sieci wirtualnych. Dane wyjściowe zwrócone w wyniku łącze sieci wirtualnej komunikacji równorzędnej jest poprawnie ustanowić następująco:
   
        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
   
    Po określeniu problem, można dodać, usunąć, lub zmień tras i tabel tras. Wpisz następujące polecenie, aby wyświetlić listę poleceń używanych w tym celu:
   
        Get-Help *-AzureRmRouteConfig

## <a name="considerations"></a>Zagadnienia do rozważenia
Zwrócono kilka rzeczy, które należy wziąć pod uwagę podczas przeglądania listy tras:

* Routing jest oparta na Najdłuższy prefiks dopasowania (LPM) Wybierane spośród Udr, trasy protokołu BGP i systemu. Jeśli istnieje więcej niż jedna trasa z tym samym dopasowaniem LPM, następnie wybór trasy odbywa się w następującej kolejności:
  
  * Trasa zdefiniowana przez użytkownika
  * Trasa protokołu BGP
  * Trasa systemowa (ustawienie domyślne)
    
    Skuteczne trasy może zobaczyć tylko skuteczne ścieżek, które są dopasowaniem LPM oparte na trasach są one dostępne. Przez pokazanie, jak trasy faktycznie są oceniane pod kątem danej karty Sieciowej, dzięki temu znacznie łatwiejsze Rozwiązywanie problemów z określonej trasy, które mogą mieć wpływ na łączność z maszyny Wirtualnej.
* Jeśli masz Udr i wysyłania ruchu do sieci (NVA), urządzenie wirtualne z *VirtualAppliance* jako **Typ następnego przeskoku**, upewnij się, że przekazywanie IP jest włączona na NVA odbieranie ruchu lub pakietów porzucone. 
* Jeśli włączono wymuszony tunelowania cały ruch wychodzący z Internetem będą kierowane do lokalnego. Protokołu RDP/SSH z Internetu do maszyny Wirtualnej może nie działać z tym ustawieniem, w zależności od tego, jak lokalną obsługuje ten ruch. 
  Tunelowanie wymuszone można włączyć:
  * Jeśli przy użyciu sieci VPN typu lokacja lokacja, ustawiając trasy zdefiniowane przez użytkownika (przez) z Typ następnego przeskoku jako brama sieci VPN
  * Jeśli trasa domyślna jest anonsowana za pośrednictwem protokołu BGP
* Dla sieci wirtualnej komunikacji równorzędnej ruchu działał prawidłowo, trasa systemu o **Typ następnego przeskoku** *VNetPeering* musi istnieć dla zakresu prefiksu peered sieci wirtualnej. Jeśli taka trasa nie istnieje, a łącze sieci wirtualnej komunikacji równorzędnej wygląda OK:
  * Poczekaj kilka sekund, a następnie spróbuj ponownie, jeśli jest to nowo utworzonego łącze komunikacji równorzędnej. Od czasu do czasu zajmuje więcej czasu propagowania tras dla wszystkich interfejsów sieciowych w podsieci.
  * Zasady grupy zabezpieczeń sieci (NSG) może mieć wpływ na przepływów ruchu sieciowego. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z grup zabezpieczeń sieci](virtual-network-nsg-troubleshoot-powershell.md) artykułu.

