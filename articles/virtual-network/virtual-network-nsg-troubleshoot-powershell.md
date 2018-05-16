---
title: Rozwiązywanie problemów z grup zabezpieczeń sieci - PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z grup zabezpieczeń sieci w modelu wdrażania usługi Azure Resource Manager przy użyciu programu Azure PowerShell.
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: 4c732bb7-5cb1-40af-9e6d-a2a307c2a9c4
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 3d1928428915d3ea5f9f28dc400f251b9f90679f
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Rozwiązywanie problemów z grup zabezpieczeń sieci przy użyciu programu Azure PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
> * [Program PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

Jeśli skonfigurowana grup zabezpieczeń sieci (NSG) na maszynie wirtualnej (VM) i występują problemy z połączeniem maszyny Wirtualnej, ten artykuł zawiera omówienie funkcji diagnostyki dla grup NSG do dalszego rozwiązywania.

Grupy NSG umożliwiają kontrolę typów ruchu przepływające i maszyn wirtualnych (VM). Grupy NSG można zastosować do podsieci w sieci wirtualnej platformy Azure (VNet) i/lub interfejsów sieciowych (NIC). Skuteczne zasady stosowane do karty Sieciowej są agregacji reguł, które istnieją w grup NSG stosowana do karty Sieciowej i podsieci, w której jest dołączona do. Reguły w tych grup NSG można czasami konflikt ze sobą i mieć wpływ na łączność sieciową z maszyny Wirtualnej.  

Można wyświetlić wszystkie reguły efektywnym elementem systemu zabezpieczeń z grup NSG, jak stosować kart sieciowych maszyny Wirtualnej. W tym artykule pokazano, jak rozwiązywać problemy z połączeniem maszyny Wirtualnej przy użyciu tych reguł w modelu wdrażania usługi Azure Resource Manager. Jeśli nie znasz z sieci wirtualnej i NSG pojęcia, przeczytaj [sieci wirtualnej](virtual-networks-overview.md) i [sieciowej grupy zabezpieczeń](virtual-networks-nsg.md) omówienie artykułów.

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Rozwiązywanie problemów z przepływem ruchu maszyny Wirtualnej za pomocą skuteczne reguły zabezpieczeń
Scenariusz, który następuje jest przykładem to powszechny problem połączenia:

Maszyna wirtualna o nazwie *VM1* jest częścią podsieci o nazwie *podsieć1* w ramach sieci wirtualnej o nazwie *WestUS VNet1*. Próba nawiązania połączenia z maszyną wirtualną za pomocą protokołu RDP za pośrednictwem portu 3389 protokołu TCP nie powiedzie się. Grupy NSG są stosowane w obu karty interfejsu Sieciowego *VM1 NIC1* i podsieć *podsieć1*. Ruch do portu 3389 protokołu TCP jest dozwolona w grupie NSG skojarzone z interfejsem sieciowym *VM1 NIC1*, ale TCP ping VM1 do portu 3389 kończy się niepowodzeniem.

Podczas tego przykładu korzysta z portu 3389 protokołu TCP, następujące czynności może służyć do określenia błędów połączenia przychodzącego i wychodzącego przez dowolnego portu.

## <a name="detailed-troubleshooting-steps"></a>Szczegółowe kroki rozwiązywania problemów
Wykonaj poniższe kroki, aby rozwiązać grup NSG dla maszyny Wirtualnej:

1. Uruchom sesję programu PowerShell systemu Azure i logowania do platformy Azure. Jeśli nie masz doświadczenia w obsłudze przy użyciu programu Azure PowerShell, przeczytaj [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) artykułu. Konto musi mieć przypisaną *Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action* operacji interfejsu sieciowego. Aby dowiedzieć się, jak przypisać operacje do kont, zobacz [Tworzenie niestandardowych ról dla kontroli dostępu](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Wprowadź następujące polecenie, aby zwrócić wszystkie reguły NSG stosowana do karty Sieciowej o nazwie *VM1 NIC1* w grupie zasobów *RG1*:
   
        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > Jeśli nie znasz nazwy karty sieciowej, wprowadź następujące polecenie, aby pobrać nazwy wszystkich kart sieciowych w grupie zasobów: 
   > 
   > `Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`
   > 
   > 
   
    Następujący tekst jest przykładowe dane wyjściowe skuteczne reguły zwrócone dla *VM1 NIC1* karty Sieciowej:
   
        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
   
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]
   
    Należy uwzględnić następujące informacje w danych wyjściowych:
   
   * Istnieją dwa **grupy NetworkSecurityGroup** sekcje: jedna jest skojarzony z podsiecią (*podsieć1*) i jest ono skojarzone z jedną kartą Sieciową (*VM1 NIC1*). W tym przykładzie zastosowano do każdej grupy NSG.
   * **Skojarzenie** pokazuje zasobów (podsieci lub kartę interfejsu Sieciowego) jest skojarzona z danej grupy NSG. Zasób NSG jest przenoszone/usunąć skojarzenia bezpośrednio przed uruchomieniem tego polecenia, może być konieczne kilka sekund zmiany do uwzględnienia w danych wyjściowych polecenia. 
   * Nazwy reguł, które są poprzedzone znakiem *defaultSecurityRules*: gdy grupa NSG jest tworzona, kilka domyślnych reguł zabezpieczeń są tworzone w niej. Nie można usunąć domyślnej reguły, ale mogą być zastąpione wyższy priorytet reguły.
     Odczyt [omówienie NSG](virtual-networks-nsg.md#default-rules) artykuł, aby dowiedzieć się więcej na temat NSG domyślne zasady zabezpieczeń.
   * **ExpandedAddressPrefix** rozszerza prefiksy adresów dla znaczników domyślnych NSG. Tagi reprezentują wiele prefiksów adresów. Rozszerzenia znaczników mogą być przydatne podczas rozwiązywania problemów z łącznością maszyny Wirtualnej z określonego adresu prefiksy. Na przykład z sieci Wirtualnej komunikacji równorzędnej, VIRTUAL_NETWORK tag zostanie rozwinięty, ukazując prefiksy połączyć za pomocą sieci wirtualnej w poprzednim danych wyjściowych.
     
     > [!NOTE]
     > Polecenie tylko pokazuje skutecznych zasad Jeśli grupa NSG jest skojarzona z podsiecią, karta sieciowa lub obu. Maszyna wirtualna może mieć wiele kart sieciowych z różnych grup NSG stosowana. Podczas rozwiązywania problemu, uruchom polecenie dla każdej karty sieciowej.
     > 
     > 
3. Aby ułatwić filtrowanie większej liczby reguły NSG, wprowadź następujące polecenia, aby dalsze poszukiwanie rozwiązania problemu: 
   
        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView
   
    Filtr dla ruchu protokołu RDP (TCP port 3389), jest stosowany do widoku siatki, jak pokazano na poniższej ilustracji:
   
    ![Lista reguł](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
4. Jak widać w widoku siatki, istnieją zarówno zezwalania i odmowy reguły protokołu RDP. Dane wyjściowe z kroku 2 wskazuje, że *DenyRDP* reguła jest w grupie NSG stosowana do podsieci. Dla reguł ruchu przychodzącego grupy NSG stosowana do podsieci są przetwarzane jako pierwsze. Jeśli zostanie znaleziony dopasowanie, grupa NSG stosowana do interfejsu sieciowego nie został przetworzony. W takim przypadku *DenyRDP* reguła z podsieci blokuje RDP do maszyny Wirtualnej (**VM1**).
   
   > [!NOTE]
   > Maszyna wirtualna może mieć wiele kart sieciowych do niego dołączony. Może być każdy jest połączony z innej podsieci. Ponieważ w poprzednich krokach polecenia są uruchamiane w odniesieniu do karty Sieciowej, jest ważne upewnić się, że możesz określić kartę Sieciową w przypadku wystąpienia awarii łączności do. Jeśli nie masz pewności, będzie można uruchamiać polecenia względem każdej karty Sieciowej podłączony do maszyny Wirtualnej.
   > 
   > 
5. Dla protokołu RDP w VM1, zmień *odmowy protokołu RDP (3389)* regułę dotyczącą *Zezwalaj RDP(3389)* w **NSG podsieć1** NSG. Upewnij się, że portu 3389 protokołu TCP jest otwarty przez otwarcie połączenia RDP do maszyny Wirtualnej lub przy użyciu narzędzia PsPing narzędzia. Użytkownik może dowiedzieć się więcej o narzędzia PsPing odczytując [stronę pobierania narzędzia PsPing](https://technet.microsoft.com/sysinternals/psping.aspx)
   
    Można lub usuń reguły z grupy NSG, korzystając z informacji w danych wyjściowych z następującego polecenia:
   
        Get-Help *-AzureRmNetworkSecurityRuleConfig

## <a name="considerations"></a>Zagadnienia do rozważenia
Podczas rozwiązywania problemów z łącznością, należy wziąć pod uwagę następujące kwestie:

* Reguły NSG domyślne zostanie blokowanie dostępu przychodzące z Internetu i zezwolić tylko sieci wirtualnej ruch przychodzący. Zasady należy jawnie dodać udostępnianie przychodzące z Internetu, zgodnie z wymaganiami.
* Jeśli nie żadnych reguł zabezpieczeń grupy NSG powoduje niepowodzenie łączności sieciowej maszyny Wirtualnej, problem może być z powodu:
  * Oprogramowanie zapory działających w systemie operacyjnym maszyny Wirtualnej
  * Trasy skonfigurowanych dla urządzenia wirtualnego lub ruch lokalny. Ruch internetowy mogą zostać przekierowane do lokalnego przy użyciu tunelowania wymuszonego. Połączenia protokołu RDP/SSH z Internetu do maszyny Wirtualnej może nie działać to ustawienie, w zależności od tego, jak sprzęt sieci lokalnej obsługuje ten ruch. Odczyt [Rozwiązywanie problemów z tras](virtual-network-routes-troubleshoot-powershell.md) artykuł, aby dowiedzieć się, jak diagnozować problemy trasy, które mogą utrudnić przepływu ruchu i z maszyny Wirtualnej. 
* Domyślnie ma połączyć za pomocą sieci wirtualnych, VIRTUAL_NETWORK tag zostanie automatycznie rozwiń węzeł aby objąć prefiksy połączyć za pomocą sieci wirtualnych. Możesz wyświetlić te prefiksy **ExpandedAddressPrefix** listy, aby rozwiązać problemy związane z łącznością komunikacji równorzędnej sieci wirtualnej. 
* Reguły efektywnym elementem systemu zabezpieczeń są wyświetlane tylko jeśli jest grupy NSG skojarzonej z maszyny Wirtualnej karty Sieciowej i podsieci. 
* Jeśli nie ma żadnych grup NSG skojarzona z kartą Sieciową lub w podsieci i publicznego adresu IP przypisane do maszyny Wirtualnej, wszystkie porty jest otwarte dla przychodzący i wychodzący dostęp. Jeśli maszyna wirtualna ma publiczny adres IP, zdecydowanie zalecane jest zastosowanie grup NSG do kart Sieciowych lub podsieci.  

