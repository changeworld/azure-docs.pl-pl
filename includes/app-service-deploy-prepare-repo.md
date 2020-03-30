---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67836786"
---
## <a name="prepare-your-repository"></a>Przygotowanie repozytorium

Aby uzyskać automatyczne kompilacje z serwera kompilacji usługi Azure App Service Kudu, upewnij się, że katalog główny repozytorium ma poprawne pliki w projekcie.

| Środowisko uruchomieniowe | Główne pliki katalogów |
|-|-|
| ASP.NET (tylko windows) | _*.sln_, _*.csproj_lub _default.aspx_ |
| ASP.NET Core | _*.sln_ lub _*.csproj_ |
| PHP | _Index.php_ |
| Ruby (tylko Linux) | _Gemfile (plik gema)_ |
| Node.js | _server.js_, _app.js_, lub _package.json_ ze skryptem startowym |
| Python | .py , _requirements.txt_lub _runtime.txt_ _ \*_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_, lub _iisstart.htm_ |
| Zadania WebJob | _\<job_name>/uruchom. rozszerzenie \<>_ w obszarze Dane _\_aplikacji/zadania/ciągłe_ dla ciągłych webjobs lub dane _aplikacji/zadania/wyzwalane\__ dla wyzwalanych webjobs. Aby uzyskać więcej informacji, zobacz [dokumentację Kudu WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funkcje | Zobacz [Ciągłe wdrażanie dla usług Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Aby dostosować wdrożenie, dołącz plik *.deployment* w katalogu głównym repozytorium. Aby uzyskać więcej informacji, zobacz [Dostosowywanie wdrożeń](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) i [niestandardowy skrypt wdrażania](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Jeśli tworzysz w programie Visual Studio, niech [Visual Studio utworzyć repozytorium dla Ciebie.](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio) Projekt jest natychmiast gotowy do wdrożenia przy użyciu git.
>

