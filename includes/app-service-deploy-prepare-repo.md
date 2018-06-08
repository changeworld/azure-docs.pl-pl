---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/05/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 1c2a4f1e463fff278981de2297662a94cca8944e
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850814"
---
## <a name="prepare-your-repository"></a>Przygotowanie repozytorium

Aby uzyskać automatyczne kompilacji z serwera kompilacji Kudu usługi aplikacji, upewnij się, że katalogu głównym repozytorium ma poprawne pliki w projekcie.

| Środowisko uruchomieniowe | Plików z katalogu głównego |
|-|-|
| ASP.NET (tylko system Windows) | _*.sln_, _*.csproj_, lub _default.aspx_ |
| ASP.NET Core | _*.sln_ lub _*.csproj_ |
| PHP | _index.php_ |
| Ruby (tylko w systemie Linux) | _Gemfile_ |
| Node.js | _Server.js_, _app.js_, lub _package.json_ przy użyciu skryptu start |
| Python (tylko system Windows) | _\*.PY_, _requirements.txt_, lub _pliku runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_, lub  _iisstart.htm_ |
| Zadania WebJob | _\<job_name > / run. \<rozszerzenia >_ w obszarze _aplikacji\_danych/zadania/ciągłego_ (dla ciągłe zadania Webjob) lub _aplikacji\_danych/zadania/wyzwalane_ (dla wyzwalane Zadania Webjob). Aby uzyskać więcej informacji, zobacz [dokumentacji zadań Webjob Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs) |
| Funkcje | Zobacz [ciągłego wdrażania usługi Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Aby dostosować wdrożenia, obejmują _.deployment_ pliku w folderze głównym repozytorium. Aby uzyskać więcej informacji, zobacz [Dostosowywanie wdrożenia](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) i [niestandardowego skryptu wdrażania](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> W przypadku tworzenia w programie Visual Studio let [programu Visual Studio utworzenie repozytorium do](/vsts/git/tutorial/creatingrepo?view=vsts&tabs=visual-studio). Projekt jest od razu gotowa do wdrożenia przy użyciu narzędzia Git.
>
>

