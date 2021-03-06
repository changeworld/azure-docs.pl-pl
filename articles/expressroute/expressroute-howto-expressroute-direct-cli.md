---
title: 'Azure ExpressRoute: Konfigurowanie direct usługi ExpressRoute Direct: CLI'
description: Ten artykuł ułatwia konfigurowanie usługi ExpressRoute Direct przy użyciu interfejsu wiersza polecenia platformy Azure
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: cherylmc
ms.openlocfilehash: dcca1417aec52fb4bf99d5c480d81995154a68b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481980"
---
# <a name="configure-expressroute-direct-by-using-the-azure-cli"></a>Konfigurowanie funkcji Direct usługi ExpressRoute przy użyciu interfejsu wiersza polecenia platformy Azure

Za pomocą usługi Azure ExpressRoute Direct można łączyć się bezpośrednio z globalną siecią firmy Microsoft w lokalizacjach komunikacji równorzędnej strategicznie rozproszonych na całym świecie. Aby uzyskać więcej informacji, zobacz [Temat Usługi ExpressRoute Direct Connect](expressroute-erdirect-about.md).

## <a name="create-the-resource"></a><a name="resources"></a>Tworzenie zasobu

1. Zaloguj się na platformie Azure i wybierz subskrypcję zawierającą usługę ExpressRoute. Zasób Direct usługi ExpressRoute i obwody usługi ExpressRoute muszą być w tej samej subskrypcji. W wierszu polecenia platformy Azure uruchom następujące polecenia:

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

2. Ponowne zarejestrowanie subskrypcji w witrynie Microsoft.Network w celu uzyskania dostępu do interfejsów API expressrouteportslocation i expressrouteport

   ```azurecli
   az provider register --namespace Microsoft.Network
   ```
3. Wyświetl listę wszystkich lokalizacji, w których jest obsługiwana usługa Direct usługi ExpressRoute:
    
   ```azurecli
   az network express-route port location list
   ```

   **Przykładowe dane wyjściowe**
  
   ```output
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
4. Określ, czy jedna z lokalizacji wymienionych w poprzednim kroku ma dostępną przepustowość:

   ```azurecli
   az network express-route port location show -l "Equinix-Ashburn-DC2"
   ```

   **Przykładowe dane wyjściowe**

   ```output
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
5. Utwórz zasób Direct usługi ExpressRoute, który jest oparty na lokalizacji wybranej w poprzednich krokach.

   Program ExpressRoute Direct obsługuje hermetyzację QinQ i Dot1Q. W przypadku wybrania opcji QinQ każdy obwód usługi ExpressRoute jest dynamicznie przypisywany znacznikowi S i jest unikatowy w całym zasobie Direct usługi ExpressRoute. Każdy znacznik C w obwodzie musi być unikatowy w obwodzie, ale nie w zasobie Direct usługi ExpressRoute.  

   Jeśli wybierzesz hermetyzację Dot1Q, musisz zarządzać unikatowością tagu C (VLAN) w całym zasobie Direct usługi ExpressRoute.  

   > [!IMPORTANT]
   > ExpressRoute Direct może być tylko jeden typ hermetyzacji. Nie można zmienić typu hermetyzacji po utworzeniu zasobu Direct usługi ExpressRoute.
   > 
 
   ```azurecli
   az network express-route port create -n $name -g $RGName --bandwidth 100 gbps  --encapsulation QinQ | Dot1Q --peering-location $PeeringLocationName -l $AzureRegion 
   ```

   > [!NOTE]
   > Można również ustawić atrybut **Hermetacje** na **Dot1Q**. 
   >

   **Przykładowe dane wyjściowe**

   ```output
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

## <a name="change-adminstate-for-links"></a><a name="state"></a>Zmienianie stanu administracyjnego dla łączy

Ten proces służy do przeprowadzania testu warstwy 1. Upewnij się, że każde połączenie krzyżowe jest poprawnie załatane do każdego routera w portach podstawowych i pomocniczych.

1. Ustaw łącza do **Włączone**. Powtórz ten krok, aby ustawić każde **łącze**na Włączone .

   Links[0] jest portem podstawowym, a Links[1] jest portem pomocniczym.

   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[0].adminState="Enabled"
   ```
   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[1].adminState="Enabled"
   ```
   **Przykładowe dane wyjściowe**

   ```output
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

   Użyj tej samej procedury, `AdminState = "Disabled"`aby obniżyć porty za pomocą programu .

## <a name="create-a-circuit"></a><a name="circuit"></a>Tworzenie obwodu

Domyślnie można utworzyć 10 obwodów w subskrypcji zawierającej zasób Direct usługi ExpressRoute. Pomoc techniczna firmy Microsoft może zwiększyć domyślny limit. Użytkownik jest odpowiedzialny za śledzenie aprowizacji i wykorzystania przepustowości. Przepustowość aprowizacji jest sumą przepustowości wszystkich obwodów w zasobie Direct usługi ExpressRoute. Wykorzystana przepustowość jest fizycznym użyciem podstawowych interfejsów fizycznych.

Dodatkowe przepustowości obwodów w uzyciu Usługi ExpressRoute Direct można używać tylko do obsługi scenariuszy opisanych w tym miejscu. Przepustowość wynosi 40 Gb/s i 100 Gb/s.

**SkuTier** może być lokalny, standardowy lub premium.

**SkuFamily** musi być MeteredData tylko jako nieograniczony nie jest obsługiwany w użyczarka expressroute direct.
Tworzenie obwodu w zasobie Direct usługi ExpressRoute:

  ```azurecli
  az network express-route create --express-route-port "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct" -n "Contoso-Direct-ckt" -g "Contoso-Direct-rg" --sku-family MeteredData --sku-tier Standard --bandwidth 100 Gbps
  ```

  Inne przepustowości obejmują 5 Gb/s, 10 Gb/s i 40 Gb/s.

  **Przykładowe dane wyjściowe**

  ```output
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

Aby uzyskać więcej informacji na temat usługi ExpressRoute Direct, zobacz [omówienie](expressroute-erdirect-about.md).
