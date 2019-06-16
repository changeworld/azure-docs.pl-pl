---
title: Utwórz sieciową grupę zabezpieczeń (wersja klasyczna) przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i wdrożyć sieciową grupę zabezpieczeń (wersja klasyczna) przy użyciu programu PowerShell
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 86810b0d-0240-46a2-8548-fca22daa56f3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: d685f395aa25580c009fe3be660a8afc42dc79d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125767"
---
# <a name="create-a-network-security-group-classic-using-powershell"></a>Utwórz sieciową grupę zabezpieczeń (model klasyczny) przy użyciu programu PowerShell
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania. Możesz również [tworzenie sieciowych grup zabezpieczeń w modelu wdrażania usługi Resource Manager](tutorial-filter-network-traffic.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Przykład programu PowerShell, poniższe polecenia oczekiwać proste środowisko już utworzone na podstawie powyższego scenariusza. Jeśli chcesz uruchamiać polecenia, ponieważ są one wyświetlane w tym dokumencie, najpierw utworzyć środowisko testowe, [tworzenia sieci wirtualnej](virtual-networks-create-vnet-classic-netcfg-ps.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Tworzenie sieciowej grupy zabezpieczeń dla podsieci frontonu

1. Jeśli po raz pierwszy używasz programu Azure PowerShell, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

2. Utwórz sieciową grupę zabezpieczeń o nazwie *sieciowa grupa zabezpieczeń frontonu*:

    ```powershell   
    New-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" -Location uswest `
      -Label "Front end subnet NSG"
   ```

3. Utwórz regułę zabezpieczeń, zezwalając na dostęp z Internetu do portu 3389:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '3389'
   ```

4. Utwórz regułę zabezpieczeń, zezwalając na dostęp z Internetu na porcie 80:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name web-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 200 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '80'
    ```

## <a name="create-an-nsg-for-the-back-end-subnet"></a>Tworzenie sieciowej grupy zabezpieczeń dla podsieci zaplecza

1. Utwórz sieciową grupę zabezpieczeń o nazwie *sieciowej grupy zabezpieczeń zaplecza*:
   
    ```powershell
    New-AzureNetworkSecurityGroup -Name "NSG-BackEnd" -Location uswest `
      -Label "Back end subnet NSG"
    ```

2. Utwórz regułę zabezpieczeń, zezwalając na dostęp z podsieci frontonu do portu 1433 (domyślnym portem używanym przez program SQL Server):
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix 192.168.1.0/24  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '1433'
    ```

3. Utwórz regułę zabezpieczeń blokuje dostęp do Internetu z podsieci:
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-BackEnd" `
      | Set-AzureNetworkSecurityRule -Name block-internet `
      -Action Deny -Protocol '*' -Type Outbound -Priority 200 `
      -SourceAddressPrefix '*'  -SourcePortRange '*' `
      -DestinationAddressPrefix Internet -DestinationPortRange '*'
   ```