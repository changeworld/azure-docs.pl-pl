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
ms.openlocfilehash: 95d89da66935ce933fee082a5f53ee2e36ea953f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344690"
---
W usłudze Cloud Shell skonfiguruj poświadczenia wdrożenia za pomocą [ `az webapp deployment user set` ](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) polecenia. Ten użytkownik wdrożenia jest wymagany do wdrożenia protokołu FTP i lokalnego narzędzia Git w aplikacji internetowej. Nazwa użytkownika i hasło są określane na poziomie konta. _Różnią się one od poświadczeń subskrypcji platformy Azure._

W poniższym przykładzie zastąp ciągi *\<username>* i *\<password>* (razem z nawiasami) nową nazwą użytkownika i hasłem. Nazwa użytkownika musi być unikatowa w obrębie platformy Azure. Hasło musi mieć długość co najmniej ośmiu znaków i zawierać dwa z następujących trzech elementów: litery, cyfry, symbole. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Powinny zostać uzyskane dane wyjściowe JSON z hasłem wyświetlanym jako `null`. Jeśli wystąpił błąd `'Conflict'. Details: 409`, zmień nazwę użytkownika. Jeśli wystąpił błąd ` 'Bad Request'. Details: 400`, użyj silniejszego hasła.

Należy skonfigurować tylko raz, a ten użytkownik wdrożenia można go używać we wszystkich wdrożeniach platformy Azure.

> [!NOTE]
> Zapisz nazwę użytkownika i hasło. Będą one potrzebne później do wdrożenia aplikacji internetowej.
>
>
