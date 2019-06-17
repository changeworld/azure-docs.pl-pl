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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66119392"
---
 Jeśli używasz usługi Azure Cloud Shell, możesz Zaloguj się do konta platformy Azure automatycznie po kliknięciu przycisku "Wypróbuj". Aby zalogować się lokalnie, otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i uruchom polecenie cmdlet, aby połączyć.

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