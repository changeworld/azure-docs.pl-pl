---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: deabef0c2c3540e515fe72a161710c95a20fa86f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183094"
---
Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień.



Jeśli używasz programu Azure PowerShell lokalnie, nawiąż połączenie z kontem platformy Azure. *Connect AzAccount* polecenie cmdlet wyświetli monit o podanie poświadczeń. Po uwierzytelnieniu, pobiera ono ustawienia konta, tak aby były dostępne dla programu Azure PowerShell. Jeśli nie uruchomiono programu PowerShell lokalnie, zamiast korzystania z usługi Azure Cloud Shell "Wypróbuj" w przeglądarce pominąć ten pierwszy krok. Połączysz się Twoje konto platformy Azure automatycznie.

```azurepowershell
Connect-AzAccount
```

Jeśli masz więcej niż jedną subskrypcję, zostanie wyświetlona lista subskrypcji platformy Azure.

```azurepowershell-interactive
Get-AzSubscription
```

Wskaż subskrypcję, której chcesz użyć.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```