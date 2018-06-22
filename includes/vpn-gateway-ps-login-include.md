---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d4d370e6b76fcfc502366642842bfeb923a13991
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313680"
---
Przed rozpoczęciem tej konfiguracji, należy zalogować się do konta platformy Azure. Polecenie cmdlet wyświetli monit o poświadczenia logowania dla konta platformy Azure. Po zalogowaniu pobraniu ustawienia konta tak, aby były dostępne dla programu Azure PowerShell. Więcej informacji znajduje się w temacie [Using Windows PowerShell with Resource Manager](../articles/powershell-azure-resource-manager.md) (Używanie programu Windows PowerShell z usługą Resource Manager).

Aby się zarejestrować, otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się z kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzureRmAccount
```

Jeśli masz wiele subskrypcji platformy Azure, wyświetl subskrypcje dla konta.

```powershell
Get-AzureRmSubscription
```

Wskaż subskrypcję, której chcesz użyć.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
