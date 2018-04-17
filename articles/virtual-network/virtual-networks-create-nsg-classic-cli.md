---
title: Utwórz grupę zabezpieczeń sieci (klasyczne) przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i wdrożyć grupę zabezpieczeń sieci (klasyczne) przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
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
ms.openlocfilehash: a9ab1a83366919a2d05da18819ed8167bdadb20a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-network-security-group-classic-using-the-azure-cli-10"></a>Tworzenie grupy zabezpieczeń sieci (wdrożenia klasyczne) przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania. Możesz również [tworzenia grup NSG w modelu wdrażania usługi Resource Manager](tutorial-filter-network-traffic-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Następujące przykładowe polecenia interfejsu wiersza polecenia Azure oczekiwać środowisku niezłożonym już utworzone w zależności od scenariusza. Do uruchomienia poleceń wyświetlaną w tym dokumencie, najpierw utworzyć środowisko testowe przez [tworzenia sieci wirtualnej](virtual-networks-create-vnet-classic-cli.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Tworzenie grupy NSG dla podsieci frontonu

1. Jeśli po raz pierwszy używasz interfejsu wiersza polecenia Azure, zobacz [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](../cli-install-nodejs.md).
2. Przełącz do trybu klasycznego:

    ```azurecli
    azure config mode asm
    ```   

3. Tworzenie grupy NSG::
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Utwórz regułę zabezpieczeń, która zezwala na dostęp do portu 3389 (RDP) z Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Utworzyć regułę, która zezwala na dostęp do portu 80 (HTTP) z Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. Kojarzenie grupy NSG do podsieci frontonu:
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>Tworzenie grupy NSG podsieci wewnętrznej

1. Utwórz grupy NSG:
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Utworzyć regułę, która umożliwia dostęp do portu 1433 (SQL) z podsieci frontonu:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Utworzyć regułę, która nie zezwala na dostęp do Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. Kojarzenie grupy NSG do podsieci wewnętrznej:
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```