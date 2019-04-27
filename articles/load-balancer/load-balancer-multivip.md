---
title: Wiele adresów VIP dla usługi w chmurze
titlesuffix: Azure Load Balancer
description: Omówienie obejmujące i sposobu ustawiania wielu adresów VIP w usłudze w chmurze
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: bf5721e206316a4ce576253743e9ac65de47094a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60591755"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Konfigurowanie wielu adresów VIP dla usługi w chmurze

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Mieli dostęp do usług platformy Azure w chmurze za pośrednictwem publicznej sieci Internet, przy użyciu podanego adresu IP przez platformę Azure. Ten publiczny adres IP jest określany jako adres VIP (wirtualny adres IP), ponieważ prowadzi do modułu równoważenia obciążenia platformy Azure, a nie maszyna wirtualna (VM) wystąpień usługi w chmurze. Możesz uzyskać dostęp dowolne wystąpienie maszyny Wirtualnej w ramach usługi w chmurze, korzystając z pojedynczego wirtualnego adresu IP.

Jednak istnieją scenariusze, w których będziesz potrzebował więcej niż jeden adres VIP jako wpis punktu do tej samej usługi w chmurze. Na przykład usługi w chmurze może hostowanie wielu witryn internetowych, które wymagają łączności SSL przy użyciu domyślnego portu 443, ponieważ każda lokacja jest hostowana dla różnych klientów lub dzierżawców. W tym scenariuszu musisz mieć inny publiczny dostępny adres IP dla każdej witryny sieci Web. Poniższy diagram przedstawia typowy wielodostępnych hostingu potrzebuje wiele certyfikatów SSL na tym samym publicznym porcie.

![Obsługa wielu adresów VIP SSL scenariusza](./media/load-balancer-multivip/Figure1.png)

W powyższym przykładzie wszystkie adresy VIP używać tego samego publicznego portu (443) i ruch jest przekierowywany do jednego lub więcej ze zrównoważonym obciążeniem: maszyny wirtualne na unikatowy port prywatny dla wewnętrznego adresu IP usługa w chmurze hostująca wszystkie witryny sieci Web.

> [!NOTE]
> Obsługuje wielu odbiorników grup dostępności funkcji SQL AlwaysOn na tym samym zestawie maszyn wirtualnych w innej sytuacji wymagających wielu adresów VIP.

Adresy VIP są dynamiczne domyślnie, co oznacza, że rzeczywisty adres IP przypisany do usługi w chmurze mogą się zmieniać wraz z upływem czasu. Aby zapobiec, aby zapobiec, możesz zarezerwować adres VIP dla usługi. Aby dowiedzieć się więcej o zarezerwowanych adresów VIP, zobacz [zastrzeżony adres IP publicznego](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Zobacz [cennik adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses/) informacji na temat cen na poziomie adresów VIP i zarezerwowane adresy IP.

Można zweryfikować adresy VIP używany przez usługi w chmurze przy użyciu programu PowerShell, a także Dodaj i Usuń adresów VIP, skojarzyć adres VIP do punktu końcowego i konfigurowanie równoważenia obciążenia na określonych adresów VIP.

## <a name="limitations"></a>Ograniczenia

W tej chwili funkcji wielu adresów VIP jest ograniczony do następujących scenariuszy:

* **Tylko IaaS**. Obsługa wielu adresów VIP można włączyć tylko dla usług w chmurze, które zawierają maszyny wirtualne. Nie można używać wielu adresów VIP scenariusze dla rozwiązania PaaS z wystąpień roli.
* **Tylko w programie PowerShell**. Obsługa wielu adresów VIP można zarządzać tylko przy użyciu programu PowerShell.

Te ograniczenia są tymczasowe i mogą ulec zmianie w dowolnym momencie. Upewnij się, że Odwiedzaj tę stronę, aby sprawdzić przyszłe zmiany.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Jak dodać adres VIP usługi w chmurze
Aby dodać adres VIP do usługi, uruchom następujące polecenie programu PowerShell:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

To polecenie wyświetla się wynik podobny do poniższego przykładu:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Jak usunąć adres VIP z usługi w chmurze
Aby usunąć adres VIP, dodać do swojej usługi w powyższym przykładzie, uruchom następujące polecenie programu PowerShell:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Adres VIP można usuwać tylko, jeśli go nie ma punktów końcowych skojarzonych z nim.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Jak pobrać informacje VIP z usługi w chmurze
Aby pobrać adresy VIP skojarzony z usługą w chmurze, uruchom następujący skrypt programu PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Skrypt wyświetli się wynik podobny do poniższego przykładu:

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

W tym przykładzie usługa w chmurze ma 3 adresów VIP:

* **Adres_vip1** jest domyślny adres VIP, wiesz, że ponieważ IsDnsProgrammedName jest ustawiany na wartość true.
* **Adres_vip2** i **Vip3** nie są używane, mają adresy IP. One będzie można używać tylko w przypadku skojarzenia punktu końcowego do adresu VIP.

> [!NOTE]
> Twoja subskrypcja zostanie naliczona tylko za dodatkowe adresy VIP gdy są one skojarzone z punktem końcowym. Aby uzyskać więcej informacji na temat cen, zobacz [cennik adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Jak skojarzyć adres VIP do punktu końcowego

Aby skojarzyć Wirtualnym adresem IP będącym do punktu końcowego usługi w chmurze, uruchom następujące polecenie programu PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

Polecenie spowoduje utworzenie punktu końcowego połączone do adresu VIP o nazwie *adres_vip2* na porcie *80*i dołącza go do maszyny Wirtualnej o nazwie *myVM1* w usłudze w chmurze o nazwie *Moja_usługa* przy użyciu *TCP* na porcie *8080*.

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

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Jak włączyć równoważenia obciążenia na określonych adresów VIP

Pojedynczy adres VIP można skojarzyć z wieloma maszynami wirtualnymi na potrzeby równoważenia obciążenia. Na przykład masz usługę w chmurze o nazwie *Moja_usługa*i dwie maszyny wirtualne o nazwie *myVM1* i *myVM2*. I usługi w chmurze ma wiele adresów VIP, jeden z nich o nazwie *adres_vip2*. Jeśli chcesz upewnić się, że cały ruch na porcie *81* na *adres_vip2* jest równomiernie rozkładane między *myVM1* i *myVM2* na porcie *8181* , uruchom następujący skrypt programu PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

Można również zaktualizować, aby użyć innego adresu VIP modułu równoważenia obciążenia. Na przykład po uruchomieniu poniższego polecenia programu PowerShell, zostanie zmieniona zestawu do użycia adresu VIP o nazwie adresu Vip1 równoważenia obciążenia:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Następne kroki

[Usługa Azure Monitor dzienniki Równoważenie obciążenia Azure](load-balancer-monitor-log.md)

[Omówienie modułu równoważenia obciążenia dostępnego z Internetu](load-balancer-internet-overview.md)

[Wprowadzenie do równoważenia obciążenia połączonego z Internetem](load-balancer-get-started-internet-arm-ps.md)

[Omówienie usługi Virtual Network](../virtual-network/virtual-networks-overview.md)

[Interfejsy API REST zastrzeżonego adresu IP](https://msdn.microsoft.com/library/azure/dn722420.aspx)
