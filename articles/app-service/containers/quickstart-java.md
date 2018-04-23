---
title: Tworzenie aplikacji internetowej w języku Java w usłudze Azure App Service w systemie Linux
description: Wdróż swoją pierwszą aplikację Hello World w języku Java w usłudze Azure App Service w systemie Linux w ciągu kilku minut.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/07/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 657a5a72650b330323406703d2c479c96c096f2e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="preview-create-a-java-web-app-in-app-service-on-linux"></a>Wersja zapoznawcza: tworzenie aplikacji internetowej w języku Java w usłudze App Service w systemie Linux

Usługa App Service w systemie Linux udostępnia obecnie funkcję w wersji zapoznawczej do obsługi aplikacji internetowych w języku Java. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Wdrażanie aplikacji internetowych w języku Java w kontenerze systemu Linux w chmurze przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-hello-world-web-app-linux) to alternatywny sposób wdrażania aplikacji w języku Java we własnym kontenerze.

> [!NOTE]
> W tym artykule opisano wdrażanie aplikacji internetowej w języku Java w usłudze App Service w systemie Linux.
>

Usługa [App Service w systemie Linux](app-service-linux-intro.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux. W tym przewodniku Szybki start przedstawiono, jak wdrożyć aplikację internetową w języku Java w usłudze App Service w systemie Linux przy użyciu wbudowanego obrazu. Aplikację internetową z wbudowanym obrazem można utworzyć przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli). Następnie aplikację w języku Java można wdrożyć w ramach aplikacji internetowej.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start: 

* Wymagana jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zainstaluj oprogramowanie Git](https://git-scm.com/).
* Zainstaluj środowisko [Eclipse](https://www.eclipse.org/downloads/).



[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]


## <a name="create-a-web-app"></a>Tworzenie aplikacji sieci Web

W usłudze Cloud Shell utwórz [aplikację internetową](../app-service-web-overview.md) w planie usługi App Service `myAppServicePlan`. Możesz zrobić to za pomocą polecenia [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). W poniższym przykładzie zastąp ciąg *\<nazwa_aplikacji>* globalnie unikatową nazwą aplikacji (prawidłowe znaki to `a-z`, `0-9` i `-`). 

```azurecli-interactive
# Bash
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --runtime "TOMCAT|8.5-jre8"
# PowerShell
az --% webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --runtime "TOMCAT|8.5-jre8"
```

Dla parametru **runtime** użyj jednego z następujących środowisk uruchomieniowych:
 * TOMCAT|8.5-jre8
 * TOMCAT|9.0-jre8


Po utworzeniu aplikacji internetowej w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących:

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<your web app name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<your web app name>.azurewebsites.net",
    "<your web app name>.scm.azurewebsites.net"
  ],
  "ftpPublishingUrl": "ftp://<your ftp URL>",  
  < JSON data removed for brevity. >
}
```

Skopiuj wartość dla właściwości **ftpPublishingUrl**. Zostanie ona użyta później w przypadku wybrania wdrożenia za pomocą połączenia FTP.

Przejdź do nowo utworzonej aplikacji internetowej.

```
http://<app_name>.azurewebsites.net
```

Jeśli aplikacja internetowa jest uruchomiona, powinien zostać wyświetlony ekran domyślny podobny do tego z poniższej ilustracji:

![Przechodzenie do aplikacji internetowej przed wdrożeniem](media/quickstart-java/browse-web-app-not-deployed.png)


## <a name="download-the-sample-java-app"></a>Pobieranie przykładowej aplikacji w języku Java

W oknie terminala na maszynie uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na maszynę lokalną. Ta przykładowa aplikacja zostanie wdrożona w późniejszym kroku.

```bash
git clone https://github.com/Azure-Samples/java-docs-hello-world
```


## <a name="deploying-the-java-app-to-app-service-on-linux"></a>Wdrażanie aplikacji w języku Java w usłudze App Service w systemie Linux

Otwórz przykładowy projekt w środowisku [Eclipse](https://www.eclipse.org/downloads/), a następnie [wyeksportuj aplikację w języku Java do pliku archiwum internetowego (WAR)](http://help.eclipse.org/kepler/index.jsp?topic=%2Forg.eclipse.wst.webtools.doc.user%2Ftopics%2Ftwcrewar.html) o nazwie `helloworld.war`.

Aby wdrożyć plik WAR aplikacji w języku Java, możesz użyć narzędzia WarDeploy (obecnie w [wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)) lub połączenia FTP.

W zależności od używanej metody wdrażania ścieżka względna służąca do przejścia do aplikacji internetowej w języku Java może się nieco różnić.

### <a name="deploy-with-wardeploy"></a>Wdrażanie za pomocą narzędzia WarDeploy 

Aby wdrożyć plik WAR za pomocą narzędzia WarDeploy, użyj następującego przykładowego wiersza polecenia programu cURL do wysłania żądania POST na adres *https://<your app name>.scm.azurewebsites.net/api/wardeploy*. Żądanie POST musi zawierać plik WAR w treści wiadomości. Poświadczenia wdrażania dla aplikacji są podawane w żądaniu za pomocą podstawowego uwierzytelniania HTTP. Aby uzyskać więcej informacji o narzędziu WarDeploy, zobacz [Wdrażanie aplikacji w usłudze Azure App Service za pomocą pliku ZIP lub WAR](../app-service-deploy-zip.md).

```bash
curl -X POST -u <username> --data-binary @"<war_file_path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

