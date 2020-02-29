---
title: Tworzenie Azure Functions w systemie Linux przy użyciu obrazu niestandardowego
description: Informacje o sposobie tworzenia usługi Azure Functions działającej w niestandardowym obrazie systemu Linux.
ms.date: 01/15/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 29b0719670529a486222ef5fad21dd803ea6f195
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190810"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Tworzenie funkcji w systemie Linux przy użyciu kontenera niestandardowego

W tym samouczku przedstawiono tworzenie i wdrażanie kodu w języku Python w celu Azure Functions jako niestandardowego kontenera platformy Docker przy użyciu podstawowego obrazu systemu Linux. Obraz niestandardowy jest zazwyczaj używany, gdy funkcje wymagają określonej wersji językowej lub mają określoną zależność lub konfigurację, która nie jest dostarczana przez wbudowany obraz.

Można również użyć domyślnego kontenera Azure App Service, zgodnie z opisem w temacie [Tworzenie pierwszej funkcji hostowanej w systemie Linux](functions-create-first-azure-function-azure-cli-linux.md). Obsługiwane obrazy podstawowe dla Azure Functions są dostępne w [repozytorium Azure Functions obrazów podstawowych](https://hub.docker.com/_/microsoft-azure-functions-base).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz aplikację funkcji i pliku dockerfile przy użyciu Azure Functions Core Tools.
> * Kompilowanie obrazu niestandardowego na platformie Docker.
> * Publikowanie obrazu niestandardowego w rejestrze kontenerów.
> * Tworzenie zasobów pomocniczych na platformie Azure dla aplikacji funkcji
> * Wdrażanie aplikacji funkcji z usługi Docker Hub.
> * Dodawanie ustawień aplikacji do aplikacji funkcji.
> * Włącz ciągłe wdrażanie.
> * Włącz połączenia SSH z kontenerem.
> * Dodawanie powiązania wyjściowego magazynu kolejki. 

Ten samouczek można wykonać na dowolnym komputerze z systemem Windows, Mac OS lub Linux. Ukończenie tego samouczka spowoduje naliczenie kosztów z kilku dolarów amerykańskich na koncie platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](./functions-run-local.md#v2) w wersji 2.7.1846 lub nowszej
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.0.77 lub nowszej
- [Środowisko uruchomieniowe Azure Functions 2. x](functions-versions.md)
- Następujące składniki środowiska uruchomieniowego języka:
    ::: zone pivot="programming-language-csharp"
    - [.NET Core 2.2. x lub nowszy](https://dotnet.microsoft.com/download)
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - [Node.js](https://nodejs.org/en/download/)
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - [PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7)
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - [Python 3,6-64 bit](https://www.python.org/downloads/release/python-3610/) lub [Python 3,7-64 bit](https://www.python.org/downloads/release/python-376/)
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - [Node.js](https://nodejs.org/en/download/)
    - [TypeScript](http://www.typescriptlang.org/#download-links)
    ::: zone-end
- [Docker](https://docs.docker.com/install/)
- [Identyfikator platformy Docker](https://hub.docker.com/signup)

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

1. W terminalu lub oknie poleceń Uruchom `func --version`, aby sprawdzić, czy Azure Functions Core Tools jest w wersji 2.7.1846 lub nowszej.
1. Uruchom `az --version`, aby sprawdzić, czy wersja interfejsu wiersza polecenia platformy Azure to 2.0.76 lub nowszego.
1. Uruchom `az login`, aby zalogować się do platformy Azure i zweryfikować aktywną subskrypcję.
1. Uruchom `docker login`, aby zalogować się do platformy Docker. To polecenie kończy się niepowodzeniem, jeśli platforma Docker nie jest uruchomiona, w tym przypadku Uruchom platformę Docker i spróbuj ponownie wykonać polecenie.

## <a name="create-and-test-the-local-functions-project"></a>Tworzenie i testowanie projektu funkcji lokalnych

1. W terminalu lub wierszu polecenia Utwórz folder dla tego samouczka w odpowiedniej lokalizacji, a następnie przejdź do tego folderu.

1. Postępuj zgodnie z instrukcjami dotyczącymi [tworzenia i aktywowania środowiska wirtualnego](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv) w celu utworzenia środowiska wirtualnego do użycia z tym samouczkiem.

1. Uruchom następujące polecenie dla wybranego języka, aby utworzyć projekt aplikacji funkcji w folderze o nazwie `LocalFunctionsProject`. Opcja `--docker` generuje `Dockerfile` dla projektu, który definiuje odpowiedni kontener niestandardowy do użycia z Azure Functions i wybranym środowiskiem uruchomieniowym.

    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionsProject --worker-runtime dotnet --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language javascript --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionsProject --worker-runtime powershell --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionsProject --worker-runtime python --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language typescript --docker
    ```
    ::: zone-end
    
1. Przejdź do folderu projektu:

    ```
    cd LocalFunctionsProject
    ```
    
1. Dodaj funkcję do projektu za pomocą następującego polecenia, gdzie argument `--name` jest unikatową nazwą funkcji, a argument `--template` Określa wyzwalacz funkcji. `func new` utworzyć podfolder pasujący do nazwy funkcji, która zawiera plik kodu odpowiedni dla wybranego języka projektu i plik konfiguracji o nazwie *Function. JSON*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

1. Aby przetestować funkcję lokalnie, Uruchom lokalny host środowiska uruchomieniowego Azure Functions w folderze *LocalFunctionsProject* :
   
    ::: zone pivot="programming-language-csharp"
    ```
    func start --build
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func start
    ```
    ::: zone-end    

    ::: zone pivot="programming-language-typescript"
    ```
    npm install
    ```

    ```
    npm start
    ```
    ::: zone-end

1. Gdy w danych wyjściowych zostanie wyświetlony punkt końcowy `HttpExample`, przejdź do `http://localhost:7071/api/HttpExample?name=Functions`. W przeglądarce powinien zostać wyświetlony komunikat podobny do "Hello, Functions" (różni się nieco w zależności od wybranego języka programowania).

1. Użyj **klawiszy Ctrl**-**C** , aby zatrzymać hosta.

## <a name="build-the-container-image-and-test-locally"></a>Kompiluj obraz kontenera i przetestuj go lokalnie

1. Obowiązkowe Obejrzyj * pliku dockerfile "w folderze *LocalFunctionsProj* . Pliku dockerfile opisuje środowisko wymagane do uruchomienia aplikacji funkcji w systemie Linux: 

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk AS installer-env

    COPY . /src/dotnet-function-app
    RUN cd /src/dotnet-function-app && \
        mkdir -p /home/site/wwwroot && \
        dotnet publish *.csproj --output /home/site/wwwroot
    
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/dotnet:2.0-appservice 
    FROM mcr.microsoft.com/azure-functions/dotnet:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY --from=installer-env ["/home/site/wwwroot", "/home/site/wwwroot"]
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/powershell:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY requirements.txt /
    RUN pip install -r /requirements.txt
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    > [!NOTE]
    > Pełną listę obsługiwanych obrazów podstawowych dla Azure Functions można znaleźć na [stronie Azure Functions Image Base](https://hub.docker.com/_/microsoft-azure-functions-base).
    
1. W folderze *LocalFunctionsProject* Uruchom polecenie [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) i podaj nazwę, `azurefunctionsimage`i tag, `v1.0.0`. Zastąp ciąg `<docker_id>` identyfikatorem konta usługi Docker Hub. To polecenie powoduje skompilowanie obrazu platformy Docker dla kontenera.

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
    Po zakończeniu działania polecenia można uruchomić nowy kontener lokalnie.
    
1. Aby przetestować kompilację, uruchom obraz w lokalnym kontenerze przy użyciu polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , zastępując ponownie `<docker_id>` przy użyciu identyfikatora platformy Docker i dodając argument porty, `-p 8080:80`:

    ```
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Gdy obraz jest uruchomiony w lokalnym kontenerze, Otwórz w przeglądarce, aby `http://localhost:8080`, w którym powinien zostać wyświetlony obraz zastępczy poniżej. Obraz jest wyświetlany w tym momencie, ponieważ funkcja jest uruchomiona w kontenerze lokalnym, tak jak w przypadku platformy Azure, co oznacza, że jest chroniona przez klucz dostępu zdefiniowany w *funkcji Function. JSON* z właściwością `"authLevel": "function"`. Kontener nie został jeszcze opublikowany w aplikacji funkcji na platformie Azure, więc klucz nie jest jeszcze dostępny. Jeśli chcesz przetestować lokalnie, Zatrzymaj platformę Docker, Zmień właściwość autoryzacji na `"authLevel": "anonymous"`, Odbuduj obraz i ponownie uruchom platformę Docker. Następnie zresetuj `"authLevel": "function"` w pliku *Function. JSON*. Aby uzyskać więcej informacji, zobacz [klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys).

    ![Obraz zastępczy wskazujący, że kontener jest uruchomiony lokalnie](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

1. Po zweryfikowaniu aplikacji funkcji w kontenerze Zatrzymaj platformę Docker przy użyciu **kombinacji klawiszy Ctrl**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Wypchnij obraz do centrum Docker

Docker Hub to rejestr kontenerów, który hostuje obrazy i udostępnia usługi obrazów i kontenerów. Aby udostępnić obraz, który obejmuje wdrażanie na platformie Azure, należy wypchnąć go do rejestru.

1. Jeśli jeszcze nie zalogowano się do platformy Docker, zrób to za pomocą polecenia [Docker login](https://docs.docker.com/engine/reference/commandline/login/) , ZASTĘPUJĄC `<docker_id>` identyfikatorem platformy Docker. To polecenie powoduje zapytanie o nazwę użytkownika i hasło. Komunikat "Logowanie pomyślne" potwierdza, że użytkownik jest zalogowany.

    ```
    docker login
    ```
    
1. Po zalogowaniu wypchnij obraz do usługi Docker Hub przy użyciu polecenia [Docker push](https://docs.docker.com/engine/reference/commandline/push/) , ponownie ZASTĘPUJĄC `<docker_id>` identyfikatorem platformy Docker.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. W zależności od szybkości sieci, wypychanie obrazu po raz pierwszy może potrwać kilka minut (wypychanie kolejnych zmian jest znacznie szybsze). Podczas oczekiwania możesz przejoć do następnej sekcji i utworzyć zasoby platformy Azure w innym terminalu.

## <a name="create-supporting-azure-resources-for-your-function"></a>Tworzenie obsługi zasobów platformy Azure dla funkcji

Aby wdrożyć kod funkcji na platformie Azure, musisz utworzyć trzy zasoby:

- Grupa zasobów, która jest kontenerem logicznym dla powiązanych zasobów.
- Konto usługi Azure Storage, które zachowuje stan i inne informacje o Twoich projektach.
- Aplikacja usługi Azure Functions, która udostępnia środowisko do wykonywania kodu funkcji. Aplikacja funkcji jest mapowana na projekt funkcji lokalnej i umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie i udostępnianie zasobów.

Aby utworzyć te elementy, Użyj poleceń interfejsu wiersza polecenia platformy Azure. Każde polecenie zawiera dane wyjściowe JSON po zakończeniu.

1. Zaloguj się do platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login) :

    ```azurecli
    az login
    ```
    
1. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów o nazwie `AzureFunctionsContainers-rg` w regionie `westeurope`. (Zazwyczaj tworzysz grupę zasobów i zasoby w regionie, w którym się znajdujesz, przy użyciu dostępnego regionu z `az account list-locations` polecenie).

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Nie można hostować aplikacji systemu Linux i Windows w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie `AzureFunctionsContainers-rg` z aplikacją funkcji systemu Windows lub aplikacją sieci Web, musisz użyć innej grupy zasobów.
    
1. Utwórz konto magazynu ogólnego przeznaczenia w grupie zasobów i regionie przy użyciu polecenia [AZ Storage account Create](/cli/azure/storage/account#az-storage-account-create) . W poniższym przykładzie Zastąp `<storage_name>` nazwą globalnie unikatową. Nazwy muszą zawierać od 3 do 24 znaków cyfry i małe litery. `Standard_LRS` określa typowe konto ogólnego przeznaczenia.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    W tym samouczku konto magazynu odnosi się tylko do kilku centów USD.
    
1. Użyj polecenia, aby utworzyć plan Premium dla Azure Functions o nazwie `myPremiumPlan` w warstwie cenowej (`--sku EP1`) **elastycznej Premium 1** , w regionie Europa zachodnia (`-location westeurope`lub użyj odpowiedniego regionu w sąsiedztwie) i kontenera systemu Linux (`--is-linux`).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Obsługa systemu Linux dla kontenerów funkcji niestandardowych jest obsługiwana w przypadku [planów dedykowanych (App Service)](functions-scale.md#app-service-plan) i [planów Premium](functions-premium-plan.md#features). W tym miejscu jest używany plan Premium, który można skalować odpowiednio do wymagań. Aby dowiedzieć się więcej o hostingu, zobacz [Porównanie planów hostingu usługi Azure Functions](functions-scale.md). Aby obliczyć koszty, zobacz [stronę cennika funkcji](https://azure.microsoft.com/pricing/details/functions/).

    Polecenie udostępnia również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą którego można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitor Azure Functions](functions-monitoring.md). Wystąpienie nie wiąże się z żadnymi kosztami, dopóki nie zostanie uaktywnione.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Tworzenie i Konfigurowanie aplikacji funkcji na platformie Azure przy użyciu obrazu

Aplikacja funkcji na platformie Azure zarządza wykonywaniem funkcji w Twoim planie hostingu. W tej sekcji użyjesz zasobów platformy Azure z poprzedniej sekcji, aby utworzyć aplikację funkcji z obrazu w usłudze Docker Hub i skonfigurować ją za pomocą parametrów połączenia do usługi Azure Storage.

1. Utwórz aplikację Functions za pomocą polecenia [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) . W poniższym przykładzie Zastąp `<storage_name>` nazwą użytą w poprzedniej sekcji dla konta magazynu. Zastąp również `<app_name>` nazwą globalnie unikatową, a `<docker_id>` z IDENTYFIKATORem platformy Docker.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    Parametr *Deployment-Container-Image-Name* określa obraz do użycia dla aplikacji funkcji. Możesz użyć polecenia [AZ functionapp config Container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) , aby wyświetlić informacje o obrazie używanym do wdrożenia. Można również użyć polecenia [AZ functionapp config Container Set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) do wdrożenia z innego obrazu.

1. Pobierz parametry połączenia dla konta magazynu utworzonego za pomocą polecenia [AZ Storage account show-Connection-String](/cli/azure/storage/account) , przypisując go do zmiennej powłoki `storageConnectionString`:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    
1. Add this setting to the function app by using the [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) command. In the following command, replace `<app_name>` with the name of your function app, and replace `<connection_string>` with the connection string from the previous step (a long encoded string that begins with "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. Funkcja może teraz używać tych parametrów połączenia w celu uzyskania dostępu do konta magazynu.

> [!TIP]
> W bash można użyć zmiennej powłoki, aby przechwycić parametry połączenia zamiast używać Schowka. Najpierw użyj następującego polecenia, aby utworzyć zmienną z parametrami połączenia:
> 
> ```bash
> storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
> ```
> 
> Następnie odwołując się do zmiennej w drugim poleceniu:
> 
> ```azurecli
> az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
> ```

> [!NOTE]    
> W przypadku opublikowania niestandardowego obrazu na koncie kontenera prywatnego należy zamiast tego użyć zmiennych środowiskowych w pliku dockerfile jako parametrów połączenia. Aby uzyskać więcej informacji, zapoznaj się z [instrukcją ENV](https://docs.docker.com/engine/reference/builder/#env). Należy również ustawić zmienne `DOCKER_REGISTRY_SERVER_USERNAME` i `DOCKER_REGISTRY_SERVER_PASSWORD`. Aby użyć wartości, należy ponownie skompilować obraz, wypchnąć obraz do rejestru, a następnie ponownie uruchomić aplikację funkcji na platformie Azure.

## <a name="verify-your-functions-on-azure"></a>Weryfikowanie funkcji na platformie Azure

W przypadku obrazu wdrożonego w aplikacji funkcji na platformie Azure można teraz wywołać funkcję za pomocą żądań HTTP. Ponieważ definicja *Function. JSON* zawiera `"authLevel": "function"`właściwości, należy najpierw uzyskać klucz dostępu (nazywany także "kluczem funkcji") i uwzględnić go jako parametr adresu URL w dowolnych żądaniach do punktu końcowego.

1. Pobierz adres URL funkcji z kluczem dostępu (funkcji) za pomocą Azure Portal lub przy użyciu interfejsu wiersza polecenia platformy Azure z poleceniem `az rest`.)

    # <a name="portal"></a>[Portal](#tab/portal)

    1. Zaloguj się do Azure Portal, a następnie Znajdź swoją aplikację funkcji, wprowadzając nazwę aplikacji funkcji w polu **wyszukiwania** w górnej części strony. W wynikach wybierz zasób **App Service** .

    1. W lewym panelu nawigacyjnym w obszarze **funkcje (tylko do odczytu)** wybierz nazwę funkcji.

    1. W panelu Szczegóły wybierz pozycję **</> Pobierz adres URL funkcji**:
    
        ![Polecenie Pobierz adres URL funkcji na Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. W oknie podręcznym wybierz pozycję **domyślne (klawisz funkcji)** , a następnie **Skopiuj**. Klucz jest ciągiem znaków następującej `?code=`.

        ![Kopiowanie adresu URL funkcji z Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Ponieważ aplikacja funkcji jest wdrażana jako kontener, nie można wprowadzać zmian w kodzie funkcji w portalu. Zamiast tego należy zaktualizować projekt w lokalnym obrazie, ponownie wypchnąć obraz do rejestru, a następnie wdrożyć go na platformie Azure. Wdrożenie ciągłe można skonfigurować w dalszej części artykułu.
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

    1. Utwórz ciąg adresu URL w następującym formacie: zastępowanie `<subscription_id>`, `<resource_group>`i `<app_name>` z IDENTYFIKATORem subskrypcji platformy Azure, grupą zasobów aplikacji funkcji i nazwą aplikacji funkcji, odpowiednio

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Na przykład adres URL może wyglądać następująco:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Dla wygody można zamiast tego przypisać adres URL do zmiennej środowiskowej i użyć go w `az rest` polecenie.
    
    1. Uruchom następujące `az rest` polecenie (dostępne w interfejsie wiersza polecenia platformy Azure w wersji 2.0.77 i nowszych), zastępując `<uri>` ciągiem identyfikatora URI z ostatniego kroku, łącznie z cudzysłowami:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. Dane wyjściowe polecenia to klucz funkcji. Adres URL pełnej funkcji jest następnie `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`, zastępowanie `<app_name>`, `<function_name>`i `<key>` wartościami określonymi przez użytkownika.
    
        > [!NOTE]
        > Pobrany tutaj klucz jest kluczem *hosta* , który działa dla wszystkich funkcji w aplikacji Functions. Metoda pokazana dla portalu Pobiera klucz tylko dla jednej funkcji.

    ---

1. Wklej adres URL funkcji na pasku adresu przeglądarki, dodając `&name=Azure` parametru do końca tego adresu URL. Tekst, taki jak "Hello Azure", powinien pojawić się w przeglądarce.

    ![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Aby przetestować autoryzację, Usuń kod = parametr z adresu URL i sprawdź, czy nie otrzymujesz odpowiedzi z funkcji.


## <a name="enable-continuous-deployment-to-azure"></a>Włączanie ciągłego wdrażania na platformie Azure

Możesz włączyć Azure Functions, aby automatycznie aktualizować wdrożenie obrazu przy każdej aktualizacji obrazu w rejestrze.

1. Włącz ciągłe wdrażanie za pomocą polecenia [AZ functionapp Deployment Container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) , zastępując `<app_name>` nazwą aplikacji funkcji:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    To polecenie umożliwia ciągłe wdrażanie i zwraca adres URL elementu webhook wdrożenia. (Ten adres URL można pobrać w dowolnym momencie za pomocą polecenia [AZ functionapp Deployment Container show-CD-URL](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) ).

1. Skopiuj adres URL elementu webhook wdrożenia do Schowka.

1. Otwórz [centrum platformy Docker](https://hub.docker.com/), zaloguj się i wybierz pozycję **repozytoria** na pasku nawigacyjnym. Znajdź i wybierz pozycję obraz, wybierz kartę elementy **webhook** , określ **nazwę elementu webhook**, wklej adres URL w **adresie URL elementu webhook**, a następnie wybierz pozycję **Utwórz**:

    ![Dodawanie elementu webhook w repozytorium DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. W przypadku zestawu elementów webhook Azure Functions ponownie wdraża obraz przy każdej aktualizacji w usłudze Docker Hub.

## <a name="enable-ssh-connections"></a>Włącz połączenia SSH

Protokół SSH umożliwia bezpieczną komunikację między kontenerem i klientem. Po włączeniu protokołu SSH można nawiązać połączenie z kontenerem za pomocą App Service Advanced Tools (kudu). Aby ułatwić łączenie się z kontenerem przy użyciu protokołu SSH, Azure Functions zapewnia podstawowy obraz, który ma już włączony protokół SSH. Musisz edytować tylko pliku dockerfile, a następnie ponownie skompilować i wdrożyć obraz. Następnie można połączyć się z kontenerem za pomocą narzędzi zaawansowanych (kudu)

1. W pliku dockerfile Dołącz ciąg `-appservice` do obrazu podstawowego w instrukcji `FROM`:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk-appservice AS installer-env
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    Różnice między obrazami podstawowymi opisano w [samouczku App Services — niestandardowe obrazy platformy Docker](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

1. Ponownie skompiluj obraz przy użyciu polecenia `docker build`, zastępując `<docker_id>` IDENTYFIKATORem platformy Docker:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Wypchnij zaktualizowany obraz do centrum platformy Docker, który powinien trwać znacznie krócej niż w przypadku pierwszej wypychania tylko zaktualizowanych segmentów obrazu.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions automatycznie ponownie wdraża obraz w aplikacji funkcji; proces odbywa się w czasie krótszym niż minutę.

1. W przeglądarce Otwórz `https://<app_name>.scm.azurewebsites.net/`, zastępując `<app_name>` unikatową nazwą. Ten adres URL to punkt końcowy narzędzi Advanced Tools (kudu) dla kontenera aplikacji funkcji.

1. Zaloguj się do konta platformy Azure, a następnie wybierz protokół **SSH** , aby nawiązać połączenie z kontenerem. Łączenie może potrwać kilka minut, jeśli platforma Azure nadal trwa proces aktualizowania obrazu kontenera.

1. Po nawiązaniu połączenia z kontenerem Uruchom polecenie `top`, aby wyświetlić aktualnie uruchomione procesy. 

    ![Najważniejsze polecenie systemu Linux działające w sesji SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Zapisywanie w kolejce usługi Azure Storage

Azure Functions umożliwia łączenie funkcji z innymi usługami i zasobami platformy Azure, które muszą napisać własny kod integracji. Te *powiązania*, które reprezentują zarówno dane wejściowe, jak i wyjściowe, są zadeklarowane w definicji funkcji. Dane z powiązań są przekazywane do funkcji jako parametry. *Wyzwalacz* jest specjalnym typem powiązania danych wejściowych. Chociaż funkcja ma tylko jeden wyzwalacz, może mieć wiele powiązań wejściowych i wyjściowych. Aby dowiedzieć się więcej, zobacz temat [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md).

W tej sekcji pokazano, jak zintegrować funkcję z kolejką usługi Azure Storage. Powiązanie danych wyjściowych dodawane do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce.

## <a name="retrieve-the-azure-storage-connection-string"></a>Pobieranie parametrów połączenia usługi Azure Storage

Wcześniej utworzono konto usługi Azure Storage do użycia przez aplikację funkcji. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. Pobierając ustawienia do pliku *Local. Settings. JSON* , można użyć zapisu tego połączenia do kolejki magazynu w ramach tego samego konta podczas lokalnego uruchamiania funkcji. 

1. Z poziomu katalogu głównego projektu uruchom następujące polecenie, zastępując `<app_name>` nazwą aplikacji funkcji z poprzedniego przewodnika Szybki Start. To polecenie spowoduje zastąpienie wszelkich istniejących wartości w pliku.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Otwórz plik *Local. Settings. JSON* i Znajdź wartość o nazwie `AzureWebJobsStorage`, czyli parametry połączenia konta magazynu. Używasz nazwy `AzureWebJobsStorage` i parametrów połączenia w innych sekcjach tego artykułu.

> [!IMPORTANT]
> Ponieważ *Local. Settings. JSON* zawiera wpisy tajne pobrane z platformy Azure, należy zawsze wykluczyć ten plik z kontroli źródła. Plik *. gitignore* utworzony za pomocą projektu funkcji lokalnych domyślnie wyklucza plik.

### <a name="add-an-output-binding-to-functionjson"></a>Dodawanie powiązania danych wyjściowych do funkcji Function. JSON

W Azure Functions każdy typ powiązania wymaga `direction`, `type`i unikatowego `name` do zdefiniowania w pliku *Function. JSON* . Twoja *Funkcja. JSON* zawiera już powiązanie wejściowe dla typu "httpTrigger" i powiązania danych wyjściowych odpowiedzi HTTP. Aby dodać powiązanie do kolejki magazynu, należy zmodyfikować plik w następujący sposób, który dodaje powiązanie danych wyjściowych dla typu "queue", gdzie kolejka jest wyświetlana w kodzie jako argument wejściowy o nazwie `msg`. Powiązanie kolejki wymaga również nazwy kolejki do użycia, w tym przypadku `outqueue`i nazwy ustawień, które przechowują parametry połączenia w tym przypadku `AzureWebJobStorage`.

::: zone pivot="programming-language-csharp"

W projekcie C# biblioteki klas powiązania są zdefiniowane jako atrybuty powiązania w metodzie funkcji. Plik *Function. JSON* jest następnie generowany automatycznie na podstawie tych atrybutów.

1. Dla powiązania kolejki Uruchom następujące polecenie [dotnet Dodaj pakiet](/dotnet/core/tools/dotnet-add-package) , aby dodać pakiet rozszerzenia magazynu do projektu.

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
    ```

1. Otwórz plik *HttpTrigger.cs* i Dodaj następującą instrukcję `using`:

    ```cs
    using Microsoft.Azure.WebJobs.Extensions.Storage;
    ```
    
1. Dodaj następujący parametr do definicji metody `Run`:
    
    ```csharp
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
    ```
    
    Definicja metody `Run` powinna teraz pasować do następującego kodu:
    
    ```csharp
    [FunctionName("HttpTrigger")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
        [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
    ```

`msg` parametr jest typu `ICollector<T>`, który reprezentuje kolekcję komunikatów, które są zapisywane do powiązania danych wyjściowych po zakończeniu działania funkcji. W takim przypadku dane wyjściowe są kolejki magazynu o nazwie `outqueue`. Parametry połączenia dla konta magazynu są ustawiane przez `StorageAccountAttribute`. Ten atrybut wskazuje ustawienie, które zawiera parametry połączenia konta magazynu i może być stosowane na poziomie klasy, metody lub parametru. W takim przypadku można pominąć `StorageAccountAttribute`, ponieważ jest już używane domyślne konto magazynu.

::: zone-end

::: zone pivot="programming-language-javascript"

Zaktualizuj *funkcję Function. JSON* , aby dopasować ją do następującej przez dodanie powiązania kolejki po powiązaniu http:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-powershell"

Zaktualizuj *funkcję Function. JSON* , aby dopasować ją do następującej przez dodanie powiązania kolejki po powiązaniu http:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-python"

Zaktualizuj *funkcję Function. JSON* , aby dopasować ją do następującej przez dodanie powiązania kolejki po powiązaniu http:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-typescript"

Zaktualizuj *funkcję Function. JSON* , aby dopasować ją do następującej przez dodanie powiązania kolejki po powiązaniu http:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

## <a name="add-code-to-use-the-output-binding"></a>Dodaj kod, aby użyć powiązania danych wyjściowych

Po zdefiniowaniu powiązania, nazwa powiązania, w tym przypadku `msg`, pojawia się w kodzie funkcji jako argument (lub w obiekcie `context` w języku JavaScript i TypeScript). Następnie można użyć tej zmiennej do zapisu komunikatów w kolejce. Musisz napisać dowolny kod na potrzeby uwierzytelniania, uzyskać odwołanie do kolejki lub zapisać dane. Wszystkie te zadania integracji są wygodnie obsługiwane w środowisku uruchomieniowym Azure Functions i powiązania danych wyjściowych kolejki.

::: zone pivot="programming-language-csharp"
```csharp
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a message to the output collection.
        msg.Add(string.Format("Name passed to the function: {0}", name));
    }
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```
::: zone-end

::: zone pivot="programming-language-javascript"
```js
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.query.name || (req.body && req.body.name)) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);

        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};
```
::: zone-end

::: zone pivot="programming-language-powershell"
```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

if ($name) {
    $outputMsg = "Name passed to the function: $name"
    Push-OutputBinding -name msg -Value $outputMsg

    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
else {
    $status = [HttpStatusCode]::BadRequest
    $body = "Please pass a name on the query string or in the request body."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```
::: zone-end

::: zone pivot="programming-language-python"
```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```
::: zone-end

::: zone pivot="programming-language-typescript"
```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));

    if (name) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);
        
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};

export default httpTrigger;
```
::: zone-end

### <a name="update-the-image-in-the-registry"></a>Aktualizowanie obrazu w rejestrze

1. W folderze głównym Uruchom ponownie `docker build` i zaktualizuj wersję w znaczniku, aby `v1.0.1`. Tak jak wcześniej, Zastąp `<docker_id>` IDENTYFIKATORem konta centrum Docker:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. Wypchnij zaktualizowany obraz z powrotem do repozytorium za pomocą `docker push`:

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Ze względu na to, że skonfigurowano ciągłe dostarczanie, Aktualizacja obrazu w rejestrze jest ponownie automatycznie aktualizowana przez aplikację funkcji na platformie Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Wyświetlanie komunikatu w kolejce usługi Azure Storage

W przeglądarce Użyj tego samego adresu URL, co wcześniej, aby wywołać funkcję. W przeglądarce powinna zostać wyświetlona taka sama odpowiedź jak wcześniej, ponieważ nie została zmodyfikowana ta część kodu funkcji. Dodany kod, jednak zapisał komunikat przy użyciu parametru adresu URL `name` do kolejki magazynu `outqueue`.

Kolejkę można wyświetlić w [Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) lub [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/). Możesz również wyświetlić kolejkę w interfejsie wiersza polecenia platformy Azure, zgodnie z opisem w następujących krokach:

1. Otwórz plik *Local. JSON* projektu funkcji i skopiuj wartość parametrów połączenia. W terminalu lub w oknie polecenia Uruchom następujące polecenie, aby utworzyć zmienną środowiskową o nazwie `AZURE_STORAGE_CONNECTION_STRING`, wklejając określone parametry połączenia zamiast `<connection_string>`. (Ta zmienna środowiskowa oznacza, że nie trzeba podawać parametrów połączenia dla każdego kolejnego polecenia przy użyciu argumentu `--connection-string`).

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. Obowiązkowe Użyj [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) polecenia, aby wyświetlić kolejki magazynu na Twoim koncie. Dane wyjściowe tego polecenia powinny zawierać kolejkę o nazwie `outqueue`, która została utworzona, gdy funkcja zapisała swój pierwszy komunikat do tej kolejki.
    
    # <a name="bash"></a>[bash](#tab/bash)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    ---

1. Użyj [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) polecenia, aby wyświetlić komunikaty w tej kolejce, która powinna być pierwszą nazwą użytą podczas testowania funkcji wcześniej. Polecenie pobiera pierwszy komunikat w kolejce w [kodowaniu Base64](functions-bindings-storage-queue-trigger.md#encoding), więc należy również zdekodować komunikat, aby był wyświetlany jako tekst.

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    Ponieważ musisz usunąć odwołanie do kolekcji komunikatów i zdekodować ją z formatu Base64, uruchom program PowerShell i użyj polecenia programu PowerShell.

    ---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz kontynuować pracę z funkcją platformy Azure przy użyciu zasobów utworzonych w tym samouczku, możesz pozostawić wszystkie te zasoby na miejscu. Ze względu na to, że został utworzony plan Premium dla Azure Functions, ponosisz jeden lub dwa USD dziennie.

Aby uniknąć bieżących kosztów, Usuń `AzureFunctionsContainer-rg` grupę zasobów, aby wyczyścić wszystkie zasoby w tej grupie: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Następne kroki

+ [Funkcje monitorowania](functions-monitoring.md)
+ [Opcje skalowania i hostowania](functions-scale.md)
+ [Hosting bezserwerowy oparty na Kubernetes](functions-kubernetes-keda.md)
