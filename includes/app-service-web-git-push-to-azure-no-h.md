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
ms.openlocfilehash: aa6f18d4f667862687083c5db3679ce9d8e188cd
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2019
ms.locfileid: "56213132"
---
W _lokalnym oknie terminala_ dodaj zdalną platformę Azure do lokalnego repozytorium Git. Zastąp ciąg _&lt;deploymentLocalGitUrl-from-create-step>_ adresem URL zdalnego repozytorium Git zapisanym w sekcji [Tworzenie aplikacji internetowej](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Gdy w Menedżerze poświadczeń repozytorium Git zostanie wyświetlony monit o podanie poświadczeń, upewnij się, że wprowadzasz poświadczenia utworzone podczas konfiguracji użytkownika wdrożenia, a nie poświadczenia, których używasz do logowania się w witrynie Azure Portal.

```bash
git push azure master
```

Wykonanie tego polecenia może potrwać kilka minut. Podczas wykonywania polecenie wyświetli informacje podobne do następującego przykładu:
