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
ms.openlocfilehash: f76dd47081a826e341d15fedc583d29f0373e475
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197690"
---
Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [az login](/cli/azure/#login) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie. Aby uzyskać więcej informacji na temat logowania się, zobacz artykuł [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure 2.0](/cli/azure/get-started-with-azure-cli).

```azurecli
az login
```

Jeśli masz więcej niż jedną subskrypcję platformy Azure, wyświetl subskrypcje dla konta.

```azurecli
az account list --all
```

Wskaż subskrypcję, której chcesz użyć.

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```