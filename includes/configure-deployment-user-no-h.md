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
ms.openlocfilehash: cd7fc7487a41979f37c9a55baeb0b8e172e808c4
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59587117"
---
W usłudze Azure Cloud Shell skonfiguruj poświadczenia wdrożenia za pomocą [ `az webapp deployment user set` ](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) polecenia. Ten użytkownik wdrożenia jest wymagany do wdrożenia protokołu FTP i lokalnego narzędzia Git w aplikacji internetowej. Nazwa użytkownika i hasło są na poziomie konta. _Są one różne od poświadczeń subskrypcji platformy Azure._

W poniższym przykładzie Zastąp  *\<username >* i  *\<hasło >*, razem z nawiasami, Nowa nazwa użytkownika i hasło. Nazwa użytkownika musi być unikatowa w obrębie platformy Azure. Hasło musi mieć co najmniej ośmiu znaków i zawierać dwa z następujących trzech elementów: litery, cyfry i symbole. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Otrzymasz dane wyjściowe JSON z hasłem wyświetlanym jako `null`. Jeśli wystąpił błąd `'Conflict'. Details: 409`, zmień nazwę użytkownika. Jeśli wystąpił błąd `'Bad Request'. Details: 400`, użyj silniejszego hasła. Nazwa użytkownika wdrożenia nie może zawierać "@" symboli dla wypycha lokalnego narzędzia Git.

Ten użytkownik wdrożenia można skonfigurować tylko jeden raz. Można go używać we wszystkich wdrożeniach platformy Azure.

> [!NOTE]
> Zapisz nazwę użytkownika i hasło. Będą one potrzebne później do wdrożenia aplikacji internetowej.
>
>
