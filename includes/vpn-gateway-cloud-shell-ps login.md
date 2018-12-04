---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fee97b29f24d8bb4f50a2929c3ceb33af85a5e21
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852361"
---
Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień.



Jeśli używasz programu Azure PowerShell lokalnie, nawiąż połączenie z kontem platformy Azure. *Connect-AzureRmAccount* polecenie cmdlet wyświetli monit o podanie poświadczeń. Po uwierzytelnieniu, pobiera ono ustawienia konta, tak aby były dostępne dla programu Azure PowerShell. Jeśli nie uruchomiono programu PowerShell lokalnie, zamiast korzystania z usługi Azure Cloud Shell "Wypróbuj" w przeglądarce pominąć ten pierwszy krok. Połączysz się Twoje konto platformy Azure automatycznie.

```azurepowershell
Connect-AzureRmAccount
```

Jeśli masz więcej niż jedną subskrypcję, zostanie wyświetlona lista subskrypcji platformy Azure.

```azurepowershell-interactive
Get-AzureRmSubscription
```

Wskaż subskrypcję, której chcesz użyć.

```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```