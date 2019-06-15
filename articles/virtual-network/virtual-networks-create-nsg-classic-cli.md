---
title: Utwórz sieciową grupę zabezpieczeń (wersja klasyczna) przy użyciu platformy Azure klasyczny interfejs wiersza polecenia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i wdrożyć sieciową grupę zabezpieczeń (wersja klasyczna) przy użyciu klasycznego wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: genlin
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
ms.openlocfilehash: 454d17ac4f4c3723d7b7ee1ac2c639b885f3fff9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721201"
---
# <a name="create-a-network-security-group-classic-using-the-azure-classic-cli"></a>Utwórz sieciową grupę zabezpieczeń (model klasyczny) przy użyciu platformy Azure klasyczny interfejs wiersza polecenia
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania. Możesz również [tworzenie sieciowych grup zabezpieczeń w modelu wdrażania usługi Resource Manager](tutorial-filter-network-traffic-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Następujące przykładowe polecenia wiersza polecenia platformy Azure oczekują proste środowisko już utworzone w zależności od scenariusza. Jeśli chcesz uruchamiać polecenia, ponieważ są one wyświetlane w tym dokumencie, najpierw utworzyć środowisko testowe, [tworzenia sieci wirtualnej](virtual-networks-create-vnet-classic-cli.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Tworzenie sieciowej grupy zabezpieczeń dla podsieci frontonu

1. Jeśli po raz pierwszy używasz interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](/cli/azure/install-cli-version-1.0).
2. Przełącz do trybu klasycznego:

    ```azurecli
    azure config mode asm
    ```   

3. Tworzenie sieciowej grupy zabezpieczeń::
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Utwórz regułę zabezpieczeń, która zezwala na dostęp do portu 3389 (RDP) z Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Utwórz regułę, która zezwala na dostęp do portu 80 (HTTP) z Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. Kojarzenie sieciowej grupy zabezpieczeń do podsieci frontonu:
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>Tworzenie sieciowej grupy zabezpieczeń dla podsieci zaplecza

1. Tworzenie sieciowej grupy zabezpieczeń:
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Utwórz regułę, która umożliwia dostęp do portu 1433 (SQL) z podsieci frontonu:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Utwórz regułę, która nie zezwala na dostęp do Internetu:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. Kojarzenie sieciowej grupy zabezpieczeń do podsieci zaplecza:
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```