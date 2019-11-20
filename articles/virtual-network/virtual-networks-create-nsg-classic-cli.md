---
title: Tworzenie sieciowej grupy zabezpieczeń (klasycznej) przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć i wdrożyć grupę zabezpieczeń sieci (klasyczną) przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: c17e75849d1127c9c1001fd87af64e3ffe0eba14
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196712"
---
# <a name="create-a-network-security-group-classic-using-the-azure-classic-cli"></a>Tworzenie sieciowej grupy zabezpieczeń (klasycznej) przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania. Możesz również [utworzyć sieciowych grup zabezpieczeń w modelu wdrażania Menedżer zasobów](tutorial-filter-network-traffic-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

W następujących przykładowych poleceniach interfejsu CLI platformy Azure oczekuje się, że proste środowisko zostało już utworzone na podstawie tego scenariusza. Jeśli chcesz uruchomić polecenia w taki sposób, aby były wyświetlane w tym dokumencie, najpierw Skompiluj środowisko testowe przez [utworzenie sieci wirtualnej](virtual-networks-create-vnet-classic-cli.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Utwórz sieciowej grupy zabezpieczeń dla podsieci frontonu

1. Jeśli nigdy nie korzystasz z interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie i Konfigurowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-cli-version-1.0).
2. Przełącz do trybu klasycznego:

    ```azurecli
    azure config mode asm
    ```   

3. Utwórz element sieciowej grupy zabezpieczeń::
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Utwórz regułę zabezpieczeń, która umożliwia dostęp do portu 3389 (RDP) z Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Utwórz regułę zezwalającą na dostęp do portu 80 (HTTP) z Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. Skojarz sieciowej grupy zabezpieczeń z podsiecią frontonu:
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>Utwórz sieciowej grupy zabezpieczeń dla podsieci zaplecza

1. Utwórz sieciowej grupy zabezpieczeń:
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Utwórz regułę zezwalającą na dostęp do portu 1433 (SQL) z podsieci frontonu:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Utwórz regułę odmawiającą dostępu do Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. Skojarz sieciowej grupy zabezpieczeń z podsiecią zaplecza:
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```