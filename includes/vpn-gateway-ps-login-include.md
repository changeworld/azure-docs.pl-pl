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
ms.openlocfilehash: 853bd32cf3ea97571929d54fb7d3ba04bde16b81
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66157449"
---
Przed rozpoczęciem tej konfiguracji, musisz zarejestrować się do konta platformy Azure. Polecenie cmdlet wyświetli monit o podanie poświadczeń logowania dla konta platformy Azure. Po zalogowaniu pobiera ono ustawienia konta, aby były dostępne w programie Azure PowerShell. Więcej informacji znajduje się w temacie [Using Windows PowerShell with Resource Manager](../articles/powershell-azure-resource-manager.md) (Używanie programu Windows PowerShell z usługą Resource Manager).

Aby się zarejestrować, otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i nawiąż połączenie z kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzAccount
```

Jeśli masz wiele subskrypcji platformy Azure, wyświetl subskrypcje dla konta.

```powershell
Get-AzSubscription
```

Wskaż subskrypcję, której chcesz użyć.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
