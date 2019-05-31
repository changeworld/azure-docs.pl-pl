---
title: Skonfiguruj niestandardowe odpowiedzi dla zapory aplikacji sieci web na platformie Azure drzwi
description: Dowiedz się, jak skonfigurować kod niestandardowy odpowiedzi i komunikat o blokowaniu przez zaporę aplikacji sieci web (WAF) żądania.
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: tyao;kumud
ms.openlocfilehash: d6d73055abe972cd3b6fee253b6bdb2b082ceca8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242986"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Skonfiguruj niestandardowe odpowiedzi dla zapory aplikacji sieci web platformy Azure

Domyślnie blokowaniu przez zaporę aplikacji sieci web platformy Azure (WAF) za pomocą usługi Azure drzwiami frontowymi żądania z powodu dopasowane reguły zwraca kod stanu 403 z **żądania jest zablokowany** wiadomości. W tym artykule opisano sposób konfigurowania kodu stanu odpowiedzi niestandardowych i komunikat odpowiedzi, gdy żądanie jest blokowany przez zaporę aplikacji sieci Web.

## <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell
Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

Możesz zainstalować program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się przy użyciu swoich poświadczeń platformy Azure, a następnie zainstalować moduł Az PowerShell.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Łączenie z platformą Azure za pomocą interakcyjne okno dialogowe logowania
```
Connect-AzAccount
Install-Module -Name Az
```
Upewnij się, że masz bieżącą wersję zainstalowanego modułu PowerShellGet. Uruchom poniższe polecenie i ponownie otwórz program PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Zainstaluj moduł Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. W tym przykładzie utworzysz grupę zasobów za pomocą [New AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Utwórz nowe zasady zapory aplikacji sieci Web przy użyciu niestandardowych odpowiedzi 

Poniżej znajduje się przykład tworzenia nowych zasad zapory aplikacji sieci Web z kodem stanu odpowiedzi niestandardowej równa 405 i komunikat **są zablokowane.** za pomocą [New AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Zmodyfikuj kod odpowiedzi niestandardowe lub ustawienia treści odpowiedzi w istniejących zasad zapory aplikacji sieci Web, za pomocą [AzFrontDoorFireWallPolicy aktualizacji ](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [drzwi](front-door-overview.md)