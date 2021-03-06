---
title: Wdrażanie w usłudze Azure Functions przy użyciu dodatku Azure Functions w usłudze Jenkins
description: Dowiedz się, jak wdrożyć usługi Azure Functions przy użyciu dodatku Jenkins Azure Functions
keywords: jenkins, azure, metodyki devops, java, usługę azure functions
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 731bac13a596bbeaf970b3f6ce976a582d1f11ae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78250922"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plug-in"></a>Wdrażanie w usłudze Azure Functions przy użyciu dodatku Azure Functions w usłudze Jenkins

[Azure Functions](/azure/azure-functions/) to usługa obliczeniowa niewymagająca użycia serwera. Dzięki usłudze Azure Functions możesz uruchamiać kod na żądanie bez konieczności aprowizowania infrastruktury lub zarządzania nią. W tym samouczku pokazano, jak wdrożyć funkcję Java w usłudze Azure Functions przy użyciu wtyczki Usługi Azure Functions.

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- **Serwer usługi Jenkins:** Jeśli nie masz zainstalowanego serwera Jenkins, zapoznaj się z tym [artykułem Utwórz serwer usługi Jenkins na platformie Azure](./install-jenkins-solution-template.md).

  > [!TIP]
  > Kod źródłowy użyty w tym samouczku znajduje się w [repozytorium GitHub Visual Studio China](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java).

## <a name="create-a-java-function"></a>Tworzenie funkcji w języku Java

Funkcję w języku Java z użyciem stosu środowiska uruchomieniowego w języku Java można utworzyć w witrynie [Azure Portal](https://portal.azure.com) lub za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/?view=azure-cli-latest).

W poniższych krokach pokazano, w jaki sposób utworzyć funkcję w języku Java przy użyciu interfejsu wiersza polecenia platformy Azure:

1. Utwórz grupę zasobów, zastępując ** &lt;resource_group>** nazwą grupy zasobów.

    ```azurecli
    az group create --name <resource_group> --location eastus
    ```

1. Utwórz konto magazynu platformy Azure, zastępując symbole zastępcze odpowiednimi wartościami.
 
    ```azurecli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Utwórz testową aplikację funkcji, zastępując symbole zastępcze odpowiednimi wartościami.

    ```azurecli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```

## <a name="prepare-jenkins-server"></a>Przygotowanie serwera Jenkins

W poniższych krokach wyjaśniono, jak przygotować serwer Jenkins:

1. Wdróż [serwer Jenkins](https://aka.ms/jenkins-on-azure) na platformie Azure. Jeśli nie masz jeszcze zainstalowanego wystąpienia serwera Jenkins, ten proces opisano w artykule [Tworzenie serwera Jenkins na platformie Azure](./install-jenkins-solution-template.md).

1. Zaloguj się do wystąpienia serwera Jenkins przy użyciu protokołu SSH.

1. W wystąpieniu serwera Jenkins zainstaluj narzędzie maven za pomocą następującego polecenia:

    ```terminal
    sudo apt install -y maven
    ```

1. W wystąpieniu serwera Jenkins zainstaluj zestaw narzędzi [Azure Functions Core Tools](/azure/azure-functions/functions-run-local), wydając następujące polecenia po pojawieniu się monitu terminala:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. Na pulpicie nawigacyjnym serwera Jenkins zainstaluj następujące wtyczki:

    - Wtyczka usługi Azure Functions
    - Wtyczka EnvInject

1. Serwer Jenkins wymaga jednostki usługi platformy Azure w celu uwierzytelniania zasobów platformy Azure i uzyskania do nich dostępu. Zapoznaj się z artykułem [Deploy to Azure App Service (Wdrażanie w usłudze Azure App Service)](./tutorial-jenkins-deploy-web-app-azure-app-service.md), aby uzyskać instrukcje krok po kroku.

1. Na serwerze Jenkins dodaj poświadczenia jako typ „Microsoft Azure Service Principal” („Jednostka usługi Microsoft Azure”), korzystając z jednostki usługi platformy Azure. Zapoznaj się samouczkiem [Deploy to Azure App Service (Wdrażanie w usłudze Azure App Service)](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins).

## <a name="fork-the-sample-github-repo"></a>Rozwidli przykładowe repozytorium GitHub

1. [Zaloguj się do repozytorium GitHub dla aplikacji nieparzystej lub nawet przykładowej](https://github.com/VSChina/odd-or-even-function.git).

1. W prawym górnym rogu ekranu usługi GitHub wybierz pozycję **Fork** (Utwórz rozwidlenie).

1. Postępuj zgodnie z monitami, aby wybrać konto usługi GitHub i zakończyć tworzenie rozwidlenia.

## <a name="create-a-jenkins-pipeline"></a>Tworzenie potoku serwera Jenkins

W tej sekcji utworzysz [potok serwera Jenkins](https://jenkins.io/doc/book/pipeline/).

1. Na pulpicie nawigacyjnym serwera Jenkins utwórz potok.

1. Włącz ustawienie **Prepare an environment for the run** (Przygotuj środowisko dla uruchomienia).

1. Dodaj następujące zmienne środowiskowe w polu **Properties Content** (Zawartość właściwości), zastępując symbole zastępcze odpowiednimi wartościami dla Twojego środowiska:

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. W sekcji **Pipeline->Definition** (Potok > Definicja) wybierz pozycję **Pipeline script from SCM** (Skrypt potoku z narzędzia SCM).

1. Wprowadź adres URL i ścieżkę skryptu rozwidlenia GitHub ("doc/resources/jenkins/JenkinsFile"), aby użyć go w [przykładzie JenkinsFile](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile).

   ```
   node {
    stage('Init') {
        checkout scm
        }

    stage('Build') {
        sh 'mvn clean package'
        }

    stage('Publish') {
        azureFunctionAppPublish appName: env.FUNCTION_NAME, 
                                azureCredentialsId: env.AZURE_CRED_ID, 
                                filePath: '**/*.json,**/*.jar,bin/*,HttpTrigger-Java/*', 
                                resourceGroup: env.RES_GROUP, 
                                sourceDirectory: 'target/azure-functions/odd-or-even-function-sample'
        }
    }
    ```

## <a name="build-and-deploy"></a>Tworzenie i wdrażanie

Czas na uruchomienie zadania serwera Jenkins.

1. Najpierw należy uzyskać klucz autoryzacji za pomocą instrukcji zawartych w artykule [Azure Functions HTTP triggers and bindings (Wyzwalacze i powiązania protokołu HTTP w usłudze Azure Functions)](/azure/azure-functions/functions-bindings-http-webhook-trigger#authorization-keys).

1. W przeglądarce wprowadź adres URL aplikacji. Zastąp symbole zastępcze odpowiednimi wartościami ** &lt;** i określ wartość liczbową dla input_number>jako dane wejściowe dla funkcji Java.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. Zostaną wyświetlone wyniki podobne do następujących przykładowych danych wyjściowych (w przykładzie użyto liczby nieparzystej, 365):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie planujesz już korzystać z tej aplikacji, usuń utworzone zasoby, wykonując następujące czynności:

```azurecli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o usłudze Azure Functions, zobacz następujący zasób:
> [!div class="nextstepaction"]
> [Dokumentacja usługi Azure Functions](/azure/azure-functions/)
