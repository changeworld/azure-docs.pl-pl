---
title: Resetowanie bramy sieci VPN platformy Azure, aby ponownie ustanowić tuneli IPsec | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono Resetowanie bramy sieci VPN platformy Azure, aby ponownie ustanowić tuneli IPsec. Artykuł dotyczy bram sieci VPN w klasycznym i modeli wdrażania Menedżera zasobów.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 79d77cb8-d175-4273-93ac-712d7d45b1fe
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: cherylmc
ms.openlocfilehash: 604af54d034b6b51a96ff098827e2352a163be18
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23884552"
---
# <a name="reset-a-vpn-gateway"></a>Resetowanie bramy VPN Gateway

Resetowanie bramy Azure VPN Gateway przydaje się w przypadku utraty połączenia sieci VPN obejmującego wiele lokalizacji w jednym lub wielu tunelach VPN typu lokacja-lokacja. W takiej sytuacji urządzenia lokalnej sieci VPN działają prawidłowo, ale nie mogą nawiązać połączenia w ramach tuneli używających protokołu IPsec z bramami sieci VPN Azure. Ten artykuł pomoże Ci zresetować bramy sieci VPN.

### <a name="what-happens-during-a-reset"></a>Co się dzieje podczas resetowania?

Brama sieci VPN składa się z dwóch wystąpień maszyn wirtualnych działających w konfiguracji aktywnego gotowości. Zresetowanie bramy uruchamia bramy, a następnie ponownie stosuje konfiguracje między różnymi lokalizacjami, aby go. Brama zachowa publiczny adres IP, który został z nią wcześniej powiązany. Oznacza to, że nie będzie konieczne aktualizowanie konfiguracji routera sieci VPN pod kątem nowego publicznego adresu IP bramy sieci VPN Azure.

Po wydaniu polecenia można zresetować bramy, natychmiast ponownego active bieżące wystąpienie bramy sieci VPN platformy Azure. Podczas pracy awaryjnej z aktywnym wystąpieniu (Trwa ponowne uruchamianie), do wstrzymania wystąpienia nastąpi krótką przerwę. Przerwa powinna trwać niż dłużej niż minutę.

Jeśli połączenie nie zostanie przywrócone po pierwszym ponownym rozruchu komputera, należy wydać to polecenie ponownie, aby uruchomić ponownie drugie wystąpienie maszyny wirtualnej (nową aktywną bramę). Jeśli wymagane jest przeprowadzenie dwóch rozruchów jednocześnie, to ponowne uruchomienie obu wystąpień maszyn wirtualnych (aktywnego i w gotowości) będzie trwać nieco dłużej. Spowoduje to dłuższą przerwę w łączności przez sieć VPN, trwającą maksymalnie od 2 do 4 minut, podczas której nastąpi ponowny rozruch maszyn wirtualnych.

Po dwóch uruchamiany ponownie Jeśli nadal występują problemy z łącznością między różnymi lokalizacjami, otwórz żądanie obsługi z portalu Azure.

## <a name="before"></a>Przed rozpoczęciem

Przed zresetowaniem bramy dla każdego tunelu połączenia sieci VPN typu lokacja-lokacja (site-to site, S2S) korzystającego z protokołu IPsec należy sprawdzić kluczowe elementy wymienione poniżej. Brak zgodności którychkolwiek elementów spowoduje przerwanie połączenia tuneli sieci VPN S2S. Sprawdzanie poprawności i usuwanie konfiguracji dla usługi lokalnej i bram sieci VPN platformy Azure pozwala uniknąć niepotrzebnych ponownych rozruchów i zakłóceń dla innych połączeń pracy w bramach.

Przed zresetowaniem bramy, sprawdź następujące elementy:

