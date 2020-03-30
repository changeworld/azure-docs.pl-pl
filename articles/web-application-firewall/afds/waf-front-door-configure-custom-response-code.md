---
title: Konfigurowanie niestandardowej odpowiedzi dla usługi WAF za pomocą drzwi ekwiomowych platformy Azure
description: Dowiedz się, jak skonfigurować niestandardowy kod odpowiedzi i komunikat, gdy Zapora aplikacji sieci Web (WAF) blokuje żądanie.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 215d4058937ad5fded6bef7a36e873b52a1b5ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185342"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Konfigurowanie niestandardowej odpowiedzi dla Zapory aplikacji sieci Web platformy Azure

Domyślnie, gdy Zapora aplikacji sieci Web platformy Azure (WAF) z usługą Azure Front Door blokuje żądanie z powodu dopasowanej reguły, zwraca kod stanu 403 z **komunikatem Żądanie jest blokowane.** W tym artykule opisano sposób konfigurowania niestandardowego kodu stanu odpowiedzi i komunikatu odpowiedzi, gdy żądanie jest blokowane przez waf.

## <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell
Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

Możesz zainstalować program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się przy użyciu poświadczeń platformy Azure i zainstalować moduł Az PowerShell.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Łączenie się z platformą Azure za pomocą interaktywnego okna dialogowego logowania
```
Connect-AzAccount
Install-Module -Name Az
```
Upewnij się, że masz zainstalowaną bieżącą wersję programu PowerShellGet. Uruchom poniższe polecenie i ponownie otwórz program PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Instalowanie modułu Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. W tym przykładzie należy utworzyć grupę zasobów przy użyciu [programu New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Tworzenie nowej zasady WAF z niestandardową odpowiedzią 

Poniżej znajduje się przykład tworzenia nowych zasad WAF z kodem stanu odpowiedzi niestandardowej ustawioną na 405 i komunikatem do **Ciebie są zablokowane.** za pomocą [programu New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

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

Zmodyfikuj niestandardowy kod odpowiedzi lub ustawienia treści odpowiedzi istniejących zasad WAF, używając [update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

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

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [Zaporze aplikacji sieci Web za pomocą drzwi ekwigów azure](../afds/afds-overview.md)