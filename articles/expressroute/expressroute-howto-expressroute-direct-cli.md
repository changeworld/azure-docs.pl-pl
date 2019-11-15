---
title: 'Azure ExpressRoute: Konfigurowanie ExpressRoute Direct: interfejs wiersza polecenia'
description: W tym artykule opisano sposób konfigurowania ExpressRoute bezpośrednio przy użyciu interfejsu wiersza polecenia platformy Azure
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: cherylmc
ms.openlocfilehash: 6a17570a62728d5b4f9c99e3c4c939b5c77cb3df
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080211"
---
# <a name="configure-expressroute-direct-by-using-the-azure-cli"></a>Konfigurowanie usługi ExpressRoute Direct przy użyciu interfejsu wiersza polecenia platformy Azure

Bezpośrednia usługi ExpressRoute platformy Azure można użyć, aby bezpośrednio połączyć się z globalnej sieci firmy Microsoft w lokalizacji komunikacji równorzędnej, strategicznie rozproszonych na całym świecie. Aby uzyskać więcej informacji, zobacz [dotyczące usługi ExpressRoute bezpośrednie łączenie](expressroute-erdirect-about.md).

## <a name="resources"></a>Utwórz zasób

1. Logowanie do platformy Azure i wybierz subskrypcję, która zawiera usługi ExpressRoute. Zasób bezpośrednio z usługi ExpressRoute i obwodów usługi ExpressRoute musi być w tej samej subskrypcji. W interfejsie wiersza polecenia platformy Azure uruchom następujące polecenia:

   ```azurecli
   az login
   ```

   Sprawdź subskrypcje dla konta: 

   ```azurecli
   az account list 
   ```

   Wybierz subskrypcję, dla której chcesz utworzyć obwód usługi ExpressRoute:

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```

2. Wyświetl wszystkie lokalizacje, w którym są obsługiwane bezpośrednio z usługi ExpressRoute:
    
   ```azurecli
   az network express-route port location list
   ```

   **Przykładowe dane wyjściowe**
  
   ```azurecli
   [
   {
    "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
    "location": null,
    "name": "Equinix-Ashburn-DC2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA3",
    "location": null,
    "name": "Equinix-Dallas-DA3",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "111 8th Avenue, New York, NY 10011",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-New-York-NY5",
    "location": null,
    "name": "Equinix-New-York-NY5",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "11 Great Oaks Blvd, SV1, San Jose, CA 95119",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-SV1",
    "location": null,
    "name": "Equinix-San-Jose-SV1",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "2001 Sixth Ave., Suite 350, SE2, Seattle, WA 98121",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Seattle-SE2",
    "location": null,
    "name": "Equinix-Seattle-SE2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ]
   ```
3. Określ, czy jeden z lokalizacji wymienionych w poprzednim kroku ma dostępną przepustowość:

   ```azurecli
   az network express-route port location show -l "Equinix-Ashburn-DC2"
   ```

   **Przykładowe dane wyjściowe**

   ```azurecli
   {
   "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
   "availableBandwidths": [
    {
      "offerName": "100 Gbps",
      "valueInGbps": 100
    }
   ],
   "contact": "support@equinix.com",
   "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
   "location": null,
   "name": "Equinix-Ashburn-DC2",
   "provisioningState": "Succeeded",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ```
4. Utwórz zasób usługi bezpośrednio z usługi ExpressRoute, która zależy od lokalizacji, który został wybrany w poprzednich krokach.

   Usługa ExpressRoute bezpośrednio obsługuje QinQ i Dot1Q hermetyzacji. Jeśli wybierzesz QinQ, każdy obwód usługi ExpressRoute jest dynamicznie przypisywany S Tag i są unikatowe w obrębie całej zasobów bezpośrednio z usługi ExpressRoute. Każdy Tag języka C, w ramach obwodu muszą być unikatowe w ramach obwodu, ale nie dla zasobów ExpressRoute bezpośrednio.  

   Jeśli wybierzesz Dot1Q hermetyzacji, można zarządzać unikatowości tagu języka C (VLAN), w całego zasobu bezpośrednio z usługi ExpressRoute.  

   > [!IMPORTANT]
   > Bezpośrednie usługi ExpressRoute może być tylko jeden typ hermetyzacji. Nie można zmienić typ hermetyzacji, po utworzeniu zasobu bezpośrednio z usługi ExpressRoute.
   > 
 
   ```azurecli
   az network express-route port create -n $name -g $RGName --bandwidth 100 gbps  --encapsulation QinQ | Dot1Q --peering-location $PeeringLocationName -l $AzureRegion 
   ```

   > [!NOTE]
   > Możesz również ustawić **hermetyzacji** atrybutu **Dot1Q**. 
   >

   **Przykładowe dane wyjściowe**

   ```azurecli
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "02ee21fe-4223-4942-a6bc-8d81daabc94f",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }  
   ```

## <a name="state"></a>Zmiana AdminState dla łączy

Ten proces umożliwia przeprowadzanie testów warstwy 1. Upewnij się, że każdy obejmującej wiele połączeń jest zainstalowane odpowiednie poprawki do poszczególnych routerów w portów podstawowych i pomocniczych.

1. Ustaw łącza **włączone**. Powtórz ten krok, aby ustawić każdy link **włączone**.

   Łącza [0] jest podstawowy port i łącza [1] jest dodatkowych portów.

   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[0].adminState="Enabled"
   ```
   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[1].adminState="Enabled"
   ```
   **Przykładowe dane wyjściowe**

   ```azurecli
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "<resourceGUID>",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }
   ```

   Użyj tej samej procedury, aby w dół do portów przy użyciu `AdminState = "Disabled"`.

## <a name="circuit"></a>Tworzenie obwodu

Domyślnie można utworzyć 10 obwody w subskrypcji, która zawiera zasób bezpośrednio z usługi ExpressRoute. Microsoft Support można zwiększenia domyślnego limitu. Jesteś odpowiedzialny za śledzenie elastycznie i wykorzystywanych przepustowość. Elastycznie przepustowości to suma przepustowości wszystkich obwodów zasobu bezpośrednio z usługi ExpressRoute. Przepustowość wykorzystywanych jest użycie fizycznego bazowego interfejsy fizyczne.

Przepustowości obwodu dodatkowe na bezpośrednio ExpressRoute służy tylko do obsługi scenariuszy opisanych w tym miejscu. Wartości przepustowości to 40 GB/s i 100 GB/s.

**SkuTier** może być lokalna, standardowa lub Premium.

**SkuFamily** muszą być MeteredData tylko jako nieograniczone nie są obsługiwane w przypadku ExpressRoute Direct.
Utwórz obwód zasobu bezpośrednio z usługi ExpressRoute:

  ```azurecli
  az network express-route create --express-route-port "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct" -n "Contoso-Direct-ckt" -g "Contoso-Direct-rg" --sku-family MeteredData --sku-tier Standard --bandwidth 100 Gbps
  ```

  Inne przepustowości obejmują 5 GB/s, 10 GB/s i 40 GB/s.

  **Przykładowe dane wyjściowe**

  ```azurecli
  {
  "allowClassicOperations": false,
  "allowGlobalReach": false,
  "authorizations": [],
  "bandwidthInGbps": 100.0,
  "circuitProvisioningState": "Enabled",
  "etag": "W/\"<etagnumber>\"",
  "expressRoutePort": {
    "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
    "resourceGroup": "Contoso-Direct-rg"
  },
  "gatewayManagerEtag": "",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRouteCircuits/ERDirect-ckt-cli",
  "location": "westus",
  "name": "ERDirect-ckt-cli",
  "peerings": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "Contoso-Direct-rg",
  "serviceKey": "<serviceKey>",
  "serviceProviderNotes": null,
  "serviceProviderProperties": null,
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "MeteredData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "stag": null,
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits"
  }  
  ```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi ExpressRoute bezpośrednio zobacz [Przegląd](expressroute-erdirect-about.md).
