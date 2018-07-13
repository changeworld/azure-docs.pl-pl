---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: d8ef59b157dc01c50d96561df31bbca4a8505018
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38728908"
---
### <a name="sign-in-to-azure-cli"></a>Logowanie do interfejsu wiersza polecenia platformy Azure
Zaloguj się do platformy Azure. W oknie terminala wpisz następujące polecenie:

```cmd
az login
```

> [!Note]
> Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Jeśli masz wiele subskrypcji platformy Azure...
Możesz wyświetlić swoje subskrypcje, uruchamiając polecenie: 

```cmd
az account list
```
Znajdź subskrypcję, która w danych wyjściowych JSON ma wartość `isDefault: true`.
Jeśli nie jest to subskrypcja, której chcesz użyć, możesz zmienić subskrypcję domyślną:

```cmd
az account set --subscription <subscription ID>
```
