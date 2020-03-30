---
title: Wdrażanie z lokalnego repozytorium Git
description: Dowiedz się, jak włączyć lokalne wdrażanie git w usłudze Azure App Service. Jednym z najprostszych sposobów wdrażania kodu z komputera lokalnego.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: efe4c07a6231e0b2c95b049db056a4e5d055db98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152996"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Wdrożenie lokalnego git w usłudze Azure App Service

W tym przewodniku przedstawiono sposób wdrażania aplikacji w [usłudze Azure App Service](overview.md) z repozytorium Git na komputerze lokalnym.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Zainstaluj Git](https://www.git-scm.com/downloads).
  
- Posiadaj lokalne repozytorium Git z kodem, który chcesz wdrożyć. Aby pobrać przykładowe repozytorium, uruchom następujące polecenie w oknie terminala lokalnego:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Wdrażanie z serwerem kompilacji Kudu

Najprostszym sposobem włączenia lokalnego wdrożenia git dla aplikacji za pomocą serwera kompilacji usługi Kudu App Service jest użycie usługi Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Pobierz adres URL wdrożenia

Aby uzyskać adres URL, aby włączyć lokalne wdrożenie [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) Git dla istniejącej aplikacji, uruchom w Aplikacji Cloud Shell. Zamień \<> nazwy aplikacji \<i> nazwy grupy nazwami aplikacji i jej grupy zasobów platformy Azure.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> Jeśli używasz planu aplikacji systemu Linux, musisz dodać ten parametr: --runtime python|3.7


Lub, aby utworzyć nową aplikację z obsługą [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) Git, uruchom `--deployment-local-git` w aplikacji Cloud Shell z parametrem. Zastąp \<>, \<> nazwy grupy i \<> nazwy planu nazwami nazwy nowej aplikacji Git, jej grupy zasobów platformy Azure i planu usługi Azure App Service.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Albo polecenie zwraca adres `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`URL, taki jak: . Użyj tego adresu URL, aby wdrożyć aplikację w następnym kroku.

Zamiast używać tego adresu URL na poziomie konta, możesz również włączyć lokalny Git przy użyciu poświadczeń na poziomie aplikacji. Usługa Azure App Service automatycznie generuje te poświadczenia dla każdej aplikacji. 

Pobierz poświadczenia aplikacji, uruchamiając następujące polecenie w aplikacji Cloud Shell. Zamień \<nazwę aplikacji> \<i> nazwa grupy nazwą aplikacji i nazwą grupy zasobów platformy Azure.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Użyj adresu URL, który powraca, aby wdrożyć aplikację w następnym kroku.

### <a name="deploy-the-web-app"></a>Wdrażanie aplikacji sieci Web

1. Otwórz lokalne okno terminala w lokalnym repozytorium Git i dodaj pilota platformy Azure. W poniższym poleceniu zastąp \<adres URL> adresem URL specyficznym dla użytkownika wdrożenia lub adresem URL specyficznym dla aplikacji, który zostałby odebrany z poprzedniego kroku.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Wypychanie na `git push azure master`platformę Azure remote za pomocą pliku . 
   
1. W oknie **Menedżera poświadczeń Git** wprowadź [hasło użytkownika wdrożenia,](#configure-a-deployment-user)a nie hasło logowania platformy Azure.
   
1. Przejrzyj dane wyjściowe. Może zostać wyświetlona automatyzacja specyficzne dla środowiska uruchomieniowego, `npm install` takich jak MSBuild dla ASP.NET, dla Node.js i `pip install` Python. 
   
1. Przejdź do aplikacji w witrynie Azure Portal, aby sprawdzić, czy zawartość jest wdrażana.

## <a name="deploy-with-azure-pipelines-builds"></a>Wdrażanie za pomocą kompilacji potoków platformy Azure

Jeśli twoje konto ma niezbędne uprawnienia, możesz skonfigurować usługi Azure Pipelines (Preview), aby włączyć lokalne wdrażanie git dla aplikacji. 

- Twoje konto platformy Azure musi mieć uprawnienia do zapisywania w usłudze Azure Active Directory i tworzenia usługi. 
  
- Twoje konto platformy Azure musi mieć rolę **właściciela** w subskrypcji platformy Azure.

- Musisz być administratorem w projekcie Azure DevOps, którego chcesz użyć.

Aby włączyć lokalne wdrażanie git dla aplikacji za pomocą usługi Azure Pipelines (Wersja zapoznawcza):

1. W [witrynie Azure portal](https://portal.azure.com)wyszukaj i wybierz **pozycję Usługi aplikacji**. 

1. Wybierz aplikację usługi Azure App Service i wybierz **Centrum wdrażania** w menu po lewej stronie.
   
1. Na stronie **Centrum wdrażania** wybierz pozycję **Lokalny git**, a następnie wybierz pozycję **Kontynuuj**. 
   
   ![Wybierz pozycję Lokalny Git, a następnie wybierz pozycję Kontynuuj](media/app-service-deploy-local-git/portal-enable.png)
   
1. Na stronie **Dostawca kompilacji** wybierz pozycję **Azure Pipelines (Preview),** a następnie wybierz pozycję **Kontynuuj**. 
   
   ![Wybierz pozycję Potoki platformy Azure (wersja zapoznawcza), a następnie wybierz pozycję Kontynuuj.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Na stronie **Konfigurowanie** skonfiguruj nową organizację Programu Azure DevOps lub określ istniejącą organizację, a następnie wybierz pozycję **Kontynuuj**.
   
   > [!NOTE]
   > Jeśli istniejącej organizacji Azure DevOps nie ma na liście, może być konieczne powiązanie jej z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Definiowanie potoku wydania dysku CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. W zależności od [warstwy cenowej](https://azure.microsoft.com/pricing/details/app-service/plans/)planu usługi App Service może zostać wyświetlona strona **wdrażania w przemieszczania.** Wybierz, czy [chcesz włączyć miejsca wdrożenia](deploy-staging-slots.md), a następnie wybierz pozycję **Kontynuuj**.
   
1. Na stronie **Podsumowanie** przejrzyj ustawienia, a następnie wybierz pozycję **Zakończ**.
   
1. Gdy usługa Azure Pipeline jest gotowa, skopiuj adres URL repozytorium Git ze strony **Centrum wdrażania,** aby użyć go w następnym kroku. 
   
   ![Kopiowanie adresu URL repozytorium Git](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. W oknie terminala lokalnego dodaj pilota platformy Azure do lokalnego repozytorium Git. W komendzie \<zastąp adres URL> adresem URL repozytorium Git, który otrzymałeś z poprzedniego kroku.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Wypychanie na `git push azure master`platformę Azure remote za pomocą pliku . 
   
1. Na stronie **Git Credential Manager** zaloguj się przy użyciu nazwy użytkownika visualstudio.com. Aby zapoznać się z innymi metodami uwierzytelniania, zobacz [Omówienie uwierzytelniania usług DevOps azure](/vsts/git/auth-overview?view=vsts).
   
1. Po zakończeniu wdrażania wyświetl postęp `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`kompilacji w punkcie `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`, a postęp wdrożenia w punkcie .
   
1. Przejdź do aplikacji w witrynie Azure Portal, aby sprawdzić, czy zawartość jest wdrażana.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Rozwiązywanie problemów z wdrażaniem

Podczas publikowania w aplikacji usługi App Service na platformie Azure mogą zostać wyświetlenia następujących typowych komunikatów o błędach:

|Komunikat|Przyczyna|Rozwiązanie
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Aplikacja nie jest uruchomiona.|Uruchom aplikację w witrynie Azure portal. Wdrożenie git nie jest dostępne po zatrzymaniu aplikacji sieci web.|
|`Couldn't resolve host 'hostname'`|Informacje o adresie pilota "azure" są niepoprawne.|Użyj `git remote -v` polecenia, aby wyświetlić listę wszystkich pilotów wraz ze skojarzonym adresem URL. Sprawdź, czy adres URL pilota "azure" jest poprawny. W razie potrzeby usuń i ponownie stwórz ten pilot przy użyciu poprawnego adresu URL.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Nie określono gałęzi podczas `git push`lub nie ustawiono `push.default` wartości w `.gitconfig`.|Uruchom `git push` ponownie, określając gałąź główną: `git push azure master`.|
|`src refspec [branchname] does not match any.`|Próbowano wypchnąć do gałęzi innej niż master na pilocie "azure".|Uruchom `git push` ponownie, określając gałąź główną: `git push azure master`.|
|`RPC failed; result=22, HTTP code = 5xx.`|Ten błąd może się zdarzyć, jeśli spróbujesz wypchnąć duże repozytorium git za pośrednictwem protokołu HTTPS.|Zmień konfigurację git na komputerze `postBuffer` lokalnym, aby pomniejsić. Na przykład: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Wdrożono aplikację Node.js z plikiem _package.json,_ który określa dodatkowe wymagane moduły.|Przejrzyj komunikaty `npm ERR!` o błędach przed tym błędem, aby uzyskać więcej kontekstu błędu. Poniżej znajdują się znane przyczyny tego `npm ERR!` błędu i odpowiednie komunikaty:<br /><br />**Zniekształcony plik package.json**:`npm ERR! Couldn't read dependencies.`<br /><br />**Moduł macierzysty nie ma dystrybucji binarnej dla systemu Windows:**<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />lub <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Dokumentacja projektu Kudu](https://github.com/projectkudu/kudu/wiki)
- [Ciągłe wdrażanie usługi Azure App Service](deploy-continuous-deployment.md)
- [Przykład: Tworzenie aplikacji sieci web i wdrażanie kodu z lokalnego repozytorium Git (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Przykład: Tworzenie aplikacji sieci Web i wdrażanie kodu z lokalnego repozytorium Git (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
