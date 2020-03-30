---
title: Szybki start:Tworzenie profilu dla wysokiej dostępności aplikacji — Azure PowerShell — Usługa Azure Traffic Manager
description: W tym artykule przewodnika Szybki start opisano sposób tworzenia profilu usługi Traffic Manager w celu utworzenia aplikacji sieci web o wysokiej dostępności.
services: traffic-manager
author: rohinkoul
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2019
ms.author: rohink
ms.openlocfilehash: 0ab7392b4fa6e248d51392706fedaed156344a99
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76934815"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Szybki start: tworzenie profilu usługi Traffic Manager dla aplikacji sieci Web o wysokiej dostępności przy użyciu programu Azure PowerShell

W tym przewodniku Szybki start opisano tworzenie profilu usługi Traffic Manager, który zapewni wysoką dostępność aplikacji internetowej.

W tym przewodniku Szybki start utworzysz dwa wystąpienia aplikacji sieci web. Każde z nich jest uruchamiane w innym regionie świadczenia usługi Azure. Utworzysz profil usługi Traffic Manager na podstawie [priorytetu punktu końcowego](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profil kieruje ruch użytkowników do lokacji głównej, w której działa aplikacja internetowa. Usługa Traffic Manager stale monitoruje aplikację internetową. Jeśli lokacja główna jest niedostępna, usługa zapewnia automatyczne przejście w tryb failover w lokacji zapasowej.

Jeśli nie masz subskrypcji platformy Azure, utwórz teraz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten artykuł wymaga modułu Azure PowerShell w wersji 5.4.1 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Tworzenie grupy zasobów przy użyciu [programu New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive


# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Utwórz profil usługi Traffic Manager przy użyciu [new-AzTrafficManagerProfile,](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) który kieruje ruch użytkownika na podstawie priorytetu punktu końcowego.

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

W przypadku tego przewodnika Szybki start potrzebne będą dwa wystąpienia aplikacji sieci web wdrożone w dwóch różnych regionach platformy Azure (*zachodnie stany USA* i *wschodnie stany USA).* Każda będzie służyć jako podstawowy punkt końcowy i punkt końcowy trybu failover dla usługi Traffic Manager.

### <a name="create-web-app-service-plans"></a>Tworzenie planów usługi aplikacji sieci Web
Tworzenie planów usługi aplikacji sieci Web przy użyciu [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) dla dwóch wystąpień aplikacji sieci web, które zostaną wdrożone w dwóch różnych regionach platformy Azure.

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
### <a name="create-a-web-app-in-the-app-service-plan"></a>Tworzenie aplikacji sieci Web w planie usługi aplikacji
Utwórz dwa wystąpienia aplikacji sieci web przy użyciu [aplikacji New-AzWebApp](/powershell/module/az.websites/new-azwebapp) w planach usługi app service w regionach *zachodniego stanu USA* i *wschodnich* stanów USA na platformie Azure.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager
Dodaj dwie aplikacje sieci Web jako punkty końcowe usługi Traffic Manager przy użyciu [new-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) do profilu usługi Traffic Manager w następujący sposób:
- Dodaj aplikację sieci Web znajdującą się w regionie West *US* Azure jako podstawowy punkt końcowy, aby rozsyłać cały ruch użytkownika. 
- Dodaj aplikację sieci Web znajdującą się w regionie Wschodnia platforma Azure *stanów USA* jako punkt końcowy trybu failover. Gdy podstawowy punkt końcowy będzie niedostępny, ruch będzie automatycznie kierowany do punktu końcowego trybu failover.

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

Określ nazwę DNS profilu usługi Traffic Manager przy użyciu [programu Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Skopiuj wartość **RelativeDnsName.** Nazwa DNS profilu usługi Traffic Manager to *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Wyświetlanie informacji o działaniu usługi Traffic Manager
1. W przeglądarce internetowej wprowadź nazwę DNS profilu usługi Traffic Manager (*http://<* relativednsname *>.trafficmanager.net*), aby wyświetlić domyślną witrynę sieci Web aplikacji.

    > [!NOTE]
    > W tym scenariuszu Szybki start wszystkie żądania są kierowane do podstawowego punktu końcowego. Jest on ustawiony na wartość **Priorytet 1**.
2. Aby wyświetlić usługę pracy awaryjnej usługi Traffic Manager w akcji, należy wyłączyć witrynę główną przy użyciu [funkcji Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint).

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Skopiuj nazwę DNS profilu usługi Traffic Manager (*http://<* relativednsname *>.trafficmanager.net*), aby wyświetlić witrynę sieci Web w nowej sesji przeglądarki internetowej.
4. Sprawdź, czy aplikacja internetowa jest nadal dostępna.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu usuń grupy zasobów, aplikacje sieci Web i wszystkie powiązane zasoby za pomocą [programu Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono profil usługi Traffic Manager, który zapewnia wysoką dostępność dla aplikacji sieci web. Aby dowiedzieć się więcej o kierowaniu ruchu, przejdź do samouczków usługi Traffic Manager.

> [!div class="nextstepaction"]
> [Samouczki usługi Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
