---
title: Create Azure Functions on Linux using a custom image
description: Informacje o sposobie tworzenia usługi Azure Functions działającej w niestandardowym obrazie systemu Linux.
ms.date: 09/27/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 5365e788f5f277bdcfdd1add56b401976dc39c5f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230756"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Create a function on Linux using a custom image

Usługa Azure Functions umożliwia hostowanie funkcji w systemie Linux w utworzonym przez użytkownika kontenerze niestandardowym. Można również [hostować w domyślnym kontenerze usługi Azure App Service](functions-create-first-azure-function-azure-cli-linux.md). This functionality requires [the Functions 2.x runtime](functions-versions.md).

Z tego samouczka dowiesz się, jak wdrożyć funkcje na platformie Azure jako obraz niestandardowy platformy Docker. This pattern is useful when you need to customize the built-in container image. Użycie obrazu niestandardowego może być przydatne w przypadku, kiedy funkcje wymagają konkretnej wersji języka lub konkretnej zależności bądź konfiguracji, które nie są dostarczone w obrazie wbudowanym. Supported base images for Azure Functions are found in the [Azure Functions base images repo](https://hub.docker.com/_/microsoft-azure-functions-base). 

Ten samouczek przedstawia sposób użycia narzędzi Azure Functions Core Tools do tworzenia funkcji w niestandardowym obrazie systemu Linux. Ten obraz jest publikowany na platformie Azure w aplikacji funkcji, która została utworzona przy użyciu interfejsu wiersza polecenia platformy Azure. Later, you update your function to connect to Azure Queue storage. You also enable.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji funkcji i pliku Dockerfile przy użyciu podstawowych narzędzi.
> * Kompilowanie obrazu niestandardowego na platformie Docker.
> * Publikowanie obrazu niestandardowego w rejestrze kontenerów.
> * Tworzenie konta usługi Azure Storage.
> * Create a Premium hosting plan.
> * Wdrażanie aplikacji funkcji z usługi Docker Hub.
> * Dodawanie ustawień aplikacji do aplikacji funkcji.
> * Enable continuous deployment.
> * Enable SSH connections to the container.
> * Add a Queue storage output binding. 
> * Add Application Insights monitoring.

Poniższe kroki można wykonać na komputerze Mac, w systemie Windows lub w systemie Linux. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed uruchomieniem tego przykładu należy dysponować następującymi elementami:

* Zainstaluj narzędzia [Azure Core Tools w wersji 2.x](functions-run-local.md#v2).

* Zainstaluj [interfejs wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz.  
Możesz również użyć usługi [Azure Cloud Shell](https://shell.azure.com/bash).

* Aktywna subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="create-the-local-project"></a>Create the local project

Uruchom następujące polecenie w wierszu polecenia, aby utworzyć projekt aplikacji funkcji w folderze `MyFunctionProj` bieżącego katalogu lokalnego. For a Python project, you [must be running in a virtual environment](functions-create-first-function-python.md#create-and-activate-a-virtual-environment).

```bash
func init MyFunctionProj --docker
```

Jeśli dołączysz opcję `--docker`, zostanie wygenerowany plik dockerfile dla projektu. Ten plik służy do tworzenia niestandardowego kontenera, w którym chcesz uruchomić projekt. Użyty obraz podstawowy zależy od wybranego języka środowiska uruchomieniowego procesów roboczych.  

Po wyświetleniu monitu wybierz środowisko uruchomieniowe procesów roboczych spośród następujących języków:

* `dotnet`: creates a .NET Core class library project (.csproj).
* `node`: tworzy projekt w języku JavaScript.
* `python`: tworzy projekt w języku Python.  

Użyj następującego polecenia, aby przejść do nowego folderu projektu `MyFunctionProj`.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-from-the-docker-file"></a>Build from the Docker file

Przyjrzyj się plikowi _Dockerfile_ w folderze głównym projektu. W tym pliku opisano środowisko wymagane do uruchomienia aplikacji funkcji w systemie Linux. Poniższy przykład to plik Dockerfile, który tworzy kontener uruchamiający aplikację funkcji w środowisku uruchomieniowym procesów roboczych języka JavaScript (Node.js): 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> The complete list of supported base images for Azure Functions can be found in the [Azure Functions base image page](https://hub.docker.com/_/microsoft-azure-functions-base).

### <a name="run-the-build-command"></a>Uruchamianie polecenia `build`

W folderze głównym uruchom polecenie [docker build](https://docs.docker.com/engine/reference/commandline/build/) i podaj nazwę, element `mydockerimage` oraz tag `v1.0.0`. Zastąp ciąg `<docker-id>` identyfikatorem konta usługi Docker Hub. To polecenie powoduje skompilowanie obrazu platformy Docker dla kontenera.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

When the command completes, you can run the new container locally.

### <a name="run-the-image-locally"></a>Run the image locally
Sprawdź, czy skompilowany obraz działa, uruchamiając obraz platformy Docker w kontenerze lokalnym. Uruchom polecenie [docker run](https://docs.docker.com/engine/reference/commandline/run/) i przekaż do niego nazwę oraz tag obrazu. Pamiętaj o określeniu portu przy użyciu argumentu `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Po uruchomieniu obrazu niestandardowego w lokalnym kontenerze Docker sprawdź, czy aplikacja funkcji i kontener działają prawidłowo, przechodząc do adresu <http://localhost:8080>.

![Run the function app locally.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> At this point, when you try to call your specific HTTP function, you get an HTTP 401 error response. This is because your function runs in the local container as it would in Azure, which means that the function key is required. Because the container hasn't yet been published to a function app, there is no function key available. You'll see later that when you use Core Tools to publish your container, the function keys are shown to you. If you want to test your function running in the local container, you can change the [authorization key](functions-bindings-http-webhook.md#authorization-keys) to `anonymous`. 

Po zweryfikowaniu aplikacji funkcji w kontenerze zatrzymaj wykonywanie. Teraz możesz wypchnąć obraz niestandardowy do swojego konta w usłudze Docker Hub.

## <a name="push-to-docker-hub"></a>Push to Docker Hub

Rejestr to aplikacja hostująca obrazy i udostępniająca usługi obrazów oraz kontenerów. Aby udostępnić obraz, należy wypchnąć go do rejestru. Usługa Docker Hub jest rejestrem obrazów platformy Docker, który umożliwia hostowanie własnych repozytoriów — publicznych lub prywatnych.

Przed wypchnięciem obrazu należy najpierw zalogować się w usłudze Docker Hub przy użyciu polecenia [docker login](https://docs.docker.com/engine/reference/commandline/login/). Zastąp ciąg `<docker-id>` nazwą swojego konta i wpisz swoje hasło w konsoli, gdy zostanie wyświetlony odpowiedni monit. Inne opcje dotyczące hasła usługi Docker Hub opisano w [dokumentacji polecenia docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

A "login succeeded" message confirms that you're logged in. Po zalogowaniu się można wypchnąć obraz do usługi Docker Hub przy użyciu polecenia [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

After the push succeeds, you can use the image as the deployment source for a new function app in Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>Create a Premium plan

Linux hosting for custom Functions containers supported on [Dedicated (App Service) plans](functions-scale.md#app-service-plan) and [Premium plans](functions-premium-plan.md#features). This tutorial uses a Premium plan, which can scale as needed. Aby dowiedzieć się więcej o hostingu, zobacz [Porównanie planów hostingu usługi Azure Functions](functions-scale.md).

The following example creates a Premium plan named `myPremiumPlan` in the **Elastic Premium 1** pricing tier (`--sku EP1`), in the West US region (`-location WestUS`), and in a Linux container (`--is-linux`).

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-an-app-from-the-image"></a>Create an app from the image

The function app manages the execution of your functions in your hosting plan. Utwórz aplikację funkcji na podstawie obrazu usługi Docker Hub przy użyciu polecenia [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

W poniższym poleceniu w miejsce symbolu zastępczego `<app_name>` wstaw unikatową nazwę aplikacji funkcji, a w miejsce symbolu zastępczego `<storage_name>` wstaw nazwę konta magazynu. Nazwa `<app_name>` jest używana jako domyślna domena DNS aplikacji funkcji, więc nazwa ta musi być unikatowa wśród wszystkich aplikacji na platformie Azure. Tak samo jak wcześniej `<docker-id>` to nazwa konta platformy Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

Parametr _deployment-container-image-name_ wskazuje obraz hostowany w usłudze Docker Hub, który zostanie użyty do utworzenia aplikacji funkcji. Use the [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) command to view information about the image used for deployment. Use the [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) command to deploy from a different image.

## <a name="configure-the-function-app"></a>Konfigurowanie aplikacji funkcji

Do łączenia się z domyślnym kontem magazynu funkcja wymaga parametrów połączenia. When you're publishing your custom image to a private container account, you should instead set these application settings as environment variables in the Dockerfile using the [ENV instruction](https://docs.docker.com/engine/reference/builder/#env), or something similar.

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

## <a name="verify-your-functions"></a>Verify your functions

<!-- we should replace this with a CLI or API-based approach, when we get something better than REST -->

The HTTP-triggered function you created requires a [function key](functions-bindings-http-webhook.md#authorization-keys) when calling the endpoint. At this time, the easiest way to get your function URL, including the key, is from the [Azure portal]. 

> [!TIP]
> You can also obtain your function keys by using the [Key management APIs](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), which requires you to present a [bearer token for authentication](/cli/azure/account#az-account-get-access-token).

Locate your new function app in the [Azure portal] by typing your function app name in the **Search** box at the top of the page and selecting the **App Service** resource.

Select the **MyHttpTrigger** function, select **</> Get function URL** > **default (Function key)**  > **Copy**.

![Kopiowanie adresu URL funkcji z witryny Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)

In this URL, the function key is the `code` query parameter. 

> [!NOTE]  
> Because your function app is deployed as a container, you can't make changes to your function code in the portal. You must instead update the project in local container and republish it to Azure.

Wklej adres URL funkcji do paska adresu przeglądarki. Dodaj wartość ciągu zapytania `&name=<yourname>` na końcu tego adresu URL, a następnie naciśnij klawisz `Enter` na klawiaturze, aby wykonać żądanie. W przeglądarce powinna zostać wyświetlona odpowiedź zwrócona przez funkcję.

Poniższy przykład przedstawia odpowiedź w przeglądarce:

![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

Adres URL żądania zawiera klucz, który domyślnie jest wymagany do uzyskania dostępu do funkcji za pośrednictwem protokołu HTTP. 

## <a name="enable-continuous-deployment"></a>Enable continuous deployment

One of the benefits of using containers is support for continuous deployment. Functions lets you automatically deploy updates when your container is updated in the registry. Enable continuous deployment with the [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) command.

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

This command returns the deployment webhook URL after continuous deployment is enabled. You can also use the [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) command to return this URL. 

Copy the deployment URL and browse to your DockerHub repo, choose the **Webhooks** tab, type a **Webhook name** for the webhook, paste your URL in **Webhook URL**, and then choose the plus sign ( **+** ).

![Add the webhook in your DockerHub repo](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

With the webhook set, any updates to the linked image in DockerHub result in the function app downloading and installing the latest image.

## <a name="enable-ssh-connections"></a>Enable SSH connections

Protokół SSH umożliwia bezpieczną komunikację między kontenerem i klientem. With SSH enabled, you can connect to your container using App Service Advanced Tools (Kudu). To make it easy to connect to your container using SSH, Functions provide a base image that has SSH already enabled. 

### <a name="change-the-base-image"></a>Change the base image

In your dockerfile, append the string `-appservice` to the base image in your `FROM` instruction, which for a JavaScript project looks like the following.

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
```

The differences in the two base images enable SSH connections into your container. These differences are detailed in [this App Services tutorial](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

### <a name="rebuild-and-redeploy-the-image"></a>Rebuild and redeploy the image

In the root folder, run the [docker build](https://docs.docker.com/engine/reference/commandline/build/) command again, as before, replace `<docker-id>` with your Docker Hub account ID. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Push the updated image back to Docker Hub.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

The updated image is redeployed to your function app.

### <a name="connect-to-your-container-in-azure"></a>Connect to your container in Azure

In the browser, navigate to the following Advanced Tools (Kudu) `scm.` endpoint for your function app container, replacing `<app_name>` with the name of your function app.

```
https://<app_name>.scm.azurewebsites.net/
```

Sign in to your Azure account, and then select the **SSH** tab to create an SSH connection into your container.

After the connection is established, run the `top` command to view the currently running processes. 

![Linux top command running in an SSH session.](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-queue-storage"></a>Write to Queue storage

Functions lets you connect Azure services and other resources to functions without having to write your own integration code. These *bindings*, which represent both input and output, are declared within the function definition. Data from bindings is provided to the function as parameters. A *trigger* is a special type of input binding. Although a function has only one trigger, it can have multiple input and output bindings. To learn more, see [Azure Functions triggers and bindings concepts](functions-triggers-bindings.md).

This section shows you how to integrate your function with an Azure Storage queue. The output binding that you add to this function writes data from an HTTP request to a message in the queue.

### <a name="download-the-function-app-settings"></a>Download the function app settings

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

### <a name="enable-extension-bundles"></a>Enable extension bundles

Because you are using a Queue storage output binding, you must have the Storage bindings extension installed before you run the project. 


# <a name="javascript--pythontabnodejspython"></a>[JavaScript / Python](#tab/nodejs+python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

With the exception of HTTP and timer triggers, bindings are implemented as extension packages. Run the following [dotnet add package](/dotnet/core/tools/dotnet-add-package) command in the Terminal window to add the Storage extension package to your project.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

> [!TIP]
> When using Visual Studio, you can also use the NuGet package manager to add this package.

---

Now, you can add a Storage output binding to your project.

### <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

In Functions, each type of binding requires a `direction`, `type`, and a unique `name` to be defined in the function.json file. The way you define these attributes depends on the language of your function app.

# <a name="javascript--pythontabnodejspython"></a>[JavaScript / Python](#tab/nodejs+python)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

### <a name="add-code-that-uses-the-output-binding"></a>Dodawanie kodu korzystającego z powiązania danych wyjściowych

After the binding is defined, you can use the `name` of the binding to access it as an attribute in the function signature. By using an output binding, you don't have to use the Azure Storage SDK code for authentication, getting a queue reference, or writing data. The Functions runtime and queue output binding do those tasks for you.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

### <a name="update-the-hosted-container"></a>Update the hosted container

In the root folder, run the [docker build](https://docs.docker.com/engine/reference/commandline/build/) command again, and this time update the version in the tag to `v1.0.2`. As before, replace `<docker-id>` with your Docker Hub account ID. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Push the updated image back to the repository.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

### <a name="verify-the-updates-in-azure"></a>Verify the updates in Azure

Use the same URL as before from the browser to trigger your function. You should see the same response. However, this time the string that you pass as the `name` parameter is written to the `outqueue` storage queue.

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

Now that you have successfully deployed your custom container to a function app in Azure, consider reading more about the following topics:

+ [Monitoring functions](functions-monitoring.md)
+ [Scale and hosting options](functions-scale.md)
+ [Kubernetes-based serverless hosting](functions-kubernetes-keda.md)

[Azure Portal]: https://portal.azure.com
