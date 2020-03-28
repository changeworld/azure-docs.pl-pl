---
title: Tworzenie funkcji platformy Azure w systemie Linux przy użyciu niestandardowego obrazu
description: Informacje o sposobie tworzenia usługi Azure Functions działającej w niestandardowym obrazie systemu Linux.
ms.date: 01/15/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 8c074c677c645dd03e3cf5288d82aa3e65720e8b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239630"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Tworzenie funkcji w systemie Linux przy użyciu kontenera niestandardowego

W tym samouczku utworzysz i wdrożysz kod języka Python w usłudze Azure Functions jako niestandardowy kontener platformy Docker przy użyciu obrazu podstawowego systemu Linux. Zazwyczaj używasz obrazu niestandardowego, gdy funkcje wymagają określonej wersji językowej lub mają określoną zależność lub konfigurację, która nie jest dostarczana przez wbudowany obraz.

Można również użyć domyślnego kontenera usługi Azure App Service, zgodnie z opisem w [programie Utwórz pierwszą funkcję hostowana w systemie Linux.](functions-create-first-azure-function-azure-cli-linux.md) Obsługiwane obrazy podstawowe dla usług Azure Functions znajdują się w [repozytorium obrazów podstawowych usług Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji funkcji i dockerfile przy użyciu narzędzi podstawowe usługi Azure Functions Core.
> * Kompilowanie obrazu niestandardowego na platformie Docker.
> * Publikowanie obrazu niestandardowego w rejestrze kontenerów.
> * Tworzenie zasobów pomocniczych na platformie Azure dla aplikacji funkcji
> * Wdrażanie aplikacji funkcji z usługi Docker Hub.
> * Dodawanie ustawień aplikacji do aplikacji funkcji.
> * Włącz ciągłe wdrażanie.
> * Włącz połączenia SSH z kontenerem.
> * Dodaj powiązanie danych wyjściowych magazynu kolejki. 

Możesz wykonać ten samouczek na dowolnym komputerze z systemem Windows, Mac OS lub Linux. Ukończenie samouczka spowoduje poniesienie kosztów w wysokości kilku usd na koncie platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Podstawowe narzędzia azure functions](./functions-run-local.md#v2) w wersji 2.7.1846 lub nowszej
- Interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.0.77 lub nowszej
- [Środowisko wykonawcze usługi Azure Functions 2.x](functions-versions.md)
- Następujące składniki środowiska wykonawczego języka:
    ::: zone pivot="programming-language-csharp"
    - [.NET Core 2.2.x lub nowszy](https://dotnet.microsoft.com/download)
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - [Node.js](https://nodejs.org/en/download/)
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - [Powershell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7)
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - [Python 3.6 - 64 bit](https://www.python.org/downloads/release/python-3610/) lub [Python 3.7 - 64 bit](https://www.python.org/downloads/release/python-376/)
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - [Node.js](https://nodejs.org/en/download/)
    - [TypeScript](http://www.typescriptlang.org/#download-links)
    ::: zone-end
- [Docker](https://docs.docker.com/install/)
- Identyfikator [platformy Docker](https://hub.docker.com/signup)

### <a name="prerequisite-check"></a>Sprawdzanie pod warunkiem

1. W oknie terminala lub `func --version` polecenia uruchom, aby sprawdzić, czy podstawowe narzędzia usługi Azure Functions są w wersji 2.7.1846 lub nowszej.
1. Uruchom, `az --version` aby sprawdzić, czy wersja interfejsu wiersza polecenia platformy Azure jest 2.0.76 lub nowsza.
1. Uruchom, `az login` aby zalogować się na platformie Azure i zweryfikować aktywną subskrypcję.
1. Uruchom, `docker login` aby zalogować się do platformy Docker. To polecenie kończy się niepowodzeniem, jeśli docker nie jest uruchomiony, w którym to przypadku uruchomić dokowane i ponowić próbę polecenia.

## <a name="create-and-test-the-local-functions-project"></a>Tworzenie i testowanie projektu funkcji lokalnych

1. W wierszu polecenia terminalu lub polecenia utwórz folder dla tego samouczka w odpowiedniej lokalizacji, a następnie przejdź do tego folderu.

1. Postępuj zgodnie z instrukcjami [dotyczącymi Tworzenie i aktywowanie środowiska wirtualnego](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv) w celu utworzenia środowiska wirtualnego do użycia w tym samouczku.

1. Uruchom następujące polecenie dla wybranego języka, aby utworzyć projekt `LocalFunctionsProject`aplikacji funkcji w folderze o nazwie . Opcja `--docker` generuje `Dockerfile` dla projektu, który definiuje odpowiedni kontener niestandardowy do użytku z usługą Azure Functions i wybranego środowiska wykonawczego.

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
    
1. Dodaj funkcję do projektu za pomocą następującego `--name` polecenia, gdzie argument jest `--template` unikatową nazwą funkcji, a argument określa wyzwalacz funkcji. `func new`utworzyć podfolder pasujący do nazwy funkcji zawierającej plik kodu odpowiedni dla wybranego języka projektu i plik konfiguracyjny o nazwie *function.json*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

1. Aby przetestować funkcję lokalnie, uruchom lokalnego hosta środowiska uruchomieniowego usługi Azure Functions w folderze *LocalFunctionsProject:*
   
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

1. Po wyświetleniu `HttpExample` punktu końcowego na wyjściu przejdź do pozycji `http://localhost:7071/api/HttpExample?name=Functions`. Przeglądarka powinna wyświetlać komunikat "Hello, Functions" (różni się nieznacznie w zależności od wybranego języka programowania).

1. Użyj **klawisza Ctrl**-**C,** aby zatrzymać hosta.

## <a name="build-the-container-image-and-test-locally"></a>Tworzenie obrazu kontenera i testowanie lokalnie

1. (Opcjonalnie) Sprawdź *Dockerfile" w folderze *LocalFunctionsProj.* Dockerfile opisuje wymagane środowisko do uruchomienia aplikacji funkcji w systemie Linux: 

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
    > Pełną listę obsługiwanych obrazów podstawowych dla usługi Azure Functions można znaleźć na [stronie obrazu podstawowego usługi Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).
    
1. W folderze *LocalFunctionsProject* uruchom polecenie [kompilacji platformy docker](https://docs.docker.com/engine/reference/commandline/build/) i podaj nazwę i `azurefunctionsimage`tag , `v1.0.0`. Zastąp ciąg `<docker_id>` identyfikatorem konta usługi Docker Hub. To polecenie powoduje skompilowanie obrazu platformy Docker dla kontenera.

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
    Po zakończeniu polecenia można uruchomić nowy kontener lokalnie.
    
1. Aby przetestować kompilację, uruchom obraz w kontenerze lokalnym za `<docker_id>` pomocą polecenia [uruchamiania platformy docker,](https://docs.docker.com/engine/reference/commandline/run/) zastępując ponownie identyfikator platformy Docker i dodając argument portów,: `-p 8080:80`

    ```
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Gdy obraz jest uruchomiony w kontenerze lokalnym, otwórz przeglądarkę, aby , który powinien wyświetlić `http://localhost:8080`obraz zastępczy pokazany poniżej. Obraz pojawia się w tym momencie, ponieważ funkcja jest uruchomiona w kontenerze lokalnym, tak jak na platformie Azure, co `"authLevel": "function"` oznacza, że jest chroniony przez klucz dostępu zdefiniowany w *function.json* z właściwością. Kontener nie został jeszcze opublikowany w aplikacji funkcji na platformie Azure, więc klucz nie jest jeszcze dostępny. Jeśli chcesz przetestować lokalnie, zatrzymaj dokowane, zmień właściwość autoryzacji na `"authLevel": "anonymous"`, odbuduj obraz i uruchom ponownie dokowane. Następnie `"authLevel": "function"` zresetuj w *function.json*. Aby uzyskać więcej informacji, zobacz [klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys).

    ![Obraz zastępczy wskazujący, że kontener działa lokalnie](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

1. Po zweryfikowaniu aplikacji funkcji w kontenerze, zatrzymaj dok. z **ctrl**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Wypychanie obrazu do centrum platformy Docker

Docker Hub to rejestr kontenerów, który obsługuje obrazy i udostępnia usługi obrazów i kontenerów. Aby udostępnić obraz, który obejmuje wdrażanie na platformie Azure, należy wypchnąć go do rejestru.

1. Jeśli nie zalogowano się jeszcze do platformy Docker, zrób to `<docker_id>` za pomocą polecenia logowania do strony platformy [Docker,](https://docs.docker.com/engine/reference/commandline/login/) zastępując identyfikator platformy Docker. To polecenie monituje o podanie nazwy użytkownika i hasła. Komunikat "Logowanie powiodło się" potwierdza, że jesteś zalogowany.

    ```
    docker login
    ```
    
1. Po zalogowaniu się wypchnij obraz do usługi Docker Hub za `<docker_id>` pomocą polecenia [wypychania docker,](https://docs.docker.com/engine/reference/commandline/push/) ponownie zastępując identyfikator platformy Docker.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. W zależności od szybkości sieci, pierwsze naciśnięcie obrazu może potrwać kilka minut (naciśnięcie kolejnych zmian jest znacznie szybsze). Podczas oczekiwania możesz przejść do następnej sekcji i utworzyć zasoby platformy Azure w innym terminalu.

## <a name="create-supporting-azure-resources-for-your-function"></a>Tworzenie zasobów platformy Azure dla swojej funkcji

Aby wdrożyć kod funkcji na platformie Azure, należy utworzyć trzy zasoby:

- Grupa zasobów, która jest logicznym kontenerem dla powiązanych zasobów.
- Konto usługi Azure Storage, które przechowuje stan i inne informacje o projektach.
- Aplikacja funkcji platformy Azure, która zapewnia środowisko do wykonywania kodu funkcji. Aplikacja funkcji mapuje do lokalnego projektu funkcji i umożliwia grupowanie funkcji jako jednostki logicznej w celu łatwiejszego zarządzania, wdrażania i udostępniania zasobów.

Do utworzenia tych elementów używasz poleceń interfejsu wiersza polecenia platformy Azure. Każde polecenie zapewnia wyjście JSON po zakończeniu.

1. Zaloguj się na platformie Azure za pomocą polecenia [logowania az:](/cli/azure/reference-index#az-login)

    ```azurecli
    az login
    ```
    
1. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów o nazwie `AzureFunctionsContainers-rg` w `westeurope` regionie. (Zazwyczaj tworzysz grupę zasobów i zasoby w regionie w pobliżu, używając dostępnego regionu z `az account list-locations` polecenia).

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Nie można hostować aplikacji systemu Linux i Windows w tej samej grupie zasobów. Jeśli masz istniejącą grupę zasobów o nazwie `AzureFunctionsContainers-rg` z aplikacją funkcji systemu Windows lub aplikacją sieci Web, musisz użyć innej grupy zasobów.
    
1. Utwórz ogólne konto magazynu w grupie zasobów i regionie za pomocą [polecenia tworzenia konta magazynu az.](/cli/azure/storage/account#az-storage-account-create) W poniższym przykładzie zastąp `<storage_name>` globalnie unikatową nazwą odpowiednią dla Ciebie. Nazwy muszą zawierać tylko od trzech do 24 cyfr i małych liter. `Standard_LRS`określa typowe konto ogólnego przeznaczenia.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Konto magazynu wiąże się tylko z kilkoma centami USD za ten samouczek.
    
1. Użyj polecenia, aby utworzyć plan Premium `myPremiumPlan` dla usługi Azure Functions o`--sku EP1`nazwie w warstwie`-location westeurope` **cenowej Elastic Premium 1** ( ),`--is-linux`w regionie Europy Zachodniej ( , lub użyć odpowiedniego regionu w pobliżu) i w kontenerze systemu Linux ( ).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Hosting linuksa dla kontenerów funkcji niestandardowych są obsługiwane w [planach dedykowanych (App Service)](functions-scale.md#app-service-plan) i [planach Premium.](functions-premium-plan.md#features) Korzystamy tutaj z planu Premium, który można skalować w razie potrzeby. Aby dowiedzieć się więcej o hostingu, zobacz [Porównanie planów hostingu usługi Azure Functions](functions-scale.md). Aby obliczyć koszty, zobacz [stronę cennika Funkcje](https://azure.microsoft.com/pricing/details/functions/).

    Polecenie aplikuje również skojarzone wystąpienie usługi Azure Application Insights w tej samej grupie zasobów, za pomocą której można monitorować aplikację funkcji i wyświetlać dzienniki. Aby uzyskać więcej informacji, zobacz [Monitorowanie funkcji platformy Azure](functions-monitoring.md). Wystąpienie nie ponosi żadnych kosztów, dopóki go nie aktywujesz.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Tworzenie i konfigurowanie aplikacji funkcji na platformie Azure za pomocą obrazu

Aplikacja funkcji na platformie Azure zarządza wykonywaniem funkcji w planie hostingu. W tej sekcji użyj zasobów platformy Azure z poprzedniej sekcji, aby utworzyć aplikację funkcji z obrazu w centrum platformy Docker i skonfigurować ją za pomocą ciągu połączenia z usługą Azure Storage.

1. Utwórz aplikację Functions za pomocą polecenia [az functionapp create.](/cli/azure/functionapp#az-functionapp-create) W poniższym przykładzie zastąp `<storage_name>` nazwą używaną w poprzedniej sekcji dla konta magazynu. Zastąp `<app_name>` również unikatową na `<docker_id>` całym świecie nazwą odpowiednią dla Użytkownika oraz identyfikatorem platformy Docker.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    Parametr *deployment-container-image-name* określa obraz, który ma być używany dla aplikacji funkcji. Za pomocą polecenia [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) można wyświetlić informacje o obrazie używanym do wdrożenia. Można również użyć polecenia [az functionapp config container set,](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) aby wdrożyć z innego obrazu.

1. Pobierz parametry połączenia dla konta magazynu utworzonego za pomocą polecenia [show-connection-string konta magazynu az,](/cli/azure/storage/account) przypisując go do zmiennej `storageConnectionString`powłoki:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Dodaj to ustawienie do aplikacji funkcji za pomocą polecenia [az functionapp config appsettings set.](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) W poniższym poleceniu zastąp `<app_name>` nazwą `<connection_string>` aplikacji funkcyjnej i zastąp ciągiem połączenia z poprzedniego kroku (długim zakodowanym ciągiem zaczyna się od "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. Funkcja może teraz używać tego ciągu połączenia, aby uzyskać dostęp do konta magazynu.

> [!TIP]
> W bash, można użyć zmiennej powłoki do przechwytywania ciągu połączenia zamiast korzystania ze schowka. Najpierw użyj następującego polecenia, aby utworzyć zmienną z ciągiem połączenia:
> 
> ```bash
> storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
> ```
> 
> Następnie należy zapoznać się ze zmienną w drugim poleceniu:
> 
> ```azurecli
> az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
> ```

> [!NOTE]    
> Jeśli publikujesz obraz niestandardowy na koncie kontenera prywatnego, należy użyć zmiennych środowiskowych w Dockerfile dla ciągu połączenia zamiast tego. Więcej informacji można znaleźć w [instrukcji ENV](https://docs.docker.com/engine/reference/builder/#env). Należy również ustawić `DOCKER_REGISTRY_SERVER_USERNAME` zmienne `DOCKER_REGISTRY_SERVER_PASSWORD`i . Aby użyć wartości, należy odbudować obraz, wypchnąć obraz do rejestru, a następnie ponownie uruchomić aplikację funkcji na platformie Azure.

## <a name="verify-your-functions-on-azure"></a>Weryfikowanie funkcji na platformie Azure

Z obraz wdrożony do aplikacji funkcji na platformie Azure, można teraz wywołać funkcję za pośrednictwem żądań HTTP. Ponieważ *definicja function.json* `"authLevel": "function"`zawiera właściwość, należy najpierw uzyskać klucz dostępu (nazywany również "kluczem funkcyjnym") i dołączyć go jako parametr ADRESU URL we wszystkich żądaniach do punktu końcowego.

1. Pobierz adres URL funkcji za pomocą klucza dostępu (funkcji) przy użyciu witryny Azure portal lub przy użyciu interfejsu wiersza polecenia platformy Azure za pomocą `az rest` polecenia.)

    # <a name="portal"></a>[Portal](#tab/portal)

    1. Zaloguj się do witryny Azure portal, a następnie zlokalizuj aplikację funkcji, wprowadzając nazwę aplikacji funkcji w polu **wyszukiwania** u góry strony. W wynikach wybierz zasób **usługi app service.**

    1. W lewym panelu nawigacyjnym w obszarze **Funkcje (Tylko do odczytu)** wybierz nazwę funkcji.

    1. W panelu szczegółów wybierz **</> Pobierz adres URL funkcji:**
    
        ![Polecenie Pobierz adres URL funkcji w witrynie Azure portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. W wyskakującym okienku wybierz **pozycję domyślną (klawisz funkcji),** a następnie **skopiuj**. Kluczem jest ciąg znaków `?code=`po .

        ![Kopiowanie adresu URL funkcji z witryny Azure portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Ponieważ aplikacja funkcji jest wdrażany jako kontener, nie można wprowadzać zmian w kodzie funkcji w portalu. Zamiast tego należy zaktualizować projekt w obrazie lokalnym, wypchnąć obraz do rejestru ponownie, a następnie ponownie wdrożyć na platformie Azure. Ciągłe wdrażanie można skonfigurować w dalszej sekcji.
    
    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

    1. Skonstruuj ciąg adresu `<subscription_id>`URL `<resource_group>`w `<app_name>` następującym formacie, zastępując i za pomocą identyfikatora subskrypcji platformy Azure, grupy zasobów aplikacji funkcji i nazwy aplikacji funkcji, odpowiednio:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Na przykład adres URL może wyglądać następująco:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Dla wygody można przypisać adres URL do zmiennej środowiskowej i użyć go w `az rest` poleceniu.
    
    1. Uruchom następujące `az rest` polecenie (dostępne w usłudze Azure CLI w wersji 2.0.77 i nowszej), zastępując `<uri>` ciąg identyfikatorem URI z ostatniego kroku, w tym cudzysłowy:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. Dane wyjściowe polecenia jest kluczem funkcyjnym. Pełny adres URL `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`funkcji `<app_name>`to `<function_name>`następnie `<key>` , zastępując , i z określonymi wartościami.
    
        > [!NOTE]
        > Klucz pobrany w tym miejscu jest klucz *hosta,* który działa dla wszystkich funkcji w aplikacji funkcji; metoda pokazana dla portalu pobiera klucz tylko dla jednej funkcji.

    ---

1. Wklej adres URL funkcji do paska adresu `&name=Azure` przeglądarki, dodając parametr na końcu tego adresu URL. Tekst taki jak "Hello Azure" powinien pojawić się w przeglądarce.

    ![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Aby przetestować autoryzację, usuń parametr code= z adresu URL i sprawdź, czy nie otrzymasz odpowiedzi z funkcji.


## <a name="enable-continuous-deployment-to-azure"></a>Włącz ciągłe wdrażanie na platformie Azure

Można włączyć usługi Azure Functions, aby automatycznie aktualizować wdrożenie obrazu przy każdej aktualizacji obrazu w rejestrze.

1. Włącz ciągłe wdrażanie za pomocą polecenia [az functionapp deployment deployment container config,](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) zastępując `<app_name>` nazwą aplikacji funkcji:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    To polecenie umożliwia ciągłe wdrażanie i zwraca adres URL elementu webhook wdrożenia. (Ten adres URL można pobrać w dowolnym późniejszym czasie za pomocą polecenia [az functionapp deployment deployment container show-cd-url).](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url)

1. Skopiuj adres URL elementu webhook wdrażania do schowka.

1. Otwórz [Centrum platformy Docker](https://hub.docker.com/), zaloguj się i wybierz **repozytoria** na pasku nawigacyjnym. Lokalizowanie i zaznaczanie obrazu, zaznacz kartę **Webhooks,** określ **nazwę elementu Webhook,** wklej adres URL w **adresie URL elementu webhook,** a następnie wybierz pozycję **Utwórz:**

    ![Dodawanie elementu webhook w repozytorium usługi DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Po skonfigurowaniu elementu webhook usługa Azure Functions ponownie wprowadza obraz przy każdej aktualizacji w centrum platformy Docker Hub.

## <a name="enable-ssh-connections"></a>Włączanie połączeń SSH

Protokół SSH umożliwia bezpieczną komunikację między kontenerem i klientem. Po włączeniu protokołu SSH można połączyć się z kontenerem za pomocą zaawansowanych narzędzi usługi App Service (Kudu). Aby ułatwić łączenie się z kontenerem przy użyciu funkcji SSH, usługa Azure Functions udostępnia obraz podstawowy, który ma już włączoną funkcję SSH. Wystarczy tylko edytować plik Dockerfile, a następnie odbudować i ponownie wdrożyć obraz. Następnie można połączyć się z kontenerem za pomocą narzędzi zaawansowanych (Kudu)

1. W pliku Dockerfile dołącz `-appservice` ciąg do obrazu `FROM` podstawowego w instrukcji:

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

    Różnice między obrazami podstawowymi są opisane w [samouczku obrazy dokowane usługi App Services — Niestandardowe obrazy dokowane](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

1. Odbuduj obraz `docker build` za pomocą `<docker_id>` polecenia ponownie, zastępując identyfikatorem platformy Docker:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Wypchnij zaktualizowany obraz do centrum platformy Docker, co powinno zająć znacznie mniej czasu niż pierwsze wypychanie tylko zaktualizowane segmenty obrazu muszą zostać przekazane.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Usługa Azure Functions automatycznie ponownie wdroży obraz do aplikacji funkcji; proces odbywa się w mniej niż minutę.

1. W przeglądarce `https://<app_name>.scm.azurewebsites.net/`otwórz `<app_name>` , zastępując swoją unikalną nazwą. Ten adres URL jest punktem końcowym zaawansowanych narzędzi (Kudu) dla kontenera aplikacji funkcji.

1. Zaloguj się do konta platformy Azure, a następnie wybierz **SSH,** aby ustanowić połączenie z kontenerem. Łączenie może potrwać kilka chwil, jeśli platforma Azure jest nadal w trakcie aktualizowania obrazu kontenera.

1. Po nawiązaniu połączenia z kontenerem `top` uruchom polecenie, aby wyświetlić aktualnie uruchomione procesy. 

    ![Najlepsze polecenie systemu Linux uruchomione w sesji SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Pisanie w kolejce usługi Azure Storage

Usługa Azure Functions umożliwia łączenie funkcji z innymi usługami platformy Azure i zasobami, które muszą pisać własny kod integracji. Te *powiązania*, które reprezentują zarówno dane wejściowe, jak i wyjściowe, są zadeklarowane w definicji funkcji. Dane z powiązań są podawane do funkcji jako parametry. *Wyzwalacz* jest specjalnym typem powiązania wejściowego. Chociaż funkcja ma tylko jeden wyzwalacz, może mieć wiele powiązań wejściowych i wyjściowych. Aby dowiedzieć się więcej, zobacz [Koncepcje wyzwalaczy i powiązań usług Azure Functions](functions-triggers-bindings.md).

W tej sekcji pokazano, jak zintegrować funkcję z kolejką usługi Azure Storage. Powiązanie danych wyjściowych, które można dodać do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce.

## <a name="retrieve-the-azure-storage-connection-string"></a>Pobieranie ciągu połączenia usługi Azure Storage

Wcześniej utworzono konto usługi Azure Storage do użycia przez aplikację funkcji. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. Pobierając to ustawienie do pliku *local.settings.json,* można użyć tego zapisu połączenia do kolejki magazynu na tym samym koncie podczas lokalnego uruchamiania funkcji. 

1. W katalogu głównym projektu uruchom następujące polecenie, zastępując `<app_name>` nazwą aplikacji funkcji z poprzedniego przewodnika Szybki start. To polecenie zastąpi wszystkie istniejące wartości w pliku.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Otwórz *local.settings.json* i znajdź `AzureWebJobsStorage`wartość o nazwie , która jest ciągiem połączenia konta magazynu. Nazwy `AzureWebJobsStorage` i ciągu połączenia należy użyć w innych sekcjach tego artykułu.

> [!IMPORTANT]
> Ponieważ *local.settings.json* zawiera wpisy tajne pobrane z platformy Azure, zawsze wyklucz ten plik z kontroli źródła. Plik *.gitignore* utworzony za pomocą projektu funkcji lokalnych domyślnie wyklucza plik.

### <a name="add-an-output-binding-to-functionjson"></a>Dodawanie powiązania wyjściowego do pliku function.json

W usłudze Azure Functions każdy `direction` `type`typ powiązania `name` wymaga , i unikatowy do zdefiniowania w pliku *function.json.* Twój *function.json* zawiera już powiązanie danych wejściowych dla typu "httpTrigger" i powiązanie danych wyjściowych dla odpowiedzi HTTP. Aby dodać powiązanie do kolejki magazynu, zmodyfikuj plik w następujący sposób, co dodaje powiązanie wyjściowe `msg`dla typu "kolejka", gdzie kolejka pojawia się w kodzie jako argument wejściowy o nazwie . Powiązanie kolejki wymaga również nazwy kolejki do użycia, w tym przypadku, `outqueue`i nazwy ustawień, `AzureWebJobStorage`które przechowują ciąg połączenia, w tym przypadku .

::: zone pivot="programming-language-csharp"

W projekcie biblioteki klas języka C# powiązania są definiowane jako atrybuty powiązania w metodzie funkcji. Plik *function.json* jest następnie automatyczniegenerowany na podstawie tych atrybutów.

1. Dla powiązania kolejki uruchom następujące [polecenie dotnet dodaj pakiet,](/dotnet/core/tools/dotnet-add-package) aby dodać pakiet rozszerzenia magazynu do projektu.

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
    ```

1. Otwórz plik *HttpTrigger.cs* i dodaj następującą `using` instrukcję:

    ```cs
    using Microsoft.Azure.WebJobs.Extensions.Storage;
    ```
    
1. Dodaj następujący parametr `Run` do definicji metody:
    
    ```csharp
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
    ```
    
    Definicja `Run` metody powinna teraz być zgodna z następującym kodem:
    
    ```csharp
    [FunctionName("HttpTrigger")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
        [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
    ```

Parametr `msg` jest `ICollector<T>` typem, który reprezentuje zbiór komunikatów, które są zapisywane do powiązania danych wyjściowych po zakończeniu funkcji. W takim przypadku dane wyjściowe `outqueue`to kolejka magazynu o nazwie . Parametry połączenia dla konta Magazyn jest `StorageAccountAttribute`ustawiany przez plik . Ten atrybut wskazuje ustawienie, które zawiera parametry połączenia konta magazynu i może być stosowane na poziomie klasy, metody lub parametru. W takim przypadku można `StorageAccountAttribute` pominąć, ponieważ używasz już domyślnego konta magazynu.

::: zone-end

::: zone pivot="programming-language-javascript"

Zaktualizuj *plik function.json,* aby dopasować następujące elementy, dodając powiązanie kolejki po wiązaniu HTTP:

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

Zaktualizuj *plik function.json,* aby dopasować następujące elementy, dodając powiązanie kolejki po wiązaniu HTTP:

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

Zaktualizuj *plik function.json,* aby dopasować następujące elementy, dodając powiązanie kolejki po wiązaniu HTTP:

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

Zaktualizuj *plik function.json,* aby dopasować następujące elementy, dodając powiązanie kolejki po wiązaniu HTTP:

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

Po zdefiniowaniu powiązania nazwa powiązania, w tym `msg`przypadku, pojawia się w kodzie `context` funkcji jako argument (lub w obiekcie w językach JavaScript i TypeScript). Następnie można użyć tej zmiennej do zapisywania wiadomości do kolejki. Należy napisać dowolny kod do uwierzytelniania, uzyskiwania odwołania do kolejki lub zapisywania danych. Wszystkie te zadania integracji są wygodnie obsługiwane w usłudze Azure Functions powiązania środowiska wykonawczego i danych wyjściowych kolejki.

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

1. W folderze głównym `docker build` uruchom ponownie, a tym razem `v1.0.1`zaktualizuj wersję w tagu do . Tak jak `<docker_id>` poprzednio, zamień identyfikator konta Usługi Docker Hub:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. Wypchnij zaktualizowany obraz z powrotem do repozytorium za pomocą: `docker push`

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Ponieważ skonfigurowano dostarczanie ciągłe, aktualizowanie obrazu w rejestrze ponownie automatycznie aktualizuje aplikację funkcji na platformie Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Wyświetlanie wiadomości w kolejce usługi Azure Storage

W przeglądarce użyj tego samego adresu URL, co poprzednio, aby wywołać funkcję. Przeglądarka powinna wyświetlać taką samą odpowiedź jak poprzednio, ponieważ nie zmodyfikowałeś tej części kodu funkcji. Dodany kod napisał jednak wiadomość używającą `name` parametru `outqueue` ADRESU URL do kolejki magazynu.

Kolejkę można wyświetlić w [witrynie Azure portal](../storage/queues/storage-quickstart-queues-portal.md) lub w [Eksploratorze magazynu platformy Microsoft Azure](https://storageexplorer.com/). Kolejkę można również wyświetlić w wierszu polecenia platformy Azure zgodnie z opisem w następujących krokach:

1. Otwórz plik *local.setting.json* projektu funkcji i skopiuj wartość ciągu połączenia. W oknie terminala lub polecenia uruchom następujące polecenie, `AZURE_STORAGE_CONNECTION_STRING`aby utworzyć zmienną środowiskową `<connection_string>`o nazwie , wklejając określony ciąg połączenia zamiast pliku . (Ta zmienna środowiskowa oznacza, że nie trzeba podawać ciągu `--connection-string` połączenia do każdego kolejnego polecenia przy użyciu argumentu).

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. (Opcjonalnie) Użyj [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) polecenia, aby wyświetlić kolejki magazynu na koncie. Dane wyjściowe z tego polecenia `outqueue`powinny zawierać kolejkę o nazwie , która została utworzona, gdy funkcja napisała swój pierwszy komunikat do tej kolejki.
    
    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    ---

1. Użyj [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) polecenia, aby wyświetlić wiadomości w tej kolejce, która powinna być pierwszą nazwą używaną podczas testowania funkcji wcześniej. Polecenie pobiera pierwszą wiadomość w kolejce w [kodowaniu base64,](functions-bindings-storage-queue-trigger.md#encoding)więc należy również zdekodować wiadomość, aby wyświetlić jako tekst.

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    Ponieważ należy wyłuskać kolekcji wiadomości i dekodować z base64, uruchom program PowerShell i użyj polecenia programu PowerShell.

    ---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz kontynuować pracę z funkcją platformy Azure przy użyciu zasobów utworzonych w tym samouczku, możesz pozostawić wszystkie te zasoby na swoim miejscu. Ponieważ utworzono plan premium dla usług Azure Functions, poniesiesz jeden lub dwa USD dziennie w kosztach bieżących.

Aby uniknąć bieżących `AzureFunctionsContainer-rg` kosztów, usuń grupę zasobów, aby wyczyścić wszystkie zasoby w tej grupie: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Następne kroki

+ [Funkcje monitorowania](functions-monitoring.md)
+ [Opcje skalowania i hostingu](functions-scale.md)
+ [Hosting bezserwerowy oparty na użądli](functions-kubernetes-keda.md)
