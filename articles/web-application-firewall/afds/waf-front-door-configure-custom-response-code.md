---
title: Konfigurowanie niestandardowej odpowiedzi dla zapory aplikacji sieci Web przy użyciu usług frontonu platformy Azure
description: Dowiedz się, jak skonfigurować niestandardowy kod odpowiedzi i komunikat, gdy Zapora aplikacji sieci Web (WAF) blokuje żądanie.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: ae3099373bbaece57c2896eb0fb4e7d59d817f25
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517152"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Konfigurowanie niestandardowej odpowiedzi dla zapory aplikacji sieci Web platformy Azure

Domyślnie, gdy Zapora aplikacji sieci Web platformy Azure (WAF) z systemem Azure front-drzwi blokuje żądanie ze względu na dopasowaną regułę, zwraca kod stanu 403 z **żądaniem jest zablokowany** komunikat. W tym artykule opisano sposób konfigurowania niestandardowego kodu stanu odpowiedzi i komunikatu odpowiedzi, gdy żądanie jest blokowane przez WAF.

## <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell
Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

Możesz zainstalować program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się przy użyciu poświadczeń platformy Azure, i zainstaluj moduł AZ PowerShell module.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Nawiązywanie połączenia z platformą Azure przy użyciu interaktywnego okna dialogowego logowania
```
Connect-AzAccount
Install-Module -Name Az
```
Upewnij się, że masz zainstalowaną bieżącą wersję programu PowerShellGet. Uruchom poniższe polecenie i ponownie otwórz program PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Zainstaluj AZ. Usługa frontdoor module 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. W tym przykładzie utworzysz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Utwórz nowe zasady WAF z odpowiedzią niestandardową 

Poniżej znajduje się przykład tworzenia nowych zasad WAF z niestandardowym kodem stanu odpowiedzi ustawionym na 405, a komunikat **jest blokowany.** przy użyciu polecenia [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

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

Modyfikowanie niestandardowego kodu odpowiedzi lub ustawień treści odpowiedzi istniejących zasad WAF przy użyciu polecenia [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

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
- Dowiedz się więcej o [zaporze aplikacji sieci Web w usłudze Azure front-drzwi](../afds/afds-overview.md)