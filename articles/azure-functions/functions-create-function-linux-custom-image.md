---
title: Tworzenie funkcji platformy Azure w systemie Linux przy użyciu niestandardowego obrazu
description: Informacje o sposobie tworzenia usługi Azure Functions działającej w niestandardowym obrazie systemu Linux.
ms.date: 03/30/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 44ca8f721967b90be283f867f8656344ec3f1906
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673411"
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

Możesz wykonać ten samouczek na dowolnym komputerze z systemem Windows, macOS lub Linux. Ukończenie samouczka spowoduje poniesienie kosztów w wysokości kilku usd na koncie platformy Azure.

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ Identyfikator [platformy Docker](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Uruchom, `docker login` aby zalogować się do platformy Docker. To polecenie kończy się niepowodzeniem, jeśli docker nie jest uruchomiony, w którym to przypadku uruchomić dokowane i ponowić próbę polecenia.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Tworzenie i testowanie projektu funkcji lokalnych

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
W wierszu polecenia terminala lub polecenia uruchom następujące polecenie dla wybranego języka, `LocalFunctionsProject`aby utworzyć projekt aplikacji funkcji w folderze o nazwie .  
::: zone-end  
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
::: zone pivot="programming-language-java"  
W pustym folderze uruchom następujące polecenie, aby wygenerować projekt usługi Functions z [archetypu narzędzia Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-Ddocker"
```
---

Maven prosi o wartości potrzebne do zakończenia generowania projektu podczas wdrażania.   
Po wyświetleniu monitu podaj następujące wartości:

| Monit | Wartość | Opis |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Wartość, która jednoznacznie identyfikuje projekt we wszystkich projektach, zgodnie z [regułami nazewnictwa pakietów](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) dla języka Java. |
| **artifactId (ida artefaktu)** | `fabrikam-functions` | Wartość, która jest nazwą słoika, bez numeru wersji. |
| **Wersja** | `1.0-SNAPSHOT` | Wybierz wartość domyślną. |
| **Pakiet** | `com.fabrikam.functions` | Wartość, która jest pakietem Java dla wygenerowanego kodu funkcji. Użyj wartości domyślnej. |

Wpisz `Y` lub naciśnij klawisz Enter, aby potwierdzić.

Maven tworzy pliki projektu w nowym folderze o nazwie _artifactId_, który w tym przykładzie jest `fabrikam-functions`. 
::: zone-end
Opcja `--docker` generuje `Dockerfile` dla projektu, który definiuje odpowiedni kontener niestandardowy do użytku z usługą Azure Functions i wybranego środowiska wykonawczego.

Przejdź do folderu projektu:
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Dodaj funkcję do projektu za pomocą następującego `--name` polecenia, gdzie argument jest `--template` unikatową nazwą funkcji, a argument określa wyzwalacz funkcji. `func new`utworzyć podfolder pasujący do nazwy funkcji zawierającej plik kodu odpowiedni dla wybranego języka projektu i plik konfiguracyjny o nazwie *function.json*.

```
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end  
Aby przetestować funkcję lokalnie, uruchom lokalny host środowiska uruchomieniowego usługi Azure Functions w katalogu głównym folderu projektu: 
::: zone pivot="programming-language-csharp"  
```
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
mvn clean package  
mvn azure-functions:run
```
::: zone-end
Po wyświetleniu `HttpExample` punktu końcowego na wyjściu przejdź do pozycji [`http://localhost:7071/api/HttpExample?name=Functions`](http://localhost:7071/api/HttpExample?name=Functions). Przeglądarka powinna wyświetlać komunikat "hello", `Functions`który odbija się `name` echem , wartość dostarczoną do parametru zapytania.

Użyj **klawisza Ctrl**-**C,** aby zatrzymać hosta.

## <a name="build-the-container-image-and-test-locally"></a>Tworzenie obrazu kontenera i testowanie lokalnie

(Opcjonalnie) Sprawdź *Dockerfile" w katalogu głównym folderu projektu. Dockerfile opisuje wymagane środowisko do uruchomienia aplikacji funkcji w systemie Linux.  Pełną listę obsługiwanych obrazów podstawowych dla usługi Azure Functions można znaleźć na [stronie obrazu podstawowego usługi Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).
    
W głównym folderze projektu uruchom polecenie [kompilacji platformy docker](https://docs.docker.com/engine/reference/commandline/build/) i podaj nazwę i `azurefunctionsimage`tag, `v1.0.0`. Zastąp ciąg `<DOCKER_ID>` identyfikatorem konta usługi Docker Hub. To polecenie powoduje skompilowanie obrazu platformy Docker dla kontenera.

```
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Po zakończeniu polecenia można uruchomić nowy kontener lokalnie.
    
Aby przetestować kompilację, uruchom obraz w kontenerze lokalnym za `<DOCKER_ID` pomocą polecenia [uruchamiania platformy docker,](https://docs.docker.com/engine/reference/commandline/run/) zastępując ponownie identyfikator platformy Docker i dodając argument portów,: `-p 8080:80`

```
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Gdy obraz jest uruchomiony w kontenerze lokalnym, otwórz przeglądarkę, aby , który powinien wyświetlić `http://localhost:8080`obraz zastępczy pokazany poniżej. Obraz pojawia się w tym momencie, ponieważ funkcja jest uruchomiona w kontenerze lokalnym, tak jak na platformie Azure, co `"authLevel": "function"` oznacza, że jest chroniony przez klucz dostępu zdefiniowany w *function.json* z właściwością. Kontener nie został jeszcze opublikowany w aplikacji funkcji na platformie Azure, więc klucz nie jest jeszcze dostępny. Jeśli chcesz przetestować dla kontenera lokalnego, zatrzymaj `"authLevel": "anonymous"`dokceny, zmień właściwość autoryzacji na , odbuduj obraz i uruchom ponownie dokowane. Następnie `"authLevel": "function"` zresetuj w *function.json*. Aby uzyskać więcej informacji, zobacz [klucze autoryzacji](functions-bindings-http-webhook-trigger.md#authorization-keys).

![Obraz zastępczy wskazujący, że kontener działa lokalnie](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
Gdy obraz jest uruchomiony w kontenerze [`http://localhost:8080/api/HttpExample?name=Functions`](http://localhost:8080/api/HttpExample?name=Functions)lokalnym, przejdź do , który powinien wyświetlać taką samą wiadomość "hello", jak poprzednio. Ponieważ archetyp Maven generuje funkcję wyzwalaną http, która używa autoryzacji anonimowej, nadal można wywołać funkcję, nawet jeśli jest uruchomiona w kontenerze. 
::: zone-end  

Po zweryfikowaniu aplikacji funkcji w kontenerze zatrzymaj **Ctrl**+**C**dok.

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

1. Zaloguj się do konta platformy Azure, a następnie wybierz **SSH,** aby ustanowić połączenie z kontenerem. Łączenie może potrwać kilka chwil, jeśli platforma Azure nadal aktualizuje obraz kontenera.

1. Po nawiązaniu połączenia z kontenerem `top` uruchom polecenie, aby wyświetlić aktualnie uruchomione procesy. 

    ![Najlepsze polecenie systemu Linux uruchomione w sesji SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Pisanie w kolejce usługi Azure Storage

Usługa Azure Functions umożliwia łączenie funkcji z innymi usługami platformy Azure i zasobami, które muszą pisać własny kod integracji. Te *powiązania*, które reprezentują zarówno dane wejściowe, jak i wyjściowe, są zadeklarowane w definicji funkcji. Dane z powiązań są podawane do funkcji jako parametry. *Wyzwalacz* jest specjalnym typem powiązania wejściowego. Chociaż funkcja ma tylko jeden wyzwalacz, może mieć wiele powiązań wejściowych i wyjściowych. Aby dowiedzieć się więcej, zobacz [Koncepcje wyzwalaczy i powiązań usług Azure Functions](functions-triggers-bindings.md).

W tej sekcji pokazano, jak zintegrować funkcję z kolejką usługi Azure Storage. Powiązanie danych wyjściowych, które można dodać do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

## <a name="add-code-to-use-the-output-binding"></a>Dodaj kod, aby użyć powiązania danych wyjściowych

Po zdefiniowaniu powiązania kolejki można teraz zaktualizować `msg` funkcję, aby odbierać parametr wyjściowy i zapisywać komunikaty do kolejki.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
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

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

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
