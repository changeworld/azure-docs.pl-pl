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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67836912"
---
Ftp i lokalny Git można wdrożyć w aplikacji sieci web platformy Azure przy użyciu *użytkownika wdrożenia.* Po skonfigurowaniu użytkownika wdrożenia, można go używać dla wszystkich wdrożeń platformy Azure. Nazwa użytkownika i hasło wdrożenia na poziomie konta różnią się od poświadczeń subskrypcji platformy Azure. 

Aby skonfigurować użytkownika wdrażania, uruchom polecenie zestawu zestawu wdrażania aplikacji [az webapp](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) w usłudze Azure Cloud Shell. Zastąp \<> \<nazwy użytkownika i hasła> nazwą użytkownika wdrożenia i hasłem. 

- Nazwa użytkownika musi być unikatowa na platformie Azure, a dla lokalnych wypychań Git nie może zawierać symbolu "@". 
- Hasło musi mieć co najmniej osiem znaków, z dwoma z następujących trzech elementów: literami, cyframi i symbolami. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Dane wyjściowe JSON `null`pokazuje hasło jako . Jeśli wystąpił błąd `'Conflict'. Details: 409`, zmień nazwę użytkownika. Jeśli wystąpił błąd `'Bad Request'. Details: 400`, użyj silniejszego hasła. 

Zarejestruj swoją nazwę użytkownika i hasło do wdrożenia aplikacji internetowych.
