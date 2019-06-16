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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66137321"
---
Dzienniki konsoli z on wygenerowany wewnątrz kontenera możesz uzyskać dostęp. Najpierw należy włączyć rejestrowanie kontenerów przez uruchomienie następującego polecenia w usłudze Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Po włączeniu rejestrowania kontenerów uruchom następujące polecenie, aby wyświetlić strumień dziennika:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Jeśli nie widzisz dzienników konsoli, sprawdź ponownie w ciągu 30 sekund.

> [!NOTE]
> Można też sprawdzić pliki dziennika z przeglądarki na `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Aby zatrzymać przesyłanie strumieniowe dzienników w dowolnym momencie, wpisz `Ctrl` + `C`.
