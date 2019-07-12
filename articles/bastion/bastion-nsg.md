---
title: Praca z maszynami wirtualnymi i sieciowych grup zabezpieczeń w usłudze Azure bastionu | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób dodawania sieciowej grupy zabezpieczeń dostęp przy użyciu Azure bastionu
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 5312ad2593e732f4c84eb67ed263bc9e4666a67a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594190"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>Praca z dostępem do sieciowej grupy zabezpieczeń i bastionu Azure (wersja zapoznawcza)

Podczas pracy z bastionu platformy Azure, można użyć grup zabezpieczeń sieci (NSG). Aby uzyskać więcej informacji, zobacz [grup zabezpieczeń](../virtual-network/security-overview.md). 

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

![Architektura](./media/bastion-nsg/nsg_architecture.png)

Na poniższym diagramie:

* Host bastionu jest wdrażana w sieci wirtualnej.
* Użytkownik łączy się do witryny Azure portal przy użyciu dowolnej przeglądarki HTML5.
* Gdy użytkownik wybierze maszyny wirtualnej, aby nawiązać połączenie.
* Za pomocą jednego kliknięcia sesję RDP/SSH zostanie otwarta w przeglądarce.
* Nie publicznego adresu IP jest wymagany na maszynie Wirtualnej platformy Azure.

## <a name="nsg"></a>Sieciowe grupy zabezpieczeń

* **AzureBastionSubnet:** Bastionu platformy Azure jest wdrażana w określonych AzureBastionSubnet.  
    * **Ruch przychodzący z publicznej sieci internet:** Spowoduje to utworzenie bastionu Azure publiczny adres IP, który wymaga portu 443 włączone na publiczny adres IP dla ruchu przychodzącego. Port 3389/22 nie muszą być otwarte na AzureBastionSubnet.
    * **Ruch wychodzący do docelowych maszyn wirtualnych:** Azure bastionu będzie korzystał z docelowych maszyn wirtualnych za pośrednictwem prywatnego adresu IP. Sieciowe grupy zabezpieczeń muszą zezwalać na ruch wychodzący do innych podsieci maszyny Wirtualnej docelowego.
* **Docelowa podsieć maszyny Wirtualnej:** Jest to podsieci, która zawiera docelowej maszyny wirtualnej, którą chcesz protokołu RDP/SSH.
    * **Ruch przychodzący z bastionu platformy Azure:** Bastionu platformy Azure skontaktuje się do docelowej maszyny Wirtualnej za pośrednictwem prywatnego adresu IP. Portów protokołu RDP/SSH (porty 3389 i 22, odpowiednio) muszą być otwarte na docelowej stronie maszyny Wirtualnej za pośrednictwem prywatnego adresu IP.

## <a name="apply"></a>Zastosować sieciowe grupy zabezpieczeń do AzureBastionSubnet

Jeśli zastosujesz sieciowych grup zabezpieczeń do **AzureBastionSubnet**, Zezwalaj na następujące dwa tagi usługi na płaszczyźnie kontroli platformy Azure i infrastrukturą:

* **GatewayManager (tylko model Resource Manager)** : ten tag określa prefiksy adresów usługi Azure Gateway Manager. Jeśli określisz GatewayManager wartości ruch dozwolony lub blokowany do GatewayManager.  W przypadku tworzenia grup zabezpieczeń sieci na AzureBastionSubnet Włącz tag GatewayManager dla ruchu przychodzącego.

* **AzureCloud (tylko model Resource Manager)** : Ten tag Określa przestrzeń adresów IP na platformie Azure, w tym wszystkich centrów danych publicznych adresów IP. Jeśli określisz AzureCloud wartości ruch dozwolony lub blokowany do platformy Azure publicznych adresów IP. Jeśli chcesz zezwolić na dostęp tylko do AzureCloud w określonym regionie, możesz określić region. Na przykład jeśli chcesz zezwolić na dostęp tylko do AzureCloud platformy Azure w regionie wschodnie stany USA, możesz określić AzureCloud.EastUS jako tag usługi. W przypadku tworzenia grup zabezpieczeń sieci na AzureBastionSubnet Włącz tag AzureCloud dla ruchu wychodzącego.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Azure bastionu zobacz [— często zadawane pytania](bastion-faq.md)
