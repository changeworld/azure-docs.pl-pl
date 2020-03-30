---
title: Tworzenie publicznej komunikacji równorzędnej usługi Azure ExpressRoute i zarządzanie nimi
description: Dowiedz się więcej o publicznej komunikacji równorzędnej platformy Azure i zarządzanie nimi
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8c1afac834fb9abb2cbf82f16f046a1624b251f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481137"
---
# <a name="create-and-manage-expressroute-public-peering"></a>Tworzenie publicznej komunikacji równorzędnej usługi ExpressRoute i zarządzanie nim

> [!div class="op_single_selector"]
> * [Artykuł — komunikacja równorzędna dla społeczeństwa](about-public-peering.md)
> * [Wideo - Komunikacja publiczna](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Artykuł — komunikacja równorzędna firmy Microsoft](expressroute-circuit-peerings.md#microsoftpeering)
>

Ten artykuł ułatwia tworzenie i zarządzanie publiczną konfiguracją routingu komunikacji równorzędnej dla obwodu usługi ExpressRoute. Można również sprawdzić stan, zaktualizować lub usunąć i anulować komunikację równorzędową. Ten artykuł dotyczy obwodów Menedżera zasobów utworzonych przed przestarzałym przestarzałym komunikacją równorzędną. Jeśli masz wcześniej istniejący obwód (utworzony przed przestarzałym przestarzałym publicznym układem równorzędnym), możesz zarządzać i konfigurować komunikację równorzędną między publiczną za pomocą [platformy Azure PowerShell,](#powershell) [interfejsu wiersza polecenia platformy Azure](#cli)i portalu [Azure.](#portal)

>[!NOTE]
>Komunikacja równorzędna publiczna jest przestarzała. Nie można utworzyć publicznego komunikacji równorzędnej w nowych obwodach usługi ExpressRoute. Jeśli masz nowy obwód usługi ExpressRoute, zamiast tego użyj [komunikacji równorzędnej firmy Microsoft](expressroute-circuit-peerings.md#microsoftpeering) dla swoich usług platformy Azure.
>

## <a name="connectivity"></a>Łączność

Łączność jest zawsze inicjowana z sieci WAN do usług platformy Microsoft Azure. Usługi platformy Microsoft Azure nie będą mogły inicjować połączeń z siecią za pośrednictwem tej domeny routingu. Jeśli twój obwód usługi ExpressRoute jest włączony dla publicznej komunikacji równorzędnej platformy Azure, można uzyskać dostęp do [publicznych zakresów adresów IP używanych na platformie Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) za pomocą obwodu.

Po włączeniu komunikacji równorzędnej publicznej można połączyć się z większością usług platformy Azure. Nie zezwalamy na selektywne wybieranie usług, do których reklamujemy trasy.

* Usługi, takie jak Usługa Azure Storage, bazy danych SQL i witryny sieci Web są oferowane na publicznych adresach IP.
* Za pośrednictwem domeny routingu komunikacji równorzędnej publicznej można prywatnie łączyć się z usługami hostowanymi na publicznych adresach IP, w tym z adresami VIP usług w chmurze.
* Możesz połączyć publiczną domenę komunikacji równorzędnej z witryną DMZ i połączyć się ze wszystkimi usługami platformy Azure na ich publicznych adresach IP z sieci WAN bez konieczności łączenia się za pośrednictwem Internetu.

## <a name="services"></a><a name="services"></a>Usług

W tej sekcji przedstawiono usługi dostępne za publiczne komunikacji równorzędnej. Ponieważ komunikacja równorzędna publicznych jest przestarzała, nie ma planu dodawania nowych lub dodatkowych usług do komunikacji równorzędnej publicznej. Jeśli używasz komunikacji równorzędnej publicznej, a usługa, której chcesz używać, jest obsługiwana tylko przez komunikację równorzędną firmy Microsoft, musisz przełączyć się na komunikację równorzędną firmy Microsoft. Zobacz [Komunikacja równorzędna firmy Microsoft,](expressroute-faqs.md#microsoft-peering) aby uzyskać listę obsługiwanych usług.

**Obsługiwane:**

* Power BI
* Większość usług platformy Azure są obsługiwane. Sprawdź bezpośrednio z usługą, której chcesz użyć do zweryfikowania pomocy technicznej.

**Nie jest obsługiwane:**
  * CDN
  * Azure Front Door
  * Serwer uwierzytelniania wieloskładnikowego (starsza wersja)
  * Traffic Manager

Aby sprawdzić dostępność dla określonej usługi, można sprawdzić dokumentację dla tej usługi, aby sprawdzić, czy istnieje zarezerwowany zakres opublikowany dla tej usługi. Następnie można wyszukać zakresy adresów IP usługi docelowej i porównać z zakresami wymienionymi w [plikach XML zakresów adresów IP platformy Azure i tagów usług — Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519). Alternatywnie można otworzyć bilet pomocy technicznej dla danej usługi w celu wyjaśnienia.

## <a name="peering-comparison"></a><a name="compare"></a>Porównanie komunikacji równorzędnej

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Publiczna komunikacja równorzędna platformy Azure ma 1 adres IP NAT skojarzony z każdą sesją BGP. W przypadku więcej niż 2 adresów IP NAT przejdź do komunikacji równorzędnej firmy Microsoft. Komunikacja równorzędna firmy Microsoft umożliwia konfigurowanie własnych alokacji translatora i odpowiedzi na usługi, a także używanie filtrów tras do selektywnych reklam prefiksów. Aby uzyskać więcej informacji, zobacz [Przechodzenie do komunikacji równorzędnej firmy Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).
>

## <a name="custom-route-filters"></a>Niestandardowe filtry tras

Można zdefiniować niestandardowe filtry tras w sieci, aby korzystać tylko z tras, których potrzebujesz. Szczegółowe informacje na temat konfiguracji routingu można znaleźć na stronie [Routing.](expressroute-routing.md)

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Kroki programu Azure PowerShell


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

Ponieważ komunikacja równorzędna publiczna jest przestarzała, nie można skonfigurować komunikacji równorzędnej w nowym obwodzie usługi ExpressRoute.

1. Sprawdź, czy masz obwód usługi ExpressRoute, który jest aprowizowany, a także włączony. Skorzystaj z następującego przykładu:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Odpowiedź jest podobna do poniższego przykładu:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                      "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
2. Skonfiguruj publiczną konfigurację równorzędną Azure dla obwodu. Przed kontynuowaniem upewnij się, że masz następujące informacje.

   * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4.
   * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Opcjonalnie:
   * Skrót MD5, jeśli zdecydujesz się go użyć.

   Uruchom poniższy przykład, aby skonfigurować publiczną komunikację równorzędną platformy Azure dla obwodu

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Jeśli zdecydujesz się użyć skrótu MD5, użyj następującego przykładu:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Pamiętaj, aby określić numer AS jako ASN komunikacji równorzędnej, a nie ASN klienta.
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>Aby uzyskać szczegółowe informacje dotyczące publicznej komunikacji równorzędnej platformy Azure

Szczegóły konfiguracji można uzyskać przy użyciu następującego polecenia cmdlet:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Aby zaktualizować konfigurację publicznej komunikacji równorzędnej Azure

Dowolną część konfiguracji można zaktualizować, korzystając z poniższego przykładu. W tym przykładzie identyfikator sieci VLAN obwodu jest aktualizowany z 200 do 600.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Aby usunąć publiczną komunikację równorzędną Azure

Konfigurację komunikacji równorzędnej można usunąć, uruchamiając następujący przykład:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Kroki interfejsu wiersza polecenia platformy Azure


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. Sprawdź obwód usługi ExpressRoute, aby upewnić się, że jest aprowizowana, a także włączona. Skorzystaj z następującego przykładu:

   ```azurecli-interactive
   az network express-route list
   ```

   Odpowiedź jest podobna do poniższego przykładu:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

2. Skonfiguruj publiczną konfigurację równorzędną Azure dla obwodu. Przed kontynuowaniem upewnij się, że masz następujące informacje.

   * Podsieć /30 dla połączenia podstawowego. Musi to być prawidłowy publiczny prefiks IPv4.
   * Podsieć /30 dla połączenia dodatkowego. Musi to być prawidłowy publiczny prefiks IPv4.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * **Opcjonalnie -** Skrót MD5, jeśli zdecydujesz się go użyć.

   Uruchom poniższy przykład, aby skonfigurować publiczne komunikacji równorzędnej platformy Azure dla obwodu:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Jeśli zdecydujesz się użyć skrótu MD5, użyj następującego przykładu:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Pamiętaj, aby określić numer AS jako ASN komunikacji równorzędnej, a nie ASN klienta.

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>Aby wyświetlić szczegóły dotyczące publicznej komunikacji równorzędnej Azure

Szczegóły konfiguracji można uzyskać, korzystając z następującego przykładu:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

Dane wyjściowe są podobne do poniższego przykładu:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Aby zaktualizować konfigurację publicznej komunikacji równorzędnej Azure

Dowolną część konfiguracji można zaktualizować, korzystając z poniższego przykładu. W tym przykładzie identyfikator sieci VLAN obwodu jest aktualizowany z 200 do 600.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Aby usunąć publiczną komunikację równorzędną Azure

Konfigurację komunikacji równorzędnej można usunąć, uruchamiając następujący przykład:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Kroki portalu platformy Azure

Aby skonfigurować komunikację równorzędnych, należy użyć kroki programu PowerShell lub interfejsu wiersza polecenia zawarte w tym artykule. Aby zarządzać komunikacją równorzędną, możesz użyć poniższych sekcji. Aby uzyskać odwołanie, te kroki wyglądają podobnie do zarządzania [komunikacją równorzędną firmy Microsoft w portalu](expressroute-howto-routing-portal-resource-manager.md#msft).

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>Aby wyświetlić szczegóły dotyczące publicznej komunikacji równorzędnej Azure

Wyświetl właściwości komunikacji równorzędnej platformy Azure, wybierając komunikację równorzędową w portalu.

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>Aby zaktualizować konfigurację publicznej komunikacji równorzędnej Azure

Wybierz wiersz do komunikacji równorzędnej, a następnie zmodyfikuj właściwości komunikacji równorzędnej.

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>Aby usunąć publiczną komunikację równorzędną Azure

Usuń konfigurację komunikacji równorzędnej, wybierając ikonę usuwania.

## <a name="next-steps"></a>Następne kroki

Następny [krok: Połącz sieć wirtualną z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Więcej informacji na temat przepływów pracy usługi ExpressRoute znajduje się w artykule [ExpressRoute workflows](expressroute-workflows.md) (Przepływy pracy usługi ExpressRoute).
* Aby uzyskać więcej informacji o komunikacji równorzędnej obwodu, zobacz artykuł [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute).
* Więcej informacji na temat pracy z sieciami wirtualnymi znajduje się w artykule [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Omówienie sieci wirtualnych).