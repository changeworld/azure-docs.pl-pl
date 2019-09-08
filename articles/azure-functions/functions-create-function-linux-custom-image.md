---
title: Tworzenie Azure Functions w systemie Linux przy użyciu obrazu niestandardowego
description: Informacje o sposobie tworzenia usługi Azure Functions działającej w niestandardowym obrazie systemu Linux.
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: 1865b1b96b5b8794f1518d639825ccd2f1dcd090
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773137"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Tworzenie funkcji w systemie Linux przy użyciu obrazu niestandardowego

Usługa Azure Functions umożliwia hostowanie funkcji w systemie Linux w utworzonym przez użytkownika kontenerze niestandardowym. Można również [hostować w domyślnym kontenerze usługi Azure App Service](functions-create-first-azure-function-azure-cli-linux.md). Ta funkcja wymaga [środowiska uruchomieniowego programu Functions 2. x](functions-versions.md).

Z tego samouczka dowiesz się, jak wdrożyć funkcje na platformie Azure jako obraz niestandardowy platformy Docker. Ten wzorzec jest przydatny, gdy trzeba dostosować wbudowany obraz kontenera. Użycie obrazu niestandardowego może być przydatne w przypadku, kiedy funkcje wymagają konkretnej wersji języka lub konkretnej zależności bądź konfiguracji, które nie są dostarczone w obrazie wbudowanym. Obsługiwane obrazy podstawowe dla Azure Functions są dostępne w [repozytorium Azure Functions obrazów podstawowych](https://hub.docker.com/_/microsoft-azure-functions-base). 

Ten samouczek przedstawia sposób użycia narzędzi Azure Functions Core Tools do tworzenia funkcji w niestandardowym obrazie systemu Linux. Ten obraz jest publikowany na platformie Azure w aplikacji funkcji, która została utworzona przy użyciu interfejsu wiersza polecenia platformy Azure. Później należy zaktualizować funkcję w celu nawiązania połączenia z usługą Azure queue storage. Można również włączyć.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji funkcji i pliku Dockerfile przy użyciu podstawowych narzędzi.
> * Kompilowanie obrazu niestandardowego na platformie Docker.
> * Publikowanie obrazu niestandardowego w rejestrze kontenerów.
> * Tworzenie konta usługi Azure Storage.
> * Utwórz plan hostingu w warstwie Premium.
> * Wdrażanie aplikacji funkcji z usługi Docker Hub.
> * Dodawanie ustawień aplikacji do aplikacji funkcji.
> * Włącz ciągłe wdrażanie.
> * Dodaj monitorowanie Application Insights.

Poniższe kroki można wykonać na komputerze Mac, w systemie Windows lub w systemie Linux. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed uruchomieniem tego przykładu należy dysponować następującymi elementami:

* Zainstaluj narzędzia [Azure Core Tools w wersji 2.x](functions-run-local.md#v2).

* Zainstaluj [interfejs wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz.  
Możesz również użyć usługi [Azure Cloud Shell](https://shell.azure.com/bash).

* Aktywna subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Tworzenie lokalnego projektu aplikacji funkcji

Uruchom następujące polecenie w wierszu polecenia, aby utworzyć projekt aplikacji funkcji w folderze `MyFunctionProj` bieżącego katalogu lokalnego. W przypadku projektu w języku Python [należy uruchomić program w środowisku wirtualnym](functions-create-first-function-python.md#create-and-activate-a-virtual-environment-optional).

```bash
func init MyFunctionProj --docker
```

Jeśli dołączysz opcję `--docker`, zostanie wygenerowany plik dockerfile dla projektu. Ten plik służy do tworzenia niestandardowego kontenera, w którym chcesz uruchomić projekt. Użyty obraz podstawowy zależy od wybranego języka środowiska uruchomieniowego procesów roboczych.  

Po wyświetleniu monitu wybierz środowisko uruchomieniowe procesów roboczych spośród następujących języków:

* `dotnet`: tworzy projekt biblioteki klas .NET Core (. csproj).
* `node`: tworzy projekt w języku JavaScript.
* `python`: tworzy projekt w języku Python.  

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

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-the-image-from-the-docker-file"></a>Tworzenie obrazu na podstawie pliku platformy Docker

Przyjrzyj się plikowi _Dockerfile_ w folderze głównym projektu. W tym pliku opisano środowisko wymagane do uruchomienia aplikacji funkcji w systemie Linux. Poniższy przykład to plik Dockerfile, który tworzy kontener uruchamiający aplikację funkcji w środowisku uruchomieniowym procesów roboczych języka JavaScript (Node.js): 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> Pełną listę obsługiwanych obrazów podstawowych dla Azure Functions można znaleźć na [stronie Azure Functions Image Base](https://hub.docker.com/_/microsoft-azure-functions-base).

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

> [!NOTE]
> W tym momencie podczas próby wywołania określonej funkcji HTTP pojawia się odpowiedź na błąd HTTP 401. Wynika to z faktu, że funkcja działa w lokalnym kontenerze, tak jak w przypadku platformy Azure, co oznacza, że klucz funkcji jest wymagany. Ponieważ kontener nie został jeszcze opublikowany w aplikacji funkcji, nie jest dostępny żaden klucz funkcji. Zobaczysz później, że w przypadku korzystania z podstawowych narzędzi do publikowania kontenera zostaną wyświetlone klucze funkcji. Jeśli chcesz przetestować funkcję uruchomioną w kontenerze lokalnym, możesz zmienić [klucz autoryzacji](functions-bindings-http-webhook.md#authorization-keys) na `anonymous`. 

Po zweryfikowaniu aplikacji funkcji w kontenerze zatrzymaj wykonywanie. Teraz możesz wypchnąć obraz niestandardowy do swojego konta w usłudze Docker Hub.

## <a name="push-the-custom-image-to-docker-hub"></a>Wypychanie obrazu niestandardowego do usługi Docker Hub

Rejestr to aplikacja hostująca obrazy i udostępniająca usługi obrazów oraz kontenerów. Aby udostępnić obraz, należy wypchnąć go do rejestru. Usługa Docker Hub jest rejestrem obrazów platformy Docker, który umożliwia hostowanie własnych repozytoriów — publicznych lub prywatnych.

Przed wypchnięciem obrazu należy najpierw zalogować się w usłudze Docker Hub przy użyciu polecenia [docker login](https://docs.docker.com/engine/reference/commandline/login/). Zastąp ciąg `<docker-id>` nazwą swojego konta i wpisz swoje hasło w konsoli, gdy zostanie wyświetlony odpowiedni monit. Inne opcje dotyczące hasła usługi Docker Hub opisano w [dokumentacji polecenia docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

Komunikat "Logowanie pomyślne" potwierdza, że użytkownik jest zalogowany. Po zalogowaniu się można wypchnąć obraz do usługi Docker Hub przy użyciu polecenia [docker push](https://docs.docker.com/engine/reference/commandline/push/).

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

## <a name="create-a-premium-plan"></a>Tworzenie planu Premium

Hosting systemu Linux dla kontenerów funkcji niestandardowych obsługiwanych w ramach [dedykowanych planów (App Service)](functions-scale.md#app-service-plan) i [planów Premium](functions-premium-plan.md#features). W tym samouczku jest używany plan Premium, który można skalować zgodnie z wymaganiami. Aby dowiedzieć się więcej o hostingu, zobacz [Porównanie planów hostingu usługi Azure Functions](functions-scale.md).

Poniższy przykład tworzy plan Premium o nazwie `myPremiumPlan` w warstwie cenowej **elastyczna 1** (`--sku EP1`), w regionie zachodnie stany USA (`-location WestUS`) i w kontenerze systemu Linux (`--is-linux`).

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-and-deploy-the-custom-image"></a>Tworzenie i wdrażanie obrazu niestandardowego

Aplikacja funkcji zarządza wykonywaniem funkcji w Twoim planie hostingu. Utwórz aplikację funkcji na podstawie obrazu usługi Docker Hub przy użyciu polecenia [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

W poniższym poleceniu w miejsce symbolu zastępczego `<app_name>` wstaw unikatową nazwę aplikacji funkcji, a w miejsce symbolu zastępczego `<storage_name>` wstaw nazwę konta magazynu. Nazwa `<app_name>` jest używana jako domyślna domena DNS aplikacji funkcji, więc nazwa ta musi być unikatowa wśród wszystkich aplikacji na platformie Azure. Tak samo jak wcześniej `<docker-id>` to nazwa konta platformy Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

Parametr _deployment-container-image-name_ wskazuje obraz hostowany w usłudze Docker Hub, który zostanie użyty do utworzenia aplikacji funkcji. Użyj polecenia [AZ functionapp config Container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) , aby wyświetlić informacje o obrazie używanym do wdrożenia. Użyj polecenia [AZ functionapp config Container Set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) do wdrożenia z innego obrazu.

## <a name="configure-the-function-app"></a>Konfigurowanie aplikacji funkcji

Do łączenia się z domyślnym kontem magazynu funkcja wymaga parametrów połączenia. W przypadku publikowania obrazu niestandardowego na koncie kontenera prywatnego należy zamiast tego ustawić te ustawienia aplikacji jako zmienne środowiskowe w pliku dockerfile przy użyciu [instrukcji ENV](https://docs.docker.com/engine/reference/builder/#env)lub podobnego.

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

## <a name="enable-continuous-deployment"></a>Włącz ciągłe wdrażanie

Jedną z zalet korzystania z kontenerów jest możliwość automatycznego wdrażania aktualizacji w przypadku aktualizowania kontenerów w rejestrze. Włącz ciągłe wdrażanie za pomocą polecenia [AZ functionapp Deployment Container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) .

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

To polecenie zwraca adres URL elementu webhook wdrożenia po włączeniu ciągłego wdrażania. Możesz również użyć polecenia [AZ functionapp Deployment Container show-CD-URL](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) , aby zwrócić ten adres URL. 

Skopiuj adres URL wdrożenia i przejdź do repozytorium DockerHub, wybierz kartę **webhook** , wpisz **nazwę elementu** webhook dla elementu WEBHOOK, wklej adres URL w **adresie URL elementu webhook**, a następnie wybierz znak plus ( **+** ).

![Dodawanie elementu webhook w repozytorium DockerHub](media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

Po ustawieniu elementu webhook wszystkie aktualizacje połączonego obrazu w programie DockerHub spowodują pobranie i zainstalowanie najnowszego obrazu.

## <a name="enable-application-insights"></a>Włącz usługę Application Insights

Zalecanym sposobem monitorowania wykonywania funkcji jest zintegrowanie aplikacji funkcji z usługą Azure Application Insights. Po utworzeniu aplikacji funkcji w Azure Portal Ta integracja jest domyślnie wykonywana. Jednak podczas tworzenia aplikacji funkcji przy użyciu interfejsu wiersza polecenia platformy Azure integracja w aplikacji funkcji na platformie Azure nie jest wykonywana.

Aby włączyć Application Insights aplikacji funkcji:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Aby dowiedzieć się więcej, zobacz [Monitor Azure Functions](functions-monitoring.md).

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji funkcji i pliku Dockerfile przy użyciu podstawowych narzędzi.
> * Kompilowanie obrazu niestandardowego na platformie Docker.
> * Publikowanie obrazu niestandardowego w rejestrze kontenerów.
> * Tworzenie konta usługi Azure Storage.
> * Utwórz plan systemu Linux Premium.
> * Wdrażanie aplikacji funkcji z usługi Docker Hub.
> * Dodawanie ustawień aplikacji do aplikacji funkcji.
> * Włącz ciągłe wdrażanie.
> * Dodaj monitorowanie Application Insights.

> [!div class="nextstepaction"] 
> [Dowiedz się więcej o opcjach wdrażania funkcji na platformie Azure](functions-deployment-technologies.md)
