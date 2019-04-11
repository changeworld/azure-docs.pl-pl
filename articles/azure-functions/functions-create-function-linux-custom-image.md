---
title: Tworzenie usługi Azure Functions w systemie Linux przy użyciu niestandardowego obrazu
description: Informacje o sposobie tworzenia usługi Azure Functions działającej w niestandardowym obrazie systemu Linux.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 02/25/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 03e1ec58b0ef3ad50a04f82ced7d20119ab3ef5b
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470070"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Tworzenie funkcji w systemie Linux przy użyciu niestandardowego obrazu

Usługa Azure Functions umożliwia hostowanie funkcji w systemie Linux w utworzonym przez użytkownika kontenerze niestandardowym. Można również [hostować w domyślnym kontenerze usługi Azure App Service](functions-create-first-azure-function-azure-cli-linux.md). Ta funkcja wymaga [środowisko uruchomieniowe 2.x funkcji](functions-versions.md).

Z tego samouczka dowiesz się, jak wdrożyć funkcje na platformie Azure jako obraz niestandardowy platformy Docker. Ten wzorzec jest przydatny, gdy musisz dostosować wbudowany obraz kontenera usługi App Service. Użycie obrazu niestandardowego może być przydatne w przypadku, kiedy funkcje wymagają konkretnej wersji języka lub konkretnej zależności bądź konfiguracji, które nie są dostarczone w obrazie wbudowanym. Obsługiwane obrazy podstawowe dla usługi Azure Functions można znaleźć w [usługi Azure Functions utworzyć repozytorium obrazów](https://hub.docker.com/_/microsoft-azure-functions-base). [Obsługa języka Python](functions-reference-python.md) znajduje się w tej chwili w wersji zapoznawczej.

Ten samouczek przedstawia sposób użycia narzędzi Azure Functions Core Tools do tworzenia funkcji w niestandardowym obrazie systemu Linux. Ten obraz jest publikowany na platformie Azure w aplikacji funkcji, która została utworzona przy użyciu interfejsu wiersza polecenia platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji funkcji i pliku Dockerfile przy użyciu podstawowych narzędzi.
> * Kompilowanie obrazu niestandardowego na platformie Docker.
> * Publikowanie obrazu niestandardowego w rejestrze kontenerów.
> * Tworzenie konta usługi Azure Storage.
> * Tworzenie planu usługi App Service dla systemu Linux.
> * Wdrażanie aplikacji funkcji z usługi Docker Hub.
> * Dodawanie ustawień aplikacji do aplikacji funkcji.
> * Włącz ciągłe wdrażanie

Poniższe kroki można wykonać na komputerze Mac, w systemie Windows lub w systemie Linux.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed uruchomieniem tego przykładu należy dysponować następującymi elementami:

* Zainstaluj narzędzia [Azure Core Tools w wersji 2.x](functions-run-local.md#v2).

* Zainstaluj [interfejs wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz.  
Możesz również użyć usługi [Azure Cloud Shell](https://shell.azure.com/bash).

* Aktywna subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Tworzenie lokalnego projektu aplikacji funkcji

Uruchom następujące polecenie w wierszu polecenia, aby utworzyć projekt aplikacji funkcji w folderze `MyFunctionProj` bieżącego katalogu lokalnego.

```bash
func init MyFunctionProj --docker
```

Jeśli dołączysz opcję `--docker`, zostanie wygenerowany plik dockerfile dla projektu. Ten plik służy do tworzenia niestandardowego kontenera, w którym chcesz uruchomić projekt. Użyty obraz podstawowy zależy od wybranego języka środowiska uruchomieniowego procesów roboczych.  

Po wyświetleniu monitu wybierz środowisko uruchomieniowe procesów roboczych spośród następujących języków:

* `dotnet`: tworzy projekt biblioteki klas platformy .NET (.csproj).
* `node`: tworzy projektu w języku JavaScript.
* `python`: tworzy projekt języka Python.

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

Gdy polecenie zostanie wykonane, zostaną zwrócone dane wyjściowe podobne do następujących:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing Dockerfile
```

Użyj następującego polecenia, aby przejść do nowego folderu projektu `MyFunctionProj`.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-the-image-from-the-docker-file"></a>Tworzenie obrazu na podstawie pliku platformy Docker

Przyjrzyj się plikowi _Dockerfile_ w folderze głównym projektu. W tym pliku opisano środowisko wymagane do uruchomienia aplikacji funkcji w systemie Linux. Poniższy przykład to plik Dockerfile, który tworzy kontener uruchamiający aplikację funkcji w środowisku uruchomieniowym procesów roboczych języka JavaScript (Node.js): 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> Pełną listę obsługiwanych obrazy podstawowe dla usługi Azure Functions można znaleźć w [stronie obrazu podstawowego z usługi Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).

### <a name="run-the-build-command"></a>Uruchamianie polecenia `build`
W folderze głównym uruchom polecenie [docker build](https://docs.docker.com/engine/reference/commandline/build/) i podaj nazwę, element `mydockerimage` oraz tag `v1.0.0`. Zastąp ciąg `<docker-id>` identyfikatorem konta usługi Docker Hub. To polecenie powoduje skompilowanie obrazu platformy Docker dla kontenera.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Gdy polecenie zostanie wykonane, zostaną zwrócone dane wyjściowe podobne do następujących. W tym przypadku będą one dotyczyć środowiska uruchomieniowego procesów roboczych JavaScript:

```bash
Sending build context to Docker daemon  17.41kB
Step 1/3 : FROM mcr.microsoft.com/azure-functions/node:2.0
2.0: Pulling from azure-functions/node
802b00ed6f79: Pull complete
44580ea7a636: Pull complete
73eebe8d57f9: Pull complete
3d82a67477c2: Pull complete
8bd51cd50290: Pull complete
7bd755353966: Pull complete
Digest: sha256:480e969821e9befe7c61dda353f63298f2c4b109e13032df5518e92540ea1d08
Status: Downloaded newer image for mcr.microsoft.com/azure-functions/node:2.0
 ---> 7c71671b838f
Step 2/3 : ENV AzureWebJobsScriptRoot=/home/site/wwwroot
 ---> Running in ed1e5809f0b7
Removing intermediate container ed1e5809f0b7
 ---> 39d9c341368a
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5e196215935a
Successfully built 5e196215935a
Successfully tagged <docker-id>/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Lokalne testowanie obrazu
Sprawdź, czy skompilowany obraz działa, uruchamiając obraz platformy Docker w kontenerze lokalnym. Uruchom polecenie [docker run](https://docs.docker.com/engine/reference/commandline/run/) i przekaż do niego nazwę oraz tag obrazu. Pamiętaj o określeniu portu przy użyciu argumentu `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Po uruchomieniu obrazu niestandardowego w lokalnym kontenerze Docker sprawdź, czy aplikacja funkcji i kontener działają prawidłowo, przechodząc do adresu <http://localhost:8080>.

![Lokalne testowanie aplikacji funkcji](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Opcjonalnie można ponownie przetestować funkcję, tym razem w kontenerze lokalnym przy użyciu następującego adresu URL:

`http://localhost:8080/api/myhttptrigger?name=<yourname>`

Po zweryfikowaniu aplikacji funkcji w kontenerze zatrzymaj wykonywanie. Teraz możesz wypchnąć obraz niestandardowy do swojego konta w usłudze Docker Hub.

## <a name="push-the-custom-image-to-docker-hub"></a>Wypychanie obrazu niestandardowego do usługi Docker Hub

Rejestr to aplikacja hostująca obrazy i udostępniająca usługi obrazów oraz kontenerów. Aby udostępnić obraz, należy wypchnąć go do rejestru. Usługa Docker Hub jest rejestrem obrazów platformy Docker, który umożliwia hostowanie własnych repozytoriów — publicznych lub prywatnych.

Przed wypchnięciem obrazu należy najpierw zalogować się w usłudze Docker Hub przy użyciu polecenia [docker login](https://docs.docker.com/engine/reference/commandline/login/). Zastąp ciąg `<docker-id>` nazwą swojego konta i wpisz swoje hasło w konsoli, gdy zostanie wyświetlony odpowiedni monit. Inne opcje dotyczące hasła usługi Docker Hub opisano w [dokumentacji polecenia docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

Pomyślne logowanie zostanie potwierdzone stosownym komunikatem. Po zalogowaniu się można wypchnąć obraz do usługi Docker Hub przy użyciu polecenia [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Sprawdź, czy wypchnięcie się powiodło, badając dane wyjściowe polecenia.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from <docker-id>/mydockerimage
e7796c35add2: Mounted from <docker-id>/mydockerimage
ae9a05b85848: Mounted from <docker-id>/mydockerimage
45c86e20670d: Mounted from <docker-id>/mydockerimage
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 1796
```

Teraz możesz użyć tego obrazu jako źródła wdrażania nowej aplikacji funkcji na platformie Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Tworzenie planu usługi App Service dla systemu Linux

Hosting systemu Linux dla usługi Functions nie jest obecnie obsługiwany w ramach planów zużycia. Aplikacje kontenera systemu Linux należy hostować w ramach planu usługi App Service w systemie Linux. Aby dowiedzieć się więcej o hostingu, zobacz [Porównanie planów hostingu usługi Azure Functions](functions-scale.md).

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-and-deploy-the-custom-image"></a>Tworzenie i wdrażanie obrazu niestandardowego

Aplikacja funkcji obsługuje wykonywanie funkcji. Utwórz aplikację funkcji na podstawie obrazu usługi Docker Hub przy użyciu polecenia [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

W poniższym poleceniu w miejsce symbolu zastępczego `<app_name>` wstaw unikatową nazwę aplikacji funkcji, a w miejsce symbolu zastępczego `<storage_name>` wstaw nazwę konta magazynu. Nazwa `<app_name>` jest używana jako domyślna domena DNS aplikacji funkcji, więc nazwa ta musi być unikatowa wśród wszystkich aplikacji na platformie Azure. Tak samo jak wcześniej `<docker-id>` to nazwa konta platformy Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

Po utworzeniu aplikacji funkcji interfejs wiersza polecenia platformy Azure wyświetli informacje podobne do następujących:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

Parametr _deployment-container-image-name_ wskazuje obraz hostowany w usłudze Docker Hub, który zostanie użyty do utworzenia aplikacji funkcji. Użyj [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) polecenie, aby wyświetlić informacje o obrazie używane w ramach wdrożenia. Użyj [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) polecenie, aby wdrożyć z innego obrazu.

## <a name="configure-the-function-app"></a>Konfigurowanie aplikacji funkcji

Do łączenia się z domyślnym kontem magazynu funkcja wymaga parametrów połączenia. W przypadku publikowania obrazu niestandardowego w prywatnym koncie kontenera należy ustawić te parametry połączenia jako zmienne środowiskowe w pliku Dockerfile przy użyciu [instrukcji ENV](https://docs.docker.com/engine/reference/builder/#env) lub podobnej.

W tym przypadku `<storage_name>` jest nazwą utworzonego konta magazynu. Parametry połączenia można uzyskać za pomocą polecenia [az storage account show-connection-string](/cli/azure/storage/account). Dodaj te ustawienia aplikacji w aplikacji funkcji za pomocą polecenia [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> Jeśli Twój kontener jest prywatny, musisz też skonfigurować następujące ustawienia aplikacji  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> Musisz zatrzymać i ponownie uruchomić aplikację funkcji, aby te wartości zostały pobrane

Teraz można przetestować funkcje działające w systemie Linux na platformie Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

Zalecanym sposobem monitorowania wykonywania funkcji jest integrowanie aplikacji funkcji z usługi Azure Application Insights. Po utworzeniu aplikacji funkcji w witrynie Azure portal, ta Integracja jest wykonywane domyślnie. Jednak po utworzeniu aplikacji funkcji przy użyciu wiersza polecenia platformy Azure, integracji w Twojej aplikacji funkcji na platformie Azure nie jest wykonywane.

Aby włączyć usługę Application Insights dla aplikacji funkcji:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Aby dowiedzieć się więcej, zobacz [monitora usługi Azure Functions](functions-monitoring.md).

## <a name="enable-continuous-deployment"></a>Włącz ciągłe wdrażanie

Jedną z zalet przy użyciu kontenerów jest możliwość automatycznego wdrażania aktualizacji po zaktualizowaniu kontenerów w rejestrze. Włącz ciągłe wdrażanie za pomocą [az functionapp deployment kontenera konfiguracji](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) polecenia.

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

To polecenie zwraca adres URL elementu webhook wdrożenia po włączeniu ciągłego wdrażania. Można również użyć [az functionapp deployment show-cd — adres url kontenera](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) polecenie, aby zwrócić ten adres URL. 

Skopiuj adres URL wdrożenia i przejdź do repozytorium DockerHub, wybierz **elementów Webhook** karty, wpisz **nazwa elementu Webhook** dla elementu webhook, wklej adres URL w **adresu URL elementu Webhook**, a następnie wybierz pozycję znak plus (**+**).

![Dodanie elementu webhook w repozytorium w witrynie DockerHub](media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

Za pomocą zestawu elementów webhook wszelkie aktualizacje obraz połączony w witrynie DockerHub doprowadzić do aplikacji funkcji, pobieranie i instalowanie najnowszego obrazu.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji funkcji i pliku Dockerfile przy użyciu podstawowych narzędzi.
> * Kompilowanie obrazu niestandardowego na platformie Docker.
> * Publikowanie obrazu niestandardowego w rejestrze kontenerów.
> * Tworzenie konta usługi Azure Storage.
> * Tworzenie planu usługi App Service dla systemu Linux.
> * Wdrażanie aplikacji funkcji z usługi Docker Hub.
> * Dodawanie ustawień aplikacji do aplikacji funkcji.

Dowiedz się, jak włączyć funkcję ciągłej integracji wbudowaną na podstawowej platformie App Service. Aplikację funkcji możesz skonfigurować tak, aby po zaktualizowaniu obrazu w usłudze Docker Hub kontener był wdrażany ponownie.

> [!div class="nextstepaction"] 
> [Ciągłe wdrażanie za pomocą usługi Web App for Containers](../app-service/containers/app-service-linux-ci-cd.md)
