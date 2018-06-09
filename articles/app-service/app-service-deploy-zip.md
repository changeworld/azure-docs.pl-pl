---
title: Wdrażanie aplikacji w usłudze Azure App Service przy użyciu pliku ZIP albo WOJENNE | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: a3178d5cb09087a243a51e20567895d03ce1f7fb
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234138"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Wdrażanie aplikacji w usłudze Azure App Service przy użyciu pliku ZIP albo WOJENNE

W tym artykule przedstawiono sposób wdrażania aplikacji sieci web za pomocą pliku ZIP lub plik WAR [usłudze Azure App Service](app-service-web-overview.md). 

To wdrożenie pliku ZIP używa tej samej usługi Kudu tego uprawnienia ciągłej integracji wdrożenia oparte na. Aparat kudu dla wdrożenia pliku ZIP obsługuje następujące funkcje: 

- Usuwanie plików pozostała z poprzedniego wdrożenia.
- Przywracanie pakietów opcję, aby włączyć domyślny proces kompilacji, w tym.
- [Dostosowanie wdrożenia](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), takie jak uruchamianie skryptów wdrażania.  
- Dzienniki wdrożenia. 

Aby uzyskać więcej informacji, zobacz [dokumentacji Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Wdraża wdrożenia pliku WAR Twojej [WAR](https://wikipedia.org/wiki/WAR_(file_format)) pliku do usługi aplikacji w celu uruchomienia aplikacji sieci web Java. Zobacz [pliku WAR wdrażanie](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule:

* [Utwórz aplikację usługi App Service](/azure/app-service/) lub użyj aplikacji utworzonej w innym samouczku.

## <a name="create-a-project-zip-file"></a>Tworzenie pliku ZIP projektu

>[!NOTE]
> Jeśli pobrano pliki w pliku ZIP, Wyodrębnij pliki. Na przykład, jeśli plik ZIP został pobrany z usługi GitHub, nie można wdrożyć tego pliku jako — jest. GitHub dodaje dodatkowe katalogi zagnieżdżone, które nie działają z usługi aplikacji. 
>

W lokalnej okno terminalu przejdź do katalogu głównego projektu aplikacji. 

Ten katalog powinien zawierać plik wejścia do aplikacji sieci web, takich jak _index.html_, _index.php_, i _app.js_. Może również zawierać pakiet zarządzania plików, takich jak _project.json_, _composer.json_, _package.json_, _bower.json_i _requirements.txt_.

Utwórz archiwum ZIP z wszystkimi elementami w projekcie. Następujące polecenie używa domyślnego narzędzia w terminalu:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="deploy-zip-file-with-azure-cli"></a>Wdróż plik ZIP z wiersza polecenia platformy Azure

Upewnij się, że wersja interfejsu wiersza polecenia Azure jest 2.0.21 lub nowszy. Aby wyświetlić wersję Uruchom `az --version` polecenie w oknie terminala.

Wdrażanie przekazanego pliku ZIP do aplikacji sieci web przy użyciu [az aplikacji sieci Web wdrożenia źródła config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip) polecenia.  

Poniższy przykład wdraża pliku ZIP, który został przekazany. Korzystając z lokalną instalacją wiersza polecenia platformy Azure, określ ścieżkę do pliku ZIP lokalnego dla `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

To polecenie wdraża pliki i katalogi z pliku ZIP do domyślnego folderu aplikacji App Service (`\home\site\wwwroot`) i ponownie uruchamia aplikację. Jeśli został skonfigurowany jakikolwiek dodatkowy niestandardowy proces kompilacji, również zostanie uruchomiony. Aby uzyskać więcej informacji, zobacz [dokumentacji Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Wdróż plik WAR

Aby wdrożyć plik WAR do usługi App Service, wysyła żądanie POST na https://<app_name>.scm.azurewebsites.net/api/wardeploy. Żądanie POST musi zawierać plik WAR w treści wiadomości. Poświadczenia wdrażania dla aplikacji są podawane w żądaniu za pomocą podstawowego uwierzytelniania HTTP. 

Uwierzytelnianie podstawowe HTTP należy poświadczenia wdrażania usługi aplikacji. Aby sprawdzić, jak ustawić poświadczenia wdrażania, zobacz [ustawić i zresetowanie poświadczeń na poziomie użytkownika](app-service-deployment-credentials.md#userscope).

### <a name="with-curl"></a>Z cURL

W poniższym przykładzie użyto narzędzie cURL można wdrożyć pliku .war. Zastąp symbole zastępcze `<username>`, `<war_file_path>`, i `<app_name>`. Po wyświetleniu monitu przez narzędzie cURL, wpisz hasło.

```bash
curl -X POST -u <username> --data-binary @"<war_file_path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Z programem PowerShell

W poniższym przykładzie użyto [Invoke RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) można wysłać żądania, który zawiera plik .war. Zastąp symbole zastępcze `<deployment_user>`, `<deployment_password>`, `<zip_file_path>`, i `<app_name>`.

```PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<war_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/wardeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Kolejne kroki

W przypadku bardziej zaawansowanych scenariuszy wdrażania, spróbuj [wdrażanie na platformie Azure za pomocą narzędzia Git](app-service-deploy-local-git.md). Wdrożenie oparte na Git na platformie Azure umożliwia kontroli wersji, Przywracanie pakietu MSBuild i więcej.

## <a name="more-resources"></a>Więcej zasobów

* [Program kudu: Wdrażanie z pliku zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Poświadczenia wdrożenia usługi aplikacji Azure](app-service-deploy-ftp.md)
