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
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 2d16893420f27caf4f8b00dc32069e3296d7c236
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459780"
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

Poniżej znajduje się przykład tworzenia nowych zasad zapory aplikacji sieci Web z kodem stanu odpowiedzi niestandardowej równa 405 i komunikat **są zablokowane.** za pomocą [New AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy).

```azurepowershell
# WAF policy setting
New-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Zmodyfikuj kod odpowiedzi niestandardowe lub ustawienia treści odpowiedzi w istniejących zasad zapory aplikacji sieci Web, za pomocą [AzFrontDoor zestaw](/powershell/module/az.frontdoor/set-azfrontdoor).

```azurepowershell
# modify WAF response code
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [drzwi](front-door-overview.md)