---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 50ce8530aca40eed07741f35be1a57bbd7cc1868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77133606"
---
Otwórz konsolę programu PowerShell z podwyższonymi uprawnieniami.

Jeśli korzystasz z usługi Azure PowerShell lokalnie, połącz się ze swoim kontem platformy Azure. Polecenie cmdlet *Connect-AzAccount* monituje o podanie poświadczeń. Po uwierzytelnieniu pobiera ustawienia konta, dzięki czemu są one dostępne dla programu Azure PowerShell. Jeśli używasz usługi Azure Cloud Shell zamiast tego, nie trzeba uruchamiać *Connect-AzAccount*. Usługa Azure Cloud Shell automatycznie łączy się z kontem platformy Azure.

```azurepowershell
Connect-AzAccount
```

Jeśli masz więcej niż jedną subskrypcję, pobierz listę subskrypcji platformy Azure.

```azurepowershell-interactive
Get-AzSubscription
```

Wskaż subskrypcję, której chcesz użyć.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```