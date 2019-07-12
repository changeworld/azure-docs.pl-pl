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
ms.date: 06/18/2019
ms.author: dariagrigoriu;cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: fa961e43408fed014be2266c14c7972d39435b97
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836894"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Lokalne wdrożenie narzędzia Git w usłudze Azure App Service

W tym przewodniku pokazano, jak wdrożyć aplikację [usługi Azure App Service](overview.md) z repozytorium Git na komputerze lokalnym.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku z instrukcjami:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Zainstaluj oprogramowanie Git](https://www.git-scm.com/downloads).
  
- Masz lokalne repozytorium Git z kodem, który chcesz wdrożyć. Aby pobrać to repozytorium przykładów, uruchom następujące polecenie w oknie lokalnego terminala:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Wdrażanie przy użyciu serwer kompilacji Kudu

Najprostszym sposobem, aby umożliwić lokalne wdrożenie narzędzia Git dla aplikacji przy użyciu serwer kompilacji Kudu App Service jest użycie usługi Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Pobierz adres URL wdrożenia

Aby uzyskać adres URL, aby włączyć lokalne wdrożenie narzędzia Git dla istniejącej aplikacji, uruchom [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) w usłudze Cloud Shell. Zastąp \<Nazwa aplikacji > i \<nazwa_grupy > nazwą swojej aplikacji i jej grupy zasobów platformy Azure.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Lub, w celu utworzenia nowej aplikacji z obsługą usługi Git Uruchom [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) w usłudze Cloud Shell za pomocą `--deployment-local-git` parametru. Zastąp \<Nazwa aplikacji >, \<nazwa_grupy >, i \<Nazwa planu > przy użyciu nazwy dla nowej aplikacji usługi Git, jej grupy zasobów platformy Azure i jej planu usługi Azure App Service.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Polecenie albo zwraca adres URL, takich jak: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Użyj tego adresu URL, aby wdrożyć aplikację w następnym kroku.

Zamiast korzystać z tego adresu URL na poziomie konta, można również włączyć lokalnego narzędzia Git przy użyciu poświadczeń na poziomie aplikacji. Usługa Azure App Service automatycznie generuje te poświadczenia dla każdej aplikacji. 

Uzyskaj poświadczenia aplikacji, uruchamiając następujące polecenie w usłudze Cloud Shell. Zastąp \<Nazwa aplikacji > i \<nazwa_grupy > z nazwą Twojej aplikacji i nazwę grupy zasobów platformy Azure.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Użyj adresu URL, która zwraca do wdrożenia aplikacji w następnym kroku.

### <a name="deploy-the-web-app"></a>Wdrażanie aplikacji sieci web

1. Otwórz okno terminalu lokalnym do lokalnego repozytorium Git, a następnie dodaj zdalną platformę Azure. W poniższym poleceniu zastąp \<adresu url > adres URL wdrożenia specyficzne dla użytkownika lub adresu URL specyficznego dla aplikacji, uzyskanego w poprzednim kroku.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Wypchnij na platformę Azure zdalne z `git push azure master`. 
   
1. W **Git Credential Manager** okna, wprowadź swoje [hasło użytkownika narzędzia deployment](#configure-a-deployment-user), nie Azure logowania hasła.
   
1. Przejrzyj dane wyjściowe. Może zostać wyświetlony automatyzacji specyficzne dla środowiska uruchomieniowego, takiego Jakmsbuild programu ASP.NET, `npm install` dla środowiska Node.js, i `pip install` dla języka Python. 
   
1. Przejdź do aplikacji w witrynie Azure portal, aby sprawdzić, czy zawartość jest wdrażana.

## <a name="deploy-with-azure-pipelines-builds"></a>Wdrażanie przy użyciu Azure potoki kompilacji

Jeśli Twoje konto ma odpowiednie uprawnienia, można Konfigurowanie potoków Azure (wersja zapoznawcza), aby włączyć lokalne wdrożenie narzędzia Git dla aplikacji. 

- Twoje konto platformy Azure musi mieć uprawnienia do zapisu do usługi Azure Active Directory i tworzenia usługi. 
  
- Twoje konto platformy Azure musi mieć **właściciela** roli w subskrypcji platformy Azure.

- Musisz być administratorem projektu DevOps platformy Azure, którego chcesz użyć.

Aby włączyć lokalne wdrożenie narzędzia Git dla aplikacji korzystających z potoków usługi Azure (wersja zapoznawcza):

1. Przejdź do strony aplikacji usługi Azure App Service w [witryny Azure portal](https://portal.azure.com)i wybierz **Centrum wdrażania** w menu po lewej stronie.
   
1. Na **Centrum wdrażania** wybierz opcję **lokalnego narzędzia Git**, a następnie wybierz pozycję **Kontynuuj**. 
   
   ![Wybierz lokalnego narzędzia Git, a następnie wybierz pozycję Kontynuuj](media/app-service-deploy-local-git/portal-enable.png)
   
1. Na **dostawcę konstrukcji** wybierz opcję **potoki usługi Azure (wersja zapoznawcza)** , a następnie wybierz pozycję **Kontynuuj**. 
   
   ![Wybierz potoki usługi Azure (wersja zapoznawcza), a następnie wybierz pozycję Kontynuuj.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Na **Konfiguruj** strony konfigurowania nowej organizacji DevOps platformy Azure, lub określ istniejącą organizację, a następnie wybierz **Kontynuuj**.
   
   > [!NOTE]
   > Istniejącej organizacji DevOps platformy Azure nie ma na liście, konieczne może się połączyć subskrypcję platformy Azure. Aby uzyskać więcej informacji, zobacz [zdefiniować potok wydania CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. W zależności od planu usługi App Service [warstwy cenowej](https://azure.microsoft.com/pricing/details/app-service/plans/), może zostać wyświetlony **Deploy do wdrażania przejściowego** strony. Wybierz opcję [Włącz miejsca wdrożenia](deploy-staging-slots.md), a następnie wybierz pozycję **Kontynuuj**.
   
1. Na **Podsumowanie** strony, przejrzyj ustawienia, a następnie wybierz pozycję **Zakończ**.
   
1. Gdy potoku usługi Azure jest gotowy, skopiuj adres URL repozytorium Git z **Centrum wdrażania** strony do użycia w następnym kroku. 
   
   ![Skopiuj adres URL repozytorium Git](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. W oknie lokalnego terminala Dodaj zdalną platformę Azure do lokalnego repozytorium Git. W poleceniu zastąp \<adresu url > adres URL repozytorium Git, która jest wyświetlana w poprzednim kroku.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Wypchnij na platformę Azure zdalne z `git push azure master`. 
   
1. Na **Git Credential Manager** strony, zaloguj się przy użyciu nazwy użytkownika Visual Studio. Aby uzyskać inne metody uwierzytelniania, zobacz [usługom DevOps platformy Azure, omówienie uwierzytelniania](/vsts/git/auth-overview?view=vsts).
   
1. Po zakończeniu wdrożenia należy wyświetlić postęp kompilacji w `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`oraz postęp wdrażania w `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`.
   
1. Przejdź do aplikacji w witrynie Azure portal, aby sprawdzić, czy zawartość jest wdrażana.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Rozwiązywanie problemów z wdrożenia

Kiedy używasz Git, aby opublikować aplikację usługi App Service na platformie Azure, mogą pojawić następujące typowe komunikaty o błędach:

|Message|Przyczyna|Rozwiązanie
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Aplikacja nie będzie działać.|Uruchom aplikację w witrynie Azure portal. Wdrożenie narzędzia Git nie jest dostępna, gdy aplikacja sieci web została zatrzymana.|
|`Couldn't resolve host 'hostname'`|Informacje o adresie "Azure" zdalny jest nieprawidłowy.|Użyj `git remote -v` polecenie, aby wyświetlić listę wszystkich dostępów zdalnych, wraz z powiązanego adresu URL. Sprawdź, czy adres URL "Azure" zdalny jest poprawna. W razie potrzeby usuń i ponownie utwórz ten zdalnego przy użyciu prawidłowego adresu URL.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Nie określono gałęzi podczas `git push`, lub nie został ustawiony `push.default` wartość w `.gitconfig`.|Uruchom `git push` ponownie, określając głównej gałęzi: `git push azure master`.|
|`src refspec [branchname] does not match any.`|Próbowano Wypychanie do gałęzi innej niż główny "Azure" zdalnego.|Uruchom `git push` ponownie, określając głównej gałęzi: `git push azure master`.|
|`RPC failed; result=22, HTTP code = 5xx.`|Ten błąd może wystąpić, Jeśli spróbujesz wypchnąć repozytorium git w dużej za pośrednictwem protokołu HTTPS.|Zmiana konfiguracji narzędzia git na komputerze lokalnym, aby `postBuffer` większe. Na przykład: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Wdrożyć aplikację Node.js przy użyciu _package.json_ pliku, który określa dodatkowe wymagane moduły.|Przegląd `npm ERR!` komunikaty o błędach przed tego błędu, aby uzyskać dodatkowy kontekst, w przypadku niepowodzenia. Poniżej przedstawiono znane przyczyny tego błędu i odpowiedni `npm ERR!` wiadomości:<br /><br />**Plik package.json źle sformułowane**: `npm ERR! Couldn't read dependencies.`<br /><br />**Moduł macierzysty nie ma binarne dystrybucji dla Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />lub <br />"npm błąd! [modulename@version] przed instalacją: \make || gmake\`|

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Projekt Kudu dokumentacji](https://github.com/projectkudu/kudu/wiki)
- [Ciągłe wdrażanie w usłudze Azure App Service](deploy-continuous-deployment.md)
- [Przykład: Tworzenie aplikacji internetowej i wdrażanie kodu z lokalnego repozytorium Git (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Przykład: Tworzenie aplikacji internetowej i wdrażanie kodu z lokalnego repozytorium Git (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
