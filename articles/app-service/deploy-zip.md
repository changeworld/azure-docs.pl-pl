---
title: Wdróż kod z plikiem ZIP lub WAR — Azure App Service | Microsoft Docs
description: Dowiedz się, jak wdrożyć aplikację w celu Azure App Service z plikiem ZIP (lub plikiem WAR dla deweloperów języka Java).
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: cephalin
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: b69488c971d6ab10e0a95e3825be097f8713016d
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070446"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Wdróż aplikację w celu Azure App Service z plikiem ZIP lub WAR

W tym artykule pokazano, jak za pomocą pliku ZIP lub pliku WAR wdrożyć aplikację internetową w celu [Azure App Service](overview.md). 

To wdrożenie pliku ZIP używa tej samej usługi kudu, która ma uprawnienia do ciągłego wdrażania opartych na integracji. Kudu obsługuje następujące funkcje wdrożenia pliku ZIP: 

- Usuwanie plików pozostawionych z poprzedniego wdrożenia.
- Opcja włączania domyślnego procesu kompilacji, w tym przywracania pakietu.
- [Dostosowanie wdrożenia](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), w tym uruchamianie skryptów wdrażania.  
- Dzienniki wdrożenia. 
- Limit rozmiaru pliku wynoszący 2048 MB.

Więcej informacji zawiera [dokumentacja aparatu Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Wdrożenie pliku WAR wdraża plik [War](https://wikipedia.org/wiki/WAR_(file_format)) w celu App Service uruchamiania aplikacji sieci Web Java. Zobacz [wdrażanie pliku War](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule:

* [Utwórz aplikację usługi App Service](/azure/app-service/) lub użyj aplikacji utworzonej w innym samouczku.

## <a name="create-a-project-zip-file"></a>Tworzenie pliku ZIP projektu

>[!NOTE]
> Jeśli pobrano pliki w pliku ZIP, najpierw Wyodrębnij pliki. Na przykład jeśli pobrano plik ZIP z usługi GitHub, nie można wdrożyć tego pliku jako-is. W witrynie GitHub są dodawane dodatkowe katalogi zagnieżdżone, które nie działają z App Service. 
>

W lokalnym oknie terminalu przejdź do katalogu głównego projektu aplikacji. 

Ten katalog powinien zawierać plik wpisu do aplikacji sieci Web, taki jak _index. html_, _index. php_i _App. js_. Może również zawierać pliki zarządzania pakietami, takie jak _Project. JSON_, _Composer. JSON_, _Package. JSON_, _Bower. JSON_i _Requirements. txt_.

Utwórz archiwum ZIP z wszystkimi elementami w projekcie. Następujące polecenie używa domyślnego narzędzia w terminalu:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="deploy-zip-file-with-azure-cli"></a>Wdróż plik ZIP przy użyciu interfejsu wiersza polecenia platformy Azure

Upewnij się, że wersja interfejsu wiersza polecenia platformy Azure to 2.0.21 lub nowsza. Aby sprawdzić, która wersja posiadasz, `az --version` Uruchom polecenie w oknie terminalu.

Wdróż przekazany plik ZIP w aplikacji sieci Web za pomocą polecenia [AZ webapp Deployment Source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) .  

W poniższym przykładzie został wdrożony przekazany plik ZIP. W `--src`przypadku korzystania z lokalnej instalacji interfejsu wiersza polecenia platformy Azure należy określić ścieżkę do lokalnego pliku zip.

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

To polecenie wdraża pliki i katalogi z pliku ZIP do domyślnego folderu aplikacji App Service (`\home\site\wwwroot`) i ponownie uruchamia aplikację.

Domyślnie Aparat wdrażania zakłada, że plik ZIP jest gotowy do uruchomienia jako-is i nie uruchamia żadnej automatyzacji kompilacji. Aby włączyć tę samą automatyzację kompilacji jak w przypadku [wdrożenia usługi git](deploy-local-git.md), `SCM_DO_BUILD_DURING_DEPLOYMENT` należy ustawić ustawienie aplikacji, uruchamiając następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```



Więcej informacji zawiera [dokumentacja aparatu Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Wdróż plik WAR

Aby wdrożyć plik WAR do App Service, Wyślij żądanie POST do `https://<app_name>.scm.azurewebsites.net/api/wardeploy`. Żądanie POST musi zawierać plik WAR w treści wiadomości. Poświadczenia wdrażania dla aplikacji są podawane w żądaniu za pomocą podstawowego uwierzytelniania HTTP.

W przypadku uwierzytelniania podstawowego HTTP wymagane są poświadczenia wdrażania App Service. Aby dowiedzieć się, jak ustawić poświadczenia wdrożenia, zobacz [Ustawianie i resetowanie poświadczeń na poziomie użytkownika](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Z zwinięciem

W poniższym przykładzie za pomocą narzędzia zwinięcie można wdrożyć plik War. Zastąp symbole `<username>`zastępcze `<war-file-path>`, i `<app-name>`. Po wyświetleniu monitu przez zwinięcie wpisz hasło.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Z programem PowerShell

Poniższy przykład używa [publikowania-AzWebapp](/powershell/module/az.websites/publish-azwebapp) Przekaż plik War. Zastąp symbole `<group-name>`zastępcze `<app-name>`, i `<war-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Następne kroki

W przypadku bardziej zaawansowanych scenariuszy wdrażania spróbuj [wdrożyć platformę Azure za pomocą narzędzia Git](deploy-local-git.md). Wdrożenie oparte na usłudze Git na platformie Azure umożliwia kontrolę wersji, przywracanie pakietów, MSBuild i nie tylko.

## <a name="more-resources"></a>Więcej zasobów

* [Kudu: Wdrażanie z pliku zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Poświadczenia wdrażania Azure App Service](deploy-ftp.md)