* Wirtualne adresy IP (VIP) bramy sieci VPN Azure i bramy lokalnej sieci VPN powinny być prawidłowo skonfigurowane zarówno w zasadach platformy Azure, jak i zasadach lokalnej sieci VPN.
* Klucz wstępny musi być taki sam w przypadku bramy sieci VPN Azure oraz bramy lokalnej sieci VPN.
* W przypadku zastosowania konkretnej konfiguracji uwzględniającej protokół IPsec/IKE, takiej jak szyfrowanie, algorytmy wyznaczania wartości skrótu i doskonałe utajnienie przekazywania (Perfect Forward Secrecy, PFS), należy się upewnić, że zarówno brama sieci VPN Azure, jak i brama lokalnej sieci VPN mają takie same konfiguracje.

## <a name="portal"></a>Azure portal

Można zresetować bramy sieci VPN usługi Resource Manager przy użyciu portalu Azure. Jeśli chcesz zresetować klasycznego bramy, zobacz [PowerShell](#resetclassic) czynności.

### <a name="resource-manager-deployment-model"></a>Model wdrażania usługi Resource Manager

1. Otwórz [portalu Azure](https://portal.azure.com) i przejdź do Menedżera zasobów bramy sieci wirtualnej, który chcesz zresetować.
2. W bloku dla bramy sieci wirtualnej kliknij pozycję "Resetuj".

  ![Resetuj bloku bramy sieci VPN](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. W bloku resetowania kliknij **zresetować** przycisku.

## <a name="ps"></a>Środowiska PowerShell

### <a name="resource-manager-deployment-model"></a>Model wdrażania usługi Resource Manager

Polecenie cmdlet resetowania bramy jest **AzureRmVirtualNetworkGateway resetowania**. Przed wykonaniem resetowanie, upewnij się, że masz najnowszą wersję [poleceń cmdlet programu PowerShell Menedżera zasobów](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0). Poniższy przykład powoduje zresetowanie bramy sieci wirtualnej o nazwie VNet1GW w grupie zasobów TestRG1:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Wynik:

Po wyświetleniu zwracanych wyników można założyć, resetowanie bramy zakończyło się pomyślnie. Jednak nie ma w wyniku zwracany, która jawnie wskazuje, że zresetowanie zakończyła się pomyślnie. Jeśli chcesz należy dokładnie przejrzeć historię, aby zobaczyć dokładnie Resetowanie bramy wystąpienia, możesz wyświetlić te informacje w [portalu Azure](https://portal.azure.com). W portalu, przejdź do **"GatewayName" -> kondycja zasobów**.

### <a name="resetclassic"></a>Klasycznego modelu wdrażania

Polecenie cmdlet resetowania bramy jest **AzureVNetGateway resetowania**. Przed wykonaniem resetowanie, upewnij się, że masz najnowszą wersję [poleceń cmdlet programu PowerShell usługi zarządzania (SM)](https://docs.microsoft.com/powershell/azure/install-azure-ps?view=azuresmps-3.7.0). Poniższy przykład powoduje zresetowanie bramy sieci wirtualnej o nazwie "ContosoVNet":

```powershell
Reset-AzureVNetGateway –VnetName “ContosoVNet”
```

Wynik:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="cli"></a>Interfejs wiersza polecenia platformy Azure

Aby zresetować bramy, należy użyć [zresetować bramy sieci wirtualnej sieci az](https://docs.microsoft.com/cli/azure/network/vnet-gateway#az_network_vnet_gateway_reset) polecenia. Poniższy przykład powoduje zresetowanie bramy sieci wirtualnej o nazwie VNet5GW w grupie zasobów TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Wynik:

Po wyświetleniu zwracanych wyników można założyć, resetowanie bramy zakończyło się pomyślnie. Jednak nie ma w wyniku zwracany, która jawnie wskazuje, że zresetowanie zakończyła się pomyślnie. Jeśli chcesz należy dokładnie przejrzeć historię, aby zobaczyć dokładnie Resetowanie bramy wystąpienia, możesz wyświetlić te informacje w [portalu Azure](https://portal.azure.com). W portalu, przejdź do **"GatewayName" -> kondycja zasobów**.