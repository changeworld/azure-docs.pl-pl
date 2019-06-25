---
title: Tożsamość zarządzana za pomocą zadań rejestru kontenera platformy Azure
description: Zapewnia dostęp do usługi Azure Container Registry zadaniu do zasobów platformy Azure, w tym innych rejestrów prywatnych kontenerów, przypisując tożsamość zarządzaną dla zasobów platformy Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 5b60727472a06aaac8ccd3dce8609461e8972311
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148028"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Korzystanie z systemu Azure zarządzanych tożsamości w zadaniach usługi ACR 

Użyj [tożsamości zarządzanej dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) próbę uwierzytelnienia z zadania rejestru Azure container Registry, rejestr kontenerów platformy Azure lub innych zasobów platformy Azure bez konieczności zapewnienia lub Zarządzanie poświadczeniami w kodzie. Na przykład umożliwia tożsamość zarządzaną ściąganie i wypychanie obrazów kontenera do rejestru w innym kroku w zadaniu.

W tym artykule dowiesz się więcej na temat zarządzanych tożsamości oraz sposób:

> [!div class="checklist"]
> * Włączanie tożsamości przypisanych przez system lub tożsamości przypisanych przez użytkownika dla zadania usługi ACR
> * Udzielanie tożsamości dostępu do zasobów platformy Azure, takich jak innych rejestrów kontenerów platformy Azure
> * Użyj tożsamości zarządzanej dostępu do zasobów z zadania 

Tworzenie zasobów platformy Azure, w tym artykule wymaga uruchomienia wiersza polecenia platformy Azure w wersji 2.0.66 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

## <a name="why-use-a-managed-identity"></a>Dlaczego warto korzystać z tożsamości zarządzanej?

Tożsamość zarządzaną dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory (Azure AD). Można skonfigurować [niektórych zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), łącznie z zadaniami usługi ACR, za pomocą tożsamości zarządzanej. Następnie użyj tożsamości, aby uzyskać dostęp do innych zasobów platformy Azure, bez przekazywania poświadczeń w kodzie albo skryptach.

Istnieją dwa typy zarządzanych tożsamości:

* *Tożsamości przypisanych przez użytkownika*, które można przypisać do wielu zasobów i utrzymują się tak długo, jak Twoja chcesz. Tożsamości przypisanych przez użytkownika są obecnie dostępne w wersji zapoznawczej.

* A *tożsamości zarządzanej przez system*, który jest unikatowy dla określonego zasobu, takie jak zadanie programu rejestru Azure container Registry i obowiązuje przez okres istnienia tego zasobu.

Po skonfigurowaniu zasobu platformy Azure za pomocą tożsamości zarządzanej udzielić dostępu do tożsamości do innego zasobu, tak jak dowolnego podmiotu zabezpieczeń. Na przykład przypisać tożsamość zarządzaną rolę za pomocą ściągania, wypychania i ściągania lub inne uprawnienia do prywatnego rejestru kontenerów na platformie Azure. (Aby uzyskać pełną listę ról rejestru, zobacz [uprawnienia i role usługi Azure Container Registry](container-registry-roles.md).) Możesz udzielić dostępu tożsamości do co najmniej jednego zasobu.

## <a name="create-container-registries"></a>Tworzenie rejestrów kontenerów

W tym samouczku potrzebne są trzy rejestry kontenerów:

* Pierwszy rejestru umożliwia tworzenie i wykonywanie zadań usługi ACR. W tym artykule nosi nazwę tego rejestru źródłowego *myregistry*. 
* Drugi i trzeci rejestry są rejestrami docelowego dla pierwszego zadania przykład wypchnąć obraz, który opiera się on. W tym artykule rejestry docelowej są nazywane *customregistry1* i *customregistry2*.

Zastąp własnymi nazwami rejestru w kolejnych krokach.

Jeśli nie masz jeszcze rejestry kontenerów platformy Azure potrzebne, zobacz [Szybki Start: Tworzenie prywatnego rejestru kontenerów przy użyciu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md). Nie musisz jeszcze wypychanie obrazów do rejestru.

## <a name="example-task-with-a-system-assigned-identity"></a>Przykład: Zadania przy użyciu tożsamości przypisanych przez system

W tym przykładzie przedstawiono sposób tworzenia [zadania wieloetapowe](container-registry-tasks-multi-step.md) przy użyciu tożsamości przypisanych przez system. Zadanie tworzy obraz, a następnie używa tożsamości do uwierzytelniania za pomocą dwóch rejestrów docelowej, aby wypchnąć obraz.

