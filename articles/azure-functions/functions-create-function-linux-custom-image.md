---
title: Tworzenie funkcji w systemie Linux przy użyciu niestandardowego obrazu (wersja zapoznawcza) | Microsoft Docs
description: Informacje o sposobie tworzenia usługi Azure Functions działającej w niestandardowym obrazie systemu Linux.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: eb03d2dae42d656e62b8f02eeccf1f7312f07732
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962445"
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>Tworzenie funkcji w systemie Linux przy użyciu niestandardowego obrazu (wersja zapoznawcza)

Usługa Azure Functions umożliwia hostowanie funkcji w systemie Linux w utworzonym przez użytkownika kontenerze niestandardowym. Można również [hostować w domyślnym kontenerze usługi Azure App Service](functions-create-first-azure-function-azure-cli-linux.md). Ta funkcja jest obecnie w wersji zapoznawczej i wymaga [środowiska uruchomieniowego usługi Functions 2.0](functions-versions.md), które jest również dostępne w wersji zapoznawczej.

W tym samouczku dowiesz się, jak wdrożyć aplikację funkcji jako obraz niestandardowy platformy Docker. Ten wzorzec jest przydatny, gdy musisz dostosować wbudowany obraz kontenera usługi App Service. Użycie obrazu niestandardowego może być przydatne w przypadku, kiedy funkcje wymagają konkretnej wersji języka lub konkretnej zależności bądź konfiguracji, które nie są dostarczone w obrazie wbudowanym.

Ten samouczek zawiera instrukcje użycia usługi Azure Functions w celu utworzenia i wypchnięcia obrazu niestandardowego do usługi Docker Hub. Tego obrazu można następnie użyć jako źródła wdrażania aplikacji funkcji działającej w systemie Linux. Kompilowanie i wypychanie obrazu jest realizowane na platformie Docker. Interfejs wiersza polecenia platformy Azure umożliwia tworzenie aplikacji funkcji i wdrażanie obrazu z usługi Docker Hub. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Kompilowanie obrazu niestandardowego na platformie Docker.
> * Publikowanie obrazu niestandardowego w rejestrze kontenerów. 
> * Tworzenie konta usługi Azure Storage. 
> * Tworzenie planu usługi App Service dla systemu Linux. 
> * Wdrażanie aplikacji funkcji z usługi Docker Hub.
> * Dodawanie ustawień aplikacji do aplikacji funkcji. 

