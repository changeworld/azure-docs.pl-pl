---
title: Resetowanie bramy sieci VPN platformy Azure w celu przywrócenia tunelu IPsec
description: W tym artykule otrzymasz od resetowania bramy sieci VPN platformy Azure w celu ponownego nawiązania tuneli protokołu IPsec. Ten artykuł dotyczy bram sieci VPN zarówno w modelach wdrażania klasycznego, jak i wdrożeniowego Menedżera zasobów.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: e3a5807a0ccfa39cc80acacedaa5fb4d3afaaed3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244422"
---
# <a name="reset-a-vpn-gateway"></a>Resetowanie bramy VPN Gateway

Resetowanie bramy Azure VPN Gateway przydaje się w przypadku utraty połączenia sieci VPN obejmującego wiele lokalizacji w jednym lub wielu tunelach VPN typu lokacja-lokacja. W takiej sytuacji urządzenia lokalnej sieci VPN działają prawidłowo, ale nie mogą nawiązać połączenia w ramach tuneli używających protokołu IPsec z bramami sieci VPN Azure. Ten artykuł ułatwia zresetowanie bramy sieci VPN.

### <a name="what-happens-during-a-reset"></a>Co dzieje się podczas resetowania?

Brama sieci VPN składa się z dwóch wystąpień maszyn wirtualnych uruchomionych w konfiguracji aktywnej w trybie wstrzymania. Po zresetowaniu bramy ponownie uruchamia bramę, a następnie ponownie uruchamia konfiguracje między lokalami. Brama zachowa publiczny adres IP, który został z nią wcześniej powiązany. Oznacza to, że nie będzie konieczne aktualizowanie konfiguracji routera sieci VPN pod kątem nowego publicznego adresu IP bramy sieci VPN Azure.

Po wydaniu polecenia, aby zresetować bramę, bieżące aktywne wystąpienie bramy sieci VPN platformy Azure jest natychmiast ponownie uruchamiane ponownie. Będzie krótka przerwa podczas pracy awaryjnej z aktywnego wystąpienia (jest ponownie uruchomiony), do wystąpienia wstrzymania. Przerwa powinna trwać niż dłużej niż minutę.

Jeśli połączenie nie zostanie przywrócone po pierwszym ponownym rozruchu komputera, należy wydać to polecenie ponownie, aby uruchomić ponownie drugie wystąpienie maszyny wirtualnej (nową aktywną bramę). Jeśli wymagane jest przeprowadzenie dwóch rozruchów jednocześnie, to ponowne uruchomienie obu wystąpień maszyn wirtualnych (aktywnego i w gotowości) będzie trwać nieco dłużej. Spowoduje to dłuższą przerwę w łączności sieci VPN, do 30 do 45 minut dla maszyn wirtualnych, aby zakończyć ponowne uruchomienie.

Po dwóch ponownych rozruchach, jeśli nadal występują problemy z łącznością między środowiskami, otwórz żądanie pomocy technicznej z witryny Azure portal.

## <a name="before-you-begin"></a><a name="before"></a>Przed rozpoczęciem

Przed zresetowaniem bramy dla każdego tunelu połączenia sieci VPN typu lokacja-lokacja (site-to site, S2S) korzystającego z protokołu IPsec należy sprawdzić kluczowe elementy wymienione poniżej. Brak zgodności którychkolwiek elementów spowoduje przerwanie połączenia tuneli sieci VPN S2S. Weryfikowanie i korygowanie konfiguracji lokalnych i bramy sieci VPN platformy Azure pozwala zaoszczędzić na niepotrzebnych ponownych rozruchach i zakłóceniach dla innych połączeń roboczych na bramy.

Przed zresetowaniem bramy sprawdź następujące elementy:

* Wirtualne adresy IP (VIP) bramy sieci VPN Azure i bramy lokalnej sieci VPN powinny być prawidłowo skonfigurowane zarówno w zasadach platformy Azure, jak i zasadach lokalnej sieci VPN.
* Klucz wstępny musi być taki sam w przypadku bramy sieci VPN Azure oraz bramy lokalnej sieci VPN.
* W przypadku zastosowania konkretnej konfiguracji uwzględniającej protokół IPsec/IKE, takiej jak szyfrowanie, algorytmy wyznaczania wartości skrótu i doskonałe utajnienie przekazywania (Perfect Forward Secrecy, PFS), należy się upewnić, że zarówno brama sieci VPN Azure, jak i brama lokalnej sieci VPN mają takie same konfiguracje.

