---
title: Resetowanie bramy sieci VPN platformy Azure, aby ponownie ustanowić tuneli IPsec | Dokumentacja firmy Microsoft
description: W tym artykule opisano procedurę resetowania bramy sieci VPN Azure, aby ponownie ustanowić tuneli IPsec. Artykuł ma zastosowanie do bram sieci VPN w klasycznym i modelem wdrażania usługi Resource Manager.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/14/2019
ms.author: cherylmc
ms.openlocfilehash: 54b89b74017b8d5d6e4bd1b52c6b3986d2802702
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60457211"
---
# <a name="reset-a-vpn-gateway"></a>Resetowanie bramy VPN Gateway

Resetowanie bramy Azure VPN Gateway przydaje się w przypadku utraty połączenia sieci VPN obejmującego wiele lokalizacji w jednym lub wielu tunelach VPN typu lokacja-lokacja. W takiej sytuacji urządzenia lokalnej sieci VPN działają prawidłowo, ale nie mogą nawiązać połączenia w ramach tuneli używających protokołu IPsec z bramami sieci VPN Azure. Ten artykuł pomoże Ci zresetować bramy sieci VPN.

### <a name="what-happens-during-a-reset"></a>Co się dzieje podczas resetowania?

Bramy VPN gateway składa się z dwóch wystąpień maszyn wirtualnych działających w konfiguracji aktywne-w gotowości. Zresetowanie bramy, ponowne uruchomienie bramy i ponowne zastosowanie do niej konfiguracji obejmujących wiele lokalizacji. Brama zachowa publiczny adres IP, który został z nią wcześniej powiązany. Oznacza to, że nie będzie konieczne aktualizowanie konfiguracji routera sieci VPN pod kątem nowego publicznego adresu IP bramy sieci VPN Azure.

Po wykonaniu polecenia, można zresetować bramy, bieżące aktywne wystąpienie bramy Azure VPN gateway jest natychmiast uruchomione ponownie. Nastąpi krótkie przerwy podczas trybu failover z aktywnego wystąpienia (ponownie uruchamiane), aby rezerwowe wystąpienie. Przerwa powinna trwać niż dłużej niż minutę.

Jeśli połączenie nie zostanie przywrócone po pierwszym ponownym rozruchu komputera, należy wydać to polecenie ponownie, aby uruchomić ponownie drugie wystąpienie maszyny wirtualnej (nową aktywną bramę). Jeśli wymagane jest przeprowadzenie dwóch rozruchów jednocześnie, to ponowne uruchomienie obu wystąpień maszyn wirtualnych (aktywnego i w gotowości) będzie trwać nieco dłużej. Spowoduje to dłuższą przerwę w łączności przez sieć VPN, trwającą maksymalnie od 2 do 4 minut, podczas której nastąpi ponowny rozruch maszyn wirtualnych.

Po dwóch ponownych uruchomieniach Jeśli nadal występują problemy z łącznością między środowiskami lokalnymi, otwórz żądanie pomocy technicznej w witrynie Azure portal.

## <a name="before"></a>Przed rozpoczęciem

Przed zresetowaniem bramy dla każdego tunelu połączenia sieci VPN typu lokacja-lokacja (site-to site, S2S) korzystającego z protokołu IPsec należy sprawdzić kluczowe elementy wymienione poniżej. Brak zgodności którychkolwiek elementów spowoduje przerwanie połączenia tuneli sieci VPN S2S. Zweryfikowanie i poprawienie konfiguracji dla usługi w środowisku lokalnym i bram sieci VPN platformy Azure pozwala uniknąć niepotrzebnego ponownego uruchamiania i zakłóceń dla innych połączeń działających w bramach.

Przed zresetowaniem bramy, sprawdź następujące elementy:

* Wirtualne adresy IP (VIP) bramy sieci VPN Azure i bramy lokalnej sieci VPN powinny być prawidłowo skonfigurowane zarówno w zasadach platformy Azure, jak i zasadach lokalnej sieci VPN.
* Klucz wstępny musi być taki sam w przypadku bramy sieci VPN Azure oraz bramy lokalnej sieci VPN.
* W przypadku zastosowania konkretnej konfiguracji uwzględniającej protokół IPsec/IKE, takiej jak szyfrowanie, algorytmy wyznaczania wartości skrótu i doskonałe utajnienie przekazywania (Perfect Forward Secrecy, PFS), należy się upewnić, że zarówno brama sieci VPN Azure, jak i brama lokalnej sieci VPN mają takie same konfiguracje.

## <a name="portal"></a>Azure Portal

Można zresetować bramy sieci VPN usługi Resource Manager przy użyciu witryny Azure portal. Jeśli chcesz zresetować bram klasyczny, zobacz [PowerShell](#resetclassic) kroki.

### <a name="resource-manager-deployment-model"></a>Model wdrażania usługi Resource Manager

1. Otwórz [witryny Azure portal](https://portal.azure.com) i przejdź do bramy sieci wirtualnej usługi Resource Manager, który chcesz zresetować.
2. W bloku bramy sieci wirtualnej kliknij przycisk "Resetuj".

   ![Zresetować blok bramy sieci VPN](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. W bloku resetowania kliknij **resetowania** przycisku.

## <a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Model wdrażania usługi Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Polecenie cmdlet służące do resetowania bramy to **AzVirtualNetworkGateway resetowania**. Przed przystąpieniem do wykonywania resetu, upewnij się, że masz najnowszą wersję [poleceń cmdlet programu PowerShell Az](https://docs.microsoft.com/powershell/module/az.network). Poniższy przykład powoduje zresetowanie bramy sieci wirtualnej o nazwie VNet1GW w grupie zasobów TestRG1:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Wynik:

Gdy zostanie wyświetlony wynik zwracany, można założyć, resetowania bramy zakończyło się pomyślnie. Jednak nie ma nic do zwracanego wyniku, która wyraźnie wskazuje, czy resetowania zakończyła się pomyślnie. Jeśli chcesz Przyjrzyj się bliżej historii, aby zobaczyć dokładnie, kiedy resetowania bramy wystąpił, możesz wyświetlić te informacje w [witryny Azure portal](https://portal.azure.com). W portalu, przejdź do **"GatewayName" -> kondycja zasobu**.

### <a name="resetclassic"></a>Klasyczny model wdrażania

Polecenie cmdlet służące do resetowania bramy to **Reset-AzureVNetGateway**. Przed przystąpieniem do wykonywania resetu, upewnij się, że masz najnowszą wersję [poleceń cmdlet programu PowerShell Service Management (SM)](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets). Poniższy przykład powoduje zresetowanie bramy dla sieci wirtualnej o nazwie "ContosoVNet":

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

Aby zresetować bramę, użyj [resetowania bramy sieci wirtualnej az sieci](https://docs.microsoft.com/cli/azure/network/vnet-gateway) polecenia. Poniższy przykład powoduje zresetowanie bramy sieci wirtualnej o nazwie VNet5GW w grupie zasobów TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Wynik:

Gdy zostanie wyświetlony wynik zwracany, można założyć, resetowania bramy zakończyło się pomyślnie. Jednak nie ma nic do zwracanego wyniku, która wyraźnie wskazuje, czy resetowania zakończyła się pomyślnie. Jeśli chcesz Przyjrzyj się bliżej historii, aby zobaczyć dokładnie, kiedy resetowania bramy wystąpił, możesz wyświetlić te informacje w [witryny Azure portal](https://portal.azure.com). W portalu, przejdź do **"GatewayName" -> kondycja zasobu**.
