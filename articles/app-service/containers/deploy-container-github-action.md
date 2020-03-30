---
title: Niestandardowy kontener CI/CD z akcji github
description: Dowiedz się, jak używać akcji GitHub do wdrażania niestandardowego kontenera systemu Linux w usłudze App Service z potoku ciągłej integracji/ciągłego wdrażania.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: d5f175d887cec1d5b5e567d3f716e6492f4516dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246969"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Wdrażanie kontenera niestandardowego w usłudze App Service przy użyciu akcji usługi GitHub

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) zapewnia elastyczność tworzenia zautomatyzowanego przepływu pracy cyklu życia tworzenia oprogramowania. Dzięki [akcji usługi Azure App Service dla kontenerów](https://github.com/Azure/webapps-container-deploy)można zautomatyzować przepływ pracy w celu wdrożenia aplikacji jako [kontenerów niestandardowych w usłudze App Service](https://azure.microsoft.com/services/app-service/containers/) przy użyciu akcji Usługi GitHub.

> [!IMPORTANT]
> GitHub Actions jest obecnie w wersji beta. Musisz najpierw [zarejestrować się, aby dołączyć do wersji zapoznawczej](https://github.com/features/actions) przy użyciu konta GitHub.
> 

Przepływ pracy jest definiowany przez plik YAML (.yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry, które składają się na przepływ pracy.

W przypadku przepływu pracy kontenera usługi Azure App Service plik ma trzy sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Uwierzytelnianie** | 1. Zdefiniuj jednostkę usługi. <br /> 2. Utwórz klucz tajny GitHub. |
|**Kompilacja** | 1. Skonfiguruj środowisko. <br /> 2. Zbuduj obraz kontenera. |
|**Wdróż** | 1. Wdrażanie obrazu kontenera. |

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

[Jednostkę usługi](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) można utworzyć za pomocą polecenia [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) w [usłudze Azure CLI](https://docs.microsoft.com/cli/azure/). To polecenie można uruchomić przy użyciu [usługi Azure Cloud Shell](https://shell.azure.com/) w witrynie Azure portal lub wybierając przycisk **Wypróbuj.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
# Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

Dane wyjściowe to obiekt JSON z poświadczeniami przypisania roli, które zapewniają dostęp do aplikacji usługi App Service podobną do poniższej. Skopiuj ten obiekt JSON do uwierzytelniania z usługi GitHub.

 ```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Zawsze dobrą praktyką jest przyznanie minimalnego dostępu. Można ograniczyć zakres w powyższym poleceniu interfejsu wiersza polecenia Az do określonej aplikacji usługi App Service i rejestru kontenerów platformy Azure, do którego są wypychane obrazy kontenerów.

## <a name="configure-the-github-secret"></a>Konfigurowanie klucza tajnego usługi GitHub

W poniższym przykładzie użyto poświadczeń na poziomie użytkownika, tj. Wykonaj kroki, aby skonfigurować klucz tajny:

1. W [usłudze GitHub](https://github.com/)przeglądaj repozytorium, wybierz **ustawienia > wpisowe > Dodaj nowy klucz tajny**

2. Wklej zawartość polecenia `az cli` poniżej jako wartość zmiennej tajnej. Na przykład `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Teraz w pliku przepływu pracy `.github/workflows/workflow.yml` w gałęzi: zastąp klucz tajny w akcji logowania platformy Azure kluczem tajnym.

4. Podobnie zdefiniuj następujące dodatkowe wpisy tajne dla poświadczeń rejestru kontenerów i ustaw je w akcji logowania platformy Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Zobaczysz wpisy tajne, jak pokazano poniżej po zdefiniowaniu.

    ![tajemnice kontenerów](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>Tworzenie obrazu kontenera

W poniższym przykładzie pokazano część przepływu pracy, który tworzy obraz docker.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>Wdrażanie w kontenerze usługi app service

Aby wdrożyć obraz w kontenerze niestandardowym `azure/webapps-container-deploy@v1` w usłudze App Service, użyj tej akcji. Ta akcja ma pięć parametrów:

| **Parametr**  | **Wyjaśnienie**  |
|---------|---------|
| **nazwa aplikacji** | (Wymagane) Nazwa aplikacji Usługi aplikacji | 
| **nazwa gniazda** | (Opcjonalnie) Wprowadź istniejące gniazdo inne niż gniazdo produkcyjne |
| **Obrazów** | (Wymagane) Określ w pełni kwalifikowaną nazwę obrazów kontenerów. Na przykład 'myregistry.azurecr.io/nginx:latest' lub 'python:3.7.2-alpine/'. W przypadku aplikacji z wieloma kontenerami można podać wiele nazw obrazów kontenerów (oddzielone między wierszami) |
| **plik konfiguracyjny** | (Opcjonalnie) Ścieżka pliku Docker-Compose. Powinna być w pełni kwalifikowana ścieżka lub względem domyślnego katalogu roboczego. Wymagane dla aplikacji z wieloma kontenerami. |
| **container-polecenie** | (Opcjonalnie) Wprowadź polecenie uruchamiania. Dla np. dotnet run lub dotnet filename.dll |

Poniżej znajduje się przykładowy przepływ pracy do tworzenia i wdrażania aplikacji Node.js do kontenera niestandardowego w usłudze App Service.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>Następne kroki

Możesz znaleźć nasz zestaw akcji pogrupowanych w różne repozytoria w usłudze GitHub, z których każdy zawiera dokumentację i przykłady ułatwiające korzystanie z usługi GitHub dla ciągłej integracji/ciągłego wdrażania i wdrażanie aplikacji na platformie Azure.

- [Logowanie w usłudze Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Usługa Azure WebApp dla kontenerów](https://github.com/Azure/webapps-container-deploy)

- [Logowanie/wylogowywki platformy Docker](https://github.com/Azure/docker-login)

- [Zdarzenia wyzwalające przepływy pracy](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s wdrożyć](https://github.com/Azure/k8s-deploy)

- [Starter CI Przepływy pracy](https://github.com/actions/starter-workflows)

- [Początkowe przepływy pracy do wdrożenia na platformie Azure](https://github.com/Azure/actions-workflow-samples)
