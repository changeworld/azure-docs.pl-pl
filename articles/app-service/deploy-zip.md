---
title: Wdrażanie kodu za pomocą pliku ZIP lub WAR — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć aplikację w usłudze Azure App Service z pliku ZIP (lub plik WAR dla deweloperów języka Java).
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: cephalin;sisirap
ms.custom: seodec18
ms.openlocfilehash: 1f94544a2e7a26ab00432a3564bb752c71254844
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484884"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Wdrażanie aplikacji w usłudze Azure App Service przy użyciu pliku ZIP lub WAR

W tym artykule dowiesz się, jak wdrożyć aplikację sieci web za pomocą pliku ZIP lub WAR pliku [usługi Azure App Service](overview.md). 

To wdrożenie plików ZIP korzysta z tej samej usługi Kudu tego uprawnienia ciągłej integracji wdrożenia oparte na. Aparat kudu obsługuje następujące funkcje do wdrożenia pliku ZIP: 

- Usuwanie plików pozostała z poprzedniego wdrożenia.
- Opcja Włącz domyślnego procesu kompilacji, który obejmuje Przywracanie pakietu.
- [Dostosowanie wdrożenia](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), takie jak uruchamianie skryptów wdrażania.  
- Dzienniki wdrożenia. 

Więcej informacji zawiera [dokumentacja aparatu Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Wdrożenia pliku WAR wdraża usługi [WAR](https://wikipedia.org/wiki/WAR_(file_format)) pliku w usłudze App Service, aby uruchomić aplikację sieci web Java. Zobacz [plik WAR wdrażanie](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Wykonanie czynności opisanych w tym artykule:

* [Utwórz aplikację usługi App Service](/azure/app-service/) lub użyj aplikacji utworzonej w innym samouczku.

## <a name="create-a-project-zip-file"></a>Tworzenie pliku ZIP projektu

>[!NOTE]
> Jeśli pobrano pliki w postaci pliku ZIP, Wyodrębnij pliki. Na przykład, jeśli plik ZIP został pobrany z witryny GitHub, nie można wdrożyć tego pliku jako-to. GitHub dodaje dodatkowe zagnieżdżonych katalogi, które nie działają z usługą App Service. 
>

W oknie lokalnego terminala przejdź do katalogu głównego projektu aplikacji. 

Ten katalog powinien zawierać pliku wejściowego do aplikacji sieci web, takie jak _index.html_, _index.php_, i _app.js_. Może również zawierać pakiet zarządzania plików, takich jak _project.json_, _composer.json_, _package.json_, _bower.json_i _requirements.txt_.

Utwórz archiwum ZIP z wszystkimi elementami w projekcie. Następujące polecenie używa domyślnego narzędzia w terminalu:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="deploy-zip-file-with-azure-cli"></a>Wdróż plik ZIP z wiersza polecenia platformy Azure

Upewnij się, że Twoja wersja interfejsu wiersza polecenia platformy Azure została 2.0.21 lub nowszej. Aby wyświetlić wersję Uruchom `az --version` polecenia w oknie terminalu.

Wdróż przekazany plik ZIP do aplikacji sieci web za pomocą [az webapp wdrożenia źródła konfiguracji zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) polecenia.  

Poniższy przykład służy do wdrażania pliku ZIP, który został przekazany. Korzystając z lokalnej instalacji wiersza polecenia platformy Azure, określ ścieżkę do lokalnego pliku ZIP dla `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

To polecenie wdraża pliki i katalogi z pliku ZIP do domyślnego folderu aplikacji App Service (`\home\site\wwwroot`) i ponownie uruchamia aplikację. Jeśli został skonfigurowany jakikolwiek dodatkowy niestandardowy proces kompilacji, również zostanie uruchomiony. Więcej informacji zawiera [dokumentacja aparatu Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Wdrożyć plik WAR

Aby wdrożyć plik WAR w usłudze App Service, wysłanie żądania POST do https://<app_name>.scm.azurewebsites.net/api/wardeploy. Żądanie POST musi zawierać plik WAR w treści wiadomości. Poświadczenia wdrażania dla aplikacji są podawane w żądaniu za pomocą podstawowego uwierzytelniania HTTP. 

W przypadku uwierzytelniania HTTP BASIC potrzebne są Twoje poświadczenia wdrożenia usługi App Service. Aby zobaczyć, jak ustawić poświadczenia wdrażania, zobacz [zestawu i zresetować poświadczenia na poziomie użytkownika](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Za pomocą programu cURL

W poniższym przykładzie użyto narzędzia cURL do wdrożenia pliku WAR. Zastąp symbole zastępcze `<username>`, `<war_file_path>`, i `<app_name>`. Po wyświetleniu monitu przez program cURL wpisz hasło.

```bash
curl -X POST -u <username> --data-binary @"<war_file_path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Z programem PowerShell

W poniższym przykładzie użyto [Invoke RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) można wysłać żądania, który zawiera plik WAR. Zastąp symbole zastępcze `<deployment_user>`, `<deployment_password>`, `<zip_file_path>`, i `<app_name>`.

```powershell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<war_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/wardeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Kolejne kroki

Dla bardziej zaawansowanych scenariuszy wdrażania, spróbuj [wdrażania na platformie Azure przy użyciu narzędzia Git](deploy-local-git.md). Wdrożenie oparte na usłudze Git na platformie Azure umożliwia kontrolę wersji, Przywracanie pakietu, program MSBuild i.

## <a name="more-resources"></a>Więcej zasobów

* [Kudu: Wdrażanie z pliku zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Poświadczenia wdrożenia usługi Azure App Service](deploy-ftp.md)
