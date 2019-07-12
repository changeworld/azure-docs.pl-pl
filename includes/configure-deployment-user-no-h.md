---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4e699707db02de07f3d1ebb7d1fa8d0575a10aa3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836912"
---
FTP i lokalnego narzędzia Git można wdrożyć aplikację internetową platformy Azure przy użyciu *użytkownika wdrożenia*. Po skonfigurowaniu użytkownika wdrożenia, można użyć go we wszystkich wdrożeniach platformy Azure. Nazwy wdrażania na poziomie konta użytkownika i hasła różnią się od poświadczeń subskrypcji platformy Azure. 

Aby skonfigurować użytkownika wdrożenia, uruchom [Ustaw użytkownika wdrożenia aplikacji sieci Web az](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) polecenia w usłudze Azure Cloud Shell. Zastąp \<username > i \<hasło > Nazwa użytkownika wdrożenia użytkownika i hasłem. 

- Nazwa użytkownika musi być unikatowa na platformie Azure, i dla lokalnego narzędzia Git nie może zawierać wypchnięciom, ' @' symboli. 
- Hasło musi mieć co najmniej ośmiu znaków i zawierać dwa z następujących trzech elementów: litery, cyfry i symbole. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Dane wyjściowe JSON zawiera hasło jako `null`. Jeśli wystąpił błąd `'Conflict'. Details: 409`, zmień nazwę użytkownika. Jeśli wystąpił błąd `'Bad Request'. Details: 400`, użyj silniejszego hasła. 

Zapisz nazwę użytkownika i hasło służące do wdrażania aplikacji sieci web.