Zaktualizuj następujące elementy:

* `username` — użyj utworzonej wcześniej nazwy użytkownika poświadczeń wdrażania.
* `war_file_path` — użyj lokalnej ścieżki do pliku WAR.
* `app_name` — użyj utworzonej wcześniej nazwy aplikacji.

Wykonaj polecenie. Po wyświetleniu monitu przez program cURL wpisz hasło na potrzeby poświadczeń wdrażania.

W przeglądarce internetowej przejdź do wdrożonej aplikacji, używając następującego adresu URL.

```bash
http://<app_name>.azurewebsites.net
```

Przykładowy kod w języku Java jest uruchamiany w aplikacji internetowej z wbudowanym obrazem.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-java/java-hello-world-in-browser.png)

W przeglądarce internetowej przejdź do serwletu.

```bash
http://<app_name>.azurewebsites.net/HelloWorldServlet
```

Serwlet jest uruchamiany w aplikacji internetowej z wbudowanym obrazem.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-java/java-hello-world-servlet-in-browser.png)



**Gratulacje!** Udało Ci się wdrożyć pierwszą własną aplikację w języku Java w usłudze App Service w systemie Linux.



### <a name="ftp-deployment"></a>Wdrażanie za pomocą połączenia FTP

Do wdrożenia pliku WAR możesz również użyć połączenia FTP. 

Wyślij plik za pomocą połączenia FTP do katalogu */home/site/wwwroot/webapps* aplikacji internetowej. Program cURL jest używany w następujących przykładowych wierszach polecenia:

```bash
curl -T war_file_path -u "app_name\username" ftp://webappFTPURL/site/wwwroot/webapps/
```

Zaktualizuj następujące elementy:

* `war_file_path` — użyj lokalnej ścieżki do pliku WAR.
* `app_name` — użyj utworzonej wcześniej nazwy aplikacji.
* `username` — użyj utworzonej wcześniej nazwy użytkownika poświadczeń wdrażania.
* `webappFTPURL` — użyj skopiowanej wcześniej wartości **nazwy hosta FTP** dla aplikacji internetowej. Nazwa hosta FTP jest również dostępna w bloku **Omówienie** bloku aplikacji internetowej w witrynie [Azure Portal](https://portal.azure.com/).

Wykonaj polecenie. Po wyświetleniu monitu przez program cURL wpisz hasło na potrzeby poświadczeń wdrażania.


W przeglądarce internetowej przejdź do wdrożonej aplikacji, używając następującego adresu URL.

```bash
http://<app_name>.azurewebsites.net/helloworld
```

Przykładowy kod w języku Java jest uruchamiany w aplikacji internetowej z wbudowanym obrazem.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-java/java-hello-world-in-browser-curl.png)

W przeglądarce internetowej przejdź do serwletu.

```bash
http://<app_name>.azurewebsites.net/helloworld/HelloWorldServlet
```

Przykładowy kod w języku Java jest uruchamiany w aplikacji internetowej z wbudowanym obrazem.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-java/java-hello-world-servlet-in-browser-curl.png)



**Gratulacje!** Udało Ci się wdrożyć pierwszą własną aplikację w języku Java w usłudze App Service w systemie Linux.



[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o używaniu języka Java wraz z platformą Azure, skorzystaj z następujących linków:

* [Azure dla deweloperów języka Java](https://docs.microsoft.com/java/azure/)
* [Wdrażanie aplikacji internetowej Hello World w kontenerze systemu Linux w chmurze przy użyciu zestawu narzędzi platformy Azure dla środowiska IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-hello-world-web-app-linux)
* [Narzędzia języka Java dla usługi Visual Studio Team Services](https://java.visualstudio.com/)
