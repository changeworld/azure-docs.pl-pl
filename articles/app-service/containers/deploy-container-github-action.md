---
title: Wdrażanie kontenera z poziomu potoku ciągłej integracji/ciągłego wdrażania za pomocą akcji GitHub — Azure App Service | Microsoft Docs
description: Dowiedz się, jak wdrożyć kontener do App Service przy użyciu akcji usługi GitHub
services: app-service
documentationcenter: ''
author: jasonfreeberg
writer: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2019
ms.author: jafreebe
ms.openlocfilehash: 2341eba2c24c06d654c9d2eeda96788d168fe27c
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809814"
---
# <a name="github-actions-for-deploying-to-web-app-for-containers"></a>Akcje usługi GitHub dotyczące wdrażania w usłudze Web App for Containers

Dzięki [akcjom GitHub](https://help.github.com/en/articles/about-github-actions) można tworzyć zautomatyzowane przepływy pracy tworzenia oprogramowania. Za pomocą Azure App Service działań w witrynie GitHub można zautomatyzować przepływ pracy w celu wdrożenia [usługi Azure Web Apps dla kontenerów](https://azure.microsoft.com/services/app-service/containers/) za pomocą akcji GitHub.

> [!IMPORTANT]
> Akcje usługi GitHub są obecnie dostępne w wersji beta. Musisz najpierw [utworzyć konto, aby dołączyć do wersji zapoznawczej](https://github.com/features/actions) przy użyciu konta usługi GitHub.
> 

Przepływ pracy jest definiowany przez plik YAML (. yml) w ścieżce `/.github/workflows/` w repozytorium. Ta definicja zawiera różne kroki i parametry wchodzące w skład przepływu pracy.

W przypadku przepływu pracy kontenera aplikacji sieci Web platformy Azure plik ma trzy sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Uwierzytelnianie** | 1. Zdefiniuj nazwę główną usługi <br /> 2. Utwórz wpis tajny usługi GitHub |
|**Utworzenia** | 1. Skonfiguruj środowisko <br /> 2. Tworzenie obrazu kontenera |
|**Wdrażanie** | 1. Wdróż obraz kontenera |

## <a name="create-a-service-principal"></a>Tworzenie jednostki usługi

[Nazwę główną usługi](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) można utworzyć przy użyciu polecenia [AZ AD Sp Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) w [interfejsie użytkownika platformy Azure](https://docs.microsoft.com/cli/azure/). Można uruchomić to polecenie przy użyciu [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając przycisk **Wypróbuj** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

W tym przykładzie Zastąp symbole zastępcze w zasobie IDENTYFIKATORem subskrypcji, grupą zasobów i nazwą aplikacji sieci Web. Dane wyjściowe to poświadczenia przypisania roli, które zapewniają dostęp do aplikacji sieci Web. Skopiuj ten obiekt JSON, którego możesz użyć do uwierzytelniania z usługi GitHub.

> [!NOTE]
> Nie trzeba tworzyć jednostki usługi, jeśli zdecydujesz się na użycie profilu publikowania na potrzeby uwierzytelniania.

> [!IMPORTANT]
> Zawsze dobrym sposobem jest przyznanie minimalnego dostępu. Dlatego zakres w poprzednim przykładzie jest ograniczony do określonej aplikacji sieci Web, a nie całej grupy zasobów.

## <a name="configure-the-github-secret"></a>Konfigurowanie wpisu tajnego usługi GitHub

Poniższy przykład używa poświadczeń na poziomie użytkownika, np. nazwy głównej usługi platformy Azure do wdrożenia. Postępuj zgodnie z instrukcjami, aby skonfigurować klucz tajny:

1. W witrynie [GitHub](https://github.com/)Przejrzyj repozytorium, wybierz pozycję **Ustawienia > wpisy tajne > Dodaj nowe hasło**

2. Wklej zawartość poniższego `az cli` polecenia jako wartość zmiennej tajnej. Na przykład `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Teraz w pliku przepływu pracy w gałęzi: `.github/workflows/workflow.yml` Zastąp klucz tajny w akcji logowania platformy Azure przy użyciu klucza tajnego.

4. Podobnie należy zdefiniować następujące dodatkowe wpisy tajne dla poświadczeń rejestru kontenera i ustawić je w akcji logowania Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Zostaną wyświetlone wpisy tajne, jak pokazano poniżej.

    ![klucze tajne kontenera](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>Tworzenie obrazu kontenera

Poniższy przykład przedstawia część przepływu pracy, który kompiluje obraz platformy Docker.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/actions/login@v1
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

## <a name="deploy-to-web-app-container"></a>Wdróż w kontenerze aplikacji sieci Web

Aby wdrożyć obraz do kontenera aplikacji sieci Web, należy użyć akcji `Azure/appservice-actions/webapp@master`. Ta akcja ma 5 parametrów:

| **Konstruktora**  | **Wyjaśnienie**  |
|---------|---------|
| **Nazwa aplikacji** | Potrzeb Nazwa aplikacji sieci Web platformy Azure | 
| **Nazwa gniazda** | Obowiązkowe Wprowadź istniejące miejsce poza miejscem produkcyjnym |
| **rastrow** | Potrzeb Określ w pełni kwalifikowaną nazwę obrazu kontenera. Na przykład "myregistry.azurecr.io/nginx:latest" lub "Python: 3.7.2-Alpine/". W przypadku scenariusza z wieloma kontenerami można podać wiele nazw obrazów kontenerów (rozdzielonych w wielu wierszach) |
| **plik konfiguracji** | Obowiązkowe Ścieżka pliku z systemem Docker — tworzenie. Powinna być w pełni kwalifikowana ścieżka lub odnosząca się do domyślnego katalogu roboczego. Wymagane dla scenariusza z obsługą kontenera |
| **Container — polecenie** | Obowiązkowe Wprowadź polecenie Start up. Na przykład uruchomienie dotnet lub NazwaPliku filename. dll |

Poniżej znajduje się przykładowy przepływ pracy do kompilowania i wdrażania aplikacji sieci Web w języku Node. js do kontenera aplikacji sieci Web platformy Azure.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/actions/login@v1
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

Zestaw akcji można znaleźć w różnych repozytoriach w usłudze GitHub, z których każda zawiera dokumentację i przykłady ułatwiające korzystanie z usługi GitHub w przypadku ciągłej integracji/ciągłego wdrażania oraz wdrażanie aplikacji na platformie Azure.

- [Logowanie do platformy Azure](https://github.com/Azure/actions)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Usługa Azure WebApp dla kontenerów](https://github.com/Azure/webapps-container-deploy)

- [Logowanie/wylogowywanie platformy Docker](https://github.com/Azure/docker-login)

- [Zdarzenia wyzwalające przepływy pracy](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s wdrażanie](https://github.com/Azure/k8s-deploy)

- [Początkowe przepływy pracy](https://github.com/actions/starter-workflows)
