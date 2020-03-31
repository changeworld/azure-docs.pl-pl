---
title: Tworzenie, testowanie i wdrażanie kontenerów w usłudze Azure Kubernetes przy użyciu akcji Usługi GitHub
description: Dowiedz się, jak używać akcji usługi GitHub do wdrażania kontenera w usłudze Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 5ee8ee4d2c9e225d82e58daffeef9e5f09e43e6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595369"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Akcje usługi GitHub do wdrażania w usłudze Kubernetes

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) zapewnia elastyczność tworzenia zautomatyzowanego przepływu pracy cyklu życia tworzenia oprogramowania. Akcja [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) Kubernetes ułatwia wdrażanie klastrów usługi Azure Kubernetes. Akcja ustawia docelowy kontekst klastra AKS, który może być używany przez inne akcje, takie jak [azure/k8s-deploy,](https://github.com/Azure/k8s-deploy/tree/master) [azure/k8s-create-secret](https://github.com/Azure/k8s-create-secret/tree/master) itp.

Przepływ pracy jest definiowany przez plik YAML (.yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry, które składają się na przepływ pracy.

W przypadku przepływu pracy kierowanego na system AKS plik składa się z trzech sekcji:

|Sekcja  |Zadania  |
|---------|---------|
|**Uwierzytelnianie** | Logowanie do rejestru kontenerów prywatnych (ACR) |
|**Kompilacja** | Tworzenie & wypychanie obrazu kontenera  |
|**Wdróż** | 1. Ustawianie docelowego klastra AKS |
| |2. Tworzenie ogólnego/docker-registry klucz tajny w klastrze Kubernetes  |
||3. Wdrażanie w klastrze Kubernetes|

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

[Jednostkę usługi](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) można utworzyć za pomocą polecenia [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) w [usłudze Azure CLI](https://docs.microsoft.com/cli/azure/). To polecenie można uruchomić przy użyciu [usługi Azure Cloud Shell](https://shell.azure.com/) w witrynie Azure portal lub wybierając przycisk **Wypróbuj.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

W powyższym poleceniu zastąp symbole zastępcze identyfikatorem subskrypcji i grupą zasobów. Dane wyjściowe to poświadczenia przypisania roli, które zapewniają dostęp do zasobu. Polecenie powinno wyprowadzić obiekt JSON podobny do tego.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Skopiuj ten obiekt JSON, którego można użyć do uwierzytelniania z usługi GitHub.

## <a name="configure-the-github-secrets"></a>Konfigurowanie wpisów tajnych usługi GitHub

Wykonaj kroki, aby skonfigurować wpisy tajne:

1. W [usłudze GitHub](https://github.com/)przejdź do repozytorium wybierz **pozycję Ustawienia > wpisowe > Dodaj nowy klucz tajny.**

    ![wpisy tajne](media/kubernetes-action/secrets.png)

2. Wklej zawartość powyższego `az cli` polecenia jako wartość zmiennej tajnej. Na przykład `AZURE_CREDENTIALS`.

3. Podobnie zdefiniuj następujące dodatkowe wpisy tajne dla poświadczeń rejestru kontenerów i ustaw je w akcji logowania platformy Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Zobaczysz wpisy tajne, jak pokazano poniżej po zdefiniowaniu.

    ![kubernetes-tajemnice](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Tworzenie obrazu kontenera i wdrażanie w klastrze usługi Azure Kubernetes

Kompilacja i wypychanie obrazów `Azure/docker-login@v1` kontenerów odbywa się przy użyciu akcji. Aby wdrożyć obraz kontenera do usługi AKS, należy użyć `Azure/k8s-deploy@v1` akcji. Ta akcja ma pięć parametrów:

| **Parametr**  | **Wyjaśnienie**  |
|---------|---------|
| **namespace** | (Opcjonalnie) Wybierz docelowy obszar nazw Kubernetes. Jeśli obszar nazw nie zostanie podany, polecenia będą uruchamiane w domyślnej przestrzeni nazw | 
| **Manifesty** |  (Wymagane) Ścieżka do plików manifestu, która będzie używana do wdrażania |
| **Obrazów** | (Opcjonalnie) W pełni kwalifikowany adres URL zasobu obrazu(-ów) do zastąpienia plików manifestu |
| **imagepullsecrets** | (Opcjonalnie) Nazwa klucza tajnego rejestru platformy docker, który został już skonfigurowany w klastrze. Każda z tych tajnych nazw jest dodawana w polu ImagePullSecrets dla obciążeń znalezionych w plikach manifestu wejściowego |
| **wersja kubectl** | (Opcjonalnie) Instaluje określoną wersję pliku binarnego kubectl |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Wdrażanie w klastrze usługi Kubernetes platformy Azure

Kompleksowy i kompleksowy przepływ pracy do tworzenia obrazów kontenerów i wdrażania w klastrze usługi Azure Kubernetes.

```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    - uses: Azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
        docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
      
    # Set the target AKS cluster.
    - uses: Azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: contoso
        resource-group: contoso-rg
        
    - uses: Azure/k8s-create-secret@v1
      with:
        container-registry-url: contoso.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: demo-k8s-secret

    - uses: Azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          demo.azurecr.io/k8sdemo:${{ github.sha }}
        imagepullsecrets: |
          demo-k8s-secret
```

## <a name="next-steps"></a>Następne kroki

Nasz zestaw akcji można znaleźć w różnych repozytoriach w usłudze GitHub, z których każdy zawiera dokumentację i przykłady ułatwiające korzystanie z usługi GitHub dla ciągłej integracji/ciągłego wdrażania i wdrażanie aplikacji na platformie Azure.

- [setup-kubectl](https://github.com/Azure/setup-kubectl)

- [k8s-set-kontekst](https://github.com/Azure/k8s-set-context)

- [aks-set-kontekst](https://github.com/Azure/aks-set-context)

- [k8s-create-secret](https://github.com/Azure/k8s-create-secret)

- [k8s-deploy](https://github.com/Azure/k8s-deploy)

- [webapps-container-deploy](https://github.com/Azure/webapps-container-deploy)

- [akcje-próbki przepływu pracy](https://github.com/Azure/actions-workflow-samples)
