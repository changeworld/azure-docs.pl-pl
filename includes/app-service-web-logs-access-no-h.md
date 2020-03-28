---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183707"
---
Można uzyskać dostęp do dzienników konsoli generowanych z wewnątrz kontenera. Najpierw włącz rejestrowanie kontenerów, uruchamiając następujące polecenie w usłudze Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Po włączeniu rejestrowania kontenerów uruchom następujące polecenie, aby wyświetlić strumień dziennika:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Jeśli nie widzisz dzienników konsoli, sprawdź ponownie w ciągu 30 sekund.

> [!NOTE]
> Można również sprawdzić pliki dziennika z `https://<app-name>.scm.azurewebsites.net/api/logs/docker`przeglądarki w .

Aby zatrzymać przesyłanie strumieniowe `Ctrl` + `C`dziennika w dowolnym momencie, wpisz .
