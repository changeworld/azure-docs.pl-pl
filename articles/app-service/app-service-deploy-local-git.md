---
title: Wdrażanie lokalnej usługi Git w usłudze Azure App Service
description: Dowiedz się, jak włączyć lokalne wdrożenie systemu Git w usłudze Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: 842cd6f67a04bec0ed06282bdeeea8b8a51c0667
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="local-git-deployment-to-azure-app-service"></a>Wdrażanie lokalnej usługi Git w usłudze Azure App Service

Ten przewodnik przedstawia sposób wdrażania kodu do [usłudze Azure App Service](app-service-web-overview.md) z repozytorium Git na komputerze lokalnym.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Wykonanie kroków tego podręcznika porad:

* [Zainstaluj oprogramowanie Git](http://www.git-scm.com/downloads).
* Obsługa lokalnego repozytorium Git z kodem, który chcesz wdrożyć.

Do korzystania z repozytorium przykładowej w odbiorze, uruchom następujące polecenie w oknie lokalne terminala:

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

## <a name="prepare-your-repository"></a>Przygotowanie repozytorium

Upewnij się, że katalogu głównym repozytorium ma poprawne pliki w projekcie.

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
| Funkcje | Zobacz [ciągłego wdrażania usługi Azure Functions](../azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Aby dostosować wdrożenia, można dołączyć _.deployment_ pliku w folderze głównym repozytorium. Aby uzyskać więcej informacji, zobacz [Dostosowywanie wdrożenia](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) i [niestandardowego skryptu wdrażania](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Upewnij się, `git commit` wszystkie zmiany, którą chcesz wdrożyć.
>
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user.md)]

## <a name="enable-git-for-your-app"></a>Włącz Git dla aplikacji

Aby umożliwić wdrożenie Git dla istniejącej aplikacji usługi App Service, uruchom [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_local_git) w powłoce chmury.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Aby zamiast tego Utwórz aplikację z obsługą Git, uruchom [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) w powłoce chmury z `--deployment-local-git` parametru.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

`az webapp create` Polecenia powinien zapewnić podobny do następujących danych wyjściowych:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="deploy-your-project"></a>Wdrażanie projektu

W _lokalnym oknie terminala_ dodaj zdalną platformę Azure do lokalnego repozytorium Git. Zastąp  _\<adres url >_ z adresem URL zdalnego Git pochodzący z [włączyć Git dla aplikacji](#enable-git-for-you-app).

```bash
git remote add azure <url>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Gdy zostanie wyświetlony monit o podanie hasła, upewnij się, że wprowadzasz hasło utworzone podczas [konfiguracji użytkownika wdrożenia](#configure-a-deployment-user), a nie hasło, którego używasz do logowania się w witrynie Azure Portal.

```bash
git push azure master
```

Automatyzacja specyficzne dla środowiska uruchomieniowego w danych wyjściowych, takich jak program MSBuild programu ASP.NET, może zostać wyświetlony `npm install` dla środowiska Node.js, a `pip install` dla języka Python. 

Po ukończeniu wdrażania aplikacji w portalu Azure powinna teraz istnieć rekord użytkownika `git push` w **opcje wdrażania** strony.

![](./media/app-service-deploy-local-git/deployment_history.png)

Przejdź do aplikacji w taki sposób, aby sprawdzić, czy zawartość jest wdrażana.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniżej przedstawiono typowe błędy lub problemy podczas przy użyciu narzędzia Git, aby opublikować aplikację usługi aplikacji na platformie Azure:

---
**Objaw**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Przyczyna**: ten błąd może się zdarzyć, jeśli aplikacja nie zostanie uruchomiona.

**Rozdzielczość**: uruchomić aplikację w portalu Azure. Wdrażanie Git jest niedostępna, jeśli aplikacja sieci Web została zatrzymana.

---
**Objaw**: `Couldn't resolve host 'hostname'`

**Przyczyna**: ten błąd może wystąpić, jeśli adres wprowadzonej podczas tworzenia zdalnego "azure" jest niepoprawne.

**Rozdzielczość**: Użyj `git remote -v` polecenie, aby wyświetlić listę wszystkich element zdalny, wraz z powiązanego adresu URL. Sprawdź, czy adres URL "platformy azure" zdalnego jest poprawna. W razie potrzeby usuń i ponownie utwórz ten zdalnego przy użyciu poprawny adres URL.

---
**Objaw**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Przyczyna**: ten błąd może się zdarzyć, jeśli nie określisz gałąź podczas `git push`, lub jeśli nie zostały jeszcze skonfigurowane `push.default` wartość w `.gitconfig`.

**Rozdzielczość**: Uruchom `git push` ponownie, określając gałęzi głównej. Na przykład:

```bash
git push azure master
```

---
**Objaw**: `src refspec [branchname] does not match any.`

**Przyczyna**: ten błąd może się zdarzyć, Jeśli spróbujesz wypychania do gałęzi innej niż główny "Azure" zdalnego.

**Rozdzielczość**: Uruchom `git push` ponownie, określając gałęzi głównej. Na przykład:

```bash
git push azure master
```

---
**Objaw**: `RPC failed; result=22, HTTP code = 5xx.`

**Przyczyna**: ten błąd może się zdarzyć, Jeśli spróbujesz push repozytorium git dużych za pośrednictwem protokołu HTTPS.

**Rozdzielczość**: Zmień konfigurację git na komputer lokalny, aby powiększyć postBuffer

```bash
git config --global http.postBuffer 524288000
```

---
**Objaw**: `Error - Changes committed to remote repository but your web app not updated.`

**Przyczyna**: ten błąd może wystąpić w przypadku wdrażania aplikacji Node.js z _package.json_ pliku, który określa dodatkowe wymagane moduły.

**Rozdzielczość**: dodatkowych komunikatów z 'npm błąd'! powinny być rejestrowane przed ten błąd i może zapewnić dodatkowy kontekst błędu. Znane są następujące przyczyny tego błędu i odpowiednie "npm błąd!' Komunikat:

* **Plik package.json źle sformułowane**: błąd npm! Nie można odczytać zależności.
* **Natywny moduł, który nie ma binarne dystrybucji dla systemu Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      LUB
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dokumentacja Kudu projektu](https://github.com/projectkudu/kudu/wiki)
* [Ciągłe wdrażanie w usłudze Azure App Service](app-service-continuous-deployment.md)
* [Przykład: Tworzenie aplikacji sieci Web i wdrażanie kodu z lokalnego repozytorium Git (Azure CLI)](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [Przykład: Tworzenie aplikacji sieci Web i wdrażanie kodu z lokalnego repozytorium Git (PowerShell)](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
