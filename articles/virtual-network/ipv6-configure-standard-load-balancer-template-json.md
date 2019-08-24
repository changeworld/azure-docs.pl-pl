---
title: Wdrażanie aplikacji dwustosowej IPv6 w programie Azure Virtual Network — szablon usługi Resource Manager (wersja zapoznawcza)
titlesuffix: Azure Virtual Network
description: W tym artykule pokazano, jak wdrożyć aplikację dwustosową IPv6 za pomocą usługa Load Balancer w warstwie Standardowa w usłudze Azure Virtual Network przy użyciu szablonów maszyn wirtualnych Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 8f2c6bc7fb7ab0939da20932fd531c158549ce7a
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012848"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---template-preview"></a>Wdrażanie aplikacji podwójnego stosu IPv6 w usłudze Azure Virtual Network — szablon (wersja zapoznawcza)

Ten artykuł zawiera listę zadań konfiguracyjnych IPv6 z częścią szablonu maszyny wirtualnej Azure Resource Manager, która ma zastosowanie do. Użyj szablonu opisanego w tym artykule, aby wdrożyć aplikację podwójnego stosu (IPv4 + IPv6) przy użyciu usługa Load Balancer w warstwie Standardowa na platformie Azure, która obejmuje sieć wirtualną o podwójnej stercie z podsieciami IPv4 i IPv6, usługa Load Balancer w warstwie Standardowa za pomocą podwójnego frontonu (IPv4 + IPv6). konfiguracje, maszyny wirtualne z kartami sieciowymi, które mają dwie konfiguracje protokołu IP, sieciowe grupy zabezpieczeń i publiczne IP. 

## <a name="required-configurations"></a>Wymagane konfiguracje

Wyszukaj sekcje szablonu w szablonie, aby zobaczyć, gdzie powinny wystąpić.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>AddressSpace IPv6 dla sieci wirtualnej

Sekcja szablonu do dodania:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Podsieć IPv6 w sieci wirtualnej IPv6 addressSpace

Sekcja szablonu do dodania:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>Konfiguracja protokołu IPv6 dla karty sieciowej

Sekcja szablonu do dodania:
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

### <a name="ipv6-network-security-group-nsg-rules"></a>Reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) IPv6

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

Jeśli używasz sieciowego urządzenia wirtualnego, Dodaj trasy IPv6 w tabeli tras. W przeciwnym razie ta konfiguracja jest opcjonalna.

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

### <a name="ipv6-public-ip-addresses"></a>Publiczne adresy IP protokołu IPv6

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

### <a name="ipv6-front-end-for-load-balancer"></a>Fronton IPv6 dla Load Balancer

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>Pula adresów zaplecza IPv6 dla Load Balancer

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

## <a name="sample-vm-template-json"></a>Przykładowy kod JSON szablonu maszyny wirtualnej
Aby wdrożyć aplikację dwustosową protokołu IPv6 w usłudze Azure Virtual Network przy użyciu szablonu Azure Resource Manager, Wyświetl przykładowy szablon w [tym miejscu](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-stdlb/).

## <a name="next-steps"></a>Następne kroki

Możesz znaleźć szczegółowe informacje o cenach [publicznych adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses/), [przepustowości sieci](https://azure.microsoft.com/pricing/details/bandwidth/)lub [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