## <a name="azure-portal"></a><a name="portal"></a>Portal Azure

Bramę sieci VPN menedżera zasobów można zresetować za pomocą witryny Azure portal. Jeśli chcesz zresetować bramę klasyczną, zobacz kroki [programu PowerShell.](#resetclassic)

### <a name="resource-manager-deployment-model"></a>Model wdrażania usługi Resource Manager

1. Otwórz [witrynę Azure portal](https://portal.azure.com) i przejdź do bramy sieci wirtualnej Usługi Resource Manager, którą chcesz zresetować.
2. W bloku bramy sieci wirtualnej kliknij przycisk "Resetuj".

   ![Resetowanie bloku bramy sieci VPN](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. Na bloku Resetuj kliknij przycisk **Resetuj.**

## <a name="powershell"></a><a name="ps"></a>Powershell

### <a name="resource-manager-deployment-model"></a>Model wdrażania usługi Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Polecenie cmdlet do resetowania bramy jest **Reset-AzVirtualNetworkGateway**. Przed zresetowaniem upewnij się, że masz najnowszą wersję [poleceń cmdlet programu PowerShell Az](https://docs.microsoft.com/powershell/module/az.network). Poniższy przykład resetuje bramę sieci wirtualnej o nazwie VNet1GW w grupie zasobów TestRG1:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Wynik:

Po otrzymaniu wyniku zwrotu można założyć, że resetowanie bramy zakończyło się pomyślnie. Jednak nie ma nic w wyniku zwrotu, który wskazuje jawnie, że resetowanie zakończyło się pomyślnie. Jeśli chcesz dokładnie przyjrzeć się historii, aby zobaczyć dokładnie, kiedy wystąpił reset bramy, możesz wyświetlić te informacje w [witrynie Azure portal](https://portal.azure.com). W portalu przejdź do **'GatewayName' -> Resource Health**.

### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Klasyczny model wdrażania

Polecenie cmdlet resetowania bramy to **Reset-AzureVNetGateway**. Polecenia cmdlet programu Azure PowerShell dla zarządzania usługami muszą być zainstalowane lokalnie na pulpicie. Nie można użyć usługi Azure Cloud Shell. Przed wykonaniem resetowania upewnij się, że masz najnowszą wersję [poleceń cmdlet programu PowerShell zarządzania usługami (SM).](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) Korzystając z tego polecenia, upewnij się, że używasz pełnej nazwy sieci wirtualnej. Klasyczne sieci wirtualne, które zostały utworzone przy użyciu portalu mają długą nazwę, która jest wymagana dla programu PowerShell. Długą nazwę można wyświetlić przy użyciu funkcji "Get-AzureVNetConfig -ExportToFile C:\Myfoldername\NetworkConfig.xml".

Poniższy przykład resetuje bramę dla sieci wirtualnej o nazwie "Group TestRG1 TestVNet1" (który pokazuje jako po prostu "TestVNet1" w portalu):

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
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

## <a name="azure-cli"></a><a name="cli"></a>Interfejs wiersza polecenia platformy Azure

Aby zresetować bramę, użyj polecenia [resetowania bramy sieciowej az.](https://docs.microsoft.com/cli/azure/network/vnet-gateway) Poniższy przykład resetuje bramę sieci wirtualnej o nazwie VNet5GW w grupie zasobów TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Wynik:

Po otrzymaniu wyniku zwrotu można założyć, że resetowanie bramy zakończyło się pomyślnie. Jednak nie ma nic w wyniku zwrotu, który wskazuje jawnie, że resetowanie zakończyło się pomyślnie. Jeśli chcesz dokładnie przyjrzeć się historii, aby zobaczyć dokładnie, kiedy wystąpił reset bramy, możesz wyświetlić te informacje w [witrynie Azure portal](https://portal.azure.com). W portalu przejdź do **'GatewayName' -> Resource Health**.