Poniższe kroki można wykonać na komputerze Mac, w systemie Windows lub w systemie Linux.  

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* [Usługa Git](https://git-scm.com/downloads)
* Aktywna [subskrypcja platformy Azure](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Docker](https://docs.docker.com/install/)
* [Konto usługi Docker Hub](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Pobierz przykład

W oknie terminalu uruchom następujące polecenie, aby sklonować repozytorium przykładowej aplikacji na maszynę lokalną, a następnie przejść do katalogu zawierającego przykładowy kod.

```bash
git clone https://github.com/Azure-Samples/functions-linux-custom-image.git --config core.autocrlf=input
cd functions-linux-custom-image
```

## <a name="build-the-image-from-the-docker-file"></a>Tworzenie obrazu na podstawie pliku platformy Docker

W tym repozytorium Git zapoznaj się z zawartością pliku _Dockerfile_. W tym pliku opisano środowisko wymagane do uruchomienia aplikacji funkcji w systemie Linux. 

```docker
# Base the image on the built-in Azure Functions Linux image.
FROM microsoft/azure-functions-runtime:2.0.0-jessie
ENV AzureWebJobsScriptRoot=/home/site/wwwroot

# Add files from this repo to the root site folder.
COPY . /home/site/wwwroot 
```
>[!NOTE]
> W przypadku hostowania obrazu w prywatnym rejestrze kontenerów należy dodać ustawienia połączenia do aplikacji funkcji, korzystając ze zmiennych **ENV** w pliku Dockerfile. Ponieważ w tym samouczku nie można zagwarantować, że użytkownik skorzysta z prywatnego rejestru, ustawienia połączenia są [dodawane po wdrożeniu przy użyciu interfejsu wiersza polecenia platformy Azure](#configure-the-function-app), co jest zgodne z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń.   

### <a name="run-the-build-command"></a>Uruchamianie polecenia kompilowania
Aby skompilować obraz platformy Docker, uruchom polecenie `docker build` i podaj nazwę `mydockerimage` oraz tag `v1.0.0`. Zastąp ciąg `<docker-id>` identyfikatorem konta usługi Docker Hub.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Polecenie spowoduje wygenerowanie danych wyjściowych podobnych do następujących:

```bash
Sending build context to Docker daemon  169.5kB
Step 1/3 : FROM microsoft/azure-functions-runtime:v2.0.0-jessie
v2.0.0-jessie: Pulling from microsoft/azure-functions-runtime
b178b12f7913: Pull complete
2d9ce077a781: Pull complete
4775d4ba55c8: Pull complete
Digest: sha256:073f45fc167b3b5c6642ef4b3c99064430d6b17507095...
Status: Downloaded newer image for microsoft/azure-functions-runtime:v2.0.0-jessie
 ---> 217799efa500
Step 2/3 : ENV AzureWebJobsScriptRoot /home/site/wwwroot
 ---> Running in 528fa2077d17
 ---> 7cc6323b8ae0
Removing intermediate container 528fa2077d17
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5bdac9878423
Successfully built 5bdac9878423
Successfully tagged ggailey777/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Lokalne testowanie obrazu
Sprawdź, czy skompilowany obraz działa, uruchamiając obraz platformy Docker w kontenerze lokalnym. Uruchom polecenie [docker run](https://docs.docker.com/engine/reference/commandline/run/) i przekaż do niego nazwę oraz tag obrazu. Pamiętaj o określeniu portu przy użyciu argumentu `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Po uruchomieniu obrazu niestandardowego w lokalnym kontenerze Docker sprawdź, czy aplikacja funkcji i kontener działają prawidłowo, przechodząc do adresu <http://localhost:8080>.

![Lokalne testowanie aplikacji funkcji](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

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
fd9e998161c9: Mounted from microsoft/azure-functions-runtime
e7796c35add2: Mounted from microsoft/azure-functions-runtime
ae9a05b85848: Mounted from microsoft/azure-functions-runtime
45c86e20670d: Mounted from microsoft/azure-functions-runtime
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 2422
```
Teraz możesz użyć tego obrazu jako źródła wdrażania nowej aplikacji funkcji na platformie Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, na potrzeby tego tematu jest wymagany interfejs wiersza polecenia platformy Azure w wersji 2.0.21 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Tworzenie planu usługi App Service dla systemu Linux

Hosting systemu Linux dla usługi Functions nie jest obecnie obsługiwany w ramach planów zużycia. Należy działać w oparciu o plan usługi App Service dla systemu Linux. Aby dowiedzieć się więcej o hostingu, zobacz [Porównanie planów hostingu usługi Azure Functions](functions-scale.md). 

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

Parametr _deployment-container-image-name_ wskazuje obraz hostowany w usłudze Docker Hub, który zostanie użyty do utworzenia aplikacji funkcji. 


## <a name="configure-the-function-app"></a>Konfigurowanie aplikacji funkcji

Do łączenia się z domyślnym kontem magazynu funkcja wymaga parametrów połączenia. W przypadku publikowania obrazu niestandardowego w prywatnym koncie kontenera należy ustawić te parametry połączenia jako zmienne środowiskowe w pliku Dockerfile przy użyciu [instrukcji ENV](https://docs.docker.com/engine/reference/builder/#env) lub podobnej. 

W tym przypadku `<storage_account>` jest nazwą utworzonego konta magazynu. Parametry połączenia można uzyskać za pomocą polecenia [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Dodaj te ustawienia aplikacji w aplikacji funkcji za pomocą polecenia [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

Teraz można przetestować funkcje działające w systemie Linux na platformie Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Kompilowanie obrazu niestandardowego na platformie Docker.
> * Publikowanie obrazu niestandardowego w rejestrze kontenerów. 
> * Tworzenie konta usługi Azure Storage. 
> * Tworzenie planu usługi App Service dla systemu Linux. 
> * Wdrażanie aplikacji funkcji z usługi Docker Hub.
> * Dodawanie ustawień aplikacji do aplikacji funkcji.

Dowiedz się, jak włączyć funkcję ciągłej integracji wbudowaną na podstawowej platformie App Service. Aplikację funkcji możesz skonfigurować tak, aby po zaktualizowaniu obrazu w usłudze Docker Hub kontener był wdrażany ponownie.

> [!div class="nextstepaction"] 
> [Ciągłe wdrażanie za pomocą usługi Web App for Containers](../app-service/containers/app-service-linux-ci-cd.md)
