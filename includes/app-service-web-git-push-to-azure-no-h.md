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
ms.openlocfilehash: 125561d61afe0fb7f704144efa1c8c20ecf03db1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
ms.locfileid: "29821049"
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