Kroki dla tego przykładu zadania są zdefiniowane w [pliku YAML](container-registry-tasks-reference-yaml.md) o nazwie `testtask.yaml`. Plik znajduje się w katalogu multipleRegistries [zadania acr](https://github.com/Azure-Samples/acr-tasks) przykłady repozytorium. Plik jest przedstawiony tutaj:

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>Utwórz zadanie przy użyciu tożsamości przypisanych przez system

Utwórz zadanie *reg wielu* , wykonując następujące [az acr zadanie Tworzenie] [ az-acr-task-create] polecenia. Kontekst zadania jest folderem multipleRegistries repozytorium przykładów i polecenia odwołuje się do pliku `testtask.yaml` w repozytorium. `--assign-identity` Parametru bez dodatkowych wartości tworzy tożsamość przypisaną przez system dla zadania. To zadanie jest skonfigurowany, więc musisz ręcznie wyzwolić, ale może skonfigurować go do uruchamiania, gdy zatwierdzenia są przekazywane do repozytorium lub żądania ściągnięcia. 

```azurecli
az acr task create \
  --registry myregistry \
  --name multiple-reg \
  --context https://github.com/Azure-Samples/acr-tasks.git#:multipleRegistries \
  --file testtask.yaml \
  --commit-trigger-enabled false \
  --pull-request-trigger-enabled false \
  --assign-identity
```

W danych wyjściowych polecenia `identity` sekcja pokazuje tożsamości typu `SystemAssigned` jest ustawiony w zadaniu. `principalId` To identyfikator jednostki usługi tożsamości:

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 

Użyj [az acr zadanie Pokaż] [ az-acr-task-show] polecenie, aby przechowywać `principalId` w zmiennej, do użycia w późniejszym poleceń:

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>Nadawanie uprawnień wypychania tożsamości dwóch rejestry kontenerów docelowej

W tej sekcji należy nadać uprawnienia tożsamości przypisanych przez system do wypychania rejestry dwóch docelowy o nazwie *customregistry1* i *customregistry2*.

Najpierw użyj [az acr show] [ az-acr-show] polecenie, aby uzyskać identyfikator zasobu każdego rejestru i przechowywać identyfikatorów w zmiennych:

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

Użyj [utworzenia przypisania roli az] [ az-role-assignment-create] polecenie, aby przypisać tożsamość `acrpush` roli do każdego rejestru. Ta rola ma uprawnienia do ściąganie i wypychanie obrazów do rejestru kontenerów.

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>Dodawanie poświadczeń rejestru docelowego na zadanie

Teraz za pomocą [Dodaj az acr zadań poświadczeń] [ az-acr-task-credential-add] polecenie, aby dodać poświadczenia tożsamości do zadania, aby mógł uwierzytelnić za pomocą obu rejestrów docelowego.

```azurecli
az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry1.azurecr.io \
  --use-identity [system]

az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry2.azurecr.io \
  --use-identity [system]
```

### <a name="manually-run-the-task"></a>Ręcznie uruchomić zadanie

Użyj [az acr zadania] [ az-acr-task-run] polecenie, aby ręcznie wyzwolić zadanie. `--set` Parametr jest używany do przekazywania nazwy serwera logowania rejestrów dwóch docelowy jako wartości dla zmiennych zadań `REGISTRY1` i `REGISTRY2`.

```azurecli
az acr task run \
  --name multiple-reg \
  --registry myregistry \
  --set REGISTRY1=customregistry1.azurecr.io \
  --set REGISTRY2=customregistry2.azurecr.io
```

Dane wyjściowe są podobne do następujących:

```console
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:02 Successfully executed container: acb_step_0
2019/05/31 22:16:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:02 Pushing image: customregistry2.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry2.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:08 Successfully pushed image: customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:08 Executing step ID: acb_step_2. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:08 Scanning for dependencies...
2019/05/31 22:16:08 Successfully scanned dependencies
2019/05/31 22:16:08 Launching container with name: acb_step_2
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:09 Successfully executed container: acb_step_2
2019/05/31 22:16:09 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:09 Pushing image: customregistry1.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry1.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:21 Successfully pushed image: customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:21 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.985291)
2019/05/31 22:16:21 Populating digests for step ID: acb_step_0...
2019/05/31 22:16:22 Successfully populated digests for step ID: acb_step_0
2019/05/31 22:16:22 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 5.743225)
2019/05/31 22:16:22 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 1.925959)
2019/05/31 22:16:22 Populating digests for step ID: acb_step_2...
2019/05/31 22:16:24 Successfully populated digests for step ID: acb_step_2
2019/05/31 22:16:24 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.061057)
2019/05/31 22:16:24 The following dependencies were found:
2019/05/31 22:16:24
- image:
    registry: customregistry2.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b
- image:
    registry: customregistry1.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b

Run ID: cf31 was successful after 35s
```

## <a name="example-task-with-a-user-assigned-identity"></a>Przykład: Zadania przy użyciu tożsamości przypisanych przez użytkownika

W tym przykładzie utworzysz tożsamości przypisanych przez użytkownika z uprawnieniami do odczytu klucze tajne z usługi Azure key vault. Możesz przypisać tę tożsamość do wieloetapowego zadania, które odczytuje klucz tajny, tworzy obraz i zaloguje się do wiersza polecenia platformy Azure można odczytać tagu obrazu.

### <a name="create-a-key-vault-and-store-a-secret"></a>Tworzenie magazynu kluczy, a następnie umieszczanie wpisu tajnego

Po pierwsze, jeśli zachodzi potrzeba, należy utworzyć grupę zasobów o nazwie *myResourceGroup* w *eastus* lokalizacji z następującym [Tworzenie grupy az] [ az-group-create]polecenia:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Użyj [tworzenie az keyvault] [ az-keyvault-create] polecenie, aby utworzyć magazyn kluczy. Pamiętaj określić nazwę unikatową magazynu kluczy. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Store przykładowy wpis tajny do magazynu kluczy przy użyciu [az keyvault secret set] [ az-keyvault-secret-set] polecenia:

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

Na przykład możesz chcieć przechowywać poświadczenia do uwierzytelniania przy użyciu prywatnego rejestru platformy Docker, dzięki czemu możesz ściągnąć obrazu prywatnego.

### <a name="create-an-identity"></a>Utwórz tożsamość

Utwórz tożsamość o nazwie *myACRTasksId* w subskrypcji przy użyciu [Utwórz tożsamość az] [ az-identity-create] polecenia. Można użyć tej samej grupie zasobów, użytego wcześniej do tworzenia rejestru kontenera lub usługi key vault lub inną.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Aby skonfigurować tożsamość w poniższych krokach, użyj [Pokaż tożsamości az] [ az-identity-show] polecenia do przechowywania tożsamości zasobu, identyfikator i identyfikator jednostki usługi w zmiennych.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>Udzielanie tożsamości dostępu do magazynu kluczy do odczytu wpisu tajnego

Uruchom następujące polecenie [az keyvault set-policy] [ az-keyvault-set-policy] polecenie, aby ustawić zasady dostępu w magazynie kluczy. Poniższy przykład umożliwia tożsamości przypisanych przez użytkownika do pobrania wpisów tajnych w magazynie kluczy. Ten dostęp będzie później potrzebny do uruchomienia zadania wieloetapowe pomyślnie.

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>Udzielanie tożsamości dostępu czytnik do grupy zasobów dla rejestru

Uruchom następujące polecenie [utworzenia przypisania roli az] [ az-role-assignment-create] polecenie, aby przypisać tożsamość roli Czytelnik, w tym przypadku do grupy zasobów zawierające rejestru źródła. Ta rola jest wymagana w dalszej części pomyślne uruchomienie zadania wieloetapowe.

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>Utwórz zadanie przy użyciu tożsamości przypisanych przez użytkownika

Teraz Utwórz [zadania wieloetapowe](container-registry-tasks-multi-step.md) i przypisać jej tożsamości przypisanych przez użytkownika. To zadanie przykład można utworzyć [pliku YAML](container-registry-tasks-reference-yaml.md) o nazwie `managed-identities.yaml` w lokalnym katalogu roboczego i Wklej poniższą zawartość. Należy zastąpić nazwę magazynu kluczy w pliku o nazwie magazynu kluczy

```yml
version: v1.0.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: name
    keyvault: https://mykeyvault.vault.azure.net/secrets/SampleSecret
steps:
  # Verify that the task can access the secret in the key vault
  - cmd: bash -c 'if [ -z "$MY_SECRET" ]; then echo "Secret not resolved"; else echo "Secret resolved"; fi'
    env: 
      - MY_SECRET='{{.Secrets.name}}' 

  # Build/push the website image to source registry, using dockerfile in the Azure-Samples repo
  - cmd: docker build -t {{.Run.Registry}}/my-website:{{.Run.ID}} https://github.com/Azure-Samples/aci-helloworld.git
  - push: 
    - "{{.Run.Registry}}/my-website:{{.Run.ID}}"
  
  # Login to Azure CLI with identity and list the tags to verify that the image is in the registry
  - cmd: microsoft/azure-cli az login --identity
  - cmd: microsoft/azure-cli az acr repository show-tags -n {{.Values.registryName}} --repository my-website
```

To zadanie wykonuje następujące czynności:

* Weryfikuje, że mają dostęp do klucza tajnego w magazynie kluczy. Ten krok jest dla celów demonstracyjnych. W rzeczywistych scenariuszy może być konieczne zadań, można pobrać poświadczeń w celu dostępu do prywatnego repozytorium usługi Docker Hub.
* Kompiluje i wypycha `mywebsite` obrazu do rejestru źródła.
* Zaloguje się do wiersza polecenia platformy Azure do listy `my-website` tagów w rejestrze źródła obrazu.

Utwórz zadanie o nazwie *msitask* i przekaż go identyfikator zasobu przypisanego przez użytkownika tożsamości, który został wcześniej utworzony. To zadanie przykładzie jest tworzony z `managed-identities.yaml` plik został zapisany w lokalnym katalogu roboczym, więc trzeba ręcznie wyzwolić.

```azurecli
az acr task create \
  --name msitask \
  --registry myregistry \
  --context /dev/null \
  --file managed-identities.yaml \
  --pull-request-trigger-enabled false \
  --commit-trigger-enabled false \
  --assign-identity $resourceID
```

W danych wyjściowych polecenia `identity` sekcja pokazuje tożsamości typu `UserAssigned` jest ustawiony w zadaniu. `principalId` To identyfikator jednostki usługi tożsamości:

```console
[...]
"identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRTasksId": {
        "clientId": "xxxxxxxx-f17e-4768-bb4e-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-1335-433d-bb6c-xxxxxxxxxxxx"
      }
[...]
```

### <a name="manually-run-the-task"></a>Ręcznie uruchomić zadanie

Użyj [az acr zadania] [ az-acr-task-run] polecenie, aby ręcznie wyzwolić zadanie. `--set` Parametr jest używany do przekazywania nazwę rejestru źródłowego do zadania:

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

Dane wyjściowe zawierają klucz tajny zostanie rozwiązany, pomyślnym utworzeniu i wypchnięto obraz, a zadanie loguje się do wiersza polecenia platformy Azure przy użyciu tożsamości można odczytać tagu obrazu z rejestru źródła:

```console
Queued a run with ID: cf32
Waiting for an agent...
2019/06/10 23:25:37 Downloading source code...
2019/06/10 23:25:38 Finished downloading source code
2019/06/10 23:25:39 Using acb_vol_4e4a0a7c-b6ef-4ec5-b40f-3436fc5eb0f5 as the home volume
2019/06/10 23:25:41 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/10 23:25:41 Successfully set up Docker network: acb_default_network
2019/06/10 23:25:41 Setting up Docker configuration...
2019/06/10 23:25:42 Successfully set up Docker configuration
2019/06/10 23:25:42 Logging in to registry: myregistry.azurecr.io
2019/06/10 23:25:43 Successfully logged into myregistry.azurecr.io
2019/06/10 23:25:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:43 Launching container with name: acb_step_0
Secret resolved
2019/06/10 23:25:44 Successfully executed container: acb_step_0
2019/06/10 23:25:44 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:44 Launching container with name: acb_step_1
Sending build context to Docker daemon  74.75kB

[...]

cf32: digest: sha256:cbb4aa83b33f6959d83e84bfd43ca901084966a9f91c42f111766473dc977f36 size: 1577
2019/06/10 23:26:05 Successfully pushed image: myregistry.azurecr.io/my-website:cf32
2019/06/10 23:26:05 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:05 Launching container with name: acb_step_3
[
  {
    "environmentName": "AzureCloud",
    "id": "xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx",
    "isDefault": true,
    "name": "DanLep Internal Consumption",
    "state": "Enabled",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
      "user": {
      "assignedIdentityInfo": "MSI",
      "name": "systemAssignedIdentity",
      "type": "servicePrincipal"
    }
  }
]
2019/06/10 23:26:09 Successfully executed container: acb_step_3
2019/06/10 23:26:09 Executing step ID: acb_step_4. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:09 Launching container with name: acb_step_4
[
  "cf32"
]
2019/06/10 23:26:14 Successfully executed container: acb_step_4
2019/06/10 23:26:14 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.025312)
2019/06/10 23:26:14 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 13.703823)
2019/06/10 23:26:14 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 6.791506)
2019/06/10 23:26:14 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 3.852972)
2019/06/10 23:26:14 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 5.079079)
```


## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano przy użyciu zarządzanych tożsamości za pomocą zadań rejestru kontenera platformy Azure i jak:

> [!div class="checklist"]
> * Włączanie tożsamości przypisanych przez system lub użytkownik przypisany na zadanie usługi ACR
> * Udzielanie tożsamości dostępu do zasobów platformy Azure, takich jak innych rejestrów kontenerów platformy Azure
> * Użyj tożsamości zarządzanej dostępu do zasobów z zadania  

* Dowiedz się więcej o [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/).

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
