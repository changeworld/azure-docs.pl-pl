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
ms.openlocfilehash: 463e8b0122339831d5d6a65b6e41d4f697a82013
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38731927"
---
W usłudze Cloud Shell utwórz poświadczenia wdrożenia za pomocą polecenia [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az_webapp_deployment_user_set). Ten użytkownik wdrożenia jest wymagany do wdrożenia protokołu FTP i lokalnego narzędzia Git w aplikacji internetowej. Nazwa użytkownika i hasło są określane na poziomie konta. _Różnią się one od poświadczeń subskrypcji platformy Azure._

W poniższym przykładzie zastąp ciągi *\<username>* i *\<password>* (razem z nawiasami) nową nazwą użytkownika i hasłem. Nazwa użytkownika musi być unikatowa w obrębie platformy Azure. Hasło musi mieć długość co najmniej ośmiu znaków i zawierać dwa z następujących trzech elementów: litery, cyfry, symbole. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Powinny zostać uzyskane dane wyjściowe JSON z hasłem wyświetlanym jako `null`. Jeśli wystąpił błąd `'Conflict'. Details: 409`, zmień nazwę użytkownika. Jeśli wystąpił błąd ` 'Bad Request'. Details: 400`, użyj silniejszego hasła.

Ten użytkownik wdrożenia jest tworzony tylko jeden raz. Można go używać we wszystkich wdrożeniach platformy Azure.

> [!NOTE]
> Zapisz nazwę użytkownika i hasło. Będą one potrzebne później do wdrożenia aplikacji internetowej.
>
>
