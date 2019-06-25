---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: f6ebc922cd2b39208ee13e1ec7d0e975197cae69
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183715"
---
Utwórz [aplikację internetową](../articles/app-service/containers/app-service-linux-intro.md) w planie usługi App Service `myAppServicePlan`. 

W usłudze Cloud Shell można użyć polecenia [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest). W poniższym przykładzie zastąp ciąg `<app-name>` globalnie unikatową nazwą aplikacji (prawidłowe znaki to `a-z`, `0-9` i `-`). Środowisko uruchomieniowe ma ustawioną wartość `PHP|7.0`. Aby wyświetlić wszystkie obsługiwane środowiska uruchomieniowe, uruchom polecenie [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest). 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.0" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.0" --deployment-local-git
```

Po utworzeniu aplikacji internetowej w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone dane wyjściowe podobne do następujących:

```json
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Utworzona została nowa pusta aplikacja internetowa z włączonym wdrażaniem git.

> [!NOTE]
> Adres URL zdalnego repozytorium Git jest wyświetlany we właściwości `deploymentLocalGitUrl` w formacie `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Zapisz ten adres URL, ponieważ będzie on potrzebny później.
>
