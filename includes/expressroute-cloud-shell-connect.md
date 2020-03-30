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
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67182964"
---
 Jeśli używasz usługi Azure Cloud Shell, logujesz się do konta platformy Azure automatycznie po kliknięciu przycisku "Wypróbuj". Aby zalogować się lokalnie, otwórz konsolę programu PowerShell z podwyższonymi uprawnieniami i uruchom polecenie cmdlet, aby się połączyć.

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