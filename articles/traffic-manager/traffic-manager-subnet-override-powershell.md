---
title: Zastąpienie podsieci usługi Azure Traffic Manager przy użyciu usługi Azure PowerShell | Dokumenty firmy Microsoft
description: Ten artykuł pomoże Ci zrozumieć, w jaki sposób zastępowanie podsieci Usługi Traffic Manager jest używane do zastępowania metody routingu profilu usługi Traffic Manager w celu kierowania ruchu do punktu końcowego na podstawie adresu IP użytkownika końcowego za pośrednictwem wstępnie zdefiniowanego zakresu adresów IP do mapowań punktów końcowych przy użyciu platformy Azure Powershell.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 323093ec78a9486d19496b0ee90e37cb42eea341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938417"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Zastąpienie podsieci Usługi Traffic Manager przy użyciu programu Azure Powershell

Zastępowanie podsieci Usługi Traffic Manager umożliwia zmianę metody routingu profilu.  Dodanie zastąpienia spowoduje kierowanie ruchu na podstawie adresu IP użytkownika końcowego z wstępnie zdefiniowanym zakresem adresu IP do mapowania punktu końcowego. 

## <a name="how-subnet-override-works"></a>Jak działa zastępowanie podsieci

Gdy zastąpienia podsieci są dodawane do profilu menedżera ruchu, usługa Traffic Manager najpierw sprawdzi, czy adres IP użytkownika końcowego jest zastępowanie podsieci. Jeśli zostanie znaleziony, kwerenda DNS użytkownika zostanie przekierowana do odpowiedniego punktu końcowego.  Jeśli mapowanie nie zostanie znalezione, Usługa Traffic Manager powróci do oryginalnej metody routingu profilu. 

Zakresy adresów IP można określić jako zakresy CIDR (na przykład 1.2.3.0/24) lub jako zakresy adresów (na przykład 1.2.3.4-5.6.7.8). Zakresy adresów IP skojarzone z każdym punktem końcowym muszą być unikatowe dla tego punktu końcowego. Wszelkie nakładanie się zakresów adresów IP między różnymi punktami końcowymi spowoduje odrzucenie profilu przez menedżera ruchu.

Istnieją dwa typy profilów routingu, które obsługują zastąpienia podsieci:

* **Geograficzne** — jeśli Usługa Traffic Manager znajdzie zastąpienie podsieci dla adresu IP kwerendy DNS, będzie kierować kwerendę do punktu końcowego, niezależnie od kondycji punktu końcowego.
* **Wydajność** — jeśli Usługa Traffic Manager znajdzie zastąpienie podsieci dla adresu IP kwerendy DNS, będzie kierować ruch do punktu końcowego tylko wtedy, gdy jest w dobrej kondycji.  Usługa Traffic Manager powróci do heurystyki routingu wydajności, jeśli punkt końcowy zastąpienia podsieci nie jest w dobrej kondycji.

## <a name="create-a-traffic-manager-subnet-override"></a>Tworzenie zastąpienia podsieci usługi Traffic Manager

Aby utworzyć zastąpienie podsieci usługi Traffic Manager, można użyć programu Azure PowerShell, aby dodać podsieci do zastąpienia punktu końcowego usługi Traffic Manager.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można uruchomić polecenia, które należy wykonać w [usłudze Azure Cloud Shell](https://shell.azure.com/powershell)lub uruchamiając program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna powłoka interaktywna. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Jeśli program PowerShell jest uruchamiany z komputera, potrzebny jest moduł programu Azure PowerShell, 1.0.0 lub nowsze. Można uruchomić, `Get-Module -ListAvailable Az` aby znaleźć zainstalowaną wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli korzystasz z programu PowerShell lokalnie, `Login-AzAccount` musisz również uruchomić, aby zalogować się na platformie Azure.


1. **Pobierz punkt końcowy programu Traffic Manager:**

    Aby włączyć zastępowanie podsieci, pobierz punkt końcowy, który chcesz dodać zastąpienie i zapisać go w zmiennej przy użyciu [get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Zastąp nazwę, nazwę profilu i nazwę grupy zasobów wartościami punktu końcowego, który się zmienia.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Dodaj zakres adresów IP do punktu końcowego:**
    
    Aby dodać zakres adresów IP do punktu końcowego, użyjesz [Add-AzTrafficManagerIpAddressRange,](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) aby dodać zakres.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    Po dodaniu zakresów należy użyć [Set-AzTrafficManagerEndpoint,](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) aby zaktualizować punkt końcowy.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
Usunięcie zakresu adresów IP można zakończyć za pomocą [Remove-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0).

1.  **Pobierz punkt końcowy programu Traffic Manager:**

    Aby włączyć zastępowanie podsieci, pobierz punkt końcowy, który chcesz dodać zastąpienie i zapisać go w zmiennej przy użyciu [get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Zastąp nazwę, nazwę profilu i nazwę grupy zasobów wartościami punktu końcowego, który się zmienia.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Usuń zakres adresów IP z punktu końcowego:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     Po usunięciu zakresów należy użyć [Set-AzTrafficManagerEndpoint,](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) aby zaktualizować punkt końcowy.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md)usługi Traffic Manager .

Dowiedz się więcej o [metodzie routingu ruchu podsieci](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)
