---
title: Praca z maszynami wirtualnymi i sieciowych grup zabezpieczeń na platformie Azure bastionu | Microsoft Docs
description: W tym artykule opisano sposób włączania dostępu sieciowej grupy zabezpieczeń za pomocą usługi Azure bastionu
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: cherylmc
ms.openlocfilehash: 4f99b24435998fc4d0c7ab724c66a318586a80d4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694949"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>Praca z usługami sieciowej grupy zabezpieczeń Access i Azure bastionu (wersja zapoznawcza)

Podczas pracy z usługą Azure bastionu można użyć sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń). Aby uzyskać więcej informacji, zobacz [grupy zabezpieczeń](../virtual-network/security-overview.md). 

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

![Architektura](./media/bastion-nsg/nsg_architecture.png)

Na tym diagramie:

* Host bastionu jest wdrażany w sieci wirtualnej.
* Użytkownik nawiązuje połączenie z Azure Portal przy użyciu dowolnej przeglądarki HTML5.
* Użytkownik wybierze maszynę wirtualną, z którą ma zostać nawiązane połączenie.
* Po jednym kliknięciu sesja RDP/SSH zostanie otwarta w przeglądarce.
* Na maszynie wirtualnej platformy Azure nie jest wymagany publiczny adres IP.

## <a name="nsg"></a>Sieciowe grupy zabezpieczeń

* **AzureBastionSubnet:** Usługa Azure bastionu jest wdrażana w konkretnym AzureBastionSubnet.  
    * **Ruch przychodzący z publicznej sieci Internet:** Usługa Azure bastionu utworzy publiczny adres IP, który wymaga, aby port 443 był włączony w publicznym adresie IP dla ruchu przychodzącego. NIE trzeba otwierać portu 3389/22 w AzureBastionSubnet.
    * **Ruch wychodzący do docelowych maszyn wirtualnych:** Usługa Azure bastionu będzie docierać do docelowych maszyn wirtualnych za pośrednictwem prywatnego adresu IP. Sieciowych grup zabezpieczeń musi zezwalać na ruch przychodzący do innych docelowych podsieci maszyn wirtualnych.
* **Docelowa podsieć maszyny wirtualnej:** Jest to podsieć zawierająca docelową maszynę wirtualną, do której ma zostać zainstalowana protokół RDP/SSH.
    * **Ruch przychodzący z usługi Azure bastionu:** Usługa Azure bastionu będzie docierać do docelowej maszyny wirtualnej za pośrednictwem prywatnego adresu IP. Porty RDP/SSH (odpowiednio porty 3389 i 22) muszą być otwarte dla docelowej maszyny wirtualnej po stronie prywatnego adresu IP.

## <a name="apply"></a>Zastosuj sieciowych grup zabezpieczeń do AzureBastionSubnet

Jeśli stosujesz sieciowych grup zabezpieczeń do **AzureBastionSubnet**, Zezwól na następujące dwa znaczniki usługi dla płaszczyzny i infrastruktury kontroli platformy Azure:

* **Gatewaymanager (tylko Menedżer zasobów)** : ten tag określa prefiksy adresów usługi Azure Gateway Manager. Jeśli dla wartości określono wartość Gatewaymanager, ruch jest dozwolony lub odrzucany do elementu Gatewaymanager.  Jeśli tworzysz sieciowych grup zabezpieczeń na AzureBastionSubnet, Włącz Tag Gatewaymanager dla ruchu przychodzącego.

* **AzureCloud (tylko Menedżer zasobów)** : ten tag określa przestrzeń adresów IP dla platformy Azure, w tym wszystkie publiczne adresy IP centrum danych. Jeśli określisz AzureCloud dla wartości, ruch jest dozwolony lub zabroniony dla publicznych adresów IP platformy Azure. Jeśli chcesz zezwolić na dostęp tylko do AzureCloud w określonym regionie, możesz określić region. Jeśli na przykład chcesz zezwolić na dostęp tylko do usługi Azure AzureCloud w regionie Wschodnie stany USA, możesz określić AzureCloud. wschód jako tag usługi. Jeśli tworzysz sieciowych grup zabezpieczeń na AzureBastionSubnet, Włącz Tag AzureCloud dla ruchu wychodzącego. Jeśli otworzysz port 443 dla ruchu przychodzącego do Internetu, nie musisz włączać znacznika AzureCloud dla ruchu przychodzącego.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure bastionu, zobacz [często zadawane pytania](bastion-faq.md)
