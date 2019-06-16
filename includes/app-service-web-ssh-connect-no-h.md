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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66138226"
---
Aby Otwórz bezpośrednie sesję SSH z kontenera, aplikacja powinna być uruchomiona.

Wklej następujący adres URL do przeglądarki i Zastąp \<app-name > nazwą swojej aplikacji:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Jeśli jeszcze nie jest uwierzytelniony, są wymagane do uwierzytelniania przy użyciu subskrypcji platformy Azure, aby połączyć. Po uwierzytelnieniu zobaczysz shell w przeglądarce, gdzie możesz uruchamiać polecenia wewnątrz kontenera.

![Połączenie SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
