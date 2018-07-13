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
ms.openlocfilehash: 9f865897ee478f25a44fe876d44aec253e84eb62
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38731916"
---
W _lokalnym oknie terminala_ dodaj zdalną platformę Azure do lokalnego repozytorium Git. Zastąp ciąg _&lt;deploymentLocalGitUrl-from-create-step>_ adresem URL zdalnego repozytorium Git zapisanym w sekcji [Tworzenie aplikacji internetowej](#create).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Gdy w Menedżerze poświadczeń repozytorium Git zostanie wyświetlony monit o podanie poświadczeń, upewnij się, że wprowadzasz poświadczenia utworzone podczas [konfiguracji użytkownika wdrożenia](#configure-a-deployment-user), a nie poświadczenia, których używasz do logowania się w witrynie Azure Portal.

```bash
git push azure master
```

Wykonanie tego polecenia może potrwać kilka minut. Podczas wykonywania polecenie wyświetli informacje podobne do następującego przykładu:
