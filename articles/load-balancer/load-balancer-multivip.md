---
title: Wiele adresów VIP dla usługi w chmurze
titlesuffix: Azure Load Balancer
description: Omówienie obejmujące oraz sposób ustawiania wielu adresów VIP w usłudze w chmurze
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 3e97bea85d4d97b159168b21b4a6e932e655ccfb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274705"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Konfigurowanie wielu adresów VIP dla usługi w chmurze

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Dostęp do usług Azure Cloud Services za pośrednictwem publicznej sieci Internet można uzyskać przy użyciu adresu IP dostarczonego przez platformę Azure. Ten publiczny adres IP jest określany mianem adresu VIP (wirtualnego adresu IP), ponieważ jest powiązany z modułem równoważenia obciążenia platformy Azure, a nie wystąpieniami maszyn wirtualnych w ramach usługi w chmurze. Możesz uzyskać dostęp do dowolnego wystąpienia maszyny wirtualnej w ramach usługi w chmurze przy użyciu jednego adresu VIP.

Istnieją jednak scenariusze, w których może być potrzebny więcej niż jeden adres VIP jako punkt wejścia do tej samej usługi w chmurze. Na przykład usługa w chmurze może hostować wiele witryn sieci Web, które wymagają łączności SSL przy użyciu domyślnego portu 443, ponieważ każda lokacja jest hostowana dla innego klienta lub dzierżawcy. W tym scenariuszu należy mieć inny publiczny adres IP dla każdej witryny sieci Web. Na poniższym diagramie przedstawiono typowy wielodostępny Host internetowy z potrzebami dla wielu certyfikatów SSL na tym samym porcie publicznym.

![Scenariusz SSL z użyciem protokołu wieloadresowego](./media/load-balancer-multivip/Figure1.png)

W powyższym przykładzie wszystkie adresy VIP używają tego samego portu publicznego (443), a ruch jest przekierowywany do co najmniej jednej maszyny wirtualnej z równoważeniem obciążenia na unikatowym porcie prywatnym dla wewnętrznego adresu IP usługi w chmurze obsługującej wszystkie witryny sieci Web.

> [!NOTE]
> Inna sytuacja wymagająca użycia wielu adresów VIP obsługuje wiele odbiorników grup dostępności SQL AlwaysOn w tym samym zestawie Virtual Machines.

Adresy VIP są domyślnie dynamiczne, co oznacza, że rzeczywisty adres IP przypisany do usługi w chmurze może ulec zmianie z upływem czasu. Aby temu zapobiec, można zarezerwować adres VIP dla usługi. Aby dowiedzieć się więcej na temat zarezerwowanych adresów VIP, zobacz temat [zastrzeżony publiczny adres IP](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Zobacz [cennik adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses/) , aby uzyskać informacje o cenach dla adresów VIP i zastrzeżonych adresów IP.

Możesz użyć programu PowerShell do zweryfikowania adresów VIP używanych przez usługi w chmurze, a także dodawania i usuwania adresów VIP, kojarzenia adresu VIP z punktem końcowym i konfigurowania równoważenia obciążenia dla określonego adresu VIP.

## <a name="limitations"></a>Ograniczenia

W tej chwili funkcjonalność obejmująca wiele adresów VIP jest ograniczona do następujących scenariuszy:

* **Tylko IaaS**. Dla usług w chmurze, które zawierają maszyny wirtualne, można włączyć tylko wiele adresów VIP. W scenariuszach PaaS z wystąpieniami roli nie można używać wieloadresowego adresu VIP.
* **Tylko program PowerShell**. Można zarządzać tylko wieloadresami VIP przy użyciu programu PowerShell.

Te ograniczenia są tymczasowe i mogą ulec zmianie w dowolnym momencie. Pamiętaj, aby ponownie odwiedzić Tę stronę, aby zweryfikować przyszłe zmiany.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Jak dodać adres VIP do usługi w chmurze
Aby dodać adres VIP do usługi, uruchom następujące polecenie programu PowerShell:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

To polecenie wyświetla wynik podobny do następującego przykładu:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Jak usunąć adres VIP z usługi w chmurze
Aby usunąć adres VIP dodany do usługi w powyższym przykładzie, uruchom następujące polecenie programu PowerShell:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Adres VIP można usunąć tylko wtedy, gdy nie ma żadnych punktów końcowych skojarzonych z nim.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Jak pobrać informacje o adresie VIP z usługi w chmurze
Aby pobrać adresy VIP skojarzone z usługą w chmurze, uruchom następujący skrypt programu PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Skrypt wyświetla wynik podobny do następującego przykładu:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

W tym przykładzie usługa w chmurze ma 3 adresy VIP:

* **Adresu VIP1** jest domyślnym adresem IP, dlatego wiesz, że wartość dla IsDnsProgrammedName jest ustawiona na true.
* **VIP2** i **Vip3** nie są używane, ponieważ nie mają żadnych adresów IP. Będą one używane tylko w przypadku skojarzenia punktu końcowego z adresem VIP.

> [!NOTE]
> Opłata zostanie naliczona tylko za dodatkowe adresy VIP po skojarzeniu ich z punktem końcowym. Aby uzyskać więcej informacji na temat cen, zobacz [cennik adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Jak skojarzyć wirtualne adresy IP z punktem końcowym

Aby skojarzyć adres VIP w usłudze w chmurze z punktem końcowym, uruchom następujące polecenie programu PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

Polecenie tworzy punkt końcowy połączony z adresem VIP o nazwie *VIP2* na porcie *80*, a następnie łączy go z maszyną wirtualną o nazwie *myVM1* w usłudze w chmurze o nazwie Moja *usługa* przy użyciu *protokołu TCP* na porcie *8080*.

Aby sprawdzić konfigurację, uruchom następujące polecenie programu PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Dane wyjściowe wyglądają podobnie do poniższego przykładu:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Jak włączyć równoważenie obciążenia w określonym wirtualnym adresie IP

Można skojarzyć pojedynczy adres VIP z wieloma maszynami wirtualnymi w celu zrównoważenia obciążenia. Na przykład masz usługę w chmurze o nazwie Moja *Usługa*i dwie maszyny wirtualne o nazwach *myVM1* i *myVM2*. A usługa w chmurze ma wiele adresów VIP, jeden z nich o nazwie *VIP2*. Jeśli chcesz upewnić się, że cały ruch do portu *81* w *VIP2* jest równoważony między *myVM1* i *myVM2* na porcie *8181*, uruchom następujący skrypt programu PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

Możesz również zaktualizować moduł równoważenia obciążenia, aby używał innego adresu VIP. Na przykład po uruchomieniu poniższego polecenia programu PowerShell zmienisz zestaw równoważenia obciążenia na używanie adresu VIP o nazwie adresu VIP1:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Następne kroki

[Dzienniki Azure Monitor dla równoważenia obciążenia platformy Azure](load-balancer-monitor-log.md)

[Przegląd modułu równoważenia obciążenia połączonego z Internetem](load-balancer-internet-overview.md)

[Wprowadzenie do modułu równoważenia obciążenia połączonego z Internetem](load-balancer-get-started-internet-arm-ps.md)

[Omówienie usługi Virtual Network](../virtual-network/virtual-networks-overview.md)

[Interfejsy API REST Zastrzeżony adres IP](https://msdn.microsoft.com/library/azure/dn722420.aspx)
