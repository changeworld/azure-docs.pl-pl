---
title: Create an HTTP triggered Python function in Azure
description: Dowiedz się, jak utworzyć pierwszą funkcję języka Python na platformie Azure przy użyciu narzędzi Azure Functions Core Tools i interfejsu wiersza polecenia platformy Azure.
ms.date: 11/07/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 18ae1ed000ffe61ce1ea9ff5c18aae98a0ffae65
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227189"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Quickstart: Create an HTTP triggered Python function in Azure

This article shows you how to use command-line tools to create a Python project that runs in Azure Functions. You also create a function that is triggered by an HTTP request. After running locally, you publish your project to run as a [serverless function](functions-scale.md#consumption-plan) in Azure. 

This article is the first of two Python quickstarts for Azure Functions. After you complete this quickstart, you can [add an Azure Storage queue output binding](functions-add-output-binding-storage-queue-python.md) to your function.

There is also a [Visual Studio Code-based version](/azure/python/tutorial-vs-code-serverless-python-01) of this article.

## <a name="prerequisites"></a>Wymagania wstępne

Before you start, you must:

+ Install [Python 3.7.4](https://www.python.org/downloads/). This version of Python is verified with Functions. Python 3.8 and later versions are not yet supported.

+ Install [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.7.1846 or a later version.

+ Install the [Azure CLI](/cli/azure/install-azure-cli) version 2.0.76 or a later version.

+ Have an active Azure subscription.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Tworzenie i aktywowanie środowiska wirtualnego

You should use a Python 3.7 environment to locally develop Python functions. Uruchom następujące polecenia, aby utworzyć i aktywować środowisko wirtualne o nazwie `.venv`.

> [!NOTE]
> If Python didn't install venv on your Linux distribution, you can install it using the following command:
> ```command
> sudo apt-get install python3-venv

### <a name="bash"></a>Bash:

```bash
python -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell or a Windows command prompt:

```powershell
py -m venv .venv
.venv\scripts\activate
```

Now that you activated the virtual environment, run the remaining commands in it. To get out of the virtual environment, run `deactivate`.

## <a name="create-a-local-functions-project"></a>Create a local functions project

A functions project can have multiple functions that all share the same local and hosting configurations.

In the virtual environment, run the following commands:

```console
func init MyFunctionProj --python
cd MyFunctionProj
```

The `func init` command creates a _MyFunctionProj_ folder. The Python project in this folder doesn't yet have any functions. You'll add them next.

## <a name="create-a-function"></a>Tworzenie funkcji

To add a function to your project, run the following command:

```console
func new --name HttpTrigger --template "HTTP trigger"
```

This commands creates a subfolder named _HttpTrigger_, which contains the following files:

* *function.json*: configuration file that defines the function, trigger, and other bindings. Notice that in this file, the value for `scriptFile` points to the file containing the function, and the `bindings` array defines the invocation trigger and bindings.

    Each binding requires a direction, type and a unique name. The HTTP trigger has an input binding of type [`httpTrigger`](functions-bindings-http-webhook.md#trigger) and output binding of type [`http`](functions-bindings-http-webhook.md#output).

* *\_\_init\_\_.py*: script file that is your HTTP triggered function. Notice that this script has a default `main()`. HTTP data from the trigger passes to the function using the `req` named `binding parameter`. The `req`, which is defined in function.json, is an instance of the [azure.functions.HttpRequest class](/python/api/azure-functions/azure.functions.httprequest). 

    The return object, defined as `$return` in *function.json*, is an instance of [azure.functions.HttpResponse class](/python/api/azure-functions/azure.functions.httpresponse). To learn more, see [Azure Functions HTTP triggers and bindings](functions-bindings-http-webhook.md).

Now you can run the new function on your local computer.

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

This command starts the function app using the Azure Functions runtime (func.exe):

```console
func host start
```

You should see the following information written to the output:

```output
Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger    
```

Copy the URL of your `HttpTrigger` function from this output and paste it into your browser's address bar. Dołącz ciąg zapytania `?name=<yourname>` do tego adresu URL i wykonaj żądanie. The following screenshot shows the response to the GET request that the local function returns to the browser:

![Verify locally in the browser](./media/functions-create-first-function-python/function-test-local-browser.png)

Use Ctrl+C to shut down your function app execution.

Now that you have run your function locally, you can deploy your function code to Azure.  
Before you can deploy your app, you'll need to create some Azure resources.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Create a function app in Azure

A function app provides an environment for executing your function code. Umożliwia ona grupowanie funkcji w ramach jednostki logicznej, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi. 

Uruchom następujące polecenie. Replace `<APP_NAME>` with a unique function app name. Replace `<STORAGE_NAME>` with a storage account name. `<APP_NAME>` jest również domyślną domeną DNS aplikacji funkcji. Ta nazwa musi być unikatowa dla wszystkich aplikacji na platformie Azure.

> [!NOTE]
> You can't host Linux and Windows apps in the same resource group. If you have an existing resource group named `myResourceGroup` with a Windows function app or web app, you must use a different resource group.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python --runtime-version 3.7 \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

The preceding command creates a function app running Python 3.7.4. It also provisions an associated Azure Application Insights instance in the same resource group. You can use this instance to monitor your function app and view logs. 

You're now ready to publish your local functions project to the function app in Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Wdrażanie projektu aplikacji funkcji na platformie Azure

After you create the function app in Azure, you can use the [func azure functionapp publish](functions-run-local.md#project-file-deployment) Core Tools command to deploy your project code to Azure. In this example, replace `<APP_NAME>` with the name of your app.

```console
func azure functionapp publish <APP_NAME> --build remote
```

The `--build remote` option builds your Python project remotely in Azure from the files in the deployment package, which is recommended. 

You'll see output similar to the following message. It's truncated here so you can read it better:

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

You can copy the `Invoke url` value for your `HttpTrigger` and use it to verify your function in Azure. The URL contains a `code` query string value that is your function key, which makes it difficult for others to call your HTTP trigger endpoint in Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> To view near real-time logs for a published Python app, use the [Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs).

## <a name="next-steps"></a>Następne kroki

You've created a Python functions project with an HTTP triggered function, run it on your local machine, and deployed it to Azure. Now, extend your function by...

> [!div class="nextstepaction"]
> [Adding an Azure Storage queue output binding](functions-add-output-binding-storage-queue-python.md)
