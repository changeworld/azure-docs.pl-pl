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
ms.openlocfilehash: e5ba08da5c58ff486bc26f2c771dfef55452629a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75681055"
---
W lokalnym oknie terminala dodaj zdalną platformę Azure do lokalnego repozytorium Git. Zastąp * \<deploymentLocalGitUrl-from-create-step>* adres url pilota Git [zapisanego](#create-a-web-app)w aplikacji Tworzenie aplikacji sieci Web .

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Gdy Menedżer poświadczeń Git monituje o poświadczenia, upewnij się, że wprowadzono poświadczenia utworzone w [konfigurowaniu użytkownika wdrożenia,](/azure/app-service/containers/tutorial-python-postgresql-app#configure-a-deployment-user)a nie poświadczenia używane do logowania się do witryny Azure portal.

```bash
git push azure master
```

Wykonanie tego polecenia może potrwać kilka minut. Podczas wykonywania polecenie wyświetli informacje podobne do następującego przykładu:
