---
title: Wiosenna wiosenna integracja/dyskI CD usługi Azure w chmurze z działaniami github
description: Jak utworzyć przepływ pracy ciągłej integracji/ciągłego wdrażania dla usługi Azure Spring Cloud za pomocą akcji Usługi GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.openlocfilehash: 559c894a2212466761de820de7486ae203337802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538468"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Wiosenna wiosenna integracja/dyskI CD usługi Azure w chmurze z działaniami github

Akcje GitHub obsługują zautomatyzowany przepływ pracy cyklu tworzenia oprogramowania. Dzięki akcji GitHub dla usługi Azure Spring Cloud możesz tworzyć przepływy pracy w repozytorium do tworzenia, testowania, pakowania, wydawania i wdrażania na platformie Azure. 

## <a name="prerequisites"></a>Wymagania wstępne
W tym przykładzie wymaga [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="set-up-github-repository-and-authenticate"></a>Konfigurowanie repozytorium GitHub i uwierzytelnienie
Do autoryzacji akcji logowania platformy Azure potrzebne są poświadczenia zasad usługi platformy Azure. Aby uzyskać poświadczenia platformy Azure, wykonaj następujące polecenia na komputerze lokalnym:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Aby uzyskać dostęp do określonej grupy zasobów, można zmniejszyć zakres:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
Polecenie powinno wyprowadzić obiekt JSON:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

W tym przykładzie użyto [próbki Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) w usłudze GitHub.  Rozwiń przykład, otwórz stronę repozytorium GitHub i kliknij kartę **Ustawienia.** Otwórz menu **Wpisy** i kliknij pozycję **Dodaj nowy klucz tajny:**

 ![Dodaj nowy klucz tajny](./media/github-actions/actions1.png)

Ustaw nazwę tajnego `AZURE_CREDENTIALS` i jej wartość na ciąg JSON znaleziony pod nagłówkiem *Konfigurowanie repozytorium GitHub i uwierzytelnienie*.

 ![Ustawianie tajnych danych](./media/github-actions/actions2.png)

Poświadczenia logowania platformy Azure można również uzyskać z usługi Key Vault w akcjach Usługi GitHub, jak wyjaśniono w obszarze [Uwierzytelnij wiosnę platformy Azure za pomocą usługi Key Vault w akcjach Usługi GitHub.](./spring-cloud-github-actions-key-vault.md)

## <a name="provision-service-instance"></a>Wystąpienie usługi świadczenia usług
Aby aprowizować wystąpienie usługi Azure Spring Cloud, uruchom następujące polecenia przy użyciu interfejsu wiersza polecenia platformy Azure.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Tworzenie przepływu pracy
Przepływ pracy jest definiowany przy użyciu następujących opcji.

### <a name="prepare-for-deployment-with-azure-cli"></a>Przygotowanie do wdrożenia za pomocą interfejsu wiersza polecenia platformy Azure
Polecenie `az spring-cloud app create` nie jest obecnie idempotentne.  Zalecamy ten przepływ pracy w istniejących aplikacjach i wystąpieniach usługi Azure Spring Cloud.

Użyj następujących poleceń interfejsu wiersza polecenia platformy Azure do przygotowania:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Wdrażanie bezpośrednio za pomocą interfejsu wiersza polecenia platformy Azure
Utwórz `.github/workflow/main.yml` plik w repozytorium:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Wdrażanie za pomocą akcji interfejsu wiersza polecenia platformy Azure
Polecenie az `run` użyje najnowszej wersji interfejsu wiersza polecenia platformy Azure. Jeśli występują przełomowe zmiany, można również użyć określonej wersji `action`interfejsu wiersza polecenia platformy Azure z platformą azure/CLI . 

> [!Note] 
> To polecenie będzie działać w `env` nowym kontenerze, więc nie będzie działać, a dostęp do plików akcji krzyżowych może mieć dodatkowe ograniczenia.

Utwórz plik .github/workflow/main.yml w repozytorium:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Wdrażanie za pomocą wtyczki Maven
Inną opcją jest użycie [wtyczki Maven](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven) do wdrażania jara i aktualizowania ustawień aplikacji. Polecenie `mvn azure-spring-cloud:deploy` jest idempotentne i automatycznie utworzy aplikacje w razie potrzeby. Nie musisz tworzyć odpowiednich aplikacji z wyprzedzeniem.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

## <a name="run-the-workflow"></a>Uruchamianie przepływu pracy
**Akcje** usługi GitHub powinny być włączane automatycznie po wypchnięciu `.github/workflow/main.yml` do usługi GitHub. Akcja zostanie wyzwolona podczas wypychania nowego zatwierdzenia. Jeśli utworzysz ten plik w przeglądarce, akcja powinna być już prowadzona.

Aby sprawdzić, czy akcja została włączona, kliknij kartę **Akcje** na stronie repozytorium Usługi GitHub:

 ![Weryfikowanie włączonej akcji](./media/github-actions/actions3.png)

Jeśli akcja jest uruchamiana w błąd, na przykład jeśli nie ustawiono poświadczeń platformy Azure, można ponownie uruchomić kontrole po naprawieniu błędu. Na stronie repozytorium GitHub kliknij pozycję **Akcje**, wybierz konkretne zadanie przepływu pracy, a następnie kliknij przycisk **Uruchom ponownie,** aby ponownie uruchomić czeki:

 ![Ponowne sprawdzanie](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Następne kroki
* [Akcje programu Key Vault dla wiosennej chmury GitHub](./spring-cloud-github-actions-key-vault.md)
* [Podmioty usługi Azure Active Directory](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [Akcje usługi GitHub dla platformy Azure](https://github.com/Azure/actions/)
