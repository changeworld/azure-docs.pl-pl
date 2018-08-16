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
ms.openlocfilehash: 875dd9d768b5f40f2d4ed3fad5b14a6cd6f6f6ba
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129371"
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
