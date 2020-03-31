---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 92e39f128e90ba83a919388e217f0edc86f81770
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769674"
---
## <a name="deploy-zip-file-with-rest-apis"></a><a name="rest"></a>Wdrażanie pliku ZIP za pomocą interfejsów API REST 

Za pomocą [interfejsów API REST usługi wdrażania](https://github.com/projectkudu/kudu/wiki/REST-API) można wdrożyć plik zip w aplikacji na platformie Azure. Aby wdrożyć, wyślij żądanie POST na adres https://<app_name>.scm.azurewebsites.net/api/zipdeploy. Żądanie POST musi zawierać plik zip w treści wiadomości. Poświadczenia wdrażania dla aplikacji są podawane w żądaniu za pomocą podstawowego uwierzytelniania HTTP. Aby uzyskać więcej informacji, zobacz [odwołanie do wdrożenia wypychania .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

Do uwierzytelniania HTTP BASIC potrzebne są poświadczenia wdrożenia usługi App Service. Aby zobaczyć, jak ustawić poświadczenia wdrożenia, zobacz [Ustawianie i resetowanie poświadczeń na poziomie użytkownika](../articles/app-service/deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Z cURL

W poniższym przykładzie użyto narzędzia cURL do wdrożenia pliku zip. Zastąp `<deployment_user>`symbole zastępcze , `<zip_file_path>`i `<app_name>`. Po wyświetleniu monitu przez cURL wpisz hasło.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

To żądanie wyzwala wdrożenie wypychania z przekazanego pliku zip. Można przejrzeć bieżące i przeszłe wdrożenia przy użyciu punktu końcowego, `https://<app_name>.scm.azurewebsites.net/api/deployments` jak pokazano w poniższym przykładzie cURL. Ponownie zastąp `<app_name>` nazwą aplikacji `<deployment_user>` i nazwą użytkownika poświadczeń wdrożenia.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>Z programem PowerShell

W poniższym przykładzie użyto [publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) przekazać plik zip. Zastąp `<group-name>`symbole zastępcze , `<app-name>`i `<zip-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

To żądanie wyzwala wdrożenie wypychania z przekazanego pliku zip. 

Aby przejrzeć bieżące i poprzednie wdrożenia, uruchom następujące polecenia. Ponownie `<deployment-user>`wymień `<deployment-password>`symbole zastępcze i `<app-name>` symbole zastępcze.

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
