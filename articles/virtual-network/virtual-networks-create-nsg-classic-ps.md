---
title: Tworzenie sieciowej grupy zabezpieczeń (klasycznej) przy użyciu programu PowerShell | Microsoft Docs
description: Dowiedz się, jak utworzyć i wdrożyć grupę zabezpieczeń sieci (klasyczną) przy użyciu programu PowerShell
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 5ef80ccd01c9c6979fd95d161d97d0dfaab58b24
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056665"
---
# <a name="create-a-network-security-group-classic-using-powershell"></a>Tworzenie sieciowej grupy zabezpieczeń (klasycznej) przy użyciu programu PowerShell
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania. Możesz również [utworzyć sieciowych grup zabezpieczeń w modelu wdrażania Menedżer zasobów](tutorial-filter-network-traffic.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Przykładowe polecenia programu PowerShell poniżej oczekują prostego środowiska już utworzonego na podstawie powyższego scenariusza. Jeśli chcesz uruchomić polecenia w taki sposób, aby były wyświetlane w tym dokumencie, najpierw Skompiluj środowisko testowe przez [utworzenie sieci wirtualnej](virtual-networks-create-vnet-classic-netcfg-ps.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Utwórz sieciowej grupy zabezpieczeń dla podsieci frontonu

1. Jeśli nie korzystasz z Azure PowerShell, zobacz [temat jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/overview).

2. Utwórz sieciową grupę zabezpieczeń o nazwie *sieciowej grupy zabezpieczeń-fronton*:

    ```powershell   
    New-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" -Location uswest `
      -Label "Front end subnet NSG"
   ```

3. Utwórz regułę zabezpieczeń zezwalającą na dostęp z Internetu do portu 3389:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '3389'
   ```

4. Utwórz regułę zabezpieczeń zezwalającą na dostęp z Internetu do portu 80:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name web-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 200 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '80'
    ```

## <a name="create-an-nsg-for-the-back-end-subnet"></a>Utwórz sieciowej grupy zabezpieczeń dla podsieci zaplecza

1. Utwórz sieciową grupę zabezpieczeń o nazwie *sieciowej grupy zabezpieczeń-zaplecza*:
   
    ```powershell
    New-AzureNetworkSecurityGroup -Name "NSG-BackEnd" -Location uswest `
      -Label "Back end subnet NSG"
    ```

2. Utwórz regułę zabezpieczeń zezwalającą na dostęp z podsieci frontonu do portu 1433 (port domyślny używany przez SQL Server):
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix 192.168.1.0/24  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '1433'
    ```

3. Utwórz regułę zabezpieczeń blokującą dostęp z podsieci do Internetu:
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-BackEnd" `
      | Set-AzureNetworkSecurityRule -Name block-internet `
      -Action Deny -Protocol '*' -Type Outbound -Priority 200 `
      -SourceAddressPrefix '*'  -SourcePortRange '*' `
      -DestinationAddressPrefix Internet -DestinationPortRange '*'
   ```