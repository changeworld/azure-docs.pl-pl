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
ms.date: 06/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: 88cc9ff4979c2e6a4a14a7d531054c1a842deaf8
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849807"
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

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git-with-kudu-builds"></a>Wdrażanie z lokalnego repozytorium Git z kompilacjami Kudu

Najprostszym sposobem włączenia lokalnego wdrożenia Git dla aplikacji na serwerze Kudu kompilacji jest użycia powłoki chmury.

### <a name="create-a-deployment-user"></a>Tworzenie użytkownika wdrożenia

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Włącz lokalnego Git z Kudu

Aby włączyć lokalne wdrożenie Git dla aplikacji na serwerze Kudu kompilacji, uruchom [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_local_git) w powłoce chmury.

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

### <a name="deploy-your-project"></a>Wdrażanie projektu

W _lokalnym oknie terminala_ dodaj zdalną platformę Azure do lokalnego repozytorium Git. Zastąp  _\<adres url >_ z adresem URL zdalnego Git pochodzący z [włączyć Git dla aplikacji](#enable-git-for-you-app).

```bash
git remote add azure <url>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Gdy zostanie wyświetlony monit o podanie hasła, upewnij się, że wprowadzasz hasło utworzone podczas [konfiguracji użytkownika wdrożenia](#configure-a-deployment-user), a nie hasło, którego używasz do logowania się w witrynie Azure Portal.

```bash
git push azure master
```

Automatyzacja specyficzne dla środowiska uruchomieniowego w danych wyjściowych, takich jak program MSBuild programu ASP.NET, może zostać wyświetlony `npm install` dla środowiska Node.js, a `pip install` dla języka Python. 

Przejdź do aplikacji w taki sposób, aby sprawdzić, czy zawartość jest wdrażana.

## <a name="deploy-from-local-git-with-vsts-builds"></a>Wdrażanie z lokalnego repozytorium Git z kompilacjami VSTS

> [!NOTE]
> Usługi aplikacji Utwórz niezbędne kompilację i wersji definicji w ramach konta usługi VSTS konta platformy Azure musi mieć rolę **właściciela** w Twojej subskrypcji platformy Azure.
>

Aby włączyć lokalne wdrożenie Git dla aplikacji na serwerze Kudu kompilacji, przejdź do aplikacji w [portalu Azure](https://portal.azure.com).

W lewym obszarze nawigacji strony aplikacji, kliknij przycisk **Centrum wdrażania** > **lokalnego Git** > **Kontynuuj**. 

![](media/app-service-deploy-local-git/portal-enable.png)

Kliknij przycisk **VSTS ciągłego dostarczania** > **kontynuować**.

![](media/app-service-deploy-local-git/vsts-build-server.png)

W **Konfiguruj** strony, konfigurowanie nowego konta usługi VSTS lub określenie istniejącego konta. Gdy skończysz, kliknij przycisk **Kontynuuj**.

> [!NOTE]
> Jeśli chcesz użyć istniejącego konta usługi VSTS nie ma na liście, należy [połączenia konta usługi VSTS do subskrypcji platformy Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

W **testu** wybierz, czy włączyć testów obciążenia, a następnie kliknij pozycję **Kontynuuj**.

W zależności od [warstwy cenowej](/pricing/details/app-service/plans/) planu usługi aplikacji może również zostać wyświetlony **wdrażanie na przemieszczania** strony. Wybierz, czy chcesz włączyć miejsc wdrożenia, a następnie kliknij przycisk **Kontynuuj**.

W **Podsumowanie** , sprawdź opcje i kliknij przycisk **Zakończ**.

Trwa kilka minut dla konta usługi VSTS będzie gotowa. Gdy będzie gotowy, skopiuj adres URL repozytorium Git w Centrum wdrażania.

![](media/app-service-deploy-local-git/vsts-repo-ready.png)

W _lokalnym oknie terminala_ dodaj zdalną platformę Azure do lokalnego repozytorium Git. Zastąp  _\<adres url >_ z adresem URL uzyskano w ostatnim kroku.

```bash
git remote add vsts <url>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Po wyświetleniu monitu przez Menedżera poświadczeń Git, zaloguj się przy użyciu programu Visual Studio — użytkownika. Aby uzyskać dodatkowe metody uwierzytelniania, zobacz [omówienie uwierzytelniania programu VSTS](/vsts/git/auth-overview?view=vsts).

```bash
git push vsts master
```

Po ukończeniu wdrażania można znaleźć postęp kompilacji w `https://<vsts_account>.visualstudio.com/<project_name>/_build` i postępu wdrożenia na `https://<vsts_account>.visualstudio.com/<project_name>/_release`.

Przejdź do aplikacji w taki sposób, aby sprawdzić, czy zawartość jest wdrażana.

## <a name="troubleshooting-kudu-deployment"></a>Rozwiązywanie problemów z wdrażaniem Kudu

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
* **Moduł macierzysty, która nie ma binarne dystrybucji dla systemu Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      LUB
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dokumentacja Kudu projektu](https://github.com/projectkudu/kudu/wiki)
* [Ciągłe wdrażanie w usłudze Azure App Service](app-service-continuous-deployment.md)
* [Przykład: Tworzenie aplikacji sieci Web i wdrażanie kodu z lokalnego repozytorium Git (Azure CLI)](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [Przykład: Tworzenie aplikacji sieci Web i wdrażanie kodu z lokalnego repozytorium Git (PowerShell)](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
