---
title: Wdrażanie kodu za pomocą pliku ZIP lub WAR
description: Dowiedz się, jak wdrożyć aplikację w usłudze Azure App Service za pomocą pliku ZIP (lub pliku WAR dla deweloperów oprogramowania Java).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 716f6813e37aec086a7d496e001fe2ca0f4aab57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945182"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Wdrażanie aplikacji w usłudze Azure App Service za pomocą pliku ZIP lub WAR

W tym artykule pokazano, jak za pomocą pliku ZIP lub pliku WAR wdrożyć aplikację internetową w [usłudze Azure App Service](overview.md). 

To wdrożenie pliku ZIP używa tej samej usługi Kudu, która zasila ciągłe wdrożenia oparte na integracji. Kudu obsługuje następujące funkcje wdrażania plików ZIP: 

- Usuwanie plików pozostałych po poprzednim wdrożeniu.
- Opcja, aby włączyć domyślny proces kompilacji, który obejmuje przywracanie pakietu.
- Dostosowywanie wdrażania, w tym uruchamianie skryptów wdrażania.  
- Dzienniki wdrażania. 
- Limit rozmiaru pliku 2048 MB.

Więcej informacji zawiera [dokumentacja aparatu Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Wdrożenie pliku WAR wdraża plik [WAR](https://wikipedia.org/wiki/WAR_(file_format)) w usłudze App Service w celu uruchomienia aplikacji internetowej Java. Zobacz [Wdrażanie pliku WAR](#deploy-war-file).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, [utwórz aplikację usługi App Service](/azure/app-service/)lub użyj aplikacji utworzonej dla innego samouczka.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
Powyższy punkt końcowy nie działa w tej chwili dla usług aplikacji systemu Linux. Zamiast tego należy rozważyć użycie protokołu FTP lub interfejsu API wdrażania usługi [ZIP.](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment)

## <a name="deploy-zip-file-with-azure-cli"></a>Wdrażanie pliku ZIP za pomocą interfejsu wiersza polecenia platformy Azure

Wdrażanie przekazanego pliku ZIP w aplikacji sieci web przy użyciu polecenia [az webapp deployment source config-zip.](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip)  

W poniższym przykładzie wdraża przekazany plik ZIP. Korzystając z lokalnej instalacji interfejsu wiersza polecenia platformy `--src`Azure, określ ścieżkę do lokalnego pliku ZIP dla programu .

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

To polecenie wdraża pliki i katalogi z pliku ZIP do domyślnego folderu aplikacji App Service (`\home\site\wwwroot`) i ponownie uruchamia aplikację.

Domyślnie aparat wdrażania zakłada, że plik ZIP jest gotowy do uruchomienia w stanie takim, w jakim jest i nie uruchamia żadnej automatyzacji kompilacji. Aby włączyć taką samą automatyzację kompilacji, `SCM_DO_BUILD_DURING_DEPLOYMENT` jak we [wdrożeniu Git,](deploy-local-git.md)ustaw ustawienie aplikacji, uruchamiając następujące polecenie w aplikacji Cloud [Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Więcej informacji zawiera [dokumentacja aparatu Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Wdrażanie pliku WAR

Aby wdrożyć plik WAR w usłudze `https://<app-name>.scm.azurewebsites.net/api/wardeploy`App Service, wyślij żądanie POST do . Żądanie POST musi zawierać plik WAR w treści wiadomości. Poświadczenia wdrażania dla aplikacji są podawane w żądaniu za pomocą podstawowego uwierzytelniania HTTP.

Zawsze `/api/wardeploy` używaj podczas wdrażania plików WAR. Ten interfejs API rozwinie plik WAR i umieści go na udostępnionym dysku plików. przy użyciu innych interfejsów API wdrażania może spowodować niespójne zachowanie. 

Do uwierzytelniania HTTP BASIC potrzebne są poświadczenia wdrożenia usługi App Service. Aby zobaczyć, jak ustawić poświadczenia wdrożenia, zobacz [Ustawianie i resetowanie poświadczeń na poziomie użytkownika](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Z cURL

W poniższym przykładzie użyto narzędzia cURL do wdrożenia pliku .war. Zastąp `<username>`symbole zastępcze , `<war-file-path>`i `<app-name>`. Po wyświetleniu monitu przez cURL wpisz hasło.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Z programem PowerShell

W poniższym przykładzie użyto [publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) przekazać plik .war. Zastąp `<group-name>`symbole zastępcze , `<app-name>`i `<war-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać bardziej zaawansowane scenariusze wdrażania, spróbuj [wdrożyć na platformie Azure za pomocą git](deploy-local-git.md). Wdrożenie oparte na git na platformie Azure umożliwia kontrolę wersji, przywracanie pakietów, MSBuild i inne.

## <a name="more-resources"></a>Więcej zasobów

* [Kudu: Wdrażanie z pliku zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Poświadczenia wdrażania usługi Azure App Service](deploy-ftp.md)
