---
title: Tworzenie Azure Functions w systemie Linux przy użyciu obrazu niestandardowego
description: Dowiedz się, jak utworzyć Azure Functions uruchomiony na niestandardowym obrazie systemu Linux.
author: ggailey777
ms.author: glenga
ms.date: 09/27/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: b4fd931142f8a3daf11b0540241323453e25ea4f
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839070"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Tworzenie funkcji w systemie Linux przy użyciu obrazu niestandardowego

Azure Functions umożliwia hostowanie funkcji w systemie Linux we własnym kontenerze niestandardowym. Można również [hostować przy użyciu domyślnego kontenera Azure App Service](functions-create-first-azure-function-azure-cli-linux.md). Ta funkcja wymaga [środowiska uruchomieniowego programu Functions 2. x](functions-versions.md).

W tym samouczku dowiesz się, jak wdrożyć funkcje na platformie Azure jako obraz niestandardowy platformy Docker. Ten wzorzec jest przydatny, gdy trzeba dostosować wbudowany obraz kontenera. Możesz chcieć użyć niestandardowego obrazu, gdy funkcje wymagają określonej wersji językowej lub wymagają określonej zależności lub konfiguracji, która nie jest dostępna w ramach wbudowanego obrazu. Obsługiwane obrazy podstawowe dla Azure Functions są dostępne w [repozytorium Azure Functions obrazów podstawowych](https://hub.docker.com/_/microsoft-azure-functions-base). 

W tym samouczku pokazano, jak za pomocą Azure Functions Core Tools utworzyć funkcję w niestandardowym obrazie systemu Linux. Ten obraz jest publikowany w aplikacji funkcji na platformie Azure, która została utworzona przy użyciu interfejsu wiersza polecenia platformy Azure. Później należy zaktualizować funkcję w celu nawiązania połączenia z usługą Azure queue storage. Można również włączyć.  

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji funkcji i pliku dockerfile przy użyciu podstawowych narzędzi.
> * Tworzenie obrazu niestandardowego przy użyciu platformy Docker.
> * Publikowanie obrazu niestandardowego w rejestrze kontenerów.
> * Utwórz konto usługi Azure Storage.
> * Utwórz plan hostingu w warstwie Premium.
> * Wdróż aplikację funkcji z poziomu usługi Docker Hub.
> * Dodaj ustawienia aplikacji do aplikacji funkcji.
> * Włącz ciągłe wdrażanie.
> * Włącz połączenia SSH z kontenerem.
> * Dodawanie powiązania wyjściowego magazynu kolejki. 
> * Dodaj monitorowanie Application Insights.

Poniższe kroki są obsługiwane na komputerze Mac, Windows lub Linux. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed uruchomieniem tego przykładu należy wykonać następujące czynności:

* Zainstaluj [podstawowe narzędzia platformy Azure w wersji 2. x](functions-run-local.md#v2).

* Zainstaluj [interfejs wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Uruchom `az --version`, aby znaleźć posiadaną wersję.  
Możesz również użyć [Azure Cloud Shell](https://shell.azure.com/bash).

* Aktywna subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="create-the-local-project"></a>Utwórz projekt lokalny

Uruchom następujące polecenie w wierszu polecenia, aby utworzyć projekt aplikacji funkcji w folderze `MyFunctionProj` bieżącego katalogu lokalnego. W przypadku projektu w języku Python [należy uruchomić program w środowisku wirtualnym](functions-create-first-function-python.md#create-and-activate-a-virtual-environment-optional).

```bash
func init MyFunctionProj --docker
```

Po dołączeniu opcji `--docker` dla projektu jest generowana pliku dockerfile. Ten plik jest używany do tworzenia niestandardowego kontenera, w którym ma zostać uruchomiony projekt. Używany obraz podstawowy zależy od wybranego języka środowiska wykonawczego procesu roboczego.  

Po wyświetleniu monitu wybierz środowisko uruchomieniowe procesu roboczego z następujących języków:

* `dotnet`: tworzy projekt biblioteki klas .NET Core (. csproj).
* `node`: tworzy projekt JavaScript.
* `python`: tworzy projekt w języku Python.  

Użyj poniższego polecenia, aby przejść do nowego folderu projektu `MyFunctionProj`.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-from-the-docker-file"></a>Kompiluj z pliku platformy Docker

Zapoznaj się z _pliku dockerfile_ w folderze głównym projektu. Ten plik zawiera opis środowiska, które jest wymagane do uruchomienia aplikacji funkcji w systemie Linux. Poniższy przykład to pliku dockerfile, który tworzy kontener, który uruchamia aplikację funkcji w środowisku uruchomieniowym procesu roboczego JavaScript (Node. js): 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> Pełną listę obsługiwanych obrazów podstawowych dla Azure Functions można znaleźć na [stronie Azure Functions Image Base](https://hub.docker.com/_/microsoft-azure-functions-base).

### <a name="run-the-build-command"></a>Uruchom polecenie `build`

W folderze głównym Uruchom polecenie [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) i podaj nazwę, `mydockerimage` i tag, `v1.0.0`. Zastąp `<docker-id>` IDENTYFIKATORem konta centrum platformy Docker. To polecenie kompiluje obraz platformy Docker dla kontenera.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Po zakończeniu działania polecenia można uruchomić nowy kontener lokalnie.

### <a name="run-the-image-locally"></a>Uruchamianie obrazu lokalnie
Sprawdź, czy utworzony obraz działa, uruchamiając obraz platformy Docker w kontenerze lokalnym. Wydaj polecenie [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) i Przekaż do niego nazwę oraz tag obrazu. Upewnij się, że port został określony przy użyciu argumentu `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

W przypadku obrazu niestandardowego działającego w lokalnym kontenerze platformy Docker Sprawdź, czy aplikacja funkcji i kontener działają prawidłowo, przechodząc do <http://localhost:8080>.

![Uruchom aplikację funkcji lokalnie.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> W tym momencie podczas próby wywołania określonej funkcji HTTP pojawia się odpowiedź na błąd HTTP 401. Wynika to z faktu, że funkcja działa w lokalnym kontenerze, tak jak w przypadku platformy Azure, co oznacza, że klucz funkcji jest wymagany. Ponieważ kontener nie został jeszcze opublikowany w aplikacji funkcji, nie jest dostępny żaden klucz funkcji. Zobaczysz później, że w przypadku korzystania z podstawowych narzędzi do publikowania kontenera zostaną wyświetlone klucze funkcji. Jeśli chcesz przetestować funkcję uruchomioną w kontenerze lokalnym, możesz zmienić [klucz autoryzacji](functions-bindings-http-webhook.md#authorization-keys) na `anonymous`. 

Po zweryfikowaniu aplikacji funkcji w kontenerze Zatrzymaj wykonywanie. Teraz można wypchnąć obraz niestandardowy do konta centrum platformy Docker.

## <a name="push-to-docker-hub"></a>Wypychanie do centrum platformy Docker

Rejestr to aplikacja, która hostuje obrazy i oferuje usługi obrazów i kontenerów usług. Aby udostępnić obraz, należy wypchnąć go do rejestru. Docker Hub to rejestr obrazów platformy Docker, które umożliwiają hostowanie własnych repozytoriów — publicznych lub prywatnych.

Przed wypchnięciem obrazu należy zalogować się do usługi Docker Hub przy użyciu polecenia [Docker login](https://docs.docker.com/engine/reference/commandline/login/) . Zastąp wartość `<docker-id>` nazwą swojego konta i wpisz swoje hasło do konsoli w wierszu polecenia. Inne opcje hasła usługi Docker Hub można znaleźć w [dokumentacji polecenia Docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

Komunikat "Logowanie pomyślne" potwierdza, że użytkownik jest zalogowany. Po zalogowaniu wypchnij obraz do usługi Docker Hub przy użyciu polecenia [Docker push](https://docs.docker.com/engine/reference/commandline/push/) .

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Po pomyślnym wypchnięciu można użyć obrazu jako źródła wdrożenia dla nowej aplikacji funkcji na platformie Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>Tworzenie planu Premium

Hosting systemu Linux dla kontenerów funkcji niestandardowych obsługiwanych w ramach [dedykowanych planów (App Service)](functions-scale.md#app-service-plan) i [planów Premium](functions-premium-plan.md#features). W tym samouczku jest używany plan Premium, który można skalować zgodnie z wymaganiami. Aby dowiedzieć się więcej o hostingu, zobacz [Azure Functions porównanie planów hostingu](functions-scale.md).

Poniższy przykład tworzy plan Premium o nazwie `myPremiumPlan` w warstwie cenowej **elastyczne 1** . (`--sku EP1`) w regionie zachodnie Stany usa (`-location WestUS`) i w kontenerze systemu Linux (`--is-linux`).

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-an-app-from-the-image"></a>Tworzenie aplikacji na podstawie obrazu

Aplikacja funkcji zarządza wykonywaniem funkcji w Twoim planie hostingu. Tworzenie aplikacji funkcji na podstawie obrazu usługi Docker Hub przy użyciu polecenia [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) .

W poniższym poleceniu Zastąp unikatową nazwę aplikacji funkcji, w której zobaczysz symbol zastępczy `<app_name>` i nazwę konta magazynu dla `<storage_name>`. @No__t-0 jest używana jako domyślna domena DNS aplikacji funkcji, więc nazwa musi być unikatowa dla wszystkich aplikacji na platformie Azure. Jak wcześniej, `<docker-id>` jest nazwą konta platformy Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

Parametr _Deployment-Container-Image-Name_ wskazuje obraz hostowany w usłudze Docker Hub, który ma zostać użyty do utworzenia aplikacji funkcji. Użyj polecenia [AZ functionapp config Container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) , aby wyświetlić informacje o obrazie używanym do wdrożenia. Użyj polecenia [AZ functionapp config Container Set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) do wdrożenia z innego obrazu.

## <a name="configure-the-function-app"></a>Konfigurowanie aplikacji funkcji

Funkcja wymaga parametrów połączenia w celu nawiązania połączenia z domyślnym kontem magazynu. W przypadku publikowania obrazu niestandardowego na koncie kontenera prywatnego należy zamiast tego ustawić te ustawienia aplikacji jako zmienne środowiskowe w pliku dockerfile przy użyciu [instrukcji ENV](https://docs.docker.com/engine/reference/builder/#env)lub podobnego.

W tym przypadku `<storage_name>` jest nazwą utworzonego konta magazynu. Pobierz parametry połączenia za pomocą polecenia [AZ Storage account show-Connection-String](/cli/azure/storage/account) . Dodaj te ustawienia aplikacji w aplikacji funkcji za pomocą polecenia [AZ functionapp config AppSettings Set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) .

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
> Jeśli kontener jest prywatny, należy również ustawić następujące ustawienia aplikacji  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> Aby można było pobrać te wartości, należy zatrzymać i uruchomić aplikację funkcji.

## <a name="verify-your-functions"></a>Weryfikowanie funkcji

<!-- we should replace this with a CLI or API-based approach, when we get something better than REST -->

Utworzona funkcja wyzwalana przez protokół HTTP wymaga [klucza funkcji](functions-bindings-http-webhook.md#authorization-keys) podczas wywoływania punktu końcowego. W tej chwili Najprostszym sposobem uzyskania adresu URL funkcji, w tym klucza, jest z [Azure Portal]. 

> [!TIP]
> Klucze funkcji można także uzyskać za pomocą [interfejsów API zarządzania kluczami](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), co wymaga zaprezentowania [tokenu okaziciela na potrzeby uwierzytelniania](/cli/azure/account#az-account-get-access-token).

Znajdź nową aplikację funkcji w [Azure Portal] , wpisując nazwę aplikacji funkcji w polu **wyszukiwania** w górnej części strony i wybierając zasób **App Service** .

Wybierz funkcję **MyHttpTrigger** , wybierz pozycję **</> Pobierz adres URL funkcji** > **domyślne (klawisz funkcyjny)**  > **kopia**.

![Skopiuj adres URL funkcji z Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)

W tym adresie URL klucz funkcji jest parametrem zapytania `code`. 

> [!NOTE]  
> Ponieważ aplikacja funkcji jest wdrażana jako kontener, nie można wprowadzać zmian w kodzie funkcji w portalu. Zamiast tego należy zaktualizować projekt w kontenerze lokalnym i ponownie opublikować go na platformie Azure.

Wklej adres URL funkcji na pasku adresu przeglądarki. Dodaj wartość ciągu zapytania `&name=<yourname>` do końca tego adresu URL i naciśnij klawisz `Enter` na klawiaturze, aby wykonać żądanie. Powinna zostać wyświetlona odpowiedź zwrócona przez funkcję wyświetlaną w przeglądarce.

W poniższym przykładzie pokazano odpowiedzi w przeglądarce:

![Odpowiedź funkcji w przeglądarce.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

Adres URL żądania zawiera domyślnie wymagany klucz, aby uzyskać dostęp do funkcji za pośrednictwem protokołu HTTP. 

## <a name="enable-continuous-deployment"></a>Włącz ciągłe wdrażanie

Jedną z zalet korzystania z kontenerów jest obsługa ciągłego wdrażania. Funkcje umożliwiają automatyczne wdrażanie aktualizacji po zaktualizowaniu kontenera w rejestrze. Włącz ciągłe wdrażanie za pomocą polecenia [AZ functionapp Deployment Container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) .

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

To polecenie zwraca adres URL elementu webhook wdrożenia po włączeniu ciągłego wdrażania. Możesz również użyć polecenia [AZ functionapp Deployment Container show-CD-URL](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) , aby zwrócić ten adres URL. 

Skopiuj adres URL wdrożenia i przejdź do repozytorium DockerHub, wybierz kartę **webhook** , wpisz **nazwę elementu** webhook dla elementu WEBHOOK, wklej adres URL w **adresie URL elementu webhook**, a następnie wybierz znak plus ( **+** ).

![Dodawanie elementu webhook w repozytorium DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

Po ustawieniu elementu webhook wszystkie aktualizacje połączonego obrazu w programie DockerHub spowodują pobranie i zainstalowanie najnowszego obrazu.

## <a name="enable-ssh-connections"></a>Włącz połączenia SSH

Protokół SSH umożliwia bezpieczną komunikację między kontenerem a klientem. Po włączeniu protokołu SSH można nawiązać połączenie z kontenerem za pomocą App Service Advanced Tools (kudu). Aby ułatwić łączenie się z kontenerem przy użyciu protokołu SSH, funkcje zapewniają podstawowy obraz, który ma już włączony protokół SSH. 

### <a name="change-the-base-image"></a>Zmiana obrazu podstawowego

W pliku dockerfile Dołącz ciąg `-appservice` do obrazu podstawowego w instrukcji `FROM`, która dla projektu JavaScript wygląda następująco.

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
```

Różnice w dwóch obrazach podstawowych umożliwiają nawiązywanie połączeń SSH z kontenerem. Te różnice opisano szczegółowo w [tym samouczku App Services](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

### <a name="rebuild-and-redeploy-the-image"></a>Ponowne kompilowanie i wdrażanie obrazu

W folderze głównym Uruchom ponownie polecenie [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) (tak jak wcześniej), zastępując `<docker-id>` identyfikatorem konta centrum platformy Docker. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Wypchnij zaktualizowany obraz z powrotem do narzędzia Docker Hub.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Zaktualizowany obraz zostanie wdrożony ponownie w aplikacji funkcji.

### <a name="connect-to-your-container-in-azure"></a>Nawiązywanie połączenia z kontenerem na platformie Azure

W przeglądarce przejdź do następujących punktów końcowych narzędzia Advanced Tools (kudu) `scm.` dla kontenera aplikacji funkcji, zastępując `<app_name>` nazwą aplikacji funkcji.

```
https://<app_name>.scm.azurewebsites.net/
```

Zaloguj się do konta platformy Azure, a następnie wybierz kartę **SSH** , aby utworzyć połączenie SSH z kontenerem.

Po nawiązaniu połączenia Uruchom polecenie `top`, aby wyświetlić aktualnie uruchomione procesy. 

![Najważniejsze polecenie systemu Linux działające w sesji SSH.](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-queue-storage"></a>Zapisz do magazynu kolejki

Funkcje umożliwiają łączenie usług platformy Azure i innych zasobów z funkcjami bez konieczności pisania kodu integracji. Te *powiązania*, które reprezentują zarówno dane wejściowe, jak i wyjściowe, są zadeklarowane w definicji funkcji. Dane z powiązań są przekazywane do funkcji jako parametry. *Wyzwalacz* jest specjalnym typem powiązania danych wejściowych. Chociaż funkcja ma tylko jeden wyzwalacz, może mieć wiele powiązań wejściowych i wyjściowych. Aby dowiedzieć się więcej, zobacz temat [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md).

W tej sekcji pokazano, jak zintegrować funkcję z kolejką usługi Azure Storage. Powiązanie danych wyjściowych dodawane do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce.

### <a name="download-the-function-app-settings"></a>Pobierz ustawienia aplikacji funkcji

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

### <a name="enable-extension-bundles"></a>Włącz zbiory rozszerzeń

Ze względu na to, że korzystasz z powiązania danych wyjściowych usługi queue storage, przed uruchomieniem projektu musisz mieć zainstalowane rozszerzenie powiązania magazynu. 

#### <a name="javascriptpython"></a>JavaScript/Python

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

#### <a name="c-class-library"></a>C#Biblioteka klas

Z wyjątkiem wyzwalaczy HTTP i Timer, powiązania są implementowane jako pakiety rozszerzeń. Uruchom następujące polecenie [dotnet Add Package](/dotnet/core/tools/dotnet-add-package) w oknie terminalu, aby dodać pakiet rozszerzenia magazynu do projektu.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

> [!TIP]
> W przypadku korzystania z programu Visual Studio można także dodać ten pakiet za pomocą Menedżera pakietów NuGet.

Teraz można dodać powiązanie danych wyjściowych magazynu do projektu.

### <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

W funkcjach każdy typ powiązania wymaga `direction`, `type` i unikatowych `name` do zdefiniowania w pliku Function. JSON. Sposób definiowania tych atrybutów zależy od języka aplikacji funkcji.

####  <a name="javascriptpython"></a>JavaScript/Python

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

#### <a name="c-class-library"></a>C#Biblioteka klas

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

### <a name="add-code-that-uses-the-output-binding"></a>Dodaj kod, który używa powiązania danych wyjściowych

Po zdefiniowaniu powiązania można użyć `name` powiązania, aby uzyskać do niego dostęp jako atrybut w sygnaturze funkcji. Za pomocą powiązania danych wyjściowych nie trzeba używać kodu zestawu SDK usługi Azure Storage do uwierzytelniania, uzyskiwania odwołania do kolejki lub zapisywania danych. Te zadania są wykonywane za pomocą środowiska uruchomieniowego usługi Functions i powiązania danych wyjściowych kolejki.

#### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

#### <a name="python"></a>Python

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

#### <a name="c-class-library"></a>C#Biblioteka klas

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

### <a name="update-the-hosted-container"></a>Aktualizowanie hostowanego kontenera

W folderze głównym ponownie uruchom polecenie [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) , a następnie zaktualizuj wersję w znaczniku do `v1.0.2`. Tak jak wcześniej, Zastąp `<docker-id>` IDENTYFIKATORem konta centrum Docker. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Wypchnij zaktualizowany obraz z powrotem do repozytorium.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

### <a name="verify-the-updates-in-azure"></a>Weryfikowanie aktualizacji na platformie Azure

Użyj tego samego adresu URL, co wcześniej w przeglądarce, aby wyzwolić funkcję. Powinna zostać wyświetlona taka sama odpowiedź. Jednak w tym czasie ciąg przekazywany jako parametr `name` jest zapisywana w kolejce magazynu `outqueue`.

### <a name="set-the-storage-account-connection"></a>Ustawianie połączenia konta magazynu

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

### <a name="query-the-storage-queue"></a>Wykonywanie zapytań względem kolejki magazynu

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

Teraz, po pomyślnym wdrożeniu niestandardowego kontenera do aplikacji funkcji na platformie Azure, weź pod uwagę więcej informacji na temat następujących tematów:

+ [Funkcje monitorowania](functions-monitoring.md)
+ [Opcje skalowania i hostowania](functions-scale.md)
+ [Hosting bezserwerowy oparty na Kubernetes](functions-kubernetes-keda.md)

[Azure Portal]: https://portal.azure.com