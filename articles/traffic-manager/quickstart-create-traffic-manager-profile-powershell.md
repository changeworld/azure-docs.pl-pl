---
title: Przewodnik Szybki Start — Tworzenie profilu usługi Traffic Manager w celu zapewnienia wysokiej dostępności aplikacji przy użyciu programu Azure PowerShell
description: W tym artykule Szybki Start opisano, jak utworzyć profil usługi Traffic Manager do tworzenia aplikacji sieci web o wysokiej dostępności.
services: traffic-manager
author: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2019
ms.author: kumud
ms.openlocfilehash: f6ed2e03352a335022d99cf703240552fa34e732
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729012"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Szybki start: Utwórz profil usługi Traffic Manager dla aplikacji sieci web o wysokiej dostępności przy użyciu programu Azure PowerShell

W tym przewodniku Szybki start opisano tworzenie profilu usługi Traffic Manager, który zapewni wysoką dostępność aplikacji internetowej.

W tym przewodniku Szybki Start utworzysz dwa wystąpienia aplikacji sieci web. Każde z nich jest uruchamiane w innym regionie świadczenia usługi Azure. Utworzysz profil usługi Traffic Manager na podstawie [priorytetu punktu końcowego](traffic-manager-routing-methods.md#priority). Profil kieruje ruch użytkowników do lokacji głównej, w której działa aplikacja internetowa. Usługa Traffic Manager stale monitoruje aplikację internetową. Jeśli lokacja główna jest niedostępna, usługa zapewnia automatyczne przejście w tryb failover w lokacji zapasowej.

Jeśli nie masz subskrypcji platformy Azure, utwórz teraz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten artykuł wymaga modułu Azure PowerShell w wersji 5.4.1 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów za pomocą [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive


# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Tworzenie profilu usługi Traffic Manager za pomocą [New AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) która kieruje ruch użytkowników tak, na podstawie priorytetu punktu końcowego.

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>Tworzenie aplikacji sieci Web

W tym przewodniku Szybki Start będą potrzebne dwa wystąpienia aplikacji sieci web wdrożona w dwóch różnych regionach platformy Azure (*zachodnie stany USA* i *wschodnie stany USA*). Każda będzie służyć jako podstawowy punkt końcowy i punkt końcowy trybu failover dla usługi Traffic Manager.

### <a name="create-web-app-service-plans"></a>Utwórz plany usługi aplikacji sieci Web
Tworzenie aplikacji sieci Web przy użyciu planów service [New AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) dwóch wystąpień aplikacji sieci web, która zostanie wdrożona w dwóch różnych regionach platformy Azure.

```azurepowershell-interactive

# Variables
$App1Name="AppServiceTM1$Random"
$App2Name="AppServiceTM2$Random"
$Location1="WestUS"
$Location2="EastUS"

# Create an App service plan
New-AzAppservicePlan -Name "$App1Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "$App2Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>Tworzenie aplikacji internetowej w planie usługi App Service
Utwórz dwa wystąpienia aplikacji sieci web przy użyciu [New AzWebApp](/powershell/module/az.websites/new-azwebapp) w usłudze App Service planów w *zachodnie stany USA* i *wschodnie stany USA* regiony platformy Azure.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager
Dodaj dwie aplikacje sieci Web jako punktów końcowych usługi Traffic Manager przy użyciu [New AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) usłudze Traffic Manager profil w następujący sposób:
- Dodawanie aplikacji sieci Web, znajduje się w *zachodnie stany USA* region platformy Azure jako podstawowego punktu końcowego do kierowania ruchu użytkowników. 
- Dodawanie aplikacji sieci Web, znajduje się w *wschodnie stany USA* region platformy Azure jako punktu końcowego trybu failover. Gdy podstawowy punkt końcowy będzie niedostępny, ruch będzie automatycznie kierowany do punktu końcowego trybu failover.

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "$App1Name-$Location1" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "$App2Name-$Location2" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>Testowanie profilu usługi Traffic Manager

W tej sekcji sprawdzisz nazwę domeny profilu usługi Traffic Manager. Skonfigurujesz również podstawowy punkt końcowy tak, aby był niedostępny. Na końcu zobaczysz, że aplikacja internetowa jest nadal dostępna. Dzieje się tak, ponieważ usługa Traffic Manager wysyła ruch do punktu końcowego trybu failover.

### <a name="determine-the-dns-name"></a>Ustalanie nazwy DNS

Określanie nazwy DNS przy użyciu profilu usługi Traffic Manager [Get AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Kopiuj **RelativeDnsName** wartości. Nazwa DNS profilu usługi Traffic Manager to *http://<* relativednsname *>. trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Wyświetlanie informacji o działaniu usługi Traffic Manager
1. W przeglądarce internetowej, wprowadź nazwę DNS profilu usługi Traffic Manager (*http://<* relativednsname *>. trafficmanager.net*) aby wyświetlić aplikację sieci Web domyślnej witryny sieci Web.

    > [!NOTE]
    > W tym scenariuszu Szybki start wszystkie żądania są kierowane do podstawowego punktu końcowego. Jest on ustawiony na wartość **Priorytet 1**.
2. Aby wyświetlić usługi Traffic Manager w tryb failover w akcji, wyłącz swojej lokacji głównej przy użyciu [AzTrafficManagerEndpoint Wyłącz](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint).

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Skopiuj nazwę DNS profilu usługi Traffic Manager (*http://<* relativednsname *>. trafficmanager.net*) do wyświetlania witryny sieci Web w nowej sesji przeglądarki sieci web.
4. Sprawdź, czy aplikacja internetowa jest nadal dostępna.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy wszystko będzie gotowe, Usuń grupy zasobów, aplikacji sieci web i wszystkie pokrewne zasoby za pomocą [AzResourceGroup Usuń](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start utworzono profil usługi Traffic Manager, która zapewnia wysoką dostępność dla aplikacji sieci web. Aby dowiedzieć się więcej o kierowaniu ruchu, przejdź do samouczków usługi Traffic Manager.

> [!div class="nextstepaction"]
> [Samouczki usługi Traffic Manager](tutorial-traffic-manager-improve-website-response.md)