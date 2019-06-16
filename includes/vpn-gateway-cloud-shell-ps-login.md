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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66146940"
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