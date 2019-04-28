---
title: Wdróż aplikację podwójnego stosu protokołu IPv6 w sieci wirtualnej platformy Azure — Menedżer zasobów szablonu (wersja zapoznawcza)
titlesuffix: Azure Virtual Network
description: W tym artykule przedstawiono sposób wdrażania aplikacji podwójnego stosu protokołu IPv6 w sieci wirtualnej platformy Azure za pomocą szablonów maszyn wirtualnych platformy Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: ae90bc4a12763803f38224d917c4644a68ae7d6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62131031"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---template-preview"></a>Wdrażanie aplikacji podwójnego stosu protokołu IPv6 na platformie Azure — szablon (wersja zapoznawcza)

Ten artykuł zawiera listę zadań konfiguracji protokołu IPv6 przy użyciu części szablonu maszyny Wirtualnej platformy Azure Resource Manager, który dotyczy. Szablon opisanych w tym artykule umożliwia wdrażanie aplikacji na platformie Azure, która obejmuje podwójnego stosu sieci wirtualnej z podsieci IPv4 i IPv6, moduł równoważenia obciążenia z konfiguracji frontonu podwójnego (IPv4 + IPv6), maszyny wirtualne z kartami sieciowymi, które mają dwuwarstwowa protokołu IP podwójny stos (IPv4 + IPv6) konfigurację sieciową grupę zabezpieczeń i publicznych adresów IP. 

## <a name="required-configurations"></a>Wymagane konfiguracje

Wyszukaj sekcje szablonu w szablonie, aby zobaczyć, których powinien wystąpić.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>Element addressSpace IPv6 w sieci wirtualnej

Dodaj sekcję szablonu:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Podsieci IPv6 w element addressSpace sieci wirtualnej IPv6

Dodaj sekcję szablonu:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>Konfiguracja protokołu IPv6 dla karty Sieciowej

Dodaj sekcję szablonu:
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

### <a name="ipv6-network-security-group-nsg-rules"></a>Reguły Sieciowej grupy zabezpieczeń sieci IPv6

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

## <a name="conditional-configuration"></a>Konfiguracja warunkowe

Jeśli używasz sieciowego urządzenia wirtualnego należy dodać trasy protokołu IPv6 w tabeli tras. W przeciwnym razie ta konfiguracja jest opcjonalne.

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

### <a name="ipv6-public-ip-addresses"></a>Protokół IPv6 publicznych adresów IP

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>Protokół IPv6 frontonu modułu równoważenia obciążenia

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>Pula adresów IPv6 zaplecza dla modułu równoważenia obciążenia

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

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>Reguły modułu równoważenia skojarzyć porty przychodzące i wychodzące obciążenia protokołu IPv6

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

## <a name="sample-vm-template-json"></a>Przykładowy kod JSON szablonu maszyny Wirtualnej
Kliknij przycisk [tutaj](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/) wdrożyć aplikację podwójnego stosu protokołu IPv6 w sieci wirtualnej platformy Azure przy użyciu szablonu usługi Azure Resource Manager.

## <a name="next-steps"></a>Kolejne kroki

Można znaleźć szczegółowe informacje na temat cen [publiczne adresy IP](https://azure.microsoft.com/pricing/details/ip-addresses/), [przepustowość sieci](https://azure.microsoft.com/pricing/details/bandwidth/), lub [modułu równoważenia obciążenia](https://azure.microsoft.com/pricing/details/load-balancer/).
