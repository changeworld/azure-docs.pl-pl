---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183710"
---
Aby otworzyć bezpośrednią sesję SSH z kontenerem, aplikacja powinna być uruchomiona.

Wklej następujący adres URL do \<przeglądarki i zastąp nazwę aplikacji> nazwą aplikacji:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Jeśli nie jesteś jeszcze uwierzytelniony, musisz uwierzytelnić się za pomocą subskrypcji platformy Azure, aby nawiązać połączenie. Po uwierzytelnieniu zobaczysz powłokę w przeglądarce, w której można uruchamiać polecenia wewnątrz kontenera.

![Połączenie SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
