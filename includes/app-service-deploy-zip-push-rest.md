---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 92e39f128e90ba83a919388e217f0edc86f81770
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769674"
---
## <a name="rest"></a>Wdróż plik ZIP przy użyciu interfejsów API REST 

Za pomocą [interfejsów API REST usługi wdrażania](https://github.com/projectkudu/kudu/wiki/REST-API) można wdrożyć plik zip w aplikacji na platformie Azure. W celu wdrożenia Wyślij żądanie POST do protokołu https://< app_name >. SCM. azurewebsites. NET/API/zipdeploy. Żądanie POST musi zawierać plik. zip w treści wiadomości. Poświadczenia wdrażania dla aplikacji są podawane w żądaniu za pomocą podstawowego uwierzytelniania HTTP. Aby uzyskać więcej informacji, zobacz [. zip — informacje o wdrożeniu.](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file) 

W przypadku uwierzytelniania podstawowego HTTP wymagane są poświadczenia wdrażania App Service. Aby dowiedzieć się, jak ustawić poświadczenia wdrożenia, zobacz [Ustawianie i resetowanie poświadczeń na poziomie użytkownika](../articles/app-service/deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Z zwinięciem

W poniższym przykładzie za pomocą narzędzia zwinięcie można wdrożyć plik. zip. Zastąp symbole zastępcze `<deployment_user>`, `<zip_file_path>`i `<app_name>`. Po wyświetleniu monitu przez zwinięcie wpisz hasło.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

To żądanie wyzwala wdrożenie wypychane z przekazanego pliku zip. Bieżące i wcześniejsze wdrożenia można przejrzeć przy użyciu punktu końcowego `https://<app_name>.scm.azurewebsites.net/api/deployments`, jak pokazano w poniższym przykładzie. Ponownie Zastąp `<app_name>` nazwą swojej aplikacji i `<deployment_user>` nazwę użytkownika poświadczeń wdrożenia.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>Z programem PowerShell

Poniższy przykład używa [publikowania-AzWebapp](/powershell/module/az.websites/publish-azwebapp) Przekaż plik. zip. Zastąp symbole zastępcze `<group-name>`, `<app-name>`i `<zip-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

To żądanie wyzwala wdrożenie wypychane z przekazanego pliku zip. 

Aby przejrzeć bieżące i wcześniejsze wdrożenia, uruchom następujące polecenia. Ponownie Zastąp symbole zastępcze `<deployment-user>`, `<deployment-password>`i `<app-name>`.

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
