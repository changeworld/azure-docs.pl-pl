---
title: Wiele adresów VIP dla usługi w chmurze
description: Omówienie wieloma wirtualnymi adresami IP oraz jak ustawić wiele adresów VIP na usługi w chmurze
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 85f6d26a-3df5-4b8e-96a1-92b2793b5284
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: b6b7b0b2d7a7f33facaf72bbd2d7937364770673
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855201"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Konfigurowanie wielu adresów VIP dla usługi w chmurze

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Usługi w chmurze Azure mogą korzystać za pośrednictwem publicznej sieci Internet, za pomocą adresu IP podał Azure. Ten publiczny adres IP jest określany jako VIP (wirtualny adres IP), ponieważ jest połączony z usługą równoważenia obciążenia Azure, a nie maszyna wirtualna (VM) wystąpień w ramach usługi w chmurze. Można uzyskać dostępu do dowolnego wystąpienia maszyny Wirtualnej w ramach usługi w chmurze przy użyciu pojedynczego wirtualnego adresu IP.

Istnieją jednak scenariuszy, w których może wymagać więcej niż jeden punkt VIP jako wpis do tej samej usługi w chmurze. Na przykład usługi w chmurze może hostować wiele witryn sieci Web, które wymagają łączności SSL przy użyciu domyślnego portu 443, ponieważ każda lokacja jest hostowana dla różnych klientów lub dzierżawców. W tym scenariuszu musisz mieć inny publiczny połączonej adres IP dla każdej witryny sieci Web. Na poniższym diagramie przedstawiono typowe wielodostępne hostingu potrzebuje wiele certyfikatów SSL na ten sam port publiczny.

![Scenariusz Multi VIP SSL](./media/load-balancer-multivip/Figure1.png)

W powyższym przykładzie wszystkie wirtualne adresy IP Użyj tego samego portu publicznego (port 443), a ruch jest przekierowywany do jednej lub maszyn wirtualnych na unikatowy port prywatny dla wewnętrznego adresu IP usługi w chmurze hosting wszystkich witryn sieci Web o zrównoważonym obciążeniu więcej.

> [!NOTE]
> Innej sytuacji wymagających wielu adresy VIP obsługuje wiele odbiorniki grupy dostępności funkcji SQL AlwaysOn na ten sam zestaw maszyn wirtualnych.

Wirtualne adresy IP są dynamiczne domyślnie, co oznacza, że rzeczywista adresu IP przypisanego do usługi w chmurze mogą się zmieniać wraz z upływem czasu. Aby zapobiec który, może zarezerwować adresu VIP dla usługi. Aby dowiedzieć się więcej na temat zarezerwowane wirtualne adresy IP, zobacz [zastrzeżone publicznego adresu IP](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Zobacz [cennik adres IP](https://azure.microsoft.com/pricing/details/ip-addresses/) Aby uzyskać informacje o cenach na adresy VIP i zarezerwowane adresy IP.

Można Sprawdź wirtualne adresy IP używane przez usługi chmury, za pomocą programu PowerShell, a także dodać i usunąć adresy VIP, skojarzyć VIP do punktu końcowego i konfigurowanie równoważenia na określonego adresu VIP obciążenia.

## <a name="limitations"></a>Ograniczenia

W tej chwili funkcje wielu adresów VIP są ograniczone do następujących scenariuszy:

* **Tylko IaaS**. Można włączyć tylko Multi VIP dla usług w chmurze, które zawierają maszyny wirtualne. Nie można użyć adresu VIP wielu wystąpień roli PaaS scenariuszy.
* **PowerShell tylko**. Wiele adresów VIP może zarządzać tylko przy użyciu programu PowerShell.

Ograniczenia te są tymczasowe i mogą ulec zmianie w dowolnym momencie. Upewnij się, że ponownie tę stronę, aby sprawdzić przyszłe zmiany.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Jak dodać adresu VIP usługi w chmurze
Aby dodać adresu VIP do usługi, uruchom następujące polecenie programu PowerShell:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

To polecenie wyświetla wynik jest podobny do poniższego przykładu:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Jak usunąć wirtualnego adresu IP z usługi w chmurze
Aby usunąć adres VIP dodane do usługi w powyższym przykładzie, uruchom następujące polecenie programu PowerShell:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Można usunąć adresu VIP, jeśli go nie ma punktów końcowych skojarzonych z nim.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Jak pobrać VIP informacji z usługi w chmurze
Aby pobrać wirtualne adresy IP skojarzone z usługą w chmurze, uruchom następujący skrypt programu PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Skrypt zawiera wynik jest podobny do poniższego przykładu:

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

W tym przykładzie usługa w chmurze ma 3 wirtualne adresy IP:

* **Adres Vip1** jest domyślny adres VIP, wiesz, że ponieważ IsDnsProgrammedName jest ustawiany na wartość true.
* **Vip2** i **Vip3** nie są używane jako nie mają adresy IP. One będzie można używać tylko jeśli skojarzyć punkt końcowy do adresu VIP.

> [!NOTE]
> Subskrypcją będzie obciążana dla dodatkowe wirtualne adresy IP, gdy są one powiązane z punktem końcowym. Aby uzyskać więcej informacji o cenach, zobacz [cennik adres IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Jak skojarzyć VIP do punktu końcowego

Aby skojarzyć adresów VIP na punkt końcowy usługi w chmurze, uruchom następujące polecenie programu PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

Polecenie tworzy punkt końcowy powiązany adres VIP o nazwie *Vip2* na porcie *80*i łączy go z maszyny Wirtualnej o nazwie *myVM1* w usłudze w chmurze o nazwie *Moja_usługa* przy użyciu *TCP* na porcie *8080*.

Aby sprawdzić konfigurację, uruchom następujące polecenie programu PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Dane wyjściowe wygląda podobnie do poniższego przykładu:

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

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Jak włączyć równoważenia na określonego adresu VIP obciążenia

Pojedynczego wirtualnego adresu IP można skojarzyć z wieloma maszynami wirtualnymi na potrzeby równoważenia obciążenia. Przykładowo, jeśli masz usługi w chmurze o nazwie *Moja_usługa*i dwie maszyny wirtualne o nazwie *myVM1* i *myVM2*. I usługi w chmurze ma wiele adresów VIP, jeden z nich o nazwie *Vip2*. Jeśli chcesz upewnić się, że cały ruch do portu *81* na *Vip2* jest rozmieszczana między *myVM1* i *myVM2* na porcie *8181* , uruchom następujący skrypt programu PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

Należy również zaktualizować przez moduł równoważenia obciążenia, aby użyć innego adresu VIP. Na przykład po uruchomieniu poniższego polecenia programu PowerShell ulegnie zmianie zestawu do używania adresu VIP o nazwie adresu Vip1 równoważenia obciążenia:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Następne kroki

[Analiza dzienników Azure Równoważenie obciążenia](load-balancer-monitor-log.md)

[Omówienie usługi równoważenia obciążenia połączonej Internet](load-balancer-internet-overview.md)

[Przed rozpoczęciem pracy internetowy modułu równoważenia obciążenia](load-balancer-get-started-internet-arm-ps.md)

[Omówienie sieci wirtualnej](../virtual-network/virtual-networks-overview.md)

[Interfejsy API REST zastrzeżonego adresu IP](https://msdn.microsoft.com/library/azure/dn722420.aspx)
