---
title: Wdrażanie aplikacji podwójnego stosu IPv6 w sieci wirtualnej platformy Azure — szablon Menedżera zasobów
titlesuffix: Azure Virtual Network
description: W tym artykule pokazano, jak wdrożyć aplikację podwójnego stosu IPv6 ze standardowym modułem równoważenia obciążenia w sieci wirtualnej platformy Azure przy użyciu szablonów maszyn wirtualnych usługi Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 1f9531b5d1decfd462a82b9d389c5af519591c83
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420644"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---template"></a>Wdrażanie aplikacji podwójnego stosu IPv6 w sieci wirtualnej platformy Azure — szablon

Ten artykuł zawiera listę zadań konfiguracyjnych IPv6 z częścią szablonu maszyny Wirtualnej Usługi Azure Resource Manager, która ma zastosowanie do. Użyj szablonu opisanego w tym artykule, aby wdrożyć aplikację z dwoma stosami (IPv4 + IPv6) przy użyciu standardowego modułu równoważenia obciążenia na platformie Azure, która zawiera sieć wirtualną z dwoma stosami z podsieciami IPv4 i IPv6, standardowy moduł równoważenia obciążenia z dwoma konfiguracjami (IPv4 + IPv6), maszyny wirtualne z kartami sieciowymi z podwójną konfiguracją IP, grupę zabezpieczeń sieciowych i publiczne adresy IP. 

## <a name="required-configurations"></a>Wymagane konfiguracje

Wyszukaj sekcje szablonu w szablonie, aby zobaczyć, gdzie powinny wystąpić.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>IPv6 addressSpace dla sieci wirtualnej

Sekcja Szablon do dodania:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Podsieć IPv6 w obrębie adresu sieci wirtualnej IPv6Space

Sekcja Szablon do dodania:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>Konfiguracja IPv6 dla karty sieciowej

Sekcja Szablon do dodania:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>Reguły sieciowej grupy zabezpieczeń IPv6

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>Konfiguracja warunkowa

Jeśli używasz wirtualnego urządzenia sieciowego, dodaj trasy IPv6 w tabeli tras. W przeciwnym razie ta konfiguracja jest opcjonalna.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>Konfiguracja opcjonalna

### <a name="ipv6-internet-access-for-the-virtual-network"></a>Dostęp do Internetu IPv6 dla sieci wirtualnej

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>Publiczne adresy IP iPv6

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6 Front end dla modułu równoważenia obciążenia

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>P.4d.

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>Reguły modułu równoważenia obciążenia IPv6 do kojarzenia portów przychodzących i wychodzących

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>Przykładowy szablon maszyny Wirtualnej JSON
Aby wdrożyć aplikację podwójnego stosu IPv6 w sieci wirtualnej platformy Azure przy użyciu szablonu Usługi Azure Resource Manager, wyświetl przykładowy szablon [tutaj](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-stdlb/).

## <a name="next-steps"></a>Następne kroki

Szczegółowe informacje na temat cen [publicznych adresów IP,](https://azure.microsoft.com/pricing/details/ip-addresses/) [przepustowości sieci](https://azure.microsoft.com/pricing/details/bandwidth/)lub [modułu równoważenia obciążenia](https://azure.microsoft.com/pricing/details/load-balancer/)można znaleźć.
