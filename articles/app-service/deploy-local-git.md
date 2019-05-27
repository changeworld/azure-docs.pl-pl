---
title: Wdrażanie z lokalnego repozytorium Git — usłudze Azure App Service
description: Dowiedz się, jak włączyć lokalne wdrożenie narzędzia Git w usłudze Azure App Service.
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
ms.custom: seodec18
ms.openlocfilehash: b879036dcd79901cb634fa197932e833cb22d12a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65956075"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Wdrażanie lokalnej usługi Git w usłudze Azure App Service

Ten poradnik pokazuje jak wdrożyć swój kod, aby [usługi Azure App Service](overview.md) z repozytorium Git na komputerze lokalnym.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku z instrukcjami:

* [Zainstaluj oprogramowanie Git](https://www.git-scm.com/downloads).
* Obsługa lokalnego repozytorium Git z kodem, który chcesz wdrożyć.

Aby użyć przykładowego repozytorium z niego skorzystać, uruchom następujące polecenie w oknie lokalnego terminala:

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-builds"></a>Wdrażanie przy użyciu kompilacji Kudu

Najprostszym sposobem włączenia lokalnego wdrożenia usługi Git dla Twojej aplikacji z serwerem kompilacji Kudu jest użycie usługi Cloud Shell.

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Włączanie lokalnej usługi Git za pomocą serwera Kudu

Aby włączyć lokalne wdrożenie usługi Git dla Twojej aplikacji przy użyciu serwera kompilacji Kudu, uruchom polecenie [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) w usłudze Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Aby zamiast tego utworzyć aplikację z obsługą usługi Git, uruchom w usłudze Cloud Shell polecenie [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) z parametrem `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

Polecenie `az webapp create` powinno dać dane wyjściowe podobne do poniższych:

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

W _lokalnym oknie terminala_ dodaj zdalną platformę Azure do lokalnego repozytorium Git. Zastąp element _\<url>_ adresem URL zdalnego repozytorium Git uzyskanego po [włączeniu usługi Git dla Twojej aplikacji](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Gdy zostanie wyświetlony monit o podanie hasła, upewnij się, że wprowadzasz hasło utworzone podczas [konfiguracji użytkownika wdrożenia](#configure-a-deployment-user), a nie hasło, którego używasz do logowania się w witrynie Azure Portal.

```bash
git push azure master
```

W danych wyjściowych możesz zobaczyć automatyzację specyficzną dla środowiska uruchomieniowego, takiego jak MSBuild dla programu ASP.NET, `npm install` dla platformy Node.js i `pip install` dla języka Python. 

Przejdź do aplikacji, aby sprawdzić, czy zawartość jest wdrażana.

## <a name="deploy-with-azure-devops-builds"></a>Wdrażanie przy użyciu kompilacji DevOps platformy Azure

> [!NOTE]
> Usługi App Service utworzyć niezbędne potoki usługi Azure w Twojej organizacji usługom DevOps platformy Azure, Twoje konto platformy Azure musi mieć rolę **właściciela** w subskrypcji platformy Azure.
>

Aby włączyć lokalne wdrożenie narzędzia Git dla aplikacji przy użyciu serwer kompilacji Kudu, przejdź do aplikacji w [witryny Azure portal](https://portal.azure.com).

W lewym obszarze nawigacji strony aplikacji kliknij **Centrum wdrażania** > **lokalnego narzędzia Git** > **Kontynuuj**.

![](media/app-service-deploy-local-git/portal-enable.png)

Kliknij przycisk **potoki platformy Azure (wersja zapoznawcza)** > **nadal**.

![](media/app-service-deploy-local-git/pipeline-builds.png)

W **Konfiguruj** strony, konfigurowania nowej organizacji DevOps platformy Azure lub określ istniejącą organizację. Po zakończeniu kliknij przycisk **Kontynuuj**.

> [!NOTE]
> Jeśli chcesz użyć istniejącej organizacji DevOps platformy Azure, która nie ma na liście, musisz [łączenie organizacji usługom DevOps platformy Azure z subskrypcją platformy Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

W zależności od [warstwy cenowej](https://azure.microsoft.com/pricing/details/app-service/plans/) planu usługi App Service może również zostać wyświetlony **Deploy do wdrażania przejściowego** strony. Wybierz, czy włączyć miejsc wdrożenia, a następnie kliknij przycisk **Kontynuuj**.

W **Podsumowanie** strony, sprawdź opcje i kliknij przycisk **Zakończ**.

Trwa kilka minut, aż gotowość organizacji usługom DevOps platformy Azure. Gdy wszystko będzie gotowe, skopiuj adres URL repozytorium Git w Centrum wdrażania.

![](media/app-service-deploy-local-git/vsts-repo-ready.png)

W _lokalnym oknie terminala_ dodaj zdalną platformę Azure do lokalnego repozytorium Git. Zastąp  _\<adresu url >_ za pomocą adresu URL, masz w ostatnim kroku.

```bash
git remote add vsts <url>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Po wyświetleniu monitu przez program Git Credential Manager, zaloguj się za pomocą użytkownika usługi Visual Studio. Aby uzyskać dodatkowe metody uwierzytelniania, zobacz [usługom DevOps platformy Azure, omówienie uwierzytelniania](/vsts/git/auth-overview?view=vsts).

```bash
git push vsts master
```

Po zakończeniu wdrożenia można znaleźć postęp kompilacji w `https://<vsts_account>.visualstudio.com/<project_name>/_build` i postęp wdrażania w `https://<vsts_account>.visualstudio.com/<project_name>/_release`.

Przejdź do aplikacji, aby sprawdzić, czy zawartość jest wdrażana.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshooting-kudu-deployment"></a>Rozwiązywanie problemów z wdrażaniem programu Kudu

Poniżej przedstawiono typowe błędy lub problemy, gdy za pomocą narzędzia Git, aby opublikować aplikację usługi App Service na platformie Azure:

---
**Objaw**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Przyczyna**: Ten błąd może wystąpić, jeśli aplikacja nie będzie działać.

**Rozpoznawanie**: Uruchom aplikację w witrynie Azure portal. Wdrażanie usługi Git jest niedostępna, jeśli aplikacja sieci Web została zatrzymana.

---
**Objaw**: `Couldn't resolve host 'hostname'`

**Przyczyna**: Ten błąd może wystąpić, jeśli adres wartości wprowadzonej podczas tworzenia zdalnego "azure" była nieprawidłowa.

**Rozpoznawanie**: Użyj `git remote -v` polecenie, aby wyświetlić listę wszystkich dostępów zdalnych, wraz z powiązanego adresu URL. Sprawdź, czy adres URL "Azure" zdalny jest poprawna. W razie potrzeby usuń i ponownie utwórz ten zdalnego przy użyciu prawidłowego adresu URL.

---
**Objaw**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Przyczyna**: Ten błąd może wystąpić, jeśli nie określisz gałęzi podczas `git push`, lub jeśli nie został ustawiony `push.default` wartość w `.gitconfig`.

**Rozpoznawanie**: Uruchom `git push` ponownie, określając gałęzi głównej. Na przykład:

```bash
git push azure master
```

---
**Objaw**: `src refspec [branchname] does not match any.`

**Przyczyna**: Ten błąd może wystąpić, Jeśli spróbujesz wypchnąć do rozgałęzienia innego niż główny "Azure" zdalnego.

**Rozpoznawanie**: Uruchom `git push` ponownie, określając gałęzi głównej. Na przykład:

```bash
git push azure master
```

---
**Objaw**: `RPC failed; result=22, HTTP code = 5xx.`

**Przyczyna**: Ten błąd może wystąpić, Jeśli spróbujesz wypchnąć repozytorium git w dużej za pośrednictwem protokołu HTTPS.

**Rozpoznawanie**: Zmienianie konfiguracji usługi git na komputerze lokalnym, aby powiększyć postBuffer

```bash
git config --global http.postBuffer 524288000
```

---
**Objaw**: `Error - Changes committed to remote repository but your web app not updated.`

**Przyczyna**: Ten błąd może wystąpić, jeśli wdrożysz aplikację Node.js przy użyciu _package.json_ pliku, który określa dodatkowe wymagane moduły.

**Rozpoznawanie**: Dodatkowe wiadomości przy użyciu funkcji "npm błąd!" powinny być rejestrowane, zanim ten błąd i może zapewnić dodatkowy kontekst błędu. Znane są następujące przyczyny tego błędu, a odpowiedni "npm błąd!" Komunikat:

* **Plik package.json źle sformułowane**: npm błąd! Nie można odczytać zależności.
* **Moduł macierzysty, który nie ma binarne dystrybucji dla Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      OR
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Projekt Kudu dokumentacji](https://github.com/projectkudu/kudu/wiki)
* [Ciągłe wdrażanie w usłudze Azure App Service](deploy-continuous-deployment.md)
* [Przykład: Tworzenie aplikacji internetowej i wdrażanie kodu z lokalnego repozytorium Git (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [Przykład: Tworzenie aplikacji internetowej i wdrażanie kodu z lokalnego repozytorium Git (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
